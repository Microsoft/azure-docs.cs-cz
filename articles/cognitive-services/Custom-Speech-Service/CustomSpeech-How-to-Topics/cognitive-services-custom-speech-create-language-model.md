---
title: 'Kurz: Vytvoření jazykového modelu – Custom Speech Service'
titlesuffix: Azure Cognitive Services
description: V tomto kurzu se dozvíte, jak vytvořit jazykový model pomocí služby Custom Speech Service.
services: cognitive-services
author: PanosPeriorellis
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: tutorial
ms.date: 05/03/2017
ms.author: panosper
ROBOTS: NOINDEX
ms.openlocfilehash: b7216688efb6a2124748cd08111d57b122085143
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2018
ms.locfileid: "47219309"
---
# <a name="tutorial-create-a-custom-language-model"></a>Kurz: Vytvoření vlastního jazykového modelu

V tomto kurzu vytvoříte vlastní jazykový model pro textové dotazy nebo promluvy, které očekáváte, že uživatelé řeknou nebo zadají do aplikace. Tento vlastní jazykový model pak můžete spolu s existujícími špičkovými modely řeči od Microsoftu využít k přidání hlasové interakce do aplikace.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Příprava dat
> * Import sady jazykových dat
> * Vytvoření vlastního jazykového modelu

