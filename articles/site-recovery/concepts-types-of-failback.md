---
title: Navrácení služeb po obnovení během zotavení po havárii pomocí Azure Site Recovery | Microsoft Docs
description: Tento článek poskytuje přehled různých typů navrácení služeb po obnovení a aspektů, které je potřeba vzít v úvahu při selhání obnovení do místního prostředí během zotavení po havárii pomocí služby Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: raynew
ms.openlocfilehash: c0eaf28f9aeb4050fd35a6036a53e3e91d00f3eb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84691080"
---
# <a name="failback-of-vmware-vms-after-disaster-recovery-to-azure"></a>Navrácení služeb po obnovení virtuálních počítačů VMware po zotavení po havárii do Azure

Po převzetí služeb při selhání do Azure v rámci procesu zotavení po havárii můžete navrátit služby po obnovení do místní lokality. Existují dva různé typy navrácení služeb po obnovení, které lze provést pomocí Azure Site Recovery: 

- Navrácení služeb po obnovení do původního umístění 
- Navrácení služeb po obnovení do alternativního umístění

Pokud dojde k převzetí služeb při selhání virtuálního počítače VMware, můžete navrátit služby po obnovení na stejný zdrojový místní virtuální počítač, pokud stále existuje. V tomto scénáři jsou zpětně replikovány pouze změny. Tento scénář je známý jako **Obnovení původního umístění**. Pokud místní virtuální počítač neexistuje, je scénář **alternativním obnovením umístění**.

> [!NOTE]
> Navrácení služeb po obnovení do původního serveru vCenter a konfiguračního serveru je možné. Nemůžete nasadit nový konfigurační server a znovu ho pomocí něj navrátit. Nemůžete také přidat nový Server vCenter do stávajícího konfiguračního serveru a navrátit služby po obnovení do nového vCenter.

## <a name="original-location-recovery-olr"></a>Obnovení původního umístění (OLR)
Pokud se rozhodnete pro navrácení služeb po obnovení původnímu virtuálnímu počítači, musí být splněny následující podmínky:

* Pokud je virtuální počítač spravovaný serverem vCenter, měl by mít hostitel ESX hlavního cíle přístup k úložišti dat virtuálního počítače.
* Pokud je virtuální počítač na hostiteli ESX, ale nespravuje ho vCenter, musí být pevný disk virtuálního počítače v úložišti dat, ke kterému má hostitel hlavního cíle přístup.
* Pokud je váš virtuální počítač na hostiteli ESX a nepoužívá vCenter, měli byste před opětovnou ochranou dokončit zjišťování hostitele ESX hlavního cíle. To platí i v případě, že dojde k selhání back-fyzických serverů.
* V případě, že už existují disky a jsou připojené k místnímu virtuálnímu počítači, můžete provést navrácení služeb po obnovení do virtuální sítě SAN (síti vSAN) nebo disku, který je založený na mapování nezpracovaných zařízení (RDM).

> [!IMPORTANT]
> Je důležité povolit disk. enableUUID = TRUE, aby během navrácení služeb po obnovení mohla služba Azure Site Recovery identifikovat původní VMDK na virtuálním počítači, do kterého se budou zapisovat probíhající změny. Pokud tato hodnota není nastavená na TRUE, pokusí se služba identifikovat odpovídající místní VMDK, aby se co nejvíce snažil. Pokud se nenalezne pravý VMDK, vytvoří se další disk a data se do něj zapisují.

## <a name="alternate-location-recovery-alr"></a>Obnovení do alternativního umístění (ALR)
Pokud místní virtuální počítač neexistuje před tím, než se znovu zamkne virtuální počítač, scénář se nazývá alternativní obnovení umístění. Pracovní postup opětovné ochrany znovu vytvoří místní virtuální počítač. Tím dojde také k tomu, že dojde k úplnému stažení dat.

* Po navrácení služeb po obnovení do alternativního umístění se virtuální počítač obnoví na stejném hostitele ESX, na kterém je nasazený hlavní cílový server. Úložiště dat, které se používá k vytvoření disku, bude stejné úložiště dat, které bylo vybráno při opětovném zapnutí ochrany virtuálního počítače.
* Můžete navrátit služby po obnovení pouze do systému souborů VMFS (Virtual Machine File System) nebo úložiště dat síti vSAN. Pokud máte RDM, znovu se zabezpečí a navrácení služeb po obnovení nebude fungovat.
* Opětovné zapnutí ochrany zahrnuje jeden velký počáteční přenos dat, který následuje po změnách. Tento proces existuje, protože virtuální počítač neexistuje v místním prostředí. Úplná data je třeba replikovat zpět. Tato reochrana bude taky trvat déle než obnovení původního umístění.
* Nemůžete navrátit služby po obnovení na disky založené na RDM. V úložišti dat VMFS/síti vSAN se dají vytvořit jenom nové disky virtuálních počítačů (VMDK).

> [!NOTE]
> Fyzický počítač, při převzetí služeb při selhání do Azure, se může vrátit zpátky jenom jako virtuální počítač VMware. Tento postup se řídí stejným pracovním postupem jako obnovení do alternativního umístění. Ujistěte se, že jste zjistili aspoň jeden hlavní cílový server a nezbytné hostitele ESX/ESXi, ke kterým se potřebujete navrátit.

## <a name="next-steps"></a>Další kroky

Proveďte [operaci navrácení služeb po obnovení](vmware-azure-failback.md)pomocí tohoto postupu.

