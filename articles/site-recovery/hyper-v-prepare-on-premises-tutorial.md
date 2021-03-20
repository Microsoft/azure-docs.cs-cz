---
title: Příprava na zotavení po havárii virtuálních počítačů Hyper-V do Azure pomocí Azure Site Recovery
description: Přečtěte si, jak připravit místní virtuální počítače Hyper-V pro zotavení po havárii do Azure pomocí Azure Site Recovery.
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/12/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 6f24a259d2d71aa6599f6dd417d5e9fc99734e99
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "86135656"
---
# <a name="prepare-on-premises-hyper-v-servers-for-disaster-recovery-to-azure"></a>Příprava místních serverů Hyper-V na zotavení po havárii do Azure

Tento článek popisuje, jak připravit místní infrastrukturu technologie Hyper-V, když chcete nastavit zotavení po havárii Hyper-VMs do Azure pomocí [Azure Site Recovery](site-recovery-overview.md).


Toto je druhý kurz v řadě, ve kterém se dozvíte, jak nastavit zotavení po havárii do Azure pro místní virtuální počítače Hyper-V. V prvním kurzu [nastavíme komponenty Azure](tutorial-prepare-azure.md) potřebné pro zotavení po havárii technologie Hyper-V.

Co se v tomto kurzu naučíte:

> [!div class="checklist"]
> * Zkontrolujte požadavky technologie Hyper-V a požadavky VMM, pokud jsou hostitelé Hyper-V spravováni nástrojem System Center VMM.
> * Pokud je to možné, připravte VMM.
> * Ověřte přístup k Internetu do umístění Azure.
> * Připravte virtuální počítače, abyste k nim měli přístup po převzetí služeb při selhání do Azure.

> [!NOTE]
> Kurzy ukazují nejjednodušší cestu k nasazení scénáře. V rámci možností používají jen výchozí možnosti a neuvádějí všechny varianty nastavení ani všechny cesty. Podrobné pokyny najdete v článku v části jak Site Recovery obsahu.

## <a name="before-you-start"></a>Než začnete

Ujistěte se, že jste připravili Azure, jak je popsáno v [prvním kurzu této série](tutorial-prepare-azure.md).

## <a name="review-requirements-and-prerequisites"></a>Kontrola požadavků a předpokladů

Zajistěte, aby hostitelé a virtuální počítače Hyper-V splňovali požadavky.

