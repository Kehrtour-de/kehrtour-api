# Kehrtour API Dokumentation

Sehr geehrte Nutzerin, sehr geehrter Nutzer,  
dieses Dokument beschreibt die öffentliche API von Kehrtour.de. Bei Fragen oder Anregungen wenden Sie sich bitte per
E-Mail an die in Github hinterlegte E-Mail-Adresse.

## Version

Diese API befindet sich noch in der BETA-Phase. Wir freuen uns jederzeit über Feedback und Anregungen.

## Allgemeines

Diese offene API dient dazu, externe Systeme an Kehrtour.de anzubinden. Sie ist nicht für den Endnutzer gedacht, sondern
für Softwareunternehmen, die Kehrtour.de in ihre Software integrieren möchten.

## Authentifizierung

Damit ein Schornsteinfeger Kehrtour.de in einer anderen Anwendung verbinden kann, wird in der Kehrtour.de
Benutzeroberfläche ein Token erzeugt. Mit diesem Token und Ihrer durch Kehrtour.de vergebenen App-ID kann in Ihrer
Anwendung ein Access-Token angefordert werden.
- Der von Kehrtour.de erzeugte Token ist 10 Zeichen lang und für den Nutzer einfach zu lesen und in die Zwischenablage zu kopieren.
- Der Token kann nur einmal in einen langlebigen Access-Token getauscht werden und verfällt danach.
- Der Token verfällt automatisch nach 10 Tagen und muss danach neu erzeugt werden

### App-ID erhalten
Wenn Sie in Ihrer Software Kehrtour.de anbinden möchten, benötigen Sie eine App-ID. Diese wird durch den Entwickler von Kehrtour.de ausgestellt und muss vertraulich behandelt werden. Daher die App-ID niemals im Frontend verwenden.
Sie erhalten Ihre App-ID unter info@kehrtour.de

### Access Token erhalten
Wenn der Schornsteinfeger Ihre Anwendung mit Kehrtour.de verknüpfen möchte und Sie bereits Ihre App-ID besitzen, kann der Schornsteinfeger in den Einstellungen von Kehrtour unter dem Menüpunkt "Verbindungen" einen Token erhalten und kopieren.
Dieser Token wird dann in Ihrer Anwendung eingegeben. Sie führen dann folgenden REST-API Call durch:

POST `/api/external/v1/exchange-token`
__Header__:
- "app-id: <Ihre App ID>"
__Request Body__:
```json
{
  "customerInformation": "<Ihre Kundennummer oder E-Mail Ihres Kunden>",
  "token": "<TOKEN des Schornsteinfegers>"
}
```
- "customerInformation": Diese Information wird in Kehrtour.de an der Verbindung angezeigt, damit der Schornsteinfeger erkennen kann, mit welchem Konto die Verbindung aktiv ist

__Response Body__:
```json
{
  "schornsteinfeger": {
    "id": "<Schornsteinfeger-ID>",
    "customerNumber": "<Kehrtour.de Customer Number>",
    "companyName": "<Name des Schornsteinfegers>",
    "email": "<Kontakt-E-Mail-Adresse des Schornsteinfegers>",
    "hoheitlicheTaetigkeiten": {
      "wirdAngeboten": boolean,
      "name": "<Name des bevollmächtigten Bezirksschornsteinfegers>"
    }
  },
  "accessToken": "<LONG-Living JWT>"
}
```
- "accessToken": Der Access Token muss auf Ihrer Seite sicher gespeichert werden und ausschließlich im Backend verwendet werden.

## Authentication Header für weitere API Calls

Um unsere REST-API nutzen zu können, setzen Sie bitte die folgenden Header in Ihren HTTPs-Request:

- `"authroziation": "Bearer <accessToken>"`

## Datensicherheit

