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
ms.topic: get-started-article
ms.date: 12/04/2018
ms.author: sethm
ms.reviewer: unknown
ms.openlocfilehash: af959507fc2e0d1b68f547d2856eb7020d3ed5c6
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2019
ms.locfileid: "54247568"
---
# <a name="start-azsreadinesschecker-cmdlet-reference"></a>Reference k rutinám Start AzsReadinessChecker

Modul: Microsoft.AzureStack.ReadinessChecker

Tento modul obsahuje pouze jedné rutiny.  Tato rutina provede jednu nebo více funkcí před nasazením nebo předem údržby pro Azure Stack.

## <a name="syntax"></a>Syntaxe

```PowerShell
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

```PowerShell
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

```PowerShell
Start-AzsReadinessChecker
       -PaaSCertificates <Hashtable>
       -DeploymentDataJSONPath <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```PowerShell
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

```PowerShell
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

```PowerShell
Start-AzsReadinessChecker
       -PfxPassword <SecureString>
       -PfxPath <String>
       -ExportPFXPath <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```PowerShell
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

```PowerShell
Start-AzsReadinessChecker
       -AADServiceAdministrator <PSCredential>
       -DeploymentDataJSONPath <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```PowerShell
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

```PowerShell
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

```PowerShell
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

**Start AzsReadinessChecker** rutina ověří certifikáty, účty Azure, předplatná Azure a Azure Active Directory, jichž. Spusťte ověření před nasazením služby Azure Stack nebo před údržby akce, jako je tajný kód otočení Azure Stack. Rutina slouží také k vygenerování žádosti o podepsání certifikátu pro certifikáty infrastruktury a volitelně PaaS certifikáty.  A konečně rutina je znovu zabalit certifikáty PFX řešení běžných potíží s balení.

## <a name="examples"></a>Příklady

### <a name="example-generate-certificate-signing-request"></a>Příklad: Vytvořit žádost o podepsání certifikátu

```PowerShell
$regionName = 'east'
$externalFQDN = 'azurestack.contoso.com'
$subjectHash = [ordered]@{"OU"="AzureStack";"O"="Microsoft";"L"="Redmond";"ST"="Washington";"C"="US"}
Start-AzsReadinessChecker -regionName $regionName -externalFQDN $externalFQDN -subject $subjectHash -IdentitySystem ADFS -requestType MultipleCSR
```

V tomto příkladu Start-AzsReadinessChecker generuje více požadavků (Podepsání certifikátu) pro certifikátů vhodných pro nasazení služby AD FS Azure Stack s názvem oblasti "východ" a externí název FQDN "azurestack.contoso.com"

### <a name="example-validate-certificates"></a>Příklad: Ověřování certifikátů

```PowerShell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -CertificatePath .\Certificates\ -PfxPassword $password -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD
```

V tomto příkladu heslo souboru PFX se zobrazí výzva k zadání bezpečně a počáteční AzsReadinessChecker kontroluje relativní složka "Certifikáty" pro certifikáty platné pro nasazení služby AAD s názvem oblasti "východ" a externí název FQDN "azurestack.contoso.com"

### <a name="example-validate-certificates-with-deployment-data-deployment-and-support"></a>Příklad: Ověření certifikátů pomocí nasazení dat. (nasazení a podporu)

```PowerShell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -CertificatePath .\Certificates\ -PfxPassword $password -DeploymentDataJSONPath .\deploymentdata.json
```

V tomto příkladu nasazení a podporu heslo souboru PFX se zobrazí výzva k zadání bezpečně a počáteční AzsReadinessChecker kontroluje relativní složka "Certifikáty" pro certifikáty platné pro nasazení, kde četlo identity, oblasti a externí plně kvalifikovaný název domény nasazení dat. soubor JSON vygenerovaný pro nasazení. 

### <a name="example-validate-paas-certificates"></a>Příklad: Ověření certifikátů PaaS

```PowerShell
$PaaSCertificates = @{
    'PaaSDBCert' = @{'pfxPath' = '<Path to DBAdapter PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSDefaultCert' = @{'pfxPath' = '<Path to Default PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSAPICert' = @{'pfxPath' = '<Path to API PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSFTPCert' = @{'pfxPath' = '<Path to FTP PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSSSOCert' = @{'pfxPath' = '<Path to SSO PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
}
Start-AzsReadinessChecker -PaaSCertificates $PaaSCertificates – RegionName east -FQDN azurestack.contoso.com
```

V tomto příkladu je vytvořena zatřiďovací tabulku s cesty a heslo pro každý certifikát PaaS. Certifikáty můžete vynechat. Start-AzsReadinessChecker zkontroluje každý PFX cesta existuje a ověří pomocí oblast východ"a"azurestack.contoso.com"externí plně kvalifikovaný název domény.

### <a name="example-validate-paas-certificates-with-deployment-data"></a>Příklad: Ověření certifikátů PaaS pomocí nasazení dat.

```PowerShell
$PaaSCertificates = @{
    'PaaSDBCert' = @{'pfxPath' = '<Path to DBAdapter PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSDefaultCert' = @{'pfxPath' = '<Path to Default PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSAPICert' = @{'pfxPath' = '<Path to API PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSFTPCert' = @{'pfxPath' = '<Path to FTP PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSSSOCert' = @{'pfxPath' = '<Path to SSO PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
}
Start-AzsReadinessChecker -PaaSCertificates $PaaSCertificates -DeploymentDataJSONPath .\deploymentdata.json
```

V tomto příkladu je vytvořena zatřiďovací tabulku s cesty a heslo pro každý certifikát PaaS. Certifikáty můžete vynechat. Start-AzsReadinessChecker zkontroluje každý PFX cesta existuje a je pomocí oblast ověří a plně kvalifikovaný název domény externího čtení ze souboru JSON nasazení dat. vygenerovaný pro nasazení. 

### <a name="example-validate-azure-identity"></a>Příklad: Ověření identit Azure

```PowerShell
$serviceAdminCredential = Get-Credential -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant e.g. serviceadmin@contoso.onmicrosoft.com"
# Supported values for the <environment name> parameter are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
Start-AzsReadinessChecker -AADServiceAdministrator $serviceAdminCredential -AzureEnvironment "<environment name>" -AzureDirectoryTenantName azurestack.contoso.com
```

V tomto příkladu přihlašovací údaje účtu správce služby se výzva k zadání bezpečně a počáteční AzsReadinessChecker kontroluje, že jsou platné pro nasazení služby AAD s název tenanta adresáře "azurestack.contoso.com" účet Azure a Azure Active Directory

### <a name="example-validate-azure-identity-with-deployment-data-deployment-support"></a>Příklad: Ověření identit Azure pomocí nasazení dat. (podpora nasazení)

```PowerSHell
$serviceAdminCredential = Get-Credential -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant e.g. serviceadmin@contoso.onmicrosoft.com"
Start-AzsReadinessChecker -AADServiceAdministrator $serviceAdminCredential -DeploymentDataJSONPath .\contoso-depploymentdata.json
```

V tomto příkladu přihlašovací údaje účtu správce služby se výzva k zadání bezpečně a počáteční AzsReadinessChecker kontroluje, že účet Azure a Azure Active Directory jsou platné pro nasazení služby AAD načteno AzureCloud a TenantName z nasazení dat. Soubor JSON generovaný pro nasazení.

### <a name="example-validate-azure-registration"></a>Příklad: Ověření registrace služby Azure

```PowerShell
$registrationCredential = Get-Credential -Message "Enter Credentials for Subscription Owner e.g. subscriptionowner@contoso.onmicrosoft.com"
$subscriptionID = "<subscription ID"
# Supported values for the <environment name> parameter are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
Start-AzsReadinessChecker -RegistrationAccount $registrationCredential -RegistrationSubscriptionID $subscriptionID -AzureEnvironment "<environment name>"
```

V tomto příkladu přihlašovací údaje vlastníka předplatného se výzva k zadání bezpečně a počáteční AzsReadinessChecker pak provede ověření pro daný účet a předplatné měli jistotu, že je možné pro registraci Azure Stack. 

### <a name="example-validate-azure-registration-with-deployment-data-deployment-team"></a>Příklad: Ověření registrace služby Azure pomocí nasazení dat. (týmu nasazení)

```PowerShell
$registrationCredential = Get-Credential -Message "Enter Credentials for Subscription Owner e.g. subscriptionowner@contoso.onmicrosoft.com"
$subscriptionID = "<subscription ID>"
Start-AzsReadinessChecker -RegistrationAccount $registrationCredential -RegistrationSubscriptionID $subscriptionID -DeploymentDataJSONPath .\contoso-deploymentdata.json
```

V tomto příkladu přihlašovací údaje vlastníka předplatného se výzva k zadání bezpečně a Start AzsReadinessChecker pak provede ověření pro daný účet a předplatné měli jistotu, že je možné pro registraci Azure Stack kde jsou další podrobnosti čtení ze souboru JSON nasazení dat. vygenerovaný pro nasazení.

### <a name="example-importexport-pfx-package"></a>Příklad: Balíček PFX Import/Export

```PowerShell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -PfxPassword $password -PfxPath .\certificates\ssl.pfx -ExportPFXPath .\certificates\ssl_new.pfx
```

V tomto příkladu heslo souboru PFX se zobrazí výzva pro bezpečné. Soubor ssl.pfx budou importovat do úložiště certifikátů místního počítače a znovu exportovat pomocí stejného hesla a uloženy jako ssl_new.pfx.  Tento postup je pro použití při ověřování certifikátu příznakem, že privátní klíč není k dispozici sadu atributů místního počítače, je přerušený řetěz certifikátů, irelevantní certifikáty nacházejí v PFX nebo řetěz certifikátů je v nesprávném pořadí.

### <a name="example-view-validation-report-deployment-support"></a>Příklad: Zobrazit sestavu ověření (podpora nasazení)

```PowerShell
Start-AzsReadinessChecker -ReportPath Contoso-AzsReadinessReport.json
```

V tomto příkladu nasazení a podporu týmu zpráva připravenost od zákazníka (Contoso) a Start AzsReadinessChecker slouží k zobrazení stavu provedení ověření společnosti Contoso provést.

### <a name="example-view-validation-report-summary-for-certificate-validation-only-deployment-and-support"></a>Příklad: Zobrazit sestavu ověření souhrnu pro certifikát ověření pouze (nasazení a podporu)

```PowerShell
Start-AzsReadinessChecker -ReportPath Contoso-AzsReadinessReport.json -ReportSections Certificate -Summary
```

V tomto příkladu nasazení a podporu týmu zpráva připravenost od zákazníka Contoso a Zobrazit souhrnný stav provedení ověření certifikátu společnosti Contoso provést pomocí Start AzsReadinessChecker.

## <a name="required-parameters"></a>Požadované parametry

> -RegionName

Určuje název oblasti nasazení Azure stacku.
|  |  |
|----------------------------|--------------|
|Zadejte:                       |Řetězec        |
|Pozice:                   |s názvem         |
|Výchozí hodnota:              |Žádný          |
|Přijměte kanálový vstup:      |False         |
|Přijměte zástupné znaky: |False         |

> – PLNĚ KVALIFIKOVANÝ NÁZEV DOMÉNY

Určuje nasazení Azure stacku externí plně kvalifikovaný název domény, také alias ExternalFQDN a ExternalDomainName.
|  |  |
|----------------------------|--------------|
|Zadejte:                       |Řetězec        |
|Pozice:                   |s názvem         |
|Výchozí hodnota:              |ExternalFQDN ExternalDomainName |
|Přijměte kanálový vstup:      |False         |
|Přijměte zástupné znaky: |False         |

> -IdentitySystem

Určuje nasazení Azure stacku systém identit platné hodnoty, AAD nebo AD FS, Azure Active Directory a Active Directory Federated Services v uvedeném pořadí.
|  |  |
|----------------------------|--------------|
|Zadejte:                       |Řetězec        |
|Pozice:                   |s názvem         |
|Výchozí hodnota:              |Žádný          |
|Platné hodnoty:               |"AAD", "ADFS"  |
|Přijměte kanálový vstup:      |False         |
|Přijměte zástupné znaky: |False         |

> -PfxPassword

Určuje heslo přidružené soubory certifikátů PFX.
|  |  |
|----------------------------|---------|
|Zadejte:                       |SecureString |
|Pozice:                   |s názvem    |
|Výchozí hodnota:              |Žádný     |
|Přijměte kanálový vstup:      |False    |
|Přijměte zástupné znaky: |False    |

> -PaaSCertificates

Určuje zatřiďovací tabulku obsahující cesty a heslo pro certifikáty PaaS.
|  |  |
|----------------------------|---------|
|Zadejte:                       |Zatřiďovací tabulka |
|Pozice:                   |s názvem    |
|Výchozí hodnota:              |Žádný     |
|Přijměte kanálový vstup:      |False    |
|Přijměte zástupné znaky: |False    |

> -DeploymentDataJSONPath

Určuje konfigurační soubor JSON nasazení dat. Azure Stack. Tento soubor je vygenerován pro nasazení.
|  |  |
|----------------------------|---------|
|Zadejte:                       |Řetězec   |
|Pozice:                   |s názvem    |
|Výchozí hodnota:              |Žádný     |
|Přijměte kanálový vstup:      |False    |
|Přijměte zástupné znaky: |False    |

> -PfxPath

Určuje cestu k problematické certifikát, který vyžaduje rutiny importu/exportu chcete vyřešit, jak je uvedeno v ověření certifikátu v tomto nástroji.
|  |  |
|----------------------------|---------|
|Zadejte:                       |Řetězec   |
|Pozice:                   |s názvem    |
|Výchozí hodnota:              |Žádný     |
|Přijměte kanálový vstup:      |False    |
|Přijměte zástupné znaky: |False    |

> -ExportPFXPath  

Určuje cílovou cestu pro výsledný soubor PFX z rutiny importu/exportu.  
|  |  |
|----------------------------|---------|
|Zadejte:                       |Řetězec   |
|Pozice:                   |s názvem    |
|Výchozí hodnota:              |Žádný     |
|Přijměte kanálový vstup:      |False    |
|Přijměte zástupné znaky: |False    |

> – Předmět

Určuje seřazený slovník předmětu pro generování žádosti o certifikát.
|  |  |
|----------------------------|---------|
|Zadejte:                       |Kolekce OrderedDictionary   |
|Pozice:                   |s názvem    |
|Výchozí hodnota:              |Žádný     |
|Přijměte kanálový vstup:      |False    |
|Přijměte zástupné znaky: |False    |

> Typ RequestType-

Určuje typ SAN žádosti o certifikát. Platné hodnoty MultipleCSR, SingleCSR.

- *MultipleCSR* generuje více žádosti o certifikát, jeden pro každou službu.
- *SingleCSR* vygeneruje jedna žádost o certifikát pro všechny služby.

|  |  |
|----------------------------|---------|
|Zadejte:                       |Řetězec   |
|Pozice:                   |s názvem    |
|Výchozí hodnota:              |Žádný     |
|Platné hodnoty:               |"MultipleCSR", "SingleCSR. |
|Přijměte kanálový vstup:      |False    |
|Přijměte zástupné znaky: |False    |

> -OutputRequestPath

Určuje cílovou cestu pro soubory žádosti o certifikát, musí directory již existuje.
|  |  |
|----------------------------|---------|
|Zadejte:                       |Řetězec   |
|Pozice:                   |s názvem    |
|Výchozí hodnota:              |Žádný     |
|Přijměte kanálový vstup:      |False    |
|Přijměte zástupné znaky: |False    |

> -AADServiceAdministrator

Určuje správce služby Azure Active Directory se použije pro nasazení Azure stacku.
|  |  |
|----------------------------|---------|
|Zadejte:                       |PSCredential   |
|Pozice:                   |s názvem    |
|Výchozí hodnota:              |Žádný     |
|Přijměte kanálový vstup:      |False    |
|Přijměte zástupné znaky: |False    |

> -AADDirectoryTenantName

Určuje název služby Azure Active Directory se použije pro nasazení Azure stacku.
|  |  |
|----------------------------|---------|
|Zadejte:                       |Řetězec   |
|Pozice:                   |s názvem    |
|Výchozí hodnota:              |Žádný     |
|Přijměte kanálový vstup:      |False    |
|Přijměte zástupné znaky: |False    |

> -AzureEnvironment

Určuje instanci služby Azure obsahující účtů, adresářů a předplatných se použije pro nasazení Azure stacku a registraci.
|  |  |
|----------------------------|---------|
|Zadejte:                       |Řetězec   |
|Pozice:                   |s názvem    |
|Výchozí hodnota:              |Žádný     |
|Platné hodnoty:               |"AzureCloud", "AzureChinaCloud", "AzureUSGovernment. |
|Přijměte kanálový vstup:      |False    |
|Přijměte zástupné znaky: |False    |

> -RegistrationAccount

Určuje účet registrace pro registrace Azure Stack.
|  |  |
|----------------------------|---------|
|Zadejte:                       |Řetězec   |
|Pozice:                   |s názvem    |
|Výchozí hodnota:              |Žádný     |
|Přijměte kanálový vstup:      |False    |
|Přijměte zástupné znaky: |False    |

> -RegistrationSubscriptionID

Určuje ID předplatného registrace pro registrace Azure Stack.
|  |  |
|----------------------------|---------|
|Zadejte:                       |Guid     |
|Pozice:                   |s názvem    |
|Výchozí hodnota:              |Žádný     |
|Přijměte kanálový vstup:      |False    |
|Přijměte zástupné znaky: |False    |

> Možnost ReportPath-

Určuje cestu pro sestavu připravenosti, výchozí hodnota je aktuální adresář a výchozí název sestavy.
|  |  |
|----------------------------|---------|
|Zadejte:                       |Řetězec   |
|Pozice:                   |s názvem    |
|Výchozí hodnota:              |Vše      |
|Přijměte kanálový vstup:      |False    |
|Přijměte zástupné znaky: |False    |

## <a name="optional-parameters"></a>Volitelné parametry

> -CertificatePath

Určuje cestu, pod kterým jenom ten certifikát požadovaný certifikát složky jsou k dispozici.

Požadované složky pro nasazení Azure Stack pomocí systému identit Azure Active Directory jsou:

Public, trezor klíčů, KeyVaultInternal, veřejný portál ARM ACSBlob ACSQueue, ACSTable, portál pro správu, ARM správce

Požadované složky pro nasazení do systému identit Active Directory Federation Services jsou služby Azure Stack:

ACSBlob, ACSQueue, ACSTable, služby AD FS, portál pro správu, ARM správce, veřejné ARM, graf, trezor klíčů, KeyVaultInternal, veřejný portál

|  |  |
|----------------------------|---------|
|Zadejte:                       |Řetězec   |
|Pozice:                   |s názvem    |
|Výchozí hodnota:              |. \Certificates |
|Přijměte kanálový vstup:      |False    |
|Přijměte zástupné znaky: |False    |

> -IncludePaaS  

Určuje, pokud služby PaaS nebo názvy hostitelů měla být přidána do žádosti o certifikát.

|  |  |
|----------------------------|------------------|
|Zadejte:                       |SwitchParameter   |
|Pozice:                   |s názvem             |
|Výchozí hodnota:              |False             |
|Přijměte kanálový vstup:      |False             |
|Přijměte zástupné znaky: |False             |

> -ReportSections

Určuje, zda jenom zobrazit souhrn, sestavy vynechá podrobností.
|  |  |
|----------------------------|---------|
|Zadejte:                       |Řetězec   |
|Pozice:                   |s názvem    |
|Výchozí hodnota:              |Vše      |
|Platné hodnoty:               |"Certifikátu", "AzureRegistration", "AzureIdentity", "Úloh", "všechny. |
|Přijměte kanálový vstup:      |False    |
|Přijměte zástupné znaky: |False    |

> -Summary

Určuje, zda jenom zobrazit souhrn, sestavy vynechá podrobností.
|  |  |
|----------------------------|------------------|
|Zadejte:                       |SwitchParameter   |
|Pozice:                   |s názvem             |
|Výchozí hodnota:              |False             |
|Přijměte kanálový vstup:      |False             |
|Přijměte zástupné znaky: |False             |

> -CleanReport

Odebere předchozí historie spuštění a ověřování a zapíše ověření do nové sestavy.
|  |  |
|----------------------------|------------------|
|Zadejte:                       |SwitchParameter   |
|Aliasy:                    |CF                |
|Pozice:                   |s názvem             |
|Výchozí hodnota:              |False             |
|Přijměte kanálový vstup:      |False             |
|Přijměte zástupné znaky: |False             |

> -OutputPath

Určuje vlastní cesta pro uložení sestavy připravenost JSON a podrobný soubor protokolu.  Pokud cesta neexistuje, nástroj se pokusí vytvořit adresář.

|  |  |
|----------------------------|------------------|
|Zadejte:                       |Řetězec            |
|Pozice:                   |s názvem             |
|Výchozí hodnota:              |$ENV: TEMP\AzsReadinessChecker  |
|Přijměte kanálový vstup:      |False             |
|Přijměte zástupné znaky: |False             |

> – Potvrzení

Výzvy k potvrzení před spuštěním rutiny.
|  |  |
|----------------------------|------------------|
|Zadejte:                       |SwitchParameter   |
|Aliasy:                    |CF                |
|Pozice:                   |s názvem             |
|Výchozí hodnota:              |False             |
|Přijměte kanálový vstup:      |False             |
|Přijměte zástupné znaky: |False             |

> -WhatIf

Ukazuje, co by se stalo při spuštění rutiny. Rutina není spuštěna.
|  |  |
|----------------------------|------------------|
|Zadejte:                       |SwitchParameter   |
|Aliasy:                    |Wi                |
|Pozice:                   |s názvem             |
|Výchozí hodnota:              |False             |
|Přijměte kanálový vstup:      |False             |
|Přijměte zástupné znaky: |False             |
