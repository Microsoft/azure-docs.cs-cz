---
title: Konfigurace Microsoft služba nabízených oznámení ve službě Azure Notification Hubs | Dokumentace Microsoftu
description: Zjistěte, jak nakonfigurovat nastavení Microsoft službu nabízených oznámení pro centra oznámení Azure.
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: jowargo
ms.openlocfilehash: 1c76b44438e6527439d0a370c92f4120424b8da5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60240316"
---
# <a name="configure-microsoft-push-notification-service-mpns-settings-for-a-notification-hub-in-the-azure-portal"></a>Konfigurace nastavení Microsoft Push Notification Service (MPNS) pro Centrum oznámení na webu Azure Portal
V tomto článku se dozvíte, jak nakonfigurovat nastavení Microsoft Push Notification Service (MPNS) pro centra oznámení Azure pomocí webu Azure portal. 

## <a name="prerequisites"></a>Požadavky
Pokud jste ještě nevytvořili centra oznámení, vytvořte teď. Další informace najdete v tématu [vytvoření centra oznámení Azure na webu Azure Portal](create-notification-hub-portal.md). 

## <a name="configure-microsoft-push-notification-service-mpns"></a>Konfigurace Microsoft služba nabízených oznámení (MPNS)

Následující postup obsahuje kroky nastavení Microsoft Push Notification Service (MPNS) pro Centrum oznámení: 

1. Na webu Azure Portal na **centra oznámení** stránce **Windows Phone (MPNS)** v nabídce vlevo.
1. Povolit neověřený nebo ověřený nabízených oznámení:

   a. Chcete-li povolit neověřená nabízená oznámení, vyberte **povolit neověřená nabízená oznámení** > **Uložit**.

      ![Snímek obrazovky, který ukazuje, jak povolit neověřená nabízená oznámení](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

   b. Pokud chcete povolit ověřené nabízených oznámení:
      * Na panelu nástrojů vyberte **nahrát certifikát**.
      * Vyberte ikonu souboru a potom vyberte soubor certifikátu.
      * Zadejte heslo pro certifikát.
      * Vyberte **OK**.
      * Na **Windows Phone (MPNS)** stránce **Uložit**.

## <a name="next-steps"></a>Další postup
Kurz s podrobnými pokyny pro odesílání nabízených oznámení do zařízení Windows Phone pomocí Azure Notification Hubs a Microsoft Push Notification Service (MPNS), najdete v tématu [nabízená oznámení do aplikací Windows Phone pomocí oznámení Rozbočovače](notification-hubs-windows-mobile-push-notifications-mpns.md).

