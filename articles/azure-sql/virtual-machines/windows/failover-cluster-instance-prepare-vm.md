---
title: Příprava virtuálních počítačů na FCI
description: Příprava virtuálních počítačů Azure na jejich použití s instancí clusteru s podporou převzetí služeb při selhání (FCI) a SQL Server v Azure Virtual Machines.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/02/2020
ms.author: mathoma
ms.openlocfilehash: e5eff13c9ec672937258cf35274d2f5f7bc66f18
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/18/2020
ms.locfileid: "92164240"
---
# <a name="prepare-virtual-machines-for-an-fci-sql-server-on-azure-vms"></a>Příprava virtuálních počítačů na FCI (SQL Server na virtuálních počítačích Azure)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Tento článek popisuje, jak připravit virtuální počítače Azure na jejich použití s instancí clusteru s podporou převzetí služeb při selhání (FCI) SQL Server. Nastavení konfigurace se liší v závislosti na řešení úložiště FCI, takže ověřte, že jste vybrali správnou konfiguraci, která bude vyhovovat vašemu prostředí a firmě. 

Další informace najdete v tématu Přehled [FCI s SQL Server na virtuálních počítačích Azure](failover-cluster-instance-overview.md) a [osvědčených postupech pro clustery](hadr-cluster-best-practices.md). 

## <a name="prerequisites"></a>Předpoklady 

