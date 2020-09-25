---
title: Vytvoření členu služby Azure blockchain – Azure Portal
description: Vytvoření členu služby Azure blockchain pro konsorcium blockchain pomocí Azure Portal.
ms.date: 07/16/2020
ms.topic: quickstart
ms.reviewer: ravastra
ms.custom: references_regions
ms.openlocfilehash: 8a7f5aaea56f34e8107664ab786a14b59cd1cb7d
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91292723"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-the-azure-portal"></a>Rychlý Start: Vytvoření člena blockchain služby Azure blockchain pomocí Azure Portal

V tomto rychlém startu nasadíte nového člena blockchain a konsorcia ve službě Azure blockchain pomocí Azure Portal.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Žádné

## <a name="create-a-blockchain-member"></a>Vytvoření členu blockchain

Člen služby Azure Blockchain je blockchain uzel v privátní síti konsorcia blockchain. Při zřizování člena můžete vytvořit nebo připojit se k síti konsorcia. Pro síť konsorcia potřebujete alespoň jednoho člena. Počet členů blockchain, které účastníci potřebují, závisí na vašem scénáři. Účastníci konsorcia mohou mít jednoho nebo více členů blockchain nebo mohou sdílet členy s ostatními účastníky. Další informace o konsorciích najdete v článku [konsorcium služby Azure blockchain](consortium.md).

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. V levém horním rohu webu Azure Portal vyberte **Vytvořit prostředek**.
1. Vyberte **blockchain**  >  **Azure blockchain Service (Preview)**.

    ![Vytvořit službu](./media/create-member/create-member.png)

    Nastavení | Popis
    --------|------------
    Předplatné | Vyberte předplatné Azure, které chcete použít pro vaši službu. Pokud máte více předplatných, zvolte předplatné, ve kterém se vám prostředek účtuje.
    Skupina prostředků | Vytvořte nový název skupiny prostředků nebo vyberte existující v rámci svého předplatného.
    Region | Vyberte oblast, ve které se má člen vytvořit. Všichni členové konsorcia musí být ve stejném umístění. Funkce nemusí být k dispozici v některých oblastech. Azure blockchain Data Manager je k dispozici v následujících oblastech Azure: Východní USA a Západní Evropa.
    Protokol | V současné době verze Preview služby Azure blockchain podporuje protokol kvora.
    Konsorcium | Pro nového konsorcia zadejte jedinečný název. Pokud se připojujete k konsorciu prostřednictvím pozvánky, vyberte konsorcium, ke které se připojujete. Další informace o konsorciích najdete v článku [konsorcium služby Azure blockchain](consortium.md).
    Name | Vyberte jedinečný název členu služby Azure blockchain. Název členu blockchain může obsahovat jenom malá písmena a číslice. Prvním znakem musí být písmeno. Hodnota musí být dlouhá 2 až 20 znaků.
    Heslo pro členský účet | Heslo pro členský účet slouží k šifrování privátního klíče pro účet Ethereem, který je vytvořen pro vašeho člena. Použijete členský účet a heslo členského účtu pro správu konsorcia.
    Ceny | Konfigurace uzlu a náklady na novou službu. Vyberte odkaz **změnit** pro výběr mezi úrovněmi **Standard** a **Basic** . Využijte *základní* vrstvu pro vývoj, testování a kontrolu konceptů. Použijte úroveň *Standard* pro nasazení produkčních úrovní. Úroveň *Standard* použijte také v případě, že používáte blockchain data Manager nebo odesíláte velký objem privátních transakcí. Změna cenové úrovně mezi základními a standardními po vytvoření členů není podporována.
    Heslo uzlu | Heslo pro výchozí uzel transakce člena Při připojování k výchozímu koncovému bodu transakčního uzlu blockchain člena použijte heslo pro základní ověřování.

1. Vyberte **zkontrolovat + vytvořit** a ověřte nastavení. Vyberte **vytvořit** a zřiďte službu. Zřizování trvá přibližně 10 minut.
1. Na panelu nástrojů vyberte **oznámení** , abyste mohli monitorovat proces nasazení.
1. Po nasazení přejděte k blockchain členu.

Vyberte **Přehled**. můžete si zobrazit základní informace o vaší službě včetně adresy RootContract a členského účtu.

![Přehled členů blockchain](./media/create-member/overview.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Můžete použít člena, kterého jste vytvořili pro další rychlý Start nebo kurz. Pokud už je nepotřebujete, můžete prostředky odstranit odstraněním `myResourceGroup` skupiny prostředků, kterou jste vytvořili pro rychlý Start.

Odstranění skupiny prostředků:

1. V Azure Portal přejděte do **skupiny prostředků** v levém navigačním podokně a vyberte skupinu prostředků, kterou chcete odstranit.
2. Vyberte **Odstranit skupinu prostředků**. Potvrďte odstranění zadáním názvu skupiny prostředků a vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste nasadili člen služby Azure blockchain a nový konsorcium. Vyzkoušejte si další rychlý Start pro použití Azure blockchain Development Kit pro Ethereem pro připojení k členu služby Azure blockchain.

> [!div class="nextstepaction"]
> [Použití Visual Studio Code k připojení ke službě Azure blockchain](connect-vscode.md)
