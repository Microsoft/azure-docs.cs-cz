---
title: Vytvoření řešení IoT pomocí služby Stream Analytics | Microsoft Docs
description: Úvodní kurz pro řešení Stream Analytics IoT mýtná celnice scénáře
keywords: řešení IOT, okno funkce
documentationcenter: ''
services: stream-analytics
author: SnehaGunda
manager: kfile
ms.assetid: a473ea0a-3eaa-4e5b-aaa1-fec7e9069f20
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/21/2018
ms.author: sngun
ms.openlocfilehash: b36833a9fe35f14eba6d9e397eb0958b716b313b
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="build-an-iot-solution-by-using-stream-analytics"></a>Vytvoření řešení IoT pomocí služby Stream Analytics

## <a name="introduction"></a>Úvod
V tomto kurzu se dozvíte, jak získat přehledy v reálném čase z vašich dat pomocí Azure Stream Analytics. Datové proudy dat, například klikněte na tlačítko-datové proudy, protokoly a události generované zařízení mohou vývojáři kombinovat snadno s staré záznamy nebo referenční data odvození informací o podniku. Jako výpočetní služba plně spravovaná, v reálném čase datový proud, který je hostován v Microsoft Azure Azure Stream Analytics poskytuje integrované odolnost proti chybám, s nízkou latencí a škálovatelnost, které jste si a spustit v minutách.

Po dokončení tohoto kurzu, budete moci:

* Seznamte se s portálem Azure Stream Analytics.
* Nakonfigurujte a nasaďte úlohu streamování.
* Vyjádřete reálného problémy a řešení je pomocí dotazovacího jazyka pro Stream Analytics.
* Vývoj streamování řešení pro vaše zákazníky pomocí služby Stream Analytics s jistotou.
* Sledování a protokolování prostředí používejte k řešení potíží.

## <a name="prerequisites"></a>Požadavky
Budete potřebovat k dokončení tohoto kurzu následující požadavky:

