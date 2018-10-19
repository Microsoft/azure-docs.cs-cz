---
title: Vysvětlení restartování systému pro virtuální počítač Azure | Dokumentace Microsoftu
description: Obsahuje seznam událostí, které můžou způsobit restartování virtuálního počítače
services: virtual-machines
documentationcenter: ''
author: genlin
manager: willchen
editor: ''
tags: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: fa316ee47e6fdabacf22e1e419bfd501620dd83d
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2018
ms.locfileid: "49429146"
---
# <a name="understand-a-system-reboot-for-azure-vm"></a>Vysvětlení restartování systému pro virtuální počítač Azure

Virtuální počítače Azure (VM) může být někdy restartovat bez zjevné příčiny bez doklad o vašich nutnosti restartování, který operaci spustil. V tomto článku jsou uvedené akce a události, které můžou způsobit restartování virtuálních počítačů a poskytuje přehled o tom, jak k zamezení problémů s neočekávaným restartováním nebo omezit dopad těchto potíží.

## <a name="configure-the-vms-for-high-availability"></a>Konfigurace virtuálních počítačů pro zajištění vysoké dostupnosti
Výpadek je konfigurace virtuálních počítačů pro zajištění vysoké dostupnosti a nejlepší způsob, jak chránit aplikace, která běží v Azure před virtuální počítač restartuje.

Zadejte tento stupeň redundance, aby vaše aplikace, doporučujeme seskupit dva nebo více virtuálních počítačů ve skupině dostupnosti. Tato konfigurace zajistí, že během buď plánované i neplánované údržby, alespoň jeden virtuální počítač je k dispozici a splňuje 99,95 % [smlouva Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_5/).

Další informace o skupinách dostupnosti najdete v následujících článcích:

- [Správa dostupnosti virtuálních počítačů](../windows/manage-availability.md)
- [Konfigurace dostupnosti virtuálních počítačů](../windows/classic/configure-availability.md)

## <a name="resource-health-information"></a>Informace o stavu prostředků 
Azure Resource Health je služba, která zveřejňuje stav jednotlivých prostředků Azure a poskytuje podrobné pokyny pro řešení problémů. V cloudovém prostředí, ve kterém už není možné přímo přistupovat k serverům nebo prvky infrastruktury cílem Resource Health je zkrátit čas, který nákladů na řešení potíží. Zejména cílem je zkrátit čas, který strávíte určující, zda je základní příčina problému v aplikaci nebo v případě uvnitř platformy Azure. Další informace najdete v tématu [principy a použití Resource Health](../../resource-health/resource-health-overview.md).

## <a name="actions-and-events-that-can-cause-the-vm-to-reboot"></a>Akce a události, které můžou způsobit restartování virtuálního počítače

### <a name="planned-maintenance"></a>Plánovaná údržba
Microsoft Azure provádí po celém světě zlepšit spolehlivost, výkon a zabezpečení hostitelské infrastruktury, které je základem virtuálních počítačů pravidelné aktualizace. Mnohé z těchto aktualizací, včetně aktualizacích pro zachování paměti, nemá jejich provedení žádný vliv na vaše virtuální počítače nebo cloudové služby.

Ale některé aktualizace vyžadují restartování. V takových případech jsou virtuální počítače vypnout během oprav infrastruktury, a pak virtuální počítače se restartují.

Vám pomohou pochopit, jaké plánovaná údržba Azure je a jak může ovlivnit dostupnost vašich virtuálních počítačů s Linuxem, najdete v článcích uvedených zde. Články poskytují informace o Azure plánované údržby procesu a tom, jak naplánovat plánované údržby tak, aby dál snížit dopad.

- [Plánovaná údržba pro virtuální počítače v Azure](../windows/planned-maintenance.md)
- [Jak plánovat plánované údržby na virtuálních počítačích Azure](../windows/classic/planned-maintenance-schedule.md)

### <a name="memory-preserving-updates"></a>Aktualizace zachovávající paměť   
Pro tuto třídu aktualizacím v Microsoft Azure uživatelé mít žádný vliv na jejich spuštěných virtuálních počítačů. Mnoho takových aktualizací se týká komponent nebo služeb, které je možné aktualizovat bez zásahů do běžící instance. Některé jsou aktualizace infrastruktury platformy v operačním systému hostitele, který lze použít bez restartování virtuálních počítačů.

