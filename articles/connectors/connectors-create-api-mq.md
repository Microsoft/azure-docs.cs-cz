---
title: Připojit k serveru IBM MQ
description: Posílání a načítání zpráv pomocí Azure nebo místního serveru IBM MQ a Azure Logic Apps
services: logic-apps
ms.suite: integration
author: ChristopherHouser
ms.author: chrishou
ms.reviewer: valthom, logicappspm
ms.topic: article
ms.date: 06/19/2019
tags: connectors
ms.openlocfilehash: 6bfd626c1ce69029ee720d24b0b143e7b4c3dd56
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77650943"
---
# <a name="connect-to-an-ibm-mq-server-from-azure-logic-apps"></a>Připojení k serveru IBM MQ z Azure Logic Apps

Konektor IBM MQ odesílá a načítá zprávy uložené na serveru IBM MQ místně nebo v Azure. Tento konektor zahrnuje klienta Microsoft MQ, který komunikuje se vzdáleným serverem IBM MQ napříč sítí TCP/IP. Tento článek poskytuje Úvodní příručku k používání konektoru MQ. Můžete začít procházením jedné zprávy ve frontě a následným pokusem o další akce.

Konektor IBM MQ zahrnuje tyto akce, ale neposkytuje žádné triggery:

- Procházení jedné zprávy bez odstranění zprávy ze serveru IBM MQ
- Projděte si dávku zpráv, aniž byste museli odstraňovat zprávy ze serveru IBM MQ.
- Doručení jedné zprávy a odstranění zprávy ze serveru IBM MQ
- Příjem dávky zpráv a odstranění zpráv ze serveru IBM MQ
- Odeslat jednu zprávu na server IBM MQ

## <a name="prerequisites"></a>Předpoklady

* Pokud používáte místní server MQ, [nainstalujte místní bránu dat](../logic-apps/logic-apps-gateway-install.md) na server v rámci vaší sítě. Server, na kterém je nainstalovaná místní brána dat, musí mít nainstalovanou .NET Framework 4,6, aby mohl konektor MQ fungovat. Pro místní bránu dat musíte také vytvořit prostředek v Azure. Další informace najdete v tématu [nastavení připojení pro bránu dat](../logic-apps/logic-apps-gateway-connection.md).

  Pokud je ale váš server MQ veřejně dostupný nebo dostupný v Azure, nemusíte používat bránu data Gateway.

* Oficiálně podporované verze IBM WebSphere MQ:

  * MQ 7.5
  * MQ 8.0
  * MQ 9,0

* Aplikace logiky, do které chcete přidat akci MQ. Tato aplikace logiky musí používat stejné umístění jako místní připojení brány dat a musí mít Trigger, který spouští váš pracovní postup. 

  Konektor MQ nemá žádné triggery, takže musíte nejdřív přidat Trigger do aplikace logiky. Můžete například použít Trigger opakování. Pokud s Logic Apps začínáte, vyzkoušejte si tento [rychlý Start, abyste vytvořili svoji první aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). 

## <a name="browse-a-single-message"></a>Procházet jednu zprávu

1. V aplikaci logiky pod triggerem nebo jinou akcí klikněte na možnost **Nový krok**. 

1. Do vyhledávacího pole zadejte "MQ" a vyberte tuto akci: **zpráva procházení**

   ![Procházet zprávu](media/connectors-create-api-mq/Browse_message.png)

1. Pokud nemáte existující připojení MQ, vytvořte připojení:  

   1. V akci vyberte **připojit přes místní bránu dat**.
   
   1. Zadejte vlastnosti serveru MQ.  

      Pro **Server**můžete zadat název serveru MQ nebo zadat IP adresu následovaný dvojtečkou a číslem portu.
    
   1. Otevřete seznam **Brána** , ve kterém se zobrazí všechna dříve konfigurovaná připojení brány. Vyberte bránu.
    
   1. Jakmile budete hotoví, vyberte **Vytvořit**. 
   
      Vaše připojení vypadá jako v tomto příkladu:

      ![Vlastnosti připojení](media/connectors-create-api-mq/Connection_Properties.png)

