---
title: Přehled definic vlastních rolí
description: Popisuje koncept vytváření definic vlastních rolí pro spravované aplikace.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 09/16/2019
ms.openlocfilehash: 7b7fff913c177703f959bfa103c8e310d01059e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "81391831"
---
# <a name="custom-role-definition-artifact-in-azure-managed-applications"></a>Artefakt definice vlastní role v Azure Managed Applications

Definice vlastní role je volitelný artefakt ve spravovaných aplikacích. Slouží k určení oprávnění, která spravovaná aplikace potřebuje k provedení svých funkcí.

Tento článek poskytuje přehled o artefaktu definice vlastní role a jeho schopnostech.

## <a name="custom-role-definition-artifact"></a>Artefakt definice vlastní role

Je potřeba pojmenovat artefakt definice vlastní role customRoleDefinition.jsna. Umístěte ji na stejnou úroveň jako createUiDefinition.jsna a mainTemplate.jsv balíčku. zip, který vytvoří definici spravované aplikace. Informace o vytvoření balíčku. zip a publikování definice spravované aplikace najdete v tématu [publikování definice spravované aplikace.](publish-service-catalog-app.md)

## <a name="custom-role-definition-schema"></a>Schéma definice vlastní role

customRoleDefinition.jsv souboru má vlastnost nejvyšší úrovně `roles` , která je polem rolí. Tyto role jsou oprávnění, která spravovaná aplikace potřebuje k fungování. V současné době jsou povoleny pouze předdefinované role, ale můžete zadat více rolí. Role může být odkazována podle ID definice role nebo podle názvu role.

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

Role se skládá buď z, `$.properties.roleName` nebo `id` :

```json
{
    "id": null,
    "properties": {
        "roleName": "Contributor"
    }
}
```

> [!NOTE]
> Můžete použít buď `id` `roleName` pole nebo. Je vyžadována pouze jedna z nich. Tato pole se používají k vyhledání definice role, která se má použít. Pokud jsou zadány obě, bude `id` použito pole.

|Vlastnost|Povinné?|Popis|
|---------|---------|---------|
|id|Ano|ID předdefinované role. Můžete použít úplné ID nebo jenom identifikátor GUID.|
|roleName|Ano|Název předdefinované role|