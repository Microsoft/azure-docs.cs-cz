---
title: Navrácení služeb po obnovení během zotavení po havárii pomocí Azure Site Recovery | Dokumentace Microsoftu
description: Tento článek obsahuje přehled různých typů navrácení služeb po obnovení a upozornění, aby bylo považováno za při selhání zpátky do místního během zotavení po havárii pomocí služby Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 05/19/2019
ms.author: raynew
ms.openlocfilehash: 1e5dc91018df822c72381e4a162c5af5d74ed83c
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/30/2019
ms.locfileid: "66399475"
---
# <a name="failback-after-disaster-recovery-of-vmware-vms"></a>Navrácení služeb po obnovení po havárii virtuálních počítačů VMware

Poté, co jste převzetí služeb při selhání do Azure jako součást procesu obnovení po havárii, můžete navrátit služby zpět do místní lokality. Existují dva různé typy navrácení služeb po obnovení, které je možné pomocí Azure Site Recovery: 

- Navrácení služeb po obnovení do původního umístění 
- Navrácení služeb po obnovení alternativního umístění

Pokud jste převzetí služeb při selhání virtuálního počítače VMware, můžete navrátit služby zpět do stejného zdrojového virtuálního počítače v místním Pokud stále existuje. V tomto scénáři se replikují jenom změny zpět. Tento scénář se označuje jako **obnovení do původního umístění**. Pokud na místním virtuálním počítači buď neexistuje, tento scénář je **obnovení do alternativního umístění**.

> [!NOTE]
> Můžete předat jenom zpět na původní vCenter a konfigurační server. Nelze nasadit nové konfigurační server a selhání, zpět pomocí. Navíc nelze přidat nový vCenter na existující konfigurační server a navrácení služeb po obnovení do nového serveru vCenter.

## <a name="original-location-recovery-olr"></a>Původní umístění (Olr)
Pokud se rozhodnete selhání zpět na původní virtuální počítač, musí být splněny následující podmínky:

* Pokud virtuální počítač je spravován serverem vCenter, hostitel ESX hlavního cílového by měl mít přístup k úložišti dat virtuálního počítače.
* Pokud virtuální počítač na hostitele ESX, ale není spravované přes vCenter, musí být pevný disk virtuálního počítače v úložišti dat, s přístupem k hostiteli hlavního cíle.
* Pokud virtuální počítač na hostitele ESX a nepoužívá vCenter, by měl předtím, než můžete znovu nastavit ochranu dokončit zjišťování hostitele ESX hlavního cíle. To platí, pokud přebíráte obnovení fyzických serverů, příliš.
* Můžete navrátit služby zpět do virtuální sítě SAN (vSAN) nebo disk, který podle mapování (RDM), pokud už existují a jsou připojené k virtuálnímu počítači s místními disky nezpracované zařízení.

> [!IMPORTANT]
> Je třeba povolit disk.enableUUID= hodnotu TRUE, aby během navrácení služeb po obnovení, služba Azure Site Recovery je schopen identifikovat původní VMDK na virtuálním počítači, do kterého budou zapsány čekající změny. Pokud tato hodnota není nastavená na být TRUE, pak služba pokusí se určit odpovídající místní VMDK na jak kapacita systému dovolí. Pokud není nalezen správný VMDK, vytvoří další disk a ke, který získá zapsat data.

## <a name="alternate-location-recovery-alr"></a>Obnovení do alternativního umístění (ALR)
Pokud na místním virtuálním počítači před znovu se zapíná ochrana virtuálního počítače neexistuje, tento scénář je volána obnovení do alternativního umístění. Pracovní postup opětovné zapnutí ochrany vytvoří místní virtuální počítač znovu. Také to způsobí stažení úplná data.

* Když převezmete služby zpět do alternativního umístění, virtuální počítač bude obnoven do stejného hostitele ESX, na kterém je nasazený na hlavní cílový server. Úložiště dat, který se používá k vytvoření disku budou stejné úložiště, který byl vybrán při znovu se zapíná ochrana virtuálního počítače.
* Můžete předat zpět pouze na systém souborů virtuálního počítače (VMFS) nebo úložiště dat sítě vSAN. Pokud máte RDM operace opětovného zapnutí ochrany a navrácení služeb po obnovení nebude fungovat.
* Operace opětovného zapnutí ochrany zahrnuje jeden velký počáteční přenos dat, který je následován změny. Tento proces existuje, protože virtuální počítač neexistuje v místním prostředí. Úplná data musí replikovat zpět. Tato operace opětovného zapnutí ochrany bude taky trvat déle než obnovení do původního umístění.
* Nemůžete po obnovení zpět na disky RDM založené. Nový virtuální počítač disky (Vmdk) můžete vytvořit pouze na úložiště dat VMFS/síť vSAN.

> [!NOTE]
> Fyzický počítač, při převzetí služeb při selhání do Azure, můžete se nepodařilo zálohovat pouze jako virtuální počítač VMware. To se řídí stejného pracovního postupu jako obnovení do alternativního umístění. Ujistěte se, že zjistíte alespoň jeden hlavní cílový server a nezbytné hostitelů ESX/ESXi na které je potřeba navrácení služeb po obnovení.

## <a name="next-steps"></a>Další postup

Postupujte podle kroků provést [operace navrácení služeb po obnovení](vmware-azure-failback.md).

