---
title: Při integraci řešení vzdáleného monitorování Azure Data Lake Store | Dokumentace Microsoftu
description: Zjistěte, jak integrovat řešení vzdáleného monitorování s Azure Data Lake Store pomocí úlohy Azure Stream Analytics.
author: philmea
manager: timlt
ms.author: philmea
ms.date: 04/29/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: 5ba9b5534e986be1cbe55043a9acdd981d2ed7fd
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/12/2018
ms.locfileid: "38971733"
---
# <a name="integrate-the-remote-monitoring-solution-with-azure-data-lake-store"></a>Při integraci řešení vzdáleného monitorování Azure Data Lake Store

Může mít pokročilé analýzy požadavky nad rámec nabízejí v řešení vzdáleného monitorování. Azure Data Lake Store je ideální pro tuto aplikaci, protože ho může ukládat data z masivního a různých datových sad, stejně jako integraci s Azure Data Lake Analytics k poskytování analýz na vyžádání.

V tomto návodu použijete úlohy Azure Stream Analytics pro streamování dat ze služby IoT hub v řešení vzdáleného monitorování pro Azure Data Lake Store.

## <a name="prerequisites"></a>Požadavky

Pokud chcete dokončit tento návod, budete potřebovat následující:

* [Nasazení akcelerátoru řešení vzdáleného monitorování](iot-accelerators-remote-monitoring-deploy.md).
  * Řešení vzdáleného monitorování bude nasazení služby IoT hub a úlohy Azure Stream Analytics používá v tomto článku do vašeho předplatného Azure.
