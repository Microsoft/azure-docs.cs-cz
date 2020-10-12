---
title: Připojit k serveru IBM MQ
description: Posílání a načítání zpráv pomocí Azure nebo místního serveru IBM MQ a Azure Logic Apps
services: logic-apps
ms.suite: integration
author: ChristopherHouser
ms.author: chrishou
ms.reviewer: valthom, estfan, logicappspm
ms.topic: article
ms.date: 05/14/2020
tags: connectors
ms.openlocfilehash: e9e554fdc092e49f5a87049de0e3dc3163105f58
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85609499"
---
# <a name="connect-to-an-ibm-mq-server-from-azure-logic-apps"></a>Připojení k serveru IBM MQ z Azure Logic Apps

Konektor IBM MQ odesílá a načítá zprávy uložené na serveru IBM MQ místně nebo v Azure. Tento konektor zahrnuje klienta Microsoft MQ, který komunikuje se vzdáleným serverem IBM MQ napříč sítí TCP/IP. Tento článek poskytuje Úvodní příručku k používání konektoru MQ. Můžete začít procházením jedné zprávy ve frontě a následným pokusem o další akce.

Konektor IBM MQ zahrnuje tyto akce, ale neposkytuje žádné triggery:

- Procházejte jednu zprávu bez odstranění zprávy ze serveru IBM MQ.
- Projděte si dávku zpráv, aniž byste museli odstraňovat zprávy ze serveru IBM MQ.
- Dostanou jednu zprávu a odstraní zprávu ze serveru IBM MQ.
- Obdrží dávku zpráv a odstraní zprávy ze serveru IBM MQ.
- Odešlete jednu zprávu na server IBM MQ.

Tady jsou oficiálně podporované verze IBM WebSphere MQ:

  * MQ 7,5
  * MQ 8,0
  * MQ 9,0
  * MQ 9,1

## <a name="prerequisites"></a>Požadavky

* Pokud používáte místní server MQ, [nainstalujte místní bránu dat](../logic-apps/logic-apps-gateway-install.md) na server v rámci vaší sítě. Server, na kterém je nainstalovaná místní brána dat, musí mít nainstalovanou .NET Framework 4,6, aby mohl konektor MQ fungovat.

  Po dokončení instalace brány musíte také vytvořit prostředek v Azure pro místní bránu dat. Další informace najdete v tématu [nastavení připojení pro bránu dat](../logic-apps/logic-apps-gateway-connection.md).

  Pokud je váš server MQ veřejně dostupný nebo dostupný v rámci Azure, nemusíte používat bránu dat.

