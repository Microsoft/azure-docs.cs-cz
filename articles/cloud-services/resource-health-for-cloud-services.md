---
title: Resource Health pro Cloud Services (Classic)
description: Tento článek pojednává o podpoře Resource Health (RHC) pro Microsoft Azure Cloud Services (Classic).
services: cloud-services
author: tanmaygore
ms.service: cloud-services
ms.topic: article
ms.date: 9/1/2020
ms.author: tagore
ms.openlocfilehash: ea25695ddc36571bef3ff61df7de3e71f6f939ca
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/13/2020
ms.locfileid: "90056046"
---
# <a name="resource-health-check-rhc-support-for-azure-cloud-services-classic"></a>Podpora Resource Health (RHC) pro Azure Cloud Services (Classic)
Tento článek pojednává o podpoře Resource Health (RHC) pro [Microsoft Azure Cloud Services (Classic)](https://azure.microsoft.com/services/cloud-services) .

[Azure Resource Health](https://docs.microsoft.com/azure/service-health/resource-health-overview) pro Cloud Services pomáhá diagnostikovat a získat podporu pro problémy se službami, které mají vliv na nasazení cloudové služby, role & instance rolí. Informuje o aktuálním a minulém stavu vašich cloudových služeb při nasazení, rolích & úrovni instance role.

Stav Azure hlásí problémy, které mají vliv na širokou škálu zákazníků Azure. Resource Health poskytuje přizpůsobený řídicí panel stavu vašich prostředků. Resource Health zobrazuje všechny časy nedostupnosti vašich prostředků z důvodu problémů se službami Azure. Tato data vám umožní snadno zjistit, jestli došlo k porušení smlouvy SLA.

:::image type="content" source="media/cloud-services-allocation-failure/rhc-blade-cloud-services.png" alt-text="Image zobrazuje okno pro kontrolu stavu prostředků v Azure Portal.":::

## <a name="how-health-is-checked-and-reported"></a>Jak se kontroluje a hlásí stav?
Stav prostředku se oznamuje na úrovni nasazení nebo role. K kontrole stavu dochází na úrovni instance role, agregujeme stav a nahlásíme ho na úrovni role. Například Pokud jsou k dispozici všechny instance rolí, je stav role k dispozici. Podobně agregujeme stav všech rolí a nahlásíme je na úrovni nasazení. Například Pokud jsou k dispozici všechny role, bude stav nasazení k dispozici. 

## <a name="why-i-cannot-see-health-status-for-my-staging-slot-deployment"></a>Proč mi nejde zobrazit stav pro nasazení přípravného slotu?
Kontroly stavu prostředků fungují jenom pro nasazení produkčního slotu. Nasazení přípravného slotu ještě není podporované. 

## <a name="does-resource-health-check-also-check-the-health-of-the-application"></a>Kontroluje Resource Health taky stav aplikace?
Ne, u instancí rolí dochází pouze k kontrole stavu a nemonitorují stav aplikací. Například I když je u 1 z 3 instancí rolí stav není v pořádku, může být aplikace stále k dispozici. RHC nepoužívá [sondy pro vyrovnávání zatížení](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview) ani test hostovaného agenta. Proto by zákazníci měli i nadále používat sondy vyrovnávání zatížení a monitorovat tak stav svých aplikací. 

## <a name="what-are-the-annotations-for-cloud-services"></a>Jaké jsou poznámky k Cloud Services?
Poznámky jsou stavem nasazení nebo rolí. Existují různé poznámky na základě stavu, důvodu změny stavu atd. 

## <a name="what-does-it-mean-by-role-instance-being-unavailable"></a>Co to znamená, že instance role je nedostupná?
To znamená, že instance role nevysílá signál v pořádku na platformu. Podrobnější vysvětlení stavu instance role najdete v podrobnostech o tom, proč se signál v pořádku nevysílá.

## <a name="what-does-it-mean-by-deployment-being-unknown"></a>Co to znamená – nasazení je "neznámé"?
Neznámý znamená, že agregovaný stav nasazení cloudové služby se nedá určit. To obvykle znamená, že se pro cloudovou službu nevytvořilo žádné výrobní nasazení, nasazení bylo nově vytvořeno (a Azure začíná shromažďovat události stavu) nebo platforma má problémy s shromažďováním událostí stavu pro toto nasazení.

## <a name="why-does-role-instance-annotations-mentions-vms-instead-of-role-instances"></a>Proč poznámky instance role zmiňují virtuální počítače místo instancí rolí?
Vzhledem k tomu, že instance rolí jsou v podstatě virtuální počítače a pro instance role se znovu používají kontroly stavu virtuálních počítačů, termín pro virtuální počítač se používá k reprezentaci instancí rolí. 

## <a name="cloud-services-deployment-level-annotations--their-meanings"></a>Cloud Services (úroveň nasazení) poznámky & jejich významy
| Poznámka | Popis | 
| --- | --- | 
| K dispozici| Toto nasazení cloudové služby nemá vliv na žádné známé problémy s platformou Azure. |
| Neznámý | V tuto chvíli nemůžeme určit stav tohoto nasazení cloudové služby. | 
| Nastavení Resource Health | Nastavuje se Resource Health pro tento prostředek. Resource Health sleduje vaše prostředky Azure a poskytuje podrobnosti o probíhajících a minulých událostech, které na ně měly vliv.|
| Snížený výkon | Vaše nasazení cloudové služby má snížený výkon. Pracujeme na automatickém obnovení vašeho nasazení cloudové služby a na zjištění příčin tohoto problému. V tuto chvíli není potřeba žádná další akce. |
| Není v pořádku | Vaše nasazení cloudové služby není v pořádku {0} , protože mimo {1} instance role nejsou k dispozici. |
| Snížený výkon | Nasazení cloudové služby je Degradováno, protože {0} mimo {1} instance role nejsou k dispozici. | 
| Dostupné a možná ovlivněné | Vaše nasazení cloudové služby je spuštěné, ale probíhající výpadky služeb Azure vám může znemožnit připojení. Po vyřešení výpadku se připojení obnoví. |
| Nedostupné a možná ovlivněné | Na stav tohoto nasazení cloudové služby může mít vliv výpadek služby Azure. Nasazení cloudové služby se po vyřešení výpadku automaticky obnoví. |
| Neznámé a možná ovlivněné | V tuto chvíli nemůžeme určit stav tohoto nasazení cloudové služby. Příčinou může být probíhající výpadek služby Azure, který může mít vliv na tento virtuální počítač, který se při vyřešení výpadku automaticky obnoví. |

## <a name="cloud-services-role-instance-level-annotations--their-meanings"></a>Cloud Services (úroveň instance role) poznámky & jejich významy
| Poznámka | Popis | 
| --- | --- | 
| K dispozici | Tento virtuální počítač nemá vliv na žádné známé problémy s platformou Azure. | 
| Neznámý | V tuto chvíli nemůžeme určit stav tohoto virtuálního počítače. |
| Zastaveno a zrušení přidělení | Tento virtuální počítač se zastavuje a uvolňuje na žádost autorizovaného uživatele nebo procesu |
| Nastavení Resource Health | Nastavuje se Resource Health pro tento prostředek. Resource Health sleduje vaše prostředky Azure a poskytuje podrobnosti o probíhajících a minulých událostech, které na ně měly vliv. |
| Neaktivní | Váš virtuální počítač je nedostupný. Pracujeme na automatickém obnovení vašeho virtuálního počítače a na zjištění příčin tohoto problému. V tuto chvíli není potřeba žádná další akce. |
| Snížený výkon | Váš virtuální počítač má snížený výkon. Pracujeme na automatickém obnovení vašeho virtuálního počítače a na zjištění příčin tohoto problému. V tuto chvíli není potřeba žádná další akce. |
| Selhání hardwaru hostitelského serveru | Na tento virtuální počítač má vliv závažná chyba {HardwareCategory} na hostitelském serveru. Azure znovu nasadí virtuální počítač na server hostitele v pořádku. |
| Migrace je naplánovaná kvůli zhoršenému hardwaru. | Platforma Azure zjistila, že {0} na serveru hostitele má snížený výkon a očekává se brzké selhání. Pokud to bude možné, co nejdříve provedeme migraci vašeho virtuálního počítače za chodu, jinak ho po {1} (UTC) nasadíme znovu. Pro minimalizaci rizika pro vaši službu a pro případ, že se hardware nepovede před migrací iniciované systémem, doporučujeme, abyste virtuální počítač co nejdříve znovu nasadili. |
| Dostupné a možná ovlivněné | Váš virtuální počítač je spuštěný, ale probíhající výpadek služeb Azure vám může zabránit v jeho připojení. Po vyřešení výpadku se připojení obnoví. |
| Nedostupné a možná ovlivněné | Na stav tohoto virtuálního počítače může mít vliv výpadek služby Azure. Váš virtuální počítač se automaticky obnoví, jakmile se výpadek vyřeší. |
| Neznámé a možná ovlivněné | V tuto chvíli nemůžeme určit stav tohoto virtuálního počítače. Příčinou může být probíhající výpadek služby Azure, který může mít vliv na tento virtuální počítač, který se při vyřešení výpadku automaticky obnoví. |
| Přidělené hardwarové prostředky | K virtuálnímu počítači se přiřadily hardwarové prostředky a brzy bude online |
| Zastavení a zrušení přidělení | Tento virtuální počítač se zastavuje a uvolňuje na žádost autorizovaného uživatele nebo procesu |
| Konfigurace se aktualizuje | Na žádost autorizovaného uživatele nebo procesu se aktualizuje konfigurace tohoto virtuálního počítače |
| Restart uživatele | Tento virtuální počítač se restartuje na žádost autorizovaného uživatele nebo procesu. Po dokončení restartování bude zase online. |
| Opětovné nasazení na jiného hostitele | Tento virtuální počítač se na žádost autorizovaného uživatele nebo procesu nasazuje znovu na jiného hostitele. Po dokončení opětovného nasazení bude zase online. |
| Zastaveno uživatelem | Tento virtuální počítač se zastavuje na žádost autorizovaného uživatele nebo procesu |
| Zastaveno uživatelem nebo procesem | Tento virtuální počítač se zastavuje na žádost autorizovaného uživatele nebo procesu spuštěného na virtuálním počítači |
| Zahájeno uživatelem | Tento virtuální počítač se spouští na žádost autorizovaného uživatele nebo procesu. Brzy bude online. |
| Údržba se znovu nasazuje na jiného hostitele. | Tento virtuální počítač se v rámci aktivity plánované údržby nasazuje znovu na jiný server hostitele. Po dokončení opětovného nasazení bude zase online. |
| Restart inicializovaný v počítači | V rámci virtuálního počítače se aktivovalo restartování. Příčinou může být selhání operačního systému virtuálního počítače nebo požadavek autorizovaného uživatele nebo procesu. Po dokončení restartování bude virtuální počítač zase online. |
| Velikost změněna uživatelem | Na žádost autorizovaného uživatele nebo procesu se mění velikost tohoto virtuálního počítače. Až se změna velikosti dokončí, bude zase online. |
| Počítač selhal. | V rámci virtuálního počítače se aktivovalo restartování. Příčinou může být selhání operačního systému virtuálního počítače nebo požadavek autorizovaného uživatele nebo procesu. Po dokončení restartování bude virtuální počítač zase online. |
| Restartování údržby pro aktualizaci hostitele | Na serveru hostitele, na kterém je tento virtuální počítač spuštěný, probíhá instalace aktualizací údržby. V tuto chvíli nemusíte dělat nic dalšího. Až se údržba dokončí, bude zase online. |
| Údržba se znovu nasazuje na nový hardware. | Tento virtuální počítač je nedostupný, protože se právě v rámci plánované údržby nasazuje na novější hardware. V tuto chvíli nemusíte dělat nic dalšího. Po dokončení plánované údržby bude zase online. |
| Počítač s nízkou prioritou byl přepnut | Tento virtuální počítač se zrušil. Tento virtuální počítač s nízkou prioritou se zastaví a nadělí. |
| Restart hostitelského serveru | Je nám líto, ale váš virtuální počítač kvůli neočekávanému restartování serveru hostitele není dostupný. Server hostitele se právě restartuje. Po dokončení restartování bude virtuální počítač opět online. V tuto chvíli není potřeba žádná další akce. |
| Opětovné nasazení kvůli chybě hostitele | Je nám líto, ale váš virtuální počítač kvůli neočekávanému selhání na serveru hostitele není dostupný a nasazuje se znovu. Platforma Azure zahájila proces automatického obnovení a v tuto chvíli spouští virtuální počítač na jiném hostiteli. V tuto chvíli není potřeba žádná další akce. |
| Neočekávaná chyba hostitele | Je nám líto, ale váš virtuální počítač kvůli neočekávanému selhání na serveru hostitele není dostupný. Platforma Azure zahájila proces automatického obnovení a v tuto chvíli restartuje server hostitele. V tuto chvíli nemusíte dělat nic dalšího. Po dokončení restartování bude virtuální počítač zase online. |
| Opětovné nasazení z důvodu neplánované údržby hostitele | Je nám líto, ale váš virtuální počítač kvůli neočekávanému selhání na serveru hostitele není dostupný a nasazuje se znovu. Platforma Azure zahájila proces automatického obnovení a v tuto chvíli spouští virtuální počítač na jiném serveru hostitele. V tuto chvíli není potřeba žádná další akce. |
| Chyba zřizování | Je nám líto, ale váš virtuální počítač kvůli neočekávaným problémům při zřizování není dostupný. Kvůli neočekávané chybě se zřizování virtuálního počítače nepovedlo. |
| Migrace za provozu | Tento virtuální počítač je pozastavený kvůli operaci migrace za provozu se zachováním paměti. Virtuální počítač se obvykle znovu spustí do 10 sekund. V tuto chvíli není potřeba žádná další akce. |
| Migrace za provozu | Tento virtuální počítač je pozastavený kvůli operaci migrace za provozu se zachováním paměti. Virtuální počítač se obvykle znovu spustí do 10 sekund. V tuto chvíli není potřeba žádná další akce. | 
| Vzdálený disk odpojen | Je nám líto, ale váš virtuální počítač je kvůli ztrátě připojení ke vzdálenému disku nedostupný. Pracujeme na opětovném navázání připojení k disku. V tuto chvíli není potřeba žádná další akce. |
| Problém se službou Azure | Na váš virtuální počítač má vliv problém se službou Azure. |
| Problém se sítí | Tento virtuální počítač má vliv na síťové zařízení na nejvyšší úrovni. |
| Neaktivní | Váš virtuální počítač je nedostupný. V tuto chvíli nemůžeme určit příčinu tohoto výpadku. |
| Restart hostitelského serveru | Je nám líto, ale váš virtuální počítač kvůli neočekávanému restartování serveru hostitele není dostupný. Kvůli neočekávanému problému s hostitelským serverem nám nemůžeme automaticky obnovit váš virtuální počítač. |
| Opětovné nasazení kvůli chybě hostitele | Je nám líto, ale váš virtuální počítač kvůli neočekávanému selhání na serveru hostitele není dostupný. Kvůli neočekávanému problému s hostitelem se nám nemůžeme automaticky obnovit váš virtuální počítač. |
| Neočekávaná chyba hostitele | Je nám líto, ale váš virtuální počítač kvůli neočekávanému selhání na serveru hostitele není dostupný. Kvůli neočekávanému problému s hostitelem se nám nemůžeme automaticky obnovit váš virtuální počítač. |
| Opětovné nasazení z důvodu neplánované údržby hostitele | Je nám líto, ale váš virtuální počítač kvůli neočekávanému selhání na serveru hostitele není dostupný. Kvůli neočekávanému problému s hostitelem se nám nemůžeme automaticky obnovit váš virtuální počítač. |
| Chyba zřizování | Je nám líto, ale váš virtuální počítač kvůli neočekávaným problémům při zřizování není dostupný. Kvůli neočekávané chybě se zřizování virtuálního počítače nepovedlo. |
| Vzdálený disk odpojen | Je nám líto, ale váš virtuální počítač je kvůli ztrátě připojení ke vzdálenému disku nedostupný. Kvůli neočekávanému problému se nám nemůžeme automaticky obnovit váš virtuální počítač. |
| Restartování z důvodu aktualizace operačního systému hosta | Platforma Azure iniciovala restart, aby se použila nová aktualizace operačního systému hosta. Po dokončení restartování bude virtuální počítač zase online. |
