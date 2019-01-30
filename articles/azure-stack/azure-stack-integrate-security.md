---
title: Předávání syslog Azure Stack
description: Zjistěte, jak pomocí řešení monitorování s použitím protokolu syslog předávání k integraci Azure Stack
services: azure-stack
author: PatAltimore
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 01/28/2019
ms.author: patricka
ms.reviewer: fiseraci
ms.lastreviewed: 01/28/2019
keywords: ''
ms.openlocfilehash: 7dff82538448b27f14dd81e2862cd63d4dd56a9b
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55247098"
---
# <a name="azure-stack-datacenter-integration---syslog-forwarding"></a>Integrace datových center Azure Stack – předávání syslog

Tento článek ukazuje, jak infrastruktura Azure stacku integrovat řešení externí zabezpečení už nasazená ve vašem datovém centru pomocí syslog. Například systém správu informace událostí zabezpečení (SIEM). Kanál syslog zpřístupňuje audity, výstrahy a protokolů zabezpečení ze všech komponent infrastruktury Azure stacku. Použití syslog předávání k integraci s řešeními monitorování zabezpečení a/nebo k načtení všech audity, výstrahy a zabezpečení zaznamená do úložiště pro uchovávání informací.

Počínaje aktualizací 1809, má Azure Stack integrované syslog klienta, který po nakonfigurování se generuje zprávy syslog s datovou částí v události formát cef (Common Format).

Následující diagram popisuje integrace služby Azure Stack s externí SIEM. Existují dva způsoby integrace, které je potřeba považovat za: první jednou (jeden modře) je infrastruktura Azure stacku, která zahrnuje virtuální počítače infrastruktury a uzly Hyper-V. Audity, protokolů zabezpečení a výstrahy z těchto komponent jsou centrálně shromažďovat a vystavené prostřednictvím syslogu s datovou částí CEF. Tento model integrace je popsaný na této stránce dokumentu.
Druhý vzor integrace je použité v ukázkách oranžově a zahrnuje řadiče pro správu základní desky (BMC), životního cyklu hostitelů hardwaru (HLH), virtuální počítače a/nebo virtuálních zařízení, na kterých běží hardwarových partnerů, monitorování a správu software a horní části přepínače (TOR rack). Protože tyto součásti jsou konkrétní, kontaktujte hardwarových partnerů hardwaru partnera pro dokumentaci o tom, jak integrovat se službou externí SIEM.

![Diagram předávání Syslog](media/azure-stack-integrate-security/syslog-forwarding.png)

## <a name="configuring-syslog-forwarding"></a>Konfigurace předávání protokolu syslog

Klient protokolu syslog v Azure stacku podporuje následující konfigurace:

1. **Syslog přes protokol TCP, vzájemné ověřování (klient a server) a TLS 1.2 šifrování:** V této konfiguraci syslog server a klient syslog ověření identity mezi sebou prostřednictvím certifikátů. Zprávy se odesílají přes zašifrovaný kanál TLS 1.2.

2. **Syslog přes protokol TCP. server ověřování a šifrování TLS 1.2:** V této konfiguraci syslog klient lze ověřit identitu serveru syslog prostřednictvím certifikátu. Zprávy se odesílají přes zašifrovaný kanál TLS 1.2.

3. **Syslog přes protokol TCP, žádné šifrování:** V této konfiguraci nejsou ověřeny syslog klienta a identit server syslog. Zprávy jsou odesílány ve formátu prostého textu prostřednictvím protokolu TCP.

4. **Syslog přes protokol UDP se žádné šifrování:** V této konfiguraci nejsou ověřeny syslog klienta a identit server syslog. Zprávy jsou odesílány ve formátu prostého textu prostřednictvím protokolu UDP.

