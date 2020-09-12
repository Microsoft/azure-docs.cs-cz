---
title: Řízení přístupu k síti
titleSuffix: Azure SQL Database & Azure Synapse Analytics
description: Přehled správy a řízení přístupu k síti pro Azure SQL Database a Azure synapse Analytics (dřív SQL Data Warehouse).
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sqldbrb=3
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 03/09/2020
ms.openlocfilehash: caad78bf61c9ad470464d69c7320aa1d08dcee09
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/03/2020
ms.locfileid: "89435367"
---
# <a name="azure-sql-database-and-azure-synapse-analytics-network-access-controls"></a>Azure SQL Database a Azure synapse Analytics – ovládací prvky přístupu k síti

Když vytvoříte logický SQL Server z [Azure Portal](single-database-create-quickstart.md) pro Azure SQL Database a Azure synapse Analytics, výsledkem je veřejný koncový bod ve formátu *yourservername.Database.Windows.NET*.

Pomocí následujících ovládacích prvků přístupu k síti můžete selektivně dovolit přístup k databázi prostřednictvím veřejného koncového bodu:

- Povolení služeb Azure: Pokud je tato možnost nastavená na ON, můžou mít k disSQL Database k dishranici jiné prostředky v rámci hranice Azure, například virtuální počítač Azure.
- Pravidla brány firewall protokolu IP: pomocí této funkce můžete výslovně dovolit připojení z konkrétní IP adresy, například z místních počítačů.

Privátní přístup k databázi nástroje můžete také z [virtuálních sítí](../../virtual-network/virtual-networks-overview.md) dovolit prostřednictvím:

- Pravidla brány firewall virtuální sítě: pomocí této funkce povolíte provoz z konkrétní virtuální sítě v rámci hranice Azure.
- Privátní odkaz: pomocí této funkce můžete vytvořit privátní koncový bod pro [logický SQL Server](logical-servers.md) v konkrétní virtuální síti.

> [!IMPORTANT]
> Tento článek se *nevztahuje na* **spravovanou instanci SQL**. Další informace o konfiguraci sítě najdete v tématu [připojení ke spravované instanci Azure SQL](../managed-instance/connect-application-instance.md) .

V níže uvedeném videu najdete nejdůležitější vysvětlení těchto ovládacích prvků přístupu a jejich možnosti:

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Data-Exposed--SQL-Database-Connectivity-Explained/player?WT.mc_id=dataexposed-c9-niner]

## <a name="allow-azure-services"></a>Povolení služeb Azure

Během vytváření nového logického serveru SQL Server [z Azure Portal](single-database-create-quickstart.md)je toto nastavení ponecháno nezaškrtnuté.

Toto nastavení můžete také změnit přes podokno brány firewall po vytvoření logického SQL serveru následujícím způsobem.
  
![Snímek obrazovky s bránou firewall serveru pro správu][2]

Když nastavíte **on**, Server umožňuje komunikaci ze všech prostředků v rámci hranice Azure, která může nebo nemusí být součástí vašeho předplatného.

V mnoha případech je nastavení **on** přísnější než to, co nejvíc zákazníci chtějí. Toto nastavení můžete nastavit na **vypnuto** a nahradit ho více omezujícími pravidly FIREWALLU protokolu IP nebo pomocí pravidel brány firewall virtuální sítě. 

To ale má vliv na následující funkce, které běží na virtuálních počítačích v Azure, které nejsou součástí vaší virtuální sítě, a proto se k databázi připojí přes IP adresu Azure:

### <a name="import-export-service"></a>Import služby export

