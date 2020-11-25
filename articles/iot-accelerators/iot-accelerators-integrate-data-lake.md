---
title: Streamování dat ze vzdáleného monitorování do Data Lake Store – Azure | Microsoft Docs
description: Naučte se integrovat řešení vzdáleného monitorování pomocí Azure Data Lake Store pomocí úlohy Azure Stream Analytics.
author: philmea
manager: timlt
ms.author: philmea
ms.date: 04/29/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: fce4da9cc9577bc9805289473d3df7647b1b0934
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96000466"
---
# <a name="integrate-the-remote-monitoring-solution-with-azure-data-lake-store"></a>Integrace řešení vzdáleného monitorování pomocí Azure Data Lake Store

Můžete mít pokročilé požadavky na analýzu nad rámec toho, co je nabízeno v řešení vzdáleného monitorování. Azure Data Lake Store je ideální pro tuto aplikaci, protože může ukládat data z obrovských a různorodých datových sad a také je integrovat s Azure Data Lake Analytics a zajistit tak analýzu na vyžádání.

V tomto postupu použijete úlohu Azure Stream Analytics ke streamování dat ze služby IoT Hub v řešení vzdáleného monitorování do Azure Data Lake Store.

## <a name="prerequisites"></a>Požadavky

Chcete-li dokončit tento postup, budete potřebovat následující:

* [Nasaďte akcelerátor řešení vzdáleného monitorování](quickstart-remote-monitoring-deploy.md).
  * Řešení vzdáleného monitorování nasadí službu IoT Hub a Azure Stream Analytics úlohy použité v tomto článku do svého předplatného Azure.
