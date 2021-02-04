---
title: Bezpečné nasazení napříč oblastmi – Azure Správce nasazení
description: Naučte se, jak nasadit službu v mnoha oblastech pomocí Azure Správce nasazení a o postupech bezpečného nasazení.
ms.topic: conceptual
ms.date: 11/21/2019
ms.custom: seodec18
ms.openlocfilehash: baed44e04a0beca02cc959d302a4a29906b4a78e
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2021
ms.locfileid: "99539514"
---
# <a name="enable-safe-deployment-practices-with-azure-deployment-manager-public-preview"></a>Povolení postupů bezpečného nasazení pomocí Azure Správce nasazení (Public Preview)

Pokud chcete službu nasadit napříč mnoha oblastmi a zajistit, aby v každé oblasti běžela podle očekávání, můžete k koordinaci fáze zavedení služby využít Azure Správce nasazení. Stejně jako u jakéhokoli nasazení Azure definujete prostředky pro vaši službu v [Správce prostředků šablonách](template-syntax.md). Po vytvoření šablon použijete Správce nasazení k popisu topologie vaší služby a způsobu, jakým má být zavedena.

Správce nasazení je funkce Správce prostředků. Rozšiřuje vaše schopnosti během nasazování. Použijte Správce nasazení, pokud máte složitou službu, která musí být nasazena do několika oblastí. Postupným zavedením služby, můžete najít potenciální problémy dříve, než bude nasazena do všech oblastí. Pokud nepotřebujete další opatření pro dvoufázové zavedení, použijte pro Správce prostředků standardní [Možnosti nasazení](deploy-portal.md) . Správce nasazení bez problémů integruje se všemi existujícími nástroji třetích stran, které podporují nasazení Správce prostředků, jako jsou nabídky průběžné integrace a průběžného doručování (CI/CD).

