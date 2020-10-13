---
title: 'Kurz: Ukázka vizuálních výstrah IoT'
titleSuffix: Azure Cognitive Services
description: V tomto kurzu použijete Custom Vision se zařízením IoT k rozpoznávání a sestavování vizuálních stavů z kanálu videa kamery.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: tutorial
ms.date: 08/05/2020
ms.author: pafarley
ms.openlocfilehash: ebc6ca630ea3cabb519805ae8505abf336a2a9ea
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90604287"
---
# <a name="tutorial-use-custom-vision-with-an-iot-device-to-report-visual-states"></a>Kurz: použití Custom Vision se zařízením IoT k hlášení vizuálních stavů

Tato ukázková aplikace ukazuje, jak použít Custom Vision k tomu, abyste mohli pomocí kamery naučit zařízení detekovat vizuální stavy. Tento scénář zjišťování můžete spustit na zařízení IoT pomocí exportovaného modelu ONNX.

Vizuální stav popisuje obsah obrázku: prázdná místnost nebo místnost s lidmi, prázdná příjezdky nebo vjezd s nákladním vozíkem atd. Na následujícím obrázku vidíte, že aplikace se detekuje, když se před fotoaparátem nachází banán nebo Apple.

![Animace označování uživatelského rozhraní pro plody před fotoaparátem](./media/iot-visual-alerts-tutorial/scoring.gif)

V tomto kurzu se dozvíte, jak:
> [!div class="checklist"]
> * Nakonfigurujte ukázkovou aplikaci tak, aby používala vlastní Custom Vision a prostředky IoT Hub.
> * Použijte aplikaci ke školení Custom Visionho projektu.
> * Použijte aplikaci k vyhodnocení nových imagí v reálném čase a odeslání výsledků do Azure.

Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/cognitive-services). 

## <a name="prerequisites"></a>Předpoklady

* [!INCLUDE [create-resources](includes/create-resources.md)]
    > [!IMPORTANT]
    > Tento projekt musí být projekt klasifikace **kompaktních** imagí, protože model budeme exportovat do ONNX později.
