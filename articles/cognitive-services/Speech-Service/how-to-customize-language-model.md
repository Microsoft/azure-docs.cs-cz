---
title: Vytvoření jazykového modelu s využitím služby Speech – Microsoft Cognitive Services | Microsoft Docs
description: Přečtěte si, jak vytvořit jazykový model s využitím služby Speech ve službách Microsoft Cognitive Services.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: speech-service
ms.topic: tutorial
ms.date: 06/25/2018
ms.author: panosper
ms.openlocfilehash: 85e67be406b3d9723476821adfb09fc4db8dc1d1
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/17/2018
ms.locfileid: "39068569"
---
# <a name="tutorial-create-a-custom-language-model"></a>Kurz: Vytvoření vlastního jazykového modelu

V tomto dokumentu vytvoříte vlastní jazykový model, který pak můžete využít spolu s existujícími špičkovými modely řeči od Microsoftu k přidání hlasové interakce do aplikace.

Tento dokument popisuje tyto postupy:
> [!div class="checklist"]
> * Příprava dat
> * Import sady jazykových dat
> * Vytvoření vlastního jazykového modelu

Pokud ještě nemáte účet služeb Cognitive Services, vytvořte si [bezplatný účet](https://azure.microsoft.com/try/cognitive-services/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Ujistěte se, že je váš účet služeb Cognitive Services připojený k předplatnému, a to otevřením stránky s [předplatnými služeb Cognitive Services](https://customspeech.ai/Subscriptions).

Můžete kliknout na tlačítko **Connect existing subscription** (Připojit stávající předplatné) a připojit se k předplatnému služby Speech vytvořenému na webu Azure Portal.

Informace o vytvoření předplatného služby Speech na webu Azure Portal najdete na stránce s [úvodními informacemi](get-started.md).

## <a name="prepare-the-data"></a>Příprava dat

Abyste pro svou aplikaci mohli vytvořit vlastní jazykový model, musíte pro systém zadat seznam ukázkových promluv, například:

*   The patient has had urticaria for the past week. (Pacient má už týden kopřivku.)
*   The patient had a well-healed herniorrhaphy scar. (Pacient měl dobře zhojenou jizvu po operaci kýly)

Věty nemusí být celé ani gramaticky správně. Měly by přesně odrážet mluvený vstup, se kterým se bude systém v nasazení setkávat. Tyto příklady by měly odrážet styl i obsah úloh, které budou uživatelé s vaší aplikací provádět.

Data jazykového modelu by se měla zapisovat ve formátu UTF-8 BOM. Textový soubor by měl obsahovat na každém řádku jeden příklad (větu, promluvu nebo dotaz).

Pokud chcete, aby některé termíny měly větší váhu (důležitost), můžete do svých dat přidat několik promluv obsahujících dané termíny. 

Následující tabulka obsahuje souhrn hlavních požadavků na jazyková data.

| Vlastnost | Hodnota |
|----------|-------|
| Kódování textu | UTF-8 BOM|
| počet promluv na řádek | 1 |
| Maximální velikost souboru | 1,5 GB |
| Poznámky | Neopakujte znaky více než čtyřikrát, například „aaaaa“.|
| Poznámky | žádné speciální znaky jako \t ani žádné jiné znaky UTF-8 s vyšší hodnotou než U+00A1 v [tabulce znaků Unicode](http://www.utf8-chartable.de/)|
| Poznámky | identifikátory URI se také zamítnou, protože identifikátor URI není možné jednoznačně vyslovit|

Text se při importu normalizuje, aby ho systém mohl zpracovat. Existuje však několik důležitých normalizací, které musí provést uživatel _před_ nahráním dat. Informace o určení vhodného jazyka při přípravě jazykových dat najdete v [pokynech pro přepis](prepare-transcription.md).

## <a name="language-support"></a>Podpora jazyků

Pro vlastní jazykové modely **převodu řeči na text** se podporují následující jazyky.

Kliknutím zobrazíte úplný seznam [podporovaných jazyků](supported-languages.md).

## <a name="import-the-language-data-set"></a>Import sady jazykových dat

Klikněte na tlačítko Import (Importovat) na řádku Language Datasets (Sady jazykových dat) a na webu se zobrazí stránka pro nahrání nové sady dat.

Až budete chtít naimportovat svou sadu jazykových dat, přihlaste se k [portálu služby Speech](https://customspeech.ai).  Pak klikněte na rozevírací nabídku Custom Speech na horním pásu karet a vyberte Adaptation Data (Adaptační data). Při prvním nahrávání dat do služby Speech se zobrazí prázdná tabulka Datasets (Datové sady).

Pokud chcete importovat novou sadu dat, klikněte na tlačítko Import (Importovat) na řádku Language Datasets (Sady jazykových dat) a na webu se zobrazí stránka pro nahrání nové sady dat. Zadejte název a popis, které vám v budoucnu pomůžou tuto sadu dat identifikovat, a zvolte národní prostředí. Pak pomocí tlačítka Choose File (Vybrat soubor) vyhledejte textový soubor jazykových dat. Pak klikněte na Import (Importovat) a sada dat se nahraje. V závislosti na velikosti sady dat může import trvat několik minut.

![vyzkoušení](media/stt/speech-language-datasets-import.png)

Po dokončení importu se vrátíte k tabulce jazykových dat, ve které se zobrazí položka odpovídající vaší sadě jazykových dat. Všimněte si, že má přiřazené jedinečné ID (GUID). U dat bude uvedený také stav, který odráží jejich aktuální stav. Během zařazování dat do fronty ke zpracování bude jejich stav Waiting (Čekající), během ověřování bude jejich stav Processing (Zpracování) a až budou data připravená k použití, bude jejich stav Complete (Dokončeno). Při ověřování dat se provádí série kontrol textu v souboru a určitá normalizace textu dat.

Když je stav Complete (Dokončeno), můžete kliknutím na View Report (Zobrazit sestavu) zobrazit sestavu ověřování jazykových dat. Zobrazí se počet promluv, které prošly a neprošly ověřováním, spolu s podrobnostmi o promluvách, které neprošly. V následujícím příkladu se kvůli nesprávným znakům nezdařilo ověření dvou ukázek (v této sadě dat obsahoval první řádek dva znaky tabulátoru, druhý řádek obsahoval několik znaků mimo tisknutelnou znakovou sadu ASCII a třetí řádek byl prázdný).

![vyzkoušení](media/stt/speech-language-datasets-report.png)

Když je stav sady jazykových dat Complete (Dokončeno), můžete ji použít k vytvoření vlastního jazykového modelu.

![vyzkoušení](media/stt/speech-language-datasets.png)

## <a name="create-a-custom-language-model"></a>Vytvoření vlastního jazykového modelu

Jakmile budou vaše data připravená, kliknutím na Language Models (Jazykové modely) v rozevírací nabídce Menu (Nabídka) zahajte proces vytváření vlastního jazykového modelu. Tato stránka obsahuje tabulku Language Models (Jazykové modely) s vašimi aktuálními vlastními jazykovými modely. Pokud jste ještě žádný vlastní jazykový model nevytvořili, tabulka bude prázdná. Aktuální národní prostředí je v tabulce uvedené u příslušné položky dat.

Před provedením jakékoli akce je nutné vybrat odpovídající národní prostředí. Aktuální národní prostředí je uvedené v názvu tabulky na všech stránkách dat, modelu a nasazení. Pokud chcete národní prostředí změnit, klikněte pod názvem tabulky na tlačítko Change Locale (Změnit národní prostředí), které vás přesměruje na potvrzovací stránku pro národní prostředí. Kliknutím na OK se vraťte do tabulky.

Na stránce Create Language Model (Vytvořit jazykový model) zadejte název a popis, které vám pomůžou sledovat důležité informace o tomto modelu, například použitou sadu dat. Pak v rozevírací nabídce vyberte Base Language Model (Základní jazykový model). Tento model bude výchozím bodem pro vaše přizpůsobování. Můžete si vybrat ze dvou základních jazykových modelů. Model pro vyhledávání a diktování je vhodný pro řeč určenou přímo pro aplikaci, jako jsou příkazy, vyhledávací dotazy nebo diktování. Konverzační model je vhodný pro rozpoznávání hovorové řeči. Tento typ řeči je obvykle určený pro jinou osobu a vyskytuje se v call centrech nebo na schůzkách. Veřejně dostupný je také nový model s názvem Universal (Univerzální). Univerzální model má za cíl poradit si se všemi scénáři a nakonec nahradit model pro vyhledávání a diktování i konverzační model.

5.  V příkladu níže po určení základního jazykového modelu vyberte pomocí rozevírací nabídky Language Data (Jazyková data) sadu jazykových dat, kterou chcete použít k přizpůsobení.

![vyzkoušení](media/stt/speech-language-models-create2.png)

Stejně jako při vytváření akustického modelu můžete volitelně vybrat, aby se po dokončení zpracování provedlo testování vašeho nového modelu offline. Při vyhodnoceních modelu se vyžaduje sada akustických dat.

Pokud chcete provést testování vašeho jazykového modelu offline, zaškrtněte políčko vedle textu Offline Testing (Testování offline). Pak v rozevírací nabídce vyberte akustický model. Pokud jste žádný vlastní akustický model nevytvořili, jedinými modely v nabídce budou základní akustické modely Microsoftu. V případě, že jste jako základní model vybrali konverzační jazykový model, musíte tady použít konverzační akustický model. V případě, že používáte jazykový model pro vyhledávání a diktování, musíte vybrat akustický model pro vyhledávání a diktování.

Nakonec vyberte sadu akustických dat, kterou chcete k vyhodnocení použít.

Až budete chtít zahájit zpracování, stiskněte Create (Vytvořit), abyste přešli k tabulce jazykových modelů. V tabulce bude nový záznam odpovídající tomuto modelu. Stav odráží stav modelu a může mít několik hodnot, například Waiting (Čekající), Processing (Zpracování) a Complete (Dokončeno).

Když se model dostane do stavu Complete (Dokončeno), můžete ho nasadit do koncového bodu. Kliknutím na View Result (Zobrazit výsledek) zobrazíte výsledky testování offline, pokud se provedlo.

Pokud někdy budete chtít změnit název nebo popis modelu, můžete k tomu použít odkaz Edit (Upravit) na odpovídajícím řádku tabulky jazykových modelů.

## <a name="next-steps"></a>Další kroky

- [Získání zkušebního předplatného služby Speech](https://azure.microsoft.com/try/cognitive-services/)
- [Rozpoznávání řeči v C#](quickstart-csharp-dotnet-windows.md)
- [Ukázková data Git](https://github.com/Microsoft/Cognitive-Custom-Speech-Service)
