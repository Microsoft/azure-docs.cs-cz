---
title: Integrovat řešení vzdáleného monitorování s Azure Data Lake Store | Microsoft Docs
description: Zjistěte, jak integrovat řešení vzdáleného monitorování s Azure Data Lake Store pomocí úlohu služby Azure Stream Analytics.
+services: ''
+suite: iot-suite
+author: philmea
+manager: timlt
+ms.author: philmea
+ms.date: 04/029/2018
+ms.topic: article
+ms.service: iot-suite
ms.openlocfilehash: 426ded8079ba5d6de4c186a8d18dd284082b0c1c
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="integrate-the-remote-monitoring-solution-with-azure-data-lake-store"></a>Integrovat řešení vzdáleného monitorování s Azure Data Lake Store

Může mít pokročilé analýzy vyžadovaného co je k dispozici v řešení vzdáleného monitorování. Azure Data Lake Store je ideální pro tuto aplikaci, protože ho lze ukládat data z masivního a různých datových sad a také integraci s Azure Data Lake Analytics k poskytování analytické informace na vyžádání.

V tento postup použijete úlohu služby Azure Stream Analytics k vysílání dat ze služby IoT hub ve vašem řešení vzdáleného monitorování k Azure Data Lake Store.

## <a name="prerequisites"></a>Požadavky

Abyste mohli dokončit tento postup, budete potřebovat následující:

* [Nasadit předkonfigurované řešení vzdáleného monitorování](iot-accelerators-remote-monitoring-deploy.md).
  * Řešení vzdáleného monitorování nasadí IoT hub a úlohy Azure Stream Analytics používá v tomto článku do vašeho předplatného Azure.
