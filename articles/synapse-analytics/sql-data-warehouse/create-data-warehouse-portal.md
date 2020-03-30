---
title: Vytvoření a dotazování fondu Synapse SQL (portál Azure)
description: Vytvoření a dotazování fondu Synapse SQL pomocí portálu Azure
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 05/28/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 5d5b2509b212172758fa867d9f27b829f43aeeaa
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349102"
---
# <a name="quickstart-create-and-query-a-synapse-sql-pool-using-the-azure-portal"></a>Úvodní příručka: Vytvoření a dotazování fondu Synapse SQL pomocí portálu Azure

Rychle vytvořte a dotazujte fondu Synapse SQL (datový sklad) v Azure Synapse Analytics (dříve SQL DW) pomocí portálu Azure.

## <a name="prerequisites"></a>Požadavky

1. Pokud nemáte předplatné Azure, vytvořte si [bezplatný](https://azure.microsoft.com/free/) účet, než začnete.

   > [!NOTE]
   > Vytvoření fondu SQL v Azure Synapse může mít za následek novou fakturovatelnou službu. Další informace najdete v [tématu Azure Synapse Analytics ceny](https://azure.microsoft.com/pricing/details/synapse-analytics/).

2. Stáhněte a nainstalujte nejnovější verzi aplikace [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k [portálu Azure](https://portal.azure.com/).

## <a name="create-a-sql-pool"></a>Vytvoření fondu SQL

Datové sklady se vytvářejí pomocí fondu SQL v Azure Synapse Analytics. Fond SQL se vytvoří s definovanou sadou [výpočetních prostředků](memory-concurrency-limits.md). Databáze se vytvoří v rámci [skupiny prostředků Azure](../../azure-resource-manager/management/overview.md) a na [logickém serveru SQL Azure](../../sql-database/sql-database-servers.md).

Následujícím postupem vytvořte fond SQL, který obsahuje ukázková data **AdventureWorksDW.**

1. v levém horním rohu portálu Azure vyberte **Vytvořit prostředek.**

   ![vytvoření prostředku na webu Azure Portal](./media/create-data-warehouse-portal/create-a-resource.png)

2. Na **nové** stránce vyberte **Databáze** a v seznamu **Doporučené** vyberte **Azure Synapse Analytics (dříve SQL DW).**

   ![vytvoření prázdného datového skladu](./media/create-data-warehouse-portal/create-a-data-warehouse.png)

3. V **části Základy**zadejte předplatné, skupinu prostředků, název fondu SQL a název serveru:

   | Nastavení | Navrhovaná hodnota | Popis |
   | :------ | :-------------- | :---------- |
   | **Předplatné** | Vaše předplatné | Podrobnosti o vašich předplatných najdete v tématu [Předplatná](https://account.windowsazure.com/Subscriptions). |
   | **Skupina prostředků** | myResourceGroup | Platné názvy skupin prostředků najdete v tématu [Pravidla a omezení pojmenování](/azure/architecture/best-practices/resource-naming). |
   | **Název fondu SQL** | Libovolný globálně jedinečný název (příkladem je *mySampleDataWarehouse)* | Platné názvy databází najdete v tématu [Identifikátory databází](/sql/relational-databases/databases/database-identifiers). Všimněte si, že fond SQL je jeden typ databáze. |
   | **Server** | Libovolný globálně jedinečný název | Vyberte existující server nebo vytvořte nový název serveru, vyberte **Vytvořit nový**. Platné názvy serverů najdete v tématu [Pravidla a omezení pojmenování](/azure/architecture/best-practices/resource-naming). |

   ![vytvoření základních podrobností datového skladu](./media/create-data-warehouse-portal/create-sql-pool-basics.png)

4. V části **Úroveň výkonu**vyberte Vybrat **úroveň výkonu,** chcete-li volitelně změnit konfiguraci pomocí posuvníku.

   ![změna úrovně výkonu datového skladu](./media/create-data-warehouse-portal/create-sql-pool-performance-level.png)  

   Další informace o úrovních výkonu najdete [v tématu Správa výpočetních prostředků v Azure SQL Data Warehouse](sql-data-warehouse-manage-compute-overview.md).

5. Teď, když jste dokončili kartu Základy formuláře Azure Synapse Analytics, vyberte **Zkontrolovat + Vytvořit** a pak **vytvořit** pro vytvoření fondu SQL. Zřizování trvá několik minut.

   ![vyberte zkontrolovat + vytvořit](./media/create-data-warehouse-portal/create-sql-pool-review-create.png)

   ![výběr možnosti Vytvořit](./media/create-data-warehouse-portal/create-sql-pool-create.png)

6. Na panelu nástrojů vyberte **Oznámení,** chcete-li sledovat proces nasazení.

   ![oznámení](./media/create-data-warehouse-portal/notification.png)

## <a name="create-a-server-level-firewall-rule"></a>Vytvoření pravidla brány firewall na úrovni serveru

Služba Azure Synapse vytvoří bránu firewall na úrovni serveru. Tato brána firewall zabraňuje externím aplikacím a nástrojům v připojení k serveru nebo k databázím na serveru. Pokud chcete umožnit připojení, můžete přidat pravidla brány firewall, která povolí připojení z konkrétních IP adres. Postupujte podle těchto pokynů a vytvořte [pravidlo brány firewall na úrovni serveru](../../sql-database/sql-database-firewall-configure.md) pro IP adresu vašeho klienta.

> [!NOTE]
> Azure Synapse komunikuje přes port 1433. Pokud se pokoušíte připojit z podnikové sítě, nemusí být odchozí provoz přes port 1433 bránou firewall vaší sítě povolený. Pokud je to tak, nebudete se moct připojit k serveru služby Azure SQL Database, dokud vaše IT oddělení neotevře port 1433.

1. Po dokončení nasazení vyberte **všechny služby** z levé nabídky. Vyberte **Databáze**, vyberte hvězdičku vedle **Azure Synapse Analytics** a přidejte Azure Synapse Analytics do oblíbených položek.

2. V levé nabídce vyberte **Azure Synapse Analytics** a pak na stránce **Azure Synapse Analytics** vyberte **mySampleDataWarehouse.** Otevře se stránka s přehledem databáze, která zobrazuje plně kvalifikovaný název serveru (například **sqlpoolservername.database.windows.net)** a poskytuje možnosti pro další konfiguraci.

3. Zkopírujte tento plně kvalifikovaný název serveru pro připojení k serveru a jeho databázím v tomto a dalších rychlých spuštěních. Chcete-li otevřít nastavení serveru, vyberte název serveru.

   ![vyhledání názvu serveru](./media/create-data-warehouse-portal/find-server-name.png)

4. Vyberte **možnost Zobrazit nastavení brány firewall**.

   ![nastavení serveru](./media/create-data-warehouse-portal/server-settings.png)

5. Otevře se stránka **Nastavení brány firewall** pro server služby SQL Database.

   ![pravidlo brány firewall serveru](./media/create-data-warehouse-portal/server-firewall-rule.png)

6. Pokud chcete přidat aktuální IP adresu k novému pravidlu brány firewall, vyberte na panelu nástrojů **přidat IP adresu klienta.** Pravidlo brány firewall může otevřít port 1433 pro jednu IP adresu nebo rozsah IP adres.

7. vyberte **Uložit**. Vytvoří se pravidlo brány firewall na úrovni serveru pro vaši aktuální IP adresu, které otevře port 1433 na logickém serveru.

8. vyberte **OK** a zavřete stránku **Nastavení brány firewall.**

Nyní se můžete připojit k serveru SQL a jeho fondům SQL pomocí této adresy IP. Připojení funguje z aplikace SQL Server Management Studio nebo jiného nástroje podle vašeho výběru. Při připojování použijte účet správce serveru, který jste předtím vytvořili.

> [!IMPORTANT]
> Standardně je přístup přes bránu firewall služby SQL Database povolený pro všechny služby Azure. Na této stránce vyberte **OFF** a pak vyberte **Uložit,** chcete-li zakázat bránu firewall pro všechny služby Azure.

## <a name="get-the-fully-qualified-server-name"></a>Získání plně kvalifikovaného názvu serveru

Na webu Azure Portal získejte plně kvalifikovaný název vašeho serveru SQL. Tento plně kvalifikovaný název použijete později při připojování k serveru.

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).

2. V yberte **Azure Synapse Analytics** z nabídky na levé straně a vyberte si na stránce **Azure Synapse Analytics.**

3. V podokně **Základy** na stránce webu Azure Portal pro vaši databázi vyhledejte a potom zkopírujte **Název serveru**. V tomto příkladu je sqlpoolservername.database.windows.net plně kvalifikovaný název.

    ![informace o připojení](./media/create-data-warehouse-portal/find-server-name-copy.png)

## <a name="connect-to-the-server-as-server-admin"></a>Připojení k serveru jako správce serveru

V této části se pomocí aplikace [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) naváže připojení k serveru SQL Azure.

1. Otevřete SQL Server Management Studio.

2. V dialogovém okně **Připojení k serveru** zadejte následující informace:

   | Nastavení | Navrhovaná hodnota | Popis |
   | :------ | :-------------- | :---------- |
   | Typ serveru | Databázový stroj | Tato hodnota se vyžaduje. |
   | Název serveru | Plně kvalifikovaný název serveru | Zde je příklad: **sqlpoolservername.database.windows.net**. |
   | Ověřování | Ověřování SQL Serveru | Ověřování SQL je jediný typ ověřování, který se v tomto kurzu konfiguruje. |
   | Přihlásit | Účet správce serveru | Účet, který jste zadali při vytváření serveru. |
   | Heslo | Heslo pro účet správce serveru | Heslo, které jste zadali při vytváření serveru. |
   ||||

   ![Připojení k serveru](./media/create-data-warehouse-portal/connect-to-server-ssms.png)

3. vyberte **Připojit**. V aplikaci SSMS se otevře okno Průzkumníka objektů. 

4. V Průzkumníku objektů rozbalte **Databáze**. Pak rozbalte **mySampleDatabase** a zobrazte objekty v nové databázi.

   ![databázové objekty](./media/create-data-warehouse-portal/connected-ssms.png) 

## <a name="run-some-queries"></a>Spuštění nějakých dotazů

SQL Data Warehouse jako dotazovací jazyk používá T-SQL. Pokud chcete otevřít okno dotazu a spustit nějaké dotazy T-SQL, použijte následující postup:

1. Vyberte **vpravo mySampleDataWarehouse** a vyberte **Nový dotaz**. Otevře se nové okno dotazu.

2. V okně dotazu zadejte následující příkaz, který zobrazí seznam databází.

    ```sql
    SELECT * FROM sys.databases
    ```

3. vyberte **Spustit**. Výsledky dotazu ukazují dvě databáze: **master** a **mySampleDataWarehouse**.

   ![Dotazování databází](./media/create-data-warehouse-portal/query-databases.png)

4. Pokud se chcete podívat na nějaká data, pomocí následujícího příkazu zobrazte počet zákazníků s příjmením Adams, kteří mají tři děti. Seznam výsledků obsahuje šest zákazníků. 

    ```sql
    SELECT LastName, FirstName FROM dbo.dimCustomer
    WHERE LastName = 'Adams' AND NumberChildrenAtHome = 3;
    ```

   ![Dotaz dbo.dimCustomer](./media/create-data-warehouse-portal/query-customer.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Účtují se vám jednotky datového skladu a data uložená ve fondu SQL. Výpočetní prostředky a prostředky úložiště se účtují odděleně.

- Pokud chcete zachovat data v úložišti, můžete pozastavit výpočetní prostředky, když nepoužíváte fond SQL. Pozastavením výpočetních prostředků se vám bude účtovat jenom úložiště dat. Výpočetní výkon můžete obnovit, kdykoli budete připraveni pracovat s daty.

- Pokud chcete odebrat budoucí poplatky, můžete odstranit fond SQL.

Chcete-li vyčistit prostředky, které již nepotřebujete, postupujte takto.

1. Přihlaste se k [portálu Azure](https://portal.azure.com), vyberte fond SQL.

   ![Vyčištění prostředků](./media/create-data-warehouse-portal/clean-up-resources.png)

2. Chcete-li pozastavit výpočetní výkon, vyberte tlačítko **Pozastavit.** Když je fond SQL pozastaven, zobrazí se tlačítko **Pokračovat.** Chcete-li pokračovat v výpočtu, vyberte **pokračovat**.

3. Pokud chcete fond SQL odebrat, abyste se neúčtovali za výpočetní prostředky nebo úložiště, vyberte **Odstranit**.

4. Chcete-li odebrat vytvořený server SQL, vyberte **sqlpoolservername.database.windows.net** v předchozím obrázku a pak vyberte **Odstranit**. S tímto odstraněním buďte opatrní, protože odstraněním serveru se odstraní také všechny databáze k tomuto serveru přiřazené.

5. Chcete-li skupinu prostředků odebrat, vyberte **položku myResourceGroup**a potom vyberte **odstranit skupinu prostředků**.

## <a name="next-steps"></a>Další kroky

Další informace o načítání dat do fondu SQL, pokračujte [načíst data do](load-data-from-azure-blob-storage-using-polybase.md) článku fondu SQL. 