* [Nasazení Azure Data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md)
  * Vaše Data Lake Store musí být nasazené do stejné oblasti jako vaše řešení vzdálené monitorování.
  * [Vytvořte složku](../data-lake-store/data-lake-store-get-started-portal.md#createfolder) s názvem "streaming" ve vašem účtu.

## <a name="create-a-consumer-group"></a>Vytvořit skupinu uživatelů

Vytvořte vyhrazenou skupinu spotřebitelů ve službě IoT hub vašeho řešení vzdáleného monitorování. Ten se použije úloha Stream Analytics pro streamovaná data pro vaše Data Lake Store.

> [!NOTE]
> Skupiny uživatelů se aplikace používají k načítání dat ze služby Azure IoT Hub. Pro každých pět výstupu příjemci měli vytvořit novou skupinu uživatelů. Můžete vytvořit až 32 skupiny příjemců.

1. Přihlaste se k portálu Azure.

1. Na webu Azure Portal, klikněte na tlačítko **Cloud Shell** tlačítko.

    ![Ikona spuštění portálu](./media/iot-accelerators-integrate-data-lake/portal-launch-icon.png)

1. Spusťte tento příkaz vytvoří novou skupinu uživatelů:

```azurecli-interactive
az iot hub consumer-group create --hub-name contoso-rm30263 --name streamanalyticsjob --resource-group contoso-rm
```

> [!NOTE]
> Použijte skupinu prostředků a názvy centra IoT z vašeho řešení vzdáleného monitorování.

## <a name="create-stream-analytics-job"></a>Vytvoření úlohy Stream Analytics

Vytvořte úlohu Azure Stream Analytics Streamovat data ze služby IoT hub k účtu Azure Data Lake store.

1. Klikněte na tlačítko **vytvořit prostředek**, vyberte Internet of Things na webu Marketplace a klikněte na **úlohy Stream Analytics**.

    ![Nová úloha Stream Analytics](./media/iot-accelerators-integrate-data-lake/new-stream-analytics-job.png)

1. Zadejte název projektu a vyberte příslušné předplatné a skupinu prostředků.

1. Vyberte umístění, do na téměř nebo ve stejné oblasti jako vaše Data Lake Store. Tady používáme, USA – východ.

1. Nezapomeňte ponechte hostitelské prostředí jako výchozí **cloudu**.

1. Klikněte na možnost **Vytvořit**.

    ![Vytvoření úlohy Stream Analytics](./media/iot-accelerators-integrate-data-lake/create-stream-analytics-job.png)

## <a name="configure-the-stream-analytics-job"></a>Konfigurace úlohy Stream Analytics

1. Přejděte **úlohy Stream Analytics** ve vaší skupině prostředků řešení vzdáleného monitorování.

1. Na stránce Přehled klikněte na tlačítko **vstupy**.

    ![Stránka s přehledem](./media/iot-accelerators-integrate-data-lake/stream-analytics-overview.png)

1. Klikněte na tlačítko **přidat vstup streamu** a vyberte **služby IoT Hub** z rozevíracího seznamu.

    ![Přidat vstup](./media/iot-accelerators-integrate-data-lake/stream-analytics-add-input.png)

1. Na nové kartě vstupu zadejte vstupní alias **IoTHub**.

1. Příjemce skupiny rozevíracího seznamu vyberte skupinu příjemců, který jste vytvořili dříve. Tady používáme **streamanalyticsjob**.

    ![Vyberte vstup](./media/iot-accelerators-integrate-data-lake/stream-analytics-new-input.png)

1. Klikněte na **Uložit**.

1. Na stránce Přehled klikněte na tlačítko **výstupy**.

    ![Přidat Data Lake Store](./media/iot-accelerators-integrate-data-lake/stream-analytics-overview-2.png)

1. Klikněte na tlačítko **přidat** a vyberte **Data Lake Store** z rozevíracího seznamu.

    ![Přidat výstup](./media/iot-accelerators-integrate-data-lake/stream-analytics-output.png)

1. Na kartě nový výstup zadejte alias pro výstup z **DataLakeStore**.

1. Vyberte účet Data Lake Store, který jste vytvořili v předchozích krocích a poskytnout strukturu složek pro streamování dat do úložiště.

1. V poli Formát data, zadejte **/streaming/ {date} / {time}**. Ponechte výchozí formát data rrrr/MM/DD a formát času HH.

    ![Poskytuje strukturu složek](./media/iot-accelerators-integrate-data-lake/stream-analytics-new-output.png)

1. Klikněte na tlačítko **Autorizovat**.

    Budete muset autorizovat s Data Lake Store přiřadit přístup pro zápis úlohy Stream analytics k systému souborů.

    ![Povolit Stream Analytics k Data Lake Store](./media/iot-accelerators-integrate-data-lake/stream-analytics-out-authorize.png)

    Zobrazí se automaticky otevíraného okna a jakmile se automaticky otevírané okno se zavře Authorize tlačítko bude zapnutá po dokončení autorizace.

    > [!NOTE]
    > Pokud se zobrazí v automaticky otevíraném okně chybu, otevřete nové okno prohlížeče v anonymním režimu a zkuste to znovu.

1. Klikněte na **Uložit**.

## <a name="edit-the-stream-analytics-query"></a>Úprava dotazu Stream Analytics

Azure Stream Analytics používá dotazovací jazyk typu SQL jako vstupní zdroj, který data streamuje, transformovat data jako požadovaný a výstup do celé řady z míst pro uložení nebo zpracování.

1. Na kartě Přehled klikněte na tlačítko **upravit dotaz**.

    ![Upravit dotaz](./media/iot-accelerators-integrate-data-lake/stream-analytics-edit-query.png)

1. V editoru dotazů, nahraďte [YourOutputAlias] a [YourInputAlias] zástupné symboly hodnotami, které jste definovali dříve.

    ```sql
    SELECT
        *, System.Timestamp as time
    INTO
        DataLakeStore
    FROM
        IoTHub
    ```

    ![Dotazu Stream Analytics](./media/iot-accelerators-integrate-data-lake/stream-analytics-query.png)

1. Klikněte na **Uložit**.
1. Klikněte na tlačítko **Ano** změny uložte.

## <a name="start-the-stream-analytics-job"></a>Spuštění úlohy Stream Analytics

1. Na kartě Přehled klikněte na tlačítko **Start**.

    ![Spuštění úlohy Stream Analytics](./media/iot-accelerators-integrate-data-lake/stream-analytics-start.png)

1. Na kartě úlohy Start klikněte na tlačítko **vlastní**.

1. Nastavit vlastní čas se vrátíte k vyzvednutí data z vašeho zařízení bylo zahájeno při streamování několik hodin.

1. Klikněte na tlačítko **Start**.

    ![Vyberte si vlastní datum](./media/iot-accelerators-integrate-data-lake/stream-analytics-start-custom.png)

    Počkejte, dokud úloha přejde do spuštěném stavu, pokud se zobrazí chyby, může to být z dotazu, ověřte, že je syntaxe správná.

    ![Spuštěná úloha](./media/iot-accelerators-integrate-data-lake/stream-analytics-running.png)

    Úloha streamování se začnou číst data ze služby IoT Hub a uložení dat v vaše Data Lake Store. Může trvat několik minut, než se data začnou objevovat v vaše Data Lake Store.

## <a name="explore-the-streaming-data"></a>Prozkoumejte streamovaných dat

1. Přejdete na vaše Data Lake Store.

1. Na kartě Přehled klikněte na tlačítko **Průzkumník dat**.

1. V Průzkumníku dat, přejít k podrobnostem **/ streamování** složky. Zobrazí se složkami, kterou vytvoří ve formátu RRRR/MM/DD/HH.

    ![Prozkoumejte streamovaných dat](./media/iot-accelerators-integrate-data-lake/data-lake-store-data-explorer.png)

    Zobrazí se soubory json s jedním souborem za hodinu.

    ![Prozkoumejte streamovaných dat](./media/iot-accelerators-integrate-data-lake/data-lake-store-file-preview.png)

## <a name="next-steps"></a>Další kroky

Azure Data Lake Analytics je možné provádět analýzy velkých objemů dat na své datové sady Data Lake Store. Další informace najdete na [dokumentace ke službě Data Lake Analytics](https://docs.microsoft.com/azure/data-lake-analytics).
