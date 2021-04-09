---
title: Předpoklady pro Azure Database Migration Service
description: Seznamte se s přehledem požadavků na použití Azure Database Migration Service k provádění migrace databáze.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: 55d5594229bccb5fcb6a406e671ed104c1e12378
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101094043"
---
# <a name="overview-of-prerequisites-for-using-the-azure-database-migration-service"></a>Přehled požadavků pro používání služby Azure Database Migration Service

K zajištění plynulého Azure Database Migration Service při provádění migrace databáze je nutné provést několik požadavků. Některé požadavky platí ve všech scénářích (páry zdroj-cíl) podporované službou, zatímco jiné požadavky jsou jedinečné pro konkrétní scénář.

Požadavky související s používáním Azure Database Migration Service jsou uvedeny v následujících oddílech.

## <a name="prerequisites-common-across-migration-scenarios"></a>Požadavky společné napříč různými scénáři migrace

Mezi všechny podporované scénáře migrace Azure Database Migration Service společné předpoklady, které zahrnují nutnost:

* Vytvořte Microsoft Azure Virtual Network pro Azure Database Migration Service pomocí modelu nasazení Azure Resource Manager, který umožňuje připojení typu Site-to-site k místním zdrojovým serverům pomocí [ExpressRoute](../expressroute/expressroute-introduction.md) nebo [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).
* Zajistěte, aby pravidla skupiny zabezpečení sítě (NSG) neblokovala odchozí port 443 z ServiceTag pro ServiceBus, úložiště a AzureMonitor. Další podrobnosti o filtrování provozu NSG virtuální sítě najdete v článku [filtrování provozu sítě pomocí skupin zabezpečení sítě](../virtual-network/virtual-network-vnet-plan-design-arm.md).
* Pokud používáte zařízení brány firewall před zdrojovými databázemi, budete možná muset přidat pravidla firewallu, která Azure Database Migration Service umožní přístup ke zdrojovým databázím pro migraci.
* Nakonfigurujte bránu [Windows Firewall pro přístup k databázovému stroji](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Povolte protokol TCP/IP, který se ve výchozím nastavení zakáže během instalace SQL Serveru Express, a to podle pokynů v článku [Povolení nebo zakázání síťového protokolu serveru](/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).

    > [!IMPORTANT]
    > Vytvoření instance Azure Database Migration Service vyžaduje přístup k nastavení virtuální sítě, která se obvykle nenachází ve stejné skupině prostředků. V důsledku toho uživatel vytvářející instanci DMS vyžaduje oprávnění na úrovni předplatného. Chcete-li vytvořit požadované role, které můžete přiřadit podle potřeby, spusťte následující skript:
    >
    > ```
    >
    > $readerActions = `
    > "Microsoft.Network/networkInterfaces/ipConfigurations/read", `
    > "Microsoft.DataMigration/*/read", `
    > "Microsoft.Resources/subscriptions/resourceGroups/read"
    >
    > $writerActions = `
    > "Microsoft.DataMigration/services/*/write", `
    > "Microsoft.DataMigration/services/*/delete", `
    > "Microsoft.DataMigration/services/*/action", `
    > "Microsoft.Network/virtualNetworks/subnets/join/action", `
    > "Microsoft.Network/virtualNetworks/write", `
    > "Microsoft.Network/virtualNetworks/read", `
    > "Microsoft.Resources/deployments/validate/action", `
    > "Microsoft.Resources/deployments/*/read", `
    > "Microsoft.Resources/deployments/*/write"
    >
    > $writerActions += $readerActions
    >
    > # TODO: replace with actual subscription IDs
    > $subScopes = ,"/subscriptions/00000000-0000-0000-0000-000000000000/","/subscriptions/11111111-1111-1111-1111-111111111111/"
    >
    > function New-DmsReaderRole() {
    > $aRole = [Microsoft.Azure.Commands.Resources.Models.Authorization.PSRoleDefinition]::new()
    > $aRole.Name = "Azure Database Migration Reader"
    > $aRole.Description = "Lets you perform read only actions on DMS service/project/tasks."
    > $aRole.IsCustom = $true
    > $aRole.Actions = $readerActions
    > $aRole.NotActions = @()
    >
    > $aRole.AssignableScopes = $subScopes
    > #Create the role
    > New-AzRoleDefinition -Role $aRole
    > }
    >
    > function New-DmsContributorRole() {
    > $aRole = [Microsoft.Azure.Commands.Resources.Models.Authorization.PSRoleDefinition]::new()
    > $aRole.Name = "Azure Database Migration Contributor"
    > $aRole.Description = "Lets you perform CRUD actions on DMS service/project/tasks."
    > $aRole.IsCustom = $true
    > $aRole.Actions = $writerActions
    > $aRole.NotActions = @()
    >
    >   $aRole.AssignableScopes = $subScopes
    > #Create the role
    > New-AzRoleDefinition -Role $aRole
    > }
    > 
    > function Update-DmsReaderRole() {
    > $aRole = Get-AzRoleDefinition "Azure Database Migration Reader"
    > $aRole.Actions = $readerActions
    > $aRole.NotActions = @()
    > Set-AzRoleDefinition -Role $aRole
    > }
    >
    > function Update-DmsConributorRole() {
    > $aRole = Get-AzRoleDefinition "Azure Database Migration Contributor"
    > $aRole.Actions = $writerActions
    > $aRole.NotActions = @()
    > Set-AzRoleDefinition -Role $aRole
    > }
    >
    > # Invoke above functions
    > New-DmsReaderRole
    > New-DmsContributorRole
    > Update-DmsReaderRole
    > Update-DmsConributorRole
    > ```

## <a name="prerequisites-for-migrating-sql-server-to-azure-sql-database"></a>Předpoklady pro migraci SQL Server do Azure SQL Database

Kromě Azure Database Migration Service nezbytných součástí, které jsou společné pro všechny scénáře migrace, existují také požadavky, které platí konkrétně pro jeden nebo jiný scénář.

Při použití Azure Database Migration Service k provádění SQL Server k Azure SQL Database migrace, kromě požadavků, které jsou společné pro všechny scénáře migrace, nezapomeňte vyřešit následující další požadavky:

* Vytvořte instanci instance Azure SQL Database, kterou provedete podle podrobných informací uvedených v článku [Vytvoření databáze v Azure SQL Database v Azure Portal](../azure-sql/database/single-database-create-quickstart.md).
* Stáhněte a nainstalujte nástroj [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) verze 3.3 nebo novější.
* Otevřete bránu Windows Firewall a povolte službě Azure Database Migration Service přístup ke zdrojovému SQL Serveru, který ve výchozím nastavení probíhá přes port TCP 1433.
* Pokud provozujete několik pojmenovaných instancí SQL Serveru s využitím dynamických portů, možná budete chtít povolit službu SQL Browser a přístup k portu UDP 1434 přes vaše brány firewall, aby se služba Azure Database Migration Service mohla připojit k pojmenované instanci na vašem zdrojovém serveru.
* Vytvořením [pravidla brány firewall](../azure-sql/database/firewall-configure.md) na úrovni serveru pro SQL Database umožníte Azure Database Migration Service přístup k cílovým databázím. Zadejte rozsah podsítě virtuální sítě, která se používá pro Azure Database Migration Service.
* Ujistěte se, že přihlašovací údaje použité pro připojení ke zdrojové instanci SQL Serveru mají oprávnění [CONTROL SERVER](/sql/t-sql/statements/grant-server-permissions-transact-sql).
* Ověřte, zda pověření používaná k připojení k cílové databázi mají oprávnění řídicí databáze v cílové databázi.

   > [!NOTE]
   > Úplný seznam požadavků nutných k použití Azure Database Migration Service k provedení migrace z SQL Server na Azure SQL Database najdete v kurzu [migrace SQL Server na Azure SQL Database](./tutorial-sql-server-to-azure-sql.md).
   >

## <a name="prerequisites-for-migrating-sql-server-to-azure-sql-managed-instance"></a>Předpoklady pro migraci SQL Server do spravované instance Azure SQL

* Vytvořte spravovanou instanci SQL podle podrobných pokynů v článku [Vytvoření spravované instance Azure SQL v Azure Portal](../azure-sql/managed-instance/instance-create-quickstart.md).
* Otevřete brány firewall, aby se povolil provoz SMB na portu 445 pro Azure Database Migration Service IP adresa nebo rozsah podsítě.
* Otevřete bránu Windows Firewall a povolte službě Azure Database Migration Service přístup ke zdrojovému SQL Serveru, který ve výchozím nastavení probíhá přes port TCP 1433.
* Pokud provozujete několik pojmenovaných instancí SQL Serveru s využitím dynamických portů, možná budete chtít povolit službu SQL Browser a přístup k portu UDP 1434 přes vaše brány firewall, aby se služba Azure Database Migration Service mohla připojit k pojmenované instanci na vašem zdrojovém serveru.
* Ujistěte se, že přihlašovací jména použitá pro připojení ke zdrojovému SQL Serveru a cílové spravované instanci patří členům role serveru sysadmin.
* Vytvořte sdílenou síťovou složku, kterou může Azure Database Migration Service použít k zálohování zdrojové databáze.
* Ujistěte se, že účet služby, ve kterém je spuštěná zdrojová instance SQL Serveru, má oprávnění k zápisu do sdílené síťové složky, kterou jste vytvořili, a že účet počítače pro zdrojový server má k této sdílené složce přístup pro čtení i zápis.
* Poznamenejte si uživatele Windows (a jeho heslo) s oprávněním Úplné řízení ke sdílené síťové složce, kterou jste vytvořili dříve. Azure Database Migration Service zosobňuje přihlašovací údaje uživatele k nahrání záložních souborů do kontejneru Azure Storage pro operaci obnovení.
* Vytvořte kontejner objektů BLOB a načtěte jeho identifikátor URI SAS pomocí kroků v článku [Správa prostředků Azure Blob Storage pomocí Průzkumník služby Storage](../vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container). Při vytváření identifikátoru URI SAS nezapomeňte vybrat všechna oprávnění (čtení, zápis, odstranění, seznam) v okně zásad.

   > [!NOTE]
   > Úplný seznam požadavků nutných k použití Azure Database Migration Service k provedení migrace z SQL Server na spravovanou instanci SQL najdete v kurzu [migrace SQL Server do spravované instance SQL](./tutorial-sql-server-to-managed-instance.md).

## <a name="next-steps"></a>Další kroky

Přehled Azure Database Migration Service a regionální dostupnosti najdete v článku [co je to Azure Database Migration Service](dms-overview.md).