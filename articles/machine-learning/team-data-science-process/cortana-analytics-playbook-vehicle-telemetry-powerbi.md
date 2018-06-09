---
title: Řídicí panel Power BI pro vehicle stavu a řídí zvyklosti - Azure | Microsoft Docs
description: Pomocí možnosti Cortana Intelligence získat přehledy v reálném čase a prediktivní na vehicle stavu a řídí zvyklosti.
services: machine-learning
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: aaeb29a5-4a13-4eab-bbf1-885690d86c56
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2018
ms.author: deguhath
ms.openlocfilehash: 8544c400fdb7d4f00a4946aba321fec3568d024a
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/08/2018
ms.locfileid: "35248448"
---
# <a name="vehicle-telemetry-analytics-solution-template-power-bi-dashboard-setup-instructions"></a>Pokyny k instalaci vehicle Telemetrie Analytics řešení šablony Power BI řídicí panel
Tato nabídka odkazy na kapitoly v této playbook: 

[!INCLUDE [cap-vehicle-telemetry-playbook-selector](../../../includes/cap-vehicle-telemetry-playbook-selector.md)]

Server Showcase řešení analýzy Telemetrie Vehicle jak dealerům car, automobilů výrobců a pojištění společnosti, se můžou využívat možnosti Cortana Intelligence. Jej mohou získat přehledy v reálném čase a prediktivní na vehicle stavu a přenášení zvyklosti k vylepšení zkušeností zákazníků, výzkum a vývoj a marketingové kampaně. Tyto podrobné pokyny ukazují, jak nakonfigurovat Power BI sestavy a řídicí panel po nasazení řešení v rámci vašeho předplatného. 

