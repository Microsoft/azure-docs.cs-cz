---
title: Azure SQL Database a řízení přístupu k síti datového skladu | Microsoft Docs
description: Přehled řízení přístupu k síti pro Azure SQL Database a datový sklad pro správu přístupu a konfiguraci jedné nebo sdružené databáze.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 08/05/2019
ms.openlocfilehash: 2d7cc217ff8ae45491c0f9d6b54ea8afea19cd2e
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2019
ms.locfileid: "69981240"
---
# <a name="azure-sql-database-and-data-warehouse-network-access-controls"></a>Azure SQL Database a řízení přístupu k síti v datovém skladu

> [!NOTE]
> Tento článek se týká Azure SQL serveru a databází SQL Database i SQL Data Warehouse, které jsou vytvořené na Azure SQL serveru. Pro zjednodušení se SQL Database používá k označení SQL Database i SQL Data Warehouse.

> [!IMPORTANT]
> Tento článek se nevztahuje na **Azure SQL Database spravovanou instanci**. Další informace o konfiguraci sítě najdete v tématu [připojení ke spravované instanci](sql-database-managed-instance-connect-app.md) .

Když vytvoříte novou službu Azure SQL Server [z Azure Portal](sql-database-single-database-get-started.md), výsledkem je veřejný koncový bod ve formátu *yourservername.Database.Windows.NET*. V rámci návrhu je odepřen veškerý přístup k veřejnému koncovému bodu. Pak můžete pomocí následujících ovládacích prvků přístupu k síti selektivně dovolit přístup k databázi SQl prostřednictvím veřejného koncového bodu.
- Povolení služeb Azure: – Pokud je nastavené na ZAPNUTo, další prostředky v rámci hranice Azure, například virtuální počítač Azure, mají přístup k SQL Database

- Pravidla brány firewall protokolu IP: – pomocí této funkce můžete výslovně dovolit připojení z konkrétní IP adresy, například z místních počítačů.

- Virtual Network pravidla brány firewall: – pomocí této funkce povolíte provoz z konkrétní Virtual Network v rámci hranice Azure.

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Data-Exposed--SQL-Database-Connectivity-Explained/player?WT.mc_id=dataexposed-c9-niner]

## <a name="allow-azure-services"></a>Povolení služeb Azure 
Při vytváření nové služby Azure SQL Server [z Azure Portal](sql-database-single-database-get-started.md)zůstane toto nastavení ponecháno nezaškrtnuté.

 ![Snímek obrazovky s vytvořením nového serveru][1]

Toto nastavení můžete také změnit přes podokno brány firewall po vytvoření služby Azure SQL Server následujícím způsobem.
  
 ![Snímek obrazovky s bránou firewall serveru pro správu][2]

Když je tato možnost nastavená na **on** Azure SQL Server, umožňuje komunikaci ze všech prostředků v rámci hranice Azure, která může nebo nemusí být součástí vašeho předplatného.

V mnoha případech je nastavení **on** přísnější než to, co nejvíc zákazníci chtějí. Můžou chtít nastavit toto nastavení na **vypnuto** a nahradit ho více omezujícími pravidly FIREWALLU protokolu IP nebo Virtual Network pravidly brány firewall. To má vliv na následující funkce:

### <a name="import-export-service"></a>Import služby export

Služba Azure SQL Database import exportu běží na virtuálních počítačích v Azure. Tyto virtuální počítače nejsou ve vaší virtuální síti, a proto při připojování k databázi získají IP adresu Azure. Při odebrání možnosti **umožnit službám Azure přístup k serveru** nebudou mít přístup k databázím tyto virtuální počítače.
Problém můžete obejít tak, že spustíte BACPAC import nebo export přímo v kódu pomocí rozhraní DACFx API.

### <a name="sql-database-query-editor"></a>Editor dotazů SQL Database

Editor dotazů Azure SQL Database je nasazený na virtuálních počítačích v Azure. Tyto virtuální počítače nejsou ve vaší virtuální síti. Virtuální počítače proto při připojování k databázi získají IP adresu Azure. Při odebrání možnosti **umožnit službám Azure přístup k serveru**nebudou mít tyto virtuální počítače přístup k vašim databázím.

### <a name="table-auditing"></a>Auditování tabulek

V současnosti existují dva způsoby, jak povolit auditování na SQL Database. Po povolení koncových bodů služby v Azure SQL Server se auditování tabulek nezdařilo. Zmírnění se omezuje na auditování objektů BLOB.

### <a name="impact-on-data-sync"></a>Dopad na synchronizaci dat

Azure SQL Database má funkci synchronizace dat, která se připojuje k vašim databázím pomocí Azure IP. Při použití koncových bodů služby vypnete možnost **Povolit službám Azure přístup k serveru** SQL Database serveru a přerušit funkci synchronizace dat.

## <a name="ip-firewall-rules"></a>Pravidla brány firewall protokolu IP
Brána firewall založená na protokolu IP je funkcí služby Azure SQL Server, která zabraňuje všem přístupům k databázovému serveru, dokud explicitně nepřidáte [IP adresy](sql-database-server-level-firewall-rule.md) klientských počítačů.


