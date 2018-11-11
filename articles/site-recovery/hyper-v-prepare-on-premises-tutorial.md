---
title: Příprava místních serverů Hyper-V pro zotavení po havárii virtuálních počítačů Hyper-V do Azure | Dokumentace Microsoftu
description: Zjistěte, jak připravit místní virtuální počítače Hyper-V pro zotavení po havárii do Azure pomocí služby Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 10/28/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 6ea6ee093f85fdc3c74b532c8d4b579d57ccf262
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51242173"
---
# <a name="prepare-on-premises-hyper-v-servers-for-disaster-recovery-to-azure"></a>Příprava místních serverů Hyper-V pro zotavení po havárii do Azure

V tomto kurzu se dozvíte, jak připravit vaši místní infrastrukturu Hyper-V, pokud chcete replikovat virtuální počítače Hyper-V do Azure pro účely zotavení po havárii. Hostitelé Hyper-V je možné spravovat pomocí System Center Virtual Machine Manager (VMM), ale to není nutné.  V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Zkontrolujte požadavky technologie Hyper-V a požadavky na nástroj VMM, pokud je k dispozici.
> * Příprava nástroje VMM, pokud je k dispozici
> * Ověřte přístup k Internetu na umístění Azure
> * Příprava virtuálních počítačů tak, aby jim přístup po převzetí služeb při selhání do Azure

Tento kurz je druhou částí série. Ujistěte se, že jste [nastavili komponenty Azure](tutorial-prepare-azure.md), jak je popsáno v předchozím kurzu.



## <a name="review-requirements-and-prerequisites"></a>Zkontrolujte požadavky a předpoklady

Ujistěte se, že hostitelé Hyper-V a virtuální počítače v souladu s požadavky.

1. [Ověřte](hyper-v-azure-support-matrix.md#on-premises-servers) místní požadavky na server.
2. [Zkontrolujte požadavky na](hyper-v-azure-support-matrix.md#replicated-vms) pro virtuální počítače Hyper-V, které chcete replikovat do Azure.
3. Zkontrolujte hostitele Hyper-V [sítě](hyper-v-azure-support-matrix.md#hyper-v-network-configuration); a hostitele a hostů [úložiště](hyper-v-azure-support-matrix.md#hyper-v-host-storage) podporu pro místní hostitelé Hyper-V.
4. Zkontrolujte podporované [sítě Azure](hyper-v-azure-support-matrix.md#azure-vm-network-configuration-after-failover), [úložiště](hyper-v-azure-support-matrix.md#azure-storage) a [výpočetní prostředí](hyper-v-azure-support-matrix.md#azure-compute-features) po převzetí služeb při selhání.
5. Místní virtuální počítače, které replikujete do Azure, musí splňovat [Požadavky na virtuální počítače Azure](hyper-v-azure-support-matrix.md#azure-vm-requirements).


## <a name="prepare-vmm-optional"></a>Příprava VMM (volitelné)

Pokud hostitele Hyper-V jsou spravovány nástrojem VMM, budete muset připravit na místním serveru VMM. 

- Ujistěte se, že VMM server obsahuje minimálně jeden cloud s jeden nebo více skupin hostitelů. Hostitele Hyper-V, na kterém běží virtuální počítače se musí nacházet v cloudu.
- Příprava serveru VMM pro mapování sítě.

### <a name="prepare-vmm-for-network-mapping"></a>Příprava mapování sítě VMM

Pokud používáte nástroj VMM, [mapování sítě](site-recovery-network-mapping.md) mapování mezi místními sítěmi virtuálních počítačů ve VMM a virtuálním sítím Azure. Mapování zajistí, že virtuální počítače Azure jsou připojené ke správné síti při jejich vytvoření po převzetí služeb při selhání.

Nástroj VMM Příprava na mapování sítě následujícím způsobem:

1. Ujistěte se, že máte [logická síť nástroje VMM](https://docs.microsoft.com/system-center/vmm/network-logical) , která je přidružená cloud, ve kterém jsou umístění hostitelé technologie Hyper-V.
2. Zkontrolujte, že máte [síť virtuálních počítačů](https://docs.microsoft.com/system-center/vmm/network-virtual) propojená na logickou síť.
3. V nástroji VMM připojte virtuální počítače k síti virtuálních počítačů.

## <a name="verify-internet-access"></a>Ověřte přístup k Internetu

1. Pro účely tohoto kurzu je nejjednodušší konfiguraci pro hostitele Hyper-V a VMM server umožňuje mít přímý přístup k Internetu bez použití proxy serveru. 
2. Zajistěte, aby, který je hostitelem technologie Hyper-V a server VMM v případě potřeby můžete přístup k požadované níže uvedených adres URL.   
3. Pokud jste řízení přístupu pomocí IP adresy, ujistěte se, že:
    - Pravidla brány firewall založená na adresu IP se můžete připojit k [Azure rozsahů IP adres Datacentra](https://www.microsoft.com/download/confirmation.aspx?id=41653)a port HTTPS (443).
    - Povolte rozsahy IP adres pro oblast Azure svého předplatného.
    
### <a name="required-urls"></a>Požadované adresy URL


[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]


## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Příprava připojení k virtuálním počítačům Azure po převzetí služeb při selhání

Během scénáře převzetí služeb při selhání můžete připojit k vašich replikované v místní síti.

Pro připojení k virtuálním počítačům s Windows pomocí protokolu RDP po převzetí služeb při selhání, povolit přístup k následujícím způsobem:

1. Pokud chcete mít přístup přes internet, před převzetím služeb při selhání povolte na místním virtuálním počítači protokol RDP. Ujistěte se, že jsou přidaná pravidla TCP a UDP pro **Veřejný** profil a že v části **Brána Windows Firewall** > **Povolené aplikace** je povolený protokol RDP pro všechny profily.
2. Pokud chcete mít přístup přes síť VPN typu Site-to-Site, povolte na místním počítači protokol RDP. Protokol RDP musí být povolený v části **Brána Windows Firewall** -> **Povolené aplikace a funkce** pro **doménovou a privátní** síť.
   Zkontrolujte, že je zásada SAN operačního systému nastavená na **OnlineAll**. [Další informace](https://support.microsoft.com/kb/3031135). Při aktivaci převzetí služeb při selhání by na virtuálním počítači neměly být žádné čekající aktualizace Windows. Jinak se nebudete moci k virtuálnímu počítači přihlásit, dokud se aktualizace nedokončí.
3. Po převzetí služeb při selhání na virtuálním počítači Azure s Windows zkontrolujte **diagnostiku spuštění**, kde se zobrazí snímek obrazovky virtuálního počítače. Pokud se nemůžete připojit, zkontrolujte, že je virtuální počítač spuštěný, a přečtěte si tyto [tipy pro řešení potíží](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

Po převzetí služeb při selhání můžete přístup k virtuálním počítačům Azure pomocí stejné IP adresy jako virtuální počítač replikovaný v místním nebo jinou IP adresu. [Další informace](concepts-on-premises-to-azure-networking.md) o nastavení přidělování IP adres pro převzetí služeb při selhání.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Nastavení zotavení po havárii do Azure pro virtuální počítače Hyper-V](tutorial-hyper-v-to-azure.md)
> [nastavit zotavení po havárii do Azure pro virtuální počítače Hyper-V v cloudech VMM](tutorial-hyper-v-vmm-to-azure.md)
