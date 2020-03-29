---
title: Bezpečné nasazení napříč oblastmi – Azure Deployment Manager
description: Popisuje, jak nasadit službu v mnoha oblastech pomocí Azure Deployment Manager. Zobrazuje postupy bezpečného nasazení k ověření stability vašeho nasazení před zavedením do všech oblastí.
ms.topic: conceptual
ms.date: 11/21/2019
ms.custom: seodec18
ms.openlocfilehash: 424cd79a6c63200e1f101cf178b1fd2c9083161e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76152523"
---
# <a name="enable-safe-deployment-practices-with-azure-deployment-manager-public-preview"></a>Povolení bezpečných postupů nasazení pomocí Azure Deployment Manager (Public Preview)

Chcete-li nasadit službu v mnoha oblastech a ujistěte se, že běží podle očekávání v každé oblasti, můžete použít Azure Deployment Manager koordinovat postupné zavedení služby. Stejně jako u každého nasazení Azure definujete prostředky pro vaši službu v [šablonách Správce prostředků](template-syntax.md). Po vytvoření šablon použijete Správce nasazení k popisu topologie pro vaši službu a způsobu jeho zavedení.

Správce nasazení je funkce Správce prostředků. Rozšiřuje vaše možnosti během nasazení. Správce nasazení použijte, pokud máte komplexní službu, kterou je třeba nasadit do několika oblastí. Postupným zavedením služby, můžete najít potenciální problémy dříve, než bude nasazena do všech oblastí. Pokud nepotřebujete další opatření pro postupné zavedení, použijte standardní [možnosti nasazení](deploy-portal.md) pro Resource Manager. Správce nasazení se bezproblémově integruje se všemi existujícími nástroji třetích stran, které podporují nasazení Správce prostředků, jako je průběžná integrace a průběžné doručování (CI/CD).