- Předplatné Microsoft Azure. Začněte [zdarma](https://azure.microsoft.com/free/). 
- Doména Windows na virtuálních počítačích Azure nebo v místním datacentru, které se rozšířily do Azure s párováním virtuálních sítí.
- Účet, který má oprávnění k vytváření objektů na virtuálních počítačích Azure a ve službě Active Directory.
- Virtuální síť Azure a podsíť s dostatkem adresního prostoru IP adres pro tyto součásti:
   - Oba virtuální počítače
   - IP adresa clusteru s podporou převzetí služeb při selhání systému Windows
   - IP adresa pro každý FCI
- Služba DNS konfigurovaná na síti Azure odkazuje na řadiče domény.

## <a name="choose-an-fci-storage-option"></a>Výběr možnosti úložiště FCI

Nastavení konfigurace pro virtuální počítač se liší v závislosti na možnosti úložiště, kterou plánujete použít pro instanci clusteru s podporou převzetí služeb při selhání SQL Server. Než připravíte virtuální počítač, přečtěte si [dostupné možnosti úložiště FCI](failover-cluster-instance-overview.md#storage) a vyberte možnost, která nejlépe vyhovuje vašemu prostředí a potřebám firmy. Pak pečlivě vyberte příslušné možnosti konfigurace virtuálních počítačů v celém rámci tohoto článku na základě vašeho výběru úložiště. 

## <a name="configure-vm-availability"></a>Konfigurace dostupnosti virtuálního počítače 

Funkce clusteru s podporou převzetí služeb při selhání vyžaduje, aby virtuální počítače byly umístěné ve [skupině dostupnosti](../../../virtual-machines/linux/tutorial-availability-sets.md) nebo v [zóně dostupnosti](../../../availability-zones/az-overview.md#availability-zones). Pokud zvolíte skupiny dostupnosti, můžete k vyhledání virtuálních počítačů blíže použít [skupiny umístění pro Proximity](../../../virtual-machines/windows/co-location.md#proximity-placement-groups) . Ve skutečnosti jsou skupiny umístění s blízkými předpoklady pro používání sdílených disků Azure. 

Pečlivě vyberte možnost dostupnosti virtuálního počítače, která odpovídá vaší zamýšlené konfiguraci clusteru: 

 - **Sdílené disky Azure**: skupina [dostupnosti](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set) nakonfigurovaná s doménou selhání a aktualizační doménou je nastavená na 1 a umístěna do [skupiny umístění blízkosti](../../../virtual-machines/windows/proximity-placement-groups-portal.md).
 - **Souborové sdílené složky Premium**: [Skupina dostupnosti](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set) nebo [zóna dostupnosti](../../../virtual-machines/windows/create-portal-availability-zone.md#confirm-zone-for-managed-disk-and-ip-address). Sdílené složky Premium jsou jedinou možností sdíleného úložiště, pokud zvolíte zóny dostupnosti jako Konfigurace dostupnosti pro vaše virtuální počítače. 
 - **Prostory úložiště s přímým přístupem**: [Skupina dostupnosti](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set).

>[!IMPORTANT]
>Po vytvoření virtuálního počítače už skupinu dostupnosti nemůžete nastavit ani změnit.

## <a name="create-the-virtual-machines"></a>Vytvoření virtuálních počítačů

Po dokončení konfigurace dostupnosti virtuálního počítače jste připraveni vytvořit virtuální počítače. Můžete použít Azure Marketplace bitovou kopii, která má nebo nemá SQL Server již nainstalována. Pokud ale zvolíte image pro SQL Server na virtuálních počítačích Azure, budete muset před konfigurací instance clusteru s podporou převzetí služeb při selhání odinstalovat SQL Server z virtuálního počítače. 


Umístit oba virtuální počítače:

- Ve stejné skupině prostředků Azure jako vaše skupina dostupnosti, pokud používáte skupiny dostupnosti.
- Ve stejné virtuální síti jako řadič domény.
- V podsíti, která má dostatek adresního prostoru IP adres pro virtuální počítače i všechny FCIs, které můžete nakonec použít na clusteru.
- V rámci skupiny dostupnosti Azure nebo zóny dostupnosti.

Virtuální počítač Azure můžete vytvořit pomocí Image [s](sql-vm-create-portal-quickstart.md) nebo [bez](../../../virtual-machines/windows/quick-create-portal.md) SQL Server předinstalovaného. Pokud zvolíte bitovou kopii SQL Server, bude nutné ručně odinstalovat instanci SQL Server před instalací instance clusteru s podporou převzetí služeb při selhání. 


## <a name="uninstall-sql-server"></a>Odinstalace SQL Server

V rámci procesu vytváření FCI nainstalujete SQL Server jako clusterovou instanci do clusteru s podporou převzetí služeb při selhání. *Pokud jste nasadili virtuální počítač s Azure Marketplaceovou imagí bez SQL Server, můžete tento krok přeskočit.* Pokud jste nasadili bitovou kopii s předinstalovaným SQL Server, bude nutné zrušit registraci SQL Server virtuálního počítače od poskytovatele prostředků virtuálního počítače SQL a poté odinstalovat SQL Server. 

### <a name="unregister-from-the-sql-vm-resource-provider"></a>Zrušení registrace od poskytovatele prostředků virtuálního počítače SQL

SQL Server image virtuálních počítačů z Azure Marketplace se automaticky zaregistrují u poskytovatele prostředků virtuálního počítače SQL. Před odinstalací předinstalované instance SQL Server musíte nejprve [zrušit registraci každého SQL Server virtuálního počítače od poskytovatele prostředků virtuálního počítače SQL](sql-vm-resource-provider-register.md#unregister-from-rp). 

### <a name="uninstall-sql-server"></a>Odinstalace SQL Server

Po zrušení registrace od poskytovatele prostředků můžete odinstalovat SQL Server. Na každém virtuálním počítači proveďte tyto kroky: 

1. Připojte se k virtuálnímu počítači pomocí protokolu RDP.

   Když se poprvé připojíte k virtuálnímu počítači pomocí protokolu RDP, zobrazí se výzva, jestli chcete, aby byl počítač v síti zjistitelný. Vyberte **Ano**.

1. Pokud používáte jednu z imagí virtuálních počítačů založených na SQL Server, odeberte instanci SQL Server:

   1. V části **programy a funkce**klikněte pravým tlačítkem na **Microsoft SQL Server 201_ (64 bitů)** a vyberte **Odinstalovat nebo změnit**.
   1. Vyberte **Odebrat**.
   1. Vyberte výchozí instanci.
   1. Odeberte všechny funkce ve **službě databázového stroje**. V rámci **sdílených funkcí**nic neodstraňujte. Uvidíte něco podobného jako na následujícím snímku obrazovky:

      ![Výběr funkcí](./media/failover-cluster-instance-prepare-vm/03-remove-features.png)

   1. Vyberte **Další**a pak vyberte **Odebrat**.
   1. Po úspěšném odebrání instance restartujte virtuální počítač. 

## <a name="open-the-firewall"></a>Otevřete bránu firewall. 

Na každém virtuálním počítači otevřete port Windows firewallu TCP, který SQL Server používá. Ve výchozím nastavení se jedná o port 1433. Můžete ale změnit port SQL Server v nasazení virtuálního počítače Azure, takže otevřete port, který SQL Server používá ve vašem prostředí. Tento port se automaticky otevře v SQL Server imagí nasazených z Azure Marketplace. 

Pokud používáte [Nástroj pro vyrovnávání zatížení](failover-cluster-instance-vnn-azure-load-balancer-configure.md), budete také muset otevřít port, který sonda stavu používá. Ve výchozím nastavení se jedná o port 59999. Může to ale být libovolný port TCP, který zadáte při vytváření nástroje pro vyrovnávání zatížení. 

Tato tabulka podrobně popisuje porty, které může být potřeba otevřít, v závislosti na konfiguraci FCI: 

   | Účel | Port | Poznámky
   | ------ | ------ | ------
   | SQL Server | TCP 1433 | Normální port pro výchozí instance SQL Server. Pokud jste použili image z Galerie, tento port se automaticky otevře. </br> </br> **Používá**se: všechny konfigurace FCI. |
   | Sonda stavu | TCP 59999 | Libovolný otevřený port TCP. Nakonfigurujte [sondu stavu](failover-cluster-instance-vnn-azure-load-balancer-configure.md#configure-health-probe) nástroje pro vyrovnávání zatížení a cluster, který bude používat tento port. </br> </br> **Používá**: FCI s nástrojem pro vyrovnávání zatížení. |
   | Sdílená složka | UDP 445 | Port, který používá služba sdílení souborů. </br> </br> **Používá se v**: FCI se službou Premium File Share. |

## <a name="join-the-domain"></a>Připojení k doméně

Musíte taky připojit virtuální počítače k doméně. Můžete to provést pomocí [šablony pro rychlý Start](../../../active-directory-domain-services/join-windows-vm-template.md#join-an-existing-windows-server-vm-to-a-managed-domain). 

## <a name="review-storage-configuration"></a>Zkontrolovat konfiguraci úložiště

Virtuální počítače vytvořené z Azure Marketplace se dodávají s připojeným úložištěm. Pokud plánujete nakonfigurovat FCI úložiště pomocí souborů úrovně Premium nebo sdílených disků Azure, můžete připojené úložiště odebrat a ušetřit tak náklady, protože pro instanci clusteru s podporou převzetí služeb při selhání se místní úložiště nepoužívá. Je ale možné použít připojené úložiště pro Prostory úložiště s přímým přístupem řešení FCI, takže je v tomto případě nemůžete odstranit. Projděte si řešení úložiště FCI, abyste zjistili, jestli je odebrání připojeného úložiště optimální pro úspory nákladů. 


## <a name="next-steps"></a>Další kroky

Teď, když jste připravili prostředí virtuálních počítačů, jste připraveni nakonfigurovat instanci clusteru s podporou převzetí služeb při selhání. 

Vyberte jedno z následujících průvodců pro konfiguraci prostředí FCI, které je vhodné pro vaši firmu: 
- [Konfigurace FCI pomocí sdílených disků Azure](failover-cluster-instance-azure-shared-disks-manually-configure.md)
- [Konfigurace FCI se sdílenou složkou prémiových souborů](failover-cluster-instance-premium-file-share-manually-configure.md)
- [Konfigurace FCI pomocí Prostory úložiště s přímým přístupem](failover-cluster-instance-storage-spaces-direct-manually-configure.md)

Další informace najdete v tématu Přehled [FCI s SQL Server na virtuálních počítačích Azure](failover-cluster-instance-overview.md) a [podporovaných konfiguracích hadr](hadr-cluster-best-practices.md). 

Další informace najdete v těchto tématech: 
- [Technologie clusterů Windows](/windows-server/failover-clustering/failover-clustering-overview)   
- [SQL Server instancí clusteru s podporou převzetí služeb při selhání](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
