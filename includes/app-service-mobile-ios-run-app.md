---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: 8146489a913ce863cee7534331231a248a3ea7ac
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2019
ms.locfileid: "56890977"
---
1. Na počítači Mac přejděte na web [Azure Portal]. Klikněte na **Všechny služby** > **App Services** a pak na back-end, který jste právě vytvořili. V nastavení mobilní aplikace zvolte preferovaný jazyk:

    - Objective-C &ndash; **Rychlý start** > **iOS (Objective-C)**
    - Swift &ndash; **Rychlý start** > **iOS (Swift)**

    V části **3. Konfigurace klientské aplikace** klikněte na **Stáhnout**. Tím stáhnete dokončený projekt Xcode přednastavený k připojení k vašemu back-endu. Otevřete projekt pomocí Xcode.

1. Stisknutím tlačítka **Spustit** sestavíte projekt a spustíte aplikaci v simulátoru iOS.

1. V aplikaci, klikněte na znaménko plus (**+**) ikona, zadejte smysluplný text, například *absolvování tohoto kurzu*a potom klikněte na Uložit tlačítko. Tím odešlete do prostředí back-end v Azure, které jste předtím vytvořili, požadavek POST. Back-end vloží data z požadavku do tabulky SQL TodoItem a vrátí informace o nově uložených položkách do mobilní aplikace. Mobilní aplikace zobrazí tato data v seznamu.

   ![Aplikace Rychlý start pro iOS](./media/app-service-mobile-ios-quickstart/mobile-quickstart-startup-ios.png)

[Azure Portal]: https://portal.azure.com/
