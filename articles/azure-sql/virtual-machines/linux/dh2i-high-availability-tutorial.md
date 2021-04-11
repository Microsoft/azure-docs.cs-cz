---
title: Nastavení skupiny dostupnosti Always On s DH2i DxEnterprise spuštěnou v systému Linux Azure Virtual Machines
description: Použití DH2i DxEnterprise jako Správce clusterů k zajištění vysoké dostupnosti se skupinou dostupnosti v SQL Server on Linux Azure Virtual Machines
ms.date: 03/04/2021
ms.service: virtual-machines-sql
ms.topic: tutorial
author: amvin87
ms.author: amitkh
ms.reviewer: vanto
ms.openlocfilehash: 56002aaa977b94b0fabee4f17343f483706eb77d
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449422"
---
# <a name="tutorial---setup-a-three-node-always-on-availability-group-with-dh2i-dxenterprise-running-on-linux-based-azure-virtual-machines"></a>Kurz – nastavení skupiny dostupnosti AlwaysOn pro tři uzly s DH2i DxEnterprise běžící na platformě Linux Azure Virtual Machines

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

V tomto kurzu se dozvíte, jak SQL Server nakonfigurovat skupinu dostupnosti Always On s DH2i DxEnterprise, která běží na platformě Linux Azure Virtual Machines (VM). 

