---
title: Připojení k serveru MQ - Azure Logic Apps | Microsoft Docs
description: Odesílat a přijímat zprávy pomocí služby Azure nebo na místním serveru MQ a Azure Logic Apps
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
ms.openlocfilehash: 6b34bd7b286ca3b206c611343217c90e0d57fbfb
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35295906"
---
# <a name="connect-to-an-ibm-mq-server-from-logic-apps-using-the-mq-connector"></a>Připojit k serveru IBM MQ z aplikace logic apps pomocí konektoru MQ 

Microsoft Connector pro MQ odešle a načítá zprávy o uložená v MQ Server místní nebo v Azure. Tento konektor zahrnuje Microsoft MQ klienta, který komunikuje se vzdáleným serverem IBM MQ přes síť TCP/IP. Tento dokument je úvodní příručka k používání konektoru MQ. Doporučujeme že začněte tím, že procházení do jedné zprávy ve frontě a potom zkusit dalších akcí.    

Konektor MQ zahrnuje následující akce. Neexistují žádné aktivační události.

-   Přejděte do jedné zprávy bez odstranění zprávy ze serveru IBM MQ
-   Procházet dávku zpráv bez odstranění zprávy ze serveru IBM MQ
-   Přijme do jedné zprávy a odstraní zprávu ze serveru IBM MQ
-   Přijetí dávku zpráv a odstranění zprávy ze serveru IBM MQ
-   Odesílání do jedné zprávy na IBM MQ Server 

## <a name="prerequisites"></a>Požadavky

* Pokud používáte MQ místnímu serveru [nainstalovat bránu dat místní](../logic-apps/logic-apps-gateway-install.md) na serveru v rámci vaší sítě. Pokud je Server MQ veřejně, nebo dostupné v rámci Azure, není bránu dat použít nebo vyžaduje.

    > [!NOTE]
    > Serveru s nainstalovanou bránu dat On-Premises musí také mít rozhraní .net Framework 4.6 nainstalovaný konektor MQ funkce.

* Vytvoření prostředku Azure pro bránu dat místní - [nastavení připojení brány dat](../logic-apps/logic-apps-gateway-connection.md).

* Oficiálně podporované IBM WebSphere MQ verze:
   * MQ 7.5
   * MQ 8.0

## <a name="create-a-logic-app"></a>Vytvoření aplikace logiky

1. V **Tabule start Azure**, vyberte **+** (znaménko plus), **Web + mobilní**a potom **aplikace logiky**. 
2. Zadejte **název**, jako je například MQTestApp, **předplatné**, **skupiny prostředků**, a **umístění** (použijte umístění, kde je nakonfigurované připojení k místní brána Data Gateway). Vyberte **připnout na řídicí panel**a vyberte **vytvořit**.  
![Vytvoření aplikace logiky](media/connectors-create-api-mq/Create_Logic_App.png)

## <a name="add-a-trigger"></a>Přidat trigger

> [!NOTE]
> Konektor MQ nemá žádné aktivační události. Ano, používat jiný aktivační událost spusťte aplikaci logiky, jako **opakování** aktivační události. 

1. **Logiku aplikace Návrhář** otevře, vyberte **opakování** v seznamu běžných aktivačních událostí.
2. Vyberte **upravit** v rámci opakování aktivační událost. 
3. Nastavte **frekvence** k **den**a nastavte **Interval** k **7**. 

## <a name="browse-a-single-message"></a>Přejděte do jedné zprávy
1. Vyberte **+ nový krok**a vyberte **přidat akci**.
2. Do vyhledávacího pole zadejte `mq`a potom vyberte **MQ - procházet zpráva**.  
![Procházet zpráv](media/connectors-create-api-mq/Browse_message.png)

3. Pokud není k dispozici připojení k existující MQ, vytvořte připojení:  

    1. Vyberte **připojit prostřednictvím místní brána dat**a zadejte vlastnosti serveru MQ.  
    Pro **Server**, můžete zadat název serveru MQ nebo zadejte IP adresu následovaný dvojtečkou a číslo portu. 
    2. **Brány** rozevírací seznam obsahuje seznam všech existujících připojení brány, které byly nakonfigurovány. Vyberte bránu.
    3. Po dokončení vyberte **Vytvořit**. Připojení vypadá podobně jako následující:   
    ![Vlastnosti připojení](media/connectors-create-api-mq/Connection_Properties.png)

