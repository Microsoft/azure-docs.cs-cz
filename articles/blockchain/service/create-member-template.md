---
title: Vytvoření členu služby Azure blockchain pomocí šablony Azure Resource Manager
description: Naučte se vytvořit člena služby Azure blockchain pomocí šablony Azure Resource Manager.
services: azure-resource-manager
author: PatAltimore
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: patricka
ms.date: 06/04/2020
ms.openlocfilehash: 8eabb6806dee96871648ea419ba36d768d32a2ab
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/08/2020
ms.locfileid: "86078162"
---
# <a name="quickstart-create-an-azure-blockchain-service-member-using-an-arm-template"></a>Rychlý Start: Vytvoření členu služby Azure blockchain pomocí šablony ARM

V tomto rychlém startu nasadíte nového člena blockchain a konsorcia v Azure blockchain Service pomocí šablony Azure Resource Manager (šablona ARM). Člen služby Azure Blockchain je blockchain uzel v privátní síti konsorcia blockchain. Při zřizování člena můžete vytvořit nebo připojit se k síti konsorcia. Pro síť konsorcia potřebujete alespoň jednoho člena. Počet členů blockchain, které účastníci potřebují, závisí na vašem scénáři. Účastníci konsorcia mohou mít jednoho nebo více členů blockchain nebo mohou sdílet členy s ostatními účastníky. Další informace o konsorciích najdete v článku [konsorcium služby Azure blockchain](consortium.md).

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Pokud vaše prostředí splňuje požadavky a Vy jste obeznámeni s používáním šablon ARM, vyberte tlačítko **nasadit do Azure** . Šablona se otevře v Azure Portal.

[![Nasazení do Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-blockchain-asaservice%2Fazuredeploy.json)

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný](https://azure.microsoft.com/free/) účet před tím, než začnete.

## <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je ze [šablon Azure pro rychlý Start](https://azure.microsoft.com/resources/templates/201-blockchain-asaservice/).

:::code language="json" source="~/quickstart-templates/201-blockchain-asaservice/azuredeploy.json" range="1-84" highlight="52-80":::

Prostředky Azure definované v šabloně:

* [**Microsoft. blockchain/blockchainMembers**](/azure/templates/microsoft.blockchain/blockchainmembers)

## <a name="deploy-the-template"></a>Nasazení šablony

1. Vyberte následující odkaz pro přihlášení do Azure a otevřete šablonu.

    [![Nasazení do Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-blockchain-asaservice%2Fazuredeploy.json)

1. Zadejte nastavení pro člena služby Azure blockchain.

    Nastavení | Description
    --------|------------
    Předplatné | Vyberte předplatné Azure, které chcete použít pro vaši službu. Pokud máte více předplatných, zvolte předplatné, ve kterém se vám prostředek účtuje.
    Skupina prostředků | Vytvořte nový název skupiny prostředků nebo vyberte existující v rámci svého předplatného.
    Umístění | Vyberte umístění pro vytvoření člena. Všichni členové konsorcia musí být ve stejném umístění. Dostupná umístění pro nasazení jsou *westeurope, eastus, southeastasia, westeurope, northeurope, westus2*a *japaneast*.
    Název členu | Vyberte jedinečný název členu služby Azure blockchain. Název členu blockchain může obsahovat jenom malá písmena a číslice. Prvním znakem musí být písmeno. Hodnota musí být dlouhá 2 až 20 znaků.
    Název konsorcia | Zadejte jedinečný název. Další informace o konsorciích najdete v článku [konsorcium služby Azure blockchain](consortium.md).
    Heslo člena | Heslo pro členský účet slouží k šifrování privátního klíče pro účet Ethereem, který je vytvořen pro vašeho člena. Použijete členský účet a heslo členského účtu pro správu konsorcia.
    Úroveň SKU | Cenová úroveň nové služby. Vyberte si úrovně **Standard** a **Basic** . Využijte *základní* vrstvu pro vývoj, testování a kontrolu konceptů. Použijte úroveň *Standard* pro nasazení produkčních úrovní. Úroveň *Standard* byste měli použít také v případě, že používáte blockchain data Manager nebo odesíláte velký objem privátních transakcí. Změna cenové úrovně mezi základními a standardními po vytvoření členů není podporována.
    Název SKU | Konfigurace uzlu a náklady na novou službu.

1. Vyberte **koupit** a šablonu nasaďte.

  Azure Portal se tady používá k nasazení šablony. Můžete také použít Azure PowerShell, Azure CLI a REST API. Další informace o dalších metodách nasazení najdete v tématu [Nasazení šablon](../../azure-resource-manager/templates/deploy-powershell.md).

## <a name="review-deployed-resources"></a>Kontrola nasazených prostředků

K zobrazení podrobností o nasazeném členu služby Azure blockchain můžete použít Azure Portal. Na portálu přejdete do skupiny prostředků, která obsahuje vašeho člena služby Azure blockchain. Vyberte člen blockchain, který jste vytvořili.

![Podrobnosti o přehledu členů služby Azure blockchain v Azure Portal](./media/create-member-template/deployed-member.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Můžete použít člen blockchain, který jste vytvořili pro další rychlý Start nebo kurz. Pokud už je nepotřebujete, můžete prostředky odstranit odstraněním skupiny prostředků, kterou jste vytvořili pro rychlý Start.

Odstranění skupiny prostředků:

1. V Azure Portal přejděte do **skupiny prostředků** v levém navigačním podokně a vyberte skupinu prostředků, kterou chcete odstranit.
2. Vyberte **Odstranit skupinu prostředků**. Potvrďte odstranění zadáním názvu skupiny prostředků a vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste nasadili člen služby Azure blockchain a nový konsorcium. Vyzkoušejte si další rychlý Start pro použití Azure blockchain Development Kit pro Ethereem pro připojení k členu služby Azure blockchain.

> [!div class="nextstepaction"]
> [Použití Visual Studio Code k připojení ke službě Azure blockchain](connect-vscode.md)