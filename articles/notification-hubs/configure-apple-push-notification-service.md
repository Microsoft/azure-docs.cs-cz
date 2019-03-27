---
title: Konfigurace Apple Push Notification Service ve službě Azure Notification Hubs | Dokumentace Microsoftu
description: Zjistěte, jak ke konfiguraci centra oznámení Azure s nastavením Apple Push Notification Service (APNS).
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: jowargo
ms.openlocfilehash: 9a9db9f05895569b050e56cdeec1ee2ee25af0df
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2019
ms.locfileid: "58488273"
---
# <a name="configure-apple-push-notification-service-apns-settings-for-a-notification-hub-in-the-azure-portal"></a>Konfigurace nastavení Apple Push Notification Service (APNS) pro Centrum oznámení na webu Azure Portal
V tomto článku se dozvíte, jak nakonfigurovat nastavení Apple Push Notification Service (APNS) pro centra oznámení Azure pomocí webu Azure portal. 

## <a name="prerequisites"></a>Požadavky
Pokud jste ještě nevytvořili centra oznámení, vytvořte teď. Další informace najdete v tématu [vytvoření centra oznámení Azure na webu Azure Portal](create-notification-hub-portal.md). 

## <a name="configure-apple-push-notification-service"></a>Konfigurace Apple Push Notification Service

Následující postup obsahuje postup pro konfiguraci nastavení Apple Push Notification Service (APNS) pro Centrum oznámení:

1. Na webu Azure Portal na **centra oznámení** stránce **Apple (APNS)** v nabídce vlevo.

1. Pro **režim ověřování**, vyberte buď **certifikát** nebo **Token**.

   a. Pokud vyberete **certifikát**:
   * Vyberte ikonu souboru a pak vyberte *.p12* soubor, který chcete odeslat.
   * Zadejte heslo.
   * Vyberte režim **Sandbox**. Nebo k odesílání nabízených oznámení uživatelům, kteří si zakoupili aplikaci z obchodu, vyberte **produkční** režimu.

     ![Snímek obrazovky APNS certifikátu konfigurace na webu Azure Portal](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png)

   b. Pokud vyberete **Token**:

   * Zadejte hodnoty pro **ID klíče**, **ID sady prostředků**, **ID týmu**, a **Token**.
   * Vyberte režim **Sandbox**. Nebo k odesílání nabízených oznámení uživatelům, kteří si zakoupili aplikaci z obchodu, vyberte **produkční** režimu.

     ![Snímek obrazovky APNS token konfigurace na webu Azure Portal](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-token.png)

## <a name="next-steps"></a>Další postup
Kurz s podrobnými pokyny pro odesílání nabízených oznámení do zařízení s Iosem najdete v následujícím článku: [Nabízená oznámení do zařízení s Iosem pomocí Notification Hubs a služby APN](notification-hubs-ios-apple-push-notification-apns-get-started.md)