Da die Datensicherheit oberste Priorität hat, prüfen wir vor der Freischaltung einer APP-ID, ob Ihre Anwendung
Verantwortungsvoll mit den Kundendaten umgeht.
Der Access Token ist nur für die jeweilige APP-ID gültig und enthält ein Berechtigungsmanagement. So können möglicherweise
nicht auf alle Daten zugegriffen werden.
Die API ist ausschließlich über HTTPS erreichbar. Die Daten werden verschlüsselt übertragen und sind somit vor
unbefugtem Zugriff geschützt.

## Rate Limits

Unsere Kehrtour.de API ist über weiche Rate Limits gegen Missbrauch geschützt. Bitte achten Sie darauf, nicht zu viele
API Requests pro Sekunde pro Nutzer zu senden. Unsere Endpunkte sind so gestaltet, dass Sie mit wenigen Requests alle
benötigten Informationen abrufen können. Sollten Sie dennoch eine höhere Rate benötigen, wenden Sie sich bitte per
E-Mail an die in Github hinterlegte E-Mail-Adresse.

## Testumgebung

Aktuell stellen wir eine Testumgebung unter https://dev.kehrtour.de zur Verfügung. Wenn Sie von uns eine APP-ID
erhalten, bekommen Sie zusätzlich einen Zugang zu unserer Entwicklungsumgebung, damit Sie Ihre Integration mit unserem
System testen können. Bitte beachten Sie, dass die Testumgebung nicht immer verfügbar ist und wir keine Garantie für die
Verfügbarkeit geben können.
Beachten Sie außerdem, dass Sie für die Testumgebung eine andere APP-ID benötigen als für die
Produktivumgebung.
Sie erhalten von uns eine APP-ID für die Produktivumgebung, sobald Sie Ihre Integration erfolgreich getestet haben.

## Endpunkte

Nachfolgend erhalten Sie eine Übersicht über die verfügbaren Endpunkte. Die Endpunkte sind in der Testumgebung und in
der Produktivumgebung identisch.

### Base-URLs

Es gibt folgende Base-URLs:

#### Testumgebung: `https://dev.kehrtour.de`

#### Produktivumgebung: `https://www.kehrtour.de`

## Schornsteinfeger Endpunkte

### GET `/api/external/v1/schornsteinfeger`

__Beschreibung__: Mit diesem Endpunkt können Sie Informationen zum Schornsteinfeger aus Kehrtour.de abrufen.
__Response__:

```json
{
  "schornsteinfeger": {
    "id": "<Schornsteinfeger-ID>",
    "customerNumber": "<Kehrtour.de Customer Number>",
    "companyName": "<Name des Schornsteinfegers>",
    "email": "<Kontakt-E-Mail-Adresse des Schornsteinfegers>",
    "hoheitlicheTaetigkeiten": {
      "wirdAngeboten": boolean,
      "name": "<Name des bevollmächtigten Bezirksschornsteinfegers>"
    }
  }
}
```

## Liegenschaftsdaten synchronisieren

### POST `/api/external/v1/data/properties`

__Beschreibung__: Mit diesem Endpunkt können Sie Liegenschaftsdaten übertragen. Bitte senden Sie maximal 50 Liegenschaften pro API Call. Wir empfehlen, die Daten jede Nacht an Kehrtour.de zu übertragen
__Response__:

