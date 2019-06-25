---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: 852a3b00e8513f9ce68c5aae54c974505d0c9af6
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67175505"
---
1. Na počítači Mac spusťte **přístup do řetězce klíčů**. V levém navigačním panelu v části **kategorie**, otevřete **mé certifikáty**. Vyhledejte certifikát protokolu SSL, který jste stáhli v předchozí části a potom zveřejnit svůj obsah. Vyberte jenom ten certifikát (nesmí být zvolen privátní klíč). Potom [ho exportovat](https://support.apple.com/kb/PH20122?locale=en_US).
2. V [webu Azure portal](https://portal.azure.com/)vyberte **Procházet vše** > **App Services**. Potom vyberte Mobile Apps back-endu. 
3. V části **nastavení**vyberte **App Service Push**. Vyberte název vašeho centra oznámení. 
4. Přejděte na **Apple Push Notification Services** > **nahrát certifikát**. Nahrajte soubor .p12, výběrem správné **režimu** (v závislosti na tom, zda certifikát klienta SSL z předchozí je produkční nebo izolovaného prostoru). Uložte změny.

Vaše služba je nyní nakonfigurováno pro práci s nabízenými oznámeními v systému iOS.

[1]: ./media/app-service-mobile-apns-configure-push/mobile-push-notification-hub.png
