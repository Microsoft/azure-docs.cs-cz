---
title: CreateUiDefinition.jsv souboru pro podokno portálu
description: Popisuje, jak vytvořit definice uživatelského rozhraní pro Azure Portal. Používá se při definování Azure Managed Applications.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: tomfitz
ms.openlocfilehash: 0e2aee194d3c97655dd4ec5aaeea46fb607c4c5e
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/14/2020
ms.locfileid: "88210966"
---
# <a name="createuidefinitionjson-for-azure-managed-applications-create-experience"></a>Soubor createUiDefinition.json pro prostředí pro vytváření spravovaných aplikací Azure

Tento dokument představuje základní koncepty **createUiDefinition.jsv** souboru. Azure Portal používá tento soubor k definování uživatelského rozhraní při vytváření spravované aplikace.

Tato šablona je následující:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
    "handler": "Microsoft.Azure.CreateUIDef",
    "version": "0.1.2-preview",
    "parameters": {
        "config": {
            "basics": { }
        },
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

Schéma vlastnosti Parameters závisí na kombinaci zadané obslužné rutiny a verze. U spravovaných aplikací jsou podporované vlastnosti `basics` , `steps` , a `outputs` `config` . Základní a postupové vlastnosti obsahují [prvky](create-uidefinition-elements.md) , jako jsou textová pole a rozevírací seznamy, které se mají zobrazit v Azure Portal. Vlastnost Outputs slouží k mapování výstupních hodnot určených prvků na parametry šablony Azure Resource Manager. Použijete `config` pouze v případě, že potřebujete přepsat výchozí chování `basics` kroku.

Zahrnutí `$schema` je doporučeno, ale volitelné. Je-li tento parametr zadán, musí hodnota pro `version` odpovídat verzi v `$schema` identifikátoru URI.

Pomocí editoru JSON můžete vytvořit createUiDefinition a potom ho otestovat v [izolovaném prostoru createUiDefinition](https://portal.azure.com/?feature.customPortal=false&#blade/Microsoft_Azure_CreateUIDef/SandboxBlade) a zobrazit jeho náhled. Další informace o izolovaném prostoru naleznete v tématu [Test rozhraní portálu pro Azure Managed Applications](test-createuidefinition.md).

## <a name="basics"></a>Základy

**Základní** krok je prvním krokem vygenerovaným při analýze souboru Azure Portal. Ve výchozím nastavení umožňuje krok základy uživatelům zvolit předplatné, skupinu prostředků a umístění pro nasazení.

:::image type="content" source="./media/create-uidefinition-overview/basics.png" alt-text="Základy – výchozí":::

V této části můžete přidat další prvky. Pokud je to možné, přidejte prvky, které dotazují parametry pro nasazení na úrovni, jako je název clusteru nebo přihlašovací údaje správce.

Následující příklad ukazuje textové pole, které bylo přidáno do výchozích prvků.

```json
"basics": [
    {
        "name": "textBox1",
        "type": "Microsoft.Common.TextBox",
        "label": "Textbox on basics",
        "defaultValue": "my text value",
        "toolTip": "",
        "visible": true
    }
]
```

## <a name="config"></a>Config

Prvek konfigurace určíte, pokud potřebujete přepsat výchozí chování pro základní kroky. Následující příklad ukazuje dostupné vlastnosti.

```json
"config": {
    "basics": {
        "description": "Customized description with **markdown**, see [more](https://www.microsoft.com).",
        "subscription": {
            "constraints": {
                "validations": [
                    {
                        "isValid": "[expression for checking]",
                        "message": "Please select a valid subscription."
                    },
                    {
                        "permission": "<Resource Provider>/<Action>",
                        "message": "Must have correct permission to complete this step."
                    }
                ]
            },
            "resourceProviders": [
                "<Resource Provider>"
            ]
        },
        "resourceGroup": {
            "constraints": {
                "validations": [
                    {
                        "isValid": "[expression for checking]",
                        "message": "Please select a valid resource group."
                    }
                ]
            },
            "allowExisting": true
        },
        "location": {
            "label": "Custom label for location",
            "toolTip": "provide a useful tooltip",
            "resourceTypes": [
                "Microsoft.Compute/virtualMachines"
            ],
            "allowedValues": [
                "eastus",
                "westus2"
            ],
            "visible": true
        }
    }
},
```

Pro `description` Zadejte řetězec s podporou Markdownu, který popisuje váš prostředek. Podporují se víceřádkové formáty a odkazy.

Pro `location` Zadejte vlastnosti ovládacího prvku umístění, který chcete přepsat. Všechny vlastnosti, které nejsou přepsány, jsou nastaveny na výchozí hodnoty. `resourceTypes` přijímá pole řetězců obsahující plně kvalifikované názvy typů prostředků. Možnosti umístění jsou omezené jenom na oblasti, které podporují typy prostředků.  `allowedValues`   přijímá pole řetězců oblastí. V rozevíracím seznamu se zobrazí pouze tyto oblasti.Můžete nastavit i `allowedValues`    `resourceTypes` . Výsledkem je průnik obou seznamů. Nakonec `visible` lze vlastnost použít k podmíněnému nebo úplnému vypnutí rozevíracího seznamu umístění.  

`subscription`Prvky a `resourceGroup` umožňují zadat další ověření. Syntaxe pro určení platnosti je shodná s vlastním ověřením pro [textové pole](microsoft-common-textbox.md). Můžete také zadat `permission` ověřování pro předplatné nebo skupinu prostředků.  

Řízení předplatného přijímá seznam oborů názvů poskytovatele prostředků. Můžete například zadat **Microsoft. COMPUTE**. Pokud uživatel vybere předplatné, které nepodporují poskytovatele prostředků, zobrazí se chybová zpráva. K této chybě dojde, pokud poskytovatel prostředků není v tomto předplatném zaregistrován a uživatel nemá oprávnění k registraci poskytovatele prostředků.  

Ovládací prvek skupina prostředků má možnost pro `allowExisting` . Když `true` Uživatelé můžou vybrat skupiny prostředků, které už mají prostředky. Tento příznak je nejvhodnější pro šablony řešení, kde výchozí chování vyžaduje, aby uživatelé vybrali novou nebo prázdnou skupinu prostředků. Ve většině dalších scénářů je zadání této vlastnosti nepotřebné.  

## <a name="steps"></a>Kroky

Vlastnost kroky obsahuje nula nebo více dalších kroků, které se zobrazí po základech. Každý krok obsahuje jeden nebo více prvků. Zvažte přidání kroků na roli nebo vrstvu nasazené aplikace. Přidejte například krok pro vstupy hlavního uzlu a krok pro pracovní uzly v clusteru.

```json
"steps": [
    {
        "name": "demoConfig",
        "label": "Configuration settings",
        "elements": [
          ui-elements-needed-to-create-the-instance
        ]
    }
]
```

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
