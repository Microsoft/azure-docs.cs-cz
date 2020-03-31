---
title: Zpracování datových streamů IoT v reálném čase pomocí Azure Stream Analytics
description: Zařízení SensorTag pro IoT, proudy dat, analytické funkce pro analýzu proudů dat a zpracování dat v reálném čase
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 11/26/2019
ms.openlocfilehash: 0755131f7d8071e37eadc1339ebc5e122725fa71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426246"
---
# <a name="process-real-time-iot-data-streams-with-azure-stream-analytics"></a>Zpracování datových streamů IoT v reálném čase pomocí Azure Stream Analytics

V tomto článku se dozvíte, jak vytvořit logiku zpracování datového proudu pro shromažďování dat ze zařízení Internetu věcí (IoT). Pomocí případu použití internetu věcí (IoT) v reálném světě můžete předvést, jak rychle a ekonomicky vytvořit řešení.

## <a name="prerequisites"></a>Požadavky

* Vytvořte bezplatné [předplatné Azure](https://azure.microsoft.com/pricing/free-trial/).
* Stáhněte si ukázkový dotaz a datové soubory z [GitHubu](https://aka.ms/azure-stream-analytics-get-started-iot).

## <a name="scenario"></a>Scénář

Contoso, společnost působící v oboru automatizace průmyslu, zcela automatizovala svůj výrobní proces. Stroje v továrně společnosti mají snímače, které dokáží vysílat datové proudy v reálném čase. V tomto scénáři chce manažer provozovny v reálném čase získávat informace na základě dat ze senzorů, hledat jejich pomocí různé vzorce a na jejich základě provádět potřebné akce. Pomocí dotazovacího jazyka Stream Analytics (SAQL) přes data senzorů můžete najít zajímavé vzory z příchozího datového proudu.

V tomto příkladu jsou data generována ze zařízení značky snímače Texas Instruments. Datová část dat je ve formátu JSON a vypadá takto:

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

1. Na [webu Azure Portal](https://portal.azure.com)vyberte **+ Vytvořit prostředek** z levé navigační nabídky. Potom vyberte **úlohu Stream Analytics** z **Analytics**.
   
    ![Vytvoření nové úlohy Stream Analytics](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-02.png)

1. Zadejte jedinečný název úlohy a ověřte, že je předplatné pro vaši úlohu správné. Vytvořte novou skupinu prostředků nebo vyberte existující skupinu z předplatného.

1. Vyberte umístění pro svou práci. Použijte stejné umístění pro skupinu prostředků a všechny prostředky ke zvýšení rychlosti zpracování a snížení nákladů. Po vytvoření konfigurací vyberte **Vytvořit**.
   
    ![Podrobnosti o vytvoření nové úlohy Stream Analytics](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-03.png)

## <a name="create-an-azure-stream-analytics-query"></a>Vytvoření dotazu služby Stream Analytics
Dalším krokem po vytvoření úlohy je napsat dotaz. Můžete testovat dotazy proti ukázková data bez připojení vstup nebo výstup do úlohy.

Stáhněte si [helloworldasa-inputstream.json](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/GettingStarted/HelloWorldASA-InputStream.json
) z GitHubu. Potom přejděte na úlohu Azure Stream Analytics na webu Azure Portal.

V levé nabídce vyberte **Dotaz** v části **Topologie úlohy.** Pak vyberte **Nahrát ukázkový vstup**. Nahrajte `HelloWorldASA-InputStream.json` soubor a vyberte **Ok**.

![Dlaždice dotazu na řídicí panel Služby Stream Analytics](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-05.png)

Všimněte si, že náhled dat je automaticky naplněn v tabulce **Vstupní náhled.**

![Náhled ukázkových vstupních dat](./media/stream-analytics-get-started-with-iot-devices/input-preview.png)

### <a name="query-archive-your-raw-data"></a>Dotaz: Archivace nezpracovaných dat

Nejjednodušším typem dotazu je průchozí dotaz, který archivuje veškerá vstupní data do definovaného výstupního umístění. Tento dotaz je výchozí dotaz naplněný v nové úloze Azure Stream Analytics.

```sql
SELECT 
    *
INTO
    Output
FROM
    InputStream
```

Vyberte **Testovat dotaz** a zobrazte výsledky v tabulce **Výsledky testů.**

![Výsledky testů pro dotaz Stream Analytics](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-07.png)

### <a name="query-filter-the-data-based-on-a-condition"></a>Dotaz: Filtrování dat na základě podmínky

Pokusme se filtrovat výsledky na základě podmínky. Rádi bychom ukázali výsledky pouze pro ty události, které pocházejí z "sensorA".

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

Vložte dotaz do editoru a vyberte **Testovat dotaz** a zkontrolujte výsledky.

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

Měli byste vidět výsledky, které obsahují pouze 245 řádků a názvy senzorů, kde je průměrná míra mírného množství větší než 100. Tento dotaz seskupí toky událostí podle hodnoty **dspl**, což je název snímače, a pomocí 30sekundového **přeskakujícího okna**. Časové dotazy musí uvádět, jak chcete, aby čas pokročil. Pomocí klauzule **TIMESTAMP BY** jste zadali sloupec **OUTPUTTIME** pro přidružení časů ke všem časovým výpočtům. Podrobné informace naleznete v informacích o [funkcích Time Management](https://docs.microsoft.com/stream-analytics-query/time-management-azure-stream-analytics) a [Windowing](https://docs.microsoft.com/stream-analytics-query/windowing-azure-stream-analytics).

### <a name="query-detect-absence-of-events"></a>Dotaz: Zjištění neexistence událostí

Jak napsat dotaz, abychom dokázali najít chybějící vstupní události? Pojďme zjistit, kdy naposledy, že senzor poslal data a pak neposlal události pro příštích 5 sekund.

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

V dotazu se používá příkaz **LEFT OUTER JOIN** na stejný datový proud (spojení sama na sebe). Při použití příkazu **INNER JOIN** se vrátí výsledek, pouze když je nalezena shoda.  Příkaz **LEFT OUTER JOIN** však v případě, že pro událost z levé strany spojení není nalezena shoda, vrátí řádek s hodnotami NULL pro všechny sloupce pravé strany. Tato technika je velmi užitečná k vyhledání absence událostí. Další informace naleznete v tématu [JOIN](https://docs.microsoft.com/stream-analytics-query/join-azure-stream-analytics).

## <a name="conclusion"></a>Závěr

Účelem tohoto článku je ukázat, jak psát různé dotazy Stream Analytics dotazovací ho jazyka a zobrazit výsledky v prohlížeči. Nicméně, to je jen proto, abyste mohli začít. Stream Analytics podporuje celou řadu vstupů a výstupů a může dokonce využít i funkce ve službě Azure Machine Learning. To z něj dělá robustní nástroj pro analýzu datových proudů. Další informace o tom, jak psát dotazy, najdete v článku o [běžných vzorech dotazů](stream-analytics-stream-analytics-query-patterns.md).