## <a name="virtual-network-firewall-rules"></a>Virtual Network pravidla brány firewall

Kromě pravidel protokolu IP vám brána firewall pro Azure SQL Server umožňuje definovat *pravidla virtuální sítě*.  
Další informace najdete v tématu [Virtual Network koncových bodů a pravidel služby pro Azure SQL Database](sql-database-vnet-service-endpoint-rule-overview.md).

 ### <a name="azure-networking-terminology"></a>Terminologie sítí Azure  
Při zkoumání Virtual Network pravidel brány firewall Pamatujte na následující síťové podmínky Azure.

**Virtuální síť:** Můžete mít virtuální sítě přidružené k vašemu předplatnému Azure. 

**Podsíť** Virtuální síť obsahuje **podsítě**. Všechny virtuální počítače Azure, které jste přiřadili k podsítím. Jedna podsíť může obsahovat několik virtuálních počítačů nebo jiných výpočetních uzlů. Výpočetní uzly, které jsou mimo vaši virtuální síť, nemají přístup k virtuální síti, pokud nenastavíte zabezpečení tak, aby umožňovalo přístup.

**Koncový bod služby Virtual Network:** [Virtual Network koncový bod služby] [VM-Virtual-Network-Service-Endpoints-Overview-649d] je podsíť, jejíž hodnoty vlastností zahrnují jeden nebo více formálních názvů typů služeb Azure. V tomto článku se zajímá název typu **Microsoft. SQL**, který odkazuje na službu Azure s názvem SQL Database.

**Pravidlo virtuální sítě:** Pravidlo virtuální sítě pro server SQL Database je podsíť, která je uvedená v seznamu řízení přístupu (ACL) vašeho serveru SQL Database. Aby byl v seznamu ACL pro váš SQL Database, podsíť musí obsahovat název typu **Microsoft. SQL** . Pravidlo virtuální sítě přikáže serveru SQL Database, aby přijímal komunikaci z každého uzlu, který je v podsíti.


## <a name="ip-vs-virtual-network-firewall-rules"></a>IP vs. Virtual Network pravidla brány firewall

Brána firewall pro Azure SQL Server umožňuje zadat rozsahy IP adres, ze kterých se SQL Database přijímá komunikace. Tento přístup je v pořádku pro stabilní IP adresy, které jsou mimo privátní síť Azure. Virtuální počítače v privátní síti Azure ale mají nakonfigurovanou dynamickou IP adresu. Dynamické IP adresy se můžou změnit, když se virtuální počítač restartuje, a zase ověří pravidlo brány firewall založené na protokolu IP. V provozním prostředí by se Folly zadat dynamickou IP adresu v pravidle brány firewall.

Toto omezení můžete obejít tak, že získáte *statickou* IP adresu pro virtuální počítač. Podrobnosti najdete v tématu [konfigurace privátních IP adres pro virtuální počítač pomocí Azure Portal] [VM-Configure-Private-IP-addresses-for-a-Virtual-Machine-using-the-Azure-Portal-321w]. Přístup ke statickým IP adresám se ale může obtížně spravovat a při velkém rozsahu je nákladný. 

Pravidla virtuální sítě jsou jednodušší alternativou ke zřízení a správě přístupu z konkrétní podsítě, která obsahuje vaše virtuální počítače.

> [!NOTE]
> V podsíti ještě nemůžete mít SQL Database. Pokud váš server Azure SQL Database byl uzlem v podsíti ve vaší virtuální síti, můžou všechny uzly v rámci virtuální sítě komunikovat s vaší SQL Database. V takovém případě můžou vaše virtuální počítače komunikovat s SQL Database bez nutnosti používat pravidla virtuální sítě nebo pravidla protokolu IP.

## <a name="next-steps"></a>Další postup

- Rychlý Start k vytvoření pravidla brány firewall IP na úrovni serveru najdete v tématu [Vytvoření databáze SQL Azure](sql-database-single-database-get-started.md).

- Rychlý Start k vytvoření pravidla brány firewall virtuální sítě na úrovni serveru najdete v tématu [Virtual Network koncové body služby a pravidla pro Azure SQL Database](sql-database-vnet-service-endpoint-rule-overview.md).

- Nápovědu k připojení k databázi SQL Azure z Open Source nebo aplikací třetích stran najdete v tématu [ukázky kódu pro rychlý Start klienta k SQL Database](https://msdn.microsoft.com/library/azure/ee336282.aspx).

- Informace o dalších portech, které možná budete muset otevřít, najdete **v SQL Database: Mimo oddíl vs** Inside [portů nad 1433 pro ADO.NET 4,5 a SQL Database](sql-database-develop-direct-route-ports-adonet-v12.md)

- Přehled připojení Azure SQL Database najdete v tématu [Architektura připojení k Azure SQL](sql-database-connectivity-architecture.md) .

- Přehled zabezpečení Azure SQL Database najdete v tématu [zabezpečení databáze](sql-database-security-overview.md) .

<!--Image references-->
[1]: ./media/sql-database-get-started-portal/new-server2.png
[2]: ./media/sql-database-get-started-portal/manage-server-firewall.png
