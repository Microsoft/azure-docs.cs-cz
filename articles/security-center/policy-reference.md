---
title: Předdefinované definice zásad pro Azure Security Center
description: Vypíše Azure Policy předdefinované definice zásad pro Azure Security Center. Tyto integrované definice zásad poskytují běžné přístupy ke správě prostředků Azure.
ms.date: 02/09/2021
ms.topic: reference
author: memildin
ms.author: memildin
ms.service: security-center
ms.custom: subject-policy-reference
ms.openlocfilehash: ab6556bc391eb3136fa8c4ba40f5a5d71da956e7
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/10/2021
ms.locfileid: "100093032"
---
# <a name="azure-policy-built-in-definitions-for-azure-security-center"></a>Předdefinované definice Azure Policy pro Azure Security Center

Tato stránka je indexem [Azure Policy](../governance/policy/overview.md) integrovaných definic zásad souvisejících s Azure Security Center. K dispozici jsou následující seskupení definic zásad:

- Skupina [iniciativy](#azure-security-center-initiatives) obsahuje definice Azure Policy iniciativ v kategorii "Security Center".
- [Výchozí skupina iniciativ](#azure-security-center-initiatives) obsahuje všechny definice Azure Policy, které jsou součástí výchozí iniciativy Security Center, [Srovnávací test zabezpečení Azure](../security/benchmarks/introduction.md). Tato společnost Microsoft vytvořila, široce uznávaná sestavení srovnávacích testů na základě kontrol z [centra pro Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) a [National Institute of Standards and Technology (NIST)](https://www.nist.gov/) se zaměřuje na zabezpečení zaměřené na Cloud.
- Skupina [kategorií](#azure-security-center-category) obsahuje seznam všech Azure Policych definic v kategorii "Security Center".

Další informace o zásadách zabezpečení najdete v tématu [práce se zásadami zabezpečení](./tutorial-security-policy.md). Další Azure Policy integrovaných pro jiné služby najdete v tématu [Azure Policy předdefinované definice](../governance/policy/samples/built-in-policies.md).

Název každé předdefinované definice zásad odkazuje na definici zásady v Azure Portal. Pomocí odkazu ve sloupci **verze** Zobrazte zdroj v [úložišti Azure Policy GitHubu](https://github.com/Azure/azure-policy).

## <a name="azure-security-center-initiatives"></a>Azure Security Center iniciativ

Další informace o integrovaných iniciativách, které jsou monitorovány nástrojem Security Center, naleznete v následující tabulce:

[!INCLUDE [azure-policy-reference-policyset-security-center](../../includes/policy/reference/bycat/policysets-security-center.md)]

## <a name="azure-security-center-default-initiative"></a>Azure Security Center výchozí iniciativa

Další informace o předdefinovaných zásadách sledovaných nástrojem Security Center naleznete v následující tabulce:

[!INCLUDE [azure-policy-reference-init-asc](../../includes/policy/reference/custom/init-asc.md)]

## <a name="azure-security-center-category"></a>Kategorie Azure Security Center

[!INCLUDE [azure-policy-reference-category-securitycenter](../../includes/policy/reference/bycat/policies-security-center.md)]

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli o Azure Policy definice zásad zabezpečení v Security Center. Další informace najdete v následujících článcích.

- Projděte si předdefinované možnosti v [úložišti služby Azure Policy na GitHubu](https://github.com/Azure/azure-policy).
- Projděte si [strukturu definic Azure Policy](../governance/policy/concepts/definition-structure.md).
- Projděte si [Vysvětlení efektů zásad](../governance/policy/concepts/effects.md).
- [Průvodce plánováním a provozem Azure Security Center](./security-center-planning-and-operations-guide.md): Naučte se plánovat a pochopit hlediska návrhu v Azure Security Center.
- [Sledování stavu zabezpečení v Azure Security Center](./security-center-monitoring.md) – Naučte se sledovat stav svých prostředků Azure.
- [Správa a zpracování výstrah zabezpečení v Azure Security Center](./security-center-managing-and-responding-alerts.md) – Zjistěte, jak spravovat výstrahy zabezpečení a reagovat na ně.
- [Sledování partnerských řešení pomocí Azure Security Center](./security-center-partner-integration.md) – Zjistěte, jak pomocí Azure Security Center sledovat stav vašich partnerských řešení.
- [Azure Policy](../governance/policy/overview.md): Naučte se auditovat a řídit prostředky Azure.
