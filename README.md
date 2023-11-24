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

Um die Kehrtour.de API nutzen zu können, benötigen Sie folgende Informationen:

- API-Key: Dieser Key wird Ihnen von einem Kehrtour.de Nutzer zur Verfügung gestellt und dient der Authentifizierung des
  einzelnen Schornsteinfegers. Der API-Key hat eine maximale Gültigkeitsdauer von 3 Monaten. Bitten Sie den Nutzer,
  einen API Key über die Kehrtour.de Einstellungen zu generieren und Ihnen diesen in Ihrer Anwendung zu übergeben.
  Zeigen Sie den Key nach der erfolgreichen Eingabe nicht mehr dem Nutzer an und bieten Sie die Möglichkeit, diesen Key
  zu löschen.
- APP-ID: Diese ID identifiziert Ihre Anwendung. Sie wird Ihnen von Kehrtour.de zur Verfügung gestellt. Bitte wenden Sie
  sich per E-Mail an die in Github hinterlegte E-Mail-Adresse, um eine APP-ID zu erhalten.
- APP-Secret: Dieses Secret wird Ihnen von Kehrtour.de zur Verfügung gestellt. Bitte wenden Sie sich per E-Mail an die
  in Github hinterlegte E-Mail-Adresse, um ein APP-Secret zu erhalten. Veröffentlichen Sie das APP-Secret niemals und
  fügen Sie dieses ausschließlich in Ihrer Backend-Anwendung ein.

### Authentication Header

Um unsere REST Api nutzen zu können, setzen Sie bitte die folgenden Header in Ihren HTTPs-Request:

- `"authroziation": "Bearer <API-KEY>"`
- `"app-id": "<APP-ID>"`
- `"app-secret": "<APP-SECRET>"`

## Datensicherheit
Da die Datensicherheit oberste Priorität hat, prüfen wir vor der Freischaltung einer APP-ID, ob Ihre Anwendung Verantwortungsvoll mit den Kundendaten umgeht.
Der API-Key ist nur für die jeweilige APP-ID gültig und enthält ein Berechtigungsmanagement. So können möglicherweise nicht auf alle Daten zugegriffen werden.
Die API ist ausschließlich über HTTPS erreichbar. Die Daten werden verschlüsselt übertragen und sind somit vor unbefugtem Zugriff geschützt.

Bitte bieten Sie den Schornsteinfegern an, einen AVV (Auftr)

## Rate Limits

Unsere Kehrtour.de API ist über weiche Rate Limits gegen Missbrauch geschützt. Bitte achten Sie darauf, nicht zu viele
API Requests pro Sekunde pro Nutzer zu senden. Unsere Endpunkte sind so gestaltet, dass Sie mit wenigen Requests alle
benötigten Informationen abrufen können. Sollten Sie dennoch eine höhere Rate benötigen, wenden Sie sich bitte per
E-Mail an die in Github hinterlegte E-Mail-Adresse.

## Testumgebung

Aktuell stellen wir eine Testumgebung unter https://api-dev.kehrtour.de zur Verfügung. Wenn Sie von uns eine APP-ID
erhalten, bekommen Sie zusätzlich einen Zugang zu unserer Entwicklungsumgebung, damit Sie Ihre Integration mit unserem
System testen können. Bitte beachten Sie, dass die Testumgebung nicht immer verfügbar ist und wir keine Garantie für die
Verfügbarkeit geben können.
Beachten Sie außerdem, dass Sie für die Testumgebung einen anderen API-Key und eine andere APP-ID benötigen, als für die
Produktivumgebung.
Sie erhalten von uns eine APP-ID für die Produktivumgebung, sobald Sie Ihre Integration erfolgreich getestet haben.

## Endpunkte

Nachfolgend erhalten Sie eine Übersicht über die verfügbaren Endpunkte. Die Endpunkte sind in der Testumgebung und in
der Produktivumgebung identisch.

### Base-URLs

Es gibt folgende Base-URLs:

#### Testumgebung: `https://api-dev.kehrtour.de`

#### Produktivumgebung: `https://api.kehrtour.de`

### GET /v1/app

__Beschreibung__: Mit diesem Endpunkt können Sie Informationen zu Ihrer APP-ID abrufen.
___Response__:

```json
{
  "app": {
    "id": "<APP-ID>",
    "name": "<APP-Name>",
    "description": "<APP-Beschreibung>",
    "email": "<Kontakt-E-Mail-Adresse des Anbieters>"
  }
}
```

### GET /va/schornsteinfeger

__Beschreibung__: Mit diesem Endpunkt können Sie Informationen zum Schornsteinfeger aus Kehrtour.de abrufen.
___Response__:

```json
{
  "schornsteinfeger": {
    "id": "<Schornsteinfeger-ID>",
    "companyName": "<Name des Schornsteinfegers>",
    "email": "<Kontakt-E-Mail-Adresse des Schornsteinfegers>",
    "hoheitlicheTaetigkeiten": {
      "wirdAngeboten": boolean,
      "name": "<Name des bevollmächtigten Bezirksschornsteinfegers>"
    }
  }
}
```

### GET /v1/kehrtouren/anstehende

__Beschreibung__: Mit diesem Endpunkt können Sie anstehende Kehrtouren abrufen.
___Response__:

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

### GET /v1/kehrtour/{kehrtourId}/details

__Beschreibung__: Mit diesem Endpunkt können Sie über die ID der Kehrtour weitere Details abrufen.
___Response__:

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
        "liegenschaftsnummer": number,
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

__liegenschaftsnummer__: (optional) Ist z.B. die Liegenschaftsnummer aus dem ERP-System des Schornsteinfegers. Dieses
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
