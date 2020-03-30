---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: tanmaygore
ms.service: virtual-machines
ms.topic: include
ms.date: 02/06/2020
ms.author: tagore
ms.custom: include file
ms.openlocfilehash: 57469bef7014010164234638f3d059ac96b125cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78383959"
---
## <a name="what-is-the-time-required-for-migration"></a>Jaký je čas potřebný pro migraci?

Plánování a realizace migrace do značné míry závisí na složitosti architektury a může trvat několik měsíců.  

## <a name="what-is-the-definition-of-a-new-customer-on-iaas-vms-classic"></a>Jaká je definice nového zákazníka na virtuálních počítačích IaaS (klasické)?

Zákazníci, kteří neměli v roce 2020 ve svých předplatných virtuální chvat YaaS (klasické) (klasické) jsou považováni za nové zákazníky. 

## <a name="what-is-the-definition-of-an-existing-customer-on-iaas-virtual-machines-classic"></a>Jaká je definice existujícího zákazníka na virtuálních počítačích IaaS (klasická)?

Zákazník, který měl aktivní nebo zastavené, ale přidělil virtuální ms IaaS (Classic) ve svých předplatných v měsíci únoru 2020, se považuje za stávajícího zákazníka. Jenom tito zákazníci se dostanou do 1. 

## <a name="why-am-i-getting-an-error-stating-newclassicvmcreationnotallowedforsubscription"></a>Proč se zobrazuje chyba s uvedením "NewClassicVMCreationNotAllowedForSubscription"?

V rámci procesu odchodu do důchodu, IaaS VM (klasické) již nejsou k dispozici pro nové zákazníky. Identifikovali jsme vás jako nové zákazníky, a proto váš provoz nebyl autorizován. Důrazně doporučujeme používat [virtuální počítače Azure pomocí ARM](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-powershell). Pokud nemůžete používat virtuální počítače Azure pomocí ARM, obraťte se na podporu pro odběr whitelisting.

## <a name="does-this-migration-plan-affect-any-of-my-existing-services-or-applications-that-run-on-azure-virtual-machines"></a>Má tento plán migrace vliv na některé stávající služby nebo aplikace spuštěné na virtuálních počítačích Azure? 

Až 1. března 2023 pro virtuální zařízení IaaS (klasické). Virtuální moduly IaaS (klasické) jsou plně podporované služby v obecné dostupnosti. I nadále můžete tyto prostředky používat k rozšíření vaší působnosti v systému Microsoft Azure. března 1st, 2023 tyto virtuální počítače budou plně vyřazené a všechny aktivní nebo přidělené virtuální chody se zastaví & přidělené. Nebude mít žádný vliv na jiné klasické prostředky, jako jsou cloudové služby (Classic), účty úložiště (Classic) atd.   

## <a name="what-happens-to-my-vms-if-i-dont-plan-on-migrating-in-the-near-future"></a>Co se stane s virtuálními počítači, pokud se je nechystám migrovat v blízké budoucnosti? 

1. března 2023 budou virtuální počítači IaaS (Classic) plně vyřazené a všechny aktivní nebo přidělené virtuální počítače budou zastaveny & přidělené. Abychom předešli dopadu na podnikání, důrazně doporučujeme, abyste migraci mohli začít plánovat ještě dnes a dokončit ji do 1. března 2023. Nejsme zastaralá existující klasická api, cloudové služby a model prostředků. Vzhledem k pokročilým funkcím dostupným v modelu nasazení Resource Manager chceme migraci usnadnit. Doporučujeme začít plánovat migraci těchto prostředků do Správce prostředků Azure. 

## <a name="what-does-this-migration-plan-mean-for-my-existing-tooling"></a>Co tento plán migrace znamená pro stávající nástroje? 

Aktualizace nástrojů na model nasazení Resource Manager je jedna z nejdůležitějších změn, se kterou v plánech migrace musíte počítat.

