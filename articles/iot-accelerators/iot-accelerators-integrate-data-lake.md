---
title: Streamování dat ze vzdáleného monitorování do úložiště datových jezer – Azure | Dokumenty společnosti Microsoft
description: Zjistěte, jak integrovat řešení vzdáleného monitorování s Azure Data Lake Store pomocí úlohy Azure Stream Analytics.
author: philmea
manager: timlt
ms.author: philmea
ms.date: 04/29/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: 0a684151e01b298c60ff17ef1470e0648a425850
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73889237"
---
# <a name="integrate-the-remote-monitoring-solution-with-azure-data-lake-store"></a>Integrace řešení vzdáleného monitorování s Azure Data Lake Store

Můžete mít pokročilé analytické požadavky nad rámec toho, co je nabízeno v řešení vzdáleného monitorování. Azure Data Lake Store je ideální pro tuto aplikaci, protože umí ukládat data z masivních a různorodých datových sad a také integrovat s Azure Data Lake Analytics a poskytovat analýzy na vyžádání.

V tomto návodu použijete úlohu Azure Stream Analytics k streamování dat z centra IoT v řešení vzdáleného monitorování do úložiště Azure Data Lake Store.

## <a name="prerequisites"></a>Požadavky

Chcete-li tento návod dokončit, budete potřebovat následující:

* [Nasazení akcelerátoru řešení vzdáleného monitorování](quickstart-remote-monitoring-deploy.md).
  * Řešení vzdáleného monitorování nasadí do vašeho předplatného Azure úlohu služby IoT hub a Azure Stream Analytics použité v tomto článku.
