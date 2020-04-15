---
title: Prošetření podezřelého zařízení
description: Tento způsob, jak průvodce vysvětluje, jak pomocí Azure Security Center pro IoT prozkoumat podezřelé zařízení IoT pomocí Log Analytics.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: b18b48ae-b445-48f8-9ac0-365d6e065b64
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: f333f28dc0e02e8d010f5521f298d0f0b031dbf2
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311037"
---
# <a name="investigate-a-suspicious-iot-device"></a>Prozkoumání podezřelého zařízení IoT

Výstrahy služby Azure Security Center for IoT poskytují jasné informace o tom, kdy jsou zařízení IoT podezřelá z účasti na podezřelých aktivitách nebo když existují náznaky, že je zařízení ohroženo.

V této příručce použijte návrhy šetření k určení potenciálních rizik pro vaši organizaci, rozhodnout, jak napravit a zjistit nejlepší způsoby, jak zabránit podobným útokům v budoucnu.

> [!div class="checklist"]
> * Vyhledání dat zařízení
> * Prozkoumat pomocí dotazů kql

## <a name="how-can-i-access-my-data"></a>Jak mohu získat přístup ke svým údajům?

Ve výchozím nastavení Azure Security Center pro IoT ukládá výstrahy zabezpečení a doporučení v pracovním prostoru Log Analytics. Můžete také zvolit uložení nezpracovaných bezpečnostních dat.

Chcete-li vyhledat pracovní prostor Log Analytics pro ukládání dat:

1. Otevřete centrum IoT,
1. V části **Zabezpečení**klepněte na položku **Přehled**a vyberte **položku Nastavení**.
1. Změňte podrobnosti konfigurace pracovního prostoru Analýzy protokolů.
1. Klikněte na **Uložit**.

V návaznosti na konfiguraci postupujte takto, abyste měli přístup k datům uloženým v pracovním prostoru Log Analytics:

1. Vyberte a klikněte na azure security center pro iot upozornění ve vašem Centru IoT.
1. Klepněte na tlačítko **Další šetření**.
1. Vyberte **Chcete-li zjistit, která zařízení mají tuto výstrahu, klikněte sem a zobrazte sloupec DeviceId**.

## <a name="investigation-steps-for-suspicious-iot-devices"></a>Vyšetřovací kroky pro podezřelá zařízení IoT

Chcete-li zobrazit přehledy a nezpracovaná data o vašich zařízeních IoT, přejděte do pracovního prostoru Log Analytics [a získejte přístup k datům](#how-can-i-access-my-data).

Podívejte se na ukázkové dotazy kql níže, abyste mohli začít s vyšetřováním upozornění a aktivit na vašem zařízení.

### <a name="related-alerts"></a>Související výstrahy

Chcete-li zjistit, zda byly spuštěny další výstrahy přibližně ve stejnou dobu, použijte následující dotaz kql:

  ```
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityAlert
  | where ExtendedProperties contains device and ResourceId contains tolower(hub)
  | project TimeGenerated, AlertName, AlertSeverity, Description, ExtendedProperties
  ```

### <a name="users-with-access"></a>Uživatelé s přístupem

Chcete-li zjistit, kteří uživatelé mají přístup k tomuto zařízení, použijte následující dotaz kql:

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

- Kteří uživatelé mají k zařízení přístup?
- Mají uživatelé s přístupem očekávané úrovně oprávnění?

### <a name="open-ports"></a>Otevřené porty

Chcete-li zjistit, které porty v zařízení jsou aktuálně používány nebo byly použity, použijte následující dotaz kql:

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

- Které naslouchání zásuvky jsou v zařízení aktuálně aktivní?
- Mají být povoleny naslouchání sokety, které jsou aktuálně aktivní?
- Jsou k zařízení připojeny nějaké podezřelé vzdálené adresy?

### <a name="user-logins"></a>Přihlášení uživatele

Chcete-li najít uživatele, kteří se přihlásili k zařízení, použijte následující dotaz kql:

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

Výsledky dotazu slouží ke zjištění:

- Kteří uživatelé se k zařízení přihlásili?
- Mají se uživatelé přihlásit?
- Připojili se uživatelé, kteří se přihlásili, z očekávaných nebo neočekávaných IP adres?

### <a name="process-list"></a>Seznam procesů

Chcete-li zjistit, zda je seznam procesů očekávaný, použijte následující dotaz kql:

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

Výsledky dotazu slouží ke zjištění:

- Byly v zařízení spuštěny nějaké podezřelé procesy?
- Byly procesy prováděny příslušnými uživateli?
- Obsahovaly některé spuštění příkazového řádku správné a očekávané argumenty?

## <a name="next-steps"></a>Další kroky

Po prozkoumání zařízení a lepší pochopení rizik, možná budete chtít [zvážit konfigurace vlastní výstrahy](quickstart-create-custom-alerts.md) ke zlepšení stavu zabezpečení řešení IoT. Pokud agenta zařízení ještě nemáte, zvažte [nasazení agenta zabezpečení](how-to-deploy-agent.md) nebo [změnu konfigurace existujícího agenta zařízení](how-to-agent-configuration.md) za účelem zlepšení výsledků.
