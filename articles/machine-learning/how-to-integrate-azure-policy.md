---
title: Auditovat a spravovat dodržování zásad
titleSuffix: Azure Machine Learning
description: Naučte se používat Azure Policy k používání předdefinovaných zásad pro Azure Machine Learning, abyste se ujistili, že vaše pracovní prostory vyhovují vašim požadavkům.
author: aashishb
ms.author: aashishb
ms.date: 03/25/2021
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.openlocfilehash: f708e2181511da97ecffcd6f1636a2b232b4fbc6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105544362"
---
# <a name="audit-and-manage-azure-machine-learning-using-azure-policy"></a>Audit a Správa Azure Machine Learning pomocí Azure Policy

[Azure Policy](../governance/policy/index.yml) je nástroj zásad správného řízení, který vám umožní zajistit, aby prostředky Azure vyhovovaly vašim zásadám. Pomocí Azure Machine Learning můžete přiřadit následující zásady:

| Zásady | Description |
| ----- | ----- |
| **Klíč spravovaný zákazníkem** | Audit nebo vymáhání, zda pracovní prostory musí používat klíč spravovaný zákazníkem |
| **Privátní odkaz** | Audit nebo vymáhání, zda pracovní prostory používají privátní koncový bod ke komunikaci s virtuální sítí. |
| **Soukromý koncový bod** | Nakonfigurujte podsíť Azure Virtual Network, kde by se měl vytvořit privátní koncový bod. |
| **Zóna Privátní DNS** | Nakonfigurujte privátní zónu DNS, která se má použít pro privátní propojení. |
| **Spravovaná identita přiřazená uživatelem** | Auditujte nebo uplatněte, jestli pracovní prostory používají spravovanou identitu přiřazenou uživatelem. |

Zásady je možné nastavit v různých oborech, například na úrovni předplatného nebo skupiny prostředků. Další informace najdete v dokumentaci k [Azure Policy](../governance/policy/overview.md).

## <a name="built-in-policies"></a>Předdefinované zásady

