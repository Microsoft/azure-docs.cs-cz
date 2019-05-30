---
title: Principy vytváření uživatelského rozhraní pro Azure definice spravované aplikace | Dokumentace Microsoftu
description: Popisuje postup vytvoření definic uživatelského rozhraní pro spravované aplikace Azure
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/26/2019
ms.author: tomfitz
ms.openlocfilehash: 3d0a6d97440404904c041369a4631fdd3fb618b4
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/28/2019
ms.locfileid: "66257552"
---
# <a name="create-azure-portal-user-interface-for-your-managed-application"></a>Vytvořit Azure portal uživatelské rozhraní pro spravované aplikace
Tento dokument představuje základní koncepty createUiDefinition.json souboru. Na webu Azure portal tento soubor používá k vygenerování uživatelského rozhraní pro vytvoření spravované aplikace.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
   "handler": "Microsoft.Compute.MultiVm",
   "version": "0.1.2-preview",
   "parameters": {
      "basics": [ ],
      "steps": [ ],
      "outputs": { }
   }
}
```

CreateUiDefinition vždy obsahuje tři vlastnosti: 

* Obslužná rutina
* version
* parameters

Pro spravované aplikace by měla být vždy obslužná rutina `Microsoft.Compute.MultiVm`, a je nejnovější podporovanou verzi `0.1.2-preview`.

Schéma vlastnost parameters závisí na kombinaci zadaná obslužná rutina a verze. Pro spravované aplikace jsou podporované vlastnosti `basics`, `steps`, a `outputs`. Vlastnosti základní informace a kroky obsahují _prvky_ -, jako jsou textová pole a rozevírací seznamy – který se má zobrazit na webu Azure Portal. Vlastnost výstupy slouží k mapování výstupní hodnoty zadané elementy na parametry šablony nasazení Azure Resource Manageru.

Včetně `$schema` je doporučené, ale volitelné. Pokud zadaná hodnota pro `version` musí shodovat s verzí v rámci `$schema` identifikátoru URI.

Můžete použít JSON editor pro vytvoření vaší definice uživatelského rozhraní nebo izolovaného prostoru definice uživatelského rozhraní můžete použít k vytvoření a zobrazit jejich náhled definice uživatelského rozhraní. Další informace o sandbox najdete v tématu [testovací rozhraní portálu pro Azure Managed Applications](test-createuidefinition.md).

## <a name="basics"></a>Základy
Základní informace o kroku je vždy první krok průvodce vygeneruje, když na webu Azure portal analyzuje soubor. Kromě zobrazení prvky určené ve `basics`, vloží prvky pro uživatele zvolte předplatné, skupinu prostředků a umístění pro nasazení na portálu. Prvky, které se dotazují pro parametry celého nasazení, jako je název clusteru nebo správce přihlašovacích údajů, by měly obecně platí, přejděte v tomto kroku.

Pokud elementu chování závisí na předplatné uživatele, skupinu prostředků nebo umístění, nelze tento prvek použít v základní informace. Například **Microsoft.Compute.SizeSelector** závisí na předplatné a umístění k určení seznam dostupných velikostí uživatele. Proto **Microsoft.Compute.SizeSelector** jde použít jenom v krocích. Obecně platí, pouze prvky **Microsoft.Common** oboru názvů lze použít v základní informace. I když některé prvky v jiných oborech názvů (jako je **Microsoft.Compute.Credentials**), které nejsou závislé na kontextu uživatele, jsou stále povolený.

## <a name="steps"></a>Kroky
Vlastnost kroků může obsahovat nula nebo více dalších kroků pro zobrazení po základy, z nichž každý obsahuje jeden nebo více prvků. Zvažte přidání kroků na úrovní nasazovaná aplikace nebo role. Třeba přidáte krok pro zadání pro hlavní uzly a krok pracovních uzlů v clusteru.

## <a name="outputs"></a>Výstupy
Na webu Azure portal používá `outputs` vlastnost pro mapování elementů z `basics` a `steps` na parametry šablony nasazení Azure Resource Manageru. Názvy parametrů šablony jsou klíče tohoto slovníku a hodnoty jsou vlastnosti objektů výstup z odkazované elementy.

Pokud chcete nastavit název prostředku spravované aplikace, musí obsahovat hodnotu s názvem `applicationResourceName` ve vlastnosti výstupy. Pokud tuto hodnotu nenastavíte, aplikace přiřadí identifikátor GUID pro název. Textové pole může obsahovat v uživatelském rozhraní, který vyžaduje název od uživatele.

```json
"outputs": {
    "vmName": "[steps('appSettings').vmName]",
    "trialOrProduction": "[steps('appSettings').trialOrProd]",
    "userName": "[steps('vmCredentials').adminUsername]",
    "pwd": "[steps('vmCredentials').vmPwd.password]",
    "applicationResourceName": "[steps('appSettings').vmName]"
}
```

## <a name="functions"></a>Funkce
Podobně jako funkce šablon v Azure Resource Manageru (jak v syntaxi a funkce), CreateUiDefinition poskytuje funkce pro práci s elementy vstupů a výstupů a funkce, jako jsou podmíněné výrazy.

## <a name="next-steps"></a>Další postup
Samotný soubor createUiDefinition.json má jednoduché schéma. Skutečné hloubka ho pochází z podporovaných prvky a funkce. Tyto položky jsou popsány podrobněji na:

- [Elementy](create-uidefinition-elements.md)
- [Functions](create-uidefinition-functions.md)

Aktuální schéma JSON pro createUiDefinition je k dispozici tady: https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json.

Příklad souboru uživatelské rozhraní, naleznete v tématu [createUiDefinition.json](https://github.com/Azure/azure-managedapp-samples/blob/master/samples/201-managed-app-using-existing-vnet/createUiDefinition.json).
