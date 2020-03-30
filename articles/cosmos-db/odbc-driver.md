---
title: Připojení k Azure Cosmos DB pomocí analytických nástrojů BI
description: Zjistěte, jak pomocí ovladače OdBC Služby Azure Cosmos DB vytvářet tabulky a zobrazení, aby bylo možné zobrazit normalizovaná data v softwaru BI a analýzy dat.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: sngun
ms.openlocfilehash: 8be17f0b624c5c34709fb420adb434b77dbc0d91
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721077"
---
# <a name="connect-to-azure-cosmos-db-using-bi-analytics-tools-with-the-odbc-driver"></a>Připojení k Azure Cosmos DB pomocí analytických nástrojů BI pomocí ovladače ODBC

Ovladač ODBC Azure Cosmos DB umožňuje připojení k Azure Cosmos DB pomocí analytických nástrojů BI, jako jsou služby integrace SQL Serveru, Desktop Power BI a Tableau, takže v těchto řešeních můžete analyzovat a vytvářet vizualizace dat Azure Cosmos DB.

Ovladač ODBC pro Azure Cosmos DB je kompatibilní s rozhraním ODBC 3.8 a podporuje syntaxi ANSI SQL-92. Tento ovladač nabízí bohaté funkce, které vám pomůžou znovu normalizovat data ve službě Azure Cosmos DB. S využitím tohoto ovladače můžete data ve službě Azure Cosmos DB reprezentovat jako tabulky a zobrazení. Tento ovladač umožňuje provádět operace SQL s tabulkami a zobrazeními, včetně seskupení podle dotazů a operací vložení, aktualizace a odstranění.

> [!NOTE]
> Připojení k Azure Cosmos DB s ovladačem ODBC je aktuálně podporované pro účty SQL API Azure Cosmos DB.

## <a name="why-do-i-need-to-normalize-my-data"></a>Proč je nutné normalizovat data?
Azure Cosmos DB je databáze bez schématu, která umožňuje rychlý vývoj aplikací a schopnost iterate na datových modelech, aniž by se omezovala na přísné schéma. Jedna databáze Azure Cosmos může obsahovat dokumenty JSON různých struktur. To je skvělé pro rychlý vývoj aplikací, ale když chcete analyzovat a vytvářet sestavy dat pomocí analýzy dat a nástrojů BI, data často musí být sloučí tea a dodržovat konkrétní schéma.

To je místo, kde ovladač ODBC přichází. Pomocí ovladače ODBC teď můžete znovu normalizovat data v Azure Cosmos DB do tabulek a zobrazení, které odpovídají vašim potřebám analýzy dat a vytváření sestav. Renormalized schémata nemají žádný vliv na podkladová data a neomezují vývojáři dodržovat. Místo toho umožňují využít nástroje kompatibilní s rozhraním ODBC pro přístup k datům. Takže teď vaše databáze Azure Cosmos nebude oblíbená jenom pro váš vývojový tým, ale vaši analytici dat ji budou také milovat.

Začněme s ovladačem ODBC.

## <a name="step-1-install-the-azure-cosmos-db-odbc-driver"></a><a id="install"></a>Krok 1: Instalace ovladače ODBC služby Azure Cosmos DB

