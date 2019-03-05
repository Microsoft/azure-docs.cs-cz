---
title: Připojení k MQ server – Azure Logic Apps | Dokumentace Microsoftu
description: Odesílat a přijímat zprávy pomocí Azure nebo místní MQ server a Azure Logic Apps
author: valthom
manager: jeconnoc
ms.author: valthom
ms.date: 06/01/2017
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 59ef41b6d7bfcf8831eaa7d8d7e6af4ea279e415
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2019
ms.locfileid: "57342237"
---
# <a name="connect-to-an-ibm-mq-server-from-logic-apps-using-the-mq-connector"></a>Připojení k IBM MQ serveru z aplikací logiky pomocí konektoru MQ

Microsoft Connector for MQ odešle a načítá zprávy, uloženy v MQ Server místní nebo v Azure. Tento konektor zahrnuje Microsoft MQ klienta, který komunikuje se vzdáleným serverem IBM MQ přes síť TCP/IP. Tento dokument je úvodní příručka k používání konektoru MQ. Doporučujeme že začít tak, že procházení do jedné zprávy do fronty a pak se pokusíte použít dalších akcí.

Konektor MQ zahrnuje následující akce. Neexistují žádné triggery.

- Přejděte do jedné zprávy bez odstranění zprávy ze serveru IBM MQ
- Procházet dávku zpráv bez odstranění zprávy ze serveru IBM MQ
- Do jedné zprávy přijme a odstraní zprávu z IBM MQ Server
- Přijetí dávku zpráv a odstranění zprávy ze serveru IBM MQ
- Odeslat do jedné zprávy na IBM MQ Server

## <a name="prerequisites"></a>Požadavky

* Pokud používáte MQ místnímu serveru [instalace místní brány dat](../logic-apps/logic-apps-gateway-install.md) na serveru ve vaší síti. Pokud je MQ Server veřejně k dispozici, nebo k dispozici v rámci Azure, není brána data gateway používá nebo vyžaduje.

    > [!NOTE]
    > Server, kde je nainstalován On-Premises Data Gateway také musí mít nainstalovaný konektor MQ – funkce rozhraní .NET Framework 4.6.

* Vytvoření prostředku Azure pro místní brána dat – [nastavení připojení brány data](../logic-apps/logic-apps-gateway-connection.md).

* Oficiálně podporované IBM WebSphere MQ verze:
    * MQ 7.5
    * MQ 8.0

## <a name="create-a-logic-app"></a>Vytvoření aplikace logiky

1. V **start pro panel Azure**vyberte **+** (znaménko plus) **Web + mobilní zařízení**a potom **aplikace logiky**.
2. Zadejte **název**, jako je například MQTestApp, **předplatné**, **skupiny prostředků**, a **umístění** (použít tak umístění kde místní Připojení k bráně dat je nakonfigurovaná). Vyberte **připnout na řídicí panel**a vyberte **vytvořit**.  
![Vytvoření aplikace logiky](media/connectors-create-api-mq/Create_Logic_App.png)

## <a name="add-a-trigger"></a>Přidat trigger

> [!NOTE]
> Konektor MQ nemá žádné aktivační události. Tedy použít další trigger spustíte svou aplikaci logiky, jako **opakování** aktivační události.

1. **Návrhář pro Logic Apps** se otevře, vyberte **opakování** v seznamu běžných aktivačních událostí.
2. Vyberte **upravit** v rámci opakovaného triggeru.
3. Nastavte **frekvence** k **den**a nastavte **Interval** k **7**.

## <a name="browse-a-single-message"></a>Přejděte do jedné zprávy
1. Vyberte **+ nový krok**a vyberte **přidat akci**.
2. Do vyhledávacího pole zadejte `mq`a pak vyberte **MQ – procházení zpráv**.  
![Procházení zpráv](media/connectors-create-api-mq/Browse_message.png)

3. Pokud není k dispozici připojení k existující Magic Quadrant, vytvořte připojení:  

    1. Vyberte **připojit přes místní bránu dat**a zadejte vlastnosti MQ server.  
    Pro **Server**, můžete zadat název serveru Magic Quadrant, nebo zadejte IP adresu, za nímž následuje dvojtečka a číslo portu.
    2. **Brány** rozevírací seznam obsahuje všechna existující připojení brány, které jsou nakonfigurované. Vyberte bránu.
    3. Po dokončení vyberte **Vytvořit**. Připojení k vypadá nějak takto:  
    ![Vlastnosti připojení](media/connectors-create-api-mq/Connection_Properties.png)

