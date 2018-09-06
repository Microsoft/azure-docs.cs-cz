---
title: zahrnout soubor
description: zahrnout soubor
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 06/29/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 7ed298fc8f13685c4872c4c54ba1e447debea79f
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2018
ms.locfileid: "43702628"
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
