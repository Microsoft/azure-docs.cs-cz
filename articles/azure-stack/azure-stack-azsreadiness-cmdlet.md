---
title: Reference k rutině Start-AzsReadinessChecker | Microsoft Docs
description: Nápovědu rutiny prostředí PowerShell pro modul kontrolu připravenosti zásobník Azure.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/08/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 8481fbd6c7cb82b34070f9bc8cc6d7f3f4b2518c
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="start-azsreadinesschecker-cmdlet-reference"></a>Rutiny Start-AzsReadinessChecker – reference

Modul: Microsoft.AzureStack.ReadinessChecker

Tento modul obsahuje jenom jedné rutiny.  Tato rutina provede jednu nebo více funkcí před nasazením nebo předem údržby pro Azure zásobníku.

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
**Start-AzsReadinessChecker** rutiny ověří předplatná Azure, certifikátů, účtů Azure a Azure Active Directory. Spusťte ověření před nasazením Azure zásobníku nebo před zásobník Azure obsluhy akcí, například otočení tajný klíč. Rutina také slouží ke generování žádosti o podpisový certifikát pro infrastrukturu a volitelně PaaS certifikátů.  Nakonec rutinu můžete znovu zabalte certifikáty PFX řešení běžných problémů s balení.

## <a name="examples"></a>Příklady
**Příklad: Generování žádosti o podepsání certifikátu**

```PowerShell
$regionName = 'east'
$externalFQDN = 'azurestack.contoso.com'
$subjectHash = [ordered]@{"OU"="AzureStack";"O"="Microsoft";"L"="Redmond";"ST"="Washington";"C"="US"}
Start-AzsReadinessChecker -regionName $regionName -externalFQDN $externalFQDN -subjectName $subjectHash -IdentitySystem ADFS -requestType MultipleCSR
```

V tomto příkladu generuje počáteční AzsReadinessChecker více požadavků (Podepsání certifikátu) pro certifikáty, které jsou vhodné pro nasazení služby AD FS zásobník Azure s názvem oblast "východ" a externí název FQDN "azurestack.contoso.com"

**Příklad: Ověření certifikátů**
```PowerShell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -CertificatePath .\Certificates\ -PfxPassword $password -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD
```

V tomto příkladu heslo PFX je výzva k zadání bezpečně a spuštění AzsReadinessChecker kontroluje relativní složce "Certifikáty", který se pro certifikáty platné pro nasazení služby AAD s názvem oblast "východ" a externí název FQDN "azurestack.contoso.com" 

**Příklad: Ověření certifikátů s daty nasazení (nasazení a podporu)**
```PowerShell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -CertificatePath .\Certificates\ -PfxPassword $password -DeploymentDataJSONPath .\deploymentdata.json
```
V tomto příkladu nasazení a podporu heslo PFX je výzva k zadání bezpečně a spuštění AzsReadinessChecker kontroluje relativní složce "Certifikáty", který se pro certifikáty platné pro nasazení, kde identity, oblast a externí plně kvalifikovaný název domény se načítají z nasazení dat JSON soubor byl vytvořen pro nasazení. 

**Příklad: Ověření PaaS certifikátů**
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

V tomto příkladu je zatřiďovací tabulku vytvořený pomocí cesty a hesla, aby každý certifikát PaaS. Certifikáty můžete vynechat. Spuštění AzsReadinessChecker kontroluje každý PFX cesta existuje a ověří je pomocí oblasti Východ' a 'azurestack.contoso.com' externí plně kvalifikovaný název domény.

**Příklad: Ověření certifikátů PaaS daty nasazení**
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

V tomto příkladu je zatřiďovací tabulku vytvořený pomocí cesty a hesla, aby každý certifikát PaaS. Certifikáty můžete vynechat. Spuštění AzsReadinessChecker kontroluje každý PFX cesta existuje a je pomocí oblast ověří a externí plně kvalifikovaný název domény čtení ze souboru JSON data nasazení vygenerovat pro nasazení. 

