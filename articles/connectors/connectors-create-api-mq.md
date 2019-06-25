---
title: Připojení k IBM MQ server – Azure Logic Apps
description: Odesílat a přijímat zprávy pomocí Azure nebo místním IBM MQ server a Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: valrobb
ms.author: valthom
ms.reviewer: chrishou, LADocs
ms.topic: article
ms.date: 06/19/2019
tags: connectors
ms.openlocfilehash: a2894799946d069916b27a4f5bcc7bd3244705b2
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2019
ms.locfileid: "67273121"
---
# <a name="connect-to-an-ibm-mq-server-from-azure-logic-apps"></a>Připojení k IBM MQ serveru z Azure Logic Apps

Konektor IBM MQ odešle a načítá zprávy, uloženy na serveru IBM MQ místně nebo v Azure. Tento konektor zahrnuje Microsoft MQ klienta, který komunikuje se vzdáleným serverem IBM MQ přes síť TCP/IP. Tento článek obsahuje úvodní příručka k používání konektoru MQ. Můžete spustit tak, že přejdete do jedné zprávy do fronty a potom zkuste jiné akce.

Konektor IBM MQ zahrnuje tyto akce, ale poskytuje žádné aktivační události:

- Přejděte do jedné zprávy bez odstranění zprávy ze serveru IBM MQ
- Procházet dávku zpráv bez odstranění zprávy ze serveru IBM MQ
- Do jedné zprávy přijme a odstraní zprávu z IBM MQ server
- Přijetí dávku zpráv a odstranění zprávy ze serveru IBM MQ
- Odesílání do jedné zprávy na server IBM MQ

## <a name="prerequisites"></a>Požadavky

* Pokud používáte MQ místnímu serveru [instalace místní brány dat](../logic-apps/logic-apps-gateway-install.md) na serveru ve vaší síti. Server, kde je nainstalována na místní bránu dat musí mít také rozhraní .NET Framework 4.6 nainstalovaný konektor Magic Quadrant pro práci. Pro místní bránu dat musíte také vytvořit prostředek v Azure. Další informace najdete v tématu [nastavení připojení brány data](../logic-apps/logic-apps-gateway-connection.md).

  Nicméně pokud je MQ server nebo veřejně dostupné v rámci Azure, nemusíte použijte bránu data gateway.

* Oficiálně podporované IBM WebSphere MQ verze:

  * MQ 7.5
  * MQ 8.0
  * MQ 9.0

* Aplikace logiky, ve které chcete přidat akci MQ. Tato aplikace logiky musí používat stejné umístění jako místní datové brány připojení a musíte již mít trigger, který se spustí pracovní postup. 

  Konektor MQ nemá žádné aktivační události, proto je nutné přidat aktivační událost nejprve do své aplikace logiky. Můžete například použít trigger opakování. Pokud se službou logic Apps teprve začínáte, zkuste to [rychlý start k vytvoření první aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). 

## <a name="browse-a-single-message"></a>Přejděte do jedné zprávy

1. Ve vaší aplikaci logiky, v rámci aktivační události nebo jiné akce, zvolte **nový krok**. 

1. Do vyhledávacího pole zadejte "mq" a vyberte tuto akci: **Procházení zpráv**

   ![Procházení zpráv](media/connectors-create-api-mq/Browse_message.png)

1. Pokud nemáte připojení k existující Magic Quadrant, vytvořte připojení:  

   1. V akci, vyberte **připojit přes místní bránu dat**.
   
   1. Zadejte vlastnosti pro váš server MQ.  

      Pro **Server**, můžete zadat název serveru Magic Quadrant, nebo zadejte IP adresu, za nímž následuje dvojtečka a číslo portu.
    
   1. Otevřít **brány** seznam, který zobrazuje všechny dříve nakonfigurované připojení brány. Vyberte bránu.
    
   1. Jakmile budete hotoví, vyberte **Vytvořit**. 
   
      Připojení bude vypadat jako v tomto příkladu:

      ![Vlastnosti připojení](media/connectors-create-api-mq/Connection_Properties.png)

