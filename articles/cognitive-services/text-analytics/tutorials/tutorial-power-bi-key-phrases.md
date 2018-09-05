---
title: Analýza textu s využitím Power BI – Azure Cognitive Services | Microsoft Docs
description: Zjistěte, jak pomocí Analýzy textu extrahovat klíčové fráze z textu uloženého v Power BI.
services: cognitive-services
author: luiscabrer
manager: cgronlun
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: tutorial
ms.date: 3/07/2018
ms.author: luisca
ms.openlocfilehash: 2cdb93d44218627efdcb0360d8cf4a4eeeca177a
ms.sourcegitcommit: 7b845d3b9a5a4487d5df89906cc5d5bbdb0507c8
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/14/2018
ms.locfileid: "42889329"
---
# <a name="text-analytics-with-power-bi"></a>Analýza textu s využitím Power BI

Microsoft Power BI vytváří z dat organizace působivé sestavy, které můžete distribuovat v rámci organizace a získat tak rychlejší a podrobnější přehled. Služba Analýza textu, která je součástí služeb Cognitive Services v Microsoft Azure, dokáže z textu extrahovat nejdůležitější fráze prostřednictvím rozhraní API pro klíčové fráze, které obsahuje. Společně vám tyto služby můžou pomoct rychle zjistit, o čem mluví vaši zákazníci a jak se při tom cítí.

V tomto kurzu uvidíte, jak integrovat Power BI Desktop a rozhraní API pro klíčové fráze za účelem extrahování nejdůležitějších frází ze zpětné vazby zákazníků pomocí vlastní funkce Power Query. Z těchto frází také vytvoříme Word Cloud.

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu budete potřebovat:

