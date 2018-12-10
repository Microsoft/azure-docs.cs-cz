---
title: Postupy bezpečného nasazení napříč oblastmi - Azure Deployment Manager
description: Popisuje postup nasazení služby v mnoha oblastech Azure Deployment Manager. Zobrazuje postupy bezpečného nasazení, aby se ověřilo nasazení před zavedením do všech oblastí.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/09/2018
ms.author: tomfitz
ms.custom: seodec18
ms.openlocfilehash: a615ab26e4ea046ced70ce2c154a0c304b741986
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53138343"
---
# <a name="enable-safe-deployment-practices-with-azure-deployment-manager-private-preview"></a>Povolit postupy bezpečného nasazení s Azure Deployment Manager (privátní verze Preview)

K nasazení vaší služby napříč mnoha oblastmi a ujistěte se, že funguje podle očekávání v každé oblasti, můžete použít Azure Deployment Manager pro koordinaci postupné zavedení služby. Stejně jako pro nasazení v Azure, můžete definovat prostředky pro vaši službu v [šablon Resource Manageru](resource-group-authoring-templates.md). Po vytvoření šablony, pomocí Správce nasazení popisující topologii pro vaši službu a jak mají být zahrnuty navýšení kapacity.

Deployment Manager je funkce Resource Manageru. Vaše možnosti rozšiřuje během nasazení. Pomocí Správce nasazení v případě, že máte komplexní služby, který musí být nasazený do několika oblastí. Postupným zavedením služby, můžete najít potenciální problémy dříve, než bude nasazena do všech oblastí. Pokud není nutné další bezpečnostní opatření postupné zavádění, použijte standardní [možnosti nasazení](resource-group-template-deploy-portal.md) pro Resource Manager. Deployment Manager se hladce integrují s všechny stávající nástroje třetích stran, které podporují nasazení Resource Manageru, jako jsou například průběžná integrace a průběžné doručování (CI/CD) nabídky. 

