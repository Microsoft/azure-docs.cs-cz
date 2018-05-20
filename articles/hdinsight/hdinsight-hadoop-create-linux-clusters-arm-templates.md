---
title: Vytváření clusterů systému Hadoop pomocí šablon - Azure HDInsight | Microsoft Docs
description: Naučte se vytvářet clustery pro HDInsight pomocí šablony Resource Manageru
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 00a80dea-011f-44f0-92a4-25d09db9d996
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: jgao
ms.openlocfilehash: ae5085eb52210844b364113a436fa033da9daac7
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="create-hadoop-clusters-in-hdinsight-by-using-resource-manager-templates"></a>Vytvoření clusterů systému Hadoop v HDInsight pomocí šablony Resource Manageru
[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

V tomto článku se dozvíte několik způsobů, jak Azure HDInsight vytvářet clustery pomocí šablony Azure Resource Manager. Další informace najdete v tématu [nasazení aplikace pomocí šablony Azure Resource Manageru](../azure-resource-manager/resource-group-template-deploy.md). Další informace o dalších funkcí a nástrojů pro vytváření clusteru, klikněte na tlačítko volič karty v horní této stránce nebo v tématu [metody vytváření clusterů](hdinsight-hadoop-provision-linux-clusters.md#cluster-setup-methods).

## <a name="prerequisites"></a>Požadavky
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Podle pokynů v tomto článku, potřebujete:

* [Předplatné](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Prostředí Azure PowerShell nebo Azure CLI.

### <a name="resource-manager-templates"></a>Šablony Resource Manageru
Šablonu Resource Manager umožňuje snadné vytváření následující resoruces pro vaši aplikaci v rámci jediné koordinované operace:
* Clustery prostředí HDInsight a jejich závislé prostředky (například výchozí účet úložiště)
* Další prostředky (například Azure SQL Database k použití Apache Sqoop)

V šabloně definujete prostředky, které jsou potřebné pro aplikaci. Můžete určit taky parametry nasazení pro vstupní hodnoty pro různá prostředí. Šablona se skládá z JSON a výrazy, které můžete použít k vytvoření hodnot pro vaše nasazení.

Můžete najít ukázky šablony HDInsight v [šablon Azure rychlý Start](https://azure.microsoft.com/resources/templates/?term=hdinsight). Použít napříč platformami [Visual Studio Code](https://code.visualstudio.com/#alt-downloads) s [Resource Manager rozšíření](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools) nebo textovém editoru a uložit šablonu do souboru na pracovní stanici. 

Další informace o šablonách Resource Manager najdete v následujících článcích:

* [Vytváření šablon Azure Resource Manageru](../azure-resource-manager/resource-group-authoring-templates.md)
* [Nasazení aplikace pomocí šablony Azure Resource Manager](../azure-resource-manager/resource-group-template-deploy.md)

## <a name="generate-templates"></a>Generování šablon

Resource Manager umožňuje export šablony Resource Manageru ze stávajících prostředků ve vašem předplatném pomocí různých nástrojů. Z vygenerované šablony pak zjistíte syntaxi šablony a podle potřeby pak můžete automatizovat opakované nasazení svého řešení.

- Portál Azure: najdete v části [Export šablony Azure Resource Manageru ze stávajících prostředků](../azure-resource-manager/resource-manager-export-template.md).
- Prostředí Azure PowerShell: Tématu [šablon exportovat Azure Resource Manageru pomocí prostředí PowerShell](../azure-resource-manager/resource-manager-export-template-powershell.md).
- Azure CLI: Viz [šablony exportovat Azure Resource Manager pomocí rozhraní příkazového řádku Azure](../azure-resource-manager/resource-manager-export-template-cli.md).


## <a name="deploy-using-the-portal"></a>Nasazení pomocí portálu

Můžete nasadit pomocí webu Azure portal šablony Resource Manageru. Další informace najdete v tématu [nasadit prostředky z vlastní šablony](../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template).

## <a name="deploy-using-powershell"></a>Nasazení pomocí prostředí PowerShell

Můžete nasadit pomocí Azure PowerShell šablony Resource Manageru. Další informace najdete v tématu [nasazení prostředků pomocí šablony Resource Manageru a prostředí Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md) a [nasadit privátní šablony Resource Manageru pomocí tokenu SAS a prostředí Azure PowerShell](../azure-resource-manager/resource-manager-powershell-sas-token.md).

## <a name="deploy-using-cli"></a>Nasazení pomocí rozhraní příkazového řádku

Můžete nasadit šablony Resource Manageru pomocí rozhraní příkazového řádku Azure. Další informace najdete v tématu [nasazení prostředků pomocí šablony Resource Manageru a rozhraní příkazového řádku Azure](../azure-resource-manager/resource-group-template-deploy-cli.md) a [nasadit privátní šablony Resource Manageru pomocí tokenu SAS a rozhraní příkazového řádku Azure](../azure-resource-manager/resource-manager-cli-sas-token.md).

## <a name="deploy-using-the-rest-api"></a>Nasazení pomocí rozhraní REST API
Můžete nasadit šablony Resource Manageru pomocí rozhraní REST API. Další informace najdete v tématu [nasazení prostředků pomocí šablony Resource Manageru a REST API Resource Manageru](../azure-resource-manager/resource-group-template-deploy-rest.md).

## <a name="deploy-with-visual-studio"></a>Nasazení s využitím sady Visual Studio
 Pomocí sady Visual Studio vytvořte projekt skupiny prostředků a nasaďte ho do Azure v uživatelském rozhraní. Vyberete typ zdroje, které chcete zahrnout do projektu. Tyto prostředky se automaticky přidají do šablony Resource Manageru. Projekt také obsahuje skript prostředí PowerShell k nasazení šablony.

Úvod do skupiny prostředků pomocí sady Visual Studio, najdete v části [vytvoření a nasazení skupin prostředků Azure pomocí sady Visual Studio](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

## <a name="troubleshoot"></a>Řešení potíží

Pokud narazíte na problémy s vytvářením clusterů HDInsight, podívejte se na [požadavky na řízení přístupu](hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>Další postup
V tomto článku jste se naučili několik způsobů, jak vytvořit cluster služby HDInsight. Další informace naleznete v následujících článcích:

* Pro další HDInsight související šablony, viz [šablon Azure rychlý Start](https://azure.microsoft.com/resources/templates/?term=hdinsight).
* Příklad nasazení prostředků prostřednictvím klientské knihovny .NET, naleznete v části [nasadit prostředky pomocí knihovny .NET a šablonu](../virtual-machines/windows/csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Podrobný příklad nasazení aplikace naleznete v tématu [zřídit a nasadit mikroslužeb předvídatelné v Azure](../app-service/app-service-deploy-complex-application-predictably.md).
* Pokyny pro nasazení řešení do různých prostředí najdete v článku věnovaném [testovacím a vývojovým prostředím v Microsoft Azure](../solution-dev-test-environments.md).
* Další informace o části šablony Azure Resource Manageru najdete v tématu [vytváření šablon](../azure-resource-manager/resource-group-authoring-templates.md).
* Seznam funkcí v šablonu Azure Resource Manager můžete použít, najdete v části [funkce šablon](../azure-resource-manager/resource-group-template-functions.md).
