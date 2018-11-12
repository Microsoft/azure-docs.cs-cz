---
title: Vytvořte clustery systému Apache Hadoop s použitím šablony – Azure HDInsight
description: Zjistěte, jak vytvářet clustery pro HDInsight pomocí šablon Resource Manageru
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: hrasheed
ms.openlocfilehash: 4e5561c4846889f53723ed8df82d7c6268aee180
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51241612"
---
# <a name="create-apache-hadoop-clusters-in-hdinsight-by-using-resource-manager-templates"></a>Vytvořte clustery systému Apache Hadoop v HDInsight pomocí šablon Resource Manageru
[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

V tomto článku se dozvíte několik způsobů, jak vytvářet clustery Azure HDInsight pomocí šablon Azure Resource Manageru. Další informace najdete v tématu [nasazení aplikace pomocí šablony Azure Resource Manageru](../azure-resource-manager/resource-group-template-deploy.md). Další informace o dalších funkcích a nástrojů pro vytváření clusteru, klepněte na volič karty v horní části této stránky nebo naleznete v tématu [metody vytváření clusterů](hdinsight-hadoop-provision-linux-clusters.md#cluster-setup-methods).

## <a name="prerequisites"></a>Požadavky
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Postupujte podle pokynů v tomto článku, budete potřebovat:

* [Předplatného Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Prostředí Azure PowerShell a/nebo Azure CLI Classic.

### <a name="resource-manager-templates"></a>Šablony Resource Manageru
Šablony Resource Manageru umožňuje snadno vytvořit následující resoruces pro vaši aplikaci v rámci jediné koordinované operace:
* Clustery HDInsight a jejich závislé prostředky (například výchozí účet úložiště)
* Jiné prostředky (jako jsou Azure SQL Database a použití Apache Sqoop)

V šabloně definujte prostředky, které jsou potřeba pro aplikaci. Zadejte také parametry nasazení pro vstupní hodnoty pro různá prostředí. Šablona se skládá z JSON a z výrazů, které můžete použít k vytvoření hodnot pro vaše nasazení.

Můžete najít ukázkové šablony HDInsight v [šablony pro rychlý start Azure](https://azure.microsoft.com/resources/templates/?term=hdinsight). Používat různé platformy [Visual Studio Code](https://code.visualstudio.com/#alt-downloads) s [rozšíření Resource Manageru](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools) nebo textovém editoru a uložte šablonu do souboru na pracovní stanici. 

Další informace o šablonách Resource Manageru najdete v následujících článcích:

* [Tvorba šablon Azure Resource Manageru](../azure-resource-manager/resource-group-authoring-templates.md)
* [Nasazení aplikace pomocí šablon Azure Resource Manageru](../azure-resource-manager/resource-group-template-deploy.md)

## <a name="generate-templates"></a>Generování šablon

Resource Manager vám umožňuje exportovat šablonu Resource Manageru z existujících prostředků ve vašem předplatném pomocí různých nástrojů. Z vygenerované šablony pak zjistíte syntaxi šablony a podle potřeby pak můžete automatizovat opakované nasazení svého řešení.

- Azure portal: viz [Export šablony Azure Resource Manageru z existujících prostředků](../azure-resource-manager/resource-manager-export-template.md).
- Azure PowerShell: Viz [šablon exportovat Azure Resource Manageru pomocí Powershellu](../azure-resource-manager/resource-manager-export-template-powershell.md).
- Rozhraní příkazového řádku Azure Classic: Viz [šablon exportovat Azure Resource Manageru pomocí rozhraní příkazového řádku Azure Classic](../azure-resource-manager/resource-manager-export-template-cli.md).


## <a name="deploy-using-the-portal"></a>Nasazení pomocí portálu

Můžete nasadit šablonu Resource Manageru pomocí webu Azure portal. Další informace najdete v tématu [nasazení prostředků z vlastní šablony](../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template).

## <a name="deploy-using-powershell"></a>Nasazení pomocí Powershellu

Můžete nasadit šablonu Resource Manageru pomocí prostředí Azure PowerShell. Další informace najdete v tématu [nasazení prostředků pomocí šablon Resource Manageru a prostředí Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md) a [nasazení privátní šablony Resource Manageru s tokenem SAS a prostředí Azure PowerShell](../azure-resource-manager/resource-manager-powershell-sas-token.md).

## <a name="deploy-using-azure-cli"></a>Nasazení pomocí rozhraní příkazového řádku Azure

Můžete nasadit šablonu Resource Manageru pomocí rozhraní příkazového řádku classic. Další informace najdete v tématu [nasazení prostředků pomocí šablon Resource Manageru a Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md) a [nasazení privátní šablony Resource Manageru s tokenem SAS a rozhraní příkazového řádku Azure](../azure-resource-manager/resource-manager-cli-sas-token.md).

## <a name="deploy-using-the-rest-api"></a>Nasazení pomocí rozhraní REST API
Můžete nasadit šablonu Resource Manageru pomocí rozhraní REST API. Další informace najdete v tématu [nasazení prostředků pomocí šablon Resource Manageru a rozhraní REST API Resource Manageru](../azure-resource-manager/resource-group-template-deploy-rest.md).

## <a name="deploy-with-visual-studio"></a>Nasazení s využitím sady Visual Studio
 Pomocí sady Visual Studio vytvořte projekt skupiny prostředků a nasaďte ji do Azure prostřednictvím uživatelského rozhraní. Vyberete typ zdroje, které mají být zahrnuty do projektu. Tyto prostředky se automaticky přidají do šablony Resource Manageru. Projekt obsahuje také skript Powershellu k nasazení šablony.

Úvod do používání aplikace Visual Studio se skupinami prostředků najdete v tématu [vytvoření a nasazení skupin prostředků Azure pomocí sady Visual Studio](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

## <a name="troubleshoot"></a>Řešení potíží

Pokud narazíte na problémy s vytvářením clusterů HDInsight, podívejte se na [požadavky na řízení přístupu](hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>Další postup
V tomto článku jste se naučili několik způsobů, jak vytvořit HDInsight cluster. Další informace naleznete v následujících článcích:

* Pro další HDInsight naleznete v tématu související šablony [šablony pro rychlý start Azure](https://azure.microsoft.com/resources/templates/?term=hdinsight).
* Příklad nasazení prostředků pomocí klientské knihovny .NET najdete v tématu [nasazení prostředků pomocí knihovny .NET a šablonou](../virtual-machines/windows/csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Podrobný příklad nasazení aplikace, najdete v části [zřizování a nasazování mikroslužeb v Azure předvídatelně](../app-service/app-service-deploy-complex-application-predictably.md).
* Pokyny pro nasazení řešení do různých prostředí najdete v článku věnovaném [testovacím a vývojovým prostředím v Microsoft Azure](../solution-dev-test-environments.md).
* Další informace o části šablony Azure Resource Manageru najdete v tématu [vytváření šablon](../azure-resource-manager/resource-group-authoring-templates.md).
* Seznam funkce, které můžete použít v šabloně Azure Resource Manageru najdete v tématu [šablony funkce](../azure-resource-manager/resource-group-template-functions.md).
