---
title: Připojení k Azure Cosmos DB pomocí analytických nástrojů BI
description: Zjistěte, jak vytvořit tabulky a zobrazení normalizovaná data lze zobrazit v BI a data analytický software pomocí ovladače Azure Cosmos DB ODBC.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: sngun
ms.openlocfilehash: 8be17f0b624c5c34709fb420adb434b77dbc0d91
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721077"
---
# <a name="connect-to-azure-cosmos-db-using-bi-analytics-tools-with-the-odbc-driver"></a>Připojení ke službě Azure Cosmos DB pomocí nástroje pro analýzu BI pomocí ovladače ODBC

Ovladač Azure Cosmos DB ODBC umožňuje připojení k Azure Cosmos DB pomocí nástrojů BI analytics, například SQL Server Integration Services, Power BI Desktopu a tableau. představují tak, že můžete analyzovat a vytvářet vizualizace dat služby Azure Cosmos DB v těchto řešeních.

Ovladač ODBC pro Azure Cosmos DB je kompatibilní s rozhraním ODBC 3.8 a podporuje syntaxi ANSI SQL-92. Tento ovladač nabízí bohaté funkce, které vám pomůžou znovu normalizovat data ve službě Azure Cosmos DB. S využitím tohoto ovladače můžete data ve službě Azure Cosmos DB reprezentovat jako tabulky a zobrazení. Tento ovladač umožňuje provádět operace SQL s tabulkami a zobrazeními, včetně seskupení podle dotazů a operací vložení, aktualizace a odstranění.

> [!NOTE]
> Připojení ke službě Azure Cosmos DB pomocí ovladače ODBC aktuálně podporuje pouze účty SQL API služby Azure Cosmos DB.

## <a name="why-do-i-need-to-normalize-my-data"></a>Proč je potřeba údaje normalizovat?
Azure Cosmos DB je schemaless databáze, která umožňuje rychlý vývoj aplikací a možnost iterovat datové modely aplikace bez se omezuje na striktní schéma. Jedna databáze Azure Cosmos může obsahovat dokumenty JSON různých struktur. To je skvělé pro rychlý vývoj aplikací, ale pokud chcete analyzovat a vytváření sestav svá data pomocí analýzy dat a nástroje BI, často je potřeba data sloučí a dodržovat určité schéma.

To je, kde je k dispozici ovladač ODBC ve. Pomocí ovladače ODBC, můžete teď znovu normalizovat data ve službě Azure Cosmos DB do tabulek a zobrazení, které odpovídají vaší analýzy dat a sestav. Renormalized schémata mít vliv na podkladová data a ne omezit vývojářům dodržovat je. Místo toho umožňují využívat kompatibilní se standardem ODBC nástroje pro přístup k datům. Takže teď vaše databáze Azure Cosmos nebude jenom oblíbená pro váš vývojový tým, ale vaše analytiky dat je uvidí.

Pusťme se do práce pomocí ovladače ODBC.

## <a id="install"></a>Krok 1: instalace ovladače Azure Cosmos DB ODBC

