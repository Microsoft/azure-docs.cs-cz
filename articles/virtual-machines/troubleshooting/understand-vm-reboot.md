---
title: Principy restartování systému pro virtuální počítač Azure | Microsoft Docs
description: Zobrazí události, které můžou způsobit restartování virtuálního počítače.
services: virtual-machines
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 4694b6ac829c42f20c6783810c248ee18d220433
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91965756"
---
# <a name="understand-a-system-reboot-for-azure-vm"></a>Principy restartování systému pro virtuální počítač Azure

Virtuální počítače Azure se někdy můžou restartovat bez zjevné příčiny, aniž by bylo nutné, abyste operaci restartování zahájili. V tomto článku jsou uvedené akce a události, které můžou způsobit restart virtuálních počítačů, a poskytuje přehled o tom, jak se vyhnout neočekávaným potížím s restartováním nebo jak snížit dopad těchto problémů.

## <a name="configure-the-vms-for-high-availability"></a>Konfigurace virtuálních počítačů pro zajištění vysoké dostupnosti

Nejlepším způsobem, jak chránit aplikaci běžící v Azure proti restartování virtuálních počítačů a výpadkům, je konfigurace virtuálních počítačů pro zajištění vysoké dostupnosti.

Pro zajištění této úrovně redundance vaší aplikace doporučujeme seskupit dva nebo více virtuálních počítačů do skupiny dostupnosti. Tato konfigurace zajišťuje, že během plánované nebo neplánované události údržby je k dispozici alespoň jeden virtuální počítač, který splňuje 99,95% [smlouvu SLA pro Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_5/).

Další informace o skupinách dostupnosti najdete v tématu [Správa dostupnosti virtuálních počítačů](../manage-availability.md) .

## <a name="resource-health-information"></a>Resource Health informace

Azure Resource Health je služba, která zveřejňuje stav jednotlivých prostředků Azure a poskytuje užitečné pokyny pro řešení problémů. V cloudovém prostředí, kde není možné získat přímý přístup k serverům nebo prvkům infrastruktury, je cílem Resource Health zkrátit dobu, po kterou strávíte při řešení potíží. Konkrétně je potřeba zkrátit dobu, kterou strávíte tím, že se rozhodnete, jestli je kořen problému v aplikaci nebo v události v rámci platformy Azure. Další informace najdete v tématu [pochopení a použití Resource Health](../../service-health/resource-health-overview.md).

## <a name="actions-and-events-that-can-cause-the-vm-to-reboot"></a>Akce a události, které můžou způsobit, že se virtuální počítač restartuje

### <a name="planned-maintenance"></a>Plánovaná údržba

Microsoft Azure pravidelně provádí aktualizace po celém světě, aby se zlepšila spolehlivost, výkon a zabezpečení hostitelské infrastruktury, která je na virtuálních počítačích. Mnohé z těchto aktualizací, včetně řešení pro zachování paměti, jsou prováděny bez jakéhokoli dopadu na virtuální počítače nebo cloudové služby.

Některé aktualizace ale vyžadují restart. V takových případech jsou virtuální počítače při opravě infrastruktury vypnuté a virtuální počítače se restartují.

Informace o tom, co je plánovaná údržba Azure a jak může ovlivnit dostupnost virtuálních počítačů se systémem Linux, najdete v článcích uvedených zde. Tyto články poskytují základní informace o procesu plánované údržby Azure a postupu při plánování plánované údržby pro další omezení jejího dopadu.

- [Plánovaná údržba pro virtuální počítače v Azure](../maintenance-and-updates.md?bc=/azure/virtual-machines/windows/breadcrumb/toc.json&toc=/azure/virtual-machines/windows/toc.json)
- [Postup při plánování plánované údržby na virtuálních počítačích Azure](../maintenance-and-updates.md?bc=/azure/virtual-machines/windows/breadcrumb/toc.json&toc=/azure/virtual-machines/windows/toc.json)

### <a name="memory-preserving-updates"></a>Aktualizace zachovávající paměť

Pro tuto třídu aktualizací v Microsoft Azure nemá uživatel vliv na spuštěné virtuální počítače. Mnoho takových aktualizací se týká komponent nebo služeb, které je možné aktualizovat bez zásahů do běžící instance. Některé jsou aktualizace infrastruktury platforem v hostitelském operačním systému, které je možné použít bez restartování virtuálních počítačů.

