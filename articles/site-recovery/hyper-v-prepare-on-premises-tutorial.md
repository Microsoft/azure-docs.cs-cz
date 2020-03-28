---
title: Příprava na zotavení po havárii virtuálních počítačích Hyper-V do Azure pomocí Azure Site Recovery
description: Zjistěte, jak připravit místní virtuální počítače Hyper-V pro zotavení po havárii do Azure pomocí Azure Site Recovery.
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/12/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: b36e4688ebd6e929a56869a6bb191d98b2f1f432
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239837"
---
# <a name="prepare-on-premises-hyper-v-servers-for-disaster-recovery-to-azure"></a>Příprava místních serverů Hyper-V na zotavení po havárii do Azure

Tento článek popisuje, jak připravit místní infrastrukturu Hyper-V, když chcete nastavit zotavení po havárii hyper-virtuálních počítačů do Azure pomocí [Azure Site Recovery](site-recovery-overview.md).


Toto je druhý kurz v řadě, který ukazuje, jak nastavit zotavení po havárii do Azure pro místní virtuální počítače Hyper-V. V prvním kurzu jsme [nastavili součásti Azure](tutorial-prepare-azure.md) potřebné pro zotavení po havárii Hyper-V.

Co se v tomto kurzu naučíte:

> [!div class="checklist"]
> * Zkontrolujte požadavky na technologie Hyper-V a požadavky v rámci nástroje VMM, pokud jsou hostitelé technologie Hyper-V spravováni službou System Center VMM.
> * Připravte VMM, pokud je to možné.
> * Ověřte přístup k internetu do umístění Azure.
> * Připravte virtuální počítače, abyste k nim měli přístup po převzetí služeb při selhání do Azure.

> [!NOTE]
> Kurzy ukazují nejjednodušší cestu nasazení pro scénář. V rámci možností používají jen výchozí možnosti a neuvádějí všechny varianty nastavení ani všechny cesty. Podrobné pokyny naleznete v článku v části Jak na webu recovery obsah.

## <a name="before-you-start"></a>Než začnete

Ujistěte se, že jste připravili Azure, jak je popsáno v [prvním kurzu v této sérii](tutorial-prepare-azure.md).

## <a name="review-requirements-and-prerequisites"></a>Kontrola požadavků a předpokladů

Ujistěte se, že hostitelé a virtuální měna Hyper-V splňují požadavky.

