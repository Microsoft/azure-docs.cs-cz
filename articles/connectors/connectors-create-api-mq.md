---
title: Připojení k serveru IBM MQ
description: Odesílání a načítání zpráv pomocí serveru IBM MQ azure nebo místního serveru IBM MQ a aplikací Azure Logic Apps
services: logic-apps
ms.suite: integration
author: ChristopherHouser
ms.author: chrishou
ms.reviewer: valthom, logicappspm
ms.topic: article
ms.date: 06/19/2019
tags: connectors
ms.openlocfilehash: 6bfd626c1ce69029ee720d24b0b143e7b4c3dd56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650943"
---
# <a name="connect-to-an-ibm-mq-server-from-azure-logic-apps"></a>Připojení k serveru IBM MQ z Aplikací Azure Logic Apps

Konektor IBM MQ odesílá a načítá zprávy uložené na serveru IBM MQ místně nebo v Azure. Tento konektor zahrnuje klienta Microsoft MQ, který komunikuje se vzdáleným serverem IBM MQ v síti TCP/IP. Tento článek obsahuje průvodce startérem pro použití konektoru MQ. Můžete začít procházením jedné zprávy ve frontě a pak zkusit jiné akce.

Konektor IBM MQ obsahuje tyto akce, ale neposkytuje žádné aktivační události:

- Procházení jedné zprávy bez odstranění zprávy ze serveru IBM MQ
- Procházení dávky zpráv bez odstranění zpráv ze serveru IBM MQ
- Přijetí jedné zprávy a odstranění zprávy ze serveru IBM MQ
- Přijímat dávky zpráv a odstranit zprávy ze serveru IBM MQ
- Odeslání jedné zprávy na server IBM MQ

## <a name="prerequisites"></a>Požadavky

* Pokud používáte místní server MQ, [nainstalujte místní bránu dat](../logic-apps/logic-apps-gateway-install.md) na server v rámci sítě. Server, na kterém je nainstalována místní brána dat, musí mít také nainstalovanou rozhraní .NET Framework 4.6, aby konektor MQ fungoval. Musíte také vytvořit prostředek v Azure pro místní bránu dat. Další informace naleznete [v tématu Nastavení připojení brány dat](../logic-apps/logic-apps-gateway-connection.md).

  Pokud je ale server MQ veřejně dostupný nebo dostupný v rámci Azure, nemusíte používat bránu dat.

* Oficiálně podporované verze IBM WebSphere MQ:

  * MQ 7,5
  * MQ 8.0
  * MQ 9.0

* Aplikace logiky, do které chcete přidat akci MQ. Tato aplikace logiky musí používat stejné umístění jako vaše místní připojení brány dat a musí již mít aktivační událost, která spustí váš pracovní postup. 

  Konektor MQ nemá žádné aktivační události, takže je nutné nejprve přidat aktivační událost do aplikace logiky. Můžete například použít aktivační událost opakování. Pokud s aplikacemi logiky teprve začínáte, zkuste tento [rychlý start vytvořit první aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). 

## <a name="browse-a-single-message"></a>Procházení jedné zprávy

1. V aplikaci logiky, pod aktivační událostí nebo jinou akcí, zvolte **Nový krok**. 

1. Do vyhledávacího pole zadejte "mq" a vyberte tuto akci: **Procházet zprávu**

   ![Procházet zprávu](media/connectors-create-api-mq/Browse_message.png)

1. Pokud nemáte existující připojení MQ, vytvořte připojení:  

   1. V akci vyberte **Připojit přes místní bránu dat**.
   
   1. Zadejte vlastnosti serveru MQ.  

      V **části Server**můžete zadat název serveru MQ nebo adresu IP následovanou dvojtečkou a číslem portu.
    
   1. Otevřete seznam **bran,** který zobrazuje všechna dříve nakonfigurovaná připojení brány. Vyberte bránu.
    
   1. Jakmile budete hotoví, vyberte **Vytvořit**. 
   
      Připojení vypadá takto:

      ![Vlastnosti připojení](media/connectors-create-api-mq/Connection_Properties.png)

