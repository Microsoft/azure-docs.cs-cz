---
title: Konfigurovat název distribuované sítě (DNN)
description: Naučte se nakonfigurovat distribuovanou síťovou síť (DNN) pro směrování provozu do vašich SQL Server na instanci clusteru s podporou převzetí služeb při selhání virtuálního počítače Azure (FCI).
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/02/2020
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: ce3261eca8697ae1fabc07785353a4e845dadeba
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91317000"
---
# <a name="configure-a-distributed-network-name-for-an-fci"></a>Konfigurace distribuovaného síťového názvu pro FCI 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

V Azure Virtual Machines se název distribuované sítě (DNN) používá ke směrování provozu na příslušný clusterovaný prostředek. Poskytuje snazší způsob, jak se připojit k instanci clusteru s podporou převzetí služeb při selhání (FCI) SQL Server než k názvu virtuální sítě (VNN), a to bez nutnosti Azure Load Balancer. Tato funkce je aktuálně ve verzi Preview a je k dispozici pouze pro SQL Server 2019 CU2 a novější a Windows Server 2016 a novější. 

V tomto článku se dozvíte, jak nakonfigurovat DNN pro směrování provozu do vaší FCIs pomocí SQL Server na virtuálních počítačích Azure pro zajištění vysoké dostupnosti a zotavení po havárii (HADR). 

## <a name="prerequisites"></a>Požadavky

Před dokončením kroků v tomto článku byste už měli mít:

- Bylo rozhodnuto, že název distribuované sítě je vhodnou [možností připojení pro vaše řešení hadr](hadr-cluster-best-practices.md#connectivity).
- Konfigurace [instancí clusteru s podporou převzetí služeb při selhání](failover-cluster-instance-overview.md) 
- Nainstalovali jste nejnovější verzi [PowerShellu](/powershell/azure/install-az-ps). 

## <a name="create-dnn-resource"></a>Vytvořit prostředek DNN 

Prostředek DNN se vytvoří ve stejné skupině clusteru jako SQL Server FCI. Pomocí PowerShellu vytvořte prostředek DNN v rámci skupiny clusterů FCI. 

Následující příkaz prostředí PowerShell přidá prostředek DNN do skupiny clusteru SQL Server FCI s názvem prostředku `<dnnResourceName>` . Název prostředku se používá k jednoznačné identifikaci prostředku. Použijte ten, který vám dává smysl a je v rámci clusteru jedinečný. Typ prostředku musí být `Distributed Network Name` . 

`-Group`Hodnota musí být název skupiny clusterů, která odpovídá SQL Server FCI, do kterého chcete přidat název distribuované sítě. Pro výchozí instanci je typický formát `SQL Server (MSSQLSERVER)` . 


```powershell
Add-ClusterResource -Name <dnnResourceName> `
-ResourceType "Distributed Network Name" -Group "<WSFC role of SQL server instance>"
```

Pokud například chcete vytvořit prostředek DNN `dnn-demo` pro výchozí SQL Server FCI, použijte následující příkaz prostředí PowerShell:

```powershell
Add-ClusterResource -Name dnn-demo `
-ResourceType "Distributed Network Name" -Group "SQL Server (MSSQLSERVER)"

```

## <a name="set-cluster-dnn-dns-name"></a>Nastavit název DNS clusteru DNN

Nastavte název DNS pro prostředek DNN v clusteru. Cluster pak tuto hodnotu použije ke směrování provozu na uzel, který je aktuálně hostitelem SQL Server FCI. 
 
Klienti používají název DNS pro připojení k SQL Server FCI. Můžete vybrat jedinečnou hodnotu. Nebo, pokud už máte existující FCI a nechcete aktualizovat připojovací řetězce klientů, můžete nakonfigurovat DNN na používání aktuálního VNNu, které klienti už používají. K tomu je potřeba před nastavením DNN ve službě DNS [Přejmenovat vnn](#rename-the-vnn) .


Pomocí tohoto příkazu nastavte název DNS pro DNN: 

```powershell
Get-ClusterResource -Name <dnnResourceName> | `
Set-ClusterParameter -Name DnsName -Value <DNSName>
```

`DNSName`Hodnota je to, co klienti používají pro připojení k SQL Server FCI. Například aby se klienti mohli připojit k `FCIDNN` , použijte následující příkaz prostředí PowerShell:

```powershell
Get-ClusterResource -Name dnn-demo | `
Set-ClusterParameter -Name DnsName -Value FCIDNN
```

Klienti budou nyní `FCIDNN` do svého připojovacího řetězce připojovat při připojování k SQL Server FCI. 

   > [!WARNING]
   > Neodstraňujte aktuální název virtuální sítě (VNN), protože se jedná o nezbytnou součást infrastruktury FCI. 


### <a name="rename-the-vnn"></a>Přejmenovat VNN 

Pokud máte existující virtuální síť a chcete, aby klienti tuto hodnotu nadále používali pro připojení k SQL Server FCI, je nutné přejmenovat aktuální VNN na hodnotu zástupného symbolu. Po přejmenování aktuálního VNN můžete nastavit hodnotu názvu DNS pro DNN na VNN. 

Některá omezení platí pro přejmenování VNN. Další informace najdete v tématu [přejmenování FCI](/sql/sql-server/failover-clusters/install/rename-a-sql-server-failover-cluster-instance).

Pokud použití aktuálního VNN není pro vaši firmu nutné, přeskočte tuto část. Po přejmenování VNN [nastavte název DNS clusteru DNN](#set-cluster-dnn-dns-name). 

   
## <a name="set-dnn-resource-online"></a>Nastavit prostředek DNN online

Až bude prostředek DNN správně pojmenovaný a v clusteru jste nastavili hodnotu názvu DNS, použijte PowerShell k nastavení prostředku DNN v clusteru: 

```powershell
Start-ClusterResource -Name <dnnResourceName>
```

Chcete-li například spustit prostředek DNN `dnn-demo` , použijte následující příkaz prostředí PowerShell: 

```powershell
Start-ClusterResource -Name dnn-demo
```

## <a name="configure-possible-owners"></a>Konfigurace možných vlastníků

Ve výchozím nastavení cluster připojí název DNS DNN ke všem uzlům v clusteru. Uzly v clusteru, které nejsou součástí SQL Server FCI, by však měly být vyloučeny ze seznamu DNN možných vlastníků. 

Chcete-li aktualizovat možné vlastníky, použijte následující postup:

1. Přejít na prostředek DNN v Správce clusteru s podporou převzetí služeb při selhání. 
1. Klikněte pravým tlačítkem na prostředek DNN a vyberte **vlastnosti**. 
   :::image type="content" source="media/hadr-distributed-network-name-dnn-configure/fci-dnn-properties.png" alt-text="Místní nabídka pro prostředek DNN s zvýrazněným příkazem Properties":::
1. Zrušte zaškrtnutí políček u všech uzlů, které se nepodílejí v instanci clusteru s podporou převzetí služeb při selhání. Seznam možných vlastníků prostředku DNN by měl odpovídat seznamu možných vlastníků pro prostředek instance SQL Server. Například za předpokladu, že se DATA3 neúčastní v FCI, je následující obrázek příklad odebrání DATA3 ze seznamu možných vlastníků pro prostředek DNN: 

   :::image type="content" source="media/hadr-distributed-network-name-dnn-configure/clear-check-for-nodes-not-in-fci.png" alt-text="Místní nabídka pro prostředek DNN s zvýrazněným příkazem Properties":::

1. Kliknutím na **OK** uložte nastavení. 


## <a name="restart-sql-server-instance"></a>Restartovat instanci SQL Server 

K restartování instance SQL Server použijte Správce clusteru s podporou převzetí služeb při selhání. Postupujte takto:

1. V Správce clusteru s podporou převzetí služeb při selhání přejít na prostředek SQL Server.
1. Klikněte pravým tlačítkem na prostředek SQL Server a převeďte ho do offline režimu. 
1. Až budou všechny přidružené prostředky offline, klikněte pravým tlačítkem na prostředek SQL Server a převeďte ho do online režimu. 

## <a name="update-connection-string"></a>Aktualizovat připojovací řetězec

Chcete-li zajistit rychlé připojení při převzetí služeb při selhání, přidejte `MultiSubnetFailover=True` do připojovacího řetězce, pokud je verze klienta SQL starší než 4.6.1. 

Kromě toho, pokud DNN nepoužívá původní VNN, klienti SQL, kteří se připojují k SQL Server FCI, budou muset aktualizovat připojovací řetězec na DNN název DNS. Chcete-li se tomuto požadavku vyhnout, můžete aktualizovat hodnotu název DNS tak, aby byla názvem VNN. Nejprve však budete muset [nahradit existující vnn zástupným symbolem](#rename-the-vnn) . 

## <a name="test-failover"></a>Testovací převzetí služeb při selhání

Testovací převzetí služeb při selhání v clusterovém prostředku pro ověření funkčnosti clusteru. 

Při testování převzetí služeb při selhání postupujte takto: 

1. Připojte se k jednomu z SQL Server uzlů clusteru pomocí protokolu RDP.
1. Otevřete **Správce clusteru s podporou převzetí služeb při selhání**. Vyberte **role**. Všimněte si, že uzel je vlastníkem role SQL Server FCI.
1. Klikněte pravým tlačítkem na roli SQL Server FCI. 
1. Vyberte **přesunout**a pak vyberte **nejlepší možný uzel**.

**Správce clusteru s podporou převzetí služeb při selhání** zobrazuje roli a její prostředky přejít do režimu offline. Prostředky se pak přesunou a vrátí zpátky do režimu online v druhém uzlu.

## <a name="test-connectivity"></a>Test připojení

Pokud chcete otestovat připojení, přihlaste se k jinému virtuálnímu počítači ve stejné virtuální síti. Otevřete **SQL Server Management Studio** a připojte se k SQL Server FCI pomocí názvu DNS DNN.

Pokud potřebujete, můžete [si stáhnout SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms).

## <a name="avoid-ip-conflict"></a>Vyhnout se konfliktu protokolu IP

Toto je volitelný krok, který zabrání tomu, aby byla virtuální adresa IP (VIP) používaná prostředkem FCI přiřazena k jinému prostředku v Azure jako duplicitní. 

I když se zákazníci nyní používají DNN k připojení k SQL Server FCI, název virtuální sítě (VNN) a virtuální IP adresy nelze odstranit, protože jsou nezbytné součásti infrastruktury FCI. Vzhledem k tomu, že už neexistuje služba Vyrovnávání zatížení, která v Azure zachovává rezervaci virtuální IP adresy, existuje riziko, že by se k jinému prostředku ve virtuální síti přiřadila stejná IP adresa jako virtuální IP adresa, kterou používá FCI. To může potenciálně vést k problému s duplicitními IP konflikty. 

Nakonfigurujte adresu APIPa nebo vyhrazený síťový adaptér pro rezervaci IP adresy. 

### <a name="apipa-address"></a>Adresa APIPa

Pokud se chcete vyhnout použití duplicitních IP adres, nakonfigurujte adresu APIPa (označuje se také jako místní adresa odkazu). Provedete to pomocí následujícího příkazu:

```powershell
Get-ClusterResource "virtual IP address" | Set-ClusterParameter 
    –Multiple @{"Address”=”169.254.1.1”;”SubnetMask”=”255.255.0.0”;"OverrideAddressMatch"=1;”EnableDhcp”=0}
```

V tomto příkazu je "virtuální IP adresa" název prostředku clusterované VIP adresy a "169.254.1.1" je adresa APIPa vybraná pro adresu VIP. Vyberte si adresu, která nejlépe odpovídá vašemu podniku. Nastavte `OverrideAddressMatch=1` , aby se IP adresa nastavila v libovolné síti, včetně adresního prostoru APIPA. 

### <a name="dedicated-network-adapter"></a>Vyhrazený síťový adaptér

Případně můžete nakonfigurovat síťový adaptér v Azure tak, aby vyhradil IP adresu, kterou používá prostředek virtuální IP adresy. Tato adresa však používá adresu v adresním prostoru podsítě a existuje dodatečná režie k tomu, aby se síťový adaptér nepoužíval k žádným jiným účelům.

## <a name="limitations"></a>Omezení

- V současné době je DNN podporován pouze pro SQL Server 2019 CU2 a novější v systému Windows Server 2016. 
- V současné době se DNN podporuje jenom u instancí clusteru s podporou převzetí služeb při selhání s SQL Server na virtuálních počítačích Azure. Pro naslouchací procesy skupiny dostupnosti použijte název virtuální sítě s Azure Load Balancer.
- Pokud pracujete s jinými SQL Server funkcemi a FCI s DNN, může docházet k většímu uvážení. Další informace najdete v tématu [FCI s interoperabilitou DNN](failover-cluster-instance-dnn-interoperability.md). 

## <a name="next-steps"></a>Další kroky

Další informace o funkcích SQL Server HADR v Azure najdete v tématu [skupiny dostupnosti](availability-group-overview.md) a [instance clusteru s podporou převzetí služeb při selhání](failover-cluster-instance-overview.md). Můžete si také přečíst [osvědčené postupy](hadr-cluster-best-practices.md) pro konfiguraci prostředí pro zajištění vysoké dostupnosti a zotavení po havárii. 

Při použití s DNN a FCI můžou existovat další požadavky na konfiguraci některých specifických funkcí SQL Server. Další informace najdete v tématu [FCI with DNN interoperability](failover-cluster-instance-dnn-interoperability.md) . 

