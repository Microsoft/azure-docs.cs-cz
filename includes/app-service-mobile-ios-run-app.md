---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: a5bde1a56bf6a1f5fca4b775c7c8e9bb7477eb6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "66240278"
---
1. Otevřete stažený klientský projekt pomocí Xcode.

2. Přejděte na [portál Azure](https://portal.azure.com/) a přejděte na mobilní aplikaci, kterou jste vytvořili. Na `Overview` okně vyhledejte adresu URL, která je veřejným koncovým bodem pro vaši mobilní aplikaci. Příklad – název webu pro název aplikace "test123" bude https://test123.azurewebsites.net.

3. Pro projekt Swift otevřete soubor `ToDoTableViewController.swift` v této složce - ZUMOAPPNAME/ZUMOAPPNAME/ToDoTableViewController.swift. Název aplikace `ZUMOAPPNAME`je .

4. V `viewDidLoad()` metodě `ZUMOAPPURL` nahraďte parametr veřejným koncovým bodem výše.

    `let client = MSClient(applicationURLString: "ZUMOAPPURL")`

    se stává
    
    `let client = MSClient(applicationURLString: "https://test123.azurewebsites.net")`
    
5. Pro projekt Objective-C otevřete soubor `QSTodoService.m` v této složce - ZUMOAPPNAME/ZUMOAPPNAME. Název aplikace `ZUMOAPPNAME`je .

6. V `init` metodě `ZUMOAPPURL` nahraďte parametr veřejným koncovým bodem výše.

    `self.client = [MSClient clientWithApplicationURLString:@"ZUMOAPPURL"];`

    se stává
    
    `self.client = [MSClient clientWithApplicationURLString:@"https://test123.azurewebsites.net"];`

7. Stisknutím tlačítka **Spustit** sestavíte projekt a spustíte aplikaci v simulátoru iOS.

8. V aplikaci klikněte**+** na ikonu plus ( ), zadejte smysluplný text, například *Dokončit kurz*, a klikněte na tlačítko Uložit. Tím odešlete do prostředí back-end v Azure, které jste předtím vytvořili, požadavek POST. Back-end vloží data z požadavku do tabulky SQL TodoItem a vrátí informace o nově uložených položkách do mobilní aplikace. Mobilní aplikace zobrazí tato data v seznamu.

   ![Aplikace Rychlý start pro iOS](./media/app-service-mobile-ios-quickstart/mobile-quickstart-startup-ios.png)

[Azure portal]: https://portal.azure.com/