1. Nastavte vlastnosti akce:

   * **Fronta**: Zadejte frontu, která se liší od připojení.

   * **MessageId**, **CorrelationId**, **GroupId**a další vlastnosti: Vyhledejte zprávu na základě různých vlastností zprávy MQ

   * **IncludeInfo**: Zadejte **True** zahrnout další informace o zprávě ve výstupu. Nebo zadejte **False** nezahrnovat další informace o zprávě ve výstupu.

   * **Časový čas**: Zadejte hodnotu, která určuje, jak dlouho má čekat, než zpráva dorazí do prázdné fronty. Pokud není nic zadáno, je načtena první zpráva ve frontě a není čas strávený čekáním na zobrazení zprávy.

     ![Procházet vlastnosti zprávy](media/connectors-create-api-mq/Browse_message_Props.png)

1. **Uložte** změny a **spusťte** aplikaci logiky.

   ![Uložit a spustit](media/connectors-create-api-mq/Save_Run.png)

   Po dokončení spuštění jsou zobrazeny kroky z běhu a můžete zkontrolovat výstup.

1. Chcete-li zkontrolovat podrobnosti pro každý krok, zaškrtněte políčko zelená. Chcete-li zkontrolovat další informace o výstupních datech, zvolte **Zobrazit nezpracované výstupy**.

   ![Procházet výstup zprávy](media/connectors-create-api-mq/Browse_message_output.png)  

   Zde je několik ukázkových nezpracovaných výstupů:

   ![Procházet nezpracovaný výstup zprávy](media/connectors-create-api-mq/Browse_message_raw_output.png)

1. Pokud nastavíte **IncludeInfo** na hodnotu true, zobrazí se následující výstup:

   ![Procházet zprávy obsahují informace](media/connectors-create-api-mq/Browse_message_Include_Info.png)

## <a name="browse-multiple-messages"></a>Procházení více zpráv

Akce **Procházet zprávy** obsahuje **možnost BatchSize,** která označuje, kolik zpráv má být vráceno z fronty.  Pokud **BatchSize** nemá žádnou položku, jsou vráceny všechny zprávy. Vrácený výstup je pole zpráv.

1. Když přidáte akci **Procházet zprávy,** ve výchozím nastavení je vybráno první dříve nakonfigurované připojení. Chcete-li vytvořit nové připojení, zvolte **Změnit připojení**. Nebo vyberte jiné připojení.

1. Po dokončení spuštění aplikace logiky, zde je některé ukázkový výstup z **procházet zprávy** akce:

   ![Procházení výstupu zpráv](media/connectors-create-api-mq/Browse_messages_output.png)

## <a name="receive-single-message"></a>Přijímat jednu zprávu

Akce **Přijímat zprávy** má stejné vstupy a výstupy jako akce **Procházet zprávu.** Při použití **přijmout zprávu**, zpráva je odstraněna z fronty.

## <a name="receive-multiple-messages"></a>Příjem více zpráv

Akce **Přijímat zprávy** má stejné vstupy a výstupy jako akce **Procházet zprávy.** Při použití **přijímat zprávy**jsou zprávy odstraněny z fronty.

Pokud nejsou žádné zprávy ve frontě při procházení nebo příjem, krok selže s tímto výstupem:  

![Chyba zprávy MQ Bez](media/connectors-create-api-mq/MQ_No_Msg_Error.png)

## <a name="send-message"></a>Odeslat zprávu

Když přidáte akci **Odeslat zprávy,** ve výchozím nastavení je vybráno první dříve nakonfigurované připojení. Chcete-li vytvořit nové připojení, zvolte **Změnit připojení**. Nebo vyberte jiné připojení.

1. Výběr platného typu zprávy: **Datagram**, **Odpovědět**nebo **Požadavek**  

   ![Odeslat msg rekvizity](media/connectors-create-api-mq/Send_Msg_Props.png)

1. Po dokončení spuštění aplikace logiky, tady je nějaký ukázkový výstup z akce **Odeslat zprávu:**

   ![Odeslat výstup Msg](media/connectors-create-api-mq/Send_Msg_Output.png)

## <a name="connector-reference"></a>Referenční informace ke konektorům

Další technické podrobnosti o tomto konektoru, jako jsou aktivační události, akce a omezení, jak je popsáno v souboru Swagger konektoru, naleznete [na referenční stránce konektoru](https://docs.microsoft.com/connectors/mq/).

> [!NOTE]
> Pro aplikace logiky v [prostředí služby integrace (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), tento konektor je ise označené verze používá [omezení zpráv ISE](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) místo.

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech logic apps](../connectors/apis-list.md)
