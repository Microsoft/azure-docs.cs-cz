---
title: Vytvoření a dotazování synapse fondu SQL (Azure Portal)
description: Vytvoření a dotazování synapse fondu SQL pomocí Azure Portal
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: development
ms.date: 05/28/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 6966932e95ff538de4b2f9be1ac06516311a0919
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2020
ms.locfileid: "79129245"
---
# <a name="quickstart-create-and-query-a-synapse-sql-pool-using-the-azure-portal"></a>Rychlý Start: vytvoření a dotazování synapse fondu SQL pomocí Azure Portal

Rychle vytvářejte a Dotazujte synapse fond SQL (datový sklad) ve službě Azure synapse Analytics (dřív SQL DW) pomocí Azure Portal.

## <a name="prerequisites"></a>Předpoklady

1. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný](https://azure.microsoft.com/free/) účet před tím, než začnete.

   > [!NOTE]
   > Vytvoření fondu SQL ve službě Azure synapse může vést k nové Fakturovatelné službě. Další informace najdete v tématu [ceny služby Azure synapse Analytics](https://azure.microsoft.com/pricing/details/synapse-analytics/).

2. Stáhněte a nainstalujte nejnovější verzi aplikace [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

## <a name="create-a-sql-pool"></a>Vytvoření fondu SQL

Datové sklady se vytvářejí pomocí fondu SQL ve službě Azure synapse Analytics. Vytvoří se fond SQL s definovanou sadou [výpočetních prostředků](memory-concurrency-limits.md). Databáze se vytvoří v rámci [skupiny prostředků Azure](../azure-resource-manager/management/overview.md) a na [logickém serveru SQL Azure](../sql-database/sql-database-servers.md).

Pomocí těchto kroků vytvořte fond SQL, který obsahuje ukázková data **AdventureWorksDW** .

1. v levém horním rohu Azure Portal vyberte **vytvořit prostředek** .

   ![Vytvoření prostředku v Azure Portal](media/create-data-warehouse-portal/create-a-resource.png)

2. Na stránce **Nový** vyberte **databáze** a v seznamu **Doporučené** vyberte **Azure synapse Analytics (dříve SQL DW)** .

   ![vytvoření prázdného datového skladu](media/create-data-warehouse-portal/create-a-data-warehouse.png)

3. V oblasti **základy**zadejte své předplatné, skupinu prostředků, název fondu SQL a název serveru:

   | Nastavení | Navrhovaná hodnota | Popis |
   | :------ | :-------------- | :---------- |
   | **Předplatné** | Vaše předplatné | Podrobnosti o vašich předplatných najdete v tématu [Předplatná](https://account.windowsazure.com/Subscriptions). |
   | **Skupina prostředků** | myResourceGroup | Platné názvy skupin prostředků najdete v tématu [Pravidla a omezení pojmenování](/azure/architecture/best-practices/resource-naming). |
   | **Název fondu SQL** | Jakýkoli globálně jedinečný název (příklad je *mySampleDataWarehouse*) | Platné názvy databází najdete v tématu [Identifikátory databází](/sql/relational-databases/databases/database-identifiers). Všimněte si, že fond SQL je jedním z typů databáze. |
   | **Server** | Libovolný globálně jedinečný název | Vyberte existující server nebo vytvořte nový název serveru, vyberte **vytvořit novou**. Platné názvy serverů najdete v tématu [Pravidla a omezení pojmenování](/azure/architecture/best-practices/resource-naming). |

   ![vytvoření základních podrobností datového skladu](media/create-data-warehouse-portal/create-sql-pool-basics.png)

4. V části **úroveň výkonu**vyberte **Vybrat úroveň výkonu** a volitelně změňte konfiguraci pomocí posuvníku.

   ![změnit úroveň výkonu datového skladu](media/create-data-warehouse-portal/create-sql-pool-performance-level.png)  

   Další informace o úrovních výkonu najdete v tématu [Správa výpočetních prostředků v Azure SQL Data Warehouse](sql-data-warehouse-manage-compute-overview.md).

5. Teď, když jste dokončili kartu základy formuláře Azure synapse Analytics, vyberte **zkontrolovat + vytvořit** a pak **vytvořit** a vytvořte fond SQL. Zřizování trvá několik minut.

   ![vybrat kontrolu + vytvořit](media/create-data-warehouse-portal/create-sql-pool-review-create.png)

   ![výběr možnosti Vytvořit](media/create-data-warehouse-portal/create-sql-pool-create.png)

6. Na panelu nástrojů vyberte **oznámení** pro monitorování procesu nasazení.

   ![oznámení](media/create-data-warehouse-portal/notification.png)

## <a name="create-a-server-level-firewall-rule"></a>Vytvoření pravidla brány firewall na úrovni serveru

Služba Azure synapse vytvoří bránu firewall na úrovni serveru. Tato brána firewall zabraňuje externím aplikacím a nástrojům v připojení k serveru nebo jakékoli databázi na serveru. Pokud chcete umožnit připojení, můžete přidat pravidla brány firewall, která povolí připojení z konkrétních IP adres. Postupujte podle těchto pokynů a vytvořte [pravidlo brány firewall na úrovni serveru](../sql-database/sql-database-firewall-configure.md) pro IP adresu vašeho klienta.

> [!NOTE]
> Azure synapse komunikuje přes port 1433. Pokud se pokoušíte připojit z podnikové sítě, nemusí být odchozí provoz přes port 1433 bránou firewall vaší sítě povolený. Pokud je to tak, nebudete se moct připojit k serveru služby Azure SQL Database, dokud vaše IT oddělení neotevře port 1433.

1. Po dokončení nasazení vyberte v nabídce na levé straně **všechny služby** . Vyberte **databáze**, vyberte hvězdičku vedle položky **Azure synapse Analytics** a přidejte Azure synapse Analytics k oblíbeným položkám.

2. V nabídce na levé straně vyberte **Azure synapse Analytics** a pak na stránce **Azure synapse Analytics** vyberte **mySampleDataWarehouse** . Otevře se stránka s přehledem pro vaši databázi, na které se zobrazí plně kvalifikovaný název serveru (například **sqlpoolservername.Database.Windows.NET**), který poskytuje možnosti pro další konfiguraci.

3. Zkopírujte tento plně kvalifikovaný název serveru, abyste ho mohli použít pro připojení k serveru a jeho databázím v tomto a dalším rychlém startu. Pokud chcete otevřít nastavení serveru, vyberte název serveru.

   ![vyhledání názvu serveru](media/create-data-warehouse-portal/find-server-name.png)

4. Vyberte možnost **Zobrazit nastavení brány firewall**.

   ![nastavení serveru](media/create-data-warehouse-portal/server-settings.png)

5. Otevře se stránka **Nastavení brány firewall** pro server služby SQL Database.

   ![pravidlo brány firewall serveru](media/create-data-warehouse-portal/server-firewall-rule.png)

6. Pokud chcete přidat aktuální IP adresu k novému pravidlu brány firewall, vyberte **Přidat IP adresu klienta** na panelu nástrojů. Pravidlo brány firewall může otevřít port 1433 pro jednu IP adresu nebo rozsah IP adres.

7. Vyberte **Uložit**. Vytvoří se pravidlo brány firewall na úrovni serveru pro vaši aktuální IP adresu, které otevře port 1433 na logickém serveru.

8. Vyberte **OK** a pak zavřete stránku **nastavení brány firewall** .

Nyní se můžete připojit k SQL serveru a jeho fondům SQL pomocí této IP adresy. Připojení funguje z aplikace SQL Server Management Studio nebo jiného nástroje podle vašeho výběru. Při připojování použijte účet správce serveru, který jste předtím vytvořili.

> [!IMPORTANT]
> Standardně je přístup přes bránu firewall služby SQL Database povolený pro všechny služby Azure. na této stránce vyberte **vypnuto** a pak výběrem **Uložit** zakažte bránu firewall pro všechny služby Azure.

## <a name="get-the-fully-qualified-server-name"></a>Získání plně kvalifikovaného názvu serveru

Na webu Azure Portal získejte plně kvalifikovaný název vašeho serveru SQL. Tento plně kvalifikovaný název použijete později při připojování k serveru.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

2. V nabídce na levé straně vyberte **Azure synapse Analytics** a na stránce **Azure synapse Analytics** vyberte svůj.

3. V podokně **Základy** na stránce webu Azure Portal pro vaši databázi vyhledejte a potom zkopírujte **Název serveru**. V tomto příkladu je plně kvalifikovaný název sqlpoolservername.database.windows.net.

    ![informace o připojení](media/create-data-warehouse-portal/find-server-name-copy.png)

## <a name="connect-to-the-server-as-server-admin"></a>Připojení k serveru jako správce serveru

V této části se pomocí aplikace [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) naváže připojení k serveru SQL Azure.

1. Otevřete SQL Server Management Studio.

2. V dialogovém okně **Připojení k serveru** zadejte následující informace:

   | Nastavení | Navrhovaná hodnota | Popis |
   | :------ | :-------------- | :---------- |
   | Typ serveru | Databázový stroj | Tato hodnota se vyžaduje. |
   | Název serveru | Plně kvalifikovaný název serveru | Tady je příklad: **sqlpoolservername.Database.Windows.NET**. |
   | Ověřování | Ověřování SQL Serveru | Ověřování SQL je jediný typ ověřování, který se v tomto kurzu konfiguruje. |
   | Přihlásit | Účet správce serveru | Účet, který jste zadali při vytváření serveru. |
   | Heslo | Heslo pro účet správce serveru | Heslo, které jste zadali při vytváření serveru. |
   ||||

   ![Připojení k serveru](media/create-data-warehouse-portal/connect-to-server-ssms.png)

3. Vyberte **připojit**. V aplikaci SSMS se otevře okno Průzkumníka objektů. 

4. V Průzkumníku objektů rozbalte **Databáze**. Pak rozbalte **mySampleDatabase** a zobrazte objekty v nové databázi.

   ![databázové objekty](media/create-data-warehouse-portal/connected-ssms.png) 

## <a name="run-some-queries"></a>Spuštění nějakých dotazů

SQL Data Warehouse jako dotazovací jazyk používá T-SQL. Pokud chcete otevřít okno dotazu a spustit nějaké dotazy T-SQL, použijte následující postup:

1. Pravým tlačítkem vyberte **mySampleDataWarehouse** a vyberte **Nový dotaz**. Otevře se nové okno dotazu.

2. V okně dotazu zadejte následující příkaz, který zobrazí seznam databází.

    ```sql
    SELECT * FROM sys.databases
    ```

3. Vyberte **provést**. Výsledky dotazu ukazují dvě databáze: **master** a **mySampleDataWarehouse**.

   ![Dotazování databází](media/create-data-warehouse-portal/query-databases.png)

4. Pokud se chcete podívat na nějaká data, pomocí následujícího příkazu zobrazte počet zákazníků s příjmením Adams, kteří mají tři děti. Seznam výsledků obsahuje šest zákazníků. 

    ```sql
    SELECT LastName, FirstName FROM dbo.dimCustomer
    WHERE LastName = 'Adams' AND NumberChildrenAtHome = 3;
    ```

   ![Dotaz dbo.dimCustomer](media/create-data-warehouse-portal/query-customer.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Účtují se vám poplatky za jednotky datového skladu a data uložená ve vašem fondu SQL. Výpočetní prostředky a prostředky úložiště se účtují odděleně.

- Pokud chcete uchovávat data v úložišti, můžete pozastavit výpočetní prostředí, když nepoužíváte fond SQL. Když pozastavíte výpočetní prostředky, bude se vám účtovat jenom úložiště dat. Můžete pokračovat v výpočtůch, kdykoli budete připraveni na práci s daty.

- Pokud chcete odebrat budoucí poplatky, můžete odstranit fond SQL.

Pomocí těchto kroků vyčistěte prostředky, které už nepotřebujete.

1. Přihlaste se k [Azure Portal](https://portal.azure.com)vyberte svůj fond SQL.

   ![Vyčištění prostředků](media/create-data-warehouse-portal/clean-up-resources.png)

2. Pokud chcete pozastavit výpočetní prostředky, vyberte tlačítko **pozastavit** . Když je fond SQL pozastaven, zobrazí se tlačítko pro **obnovení** . Chcete-li obnovit výpočetní výkon, vyberte možnost **pokračovat**.

3. Pokud chcete odebrat fond SQL, aby se vám neúčtovaly výpočetní výkon nebo úložiště, vyberte **Odstranit**.

4. Pokud chcete odebrat vytvořený SQL Server, vyberte na předchozím obrázku **sqlpoolservername.Database.Windows.NET** a pak vyberte **Odstranit**. S tímto odstraněním buďte opatrní, protože odstraněním serveru se odstraní také všechny databáze k tomuto serveru přiřazené.

5. Pokud chcete odebrat skupinu prostředků, vyberte **myResourceGroup**a pak vyberte **Odstranit skupinu prostředků**.

## <a name="next-steps"></a>Další kroky

Pokud se chcete dozvědět víc o načítání dat do fondu SQL, přejděte do článku o [načtení dat do fondu SQL](load-data-from-azure-blob-storage-using-polybase.md) . 