Takové aktualizace zachovávající paměť jsou možné díky technologii umožňující místní migraci za chodu. Po aktualizaci se virtuální počítač umístí do *pozastaveného* stavu. Tento stav zachová paměť v paměti RAM, zatímco základní hostitelský operační systém obdrží potřebné aktualizace a opravy. Virtuální počítač se obnoví během 30 sekund od pozastavení. Jakmile se virtuální počítač obnoví, jeho hodiny se automaticky synchronizují.

Kvůli krátké době pozastavení nasazování aktualizací prostřednictvím tohoto mechanismu významně snižuje dopad na virtuální počítače. Tímto způsobem ale nemůžete nasadit všechny aktualizace. 

Aktualizace více instancí (pro virtuální počítače ve skupině dostupnosti) se instalují vždy jenom v jedné aktualizační doméně.

> [!NOTE]
> Počítače se systémem Linux, ve kterých jsou staré verze jádra, jsou při této metodě aktualizace ovlivněny nouzovým jádrem jádra. Chcete-li se tomuto problému vyhnout, aktualizujte na jádro verze 3.10.0-327.10.1 nebo novější. Další informace najdete v tématu [virtuální počítač Azure Linux v jádru založeném na 3,10, který se od upgradu uzlu hostitele nejedná](https://support.microsoft.com/help/3212236).

### <a name="user-initiated-reboot-or-shutdown-actions"></a>Akce restartování nebo vypnutí zahájené uživatelem

Pokud provedete restartování z Azure Portal, Azure PowerShell, rozhraní příkazového řádku nebo REST API, můžete najít událost v [protokolu aktivit Azure](../../azure-monitor/platform/platform-logs-overview.md).

Pokud provedete akci z operačního systému virtuálního počítače, můžete najít událost v systémových protokolech.

Další scénáře, které obvykle způsobí, že se virtuální počítač restartuje, zahrnuje několik akcí změny konfigurace. Obvykle se zobrazí varovná zpráva oznamující, že provedení konkrétní akce způsobí restartování virtuálního počítače. Příkladem může být jakákoli operace změny velikosti virtuálního počítače, změna hesla účtu správce a nastavení statické IP adresy.

### <a name="azure-security-center-and-windows-update"></a>Azure Security Center a web Windows Update

Azure Security Center monitoruje každodenní virtuální počítače se systémem Windows a Linux pro chybějící aktualizace operačního systému. Security Center načte seznam dostupných aktualizací zabezpečení a kritické aktualizace od web Windows Update nebo Windows Server Update Services (WSUS) podle toho, která služba je nakonfigurovaná na virtuálním počítači s Windows. Security Center také kontroluje nejnovější aktualizace pro systémy Linux. Pokud ve vašem VIRTUÁLNÍm počítači chybí aktualizace systému, Security Center doporučuje, abyste použili aktualizace systému. Použití těchto aktualizací systému se řídí Security Center v Azure Portal. Po použití některých aktualizací se může vyžadovat restartování virtuálního počítače. Další informace najdete v tématu věnovaném [použití aktualizací systému v Azure Security Center](../../security-center/asset-inventory.md).

Podobně jako na místních serverech nenabízí Azure aktualizace z web Windows Update na virtuální počítače s Windows, protože tyto počítače mají být spravované svými uživateli. Ale doporučujeme ponechat nastavení automatického web Windows Update povolené. Automatická instalace aktualizací z web Windows Update může také způsobit restartování po použití aktualizací. Další informace najdete v tématu [web Windows Update nejčastější dotazy](https://support.microsoft.com/help/12373/windows-update-faq).

### <a name="other-situations-affecting-the-availability-of-your-vm"></a>Další situace ovlivňující dostupnost vašeho virtuálního počítače

V některých případech může Azure aktivně pozastavit použití virtuálního počítače. Před provedením této akce obdržíte e-mailová oznámení, takže budete mít možnost vyřešit základní problémy. Příklady problémů, které mají vliv na dostupnost virtuálních počítačů, zahrnují porušení zabezpečení a dobu platnosti platebních metod.

### <a name="host-server-faults"></a>Chyby hostitelského serveru

Virtuální počítač je hostovaný na fyzickém serveru, který běží v datovém centru Azure. Fyzický server používá kromě několika dalších součástí Azure agenta označovaného jako hostitelský agent. Pokud tyto součásti softwaru Azure na fyzickém serveru přestanou reagovat, systém monitorování spustí restart hostitelského serveru, aby se pokusil o obnovení. Virtuální počítač je obvykle k dispozici znovu během pěti minut a pokračuje v provozu na stejném hostiteli jako dříve.

Chyby serveru jsou obvykle způsobeny selháním hardwaru, například selháním pevného disku nebo jednotky SSD (Solid-State). Azure průběžně monitoruje tyto výskyty, identifikuje základní chyby a shrnuje aktualizace po implementaci a testování zmírnění rizik.

Vzhledem k tomu, že některé chyby hostitelského serveru můžou být pro tento server specifické, může se zlepšit opakovaný restart virtuálního počítače tak, že ručně znovu nasadí virtuální počítač na jiný hostitelský server. Tuto operaci lze aktivovat pomocí možnosti **znovu nasadit** na stránce s podrobnostmi virtuálního počítače nebo zastavením a RESTARTOVÁNÍM virtuálního počítače v Azure Portal.

### <a name="auto-recovery"></a>Automatické obnovení

Pokud se hostitelský server nemůže z nějakého důvodu restartovat, platforma Azure zahájí akci automatického obnovení, která bude mít k disdobu nepoškozený hostitelský server pro další šetření. 

Všechny virtuální počítače na tomto hostiteli se automaticky přemístě na jiný hostitelský server v pořádku. Tento proces je obvykle dokončen do 15 minut. Další informace o procesu automatického obnovení najdete v tématu [Automatické obnovení virtuálních počítačů](https://azure.microsoft.com/blog/service-healing-auto-recovery-of-virtual-machines).

### <a name="unplanned-maintenance"></a>Neplánovaná údržba

Ve výjimečných případech může tým provozu Azure potřebovat aktivity údržby, aby zajistil celkový stav platformy Azure. Toto chování může mít vliv na dostupnost virtuálního počítače a obvykle má za následek stejnou akci automatického obnovení, jak je popsáno výše.  

Neplánovaná údržba zahrnuje následující:

- Defragmentace naléhavých uzlů
- Aktualizace naléhavých síťových přepínačů

### <a name="vm-crashes"></a>Selhání virtuálních počítačů

Virtuální počítače se můžou restartovat kvůli problémům v samotném virtuálním počítači. Zatížení nebo role, která běží na virtuálním počítači, může aktivovat kontrolu chyb v hostovaném operačním systému. Nápovědu k určení příčiny selhání najdete v protokolech systému a aplikací pro virtuální počítače s Windows a v protokolech sériového systému Linux.

### <a name="storage-related-forced-shutdowns"></a>Vynucená vypnutí související s úložištěm

Virtuální počítače v Azure spoléhají na virtuální disky pro operační systém a úložiště dat hostované v infrastruktuře Azure Storage. Kdykoliv se dostupnost nebo připojení mezi virtuálním počítačem a přidruženými virtuálními disky projeví déle než 120 sekund, platforma Azure provede vynucené vypnutí virtuálních počítačů, aby se předešlo poškození dat. Po obnovení připojení úložiště se virtuální počítače automaticky zapněte. 

Doba vypnutí může být kratší než pět minut, ale může být výrazně delší. Následuje jeden z konkrétních případů, které jsou spojeny s vynuceným vypnutím souvisejícím s úložištěm: 

**Překročení limitů v/v**

Virtuální počítače se můžou dočasně vypnout, I když jsou požadavky na vstupně-výstupní operace konzistentně omezené, protože objem vstupně-výstupních operací za sekundu (IOPS) překračuje limity vstupně-výstupních operací disku. (Standardní diskové úložiště je omezeno na 500 IOPS.) Pokud chcete tento problém zmírnit, použijte diskové svazky nebo nakonfigurujte prostor úložiště uvnitř virtuálního počítače hosta v závislosti na zatížení. 

### <a name="other-incidents"></a>Další incidenty

Ve výjimečných případech může rozšířený problém ovlivnit více serverů v datovém centru Azure. Pokud k tomuto problému dojde, tým Azure pošle e-mailová oznámení ovlivněným předplatným. Na [řídicím panelu Azure Service Health](https://azure.microsoft.com/status/) a Azure Portal můžete zjistit stav trvalých incidentů a minulých incidentů.