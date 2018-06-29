---
title: Připojte se k databázi Cosmos Azure pomocí nástrojů BI analytics | Microsoft Docs
description: Naučte se používat ovladač Azure Cosmos DB ODBC k vytvoření tabulky a zobrazení tak, aby normalizovaný data lze zobrazit v softwaru analytics BI a data.
keywords: rozhraní ODBC, ovladače odbc
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 03/22/2018
ms.author: sngun
ms.openlocfilehash: e93b241c79a50380f4ef1538dfbf7615232e6c49
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37096505"
---
# <a name="connect-to-azure-cosmos-db-using-bi-analytics-tools-with-the-odbc-driver"></a>Připojte se k databázi Cosmos Azure pomocí nástrojů BI analýzy pomocí ovladače ODBC

Ovladač Azure Cosmos DB ODBC umožňuje připojení k databázi Cosmos Azure pomocí nástroje Analýza BI například Tableau, SQL Server Integration Services a Power BI Desktop, aby mohli analyzovat a vytvořit vizualizacemi vašich dat. Azure Cosmos DB v těchto řešeních.

Ovladač Azure Cosmos DB ODBC je kompatibilní s ODBC 3.8 a podporuje syntaxi ANSI SQL-92. Ovladač nabízí bohaté funkce můžete renormalize data v Azure Cosmos DB. Použití ovladače, může představovat data v Azure Cosmos DB jako tabulky a zobrazení. Ovladač umožňuje provádět operace SQL u tabulky a zobrazení, včetně skupiny dotazy, vložení, aktualizace a odstraní.

## <a name="why-do-i-need-to-normalize-my-data"></a>Proč musím normalizaci svá data?
Azure Cosmos DB je schemaless databáze, takže umožňuje rychlý vývoj aplikací povolením aplikace k iteraci jejich datového modelu za chodu a není je omezit striktní schématu. Izolované databáze Azure Cosmos DB může obsahovat dokumenty JSON různé struktur. Tato služba je skvělá pro rychlý vývoj aplikací, ale když chcete k analýze a vytváření sestav dat pomocí analýzy dat a nástroje BI, často potřebuje data vyrovnány a splňovat určité schéma.

Toto je, kde odeslán ovladač ODBC. Pomocí ovladače ODBC, můžete nyní renormalize data v Azure Cosmos DB do tabulek a zobrazení hodí k vašim datovým potřebám analýz a generování sestav. Renormalized schémata nemají žádný vliv na základní data a není omezit jen vývojáři řídit k nim, umožňují vám využít kompatibilní s rozhraním ODBC nástroje pro přístup k datům. Takže teď vaše databáze Azure Cosmos DB pouze nebude oblíbená položka pro váš vývojový tým, ale bude datoví analytici skvělé příliš.

Teď umožňuje Začínáme pomocí ovladače ODBC.

## <a id="install"></a>Krok 1: Instalace ovladač Azure Cosmos DB ODBC

