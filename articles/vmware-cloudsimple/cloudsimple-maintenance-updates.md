---
title: Řešení Azure VMware podle CloudSimple - CloudSimple Údržba a aktualizace
description: Popisuje proces CloudSimple služby plánovanou údržbu a aktualizace
author: sharaths-cs
ms.author: dikamath
ms.date: 04/30/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4dde358f10e9ac5054297ff68a0971404c0dc135
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65160242"
---
# <a name="cloudsimple-maintenance-and-updates"></a>CloudSimple Údržba a aktualizace

Prostředí privátního cloudu je navržené pro mít žádný jediný bod selhání:

* ESXi clustery jsou nakonfigurovány s vysokou dostupností vSphere. Clustery jsou velikosti mít alespoň jeden uzel za chodu pro odolnost proti chybám.
* Síť vSAN, který vyžaduje alespoň tři uzly a zajistit ochranu proti selhání jednoho poskytuje redundantní primárního úložiště. síť vSAN, je možné nakonfigurovat zajistit vyšší odolnost pro větší clustery.
* vCenter, PSČ a NSX Správce virtuálních počítačů jsou konfigurovány pomocí diskového pole RAID 10 úložiště zásad ochrany před selháním úložiště. Virtuální počítače jsou chráněny před selháním uzlu/network vSphere vysokou dostupnost.
* Hostitelé ESXi mít záložní ventilátory a síťové adaptéry.
* Přepínače TOR a páteřní. Pokud jsou nakonfigurované v párech HA zajistit odolnost proti chybám.

CloudSimple nepřetržitě monitoruje následující virtuální počítače týkající se dostupnosti a dostupnosti a poskytuje smluv o úrovni služeb:

* Hostitelé ESXi
* vCenter
* PSC
* NSX správce

CloudSimple také sleduje nepřetržitě následující chyby:

* Pevné disky
* Fyzických portů se síťovými Kartami
* Servery
* Fanoušků.
* Napájení
* Přepínače
* Porty přepínačů

Pokud disku nebo uzlu selže, je ovlivněné cluster VMware okamžitě ho přenést zpět do stavu automaticky přidán nový uzel.

CloudSimple zálohuje, udržuje a aktualizuje tyto prvky VMware v privátních cloudů:

* ESXi
* vCenter služby platformy
* Kontrolér
* vSAN
* NSX

## <a name="back-up-and-restore"></a>Zálohování a obnovení

CloudSimple záloha zahrnuje:

* Noční přírůstkové zálohování serveru vCenter, PSČ a DVS pravidla.
* Použití vCenter nativních rozhraní API pro zálohování součásti v aplikační vrstvě.
* Automatické zálohování před všechny aktualizace nebo upgradu software pro správu VMware.
* Šifrování dat ve zdroji, přes vCenter, než se přenos dat přes zašifrovaný kanál TLS1.2 do Azure. Jsou data uložená v objektu blob Azure, ve kterém se replikují napříč oblastmi.

Můžete požádat o obnovení tak, že otevřete [žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="maintenance"></a>Údržba

CloudSimple provede několik typů plánované údržby.

### <a name="backendinternal-maintenance"></a>Back-end/interní údržby

Tato údržba obvykle zahrnuje znovu konfigurovat fyzické prostředky nebo instalaci oprav softwaru. Neovlivní normální spotřeba prostředků probíhá údržba. Pomocí redundantních síťových adaptérů na každý fyzický rack nejsou ovlivněny běžné síťový provoz a operace privátního cloudu. Můžete si všimnout dopad na výkon jenom v případě, že se očekává, že vaše organizace používat plně redundantní šířky pásma během intervalu údržby.

### <a name="cloudsimple-portal-maintenance"></a>CloudSimple portálu údržby

Při aktualizaci rovina řízení CloudSimple nebo infrastruktury, je nutná odstávka některé omezené služby. V současné době může být intervaly údržby tak často jako jednou za měsíc. Frekvence se očekává tak, aby odmítal v čase. CloudSimple poskytuje oznámení o údržbě portálu a udržuje interval co nejkratší. Během intervalu údržby portálu i tyto služby nadále fungovat bez jakéhokoli dopadu:

* Rovina správy VMware a aplikací
* přístup k serveru vCenter
* Všechny sítě a úložiště
* Všem službám Azure

### <a name="vmware-infrastructure-maintenance"></a>Údržba infrastruktury VMware

Někdy je potřeba provést změny v konfiguraci infrastruktury VMware.  V současné době tyto intervaly může dojít, každé 1-2 měsíce, ale očekává se frekvence tak, aby odmítal v čase. Tento typ údržby obvykle provést bez přerušení normálního využití CloudSimple služeb. Během intervalu údržby VMware tyto služby nadále schopné fungovat bez jakéhokoli dopadu:

* Rovina správy VMware a aplikací
* přístup k serveru vCenter
* Všechny sítě a úložiště
* Všem službám Azure

## <a name="updates-and-upgrades"></a>Aktualizace a upgrady

CloudSimple zodpovídá za správu životního cyklu softwaru VMware (ESXi, vCenter, PSČ a NSX) v privátním cloudu.

Softwarové aktualizace zahrnují:

* **Opravy**. Opravy zabezpečení nebo opravy chyb vydání ve VMware.
* **Aktualizace**. Vedlejší verze aktualizace změnit součásti zásobníku VMware.
* **Upgrady**. Změna hlavní verze součásti zásobníku VMware.

CloudSimple testuje opravy zabezpečení, jakmile bude k dispozici z VMware. Podle smlouvy SLA CloudSimple zahrnuje navýšení kapacity na úroveň opravy zabezpečení pro prostředí privátních cloudů v rámci jednoho týdne.

CloudSimple poskytuje čtvrtletní údržby aktualizace softwarových součástí VMware. Pokud je dostupná nová hlavní verze softwaru VMware, CloudSimple funguje se zákazníky ke koordinaci časového období údržby vhodné k upgradu.

## <a name="next-steps"></a>Další postup

[Zálohování virtuálních počítačů pracovního vytížení pomocí Veeam](https://docs.azure.cloudsimple.com/backup-workloads-veeam/).