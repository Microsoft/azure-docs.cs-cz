---
title: Připojení k Azure Cosmos DB pomocí analytických nástrojů BI
description: Naučte se používat ovladač Azure Cosmos DB ODBC k vytváření tabulek a zobrazení, aby bylo možné v softwaru BI a data Analytics zobrazovat normalizovaná data.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/02/2019
ms.author: sngun
ms.openlocfilehash: 7df9a34923ed8cd0db45df9af4feb28520d45e3a
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93097596"
---
# <a name="connect-to-azure-cosmos-db-using-bi-analytics-tools-with-the-odbc-driver"></a>Připojení k Azure Cosmos DB pomocí nástrojů BI Analytics s ovladačem ODBC
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Ovladač Azure Cosmos DB ODBC vám umožní připojit se k Azure Cosmos DB pomocí analytických nástrojů BI, jako jsou služba SSIS (SQL Server Integration Services), Power BI Desktop a Tableau, abyste mohli analyzovat a vytvářet vizualizace vašich Azure Cosmos DB dat v těchto řešeních.

Ovladač ODBC pro Azure Cosmos DB je kompatibilní s rozhraním ODBC 3.8 a podporuje syntaxi ANSI SQL-92. Tento ovladač nabízí bohaté funkce, které vám pomůžou znovu normalizovat data ve službě Azure Cosmos DB. S využitím tohoto ovladače můžete data ve službě Azure Cosmos DB reprezentovat jako tabulky a zobrazení. Tento ovladač umožňuje provádět operace SQL s tabulkami a zobrazeními, včetně seskupení podle dotazů a operací vložení, aktualizace a odstranění.

> [!NOTE]
> Připojení k Azure Cosmos DB s ovladačem ODBC se v současné době podporuje jenom pro účty rozhraní SQL API Azure Cosmos DB.

## <a name="why-do-i-need-to-normalize-my-data"></a>Proč potřebuji normalizovat moje data?
Azure Cosmos DB je databáze bez schématu, která umožňuje rychlý vývoj aplikací a možnost iterovat na datové modely bez omezeného schématu na striktní schéma. Jedna databáze Azure Cosmos může obsahovat dokumenty JSON různých struktur. To je skvělé pro rychlý vývoj aplikací, ale pokud chcete analyzovat a vytvářet sestavy vašich dat pomocí nástrojů pro analýzu dat a nástrojů BI, data často musí být shrnutá a splňovat konkrétní schéma.

V takovém případě ovladač ODBC přichází. Pomocí ovladače ODBC teď můžete znovu normalizovat data v Azure Cosmos DB v tabulkách a zobrazeních, která odpovídají vašim požadavkům na analýzu dat a vytváření sestav. Renormalizovaná schémata nemají žádný vliv na podkladová data a nevyhovují vývojářům v jejich souladu. Místo toho vám umožňují využít nástroje kompatibilní s rozhraním ODBC pro přístup k datům. Takže teď vaše databáze Azure Cosmos nebude jenom oblíbená pro váš vývojový tým, ale vaše analytiky dat je uvidí.

Pojďme začít s ovladačem ODBC.

## <a name="step-1-install-the-azure-cosmos-db-odbc-driver"></a><a id="install"></a>Krok 1: instalace ovladače Azure Cosmos DB ODBC

