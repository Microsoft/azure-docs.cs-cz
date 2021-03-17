---
title: 'Kurz: sestavení aplikace v baňce pro překlad, syntetizování a analýzu textu – Překladatel'
titleSuffix: Azure Cognitive Services
description: V tomto kurzu vytvoříte webovou aplikaci založenou na baňce pro překlad textu, analýze mínění a syntetizování přeloženého textu na řeč.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: tutorial
ms.date: 03/04/2021
ms.author: lajanuar
ms.custom: devx-track-python, devx-track-js
ms.openlocfilehash: c04bac76453d565abb99a971386b9ce0461b88ae
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102172075"
---
# <a name="tutorial-build-a-flask-app-with-azure-cognitive-services"></a>Kurz: sestavení aplikace v baňce pomocí Azure Cognitive Services

V tomto kurzu vytvoříte webovou aplikaci v baňce, která používá Azure Cognitive Services k překladu textu, analýze mínění a syntetizování přeloženého textu na řeč. Náš fokus je na trasách kódu Pythonu, které tuto aplikaci umožňují, ale budeme vám pomůžou s HTML a JavaScriptem, který aplikaci nasadí. Pokud narazíte na nějaké problémy, dejte nám vědět pomocí tlačítka pro odeslání názoru níže.

V tomto kurzu se dozvíte, co tento kurz popisuje:

> [!div class="checklist"]
> * Získání klíčů předplatného Azure
> * Nastavení vývojového prostředí a instalace závislostí
> * Vytvoření aplikace v baňce
> * Použití překladatele k překladu textu
> * Použijte Analýza textu k analýze kladného/záporného mínění vstupního textu a překladů.
> * Použití služby Speech Services k převodu přeloženého textu na syntetizované řeč
> * Místní spuštění aplikace v baňce

> [!TIP]
> Pokud byste chtěli přeskočit a podívat se na všechny kódy najednou, celá ukázka, společně s pokyny pro sestavení, jsou k dispozici na [GitHubu](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Flask-App-Tutorial).

## <a name="what-is-flask"></a>Co je Flask?

Baňka je mikrorozhraní pro vytváření webových aplikací. To znamená, že v baňce získáte nástroje, knihovny a technologie, které vám umožní vytvořit webovou aplikaci. Tato webová aplikace může být některé webové stránky, blog, wikiweb nebo jít jako podstatné jako webové aplikace kalendáře nebo komerční web.

Pro ty, které chcete podrobně po tomto kurzu, je zde několik užitečných odkazů:

* [Dokumentace k baňce](http://flask.pocoo.org/)
* [Baňka pro Dummies – příručka pro začátečníky do baňky](https://codeburst.io/flask-for-dummies-a-beginners-guide-to-flask-part-uno-53aec6afc5b1)

## <a name="prerequisites"></a>Požadavky

Pojďme si projít klíče softwaru a předplatného, které pro tento kurz budete potřebovat.

* [Python 3.5.2 nebo novější](https://www.python.org/downloads/)
* [Nástroje Git](https://git-scm.com/downloads)
* Rozhraní IDE nebo textový editor, například [Visual Studio Code](https://code.visualstudio.com/) nebo [Atom](https://atom.io/)  
* [Chrome](https://www.google.com/chrome/browser/) nebo [Firefox](https://www.mozilla.org/firefox)
* Klíč předplatného **překladatele** (můžete pravděpodobně použít **globální** umístění.)
* Klíč předplatného **Analýza textu** v oblasti **západní USA** .
* Klíč předplatného **služby Speech Services** v oblasti **západní USA**

## <a name="create-an-account-and-subscribe-to-resources"></a>Vytvoření účtu a přihlášení k odběru prostředků

Jak už jsme uvedli, budete pro tento kurz potřebovat tři klíče předplatného. To znamená, že je potřeba vytvořit prostředek v rámci účtu Azure pro:
* Translator
* Analýza textu
* Hlasové služby

Pro podrobné pokyny k vytváření prostředků použijte [v Azure Portal vytvořit účet Cognitive Services](../cognitive-services-apis-create-account.md) .

> [!IMPORTANT]
> Pro tento kurz prosím vytvořte svoje prostředky v oblasti Západní USA. Pokud používáte jinou oblast, budete muset v každém ze svých souborů Pythonu upravit základní adresu URL.

## <a name="set-up-your-dev-environment"></a>Vytvoření a nastavení vývojového prostředí

Před vytvořením webové aplikace v baňce budete muset vytvořit pracovní adresář pro váš projekt a nainstalovat několik balíčků Pythonu.

### <a name="create-a-working-directory"></a>Vytvořit pracovní adresář

1. Otevřete příkazový řádek (Windows) nebo terminál (macOS/Linux). Pak vytvořte pracovní adresář a podadresáře pro váš projekt:  

   ```
   mkdir -p flask-cog-services/static/scripts && mkdir flask-cog-services/templates
   ```
2. Přejděte do pracovního adresáře vašeho projektu:  

   ```
   cd flask-cog-services
   ```

### <a name="create-and-activate-your-virtual-environment-with-virtualenv"></a>Vytvoření a aktivace virtuálního prostředí pomocí `virtualenv`

Pojďme vytvořit virtuální prostředí pro naši aplikaci v baňce pomocí `virtualenv` . Pomocí virtuálního prostředí zajistíte, aby bylo k dispozici čisté prostředí, ze kterého můžete pracovat.

1. V pracovním adresáři spusťte tento příkaz a vytvořte virtuální prostředí: **MacOS/Linux:**
   ```
   virtualenv venv --python=python3
   ```
   Explicitně jsme deklarovali, že virtuální prostředí by mělo používat Python 3. Tím se zajistí, že uživatelé s více instalacemi Pythonu používají správnou verzi.

   **Windows CMD/Windows bash:**
   ```
   virtualenv venv
   ```
   Abychom mohli něco zjednodušit, pojmenováváme vaše virtuální prostředí venv.

2. Příkazy pro aktivaci virtuálního prostředí se budou lišit v závislosti na vaší platformě nebo prostředí:   

   | Platforma | Prostředí | Příkaz |
   |----------|-------|---------|
   | macOS/Linux | bash/zsh | `source venv/bin/activate` |
   | Windows | bash | `source venv/Scripts/activate` |
   | | Příkazový řádek | `venv\Scripts\activate.bat` |
   | | PowerShell | `venv\Scripts\Activate.ps1` |

   Po spuštění tohoto příkazu by se měl na příkazovém řádku nebo relaci terminálu předcházet `venv` .

3. Relaci můžete kdykoli deaktivovat zadáním příkazu do příkazového řádku nebo terminálu: `deactivate` .

> [!NOTE]
> Python obsahuje rozsáhlou dokumentaci k vytváření a správě virtuálních prostředí, viz [virtualenv](https://virtualenv.pypa.io/en/latest/).

### <a name="install-requests"></a>Požadavky na instalaci

Požadavky jsou oblíbený modul, který se používá k odesílání požadavků HTTP 1,1. K adresám URL není nutné přidávat řetězce dotazů ručně, nebo vyformátovat data POST.

1. Chcete-li nainstalovat požadavky, spusťte příkaz:

   ```
   pip install requests
   ```

> [!NOTE]
> Pokud se chcete dozvědět víc o požadavcích, přečtěte si téma [požadavky: http pro lidi](https://2.python-requests.org/en/master/).

### <a name="install-and-configure-flask"></a>Instalace a konfigurace baňky

Dál musíme nainstalovat baňce. Baňka zpracovává směrování pro naši webovou aplikaci a umožňuje nám vydávat volání mezi servery, které skrývá naše klíče předplatného od koncového uživatele.

1. Pro instalaci baňky spusťte příkaz:
   ```
   pip install Flask
   ```
   Pojďme se ujistit, že se nainstalovala baňka. Spusťte tento příkaz:
   ```
   flask --version
   ```
   Verze by měla být vytištěna na terminálu. Cokoli jiného znamená, že se něco pokazilo.

2. Pokud chcete spustit aplikaci v baňce, můžete použít příkaz baňky nebo přepínač-m v Pythonu s baňkou. Než to budete moct udělat, musíte říct terminálu, se kterým má aplikace spolupracovat, a to tak, že exportuje `FLASK_APP` proměnnou prostředí:

   **MacOS/Linux**:
   ```
   export FLASK_APP=app.py
   ```

   **Windows**:
   ```
   set FLASK_APP=app.py
   ```

## <a name="create-your-flask-app"></a>Vytvoření aplikace v baňce

V této části se chystáte vytvořit aplikaci Barebones, která vrátí soubor HTML, když uživatelé narazí na kořen vaší aplikace. Nevěnujte příliš mnoho času při pokusu o oddálení kódu, vrátíme se zpátky a aktualizujeme tento soubor později.

### <a name="what-is-a-flask-route"></a>Co je to postup v baňce?

Pojďme si vymluvit o[trasách](http://flask.pocoo.org/docs/1.0/api/#flask.Flask.route). Směrování se používá k vytvoření vazby adresy URL ke konkrétní funkci. V baňce se k registraci funkcí na konkrétní adresy URL používá dekoratéry trasy. Například když uživatel přejde na kořen ( `/` ) naší webové aplikace, `index.html` je vykreslen.  

```python
@app.route('/')
def index():
    return render_template('index.html')
```

Pojďme se podívat na jeden další příklad pro kladivo tohoto domova.

```python
@app.route('/about')
def about():
    return render_template('about.html')
```

Tento kód zajišťuje, že když uživatel přejde k tomu `http://your-web-app.com/about` , že se `about.html` soubor vykreslí.

I když tyto ukázky ilustrují, jak vykreslovat stránky HTML pro uživatele, trasy lze také použít k volání rozhraní API při stisknutí tlačítka nebo provedení libovolného počtu akcí, aniž byste museli opustit domovskou stránku. Tato akce se zobrazí v akci, když vytvoříte trasy pro syntézu překladu, mínění a řeči.

### <a name="get-started"></a>Začínáme

1. Otevřete projekt v integrovaném vývojovém prostředí a pak vytvořte soubor s názvem `app.py` v kořenovém adresáři pracovního adresáře. Potom zkopírujte tento kód do `app.py` a uložte:

   ```python
   from flask import Flask, render_template, url_for, jsonify, request

   app = Flask(__name__)
   app.config['JSON_AS_ASCII'] = False

   @app.route('/')
   def index():
       return render_template('index.html')
   ```

   Tento blok kódu oznamuje aplikaci, aby se zobrazila `index.html` vždy, když uživatel přejde do kořenového adresáře vaší webové aplikace ( `/` ).

2. Nyní vytvoříme front-end pro naši webovou aplikaci. Vytvořte `index.html` v adresáři soubor s názvem `templates` . Pak tento kód zkopírujte do `templates/index.html` .

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

3. Pojďme otestovat aplikaci v baňce. Z terminálu spusťte:

   ```
   flask run
   ```

4. Otevřete prohlížeč a přejděte na adresu URL, která je k dispozici. Měla by se zobrazit vaše jediná stránková aplikace. Stisknutím **kombinace kláves CTRL + C** aplikaci ukončete.

## <a name="translate-text"></a>Přeložení textu

Teď, když máte představu o tom, jak funguje jednoduchá aplikace v baňce, pojďme:

* Napište nějaký Python pro volání překladatele a vraťte odpověď.
* Vytvoření trasy v baňce pro volání kódu Pythonu
* Aktualizace kódu HTML pomocí oblasti pro textové zadání a překlad, selektor jazyka a tlačítko přeložit
* Psaní JavaScriptu, který umožňuje uživatelům pracovat s vaší aplikací v baňce z HTML

### <a name="call-the-translator"></a>Volání překladatele

První věc, kterou potřebujete udělat, je napsat funkci pro volání překladatele. Tato funkce provede dva argumenty: `text_input` a `language_output` . Tato funkce se volá vždycky, když uživatel stiskne tlačítko přeložit ve vaší aplikaci. Textová oblast v HTML se odešle jako `text_input` a jako hodnota výběru jazyka v HTML se pošle jako `language_output` .

1. Pojďme začít vytvořením souboru s názvem `translate.py` v kořenovém adresáři vašeho pracovního adresáře.
2. Dále přidejte tento kód do `translate.py` . Tato funkce přijímá dva argumenty: `text_input` a `language_output` .
   ```python
   import os, requests, uuid, json

   # Don't forget to replace with your Cog Services subscription key!
   # If you prefer to use environment variables, see Extra Credit for more info.
   subscription_key = 'YOUR_TRANSLATOR_TEXT_SUBSCRIPTION_KEY'
   location = 'YOUR_TRANSLATOR_RESOURCE_LOCATION'
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
           'Ocp-Apim-Subscription-Region': location,
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
3. Přidejte klíč předplatného překladatele a uložte ho.

### <a name="add-a-route-to-apppy"></a>Přidat trasu k `app.py`

V dalším kroku budete muset vytvořit trasu v aplikaci v baňce, která volá `translate.py` . Tato trasa bude volána pokaždé, když uživatel stiskne tlačítko přeložit ve vaší aplikaci.

V případě této aplikace bude vaše trasa přijímat `POST` požadavky. Důvodem je, že funkce očekává převod textu a výstupní jazyk pro překlad.

Baňka poskytuje pomocné funkce, které vám pomůžou analyzovat a spravovat jednotlivé požadavky. V poskytnutém kódu `get_json()` vrátí data z `POST` požadavku jako JSON. Pak použijete `data['text']` a `data['to']` , textové a výstupní hodnoty jazyka jsou předány `get_translation()` funkci dostupné z `translate.py` . Posledním krokem je vrátit odpověď jako JSON, protože je potřeba zobrazit tato data ve vaší webové aplikaci.

V následujících částech se tento proces opakuje při vytváření tras pro analýzu mínění a syntézu řeči.

1. Otevřete `app.py` a vyhledejte příkaz Import v horní části `app.py` a přidejte následující řádek:

   ```python
   import translate
   ```
   Nyní může aplikace v baňce používat metodu dostupnou prostřednictvím `translate.py` .

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

### <a name="update-indexhtml"></a>Aktualizace souboru `index.html`

Teď, když máte funkci pro překlad textu a trasu do vaší aplikace, která ji volá, je dalším krokem zahájení vytváření HTML pro vaši aplikaci. Následující kód HTML má několik věcí:

* Poskytuje textovou oblast, kde mohou uživatelé zadat text k překladu.
* Obsahuje selektor jazyka.
* Obsahuje prvky HTML pro vykreslení zjištěného jazyka a skóre spolehlivosti vrácené během překladu.
* Poskytuje textovou oblast, která je jen pro čtení, kde se zobrazuje výstup překladu.
* Obsahuje zástupné symboly pro kód analýzy mínění a kód pro syntézu řeči, který do tohoto souboru přidáte později v tomto kurzu.

Pojďme aktualizovat `index.html` .

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

Dalším krokem je psaní JavaScriptu. Toto je most mezi cestou HTML a baňkou.

### <a name="create-mainjs"></a>Vytvořeny `main.js`  

`main.js`Soubor je most mezi cestou HTML a baňkou. Vaše aplikace bude používat kombinaci jQuery, AJAX a XMLHttpRequest pro vykreslování obsahu a provádění `POST` požadavků na vaše trasy v baňce.

V níže uvedeném kódu se k vytvoření požadavku na svou trasu na baňce používá obsah z HTML. Konkrétně je obsah textové oblasti a selektor jazyka přiřazen k proměnným a pak je předána společně v žádosti `translate-text` .

Kód pak provede iteraci odpovědí a aktualizuje HTML pomocí překladu, zjištěného jazyka a skóre spolehlivosti.

1. V rámci integrovaného vývojového prostředí vytvořte soubor s názvem `main.js` v `static/scripts` adresáři.
2. Kopírovat tento kód do `static/scripts/main.js` :
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

### <a name="test-translation"></a>Testování překladu

Pojďme testovat převod v aplikaci.

```
flask run
```

Přejděte na zadanou adresu serveru. Do vstupní oblasti zadejte text, vyberte jazyk a stiskněte přeložit. Měli byste získat překlad. Pokud nefunguje, ujistěte se, že jste přidali klíč předplatného.

> [!TIP]
> Pokud se změny, které jste provedli, nezobrazují, nebo aplikace nefunguje tak, jak byste ji očekávali, zkuste vymazat mezipaměť nebo otevřít soukromé/anonymním okno.

Stisknutím **kombinace kláves Ctrl + c** aplikaci ukončete a potom přejděte k další části.

## <a name="analyze-sentiment"></a>Analýza mínění

[Rozhraní API pro analýzu textu](../text-analytics/overview.md) lze použít k provedení analýzy mínění, extrakci klíčových frází z textu nebo rozpoznání zdrojového jazyka. V této aplikaci použijeme analýzu mínění k určení, jestli je poskytnutý text kladný, neutrální nebo záporný. Rozhraní API vrací číselné skóre v rozsahu 0 až 1. Skóre blížící se 1 značí pozitivní mínění a skóre blížící se 0 značí negativní mínění.

V této části se chystáte několik věcí:

* Napište nějaký Python pro volání rozhraní API pro analýzu textu k provedení analýzy mínění a vrácení odpovědi.
* Vytvoření trasy v baňce pro volání kódu Pythonu
* Aktualizujte kód HTML o oblast pro mínění skóre a tlačítko pro provedení analýzy.
* Psaní JavaScriptu, který umožňuje uživatelům pracovat s vaší aplikací v baňce z HTML

### <a name="call-the-text-analytics-api"></a>Volání rozhraní Text Analytics API

Pojďme napsat funkci, která volá rozhraní API pro analýzu textu. Tato funkce bude mít čtyři argumenty: `input_text` , `input_language` , a `output_text` `output_language` . Tato funkce se volá vždycky, když uživatel stiskne v aplikaci tlačítko pro analýzu spuštění mínění. Data poskytnutá uživatelem z oblasti textu a výběr jazyka a také zjištěný jazyk a překlad překladu jsou k dispozici u jednotlivých požadavků. Objekt Response obsahuje skóre mínění pro zdroj a překlad. V následujících částech budete psát nějaký JavaScript, abyste mohli analyzovat odpověď a použít ji ve vaší aplikaci. Prozatím se podíváme na volání rozhraní API pro analýzu textu.

1. Pojďme vytvořit soubor s názvem `sentiment.py` v kořenovém adresáři vašeho pracovního adresáře.
2. Dále přidejte tento kód do `sentiment.py` .
   ```python
   import os, requests, uuid, json

   # Don't forget to replace with your Cog Services subscription key!
   subscription_key = 'YOUR_TEXT_ANALYTICS_SUBSCRIPTION_KEY'
   endpoint = "YOUR_TEXT_ANALYTICS_ENDPOINT" 
   # Our Flask route will supply four arguments: input_text, input_language,
   # output_text, output_language.
   # When the run sentiment analysis button is pressed in our Flask app,
   # the Ajax request will grab these values from our web app, and use them
   # in the request. See main.js for Ajax calls.

   def get_sentiment(input_text, input_language):
       path = '/text/analytics/v3.0/sentiment'
       constructed_url = endpoint + path

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
           ]
       }
       response = requests.post(constructed_url, headers=headers, json=body)
       return response.json()
   ```
3. Přidejte svůj klíč předplatného Analýza textu a uložte ho.

### <a name="add-a-route-to-apppy"></a>Přidat trasu k `app.py`

Pojďme v aplikaci v baňce vytvořit trasu, která volá `sentiment.py` . Tato trasa bude volána pokaždé, když uživatel stiskne tlačítko pro analýzu spuštění mínění ve vaší aplikaci. Podobně jako u trasy k překladu Tato trasa přijímá `POST` požadavky, protože funkce očekává argumenty.

1. Otevřete `app.py` a vyhledejte příkaz Import v horní části `app.py` a aktualizujte ho:

   ```python
   import translate, sentiment
   ```
   Nyní může aplikace v baňce používat metodu dostupnou prostřednictvím `sentiment.py` .

2. Zkopírujte tento kód na konec `app.py` a uložte:
   ```python
   @app.route('/sentiment-analysis', methods=['POST'])
   def sentiment_analysis():
       data = request.get_json()
       input_text = data['inputText']
       input_lang = data['inputLanguage']
       response = sentiment.get_sentiment(input_text, input_lang)
       return jsonify(response)
   ```

### <a name="update-indexhtml"></a>Aktualizace souboru `index.html`

Teď, když máte funkci pro spuštění analýzy mínění a trasu do vaší aplikace, která ji volá, je dalším krokem spuštění psaní HTML pro vaši aplikaci. Následující kód HTML má několik věcí:

* Přidá do aplikace tlačítko pro spuštění analýzy mínění
* Přidá prvek, který vysvětluje hodnocení mínění
* Přidá prvek pro zobrazení výsledků mínění

1. Otevřete `index.html` a vyhledejte tyto komentáře kódu:
   ```html
   <!-- Start sentiment code-->

   <!-- End sentiment code -->
   ```

2. Nahraďte komentáře kódu tímto blokem HTML:
   ```html
   <button type="submit" class="btn btn-primary mb-2" id="sentiment-analysis">Run sentiment analysis</button></br>
   <div id="sentiment" style="display: none">
      <p>Sentiment can be labeled as "positive", "negative", "neutral", or "mixed". </p>
      <strong>Sentiment label for input:</strong> <span id="input-sentiment"></span><br />
   </div>
   ```

### <a name="update-mainjs"></a>Aktualizace souboru `main.js`

V níže uvedeném kódu se k vytvoření požadavku na svou trasu na baňce používá obsah z HTML. Konkrétně je obsah textové oblasti a selektor jazyka přiřazen k proměnným a pak je předána spolu s požadavkem do `sentiment-analysis` trasy.

Kód pak projde odpověďmi a aktualizuje kód HTML pomocí mínění skóre.

1. V rámci integrovaného vývojového prostředí vytvořte soubor s názvem `main.js` v `static` adresáři.

2. Kopírovat tento kód do `static/scripts/main.js` :
   ```javascript
   //Run sentiment analysis on input and translation.
   $("#sentiment-analysis").on("click", function(e) {
     e.preventDefault();
     var inputText = document.getElementById("text-to-translate").value;
     var inputLanguage = document.getElementById("detected-language-result").innerHTML;
     var outputText = document.getElementById("translation-result").value;
     var outputLanguage = document.getElementById("select-language").value;

     var sentimentRequest = { "inputText": inputText, "inputLanguage": inputLanguage};

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
                 document.getElementById("input-sentiment").textContent = data.documents[i].sentiment;
               }
             }
           }
           for (var i = 0; i < data.errors.length; i++) {
             if (typeof data.errors[i] !== "undefined"){
               if (data.errors[i].id === "1") {
                 document.getElementById("input-sentiment").textContent = data.errors[i].message;
               }
             }
           }
           if (document.getElementById("input-sentiment").textContent !== ''){
             document.getElementById("sentiment").style.display = "block";
           }
         }
       });
     }
   });
   // In the next section, you'll add code for speech synthesis here.
   ```

### <a name="test-sentiment-analysis"></a>Analýza mínění testování

Mínění analýzy testování v aplikaci.

```
flask run
```

Přejděte na zadanou adresu serveru. Do vstupní oblasti zadejte text, vyberte jazyk a stiskněte přeložit. Měli byste získat překlad. Potom stiskněte tlačítko spustit analýzu mínění. Měla by se zobrazit dvě skóre. Pokud nefunguje, ujistěte se, že jste přidali klíč předplatného.

> [!TIP]
> Pokud se změny, které jste provedli, nezobrazují, nebo aplikace nefunguje tak, jak byste ji očekávali, zkuste vymazat mezipaměť nebo otevřít soukromé/anonymním okno.

Stisknutím **kombinace kláves Ctrl + c** aplikaci ukončete a potom přejděte k další části.

## <a name="convert-text-to-speech"></a>Převod textu na řeč

[Rozhraní API pro převod textu na mluvené slovo](../speech-service/text-to-speech.md) umožňuje, aby aplikace převedla text na syntetizované rozpoznávání řeči od přirozeného člověka. Služba podporuje standardní, neuronové a vlastní hlasy. Naše ukázková aplikace používá několik dostupných hlasů. úplný seznam najdete v části [podporované jazyky](../speech-service/language-support.md#text-to-speech).

V této části se chystáte několik věcí:

* Psaní některých Pythonů pro převod textu na řeč pomocí rozhraní API pro převod textu na řeč
* Vytvoření trasy v baňce pro volání kódu Pythonu
* Aktualizujte kód HTML pomocí tlačítka pro převod textu na řeč a prvku pro přehrávání zvuku.
* Psaní JavaScriptu, který umožňuje uživatelům pracovat s aplikací v baňce

### <a name="call-the-text-to-speech-api"></a>Volání rozhraní API pro převod textu na mluvené slovo

Pojďme napsat funkci pro převod textu na řeč. Tato funkce provede dva argumenty: `input_text` a `voice_font` . Tato funkce se volá vždycky, když uživatel stiskne v aplikaci tlačítko převést text na řeč. `input_text` je výstup překladu vrácený voláním k překladu textu, `voice_font` je hodnota z selektor hlasového písma v HTML.

1. Pojďme vytvořit soubor s názvem `synthesize.py` v kořenovém adresáři vašeho pracovního adresáře.

2. Dále přidejte tento kód do `synthesize.py` .
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

### <a name="add-a-route-to-apppy"></a>Přidat trasu k `app.py`

Pojďme v aplikaci v baňce vytvořit trasu, která volá `synthesize.py` . Tato trasa bude volána pokaždé, když uživatel stiskne tlačítko převést text na řeč v aplikaci. Podobně jako trasy pro překlad a analýzu mínění Tato trasa přijímá `POST` požadavky, protože funkce očekává dva argumenty: text, který se má syntetizovat, a písmo hlasu pro přehrávání.

1. Otevřete `app.py` a vyhledejte příkaz Import v horní části `app.py` a aktualizujte ho:

   ```python
   import translate, sentiment, synthesize
   ```
   Nyní může aplikace v baňce používat metodu dostupnou prostřednictvím `synthesize.py` .

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

### <a name="update-indexhtml"></a>Aktualizace souboru `index.html`

Teď, když máte funkci pro převod textu na řeč a trasu v aplikaci, abyste ji mohli zavolat, je dalším krokem spuštění psaní HTML pro vaši aplikaci. Následující kód HTML má několik věcí:

* Poskytuje rozevírací seznam pro výběr hlasu.
* Přidá tlačítko pro převod textu na řeč.
* Přidá zvukový prvek, který se používá k přehrání syntetizované řeči.

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
       <option value="(zh-TW, Yating, Apollo)">Chinese (Taiwan)| Female | Yating, Apollo</option>
       <option value="(zh-TW, Zhiwei, Apollo)">Chinese (Taiwan) | Male | Zhiwei, Apollo</option>
       <option value="(hr-HR, Matej)">Croatian | Male | Matej</option>
       <option value="(en-US, AriaRUS)">English (US) | Female | AriaRUS</option>
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
       <option value="(ko-KR, HeamiRUS)">Korean | Female | Heami</option>
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

5. Nezapomeňte svou práci uložit.

### <a name="update-mainjs"></a>Aktualizace souboru `main.js`

V níže uvedeném kódu se k vytvoření požadavku na svou trasu na baňce používá obsah z HTML. Konkrétně je převod a písmo hlasu přiřazena proměnným a pak jsou předány spolu v požadavku do `text-to-speech` trasy.

Kód pak projde odpověďmi a aktualizuje kód HTML pomocí mínění skóre.

1. V rámci integrovaného vývojového prostředí vytvořte soubor s názvem `main.js` v `static` adresáři.
2. Kopírovat tento kód do `static/scripts/main.js` :
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
3. Už jste téměř hotovi. Poslední věc, kterou se chystáte udělat, je přidání kódu k `main.js` automatickému výběru hlasového písma na základě jazyka vybraného pro překlad. Přidat tento blok kódu k `main.js` :
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

Pojďme vyzkoušet syntézu řeči v aplikaci.

```
flask run
```

Přejděte na zadanou adresu serveru. Do vstupní oblasti zadejte text, vyberte jazyk a stiskněte přeložit. Měli byste získat překlad. V dalším kroku vyberte hlas a pak stiskněte tlačítko převést text na řeč. překlad by měl být přehrán jako syntetizované rozpoznávání řeči. Pokud nefunguje, ujistěte se, že jste přidali klíč předplatného.

> [!TIP]
> Pokud se změny, které jste provedli, nezobrazují, nebo aplikace nefunguje tak, jak byste ji očekávali, zkuste vymazat mezipaměť nebo otevřít soukromé/anonymním okno.

To je to, že máte funkční aplikaci, která provádí překlady, analyzuje mínění a syntetizuje řeč. Stisknutím **kombinace kláves Ctrl + c** aplikaci ukončete. Nezapomeňte se podívat na ostatní [Cognitive Services Azure](../index.yml).

## <a name="get-the-source-code"></a>Získání zdrojového kódu

Zdrojový kód tohoto projektu je k dispozici na [GitHubu](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Flask-App-Tutorial).

## <a name="next-steps"></a>Další kroky

* [Reference překladatele](./reference/v3-0-reference.md)
* [Referenční informace k rozhraní API pro analýzu textu](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7)
* [Referenční informace k rozhraní API pro převod textu na řeč](../speech-service/rest-text-to-speech.md)