> [!IMPORTANT]
> Společnost Microsoft důrazně doporučuje použití ověřování a šifrování pomocí protokolu TCP (konfigurace #1 nebo na velmi minimální #2) pro produkční prostředí pro ochranu před útoky man-in-the-middle a odposlouchávání zpráv.

### <a name="cmdlets-to-configure-syslog-forwarding"></a>Ke konfiguraci předávání syslog
Konfigurace předávání protokolu syslog vyžaduje přístup k privilegovaným koncový bod (období). Dvě rutiny Powershellu jsou přidané do období konfigurace předávání protokolu syslog:


```powershell
### cmdlet to pass the syslog server information to the client and to configure the transport protocol, the encryption and the authentication between the client and the server

Set-SyslogServer [-ServerName <String>] [-ServerPort <String>] [-NoEncryption] [-SkipCertificateCheck] [-SkipCNCheck] [-UseUDP] [-Remove]

### cmdlet to configure the certificate for the syslog client to authenticate with the server

Set-SyslogClient [-pfxBinary <Byte[]>] [-CertPassword <SecureString>] [-RemoveCertificate] 
```
#### <a name="cmdlets-parameters"></a>Parametry rutiny

Parametry pro *Set-SyslogServer* rutiny:

| Parametr | Popis | Type | Požaduje se |
|---------|---------|---------|---------|
|*ServerName* | Plně kvalifikovaný název domény nebo IP adresa serveru syslog | Řetězec | ano|
|*ServerPort* | Číslo portu serveru syslog naslouchá | Řetězec | ano|
|*NoEncryption*| Platnost klienta k odeslání zprávy syslog ve formátu prostého textu | Příznak | ne|
|*SkipCertificateCheck*| Přeskočit ověření certifikátu poskytnutého na server syslog během počáteční TLS handshake | Příznak | ne|
|*SkipCNCheck*| Přeskočit ověření hodnoty běžný název certifikátu poskytnutého na server syslog během počáteční TLS handshake | Příznak | ne|
|*UseUDP*| Syslog používat UDP jako přenosový protokol |Příznak | ne|
|*odebrat*| Odebrat konfiguraci serveru z klienta a ukončit předávání syslog| Příznak | ne|

Parametry pro *Set-SyslogClient* rutiny:
| Parametr | Popis | Type |
|---------|---------| ---------|
| *pfxBinary* | soubor PFX obsahující certifikát, který se použije klient jako identita k ověření vůči serveru syslog  | Byte] |
| *CertPassword* |  Heslo pro import privátní klíč, který je přidružený k souboru pfx | SecureString |
|*RemoveCertificate* | Odeberte certifikát z klienta | Příznak|

### <a name="configuring-syslog-forwarding-with-tcp-mutual-authentication-and-tls-12-encryption"></a>Konfigurace předávání protokolu syslog pomocí protokolu TCP, vzájemného ověřování a šifrování TLS 1.2

V této konfiguraci syslog klienta ve službě Azure Stack přeposílá zprávy na server syslog přes protokol TCP, pomocí šifrování TLS 1.2. Během počáteční handshake klient ověří, že server poskytuje certifikát platný a důvěryhodné; Podobně klient také poskytuje certifikát na server jako potvrzení identity. Tato konfigurace je nejbezpečnější jako jeho poskytuje úplné ověření identity klienta a serveru a posílání zpráv přes zašifrovaný kanál. 

> [!IMPORTANT]
> Společnost Microsoft důrazně doporučuje použít tuto konfiguraci pro produkční prostředí. 

Konfigurace předávání protokolu syslog pomocí protokolu TCP, vzájemného ověřování a šifrování TLS 1.2, spusťte v relaci období obě tyto rutiny:

```powershell
# Configure the server
Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -ServerPort <Port number on which the syslog server is listening on>

# Provide certificate to the client to authenticate against the server
Set-SyslogClient -pfxBinary <Byte[] of pfx file> -CertPassword <SecureString, password for accessing the pfx file>
```

Klientský certifikát musí mít stejným kořenem jako se zadal při nasazení služby Azure Stack. Také musí obsahovat privátní klíč.

```powershell
##Example on how to set your syslog client with the certificate for mutual authentication.
##This example script must be run from your hardware lifecycle host or privileged access workstation.

$ErcsNodeName = "<yourPEP>"
$password = ConvertTo-SecureString -String "<your cloudAdmin account password" -AsPlainText -Force
 
$cloudAdmin = "<your cloudAdmin account name>"
$CloudAdminCred = New-Object System.Management.Automation.PSCredential ($cloudAdmin, $password)
 
$certPassword = $password
$certContent = Get-Content -Path C:\cert\<yourClientCertificate>.pfx -Encoding Byte
 
$params = @{ 
    ComputerName = $ErcsNodeName 
    Credential = $CloudAdminCred 
    ConfigurationName = "PrivilegedEndpoint" 
}

$session = New-PSSession @params
 
$params = @{ 
    Session = $session 
    ArgumentList = @($certContent, $certPassword) 
}
Write-Verbose "Invoking cmdlet to set syslog client certificate..." -Verbose 
Invoke-Command @params -ScriptBlock { 
    param($CertContent, $CertPassword) 
    Set-SyslogClient -PfxBinary $CertContent -CertPassword $CertPassword }
```

