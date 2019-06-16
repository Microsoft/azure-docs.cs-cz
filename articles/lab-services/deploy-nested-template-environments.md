---
title: Nasazení vnořených prostředí šablony Resource Manageru ve službě Azure DevTest Labs | Dokumentace Microsoftu
description: Informace o nasazení vnořených šablon Azure Resource Manageru zajistit prostředí s Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2019
ms.author: spelluru
ms.openlocfilehash: eec0cde4a36449f85998bfb04d16f1d52c68bb19
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65835282"
---
# <a name="deploy-nested-azure-resource-manager-templates-for-testing-environments"></a>Nasazení vnořených šablon Azure Resource Manageru pro testovací prostředí
Vnořené nasazení můžete provést další šablony Azure Resource Manageru z v rámci hlavní šablony Resource Manageru. Umožňuje rozložit na sadu cílových a zaměřené na konkrétní účel šablony nasazení. Poskytuje výhody z hlediska testování, opakované použití a čitelnost. Tento článek [použití propojených šablon při nasazování prostředků Azure](../azure-resource-manager/resource-group-linked-templates.md) dobrý přehled o tomto řešení poskytuje několik vzorových kódů. Tento článek poskytuje příklad, který je specifický pro Azure DevTest Labs. 

## <a name="key-parameters"></a>Parametry klíče
Šablony Resource Manageru můžete vytvořit úplně od začátku, doporučujeme použít [projekt skupiny prostředků Azure](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) v sadě Visual Studio, který usnadňuje vývoj a ladění šablon. Když přidáte vnořené nasazení prostředků do azuredeploy.json, Visual Studio přidá několik položek, chcete-li šablonu flexibilnější. Tyto položky zahrnout podsložky se sekundární soubor šablony a parametrů, názvy proměnných v rámci souboru hlavní šablony a dva parametry pro umístění úložiště pro nové soubory. **_ArtifactsLocation** a **_artifactsLocationSasToken** jsou klíčové parametry, které používá DevTest Labs. 

Pokud nejste obeznámeni s tím, jak funguje DevTest Labs s prostředím, přečtěte si téma [vytvoření prostředí více virtuálních počítačů a prostředků PaaS pomocí šablony Azure Resource Manageru](devtest-lab-create-environment-from-arm.md). Šablony jsou uloženy v úložišti propojen testovací prostředí v DevTest Labs. Když vytvoříte nové prostředí s využitím těchto šablon, soubory přesunou do kontejneru služby Azure Storage v testovacím prostředí. Aby bylo možné identifikovat a zkopírovat vnořené soubory, DevTest Labs identifikuje parametry _artifactsLocation a _artifactsLocationSasToken a zkopíruje podsložky do kontejneru úložiště. Potom automaticky vloží umístění a token sdíleného přístupového podpisu (SaS) do parametrů. 

## <a name="nested-deployment-example"></a>Příklad vnořené nasazení
Tady je jednoduchý příklad vnořené nasazení:

```json

"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
"contentVersion": "1.0.0.0",
"parameters": {
    "_artifactsLocation": {
        "type": "string"
    },
    "_artifactsLocationSasToken": {
        "type": "securestring"
    }},
"variables": {
    "NestOneTemplateFolder": "nestedtemplates",
    "NestOneTemplateFileName": "NestOne.json",
    "NestOneTemplateParametersFileName": "NestOne.parameters.json"},
    "resources": [
    {
        "name": "NestOne",
        "type": "Microsoft.Resources/deployments",
        "apiVersion": "2016-09-01",
        "dependsOn": [ ],
        "properties": {
            "mode": "Incremental",
            "templateLink": {
                "uri": "[concat(parameters('_artifactsLocation'), '/', variables('NestOneTemplateFolder'), '/', variables('NestOneTemplateFileName'), parameters('_artifactsLocationSasToken'))]",
                "contentVersion": "1.0.0.0"
            },
            "parametersLink": {
                "uri": "[concat(parameters('_artifactsLocation'), '/', variables('NestOneTemplateFolder'), '/', variables('NestOneTemplateParametersFileName'), parameters('_artifactsLocationSasToken'))]",
                "contentVersion": "1.0.0.0"
            }
        }    
    }],
"outputs": {}
```

Složky v úložišti, který obsahuje tato šablona obsahuje podsložky `nestedtemplates` se soubory **NestOne.json** a **NestOne.parameters.json**. V **azuredeploy.json**, identifikátor URI pro šablony se vytvořil pomocí artefaktů umístění, složky vnořené šablony vnořené název souboru šablony. Identifikátor URI pro parametry Podobně se vytvořil pomocí artefaktů umístění, vnořené šablony složku a soubor parametrů pro vnořené šablony. 

Zde je snímek stejnou strukturu projektu v sadě Visual Studio: 

![Struktura projektu v sadě Visual Studio](./media/deploy-nested-template-environments/visual-studio-project-structure.png)

Můžete přidat další složky ve složce primární, ale ne všechny hlouběji než jedné úrovni. 

## <a name="next-steps"></a>Další postup
Naleznete v následujících článcích pro podrobnosti o prostředí: 

- [Vytvoření prostředí více virtuálních počítačů a prostředků PaaS pomocí šablony Azure Resource Manageru](devtest-lab-create-environment-from-arm.md)
- [Konfigurace a používání veřejných prostředích ve službě Azure DevTest Labs](devtest-lab-configure-use-public-environments.md)
- [Připojení prostředí k virtuální síti testovacího prostředí ve službě Azure DevTest Labs](connect-environment-lab-virtual-network.md)