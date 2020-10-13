---
title: Prozkoumat podezřelé zařízení
description: Tato příručka vysvětluje, jak pomocí programu Defender pro IoT prozkoumat podezřelé zařízení IoT pomocí Log Analytics.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/04/2020
ms.author: mlottner
ms.openlocfilehash: cd7802fa4a88c3b05140d30ab4d8b2bbd4adc8f4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90936462"
---
# <a name="investigate-a-suspicious-iot-device"></a>Prozkoumat podezřelé zařízení IoT

Služba Defender pro výstrahy služby IoT poskytuje jasné údaje o tom, že zařízení IoT jsou podezřelá z účasti na podezřelých aktivitách nebo když existují náznaky ohrožení zařízení.

V tomto průvodci můžete použít návrhy šetření, které vám pomůžou určit potenciální rizika pro vaši organizaci, rozhodnout se, jak je opravit, a zjistit nejlepší způsoby, jak v budoucnu zabránit podobným útokům.

> [!div class="checklist"]
> * Hledání dat zařízení
> * Prozkoumat pomocí dotazů KQL

## <a name="how-can-i-access-my-data"></a>Jak můžu získat přístup k datům?

Ve výchozím nastavení Defender pro IoT ukládá výstrahy a doporučení zabezpečení v pracovním prostoru Log Analytics. Můžete také zvolit ukládání nezpracovaných dat zabezpečení.

Vyhledání pracovního prostoru Log Analytics pro úložiště dat:

1. Otevřete Centrum IoT,
1. V části **zabezpečení**vyberte **Nastavení**a potom vyberte **shromažďování dat**.
1. Změňte podrobnosti konfigurace Log Analytics pracovního prostoru.
1. Klikněte na **Uložit**.

Pro přístup k datům uloženým v pracovním prostoru Log Analytics proveďte následující konfiguraci:

1. V IoT Hub vyberte a klikněte na výstrahu Defenderu pro IoT.
1. Klikněte na **Další šetření**.
1. Vyberte **, pokud chcete zobrazit, která zařízení mají toto upozornění, klikněte sem a zobrazte sloupec DeviceID**.

## <a name="investigation-steps-for-suspicious-iot-devices"></a>Postup šetření pro podezřelá zařízení IoT

