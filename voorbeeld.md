# Woord vooraf #
Je hebt een webserver draaien en de volgende bestanden geinstalleerd:
- ./js/app.js
- ./css/app.css

Je hebt ook een map ./data waarin straks de formulier schemas in worden opgeslagen. De webpagina waarin het webcomponent wordt geladen staat in de virtuele root. Zie hieronder voor voorbeeld.

![](http://vlugten.nl/bhh/images-wiki-github/structuurmap.png)


In dit voorbeeld gaan we een aanmeldformulier maken. Het formulier gebruik de volgende features:
- herhaalde groep
- een toonconditie
- validatie van input
- uitsluiting te verzenden data
- prefill van data in twee velden

Om het complete formulier te laten werken moeten we 2 bestanden aanmaken:
1. html pagina waarin de webcomponent geladen moet worden (je kan ook het webcomponent in een bestaande html pagina implementeren, zie [hier](implementeren#Applicatie-implementeren-op-een-webpagina))
2. json bestand met formulierbeschrijving (het JSON schema)

# Stap 1 #
* Start een editor (bijvoorbeeld Visual Code Studio of kladblok) en creëer 2 lege documenten;
* Noem het eerste document bijvoorbeeld voorbeeld.html
* Noem het tweede document bijvoorbeeld voorbeeld.json

# Stap 2. #
Plak de volgende code in het voorbeeld.html bestand en sla dit op in de 'root' van dit formulieren op je webserver. Dus 1 niveau hoger dan de data, js en css mappen.
````
<!DOCTYPE html>
<html lang="nl">
  <head>
		<title>Voorbeeld formulier</title>
		<link href=css/app.css rel=preload as=style>
		<link href=js/app.js rel=preload as=script>
		<link href=css/app.css rel=stylesheet>
	</head>
  <body>
    <noscript>
		<strong>We're sorry but bd-forms doesn't work properly without JavaScript enabled. Please enable it to continue.</strong>
    </noscript>
    <bd-forms></bd-forms>
	<script>
		window.bdForms = {};
		window.bdForms.config = {
			postRequestEndpoint: 'bd-test',
			getRequestEndpoint: 'data/voorbeeld.json',
			redirectLocation: 'http://google.com',
			submitButtonText: "Opslaan en verder"
		};
		window.bdForms.model = {
			naam: "Albert",
			geboortedatum: "01-01-1996"
		}
		window.bdForms.static = {}
		window.bdForms.validatorErrors = {}
    </script>
    <script src=js/app.js></script>
  </body>
</html>
```` 
In dit voorbeeld.html bestand zie je bijvoorbeeld dat:
- **getRequestEndpoint: 'data/voorbeeld.json'** verwijst naar het json schema dat we zo gaan maken
- **naam: "Albert"** straks het veld **naam** gaat vullen met Albert.
- **geboortedatum: "01-01-1996"** straks het veld **geboortedatum** gaat vullen met 01-01-1996.

# Stap 3 Het formulierschema #
## De basis ##
Nu gaan we het formulier schema maken (naslag [hier](schema-creeren) ). We beginnen met een leeg schema:
````
{
  "schema": {
    "version": "1.0",
    "groups": [

        [voeg hier je formulierelementen toe]

    ]
  }
}
````
## De eerste groep met vragen ##
We definieren nu onze eerste 3 vragen:
- Naam
- Geboortedatum
- Hebt u kinderen

Onderstaande JSON code definieert de eerste groep met vragen. 

````
{
    "legend": "Vul uw NAW gegevens in",
    "fields": [
        {
            "label": "Naam",
            "type": "input",
            "inputType": "text",
            "model": "naam"
        },
        {
            "label": "Geboortedatum",
            "type": "date",
            "placeholder": "dd-mm-jjjj",
            "model": "geboortedatum",
            "validator": ["datum"]
        },
        {
            "label": "Hebt u kinderen?",
            "type": "radios",
            "model": "kinderenJANEE",
            "doNotPost": true,
            "values": [ { "name": "ja", "value": true }, { "name": "nee", "value": false } ]
        }
    ]
}
````
Deze code voegen we in op de plek  [voeg hier je formulierelementen toe]. 

````
{
  "schema": {
    "version": "1.0",
    "groups": [

      {
        "legend": "Vul uw NAW gegevens in",
        "fields": [
          {
            "label": "Naam",
            "type": "input",
            "inputType": "text",
            "model": "naam"
          },
          {
            "label": "Geboortedatum",
            "type": "date",
            "placeholder": "dd-mm-jjjj",
            "model": "geboortedatum",
            "validator": ["datum"]
          },
          {
            "label": "Hebt u kinderen?",
            "type": "radios",
            "model": "kinderenJANEE",
            "doNotPost": true,
            "values": [ { "name": "ja", "value": true }, { "name": "nee", "value": false } ]
          }
        ]
      }

    ]
  }
}
````
***Let op!** Het luistert uiterst nauw dat je de { } & [ ] juist paired. Ook het vergeten (weg te halen) van een , kan tot het niet werken van de webcomponent leiden. Een goed editor helpt je hierbij.*

Open voorbeeld.json met je editor en plak bovenstaande code in voorbeeld.json. Sla dit op in de map data. Ga naar je browser en roep voorbeeld.html op. Je formulier verschijnt nu, met de eerste twee vragen voor-ingevuld.

![](http://vlugten.nl/bhh/images-wiki-github/voorbeeld-1.png)

Als je nu op 'Opslaan en verder' drukt zul je zien dat de ingevulde waarden in de POST worden verstuurd. 

![](http://vlugten.nl/bhh/images-wiki-github/voorbeeld-2.png)

Met uitzondering van kinderenJANEE. Immers, de parameter "doNotPost" staat op true, wat betekent dat de ingevulde waarde (radioknop value ja/nee) niet wordt meegestuurd in de POST. Hier wordt de waarde van kinderenJANEE straks gebruikt om een ander deel van het formulier te laten zien. Nieuwe vragen met nieuwe waarden die wel moeten worden meegestuurd.

## De tweede groep met vragen, met een herhaling en toonconditie ##
Wanneer je op **ja** hebt geklikt bij de radiobuttongroep kinderenJANEE zal er een extra vraag worden gesteld, namelijk de naam en leeftijd van kind. En er wordt gevraagd of je nog een kind wilt toevoegen aan de lijst.

![](http://vlugten.nl/bhh/images-wiki-github/voorbeeld-3.png)

Voeg onderstaande code toe aan je bestaande JSON schema:

````
{
  "legend": "",
  "fields": [
    { "type": "label", "label": "Naam kind" },
    { "type": "label", "label": "Leeftijd" }
  ],
  "customVisible": [
    {
      "formula": "equalsValue",
      "parameters": { "fieldModels": ["kinderenJANEE"], "value": true }
    }
  ]
},
{
  "legend":"",
  "repeatModel": "Kinderen",
  "addLink": "Voeg nog een kind toe",
  "removeLink": "bucket",
  "fields": [
    {
      "type": "input",
      "inputType": "text",
      "model": "kind",
      "validatorParameters": { "label": "de naam van het kind"}
    },
    {
      "type": "number",
      "model": "leeftijd",
      "validator": ["verplicht"],
      "validatorParameters": { "label": "de leeftijd"}
    }
  ],
  "customVisible": [
    {
      "formula": "equalsValue",
      "parameters": { "fieldModels": ["kinderenJANEE"], "value": true }
    }
  ]
}
````
Je JSON code ziet er dan als volgt uit 
````
{
  "schema": {
    "version": "1.0",
    "groups": [
      {
        "legend": "Vul uw NAW gegevens in",
        "fields": [
          {
            "label": "Naam",
            "type": "input",
            "inputType": "text",
            "model": "naam"
          },
          {
            "label": "Geboortedatum",
            "type": "date",
            "placeholder": "dd-mm-jjjj",
            "model": "geboortedatum",
            "validator": ["datum"]
          },
          {
            "label": "Hebt u kinderen?",
            "type": "radios",
            "model": "kinderenJANEE",
            "doNotPost": true,
            "values": [ { "name": "ja", "value": true }, { "name": "nee", "value": false } ]
          }
        ]
      },
      {
        "legend": "",
        "fields": [
          { "type": "label", "label": "Naam kind" },
          { "type": "label", "label": "Leeftijd" }
        ],
        "customVisible": [
          {
            "formula": "equalsValue",
            "parameters": { "fieldModels": ["kinderenJANEE"], "value": true }
          }
        ]
      },
      {
        "legend":"",
        "repeatModel": "Kinderen",
        "addLink": "Voeg nog een kind toe",
        "removeLink": "bucket",
        "fields": [
          {
            "type": "input",
            "inputType": "text",
            "model": "kind",
            "validatorParameters": { "label": "de naam van het kind"}
          },
          {
            "type": "number",
            "model": "leeftijd",
            "validator": ["verplicht"],
            "validatorParameters": { "label": "de leeftijd"}
          }
        ],
        "customVisible": [
          {
            "formula": "equalsValue",
            "parameters": { "fieldModels": ["kinderenJANEE"], "value": true }
          }
        ]
      }
    ]
  }
}
````
Let op de 
````
},
{ 
````
om de twee groepen bij elkaar te houden.

Als je de code bekijkt zie je dat de tweede groep pas getoond wordt als er aan een conditie is voldaan, de customVisible (meer [hier](Toepassen-van-condities#customvisible)). Namelijk de conditie ALS kinderenJANEE = WAAR toon dan dit blok { }.
````
        "customVisible": [
          {
            "formula": "equalsValue",
            "parameters": { "fieldModels": ["kinderenJANEE"], "value": true }
          }
        ]
````
Meer over condities is [hier](formules-bij-condities) te lezen.

Wanneer je een kind toevoegd en alles verstuurd, zou je het volgende moeten zien:

![](http://vlugten.nl/bhh/images-wiki-github/voorbeeld-4.png)
