---
title: CreateUiDefinition.jsv souboru pro podokno portálu
description: Popisuje, jak vytvořit definice uživatelského rozhraní pro Azure Portal. Používá se při definování Azure Managed Applications.
author: tfitzmac
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: tomfitz
ms.openlocfilehash: 2956c76f5bec353639b39228b982db21b6932deb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "80294898"
---
# <a name="createuidefinitionjson-for-azure-managed-applications-create-experience"></a>Soubor createUiDefinition.json pro prostředí pro vytváření spravovaných aplikací Azure

Tento dokument představuje základní koncepty **createUiDefinition.jsv** souboru, který Azure Portal používá k definování uživatelského rozhraní při vytváření spravované aplikace.

Tato šablona je následující:

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

CreateUiDefinition vždy obsahuje tři vlastnosti: 

* popisovač
* verze
* parameters

Obslužná rutina by měla být vždycky `Microsoft.Azure.CreateUIDef` a nejnovější podporovaná verze je `0.1.2-preview` .

Schéma vlastnosti Parameters závisí na kombinaci zadané obslužné rutiny a verze. U spravovaných aplikací jsou podporované vlastnosti `basics` , `steps` a `outputs` . Základní a postupové vlastnosti obsahují [prvky](create-uidefinition-elements.md) , jako jsou textová pole a rozevírací seznamy, které se mají zobrazit v Azure Portal. Vlastnost Outputs slouží k mapování výstupních hodnot určených prvků na parametry šablony nasazení Azure Resource Manager.

Zahrnutí `$schema` je doporučeno, ale volitelné. Je-li tento parametr zadán, musí hodnota pro `version` odpovídat verzi v `$schema` identifikátoru URI.

Pomocí editoru JSON můžete vytvořit createUiDefinition a potom ho otestovat v [izolovaném prostoru createUiDefinition](https://portal.azure.com/?feature.customPortal=false&#blade/Microsoft_Azure_CreateUIDef/SandboxBlade) a zobrazit jeho náhled. Další informace o izolovaném prostoru naleznete v tématu [Test rozhraní portálu pro Azure Managed Applications](test-createuidefinition.md).

## <a name="basics"></a>Základy

Základy je první krok vygenerovaný při Azure Portal analyzuje soubor. Kromě zobrazení prvků určených v `basics` portálu vloží portál prvky pro uživatele pro výběr předplatného, skupiny prostředků a umístění pro nasazení. Pokud je to možné, v tomto kroku by se měly použít prvky, které dotazují parametry pro nasazení na úrovni pro nasazení, jako je název clusteru nebo přihlašovací údaje správce.

## <a name="steps"></a>Kroky

Vlastnost kroků může obsahovat nula nebo více dalších kroků, které se zobrazí po základech, z nichž každý obsahuje jeden nebo více prvků. Zvažte přidání kroků na roli nebo vrstvu nasazené aplikace. Přidejte například krok pro vstupy hlavního uzlu a krok pro pracovní uzly v clusteru.

## <a name="outputs"></a>Výstupy

Azure Portal používá `outputs` vlastnost k mapování prvků z `basics` a `steps` na parametry šablony nasazení Azure Resource Manager. Klíče tohoto slovníku jsou názvy parametrů šablony a hodnoty jsou vlastnosti výstupních objektů z odkazovaných prvků.

Chcete-li nastavit název prostředku spravované aplikace, je nutné zahrnout hodnotu s názvem `applicationResourceName` ve vlastnosti výstupy. Pokud tuto hodnotu nenastavíte, aplikace přiřadí identifikátor GUID pro název. Do uživatelského rozhraní můžete zahrnout textové pole, které požádá o jméno od uživatele.

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

Chcete-li filtrovat dostupná umístění pouze do těch umístění, která podporují typy prostředků, které mají být nasazeny, zadejte pole typů prostředků. Pokud zadáte více než jeden typ prostředku, vrátí se pouze ta umístění, která podporují všechny typy prostředků. Tato vlastnost je nepovinná.

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

## <a name="functions"></a>Functions

CreateUiDefinition poskytuje [funkce](create-uidefinition-functions.md) pro práci s vstupy a výstupy prvků a funkcemi, jako jsou podmíněné. Tyto funkce jsou podobné v syntaxi a funkcích pro Azure Resource Manager funkcí šablon.

## <a name="next-steps"></a>Další kroky

createUiDefinition.jsv samotném souboru má jednoduché schéma. Skutečná hloubka se nachází ze všech podporovaných elementů a funkcí. Tyto položky jsou popsány podrobněji na adrese:

- [Elements](create-uidefinition-elements.md)
- [Functions](create-uidefinition-functions.md)

Aktuální schéma JSON pro createUiDefinition je k dispozici zde: `https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json` .

Příklad souboru uživatelského rozhraní naleznete v tématu [createUiDefinition.json](https://github.com/Azure/azure-managedapp-samples/blob/master/Managed%20Application%20Sample%20Packages/201-managed-app-using-existing-vnet/createUiDefinition.json).