1. [Ověřte](hyper-v-azure-support-matrix.md#on-premises-servers) požadavky na místní server.
2. [Zkontrolujte požadavky na](hyper-v-azure-support-matrix.md#replicated-vms) virtuální počítače Hyper-V, které chcete replikovat do Azure.
3. Zkontrolujte síť [hostitelů](hyper-v-azure-support-matrix.md#hyper-v-network-configuration)Hyper-V ; a podporu [úložiště](hyper-v-azure-support-matrix.md#hyper-v-host-storage) hostitelů a hostů pro místní hostitele Hyper-V.
4. Zkontrolujte podporované [sítě Azure](hyper-v-azure-support-matrix.md#azure-vm-network-configuration-after-failover), [úložiště](hyper-v-azure-support-matrix.md#azure-storage) a [výpočetní prostředí](hyper-v-azure-support-matrix.md#azure-compute-features) po převzetí služeb při selhání.
5. Místní virtuální počítače, které replikujete do Azure, musí splňovat [Požadavky na virtuální počítače Azure](hyper-v-azure-support-matrix.md#azure-vm-requirements).


## <a name="prepare-vmm-optional"></a>Příprava Nástroje VMM (volitelné)

Pokud jsou hostitelé Technologie Hyper-V spravováni službou VMM, je třeba připravit místní server VMM. 

- Ujistěte se, že server VMM má alespoň jeden cloud s jednou nebo více skupinami hostitelů. Hostitel Hyper-V, na kterém jsou spuštěné virtuální chod, by měl být umístěn v cloudu.
- Připravte server VMM na mapování sítě.

### <a name="prepare-vmm-for-network-mapping"></a>Příprava vmm pro mapování sítě

Pokud používáte VMM, [mapy mapování sítě](site-recovery-network-mapping.md) mezi místními sítěmi virtuálních vyvůzkových virtuálních zařízení VM a virtuálními sítěmi Azure. Mapování zajišťuje, že virtuální počítače Azure jsou připojené ke správné síti, když se vytvoří po převzetí služeb při selhání.

Příprava vmm pro mapování sítě takto:

1. Ujistěte se, že máte [logickou síť VMM,](https://docs.microsoft.com/system-center/vmm/network-logical) která je přidružená ke cloudu, ve kterém jsou umístěni hostitelé Hyper-V.
2. Ujistěte se, že máte [síť virtuálních montovny](https://docs.microsoft.com/system-center/vmm/network-virtual) propojenou s logickou sítí.
3. Ve VMM připojte virtuální zařízení k síti virtuálních zařízení.

## <a name="verify-internet-access"></a>Ověření přístupu k internetu

1. Pro účely kurzu je nejjednodušší konfigurace pro hostitele Hyper-V a server VMM, aby měli přímý přístup k internetu bez použití proxy serveru. 
2. Ujistěte se, že hostitelé technologie Hyper-V a případně server VMM mají přístup k požadovaným adresám URL níže.   
3. Pokud řídíte přístup podle IP adresy, ujistěte se, že:
    - Pravidla brány firewall založené na IP adresách se můžou připojit k [rozsahům IP adres Datového centra Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653)a k portu HTTPS (443).
    - Povolte rozsahy IP adres pro oblast Azure vašeho předplatného.
    
### <a name="required-urls"></a>Povinné adresy URL


[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]


## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Příprava připojení k virtuálním počítačům Azure po převzetí služeb při selhání

Během scénáře převzetí služeb při selhání můžete chtít připojit k replikované místní síti.

Chcete-li se připojit k virtuálním čkům windows pomocí funkce RDP po převzetí služeb při selhání, povolte přístup následujícím způsobem:

1. Pokud chcete mít přístup přes internet, před převzetím služeb při selhání povolte na místním virtuálním počítači protokol RDP. Ujistěte se, že tcp a UDP pravidla jsou přidány pro **veřejný** profil a že RDP je povoleno v **programu Windows Firewall** > **povolené aplikace** pro všechny profily.
2. Pokud chcete mít přístup přes síť VPN typu Site-to-Site, povolte na místním počítači protokol RDP. Rdp by měl být povolen v**aplikacích a funkcích povolené bránou** **Windows Firewall** -> pro **domény a soukromé** sítě.
   Zkontrolujte, že je zásada SAN operačního systému nastavená na **OnlineAll**. [Další informace](https://support.microsoft.com/kb/3031135). Při aktivaci převzetí služeb při selhání by na virtuálním počítači neměly být žádné čekající aktualizace Windows. Pokud existují, nebudete se moci přihlásit k virtuálnímu počítači, dokud se aktualizace nedokončí.
3. Po převzetí služeb při selhání na virtuálním počítači Azure s Windows zkontrolujte **diagnostiku spuštění**, kde se zobrazí snímek obrazovky virtuálního počítače. Pokud se nemůžete připojit, zkontrolujte, že je virtuální počítač spuštěný, a přečtěte si tyto [tipy pro řešení potíží](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

Po převzetí služeb při selhání můžete přistupovat k virtuálním počítačům Azure pomocí stejné IP adresy jako replikovaný místní virtuální počítač nebo jiné IP adresy. [Přečtěte si další informace](concepts-on-premises-to-azure-networking.md) o nastavení adresování IP pro převzetí služeb při selhání.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Nastavení zotavení po havárii na azure pro virtuální počítače S VM](tutorial-hyper-v-to-azure.md)
> [Nastavte zotavení po havárii na virtuální počítače Azure pro Hyper-V v cloudech VMM](tutorial-hyper-v-vmm-to-azure.md)