```json
{
  "properties": {
    "externalId": "<Eindeutige ID der Liegenschaft in Ihrer Software>",
    "propertyNumber": <Optional, Float-Wert, der die Liegenschaft als laufende Nummer identifiert. Kann im Kehrtourplaner durchsucht werden>,
    "customerNumber": "<Optional, Text-Wert, der eine Kundennummer definiert. Kann im Kehrtourplaner durchsucht werden>",
    "address": {
      "street": "<Straße der Liegenschaft>",
      "houseNumber": "<Hausnummer inkl. Zusatz der Liegenschaft, z.B. 2A>",
      "line2": "<Optional, zweite Zeile der Anschrift>",
      "city": "<Stadt>",
      "zipCode": "<Postleitzahl>",
      "country": "<Land, z.B. Deutschland>"
    },
    "type": enum,
    "role": enum,
    "invoiceRecipients": [enum],
    "appointmentRecipients": [enum],
    "unused": boolean <true, wenn das Gebäude ungenutzt ist>,
    "unusedReason": "<Optional, Grund im Klartext, warum das Gebäude ungenutzt ist>",
    "workNote": "<Optional, Arbeitsnotiz der Liegenschaft>",
    "note": "<Optional, Notiz der Liegenschaft>",
    "owner": {
      "externalId": "<Eindeutige ID des Kunden in Ihrer Software>",
      "legalType": enum,
      "salutation": enum <Nur notwendig, wenn legalType PERSON ist>,
      "firstName": "<Vorname, nur notwendig wenn legalType PERSON ist>",
      "lastName": "<Nachname, nur notwendig wenn legalType PERSON ist>",
      "companyName": "<Firmenname, nur notwendig wenn legalType COMPANY ist>",
      "email": "<Optional, E-Mail Adresse des Kunden>",
      "mobilePhoneNumber": "<Optional, mobile Rufnummer des Kunden>",
      "phoneNumber": "<Optional, Rufnummer des Kunden>",
      "address": {
        "street": "<Straße des Kunden>",
        "houseNumber": "<Hausnummer inkl. Zusatz des Kunden, z.B. 2A>",
        "line2": "<Optional, zweite Zeile der Anschrift>",
        "city": "<Stadt>",
        "zipCode": "<Postleitzahl>",
        "country": "<Land, z.B. Deutschland>"
      }
    },
    // resident ist optional
    "resident": {
      "externalId": "<Eindeutige ID des Kunden in Ihrer Software>",
      "legalType": enum,
      "salutation": enum <Nur notwendig, wenn legalType PERSON ist>,
      "firstName": "<Vorname, nur notwendig wenn legalType PERSON ist>",
      "lastName": "<Nachname, nur notwendig wenn legalType PERSON ist>",
      "companyName": "<Firmenname, nur notwendig wenn legalType COMPANY ist>",
      "email": "<Optional, E-Mail Adresse des Kunden>",
      "mobilePhoneNumber": "<Optional, mobile Rufnummer des Kunden>",
      "phoneNumber": "<Optional, Rufnummer des Kunden>",
      "address": {
        "street": "<Straße des Kunden>",
        "houseNumber": "<Hausnummer inkl. Zusatz des Kunden, z.B. 2A>",
        "line2": "<Optional, zweite Zeile der Anschrift>",
        "city": "<Stadt>",
        "zipCode": "<Postleitzahl>",
        "country": "<Land, z.B. Deutschland>"
      }
    },
    // administrator ist optional
    "administrator": {
      "externalId": "<Eindeutige ID des Kunden in Ihrer Software>",
      "legalType": enum,
      "salutation": enum <Nur notwendig, wenn legalType PERSON ist>,
      "firstName": "<Vorname, nur notwendig wenn legalType PERSON ist>",
      "lastName": "<Nachname, nur notwendig wenn legalType PERSON ist>",
      "companyName": "<Firmenname, nur notwendig wenn legalType COMPANY ist>",
      "email": "<Optional, E-Mail Adresse des Kunden>",
      "mobilePhoneNumber": "<Optional, mobile Rufnummer des Kunden>",
      "phoneNumber": "<Optional, Rufnummer des Kunden>",
      "address": {
        "street": "<Straße des Kunden>",
        "houseNumber": "<Hausnummer inkl. Zusatz des Kunden, z.B. 2A>",
        "line2": "<Optional, zweite Zeile der Anschrift>",
        "city": "<Stadt>",
        "zipCode": "<Postleitzahl>",
        "country": "<Land, z.B. Deutschland>"
      }
    },
    "aufgaben": [{
      "fiscalYear": number,
      "taetigkeiten": [{
        "type": enum,
        "arbeitswerte": number,
        "verweildauer": number <Optional, in Minuten>,
        "category": enum,
        "completed": boolean,
        "completedAt": "<ISO Timestamp, wann Fertiggestellt, wenn completed = true>",
        "quarter": number <Notwendig, wenn type = QUARTER>,
        "startDate": "<ISO Timestamp, Beginn des Zeitraumes wenn type = RANGE>",
        "endDate": "<ISO Timestamp, Ende des Zeitraumes wenn type = RANGE>",
        "zusammenlegung": boolean <true, wenn zusammenlegung, z.B. bei category KEHRUNG>
      }]
    }],
    // Property Units sind z.B. Wohnungen in einem Mehrfamilienhaus
    "propertyUnits": [{
      "externalId": "<Eindeutige ID der Einheit in Ihrer Software>",
      "type": enum,
      "description": "<Optional, Beschreibung der Einheit>",
      "apartmentNumber": number <Optional, Wohnungsnummer>,
      "floor": number <Optional, Etage der Wohnung>,
      "active": boolean,
      "appointmentRecipients": [enum],
      // optional
      "owner": {
        "externalId": "<Eindeutige ID des Kunden in Ihrer Software>",
        "legalType": enum,
        "salutation": enum <Nur notwendig, wenn legalType PERSON ist>,
        "firstName": "<Vorname, nur notwendig wenn legalType PERSON ist>",
        "lastName": "<Nachname, nur notwendig wenn legalType PERSON ist>",
        "companyName": "<Firmenname, nur notwendig wenn legalType COMPANY ist>",
        "email": "<Optional, E-Mail Adresse des Kunden>",
        "mobilePhoneNumber": "<Optional, mobile Rufnummer des Kunden>",
        "phoneNumber": "<Optional, Rufnummer des Kunden>",
        "address": {
          "street": "<Straße des Kunden>",
          "houseNumber": "<Hausnummer inkl. Zusatz des Kunden, z.B. 2A>",
          "line2": "<Optional, zweite Zeile der Anschrift>",
          "city": "<Stadt>",
          "zipCode": "<Postleitzahl>",
          "country": "<Land, z.B. Deutschland>"
        }
      },
      // optional
      "resident": {
        "externalId": "<Eindeutige ID des Kunden in Ihrer Software>",
        "legalType": enum,
        "salutation": enum <Nur notwendig, wenn legalType PERSON ist>,
        "firstName": "<Vorname, nur notwendig wenn legalType PERSON ist>",
        "lastName": "<Nachname, nur notwendig wenn legalType PERSON ist>",
        "companyName": "<Firmenname, nur notwendig wenn legalType COMPANY ist>",
        "email": "<Optional, E-Mail Adresse des Kunden>",
        "mobilePhoneNumber": "<Optional, mobile Rufnummer des Kunden>",
        "phoneNumber": "<Optional, Rufnummer des Kunden>",
        "address": {
          "street": "<Straße des Kunden>",
          "houseNumber": "<Hausnummer inkl. Zusatz des Kunden, z.B. 2A>",
          "line2": "<Optional, zweite Zeile der Anschrift>",
          "city": "<Stadt>",
          "zipCode": "<Postleitzahl>",
          "country": "<Land, z.B. Deutschland>"
        }
      },
      // aufgaben der einzelnen Einheit
      "aufgaben": [{
        "fiscalYear": number,
        "taetigkeiten": [{
          "type": enum,
          "arbeitswerte": number,
          "verweildauer": number <Optional, in Minuten>,
          "category": enum,
          "completed": boolean,
          "completedAt": "<ISO Timestamp, wann Fertiggestellt, wenn completed = true>",
          "quarter": number <Notwendig, wenn type = QUARTER>,
          "startDate": "<ISO Timestamp, Beginn des Zeitraumes wenn type = RANGE>",
          "endDate": "<ISO Timestamp, Ende des Zeitraumes wenn type = RANGE>",
          "zusammenlegung": boolean <true, wenn zusammenlegung, z.B. bei category KEHRUNG>
        }]
      }]
    }]
  }
}
```
__Enums__:

