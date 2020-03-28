---
title: 'Kurz: Ukázka vizuálních výstrah IoT'
titleSuffix: Azure Cognitive Services
description: V tomto kurzu použijete vlastní vizi se zařízením IoT k rozpoznání a nahlášení vizuálních stavů z videokanálu kamery.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: tutorial
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: 9f3802ada79ee87d1a04634f7caac3b1b4286dce
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74978028"
---
# <a name="tutorial-use-custom-vision-with-an-iot-device-to-report-visual-states"></a>Kurz: Použití vlastního vidění se zařízením IoT k nahlášení vizuálních stavů

Tato ukázková aplikace ukazuje, jak pomocí vlastního vidění trénovat zařízení s kamerou ke zjišťování vizuálních stavů. Tento scénář zjišťování můžete spustit na zařízení IoT pomocí modelu ONNX exportovaného ze služby Custom Vision.

Vizuální stav popisuje obsah obrázku: prázdnou místnost nebo místnost s lidmi, prázdnou příjezdovou cestu nebo příjezdovou cestu s nákladním vozidlem a tak dále. Na obrázku níže můžete vidět aplikaci, která detekuje, kdy je před kamerou umístěn banán nebo jablko.

![Animace uživatelského rozhraní označující ovoce před kamerou](./media/iot-visual-alerts-tutorial/scoring.gif)

V tomto kurzu se dozvíte, jak:
> [!div class="checklist"]
> * Nakonfigurujte ukázkovou aplikaci tak, aby používala vlastní prostředky služby Vision a IoT Hub.
> * Pomocí aplikace můžete trénovat svůj vlastní projekt Vize.
> * Pomocí aplikace můžete získávat nové obrázky v reálném čase a odesílat výsledky do Azure.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/) než začnete. 

## <a name="prerequisites"></a>Požadavky

* [!INCLUDE [create-resources](includes/create-resources.md)]
    > [!IMPORTANT]
    > Tento projekt musí být **projekt klasifikace kompaktních** obrázků, protože model budeme později exportovat do ONNX.
