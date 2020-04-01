---
title: Připojení k serveru IBM MQ
description: Odesílání a načítání zpráv pomocí serveru IBM MQ azure nebo místního serveru IBM MQ a aplikací Azure Logic Apps
services: logic-apps
ms.suite: integration
author: ChristopherHouser
ms.author: chrishou
ms.reviewer: valthom, logicappspm
ms.topic: article
ms.date: 03/31/2020
tags: connectors
ms.openlocfilehash: 737c5b90b216156ca08346f4a64fd0b421ad6c19
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80410276"
---
# <a name="connect-to-an-ibm-mq-server-from-azure-logic-apps"></a>Připojení k serveru IBM MQ z Aplikací Azure Logic Apps

Konektor IBM MQ odesílá a načítá zprávy uložené na serveru IBM MQ místně nebo v Azure. Tento konektor zahrnuje klienta Microsoft MQ, který komunikuje se vzdáleným serverem IBM MQ v síti TCP/IP. Tento článek obsahuje průvodce startérem pro použití konektoru MQ. Můžete začít procházením jedné zprávy ve frontě a pak zkusit jiné akce.

Konektor IBM MQ obsahuje tyto akce, ale neposkytuje žádné aktivační události:

- Projděte jednu zprávu bez odstranění zprávy ze serveru IBM MQ.
- Procházet dávku zpráv bez odstranění zpráv ze serveru IBM MQ.
- Obdržíte jednu zprávu a odstraňte zprávu ze serveru IBM MQ.
- Přijímat dávky zpráv a odstranit zprávy ze serveru IBM MQ.
- Odešlete jednu zprávu na server IBM MQ.

Zde jsou oficiálně podporované verze IBM WebSphere MQ:

  * MQ 7,5
  * MQ 8.0
  * MQ 9.0

## <a name="prerequisites"></a>Požadavky

* Pokud používáte místní server MQ, [nainstalujte místní bránu dat](../logic-apps/logic-apps-gateway-install.md) na server v rámci sítě. Server, na kterém je nainstalována místní brána dat, musí mít také nainstalovanou rozhraní .NET Framework 4.6, aby konektor MQ fungoval.

  Po dokončení instalace brány, musíte také vytvořit prostředek v Azure pro místní brány dat. Další informace naleznete [v tématu Nastavení připojení brány dat](../logic-apps/logic-apps-gateway-connection.md).

  Pokud je váš server MQ veřejně dostupný nebo dostupný v rámci Azure, nemusíte používat bránu dat.

