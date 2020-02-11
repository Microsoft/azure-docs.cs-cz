---
title: Předpověď počasí s využitím Azure Machine Learning s daty IoT Hub
description: Použijte Azure Machine Learning pro předpověď pravděpodobnosti deště na základě dat o teplotě a vlhkosti, které vaše centrum IoT shromažďuje ze senzoru.
author: robinsh
manager: philmea
keywords: Předpověď počasí do strojového učení
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 02/10/2020
ms.author: robinsh
ms.openlocfilehash: b71b86c14c55c312ef420a4d8517140fdded4072
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2020
ms.locfileid: "77122162"
---
# <a name="weather-forecast-using-the-sensor-data-from-your-iot-hub-in-azure-machine-learning"></a>Předpověď počasí pomocí dat ze senzorů ze služby IoT Hub v Azure Machine Learning

![Komplexní diagram](media/iot-hub-get-started-e2e-diagram/6.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

Machine Learning je technika datové vědy, která pomáhá počítačům učit se z existujících dat a předpovídat budoucí chování, výsledky a trendy. Azure Machine Learning je cloudová služba pro prediktivní analýzu, která umožňuje rychle vytvářet a nasazovat prediktivní modely jako analytická řešení.

## <a name="what-you-learn"></a>Co se naučíte

Naučíte se, jak pomocí Azure Machine Learning provádět předpověď počasí s využitím dat o teplotě a vlhkosti ve službě Azure IoT Hub. Šance na deště je výstupem připraveného modelu předpovědi počasí. Model je vytvořen na základě historických dat, aby se mohla odhadnout pravděpodobnost srážky na základě teploty a vlhkosti.

## <a name="what-you-do"></a>Co dělat

- Nasaďte model předpovědi počasí jako webovou službu.
- Přidejte skupinu příjemců a připravte si tak přístup k datům do služby IoT Hub.
- Vytvořte úlohu Stream Analytics a nakonfigurujte úlohu na:
  - Načte data o teplotě a vlhkosti ze služby IoT Hub.
  - Zavolejte webovou službu, abyste získali možnost deště.
  - Výsledek uložte do úložiště objektů BLOB v Azure.
- K zobrazení předpovědi počasí použijte Průzkumník služby Microsoft Azure Storage.

## <a name="what-you-need"></a>Co potřebujete

- Dokončete kurz [online simulátoru malin](iot-hub-raspberry-pi-web-simulator-get-started.md) . nebo v některém z kurzů zařízení; například [Malina Pi s Node. js](iot-hub-raspberry-pi-kit-node-get-started.md). Tyto požadavky se týkají následujících požadavků:
  - Aktivní předplatné Azure.
  - Azure IoT Hub v rámci vašeho předplatného.
  - Klientská aplikace, která odesílá zprávy do služby Azure IoT Hub.
- Účet [Azure Machine Learning Studio (Classic)](https://studio.azureml.net/) .

## <a name="deploy-the-weather-prediction-model-as-a-web-service"></a>Nasazení modelu předpovědi počasí jako webové služby

V této části získáte model předpovědi počasí z knihovny Azure AI Library. Pak přidáte modul R-Script do modelu pro vyčištění dat o teplotě a vlhkosti. Nakonec model nasadíte jako prediktivní webovou službu.

### <a name="get-the-weather-prediction-model"></a>Získání modelu předpovědi počasí

V této části získáte model předpovědi počasí z Azure AI Gallery a otevřete ho v Azure Machine Learning Studio (Classic).

1. Přejít na [stránku modelu předpovědi počasí](https://gallery.cortanaintelligence.com/Experiment/Weather-prediction-model-1).

   ![Otevřete stránku model předpovědi počasí v Azure AI Gallery](media/iot-hub-weather-forecast-machine-learning/weather-prediction-model-in-azure-ai-gallery.png)

1. Kliknutím na **otevřít v Studio (Classic)** otevřete model v Microsoft Azure Machine Learning Studio (Classic).

   ![Otevření modelu předpovědi počasí v Azure Machine Learning Studio (Classic)](media/iot-hub-weather-forecast-machine-learning/open-ml-studio.png)

### <a name="add-an-r-script-module-to-clean-temperature-and-humidity-data"></a>Přidejte modul R-Script pro čistou teplotu a data vlhkosti.

Aby se model choval správně, musí být data o teplotě a vlhkosti převoditelná na číselná data. V této části přidáte modul R-Script do modelu předpovědi počasí, který odebere všechny řádky, které mají hodnoty dat pro teplotu nebo vlhkost, které nelze převést na číselné hodnoty.

1. Na levé straně okna Azure Machine Learning Studio klikněte na šipku a rozbalte panel nástroje. Do vyhledávacího pole zadejte "Execute". Vyberte modul **spuštění skriptu jazyka R** .

   ![Vyberte možnost spustit modul skriptu jazyka R.](media/iot-hub-weather-forecast-machine-learning/select-r-script-module.png)

1. Přetáhněte modul **spuštění skriptu jazyka r** poblíž modulu **Vyčištění chybějících dat** a stávajícího modulu **skriptu pro spuštění r** v diagramu. Odstraňte připojení mezi **čistými chybějícími daty** a moduly **spuštění skriptu R** a pak připojte vstupy a výstupy nového modulu, jak je znázorněno na obrázku.

   ![Přidat modul spuštění skriptu R](media/iot-hub-weather-forecast-machine-learning/add-r-script-module.png)

1. Výběrem nového modulu **skriptu jazyka R** otevřete jeho okno vlastností. Zkopírujte a vložte následující kód do pole **skriptu jazyka R** .

   ```r
   # Map 1-based optional input ports to variables
   data <- maml.mapInputPort(1) # class: data.frame

   data$temperature <- as.numeric(as.character(data$temperature))
   data$humidity <- as.numeric(as.character(data$humidity))

   completedata <- data[complete.cases(data), ]

   maml.mapOutputPort('completedata')

   ```

   Až budete hotovi, okno Vlastnosti by mělo vypadat podobně jako v následujícím příkladu:

   ![Přidat kód pro spuštění modulu R Script](media/iot-hub-weather-forecast-machine-learning/add-code-to-module.png)

### <a name="deploy-predictive-web-service"></a>Nasazení prediktivní webové služby

V této části ověříte model, nastavíte prediktivní webovou službu založenou na modelu a pak nasadíte webovou službu.

1. Kliknutím na tlačítko **Spustit** ověříte kroky v modelu. Dokončení tohoto kroku může trvat několik minut.

   ![Spusťte experiment a ověřte postup.](media/iot-hub-weather-forecast-machine-learning/run-experiment.png)

1. Klikněte na **nastavit webovou službu** > **prediktivní webovou službu**. Otevře se diagram prediktivního experimentu.

   ![Nasazení modelu předpovědi počasí v Azure Machine Learning Studio (Classic)](media/iot-hub-weather-forecast-machine-learning/predictive-experiment.png)

1. V diagramu prediktivního experimentu odstraňte spojení mezi **vstupním modulem webové služby** a **datovou sadou počasí** v horní části. Pak přetáhněte **Vstupní modul webové služby** někam do blízkosti modulu určení **skóre modelu** a připojte ho, jak je znázorněno níže:

   ![Propojení dvou modulů v Azure Machine Learning Studio (klasické)](media/iot-hub-weather-forecast-machine-learning/13_connect-modules-azure-machine-learning-studio.png)

1. Kliknutím na tlačítko **Spustit** ověříte kroky v modelu.

1. Kliknutím na **nasadit webovou službu** nasaďte model jako webovou službu.

1. Na řídicím panelu modelu stáhněte **sešit aplikace Excel 2010 nebo starší** pro **požadavek nebo odpověď**.

   > [!Note]
   > Je nutné stáhnout **sešit aplikace Excel 2010 nebo starší** , i když v počítači používáte novější verzi aplikace Excel.

   ![Stažení Excelu pro koncový bod odpovědi na žádost](media/iot-hub-weather-forecast-machine-learning/download-workbook.png)

1. Otevřete excelový sešit, poznamenejte si **adresu URL webové služby** a **přístupový klíč**.

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Vytvoření, konfigurace a spuštění úlohy Stream Analytics

### <a name="create-a-stream-analytics-job"></a>Vytvoření úlohy Stream Analytics

1. Na webu [Azure Portal](https://portal.azure.com/) klikněte na **Vytvořit prostředek** > **Internet věcí** > **Úloha Stream Analytics**.
1. Zadejte o úloze následující informace.

   **Název úlohy:** Název, který chcete úloze dát. Název musí být globálně jedinečný.

   **Skupina prostředků**: použijte stejnou skupinu prostředků, kterou používá IoT Hub.

   **Umístění**: použijte stejné umístění jako vaše skupina prostředků.

   **Připnout na řídicí panel:** Zaškrtněte tuto možnost pro snadný přístup k centru IoT z řídicího panelu.

   ![Vytvoření úlohy Stream Analytics v Azure](media/iot-hub-weather-forecast-machine-learning/7_create-stream-analytics-job-azure.png)

1. Klikněte na možnost **Vytvořit**.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Přidání vstupu úlohy Stream Analytics

1. Otevřete Stream Analytics úlohu.
1. V části **Topologie úlohy** klikněte na **Vstupy**.
1. V podokně **vstupy** klikněte na **Přidat**a zadejte následující informace:

   **Vstupní alias**: jedinečný alias pro vstup.

   **Zdroj**: vyberte **Centrum IoT**.

   **Skupina příjemců**: vyberte skupinu uživatelů, kterou jste vytvořili.

   ![Přidání vstupu do úlohy Stream Analytics v Azure](media/iot-hub-weather-forecast-machine-learning/8_add-input-stream-analytics-job-azure.png)

1. Klikněte na možnost **Vytvořit**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Přidání vstupu úlohy Stream Analytics

1. V části **Topologie úlohy** klikněte na **Výstupy**.
1. V podokně **výstupy** klikněte na tlačítko **Přidat**a zadejte následující informace:

   **Alias pro výstup:** Jedinečný alias pro výstup.

   **Jímka**: vyberte **BLOB Storage**.

   **Účet úložiště**: účet úložiště pro úložiště objektů BLOB. Můžete vytvořit účet úložiště nebo použít nějaký existující.

   **Kontejner**: kontejner, ve kterém je objekt BLOB uložený. Můžete vytvořit kontejner nebo použít existující.

   **Formát serializace události**: vyberte **CSV**.

   ![Přidání výstupu do úlohy Stream Analytics v Azure](media/iot-hub-weather-forecast-machine-learning/9_add-output-stream-analytics-job-azure.png)

1. Klikněte na možnost **Vytvořit**.

### <a name="add-a-function-to-the-stream-analytics-job-to-call-the-web-service-you-deployed"></a>Přidání funkce do úlohy Stream Analytics pro volání webové služby, kterou jste nasadili

1. V části **topologie úlohy**klikněte na **funkce** > **Přidat**.
1. Zadejte následující informace:

   **Alias funkce**: zadejte `machinelearning`.

   **Typ funkce**: vyberte **Azure ml**.

   **Možnost importu**: vyberte **importovat z jiného předplatného**.

   **Adresa URL**: zadejte adresu URL webové služby, kterou jste si poznamenali v excelovém sešitu.

   **Klíč**: zadejte přístupový klíč, který jste si poznamenali v excelovém sešitu.

   ![Přidání funkce do úlohy Stream Analytics v Azure](media/iot-hub-weather-forecast-machine-learning/10_add-function-stream-analytics-job-azure.png)

1. Klikněte na možnost **Vytvořit**.

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

1. Klikněte na možnost **Uložit**.

### <a name="run-the-stream-analytics-job"></a>Spuštění úlohy Stream Analytics

V úloze Stream Analytics klikněte na **Spustit** > **Nyní** > **Spustit**. Jakmile se úloha úspěšně spustí, stav úlohy se změní ze **Zastaveno** na **Spuštěno**.

![Spuštění úlohy Stream Analytics](media/iot-hub-weather-forecast-machine-learning/11_run-stream-analytics-job-azure.png)

## <a name="use-microsoft-azure-storage-explorer-to-view-the-weather-forecast"></a>Použití Průzkumník služby Microsoft Azure Storage k zobrazení předpovědi počasí

Spusťte klientskou aplikaci, která začne shromažďovat a odesílat data o teplotě a vlhkosti do služby IoT Hub. U každé zprávy, kterou služba IoT Hub přijme, Stream Analytics úloha volá webovou službu předpovědi počasí, aby vznikla možnost deště. Výsledek se pak uloží do úložiště objektů BLOB v Azure. Průzkumník služby Azure Storage je nástroj, který můžete použít k zobrazení výsledku.

1. [Stáhněte a nainstalujte Průzkumník služby Microsoft Azure Storage](https://storageexplorer.com/).
1. Otevřete Průzkumník služby Azure Storage.
1. Přihlaste se ke svému účtu Azure.
1. Vyberte své předplatné.
1. Klikněte na své předplatné > **účty úložiště** > svého účtu úložiště > **kontejnery objektů BLOB** > vašem kontejneru.
1. Pokud chcete zobrazit výsledek, Stáhněte si soubor. csv. Poslední sloupec zaznamená šanci na deště.

   ![Získat výsledek předpovědi počasí s Azure Machine Learning](media/iot-hub-weather-forecast-machine-learning/weather-forecast-result.png)

## <a name="summary"></a>Souhrn

Úspěšně jste použili Azure Machine Learning k vyprodukování pravděpodobnosti deště na základě dat o teplotě a vlhkosti, které vaše centrum IoT přijme.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]