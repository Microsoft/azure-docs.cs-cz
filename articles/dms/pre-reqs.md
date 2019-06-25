---
title: Přehled požadavků na používání Azure Database Migration Service | Dokumentace Microsoftu
description: Další informace o přehled požadavků na používání Azure Database Migration Service k provedení migrace databází.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 05/29/2019
ms.openlocfilehash: 4e21014f7b4ed86846a100ed9a2b1cd4b0400974
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66304259"
---
# <a name="overview-of-prerequisites-for-using-the-azure-database-migration-service"></a>Přehled požadavků na používání Azure Database Migration Service

Existuje několik předpokladů, které jsou potřeba k tomu, že služba Azure Database Migration Service běží bez problémů při provádění migrace databází. Některé z požadovaných součástí ve všech scénářích (párů zdroj cíl) podporované službou, zatímco jiné požadavky, které jsou jedinečné pro konkrétní scénář uplatnit.

Požadavky související s využitím Azure Database Migration Service jsou uvedené v následujících částech.

## <a name="prerequisites-common-across-migration-scenarios"></a>Požadavky, které jsou společné napříč scénáři migrace

Požadavky na Azure Database Migration Service, které jsou společné pro všechny podporované scénáře migrace patří:

* Vytvoření služby Azure Virtual Network (VNet) pro Azure Database Migration Service pomocí modelu nasazení Azure Resource Manageru, který poskytuje připojení site-to-site k vašich zdrojových serverů s místními pomocí [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) nebo [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
* Ujistěte se, že není pravidla skupiny zabezpečení sítě virtuální sítě (NSG) blokovat následující komunikační porty 443, 53, 9354, 445, 12000. Další podrobnosti o filtrování provozu skupiny zabezpečení sítě Azure VNet najdete v článku [filtrování provozu sítě s použitím skupin zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
* Při použití zařízení brány firewall u zdrojových databází, budete muset přidat pravidla firewallu povolující Azure Database Migration Service přístup k zdrojových databází pro migraci.
* Nakonfigurujte bránu [Windows Firewall pro přístup k databázovému stroji](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Povolte protokol TCP/IP, který se ve výchozím nastavení zakáže během instalace SQL Serveru Express, a to podle pokynů v článku [Povolení nebo zakázání síťového protokolu serveru](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).

    > [!IMPORTANT]
    > Vytvoření instance služby Azure Database Migration Service vyžaduje přístup k nastavení virtuální sítě, které obvykle nejsou ve stejné skupině prostředků. V důsledku toho uživatel vytvářející instance DMS vyžaduje oprávnění na úrovni předplatného. Chcete-li vytvořit požadované role, které můžete přiřadit podle potřeby, spusťte následující skript:
    >
    > ```
    >
    > $readerActions = `
    > "Microsoft.DataMigration/services/*/read", `
    > "Microsoft.Network/networkInterfaces/ipConfigurations/read"
    >
    > $writerActions = `
    > "Microsoft.DataMigration/services/*/write", `
    > "Microsoft.DataMigration/services/*/delete", `
    > "Microsoft.DataMigration/services/*/action"
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

## <a name="prerequisites-for-migrating-sql-server-to-azure-sql-database"></a>Požadavky pro migraci SQL serveru do služby Azure SQL Database

Kromě požadavků na služby Azure Database Migration Service, které jsou společné pro všechny scénáře migrace existují také požadavky, které platí konkrétně pro jeden scénář nebo jiného.

Při použití Azure Database Migration Service můžete provést SQL serveru do migrace Azure SQL Database, kromě požadavků, které jsou společné pro všechny scénáře migrace, je potřeba vyřešit následující další požadavky:

* Vytvoření instance Azure SQL Database instance, což uděláte pomocí následujících podrobností v článku C[vytvořit databázi Azure SQL na webu Azure Portal](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal).
* Stáhněte a nainstalujte nástroj [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) verze 3.3 nebo novější.
* Otevřete bránu Windows Firewall a povolte službě Azure Database Migration Service přístup ke zdrojovému SQL Serveru, který ve výchozím nastavení probíhá přes port TCP 1433.
* Pokud provozujete několik pojmenovaných instancí SQL Serveru s využitím dynamických portů, možná budete chtít povolit službu SQL Browser a přístup k portu UDP 1434 přes vaše brány firewall, aby se služba Azure Database Migration Service mohla připojit k pojmenované instanci na vašem zdrojovém serveru.
* Vytvořte pro server služby Azure SQL Database [pravidlo brány firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) na úrovni serveru, které službě Azure Database Migration Service povolí přístup k cílovým databázím. Zadejte rozsah podsítí virtuální sítě použité pro službu Azure Database Migration Service.
* Ujistěte se, že přihlašovací údaje použité pro připojení ke zdrojové instanci SQL Serveru mají oprávnění [CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql).
* Ujistěte se, že přihlašovací údaje použité pro připojení k cílové instanci služby Azure SQL Database mají oprávnění CONTROL DATABASE k cílovým databázím Azure SQL.

   > [!NOTE]
   > Úplný seznam požadovaných součástí pro Azure Database Migration Service můžete provést migraci z SQL serveru do služby Azure SQL Database, najdete v kurzu [migrace systému SQL Server do služby Azure SQL Database](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-azure-sql).
   > 

## <a name="prerequisites-for-migrating-sql-server-to-an-azure-sql-database-managed-instance"></a>Požadavky pro migraci SQL serveru do spravované instance Azure SQL Database

* Vytvoření spravované instance Azure SQL Database pomocí následujících podrobností v článku [vytvořit Azure SQL Database Managed Instance na webu Azure Portal](https://aka.ms/sqldbmi).
* Otevření brány firewall pro povolení provozu SMB na portu 445 pro rozsah IP adres služby Azure Database migrace adresy nebo podsítě.
* Otevřete bránu Windows Firewall a povolte službě Azure Database Migration Service přístup ke zdrojovému SQL Serveru, který ve výchozím nastavení probíhá přes port TCP 1433.
* Pokud provozujete několik pojmenovaných instancí SQL Serveru s využitím dynamických portů, možná budete chtít povolit službu SQL Browser a přístup k portu UDP 1434 přes vaše brány firewall, aby se služba Azure Database Migration Service mohla připojit k pojmenované instanci na vašem zdrojovém serveru.
* Ujistěte se, že přihlašovací jména použitá pro připojení ke zdrojovému SQL Serveru a cílové spravované instanci patří členům role serveru sysadmin.
* Vytvořte sdílenou síťovou složku, kterou může Azure Database Migration Service použít k zálohování zdrojové databáze.
* Ujistěte se, že účet služby, ve kterém je spuštěná zdrojová instance SQL Serveru, má oprávnění k zápisu do sdílené síťové složky, kterou jste vytvořili, a že účet počítače pro zdrojový server má k této sdílené složce přístup pro čtení i zápis.
* Poznamenejte si uživatele Windows (a jeho heslo) s oprávněním Úplné řízení ke sdílené síťové složce, kterou jste vytvořili dříve. Služba Azure Database Migration Service zosobní přihlašovací údaje uživatele za účelem nahrání záložních souborů do kontejneru úložiště Azure pro operaci obnovení.
* Vytvořit kontejner objektů blob a získat jeho identifikátor URI SAS pomocí kroků v článku [prostředků Správa služby Azure Blob Storage pomocí Storage Exploreru](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container). Ujistěte se, že při vytváření identifikátor URI SAS vyberte všechna oprávnění (čtení, zápisu, Delete, List) v okně zásad.

   > [!NOTE]
   > Úplný seznam požadovaných součástí pro Azure Database Migration Service můžete provést migraci z SQL serveru do Azure SQL Database Managed Instance, najdete v kurzu [migrace systému SQL Server do Azure SQL Database Managed Instance ](https://aka.ms/migratetomiusingdms).

## <a name="next-steps"></a>Další postup

Přehled služby Azure Database Migration Service a dostupnosti v jednotlivých oblastech najdete v článku [co je Azure Database Migration Service](dms-overview.md).
