---
title: Vytvoření centra oznámení Azure pomocí Azure Portal | Microsoft Docs
description: V tomto kurzu se naučíte vytvořit centrum oznámení Azure pomocí Azure Portal.
services: notification-hubs
author: sethmanheim
ms.author: sethm
manager: femila
ms.reviewer: thsomasu
ms.date: 08/04/2020
ms.lastreviewed: 02/14/2019
ms.topic: quickstart
ms.service: notification-hubs
ms.workload: mobile
ms.custom:
- mode-portal
ms.openlocfilehash: 4381c9259788982832a02ecbe2c87c6f6e723772
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533602"
---
# <a name="quickstart-create-an-azure-notification-hub-in-the-azure-portal"></a>Rychlý Start: vytvoření centra oznámení Azure v Azure Portal

Služba Azure Notification Hubs nabízí snadno použitelné, škálovatelné zasílací jádro, které umožňuje posílat oznámení libovolné platformě (iOS, Android, Windows, Kindle, Baidu atd.) z libovolného back-endu (cloudový nebo místní). Další informace o této službě najdete v tématu [co je Azure Notification Hubs?](notification-hubs-push-notification-overview.md).

V tomto rychlém startu vytvoříte centrum oznámení v Azure Portal. První část obsahuje kroky pro vytvoření oboru názvů Notification Hubs a centra v daném oboru názvů. Druhá část obsahuje postup vytvoření centra oznámení v existujícím oboru názvů Notification Hubs.

## <a name="create-a-namespace-and-a-notification-hub"></a>Vytvoření oboru názvů a centra oznámení

V této části vytvoříte obor názvů a centrum v oboru názvů.

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

## <a name="create-a-notification-hub-in-an-existing-namespace"></a>Vytvoření centra oznámení v existujícím oboru názvů

V této části vytvoříte centrum oznámení v existujícím oboru názvů.

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
2. V nabídce vlevo vyberte **všechny služby** , vyhledejte **Centrum oznámení**, vyberte **hvězdičku** ( `*` ) vedle položky **obory názvů centra oznámení** a přidejte je do části **Oblíbené** v nabídce vlevo. Vyberte **obory názvů centra oznámení**.

      ![Azure Portal – výběr oborů názvů centra oznámení](./media/create-notification-hub-portal/select-notification-hub-namespaces-all-services.png)
3. Na stránce **obory názvů centra oznámení** vyberte ze seznamu svůj obor názvů.

      ![Ze seznamu vyberte svůj obor názvů.](./media/create-notification-hub-portal/select-namespace.png)
4. Na stránce **obor názvů centra oznámení** vyberte **Přidat centrum** na panelu nástrojů.

      ![Obory názvů centra oznámení – tlačítko Přidat centrum](./media/create-notification-hub-portal/add-hub-button.png)
5. Na stránce **nové centrum oznámení** zadejte název centra oznámení a vyberte **OK**.

      ![Nová stránka centra oznámení – > zadejte název vašeho centra.](./media/create-notification-hub-portal/new-notification-hub-page.png)
6. V horní části vyberte **oznámení** (ikona zvonku) a zobrazte stav nasazení nového centra. Výběrem **X** v pravém horním rohu zavřete okno oznámení.

      ![Oznámení o nasazení](./media/create-notification-hub-portal/deployment-notification.png)
7. Aktualizujte webovou stránku **obory názvů centra oznámení** , abyste viděli nové centrum v seznamu.

      ![Snímek obrazovky, který zobrazuje webovou stránku obory názvů centra oznámení s novým centrem v seznamu.](./media/create-notification-hub-portal/new-hub-in-list.png)
8. Vyberte **Centrum oznámení** , kde se zobrazí domovská stránka centra oznámení.

      ![Snímek obrazovky, který zobrazuje domovskou stránku centra oznámení.](./media/create-notification-hub-portal/hub-home-page.png)

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili centrum oznámení. Informace o tom, jak nakonfigurovat nastavení centra pro PNS (Platform Notification System), najdete v tématu [Konfigurace centra oznámení s nastavením PNS](configure-notification-hub-portal-pns-settings.md).
