---
title: Zpracování datových proudů IoT v reálném čase pomocí Azure Stream Analytics
description: Zařízení SensorTag pro IoT, proudy dat, analytické funkce pro analýzu proudů dat a zpracování dat v reálném čase
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: how-to
ms.date: 11/26/2019
ms.openlocfilehash: a438401ff93c20d8759e6128936c3626bd3de484
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98012678"
---
# <a name="process-real-time-iot-data-streams-with-azure-stream-analytics"></a>Zpracování datových proudů IoT v reálném čase pomocí Azure Stream Analytics

V tomto článku se dozvíte, jak vytvořit logiku zpracování datového proudu pro shromažďování dat ze zařízení Internet věcí (IoT). Pomocí případu použití Real-World Internet věcí (IoT) můžete předvést, jak rychle a ekonomicky sestavovat řešení.

## <a name="prerequisites"></a>Předpoklady

* Vytvořte si bezplatné [předplatné Azure](https://azure.microsoft.com/pricing/free-trial/).
* Stáhněte si ukázkový dotaz a datové soubory z [GitHubu](https://aka.ms/azure-stream-analytics-get-started-iot).

## <a name="scenario"></a>Scenario

Contoso, společnost působící v oboru automatizace průmyslu, zcela automatizovala svůj výrobní proces. Stroje v továrně společnosti mají snímače, které dokáží vysílat datové proudy v reálném čase. V tomto scénáři chce manažer provozovny v reálném čase získávat informace na základě dat ze senzorů, hledat jejich pomocí různé vzorce a na jejich základě provádět potřebné akce. K vyhledání zajímavých vzorů z příchozího datového proudu dat můžete použít jazyk SAQL (Stream Analytics Query Language) prostřednictvím dat snímače.

V tomto příkladu jsou data generována ze zařízení se značkami Texas Instrumentation snímače. Datová část dat je ve formátu JSON a vypadá takto:

```json
{
    "time": "2016-01-26T20:47:53.0000000",  
    "dspl": "sensorE",  
    "temp": 123,  
    "hmdt": 34  
}  
```

V podobném scénáři z reálného prostředí by se jednalo o stovky podobných snímačů generujících události jako datový proud. V ideálním případě by bylo také nainstalováno nějaké zařízení sloužící jako brána k odesílání událostí do služby [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) nebo [Azure IoT Hubs](https://azure.microsoft.com/services/iot-hub/). Úlohu Stream Analytics tyto události ze služby Event Hubs ingestuje a spustí dotazy na analýzu datových proudů v reálném čase. Poté můžete výsledky odeslat do jednoho z [podporovaných výstupů](stream-analytics-define-outputs.md).

Pro snadnější použití tato příručka Začínáme poskytuje soubor ukázkových dat, která byla zaznamenána skutečnými zařízeními SensorTag. Na těchto ukázkových datech můžete spouštět dotazy a zobrazit výsledky. V následujících kurzech se naučíte připojovat své úlohy ke vstupům a výstupům a nasazovat je ve službě Azure.

## <a name="create-a-stream-analytics-job"></a>Vytvoření úlohy Stream Analytics

1. V [Azure Portal](https://portal.azure.com)v navigační nabídce vlevo vyberte **+ vytvořit prostředek** . Pak vyberte **Stream Analytics úlohy** z **analýz**.
   
    ![Vytvoření nové úlohy Stream Analytics](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-02.png)

1. Zadejte jedinečný název úlohy a ověřte, že je předplatné pro vaši úlohu správné. Vytvořte novou skupinu prostředků nebo vyberte existující v rámci předplatného.

1. Vyberte umístění pro vaši úlohu. Pro skupinu prostředků a všechny prostředky použijte stejné umístění, abyste zvýšili rychlost zpracování a snížili náklady. Po provedení konfigurace vyberte **vytvořit**.
   
    ![Podrobnosti o vytvoření nové úlohy Stream Analytics](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-03.png)

## <a name="create-an-azure-stream-analytics-query"></a>Vytvoření dotazu služby Stream Analytics
Dalším krokem po vytvoření úlohy je zápis dotazu. Můžete testovat dotazy proti ukázkovým datům bez propojení vstupu nebo výstupu s vaší úlohou.

Stáhněte si [HelloWorldASA-InputStream.js](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/GettingStarted/HelloWorldASA-InputStream.json
) z GitHubu. Pak v Azure Portal přejděte na svou Azure Stream Analytics úlohu.

V nabídce vlevo vyberte **dotaz** v části **topologie úlohy** . Pak vyberte **nahrát vzorový vstup**. Nahrajte `HelloWorldASA-InputStream.json` soubor a vyberte **OK**.

![Dlaždice dotazů na řídicím panelu Stream Analytics](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-05.png)

Všimněte si, že náhled dat se vyplní automaticky v tabulce **input Preview** .

![Náhled ukázkových vstupních dat](./media/stream-analytics-get-started-with-iot-devices/input-preview.png)

### <a name="query-archive-your-raw-data"></a>Dotaz: Archivace nezpracovaných dat

Nejjednodušším typem dotazu je průchozí dotaz, který archivuje veškerá vstupní data do definovaného výstupního umístění. Tento dotaz je výchozí dotaz naplněný v nové Azure Stream Analytics úlohy.

```sql
SELECT 
    *
INTO
    Output
FROM
    InputStream
```

Vyberte **test Query** a podívejte se na výsledky v tabulce **výsledky testu** .

![Výsledky testu pro dotaz Stream Analytics](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-07.png)

### <a name="query-filter-the-data-based-on-a-condition"></a>Dotaz: Filtrování dat na základě podmínky

Zkusíme filtrovat výsledky na základě podmínky. Chtěli bychom zobrazit výsledky jenom pro události, které pocházejí z "senzoru".

```sql
SELECT 
    time,
    dspl AS SensorName,
    temp AS Temperature,
    hmdt AS Humidity
INTO
   Output
FROM
    InputStream
WHERE dspl='sensorA'
```

Vložte dotaz do editoru a vyberte **test Query** a zkontrolujte výsledky.

![Filtrování datového proudu](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-08.png)

### <a name="query-alert-to-trigger-a-business-workflow"></a>Dotaz: Upozornění spouštějící pracovní postup společnosti

Vytvoříme podrobnější dotaz. Pro každý typ snímače chceme monitorovat průměrnou teplotu v 30sekundovém intervalu a výsledky zobrazit pouze v případě, že teplota překročí 100 stupňů.

```sql
SELECT 
    System.Timestamp AS OutputTime,
    dspl AS SensorName,
    Avg(temp) AS AvgTemperature
INTO
   Output
FROM
    InputStream TIMESTAMP BY time
GROUP BY TumblingWindow(second,30),dspl
HAVING Avg(temp)>100
```

![30sekundový dotaz filtru](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-10.png)

Měli byste vidět výsledky, které obsahují jenom 245 řádky a názvy senzorů, u kterých je průměr teplota překročila větší než 100. Tento dotaz seskupí toky událostí podle hodnoty **dspl**, což je název snímače, a pomocí 30sekundového **přeskakujícího okna**. Dočasné dotazy musí nastavovat způsob, jakým budete chtít čas sledovat. Pomocí klauzule **timestamp** by jste zadali sloupec **OUTPUTTIME** , který bude přidružit časy ke všem dočasným výpočtům. Podrobné informace najdete v tématu o funkcích [pro správu času](/stream-analytics-query/time-management-azure-stream-analytics) a [oknech](/stream-analytics-query/windowing-azure-stream-analytics).

### <a name="query-detect-absence-of-events"></a>Dotaz: Zjištění neexistence událostí

Jak napsat dotaz, abychom dokázali najít chybějící vstupní události? Pojďme najít čas, kdy senzor odeslal data a pak neodeslal události na dalších 5 sekund.

```sql
SELECT 
    t1.time,
    t1.dspl AS SensorName
INTO
   Output
FROM
    InputStream t1 TIMESTAMP BY time
LEFT OUTER JOIN InputStream t2 TIMESTAMP BY time
ON
    t1.dspl=t2.dspl AND
    DATEDIFF(second,t1,t2) BETWEEN 1 and 5
WHERE t2.dspl IS NULL
```

![Zjištění neexistence událostí](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-11.png)

V dotazu se používá příkaz **LEFT OUTER JOIN** na stejný datový proud (spojení sama na sebe). Při použití příkazu **INNER JOIN** se vrátí výsledek, pouze když je nalezena shoda.  Příkaz **LEFT OUTER JOIN** však v případě, že pro událost z levé strany spojení není nalezena shoda, vrátí řádek s hodnotami NULL pro všechny sloupce pravé strany. Tato technika je velmi užitečná k vyhledání absence událostí. Další informace najdete v tématu [Join](/stream-analytics-query/join-azure-stream-analytics).

## <a name="conclusion"></a>Závěr

Účelem tohoto článku je Ukázat, jak psát různé dotazy na dotazovací jazyk Stream Analytics a zobrazit výsledky v prohlížeči. To je ale jenom k tomu, abyste mohli začít. Stream Analytics podporuje celou řadu vstupů a výstupů a může dokonce využít i funkce ve službě Azure Machine Learning. To z něj dělá robustní nástroj pro analýzu datových proudů. Další informace o tom, jak psát dotazy, najdete v článku o [běžných vzorech dotazů](stream-analytics-stream-analytics-query-patterns.md).