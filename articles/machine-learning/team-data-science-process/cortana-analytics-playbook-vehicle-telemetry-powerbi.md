---
title: Řídicí panel Power BI pro stavu vozidel a jízdních návycích - vědecké zpracování týmových dat
description: Vývoj řídicí panel Power BI a získejte v reálném čase, prediktivní, batch přehledů stavu vozidel a jízdních návycích.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 03/14/2018
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: a964820979f24af1fee2be65461302be73b9e77b
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53135113"
---
# <a name="vehicle-telemetry-analytics-solution-template-power-bi-dashboard-setup-instructions"></a>Pokyny k instalaci vozidla telemetrická data Analytics řešení šablony Power BI řídicí panel

Představuje řešení analýzy Telemetrie vozidla jak můžou autorizovaní prodejci, výrobci automobilů a pojišťovací společnosti můžou pomocí možností Cortana Intelligence. Může získat v reálném čase a prediktivní přehledy o stavu vozidel a jízdních návycích ke zlepšení prostředí pro zákazníky, výzkumu a vývoje a marketingových kampaní. Tyto podrobné pokyny ukazují, jak nakonfigurovat řídicího panelu a sestavách Power BI po nasazení řešení v rámci vašeho předplatného. 

Souhrnný popis tohoto řešení najdete v tématu [řešení analýzy Telemetrie vozidla playbook](cortana-analytics-playbook-vehicle-telemetry.md).
Prozkoumejte ještě dál toto řešení, najdete v článku [playbook řešení analýzy Telemetrie vozidla: Podívejte se na řešení](cortana-analytics-playbook-vehicle-telemetry-deep-dive.md).

