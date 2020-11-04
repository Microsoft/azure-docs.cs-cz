---
title: Auditovat a spravovat dodržování zásad
titleSuffix: Azure Machine Learning
description: Naučte se používat Azure Policy k používání předdefinovaných zásad pro Azure Machine Learning, abyste se ujistili, že vaše pracovní prostory vyhovují vašim požadavkům.
author: jhirono
ms.author: jhirono
ms.date: 09/15/2020
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.openlocfilehash: 47a97bb8fbe52d3dde84afd77997b179a5c9248d
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93325499"
---
# <a name="audit-and-manage-azure-machine-learning-using-azure-policy"></a>Audit a Správa Azure Machine Learning pomocí Azure Policy

[Azure Policy](../governance/policy/index.yml) je nástroj zásad správného řízení, který vám umožní zajistit, aby prostředky Azure vyhovovaly vašim zásadám. Pomocí Azure Machine Learning můžete přiřadit následující zásady:

* **Klíč spravovaný zákazníkem** : audit nebo vymáhání, jestli musí pracovní prostory používat klíč spravovaný zákazníkem.
* **Privátní odkaz** : Audituje, jestli pracovní prostory používají privátní koncový bod ke komunikaci s virtuální sítí.

Zásady je možné nastavit v různých oborech, například na úrovni předplatného nebo skupiny prostředků. Další informace najdete v dokumentaci k [Azure Policy](../governance/policy/overview.md).

## <a name="built-in-policies"></a>Předdefinované zásady

Azure Machine Learning poskytuje sadu zásad, které můžete použít pro běžné scénáře s Azure Machine Learning. Tyto definice zásad můžete přiřadit k existujícímu předplatnému nebo je použít jako základ pro vytvoření vlastních definicí. Úplný seznam předdefinovaných zásad pro Azure Machine Learning najdete v tématu [předdefinované zásady pro Azure Machine Learning](../governance/policy/samples/built-in-policies.md#machine-learning).

Pokud chcete zobrazit integrované definice zásad související s Azure Machine Learning, použijte následující postup:

1. V [Azure Portal](https://portal.azure.com)přejít na __Azure Policy__ .
1. Vyberte __definice__.
1. Pro __typ__ vyberte možnost _předdefinované_ a pro __kategorie__ vyberte možnost __Machine Learning__.

Tady můžete vybrat definice zásad a zobrazit je. Při prohlížení definice můžete použít odkaz __přiřadit__ a přiřadit zásadu ke konkrétnímu oboru a nakonfigurovat parametry pro tyto zásady. Další informace najdete v tématu [přiřazení portálu pro zásady](../governance/policy/assign-policy-portal.md).

Zásady můžete přiřadit také pomocí [Azure PowerShell](../governance/policy/assign-policy-powershell.md), [Azure CLI](../governance/policy/assign-policy-azurecli.md)a [šablon](../governance/policy/assign-policy-template.md).

## <a name="workspaces-encryption-with-customer-managed-key"></a>Šifrování pracovních prostorů pomocí klíče spravovaného zákazníkem

Určuje, jestli mají být pracovní prostory šifrované pomocí klíče spravovaného zákazníkem (CMK), nebo pomocí klíče spravovaného společností Microsoft k šifrování metrik a metadat. Další informace o použití CMK najdete v části [Azure Cosmos DB](concept-enterprise-security.md#azure-cosmos-db) článku podnikového zabezpečení.

Pokud chcete nakonfigurovat tuto zásadu, nastavte parametr efektu na __audit__ nebo __Odepřít__. Pokud je tato možnost nastavená na __audit__ , můžete vytvářet pracovní prostory bez CMK a v protokolu aktivit se vytvoří událost upozornění.

Pokud je zásada nastavená na __Odepřít__ , nemůžete vytvořit pracovní prostor, pokud neurčíte CMK. Při pokusu o vytvoření pracovního prostoru bez CMK dojde k chybě, která je podobná `Resource 'clustername' was disallowed by policy` a v protokolu aktivit vytvoří chybu. Identifikátor zásady se také vrátí jako součást této chyby.

## <a name="workspaces-should-use-private-link"></a>Pracovní prostory by měly používat privátní odkaz

Určuje, jestli by měly pracovní prostory používat privátní propojení Azure ke komunikaci s Azure Virtual Network. Další informace o použití privátního odkazu najdete v tématu [konfigurace privátního odkazu pro pracovní prostor](how-to-configure-private-link.md).

Pokud chcete nakonfigurovat tuto zásadu, nastavte parametr efekt na __audit__. Pokud vytvoříte pracovní prostor bez použití privátního odkazu, vytvoří se v protokolu aktivit událost s upozorněním.

## <a name="next-steps"></a>Další kroky

* [Dokumentace k Azure Policy](../governance/policy/overview.md)
* [Předdefinované zásady pro Azure Machine Learning](policy-reference.md)
* [Práce se zásadami zabezpečení pomocí Azure Security Center](../security-center/tutorial-security-policy.md)