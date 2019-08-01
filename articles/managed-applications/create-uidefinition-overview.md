---
title: CreateUiDefitinion. JSON pro prostředí pro vytváření spravované aplikace Azure | Microsoft Docs
description: Popisuje, jak vytvořit definice uživatelského rozhraní pro Azure Managed Applications
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
ms.openlocfilehash: 50bbaf740a67d3830df2d0447b9522153cb8c93c
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68619085"
---
# <a name="createuidefitinionjson-for-azure-managed-applications-create-experience"></a>CreateUiDefitinion. JSON pro prostředí pro vytváření spravované aplikace Azure
Tento dokument zavádí základní koncepty souboru **createUiDefinition. JSON** , který Azure Portal používá k definování uživatelského rozhraní při vytváření spravované aplikace.

Tato šablona je následující:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
   "handler": "Microsoft.Azure.CreateUIDef",
   "version": "0.1.2-preview",
   "parameters": {
      "basics": [ ],
      "steps": [ ],
      "outputs": { }
   }
}
```

CreateUiDefinition vždy obsahuje tři vlastnosti: 

* popisovač
* version
* parameters

Obslužná rutina by měla `Microsoft.Azure.CreateUIDef`být vždycky a nejnovější podporovaná verze `0.1.2-preview`je.

Schéma vlastnosti Parameters závisí na kombinaci zadané obslužné rutiny a verze. U spravovaných aplikací jsou `basics`podporované vlastnosti, `steps`a `outputs`. Základní a postupové vlastnosti obsahují [prvky](create-uidefinition-elements.md) , jako jsou textová pole a rozevírací seznamy, které se mají zobrazit v Azure Portal. Vlastnost Outputs slouží k mapování výstupních hodnot určených prvků na parametry šablony nasazení Azure Resource Manager.

Zahrnutí `$schema` je doporučeno, ale volitelné. Je-li tento parametr zadán `version` , musí hodnota pro odpovídat verzi `$schema` v identifikátoru URI.

Editor JSON můžete použít k vytvoření definice uživatelského rozhraní a pak ho otestovat v izolovaném [prostoru (sandbox) definice uživatelského rozhraní](https://portal.azure.com/?feature.customPortal=false&#blade/Microsoft_Azure_CreateUIDef/SandboxBlade) a zobrazit jeho náhled. Další informace o izolovaném prostoru naleznete v tématu [Test rozhraní portálu pro Azure Managed Applications](test-createuidefinition.md).

## <a name="basics"></a>Základní informace
Základy je první krok vygenerovaný při Azure Portal analyzuje soubor. Kromě zobrazení prvků určených v `basics`portálu vloží portál prvky pro uživatele pro výběr předplatného, skupiny prostředků a umístění pro nasazení. Pokud je to možné, v tomto kroku by se měly použít prvky, které dotazují parametry pro nasazení na úrovni pro nasazení, jako je název clusteru nebo přihlašovací údaje správce.

Pokud chování prvku závisí na předplatném uživatele, skupině prostředků nebo umístění, pak tento prvek nelze použít v základech. Například **Microsoft. Compute. SizeSelector** závisí na předplatném uživatele a umístění, abyste určili seznam dostupných velikostí. Proto se **Microsoft. Compute. SizeSelector** dá použít jenom v krocích. Obecně platí, že v základech lze použít pouze prvky v oboru názvů **Microsoft. Common** . I když některé elementy v jiných oborech názvů (například **Microsoft. Compute. přihlašovací údaje**), které nejsou závislé na kontextu uživatele, jsou stále povoleny.

## <a name="steps"></a>Kroky
Vlastnost kroků může obsahovat nula nebo více dalších kroků, které se zobrazí po základech, z nichž každý obsahuje jeden nebo více prvků. Zvažte přidání kroků na roli nebo vrstvu nasazené aplikace. Přidejte například krok pro vstupy hlavního uzlu a krok pro pracovní uzly v clusteru.

## <a name="outputs"></a>Výstupy
Azure Portal používá `outputs` vlastnost k mapování prvků z `basics` a `steps` na parametry šablony nasazení Azure Resource Manager. Klíče tohoto slovníku jsou názvy parametrů šablony a hodnoty jsou vlastnosti výstupních objektů z odkazovaných prvků.

Chcete-li nastavit název prostředku spravované aplikace, je nutné zahrnout hodnotu s `applicationResourceName` názvem ve vlastnosti výstupy. Pokud tuto hodnotu nenastavíte, aplikace přiřadí identifikátor GUID pro název. Do uživatelského rozhraní můžete zahrnout textové pole, které požádá o jméno od uživatele.

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
CreateUiDefinition poskytuje [funkce](create-uidefinition-functions.md) pro práci s vstupy a výstupy prvků a funkcemi, jako jsou podmíněné. Tyto funkce jsou podobné v syntaxi a funkcích pro Azure Resource Manager funkcí šablon.

## <a name="next-steps"></a>Další postup
Samotný soubor createUiDefinition. JSON má jednoduché schéma. Skutečná hloubka se nachází ze všech podporovaných elementů a funkcí. Tyto položky jsou popsány podrobněji na adrese:

- [Elementu](create-uidefinition-elements.md)
- [Functions](create-uidefinition-functions.md)

Aktuální schéma JSON pro createUiDefinition je k dispozici zde https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json:.

Příklad souboru uživatelského rozhraní naleznete v tématu [createUiDefinition. JSON](https://github.com/Azure/azure-managedapp-samples/blob/master/samples/201-managed-app-using-existing-vnet/createUiDefinition.json).
