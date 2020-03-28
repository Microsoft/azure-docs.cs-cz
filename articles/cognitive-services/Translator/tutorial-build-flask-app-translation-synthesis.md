---
title: 'Kurz: Vytvoření aplikace Flask pro překlad, syntézu a analýzu textu - Translator Text API'
titleSuffix: Azure Cognitive Services
description: V tomto kurzu vytvoříte webovou aplikaci založenou na Flask, která bude překládat text, analyzovat mínění a syntetizovat přeložený text do řeči.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: tutorial
ms.date: 02/10/2020
ms.author: swmachan
ms.openlocfilehash: b41b68725b6747cbada13a9acc321724b3f89d67
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77118577"
---
# <a name="tutorial-build-a-flask-app-with-azure-cognitive-services"></a>Kurz: Vytvoření aplikace Flask s Azure Cognitive Services

V tomto kurzu vytvoříte webovou aplikaci Flask, která používá Služby Azure Cognitive Services k překladu textu, analýze mínění a syntéze přeloženého textu do řeči. Zaměřujeme se na kód Pythonu a flask trasy, které umožňují naši aplikaci, ale my vám pomůže ven s HTML a Javascript, který táhne aplikace dohromady. Pokud narazíte na nějaké problémy, dejte nám vědět pomocí tlačítka zpětné vazby níže.

Zde je to, co tento výukový program pokrývá:

> [!div class="checklist"]
> * Získání klíčů předplatného Azure
> * Nastavení vývojového prostředí a instalace závislostí
> * Vytvoření aplikace Flask
> * Překlad textu pomocí rozhraní Translator Text API
> * Použití analýzy textu k analýze kladných/negativních mínění vstupního textu a překladů
> * Převod přeložený textu na syntetizovaný řeč pomocí služby Speech Services
> * Spusťte aplikaci Flask místně

> [!TIP]
> Pokud chcete přeskočit dopředu a zobrazit veškerý kód najednou, celá ukázka spolu s pokyny k sestavení jsou k dispozici na [GitHubu](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Flask-App-Tutorial).

## <a name="what-is-flask"></a>Co je baňka?

Flask je mikrorámec pro vytváření webových aplikací. To znamená, že flask poskytuje nástroje, knihovny a technologie, které vám umožní vytvořit webovou aplikaci. Tato webová aplikace může být některé webové stránky, blog, wiki nebo jít jako podstatné jako on-line kalendář aplikace nebo komerční webové stránky.

Pro ty z vás, kteří se chtějí ponořit po tomto tutoriálu zde je několik užitečných odkazů:

* [Dokumentace baňky](http://flask.pocoo.org/)
* [Baňka pro nechápavé - Začátečník průvodce Baňka](https://codeburst.io/flask-for-dummies-a-beginners-guide-to-flask-part-uno-53aec6afc5b1)

## <a name="prerequisites"></a>Požadavky

Podívejme se na software a klíče předplatného, které budete potřebovat pro tento kurz.

* [Python 3.5.2 nebo novější](https://www.python.org/downloads/)
* [Nástroje Git](https://git-scm.com/downloads)
* IDE nebo textový editor, například [Visual Studio Code](https://code.visualstudio.com/) nebo [Atom](https://atom.io/)  
* [Chrome](https://www.google.com/chrome/browser/) nebo [Firefox](https://www.mozilla.org/firefox)
* Klíč **předplatného překladače textu** (Všimněte si, že není nutné vybrat oblast.)
* Klíč předplatného **Služby Text Analytics** v oblasti **Západní USA.**
* Klíč předplatného **služby Speech Services** v oblasti **Západní USA.**

## <a name="create-an-account-and-subscribe-to-resources"></a>Vytvoření účtu a přihlášení k odběru prostředků

Jak již bylo zmíněno, budete potřebovat tři klíče předplatného pro tento kurz. To znamená, že musíte vytvořit prostředek v rámci účtu Azure pro:
* Translator Text
* Analýza textu
* Hlasové služby

Pomocí [příkazu Vytvořit účet kognitivních služeb na webu Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) můžete k vytvoření prostředků podrobné pokyny.

> [!IMPORTANT]
> V tomto kurzu vytvořte prostředky v oblasti Západní USA. Pokud používáte jinou oblast, budete muset upravit základní adresu URL v každém souboru Pythonu.

## <a name="set-up-your-dev-environment"></a>Vytvoření a nastavení vývojového prostředí

Před sestavením webové aplikace Flask budete muset vytvořit pracovní adresář pro váš projekt a nainstalovat několik balíčků Pythonu.

### <a name="create-a-working-directory"></a>Vytvoření pracovního adresáře

1. Otevřete příkazový řádek (Windows) nebo terminál (macOS/Linux). Potom vytvořte pracovní adresář a podadresáře pro váš projekt:  

   ```
   mkdir -p flask-cog-services/static/scripts && mkdir flask-cog-services/templates
   ```
2. Změna pracovního adresáře projektu:  

   ```
   cd flask-cog-services
   ```

### <a name="create-and-activate-your-virtual-environment-with-virtualenv"></a>Vytvořte a aktivujte své virtuální prostředí pomocí`virtualenv`

Pojďme vytvořit virtuální prostředí pro naši `virtualenv`aplikaci Flask pomocí . Pomocí virtuálního prostředí zajistíte, že máte čisté prostředí pro práci.

1. V pracovním adresáři spusťte tento příkaz a vytvořte virtuální prostředí: **macOS/Linux:**
   ```
   virtualenv venv --python=python3
   ```
   Explicitně jsme deklarovali, že virtuální prostředí by mělo používat Python 3. Tím je zajištěno, že uživatelé s více instalacemi Pythonu používají správnou verzi.

   **Windows CMD / Windows Bash:**
   ```
   virtualenv venv
   ```
   Abyto bylo jednoduché, pojmenováváme vaše virtuální prostředí venv.

2. Příkazy pro aktivaci virtuálního prostředí se budou lišit v závislosti na vaší platformě/prostředí:   

   | Platforma | Prostředí | Příkaz |
   |----------|-------|---------|
   | macOS/Linux | bash/zsh | `source venv/bin/activate` |
   | Windows | bash | `source venv/Scripts/activate` |
   | | Příkazový řádek | `venv\Scripts\activate.bat` |
   | | PowerShell | `venv\Scripts\Activate.ps1` |

   Po spuštění tohoto příkazu by měl být příkazový řádek nebo terminálová relace předřazena . `venv`

3. Relaci můžete kdykoli deaktivovat zadáním příkazu do příkazového `deactivate`řádku nebo terminálu: .

> [!NOTE]
> Python má rozsáhlou dokumentaci pro vytváření a správu virtuálních prostředí, viz [virtualenv](https://virtualenv.pypa.io/en/latest/).

### <a name="install-requests"></a>Požadavky na instalaci

Požadavky je populární modul, který se používá k odesílání požadavků HTTP 1.1. Není nutné ručně přidávat řetězce dotazů do adres URL nebo kódovat data POST.

1. Chcete-li nainstalovat požadavky, spusťte:

   ```
   pip install requests
   ```

> [!NOTE]
> Další informace o požadavcích najdete v tématu [Žádosti: HTTP pro člověka](https://2.python-requests.org/en/master/).

### <a name="install-and-configure-flask"></a>Instalace a konfigurace flasku

Dále musíme nainstalovat Flask. Flask zpracovává směrování pro naši webovou aplikaci a umožňuje nám provádět volání mezi servery, které skrývají naše klíče předplatného před koncovým uživatelem.

1. Chcete-li nainstalovat Baňku, spusťte:
   ```
   pip install Flask
   ```
   Ujistíme se, že byl flask nainstalován. Spuštěním příkazu
   ```
   flask --version
   ```
   Verze by měla být vytištěna na terminálu. Cokoliv jiného znamená, že se něco pokazilo.

2. Chcete-li spustit aplikaci Flask, můžete buď použít příkaz baňky nebo přepínač Pythonu -m s Flask. Než to budete moci udělat, musíte terminálu sdělit, `FLASK_APP` se kterou aplikací pracovat exportem proměnné prostředí:

   **macOS/Linux**:
   ```
   export FLASK_APP=app.py
   ```

   **Windows**:
   ```
   set FLASK_APP=app.py
   ```

## <a name="create-your-flask-app"></a>Vytvoření aplikace Flask

V této části vytvoříte aplikaci Barebones Flask, která vrátí soubor HTML, když uživatelé narazí na kořenovou složku vaší aplikace. Neztrácejte příliš mnoho času se snaží vybrat od sebe kód, vrátíme se k aktualizaci tohoto souboru později.

### <a name="what-is-a-flask-route"></a>Co je trasa baňky?

Pojďme si na chvíli promluvit o "[trasách](http://flask.pocoo.org/docs/1.0/api/#flask.Flask.route)". Směrování se používá k vytvoření svázání adresy URL s určitou funkcí. Baňka používá dekoratéry tras k registraci funkcí na konkrétní adresy URL. Například, když uživatel přejde do`/`kořenového adresáře `index.html` ( ) naší webové aplikace, je vykreslen.  

```python
@app.route('/')
def index():
    return render_template('index.html')
```

Podívejme se na další příklad, jak zatlouct tento dům.

```python
@app.route('/about')
def about():
    return render_template('about.html')
```

Tento kód zajišťuje, že když `http://your-web-app.com/about` uživatel `about.html` přejde na soubor je vykreslen.

Zatímco tyto ukázky ilustrují, jak vykreslit html stránky pro uživatele, trasy lze také volat rozhraní API při stisknutí tlačítka nebo provést libovolný počet akcí, aniž by bylo třeba přejít od domovské stránky. Uvidíte to v akci při vytváření tras pro překlad, mínění a syntézu řeči.

### <a name="get-started"></a>Začínáme

1. Otevřete projekt v rozhraní IDE a `app.py` vytvořte soubor pojmenovaný v kořenovém adresáři pracovního adresáře. Dále zkopírujte tento `app.py` kód do a uložte:

   ```python
   from flask import Flask, render_template, url_for, jsonify, request

   app = Flask(__name__)
   app.config['JSON_AS_ASCII'] = False

   @app.route('/')
   def index():
       return render_template('index.html')
   ```

   Tento blok kódu říká `index.html` aplikaci, aby se zobrazila`/`vždy, když uživatel přejde do kořenového adresáře vaší webové aplikace ( .

2. Dále vytvoříme front-end pro naši webovou aplikaci. Vytvořte soubor `index.html` pojmenovaný `templates` v adresáři. Potom zkopírujte `templates/index.html`tento kód do .

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

3. Otestujeme aplikaci Flask. Z terminálu, běh:

   ```
   flask run
   ```

4. Otevřete prohlížeč a přejděte na uvedenou adresu URL. Měli byste vidět svou aplikaci na jednu stránku. Stisknutím **kláves Ctrl + c** aplikaci utečte.

## <a name="translate-text"></a>Přeložení textu

Nyní, když máte představu o tom, jak funguje jednoduchá aplikace Flask, pojďme:

* Napsat nějaký Python pro volání textového rozhraní TRANSLATOR API a vrátit odpověď
* Vytvoření trasy Flask pro volání kódu Pythonu
* Aktualizace HTML s oblastí pro zadávání textu a překlad, výběr jazyka a tlačítko Přeložit
* Napište Javascript, který umožňuje uživatelům komunikovat s aplikací Flask z HTML

### <a name="call-the-translator-text-api"></a>Volání textového rozhraní TRANSLATOR API

První věc, kterou musíte udělat, je napsat funkci pro volání překladače text API. Tato funkce bude mít `text_input` dva `language_output`argumenty: a . Tato funkce se nazývá vždy, když uživatel stiskne tlačítko přeložit ve vaší aplikaci. Textová oblast v html je `text_input`odeslána jako a hodnota výběru `language_output`jazyka v html je odeslána jako .

1. Začněme vytvořením souboru `translate.py` volaného v kořenovém adresáři pracovního adresáře.
2. Dále přidejte tento `translate.py`kód do . Tato funkce má dva `text_input` `language_output`argumenty: a .
   ```python
   import os, requests, uuid, json

   # Don't forget to replace with your Cog Services subscription key!
   # If you prefer to use environment variables, see Extra Credit for more info.
   subscription_key = 'YOUR_TRANSLATOR_TEXT_SUBSCRIPTION_KEY'
   
   # Don't forget to replace with your Cog Services location!
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
           'Ocp-Apim-Subscription-Region': 'location',
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
3. Přidejte klíč předplatného Translator Text a uložte ho.

### <a name="add-a-route-to-apppy"></a>Přidání trasy do`app.py`

Dále budete muset vytvořit trasu v aplikaci Flask, která volá `translate.py`. Tato trasa se bude volat pokaždé, když uživatel stiskne tlačítko přeložit ve vaší aplikaci.

Pro tuto aplikaci bude vaše `POST` trasa přijímat požadavky. Důvodem je, že funkce očekává, že text přeložit a výstupní jazyk pro překlad.

Baňka poskytuje pomocné funkce, které vám pomohou analyzovat a spravovat každý požadavek. V poskytnutém kódu `get_json()` vrátí data `POST` z požadavku jako JSON. Potom `data['text']` pomocí `data['to']`a , text a výstupní `get_translation()` jazyk `translate.py`hodnoty jsou předány funkce k dispozici od . Posledním krokem je vrátit odpověď jako JSON, protože budete muset zobrazit tato data ve webové aplikaci.

V následujících částech budete tento proces opakovat při vytváření tras pro analýzu mínění a syntézu řeči.

1. Otevřete `app.py` a vyhledejte příkaz `app.py` importu v horní části a přidejte následující řádek:

   ```python
   import translate
   ```
   Nyní naše flask app můžete `translate.py`použít metodu k dispozici prostřednictvím .

2. Zkopírujte tento kód `app.py` na konec a uložte:

   ```python
   @app.route('/translate-text', methods=['POST'])
   def translate_text():
       data = request.get_json()
       text_input = data['text']
       translation_output = data['to']
       response = translate.get_translation(text_input, translation_output)
       return jsonify(response)
   ```

### <a name="update-indexhtml"></a>Aktualizace souboru `index.html`

Teď, když máte funkci překladu textu a trasu v aplikaci Flask, která ji nazývá, je dalším krokem začít vytvářet HTML pro vaši aplikaci. Html níže dělá několik věcí:

* Poskytuje textovou oblast, do které mohou uživatelé zadávat text, který chcete přeložit.
* Obsahuje volič jazyka.
* Obsahuje prvky HTML, které vykreslují zjištěný jazyk a skóre spolehlivosti vrácené během překladu.
* Poskytuje textovou oblast jen pro čtení, kde je zobrazen výstup překladu.
* Zahrnuje zástupné symboly pro analýzu mínění a kód pro syntézu řeči, který přidáte do tohoto souboru později v kurzu.

Pojďme aktualizovat `index.html`.

1. Otevřete `index.html` a vyhledejte tyto komentáře kódu:
   ```html
   <!-- HTML provided in the following sections goes here. -->

   <!-- End -->
   ```

2. Nahraďte komentáře kódu tímto blokem HTML:
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

Dalším krokem je napsat nějaký Javascript. Toto je most mezi trasou HTML a Flask.

### <a name="create-mainjs"></a>Vytvořit`main.js`  

Soubor `main.js` je most mezi trasou HTML a Flask. Vaše aplikace bude používat kombinaci jQuery, Ajax a XMLHttpRequest `POST` k vykreslení obsahu a požadavky na vaše trasy Flask.

V níže uvedeném kódu se obsah z HTML používá k vytvoření požadavku na trasu Flask. Konkrétně obsah textové oblasti a volič jazyka jsou přiřazeny proměnné a pak předány `translate-text`spolu v požadavku na .

Kód pak itetuje prostřednictvím odpovědi a aktualizuje HTML s překladem, zjištěný jazyk a skóre spolehlivosti.

1. Z rozhraní IDE vytvořte `main.js` soubor `static/scripts` pojmenovaný v adresáři.
2. Zkopírujte tento `static/scripts/main.js`kód do :
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

### <a name="test-translation"></a>Zkušební překlad

Pojďme otestovat překlad v aplikaci.

```
flask run
```

Přejděte na zajišťovnou adresu serveru. Zadejte text do vstupní oblasti, vyberte jazyk a stiskněte příkaz přeložit. Měl by sis sehnat překlad. Pokud nefunguje, ujistěte se, že jste přidali klíč předplatného.

> [!TIP]
> Pokud se provedené změny nezobrazují nebo aplikace nefunguje tak, jak očekáváte, zkuste vymazat mezipaměť nebo otevřít soukromé/anonymní okno.

Stisknutím **kláves CTRL + c** aplikaci zabijete a přejdete k další části.

## <a name="analyze-sentiment"></a>Analýza mínění

Rozhraní [API analýzy textu](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) lze použít k provádění analýzy mínění, extrahování klíčových frází z textu nebo ke zjištění zdrojového jazyka. V této aplikaci použijeme analýzu mínění k určení, zda je zadaný text kladný, neutrální nebo negativní. Rozhraní API vrací číselné skóre v rozsahu 0 až 1. Skóre blížící se 1 označují pozitivní sentiment a skóre téměř 0 označují negativní sentiment.

V této části uděláte několik věcí:

* Napište nějaký Python pro volání rozhraní API analýzy textu, abyste provedli analýzu mínění a vrátili odpověď
* Vytvoření trasy Flask pro volání kódu Pythonu
* Aktualizace kódu HTML s oblastí pro skóre mínění a tlačítkem pro provedení analýzy
* Napište Javascript, který umožňuje uživatelům komunikovat s aplikací Flask z HTML

### <a name="call-the-text-analytics-api"></a>Volání rozhraní Text Analytics API

Napíšeme funkci pro volání rozhraní API pro analýzu textu. Tato funkce bude mít `input_text`čtyři `input_language` `output_text`argumenty: , , a `output_language`. Tato funkce se nazývá vždy, když uživatel stiskne tlačítko analýzy mínění spustit ve vaší aplikaci. Ke každému požadavku jsou poskytnuty údaje poskytnuté uživatelem z textové oblasti a voliče jazyka, stejně jako zjištěný jazyk a překladový výstup. Objekt odpovědi obsahuje skóre mínění pro zdroj a překlad. V následujících částech napíšete nějaký Javascript, abyste analýzu odpovědi a použili ve své aplikaci. Prozatím se zaměřme na volání rozhraní API pro analýzu textu.

1. Pojďme vytvořit soubor `sentiment.py` s názvem v kořenovém adresáři pracovního adresáře.
2. Dále přidejte tento `sentiment.py`kód do .
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
3. Přidejte klíč předplatného Služby Text Analytics a uložte jej.

### <a name="add-a-route-to-apppy"></a>Přidání trasy do`app.py`

V aplikaci Flask vytvoříme trasu, která bude volat `sentiment.py`. Tato trasa se bude volat pokaždé, když uživatel stiskne tlačítko analýzy mínění spustit ve vaší aplikaci. Stejně jako trasa pro překlad, `POST` tato trasa bude přijímat požadavky, protože funkce očekává argumenty.

1. Otevřete `app.py` a vyhledejte příkaz `app.py` importu v horní části a aktualizujte ho:

   ```python
   import translate, sentiment
   ```
   Nyní naše flask app můžete `sentiment.py`použít metodu k dispozici prostřednictvím .

2. Zkopírujte tento kód `app.py` na konec a uložte:
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

### <a name="update-indexhtml"></a>Aktualizace souboru `index.html`

Teď, když máte funkci pro spuštění analýzy mínění a trasu v aplikaci Flask, která ji volá, je dalším krokem začít psát HTML pro vaši aplikaci. Html níže dělá několik věcí:

* Přidá do aplikace tlačítko pro spuštění analýzy mínění.
* Přidá prvek, který vysvětluje hodnocení mínění.
* Přidá prvek pro zobrazení skóre mínění.

1. Otevřete `index.html` a vyhledejte tyto komentáře kódu:
   ```html
   <!-- Start sentiment code-->

   <!-- End sentiment code -->
   ```

2. Nahraďte komentáře kódu tímto blokem HTML:
   ```html
   <button type="submit" class="btn btn-primary mb-2" id="sentiment-analysis">Run sentiment analysis</button></br>
   <div id="sentiment" style="display: none">
      <p>Sentiment scores are provided on a 1 point scale. The closer the sentiment score is to 1, indicates positive sentiment. The closer it is to 0, indicates negative sentiment.</p>
      <strong>Sentiment score for input:</strong> <span id="input-sentiment"></span><br />
      <strong>Sentiment score for translation:</strong> <span id="translation-sentiment"></span>
   </div>
   ```

### <a name="update-mainjs"></a>Aktualizace souboru `main.js`

V níže uvedeném kódu se obsah z HTML používá k vytvoření požadavku na trasu Flask. Konkrétně obsah textové oblasti a volič jazyka jsou přiřazeny proměnným a pak předány `sentiment-analysis` spolu v požadavku na trasu.

Kód pak iterates prostřednictvím odpovědi a aktualizuje HTML s míněním skóre.

1. Z rozhraní IDE vytvořte `main.js` soubor `static` pojmenovaný v adresáři.

2. Zkopírujte tento `static/scripts/main.js`kód do :
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

### <a name="test-sentiment-analysis"></a>Test analýzy mínění

Otestujeme analýzu mínění v aplikaci.

```
flask run
```

Přejděte na zajišťovnou adresu serveru. Zadejte text do vstupní oblasti, vyberte jazyk a stiskněte příkaz přeložit. Měl by sis sehnat překlad. Dále stiskněte tlačítko spustit analýzu mínění. Měli byste vidět dvě skóre. Pokud nefunguje, ujistěte se, že jste přidali klíč předplatného.

> [!TIP]
> Pokud se provedené změny nezobrazují nebo aplikace nefunguje tak, jak očekáváte, zkuste vymazat mezipaměť nebo otevřít soukromé/anonymní okno.

Stisknutím **kláves CTRL + c** aplikaci zabijete a přejdete k další části.

## <a name="convert-text-to-speech"></a>Převod textu na řeč

Rozhraní [API pro převod textu na řeč](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech) umožňuje vaší aplikaci převést text na přirozenou syntetizovanou řeč podobné člověku. Služba podporuje standardní, neurální a vlastní hlasy. Naše ukázková aplikace používá několik dostupných hlasů, pro úplný seznam, viz [podporované jazyky](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#text-to-speech).

V této části uděláte několik věcí:

* Napište nějaký Python pro převod převodu textu na řeč pomocí rozhraní API pro převod textu na řeč
* Vytvoření trasy Flask pro volání kódu Pythonu
* Aktualizace HTML tlačítkem pro převod převodu převodu převodu textu na řeč a prvkem pro přehrávání zvuku
* Napište Javascript, který umožňuje uživatelům komunikovat s aplikací Flask

### <a name="call-the-text-to-speech-api"></a>Volání rozhraní API pro převod textu na řeč

Napíšeme funkci pro převod převodu převodu převodu textu na řeč. Tato funkce bude mít `input_text` dva `voice_font`argumenty: a . Tato funkce se nazývá vždy, když uživatel stiskne tlačítko převodu převodu textu na řeč ve vaší aplikaci. `input_text`je výstup překladu vrácený voláním `voice_font` k překladu textu, je hodnota z voliče hlasových písem v HTML.

1. Pojďme vytvořit soubor `synthesize.py` s názvem v kořenovém adresáři pracovního adresáře.

2. Dále přidejte tento `synthesize.py`kód do .
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
3. Přidejte klíč předplatného služby Speech Services a uložte ho.

### <a name="add-a-route-to-apppy"></a>Přidání trasy do`app.py`

V aplikaci Flask vytvoříme trasu, která bude volat `synthesize.py`. Tato trasa se bude volat pokaždé, když uživatel stiskne tlačítko převodu převodu textu na řeč ve vaší aplikaci. Stejně jako trasy pro překlad a analýzu `POST` mínění bude tato trasa přijímat požadavky, protože funkce očekává dva argumenty: text syntetizovat a hlasové písmo pro přehrávání.

1. Otevřete `app.py` a vyhledejte příkaz `app.py` importu v horní části a aktualizujte ho:

   ```python
   import translate, sentiment, synthesize
   ```
   Nyní naše flask app můžete `synthesize.py`použít metodu k dispozici prostřednictvím .

2. Zkopírujte tento kód `app.py` na konec a uložte:

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

### <a name="update-indexhtml"></a>Aktualizace souboru `index.html`

Teď, když máte funkci pro převod převodu textu na řeč a trasu v aplikaci Flask, která ji volá, je dalším krokem začít psát HTML pro vaši aplikaci. Html níže dělá několik věcí:

* Poskytuje rozevírací seznam pro výběr hlasu.
* Přidá tlačítko pro převod převodu převodu převodu textu na řeč.
* Přidá zvukový prvek, který se používá k přehrávání syntetizované řeči.

1. Otevřete `index.html` a vyhledejte tyto komentáře kódu:
   ```html
   <!-- Start voice font selection code -->

   <!-- End voice font selection code -->
   ```

2. Nahraďte komentáře kódu tímto blokem HTML:
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

4. Nahraďte komentáře kódu tímto blokem HTML:

```html
<button type="submit" class="btn btn-primary mb-2" id="text-to-speech">Convert text-to-speech</button>
<div id="audio-playback">
  <audio id="audio" controls>
    <source id="audio-source" type="audio/mpeg" />
  </audio>
</div>
```

5. Ujistěte se, že uložíte svou práci.

### <a name="update-mainjs"></a>Aktualizace souboru `main.js`

V níže uvedeném kódu se obsah z HTML používá k vytvoření požadavku na trasu Flask. Konkrétně překlad a hlasové písmo jsou přiřazeny proměnným a pak `text-to-speech` předány v požadavku na trasu.

Kód pak iterates prostřednictvím odpovědi a aktualizuje HTML s míněním skóre.

1. Z rozhraní IDE vytvořte `main.js` soubor `static` pojmenovaný v adresáři.
2. Zkopírujte tento `static/scripts/main.js`kód do :
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
3. Už jste téměř hotovi. Poslední věc, kterou budete dělat, je `main.js` přidat nějaký kód pro automatické vyberte hlasové písmo na základě jazyka vybraného pro překlad. Přidat tento blok `main.js`kódu do :
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

Pojďme otestovat syntézu řeči v aplikaci.

```
flask run
```

Přejděte na zajišťovnou adresu serveru. Zadejte text do vstupní oblasti, vyberte jazyk a stiskněte příkaz přeložit. Měl by sis sehnat překlad. Dále vyberte hlas a stiskněte tlačítko převodu převodu textu na řeč. překlad by měl být přehrán jako syntetizovaný projev. Pokud nefunguje, ujistěte se, že jste přidali klíč předplatného.

> [!TIP]
> Pokud se provedené změny nezobrazují nebo aplikace nefunguje tak, jak očekáváte, zkuste vymazat mezipaměť nebo otevřít soukromé/anonymní okno.

To je ono, máte pracovní aplikaci, která provádí překlady, analyzuje sentiment a syntetizovanou řeč. Stisknutím **kláves CTRL + c** aplikaci utečte. Nezapomeňte se podívat na další [služby Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/).

## <a name="get-the-source-code"></a>Získání zdrojového kódu

Zdrojový kód pro tento projekt je k dispozici na [GitHubu](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Flask-App-Tutorial).

## <a name="next-steps"></a>Další kroky

* [Referenční informace k rozhraní Translator Text API](https://docs.microsoft.com/azure/cognitive-services/Translator/reference/v3-0-reference)
* [Referenční informace k rozhraní API pro analýzu textu](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7)
* [Referenční informace k rozhraní API pro převod textu na řeč](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-text-to-speech)
