---
title: Reference k rutinám Start AzsReadinessChecker | Dokumentace Microsoftu
description: Nápověda rutiny prostředí PowerShell pro modul Kontrola připravenosti Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/30/2018
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 12/04/2018
ms.openlocfilehash: f920059f97f43a2ac3c48dad1c8f999833f6add1
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2019
ms.locfileid: "58757768"
---
# <a name="start-azsreadinesschecker-cmdlet-reference"></a>Reference k rutinám Start AzsReadinessChecker

Modul: **Microsoft.AzureStack.ReadinessChecker**

Tento modul obsahuje pouze jedné rutiny. Rutina provede jednu nebo více funkcí před nasazením nebo předem údržby pro Azure Stack.

## <a name="syntax"></a>Syntaxe

```powershell
Start-AzsReadinessChecker
       [-CertificatePath <String>]
       -PfxPassword <SecureString>
       -RegionName <String>
       -FQDN <String>
       -IdentitySystem <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       [-CertificatePath <String>]
       -PfxPassword <SecureString>
       -DeploymentDataJSONPath <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -PaaSCertificates <Hashtable>
       -DeploymentDataJSONPath <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -PaaSCertificates <Hashtable>
       -RegionName <String>
       -FQDN <String>
       -IdentitySystem <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -RegionName <String>
       -FQDN <String>
       -IdentitySystem <String>
       -Subject <OrderedDictionary>
       -RequestType <String>
       [-IncludePaaS]
       -OutputRequestPath <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -PfxPassword <SecureString>
       -PfxPath <String>
       -ExportPFXPath <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -AADServiceAdministrator <PSCredential>
       -AADDirectoryTenantName <String>
       -IdentitySystem <String>
       -AzureEnvironment <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -AADServiceAdministrator <PSCredential>
       -DeploymentDataJSONPath <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -RegistrationAccount <PSCredential>
       -RegistrationSubscriptionID <Guid>
       -AzureEnvironment <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -RegistrationAccount <PSCredential>
       -RegistrationSubscriptionID <Guid>
       -DeploymentDataJSONPath <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -ReportPath <String>
       [-ReportSections <String>]
       [-Summary]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

## <a name="description"></a>Popis

**Start AzsReadinessChecker** rutina ověří certifikáty, účty Azure, předplatná Azure a Azure Active Directory, jichž. Spusťte ověření před nasazením služby Azure Stack nebo před údržby akce, například otočení tajných kódů služby Azure Stack. Rutina také umožňuje vygenerovat certifikát Podepisování žádostí o certifikáty infrastruktury a volitelně PaaS certifikáty. A konečně rutina je znovu zabalit certifikáty PFX řešení běžných potíží s balení.

## <a name="examples"></a>Příklady

### <a name="example-generate-certificate-signing-request"></a>Příklad: generování žádosti o podepsání certifikátu

```powershell
$regionName = 'east'
$externalFQDN = 'azurestack.contoso.com'
$subjectHash = [ordered]@{"OU"="AzureStack";"O"="Microsoft";"L"="Redmond";"ST"="Washington";"C"="US"}
Start-AzsReadinessChecker -regionName $regionName -externalFQDN $externalFQDN -subject $subjectHash -IdentitySystem ADFS -requestType MultipleCSR
```

V tomto příkladu `Start-AzsReadinessChecker` generuje více (nástroje CSR) žádosti o podepsání certifikátu pro certifikáty, které jsou vhodné pro nasazení služby AD FS Azure Stack s názvem oblasti **východ** a externí název FQDN  **azurestack.contoso.com**.

### <a name="example-validate-certificates"></a>Příklad: ověřování certifikátů

```powershell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -CertificatePath .\Certificates\ -PfxPassword $password -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD
```

V tomto příkladu je požadované pro zabezpečení, heslo souboru PFX a `Start-AzsReadinessChecker` kontroluje složce relativní **certifikáty** pro certifikáty platné pro nasazení služby AAD s názvem oblasti **východ** a externí plně kvalifikovaný název domény **azurestack.contoso.com**.

### <a name="example-validate-certificates-with-deployment-data-deployment-and-support"></a>Příklad: ověření certifikátů pomocí nasazení dat. (nasazení a podporu)

```powershell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -CertificatePath .\Certificates\ -PfxPassword $password -DeploymentDataJSONPath .\deploymentdata.json
```

V tomto příkladu nasazení a podporu heslo PFX se vyžaduje pro zabezpečení, a `Start-AzsReadinessChecker` kontroluje složce relativní **certifikáty** pro certifikáty platné pro nasazení, kde je identita, oblasti a externí plně kvalifikovaný název domény čtení ze souboru JSON nasazení dat. vygenerovaný pro nasazení.

### <a name="example-validate-paas-certificates"></a>Příklad: ověřování certifikátů PaaS

```powershell
$PaaSCertificates = @{
    'PaaSDBCert' = @{'pfxPath' = '<Path to DBAdapter PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSDefaultCert' = @{'pfxPath' = '<Path to Default PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSAPICert' = @{'pfxPath' = '<Path to API PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSFTPCert' = @{'pfxPath' = '<Path to FTP PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSSSOCert' = @{'pfxPath' = '<Path to SSO PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
}
Start-AzsReadinessChecker -PaaSCertificates $PaaSCertificates – RegionName east -FQDN azurestack.contoso.com
```

V tomto příkladu je vytvořena zatřiďovací tabulku s cesty a heslo pro každý certifikát PaaS. Certifikáty můžete vynechat. `Start-AzsReadinessChecker` kontroluje, zda každá PFX cesta existuje a ověřuje pomocí oblast **východ** a plně kvalifikovaný název domény externího **azurestack.contoso.com**.

### <a name="example-validate-paas-certificates-with-deployment-data"></a>Příklad: ověření certifikátů PaaS pomocí nasazení dat.

```powershell
$PaaSCertificates = @{
    'PaaSDBCert' = @{'pfxPath' = '<Path to DBAdapter PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSDefaultCert' = @{'pfxPath' = '<Path to Default PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSAPICert' = @{'pfxPath' = '<Path to API PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSFTPCert' = @{'pfxPath' = '<Path to FTP PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSSSOCert' = @{'pfxPath' = '<Path to SSO PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
}
Start-AzsReadinessChecker -PaaSCertificates $PaaSCertificates -DeploymentDataJSONPath .\deploymentdata.json
```

V tomto příkladu je vytvořena zatřiďovací tabulku s cesty a heslo pro každý certifikát PaaS. Certifikáty můžete vynechat. `Start-AzsReadinessChecker` ověří, že každá PFX cesta existuje a ověřuje pomocí oblast, a plně kvalifikovaný název domény externího čtení ze souboru JSON nasazení dat. vygenerovaný pro nasazení.

### <a name="example-validate-azure-identity"></a>Příklad: ověření identit Azure

```powershell
$serviceAdminCredential = Get-Credential -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant e.g. serviceadmin@contoso.onmicrosoft.com"
# Supported values for the <environment name> parameter are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
Start-AzsReadinessChecker -AADServiceAdministrator $serviceAdminCredential -AzureEnvironment "<environment name>" -AzureDirectoryTenantName azurestack.contoso.com
```

V tomto příkladu jsou požadované pro zabezpečení, přihlašovací údaje účtu správce služby a `Start-AzsReadinessChecker` ověří, že účet Azure a Azure Active Directory jsou platné pro nasazení s názvem adresáře tenanta služby AAD  **azurestack.contoso.com**.

### <a name="example-validate-azure-identity-with-deployment-data-deployment-support"></a>Příklad: ověření identit Azure pomocí nasazení dat. (podpora nasazení)

```PowerSHell
$serviceAdminCredential = Get-Credential -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant e.g. serviceadmin@contoso.onmicrosoft.com"
Start-AzsReadinessChecker -AADServiceAdministrator $serviceAdminCredential -DeploymentDataJSONPath .\contoso-depploymentdata.json
```

V tomto příkladu jsou požadované pro zabezpečení, přihlašovací údaje účtu správce služby a `Start-AzsReadinessChecker` ověří, že účet Azure a Azure Active Directory jsou platné pro nasazení služby AAD, kde **AzureCloud** a **TenantName** jsou čtení ze souboru JSON nasazení dat. vygenerovaný pro nasazení.

### <a name="example-validate-azure-registration"></a>Příklad: ověření registrace služby Azure

```powershell
$registrationCredential = Get-Credential -Message "Enter Credentials for Subscription Owner e.g. subscriptionowner@contoso.onmicrosoft.com"
$subscriptionID = "<subscription ID"
# Supported values for the <environment name> parameter are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
Start-AzsReadinessChecker -RegistrationAccount $registrationCredential -RegistrationSubscriptionID $subscriptionID -AzureEnvironment "<environment name>"
```

V tomto příkladu jsou požadovány pro zabezpečení, přihlašovací údaje vlastníka předplatného a `Start-AzsReadinessChecker` pak provede ověření pro daný účet a předplatné, abyste zajistili, je možné pro registraci Azure Stack.

### <a name="example-validate-azure-registration-with-deployment-data-deployment-team"></a>Příklad: ověření registrace služby Azure pomocí nasazení dat. (týmu nasazení)

```powershell
$registrationCredential = Get-Credential -Message "Enter Credentials for Subscription Owner e.g. subscriptionowner@contoso.onmicrosoft.com"
$subscriptionID = "<subscription ID>"
Start-AzsReadinessChecker -RegistrationAccount $registrationCredential -RegistrationSubscriptionID $subscriptionID -DeploymentDataJSONPath .\contoso-deploymentdata.json
```

V tomto příkladu jsou požadovány pro zabezpečení, přihlašovací údaje vlastníka předplatného a `Start-AzsReadinessChecker` pak provede ověření pro daný účet a předplatné, abyste zajistili, je možné pro registraci Azure Stack, kde jsou další podrobnosti o čtení z nasazení dat. soubor JSON vygenerovaný pro nasazení.

### <a name="example-importexport-pfx-package"></a>Příklad: import a export balíčků PFX

```powershell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -PfxPassword $password -PfxPath .\certificates\ssl.pfx -ExportPFXPath .\certificates\ssl_new.pfx
```

V tomto příkladu je požadované pro zabezpečení heslo souboru PFX. Soubor Ssl.pfx je importovat do úložiště certifikátů místního počítače, znovu exportovat pomocí stejného hesla a uložit jako Ssl_new.pfx. Tento postup se používá při ověřování certifikátu příznakem, že privátní klíč nemá **místního počítače** sadu atributů je přerušený řetěz certifikátů, irelevantní certifikáty nacházejí v PFX nebo řetěz certifikátů v nesprávném pořadí.

### <a name="example-view-validation-report-deployment-support"></a>Příklad: Zobrazit sestavu ověření (podpora nasazení)

```powershell
Start-AzsReadinessChecker -ReportPath Contoso-AzsReadinessReport.json
```

V tomto příkladu nasazení a podporu týmu obdrží sestava připravenosti od zákazníka (Contoso) a používá `Start-AzsReadinessChecker` zobrazíte stav spuštění ověření společnosti Contoso provést.

### <a name="example-view-validation-report-summary-for-certificate-validation-only-deployment-and-support"></a>Příklad: Zobrazit sestavu ověření souhrnu pro certifikát ověření pouze (nasazení a podporu)

```powershell
Start-AzsReadinessChecker -ReportPath Contoso-AzsReadinessReport.json -ReportSections Certificate -Summary
```

V tomto příkladu nasazení a podporu týmu obdrží sestava připravenosti od zákazníka (Contoso) a používá `Start-AzsReadinessChecker` Chcete-li zobrazit souhrnný stav spuštění ověření certifikátu společnosti Contoso provést.

## <a name="required-parameters"></a>Požadované parametry

### <a name="-regionname"></a>-RegionName

Určuje název oblasti nasazení Azure Stack.

|  |  |
|----------------------------|--------------|
|Zadejte:                       |String        |
|Pozice:                   |s názvem         |
|Výchozí hodnota:              |Žádný          |
|Přijměte kanálový vstup:      |False         |
|Přijměte zástupné znaky: |False         |

### <a name="-fqdn"></a>-FQDN

Určuje nasazení Azure stacku externí plně kvalifikovaný název domény, také alias **ExternalFQDN** a **ExternalDomainName**.

|  |  |
|----------------------------|--------------|
|Zadejte:                       |String        |
|Pozice:                   |s názvem         |
|Výchozí hodnota:              |ExternalFQDN, ExternalDomainName |
|Přijměte kanálový vstup:      |False         |
|Přijměte zástupné znaky: |False         |

### <a name="-identitysystem"></a>-IdentitySystem

Určuje Azure Stack nasazení identity systému platné hodnoty, AAD nebo AD FS, Azure Active Directory a Active Directory Federated Services, v uvedeném pořadí.

|  |  |
|----------------------------|--------------|
|Zadejte:                       |String        |
|Pozice:                   |s názvem         |
|Výchozí hodnota:              |Žádný          |
|Platné hodnoty:               |"AAD", "ADFS"  |
|Přijměte kanálový vstup:      |False         |
|Přijměte zástupné znaky: |False         |

### <a name="-pfxpassword"></a>-PfxPassword

Určuje heslo přidružené soubory certifikátů PFX.

|  |  |
|----------------------------|---------|
|Zadejte:                       |SecureString |
|Pozice:                   |s názvem    |
|Výchozí hodnota:              |Žádný     |
|Přijměte kanálový vstup:      |False    |
|Přijměte zástupné znaky: |False    |

### <a name="-paascertificates"></a>-PaaSCertificates

Určuje zatřiďovací tabulku obsahující cesty a heslo pro certifikáty PaaS.

|  |  |
|----------------------------|---------|
|Zadejte:                       |Zatřiďovací tabulka |
|Pozice:                   |s názvem    |
|Výchozí hodnota:              |Žádný     |
|Přijměte kanálový vstup:      |False    |
|Přijměte zástupné znaky: |False    |

### <a name="-deploymentdatajsonpath"></a>-DeploymentDataJSONPath

Určuje konfigurační soubor JSON nasazení dat. Azure Stack. Tento soubor je vygenerován pro nasazení.

|  |  |
|----------------------------|---------|
|Zadejte:                       |String   |
|Pozice:                   |s názvem    |
|Výchozí hodnota:              |Žádný     |
|Přijměte kanálový vstup:      |False    |
|Přijměte zástupné znaky: |False    |

### <a name="-pfxpath"></a>-PfxPath

Určuje cestu k problematické certifikát, který vyžaduje rutiny import/export, pokud chcete vyřešit, jak je uvedeno v ověření certifikátu v tomto nástroji.

|  |  |
|----------------------------|---------|
|Zadejte:                       |String   |
|Pozice:                   |s názvem    |
|Výchozí hodnota:              |Žádný     |
|Přijměte kanálový vstup:      |False    |
|Přijměte zástupné znaky: |False    |

### <a name="-exportpfxpath"></a>-ExportPFXPath  

Určuje cílovou cestu pro výsledný soubor PFX z rutiny importu/exportu.  

|  |  |
|----------------------------|---------|
|Zadejte:                       |String   |
|Pozice:                   |s názvem    |
|Výchozí hodnota:              |Žádný     |
|Přijměte kanálový vstup:      |False    |
|Přijměte zástupné znaky: |False    |

### <a name="-subject"></a>– Předmět

Určuje seřazený slovník předmětu pro generování žádosti o certifikát.

|  |  |
|----------------------------|---------|
|Zadejte:                       |Kolekce OrderedDictionary   |
|Pozice:                   |s názvem    |
|Výchozí hodnota:              |Žádný     |
|Přijměte kanálový vstup:      |False    |
|Přijměte zástupné znaky: |False    |

### <a name="-requesttype"></a>Typ RequestType-

Určuje typ SAN žádosti o certifikát. Platné hodnoty jsou **MultipleCSR**, **SingleCSR**.

- **MultipleCSR** generuje více žádosti o certifikát, jeden pro každou službu.
- **SingleCSR** vygeneruje jedna žádost o certifikát pro všechny služby.

|  |  |
|----------------------------|---------|
|Zadejte:                       |String   |
|Pozice:                   |s názvem    |
|Výchozí hodnota:              |Žádný     |
|Platné hodnoty:               |'MultipleCSR','SingleCSR' |
|Přijměte kanálový vstup:      |False    |
|Přijměte zástupné znaky: |False    |

### <a name="-outputrequestpath"></a>-OutputRequestPath

Určuje cílovou cestu pro soubory žádosti o certifikát. Adresář již musí existovat.

|  |  |
|----------------------------|---------|
|Zadejte:                       |String   |
|Pozice:                   |s názvem    |
|Výchozí hodnota:              |Žádný     |
|Přijměte kanálový vstup:      |False    |
|Přijměte zástupné znaky: |False    |

### <a name="-aadserviceadministrator"></a>-AADServiceAdministrator

Určuje správce služby Azure Active Directory se použije pro nasazení Azure stacku.

|  |  |
|----------------------------|---------|
|Zadejte:                       |PSCredential   |
|Pozice:                   |s názvem    |
|Výchozí hodnota:              |Žádný     |
|Přijměte kanálový vstup:      |False    |
|Přijměte zástupné znaky: |False    |

### <a name="-aaddirectorytenantname"></a>-AADDirectoryTenantName

Určuje název služby Azure Active Directory se použije pro nasazení Azure stacku.

|  |  |
|----------------------------|---------|
|Zadejte:                       |String   |
|Pozice:                   |s názvem    |
|Výchozí hodnota:              |Žádný     |
|Přijměte kanálový vstup:      |False    |
|Přijměte zástupné znaky: |False    |

### <a name="-azureenvironment"></a>-AzureEnvironment

Určuje instanci služby Azure obsahující účtů, adresářů a předplatných se použije pro nasazení Azure stacku a registraci.

|  |  |
|----------------------------|---------|
|Zadejte:                       |String   |
|Pozice:                   |s názvem    |
|Výchozí hodnota:              |Žádný     |
|Platné hodnoty:               |'AzureCloud','AzureChinaCloud','AzureUSGovernment' |
|Přijměte kanálový vstup:      |False    |
|Přijměte zástupné znaky: |False    |

### <a name="-registrationaccount"></a>-RegistrationAccount

Určuje účet registrace pro registrace Azure Stack.

|  |  |
|----------------------------|---------|
|Zadejte:                       |String   |
|Pozice:                   |s názvem    |
|Výchozí hodnota:              |Žádný     |
|Přijměte kanálový vstup:      |False    |
|Přijměte zástupné znaky: |False    |

### <a name="-registrationsubscriptionid"></a>-RegistrationSubscriptionID

Určuje ID předplatného registrace pro registrace Azure Stack.

|  |  |
|----------------------------|---------|
|Zadejte:                       |Guid     |
|Pozice:                   |s názvem    |
|Výchozí hodnota:              |Žádný     |
|Přijměte kanálový vstup:      |False    |
|Přijměte zástupné znaky: |False    |

### <a name="-reportpath"></a>-ReportPath

Určuje cestu pro sestavu připravenosti, výchozí hodnota je aktuální adresář a výchozí název sestavy.

|  |  |
|----------------------------|---------|
|Zadejte:                       |String   |
|Pozice:                   |s názvem    |
|Výchozí hodnota:              |Vše      |
|Přijměte kanálový vstup:      |False    |
|Přijměte zástupné znaky: |False    |

## <a name="optional-parameters"></a>Volitelné parametry

### <a name="-certificatepath"></a>-CertificatePath

Určuje cestu, pod kterým jenom ten certifikát požadovaný certifikát složky jsou k dispozici.

Požadované složky pro nasazení Azure Stack pomocí systému identit Azure Active Directory jsou:

Public, trezor klíčů, KeyVaultInternal, veřejný portál ARM ACSBlob ACSQueue, ACSTable, portál pro správu, ARM správce

Požadované složky pro nasazení do systému identit Active Directory Federation Services jsou služby Azure Stack:

ACSBlob, ACSQueue, ACSTable, ADFS, Admin Portal, ARM Admin, ARM Public, Graph, KeyVault, KeyVaultInternal, Public Portal

|  |  |
|----------------------------|---------|
|Zadejte:                       |String   |
|Pozice:                   |s názvem    |
|Výchozí hodnota:              |.\Certificates |
|Přijměte kanálový vstup:      |False    |
|Přijměte zástupné znaky: |False    |

### <a name="-includepaas"></a>-IncludePaaS  

Určuje, zda by měl PaaS služby/hostitele přidat do žádosti o certifikát.

|  |  |
|----------------------------|------------------|
|Zadejte:                       |SwitchParameter   |
|Pozice:                   |s názvem             |
|Výchozí hodnota:              |False             |
|Přijměte kanálový vstup:      |False             |
|Přijměte zástupné znaky: |False             |

### <a name="-reportsections"></a>-ReportSections

Určuje, zda jenom zobrazit souhrn, sestavy vynechá podrobností.

|  |  |
|----------------------------|---------|
|Zadejte:                       |String   |
|Pozice:                   |s názvem    |
|Výchozí hodnota:              |Vše      |
|Platné hodnoty:               |'Certificate','AzureRegistration','AzureIdentity','Jobs','All' |
|Přijměte kanálový vstup:      |False    |
|Přijměte zástupné znaky: |False    |

### <a name="-summary"></a>-Summary

Určuje, zda jenom zobrazit souhrn, sestavy vynechá podrobností.

|  |  |
|----------------------------|------------------|
|Zadejte:                       |SwitchParameter   |
|Pozice:                   |s názvem             |
|Výchozí hodnota:              |False             |
|Přijměte kanálový vstup:      |False             |
|Přijměte zástupné znaky: |False             |

### <a name="-cleanreport"></a>-CleanReport

Odebere předchozí historie spuštění a ověřování a zapíše ověření do nové sestavy.

|  |  |
|----------------------------|------------------|
|Zadejte:                       |SwitchParameter   |
|Aliasy:                    |cf                |
|Pozice:                   |s názvem             |
|Výchozí hodnota:              |False             |
|Přijměte kanálový vstup:      |False             |
|Přijměte zástupné znaky: |False             |

### <a name="-outputpath"></a>-OutputPath

Určuje vlastní cesta pro uložení sestavy připravenost JSON a podrobný soubor protokolu. Pokud cesta neexistuje, příkaz se pokusí vytvořit adresář.

|  |  |
|----------------------------|------------------|
|Zadejte:                       |String            |
|Pozice:                   |s názvem             |
|Výchozí hodnota:              |$ENV:TEMP\AzsReadinessChecker  |
|Přijměte kanálový vstup:      |False             |
|Přijměte zástupné znaky: |False             |

### <a name="-confirm"></a>– Potvrzení

Výzvy k potvrzení před spuštěním rutiny.

|  |  |
|----------------------------|------------------|
|Zadejte:                       |SwitchParameter   |
|Aliasy:                    |cf                |
|Pozice:                   |s názvem             |
|Výchozí hodnota:              |False             |
|Přijměte kanálový vstup:      |False             |
|Přijměte zástupné znaky: |False             |

### <a name="-whatif"></a>-WhatIf

Ukazuje, co by se stalo při spuštění rutiny. Rutina není spuštěna.

|  |  |
|----------------------------|------------------|
|Zadejte:                       |SwitchParameter   |
|Aliasy:                    |wi                |
|Pozice:                   |s názvem             |
|Výchozí hodnota:              |False             |
|Přijměte kanálový vstup:      |False             |
|Přijměte zástupné znaky: |False             |
