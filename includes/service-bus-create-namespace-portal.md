---
title: zahrnout soubor
description: zahrnout soubor
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 02/12/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: b38f66670ba29022713ae39824a190fcffb688c7
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56238674"
---
Pokud chcete začít používat entity zasílání zpráv služby Service Bus v Azure, musíte nejprve vytvořit obor názvů s jedinečným názvem v rámci Azure. Obor názvů poskytuje kontejner oboru pro adresování prostředků služby Service Bus v rámci vaší aplikace.

Vytvoření oboru názvů:

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com).
2. V levém navigačním podokně portálu vyberte **+ vytvořit prostředek**vyberte **integrace**a pak vyberte **služby Service Bus**.

    ![Vytvořit prostředek -> integrace -> služby Service Bus](./media/service-bus-create-namespace-portal/create-resource-service-bus-menu.png)
3. V **vytvoření oboru názvů** dialogového okna, proveďte následující kroky: 
    1. Zadejte **název pro obor názvů**. Systém okamžitě kontroluje, jestli je název dostupný.
    2. Vyberte cenovou úroveň (Basic, Standard nebo Premium) pro obor názvů. Pokud chcete použít [témata a odběry](../articles/service-bus-messaging/service-bus-queues-topics-subscriptions.md#topics-and-subscriptions), zvolit úroveň Standard nebo Premium. Témata/předplatná se v cenové úrovni Basic nepodporují.
    3. Pokud jste vybrali **Premium** cenovou úroveň, postupujte podle těchto kroků: 
        1. Zadejte počet **jednotky zasílání zpráv**. Úroveň premium zajišťuje izolaci prostředků na úrovni procesoru a paměti, takže každá úloha běží izolovaně. Kontejner prostředků se nazývá jednotka zasílání zpráv. Každému prémiovému obor názvů se přiřadí aspoň jedna jednotka zasílání zpráv. Můžete vybrat 1, 2 nebo 4 jednotky zasílání zpráv pro každý obor názvů Service Bus úrovně Premium. Další informace najdete v tématu [služby zasílání zpráv Service Bus úrovně Premium](../articles/service-bus-messaging/service-bus-premium-messaging.md).
        2. Určete, jestli chcete vytvořit obor názvů **zónově redundantní**. Redundanci zón poskytuje vylepšenou dostupnost šířením replik napříč zónami dostupnosti v rámci jedné oblasti bez dalších poplatků. Další informace najdete v tématu [zón dostupnosti v Azure](../articles/availability-zones/az-overview.md).
    4. Pro **předplatné**, zvolte předplatné Azure, ve kterém chcete vytvořit obor názvů.
    5. Pro **skupiny prostředků**, zvolte existující skupinu prostředků, ve které bude obor názvů live, nebo vytvořte novou.      
    6. Pro **umístění**, vyberte oblast, ve kterém by měl váš obor názvů hostovat.
    7. Vyberte **Vytvořit**. Systém teď vytvoří obor názvů a povolí ho. Pravděpodobně budete muset několik minut počkat, než systém zřídí prostředky pro váš účet.
   
        ![Vytvoření oboru názvů](./media/service-bus-create-namespace-portal/create-namespace.png)
4. Potvrďte, že je obor názvů služby Service bus úspěšné nasazení. Pokud chcete zobrazit oznámení, vyberte **ikonu zvonku (výstrahy)** na panelu nástrojů. Vyberte **název skupiny prostředků** v oznámení, jak je znázorněno na obrázku. Zobrazí se skupiny prostředků, který obsahuje obor názvů služby Service bus.

    ![Výstraha nasazení](./media/service-bus-create-namespace-portal/deployment-alert.png)
5. Na **skupiny prostředků** stránce vaší skupiny prostředků, vyberte vaše **obor názvů služby Service bus**. 

    ![Skupina prostředků stránce – Výběr oboru názvů služby service bus](./media/service-bus-create-namespace-portal/resource-group-select-service-bus.png)
6. Zobrazí domovská stránka pro váš obor názvů služby Service bus. 

    ![Domovská stránka oboru názvů služby service bus](./media/service-bus-create-namespace-portal/service-bus-namespace-home-page.png)

### <a name="get-the-management-credentials"></a>Získání přihlašovacích údajů pro správu
Vytvořením nového oboru názvů se automaticky vygeneruje počáteční pravidlo sdíleného přístupového podpisu (SAS) s přidruženým párem primárního a sekundárního klíče, které udělují úplnou kontrolu nad všemi aspekty tohoto oboru názvů. Zobrazit [ověřování a autorizace Service Bus](../articles/service-bus-messaging/service-bus-authentication-and-authorization.md) informace o tom, jak vytvořit pravidla s větším omezeného práva pro pravidelné odesílateli a příjemci. Pokud chcete zkopírovat primární a sekundární klíče pro váš obor názvů, postupujte takto: 

1. Klikněte na **Všechny prostředky** a pak klikněte na název nově vytvořeného oboru názvů.
2. V okně oboru názvů klikněte na **Zásady sdíleného přístupu**.
3. Na obrazovce **Zásady sdíleného přístupu** klikněte na **RootManageSharedAccessKey**.
   
    ![connection-info](./media/service-bus-create-namespace-portal/connection-info.png)
4. V **zásad: RootManageSharedAccessKey** okna, klikněte na tlačítko kopírování vedle **primární připojovací řetězec**, zkopírujte připojovací řetězec do schránky pro pozdější použití. Vložte tuto hodnotu do Poznámkového bloku nebo jiného dočasného umístění.
   
    ![connection-string](./media/service-bus-create-namespace-portal/connection-string.png)
5. Opakujte předchozí krok, zkopírujte si hodnotu pro **primární klíč** a vložte ji do dočasného umístění pro pozdější použití.

<!--Image references-->