**Příklad: Ověření identit Azure**
```PowerShell
$serviceAdminCredential = Get-Credential -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant e.g. serviceadmin@contoso.onmicrosoft.com"
Start-AzsReadinessChecker -AADServiceAdministrator $serviceAdminCredential -AzureEnvironment AzureCloud -AzureDirectoryTenantName azurestack.contoso.com
```

V tomto příkladu se přihlašovací údaje účtu správce služby se zobrazí výzva k bezpečně a AzsReadinessChecker spuštění ověří, že účet Azure a Azure Active Directory jsou platné pro nasazení služby AAD s názvem directory klienta "azurestack.contoso.com"


**Příklad: Ověření identit Azure s daty nasazení (podpora nasazení)**
```PowerSHell
$serviceAdminCredential = Get-Credential -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant e.g. serviceadmin@contoso.onmicrosoft.com"
Start-AzsReadinessChecker -AADServiceAdministrator $serviceAdminCredential -DeploymentDataJSONPath .\contoso-depploymentdata.json
```

V tomto příkladu přihlašovací údaje účtu správce služby se zobrazí výzva k bezpečně a spuštění AzsReadinessChecker zkontroluje, že účet Azure a Azure Active Directory jsou platné pro nasazení služby AAD kde AzureCloud a TenantName se načítají z data nasazení JSON soubor byl vytvořen pro nasazení.


**Příklad: Ověření registrace Azure**
```PowerShell
$registrationCredential = Get-Credential -Message "Enter Credentials for Subscription Owner"e.g. subscriptionowner@contoso.onmicrosoft.com"
$subscriptionID = "f7c26209-cd2d-4625-86ba-724ebeece794"
Start-AzsReadinessChecker -RegistrationAccount $registrationCredential -RegistrationSubscriptionID $subscriptionID -AzureEnvironment AzureCloud
```

V tomto příkladu pověření vlastníka předplatného se zobrazí výzva k bezpečně a spuštění AzsReadinessChecker potom provede ověřování pro daný účet a předplatné tak, aby byl lze použít pro registraci Azure zásobníku. 


**Příklad: Ověření registrace Azure s daty nasazení (tým pro nasazení)**
```PowerShell
$registrationCredential = Get-Credential -Message "Enter Credentials for Subscription Owner"e.g. subscriptionowner@contoso.onmicrosoft.com"
$subscriptionID = "f7c26209-cd2d-4625-86ba-724ebeece794"
Start-AzsReadinessChecker -RegistrationAccount $registrationCredential -RegistrationSubscriptionID $subscriptionID -DeploymentDataJSONPath .\contoso-deploymentdata.json
```

V tomto příkladu pověření vlastníka předplatného se zobrazí výzva k bezpečně a spuštění AzsReadinessChecker potom provede ověřování pro daný účet a předplatné tak, aby byl lze použít pro registraci Azure zásobníku kde jsou další podrobnosti čtení ze souboru JSON data nasazení vygenerovat pro nasazení.

**Příklad: PFX importu a exportu balíčku**
```PowerShell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -PfxPassword $password -PfxPath .\certificates\ssl.pfx -ExportPFXPath .\certificates\ssl_new.pfx
```

V tomto příkladu heslo PFX zobrazí výzva k bezpečně. Soubor ssl.pfx budou naimportovány do úložiště certifikátů místního počítače a znovu exportovat pomocí stejného hesla a uložit jako ssl_new.pfx.  Tato procedura je pro použití při ověřování certifikátu označené, že privátní klíč nemá nastaven atribut místního počítače, řetěz certifikátů je poškozená, důležité certifikáty se nacházejí v soubor PFX nebo řetěz certifikátů je v nesprávném pořadí.


**Příklad: Zobrazení sestavy ověření (podpora nasazení)**
```PowerShell
Start-AzsReadinessChecker -ReportPath Contoso-AzsReadinessReport.json
```

V tomto příkladu týmu nasazení nebo podporu obdrží sestava připravenosti od zákazníků (Contoso) a použít počáteční AzsReadinessChecker zobrazíte stav ověření spuštěních, provést Contoso.

