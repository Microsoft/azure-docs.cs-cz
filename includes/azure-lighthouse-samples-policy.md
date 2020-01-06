---
title: zahrnout soubor
description: zahrnout soubor
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 12/19/2019
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 55eee87ed1e07b61a7f8287e86ddc0a7a6aa4df9
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75456757"
---
Tyto ukázky ukazují, jak používat Azure Policy s předplatnými, která jsou nasazená pro správu delegovaných prostředků Azure.

| **Šablona** | **Popis** |
|---------|---------|
| [policy-add-or-replace-tag](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-add-or-replace-tag) | Přiřadí zásadu, která pro delegované předplatné přidá nebo odebere značku (pomocí efektu modify). Další informace najdete v tématu věnovaném [nasazení zásady, kterou je možné v rámci delegovaného předplatného napravit](../articles/lighthouse/how-to/deploy-policy-remediation.md). |
| [policy-audit-delegation](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-audit-delegation) | Přiřadí zásadu, která bude auditovat přiřazení delegování. |
| [policy-enforce-keyvault-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-enforce-keyvault-monitoring) | Přiřadí zásadu, která umožňuje diagnostiku prostředků služby Azure Key Vault v delegovaných předplatných (pomocí efektu deployIfNotExists). Další informace najdete v tématu věnovaném [nasazení zásady, kterou je možné v rámci delegovaného předplatného napravit](../articles/lighthouse/how-to/deploy-policy-remediation.md). |
| [policy-enforce-sub-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-enforce-sub-monitoring) | Přiřadí několik zásad pro umožnění diagnostiky v delegovaném předplatném a připojí všechny virtuální počítače s Windows a Linuxem k pracovnímu prostoru Log Analytics vytvořenému touto zásadou. Další informace najdete v tématu věnovaném [nasazení zásady, kterou je možné v rámci delegovaného předplatného napravit](../articles/lighthouse/how-to/deploy-policy-remediation.md). |
| [policy-initiative](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-initiative) | Aplikuje iniciativu (několik souvisejících definic zásad) na delegované předplatné. |

