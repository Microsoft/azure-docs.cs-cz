---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: a5bde1a56bf6a1f5fca4b775c7c8e9bb7477eb6b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66240278"
---
1. Otevřete stažený klientský projekt pomocí Xcode.

2. Přejděte [webu Azure portal](https://portal.azure.com/) a přejděte do mobilní aplikace, kterou jste vytvořili. Na `Overview` okno Vyhledat adresu URL, které je veřejný koncový bod pro mobilní aplikace. Například - sitename pro Moje aplikace název "test123" bude https://test123.azurewebsites.net.

3. Pro projekt, Swift, otevřete soubor `ToDoTableViewController.swift` v této složce - ZUMOAPPNAME/ZUMOAPPNAME/ToDoTableViewController.swift. Název aplikace je `ZUMOAPPNAME`.

4. V `viewDidLoad()` metoda, nahraďte `ZUMOAPPURL` parametr s veřejným koncovým bodem výše.

    `let client = MSClient(applicationURLString: "ZUMOAPPURL")`

    změní
    
    `let client = MSClient(applicationURLString: "https://test123.azurewebsites.net")`
    
5. Pro projekt jazyka Objective-C, otevřete soubor `QSTodoService.m` v této složce - ZUMOAPPNAME/ZUMOAPPNAME. Název aplikace je `ZUMOAPPNAME`.

6. V `init` metoda, nahraďte `ZUMOAPPURL` parametr s veřejným koncovým bodem výše.

    `self.client = [MSClient clientWithApplicationURLString:@"ZUMOAPPURL"];`

    změní
    
    `self.client = [MSClient clientWithApplicationURLString:@"https://test123.azurewebsites.net"];`

7. Stisknutím tlačítka **Spustit** sestavíte projekt a spustíte aplikaci v simulátoru iOS.

8. V aplikaci, klikněte na znaménko plus ( **+** ) ikona, zadejte smysluplný text, například *absolvování tohoto kurzu*a potom klikněte na Uložit tlačítko. Tím odešlete do prostředí back-end v Azure, které jste předtím vytvořili, požadavek POST. Back-end vloží data z požadavku do tabulky SQL TodoItem a vrátí informace o nově uložených položkách do mobilní aplikace. Mobilní aplikace zobrazí tato data v seznamu.

   ![Aplikace Rychlý start pro iOS](./media/app-service-mobile-ios-quickstart/mobile-quickstart-startup-ios.png)

[Azure portal]: https://portal.azure.com/