**Příklad: Zobrazení sestavy ověření shrnutí certifikát ověření pouze (nasazení a podporu)**
```PowerShell
Start-AzsReadinessChecker -ReportPath Contoso-AzsReadinessReport.json -ReportSections Certificate -Summary
```

V tomto příkladu týmu nasazení nebo podporu obdrží sestava připravenosti od zákazníků Contoso a pomocí spuštění AzsReadinessChecker zobrazíte souhrnnou stav spuštěních ověření certifikátu, provést Contoso.



## <a name="required-parameters"></a>Požadované parametry
> -RegionName

Určuje název oblasti Azure zásobníku nasazení.
|  |  |
|----------------------------|--------------|
|Zadejte:                       |Řetězec        |
|Pozice:                   |S názvem         |
|Výchozí hodnota:              |Žádný          |
|Přijměte vstupní kanál:      |False         |
|Přijměte zástupné znaky: |False         |

> -PLNĚ KVALIFIKOVANÝ NÁZEV DOMÉNY    

Určuje nasazení Azure zásobníku externí plně kvalifikovaný název domény, také alias jako ExternalFQDN a ExternalDomainName.
|  |  |
|----------------------------|--------------|
|Zadejte:                       |Řetězec        |
|Pozice:                   |S názvem         |
|Výchozí hodnota:              |ExternalFQDN ExternalDomainName |
|Přijměte vstupní kanál:      |False         |
|Přijměte zástupné znaky: |False         |

 

> -IdentitySystem    

Určuje nasazení Azure zásobníku systém identit platné hodnoty, AAD nebo AD FS, Azure Active Directory a Active Directory Federated Services v uvedeném pořadí.
|  |  |
|----------------------------|--------------|
|Zadejte:                       |Řetězec        |
|Pozice:                   |S názvem         |
|Výchozí hodnota:              |Žádný          |
|Platné hodnoty:               |AAD, 'SLUŽBY AD FS.  |
|Přijměte vstupní kanál:      |False         |
|Přijměte zástupné znaky: |False         |

> -PfxPassword    

Určuje heslo přidružené soubory certifikátů PFX.
|  |  |
|----------------------------|---------|
|Zadejte:                       |SecureString |
|Pozice:                   |S názvem    |
|Výchozí hodnota:              |Žádný     |
|Přijměte vstupní kanál:      |False    |
|Přijměte zástupné znaky: |False    |

> -PaaSCertificates

Určuje zatřiďovací tabulky obsahující cesty a hesla, aby PaaS certifikáty.
|  |  |
|----------------------------|---------|
|Zadejte:                       |Zatřiďovací tabulky |
|Pozice:                   |S názvem    |
|Výchozí hodnota:              |Žádný     |
|Přijměte vstupní kanál:      |False    |
|Přijměte zástupné znaky: |False    |

> -DeploymentDataJSONPath

Určuje konfigurační soubor zásobník Azure nasazení data JSON. Tento soubor se generuje pro nasazení.
|  |  |
|----------------------------|---------|
|Zadejte:                       |Řetězec   |
|Pozice:                   |S názvem    |
|Výchozí hodnota:              |Žádný     |
|Přijměte vstupní kanál:      |False    |
|Přijměte zástupné znaky: |False    |

> -PfxPath

Určuje cestu k problematické certifikát, který vyžaduje rutiny import a export chcete opravit, jak je indikován ověření certifikátu v tomto nástroji.
|  |  |
|----------------------------|---------|
|Zadejte:                       |Řetězec   |
|Pozice:                   |S názvem    |
|Výchozí hodnota:              |Žádný     |
|Přijměte vstupní kanál:      |False    |
|Přijměte zástupné znaky: |False    |

> -ExportPFXPath  

Určuje cílovou cestu pro výsledný soubor PFX z rutiny importu a exportu.  
|  |  |
|----------------------------|---------|
|Zadejte:                       |Řetězec   |
|Pozice:                   |S názvem    |
|Výchozí hodnota:              |Žádný     |
|Přijměte vstupní kanál:      |False    |
|Přijměte zástupné znaky: |False    |

> -Subjektu

