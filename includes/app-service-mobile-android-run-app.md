---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 05/09/2019
ms.author: crdun
ms.openlocfilehash: 63c54f8af91b6b4a76ba49d5e6fc7b3cda9f5b98
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66240323"
---
1. Otevřete projekt v **Android Studiu** pomocí možnosti **Importovat projekt (Eclipse ADT, Gradle atd.)** . Nezapomeňte vybrat tuto možnost importu, abyste se vyhnuli případným chybám JDK.

2. Otevřete soubor `ToDoActivity.java` v této složce - ZUMOAPPNAME/aplikace/src/main/java/com/příklad/zumoappname. Název aplikace je `ZUMOAPPNAME`.

3. Přejděte [webu Azure portal](https://portal.azure.com/) a přejděte do mobilní aplikace, kterou jste vytvořili. Na `Overview` okno Vyhledat adresu URL, které je veřejný koncový bod pro mobilní aplikace. Například - sitename pro Moje aplikace název "test123" bude https://test123.azurewebsites.net.

4. V `onCreate()` metoda, nahraďte `ZUMOAPPURL` parametr s veřejným koncovým bodem výše.
    
    `new MobileServiceClient("ZUMOAPPURL", this).withFilter(new ProgressFilter());` 
    
    změní
    
    `new MobileServiceClient("https://test123.azurewebsites.net", this).withFilter(new ProgressFilter());`
    
5. Stisknutím tlačítka **Spustit aplikaci** sestavíte projekt a spustíte aplikaci v simulátoru Androidu.

4. V aplikaci zadejte smysluplný text, třeba *Dokončit kurz*, a klikněte na tlačítko Přidat. Tím odešlete do prostředí back-end v Azure, které jste předtím vytvořili, požadavek POST. Back-end vloží data z požadavku do tabulky SQL TodoItem a vrátí informace o nově uložených položkách do mobilní aplikace. Mobilní aplikace zobrazí tato data v seznamu.
    ![Rychlý start s Androidem](./media/app-service-mobile-android-quickstart/mobile-quickstart-startup-android.png)