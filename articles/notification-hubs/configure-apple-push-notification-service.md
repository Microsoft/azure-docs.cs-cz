---
title: Konfigurace Apple Push Notification Service v Azure Notification Hubs | Microsoft Docs
description: Přečtěte si, jak nakonfigurovat nastavení služby Azure Notification hub s Apple Push Notification Service (APNS).
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 06/22/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 03/25/2019
ms.openlocfilehash: 63c7e0c9569428b55420911f253deee52ce440cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "85255394"
---
# <a name="configure-apple-push-notification-service-settings-for-a-notification-hub-in-the-azure-portal"></a>Konfigurace nastavení Apple Push Notification Service pro Centrum oznámení v Azure Portal

V tomto článku se dozvíte, jak nakonfigurovat nastavení služby Apple Push Notification Service (APNS) pro Centrum oznámení Azure pomocí Azure Portal.

## <a name="prerequisites"></a>Požadavky

Pokud jste centrum oznámení ještě nevytvořili, vytvořte ho hned teď. Další informace najdete v tématu [vytvoření centra oznámení Azure v Azure Portal](create-notification-hub-portal.md).

## <a name="configure-apple-push-notification-service"></a>Konfigurace Apple Push Notification Service

Následující postup vám poskytne postup konfigurace nastavení Apple Push Notification Service (APNS) pro Centrum oznámení:

1. V Azure Portal na stránce **centra oznámení** vyberte v nabídce vlevo možnost **Apple (APNs)** .

1. V **režimu ověřování** vyberte buď **certifikát** , nebo **token**.

   - Pokud vyberete možnost **certifikát**:
      - Vyberte ikonu souboru a potom vyberte soubor *. p12* , který chcete nahrát.
      - Zadejte heslo.
      - Vyberte režim **Izolovaný prostor**. Nebo pokud chcete odesílat nabízená oznámení uživatelům, kteří si zakoupili vaši aplikaci ze Storu, vyberte režim **výroby** .

     ![Snímek obrazovky s konfigurací certifikátu APNS v Azure Portal](./media/configure-apple-push-notification-service/notification-hubs-apple-config-cert.png)

   - Pokud vyberete **token**:
      - Zadejte hodnoty **ID klíče**, **ID sady**, **ID týmu** a **tokenu**.
      - Vyberte režim **Izolovaný prostor**. Nebo pokud chcete odesílat nabízená oznámení uživatelům, kteří si zakoupili vaši aplikaci ze Storu, vyberte režim **výroby** .

     ![Snímek obrazovky s konfigurací tokenu APNS v Azure Portal](./media/configure-apple-push-notification-service/notification-hubs-apple-config-token.png)

## <a name="next-steps"></a>Další kroky

Kurz s podrobnými pokyny pro posílání oznámení do zařízení se systémem iOS najdete v následujícím článku: [Posílání nabízených oznámení do aplikací pro iOS pomocí Azure Notification Hubs](ios-sdk-get-started.md).
