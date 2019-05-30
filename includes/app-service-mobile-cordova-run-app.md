---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 05/06/2019
ms.author: crdun
ms.openlocfilehash: 8d7731480b6239c572d39f52b6a0217d2ac48d25
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2019
ms.locfileid: "66240234"
---
1. Přejděte na soubor řešení v projektu klienta (.sln) a otevřete ho pomocí sady Visual Studio.

2. V sadě Visual Studio zvolte platformu řešení (Android, iOS nebo Windows) z rozevíracího seznamu vedle spouštěcí šipky. Vyberte konkrétní zařízení pro nasazení nebo emulátor kliknutím na rozevírací seznam na zelené šipce. Můžete použít výchozí platformu Android a emulátor Ripple. Pokročilejší kurzy (například k nabízeným oznámením) vyžadují výběr podporovaného zařízení nebo emulátoru.

3. Otevřete soubor `ToDoActivity.java` v této složce - ZUMOAPPNAME/aplikace/src/main/java/com/příklad/zumoappname. Název aplikace je `ZUMOAPPNAME`.

4. Přejděte [webu Azure portal](https://portal.azure.com/) a přejděte do mobilní aplikace, kterou jste vytvořili. Na `Overview` okno Vyhledat adresu URL, které je veřejný koncový bod pro mobilní aplikace. Například - sitename pro Moje aplikace název "test123" bude https://test123.azurewebsites.net.

5. Přejděte na `index.js` ZUMOAPPNAME/www/js/index.js a v souboru `onDeviceReady()` metoda, nahraďte `ZUMOAPPURL` parametr s veřejným koncovým bodem výše.

    `client = new WindowsAzure.MobileServiceClient('ZUMOAPPURL');`
    
    změní
    
    `client = new WindowsAzure.MobileServiceClient('https://test123.azurewebsites.net');`
    
6. Aplikaci Cordova sestavíte a spustíte stisknutím klávesy F5 nebo kliknutím na zelenou šipku. Pokud se v emulátoru zobrazí dialogové okno zabezpečení požadující přístup k síti, potvrďte ho.

7. Po spuštění aplikace na zařízení nebo emulátoru zadejte smysluplný text do **zadejte nový text**, jako například *absolvování tohoto kurzu* a potom klikněte na tlačítko **přidat** tlačítko.

Back-end vloží data z požadavku do tabulky TodoItem ve službě SQL Database a vrátí informace o nově uložených položkách do mobilní aplikace. Mobilní aplikace zobrazí tato data v seznamu.

Kroky 3 až 5 můžete opakovat i pro jiné platformy.