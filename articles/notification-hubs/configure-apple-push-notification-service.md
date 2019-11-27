---
title: Konfigurace Apple Push Notification Service v Azure Notification Hubs | Microsoft Docs
description: Přečtěte si, jak nakonfigurovat nastavení služby Azure Notification hub s Apple Push Notification Service (APNS).
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
ms.openlocfilehash: 116af5192236045ec50409f65a9687ffec5223b5
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406093"
---
# <a name="configure-apple-push-notification-service-apns-settings-for-a-notification-hub-in-the-azure-portal"></a>Konfigurace nastavení služby Apple Push Notification Service (APNS) pro Centrum oznámení v Azure Portal
V tomto článku se dozvíte, jak nakonfigurovat nastavení služby Apple Push Notification Service (APNS) pro Centrum oznámení Azure pomocí Azure Portal. 

## <a name="prerequisites"></a>Požadavky
Pokud jste centrum oznámení ještě nevytvořili, vytvořte ho hned teď. Další informace najdete v tématu [vytvoření centra oznámení Azure v Azure Portal](create-notification-hub-portal.md). 

## <a name="configure-apple-push-notification-service"></a>Konfigurace Apple Push Notification Service

Následující postup vám poskytne postup konfigurace nastavení Apple Push Notification Service (APNS) pro Centrum oznámení:

1. V Azure Portal na stránce **centra oznámení** vyberte v nabídce vlevo možnost **Apple (APNs)** .

1. V **režimu ověřování**vyberte buď **certifikát** , nebo **token**.

   a. Pokud vyberete možnost **certifikát**:
   * Vyberte ikonu souboru a potom vyberte soubor *. p12* , který chcete nahrát.
   * Zadejte heslo.
   * Vyberte režim **Sandbox**. Nebo pokud chcete odesílat nabízená oznámení uživatelům, kteří si zakoupili vaši aplikaci ze Storu, vyberte režim **výroby** .

     ![Snímek obrazovky s konfigurací certifikátu APNS v Azure Portal](./media/configure-apple-push-notification-service/notification-hubs-apple-config-cert.png)

   b. Pokud vyberete **token**:

   * Zadejte hodnoty **ID klíče**, **ID sady**, **ID týmu**a **tokenu**.
   * Vyberte režim **Sandbox**. Nebo pokud chcete odesílat nabízená oznámení uživatelům, kteří si zakoupili vaši aplikaci ze Storu, vyberte režim **výroby** .

     ![Snímek obrazovky s konfigurací tokenu APNS v Azure Portal](./media/configure-apple-push-notification-service/notification-hubs-apple-config-token.png)

## <a name="next-steps"></a>Další kroky
Kurz s podrobnými pokyny pro doručování oznámení do zařízení se systémem iOS najdete v následujícím článku: [nabízená oznámení na zařízení s iOS pomocí Notification Hubs a APNs](notification-hubs-ios-apple-push-notification-apns-get-started.md) .
