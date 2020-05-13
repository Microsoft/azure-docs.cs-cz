---
title: Průvodce odstraňováním potíží – Azure Event Hubs | Microsoft Docs
description: Tento článek poskytuje seznam výjimek zasílání zpráv Azure Event Hubs a navrhovaných akcí.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 01/16/2020
ms.author: shvija
ms.openlocfilehash: ab3cbdf938409f4eacffa10ae5cb20f8c36b5856
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/12/2020
ms.locfileid: "83124665"
---
# <a name="azure-event-hubs---troubleshooting-guide"></a>Azure Event Hubs – Průvodce odstraňováním potíží
V tomto článku najdete tipy a doporučení pro odstraňování problémů s několika problémy, které se můžou zobrazit při používání Azure EventHubs.

## <a name="connectivity-certificate-or-timeout-issues"></a>Problémy s připojením, certifikátem nebo časovým limitem
Následující postup vám může pomáhat s odstraňováním potíží s připojením/vypršením časového limitu pro všechny služby v rámci *. servicebus.windows.net. 

- Přejděte na nebo [wget](https://www.gnu.org/software/wget/) `https://<yournamespacename>.servicebus.windows.net/` . Pomáhá s kontrolou, jestli máte problémy s filtrováním IP adres nebo virtuální sítí nebo s řetězem certifikátů (nejběžnější při použití sady Java SDK).

    Příklad úspěšné zprávy:
    
    ```xml
    <feed xmlns="http://www.w3.org/2005/Atom"><title type="text">Publicly Listed Services</title><subtitle type="text">This is the list of publicly-listed services currently available.</subtitle><id>uuid:27fcd1e2-3a99-44b1-8f1e-3e92b52f0171;id=30</id><updated>2019-12-27T13:11:47Z</updated><generator>Service Bus 1.1</generator></feed>
    ```
    
    Příklad chybové zprávy o selhání:

    ```json
    <Error>
        <Code>400</Code>
        <Detail>
            Bad Request. To know more visit https://aka.ms/sbResourceMgrExceptions. . TrackingId:b786d4d1-cbaf-47a8-a3d1-be689cda2a98_G22, SystemTracker:NoSystemTracker, Timestamp:2019-12-27T13:12:40
        </Detail>
    </Error>
    ```
- Spusťte následující příkaz, který zkontroluje, jestli je v bráně firewall blokovaný port. Použité porty jsou 443 (HTTPS), 5671 (AMQP) a 9093 (Kafka). V závislosti na knihovně, kterou používáte, se používají také jiné porty. Tady je ukázkový příkaz, který zkontroluje, jestli je port 5671 blokovaný.

    ```powershell
    tnc <yournamespacename>.servicebus.windows.net -port 5671
    ```

    V systému Linux:

    ```shell
    telnet <yournamespacename>.servicebus.windows.net 5671
    ```
- Pokud dochází k problémům s přerušovaným připojením, spusťte následující příkaz, který zkontroluje, jestli nedošlo k vyřazeným paketům. Tento příkaz se pokusí vytvořit 25 různých připojení TCP každé 1 sekundy ke službě. Pak můžete zjistit, kolik z nich bylo úspěšné/neúspěšné, a také zobrazit latenci připojení TCP. Nástroj si můžete stáhnout `psping` [tady](/sysinternals/downloads/psping).

    ```shell
    .\psping.exe -n 25 -i 1 -q <yournamespacename>.servicebus.windows.net:5671 -nobanner     
    ```
    Ekvivalentní příkazy můžete použít, pokud používáte jiné nástroje `tnc` , například, `ping` a tak dále. 
- Získejte trasování sítě, pokud předchozí kroky neumožňují a neanalyzují ho pomocí nástrojů, jako je třeba [Wireshark](https://www.wireshark.org/). V případě potřeby kontaktujte [Podpora Microsoftu](https://support.microsoft.com/) . 

## <a name="issues-that-may-occur-with-service-upgradesrestarts"></a>Problémy, které se mohou vyskytnout při upgradech nebo restartu služby
Upgrady a restarty back-end služby můžou způsobit následující dopad na vaše aplikace:

- Žádosti můžou být v neomezeném omezení.
- Je možné, že v příchozích zprávách nebo požadavcích dojde k přerušení.
- Soubor protokolu může obsahovat chybové zprávy.
- Aplikace se můžou po několik sekund odpojit od služby.

Pokud kód aplikace využívá sadu SDK, zásady opakování jsou již vytvořeny a aktivní. Aplikace se znovu připojí bez významného dopadu na aplikaci nebo pracovní postup.


## <a name="next-steps"></a>Další kroky

Další informace o službě Event Hubs najdete na následujících odkazech:

* [Přehled Event Hubs](event-hubs-what-is-event-hubs.md)
* [Vytvoření centra událostí](event-hubs-create.md)
* [Nejčastější dotazy k Event Hubs](event-hubs-faq.md)
