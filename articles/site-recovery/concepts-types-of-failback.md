---
title: Navrácení služeb po obnovení po obnovení po obnovení po selhání pomocí azure site recovery | Dokumenty společnosti Microsoft
description: Tento článek poskytuje přehled různých typů navrácení služeb po obnovení a upozornění, které je třeba vzít v úvahu při selhání zpět do místní ho během zotavení po havárii se službou Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: raynew
ms.openlocfilehash: c0eaf28f9aeb4050fd35a6036a53e3e91d00f3eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281831"
---
# <a name="failback-of-vmware-vms-after-disaster-recovery-to-azure"></a>Navrácení služeb po obnovení virtuálních počítačů VMware po zotavení po havárii do Azure

Po převzetí služeb při selhání azure jako součást procesu zotavení po havárii, můžete obnovit služby při selhání na místní web. Existují dva různé typy navrácení služeb po obnovení, které jsou možné s Azure Site Recovery: 

- Návrat z převzetí služeb při selhání do původního umístění 
- Návrat z převzetí služeb při selhání do jiného umístění

Pokud jste selhali přes virtuální počítač VMware, můžete se vrátit zpět do stejného zdroje místního virtuálního počítače, pokud stále existuje. V tomto scénáři pouze změny jsou replikovány zpět. Tento scénář se označuje jako **obnovení původníumístění**. Pokud místní virtuální počítač neexistuje, scénář je **obnovení alternativní**umístění .

> [!NOTE]
> Můžete pouze zpět na původní vCenter a konfigurační server. Nelze nasadit nový konfigurační server a pomocí jeho použití obnovit jeho použití. Také nelze přidat nový vCenter na existující konfigurační server a navrácení služeb po obnovení do nového virtuálního centra.

## <a name="original-location-recovery-olr"></a>Obnovení původního umístění (OLR)
Pokud se rozhodnete vrátit zpět do původního virtuálního počítače, musí být splněny následující podmínky:

* Pokud je virtuální počítač spravován serverem vCenter, měl by mít hostitel ESX hlavního cíle přístup k úložišti dat virtuálního počítače.
* Pokud je virtuální počítač na hostiteli ESX, ale není spravován vCenter, pak pevný disk virtuálního počítače musí být v úložišti dat, ke kterému má hostitel hlavního cíle přístup.
* Pokud je váš virtuální počítač na hostiteli ESX a nepoužívá vCenter, měli byste dokončit zjišťování hostitele ESX hlavního cíle před opětovným účelem. To platí i v případě, že selhává fyzické servery.
* Můžete znovu navrácení do sítě virtuální ho úložiště (vSAN) nebo na disk, který je založen na mapování nezpracovaných zařízení (RDM), pokud disky již existují a jsou připojeny k místnímu virtuálnímu počítači.

> [!IMPORTANT]
> Je důležité povolit disk.enableUUID= PRAVDA, aby během navrácení služeb po obnovení služba Azure Site Recovery je schopna identifikovat původní VMDK na virtuálním počítači, do kterého budou zapsány čekající změny. Pokud tato hodnota není nastavena na hodnotu TRUE, pak se služba pokusí identifikovat odpovídající místní VMDK na základě maximální úsilí. Pokud není nalezen správný nástroj VMDK, vytvoří další disk a data se na něj zapíší.

## <a name="alternate-location-recovery-alr"></a>Obnovení alternativního umístění (ALR)
Pokud místní virtuální počítač neexistuje před opětovnou ochranou virtuálního počítače, scénář se nazývá obnovení alternativníumístění. Pracovní postup reprotect znovu vytvoří místní virtuální počítač znovu. To také způsobí úplné stažení dat.

* Když se vrátíte do alternativního umístění, virtuální počítač se obnoví na stejného hostitele ESX, na kterém je nasazen hlavní cílový server. Úložiště dat, které se používá k vytvoření disku bude stejné úložiště dat, které bylo vybráno při opětovné ochraně virtuálního počítače.
* Můžete zpět pouze do systému souborů virtuálního počítače (VMFS) nebo vSAN úložiště dat. Pokud máte RDM, reprotect a navrácení služeb po obnovení nebude fungovat.
* Reprotect zahrnuje jeden velký počáteční přenos dat, který následuje změny. Tento proces existuje, protože virtuální počítač neexistuje v místním prostředí. Úplná data musí být replikována zpět. Tato opětovná ochrana bude také trvat déle než původní obnovení umístění.
* Nelze vrátit zpět na disky založené na rdm. V úložišti dat VMFS/vSAN lze vytvořit pouze nové disky virtuálních počítačů (VMDK).

> [!NOTE]
> Fyzický počítač, když se převzetí dat přes Azure, může se nezdaří zpět pouze jako virtuální počítač VMware. To toto následuje stejný pracovní postup jako obnovení alternativní umístění. Ujistěte se, že zjistíte alespoň jeden hlavní cílový server a nezbytné hostitele ESX/ESXi, na které je třeba je vrátit zpět.

## <a name="next-steps"></a>Další kroky

Postupujte podle pokynů k provedení [operace navrácení služeb po selhání](vmware-azure-failback.md).