### <a name="configuring-syslog-forwarding-with-tcp-server-authentication-and-tls-12-encryption"></a>Konfigurace předávání protokolu syslog s TCP, Server ověřování a šifrování TLS 1.2

V této konfiguraci syslog klienta ve službě Azure Stack přeposílá zprávy na server syslog přes protokol TCP, pomocí šifrování TLS 1.2. Během počáteční handshake klient také ověří, že server poskytuje platný důvěryhodný certifikát. Tato konfigurace zabraňuje klienta pro odesílání zpráv do nedůvěryhodných cílů.
Ověřování a šifrování pomocí protokolu TCP je výchozí konfigurace a představuje minimální úroveň zabezpečení, které společnost Microsoft doporučuje pro produkční prostředí. 

```powershell
Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -ServerPort <Port number on which the syslog server is listening on>
```

V případě, že chcete testovat integraci váš server syslog s klientem služby Azure Stack pomocí certifikátu podepsaného svým držitelem a/nebo nedůvěryhodných, můžete použít tyto příznaky pro přeskočení ověření serveru prováděných klientem během počáteční metody handshake.

```powershell
 #Skip validation of the Common Name value in the server certificate. Use this flag if you provide an IP address for your syslog server
 Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -ServerPort <Port number on which the syslog server is listening on>
 -SkipCNCheck

 #Skip entirely the server certificate validation
 Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -ServerPort <Port number on which the syslog server is listening on>
 -SkipCertificateCheck
```

> [!IMPORTANT]
> Společnost Microsoft nedoporučuje použití příznaku - SkipCertificateCheck pro produkční prostředí. 

### <a name="configuring-syslog-forwarding-with-tcp-and-no-encryption"></a>Konfigurace předávání protokolu syslog pomocí protokolu TCP a bez šifrování

V této konfiguraci syslog klienta ve službě Azure Stack přeposílá zprávy na server syslog přes protokol TCP, se bez šifrování. Klient neověřuje identity serveru se ani poskytuje vlastní identity k serveru pro ověření. 

```powershell
Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -ServerPort <Port number on which the syslog server is listening on> -NoEncryption
```

> [!IMPORTANT]
> Společnost Microsoft nedoporučuje používat tuto konfiguraci pro produkční prostředí. 


### <a name="configuring-syslog-forwarding-with-udp-and-no-encryption"></a>Konfigurace předávání protokolu syslog UDP a bez šifrování

V této konfiguraci syslog klienta ve službě Azure Stack přeposílá zprávy na server syslog přes protokol UDP, se bez šifrování. Klient neověřuje identity serveru se ani poskytuje vlastní identity k serveru pro ověření. 

```powershell
Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -ServerPort <Port number on which the syslog server is listening on> -UseUDP
```

I UDP se žádné šifrování je nejjednodušší konfiguraci, neposkytuje žádné ochranu proti útokům man-in-the-middle a odposlouchávání zpráv. 

> [!IMPORTANT]
> Společnost Microsoft nedoporučuje používat tuto konfiguraci pro produkční prostředí. 


## <a name="removing-syslog-forwarding-configuration"></a>Odebírá se konfigurace předávání protokolu syslog

Chcete úplně odebrat konfiguraci serveru syslog a ukončit předávání syslog:

**Odebrat z klienta Konfigurace serveru syslog**

```PowerShell  
Set-SyslogServer -Remove
```

**Odebrat certifikát klienta z klienta**

```PowerShell  
Set-SyslogClient -RemoveCertificate
```

## <a name="verifying-the-syslog-setup"></a>Ověřuje se nastavení protokolu syslog

Pokud klienta syslog se úspěšně připojí k vašemu syslog serveru, by brzo začít přijímat události. Pokud nevidíte žádné události, zkontrolujte konfiguraci vašeho klienta protokolu syslog spuštěním následující rutiny:

**Ověření konfigurace serveru syslog klienta**

```PowerShell  
Get-SyslogServer
```

**Ověření instalace certifikátu klienta syslog**

```PowerShell  
Get-SyslogClient
```

## <a name="syslog-message-schema"></a>Schéma zprávy Syslog

Předávání syslog infrastruktury Azure stacku odesílá zprávy ve formátu v Common Event Format (CEF).
Každou zprávu syslog je uspořádáno na základě tohoto schématu: 

```Syslog
<Time> <Host> <CEF payload>
```

Datová část CEF je založená na struktuře níže, ale mapování pro každé pole se liší v závislosti na typu zprávy (Windows událost, výstraha vytvořena, výstraha uzavřena).