1. Stáhněte si ovladače pro vaše prostředí:

    | Instalační služba | Podporované operační systémy| 
    |---|---| 
    |[64bitový soubor Microsoft Azure Cosmos DB ODBC](https://aka.ms/cosmos-odbc-64x64) pro 64bitový systém Windows| 64bitové verze Windows 8.1 nebo novější, Windows 8, Windows 7, Windows Server 2012 R2, Windows Server 2012 a Windows Server 2008 R2.| 
    |[Microsoft Azure Cosmos DB ODBC 32x64bit.msi](https://aka.ms/cosmos-odbc-32x64) pro 32bitový v 64bitovém systému Windows| 64bitové verze systému Windows 8.1 nebo novější, Windows 8, Windows 7, Windows XP, Windows Vista, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 a Windows Server 2003.| 
    |[Microsoft Azure Cosmos DB ODBC 32bitový.msi](https://aka.ms/cosmos-odbc-32x32) pro 32bitový systém Windows|32bitové verze Windows 8.1 nebo novější, Windows 8, Windows 7, Windows XP a Windows Vista.|

    Spusťte soubor msi místně, který spustí **Průvodce instalací ovladače ODBC databáze Microsoft Azure Cosmos DB**. 

1. Dokončete průvodce instalací pomocí výchozího vstupu k instalaci ovladače ODBC.

1. Otevřete v počítači aplikaci **Správce dat od rozhraní ODBC.** To lze provést zadáním **zdrojů dat ODBC** do vyhledávacího pole systému Windows. 
    Ovladač můžete potvrdit, že byl nainstalován, kliknutím na kartu **Ovladače** a zajištěním, že je uveden **ovladač ODBC služby Microsoft Azure Cosmos DB.**

    ![Správce zdroje dat Azure Cosmos DB ODBC](./media/odbc-driver/odbc-driver.png)

## <a name="step-2-connect-to-your-azure-cosmos-database"></a><a id="connect"></a>Krok 2: Připojení k databázi Azure Cosmos

1. Po [instalaci ovladače ODBC služby Azure Cosmos DB](#install)klikněte v okně Správce zdroje dat rozhraní **ODBC** na **tlačítko Přidat**. Můžete vytvořit uživatelské nebo systémové DSN. V tomto příkladu vytváříte dsn uživatele.

1. V okně **Vytvořit nový zdroj dat** vyberte ovladač **ODBC databáze Microsoft Azure Cosmos DB**a klepněte na tlačítko **Dokončit**.

1. V okně **nastavení ovladače Azure Cosmos DB ODBC Driver SDN** vyplňte následující informace: 

    ![Okno nastavení dsn ovladače OdBC služby Azure Cosmos DB](./media/odbc-driver/odbc-driver-dsn-setup.png)
    - **Název zdroje dat**: Vlastní popisný název pro dsn ODBC. Tento název je jedinečný pro váš účet Azure Cosmos DB, proto jej pojmenujte odpovídajícím způsobem, pokud máte více účtů.
    - **Popis**: Stručný popis zdroje dat.
    - **Hostitel:** IDENTIFIKÁTOR URI pro váš účet Azure Cosmos DB. Můžete načíst ze stránky Azure Cosmos DB Keys na webu Azure Portal, jak je znázorněno na následujícím snímku obrazovky. 
    - **Přístupový klíč**: Primární nebo sekundární klíč pro čtení a zápis nebo jen pro čtení ze stránky Klíče Azure Cosmos DB na webu Azure Portal, jak je znázorněno na následujícím snímku obrazovky. Doporučujeme použít klíč jen pro čtení, pokud se dsn používá pro zpracování dat jen pro čtení a vytváření sestav.
    ![Stránka Klíče Azure Cosmos DB](./media/odbc-driver/odbc-cosmos-account-keys.png)
    - **Šifrovat přístupový klíč pro:** Vyberte nejlepší volbu na základě uživatelů tohoto počítače. 
    
1. Kliknutím na tlačítko **Test** se ujistěte, že se můžete připojit ke svému účtu Azure Cosmos DB. 

1.  Klikněte na **Upřesnit možnosti** a nastavte následující hodnoty:
    *  **Verze rozhraní REST API**: Vyberte [verzi rozhraní REST API](https://docs.microsoft.com/rest/api/cosmos-db/) pro vaše operace. Výchozí 2015-12-16. Pokud máte kontejnery s [velkými klíči oddílů](large-partition-keys.md) a vyžadují rozhraní REST API verze 2018-12-31:
        - Zadejte **2018-12-31** pro verzi REST API
        - V nabídce **Start** zadejte příkaz "regedit" pro vyhledání a otevření aplikace **Editor registru.**
        - V Editoru registru přejděte na cestu: **Počítač\HKEY_LOCAL_MACHINE\SOFTWARE\ODBC\ODBC. INI**
        - Vytvořte nový podklíč se stejným názvem jako dsn, například "Contoso account ODBC DSN".
        - Přejděte do podklíče Účet Contoso ODBC DSN.
        - Kliknutím pravým tlačítkem myši přidáte novou hodnotu **String:**
            - Název hodnoty: **Token ignorsession**
            - Údaje o hodnotě: **1**
            ![nastavení editoru registru](./media/odbc-driver/cosmos-odbc-edit-registry.png)
    - **Konzistence dotazu**: Vyberte [úroveň konzistence](consistency-levels.md) pro vaše operace. Výchozí hodnota je Session.
    - **Počet opakování**: Zadejte počet opakování operace, pokud počáteční požadavek není dokončen z důvodu omezení rychlosti provozu.
    - **Soubor schématu**: Zde máte řadu možností.
        - Ve výchozím nastavení ponechat tuto položku tak, jak je (prázdné), ovladač prohledá první stránku dat pro všechny kontejnery k určení schématu každého kontejneru. To se označuje jako mapování kontejnerů. Bez definovaného souboru schématu musí ovladač provést prohledávací akci pro každou relaci ovladače a může vést k vyšší době spuštění aplikace pomocí dsn. Doporučujeme vždy přidružit soubor schématu pro DSN.
        - Pokud již máte soubor schématu (případně soubor, který jste vytvořili pomocí Editoru schématu), můžete kliknout na **Procházet**, přejít na soubor, kliknout na **Uložit**a potom kliknout na **OK**.
        - Pokud chcete vytvořit nové schéma, klepněte na tlačítko **OK**a v hlavním okně klepněte na **položku Editor schémat.** Potom pokračujte k informacím editoru schémat. Po vytvoření nového souboru schématu nezapomeňte přejít zpět do okna **Upřesnit možnosti** a zahrnout nově vytvořený soubor schématu.

1. Po dokončení a zavření okna **nastavení DSN ovladače Azure Cosmos DB ODBC** se přidá nový uživatelský dsn na kartu DSN uživatele.

    ![Nový Azure Cosmos DB ODBC DSN na kartě DSN uživatele](./media/odbc-driver/odbc-driver-user-dsn.png)

## <a name="step-3-create-a-schema-definition-using-the-container-mapping-method"></a><a id="#container-mapping"></a>Krok 3: Vytvoření definice schématu pomocí metody mapování kontejneru

Existují dva typy metod vzorkování, které můžete použít: **mapování kontejnerů** nebo **oddělovače tabulek**. Vzorkovací relace může využít obě metody odběru vzorků, ale každá nádoba může používat pouze specifickou metodu vzorkování. Následující kroky vytvořit schéma pro data v jednom nebo více kontejnerů pomocí metody mapování kontejneru. Tato metoda vzorkování načte data na stránce kontejneru k určení struktury dat. Převádí kontejner do tabulky na straně ODBC. Tato metoda odběru vzorků je účinná a rychlá, pokud jsou data v kontejneru homogenní. Pokud kontejner obsahuje heterogenní typ dat, doporučujeme použít [metodu mapování oddělovačů tabulek,](#table-mapping) protože poskytuje robustnější metodu vzorkování k určení datových struktur v kontejneru. 

1. Po dokončení kroků 1-4 v [okně Připojení k databázi Azure Cosmos](#connect)klikněte v okně **nastavení dsn ovladače Azure Cosmos DB NAODB na položku** **Editor** schémat.

    ![Tlačítko Editor schématu v okně nastavení dsn ovladače Azure Cosmos DB ODBC](./media/odbc-driver/odbc-driver-schema-editor.png)
1. V okně **Editor schémat** klepněte na tlačítko **Vytvořit nový**.
    Okno **Generovat schéma** zobrazí všechny kontejnery v účtu Azure Cosmos DB. 

1. Vyberte jednu nebo více kontejnerů, které chcete vzorkovat, a klepněte na **položku Ukázka**. 

1. Na kartě **Návrhové zobrazení** jsou reprezentovány databáze, schéma a tabulka. V zobrazení tabulky se při prohledávce zobrazí sada vlastností přidružených k názvům sloupců (název SQL, zdrojový název atd.).
    Pro každý sloupec můžete upravit název sloupce SQL, typ SQL, délku SQL (pokud je k dispozici), měřítko (pokud je k dispozici), přesnost (pokud je k dispozici) a Nullable.
    - **Chcete-li** tento sloupec vyloučit z výsledků dotazu, můžete skrýt sloupec na **hodnotu true.** Sloupce označené Skrýt sloupec = true nejsou vráceny pro výběr a projekci, i když jsou stále součástí schématu. Můžete například skrýt všechny vlastnosti požadované systému Azure Cosmos DB počínaje "_".
    - Sloupec **id** je jediné pole, které nelze skrýt, protože se používá jako primární klíč v normalizovaném schématu. 

1. Po dokončení definování schématu klepněte na **položku Uložit soubor** | **Save**, přejděte do adresáře a uložte schéma a klepněte na tlačítko **Uložit**.

1. Chcete-li toto schéma použít s dsn, otevřete **okno nastavení dsn ovladače Azure Cosmos DB ODBC Driver** (prostřednictvím správce zdroje dat ROZHRANÍ ODBC), klepněte na tlačítko **Upřesnit možnosti**a potom v poli **Soubor schématu** přejděte na uložené schéma. Uložení souboru schématu do existujícího dsn upravuje připojení DSN k oboru na data a strukturu definovanou schématem.

## <a name="step-4-create-a-schema-definition-using-the-table-delimiters-mapping-method"></a><a id="table-mapping"></a>Krok 4: Vytvoření definice schématu pomocí metody mapování oddělovačů tabulek

Existují dva typy metod vzorkování, které můžete použít: **mapování kontejnerů** nebo **oddělovače tabulek**. Vzorkovací relace může využít obě metody odběru vzorků, ale každá nádoba může používat pouze specifickou metodu vzorkování. 

Následující kroky vytvoří schéma pro data v jednom nebo více kontejnerech pomocí metody mapování **oddělovačů tabulek.** Doporučujeme použít tuto metodu vzorkování, pokud kontejnery obsahují heterogenní typ dat. Tuto metodu můžete použít k oboru vzorkování na sadu atributů a jeho odpovídající hodnoty. Například pokud dokument obsahuje vlastnost "Typ", můžete obor vzorkování na hodnoty této vlastnosti. Konečným výsledkem vzorkování by byla sada tabulek pro každou z hodnot pro Typ, který jste zadali. Například Type = Car vytvoří tabulku Car, zatímco Typ = Rovina vytvoří rovinnou tabulku.

1. Po dokončení kroků 1-4 v [okně Připojení k databázi Azure Cosmos](#connect)klikněte v okně nastavení dsn ovladače Azure Cosmos DB NAODB na položku **Editor** schémat.

1. V okně **Editor schémat** klepněte na tlačítko **Vytvořit nový**.
    Okno **Generovat schéma** zobrazí všechny kontejnery v účtu Azure Cosmos DB. 

1. Vyberte kontejner na kartě **Ukázkové zobrazení** a ve sloupci **Definice mapování** pro kontejner klikněte na **Upravit**. Potom v okně **Definice mapování** vyberte **metodu oddělovačů tabulek.** Potom udělejte následující:

    a. Do pole **Atributy** zadejte název vlastnosti oddělovače. Toto je vlastnost v dokumentu, kterou chcete obor vzorkování, například město a stiskněte klávesu ENTER. 

    b. Pokud chcete vzorkování obor pouze na určité hodnoty pro atribut, který jste zadali výše, vyberte atribut ve výběrovém poli, zadejte hodnotu do pole **Hodnota** (např. Můžete pokračovat v přidávání více hodnot pro atributy. Pouze se ujistěte, že je při zadávání hodnot vybrán správný atribut.

    Pokud například zahrnete hodnotu **Atributy** města a chcete tabulku omezit tak, aby zahrnovala pouze řádky s hodnotou města New York a Dubaj, zadejte město do pole Atributy a do pole **Hodnoty** v Poli Hodnoty v New Yorku a pak v Dubaji.

1. Klikněte na tlačítko **OK**. 

1. Po dokončení definic mapování pro kontejnery, které chcete utípnout, klikněte v okně **Editor schémat** **ukáznit ukázku**.
     Pro každý sloupec můžete upravit název sloupce SQL, typ SQL, délku SQL (pokud je k dispozici), měřítko (pokud je k dispozici), přesnost (pokud je k dispozici) a Nullable.
    - **Chcete-li** tento sloupec vyloučit z výsledků dotazu, můžete skrýt sloupec na **hodnotu true.** Sloupce označené Skrýt sloupec = true nejsou vráceny pro výběr a projekci, i když jsou stále součástí schématu. Můžete například skrýt všechny vlastnosti požadované systému `_`Azure Cosmos DB počínaje .
    - Sloupec **id** je jediné pole, které nelze skrýt, protože se používá jako primární klíč v normalizovaném schématu. 

1. Po dokončení definování schématu klepněte na **položku Uložit soubor** | **Save**, přejděte do adresáře a uložte schéma a klepněte na tlačítko **Uložit**.

1. V okně **Nastavení dsn ovladače Azure Cosmos DB db** klikněte na **Upřesnit možnosti**. Potom v poli **Soubor schématu** přejděte do uloženého souboru schématu a klepněte na tlačítko **OK**. Dalším klepnutím na **tlačítko OK** uložte dsn. Tím se uloží schéma, které jste vytvořili, do dsn. 

## <a name="optional-set-up-linked-server-connection"></a>(Nepovinné) Nastavení připojení propojeného serveru

Azure Cosmos DB můžete dotazovat ze služby SQL Server Management Studio (SSMS) nastavením připojení propojeného serveru.

1. Vytvořte zdroj systémových dat, jak je `SDS Name`popsáno v kroku [2](#connect), pojmenovaném například .

1. [Nainstalujte sql server management studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) a připojte se k serveru. 

1. V editoru dotazů SSMS vytvořte propojený objekt `DEMOCOSMOS` serveru pro zdroj dat pomocí následujících příkazů. Nahraďte `DEMOCOSMOS` název propojeného serveru `SDS Name` a názvem systémového zdroje dat.

    ```sql
    USE [master]
    GO
    
    EXEC master.dbo.sp_addlinkedserver @server = N'DEMOCOSMOS', @srvproduct=N'', @provider=N'MSDASQL', @datasrc=N'SDS Name'
    
    EXEC master.dbo.sp_addlinkedsrvlogin @rmtsrvname=N'DEMOCOSMOS', @useself=N'False', @locallogin=NULL, @rmtuser=NULL, @rmtpassword=NULL
    
    GO
    ```
    
Chcete-li zobrazit nový název propojeného serveru, aktualizujte seznam Propojené servery.

![Propojený server v SSMS](./media/odbc-driver/odbc-driver-linked-server-ssms.png)

### <a name="query-linked-database"></a>Propojený dotaz na databázi

Chcete-li zadat dotaz na propojenou databázi, zadejte dotaz SSMS. V tomto příkladu dotaz vybere z tabulky `customers`v kontejneru s názvem :

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
> Propojený server Cosmos DB nepodporuje čtyřdílné pojmenování. Chyba je vrácena podobně jako následující zpráva:

```
Msg 7312, Level 16, State 1, Line 44

Invalid use of schema or catalog for OLE DB provider "MSDASQL" for linked server "DEMOCOSMOS". A four-part name was supplied, but the provider does not expose the necessary interfaces to use a catalog or schema.
``` 

## <a name="optional-creating-views"></a>(Nepovinné) Vytváření pohledů
Můžete definovat a vytvářet pohledy jako součást procesu vzorkování. Tato zobrazení jsou ekvivalentní zobrazení SQL. Jsou jen pro čtení a jsou rozsah výběry a projekce dotazu SQL Azure Cosmos DB definované. 

Pokud chcete vytvořit zobrazení pro data, klikněte v okně **Editor schémat** ve sloupci **Definice zobrazení** na **Přidat** na řádku kontejneru, který chcete utvořit. 
    ![Vytvoření zobrazení dat](./media/odbc-driver/odbc-driver-create-view.png)


Potom v okně **Zobrazit definice** postupujte takto:

1. Klepněte na **tlačítko Nový**, zadejte název zobrazení, například EmployeesfromSeattleView a klepněte na tlačítko **OK**.

1. V okně **Upravit zobrazení** zadejte dotaz Azure Cosmos DB. Musí se jednat o [dotaz SQL služby Azure Cosmos DB](how-to-sql-query.md), například `SELECT c.City, c.EmployeeName, c.Level, c.Age, c.Manager FROM c WHERE c.City = "Seattle"`klikněte na tlačítko **OK**.

    ![Přidání dotazu při vytváření zobrazení](./media/odbc-driver/odbc-driver-create-view-2.png)


Můžete vytvořit mnoho zobrazení, jak se vám líbí. Po dokončení definování zobrazení můžete data ochutnat. 

## <a name="step-5-view-your-data-in-bi-tools-such-as-power-bi-desktop"></a>Krok 5: Zobrazení dat v nástrojích BI, jako je Power BI Desktop

Pomocí nového dsn pro připojení k Azure Cosmos DB pomocí všech nástrojů kompatibilních s rozhraním ODBC můžete jednoduše ukázat, jak se připojit k Power BI Desktopu a vytvořit vizualizaci Power BI.

1. Otevřete Power BI Desktop.

1. Klepněte na tlačítko **Získat data**.

    ![Načtení dat v Power BI Desktopu](./media/odbc-driver/odbc-driver-power-bi-get-data.png)

1. V okně **Získat data** klepněte na **položku Jiné** | **připojení ODBC** | **.**

    ![Volba zdroje dat ODBC v Power BI Get Data](./media/odbc-driver/odbc-driver-power-bi-get-data-2.png)

1. V okně **Z rozhraní ODBC** vyberte název zdroje dat, který jste vytvořili, a klepněte na tlačítko **OK**. Položky **Upřesnit možnosti** můžete ponechat prázdné.

    ![Volba Název zdroje dat (DSN) v Power BI Get Data](./media/odbc-driver/odbc-driver-power-bi-get-data-3.png)

1. V okně **Access a data using a ODBC driver** vyberte Default or **Custom** a potom klepněte na **tlačítko Připojit**. Není nutné zahrnout **vlastnosti připojovacího řetězce Pověření**.

1. V okně **Navigátor** rozbalte v levém podokně databázi, schéma a vyberte tabulku. Podokno výsledků obsahuje data pomocí schématu, které jste vytvořili.

    ![Výběr tabulky v Power BI Get Data](./media/odbc-driver/odbc-driver-power-bi-get-data-4.png)

1. Pokud chcete data vizualizovat na ploše Power BI, zaškrtněte políčko před názvem tabulky a klikněte na **Načíst**.

1. V Power BI Desktopu vyberte úplně vlevo kartu Data ![Karta Data v Power BI Desktopu](./media/odbc-driver/odbc-driver-data-tab.png) potvrďte, že vaše data byla importována.

1. Vizuály teď můžete vytvářet pomocí Power ![BI tak,](./media/odbc-driver/odbc-driver-report-tab.png)že kliknete na kartu Sestava na kartě Sestava v Power BI Desktopu , kliknete na **Nový vizuál**a pak si přizpůsobíte dlaždici. Další informace o vytváření vizualizací v Power BI Desktopu najdete v tématu [Typy vizualizací v Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-visualization-types-for-reports-and-q-and-a/).

## <a name="troubleshooting"></a>Řešení potíží

Pokud se zobrazí následující chyba, ujistěte se, že hodnoty **hostitele** a **přístupového klíče,** které jste zkopírovali portál Azure v [kroku 2,](#connect) jsou správné a pak opakujte akci. Pomocí tlačítek kopírování vpravo od hodnot **Klíče hostitele** a **přístupu** na webu Azure Portal zkopírujte hodnoty bez chyb.

    [HY000]: [Microsoft][Azure Cosmos DB] (401) HTTP 401 Authentication Error: {"code":"Unauthorized","message":"The input authorization token can't serve the request. Please check that the expected payload is built as per the protocol, and check the key being used. Server used the following payload to sign: 'get\ndbs\n\nfri, 20 jan 2017 03:43:55 gmt\n\n'\r\nActivityId: 9acb3c0d-cb31-4b78-ac0a-413c8d33e373"}`

## <a name="next-steps"></a>Další kroky

Další informace o Cosmos Azure DB najdete v tématu [Vítá vás Azure Cosmos DB](introduction.md).
