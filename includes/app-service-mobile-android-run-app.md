---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 05/09/2019
ms.author: crdun
ms.openlocfilehash: 63c54f8af91b6b4a76ba49d5e6fc7b3cda9f5b98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "66240323"
---
1. Otevřete projekt v **Android Studiu** pomocí možnosti **Importovat projekt (Eclipse ADT, Gradle atd.)**. Nezapomeňte vybrat tuto možnost importu, abyste se vyhnuli případným chybám JDK.

2. Otevřete `ToDoActivity.java` soubor v této složce - ZUMOAPPNAME/app/src/main/java/com/example/zumoappname. Název aplikace `ZUMOAPPNAME`je .

3. Přejděte na [portál Azure](https://portal.azure.com/) a přejděte na mobilní aplikaci, kterou jste vytvořili. Na `Overview` okně vyhledejte adresu URL, která je veřejným koncovým bodem pro vaši mobilní aplikaci. Příklad – název webu pro název aplikace "test123" bude https://test123.azurewebsites.net.

4. V `onCreate()` metodě `ZUMOAPPURL` nahraďte parametr veřejným koncovým bodem výše.
    
    `new MobileServiceClient("ZUMOAPPURL", this).withFilter(new ProgressFilter());` 
    
    se stává
    
    `new MobileServiceClient("https://test123.azurewebsites.net", this).withFilter(new ProgressFilter());`
    
5. Stisknutím tlačítka **Spustit aplikaci** sestavíte projekt a spustíte aplikaci v simulátoru Androidu.

4. V aplikaci zadejte smysluplný text, třeba *Dokončit kurz*, a klikněte na tlačítko Přidat. Tím odešlete do prostředí back-end v Azure, které jste předtím vytvořili, požadavek POST. Back-end vloží data z požadavku do tabulky SQL TodoItem a vrátí informace o nově uložených položkách do mobilní aplikace. Mobilní aplikace zobrazí tato data v seznamu.
    ![Rychlý start androidu](./media/app-service-mobile-android-quickstart/mobile-quickstart-startup-android.png)