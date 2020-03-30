---
title: Vytvoření centra oznámení Azure pomocí portálu Azure | Dokumenty společnosti Microsoft
description: V tomto kurzu se dozvíte, jak vytvořit centrum oznámení Azure pomocí portálu Azure.
services: notification-hubs
author: sethmanheim
manager: femila
editor: jwargo
ms.service: notification-hubs
ms.workload: mobile
ms.topic: quickstart
ms.date: 02/14/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 02/14/2019
ms.openlocfilehash: 3aeeb989d15dc74849c85fa58cbefa891809f3c5
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80347089"
---
# <a name="quickstart-create-an-azure-notification-hub-in-the-azure-portal"></a>Úvodní příručka: Vytvoření centra oznámení Azure na webu Azure Portal 
Služba Azure Notification Hubs nabízí snadno použitelné, škálovatelné zasílací jádro, které umožňuje posílat oznámení libovolné platformě (iOS, Android, Windows, Kindle, Baidu atd.) z libovolného back-endu (cloudový nebo místní). Další informace o službě najdete v tématu [Co je Centra oznámení Azure?](notification-hubs-push-notification-overview.md).

V tomto rychlém startu vytvoříte centrum oznámení na webu Azure Portal. První část obsahuje kroky k vytvoření oboru názvů Centra oznámení a centra v tomto oboru názvů. Druhá část obsahuje kroky k vytvoření centra oznámení v existujícím oboru názvů Centra oznámení. 

## <a name="create-a-namespace-and-a-notification-hub"></a>Vytvoření oboru názvů a centra oznámení
V této části vytvoříte obor názvů a rozbočovač v oboru názvů. 

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

## <a name="create-a-notification-hub-in-an-existing-namespace"></a>Vytvoření centra oznámení v existujícím oboru názvů
V této části vytvoříte centrum oznámení v existujícím oboru názvů. 

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. V yberte **Všechny služby** v levé nabídce,`*`vyhledejte Centrum **oznámení**, vyberte **hvězdičku** ( ) vedle **položky Obory názvů centra oznámení** a přidejte je do oddílu OBLÍBENÉ **položky** v levé nabídce. Vyberte **Obory názvů centra oznámení**. 

      ![Portál Azure – výběr oborů názvů centra oznámení](./media/create-notification-hub-portal/select-notification-hub-namespaces-all-services.png)
3. Na stránce **Obory názvů centra oznámení** vyberte obor názvů ze seznamu. 

      ![Výběr oboru názvů ze seznamu](./media/create-notification-hub-portal/select-namespace.png)
1. Na stránce **Obor názvů centra oznámení** vyberte na panelu nástrojů přidat **centrum.** 

      ![Obory názvů centra oznámení – tlačítko Přidat rozbočovač](./media/create-notification-hub-portal/add-hub-button.png)
4. Na stránce **Nové centrum oznámení** zadejte název centra oznámení a vyberte **OK**.

      ![Nová stránka Centra oznámení - > zadat název centra](./media/create-notification-hub-portal/new-notification-hub-page.png)
4. Chcete-li zobrazit stav nasazení nového centra, vyberte v horní části možnost **Oznámení** (ikona Zvonek). **Chcete-li** zavřít okno oznámení, vyberte x v pravém rohu. 

      ![Oznámení o nasazení](./media/create-notification-hub-portal/deployment-notification.png)
5. Aktualizujte webovou stránku **Obory názvů centra oznámení,** abyste viděli nové centrum v seznamu. 

      ![Portál Azure Portal – oznámení -> Přejít k prostředku](./media/create-notification-hub-portal/new-hub-in-list.png)
6. Výběrem **centra oznámení** zobrazíte domovskou stránku centra oznámení. 

      ![Portál Azure Portal – oznámení -> Přejít k prostředku](./media/create-notification-hub-portal/hub-home-page.png)

## <a name="next-steps"></a>Další kroky
V tomto rychlém startu jste vytvořili centrum oznámení. Informace o konfiguraci rozbočovače pomocí nastavení systému oznámení platformy (PNS) naleznete [v tématu Konfigurace centra oznámení s nastavením Služby PNS](configure-notification-hub-portal-pns-settings.md). 