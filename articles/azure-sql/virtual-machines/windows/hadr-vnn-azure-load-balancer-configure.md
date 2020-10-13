---
title: Konfigurace VNN s využitím Azure Load Balancer pro HADR
description: Naučte se konfigurovat nástroj pro vyrovnávání zatížení Azure ke směrování provozu na název virtuální sítě (VNN) pro skupinu dostupnosti nebo instanci clusteru s podporou převzetí služeb při selhání (FCI) s SQL Server na virtuálních počítačích Azure pro zajištění vysoké dostupnosti a zotavení po havárii (HADR).
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
ms.openlocfilehash: 2f36e568603ded5a89f88cf11627a09a5a240fac
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91316983"
---
# <a name="configure-vnn-with-azure-load-balancer-sql-server-on-azure-vms"></a>Konfigurace VNN s využitím Azure Load Balancer (SQL Server na virtuálních počítačích Azure)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

V Azure Virtual Machines clustery pomocí nástroje pro vyrovnávání zatížení uchovávají IP adresy, které se musí nacházet na jednom uzlu clusteru. V tomto řešení služba Vyrovnávání zatížení uchovává IP adresu pro název virtuální sítě (VNN), který používá clusterový prostředek v Azure. 

V tomto článku se naučíte konfigurovat nástroj pro vyrovnávání zatížení pomocí služby Azure Load Balancer. Nástroj pro vyrovnávání zatížení bude směrovat provoz do [naslouchacího procesu](availability-group-overview.md) nebo [instancí clusteru s podporou převzetí služeb při selhání (FCIs)](failover-cluster-instance-overview.md) na virtuálních počítačích Azure pomocí SQL Server pro zajištění vysoké dostupnosti a zotavení po havárii (hadr). 



## <a name="prerequisites"></a>Požadavky

Před dokončením kroků v tomto článku byste už měli mít:

- Bylo rozhodnuto, že Azure Load Balancer je vhodná [možnost připojení pro vaše řešení hadr](hadr-cluster-best-practices.md#connectivity).
- Konfigurace [naslouchacího procesu skupiny dostupnosti](availability-group-overview.md) nebo [instancí clusteru s podporou převzetí služeb při selhání](failover-cluster-instance-overview.md). 
- Nainstalovali jste nejnovější verzi [PowerShellu](/powershell/azure/install-az-ps?view=azps-4.2.0). 


## <a name="create-load-balancer"></a>Vytvoření nástroje pro vyrovnávání zatížení

K vytvoření nástroje pro vyrovnávání zatížení použijte [Azure Portal](https://portal.azure.com) :

1. V Azure Portal přejdete do skupiny prostředků, která obsahuje virtuální počítače.

1. Vyberte **Přidat**. Vyhledejte Azure Marketplace **Load Balancer**. Vyberte **Load Balancer**.

1. Vyberte **Vytvořit**.

1. Nástroj pro vyrovnávání zatížení nastavte pomocí následujících hodnot:

   - **Předplatné**: vaše předplatné Azure.
   - **Skupina prostředků**: Skupina prostředků, která obsahuje vaše virtuální počítače.
   - **Název**: název, který identifikuje Nástroj pro vyrovnávání zatížení.
   - **Oblast**: umístění Azure, které obsahuje vaše virtuální počítače.
   - **Zadejte**: buď Public, nebo Private. K privátnímu nástroji pro vyrovnávání zatížení se dá v rámci virtuální sítě dostat. Většina aplikací Azure může používat privátní Nástroj pro vyrovnávání zatížení. Pokud vaše aplikace potřebuje přístup k SQL Server přímo přes Internet, použijte veřejný Nástroj pro vyrovnávání zatížení.
   - **SKU**: Standard.
   - **Virtuální síť**: stejná síť jako virtuální počítače.
   - **Přiřazení IP adresy**: statické. 
   - **Privátní IP adresa**: IP adresa, kterou jste přiřadili k síťovému prostředku v clusteru.

   Následující obrázek ukazuje uživatelské rozhraní **pro vytvoření nástroje pro vyrovnávání zatížení** :

   ![Nastavení nástroje pro vyrovnávání zatížení](./media/failover-cluster-instance-premium-file-share-manually-configure/30-load-balancer-create.png)
   

## <a name="configure-backend-pool"></a>Konfigurovat back-end fond

1. Vraťte se do skupiny prostředků Azure, která obsahuje virtuální počítače, a Najděte nový nástroj pro vyrovnávání zatížení. Možná budete muset aktualizovat zobrazení skupiny prostředků. Vyberte nástroj pro vyrovnávání zatížení.

1. Vyberte **back-end fondy**a pak vyberte **Přidat**.

1. Přidružte back-end fond ke skupině dostupnosti, která obsahuje virtuální počítače.

1. V části **cílové konfigurace sítě IP**vyberte **virtuální počítač** a zvolte virtuální počítače, které se budou podílet jako uzly clusteru. Nezapomeňte zahrnout všechny virtuální počítače, které budou hostovat FCI nebo skupinu dostupnosti.

1. Výběrem **OK** vytvořte fond back-end.

## <a name="configure-health-probe"></a>Konfigurace sondy stavu

1. V podokně vyrovnávání zatížení vyberte **sondy stavu**.

1. Vyberte **Přidat**.

1. V podokně **Přidat sondu stavu** <span id="probe"> </span> nastavte následující parametry sondy stavu:

   - **Name (název**): název pro sondu stavu.
   - **Protokol**: TCP.
   - **Port**: port, který jste vytvořili v bráně firewall pro sondu stavu [při přípravě virtuálního počítače](failover-cluster-instance-prepare-vm.md#uninstall-sql-server-1). V tomto článku příklad používá port TCP `59999` .
   - **Interval**: 5 sekund.
   - **Prahová hodnota špatného stavu**: 2 po sobě jdoucích selhání.

1. Vyberte **OK**.

## <a name="set-load-balancing-rules"></a>Nastavit pravidla vyrovnávání zatížení

1. V podokně vyrovnávání zatížení vyberte **pravidla vyrovnávání zatížení**.

1. Vyberte **Přidat**.

1. Nastavte parametry pravidla vyrovnávání zatížení:

   - **Name (název**): název pro pravidla vyrovnávání zatížení.
   - **IP adresa front-endu**: IP adresa pro síťový prostředek clusteru SQL Server FCI nebo NASLOUCHACÍHO procesu AG.
   - **Port**: port SQL Server TCP. Výchozí port instance je 1433.
   - **Port back-endu**: stejný port jako hodnota **portu** , když povolíte **plovoucí IP adresu (přímé návratu serveru)**.
   - **Back-end fond**: název back-endu, který jste nakonfigurovali dříve.
   - **Sonda stavu**: sonda stavu, kterou jste nakonfigurovali dříve.
   - **Trvalost relace**: žádné.
   - **Časový limit nečinnosti (minuty)**: 4.
   - **Plovoucí IP adresa (přímá návrat ze serveru)**: povoleno.

1. Vyberte **OK**.

## <a name="configure-cluster-probe"></a>Konfigurace sondy clusteru

Nastavte parametr portu testu clusteru v prostředí PowerShell.

Pokud chcete nastavit parametr portu sondy clusteru, aktualizujte proměnné v následujícím skriptu pomocí hodnot z vašeho prostředí. Odstraňte ze skriptu lomené závorky ( `<` a `>` ).

```powershell
$ClusterNetworkName = "<Cluster Network Name>"
$IPResourceName = "<SQL Server FCI / AG Listener IP Address Resource Name>" 
$ILBIP = "<n.n.n.n>" 
[int]$ProbePort = <nnnnn>

Import-Module FailoverClusters

Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
```

Následující tabulka popisuje hodnoty, které je třeba aktualizovat:


|**Hodnota**|**Popis**|
|---------|---------|
|`Cluster Network Name`| Název clusteru převzetí služeb při selhání systému Windows Server pro síť. V **Správce clusteru s podporou převzetí služeb při selhání**  >  **sítě**klikněte pravým tlačítkem myši na síť a vyberte **vlastnosti**. Správná hodnota je pod **názvem** na kartě **Obecné** .|
|`SQL Server FCI/AG listener IP Address Resource Name`|Název prostředku pro IP adresu naslouchacího procesu SQL Server FCI nebo AG V **Failover Cluster Manager**  >  **rolích**Správce clusteru s podporou převzetí služeb při selhání v rámci role SQL Server FCI v části **název serveru**klikněte pravým tlačítkem na prostředek IP adresy a vyberte **vlastnosti**. Správná hodnota je pod **názvem** na kartě **Obecné** .|
|`ILBIP`|IP adresa interního nástroje pro vyrovnávání zatížení (interního nástroje). Tato adresa je nakonfigurovaná v Azure Portal jako interního nástroje adresa front-endu. To je také adresa IP SQL Server FCI. Můžete ji najít v **Správce clusteru s podporou převzetí služeb při selhání** na stejné stránce vlastností, kde jste našli `<SQL Server FCI/AG listener IP Address Resource Name>` .|
|`nnnnn`|Port testu, který jste nakonfigurovali v testu stavu nástroje pro vyrovnávání zatížení. Nepoužívaný port TCP je platný.|
|SubnetMask| Maska podsítě pro parametr clusteru. Musí se jednat o adresu všesměrového vysílání IP protokolu TCP: `255.255.255.255` .| 


Po nastavení sondy clusteru můžete zobrazit všechny parametry clusteru v prostředí PowerShell. Spusťte tento skript:

```powershell
Get-ClusterResource $IPResourceName | Get-ClusterParameter
```


## <a name="test-failover"></a>Testovací převzetí služeb při selhání


Testovací převzetí služeb při selhání v clusterovém prostředku pro ověření funkčnosti clusteru. 

# <a name="failover-cluster-instance"></a>[Instance clusteru s podporou převzetí služeb při selhání](#tab/fci)

Postupujte následovně:

1. Připojte se k jednomu z SQL Server uzlů clusteru pomocí protokolu RDP.
1. Otevřete **Správce clusteru s podporou převzetí služeb při selhání**. Vyberte **role**. Všimněte si, že uzel je vlastníkem role SQL Server FCI.
1. Klikněte pravým tlačítkem na roli SQL Server FCI. 
1. Vyberte **přesunout**a pak vyberte **nejlepší možný uzel**.

**Správce clusteru s podporou převzetí služeb při selhání** zobrazuje roli a její prostředky přejít do režimu offline. Prostředky se pak přesunou a vrátí zpátky do režimu online v druhém uzlu.



# <a name="ag-listener"></a>[Naslouchací proces AG](#tab/ag)

Postupujte následovně:

1. Otevřete [SQL Server Management Studio)](/sql/ssms/download-sql-server-management-studio-ssms) a připojte se ke svému naslouchacího procesu skupiny dostupnosti. 

1. Rozbalte **skupinu dostupnosti Always On** v **Průzkumník objektů**. 
1. Klikněte pravým tlačítkem na skupinu dostupnosti a vyberte **převzetí služeb při selhání**. 
1. Postupujte podle pokynů průvodce a převzetí služeb při selhání skupinou dostupnosti do sekundární repliky. 

Převzetí služeb při selhání je úspěšné, když repliky přepínají role a synchronizují se. 

---

## <a name="test-connectivity"></a>Test připojení

Pokud chcete otestovat připojení, přihlaste se k jinému virtuálnímu počítači ve stejné virtuální síti. Otevřete **SQL Server Management Studio** a připojte se k názvu SQL Server FCI nebo k naslouchacího procesu skupiny dostupnosti.

>[!NOTE]
>Pokud potřebujete, můžete [si stáhnout SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms).



## <a name="next-steps"></a>Další kroky

Další informace o funkcích SQL Server HADR v Azure najdete v tématu [skupiny dostupnosti](availability-group-overview.md) a [instance clusteru s podporou převzetí služeb při selhání](failover-cluster-instance-overview.md). Můžete si také přečíst [osvědčené postupy](hadr-cluster-best-practices.md) pro konfiguraci prostředí pro zajištění vysoké dostupnosti a zotavení po havárii. 



