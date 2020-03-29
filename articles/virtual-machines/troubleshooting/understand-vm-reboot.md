---
title: Principy restartování systému pro virtuální počítač Azure | Dokumenty společnosti Microsoft
description: Uvádí události, které mohou způsobit restartování virtuálního počítače.
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
ms.openlocfilehash: e94ffb3d34082745c3d7ca86cfda2b93c0ed08da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919409"
---
# <a name="understand-a-system-reboot-for-azure-vm"></a>Principy restartování systému pro virtuální počítač Azure

Virtuální počítače Azure (VM) může někdy restartovat bez zjevného důvodu, bez důkazů o vaší zahájení operace restartování. Tento článek uvádí akce a události, které mohou způsobit restartování virtuálních počítačů a poskytuje přehled o tom, jak se vyhnout neočekávaným problémům s restartováním nebo snížit dopad těchto problémů.

## <a name="configure-the-vms-for-high-availability"></a>Konfigurace virtuálních počítače pro vysokou dostupnost

Nejlepší způsob, jak chránit aplikaci, která běží v Azure proti restartování virtuálních počítačů a prostojům, je konfigurace virtuálních počítačů pro vysokou dostupnost.

Chcete-li poskytnout tuto úroveň redundance pro vaši aplikaci, doporučujeme seskupit dva nebo více virtuálních počítačů v sadě dostupnosti. Tato konfigurace zajišťuje, že během plánované nebo neplánované události údržby je k dispozici alespoň jeden virtuální počítač, který splňuje 99,95% [azure sla](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_5/).

Další informace o sadách dostupnosti najdete [v tématu Správa dostupnosti virtuálních aplikací.](../windows/manage-availability.md)

## <a name="resource-health-information"></a>Informace o stavu zdrojů

Azure Resource Health je služba, která zveřejňuje stav jednotlivých prostředků Azure a poskytuje užitečné pokyny pro řešení problémů. V cloudovém prostředí, kde není možné přímo přistupovat k serverům nebo prvkům infrastruktury, je cílem služby Resource Health zkrátit čas, který strávíte řešením potíží. Cílem je zejména zkrátit čas, který strávíte určením, zda kořen problému spočívá v aplikaci nebo v události uvnitř platformy Azure. Další informace naleznete v [tématu Understand and Use Resource Health](../../resource-health/resource-health-overview.md).

## <a name="actions-and-events-that-can-cause-the-vm-to-reboot"></a>Akce a události, které můžou způsobit restartování virtuálního počítače

### <a name="planned-maintenance"></a>Plánovaná údržba

Microsoft Azure pravidelně provádí aktualizace po celém světě, aby zlepšil spolehlivost, výkon a zabezpečení hostitelské infrastruktury, která je základem virtuálních počítačích. Mnohé z těchto aktualizací, včetně aktualizací zachování paměti, se provádějí bez jakéhokoli dopadu na vaše virtuální počítače nebo cloudové služby.

Některé aktualizace však vyžadují restartování. V takových případech virtuálních disekonů se vypnou, zatímco my oprava infrastruktury a pak virtuální chody restartování.

Informace o tom, co je plánovaná údržba Azure a jak může ovlivnit dostupnost virtuálních počítačů s Linuxem, najdete v článcích uvedených tady. Tyto články poskytují základní informace o procesu plánované údržby Azure a postupu při plánování plánované údržby pro další omezení jejího dopadu.

- [Plánovaná údržba pro virtuální počítače v Azure](../windows/planned-maintenance.md)
- [Postup při plánování plánované údržby na virtuálních počítačích Azure](../windows/classic/planned-maintenance-schedule.md)

### <a name="memory-preserving-updates"></a>Aktualizace zachovávající paměť

Pro tuto třídu aktualizací v Microsoft Azure uživatelé zaznamenat žádný vliv na jejich spuštěné virtuální počítače. Mnoho takových aktualizací se týká komponent nebo služeb, které je možné aktualizovat bez zásahů do běžící instance. Některé z těchto aktualizací infrastruktury platformy v hostitelském operačním systému, které lze použít bez restartování virtuálních počítačů.

