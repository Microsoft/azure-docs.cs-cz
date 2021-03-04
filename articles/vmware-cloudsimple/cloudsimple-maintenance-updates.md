---
title: Údržba a aktualizace CloudSimple
titleSuffix: Azure VMware Solution by CloudSimple
description: Popisuje proces služby CloudSimple pro plánovanou údržbu a aktualizace.
author: sharaths-cs
ms.author: dikamath
ms.date: 03/04/2021
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 814a901fba065e36ea3ba9e3a6fe1175365de6c5
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102120704"
---
# <a name="cloudsimple-maintenance-and-updates"></a>Údržba a aktualizace CloudSimple

Prostředí privátního cloudu je navrženo tak, aby nedošlo k žádnému jedinému bodu selhání.

* Clustery ESXi jsou nakonfigurovány s vysokou dostupností vSphere (HA). Clustery mají velikost alespoň jednoho náhradního uzlu pro odolnost.
* Redundantní primární úložiště poskytuje síti vSAN, což vyžaduje aspoň tři uzly, aby se zajistila ochrana před jediným selháním. Síti vSAN je možné nakonfigurovat tak, aby poskytovala vyšší odolnost proti většímu objemu clusterů.
* Virtuální počítače vCenter, PSC a NSX Manager jsou nakonfigurované s úložištěm RAID-10, které se chrání před selháním úložiště. Virtuální počítače jsou chráněné proti selhání uzlů/sítě pomocí vSphere HA.
* Hostitelé ESXi mají redundantní ventilátory a síťové karty.
* Přepínače pro mandát a hřbet jsou nakonfigurovány ve dvojicích HA za účelem zajištění odolnosti.

CloudSimple nepřetržitě sleduje následující virtuální počítače po dobu provozu a dostupnost a poskytuje SLA dostupnosti:

* Hostitelé ESXi
* vCenter
* HESEL
* NSX Manager

CloudSimple také sleduje chyby v nepřetržitém důsledku:

* Pevné disky
* Fyzické porty NIC
* Servery
* Fans
* Napájení
* přepínače,
* Porty přepínače

Pokud dojde k chybě disku nebo uzlu, do ovlivněného clusteru VMware se automaticky přidá nový uzel, aby byl okamžitě vrácen do stavu.

CloudSimple zálohuje, udržuje a aktualizuje tyto prvky VMware v privátních cloudech:

* ESXi
* Služby platformy vCenter
* Controller
* Síti vSAN
* NSX

## <a name="back-up-and-restore"></a>Zálohování a obnovení

CloudSimple Backup zahrnuje:

* Noční přírůstkové zálohování pravidel vCenter, PSC a DVS.
* nativní rozhraní API vCenter pro zálohování komponent v aplikační vrstvě.
* Automatické zálohování před aktualizací nebo upgradem softwaru pro správu VMware.
* šifrování dat vCenter ve zdroji před přenosem dat přes zašifrovaný kanál TLS 1.2 do Azure. Data se ukládají do objektu blob Azure, kde se replikují napříč oblastmi.

Obnovení si můžete vyžádat otevřením [support Request](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="maintenance"></a>Údržba

CloudSimple má několik typů plánované údržby.

### <a name="backendinternal-maintenance"></a>Back-end/interní údržba

Tato údržba obvykle zahrnuje změnu konfigurace fyzických prostředků nebo instalaci oprav softwaru. Neovlivňuje normální spotřebu prostředků, které se obsluhují. Díky redundantním síťovým kartám, které jsou na každém fyzickém stojanu, se neovlivní normální síťové přenosy a privátní cloud. Dopad na výkon si můžete všimnout pouze v případě, že vaše organizace očekává použití plné redundantní šířky pásma během intervalu údržby.

### <a name="cloudsimple-portal-maintenance"></a>Údržba portálu CloudSimple

Při aktualizaci roviny ovládacího prvku CloudSimple nebo infrastruktury se vyžaduje některá omezená výpadky služeb. V současné době mohou být intervaly údržby stejně časté jako jedenkrát za měsíc. Frekvence se očekává, že se v průběhu času odmítne. CloudSimple poskytuje oznámení pro údržbu portálu a udržuje co nejkratší interval. Během intervalu údržby portálu budou následující služby fungovat bez dopadu:

* Rovina správy VMware a aplikace
* přístup vCenter
* Všechny sítě a úložiště
* Veškerý provoz Azure

### <a name="vmware-infrastructure-maintenance"></a>Údržba infrastruktury VMware

V některých případech je potřeba provést změny v konfiguraci infrastruktury VMware.  V současné době se tyto intervaly můžou vyskytnout každých 1-2 měsíců, ale frekvence se očekává, že se v průběhu času odmítne. Tento typ údržby se obvykle dá provést bez přerušení běžné spotřeby služeb CloudSimple Services. Během intervalu údržby VMware budou následující služby fungovat bez dopadu:

* Rovina správy VMware a aplikace
* přístup vCenter
* Všechny sítě a úložiště
* Veškerý provoz Azure

## <a name="updates-and-upgrades"></a>Aktualizace a upgrady

CloudSimple zodpovídá za správu životního cyklu softwaru VMware (ESXi, vCenter, PSC a NSX) v privátním cloudu.

Mezi aktualizace softwaru patří:

* **Opravy**. Opravy zabezpečení nebo opravy chyb vydané VMware.
* **Aktualizace**. Dílčí verze změny komponenty zásobníku VMware.
* **Upgrady**. Hlavní změna verze komponenty zásobníku VMware.

CloudSimple testuje kritickou opravu zabezpečení, jakmile bude k dispozici z VMware. 

CloudSimple poskytuje čtvrtletní aktualizace pro softwarové komponenty VMware. Když je k dispozici nová hlavní verze softwaru VMware, CloudSimple spolupracuje se zákazníky na koordinaci vhodného časového období údržby pro upgrade.

## <a name="next-steps"></a>Další kroky

[Zálohování virtuálních počítačů s úlohou pomocí Veeam](backup-workloads-veeam.md)
