---
title: Kurz – analýza podvodných dat volání pomocí Azure Stream Analytics a vizualizace výsledků v Power BIm řídicím panelu
description: V tomto kurzu najdete ucelenou ukázku použití Azure Stream Analytics k analýze podvodných volání ve streamu telefonních hovorů.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: tutorial
ms.custom: contperf-fy21q2
ms.date: 12/17/2020
ms.openlocfilehash: 1e26159c07ca551a78ee2f83a0ca64779c60f7b4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98018866"
---
# <a name="tutorial-analyze-fraudulent-call-data-with-stream-analytics-and-visualize-results-in-power-bi-dashboard"></a>Kurz: analýza podvodných dat volání pomocí Stream Analytics a vizualizace výsledků v Power BI řídicím panelu

V tomto kurzu se dozvíte, jak analyzovat data telefonních hovorů pomocí Azure Stream Analytics. Data telefonního hovoru generovaná klientskou aplikací obsahují podvodné hovory, které jsou filtrovány pomocí úlohy Stream Analytics. Techniky z tohoto kurzu můžete použít pro jiné typy odhalování podvodů, jako je podvod kreditních karet nebo krádež identity.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vygenerujte ukázková data telefonního hovoru a odešlete je do Azure Event Hubs.
> * Vytvoření úlohy Stream Analytics
> * Nakonfigurujte vstup a výstup úlohy.
> * Definujte dotazy pro filtrování podvodných volání.
> * Otestujte a spusťte úlohu.
> * Vizualizujte výsledky v Power BI.

## <a name="prerequisites"></a>Předpoklady

