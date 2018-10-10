---
title: Přehled požadavků na používání Azure Database Migration Service | Dokumentace Microsoftu
description: Další informace o přehled požadavků na používání Azure Database Migration Service k provedení migrace databází.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: ''
ms.reviewer: ''
ms.service: database-migration
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 10/09/2018
ms.openlocfilehash: b3c974eaf4e44a80046efc4b1545f29797fcaf0c
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2018
ms.locfileid: "48887662"
---
# <a name="overview-of-prerequisites-for-using-the-azure-database-migration-service"></a>Přehled požadavků na používání Azure Database Migration Service
Existuje několik předpokladů, které jsou potřeba k tomu, že Azure Database Migration Service běží bez problémů při provádění migrace databází. Některé z požadovaných součástí ve všech scénářích (párů zdroj cíl) podporované službou, zatímco jiné požadavky, které jsou jedinečné pro konkrétní scénář uplatnit.

Požadavky související s využitím Azure Database Migration Service jsou uvedené v následujících částech.

## <a name="prerequisites-common-across-migration-scenarios"></a>Požadavky, které jsou společné napříč scénáři migrace
Požadavky na Azure Database Migration Service, které jsou společné pro všechny podporované scénáře migrace patří:
- Vytvořte pro službu Azure Database Migration Service virtuální síť s použitím modelu nasazení Azure Resource Manager, který poskytuje možnosti připojení typu Site-to-Site k místním zdrojovým serverům prostřednictvím [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) nebo sítě [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Zajistěte, aby pravidla skupiny zabezpečení virtuální sítě Azure neblokovala následující komunikační porty: 443, 53, 9354, 445 a 12000. Další podrobnosti o filtrování provozu pomocí skupiny zabezpečení virtuální sítě Azure najdete v článku [Filtrování provozu sítě s použitím skupin zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
- Pokud před zdrojovými databázemi používáte zařízení brány firewall, možná bude potřeba přidat pravidla brány firewall, která službě Azure Database Migration Service povolí přístup ke zdrojovým databázím za účelem migrace.
- Nakonfigurujte bránu [Windows Firewall pro přístup k databázovému stroji](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Povolte protokol TCP/IP, který se ve výchozím nastavení zakáže během instalace SQL Serveru Express, a to podle pokynů v článku [Povolení nebo zakázání síťového protokolu serveru](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).

## <a name="prerequisites-for-migrating-sql-server-to-azure-sql-database"></a>Požadavky pro migraci SQL serveru do služby Azure SQL Database 
Kromě požadavků na služby Azure Database Migration Service, které jsou společné pro všechny scénáře migrace existují také požadavky, které platí konkrétně pro jeden scénář nebo jiného.

Při použití Azure Database Migration Service můžete provést SQL serveru do migrace Azure SQL Database, kromě požadavků, které jsou společné pro všechny scénáře migrace, je potřeba vyřešit následující další požadavky:

- Vytvoření instance Azure SQL Database instance, což uděláte pomocí následujících podrobností v článku C[vytvořit databázi Azure SQL na webu Azure Portal](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal).
- Stáhněte a nainstalujte nástroj [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) verze 3.3 nebo novější.
- Otevřete bránu Windows Firewall a povolte službě Azure Database Migration Service přístup ke zdrojovému SQL Serveru, který ve výchozím nastavení probíhá přes port TCP 1433.
- Pokud provozujete několik pojmenovaných instancí SQL Serveru s využitím dynamických portů, možná budete chtít povolit službu SQL Browser a přístup k portu UDP 1434 přes vaše brány firewall, aby se služba Azure Database Migration Service mohla připojit k pojmenované instanci na vašem zdrojovém serveru.
- Vytvořte pro server služby Azure SQL Database [pravidlo brány firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) na úrovni serveru, které službě Azure Database Migration Service povolí přístup k cílovým databázím. Zadejte rozsah podsítí virtuální sítě použité pro službu Azure Database Migration Service.
- Ujistěte se, že přihlašovací údaje použité pro připojení ke zdrojové instanci SQL Serveru mají oprávnění [CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql).
- Ujistěte se, že přihlašovací údaje použité pro připojení k cílové instanci služby Azure SQL Database mají oprávnění CONTROL DATABASE k cílovým databázím SQL Azure.

   > [!NOTE]
   > Úplný seznam požadovaných součástí pro Azure Database Migration Service můžete provést migraci z SQL serveru do služby Azure SQL Database, najdete v kurzu [migrace systému SQL Server do služby Azure SQL Database](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-azure-sql).
   > 

## <a name="prerequisites-for-migrating-sql-server-to-azure-sql-database-managed-instance"></a>Požadavky pro migraci SQL serveru do Azure SQL Database Managed Instance
- Vytvoření instance Azure SQL Database Managed Instance pomocí následujících podrobností v článku [vytvořit Azure SQL Database Managed Instance na webu Azure Portal](https://aka.ms/sqldbmi).
- Otevření brány firewall pro povolení provozu SMB na portu 445 pro rozsah IP adres služby Azure Database migrace adresy nebo podsítě.
- Otevřete bránu Windows Firewall a povolte službě Azure Database Migration Service přístup ke zdrojovému SQL Serveru, který ve výchozím nastavení probíhá přes port TCP 1433.
- Pokud provozujete několik pojmenovaných instancí SQL Serveru s využitím dynamických portů, možná budete chtít povolit službu SQL Browser a přístup k portu UDP 1434 přes vaše brány firewall, aby se služba Azure Database Migration Service mohla připojit k pojmenované instanci na vašem zdrojovém serveru.
- Ujistěte se, že přihlašovací jména použitá pro připojení ke zdrojovému SQL Serveru a cílové spravované instanci patří členům role serveru sysadmin.
- Vytvořte sdílenou síťovou složku, kterou může Azure Database Migration Service použít k zálohování zdrojové databáze.
- Ujistěte se, že účet služby, ve kterém je spuštěná zdrojová instance SQL Serveru, má oprávnění k zápisu do sdílené síťové složky, kterou jste vytvořili, a že účet počítače pro zdrojový server má k této sdílené složce přístup pro čtení i zápis.
- Poznamenejte si uživatele Windows (a jeho heslo) s oprávněním Úplné řízení ke sdílené síťové složce, kterou jste vytvořili dříve. Služba Azure Database Migration Service zosobní přihlašovací údaje uživatele za účelem nahrání záložních souborů do kontejneru úložiště Azure pro operaci obnovení.
- Vytvořit kontejner objektů blob a získat jeho identifikátor URI SAS pomocí kroků v článku [prostředků Správa služby Azure Blob Storage pomocí Storage Exploreru](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container). Ujistěte se, že při vytváření identifikátor URI SAS vyberte všechna oprávnění (čtení, zápisu, Delete, List) v okně zásad.

   > [!NOTE]
   > Úplný seznam požadovaných součástí pro Azure Database Migration Service můžete provést migraci z SQL serveru do Azure SQL Database Managed Instance, najdete v kurzu [migrace systému SQL Server do Azure SQL Database Managed Instance ](https://aka.ms/migratetomiusingdms).

## <a name="next-steps"></a>Další postup
Přehled služby Azure Database Migration Service a dostupnosti v jednotlivých oblastech najdete v článku [co je Azure Database Migration Service](dms-overview.md). 