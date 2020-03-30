---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: b609a708a987194398c53bdf83f0d6e1f281808d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67175500"
---
Ve výchozím nastavení mohou být uživatelská api v back-endu mobilních aplikací vyvolána anonymně. Dále je třeba omezit přístup pouze na ověřené klienty.  

* **Back-end Node.js (přes portál Azure):**  

    V nastavení mobilních aplikací klikněte na **Snadné tabulky** a vyberte tabulku. Klepněte na **tlačítko Změnit oprávnění**, vyberte možnost **Ověřený přístup pouze** pro všechna oprávnění a potom klepněte na tlačítko **Uložit**.
* **Back-end .NET (C#)**:  

    V projektu serveru přejděte na **řadiče** > **TodoItemController.cs**. Přidejte `[Authorize]` atribut do třídy **TodoItemController** následujícím způsobem. Chcete-li omezit přístup pouze k určité metody, můžete také použít tento atribut pouze pro tyto metody namísto třídy. Znovu publikujte projekt serveru.

        [Authorize]
        public class TodoItemController : TableController<TodoItem>

* **Back-end Node.js (pomocí kódu Node.js):**  

    Chcete-li vyžadovat ověření pro přístup k tabulce, přidejte do skriptu serveru Node.js následující řádek:

        table.access = 'authenticated';

    Další podrobnosti naleznete v [tématu Postup: Vyžadovat ověřování pro přístup k tabulkám](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-auth). Informace o stažení projektu kódu rychlého startu z webu naleznete v tématu [Postup: Stažení projektu back-endového kódu node.js pomocí Gitu](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart).
