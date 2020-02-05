---
title: Řešení Azure VMware (AVS) – údržba a aktualizace služby AVS
description: Popisuje proces služby AVS pro plánovanou údržbu a aktualizace.
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: bf5937183fc20579ecd21aca8543a0a78d4b9ff3
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025023"
---
# <a name="avs-maintenance-and-updates"></a>Údržba a aktualizace pro funkci AVS

Prostředí privátního cloudu pro funkci AVS je navrženo tak, aby nemohlo být jediným bodem selhání.

* Clustery ESXi jsou nakonfigurovány s vysokou dostupností vSphere (HA). Clustery mají velikost alespoň jednoho náhradního uzlu pro odolnost.
* Redundantní primární úložiště poskytuje síti vSAN, což vyžaduje aspoň tři uzly, aby se zajistila ochrana před jediným selháním. Síti vSAN je možné nakonfigurovat tak, aby poskytovala vyšší odolnost proti většímu objemu clusterů.
* Virtuální počítače vCenter, PSC a NSX Manager jsou nakonfigurované s úložištěm RAID-10, které se chrání před selháním úložiště. Virtuální počítače jsou chráněné proti selhání uzlů/sítě pomocí vSphere HA.
* Hostitelé ESXi mají redundantní ventilátory a síťové karty.
* Přepínače pro mandát a hřbet jsou nakonfigurovány ve dvojicích HA za účelem zajištění odolnosti.

Služby AVS nepřetržitě monitorují následující virtuální počítače z provozu a dostupnosti a poskytuje SLA dostupnosti:

* Hostitelé ESXi
* vCenter
* PSC
* NSX Manager

Služby AVS také neustále monitorují následující chyby:

* Pevné disky
* Fyzické porty NIC
* Servery
* Větrák
* Power
* Přepínače
* Porty přepínače

Pokud dojde k chybě disku nebo uzlu, do ovlivněného clusteru VMware se automaticky přidá nový uzel, aby byl okamžitě vrácen do stavu.

Aplikace AVS zálohuje, udržuje a aktualizuje tyto prvky VMware v privátních cloudech AVS:

* ESXi
* Služby platformy vCenter
* Kontrolér
* vSAN
* NSX

## <a name="back-up-and-restore"></a>Zálohování a obnovení

Záloha pro funkci AVS zahrnuje:

* Noční přírůstkové zálohování pravidel vCenter, PSC a DVS.
* nativní rozhraní API vCenter pro zálohování komponent v aplikační vrstvě.
* Automatické zálohování před aktualizací nebo upgradem softwaru pro správu VMware.
* šifrování dat vCenter ve zdroji před přenosem dat přes zašifrovaný kanál TLS 1.2 do Azure. Data se ukládají do objektu blob Azure, kde se replikují napříč oblastmi.

Obnovení si můžete vyžádat otevřením [support Request](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="maintenance"></a>Údržba

Pro funkci AVS existuje několik typů plánované údržby.

### <a name="backendinternal-maintenance"></a>Back-end/interní údržba

Tato údržba obvykle zahrnuje změnu konfigurace fyzických prostředků nebo instalaci oprav softwaru. Neovlivňuje normální spotřebu prostředků, které se obsluhují. Díky redundantním síťovým kartám, které jsou na každém fyzickém stojanu, se neovlivní normální síťové přenosy a operace privátního cloudu AVS Dopad na výkon si můžete všimnout pouze v případě, že vaše organizace očekává použití plné redundantní šířky pásma během intervalu údržby.

### <a name="avs-portal-maintenance"></a>Údržba portálu AVS

Při aktualizaci plochy ovládacího prvku AVS nebo infrastruktury se vyžaduje některá omezená výpadky služeb. V současné době mohou být intervaly údržby stejně časté jako jedenkrát za měsíc. Frekvence se očekává, že se v průběhu času odmítne. AVS poskytuje oznámení pro údržbu portálu a udržuje co nejkratší interval. Během intervalu údržby portálu budou následující služby fungovat bez dopadu:

* Rovina správy VMware a aplikace
* přístup vCenter
* Všechny sítě a úložiště
* Veškerý provoz Azure

### <a name="vmware-infrastructure-maintenance"></a>Údržba infrastruktury VMware

V některých případech je potřeba provést změny v konfiguraci infrastruktury VMware. V současné době se tyto intervaly můžou vyskytnout každých 1-2 měsíců, ale frekvence se očekává, že se v průběhu času odmítne. Tento typ údržby se obvykle dá provést bez přerušení běžné spotřeby služeb AVS. Během intervalu údržby VMware budou následující služby fungovat bez dopadu:

* Rovina správy VMware a aplikace
* přístup vCenter
* Všechny sítě a úložiště
* Veškerý provoz Azure

## <a name="updates-and-upgrades"></a>Aktualizace a upgrady

Službě AVS zodpovídá za správu životního cyklu softwaru VMware (ESXi, vCenter, PSC a NSX) v privátním cloudu služby AVS.

Mezi aktualizace softwaru patří:

* **Opravy**. Opravy zabezpečení nebo opravy chyb vydané VMware.
* **Aktualizace**. Dílčí verze změny komponenty zásobníku VMware.
* **Upgrady**. Hlavní změna verze komponenty zásobníku VMware.

AVS testuje kritickou opravu zabezpečení, jakmile bude k dispozici z VMware. V rámci smlouvy o úrovni služeb (AVS) odvede Oprava zabezpečení pro prostředí privátního cloudu v týdnu.

AVS nabízí čtvrtletní aktualizace softwaru VMware. Pokud je k dispozici nová hlavní verze softwaru VMware, funkce AVS spolupracuje se zákazníky na koordinaci vhodného časového období údržby pro upgrade.

## <a name="next-steps"></a>Další kroky

[Zálohování virtuálních počítačů s úlohou pomocí Veeam](backup-workloads-veeam.md)
