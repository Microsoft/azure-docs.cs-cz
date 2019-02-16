---
title: Vytvoření centra oznámení Azure pomocí webu Azure portal | Dokumentace Microsoftu
description: V tomto kurzu se dozvíte, jak k vytvoření centra oznámení Azure pomocí webu Azure portal.
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.topic: quickstart
ms.date: 02/14/2019
ms.author: jowargo
ms.openlocfilehash: 62e72f27e48f7bf220901f4eb36090f926724a2a
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2019
ms.locfileid: "56314104"
---
# <a name="create-an-azure-notification-hub-in-the-azure-portal"></a>Vytvoření centra oznámení Azure na webu Azure Portal 
Služba Azure Notification Hubs nabízí snadno použitelné, škálovatelné zasílací jádro, které umožňuje posílat oznámení libovolné platformě (iOS, Android, Windows, Kindle, Baidu atd.) z libovolného back-endu (cloudový nebo místní). Další informace týkající se služby najdete v tématu [co je Azure Notification Hubs?](notification-hubs-push-notification-overview.md).

V tomto rychlém startu vytvoříte Centrum oznámení na webu Azure Portal. První část obsahuje kroky k vytvoření oboru názvů Notification Hubs a rozbočovač v tomto oboru názvů. Druhá část obsahuje kroky k vytvoření centra oznámení v existujícího oboru názvů Notification Hubs. 

## <a name="create-a-namespace-and-a-notification-hub"></a>Vytvoření oboru názvů a centra oznámení
V této části vytvoříte obor názvů a centra v oboru názvů. 

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

## <a name="create-a-notification-hub-in-an-existing-namespace"></a>Vytvoření centra oznámení v existujícího oboru názvů
V této části vytvoříte v existující obor názvů centra oznámení. 

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vyberte **všechny služby** v nabídce vlevo vyhledejte **centra oznámení**vyberte **hvězdičky** (`*`) vedle položky **názvové prostory centra oznámení** a přidejte ji tak **Oblíbené** části v nabídce vlevo. Vyberte **názvové prostory centra oznámení**. 

      ![Azure portal – vyberte názvové prostory centra oznámení](./media/create-notification-hub-portal/select-notification-hub-namespaces-all-services.png)
3. Na **názvové prostory centra oznámení** vašeho oboru názvů vyberte ze seznamu. 

      ![Zvolte svůj obor názvů v seznamu](./media/create-notification-hub-portal/select-namespace.png)
1. Na **Namespace centra oznámení** stránce **přidat Centrum** na panelu nástrojů. 

      ![Názvové prostory centra oznámení – přidat tlačítko centra](./media/create-notification-hub-portal/add-hub-button.png)
4. Na **nového centra oznámení** stránky, zadejte název centra oznámení a vyberte **OK**.

      ![Nového centra oznámení -> stránky zadejte název vašeho centra](./media/create-notification-hub-portal/new-notification-hub-page.png)
4. Vyberte **oznámení** (ikona zvonku) v horní části stránky a zjistit stav nasazení nového centra. Vyberte **X** v pravém rohu zavřete okno oznámení. 

      ![Oznámení nasazení](./media/create-notification-hub-portal/deployment-notification.png)
5. Aktualizovat **názvové prostory centra oznámení** webovou stránku zobrazit vaše nové centrum v seznamu. 

      ![Portál Azure Portal – oznámení -> Přejít k prostředku](./media/create-notification-hub-portal/new-hub-in-list.png)
6. Vyberte vaše **centra oznámení** zobrazíte na domovské stránce pro vaše Centrum oznámení. 

      ![Portál Azure Portal – oznámení -> Přejít k prostředku](./media/create-notification-hub-portal/hub-home-page.png)

## <a name="next-steps"></a>Další postup
V tomto rychlém startu jste vytvořili centrum oznámení. Zjistěte, jak nakonfigurovat nastavení (PNS) systému oznámení platformy centra, najdete v článku [nakonfigurovat nastavení systému oznámení platformy centra oznámení](configure-notification-hub-portal-pns-settings.md). 