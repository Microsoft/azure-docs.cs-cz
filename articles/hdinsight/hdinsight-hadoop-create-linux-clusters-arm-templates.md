---
title: Vytváření clusterů Apache Hadoop pomocí šablon – Azure HDInsight
description: Naučte se vytvářet clustery pro HDInsight pomocí šablon Správce prostředků.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 04/07/2020
ms.openlocfilehash: a14a7276d51b7bc1f490bc9ab49f72aedfb829f7
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2020
ms.locfileid: "92541838"
---
# <a name="create-apache-hadoop-clusters-in-hdinsight-by-using-resource-manager-templates"></a>Vytváření clusterů Apache Hadoop ve službě HDInsight pomocí šablon Správce prostředků

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

V tomto článku se dozvíte několik způsobů, jak vytvářet clustery Azure HDInsight pomocí [šablon Azure Resource Manager](../azure-resource-manager/templates/deploy-powershell.md). Další informace o dalších nástrojích pro vytváření clusteru a funkcích získáte kliknutím na volič karty v horní části této stránky. Viz také [metody vytváření clusterů](hdinsight-hadoop-provision-linux-clusters.md#cluster-setup-methods).

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="resource-manager-templates"></a>Šablony Resource Manageru

Šablona Správce prostředků usnadňuje vytvoření následujících prostředků pro vaši aplikaci v rámci jediné koordinované operace:

* Clustery HDInsight a jejich závislé prostředky (například výchozí účet úložiště).
* Další prostředky (například Azure SQL Database pro použití [Apache Sqoop](https://sqoop.apache.org/)).

V šabloně definujete prostředky, které jsou nutné pro aplikaci. Parametry nasazení můžete zadat také pro vstupní hodnoty pro různá prostředí. Šablona se skládá z formátu JSON a výrazů, které slouží k vytvoření hodnot pro vaše nasazení.

Ukázky šablon HDInsight najdete v [šablonách rychlý Start pro Azure](https://azure.microsoft.com/resources/templates/?term=hdinsight). Použijte [Visual Studio Code](https://code.visualstudio.com/#alt-downloads) pro různé platformy s [rozšířením správce prostředků](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools) nebo textovým editorem a uložte šablonu do souboru na pracovní stanici.

Další informace o šablonách Správce prostředků najdete v následujících článcích a příkladech:

* [Vytváření šablon Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md)
* [Nasazení aplikace pomocí šablon Azure Resource Manager](../azure-resource-manager/templates/deploy-powershell.md)
* Referenční dokumentace šablony [Microsoft. HDInsight/clusterů](/azure/templates/microsoft.hdinsight/allversions)
* [Šablony pro rychlý Start Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Hdinsight&pageNumber=1&sort=Popular)

## <a name="generate-templates"></a>Generování šablon

Správce prostředků umožňuje exportovat šablonu Správce prostředků z existujících prostředků ve vašem předplatném pomocí různých nástrojů. Z vygenerované šablony pak zjistíte syntaxi šablony a podle potřeby pak můžete automatizovat opakované nasazení svého řešení. Další informace najdete v tématu [Export šablon](../azure-resource-manager/templates/export-template-portal.md).

## <a name="deploy-using-the-portal"></a>Nasazení pomocí portálu

Šablonu Správce prostředků můžete nasadit pomocí Azure Portal. Další informace najdete v tématu [nasazení prostředků z vlastní šablony](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template).

## <a name="deploy-using-powershell"></a>Nasazení pomocí PowerShellu

Šablonu Správce prostředků můžete nasadit pomocí Azure PowerShell. Další informace najdete v tématu [nasazení prostředků pomocí šablon Správce prostředků a Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) a [nasazení privátní šablony Správce prostředků pomocí tokenu SAS a Azure PowerShell](../azure-resource-manager/templates/secure-template-with-sas-token.md).

## <a name="deploy-using-azure-cli"></a>Nasazení s využitím rozhraní příkazového řádku Azure

Pomocí Azure CLI můžete nasadit šablonu Správce prostředků. Další informace najdete v tématu [nasazení prostředků pomocí šablon Správce prostředků a Azure CLI](../azure-resource-manager/templates/deploy-cli.md) a [nasazení privátních správce prostředků šablony s tokenem SAS a](../azure-resource-manager/templates/secure-template-with-sas-token.md)rozhraním příkazového řádku Azure CLI.

## <a name="deploy-using-the-rest-api"></a>Nasazení pomocí REST API

Šablonu Správce prostředků můžete nasadit pomocí REST API. Další informace najdete v tématu [nasazení prostředků pomocí šablon Správce prostředků a Správce prostředků REST API](../azure-resource-manager/templates/deploy-rest.md).

## <a name="deploy-with-visual-studio"></a>Nasazení s využitím sady Visual Studio

 Pomocí sady Visual Studio vytvořte projekt skupiny prostředků a nasaďte ho do Azure prostřednictvím uživatelského rozhraní. Vyberte typ prostředků, které chcete zahrnout do projektu. Tyto prostředky jsou automaticky přidány do šablony Správce prostředků. Projekt také poskytuje skript prostředí PowerShell pro nasazení šablony.

Úvod k používání sady Visual Studio se skupinami prostředků najdete v tématu [Vytvoření a nasazení skupin prostředků Azure pomocí sady Visual Studio](../azure-resource-manager/templates/create-visual-studio-deployment-project.md).

## <a name="troubleshoot"></a>Řešení potíží

Pokud narazíte na problémy s vytvářením clusterů HDInsight, podívejte se na [požadavky na řízení přístupu](hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli o několika způsobech, jak vytvořit cluster HDInsight. Další informace najdete v těchto článcích:

* Další šablony související s HDInsight najdete v tématu [šablony rychlý Start pro Azure](https://azure.microsoft.com/resources/templates/?term=hdinsight).
* Příklad nasazení prostředků prostřednictvím klientské knihovny .NET najdete v tématu [nasazení prostředků pomocí knihoven .NET a šablony](../virtual-machines/windows/csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Podrobný příklad nasazení aplikace najdete v tématu věnovaném [zřizování a nasazování mikroslužeb v Azure](../app-service/deploy-complex-application-predictably.md).
* Pokyny pro nasazení řešení do různých prostředí najdete v článku věnovaném [testovacím a vývojovým prostředím v Microsoft Azure](../devtest-labs/devtest-lab-overview.md).
* Další informace o částech šablony Azure Resource Manager najdete v tématu [vytváření šablon](../azure-resource-manager/templates/template-syntax.md).
* Seznam funkcí, které můžete použít v šabloně Azure Resource Manager, najdete v tématu [funkce šablon](../azure-resource-manager/templates/template-functions.md).