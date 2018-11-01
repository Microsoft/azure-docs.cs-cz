---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: 1d1d593b7305e0cd9899f4ec388cb441ced90b10
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2018
ms.locfileid: "50133079"
---
1. Na počítači Mac přejděte na web [Azure Portal]. Klikněte na **Všechny služby** > **App Services** a pak na back-end, který jste právě vytvořili. V nastavení mobilní aplikace zvolte preferovaný jazyk:

    - Objective-C &ndash; **Rychlý start** > **iOS (Objective-C)**
    - Swift &ndash; **Rychlý start** > **iOS (Swift)**

    V části **3. Konfigurace klientské aplikace** klikněte na **Stáhnout**. Tím stáhnete dokončený projekt Xcode přednastavený k připojení k vašemu back-endu. Otevřete projekt pomocí Xcode.

1. Stisknutím tlačítka **Spustit** sestavíte projekt a spustíte aplikaci v simulátoru iOS.

1. V aplikaci zadejte smysluplný text, například *Dokončit kurz* a pak klikněte na ikonu plus (**+**). Tím odešlete do prostředí back-end v Azure, které jste předtím vytvořili, požadavek POST. Prostředí back-end vloží data z požadavku do tabulky SQL TodoItem a vrátí informace o nově uložených položkách do mobilní aplikace. Mobilní aplikace zobrazí tato data v seznamu.

   ![Aplikace Rychlý start pro iOS](./media/app-service-mobile-ios-quickstart/mobile-quickstart-startup-ios.png)

[Azure Portal]: https://portal.azure.com/
