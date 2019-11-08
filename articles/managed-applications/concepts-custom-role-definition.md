---
title: Přehled definic vlastních rolí v Azure Managed Applications | Microsoft Docs
description: Popisuje koncept vytváření definic vlastních rolí pro spravované aplikace.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 09/16/2019
ms.openlocfilehash: 7f4371bea467d6d4c99a776e03cdf13070d77ac6
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818393"
---
# <a name="custom-role-definition-artifact-in-azure-managed-applications"></a>Artefakt definice vlastní role v Azure Managed Applications

Definice vlastní role je volitelný artefakt ve spravovaných aplikacích. Slouží k určení oprávnění, která spravovaná aplikace potřebuje k provedení svých funkcí.

Tento článek poskytuje přehled o artefaktu definice vlastní role a jeho schopnostech.

## <a name="custom-role-definition-artifact"></a>Artefakt definice vlastní role

Je nutné pojmenovat artefakt definice vlastní role customRoleDefinition. JSON. Umístěte ho do stejné úrovně jako createUiDefinition. JSON a mainTemplate. JSON v balíčku. zip, který vytvoří definici spravované aplikace. Informace o vytvoření balíčku. zip a publikování definice spravované aplikace najdete v tématu [publikování definice spravované aplikace.](publish-managed-app-definition-quickstart.md)

## <a name="custom-role-definition-schema"></a>Schéma definice vlastní role

Soubor customRoleDefinition. JSON má vlastnost `roles` nejvyšší úrovně, která je polem rolí. Tyto role jsou oprávnění, která spravovaná aplikace potřebuje k fungování. V současné době jsou povoleny pouze předdefinované role, ale můžete zadat více rolí. Role může být odkazována podle ID definice role nebo podle názvu role.

Ukázkový JSON pro definici vlastní role:

```json
{
    "contentVersion": "0.0.0.1",
    "roles": [
        {
            "properties": {
                "roleName": "Contributor"
            }
        },
        {
            "id": "acdd72a7-3385-48ef-bd42-f606fba81ae7"
        },
        {
            "id": "/providers/Microsoft.Authorization/roledefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c"
        }
    ]
}
```

## <a name="roles"></a>Role

Role se skládá buď z `$.properties.roleName`, nebo `id`:

```json
{
    "id": null,
    "properties": {
        "roleName": "Contributor"
    }
}
```

> [!NOTE]
> Můžete použít buď pole `id` nebo `roleName`. Je vyžadována pouze jedna z nich. Tato pole se používají k vyhledání definice role, která se má použít. Pokud jsou zadány obě, použije se pole `id`.

|Vlastnost|Povinné?|Popis|
|---------|---------|---------|
|id|Ano|ID předdefinované role. Můžete použít úplné ID nebo jenom identifikátor GUID.|
|RoleName|Ano|Název předdefinované role|