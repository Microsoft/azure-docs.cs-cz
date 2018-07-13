---
title: zahrnout soubor
description: zahrnout soubor
services: service-bus-messaging
author: sethmanheim
ms.service: service-bus-messaging
ms.topic: include
ms.date: 06/29/2018
ms.author: sethm
ms.custom: include file
ms.openlocfilehash: bad7e87cb194fb7d8846e7e675881658a1d33b1e
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37138156"
---
Ujistěte se, že jste již vytvořili obor názvů Service Bus, jak je znázorněno [tady][namespace-how-to].

1. Přihlaste se na web [Azure Portal][azure-portal].
2. V levém navigačním podokně portálu klikněte na **Service Bus** (pokud položku **Service Bus** nevidíte, klikněte na **Všechny služby**).
3. Klikněte na obor názvů, ve kterém chcete vytvořit frontu. V tomto případě se jedná o **sbnstest1**.
   
    ![Vytvoření fronty][createqueue1]
4. V okně oboru názvů klikněte na **Fronty** a pak v okně **Fronty** klikněte na **+ Fronta**.
   
    ![Vyberte Fronty][createqueue2]
5. Zadejte **Název** fronty a nechte ostatní hodnoty na jejich výchozích hodnotách.
   
    ![Vyberte Nový][createqueue3]
6. V dolní části okna klikněte na **Vytvořit**.

[createqueue1]: ./media/service-bus-create-queue-portal/create-queue1.png
[createqueue2]: ./media/service-bus-create-queue-portal/create-queue2.png
[createqueue3]: ./media/service-bus-create-queue-portal/create-queue3.png

[namespace-how-to]: ../articles/service-bus-messaging/service-bus-create-namespace-portal.md
[azure-portal]: https://portal.azure.com