```CEF
# Common Event Format schema
CEF: <Version>|<Device Vendor>|<Device Product>|<Device Version>|<Signature ID>|<Name>|<Severity>|<Extensions>
* Version: 0.0
* Device Vendor: Microsoft
* Device Product: Microsoft Azure Stack
* Device Version: 1.0
```

### <a name="cef-mapping-for-privileged-endpoint-events"></a>CEF mapování pro privilegované koncový bod události

```
Prefix fields
* Signature ID: Microsoft-AzureStack-PrivilegedEndpoint: <PEP Event ID>
* Name: <PEP Task Name>
* Severity: mapped from PEP Level (details see the PEP Severity table below)
```

Tabulka událostí pro privilegované koncový bod:

| Událost | ID události období | Název úlohy období | Severity |
|-------|--------------| --------------|----------|
|PrivilegedEndpointAccessed|1000|PrivilegedEndpointAccessedEvent|5|
|SupportSessionTokenRequested |1001|SupportSessionTokenRequestedEvent|5|
|SupportSessionDevelopmentTokenRequested |1002|SupportSessionDevelopmentTokenRequestedEvent|5|
|SupportSessionUnlocked |1003|SupportSessionUnlockedEvent|10|
|SupportSessionFailedToUnlock |1004|SupportSessionFailedToUnlockEvent|10|
|PrivilegedEndpointClosed |1005|PrivilegedEndpointClosedEvent|5|
|NewCloudAdminUser |1006|NewCloudAdminUserEvent|10|
|RemoveCloudAdminUser |1007|RemoveCloudAdminUserEvent|10|
|SetCloudAdminUserPassword |1008|SetCloudAdminUserPasswordEvent|5|
|GetCloudAdminPasswordRecoveryToken |1009|GetCloudAdminPasswordRecoveryTokenEvent|10|
|ResetCloudAdminPassword |1010|ResetCloudAdminPasswordEvent|10|

Tabulka závažnost období:

| Severity | Úroveň | Číselná hodnota |
|----------|-------| ----------------|
|0|Nedefinováno|Hodnota: 0. Označuje protokolů na všech úrovních|
|10|Kritická|Hodnota: 1. Označuje informace pro kritická výstraha|
|8|Chyba| Hodnota: 2. Označuje chybu v protokolech|
|5|Upozornění|Hodnota: 3. Označuje informace pro upozornění|
|2|Informace|Hodnota: 4. Označuje informace pro informační zpráva|
|0|Podrobný|Hodnota: 5. Označuje protokolů na všech úrovních|

### <a name="cef-mapping-for-recovery-endpoint-events"></a>CEF mapování pro události koncového bodu obnovení

```
Prefix fields
* Signature ID: Microsoft-AzureStack-PrivilegedEndpoint: <REP Event ID>
* Name: <REP Task Name>
* Severity: mapped from REP Level (details see the REP Severity table below)
```

Tabulka událostí pro koncový bod obnovení:

| Událost | ID události zástupce | Název úlohy zástupce | Severity |
|-------|--------------| --------------|----------|
|RecoveryEndpointAccessed |1011|RecoveryEndpointAccessedEvent|5|
|RecoverySessionTokenRequested |1012|RecoverySessionTokenRequestedEvent |5|
|RecoverySessionDevelopmentTokenRequested |1013|RecoverySessionDevelopmentTokenRequestedEvent|5|
|RecoverySessionUnlocked |1014|RecoverySessionUnlockedEvent |10|
|RecoverySessionFailedToUnlock |1015|RecoverySessionFailedToUnlockEvent|10|
|RecoveryEndpointClosed |1016|RecoveryEndpointClosedEvent|5|

Tabulka zástupce závažnosti:
| Severity | Úroveň | Číselná hodnota |
|----------|-------| ----------------|
|0|Nedefinováno|Hodnota: 0. Označuje protokolů na všech úrovních|
|10|Kritická|Hodnota: 1. Označuje informace pro kritická výstraha|
|8|Chyba| Hodnota: 2. Označuje chybu v protokolech|
|5|Upozornění|Hodnota: 3. Označuje informace pro upozornění|
|2|Informace|Hodnota: 4. Označuje informace pro informační zpráva|
|0|Podrobný|Hodnota: 5. Označuje protokolů na všech úrovních|

### <a name="cef-mapping-for-windows-events"></a>CEF mapování pro události Windows

```
* Signature ID: ProviderName:EventID
* Name: TaskName
* Severity: Level (for details, see the severity table below)
* Extension: Custom Extension Name (for details, see the Custom Extension table below)
```

