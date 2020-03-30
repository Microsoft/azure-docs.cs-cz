---
author: memildin
ms.author: memildin
manager: rkarlin
ms.date: 02/24/2020
ms.topic: include
ms.openlocfilehash: c77849b2285283a34e6adf84dc3845a4076407af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77597932"
---
## <a name="attack-scenario"></a>Scénář útoku

Útoky hrubou silou se běžně zaměřují na porty pro správu jako prostředek k získání přístupu k virtuálnímu virtuálnímu serveru. Pokud bude úspěšný, útočník může převzít kontrolu nad virtuálním počítačem a vytvořit oporu ve vašem prostředí.

Jedním ze způsobů, jak snížit expozici útoku hrubou silou, je omezit dobu, po kterou je port otevřen. Porty pro správu nemusí být vždy otevřené. Musí být otevřené jenom v době, kdy jste připojení k virtuálnímu virtuálnímu zařízení, například k provádění úloh správy nebo údržby. Pokud je povolena just-in-time, Security Center používá [skupiny zabezpečení sítě](../articles/virtual-network/security-overview.md#security-rules) (NSG) a Azure Firewall pravidla, která omezují přístup k porty pro správu, takže nemohou být cílem útočníků.

![Scénář just-in-time](../articles/security-center/media/security-center-just-in-time/just-in-time-scenario.png)

## <a name="how-does-jit-access-work"></a>Jak funguje přístup k JIT?

Když je povoleno just-in-time, Security Center uzamkne příchozí provoz na vaše virtuální počítače Azure vytvořením pravidla skupiny zabezpečení. Vyberete porty na virtuálním počítači, na které bude uzamčen příchozí provoz. Tyto porty jsou řízeny řešením just-in-time.

Když uživatel požádá o přístup k virtuálnímu virtuálnímu zařízení, Centrum zabezpečení zkontroluje, zda má uživatel oprávnění [řízení přístupu na základě rolí (RBAC)](../articles/role-based-access-control/role-assignments-portal.md) pro tento virtuální hod. Pokud je požadavek schválen, Security Center automaticky nakonfiguruje skupiny zabezpečení sítě (NSGs) a Azure Firewall povolit příchozí provoz na vybrané porty a požadované zdrojové IP adresy nebo rozsahy, po dobu, která byla zadána. Po uplynutí doby centrum zabezpečení obnoví skupiny zabezpečení do svých předchozích stavů. Tato připojení, která jsou již vytvořena, však nejsou přerušena.

 > [!NOTE]
 > Pokud je žádost o přístup JIT schválena pro virtuální počítač za bránou Azure Firewall, centrum zabezpečení automaticky změní pravidla zásad skupiny zabezpečení i brány firewall. Po dobu, která byla zadána, pravidla umožňují příchozí provoz na vybrané porty a požadované zdrojové IP adresy nebo rozsahy. Po uplynutí doby centrum zabezpečení obnoví bránu firewall a pravidla skupiny zabezpečení sítě do předchozích stavů.


## <a name="permissions-needed-to-configure-and-use-jit"></a>Oprávnění potřebná ke konfiguraci a použití JIT

| Chcete-li uživateli povolit: | Oprávnění k nastavení|
| --- | --- |
| Konfigurace nebo úprava zásad JIT pro virtuální počítače | *Přiřazení těchto akcí k roli:*  <ul><li>V oboru předplatného nebo skupiny prostředků, která je přidružená k virtuálnímu počítači:<br/> `Microsoft.Security/locations/jitNetworkAccessPolicies/write` </li><li> V oboru předplatného nebo skupiny prostředků virtuálního počítači: <br/>`Microsoft.Compute/virtualMachines/write`</li></ul> | 
|Žádost o přístup JIT k virtuálnímu virtuálnímu serveru | *Přiřaďte uživateli tyto akce:*  <ul><li>V oboru předplatného nebo skupiny prostředků, která je přidružená k virtuálnímu počítači:<br/>  `Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action` </li><li>V oboru předplatného nebo skupiny prostředků, která je přidružená k virtuálnímu počítači:<br/>  `Microsoft.Security/locations/jitNetworkAccessPolicies/*/read` </li><li>  V oboru předplatného nebo skupiny prostředků nebo virtuálního počítače:<br/> `Microsoft.Compute/virtualMachines/read` </li><li>  V oboru předplatného nebo skupiny prostředků nebo virtuálního počítače:<br/> `Microsoft.Network/networkInterfaces/*/read` </li></ul>|