__type__:
- `EINFAMILIENHAUS`
- `MEHRFAMILIENHAUS`
- `GEWERBEBETRIEB`
- `MEHRFAMILIENHAUS_MIT_GEWERBEBETRIEB`
- `UNBEKANNT`

__role__:
- `FREIE_UND_HOHEITLICHE_AUFGABEN` - Freie und hoheitliche Tätigkeiten
- `GEHOERT_NICHT_ZUM_KEHRBEZIRK` - Nur freie Tätigkeiten
- `NUR_HOHEITLICHE_AUFGABEN` - Nur hoheitliche Tätigkeiten

__invoiceRecipients, appointmentRecipients__:
- `OWNER` - Eigentümer
- `RESIDENT` - Bewohner
- `ADMINISTRATOR` - Verwalter

__legalType__:
- `PERSON` - Natürliche Person
- `COMPANY` - Unternehmen

__salutation__:
- `UNKNOWN`
- `HERR`
- `FRAU`
- `EHELEUTE`
- `HERR_UND_FRAU`
- `ERBENGEMEINSCHAFT`
- `HERR_ODER_FRAU`
- `FIRMA`

__taetigkeiten.type__:
- `YEAR` - Tätigkeit im Jahr
- `QUARTER` - Tätigkeit in einem Quartal
- `RANGE` - Tätigkeit in einem Zeitraum

