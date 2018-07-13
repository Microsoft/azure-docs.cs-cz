---
title: Předpověď počasí s využitím Azure Machine Learning s daty ze služby IoT Hub | Dokumentace Microsoftu
description: Použití Azure Machine Learning k predikci pravděpodobnost deště podle služby IoT hub se shromažďuje ze senzoru teploty a vlhkosti data.
author: rangv
manager: ''
keywords: předpověď počasí strojového učení
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/11/2018
ms.author: rangv
ms.openlocfilehash: a331f8a8a69ffe41a368c1b36f1680890aaac8bf
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38666870"
---
# <a name="weather-forecast-using-the-sensor-data-from-your-iot-hub-in-azure-machine-learning"></a>Předpověď počasí s využitím dat snímačů ze služby IoT hub ve službě Azure Machine Learning

![Diagram začátku do konce](media/iot-hub-get-started-e2e-diagram/6.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

Strojové učení je technika datové vědy, které umožňuje počítačům učit se z existujících dat předpovídá budoucí chování, výsledky a trendy. Azure Machine Learning je cloudová služba pro prediktivní analýzu, která umožňuje rychle vytvářet a nasazovat prediktivní modely jako analytická řešení.

## <a name="what-you-learn"></a>Co se naučíte

Zjistíte, jak pomocí Azure Machine Learning (pravděpodobnost deště) předpovědi počasí pomocí teploty a vlhkosti dat ze služby Azure IoT hub. Pravděpodobnost deště je výstup modelu předpovědi počasí připravený. Model je postavený na základě historických dat předpovídá pravděpodobnost deště na základě teploty a vlhkosti.

## <a name="what-you-do"></a>Co můžete dělat

- Nasazení modelu předpovědi počasí jako webovou službu.
- Připravte se službu IoT hub pro přístup k datům tak, že přidáte skupinu příjemců.
- Vytvoření úlohy Stream Analytics a konfigurovat úlohy:
  - Číst teploty a vlhkosti data ze služby IoT hub.
  - Volání webové služby, chcete-li získat pravděpodobnost deště.
  - Uložte výsledek do služby Azure blob storage.
- Zobrazíte předpověď počasí pomocí Průzkumníka služby Microsoft Azure Storage.

## <a name="what-you-need"></a>Co potřebujete

- Kurz [nastavit zařízení](iot-hub-raspberry-pi-kit-node-get-started.md) dokončeno, která zahrnuje následující požadavky:
  - Aktivní předplatné Azure.
  - Azure IoT hub v rámci vašeho předplatného.
  - Klientská aplikace, která odesílá zprávy do služby Azure IoT hub.
- Účet Azure Machine Learning Studio. ([Vyzkoušejte si Machine Learning Studio zdarma](https://studio.azureml.net/)).

## <a name="deploy-the-weather-prediction-model-as-a-web-service"></a>Nasazení modelu předpovědi počasí jako webovou službu

1. Přejděte [stránce o modelu předpovědi počasí](https://gallery.cortanaintelligence.com/Experiment/Weather-prediction-model-1).
1. Klikněte na tlačítko **Open in Studio** v Microsoft Azure Machine Learning Studio.
   ![Otevřete stránku modelu předpovědi počasí v galerii Cortana Intelligence](media/iot-hub-weather-forecast-machine-learning/2_weather-prediction-model-in-cortana-intelligence-gallery.png)
1. Klikněte na tlačítko **spustit** ověření kroky v modelu. Tento krok může trvat 2 minut.
   ![Otevření modelu předpovědi počasí v Azure Machine Learning Studio](media/iot-hub-weather-forecast-machine-learning/3_open-weather-prediction-model-in-azure-machine-learning-studio.png)
1. Klikněte na tlačítko **nastavení webové služby** > **prediktivní webová služba**.
   ![Nasazení modelu předpovědi počasí v Azure Machine Learning Studio](media/iot-hub-weather-forecast-machine-learning/4-deploy-weather-prediction-model-in-azure-machine-learning-studio.png)
1. V diagramu, přetáhněte **webové služby vstup** modulu někde blízko **Score Model** modulu.
1. Připojení **webové služby vstup** modulu **určení skóre modelu** modulu.
   ![Připojit dva moduly v nástroji Azure Machine Learning Studio](media/iot-hub-weather-forecast-machine-learning/13_connect-modules-azure-machine-learning-studio.png)
1. Klikněte na tlačítko **spustit** ověření kroky v modelu.
1. Klikněte na tlačítko **NASADIT webovou službu** nasazení modelu jako webové služby.
1. Na řídicím panelu modelu, stáhněte si **Excel 2010 nebo starší sešitu** pro **žádostí a odpovědí**.

   > [!Note]
   > Ujistěte se, že stáhnete **Excel 2010 nebo starší sešitu** i v případě, že používáte novější verzi Excelu na vašem počítači.

   ![Stažení aplikace Excel pro koncový bod REQUEST RESPONSE](media/iot-hub-weather-forecast-machine-learning/5_download-endpoint-app-excel-for-request-response.png)

1. Otevřete Excelový sešit, poznamenejte si, **adresa URL webové služby** a **přístupový klíč**.

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Vytvořte, nakonfigurujte a spusťte úlohu Stream Analytics

### <a name="create-a-stream-analytics-job"></a>Vytvoření úlohy Stream Analytics

1. Na webu [Azure Portal](https://portal.azure.com/) klikněte na **Vytvořit prostředek** > **Internet věcí** > **Úloha Stream Analytics**.
1. Zadejte o úloze následující informace.

   **Název úlohy:** Název, který chcete úloze dát. Název musí být globálně jedinečný.

   **Skupina prostředků**: použijte stejnou skupinu prostředků, která používá službu IoT hub.

   **Umístění**: určit stejné umístění jako skupina prostředků.

   **Připnout na řídicí panel:** Zaškrtněte tuto možnost pro snadný přístup k centru IoT z řídicího panelu.

   ![Vytvoření úlohy Stream Analytics v Azure](media/iot-hub-weather-forecast-machine-learning/7_create-stream-analytics-job-azure.png)

1. Klikněte na možnost **Vytvořit**.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Přidání vstupu úlohy Stream Analytics

1. Otevřete úlohu Stream Analytics.
1. V části **Topologie úlohy** klikněte na **Vstupy**.
1. V **vstupy** podokně klikněte na tlačítko **přidat**a potom zadejte následující informace:

   **Vstupní alias**: jedinečný alias pro vstup.

   **Zdroj**: vyberte **služby IoT hub**.

   **Skupina uživatelů**: Vyberte skupinu uživatelů, kterou jste vytvořili.

   ![Přidat vstup do úlohy Stream Analytics v Azure](media/iot-hub-weather-forecast-machine-learning/8_add-input-stream-analytics-job-azure.png)

1. Klikněte na možnost **Vytvořit**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Přidání vstupu úlohy Stream Analytics

1. V části **Topologie úlohy** klikněte na **Výstupy**.
1. V **výstupy** podokně klikněte na tlačítko **přidat**a potom zadejte následující informace:

   **Alias pro výstup:** Jedinečný alias pro výstup.

   **Jímka**: vyberte **úložiště objektů Blob**.

   **Účet úložiště**: účet úložiště pro úložiště objektů blob. Můžete vytvořit účet úložiště nebo použijte již existující.

   **Kontejner**: kontejner, ve kterém je uložený objekt blob. Můžete vytvořit kontejner nebo použijte již existující.

   **Formát serializace události**: vyberte **sdíleného svazku clusteru**.

   ![Přidat výstup do úlohy Stream Analytics v Azure](media/iot-hub-weather-forecast-machine-learning/9_add-output-stream-analytics-job-azure.png)

1. Klikněte na možnost **Vytvořit**.

### <a name="add-a-function-to-the-stream-analytics-job-to-call-the-web-service-you-deployed"></a>Přidání funkce do úlohy Stream Analytics k volání webové služby, které jste nasadili

1. V části **topologie úlohy**, klikněte na tlačítko **funkce** > **přidat**.
1. Zadejte následující informace:

   **Alias funkce**: Zadejte `machinelearning`.

   **Typ funkce**: vyberte **Azure ML**.

   **Možnost importu**: vyberte **Import z jiného předplatného**.

   **Adresa URL**: Zadejte adresu URL webové služby, který jste si poznamenali z Excelového sešitu.

   **Klíč**: Zadejte přístupový klíč, který jste si poznamenali z Excelového sešitu.

   ![Přidání funkce do úlohy Stream Analytics v Azure](media/iot-hub-weather-forecast-machine-learning/10_add-function-stream-analytics-job-azure.png)

1. Klikněte na možnost **Vytvořit**.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Konfigurace dotazu pro úlohu Stream Analytics

1. V části **Topologie úlohy** klikněte na **Dotaz**.
1. Nahraďte stávající kód následujícím kódem:

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

V úloze Stream Analytics klikněte na **Spustit** > **Nyní** > **Spustit**. Jakmile se úloha úspěšně spustí, stav úlohy se změní ze **Zastaveno** na **Spuštěno**.

![Spuštění úlohy Stream Analytics](media/iot-hub-weather-forecast-machine-learning/11_run-stream-analytics-job-azure.png)

## <a name="use-microsoft-azure-storage-explorer-to-view-the-weather-forecast"></a>Pomocí Průzkumníka služby Microsoft Azure Storage zobrazíte předpovědí počasí

Spusťte klientská aplikace mohla zahájit shromažďování a odesílání teploty a vlhkosti dat do služby IoT hub. Pro každou zprávu, která bude přijímat vaše Centrum IoT úlohy Stream Analytics volat webovou službu předpovědi počasí pro vytvoření pravděpodobnost deště. Výsledek se pak uloží do úložiště objektů blob v Azure. Průzkumník služby Azure Storage je nástroj, který můžete použít k zobrazení výsledku.

1. [Stáhněte a nainstalujte Microsoft Azure Storage Explorer](http://storageexplorer.com/).
1. Otevřete Průzkumníka služby Azure Storage.
1. Přihlaste se ke svému účtu Azure.
1. Vyberte své předplatné.
1. Kliknutím na své předplatné > **účty úložiště** > váš účet úložiště > **kontejnery objektů Blob** > vašeho kontejneru.
1. Otevřete soubor CSV k zobrazení výsledku. Poslední sloupec zaznamenává pravděpodobnost deště.

   ![Získat výsledek předpověď počasí s Azure Machine Learning](media/iot-hub-weather-forecast-machine-learning/12_get-weather-forecast-result-azure-machine-learning.png)

## <a name="summary"></a>Souhrn

Azure Machine Learning jste úspěšně použili k vytvoření pravděpodobnost deště založené na datech teploty a vlhkosti, která bude přijímat vaše Centrum IoT.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]