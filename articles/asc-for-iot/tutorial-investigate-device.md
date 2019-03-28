---
title: ASC kurzu šetření zařízení IoT ve verzi Preview | Dokumentace Microsoftu
description: Tento článek vysvětluje, jak pomocí ASC pro IoT můžete prozkoumat podezřelé zařízení IoT pomocí služby Log Analytics.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: b18b48ae-b445-48f8-9ac0-365d6e065b64
ms.service: ascforiot
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2019
ms.author: mlottner
ms.openlocfilehash: 5459c5a18742b92b77866241212f21c11d7851c9
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541551"
---
# <a name="tutorial-investigate-a-suspicious-iot-device"></a>Kurz: Prozkoumat podezřelé zařízení IoT

> [!IMPORTANT]
> ASC pro IoT je aktuálně ve verzi public preview.
> Tato verze preview je k dispozici bez smlouvy o úrovni služeb a nedoporučuje se používat pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

ASC pro oznámení služby IoT a důkazy poskytnout jasné údaje, když zařízení IoT je podezření, že účasti v podezřelé aktivity nebo pokud existují označení, že dojde k ohrožení zařízení. 

V tomto kurzu použijte šetření návrhy poskytován, abyste mohli zjistit potenciální rizika pro vaši organizaci, rozhodněte se, jak opravit a Objevte nejlepší způsoby útokům podobně jako v budoucnu.  

> [!div class="checklist"]
> * Najít data zařízení
> * Prozkoumat pomocí kql dotazů


## <a name="where-can-i-access-my-data"></a>Kde lze získat přístup k Moje data

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

Chcete-li získat přístup k analýze a nezpracovaných dat o vašich zařízeních IoT, přejděte do pracovního prostoru Log Analytics [kam může přístup data](#where-can-i-access-my-data).

Zkontrolujte a prozkoumat data zařízení následující údaje a aktivit pomocí následujících dotazů kql:

- Chcete zjistit, pokud se ostatní výstrahy aktivuje kolem stejné použijte následující dotaz kql:

  ~~~
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityAlert
  | where ExtendedProperties contains device and ResourceId contains tolower(hub)
  | project TimeGenerated, AlertName, AlertSeverity, Description, ExtendedProperties
  ~~~

- Chcete-li zjistit, kteří mají přístup k zařízení použijte následující dotaz kql: 

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
  2. Mají uživatelé s přístupem úrovně oprávnění očekávat? 

- Pokud chcete zjistit, jak se uživatelé budou mít přístup k zařízení, použijte následující dotaz kql:

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

    Tato data použijte k zjištění:
  1. Které naslouchá sockets jsou momentálně aktivní zařízení?
  2. Musí být naslouchání soketů, které jsou aktuálně aktivní?

- Pokud chcete zjistit, kdo přihlášení k zařízení, použijte následující dotaz kql: 
 
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

    Výsledky dotazu použijte ke zjištění:
  1. Kteří uživatelé přihlášení k zařízení?
  2. Z neočekávané nebo očekávaných IP adres připojit uživatele, kteří přihlášení?
  
- Pokud se zařízení chová podle očekávání, použijte následující dotaz kql:

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

    Výsledky dotazu použijte ke zjištění:

  1. Nebyly žádné podezřelých procesů spuštěných v zařízení?
  2. Byly spuštěny procesy podle příslušné uživatele?
  3. Žádné spuštění příkazového řádku obsahovat správné a očekávaných argumentů?

## <a name="next-steps"></a>Další postup
Po prošetření zařízení a získat lepší přehled rizik, můžete chtít zvážit [konfigurace vlastních výstrah](quickstart-create-custom-alerts.md) a zlepšit tak stav zabezpečení řešení IoT. Pokud ještě nemáte agenta do zařízení, vezměte v úvahu [nasazení agenta zabezpečení](select-deploy-agent.md) nebo [změna konfigurace existujícího agenta zařízení](concept-agent-configuration.md) dosáhnout lepších výsledků. 
 