* Budete také muset [vytvořit prostředek IoT Hub](https://ms.portal.azure.com/#create/Microsoft.IotHub) v Azure.
* [Visual Studio 2015 nebo novější](https://www.visualstudio.com/downloads/)
* Volitelně je zařízení IoT s Windows 10 IoT Core verze 17763 nebo vyšší. Aplikaci můžete také spustit přímo z počítače.
   * Pro maliny pi 2 a 3 můžete nastavit Windows 10 přímo z aplikace řídicího panelu IoT. Pro jiná zařízení, jako je například DrangonBoard, je třeba ji Flash použít [metodou EMMC](https://docs.microsoft.com/windows/iot-core/tutorials/quickstarter/devicesetup#flashing-with-emmc-for-dragonboard-410c-other-qualcomm-devices). Pokud potřebujete pomáhat s nastavením nového zařízení, přečtěte si téma [nastavení zařízení](https://docs.microsoft.com/windows/iot-core/tutorials/quickstarter/devicesetup) v dokumentaci k Windows IoT.

## <a name="about-the-visual-alerts-app"></a>O aplikaci vizuálních výstrah

Aplikace pro vizuální výstrahy IoT se spouští v souvislé smyčce, přičemž podle potřeby přepíná mezi čtyřmi různými stavy:

* **Žádný model**: stav no-op. Aplikace se nepřetržitě dokončí po dobu jedné sekundy a zkontroluje kameru.
* **Zachytávání školicích imagí**: v tomto stavu aplikace zachytí obrázek a nahraje ho jako školicí obrázek do cílového Custom Vision projektu. Aplikace se pak přejde do režimu spánku po 500 ms a operace se opakuje, dokud se nezachytí cílový počet imagí. Potom aktivuje školení modelu Custom Vision.
* **Čekání na vyškolený model**: v tomto stavu aplikace volá rozhraní API Custom Vision každou sekundu, aby zkontrolovala, jestli cílový projekt obsahuje proškolenou iteraci. Když ho najde, stáhne odpovídající model ONNX do místního souboru a přepne do stavu **bodování** .
* **Bodování**: v tomto stavu aplikace používá Windows ml k vyhodnocení jednoho snímku z fotoaparátu proti místnímu ONNX modelu. Výsledná klasifikace obrázku se zobrazí na obrazovce a odešle se jako zpráva do IoT Hub. Aplikace se pak před vyhodnocením nové image do režimu spánku za jednu sekundu.

## <a name="examine-the-code-structure"></a>Kontrola struktury kódu

Následující soubory zpracovávají hlavní funkce aplikace.

| Soubor | Popis |
|-------------|-------------|
| [MainPage. XAML](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/MainPage.xaml) | Tento soubor definuje uživatelské rozhraní XAML. Je hostitelem ovládacího prvku webové kamery a obsahuje popisky používané pro aktualizace stavu.|
| [MainPage.xaml.cs](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/MainPage.xaml.cs) | Tento kód řídí chování uživatelského rozhraní XAML. Obsahuje kód pro zpracování stavového stroje.|
| [CustomVision\CustomVisionServiceWrapper.cs](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/CustomVision/CustomVisionServiceWrapper.cs) | Tato třída je obálkou, která zpracovává integraci s Custom Vision Service.|
| [CustomVision\CustomVisionONNXModel.cs](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/CustomVision/CustomVisionONNXModel.cs) | Tato třída je obálkou, která zpracovává integraci s Windows ML pro načtení modelu ONNX a vyhodnocování imagí.|
| [IoTHub\IotHubWrapper.cs](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/IoTHub/IoTHubWrapper.cs) | Tato třída je obálkou, která zpracovává integraci s IoT Hub pro nahrávání výsledků bodování do Azure.|

## <a name="set-up-the-visual-alerts-app"></a>Nastavení aplikace vizuálních výstrah

Pomocí těchto kroků můžete aplikaci IoT vizuální výstrahy spuštěné na vašem počítači nebo zařízení IoT zobrazit.

1. Naklonujte nebo Stáhněte si [ukázku IoTVisualAlerts](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/tree/master/IoTVisualAlerts) na GitHubu.
1. Otevřete řešení _IoTVisualAlerts. sln_ v aplikaci Visual Studio
1. Integrujte svůj Custom Vision projekt:
    1. V _CustomVision\CustomVisionServiceWrapper.cs_ skriptu aktualizujte `ApiKey` proměnnou pomocí školicího klíče.
    1. Pak proměnnou aktualizujte `Endpoint` pomocí adresy URL koncového bodu přidruženého k vašemu klíči.
    1. Aktualizujte `targetCVSProjectGuid` proměnnou odpovídajícím ID Custom Vision projektu, který chcete použít. 
1. Nastavte prostředek IoT Hub:
    1. V _IoTHub\IotHubWrapper.cs_ skriptu aktualizujte `s_connectionString` proměnnou pomocí správného připojovacího řetězce pro vaše zařízení. 
    1. Na Azure Portal načtěte instanci IoT Hub, v části **Průzkumník**klikněte na **zařízení IoT** , v nabídce Nástroje vyberte cílové zařízení (nebo v případě potřeby vytvořte) a v části **primární připojovací řetězec**Najděte připojovací řetězec. Řetězec bude obsahovat váš název IoT Hub, ID zařízení a sdílený přístupový klíč. má následující formát: `{your iot hub name}.azure-devices.net;DeviceId={your device id};SharedAccessKey={your access key}` .

## <a name="run-the-app"></a>Spuštění aplikace

Pokud aplikaci spouštíte na počítači, vyberte v aplikaci Visual Studio **místní počítač** pro cílové zařízení a jako cílovou platformu vyberte **x64** nebo **x86** . Pak stiskněte klávesu F5 ke spuštění programu. Aplikace by měla začít a zobrazovat živý kanál z kamery a stavové zprávy.

Pokud nasazujete do zařízení IoT s procesorem ARM, budete muset jako cílové zařízení vybrat **ARM** jako cílovou platformu a **vzdálený počítač** . Po zobrazení výzvy zadejte IP adresu zařízení (musí se nacházet ve stejné síti jako váš počítač). Po spuštění zařízení a jeho připojení k síti můžete získat IP adresu z výchozí aplikace Windows IoT. Stisknutím klávesy F5 program spusťte.

Při prvním spuštění aplikace nebude mít žádné znalosti o vizuálních stavech. Zobrazí se stavová zpráva, že žádný model není k dispozici. 

## <a name="capture-training-images"></a>Zachytit školicí snímky

Chcete-li nastavit model, je nutné aplikaci umístit do stavu **zachycení školicích imagí** . Proveďte jeden z následujících kroků:
* Pokud používáte aplikaci na počítači PC, použijte tlačítko v pravém horním rohu uživatelského rozhraní.
* Pokud spouštíte aplikaci na zařízení IoT, zavolejte `EnterLearningMode` metodu na zařízení prostřednictvím IoT Hub. Můžete ji volat pomocí položky zařízení v nabídce IoT Hub v Azure Portal nebo pomocí nástroje, jako je například [Device Explorer IoT Hub](https://github.com/Azure/azure-iot-sdk-csharp).
 
Když aplikace vstoupí do stavu **zachycení školicích imagí** , zachytí o dvou obrázcích každou sekundu, dokud nedosáhne cílového počtu imagí. Ve výchozím nastavení je cílem 30 imagí, ale tento parametr můžete nastavit předáním požadovaného čísla jako argumentu `EnterLearningMode` metodě IoT Hub. 

I když aplikace zachytí image, je nutné ji zveřejnit pro typy vizuálních stavů, které chcete detekovat (například prázdná místnost, místnost s lidmi, prázdná stůl, stůl s automobilem a tak dále).

## <a name="train-the-custom-vision-model"></a>Výuka Custom Visionho modelu

Jakmile aplikace dokončí zachycení imagí, nahraje je a pak přepne na stav **čekání na vyškolený model** . V tuto chvíli potřebujete přejít na [web Custom Vision](https://www.customvision.ai/) a vytvořit model založený na nových školicích obrázcích. Příklad tohoto procesu znázorňuje následující animace.

![Animace: označení více obrázků banánů](./media/iot-visual-alerts-tutorial/labeling.gif)

Postup opakování tohoto procesu ve vlastním scénáři:

1. Přihlaste se k [webu Custom Vision](http://customvision.ai).
1. Vyhledejte cílový projekt, který by teď měl mít všechny školicí materiály, které nahrála aplikace.
1. U každého vizuálního stavu, který chcete identifikovat, vyberte příslušné bitové kopie a ručně použijte značku.
    * Například pokud váš cíl rozlišuje mezi prázdnou místností a místností s lidmi, doporučujeme označit pět nebo více obrázků s lidmi jako novou třídu, **lidi**a označením pěti nebo více obrázků, aniž by bylo nutné používat jako **zápornou** značku. To vám pomůže model odlišit mezi oběma stavy.
    * Dalším příkladem je, že pokud máte v cíli přibližný způsob, jakým je plná police, můžete použít značky jako **EmptyShelf**, **PartiallyFullShelf**a **FullShelf**.
1. Až budete hotovi, vyberte tlačítko **výuka** .
1. Po dokončení školení aplikace zjistí, že je k dispozici vyškolená iterace. Spustí proces exportu trained model, který ONNX a stáhne do zařízení.

## <a name="use-the-trained-model"></a>Použití vytrénovaného modelu

Jakmile aplikace stáhne model trained, přepne do stavu **bodování** a spustí snímky z kamery v souvislé smyčce.

U každého zaznamenaného obrázku aplikace zobrazí horní značku na obrazovce. Pokud nerozpozná vizuální stav, nezobrazí se **žádné shody**. Aplikace také pošle tyto zprávy do IoT Hub a pokud je zjištěna třída, bude zpráva zahrnovat popisek, skóre spolehlivosti a vlastnost s názvem `detectedClassAlert` , kterou mohou používat IoT Hub klienti, kteří mají zájem o rychlé směrování zpráv na základě vlastností.

Kromě toho je v ukázce k detekci, kdy je spuštěná na Malině PI s smyslovou jednotkou HAT, použitou [knihovnu HAT Hat](https://github.com/emmellsoft/RPi.SenseHat) , takže ji můžete použít jako zobrazení výstupu nastavením všech indikátorů zobrazení na červenou, kdykoli detekuje třídu a prázdné, když nedetekuje cokoli.

## <a name="reuse-the-app"></a>Opětovné použití aplikace

Pokud byste chtěli obnovit aplikaci zpátky do původního stavu, můžete tak učinit kliknutím na tlačítko v pravém horním rohu uživatelského rozhraní nebo vyvoláním metody `DeleteCurrentModel` prostřednictvím IoT Hub.

V libovolném okamžiku můžete opakovat krok nahrávání školicích imagí kliknutím na tlačítko pravého horního tlačítka uživatelského rozhraní nebo volání `EnterLearningMode` metody znovu.

Pokud aplikaci spouštíte na zařízení a potřebujete znovu načíst IP adresu (například k navázání vzdáleného připojení prostřednictvím [vzdáleného klienta Windows IoT](https://www.microsoft.com/p/windows-iot-remote-client/9nblggh5mnxz#activetab=pivot:overviewtab)), můžete `GetIpAddress` metodu volat prostřednictvím IoT Hub.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už projekt Custom Vision nechcete udržovat, odstraňte ho. Na [webu Custom Vision](https://customvision.ai)přejděte na **projekty** a v rámci nového projektu vyberte odpadkový koš.

![Snímek obrazovky s panelem označeným nový projekt s ikonou odpadkového koše](./media/csharp-tutorial/delete_project.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu nastavíte a spustíte aplikaci, která detekuje informace o vizuálním stavu na zařízení IoT a pošle výsledky do IoT Hub. Dále si podrobněji prozkoumejte zdrojový kód nebo proveďte jednu z navrhovaných úprav níže.

> [!div class="nextstepaction"]
> [IoTVisualAlerts Sample (GitHub)](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/tree/master/IoTVisualAlerts)

* Přidejte metodu IoT Hub pro přepínání aplikace přímo na stav čekání na **vyškolený model** . Tímto způsobem můžete model proškolit s imagemi, které nejsou zachyceny samotným zařízením, a potom vložit nový model do zařízení v příkazu.
* Postupujte podle kurzu [vizualizace dat snímače v reálném](https://docs.microsoft.com/azure/iot-hub/iot-hub-live-data-visualization-in-power-bi) čase a vytvořte řídicí panel Power BI, který vizualizuje IoT Hub výstrahy odesílané ukázkou.
* Postupujte podle kurzu [vzdáleného monitorování IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps) a vytvořte aplikaci logiky, která reaguje na IoT Hub výstrahy při zjištění vizuálních stavů.