Pokud chcete zobrazit přehledy a nezpracovaná data o zařízeních IoT, přejděte do svého pracovního prostoru Log Analytics, [abyste měli přístup k datům](#how-can-i-access-my-data).

V ukázkových dotazech k KQL můžete začít s vyšetřováním výstrah a aktivit v zařízení.

### <a name="related-alerts"></a>Související výstrahy

Pokud chcete zjistit, jestli se po stejnou dobu spouštěly další výstrahy, použijte následující dotaz KQL:

  ```
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityAlert
  | where ExtendedProperties contains device and ResourceId contains tolower(hub)
  | project TimeGenerated, AlertName, AlertSeverity, Description, ExtendedProperties
  ```

### <a name="users-with-access"></a>Uživatelé s přístupem

Pokud chcete zjistit, kteří uživatelé mají k tomuto zařízení přístup, použijte následující dotaz KQL:

 ```
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "LocalUsers"
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     GroupNames=extractjson("$.GroupNames", EventDetails, typeof(string)),
     UserName=extractjson("$.UserName", EventDetails, typeof(string))
  | summarize FirstObserved=min(TimestampLocal) by GroupNames, UserName
 ```
Pomocí těchto dat můžete zjistit:

- Kteří uživatelé mají přístup k zařízení?
- Mají uživatelé s přístupem očekávané úrovně oprávnění?

### <a name="open-ports"></a>Otevřené porty

Chcete-li zjistit, které porty v zařízení se aktuálně používají nebo byly použity, použijte následující dotaz KQL:

 ```
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "ListeningPorts"
     and extractjson("$.LocalPort", EventDetails, typeof(int)) <= 1024 // avoid short-lived TCP ports (Ephemeral)
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     Protocol=extractjson("$.Protocol", EventDetails, typeof(string)),
     LocalAddress=extractjson("$.LocalAddress", EventDetails, typeof(string)),
     LocalPort=extractjson("$.LocalPort", EventDetails, typeof(int)),
     RemoteAddress=extractjson("$.RemoteAddress", EventDetails, typeof(string)),
     RemotePort=extractjson("$.RemotePort", EventDetails, typeof(string))
  | summarize MinObservedTime=min(TimestampLocal), MaxObservedTime=max(TimestampLocal), AllowedRemoteIPAddress=makeset(RemoteAddress), AllowedRemotePort=makeset(RemotePort) by Protocol, LocalPort
 ```

Pomocí těchto dat můžete zjistit:

- Které naslouchající sokety jsou v zařízení aktuálně aktivní?
- Má se povolit naslouchání soketů, které jsou aktuálně aktivní?
- Jsou k zařízení připojeny nějaké podezřelé vzdálené adresy?

### <a name="user-logins"></a>Přihlášení uživatelů

Pokud chcete najít uživatele, kteří se k zařízení přihlásili, použijte následující dotaz KQL:

 ```
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "Login"
     // filter out local, invalid and failed logins
     and EventDetails contains "RemoteAddress"
     and EventDetails !contains '"RemoteAddress":"127.0.0.1"'
     and EventDetails !contains '"UserName":"(invalid user)"'
     and EventDetails !contains '"UserName":"(unknown user)"'
     //and EventDetails !contains '"Result":"Fail"'
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     UserName=extractjson("$.UserName", EventDetails, typeof(string)),
     LoginHandler=extractjson("$.Executable", EventDetails, typeof(string)),
     RemoteAddress=extractjson("$.RemoteAddress", EventDetails, typeof(string)),
     Result=extractjson("$.Result", EventDetails, typeof(string))
  | summarize CntLoginAttempts=count(), MinObservedTime=min(TimestampLocal), MaxObservedTime=max(TimestampLocal), CntIPAddress=dcount(RemoteAddress), IPAddress=makeset(RemoteAddress) by UserName, Result, LoginHandler
 ```

Pomocí výsledků dotazu můžete zjistit:

- Kteří uživatelé se k zařízení přihlásili?
- Má uživatel přihlášený, měl by se přihlásit?
- Pracovali uživatelé, kteří se přihlásili z očekávaných nebo neočekávaných IP adres?

### <a name="process-list"></a>Seznam procesů

Pokud chcete zjistit, jestli je seznam procesů podle očekávání, použijte následující dotaz KQL:

 ```
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "ProcessCreate"
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     Executable=extractjson("$.Executable", EventDetails, typeof(string)),
     UserId=extractjson("$.UserId", EventDetails, typeof(string)),
     CommandLine=extractjson("$.CommandLine", EventDetails, typeof(string))
  | join kind=leftouter (
     // user UserId details
     SecurityIoTRawEvent
     | where
        DeviceId == device and AssociatedResourceId contains tolower(hub)
        and RawEventName == "LocalUsers"
     | project
        UserId=extractjson("$.UserId", EventDetails, typeof(string)),
        UserName=extractjson("$.UserName", EventDetails, typeof(string))
     | distinct UserId, UserName
  ) on UserId
  | extend UserIdName = strcat("Id:", UserId, ", Name:", UserName)
  | summarize CntExecutions=count(), MinObservedTime=min(TimestampLocal), MaxObservedTime=max(TimestampLocal), ExecutingUsers=makeset(UserIdName), ExecutionCommandLines=makeset(CommandLine) by Executable
```

Pomocí výsledků dotazu můžete zjistit:

- V zařízení byly spuštěné nějaké podezřelé procesy?
- Byly procesy spouštěny příslušnými uživateli?
- Obsahovalo jakékoli spuštění příkazového řádku správné a očekávané argumenty?

## <a name="next-steps"></a>Další kroky

Když prozkoumáte zařízení a získáte lepší porozumění vašim rizikům, možná budete chtít zvážit [konfiguraci vlastních výstrah](quickstart-create-custom-alerts.md) , abyste vylepšili stav zabezpečení řešení IoT. Pokud ještě nemáte agenta zařízení, zvažte [nasazení agenta zabezpečení](how-to-deploy-agent.md) nebo [změnu konfigurace stávajícího agenta zařízení](how-to-agent-configuration.md) , aby se zlepšily vaše výsledky.
