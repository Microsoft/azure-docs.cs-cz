---
title: Vytvoření členu služby Azure blockchain – Azure Portal
description: Vytvoření členu služby Azure blockchain pro konsorcium blockchain pomocí Azure Portal.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 11/18/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: janders
manager: femila
ms.openlocfilehash: 9b08bf030d66c058ef1ec7c57f1fb2517046c522
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74285109"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-the-azure-portal"></a>Rychlý Start: Vytvoření člena blockchain služby Azure blockchain pomocí Azure Portal

V tomto rychlém startu nasadíte nového člena blockchain a konsorcia ve službě Azure blockchain pomocí Azure Portal.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-blockchain-member"></a>Vytvoření člena blockchainu

Vytvořte člena blockchain, který spouští protokol hlavní knihy kvora v nové nebo existující konsorcium.

1. Přihlásit se na [Azure Portal](https://portal.azure.com).
1. V levém horním rohu webu Azure Portal vyberte **Vytvořit prostředek**.
1. Vyberte **Blockchain** > **Azure blockchain Service (Preview)** .

    ![Vytvořit službu](./media/create-member/create-member.png)

    Nastavení | Popis
    --------|------------
    Předplatné | Vyberte předplatné Azure, které chcete použít pro vaši službu. Pokud máte více předplatných, zvolte předplatné, ve kterém se vám prostředek účtuje.
    Skupina prostředků | Vytvořte nový název skupiny prostředků nebo vyberte existující v rámci svého předplatného.
    Region (Oblast) | Vyberte oblast, ve které se má člen vytvořit. Všichni členové konsorcia musí být ve stejném umístění.
    Protocol (Protokol) | V současné době verze Preview služby Azure blockchain podporuje protokol kvora.
    Konsorcium | Pro nového konsorcia zadejte jedinečný název. Pokud se připojujete k konsorciu prostřednictvím pozvánky, vyberte konsorcium, ke které se připojujete.
    Název | Vyberte jedinečný název členu služby Azure blockchain. Název členu blockchain může obsahovat jenom malá písmena a číslice. Prvním znakem musí být písmeno. Hodnota musí být dlouhá 2 až 20 znaků.
    Heslo pro členský účet | Heslo pro členský účet slouží k šifrování privátního klíče pro účet Ethereem, který je vytvořen pro vašeho člena. Použijete členský účet a heslo členského účtu pro správu konsorcia.
    Popis | Popis konsorcia
    Ceny | Konfigurace uzlu a náklady na novou službu. Vyberte odkaz **změnit** pro výběr mezi úrovněmi **Standard** a **Basic** .
    Heslo uzlu | Heslo pro výchozí uzel transakce člena Při připojování k výchozímu koncovému bodu transakčního uzlu blockchain člena použijte heslo pro základní ověřování.

1. Vyberte **zkontrolovat + vytvořit** a ověřte nastavení. Vyberte **vytvořit** a zřiďte službu. Zřizování trvá přibližně 10 minut.
1. Na panelu nástrojů vyberte **oznámení** , abyste mohli monitorovat proces nasazení.
1. Po nasazení přejděte k blockchain členu.

Vyberte **Přehled**. můžete si zobrazit základní informace o vaší službě včetně adresy RootContract a členského účtu.

![Přehled členů blockchain](./media/create-member/overview.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Můžete použít člena, kterého jste vytvořili pro další rychlý Start nebo kurz. Pokud už je nepotřebujete, můžete prostředky odstranit tak, že odstraníte `myResourceGroup` skupinu prostředků, kterou jste vytvořili pro rychlý Start.

Odstranění skupiny prostředků:

1. V Azure Portal přejděte do **skupiny prostředků** v levém navigačním podokně a vyberte skupinu prostředků, kterou chcete odstranit.
2. Vyberte **Odstranit skupinu prostředků**. Potvrďte odstranění zadáním názvu skupiny prostředků a vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste nasadili člen služby Azure blockchain a nový konsorcium. Vyzkoušejte si další rychlý Start pro použití Azure blockchain Development Kit pro Ethereem pro připojení k konsorciu ve službě Azure blockchain.

> [!div class="nextstepaction"]
> [Použití Visual Studio Code k připojení k síti konsorcia Azure blockchain](connect-vscode.md)
