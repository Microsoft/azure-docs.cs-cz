---
title: Integrace řídicího panelu Power BI s Azure Stream Analytics
description: Tento článek popisuje, jak pomocí řídicího panelu Power BI v reálném čase vizualizovat data z úlohy Azure Stream Analytics.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/05/2019
ms.openlocfilehash: 8466fbcb4325dc244551a3b84fc20581366b7071
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78851149"
---
# <a name="stream-analytics-and-power-bi-a-real-time-analytics-dashboard-for-streaming-data"></a>Stream Analytics a Power BI: Řídicí panel analýzy v reálném čase pro streamování dat

Azure Stream Analytics vám umožňuje využívat jeden z předních nástrojů business intelligence, [Microsoft Power BI](https://powerbi.com/). V tomto článku se dozvíte, jak vytvářet nástroje business intelligence pomocí Power BI jako výstupu pro úlohy Azure Stream Analytics. Dozvíte se také, jak vytvořit a používat řídicí panel v reálném čase.

Tento článek pokračuje z kurzu pro detekci podvodů streamanalytics [v reálném čase.](stream-analytics-real-time-fraud-detection.md) Vychází z pracovního postupu vytvořeného v tomto kurzu a přidává výstup Power BI, takže můžete vizualizovat podvodné telefonní hovory, které jsou zjištěny úlohou Streaming Analytics. 

Můžete sledovat [video,](https://www.youtube.com/watch?v=SGUpT-a99MA) které ilustruje tento scénář.


## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že jste provedli následující akce:

* Účet Azure.
* Účet pro Power BI Pro. Můžete použít pracovní účet nebo školní účet.
* Dokončená verze výukového programu [pro detekci podvodů](stream-analytics-real-time-fraud-detection.md) v reálném čase. Kurz obsahuje aplikaci, která generuje fiktivní metadata telefonního hovoru. V kurzu vytvoříte centrum událostí a odešlete data streamování telefonního hovoru do centra událostí. Napíšete dotaz, který detekuje podvodné volání (volání ze stejného čísla současně v různých umístěních). 


## <a name="add-power-bi-output"></a>Přidání výstupu Power BI
V kurzu detekce podvodů v reálném čase se výstup odesílá do úložiště objektů Blob Azure. V této části přidáte výstup, který odesílá informace do Power BI.

1. Na webu Azure Portal otevřete úlohu Streamování Analytics, kterou jste vytvořili dříve. Pokud jste použili navrhovaný název, `sa_frauddetection_job_demo`bude úloha pojmenována .

2. V levé nabídce vyberte **Výstupy** v části **Topologie úlohy**. Pak v rozevírací nabídce vyberte **+ Přidat** a zvolte **Power BI.**

3. Vyberte **+ Přidat** > **Power BI**. Potom ve formuláři vyplňte následující podrobnosti a vyberte **Autorizovat**:

   |**Nastavení**  |**Navrhovaná hodnota**  |
   |---------|---------|
   |Alias pro výstup  |  CallStream-PowerBI  |
   |Název datové sady  |   sa-datová sada  |
   |Název tabulky |  podvodné volání  |

   ![Konfigurace výstupu služby Stream Analytics](media/stream-analytics-power-bi-dashboard/configure-stream-analytics-output.png)

   > [!WARNING]
   > Pokud má Power BI datovou sadu a tabulku se stejnými názvy, jaké zadáte v úloze Stream Analytics, přepíší se ty stávající.
   > Doporučujeme, abyste tuto datovou sadu a tabulku ve svém účtu Power BI explicitně nevytvářeli. Automaticky se vytvoří, když spustíte úlohu Stream Analytics a úloha začne pumpovat výstup do Power BI. Pokud váš dotaz na úlohu nevrátí žádné výsledky, datová sada a tabulka nejsou vytvořeny.
   >

4. Když vyberete **Autorizovat**, otevře se místní okno s výzvou k zadání přihlašovacích údajů kvůli ověření vašeho účtu Power BI. Po úspěšné autorizaci uložte nastavení výběrem možnosti **Uložit**.

8. Klikněte na **Vytvořit**.

Datová sada je vytvořena s následujícím nastavením:

* **defaultRetentionPolicy: BasicFIFO** - Data jsou FIFO, s maximálně 200 000 řádky.
* **defaultMode: pushStreaming** – Datová sada podporuje jak streamované dlaždice, tak tradiční vizuály založené na sestavách (označované také jako push).

V současné době nelze vytvořit datové sady s jinými příznaky.

Další informace o datových sadách Power BI najdete v tématu Odkaz na [rozhraní API Power BI REST.](https://msdn.microsoft.com/library/mt203562.aspx)


## <a name="write-the-query"></a>Napsat dotaz

1. Zavřete okno **Výstupy** a vraťte se do pracovního kotouče.

2. Klikněte na pole **Dotaz.** 

3. Zadejte následující dotaz. Tento dotaz je podobný dotazu pro vlastní spojení, který jste vytvořili v kurzu pro zjišťování podvodů. Rozdíl je v tom, že tento dotaz`CallStream-PowerBI`odesílá výsledky do nového výstupu, který jste vytvořili ( ). 

    >[!NOTE]
    >Pokud jste nejmenovali `CallStream` vstup v kurzu pro zjišťování `CallStream` podvodů, nahraďte název v klauzulích **FROM** a **JOIN** v dotazu.

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

Tato část je volitelná, ale doporučená. 

1. Pokud aplikace TelcoStreaming momentálně není spuštěná, spusťte ji takto:

    * Otevřete příkazový řádek.
    * Přejděte do složky, kde jsou soubory telcogenerator.exe a upravené soubory telcodatagen.exe.config.
    * Spusťte následující příkaz:

       `telcodatagen.exe 1000 .2 2`

2. Na stránce **Dotaz** pro úlohu Stream Analytics klikněte `CallStream` na tečky vedle vstupu a potom vyberte **Ukázková data ze vstupu**.

3. Určete, že chcete tři minuty dat a klepněte na tlačítko **OK**. Počkejte, dokud se nezobrazí oznámení o dokončeném vzorkování dat.

4. Klikněte na **Testovat** a prohlédněte si výsledky.

## <a name="run-the-job"></a>Spuštění úlohy

1. Zkontrolujte, zda je spuštěná aplikace TelcoStreaming.

2. Přejděte na stránku **Přehled** pro úlohu Stream Analytics a vyberte **Start**.

    ![Spuštění úlohy Stream Analytics](./media/stream-analytics-power-bi-dashboard/stream-analytics-sa-job-start-output.png)

Vaše úloha Služby Streamování analytics začne v příchozím streamu hledat podvodné hovory. Úloha také vytvoří datovou sadu a tabulku v Power BI a začne jim odesílat data o podvodných hovorech.


## <a name="create-the-dashboard-in-power-bi"></a>Vytvoření řídicího panelu v Power BI

1. Přejděte na [Powerbi.com](https://powerbi.com) a přihlaste se pomocí pracovního nebo školního účtu. Pokud dotaz na pracovní pozici Stream Analytics vydělá výsledky, uvidíte, že vaše datová sada je již vytvořena:

    ![Umístění datové sady datových ze streamování v Power BI](./media/stream-analytics-power-bi-dashboard/stream-analytics-streaming-dataset.png)

2. V pracovním prostoru ** + &nbsp;** klikněte na Vytvořit .

    ![Tlačítko Vytvořit v pracovním prostoru Power BI](./media/stream-analytics-power-bi-dashboard/pbi-create-dashboard.png)

3. Vytvořte nový řídicí `Fraudulent Calls`panel a pojmenujte jej .

    ![Vytvoření řídicího panelu a jeho zadání názvu v pracovním prostoru Power BI](./media/stream-analytics-power-bi-dashboard/pbi-create-dashboard-name.png)

4. V horní části okna klikněte na **Přidat dlaždici**, vyberte **VLASTNÍ STREAMOVANÁ DATA**a potom klikněte na **Další**.

    ![Dlaždice vlastní datové sady datových proudů v Power BI](./media/stream-analytics-power-bi-dashboard/custom-streaming-data.png)

5. V části **VAŠE SADY DATSET**Vyberte datovou sadu a klepněte na tlačítko **Další**.

    ![Vaše datová sada datových proudů v Power BI](./media/stream-analytics-power-bi-dashboard/your-streaming-dataset.png)

6. V části **Typ vizualizace**vyberte **Karta**a potom v seznamu **Pole** vyberte **podvodná volání**.

    ![Podrobnosti vizualizace pro novou dlaždici](./media/stream-analytics-power-bi-dashboard/add-fraudulent-calls-tile.png)

7. Klikněte na **Další**.

8. Vyplňte podrobnosti dlaždice, jako je název a titulky.

    ![Název a titulky pro novou dlaždici](./media/stream-analytics-power-bi-dashboard/pbi-new-tile-details.png)

9. Klikněte na **Použít**.

    Nyní máte podvod pult!

    ![Počítadlo podvodů na řídicím panelu Power BI](./media/stream-analytics-power-bi-dashboard/power-bi-fraud-counter-tile.png)

8. Podle pokynů znovu přidejte dlaždici (počínaje krokem 4). Tentokrát postupujte takto:

    * Až se dostanete k **typu vizualizace**, vyberte **spojnicový graf**. 
    * Přidejte osu a vyberte **windowend**. 
    * Přidejte hodnotu a vyberte **podvodnávolání**.
    * Jako **časové okno pro zobrazení** vyberte posledních 10 minut.

      ![Vytvoření dlaždice pro spojnicový graf v Power BI](./media/stream-analytics-power-bi-dashboard/pbi-create-tile-line-chart.png)

9. Klepněte na **tlačítko Další**, přidejte název a titulky a klepněte na **tlačítko Použít**.

     Řídicí panel Power BI teď poskytuje dvě zobrazení dat o podvodných hovorech, jak bylo zjištěno v datových proudech dat.

     ![Panel Dokončený Power BI zobrazující dvě dlaždice pro podvodné hovory](./media/stream-analytics-power-bi-dashboard/pbi-dashboard-fraudulent-calls-finished.png)


## <a name="learn-more-about-power-bi"></a>Další informace o Power BI

Tento kurz ukazuje, jak vytvořit pouze několik druhů vizualizací pro datovou sadu. Power BI vám pomůže vytvořit další nástroje business intelligence pro vaši organizaci. Další nápady najdete v následujících zdrojích:

* Další příklad řídicího panelu Power BI nazvěte [video Začínáme s Power BI.](https://youtu.be/L-Z_6P56aas?t=1m58s)
* Další informace o konfiguraci výstupu úlohy Streaming Analytics v Power BI a používání skupin Power BI najdete v článku o [výstupech Stream Analytics](stream-analytics-define-outputs.md) v části [Power BI.](stream-analytics-define-outputs.md#power-bi) 
* Informace o používání Power BI obecně najdete [v tématu Řídicí panely v Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/).


## <a name="learn-about-limitations-and-best-practices"></a>Informace o omezeních a doporučených postupech
V současné době se Power BI dá volat zhruba jednou za sekundu. Streamování vizuálů podporuje pakety o 15 KB. Kromě toho streamování vizuálů senezdaří (ale push pokračuje v práci). Z důvodu těchto omezení se Power BI nejvíce přirozeně hodí k případům, kdy Azure Stream Analytics výrazně redukuje zatížení dat. Doporučujeme použít okno omílání nebo hopping okno zajistit, že nabízená data je maximálně jeden push za sekundu a že dotaz přistane v rámci požadavky na propustnost.

K výpočtu hodnoty můžete použít následující rovnici, která vám poskytne okno během několika sekund:

![Rovnice pro výpočet hodnoty, která dává okno v sekundách](./media/stream-analytics-power-bi-dashboard/compute-window-seconds-equation.png)  

Například:

* Máte 1 000 zařízení odesílajících data v intervalech jedné sekundy.
* Používáte sku Power BI Pro, která podporuje 1 000 000 řádků za hodinu.
* Chcete do Power BI publikovat průměrné množství dat na zařízení.

V důsledku toho se rovnice stane:

![Rovnice založená na příkladech kritérií](./media/stream-analytics-power-bi-dashboard/power-bi-example-equation.png)  

Vzhledem k této konfiguraci můžete původní dotaz změnit na následující:

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
Pokud se heslo od vytvoření nebo posledního ověření úlohy změnilo, musíte znovu ověřit svůj účet Power BI. Pokud je azure multifaktorové ověřování nakonfigurované ve vašem tenantovi Azure Active Directory (Azure AD), musíte taky každé dva týdny obnovit autorizaci Power BI. Pokud neobnovíte, můžete zobrazit příznaky, jako je například `Authenticate user error` nedostatek výstupu úlohy nebo v protokolech operace.

Podobně pokud úloha spustí po vypršení platnosti tokenu, dojde k chybě a úloha se nezdaří. Pokud chcete tento problém vyřešit, zastavte spuštěnou úlohu a přejděte na výstup Power BI. Chcete-li zabránit ztrátě dat, vyberte propojení **Obnovit autorizaci** a restartujte úlohu z posledního **zastaveného času**.

Po aktualizaci autorizace pomocí Power BI se v oblasti autorizace zobrazí zelená výstraha, která bude odrážet, že byl problém vyřešen.

## <a name="get-help"></a>Podpora
Další pomoc našlápneme na fórum [Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Další kroky
* [Úvod do Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k dotazovacímu jazyku Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Odkaz na rozhraní REST SLUŽBY Azure Stream Analytics Management REST](https://msdn.microsoft.com/library/azure/dn835031.aspx)
