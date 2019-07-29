---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 07/05/2019
ms.author: glenga
ms.openlocfilehash: 0493de7374cffcc40f0434d84facf50b6a708c7b
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68592900"
---
1. Chcete-li spustit funkci, stiskněte klávesu **F5**. Možná budete muset povolit výjimku brány firewall, aby nástroje mohly zpracovávat požadavky HTTP. Úrovně autorizace se při místním spuštění nikdy neuplatňují.

2. Zkopírujte adresu URL vaší funkce z výstupu modulu runtime služby Azure Functions.

    ![Místní modul runtime Azure](./media/functions-run-function-test-local-vs/functions-debug-local-vs.png)

3. Vložte adresu URL pro požadavek HTTP do panelu adresy prohlížeče. K této adrese URL připojte řetězec dotazu `?name=<YOUR_NAME>` a proveďte požadavek. Následuje ukázka odezvy na místní požadavek GET vrácené funkcí v prohlížeči: 

    ![Odezva místního hostitele funkce v prohlížeči](./media/functions-run-function-test-local-vs/functions-run-browser-local-vs.png)

4. Pokud chcete zastavit ladění, stiskněte **Shift + F5**.