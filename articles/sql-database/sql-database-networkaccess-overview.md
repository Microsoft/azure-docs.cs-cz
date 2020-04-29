---
title: Řízení přístupu k síti
description: Přehled řízení přístupu k síti pro Azure SQL Database a datový sklad pro správu přístupu a konfiguraci jedné nebo sdružené databáze.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81414419"
---
# <a name="azure-sql-database-and-data-warehouse-network-access-controls"></a>Azure SQL Database a řízení přístupu k síti v datovém skladu

> [!NOTE]
> Tento článek se týká Azure SQL serveru a databází SQL Database i SQL Data Warehouse, které jsou vytvořené na Azure SQL serveru. Pro zjednodušení se SQL Database používá k označení SQL Database i SQL Data Warehouse.

> [!IMPORTANT]
> Tento článek se *nevztahuje na* **Azure SQL Database spravovanou instanci**. Další informace o konfiguraci sítě najdete v tématu [připojení ke spravované instanci](sql-database-managed-instance-connect-app.md) .

Když v [Azure Portal](sql-database-single-database-get-started.md)vytvoříte novou SQL Server Azure, výsledkem je veřejný koncový bod ve formátu *yourservername.Database.Windows.NET*.

Pomocí následujících ovládacích prvků přístupu k síti můžete selektivně dovolit přístup k SQL Database prostřednictvím veřejného koncového bodu:
- Povolení služeb Azure: Pokud je tato možnost nastavená na ON, můžou mít k disSQL Database k dishranici jiné prostředky v rámci hranice Azure, například virtuální počítač Azure.

- Pravidla brány firewall protokolu IP: pomocí této funkce můžete výslovně dovolit připojení z konkrétní IP adresy, například z místních počítačů.

Privátní přístup k SQL Database z [virtuálních sítí](../virtual-network/virtual-networks-overview.md) můžete také taky udělit prostřednictvím:
- Virtual Network pravidla brány firewall: pomocí této funkce povolíte provoz z konkrétní Virtual Network v rámci hranice Azure.

- Privátní odkaz: pomocí této funkce můžete vytvořit privátní koncový bod pro Azure SQL Server v rámci konkrétní Virtual Network



V níže uvedeném videu najdete nejdůležitější vysvětlení těchto ovládacích prvků přístupu a jejich možnosti:
> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Data-Exposed--SQL-Database-Connectivity-Explained/player?WT.mc_id=dataexposed-c9-niner]


## <a name="allow-azure-services"></a>Povolení služeb Azure 
Při vytváření nové služby Azure SQL Server [z Azure Portal](sql-database-single-database-get-started.md)zůstane toto nastavení ponecháno nezaškrtnuté.



Toto nastavení můžete také změnit přes podokno brány firewall po vytvoření služby Azure SQL Server následujícím způsobem.
  
 ![Snímek obrazovky s bránou firewall serveru pro správu][2]

Když je tato možnost nastavená na **on** Azure SQL Server, umožňuje komunikaci ze všech prostředků v rámci hranice Azure, která může nebo nemusí být součástí vašeho předplatného.

V mnoha případech je nastavení **on** přísnější než to, co nejvíc zákazníci chtějí. Můžou chtít nastavit toto nastavení na **vypnuto** a nahradit ho více omezujícími pravidly FIREWALLU protokolu IP nebo Virtual Network pravidly brány firewall. To má vliv na následující funkce, které běží na virtuálních počítačích v Azure, které nejsou součástí vaší virtuální sítě, a proto se k SQL Database připojí přes IP adresu Azure.