* Nejnovější verzi [prostředí Azure PowerShell](/powershell/azure/overview)
* 2017 Visual Studio 2015, nebo bezplatnou [Visual Studio Community](https://www.visualstudio.com/products/visual-studio-community-vs.aspx)
* [Předplatného Azure](https://azure.microsoft.com/pricing/free-trial/)
* Oprávnění správce na počítači
* Stáhnout zdrojový kód pro TollApp analýza dat snímačů z [úložiště azure. stream analytics GitHub.](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/TollApp). Toto úložiště obsahuje ukázková data a dotazy, které budete používat v dalších částech. 

## <a name="scenario-introduction-hello-toll"></a>Scénář Úvod: "Hello, Projedou!"
Stanice projedou je běžné jevu. Narazíte je na mnoha rychlostních, mosty a tunely po celém světě. Každé stanici projedou má více kabin projedou. Na ruční kabin zastavíte platit projedou k průvodcem. Senzor nad každý stánek na automatizované kabin kontroluje RFID kartu, která je opatřit čelního skla z vaší vehicle, jak předat stánek projedou. Je snadné k vizualizaci průchod vozidel přes tyto stanice projedou jako datového proudu událostí za které zajímavé operace lze provádět.

![Obrázek automobilů na projedou kabin](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image1.jpg)

## <a name="incoming-data"></a>Příchozí data
V tomto kurzu pracuje se dvěma datových proudů. Nainstalovaných ve vstupu a ukončení stanice projedou vytvořit první datového proudu. Druhý datový proud je statický vyhledávání datovou sadu, která má vehicle registrační data.

### <a name="entry-data-stream"></a>Vstupní datový proud
Vstupní datový proud obsahuje informace o aut při vstupu projedou stanice.

| TollID | EntryTime | LicensePlate | Stav | Make | Model | VehicleType | VehicleWeight | Projedou | Značka |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 |2014-09-10 12:01:00.000 |JNB 7001 |NY |Honda |CRV |1 |0 |7 | |
| 1 |2014-09-10 12:02:00.000 |YXZ 1001 |NY |Toyota |Camry |1 |0 |4 |123456789 |
| 3 |2014-09-10 12:02:00.000 |ABC 1004 |CT |Ford |Taurus |1 |0 |5 |456789123 |
| 2 |2014-09-10 12:03:00.000 |XYZ 1003 |CT |Toyota |Corolla |1 |0 |4 | |
| 1 |2014-09-10 12:03:00.000 |BNJ 1007 |NY |Honda |CRV |1 |0 |5 |789123456 |
| 2 |2014-09-10 12:05:00.000 |CDE 1007 |NI |Toyota |4x4 |1 |0 |6 |321987654 |

Zde je stručný popis sloupce:

| Sloupec | Popis |
| --- | --- |
| TollID |ID stánek projedou, která jednoznačně identifikuje stánek projedou |
| EntryTime |Datum a čas položky vehicle k stánek projedou v UTC |
| LicensePlate |Počet registrační nástroj |
| Stav |Stav v USA |
| Make |Výrobce daného automobilu |
| Model |Číslo modelu daného automobilu |
| VehicleType |Buď 1 pro osobní vozidel či 2 pro komerční vozidel |
| WeightType |Vehicle váhy v tunách; 0 pro osobní vozidel |
| Projedou |Hodnota projedou v USD |
| Značka |E-Tag na automobilu, který zautomatizuje platebních; prázdné, kde je platba bylo provedeno ručně |

### <a name="exit-data-stream"></a>Výstupní datový proud
Výstupní datový proud obsahuje informace o aut ponechat projedou stanice.

| **TollId** | **ExitTime** | **LicensePlate** |
| --- | --- | --- |
| 1 |2014-09-10T12:03:00.0000000Z |JNB 7001 |
| 1 |2014-09-10T12:03:00.0000000Z |YXZ 1001 |
| 3 |2014-09-10T12:04:00.0000000Z |ABC 1004 |
| 2 |2014-09-10T12:07:00.0000000Z |XYZ 1003 |
| 1 |2014-09-10T12:08:00.0000000Z |BNJ 1007 |
| 2 |2014-09-10T12:07:00.0000000Z |CDE 1007 |

Zde je stručný popis sloupce:

| Sloupec | Popis |
| --- | --- |
| TollID |ID stánek projedou, která jednoznačně identifikuje stánek projedou |
| ExitTime |Datum a čas ukončení nástroj z projedou stánek v UTC |
| LicensePlate |Počet registrační nástroj |

### <a name="commercial-vehicle-registration-data"></a>Vehicle komerční registrační data
Tento kurz používá statický snímek databáze komerční vehicle registrace.

| LicensePlate | RegistrationId | Vypršela platnost |
| --- | --- | --- |
| SVT 6023 |285429838 |1 |
| XLZ 3463 |362715656 |0 |
| BAC 1005 |876133137 |1 |
| RIV 8632 |992711956 |0 |
| SNY 7188 |592133890 |0 |
| ELH 9896 |678427724 |1 |

Zde je stručný popis sloupce:

| Sloupec | Popis |
| --- | --- |
| LicensePlate |Počet registrační nástroj |
| RegistrationId |ID registrace vehicle |
| Vypršela platnost |Stav registrace nástroj: 0, pokud je aktivní, vehicle registrace 1, pokud platnost registrace |

## <a name="set-up-the-environment-for-azure-stream-analytics"></a>Nastavení prostředí pro Azure Stream Analytics
K dokončení tohoto kurzu potřebujete předplatné Microsoft Azure. Společnost Microsoft nabízí bezplatné zkušební verze pro služby Microsoft Azure.

Pokud účet Azure nemáte, můžete [požadavku bezplatná zkušební verze](http://azure.microsoft.com/pricing/free-trial/).

> [!NOTE]
> Chcete-li zaregistrovat k bezplatné zkušební verzi, musíte mobilní zařízení, které může přijímat textové zprávy a platná platební karta.
> 
> 

Ujistěte se, postupujte podle kroků v části "Vyčištění účtu Azure" na konci tohoto článku, tak, aby bylo možné provést doporučené použití Azure kreditu.

## <a name="provision-azure-resources-required-for-the-tutorial"></a>Zřízení prostředků Azure, vyžaduje se pro tento kurz
Tento kurz vyžaduje dva události rozbočovače pro příjem *položka* a *ukončete* datových proudů. Azure SQL Database výstupy výsledky úlohy Stream Analytics. Azure Storage ukládá referenční data o vehicle registrace.

Setup.ps1 skript ve složce TollApp na Githubu slouží k vytvoření všechny požadované prostředky. V zájmu čas doporučujeme ho spustíte. Pokud chcete získat další informace o tom, jak nakonfigurovat tyto prostředky na portálu Azure, podívejte se na Příloha "Konfigurace kurz prostředků na portálu Azure".

Stáhněte a uložte v podporu [TollApp](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TollApp/TollApp.zip) složky a soubory.

Otevřete **Microsoft Azure PowerShell** okno *jako správce*. Pokud ještě nemáte prostředí Azure PowerShell, postupujte podle pokynů v [instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/overview) k její instalaci.

Protože Windows automaticky blokuje soubory .exe, .dll a .ps1, budete muset nastavte zásady spouštění, před spuštěním skriptu. Zkontrolujte, že je spuštěný okno Azure PowerShell *jako správce*. Spustit **Set-ExecutionPolicy unrestricted**. Po zobrazení výzvy zadejte **Y**.

![Snímek obrazovky "Set-ExecutionPolicy unrestricted" spuštěné v okno Azure PowerShell](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image2.png)

Spustit **Get-ExecutionPolicy** a ujistěte se, že příkaz fungovala.

![Snímek obrazovky "Get-ExecutionPolicy" spuštěné v okno Azure PowerShell](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image3.png)

Přejděte do adresáře, který má generátor aplikace a skripty.

![Snímek obrazovky "cd .\TollApp\TollApp" spuštěna v okně prostředí Azure PowerShell](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image4.png)

Typ **.\\ Setup.ps1** nastavení účtu Azure, vytvořit a nakonfigurovat všechny požadované prostředky a začít generovat události. Skript náhodně oblasti převezme vytvoření vašich prostředků. K explicitnímu zadání oblast, můžete předat **-umístění** parametr jako v následujícím příkladu:

**. \\Setup.ps1-umístění "Střed USA"**

![Snímek obrazovky stránky Azure přihlášení](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image5.png)

Skript se otevře **přihlásit** stránky pro Microsoft Azure. Zadejte přihlašovací údaje účtu.

> [!NOTE]
> Pokud má váš účet přístup k více předplatných, budete vyzváni k zadání názvu odběru, který chcete použít pro tento kurz.
> 
> 

Skript může trvat několik minut. Po dokončení výstup by měl vypadat jako na následujícím snímku obrazovky.

![Snímek obrazovky výstupu skriptu v okně prostředí Azure PowerShell](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image6.PNG)

Zobrazí se také další okno, který je podobný na následujícím snímku obrazovky. Tato aplikace je odesílání událostí do centra událostí Azure, který je vyžadován ke spuštění tohoto kurzu. Ano nezadávejte zastavte aplikaci, nebo toto okno zavřít, dokud nedokončíte tohoto kurzu.

![Snímek obrazovky "Odesílající event hub data"](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image7.png)

Nyní byste měli mít vašich prostředků na portálu Azure nyní zobrazeny. Přejděte na <https://portal.azure.com>a přihlaste se pomocí přihlašovacích údajů účtu. Všimněte si, že aktuálně některé funkce využívá portálu classic. Tyto kroky bude jasně označen.

### <a name="azure-event-hubs"></a>Azure Event Hubs

Z portálu Azure klikněte na tlačítko **další služby** na spodní části podokna levém správy. Typ **služby Event hubs** v v poli se zobrazí nový obor názvů centra událostí, který začíná **tolldata**. Tento namesapce je vytvořen skriptem Setup.ps1. Zobrazí se dva centra událostí s názvem **položka** a **ukončete** vytvořit v tomto oboru názvů.

### <a name="azure-storage-container"></a>Kontejner úložiště Azure
Z portálu Azure přejděte do účtů úložiště, měli byste vidět účet úložiště, který začíná **tolldata**. Klikněte **tolldata** kontejneru zobrazíte nahrávaný soubor JSON, který má vehicle registrační data.

### <a name="azure-sql-database"></a>Azure SQL Database
1. Přejděte zpět na portál Azure na první kartě, kterou jste otevřeli v prohlížeči. Klikněte na tlačítko **databází SQL** na levé straně na portálu Azure SQL database, která se použije v tomto kurzu a klikněte na tlačítko Zobrazit **tolldatadb**.
   
    ![Snímek obrazovky vytvořené databáze SQL](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image15.png)
2. Zkopírujte název serveru bez číslo portu (*servername*. database.windows.net, např.).
    ![Snímek obrazovky vytvoření databáze SQL db](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image15a.png)

## <a name="connect-to-the-database-from-visual-studio"></a>Připojení k databázi ze sady Visual Studio
Pomocí sady Visual Studio pro přístup k výsledky dotazu v databázi výstup.

Připojení k databázi SQL (cíl) ze sady Visual Studio:

1. Otevřete Visual Studio a pak klikněte na tlačítko **nástroje** > **připojit k databázi**.
2. Pokud se zobrazí dotaz, klikněte na tlačítko **Microsoft SQL Server** jako zdroj dat.
   
    ![Dialogové okno Změnit zdroj dat](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image16.png)
3. V **název serveru** pole, vložte název, který jste zkopírovali v předchozí části z portálu Azure (to znamená, *servername*. database.windows.net).
4. Klikněte na tlačítko **použít ověřování systému SQL Server**.
5. Zadejte **tolladmin** v **uživatelské jméno** pole a **123toll!** v **heslo** pole.
6. Klikněte na tlačítko **vyberte nebo zadejte název databáze**a vyberte **TollDataDB** jako databáze.
   
    ![Přidat dialogové okno připojení](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image17.jpg)
7. Klikněte na **OK**.
8. Otevřete Průzkumníka serveru.
   
    ![Průzkumník serveru](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image18.png)
9. V tématu čtyři tabulek v databázi TollDataDB.
   
    ![Tabulky v databázi TollDataDB](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image19.jpg)

## <a name="event-generator-tollapp-sample-project"></a>Generátor událostí: TollApp ukázkový projekt
Odesílat události pomocí programu TollApp ukázkové aplikace se automaticky spustí skript prostředí PowerShell. Nemusíte provádět žádné další kroky.

Ale pokud vás zajímají podrobnosti implementace najdete zdrojový kód aplikace TollApp v Githubu [ukázky/TollApp](https://aka.ms/azure-stream-analytics-toll-source).

![Snímek obrazovky ukázkový kód zobrazený v sadě Visual Studio](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image20.png)

## <a name="create-a-stream-analytics-job"></a>Vytvoření úlohy Stream Analytics
1. Na portálu Azure klikněte na zelenou znaménko plus v levém horním rohu stránky vytvořte novou úlohu služby Stream Analytics. Vyberte **Intelligence + analýzy** a pak klikněte na **úlohy služby Stream Analytics**.
   
    ![Tlačítko Nová](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image21.png)
2. Zadejte název úlohy, ověření předplatného opravte a potom vytvořit novou skupinu prostředků ve stejné oblasti jako úložiště centra událostí (výchozí hodnota je jihu USA pro skript).
3. Klikněte na tlačítko **připnout na řídicí panel** a potom **vytvořit** v dolní části stránky.
   
    ![Vytvoření úlohy Stream Analytics možnost](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image22.png)

## <a name="define-input-sources"></a>Definování vstupních zdrojů
1. Úloha vytvoří a otevře stránka úlohy. Nebo můžete kliknout na úlohy vytvořené analytics na řídicím panelu portálu.

2. Klikněte **VSTUPY** a definujte zdrojová data.
   
    ![Na kartě vstupy](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image24.png)
3. Klikněte na tlačítko **přidat vstup**.
   
    ![Přidat vstup možnost](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image25.png)
4. Zadejte **EntryStream** jako **vstupní ALIAS**.
5. Typ zdroje je **datový proud**
6. Zdroj je **centra událostí**.
7. **Obor názvů sběrnice** TollData, jeden v rozevírací by měla být mimo provoz.
8. **Název centra událostí** musí být nastavena na **položka**.
9. **Název zásady centra událostí*je **RootManageSharedAccessKey** (výchozí hodnota).
10. Vyberte **JSON** pro **formát SERIALIZACE událostí** a **UTF8** pro **kódování**.
   
    Nastavení bude vypadat jako:
   
    ![Nastavení centra událostí](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image28.png)

10. Klikněte na tlačítko **vytvořit** v dolní části stránky průvodce ukončíte.
    
    Teď, když jste vytvořili vstupní datový proud, postupujte podle stejných kroků vytvořte výstupní datový proud. Ujistěte se, že zadejte hodnoty jako na následujícím snímku obrazovky.
    
    ![Nastavení pro výstupní datový proud](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image31.png)
    
    Jste definovali dva vstupní datové proudy:
    
    ![Definované vstupní datové proudy na portálu Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image32.png)
    
    V dalším kroku přidáte referenčního datového vstupu pro objekt blob, který obsahuje car registrační data.
11. Klikněte na tlačítko **přidat**a pak opakujte stejný postup pro datový proud vstupy ale vyberte **referenční DATA** místo **datový proud** a **vstupní Alias** je **registrace**.

12. účet úložiště, který začíná **tolldata**. Název kontejneru musí být **tolldata**a **vzorek cesty** by měla být **registration.json**. Tento název souboru je velká a malá písmena a musí být **malá**.
    
    ![Nastavení úložiště blog](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image34.png)
13. Klikněte na tlačítko **vytvořit** ukončíte průvodce.

Nyní jsou definovány všechny vstupy.

## <a name="define-output"></a>Definujte výstupní
1. V podokně přehled úlohy Stream Analytics vyberte **výstupy**.
   
    ![Karta Výstup a možnost "Přidat výstup"](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image37.png)
2. Klikněte na tlačítko **Add** (Přidat).
3. Nastavte **alias pro výstup** k "výstupní" a potom **jímky** k **databáze SQL**.
3. Vyberte název serveru, který se používal v části "Připojení k databázi ze sady Visual Studio" v článku. Název databáze je **TollDataDB**.
4. Zadejte **tolladmin** v **uživatelské jméno** pole, **123toll!** v **heslo** pole, a **TollDataRefJoin** v **tabulky** pole.
   
    ![Nastavení databáze SQL](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image38.png)
5. Klikněte na možnost **Vytvořit**.

## <a name="azure-stream-analytics-query"></a>Azure Stream analytics dotazu
**Dotazu** karta obsahuje dotaz SQL, který transformuje příchozí data.

![Dotaz, přidat na kartu dotaz](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image39.png)

V tomto kurzu se pokusí odpovězte několik obchodní otázky, které se vztahují k datům a konstrukce Stream Analytics dotazy, které lze použít v Azure Stream Analytics k poskytování příslušných odpovědí pro výběr poplatků.

Než začnete vaše první práce Stream Analytics, podíváme se na několik scénářů a syntaxe dotazu.

## <a name="introduction-to-azure-stream-analytics-query-language"></a>Úvod do Azure Stream Analytics query language
- - -
Řekněme, že je potřeba počítat počet vozidel, které projedou stánek zadejte. Protože to je nepřetržitý proud událostí, musíte definovat "v časovém intervalu." Pojďme upravit dotaz a "jak množství prostředků zadejte projedou stánek každé tři minuty?". To se obvykle označuje jako počet přeskakující.

Podívejme se na Azure Stream Analytics dotaz, který odpoví na tuto otázku:

    SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count
    FROM EntryStream TIMESTAMP BY EntryTime
    GROUP BY TUMBLINGWINDOW(minute, 3), TollId

Jak vidíte, Azure Stream Analytics používá dotazovací jazyk, který je například SQL a přidává několik rozšíření k určení souvisejících s časem aspekty dotazu.

Další podrobnosti najdete v tématu o [Správa času](https://msdn.microsoft.com/library/azure/mt582045.aspx) a [Oddílová](https://msdn.microsoft.com/library/azure/dn835019.aspx) konstrukce použitý v dotazu z webu MSDN.

## <a name="testing-azure-stream-analytics-queries"></a>Testování dotazů Azure Stream Analytics
Teď, když jste napsali svůj první dotaz Azure Stream Analytics, je třeba otestovat pomocí ukázkových datových souborů umístěné ve složce TollApp v následující cestě:

**.. \\TollApp\\TollApp\\dat**

Tato složka obsahuje následující soubory:

* Entry.json
* Exit.json
* Registration.json

## <a name="question-1-number-of-vehicles-entering-a-toll-booth"></a>Otázka č. 1: Počet vozidel zadávání stánek projedou
1. Otevřete portál Azure a přejděte na vytvořenou úlohu Azure Stream Analytics. Klikněte **dotazu** kartě a vložte dotaz z předchozí části.

2. K ověření tohoto dotazu proti ukázková data, nahrát data do vstupu EntryStream kliknutím... symbolů a výběr **nahrát ukázková data ze souboru**.

    ![Snímek obrazovky Entry.json souboru](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image41.png)
3. V podokně se zobrazí, vyberte soubor (Entry.json) na místním počítači a klikněte na tlačítko **OK**. **Test** ikonu teď osvětlení a možné klepnout.
   
    ![Snímek obrazovky Entry.json souboru](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image42.png)
3. Ověřte, že výstup dotaz podle očekávání:
   
    ![Výsledky testu](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image43.png)

## <a name="question-2-report-total-time-for-each-car-to-pass-through-the-toll-booth"></a>Otázka č. 2: Sestava celkovou dobu pro každý car předávat stánek projedou
Průměrná doba, které je nutné pro automobilu předávat projedou pomáhá vyhodnocení efektivitu proces a zkušeností zákazníků.

Najít celkovou dobu, je nutné připojit datový proud EntryTime s ExitTime datového proudu. Datové proudy pro TollId a LicencePlate sloupce se připojí. **Připojení** operátor vyžaduje, abyste zadejte dočasné volnost, který popisuje přijatelný časový rozdíl mezi připojené k události. Budete používat **DATEDIFF** funkce k určení, že události musí být delší než 15 minut od sebe navzájem. Také použijete **DATEDIFF** funkce ukončíte a časy položku k výpočtu skutečnou času stráví automobilu projedou stanice. Všimněte si rozdílu použití **DATEDIFF** při použití v **vyberte** příkaz ne **připojení** podmínku.

    SELECT EntryStream.TollId, EntryStream.EntryTime, ExitStream.ExitTime, EntryStream.LicensePlate, DATEDIFF (minute , EntryStream.EntryTime, ExitStream.ExitTime) AS DurationInMinutes
    FROM EntryStream TIMESTAMP BY EntryTime
    JOIN ExitStream TIMESTAMP BY ExitTime
    ON (EntryStream.TollId= ExitStream.TollId AND EntryStream.LicensePlate = ExitStream.LicensePlate)
    AND DATEDIFF (minute, EntryStream, ExitStream ) BETWEEN 0 AND 15

1. K otestování tohoto dotazu, aktualizovat dotaz na **dotazu** pro úlohu. Přidejte soubor testu pro **ExitStream** stejně jako **EntryStream** byla zadaná výše.
   
2. Klikněte na tlačítko **Test**.

3. Zaškrtněte políčko Zobrazit výstup a otestujte dotaz:
   
    ![Výstup testu](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image45.png)

## <a name="question-3-report-all-commercial-vehicles-with-expired-registration"></a>Otázka č. 3: Sestavy všech komerční vozidel s vypršenou platností registrace
Azure Stream Analytics můžete použít statické snímky dat spojení s dočasné datové proudy. K předvedení tato možnost, použijte následující ukázkový dotaz.

Pokud komerční vehicle je registrován ve společnosti projedou, můžete předat prostřednictvím stánek projedou bez zastavení pro kontroly. Registrace komerční Vehicle vyhledávací tabulky budete používat k identifikaci všech vozidel komerční, jejichž platnost vypršela registrace.

```
SELECT EntryStream.EntryTime, EntryStream.LicensePlate, EntryStream.TollId, Registration.RegistrationId
FROM EntryStream TIMESTAMP BY EntryTime
JOIN Registration
ON EntryStream.LicensePlate = Registration.LicensePlate
WHERE Registration.Expired = '1'
```

Při testování dotazu pomocí referenční data, musíte definovat vstupní zdroj pro referenční data, která jste již dokončili.

K otestování tohoto dotazu, vložte dotaz do **dotazu** , klikněte na **testování**a určete dvou vstupních zdrojů a registraci vzorová data a klikněte na tlačítko **testování**.  
   
![Výstup testu](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image46.png)

## <a name="start-the-stream-analytics-job"></a>Spustit úlohu služby Stream Analytics
Nyní je čas na dokončení konfigurace a spustit úlohu. Uložení dotazu z 3 otázku, která vytvoří výstupu, který odpovídá schématu **TollDataRefJoin** výstupní tabulka.

Přejděte do úlohy **řídicí panel**a klikněte na tlačítko **spustit**.

![Snímek obrazovky tlačítka Start na řídicím panelu úlohy](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image48.png)

V dialogovém okně, které se otevře, změňte **spustit výstup** čas k **vlastní čas**. Změňte hodinu na jednu hodinu před aktuálním časem. Tato změna zajistí, že všechny události z centra událostí jsou zpracovány od spuštění k vygenerování události na začátku tohoto kurzu. Klikněte na tlačítko **spustit** tlačítko Spustit úlohu.

![Výběr vlastních času](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image49.png)

Spouštění úlohy může trvat několik minut. Stav můžete zobrazit na stránce nejvyšší úrovně pro Stream Analytics.

![Snímek obrazovky stav úlohy](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image50.png)

## <a name="check-results-in-visual-studio"></a>Výsledky kontroly v sadě Visual Studio
1. Otevřete Průzkumníka serveru Visual Studia a klikněte pravým tlačítkem myši **TollDataRefJoin** tabulky.
2. Klikněte na tlačítko **zobrazit Data tabulky** na zobrazení výstupu úlohy.
   
    ![Výběr "Zobrazit tabulku Data" v Průzkumníku serveru](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image51.jpg)

## <a name="scale-out-azure-stream-analytics-jobs"></a>Horizontální navýšení kapacity Azure Stream Analytics úlohy
Azure Stream Analytics je určena pro pružně škálování tak, aby ho může zpracovávat velké množství dat. Můžete použít Azure Stream Analytics dotaz **PARTITION BY** klauzule systému říct, že bude tento krok škálovat. **PartitionId** je speciální sloupec, který přidá systému tak, aby odpovídaly ID oddílu vstupu (Centrum událostí).

    SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*)AS Count
    FROM EntryStream TIMESTAMP BY EntryTime PARTITION BY PartitionId
    GROUP BY TUMBLINGWINDOW(minute,3), TollId, PartitionId

1. Zastavení aktuální úlohy, aktualizaci dotazu ve **dotazu** a otevřete **nastavení** zařízení na řídicím panelu úloh. Klikněte na tlačítko **škálování**.
   
    **JEDNOTEK STREAMING** definovat množství výpočetního výkonu, který může přijímat úlohy.
2. Změňte rozevíracího z 1 z verze 6.
   
    ![Snímek obrazovky výběru 6 jednotky streamování](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image52.png)
3. Přejděte na **výstupy** kartě a změňte název tabulky SQL na **TollDataTumblingCountPartitioned**.

Je-li spustit úlohu nyní může Azure Stream Analytics rozložení práce mezi víc zdrojích výpočtů a dosáhnout lepší propustnosti. Upozorňujeme, že aplikace TollApp také odesílá události podle TollId na oddíly.

## <a name="monitor"></a>Monitorování
**Monitorování** oblast obsahuje statistiku spuštěná úloha. Prvním konfigurace je potřeba k použití úložiště účet ve stejné oblasti (název poplatků jako zbytek tohoto dokumentu).   

![Snímek obrazovky monitorování](media/stream-analytics-build-an-iot-solution-using-stream-analytics/monitoring.png)

Dostanete **protokoly aktivity** z řídicího panelu úloha **nastavení** také oblasti.


## <a name="conclusion"></a>Závěr
V tomto kurzu jste zavedly ve službě Azure Stream Analytics. Je prokázat, jak nakonfigurovat vstupy a výstupy úlohy Stream Analytics. Pomocí dat Projedou scénář, kurz popsaných nejběžnějších problémů, které jsou vyvolány v prostoru dat v provozu a jak lze vyřešit pomocí jednoduchého dotazy podobné jazyku SQL v Azure Stream Analytics. Tento kurz popisuje konstrukce rozšíření SQL pro práci s dočasná data. Vám ukázal, jak propojit datové proudy, jak zlepšit komunikaci oddělení datový proud s statická referenční data a postup škálování dotazů k dosažení vyšší propustnost.

I když tento kurz představuje dobrou úvod, není kompletní, a to jakýmkoli způsobem. Můžete najít další typy dotazů pomocí jazyka SAQL v [dotaz příkladů běžných vzorů využití Stream Analytics](stream-analytics-stream-analytics-query-patterns.md).
Odkazovat [online dokumentaci](https://azure.microsoft.com/documentation/services/stream-analytics/) Další informace o Azure Stream Analytics.

## <a name="clean-up-your-azure-account"></a>Vyčištění účtu Azure
1. Zastavte úlohu služby Stream Analytics na portálu Azure.
   
    Skript Setup.ps1 vytvoří dvě centrům událostí a databázi SQL. Následující pokyny vám pomůžou vyčištění prostředků na konci tohoto kurzu.
2. V okně prostředí PowerShell, zadejte **.\\ CleanUp.ps1** spustit skript, který odstraní prostředky v tomto kurzu použili.
   
   > [!NOTE]
   > Prostředky jsou identifikovány názvem. Ujistěte se, že jednotlivé položky pečlivě zkontrolovat před potvrzením odebrání.
   > 
   > 


