---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: 3217383b105c022aef42d8000f3a41cefea542fe
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66140909"
---
1. Navštivte [Azure Portal].
2. Klikněte na **App Services** > back-end, který jste vytvořili.
3. V nastavení mobilní aplikace klikněte na **Rychlý start** > **Cordova**.
![Azure Portal se zvýrazněnou možností Rychlý start Mobile Apps][quickstart]
4. V části **Configure your client application** (Konfigurace klientské aplikace) vyberte možnost **Vytvořit novou aplikaci** a klikněte na **Stáhnout**.
2. Rozbalte stažený soubor ZIP do adresáře na pevném disku, přejděte do souboru řešení (.sln) a otevřete ho pomocí sady Visual Studio.
3. V sadě Visual Studio zvolte platformu řešení (Android, iOS nebo Windows) z rozevíracího seznamu vedle spouštěcí šipky. Vyberte konkrétní zařízení pro nasazení nebo emulátor kliknutím na rozevírací seznam na zelené šipce. Můžete použít výchozí platformu Android a emulátor Ripple. Pokročilejší kurzy (například k nabízeným oznámením) vyžadují výběr podporovaného zařízení nebo emulátoru.
4. Aplikaci Cordova sestavíte a spustíte stisknutím klávesy F5 nebo kliknutím na zelenou šipku. Pokud se v emulátoru zobrazí dialogové okno zabezpečení požadující přístup k síti, potvrďte ho.
5. Po spuštění aplikace v zařízení nebo emulátoru zadejte smysluplný text do pole **Zadejte nový text**, například *Dokončení kurzu*, a pak klikněte na tlačítko **Přidat**.

Back-end vloží data z požadavku do tabulky TodoItem ve službě SQL Database a vrátí informace o nově uložených položkách do mobilní aplikace. Mobilní aplikace zobrazí tato data v seznamu.

Kroky 3 až 5 můžete opakovat i pro jiné platformy.

<!-- Images. -->
[quickstart]: ./media/app-service-mobile-configure-new-backend/quickstart.png

<!-- URLs -->
[Azure Portal]: https://portal.azure.com/
