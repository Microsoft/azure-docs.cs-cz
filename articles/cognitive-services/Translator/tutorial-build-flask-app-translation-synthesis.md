---
title: 'Kurz: Sestavení aplikace Flask přeložit syntetizace a analyzujte text – Translator Text API'
titleSuffix: Azure Cognitive Services
description: V tomto kurzu sestavíte aplikaci založené na Flask, která používá Azure Cognitive Services pro překlad textu, analýza mínění a syntetizace přeloženého textu na řeč. Našim hlavním cílem je kód Python a Flask tras, které umožňují naši aplikaci. Jsme nebude trávit spoustu času na jazyce Javascript, která řídí aplikaci, ale poskytují všechny soubory pro vám umožní zkontrolovat.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: tutorial
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: cef747e82e7d039952bec73e822f28eab2adaa97
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67434894"
---
# <a name="tutorial-build-a-flask-app-with-azure-cognitive-services"></a>Kurz: Sestavení aplikace Flask pomocí služeb Azure Cognitive Services

V tomto kurzu vytvoříte webovou aplikaci Flask, která používá Azure Cognitive Services pro překlad textu, analýza mínění a syntetizace přeloženého textu na řeč. Našim hlavním cílem je kód Python a Flask tras, které umožňují naši aplikaci, ale můžeme vám je pomůžeme vyřešit pomocí HTML a Javascript a který je shrnutím aplikace. Pokud narazíte na případné problémy nám dejte vědět pomocí tlačítka pro zpětnou vazbu.

Zde je, co tento kurz se zabývá:

> [!div class="checklist"]
> * Získání klíčů předplatného Azure
> * Nastavení vývojového prostředí a nainstalujte závislosti
> * Vytvoření aplikace Flask
> * Translator Text API umožňuje překlad textu
> * Použití analýzy textu k analýze pozitivní nebo negativní zabarvení vstupního textu a překlady
> * Použijte k převedení přeložený text do řečového hlasové služby
> * Místní spuštění aplikace Flask

> [!TIP]
> Pokud chcete přeskočit a zobrazit veškerý kód najednou, celé ukázkové spolu s sestavení pokyny jsou k dispozici na [Githubu](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Flask-App-Tutorial).

## <a name="what-is-flask"></a>Co je Flask?

Flask je microframework pro vytváření webových aplikací. To znamená, že poskytuje nástroje, knihovny a technologie, které umožňují vytvořit webovou aplikaci Flask. Tato webová aplikace může být několik webových stránek, blogu, wiki nebo go jako podstatné jako kalendáři založeném na webovou aplikaci nebo komerčních webů.

Pro ty, kteří chtějí podrobně prozkoumat po tomto kurzu tady je pár užitečných odkazů:

