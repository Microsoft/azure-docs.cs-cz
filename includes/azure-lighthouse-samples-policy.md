---
title: zahrnout soubor
description: zahrnout soubor
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 10/12/2020
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 20d80fe1a09d388552b6289f8a9b23c878672f94
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91974277"
---
Tyto ukázky ukazují, jak používat Azure Policy s předplatnými, která jsou nasazená do služby Azure Lighthouse.

| **Šablona** | **Popis** |
|---------|---------|
| [policy-add-or-replace-tag](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-add-or-replace-tag) | Přiřadí zásadu, která pro delegované předplatné přidá nebo odebere značku (pomocí efektu modify). Další informace najdete v tématu věnovaném [nasazení zásady, kterou je možné v rámci delegovaného předplatného napravit](../articles/lighthouse/how-to/deploy-policy-remediation.md). |
| [policy-allow-certain-managing-tenants](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-allow-certain-managing-tenants) | Přiřadí zásadu, která omezí delegování Azure Lighthouse na konkrétní spravující tenanty. |
| [policy-audit-delegation](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-audit-delegation) | Přiřadí zásadu, která bude auditovat přiřazení delegování. |
| [policy-delegate-management-groups](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-delegate-management-groups) | Přiřadí zásadu, která potvrzuje, že předplatná v rámci skupiny pro správu byla delegována na tenanta pro správu, a v případě potřeby vytvoří toto přiřazení.
| [policy-enforce-keyvault-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-enforce-keyvault-monitoring) | Přiřadí zásadu, která umožňuje diagnostiku prostředků služby Azure Key Vault v delegovaných předplatných (pomocí efektu deployIfNotExists). Další informace najdete v tématu věnovaném [nasazení zásady, kterou je možné v rámci delegovaného předplatného napravit](../articles/lighthouse/how-to/deploy-policy-remediation.md). |
| [policy-enforce-sub-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-enforce-sub-monitoring) | Přiřadí několik zásad pro umožnění diagnostiky v delegovaném předplatném a připojí všechny virtuální počítače s Windows a Linuxem k pracovnímu prostoru Log Analytics vytvořenému touto zásadou. Další informace najdete v tématu věnovaném [nasazení zásady, kterou je možné v rámci delegovaného předplatného napravit](../articles/lighthouse/how-to/deploy-policy-remediation.md). |
| [policy-initiative](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-initiative) | Aplikuje iniciativu (několik souvisejících definic zásad) na delegované předplatné. |