* [Nasazení Azure Data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md)
  * Data Lake Store, musí být nasazené do stejné oblasti jako vaše řešení vzdáleného monitorování.
  * [Vytvořte složku](../data-lake-store/data-lake-store-get-started-portal.md#createfolder) s názvem "streamování" ve vašem účtu.

## <a name="create-a-consumer-group"></a>Vytvořte skupinu uživatelů

Vytvořte vyhrazenou skupinu spotřebitelů ve službě IoT hub vašeho řešení vzdáleného monitorování. Bude se používat v úloze Stream Analytics pro streamování dat do Data Lake Store.

> [!NOTE]
> Skupiny uživatelů slouží aplikace k načítání dat z Azure IoT Hub. Pro každých pět Výstup příjemci měli vytvořit nové skupiny příjemců. Můžete vytvořit až 32 skupiny příjemců.

1. Přihlaste se k portálu Azure.

1. Na portálu Azure klikněte **cloudové prostředí** tlačítko.

    ![Ikona spuštění portálu](./media/iot-accelerators-integrate-data-lake/portal-launch-icon.png)

1. Spusťte tento příkaz k vytvoření nové skupiny příjemců:

```azurecli-interactive
az iot hub consumer-group create --hub-name contoso-rm30263 --name streamanalyticsjob --resource-group contoso-rm
```

> [!NOTE]
> Použijte skupinu prostředků a názvy Centrum IoT z řešení vzdáleného monitorování.

## <a name="create-stream-analytics-job"></a>Vytvoření úlohy Stream Analytics

Umožňuje vytvořte úlohu služby Azure Stream Analytics do ní může Streamovat data ze služby IoT hub do Azure Data Lake store.

1. Klikněte na tlačítko **vytvořit prostředek**vyberte Internet věcí z Marketplace a klikněte na tlačítko **úlohy služby Stream Analytics**.

    ![Nové úlohy Stream Analytics](./media/iot-accelerators-integrate-data-lake/new-stream-analytics-job.png)

1. Zadejte název úlohy a vyberte příslušné předplatné a prostředků skupiny.

1. Vyberte umístění, do nejbližší nebo ve stejné oblasti jako vaše Data Lake Store. Tady používáme východní USA.

1. Je třeba ponechat hostitelské prostředí jako výchozí **cloudu**.

1. Klikněte na možnost **Vytvořit**.

    ![Vytvoření úlohy Stream Analytics](./media/iot-accelerators-integrate-data-lake/create-stream-analytics-job.png)

## <a name="configure-the-stream-analytics-job"></a>Nakonfigurujte úlohu služby Stream Analytics

1. Přejděte na **úlohy služby Stream Analytics** ve vaší skupině prostředků řešení vzdáleného monitorování.

1. Na stránce Přehled klikněte na **vstupy**.

    ![Stránka s přehledem](./media/iot-accelerators-integrate-data-lake/stream-analytics-overview.png)

1. Klikněte na tlačítko **přidat vstup datového proudu** a vyberte **IoT Hub** z rozevíracího seznamu.

    ![Přidat vstup](./media/iot-accelerators-integrate-data-lake/stream-analytics-add-input.png)

1. Na kartě nový vstupní zadejte alias vstupu z **IoTHub**.

1. Příjemce skupiny rozevíracího seznamu vyberte skupinu příjemců, které jste vytvořili dříve. Tady používáme **streamanalyticsjob**.

    ![Vyberte vstup](./media/iot-accelerators-integrate-data-lake/stream-analytics-new-input.png)

1. Klikněte na **Uložit**.

1. Na stránce Přehled klikněte na **výstupy**.

    ![Přidat Data Lake Store](./media/iot-accelerators-integrate-data-lake/stream-analytics-overview-2.png)

1. Klikněte na tlačítko **přidat** a vyberte **Data Lake Store** z rozevíracího seznamu.

    ![Přidat výstup](./media/iot-accelerators-integrate-data-lake/stream-analytics-output.png)

1. Na kartě nový výstupní zadejte alias pro výstup z **DataLakeStore**.

1. Vyberte účet Data Lake Store, kterou jste vytvořili v předchozích krocích a poskytují strukturu složek pro datový proud dat do úložiště.

1. V poli Datum formátu zadejte **/streaming/ {date} / {time}**. Ponechte výchozí formát rrrr/MM/DD datum a čas formátu HH.

    ![Zadejte struktura složek](./media/iot-accelerators-integrate-data-lake/stream-analytics-new-output.png)

1. Klikněte na tlačítko **Autorizovat**.

    Budete mít k autorizaci s Data Lake Store umožňuje přístup pro zápis úlohy Stream analytics k systému souborů.

    ![Autorizace služby Stream Analytics do Data Lake Store](./media/iot-accelerators-integrate-data-lake/stream-analytics-out-authorize.png)

    Zobrazí se místní okno a po zavření automaticky otevřeném okně tlačítko Autorizovat bude šedé, po dokončení autorizace.

    > [!NOTE]
    > Pokud se zobrazí v automaticky otevíraném okně chybu, otevřete nové okno prohlížeče v režimu Incognito a zkuste to znovu.

1. Klikněte na **Uložit**.

## <a name="edit-the-stream-analytics-query"></a>Upravit dotaz služby Stream Analytics

Azure Stream Analytics používá k určení vstupního zdroje, které datové proudy dat, transformovat data jako požadovaný a výstupní na celou řadu úložiště nebo zpracování cíle dotazovací jazyk podobné jazyku SQL.

1. Na kartě Přehled klikněte na **upravit dotaz**.

    ![Upravit dotaz](./media/iot-accelerators-integrate-data-lake/stream-analytics-edit-query.png)

1. V editoru dotazů, nahraďte [YourOutputAlias] a zástupné symboly [YourInputAlias] s hodnotami, které jste definovali dříve.

    ```sql
    SELECT
        *, System.Timestamp as time
    INTO
        DataLakeStore
    FROM
        IoTHub
    ```

    ![Stream Analytics dotazu](./media/iot-accelerators-integrate-data-lake/stream-analytics-query.png)

1. Klikněte na **Uložit**.
1. Klikněte na tlačítko **Ano** pro přijetí změn.

## <a name="start-the-stream-analytics-job"></a>Spustit úlohu služby Stream Analytics

1. Na kartě Přehled klikněte na **spustit**.

    ![Spuštění úlohy Stream Analytics](./media/iot-accelerators-integrate-data-lake/stream-analytics-start.png)

1. Na kartě úlohy Start klikněte na **vlastní**.

1. Nastavit vlastní čas se vrátíte k zachycení dat ze zařízení bylo zahájeno při streamování několik hodin.

1. Klikněte na tlačítko **spustit**.

    ![Vyberte vlastní data](./media/iot-accelerators-integrate-data-lake/stream-analytics-start-custom.png)

    Počkejte, dokud úloha přejde do běžícím stavu, pokud se zobrazí chyby může být z dotazu, ověřte správnost syntaxe.

    ![Spuštěná úloha](./media/iot-accelerators-integrate-data-lake/stream-analytics-running.png)

    Úloha streamování bude zahájeno čtení dat ze služby IoT Hub a uložení dat v Data Lake Store. To může trvat několik minut, pokud data chcete začít se zobrazí v Data Lake Store.

## <a name="explore-the-streaming-data"></a>Streamování data prozkoumat

1. Přejděte do Data Lake Store.

1. Na kartě Přehled klikněte na **Průzkumníku dat**.

1. V Průzkumníku dat přejdete dolů k **/ streamování** složky. Zobrazí se složkami, kterou vytvoří ve formátu RRRR/MM/DD/HH.

    ![Prozkoumejte dat](./media/iot-accelerators-integrate-data-lake/data-lake-store-data-explorer.png)

    Zobrazí se soubory json s jedním souborem za hodinu.

    ![Prozkoumejte dat](./media/iot-accelerators-integrate-data-lake/data-lake-store-file-preview.png)

## <a name="next-steps"></a>Další kroky

Azure Data Lake Analytics můžete použít k provedení analýzy velkých objemů dat o sadách dat vaše Data Lake Store. Další informace na [dokumentace k Data Lake Analytics](https://docs.microsoft.com/en-us/azure/data-lake-analytics).
