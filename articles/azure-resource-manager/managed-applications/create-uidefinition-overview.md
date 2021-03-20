---
title: CreateUiDefinition.jsv souboru pro podokno portálu
description: Popisuje, jak vytvořit definice uživatelského rozhraní pro Azure Portal. Používá se při definování Azure Managed Applications.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: tomfitz
ms.openlocfilehash: 327fa1d7eb73d8e65bb4f81c1dff0fe2bec2913b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "89319559"
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
            "isWizard": false,
            "basics": { }
        },
        "basics": [ ],
        "steps": [ ],
        "outputs": { },
        "resourceTypes": [ ]
    }
}
```

`CreateUiDefinition`Vždy obsahuje tři vlastnosti:

* popisovač
* verze
* parameters

Obslužná rutina by měla být vždycky `Microsoft.Azure.CreateUIDef` a nejnovější podporovaná verze je `0.1.2-preview` .

Schéma vlastnosti Parameters závisí na kombinaci zadané obslužné rutiny a verze. U spravovaných aplikací jsou podporované vlastnosti `config` , `basics` , a `steps` `outputs` . Použijete `config` pouze v případě, že potřebujete přepsat výchozí chování `basics` kroku. Základní a postupové vlastnosti obsahují [prvky](create-uidefinition-elements.md) , jako jsou textová pole a rozevírací seznamy, které se mají zobrazit v Azure Portal. Vlastnost Outputs slouží k mapování výstupních hodnot určených prvků na parametry šablony Azure Resource Manager.

Zahrnutí `$schema` je doporučeno, ale volitelné. Je-li tento parametr zadán, musí hodnota pro `version` odpovídat verzi v `$schema` identifikátoru URI.

Pomocí editoru JSON můžete vytvořit createUiDefinition a potom ho otestovat v [izolovaném prostoru createUiDefinition](https://portal.azure.com/?feature.customPortal=false&#blade/Microsoft_Azure_CreateUIDef/SandboxBlade) a zobrazit jeho náhled. Další informace o izolovaném prostoru naleznete v tématu [Test rozhraní portálu pro Azure Managed Applications](test-createuidefinition.md).

## <a name="config"></a>Config

`config`Vlastnost je nepovinná. Použijte ji k přepsání výchozího chování kroku základy nebo k nastavení rozhraní jako podrobného průvodce. Pokud `config` se použije, je to první vlastnost v oddílu **createUiDefinition.jsv** souboru `parameters` . Následující příklad ukazuje dostupné vlastnosti.

```json
"config": {
    "isWizard": false,
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

### <a name="wizard"></a>Tip

Tato `isWizard` vlastnost vám umožní před pokračováním na další krok vyžadovat úspěšné ověření každého kroku. Pokud `isWizard` vlastnost není zadána, výchozí hodnota je **false** a podrobné ověřování není vyžadováno.

Pokud `isWizard` je povoleno, nastavte na **hodnotu true**, karta **základy** je k dispozici a všechny ostatní karty jsou zakázané. Když je vybráno tlačítko **Další** , ikona karty indikuje, zda bylo ověření karty úspěšné nebo selhalo. Po dokončení a ověření povinných polí karty můžete na další kartu povolit navigaci tlačítkem **Další** . Když všechny karty projdou ověřením, můžete přejít na stránku **Kontrola a vytvoření** a kliknutím na tlačítko **vytvořit** zahájit nasazení.

:::image type="content" source="./media/create-uidefinition-overview/tab-wizard.png" alt-text="Průvodce kartami":::

### <a name="override-basics"></a>Základní informace o potlačení

Základní konfigurace umožňuje přizpůsobit krok základy.

Pro `description` Zadejte řetězec s podporou Markdownu, který popisuje váš prostředek. Podporují se víceřádkové formáty a odkazy.

`subscription`Prvky a `resourceGroup` umožňují zadat další ověření. Syntaxe pro určení platnosti je shodná s vlastním ověřením pro [textové pole](microsoft-common-textbox.md). Můžete také zadat `permission` ověřování pro předplatné nebo skupinu prostředků.  

Řízení předplatného přijímá seznam oborů názvů poskytovatele prostředků. Můžete například zadat **Microsoft. COMPUTE**. Pokud uživatel vybere předplatné, které nepodporují poskytovatele prostředků, zobrazí se chybová zpráva. K této chybě dojde, pokud poskytovatel prostředků není v tomto předplatném zaregistrován a uživatel nemá oprávnění k registraci poskytovatele prostředků.  

Ovládací prvek skupina prostředků má možnost pro `allowExisting` . Když `true` Uživatelé můžou vybrat skupiny prostředků, které už mají prostředky. Tento příznak je nejvhodnější pro šablony řešení, kde výchozí chování vyžaduje, aby uživatelé vybrali novou nebo prázdnou skupinu prostředků. Ve většině dalších scénářů je zadání této vlastnosti nepotřebné.  

Pro `location` Zadejte vlastnosti ovládacího prvku umístění, který chcete přepsat. Všechny vlastnosti, které nejsou přepsány, jsou nastaveny na výchozí hodnoty. `resourceTypes` přijímá pole řetězců obsahující plně kvalifikované názvy typů prostředků. Možnosti umístění jsou omezené jenom na oblasti, které podporují typy prostředků.  `allowedValues`   přijímá pole řetězců oblastí. V rozevíracím seznamu se zobrazí pouze tyto oblasti.Můžete nastavit i `allowedValues`    `resourceTypes` . Výsledkem je průnik obou seznamů. Nakonec `visible` lze vlastnost použít k podmíněnému nebo úplnému vypnutí rozevíracího seznamu umístění.  

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

## <a name="steps"></a>Postup

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
