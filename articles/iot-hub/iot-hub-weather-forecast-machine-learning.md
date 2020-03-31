---
title: Předpověď počasí pomocí Azure Machine Learning s daty ioT hubu
description: Azure Machine Learning slouží k předvídání pravděpodobnosti deště na základě údajů o teplotě a vlhkosti, které vaše centrum IoT hub shromažďuje ze senzoru.
author: robinsh
manager: philmea
keywords: předpověď počasí strojové učení
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 02/10/2020
ms.author: robinsh
ms.openlocfilehash: b71b86c14c55c312ef420a4d8517140fdded4072
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77122162"
---
# <a name="weather-forecast-using-the-sensor-data-from-your-iot-hub-in-azure-machine-learning"></a>Předpověď počasí pomocí dat senzorů z vašeho centra IoT v Azure Machine Learning

![Diagram koncového bodu](media/iot-hub-get-started-e2e-diagram/6.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

Strojové učení je technika datové vědy, která pomáhá počítačům učit se z existujících dat předpovídat budoucí chování, výsledky a trendy. Azure Machine Learning je cloudová služba pro prediktivní analýzu, která umožňuje rychle vytvářet a nasazovat prediktivní modely jako analytická řešení.

## <a name="what-you-learn"></a>Co se naučíte

Dozvíte se, jak pomocí Azure Machine Learning dělat předpověď počasí (šance na déšť) pomocí teploty a vlhkosti dat z centra Azure IoT hub. Pravděpodobnost deště je výstupem připraveného modelu předpovědi počasí. Model je postaven na historických datech, aby se předpověděla pravděpodobnost deště na základě teploty a vlhkosti.

## <a name="what-you-do"></a>Co děláte

- Nasaďte model předpovědi počasí jako webovou službu.
- Přidejte skupinu spotřebitelů, abyste si připravili centrum IoT hub pro přístup k datům.
- Vytvořte úlohu Stream Analytics a nakonfigurujte ji tak, aby:
  - Přečtěte si údaje o teplotě a vlhkosti z vašeho centra IoT hub.
  - Zavolejte na webovou službu, abyste získali šanci na déšť.
  - Uložte výsledek do úložiště objektů blob Azure.
- Pomocí Průzkumníka úložiště Microsoft Azure zobrazíte předpověď počasí.

## <a name="what-you-need"></a>Co potřebujete

- Dokončete kurz [online simulátoru Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md) nebo jeden z výukových programů zařízení; například [Raspberry Pi s node.js](iot-hub-raspberry-pi-kit-node-get-started.md). Tyto požadavky se vztahují na tyto požadavky:
  - Aktivní předplatné Azure.
  - Azure IoT hub v rámci vašeho předplatného.
  - Klientská aplikace, která odesílá zprávy do centra Azure IoT hub.
- Účet [Azure Machine Learning Studio (klasický).](https://studio.azureml.net/)

## <a name="deploy-the-weather-prediction-model-as-a-web-service"></a>Nasazení modelu předpovědi počasí jako webové služby

V této části získáte model předpovědi počasí z knihovny Azure AI. Potom přidáte do modelu modul R-script, který vyčistí data o teplotě a vlhkosti. Nakonec nasadíte model jako prediktivní webové služby.

### <a name="get-the-weather-prediction-model"></a>Získejte model předpovědi počasí

V této části získáte model předpovědi počasí z Galerie AI Azure a otevřete ho v Azure Machine Learning Studio (klasické).

1. Přejděte na [stránku modelu předpovědi počasí](https://gallery.cortanaintelligence.com/Experiment/Weather-prediction-model-1).

   ![Otevření stránky modelu předpovědi počasí v Galerii Azure AI](media/iot-hub-weather-forecast-machine-learning/weather-prediction-model-in-azure-ai-gallery.png)

1. Kliknutím na **Otevřít v aplikaci Studio (klasická)** otevřete model v Microsoft Azure Machine Learning Studio (klasické).

   ![Otevření modelu předpovědi počasí v Azure Machine Learning Studio (klasické)](media/iot-hub-weather-forecast-machine-learning/open-ml-studio.png)

### <a name="add-an-r-script-module-to-clean-temperature-and-humidity-data"></a>Přidání modulu R-script pro čištění údajů o teplotě a vlhkosti

Aby se model choval správně, musí být data o teplotě a vlhkosti převoditelná na číselná data. V této části přidáte modul skriptu R do modelu předpovědi počasí, který odebere všechny řádky, které mají hodnoty dat pro teplotu nebo vlhkost, které nelze převést na číselné hodnoty.

1. Na levé straně okna Azure Machine Learning Studio klikněte na šipku rozbalte panel nástrojů. Do vyhledávacího pole zadejte "Execute". Vyberte modul **Spustit skript R.**

   ![Vybrat modul Spustit skript R](media/iot-hub-weather-forecast-machine-learning/select-r-script-module.png)

1. Přetáhněte modul **Spustit skript R** poblíž modulu Vyčistit chybějící **data** a existujícího modulu Spustit **skript R** v diagramu. Odstraňte spojení mezi moduly **Vyčistit chybějící data** a **Moduly Skript u spuštění R** a potom připojte vstupy a výstupy nového modulu, jak je znázorněno na obrázku.

   ![Přidat modul Spustit skript R](media/iot-hub-weather-forecast-machine-learning/add-r-script-module.png)

1. Vyberte nový modul **Spustit skript R,** chcete-li otevřít jeho okno vlastností. Zkopírujte a vložte následující kód do pole **Skript R.**

   ```r
   # Map 1-based optional input ports to variables
   data <- maml.mapInputPort(1) # class: data.frame

   data$temperature <- as.numeric(as.character(data$temperature))
   data$humidity <- as.numeric(as.character(data$humidity))

   completedata <- data[complete.cases(data), ]

   maml.mapOutputPort('completedata')

   ```

   Po dokončení by okno vlastností mělo vypadat podobně jako následující:

   ![Přidání kódu do modulu Spustit skript R](media/iot-hub-weather-forecast-machine-learning/add-code-to-module.png)

### <a name="deploy-predictive-web-service"></a>Nasazení prediktivní webové služby

V této části ověříte model, nastavíte prediktivní webovou službu založenou na modelu a potom nasadíte webovou službu.

1. Klepnutím na **tlačítko Spustit** ověřte kroky v modelu. Tento krok může trvat několik minut.

   ![Spuštění experimentu k ověření kroků](media/iot-hub-weather-forecast-machine-learning/run-experiment.png)

1. Klepněte na tlačítko **NASTAVIT WEBOVOU SLUŽBU** > **Prediktivní webová služba**. Otevře se diagram prediktivního experimentu.

   ![Nasazení modelu předpovědi počasí v Azure Machine Learning Studio (klasické)](media/iot-hub-weather-forecast-machine-learning/predictive-experiment.png)

1. V diagramu prediktivního experimentu odstraňte spojení mezi vstupním modulem **webové služby** a **sadou data počasí** v horní části. Potom přetáhněte vstupní modul **webové služby** někam poblíž modulu **Model skóre** a připojte jej, jak je znázorněno na obrázku:

   ![Připojení dvou modulů v Azure Machine Learning Studio (klasické)](media/iot-hub-weather-forecast-machine-learning/13_connect-modules-azure-machine-learning-studio.png)

1. Klepnutím na tlačítko **RUN** ověřte kroky v modelu.

1. Chcete-li nasadit model jako webovou službu, klepněte na tlačítko **NASADIT WEBOVOU SLUŽBU.**

1. Na řídicí panel modelu stáhněte **excel ový sešit 2010 nebo starší** pro **požadavek/odpověď**.

   > [!Note]
   > Stažení **sešitu aplikace Excel 2010 nebo starší** ho stáhnete i v případě, že v počítači používáte novější verzi Excelu.

   ![Stažení koncového bodu odpovědi požadavku na odpověď v Excelu](media/iot-hub-weather-forecast-machine-learning/download-workbook.png)

1. Otevřete excelový sešit, poznamenejte si **adresu URL webové služby** a **klávesu ACCESS**.

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Vytvoření, konfigurace a spuštění úlohy Stream Analytics

### <a name="create-a-stream-analytics-job"></a>Vytvoření úlohy Stream Analytics

1. Na webu [Azure Portal](https://portal.azure.com/) klikněte na **Vytvořit prostředek** > **Internet věcí** > **Úloha Stream Analytics**.
1. Zadejte o úloze následující informace.

   **Název úlohy:** Název, který chcete úloze dát. Název musí být globálně jedinečný.

   **Skupina prostředků**: Použijte stejnou skupinu prostředků, kterou používá vaše centrum IoT Hub.

   **Umístění:** Použijte stejné umístění jako skupina prostředků.

   **Připnout na řídicí panel:** Zaškrtněte tuto možnost pro snadný přístup k centru IoT z řídicího panelu.

   ![Vytvoření úlohy Stream Analytics v Azure](media/iot-hub-weather-forecast-machine-learning/7_create-stream-analytics-job-azure.png)

1. Klikněte na **Vytvořit**.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Přidání vstupu úlohy Stream Analytics

1. Otevřete úlohu Stream Analytics.
1. V části **Topologie úlohy** klikněte na **Vstupy**.
1. V podokně **Vstupy** klikněte na **Přidat**a zadejte následující informace:

   **Vstupní alias**: Jedinečný alias pro vstup.

   **Zdroj**: Vyberte **centrum IoT hub**.

   **Skupina spotřebitelů**: Vyberte skupinu spotřebitelů, kterou jste vytvořili.

   ![Přidání vstupu do úlohy Stream Analytics v Azure](media/iot-hub-weather-forecast-machine-learning/8_add-input-stream-analytics-job-azure.png)

1. Klikněte na **Vytvořit**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Přidání vstupu úlohy Stream Analytics

1. V části **Topologie úlohy** klikněte na **Výstupy**.
1. V podokně **Výstupy** klikněte na **Přidat**a zadejte následující informace:

   **Alias pro výstup:** Jedinečný alias pro výstup.

   **Jímky**: Vyberte **úložiště objektů blob**.

   **Účet úložiště**: Účet úložiště pro úložiště objektů blob. Můžete si vytvořit účet úložiště nebo použít existující.

   **Kontejner**: Kontejner, do kterého se objekt blob uloží. Můžete vytvořit kontejner nebo použít existující.

   **Formát serializace událostí**: Vyberte **možnost CSV**.

   ![Přidání výstupu do úlohy Stream Analytics v Azure](media/iot-hub-weather-forecast-machine-learning/9_add-output-stream-analytics-job-azure.png)

1. Klikněte na **Vytvořit**.

### <a name="add-a-function-to-the-stream-analytics-job-to-call-the-web-service-you-deployed"></a>Přidání funkce do úlohy Stream Analytics pro volání webové služby, kterou jste nasadili

1. V části **Topologie úlohy**klikněte na **položku Funkce** > **Přidat**.
1. Zadejte následující informace:

   **Alias funkce** `machinelearning`: Enter .

   **Typ funkce**: Vyberte **Azure ML**.

   **Možnost importu:** Vyberte **Import ovat z jiného předplatného**.

   **Adresa URL**: Zadejte adresu URL webové služby, kterou jste si poznamenali ze sešitu aplikace Excel.

   **Klíč:** Zadejte přístupový klíč, který jste si poznamenali ze sešitu aplikace Excel.

   ![Přidání funkce do úlohy Stream Analytics v Azure](media/iot-hub-weather-forecast-machine-learning/10_add-function-stream-analytics-job-azure.png)

1. Klikněte na **Vytvořit**.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Konfigurace dotazu pro úlohu Stream Analytics

1. V části **Topologie úlohy** klikněte na **Dotaz**.
1. Nahraďte existující kód následujícím kódem:

   ```sql
   WITH machinelearning AS (
      SELECT EventEnqueuedUtcTime, temperature, humidity, machinelearning(temperature, humidity) as result from [YourInputAlias]
   )
   Select System.Timestamp time, CAST (result.[temperature] AS FLOAT) AS temperature, CAST (result.[humidity] AS FLOAT) AS humidity, CAST (result.[Scored Probabilities] AS FLOAT ) AS 'probabalities of rain'
   Into [YourOutputAlias]
   From machinelearning
   ```

   Nahraďte `[YourInputAlias]` názvem aliasu pro vstup úlohy.

   Nahraďte `[YourOutputAlias]` názvem aliasu pro výstup.

1. Klikněte na **Uložit**.

### <a name="run-the-stream-analytics-job"></a>Spuštění úlohy Stream Analytics

V úloze Stream Analytics klikněte na **Spustit** > **nyní** > **spustit**. Jakmile se úloha úspěšně spustí, stav úlohy se změní ze **Zastaveno** na **Spuštěno**.

![Spuštění úlohy Stream Analytics](media/iot-hub-weather-forecast-machine-learning/11_run-stream-analytics-job-azure.png)

## <a name="use-microsoft-azure-storage-explorer-to-view-the-weather-forecast"></a>Zobrazení předpovědi počasí pomocí Průzkumníka úložiště Microsoft Azure

Spusťte klientskou aplikaci a začněte shromažďovat a odesílat data o teplotě a vlhkosti do centra IoT hub. Pro každou zprávu, kterou vaše služba IoT hub obdrží, úloha Stream Analytics volá webovou službu předpověď počasí, aby vytvořila možnost deště. Výsledek se pak uloží do úložiště objektů blob Azure. Azure Storage Explorer je nástroj, který můžete použít k zobrazení výsledku.

1. [Stáhněte a nainstalujte Microsoft Azure Storage Explorer](https://storageexplorer.com/).
1. Otevřete Průzkumníka úložišť Azure.
1. Přihlaste se ke svému účtu Azure.
1. Vyberte své předplatné.
1. Klikněte na předplatné > **účty úložiště** > účet úložiště > kontejnery objektů **blob** > kontejneru.
1. Stáhněte soubor .csv a zodownloadnete výsledek. Poslední sloupec zaznamenává pravděpodobnost deště.

   ![Získejte výsledky předpovědi počasí pomocí Azure Machine Learning](media/iot-hub-weather-forecast-machine-learning/weather-forecast-result.png)

## <a name="summary"></a>Souhrn

Azure Machine Learning jste úspěšně využili k vytvoření pravděpodobnosti deště na základě dat o teplotě a vlhkosti, která vaše centrum IoT hub přijímá.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]