Tabulka závažnosti události Windows:
| Hodnota závažnosti CEF | Úroveň události Windows | Číselná hodnota |
|--------------------|---------------------| ----------------|
|0|Nedefinováno|Hodnota: 0. Označuje protokolů na všech úrovních|
|10|Kritická|Hodnota: 1. Označuje informace pro kritická výstraha|
|8|Chyba| Hodnota: 2. Označuje chybu v protokolech|
|5|Upozornění|Hodnota: 3. Označuje informace pro upozornění|
|2|Informace|Hodnota: 4. Označuje informace pro informační zpráva|
|0|Podrobný|Hodnota: 5. Označuje protokolů na všech úrovních|

Tabulka vlastní rozšíření pro události Windows ve službě Azure Stack:
| Název vlastní rozšíření | Příklad události Windows | 
|-----------------------|---------|
|MasChannel | Systémový|
|MasComputer | test.azurestack.contoso.com|
|MasCorrelationActivityID| C8F40D7C-3764-423B-A4FA-C994442238AF|
|MasCorrelationRelatedActivityID| C8F40D7C-3764-423B-A4FA-C994442238AF|
|MasEventData| svchost!!4132,G,0!!!!EseDiskFlushConsistency!!ESENT!!0x800000|
|MasEventDescription| Nastavení zásad skupiny pro uživatele byla úspěšně zpracována. Nebyly od poslední úspěšné zpracování zásad skupiny se nezjistily žádné změny.|
|MasEventID|1501|
|MasEventRecordID|26637|
|MasExecutionProcessID | 29380|
|MasExecutionThreadID |25480|
|MasKeywords |0x8000000000000000|
|MasKeywordName |Úspěšný audit|
|MasLevel |4|
|MasOpcode |1|
|MasOpcodeName |informace|
|MasProviderEventSourceName ||
|MasProviderGuid |AEA1B4FA-97D1-45F2-A64C-4D69FFFD92C9|
|MasProviderName |Microsoft-Windows-GroupPolicy|
|MasSecurityUserId |\<Windows SID\> |
|MasTask |0|
|MasTaskCategory| Vytvoření procesu|
|MasUserData|KB4093112!!5112!!Installed!!0x0!!WindowsUpdateAgent Xpath: /Event/UserData/*|
|MasVersion|0|

### <a name="cef-mapping-for-alerts-created"></a>CEF mapování pro vytvoření výstrahy

```
* Signature ID: Microsoft Azure Stack Alert Creation : FaultTypeId
* Name: FaultTypeId : AlertId
* Severity: Alert Severity (for details, see alerts severity table below)
* Extension: Custom Extension Name (for details, see the Custom Extension table below)
```

Tabulka závažnost výstrahy:
| Severity | Úroveň |
|----------|-------|
|0|Nedefinováno|
|10|Kritická|
|5|Upozornění|

Vlastní rozšíření tabulky pro upozornění vytvořená ve službě Azure Stack:
| Název vlastní rozšíření | Příklad: | 
|-----------------------|---------|
|MasEventDescription|POPIS: Uživatelský účet \<TestUser\> bylo vytvořeno za \<TestDomain\>. Je možné bezpečnostní riziko. --OPRAVY: Kontaktujte podporu. K vyřešení tohoto problému je nutné pomoc zákazníkům. Nepokoušejte se vyřešit tento problém bez jejich pomoci. Než otevřete žádost o podporu, spusťte proces shromažďování souborů protokolů pomocí pokynů z https://aka.ms/azurestacklogfiles |

### <a name="cef-mapping-for-alerts-closed"></a>CEF mapování pro zavření výstrahy

```
* Signature ID: Microsoft Azure Stack Alert Creation : FaultTypeId
* Name: FaultTypeId : AlertId
* Severity: Information
```

Následující příklad ukazuje zprávy syslog s datovou částí CEF:
```
2018:05:17:-23:59:28 -07:00 TestHost CEF:0.0|Microsoft|Microsoft Azure Stack|1.0|3|TITLE: User Account Created -- DESCRIPTION: A user account \<TestUser\> was created for \<TestDomain\>. It's a potential security risk. -- REMEDIATION: Please contact Support. Customer Assistance is required to resolve this issue. Do not try to resolve this issue without their assistance. Before you open a support request, start the log file collection process using the guidance from https://aka.ms/azurestacklogfiles|10
```

## <a name="next-steps"></a>Další postup

[Zásady údržby](azure-stack-servicing-policy.md)