## <a name="how-long-will-the-management-plane-downtime-be"></a>Jak dlouho bude trvat výpadek roviny správy? 

To závisí na počtu prostředků, které se migrují. U menších nasazení (několik desítek virtuálních počítačů) by celá migrace měla trvat méně než hodinu. U rozsáhlých nasazení (stovky virtuálních počítačů) může migrace trvat několik hodin.

## <a name="can-i-roll-back-after-my-migrating-resources-are-committed-in-resource-manager"></a>Dají se vrátit změny po potvrzení migrovaných prostředků v Resource Manageru? 

Migraci můžete přerušit, dokud jsou prostředky v připraveném stavu. Po úspěšné migraci prostředků prostřednictvím operace potvrzení se vrácení zpět nepodporuje.

## <a name="can-i-roll-back-my-migration-if-the-commit-operation-fails"></a>Je možné vrátit migraci zpět v případě, že selže operace potvrzení? 

Pokud selže operace potvrzení, migraci není možné přerušit. Všechny operace migrace, včetně operace potvrzení, jsou idempotentní. Proto doporučujeme, abyste operaci po chvilce zkusili opakovat. Pokud stále čelíte chybě, vytvořte lístek podpory.

## <a name="do-i-have-to-buy-another-express-route-circuit-if-i-have-to-use-iaas-under-resource-manager"></a>Je nutné koupit další okruh ExpressRoute, když potřebuji používat infrastrukturu jako službu (IaaS) v modelu Resource Manager? 

Ne. Nedávno jsme povolili [přesun okruhů ExpressRoute z modelu nasazení Classic na Resource Manager](../articles/expressroute/expressroute-move.md). Pokud už máte okruh ExpressRoute, není nutné kupovat nový.

## <a name="what-if-i-had-configured-role-based-access-control-policies-for-my-classic-iaas-resources"></a>Co když byly pro prostředky IaaS v modelu Classic nakonfigurované zásady řízení přístupu na základě role? 

Během migrace se prostředky transformují z modelu Classic na Resource Manager. Proto doporučujeme naplánovat aktualizace zásad řízení přístupu na základě role, které je třeba provést, až po migraci.

## <a name="i-backed-up-my-classic-vms-in-a-vault-can-i-migrate-my-vms-from-classic-mode-to-resource-manager-mode-and-protect-them-in-a-recovery-services-vault"></a>Své klasické virtuální aplikace jsem zálohoval v trezoru. Můžu migrovat svoje virtuální počítače z klasického režimu do režimu Resource Manageru a chránit je pomocí trezoru služby Recovery Services?

Když přesunete virtuální ho z klasického režimu do režimu Správce prostředků, zálohy před migrací nebudou migrovat do nově migrovaného virtuálního počítače Správce prostředků. Pokud si ale chcete zachovat zálohy klasických virtuálních počítačů, postupujte před migrací podle těchto kroků. 

1. V trezoru Služby pro obnovení přejděte na kartu **Chráněné položky** a vyberte virtuální počítač. 
2. Klikněte na Zastavit ochranu. Políčko *Delete associated backup data* (Odstranit přidružená data záloh) ponechte **nezaškrtnuté**.

> [!NOTE]
> Náklady na instanci zálohování vám budou účtovány, dokud neuchováte data. Záložní kopie budou vyřazovány podle rozsahu uchování. Poslední záložní kopie je však vždy zachována, dokud explicitně neodstraníte záložní data. Doporučujeme zkontrolovat rozsah uchování virtuálního počítače a aktivovat "Odstranit záložní data" na chráněné položce v trezoru, jakmile rozsah uchování je u konce. 
>
>

Chcete-li přenést virtuální počítač do režimu Správce prostředků, 

1. Odstraňte z virtuálního počítače zálohu/rozšíření snímků.
2. Proveďte migraci virtuálního počítače z klasického režimu do režimu Resource Manageru. Ověřte, že se do režimu Resource Manager migruje také úložiště a informace o síti odpovídající tomuto virtuálnímu počítači.

