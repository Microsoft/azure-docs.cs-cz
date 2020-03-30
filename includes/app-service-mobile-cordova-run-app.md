---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 05/06/2019
ms.author: crdun
ms.openlocfilehash: 8d7731480b6239c572d39f52b6a0217d2ac48d25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "66240234"
---
1. Přejděte do souboru řešení v klientském projektu (.sln) a otevřete jej pomocí sady Visual Studio.

2. V sadě Visual Studio zvolte platformu řešení (Android, iOS nebo Windows) z rozevíracího seznamu vedle spouštěcí šipky. Vyberte konkrétní zařízení pro nasazení nebo emulátor kliknutím na rozevírací seznam na zelené šipce. Můžete použít výchozí platformu Android a emulátor Ripple. Pokročilejší kurzy (například k nabízeným oznámením) vyžadují výběr podporovaného zařízení nebo emulátoru.

3. Otevřete `ToDoActivity.java` soubor v této složce - ZUMOAPPNAME/app/src/main/java/com/example/zumoappname. Název aplikace `ZUMOAPPNAME`je .

4. Přejděte na [portál Azure](https://portal.azure.com/) a přejděte na mobilní aplikaci, kterou jste vytvořili. Na `Overview` okně vyhledejte adresu URL, která je veřejným koncovým bodem pro vaši mobilní aplikaci. Příklad – název webu pro název aplikace "test123" bude https://test123.azurewebsites.net.

5. Přejděte `index.js` do souboru v ZUMOAPPNAME/www/js/index.js a v `onDeviceReady()` metodě nahraďte `ZUMOAPPURL` parametr veřejným koncovým bodem výše.

    `client = new WindowsAzure.MobileServiceClient('ZUMOAPPURL');`
    
    se stává
    
    `client = new WindowsAzure.MobileServiceClient('https://test123.azurewebsites.net');`
    
6. Aplikaci Cordova sestavíte a spustíte stisknutím klávesy F5 nebo kliknutím na zelenou šipku. Pokud se v emulátoru zobrazí dialogové okno zabezpečení požadující přístup k síti, potvrďte ho.

7. Po spuštění aplikace na zařízení nebo emulátoru zadejte smysluplný text **do pole Zadejte nový text**, například Dokončit *kurz* a klikněte na tlačítko **Přidat.**

Back-end vloží data z požadavku do tabulky TodoItem ve službě SQL Database a vrátí informace o nově uložených položkách do mobilní aplikace. Mobilní aplikace zobrazí tato data v seznamu.

Kroky 3 až 5 můžete opakovat i pro jiné platformy.