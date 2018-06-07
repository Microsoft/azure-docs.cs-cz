---
title: Vytvoření centra událostí Azure | Microsoft Docs
description: Vytvoření Azure Event Hubs obor názvů a centra událostí pomocí portálu Azure
services: event-hubs
author: sethmanheim
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.date: 05/29/2017
ms.author: sethm
ms.openlocfilehash: 9b466d4e727c1511ca2318c0da3ec2807a965a5d
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34625538"
---
# <a name="create-an-event-hubs-namespace-and-an-event-hub-using-the-azure-portal"></a>Vytvoření oboru názvů Event Hubs a centra událostí pomocí portálu Azure

## <a name="create-an-event-hubs-namespace"></a>Vytvoření oboru názvů Event Hubs

1. Přihlaste se k [portál Azure][Azure portal]a klikněte na tlačítko **vytvořit prostředek** v levém horním rohu obrazovky.
2. Klikněte na tlačítko **Internet věcí**a potom klikněte na **Event Hubs**.
   
    ![](./media/event-hubs-create/create-event-hub9.png)

3. V **vytvoření oboru názvů**, zadejte název oboru názvů. Systém okamžitě kontroluje, jestli je název dostupný.  

4. Po kontrole, že je název oboru názvů k dispozici, zvolte cenovou úroveň (Basic nebo Standard). Zvolte také předplatné Azure, skupinu prostředků a umístění, ve kterém se má prostředek vytvořit.
 
5. Kliknutím na **Vytvořit** vytvoříte obor názvů. Možná budete muset několik minut počkat, než systém úplně zřídí prostředky.

    ![](./media/event-hubs-create/create-event-hub1.png)

6. V seznamu portálu oborů názvů klikněte na nově vytvořený obor názvů.

7. Klikněte na tlačítko **zásady sdíleného přístupu**a potom klikněte na **RootManageSharedAccessKey**.
    
    ![](./media/event-hubs-create/create-event-hub7.png)

8. Kliknutím na tlačítko kopírovat zkopírujte připojovací řetězec **RootManageSharedAccessKey** do schránky. Uložte tento připojovací řetězec do dočasného umístění, například Poznámkový blok pro pozdější použití.
    
    ![](./media/event-hubs-create/create-event-hub8.png)

## <a name="create-an-event-hub"></a>Vytvoření centra událostí

1. V seznamu oboru názvů služby Event Hubs klikněte na nově vytvořený obor názvů.      
   
    ![](./media/event-hubs-create/create-event-hub2.png) 

2. V okně oboru názvů klikněte na **Event Hubs**.
   
    ![](./media/event-hubs-create/create-event-hub3.png)

3. V horní části okna klikněte na tlačítko **+ centra událostí**.
   
    ![](./media/event-hubs-create/create-event-hub4.png)
4. Zadejte název pro vaše Centrum událostí a pak klikněte na **vytvořit**. 

Centrum událostí je teď vytvořené a máte připojovací řetězce, které potřebujete k odesílání a příjmu událostí.

## <a name="next-steps"></a>Další postup

Další informace o službě Event Hubs naleznete pod těmito odkazy:

* [Přehled služby Event Hubs](event-hubs-what-is-event-hubs.md)
* [Přehled rozhraní API služby Event Hubs](event-hubs-api-overview.md)

[Azure portal]: https://portal.azure.com/