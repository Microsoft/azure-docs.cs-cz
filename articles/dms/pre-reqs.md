---
title: Požadavky pro službu migrace databáze Azure
description: Přečtěte si o přehledu předpokladů pro použití služby Migrace databáze Azure k provádění migrací databáze.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/25/2020
ms.openlocfilehash: 89cb63630e3dbe953ed3f4fd8796d01ba0d36067
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77651487"
---
# <a name="overview-of-prerequisites-for-using-the-azure-database-migration-service"></a>Přehled požadavků pro používání služby Azure Database Migration Service

Existuje několik předpokladů, které jsou nutné k zajištění azure database migration service běží hladce při provádění migrace databáze. Některé požadavky platí pro všechny scénáře (páry cíl zdroj) podporované službou, zatímco jiné požadavky jsou jedinečné pro konkrétní scénář.

Požadavky spojené s používáním služby migrace databáze Azure jsou uvedeny v následujících částech.

## <a name="prerequisites-common-across-migration-scenarios"></a>Požadavky běžné napříč scénáři migrace

Mezi požadavky služby Azure Database Migration Service, které jsou společné ve všech podporovaných scénářích migrace, patří:

* Vytvořte službu Migrace databáze Microsoft Azure pro Azure pomocí modelu nasazení Azure Resource Manageru, který poskytuje připojení k místním zdrojovým serverům site-to-site pomocí [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) nebo [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
* Ujistěte se, že pravidla skupiny zabezpečení sítě virtuální sítě (NSG) neblokují následující komunikační porty 443, 53, 9354, 445, 12000. Další podrobnosti o filtrování přenosů skupin nsg ve virtuální síti naleznete v článku [Filtrování síťového provozu se skupinami zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
* Při použití zařízení brány firewall před zdrojovou databází, budete muset přidat pravidla brány firewall povolit Azure Database Migration Service pro přístup ke zdrojové databáze pro migraci.
* Nakonfigurujte bránu [Windows Firewall pro přístup k databázovému stroji](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Povolte protokol TCP/IP, který se ve výchozím nastavení zakáže během instalace SQL Serveru Express, a to podle pokynů v článku [Povolení nebo zakázání síťového protokolu serveru](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).

    > [!IMPORTANT]
    > Vytvoření instance služby Migrace databáze Azure vyžaduje přístup k nastavení virtuální sítě, které obvykle nejsou ve stejné skupině prostředků. V důsledku toho uživatel, který vytváří instanci Služby DMS, vyžaduje oprávnění na úrovni předplatného. Chcete-li vytvořit požadované role, které můžete přiřadit podle potřeby, spusťte následující skript:
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

## <a name="prerequisites-for-migrating-sql-server-to-azure-sql-database"></a>Požadavky pro migraci serveru SQL Server do databáze Azure SQL

Kromě předpokladů služby Azure Database Migration Service, které jsou společné pro všechny scénáře migrace, existují také požadavky, které platí konkrétně pro jeden scénář nebo jiný.

Při použití služby migrace databáze Azure k provedení migrace SQL Serveru do databáze Azure SQL, kromě předpokladů, které jsou společné pro všechny scénáře migrace, nezapomeňte řešit následující další předpoklady:

* Vytvořte instanci služby Azure SQL Database podle podrobných pokynů v článku [Vytvoření databáze Azure SQL na webu Azure Portal](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal).
* Stáhněte a nainstalujte nástroj [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) verze 3.3 nebo novější.
* Otevřete bránu Windows Firewall a povolte službě Azure Database Migration Service přístup ke zdrojovému SQL Serveru, který ve výchozím nastavení probíhá přes port TCP 1433.
* Pokud provozujete několik pojmenovaných instancí SQL Serveru s využitím dynamických portů, možná budete chtít povolit službu SQL Browser a přístup k portu UDP 1434 přes vaše brány firewall, aby se služba Azure Database Migration Service mohla připojit k pojmenované instanci na vašem zdrojovém serveru.
* Vytvořte pro server služby Azure SQL Database [pravidlo brány firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) na úrovni serveru, které službě Azure Database Migration Service povolí přístup k cílovým databázím. Zadejte rozsah podsítí virtuální sítě používané pro službu migrace databáze Azure.
* Ujistěte se, že přihlašovací údaje použité pro připojení ke zdrojové instanci SQL Serveru mají oprávnění [CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql).
* Ujistěte se, že přihlašovací údaje použité pro připojení k cílové instanci služby Azure SQL Database mají oprávnění CONTROL DATABASE k cílovým databázím Azure SQL.

   > [!NOTE]
   > Úplný seznam požadavků, které jsou nutné k použití služby migrace do databáze Azure k provádění migrací z SQL Serveru do azure sql database, najdete v kurzu [Migrace SERVERU SQL server do azure sql database](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-azure-sql).
   >

## <a name="prerequisites-for-migrating-sql-server-to-an-azure-sql-database-managed-instance"></a>Požadavky pro migraci SQL Serveru do spravované instance Azure SQL Database

* Vytvořte spravovanou instanci Azure SQL Database podle podrobností v článku [Vytvoření spravované instance Azure SQL Database na webu Azure Portal](https://aka.ms/sqldbmi).
* Otevřete brány firewall, abyste povolili provoz SMB na portu 445 pro IP adresu nebo rozsah podsítí služby Azure Database Migration Service.
* Otevřete bránu Windows Firewall a povolte službě Azure Database Migration Service přístup ke zdrojovému SQL Serveru, který ve výchozím nastavení probíhá přes port TCP 1433.
* Pokud provozujete několik pojmenovaných instancí SQL Serveru s využitím dynamických portů, možná budete chtít povolit službu SQL Browser a přístup k portu UDP 1434 přes vaše brány firewall, aby se služba Azure Database Migration Service mohla připojit k pojmenované instanci na vašem zdrojovém serveru.
* Ujistěte se, že přihlašovací jména použitá pro připojení ke zdrojovému SQL Serveru a cílové spravované instanci patří členům role serveru sysadmin.
* Vytvořte sdílenou síťovou složku, kterou může Azure Database Migration Service použít k zálohování zdrojové databáze.
* Ujistěte se, že účet služby, ve kterém je spuštěná zdrojová instance SQL Serveru, má oprávnění k zápisu do sdílené síťové složky, kterou jste vytvořili, a že účet počítače pro zdrojový server má k této sdílené složce přístup pro čtení i zápis.
* Poznamenejte si uživatele Windows (a jeho heslo) s oprávněním Úplné řízení ke sdílené síťové složce, kterou jste vytvořili dříve. Služba migrace databáze Azure zosobňuje přihlašovací údaje uživatele k nahrání záložních souborů do kontejneru úložiště Azure pro operaci obnovení.
* Vytvořte kontejner objektů blob a načtěte jeho identifikátor URI SAS pomocí kroků v článku [Správa prostředků úložiště objektů blob Azure pomocí Průzkumníka úložiště](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container). Nezapomeňte vybrat všechna oprávnění (Čtení, Zápis, Odstranit, Seznam) v okně zásad při vytváření identifikátoru URI SAS.

   > [!NOTE]
   > Úplný seznam požadavků potřebných k použití služby migrace do databáze Azure k provádění migrací z SQL Serveru do spravované instance Azure SQL Database Se podívejte na kurz [Migrace SERVERU SQL serveru do spravované instance Azure SQL Database .](https://aka.ms/migratetomiusingdms)

## <a name="next-steps"></a>Další kroky

Přehled služby migrace databáze Azure a místní dostupnosti najdete v článku [Co je služba migrace databáze Azure](dms-overview.md).
