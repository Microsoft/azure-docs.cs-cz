---
title: Vytvoření členu služby Azure blockchain – Azure Portal
description: Vytvoření členu služby Azure blockchain pro konsorcium blockchain pomocí Azure Portal.
ms.date: 03/12/2020
ms.topic: quickstart
ms.reviewer: ravastra
ms.openlocfilehash: 3c468633a193d78fb1c017a756ee372c6feefb12
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79203657"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-the-azure-portal"></a>Rychlý Start: Vytvoření člena blockchain služby Azure blockchain pomocí Azure Portal

V tomto rychlém startu nasadíte nového člena blockchain a konsorcia ve službě Azure blockchain pomocí Azure Portal.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-blockchain-member"></a>Vytvoření členu blockchain

Člen služby Azure Blockchain je blockchain uzel v privátní síti konsorcia blockchain. Při zřizování člena můžete vytvořit nebo připojit se k síti konsorcia. Pro síť konsorcia potřebujete alespoň jednoho člena. Počet členů blockchain, které účastníci potřebují, závisí na vašem scénáři. Účastníci konsorcia mohou mít jednoho nebo více členů blockchain nebo mohou sdílet členy s ostatními účastníky. Další informace o konsorciích najdete v článku [konsorcium služby Azure blockchain](consortium.md).

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. V levém horním rohu webu Azure Portal vyberte **Vytvořit prostředek**.
1. Vyberte **Blockchain** > **Azure blockchain Service (Preview)** .

    ![Vytvořit službu](./media/create-member/create-member.png)

    Nastavení | Popis
    --------|------------
    Předplatné | Vyberte předplatné Azure, které chcete použít pro vaši službu. Pokud máte více předplatných, zvolte předplatné, ve kterém se vám prostředek účtuje.
    Skupina prostředků | Vytvořte nový název skupiny prostředků nebo vyberte existující v rámci svého předplatného.
    Oblast | Vyberte oblast, ve které se má člen vytvořit. Všichni členové konsorcia musí být ve stejném umístění.
    Protokol | V současné době verze Preview služby Azure blockchain podporuje protokol kvora.
    Konsorcium | Pro nového konsorcia zadejte jedinečný název. Pokud se připojujete k konsorciu prostřednictvím pozvánky, vyberte konsorcium, ke které se připojujete. Další informace o konsorciích najdete v článku [konsorcium služby Azure blockchain](consortium.md).
    Název | Vyberte jedinečný název členu služby Azure blockchain. Název členu blockchain může obsahovat jenom malá písmena a číslice. Prvním znakem musí být písmeno. Hodnota musí být dlouhá 2 až 20 znaků.
    Heslo pro členský účet | Heslo pro členský účet slouží k šifrování privátního klíče pro účet Ethereem, který je vytvořen pro vašeho člena. Použijete členský účet a heslo členského účtu pro správu konsorcia.
    Ceny | Konfigurace uzlu a náklady na novou službu. Vyberte odkaz **změnit** pro výběr mezi úrovněmi **Standard** a **Basic** . Úroveň *Basic* slouží k vývoji, testování a testování konceptů. Použijte úroveň *Standard* pro nasazení produkčních úrovní.
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

V tomto rychlém startu jste nasadili člen služby Azure blockchain a nový konsorcium. Vyzkoušejte si další rychlý Start pro použití Azure blockchain Development Kit pro Ethereem pro připojení k členu služby Azure blockchain.

> [!div class="nextstepaction"]
> [Použití Visual Studio Code k připojení ke službě Azure blockchain](connect-vscode.md)
