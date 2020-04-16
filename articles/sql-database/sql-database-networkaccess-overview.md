---
title: Ovládací prvky přístupu k síti
description: Přehled ovládacích prvků přístupu k síti pro Azure SQL Database a Datový sklad pro správu přístupu a konfiguraci jedné nebo sdružené databáze.
services: sql-database
ms.service: sql-database
ms.subservice: security
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 03/09/2020
ms.openlocfilehash: 8b4ee679b21d904f997f727f5f26275c86acc9c5
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414419"
---
# <a name="azure-sql-database-and-data-warehouse-network-access-controls"></a>Ovládací prvky přístupu k síti Azure SQL Database a Data Warehouse

> [!NOTE]
> Tento článek se vztahuje na Server Azure SQL a databáze SQL Database a SQL Data Warehouse, které jsou vytvořené na serveru Azure SQL. Pro zjednodušení se SQL Database používá k označení SQL Database i SQL Data Warehouse.

> [!IMPORTANT]
> Tento článek se *nevztahuje* na **azure SQL database spravované instance**. Další informace o konfiguraci sítě naleznete v [tématu připojení ke spravované instanci](sql-database-managed-instance-connect-app.md) .

Když vytvoříte nový Azure SQL Server z [webu Azure Portal](sql-database-single-database-get-started.md), výsledkem je veřejný koncový bod ve formátu, *yourservername.database.windows.net*.

Pomocí následujících ovládacích prvků přístupu k síti můžete selektivně povolit přístup k databázi SQL prostřednictvím veřejného koncového bodu:
- Povolit služby Azure: Když je nastavena na ZAPNUTO, další prostředky v rámci hranice Azure, například virtuální počítač Azure, přístup k databázi SQL

- Pravidla brány firewall IP: Pomocí této funkce můžete explicitně povolit připojení z určité adresy IP, například z místních počítačů.

Můžete také povolit soukromý přístup k databázi SQL z [virtuálních sítí](../virtual-network/virtual-networks-overview.md) prostřednictvím:
- Pravidla brány firewall virtuální sítě: Pomocí této funkce můžete povolit provoz z určité virtuální sítě v rámci hranice Azure.

- Privátní odkaz: Pomocí této funkce můžete vytvořit privátní koncový bod pro Azure SQL Server v rámci konkrétní virtuální sítě.



Podívejte se na níže uvedené video pro vysoké úrovni vysvětlení těchto ovládacích prvků přístupu a co dělají:
> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Data-Exposed--SQL-Database-Connectivity-Explained/player?WT.mc_id=dataexposed-c9-niner]


## <a name="allow-azure-services"></a>Povolení služeb Azure 
Při vytváření nového Azure SQL Server [z webu Azure Portal](sql-database-single-database-get-started.md)toto nastavení zůstane nezaškrtnuté.



Toto nastavení můžete také změnit prostřednictvím podokna brány firewall po vytvoření Azure SQL Server následujícím způsobem.
  
 ![Snímek obrazovky se správou brány firewall serveru][2]

Když je **nastavena na ON** Azure SQL Server umožňuje komunikaci ze všech prostředků uvnitř hranice Azure, které může nebo nemusí být součástí vašeho předplatného.

V mnoha případech je nastavení **ON** tolerantnější než to, co většina zákazníků chce. Mohou chtít nastavit toto nastavení **na OFF** a nahradit jej více omezující pravidla brány firewall IP nebo pravidla brány firewall virtuální sítě. To má vliv na následující funkce, které běží na virtuálních počítačích v Azure, které nejsou součástí vaší virtuální sítě, a proto se připojují k databázi SQL prostřednictvím IP adresy Azure.

