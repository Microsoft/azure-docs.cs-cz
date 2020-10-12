---
title: Integrace řídicího panelu Power BI s Azure Stream Analytics
description: Tento článek popisuje, jak pomocí řídicího panelu Power BI v reálném čase vizualizovat data z Azure Stream Analytics úlohy.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 8/6/2020
ms.openlocfilehash: 4c6d1d3877629150493ee2a57a04573760d2772a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88870013"
---
# <a name="stream-analytics-and-power-bi-a-real-time-analytics-dashboard-for-streaming-data"></a>Stream Analytics a Power BI: řídicí panel analýzy v reálném čase pro streamovaná data

Azure Stream Analytics vám umožní využít jeden z špičkových business intelligence nástrojů, [Power BI Microsoftu](https://powerbi.com/). V tomto článku se naučíte, jak vytvářet business intelligence nástroje pomocí Power BI jako výstup pro Azure Stream Analytics úlohy. Naučíte se také, jak vytvořit a používat řídicí panel v reálném čase.

Tento článek pokračuje v kurzu Stream Analytics [rozpoznávání podvodů v reálném čase](stream-analytics-real-time-fraud-detection.md) . Vytvoří se v pracovním postupu vytvořeném v tomto kurzu a přidá výstup Power BI, abyste mohli vizualizovat podvodné telefonní hovory zjištěné úlohou Stream Analytics. 

Můžete se podívat [na video](https://www.youtube.com/watch?v=SGUpT-a99MA)  , které znázorňuje tento scénář.


## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že jste provedli následující akce:

* Účet Azure:
* Účet pro Power BI Pro. Můžete použít pracovní účet nebo školní účet.
* Dokončená verze kurzu [zjišťování podvodů v reálném čase](stream-analytics-real-time-fraud-detection.md) . Kurz obsahuje aplikaci, která generuje fiktivní metadata telefonního hovoru. V tomto kurzu vytvoříte centrum událostí a odešlete data o telefonních hovorech streamování do centra událostí. Napíšete dotaz, který detekuje falešná volání (volání ze stejného čísla současně v různých umístěních). 


## <a name="add-power-bi-output"></a>Přidat výstup Power BI
V kurzu zjišťování podvodů v reálném čase se výstup pošle do úložiště objektů BLOB v Azure. V této části přidáte výstup, který odesílá informace do Power BI.

1. V Azure Portal otevřete úlohu Stream Analytics, kterou jste vytvořili dříve. Pokud jste použili navrhovaný název, úloha se jmenuje `sa_frauddetection_job_demo` .

2. V nabídce vlevo vyberte v části **topologie úlohy**možnost **výstupy** . Pak vyberte **+ Přidat** a v rozevírací nabídce vyberte **Power BI** .

3. Vyberte **+ Přidat**  >  **Power BI**. Pak vyplňte formulář následujícími podrobnostmi a vyberte **autorizovat** , abyste pro připojení k Power BI používali vlastní identitu uživatele (token je platný po 90 dnech). 

>[!NOTE]
>Pro produkční úlohy doporučujeme, abyste se připojili k [použití spravované identity k ověření Azure Stream Analytics úlohy Power BI](https://docs.microsoft.com/azure/stream-analytics/powerbi-output-managed-identity).

   |**Nastavení**  |**Navrhovaná hodnota**  |
   |---------|---------|
   |Alias pro výstup  |  CallStream-PowerBI  |
   |Název datové sady  |   SA – datová sada  |
   |Název tabulky |  podvodné – volání  |

   ![Konfigurace výstupu Stream Analytics](media/stream-analytics-power-bi-dashboard/configure-stream-analytics-output.png)

   > [!WARNING]
   > Pokud Power BI obsahuje datovou sadu a tabulku, které mají stejné názvy jako ty, které zadáte v úloze Stream Analytics, existující objekty se přepíší.
   > Doporučujeme, abyste tuto datovou sadu a tabulku nevytvořili explicitně v účtu Power BI. Automaticky se vytvoří při spuštění úlohy Stream Analytics a úloha začne vyčerpat výstup do Power BI. Pokud dotaz na úlohu nevrátí žádné výsledky, datová sada a tabulka se nevytvoří.
   >

4. Když vyberete **Autorizovat**, otevře se místní okno s výzvou k zadání přihlašovacích údajů kvůli ověření vašeho účtu Power BI. Po úspěšné autorizaci uložte nastavení výběrem možnosti **Uložit**.

8. Klikněte na **Vytvořit**.

Datová sada je vytvořena s následujícími nastaveními:

* **defaultRetentionPolicy: BasicFIFO** -data jsou FIFO s maximálně 200 000 řádky.
* **defaultMode: hodnotu pushstreaming** – datová sada podporuje jak streamování, tak tradiční vizuály založené na sestavách (označované také jako push).

V současné době nemůžete vytvořit datové sady s jinými příznaky.

Další informace o Power BI datových sadách naleznete v tématu [Power BI REST API](https://msdn.microsoft.com/library/mt203562.aspx) reference.


## <a name="write-the-query"></a>Zapsat dotaz

1. Zavřete okno **výstupy** a vraťte se do okna úlohy.

2. Klikněte na pole **dotazu** . 

3. Zadejte následující dotaz. Tento dotaz je podobný dotazu, který jste vytvořili v kurzu pro detekci podvodů. Rozdílem je, že tento dotaz odesílá výsledky do nového výstupu, který jste vytvořili ( `CallStream-PowerBI` ). 

    >[!NOTE]
    >Pokud jste nezaznamenali vstup `CallStream` v kurzu pro detekci podvodů, nahraďte své jméno `CallStream` v klauzulích **from** a **Join** v dotazu.

   ```SQL
   /* Our criteria for fraud:
   Calls made from the same caller to two phone switches in different locations (for example, Australia and Europe) within five seconds */

   SELECT System.Timestamp AS WindowEnd, COUNT(*) AS FraudulentCalls
   INTO "CallStream-PowerBI"
   FROM "CallStream" CS1 TIMESTAMP BY CallRecTime
   JOIN "CallStream" CS2 TIMESTAMP BY CallRecTime

   /* Where the caller is the same, as indicated by IMSI (International Mobile Subscriber Identity) */
   ON CS1.CallingIMSI = CS2.CallingIMSI

   /* ...and date between CS1 and CS2 is between one and five seconds */
   AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5

   /* Where the switch location is different */
   WHERE CS1.SwitchNum != CS2.SwitchNum
   GROUP BY TumblingWindow(Duration(second, 1))
   ```

4. Klikněte na **Uložit**.


## <a name="test-the-query"></a>Testování dotazu

Tato část je volitelná, ale doporučuje se. 

1. Pokud aplikace TelcoStreaming aktuálně není spuštěná, spusťte ji pomocí následujících kroků:

    * Otevřete příkazový řádek.
    * Přejde do složky, kde jsou telcogenerator.exe a změněné soubory telcodatagen.exe.config.
    * Spusťte následující příkaz:

       `telcodatagen.exe 1000 .2 2`

2. Na stránce s **dotazem** pro vaši úlohu Stream Analytics klikněte na tečky vedle `CallStream` vstupu a pak vyberte **vzorová data ze vstupu**.

3. Zadejte, že chcete mít data pro tři minuty a klikněte na **OK**. Počkejte, dokud se nezobrazí oznámení o dokončeném vzorkování dat.

4. Klikněte na **test** a Prohlédněte si výsledky.

## <a name="run-the-job"></a>Spuštění úlohy

1. Ujistěte se, že je aplikace TelcoStreaming spuštěná.

2. Přejděte na stránku **Přehled** pro vaši úlohu Stream Analytics a vyberte **Spustit**.

    ![Spustit úlohu Stream Analytics](./media/stream-analytics-power-bi-dashboard/stream-analytics-sa-job-start-output.png)

Vaše úloha Stream Analytics začne hledat falešná volání v příchozím datovém proudu. Úloha také vytvoří datovou sadu a tabulku v Power BI a začne odesílat data o podvodných voláních.


## <a name="create-the-dashboard-in-power-bi"></a>Vytvoření řídicího panelu v Power BI

1. Přejít na [PowerBI.com](https://powerbi.com) a přihlaste se pomocí svého pracovního nebo školního účtu. Pokud dotaz Stream Analytics úlohy vypíše výsledky, vidíte, že vaše datová sada je už vytvořená:

    ![Umístění datové sady streamování v Power BI](./media/stream-analytics-power-bi-dashboard/stream-analytics-streaming-dataset.png)

2. V pracovním prostoru klikněte na ** + &nbsp; vytvořit**.

    ![Tlačítko vytvořit v pracovním prostoru Power BI](./media/stream-analytics-power-bi-dashboard/pbi-create-dashboard.png)

3. Vytvořte nový řídicí panel a pojmenujte ho `Fraudulent Calls` .

    ![Vytvořte řídicí panel a pojmenujte ho v Power BI pracovním prostoru.](./media/stream-analytics-power-bi-dashboard/pbi-create-dashboard-name.png)

4. V horní části okna klikněte na **Přidat dlaždici**, vyberte **vlastní streamovaná data**a pak klikněte na **Další**.

    ![Dlaždice vlastní datové sady streamování v Power BI](./media/stream-analytics-power-bi-dashboard/custom-streaming-data.png)

5. V části **DATSETS**vyberte datovou sadu a klikněte na **Další**.

    ![Datová sada streamování v Power BI](./media/stream-analytics-power-bi-dashboard/your-streaming-dataset.png)

6. V části **typ vizualizace**vyberte **karta**a potom v seznamu **pole** vyberte **podvodnávolání**.

    ![Podrobnosti vizualizace pro novou dlaždici](./media/stream-analytics-power-bi-dashboard/add-fraudulent-calls-tile.png)

7. Klikněte na **Next** (Další).

8. Vyplňte podrobnosti dlaždice jako název a podnadpis.

    ![Nadpis a podnadpis pro novou dlaždici](./media/stream-analytics-power-bi-dashboard/pbi-new-tile-details.png)

9. Klikněte na **Použít**.

    Nyní máte počítadlo podvodů.

    ![Čítač podvodů v Power BIovém řídicím panelu](./media/stream-analytics-power-bi-dashboard/power-bi-fraud-counter-tile.png)

8. Pomocí kroků znovu přidejte dlaždici (počínaje krokem 4). Tentokrát postupujte následovně:

    * Po zobrazení **typu vizualizace**vyberte **Spojnicový graf**. 
    * Přidejte osu a vyberte **windowend**. 
    * Přidejte hodnotu a vyberte **podvodnávolání**.
    * Jako **časové okno pro zobrazení** vyberte posledních 10 minut.

      ![Vytvořit dlaždici pro spojnicový graf v Power BI](./media/stream-analytics-power-bi-dashboard/pbi-create-tile-line-chart.png)

9. Klikněte na **Další**, přidejte název a podnadpis a klikněte na **použít**.

     Řídicí panel Power BI nyní poskytuje dvě zobrazení dat o podvodných voláních, která byla zjištěna v datech streamování.

     ![Dokončený Power BI řídicí panel ukazující dvě dlaždice pro podvodné hovory](./media/stream-analytics-power-bi-dashboard/pbi-dashboard-fraudulent-calls-finished.png)

## <a name="learn-about-limitations-and-best-practices"></a>Další informace o omezeních a osvědčených postupech
V současné době je možné Power BI volat zhruba jednou za sekundu. Vizuály streamování podporují pakety o 15 KB. Kromě toho se vizuály streamování nezdaří (ale i nadále funguje). Z důvodu těchto omezení Power BI sami přirozeně zapůjčení do případů, kdy Azure Stream Analytics významným omezením zatížení dat. Doporučujeme použít bubnové okno nebo skákající okno, aby bylo zajištěno, že nabízení dat je nejvýše jedna nabízená oznámení za sekundu a že váš dotaz je v rámci požadavků na propustnost.

Pomocí následující rovnice můžete vypočítat hodnotu a poskytnout tak oknu během několika sekund:

![Rovnice k výpočtu hodnoty za účelem poskytnutí okna v sekundách](./media/stream-analytics-power-bi-dashboard/compute-window-seconds-equation.png)  

Například:

* Máte 1 000 zařízení odesílajících data v sekundách za sekundu.
* Používáte Power BI Pro SKU, který podporuje 1 000 000 řádků za hodinu.
* Chcete publikovat množství průměrných dat na zařízení Power BI.

Výsledkem je, že se rovnice bude:

![Rovnice založená na ukázkových kritériích](./media/stream-analytics-power-bi-dashboard/power-bi-example-equation.png)  

Tato konfigurace umožňuje změnit původní dotaz na následující:

```SQL
    SELECT
        MAX(hmdt) AS hmdt,
        MAX(temp) AS temp,
        System.TimeStamp AS time,
        dspl
    INTO "CallStream-PowerBI"
    FROM
        Input TIMESTAMP BY time
    GROUP BY
        TUMBLINGWINDOW(ss,4),
        dspl
```

### <a name="renew-authorization"></a>Obnovit autorizaci
Pokud se heslo od vytvoření nebo posledního ověření úlohy změnilo, budete muset účet Power BI znovu ověřit. Pokud je Azure Multi-Factor Authentication nakonfigurovaný v tenantovi Azure Active Directory (Azure AD), musíte také obnovit Power BI autorizaci každé dva týdny. Pokud obnovení neprovedete, můžete se podívat na příznaky, jako je například nedostatek výstupu úlohy nebo `Authenticate user error` v protokolech operací.

Podobně platí, že pokud se úloha spustí po vypršení platnosti tokenu, dojde k chybě a úloha se nezdařila. Pokud chcete tento problém vyřešit, zastavte úlohu, která běží, a přejít na výstup Power BI. Aby nedošlo ke ztrátě dat, vyberte odkaz **obnovit autorizaci** a potom restartujte úlohu z **posledního pozastaveného času**.

Po obnovení autorizace pomocí Power BI se v oblasti autorizace zobrazí Zelená výstraha, která odráží, že byl problém vyřešen.

## <a name="next-steps"></a>Další kroky
* [Úvod do Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Výstupy Stream Analytics](stream-analytics-define-outputs.md)
* [Referenční dokumentace jazyka Azure Stream Analytics dotazů](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referenční informace o REST API správy Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
* [Použití spravované identity k ověření Azure Stream Analytics úlohy pro Power BI](https://docs.microsoft.com/azure/stream-analytics/powerbi-output-managed-identity)