* [Dokumentace ke službě Flask](http://flask.pocoo.org/)
* [Flask figurínách – Průvodce pro začátečníky Flask](https://codeburst.io/flask-for-dummies-a-beginners-guide-to-flask-part-uno-53aec6afc5b1)

## <a name="prerequisites"></a>Požadavky

Pojďme se podívat na software a předplatné klíče, které budete potřebovat pro účely tohoto kurzu.

* [Python verze 3.5.2 nebo novější](https://www.python.org/downloads/)
* [Nástroje Git](https://git-scm.com/downloads)
* Integrované vývojové prostředí nebo textovém editoru, jako například [Visual Studio Code](https://code.visualstudio.com/) nebo [Atom](https://atom.io/)  
* [Chrome](https://www.google.com/chrome/browser/) nebo [Firefox](https://www.mozilla.org/firefox)
* A **Translator Text** klíč předplatného (Všimněte si, že není nutné vyberte oblast.)
* A **rozhraní Text Analytics** klíč předplatného **USA – západ** oblasti.
* A **hlasové služby** klíč předplatného **USA – západ** oblasti.

## <a name="create-an-account-and-subscribe-to-resources"></a>Vytvoření účtu a přihlášení k odběru k prostředkům

Jak už jsme zmínili budete potřebovat tři klíče předplatného pro účely tohoto kurzu. To znamená, že je potřeba vytvořit prostředek v rámci svého účtu Azure pro:
* Translator Text
* Analýza textu
* Hlasové služby

Použití [vytvoření účtu Cognitive Services na webu Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) podrobné pokyny k vytváření prostředků.

> [!IMPORTANT]
> Pro účely tohoto kurzu vytvořte prosím vaše prostředky v oblasti západní USA. Pokud používáte jiné oblasti, budete muset upravit základní adresu URL ve všech soubory Pythonu.

## <a name="set-up-your-dev-environment"></a>Vytvoření a nastavení vývojového prostředí

Předtím, než vytvoříte webovou aplikaci Flask, budete muset vytvoření pracovní adresář pro váš projekt a instalace několik balíčků Python.

### <a name="create-a-working-directory"></a>Vytvořte pracovní adresář

1. Otevřete příkazový řádek (Windows) nebo terminálu (macOS nebo Linux). Potom vytvořte do pracovního adresáře a podadresáře projektu:  

   ```
   mkdir -p flask-cog-services/static/scripts && mkdir flask-cog-services/templates
   ```
2. Změna pracovního adresáře vašeho projektu:  

   ```
   cd flask-cog-services
   ```

### <a name="create-and-activate-your-virtual-environment-with-virtualenv"></a>Vytvoření a aktivace virtuální prostředí pomocí `virtualenv`

Pojďme vytvořit virtuální prostředí pro naše aplikace Flask pomocí `virtualenv`. Pomocí virtuální prostředí se zajistí, že máte čisté prostředí pro práci z.

1. V pracovním adresáři, spuštěním následujícího příkazu vytvořte virtuální prostředí: **macOS nebo Linux:**
   ```
   virtualenv venv --python=python3
   ```
   Jsme jste explicitně deklarovat, že mají používat virtuální prostředí Python 3. Tím se zajistí, že uživatelé s více instalací Pythonu používají správnou verzi.

   **Windows CMD / Windows Bash:**
   ```
   virtualenv venv
   ```
   Abychom si to nekomplikovali, jsme pojmenováváte venv vaše virtuální prostředí.

2. Příkazy k aktivaci virtuální prostředí se budou lišit v závislosti na vaší platformě nebo prostředí:   

   | Platforma | Prostředí | Příkaz |
   |----------|-------|---------|
   | macOS/Linux | bash/zsh | `source venv/bin/activate` |
   | Windows | Bash | `source venv/Scripts/activate` |
   | | Příkazový řádek | `venv\Scripts\activate.bat` |
   | | PowerShell | `venv\Scripts\Activate.ps1` |

   Po spuštění tohoto příkazu příkazového řádku nebo relaci Terminálové služby musí být uvedena `venv`.

3. Můžete deaktivovat relaci kdykoliv to zadáním do příkazového řádku nebo terminálu: `deactivate`.

> [!NOTE]
> Python má rozsáhlou dokumentaci k vytváření a správě virtuálních prostředí, najdete v článku [virtualenv](https://virtualenv.pypa.io/en/latest/).

### <a name="install-requests"></a>Nainstalujte požadavky

Požadavky je populární modul, který se používá k odesílání požadavků HTTP 1.1. Není nutné ručně přidat řetězce dotazu do vaší adresy URL, nebo ke kódování následných dat formuláře.

1. Pokud chcete nainstalovat požadavků, spusťte:

   ```
   pip install requests
   ```

> [!NOTE]
> Pokud chcete získat další informace o žádosti, přečtěte si téma [požadavky: Protokol HTTP pro člověka](http://docs.python-requests.org/en/master/).

### <a name="install-and-configure-flask"></a>Instalace a konfigurace Flask

Dále budeme muset nainstalovat Flask. Flask zpracovávat směrování pro naši webovou aplikaci a umožňují nám na volání na serveru, které skrýt naše klíče předplatného od koncového uživatele.

1. Pokud chcete nainstalovat Flask, spusťte:
   ```
   pip install Flask
   ```
   Ujistíme se, že byla nainstalována Flask. Spuštěním příkazu
   ```
   flask --version
   ```
   Verze by měl vytisknout do terminálu. Cokoli jiného znamená, že došlo k chybě.

2. Ke spuštění aplikace Flask, můžete použít příkaz flask nebo přepínače -m Python s Flask. Předtím, než můžete tak učinit, budete muset zjistit, které aplikace pro práci se exportováním terminálu `FLASK_APP` proměnné prostředí:

   **macOS/Linux**:
   ```
   export FLASK_APP=app.py
   ```

   **Windows:**
   ```
   set FLASK_APP=app.py
   ```

## <a name="create-your-flask-app"></a>Vytvoření aplikace Flask

V této části se chystáte vytvořit nejzákladnější aplikaci Flask, která vrací soubor ve formátu HTML, když uživatelé dostat kořenovém adresáři aplikace. Není strávit příliš mnoho času při výběru této doby změny nepublikujete kód, vrátíme později aktualizovat tento soubor.

### <a name="what-is-a-flask-route"></a>Co je trasu Flask?

Pojďme si krátce mluvit o "[trasy](http://flask.pocoo.org/docs/1.0/api/#flask.Flask.route)". Směrování slouží k vytvoření vazby adresu URL pro konkrétní funkci. Flask používá k registraci funkce pro konkrétní adresy URL trasy dekorátory. Například když uživatel přejde do kořenového adresáře (`/`) naší webové aplikace, `index.html` vykreslením.  

```python
@app.route('/')
def index():
    return render_template('index.html')
```

Pojďme se podívat na další jedním z příkladů k hammer této domovské.

```python
@app.route('/about')
def about():
    return render_template('about.html')
```

Tento kód zajistí, že když uživatel přejde na `http://your-web-app.com/about` , který `about.html` se vykreslí soubor.

Zatímco tyto ukázky ukazují, jak vykreslování stránek html pro uživatele, můžete pro volání rozhraní API, když se stiskne tlačítko nebo přijmout libovolný počet akcí, aniž byste museli opustit domovské stránce použity trasy. Zobrazí se vám to v akci při vytváření trasy pro překlad, zabarvení a syntézu řeči.

### <a name="get-started"></a>Začínáme

1. Otevřete projekt v prostředí (IDE) a pak vytvořte soubor s názvem `app.py` v kořenu pracovního adresáře. V dalším kroku zkopírujte tento kód do `app.py` a uložte:

   ```python
   from flask import Flask, render_template, url_for, jsonify, request

   app = Flask(__name__)
   app.config['JSON_AS_ASCII'] = False

   @app.route('/')
   def index():
       return render_template('index.html')
   ```

   Tento blok kódu instruuje aplikaci, která zobrazí `index.html` vždy, když uživatel přejde do kořenového adresáře webové aplikace (`/`).

2. V dalším kroku vytvoříme front-endu pro naši webovou aplikaci. Vytvořte soubor s názvem `index.html` v `templates` adresáře. Zkopírujte tento kód do `templates/index.html`.

   ```html
   <!doctype html>
   <html lang="en">
     <head>
       <!-- Required metadata tags -->
       <meta charset="utf-8">
       <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
       <meta name="description" content="Translate and analyze text with Azure Cognitive Services.">
       <!-- Bootstrap CSS -->
       <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css" integrity="sha384-Gn5384xqQ1aoWXA+058RXPxPg6fy4IWvTNh0E263XmFcJlSAwiGgFAW/dAiS6JXm" crossorigin="anonymous">
       <title>Translate and analyze text with Azure Cognitive Services</title>
     </head>
     <body>
       <div class="container">
         <h1>Translate, synthesize, and analyze text with Azure</h1>
         <p>This simple web app uses Azure for text translation, text-to-speech conversion, and sentiment analysis of input text and translations. Learn more about <a href="https://docs.microsoft.com/azure/cognitive-services/">Azure Cognitive Services</a>.
        </p>
        <!-- HTML provided in the following sections goes here. -->

        <!-- End -->
       </div>

       <!-- Required Javascript for this tutorial -->
       <script src="https://code.jquery.com/jquery-3.2.1.slim.min.js" integrity="sha384-KJ3o2DKtIkvYIK3UENzmM7KCkRr/rE9/Qpg6aAZGJwFDMVNA/GpGFF93hXpG5KkN" crossorigin="anonymous"></script>
       <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.3.1/jquery.min.js"></script>
       <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.12.9/umd/popper.min.js" integrity="sha384-ApNbgh9B+Y1QKtv3Rn7W3mgPxhU9K/ScQsAP7hUibX39j7fakFPskvXusvfa0b4Q" crossorigin="anonymous"></script>
       <script src="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/js/bootstrap.min.js" integrity="sha384-JZR6Spejh4U02d8jOt6vLEHfe/JQGiRRSQQxSfFWpi1MquVdAyjUar5+76PVCmYl" crossorigin="anonymous"></script>
       <script type = "text/javascript" src ="static/scripts/main.js"></script>
     </body>
   </html>
   ```

3. Teď otestujte aplikaci Flask. Z terminálu spusťte:

   ```
   flask run
   ```

4. Otevřete prohlížeč a přejděte na adresu URL k dispozici. Měli byste vidět jednostránkovou aplikaci. Stisknutím klávesy **Ctrl + c** k ukončení aplikace.

## <a name="translate-text"></a>Přeložení textu

Teď, když máte představu o jednoduchou aplikaci Flask fungování, můžeme:

* Zápis některých Python pro volání rozhraní Translator Text API a vrátí odpověď
* Vytvořit trasu k volání kódu Python Flask
* Aktualizace kódu HTML k oblasti pro zadávání textu a překladu, výběr jazyka a překládat tlačítko
* Zápis jazyka Javascript, který umožňuje uživatelům interakci s vaší aplikací Flask z kódu HTML

### <a name="call-the-translator-text-api"></a>Volání rozhraní Translator Text API

První věc, kterou je třeba provést je zápis funkce pro volání rozhraní Translator Text API. Tato funkce bude trvat dva argumenty: `text_input` a `language_output`. Tato funkce je volána vždy, když uživatel stiskne tlačítko přeložit ve vaší aplikaci. Textová oblast v kódu HTML se odešle jako `text_input`, a hodnota výběr jazyka v kódu HTML se odešle jako `language_output`.

1. Začněme vytvořením souboru s názvem `translate.py` v kořenu pracovního adresáře.
2. Dále přidejte tento kód `translate.py`. Tato funkce přebírá dva argumenty: `text_input` a `language_output`.
   ```python
   import os, requests, uuid, json

   # Don't forget to replace with your Cog Services subscription key!
   # If you prefer to use environment variables, see Extra Credit for more info.
   subscription_key = 'YOUR_TRANSLATOR_TEXT_SUBSCRIPTION_KEY'

   # Our Flask route will supply two arguments: text_input and language_output.
   # When the translate text button is pressed in our Flask app, the Ajax request
   # will grab these values from our web app, and use them in the request.
   # See main.js for Ajax calls.
   def get_translation(text_input, language_output):
       base_url = 'https://api.cognitive.microsofttranslator.com'
       path = '/translate?api-version=3.0'
       params = '&to=' + language_output
       constructed_url = base_url + path + params

       headers = {
           'Ocp-Apim-Subscription-Key': subscription_key,
           'Content-type': 'application/json',
           'X-ClientTraceId': str(uuid.uuid4())
       }

       # You can pass more than one object in body.
       body = [{
           'text' : text_input
       }]
       response = requests.post(constructed_url, headers=headers, json=body)
       return response.json()
   ```
3. Přidejte klíč předplatné Translator Text a uložte.

### <a name="add-a-route-to-apppy"></a>Přidat trasu k `app.py`

V dalším kroku budete muset vytvořit trasu ve vaší aplikaci Flask, která volá `translate.py`. Tato trasa bude volána pokaždé, když uživatel stiskne tlačítko přeložit ve vaší aplikaci.

Pro tuto aplikaci vaší trasy probíhá tak, aby přijímal `POST` požadavky. Je to proto, funkce očekává, že text k přeložení a výstup jazyk pro překlad.

Flask poskytuje pomocné funkce, které vám pomohou analyzovat a spravovat každý požadavek. V kódu k dispozici `get_json()` vrátí data z `POST` žádosti jako JSON. Pak pomocí `data['text']` a `data['to']`, text a výstupní hodnoty jazyka jsou předány `get_translation()` funkce, které jsou k dispozici z `translate.py`. Posledním krokem je vrací odpověď jako dokumenty JSON, protože budete muset tato data zobrazit ve webové aplikaci.

V následujících částech vám tento postup opakujte při vytváření trasy pro analýzu a převod řeči syntézu mínění.

1. Otevřít `app.py` a vyhledejte příkaz importu v horní části `app.py` a přidejte následující řádek:

   ```python
   import translate
   ```
   Teď aplikaci Flask můžete použít metodu k dispozici prostřednictvím `translate.py`.

2. Zkopírujte tento kód na konec `app.py` a uložte:

   ```python
   @app.route('/translate-text', methods=['POST'])
   def translate_text():
       data = request.get_json()
       text_input = data['text']
       translation_output = data['to']
       response = translate.get_translation(text_input, translation_output)
       return jsonify(response)
   ```

### <a name="update-indexhtml"></a>Aktualizace `index.html`

Teď, když máte funkce pro převod textu a směrování ve vaší aplikaci Flask na říkat, dalším krokem je začít vytvářet kód HTML pro vaši aplikaci. Následující kód HTML provede několik věcí:

* Poskytuje textovou oblast, ve kterém můžete uživatele zadat text k přeložení.
* Obsahuje výběr jazyka.
* Obsahuje elementy HTML pro vykreslení zjištěný jazyk a skóre spolehlivosti vrátil během překladu.
* Poskytuje jen pro čtení textová oblast, ve kterém se zobrazí výstup překladu.
* Obsahuje zástupné symboly pro mínění analýzu a převod řeči syntézu kód, který v pozdější části kurzu přidáte do tohoto souboru.

Aktualizujme `index.html`.

1. Otevřít `index.html` a vyhledejte tyto komentáře kódu:
   ```html
   <!-- HTML provided in the following sections goes here. -->

   <!-- End -->
   ```

2. Nahraďte tento blok kódu HTML v komentářích ke kódu:
   ```html
   <div class="row">
     <div class="col">
       <form>
         <!-- Enter text to translate. -->
         <div class="form-group">
           <label for="text-to-translate"><strong>Enter the text you'd like to translate:</strong></label>
           <textarea class="form-control" id="text-to-translate" rows="5"></textarea>
         </div>
         <!-- Select output language. -->
         <div class="form-group">
           <label for="select-language"><strong>Translate to:</strong></label>
           <select class="form-control" id="select-language">
             <option value="ar">Arabic</option>
             <option value="ca">Catalan</option>
             <option value="zh-Hans">Chinese (Simplified)</option>
             <option value="zh-Hant">Chinese (Traditional)</option>
             <option value="hr">Croatian</option>
             <option value="en">English</option>
             <option value="fr">French</option>
             <option value="de">German</option>
             <option value="el">Greek</option>
             <option value="he">Hebrew</option>
             <option value="hi">Hindi</option>
             <option value="it">Italian</option>
             <option value="ja">Japanese</option>
             <option value="ko">Korean</option>
             <option value="pt">Portuguese</option>
             <option value="ru">Russian</option>
             <option value="es">Spanish</option>
             <option value="th">Thai</option>
             <option value="tr">Turkish</option>
             <option value="vi">Vietnamese</option>
           </select>
         </div>
         <button type="submit" class="btn btn-primary mb-2" id="translate">Translate text</button></br>
         <div id="detected-language" style="display: none">
           <strong>Detected language:</strong> <span id="detected-language-result"></span><br />
           <strong>Detection confidence:</strong> <span id="confidence"></span><br /><br />
         </div>

         <!-- Start sentiment code-->

         <!-- End sentiment code -->

       </form>
     </div>
     <div class="col">
       <!-- Translated text returned by the Translate API is rendered here. -->
       <form>
         <div class="form-group" id="translator-text-response">
           <label for="translation-result"><strong>Translated text:</strong></label>
           <textarea readonly class="form-control" id="translation-result" rows="5"></textarea>
         </div>

         <!-- Start voice font selection code -->

         <!-- End voice font selection code -->

       </form>

       <!-- Add Speech Synthesis button and audio element -->

       <!-- End Speech Synthesis button -->

     </div>
   </div>
   ```

Dalším krokem je napsat určitého kódu Javascript. Toto je most mezi HTML a Flask trasy.

### <a name="create-mainjs"></a>Vytvoření `main.js`  

`main.js` Soubor je most mezi HTML a Flask trasy. Vaše aplikace bude používat kombinaci jQuery, Ajax a XMLHttpRequest vykreslení obsahu a zkontrolujte `POST` požadavky na Flask trasy.

V následujícím kódu se používá obsah z HTML vytvořit požadavek na trase Flask. Konkrétně obsah části textu a výběr jazyka jsou přiřazena k proměnné a pak se předají do požadavku `translate-text`.

Kód poté Iteruje přes odpovědi a aktualizuje HTML s překladem, zjištěný jazyk a skóre spolehlivosti.

1. Z prostředí IDE, vytvořte soubor s názvem `main.js` v `static/scripts` adresáře.
2. Zkopírujte tento kód do `static/scripts/main.js`:
   ```javascript
   //Initiate jQuery on load.
   $(function() {
     //Translate text with flask route
     $("#translate").on("click", function(e) {
       e.preventDefault();
       var translateVal = document.getElementById("text-to-translate").value;
       var languageVal = document.getElementById("select-language").value;
       var translateRequest = { 'text': translateVal, 'to': languageVal }

       if (translateVal !== "") {
         $.ajax({
           url: '/translate-text',
           method: 'POST',
           headers: {
               'Content-Type':'application/json'
           },
           dataType: 'json',
           data: JSON.stringify(translateRequest),
           success: function(data) {
             for (var i = 0; i < data.length; i++) {
               document.getElementById("translation-result").textContent = data[i].translations[0].text;
               document.getElementById("detected-language-result").textContent = data[i].detectedLanguage.language;
               if (document.getElementById("detected-language-result").textContent !== ""){
                 document.getElementById("detected-language").style.display = "block";
               }
               document.getElementById("confidence").textContent = data[i].detectedLanguage.score;
             }
           }
         });
       };
     });
     // In the following sections, you'll add code for sentiment analysis and
     // speech synthesis here.
   })
   ```

### <a name="test-translation"></a>Test překladu

Teď otestujte překlad v aplikaci.

```
flask run
```

Přejděte na adresu zadaný server. Zadejte text do vstupní oblast, vyberte jazyk a stiskněte klávesu přeložit. Měli byste obdržet překlad. Pokud to nepomůže, ujistěte se, že jste přidali váš klíč předplatného.

> [!TIP]
> Pokud se nezobrazují změny, které jste provedli, nebo aplikace nefunguje tak, jak je pravděpodobné, že ho, zkuste vymazat mezipaměť a otevírání privátním okně.

Stisknutím klávesy **CTRL + c** ukončit aplikaci a pak přejděte k další části.

## <a name="analyze-sentiment"></a>Analýza mínění

[Rozhraní Text Analytics API](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) je možné provést analýzu mínění, extrakce klíčových frází z textu nebo rozpoznání zdrojového jazyka. V této aplikaci budeme používat analýzu subjektivního hodnocení k určení, zda je zadaný text pozitivní, neutrální nebo záporná. Rozhraní API vrátí číselné hodnocení v rozsahu 0 až 1. Hodnocení blížící se 1 značí pozitivní zabarvení a hodnocení blížící se 0 značí negativní zabarvení.

V této části se chystáte udělat pár věcí:

* Zápis některých Python pro volání rozhraní Text Analytics API provést analýzu subjektivního hodnocení a vrátí odpověď
* Vytvořit trasu k volání kódu Python Flask
* Aktualizace kódu HTML k oblasti pro skóre mínění a tlačítko k provádění analýz
* Zápis jazyka Javascript, který umožňuje uživatelům interakci s vaší aplikací Flask z kódu HTML

### <a name="call-the-text-analytics-api"></a>Volání rozhraní Text Analytics API

Napíšeme funkci, která volá rozhraní API pro analýzu textu. Tato funkce bude trvat čtyři argumenty: `input_text`, `input_language`, `output_text`, a `output_language`. Tato funkce je volána vždy, když uživatel stiskne tlačítko analýzy subjektivního hodnocení spouštět ve vaší aplikaci. Data zadaná uživatelem z selektor oblast a jazyk textu, jakož i zjištěného jazyka a překladu výstupu jsou k dispozici s každou žádostí. Objekt odpovědi obsahuje skóre mínění pro zdroj a překladu. V následujících částech budete psát určitého kódu Javascript analyzovat odpověď a jeho použití ve vaší aplikaci. Prozatím se soustřeďme na volání rozhraní API pro analýzu textu.

1. Pojďme vytvořit soubor s názvem `sentiment.py` v kořenu pracovního adresáře.
2. Dále přidejte tento kód `sentiment.py`.
   ```python
   import os, requests, uuid, json

   # Don't forget to replace with your Cog Services subscription key!
   subscription_key = 'YOUR_TEXT_ANALYTICS_SUBSCRIPTION_KEY'

   # Our Flask route will supply four arguments: input_text, input_language,
   # output_text, output_language.
   # When the run sentiment analysis button is pressed in our Flask app,
   # the Ajax request will grab these values from our web app, and use them
   # in the request. See main.js for Ajax calls.

   def get_sentiment(input_text, input_language, output_text, output_language):
       base_url = 'https://westus.api.cognitive.microsoft.com/text/analytics'
       path = '/v2.0/sentiment'
       constructed_url = base_url + path

       headers = {
           'Ocp-Apim-Subscription-Key': subscription_key,
           'Content-type': 'application/json',
           'X-ClientTraceId': str(uuid.uuid4())
       }

       # You can pass more than one object in body.
       body = {
           'documents': [
               {
                   'language': input_language,
                   'id': '1',
                   'text': input_text
               },
               {
                   'language': output_language,
                   'id': '2',
                   'text': output_text
               }
           ]
       }
       response = requests.post(constructed_url, headers=headers, json=body)
       return response.json()
   ```
3. Přidejte klíč předplatného pro analýzu textu a uložte.

### <a name="add-a-route-to-apppy"></a>Přidat trasu k `app.py`

Pojďme vytvořit trasu ve vaší aplikaci Flask, která volá `sentiment.py`. Tato trasa bude volána pokaždé, když uživatel stiskne tlačítko analýzy subjektivního hodnocení spouštět ve vaší aplikaci. Jako trasu pro překlad, bude tato trasa tak, aby přijímal `POST` žádosti, protože funkce očekává argumenty.

1. Otevřít `app.py` a vyhledejte příkaz importu v horní části `app.py` a aktualizovat ho:

   ```python
   import translate, sentiment
   ```
   Teď aplikaci Flask můžete použít metodu k dispozici prostřednictvím `sentiment.py`.

2. Zkopírujte tento kód na konec `app.py` a uložte:
   ```python
   @app.route('/sentiment-analysis', methods=['POST'])
   def sentiment_analysis():
       data = request.get_json()
       input_text = data['inputText']
       input_lang = data['inputLanguage']
       output_text = data['outputText']
       output_lang =  data['outputLanguage']
       response = sentiment.get_sentiment(input_text, input_lang, output_text, output_lang)
       return jsonify(response)
   ```

### <a name="update-indexhtml"></a>Aktualizace `index.html`

Teď, když máte funkci, kterou chcete spustit analýzu mínění a trasy ve vaší aplikaci Flask na říkat, dalším krokem je začít psát kód HTML pro vaši aplikaci. Následující kód HTML provede několik věcí:

* Přidá tlačítko do své aplikace a spuštění analýzy mínění
* Přidá prvek, který vysvětluje, vyhodnocování mínění
* Přidá prvek, který chcete zobrazit skóre mínění

1. Otevřít `index.html` a vyhledejte tyto komentáře kódu:
   ```html
   <!-- Start sentiment code-->

   <!-- End sentiment code -->
   ```

2. Nahraďte tento blok kódu HTML v komentářích ke kódu:
   ```html
   <button type="submit" class="btn btn-primary mb-2" id="sentiment-analysis">Run sentiment analysis</button></br>
   <div id="sentiment" style="display: none">
      <p>Sentiment scores are provided on a 1 point scale. The closer the sentiment score is to 1, indicates positive sentiment. The closer it is to 0, indicates negative sentiment.</p>
      <strong>Sentiment score for input:</strong> <span id="input-sentiment"></span><br />
      <strong>Sentiment score for translation:</strong> <span id="translation-sentiment"></span>
   </div>
   ```

### <a name="update-mainjs"></a>Aktualizace `main.js`

V následujícím kódu se používá obsah z HTML vytvořit požadavek na trase Flask. Konkrétně obsah části textu a výběr jazyka jsou přiřazena k proměnné a pak se předají do požadavku `sentiment-analysis` trasy.

Kód poté Iteruje přes odpovědi a aktualizuje HTML na skóre mínění.

1. Z prostředí IDE, vytvořte soubor s názvem `main.js` v `static` adresáře.

2. Zkopírujte tento kód do `static/scripts/main.js`:
   ```javascript
   //Run sentinment analysis on input and translation.
   $("#sentiment-analysis").on("click", function(e) {
     e.preventDefault();
     var inputText = document.getElementById("text-to-translate").value;
     var inputLanguage = document.getElementById("detected-language-result").innerHTML;
     var outputText = document.getElementById("translation-result").value;
     var outputLanguage = document.getElementById("select-language").value;

     var sentimentRequest = { "inputText": inputText, "inputLanguage": inputLanguage, "outputText": outputText,  "outputLanguage": outputLanguage };

     if (inputText !== "") {
       $.ajax({
         url: "/sentiment-analysis",
         method: "POST",
         headers: {
             "Content-Type":"application/json"
         },
         dataType: "json",
         data: JSON.stringify(sentimentRequest),
         success: function(data) {
           for (var i = 0; i < data.documents.length; i++) {
             if (typeof data.documents[i] !== "undefined"){
               if (data.documents[i].id === "1") {
                 document.getElementById("input-sentiment").textContent = data.documents[i].score;
               }
               if (data.documents[i].id === "2") {
                 document.getElementById("translation-sentiment").textContent = data.documents[i].score;
               }
             }
           }
           for (var i = 0; i < data.errors.length; i++) {
             if (typeof data.errors[i] !== "undefined"){
               if (data.errors[i].id === "1") {
                 document.getElementById("input-sentiment").textContent = data.errors[i].message;
               }
               if (data.errors[i].id === "2") {
                 document.getElementById("translation-sentiment").textContent = data.errors[i].message;
               }
             }
           }
           if (document.getElementById("input-sentiment").textContent !== '' && document.getElementById("translation-sentiment").textContent !== ""){
             document.getElementById("sentiment").style.display = "block";
           }
         }
       });
     }
   });
   // In the next section, you'll add code for speech synthesis here.
   ```

### <a name="test-sentiment-analysis"></a>Analýza subjektivního hodnocení testu

Můžeme otestovat analýzu mínění v aplikaci.

```
flask run
```

Přejděte na adresu zadaný server. Zadejte text do vstupní oblast, vyberte jazyk a stiskněte klávesu přeložit. Měli byste obdržet překlad. Dále stisknutím tlačítka analýzy mínění spuštění. Měli byste vidět dvě skóre. Pokud to nepomůže, ujistěte se, že jste přidali váš klíč předplatného.

> [!TIP]
> Pokud se nezobrazují změny, které jste provedli, nebo aplikace nefunguje tak, jak je pravděpodobné, že ho, zkuste vymazat mezipaměť a otevírání privátním okně.

Stisknutím klávesy **CTRL + c** ukončit aplikaci a pak přejděte k další části.

## <a name="convert-text-to-speech"></a>Převod textu na řeč

[Rozhraní API pro převod textu na řeč](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech) umožňuje aplikaci převést text do fyzické řečového podobnou té lidské. Tato služba podporuje standardní neuronových sítí a vlastní hlasy. Naše ukázková aplikace používá několik dostupných hlasů pro úplný seznam naleznete v tématu [podporované jazyky](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#text-to-speech).

V této části se chystáte udělat pár věcí:

* Zápis některých Python pro převod převod textu na řeč pomocí rozhraní API pro převod textu na řeč
* Vytvořit trasu k volání kódu Python Flask
* Aktualizace kódu HTML s tlačítkem pro převod textu na řeč a element pro přehrávání zvuku
* Zápis jazyka Javascript, který umožňuje uživatelům interakci s vaší aplikací Flask

### <a name="call-the-text-to-speech-api"></a>Volání rozhraní API pro převod textu na řeč

Napíšeme funkce pro převod převod textu na řeč. Tato funkce bude trvat dva argumenty: `input_text` a `voice_font`. Tato funkce je volána vždy, když uživatel stiskne tlačítko Převod textu na řeč převést ve vaší aplikaci. `input_text` je vrácený voláním na překlad textu, výstup překlad `voice_font` je hodnota volič hlasového písma v kódu HTML.

1. Pojďme vytvořit soubor s názvem `synthesize.py` v kořenu pracovního adresáře.

2. Dále přidejte tento kód `synthesize.py`.
   ```Python
   import os, requests, time
   from xml.etree import ElementTree

   class TextToSpeech(object):
       def __init__(self, input_text, voice_font):
           subscription_key = 'YOUR_SPEECH_SERVICES_SUBSCRIPTION_KEY'
           self.subscription_key = subscription_key
           self.input_text = input_text
           self.voice_font = voice_font
           self.timestr = time.strftime('%Y%m%d-%H%M')
           self.access_token = None

       # This function performs the token exchange.
       def get_token(self):
           fetch_token_url = 'https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken'
           headers = {
               'Ocp-Apim-Subscription-Key': self.subscription_key
           }
           response = requests.post(fetch_token_url, headers=headers)
           self.access_token = str(response.text)

       # This function calls the TTS endpoint with the access token.
       def save_audio(self):
           base_url = 'https://westus.tts.speech.microsoft.com/'
           path = 'cognitiveservices/v1'
           constructed_url = base_url + path
           headers = {
               'Authorization': 'Bearer ' + self.access_token,
               'Content-Type': 'application/ssml+xml',
               'X-Microsoft-OutputFormat': 'riff-24khz-16bit-mono-pcm',
               'User-Agent': 'YOUR_RESOURCE_NAME',
           }
           # Build the SSML request with ElementTree
           xml_body = ElementTree.Element('speak', version='1.0')
           xml_body.set('{http://www.w3.org/XML/1998/namespace}lang', 'en-us')
           voice = ElementTree.SubElement(xml_body, 'voice')
           voice.set('{http://www.w3.org/XML/1998/namespace}lang', 'en-US')
           voice.set('name', 'Microsoft Server Speech Text to Speech Voice {}'.format(self.voice_font))
           voice.text = self.input_text
           # The body must be encoded as UTF-8 to handle non-ascii characters.
           body = ElementTree.tostring(xml_body, encoding="utf-8")

           #Send the request
           response = requests.post(constructed_url, headers=headers, data=body)

           # Write the response as a wav file for playback. The file is located
           # in the same directory where this sample is run.
           return response.content
   ```
3. Přidejte klíč předplatného. hlasové služby a uložte.

### <a name="add-a-route-to-apppy"></a>Přidat trasu k `app.py`

Pojďme vytvořit trasu ve vaší aplikaci Flask, která volá `synthesize.py`. Tato trasa bude volána pokaždé, když uživatel stiskne tlačítko Převod textu na řeč převést ve vaší aplikaci. Stejně jako trasy pro převod a analýza mínění, bude tato trasa tak, aby přijímal `POST` žádosti, protože funkce očekává dva argumenty: text, tak, aby odpovídaly a hlasového písma pro přehrávání.

1. Otevřít `app.py` a vyhledejte příkaz importu v horní části `app.py` a aktualizovat ho:

   ```python
   import translate, sentiment, synthesize
   ```
   Teď aplikaci Flask můžete použít metodu k dispozici prostřednictvím `synthesize.py`.

2. Zkopírujte tento kód na konec `app.py` a uložte:

   ```Python
   @app.route('/text-to-speech', methods=['POST'])
   def text_to_speech():
       data = request.get_json()
       text_input = data['text']
       voice_font = data['voice']
       tts = synthesize.TextToSpeech(text_input, voice_font)
       tts.get_token()
       audio_response = tts.save_audio()
       return audio_response
   ```

### <a name="update-indexhtml"></a>Aktualizace `index.html`

Teď, když máte funkce pro převod převod textu na řeč a směrování ve vaší aplikaci Flask na říkat, dalším krokem je začít psát kód HTML pro vaši aplikaci. Následující kód HTML provede několik věcí:

* Obsahuje Výběr hlasu rozevíracího seznamu
* Přidá tlačítko pro převod převod textu na řeč
* Přidá zvukový prvek, který se používá k přehrání syntetizovaný řeči

1. Otevřít `index.html` a vyhledejte tyto komentáře kódu:
   ```html
   <!-- Start voice font selection code -->

   <!-- End voice font selection code -->
   ```

2. Nahraďte tento blok kódu HTML v komentářích ke kódu:
   ```html
   <div class="form-group">
     <label for="select-voice"><strong>Select voice font:</strong></label>
     <select class="form-control" id="select-voice">
       <option value="(ar-SA, Naayf)">Arabic | Male | Naayf</option>
       <option value="(ca-ES, HerenaRUS)">Catalan | Female | HerenaRUS</option>
       <option value="(zh-CN, HuihuiRUS)">Chinese (Mainland) | Female | HuihuiRUS</option>
       <option value="(zh-CN, Kangkang, Apollo)">Chinese (Mainland) | Male | Kangkang, Apollo</option>
       <option value="(zh-HK, Tracy, Apollo)">Chinese (Hong Kong)| Female | Tracy, Apollo</option>
       <option value="(zh-HK, Danny, Apollo)">Chinese (Hong Kong) | Male | Danny, Apollo</option>
       <option value="(zh-TW, Yating, Apollo)">Chinese (Taiwan)| Female | Yaiting, Apollo</option>
       <option value="(zh-TW, Zhiwei, Apollo)">Chinese (Taiwan) | Male | Zhiwei, Apollo</option>
       <option value="(hr-HR, Matej)">Croatian | Male | Matej</option>
       <option value="(en-US, Jessa24kRUS)">English (US) | Female | Jessa24kRUS</option>
       <option value="(en-US, Guy24kRUS)">English (US) | Male | Guy24kRUS</option>
       <option value="(en-IE, Sean)">English (IE) | Male | Sean</option>
       <option value="(fr-FR, Julie, Apollo)">French | Female | Julie, Apollo</option>
       <option value="(fr-FR, HortenseRUS)">French | Female | Julie, HortenseRUS</option>
       <option value="(fr-FR, Paul, Apollo)">French | Male | Paul, Apollo</option>
       <option value="(de-DE, Hedda)">German | Female | Hedda</option>
       <option value="(de-DE, HeddaRUS)">German | Female | HeddaRUS</option>
       <option value="(de-DE, Stefan, Apollo)">German | Male | Apollo</option>
       <option value="(el-GR, Stefanos)">Greek | Male | Stefanos</option>
       <option value="(he-IL, Asaf)">Hebrew (Isreal) | Male | Asaf</option>
       <option value="(hi-IN, Kalpana, Apollo)">Hindi | Female | Kalpana, Apollo</option>
       <option value="(hi-IN, Hemant)">Hindi | Male | Hemant</option>
       <option value="(it-IT, LuciaRUS)">Italian | Female | LuciaRUS</option>
       <option value="(it-IT, Cosimo, Apollo)">Italian | Male | Cosimo, Apollo</option>
       <option value="(ja-JP, Ichiro, Apollo)">Japanese | Male | Ichiro</option>
       <option value="(ja-JP, HarukaRUS)">Japanese | Female | HarukaRUS</option>
       <option value="(ko-KR, HeamiRUS)">Korean | Female | Haemi</option>
       <option value="(pt-BR, HeloisaRUS)">Portuguese (Brazil) | Female | HeloisaRUS</option>
       <option value="(pt-BR, Daniel, Apollo)">Portuguese (Brazil) | Male | Daniel, Apollo</option>
       <option value="(pt-PT, HeliaRUS)">Portuguese (Portugal) | Female | HeliaRUS</option>
       <option value="(ru-RU, Irina, Apollo)">Russian | Female | Irina, Apollo</option>
       <option value="(ru-RU, Pavel, Apollo)">Russian | Male | Pavel, Apollo</option>
       <option value="(ru-RU, EkaterinaRUS)">Russian | Female | EkaterinaRUS</option>
       <option value="(es-ES, Laura, Apollo)">Spanish | Female | Laura, Apollo</option>
       <option value="(es-ES, HelenaRUS)">Spanish | Female | HelenaRUS</option>
       <option value="(es-ES, Pablo, Apollo)">Spanish | Male | Pablo, Apollo</option>
       <option value="(th-TH, Pattara)">Thai | Male | Pattara</option>
       <option value="(tr-TR, SedaRUS)">Turkish | Female | SedaRUS</option>
       <option value="(vi-VN, An)">Vietnamese | Male | An</option>
     </select>
   </div>
   ```

3. Dále vyhledejte tyto komentáře kódu:
   ```html
   <!-- Add Speech Synthesis button and audio element -->

   <!-- End Speech Synthesis button -->
   ```

4. Nahraďte tento blok kódu HTML v komentářích ke kódu:

```html
<button type="submit" class="btn btn-primary mb-2" id="text-to-speech">Convert text-to-speech</button>
<div id="audio-playback">
  <audio id="audio" controls>
    <source id="audio-source" type="audio/mpeg" />
  </audio>
</div>
```

5. Ujistěte se, že a práci uložte.

### <a name="update-mainjs"></a>Aktualizace `main.js`

V následujícím kódu se používá obsah z HTML vytvořit požadavek na trase Flask. Konkrétně překlad hlasového písma jsou přiřazena k proměnné a pak se předají do požadavku `text-to-speech` trasy.

Kód poté Iteruje přes odpovědi a aktualizuje HTML na skóre mínění.

1. Z prostředí IDE, vytvořte soubor s názvem `main.js` v `static` adresáře.
2. Zkopírujte tento kód do `static/scripts/main.js`:
   ```javascript
   // Convert text-to-speech
   $("#text-to-speech").on("click", function(e) {
     e.preventDefault();
     var ttsInput = document.getElementById("translation-result").value;
     var ttsVoice = document.getElementById("select-voice").value;
     var ttsRequest = { 'text': ttsInput, 'voice': ttsVoice }

     var xhr = new XMLHttpRequest();
     xhr.open("post", "/text-to-speech", true);
     xhr.setRequestHeader("Content-Type", "application/json");
     xhr.responseType = "blob";
     xhr.onload = function(evt){
       if (xhr.status === 200) {
         audioBlob = new Blob([xhr.response], {type: "audio/mpeg"});
         audioURL = URL.createObjectURL(audioBlob);
         if (audioURL.length > 5){
           var audio = document.getElementById("audio");
           var source = document.getElementById("audio-source");
           source.src = audioURL;
           audio.load();
           audio.play();
         }else{
           console.log("An error occurred getting and playing the audio.")
         }
       }
     }
     xhr.send(JSON.stringify(ttsRequest));
   });
   // Code for automatic language selection goes here.
   ```
3. Už jste téměř hotovi. Poslední věcí, kterou se chystáte udělat, je přidání kódu pro `main.js` k automatickému výběru hlasového písma podle jazyk vybraný pro překlad. Přidejte tento blok kódu do `main.js`:
   ```javascript
   // Automatic voice font selection based on translation output.
   $('select[id="select-language"]').change(function(e) {
     if ($(this).val() == "ar"){
       document.getElementById("select-voice").value = "(ar-SA, Naayf)";
     }
     if ($(this).val() == "ca"){
       document.getElementById("select-voice").value = "(ca-ES, HerenaRUS)";
     }
     if ($(this).val() == "zh-Hans"){
       document.getElementById("select-voice").value = "(zh-HK, Tracy, Apollo)";
     }
     if ($(this).val() == "zh-Hant"){
       document.getElementById("select-voice").value = "(zh-HK, Tracy, Apollo)";
     }
     if ($(this).val() == "hr"){
       document.getElementById("select-voice").value = "(hr-HR, Matej)";
     }
     if ($(this).val() == "en"){
       document.getElementById("select-voice").value = "(en-US, Jessa24kRUS)";
     }
     if ($(this).val() == "fr"){
       document.getElementById("select-voice").value = "(fr-FR, HortenseRUS)";
     }
     if ($(this).val() == "de"){
       document.getElementById("select-voice").value = "(de-DE, HeddaRUS)";
     }
     if ($(this).val() == "el"){
       document.getElementById("select-voice").value = "(el-GR, Stefanos)";
     }
     if ($(this).val() == "he"){
       document.getElementById("select-voice").value = "(he-IL, Asaf)";
     }
     if ($(this).val() == "hi"){
       document.getElementById("select-voice").value = "(hi-IN, Kalpana, Apollo)";
     }
     if ($(this).val() == "it"){
       document.getElementById("select-voice").value = "(it-IT, LuciaRUS)";
     }
     if ($(this).val() == "ja"){
       document.getElementById("select-voice").value = "(ja-JP, HarukaRUS)";
     }
     if ($(this).val() == "ko"){
       document.getElementById("select-voice").value = "(ko-KR, HeamiRUS)";
     }
     if ($(this).val() == "pt"){
       document.getElementById("select-voice").value = "(pt-BR, HeloisaRUS)";
     }
     if ($(this).val() == "ru"){
       document.getElementById("select-voice").value = "(ru-RU, EkaterinaRUS)";
     }
     if ($(this).val() == "es"){
       document.getElementById("select-voice").value = "(es-ES, HelenaRUS)";
     }
     if ($(this).val() == "th"){
       document.getElementById("select-voice").value = "(th-TH, Pattara)";
     }
     if ($(this).val() == "tr"){
       document.getElementById("select-voice").value = "(tr-TR, SedaRUS)";
     }
     if ($(this).val() == "vi"){
       document.getElementById("select-voice").value = "(vi-VN, An)";
     }
   });
   ```

### <a name="test-your-app"></a>Testování aplikace

Můžeme otestovat syntézu řeči v aplikaci.

```
flask run
```

Přejděte na adresu zadaný server. Zadejte text do vstupní oblast, vyberte jazyk a stiskněte klávesu přeložit. Měli byste obdržet překlad. V dalším kroku vyberte hlasový vstup a potom klikněte na tlačítko pro převod textu na řeč convert. překlad by měl být přehrát jako řečového. Pokud to nepomůže, ujistěte se, že jste přidali váš klíč předplatného.

> [!TIP]
> Pokud se nezobrazují změny, které jste provedli, nebo aplikace nefunguje tak, jak je pravděpodobné, že ho, zkuste vymazat mezipaměť a otevírání privátním okně.

A je to, že máte funkční aplikaci, která provádí překlad, analyzuje mínění a řečového. Stisknutím klávesy **CTRL + c** k ukončení aplikace. Nezapomeňte se podívat na druhý [Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/).

## <a name="get-the-source-code"></a>Získat zdrojový kód

Zdrojový kód pro tento projekt je k dispozici na [Githubu](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Flask-App-Tutorial).

## <a name="next-steps"></a>Další postup

* [Referenční informace k rozhraní Translator Text API](https://docs.microsoft.com/azure/cognitive-services/Translator/reference/v3-0-reference)
* [Referenční informace k rozhraní API pro analýzu textu](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7)
* [Referenční informace k rozhraní API pro převod textu na řeč](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-text-to-speech)
