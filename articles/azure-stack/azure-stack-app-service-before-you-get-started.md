---
title: Před nasazením služby App Service ve službě Azure Stack | Dokumentace Microsoftu
description: Kroky k dokončení před nasazením služby App Service ve službě Azure Stack
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/11/2018
ms.author: anwestg
ms.openlocfilehash: ec34b99f48fbe7b634d7d143e8e108e8f2868f67
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55182723"
---
# <a name="before-you-get-started-with-app-service-on-azure-stack"></a>Před zahájením práce s App Service ve službě Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Před nasazením služby Azure App Service ve službě Azure Stack, musíte dokončit požadované kroky uvedené v tomto článku.

> [!IMPORTANT]
> Aktualizace 1809 do služby Azure Stack integrované systému nebo nasadit nejnovější Azure Stack Development Kit (ASDK) před nasazením Azure App Service 1.4.

## <a name="download-the-installer-and-helper-scripts"></a>Stáhněte si instalační program a pomocné skripty

1. Stáhněte si [služby App Service v Azure stacku nasazení pomocné skripty](https://aka.ms/appsvconmashelpers).
2. Stáhněte si [služby App Service na Instalační službě Azure Stack](https://aka.ms/appsvconmasinstaller).
3. Extrahujte soubory ze souboru ZIP pomocné skripty. Extrahují se následující soubory a složky:

   - Common.ps1
   - Create-AADIdentityApp.ps1
   - Create-ADFSIdentityApp.ps1
   - Create-AppServiceCerts.ps1
   - Get-AzureStackRootCert.ps1
   - Remove-AppService.ps1
   - Složky modulů
     - GraphAPI.psm1

## <a name="syndicate-the-custom-script-extension-from-the-marketplace"></a>Publikování rozšíření vlastních skriptů z Marketplace

Azure App Service ve službě Azure Stack vyžaduje v1.9.0 rozšíření vlastních skriptů.  Přípona musí být [syndikovat markeplace](https://docs.microsoft.com/azure/azure-stack/azure-stack-download-azure-marketplace-item) před zahájením nasazení nebo upgradu služby Azure App Service ve službě Azure Stack

## <a name="high-availability"></a>Vysoká dostupnost

Aktualizace Azure Stack 1802 přidali podporu domén selhání. Nová nasazení služby Azure App Service ve službě Azure Stack se bude distribuovat napříč doménami selhání a zajistit odolnost proti chybám.

Existující nasazení služby Azure App Service ve službě Azure Stack, které se nasadily před aktualizace 1802, najdete v článku [vyrovnat poskytovatele prostředků App Service napříč doménami selhání](azure-stack-app-service-fault-domain-update.md) článku.

Kromě toho nasaďte požadované souborového serveru a instance systému SQL Server v konfiguraci s vysokou dostupností.

## <a name="get-certificates"></a>Získání certifikátů

### <a name="azure-resource-manager-root-certificate-for-azure-stack"></a>Azure Resource Manageru kořenový certifikát pro službu Azure Stack

Otevřete relaci Powershellu se zvýšenými oprávněními na počítači, se kterým dosáhnete privileged koncový bod na integrovaný systém Azure Stack nebo Azure Stack Development Kit hostitele.

Spustit *Get-AzureStackRootCert.ps1* skriptu ze složky, které jste extrahovali pomocné skripty. Skript vytvoří ve stejné složce jako skript, který potřebuje služby App Service pro vytváření certifikátů kořenový certifikát.

Při spuštění následujícího příkazu Powershellu, budete mít k poskytnutí privileged koncového bodu a přihlašovací údaje pro AzureStack\CloudAdmin.

```PowerShell
    Get-AzureStackRootCert.ps1
```

#### <a name="get-azurestackrootcertps1-script-parameters"></a>Get-AzureStackRootCert.ps1 parametry skriptu

| Parametr | Požadované nebo volitelné | Výchozí hodnota | Popis |
| --- | --- | --- | --- |
| PrivilegedEndpoint | Požaduje se | AzS-ERCS01 | Privilegované koncového bodu |
| CloudAdminCredential | Požaduje se | AzureStack\CloudAdmin | Přihlašovací údaje účtu domény pro správce cloudu Azure Stack |

### <a name="certificates-required-for-asdk-deployment-of-azure-app-service"></a>Certifikáty pro ASDK nasazení služby Azure App Service

*Vytvořit AppServiceCerts.ps1* skript funguje u certifikační autority Azure stacku vytvořit čtyři certifikáty, které vyžaduje služba App Service.

| Název souboru | Použití |
| --- | --- |
| _.appservice.local.azurestack.external.pfx | Výchozí certifikát SSL služby App Service |
| api.appservice.local.azurestack.external.pfx | Certifikát SSL rozhraní API služby aplikace |
| ftp.appservice.local.azurestack.external.pfx | Certifikát SSL vydavatele služby App Service |
| sso.appservice.local.azurestack.external.pfx | Certifikát aplikace identity služby App Service |

K vytvoření certifikátů, postupujte podle těchto kroků:

1. Přihlaste se pomocí účtu AzureStack\AzureStackAdmin hostiteli Azure Stack Development Kit.
2. Otevřete relaci Powershellu se zvýšenými oprávněními.
3. Spustit *vytvořit AppServiceCerts.ps1* skriptu ze složky, které jste extrahovali pomocné skripty. Tento skript vytvoří čtyři certifikáty ve stejné složce jako skript, který požadavkům vytváření certifikátů služby App Service.
4. Zadejte heslo k zabezpečení soubory PFX a poznamenejte si ho. Budete muset zadat ve službě App Service na Instalační službě Azure Stack.

#### <a name="create-appservicecertsps1-script-parameters"></a>Create-AppServiceCerts.ps1 script parameters

| Parametr | Požadované nebo volitelné | Výchozí hodnota | Popis |
| --- | --- | --- | --- |
| pfxPassword | Požaduje se | Null | Heslo, který pomáhá chránit soukromý klíč certifikátu |
| DomainName | Požaduje se | local.azurestack.external | Přípona oblasti a domény Azure Stack |

### <a name="certificates-required-for-azure-stack-production-deployment-of-azure-app-service"></a>Certifikáty pro produkční nasazení Azure Stack služby Azure App Service

Ke spuštění poskytovatele prostředků v produkčním prostředí, je nutné zadat tyto certifikáty:

- Výchozí certifikát domény
- Certifikát rozhraní API
- Certifikát pro publikování
- Certifikát identity

#### <a name="default-domain-certificate"></a>Výchozí certifikát domény

Výchozí certifikát domény je umístěn na role front-endu. Uživatelské aplikace pro zástupný znak nebo výchozí domény žádost do služby Azure App Service použít tento certifikát. Certifikát se také používá pro operací správy zdrojů (Kudu).

Certifikát musí být ve formátu .pfx a musí být certifikát se zástupným znakem tři předmět. Tento požadavek umožňuje jeden certifikát pro výchozí doménu a koncový bod správce řízení služeb pro operací správy zdrojů.

| Formát | Příklad: |
| --- | --- |
| \*.appservice.\<region\>.\<DomainName\>.\<extension\> | \*.appservice.redmond.azurestack.external |
| \*.scm.appservice.<region>.<DomainName>.<extension> | \*.scm.appservice.redmond.azurestack.external |
| \*.sso.appservice.<region>.<DomainName>.<extension> | \*.sso.appservice.redmond.azurestack.external |

#### <a name="api-certificate"></a>Certifikát rozhraní API

Certifikát rozhraní API je umístěn na roli správy. Poskytovatel prostředků se použije k tomu zabezpečené volání rozhraní API. Certifikát pro publikování musí obsahovat subjektem, který odpovídá položce DNS rozhraní API.

| Formát | Příklad: |
| --- | --- |
| api.appservice.\<region\>.\<DomainName\>.\<extension\> | api.appservice.redmond.azurestack.external |

#### <a name="publishing-certificate"></a>Certifikát pro publikování

Certifikát pro role vydavatele zabezpečuje FTPS provoz pro vlastníky aplikace. při jejich nahrávání obsahu. Certifikát pro publikování musí obsahovat subjektem, který odpovídá položce FTPS DNS.

| Formát | Příklad: |
| --- | --- |
| ftp.appservice.\<region\>.\<DomainName\>.\<extension\> | ftp.appservice.redmond.azurestack.external |

#### <a name="identity-certificate"></a>Certifikát identity

Certifikát aplikace identity umožňuje:

- Integrace mezi Azure Active Directory (Azure AD) nebo Active Directory Federation Services (AD FS) directory, Azure Stack a App Service pro integraci s poskytovateli prostředků compute.
- Jednotné přihlašování scénáře pro pokročilé vývojářské nástroje v rámci Azure App Service ve službě Azure Stack.

Certifikát identity musí obsahovat subjektem, který odpovídá formátu.

| Formát | Příklad: |
| --- | --- |
| sso.appservice.\<region\>.\<DomainName\>.\<extension\> | sso.appservice.redmond.azurestack.external |


### <a name="validate-certificates"></a>Ověřování certifikátů
Před nasazením poskytovatele prostředků app service, měli byste [ověřit certifikáty, které se použije](azure-stack-validate-pki-certs.md#perform-platform-as-a-service-certificate-validation) s použitím nástroje prerequisite Checker připravenosti Azure Stack k dispozici [Galerie prostředí PowerShell](https://aka.ms/AzsReadinessChecker). Nástroj Azure Stack připravenosti kontrola ověří, že generované certifikáty PKI jsou vhodné pro nasazení služby app. 

Jako osvědčený postup, při práci s některým z nezbytné [certifikáty Azure Stack PKI](azure-stack-pki-certs.md), měli byste naplánovat ponechat dostatek času k testování a v případě potřeby znovu vystavit certifikáty. 

## <a name="virtual-network"></a>Virtuální síť

> [!NOTE]
> Před vytvořením vlastní virtuální sítě je volitelné, protože Azure App Service ve službě Azure Stack můžete vytvořit požadované virtuální sítě, ale pak budou muset komunikovat s SQL a souborový Server prostřednictvím veřejné IP adresy.

Azure App Service ve službě Azure Stack umožňuje nasazení poskytovatele prostředků do existující virtuální síť nebo vám umožní vytvářet virtuální sítě jako součást svého nasazení. Použití existující virtuální síť umožňuje využívání interní IP adresy pro připojení k souborový server a SQL server vyžaduje ve službě Azure App Service ve službě Azure Stack. Virtuální síť musí mít nakonfigurovanou následující rozsah adres a podsítí před instalací služby Azure App Service ve službě Azure Stack:

Virtual Network - /16

Podsítě

- ControllersSubnet /24
- ManagementServersSubnet /24
- FrontEndsSubnet /24
- PublishersSubnet /24
- WorkersSubnet /21

## <a name="prepare-the-file-server"></a>Příprava souborového serveru

Azure App Service vyžaduje použití souborového serveru. Pro nasazení v produkčním prostředí musí být souborový server nakonfigurovaný jako vysoce dostupné a umožňuje zpracovávat chyby.

### <a name="quickstart-template-for-file-server-for-deployments-of-azure-app-service-on-asdk"></a>Šablony rychlý start pro souborový Server pro nasazení služby Azure App Service na ASDK.

Pouze nasazení Azure Stack Development Kit, můžete použít [příklad šablony nasazení Azure Resource Manageru](https://aka.ms/appsvconmasdkfstemplate) pro nasazení nakonfigurované jedním uzlem souborového serveru. Jeden souborový server se bude v pracovní skupině.

### <a name="quickstart-template-for-highly-available-file-server-and-sql-server"></a>Šablony rychlý start pro vysoce dostupný souborový Server a SQL Server

A [šablonu pro rychlý start referenční architektury](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/appservice-fileserver-sqlserver-ha) je nyní k dispozici, který bude nasazení souborového serveru, systému SQL Server, podporu služby Active Directory infrastruktury ve virtuální síti nakonfigurovat tak, aby podporují nasazení s vysokou dostupností Azure App Service v Azure stacku.  

### <a name="steps-to-deploy-a-custom-file-server"></a>Postup nasazení souborových serverů se vlastní

>[!IMPORTANT]
> Pokud budete chtít nasadit službu App Service v existující virtuální síť, souborový Server musí být nasazené do samostatné podsítě ze služby App Service.

#### <a name="provision-groups-and-accounts-in-active-directory"></a>Zřízení skupin a účtů ve službě Active Directory

1. Vytvořte následující skupiny globálního zabezpečení služby Active Directory:

   - FileShareOwners
   - FileShareUsers

2. Vytvořte následující účty služby Active Directory jako účty služeb:

   - FileShareOwner
   - FileShareUser

   Osvědčeným postupem je uživatelů pro tyto účty (a pro všechny webové role) zabezpečení by měl být jedinečný a mít silné uživatelská jména a hesla. Nastavení hesla pomocí následujících podmínek:

   - Povolit **platnost hesla nikdy nevyprší**.
   - Povolit **uživatel nemůže změnit heslo**.
   - Zakázat **musí uživatel změnit heslo při příštím přihlášení**.

3. Přidejte účty do členství ve skupinách následujícím způsobem:

   - Přidat **vlastníka sdílené složky** k **FileShareOwners** skupiny.
   - Přidat **uživatele sdílené složky** k **FileShareUsers** skupiny.

#### <a name="provision-groups-and-accounts-in-a-workgroup"></a>Zřízení skupin a účtů v pracovní skupině

>[!NOTE]
> Když konfigurujete souborového serveru, spusťte následující příkazy z **příkazový řádek správce**. <br>***Nepoužívejte prostředí PowerShell.***

Při použití šablony Azure Resource Manageru jsou uživatelé už vytvořili.

1. Spuštěním následujících příkazů vytvořte vlastníka sdílené složky a přihlašovací účty. Nahraďte `<password>` vlastními hodnotami.

   ``` DOS
   net user FileShareOwner <password> /add /expires:never /passwordchg:no
   net user FileShareUser <password> /add /expires:never /passwordchg:no
   ```

2. Nastavení hesla pro účty bez vypršení platnosti tak spustíte následující příkazy:

   ``` DOS
   WMIC USERACCOUNT WHERE "Name='FileShareOwner'" SET PasswordExpires=FALSE
   WMIC USERACCOUNT WHERE "Name='FileShareUser'" SET PasswordExpires=FALSE
   ```

3. Vytvoří místní skupiny FileShareUsers a FileShareOwners a do nich přidejte účty v prvním kroku:

   ``` DOS
   net localgroup FileShareUsers /add
   net localgroup FileShareUsers FileShareUser /add
   net localgroup FileShareOwners /add
   net localgroup FileShareOwners FileShareOwner /add
   ```

#### <a name="provision-the-content-share"></a>Poskytování obsahu sdílené složky

Sdílená složka obsahu obsahuje obsah webu tenanta. Postup pro zřízení sdílené složky obsahu na jeden souborový server je stejný pro prostředí služby Active Directory a pracovní skupiny. Ale to se liší pro cluster převzetí služeb při selhání ve službě Active Directory.

#### <a name="provision-the-content-share-on-a-single-file-server-active-directory-or-workgroup"></a>Zřízení sdílené složky obsahu na jeden souborový server (Active Directory nebo pracovní skupiny)

Jeden souborový server spusťte následující příkazy v příkazovém řádku se zvýšenými oprávněními. Nahraďte hodnotu pro `C:\WebSites` s příslušnými cestami ve vašem prostředí.

```DOS
set WEBSITES_SHARE=WebSites
set WEBSITES_FOLDER=C:\WebSites
md %WEBSITES_FOLDER%
net share %WEBSITES_SHARE% /delete
net share %WEBSITES_SHARE%=%WEBSITES_FOLDER% /grant:Everyone,full
```

### <a name="configure-access-control-to-the-shares"></a>Konfigurace řízení přístupu ke sdíleným složkám

Spusťte následující příkazy v příkazovém řádku se zvýšenými oprávněními na souborovém serveru nebo na uzlu clusteru převzetí služeb při selhání, na kterém je aktuálním vlastníkem prostředku clusteru. Nahraďte hodnoty kurzívou hodnoty, které jsou specifické pro vaše prostředí.

#### <a name="active-directory"></a>Active Directory

```DOS
set DOMAIN=<DOMAIN>
set WEBSITES_FOLDER=C:\WebSites
icacls %WEBSITES_FOLDER% /reset
icacls %WEBSITES_FOLDER% /grant Administrators:(OI)(CI)(F)
icacls %WEBSITES_FOLDER% /grant %DOMAIN%\FileShareOwners:(OI)(CI)(M)
icacls %WEBSITES_FOLDER% /inheritance:r
icacls %WEBSITES_FOLDER% /grant %DOMAIN%\FileShareUsers:(CI)(S,X,RA)
icacls %WEBSITES_FOLDER% /grant *S-1-1-0:(OI)(CI)(IO)(RA,REA,RD)
```

#### <a name="workgroup"></a>Pracovní skupina

```DOS
set WEBSITES_FOLDER=C:\WebSites
icacls %WEBSITES_FOLDER% /reset
icacls %WEBSITES_FOLDER% /grant Administrators:(OI)(CI)(F)
icacls %WEBSITES_FOLDER% /grant FileShareOwners:(OI)(CI)(M)
icacls %WEBSITES_FOLDER% /inheritance:r
icacls %WEBSITES_FOLDER% /grant FileShareUsers:(CI)(S,X,RA)
icacls %WEBSITES_FOLDER% /grant *S-1-1-0:(OI)(CI)(IO)(RA,REA,RD)
```

## <a name="prepare-the-sql-server-instance"></a>Připravit instanci systému SQL Server

Pro Azure App Service v Azure stacku hostování a monitorování míry využití databáze musíte připravit instanci SQL serveru pro uchování databáze služby App Service.

Pro nasazení Azure Stack Development Kit, můžete použít SQL serveru Express 2014 SP2 nebo novější.

Pro produkci a vysokou dostupnost, doporučujeme použít plnou verzi SQL Server 2014 SP2 nebo novější, povolit ověřování ve smíšeném režimu a nasazení v [konfiguraci s vysokou dostupností](https://docs.microsoft.com/sql/sql-server/failover-clusters/high-availability-solutions-sql-server).

Instanci systému SQL Server pro službu Azure App Service ve službě Azure Stack musí být přístupné ze všech rolí služby App Service. Můžete nasadit SQL Server v rámci výchozí předplatné poskytovatele ve službě Azure Stack. Můžete taky použít stávající infrastruktury v rámci vaší organizace (za předpokladu, že je připojení ke službě Azure Stack). Pokud používáte image Azure Marketplace, nezapomeňte nakonfigurovat bránu firewall odpovídajícím způsobem.

>[!NOTE]
> Počet imagí virtuálních počítačů SQL IaaS jsou dostupné prostřednictvím funkce pro správu webu Marketplace. Ujistěte se, že můžete kdykoli stáhnout nejnovější verzi rozšíření SQL IaaS, před nasazením virtuálního počítače pomocí položky Marketplace. Image s SQL serverem jsou stejné jako virtuální počítače SQL, které jsou dostupné v Azure. Pro virtuální počítače vytvořené z těchto imagí rozšíření IaaS a odpovídající portálu vylepšení SQL poskytují funkce, jako jsou automatické opravy a zálohování.
>
Pro všechny role systému SQL Server můžete použít výchozí instance nebo pojmenované instance. Pokud používáte pojmenovanou instanci, je potřeba ručně spusťte službu SQL Server Browser a otevřete port 1434.

>[!IMPORTANT]
> Pokud budete chtít nasadit službu App Service v existující virtuální sítě serveru SQL Server musí být nasazené do samostatné podsítě služby App Service a souborový Server.
>

## <a name="create-an-azure-active-directory-application"></a>Vytvoření aplikace Azure Active Directory

Konfigurace instančního objektu služby Azure AD podporují následující operace:

- Škálovací sady virtuálních počítačů integrace na vrstvy pracovních procesů.
- Jednotné přihlašování k portálu a pokročilé vývojářské nástroje Azure Functions.

Tento postup platí pro pouze prostředí Azure AD zabezpečené Azure Stack.

Správci musí nakonfigurovat jednotné přihlašování pro:

- Povolte nástroje pro pokročilé vývojáře ve službě App Service (Kudu).
- Povolte použití možnosti portálu Azure Functions.

Postupujte následovně:

1. Otevřete prostředí PowerShell instanci jako azurestack\AzureStackAdmin.
2. Přejděte do umístění skripty, které jste stažené a rozbalené v [požadovaný krok](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-before-you-get-started).
3. [Instalace Powershellu pro Azure Stack](azure-stack-powershell-install.md).
4. Spustit **vytvořit AADIdentityApp.ps1** skriptu. Jakmile budete vyzváni, zadejte ID tenanta Azure AD, který používáte pro nasazení Azure Stack. Zadejte například **myazurestack.onmicrosoft.com**.
5. V **přihlašovacích údajů** okno, zadejte účet správce služby Azure AD a heslo. Vyberte **OK**.
6. Zadejte cestu k souboru certifikátu a heslo certifikátu pro [certifikát vytvořený dříve](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-before-you-get-started). Certifikát vytvořený pro tento krok ve výchozím nastavení je **sso.appservice.local.azurestack.external.pfx**.
7. Tento skript vytvoří novou aplikaci v instanci tenanta Azure AD. Poznamenejte si ID aplikace, který je vrácen ve výstupu prostředí PowerShell. Tyto informace během instalace budete potřebovat.
8. Otevřete nové okno prohlížeče a přihlaste se k [webu Azure portal](https://portal.azure.com) jako správce služby Azure Active Directory
9. Otevřete poskytovatele prostředků služby Azure AD.
10. Vyberte **registrace aplikací**.
11. Vyhledejte ID aplikace vrací jako součást kroku 7. Aplikace služby App Service je uvedena.
12. Vyberte **aplikace** v seznamu.
13. Vyberte **nastavení**.
14. Vyberte **požadovaná oprávnění** > **udělit oprávnění** > **Ano**.

```PowerShell
    Create-AADIdentityApp.ps1
```

| Parametr | Požadované nebo volitelné | Výchozí hodnota | Popis |
| --- | --- | --- | --- |
| DirectoryTenantName | Požaduje se | Null | ID tenanta Azure AD Zadejte identifikátor GUID nebo řetězec. Příkladem je myazureaaddirectory.onmicrosoft.com. |
| AdminArmEndpoint | Požaduje se | Null | Koncový bod Azure Resource Manageru správce. Příkladem je adminmanagement.local.azurestack.external. |
| TenantARMEndpoint | Požaduje se | Null | Koncový bod Azure Resource Manageru tenanta. Příkladem je management.local.azurestack.external. |
| AzureStackAdminCredential | Požaduje se | Null | Pověření správce služby Azure AD. |
| CertificateFilePath | Požaduje se | Null | **Úplná cesta** pro soubor certifikátu aplikace identity vygeneruje dříve. |
| CertificatePassword | Požaduje se | Null | Heslo, který pomáhá chránit soukromý klíč certifikátu. |
| Prostředí | Nepovinné | AzureCloud | Název podporované Cloudovém prostředí, ve kterém je k dispozici cílové služby Azure Active Directory Graph.  Povolené hodnoty: "AzureCloud."AzureChinaCloud",", azureusgovernment nebo' "AzureGermanCloud".|

## <a name="create-an-active-directory-federation-services-application"></a>Vytvoření aplikace Active Directory Federation Services

Pro prostředí Azure Stack zabezpečené službou AD FS musíte nakonfigurovat instanční objekt služby AD FS pro podporu následujících operací:

- Škálovací sady virtuálních počítačů integrace na vrstvy pracovních procesů.
- Jednotné přihlašování k portálu a pokročilé vývojářské nástroje Azure Functions.

Správci musí nakonfigurovat jednotné přihlašování pro:

- Nakonfigurujte hlavní název služby pro integraci sady škálování virtuálního počítače na vrstvy pracovních procesů.
- Povolte nástroje pro pokročilé vývojáře ve službě App Service (Kudu).
- Povolte použití možnosti portálu Azure Functions.

Postupujte následovně:

1. Otevřete prostředí PowerShell instanci jako azurestack\AzureStackAdmin.
2. Přejděte do umístění skripty, které jste stažené a rozbalené v [požadovaný krok](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-before-you-get-started).
3. [Instalace Powershellu pro Azure Stack](azure-stack-powershell-install.md).
4. Spustit **vytvořit ADFSIdentityApp.ps1** skriptu.
5. V **přihlašovacích údajů** okno, zadejte účet správce cloudu služby AD FS a heslo. Vyberte **OK**.
6. Zadejte cestu k souboru certifikátu a certifikát heslo pro [certifikát vytvořený dříve](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-before-you-get-started). Certifikát vytvořený pro tento krok ve výchozím nastavení je **sso.appservice.local.azurestack.external.pfx**.

```PowerShell
    Create-ADFSIdentityApp.ps1
```

| Parametr | Požadované nebo volitelné | Výchozí hodnota | Popis |
| --- | --- | --- | --- |
| AdminArmEndpoint | Požaduje se | Null | Koncový bod Azure Resource Manageru správce. Příkladem je adminmanagement.local.azurestack.external. |
| PrivilegedEndpoint | Požaduje se | Null | Privilegované koncový bod. Příkladem je AzS-ERCS01. |
| CloudAdminCredential | Požaduje se | Null | Přihlašovací údaje účtu domény pro správce cloudu Azure Stack. Příkladem je Azurestack\CloudAdmin. |
| CertificateFilePath | Požaduje se | Null | **Úplná cesta** na soubor PFX certifikátu aplikace identity. |
| CertificatePassword | Požaduje se | Null | Heslo, který pomáhá chránit soukromý klíč certifikátu. |

## <a name="next-steps"></a>Další postup

[Instalace poskytovatele prostředků App Service](azure-stack-app-service-deploy.md)