Určuje slovníku seřazené subjektu pro generování žádost o certifikát.
|  |  |
|----------------------------|---------|
|Zadejte:                       |Kolekce OrderedDictionary   |
|Pozice:                   |S názvem    |
|Výchozí hodnota:              |Žádný     |
|Přijměte vstupní kanál:      |False    |
|Přijměte zástupné znaky: |False    |

> -RequestType

Určuje typ sítě SAN žádosti o certifikát. Platné hodnoty MultipleCSR, SingleCSR.
- *MultipleCSR* generuje několik žádostí o certifikát, jeden pro každou službu.
- *SingleCSR* vygeneruje jednu žádost o certifikát pro všechny služby.   

|  |  |
|----------------------------|---------|
|Zadejte:                       |Řetězec   |
|Pozice:                   |S názvem    |
|Výchozí hodnota:              |Žádný     |
|Platné hodnoty:               |'MultipleCSR', 'SingleCSR. |
|Přijměte vstupní kanál:      |False    |
|Přijměte zástupné znaky: |False    |

> -OutputRequestPath

Určuje cílovou cestu pro soubory žádost o certifikát, musí adresář již existuje.
|  |  |
|----------------------------|---------|
|Zadejte:                       |Řetězec   |
|Pozice:                   |S názvem    |
|Výchozí hodnota:              |Žádný     |
|Přijměte vstupní kanál:      |False    |
|Přijměte zástupné znaky: |False    |

> -AADServiceAdministrator

Určuje Azure Active Directory Správce služby pro nasazení Azure zásobníku.
|  |  |
|----------------------------|---------|
|Zadejte:                       |Přihlašovací údaje   |
|Pozice:                   |S názvem    |
|Výchozí hodnota:              |Žádný     |
|Přijměte vstupní kanál:      |False    |
|Přijměte zástupné znaky: |False    |

> -AADDirectoryTenantName

Určuje název služby Azure Active Directory má být použit pro nasazení Azure zásobníku.
|  |  |
|----------------------------|---------|
|Zadejte:                       |Řetězec   |
|Pozice:                   |S názvem    |
|Výchozí hodnota:              |Žádný     |
|Přijměte vstupní kanál:      |False    |
|Přijměte zástupné znaky: |False    |

> -AzureEnvironment

Určuje instanci Azure Services obsahující účty, adresářů a odběry, který se má použít pro nasazení Azure zásobníku a registraci.
|  |  |
|----------------------------|---------|
|Zadejte:                       |Řetězec   |
|Pozice:                   |S názvem    |
|Výchozí hodnota:              |Žádný     |
|Platné hodnoty:               |'AzureCloud', 'AzureChinaCloud', 'AzureGermanCloud. |
|Přijměte vstupní kanál:      |False    |
|Přijměte zástupné znaky: |False    |

> -RegistrationAccount

Určuje účet registrace, který se má použít pro registraci zásobník Azure.
|  |  |
|----------------------------|---------|
|Zadejte:                       |Řetězec   |
|Pozice:                   |S názvem    |
|Výchozí hodnota:              |Žádný     |
|Přijměte vstupní kanál:      |False    |
|Přijměte zástupné znaky: |False    |

> -RegistrationSubscriptionID

Určuje ID registrace předplatného má být použit pro registraci Azure zásobníku.
|  |  |
|----------------------------|---------|
|Zadejte:                       |Guid     |
|Pozice:                   |S názvem    |
|Výchozí hodnota:              |Žádný     |
|Přijměte vstupní kanál:      |False    |
|Přijměte zástupné znaky: |False    |

> -Možnost ReportPath

Určuje cestu pro sestavu připravenosti, použije se výchozí hodnota aktuální adresář a výchozí název sestavy.
|  |  |
|----------------------------|---------|
|Zadejte:                       |Řetězec   |
|Pozice:                   |S názvem    |
|Výchozí hodnota:              |Vše      |
|Přijměte vstupní kanál:      |False    |
|Přijměte zástupné znaky: |False    |



## <a name="optional-parameters"></a>Volitelné parametry
> -CertificatePath     

