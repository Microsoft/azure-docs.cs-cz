---
title: Nejčastější dotazy ohledně migrace z modelu Classic na Azure Resource Manager
description: Nejčastější dotazy ohledně migrace z modelu Classic na Azure Resource Manager
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: 33dfd298224962617891f3ab1d540462b2b88954
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/18/2020
ms.locfileid: "94844604"
---
# <a name="frequently-asked-questions-about-classic-to-azure-resource-manager-migration"></a>Nejčastější dotazy ohledně migrace z modelu Classic na Azure Resource Manager

> [!IMPORTANT]
> V dnešní době se o 90% virtuálních počítačů IaaS používají [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/). Od 28. února 2020 se klasické virtuální počítače zastaraly a budou plně vyřazeny od 1. března 2023. [Přečtěte si další informace]( https://aka.ms/classicvmretirement) o této zastaralosti a [o tom, jak vás to ovlivní](./classic-vm-deprecation.md#how-does-this-affect-me).

## <a name="what-is-azure-service-manager-and-what-does-it-mean-by-classic"></a>Co je Azure Service Manager a co to znamená Classic?

Slovo "Classic" na virtuálním počítači s IaaS (Classic) odkazuje na virtuální počítače spravované službou Azure Service Manager (ASM). Azure Service Manager (ASM) je stará Řídicí rovina Azure zodpovědná za vytváření, správu a odstraňování virtuálních počítačů a provádění jiných operací roviny řízení. 

## <a name="what-is-azure-resource-manager"></a>Co je Azure Resource Manager?

[Azure Resource Manager](../azure-resource-manager/management/overview.md) je nejnovější Řídicí rovina Azure zodpovědná za vytváření, správu a odstraňování virtuálních počítačů a provádění jiných operací roviny řízení. 

## <a name="what-is-the-time-required-for-migration"></a>Jaký je čas potřebný k migraci?

Plánování a provádění migrace značně závisí na složitosti architektury a může trvat několik měsíců.  

## <a name="what-is-the-definition-of-a-new-customer-on-iaas-vms-classic"></a>Jaká je definice nového zákazníka na virtuálních počítačích s IaaS (Classic)?

Zákazníci, kteří ve svém předplatném neIaaSi virtuální počítače (Classic) v měsíci únor 2020 (měsíc před vyřazením), se považují za nové zákazníky. 

## <a name="what-is-the-definition-of-an-existing-customer-on-iaas-virtual-machines-classic"></a>Jaká je definice stávajícího zákazníka v IaaS Virtual Machines (Classic)?

Zákazník, který ve svém předplatném v měsíci února 2020 aktivní nebo zastavený, ale přidělené virtuální počítače IaaS (Classic), se považuje za stávajícího zákazníka. Až do 1. března 2023 se k migraci svých virtuálních počítačů z Azure Service Manager do Azure Resource Manager dostanou jenom tito zákazníci. 

## <a name="why-am-i-getting-an-error-stating-newclassicvmcreationnotallowedforsubscription"></a>Proč se mi zobrazuje chyba s oznámením "NewClassicVMCreationNotAllowedForSubscription"?

V rámci procesu vyřazení již nejsou virtuální počítače IaaS (Classic) k dispozici pro nové zákazníky. Identifikovali jsme vás jako noví zákazníci, takže vaše operace nebyla autorizována. Důrazně doporučujeme používat službu [Azure Virtual Machines s využitím ARM](./windows/quick-create-powershell.md). Pokud nemůžete použít virtuální počítače Azure pomocí ARM, obraťte se prosím na podporu a přidejte své předplatné do seznamu povolených.

## <a name="does-this-migration-plan-affect-any-of-my-existing-services-or-applications-that-run-on-azure-virtual-machines"></a>Má tento plán migrace vliv na některé stávající služby nebo aplikace spuštěné na virtuálních počítačích Azure? 

Ne do 1. března 2023 pro virtuální počítače s IaaS (Classic). Virtuální počítače s IaaS (Classic) mají obecně dostupné služby. I nadále můžete tyto prostředky používat k rozšíření vaší působnosti v systému Microsoft Azure. Od 1. března 2023 se tyto virtuální počítače kompletně vyřadí a všechny aktivní nebo přidělené virtuální počítače se zastaví & přidělení zrušeno. Nebude to mít žádný vliv na jiné klasické prostředky, jako je Cloud Services (Classic), účty úložiště (Classic) atd.   

## <a name="what-happens-to-my-vms-if-i-dont-plan-on-migrating-in-the-near-future"></a>Co se stane s virtuálními počítači, pokud se je nechystám migrovat v blízké budoucnosti? 

1. března 2023 budou virtuální počítače IaaS (Classic) kompletně vyplněné a všechny aktivní nebo přidělené virtuální počítače se zastaví & přidělení zrušeno. Abychom předešli dopadům na chod firmy, máme v dnešní době komentář k zahájení plánování migrace a dokončit ho do 1. března 2023. Stávající rozhraní API Classic, Cloud Services a model prostředků nepoužíváme. Vzhledem k pokročilým funkcím dostupným v modelu nasazení Resource Manager chceme migraci usnadnit. Doporučujeme, abyste zahájili plánování migrace těchto prostředků na Azure Resource Manager. 

## <a name="what-does-this-migration-plan-mean-for-my-existing-tooling"></a>Co tento plán migrace znamená pro stávající nástroje? 

Aktualizace nástrojů na model nasazení Resource Manager je jedna z nejdůležitějších změn, se kterou v plánech migrace musíte počítat.

## <a name="how-long-will-the-management-plane-downtime-be"></a>Jak dlouho bude trvat výpadek roviny správy? 

To závisí na počtu prostředků, které se migrují. U menších nasazení (několik desítek virtuálních počítačů) by celá migrace měla trvat méně než hodinu. U rozsáhlých nasazení (stovky virtuálních počítačů) může migrace trvat několik hodin.

## <a name="can-i-roll-back-after-my-migrating-resources-are-committed-in-resource-manager"></a>Dají se vrátit změny po potvrzení migrovaných prostředků v Resource Manageru? 

Migraci můžete přerušit, dokud jsou prostředky v připraveném stavu. Po úspěšné migraci prostředků prostřednictvím operace potvrzení se vrácení zpět nepodporuje.

## <a name="can-i-roll-back-my-migration-if-the-commit-operation-fails"></a>Je možné vrátit migraci zpět v případě, že selže operace potvrzení? 

Pokud selže operace potvrzení, migraci není možné přerušit. Všechny operace migrace, včetně operace potvrzení, jsou idempotentní. Proto doporučujeme, abyste operaci po chvilce zkusili opakovat. Pokud pořád dojde k chybě, vytvořte lístek podpory.

## <a name="do-i-have-to-buy-another-express-route-circuit-if-i-have-to-use-iaas-under-resource-manager"></a>Je nutné koupit další okruh ExpressRoute, když potřebuji používat infrastrukturu jako službu (IaaS) v modelu Resource Manager? 

Ne. Nedávno jsme povolili [přesun okruhů ExpressRoute z modelu nasazení Classic na Resource Manager](../expressroute/expressroute-move.md). Pokud už máte okruh ExpressRoute, není nutné kupovat nový.

## <a name="what-if-i-had-configured-azure-role-based-access-control-policies-for-my-classic-iaas-resources"></a>Co když mám nakonfigurovat zásady řízení přístupu na základě rolí Azure pro moje klasické prostředky IaaS? 

Během migrace se prostředky transformují z modelu Classic na Resource Manager. Proto doporučujeme, abyste naplánovali aktualizace zásad Azure RBAC, které se musí provést po migraci.

## <a name="i-backed-up-my-classic-vms-in-a-vault-can-i-migrate-my-vms-from-classic-mode-to-resource-manager-mode-and-protect-them-in-a-recovery-services-vault"></a>Tyto klasické virtuální počítače jsem zálohoval do trezoru. Můžu migrovat svoje virtuální počítače z klasického režimu do režimu Resource Manageru a chránit je pomocí trezoru služby Recovery Services?

Když přesunete virtuální počítač z klasického do režimu Správce prostředků, zálohy provedené před migrací se nebudou migrovat na nově migrovaný Správce prostředků virtuální počítač. Pokud ale chcete zachovat zálohy klasických virtuálních počítačů, postupujte před migrací pomocí těchto kroků. 

1. V trezoru Recovery Services otevřete kartu **chráněné položky** a vyberte virtuální počítač. 
2. Klikněte na Zastavit ochranu. Políčko *Delete associated backup data* (Odstranit přidružená data záloh) ponechte **nezaškrtnuté**.

> [!NOTE]
> Náklady na instanci zálohy se vám budou účtovat, dokud si zachováte data. Záložní kopie se vyřadí jako rozsah pro uchování dat. Poslední záložní kopie je ale vždycky zachovaná, dokud data zálohy explicitně neodstraníte. Po překročení rozsahu uchování doporučujeme, abyste zkontrolovali rozsah uchování virtuálního počítače a aktivovali v chráněné položce v trezoru možnost odstranit data zálohy. 
>
>

Chcete-li migrovat virtuální počítač do režimu Správce prostředků, 

1. Odstraňte z virtuálního počítače zálohu/rozšíření snímků.
2. Proveďte migraci virtuálního počítače z klasického režimu do režimu Resource Manageru. Ověřte, že se do režimu Resource Manager migruje také úložiště a informace o síti odpovídající tomuto virtuálnímu počítači.

Pokud navíc chcete zálohovat migrovaný virtuální počítač, v okně Správa virtuálního počítače [Povolte zálohování](../backup/quick-backup-vm-portal.md#enable-backup-on-a-vm).

## <a name="can-i-validate-my-subscription-or-resources-to-see-if-theyre-capable-of-migration"></a>Je možné ověřit, jestli jsou prostředky nebo předplatné schopné migrace? 

Ano. U možnosti migrace s podporou platformy je prvním krokem přípravy na migraci ověření, že jsou prostředky schopné migrace. V případě selhání operace ověření se zobrazí zprávy se všemi důvody, proč migraci není možné dokončit.

## <a name="what-happens-if-i-run-into-a-quota-error-while-preparing-the-iaas-resources-for-migration"></a>Co se stane, pokud při přípravě prostředků IaaS na migraci dojde k chybě kvóty? 

Doporučujeme migraci přerušit a pak odeslat žádost o navýšení kvót v oblasti, ve které virtuální počítače migrujete. Po schválení žádosti o kvóty můžete znovu začít provádět kroky migrace.

## <a name="how-do-i-report-an-issue"></a>Jak se dá nahlásit problém? 

Vystavte své problémy a dotazy týkající se migrace na [stránku s otázkou pro virtuální počítač&Microsoft Q](/answers/topics/azure-virtual-machines.html)a klíčovým slovem ClassicIaaSMigration. Doporučujeme, abyste všechny vaše dotazy zveřejnili na tomto fóru. Pokud máte smlouvu o podpoře, můžete také odeslat lístek podpory.

## <a name="what-if-i-dont-like-the-names-of-the-resources-that-the-platform-chose-during-migration"></a>Co když se mi nelíbí názvy prostředků, které platforma zvolila během migrace? 

Všechny prostředky, pro které jste explicitně zadali název v modelu nasazení Classic, se během migrace zachovají. V některých případech se vytvoří nové prostředky. Příklad: pro každý virtuální počítač se vytvoří síťové rozhraní. Aktuálně nepodporujeme možnost upravovat názvy těchto nových prostředků vytvořených během migrace. Hlasujte pro tuto funkci na [fóru Azure pro názory](https://feedback.azure.com).

## <a name="can-i-migrate-expressroute-circuits-used-across-subscriptions-with-authorization-links"></a>Je možné migrovat okruhy ExpressRoute používané napříč předplatnými pomocí autorizačních odkazů? 

Okruhy ExpressRoute používající autorizační odkazy mezi předplatnými není možné automaticky migrovat bez výpadku. Nabízíme doprovodné materiály popisující ruční postup jejich migrace. Postup a další informace najdete v článku [Migrace okruhů ExpressRoute a přidružených virtuálních sítí z modelu nasazení Classic na Resource Manager](../expressroute/expressroute-migration-classic-resource-manager.md).

## <a name="i-got-the-message-vm-is-reporting-the-overall-agent-status-as-not-ready-hence-the-vm-cannot-be-migrated-ensure-that-the-vm-agent-is-reporting-overall-agent-status-as-ready-or-vm-contains-extension-whose-status-is-not-being-reported-from-the-vm-hence-this-vm-cannot-be-migrated"></a>Zobrazila se mi zpráva *"virtuální počítač hlásí celkový stav agenta jako Nepřipraveno. Virtuální počítač proto nelze migrovat. Ujistěte se, že agent virtuálního počítače hlásí celkový stav agenta jako připravený nebo zda* *obsahuje rozšíření, jehož stav není hlášený z virtuálního počítače. Proto tento virtuální počítač nejde migrovat. "*

Tato zpráva se zobrazí v případě, že virtuální počítač nemá odchozí připojení k internetu. Agent virtuálního počítače se pomocí odchozího připojení spojuje s účtem služby Azure Storage, aby každých pět minut aktualizoval stav agenta.


## <a name="next-steps"></a>Další kroky

Pro Linux:

* [Přehled migrace prostředků IaaS podporovaných platformou z klasických na Azure Resource Manager](./linux/migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Podrobné technické informace o platformou podporované migraci z modelu Classic na Azure Resource Manager](./migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Plánování migrace prostředků IaaS z nasazení Classic do Azure Resource Manageru](./linux/migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Použití PowerShellu k migraci prostředků IaaS z modelu Classic na Azure Resource Manager](./windows/migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Migrace prostředků IaaS z modelu Classic na Azure Resource Manager pomocí rozhraní příkazového řádku](./linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Komunitní nástroje pro pomoc s migrací prostředků IaaS z modelu Classic na Azure Resource Manager](./windows/migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Běžné chyby při migraci](./linux/migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Ve Windows:

* [Přehled migrace prostředků IaaS podporovaných platformou z klasických na Azure Resource Manager](./windows/migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Podrobné technické informace o platformou podporované migraci z modelu Classic na Azure Resource Manager](./migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Plánování migrace prostředků IaaS z nasazení Classic do Azure Resource Manageru](./windows/migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Použití PowerShellu k migraci prostředků IaaS z modelu Classic na Azure Resource Manager](./windows/migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Migrace prostředků IaaS z modelu Classic na Azure Resource Manager pomocí rozhraní příkazového řádku](./linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Komunitní nástroje pro pomoc s migrací prostředků IaaS z modelu Classic na Azure Resource Manager](./windows/migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Běžné chyby při migraci](./windows/migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)