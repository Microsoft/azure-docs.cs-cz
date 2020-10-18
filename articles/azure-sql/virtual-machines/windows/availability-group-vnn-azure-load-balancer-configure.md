---
title: Konfigurace nástroje pro vyrovnávání zatížení pro naslouchací proces AG VNN
description: Naučte se konfigurovat Azure Load Balancer pro směrování provozu do naslouchacího procesu VNN (Virtual Network Name) pro vaši skupinu dostupnosti s SQL Server na virtuálních počítačích Azure pro zajištění vysoké dostupnosti a zotavení po havárii (HADR).
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
ms.openlocfilehash: a07f0416f26f81e8a2b6d22c79047dc8651bb78c
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/18/2020
ms.locfileid: "92168803"
---
# <a name="configure-load-balancer-for-ag-vnn-listener"></a>Konfigurace nástroje pro vyrovnávání zatížení pro naslouchací proces AG VNN
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

V Azure Virtual Machines clustery pomocí nástroje pro vyrovnávání zatížení uchovávají IP adresy, které se musí nacházet na jednom uzlu clusteru. V tomto řešení má nástroj pro vyrovnávání zatížení uloženou IP adresu pro naslouchací proces virtuální sítě (VNN) pro skupinu dostupnosti Always On (AG). 

V tomto článku se naučíte konfigurovat nástroj pro vyrovnávání zatížení pomocí služby Azure Load Balancer. Nástroj pro vyrovnávání zatížení bude směrovat provoz do [naslouchacího procesu služby Availability Group (AG)](availability-group-overview.md) s SQL Server na virtuálních počítačích Azure pro zajištění vysoké dostupnosti a zotavení po havárii (hadr). 

Pro použití alternativní možnosti připojení pro zákazníky, kteří jsou na SQL Server 2019 CU8 a novějších verzích, zvažte místo pro zjednodušenou konfiguraci a vylepšené převzetí služeb při selhání [naslouchací proces DNN](availability-group-vnn-azure-load-balancer-configure.md) .  



## <a name="prerequisites"></a>Předpoklady

Před dokončením kroků v tomto článku byste už měli mít:

- Bylo rozhodnuto, že Azure Load Balancer je vhodná [možnost připojení pro vaše řešení hadr](hadr-cluster-best-practices.md#connectivity).
- Nakonfigurovali jste [naslouchací proces skupiny dostupnosti](availability-group-overview.md).
- Nainstalovali jste nejnovější verzi [PowerShellu](/powershell/azure/install-az-ps). 


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
   - **IP adresa front-endu**: IP adresa pro SQL Server FCIs nebo síťový prostředek clusteru NASLOUCHACÍHO procesu AG.
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
|`AG listener IP Address Resource Name`|Název prostředku pro IP adresu naslouchacího procesu SQL Server FCI nebo AG V **Failover Cluster Manager**  >  **rolích**Správce clusteru s podporou převzetí služeb při selhání v rámci role SQL Server FCI v části **název serveru**klikněte pravým tlačítkem na prostředek IP adresy a vyberte **vlastnosti**. Správná hodnota je pod **názvem** na kartě **Obecné** .|
|`ILBIP`|IP adresa interního nástroje pro vyrovnávání zatížení (interního nástroje). Tato adresa je nakonfigurovaná v Azure Portal jako interního nástroje adresa front-endu. To je také adresa IP SQL Server FCI. Můžete ji najít v **Správce clusteru s podporou převzetí služeb při selhání** na stejné stránce vlastností, kde jste našli `<AG listener IP Address Resource Name>` .|
|`nnnnn`|Port testu, který jste nakonfigurovali v testu stavu nástroje pro vyrovnávání zatížení. Nepoužívaný port TCP je platný.|
|SubnetMask| Maska podsítě pro parametr clusteru. Musí se jednat o adresu všesměrového vysílání IP protokolu TCP: `255.255.255.255` .| 


Po nastavení sondy clusteru můžete zobrazit všechny parametry clusteru v prostředí PowerShell. Spusťte tento skript:

```powershell
Get-ClusterResource $IPResourceName | Get-ClusterParameter
```


## <a name="test-failover"></a>Testovací převzetí služeb při selhání


Testovací převzetí služeb při selhání v clusterovém prostředku pro ověření funkčnosti clusteru. 


Postupujte následovně:

1. Otevřete [SQL Server Management Studio)](/sql/ssms/download-sql-server-management-studio-ssms) a připojte se ke svému naslouchacího procesu skupiny dostupnosti. 
1. Rozbalte **skupinu dostupnosti Always On** v **Průzkumník objektů**. 
1. Klikněte pravým tlačítkem na skupinu dostupnosti a vyberte **převzetí služeb při selhání**. 
1. Postupujte podle pokynů průvodce a převzetí služeb při selhání skupinou dostupnosti do sekundární repliky. 

Převzetí služeb při selhání je úspěšné, když repliky přepínají role a synchronizují se. 


## <a name="test-connectivity"></a>Test připojení

Pokud chcete otestovat připojení, přihlaste se k jinému virtuálnímu počítači ve stejné virtuální síti. Otevřete **SQL Server Management Studio** a připojte se ke naslouchacího procesu skupiny dostupnosti.

>[!NOTE]
>Pokud potřebujete, můžete [si stáhnout SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms).

## <a name="next-steps"></a>Další kroky

Další informace o funkcích SQL Server HADR v Azure najdete v tématu [skupiny dostupnosti](availability-group-overview.md) a [instance clusteru s podporou převzetí služeb při selhání](failover-cluster-instance-overview.md). Můžete si také přečíst [osvědčené postupy](hadr-cluster-best-practices.md) pro konfiguraci prostředí pro zajištění vysoké dostupnosti a zotavení po havárii. 



