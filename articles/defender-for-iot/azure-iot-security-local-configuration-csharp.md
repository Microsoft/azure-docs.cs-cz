---
title: Defender pro místní konfiguraci agenta zabezpečení IoT (C#)
description: Přečtěte si další informace o službě Defender pro službu zabezpečení IoT, místní konfigurační soubor agenta zabezpečení pro jazyk C#.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.custom: devx-track-csharp
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/26/2019
ms.author: mlottner
ms.openlocfilehash: 19fa5b2949888993954f3075d1e10c9e8f126e2f
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90936303"
---
# <a name="understanding-the-local-configuration-file-c-agent"></a>Principy místního konfiguračního souboru (agent C#)

Agent zabezpečení služby Defender pro IoT používá konfigurace z místního konfiguračního souboru.

Agent zabezpečení čte konfigurační soubor jednou při spuštění agenta. Konfigurace nalezené v místním konfiguračním souboru obsahuje konfiguraci ověřování i další konfigurace související s agenty.

Agent zabezpečení v jazyce C# používá více konfiguračních souborů:

- Konfigurace vztahující se k agentům **General.config** .
- **Authentication.config** – konfigurace související s ověřováním (včetně podrobností ověřování).
- **SecurityIotInterface.config** – konfigurace související s IoT

Konfigurační soubory obsahují výchozí konfiguraci. Konfigurace ověřování se naplní během instalace agenta a změny konfiguračního souboru se provedou při restartování agenta.

## <a name="configuration-file-location"></a>Umístění konfiguračního souboru

Pro Linux:

- Konfigurační soubory operačního systému jsou umístěny v `/var/ASCIoTAgent` .

Ve Windows:

- Konfigurační soubory operačního systému se nacházejí v adresáři agenta zabezpečení.

### <a name="generalconfig-configurations"></a>Konfigurace General.config

| Název konfigurace | Možné hodnoty | Podrobnosti |
|:-----------|:---------------|:--------|
| ID agenta | Identifikátor GUID | Jedinečný identifikátor agenta |
| readRemoteConfigurationTimeout | TimeSpan | Časové období načítání vzdálené konfigurace z IoT Hub. Pokud Agent nemůže v zadaném čase načíst konfiguraci, bude časový limit operace vypršel.|
| schedulerInterval | TimeSpan | Interval interního plánovače. |
| producerInterval | TimeSpan | Interval pracovního procesu producenta události |
| consumerInterval | TimeSpan | Interval pracovního procesu pro uživatele události |
| highPriorityQueueSizePercentage | 0 < číslo < 1 | Část celkové mezipaměti vyhrazená pro zprávy s vysokou prioritou. |
| logLevel | "Vypnuto", "závažná", "Chyba", "upozornění", "informace", "ladění"  | Zprávy protokolu rovnající se této závažnosti se budou protokolovat do ladicí konzoly (syslog v systému Linux). |
| LogLevel |  "Vypnuto", "závažná", "Chyba", "upozornění", "informace", "ladění"| Zprávy protokolu rovnající se této závažnosti se budou protokolovat do souboru (syslog v Linux). |
| diagnosticVerbosityLevel | None, "All", "All", | Úroveň podrobností diagnostických událostí. Žádné – neodesílají se diagnostické události, odesílají se jenom diagnostické události s vysokou důležitostí, všechny protokoly se také odesílají jako diagnostické události. |
| logFilePath | Cesta k souboru | Pokud > vypnuto, protokoly se zapisují do tohoto souboru. |
| defaultEventPriority | "Vysoká", "nízká", "off" | Výchozí priorita události |

### <a name="generalconfig-example"></a>Příklad General.config

```xml
<?xml version="1.0" encoding="utf-8"?>
<General>
  <add key="agentId" value="da00006c-dae9-4273-9abc-bcb7b7b4a987" />
  <add key="readRemoteConfigurationTimeout" value="00:00:30" />
  <add key="schedulerInterval" value="00:00:01" />
  <add key="producerInterval" value="00:02:00" />
  <add key="consumerInterval" value="00:02:00" />
  <add key="highPriorityQueueSizePercentage" value="0.5" />
  <add key="logLevel" value="Information" />
  <add key="fileLogLevel" value="Off"/>
  <add key="diagnosticVerbosityLevel" value="Some" />
  <add key="logFilePath" value="IotAgentLog.log" />
  <add key="defaultEventPriority" value="Low"/>
</General>
```

### <a name="authenticationconfig"></a>Authentication.config

| Název konfigurace | Možné hodnoty | Podrobnosti |
|:-----------|:---------------|:--------|
| moduleName | řetězec | Název identity modulu zabezpečení Tento název musí odpovídat názvu identity modulu v zařízení. |
| deviceId | řetězec | ID zařízení (registrované v Azure IoT Hub) || schedulerInterval | Řetězec TimeSpan | Interval interního plánovače. |
| gatewayHostname | řetězec | Název hostitele služby Azure IoT Hub. Obvykle <>. azure-devices.net |
| filePath | řetězec – cesta k souboru | Cesta k souboru, který obsahuje tajný klíč ověřování.|
| typ | "SymmetricKey", "SelfSignedCertificate" | Tajný klíč uživatele pro ověřování. Pokud je tajný klíč uživatele symetrický klíč, vyberte *SymmetricKey* . Pokud je tajný kód certifikát podepsaný svým držitelem, vyberte *certifikát podepsaný držitelem* . |
| identity | "DPS", "Module", "Device" | Ověřování identity – DPS Pokud se provádí ověření prostřednictvím DPS, modul, pokud se provádí ověřování pomocí přihlašovacích údajů modulu, nebo zařízení, pokud se provádí ověřování pomocí přihlašovacích údajů k zařízení.
| certificateLocationKind |  "Místní_soubor", "Store" | Místní_soubor Pokud je certifikát uložen v souboru, uloží se, pokud se certifikát nachází v úložišti certifikátů. |
| idScope | řetězec | Rozsah ID v DPS |
| registrationId | řetězec  | ID registrace zařízení DPS. |
|

### <a name="authenticationconfig-example"></a>Příklad Authentication.config

```xml
<?xml version="1.0" encoding="utf-8"?>
<Authentication>
  <add key="moduleName" value="azureiotsecurity"/>
  <add key="deviceId" value="d1"/>
  <add key="gatewayHostname" value=""/>
  <add key="filePath" value="c:\p-dps-d1.pfx"/>
  <add key="type" value="SelfSignedCertificate" />                     <!-- SymmetricKey, SelfSignedCertificate-->
  <add key="identity" value="DPS" />                 <!-- Device, Module, DPS -->
  <add key="certificateLocationKind" value="LocalFile" />  <!-- LocalFile, Store -->
  <add key="idScope" value="0ne0005335B"/>
  <add key="registrationId" value="d1"/>
</Authentication>
```

### <a name="securityiotinterfaceconfig"></a>SecurityIotInterface.config

| Název konfigurace | Možné hodnoty | Podrobnosti |
|:-----------|:---------------|:--------|
| transportType | "Ampq" "MQTT" | IoT Hub typ přenosu. |
|

### <a name="securityiotinterfaceconfig-example"></a>Příklad SecurityIotInterface.config

```xml
<ExternalInterface>
  <add key="facadeType"  value="Microsoft.Azure.Security.IoT.Agent.Common.SecurityIoTHubInterface, Security.Common" />
  <add key="transportType" value="Amqp"/>
</ExternalInterface>
```

## <a name="next-steps"></a>Další kroky

- Přečtěte si téma [Přehled](overview.md) služby Defender for IoT.
- Další informace o programu Defender pro [architekturu](architecture.md) IoT
- Povolení služby Defender pro IoT [Service](quickstart-onboard-iot-hub.md)
- Přečtěte si [Nejčastější dotazy](resources-frequently-asked-questions.md) ke službě Defender for IoT
- Přečtěte si, jak získat přístup k [nezpracovaným datům zabezpečení](how-to-security-data-access.md)
- Vysvětlení [doporučení](concept-recommendations.md)
- Vysvětlení [výstrah](concept-security-alerts.md) zabezpečení
