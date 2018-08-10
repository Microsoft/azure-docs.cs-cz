---
title: Vytvoření centra událostí Azure | Dokumentace Microsoftu
description: Vytvořit obor názvů služby Azure Event Hubs a centra událostí pomocí webu Azure portal
services: event-hubs
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.date: 05/29/2017
ms.author: shvija
ms.openlocfilehash: fb2020f7bfc8521e141adb4eefbc227e8123a269
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2018
ms.locfileid: "40002616"
---
# <a name="create-an-event-hubs-namespace-and-an-event-hub-using-the-azure-portal"></a>Vytvořit obor názvů služby Event Hubs a centra událostí pomocí webu Azure portal

## <a name="create-an-event-hubs-namespace"></a>Vytvoření oboru názvů služby Event Hubs

1. Přihlaste se k [webu Azure portal][Azure portal]a klikněte na tlačítko **vytvořit prostředek** v levém horním rohu obrazovky.
2. Klikněte na **Internet věcí** a pak na **Event Hubs**.
   
    ![](./media/event-hubs-create/create-event-hub9.png)

3. V části **Vytvořit obor názvů** zadejte název oboru názvů. Systém okamžitě kontroluje, jestli je název dostupný.  

4. Po kontrole, že je název oboru názvů k dispozici, zvolte cenovou úroveň (Basic nebo Standard). Zvolte také předplatné Azure, skupinu prostředků a umístění, ve kterém se má prostředek vytvořit.
 
5. Kliknutím na **Vytvořit** vytvoříte obor názvů. Možná budete muset několik minut počkat, než systém úplně zřídí prostředky.

    ![](./media/event-hubs-create/create-event-hub1.png)

6. V seznamu oborů názvů na portálu klikněte na nově vytvořený obor názvů.

7. Klikněte na **Zásady sdíleného přístupu** a pak na **RootManageSharedAccessKey**.
    
    ![](./media/event-hubs-create/create-event-hub7.png)

8. Kliknutím na tlačítko kopírovat zkopírujte připojovací řetězec **RootManageSharedAccessKey** do schránky. Pro pozdější použití si tento připojovací řetězec uložte do dočasného umístění, například do Poznámkového bloku.
    
    ![](./media/event-hubs-create/create-event-hub8.png)

## <a name="create-an-event-hub"></a>Vytvoření centra událostí

1. V seznamu oborů názvů služby Event Hubs klikněte na nově vytvořený obor názvů.      
   
    ![](./media/event-hubs-create/create-event-hub2.png) 

2. V okně oboru názvů klikněte na **Event Hubs**.
   
    ![](./media/event-hubs-create/create-event-hub3.png)

3. V horní části okna klikněte na tlačítko **+ Centrum událostí**.
   
    ![](./media/event-hubs-create/create-event-hub4.png)
4. Zadejte název centra událostí a pak klikněte na **Vytvořit**. 

Vaše Centrum událostí je nyní vytvořen a máte připojovací řetězce, které potřebujete k odesílání a příjmu událostí.

## <a name="next-steps"></a>Další postup

Další informace o službě Event Hubs naleznete pod těmito odkazy:

* [Přehled služby Event Hubs](event-hubs-what-is-event-hubs.md)
* [Přehled rozhraní API služby Event Hubs](event-hubs-api-overview.md)

[Azure portal]: https://portal.azure.com/