## <a name="prerequisites"></a>Požadavky
* Nasazení [analýza Telemetrie vozidel](https://gallery.cortanaintelligence.com/Solutions/5bdb23f3abb448268b7402ab8907cc90) řešení. 
* [Instalace Power BI Desktopu](https://aka.ms/pbidesktopstore).
* Získat [předplatného Azure](https://azure.microsoft.com/pricing/free-trial/). Pokud nemáte předplatné Azure, začněte s bezplatným předplatným Azure.
* Otevřete si účet Power BI.

## <a name="cortana-intelligence-suite-components"></a>Součástí Cortana Intelligence suite
Jako součást šablony řešení analýzy Telemetrie vozidla jsou tyto služby Cortana Intelligence nasazených ve vašem předplatném:

* **Azure Event Hubs** ingestuje miliony událostí telemetrie vozidel do Azure.
* **Azure Stream Analytics** poskytuje v reálném čase přehledy o stavu vozidel a ukládá tato data do dlouhodobého úložiště pro bohatší dávkové analýzy.
* **Azure Machine Learning** detekuje anomálie v reálném čase a používá k zajištění prediktivních přehledů dávkové zpracování.
* **Azure HDInsight** transformuje data ve velkém měřítku.
* **Azure Data Factory** zajišťuje orchestraci, plánování, správu prostředků a monitorování kanálu dávkového zpracování.

**Power BI** poskytuje tomuto řešení panel s bohatými funkcemi pro vizualizace prediktivních analýz a data. 

Toto řešení využívá dvou různých datových zdrojů:

* Signály simulované vozidel a diagnostických datové sady
* Katalog vozidel

Simulátor telematiky vozidel je součástí tohoto řešení. Vysílá diagnostické informace a signály, které odpovídají stavu vozidel a řízení vzory v daném bodě v čase. 

Katalog vozidel je referenční sady dat, která se mapuje VINs modely.

## <a name="power-bi-dashboard-preparation"></a>Příprava řídicí panel Power BI
### <a name="set-up-the-power-bi-real-time-dashboard"></a>Nastavení řídicího panelu Power BI v reálném čase

#### <a name="start-the-real-time-dashboard-application"></a>Spusťte aplikaci v řídicím panelu v reálném čase
Po dokončení nasazení, postupujte podle pokynů ruční operace.

1. Stáhnout aplikaci pro řídicí panel v reálném čase RealtimeDashboardApp.zip a rozbalte ho.

1.  Ve složce rozzipovaný otevřete RealtimeDashboardApp.exe.config konfiguračního souboru aplikace. Nahraďte appSettings pro Event Hubs, Azure Blob storage a připojení služby Azure Machine Learning s hodnotami v pokynech pro ruční operace. Uložte provedené změny.

1. Spusťte aplikaci RealtimeDashboardApp.exe. V okně Přihlásit zadejte přihlašovací údaje platné Power BI. 

   ![Power BI okno s přihlášením](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/5-sign-into-powerbi.png)
   
1. Vyberte **Přijmout**. Aplikace se spustí.

   ![Oprávnění řídicího panelu Power BI](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/6-powerbi-dashboard-permissions.png)

1. Přihlášení na web Power BI a vytvoření řídicího panelu v reálném čase.

Nyní jste připraveni nakonfigurovat na řídicí panel Power BI.  

### <a name="configure-power-bi-reports"></a>Konfigurace sestav Power BI
V reálném čase sestavy a řídicí panel trvat asi 30 až 45 minut. 

1. Přejděte [Power BI](http://powerbi.com) webové stránky a přihlaste se.

    ![Power BI přihlašovací stránky](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/6-1-powerbi-signin.png)

1. Nové datové sady je generována v Power BI. Vyberte **ConnectedCarsRealtime** datové sady.

    ![ConnectedCarsRealtime datové sady](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/7-select-connected-cars-realtime-dataset.png)

1. Pro uložení sestavy prázdné, stiskněte Ctrl + S.

    ![Prázdná sestava](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/8-save-blank-report.png)

1. Zadejte název sestavy **vozidla Telemetrie analýzy v reálném čase – sestavy**.

    ![Název sestavy](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/9-provide-report-name.png)

## <a name="real-time-reports"></a>Sestavy v reálném čase
V tomto řešení jsou tři v reálném čase sestavy:

* Vozidla v operaci
* Vozidel údržby
* Statistika stavu vozidel

Můžete nakonfigurovat všechny tři sestav, nebo můžete zastavit po jakékoli fázi. Potom můžete přejít k další části o tom, jak nakonfigurovat sestavy služby batch. Doporučujeme vytvořit všechny tři sestav můžete znázorňovat úplný přehled v reálném čase cesty řešení.  

### <a name="vehicles-in-operation-report"></a>Vozidla v sestavě operaci
1. Dvakrát klikněte na panel **stránka 1**a přejmenujte jej **vozidla v operaci**.

    ![Vozidla v operaci](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4a.png)  

1. Na **pole** kartu, vyberte možnost **vin**. Na **vizualizace** kartu, vyberte **karty** vizualizace.  

    **Karty** vytvoří se vizualizace, jak je znázorněno na následujícím obrázku:

    ![Vyberte vin](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4b.png)

1. Vyberte prázdnou oblast pro přidání nové vizualizace.  

1. Na **pole** kartu, vyberte možnost **Město** a **vin**. Na **vizualizace** kartu, vyberte **mapy** vizualizace. Přetáhněte **vin** k **hodnoty** oblasti. Přetáhněte **Město** k **legendy** oblasti. 

    ![Vizualizace karet](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4c.png)

1. Na **vizualizace** kartu, vyberte **formátu** oddílu. Vyberte **Title**a změnit **Text** k **vozidla v operaci podle města**.

    ![Vozidla v operaci podle města](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4d.png)   

    Konečné vizualizace vypadá jako v následujícím příkladu:

    ![Konečné vizualizace](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4e.png)

1. Vyberte prázdnou oblast pro přidání nové vizualizace.  

1. Na **pole** kartu, vyberte možnost **Město** a **vin**. Na **vizualizace** kartu, vyberte **skupinový sloupcový graf** vizualizace. Přetáhněte **Město** k **osy** oblasti. Přetáhněte **vin** k **hodnotu** oblasti.

1. Graf seřaďte podle **počet vin**.

    ![Počet vin](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4f.png)  

1. Změnit graf **Title** k **vozidla v operaci podle města**. 

1. Vyberte **formátu** a potom vyberte **barvy dat**. Změna **Zobrazit vše** k **na**.

    ![Barvy dat](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4g.png)  

1. Změna barvy jednotlivých město tak, že vyberete symbol barvu.

    ![Změna barvy](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4h.png)  

1. Vyberte prázdnou oblast pro přidání nové vizualizace.  

1. Na **vizualizace** kartu, vyberte **skupinový sloupcový graf** vizualizace. Na **pole** kartu tak, že přetáhnete **Město** k **osy** oblasti. Přetáhněte **modelu** k **legendy** oblasti. Přetáhněte **vin** k **hodnotu** oblasti.

    ![Skupinový sloupcový graf](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4i.png)

    Graf vypadat jako na následujícím obrázku:

    ![Vykreslování](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4j.png)

1. Změna uspořádání všechny vizualizace tak, že bude stránka vypadat jako v následujícím příkladu:

    ![Řídicí panel s vizualizacemi](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4k.png)

Úspěšně jste nakonfigurovali sestavy "Vozidla v operaci". Můžete vytvořit další sestavy v reálném čase, nebo můžete ukončit a konfigurace řídicího panelu. 

### <a name="vehicles-requiring-maintenance-report"></a>Sestavy vyžadující údržba vozidel

1. Vyberte ![přidat](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4add.png) přidat nové sestavy. Přejmenujte ho **vozidel nutnosti údržby**.

    ![Vozidel údržby](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4l.png)  

1. Na **pole** kartu, vyberte možnost **vin**. Na **vizualizace** kartu, vyberte **karty** vizualizace.

    ![Vizualizace karet vin](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4m.png)  

    Datová sada obsahuje pole s názvem **MaintenanceLabel**. Toto pole může mít hodnotu "0" nebo "1". Hodnota je nastavena pomocí modelu strojového učení, pro kterého je zřízené jako součást řešení. Je integrovaná s cestou v reálném čase. Hodnotu "1" znamená, že vozidla vyžaduje údržbu. 

1. Chcete-li přidat **filtru na úrovni stránek** zobrazíte data pro vozidel, které vyžadují údržbu: 

   a. Přetáhněte **MaintenanceLabel** pole **filtry na úrovni stránek**.
  
      ![Filtry na úrovni stránek](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n1.png)

    b. V dolní části **MaintenanceLabel filtry stránky úroveň**vyberte **základního filtrování**.

      ![Základní filtrování](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n2.png) 

    c. Nastavte na hodnotu filtru **1**.

      ![Hodnota filtru](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n3.png)  

1. Vyberte prázdnou oblast pro přidání nové vizualizace.  

1. Na **vizualizace** kartu, vyberte **skupinový sloupcový graf** vizualizace. 

    ![Karta vin](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4o.png)

    ![Skupinový sloupcový graf](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4p.png)

1. Na **pole** kartu tak, že přetáhnete **modelu** k **osy** oblasti. Přetáhněte **vin** k **hodnotu** oblasti. Seřaďte vizualizaci podle **počet vin**. Změnit graf **Title** k **vozidel nutnosti údržby modelem**. 

1. Na **pole** ![pole image](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4field.png) část **vizualizace** kartu tak, že přetáhnete **vin** k **sytost barev**.

    ![Sytost barev](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4q.png)  

1. Na **formátu** oddíl, změna **barvy dat** ve vizualizaci: 

    a. Změnit **minimální** barvu, která má **F2C812**.

    b. Změnit **maximální** barvu, která má **FF6300**.

    ![Nové barvy dat](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4r.png)

    Nové vizualizace barvy vypadat jako v následujícím příkladu:

    ![Nové vizualizace barvy](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4s.png)  

1. Vyberte prázdnou oblast pro přidání nové vizualizace.  

1. Na **vizualizace** kartu, vyberte možnost **skupinový sloupcový graf**. Přetáhněte **vin** k **hodnotu** oblasti. Přetáhněte **Město** k **osy** oblasti. Graf seřaďte podle **počet vin**. Změnit graf **Title** k **vozidel údržby podle města**.

    ![Vozidel údržby podle města](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4t.png)  

1. Vyberte prázdnou oblast pro přidání nové vizualizace.  

1. Na **vizualizace** kartu, vyberte **kartu s více řádky** vizualizace. Přetáhněte **modelu** a **vin** k **pole** oblasti.

    ![Kartu s více řádky](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4u.png)    

1. Změna uspořádání všechny vizualizace tak, aby konečná sestava bude vypadat jako v následujícím příkladu: 

    ![Upraveným Konečná sestava](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4v.png)  

Úspěšně jste nakonfigurovali v reálném čase sestavy "Vozidel nutnosti údržby". Můžete vytvořit další sestavy v reálném čase, nebo můžete ukončit a konfigurace řídicího panelu. 

### <a name="vehicle-health-statistics-report"></a>Sestavy statistik stavu vozidel

1. Vyberte ![přidat](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4add.png) přidat nové sestavy. Přejmenujte ho **statistik stavu vozidel**. 

1. Na **vizualizace** kartu, vyberte **měřidla** vizualizace. Přetáhněte **rychlost** k **hodnotu**, **minimální hodnota**, a **maximální hodnota** oblasti.

   ![Statistika stavu vozidel](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4w.png)  

1. V **hodnotu** oblasti, změňte výchozí agregaci **rychlost** k **průměrné**.

1. V **minimální hodnota** oblasti, změňte výchozí agregaci **rychlost** k **minimální**.

1. V **maximální hodnota** oblasti, změňte výchozí agregaci **rychlost** k **maximální**.

   ![Rychlost hodnoty](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4x.png)  

1. Přejmenovat **název měřidla** k **průměrná rychlost**.

   ![Ukazatel](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4y.png)  

1. Vyberte prázdnou oblast pro přidání nové vizualizace.  

    Podobně, přidejte **měřidla** pro **průměrné stroj ropy**, **průměrná posílit**, a **průměrná teplota modul**.  

1. Změňte výchozí agregaci polí v jednotlivých měřidla, stejně jako v předchozích krocích v **průměrná rychlost** měřidla.

    ![Další měřidla](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4z.png)

1. Vyberte prázdnou oblast pro přidání nové vizualizace.

1. Na **vizualizace** kartu, vyberte **spojnicový a skupinový sloupcový graf** vizualizace. Přetáhněte **Město** k **sdílená osa**. Přetáhněte **tirepressure**, **engineoil**, a **rychlost** k **hodnoty ve sloupcích** oblasti. Změňte jejich typ agregace na **průměrné**. 

1. Přetáhněte **engineTemperature** k **hodnoty řádků** oblasti. Změnit typ agregace pro **průměrné**. 

    ![Sloupec a hodnoty řádků](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4aa.png)

1. Změnit graf **Title** k **průměrná rychlost, můžete zadat přetížení, modul ropy a teploty modulu**.  

    ![Název spojnicový a skupinový sloupcový graf](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4bb.png)

1. Vyberte prázdnou oblast pro přidání nové vizualizace.

1. Na **vizualizace** kartu, vyberte **mapy stromové struktury** vizualizace. Přetáhněte **modelu** k **skupiny** oblasti. Přetáhněte **MaintenanceProbability** k **hodnoty** oblasti.

1. Změnit graf **Title** k **vozidla modely údržby**.

    ![Nadpis mapy stromové struktury](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4cc.png)

1. Vyberte prázdnou oblast pro přidání nové vizualizace.

1. Na **vizualizace** kartu, vyberte **100 % skládaný pruhový graf** vizualizace. Přetáhněte **Město** k **osy** oblasti. Přetáhněte **MaintenanceProbability** a **RecallProbability** k **hodnotu** oblasti.

    ![Osy a hodnota oblastí](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4dd.png)

1. Na **formátu** vyberte **barvy dat**. Nastavte **MaintenanceProbability** barevné hodnotě **F2C80F**.

1. Změnit graf **Title** k **pravděpodobnost vozidla údržby & spojené s vracením podle města**.

    ![Název 100 % skládaný pruhový graf](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ee.png)

1. Vyberte prázdnou oblast pro přidání nové vizualizace.

1. Na **vizualizace** kartu, vyberte **plošný graf** vizualizace. Přetáhněte **modelu** k **osy** oblasti. Přetáhněte **engineOil**, **tirepressure**, **rychlost**, a **MaintenanceProbability** k **hodnoty** oblast. Změňte jejich typ agregace na **průměrné**. 

    ![Typ agregace](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ff.png)

1. Změnit graf **Title** k **průměrná modul ropy, můžete zadat tlaku, rychlost a údržby pravděpodobnost modelem**.

    ![Název oblasti grafu](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4gg.png)

1. Vyberte prázdnou oblast pro přidání nové vizualizace.

1. Na **vizualizace** kartu, vyberte **bodový graf** vizualizace. Přetáhněte **modelu** k **podrobnosti** a **legendy** oblasti. Přetáhněte **posílit** k **osy x** oblasti. Změňte agregaci na **průměrné**. Přetáhněte **engineTemperature** k **osy y** oblasti. Změňte agregaci na **průměrné**. Přetáhněte **vin** k **velikost** oblasti.

    ![Podrobnosti, legendy, osy a velikosti oblasti](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4hh.png)

1. Změnit graf **Title** k **průměrné paliva průměrem engineTemperature a počet vin modelem**.

    ![Bodový graf nadpisu](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ii.png)

    Výsledná sestava bude vypadat jako v následujícím příkladu:

    ![Konečná sestava](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4jj.png)

### <a name="pin-visualizations-from-the-reports-to-the-real-time-dashboard"></a>Připnutí vizualizací ze sestav na řídicím panelu v reálném čase
1. Vytvořit prázdný řídicí panel tak, že vyberete plus symbol vedle **řídicí panely**. Zadejte název **řídicí panel analýzy Telemetrie vozidla**.

    ![Řídicí panel a symbol](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.5.png)

1. Připnete vizualizace z předchozí sestavy na řídicí panel. 

    ![Řídicí panel připnout symbol](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.6.png)

    Když všechny tři sestavy jsou připnuté na řídicí panel, by měl vypadat jako v následujícím příkladu. Pokud jste nevytvořili všechny sestavy, řídicí panel může vypadat jinak. 

    ![Řídicí panel se sestavami](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-4.0.png)

Úspěšně jste vytvořili řídicího panelu v reálném čase. V průběhu spouštění CarEventGenerator.exe RealtimeDashboardApp.exe, uvidíte na řídicím panelu živé aktualizace. Následující kroky dokončení trvat asi 10 až 15 minut.

## <a name="set-up-the-power-bi-batch-processing-dashboard"></a>Nastavit řídicí panel Power BI dávkové zpracování
> [!NOTE]
> Trvá přibližně dvě hodiny (od úspěšné dokončení nasazení) pro začátku do konce dávkové zpracování kanálu dokončí provádění a zpracovat za rok za generovaná data. Čekající na zpracování dokončit, než budete pokračovat následujícím postupem:
> 
> 

### <a name="download-the-power-bi-designer-file"></a>Stáhněte si soubor návrháře Power BI

1. Předem nakonfigurovaný soubor návrháře Power BI je součástí pokyny k ruční operace nasazení. Vyhledejte "2. Nastavit řídicí panel Power BI dávkové zpracování."

1. Stažení šablony Power BI pro dávkové zpracování řídicí panel zde volá **ConnectedCarsPbiReport.pbix**.

1. Uloží do místního prostředí.

### <a name="configure-power-bi-reports"></a>Konfigurace sestav Power BI

1. Otevřete soubor návrháře **ConnectedCarsPbiReport.pbix** pomocí Power BI Desktopu. Pokud ho nemáte, nainstalujte Power BI Desktopu z [instalace Power BI Desktopu](https://aka.ms/pbidesktopstore) webu.

1. Vyberte **upravit dotazy**.

    ![Upravit dotazy](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/10-edit-powerbi-query.png)

1. Dvakrát klikněte na panel **zdroj**.

    ![Zdroj](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/11-set-powerbi-source.png)

1. Aktualizujte připojovací řetězec serveru Azure SQL serveru, který je teď Zřizuje se jako součást svého nasazení. Podívejte se v pokynech pro ruční operace v rámci Azure SQL database:

    * Server: somethingsrv.database.windows.net
    * Databáze: connectedcar
    * Uživatelské jméno: uživatelské jméno
    * Heslo: Heslo SQL serveru můžete spravovat z portálu Azure portal.

1. Ponechte **databáze** jako **connectedcar**.

    ![Databáze](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/12-set-powerbi-database.png)

1. Vyberte **OK**.

1. **Přihlašovacích údajů Windows** ve výchozím nastavení je vybraná karta. Změňte ho na **přihlašovací údaje databáze** tak, že vyberete **databáze** karty na pravé straně.

1. Zadejte **uživatelské jméno** a **heslo** pro Azure SQL database, který byl zadán během instalace jeho nasazení.

    ![Databázová pověření](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/13-provide-database-credentials.png)

1. Vyberte **Connect** (Připojit).

1. Předchozí kroky opakujte pro všechny tři zbývající dotazů k dispozici v pravém podokně. Pak aktualizujte podrobnosti připojení zdroje dat.

1. Vyberte **zavřít a načíst**. Power BI Desktopu soubor datové sady jsou propojené tabulky databáze SQL.

1. Vyberte **zavřete** zavřete soubor Power BI Desktopu.

    ![Zavřít](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/14-close-powerbi-desktop.png)

1. Vyberte **Uložit** a uložte změny. 

Nyní jste nakonfigurovali všechny sestavy, které odpovídají dávkového zpracování cestu v řešení. 

## <a name="upload-to-powerbicom"></a>Nahrání do: powerbi.com
1. Přejděte [webový portál Power BI](http://powerbi.com)a přihlaste se.

1. Vyberte **Načíst data**.

1. Nahrajte soubor Power BI Desktopu. Vyberte **získat Data** > **získat soubory** > **místního souboru**.

1. Přejděte na **ConnectedCarsPbiReport.pbix**.

1. Po nahrání souboru, přejděte zpět na pracovní prostor Power BI. Datové sady, sestavy a prázdný řídicí panel se vytvoří za vás.  

1. Připnout grafy na nový řídicí panel volá **řídicí panel analýzy Telemetrie vozidla** v Power BI. Vyberte prázdný řídicí panel, který byl dříve vytvořen a potom přejděte ke **sestavy** oddílu. Vyberte nově nahraných sestav.  

    ![Nový řídicí panel Power BI](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard1.png) 

    Sestavy má šest stránek:

    Stránka 1: Hustota vozidla  
    Stránka 2: Stavu vozidel v reálném čase  
    Stránka 3: Agresivně řízené vozidel   
    Stránka 4: Připomenout vozidel  
    Stránka 5: Posílit efektivně řízené vozidel  
    Stránky 6: Logo společnosti Contoso motory  

    ![Sestavy Power BI s šest stránek](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard2.png)

1. Z **stránka 3**, připněte následujícím obsahem:  

    a. **Počet vin**  

   ![Stránka 3 počet vin](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard3.png)

    b. **Agresivní vozidel řízené modelem – Vodopádový graf** 

   ![Graf stránka 3 4](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard4.png)

1. Z **stránka 5**, připněte následujícím obsahem: 

    a. **Počet vin**

   ![Stránka 5 grafu 5](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard5.png)

    b. **Zvýšení vozidel modelem: Skupinový sloupcový graf**

   ![Stránka 5 grafu 6](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard6.png)

1. Z **stránka 4**, připněte následujícím obsahem:  

    a. **Počet vin** 

   ![Stránka 4 grafu 7](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard7.png) 

    b. **Odvolaného vozidel podle města, model: mapy stromové struktury**

   ![Stránka 4 grafu 8](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard8.png)  

1. Z **stránky 6**, připněte následujícím obsahem:  

    * **Logo společnosti Contoso motory**

    ![Logo společnosti Contoso motory](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard9.png)

### <a name="organize-the-dashboard"></a>Uspořádat řídicím panelu  

1. Přejdete na řídicí panel.

1. Najeďte myší nad každým grafem. Přejmenujte každý graf založený na pojmenování najdete v následujícím příkladu hotový řídicí panel:

   ![Řídicí panel organizace](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-organize-dashboard2.png) 
   
1. Přesuňte na grafy přibližně vypadat jako následující ukázkový řídicí panel:

    ![Upraveným řídicí panel](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard.png)

1. Po vytvoření všech sestav, které jsou uvedené v tomto dokumentu se konečné hotový řídicí panel bude vypadat jako v následujícím příkladu: 

   ![Poslední řídicí panel](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-organize-dashboard3.png)

Úspěšně jste vytvořili sestavy a řídicí panel, abyste získali v reálném čase, prediktivní a batch přehledy o stavu vozidel a jízdních návycích.  