1. [Ověřte](hyper-v-azure-support-matrix.md#on-premises-servers) požadavky na místní server.
2. [Ověřte požadavky](hyper-v-azure-support-matrix.md#replicated-vms) na virtuální počítače Hyper-V, které chcete replikovat do Azure.
3. Kontrolovat [sítě](hyper-v-azure-support-matrix.md#hyper-v-network-configuration)hostitele Hyper-V; a podpora [úložiště](hyper-v-azure-support-matrix.md#hyper-v-host-storage) hosta a hosta pro místní hostitele Hyper-V.
4. Zkontrolujte podporované [sítě Azure](hyper-v-azure-support-matrix.md#azure-vm-network-configuration-after-failover), [úložiště](hyper-v-azure-support-matrix.md#azure-storage) a [výpočetní prostředí](hyper-v-azure-support-matrix.md#azure-compute-features) po převzetí služeb při selhání.
5. Místní virtuální počítače, které replikujete do Azure, musí splňovat [Požadavky na virtuální počítače Azure](hyper-v-azure-support-matrix.md#azure-vm-requirements).


## <a name="prepare-vmm-optional"></a>Příprava VMM (volitelné)

Pokud jsou hostitelé Hyper-V spravováni nástrojem VMM, je nutné připravit místní server VMM. 

- Ujistěte se, že server VMM má minimálně jeden Cloud s jednou nebo více skupinami hostitelů. Hostitel Hyper-V, na kterém běží virtuální počítače, by měl být umístěný v cloudu.
- Připravte server VMM pro mapování sítě.

### <a name="prepare-vmm-for-network-mapping"></a>Příprava VMM pro mapování sítě

Pokud používáte VMM, [mapování sítě](./hyper-v-vmm-network-mapping.md) se mapují mezi místní sítě virtuálních počítačů VMM a virtuální sítě Azure. Mapování zajišťuje, že se virtuální počítače Azure připojí ke správné síti při jejich vytvoření po převzetí služeb při selhání.

Připravte VMM na mapování sítě následujícím způsobem:

1. Ujistěte se, že máte [logickou síť VMM](/system-center/vmm/network-logical) , která je přidružená ke cloudu, ve kterém jsou umístění hostitelé Hyper-V.
2. Ujistěte se, že máte [síť virtuálních počítačů](/system-center/vmm/network-virtual) propojenou s logickou sítí.
3. V nástroji VMM Připojte virtuální počítače k síti virtuálních počítačů.

## <a name="verify-internet-access"></a>Ověřit přístup k Internetu

1. Pro účely tohoto kurzu je nejjednodušší konfigurace pro hostitele Hyper-V a server VMM, aby měl přímý přístup k Internetu bez použití proxy serveru. 
2. Ujistěte se, že hostitelé Hyper-V a server VMM mají v případě potřeby přístup k požadovaným adresám URL uvedeným níže.   
3. Pokud ovládáte přístup pomocí IP adresy, ujistěte se, že:
    - Pravidla brány firewall založená na IP adresách se můžou připojovat k [rozsahům IP datacenter Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653)a portu HTTPS (443).
    - Povolte rozsahy IP adres pro oblast Azure svého předplatného.
    
### <a name="required-urls"></a>Požadované adresy URL


[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]


## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Příprava připojení k virtuálním počítačům Azure po převzetí služeb při selhání

Během scénáře převzetí služeb při selhání se můžete chtít připojit ke své replikované místní síti.

Pokud se chcete po převzetí služeb při selhání připojit k virtuálním počítačům s Windows, povolte přístup následujícím způsobem:

1. Pokud chcete mít přístup přes internet, před převzetím služeb při selhání povolte na místním virtuálním počítači protokol RDP. Ujistěte se, že jsou přidaná pravidla TCP a UDP pro **veřejný** profil a že protokol RDP je povolený v povolených aplikacích **brány Windows Firewall**  >   pro všechny profily.
2. Pokud chcete mít přístup přes síť VPN typu Site-to-Site, povolte na místním počítači protokol RDP. Protokol RDP by měl být povolený v **bráně Windows Firewall**  ->  **povolené aplikace a funkce** pro **domény a privátní** sítě.
   Zkontrolujte, že je zásada SAN operačního systému nastavená na **OnlineAll**. [Další informace](https://support.microsoft.com/kb/3031135). Při aktivaci převzetí služeb při selhání by na virtuálním počítači neměly být žádné čekající aktualizace Windows. V takovém případě se k virtuálnímu počítači nebudete moct přihlásit, dokud se aktualizace nedokončí.
3. Po převzetí služeb při selhání na virtuálním počítači Azure s Windows zkontrolujte **diagnostiku spuštění**, kde se zobrazí snímek obrazovky virtuálního počítače. Pokud se nemůžete připojit, zkontrolujte, že je virtuální počítač spuštěný, a přečtěte si tyto [tipy pro řešení potíží](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

Po převzetí služeb při selhání získáte přístup k virtuálním počítačům Azure pomocí stejné IP adresy jako replikovaný místní virtuální počítač nebo jiná IP adresa. [Přečtěte si další informace](concepts-on-premises-to-azure-networking.md) o nastavení adresování IP pro převzetí služeb při selhání.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Nastavení zotavení po havárii do Azure pro virtuální počítače](./hyper-v-azure-tutorial.md) 
>  Hyper-V [Nastavení zotavení po havárii do Azure pro virtuální počítače Hyper-V v cloudech VMM](./hyper-v-vmm-azure-tutorial.md)