Než začnete, ujistěte se, že jste dokončili následující kroky:

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/).
* Stáhněte si aplikaci pro generování událostí telefonních hovorů [TelcoGenerator.zip](https://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip) z webu Microsoft Download Center. Případně získejte zdrojový kód z [GitHubu](https://aka.ms/azure-stream-analytics-telcogenerator).
* Budete potřebovat účet Power BI.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se na [Azure Portal](https://portal.azure.com).

## <a name="create-an-azure-event-hub"></a>Vytvoření centra událostí Azure

Než bude Stream Analytics moct analyzovat datový proud podvodných volání, musí se data odeslat do Azure. V tomto kurzu budete data odesílat do Azure pomocí služby [Azure Event Hubs](../event-hubs/event-hubs-about.md).

Pomocí následujícího postupu vytvořte centrum událostí a odešlete do něj data volání:

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).
2. Vyberte **vytvořit prostředek**  >  **Internet věcí**  >  **Event Hubs**.

   ![Vytvoření centra událostí Azure na portálu](media/stream-analytics-real-time-fraud-detection/find-event-hub-resource.png)
3. Vyplňte podokno **vytvořit obor názvů** s následujícími hodnotami:

   |**Nastavení**  |**Navrhovaná hodnota** |**Popis**  |
   |---------|---------|---------|
   |Name     | asaTutorialEventHub        |  Jedinečný název pro identifikaci oboru názvů centra událostí.       |
   |Předplatné     |   \<Your subscription\>      |   Vyberte předplatné Azure, ve kterém chcete vytvořit centrum událostí.      |
   |Skupina prostředků     |   MyASADemoRG      |  Vyberte **Vytvořit nový** a zadejte název nové skupiny prostředků pro váš účet.       |
   |Umístění     |   USA – západ 2      |    Umístění, kde můžete nasadit obor názvů centra událostí.     |

4. Pro zbývající nastavení použijte výchozí možnosti a vyberte **zkontrolovat + vytvořit**. Pak vyberte **vytvořit** a zahajte nasazení.

   ![Vytvořit obor názvů centra událostí v Azure Portal](media/stream-analytics-real-time-fraud-detection/create-event-hub-namespace.png)

5. Po dokončení nasazení oboru názvů, přejít na **všechny prostředky** a najít *asaTutorialEventHub* v seznamu prostředků Azure. Vyberte *asaTutorialEventHub* a otevřete ji.

6. Dále vyberte **+ centrum událostí** a zadejte **název** centra událostí. Nastavte **počet oddílů** na 2.  Ve zbývajících nastaveních použijte výchozí možnosti a vyberte **vytvořit**. Potom počkejte na úspěšné dokončení nasazení.

   ![Konfigurace centra událostí v Azure Portal](media/stream-analytics-real-time-fraud-detection/create-event-hub-portal.png)

### <a name="grant-access-to-the-event-hub-and-get-a-connection-string"></a>Udělení přístupu k centru událostí a získání připojovacího řetězce

Aby mohla aplikace odesílat data do Azure Event Hubs, musí mít centrum událostí zásadu, která umožňuje přístup. Zásady přístupu vytváří připojovací řetězec, který obsahuje informace o autorizaci.

1. Přejděte do centra událostí, které jste vytvořili v předchozím kroku, a *MyEventHub*. V části **Nastavení** vyberte **Zásady sdíleného přístupu** a pak vyberte **+ Přidat**.

2. Pojmenujte zásadu **MyPolicy** a ujistěte se, že je zaškrtnutá možnost **Spravovat**. Potom vyberte **Vytvořit**.

   ![Vytvoření zásad sdíleného přístupu k centru událostí](media/stream-analytics-real-time-fraud-detection/create-event-hub-access-policy.png)

3. Jakmile se zásada vytvoří, vyberte název zásady a otevřete zásadu. Najděte **připojovací řetězec – primární klíč**. Vyberte tlačítko **Kopírovat** vedle připojovacího řetězce.

   ![Uložení připojovacího řetězce zásady sdíleného přístupu](media/stream-analytics-real-time-fraud-detection/save-connection-string.png)

4. Vložte připojovací řetězec do textového editoru. Tento připojovací řetězec budete potřebovat v další části.

   Připojovací řetězec vypadá takto:

   `Endpoint=sb://<Your event hub namespace>.servicebus.windows.net/;SharedAccessKeyName=<Your shared access policy name>;SharedAccessKey=<generated key>;EntityPath=<Your event hub name>`

   Všimněte si, že připojovací řetězec obsahuje více párů klíč-hodnota oddělené středníky: **Endpoint**, **SharedAccessKeyName**, **SharedAccessKey** a **EntityPath**.

## <a name="start-the-event-generator-application"></a>Spuštění aplikace generátoru událostí

Před spuštěním aplikace TelcoGenerator byste ji měli nakonfigurovat tak, aby odesílala data do služby Azure Event Hubs, kterou jste vytvořili dříve.

1. Extrahujte obsah souboru [TelcoGenerator.zip](https://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip).
2. Otevřete `TelcoGenerator\TelcoGenerator\telcodatagen.exe.config` soubor v textovém editoru podle vašeho výběru, kde je více než jeden `.config` soubor, a ujistěte se, že jste otevřeli ten správný.

3. Aktualizujte element `<appSettings>` v konfiguračním souboru následujícím způsobem:

   * Nastavte hodnotu klíče *EventHubName* na hodnotu EntityPath v připojovacím řetězci.
   * Nastavte hodnotu klíče *Microsoft.ServiceBus.ConnectionString* na připojovací řetězec bez hodnoty EntityPath. Nezapomeňte odebrat středník, který předchází hodnotu EntityPath.

4. Soubor uložte.

5. Dále otevřete příkazové okno a přejděte do složky, do které jste extrahovali aplikaci TelcoGenerator. Potom zadejte následující příkaz:

   ```cmd
   .\telcodatagen.exe 1000 0.2 2
   ```

   Tento příkaz má následující parametry:
   * Počet záznamů dat volání za hodinu.
   * Procento pravděpodobnosti podvodů – to znamená, jak často by měla aplikace simulovat podvodné volání. Hodnota 0,2 znamená, že přibližně 20 % záznamů volání bude falešných.
   * Doba v hodinách – počet hodin, po které by aplikace měla být spuštěná. Aplikaci můžete kdykoli zastavit ukončením procesu (**CTRL + C**) na příkazovém řádku.

   Po několika sekundách aplikace začne zobrazovat záznamy telefonních hovorů na obrazovce, když je odešle do centra událostí. Data telefonních hovorů obsahují následující pole:

   |**Záznam**  |**Definition**  |
   |---------|---------|
   |CallrecTime    |  Časové razítko pro počáteční čas volání.       |
   |SwitchNum     |  Telefonní ústředna použitá pro spojení volání. V tomto příkladu jsou přepínače řetězce reprezentující zemi nebo oblast původu (USA, Čína, Spojené království, Německo nebo Austrálie).       |
   |CallingNum     |  Telefonní číslo volajícího.       |
   |CallingIMSI     |  IMSI (International Mobile Subscriber Identity). Jedinečný identifikátor volajícího.       |
   |CalledNum     |   Telefonní číslo příjemce volání.      |
   |CalledIMSI     |  IMSI (International Mobile Subscriber Identity). Jedinečný identifikátor příjemce volání.       |

## <a name="create-a-stream-analytics-job"></a>Vytvoření úlohy Stream Analytics

Teď, když máte stream událostí volání, můžete vytvořit úlohu Stream Analytics, která načte data z centra událostí.

1. Pokud chcete vytvořit úlohu Stream Analytics, přejděte na web [Azure Portal](https://portal.azure.com/).

2. Vyberte **vytvořit prostředek** a vyhledejte **Stream Analytics úlohy**. Vyberte dlaždici **Stream Analytics úlohy** a vyberte * vytvořit * *.

3. Do formuláře **nové úlohy Stream Analytics** zadejte následující hodnoty:

   |**Nastavení**  |**Navrhovaná hodnota**  |**Popis**  |
   |---------|---------|---------|
   |Název úlohy     |  ASATutorial       |   Jedinečný název pro identifikaci oboru názvů centra událostí.      |
   |Předplatné    |  \<Your subscription\>   |   Vyberte předplatné Azure, ve kterém chcete vytvořit úlohu.       |
   |Skupina prostředků   |   MyASADemoRG      |   Vyberte **Použít existující** a zadejte název nové skupiny prostředků pro váš účet.      |
   |Umístění   |    USA – západ 2     |      Umístění, kde můžete nasadit úlohu. Doporučuje se umístit úlohu a centrum událostí do stejné oblasti, abyste dosáhli nejlepšího výkonu a abyste neplatili za přenos dat mezi oblastmi.      |
   |Hostitelské prostředí    | Cloud        |     Úlohy Stream Analytics můžete nasadit do cloudu nebo do hraničního zařízení. Cloud umožňuje nasazení do cloudu Azure a Edge umožňuje nasazení do zařízení IoT Edge.    |
   |Jednotky streamování     |    1       |      Jednotky streamování představují výpočetní prostředky nutné k provedení úlohy. Ve výchozím nastavení je tato hodnota nastavená na 1. Podrobnosti o škálování jednotek streamování najdete v článku věnovaném [principům a úpravám jednotek streamování](stream-analytics-streaming-unit-consumption.md).      |

4. Pro zbývající nastavení použijte výchozí možnosti, vyberte **vytvořit** a počkejte na úspěšné nasazení.

   ![Vytvoření úlohy Azure Stream Analytics](media/stream-analytics-real-time-fraud-detection/create-stream-analytics-job.png)

## <a name="configure-job-input"></a>Konfigurace vstupu úlohy

Dalším krokem je definování vstupního zdroje, ze kterého bude úloha číst data pomocí centra událostí, které jste vytvořili v předchozí části.

1. V Azure Portal otevřete stránku **všechny prostředky** a vyhledejte úlohu Stream Analytics *s názvem asatutorial* .

2. V části **topologie úlohy** Stream Analytics úlohy vyberte **vstupy**.

3. Vyberte **+ Přidat vstup streamu** a **Centrum událostí**. Vyplňte vstupní formulář s následujícími hodnotami:

   |**Nastavení**  |**Navrhovaná hodnota**  |**Popis**  |
   |---------|---------|---------|
   |Alias vstupu     |  CallStream       |  Zadejte popisný název, který bude identifikovat váš vstup. Vstupní alias může obsahovat jenom alfanumerické znaky, spojovníky a podtržítka a musí být dlouhý 3 až 63 znaků.       |
   |Předplatné    |   \<Your subscription\>      |   Vyberte předplatné Azure, ve kterém jste vytvořili centrum událostí. Centrum událostí může být ve stejném předplatném jako úloha Stream Analytics, ale i v jiném.       |
   |Obor názvů centra událostí    |  asaTutorialEventHub       |  Vyberte obor názvů centra událostí, který jste vytvořili v předchozí části. Všechny obory názvů centra událostí dostupné v aktuálním předplatném jsou uvedeny v rozevírací nabídce.       |
   |Název centra událostí    |   MyEventHub      |  Vyberte centrum událostí, které jste vytvořili v předchozí části. Všechna centra událostí dostupná v aktuálním předplatném jsou uvedena v rozevírací nabídce.       |
   |Název zásad centra událostí   |  MyPolicy       |  Vyberte zásady sdíleného přístupu k centru událostí, které jste vytvořili v předchozí části. Všechny zásady centra událostí dostupné v aktuálním předplatném jsou uvedeny v rozevírací nabídce.       |

4. Pro zbývající nastavení použijte výchozí možnosti a vyberte **Uložit**.

   ![Konfigurace vstupu Azure Stream Analytics](media/stream-analytics-real-time-fraud-detection/configure-stream-analytics-input.png)

## <a name="configure-job-output"></a>Konfigurace výstupu úlohy

Posledním krokem je definování výstupní jímky, kde úloha může zapisovat transformovaná data. V tomto kurzu data vypíšete a vizualizujete pomocí Power BI.

1. Z Azure Portal otevřete **všechny prostředky** a vyberte úlohu Stream Analytics *s názvem asatutorial* .

2. V části **topologie úlohy** Stream Analytics úlohy vyberte možnost **výstupy** .

3. Vyberte **+ Přidat**  >  **Power BI**. Potom vyberte **autorizovat** a postupujte podle výzev k ověření Power BI.

:::image type="content" source="media/stream-analytics-real-time-fraud-detection/authorize-power-bi.png" alt-text="tlačítko pro autorizaci pro Power BI":::

4. Vyplňte formulář výstupu s následujícími podrobnostmi a vyberte **Uložit**:

   |**Nastavení**  |**Navrhovaná hodnota**  |
   |---------|---------|
   |Alias pro výstup  |  MyPBIoutput  |
   |Pracovní prostor skupiny| Můj pracovní prostor |
   |Název datové sady  |   ASAdataset  |
   |Název tabulky |  ASATable  |
   | Režim ověřování | Token uživatele |

   ![Konfigurace výstupu Stream Analytics](media/stream-analytics-real-time-fraud-detection/configure-stream-analytics-output.png)

   V tomto kurzu se používá režim ověřování *tokenem uživatele* . Pokud chcete použít spravovanou identitu, přečtěte si téma [použití spravované identity k ověření Azure Stream Analytics úlohy pro Power BI](powerbi-output-managed-identity.md).

## <a name="create-queries-to-transform-real-time-data"></a>Vytváření dotazů pro transformaci dat v reálném čase

V tomto okamžiku máte nastavenou úlohu Stream Analytics pro čtení příchozího datového proudu. Dalším krokem je vytvoření dotazu, který analyzuje data v reálném čase. Dotazy používají jazyk podobný jazyku SQL, který obsahuje některá rozšíření specifická pro Stream Analytics.

V této části kurzu vytvoříte a otestujete několik dotazů, abyste se dozvěděli několik způsobů, jak transformovat vstupní datový proud k analýze. 

Dotazy, které tady vytvoříte, budou jenom zobrazovat transformovaná data na obrazovku. V pozdější části napíšete transformovaná data pro Power BI.

Další informace o tomto jazyce najdete v referenčních informacích k jazyku [Azure Stream Analytics dotazů](/stream-analytics-query/stream-analytics-query-language-reference).

### <a name="test-using-a-pass-through-query"></a>Testování pomocí předávacího dotazu

Pokud chcete archivovat každou událost, můžete použít předávací dotaz ke čtení všech polí v datové části události.

1. V Azure Portal přejděte na svou Stream Analytics úlohu a v části *topologie úlohy* vyberte **dotaz** . 

2. V okně dotazu zadejte tento dotaz:

   ```SQL
   SELECT 
       *
   FROM 
       CallStream
   ```

    >[!NOTE]
    >Stejně jako u jazyka SQL nejsou klíčová slova rozlišovat velká a malá písmena a prázdné znaky nejsou významné.

    V tomto dotazu `CallStream` je alias, který jste zadali při vytváření vstupu. Pokud jste použili jiný alias, použijte místo něj tento název.

3. Vyberte **dotaz testu**.

    Úloha Stream Analytics spouští dotaz na vzorová data ze vstupu a zobrazuje výstup v dolní části okna. Výsledky indikují, že centrum událostí a úloha analýzy streamování jsou správně nakonfigurované.

    :::image type="content" source="media/stream-analytics-real-time-fraud-detection/sample-output-passthrough.png" alt-text="Ukázkový výstup z testovacího dotazu":::

    Přesný počet záznamů, které vidíte, bude záviset na tom, kolik záznamů bylo v ukázce zachyceno.

### <a name="reduce-the-number-of-fields-using-a-column-projection"></a>Snížení počtu polí pomocí projekce sloupce

V mnoha případech vaše analýza nepotřebuje všechny sloupce ze vstupního streamu. Dotaz můžete použít pro projekt menší sady vrácených polí než v předávacím dotazu.

Spusťte následující dotaz a Všimněte si výstupu.

```SQL
SELECT CallRecTime, SwitchNum, CallingIMSI, CallingNumCalledNum 
FROM 
    CallStream
```

### <a name="count-incoming-calls-by-region-tumbling-window-with-aggregation"></a>Počet příchozích volání podle oblasti: okno bubnu s agregací

Předpokládejme, že chcete spočítat počet příchozích volání na oblast. V případě streamování dat, pokud chcete provádět agregační funkce, jako je počítání, potřebujete segmentovat datový proud na dočasné jednotky, protože samotný datový proud je efektivně nekonečné. Provedete to pomocí [funkce okna](stream-analytics-window-functions.md)Stream Analytics. Pak můžete pracovat s daty v tomto okně jako s jednotkou.

Pro tuto transformaci budete chtít sekvenci dočasných oken, která se nepřekrývají – každé okno bude mít diskrétní sadu dat, která můžete seskupit a agregovat. Tento typ okna se označuje jako *bubnové okno*. V rámci okna bubnu můžete získat počet příchozích volání seskupených podle `SwitchNum` , který představuje zemi nebo oblast, ve které volání vzniklo.

1. Do editoru dotazů vložte následující dotaz:

    ```SQL
    SELECT 
        System.Timestamp as WindowEnd, SwitchNum, COUNT(*) as CallCount 
    FROM
        CallStream TIMESTAMP BY CallRecTime 
    GROUP BY TUMBLINGWINDOW(s, 5), SwitchNum
    ```

    Tento dotaz používá `Timestamp By` klíčové slovo v `FROM` klauzuli k určení pole timestamp ve vstupním streamu, které se má použít k definování bubnového okna. V tomto případě okno rozdělí data do segmentů podle `CallRecTime` pole v každém záznamu. (Pokud není zadané žádné pole, bude operace oken používat čas, kdy každá událost dorazí do centra událostí. Viz část "doba doručení vs – čas použití aplikace" v tématu [referenční informace k jazyku pro dotaz Stream Analytics](/stream-analytics-query/stream-analytics-query-language-reference). 

    Projekce obsahuje `System.Timestamp` , který vrací časové razítko pro konec každého okna. 

    Chcete-li určit, že chcete použít bubnové okno, použijte funkci [TUMBLINGWINDOW](/stream-analytics-query/tumbling-window-azure-stream-analytics) v `GROUP BY` klauzuli. Ve funkci zadáte časovou jednotku (kdekoliv od sekundy do dne) a velikost okna (počet jednotek). V tomto příkladu se bubnové okno skládá z 5 sekund, takže pro každé 5 sekund se získá počet podle země/oblasti.

2. Vyberte **dotaz testu**. Ve výsledcích si všimněte, že časová razítka v rámci **WindowEnd** jsou v přírůstcích 5 sekund.

### <a name="detect-sim-fraud-using-a-self-join"></a>Zjišťování podvodů SIM pomocí automatického spojení

V tomto příkladu zvažte podvodné použití volání, která pocházejí od stejného uživatele, ale v různých umístěních do 5 sekund od sebe. Například stejný uživatel nemůže legitimně uskutečnit volání z USA a Austrálie současně.

Chcete-li tyto případy vyhledat, můžete k připojení ke streamu na základě hodnoty použít samoobslužné spojení dat streamování `CallRecTime` . Pak můžete vyhledat záznamy volání `CallingIMSI` , kde hodnota (původní číslo) je stejná, ale `SwitchNum` hodnota (země/oblast původu) není shodná.

Použijete-li spojení s datovými proudy, je nutné, aby spojení poskytovalo určité meze, jak daleko může být srovnávací řádky odděleny v čase. Jak bylo uvedeno dříve, streamovaná data jsou efektivně nekonečné. Časové meze pro relaci jsou zadány uvnitř `ON` klauzule JOIN pomocí `DATEDIFF` funkce. V takovém případě se spojení vychází z intervalu 5 sekund volání dat.

1. Do editoru dotazů vložte následující dotaz:

    ```SQL
    SELECT System.Timestamp AS WindowEnd, COUNT(*) AS FraudulentCalls
    INTO "MyPBIoutput"
    FROM "CallStream" CS1 TIMESTAMP BY CallRecTime
    JOIN "CallStream" CS2 TIMESTAMP BY CallRecTime
    ON CS1.CallingIMSI = CS2.CallingIMSI
    AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5
    WHERE CS1.SwitchNum != CS2.SwitchNum
    GROUP BY TumblingWindow(Duration(second, 1))
    ```

    Tento dotaz je stejný jako jakékoli spojení SQL s výjimkou `DATEDIFF` funkce ve spojení. Tato verze `DATEDIFF` je specifická pro Stream Analytics a musí se objevit v `ON...BETWEEN` klauzuli. Parametry jsou časovou jednotkou (sekundy v tomto příkladu) a aliasy dvou zdrojů pro spojení. To se liší od standardní funkce SQL `DATEDIFF` .

    `WHERE`Klauzule zahrnuje podmínku, která označuje podvodný hovor: původní přepínače nejsou stejné.

2. Vyberte **dotaz testu**. Zkontrolujte výstup a pak vyberte **Uložit dotaz**.

## <a name="start-the-job-and-visualize-output"></a>Spuštění úlohy a vizualizace výstupu

1. Chcete-li spustit úlohu, přejděte do **přehledu** úlohy a vyberte možnost **Spustit**.

2. Vyberte **Nyní** pro čas spuštění výstupu úlohy a vyberte **Spustit**. Stav úlohy můžete sledovat v oznamovacím pruhu.

3. Po úspěšném provedení úlohy přejděte do [Power BI](https://powerbi.com/) a přihlaste se pomocí svého pracovního nebo školního účtu. Pokud dotaz úlohy Stream Analytics vypisuje výsledky, na kartě **Datové sady** se zobrazí existující datová sada *ASAdataset*, kterou jste vytvořili.

4. V pracovním prostoru Power BI vyberte **+ Vytvořit** a vytvořte nový řídicí panel *Podvodná volání*.

5. V horní části okna vyberte **Upravit** a **Přidat dlaždici**. Potom vyberte **Vlastní streamovaná data** a **Další**. V části **Vaše datové sady** zvolte **ASAdataset**. V rozevíracím seznamu **typ vizualizace** vyberte **karta** a přidejte **falešná volání** do **polí**. Vyberte **Další**, zadejte název dlaždice a pak výběrem možnosti **Použít** dlaždici vytvořte.

   ![Vytvoření dlaždic řídicího panelu Power BI](media/stream-analytics-real-time-fraud-detection/create-power-bi-dashboard-tiles.png)

6. Zopakujte krok 5 s následujícími možnostmi:
   * Jako typ vizualizace vyberte spojnicový graf.
   * Přidejte osu a vyberte **windowend**.
   * Přidejte hodnotu a vyberte **podvodnávolání**.
   * Jako **časové okno pro zobrazení** vyberte posledních 10 minut.

7. Po přidání obou dlaždic by váš řídicí panel měl vypadat jako v následujícím příkladu. Všimněte si, že pokud vaše aplikace pro odesílatele centra událostí a Stream Analytics běží, váš Power BI řídicí panel se pravidelně aktualizuje, protože dorazí na nová data.

   ![Zobrazení výsledků v Power BIovém řídicím panelu](media/stream-analytics-real-time-fraud-detection/power-bi-results-dashboard.png)

## <a name="embedding-your-power-bi-dashboard-in-a-web-application"></a>Vložení řídicího panelu Power BI do webové aplikace

V této části kurzu použijete ukázkovou webovou aplikaci [ASP.NET](https://asp.net/) vytvořenou týmem Power BI pro vložení vašeho řídicího panelu. Další informace o vkládání řídicích panelů najdete v tématu [Vkládání pomocí Power BI](/power-bi/developer/embedding).

Pokud chcete nastavit aplikaci, přejděte do úložiště GitHubu [PowerBI-Developer-Samples](https://github.com/Microsoft/PowerBI-Developer-Samples) a postupujte podle pokynů v části **data, která vlastní uživatel** (použijte adresy URL přesměrování a domovské stránky v podčásti **Integration-Web-App** ). Vzhledem k tomu, že používáme příklad řídicího panelu, použijte vzorový kód **Integration-Web-App** umístěný v [úložišti GitHub](https://github.com/microsoft/PowerBI-Developer-Samples/tree/master/.NET%20Framework/Embed%20for%20your%20organization/).
Jakmile aplikace běží v prohlížeči, postupujte podle těchto kroků a vložte do webové stránky řídicí panel, který jste vytvořili dříve:

1. Vyberte možnost **Přihlásit se k Power BI**, která aplikaci udělí přístup k řídicím panelům v účtu Power BI.

2. Vyberte tlačítko **Získat řídicí panely**, které zobrazí tabulku s přehledem řídicích panelů ve vašem účtu. Vyhledejte název řídicího panelu **powerbi-embedded-dashboard**, který jste vytvořili dříve, a zkopírujte odpovídající hodnotu **EmbedUrl**.

3. Nakonec vložte hodnotu **EmbedUrl** do odpovídajícího textového pole a vyberte **Vložit řídicí panel**. Nyní můžete vidět stejný řídicí panel vložený do webové aplikace.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili jednoduchou úlohu Stream Analytics, analyzovali jste příchozí data a zobrazili výsledky na řídicím panelu Power BI. Další informace o úlohách Stream Analytics získáte v dalším kurzu:

> [!div class="nextstepaction"]
> [Spouštění Azure Functions z úloh Stream Analytics](stream-analytics-with-azure-functions.md)