Pokud ještě nemáte účet služeb Cognitive Services, vytvořte si [bezplatný účet](https://cris.ai) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Ujistěte se, že je váš účet služeb Cognitive Services připojený k předplatnému, a to otevřením stránky s [předplatnými služeb Cognitive Services](https://cris.ai/Subscriptions).

Pokud se nezobrazí žádná předplatná, můžete nechat služby Cognitive Services vytvořit účet za vás kliknutím na tlačítko **Get free subscription** (Získat bezplatné předplatné). Nebo můžete kliknout na tlačítko **Connect existing subscription** (Připojit stávající předplatné) a připojit se k předplatnému služby Custom Search Service vytvořenému na webu Azure Portal.

Informace o vytvoření předplatného služby Custom Search Service na webu Azure Portal najdete v tématu [Vytvoření účtu rozhraní API služeb Cognitive Services na webu Azure Portal](../../cognitive-services-apis-create-account.md).

## <a name="prepare-the-data"></a>Příprava dat

Abyste pro svou aplikaci mohli vytvořit vlastní jazykový model, musíte pro systém zadat seznam ukázkových promluv, například:

*   He has had urticaria for the past week. (Už týden má kopřivku)
*   The patient had a well-healed herniorrhaphy scar. (Pacient měl dobře zhojenou jizvu po operaci kýly)

Věty nemusí být celé ani gramaticky správně. Měly by přesně odrážet mluvený vstup, se kterým očekáváte, že se systém v nasazení bude setkávat. Tyto příklady by měly odrážet styl i obsah úloh, které budou uživatelé s vaší aplikací provádět.

Data jazykového modelu by se měla zapisovat do souboru ve formátu prostého textu v kódování US-ASCII nebo UTF-8 v závislosti na národním prostředí. Národní prostředí en-US podporuje obě kódování. Národní prostředí zh-CN podporuje pouze UTF-8 (značka pořadí bajtů je volitelná). Textový soubor by měl obsahovat na každém řádku jeden příklad (větu, promluvu nebo dotaz).

Pokud chcete, aby některé věty měly větší váhu (důležitost), můžete je do svých dat přidat několikrát. Vhodný počet opakování je 10 až 100. Pokud opakování normalizujete na 100, můžete snadno určit relativní míru důležitosti věty vzhledem k této hodnotě.

Následující tabulka obsahuje souhrn hlavních požadavků na jazyková data.

| Vlastnost | Hodnota |
|----------|-------|
| Kódování textu | en-US: US-ACSII nebo UTF-8; zh-CN: UTF-8|
| počet promluv na řádek | 1 |
| Maximální velikost souboru | 200 MB |
| Poznámky | neopakujte znaky více než 4krát, například „aaaaa“|
| Poznámky | žádné speciální znaky jako \t ani žádné jiné znaky UTF-8 s vyšší hodnotou než U+00A1 v [tabulce znaků Unicode](http://www.utf8-chartable.de/)|
| Poznámky | identifikátory URI se také zamítnou, protože identifikátor URI není možné jednoznačně vyslovit|

Text se při importu normalizuje, aby ho systém mohl zpracovat. Existuje však několik důležitých normalizací, které musí provést uživatel _před_ nahráním dat. Informace o určení vhodného jazyka při přípravě jazykových dat najdete v [pokynech pro přepis](cognitive-services-custom-speech-transcription-guidelines.md).

## <a name="import-the-language-data-set"></a>Import sady jazykových dat

Klikněte na tlačítko Import (Importovat) na řádku Acoustic Datasets (Sady akustických dat) a na webu se zobrazí stránka pro nahrání nové sady dat.

Jakmile budete připraveni importovat svou sadu jazykových dat, přihlaste se k [portálu služby Custom Speech Service](https://cris.ai).  Pak klikněte na rozevírací nabídku Custom Speech na horním pásu karet a vyberte Adaptation Data (Adaptační data). Pokud do služby Custom Speech Service nahráváte data poprvé, zobrazí se prázdná tabulka Datasets (Datové sady).

Pokud chcete importovat novou sadu dat, klikněte na tlačítko Import (Importovat) na řádku Language Datasets (Sady jazykových dat) a na webu se zobrazí stránka pro nahrání nové sady dat. Zadejte název a popis, které vám v budoucnu pomůžou sadu dat identifikovat. Pak pomocí tlačítka Choose File (Vybrat soubor) vyhledejte textový soubor jazykových dat. Pak klikněte na Import (Importovat) a sada dat se nahraje. V závislosti na velikosti sady dat to může trvat několik minut.

![vyzkoušení](../../../media/cognitive-services/custom-speech-service/custom-speech-language-datasets-import.png)

Po dokončení importu se vrátíte k tabulce jazykových dat, ve které se zobrazí položka odpovídající vaší sadě jazykových dat. Všimněte si, že má přiřazené jedinečné ID (GUID). U dat bude uvedený také stav, který odráží jejich aktuální stav. Během zařazování dat do fronty ke zpracování bude jejich stav Waiting (Čekající), během ověřování bude jejich stav Processing (Zpracování) a až budou data připravená k použití, bude jejich stav Complete (Dokončeno). Při ověřování dat se provádí série kontrol textu v souboru a určitá normalizace textu dat.

Když je stav Complete (Dokončeno), můžete kliknutím na View Report (Zobrazit sestavu) zobrazit sestavu ověřování jazykových dat. Zobrazí se počet promluv, které prošly a neprošly ověřováním, spolu s podrobnostmi o promluvách, které neprošly. V následujícím příkladu se kvůli nesprávným znakům nezdařilo ověření dvou promluv (v této sadě dat obsahovala první promluva dvě emotikony a druhá obsahovala několik znaků mimo tisknutelnou znakovou sadu ASCII).

![vyzkoušení](../../../media/cognitive-services/custom-speech-service/custom-speech-language-datasets-report.png)

Když je stav sady jazykových dat Complete (Dokončeno), můžete ji použít k vytvoření vlastního jazykového modelu.

![vyzkoušení](../../../media/cognitive-services/custom-speech-service/custom-speech-language-datasets.png)

## <a name="create-a-custom-language-model"></a>Vytvoření vlastního jazykového modelu

Jakmile budou vaše data připravená, kliknutím na Language Models (Jazykové modely) v rozevírací nabídce Menu (Nabídka) zahajte proces vytváření vlastního jazykového modelu. Tato stránka obsahuje tabulku Language Models (Jazykové modely) s vašimi aktuálními vlastními jazykovými modely. Pokud jste ještě žádný vlastní jazykový model nevytvořili, tabulka bude prázdná. Aktuální národní prostředí se odráží v názvu tabulky. Pokud chcete vytvořit jazykový model pro jiný jazyk, klikněte na Change Locale (Změnit národní prostředí). Další informace o podporovaných jazycích najdete v části věnované [změně národního prostředí](cognitive-services-custom-speech-change-locale.md). Pokud chcete vytvořit nový model, klikněte na odkaz Create New (Vytvořit nový) pod názvem tabulky.

Na stránce Create Language Model (Vytvořit jazykový model) zadejte název a popis, které vám pomůžou sledovat důležité informace o tomto modelu, například použitou sadu dat. Pak v rozevírací nabídce vyberte Base Language Model (Základní jazykový model). Tento model bude výchozím bodem pro vaše přizpůsobování. Můžete si vybrat ze dvou základních jazykových modelů. _Jazykový model Microsoftu pro vyhledávání a diktování_ je vhodný pro řeč určenou přímo pro aplikaci, jako jsou příkazy, vyhledávací dotazy nebo diktování. _Konverzační jazykový model Microsoftu_ je vhodný pro rozpoznávání hovorové řeči. Tento typ řeči je obvykle určený pro jinou osobu a vyskytuje se v call centrech nebo na schůzkách.

Po určení základního jazykového modelu pomocí rozevírací nabídky Language Data (Jazyková data) vyberte sadu jazykových dat, kterou chcete použít k přizpůsobení.

![vyzkoušení](../../../media/cognitive-services/custom-speech-service/custom-speech-language-models-create2.png)

Stejně jako při vytváření akustického modelu můžete volitelně vybrat, aby se po dokončení zpracování provedlo testování vašeho nového modelu offline. Vzhledem k tomu, že se jedná o vyhodnocení výkonu převodu řeči na text, vyžaduje testování offline sadu akustických dat.

Pokud chcete provést testování vašeho jazykového modelu offline, zaškrtněte políčko vedle textu Offline Testing (Testování offline). Pak v rozevírací nabídce vyberte akustický model. Pokud jste žádný vlastní akustický model nevytvořili, jedinými modely v nabídce budou základní akustické modely Microsoftu. V případě, že jste jako základní model vybrali konverzační jazykový model, musíte tady použít konverzační akustický model. V případě, že používáte jazykový model pro vyhledávání a diktování, musíte vybrat akustický model pro vyhledávání a diktování.

Nakonec vyberte sadu akustických dat, kterou chcete k vyhodnocení použít.

Jakmile budete připraveni zahájit zpracování stiskněte Create (Vytvořit). Tím se vrátíte k tabulce jazykových modelů. V tabulce bude nový záznam odpovídající tomuto modelu. Stav odráží stav modelu a může mít několik hodnot, například Waiting (Čekající), Processing (Zpracování) a Complete (Dokončeno).

Když se model dostane do stavu Complete (Dokončeno), můžete ho nasadit do koncového bodu. Kliknutím na View Result (Zobrazit výsledek) zobrazíte výsledky testování offline, pokud se provedlo.

Pokud někdy budete chtít změnit název nebo popis modelu, můžete k tomu použít odkaz Edit (Upravit) na odpovídajícím řádku tabulky jazykových modelů.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili vlastní jazykový model pro použití s textem. Pokud chcete vytvořit vlastní akustický model pro použití se zvukovými soubory a přepisy, pokračujte ke kurzu vytvoření akustického modelu.

> [!div class="nextstepaction"]
> [Vytvoření vlastního akustického modelu](cognitive-services-custom-speech-create-acoustic-model.md)
