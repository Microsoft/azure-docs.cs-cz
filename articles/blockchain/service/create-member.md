---
title: Vytvoření služby Azure Blockchain pomocí webu Azure portal
description: Pomocí služby Azure Blockchain vytvořit člena consortium.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: janders
manager: femila
ms.openlocfilehash: 51775c5534a13fb2515fafa182658beafd38c1eb
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/02/2019
ms.locfileid: "65026890"
---
# <a name="quickstart-create-an-azure-blockchain-service-using-the-azure-portal"></a>Rychlý start: Vytvoření služby Azure Blockchain pomocí webu Azure portal

Služba Azure Blockchain je platforma blockchain, můžete spustit svoji obchodní logiku v rámci inteligentní kontraktu. V tomto rychlém startu se dozvíte, jak začít tím, že vytvoříte spravovaný registr s pomocí webu Azure portal.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-managed-ledger"></a>Vytvoření spravované účetní kniha

Služba Azure Blockchain se vytvoří s definovanou sadou výpočetních a úložných prostředků.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. V levém horním rohu webu Azure Portal vyberte **Vytvořit prostředek**.
1. Vyberte **Blockchain** > **služby Azure Blockchain**.
1. Dokončení šablony.

    ![Vytvořit službu](./media/create-member/create-member.png)

    Nastavení | Popis
    --------|------------
    Blockchain člena | Zvolte jedinečný název, který identifikuje vaši službu Azure Blockchain člena. Název člena blockchain může obsahovat jenom malá písmena a číslice. První znak musí být písmeno. Hodnota musí být dlouhý 2 až 20 znaků.
    Předplatné | Vyberte předplatné Azure, kterou chcete použít pro vaši službu. Pokud máte více předplatných, zvolte předplatné, ve kterém se vám prostředek účtuje.
    Skupina prostředků | Název nové skupiny prostředků nebo některé ze stávajících ve vašem předplatném.
    Oblast | Umístění musí být stejný pro všechny členy konsorcia.
    Heslo účtu člena | Zadejte nové heslo pro členský účet. Člen heslo účtu se používá k ověření na člen blockchain veřejný koncový bod pomocí základního ověřování.
    Název W3C | Zadejte jedinečný název pro nový consortium. Pokud se zapojíte consortium prostřednictvím pozvánku, hodnota je consortium, ke které se připojujete.
    Popis | Popis konsorcia.
    Protocol (Protokol) |  Ve verzi Preview podporuje protokol kvora.
    Ceny | Konfigurace uzlu pro vaši novou službu. Vyberte **standardní**. 2 uzly program pro ověření a 1 transakci uzel je ve výchozím nastavení.

1. Vyberte **vytvořit** zřídit službu. Zřizování trvá asi 10 minut.
1. Vyberte **oznámení** na panelu nástrojů, které chcete monitorovat proces nasazení.
1. Po nasazení přejděte na svůj blockchain člen.

Vyberte **přehled**, můžete zobrazit základní informace o službě včetně RootContract adresu a členský účet.

![Přehled členů Blockchain](./media/create-member/overview.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Můžete členské, kterou jste vytvořili pro další rychlý start nebo kurz. Pokud už nepotřebujete prostředky můžete odstranit tak, že odstraníte `myResourceGroup` skupiny prostředků vytvořené službou Azure Blockchain.

Pokud chcete odstranit skupinu prostředků:

1. Na webu Azure Portal, přejděte na **skupiny prostředků** v levém navigačním podokně a vyberte skupinu prostředků, kterou chcete odstranit.
2. Vyberte **Odstranit skupinu prostředků**. Ověření odstranění proveďte tak, že zadáte název skupiny prostředků a vyberte **odstranit**.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Použití MetaMask k připojení a nasazovat inteligentní kontraktu](connect-metamask.md)