Azure Deployment Manager je ve verzi preview. Pomozte nám vylepšit funkci tím, že [poskytnete zpětnou vazbu](https://aka.ms/admfeedback).

Chcete-li použít Správce nasazení, musíte vytvořit čtyři soubory:

* Šablona topologie
* Šablona pro zavedení
* Soubor parametrů pro topologii
* Soubor parametrů pro zavedení

Před nasazením šablony pro zavedení nasadíte šablonu topologie.

Další prostředky:

- [Odkaz na rozhraní REST správce nasazení Azure](https://docs.microsoft.com/rest/api/deploymentmanager/).
- [Kurz: Použijte Správce nasazení Azure se šablonami Správce prostředků](./deployment-manager-tutorial.md).
- [Kurz: Použití kontroly stavu ve Správci nasazení Azure](./deployment-manager-tutorial-health-check.md).
- [Ukázka Správce nasazení Azure](https://github.com/Azure-Samples/adm-quickstart).

## <a name="identity-and-access"></a>Identita a přístup

Pomocí Správce nasazení provádí akce nasazení [spravovaná identita přiřazená uživatelem.](../../active-directory/managed-identities-azure-resources/overview.md) Tuto identitu vytvoříte před zahájením nasazení. Musí mít přístup k předplatnému, do jehož nasazení službu nasazujete, a dostatečná oprávnění k dokončení nasazení. Informace o akcích udělených prostřednictvím rolí najdete [v tématu Předdefinované role pro prostředky Azure](../../role-based-access-control/built-in-roles.md).

Identita musí být umístěna ve stejném umístění jako zavedení.

## <a name="topology-template"></a>Šablona topologie

Šablona topologie popisuje prostředky Azure, které tvoří vaši službu a kde je nasadit. Následující obrázek znázorňuje topologii ukázkové služby:

![Hierarchie od topologie služeb přes služby až po servisní jednotky](./media/deployment-manager-overview/service-topology.png)

Šablona topologie obsahuje následující zdroje:

* Zdroj artefaktů – kde jsou uloženy šablony a parametry Správce prostředků
* Topologie služby - odkazuje na zdroj artefaktů
  * Služby – určuje umístění a ID předplatného Azure
    * Jednotky služeb – určuje skupinu prostředků, režim nasazení a cestu k souboru šablony a parametru.

Chcete-li pochopit, co se děje na každé úrovni, je užitečné zjistit, které hodnoty zadáte.

![Hodnoty pro každou úroveň](./media/deployment-manager-overview/topology-values.png)

### <a name="artifact-source-for-templates"></a>Zdroj artefaktů pro šablony

V šabloně topologie vytvoříte zdroj artefaktů, který obsahuje soubory šablon a parametrů. Zdroj artefaktu je způsob, jak vytáhnout soubory pro nasazení. Zobrazí se další zdroj artefaktů pro binární soubory dále v tomto článku.

Následující příklad ukazuje obecný formát zdroje artefaktů.

```json
{
  "type": "Microsoft.DeploymentManager/artifactSources",
  "apiVersion": "2018-09-01-preview",
  "name": "<artifact-source-name>",
  "location": "<artifact-source-location>",
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

Další informace naleznete v [tématu reference šablony artifactSources](/azure/templates/Microsoft.DeploymentManager/artifactSources).

### <a name="service-topology"></a>Topologie služby

Následující příklad ukazuje obecný formát zdroje topologie služby. Zadáte ID prostředku zdroje artefaktu, který obsahuje šablony a soubory parametrů. Topologie služby zahrnuje všechny prostředky služby. Chcete-li se ujistit, že zdroj artefaktů je k dispozici, závisí na něm topologie služby.

```json
{
  "type": "Microsoft.DeploymentManager/serviceTopologies",
  "apiVersion": "2018-09-01-preview",
  "name": "<topology-name>",
  "location": "<topology-location>",
  "dependsOn": [
    "<artifact-source>"
  ],
  "properties": {
    "artifactSourceId": "<resource-ID-artifact-source>"
  },
  "resources": [
    {
      "type": "services",
        ...
        }
    ]
}
```

Další informace naleznete v [tématu serviceTopologies odkaz na šablonu](/azure/templates/Microsoft.DeploymentManager/serviceTopologies).

### <a name="services"></a>Služby

Následující příklad ukazuje obecný formát prostředku služeb. V každé službě poskytujete umístění a ID předplatného Azure, které se použije pro nasazení vaší služby. Chcete-li nasadit do několika oblastí, definujete službu pro každou oblast. Služba závisí na topologii služby.

```json
{
  "type": "services",
  "apiVersion": "2018-09-01-preview",
  "name": "<service-name>",
  "location": "<service-location>",
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

Další informace naleznete v [tématu odkazy na šablonu služeb](/azure/templates/Microsoft.DeploymentManager/serviceTopologies/services).

### <a name="service-units"></a>Jednotky služeb

Následující příklad ukazuje obecný formát prostředku jednotky servisu. V každé servisní jednotce určíte skupinu prostředků, [režim nasazení,](deployment-modes.md) který se má použít pro nasazení, a cestu k souboru šablony a parametru. Pokud zadáte relativní cestu pro šablonu a parametry, úplná cesta je vytvořena z kořenové složky ve zdroji artefaktů. Můžete zadat absolutní cestu pro šablonu a parametry, ale ztratíte možnost snadno verze verze. Servisní jednotka závisí na službě.

```json
{
  "type": "serviceUnits",
  "apiVersion": "2018-09-01-preview",
  "name": "<service-unit-name>",
  "location": "<service-unit-location>",
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

Každá šablona by měla obsahovat související prostředky, které chcete nasadit v jednom kroku. Servisní jednotka může mít například šablonu, která nasazuje všechny prostředky pro front-end vaší služby.

Další informace naleznete v [tématu serviceUnits reference template .](/azure/templates/Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits)

## <a name="rollout-template"></a>Šablona pro zavedení

Šablona zavedení popisuje kroky, které je třeba provést při nasazování služby. Zadáte topologii služby, která se má použít, a definujete pořadí pro nasazení jednotek služby. Obsahuje zdroj artefaktů pro ukládání binárních souborů pro nasazení. V šabloně pro zavedení definujete následující hierarchii:

* Zdroj artefaktů
* Krok
* Zavádění
  * Skupiny kroků
    * Operace nasazení

Následující obrázek znázorňuje hierarchii šablony pro zavedení:

![Hierarchie od zavedení k krokům](./media/deployment-manager-overview/Rollout.png)

Každé zavedení může mít mnoho skupin kroků. Každá skupina kroků má jednu operaci nasazení, která odkazuje na servisní jednotku v topologii služby.

### <a name="artifact-source-for-binaries"></a>Zdroj artefaktů pro binární soubory

V šabloně zavedení vytvoříte zdroj artefaktů pro binární soubory, které potřebujete nasadit do služby. Tento zdroj artefaktů je podobný [zdroji artefaktů pro šablony](#artifact-source-for-templates)s tím rozdílem, že obsahuje skripty, webové stránky, zkompilovaný kód nebo jiné soubory potřebné pro vaši službu.

### <a name="steps"></a>Kroky

Můžete definovat krok provést před nebo po operaci nasazení. V současné době `wait` jsou k dispozici pouze krok a krok "healthCheck".

Krok čekání pozastaví nasazení před pokračováním. Umožňuje ověřit, že vaše služba běží podle očekávání před nasazením další jednotky služby. Následující příklad ukazuje obecný formát kroku čekání.

```json
{
    "type": "Microsoft.DeploymentManager/steps",
    "apiVersion": "2018-09-01-preview",
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

Vlastnost duration používá [normu ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Durations). Předchozí příklad určuje jednominutové čekání.

Další informace o kroku kontroly stavu najdete v [tématu Zavedení zavedení integrace stavu do Správce nasazení Azure](./deployment-manager-health-check.md) a [kurz: Použití kontroly stavu ve Správci nasazení Azure](./deployment-manager-tutorial-health-check.md).

Další informace naleznete v [tématu odkaz na šablonu kroků](/azure/templates/Microsoft.DeploymentManager/steps).

### <a name="rollouts"></a>Uvedení

Chcete-li se ujistit, že zdroj artefaktu je k dispozici, závisí na něm zavedení. Zavedení definuje kroky skupiny pro každou jednotku služby, která je nasazena. Můžete definovat akce, které mají být podnikány před nebo po nasazení. Můžete například určit, že nasazení čeká po nasazení servisní jednotky. Můžete definovat pořadí skupin kroků.

Objekt identity určuje [uživatelem přiřazenou spravovanou identitu,](#identity-and-access) která provádí akce nasazení.

Následující příklad ukazuje obecný formát zavedení.

```json
{
  "type": "Microsoft.DeploymentManager/rollouts",
  "apiVersion": "2018-09-01-preview",
  "name": "<rollout-name>",
  "location": "<rollout-location>",
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

Další informace naleznete v [tématu reference šablony rollouts](/azure/templates/Microsoft.DeploymentManager/rollouts).

## <a name="parameter-file"></a>Soubor parametrů

Vytvoříte dva soubory parametrů. Jeden soubor parametrů se používá při nasazování topologie služby a druhý se používá pro nasazení zaváděcí. Existují některé hodnoty, které je třeba zajistit, aby byly stejné v obou souborech parametrů.

## <a name="containerroot-variable"></a>proměnná containerRoot

S nasazenís verzí se cesta k artefaktům mění s každou novou verzí. Při prvním spuštění nasazení může být `https://<base-uri-blob-container>/binaries/1.0.0.0`cesta . Podruhé by to `https://<base-uri-blob-container>/binaries/1.0.0.1`mohlo být . Správce nasazení zjednodušuje získání správné kořenové cesty `$containerRoot` pro aktuální nasazení pomocí proměnné. Tato hodnota se změní s každou verzí a není známa před nasazením.

Použijte `$containerRoot` proměnnou v souboru parametrů pro šablonu k nasazení prostředků Azure. V době nasazení je tato proměnná nahrazena skutečnými hodnotami z zavedení.

Například během zavádění vytvoříte zdroj artefaktů pro binární artefakty.

```json
{
  "type": "Microsoft.DeploymentManager/artifactSources",
  "apiVersion": "2018-09-01-preview",
  "name": "[variables('rolloutArtifactSource').name]",
  "location": "[parameters('azureResourceLocation')]",
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

Všimněte `artifactRoot` `sasUri` si vlastností a. Kořen artefaktu může být nastaven `binaries/1.0.0.0`na hodnotu jako . Identifikátor URI SAS je identifikátor URI pro váš kontejner úložiště s tokenem SAS pro přístup. Správce nasazení automaticky vytvoří hodnotu `$containerRoot` proměnné. Kombinuje tyto hodnoty ve `<container>/<artifactRoot>`formátu .

Soubor šablony a parametrů musí znát správnou cestu pro získání binárních souborů s verzí. Chcete-li například nasadit soubory pro webovou aplikaci, vytvořte následující soubor parametrů s proměnnou $containerRoot. Pro cestu je nutné`\\`použít dvě zpětná lomítka ( ), protože první je řídicí znak.

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
  "apiVersion": "2015-08-01",
  "type": "extensions",
  "location": "[parameters('location')]",
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

Můžete spravovat nasazení s verzí vytvořením nových složek a předávání tohoto kořene během zavádění. Cesta prochází do šablony, která nasazuje prostředky.

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli o Správce nasazení. Přejděte k dalšímu článku a zjistěte, jak nasadit pomocí Správce nasazení.

> [!div class="nextstepaction"]
> [Kurz: Použití Správce nasazení Azure se šablonami Správce prostředků](./deployment-manager-tutorial.md)
>
> [Úvodní příručka: Vyzkoušejte Azure Deployment Manager během několika minut](https://github.com/Azure-Samples/adm-quickstart)