4. Ve vlastnostech akce můžete:  

    * Použití **fronty** vlastnost, která fronty jiný název než co je definována v připojení
    * Použití **MessageId**, **CorrelationId**, **GroupId**a dalších vlastností a vyhledejte zprávu založenou na jiné vlastnosti zprávy MQ
    * Nastavit **IncludeInfo** k **True** zahrnout informace o dalších zpráv ve výstupu. Nebo ji nastavte na **False** tak, aby neobsahoval informace o dalších zpráv ve výstupu.
    * Zadejte **časový limit** hodnotu určete, jak dlouho čekat na zprávu, která přicházejí v prázdné frontě. Pokud je zadán nic, se načte první zprávu ve frontě a neexistuje žádný doby čekání na zprávu, která se zobrazí.  
    ![Procházet vlastnosti zprávy](media/connectors-create-api-mq/Browse_message_Props.png)

5. **Uložit** změny a potom **spustit** svou aplikaci logiky:  
![Uložte a spusťte](media/connectors-create-api-mq/Save_Run.png)

6. Za několik sekund jsou uvedeny kroky spuštění, a můžete se podívat na výstup. Vyberte na zelenou značku zaškrtnutí zobrazíte podrobnosti o jednotlivých kroků. Vyberte **najdete v části nezpracovaná výstupy** zobrazíte další podrobnosti na výstupní data.  
![Procházet výstup zpráv](media/connectors-create-api-mq/Browse_message_output.png)  

    Nezpracovaná výstup:  
    ![Procházet nezpracovaná výstup zpráv](media/connectors-create-api-mq/Browse_message_raw_output.png)

7. Když **IncludeInfo** je možnost nastavena na hodnotu true, se zobrazí následující výstup:  
![Procházet zpráva obsahovat informace o](media/connectors-create-api-mq/Browse_message_Include_Info.png)

## <a name="browse-multiple-messages"></a>Procházet více zpráv
**Procházet zprávy** zahrnuje akce **BatchSize** možnost indikující, kolik zpráv má být vrácen z fronty.  Pokud **BatchSize** nemá žádný záznam, jsou vráceny všechny zprávy. Vrácený výstupem je pole zpráv.

1. Při přidávání **procházet zprávy** je standardně vybraná akce, první připojení, který je nakonfigurovaný. Vyberte **změnit připojení** vytvořte nové připojení, nebo vyberte jiné připojení.

2. Výstup procházet zprávy zobrazí:  
![Procházet výstupní zprávy](media/connectors-create-api-mq/Browse_messages_output.png)

## <a name="receive-a-single-message"></a>Přijímat do jedné zprávy
**Příjmu zpráv** akce se stejnými vstupy a výstupy jako **zpráva Procházet** akce. Při použití **příjmu zpráv**, odstraní zprávu z fronty.

## <a name="receive-multiple-messages"></a>Zobrazí více zpráv
**Přijímat zprávy** akce se stejnými vstupy a výstupy jako **procházet zprávy** akce. Při použití **přijímat zprávy**, zprávy jsou odstraněny z fronty.

Pokud nejsou žádné zprávy ve frontě, když procházet nebo příjmu, selže s následující výstup:  
![Ne MQ zprávy chyby](media/connectors-create-api-mq/MQ_No_Msg_Error.png)

## <a name="send-a-message"></a>Odeslat zprávu
1. Při přidávání **odesílání zpráv** ve výchozím nastavení je vybrané akce, první připojení, který je nakonfigurovaný. Vyberte **změnit připojení** vytvořte nové připojení, nebo vyberte jiné připojení. Platném **typy zpráv** jsou **Datagram**, **odpověď**, nebo **požadavku**.  
![Odeslat Msg Props](media/connectors-create-api-mq/Send_Msg_Props.png)

2. Výstup odeslat zpráva vypadá takto:  
![Odeslání výstupu Msg](media/connectors-create-api-mq/Send_Msg_Output.png)

## <a name="connector-specific-details"></a>Podrobnosti o konkrétní konektor

Zobrazit všechny aktivační události a akce definované v swagger a také zobrazit žádné limity v [connector – podrobnosti](/connectors/mq/).

## <a name="next-steps"></a>Další postup
[Vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Prozkoumejte dalších dostupných konektorů v Logic Apps v našem [rozhraní API seznamu](apis-list.md).
