---
title: CloudJednoduchá údržba a aktualizace
titleSuffix: Azure VMware Solution by CloudSimple
description: Popisuje proces služby CloudSimple pro plánovanou údržbu a aktualizace.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 826fae1123b355a4143118b53ba649f0939acaf7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025023"
---
# <a name="cloudsimple-maintenance-and-updates"></a>CloudJednoduchá údržba a aktualizace

Prostředí privátního cloudu je navrženo tak, aby nemělo jediný bod selhání.

* Clustery ESXi jsou konfigurovány s vSphere Vysoká dostupnost (HA). Velikost clusterů má alespoň jeden náhradní uzel pro odolnost proti chybám.
* Redundantní primární úložiště je poskytováno vSAN, který vyžaduje alespoň tři uzly pro ochranu proti jedné chybě. vSAN lze nakonfigurovat tak, aby poskytovalvyšší odolnost pro větší clustery.
* Virtuální počítače vCenter, PSC a NSX Manager jsou nakonfigurovány s úložištěm RAID-10, aby byly chráněny před selháním úložiště. Virtuální počítače jsou chráněny proti selhání uzlu/sítě vSphere HA.
* Hostitelé ESXi mají redundantní ventilátory a síťové karty.
* Přepínače TOR a páteře jsou konfigurovány v párech HA, aby poskytovaly odolnost.

CloudSimple průběžně monitoruje následující virtuální aplikace pro dostupnost a dostupnost a poskytuje služby SLA dostupnosti:

* EsXi hostitelé
* vCenter
* Psc
* NSX manažer

CloudSimple také monitoruje následující nepřetržitě pro poruchy:

* Pevné disky
* Fyzické porty NIC
* Servery
* Fans
* Napájení
* Přepínače
* Přepnutí portů

Pokud disk nebo uzel selže, nový uzel je automaticky přidán do postiženého clusteru VMware, aby se okamžitě vrátil do stavu.

CloudSimple zálohuje, udržuje a aktualizuje tyto prvky VMware v privátních cloudech:

* Esxi
* služby platformy vCenter
* Řadič
* vSAN
* NSX

## <a name="back-up-and-restore"></a>Zálohování a obnovení

CloudSimple zálohování zahrnuje:

* Noční přírůstkové zálohování pravidel vCenter, PSC a DVS.
* nativní api aplikace vCenter pro zálohování součástí v aplikační vrstvě.
* Automatické zálohování před aktualizací nebo upgradem softwaru pro správu voblasti VMware.
* šifrování dat vCenter u zdroje před přenosem dat přes šifrovaný kanál TLS1.2 do Azure. Data se ukládají v objektu blob Azure, kde se replikují napříč oblastmi.

O obnovení můžete požádat otevřením [žádosti o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="maintenance"></a>Údržba

CloudSimple provádí několik typů plánované údržby.

### <a name="backendinternal-maintenance"></a>Údržba backendu/vnitřního

Tato údržba obvykle zahrnuje změnu konfigurace fyzických prostředků nebo instalaci softwarových oprav. Nemá vliv na normální spotřebu obsluhovaných aktiv. Vzhledem k tomu, že redundantní síťové karty přejdou do každého fyzického racku, normální síťový provoz a operace privátního cloudu nejsou ovlivněny. Dopad na výkon můžete zaznamenat pouze v případě, že vaše organizace očekává, že během intervalu údržby bude používat plnou redundantní šířku pásma.

### <a name="cloudsimple-portal-maintenance"></a>Údržba portálu CloudSimple

Některé omezené prostoje služby je vyžadováno při aktualizaci řídicí roviny clouduSimple nebo infrastruktury. V současné době mohou být intervaly údržby stejně časté jako jednou za měsíc. Očekává se, že frekvence bude v průběhu času klesat. CloudSimple poskytuje oznámení pro údržbu portálu a udržuje interval co nejkratší. Během intervalu údržby portálu fungují bez jakéhokoli vtípku následující služby:

* Letadlo a aplikace pro správu společnosti VMware
* vCenter přístup
* Všechny sítě a úložiště
* Veškerý provoz Azure

### <a name="vmware-infrastructure-maintenance"></a>Údržba infrastruktury společnosti VMware

V některých proto je nutné provést změny konfigurace infrastruktury VMware.  V současné době se tyto intervaly mohou vyskytovat každé 1-2 měsíce, ale očekává se, že frekvence v průběhu času klesá. Tento typ údržby lze obvykle provést bez přerušení normální spotřeby cloudsimple služeb. Během intervalu údržby společnosti VMware fungují bez jakéhokoli vtípku bez dopadu následující služby:

* Letadlo a aplikace pro správu společnosti VMware
* vCenter přístup
* Všechny sítě a úložiště
* Veškerý provoz Azure

## <a name="updates-and-upgrades"></a>Aktualizace a upgrady

CloudSimple je zodpovědný za správu životního cyklu softwaru VMware (ESXi, vCenter, PSC a NSX) v privátním cloudu.

Aktualizace softwaru zahrnují:

* **Záplaty**. Opravy zabezpečení nebo opravy chyb vydané společností VMware.
* **Aktualizace**. Dílčí verze změny součásti zásobníku VMware.
* **Inovace**. Hlavní změna verze součásti zásobníku VMware.

CloudSimple testuje kritickou opravu zabezpečení, jakmile bude k dispozici od společnosti VMware. Podle sla, CloudSimple zavádí opravy zabezpečení do prostředí Privátního cloudu během týdne.

CloudSimple poskytuje čtvrtletní aktualizace údržby softwarových komponent VMware. Když je k dispozici nová hlavní verze softwaru VMware, CloudSimple spolupracuje se zákazníky na koordinaci vhodného okna údržby pro upgrade.

## <a name="next-steps"></a>Další kroky

[Zálohování virtuálních virtuálních měn s úlohami pomocí veeamu](backup-workloads-veeam.md)