Další informace o DxEnterprise najdete v tématu [DH2i DxEnterprise](https://dh2i.com/dxenterprise-availability-groups/).

> [!NOTE]
> Společnost Microsoft podporuje přesun dat, skupinu dostupnosti a součásti SQL Server. Kontaktujte DH2i a požádejte o podporu týkající se dokumentace ke clusteru DH2i DxEnterprise pro správu clusteru a kvora.
 

Tento kurz se skládá z následujících kroků:

> [!div class="checklist"]
> * Nainstalujte SQL Server na všechny virtuální počítače Azure, které budou součástí skupiny dostupnosti.
> * Nainstalujte DxEnterprise na všechny virtuální počítače a nakonfigurujte cluster DxEnterprise.
> * Vytvořte virtuální hostitele pro zajištění podpory převzetí služeb při selhání a vysoké dostupnosti, přidejte do skupiny dostupnosti skupinu dostupnosti a databázi.
> * Vytvořte interní nástroj pro vyrovnávání zatížení Azure pro naslouchací proces skupiny dostupnosti (volitelné).
> * Provedení ručního nebo automatického převzetí služeb při selhání.

V tomto kurzu nastavíme cluster DxEnterprise pomocí [uživatelského rozhraní klienta DxAdmin](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-dxadmin-client-ui-quick-start-guide/). Volitelně můžete také nastavit cluster pomocí rozhraní příkazového řádku [DxCLI](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-software-dxcli-guide/) . V tomto příkladu jsme použili čtyři virtuální počítače. Tři z těchto virtuálních počítačů používají Ubuntu 18,04 a jsou součástí clusteru se třemi uzly. Na čtvrtém virtuálním počítači běží Windows 10 s nástrojem DxAdmin ke správě a konfiguraci clusteru.

## <a name="prerequisites"></a>Požadavky

- Vytvořte v Azure čtyři virtuální počítače. Postupujte podle pokynů v tématu [rychlý Start: Vytvoření virtuálního počítače se systémem Linux v Azure Portal](../../../virtual-machines/linux/quick-create-portal.md) článku pro vytvoření virtuálních počítačů založených na systému Linux. Podobně pro vytváření virtuálních počítačů založených na Windows použijte [rychlý Start: Vytvoření virtuálního počítače s Windows v článku Azure Portal](../../../virtual-machines/windows/quick-create-portal.md) .
- Nainstalujte .NET 3,1 na všechny virtuální počítače se systémem Linux, které budou součástí clusteru. Postupujte podle pokynů uvedených [tady](/dotnet/core/install/linux) na základě operačního systému Linux, který zvolíte.
- Vyžaduje se platná licence DxEnterprise se zapnutými funkcemi správy skupin dostupnosti. Další informace najdete v tématu [DxEnterprise bezplatnou zkušební verzi](https://dh2i.com/trial/) o tom, jak můžete získat bezplatnou zkušební verzi.

## <a name="install-sql-server-on-all-the-azure-vms-that-will-be-part-of-the-availability-group"></a>Nainstalujte SQL Server na všechny virtuální počítače Azure, které budou součástí skupiny dostupnosti.

V tomto kurzu nastavujeme tři clustery se systémem Linux, na kterých je spuštěná Skupina dostupnosti. Dodržujte dokumentaci k [instalaci SQL Server na Linux](/sql/linux/sql-server-linux-overview#install) na základě volby platformy Linux. Doporučujeme vám také nainstalovat nástroje pro [SQL Server](/sql/linux/sql-server-linux-setup-tools) pro tento kurz.
 
> [!NOTE]
> Ujistěte se, že operační systém Linux, který si zvolíte, je společná distribuce, která je podporována [DH2i DxEnterprise (viz část minimální požadavky na systém)](https://dh2i.com/wp-content/uploads/DxEnterprise-v20-Admin-Guide.pdf) a [Microsoft SQL Server](/sql/linux/sql-server-linux-release-notes-2019#supported-platforms).
>
> V tomto příkladu používáme Ubuntu 18,04, který podporuje DH2i DxEnterprise i Microsoft SQL Server.

Pro účely tohoto kurzu nebudeme instalovat SQL Server na virtuálním počítači s Windows, protože tento uzel nepatří do clusteru a používá se jenom ke správě clusteru pomocí DxAdmin.

Po dokončení tohoto kroku byste měli mít nástroje SQL Server a [SQL Server](/sql/linux/sql-server-linux-setup-tools) (volitelně) nainstalovány na všechny tři virtuální počítače se systémem Linux, které se budou podílet na skupině dostupnosti.
 
## <a name="install-dxenterprise-on-all-the-vms-and-configure-the-cluster"></a>Nainstalovat DxEnterprise na všechny virtuální počítače a nakonfigurovat cluster

V tomto kroku nainstalujeme DH2i DxEnterprise pro Linux na třech virtuálních počítačích se systémem Linux. Následující tabulka popisuje role, které každý server hraje v clusteru:

| Number of VMs | Role DH2i DxEnterprise | Role repliky skupiny dostupnosti Microsoft SQL Server |
|--|--|--|
| 1 | Uzel clusteru – založený na systému Linux | Primární |
| 1 | Uzel clusteru – založený na systému Linux | Sekundární – synchronní potvrzení |
| 1 | Uzel clusteru – založený na systému Linux | Sekundární – synchronní potvrzení |
| 1 | Klient DxAdmin | NA |


Pokud chcete nainstalovat DxEnterprise na tři uzly se systémem Linux, postupujte podle dokumentace DH2i DxEnterprise v závislosti na operačním systému Linux, který jste si zvolili. Nainstalujte DxEnterprise pomocí některé z metod uvedených níže.

- **Ubuntu**
    - [Úvodní příručka instalace úložiště](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-ubuntu-installation-quick-start-guide/)
    - [Úvodní příručka rozšíření](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-azure-extension-quick-start-guide/)
    - [Úvodní příručka image Marketplace](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-azure-marketplace-image-for-linux-quick-start-guide/)
- **RHEL**
    - [Úvodní příručka instalace úložiště](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-rhel-centos-installation-quick-start-guide/)
    - [Úvodní příručka rozšíření](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-azure-extension-quick-start-guide/)
    - [Úvodní příručka image Marketplace](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-azure-marketplace-image-for-linux-quick-start-guide/)

Pokud chcete nainstalovat jenom nástroj klienta DxAdmin na virtuální počítač s Windows, postupujte podle [uživatelského rozhraní klienta DxAdmin úvodní příručka](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-dxadmin-client-ui-quick-start-guide/).

Po provedení tohoto kroku byste měli mít cluster DxEnterprise vytvořený na virtuálních počítačích se systémem Linux a na klientském počítači se systémem Windows nainstalovaný klient DxAdmin. 

> [!NOTE]
> Můžete také vytvořit cluster se třemi uzly, v němž je jeden z uzlů přidán jako *režim pouze pro konfiguraci*, jak je popsáno [zde](/sql/database-engine/availability-groups/windows/availability-modes-always-on-availability-groups#SupportedAvModes) , chcete-li povolit automatické převzetí služeb při selhání. 

## <a name="create-the-virtual-hosts-to-provide-failover-support-and-high-availability"></a>Vytvoření virtuálních hostitelů pro zajištění podpory převzetí služeb při selhání a vysoké dostupnosti

V tomto kroku vytvoříme virtuálního hostitele, skupinu dostupnosti a pak přidáte databázi s použitím uživatelského rozhraní DxAdmin.   

> [!NOTE]
> Během tohoto kroku se SQL Server instance restartují, aby se povolily Always zapnuto. 

Připojte se k počítači klienta Windows, na kterém běží DxAdmin, a připojte se ke clusteru vytvořenému v kroku výše. Pokud chcete povolit možnost Always On a vytvořit virtuálního hostitele a skupinu dostupnosti, postupujte podle pokynů popsaných v části [skupiny dostupnosti MSSQL s DxAdmin](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-mssql-availability-groups-with-dxadmin-quick-start-guide/) . 

> [!TIP]
> Před přidáním databází se ujistěte, že je databáze vytvořená a zálohovaná na primární instanci SQL Server.  

## <a name="create-the-internal-azure-load-balancer-for-listener-optional"></a>Vytvoření interního nástroje pro vyrovnávání zatížení Azure pro naslouchací proces (volitelné)

V tomto volitelném kroku můžete vytvořit a nakonfigurovat nástroj pro vyrovnávání zatížení Azure, který obsahuje IP adresy pro naslouchací procesy skupiny dostupnosti. Další informace o Azure Load Balancer najdete v [Azure Load Balancer](../../../load-balancer/load-balancer-overview.md). Pokud chcete nakonfigurovat naslouchací proces pro vyrovnávání zatížení Azure a naslouchací proces skupiny dostupnosti pomocí DxAdmin, postupujte podle [Úvodní příručka Azure Load Balancer](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-azure-load-balancer-quick-start-guide/)DxEnterprise.

Po provedení tohoto kroku byste měli mít vytvořeného naslouchacího procesu skupiny dostupnosti a namapovaný na interní nástroj pro vyrovnávání zatížení Azure.

## <a name="test-manual-or-automatic-failover"></a>Ruční test nebo automatické převzetí služeb při selhání

U automatického testu převzetí služeb při selhání můžete pokračovat a uvést primární repliku (vypnout virtuální počítač z Azure Portal). Dojde k replikaci náhlé nedostupnosti primárního uzlu. Očekávané chování:
- Správce clusterů propaguje jednu ze sekundárních replik ve skupině dostupnosti na primární.
- Neúspěšná primární replika se po zálohování automaticky připojí ke clusteru. Správce clusteru ho propaguje do sekundární repliky.

 
Ruční převzetí služeb při selhání můžete provést také pomocí následujících kroků:

1. Připojení ke clusteru přes DxAdmin   
1. Rozšíření virtuálního hostitele pro skupinu dostupnosti
1. Klikněte pravým tlačítkem myši na cílový uzel nebo sekundární repliku a vyberte možnost **Spustit hostování pro člena** a iniciovat převzetí služeb při selhání. 

Další informace o dalších operacích v rámci DxEnterprise najdete v příručce pro [správu DxEnterprise](https://dh2i.com/wp-content/uploads/DxEnterprise-v20-Admin-Guide.pdf) a v [příručce DxEnterprise DxCLI](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-software-dxcli-guide/) .

## <a name="next-steps"></a>Další kroky

- Další informace o [skupinách dostupnosti v systému Linux](/sql/linux/sql-server-linux-availability-group-overview)
- [Rychlý Start: Vytvoření virtuálního počítače se systémem Linux v Azure Portal](../../../virtual-machines/linux/quick-create-portal.md)
- [Rychlý start: Vytvoření virtuálního počítače s Windows na webu Azure Portal](../../../virtual-machines/windows/quick-create-portal.md)
- [Podporované platformy pro SQL Server 2019 na platformě Linux](/sql/linux/sql-server-linux-release-notes-2019#supported-platforms)