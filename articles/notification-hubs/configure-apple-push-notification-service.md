---
title: Konfigurace služby nabízených oznámení Apple v centrech oznámení Azure | Dokumenty společnosti Microsoft
description: Přečtěte si, jak nakonfigurovat centrum oznámení Azure pomocí nastavení služby APNS (Apple Push Notification Service).
services: notification-hubs
author: sethmanheim
manager: femila
editor: jwargo
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 03/25/2019
ms.openlocfilehash: eb1122ba3de0002507589d3e607d1e39d905c308
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127513"
---
# <a name="configure-apple-push-notification-service-settings-for-a-notification-hub-in-the-azure-portal"></a>Konfigurace nastavení služby Nabízených oznámení Apple pro centrum oznámení na webu Azure Portal

Tento článek ukazuje, jak nakonfigurovat nastavení služby Apple Push Notification Service (APNS) pro centrum oznámení Azure pomocí portálu Azure. 

## <a name="prerequisites"></a>Požadavky
Pokud jste centrum oznámení ještě nevytvořili, vytvořte ho nyní. Další informace najdete [v tématu Vytvoření centra oznámení Azure na webu Azure Portal](create-notification-hub-portal.md). 

## <a name="configure-apple-push-notification-service"></a>Konfigurace služby nabízených oznámení Apple

Následující postup poskytuje postup konfigurace nastavení služby APNS služby Nabízených oznámení Apple (APNS) pro centrum oznámení:

1. Na webu Azure Portal na stránce **Centrum oznámení** vyberte **apple (APNS)** v levé nabídce.

1. V **režimu ověřování**vyberte **certifikát** nebo **token**.

   a. Pokud vyberete **certifikát**:
   * Vyberte ikonu souboru a pak vyberte soubor *P12,* který chcete nahrát.
   * Zadejte heslo.
   * Vyberte režim **Izolovaný prostor**. Chcete-li odeslat nabízená oznámení uživatelům, kteří si aplikaci zakoupili v obchodě, vyberte **režim produkčního** prostředí.

     ![Snímek obrazovky s konfigurací certifikátu APNS na webu Azure Portal](./media/configure-apple-push-notification-service/notification-hubs-apple-config-cert.png)

   b. Pokud zvolíte **Token**:

   * Zadejte hodnoty pro **ID klíče**, **ID balíčku**, **ID týmu**a **token**.
   * Vyberte režim **Izolovaný prostor**. Chcete-li odeslat nabízená oznámení uživatelům, kteří si aplikaci zakoupili v obchodě, vyberte **režim produkčního** prostředí.

     ![Snímek obrazovky s konfigurací tokenu APNS na webu Azure Portal](./media/configure-apple-push-notification-service/notification-hubs-apple-config-token.png)

## <a name="next-steps"></a>Další kroky
Kurz s podrobnými pokyny pro odesílání oznámení do zařízení se systémem iOS najdete v následujícím článku: [Nabízená oznámení do zařízení s iOS pomocí center oznámení a APNS](notification-hubs-ios-apple-push-notification-apns-get-started.md)
