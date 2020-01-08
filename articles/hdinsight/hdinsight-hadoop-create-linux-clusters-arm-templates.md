---
title: Vytváření clusterů Apache Hadoop pomocí šablon – Azure HDInsight
description: Naučte se vytvářet clustery pro HDInsight pomocí šablon Správce prostředků.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/18/2019
ms.openlocfilehash: c840cf452f047177c1244caedf09d976f0514961
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75435541"
---
# <a name="create-apache-hadoop-clusters-in-hdinsight-by-using-resource-manager-templates"></a>Vytváření clusterů Apache Hadoop ve službě HDInsight pomocí šablon Správce prostředků

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

V tomto článku se dozvíte několik způsobů, jak vytvářet clustery Azure HDInsight pomocí šablon Azure Resource Manager. Další informace najdete v tématu [nasazení aplikace pomocí šablony Azure Resource Manager](../azure-resource-manager/resource-group-template-deploy.md). Další informace o dalších nástrojích pro vytváření clusteru a funkcích získáte kliknutím na volič karty v horní části této stránky nebo v tématu [metody vytváření clusteru](hdinsight-hadoop-provision-linux-clusters.md#cluster-setup-methods).

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="prerequisites"></a>Požadavky

* [Předplatného Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Azure PowerShell a/nebo Azure CLI.

### <a name="resource-manager-templates"></a>Šablony Správce prostředků

Šablona Správce prostředků usnadňuje vytvoření následujících prostředků pro vaši aplikaci v rámci jediné koordinované operace:
* Clustery HDInsight a jejich závislé prostředky (například výchozí účet úložiště).
* Další prostředky (například Azure SQL Database pro použití [Apache Sqoop](https://sqoop.apache.org/)).

V šabloně definujete prostředky, které jsou nutné pro aplikaci. Parametry nasazení můžete zadat také pro vstupní hodnoty pro různá prostředí. Šablona se skládá z formátu JSON a výrazů, které slouží k vytvoření hodnot pro vaše nasazení.

Ukázky šablon HDInsight najdete v [šablonách rychlý Start pro Azure](https://azure.microsoft.com/resources/templates/?term=hdinsight). Použijte [Visual Studio Code](https://code.visualstudio.com/#alt-downloads) pro různé platformy s [rozšířením správce prostředků](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools) nebo textovým editorem a uložte šablonu do souboru na pracovní stanici.

Další informace o šablonách Správce prostředků najdete v následujících článcích a příkladech:

* [Vytváření šablon Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md)
* [Nasazení aplikace pomocí šablon Azure Resource Manager](../azure-resource-manager/resource-group-template-deploy.md)
* Referenční dokumentace šablony [Microsoft. HDInsight/clusterů](/azure/templates/microsoft.hdinsight/allversions)
* [Šablony pro rychlý Start Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Hdinsight&pageNumber=1&sort=Popular)

## <a name="generate-templates"></a>Generování šablon

Správce prostředků umožňuje exportovat šablonu Správce prostředků z existujících prostředků ve vašem předplatném pomocí různých nástrojů. Z vygenerované šablony pak zjistíte syntaxi šablony a podle potřeby pak můžete automatizovat opakované nasazení svého řešení. Další informace najdete v tématu [Export šablon](../azure-resource-manager/templates/export-template-portal.md).

## <a name="deploy-using-the-portal"></a>Nasazení pomocí portálu

Šablonu Správce prostředků můžete nasadit pomocí Azure Portal. Další informace najdete v tématu [nasazení prostředků z vlastní šablony](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template).

## <a name="deploy-using-powershell"></a>Nasazování pomocí PowerShellu

Šablonu Správce prostředků můžete nasadit pomocí Azure PowerShell. Další informace najdete v tématu [nasazení prostředků pomocí šablon Správce prostředků a Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md) a [nasazení privátní šablony Správce prostředků pomocí tokenu SAS a Azure PowerShell](../azure-resource-manager/resource-manager-powershell-sas-token.md).

## <a name="deploy-using-azure-cli"></a>Nasazení s využitím rozhraní příkazového řádku Azure

Pomocí Azure CLI můžete nasadit šablonu Správce prostředků. Další informace najdete v tématu [nasazení prostředků pomocí šablon Správce prostředků a Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md) a [nasazení privátních správce prostředků šablony s tokenem SAS a](../azure-resource-manager/resource-manager-cli-sas-token.md)rozhraním příkazového řádku Azure CLI.

## <a name="deploy-using-the-rest-api"></a>Nasazení pomocí REST API

Šablonu Správce prostředků můžete nasadit pomocí REST API. Další informace najdete v tématu [nasazení prostředků pomocí šablon Správce prostředků a Správce prostředků REST API](../azure-resource-manager/resource-group-template-deploy-rest.md).

## <a name="deploy-with-visual-studio"></a>Nasazení pomocí sady Visual Studio

 Pomocí sady Visual Studio vytvořte projekt skupiny prostředků a nasaďte ho do Azure prostřednictvím uživatelského rozhraní. Vyberte typ prostředků, které chcete zahrnout do projektu. Tyto prostředky jsou automaticky přidány do šablony Správce prostředků. Projekt také poskytuje skript prostředí PowerShell pro nasazení šablony.

Úvod k používání sady Visual Studio se skupinami prostředků najdete v tématu [Vytvoření a nasazení skupin prostředků Azure pomocí sady Visual Studio](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

## <a name="troubleshoot"></a>Řešení potíží

Pokud narazíte na problémy s vytvářením clusterů HDInsight, podívejte se na [požadavky na řízení přístupu](hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli o několika způsobech, jak vytvořit cluster HDInsight. Další informace najdete v následujících článcích:

* Další šablony související s HDInsight najdete v tématu [šablony rychlý Start pro Azure](https://azure.microsoft.com/resources/templates/?term=hdinsight).
* Příklad nasazení prostředků prostřednictvím klientské knihovny .NET najdete v tématu [nasazení prostředků pomocí knihoven .NET a šablony](../virtual-machines/windows/csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Podrobný příklad nasazení aplikace najdete v tématu věnovaném [zřizování a nasazování mikroslužeb v Azure](../app-service/deploy-complex-application-predictably.md).
* Pokyny pro nasazení řešení do různých prostředí najdete v článku věnovaném [testovacím a vývojovým prostředím v Microsoft Azure](../solution-dev-test-environments.md).
* Další informace o částech šablony Azure Resource Manager najdete v tématu [vytváření šablon](../azure-resource-manager/templates/template-syntax.md).
* Seznam funkcí, které můžete použít v šabloně Azure Resource Manager, najdete v tématu [funkce šablon](../azure-resource-manager/resource-group-template-functions.md).
