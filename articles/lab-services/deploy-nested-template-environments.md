---
title: Nasazení vnořených prostředí šablon v laboratořích Azure DevTest Labs
description: Zjistěte, jak nasadit vnořené šablony Azure Resource Manageru, aby poskytovaly prostředí pomocí Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: e83bc4e77a44f20d55fa3b56bc81aefd1d25bb03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76168827"
---
# <a name="deploy-nested-azure-resource-manager-templates-for-testing-environments"></a>Nasazení vnořených šablon Azure Resource Managerpro testovací prostředí
Vnořené nasazení umožňuje spouštět další šablony Azure Resource Manager z v rámci hlavní šablony Správce prostředků. Umožňuje rozložit nasazení do sady cílových a účelově specifických šablon. Poskytuje výhody, pokud jde o testování, opakované použití a čitelnost. Článek [Použití propojených šablon při nasazování prostředků Azure](../azure-resource-manager/templates/linked-templates.md) poskytuje dobrý přehled o tomto řešení s několika ukázkami kódu. Tento článek obsahuje příklad, který je specifický pro Azure DevTest Labs. 

## <a name="key-parameters"></a>Klíčové parametry
Zatímco můžete vytvořit vlastní šablonu Správce prostředků od začátku, doporučujeme použít [projekt Skupiny prostředků Azure](../azure-resource-manager/templates/create-visual-studio-deployment-project.md) v sadě Visual Studio, což usnadňuje vývoj a ladění šablon. Když přidáte vnořený prostředek nasazení do azuredeploy.json, Visual Studio přidá několik položek, aby šablona byla flexibilnější. Tyto položky zahrnují podsložku se sekundární šablonou a souborem parametrů, názvy proměnných v hlavním souboru šablony a dva parametry pro umístění úložiště pro nové soubory. _artifactsLocation **_artifactsLocation** a **_artifactsLocationSasToken** jsou klíčové parametry, které devTest Labs používá. 

Pokud nejste obeznámeni s tím, jak DevTest Labs funguje s prostředími, najdete v tématu [Vytváření prostředí s více virtuálními počítači a prostředků PaaS pomocí šablon Azure Resource Manager](devtest-lab-create-environment-from-arm.md). Vaše šablony jsou uloženy v úložišti propojeném s testovacím prostředím v devTest Labs. Když vytvoříte nové prostředí s těmito šablonami, soubory se přesunou do kontejneru úložiště Azure v testovacím prostředí. Aby bylo možné identifikovat a zkopírovat vnořené soubory, DevTest Labs identifikuje _artifactsLocation a _artifactsLocationSasToken parametry a zkopíruje podsložky až do kontejneru úložiště. Poté automaticky vloží umístění a token sdíleného přístupového podpisu (SaS) do parametrů. 

## <a name="nested-deployment-example"></a>Příklad vnořeného nasazení
Zde je jednoduchý příklad vnořeného nasazení:

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

Složka v úložišti obsahující mj. `nestedtemplates` **NestOne.json** **NestOne.parameters.json** V **souboru azuredeploy.json**je identifikátor URI pro šablonu vytvořen pomocí umístění artefaktů, vnořené složky šablony, názvu souboru vnořené šablony. Podobně identifikátor URI pro parametry je sestaven pomocí umístění artefaktů, vnořené složky šablony a souboru parametrů pro vnořenou šablonu. 

Tady je obrázek stejné struktury projektu v sadě Visual Studio: 

![Struktura projektu v sadě Visual Studio](./media/deploy-nested-template-environments/visual-studio-project-structure.png)

Do primární složky můžete přidat další složky, ale ne hlouběji než jedna úroveň. 

## <a name="next-steps"></a>Další kroky
Podrobnosti o prostředích naleznete v následujících článcích: 

- [Vytvoření prostředí více virtuálních počítačů a prostředků PaaS pomocí šablony Azure Resource Manageru](devtest-lab-create-environment-from-arm.md)
- [Konfigurace a používání veřejných prostředí v laboratořích Azure DevTest Labs](devtest-lab-configure-use-public-environments.md)
- [Připojení prostředí k virtuální síti testovacího prostředí v laboratořích Azure DevTest Labs](connect-environment-lab-virtual-network.md)