---
title: Nejčastější dotazy k Azure automanage pro virtuální počítače
description: Odpovědi na nejčastější dotazy týkající se služby Azure automanage pro virtuální počítače.
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: troubleshooting
ms.date: 09/04/2020
ms.author: deanwe
ms.openlocfilehash: 003f97c99de7dd4be79e820e822b6071f45ed146
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91714961"
---
# <a name="frequently-asked-questions-for-azure-automanage-for-vms"></a>Nejčastější dotazy k Azure automanage pro virtuální počítače

Tento článek obsahuje odpovědi na některé nejběžnější dotazy týkající se [služby Azure automanage pro virtuální počítače](automanage-virtual-machines.md).

Pokud váš problém s Azure není v tomto článku řešen, navštivte fóra Azure na [webu MSDN a Stack Overflow](https://azure.microsoft.com/support/forums/). Svůj problém můžete vystavit na těchto fórech nebo odeslat na [ @AzureSupport Twitter](https://twitter.com/AzureSupport). Můžete také odeslat žádost o podporu Azure. Pokud chcete odeslat žádost o podporu, vyberte na [stránce podpory Azure](https://azure.microsoft.com/support/options/)možnost **získat podporu**.


## <a name="azure-automanage-for-virtual-machines"></a>Azure automanage pro virtuální počítače

**Jaké jsou všechny požadavky nutné k povolení Azure automanage?**

Níže jsou uvedené předpoklady pro povolení služby Azure automanage:
- Jenom virtuální počítače s Windows serverem
- Musí být spuštěny virtuální počítače.
- Virtuální počítače musí být v podporované oblasti.
- Uživatel musí mít správná oprávnění.
- Jenom virtuální počítače bez škálování
- Virtuální počítače nesmí odkazovat na pracovní prostor Log Analytics v jiném předplatném.
- Automanage v tuto chvíli nepodporuje odběry izolovaného prostoru (sandbox).

**Jaká oprávnění Azure RBAC je potřeba k povolení automanage?**

Pokud povolíte možnost automanage na virtuálním počítači s existujícím účtem pro správu, budete potřebovat roli přispěvatele pro skupinu prostředků, ve které se virtuální počítač nachází.

Pokud při povolování používáte nový účet automanage, musíte mít roli vlastníka nebo mít k předplatnému roli správce přístupu Přispěvatel + uživatel.


**Které oblasti jsou podporovány?**

Podporovány jsou virtuální počítače v následujících oblastech: Západní Evropa, Východní USA, Západní USA 2, Kanada – střed, Středozápadní USA.


**Které funkce automatizuje Azure automanage?**

Automaticky spravujte registraci, konfiguraci a monitorování v průběhu životního cyklu virtuálního počítače. [zde](virtual-machines-best-practices.md)jsou uvedené služby.

**Funguje Azure automanage s virtuálními počítači s podporou ARC Azure?**

Služba automanage aktuálně nepodporuje virtuální počítače s podporou ARC.

**Můžu přizpůsobit konfigurace v Azure automanage?**

Zákazníci mohou přizpůsobit nastavení pro konkrétní služby, jako je například uchovávání Azure Backup, prostřednictvím předvoleb konfigurace. Úplný seznam nastavení, která se dají změnit, najdete v [naší dokumentaci.](virtual-machines-best-practices.md)


**Funguje Azure automanage i s virtuálními počítači se systémy Linux a Windows?**

V současné době umožňuje automanage virtuální počítače Azure s Windows serverem.


**Můžu u sebe selektivně použít jenom tuto sadu virtuálních počítačů?**

U vybraných nových a existujících virtuálních počítačů je možné povolit možnost automanage s možností jednoduchého kliknutí. Možnost automanage je také možné kdykoli zakázat.


**Podporuje Azure automanage virtuální počítače v sadě škálování virtuálních počítačů?**

Ne, služba Azure automanage v současné době nepodporuje virtuální počítače v sadě škálování virtuálního počítače.


**Jak spousta Azure provádí náklady na správu?**

Azure automanage je k dispozici bez dalších nákladů ve verzi Public Preview. Připojením prostředků Azure, jako je například Azure Backup, se účtují náklady.


**Je možné použít možnost automanage prostřednictvím zásad Azure?**

Ano, máme vestavěnou zásadu, která automaticky aplikuje správu na všechny virtuální počítače v rámci definovaného oboru. Spolu s vaším účtem pro vlastní správu také určíte konfigurační profil (DevTest nebo produkční). Další informace o povolení automanage prostřednictvím zásad Azure [najdete tady](virtual-machines-policy-enable.md).


**Co je účet pro autosprávu?**

Účet automatické správy je soubor MSI (Identita spravované služby), který poskytuje kontext zabezpečení nebo identitu, pod kterou se automatizované operace vyskytují.


**Při povolování automanage má vliv na všechny další virtuální počítače kromě vybraných virtuálních počítačů?**

Pokud je váš virtuální počítač propojený s existujícím pracovním prostorem Log Analytics, bude možné tento pracovní prostor použít k použití těchto řešení: Change Tracking, inventarizaci a Update Management. Všem virtuálním počítačům připojeným k tomuto pracovnímu prostoru budou tato řešení povolena.


**Můžu změnit konfigurační profil mého virtuálního počítače?**

V tuto chvíli budete muset pro tento virtuální počítač Zakázat možnost automanage a potom znovu povolit automanage s požadovaným konfiguračním profilem a preferencemi.


**Pokud je můj virtuální počítač už nakonfigurovaný pro službu, třeba Update Management, provede to, aby se znovu nakonfigurovala.**
Ne, automanage ji nebude znovu konfigurovat. Začneme monitorovat prostředky přidružené k této službě za účelem posunu.


**Proč je můj virtuální počítač v portálu pro správu, který je na webu pro správu, stav neúspěchu?**

Pokud se stav zobrazuje jako *neúspěšný*, můžete řešit problémy s nasazením prostřednictvím skupiny prostředků, ve které se váš virtuální počítač nachází. Přejděte na **skupiny prostředků**, vyberte svoji skupinu prostředků, klikněte na **nasazení** a v části stav *selhání se* zobrazí podrobnosti o chybě.

**Jak můžu získat podporu při řešení potíží pro autosprávu?**

Můžete zasouborovat [lístek případu technické podpory](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). U možnosti **Služba** vyhledejte a v části *monitorování a Správa* vyberte možnost *Spravovat* .


## <a name="next-steps"></a>Další kroky

Zkuste povolit automanage pro virtuální počítače v Azure Portal.

> [!div class="nextstepaction"]
> [Povolit automanage pro virtuální počítače v Azure Portal](quick-create-virtual-machines-portal.md)