1. Stáhněte si ovladače pro vaše prostředí:

    | Instalační program | Podporované operační systémy| 
    |---|---| 
    |[Microsoft Azure Cosmos DB rozhraní ODBC 64-bit. msi](https://aka.ms/cosmos-odbc-64x64) pro 64-bit Windows| 64bitová verze systému Windows 8.1 nebo novější, Windows 8, Windows 7, Windows Server 2012 R2, Windows Server 2012 a Windows Server 2008 R2.| 
    |[Microsoft Azure Cosmos DB rozhraní ODBC 32x64-bit. msi](https://aka.ms/cosmos-odbc-32x64) pro 32-bit v 64 bitovém systému Windows| 64bitová verze systému Windows 8.1 nebo novější, Windows 8, Windows 7, Windows XP, Windows Vista, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 a Windows Server 2003.| 
    |[Microsoft Azure Cosmos DB rozhraní ODBC 32-bit. msi](https://aka.ms/cosmos-odbc-32x32) pro 32-bit Windows|32bitové verze systému Windows 8.1 nebo novější, Windows 8, Windows 7, Windows XP a Windows Vista.|

    Spusťte soubor MSI místně, což spustí **Průvodce instalací ovladače ODBC Microsoft Azure Cosmos DB**. 

1. Dokončení Průvodce instalací pomocí výchozí vstup do nainstalujte ovladač ODBC.

1. V počítači otevřete aplikaci **Správce zdrojů dat ODBC** . To můžete provést tak, že do vyhledávacího pole Windows zadáte **zdroje dat ODBC** . 
    Instalaci ovladače můžete ověřit kliknutím na kartu **ovladače** a zajistěte, aby byl uvedený **Microsoft Azure Cosmos DB ovladač ODBC** .

    ![Azure Cosmos DB správce zdrojů dat ODBC](./media/odbc-driver/odbc-driver.png)

## <a id="connect"></a>Krok 2: připojení k databázi Azure Cosmos

1. Po [instalaci ovladače Azure Cosmos DB ODBC](#install)v okně **Správce zdrojů dat ODBC** klikněte na **Přidat**. Můžete vytvořit uživatele nebo název DSN systému. V tomto příkladu vytvoříte uživatelské DSN.

1. V okně **vytvořit nový zdroj dat** vyberte **Microsoft Azure Cosmos DB ovladač ODBC**a pak klikněte na **Dokončit**.

1. V okně **Azure Cosmos DB ODBC ovladače SDN nastavení** zadejte následující informace: 

    ![Azure Cosmos DB ODBC ovladač DSN okno](./media/odbc-driver/odbc-driver-dsn-setup.png)
    - **Název zdroje dat**: vlastní popisný název DSN ODBC. Tento název je jedinečný pro váš účet Azure Cosmos DB, tak pojmenujte ho správně Pokud máte více účtů.
    - **Popis**: stručný popis zdroje dat.
    - **Host**: identifikátor URI pro váš účet Azure Cosmos DB. Můžete načíst to ze stránky klíče služby Azure Cosmos DB na webu Azure Portal, jak je znázorněno na následujícím snímku obrazovky. 
    - **Přístupový klíč**: primární nebo sekundární klíč, který je jen pro čtení a zápis, nebo jen pro čtení ze stránky Azure Cosmos DB klíče v Azure Portal, jak je znázorněno na následujícím snímku obrazovky. Doporučujeme že použít klíč jen pro čtení, pokud název zdroje dat se používá pro zpracování dat jen pro čtení a vytváření sestav.
    Stránka klíčů Azure Cosmos DB ![](./media/odbc-driver/odbc-cosmos-account-keys.png)
    - **Šifrovat přístupový klíč pro**: vyberte nejlepší volbu na základě uživatelů tohoto počítače. 
    
1. Klikněte na tlačítko **test** a ujistěte se, že se můžete připojit k účtu Azure Cosmos DB. 

1.  Klikněte na **Rozšířené možnosti** a nastavte následující hodnoty:
    *  **REST API verze**: vyberte [verzi REST API](https://docs.microsoft.com/rest/api/cosmos-db/) pro vaše operace. Výchozí hodnota je 2015-12-16. Pokud máte kontejnery s [velkými klíči oddílů](large-partition-keys.md) a vyžadujete REST API verze 2018-12-31:
        - Zadejte **2018-12-31** pro REST API verzi.
        - V nabídce **Start** zadejte příkaz regedit a vyhledejte a otevřete aplikaci **Editor registru** .
        - V editoru registru přejděte na cestu: **počítač \ HKEY_LOCAL_MACHINE \software\odbc\odbc. INI**
        - Vytvořte nový podklíč se stejným názvem jako název DSN, např. "účet contoso ODBC DSN".
        - Přejděte do podklíče "účet" ODBC DSN "účtu contoso.
        - Kliknutím pravým tlačítkem přidáte novou **řetězcovou** hodnotu:
            - Název hodnoty: **IgnoreSessionToken**
            - Údaj hodnoty: **1**
            ![nastavení editoru registru](./media/odbc-driver/cosmos-odbc-edit-registry.png)
    - **Konzistence dotazů**: vyberte [úroveň konzistence](consistency-levels.md) pro vaše operace. Výchozí hodnota je relace.
    - **Počet opakování**: zadejte počet opakování operace, pokud se počáteční žádost nedokončila z důvodu omezení rychlosti služby.
    - **Soubor schématu**: tady máte několik možností.
        - Ve výchozím nastavení zachová tuto položku jako (prázdná), ovladač prohledá první stránku dat pro všechny kontejnery a určí schéma každého kontejneru. Toto je známé jako mapování kontejneru. Bez souboru schématu definice ovladač nemá k provedení kontroly pro každou relaci ovladač a může mít za následek vyšší dobu spuštění aplikace pomocí DSN. Doporučujeme vždy přiřadit soubor schématu pro zdroje dat DSN.
        - Pokud už máte soubor schématu (Možná ho vytvoříte pomocí editoru schémat), můžete kliknout na **Procházet**, přejít k souboru, kliknout na **Uložit**a pak na **OK**.
        - Pokud chcete vytvořit nové schéma, klikněte na tlačítko **OK**a potom v hlavním okně klikněte na **editor schémat** . Pak přejděte k informacím editoru schématu. Po vytvoření nového souboru schématu nezapomeňte přejít zpět do okna **Upřesnit možnosti** a zahrnout nově vytvořený soubor schématu.

1. Po dokončení a zavření okna **Azure Cosmos DB nastavení DSN ovladače ODBC** se nový uživatel DSN přidá na kartu uživatelské DSN.

    ![Nové Azure Cosmos DB název DSN rozhraní ODBC na kartě Uživatelské DSN](./media/odbc-driver/odbc-driver-user-dsn.png)

## <a id="#container-mapping"></a>Krok 3: vytvoření definice schématu pomocí metody mapování kontejnerů

Existují dva typy metod vzorkování, které lze použít: **mapování kontejnerů** nebo **oddělovače tabulek**. Relace vzorkování může využívat jak metody vzorkování, ale každý kontejner může použít pouze konkrétní metodu vzorkování. Následující postup vytvoří schéma pro data v jednom nebo více kontejnerech pomocí metody mapování kontejnerů. Tato metoda vzorkování načítá data na stránce kontejneru pro určení struktury dat. Předává kontejner na straně rozhraní ODBC jako tabulku. Tato metoda vzorkování je efektivní a rychlá, když jsou data v kontejneru homogenní. Pokud kontejner obsahuje heterogenní typ dat, doporučujeme použít [metodu mapování oddělovače tabulky](#table-mapping) , protože poskytuje pružnější způsob vzorkování pro určení datových struktur v kontejneru. 

1. Po dokončení kroků 1-4 v tématu [připojení k databázi Azure Cosmos](#connect)klikněte na **editor schémat** v okně **Nastavení DSN ovladače Azure Cosmos DB ODBC** .

    ![Tlačítko editoru schématu v okně instalace ovladačů název zdroje dat v Azure Cosmos DB ODBC](./media/odbc-driver/odbc-driver-schema-editor.png)
1. V okně **editor schémat** klikněte na **vytvořit nový**.
    V okně **generovat schéma** se zobrazí všechny kontejnery v účtu Azure Cosmos DB. 

1. Vyberte jeden nebo více kontejnerů, které chcete vzorkovat, a potom klikněte na tlačítko **Ukázka**. 

1. Na kartě **návrhové zobrazení** je znázorněna databáze, schéma a tabulka. V zobrazení tabulky vyhledávání zobrazuje sadu vlastností, které jsou spojené s názvy sloupců (název systému SQL, název zdroje atd.).
    Pro každý sloupec, můžete upravit název sloupce SQL, datový typ SQL, SQL délka (Pokud je k dispozici), škálování (Pokud je k dispozici), přesnosti (Pokud je k dispozici) a s možnou hodnotou Null.
    - Pokud chcete tento sloupec z výsledků dotazu vyloučit, můžete nastavit možnost **Skrýt sloupec** na **hodnotu true** . Sloupce označené skrýt sloupec = true nebudou zobrazeny pro výběr a projekce, i když jsou stále součástí schématu. Například můžete skrýt všechny vlastnosti služby Azure Cosmos DB je požadován systém počínaje "_".
    - Sloupec **ID** je jediné pole, které nelze skrýt, protože se používá jako primární klíč v normalizovaném schématu. 

1. Po dokončení definování schématu klikněte na **soubor** | **Uložit**, přejděte do adresáře a uložte schéma a pak klikněte na **Uložit**.

1. Pokud chcete toto schéma použít se zdrojem DSN, otevřete **okno Azure Cosmos DB nastavení DSN ovladače ODBC** (přes Správce zdrojů dat ODBC), klikněte na **Upřesnit možnosti**a pak v poli **schématu** přejděte do uloženého schématu. Ukládání souboru schématu do existující DSN upraví připojení DSN do oboru k datům a struktura definovaná pomocí schématu.

## <a id="table-mapping"></a>Krok 4: vytvoření definice schématu pomocí metody mapování oddělovačů tabulek

Existují dva typy metod vzorkování, které lze použít: **mapování kontejnerů** nebo **oddělovače tabulek**. Relace vzorkování může využívat jak metody vzorkování, ale každý kontejner může použít pouze konkrétní metodu vzorkování. 

Následující postup vytvoří schéma pro data v jednom nebo více kontejnerech pomocí metody mapování **oddělovače tabulky** . Tuto metodu vzorkování doporučujeme použít, pokud vaše kontejnery obsahují heterogenní typ dat. Tuto metodu můžete použít k určení rozsahu vzorkování na sadu atributů a jejich odpovídající hodnoty. Například pokud dokument obsahuje vlastnost "Type", můžete upřesnit rozsah vzorkování na hodnoty této vlastnosti. Konečný výsledek vzorkování by sadu tabulek pro jednotlivé hodnoty pro typ, které jste zadali. Zadejte například = Auto vytvoří tabulku Car při typ = roviny byste mohli vytvořit tabulku roviny.

1. Po dokončení kroků 1-4 v tématu [připojení k databázi Azure Cosmos](#connect)klikněte na **editor schémat** v okně nastavení DSN ovladače Azure Cosmos DB ODBC.

1. V okně **editor schémat** klikněte na **vytvořit nový**.
    V okně **generovat schéma** se zobrazí všechny kontejnery v účtu Azure Cosmos DB. 

1. Vyberte kontejner na kartě **ukázkové zobrazení** ve sloupci **definice mapování** pro kontejner klikněte na **Upravit**. Pak v okně **definice mapování** vyberte možnost **oddělovače tabulky** . Potom udělejte následující:

    a. Do pole **atributy** zadejte název vlastnosti oddělovače. Toto je vlastnost v dokumentu, který chcete určit obor vzorkování pro město, a stiskněte klávesu enter. 

    b. Pokud chcete určit rozsah vzorkování pouze na určité hodnoty pro atribut, který jste zadali výše, vyberte atribut v poli výběr, zadejte hodnotu do pole **hodnota** (např. Praha) a stiskněte klávesu ENTER. Chcete-li přidat více hodnot pro atributy můžete pokračovat. Jenom zkontrolujte, že je vybraný správný atribut, pokud zadáváte hodnoty.

    Například pokud zahrnete hodnotu **atributů** City (město) a chcete omezit tabulku tak, aby zahrnovala pouze řádky s hodnotou města New York a Dubaj, měli byste zadat City do pole atributy a New York a pak Dubaj v poli **hodnoty** .

1. Klikněte na tlačítko **OK**. 

1. Po dokončení definic mapování pro kontejnery, které chcete vzorkovat, v okně **editor schémat** klikněte na položku **Ukázka**.
     Pro každý sloupec, můžete upravit název sloupce SQL, datový typ SQL, SQL délka (Pokud je k dispozici), škálování (Pokud je k dispozici), přesnosti (Pokud je k dispozici) a s možnou hodnotou Null.
    - Pokud chcete tento sloupec z výsledků dotazu vyloučit, můžete nastavit možnost **Skrýt sloupec** na **hodnotu true** . Sloupce označené skrýt sloupec = true nebudou zobrazeny pro výběr a projekce, i když jsou stále součástí schématu. Můžete například skrýt všechny vlastnosti požadované Azure Cosmos DB systému počínaje `_`.
    - Sloupec **ID** je jediné pole, které nelze skrýt, protože se používá jako primární klíč v normalizovaném schématu. 

1. Po dokončení definování schématu klikněte na **soubor** | **Uložit**, přejděte do adresáře a uložte schéma a pak klikněte na **Uložit**.

1. Zpátky v okně **Azure Cosmos DB nastavení DSN ovladače ODBC** klikněte na **Upřesnit možnosti**. Pak v poli **soubor schématu** přejděte do uloženého souboru schématu a klikněte na **OK**. Opětovným kliknutím na tlačítko **OK** uložte název DSN. Název zdroje dat tím uložíte schéma, které jste vytvořili. 

## <a name="optional-set-up-linked-server-connection"></a>(Volitelné) Nastavit připojení odkazovaného serveru

Díky nastavení připojení odkazovaného serveru se můžete dotazovat službu Azure Cosmos DB z SQL Server Management Studio (SSMS).

1. Vytvořte systémový zdroj dat, jak je popsáno v [kroku 2](#connect)s názvem například `SDS Name`.

1. [Nainstalujte SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) a připojte se k serveru. 

1. V editoru dotazů SSMS vytvořte objekt propojeného serveru `DEMOCOSMOS` pro zdroj dat pomocí následujících příkazů. Nahraďte `DEMOCOSMOS` názvem vašeho odkazovaného serveru a `SDS Name` názvem systémového zdroje dat.

    ```sql
    USE [master]
    GO
    
    EXEC master.dbo.sp_addlinkedserver @server = N'DEMOCOSMOS', @srvproduct=N'', @provider=N'MSDASQL', @datasrc=N'SDS Name'
    
    EXEC master.dbo.sp_addlinkedsrvlogin @rmtsrvname=N'DEMOCOSMOS', @useself=N'False', @locallogin=NULL, @rmtuser=NULL, @rmtpassword=NULL
    
    GO
    ```
    
Pokud chcete zobrazit nový název odkazovaný server, aktualizujte seznam propojené servery.

![Odkazovaný Server v aplikaci SSMS](./media/odbc-driver/odbc-driver-linked-server-ssms.png)

### <a name="query-linked-database"></a>Dotazování propojené databáze

K dotazování propojené databáze, zadejte dotaz služby SSMS. V tomto příkladu se dotaz vybere z tabulky v kontejneru s názvem `customers`:

```sql
SELECT * FROM OPENQUERY(DEMOCOSMOS, 'SELECT *  FROM [customers].[customers]')
```

Spusťte dotaz. Výsledek by měl být podobný tomuto:

```
attachments/  1507476156    521 Bassett Avenue, Wikieup, Missouri, 5422   "2602bc56-0000-0000-0000-59da42bc0000"   2015-02-06T05:32:32 +05:00 f1ca3044f17149f3bc61f7b9c78a26df
attachments/  1507476156    167 Nassau Street, Tuskahoma, Illinois, 5998   "2602bd56-0000-0000-0000-59da42bc0000"   2015-06-16T08:54:17 +04:00 f75f949ea8de466a9ef2bdb7ce065ac8
attachments/  1507476156    885 Strong Place, Cassel, Montana, 2069       "2602be56-0000-0000-0000-59da42bc0000"   2015-03-20T07:21:47 +04:00 ef0365fb40c04bb6a3ffc4bc77c905fd
attachments/  1507476156    515 Barwell Terrace, Defiance, Tennessee, 6439     "2602c056-0000-0000-0000-59da42bc0000"   2014-10-16T06:49:04 +04:00      e913fe543490432f871bc42019663518
attachments/  1507476156    570 Ruby Street, Spokane, Idaho, 9025       "2602c156-0000-0000-0000-59da42bc0000"   2014-10-30T05:49:33 +04:00 e53072057d314bc9b36c89a8350048f3
```

> [!NOTE]
> Propojené služby Cosmos DB server nepodporuje názvy složené ze čtyř částí. Vrátí se chyba podobná následující zpráva:

```
Msg 7312, Level 16, State 1, Line 44

Invalid use of schema or catalog for OLE DB provider "MSDASQL" for linked server "DEMOCOSMOS". A four-part name was supplied, but the provider does not expose the necessary interfaces to use a catalog or schema.
``` 

## <a name="optional-creating-views"></a>(Volitelné) Vytváření zobrazení
Můžete definovat a vytvořit zobrazení jako součást procesu vzorkování. Tato zobrazení jsou ekvivalentní zobrazení SQL. Jsou jen pro čtení a výběry a projekce dotazu SQL služby Azure Cosmos DB definované jsou oboru. 

Chcete-li vytvořit zobrazení pro data, v okně **editor schémat** klikněte ve sloupci **definice zobrazení** na položku **Přidat** na řádku kontejneru a proveďte ukázku. 
    ![vytvořit zobrazení dat](./media/odbc-driver/odbc-driver-create-view.png)


Pak v okně **definice zobrazení** proveďte následující:

1. Klikněte na **Nový**, zadejte název zobrazení, třeba EmployeesfromSeattleView, a pak klikněte na **OK**.

1. V okně **Upravit zobrazení** zadejte Azure Cosmos DB dotaz. Musí se jednat o [Azure Cosmos DB dotaz SQL](how-to-sql-query.md), například `SELECT c.City, c.EmployeeName, c.Level, c.Age, c.Manager FROM c WHERE c.City = "Seattle"`, a pak klikněte na **OK**.

    ![Přidat dotaz při vytváření zobrazení](./media/odbc-driver/odbc-driver-create-view-2.png)


Chcete, můžete vytvořit mnoha zobrazení. Po dokončení definování zobrazení, můžete pak Vzorkovat data. 

## <a name="step-5-view-your-data-in-bi-tools-such-as-power-bi-desktop"></a>Krok 5: Zobrazení dat v nástrojích BI, jako je například Power BI Desktopu

Váš nový název zdroje dat můžete použít pro připojení ke službě Azure Cosmos DB se žádné kompatibilní se standardem ODBC nástroje – tento krok jednoduše se dozvíte, jak se připojit k Power BI Desktopu a vytvoření vizualizace pomocí Power BI.

1. Otevřete Power BI Desktop.

1. Klikněte na **získat data**.

    ![Získání dat v Power BI Desktopu](./media/odbc-driver/odbc-driver-power-bi-get-data.png)

1. V okně **získat data** klikněte na **jiné** | **ODBC** | **připojit**.

    ![Vyberte zdroj dat rozhraní ODBC, v Power BI získat Data](./media/odbc-driver/odbc-driver-power-bi-get-data-2.png)

1. V okně **z rozhraní ODBC** vyberte název zdroje dat, který jste vytvořili, a pak klikněte na **OK**. **Rozšířené položky možností** můžete nechat prázdné.

    ![Zvolte název zdroje dat (DSN) v Power BI získat Data](./media/odbc-driver/odbc-driver-power-bi-get-data-3.png)

1. V okně **přístup ke zdroji dat pomocí ovladače ODBC** vyberte **výchozí nebo vlastní** a potom klikněte na **připojit**. Nemusíte zahrnovat **vlastnosti připojovacího řetězce přihlašovacích údajů**.

1. V okně **navigátor** rozbalte v levém podokně databázi, schéma a pak vyberte tabulku. V podokně Výsledky zahrnuje data pomocí schématu, kterou jste vytvořili.

    ![Výběr tabulky v Power BI získat Data](./media/odbc-driver/odbc-driver-power-bi-get-data-4.png)

1. Chcete-li vizualizovat data v Power BI ploše, zaškrtněte políčko před názvem tabulky a potom klikněte na tlačítko **načíst**.

1. V Power BI Desktopu, úplně vlevo, vyberte na kartě Data ![Karta data v Power BI Desktopu](./media/odbc-driver/odbc-driver-data-tab.png) Potvrďte data byla importována.

1. Vizuály teď můžete vytvářet pomocí Power BI kliknutím na kartu Sestava ![v Power BI Desktop](./media/odbc-driver/odbc-driver-report-tab.png), kliknutím na **Nový vizuál**a následně přizpůsobením dlaždice. Další informace o vytváření vizualizací v Power BI Desktop najdete v tématu [typy vizualizací v Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-visualization-types-for-reports-and-q-and-a/).

## <a name="troubleshooting"></a>Řešení potíží

Pokud se zobrazí následující chyba, ujistěte se, že hodnoty pro **hostitele** a **přístupová oprávnění** , které jste zkopírovali Azure Portal v [kroku 2](#connect) jsou správné, a pak to zkuste znovu. Použijte tlačítka Kopírovat napravo od **hostitele** a přístupové hodnoty **klíčů** v Azure Portal ke zkopírování hodnoty bez chyb.

    [HY000]: [Microsoft][Azure Cosmos DB] (401) HTTP 401 Authentication Error: {"code":"Unauthorized","message":"The input authorization token can't serve the request. Please check that the expected payload is built as per the protocol, and check the key being used. Server used the following payload to sign: 'get\ndbs\n\nfri, 20 jan 2017 03:43:55 gmt\n\n'\r\nActivityId: 9acb3c0d-cb31-4b78-ac0a-413c8d33e373"}`

## <a name="next-steps"></a>Další kroky

Další informace o Cosmos Azure DB najdete v tématu [Vítá vás Azure Cosmos DB](introduction.md).
