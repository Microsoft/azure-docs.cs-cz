---
title: Přehled vlastních definic rolí
description: Popisuje koncept vytváření vlastních definic rolí pro spravované aplikace.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 09/16/2019
ms.openlocfilehash: 88e42fd9626276f6c77b46b33c138407f91d06ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75650757"
---
# <a name="custom-role-definition-artifact-in-azure-managed-applications"></a>Artefakt vlastní definice role ve spravovaných aplikacích Azure

Vlastní definice role je volitelný artefakt ve spravovaných aplikacích. Používá se k určení, jaká oprávnění spravovaná aplikace potřebuje k provádění svých funkcí.

Tento článek obsahuje přehled artefaktu vlastní definice role a jeho možností.

## <a name="custom-role-definition-artifact"></a>Artefakt vlastní definice role

Je třeba pojmenovat vlastní artefakt definice role customRoleDefinition.json. Umístěte jej na stejnou úroveň jako createUiDefinition.json a mainTemplate.json v balíčku .zip, který vytvoří definici spravované aplikace. Informace o tom, jak vytvořit balíček ZIP a publikovat definici spravované aplikace, najdete v [tématu Publikování definice spravované aplikace.](publish-managed-app-definition-quickstart.md)

## <a name="custom-role-definition-schema"></a>Schéma vlastní definice role

Soubor customRoleDefinition.json má vlastnost `roles` nejvyšší úrovně, která je pole rolí. Tyto role jsou oprávnění, která musí spravovat aplikace fungovat. V současné době jsou povoleny pouze předdefinované role, ale můžete zadat více rolí. Na roli lze odkazovat id definice role nebo název role.

Ukázka json pro vlastní definici role:

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

Role se skládá buď `$.properties.roleName` z `id`a nebo a :

```json
{
    "id": null,
    "properties": {
        "roleName": "Contributor"
    }
}
```

> [!NOTE]
> Můžete použít pole `id` `roleName` nebo. Je vyžadován pouze jeden. Tato pole se používají k vyhledat definici role, která by měla být použita. Pokud jsou zadány `id` obě, bude použito toto pole.

|Vlastnost|Povinné?|Popis|
|---------|---------|---------|
|id|Ano|ID předdefinované role. Můžete použít celé ID nebo jen IDENTIFIKÁTOR.|
|Rolename|Ano|Název předdefinované role.|