1. Nastavte vlastnosti akce:

   * **Queue**: zadejte frontu, která se liší od připojení.

   * **MessageID**, **ID korelace**, **GroupID**a další vlastnosti: Vyhledejte zprávu na základě různých vlastností zprávy MQ.

   * **IncludeInfo**: zadejte **hodnotu true** , pokud chcete do výstupu zahrnout další informace o zprávě. Případně můžete zadat **hodnotu false** , chcete-li do výstupu přidat další informace o zprávě.

   * **Timeout**: zadejte hodnotu, která určuje, jak dlouho se má čekat na doručení zprávy do prázdné fronty. Pokud nezadáte žádnou hodnotu, načte se první zpráva ve frontě a nestrávená doba čekání na zobrazení zprávy.

     ![Procházet vlastnosti zprávy](media/connectors-create-api-mq/Browse_message_Props.png)

1. **Uložte** změny a pak **Spusťte** aplikaci logiky.

   ![Uložit a spustit](media/connectors-create-api-mq/Save_Run.png)

   Po dokončení běhu se zobrazí kroky z běhu a můžete si prohlédnout výstup.

1. Chcete-li zobrazit podrobnosti o jednotlivých krocích, vyberte zelenou značku zaškrtnutí. Chcete-li si přečíst další informace o výstupních datech, vyberte možnost **Zobrazit nezpracované výstupy**.

   ![Procházet výstup zprávy](media/connectors-create-api-mq/Browse_message_output.png)  

   Zde je ukázkový nezpracovaný výstup:

   ![Procházet nezpracovaný výstup zprávy](media/connectors-create-api-mq/Browse_message_raw_output.png)

1. Pokud nastavíte **IncludeInfo** na hodnotu true, zobrazí se následující výstup:

   ![Procházet informace o zahrnutí zprávy](media/connectors-create-api-mq/Browse_message_Include_Info.png)

## <a name="browse-multiple-messages"></a>Procházet více zpráv

Akce **Procházet zprávy** obsahuje možnost **BatchSize** , která určuje, kolik zpráv by mělo být vráceno z fronty.  Pokud **BatchSize** nemá žádnou položku, budou vráceny všechny zprávy. Vrácený výstup je pole zpráv.

1. Když přidáte akci **Procházet zprávy** , ve výchozím nastavení je vybraná první dřív nakonfigurované připojení. Chcete-li vytvořit nové připojení, klikněte na tlačítko **změnit připojení**. Případně vyberte jiné připojení.

1. Po dokončení spuštění aplikace logiky je zde uveden ukázkový výstup z akce **Procházet zprávy** :

   ![Výstup procházení zpráv](media/connectors-create-api-mq/Browse_messages_output.png)

## <a name="receive-single-message"></a>Přijmout jednu zprávu

Akce **přijmout zprávu** má stejné vstupy a výstupy jako akce **procházení zprávy** . Při použití **zprávy Receive**se zpráva z fronty odstraní.

## <a name="receive-multiple-messages"></a>Přijímání více zpráv

Akce **přijmout zprávy** má stejné vstupy a výstupy jako akce **Procházet zprávy** . Při použití **zprávy Receive**se zprávy z fronty odstraňují.

Pokud ve frontě nejsou žádné zprávy při procházení nebo přijímání, krok se nezdařil s tímto výstupem:  

![MQ – chyba zprávy](media/connectors-create-api-mq/MQ_No_Msg_Error.png)

## <a name="send-message"></a>Odeslat zprávu

Když přidáte akci **Odeslat zprávy** , ve výchozím nastavení je vybráno první dřív nakonfigurované připojení. Chcete-li vytvořit nové připojení, klikněte na tlačítko **změnit připojení**. Případně vyberte jiné připojení.

1. Vyberte platný typ zprávy: **datagram**, **Reply**nebo **Request** .  

   ![Poslat zprávu props](media/connectors-create-api-mq/Send_Msg_Props.png)

1. Až se aplikace logiky dokončí, tady je ukázkový výstup z akce **Odeslat zprávu** :

   ![Odeslat výstup zprávy](media/connectors-create-api-mq/Send_Msg_Output.png)

## <a name="connector-reference"></a>Referenční informace ke konektorům

Další technické podrobnosti o této spojnici, jako jsou triggery, akce a omezení, jak je popsáno v souboru Swagger konektoru, najdete na [referenční stránce konektoru](https://docs.microsoft.com/connectors/mq/).

> [!NOTE]
> V případě Logic Apps v [prostředí ISE (Integration Service Environment)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)Tato verze konektoru ISE-Label používá místo toho [omezení zpráv ISE](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) .

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech Logic Apps](../connectors/apis-list.md)
