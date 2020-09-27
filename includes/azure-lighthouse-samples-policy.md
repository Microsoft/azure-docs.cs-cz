---
title: zahrnout soubor
description: zahrnout soubor
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 09/24/2020
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: a9c9b5081232988cb4ab80bfcbb9f2d92d1d025f
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91336564"
---
Tyto ukázky ukazují, jak používat Azure Policy s předplatnými, která jsou nasazená do služby Azure Lighthouse.

| **Šablona** | **Popis** |
|---------|---------|
| [policy-add-or-replace-tag](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-add-or-replace-tag) | Přiřadí zásadu, která pro delegované předplatné přidá nebo odebere značku (pomocí efektu modify). Další informace najdete v tématu věnovaném [nasazení zásady, kterou je možné v rámci delegovaného předplatného napravit](../articles/lighthouse/how-to/deploy-policy-remediation.md). |
| [policy-audit-delegation](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-audit-delegation) | Přiřadí zásadu, která bude auditovat přiřazení delegování. |
| [policy-delegate-management-groups](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-delegate-management-groups) | Přiřadí zásadu, která potvrzuje, že předplatná v rámci skupiny pro správu byla delegována na tenanta pro správu, a v případě potřeby vytvoří toto přiřazení.
| [policy-enforce-keyvault-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-enforce-keyvault-monitoring) | Přiřadí zásadu, která umožňuje diagnostiku prostředků služby Azure Key Vault v delegovaných předplatných (pomocí efektu deployIfNotExists). Další informace najdete v tématu věnovaném [nasazení zásady, kterou je možné v rámci delegovaného předplatného napravit](../articles/lighthouse/how-to/deploy-policy-remediation.md). |
| [policy-enforce-sub-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-enforce-sub-monitoring) | Přiřadí několik zásad pro umožnění diagnostiky v delegovaném předplatném a připojí všechny virtuální počítače s Windows a Linuxem k pracovnímu prostoru Log Analytics vytvořenému touto zásadou. Další informace najdete v tématu věnovaném [nasazení zásady, kterou je možné v rámci delegovaného předplatného napravit](../articles/lighthouse/how-to/deploy-policy-remediation.md). |
| [policy-initiative](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-initiative) | Aplikuje iniciativu (několik souvisejících definic zásad) na delegované předplatné. |

