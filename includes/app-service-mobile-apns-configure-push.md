---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: 852a3b00e8513f9ce68c5aae54c974505d0c9af6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "67175505"
---
1. Na Macu spusťte **Keychain Access**. Na levém navigačním panelu v **části Kategorie**otevřete **moje certifikáty**. Najděte certifikát SSL, který jste stáhli v předchozí části, a pak prozradíte jeho obsah. Vyberte pouze certifikát (nevybírejte soukromý klíč). Pak [jej exportujte](https://support.apple.com/kb/PH20122?locale=en_US).
2. Na [webu Azure Portal](https://portal.azure.com/)vyberte **Procházet všechny** > **služby aplikací**. Pak vyberte back-end mobilních aplikací. 
3. V části **Nastavení**vyberte **Nabízená služba Push**. Pak vyberte název centra oznámení. 
4. Přejděte na certifikát apple **push notification services** > **upload .** Nahrajte soubor .p12 a vyberte správný **režim** (v závislosti na tom, zda je váš klientský certifikát SSL z dřívější výroby nebo izolovaného prostoru). Uložte všechny změny.

Vaše služba je teď nakonfigurovaná pro práci s nabízenými oznámeními v systému iOS.

[1]: ./media/app-service-mobile-apns-configure-push/mobile-push-notification-hub.png