* Aplikace logiky, do které chcete přidat akci MQ. Tato aplikace logiky musí používat stejné umístění jako vaše místní připojení brány dat a musí již mít aktivační událost, která spustí váš pracovní postup.

  Konektor MQ nemá žádné aktivační události, takže je nutné nejprve přidat aktivační událost do aplikace logiky. Můžete například použít aktivační událost opakování. Pokud s aplikacemi logiky teprve začínáte, zkuste tento [rychlý start vytvořit první aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="create-connection"></a>

## <a name="create-mq-connection"></a>Vytvořit připojení MQ

Pokud při přidání akce MQ ještě nemáte připojení MQ, budete vyzváni k vytvoření připojení, například:

![Poskytnutí informací o připojení](media/connectors-create-api-mq/connection-properties.png)

1. Pokud se připojujete k místnímu serveru MQ, vyberte **Připojit přes místní bránu dat**.

1. Zadejte informace o připojení serveru MQ.

   * V **části Server**můžete zadat název serveru MQ nebo adresu IP následovanou dvojtečkou a číslem portu.

   * Chcete-li použít ssl (SSL), vyberte **Povolit protokol SSL?**.

     Konektor MQ aktuálně podporuje pouze ověřování serveru, nikoli ověřování klienta. Další informace naleznete v tématu [Problémy s připojením a ověřováním](#connection-problems).

1. V části **brána** postupujte takto:

   1. Ze seznamu **Předplatných** vyberte předplatné Azure přidružené k prostředku brány Azure.

   1. Ze seznamu **Brána připojení** vyberte prostředek brány Azure, který chcete použít.

1. Až to budete mít, vyberte **Vytvořit**.

<a name="connection-problems"></a>

### <a name="connection-and-authentication-problems"></a>Problémy s připojením a ověřováním

Když se aplikace logiky pokusí připojit k místnímu serveru MQ, může se zobrazit tato chyba:

`"MQ: Could not Connect the Queue Manager '<queue-manager-name>': The Server was expecting an SSL connection."`

* Pokud používáte konektor MQ přímo v Azure, server MQ musí použít certifikát vydaný důvěryhodnou [certifikační autoritou](https://www.ssl.com/faqs/what-is-a-certificate-authority/).

* Pokud používáte místní bránu dat, zkuste použít certifikát, který je vydán důvěryhodnou [certifikační autoritou,](https://www.ssl.com/faqs/what-is-a-certificate-authority/) pokud je to možné. Pokud však tato možnost není možná, můžete použít certifikát podepsaný svým držitelem, který není vydán důvěryhodnou [certifikační autoritou](https://www.ssl.com/faqs/what-is-a-certificate-authority/) a je považován za méně zabezpečený.

  Chcete-li nainstalovat certifikát podepsaný svým držitelem, můžete použít nástroj **Správce certifikace systému Windows** (certmgr.msc). V tomto scénáři je třeba v místním počítači, kde je spuštěna místní služba brány dat, nainstalovat certifikát do úložiště certifikátů **místního počítače** na úrovni **důvěryhodných kořenových certifikačních úřadů.**

  1. V počítači, ve kterém je spuštěna služba místní brány dat, otevřete nabídku Start, najděte a vyberte **Spravovat uživatelské certifikáty**.

  1. Po otevření nástroje Správce certifikace systému Windows přejděte do složky **Certifikáty – důvěryhodné** >  **kořenové certifikační úřady** a certifikát nainstalujte.

     > [!IMPORTANT]
     > Ujistěte se, že jste nainstalovali certifikát v úložišti **Certifikáty – místní počítač** > **důvěryhodné kořenové certifikační úřady.**

* Server MQ vyžaduje definování specifikace šifry, kterou chcete použít pro připojení SSL. SsLStream v rozhraní .NET však neumožňuje zadat pořadí pro specifikace šifry. Chcete-li toto omezení obejít, můžete změnit konfiguraci serveru MQ tak, aby odpovídala první specifikaci šifry v sadě, kterou konektor odesílá v vyjednávání SSL.

  Při pokusu o připojení server MQ zaznamená zprávu o události, která označuje, že připojení se nezdařilo, protože druhý konec použil nesprávnou specifikaci šifry. Zpráva o události obsahuje specifikaci šifry, která se zobrazí jako první v seznamu. Aktualizujte specifikaci šifry v konfiguraci kanálu tak, aby odpovídala specifikaci šifry ve zprávě o události.

## <a name="browse-single-message"></a>Procházet jednu zprávu

1. V aplikaci logiky vyberte pod aktivační událostí nebo jinou akcí **nový krok**.

1. Do vyhledávacího pole `mq`zadejte a vyberte akci **Procházet zprávu.**

   ![Vybrat akci "Procházet zprávu"](media/connectors-create-api-mq/browse-message.png)

1. Pokud jste ještě nevytvořili připojení MQ, budete vyzváni k [vytvoření tohoto připojení](#create-connection).

1. Po vytvoření připojení nastavte vlastnosti akce **Procházet zprávu:**

   | Vlastnost | Popis |
   |----------|-------------|
   | **Fronta** | Pokud se liší od fronty zadané v připojení, zadejte, že fronta. |
   | **MessageId**, **CorrelationId**, **GroupId**a další vlastnosti | Vyhledejte zprávu založenou na různých vlastnostech zprávy MQ |
   | **IncludeInfo** | Chcete-li do výstupu zahrnout další informace o zprávě, vyberte **možnost true**. Chcete-li ve výstupu vynechat další informace o zprávě, vyberte **možnost false**. |
   | **Časový limit** | Zadejte hodnotu, která určí, jak dlouho má čekat, až zpráva dorazí do prázdné fronty. Pokud není nic zadáno, je načtena první zpráva ve frontě a není čas strávený čekáním na zobrazení zprávy. |
   |||

   Například:

   ![Vlastnosti akce "Procházet zprávu"](media/connectors-create-api-mq/browse-message-properties.png)

1. Až budete hotovi, na panelu nástrojů návrháře vyberte **Uložit**. Pokud chcete aplikaci otestovat, vyberte **Spustit**.

   Po dokončení spuštění návrhář zobrazí kroky pracovního postupu a jejich stav, takže můžete zkontrolovat výstup.

1. Chcete-li zobrazit podrobnosti o jednotlivých krocích, klikněte na záhlaví kroku. Chcete-li zkontrolovat další informace o výstupu kroku, vyberte **zobrazit nezpracované výstupy**.

   ![Procházet výstup zprávy](media/connectors-create-api-mq/browse-message-output.png)

   Zde je několik ukázkových nezpracovaných výstupů:

   ![Procházet nezpracovaný výstup zprávy](media/connectors-create-api-mq/browse-message-raw-output.png)

1. Pokud nastavíte **IncludeInfo** na **hodnotu true**, zobrazí se další výstup:

   ![Procházet zprávy obsahují informace](media/connectors-create-api-mq/browse-message-include-info.png)

## <a name="browse-multiple-messages"></a>Procházení více zpráv

Akce **Procházet zprávy** obsahuje **možnost BatchSize,** která označuje, kolik zpráv se má vrátit z fronty. Pokud **BatchSize** nemá žádnou hodnotu, jsou vráceny všechny zprávy. Vrácený výstup je pole zpráv.

1. Postupujte podle předchozích kroků, ale místo toho přidejte akci **Procházet zprávy.**

1. Pokud jste ještě nevytvořili připojení MQ, budete vyzváni k [vytvoření tohoto připojení](#create-connection). V opačném případě se ve výchozím nastavení použije první dříve nakonfigurované připojení. Chcete-li vytvořit nové připojení, vyberte **možnost Změnit připojení**. Nebo vyberte jiné připojení.

1. Zadejte informace pro akci.

1. Uložte a spusťte aplikaci logiky.

   Po dokončení spuštění aplikace logiky, tady je nějaký ukázkový výstup z **akce Procházet zprávy:**

   ![Ukázkový výstup "Procházet zprávy"](media/connectors-create-api-mq/browse-messages-output.png)

## <a name="receive-single-message"></a>Přijímat jednu zprávu

Akce **Přijímat zprávy** má stejné vstupy a výstupy jako akce **Procházet zprávu.** Při použití **přijmout zprávu**, zpráva je odstraněna z fronty.

## <a name="receive-multiple-messages"></a>Příjem více zpráv

Akce **Přijímat zprávy** má stejné vstupy a výstupy jako akce **Procházet zprávy.** Při použití **přijímat zprávy**jsou zprávy odstraněny z fronty.

> [!NOTE]
> Při spuštění procházet nebo přijímat akce ve frontě, která nemá žádné zprávy, akce se nezdaří s tímto výstupem:
>
> ![Chyba MQ "žádná zpráva"](media/connectors-create-api-mq/mq-no-message-error.png)

## <a name="send-message"></a>Odeslat zprávu

1. Postupujte podle předchozích kroků, ale místo toho přidejte akci **Odeslat zprávu.**

1. Pokud jste ještě nevytvořili připojení MQ, budete vyzváni k [vytvoření tohoto připojení](#create-connection). V opačném případě se ve výchozím nastavení použije první dříve nakonfigurované připojení. Chcete-li vytvořit nové připojení, vyberte **možnost Změnit připojení**. Nebo vyberte jiné připojení.

1. Zadejte informace pro akci. V **případě MessageType**vyberte platný typ zprávy: **Datagram**, **Reply**nebo **Request**

   ![Vlastnosti pro "Odeslat akci zprávy"](media/connectors-create-api-mq/send-message-properties.png)

1. Uložte a spusťte aplikaci logiky.

   Po dokončení spuštění aplikace logiky, tady je nějaký ukázkový výstup z akce **Odeslat zprávu:**

   ![Ukázkový výstup "Odeslat zprávu"](media/connectors-create-api-mq/send-message-output.png)

## <a name="connector-reference"></a>Referenční informace ke konektorům

Technické podrobnosti o akcích a omezeních, které jsou popsány v popisu swagger konektoru, zkontrolujte [referenční stránku konektoru](/connectors/mq/).

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech logic apps](../connectors/apis-list.md)