1. Stáhněte si ovladače pro vaše prostředí:

    | Instalační služba | Podporované operační systémy| 
    |---|---| 
    |[Microsoft Azure Cosmos DB rozhraní ODBC 64-bit.msi](https://aka.ms/cosmos-odbc-64x64) pro 64-bitové Windows| 64 verze Windows 8.1 nebo novější, Windows 8, Windows 7, Windows Server 2012 R2, Windows Server 2012 a Windows Server 2008 R2.| 
    |[Microsoft Azure Cosmos DB rozhraní ODBC 32x64-bit.msi](https://aka.ms/cosmos-odbc-32x64) pro 32-bit v 64-bitovém systému Windows| 64 verze Windows 8.1 nebo novější, Windows 8, Windows 7, Windows XP, Windows Vista, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 a Windows Server 2003.| 
    |[Microsoft Azure Cosmos DB rozhraní ODBC 32-bit.msi](https://aka.ms/cosmos-odbc-32x32) pro 32-bitové Windows|32 verze Windows 8.1 nebo novější, Windows 8, Windows 7, Windows XP a Windows Vista.|

    Spusťte soubor MSI místně, což spustí **Průvodce instalací ovladače ODBC Microsoft Azure Cosmos DB** . 

1. Dokončete Průvodce instalací s použitím výchozího vstupu pro instalaci ovladače ODBC.

1. V počítači otevřete aplikaci **Správce zdrojů dat ODBC** . To můžete provést tak, že do vyhledávacího pole Windows zadáte **zdroje dat ODBC** . 
    Instalaci ovladače můžete ověřit kliknutím na kartu **ovladače** a zajistěte, aby byl uvedený **Microsoft Azure Cosmos DB ovladač ODBC** .

    :::image type="content" source="./media/odbc-driver/odbc-driver.png" alt-text="Azure Cosmos DB správce zdrojů dat ODBC":::

## <a name="step-2-connect-to-your-azure-cosmos-database"></a><a id="connect"></a>Krok 2: připojení k databázi Azure Cosmos

1. Po [instalaci ovladače Azure Cosmos DB ODBC](#install)v okně **Správce zdrojů dat ODBC** klikněte na **Přidat** . Můžete vytvořit uživatelský nebo systémový název DSN. V tomto příkladu vytváříte uživatelské DSN.

1. V okně **vytvořit nový zdroj dat** vyberte **Microsoft Azure Cosmos DB ovladač ODBC** a pak klikněte na **Dokončit** .

1. V okně **Azure Cosmos DB ODBC ovladače SDN nastavení** zadejte následující informace: 

    :::image type="content" source="./media/odbc-driver/odbc-driver-dsn-setup.png" alt-text="Azure Cosmos DB správce zdrojů dat ODBC":::
    - **Název zdroje dat** : vlastní popisný název DSN ODBC. Tento název je jedinečný pro váš Azure Cosmos DB účet, proto ho pojmenujte, pokud máte více účtů.
    - **Popis** : stručný popis zdroje dat.
    - **Host** : identifikátor URI pro váš účet Azure Cosmos DB. To můžete načíst ze stránky Azure Cosmos DB klíče v Azure Portal, jak je znázorněno na následujícím snímku obrazovky. 
    - **Přístupový klíč** : primární nebo sekundární klíč, který je jen pro čtení a zápis, nebo jen pro čtení ze stránky Azure Cosmos DB klíče v Azure Portal, jak je znázorněno na následujícím snímku obrazovky. Pokud se název DSN používá pro zpracování dat a vytváření sestav jen pro čtení, doporučujeme použít klíč jen pro čtení.
    :::image type="content" source="./media/odbc-driver/odbc-cosmos-account-keys.png" alt-text="Azure Cosmos DB správce zdrojů dat ODBC" ODBC DSN "účtu contoso.
        - Kliknutím pravým tlačítkem přidáte novou **řetězcovou** hodnotu:
            - Název hodnoty: **IgnoreSessionToken**
            - Data hodnoty: **1** 
             :::image type="content" source="./media/odbc-driver/cosmos-odbc-edit-registry.png" alt-text="Azure Cosmos DB správce zdrojů dat ODBC":::
    - **Konzistence dotazů** : vyberte [úroveň konzistence](consistency-levels.md) pro vaše operace. Výchozí hodnota je Session.
    - **Počet opakování** : zadejte počet opakování operace, pokud se počáteční žádost nedokončila z důvodu omezení rychlosti služby.
    - **Soubor schématu** : tady máte několik možností.
        - Ve výchozím nastavení zachová tuto položku jako (prázdná), ovladač prohledá první stránku dat pro všechny kontejnery a určí schéma každého kontejneru. Toto je známé jako mapování kontejneru. Bez definovaného souboru schématu musí ovladač provést kontrolu každé relace ovladače a může mít za následek vyšší dobu spuštění aplikace, která používá DSN. Doporučujeme vždy přidružit soubor schématu pro DSN.
        - Pokud už máte soubor schématu (Možná ho vytvoříte pomocí editoru schémat), můžete kliknout na **Procházet** , přejít k souboru, kliknout na **Uložit** a pak na **OK** .
        - Pokud chcete vytvořit nové schéma, klikněte na tlačítko **OK** a potom v hlavním okně klikněte na **editor schémat** . Pak přejděte k informacím editoru schématu. Po vytvoření nového souboru schématu nezapomeňte přejít zpět do okna **Upřesnit možnosti** a zahrnout nově vytvořený soubor schématu.

1. Po dokončení a zavření okna **Azure Cosmos DB nastavení DSN ovladače ODBC** se nový uživatel DSN přidá na kartu uživatelské DSN.

    :::image type="content" source="./media/odbc-driver/odbc-driver-user-dsn.png" alt-text="Azure Cosmos DB správce zdrojů dat ODBC":::

## <a name="step-3-create-a-schema-definition-using-the-container-mapping-method"></a><a id="#container-mapping"></a>Krok 3: vytvoření definice schématu pomocí metody mapování kontejnerů

Existují dva typy metod vzorkování, které lze použít: **mapování kontejnerů** nebo **oddělovače tabulek** . Relace vzorkování může využívat jak metody vzorkování, ale každý kontejner může použít pouze konkrétní metodu vzorkování. Následující postup vytvoří schéma pro data v jednom nebo více kontejnerech pomocí metody mapování kontejnerů. Tato metoda vzorkování načítá data na stránce kontejneru pro určení struktury dat. Předává kontejner na straně rozhraní ODBC jako tabulku. Tato metoda vzorkování je efektivní a rychlá, když jsou data v kontejneru homogenní. Pokud kontejner obsahuje heterogenní typ dat, doporučujeme použít [metodu mapování oddělovače tabulky](#table-mapping) , protože poskytuje pružnější způsob vzorkování pro určení datových struktur v kontejneru. 

1. Po dokončení kroků 1-4 v tématu [připojení k databázi Azure Cosmos](#connect)klikněte na **editor schémat** v okně **Nastavení DSN ovladače Azure Cosmos DB ODBC** .

    :::image type="content" source="./media/odbc-driver/odbc-driver-schema-editor.png" alt-text="Azure Cosmos DB správce zdrojů dat ODBC":::
1. V okně **editor schémat** klikněte na **vytvořit nový** .
    V okně **generovat schéma** se zobrazí všechny kontejnery v účtu Azure Cosmos DB. 

1. Vyberte jeden nebo více kontejnerů, které chcete vzorkovat, a potom klikněte na tlačítko **Ukázka** . 

1. Na kartě **návrhové zobrazení** je znázorněna databáze, schéma a tabulka. V zobrazení tabulky zobrazí Kontrola sadu vlastností přidružených k názvům sloupců (název SQL, název zdroje atd.).
    U každého sloupce můžete změnit název sloupce SQL, typ SQL, délku SQL (Pokud je k dispozici), měřítko (Pokud je k dispozici), přesnost (Pokud je k dispozici) a hodnotu null.
    - Pokud chcete tento sloupec z výsledků dotazu vyloučit, můžete nastavit možnost **Skrýt sloupec** na **hodnotu true** . Sloupce označené jako skrýt sloupec = true nejsou vraceny pro výběr a projekci, i když jsou stále součástí schématu. Můžete například skrýt všechny požadované vlastnosti Azure Cosmos DB systému začínající znakem "_".
    - Sloupec **ID** je jediné pole, které nelze skrýt, protože se používá jako primární klíč v normalizovaném schématu. 

1. Po dokončení definování schématu klikněte na **File**  |  **Uložit** soubor, přejděte do adresáře a uložte schéma a pak klikněte na **Uložit** .

1. Pokud chcete toto schéma použít se zdrojem DSN, otevřete **okno Azure Cosmos DB nastavení DSN ovladače ODBC** (přes Správce zdrojů dat ODBC), klikněte na **Upřesnit možnosti** a pak v poli **schématu** přejděte do uloženého schématu. Uložením souboru schématu do stávajícího DSN se upraví připojení DSN k oboru pro data a strukturu, která je definovaná schématem.

## <a name="step-4-create-a-schema-definition-using-the-table-delimiters-mapping-method"></a><a id="table-mapping"></a>Krok 4: vytvoření definice schématu pomocí metody mapování oddělovačů tabulek

Existují dva typy metod vzorkování, které lze použít: **mapování kontejnerů** nebo **oddělovače tabulek** . Relace vzorkování může využívat jak metody vzorkování, ale každý kontejner může použít pouze konkrétní metodu vzorkování. 

Následující postup vytvoří schéma pro data v jednom nebo více kontejnerech pomocí metody mapování **oddělovače tabulky** . Tuto metodu vzorkování doporučujeme použít, pokud vaše kontejnery obsahují heterogenní typ dat. Tuto metodu lze použít k určení rozsahu vzorkování do sady atributů a odpovídajících hodnot. Například pokud dokument obsahuje vlastnost "Type", můžete určit rozsah vzorkování na hodnoty této vlastnosti. Konečný výsledek vzorkování by představoval sadu tabulek pro každou z hodnot typu, který jste zadali. Například Type = auto vytvoří tabulku auta, zatímco Type = rovina vytvoří tabulku rovin.

1. Po dokončení kroků 1-4 v tématu [připojení k databázi Azure Cosmos](#connect)klikněte na **editor schémat** v okně nastavení DSN ovladače Azure Cosmos DB ODBC.

1. V okně **editor schémat** klikněte na **vytvořit nový** .
    V okně **generovat schéma** se zobrazí všechny kontejnery v účtu Azure Cosmos DB. 

1. Vyberte kontejner na kartě **ukázkové zobrazení** ve sloupci **definice mapování** pro kontejner klikněte na **Upravit** . Pak v okně **definice mapování** vyberte možnost **oddělovače tabulky** . Potom udělejte následující:

    a. Do pole **atributy** zadejte název vlastnosti oddělovače. Toto je vlastnost v dokumentu, pro kterou chcete určit obor vzorkování, například City a stiskněte klávesu ENTER. 

    b. Pokud chcete určit rozsah vzorkování pouze na určité hodnoty pro atribut, který jste zadali výše, vyberte atribut v poli výběr, zadejte hodnotu do pole **hodnota** (např. Praha) a stiskněte klávesu ENTER. Můžete pokračovat v přidávání více hodnot pro atributy. Pouze zajistěte, aby byl při zadávání hodnot vybrán správný atribut.

    Například pokud zahrnete hodnotu **atributů** City (město) a chcete omezit tabulku tak, aby zahrnovala pouze řádky s hodnotou města New York a Dubaj, měli byste zadat City do pole atributy a New York a pak Dubaj v poli **hodnoty** .

1. Klikněte na **OK** . 

1. Po dokončení definic mapování pro kontejnery, které chcete vzorkovat, v okně **editor schémat** klikněte na položku **Ukázka** .
     U každého sloupce můžete změnit název sloupce SQL, typ SQL, délku SQL (Pokud je k dispozici), měřítko (Pokud je k dispozici), přesnost (Pokud je k dispozici) a hodnotu null.
    - Pokud chcete tento sloupec z výsledků dotazu vyloučit, můžete nastavit možnost **Skrýt sloupec** na **hodnotu true** . Sloupce označené jako skrýt sloupec = true nejsou vraceny pro výběr a projekci, i když jsou stále součástí schématu. Můžete například skrýt všechny požadované vlastnosti Azure Cosmos DB systému od `_` .
    - Sloupec **ID** je jediné pole, které nelze skrýt, protože se používá jako primární klíč v normalizovaném schématu. 

1. Po dokončení definování schématu klikněte na **File**  |  **Uložit** soubor, přejděte do adresáře a uložte schéma a pak klikněte na **Uložit** .

1. Zpátky v okně **Azure Cosmos DB nastavení DSN ovladače ODBC** klikněte na **Upřesnit možnosti** . Pak v poli **soubor schématu** přejděte do uloženého souboru schématu a klikněte na **OK** . Opětovným kliknutím na tlačítko **OK** uložte název DSN. Tím se uloží schéma, které jste vytvořili do DSN. 

## <a name="optional-set-up-linked-server-connection"></a>Volitelné Nastavení připojení propojeného serveru

Nastavením připojení připojeného serveru můžete zadat dotaz na Azure Cosmos DB z SQL Server Management Studio (SSMS).

1. Vytvořte systémový zdroj dat, jak je popsáno v [kroku 2](#connect)nazvané příklad `SDS Name` .

1. [Nainstalujte SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) a připojte se k serveru. 

1. V editoru dotazů SSMS vytvořte objekt propojeného serveru `DEMOCOSMOS` pro zdroj dat pomocí následujících příkazů. Nahraďte `DEMOCOSMOS` názvem vašeho odkazovaného serveru a `SDS Name` názvem vašeho systémového zdroje dat.

    ```sql
    USE [master]
    GO
    
    EXEC master.dbo.sp_addlinkedserver @server = N'DEMOCOSMOS', @srvproduct=N'', @provider=N'MSDASQL', @datasrc=N'SDS Name'
    
    EXEC master.dbo.sp_addlinkedsrvlogin @rmtsrvname=N'DEMOCOSMOS', @useself=N'False', @locallogin=NULL, @rmtuser=NULL, @rmtpassword=NULL
    
    GO
    ```
    
Pokud chcete zobrazit nový název propojeného serveru, aktualizujte seznam propojených serverů.

:::image type="content" source="./media/odbc-driver/odbc-driver-linked-server-ssms.png" alt-text="Azure Cosmos DB správce zdrojů dat ODBC":::

### <a name="query-linked-database"></a>Dotaz na propojenou databázi

Chcete-li zadat dotaz na propojenou databázi, zadejte dotaz SSMS. V tomto příkladu se dotaz vybere z tabulky v kontejneru s názvem `customers` :

```sql
SELECT * FROM OPENQUERY(DEMOCOSMOS, 'SELECT *  FROM [customers].[customers]')
```

Spusťte dotaz. Výsledek by měl vypadat nějak takto:

```
attachments/  1507476156    521 Bassett Avenue, Wikieup, Missouri, 5422   "2602bc56-0000-0000-0000-59da42bc0000"   2015-02-06T05:32:32 +05:00 f1ca3044f17149f3bc61f7b9c78a26df
attachments/  1507476156    167 Nassau Street, Tuskahoma, Illinois, 5998   "2602bd56-0000-0000-0000-59da42bc0000"   2015-06-16T08:54:17 +04:00 f75f949ea8de466a9ef2bdb7ce065ac8
attachments/  1507476156    885 Strong Place, Cassel, Montana, 2069       "2602be56-0000-0000-0000-59da42bc0000"   2015-03-20T07:21:47 +04:00 ef0365fb40c04bb6a3ffc4bc77c905fd
attachments/  1507476156    515 Barwell Terrace, Defiance, Tennessee, 6439     "2602c056-0000-0000-0000-59da42bc0000"   2014-10-16T06:49:04 +04:00      e913fe543490432f871bc42019663518
attachments/  1507476156    570 Ruby Street, Spokane, Idaho, 9025       "2602c156-0000-0000-0000-59da42bc0000"   2014-10-30T05:49:33 +04:00 e53072057d314bc9b36c89a8350048f3
```

> [!NOTE]
> Propojený Cosmos DB Server nepodporuje pojmenovávání se čtyřmi částmi. Vrátí se chybová zpráva podobná následující zprávě:

```
Msg 7312, Level 16, State 1, Line 44

Invalid use of schema or catalog for OLE DB provider "MSDASQL" for linked server "DEMOCOSMOS". A four-part name was supplied, but the provider does not expose the necessary interfaces to use a catalog or schema.
``` 

## <a name="optional-creating-views"></a>Volitelné Vytváření zobrazení
V rámci procesu vzorkování můžete definovat a vytvářet zobrazení. Tato zobrazení jsou ekvivalentní zobrazení SQL. Jsou jen pro čtení a jsou určené pro výběr a projekce Azure Cosmos DB definovaného dotazu SQL. 

Chcete-li vytvořit zobrazení pro data, v okně **editor schémat** klikněte ve sloupci **definice zobrazení** na položku **Přidat** na řádku kontejneru a proveďte ukázku. 

:::image type="content" source="./media/odbc-driver/odbc-driver-create-view.png" alt-text="Azure Cosmos DB správce zdrojů dat ODBC":::


Pak v okně **definice zobrazení** proveďte následující:

1. Klikněte na **Nový** , zadejte název zobrazení, třeba EmployeesfromSeattleView, a pak klikněte na **OK** .

1. V okně **Upravit zobrazení** zadejte Azure Cosmos DB dotaz. Musí se jednat o [Azure Cosmos DB dotaz SQL](./sql-query-getting-started.md), například `SELECT c.City, c.EmployeeName, c.Level, c.Age, c.Manager FROM c WHERE c.City = "Seattle"` a pak klikněte na tlačítko **OK** .

    :::image type="content" source="./media/odbc-driver/odbc-driver-create-view-2.png" alt-text="Azure Cosmos DB správce zdrojů dat ODBC":::


V takovém případě můžete vytvořit mnoho zobrazení. Po dokončení definování zobrazení pak můžete data vzorkovat. 

## <a name="step-5-view-your-data-in-bi-tools-such-as-power-bi-desktop"></a>Krok 5: zobrazení dat v nástrojích BI, jako je Power BI Desktop

Pomocí nového názvu DSN se můžete připojit k Azure Cosmos DB pomocí libovolných nástrojů kompatibilních s rozhraním ODBC. v tomto kroku se dozvíte, jak se připojit k Power BI Desktop a vytvořit vizualizaci Power BI.

1. Otevřete Power BI Desktop.

1. Klikněte na **získat data** .

    :::image type="content" source="./media/odbc-driver/odbc-driver-power-bi-get-data.png" alt-text="Azure Cosmos DB správce zdrojů dat ODBC":::

1. V okně **získat data** klikněte na **jiný**  |  **ODBC**  |  **připojit** .

    :::image type="content" source="./media/odbc-driver/odbc-driver-power-bi-get-data-2.png" alt-text="Azure Cosmos DB správce zdrojů dat ODBC":::

1. V okně **z rozhraní ODBC** vyberte název zdroje dat, který jste vytvořili, a pak klikněte na **OK** . **Rozšířené položky možností** můžete nechat prázdné.

   :::image type="content" source="./media/odbc-driver/odbc-driver-power-bi-get-data-3.png" alt-text="Azure Cosmos DB správce zdrojů dat ODBC":::

1. V okně **přístup ke zdroji dat pomocí ovladače ODBC** vyberte **výchozí nebo vlastní** a potom klikněte na **připojit** . Nemusíte zahrnovat **vlastnosti připojovacího řetězce přihlašovacích údajů** .

1. V okně **navigátor** rozbalte v levém podokně databázi, schéma a pak vyberte tabulku. Podokno výsledků obsahuje data pomocí schématu, které jste vytvořili.

    :::image type="content" source="./media/odbc-driver/odbc-driver-power-bi-get-data-4.png" alt-text="Azure Cosmos DB správce zdrojů dat ODBC":::

1. Chcete-li vizualizovat data v Power BI ploše, zaškrtněte políčko před názvem tabulky a potom klikněte na tlačítko **načíst** .

1. V Power BI Desktop úplně vlevo vyberte kartu data a potvrďte tak, že se :::image type="icon" source="./media/odbc-driver/odbc-driver-data-tab.png"::: data naimportovala. 

1. Vizuály teď můžete vytvářet pomocí Power BI kliknutím na kartu Sestava :::image type="icon" source="./media/odbc-driver/odbc-driver-report-tab.png"::: , kliknutím na **Nový vizuál** a přizpůsobením dlaždice. Další informace o vytváření vizualizací v Power BI Desktop najdete v tématu [typy vizualizací v Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-visualization-types-for-reports-and-q-and-a/). 

## <a name="troubleshooting"></a>Řešení potíží

Pokud se zobrazí následující chyba, ujistěte se, že hodnoty pro **hostitele** a **přístupová oprávnění** , které jste zkopírovali Azure Portal v [kroku 2](#connect) jsou správné, a pak to zkuste znovu. Použijte tlačítka Kopírovat napravo od **hostitele** a přístupové hodnoty **klíčů** v Azure Portal ke zkopírování hodnoty bez chyb.

```output
[HY000]: [Microsoft][Azure Cosmos DB] (401) HTTP 401 Authentication Error: {"code":"Unauthorized","message":"The input authorization token can't serve the request. Please check that the expected payload is built as per the protocol, and check the key being used. Server used the following payload to sign: 'get\ndbs\n\nfri, 20 jan 2017 03:43:55 gmt\n\n'\r\nActivityId: 9acb3c0d-cb31-4b78-ac0a-413c8d33e373"}
```


## <a name="next-steps"></a>Další kroky

Další informace o Cosmos Azure DB najdete v tématu [Vítá vás Azure Cosmos DB](introduction.md).