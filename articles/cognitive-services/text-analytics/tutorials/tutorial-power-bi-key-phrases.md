---
title: 'Kurz: Analýza textu s využitím Power BI'
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak pomocí Analýzy textu extrahovat klíčové fráze z textu uloženého v Power BI.
services: cognitive-services
author: luiscabrer
manager: cgronlun
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: tutorial
ms.date: 09/12/2018
ms.author: luisca
ms.openlocfilehash: 0bda38db089218e0d5f6f8ff15c9eac888900e95
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55209090"
---
# <a name="tutorial-integrate-power-bi-with-the-text-analytics-cognitive-service"></a>Kurz: Integrace Power BI s Text Analytics služby Cognitive Services

Microsoft Power BI Desktop je bezplatná aplikace, se kterou se můžete připojit k datům abyste je mohli transformovat a vizualizovat. Služba Analýza textu je součástí služby Microsoft Azure Cognitive Services. Umožňuje zpracovat jazyk v jeho přirozené podobě. Služba dokáže z nezpracovaného a nestrukturovaného textu extrahovat nejdůležitější fráze, analyzovat mínění a identifikovat známé prvky, jako jsou značky. Společně vám tyto služby můžou pomoct rychle zjistit, o čem mluví vaši zákazníci a jak se při tom cítí.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Používat Power BI Desktop k importu a transformaci dat
> * Vytvořit vlastní funkce v Power BI Desktopu
> * Integrovat Power BI Desktop do rozhraní API pro analýzu klíčových frází v textu
> * Používat rozhraní API pro analýzu klíčových frází v textu k extrakci nejdůležitějších frází z připomínek zákazníků
> * Vytvořit z připomínek zákazníků Word Cloud

## <a name="prerequisites"></a>Požadavky
<a name="Prerequisites"></a>