> [!div class="checklist"]
> * Microsoft Power BI Desktop. [Stáhněte si ho zdarma](https://powerbi.microsoft.com/get-started/).
> * Účet Microsoft Azure. [Začněte s bezplatnou zkušební verzí](https://azure.microsoft.com/free/) nebo se [přihlaste](https://portal.azure.com/).
> * Přístupový klíč pro Analýzu textu. [Přihlaste se](../../cognitive-services-apis-create-account.md) a pak [získejte svůj klíč](../how-tos/text-analytics-how-to-access-key.md).
> * Komentáře zákazníků. [Stáhněte si naše ukázková data](https://aka.ms/cogsvc/ta) nebo použijte vlastní.

## <a name="loading-customer-data"></a>Načtení zákaznických dat

Začněte tím, že otevřete Power BI Desktop a načtete textový soubor s oddělovači (CSV) **FabrikamComments.csv**. Tento soubor představuje denní hypotetickou aktivitu na fóru podpory malé fiktivní společnosti.

> [!NOTE]
> Power BI může využít data z celé řady zdrojů, jako je například Facebook nebo databáze SQL. Další informace najdete v tématech [Integrace Facebooku s Power BI](https://powerbi.microsoft.com/integrations/facebook/) a [Integrace SQL Serveru s Power BI](https://powerbi.microsoft.com/integrations/sql-server/).

V hlavním okně Power BI Desktopu vyhledejte na pásu karet Domů skupinu Externí data. V rozevírací nabídce **Získat data** v této skupině zvolte **Text/CSV**.

![[Tlačítko Získat data]](../media/tutorials/power-bi/get-data-button.png)

Zobrazí se dialogové okno Otevřít. Přejděte do složky Stažené soubory nebo jiné složky, která obsahuje ukázkový datový soubor. Klikněte na soubor `FabrikamComments.csv` a pak na tlačítko **Otevřít**. Zobrazí se dialogové okno pro import CSV.

![[Dialogové okno pro import CSV]](../media/tutorials/power-bi/csv-import.png)

V dialogovém okně pro import CSV můžete ověřit, jestli Power BI Desktop správně rozpoznal znakovou sadu, oddělovač, řádky záhlaví a typy sloupců. Všechny tyto údaje jsou správné, takže klikneme na **Načíst**.

Pokud chcete zobrazit načtená data, pomocí tlačítka Zobrazení dat u levého okraje pracovního prostoru Power BI přepněte na zobrazení dat. Otevře se podobná tabulka jako v aplikaci Microsoft Excel, která obsahuje naše data.

![[Počáteční zobrazení naimportovaných dat]](../media/tutorials/power-bi/initial-data-view.png)

## <a name="preparing-the-data"></a>Příprava dat

Možná bude potřeba připravit data na zpracování službou Klíčové fráze tím, že je transformujete v Power BI Desktopu.

Například naše data obsahují pole `subject` i `comment`. Při extrahování klíčových frází bychom měli zohlednit text v obou těchto polích, ne pouze v poli `comment`. Tento úkol usnadňuje funkce Sloučit sloupce v Power BI Desktopu.

Kliknutím na **Upravit dotazy** ve skupině Externí data na pásu karet Domů v hlavním okně Power BI Desktopu otevřete Editor dotazů. 

![[Skupina Externí data na pásu karet Domů]](../media/tutorials/power-bi/edit-queries.png)

Pokud ještě není vybraný, v seznamu Dotazy na levé straně okna vyberte dotaz FabrikamComments.

Teď v tabulce vyberte oba sloupce `subject` a `comment`. Možná budete muset zobrazení vodorovně posunout, aby se tyto sloupce zobrazily. Nejprve klikněte na záhlaví sloupce `subject`, pak podržte stisknutou klávesu Ctrl a klikněte na záhlaví sloupce `comment`.

![[Výběr polí, která se mají sloučit]](../media/tutorials/power-bi/select-columns.png)

Ve skupině Textové sloupce na pásu karet Transformace klikněte na **Sloučit sloupce**. Zobrazí se dialogové okno Sloučit sloupce.

![[Sloučení polí s využitím dialogového okna Sloučit sloupce]](../media/tutorials/power-bi/merge-columns.png)

V dialogovém okně Sloučit sloupce zvolte jako oddělovač tabulátor a pak klikněte na **OK**. Hotovo!

Můžete také zvážit vyfiltrování prázdných zpráv pomocí filtru Odebrat prázdné nebo odebrání netisknutelných znaků pomocí transformace Vyčistit. Pokud vaše data obsahují sloupec, jako je sloupec `spamscore` v našem ukázkovém souboru, můžete pomocí filtru Číslo přeskočit nevyžádané komentáře.

## <a name="understanding-the-api"></a>Vysvětlení rozhraní API

Rozhraní API pro klíčové fráze dokáže pro jeden požadavek HTTP zpracovat až tisíc textových dokumentů. Power BI však upřednostňuje postupné zpracování jednotlivých záznamů, takže naše volání rozhraní API budou obsahovat pouze jeden dokument. [Rozhraní API](//westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) vyžaduje, aby každý zpracovávaný dokument obsahoval následující pole.

| | |
| - | - |
| `id`  | Jedinečný identifikátor tohoto dokumentu v rámci požadavku. Odpověď také obsahuje toto pole. Díky tomu můžete v případě, že zpracováváte více dokumentů, snadno přidružit extrahované klíčové fráze k dokumentu, ze kterého pocházejí. My pro každý požadavek zpracováváme jeden dokument, takže už víme, ke kterému dokumentu je odpověď přidružená, a `id` tedy může být ve všech požadavcích stejné.|
| `text`  | Text, který se má zpracovat. Získáme ho ze sloupce `Merged`, který jsme vytvořili a který obsahuje řádek předmětu sloučený s textem komentáře. Rozhraní API pro klíčové fráze vyžaduje, aby tato data nepřesáhla délku přibližně 5 000 znaků.|
| `language` | Kód představující jazyk, ve kterém je dokument napsaný. Všechny naše zprávy jsou v angličtině, takže v našem dotazu můžeme pevně zakódovat jazyk `en`.|

Pro odeslání do rozhraní API pro klíčové fráze potřebujeme tato pole zkombinovat do dokumentu JSON (JavaScript Object Notation). Odpověď na tento požadavek je také dokument JSON, který musíme parsovat a získat z něj klíčové fráze.

## <a name="creating-a-custom-function"></a>Vytvoření vlastní funkce

Teď jsme připraveni vytvořit vlastní funkci, která provede integraci Power BI a Analýzy textu. Power BI Desktop tuto funkci zavolá pro každý řádek tabulky a vytvoří nový sloupec s výsledky.

Funkce jako parametr přijímá text ke zpracování. Převádí data do a z požadovaného formátu JSON (JavaScript Object Notation) a odesílá požadavky HTTP do koncového bodu rozhraní API pro klíčové fráze. Po dokončení parsování odpovědi vrátí funkce řetězec obsahující čárkami oddělený seznam extrahovaných klíčových frází.

> [!NOTE]
> Vlastní funkce Power BI Desktopu se píší v [jazyce vzorců Power Query M](https://msdn.microsoft.com/library/mt211003.aspx) nebo zkráceně jenom M. M je funkcionální programovací jazyk založený na [F#](https://docs.microsoft.com/dotnet/fsharp/). Pro dokončení tohoto kurzu však nemusíte být programátor – požadovaný kód je uvedený níže.

Stále byste měli být v okně Editoru dotazů. Na pásu karet Domů klikněte na **Nový zdroj** (ve skupině Nový dotaz) a v rozevírací nabídce zvolte **Prázdný dotaz**. 

V seznamu Dotazy se zobrazí nový dotaz s počátečním názvem Query1. Dvakrát na tuto položku klikněte a zadejte název `KeyPhrases`.

Teď kliknutím na **Rozšířený editor** ve skupině Dotaz na pásu karet Domů otevřete okno Rozšířený editor. Odstraňte kód, který okno už obsahuje, a vložte následující kód. 

> [!NOTE]
> V následujících příkladech předpokládáme, že se koncový bod nachází na adrese https://westus.api.cognitive.microsoft.com.  Analýza textu podporuje vytvoření předplatného ve 13 různých oblastech. Pokud jste si službu zaregistrovali v jiné oblasti, nezapomeňte použít koncový bod pro oblast, kterou jste vybrali. Měl by se zobrazit na stránce Přehled na webu Azure Portal, když vyberete své předplatné Analýzy textu.

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

Na řádku začínajícím na `apikey` vložte také svůj klíč rozhraní API pro analýzu textu, který získáte z řídicího panelu Microsoft Azure. (Nezapomeňte ponechat uvozovky před a za klíčem.) Potom klikněte na **Hotovo**.

## <a name="using-the-function"></a>Použití funkce

Teď můžeme pomocí vlastní funkce získat klíčové fráze obsažené v jednotlivých komentářích našich zákazníků a uložit je do nového sloupce tabulky. 

Stále v Editoru dotazů přepněte zpět na dotaz FabrikamComments, přejděte na pás karet Přidat sloupec a klikněte na tlačítko **Vyvolat vlastní funkci** ve skupině Obecné.

![[Tlačítko Vyvolat vlastní funkci]](../media/tutorials/power-bi/invoke-custom-function-button.png)<br><br>

V dialogovém okně Vyvolat vlastní funkci jako název nového sloupce zadejte `keyphrases`. Jako Dotaz funkce zvolte naši vlastní funkci `KeyPhrases`. 

V dialogovém okně se zobrazí nové pole s dotazem, jaké pole chceme naší funkci předat jako parametr `text`. V rozevírací nabídce zvolte `Merged` (sloupec, který jsme vytvořili dříve sloučením polí s předmětem a zprávou).

![[Vyvolání vlastní funkce]](../media/tutorials/power-bi/invoke-custom-function.png)

Nakonec klikněte na **OK**.

Pokud je vše připravené, Power BI zavolá naši funkci pro každý řádek tabulky, provede dotazy na klíčové fráze a přidá do tabulky nový sloupec. Předtím však možná bude nutné zadat nastavení ověřování a ochrany osobních údajů.

## <a name="authentication-and-privacy"></a>Ověřování a ochrana osobních údajů

Po zavření dialogového okna Vyvolat vlastní funkci se může zobrazit banner s výzvou k určení způsobu připojení ke koncovému bodu služby Klíčová slova.

![[Banner s výzvou k zadání přihlašovacích údajů]](../media/tutorials/power-bi/credentials-banner.png)

Klikněte na **Upravit přihlašovací údaje**, v dialogovém okně se ujistěte, že je vybraná možnost Anonymní, a pak klikněte na **Připojit**. 

> [!NOTE]
> Proč anonymní? Služba Analýza textu vás ověří pomocí klíče rozhraní API, takže Power BI nemusí poskytovat přihlašovací údaje pro samotný požadavek HTTP.

![[Nastavení ověřování na Anonymní]](../media/tutorials/power-bi/access-web-content.png)

Pokud se banner Upravit přihlašovací údaje zobrazuje i po zvolení anonymního přístupu, možná jste zapomněli vložit svůj klíč rozhraní API. Zkontrolujte vlastní funkci `KeyPhrases` v Rozšířeném editoru a ujistěte se, že obsahuje váš klíč rozhraní API.

Dále se může zobrazit banner s výzvou k zadání informací o ochraně osobních údajů u vašich zdrojů dat. 

![[Banner s výzvou k zadání informací o ochraně osobních údajů]](../media/tutorials/power-bi/privacy-banner.png)

Klikněte na **Pokračovat** a pro každý zdroj dat v dialogovém okně zvolte Veřejné. Potom klikněte na **Uložit**.

![[Nastavení ochrany osobních údajů u zdrojů dat]](../media/tutorials/power-bi/privacy-dialog.png)

## <a name="creating-the-word-cloud"></a>Vytvoření Word Cloudu

Jakmile vyřešíte všechny zobrazené bannery, kliknutím na **Zavřít a použít** na pásu karet Domů zavřete Editor dotazů.

Power BI Desktopu chvíli trvá, než provede potřebné požadavky HTTP. Sloupec `keyphrases` pro každý řádek tabulky obsahuje klíčové fráze rozpoznané v textu rozhraním API pro klíčové fráze. 

Teď tento sloupec použijeme k vygenerování Word Cloudu. Začněte kliknutím na tlačítko Sestava nalevo od pracovního prostoru v hlavním okně Power BI Desktopu.

> [!NOTE]
> Proč k vygenerování Word Cloudu použít extrahované klíčové fráze, a ne úplný text každého komentáře? Klíčové fráze nám poskytují *důležitá* slova z komentářů našich zákazníků, ne pouze *nejčastější* slova. Navíc nedojde ke zkreslení velikosti slov ve výsledném Cloudu častým používáním určitého slova v relativně malém počtu komentářů.

Pokud ještě nemáte nainstalovaný vlastní vizuál Word Cloud, nainstalujte ho. Na panelu Vizualizace napravo od pracovního prostoru klikněte na tři tečky (**...**) a zvolte **Importovat ze Storu**. Vyhledejte „cloud“ a klikněte na tlačítko **Přidat** vedle vizuálu Word Cloud. Power BI nainstaluje vizuál Word Cloud a bude vás informovat o jeho úspěšném nainstalování.

![[Přidání vlastního vizuálu]](../media/tutorials/power-bi/add-custom-visuals.png)<br><br>

Teď pojďme vytvořit vlastní Word Cloud!

![[Ikona Word Cloud na panelu Vizualizace]](../media/tutorials/power-bi/visualizations-panel.png)

Nejprve klikněte na ikonu Word Cloud na panelu Vizualizace. V pracovním prostoru se zobrazí nová sestava. Přetáhněte pole `keyphrases` z panelu Pole do pole Kategorie na panelu Vizualizace. Uvnitř sestavy se zobrazí Word Cloud.

Teď na panelu Vizualizace přepněte na stránku Formát. V kategorii Nevýznamová slova zapněte **Výchozí nevýznamová slova**. Tím se z Cloudu odstraní běžná krátká slova, jako je například „of“. 

![[Aktivace výchozích nevýznamových slov]](../media/tutorials/power-bi/default-stop-words.png)

O trochu níže na tomto panelu vypněte **Otočení textu** a **Nadpis**.

![[Aktivace detailního režimu]](../media/tutorials/power-bi/word-cloud-focus-mode.png)

Kliknutím na nástroj Detailní režim v sestavě získáte lepší zobrazení Word Cloudu. Tento nástroj roztáhne Word Cloud tak, aby zaplnil celý pracovní prostor, jak je znázorněno níže.

![[Word Cloud]](../media/tutorials/power-bi/word-cloud.png)

## <a name="more-text-analytics-services"></a>Další služby Analýzy textu

Služba Analýza textu, což je jedna ze služeb Cognitive Services, které Microsoft Azure nabízí, poskytuje také analýzu mínění a rozpoznávání jazyka. Zejména rozpoznávání jazyka je užitečné v případě, že je zpětná vazba od vašich zákazníků i v jiných jazycích než angličtině.

Obě tato rozhraní API jsou velmi podobná rozhraní API pro klíčové fráze. K jejich integraci s Power BI Desktopem je tedy možné použít téměř identické vlastní funkce. Stačí vytvořit prázdný dotaz a stejně jako předtím do Rozšířeného editoru vložit odpovídající kód uvedený níže. (Nezapomeňte na svůj přístupový klíč!) Pak stejně jako předtím použijte funkci, která do tabulky přidá nový sloupec.

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

Tady jsou dvě verze funkce Rozpoznávání jazyka. První vrátí kód ISO jazyka (např. `en` pro angličtinu), zatímco druhá vrátí popisný název (např. `English`). Jak si můžete všimnout, tyto dvě verze se liší pouze v posledním řádku těla funkce.

```fsharp
// Returns the two-letter language code (e.g. en for English) of the text
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
// Returns the name (e.g. English) of the language in which the text is written
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

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o službě Analýza textu, jazyce vzorců Power Query M nebo Power BI.

> [!div class="nextstepaction"]
> [Referenční informace k rozhraní API pro analýzu textu](//westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6)

> [!div class="nextstepaction"]
> [Referenční informace k Power Query M](//msdn.microsoft.com/library/mt211003.aspx)

> [!div class="nextstepaction"]
> [Dokumentace k Power BI](//powerbi.microsoft.com/documentation/powerbi-landing-page/)