* Budete také muset [vytvořit prostředek služby IoT Hub v](https://ms.portal.azure.com/#create/Microsoft.IotHub) Azure.
* [Visual Studio 2015 nebo novější](https://www.visualstudio.com/downloads/)
* Volitelně zařízení IoT se systémem Windows 10 IoT Core verze 17763 nebo vyšší. Aplikaci můžete také spustit přímo z počítače.
   * Pro Raspberry Pi 2 a 3 můžete nastavit Windows 10 přímo z aplikace IoT Dashboard. U jiných zařízení, jako je DrangonBoard, budete muset blikat pomocí [metody eMMC](https://docs.microsoft.com/windows/iot-core/tutorials/quickstarter/devicesetup#flashing-with-emmc-for-dragonboard-410c-other-qualcomm-devices). Pokud potřebujete pomoc s nastavením nového zařízení, [přečtěte si](https://docs.microsoft.com/windows/iot-core/tutorials/quickstarter/devicesetup) informace o nastavení zařízení v dokumentaci k Windows IoT.

## <a name="about-the-visual-alerts-app"></a>O aplikaci Vizuální upozornění

Aplikace IoT Visual Alerts běží v souvislé smyčce a podle potřeby přepíná mezi čtyřmi různými stavy:

* **Žádný model**: Stav bez operace. Aplikace bude nepřetržitě spát po dobu jedné sekundy a zkontrolujte fotoaparát.
* **Zachycení trénovacích obrázků**: V tomto stavu aplikace zachytí obrázek a nahraje jej jako tréninkový obrázek do cílového projektu Custom Vision. Aplikace pak přepne po dobu 500 ms a opakuje operaci, dokud není zachycen nastavený cílový počet snímků. Pak spustí školení modelu Custom Vision.
* **Čekání na trénovaný model**: V tomto stavu aplikace volá rozhraní API vlastní vize každou sekundu ke kontrole, zda cílový projekt obsahuje trénované iteraci. Když najde jeden, stáhne odpovídající model ONNX do místního souboru a přepne do stavu **Bodování.**
* **V**tomto stavu aplikace používá Windows ML k vyhodnocení jednoho snímku z kamery proti místnímu modelu ONNX. Výsledná klasifikace obrázků se zobrazí na obrazovce a odešle se jako zpráva do služby IoT Hub. Aplikace pak spí po dobu jedné sekundy před bodování nový obrázek.

## <a name="understand-the-code-structure"></a>Porozumět struktuře kódu

Následující soubory zpracovávají hlavní funkce aplikace.

| File | Popis |
|-------------|-------------|
| [Soubor MainPage.xaml](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/MainPage.xaml) | Tento soubor definuje uživatelské rozhraní XAML. Hostuje ovládání webové kamery a obsahuje štítky používané pro aktualizace stavu.|
| [MainPage.xaml.cs](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/MainPage.xaml.cs) | Tento kód řídí chování uživatelského rozhraní XAML. Obsahuje kód zpracování stavového počítače.|
| [CustomVision\CustomVisionServiceWrapper.cs](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/CustomVision/CustomVisionServiceWrapper.cs) | Tato třída je obálka, která zpracovává integraci se službou Custom Vision Service.|
| [CustomVision\CustomVisionONNXModel.cs](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/CustomVision/CustomVisionONNXModel.cs) | Tato třída je obálka, která zpracovává integraci s Windows ML pro načítání modelu ONNX a bodování bitové kopie proti němu.|
| [IoTHub\IotHubWrapper.cs](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/IoTHub/IotHubWrapper.cs) | Tato třída je obálka, která zpracovává integraci s IoT Hub pro nahrávání výsledků vyhodnocování do Azure.|

## <a name="set-up-the-visual-alerts-app"></a>Nastavení aplikace Vizuální upozornění

Podle těchto kroků můžete na počítači nebo zařízení IoT spustit aplikaci IoT Visual Alerts.

1. Klonujte nebo stáhněte [ukázku IoTVisualAlerts](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/tree/master/IoTVisualAlerts) na GitHubu.
1. Otevření řešení _IoTVisualAlerts.sln_ v sadě Visual Studio
1. Integrujte svůj vlastní projekt Vision:
    1. Ve skriptu _CustomVision\CustomVisionServiceWrapper.cs_ `ApiKey` aktualizujte proměnnou pomocí trénovacího klíče.
    1. Pak aktualizujte `Endpoint` proměnnou s adresou URL koncového bodu přidruženou k vašemu klíči.
    1. Aktualizujte `targetCVSProjectGuid` proměnnou s odpovídajícím ID projektu vlastní vize, který chcete použít. 
1. Nastavte prostředek centra IoT Hub:
    1. Ve skriptu _IoTHub\IotHubWrapper.cs_ `s_connectionString` aktualizujte proměnnou správným připojovacím řetězcem pro vaše zařízení. 
    1. Na webu Azure Portal načtěte instanci služby IoT Hub, klikněte na **zařízení IoT** v části **Průzkumníky**, vyberte na cílovém zařízení (nebo ho v případě potřeby vytvořte) a najděte připojovací řetězec v části **Primární připojovací řetězec**. Řetězec bude obsahovat název centra IoT Hub, ID zařízení a sdílený přístupový klíč. má následující formát: `{your iot hub name}.azure-devices.net;DeviceId={your device id};SharedAccessKey={your access key}`.

## <a name="run-the-app"></a>Spuštění aplikace

Pokud aplikaci používáte na počítači, vyberte **místní počítač** pro cílové zařízení v Sadě Visual Studio a pro cílovou platformu vyberte **x64** nebo **x86.** Potom spusťte program stisknutím klávesy F5. Aplikace by měla spustit a zobrazit živý přenos z fotoaparátu a stavovou zprávu.

Pokud nasazujete do zařízení IoT s procesorem ARM, budete muset vybrat **ARM** jako cílovou platformu a **vzdálený počítač** jako cílové zařízení. Po zobrazení výzvy zadejte IP adresu zařízení (musí být ve stejné síti jako váš počítač). Ip adresu můžete získat z výchozí aplikace Windows IoT po spuštění zařízení a připojení k síti. Stisknutím klávesy F5 spusťte program.

Při prvním spuštění aplikace nebude mít žádné znalosti o vizuálních stavech. Zobrazí se stavová zpráva, že není k dispozici žádný model. 

## <a name="capture-training-images"></a>Zachycení tréninkových obrázků

Chcete-li nastavit model, musíte aplikaci umístit do stavu **Zachycení tréninkových obrázků.** Udělejte jeden z následujících kroků:
* Pokud aplikaci používáte na počítači, použijte tlačítko v pravém horním rohu uhlavního nastavení.
* Pokud aplikaci spouštěte na zařízení IoT, zavolejte metodu `EnterLearningMode` na zařízení prostřednictvím ioT hubu. Můžete volat prostřednictvím položky zařízení v nabídce Služby IoT Hub na webu Azure Portal nebo pomocí nástroje, jako je [IoT Hub Device Explorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer).
 
Když aplikace vstoupí do stavu **Pořizování tréninkových obrázků,** zachytí přibližně dva obrázky každou sekundu, dokud nedosáhne cílového počtu obrázků. Ve výchozím nastavení se jedná o 30 bitových kopií, ale tento `EnterLearningMode` parametr můžete nastavit předáním požadovaného čísla jako argumentu metodě IoT Hub. 

Zatímco aplikace zachytává obrázky, musíte vystavit fotoaparát typům vizuálních stavů, které chcete detekovat (například prázdná místnost, místnost s lidmi, prázdný stůl, stůl s vozíkem s nácážkami a tak dále).

## <a name="train-the-custom-vision-model"></a>Trénování modelu Custom Vision

Jakmile aplikace dokončí pořizování obrázků, nahraje je a pak přepne do stavu **Čekání na trénovaný model.** V tomto okamžiku musíte přejít na [portál Vlastní vize](https://www.customvision.ai/) a vytvořit model založený na nových trénovacích irecích. Následující animace ukazuje příklad tohoto procesu.

![Animace: označování více obrázků banánů](./media/iot-visual-alerts-tutorial/labeling.gif)

Postup tohoto postupu s vlastním scénářem:

1. Přihlaste se k [portálu Vlastní vize](http://customvision.ai).
1. Najděte svůj cílový projekt, který by teď měl mít všechny tréninkové obrázky, které aplikace nahrála.
1. Pro každý vizuální stav, který chcete identifikovat, vyberte příslušné obrázky a ručně aplikujte značku.
    * Pokud je například vaším cílem rozlišovat mezi prázdnou místností a místností s lidmi v ní, doporučujeme označit pět nebo více obrázků lidmi jako novou třídu, **Lidé**a označit pět nebo více obrázků bez lidí jako značku **Negativní.** To pomůže modelu rozlišovat mezi dvěma stavy.
    * Jako další příklad, pokud je vaším cílem aproximovat, jak plná je police, můžete použít značky jako **EmptyShelf**, **PartiallyFullShelf**a **FullShelf**.
1. Až budete hotovi, vyberte tlačítko **Vlak.**
1. Po dokončení školení aplikace zjistí, že je k dispozici trénovaná iterace. Spustí proces exportu trénovaného modelu do ONNX a jeho stažení do zařízení.

## <a name="use-the-trained-model"></a>Použití vytrénovaného modelu

Jakmile aplikace stáhne trénovaný model, přepne se do stavu **Bodování** a začne bodovat obrázky z fotoaparátu v nepřetržité smyčce.

U každého pořízeného snímku aplikace zobrazí horní značku na obrazovce. Pokud nerozpozná vizuální stav, zobrazí se **bez shod**. Aplikace také odesílá tyto zprávy do služby IoT Hub a pokud je zjištěna třída, bude zpráva `detectedClassAlert`obsahovat popisek, skóre spolehlivosti a vlastnost s názvem , kterou mohou klienti služby IoT Hub, kteří mají zájem o rychlé směrování zpráv na základě vlastností.

Kromě toho ukázka používá [knihovnu Sense HAT](https://github.com/emmellsoft/RPi.SenseHat) k detekci, kdy běží na Raspberry Pi s jednotkou Sense HAT, takže ji může použít jako výstupní displej nastavením všech světel zobrazení na červenou, kdykoli detekuje třídu a prázdnou, když nic nedetekuje.

## <a name="reuse-the-app"></a>Opětovné použití aplikace

Pokud chcete obnovit aplikaci zpět do původního stavu, můžete tak učinit kliknutím na tlačítko v pravém horním rohu ui nebo `DeleteCurrentModel` vyvoláním metody prostřednictvím služby IoT Hub.

V libovolném okamžiku můžete opakovat krok nahrávání tréninkových obrázků kliknutím na pravé `EnterLearningMode` horní tlačítko ui nebo opětovným voláním metody.

Pokud aplikaci spouštěte na zařízení a potřebujete znovu načíst IP adresu (navázání vzdáleného připojení prostřednictvím [vzdáleného klienta Windows IoT](https://www.microsoft.com/p/windows-iot-remote-client/9nblggh5mnxz#activetab=pivot:overviewtab), například), můžete metodu `GetIpAddress` volat prostřednictvím služby IoT Hub.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud projekt vlastní vize již nechcete udržovat, odstraňte jej. Na [webu Custom Vision](https://customvision.ai)přejděte na **Projekty** a vyberte koš pod novým projektem.

![Snímek obrazovky panelu s popiskem Můj nový projekt s ikonou koše](./media/csharp-tutorial/delete_project.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu nastavíte a spotřebujete aplikaci, která detekuje informace o vizuálním stavu na zařízení IoT a odešle výsledky do služby IoT Hub. Dále prozkoumejte zdrojový kód nebo proveďte jednu z níže uvedených navrhovaných úprav.

> [!div class="nextstepaction"]
> [Ukázka IoTVisualAlerts (GitHub)](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/tree/master/IoTVisualAlerts)

* Přidejte metodu IoT Hub pro přepnutí aplikace přímo do stavu **Čekání na trénovaný model.** Tímto způsobem můžete trénovat model s obrázky, které nejsou zachyceny samotným zařízením a potom posunout nový model do zařízení na příkaz.
* Postupujte podle kurzu [vizualizace dat senzorů v reálném čase](https://docs.microsoft.com/azure/iot-hub/iot-hub-live-data-visualization-in-power-bi) a vytvořte řídicí panel Power BI pro vizualizaci výstrah ioT hubu odeslaných ukázkou.
* Postupujte podle kurzu [vzdáleného monitorování IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps) a vytvořte aplikaci logiky, která reaguje na výstrahy služby IoT Hub při zjištění vizuálních stavů.
