---
title: Vytváření clusterů Apache Hadoop pomocí šablon - Azure HDInsight
description: Zjistěte, jak vytvářet clustery pro HDInsight pomocí šablon Správce prostředků
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/18/2019
ms.openlocfilehash: 9498f2cf56f0bfe20d0806e5dc9872403dabb180
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75979097"
---
# <a name="create-apache-hadoop-clusters-in-hdinsight-by-using-resource-manager-templates"></a>Vytváření clusterů Apache Hadoop v HDInsightpomocí šablon Správce prostředků

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

V tomto článku se dozvíte několik způsobů, jak vytvořit clustery Azure HDInsight pomocí šablon Azure Resource Manager. Další informace najdete [v tématu Nasazení aplikace pomocí šablony Azure Resource Manager](../azure-resource-manager/templates/deploy-powershell.md). Další informace o dalších nástrojích a funkcích pro vytváření clusteru zobrazíte klepnutím na volič karet v horní části této stránky nebo v [tématu Metody vytváření clusteru](hdinsight-hadoop-provision-linux-clusters.md#cluster-setup-methods).

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="prerequisites"></a>Požadavky

* Předplatné [Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Azure PowerShell a/nebo Azure CLI.

### <a name="resource-manager-templates"></a>Šablony Resource Manageru

Šablona Správce prostředků usnadňuje vytvoření následujících prostředků pro vaši aplikaci v jedné koordinované operaci:
* HDInsight clustery a jejich závislé prostředky (například výchozí účet úložiště).
* Další prostředky (například Azure SQL Database pro použití [Apache Sqoop).](https://sqoop.apache.org/)

V šabloně definujete prostředky, které jsou potřebné pro aplikaci. Můžete také zadat parametry nasazení vstupníhodnoty pro různá prostředí. Šablona se skládá z JSON a výrazy, které používáte k vytvoření hodnoty pro vaše nasazení.

Ukázky šablon HDInsight najdete na [šablonách azure quickstart](https://azure.microsoft.com/resources/templates/?term=hdinsight). Pomocí kódu [Visual Studia](https://code.visualstudio.com/#alt-downloads) pro různé platformy s [příponou Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools) nebo textovým editorem uložte šablonu do souboru na pracovní stanici.

Další informace o šablonách Správce prostředků naleznete v následujících článcích a příkladech:

* [Autor šablony Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md)
* [Nasazení aplikace pomocí šablon Azure Resource Manageru](../azure-resource-manager/templates/deploy-powershell.md)
* Odkaz na šablonu [Microsoft.HDInsight/clusterů](/azure/templates/microsoft.hdinsight/allversions)
* [Šablony rychlých startů Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Hdinsight&pageNumber=1&sort=Popular)

## <a name="generate-templates"></a>Generovat šablony

Správce prostředků umožňuje exportovat šablonu Správce prostředků z existujících prostředků v předplatném pomocí různých nástrojů. Z vygenerované šablony pak zjistíte syntaxi šablony a podle potřeby pak můžete automatizovat opakované nasazení svého řešení. Další informace naleznete v [tématu Export šablon](../azure-resource-manager/templates/export-template-portal.md).

## <a name="deploy-using-the-portal"></a>Nasazení pomocí portálu

Šablonu Správce prostředků můžete nasadit pomocí portálu Azure. Další informace naleznete v [tématu Nasazení prostředků z vlastní šablony](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template).

## <a name="deploy-using-powershell"></a>Nasazení pomocí PowerShellu

Šablonu Správce prostředků můžete nasadit pomocí Azure PowerShellu. Další informace najdete [v tématu Nasazení prostředků pomocí šablon Správce prostředků a Azure PowerShellu](../azure-resource-manager/templates/deploy-powershell.md) a [Nasazení privátní šablony Správce prostředků s tokenem SAS a Azure PowerShellem.](../azure-resource-manager/resource-manager-powershell-sas-token.md)

## <a name="deploy-using-azure-cli"></a>Nasazení s využitím rozhraní příkazového řádku Azure

Šablonu Správce prostředků můžete nasadit pomocí azure cli. Další informace najdete [v tématu Nasazení prostředků pomocí šablon Správce prostředků a Azure CLI](../azure-resource-manager/templates/deploy-cli.md) a [nasazení privátní šablony Správce prostředků s tokenem SAS a Azure CLI](../azure-resource-manager/resource-manager-cli-sas-token.md).

## <a name="deploy-using-the-rest-api"></a>Nasazení pomocí rozhraní REST API

Šablonu Správce prostředků můžete nasadit pomocí rozhraní REST API. Další informace naleznete v [tématu Nasazení prostředků pomocí šablon Správce prostředků a rozhraní REST API správce prostředků](../azure-resource-manager/templates/deploy-rest.md).

## <a name="deploy-with-visual-studio"></a>Nasazení s využitím sady Visual Studio

 Pomocí Visual Studia vytvořte projekt skupiny prostředků a nasaďte ho do Azure prostřednictvím uživatelského rozhraní. Vyberte typ zdrojů, které chcete zahrnout do projektu. Tyto prostředky jsou automaticky přidány do šablony Správce prostředků. Projekt také poskytuje skript prostředí PowerShell pro nasazení šablony.

Úvod k používání Visual Studia se skupinami prostředků najdete v [tématu Vytváření a nasazování skupin prostředků Azure prostřednictvím sady Visual Studio](../azure-resource-manager/templates/create-visual-studio-deployment-project.md).

## <a name="troubleshoot"></a>Řešení potíží

Pokud narazíte na problémy s vytvářením clusterů HDInsight, podívejte se na [požadavky na řízení přístupu](hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="next-steps"></a>Další kroky

V tomto článku jste se naučili několik způsobů, jak vytvořit cluster HDInsight. Další informace naleznete v následujících článcích:

* Další šablony související s HDInsight najdete v [tématu Šablony rychlého startu Azure](https://azure.microsoft.com/resources/templates/?term=hdinsight).
* Příklad nasazení prostředků prostřednictvím knihovny klienta .NET naleznete v tématu [Nasazení prostředků pomocí knihoven .NET a šablony](../virtual-machines/windows/csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Podrobný příklad nasazení aplikace najdete v tématu [Zřizování a nasazování mikroslužeb předvídatelně v Azure](../app-service/deploy-complex-application-predictably.md).
* Pokyny pro nasazení řešení do různých prostředí najdete v článku věnovaném [testovacím a vývojovým prostředím v Microsoft Azure](../solution-dev-test-environments.md).
* Další informace o částech šablony Azure Resource Manager, najdete v [tématu Vytváření šablon](../azure-resource-manager/templates/template-syntax.md).
* Seznam funkcí, které můžete použít v šabloně Správce prostředků Azure, najdete v [tématu Šablony funkcí](../azure-resource-manager/templates/template-functions.md).