* [Nasazení úložiště datových jezer Azure](../data-lake-store/data-lake-store-get-started-portal.md)
  * Úložiště datového jezera by mělo být nasazeno do stejné oblasti jako řešení vzdáleného monitorování.
  * [Vytvořte ve](../data-lake-store/data-lake-store-get-started-portal.md#createfolder) svém účtu složku s názvem "streaming".

## <a name="create-a-consumer-group"></a>Vytvoření skupiny spotřebitelů

Vytvořte vyhrazenou skupinu spotřebitelů v centru IoT vašeho řešení vzdáleného monitorování. To bude použito úlohou Stream Analytics pro streamování dat do úložiště Data Lake Store.

> [!NOTE]
> Skupiny spotřebitelů používají aplikace k vyprávění dat z Azure IoT Hub. Měli byste vytvořit novou skupinu spotřebitelů pro každých pět výstupních spotřebitelů. Můžete vytvořit až 32 skupin spotřebitelů.

1. Přihlaste se k portálu Azure.

1. Na webu Azure Portal klikněte na tlačítko **Cloud Shell.**

    ![Ikona spuštění portálu](./media/iot-accelerators-integrate-data-lake/portal-launch-icon.png)

1. Spusťte tento příkaz a vytvořte novou skupinu spotřebitelů:

```azurecli-interactive
az iot hub consumer-group create --hub-name contoso-rm30263 --name streamanalyticsjob --resource-group contoso-rm
```

> [!NOTE]
> Použijte názvy skupiny prostředků a centra IoT z vašeho řešení vzdáleného monitorování.

## <a name="create-stream-analytics-job"></a>Vytvořit úlohu analýzy datového proudu

Vytvořte úlohu Azure Stream Analytics pro streamování dat z vašeho centra IoT do úložiště Azure Data Lake.

1. Klikněte na **Vytvořit zdroj**, na webu Marketplace vyberte Možnost Internet věcí a klikněte na **Úloha Stream Analytics**.

    ![Nová úloha analýzy datového proudu](./media/iot-accelerators-integrate-data-lake/new-stream-analytics-job.png)

1. Zadejte název úlohy a vyberte příslušnou skupinu Odběr a Prostředky.

1. Vyberte umístění v blízké nebo ve stejné oblasti jako úložiště dat lake store. Zde používáme východní USA.

1. Ujistěte se, že ponechat hostingové prostředí jako výchozí **Cloud**.

1. Klikněte na **Vytvořit**.

    ![Vytvořit úlohu analýzy datového proudu](./media/iot-accelerators-integrate-data-lake/create-stream-analytics-job.png)

## <a name="configure-the-stream-analytics-job"></a>Konfigurace úlohy Stream Analytics

1. Přejděte na **úlohu Stream Analytics** ve skupině prostředků řešení vzdáleného monitorování.

1. Na stránce Přehled klikněte na **Položky**.

    ![Stránka S přehledem](./media/iot-accelerators-integrate-data-lake/stream-analytics-overview.png)

1. V rozevíracím souboru klikněte na **Přidat vstup datového proudu** a vrozeněte **ioT hub.**

    ![Přidat vstup](./media/iot-accelerators-integrate-data-lake/stream-analytics-add-input.png)

1. Na kartě Nový vstup zadejte vstupní alias **IoTHubu**.

1. V rozevíracím přehledu Skupina příjemce vyberte skupinu spotřebitelů, kterou jste vytvořili dříve. Zde používáme **streamanalyticsjob**.

    ![Vybrat vstup](./media/iot-accelerators-integrate-data-lake/stream-analytics-new-input.png)

1. Klikněte na **Uložit**.

1. Na stránce Přehled klikněte na **Výstupy**.

    ![Přidat úložiště datových jezer](./media/iot-accelerators-integrate-data-lake/stream-analytics-overview-2.png)

1. V rozevíracím souboru klikněte na **Přidat** a vyberte **Úložiště datových jezer.**

    ![Přidat výstup](./media/iot-accelerators-integrate-data-lake/stream-analytics-output.png)

1. Na kartě Nový výstup zadejte alias Výstup **DataLakeStore**.

1. Vyberte účet Úložiště datového jezera, který jste vytvořili v předchozích krocích, a zadejte strukturu složek pro streamování dat do úložiště.

1. Do pole Formát data zadejte **/streaming/{date}/{time}**. Ponechte výchozí formát Data formátu YYYY/MM/DD a Time HH.

    ![Poskytnout strukturu složek](./media/iot-accelerators-integrate-data-lake/stream-analytics-new-output.png)

1. Klepněte na **tlačítko Autorizovat**.

    Budete muset autorizovat s Data Lake Store poskytnout stream analytics úlohy zápisu do systému souborů.

    ![Autorizace streamové analýzy do úložiště datových jezer](./media/iot-accelerators-integrate-data-lake/stream-analytics-out-authorize.png)

    Uvidíte vyskakovací okno a jakmile se vyskakovací okno zavře Autorizovat tlačítko bude šedě po dokončení autorizace.

    > [!NOTE]
    > Pokud se v automaticky otevíraném okně zobrazí chyba, otevřete nové okno prohlížeče v anonymním režimu a akci opakujte.

1. Klikněte na **Uložit**.

## <a name="edit-the-stream-analytics-query"></a>Úprava dotazu Stream Analytics

Azure Stream Analytics používá dotazovací jazyk podobný SQL k určení vstupního zdroje, který streamuje data, transformuje tato data podle potřeby a výstup do různých cílů úložiště nebo zpracování.

1. Na kartě Přehled klikněte na **Upravit dotaz**.

    ![Upravit dotaz](./media/iot-accelerators-integrate-data-lake/stream-analytics-edit-query.png)

1. V editoru dotazů nahraďte zástupné symboly [YourOutputAlias] a [YourInputAlias] hodnotami, které jste definovali dříve.

    ```sql
    SELECT
        *, System.Timestamp as time
    INTO
        DataLakeStore
    FROM
        IoTHub
    ```

    ![Dotaz analýzy datového proudu](./media/iot-accelerators-integrate-data-lake/stream-analytics-query.png)

1. Klikněte na **Uložit**.
1. Chcete-li změny přijmout, klepněte na tlačítko **Ano.**

## <a name="start-the-stream-analytics-job"></a>Spuštění úlohy Stream Analytics

1. Na kartě Přehled klikněte na **tlačítko Start**.

    ![Spustit úlohu analýzy datového proudu](./media/iot-accelerators-integrate-data-lake/stream-analytics-start.png)

1. Na kartě Spustit úlohu klikněte na **Vlastní**.

1. Nastavte vlastní čas pro návrat o několik hodin zpět, abyste mohli vyzvednout data z doby, kdy vaše zařízení začne streamovat.

1. Klepněte na tlačítko **Start**.

    ![Vybrat vlastní datum](./media/iot-accelerators-integrate-data-lake/stream-analytics-start-custom.png)

    Počkejte, až úloha přejde do stavu spuštění, pokud se zobrazí chyby, které by mohly být z dotazu, ujistěte se, že syntaxe je správná.

    ![Úloha spuštěna](./media/iot-accelerators-integrate-data-lake/stream-analytics-running.png)

    Úloha streamování začne číst data z vašeho ioT hubu a ukládat data do úložiště datového jezera. Může trvat několik minut, než se data začnou zobrazovat v úložišti Data Lake Store.

## <a name="explore-the-streaming-data"></a>Prozkoumejte streamovaná data

1. Přejděte do obchodu Data Lake Store.

1. Na kartě Přehled klikněte na **Průzkumník dat**.

1. V Průzkumníku dat přejděte k složce **/streaming.** Uvidíte složky vytvořené ve formátu YYYY/MM/DD/HH.

    ![Prozkoumejte streamovaná data](./media/iot-accelerators-integrate-data-lake/data-lake-store-data-explorer.png)

    Uvidíte json soubory s jedním souborem za hodinu.

    ![Prozkoumejte streamovaná data](./media/iot-accelerators-integrate-data-lake/data-lake-store-file-preview.png)

## <a name="next-steps"></a>Další kroky

Azure Data Lake Analytics se dá použít k analýze velkých objemů dat ve vašich datových sadách Úložiště datových jezer. Další informace naleznete v [dokumentaci](https://docs.microsoft.com/azure/data-lake-analytics)k analýze dat .
