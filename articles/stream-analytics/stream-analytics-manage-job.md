---
title: 'Kurz: vytvoření a Správa úlohy Stream Analytics pomocí Azure Portal'
description: Tento kurz poskytuje ucelenou ukázku způsobu použití Azure Stream Analytics k analýze podvodných volání ve streamu telefonních hovorů.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.workload: data-services
ms.topic: tutorial
ms.custom: mvc
ms.date: 06/03/2019
ms.openlocfilehash: 540cd0e136dc602848c34edbd3914d69ca725758
ms.sourcegitcommit: be344deef6b37661e2c496f75a6cf14f805d7381
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/07/2019
ms.locfileid: "72000548"
---
# <a name="analyze-phone-call-data-with-stream-analytics-and-visualize-results-in-power-bi-dashboard"></a>Analýza dat telefonního hovoru pomocí Stream Analytics a vizualizace výsledků v řídicím panelu Power BI

V tomto kurzu se naučíte analyzovat data telefonních hovorů pomocí Azure Stream Analytics. Data telefonního hovoru generovaná klientskou aplikací obsahují některá falešná volání, která se budou filtrovat podle Stream Analytics úlohy.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Generování ukázkových dat telefonního hovoru a jejich odeslání do Azure Event Hubs
> * Vytvoření úlohy Stream Analytics
> * Konfigurace vstupu a výstupu úlohy
> * Definování dotazu pro filtrování podvodných volání
> * Testování a spuštění úlohy
> * Vizualizace výsledků v Power BI

## <a name="prerequisites"></a>Požadavky

Než začnete, proveďte následující akce:

* Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/).
* Přihlaste se k [Azure Portal](https://portal.azure.com/).
* Stáhněte si aplikaci generátor událostí telefonního hovoru [TelcoGenerator. zip](https://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip) z webu Microsoft Download Center nebo získejte zdrojový kód z [GitHubu](https://aka.ms/azure-stream-analytics-telcogenerator).
* Budete potřebovat Power BI účet.

## <a name="create-an-azure-event-hub"></a>Vytvoření centra událostí Azure

Než Stream Analytics může analyzovat datový proud podvodných volání, musí být data odeslána do Azure. V tomto kurzu budete odesílat data do Azure pomocí [Event Hubs Azure](https://docs.microsoft.com/azure/event-hubs/event-hubs-what-is-event-hubs).

Pomocí následujících kroků vytvořte centrum událostí a odešlete data volání do tohoto centra událostí:

1. Přihlaste se k [Azure Portal](https://portal.azure.com/).
2. Vyberte **vytvořit prostředek** > **Internet věcí** **Event Hubs** > .

   ![Vytvoření centra událostí Azure na portálu](media/stream-analytics-manage-job/find-event-hub-resource.png)
3. Vyplňte podokno **vytvořit obor názvů** s následujícími hodnotami:

   |**Nastavením**  |**Navrhovaná hodnota** |**Popis**  |
   |---------|---------|---------|
   |Name     | myEventHubsNS        |  Jedinečný název, který identifikuje obor názvů centra událostí.       |
   |Formě     |   předplatné \<Your @ no__t-1      |   Vyberte předplatné Azure, ve kterém chcete vytvořit centrum událostí.      |
   |Skupina prostředků     |   MyASADemoRG      |  Vyberte **vytvořit nový** a zadejte nový název skupiny prostředků pro váš účet.       |
   |Umístění     |   Západní USA 2      |    Umístění, kde se dá nasadit obor názvů centra událostí.     |

4. Pro zbývající nastavení použijte výchozí možnosti a vyberte **vytvořit**.

   ![Vytvořit obor názvů centra událostí v Azure Portal](media/stream-analytics-manage-job/create-event-hub-namespace.png)

5. Po dokončení nasazení oboru názvů, přejít na **všechny prostředky** a najít *myEventHubsNS* v seznamu prostředků Azure. Vyberte *myEventHubsNS* a otevřete ji.
6. Dále vyberte **+ centrum událostí** a zadejte **název** jako *MyEventHub* nebo jiný název podle vašeho výběru. Pro zbývající nastavení použijte výchozí možnosti a vyberte **vytvořit**. Pak počkejte, než bude nasazení úspěšné.

   ![Konfigurace centra událostí v Azure Portal](media/stream-analytics-manage-job/create-event-hub-portal.png)

### <a name="grant-access-to-the-event-hub-and-get-a-connection-string"></a>Udělení přístupu k centru událostí a získání připojovacího řetězce

Aby mohla aplikace odesílat data do Azure Event Hubs, musí mít centrum událostí zásadu, která umožňuje odpovídající přístup. Zásady přístupu vytváří připojovací řetězec, který obsahuje autorizační informace.

1. Přejděte do centra událostí, které jste vytvořili v předchozím kroku, MyEventHub *. V části **Nastavení**vyberte **zásady sdíleného přístupu** a pak vyberte **+ Přidat**.

2. Pojmenujte zásady **MyPolicy** a ujistěte se, že je zaškrtnuté políčko **Spravovat** . Pak vyberte **vytvořit**.

   ![Vytvořit zásady sdíleného přístupu centra událostí](media/stream-analytics-manage-job/create-event-hub-access-policy.png)

3. Po vytvoření zásady vyberte, že se má zásada otevřít, a vyhledejte **připojovací řetězec – primární klíč**. Vyberte modré tlačítko **Kopírovat** vedle připojovacího řetězce.

   ![Uložení připojovacího řetězce zásad sdíleného přístupu](media/stream-analytics-manage-job/save-connection-string.png)

4. Vložte připojovací řetězec do textového editoru. Tento připojovací řetězec budete potřebovat v další části.

   Připojovací řetězec vypadá takto:

   `Endpoint=sb://<Your event hub namespace>.servicebus.windows.net/;SharedAccessKeyName=<Your shared access policy name>;SharedAccessKey=<generated key>;EntityPath=<Your event hub name>`

   Všimněte si, že připojovací řetězec obsahuje několik párů klíč-hodnota oddělených středníky: **Endpoint**, **SharedAccessKeyName**, **SharedAccessKey**a **EntityPath**.

## <a name="start-the-event-generator-application"></a>Spuštění aplikace generátoru událostí

Před spuštěním aplikace TelcoGenerator byste ji měli nakonfigurovat tak, aby odesílala data do služby Azure Event Hubs, kterou jste vytvořili dříve.

1. Extrahujte obsah souboru [TelcoGenerator. zip](https://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip) .
2. Otevřete soubor `TelcoGenerator\TelcoGenerator\telcodatagen.exe.config` v textovém editoru podle vašeho výběru (více než jeden soubor. config, takže si nezapomeňte otevřít ten správný).

3. Aktualizujte element `<appSettings>` v konfiguračním souboru s následujícími podrobnostmi:

   * Nastavte hodnotu klíče *EventHubName* na hodnotu EntityPath v připojovacím řetězci.
   * Nastavte hodnotu klíče *Microsoft. ServiceBus. ConnectionString* na připojovací řetězec bez hodnoty EntityPath (Nezapomeňte odebrat středník, který ho předchází).

4. Uložte soubor.
5. Poté otevřete příkazové okno a přejděte do složky, do které jste TelcoGenerator aplikaci. Potom zadejte následující příkaz:

   ```cmd
   telcodatagen.exe 1000 0.2 2
   ```

   Tento příkaz má následující parametry:
   * Počet záznamů dat volání za hodinu.
   * Procento pravděpodobnosti podvodů, což je, jak často by měla aplikace simulovat podvodné volání. Hodnota 0,2 znamená, že přibližně 20% záznamů volání bude vypadat podvodně.
   * Doba trvání v hodinách, což je počet hodin, po které by měla aplikace běžet. Aplikaci můžete kdykoli zastavit ukončením procesu (**CTRL + C**) na příkazovém řádku.

   Po několika sekundách se v aplikaci spustí na obrazovce zobrazení záznamů telefonních hovorů, protože se odesílají do centra událostí. Data telefonního hovoru obsahují následující pole:

   |**Zapisovací**  |**Definition**  |
   |---------|---------|
   |CallrecTime    |  Časové razítko pro čas spuštění volání       |
   |SwitchNum     |  Telefonní přepínač použitý k připojení volání. V tomto příkladu jsou přepínače řetězce reprezentující zemi nebo oblast původu (USA, Čína, Spojené království, Německo nebo Austrálie).       |
   |CallingNum     |  Telefonní číslo volajícího.       |
   |CallingIMSI     |  Mezinárodní identita mobilního předplatitele (číslo IMSI). Je to jedinečný identifikátor volajícího.       |
   |CalledNum     |   Telefonní číslo příjemce volání.      |
   |CalledIMSI     |  Číslo IMSI (International Mobile Subscriber Identity). Je to jedinečný identifikátor příjemce volání.       |

## <a name="create-a-stream-analytics-job"></a>Vytvoření úlohy Stream Analytics

Teď, když máte proud událostí volání, můžete vytvořit úlohu Stream Analytics, která načte data z centra událostí.

1. Chcete-li vytvořit úlohu Stream Analytics, přejděte na [Azure Portal](https://portal.azure.com/).

2. Vyberte **vytvořit prostředek** > **Internet věcí** **Stream Analytics úlohou** > .

3. Vyplňte podokno **nová Stream Analytics úlohy** následujícími hodnotami:

   |**Nastavením**  |**Navrhovaná hodnota**  |**Popis**  |
   |---------|---------|---------|
   |Název úlohy     |  S názvem asatutorial       |   Jedinečný název, který identifikuje obor názvů centra událostí.      |
   |Formě    |  předplatné \<Your @ no__t-1   |   Vyberte předplatné Azure, ve kterém chcete vytvořit úlohu.       |
   |Skupina prostředků   |   MyASADemoRG      |   Vyberte **použít existující** a zadejte nový název skupiny prostředků pro váš účet.      |
   |Umístění   |    Západní USA 2     |      Umístění, kde lze úlohu nasadit. Doporučuje se umístit úlohu a centrum událostí ve stejné oblasti pro dosažení co nejlepších výsledků, takže nebudete platit za přenos dat mezi oblastmi.      |
   |Hostitelské prostředí    | Cloud        |     Stream Analytics úlohy je možné nasadit do cloudu nebo na Edge. Cloud umožňuje nasazení do cloudu Azure a Edge umožňuje nasazení do zařízení IoT Edge.    |
   |Jednotky streamování     |    první       |      Jednotky streamování představují výpočetní prostředky, které jsou nutné ke spuštění úlohy. Ve výchozím nastavení je tato hodnota nastavená na 1. Další informace o škálování jednotek streamování najdete v článku [Principy a úpravy jednotek streamování](stream-analytics-streaming-unit-consumption.md) .      |

4. Pro zbývající nastavení použijte výchozí možnosti, vyberte **vytvořit**a počkejte na úspěšné nasazení.

   ![Vytvoření úlohy Azure Stream Analytics](media/stream-analytics-manage-job/create-stream-analytics-job.png)

## <a name="configure-job-input"></a>Konfigurovat vstup úlohy

Dalším krokem je definování vstupního zdroje pro úlohu pro čtení dat pomocí centra událostí, které jste vytvořili v předchozí části.

1. V Azure Portal otevřete podokno **všechny prostředky** a vyhledejte úlohu Stream Analytics *s názvem asatutorial* .

2. V části **topologie úlohy** v podokně Stream Analytics úlohy vyberte možnost **vstupy** .

3. Vyberte **+ Přidat vstup streamu** a **centrum událostí**. Vyplňte podokno následujícími hodnotami:

   |**Nastavením**  |**Navrhovaná hodnota**  |**Popis**  |
   |---------|---------|---------|
   |Alias vstupu     |  CallStream       |  Zadejte popisný název pro identifikaci vašeho vstupu. Vstupní alias může obsahovat jenom alfanumerické znaky, spojovníky a podtržítka a musí být 3-63 znaků dlouhý.       |
   |Formě    |   předplatné \<Your @ no__t-1      |   Vyberte předplatné Azure, ve kterém jste vytvořili centrum událostí. Centrum událostí může být ve stejném nebo jiném předplatném jako úloha Stream Analytics.       |
   |Obor názvů centra událostí    |  myEventHubsNS       |  Vyberte obor názvů centra událostí, který jste vytvořili v předchozí části. Všechny obory názvů centra událostí dostupné v aktuálním předplatném jsou uvedeny v rozevírací nabídce.       |
   |Název centra událostí    |   MyEventHub      |  Vyberte centrum událostí, které jste vytvořili v předchozí části. Všechna centra událostí dostupná v aktuálním předplatném jsou uvedena v rozevíracím seznamu.       |
   |Název zásad centra událostí   |  MyPolicy       |  Vyberte zásady sdíleného přístupu centra událostí, které jste vytvořili v předchozí části. Všechny zásady centra událostí dostupné v aktuálním předplatném jsou uvedeny v rozevírací nabídce.       |

4. Pro zbývající nastavení použijte výchozí možnosti a vyberte **Uložit**.

   ![Konfigurace vstupu Azure Stream Analytics](media/stream-analytics-manage-job/configure-stream-analytics-input.png)

## <a name="configure-job-output"></a>Nakonfigurovat výstup úlohy

Posledním krokem je definování výstupní jímky pro úlohu, kde může zapisovat transformovaná data. V tomto kurzu výstup a vizualizace dat pomocí Power BI.

1. V podokně Azure Portal otevřete **všechny prostředky** a úlohu Stream Analytics *s názvem asatutorial* .

2. V části **topologie úlohy** v podokně Stream Analytics úlohy vyberte možnost **výstupy** .

3. Vyberte **+ přidat** > **Power BI**. Pak vyplňte formulář následujícími podrobnostmi a vyberte **autorizovat**:

   |**Nastavením**  |**Navrhovaná hodnota**  |
   |---------|---------|
   |Alias pro výstup  |  MyPBIoutput  |
   |Název datové sady  |   ASAdataset  |
   |Název tabulky |  ASATable  |

   ![Konfigurace výstupu Stream Analytics](media/stream-analytics-manage-job/configure-stream-analytics-output.png)

4. Když vyberete **autorizovat**, otevře se automaticky otevírané okno s výzvou k zadání přihlašovacích údajů pro ověření účtu Power BI. Po úspěšném ověření **uložte** nastavení.

## <a name="define-a-query-to-analyze-input-data"></a>Definování dotazu pro analýzu vstupních dat

Dalším krokem je vytvoření transformace, která analyzuje data v reálném čase. Transformační dotaz definujete pomocí [Stream Analytics dotazovacího jazyka](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference). Dotaz použitý v tomto kurzu detekuje podvodné hovory z telefonních dat.

V tomto příkladu jsou falešná volání uskutečněna ze stejného uživatele během pěti sekund, ale v různých umístěních. Stejný uživatel například nemůže legitimně uskutečnit volání z USA a Austrálie ve stejnou dobu. Definování transformačního dotazu pro úlohu Stream Analytics:

1. V Azure Portal otevřete podokno **všechny prostředky** a přejděte do úlohy služby **s názvem asatutorial** Stream Analytics, kterou jste vytvořili dříve.

2. V části **topologie úlohy** v podokně Stream Analytics úlohy vyberte možnost **dotazu** . Okno dotazu obsahuje seznam vstupů a výstupů, které jsou pro úlohu nakonfigurované, a umožňuje vytvořit dotaz pro transformaci vstupního streamu.

3. Nahraďte existující dotaz v editoru následujícím dotazem, který provede samoobslužné spojení na 5 sekund intervalu volání dat:

   ```sql
   SELECT System.Timestamp AS WindowEnd, COUNT(*) AS FraudulentCalls
   INTO "MyPBIoutput"
   FROM "CallStream" CS1 TIMESTAMP BY CallRecTime
   JOIN "CallStream" CS2 TIMESTAMP BY CallRecTime
   ON CS1.CallingIMSI = CS2.CallingIMSI
   AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5
   WHERE CS1.SwitchNum != CS2.SwitchNum
   GROUP BY TumblingWindow(Duration(second, 1))
   ```

   Chcete-li kontrolovat falešná volání, můžete data streamování automaticky připojit na základě hodnoty `CallRecTime`. Potom můžete vyhledat záznamy volání, kde hodnota `CallingIMSI` (původní číslo) je stejná, ale hodnota `SwitchNum` (země/oblast původu) se liší. Když použijete operaci JOIN s datovým proudem, musí spojení poskytnout určitá omezení, jak daleko může být srovnávací řádky odděleny v čase. Vzhledem k tomu, že streamovaná data jsou nekonečné, jsou časová omezení pro relaci určena v rámci klauzule **on** spojení pomocí funkce [DateDiff](https://docs.microsoft.com/stream-analytics-query/datediff-azure-stream-analytics) .

   Tento dotaz je stejně jako normální spojení SQL s výjimkou funkce **DateDiff** . Funkce **DateDiff** použitá v tomto dotazu je specifická pro Stream Analytics a musí se nacházet v klauzuli `ON...BETWEEN`.

4. **Uložte** dotaz.

   ![Definování Stream Analytics dotazů na portálu](media/stream-analytics-manage-job/define-stream-analytics-query.png)

## <a name="test-your-query"></a>Test dotazu

Dotaz můžete testovat z editoru dotazů pomocí ukázkových dat. Otestujte dotaz spuštěním následujících kroků:

1. Ujistěte se, že je aplikace TelcoGenerator spuštěná a vytváří záznamy telefonních hovorů.

2. V podokně **dotazu** vyberte tečky vedle vstupu *CallStream* a pak vyberte **vzorová data ze vstupu**.

3. Nastavte **minuty** na 3 a vyberte **OK**. Ze vstupního streamu se pak navzorkují data o třech minutách a vy budete upozorněni, až budou ukázková data připravená. Stav vzorkování můžete zobrazit z oznamovacího panelu.

   Ukázková data jsou dočasně uložená a jsou dostupná, když máte okno dotazu otevřené. Pokud zavřete okno dotazu, budou ukázková data zahozena a budete muset vytvořit novou sadu ukázkových dat, pokud chcete otestovat. Alternativně můžete použít soubor JSON s ukázkovými daty z [GitHubu](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/telco.json)a pak tento soubor JSON nahrát k použití jako vzorová data pro vstup *CallStream* .

   ![Vizuál, jak vzorkovat vstupní data pro Stream Analytics](media/stream-analytics-manage-job/sample-input-data-asa.png)

4. Vyberte **test** pro otestování dotazu. Měli byste vidět následující výsledky:

   ![Výstup z Stream Analytics testu dotazu](media/stream-analytics-manage-job/sample-test-output-restuls.png)

## <a name="start-the-job-and-visualize-output"></a>Spuštění úlohy a vizualizace výstupu

1. Chcete-li spustit úlohu, přejděte do podokna **Přehled** úlohy a vyberte možnost **Spustit**.

2. Vyberte **nyní** pro čas spuštění výstupu úlohy a vyberte **Spustit**. Stav úlohy můžete zobrazit v oznamovacím pruhu.

3. Po úspěšném dokončení úlohy přejděte na [Power BI](https://powerbi.com/) a přihlaste se pomocí svého pracovního nebo školního účtu. Pokud se Stream Analytics dotaz na úlohu předchází výsledků, datová sada *ASAdataset* , kterou jste vytvořili, existuje na kartě **datové sady** .

4. V pracovním prostoru Power BI vyberte **+ vytvořit** a vytvořte nový řídicí panel s názvem *podvodné volání*.

5. V horní části okna vyberte **Přidat dlaždici**. Pak vyberte **vlastní streamovaná data** a **Další**. Vyberte **ASAdataset** v rámci **vašich datových sad**. V rozevíracím seznamu **typ vizualizace** vyberte **karta** a přidejte **falešná volání** do **polí**. Vyberte **Další** a zadejte název dlaždice a potom vyberte **použít** pro vytvoření dlaždice.

   ![Vytvoření dlaždic řídicího panelu Power BI](media/stream-analytics-manage-job/create-power-bi-dashboard-tiles.png)

6. Postupujte podle kroku 5 znovu s následujícími možnostmi:
   * Po zobrazení typu vizualizace vyberte spojnicový graf.
   * Přidejte osu a vyberte **windowend**.
   * Přidejte hodnotu a vyberte **podvodnávolání**.
   * Pro **zobrazení časového okna**vyberte posledních 10 minut.

7. Po přidání obou dlaždic by měl řídicí panel vypadat jako v následujícím příkladu. Všimněte si, že pokud vaše aplikace pro odesílatele centra událostí a Stream Analytics běží, váš Power BI řídicí panel se pravidelně aktualizuje, protože dorazí na nová data.

   ![Zobrazení výsledků v Power BIovém řídicím panelu](media/stream-analytics-manage-job/power-bi-results-dashboard.png)

## <a name="embedding-your-power-bi-dashboard-in-a-web-application"></a>Vložení řídicího panelu Power BI do webové aplikace

V této části kurzu použijete ukázkovou webovou aplikaci [ASP.NET](https://asp.net/) vytvořenou týmem Power BI pro vložení vašeho řídicího panelu. Další informace o vkládání řídicích panelů najdete v článku [vkládání s Power BI](https://docs.microsoft.com/power-bi/developer/embedding) .

Pokud chcete nastavit aplikaci, přejděte do úložiště GitHubu [PowerBI-Developer-Samples](https://github.com/Microsoft/PowerBI-Developer-Samples) a postupujte podle pokynů v části **data, která vlastní uživatel** (použijte adresy URL přesměrování a domovské stránky v podčásti **Integration-Web-App** ). Vzhledem k tomu, že používáme příklad řídicího panelu, použijte vzorový kód **Integration-Web-App** umístěný v [úložišti GitHub](https://github.com/Microsoft/PowerBI-Developer-Samples/tree/master/User%20Owns%20Data/integrate-web-app).
Jakmile máte aplikaci spuštěnou v prohlížeči, postupujte podle těchto kroků a vložte řídicí panel, který jste vytvořili dříve, do webové stránky:

1. Vyberte možnost **Přihlásit se k Power BI**, která aplikaci udělí přístup k řídicím panelům v účtu Power BI.

2. Vyberte tlačítko **získat řídicí panely** , ve kterém se zobrazí řídicí panely vašeho účtu v tabulce. Vyhledejte název řídicího panelu, který jste vytvořili dříve, **PowerBI-Embedded-Dashboard**a zkopírujte odpovídající **EmbedUrl**.

3. Nakonec vložte **EmbedUrl** do příslušného textového pole a vyberte **Vložit řídicí panel**. Nyní můžete zobrazit stejný řídicí panel vložený v rámci webové aplikace.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili jednoduchou úlohu Stream Analytics, analyzovali příchozí data a výsledky zobrazíte v Power BIm řídicím panelu. Další informace o úlohách Stream Analytics najdete v dalším kurzu:

> [!div class="nextstepaction"]
> [Spuštění Azure Functions v rámci úlohy Stream Analytics](stream-analytics-with-azure-functions.md)
