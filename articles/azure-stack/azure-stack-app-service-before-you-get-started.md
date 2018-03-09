---
title: "Před nasazením služby App Service v zásobníku Azure | Microsoft Docs"
description: "Kroky k dokončení před nasazením služby App Service v Azure zásobníku"
services: azure-stack
documentationcenter: 
author: apwestgarth
manager: stefsch
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/02/2018
ms.author: anwestg
ms.openlocfilehash: f400180bc71efc6766b73b098c1f82542eec86f7
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
---
# <a name="before-you-get-started-with-app-service-on-azure-stack"></a>Před zahájením práce s App Service v Azure zásobníku

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

Před nasazením služby Azure App Service v zásobníku Azure, musíte splnit požadavky v tomto článku.

## <a name="download-the-installer-and-helper-scripts"></a>Stáhněte si skripty Instalační program a pomocné rutiny

1. Stažení [služby App Service na Azure zásobníku nasazení pomocníka skripty](https://aka.ms/appsvconmashelpers).
2. Stažení [služby App Service na Instalační službě Azure zásobníku](https://aka.ms/appsvconmasinstaller).
3. Extrahujte soubory ze souboru ZIP, skripty pomocné rutiny. Následující soubory a struktura složek se zobrazí:
   - Common.ps1
   - Create-AADIdentityApp.ps1
   - Create-ADFSIdentityApp.ps1
   - Create-AppServiceCerts.ps1
   - Get-AzureStackRootCert.ps1
   - Remove-AppService.ps1
   - Moduly
     - GraphAPI.psm1

## <a name="prepare-for-high-availability"></a>Příprava pro zajištění vysoké dostupnosti

Azure App Service v Azure zásobníku nelze nabízejí aktuálně vysokou dostupnost, protože zásobník Azure nasadí úloh pouze jednu doménu selhání.

Příprava Azure App Service v zásobníku Azure pro vysokou dostupnost, nasaďte požadované souborového serveru a instance systému SQL Server v konfiguraci s vysokou dostupností. Pokud Azure zásobníku podporuje více domén selhání, budou příručka neobsahuje pokyny k povolení služby Azure App Service v Azure zásobníku v konfiguraci s vysokou dostupností.

## <a name="get-certificates"></a>Získání certifikátů

### <a name="azure-resource-manager-root-certificate-for-azure-stack"></a>Azure Resource Manager kořenový certifikát pro Azure zásobníku

V relaci prostředí PowerShell spuštěna jako azurestack\CloudAdmin na počítači, který můžete dosáhnout privilegované koncového bodu na integrovaný systém zásobník Azure nebo Azure zásobníku Development Kit hostitele spusťte skript Get-AzureStackRootCert.ps1 ze složky, které jste extrahovali skripty pomocné rutiny. Skript vytvořit kořenový certifikát ve stejné složce jako skript, který potřebuje služby App Service pro vytváření certifikátů.

| Get-AzureStackRootCert.ps1 parameter | Požadované nebo volitelné | Výchozí hodnota | Popis |
| --- | --- | --- | --- |
| PrivilegedEndpoint | Požaduje se | AzS-ERCS01 | Privilegované koncový bod |
| CloudAdminCredential | Požaduje se | AzureStack\CloudAdmin | Pověření pro účet domény pro admins cloudu Azure zásobníku |

### <a name="certificates-required-for-the-azure-stack-development-kit"></a>Certifikáty požadované pro Azure zásobníku Development Kit

První skript funguje s certifikační autority zásobník Azure k vytvoření čtyři certifikátů, které potřebuje služby App Service:

| Název souboru | Použití |
| --- | --- |
| _.appservice.local.azurestack.external.pfx | Certifikát SSL výchozí služby App Service |
| api.appservice.local.azurestack.external.pfx | Certifikát SSL rozhraní API služby aplikace |
| ftp.appservice.local.azurestack.external.pfx | Certifikát SSL služby App Service vydavatele |
| sso.appservice.local.azurestack.external.pfx | Certifikátu identity aplikace služby App Service |

Spuštění skriptu na hostiteli Azure zásobníku Development Kit a ujistěte se, že používáte prostředí PowerShell jako azurestack\CloudAdmin:

1. V relaci prostředí PowerShell spuštěna jako azurestack\AzureStackAdmin spusťte skript vytvořit AppServiceCerts.ps1 ze složky, které jste extrahovali skripty pomocné rutiny. Tento skript vytvoří čtyři certifikáty ve stejné složce jako skript, který potřebuje služby App Service pro vytváření certifikátů.
2. Zadejte heslo k zabezpečení soubory PFX a poznamenejte si ho. Musíte zadat ji ve službě App Service na Azure zásobníku Instalační služby.

#### <a name="create-appservicecertsps1-parameters"></a>Create-AppServiceCerts.ps1 parameters

| Parametr | Požadované nebo volitelné | Výchozí hodnota | Popis |
| --- | --- | --- | --- |
| PfxPassword | Požaduje se | Null | Heslo, který pomáhá chránit privátní klíč certifikátu |
| DomainName | Požaduje se | local.azurestack.external | Azure příponu zásobníku oblast a domény |

### <a name="certificates-required-for-a-production-deployment-of-azure-app-service-on-azure-stack"></a>Certifikáty požadované pro produkční nasazení služby Azure App Service v Azure zásobníku

Provoz poskytovatele prostředků v produkčním prostředí, je nutné zadat následující čtyři certifikáty:

#### <a name="default-domain-certificate"></a>Výchozí certifikát domény

Výchozí certifikát domény je umístěn v roli Front-endu. Uživatelské aplikace pro zástupný znak nebo výchozí domény požadavky do služby Azure App Service pomocí tohoto certifikátu. Certifikát je také použít k řízení operací se zdrojovým (Kudu).

Certifikát musí být ve formátu .pfx a musí být certifikát se zástupným znakem tři subjektu. To umožňuje jeden certifikát tak, aby pokrývalo výchozí doménu a SCM koncový bod pro operace zdroj ovládacího prvku.

| Formát | Příklad: |
| --- | --- |
| \*.appservice.\<region\>.\<DomainName\>.\<extension\> | \*.appservice.redmond.azurestack.external |
| \*.scm.appservice.<region>.<DomainName>.<extension> | \*.scm.appservice.redmond.azurestack.external |
| \*.sso.appservice.<region>.<DomainName>.<extension> | \*.sso.appservice.redmond.azurestack.external |

#### <a name="api-certificate"></a>Certifikát rozhraní API

Rozhraní API certifikát je umístěn na roli správy. Zprostředkovatel prostředků se používá k lepšímu zabezpečená volání rozhraní API. Certifikát pro publikování musí obsahovat subjektem, který odpovídá položky DNS rozhraní API.

| Formát | Příklad: |
| --- | --- |
| api.appservice.\<region\>.\<DomainName\>.\<extension\> | api.appservice.redmond.azurestack.external |

#### <a name="publishing-certificate"></a>Publikování certifikátů

Certifikát pro roli vydavatele zabezpečuje FTPS přenos pro vlastníci aplikace při uložení obsahu. Certifikát pro publikování musí obsahovat subjektem, který odpovídá položce FTPS DNS.

| Formát | Příklad: |
| --- | --- |
| ftp.appservice.\<region\>.\<DomainName\>.\<extension\> | ftp.appservice.redmond.azurestack.external |

#### <a name="identity-certificate"></a>Certifikát identity

Certifikát identity aplikace umožňuje:

- Integrace mezi Azure Active Directory (Azure AD) nebo Active Directory Federation Services (AD FS) directory zásobník Azure a služby App Service pro podporu integrace s poskytovatele výpočetních prostředků.
- Jeden přihlašování scénáře pro nástroje pro pokročilé vývojáře ve službě Azure App Service v Azure zásobníku.

Certifikát pro identitu musí obsahovat subjektem, který odpovídá následujícím formátu:

| Formát | Příklad: |
| --- | --- |
| sso.appservice.\<region\>.\<DomainName\>.\<extension\> | sso.appservice.redmond.azurestack.external |

## <a name="virtual-network"></a>Virtual Network

Aplikační služba Azure v zásobníku Azure umožňuje nasadit poskytovatele prostředků do existující virtuální síť.  To umožňuje použití interní IP adresy pro připojení k souborovému serveru a vyžaduje službou Azure App Service v Azure zásobníku serveru SQL server.  Virtuální síť musí být nakonfigurované následující rozsah adres a podsítí před instalací služby Azure App Service v zásobníku Azure:

Virtuální síť – /16

Podsítě

ControllersSubnet /24 ManagementServersSubnet /24 FrontEndsSubnet /24 PublishersSubnet /24 WorkersSubnet /21

## <a name="prepare-the-file-server"></a>Příprava souborového serveru

Aplikační služba Azure vyžaduje použití souborového serveru. Pro nasazení v produkčním prostředí musí být souborový server nakonfigurovaný jako vysoce dostupné a umožňuje zpracovávat chyby.

Pro nasazení Azure zásobníku Development Kit můžete použít [šablonu nasazení Azure Resource Manager příklad](https://aka.ms/appsvconmasdkfstemplate) pro nasazení nakonfigurovaný jeden uzel souborového serveru. Jeden uzel souborového serveru bude v pracovní skupině.

>[!IMPORTANT]
> Pokud se rozhodnete nasadit služby App Service v existující virtuální síť souborový Server musí být nasazené do samostatnou podsíť ze služby App Service.
>

### <a name="provision-groups-and-accounts-in-active-directory"></a>Zřídit skupin a účtů ve službě Active Directory

1. Vytvořte následující skupiny globálního zabezpečení služby Active Directory:
   - FileShareOwners
   - FileShareUsers
2. Vytvořte následující účty služby Active Directory jako účty služeb:
   - FileShareOwner
   - FileShareUser

   Zabezpečení doporučujeme uživatelům pro tyto účty (a pro všechny webové role) musí být odlišný od sebe navzájem a mít silné uživatelských jmen a hesel. Nastavení hesla s následující podmínky:
   - Povolit **heslo je platné stále**.
   - Povolit **uživatel nemůže změnit heslo**.
   - Zakázat **musí uživatel změnit heslo při příštím přihlášení**.
3. Přidejte účty na členství ve skupinách následujícím způsobem:
   - Přidat **vlastník sdílené složky** k **FileShareOwners** skupiny.
   - Přidat **FileShareUser** k **FileShareUsers** skupiny.

### <a name="provision-groups-and-accounts-in-a-workgroup"></a>Zřídit skupin a účtů v pracovní skupině.

>[!NOTE]
> Konfiguraci souborového serveru, spusťte následující příkazy v okně příkazového řádku pro správu. *Nepoužívejte prostředí PowerShell.*

Když použijete šablonu Azure Resource Manager, uživatelé jsou již vytvořeny.

1. Spusťte následující příkazy můžete vytvořit účty vlastník sdílené složky a FileShareUser. Nahraďte `<password>` vlastními hodnotami.
    ``` DOS
    net user FileShareOwner <password> /add /expires:never /passwordchg:no
    net user FileShareUser <password> /add /expires:never /passwordchg:no
    ```
2. Nastavení hesla pro účty bez vypršení platnosti spuštěním následujících příkazů WMIC:
    ``` DOS
    WMIC USERACCOUNT WHERE "Name='FileShareOwner'" SET PasswordExpires=FALSE
    WMIC USERACCOUNT WHERE "Name='FileShareUser'" SET PasswordExpires=FALSE
    ```
3. Vytvořte místní skupiny FileShareUsers a FileShareOwners a do nich přidat účty v prvním kroku:
    ``` DOS
    net localgroup FileShareUsers /add
    net localgroup FileShareUsers FileShareUser /add
    net localgroup FileShareOwners /add
    net localgroup FileShareOwners FileShareOwner /add
    ```

### <a name="provision-the-content-share"></a>Zřídit sdílené složce obsahu

Sdílené složce obsahu obsahuje obsah webu klienta. Postup přidělení sdílené složky obsahu na jeden souborový server je stejný pro prostředí služby Active Directory a pracovní skupiny. Ale se liší pro cluster s podporou převzetí služeb při selhání ve službě Active Directory.

#### <a name="provision-the-content-share-on-a-single-file-server-active-directory-or-workgroup"></a>Zřídit sdílené složky obsahu na jeden souborový server (Active Directory nebo pracovní skupině)

Na jeden souborový server, na příkazovém řádku se zvýšenými oprávněními spusťte následující příkazy. Nahraďte hodnotu pro `C:\WebSites` s příslušnými cestami ve vašem prostředí.

```DOS
set WEBSITES_SHARE=WebSites
set WEBSITES_FOLDER=C:\WebSites
md %WEBSITES_FOLDER%
net share %WEBSITES_SHARE% /delete
net share %WEBSITES_SHARE%=%WEBSITES_FOLDER% /grant:Everyone,full
```

### <a name="add-the-fileshareowners-group-to-the-local-administrators-group"></a>Přidejte skupinu FileShareOwners do místní skupiny Administrators

Vzdálená správa systému Windows fungovat správně musíte přidat FileShareOwners skupiny do místní skupiny Administrators.

#### <a name="active-directory"></a>Active Directory

Na souborovém serveru nebo na všech souborových serverů, který funguje jako uzel clusteru s podporou převzetí služeb při selhání, spusťte na příkazovém řádku se zvýšenými oprávněními následující příkazy. Nahraďte hodnotu pro `<DOMAIN>` s názvem domény, který chcete použít.

```DOS
set DOMAIN=<DOMAIN>
net localgroup Administrators %DOMAIN%\FileShareOwners /add
```

#### <a name="workgroup"></a>Pracovní skupina

Na souborovém serveru spusťte následující příkaz na příkazovém řádku se zvýšenými oprávněními:

```DOS
net localgroup Administrators FileShareOwners /add
```

### <a name="configure-access-control-to-the-shares"></a>Konfigurace řízení přístupu ke sdíleným složkám

Na příkazovém řádku se zvýšenými oprávněními spusťte následující příkazy, na souborovém serveru nebo na uzlu clusteru převzetí služeb při selhání, což je aktuální vlastník prostředku clusteru. Nahraďte hodnoty kurzívou hodnoty, které jsou specifické pro vaše prostředí.

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

## <a name="prepare-the-sql-server-instance"></a>Příprava instance systému SQL Server

Pro Azure App Service na hostování zásobník Azure a měření databáze je nutné připravit instance systému SQL Server k uchování databáze služby App Service.

Pro nasazení Azure zásobníku Development Kit, můžete použít SQL Server Express 2014 SP2 nebo novější.

Pro účely vysokou dostupnost a provozní doporučujeme použít s plnou verzí systému SQL Server 2014 SP2 nebo novější, povolit ověřování ve smíšeném režimu a nasadit v [vysoce dostupné konfiguraci](https://docs.microsoft.com/sql/sql-server/failover-clusters/high-availability-solutions-sql-server).

Instanci systému SQL Server pro službu Azure App Service v zásobníku Azure musí být přístupné ze všech rolí služby App Service. SQL Server v rámci předplatného výchozí zprostředkovatel v zásobníku Azure můžete nasadit. Můžete taky použít stávající infrastruktury v rámci vaší organizace (Pokud je k dispozici připojení k Azure zásobníku). Pokud používáte image Azure Marketplace, nezapomeňte nakonfigurovat bránu firewall, odpovídajícím způsobem.

Pro všechny role systému SQL Server můžete použít výchozí instance nebo pojmenované instance. Pokud používáte pojmenovanou instanci, je nutné ručně spusťte službu SQL Server Browser a otevřete port 1434.

>[!IMPORTANT]
> Pokud se rozhodnete nasadit služby App Service v existující virtuální síť systému SQL Server musí být nasazené do samostatnou podsíť ze služby App Service a souborový Server.
>

## <a name="create-an-azure-active-directory-application"></a>Vytvoření aplikace Azure Active Directory

Konfigurace objektu zabezpečení služby Azure AD pro podporu následujících:

- Škálovací sady virtuálních počítačů integrace v vrstvách pracovního procesu.
- Jednotné přihlašování pro nástroje portálu a pokročilé vývojáře Azure Functions.

Tento postup platí pro pouze prostředí Azure AD zabezpečené Azure zásobníku.

Správci musí nakonfigurovat jednotné přihlašování k:

- Povolte rozšířené vývojářské nástroje v rámci služby App Service (Kudu).
- Povolte používání prostředí portálu Azure Functions.

Postupujte následovně:

1. Otevřete prostředí PowerShell instance jako azurestack\AzureStackAdmin.
2. Přejděte do umístění souboru skripty, které stažené a rozbalené v [požadovaných krok](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-before-you-get-started#download-the-azure-app-service-on-azure-stack-installer-and-helper-scripts).
3. [Instalace prostředí PowerShell pro Azure zásobníku](azure-stack-powershell-install.md).
4. Spustit **vytvořit AADIdentityApp.ps1** skriptu. Když se zobrazí výzva, zadejte ID klienta Azure AD, který používáte pro vaše nasazení Azure zásobníku. Zadejte například **myazurestack.onmicrosoft.com**.
5. V **pověření** okno, zadejte účet správce služby Azure AD a heslo. Vyberte **OK**.
6. Zadejte heslo certifikátu pro a cesta k souboru certifikátu [certifikát vytvořený dříve](https://docs.microsoft.com/en-gb/azure/azure-stack/azure-stack-app-service-before-you-get-started#certificates-required-for-azure-app-service-on-azure-stack). Certifikát vytvořený pro tento krok ve výchozím nastavení je **sso.appservice.local.azurestack.external.pfx**.
7. Tento skript vytvoří novou aplikaci v instanci Azure AD klienta. Poznamenejte si ID aplikace, která je vrácena ve výstupu prostředí PowerShell. Je třeba tyto informace během instalace.
8. Otevřete nové okno prohlížeče a přihlaste se k [portál Azure](https://portal.azure.com) jako správce služby Azure Active Directory
9. Otevřete poskytovatel prostředků Azure AD.
10. Vyberte **registrace aplikace**.
11. Vyhledejte číslo ID aplikace, které jsou vrácena jako součást kroku 7. Aplikace služby App Service je uveden.
12. Vyberte **aplikace** v seznamu.
13. Klikněte na tlačítko **nastavení**.
14. Vyberte **požadovaná oprávnění** > **udělit oprávnění** > **Ano**.

| Vytvoření AADIdentityApp.ps1 parametr | Požadované nebo volitelné | Výchozí hodnota | Popis |
| --- | --- | --- | --- |
| DirectoryTenantName | Požaduje se | Null | Azure AD ID klienta. Zadejte identifikátor GUID nebo řetězec. Příkladem je myazureaaddirectory.onmicrosoft.com. |
| AdminArmEndpoint | Požaduje se | Null | Koncový bod správce Azure Resource Manager. Příkladem je adminmanagement.local.azurestack.external. |
| TenantARMEndpoint | Požaduje se | Null | Koncový bod tenant Azure Resource Manager. Příkladem je management.local.azurestack.external. |
| AzureStackAdminCredential | Požaduje se | Null | Pověření správce služby Azure AD. |
| CertificateFilePath | Požaduje se | Null | Cesta k souboru certifikátu aplikace identity generované dříve. |
| CertificatePassword | Požaduje se | Null | Heslo, který pomáhá chránit privátní klíč certifikátu. |

## <a name="create-an-active-directory-federation-services-application"></a>Vytvoření aplikace Active Directory Federation Services

Pro prostředí Azure zásobníku zabezpečeným službou AD FS musíte nakonfigurovat objektu služby AD FS pro podporu následujících:

- Škálovací sady virtuálních počítačů integrace v vrstvách pracovního procesu.
- Jednotné přihlašování pro nástroje portálu a pokročilé vývojáře Azure Functions.

Správci musí nakonfigurovat jednotné přihlašování k:

- Nakonfigurujte hlavní název služby pro integraci sady škálování virtuálního počítače v pracovní vrstvách.
- Povolte rozšířené vývojářské nástroje v rámci služby App Service (Kudu).
- Povolte používání prostředí portálu Azure Functions.

Postupujte následovně:

1. Otevřete prostředí PowerShell instance jako azurestack\AzureStackAdmin.
2. Přejděte do umístění souboru skripty, které stažené a rozbalené v [požadovaných krok](https://docs.microsoft.com/en-gb/azure/azure-stack/azure-stack-app-service-before-you-get-started#download-the-azure-app-service-on-azure-stack-installer-and-helper-scripts).
3. [Instalace prostředí PowerShell pro Azure zásobníku](azure-stack-powershell-install.md).
4. Spustit **vytvořit ADFSIdentityApp.ps1** skriptu.
5. V **pověření** okno, zadejte účet správce cloudové služby AD FS a heslo. Vyberte **OK**.
6. Zadejte heslo certifikátu pro a cesta k souboru certifikátu [certifikát vytvořený dříve](https://docs.microsoft.com/en-gb/azure/azure-stack/azure-stack-app-service-before-you-get-started#certificates-required-for-azure-app-service-on-azure-stack). Certifikát vytvořený pro tento krok ve výchozím nastavení je **sso.appservice.local.azurestack.external.pfx**.

| Create-ADFSIdentityApp.ps1  parameter | Požadované nebo volitelné | Výchozí hodnota | Popis |
| --- | --- | --- | --- |
| AdminArmEndpoint | Požaduje se | Null | Koncový bod správce Azure Resource Manager. Příkladem je adminmanagement.local.azurestack.external. |
| PrivilegedEndpoint | Požaduje se | Null | Privilegované koncový bod. Příkladem je AzS ERCS01. |
| CloudAdminCredential | Požaduje se | Null | Přihlašovací údaje účtu domény pro správce cloudu Azure zásobníku. Příkladem je Azurestack\CloudAdmin. |
| CertificateFilePath | Požaduje se | Null | Cesta k souboru certifikátu PFX identity aplikace. |
| CertificatePassword | Požaduje se | Null | Heslo, který pomáhá chránit privátní klíč certifikátu. |

## <a name="next-steps"></a>Další postup

[Nainstalujte zprostředkovatele prostředků služby App Service](azure-stack-app-service-deploy.md)