* Aplikace logiky, do které chcete přidat akci MQ. Tato aplikace logiky musí používat stejné umístění jako místní připojení brány dat a musí mít Trigger, který spouští váš pracovní postup.

  Konektor MQ nemá žádné triggery, takže musíte nejdřív přidat Trigger do aplikace logiky. Můžete například použít Trigger opakování. Pokud s Logic Apps začínáte, vyzkoušejte si tento [rychlý Start, abyste vytvořili svoji první aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="create-connection"></a>

## <a name="create-mq-connection"></a>Vytvořit připojení MQ

Pokud ještě nemáte připojení MQ, když přidáte akci MQ, budete vyzváni k vytvoření připojení, například:

![Zadání informací o připojení](media/connectors-create-api-mq/connection-properties.png)

1. Pokud se připojujete k místnímu serveru MQ, vyberte **připojit přes místní bránu dat**.

1. Zadejte informace o připojení pro váš server MQ.

   * Pro **Server**můžete zadat název serveru MQ nebo zadat IP adresu následovaný dvojtečkou a číslem portu.

   * Pokud chcete použít SSL (Secure Sockets Layer) (SSL), vyberte **Povolit SSL?**.

     Konektor MQ aktuálně podporuje pouze ověřování serveru, nikoli ověřování klientů. Další informace najdete v tématu [problémy s připojením a ověřováním](#connection-problems).

1. V části **Brána** proveďte tyto kroky:

   1. V seznamu **předplatné** vyberte předplatné Azure přidružené k vašemu prostředku brány Azure.

   1. V seznamu **Brána připojení** vyberte prostředek brány Azure, který chcete použít.

1. Po dokončení vyberte **Vytvořit**.

<a name="connection-problems"></a>

### <a name="connection-and-authentication-problems"></a>Problémy s připojením a ověřováním

Když se aplikace logiky pokusí připojit k místnímu serveru MQ, může se zobrazit tato chyba:

`"MQ: Could not Connect the Queue Manager '<queue-manager-name>': The Server was expecting an SSL connection."`

* Pokud používáte konektor MQ přímo v Azure, musí server MQ používat certifikát vydaný důvěryhodnou [certifikační autoritou](https://www.ssl.com/faqs/what-is-a-certificate-authority/).

* Pokud používáte místní bránu dat, zkuste použít certifikát vydaný důvěryhodnou [certifikační autoritou](https://www.ssl.com/faqs/what-is-a-certificate-authority/) , pokud je to možné. Pokud však tato možnost není možná, můžete použít certifikát podepsaný svým držitelem, který není vydán důvěryhodnou [certifikační autoritou](https://www.ssl.com/faqs/what-is-a-certificate-authority/) a který je považován za méně bezpečný.

  Chcete-li nainstalovat certifikát podepsaný svým držitelem, můžete použít nástroj **správce certifikace systému Windows** (certmgr. msc). V tomto scénáři je potřeba nainstalovat certifikát do úložiště certifikátů **místního počítače** na úrovni **důvěryhodných kořenových certifikačních autorit** v místním počítači, na kterém je spuštěná služba místní brány dat.

  1. V počítači, kde je spuštěna služba místní brána dat, otevřete nabídku Start, vyhledejte a vyberte možnost **Spravovat uživatelské certifikáty**.

  1. Po otevření nástroje Správce certifikace systému Windows přejdete do složky **certifikáty – místní počítač**  >   **Důvěryhodné kořenové certifikační autority** a certifikát nainstalujete.

     > [!IMPORTANT]
     > Ujistěte se, že jste nainstalovali certifikát do **Certificates - Local Computer**  >  úložiště**Důvěryhodné kořenové certifikační autority** počítače místní počítač.

* Server MQ vyžaduje, abyste definovali specifikaci šifrování, kterou chcete použít pro připojení TLS/SSL. SslStream v rozhraní .NET ale neumožňuje zadat pořadí pro specifikace šifry. Pokud chcete toto omezení obejít, můžete změnit konfiguraci serveru MQ tak, aby odpovídala první specifikaci šifry v sadě, kterou konektor odesílá při vyjednávání TLS/SSL.

  Když se pokusíte o připojení, server MQ zaznamená zprávu události, která indikuje, že připojení selhalo, protože druhý konec použil nesprávnou specifikaci šifry. Zpráva o události obsahuje specifikace šifry, která se v seznamu zobrazuje jako první. Aktualizujte specifikaci šifry v konfiguraci kanálu tak, aby odpovídala specifikaci šifry ve zprávě události.

## <a name="browse-single-message"></a>Procházet jednu zprávu

1. V aplikaci logiky pod triggerem nebo jinou akcí vyberte **Nový krok**.

1. Do vyhledávacího pole zadejte `mq` a vyberte akci **Procházet zprávu** .

   ![Vybrat akci procházet zprávu](media/connectors-create-api-mq/browse-message.png)

1. Pokud jste ještě nevytvořili připojení MQ, budete vyzváni k [vytvoření tohoto připojení](#create-connection).

1. Po vytvoření připojení nastavte vlastnosti akce **zprávy procházet** :

   | Vlastnost | Popis |
   |----------|-------------|
   | **Fronta** | Pokud se liší od fronty zadané v rámci připojení, zadejte tuto frontu. |
   | **MessageID**, **ID korelace**, **identifikátor skupiny**a další vlastnosti | Vyhledat zprávu, která je založena na různých vlastnostech zprávy MQ |
   | **IncludeInfo** | Pokud chcete do výstupu zahrnout další informace o zprávě, vyberte **true**. Pokud chcete ve výstupu vynechat další informace o zprávě, vyberte **false (NEPRAVDA**). |
   | **Prodlev** | Zadejte hodnotu, která určuje, jak dlouho se má čekat na doručení zprávy do prázdné fronty. Pokud nezadáte žádnou hodnotu, načte se první zpráva ve frontě a nestrávená doba čekání na zobrazení zprávy. |
   |||

   Například:

   ![Vlastnosti pro akci procházet zprávu](media/connectors-create-api-mq/browse-message-properties.png)

1. Až budete hotovi, na panelu nástrojů návrháře vyberte **Uložit**. Pokud chcete aplikaci otestovat, vyberte **Spustit**.

   Po dokončení běhu Návrhář zobrazí kroky pracovního postupu a jejich stav, abyste mohli zkontrolovat výstup.

1. Chcete-li zobrazit podrobnosti o jednotlivých krocích, klikněte na záhlaví kroku. Chcete-li zobrazit další informace o výstupu kroku, vyberte možnost **Zobrazit nezpracované výstupy**.

   ![Procházet výstup zprávy](media/connectors-create-api-mq/browse-message-output.png)

   Zde je ukázkový nezpracovaný výstup:

   ![Procházet nezpracovaný výstup zprávy](media/connectors-create-api-mq/browse-message-raw-output.png)

1. Pokud nastavíte **IncludeInfo** na **hodnotu true**, zobrazí se další výstup:

   ![Procházet informace o zahrnutí zprávy](media/connectors-create-api-mq/browse-message-include-info.png)

## <a name="browse-multiple-messages"></a>Procházet více zpráv

Akce **Procházet zprávy** obsahuje možnost **BatchSize** , která určuje, kolik zpráv se má vrátit z fronty. Pokud **BatchSize** nemá žádnou hodnotu, vrátí se všechny zprávy. Vrácený výstup je pole zpráv.

1. Postupujte podle předchozích kroků, ale místo toho přidejte akci **Procházet zprávy** .

1. Pokud jste ještě nevytvořili připojení MQ, budete vyzváni k [vytvoření tohoto připojení](#create-connection). V opačném případě se ve výchozím nastavení použije první dřív nakonfigurované připojení. Chcete-li vytvořit nové připojení, vyberte možnost **změnit připojení**. Případně vyberte jiné připojení.

1. Zadejte informace pro akci.

1. Uložte a spusťte aplikaci logiky.

   Až se aplikace logiky dokončí, tady je ukázkový výstup z akce **Procházet zprávy** :

   ![Ukázka výstupu "Procházet zprávy"](media/connectors-create-api-mq/browse-messages-output.png)

## <a name="receive-single-message"></a>Přijmout jednu zprávu

Akce **přijmout zprávu** má stejné vstupy a výstupy jako akce **procházení zprávy** . Když použijete **zprávu přijmout**, zpráva se odstraní z fronty.

## <a name="receive-multiple-messages"></a>Přijímání více zpráv

Akce **přijmout zprávy** má stejné vstupy a výstupy jako akce **Procházet zprávy** . Když použijete **přijímat zprávy**, zprávy se odstraní z fronty.

> [!NOTE]
> Když spustíte akci procházet nebo přijmout ve frontě, která nemá žádné zprávy, akce s tímto výstupem se nezdařila:
>
> ![Chyba typu MQ "žádná zpráva"](media/connectors-create-api-mq/mq-no-message-error.png)

## <a name="send-message"></a>Odeslat zprávu

1. Postupujte podle předchozích kroků, ale místo toho přidejte akci **Odeslat zprávu** .

1. Pokud jste ještě nevytvořili připojení MQ, budete vyzváni k [vytvoření tohoto připojení](#create-connection). V opačném případě se ve výchozím nastavení použije první dřív nakonfigurované připojení. Chcete-li vytvořit nové připojení, vyberte možnost **změnit připojení**. Případně vyberte jiné připojení.

1. Zadejte informace pro akci. V případě **MessageType**Vyberte platný typ zprávy: **datagram**, **Reply**nebo **Request** .

   ![Vlastnosti pro akci odeslání zprávy](media/connectors-create-api-mq/send-message-properties.png)

1. Uložte a spusťte aplikaci logiky.

   Až se aplikace logiky dokončí, tady je ukázkový výstup z akce **Odeslat zprávu** :

   ![Ukázka výstupu "Odeslat zprávu"](media/connectors-create-api-mq/send-message-output.png)

## <a name="connector-reference"></a>Referenční informace ke konektorům

Technické podrobnosti o akcích a omezeních, které jsou popsány v popisu Swagger v konektoru, najdete na [referenční stránce](/connectors/mq/)konektoru.

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech Logic Apps](../connectors/apis-list.md)