## <a name="prerequisites"></a>Požadavky
* Nasazení [Vehicle Telemetrie Analytics](https://gallery.cortanaintelligence.com/Solution/5bdb23f3abb448268b7402ab8907cc90) řešení. 
* [Nainstalujte Power BI Desktop](http://www.microsoft.com/download/details.aspx?id=45331).
* Získat [předplatné](https://azure.microsoft.com/pricing/free-trial/). Pokud nemáte předplatné Azure, začněte s bezplatnou předplatné Azure.
* Otevřete účet Power BI.

## <a name="cortana-intelligence-suite-components"></a>Cortana Intelligence suite součásti
Jako součást řešení šablony Vehicle Telemetrie analýzy jsou tyto služby Cortana Intelligence nasadit ve vašem předplatném:

* **Azure Event Hubs** ingestuje miliony událostí vehicle telemetrická data do Azure.
* **Azure Stream Analytics** poskytuje přehledy v reálném čase na vehicle stavu a přetrvává tato data do dlouhodobého úložiště pro širší batch analýzu.
* **Azure Machine Learning** zjistí anomálie v reálném čase a k poskytování prediktivní insights používá dávkové zpracování.
* **Azure HDInsight** transformuje data ve velkém měřítku.
* **Azure Data Factory** zpracovává orchestration, plánování, správy prostředků a monitorování dávkové zpracování kanálu.

**Power BI** toto řešení poskytuje bohaté řídicí panel pro data a vizualizace prediktivní analýzy. 

Toto řešení využívá dva různé datové zdroje:

* Simulované vehicle signály a diagnostiky datové sady
* Vehicle katalogu

Vehicle telematika jsme je součástí tohoto řešení. Ho vysílá diagnostické informace a signály, které odpovídají stav vehicle a řízení vzory k danému bodu v čase. 

Vehicle katalog je referenční datové sady, která mapuje VINs modely.

## <a name="power-bi-dashboard-preparation"></a>Příprava řídicí panel Power BI
### <a name="set-up-the-power-bi-real-time-dashboard"></a>Nastavení v reálném čase řídicí panel Power BI

#### <a name="start-the-real-time-dashboard-application"></a>Spusťte aplikaci v reálném čase řídicí panel
Po dokončení nasazení, postupujte podle pokynů ruční operace.

1. Stáhnout aplikaci v reálném čase řídicí panel RealtimeDashboardApp.zip a rozbalte ho.

2.  V rozbalené složce otevřete konfigurační soubor aplikace RealtimeDashboardApp.exe.config. Nahraďte appSettings pro službu Event Hubs, Azure Blob storage a připojení služby Azure Machine Learning s hodnotami v pokynech ruční operace. Uložte provedené změny.

3. Spusťte aplikaci RealtimeDashboardApp.exe. V okně Přihlásit zadejte platné přihlašovací údaje Power BI. 

   ![Power BI přihlásit okna](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/5-sign-into-powerbi.png)
   
4. Vyberte **přijmout**. Se spustí aplikace.

   ![Oprávnění řídicí panel Power BI](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/6-powerbi-dashboard-permissions.png)

5. Přihlaste se k webu Power BI a vytvořit řídicí panel v reálném čase.

Nyní jste připraveni konfigurovat řídicí panel Power BI.  

### <a name="configure-power-bi-reports"></a>Konfigurace sestav Power BI
V reálném čase sestavy a řídicí panel trvat asi 30 až 45 minut na dokončení. 

1. Vyhledejte [Power BI](http://powerbi.com) webovou stránku a přihlaste se.

    ![Power BI přihlašovací stránky](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/6-1-powerbi-signin.png)

2. Nové sady dat se generuje ve službě Power BI. Vyberte **ConnectedCarsRealtime** datové sady.

    ![ConnectedCarsRealtime datové sady](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/7-select-connected-cars-realtime-dataset.png)

3. Chcete-li prázdné sestavu uložit, stiskněte Ctrl + S.

    ![Prázdné sestavy](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/8-save-blank-report.png)

4. Zadejte název sestavy **Vehicle Telemetrie analýzy v reálném čase - sestavy**.

    ![Název sestavy](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/9-provide-report-name.png)

## <a name="real-time-reports"></a>Sestavy v reálném čase
V tomto řešení jsou tři v reálném čase sestavy:

* Vozidel v operaci
* Vozidel údržby
* Vehicle stavu statistiky

Můžete nakonfigurovat všechna tři sestavy, nebo můžete zastavit po jakékoli fázi. Budete pak můžete pokračovat v další části o tom, jak nakonfigurovat batch sestavy. Doporučujeme vytvořit všechny tři sestav můžete znázorňovat úplné statistiky v reálném čase cesty řešení.  

### <a name="vehicles-in-operation-report"></a>Vozidel v sestavě operaci
1. Klikněte dvakrát na **stránka 1**a přejmenujte ji **vozidel v operaci**.

    ![Vozidel v operaci](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4a.png)  

2. Na **pole** vyberte **vin**. Na **vizualizace** vyberte **karty** vizualizace.  

    **Karty** vizualizace se vytvoří, jak je znázorněno na následujícím obrázku:

    ![Vyberte vin](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4b.png)

3. Vyberte oblast, chcete-li přidat nové vizualizace na prázdné.  

4. Na **pole** vyberte **města** a **vin**. Na **vizualizace** vyberte **mapy** vizualizace. Přetáhněte **vin** k **hodnoty** oblasti. Přetáhněte **města** k **legendy** oblasti. 

    ![Karta vizualizace](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4c.png)

5. Na **vizualizace** vyberte **formát** části. Vyberte **název**a změňte **Text** k **vozidel v operaci podle města**.

    ![Vozidel v operaci podle města](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4d.png)   

    Poslední vizualizace vypadá jako v následujícím příkladu:

    ![Poslední vizualizace](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4e.png)

6. Vyberte oblast, chcete-li přidat nové vizualizace na prázdné.  

7. Na **pole** vyberte **města** a **vin**. Na **vizualizace** vyberte **skupinový sloupcový graf** vizualizace. Přetáhněte **města** k **osy** oblasti. Přetáhněte **vin** k **hodnotu** oblasti.

8. Řazení grafu podle **počet vin**.

    ![Počet vin](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4f.png)  

9. Změnit grafu **název** k **vozidel v operaci podle města**. 

10. Vyberte **formátu** a pak vyberte **Data barvy**. Změna **Zobrazit vše** k **na**.

    ![Data barvy](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4g.png)  

11. Změníte barvu jednotlivých města výběrem barvu symbolu.

    ![Změna barvy](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4h.png)  

12. Vyberte oblast, chcete-li přidat nové vizualizace na prázdné.  

13. Na **vizualizace** vyberte **skupinový sloupcový graf** vizualizace. Na **pole** kartě, přetáhněte **města** k **osy** oblasti. Přetáhněte **modelu** k **legendy** oblasti. Přetáhněte **vin** k **hodnotu** oblasti.

    ![Skupinový sloupcový graf](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4i.png)

    Graf vypadá jako na následujícím obrázku:

    ![Vykreslování](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4j.png)

14. Změna uspořádání všemi vizualizacemi tak, aby stránce vypadá jako v následujícím příkladu:

    ![Řídicí panel s vizualizacemi](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4k.png)

Úspěšně jste nakonfigurovali sestavy "Vozidel v operace". Můžete vytvořit další sestavu v reálném čase, nebo můžete zastavit sem a nakonfigurovat řídicího panelu. 

### <a name="vehicles-requiring-maintenance-report"></a>Sestava vozidel nutnosti údržby

1. Vyberte ![přidat](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4add.png) přidat novou sestavu. Přejmenujte ji **vozidel nutnosti údržby**.

    ![Vozidel údržby](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4l.png)  

2. Na **pole** vyberte **vin**. Na **vizualizace** vyberte **karty** vizualizace.

    ![Karta vin vizualizace](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4m.png)  

    Datová sada obsahuje pole s názvem **MaintenanceLabel**. Toto pole může mít hodnotu "0" nebo "1". Hodnota je nastavena ve model strojového učení, která je zřízená jako součást řešení. Je integrovaný s cestou v reálném čase. Hodnotu "1" udává, že vehicle vyžaduje údržby. 

3. Chcete-li přidat **filtr úroveň stránek** zobrazte data pro vozidel, které vyžadují údržby: 

   a. Přetáhněte **MaintenanceLabel** do **stránky úroveň filtry**.
  
      ![Filtry úrovně stránky](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n1.png)

    b. V dolní části **stránky úroveň filtry MaintenanceLabel**, vyberte **základní filtrování**.

      ![Základní filtrování](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n2.png) 

    c. Nastavte hodnotu filtru na **1**.

      ![Hodnota filtru](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n3.png)  

4. Vyberte oblast, chcete-li přidat nové vizualizace na prázdné.  

5. Na **vizualizace** vyberte **skupinový sloupcový graf** vizualizace. 

    ![Karta vin](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4o.png)

    ![Skupinový sloupcový graf](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4p.png)

6. Na **pole** kartě, přetáhněte **modelu** k **osy** oblasti. Přetáhněte **vin** k **hodnotu** oblasti. Seřaďte vizualizaci pomocí **počet vin**. Změnit grafu **název** k **vozidel nutnosti údržby modelem**. 

7. Na **pole** ![pole image](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4field.png) části **vizualizace** kartě, přetáhněte **vin** k **sytost barev**.

    ![Sytost barev](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4q.png)  

8. Na **formátu** změňte **Data barvy** v vizualizaci: 

    a. Změna **minimální** barvu, která má **F2C812**.

    b. Změna **maximální** barvu, která má **FF6300**.

    ![Nové barvy dat](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4r.png)

    Nové vizualizační barvy vypadat podobně jako v následujícím příkladu:

    ![Nové vizualizační barvy](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4s.png)  

9. Vyberte oblast, chcete-li přidat nové vizualizace na prázdné.  

10. Na **vizualizace** vyberte **skupinový sloupcový graf**. Přetáhněte **vin** k **hodnotu** oblasti. Přetáhněte **města** k **osy** oblasti. Řazení grafu podle **počet vin**. Změnit grafu **název** k **vozidel údržby podle města**.

    ![Vozidel údržby podle města](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4t.png)  

11. Vyberte oblast, chcete-li přidat nové vizualizace na prázdné.  

12. Na **vizualizace** vyberte **více řádků karty** vizualizace. Přetáhněte **modelu** a **vin** k **pole** oblasti.

    ![Karta s více řádků](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4u.png)    

13. Změna uspořádání všemi vizualizacemi tak, aby poslední sestava vypadá jako v následujícím příkladu: 

    ![Upraveným závěrečnou zprávu](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4v.png)  

Úspěšně jste nakonfigurovali v reálném čase sestavy "Vozidel nutnosti údržby". Můžete vytvořit další sestavu v reálném čase, nebo můžete zastavit sem a nakonfigurovat řídicího panelu. 

### <a name="vehicle-health-statistics-report"></a>Sestava stavu statistiky vehicle

1. Vyberte ![přidat](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4add.png) přidat novou sestavu. Přejmenujte ji **vozidel stavu statistiky**. 

2. Na **vizualizace** vyberte **měřidla** vizualizace. Přetáhněte **rychlost** k **hodnotu**, **minimální hodnota**, a **maximální hodnota** oblasti.

   ![Statistiky vozidel stavu](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4w.png)  

3. V **hodnotu** oblasti, změňte výchozí agregace **rychlost** k **průměrná**.

4. V **minimální hodnota** oblasti, změňte výchozí agregace **rychlost** k **minimální**.

5. V **maximální hodnota** oblasti, změňte výchozí agregace **rychlost** k **maximální**.

   ![Rychlost hodnoty](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4x.png)  

6. Přejmenujte **název měřidla** k **průměrná rychlost**.

   ![Ukazatel](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4y.png)  

7. Vyberte oblast, chcete-li přidat nové vizualizace na prázdné.  

    Podobně, přidejte **měřidla** pro **průměrná těžba ropy modul**, **průměrná paliva**, a **průměrnou teplotu motoru**.  

8. Změňte výchozí agregace polí v každé měřidla, stejně jako v předchozích krocích **průměrná rychlost** měřidla.

    ![Další měřidla](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4z.png)

9. Vyberte oblast, chcete-li přidat nové vizualizace na prázdné.

10. Na **vizualizace** vyberte **spojnicový a skupinový sloupcový graf** vizualizace. Přetáhněte **města** k **sdílené osy**. Přetáhněte **tirepressure**, **engineoil**, a **rychlost** k **hodnoty ve sloupcích** oblasti. Změnit jejich typ agregace na **průměrná**. 

11. Přetáhněte **engineTemperature** k **hodnoty řádku** oblasti. Změnit typ agregace na **průměrná**. 

    ![Sloupce a řádku hodnoty](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4aa.png)

12. Změnit grafu **název** k **průměrná rychlost, můžete zadat naléhavost, modul těžba ropy a teploty modul**.  

    ![Název spojnicový a skupinový sloupcový graf](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4bb.png)

13. Vyberte oblast, chcete-li přidat nové vizualizace na prázdné.

14. Na **vizualizace** vyberte **vlastnosti Treemap** vizualizace. Přetáhněte **modelu** k **skupiny** oblasti. Přetáhněte **MaintenanceProbability** k **hodnoty** oblasti.

15. Změnit grafu **název** k **Vehicle modely údržby**.

    ![Název vlastnosti Treemap](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4cc.png)

16. Vyberte oblast, chcete-li přidat nové vizualizace na prázdné.

17. Na **vizualizace** vyberte **100 % skládaný sloupcový graf** vizualizace. Přetáhněte **města** k **osy** oblasti. Přetáhněte **MaintenanceProbability** a **RecallProbability** k **hodnotu** oblasti.

    ![Osy a hodnotu oblastí](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4dd.png)

18. Na **formátu** vyberte **Data barvy**. Nastavte **MaintenanceProbability** barvy, která má hodnotu **F2C80F**.

19. Změnit grafu **název** k **pravděpodobnosti Vehicle údržby & odvolání podle města**.

    ![Název 100 % skládaný pruhový graf](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ee.png)

20. Vyberte oblast, chcete-li přidat nové vizualizace na prázdné.

21. Na **vizualizace** vyberte **plošný graf** vizualizace. Přetáhněte **modelu** k **osy** oblasti. Přetáhněte **engineOil**, **tirepressure**, **rychlost**, a **MaintenanceProbability** k **hodnoty** oblast. Změnit jejich typ agregace na **průměrná**. 

    ![Typ agregace](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ff.png)

22. Změnit grafu **název** k **průměrná těžba ropy modul, můžete zadat naléhavost, rychlost a údržby pravděpodobnosti modelem**.

    ![Název oblasti grafu](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4gg.png)

23. Vyberte oblast, chcete-li přidat nové vizualizace na prázdné.

24. Na **vizualizace** vyberte **bodový graf** vizualizace. Přetáhněte **modelu** k **podrobnosti** a **legendy** oblasti. Přetáhněte **paliva** k **osy x** oblasti. Změnit agregace na **průměrná**. Přetáhněte **engineTemperature** k **osy y** oblasti. Změnit agregace na **průměrná**. Přetáhněte **vin** k **velikost** oblasti.

    ![Podrobnosti, legendy, osy a velikosti oblasti](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4hh.png)

25. Změnit grafu **název** k **průměrná paliva průměr engineTemperature a počet vin modelem**.

    ![Bodový název grafu](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ii.png)

    Závěrečná zpráva vypadá jako v následujícím příkladu:

    ![Závěrečnou zprávu](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4jj.png)

### <a name="pin-visualizations-from-the-reports-to-the-real-time-dashboard"></a>Vizualizace kódu PIN ze sestav na řídicím panelu v reálném čase
1. Vytvořit prázdný řídicí panel tak, že vyberete plus symbol vedle **řídicí panely**. Zadejte název **panelu analýzy Telemetrie Vehicle**.

    ![Řídicí panel plus – symbol](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.5.png)

2. Připnete vizualizace z předchozí sestavy na řídicí panel. 

    ![Symbol pin řídicí panel](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.6.png)

    Když jsou všechny tři sestavy připnuli k řídicímu panelu, by měla vypadat podobně jako v následujícím příkladu. Pokud jste nevytvořili všechny sestavy, může vypadat jinak řídicího panelu. 

    ![Řídicí panel se sestavami](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-4.0.png)

Úspěšně jste vytvořili řídicím panelu v reálném čase. Při dalším spuštění CarEventGenerator.exe a RealtimeDashboardApp.exe, uvidíte na řídicím panelu živé aktualizace. Následující kroky trvat asi 10 až 15 minut.

## <a name="set-up-the-power-bi-batch-processing-dashboard"></a>Nastavit řídicí panel Power BI dávkové zpracování
> [!NOTE]
> O dvou hodin (z úspěšné dokončení nasazení) je potřebná pro zpracování kanálu dokončí provádění a zpracovat v roce za generované datové dávky začátku do konce. Čekající na zpracování dokončení, než budete pokračovat v následujících krocích:
> 
> 

### <a name="download-the-power-bi-designer-file"></a>Stáhněte si soubor návrháře Power BI

1. Předem nakonfigurovaný soubor návrháře Power BI je součástí pokyny k ruční operace nasazení. Podívejte se na "2. Vytvořit řídicí panel Power BI dávkové zpracování."

2. Stažení šablony Power BI pro dávkové zpracování řídicí panel zde názvem **ConnectedCarsPbiReport.pbix**.

3. Uložte místně.

### <a name="configure-power-bi-reports"></a>Konfigurace sestav Power BI

1. Otevřete soubor návrháře **ConnectedCarsPbiReport.pbix** pomocí Power BI Desktop. Pokud ji nemáte, nainstalujte Power BI Desktop z [instalace Power BI Desktop](http://www.microsoft.com/download/details.aspx?id=45331) webu.

2. Vyberte **upravit dotazy**.

    ![Úpravy dotazů](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/10-edit-powerbi-query.png)

3. Klikněte dvakrát na **zdroj**.

    ![Zdroj](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/11-set-powerbi-source.png)

4. Aktualizujte připojovací řetězec serveru se serverem Azure SQL, které byly zřízeny jako součást nasazení. Podívejte se v pokynech ruční operace v rámci Azure SQL database:

    * Server: somethingsrv.database.windows.net
    * Databáze: connectedcar
    * Uživatelské jméno: uživatelské jméno
    * Heslo: Heslo systému SQL Server můžete spravovat z portálu Azure.

5. Nechte **databáze** jako **connectedcar**.

    ![Databáze](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/12-set-powerbi-database.png)

6. Vyberte **OK**.

7. **Pověření systému Windows** ve výchozím nastavení je zvolena karta. Změňte ho na **databáze pověření** výběrem **databáze** kartě vpravo.

8. Zadejte **uživatelské jméno** a **heslo** vaší databáze Azure SQL, který byl zadán během instalace jeho nasazení.

    ![Přihlašovací údaje databáze](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/13-provide-database-credentials.png)

9. Vyberte **Connect** (Připojit).

10. Opakujte předchozí kroky pro každý tři zbývající dotazů v pravém podokně. Aktualizujte podrobnosti připojení zdroje dat.

11. Vyberte **zavřete a načíst**. Power BI Desktop souboru datové sady jsou připojené k tabulky databáze SQL.

12. Vyberte **zavřete** zavřete soubor Power BI Desktop.

    ![Zavřít](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/14-close-powerbi-desktop.png)

13. Vyberte **Uložit** a uložte změny. 

Nyní máte nakonfigurovaný všechny sestavy, které odpovídají dávkového zpracování cestu v řešení. 

## <a name="upload-to-powerbicom"></a>Nahrát na powerbi.com
1. Přejděte na [Power BI webový portál](http://powerbi.com)a přihlaste se.

2. Vyberte **Načíst data**.

3. Nahrajte soubor Power BI Desktop. Vyberte **načíst Data** > **získat soubory** > **místního souboru**.

4. Přejděte na **ConnectedCarsPbiReport.pbix**.

5. Po nahrání souboru, přejděte zpět na váš pracovní prostor Power BI. Datové sady, sestavy a prázdný řídicí panel se vytvoří pro vás.  

6. Grafy kód PIN na nové řídicí panel názvem **panelu analýzy Telemetrie Vehicle** v Power BI. Vyberte prázdný řídicí panel, který byl dřív vytvořili a potom přejděte na **sestavy** části. Vyberte nově nahraném sestavu.  

    ![Nové řídicí panel Power BI](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard1.png) 

    Sestava obsahuje šest stránky:

    Stránka 1: Hustotu Vehicle  
    Stránka 2: Stav v reálném čase vehicle  
    Stránka 3: Intenzivně řízené vozidel   
    Stránka 4: Připomenout vozidel  
    5 stránky: Efektivní řízené vozidel paliva  
    6 stránky: Logo společnosti Contoso motory  

    ![Sestavy Power BI s šest stránek](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard2.png)

7. Z **stránka 3**, připnout na následující obsah:  

    a. **Počet vin**  

   ![Stránka 3 počet vin](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard3.png)

    b. **Intenzivně řídí vozidel modelu – vodopádu grafu** 

   ![Graf stránka 3 4](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard4.png)

8. Z **stránky 5**, připnout na následující obsah: 

    a. **Počet vin**

   ![Stránka 5 grafu 5](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard5.png)

    b. **Zvýšení vozidel modelem: Skupinový sloupcový graf**

   ![Stránka 5 grafu 6](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard6.png)

9. Z **stránky 4**, připnout na následující obsah:  

    a. **Počet vin** 

   ![Stránka 4 grafu 7](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard7.png) 

    b. **Odvolané vozidel podle města, model: vlastnosti Treemap**

   ![Stránka 4 grafu 8](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard8.png)  

10. Z **stránky 6**, připnout na následující obsah:  

    * **Logo společnosti Contoso motory**

    ![Logo společnosti Contoso motory](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard9.png)

### <a name="organize-the-dashboard"></a>Uspořádání řídicí panel  

1. Přejděte na řídicí panel.

2. Podržte ukazatel nad každým grafem. Přejmenujte každým grafem podle pojmenování najdete v následujícím příkladu dokončení řídicí panel:

   ![Řídicí panel organizace](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-organize-dashboard2.png) 
   
3. Přesunete na grafy přibližně vypadat podobně jako v následujícím příkladu řídicí panel:

    ![Upraveným řídicí panel](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard.png)

4. Po vytvoření všechny sestavy, které jsou uvedené v tomto dokumentu konečné dokončení řídicí panel vypadá jako v následujícím příkladu: 

   ![Poslední řídicí panel](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-organize-dashboard3.png)

Úspěšně jste vytvořili sestavy a řídicí panel k získání v reálném čase, prediktivní a zvyklosti batch Statistika na vehicle stavu a řídí.  