Navíc pokud chcete zálohovat migrovaný virtuální počítač, přejděte na okno pro správu virtuálního počítače [a povolte zálohování](../articles/backup/quick-backup-vm-portal.md#enable-backup-on-a-vm).

## <a name="can-i-validate-my-subscription-or-resources-to-see-if-theyre-capable-of-migration"></a>Je možné ověřit, jestli jsou prostředky nebo předplatné schopné migrace? 

Ano. U možnosti migrace s podporou platformy je prvním krokem přípravy na migraci ověření, že jsou prostředky schopné migrace. V případě selhání operace ověření se zobrazí zprávy se všemi důvody, proč migraci není možné dokončit.

## <a name="what-happens-if-i-run-into-a-quota-error-while-preparing-the-iaas-resources-for-migration"></a>Co se stane, pokud při přípravě prostředků IaaS na migraci dojde k chybě kvóty? 

Doporučujeme migraci přerušit a pak odeslat žádost o navýšení kvót v oblasti, ve které virtuální počítače migrujete. Po schválení žádosti o kvóty můžete znovu začít provádět kroky migrace.

## <a name="how-do-i-report-an-issue"></a>Jak se dá nahlásit problém? 

Problémy a dotazy týkající se migrace zveřejněte na našem [fóru k virtuálním počítačům](https://social.msdn.microsoft.com/Forums/azure/home?forum=WAVirtualMachinesforWindows) s použitím klíčového slova ClassicIaaSMigration. Doporučujeme, abyste všechny vaše dotazy zveřejnili na tomto fóru. Pokud máte smlouvu o podpoře, můžete také odeslat lístek podpory.

## <a name="what-if-i-dont-like-the-names-of-the-resources-that-the-platform-chose-during-migration"></a>Co když se mi nelíbí názvy prostředků, které platforma zvolila během migrace? 

Všechny prostředky, pro které jste explicitně zadali název v modelu nasazení Classic, se během migrace zachovají. V některých případech se vytvoří nové prostředky. Příklad: pro každý virtuální počítač se vytvoří síťové rozhraní. Aktuálně nepodporujeme možnost upravovat názvy těchto nových prostředků vytvořených během migrace. Hlasujte pro tuto funkci na [fóru Azure pro názory](https://feedback.azure.com).

## <a name="can-i-migrate-expressroute-circuits-used-across-subscriptions-with-authorization-links"></a>Je možné migrovat okruhy ExpressRoute používané napříč předplatnými pomocí autorizačních odkazů? 

Okruhy ExpressRoute používající autorizační odkazy mezi předplatnými není možné automaticky migrovat bez výpadku. Nabízíme doprovodné materiály popisující ruční postup jejich migrace. Postup a další informace najdete v článku [Migrace okruhů ExpressRoute a přidružených virtuálních sítí z modelu nasazení Classic na Resource Manager](../articles/expressroute/expressroute-migration-classic-resource-manager.md).

## <a name="i-got-the-message-vm-is-reporting-the-overall-agent-status-as-not-ready-hence-the-vm-cannot-be-migrated-ensure-that-the-vm-agent-is-reporting-overall-agent-status-as-ready-or-vm-contains-extension-whose-status-is-not-being-reported-from-the-vm-hence-this-vm-cannot-be-migrated"></a>Dostal jsem zprávu *"Virtuální měsíč hlásí celkový stav agenta jako Není připraven. Proto virtuální ho nelze migrovat. Ujistěte se, že agent virtuálního soudu hlásí celkový stav agenta jako připravený"* nebo *"Virtuální jazyk obsahuje rozšíření, jehož stav není hlášen z virtuálního soudu. Proto tento virtuální virtuální ms nelze migrovat."*

Tato zpráva se zobrazí v případě, že virtuální počítač nemá odchozí připojení k internetu. Agent virtuálního počítače se pomocí odchozího připojení spojuje s účtem služby Azure Storage, aby každých pět minut aktualizoval stav agenta.