Azure Správce nasazení je ve verzi Preview. Pomůžeme nám vylepšit funkci poskytnutím [zpětné vazby](https://aka.ms/admfeedback).

Chcete-li použít Správce nasazení, je nutné vytvořit čtyři soubory:

* Šablona topologie
* Šablona zavedení
* Soubor parametrů pro topologii
* Soubor parametrů pro zavedení

Šablonu topologie nasadíte před nasazením šablony zavedení.

Další prostředky:

* [Reference k Azure Správce nasazení REST API](/rest/api/deploymentmanager/).
* [Kurz: použití Správce nasazení Azure se šablonami správce prostředků](./deployment-manager-tutorial.md).
* [Kurz: použití kontroly stavu v Azure Správce nasazení](./deployment-manager-tutorial-health-check.md).
* [Ukázka Azure Správce nasazení](https://github.com/Azure-Samples/adm-quickstart).

## <a name="identity-and-access"></a>Identita a přístup

Při Správce nasazení provede [uživatelem přiřazenou spravovanou identitu](../../active-directory/managed-identities-azure-resources/overview.md) akce nasazení. Tuto identitu vytvoříte před zahájením nasazení. Musí mít přístup k předplatnému, ke kterému službu nasazujete, a dostatečné oprávnění k dokončení nasazení. Informace o akcích udělených prostřednictvím rolí najdete v tématu [předdefinované role Azure](../../role-based-access-control/built-in-roles.md).

Identita se musí nacházet ve stejném umístění jako zavedení.

## <a name="topology-template"></a>Šablona topologie

Šablona topologie popisuje prostředky Azure, které tvoří vaši službu a kam je nasazovat. Následující obrázek znázorňuje topologii ukázkové služby:

![Hierarchie z topologie služby až po jednotky služeb](./media/deployment-manager-overview/service-topology.png)

Šablona topologie obsahuje následující zdroje:

* Zdroj artefaktu – kde jsou uloženy šablony Správce prostředků a parametry.
* Topologie služby – odkazuje na zdroj artefaktů.
  * Služby – určuje umístění a ID předplatného Azure.
    * Jednotky služeb – určuje skupinu prostředků, režim nasazení a cestu k šablonám a souborům parametrů.

Chcete-li zjistit, co se stane na každé úrovni, je užitečné zjistit, které hodnoty jsou k dispozici.

![Hodnoty pro každou úroveň](./media/deployment-manager-overview/topology-values.png)

### <a name="artifact-source-for-templates"></a>Zdroj artefaktů pro šablony

V šabloně topologie vytvoříte zdroj artefaktů, který obsahuje soubory šablon a parametrů. Zdroj artefaktu je způsob, jak načíst soubory pro nasazení. Později v tomto článku uvidíte jiný zdroj artefaktů pro binární soubory.

Následující příklad ukazuje obecný formát zdroje artefaktu.

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

Další informace najdete v tématu [Referenční dokumentace k šablonám artifactSources](/azure/templates/Microsoft.DeploymentManager/artifactSources).

### <a name="service-topology"></a>Topologie služby

Následující příklad ukazuje obecný formát prostředku topologie služby. Zadejte ID prostředku pro zdroj artefaktu, který obsahuje šablony a soubory parametrů. Topologie služby zahrnuje všechny prostředky služby. Ujistěte se, že je zdroj artefaktů dostupný, protože topologie služby na něm závisí.

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

Další informace najdete v tématu [Referenční dokumentace k šablonám serviceTopologies](/azure/templates/Microsoft.DeploymentManager/serviceTopologies).

### <a name="services"></a>Služby

Následující příklad ukazuje obecný formát prostředku služeb. V každé službě zadáte umístění a ID předplatného Azure, které se použije k nasazení vaší služby. Pro nasazení do několika oblastí definujete službu pro každou oblast. Služba závisí na topologii služby.

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

Další informace najdete v tématu [Referenční dokumentace k šablonám služeb](/azure/templates/Microsoft.DeploymentManager/serviceTopologies/services).

### <a name="service-units"></a>Jednotky služeb

Následující příklad ukazuje obecný formát prostředku jednotek služby. V každé jednotce služby určíte skupinu prostředků, [režim nasazení](deployment-modes.md) , který se má použít pro nasazení, a cestu k šabloně a souboru parametrů. Pokud zadáte relativní cestu pro šablonu a parametry, úplná cesta je vytvořena z kořenové složky ve zdroji artefaktů. Můžete zadat absolutní cestu pro šablonu a parametry, ale ztratíte možnost snadné verze vašich vydání. Jednotka služby závisí na službě.

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

Každá šablona by měla obsahovat související prostředky, které chcete nasadit v jednom kroku. Například jednotka služby by mohla mít šablonu, která nasadí všechny prostředky pro front-end služby.

Další informace najdete v tématu [Referenční dokumentace k šablonám serviceUnits](/azure/templates/Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits).

## <a name="rollout-template"></a>Šablona zavedení

Šablona zavedení popisuje kroky, které je potřeba provést při nasazování služby. Zadáte topologii služby, která se má použít, a definujte pořadí pro nasazování jednotek služby. Obsahuje zdroj artefaktů pro ukládání binárních souborů pro nasazení. V šabloně zavedení definujte tuto hierarchii:

* Zdroj artefaktů
* Krok.
* Zavedení.
  * Skupiny kroků.
    * Operace nasazení.

Následující obrázek ukazuje hierarchii šablony zavedení:

![Hierarchie z zavedení do kroků](./media/deployment-manager-overview/Rollout.png)

Každé zavedení může mít mnoho skupin kroků. Každá skupina kroků má jednu operaci nasazení, která odkazuje na jednotku služby v topologii služby.

### <a name="artifact-source-for-binaries"></a>Zdroj artefaktů pro binární soubory

V šabloně zavedení vytvoříte zdroj artefaktů pro binární soubory, které potřebujete nasadit do služby. Tento zdroj artefaktu je podobný [zdroji artefaktů pro šablony](#artifact-source-for-templates), s výjimkou, že obsahuje skripty, webové stránky, zkompilovaný kód nebo jiné soubory, které vaše služba vyžaduje.

### <a name="steps"></a>Postup

Můžete definovat krok, který se provede buď před, nebo po operaci nasazení. V současné době `wait` `healthCheck` je k dispozici pouze krok a krok.

`wait`Krok před pokračováním pozastaví nasazení. Umožňuje ověřit, jestli je služba spuštěná podle očekávání, ještě než začnete nasazovat další jednotku služby. Následující příklad ukazuje obecný formát `wait` kroku.

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

Vlastnost Duration používá [Standard ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Durations). Předchozí příklad určuje čekání na jednu minutu.

Další informace o kontrolách stavu najdete v tématu [zavedení integrace stavu do azure Správce nasazení](./deployment-manager-health-check.md) a [kurzu: použití kontroly stavu ve službě Azure Správce nasazení](./deployment-manager-tutorial-health-check.md).

Další informace najdete v tématu [Referenční dokumentace k šablonám](/azure/templates/Microsoft.DeploymentManager/steps).

### <a name="rollouts"></a>Uvedení

Ujistěte se, že je zdroj artefaktů k dispozici, protože na něm závisí implementace. Zavedení definuje skupiny kroků pro každou nasazenou jednotku služby. Můžete definovat akce, které se mají provést před nebo po nasazení. Můžete například určit nasazení, které se má čekat po nasazení jednotky služby. Můžete definovat pořadí skupin kroků.

Objekt identity Určuje [spravovanou identitu přiřazenou uživatelem](#identity-and-access) , která provádí akce nasazení.

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

Další informace najdete v tématu [Referenční dokumentace k šablonám uvádění](/azure/templates/Microsoft.DeploymentManager/rollouts).

## <a name="parameter-file"></a>Soubor parametrů

Vytvoříte dva soubory parametrů. Jeden soubor parametrů se používá při nasazování topologie služby a druhá se používá pro nasazení zavedení. Existují některé hodnoty, které potřebujete, abyste se ujistili, že jsou stejné v obou souborech parametrů.

## <a name="containerroot-variable"></a>containerRoot – proměnná

U nasazení s použitím verzí se cesta k artefaktům mění s každou novou verzí. Při prvním spuštění nasazení může být cesta `https://<base-uri-blob-container>/binaries/1.0.0.0` . Druhý čas může být `https://<base-uri-blob-container>/binaries/1.0.0.1` . Správce nasazení zjednodušuje získávání správné kořenové cesty pro aktuální nasazení pomocí `$containerRoot` proměnné. Tato hodnota se změní v každé verzi a před nasazením není známa.

Použijte `$containerRoot` proměnnou v souboru parametrů pro šablonu, která nasazuje prostředky Azure. V době nasazení je tato proměnná nahrazena skutečnými hodnotami z zavedení.

Například při zavádění vytvoříte zdroj artefaktů pro binární artefakty.

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

Všimněte si `artifactRoot` `sasUri` vlastností a. Kořen artefaktu může být nastaven na hodnotu, například `binaries/1.0.0.0` . Identifikátor URI SAS je identifikátor URI kontejneru úložiště s tokenem SAS pro přístup. Správce nasazení automaticky vytvoří hodnotu `$containerRoot` proměnné. Kombinuje tyto hodnoty ve formátu `<container>/<artifactRoot>` .

Šablona a soubor parametrů potřebují znát správnou cestu pro získání binárních souborů s verzí. Pokud například chcete nasadit soubory pro webovou aplikaci, vytvořte následující soubor parametrů s `$containerRoot` proměnnou. Pro cestu je nutné použít dvě zpětná lomítka ( `\\` ), protože první je řídicí znak.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "deployPackageUri": {
      "value": "$containerRoot\\helloWorldWebAppWUS.zip"
    }
  }
}
```

Pak použijte tento parametr v šabloně:

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

Nasazení ve verzi můžete spravovat tak, že vytvoříte nové složky a během zavádění předáte tuto kořenovou cestu. Cesta pokračuje do šablony, která nasazuje prostředky.

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli o Správce nasazení. Přejděte k dalšímu článku a Naučte se, jak nasadit pomocí Správce nasazení.

> [!div class="nextstepaction"]
> [Kurz: použití Správce nasazení Azure se šablonami Správce prostředků](./deployment-manager-tutorial.md)
>
> [Rychlý Start: Vyzkoušejte si Azure Správce nasazení během několika minut](https://github.com/Azure-Samples/adm-quickstart)