Určuje cestu, pod kterým jenom certifikát vyžaduje, zda jsou k dispozici certifikát složek.

Požadované složky pro nasazení zásobník Azure s Azure Active Directory systém identit jsou:

ACSBlob, ACSQueue, ACSTable, portál pro správu, správce ARM ARM Public, KeyVault, KeyVaultInternal, veřejné portálu

Požadované složky pro nasazení s systém identit Active Directory Federation Services jsou zásobník Azure:

ACSBlob, ACSQueue, ACSTable, služby AD FS, portál pro správu, správce ARM, veřejné ARM, grafu, KeyVault, KeyVaultInternal, veřejné portálu


|  |  |
|----------------------------|---------|
|Zadejte:                       |Řetězec   |
|Pozice:                   |S názvem    |
|Výchozí hodnota:              |. \Certificates |
|Přijměte vstupní kanál:      |False    |
|Přijměte zástupné znaky: |False    |


> -IncludePaaS  

Určuje, pokud PaaS služby nebo názvy hostitelů musí být přidaní do žádosti certifikátu.


|  |  |
|----------------------------|------------------|
|Zadejte:                       |Přepínací parametr   |
|Pozice:                   |S názvem             |
|Výchozí hodnota:              |False             |
|Přijměte vstupní kanál:      |False             |
|Přijměte zástupné znaky: |False             |


> -ReportSections        

Určuje, zda jenom zobrazit souhrn, sestava vynechá podrobností.
|  |  |
|----------------------------|---------|
|Zadejte:                       |Řetězec   |
|Pozice:                   |S názvem    |
|Výchozí hodnota:              |Vše      |
|Platné hodnoty:               |'Certifikátu', 'AzureRegistration', 'AzureIdentity', "Úlohy", "Vše" |
|Přijměte vstupní kanál:      |False    |
|Přijměte zástupné znaky: |False    |


> – Souhrn 

Určuje, zda jenom zobrazit souhrn, sestava vynechá podrobností.
|  |  |
|----------------------------|------------------|
|Zadejte:                       |Přepínací parametr   |
|Pozice:                   |S názvem             |
|Výchozí hodnota:              |False             |
|Přijměte vstupní kanál:      |False             |
|Přijměte zástupné znaky: |False             |


> -CleanReport  

Odebere předchozí historie provádění a ověřování a zapíše ověření do nové sestavy.
|  |  |
|----------------------------|------------------|
|Zadejte:                       |Přepínací parametr   |
|Aliasy:                    |CR                |
|Pozice:                   |S názvem             |
|Výchozí hodnota:              |False             |
|Přijměte vstupní kanál:      |False             |
|Přijměte zástupné znaky: |False             |


> -OutputPath    

Určuje vlastní cesta pro uložení sestavy připravenosti JSON a podrobný soubor protokolu.  Pokud cesta již neexistuje, tento nástroj se pokusí vytvořit adresář.
|  |  |
|----------------------------|------------------|
|Zadejte:                       |Řetězec            |
|Pozice:                   |S názvem             |
|Výchozí hodnota:              |$ENV: TEMP\AzsReadinessChecker  |
|Přijměte vstupní kanál:      |False             |
|Přijměte zástupné znaky: |False             |


> – Potvrzení  

Vyzve k potvrzení před spuštěním rutiny.
|  |  |
|----------------------------|------------------|
|Zadejte:                       |Přepínací parametr   |
|Aliasy:                    |CR                |
|Pozice:                   |S názvem             |
|Výchozí hodnota:              |False             |
|Přijměte vstupní kanál:      |False             |
|Přijměte zástupné znaky: |False             |


> -WhatIf  

Zobrazuje, co by se stalo při spuštění rutiny. Není-li spustit rutinu.
|  |  |
|----------------------------|------------------|
|Zadejte:                       |Přepínací parametr   |
|Aliasy:                    |Wi                |
|Pozice:                   |S názvem             |
|Výchozí hodnota:              |False             |
|Přijměte vstupní kanál:      |False             |
|Přijměte zástupné znaky: |False             |

 