* [Nasazení Azure Data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md)
  * Vaše Data Lake Store by se měly nasadit do stejné oblasti jako řešení vzdáleného monitorování.
  * Ve svém účtu [vytvořte složku](../data-lake-store/data-lake-store-get-started-portal.md#createfolder) s názvem streaming.

## <a name="create-a-consumer-group"></a>Vytvoření skupiny uživatelů

Vytvořte vyhrazenou skupinu uživatelů ve službě IoT Hub řešení vzdáleného monitorování. Tento úkol bude použit úlohou Stream Analytics pro streamování dat do vašeho Data Lake Store.

> [!NOTE]
> Skupiny uživatelů používají aplikace k vyžádání dat z Azure IoT Hub. Měli byste vytvořit novou skupinu uživatelů pro každých pět výstupních spotřebitelů. Můžete vytvořit až 32 skupin uživatelů.

1. Přihlaste se k webu Azure Portal.

1. V Azure Portal klikněte na tlačítko **Cloud Shell** .

    ![Ikona spuštění portálu](./media/iot-accelerators-integrate-data-lake/portal-launch-icon.png)

1. Spuštěním tohoto příkazu vytvořte novou skupinu příjemců:

```azurecli-interactive
az iot hub consumer-group create --hub-name contoso-rm30263 --name streamanalyticsjob --resource-group contoso-rm
```

> [!NOTE]
> Použijte skupinu prostředků a názvy služby IoT Hub z řešení vzdáleného monitorování.

## <a name="create-stream-analytics-job"></a>Vytvořit úlohu Stream Analytics

Vytvořte úlohu Azure Stream Analytics pro streamování dat ze služby IoT Hub do úložiště Azure Data Lake.

1. Klikněte na **vytvořit prostředek**, na webu Marketplace vyberte Internet věcí a klikněte na **Stream Analytics úlohu**.

    ![Nová úloha Stream Analytics](./media/iot-accelerators-integrate-data-lake/new-stream-analytics-job.png)

1. Zadejte název úlohy a vyberte příslušné předplatné a skupinu prostředků.

1. Vyberte umístění v blízkosti nebo ve stejné oblasti jako Data Lake Store. Tady používáme Východní USA.

1. Zajistěte, aby se hostující prostředí nechalo jako výchozí **Cloud**.

1. Klikněte na **Vytvořit**.

    ![Vytvořit úlohu Stream Analytics](./media/iot-accelerators-integrate-data-lake/create-stream-analytics-job.png)

## <a name="configure-the-stream-analytics-job"></a>Konfigurace úlohy Stream Analytics

1. Ve skupině prostředků řešení vzdáleného monitorování přejdete na **úlohu Stream Analytics** .

1. Na stránce Přehled klikněte na možnost **vstupy**.

    ![Stránka Přehled](./media/iot-accelerators-integrate-data-lake/stream-analytics-overview.png)

1. Klikněte na **Přidat vstup datového proudu** a v rozevíracím seznamu vyberte **IoT Hub** .

    ![Přidat vstup](./media/iot-accelerators-integrate-data-lake/stream-analytics-add-input.png)

1. Na kartě nová vstup zadejte vstupní alias pro **IoTHub**.

1. V rozevíracím seznamu Skupina příjemců vyberte skupinu uživatelů, kterou jste vytvořili dříve. Tady používáme **streamanalyticsjob**.

    ![Vybrat vstup](./media/iot-accelerators-integrate-data-lake/stream-analytics-new-input.png)

1. Klikněte na **Uložit**.

1. Na stránce Přehled klikněte na možnost **výstupy**.

    ![Přidat Data Lake Store](./media/iot-accelerators-integrate-data-lake/stream-analytics-overview-2.png)

1. Klikněte na **Přidat** a v rozevíracím seznamu vyberte **Data Lake Store** .

    ![Přidat výstup](./media/iot-accelerators-integrate-data-lake/stream-analytics-output.png)

1. Na kartě nový výstup zadejte alias pro výstup **DataLakeStore**.

1. Vyberte účet Data Lake Store, který jste vytvořili v předchozích krocích, a poskytněte strukturu složek pro streamování dat do úložiště.

1. Do pole formát data zadejte **/streaming/{Date}/{Time}**. Ponechte výchozí formát data YYYY/MM/DD a formát času HH.

    ![Zadat strukturu složek](./media/iot-accelerators-integrate-data-lake/stream-analytics-new-output.png)

1. Klikněte na **autorizovat**.

    Abyste mohli úloze Stream Analytics povolit přístup k systému souborů, bude nutné autorizovat Data Lake Store.

    ![Autorizovat Stream Analytics Data Lake Store](./media/iot-accelerators-integrate-data-lake/stream-analytics-out-authorize.png)

    Zobrazí se automaticky otevírané okno a po dokončení autorizace se automaticky zavře tlačítko autorizovat autorizovat.

    > [!NOTE]
    > Pokud se v překryvném okně zobrazí chyba, otevřete nové okno prohlížeče v režimu anonymním a zkuste to znovu.

1. Klikněte na **Uložit**.

## <a name="edit-the-stream-analytics-query"></a>Úprava Stream Analyticsového dotazu

Azure Stream Analytics používá dotazovací jazyk podobný SQL k určení vstupního zdroje, který streamuje data, transformuje tato data podle potřeby a vypíše výstup do celé řady úložišť nebo cílů zpracování.

1. Na kartě Přehled klikněte na **Upravit dotaz**.

    ![Upravit dotaz](./media/iot-accelerators-integrate-data-lake/stream-analytics-edit-query.png)

1. V editoru dotazů Nahraďte zástupné symboly [YourOutputAlias] a [YourInputAlias] hodnotami, které jste definovali dříve.

    ```sql
    SELECT
        *, System.Timestamp as time
    INTO
        DataLakeStore
    FROM
        IoTHub
    ```

    ![Stream Analytics dotaz](./media/iot-accelerators-integrate-data-lake/stream-analytics-query.png)

1. Klikněte na **Uložit**.
1. Potvrďte změny kliknutím na **Ano** .

## <a name="start-the-stream-analytics-job"></a>Spustit úlohu Stream Analytics

1. Na kartě Přehled klikněte na tlačítko **Start**.

    ![Spustit úlohu Stream Analytics](./media/iot-accelerators-integrate-data-lake/stream-analytics-start.png)

1. Na kartě spustit úlohu klikněte na **vlastní**.

1. Nastavte vlastní čas, kdy se chcete vrátit k několika hodinám a vybírat data z doby, kdy zařízení zahájilo streamování.

1. Klikněte na tlačítko **Start**.

    ![Vybrat vlastní datum](./media/iot-accelerators-integrate-data-lake/stream-analytics-start-custom.png)

    Počkejte, dokud úloha nepřejde do stavu spuštěno, pokud se zobrazí chyby, které by mohly být z dotazu, zkontrolujte, zda je syntaxe správná.

    ![Úloha spuštěna](./media/iot-accelerators-integrate-data-lake/stream-analytics-running.png)

    Úloha streamování začne číst data z vašeho IoT Hub a ukládá je do Data Lake Store. Může trvat několik minut, než se data začnou zobrazovat v Data Lake Store.

## <a name="explore-the-streaming-data"></a>Prozkoumat streamovaná data

1. Přejít na Data Lake Store.

1. Na kartě Přehled klikněte na **Průzkumník dat**.

1. V Průzkumníku dat přejděte k podrobnostem do složky **/streaming** . Zobrazí se složky vytvořené ve formátu RRRR/MM/DD.

    ![Snímek obrazovky zobrazující cestu ke složce/streaming/YYYY/MM/DD/HH](./media/iot-accelerators-integrate-data-lake/data-lake-store-data-explorer.png)

    Soubory JSON se zobrazí v jednom souboru za hodinu.

    ![Prozkoumat streamovaná data](./media/iot-accelerators-integrate-data-lake/data-lake-store-file-preview.png)

## <a name="next-steps"></a>Další kroky

Azure Data Lake Analytics lze použít k provádění analýz velkých objemů dat na vašich Data Lake Store datových sadách. Další informace najdete v [dokumentaci k Data Lake Analytics](../data-lake-analytics/index.yml).