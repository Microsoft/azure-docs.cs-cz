---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: 53494bc9642a3df91492d2353a6aa3c6875c1fff
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2020
ms.locfileid: "77172769"
---
|Název |Popis |Vliv (s) |Verze |
|---|---|---|---|
|[Nasazení nastavení diagnostiky pro Key Vault do centra událostí](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Key%20Vault/KeyVault_DiagnosticLog_Deploy.json) |Nasadí nastavení diagnostiky pro Key Vault do služby streamování do místního centra událostí, pokud dojde k vytvoření nebo aktualizaci Key Vault, u kterých chybí tato nastavení diagnostiky. |deployIfNotExists |1.0.0 |
|[Měly by být povolené diagnostické protokoly v Key Vault.](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Key%20Vault/KeyVault_AuditDiagnosticLog_Audit.json) |Audituje povolení diagnostických protokolů. To umožňuje znovu vytvořit stopy aktivity, které se mají použít pro účely šetření. Když dojde k bezpečnostnímu incidentu nebo při ohrožení zabezpečení sítě |AuditIfNotExists, zakázáno |1.0.0 |
|[Key Vault objekty by měly být obnovitelné](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |Tato zásada Audituje, jestli nejsou objekty trezoru klíčů obnovitelné. Funkce obnovitelného odstranění pomáhá efektivně uchovávat prostředky pro danou dobu uchování (90 dní) i po operaci odstranění a zároveň dává dojem, že se objekt odstranil. Pokud je zapnutá možnost vyprázdnění ochrany, trezor nebo objekt v odstraněném stavu nelze odstranit, dokud neuplyne doba uchování 90 dnů. Tyto trezory a objekty můžou být pořád obnoveny, takže zákazníci, kteří budou dodržovat zásady uchovávání informací. |Audit, zakázáno |1.0.0 |
