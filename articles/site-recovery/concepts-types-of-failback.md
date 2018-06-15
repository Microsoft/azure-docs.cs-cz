---
title: Navrácení služeb po obnovení ve službě Azure Site Recovery | Microsoft Docs
description: Tento článek obsahuje přehled různých typů navrácení služeb po obnovení a upozornění, aby byla považována za při selhání zpátky do místního se službou Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: guaravd
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: rajanki
ms.openlocfilehash: 372a7867b47960338d7a1bf7e646fb9fffbe72e1
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
ms.locfileid: "29803548"
---
# <a name="overview-of-failback"></a>Přehled o navrácení služeb po obnovení

Až budete mít převzetí služeb při selhání do Azure, může selhat zpět do místního webu. Existují dva různé typy navrácení služeb po obnovení, které je možné s Azure Site Recovery: 

- Navrácení služeb po obnovení do původního umístění 
- Selhání zpátky do alternativního umístění

Pokud jste při selhání virtuálního počítače VMware, můžete Pokud můžou selhat zpět do stejného zdrojového místní virtuální počítače stále existuje. V tomto scénáři jsou replikovány pouze změny zpět. Tento scénář se označuje jako **obnovení do původního umístění**. Pokud na místním virtuálním počítači neexistuje, tento scénář je **alternativní umístění obnovení**.

> [!NOTE]
> Můžete pouze navrácení služeb po obnovení do původního vCenter a konfigurační server. Nelze nasadit na nový server konfigurace a selhání zpět jeho použití. Navíc nelze přidat nový vCenter existující konfigurační server a navrácení služeb po obnovení do nové vCenter.

## <a name="original-location-recovery-olr"></a>Obnovení do původního umístění (OLR)
Pokud si zvolíte selhání zpět do původního virtuálního počítače, musí být splněny následující podmínky:

* Pokud je virtuální počítač spravovaný vCenter server, hostitele ESX se hlavní cíl měli přístup k úložišti dat virtuálního počítače.
* Pokud virtuální počítač na hostiteli ESX, ale není spravován nástrojem vCenter, musí být pevný disk virtuálního počítače v úložiště dat, které se hlavní cíl hostitelé mají přístup.
* Pokud virtuální počítač na hostiteli ESX a nepoužívá vCenter, by měl předtím, než můžete znovu nastavte ochranu dokončit zjišťování hostitele ESX hlavního cíle. To platí, pokud po obnovení zpět fyzických serverů, příliš.
* Vám může selhat zpět k virtuální síti SAN (vSAN) nebo disk, který podle nezpracované zařízení mapování (RDM), pokud již existují a jsou připojené k virtuálnímu počítači místní disky.

> [!IMPORTANT]
> Je důležité, aby během navrácení služeb po obnovení, je služba Azure Site Recovery dokáže zjistit původní VMDK na virtuální počítač, na který bude zapsán čekajících změn povolit disk.enableUUID= hodnotu TRUE. Pokud není tato hodnota nastavená na být TRUE, pak služba se pokusí identifikovat odpovídající místní VMDK na kapacita systému dovolí. Pokud není pravé VMDK nalezen, vytvoří další disku a získá k, zapisovat data.

## <a name="alternate-location-recovery-alr"></a>Obnovení do náhradního umístění (ALR)
Pokud virtuální počítač na místě před opětovnou ochranu virtuálního počítače neexistuje, tento scénář nazývá obnovení do alternativního umístění. Pracovní postup opětovné ochrany znovu vytvoří na místním virtuálním počítači. Také to způsobí stahování úplná data.

* Pokud žádnou zpět do alternativního umístění, virtuální počítač je obnovena na stejném hostiteli ESX, na kterém je nasazený hlavní cílový server. Úložiště dat, který slouží k vytvoření disku bude stejné úložiště, který byl vybrán při opětovné povolení ochrany virtuálního počítače.
* Může selhat zpět jenom na systém souborů virtuálního počítače (VMFS) nebo sítě vSAN úložiště. Pokud máte RDM, opětovné ochrany a navrácení služeb po obnovení nebude fungovat.
* Opětovné ochrany zahrnuje jeden velký počáteční přenos dat, za kterým následuje změny. Tento proces existuje, protože virtuální počítač na místní neexistuje. Kompletní datový musí replikovat zpět. Tato opětovné ochrany bude taky trvat déle než obnovení do původního umístění.
* Nelze nepodaří zpět na základě RDM disky. V úložišti dat VMFS nebo síť vSAN může vytvořit pouze nové disky virtuálního počítače (VMDKs).

> [!NOTE]
> Fyzický počítač, při převzetí služeb při selhání do Azure, můžete se nezdařilo zpět pouze jako virtuální počítač VMware. To odpovídá témže pracovním postupu jako alternativní umístění pro obnovení. Ujistěte se, že zjistíte alespoň jeden hlavní cílový server a potřeby hostitelů ESX/ESXi, na které budete muset navrácení služeb po obnovení.

## <a name="next-steps"></a>Další postup

Postupujte podle kroků provést [operace navrácení služeb po obnovení](vmware-azure-failback.md).

