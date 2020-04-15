---
title: Místní konfigurace agenta zabezpečení (C#)
description: Přečtěte si další informace o službě zabezpečení Azure Security Center for IoT, místním konfiguračním souboru agenta zabezpečení pro C#.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 2cf6a49b-5d35-491f-abc3-63ec24eb4bc2
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/26/2019
ms.author: mlottner
ms.openlocfilehash: adf0d72763e0cb1892d64c68a6dce05abbf6f582
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311666"
---
# <a name="understanding-the-local-configuration-file-c-agent"></a>Principy místního konfiguračního souboru (agent jazyka C#)

Azure Security Center pro agenta zabezpečení IoT používá konfigurace z místního konfiguračního souboru.

Agent zabezpečení přečte konfigurační soubor jednou při spuštění agenta. Konfigurace nalezené v místním konfiguračním souboru obsahují konfiguraci ověřování i další konfigurace související s agentem.

Agent zabezpečení jazyka C# používá více konfiguračních souborů:

- **General.config** - Konfigurace související s agentem.
- **Authentication.config** - Konfigurace související s ověřováním (včetně podrobností o ověřování).
- **SecurityIotInterface.config** - Konfigurace související s IoT.

Konfigurační soubory obsahují výchozí konfiguraci. Konfigurace ověřování je naplněna během instalace agenta a změny konfiguračního souboru jsou provedeny při restartování agenta.

## <a name="configuration-file-location"></a>Umístění konfiguračního souboru

Pro Linux:

- Konfigurační soubory `/var/ASCIoTAgent`operačního systému jsou umístěny v .

Ve Windows:

- Konfigurační soubory operačního systému jsou umístěny v adresáři agenta zabezpečení.

### <a name="generalconfig-configurations"></a>Konfigurace General.config

| Název konfigurace | Možné hodnoty | Podrobnosti |
|:-----------|:---------------|:--------|
| agentId | GUID | Jedinečný identifikátor agenta |
| readRemoteConfigurationTimeout | TimeSpan | Časové období pro načítání vzdálené konfigurace z ioT hubu. Pokud agent nemůže načíst konfiguraci v zadaném čase, operace bude časový mzda.|
| schedulerInterval | TimeSpan | Interval interního plánovače. |
| producentInterval | TimeSpan | Interval pracovníka výrobce událostí. |
| consumerInterval | TimeSpan | Interval pracovníka příjemce události. |
| highPriorityQueueSizePercentage | 0 < číslo < 1 | Část celkové mezipaměti vyhrazené pro zprávy s vysokou prioritou. |
| Loglevel | "Vypnuto", "Závažná", "Chyba", "Varování", "Informace", "Ladění"  | Protokolzprávy stejné a nad tuto závažnost jsou zaznamenány ladění konzole (Syslog v Linuxu). |
| fileLogLevel |  "Vypnuto", "Závažná", "Chyba", "Varování", "Informace", "Ladění"| Protokolzprávy stejné a vyšší než tato závažnost jsou zaznamenány do souboru (Syslog v Linuxu). |
| diagnosticVerbosityLevel | "Žádné", "Některé", "Všechny", | Úroveň podrobností diagnostických událostí. Žádné - diagnostické události nejsou odeslány, Některé - Jsou odesílány pouze diagnostické události s vysokou důležitostí, Všechny - všechny protokoly jsou také odesílány jako diagnostické události. |
| Logfilepath | Cesta k souboru | Pokud fileLogLevel > Off, protokoly jsou zapsány do tohoto souboru. |
| defaultEventPriority | "Vysoká", "Nízká", "Vypnuto" | Výchozí priorita události. |

### <a name="generalconfig-example"></a>Příklad souboru General.config

```XML
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

### <a name="authenticationconfig"></a>Nástroj Authentication.config

| Název konfigurace | Možné hodnoty | Podrobnosti |
|:-----------|:---------------|:--------|
| název modulu | řetězec | Název identity modulu zabezpečení. Tento název musí odpovídat názvu identity modulu v zařízení. |
| deviceId | řetězec | ID zařízení (jako registrované v Azure IoT Hub). || schedulerInterval | Řetězec TimeSpan | Interval interního plánovače. |
| gatewayHostname | řetězec | Název hostitele služby Azure Iot Hub. Obvykle <my-hub>.azure-devices.net |
| Filepath | řetězec - cesta k souboru | Cesta k souboru, který obsahuje tajný klíč ověřování.|
| type | "SymmetricKey", "SelfSignedCertificateCertificate" | Tajný klíč uživatele pro ověřování. Pokud *SymmetricKey* je tajný klíč uživatele symetrickým klíčem, zvolte *certifikát podepsaný svým držitelem,* pokud je tajný klíč certifikát podepsaný svým držitelem. |
| identity | "DPS", "Modul", "Zařízení" | Ověřovací identita – DPS, pokud je ověřování provedeno prostřednictvím DPS, Modul, pokud je ověřování provedeno pomocí pověření modulu, nebo zařízení, pokud je ověřování provedeno pomocí pověření zařízení.
| certifikátLocationKind |  "LocalFile", "Store" | LocalFile Pokud je certifikát uložen v souboru, uložte, pokud je certifikát umístěn v úložišti certifikátů. |
| idScope | řetězec | ID rozsah DPS |
| id registrace | řetězec  | DPS ID registrace zařízení. |
|

### <a name="authenticationconfig-example"></a>Příklad authentication.config

```XML
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

### <a name="securityiotinterfaceconfig"></a>Bezpečnostní rozhraní Iot.config

| Název konfigurace | Možné hodnoty | Podrobnosti |
|:-----------|:---------------|:--------|
| transportType | "Ampq" "Mqtt" | Typ přenosu ioT Hub. |
|

### <a name="securityiotinterfaceconfig-example"></a>Příklad SecurityIotInterface.config

```XML
<ExternalInterface>
  <add key="facadeType"  value="Microsoft.Azure.Security.IoT.Agent.Common.SecurityIoTHubInterface, Security.Common" />
  <add key="transportType" value="Amqp"/>
</ExternalInterface>
```

## <a name="next-steps"></a>Další kroky

- Přečtěte si [přehled](overview.md) služby Azure Security Center for IoT
- Další informace o Azure Security Center pro [architekturu](architecture.md) IoT
- Povolení [služby](quickstart-onboard-iot-hub.md) Azure Security Center for IoT
- Přečtěte si [nejčastější dotazy](resources-frequently-asked-questions.md) ke Službě Azure Security Center for IoT
- Zjistěte, jak získat přístup k [nezpracovaným bezpečnostním datům](how-to-security-data-access.md)
- Principy [doporučení](concept-recommendations.md)
- Principy [výstrah](concept-security-alerts.md) zabezpečení
