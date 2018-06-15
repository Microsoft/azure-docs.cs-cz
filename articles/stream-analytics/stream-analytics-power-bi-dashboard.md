---
title: Integrace řídicí panel Power BI s Azure Stream Analytics
description: Tento článek popisuje, jak používat v reálném čase řídicí panel Power BI k vizualizaci dat mimo úlohu služby Azure Stream Analytics.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/27/2017
ms.openlocfilehash: e84903870110091d527e870600d9a67bdc9cc6e5
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
ms.locfileid: "31418450"
---
# <a name="tutorial-stream-analytics-and-power-bi-a-real-time-analytics-dashboard-for-streaming-data"></a>Kurz: Stream Analytics a Power BI: řídicí panel analýzy v reálném čase pro datový proud
Azure Stream Analytics můžete využít jeden z úvodní nástroje business intelligence [Microsoft Power BI](https://powerbi.com/). V tomto článku se dozvíte, jak vytvořit nástroje business intelligence pomocí Power BI jako výstup pro úlohy Azure Stream Analytics. Také zjistíte, jak vytvořit a použít v reálném čase řídicí panel.

Tento článek pokračuje ze služby Stream Analytics [odhalování podvodů v reálném čase](stream-analytics-real-time-fraud-detection.md) kurzu. Staví na pracovní postup vytvořený v tomto kurzu, přičemž přidá Power BI výstup, takže můžete vizualizovat podvodné telefonních hovorů, které byly zjištěny nástrojem úlohu streamování Analytics. 

Můžete sledovat [video](https://www.youtube.com/watch?v=SGUpT-a99MA) který znázorňuje tento scénář.


## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že máte následující:

* Účet Azure.
* Účet pro Power BI. Můžete použít účet pracovní nebo školní účet.
* Dokončené verzi [odhalování podvodů v reálném čase](stream-analytics-real-time-fraud-detection.md) kurzu. Tento kurz zahrnuje aplikaci, která generuje metadata fiktivní telefonní hovor. V tomto kurzu vytvoříte centra událostí a odesílání dat telefonního hovoru do centra událostí. Můžete vytvořit dotaz, který zjistí podvodné volání (volání ze stejného čísla ve stejnou dobu v různých umístěních). 


## <a name="add-power-bi-output"></a>Přidat výstup Power BI
V tomto kurzu zjišťování podvodů v reálném čase je výstup odeslán do úložiště objektů Blob Azure. V této části přidáte výstupu, který odesílá informace do Power BI.

1. Na portálu Azure otevřete úlohu streamování analýzy, kterou jste vytvořili dříve. Pokud jste použili navrhovaný název, má název úlohy `sa_frauddetection_job_demo`.

2. Vyberte **výstupy** uprostřed řídicím panelu úlohy a pak vyberte položku **+ přidat**.

3. Pro **Alias pro výstup**, zadejte `CallStream-PowerBI`. Můžete použít jiný název. Pokud tak učiníte, poznamenejte si, protože potřebujete název později. 

4. V části **jímky**, vyberte **Power BI**.

   ![Vytvoření výstupu pro Power BI](./media/stream-analytics-power-bi-dashboard/create-pbi-ouptut.png)

5. Klikněte na tlačítko **Autorizovat**.

    Otevře okno se kterém můžete zadat vaše přihlašovací údaje Azure pro pracovní nebo školní účet. 

    ![Zadejte přihlašovací údaje pro přístup k Power BI.](./media/stream-analytics-power-bi-dashboard/authorize-area.png)

6. Zadejte své přihlašovací údaje. Upozorňujeme, pak po zadání přihlašovacích údajů se udělíte oprávnění k úlohy streamování Analytics pro přístup k vaší oblasti Power BI.

7. Když se vrátíte na **nový výstupní** okno, zadejte následující informace:

    * **Skupina pracovního prostoru**: Vyberte pracovní prostor v klientovi služby Power BI, kde chcete vytvořit datová sada.
    * **Název datové sady**: Zadejte `sa-dataset`. Můžete použít jiný název. Pokud tak učiníte, poznamenejte si ho na později.
    * **Název tabulky**: Zadejte `fraudulent-calls`. Power BI výstup z úlohy Stream Analytics v současné době může mít pouze jednu tabulku v datové sadě.

    ![Pracovní prostor PBI](./media/stream-analytics-power-bi-dashboard/create-pbi-ouptut-with-dataset-table.png)

    > [!WARNING]
    > Pokud má Power BI datovou sadu a tabulky, které mají stejné názvy jako ty, které zadáte v úloze Stream Analytics, se přepíší existující.
    > Doporučujeme vám, že nevytvoříte explicitně tuto datovou sadu a tabulky ve vašem účtu Power BI. Se automaticky vytvoří při spuštění úlohy Stream Analytics a úloha spustí čerpací výstup do Power BI. Pokud úloha dotaz nevrátí žádné výsledky, datové sady a tabulky nejsou vytvořeny.
    >

8. Klikněte na možnost **Vytvořit**.

Datová sada je vytvořen s následujícími nastaveními:

* **defaultRetentionPolicy: BasicFIFO**: Data jsou FIFO, s maximálně 200 000 řádků.
* **Vlastnost defaultMode: pushStreaming**: datovou sadu (také známa jako podporuje streamování dlaždice a tradiční vizuální prvky založené na sestavy nabídka).

V současné době nelze vytvořit datové sady s další příznaky.

Další informace o datových sadách Power BI, najdete v článku [Power BI REST API](https://msdn.microsoft.com/library/mt203562.aspx) odkaz.


## <a name="write-the-query"></a>Dotaz zapište

1. Zavřít **výstupy** okno a vraťte se do okna úlohy.

2. Klikněte **dotazu** pole. 

3. Zadejte následující dotaz. Tento dotaz je podobná dotaz spojení sama na sebe, kterou jste vytvořili v tomto kurzu odhalování podvodů. Rozdílem je, že tento dotaz odešle výsledky do nové výstup, který jste vytvořili (`CallStream-PowerBI`). 

    >[!NOTE]
    >Pokud není název vstupu `CallStream` v tomto kurzu odhalování podvodů, nahraďte název pro `CallStream` v **FROM** a **připojení** klauzule v dotazu.

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

4. Klikněte na **Uložit**.


## <a name="test-the-query"></a>Otestujte dotaz
Tato část je volitelný, ale doporučujeme. 

1. Pokud aplikace TelcoStreaming není aktuálně spuštěná, spusťte ji pomocí následujících kroků:

    * Otevřete okno příkazového řádku.
    * Přejděte do složky, kde jsou telcogenerator.exe a telcodatagen.exe.config změněné soubory.
    * Spusťte následující příkaz:

            telcodatagen.exe 1000 .2 2

2. V **dotazu** okně klikněte na tlačítko se tečkami vedle `CallStream` vstup a pak vyberte **vzorová data ze vstupu**.

3. Určete, zda má tři minut za dat a klikněte na tlačítko **OK**. Počkejte, dokud se oznámení, že data odebírají vzorky.

4. Klikněte na tlačítko **Test** a zajistěte, aby vám výsledky.


## <a name="run-the-job"></a>Spustit úlohu

1. Ujistěte se, zda je spuštěna aplikace TelcoStreaming.

2. Zavřít **dotazu** okno.

3. V okně úlohy klikněte na tlačítko **spustit**.

    ![Spustit úlohu služby Stream Analytics](./media/stream-analytics-power-bi-dashboard/stream-analytics-sa-job-start-output.png)

Úlohu streamování Analytics spustí vyhledávání podvodné volání v příchozím datovém proudu. Úloha také vytvoří datovou sadu a tabulkou v Power BI a spustí odesílání dat o podvodné volání k nim.


## <a name="create-the-dashboard-in-power-bi"></a>Vytvořit řídicí panel v Power BI

1. Přejděte na [Powerbi.com](https://powerbi.com) a přihlaste se pomocí svého pracovního nebo školního účtu. Pokud dotaz úlohy Stream Analytics výstupy výsledků, zobrazí vaše datová sada je už vytvořený:

    ![Streamovanou datovou sadu v Power BI](./media/stream-analytics-power-bi-dashboard/streaming-dataset.png)

2. V pracovním prostoru, klikněte na tlačítko  **+ &nbsp;vytvořit**.

    ![Tlačítko vytvořit v pracovním prostoru Power BI](./media/stream-analytics-power-bi-dashboard/pbi-create-dashboard.png)

3. Vytvořit nový řídicí panel a pojmenujte ji `Fraudulent Calls`.

    ![Vytvořit řídicí panel a pojmenujte ho v prostoru Power BI](./media/stream-analytics-power-bi-dashboard/pbi-create-dashboard-name.png)

4. V horní části okna klikněte na tlačítko **přidat dlaždice**, vyberte **datových proudů vlastní**a potom klikněte na **Další**.

    ![Vlastní streamování datové sady](./media/stream-analytics-power-bi-dashboard/custom-streaming-data.png)

5. V části **si DATSETS**, vyberte datovou sadu a pak klikněte na tlačítko **Další**.

    ![Vaši streamovanou datovou sadu](./media/stream-analytics-power-bi-dashboard/your-streaming-dataset.png)

6. V části **typ vizualizace**, vyberte **karty**a potom v **pole** seznamu, vyberte **fraudulentcalls**.

    ![Vizualizace podrobnosti nové dlaždice.](./media/stream-analytics-power-bi-dashboard/add-fraud.png)

7. Klikněte na **Další**.

8. Zadejte podrobnosti o dlaždici jako nadpis a podnadpis.

    ![Nadpis a podnadpis nové dlaždice.](./media/stream-analytics-power-bi-dashboard/pbi-new-tile-details.png)

9. Klikněte na tlačítko **Použít**.

    Nyní máte čítač podvod!

    ![Čítač podvod](./media/stream-analytics-power-bi-dashboard/fraud-counter.png)

8. Postup znovu přidat dlaždici (počínaje krok 4). Tentokrát, postupujte takto:

    * Když dojde k **typ vizualizace**, vyberte **spojnicový graf**. 
    * Přidejte osy a vyberte **windowend**. 
    * Přidejte hodnotu a vyberte **fraudulentcalls**.
    * Pro **časový interval pro zobrazení**, vyberte posledních 10 minut.

    ![Vytvoření dlaždici spojnicový graf](./media/stream-analytics-power-bi-dashboard/pbi-create-tile-line-chart.png)

9. Klikněte na tlačítko **Další**, přidat nadpis a podnadpis a klikněte na **použít**.

    Řídicí panel Power BI teď umožňuje dvě zobrazení dat o podvodné volání jako v dat zjištěn.

    ![Dokončení zobrazující dvě dlaždice pro podvodné volání řídicí panel Power BI](./media/stream-analytics-power-bi-dashboard/pbi-dashboard-fraudulent-calls-finished.png)


## <a name="learn-more-about-power-bi"></a>Další informace o Power BI

Tento kurz ukazuje, jak vytvořit pouze několik druhů vizualizace pro datovou sadu. Power BI můžete vytvořit další nástroje business intelligence zákazníka pro vaši organizaci. Další nápady najdete v následujících materiálech:

* Další příklad řídicí panel Power BI, podívejte se [Začínáme s Power BI](https://youtu.be/L-Z_6P56aas?t=1m58s) videa.
* Další informace o konfiguraci streamování Analytics úlohy výstup do Power BI a používání skupin Power BI, zkontrolujte [Power BI](stream-analytics-define-outputs.md#power-bi) části [Stream Analytics výstupy](stream-analytics-define-outputs.md) článku. 
* Informace o používání Power BI obecně najdete v tématu [řídicí panely v Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/).


## <a name="learn-about-limitations-and-best-practices"></a>Další informace o omezení a doporučené postupy
V současné době Power BI je možné volat přibližně jednou za sekundu. Streamování vizuály podporu paketů 15 kb. Kromě toho selhání streamování vizuály (ale nabízené bude pořád fungovat,). Z důvodu tato omezení Power BI různě nejvíce přirozeně případech, kde Azure Stream Analytics nepodporuje snížení zatížení významné data. Doporučujeme, abyste pomocí Přeskakující okno nebo okno Hopping a ujistěte se, že datová oznámení jsou maximálně jeden nabízené za sekundu a že váš dotaz pojmenováváme v rámci požadavky na propustnost.

Vzorce můžete použít k výpočtu hodnoty umožnit okně aplikace v sekundách:

![Equation1](./media/stream-analytics-power-bi-dashboard/equation1.png)  

Příklad:

* Máte 1 000 zařízení odesílání dat v intervalech jednu sekundu.
* Používáte SKU Pro Power BI, který podporuje 1 000 000 řádků za hodinu.
* Chcete publikovat množství průměr dat na zařízení k Power BI.

V důsledku toho rovnici:

![Equation2](./media/stream-analytics-power-bi-dashboard/equation2.png)  

V této konfiguraci můžete změnit původní dotaz na následující:

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


### <a name="renew-authorization"></a>Obnovit autorizaci
Pokud došlo ke změně hesla vzhledem k tomu, že vaše úlohy vytvoření nebo poslední ověření, musíte k novému ověření svůj účet Power BI. Pokud Azure Multi-Factor Authentication nakonfigurován v klientovi služby Azure Active Directory (Azure AD), musíte taky obnovit Power BI autorizace každé dva týdny. Pokud neobnovíte, se může zobrazit příznaky například chybějících výstup úlohy nebo `Authenticate user error` v protokoly operací.

Podobně pokud úloha spustí po vypršení platnosti tokenu, dojde k chybě a úloha se nezdaří. Chcete-li vyřešit tento problém, přejděte na výstupu Power BI a zastavit úlohu, která je spuštěná. Aby nedošlo ke ztrátě dat, vyberte **obnovit autorizace** propojit a pak restartujte úlohu z **naposledy Zastaveno**.

Po aktualizaci autorizaci s Power BI, Zelená výstraha se zobrazí v oblasti autorizace tak, aby odrážela, že byl problém vyřešen.

## <a name="get-help"></a>Podpora
Pro další pomoc, vyzkoušejte naše [fórum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Další postup
* [Úvod do služby Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics query language – referenční informace](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referenční dokumentace Azure Stream Analytics Management REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)