### <a name="import-export-service"></a>Import exportní služby
Import export ní služba **nefunguje, když povolit přístup ke službám Azure** je **nastavena**na OFF . Problém však můžete vyřešit [ručním spuštěním sqlpackage.exe z virtuálního počítače Azure nebo provedením exportu](https://docs.microsoft.com/azure/sql-database/import-export-from-vm) přímo ve vašem kódu pomocí rozhraní DACFx API.

### <a name="data-sync"></a>Synchronizace dat
Chcete-li použít funkci synchronizace dat s **možností Povolit přístup ke službám Azure** nastaveným na **hodnotu OFF**, je třeba vytvořit jednotlivé položky pravidel brány firewall pro přidání IP adres z [značky služby](sql-database-server-level-firewall-rule.md) **SQL** pro oblast hostující databázi **Hub.**
Přidejte tato pravidla brány firewall na úrovni serveru do logických serverů hostujících databáze **Hub** i **Členské** databáze (které mohou být v různých oblastech)

Ke generování IP adres odpovídajících značce SQL pro oblast Západní USA použijte následující skript Prostředí PowerShell.
```powershell
PS C:\>  $serviceTags = Get-AzNetworkServiceTag -Location eastus2
PS C:\>  $sql = $serviceTags.Values | Where-Object { $_.Name -eq "Sql.WestUS" }
PS C:\> $sql.Properties.AddressPrefixes.Count
70
PS C:\> $sql.Properties.AddressPrefixes
13.86.216.0/25
13.86.216.128/26
13.86.216.192/27
13.86.217.0/25
13.86.217.128/26
13.86.217.192/27
```

> [!TIP]
> Get-AzNetworkServiceTag vrátí globální rozsah pro značku služby SQL navzdory zadání parametru Location. Nezapomeňte ji filtrovat do oblasti, která je hostitelem databáze hubu používané vaší synchronizační skupinou.

Všimněte si, že výstup skriptu PowerShell je v zápisu Classless Inter-Domain Routing (CIDR) a to je třeba převést do formátu Počáteční a Koncové IP adresy pomocí [Get-IPrangeStartEnd.ps1,](https://gallery.technet.microsoft.com/scriptcenter/Start-and-End-IP-addresses-bcccc3a9) jako je tento
```powershell
PS C:\> Get-IPrangeStartEnd -ip 52.229.17.93 -cidr 26                                                                   
start        end
-----        ---
52.229.17.64 52.229.17.127
```

Proveďte následující další kroky k převodu všech IP adres z formátu CIDR na počáteční a koncový formát IP adresy.

```powershell
PS C:\>foreach( $i in $sql.Properties.AddressPrefixes) {$ip,$cidr= $i.split('/') ; Get-IPrangeStartEnd -ip $ip -cidr $cidr;}                                                                                                                
start          end
-----          ---
13.86.216.0    13.86.216.127
13.86.216.128  13.86.216.191
13.86.216.192  13.86.216.223
```
Teď je můžete přidat jako odlišná pravidla brány firewall a pak nastavit **povolit službám Azure přístup k serveru** off.


## <a name="ip-firewall-rules"></a>Pravidla brány firewall IP
Brána firewall založená na PROTOKOLU IP je funkce serveru Azure SQL Server, která zabraňuje veškerému přístupu k databázovému serveru, dokud explicitně [nepřidáte IP adresy](sql-database-server-level-firewall-rule.md) klientských počítačů.


## <a name="virtual-network-firewall-rules"></a>Pravidla brány firewall virtuální sítě

Kromě pravidel IP umožňuje brána firewall Azure SQL Server definovat *pravidla virtuální sítě*.  
Další informace najdete v [tématu koncové body a pravidla služby Virtuální síť pro Azure SQL Database](sql-database-vnet-service-endpoint-rule-overview.md) nebo se podívejte na toto video:

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Data-Exposed--Demo--Vnet-Firewall-Rules-for-SQL-Database/player?WT.mc_id=dataexposed-c9-niner]

 ### <a name="azure-networking-terminology"></a>Terminologie azure networkingu  
Při zkoumání pravidel brány firewall virtuální sítě si uvědomte následující podmínky sítě Azure

**Virtuální síť:** K předplatnému Azure můžete mít přidružené virtuální sítě. 

**Podsíť:** Virtuální síť obsahuje **podsítě**. Všechny virtuální počítače Azure (VM), které jste přiřadili k podsítím. Jedna podsíť může obsahovat více virtuálních počítačů nebo jiných výpočetních uzlů. Výpočetní uzly, které jsou mimo vaši virtuální síť, nemají přístup k vaší virtuální síti, pokud nenakonfigurujete zabezpečení tak, aby přístup umožňovalo.

**Koncový bod služby Virtuální síť:** [Koncový bod služby Virtuální síť](../virtual-network/virtual-network-service-endpoints-overview.md) je podsíť, jejíž hodnoty vlastností zahrnují jeden nebo více formálních názvů typů služby Azure. V tomto článku nás zajímá název typu **Microsoft.Sql**, který odkazuje na službu Azure s názvem SQL Database.

**Pravidlo virtuální sítě:** Pravidlo virtuální sítě pro server SQL Database je podsíť, která je uvedena v seznamu řízení přístupu (ACL) serveru SQL Database. Chcete-li být v acl pro databázi SQL, podsíť musí obsahovat název typu **Microsoft.Sql.** Pravidlo virtuální sítě říká serveru SQL Database přijímat komunikaci z každého uzlu, který je v podsíti.


## <a name="ip-vs-virtual-network-firewall-rules"></a>Pravidla brány firewall ip vs. virtuální sítě

Brána firewall Azure SQL Server umožňuje určit rozsahy IP adres, ze kterých jsou komunikace přijímány do databáze SQL. Tento přístup je v pořádku pro stabilní IP adresy, které jsou mimo privátní síť Azure. Virtuální počítače (VM) v privátní síti Azure jsou však nakonfigurované s *dynamickými* IP adresami. Dynamické IP adresy se můžou změnit při restartování virtuálního počítače a zase zneplatní pravidlo brány firewall založené na protokolu IP. Bylo by pošetilé zadat dynamickou adresu IP v pravidle brány firewall v produkčním prostředí.

Toto omezení můžete obejít získáním *statické* IP adresy pro váš virtuální počítač. Podrobnosti najdete [v tématu Konfigurace privátních IP adres pro virtuální počítač pomocí portálu Azure](../virtual-network/virtual-networks-static-private-ip-arm-pportal.md). Statický přístup IP však může být obtížné spravovat a je nákladné při práci ve velkém měřítku. 

Pravidla virtuální sítě jsou jednodušší alternativou k vytvoření a správě přístupu z určité podsítě, která obsahuje vaše virtuální počítače.

> [!NOTE]
> V podsíti ještě nemůžete mít databázi SQL. Pokud váš server Azure SQL Database byl uzel v podsíti ve vaší virtuální síti, všechny uzly ve virtuální síti mohou komunikovat s databází SQL. V takovém případě mohou virtuální počítače komunikovat s databází SQL, aniž by bylo nutné použít pravidla virtuální sítě nebo pravidla IP.

## <a name="private-link"></a>Privátní propojení 
Private Link umožňuje připojení k Azure SQL Server přes **privátní koncový bod**. Privátní koncový bod je privátní IP adresa v rámci konkrétní [virtuální sítě](../virtual-network/virtual-networks-overview.md) a podsítě.

## <a name="next-steps"></a>Další kroky

- Úvodní příručka k vytvoření pravidla brány firewall IP na úrovni serveru najdete [v tématu Vytvoření databáze Azure SQL](sql-database-single-database-get-started.md).

- Úvodní příručka k vytvoření pravidla brány firewall virtuální sítě na úrovni serveru najdete v tématu [koncové body služby Virtuální síť a pravidla pro Azure SQL Database](sql-database-vnet-service-endpoint-rule-overview.md).

- Nápovědu k připojení k databázi Azure SQL z aplikací s otevřeným zdrojovým kódem nebo aplikací jiných výrobců najdete v [tématu Ukázky kódu rychlého spuštění klienta do databáze SQL .](https://msdn.microsoft.com/library/azure/ee336282.aspx)

- Informace o dalších portech, které budete muset otevřít, naleznete v **databázi SQL: Mimo vs uvnitř** [porty nad 1433 pro ADO.NET 4.5 a SQL Database](sql-database-develop-direct-route-ports-adonet-v12.md)

- Přehled připojení k databázi Azure SQL najdete v tématu [Architektura připojení Azure SQL](sql-database-connectivity-architecture.md)

- Přehled zabezpečení Azure SQL Database najdete v tématu [Zabezpečení databáze](sql-database-security-overview.md)

<!--Image references-->
[1]: ./media/sql-database-get-started-portal/new-server2.png
[2]: ./media/sql-database-get-started-portal/manage-server-firewall.png

