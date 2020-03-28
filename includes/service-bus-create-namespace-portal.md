---
title: zahrnout soubor
description: zahrnout soubor
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 02/20/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: dc80141d796b66dd7e610342166f7b88df58f530
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "75928061"
---
## <a name="create-a-namespace-in-the-azure-portal"></a>Vytvoření oboru názvů na webu Azure Portal
Pokud chcete začít používat entity zasílání zpráv služby Service Bus v Azure, musíte nejprve vytvořit obor názvů s jedinečným názvem v rámci Azure. Obor názvů poskytuje kontejner oboru pro adresování prostředků služby Service Bus v rámci vaší aplikace.

Vytvoření oboru názvů:

1. Přihlášení k [portálu Azure](https://portal.azure.com)
2. V levém navigačním podokně portálu vyberte **+ Vytvořit prostředek**, vyberte **Integrace**a pak vyberte **Service Bus**.

    ![Vytvoření prostředku -> Integrace -> service bus](./media/service-bus-create-namespace-portal/create-resource-service-bus-menu.png)
3. V dialogovém okně **Vytvořit obor názvů** proveďte následující kroky: 
    1. Zadejte **název oboru názvů**. Systém okamžitě kontroluje, jestli je název dostupný. Seznam pravidel pro pojmenování oborů názvů naleznete v tématu [Vytvoření rozhraní REST API oboru názvů](/rest/api/servicebus/create-namespace).
    2. Vyberte cenovou úroveň (základní, standardní nebo prémiovou) pro obor názvů. Pokud chcete používat [témata a předplatná](../articles/service-bus-messaging/service-bus-queues-topics-subscriptions.md#topics-and-subscriptions), zvolte standardní nebo premium. Témata/předplatná se v cenové úrovni Basic nepodporují.
    3. Pokud jste vybrali cenovou úroveň **Premium,** postupujte takto: 
        1. Zadejte počet **jednotek zasílání zpráv**. Úroveň premium poskytuje izolaci prostředků na úrovni procesoru a paměti tak, aby každá úloha běží izolovaně. Kontejner prostředků se nazývá jednotka zasílání zpráv. Prémiový obor názvů má alespoň jednu jednotku zasílání zpráv. Pro každý obor názvů Service Bus Premium můžete vybrat 1, 2 nebo 4 jednotky pro zasílání zpráv. Další informace naleznete v tématu [Service Bus Premium Messaging](../articles/service-bus-messaging/service-bus-premium-messaging.md).
        2. Určete, zda chcete zónu oboru názvů učinit **nadbytečnou**. Redundance zóny poskytuje zvýšenou dostupnost rozložením replik mezi zóny dostupnosti v rámci jedné oblasti bez dalších nákladů. Další informace najdete [v tématu Zóny dostupnosti v Azure](../articles/availability-zones/az-overview.md).
    4. V **části Předplatné**zvolte předplatné Azure, ve kterém chcete vytvořit obor názvů.
    5. Pro **skupinu prostředků**zvolte existující skupinu prostředků, ve které bude obor názvů žít, nebo vytvořte novou.      
    6. V **části Umístění**zvolte oblast, ve které má být obor názvů hostován.
    7. Vyberte **Vytvořit**. Systém teď vytvoří obor názvů a povolí ho. Pravděpodobně budete muset několik minut počkat, než systém zřídí prostředky pro váš účet.
   
        ![Vytvoření oboru názvů](./media/service-bus-create-namespace-portal/create-namespace.png)
4. Zkontrolujte, zda je obor názvů sběrnice úspěšně nasazen. Chcete-li zobrazit oznámení, vyberte **ikonu zvonku (Upozornění)** na panelu nástrojů. Vyberte **název skupiny prostředků** v oznámení, jak je znázorněno na obrázku. Zobrazí se skupina prostředků, která obsahuje obor názvů služby Service Bus.

    ![Výstraha nasazení](./media/service-bus-create-namespace-portal/deployment-alert.png)
5. Na stránce **Skupina prostředků** pro skupinu prostředků vyberte obor názvů **servisní chrupnice**. 

    ![Stránka skupiny prostředků – vyberte obor názvů servisního sběrnice](./media/service-bus-create-namespace-portal/resource-group-select-service-bus.png)
6. Zobrazí se domovská stránka oboru názvů služby Service Bus. 

    ![Domovská stránka oboru názvů služby Service Bus](./media/service-bus-create-namespace-portal/service-bus-namespace-home-page.png)

## <a name="get-the-connection-string"></a>Získání připojovacího řetězce 
Vytvořením nového oboru názvů se automaticky vygeneruje počáteční pravidlo sdíleného přístupového podpisu (SAS) s přidruženým párem primárního a sekundárního klíče, které udělují úplnou kontrolu nad všemi aspekty tohoto oboru názvů. Informace o tom, jak vytvořit pravidla s omezenějšími právy pro běžné odesílatele a příjemce, najdete v tématu [Ověřování a autorizace služby Service Bus.](../articles/service-bus-messaging/service-bus-authentication-and-authorization.md) Chcete-li zkopírovat primární a sekundární klíče pro obor názvů, postupujte takto: 

1. Klikněte na **Všechny prostředky** a pak klikněte na název nově vytvořeného oboru názvů.
2. V okně oboru názvů klikněte na **Zásady sdíleného přístupu**.
3. Na obrazovce **Zásady sdíleného přístupu** klikněte na **RootManageSharedAccessKey**.
   
    ![connection-info](./media/service-bus-create-namespace-portal/connection-info.png)
4. V okně **Zásada: RootManageSharedAccessKey** klikněte na tlačítko Kopírovat vedle položky **Primární připojovací řetězec** a zkopírujte si připojovací řetězec do schránky pro pozdější použití. Vložte tuto hodnotu do Poznámkového bloku nebo jiného dočasného umístění.
   
    ![connection-string](./media/service-bus-create-namespace-portal/connection-string.png)
5. Opakujte předchozí krok, zkopírujte si hodnotu pro **primární klíč** a vložte ji do dočasného umístění pro pozdější použití.

<!--Image references-->

