---
title: 'Rychlý Start: vytvoření a dotazování vyhrazeného fondu SQL (dřív SQL DW) (Azure Portal)'
description: Vytvoření a dotazování vyhrazeného fondu SQL (dřív SQL DW) pomocí Azure Portal
services: synapse-analytics
author: pimorano
ms.author: pimorano
manager: craigg
ms.reviewer: igorstan
ms.date: 05/28/2019
ms.topic: quickstart
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.custom:
- seo-lt-2019
- azure-synapse
- mode-portal
ms.openlocfilehash: 99530b1949f03867a5b755208191341556ba8083
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535830"
---
# <a name="quickstart-create-and-query-a-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics-using-the-azure-portal"></a>Rychlý Start: vytvoření a dotazování vyhrazeného fondu SQL (dřív SQL DW) ve službě Azure synapse Analytics pomocí Azure Portal

K rychlému vytvoření a dotazování vyhrazeného fondu SQL (dřív SQL DW) ve službě Azure synapse Analytics pomocí Azure Portal.

## <a name="prerequisites"></a>Požadavky

1. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

   > [!NOTE]
   > Vytvoření vyhrazeného fondu SQL (dříve SQL DW) ve službě Azure synapse může vést k nové Fakturovatelné službě. Další informace najdete v tématu [ceny služby Azure synapse Analytics](https://azure.microsoft.com/pricing/details/synapse-analytics/).

2. Stáhněte a nainstalujte nejnovější verzi aplikace [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) (SSMS). Poznámka: SSMS je k dispozici pouze na platformách založených na systému Windows, další informace najdete v [úplném seznamu podporovaných platforem](/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15&preserve-view=true#supported-operating-systems-ssms-185t).

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se na [Azure Portal](https://portal.azure.com/).

## <a name="create-a-sql-pool"></a>Vytvoření fondu SQL

Datové sklady se vytvářejí pomocí vyhrazeného fondu SQL (dřív SQL DW) ve službě Azure synapse Analytics. Vyhrazený fond SQL (dřív SQL DW) se vytvoří s definovanou sadou [výpočetních prostředků](memory-concurrency-limits.md). Databáze se vytvoří v rámci [skupiny prostředků Azure](../../azure-resource-manager/management/overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) a na [logickém SQL serveru](../../azure-sql/database/logical-servers.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

Pomocí těchto kroků můžete vytvořit vyhrazený fond SQL (dřív SQL DW), který obsahuje ukázková data **AdventureWorksDW** .

1. V levém horním rohu webu Azure Portal vyberte **Vytvořit prostředek**.

   ![Vytvoření prostředku v Azure Portal](./media/create-data-warehouse-portal/create-a-resource.png)

2. V panelu hledání typ "vyhrazený fond SQL" vyberte vyhrazený fond SQL (dřív SQL DW). Na stránce, která se otevře, vyberte **vytvořit** .

   ![vytvoření prázdného datového skladu](./media/create-data-warehouse-portal/create-a-data-warehouse.png)

3. V oblasti **základy** zadejte své předplatné, skupinu prostředků, vyhrazený fond SQL (dříve SQL DW) a název serveru:

   | Nastavení | Navrhovaná hodnota | Popis |
   | :------ | :-------------- | :---------- |
   | **Předplatné** | Vaše předplatné | Podrobnosti o vašich předplatných najdete v tématu [Předplatná](https://account.windowsazure.com/Subscriptions). |
   | **Skupina prostředků** | myResourceGroup | Platné názvy skupin prostředků najdete v tématu [Pravidla a omezení pojmenování](/azure/architecture/best-practices/resource-naming?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). |
   | **Název fondu SQL** | Jakýkoli globálně jedinečný název (příklad je *mySampleDataWarehouse*) | Platné názvy databází najdete v tématu [Identifikátory databází](/sql/relational-databases/databases/database-identifiers?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).  |
   | **Server** | Libovolný globálně jedinečný název | Vyberte existující server nebo vytvořte nový název serveru, vyberte **vytvořit novou**. Platné názvy serverů najdete v tématu [Pravidla a omezení pojmenování](/azure/architecture/best-practices/resource-naming?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). |

   ![vytvoření základních podrobností datového skladu](./media/create-data-warehouse-portal/create-sql-pool-basics.png)

4. V části **úroveň výkonu** vyberte **Vybrat úroveň výkonu** a volitelně změňte konfiguraci pomocí posuvníku.

   ![změnit úroveň výkonu datového skladu](./media/create-data-warehouse-portal/create-sql-pool-performance-level.png)  

   Další informace o úrovních výkonu najdete v tématu [Správa výpočetních prostředků ve službě Azure synapse Analytics](sql-data-warehouse-manage-compute-overview.md).

5. Vyberte **Další nastavení**, v části **použít existující data** zvolte možnost **Ukázka** , aby se AdventureWorksDW vytvořil jako ukázková databáze.

    ![Vyberte možnost použít existující data.](./media/create-data-warehouse-portal/create-sql-pool-additional-1.png)

6. Teď, když jste dokončili kartu základy formuláře Azure synapse Analytics, vyberte **zkontrolovat + vytvořit** a pak **vytvořit** a vytvořte fond SQL. Zřizování trvá několik minut.

   ![vybrat kontrolu + vytvořit](./media/create-data-warehouse-portal/create-sql-pool-review-create.png)

   ![výběr možnosti Vytvořit](./media/create-data-warehouse-portal/create-sql-pool-create.png)

7. Na panelu nástrojů vyberte **Oznámení**, abyste mohli sledovat proces nasazení.

   ![Snímek obrazovky s probíhajícím nasazením zobrazuje oznámení.](./media/create-data-warehouse-portal/notification.png)

## <a name="create-a-server-level-firewall-rule"></a>Vytvoření pravidla brány firewall na úrovni serveru

Služba Azure synapse vytvoří bránu firewall na úrovni serveru. Tato brána firewall zabraňuje externím aplikacím a nástrojům v připojení k serveru nebo jakékoli databázi na serveru. Pokud chcete umožnit připojení, můžete přidat pravidla brány firewall, která povolí připojení z konkrétních IP adres. Postupujte podle těchto pokynů a vytvořte [pravidlo brány firewall na úrovni serveru](../../azure-sql/database/firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) pro IP adresu vašeho klienta.

> [!NOTE]
> Azure synapse komunikuje přes port 1433. Pokud se pokoušíte připojit z podnikové sítě, nemusí být odchozí provoz přes port 1433 bránou firewall vaší sítě povolený. Pokud ano, nemůžete se připojit k serveru, dokud vaše IT oddělení neotevře port 1433.

1. Po dokončení nasazení vyberte v nabídce na levé straně **všechny služby** . Vyberte **databáze**, vyberte hvězdičku vedle položky **Azure synapse Analytics** a přidejte Azure synapse Analytics k oblíbeným položkám.

2. V nabídce na levé straně vyberte **Azure synapse Analytics** a pak na stránce **Azure synapse Analytics** vyberte **mySampleDataWarehouse** . Otevře se stránka s přehledem pro vaši databázi, na které se zobrazí plně kvalifikovaný název serveru (například **sqlpoolservername.Database.Windows.NET**), který poskytuje možnosti pro další konfiguraci.

3. Zkopírujte tento plně kvalifikovaný název serveru, abyste ho mohli použít pro připojení k serveru a jeho databázím v tomto a dalším rychlém startu. Pokud chcete otevřít nastavení serveru, vyberte název serveru.

   ![vyhledání názvu serveru](./media/create-data-warehouse-portal/find-server-name.png)

4. Vyberte **Zobrazit nastavení brány firewall**.

   ![nastavení serveru](./media/create-data-warehouse-portal/server-settings.png)

5. Otevře se stránka **nastavení brány firewall** pro server.

   ![pravidlo brány firewall serveru](./media/create-data-warehouse-portal/server-firewall-rule.png)

6. Pokud chcete přidat aktuální IP adresu k novému pravidlu brány firewall, vyberte **Přidat IP adresu klienta** na panelu nástrojů. Pravidlo brány firewall může otevřít port 1433 pro jednu IP adresu nebo rozsah IP adres.

7. Vyberte **Uložit**. Vytvoří se pravidlo brány firewall na úrovni serveru pro vaši aktuální IP adresu pro otevření portu 1433 na serveru.

8. Vyberte **OK** a pak zavřete stránku **nastavení brány firewall** .

Nyní se můžete připojit k serveru a jeho fondům SQL pomocí této IP adresy. Připojení funguje z aplikace SQL Server Management Studio nebo jiného nástroje podle vašeho výběru. Při připojování použijte účet správce serveru, který jste předtím vytvořili.

> [!IMPORTANT]
> Standardně je přístup přes bránu firewall služby SQL Database povolený pro všechny služby Azure. na této stránce vyberte **vypnuto** a pak výběrem **Uložit** zakažte bránu firewall pro všechny služby Azure.

## <a name="get-the-fully-qualified-server-name"></a>Získání plně kvalifikovaného názvu serveru

Získejte plně kvalifikovaný název serveru pro váš server v Azure Portal. Tento plně kvalifikovaný název použijete později při připojování k serveru.

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).

2. V nabídce na levé straně vyberte **Azure synapse Analytics** a na stránce **Azure synapse Analytics** vyberte svůj.

3. V podokně **Základy** na stránce webu Azure Portal pro vaši databázi vyhledejte a potom zkopírujte **Název serveru**. V tomto příkladu je plně kvalifikovaný název sqlpoolservername.database.windows.net.

    ![informace o připojení](./media/create-data-warehouse-portal/find-server-name.png)

## <a name="connect-to-the-server-as-server-admin"></a>Připojení k serveru jako správce serveru

V této části se k navázání připojení k serveru používá [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) (SSMS).

1. Otevřete sadu SQL Server Management Studio.

2. V dialogovém okně **Připojení k serveru** zadejte následující informace:

   | Nastavení | Navrhovaná hodnota | Popis |
   | :------ | :-------------- | :---------- |
   | Typ serveru | Databázový stroj | Tato hodnota se vyžaduje. |
   | Název serveru | Plně kvalifikovaný název serveru | Tady je příklad: **sqlpoolservername.Database.Windows.NET**. |
   | Ověřování | Ověřování SQL Serveru | Ověřování SQL je jediný typ ověřování, který se v tomto kurzu konfiguruje. |
   | Přihlásit | Účet správce serveru | Účet, který jste zadali při vytváření serveru. |
   | Heslo | Heslo pro účet správce serveru | Heslo, které jste zadali při vytváření serveru. |
   ||||

   ![Připojení k serveru](./media/create-data-warehouse-portal/connect-to-server-ssms.png)

3. Vyberte **připojit**. V aplikaci SSMS se otevře okno Průzkumníka objektů.

4. V Průzkumníku objektů rozbalte **Databáze**. Pak rozbalte **mySampleDatabase** a zobrazte objekty v nové databázi.

   ![databázové objekty](./media/create-data-warehouse-portal/connected-ssms.png)

## <a name="run-some-queries"></a>Spuštění nějakých dotazů

Nedoporučujeme spouštět velké dotazy, když se přihlásíte jako správce serveru, protože používá [třídu omezeného prostředku](resource-classes-for-workload-management.md). Místo toho nakonfigurujte [izolaci úloh](./quickstart-configure-workload-isolation-tsql.md) , jak [je znázorněno v kurzech](./load-data-wideworldimportersdw.md#create-a-user-for-loading-data).

Azure synapse Analytics používá jako dotazovací jazyk T-SQL. Pokud chcete otevřít okno dotazu a spustit nějaké dotazy T-SQL, použijte následující postup:

1. Pravým tlačítkem vyberte **mySampleDataWarehouse** a vyberte **Nový dotaz**. Otevře se nové okno dotazu.

2. V okně dotazu zadejte následující příkaz, který zobrazí seznam databází.

    ```sql
    SELECT * FROM sys.databases
    ```

3. Vyberte **provést**. Výsledky dotazu ukazují dvě databáze: **master** a **mySampleDataWarehouse**.

   ![Dotazování databází](./media/create-data-warehouse-portal/query-databases.png)

4. Pokud se chcete podívat na nějaká data, pomocí následujícího příkazu zobrazte počet zákazníků s příjmením Adams, kteří mají tři děti. Seznam výsledků obsahuje šest zákazníků.

    ```sql
    SELECT LastName, FirstName FROM dbo.dimCustomer
    WHERE LastName = 'Adams' AND NumberChildrenAtHome = 3;
    ```

   ![Dotaz dbo.dimCustomer](./media/create-data-warehouse-portal/query-customer.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Účtují se vám poplatky za jednotky datového skladu a data uložená ve vašem vyhrazeném fondu SQL (dřív SQL DW). Výpočetní prostředky a prostředky úložiště se účtují odděleně.

- Pokud chcete uchovávat data v úložišti, můžete pozastavit výpočetní prostředky, když nepoužíváte vyhrazený fond SQL (dřív SQL DW). Když pozastavíte výpočetní prostředky, bude se vám účtovat jenom úložiště dat. Můžete pokračovat v výpočtůch, kdykoli budete připraveni na práci s daty.

- Pokud chcete odebrat budoucí poplatky, můžete odstranit vyhrazený fond SQL (dřív SQL DW).

Pomocí těchto kroků vyčistěte prostředky, které už nepotřebujete.

1. Přihlaste se k [Azure Portal](https://portal.azure.com), vyberte vyhrazený fond SQL (dřív SQL DW).

   ![Vyčištění prostředků](./media/create-data-warehouse-portal/clean-up-resources.png)

2. Pokud chcete pozastavit výpočetní prostředky, vyberte tlačítko **pozastavit** . Pokud je vyhrazený fond SQL (dřív SQL DW) pozastavený, zobrazí se tlačítko pro **obnovení** . Chcete-li obnovit výpočetní výkon, vyberte možnost **pokračovat**.

3. Pokud chcete odebrat vyhrazený fond SQL (dřív SQL DW), takže se vám neúčtují výpočetní výkon nebo úložiště, vyberte **Odstranit**.

4. Pokud chcete odebrat server, který jste vytvořili, vyberte na předchozím obrázku **sqlpoolservername.Database.Windows.NET** a pak vyberte **Odstranit**. S tímto odstraněním buďte opatrní, protože odstraněním serveru se odstraní také všechny databáze k tomuto serveru přiřazené.

5. Pokud chcete odebrat skupinu prostředků, vyberte **myResourceGroup** a pak vyberte **Odstranit skupinu prostředků**.

Chcete optimalizovat a uložit své útraty do cloudu?

[!INCLUDE [cost-management-horizontal](../../../includes/cost-management-horizontal.md)]

## <a name="next-steps"></a>Další kroky

Pokud se chcete dozvědět víc o načítání dat do vyhrazeného fondu SQL (dřív SQL DW), přejděte k článku [načtení dat do vyhrazeného fondu SQL](load-data-from-azure-blob-storage-using-copy.md) .
