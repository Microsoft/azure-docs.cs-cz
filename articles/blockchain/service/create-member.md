---
title: Vytvoření členu blockchain služby Azure blockchain pomocí Azure Portal
description: Pomocí služby Azure blockchain vytvořte člen blockchain.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 08/29/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: janders
manager: femila
ms.openlocfilehash: 9f58379acd88fd93a00edc11b73b41dc3d6226a5
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2019
ms.locfileid: "70241044"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-the-azure-portal"></a>Rychlý start: Vytvoření členu blockchain služby Azure blockchain pomocí Azure Portal

Služba Azure Blockchain je blockchain platforma, kterou můžete v rámci inteligentní smlouvy spustit svou obchodní logiku. V tomto rychlém startu se dozvíte, jak začít vytvořením členu blockchain pomocí Azure Portal.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-blockchain-member"></a>Vytvoření člena blockchainu

Vytvořte člena blockchain, který spouští protokol hlavní knihy kvora v nové nebo existující konsorcium.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. V levém horním rohu webu Azure Portal vyberte **Vytvořit prostředek**.
1. Vyberte **blockchain** > **službu Azure blockchain**.
1. Dokončete šablonu.

    ![Vytvořit službu](./media/create-member/create-member.png)

    Nastavení | Popis
    --------|------------
    Člen blockchain | Vyberte jedinečný název, který identifikuje vašeho člena blockchain služby Azure blockchain. Název členu blockchain může obsahovat jenom malá písmena a číslice. Prvním znakem musí být písmeno. Hodnota musí být dlouhá 2 až 20 znaků.
    Subscription | Vyberte předplatné Azure, které chcete použít pro vaši službu. Pokud máte více předplatných, zvolte předplatné, ve kterém se vám prostředek účtuje.
    Resource group | Název nové skupiny prostředků nebo některé ze stávajících ve vašem předplatném.
    Oblast | Umístění musí být stejné pro všechny členy konsorcia.
    Heslo pro členský účet | Heslo pro členský účet slouží k šifrování privátního klíče pro účet Ethereem, který je vytvořen pro vašeho člena. Použijete členský účet a heslo členského účtu pro správu konsorcia.
    Název konsorcia | Pro nového konsorcia zadejte jedinečný název. Pokud se připojujete k konsorciu prostřednictvím pozvánky, je tato hodnota konsorciem, ke kterému se připojujete.
    Popis | Popis konsorcia
    Protocol |  Verze Preview podporuje protokol kvora.
    Ceny | Konfigurace uzlu pro novou službu. Vyberte **Standard**. 2 uzly validátoru a 1 uzel transakce jsou výchozí nastavení.
    Heslo uzlu transakce | Heslo pro výchozí uzel transakce člena Při připojování k výchozímu koncovému bodu transakčního uzlu blockchain člena použijte heslo pro základní ověřování.

1. Vyberte **vytvořit** a zřiďte službu. Zřizování trvá přibližně 10 minut.
1. Na panelu nástrojů vyberte **oznámení** , abyste mohli monitorovat proces nasazení.
1. Po nasazení přejděte k blockchain členu.

Vyberte **Přehled**. můžete si zobrazit základní informace o vaší službě včetně adresy RootContract a členského účtu.

![Přehled členů blockchain](./media/create-member/overview.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Můžete použít člena, kterého jste vytvořili pro další rychlý Start nebo kurz. Pokud už je nepotřebujete, můžete prostředky odstranit odstraněním `myResourceGroup` skupiny prostředků, kterou jste vytvořili ve službě Azure blockchain.

Odstranění skupiny prostředků:

1. V Azure Portal přejděte do **skupiny prostředků** v levém navigačním podokně a vyberte skupinu prostředků, kterou chcete odstranit.
2. Vyberte **Odstranit skupinu prostředků**. Potvrďte odstranění zadáním názvu skupiny prostředků a vyberte **Odstranit**.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Připojení a nasazení inteligentního kontraktu pomocí MetaMask](connect-metamask.md)