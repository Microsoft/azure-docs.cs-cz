---
title: Azure Policy kontroly dodržování předpisů pro Azure Kognitivní hledání
description: Seznam Azure Policy kontrolních mechanismů dodržování předpisů, které jsou k dispozici pro Azure Kognitivní hledání. Tyto integrované definice zásad poskytují běžné přístupy ke správě dodržování předpisů vašich prostředků Azure.
ms.date: 07/30/2020
ms.topic: sample
author: HeidiSteen
ms.author: heidist
ms.service: search
ms.custom: subject-policy-compliancecontrols
ms.openlocfilehash: e030bc86dcdd36e6267345433510a79c5eb13bd0
ms.sourcegitcommit: 5f7b75e32222fe20ac68a053d141a0adbd16b347
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2020
ms.locfileid: "87475001"
---
# <a name="azure-policy-regulatory-compliance-controls-for-azure-cognitive-search"></a>Azure Policy kontroly dodržování předpisů pro Azure Kognitivní hledání

Pokud používáte [Azure Policy](../governance/policy/overview.md) k vymáhání doporučení v [testu zabezpečení Azure](../security/benchmarks/introduction.md), pravděpodobně už víte, že můžete vytvářet zásady pro identifikaci a opravy nekompatibilních služeb. Tyto zásady můžou být vlastní nebo můžou být založené na předdefinovaných definicích, které poskytují kritéria dodržování předpisů a vhodná řešení.

V případě Azure Kognitivní hledání je v současné době jedna integrovaná definice, kterou můžete použít v přiřazení zásady. Integrovaná je pro protokolování a monitorování. Při použití této předdefinované definice v [zásadě, kterou vytvoříte](../governance/policy/assign-policy-portal.md), bude systém vyhledávat vyhledávací služby, které nemají [diagnostické protokolování](search-monitor-logs.md), a pak je povolit odpovídajícím způsobem.

[Dodržování legislativních předpisů v Azure Policy](../governance/policy/concepts/regulatory-compliance.md) poskytuje definice Microsoftem a spravované iniciativy, označované jako _předdefinované_, pro **domény dodržování předpisů** a **bezpečnostní prvky zabezpečení** související s různými standardy dodržování předpisů. Tato stránka obsahuje seznam **domén dodržování předpisů** a **řízení zabezpečení** pro Azure kognitivní hledání. Můžete jim přiřadit předdefinované **ovládací prvky zabezpečení** , které vám pomůžou zajistit, aby prostředky Azure vyhovovaly konkrétnímu standardu.

[!INCLUDE [azure-policy-compliancecontrols-introwarning](../../includes/policy/standards/intro-warning.md)]

[!INCLUDE [azure-policy-compliancecontrols-search](../../includes/policy/standards/byrp/microsoft.search.md)]

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [Azure Policy dodržování předpisů](../governance/policy/concepts/regulatory-compliance.md).
- Projděte si předdefinované možnosti v [úložišti služby Azure Policy na GitHubu](https://github.com/Azure/azure-policy).