---
title: Nasazení vnořených prostředí šablon v Azure DevTest Labs
description: Naučte se nasazovat vnořené Azure Resource Manager šablony, které poskytují prostředí s Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 39002e286fafd4f813333a14ed86256a517897e9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "85481336"
---
# <a name="deploy-nested-azure-resource-manager-templates-for-testing-environments"></a>Nasazení vnořených Azure Resource Manager šablon pro testovací prostředí
Vnořené nasazení umožňuje spouštět jiné šablony Azure Resource Manager v rámci hlavní Správce prostředků šablony. Umožňuje rozložit nasazení do sady cílových šablon a specifických pro účel. Poskytuje výhody v souvislosti s testováním, opětovným používáním a čitelností. Článek [použití propojených šablon při nasazení prostředků Azure](../azure-resource-manager/templates/linked-templates.md) poskytuje dobrý přehled tohoto řešení s několika ukázkami kódu. Tento článek poskytuje příklad, který je specifický pro Azure DevTest Labs. 

## <a name="key-parameters"></a>Parametry klíče
I když můžete vytvořit vlastní šablonu Správce prostředků od začátku, doporučujeme použít [projekt skupiny prostředků Azure](../azure-resource-manager/templates/create-visual-studio-deployment-project.md) v aplikaci Visual Studio, který usnadňuje vývoj a ladění šablon. Když přidáte vnořený prostředek nasazení k azuredeploy.jsv, Visual Studio přidá několik položek, aby bylo možné šablonu pružnější. Tyto položky zahrnují podsložku se sekundární šablonou a souborem parametrů, názvy proměnných v rámci hlavního souboru šablony a dva parametry pro umístění úložiště pro nové soubory. **_ArtifactsLocation** a **_artifactsLocationSasToken** jsou klíčové parametry, které používá DevTest Labs. 

Pokud nejste obeznámeni s tím, jak DevTest Labs pracuje s prostředími, přečtěte si téma [vytvoření prostředí s více virtuálními počítači a prostředků PaaS pomocí šablon Azure Resource Manager](devtest-lab-create-environment-from-arm.md). Vaše šablony jsou uložené v úložišti propojeném s testovacím prostředím v DevTest Labs. Když vytvoříte nové prostředí s těmito šablonami, soubory se přesunou do kontejneru Azure Storage v testovacím prostředí. Aby bylo možné identifikovat a kopírovat vnořené soubory, DevTest Labs identifikuje parametry _artifactsLocation a _artifactsLocationSasToken a kopíruje podsložky až do kontejneru úložiště. Pak automaticky vloží do parametrů umístění a token sdíleného přístupového podpisu (SaS). 

## <a name="nested-deployment-example"></a>Příklad vnořeného nasazení
Tady je jednoduchý příklad vnořeného nasazení:

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

Složka v úložišti, která obsahuje tuto šablonu, má podsložku `nestedtemplates` se soubory **NestOne.jsv** a **NestOne.parameters.jsna**. V **azuredeploy.jsna** je identifikátor URI pro šablonu sestaven pomocí umístění artefaktů, vnořené složky šablony, názvu vnořeného souboru šablony. Podobně identifikátor URI pro parametry je sestaven pomocí umístění artefaktů, vnořené složky šablony a souboru parametrů pro vnořenou šablonu. 

Zde je obrázek stejné struktury projektu v aplikaci Visual Studio: 

![Struktura projektu v aplikaci Visual Studio](./media/deploy-nested-template-environments/visual-studio-project-structure.png)

Do primární složky můžete přidat další složky, ale ne všechny hlubší než jenom jednu úroveň. 

## <a name="next-steps"></a>Další kroky
Podrobnosti o prostředích najdete v následujících článcích: 

- [Vytvoření prostředí více virtuálních počítačů a prostředků PaaS pomocí šablony Azure Resource Manageru](devtest-lab-create-environment-from-arm.md)
- [Konfigurace a použití veřejných prostředí v Azure DevTest Labs](devtest-lab-configure-use-public-environments.md)
- [Připojte prostředí k virtuální síti testovacího prostředí v Azure DevTest Labs](connect-environment-lab-virtual-network.md)