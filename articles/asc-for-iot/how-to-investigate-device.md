---
title: Azure Security Center pro Průvodce šetření zařízení IoT ve verzi Preview | Dokumentace Microsoftu
description: Tento postup průvodce vysvětluje, jak prozkoumat podezřelé zařízení IoT pomocí služby Log Analytics pomocí Azure Security Center pro IoT.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: b18b48ae-b445-48f8-9ac0-365d6e065b64
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2019
ms.author: mlottner
ms.openlocfilehash: 56378e94bf448da4c3047c30be3ae25887f113b5
ms.sourcegitcommit: d83fa82d6fec451c0cb957a76cfba8d072b72f4f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2019
ms.locfileid: "58862620"
---
# <a name="investigate-a-suspicious-iot-device"></a>Prozkoumat podezřelé zařízení IoT

> [!IMPORTANT]
> Azure Security Center pro IoT je aktuálně ve verzi public preview.
> Tato verze preview je k dispozici bez smlouvy o úrovni služeb a nedoporučuje se používat pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Security Center (ASC) pro oznámení služby IoT a důkazy poskytnout jasné údaje, když zařízení IoT je podezření, že účasti v podezřelé aktivity nebo pokud existují označení, že dojde k ohrožení zařízení. 

V této příručce použijte návrhy šetření poskytován, abyste mohli zjistit potenciální rizika pro vaši organizaci, rozhodněte se, jak opravit a Objevte nejlepší způsoby útokům podobně jako v budoucnu.  

> [!div class="checklist"]
> * Najít data zařízení
> * Prozkoumat pomocí kql dotazů


## <a name="how-can-i-access-my-data"></a>Jak lze získat přístup k Moje data?

Ve výchozím nastavení ukládá ASC pro IoT výstrah zabezpečení a doporučení v pracovním prostoru Log Analytics. Můžete také ukládat data nezpracovaná zabezpečení.

Přejděte pracovního prostoru Log Analytics pro úložiště dat:

1. Otevřete své Centrum IoT 
1. Klikněte na tlačítko **zabezpečení**a pak vyberte **nastavení**.
1. Změňte podrobností o konfiguraci pracovního prostoru Log Analytics. 
1. Klikněte na **Uložit**. 

Po konfiguraci proveďte následující příkaz pro přístup k datům uloženým ve vašem pracovním prostoru Log Analytics:

1. Vyberte a klikněte na ASC IoT výstrahy ve službě IoT Hub. 
1. Klikněte na tlačítko **další šetření**. 
1. Vyberte **zobrazíte, která zařízení mají tato výstraha, klikněte sem a zobrazit sloupce DeviceId**.

## <a name="investigation-steps-for-suspicious-iot-devices"></a>Postup vyšetřování podezřelých zařízení IoT

Chcete-li získat přístup k analýze a nezpracovaných dat o vašich zařízeních IoT, přejděte do pracovního prostoru Log Analytics [přistupovat ke svým datům](#how-can-i-access-my-data).

Zkontrolujte a prozkoumat data zařízení následující údaje a aktivit pomocí následujících dotazů kql.

### <a name="related-alerts"></a>Související upozornění

Chcete zjistit, pokud se ostatní výstrahy aktivuje kolem stejné použijte následující dotaz kql:

  ~~~
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityAlert
  | where ExtendedProperties contains device and ResourceId contains tolower(hub)
  | project TimeGenerated, AlertName, AlertSeverity, Description, ExtendedProperties
  ~~~

### <a name="users-with-access"></a>Uživatelé s přístupem

Chcete-li zjistit, kteří mají přístup k zařízení použijte následující dotaz kql: 

  ~~~
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
  ~~~
Tato data použijte k zjištění: 
  1. Kteří uživatelé mají přístup k zařízení?
  2. Mají uživatelé s přístupem úrovně oprávnění podle očekávání? 

### <a name="open-ports"></a>Otevřít porty

Najít následující dotaz kql použít na více instancí, které porty v zařízení se právě používají, nebo byla použita: 

  ~~~
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
  ~~~

    Use this data to discover:
  1. Které naslouchá sockets jsou momentálně aktivní zařízení?
  2. Má být povolený naslouchání soketů, které jsou momentálně aktivní?
  3. Existují všechny podezřelé vzdálené adresy, které jsou připojené k zařízení?

### <a name="user-logins"></a>Přihlášení uživatele

Uživatelé, kteří přihlášení na zařízení a zjistěte, použijte tento dotaz kql: 
 
  ~~~
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
  ~~~

    Use the query results to discover:
  1. Kteří uživatelé přihlášení k zařízení?
  2. Máte uživatele, kteří přihlášení pro přihlášení?
  3. Z neočekávané nebo očekávaných IP adres připojit uživatele, kteří přihlášení?
  
### <a name="process-list"></a>Seznam procesů

Chcete-li zjistit, jestli seznam procesů je jako očekávané použití následující dotaz kql: 

  ~~~
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
  ~~~

    Use the query results to discover:

  1. Nebyly žádné podezřelých procesů spuštěných v zařízení?
  2. Byly spuštěny procesy podle příslušné uživatele?
  3. Žádné spuštění příkazového řádku obsahovat správné a očekávaných argumentů?

## <a name="next-steps"></a>Další postup
Po prošetření zařízení a získat lepší přehled rizik, můžete chtít zvážit [konfigurace vlastních výstrah](quickstart-create-custom-alerts.md) a zlepšit tak stav zabezpečení řešení IoT. Pokud ještě nemáte agenta do zařízení, vezměte v úvahu [nasazení agenta zabezpečení](how-to-deploy-agent.md) nebo [změna konfigurace existujícího agenta zařízení](how-to-agent-configuration.md) dosáhnout lepších výsledků. 