4. Ve vlastnostech akce můžete:  

    * Použití **fronty** vlastnosti pro přístup k názvu jinou frontu než definovaná v připojení
    * Použití **MessageId**, **CorrelationId**, **GroupId**a další vlastnosti a vyhledejte zprávu na základě různých vlastností zprávu MQ
    * Nastavte **IncludeInfo** k **True** zahrnout informace o dalších zpráv ve výstupu. Nebo ji nastavte na **False** tak, aby nezahrnovala informace o dalších zpráv ve výstupu.
    * Zadejte **vypršení časového limitu** hodnotu k určení jak dlouho čekat zprávy dorazí v prázdné frontě. Pokud je zadán nic, se načte první zprávu ve frontě a neexistuje žádný čas strávený čekáním zprávu zobrazit.  
    ![Procházet vlastnosti zprávy](media/connectors-create-api-mq/Browse_message_Props.png)

5. **Uložit** změny a potom **spustit** svou aplikaci logiky:  
![Uložit a spustit](media/connectors-create-api-mq/Save_Run.png)

6. Za několik sekund jsou uvedeny kroky spuštění a můžete se podívat na výstupu. Vyberte zelenou značku zaškrtnutí zobrazíte podrobnosti o jednotlivých kroků. Vyberte **zobrazit nezpracované výstupy** zobrazíte další podrobnosti na výstupní data.  
![Procházet výstup zpráv](media/connectors-create-api-mq/Browse_message_output.png)  

    Nezpracovaného výstupu:  
    ![Procházení zpráv nezpracovaného výstupu](media/connectors-create-api-mq/Browse_message_raw_output.png)

7. Když **IncludeInfo** je možnost nastavená na hodnotu true, se zobrazí následující výstup:  
![Procházet zpráva obsahovat informace o](media/connectors-create-api-mq/Browse_message_Include_Info.png)

## <a name="browse-multiple-messages"></a>Procházet více zpráv
**Procházet zprávy** obsahuje akce **BatchSize** možnost určit, kolik zpráv má být vrácen z fronty.  Pokud **BatchSize** nemá žádný záznam se vrátí všechny zprávy. Vrácené výstupem je pole zpráv.

1. Při přidávání **procházet zprávy** je standardně vybraná akce, první připojení, který je nakonfigurovaný. Vyberte **změnit připojení** a vytvořte nové připojení, nebo vyberte jiné připojení.

2. Výstup z procházení zprávy zobrazí:  
![Procházet výstupní zprávy](media/connectors-create-api-mq/Browse_messages_output.png)

## <a name="receive-a-single-message"></a>Zobrazí se jedné zpráva
**Přijímání zpráv** akce se stejnými vstupy a výstupy jako **procházet zpráva** akce. Při použití **přijímání zpráv**, odstraní zprávu z fronty.

## <a name="receive-multiple-messages"></a>Přijmout více zpráv
**Přijímat zprávy** akce se stejnými vstupy a výstupy jako **procházet zprávy** akce. Při použití **přijímat zprávy**, odstraní se zprávy z fronty.

Pokud nejsou žádné zprávy ve frontě, při provádění prohlížení nebo příjmu, selže s následující výstup:  
![Chyba č MQ Message](media/connectors-create-api-mq/MQ_No_Msg_Error.png)

## <a name="send-a-message"></a>Odeslat zprávu
1. Při přidávání **odeslat zprávu** je standardně vybraná akce, první připojení, který je nakonfigurovaný. Vyberte **změnit připojení** a vytvořte nové připojení, nebo vyberte jiné připojení. Platnými **typy zpráv** jsou **Datagram**, **odpověď**, nebo **požádat o**.  
![Odeslat zprávu Props](media/connectors-create-api-mq/Send_Msg_Props.png)

2. Výstup odesílání zprávy by měl vypadat nějak takto:  
![Msg – výstup](media/connectors-create-api-mq/Send_Msg_Output.png)

## <a name="connector-specific-details"></a>Podrobné informace specifické pro konektor

Zobrazit všechny aktivační události a akce definované ve swaggeru a také zjistit žádné omezení [podrobnosti o konektoru](/connectors/mq/).

## <a name="next-steps"></a>Další postup
[Vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Prozkoumejte další dostupné konektory v Logic Apps na naše [rozhraní API seznamu](apis-list.md).
