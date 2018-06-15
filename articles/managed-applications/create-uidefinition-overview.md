---
title: Pochopení vytváření uživatelského rozhraní aplikace spravovaného definice Azure | Microsoft Docs
description: Popisuje postup vytvoření definice uživatelského rozhraní pro spravované aplikace Azure
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
ms.date: 12/15/2017
ms.author: tomfitz
ms.openlocfilehash: 59003e71324f5342cb2b724f670603fd6b67afe4
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2018
ms.locfileid: "34305221"
---
# <a name="create-azure-portal-user-interface-for-your-managed-application"></a>Vytvoření Azure portálu uživatelského rozhraní pro spravované aplikace
Tento dokument uvádí základní koncepty createUiDefinition.json souboru. Portál Azure tento soubor používá ke generování uživatelského rozhraní pro vytváření spravované aplikace.

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

* obslužné rutiny
* verze
* parameters

Pro spravované aplikace, musí být vždy obslužná rutina `Microsoft.Compute.MultiVm`, a nejnovější podporovaná verze je `0.1.2-preview`.

Schéma vlastnosti parametry závisí na kombinaci zadaná obslužná rutina a verze. Pro spravované aplikace jsou podporované vlastnosti `basics`, `steps`, a `outputs`. Vlastnosti základní informace a kroky obsahovat _elementy_ - jako textová pole a rozevírací seznamy – který se má zobrazit na portálu Azure. Vlastnost výstupy slouží k mapování výstup hodnot zadaných elementů na parametry šablony nasazení Azure Resource Manager.

Včetně `$schema` je doporučená, ale volitelné. Je-li zadán, bude hodnota pro `version` musí shodovat s verzí v rámci `$schema` identifikátor URI.

## <a name="basics"></a>Základy
Základní informace o kroku je vždy první krok průvodce vygeneruje, když na portál Azure analyzuje soubor. Kromě zobrazení prvky určené ve `basics`, portálu vloží prvky pro uživatelům si vybrat předplatné, skupinu prostředků a umístění pro nasazení. Obecně platí by měly prvky, které dotazu pro nasazení celou parametry, jako je třeba název clusteru nebo správce přihlašovacích údajů, přejděte v tomto kroku.

Pokud elementu chování závisí na předplatné, skupinu prostředků nebo umístění uživatele, pak tento prvek nelze použít v základní informace. Například **Microsoft.Compute.SizeSelector** závisí na předplatném a umístění určit seznam dostupných velikostí uživatele. Proto **Microsoft.Compute.SizeSelector** lze použít pouze v krocích. Obecně platí, pouze elementy v **Microsoft.Common** oboru názvů lze použít v základní informace. I když některé prvky v jiných oborech názvů (jako je **Microsoft.Compute.Credentials**), nemáte jsou závislé na kontextu uživatele, jsou stále povoleny.

## <a name="steps"></a>Kroky
Vlastnost kroky může obsahovat nula nebo více další kroky k zobrazení po základy, z nichž každý obsahuje jeden či více elementů. Zvažte přidání kroků / role nebo vrstvě nasazení aplikace. Například přidáte krok pro vstupy pro hlavní uzly a krok pro uzly pracovního procesu v clusteru.

## <a name="outputs"></a>Výstupy
Používá portál Azure `outputs` vlastnost pro mapování elementů od `basics` a `steps` na parametry šablony nasazení Azure Resource Manager. Názvy parametrů šablony jsou klíče tohoto slovníku a hodnoty jsou vlastnosti objektů výstup z odkazované elementy.

Pokud chcete nastavit název prostředku spravované aplikace, musí obsahovat hodnotu s názvem `applicationResourceName` ve vlastnosti výstupy. Pokud tuto hodnotu nenastavíte, aplikace přiřadí identifikátor GUID pro název. Můžete zahrnout do textového pole v uživatelském rozhraní, který vyžaduje název od uživatele.

```json
"outputs": {
    "vmName": "[steps('appSettings').vmName]",
    "trialOrProduction": "[steps('appSettings').trialOrProd]",
    "userName": "[steps('vmCredentials').adminUsername]",
    "pwd": "[steps('vmCredentials').vmPwd.password]",
    "applicationResourceName": "[steps('appSettings').vmName]"
}
```

## <a name="functions"></a>Functions
Podobně jako funkce šablon v Azure Resource Manager (jak v syntaxi a funkce), CreateUiDefinition poskytuje funkce pro práci s prvky vstupy a výstupy, a také funkce, jako je podmíněné příkazy.

## <a name="next-steps"></a>Další postup
Samotný soubor createUiDefinition.json má jednoduché schéma. Skutečné hloubka ho pochází z podporované elementy a funkce. Tyto položky jsou popsány podrobněji na:

- [Elementy](create-uidefinition-elements.md)
- [Functions](create-uidefinition-functions.md)

Zde jsou k dispozici aktuální schéma JSON pro createUiDefinition: https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json.

Příklad uživatelské rozhraní souboru, najdete v části [createUiDefinition.json](https://github.com/Azure/azure-managedapp-samples/blob/master/samples/201-managed-app-using-existing-vnet/createUiDefinition.json).