- Microsoft Power BI Desktop. [Stáhněte si ho zdarma](https://powerbi.microsoft.com/get-started/).
- Účet Microsoft Azure. [Začněte s bezplatnou zkušební verzí](https://azure.microsoft.com/free/) nebo se [přihlaste](https://portal.azure.com/).
- Účet rozhraní API služby Cognitive Services s rozhraním API pro analýzu textu. Pokud ho nemáte, můžete se [zaregistrovat](../../cognitive-services-apis-create-account.md) a použít pro tento kurz bezplatnou vrstvu 5000 transakcí/měsíc (viz [podrobnosti o cenách](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)).
- [Přístupový klíč k Analýze textu](../how-tos/text-analytics-how-to-access-key.md) vygenerovaný během registrace.
- Komentáře zákazníků. Můžete použít [naše ukázková data](https://aka.ms/cogsvc/ta) nebo vlastní data. V tomto kurzu se předpokládá, že použijete ukázková data.

## <a name="load-customer-data"></a>Načtení zákaznických dat
<a name="LoadingData"></a>

Začněte tím, že otevřete Power BI Desktop a načtete textový soubor s oddělovači (CSV) `FabrikamComments.csv`, který jste si stáhli v části [Požadavky](#Prerequisites). Tento soubor představuje denní hypotetickou aktivitu na fóru podpory malé fiktivní společnosti.

> [!NOTE]
> Power BI může využít data z celé řady zdrojů, jako je například Facebook nebo databáze SQL. Další informace najdete v tématech [Integrace Facebooku s Power BI](https://powerbi.microsoft.com/integrations/facebook/) a [Integrace SQL Serveru s Power BI](https://powerbi.microsoft.com/integrations/sql-server/).

V hlavním okně Power BI Desktopu vyberte pás karet **Domů**. Na pásu karet ve skupině **Externí data** otevřete rozevírací nabídku **Načíst data** a vyberte **Text/CSV**.

![[Tlačítko Získat data]](../media/tutorials/power-bi/get-data-button.png)

Zobrazí se dialogové okno Otevřít. Přejděte do složky Stažené soubory nebo do složky, kam jste si stáhli soubor `FabrikamComments.csv`. Klikněte na soubor `FabrikamComments.csv` a pak na tlačítko **Otevřít**. Zobrazí se dialogové okno pro import CSV.

![[Dialogové okno pro import CSV]](../media/tutorials/power-bi/csv-import.png)

V dialogovém okně pro import CSV můžete ověřit, jestli Power BI Desktop správně rozpoznal znakovou sadu, oddělovač, řádky záhlaví a typy sloupců. Pokud jsou tyto informace správně, klikněte na **Načíst**.

Pokud chcete zobrazit načtená data, klikněte na tlačítko **Zobrazení dat** na levém okraji pracovního prostoru Power BI. Otevře se tabulka s daty, která vidíte v Microsoft Excelu.

![[Počáteční zobrazení naimportovaných dat]](../media/tutorials/power-bi/initial-data-view.png)

## <a name="prepare-the-data"></a>Příprava dat
<a name="PreparingData"></a>

Před zpracováním možná budete muset data přeměnit v Power BI Desktopu, abyste je mohli zpracovat rozhraním API pro klíčové fráze služby Analýza textu.

Ukázková data obsahují sloupec `subject` a sloupec `comment`. Funkce sloučení sloupců v Power BI Desktopu extrahuje klíčové fráze z dat obou sloupců (místo jen ze sloupce `comment`).

V Power BI Desktopu vyberte pás karet **Domů**. Ve skupině **Externí data** klikněte na **Upravit dotazy**.

![[Skupina Externí data na pásu karet Domů]](../media/tutorials/power-bi/edit-queries.png)

V seznamu **Dotazy** vyberte na levé straně okna soubor `FabrikamComments`, pokud ještě není vybraný.

Teď v tabulce vyberte oba sloupce `subject` a `comment`. Možná budete muset zobrazení vodorovně posunout, aby se tyto sloupce zobrazily. Nejprve klikněte na záhlaví sloupce `subject`, pak podržte stisknutou klávesu Ctrl a klikněte na záhlaví sloupce `comment`.

![[Výběr polí, která se mají sloučit]](../media/tutorials/power-bi/select-columns.png)

Vyberte pás karet **Transformace**. Ve skupině **Textové sloupce** na pásu karet klikněte na **Sloučit sloupce**. Zobrazí se dialogové okno Sloučit sloupce.

![[Sloučení polí s využitím dialogového okna Sloučit sloupce]](../media/tutorials/power-bi/merge-columns.png)

V dialogovém okně Sloučit sloupce zvolte jako oddělovač `Tab` a klikněte na **OK**.

Můžete také vyfiltrovat prázdné zprávy filtrem Odebrat prázdné nebo odebrat netisknutelné znaky transformací Vyčistit. Pokud vaše data obsahují podobný sloupec, jako je sloupec `spamscore` v ukázkovém souboru, můžete použít číselný filtr k přeskočení nevyžádaných komentářů.

## <a name="understand-the-api"></a>Vysvětlení rozhraní API
<a name="UnderstandingAPI"></a>

[Rozhraní API Klíčové fráze](//westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) služby Analýza textu dokáže v jednom požadavku HTTP zpracovat až tisíc textových dokumentů. Power BI ale upřednostňuje postupné zpracování jednotlivých záznamů. V tomto kurzu bude ve voláních rozhraní API vždy jen jeden dokument. Rozhraní API Klíčové fráze vyžaduje, aby každý zpracovávaný dokument obsahoval následující pole.

| | |
| - | - |
| `id`  | Jedinečný identifikátor tohoto dokumentu v rámci požadavku. Odpověď také obsahuje toto pole. Tímto způsobem můžete v případě zpracování více dokumentů snadno přidružit extrahované klíčové fráze k dokumentu, ze kterého pocházejí. V tomto kurzu zpracováváte pro každou žádost jenom jeden dokument. Proto můžete hodnotu `id` pevně zadat, aby byla pro všechny požadavky stejná.|
| `text`  | Text, který se má zpracovat. Hodnota tohoto pole pochází ze sloupce `Merged` vytvořeného v [předchozím oddílu](#PreparingData). V tomto sloupci je sloučený řádek předmětu s textem komentáře. Rozhraní API pro klíčové fráze vyžaduje, aby tato data nepřesáhla délku přibližně 5 000 znaků.|
| `language` | Kód pro přirozený jazyk, ve kterém je dokument napsaný. Všechny zprávy v ukázkových datech jsou v angličtině, takže do tohoto pole můžete pevně zadat hodnotu `en`.|

## <a name="create-a-custom-function"></a>Vytvoření vlastní funkce
<a name="CreateCustomFunction"></a>

Teď jste připraveni vytvořit vlastní funkci, která integruje Power BI s Analýzou textu. Funkce jako parametr přijímá text ke zpracování. Převede data do požadovaného formátu JSON (nebo z něj) a vytvoří požadavek HTTP do rozhraní API Klíčové fráze. Dále funkce parsuje odpověď z API a vrátí řetězec, který obsahuje seznam extrahovaných klíčových frází oddělených čárkou.

> [!NOTE]
> Vlastní funkce Power BI Desktopu se píší v [jazyce vzorců Power Query M](https://msdn.microsoft.com/library/mt211003.aspx) nebo zkráceně jenom M. M je funkcionální programovací jazyk založený na [F#](https://docs.microsoft.com/dotnet/fsharp/). Pro dokončení tohoto kurzu však nemusíte být programátor – požadovaný kód je uvedený níže.

V Power BI Desktopu se ujistěte, že jste v okně Editoru dotazů. Pokud v něm nejste, vyberte pás karet **Domů** a ve skupině **Externí data** klikněte na **Upravit dotazy**.

Na pásu karet **Domů** otevřete ve skupině **Nový dotaz** rozevírací nabídku **Nový zdroj** a vyberte **Prázdný dotaz**. 

V seznamu dotazů se zobrazí nový dotaz s počátečním názvem `Query1`. Dvakrát na tuto položku klikněte a zadejte název `KeyPhrases`.

Na pásu karet **Domů** ve skupině **Dotaz** klikněte na **Rozšířený editor**. Otevře se okno rozšířeného editoru. Odstraňte kód, který okno už obsahuje, a vložte následující kód. 

> [!NOTE]
> Následující příklady předpokládají, že koncový bod rozhraní API pro analýzu textu začíná oblastí `https://westus.api.cognitive.microsoft.com`. Analýza textu umožňuje vytvořit předplatné ve 13 různých oblastech. Pokud jste si službu zaregistrovali v jiné oblasti, nezapomeňte použít koncový bod pro oblast, kterou jste vybrali. Tento koncový bod najdete, když se přihlásíte na webu [Azure Portal](https://azure.microsoft.com/features/azure-portal/), vyberete předplatné Analýza textu a vyberete stránku Přehled.

```fsharp
// Returns key phrases from the text in a comma-separated list
(text) => let
    apikey      = "YOUR_API_KEY_HERE",
    endpoint    = "https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/keyPhrases",
    jsontext    = Text.FromBinary(Json.FromValue(Text.Start(Text.Trim(text), 5000))),
    jsonbody    = "{ documents: [ { language: ""en"", id: ""0"", text: " & jsontext & " } ] }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),
    keyphrases  = Text.Lower(Text.Combine(jsonresp[documents]{0}[keyPhrases], ", "))
in  keyphrases
```

Nahraďte `YOUR_API_KEY_HERE` přístupovým klíčem Analýzy textu. Tento klíč také najdete, když se přihlásíte na webu [Azure Portal](https://azure.microsoft.com/features/azure-portal/), vyberete předplatné Analýza textu a vyberete stránku Přehled. Nezapomeňte nechat uvozovky před i za klíčem. Potom klikněte na **Hotovo**.

## <a name="use-the-custom-function"></a>Použití vlastní funkce
<a name="UseCustomFunction"></a>

Teď můžete použít vlastní funkci k extrakci klíčových frází ze všech komentářů zákazníků. Získané fráze uložíte do nového sloupce tabulky. 

V Power BI Desktopu se v okně Editoru dotazů vraťte zpět k dotazu `FabrikamComments`. Vyberte pás karet **Přidat sloupec**. Ve skupině **Obecné** klikněte na **Vyvolat vlastní funkci**.

![[Tlačítko Vyvolat vlastní funkci]](../media/tutorials/power-bi/invoke-custom-function-button.png)<br><br>

Zobrazí se dialogové okno Vyvolat vlastní funkci. Do pole **Nový název sloupce** zadejte `keyphrases`. V poli **Dotaz na funkci** vyberte vlastní funkci `KeyPhrases`, kterou jste vytvořili.

V dialogovém okně se zobrazí nové pole **text (volitelné)**. Toto pole se ptá, jaký sloupec chcete použít k zadání hodnot parametru `text` rozhraní API pro klíčové fráze (nezapomeňte, že jste hodnoty parametrů `language` a `id` už pevně zadali). V rozevírací nabídce vyberte `Merged` (sloupec jste vytvořili [dříve](#PreparingData) sloučením polí předmětu a zprávy).

![[Vyvolání vlastní funkce]](../media/tutorials/power-bi/invoke-custom-function.png)

Nakonec klikněte na **OK**.

Pokud je všechno připravené, Power BI volá vaši funkci pro každý řádek tabulky vždy jen jednou. Odešle dotazy rozhraní API pro klíčové fráze a přidá do tabulky nový sloupec s uloženými výsledky. Předtím však možná bude nutné zadat nastavení ověřování a ochrany osobních údajů.

## <a name="authentication-and-privacy"></a>Ověřování a ochrana osobních údajů
<a name="Authentication"></a>

Po zavření dialogového okna Vyvolat vlastní funkci se může zobrazit banner s výzvou, abyste upřesnili, jak se připojit k rozhraní API pro klíčová slova.

![[Banner s výzvou k zadání přihlašovacích údajů]](../media/tutorials/power-bi/credentials-banner.png)

Klikněte na **Upravit přihlašovací údaje**, v dialogovém okně se ujistěte, že je vybraná možnost `Anonymous`, a pak klikněte na **Připojit**. 

> [!NOTE]
> Vybrali jste `Anonymous`, protože služba Analýza textu použije k vašemu ověření přístupový klíč, aby řešení Power BI nemuselo poskytovat přihlašovací údaje pro samotný požadavek HTTP.

![[Nastavení ověřování na Anonymní]](../media/tutorials/power-bi/access-web-content.png)

Pokud se banner Upravit přihlašovací údaje zobrazuje i po zvolení anonymního přístupu, možná jste do kódu [vlastní funkce](#CreateCustomFunction) `KeyPhrases` zapomněli vložit svůj přístupový klíč Analýzy textu.

Dále se může zobrazit banner s výzvou k zadání informací o ochraně osobních údajů u vašich zdrojů dat. 

![[Banner s výzvou k zadání informací o ochraně osobních údajů]](../media/tutorials/power-bi/privacy-banner.png)

Klikněte na **Pokračovat** a pro každý zdroj dat v dialogu zvolte `Public`. Potom klikněte na **Uložit**.

![[Nastavení ochrany osobních údajů u zdrojů dat]](../media/tutorials/power-bi/privacy-dialog.png)

## <a name="create-the-word-cloud"></a>Vytvoření Word Cloudu
<a name="WordCloud"></a>

Jakmile vyřešíte všechny zobrazené bannery, kliknutím na **Zavřít a použít** na pásu karet Domů zavřete Editor dotazů.

Power BI Desktopu chvíli trvá, než provede potřebné požadavky HTTP. Sloupec `keyphrases` pro každý řádek tabulky obsahuje klíčové fráze rozpoznané v textu rozhraním API pro klíčové fráze. 

Teď tento sloupec použijete k vygenerování Word Cloudu. Začněte tím, že v hlavním okně Power BI Desktopu kliknete na tlačítko **Sestava**, které je nalevo od pracovního prostoru.

> [!NOTE]
> Proč k vygenerování Word Cloudu použít extrahované klíčové fráze, a ne úplný text každého komentáře? Klíčové fráze nám poskytují *důležitá* slova z komentářů našich zákazníků, ne pouze *nejčastější* slova. Navíc nedojde ke zkreslení velikosti slov ve výsledném Cloudu častým používáním určitého slova v relativně malém počtu komentářů.

Pokud ještě nemáte nainstalovaný vlastní vizuál Word Cloud, nainstalujte ho. Na panelu Vizualizace napravo od pracovního prostoru klikněte na tři tečky (**...**) a zvolte **Importovat ze Storu**. Vyhledejte „cloud“ a klikněte na tlačítko **Přidat** vedle vizuálu Word Cloud. Power BI nainstaluje vizuál Word Cloud a bude vás informovat o úspěšné instalaci.

![[Přidání vlastního vizuálu]](../media/tutorials/power-bi/add-custom-visuals.png)<br><br>

Nejprve klikněte na ikonu Word Cloud na panelu Vizualizace.

![[Ikona Word Cloud na panelu Vizualizace]](../media/tutorials/power-bi/visualizations-panel.png)

V pracovním prostoru se zobrazí nová sestava. Přetáhněte pole `keyphrases` z panelu Pole do pole Kategorie na panelu Vizualizace. Uvnitř sestavy se zobrazí Word Cloud.

Teď na panelu Vizualizace přepněte na stránku Formát. V kategorii Nevýznamová slova zapněte **Výchozí nevýznamová slova**. Tím se z Cloudu odstraní běžná krátká slova, jako je například „of“. 

![[Aktivace výchozích nevýznamových slov]](../media/tutorials/power-bi/default-stop-words.png)

O trochu níže na tomto panelu vypněte **Otočení textu** a **Nadpis**.

![[Aktivace detailního režimu]](../media/tutorials/power-bi/word-cloud-focus-mode.png)

Kliknutím na nástroj Detailní režim v sestavě získáte lepší zobrazení Word Cloudu. Tento nástroj roztáhne Word Cloud tak, aby zaplnil celý pracovní prostor, jak je znázorněno níže.

![[Word Cloud]](../media/tutorials/power-bi/word-cloud.png)

## <a name="more-text-analytics-services"></a>Další služby Analýzy textu
<a name="MoreServices"></a>

Služba Analýza textu, což je jedna ze služeb Cognitive Services, které Microsoft Azure nabízí, poskytuje také analýzu mínění a rozpoznávání jazyka. Rozpoznávání jazyka je užitečné hlavně v případě, že zpětná vazba od vašich zákazníků není vždy v angličtině.

Obě tato rozhraní API jsou podobná rozhraní API pro klíčové fráze. To znamená, že je můžete integrovat do Power BI Desktopu pomocí vlastních funkcí, které jsou skoro stejné jako funkce vytvořená v tomto kurzu. Stačí vytvořit prázdný dotaz a stejně jako předtím do Rozšířeného editoru vložit odpovídající kód uvedený níže. (Nezapomeňte na svůj přístupový klíč!) Pak stejně jako předtím použijte funkci, která do tabulky přidá nový sloupec.

Následující funkce Analýzy mínění vrátí skóre značící, jak pozitivní je mínění vyjádřené v textu.

```fsharp
// Returns the sentiment score of the text, from 0.0 (least favorable) to 1.0 (most favorable)
(text) => let
    apikey      = "YOUR_API_KEY_HERE",
    endpoint    = "https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/sentiment",
    jsontext    = Text.FromBinary(Json.FromValue(Text.Start(Text.Trim(text), 5000))),
    jsonbody    = "{ documents: [ { language: ""en"", id: ""0"", text: " & jsontext & " } ] }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),
    sentiment   = jsonresp[documents]{0}[score]
in  sentiment
```

Tady jsou dvě verze funkce Rozpoznávání jazyka. První vrátí kód ISO jazyka (například pro angličtinu je to `en`), zatímco druhá vrací popisný název (například `English`). Jak si můžete všimnout, tyto dvě verze se liší pouze v posledním řádku těla funkce.

```fsharp
// Returns the two-letter language code (for example, 'en' for English) of the text
(text) => let
    apikey      = "YOUR_API_KEY_HERE",
    endpoint    = "https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/languages",
    jsontext    = Text.FromBinary(Json.FromValue(Text.Start(Text.Trim(text), 5000))),
    jsonbody    = "{ documents: [ { id: ""0"", text: " & jsontext & " } ] }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),
    language    = jsonresp[documents]{0}[detectedLanguages]{0}[iso6391Name]
in  language
```
```fsharp
// Returns the name (for example, 'English') of the language in which the text is written
(text) => let
    apikey      = "YOUR_API_KEY_HERE",
    endpoint    = "https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/languages",
    jsontext    = Text.FromBinary(Json.FromValue(Text.Start(Text.Trim(text), 5000))),
    jsonbody    = "{ documents: [ { id: ""0"", text: " & jsontext & " } ] }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),
    language    = jsonresp[documents]{0}[detectedLanguages]{0}[name]
in  language
```

A konečně, tady je již uvedená varianta funkce Klíčové fráze, která vrátí fráze jako objekt seznamu, a ne jako jeden řetězec čárkami oddělených frází. 

> [!NOTE]
> Vracení jednoho řetězce sloužilo ke zjednodušení našeho příkladu Word Cloudu. Seznam je oproti tomu flexibilnější formát pro práci s vrácenými frázemi v Power BI. V Power BI Desktopu můžete s objekty seznamu pracovat s využitím skupiny Strukturovaný sloupec na pásu karet Transformace v Editoru dotazů.

```fsharp
// Returns key phrases from the text as a list object
(text) => let
    apikey      = "YOUR_API_KEY_HERE",
    endpoint    = "https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/keyPhrases",
    jsontext    = Text.FromBinary(Json.FromValue(Text.Start(Text.Trim(text), 5000))),
    jsonbody    = "{ documents: [ { language: ""en"", id: ""0"", text: " & jsontext & " } ] }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),
    keyphrases  = jsonresp[documents]{0}[keyPhrases]
in  keyphrases
```

## <a name="next-steps"></a>Další postup
<a name="NextSteps"></a>

Přečtěte si další informace o službě Analýza textu, jazyce vzorců Power Query M nebo Power BI.

> [!div class="nextstepaction"]
> [Referenční informace k rozhraní API pro analýzu textu](//westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6)

> [!div class="nextstepaction"]
> [Referenční informace k Power Query M](//msdn.microsoft.com/library/mt211003.aspx)

> [!div class="nextstepaction"]
> [Dokumentace k Power BI](//powerbi.microsoft.com/documentation/powerbi-landing-page/)