### <a name="import-export-service"></a>Import služby export
Služba import exportu nefunguje **, když**je **povolený přístup ke službám Azure** . Problém ale můžete obejít [tak, že ručně spustíte SqlPackage. exe z virtuálního počítače Azure nebo exportujete](https://docs.microsoft.com/azure/sql-database/import-export-from-vm) přímo do kódu pomocí rozhraní DACFx API.

### <a name="data-sync"></a>Synchronizace dat
Chcete-li použít funkci synchronizace dat s nastavením zakázat **přístup ke službám Azure** **, je**třeba vytvořit jednotlivé položky pravidla brány firewall a [Přidat IP adresy](sql-database-server-level-firewall-rule.md) ze **značky služby SQL** pro oblast hostující databázi **centra** .
Přidejte tato pravidla brány firewall na úrovni serveru do logických serverů, které hostují databáze **centrálních** i **členských** databází (které mohou být v různých oblastech).

Pomocí následujícího skriptu PowerShellu vygenerujte IP adresy odpovídající značce služby SQL pro Západní USA oblast.
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
> Get-AzNetworkServiceTag vrátí globální rozsah pro tag služby SQL navzdory zadání parametru Location. Nezapomeňte ho filtrovat do oblasti, která je hostitelem databáze centra používané skupinou synchronizace.

Všimněte si, že výstup skriptu PowerShellu je v zápisu CIDR (Classless Inter-Domain Routing) a je potřeba ho převést na formát počáteční a koncové IP adresy pomocí [Get-IPrangeStartEnd. ps1,](https://gallery.technet.microsoft.com/scriptcenter/Start-and-End-IP-addresses-bcccc3a9) jako je to.
```powershell
PS C:\> Get-IPrangeStartEnd -ip 52.229.17.93 -cidr 26                                                                   
start        end
-----        ---
52.229.17.64 52.229.17.127
```

Provedením následujících dalších kroků převedete všechny IP adresy z CIDR do počátečního a koncového formátu IP adresy.

```powershell
PS C:\>foreach( $i in $sql.Properties.AddressPrefixes) {$ip,$cidr= $i.split('/') ; Get-IPrangeStartEnd -ip $ip -cidr $cidr;}                                                                                                                
start          end
-----          ---
13.86.216.0    13.86.216.127
13.86.216.128  13.86.216.191
13.86.216.192  13.86.216.223
```
Nyní je můžete přidat jako jedinečná pravidla brány firewall a potom nastavit možnost **Povolení služeb Azure přístup k serveru** na off.


## <a name="ip-firewall-rules"></a>Pravidla brány firewall protokolu IP
Brána firewall založená na protokolu IP je funkcí služby Azure SQL Server, která zabraňuje všem přístupům k databázovému serveru, dokud explicitně [nepřidáte IP adresy](sql-database-server-level-firewall-rule.md) klientských počítačů.


## <a name="virtual-network-firewall-rules"></a>Virtual Network pravidla brány firewall

Kromě pravidel protokolu IP vám brána firewall pro Azure SQL Server umožňuje definovat *pravidla virtuální sítě*.  
Další informace najdete v tématu [Virtual Network koncových bodů a pravidel služby pro Azure SQL Database](sql-database-vnet-service-endpoint-rule-overview.md) nebo Sledujte toto video:

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Data-Exposed--Demo--Vnet-Firewall-Rules-for-SQL-Database/player?WT.mc_id=dataexposed-c9-niner]

 ### <a name="azure-networking-terminology"></a>Terminologie sítí Azure  
Při zkoumání Virtual Network pravidel brány firewall Pamatujte na následující síťové podmínky Azure.

**Virtuální síť:** Můžete mít virtuální sítě přidružené k vašemu předplatnému Azure. 

**Podsíť:** Virtuální síť obsahuje **podsítě**. Všechny virtuální počítače Azure, které jste přiřadili k podsítím. Jedna podsíť může obsahovat několik virtuálních počítačů nebo jiných výpočetních uzlů. Výpočetní uzly, které jsou mimo vaši virtuální síť, nemají přístup k virtuální síti, pokud nenastavíte zabezpečení tak, aby umožňovalo přístup.

**Koncový bod služby Virtual Network:** [Koncový bod služby Virtual Network](../virtual-network/virtual-network-service-endpoints-overview.md) je podsíť, jejíž hodnoty vlastností zahrnují jeden nebo více formálních názvů typů služeb Azure. V tomto článku se zajímá název typu **Microsoft. SQL**, který odkazuje na službu Azure s názvem SQL Database.

**Pravidlo virtuální sítě:** Pravidlo virtuální sítě pro server SQL Database je podsíť, která je uvedená v seznamu řízení přístupu (ACL) vašeho serveru SQL Database. Aby byl v seznamu ACL pro váš SQL Database, podsíť musí obsahovat název typu **Microsoft. SQL** . Pravidlo virtuální sítě přikáže serveru SQL Database, aby přijímal komunikaci z každého uzlu, který je v podsíti.


## <a name="ip-vs-virtual-network-firewall-rules"></a>IP a Virtual Network pravidla brány firewall

Brána firewall pro Azure SQL Server umožňuje zadat rozsahy IP adres, ze kterých se SQL Database přijímá komunikace. Tento přístup je v pořádku pro stabilní IP adresy, které jsou mimo privátní síť Azure. Virtuální počítače v privátní síti Azure ale mají nakonfigurovanou *dynamickou* IP adresu. Dynamické IP adresy se můžou změnit, když se virtuální počítač restartuje, a zase ověří pravidlo brány firewall založené na protokolu IP. V provozním prostředí by se Folly zadat dynamickou IP adresu v pravidle brány firewall.

Toto omezení můžete obejít tak, že získáte *statickou* IP adresu pro virtuální počítač. Podrobnosti najdete v tématu [Konfigurace privátních IP adres pro virtuální počítač pomocí Azure Portal](../virtual-network/virtual-networks-static-private-ip-arm-pportal.md). Přístup ke statickým IP adresám se ale může obtížně spravovat a při velkém rozsahu je nákladný. 

Pravidla virtuální sítě jsou jednodušší alternativou ke zřízení a správě přístupu z konkrétní podsítě, která obsahuje vaše virtuální počítače.

> [!NOTE]
> V podsíti ještě nemůžete mít SQL Database. Pokud váš server Azure SQL Database byl uzlem v podsíti ve vaší virtuální síti, můžou všechny uzly v rámci virtuální sítě komunikovat s vaší SQL Database. V takovém případě můžou vaše virtuální počítače komunikovat s SQL Database bez nutnosti používat pravidla virtuální sítě nebo pravidla protokolu IP.

## <a name="private-link"></a>Privátní propojení 
Privátní odkaz vám umožní připojit se k Azure SQL Server prostřednictvím **privátního koncového bodu**. Privátní koncový bod je privátní IP adresa v rámci konkrétní [Virtual Network](../virtual-network/virtual-networks-overview.md) a podsítě.

## <a name="next-steps"></a>Další kroky

- Rychlý Start k vytvoření pravidla brány firewall IP na úrovni serveru najdete v tématu [Vytvoření databáze SQL Azure](sql-database-single-database-get-started.md).

- Rychlý Start k vytvoření pravidla brány firewall virtuální sítě na úrovni serveru najdete v tématu [Virtual Network koncové body služby a pravidla pro Azure SQL Database](sql-database-vnet-service-endpoint-rule-overview.md).

- Nápovědu k připojení k databázi SQL Azure z Open Source nebo aplikací třetích stran najdete v tématu [ukázky kódu pro rychlý Start klienta k SQL Database](https://msdn.microsoft.com/library/azure/ee336282.aspx).

- Informace o dalších portech, které možná budete muset otevřít, najdete v části **SQL Database: mimo rámec a v** části [porty nad 1433 pro ADO.NET 4,5 a SQL Database](sql-database-develop-direct-route-ports-adonet-v12.md)

- Přehled připojení Azure SQL Database najdete v tématu [Architektura připojení k Azure SQL](sql-database-connectivity-architecture.md) .

- Přehled zabezpečení Azure SQL Database najdete v tématu [zabezpečení databáze](sql-database-security-overview.md) .

<!--Image references-->
[1]: ./media/sql-database-get-started-portal/new-server2.png
[2]: ./media/sql-database-get-started-portal/manage-server-firewall.png

