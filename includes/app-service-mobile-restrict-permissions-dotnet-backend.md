---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: b609a708a987194398c53bdf83f0d6e1f281808d
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/15/2019
ms.locfileid: "67175500"
---
Ve výchozím nastavení lze rozhraní API v Mobile Appsm back-endu vyvolat anonymně. Dál je potřeba omezit přístup jenom na ověřené klienty.  

* **Back-end Node. js (prostřednictvím Azure Portal)** :  

    V nastavení Mobile Apps klikněte na možnost **snadné tabulky** a vyberte tabulku. Klikněte na **změnit oprávnění**, vyberte **ověřený přístup jenom** pro všechna oprávnění a pak klikněte na **Uložit**.
* **Back-end .NETC#()** :  

    V projektu serveru přejděte na Controllers > **TodoItemController.cs**. Přidejte atribut do třídy TodoItemController následujícím způsobem. `[Authorize]` Chcete-li omezit přístup pouze na konkrétní metody, můžete také použít tento atribut pouze pro tyto metody namísto třídy. Publikujte projekt serveru znovu.

        [Authorize]
        public class TodoItemController : TableController<TodoItem>

* **Back-end Node. js (prostřednictvím kódu Node. js)** :  

    Chcete-li vyžadovat ověřování pro přístup k tabulce, přidejte následující řádek do skriptu serveru Node. js:

        table.access = 'authenticated';

    Další podrobnosti najdete v tématu [How to: Vyžadovat ověřování pro přístup k tabulkám](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-auth) Informace o tom, jak stáhnout projekt kódu pro rychlé zprovoznění z webu, [najdete v tématu How to: Stáhněte si projekt kódu pro rychlý Start back-end node](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart). js pomocí Gitu.
