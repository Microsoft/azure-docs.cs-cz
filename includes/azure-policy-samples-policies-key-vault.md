---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/12/2020
ms.author: dacoulte
ms.openlocfilehash: 7b2179c0a924f7fc29aa70ff748d435e664980ae
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2020
ms.locfileid: "77193128"
---
|Název |Popis |Vliv (s) |Version |
|---|---|---|---|
|[Nasazení nastavení diagnostiky pro Key Vault do centra událostí](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_DiagnosticLog_Deploy.json) |Nasadí nastavení diagnostiky pro Key Vault do služby streamování do místního centra událostí, pokud dojde k vytvoření nebo aktualizaci Key Vault, u kterých chybí tato nastavení diagnostiky. |deployIfNotExists |1.0.0 |
|[Měly by být povolené diagnostické protokoly v Key Vault.](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_AuditDiagnosticLog_Audit.json) |Audituje povolení diagnostických protokolů. To umožňuje znovu vytvořit stopy aktivity, které se mají použít pro účely šetření. Když dojde k bezpečnostnímu incidentu nebo při ohrožení zabezpečení sítě |AuditIfNotExists, zakázáno |1.0.0 |
|[Key Vault objekty by měly být obnovitelné](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |Tato zásada Audituje, jestli nejsou objekty trezoru klíčů obnovitelné. Funkce obnovitelného odstranění pomáhá efektivně uchovávat prostředky pro danou dobu uchování (90 dní) i po operaci odstranění a zároveň dává dojem, že se objekt odstranil. Pokud je zapnutá možnost vyprázdnění ochrany, trezor nebo objekt v odstraněném stavu nelze odstranit, dokud neuplyne doba uchování 90 dnů. Tyto trezory a objekty můžou být pořád obnoveny, takže zákazníci, kteří budou dodržovat zásady uchovávání informací. |Audit, zakázáno |1.0.0 |
