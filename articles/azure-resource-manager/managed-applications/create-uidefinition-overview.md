---
title: Soubor CreateUiDefinition.json pro podokno portálu
description: Popisuje, jak vytvořit definice uživatelského rozhraní pro portál Azure. Používá se při definování spravovaných aplikací Azure.
author: tfitzmac
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: tomfitz
ms.openlocfilehash: 2956c76f5bec353639b39228b982db21b6932deb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294898"
---
# <a name="createuidefinitionjson-for-azure-managed-applications-create-experience"></a>Soubor createUiDefinition.json pro prostředí pro vytváření spravovaných aplikací Azure

Tento dokument představuje základní koncepty souboru **createUiDefinition.json,** který portál Azure používá k definování uživatelského rozhraní při vytváření spravované aplikace.

Šablona je následující:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
   "handler": "Microsoft.Azure.CreateUIDef",
   "version": "0.1.2-preview",
   "parameters": {
      "basics": [ ],
      "steps": [ ],
      "outputs": { },
      "resourceTypes": [ ]
   }
}
```

A CreateUiDefinition vždy obsahuje tři vlastnosti: 

* Obslužné rutiny
* version
* parameters

Obslužná `Microsoft.Azure.CreateUIDef`rutina by měla `0.1.2-preview`být vždy a nejnovější podporovaná verze je .

Schéma vlastnosti parameters závisí na kombinaci zadané obslužné rutiny a verze. U spravovaných aplikací `basics` `steps`jsou `outputs`podporované vlastnosti , , a . Vlastnosti základů a kroků obsahují [prvky](create-uidefinition-elements.md) , které se mají zobrazit na webu Azure Portal. Vlastnost outputs se používá k mapování výstupních hodnot zadaných prvků na parametry šablony nasazení Azure Resource Manager.

Včetně `$schema` je doporučeno, ale volitelné. Pokud je zadán, `version` hodnota pro musí `$schema` odpovídat verzi v rámci identifikátoru URI.

Můžete použít editor JSON k vytvoření createUiDefinition a pak jej otestovat v [poli createUiDefinition Sandbox](https://portal.azure.com/?feature.customPortal=false&#blade/Microsoft_Azure_CreateUIDef/SandboxBlade) a zobrazit jeho náhled. Další informace o izolovaném prostoru najdete v [tématu Testování rozhraní portálu pro spravované aplikace Azure](test-createuidefinition.md).

## <a name="basics"></a>Základy

Základy je první krok generovaný při analýzách souboru portálu Azure. Kromě zobrazení prvků určených `basics`v aplikaci aplikace portal vloží prvky, které uživatelé zvolí k výběru předplatného, skupiny prostředků a umístění pro nasazení. Pokud je to možné, měly by v tomto kroku přejít prvky, které zasazují o parametry pro celé nasazení, jako je název clusteru nebo pověření správce.

## <a name="steps"></a>Kroky

Vlastnost steps může obsahovat nula nebo více dalších kroků k zobrazení po základech, z nichž každý obsahuje jeden nebo více prvků. Zvažte přidání kroků pro roli nebo vrstvu nasazené aplikace. Například přidejte krok pro vstupy hlavních uzlů a krok pro pracovní uzly v clusteru.

## <a name="outputs"></a>Výstupy

Portál Azure používá `outputs` vlastnost k `basics` mapování `steps` prvků z a na parametry šablony nasazení Azure Resource Manageru. Klávesy tohoto slovníku jsou názvy parametrů šablony a hodnoty jsou vlastnosti výstupních objektů z odkazovaných prvků.

Chcete-li nastavit název spravovaného prostředku aplikace, musíte zahrnout hodnotu pojmenovanou `applicationResourceName` ve vlastnosti outputs. Pokud tuto hodnotu nenastavíte, aplikace přiřadí název GUID. Do uživatelského rozhraní můžete zahrnout textové pole, které požaduje jméno od uživatele.

```json
"outputs": {
    "vmName": "[steps('appSettings').vmName]",
    "trialOrProduction": "[steps('appSettings').trialOrProd]",
    "userName": "[steps('vmCredentials').adminUsername]",
    "pwd": "[steps('vmCredentials').vmPwd.password]",
    "applicationResourceName": "[steps('appSettings').vmName]"
}
```

## <a name="resource-types"></a>Typy prostředků

Chcete-li filtrovat dostupná umístění pouze do umístění, která podporují typy prostředků k nasazení, zadejte pole typů prostředků. Pokud zadáte více než jeden typ prostředku, budou vrácena pouze ta umístění, která podporují všechny typy prostředků. Tato vlastnost je nepovinná.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
    "handler": "Microsoft.Azure.CreateUIDef",
    "version": "0.1.2-preview",
    "parameters": {
      "resourceTypes": ["Microsoft.Compute/disks"],
      "basics": [
        ...
```  

## <a name="functions"></a>Funkce

CreateUiDefinition poskytuje [funkce](create-uidefinition-functions.md) pro práci se vstupy a výstupy prvků a funkce, jako jsou například podmínky. Tyto funkce jsou podobné v syntaxi i funkce funkce Azure Resource Manager šablony funkce.

## <a name="next-steps"></a>Další kroky

Samotný soubor createUiDefinition.json má jednoduché schéma. Skutečná hloubka pochází ze všech podporovaných prvků a funkcí. Tyto položky jsou podrobněji popsány na adrese:

- [Elementy](create-uidefinition-elements.md)
- [Functions](create-uidefinition-functions.md)

Aktuální schéma JSON pro createUiDefinition je k `https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json`dispozici zde: .

Příklad souboru uživatelského rozhraní naleznete [v tématu createUiDefinition.json](https://github.com/Azure/azure-managedapp-samples/blob/master/Managed%20Application%20Sample%20Packages/201-managed-app-using-existing-vnet/createUiDefinition.json).
