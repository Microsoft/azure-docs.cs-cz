---
title: Předdefinované definice zásad pro Azure Security Center
description: Vypíše Azure Policy předdefinované definice zásad pro Azure Security Center. Tyto integrované definice zásad poskytují běžné přístupy ke správě prostředků Azure.
ms.date: 03/05/2021
ms.topic: reference
author: memildin
ms.author: memildin
ms.service: security-center
ms.custom: subject-policy-reference
ms.openlocfilehash: 2af70348edc7d74bac89111a8409049cae7a3ae0
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2021
ms.locfileid: "102428051"
---
# <a name="azure-policy-built-in-definitions-for-azure-security-center"></a>Předdefinované definice Azure Policy pro Azure Security Center

Tato stránka je indexem [Azure Policy](../governance/policy/overview.md) integrovaných definic zásad souvisejících s Azure Security Center. K dispozici jsou následující seskupení definic zásad:

- Skupina [iniciativy](#azure-security-center-initiatives) obsahuje definice Azure Policy iniciativ v kategorii "Security Center".
- [Výchozí skupina iniciativ](#azure-security-center-initiatives) obsahuje všechny definice Azure Policy, které jsou součástí výchozí iniciativy Security Center, [Srovnávací test zabezpečení Azure](../security/benchmarks/introduction.md). Tato společnost Microsoft vytvořila, široce uznávaná sestavení srovnávacích testů na základě kontrol z [centra pro Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) a [National Institute of Standards and Technology (NIST)](https://www.nist.gov/) se zaměřuje na zabezpečení zaměřené na Cloud.
- Skupina [kategorií](#azure-security-center-category) uvádí všechny definice Azure Policy v kategorii "Security Center".

Další informace o zásadách zabezpečení najdete v tématu [práce se zásadami zabezpečení](./tutorial-security-policy.md). Další Azure Policy integrovaných pro jiné služby najdete v tématu [Azure Policy předdefinované definice](../governance/policy/samples/built-in-policies.md).

Název každé předdefinované definice zásad odkazuje na definici zásady v Azure Portal. Pomocí odkazu ve sloupci **verze** Zobrazte zdroj v [úložišti Azure Policy GitHubu](https://github.com/Azure/azure-policy).

## <a name="azure-security-center-initiatives"></a>Azure Security Center iniciativ

Další informace o integrovaných iniciativách, které jsou monitorovány nástrojem Security Center, naleznete v následující tabulce:

[!INCLUDE [azure-policy-reference-policyset-security-center](../../includes/policy/reference/bycat/policysets-security-center.md)]

## <a name="security-centers-default-initiative-azure-security-benchmark"></a>Výchozí iniciativa Security Center (srovnávací test zabezpečení Azure)

Další informace o předdefinovaných zásadách sledovaných nástrojem Security Center naleznete v následující tabulce:

[!INCLUDE [azure-policy-reference-init-asc](../../includes/policy/reference/custom/init-asc.md)]

## <a name="azure-security-center-category"></a>Kategorie Azure Security Center

[!INCLUDE [azure-policy-reference-category-securitycenter](../../includes/policy/reference/bycat/policies-security-center.md)]

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli o Azure Policy definice zásad zabezpečení v Security Center. Další informace o iniciativách, zásadách a způsobu jejich vztahu k doporučením Security Center najdete v tématu [co jsou zásady zabezpečení, iniciativy a doporučení?](security-policy-concept.md).