Takové aktualizace zachovávající paměť jsou možné díky technologii umožňující místní migraci za chodu. Když se aktualizuje, virtuální počítač je umístěn v *pozastaveném* stavu. Tento stav zachová paměť v paměti RAM, zatímco základní hostitelský operační systém obdrží potřebné aktualizace a opravy. Virtuální počítač se obnoví během 30 sekund od pozastavení. Jakmile se virtuální počítač obnoví, jeho hodiny se automaticky synchronizují.

Z důvodu krátké období pozastavení nasazení aktualizací prostřednictvím tohoto mechanismu výrazně snižuje dopad na virtuální počítače. Tímto způsobem však nelze nasadit všechny aktualizace. 

Aktualizace více instancí (pro virtuální počítače ve skupině dostupnosti) se instalují vždy jenom v jedné aktualizační doméně.

> [!NOTE]
> Linuxové počítače, které mají staré verze jádra, jsou během této metody aktualizace ovlivněny panikou jádra. Chcete-li se tomuto problému vyhnout, aktualizujte na verzi jádra 3.10.0-327.10.1 nebo novější. Další informace najdete [v tématu Azure Linux VM na 3.10-založené jádro paniky po upgradu uzlu hostitele](https://support.microsoft.com/help/3212236).

### <a name="user-initiated-reboot-or-shutdown-actions"></a>Akce restartování nebo vypnutí zahájené uživatelem

Pokud provedete restartování z webu Azure Portal, Azure PowerShell, rozhraní příkazového řádku nebo rozhraní REST API, můžete událost najít v [protokolu aktivit Azure](../../azure-monitor/platform/platform-logs-overview.md).

Pokud provedete akci z operačního systému virtuálního soudu, můžete najít událost v protokolech systému.

Další scénáře, které obvykle způsobí restartování virtuálního počítače, zahrnují více akcí změny konfigurace. Obvykle se zobrazí varovná zpráva označující, že provedení určité akce bude mít za následek restartování virtuálního počítače. Mezi příklady patří všechny operace změny velikosti virtuálního počítače, změna hesla účtu pro správu a nastavení statické ADRESY IP.

### <a name="azure-security-center-and-windows-update"></a>Azure Security Center a Windows Update

Azure Security Center monitoruje každodenní virtuální počítače s Windows a Linuxem, aby nechyběly aktualizace operačního systému. Security Center načte seznam dostupných aktualizací zabezpečení a důležitých aktualizací ze služby Windows Update nebo Služby WSUS (Windows Server Update Services) v závislosti na tom, která služba je nakonfigurována na virtuálním počítači se systémem Windows. Security Center také kontroluje nejnovější aktualizace pro systémy Linux. Pokud ve vašem virtuálním počítači chybí aktualizace systému, Security Center doporučuje použít aktualizace systému. Aplikace těchto aktualizací systému se řídí prostřednictvím Centra zabezpečení na webu Azure Portal. Po instalaci některých aktualizací může být nutné restartovat virtuální počítač. Další informace najdete [v tématu Apply system updates in Azure Security Center](../../security-center/security-center-apply-system-updates.md).

Stejně jako místní servery Azure nenabízené aktualizace ze služby Windows Update na virtuální počítače se systémem Windows, protože tyto počítače jsou určeny ke spravované jejich uživateli. Doporučujeme však ponechat automatické nastavení služby Windows Update povolené. Automatická instalace aktualizací ze služby Windows Update může také způsobit restartování po instalaci aktualizací. Další informace naleznete v [nejčastějších dotazech ke službě Windows Update](https://support.microsoft.com/help/12373/windows-update-faq).

### <a name="other-situations-affecting-the-availability-of-your-vm"></a>Další situace ovlivňující dostupnost vašeho virtuálního počítače

Existují i jiné případy, ve kterých Azure může aktivně pozastavit používání virtuálního počítače. Před touto akcí obdržíte e-mailová oznámení, takže budete mít možnost vyřešit základní problémy. Mezi příklady problémů, které ovlivňují dostupnost virtuálních zařízení, patří porušení zabezpečení a vypršení platnosti platebních metod.

### <a name="host-server-faults"></a>Chyby hostitelského serveru

Virtuální počítač je hostovaný na fyzickém serveru, který běží uvnitř datového centra Azure. Fyzický server spustí agenta s názvem Host Agent kromě několika dalších součástí Azure. Když tyto softwarové součásti Azure na fyzickém serveru přestanou reagovat, monitorovací systém spustí restartování hostitelského serveru a pokusí se o obnovení. Virtuální počítač je obvykle k dispozici znovu během pěti minut a nadále žít na stejném hostiteli jako dříve.

Chyby serveru jsou obvykle způsobeny selháním hardwaru, například selháním pevného disku nebo jednotky SSD. Azure průběžně monitoruje tyto výskyty, identifikuje základní chyby a zavádí aktualizace po implementaci a testování zmírnění.

Vzhledem k tomu, že některé chyby hostitelského serveru mohou být specifické pro tento server, může být situace restartování opakovaného virtuálního počítače vylepšena ručním opětovným nasazením virtuálního počítače na jiný hostitelský server. Tato operace se dá aktivovat pomocí možnosti **opětovného nasazení** na stránce podrobností virtuálního počítače nebo zastavením a restartováním virtuálního počítače na webu Azure Portal.

### <a name="auto-recovery"></a>Automatické obnovení

Pokud hostitelský server nelze restartovat z nějakého důvodu, platforma Azure zahájí akci automatického obnovení, aby vadný hostitelský server z rotace pro další šetření. 

Všechny virtuální servery na tomto hostiteli jsou automaticky přemístěny na jiný hostitelský server v pořádku. Tento proces je obvykle dokončen do 15 minut. Další informace o procesu automatického obnovení najdete v [tématu Automatické obnovení virtuálních vod](https://azure.microsoft.com/blog/service-healing-auto-recovery-of-virtual-machines).

### <a name="unplanned-maintenance"></a>Neplánovaná údržba

Ve výjimečných případech může provozní tým Azure potřebovat provádět aktivity údržby, aby zajistil celkový stav platformy Azure. Toto chování může ovlivnit dostupnost virtuálního počítače a obvykle vede ke stejné akci automatického obnovení, jak je popsáno výše.  

Neplánovaná údržba zahrnuje následující:

- Urgentní defragmentace uzlu
- Naléhavé aktualizace síťových přepínačů

### <a name="vm-crashes"></a>Selhání virtuálního virtuálního aplikace

Virtuální počítače může restartovat z důvodu problémů v rámci samotného virtuálního počítače. Úloha nebo role, která běží na virtuálním počítači, může spustit kontrolu chyb v rámci hostovaného operačního systému. Pro určení důvodu selhání, zobrazení protokolů systému a aplikací pro virtuální počítače se systémem Windows a sériové protokoly pro virtuální počítače s Linuxem.

### <a name="storage-related-forced-shutdowns"></a>Vynucená vypnutí související s úložištěm

Virtuální počítače v Azure spoléhají na virtuální disky pro operační systém a úložiště dat, které je hostované na infrastruktuře Azure Storage. Kdykoli je dostupnost nebo připojení mezi virtuálním počítačem a přidruženými virtuálními disky ovlivněna po dobu delší než 120 sekund, platforma Azure provede vynucené vypnutí virtuálních počítačů, aby se zabránilo poškození dat. Virtuální počítačse po obnovení připojení k úložišti automaticky znovu zapne. 

Doba vypnutí může být až pět minut, ale může být výrazně delší. Následuje jeden z konkrétních případů, které jsou spojeny s vynucenými vypnutími souvisejícími s úložištěm: 

**Překročení limitů vi**

Virtuální počítače může být dočasně vypnuty, když jsou požadavky na vstupně-v.i., protože objem vstupně-no operací za sekundu (VOPS) překračuje limity vstupně-va pro disk. (Standardní diskové úložiště je omezeno na 500 viops.) Chcete-li tento problém zmírnit, použijte prokládání disku nebo nakonfigurujte úložný prostor uvnitř hostovaného virtuálního počítače, v závislosti na zatížení. Podrobnosti najdete [v tématu Konfigurace virtuálních počítačích Azure pro optimální výkon úložiště](https://blogs.msdn.com/b/mast/archive/2014/10/14/configuring-azure-virtual-machines-for-optimal-storage-performance.aspx).

### <a name="other-incidents"></a>Další incidenty

Ve výjimečných případech může rozšířený problém ovlivnit více serverů v datovém centru Azure. Pokud k tomuto problému dojde, tým Azure odešle e-mailová oznámení do ohrožených předplatných. Můžete zkontrolovat [azure service health řídicí panel](https://azure.microsoft.com/status/) a portál Azure pro stav probíhajících výpadků a minulých incidentů.