__taetigkeiten.category__:
- `KEHRUNG`
- `EMISSIONSMESSUNG`
- `FESTSTOFFMESSUNG`
- `FEUERSTAETTENSCHAU`
- `GASHAUSSCHAU`
- `SONSTIGES_1`
- `SONSTIGES_2`
- `SONSTIGES_3`
- `LUFTANLAGE`

__propertyUnits.type__:
- `WOHNUNG`
- `SONSTIGES`

## Kehrtourplaner Endpunkte

### GET `/api/external/v1/kehrtouren/anstehende`

__Beschreibung__: Mit diesem Endpunkt können Sie anstehende Kehrtouren abrufen.
__Response__:

```json
{
  "kehrtouren": [
    {
      "id": "<Kehrtour-ID>",
      "kehrtourNumber": "<Human-Readable Nummer der Kehrtour>",
      "versandArt": enum,
      "startDateTime": "<Startdatum und Uhrzeit der Kehrtour. Format: ISO 8601>",
      "appointmentType": enum,
      "status": enum
    }
  ]
}
```

__Enums__:

__appointmentType__:

- `ONLY_DAY` bedeutet, dass die Zeiten Kehrtour in genau einem Zeitblock für jede Liegenschaft
  stattfindet.
- `FIXED_SLOTS` bedeutet, dass die Liegenschaften in mehreren Zeitblöcken besucht werden.

__versandArt__:

- `SELF_PRINTING` bedeutet, dass der Schornsteinfeger die Kehrtour selbst ausdruckt.
- `SEND_BY_KEHRTOUR` bedeutet, dass Kehrtour.de die Terminankündigungen zustellt.

__status__:

- `SEND_SCHEDULED`: Die Kehrtour wurde noch nicht versendet.
- `COMPLETED`: Die Kehrtour wurde erfolgreich versendet.
- `CANCELLED`: Die Kehrtour wurde storniert.
- `DOCUMENT_GENERATION_SCHEDULED`: Die Kehrtour wurde erstellt und die Terminankündigungen werden gerade zum Ausdrucken
  generiert.

### GET `/api/external/v1/kehrtour/{kehrtourId}/details`

__Beschreibung__: Mit diesem Endpunkt können Sie über die ID der Kehrtour weitere Details abrufen.
__Response__:

```json
{
  "kehrtour": {
    "id": "<Kehrtour-ID>",
    "kehrtourNumber": "<Human-Readable Nummer der Kehrtour>",
    "versandArt": enum,
    "startDateTime": "<Startdatum und Uhrzeit der Kehrtour. Format: ISO 8601>",
    "appointmentType": enum,
    "status": enum,
    "liegenschaften": [
      {
        "id": "<Liegenschaft-ID>",
        "propertyNumber": number,
        "address": {
          "street": "Straße der Liegenschaft",
          "houseNumber": "(optional) Hausnummer",
          "line2": "(optional) Adresszusatz",
          "city": "Ort",
          "zipCode": "Postleitzahl",
          "country": "Land"
        },
        "recipients": [
          {
            "id": "<Empfänger-ID>",
            "salutation": enum,
            "name": "<Name des Empfängers>",
            "email": "<(optional) E-Mail-Adresse des Empfängers>",
            "phoneNumber": "<(optional) Telefonnummer des Empfängers>",
            "appointmentDelivery": [
              enum
            ],
            "address": {
              "street": "Straße des Kunden",
              "houseNumber": "(optional) Hausnummer",
              "line2": "(optional) Adresszusatz",
              "city": "Ort",
              "zipCode": "Postleitzahl",
              "country": "Land"
            },
            "recipientType": enum
          }
        ],
        "schornsteinfegerAufgaben": [
          {
            "id": "<Aufgaben-ID>",
            "name": "<Name der Aufgabe>",
            "description": "<Beschreibung der Aufgabe>",
            "category": enum
          }
        ],
        "startDateTime": "<Startdatum und Uhrzeit des Zeitblocks. Format: ISO 8601>",
        "endDateTime": "<Enddatum und Uhrzeit des Zeitblocks. Format: ISO 8601>",
        "customerFeedback": enum
      }
    ]
  }
}
```

__Zusatzbemerkungen Felder__:

__propertyNumber__: (optional) Ist z.B. die Liegenschaftsnummer aus dem ERP-System des Schornsteinfegers. Dieses
Feld ist eine
float Zahl.
__customerFeedback__: Dieses Feld liefert die Rückmeldung des Kunden. Wenn mehrere Empfänger vorhanden sind, wiegt die
Antwort des Bewohners mehr als die Antwort des Eigentümers. Wenn der Kunde noch nicht geantwortet hat, ist das
Feld `UNKNOWN`.

__salutation__:

- `UNKNOWN`: Unbekannte Anrede
- `HERR`: Herr
- `FRAU`: Frau
- `EHELEUTE`: Eheleute
- `HERR_UND_FRAU`: Herr und Frau
- `ERBENGEMEINSCHAFT`: Erbengemeinschaft
- `HERR_ODER_FRAU`: Herr oder Frau
- `FIRMA`: Firma

__appointmentDelivery__:

- `LETTER`: Terminankündigung per Brief
- `EMAIL`: Terminankündigung per E-Mail
- `SMS`: Terminankündigung per SMS
- `WHATSAPP`: Terminankündigung per Whatsapp
- `SELF_PRINTED`: Terminankündigung zum Selbstausdrucken

__recipientType__:

- `OWNER`: Eigentümer
- `RESIDENT`: Bewohner
- `ADMINISTRATOR`: Verwalter

__"schornsteinfegerAufgaben.category"__: (optional)

- `KEHRUNG`: Kehrung
- `EMISSIONSMESSUNG`: Emissionsmessung
- `FEUERSTÄTTENSCHAU`: Feuerstättenschau
- `FESTSTOFFMESSUNG`: Feststoffmessung

__customerFeedback__:

- `UNKNOWN`: Unbekannt, es wurde noch keine Rückmeldung gegeben
- `CUSTOMER_AT_HOME`: Kunde ist zu Hause
- `CUSTOMER_NOT_AT_HOME`: Kunde ist nicht zu Hause
- `CUSTOMER_DOES_NOT_KNOW`: Kunde ist sich noch nicht sicher
