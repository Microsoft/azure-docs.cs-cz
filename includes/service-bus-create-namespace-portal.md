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
ms.openlocfilehash: d2c309340155bc626d4da94d74aee9be51bde510
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90606165"
---
## <a name="create-a-namespace-in-the-azure-portal"></a>Vytvoření oboru názvů na webu Azure Portal
Pokud chcete začít používat entity zasílání zpráv služby Service Bus v Azure, musíte nejprve vytvořit obor názvů s jedinečným názvem v rámci Azure. Obor názvů poskytuje kontejner oboru pro adresování prostředků služby Service Bus v rámci vaší aplikace.

Vytvoření oboru názvů:

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com).
2. V levém navigačním podokně portálu vyberte **+ vytvořit prostředek**, vyberte **integrace**a pak vyberte **Service Bus**.

    ![Vytvoření > > integrací prostředků Service Bus](./media/service-bus-create-namespace-portal/create-resource-service-bus-menu.png)
3. V dialogovém okně **vytvořit obor názvů** proveďte následující kroky: 
    1. Zadejte **název oboru názvů**. Systém okamžitě kontroluje, jestli je název dostupný. Seznam pravidel pro názvové obory názvů najdete v tématu [Create Namespace REST API](/rest/api/servicebus/create-namespace).
    2. Vyberte cenovou úroveň (Basic, Standard nebo Premium) pro obor názvů. Pokud chcete používat [témata a odběry](../articles/service-bus-messaging/service-bus-queues-topics-subscriptions.md#topics-and-subscriptions), vyberte buď Standard, nebo Premium. Témata/předplatná se v cenové úrovni Basic nepodporují.
    3. Pokud jste vybrali cenovou úroveň **Premium** , postupujte podle následujících kroků: 
        1. Zadejte počet **jednotek zasílání zpráv**. Úroveň Premium poskytuje izolaci prostředků na úrovni procesoru a paměti, aby se jednotlivé úlohy spouštěly izolovaně. Kontejner prostředků se nazývá jednotka zasílání zpráv. Obor názvů Premium má alespoň jednu jednotku zasílání zpráv. Pro každý obor názvů Service Bus Premium můžete vybrat 1, 2 nebo 4 jednotky zasílání zpráv. Další informace najdete v tématu [Service Bus zasílání zpráv](../articles/service-bus-messaging/service-bus-premium-messaging.md)na úrovni Premium.
        2. Určete, zda má být zóna oboru názvů **redundantní**. Redundance zóny poskytuje rozšířenou dostupnost tím, že šíří repliky napříč zónami dostupnosti v rámci jedné oblasti bez dalších nákladů. Další informace najdete v tématu [zóny dostupnosti v Azure](../articles/availability-zones/az-overview.md).
    4. V části **předplatné**vyberte předplatné Azure, ve kterém chcete vytvořit obor názvů.
    5. V poli **Skupina prostředků**vyberte existující skupinu prostředků, ve které bude obor názvů živý, nebo vytvořte novou.      
    6. V poli **umístění**vyberte oblast, ve které by měl být hostovaný obor názvů.
    7. Vyberte **Vytvořit**. Systém teď vytvoří obor názvů a povolí ho. Pravděpodobně budete muset několik minut počkat, než systém zřídí prostředky pro váš účet.
   
        ![Vytvoření oboru názvů](./media/service-bus-create-namespace-portal/create-namespace.png)
4. Potvrďte, že obor názvů služby Service Bus byl úspěšně nasazen. Oznámení zobrazíte výběrem **ikony zvonku (výstrahy)** na panelu nástrojů. Vyberte **název skupiny prostředků** v oznámení, jak je znázorněno na obrázku. Zobrazí se skupina prostředků, která obsahuje obor názvů služby Service Bus.

    ![Výstraha nasazení](./media/service-bus-create-namespace-portal/deployment-alert.png)
5. Na stránce **skupiny prostředků** pro vaši skupinu prostředků vyberte svůj **obor názvů služby Service Bus**. 

    ![Stránka skupiny prostředků – vyberte obor názvů služby Service Bus.](./media/service-bus-create-namespace-portal/resource-group-select-service-bus.png)
6. Zobrazí se Domovská stránka oboru názvů služby Service Bus. 

    ![Domovská stránka oboru názvů služby Service Bus](./media/service-bus-create-namespace-portal/service-bus-namespace-home-page.png)

## <a name="get-the-connection-string"></a>Získání připojovacího řetězce 
Vytvořením nového oboru názvů se automaticky vygeneruje počáteční pravidlo sdíleného přístupového podpisu (SAS) s přidruženým párem primárního a sekundárního klíče, které udělují úplnou kontrolu nad všemi aspekty tohoto oboru názvů. Informace o tom, jak vytvořit pravidla s více omezenými právy pro pravidelné odesílatele a přijímače, najdete v tématu [Service Bus ověřování a autorizaci](../articles/service-bus-messaging/service-bus-authentication-and-authorization.md) . K zkopírování primárního a sekundárního klíče pro svůj obor názvů použijte následující postup: 

1. Klikněte na **Všechny prostředky** a pak klikněte na název nově vytvořeného oboru názvů.
2. V okně oboru názvů klikněte na **Zásady sdíleného přístupu**.
3. Na obrazovce **Zásady sdíleného přístupu** klikněte na **RootManageSharedAccessKey**.
   
    ![Snímek obrazovky s zvýrazněnou zásadou zobrazí okno zásady sdíleného přístupu.](./media/service-bus-create-namespace-portal/connection-info.png)
4. V okně **Zásada: RootManageSharedAccessKey** klikněte na tlačítko Kopírovat vedle položky **Primární připojovací řetězec** a zkopírujte si připojovací řetězec do schránky pro pozdější použití. Vložte tuto hodnotu do Poznámkového bloku nebo jiného dočasného umístění.
   
    ![Snímek obrazovky se zobrazí S názvem RootManageSharedAccessKey, který obsahuje klíče a připojovací řetězce.](./media/service-bus-create-namespace-portal/connection-string.png)
5. Opakujte předchozí krok, zkopírujte si hodnotu pro **primární klíč** a vložte ji do dočasného umístění pro pozdější použití.

<!--Image references-->