Takové aktualizace zachovávající paměť jsou možné díky technologii umožňující místní migraci za chodu. Když je aktualizován, virtuální počítač je umístěn v *pozastaveno* stavu. Tento stav zachová paměť v paměti RAM, zatímco základní hostitelský operační systém obdrží potřebné aktualizace a opravy. Virtuální počítač se obnoví během 30 sekund od pozastavení. Jakmile se virtuální počítač obnoví, jeho hodiny se automaticky synchronizují.

Z důvodu krátkému období pozastavení nasazování aktualizací díky tomuto mechanismu výrazně snižuje dopad na virtuálních počítačích. Ale ne všechny aktualizace je možné nasadit tímto způsobem. 

Aktualizace více instancí (pro virtuální počítače ve skupině dostupnosti) se instalují vždy jenom v jedné aktualizační doméně.

> [!NOTE]
> Počítače s Linuxem, které mají starší verze jádra jsou ovlivněny poplachu jádra během této metody aktualizace. K tomuto problému vyhnout, aktualizujte na 3.10.0-327.10.1 verze jádra nebo novější. Další informace najdete v tématu [poplachy Linuxový virtuální počítač Azure na jádra na základě 3.10 po upgradu uzlu hostitele](https://support.microsoft.com/help/3212236).     
    
### <a name="user-initiated-reboot-or-shutdown-actions"></a>Nebo zapnul a je akce zahájená uživatelem
 
Pokud provést restartování z webu Azure portal, prostředí Azure PowerShell, rozhraní příkazového řádku nebo rozhraní API REST, můžete vyhledat události v [protokolu aktivit Azure](../../monitoring-and-diagnostics/monitoring-overview-activity-logs.md).

Pokud provedení akce z operačního systému Virtuálního počítače, najdete v protokolech systému události.

Další scénáře, které obvykle způsobí restartování virtuálního počítače zahrnují více změna konfigurace akce. Obvykle uvidíte, že se upozornění, která provádí určitou akci způsobí restartování virtuálního počítače. Mezi příklady patří všechny virtuálního počítače změnit velikost operace, změna hesla pro účet správce a nastavení statické IP adresy.

### <a name="azure-security-center-and-windows-update"></a>Azure Security Center a Windows Update
Azure Security Center monitoruje denní Windows a virtuální počítače s Linuxem pro chybějící aktualizace operačního systému. Security Center načte seznam dostupných zabezpečení a kritických aktualizací ze služby Windows Update nebo Windows Server Update Services (WSUS), podle toho, která služba je nakonfigurovaná na virtuálním počítači s Windows. Security Center také vyhledává nejnovějších aktualizací pro systémy Linux. Pokud se váš virtuální počítač je chybějící aktualizace systému, Security Center doporučuje použití aktualizací systému. Aplikace tyto aktualizace systému je řízen pomocí Security Center na webu Azure Portal. Po instalaci některé aktualizace, může být vyžadováno restartování virtuálních počítačů. Další informace najdete v tématu [nainstalovat aktualizace systému ve službě Azure Security Center](../../security-center/security-center-apply-system-updates.md).

Například na místních serverech Azure není nabízené aktualizace ze služby Windows Update k virtuálním počítačům s Windows, vzhledem k tomu, že tyto počítače mají spravovat přes své uživatele. Máte však doporučujeme ponechat nastavení automatické aktualizace Windows povolena. Automatická instalace aktualizací ze služby Windows Update může také způsobit restartování docházelo až se aktualizace. Další informace najdete v tématu [nejčastější dotazy k Windows Update](https://support.microsoft.com/help/12373/windows-update-faq).

### <a name="other-situations-affecting-the-availability-of-your-vm"></a>Jindy by to mělo dopad na dostupnost virtuálního počítače
Existují další případy, ve kterých může být Azure aktivně pozastavit používání virtuálního počítače. Obdržíte e-mailová oznámení předtím, než tuto akci, tak budete mít příležitost dobře se základní řešení. Příklady problémy, které ovlivňují dostupnost virtuálních počítačů: narušení zabezpečení a vypršení jejich platnosti platební metody.

### <a name="host-server-faults"></a>Chyby serveru hostitele 
Virtuální počítač je hostován na fyzickém serveru, na kterém běží v datovém centru Azure. Fyzický server běží agentovi nazvaném kromě pár dalšími komponentami Azure, aby byl Agent hostitele. Tyto součásti softwaru Azure na fyzickém serveru, přestane reagovat, spustí monitorování systému restartování hostitelského serveru pokusu o obnovení. Virtuální počítač je obvykle k dispozici během pěti minut znovu a pokračuje v za provozu na stejném hostiteli jako dříve.

Chyby serveru jsou obvykle způsobeno selhání hardwaru, jako je například selhání jednotky SSD nebo pevný disk. Azure nepřetržitě monitoruje tyto výskytů, identifikuje základní chyby a zavádění aktualizací po omezení rizik byla implementována a testování.

Protože některé chyby serveru hostitele může být specifické pro tento server, by mohlo být zlepšení opakované situace restartování virtuálního počítače pomocí ruční opětovné nasazení virtuálního počítače na jiný hostitelský server. Tato operace se dá spouštět pomocí **znovu nasadit** možnosti na stránce podrobností virtuálního počítače nebo prostřednictvím zastavení a restartování virtuálního počítače na webu Azure Portal.

### <a name="auto-recovery"></a>Automatické obnovení
Pokud z nějakého důvodu nelze restartovat hostitelský server, na platformě Azure zahájí akce automatického obnovení vadným hostitelský server ze smyčky pro další zkoumání. 

Všechny virtuální počítače na tomto hostiteli jsou automaticky přemístit na jiný, v pořádku hostitelský server. Tento proces je obvykle dokončena během 15 minut. Další informace o proces automatického obnovení najdete v tématu [automatické obnovení virtuálních počítačů](https://azure.microsoft.com/blog/service-healing-auto-recovery-of-virtual-machines).

### <a name="unplanned-maintenance"></a>Neplánovaná Údržba
Ve výjimečných případech může být nutné Azure provozní tým provádět činnosti údržby zajistit celkový stav platformy Azure. Toto chování může mít vliv na dostupnost virtuálních počítačů, a to situace obvykle vzniká stejné akce automatického obnovení jak bylo popsáno dříve.  

Neplánované maintenances patří:

- Defragmentace urgentní uzlu
- Aktualizace přepínač urgentní sítě

### <a name="vm-crashes"></a>Virtuální počítač, dojde k chybě
Kvůli problémům v rámci samotných virtuálních počítačů může restartovat virtuální počítače. Kontrola chyb v rámci hostovaného operačního systému můžou aktivovat úlohy nebo role, která běží na virtuálním počítači. Pro pomoc při rozhodování, důvod selhání zobrazení systému a virtuální počítače s Windows v protokolech aplikací a sériové protokoly pro virtuální počítače s Linuxem.

### <a name="storage-related-forced-shutdowns"></a>Související úložiště vynucené vypnutí
Virtuální počítače v Azure využívají virtuální disky pro operační systém a úložiště dat, které je hostované v infrastruktuře Azure Storage. Pokaždé, když se po dobu více než 120 sekund je vliv na dostupnost nebo připojení mezi virtuální počítač a přidruženými virtuálními disky, Platforma Azure provede vynucené vypnutí virtuálních počítačů, abyste zabránili poškození dat. Virtuální počítače jsou automaticky zase zapne po obnovení připojení úložiště. 

Doba trvání vypnutí dá zkrátit až pět minut, ale mohou být podstatně delší. Tady je jeden z konkrétní případy, které souvisí s týkající se úložiště vynucené vypnutí: 

**Vstupně-výstupních operací překročení omezení**

Virtuální počítače může být dočasně vypnout, když vstupně-výstupních požadavků se omezují konzistentní vzhledem k tomu, že objem vstupně-výstupních operací za sekundu (IOPS) překračuje limity vstupně-výstupních operací disku. (Disk storage úrovně standard je omezený na 500 IOPS.) Chcete-li tento problém zmírnit, použijte prokládání disků nebo nakonfigurovat v prostoru úložiště uvnitř hostovaného virtuálního počítače, v závislosti na zatížení. Podrobnosti najdete v tématu [konfigurace virtuálních počítačů Azure pro optimální výkon úložiště](http://blogs.msdn.com/b/mast/archive/2014/10/14/configuring-azure-virtual-machines-for-optimal-storage-performance.aspx).

Vyšší limity vstupně-výstupních operací jsou k dispozici prostřednictvím Azure Premium Storage s využitím až 80 000 vstupně-výstupních operací. Další informace najdete v tématu [vysoce výkonné úložiště úrovně Premium](../windows/premium-storage.md).

### <a name="other-incidents"></a>Jiné incidenty
Ve výjimečných případech může ovlivnit rozšířených problém více serverů v datovém centru Azure. Pokud k tomuto problému dochází, tým Azure odešle e-mailová oznámení ovlivněná předplatná. Můžete zkontrolovat [řídicí panel stavu služeb Azure](https://azure.microsoft.com/status/) a webu Azure portal pro stav probíhající výpadků a minulé incidenty.