1. Nastavte vlastnosti akce:

   * **fronty**: Zadejte frontu, která se liší od připojení.

   * **ID zprávy**, **CorrelationId**, **GroupId**a další vlastnosti: Procházet zprávy na základě různých vlastností zprávu MQ

   * **IncludeInfo**: Zadejte **True** zahrnout informace o dalších zpráv ve výstupu. Nebo zadejte **False** tak, aby nezahrnovala informace o dalších zpráv ve výstupu.

   * **Časový limit**: Zadejte hodnotu k určení jak dlouho čekat zprávy dorazí v prázdné frontě. Pokud je zadán nic, se načte první zprávu ve frontě a neexistuje žádný čas strávený čekáním zprávu zobrazit.

     ![Procházet vlastnosti zprávy](media/connectors-create-api-mq/Browse_message_Props.png)

1. **Uložit** změny a potom **spustit** svou aplikaci logiky.

   ![Uložit a spustit](media/connectors-create-api-mq/Save_Run.png)

   Po dokončení běhu, jsou uvedeny kroky ze spuštění a najdete ve výstupu.

1. Pokud chcete podívat na podrobnosti pro jednotlivé kroky, vyberte zelenou značku zaškrtnutí. Chcete-li zobrazit další informace o výstupní data, zvolte **zobrazit nezpracované výstupy**.

   ![Procházet výstup zpráv](media/connectors-create-api-mq/Browse_message_output.png)  

   Tady je některé nezpracovaná ukázkový výstup:

   ![Procházení zpráv nezpracovaného výstupu](media/connectors-create-api-mq/Browse_message_raw_output.png)

1. Pokud nastavíte **IncludeInfo** na hodnotu true, následující se zobrazí výstup:

   ![Procházet zpráva obsahovat informace o](media/connectors-create-api-mq/Browse_message_Include_Info.png)

## <a name="browse-multiple-messages"></a>Procházet více zpráv

**Procházet zprávy** obsahuje akce **BatchSize** možnost určit, kolik zpráv má být vrácen z fronty.  Pokud **BatchSize** nemá žádný záznam se vrátí všechny zprávy. Vrácené výstupem je pole zpráv.

1. Když přidáte **procházet zprávy** akce, první dříve nakonfigurované připojení je standardně vybraná. Chcete-li vytvořit nové připojení, zvolte **změnit připojení**. Nebo vyberte jiné připojení.

1. Po dokončení běh aplikace logiky, tady je několik ukázkový výstup z **procházet zprávy** akce:

   ![Procházet výstupní zprávy](media/connectors-create-api-mq/Browse_messages_output.png)

## <a name="receive-single-message"></a>Zobrazí se jedné zpráva

**Přijímání zpráv** akce se stejnými vstupy a výstupy jako **procházet zpráva** akce. Při použití **přijímání zpráv**, odstraní zprávu z fronty.

## <a name="receive-multiple-messages"></a>Přijmout více zpráv

**Přijímat zprávy** akce se stejnými vstupy a výstupy jako **procházet zprávy** akce. Při použití **přijímat zprávy**, odstraní se zprávy z fronty.

Pokud nejsou žádné zprávy ve frontě, při provádění prohlížení nebo příjmu, tohoto kroku se nepovede se tento výstup:  

![Chyba č MQ Message](media/connectors-create-api-mq/MQ_No_Msg_Error.png)

## <a name="send-message"></a>Odeslání zprávy

Když přidáte **odesílat zprávy** akce, první dříve nakonfigurované připojení je standardně vybraná. Chcete-li vytvořit nové připojení, zvolte **změnit připojení**. Nebo vyberte jiné připojení.

1. Vyberte typ platná zpráva: **Datagram**, **odpověď**, nebo **žádosti**  

   ![Odeslat zprávu Props](media/connectors-create-api-mq/Send_Msg_Props.png)

1. Po ukončení aplikace logiky, tady je několik ukázkový výstup z **odeslat zprávu** akce:

   ![Msg – výstup](media/connectors-create-api-mq/Send_Msg_Output.png)

## <a name="connector-reference"></a>Referenční informace ke konektorům

Technické podrobnosti o akcích a omezení, které jsou popsány pomocí konektoru OpenAPI (dříve Swagger) popis, přečtěte si tento konektor [referenční stránce](/connectors/mq/).

## <a name="next-steps"></a>Další postup

* Další informace o dalších [konektory Logic Apps](../connectors/apis-list.md)
