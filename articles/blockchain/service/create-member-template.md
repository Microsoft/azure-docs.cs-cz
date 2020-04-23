---
title: Vytvoření člena služby Azure Blockchain Service pomocí šablony Azure Resource Manager
description: Zjistěte, jak vytvořit člena služby Azure Blockchain service pomocí šablony Azure Resource Manager.
services: azure-resource-manager
author: PatAltimore
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: patricka
ms.date: 04/22/2020
ms.openlocfilehash: 8fd5a1775a6a217ce9dd8c80439dd2c54f9cd727
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2020
ms.locfileid: "82087594"
---
# <a name="create-an-azure-blockchain-service-member-using-an-azure-resource-manager-template"></a>Vytvoření člena služby Azure Blockchain Service pomocí šablony Azure Resource Manager

V tomto rychlém startu nasadíte nového člena blockchainu a konsorcia ve službě Azure Blockchain pomocí šablony Azure Resource Manager.

Člen služby Azure Blockchain Je uzel blockchainu v privátní blockchainové síti konsorcia. Při zřizování člena můžete vytvořit nebo se připojit k síti konsorcia. Potřebujete alespoň jednoho člena pro síť konsorcia. Počet členů blockchainu, které účastníci potřebují, závisí na vašem scénáři. Účastníci konsorcia mohou mít jednoho nebo více členů blockchainu nebo mohou členy sdílet s ostatními účastníky. Další informace o konsorciích najdete v [tématu Konsorcium služby Azure Blockchain Service](consortium.md).

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Pokud nemáte předplatné Azure, vytvořte si [bezplatný](https://azure.microsoft.com/free/) účet, než začnete.

## <a name="prerequisites"></a>Požadované součásti

Žádné.

## <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je ze [šablon Azure QuickStart](https://github.com/Azure/azure-quickstart-templates/blob/master/201-blockchain-asaservice/).

[!code-json[<Azure Resource Manager template create blockchain member>](~/quickstart-templates/201-blockchain-asaservice/azuredeploy.json)]

Prostředky Azure definované v šabloně:

* [**Microsoft.Blockchain/blockchainČlenové**](https://docs.microsoft.com/azure/templates/microsoft.blockchain/blockchainmembers)

## <a name="deploy-the-template"></a>Nasazení šablony

1. Vyberte následující odkaz pro přihlášení do Azure a otevřete šablonu.

    [![Nasazení do Azure](./media/create-member-template/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-blockchain-asaservice%2Fazuredeploy.json)

1. Zadejte nastavení pro člena služby Azure Blockchain Service.

    Nastavení | Popis
    --------|------------
    Předplatné | Vyberte předplatné Azure, které chcete použít pro vaši službu. Pokud máte více předplatných, zvolte předplatné, ve kterém se vám prostředek účtuje.
    Skupina prostředků | Vytvořte nový název skupiny prostředků nebo zvolte existující název z předplatného.
    Umístění | Zvolte umístění pro vytvoření člena. Všichni členové konsorcia musí být ve stejném umístění. K dispozici jsou místa pro nasazení *jsou westeurope, eastus, southeastasia, westeurope, northeurope, westus2*a *japaneast*.
    Název členu | Zvolte jedinečný název pro člena služby Azure Blockchain Service. Název člena blockchainu může obsahovat pouze malá písmena a čísla. Prvním znakem musí být písmeno. Hodnota musí být dlouhá 2 až 20 znaků.
    Název konsorcia | Zadejte jedinečný název. Další informace o konsorciích najdete v [tématu Konsorcium služby Azure Blockchain Service](consortium.md).
    Členské heslo | Heslo členského účtu se používá k šifrování soukromého klíče pro účet Ethereum, který je vytvořen pro vašeho člena. Pro správu konsorcia se používá heslo členského účtu a členského účtu.
    Úroveň Sku | Cenová úroveň pro vaši novou službu. Vyberte si mezi **úrovněmi Standard** a **Basic.** Vrstvu *Basic* použijte pro vývoj, testování a testování konceptů. Použijte *úroveň Standard* pro nasazení výrobní třídy. Úroveň *Standard* byste měli použít také v případě, že používáte Správce dat Blockchain nebo odesíláte velký objem soukromých transakcí. Změna cenové úrovně mezi základní a standardní po vytvoření člena není podporována.
    Název sku | Konfigurace uzlu a náklady na novou službu.

1. Vyberte **Koupit,** chcete-li šablonu nasadit.

  Portál Azure se tady používá k nasazení šablony. Můžete taky použít Azure PowerShell, Azure CLI a REST API. Další metody nasazení najdete v [tématu Nasazení šablon](../../azure-resource-manager/templates/deploy-powershell.md).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Můžete použít člena blockchainu, který jste vytvořili, pro další rychlý start nebo kurz. Pokud již není potřeba, můžete odstranit prostředky odstraněním skupiny prostředků, které jste vytvořili pro rychlý start.

Odstranění skupiny prostředků:

1. Na webu Azure Portal přejděte do **skupiny prostředků** v levém navigačním podokně a vyberte skupinu prostředků, kterou chcete odstranit.
2. Vyberte **Odstranit skupinu prostředků**. Ověřte odstranění zadáním názvu skupiny prostředků a vyberte **odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste nasadili člena služby Azure Blockchain Service a nové konsorcium. Zkuste další rychlý start použít Azure Blockchain Development Kit pro ethereum připojit k členu služby Azure Blockchain Service.

> [!div class="nextstepaction"]
> [Připojení ke službě Azure Blockchain Service pomocí kódu Visual Studia](connect-vscode.md)