Azure Deployment Manager je ve verzi Private Preview. Chcete-li používat Azure Deployment Manager, proveďte [registrační formulář](https://aka.ms/admsignup). Pomoc se tím, že poskytuje vylepšení funkce [zpětnou vazbu](https://aka.ms/admfeedback).

Pokud chcete použít nástroj Deployment Manager, budete muset vytvořit čtyři soubory:

* Topologie šablony
* Nasazení šablony
* Soubor parametrů pro topologii
* Soubor parametrů pro zavedení

Nasazení šablony topologie před nasazením šablony nasazení.

Referenční dokumentace rozhraní REST API služby Azure Deployment Manager můžete najít [tady](https://docs.microsoft.com/rest/api/deploymentmanager/).

## <a name="supported-locations"></a>Podporovaná umístění

Ve verzi Preview Deployment Manager prostředky jsou podporovány ve středu USA a východní USA 2. Při definování prostředků v šablonách topologie a uvedení do provozu, jako jsou jednotky služby, zdroje artefaktů a uvedení popsaných v tomto článku musíte zadat jednu z těchto oblastí pro umístění. Prostředky, které nasazujete, vytvoření služby, jako jsou virtuální počítače, účty storage a web apps, jsou však podporovány ve všech svých [standardní umístění](https://azure.microsoft.com/global-infrastructure/services/?products=all).  

## <a name="identity-and-access"></a>Identita a přístup

Pomocí nástroje Deployment Manager [uživatelsky přiřazené identity spravované](../active-directory/managed-identities-azure-resources/overview.md) provede akce nasazení. Tuto identitu můžete vytvořit před spuštěním nasazení. Musí mít přístup k předplatnému, které nasazujete službu do a dostatečná oprávnění k dokončení nasazení. Informace o akcích udělit prostřednictvím rolí najdete v tématu [předdefinované role pro prostředky Azure](../role-based-access-control/built-in-roles.md).

Identita se musí nacházet v jednom z podporovaných umístění Deployment Manager a musí se nacházet ve stejném umístění jako uvedení.

## <a name="topology-template"></a>Topologie šablony

Šablona topologie popisuje prostředky Azure, které tvoří vaši službu a, jak je nasadíte. Topologie služby příklad na následujícím obrázku:

![Hierarchii z topologie služby a služby pro službu jednotky](./media/deployment-manager-overview/service-topology.png)

Šablona topologie obsahuje následující prostředky:

* Zdroje artefaktů – kde jsou uloženy šablon Resource Manageru a parametry
* Topologie služby – odkazuje na zdroj artefaktu
  * Služby – určuje umístění a ID předplatného Azure
    * Service jednotky – určuje skupinu prostředků, režim nasazení a cesta k souboru šablony a parametrů

Vysvětlení, co se stane, že na všech úrovních, je užitečné, chcete-li zobrazit hodnoty, které zadáte.

![Hodnoty pro každou úroveň](./media/deployment-manager-overview/topology-values.png)

### <a name="artifact-source-for-templates"></a>Zdroje artefaktů pro šablony

V šabloně topologie vytvoříte zdroj artefaktu, který obsahuje soubory šablony a parametrů. Zdroje artefaktů je způsob, jak o přijetí změn soubory pro nasazení. Zobrazí se vám jiného zdroje artefaktů pro binární soubory dále v tomto článku.

Následující příklad ukazuje obecný formát zdroje artefaktu.

```json
{
    "type": "Microsoft.DeploymentManager/artifactSources",
    "name": "<artifact-source-name>",
    "location": "<artifact-source-location>",
    "apiVersion": "2018-09-01-preview",
    "properties": {
        "sourceType": "AzureStorage",
        "artifactRoot": "<root-folder-for-templates>",
        "authentication": {
            "type": "SAS",
            "properties": {
                "sasUri": "<SAS-URI-for-storage-container>"
            }
        }
    }
}
```

Další informace najdete v tématu [referenčními informacemi k šablonám artifactSources](/azure/templates/Microsoft.DeploymentManager/artifactSources).

### <a name="service-topology"></a>Topologie služby

Následující příklad ukazuje obecný formát prostředek topologie služby. Můžete zadat ID prostředku zdroje artefaktu, který obsahuje šablony a soubory parametrů. Topologie služby zahrnuje všechny prostředky služby. Pokud chcete mít jistotu, že je k dispozici zdroj artefaktu, na ní závisí služba topologie.

```json
{
    "type": "Microsoft.DeploymentManager/serviceTopologies",
    "name": "<topology-name>",
    "location": "<topology-location>",
    "apiVersion": "2018-09-01-preview",
    "properties": {
        "artifactSourceId": "<resource-ID-artifact-source>"
    },
    "dependsOn": [
        "<artifact-source>"
    ],
    "resources": [
        {
            "type": "services",
            ...
        }
    ]
}
```

Další informace najdete v tématu [referenčními informacemi k šablonám serviceTopologies](/azure/templates/Microsoft.DeploymentManager/serviceTopologies).

### <a name="services"></a>Služby

Následující příklad ukazuje obecný formát prostředku služby. V každé služby je zadat ID předplatného umístěním a Azure pro nasazení vaší služby. K nasazení do několika oblastí, můžete definovat služby pro každou oblast. Služba závisí na topologii služby.

```json
{
    "type": "services",
    "name": "<service-name>",
    "location": "<service-location>",
    "apiVersion": "2018-09-01-preview",
    "dependsOn": [
        "<service-topology>"
    ],
    "properties": {
        "targetSubscriptionId": "<subscription-ID>",
        "targetLocation": "<location-of-deployed-service>"
    },
    "resources": [
        {
            "type": "serviceUnits",
            ...
        }
    ]
}
```

Další informace najdete v tématu [služby referenčními informacemi k šablonám](/azure/templates/Microsoft.DeploymentManager/serviceTopologies/services).

### <a name="service-units"></a>Jednotky služeb

Následující příklad ukazuje obecný formát prostředek jednotky služby. V každé jednotce služby zadejte skupinu prostředků, [režim nasazení](deployment-modes.md) pro nasazení a cesta k souboru šablony a parametrů. Pokud chcete zadat relativní cestu pro šablonu a parametry, úplná cesta je vytvořen z kořenové složky ve zdroji artefaktů. Můžete zadat absolutní cestu pro šablonu a parametry, ale můžete ztratit možnost snadno verzi vydávání verzí. Jednotky služby závisí na službě.

```json
{
    "type": "serviceUnits",
    "name": "<service-unit-name>",
    "location": "<service-unit-location>",
    "apiVersion": "2018-09-01-preview",
    "dependsOn": [
        "<service>"
    ],
    "tags": {
        "serviceType": "Service West US Web App"
    },
    "properties": {
        "targetResourceGroup": "<resource-group-name>",
        "deploymentMode": "Incremental",
        "artifacts": {
            "templateArtifactSourceRelativePath": "<relative-path-to-template>",
            "parametersArtifactSourceRelativePath": "<relative-path-to-parameter-file>"
        }
    }
}
```

Každá šablona by měla obsahovat související prostředky, které chcete nasadit v jednom kroku. Jednotky služby může mít například šablonu, která nasadí všechny prostředky pro vaši službu front-endu.

Další informace najdete v tématu [referenčními informacemi k šablonám serviceUnits](/azure/templates/Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits).

## <a name="rollout-template"></a>Nasazení šablony

Šablona uvedení popisuje postup při nasazování služby. Zadáte topologie služby a definovat pořadí pro nasazení služby jednotky. Obsahuje zdroj artefaktu pro ukládání binárních souborů pro nasazení. V šabloně zavedení definovat následující hierarchie:

* Zdroj artefaktu
* Krok
* Zavedení
  * Skupiny kroku
    * Operace nasazení

Hierarchie šablona uvedení na následujícím obrázku:

![Hierarchie od uvedení kroky](./media/deployment-manager-overview/Rollout.png)

Každý zavedení může mít mnoho skupin kroků. Jedna operace nasazení, který odkazuje na službu jednotku v topologii služby má každá skupina kroku.

### <a name="artifact-source-for-binaries"></a>Zdroje artefaktů pro binární soubory

V šabloně zavedení vytvoříte zdroj artefaktu pro binární soubory, které potřebujete k nasazení do služby. Je podobný tomuto zdroji artefaktů [zdroje artefaktu pro šablony](#artifact-source-for-templates), s tím rozdílem, že obsahuje skripty, webové stránky, zkompilovaného kódu nebo jiné soubory potřebné pro vaši službu.

### <a name="steps"></a>Kroky

Můžete definovat krok provést před nebo po operaci nasazení. V současné době pouze `wait` krokem je k dispozici. Krok čekání pozastaví nasazení, než budete pokračovat. Umožňuje ověřit, že vaše služba je spuštěná podle očekávání před nasazením další jednotku služby. Následující příklad ukazuje obecný formát krok čekání.

```json
{
    "apiVersion": "2018-09-01-preview",
    "type": "Microsoft.DeploymentManager/steps",
    "name": "waitStep",
        "location": "<step-location>",
    "properties": {
        "stepType": "wait",
        "attributes": {
          "duration": "PT1M"
        }
    }
},
```

Vlastnost duration používá [standardu ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Durations). Předchozí příklad určuje minutu trvající čekání.

Další informace najdete v tématu [kroky referenčními informacemi k šablonám](/azure/templates/Microsoft.DeploymentManager/steps).

### <a name="rollouts"></a>Uvedení

Pokud chcete mít jistotu, že je k dispozici zdroj artefaktu, uvedení závisí na něm. Uvedení definuje skupiny kroků pro každou službu jednotku, která je nasazena. Můžete definovat akce prováděné před nebo po nasazení. Například můžete určit, že nasazení počkejte po nasazení služby jednotky. Můžete definovat pořadí skupin kroků.

Určuje objekt identita [uživatelsky přiřazené identity spravované](#identity-and-access) , který provede akce související s nasazením.

Následující příklad ukazuje obecný formát uvedení.

```json
{
    "type": "Microsoft.DeploymentManager/rollouts",
    "name": "<rollout-name>",
    "location": "<rollout-location>",
    "apiVersion": "2018-09-01-preview",
    "Identity": {
        "type": "userAssigned",
        "identityIds": [
            "<managed-identity-ID>"
        ]
    },
    "dependsOn": [
        "<artifact-source>"
    ],
    "properties": {
        "buildVersion": "1.0.0.0",
        "artifactSourceId": "<artifact-source-ID>",
        "targetServiceTopologyId": "<service-topology-ID>",
        "stepGroups": [
            {
                "name": "stepGroup1",
                "dependsOnStepGroups": ["<step-group-name>"],
                "preDeploymentSteps": ["<step-ID>"],
                "deploymentTargetId":
                    "<service-unit-ID>",
                "postDeploymentSteps": ["<step-ID>"]
            },
            ...
        ]
    }
}
```

Další informace najdete v tématu [referenčními informacemi k šablonám uvedení](/azure/templates/Microsoft.DeploymentManager/rollouts).

## <a name="parameter-file"></a>Soubor s parametry

Můžete vytvořit dva soubory parametrů. Jeden soubor parametr se používá při nasazování topologie služby a druhé se používá pro nasazení zavedení. Existují některé hodnoty, třeba Ujistěte se, že jsou stejné v obou souborech parametru.  

## <a name="containerroot-variable"></a>containerRoot proměnné

Se systémovou správou verzí nasazení se změní cesta k artefaktům s každou novou verzí. Při prvním spuštění nasazení cesta může být `https://<base-uri-blob-container>/binaries/1.0.0.0`. Při druhém volání může být `https://<base-uri-blob-container>/binaries/1.0.0.1`. Deployment Manager usnadňuje získávání správnou kořenovou cestu pro aktuální nasazení s použitím `$containerRoot` proměnné. Tato hodnota změní s jednotlivými verzemi a není známý před nasazením.

Použití `$containerRoot` proměnné v souboru parametrů pro šablonu nasazení prostředků Azure. V době nasazení je tato proměnná nahradit skutečnými hodnotami od uvedení. 

Třeba při uvedení vytvořit zdroj artefaktu binární artefaktů.

```json
{
    "type": "Microsoft.DeploymentManager/artifactSources",
    "name": "[variables('rolloutArtifactSource').name]",
    "location": "[parameters('azureResourceLocation')]",
    "apiVersion": "2018-09-01-preview",
    "properties": {
        "sourceType": "AzureStorage",
        "artifactRoot": "[parameters('binaryArtifactRoot')]",
        "authentication" :
        {
            "type": "SAS",
            "properties": {
                "sasUri": "[parameters('artifactSourceSASLocation')]"
            }
        }
    }
},
```

Všimněte si, že `artifactRoot` a `sasUri` vlastnosti. Kořenový adresář artefaktů může být nastaveno na hodnotu jako `binaries/1.0.0.0`. Identifikátor URI SAS je identifikátor URI pro kontejner úložiště pomocí tokenu SAS pro přístup. Deployment Manager automaticky vytvoří hodnotu `$containerRoot` proměnné. Kombinuje tyto hodnoty ve formátu `<container>/<artifactRoot>`.

Soubor šablony a parametrů znát správnou cestu k zařazení verze binárních souborů. Například nasazení souborů pro webovou aplikaci, vytvořte následující soubor s parametry s proměnnou $containerRoot. Je nutné použít dvě zpětná lomítka (`\\`) pro cestu vzhledem k tomu, že je první znak escape.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "deployPackageUri": {
            "value": "$containerRoot\\helloWorldWebAppWUS.zip"
        }
    }
}
```

Potom použijte tento parametr v šabloně:

```json
{
    "name": "MSDeploy",
    "type": "extensions",
    "location": "[parameters('location')]",
    "apiVersion": "2015-08-01",
    "dependsOn": [
        "[concat('Microsoft.Web/sites/', parameters('WebAppName'))]"
    ],
    "tags": {
        "displayName": "WebAppMSDeploy"
    },
    "properties": {
        "packageUri": "[parameters('deployPackageURI')]"
    }
}
```

Správa verzí nasazení vytvořením nové složky a předáním tohoto kořenového během uvedení. Cesta prochází do šablony, který se nasazuje prostředky.

## <a name="next-steps"></a>Další postup

V tomto článku jste se dozvěděli o Deployment Manager. Přejděte k dalším článku se dozvíte, jak nasadit pomocí nástroje Deployment Manager.

> [!div class="nextstepaction"]
> [Kurz: Použití Azure Deployment Manager pomocí šablon Resource Manageru](./deployment-manager-tutorial.md)