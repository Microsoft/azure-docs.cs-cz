---
title: Vytvoření člena služby Azure Blockchain – portál Azure
description: Vytvořte člena služby Azure Blockchain Service pro konsorcium blockchainu pomocí portálu Azure.
ms.date: 03/12/2020
ms.topic: quickstart
ms.reviewer: ravastra
ms.openlocfilehash: 3c468633a193d78fb1c017a756ee372c6feefb12
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79203657"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-the-azure-portal"></a>Úvodní příručka: Vytvoření člena blockchainové služby Azure Blockchain pomocí portálu Azure

V tomto rychlém startu nasadíte nového člena blockchainu a konsorcia ve službě Azure Blockchain pomocí portálu Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-blockchain-member"></a>Vytvoření člena blockchainu

Člen služby Azure Blockchain Je uzel blockchainu v privátní blockchainové síti konsorcia. Při zřizování člena můžete vytvořit nebo se připojit k síti konsorcia. Potřebujete alespoň jednoho člena pro síť konsorcia. Počet členů blockchainu, které účastníci potřebují, závisí na vašem scénáři. Účastníci konsorcia mohou mít jednoho nebo více členů blockchainu nebo mohou členy sdílet s ostatními účastníky. Další informace o konsorciích najdete v [tématu Konsorcium služby Azure Blockchain Service](consortium.md).

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. V levém horním rohu webu Azure Portal vyberte **Vytvořit prostředek**.
1. Vyberte **blockchainovou** > **blockchainovou službu Blockchain (preview).**

    ![Vytvořit službu](./media/create-member/create-member.png)

    Nastavení | Popis
    --------|------------
    Předplatné | Vyberte předplatné Azure, které chcete použít pro vaši službu. Pokud máte více předplatných, zvolte předplatné, ve kterém se vám prostředek účtuje.
    Skupina prostředků | Vytvořte nový název skupiny prostředků nebo zvolte existující název z předplatného.
    Region (Oblast) | Zvolte oblast, kterou chcete vytvořit člen. Všichni členové konsorcia musí být ve stejném umístění.
    Protocol (Protokol) | V současné době Azure Blockchain Service Preview podporuje protokol Kvora.
    Konsorcium | Pro nové konsorcium zadejte jedinečný název. Pokud se připojíte ke konsorciu prostřednictvím pozvánky, zvolte konsorcium, ke sdružení, ke sdružení, ke sdružení, ke sdružení. Další informace o konsorciích najdete v [tématu Konsorcium služby Azure Blockchain Service](consortium.md).
    Name (Název) | Zvolte jedinečný název pro člena služby Azure Blockchain Service. Název člena blockchainu může obsahovat pouze malá písmena a čísla. Prvním znakem musí být písmeno. Hodnota musí být dlouhá 2 až 20 znaků.
    Heslo členského účtu | Heslo členského účtu se používá k šifrování soukromého klíče pro účet Ethereum, který je vytvořen pro vašeho člena. Pro správu konsorcia se používá heslo členského účtu a členského účtu.
    Ceny | Konfigurace uzlu a náklady na novou službu. Vyberte odkaz **Změnit,** chcete-li zvolit mezi **standardní** a **základní** úrovní. Úroveň *Basic* je určená pro vývoj, testování a testování konceptů. Použijte *úroveň Standard* pro nasazení výrobní třídy.
    Heslo uzlu | Heslo pro výchozí uzel transakce člena. Heslo použijte pro základní ověřování při připojování k výchozímu koncovému bodu koncového uzlu výchozího transakčního uzlu člena blockchainu.

1. Chcete-li ověřit nastavení, vyberte **Zkontrolovat + vytvořit.** Chcete-li službu zřídit, vyberte **možnost Vytvořit.** Zřizování trvá přibližně 10 minut.
1. Chcete-li sledovat proces nasazení, vyberte na panelu nástrojů možnost **Oznámení.**
1. Po nasazení přejděte na člena blockchainu.

Vyberte **Přehled**, můžete zobrazit základní informace o službě, včetně adresy RootContract a členského účtu.

![Přehled členů blockchainu](./media/create-member/overview.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Člen, který jste vytvořili, můžete použít pro další rychlý start nebo kurz. Pokud již není potřeba, můžete odstranit prostředky `myResourceGroup` odstraněním skupiny prostředků, které jste vytvořili pro rychlý start.

Odstranění skupiny prostředků:

1. Na webu Azure Portal přejděte do **skupiny prostředků** v levém navigačním podokně a vyberte skupinu prostředků, kterou chcete odstranit.
2. Vyberte **Odstranit skupinu prostředků**. Ověřte odstranění zadáním názvu skupiny prostředků a vyberte **odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste nasadili člena služby Azure Blockchain Service a nové konsorcium. Zkuste další rychlý start použít Azure Blockchain Development Kit pro ethereum připojit k členu služby Azure Blockchain Service.

> [!div class="nextstepaction"]
> [Připojení ke službě Azure Blockchain Service pomocí kódu Visual Studia](connect-vscode.md)