1. Stáhněte si ovladače pro vaše prostředí:

    | Instalační program | Podporované operační systémy| 
    |---|---| 
    |[Microsoft Azure Cosmos DB ODBC 64-bit.msi](https://aka.ms/documentdb-odbc-64x64) pro 64bitový systém Windows| 64bitové verze Windows 8.1 nebo novější, Windows 8, Windows 7, Windows Server 2012 R2, Windows Server 2012 a Windows Server 2008 R2.| 
    |[Microsoft Azure Cosmos DB ODBC 32 x 64-bit.msi](https://aka.ms/documentdb-odbc-32x64) pro 32bitovou verzi na 64bitovém systému Windows| 64bitové verze Windows 8.1 nebo novější, Windows 8, Windows 7, Windows XP, Windows Vista, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 a Windows Server 2003.| 
    |[Microsoft Azure Cosmos DB ODBC 32-bit.msi](https://aka.ms/documentdb-odbc-32x32) pro 32bitový systém Windows|32bitové verze Windows 8.1 nebo novější, Windows 8, Windows 7, Windows XP a Windows Vista.|

    Spusťte soubor msi místně, která se spouští **Průvodce instalací ovladačů DB Microsoft Azure Cosmos ODBC**. 
2. Dokončení Průvodce instalací pomocí výchozího vstup pro instalaci ovladače ODBC.
3. Otevřete **správce zdrojů dat ODBC** aplikace v počítači, můžete to provedete zadáním **zdroje dat ODBC** v systému Windows vyhledávacího pole. 
    Můžete potvrdit, ovladač byl nainstalován kliknutím **ovladače** kartě a zajištění **ovladače ODBC Microsoft Azure Cosmos DB** je uveden.

    ![Azure Cosmos DB správce zdrojů dat ODBC](./media/odbc-driver/odbc-driver.png)

## <a id="connect"></a>Krok 2: Připojení k databázi Azure Cosmos DB

1. Po [instalaci ovladače Azure Cosmos DB ODBC](#install)v **správce zdrojů dat ODBC** okně klikněte na tlačítko **přidat**. Můžete vytvořit uživatele nebo název DSN systému. V tomto příkladu vytvoříte uživatelské DSN.
2. V **vytvořit nový zdroj dat** vyberte **ovladače ODBC Microsoft Azure Cosmos DB**a potom klikněte na **Dokončit**.
3. V **Azure Cosmos DB ODBC ovladač SDN instalace** vyplňte následující: 

    ![Azure okno nastavení DSN ovladač ODBC pro Cosmos DB](./media/odbc-driver/odbc-driver-dsn-setup.png)
    - **Název zdroje dat**: vlastní popisný název DSN rozhraní ODBC. Tento název je jedinečný pro váš účet Azure Cosmos DB, takže název správně Pokud máte více účtů.
    - **Popis**: stručný popis zdroje dat.
    - **Hostitele**: identifikátor URI pro váš účet Azure Cosmos DB. Můžete načíst to ze stránky Azure Cosmos DB klíče na portálu Azure, jak je znázorněno na následujícím snímku obrazovky. 
    - **Přístup ke klíči**: klíč primární nebo sekundární, čtení a zápis nebo jen pro čtení z klíče DB Cosmos Azure stránky na portálu Azure, jak je znázorněno na následujícím snímku obrazovky. Doporučujeme, že abyste použili klíč jen pro čtení, pokud název zdroje dat se používá pro zpracování dat jen pro čtení a vytváření sestav.
    ![Stránky Azure DB klíče Cosmos](./media/odbc-driver/odbc-driver-keys.png)
    - **Přístupový klíč pro šifrování**: vybrat nejlepší volbou založená na uživatelích tohoto počítače. 
4. Klikněte **Test** tlačítko a ujistěte se, zda se můžete připojit ke svému účtu Azure Cosmos DB. 
5. Klikněte na tlačítko **pokročilé možnosti** a nastavte následující hodnoty:
    - **Dotaz konzistence**: vyberte [úroveň konzistence](consistency-levels.md) pro operace. Výchozí hodnota je relace.
    - **Počet opakovaných pokusů**: Zadejte počet pokusů o opakování operace, pokud není dokončena počáteční požadavek z důvodu omezení rychlosti služby.
    - **Soubor se schématem**: Zde máte několik možností.
        - Ve výchozím nastavení tato položka, jako je (prázdný), a kontroluje ovladače data na první stránce pro všechny kolekce k určení schéma každou kolekci. To se označuje jako kolekce mapování. Bez soubor schéma definované ovladače se má provést kontrola pro každou relaci ovladačů a může mít za následek vyšší doba spuštění aplikace pomocí DSN. Doporučujeme vždy přiřadit soubor schématu pro zdroje dat DSN.
        - Pokud již máte soubor schématu (může být ten, který jste vytvořili pomocí [schématu Editor](#schema-editor)), můžete kliknout na **Procházet**, přejděte k souboru, klikněte na **Uložit**a potom klikněte na **OK**.
        - Pokud chcete vytvořit nové schéma, klikněte na tlačítko **OK**a potom klikněte na **schématu Editor** v hlavním okně. Pak pokračujte [schématu Editor](#schema-editor) informace. Po vytvoření nového souboru schématu, nezapomeňte se vraťte **pokročilé možnosti** okno zahrnout schématu nově vytvořený soubor.

6. Po dokončení a zavření **Azure Cosmos DB ODBC ovladač DSN instalace** okně Nový uživatelský název DSN se přidá do karty Uživatelské DSN.

    ![Nové Azure Cosmos DB název DSN rozhraní ODBC na kartě Uživatelské DSN](./media/odbc-driver/odbc-driver-user-dsn.png)

## <a id="#collection-mapping"></a>Krok 3: Vytvoření definice schématu pomocí metody kolekce mapování

Existují dva typy metod vzorkování, které můžete použít: **kolekce mapování** nebo **tabulky oddělovače**. Relaci vzorkování můžete využít obě metody vzorkování, ale každá kolekce metodu lze použít pouze konkrétní vzorkování. Následující postup vytvořit schéma pro data v jedné nebo více kolekcí pomocí metody kolekce mapování. Tato metoda vzorkování načte data na stránce určit strukturu dat kolekce. Ho transponuje kolekci do tabulky na straně ODBC. Tato metoda vzorkování je rychlé a efektivní, když jsou data v kolekci homogenního. Pokud kolekce obsahuje heterogenous typu dat, doporučujeme použít [tabulky oddělovače mapování metoda](#table-mapping) jako poskytuje robustnější metody vzorkování určit datové struktury v kolekci. 

1. Po dokončení kroků 1 – 4 v [připojit k databázi Azure Cosmos DB](#connect), klikněte na tlačítko **schématu Editor** v **Azure Cosmos DB ODBC ovladač DSN instalace** okno.

    ![Schéma editor tlačítka v okně Azure Cosmos DB ODBC ovladač DSN nastavení](./media/odbc-driver/odbc-driver-schema-editor.png)
2. V **schématu Editor** okně klikněte na tlačítko **vytvořit nový**.
    **Generovat schéma** okno zobrazí všechny kolekce v účtu Azure Cosmos DB. 
3. Vyberte jeden nebo více kolekcí pro ukázkové a pak klikněte na tlačítko **ukázka**. 
4. V **zobrazení návrhu** jsou reprezentované kartě, databáze, schéma a tabulku. V zobrazení tabulky zobrazí kontroly sadu vlastnosti související s názvy sloupců (název serveru SQL, název zdroje atd.).
    Pro každý sloupec můžete upravit název sloupce SQL, typ SQL, délka SQL (pokud existuje), škálování (pokud existuje), přesnost (pokud existuje) a s možnou hodnotou Null.
    - Můžete nastavit **skrýt sloupce** k **true** Pokud chcete vyloučit sloupce z výsledků dotazu. Sloupce označena skrýt sloupce = true nebudou zobrazeny pro výběr a projekce, i když jsou stále součástí schématu. Například můžete skrýt všechny vlastnosti systém požadovaný Azure Cosmos DB počínaje "_".
    - **Id** sloupec je pouze pole, které nemohou být skrytá. protože je používán jako primární klíč v normalizovaný schématu. 
5. Po dokončení definování schématu, klikněte na tlačítko **soubor** | **Uložit**, přejděte do adresáře uložte schématu a pak klikněte na tlačítko **Uložit**.

    Pokud chcete v budoucnu toto schéma používají, pomocí názvu DSN, otevřete okno Azure Cosmos DB ODBC ovladač DSN nastavení (pomocí Správce zdrojů dat ODBC), klikněte na tlačítko Upřesnit možnosti a pak v dialogovém okně soubor schématu přejděte na schéma uložené. Ukládání souboru schématu do existující DSN upravuje DSN připojení k oboru k datům a struktury definované schéma.

## <a id="table-mapping"></a>Krok 4: Vytvoření definice schématu pomocí tabulky oddělovače mapování – metoda

Existují dva typy metod vzorkování, které můžete použít: **kolekce mapování** nebo **tabulky oddělovače**. Relaci vzorkování můžete využít obě metody vzorkování, ale každá kolekce metodu lze použít pouze konkrétní vzorkování. 

Následujících kroků vytvořte schéma pro data v jedné nebo více kolekcí pomocí **tabulky oddělovače** mapování metoda. Doporučujeme použít tuto metodu vzorkování, pokud kolekce obsahují heterogenní typ dat. Tuto metodu můžete použít k určení rozsahu vzorkování na sadu atributů a jeho odpovídající hodnoty. Například pokud dokument obsahuje vlastnost "Druh", můžete určit obor vzorkuje hodnoty této vlastnosti. Konečný výsledek vzorkuje by sadu tabulek pro každou z hodnot pro typ jste zadali. Zadejte například = Auto vytvoří tabulku Car při typ = roviny byste mohli vytvořit tabulku roviny.

1. Po dokončení kroků 1 – 4 v [připojit k databázi Azure Cosmos DB](#connect), klikněte na tlačítko **schématu Editor** v okně Azure Cosmos DB ODBC ovladač DSN nastavení.
2. V **schématu Editor** okně klikněte na tlačítko **vytvořit nový**.
    **Generovat schéma** okno zobrazí všechny kolekce v účtu Azure Cosmos DB. 
3. Vyberte kolekci, která na **ukázkové zobrazení** ve **mapování definice** sloupec pro kolekce, klikněte na tlačítko **upravit**. Potom v **mapování definice** vyberte **tabulky oddělovače** metoda. Potom udělejte následující:

    a. V **atributy** zadejte název vlastnosti oddělovač. Toto je vlastnost dokumentu, který chcete určit obor vzorkování na, například města a stiskněte klávesu enter. 

    b. Pokud chcete určit obor vzorkování na určité hodnoty pro atribut, který jste právě zadali, vyberte atribut v výběr a potom zadejte hodnotu do **hodnotu** pole, například Praha a stiskněte klávesu enter. Můžete přidat více hodnot atributů. Právě zkontrolujte, že je vybraný správný atribut, když zadáváte hodnoty.

    Například, pokud zahrnete **atributy** hodnotu města a budete chtít omezit tabulka zahrnovat pouze řádky s hodnotou města Brno a Dubaj, zadejte město v atributy pole a New Yorku a pak Dubaj do **Hodnoty** pole.
4. Klikněte na **OK**. 
5. Po dokončení definice mapování pro kolekce, které chcete v ukázkové **schématu Editor** okně klikněte na tlačítko **ukázka**.
     Pro každý sloupec můžete upravit název sloupce SQL, typ SQL, délka SQL (pokud existuje), škálování (pokud existuje), přesnost (pokud existuje) a s možnou hodnotou Null.
    - Můžete nastavit **skrýt sloupce** k **true** Pokud chcete vyloučit sloupce z výsledků dotazu. Sloupce označena skrýt sloupce = true nebudou zobrazeny pro výběr a projekce, i když jsou stále součástí schématu. Například můžete skrýt všechny vlastnosti systém požadovaný Azure Cosmos DB počínaje `_`.
    - **Id** sloupec je pouze pole, které nemohou být skrytá. protože je používán jako primární klíč v normalizovaný schématu. 
6. Po dokončení definování schématu, klikněte na tlačítko **soubor** | **Uložit**, přejděte do adresáře uložte schématu a pak klikněte na tlačítko **Uložit**.
7. Zpět v **Azure Cosmos DB ODBC ovladač DSN instalace** okně klikněte na tlačítko **pokročilé možnosti**. Pak na **soubor schématu** pole, přejděte na soubor uložený schéma a klikněte na tlačítko **OK**. Klikněte na tlačítko **OK** znovu a uložit DSN. To umožňuje ušetřit schéma, které jste vytvořili s DSN. 

## <a name="optional-set-up-linked-server-connection"></a>(Volitelné) Nastavení připojení odkazovaného serveru

Nastavením připojení odkazovaného serveru se můžete dotazovat Azure Cosmos databáze z SQL Server Management Studio (SSMS).

1. Vytvořte zdroj dat systému, jak je popsáno v [kroku 2](#connect)s názvem například `SDS Name`.
2. [Nainstalujte SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) a připojte se k serveru. 
3. V editoru dotazů SSMS vytvořit objekt odkazovaný server `DEMOCOSMOS` ke zdroji dat pomocí následujících příkazů. Nahraďte `DEMOCOSMOS` s názvem pro odkazovaný server, a `SDS Name` s názvem zdroje dat systému.

    ```sql
    USE [master]
    GO
    
    EXEC master.dbo.sp_addlinkedserver @server = N'DEMOCOSMOS', @srvproduct=N'', @provider=N'MSDASQL', @datasrc=N'SDS Name'
    
    EXEC master.dbo.sp_addlinkedsrvlogin @rmtsrvname=N'DEMOCOSMOS', @useself=N'False', @locallogin=NULL, @rmtuser=NULL, @rmtpassword=NULL
    
    GO
    ```
    
Pokud chcete zobrazit nový název odkazovaný server, aktualizujte seznam odkazované servery.

![Odkazovaný Server v aplikaci SSMS](./media/odbc-driver/odbc-driver-linked-server-ssms.png)

### <a name="query-linked-database"></a>Propojené dotaz do databáze

K dotazování propojené databáze, zadejte dotaz SSMS. V tomto příkladu dotaz vybere z tabulky v kolekci s názvem `customers`:

```sql
SELECT * FROM OPENQUERY(DEMOCOSMOS, 'SELECT *  FROM [customers].[customers]')
```

Provedení dotazu. Výsledek by měl vypadat podobně jako tento:

```
attachments/  1507476156    521 Bassett Avenue, Wikieup, Missouri, 5422   "2602bc56-0000-0000-0000-59da42bc0000"   2015-02-06T05:32:32 +05:00 f1ca3044f17149f3bc61f7b9c78a26df
attachments/  1507476156    167 Nassau Street, Tuskahoma, Illinois, 5998   "2602bd56-0000-0000-0000-59da42bc0000"   2015-06-16T08:54:17 +04:00 f75f949ea8de466a9ef2bdb7ce065ac8
attachments/  1507476156    885 Strong Place, Cassel, Montana, 2069       "2602be56-0000-0000-0000-59da42bc0000"   2015-03-20T07:21:47 +04:00 ef0365fb40c04bb6a3ffc4bc77c905fd
attachments/  1507476156    515 Barwell Terrace, Defiance, Tennessee, 6439     "2602c056-0000-0000-0000-59da42bc0000"   2014-10-16T06:49:04 +04:00      e913fe543490432f871bc42019663518
attachments/  1507476156    570 Ruby Street, Spokane, Idaho, 9025       "2602c156-0000-0000-0000-59da42bc0000"   2014-10-30T05:49:33 +04:00 e53072057d314bc9b36c89a8350048f3
```

> [!NOTE]
> Odkazovaný server Cosmos DB nepodporuje pojmenování sestávající ze čtyř částí. Vrátí se chyba podobná následující zpráva:

```
Msg 7312, Level 16, State 1, Line 44

Invalid use of schema or catalog for OLE DB provider "MSDASQL" for linked server "DEMOCOSMOS". A four-part name was supplied, but the provider does not expose the necessary interfaces to use a catalog or schema.
``` 

## <a name="optional-creating-views"></a>(Volitelné) Vytváření zobrazení
Můžete definovat a vytvářet zobrazení v rámci procesu vzorkování. Tato zobrazení jsou ekvivalentní zobrazení SQL. Jsou jen pro čtení a oboru se výběr a definované projekce Azure Cosmos DB SQL. 

Vytvoření zobrazení pro vaše data v **schématu Editor** okno v **definice zobrazení** sloupce, klikněte na tlačítko **přidat** na řádek kolekce ukázce. Potom v **definice zobrazení** okno, postupujte takto:
1. Klikněte na tlačítko **nový**, zadejte název zobrazení, například EmployeesfromSeattleView a pak klikněte na tlačítko **OK**.
2. V **upravit zobrazení** okno, zadejte dotaz služby Azure Cosmos DB. Dotaz SQL Azure Cosmos databáze musí být například`SELECT c.City, c.EmployeeName, c.Level, c.Age, c.Gender, c.Manager FROM c WHERE c.City = “Seattle”`a potom klikněte na **OK**.

Jak se vám líbí, můžete vytvořit mnoho zobrazení. Po dokončení definování zobrazení, můžete pak Vzorkovat data. 

## <a name="step-5-view-your-data-in-bi-tools-such-as-power-bi-desktop"></a>Krok 5: Zobrazení dat v nástrojích BI, například Power BI Desktop

Nové DSN můžete použít pro připojení k žádné kompatibilní s rozhraním ODBC nástroje DocumentADB – tento krok jednoduše ukazuje, jak se připojit k Power BI Desktop a vytvořit vizualizaci Power BI.

1. Otevřete Power BI Desktop.
2. Klikněte na tlačítko **získat Data**.
3. V **načíst Data** okně klikněte na tlačítko **jiných** | **ODBC** | **Connect**.
4. V **z rozhraní ODBC** okno, vyberte zdroj dat jste vytvořili a potom klikněte na **OK**. Můžete nechat **pokročilé možnosti** položky, které jsou prázdné.
5. V **přístup k datovému zdroji prostřednictvím ovladače ODBC** vyberte **výchozí nebo vlastní** a pak klikněte na **Connect**. Nemusíte zahrnovat **pověření vlastnosti připojovacího řetězce**.
6. V **Navigátor** okno, v levém podokně rozbalte databázi schématu a pak vyberte v tabulce. V podokně Výsledky zahrnuje data pomocí schématu, kterou jste vytvořili.
7. K vizualizaci dat v Power BI desktop, zaškrtněte políčko před název tabulky a pak klikněte na tlačítko **zatížení**.
8. V Power BI Desktop vkládá zcela vlevo, vyberte kartu dat ![Karta data v Power BI Desktop](./media/odbc-driver/odbc-driver-data-tab.png) Potvrďte vaše data byla importována.
9. Nyní můžete vytvořit vizuální prvky používající Power BI kliknutím na kartu sestavy ![kartu sestavy v Power BI Desktop](./media/odbc-driver/odbc-driver-report-tab.png), kliknete na příkaz **nové Visual**a pak přizpůsobení dlaždice. Další informace o vytváření vizualizací v Power BI Desktop najdete v tématu [typů vizualizace v Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-visualization-types-for-reports-and-q-and-a/).

## <a name="troubleshooting"></a>Řešení potíží

Pokud se zobrazí následující chyba, zkontrolujte **hostitele** a **přístupový klíč** hodnoty, které jste zkopírovali na portálu Azure v [kroku 2](#connect) jsou správné a opakujte. Pomocí tlačítek kopírovat na pravé straně **hostitele** a **přístupový klíč** hodnoty v portálu Azure a Kopírovat bez hodnoty chyb.

    [HY000]: [Microsoft][Azure Cosmos DB] (401) HTTP 401 Authentication Error: {"code":"Unauthorized","message":"The input authorization token can't serve the request. Please check that the expected payload is built as per the protocol, and check the key being used. Server used the following payload to sign: 'get\ndbs\n\nfri, 20 jan 2017 03:43:55 gmt\n\n'\r\nActivityId: 9acb3c0d-cb31-4b78-ac0a-413c8d33e373"}`

## <a name="next-steps"></a>Další postup

Další informace o Cosmos Azure DB najdete v tématu [Vítá vás Azure Cosmos DB](introduction.md).