Služba import exportu nefunguje **, když**je **povolený přístup ke službám Azure** . Problém ale můžete obejít [tak, že ručně spustíte sqlpackage.exe z virtuálního počítače Azure nebo exportujete](https://docs.microsoft.com/azure/sql-database/import-export-from-vm) přímo do kódu pomocí rozhraní API DACFx.

### <a name="data-sync"></a>Synchronizace dat

Chcete-li použít funkci synchronizace dat s nastavením zakázat **přístup ke službám Azure** **, je**třeba vytvořit jednotlivé položky pravidla brány firewall a [Přidat IP adresy](firewall-create-server-level-portal-quickstart.md) ze **značky služby SQL** pro oblast hostující databázi **centra** .
Přidejte tato pravidla brány firewall na úrovni serveru na servery hostující databázi **centrálních** i **členských** databází (které mohou být v různých oblastech).

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

Všimněte si, že výstup skriptu PowerShellu je v zápisu CIDR (Classless Inter-Domain Routing). Tento postup je potřeba převést na formát počáteční a koncové IP adresy, a to pomocí [Get-IPrangeStartEnd.ps1](https://gallery.technet.microsoft.com/scriptcenter/Start-and-End-IP-addresses-bcccc3a9) takto:

```powershell
PS C:\> Get-IPrangeStartEnd -ip 52.229.17.93 -cidr 26
start        end
-----        ---
52.229.17.64 52.229.17.127
```

Pomocí tohoto dalšího skriptu PowerShellu můžete převést všechny IP adresy z CIDR na počáteční a koncový formát IP adresy.

```powershell
PS C:\>foreach( $i in $sql.Properties.AddressPrefixes) {$ip,$cidr= $i.split('/') ; Get-IPrangeStartEnd -ip $ip -cidr $cidr;}
start          end
-----          ---
13.86.216.0    13.86.216.127
13.86.216.128  13.86.216.191
13.86.216.192  13.86.216.223
```

Nyní je můžete přidat jako jedinečná pravidla brány firewall a potom nastavit možnost **Povolení služeb Azure přístup k serveru**  na off.

## <a name="ip-firewall-rules"></a>Pravidla brány firewall protokolu IP

Brána firewall založená na protokolu IP je funkcí logického SQL serveru v Azure, která zabraňuje všem přístupům k vašemu serveru, dokud explicitně [nepřidáte IP adresy](firewall-create-server-level-portal-quickstart.md) klientských počítačů.

## <a name="virtual-network-firewall-rules"></a>Pravidla brány firewall virtuální sítě

Kromě pravidel protokolu IP je možné pomocí brány firewall serveru definovat *pravidla virtuální sítě*.  
Další informace najdete v tématu [koncové body a pravidla služby virtuální sítě pro Azure SQL Database](vnet-service-endpoint-rule-overview.md) nebo Sledujte toto video:

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Data-Exposed--Demo--Vnet-Firewall-Rules-for-SQL-Database/player?WT.mc_id=dataexposed-c9-niner]

### <a name="azure-networking-terminology"></a>Terminologie sítí Azure

Při zkoumání pravidel brány firewall virtuální sítě Pamatujte na tyto síťové podmínky Azure

**Virtuální síť:** Můžete mít virtuální sítě přidružené k vašemu předplatnému Azure.

**Podsíť:** Virtuální síť obsahuje **podsítě**. Všechny virtuální počítače Azure, které jste přiřadili k podsítím. Jedna podsíť může obsahovat několik virtuálních počítačů nebo jiných výpočetních uzlů. Výpočetní uzly, které jsou mimo vaši virtuální síť, nemají přístup k virtuální síti, pokud nenastavíte zabezpečení tak, aby umožňovalo přístup.

**Koncový bod služby virtuální sítě:** [Koncový bod služby virtuální sítě](../../virtual-network/virtual-network-service-endpoints-overview.md) je podsíť, jejíž hodnoty vlastností zahrnují jeden nebo více formálních názvů typů služeb Azure. V tomto článku se zajímá název typu **Microsoft. SQL**, který odkazuje na službu Azure s názvem SQL Database.

**Pravidlo virtuální sítě:** Pravidlo virtuální sítě pro váš server je podsíť, která je uvedená v seznamu řízení přístupu (ACL) vašeho serveru. Aby byl v seznamu ACL pro vaši databázi v SQL Database, podsíť musí obsahovat název typu **Microsoft. SQL** . Pravidlo virtuální sítě instruuje server, aby přijímal komunikaci z každého uzlu, který je v podsíti.

## <a name="ip-vs-virtual-network-firewall-rules"></a>IP a pravidla brány firewall virtuální sítě

Brána Azure SQL Database firewall umožňuje zadat rozsahy IP adres, ze kterých se SQL Database přijímá komunikace. Tento přístup je v pořádku pro stabilní IP adresy, které jsou mimo privátní síť Azure. Virtuální počítače v privátní síti Azure ale mají nakonfigurovanou *dynamickou* IP adresu. Dynamické IP adresy se můžou změnit, když se virtuální počítač restartuje, a zase ověří pravidlo brány firewall založené na protokolu IP. V provozním prostředí by se Folly zadat dynamickou IP adresu v pravidle brány firewall.

Toto omezení můžete obejít tak, že získáte *statickou* IP adresu pro virtuální počítač. Podrobnosti najdete v tématu [Vytvoření virtuálního počítače se statickou veřejnou IP adresou pomocí Azure Portal](../../virtual-network/virtual-network-deploy-static-pip-arm-portal.md). Přístup ke statickým IP adresám se ale může obtížně spravovat a je nákladný, pokud je to v rozsahu.

Pravidla virtuální sítě jsou jednodušší alternativou ke zřízení a správě přístupu z konkrétní podsítě, která obsahuje vaše virtuální počítače.

> [!NOTE]
> V podsíti ještě nemůžete mít SQL Database. Pokud byl váš server uzlem v podsíti ve vaší virtuální síti, můžou všechny uzly v rámci virtuální sítě komunikovat s vaším SQL Database. V takovém případě můžou vaše virtuální počítače komunikovat s SQL Database bez nutnosti používat pravidla virtuální sítě nebo pravidla protokolu IP.

## <a name="private-link"></a>Private Link

Privátní odkaz vám umožní připojit se k serveru prostřednictvím **privátního koncového bodu**. Privátní koncový bod je privátní IP adresa v konkrétní [virtuální síti](../../virtual-network/virtual-networks-overview.md) a podsíti.

## <a name="next-steps"></a>Další kroky

- Rychlý Start k vytvoření pravidla brány firewall IP na úrovni serveru najdete v tématu [Vytvoření databáze v SQL Database](single-database-create-quickstart.md).

- Rychlý Start týkající se vytvoření pravidla brány firewall virtuální sítě na úrovni serveru najdete v tématu [Virtual Network koncové body služby a pravidla pro Azure SQL Database](vnet-service-endpoint-rule-overview.md).

- Nápovědu k připojení k databázi v SQL Database z otevřených zdrojů nebo aplikací třetích stran najdete v tématu [ukázky kódu pro rychlý Start klienta k SQL Database](https://msdn.microsoft.com/library/azure/ee336282.aspx).

- Informace o dalších portech, které možná budete muset otevřít, najdete v části **SQL Database: mimo rámec a v** části [porty nad 1433 pro ADO.NET 4,5 a SQL Database](adonet-v12-develop-direct-route-ports.md)

- Přehled připojení Azure SQL Database najdete v tématu [Architektura připojení k Azure SQL](connectivity-architecture.md) .

- Přehled zabezpečení Azure SQL Database najdete v tématu [zabezpečení databáze](security-overview.md) .

<!--Image references-->
[1]: media/quickstart-create-single-database/new-server2.png
[2]: media/quickstart-create-single-database/manage-server-firewall.png
 
