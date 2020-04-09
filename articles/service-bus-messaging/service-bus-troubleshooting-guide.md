---
title: Průvodce odstraňováním potíží pro Azure Service Bus | Dokumenty společnosti Microsoft
description: Tento článek obsahuje seznam výjimek zasílání zpráv Azure Service Bus a navrhované akce, které mají být podniknuty, když dojde k výjimce.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 3d8526fe-6e47-4119-9f3e-c56d916a98f9
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2020
ms.author: aschhab
ms.openlocfilehash: 63bf035d4e19cc1d64998a6ad533812e71ee71b8
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80887772"
---
# <a name="troubleshooting-guide-for-azure-service-bus"></a>Průvodce odstraňováním potíží pro Azure Service Bus
Tento článek obsahuje tipy pro řešení potíží a doporučení pro několik problémů, které se mohou zobrazit při používání služby Azure Service Bus. 

## <a name="connectivity-certificate-or-timeout-issues"></a>Problémy s připojením, certifikátem nebo časovým časem
Následující kroky vám mohou pomoci s řešením problémů s připojením/certifikátem/časovým časem pro všechny služby v části *.servicebus.windows.net. 

- Přejděte na nebo [wget](https://www.gnu.org/software/wget/) `https://<yournamespace>.servicebus.windows.net/`. Pomáhá při kontrole, zda máte filtrování IP nebo problémy s virtuální sítí nebo řetězem certifikátů (nejběžnější při použití sady Java SDK).

    Příklad úspěšné zprávy:
    
    ```xml
    <feed xmlns="http://www.w3.org/2005/Atom"><title type="text">Publicly Listed Services</title><subtitle type="text">This is the list of publicly-listed services currently available.</subtitle><id>uuid:27fcd1e2-3a99-44b1-8f1e-3e92b52f0171;id=30</id><updated>2019-12-27T13:11:47Z</updated><generator>Service Bus 1.1</generator></feed>
    ```
    
    Příklad chybové zprávy selhání:

    ```json
    <Error>
        <Code>400</Code>
        <Detail>
            Bad Request. To know more visit https://aka.ms/sbResourceMgrExceptions. . TrackingId:b786d4d1-cbaf-47a8-a3d1-be689cda2a98_G22, SystemTracker:NoSystemTracker, Timestamp:2019-12-27T13:12:40
        </Detail>
    </Error>
    ```
- Spusťte následující příkaz a zkontrolujte, zda je v bráně firewall blokován nějaký port. Použité porty jsou 443 (HTTPS), 5671 (AMQP) a 9354 (Net Messaging/SBMP). V závislosti na knihovně, kterou používáte, se používají také další porty. Zde je ukázkový příkaz, který kontroluje, zda je port 5671 blokován. 

    ```powershell
    tnc <yournamespacename>.servicebus.windows.net -port 5671
    ```

    Na Linuxu:

    ```shell
    telnet <yournamespacename>.servicebus.windows.net 5671
    ```
- Pokud dochází k občasným problémům s připojením, spusťte následující příkaz a zkontrolujte, zda nejsou nějaké vynecháné pakety. Tento příkaz se pokusí vytvořit 25 různých připojení TCP každých 1 sekundu se službou. Potom můžete zkontrolovat, kolik z nich se podařilo nebo se nezdařilo a také zobrazit latenci připojení TCP. Zde si `psping` můžete stáhnout nástroj [zde](/sysinternals/downloads/psping).

    ```shell
    .\psping.exe -n 25 -i 1 -q <yournamespace>.servicebus.windows.net:5671 -nobanner     
    ```
    Ekvivalentní příkazy můžete použít, pokud používáte `tnc`jiné `ping`nástroje, například , a tak dále. 
- Získat trasování do sítě, pokud předchozí kroky nepomáhají a analyzovat pomocí nástrojů, jako je [Wireshark](https://www.wireshark.org/). V případě potřeby kontaktujte [podporu společnosti Microsoft.](https://support.microsoft.com/) 

## <a name="issues-that-may-occur-with-service-upgradesrestarts"></a>Problémy, které mohou nastat s upgrady nebo restartování služby
Upgrady a restartování služby Back-end mohou způsobit následující dopad na vaše aplikace:

- Požadavky mohou být na okamžik omezeny.
- Může dojít k poklesu příchozích zpráv nebo požadavků.
- Soubor protokolu může obsahovat chybové zprávy.
- Aplikace mohou být na několik sekund odpojeny od služby.

Pokud kód aplikace využívá sdk, zásady opakování je již integrována a aktivní. Aplikace se znovu připojí bez významného dopadu na aplikaci/pracovní postup.

## <a name="unauthorized-access-send-claims-are-required"></a>Neoprávněný přístup: Odesílání deklarací je vyžadováno
Tato chyba se může zobrazit při pokusu o přístup k tématu služby Service Bus z aplikace Visual Studio v místním počítači pomocí spravované identity přiřazené uživatelem s oprávněními k odeslání.

```bash
Service Bus Error: Unauthorized access. 'Send' claim\(s\) are required to perform this operation.
```

Chcete-li tuto chybu vyřešit, nainstalujte knihovnu [Microsoft.Azure.Services.AppAuthentication.](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/)  Další informace naleznete v [tématu Local development authentication](..\key-vault\service-to-service-authentication.md#local-development-authentication). 

Informace o tom, jak přiřadit oprávnění k rolím, najdete [v tématu Ověření spravované identity pomocí Služby Azure Active Directory pro přístup k prostředkům Služby Azure Service Bus](service-bus-managed-service-identity.md).

## <a name="next-steps"></a>Další kroky
Viz následující články: 

- [Výjimky správce prostředků Azure](service-bus-resource-manager-exceptions.md). Obsahuje seznam výjimek generovaných při interakci s Azure Service Bus pomocí Azure Resource Manager (prostřednictvím šablon nebo přímých volání).
- [Výjimky pro zasílání zpráv](service-bus-messaging-exceptions.md). Poskytuje seznam výjimek generovaných rozhraním .NET Framework pro azure service bus. 