Azure Machine Learning poskytuje sadu zásad, které můžete použít pro běžné scénáře s Azure Machine Learning. Tyto definice zásad můžete přiřadit k existujícímu předplatnému nebo je použít jako základ pro vytvoření vlastních definicí. Úplný seznam předdefinovaných zásad pro Azure Machine Learning najdete v tématu [předdefinované zásady pro Azure Machine Learning](../governance/policy/samples/built-in-policies.md#machine-learning).

Pokud chcete zobrazit integrované definice zásad související s Azure Machine Learning, použijte následující postup:

1. V [Azure Portal](https://portal.azure.com)přejít na __Azure Policy__ .
1. Vyberte __definice__.
1. Pro __typ__ vyberte možnost _předdefinované_ a pro __kategorie__ vyberte možnost __Machine Learning__.

Tady můžete vybrat definice zásad a zobrazit je. Při prohlížení definice můžete použít odkaz __přiřadit__ a přiřadit zásadu ke konkrétnímu oboru a nakonfigurovat parametry pro tyto zásady. Další informace najdete v tématu [přiřazení portálu pro zásady](../governance/policy/assign-policy-portal.md).

Zásady můžete přiřadit také pomocí [Azure PowerShell](../governance/policy/assign-policy-powershell.md), [Azure CLI](../governance/policy/assign-policy-azurecli.md)a [šablon](../governance/policy/assign-policy-template.md).

## <a name="workspace-encryption-with-customer-managed-key"></a>Šifrování pracovního prostoru pomocí klíče spravovaného zákazníkem

Určuje, jestli má být pracovní prostor zašifrovaný pomocí klíče spravovaného zákazníkem, nebo pomocí klíče spravovaného společností Microsoft k šifrování metrik a metadat. Další informace o použití klíče spravovaného zákazníkem najdete v části [Azure Cosmos DB](concept-data-encryption.md#azure-cosmos-db) článku šifrování dat.

Pokud chcete nakonfigurovat tuto zásadu, nastavte parametr efektu na __audit__ nebo __Odepřít__. Pokud je tato možnost nastavená na __audit__, můžete vytvořit pracovní prostor bez klíče spravovaného zákazníkem a událost upozornění se vytvoří v protokolu aktivit.

Pokud je zásada nastavená na __Odepřít__, nemůžete vytvořit pracovní prostor, pokud neurčíte klíč spravovaný zákazníkem. Když se pokusíte vytvořit pracovní prostor bez klíče spravovaného zákazníkem, dojde k chybě, která je podobná `Resource 'clustername' was disallowed by policy` a v protokolu aktivit vytvoří chybu. Identifikátor zásady se také vrátí jako součást této chyby.

## <a name="workspace-should-use-private-link"></a>Pracovní prostor by měl používat privátní odkaz

Určuje, jestli má pracovní prostor používat privátní propojení Azure ke komunikaci s Azure Virtual Network. Další informace o použití privátního odkazu najdete v tématu [konfigurace privátního odkazu pro pracovní prostor](how-to-configure-private-link.md).

Pokud chcete nakonfigurovat tuto zásadu, nastavte parametr efektu na __audit__ nebo __Odepřít__. Pokud je nastaveno na __audit__, můžete vytvořit pracovní prostor bez použití privátního odkazu a událost upozornění se vytvoří v protokolu aktivit.

Pokud je zásada nastavená na __Odepřít__, nemůžete vytvořit pracovní prostor, pokud nepoužívá privátní odkaz. Při pokusu o vytvoření pracovního prostoru bez privátního propojení dojde k chybě. Tato chyba je také zaznamenána v protokolu aktivit. Identifikátor zásady se vrátí jako součást této chyby.

## <a name="workspace-should-use-private-endpoint"></a>Pracovní prostor by měl používat privátní koncový bod

Nakonfiguruje pracovní prostor pro vytvoření privátního koncového bodu v zadané podsíti Azure Virtual Network.

Pokud chcete nakonfigurovat tuto zásadu, nastavte parametr efektu na __DeployIfNotExists__. Nastavte __privateEndpointSubnetID__ na ID Azure Resource Manager podsítě.
## <a name="workspace-should-use-private-dns-zones"></a>Pracovní prostor by měl používat privátní zóny DNS

Nakonfiguruje pracovní prostor na používání privátní zóny DNS a přepíše výchozí rozlišení DNS pro soukromý koncový bod.

Pokud chcete nakonfigurovat tuto zásadu, nastavte parametr efektu na __DeployIfNotExists__. Nastavte __privateDnsZoneId__ na ID Azure Resource Manager privátní zóny DNS, která se má použít. 

## <a name="workspace-should-use-user-assigned-managed-identity"></a>Pracovní prostor by měl používat spravovanou identitu přiřazenou uživatelem

Určuje, jestli je pracovní prostor vytvořený pomocí spravované identity přiřazené systémem (výchozí) nebo spravované identity přiřazené uživatelem. Spravovaná identita pro pracovní prostor se používá pro přístup k přidruženým prostředkům, jako jsou Azure Storage, Azure Container Registry, Azure Key Vault a Azure Application Insights. Další informace najdete v tématu [použití spravovaných identit pomocí Azure Machine Learning](how-to-use-managed-identities.md).

Pokud chcete nakonfigurovat tuto zásadu, nastavte parametr efektu na __audit__, __Deny__ nebo __disabled__. Pokud nastavíte __audit__, můžete vytvořit pracovní prostor bez zadání uživatelem přiřazené spravované identity. Použije se identita přiřazená systémem a v protokolu aktivit se vytvoří událost upozornění.

Pokud je zásada nastavená na __Odepřít__, nemůžete vytvořit pracovní prostor, pokud během procesu vytváření neposkytnete uživatelem přiřazenou identitu. Při pokusu o vytvoření pracovního prostoru bez zadání identity přiřazené uživatelem dojde k chybě. Tato chyba se taky zaznamená do protokolu aktivit. Identifikátor zásady se vrátí jako součást této chyby.

## <a name="next-steps"></a>Další kroky

* [Dokumentace k Azure Policy](../governance/policy/overview.md)
* [Předdefinované zásady pro Azure Machine Learning](policy-reference.md)
* [Práce se zásadami zabezpečení pomocí Azure Security Center](../security-center/tutorial-security-policy.md)