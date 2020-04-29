---
author: memildin
ms.author: memildin
manager: rkarlin
ms.date: 02/24/2020
ms.topic: include
ms.openlocfilehash: c77849b2285283a34e6adf84dc3845a4076407af
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "77597932"
---
## <a name="attack-scenario"></a>Scénář útoku

Útoky hrubou silou běžně cílí na porty správy jako prostředky pro získání přístupu k virtuálnímu počítači. V případě úspěchu může útočník převzít kontrolu nad VIRTUÁLNÍm počítačem a vytvořit dostane do vašeho prostředí.

Jedním ze způsobů, jak omezit vystavení útokem hrubou silou, je omezit dobu, po kterou je port otevřený. Porty pro správu není nutné otevírat po všech časech. Stačí je otevřít, jenom když jste připojeni k virtuálnímu počítači, například provádět úlohy správy nebo údržby. Pokud je povolený program just-in-time, Security Center používá pravidla [skupiny zabezpečení sítě](../articles/virtual-network/security-overview.md#security-rules) (NSG) a Azure firewall, která omezují přístup k portům pro správu, aby na ně nedokázali zacílit útočníci.

![Scénář za běhu](../articles/security-center/media/security-center-just-in-time/just-in-time-scenario.png)

## <a name="how-does-jit-access-work"></a>Jak funguje přístup JIT?

Když je povolený postup za běhu, Security Center zamkne příchozí provoz do vašich virtuálních počítačů Azure vytvořením pravidla NSG. Vyberete porty na virtuálním počítači, pro které bude příchozí provoz uzamčen. Tyto porty jsou ovládány pomocí řešení za běhu.

Když si uživatel požádá o přístup k virtuálnímu počítači, Security Center zkontroluje, jestli má uživatel pro tento virtuální počítač oprávnění [Access Control na základě role (RBAC)](../articles/role-based-access-control/role-assignments-portal.md) . Pokud je žádost schválená, Security Center automaticky nakonfiguruje skupiny zabezpečení sítě (skupin zabezpečení sítě) a Azure Firewall tak, aby povolovaly příchozí provoz na vybraných portech a požadované zdrojové IP adresy nebo rozsahy po dobu, kterou jste zadali. Po vypršení časového limitu Security Center obnoví skupin zabezpečení sítě do jejich předchozích stavů. Připojení, která jsou již navázána, se však přeruší.

 > [!NOTE]
 > Pokud je žádost o přístup JIT schválená pro virtuální počítač za Azure Firewall, Security Center automaticky změní obě pravidla zásad NSG i brány firewall. V době, kterou jste zadali, budou pravidla umožňovat příchozí provoz na vybrané porty a požadované zdrojové IP adresy nebo rozsahy. Po uplynutí této doby Security Center obnoví v předchozích stavech pravidla brány firewall a NSG.


## <a name="permissions-needed-to-configure-and-use-jit"></a>Oprávnění potřebná ke konfiguraci a použití JIT

| Umožnění uživateli: | Oprávnění k nastavení|
| --- | --- |
| Konfigurace nebo úprava zásad JIT pro virtuální počítač | *Přiřaďte k roli tyto akce:*  <ul><li>V oboru předplatného nebo skupiny prostředků, která je přidružená k virtuálnímu počítači:<br/> `Microsoft.Security/locations/jitNetworkAccessPolicies/write` </li><li> V oboru předplatného nebo skupiny prostředků virtuálního počítače: <br/>`Microsoft.Compute/virtualMachines/write`</li></ul> | 
|Vyžádat přístup JIT k virtuálnímu počítači | *Přiřaďte uživatele k těmto akcím:*  <ul><li>V oboru předplatného nebo skupiny prostředků, která je přidružená k virtuálnímu počítači:<br/>  `Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action` </li><li>V oboru předplatného nebo skupiny prostředků, která je přidružená k virtuálnímu počítači:<br/>  `Microsoft.Security/locations/jitNetworkAccessPolicies/*/read` </li><li>  V oboru předplatného nebo skupiny prostředků nebo virtuálního počítače:<br/> `Microsoft.Compute/virtualMachines/read` </li><li>  V oboru předplatného nebo skupiny prostředků nebo virtuálního počítače:<br/> `Microsoft.Network/networkInterfaces/*/read` </li></ul>|