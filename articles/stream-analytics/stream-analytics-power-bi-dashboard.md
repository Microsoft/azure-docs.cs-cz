---
title: Integrace řídicího panelu Power BI s využitím Azure Stream Analytics
description: Tento článek popisuje, jak použít v reálném čase řídicí panel Power BI k vizualizaci dat mimo úlohu Azure Stream Analytics.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: c415bdecdaf55f3068dcd804ab34de402fe7a31f
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2019
ms.locfileid: "67612294"
---
# <a name="stream-analytics-and-power-bi-a-real-time-analytics-dashboard-for-streaming-data"></a>Stream Analytics a Power BI: Řídicí panel analýzy v reálném čase pro streamovaná data

Azure Stream Analytics umožňuje využít jeden z předních nástroje business intelligence, [Microsoft Power BI](https://powerbi.com/). V tomto článku se dozvíte, jak vytvořit nástroje business intelligence pomocí Power BI jako výstup pro úlohy Azure Stream Analytics. Také se dozvíte, jak vytvořit a použít řídicí panel v reálném čase.

Tento článek pokračuje ze Stream Analytics [zjišťování možných podvodů v reálném čase](stream-analytics-real-time-fraud-detection.md) kurzu. Pracovní postup vytvořený v tomto kurzu vychází a přidá Power BI výstup tak, aby můžete vizualizovat podvodnému telefonních hovorů, které rozpozná úlohu Stream Analytics. 

Můžete sledovat [video](https://www.youtube.com/watch?v=SGUpT-a99MA) , který ukazuje tento scénář.


## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že jste provedli následující akce:

* Účet Azure.
* Účet pro Power BI. Můžete použít účet pracovní nebo školní účet.
* Úplnou verzi [zjišťování možných podvodů v reálném čase](stream-analytics-real-time-fraud-detection.md) kurzu. Tento kurz zahrnuje aplikace, která generuje metadata fiktivní telefonní hovor. V tomto kurzu vytvořte Centrum událostí a datových proudů dat telefonních hovorů odesílat do centra událostí. Můžete napsat dotaz, který zjistí podvodná volání (volání z stejný počet současně v různých umístěních). 


## <a name="add-power-bi-output"></a>Přidat výstup Power BI
V tomto kurzu zjišťování možných podvodů v reálném čase je výstup odeslán do služby Azure Blob storage. V této části můžete přidat výstup, který odesílá informace do Power BI.

1. Na webu Azure Portal otevřete úlohu Stream Analytics, který jste vytvořili dříve. Pokud jste použili navrhovaný název, je název úlohy `sa_frauddetection_job_demo`.

2. V nabídce vlevo vyberte **výstupy** pod **topologie úlohy**. Vyberte **+ přidat** a zvolte **Power BI** z rozevírací nabídky.

3. Vyberte **+ Přidat** > **Power BI**. Potom ve formuláři vyplňte následující podrobnosti a vyberte **Autorizovat**:

   |**Nastavení**  |**Navrhovaná hodnota**  |
   |---------|---------|
   |Alias pro výstup  |  CallStream-PowerBI  |
   |Název datové sady  |   sa-dataset  |
   |Název tabulky |  fraudulent-calls  |

   ![Konfigurace výstupu Stream Analytics](media/stream-analytics-power-bi-dashboard/configure-stream-analytics-output.png)

   > [!WARNING]
   > Pokud Power BI obsahuje datovou sadu a tabulku, která mají stejné názvy jako ty, které zadáte v úloze Stream Analytics, jsou přepsat ty stávající.
   > Doporučujeme vám, že nevytvoříte explicitně touto datovou sadou a tabulka ve vašem účtu Power BI. Vytvoří se automaticky při spuštění vaší úlohy Stream Analytics a úloha spustí čerpací výstup do Power BI. Pokud vaše úloha dotaz nevrátí žádné výsledky, nevytvoří se datová sada a tabulky.
   >

4. Když vyberete **Autorizovat**, otevře se místní okno s výzvou k zadání přihlašovacích údajů kvůli ověření vašeho účtu Power BI. Po úspěšné autorizaci uložte nastavení výběrem možnosti **Uložit**.

8. Klikněte na možnost **Vytvořit**.

Datová sada se vytvoří s následujícím nastavením:

* **defaultRetentionPolicy: BasicFIFO** -Data jsou FIFO, s délkou maximálně 200 000 řádků.
* **defaultMode: pushStreaming** – datová sada podporuje dlaždice streamování a tradiční sestava založená vizuály (push).

V současné době nelze vytvořit datové sady s další příznaky.

Další informace o datových sad Power BI, najdete v článku [rozhraní REST API Power BI](https://msdn.microsoft.com/library/mt203562.aspx) odkaz.


## <a name="write-the-query"></a>Napsat dotaz

1. Zavřít **výstupy** okno a vraťte se do okna úlohy.

2. Klikněte na tlačítko **dotazu** pole. 

3. Zadejte následující dotaz. Tento dotaz je podobný dotaz spojení sama na sebe, který jste vytvořili v kurzu zjišťování možných podvodů. Rozdíl spočívá v tom, že tento dotaz odešle výsledky na nový výstup, který jste vytvořili (`CallStream-PowerBI`). 

    >[!NOTE]
    >Pokud není název vstupu `CallStream` v kurzu zjišťování možných podvodů, nahraďte název `CallStream` v **FROM** a **připojení** klauzule v dotazu.

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


## <a name="test-the-query"></a>Otestujte dotaz

Tato část je volitelný, ale doporučujeme. 

1. Pokud aplikace TelcoStreaming není aktuálně spuštěná, spusťte ji pomocí následujících kroků:

    * Otevřete příkazový řádek.
    * Přejděte do složky, kde jsou telcogenerator.exe a telcodatagen.exe.config upravené soubory.
    * Spusťte následující příkaz:

       `telcodatagen.exe 1000 .2 2`

2. Na **dotazu** stránce pro vaši úlohu Stream Analytics, klikněte na tečky vedle `CallStream` vstup a potom vyberte **ukázková data ze vstupu**.

3. Určete, jestli má tři minuty v hodnotě data a klikněte na tlačítko **OK**. Počkejte, dokud se nezobrazí oznámení o dokončeném vzorkování dat.

4. Klikněte na tlačítko **Test** a zkontrolovat výsledky kontroly.

## <a name="run-the-job"></a>Spuštění úlohy

1. Ujistěte se, že je spuštěná aplikace TelcoStreaming.

2. Přejděte **přehled** stránce pro vaši úlohu Stream Analytics a vyberte **Start**.

    ![Spuštění úlohy Stream Analytics](./media/stream-analytics-power-bi-dashboard/stream-analytics-sa-job-start-output.png)

Úlohy Stream Analytics začíná hledání podvodných volání v příchozím datovém proudu. Tato úloha také vytvoří datová sada a tabulky v Power BI a spustí odesílání dat o podvodných volání k nim.


## <a name="create-the-dashboard-in-power-bi"></a>Vytvoření řídicího panelu v Power BI

1. Přejděte na [Powerbi.com](https://powerbi.com) a přihlaste se pomocí svého pracovního nebo školního účtu. Pokud dotaz úlohy Stream Analytics odeslal výstupní výsledky, uvidíte, že vaše datová sada je již vytvořili:

    ![Streamování umístění datové sady v Power BI](./media/stream-analytics-power-bi-dashboard/stream-analytics-streaming-dataset.png)

2. V pracovním prostoru, klikněte na tlačítko  **+ &nbsp;vytvořit**.

    ![Tlačítko vytvořit v pracovním prostoru Power BI](./media/stream-analytics-power-bi-dashboard/pbi-create-dashboard.png)

3. Vytvořit nový řídicí panel a pojmenovat ho `Fraudulent Calls`.

    ![Vytvořte řídicí panel a pojmenujte ho v pracovním prostoru Power BI](./media/stream-analytics-power-bi-dashboard/pbi-create-dashboard-name.png)

4. V horní části okna klikněte na tlačítko **přidat dlaždici**vyberte **vlastní STREAMOVANÁ DATA**a potom klikněte na tlačítko **Další**.

    ![Vlastní streamovaná datová sada dlaždice v Power BI](./media/stream-analytics-power-bi-dashboard/custom-streaming-data.png)

5. V části **YOUR DATSETS**vyberte vaši datovou sadu a pak klikněte na **Další**.

    ![Datové sady streamování v Power BI](./media/stream-analytics-power-bi-dashboard/your-streaming-dataset.png)

6. V části **typ vizualizace**vyberte **karty**a pak v **pole** seznamu vyberte **podvodnávolání**.

    ![Vizualizace podrobnosti nové dlaždice.](./media/stream-analytics-power-bi-dashboard/add-fraudulent-calls-tile.png)

7. Klikněte na **Další**.

8. Vyplňte podrobnosti o dlaždici jako nadpis a podnadpis.

    ![Nadpis a podnadpis nové dlaždice.](./media/stream-analytics-power-bi-dashboard/pbi-new-tile-details.png)

9. Klikněte na tlačítko **Použít**.

    Nyní máte čítač podvod.

    ![Čítač možných podvodů v řídicím panelu Power BI](./media/stream-analytics-power-bi-dashboard/power-bi-fraud-counter-tile.png)

8. Postupujte podle kroků znovu přidejte dlaždici (od kroku 4). Tentokrát, postupujte takto:

    * Pokud získáte **typ vizualizace**vyberte **spojnicový graf**. 
    * Přidejte osu a vyberte **windowend**. 
    * Přidejte hodnotu a vyberte **podvodnávolání**.
    * Jako **časové okno pro zobrazení** vyberte posledních 10 minut.

      ![Vytvoření dlaždice pro spojnicový graf v Power BI](./media/stream-analytics-power-bi-dashboard/pbi-create-tile-line-chart.png)

9. Klikněte na tlačítko **Další**, přidejte nadpis a podnadpis a klikněte na tlačítko **použít**.

     Řídicí panel Power BI teď nabízí dvě zobrazení dat o podvodných volání zjištěné v streamovaná data.

     ![Dokončení zobrazující dvě dlaždice podvodných volání řídicí panel Power BI](./media/stream-analytics-power-bi-dashboard/pbi-dashboard-fraudulent-calls-finished.png)


## <a name="learn-more-about-power-bi"></a>Další informace o Power BI

Tento kurz ukazuje, jak vytvořit několik typů vizualizace pro datovou sadu. Power BI můžete vytvořit dalšími nástroji business intelligence zákazníka pro vaši organizaci. Další informace naleznete v následujících zdrojích:

* Další příklad řídicího panelu Power BI, podívejte se [Začínáme s Power BI](https://youtu.be/L-Z_6P56aas?t=1m58s) videa.
* Další informace o konfiguraci Stream Analytics úlohy výstup do Power BI a používání skupin v Power BI, přečtěte si [Power BI](stream-analytics-define-outputs.md#power-bi) část [výstupy Stream Analytics](stream-analytics-define-outputs.md) článku. 
* Informace o používání Power BI obvykle najdete v tématu [řídicí panely v Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/).


## <a name="learn-about-limitations-and-best-practices"></a>Další informace o omezeních a osvědčených postupech
V současné době Power BI je možné volat přibližně jednou za sekundu. Vizuály podporují pakety 15 kb. Kromě toho při selhání převzít vizuály (ale nabízených i nadále fungovat). Kvůli těmto omezením, Power BI nejvíce přirozeně se propůjčuje případy, kde Azure Stream Analytics se významné datové snížení zatížení. Doporučujeme použít aktivační událost pro Přeskakující okno nebo Hopping okno zajistit, že datová oznámení jsou maximálně jeden nabízených oznámení za sekundu a že váš dotaz jsou v rámci požadavkům na propustnost.

Do následující rovnice slouží k výpočtu hodnoty poskytnout okno během několika sekund:

![Rovnice vypočítat hodnotu okno v řádu sekund](./media/stream-analytics-power-bi-dashboard/compute-window-seconds-equation.png)  

Příklad:

* Máte 1 000 zařízení, odesílání dat v sekundových intervalech.
* Používání Power BI Pro jednotky SKU, podporuje 1 000 000 řádků za hodinu.
* Chcete publikovat do Power BI množství průměr dat na zařízení.

V důsledku toho rovnici:

![Na základě příkladu kritérií rovnice](./media/stream-analytics-power-bi-dashboard/power-bi-example-equation.png)  

V této konfiguraci můžete změnit původní dotaz takto:

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
Pokud došlo ke změně hesla od úlohy byl vytvořen nebo poslední ověření, budete muset donutit vašeho účtu Power BI. Pokud Azure Multi-Factor Authentication je nakonfigurován na tenantovi služby Azure Active Directory (Azure AD), musíte také obnovit autorizaci Power BI každé dva týdny. Pokud neobnovíte, je možné, uvidíte příznaky, jako je například chybějící výstup úlohy nebo `Authenticate user error` v protokoly operací.

Podobně pokud se úloha spustí po vypršení platnosti tokenu, dojde k chybě a úloha se nezdaří. Chcete-li tento problém vyřešit, po zastavení úlohy, na kterém běží a přejděte na výstup do Power BI. Aby nedošlo ke ztrátě dat, vyberte **obnovit autorizaci** propojit a potom restartujte úlohu z **posledním zastavení**.

Po povolení byl aktualizován s Power BI, zelenou výstraha se zobrazí v oblasti autorizace tak, aby odrážely, že byl problém vyřešen.

## <a name="get-help"></a>Podpora
Potřebujete další pomoc, vyzkoušejte naše [fóru Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Další postup
* [Úvod do služby Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční dokumentace jazyka dotazu Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referenční informace k Azure rozhraní REST API správy Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
