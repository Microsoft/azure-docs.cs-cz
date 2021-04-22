---
title: Rychlý Start k vytvoření Azure Migrate projektu pomocí šablony Azure Resource Manager
description: V tomto rychlém startu se dozvíte, jak vytvořit Azure Migrate projekt pomocí šablony Azure Resource Manager (šablona ARM).
ms.date: 04/23/2021
author: rahulg1190
manager: bsiva
ms.author: rahugup
ms.topic: quickstart
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: 5129a414c6b27e28d1821b72e7e0cfb744dc3452
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2021
ms.locfileid: "107893054"
---
# <a name="quickstart-create-an-azure-migrate-project-using-an-arm-template"></a>Rychlý Start: vytvoření projektu Azure Migrate pomocí šablony ARM

V tomto rychlém startu se dozvíte, jak nastavit Azure Migrate obnovení projektu pomocí šablony Azure Resource Manager (šablona ARM). Azure Migrate poskytuje centralizované centrum pro vyhodnocení a migraci na místní servery, infrastrukturu, aplikace a data Azure. Azure Migrate podporuje posuzování a migraci místních virtuálních počítačů VMware, virtuálních počítačů Hyper-V, fyzických serverů, dalších virtualizovaných virtuálních počítačů, databází, webových aplikací a virtuálních ploch.

Tato šablona vytvoří projekt Azure Migrate, který se bude dále používat pro vyhodnocení a migraci místních serverů, infrastruktury, aplikací a dat Azure.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Pokud vaše prostředí splňuje požadavky a jste obeznámeni s používáním šablon ARM, vyberte tlačítko **Nasazení do Azure**. Šablona se otevře v prostředí Azure Portal.

[![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-migrate-project-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Požadavky

Pokud nemáte aktivní předplatné Azure, můžete si před tím, než začnete, vytvořit [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je jednou z [šablon pro rychlý start Azure](https://azure.microsoft.com/en-us/resources/templates/101-migrate-project-create/).

:::code language="json" source="~/quickstart-templates/101-migrate-project-create/azuredeploy.json":::



## <a name="deploy-the-template"></a>Nasazení šablony

Aby bylo možné šablonu nasadit, je nutné zadat **předplatné**, **skupinu prostředků**, **název projektu** a **umístění** .

1. Pokud se chcete přihlásit k Azure a otevřít šablonu, vyberte Image **nasadit do Azure** .

   [![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-migrate-project-create%2Fazuredeploy.json)

2. Vyberte nebo zadejte následující hodnoty:

   :::image type="content" source="media/quickstart-create-migrate-project-template/create-migrate-project.png" alt-text="Šablona pro vytvoření projektu Azure Migrate.":::

   - **Předplatné**: Vyberte své předplatné Azure.
   - **Skupina prostředků**: Vyberte existující skupinu nebo vyberte **vytvořit novou** a přidejte skupinu.
   - **Umístění**: ve výchozím nastavení je umístění skupiny prostředků a po výběru skupiny prostředků nebude k dispozici.
   - **Název projektu migrace**: zadejte název trezoru.
   - **Umístění**: vyberte umístění, do kterého chcete nasadit Azure Migrate projekt a jeho prostředky. 

3. Kliknutím na tlačítko **Revize + vytvořit** spusťte nasazení. 
   
## <a name="validate-the-deployment"></a>Ověření nasazení

Chcete-li ověřit, zda byl projekt Azure Migrate vytvořen, použijte Azure Portal.


1. Přejděte na Azure Migrate hledáním **Azure Migrate** na panelu hledání na Azure Portal. 
2. V dlaždici Windows, Linux a SQL Server klikněte na tlačítko **vyhledat, vyhodnotit a migrovat** .
3. Vyberte **předplatné Azure** a **projekt** podle hodnot zadaných v nasazení. 


## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili Azure Migrate projekt. Pokud se chcete dozvědět víc o Azure Migrate a jeho schopnostech, přejděte k části Azure Migrate přehled.

> [!div class="nextstepaction"]
> [Přehled služby Azure Migrate](migrate-services-overview.md)
> 