---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: b609a708a987194398c53bdf83f0d6e1f281808d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66139852"
---
Ve výchozím nastavení může anonymně volat rozhraní API v back-end Mobile Apps. Dále je třeba omezit přístup jenom ověřené klienty.  

* **Node.js zpět end (prostřednictvím webu Azure portal)** :  

    V nastavení mobilní aplikace klikněte na tlačítko **jednoduché tabulky** a výběr tabulky. Klikněte na tlačítko **změnit oprávnění**vyberte **ověřený přístup pouze** pro všechna oprávnění a pak klikněte na tlačítko **Uložit**.
* **Rozhraní .NET back-endu (C#)** :  

    V projektu serveru, přejděte na **řadiče** > **TodoItemController.cs**. Přidat `[Authorize]` atribut **TodoItemController** třídy následujícím způsobem. Pokud chcete omezit přístup jenom na konkrétní metody, můžete také použít tento atribut jenom do těchto metod namísto třídy. Znovu publikujte projekt serveru.

        [Authorize]
        public class TodoItemController : TableController<TodoItem>

* **Back-end Node.js (prostřednictvím kódu Node.js)** :  

    Ověřování vyžadovat pro přístup k tabulce, přidejte následující řádek do skriptu server Node.js:

        table.access = 'authenticated';

    Další podrobnosti najdete v tématu [jak: Ověřování vyžadovat pro přístup k tabulkám](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-auth). Zjistěte, jak stáhnout projekt rychlý start kód z lokality, najdete v článku [jak: Stáhněte si projekt kódu rychlý start back-end Node.js pomocí Gitu](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart).
