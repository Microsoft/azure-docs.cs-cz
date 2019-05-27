---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: f4ba467b6d80c9ccafba0a91c1f04152b92cf869
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66140670"
---
1. Na počítači Mac přejděte na web [Azure Portal]. Klikněte na **Všechny služby** > **App Services** a pak na back-end, který jste právě vytvořili. V nastavení mobilní aplikace zvolte preferovaný jazyk:

   - Objective-C &ndash; **Rychlý start** > **iOS (Objective-C)**
   - Swift &ndash; **Rychlý start** > **iOS (Swift)**

     V části **3. Konfigurace klientské aplikace** klikněte na **Stáhnout**. Tím stáhnete dokončený projekt Xcode přednastavený k připojení k vašemu back-endu. Otevřete projekt pomocí Xcode.

1. Stisknutím tlačítka **Spustit** sestavíte projekt a spustíte aplikaci v simulátoru iOS.

1. V aplikaci, klikněte na znaménko plus (**+**) ikona, zadejte smysluplný text, například *absolvování tohoto kurzu*a potom klikněte na Uložit tlačítko. Tím odešlete do prostředí back-end v Azure, které jste předtím vytvořili, požadavek POST. Back-end vloží data z požadavku do tabulky SQL TodoItem a vrátí informace o nově uložených položkách do mobilní aplikace. Mobilní aplikace zobrazí tato data v seznamu.

   ![Aplikace Rychlý start pro iOS](./media/app-service-mobile-ios-quickstart/mobile-quickstart-startup-ios.png)

[Azure Portal]: https://portal.azure.com/
