---
title: 'Kurz: Vytvoření jazykového modelu s využitím služby Speech'
titlesuffix: Azure Cognitive Services
description: Zjistěte, jak vytvořit jazykový model s využitím služby Speech.
services: cognitive-services
author: PanosPeriorellis
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: tutorial
ms.date: 06/25/2018
ms.author: panosper
ms.openlocfilehash: b8d58450ccc5081e3be3131761d1321a32567df3
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2018
ms.locfileid: "49468989"
---
# <a name="tutorial-create-a-custom-language-model"></a>Kurz: Vytvoření vlastního jazykového modelu

V tomto dokumentu vytvoříte vlastní jazykový model. Tento vlastní jazykový model pak můžete spolu s existujícími špičkovými modely řeči od Microsoftu využít k přidání hlasové interakce do aplikace.

Tento dokument popisuje tyto postupy:
> [!div class="checklist"]
> * Příprava dat
> * Import sady jazykových dat
> * Vytvoření vlastního jazykového modelu

Pokud ještě nemáte účet služeb Cognitive Services, vytvořte si [bezplatný účet](https://azure.microsoft.com/try/cognitive-services/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Abyste měli jistotu, že váš účet služeb Cognitive Services je připojený k předplatnému, otevřete stránku s [předplatnými služeb Cognitive Services](https://customspeech.ai/Subscriptions).

Pokud se chcete připojit k předplatnému služeb Speech Services, které se vytvořilo na portálu Azure Portal, vyberte tlačítko **Připojit k existujícímu předplatnému**.

Informace o vytvoření předplatného služeb Speech Services na portálu Azure Portal najdete na stránce [Začínáme](get-started.md).

## <a name="prepare-the-data"></a>Příprava dat

Pokud chcete pro svou aplikaci vytvořit vlastní jazykový model, musíte do systému zadat seznam ukázkových promluv, třeba:

*   The patient has had urticaria for the past week. (Pacient má už týden kopřivku.)
*   The patient had a well-healed herniorrhaphy scar. (Pacient měl dobře zhojenou jizvu po operaci kýly)

Věty nemusí být celé ani gramaticky správně, ale měly by přesně odrážet mluvený vstup, se kterým se bude systém v nasazení setkávat. Tyto příklady by měly odrážet styl i obsah úloh, které budou uživatelé s vaší aplikací provádět.

Data jazykového modelu by se měla zapisovat ve formátu UTF-8 BOM. Textový soubor by měl obsahovat na každém řádku jeden příklad (větu, promluvu nebo dotaz).

Pokud chcete, aby některé termíny měly větší váhu (důležitost), můžete do svých dat přidat několik promluv, které dané termíny obsahují. 

Následující tabulka obsahuje souhrn hlavních požadavků na jazyková data.

| Vlastnost | Hodnota |
|----------|-------|
| Kódování textu | UTF-8 BOM|
| Počet promluv na řádek | 1 |
| Maximální velikost souboru | 1,5 GB |
| Poznámky | Neopakujte znaky více než čtyřikrát, například aaaaa.|
| Poznámky | Žádné speciální znaky jako \t ani žádné jiné znaky UTF-8 s vyšší hodnotou než U+00A1 v [tabulce znaků Unicode](http://www.utf8-chartable.de/)|
| Poznámky | identifikátory URI se také zamítnou, protože identifikátor URI není možné jednoznačně vyslovit|

Text se při importu normalizuje, aby ho systém mohl zpracovat. Existuje však několik důležitých normalizací, které musí provést uživatel _před_ nahráním dat. Informace o tom, jak určit vhodný jazyk, který se použije při přípravě jazykových dat, najdete v [pokynech pro přepis](prepare-transcription.md).

## <a name="language-support"></a>Podpora jazyků

Vlastní jazykové modely převodu **řeči na text** najdete v úplném seznamu [podporovaných jazyků](language-support.md#text-to-speech).



## <a name="import-the-language-data-set"></a>Import sady jazykových dat

Vyberte tlačítko **Importovat** na řádku **Sady jazykových dat** a na webu se zobrazí stránka pro nahrání nové sady dat.

Až budete chtít naimportovat svou sadu jazykových dat, přihlaste se k [portálu služeb Speech Services](https://customspeech.ai). Nejdříve vyberte rozevírací nabídku **Custom Speech** v horním pásu karet. Pak vyberte **Adaptační data**. Při prvním pokusu nahrát data do služeb Speech Services se zobrazí prázdná tabulka s názvem **Sady dat**.

Pokud chcete importovat novou sadu dat, vyberte tlačítko **Importovat** na řádku **Sady jazykových dat**. Pak web zobrazí stránku, kde se dá nahrát nová sada dat. Zadejte **Název** a **Popis**, které vám v budoucnu pomůžou tuto sadu dat identifikovat, a pak zvolte národní prostředí. 

Následně pomocí tlačítka **Vybrat soubor** vyhledejte textový soubor jazykových dat. Nakonec vyberte **Importovat** a sada dat se nahraje. V závislosti na velikosti sady dat může import trvat několik minut.

![Vyzkoušení](media/stt/speech-language-datasets-import.png)

Až se import dokončí, jazyková data budou mít položku, která bude odpovídat vaší sadě jazykových dat. Všimněte si, že má přiřazené jedinečné ID (GUID). Data mají navíc i stav, který odráží jejich aktuální stav. Během zařazování dat do fronty ke zpracování bude jejich stav **Čeká**, během ověřování bude jejich stav **Zpracování**, a až budou data připravená k použití, bude jejich stav **Dokončeno**. Při ověřování dat se provádí řada kontrol textu v souboru. Kromě toho probíhá taky určitá normalizace textu v datech.

Když je stav **Dokončeno**, můžete vybrat **Zobrazit sestavu** a zobrazit si tak sestavu ověřování jazykových dat. Zobrazí se počet promluv, které prošly a neprošly ověřováním, spolu s podrobnostmi o promluvách, které neprošly. V následujícím příkladu se dvě ukázky nepovedlo ověřit, protože obsahovaly nesprávné znaky. (V této sadě dat obsahoval první řádek dva znaky tabulátoru, druhý obsahoval několik znaků, které nejsou součástí sady tisknutelných znaků ASCII, a třetí řádek byl prázdný.)

![Vyzkoušení](media/stt/speech-language-datasets-report.png)

Když je stav sady jazykových dat **Dokončeno**, můžete ji použít k vytvoření vlastního jazykového modelu.

![Vyzkoušení](media/stt/speech-language-datasets.png)

## <a name="create-a-custom-language-model"></a>Vytvoření vlastního jazykového modelu

Jakmile budou vaše jazyková data připravená, vyberte **Jazykové modely** v rozevírací nabídce **Nabídka**, abyste zahájili proces vytváření vlastního jazykového modelu. Tato stránka obsahuje tabulku **Jazykové modely** s vašimi aktuálními vlastními jazykovými modely. Pokud jste ještě žádný vlastní jazykový model nevytvořili, tabulka bude prázdná. Aktuální národní prostředí je v tabulce uvedené u příslušné položky dat.

Před provedením jakékoli akce je nutné vybrat odpovídající národní prostředí. Aktuální národní prostředí je uvedené v názvu tabulky na všech stránkách dat, modelu a nasazení. Pokud chcete změnit národní prostředí, vyberte tlačítko **Změnit národní prostředí**, které najdete pod názvem tabulky.  Tím přejdete na stránku pro potvrzení národní prostředí. Vyberte **OK**, vrátíte se na tabulku.

Na stránce Vytvořit jazykový model zadejte **Název** a **Popis**, které vám pomůžou sledovat důležité informace o tomto modelu, například použitou sadu dat. Pak v rozevírací nabídce vyberte **Základní jazykový model**. Tento model je výchozím bodem pro vaše přizpůsobení. 

Můžete si vybrat ze dvou základních jazykových modelů. Model pro vyhledávání a diktování je vhodný pro řeč určenou přímo pro aplikaci, jako jsou příkazy, vyhledávací dotazy nebo diktování. Konverzační model je vhodný pro rozpoznávání hovorové řeči. Tento typ řeči je obvykle určený pro jinou osobu a vyskytuje se v call centrech nebo na schůzkách. 

Model pro vyhledávání a diktování je vhodný pro řeč určenou přímo pro aplikaci, jako jsou příkazy, vyhledávací dotazy nebo diktování. Konverzační model je vhodný pro rozpoznávání hovorové řeči. Tento typ řeči je obvykle určený pro jinou osobu a vyskytuje se v call centrech nebo na schůzkách. Veřejně dostupný je i nový model s názvem Universal (Univerzální). Univerzální model má za cíl poradit si se všemi scénáři a nakonec nahradit model pro vyhledávání a diktování i konverzační model.

Stejně jako v příkladu níže, až zadáte základní jazykový model, vyberte z rozevírací nabídky **Jazyková data** sadu jazykových dat, kterou chcete použít k přizpůsobení.

![Vyzkoušení](media/stt/speech-language-models-create2.png)

Obdobně jako při vytváření akustického modelu můžete volitelně vybrat, aby se po dokončení zpracování váš nový model otestoval offline. Při vyhodnoceních modelu se vyžaduje sada akustických dat.

Pokud chcete svůj jazykový model otestovat offline, zaškrtněte políčko vedle **Testování offline**. Pak v rozevírací nabídce vyberte akustický model. Pokud jste žádný vlastní akustický model nevytvořili, jedinými modely v nabídce budou základní akustické modely Microsoftu. Pokud jste jako základní model vybrali konverzační jazykový model, musíte tady použít konverzační akustický model. Pokud používáte jazykový model pro vyhledávání a diktování, musíte vybrat akustický model pro vyhledávání a diktování.

Nakonec vyberte sadu akustických dat, kterou chcete k vyhodnocení použít.

Až budete připravení zahájit zpracování, vyberte **Vytvořit**. Dále se zobrazí tabulka jazykových modelů. V tabulce bude nový záznam odpovídající tomuto modelu. Stav odráží stav modelu a může mít několik hodnot, mezi které patří **Čeká**, **Zpracování** a **Dokončeno**.

Až model dosáhne stavu **Dokončeno**, dá se nasadit do koncového bodu. Když vyberete **Zobrazit výsledek**, zobrazíte si výsledky testování offline, pokud jste ho provedli.

Pokud někdy budete chtít změnit **Název** nebo **Popis** modelu, můžete k tomu použít odkaz **Upravit** na odpovídajícím řádku tabulky jazykových modelů.

## <a name="next-steps"></a>Další kroky

- [Získání zkušebního předplatného služeb Speech Services](https://azure.microsoft.com/try/cognitive-services/)
- [Rozpoznávání řeči v C#](quickstart-csharp-dotnet-windows.md)
- [Ukázková data z Gitu](https://github.com/Microsoft/Cognitive-Custom-Speech-Service)
