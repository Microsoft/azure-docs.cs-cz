---
title: Nasazení Azure Stack – PowerShell | Dokumentace Microsoftu
description: V tomto článku nainstalujete ASDK z příkazového řádku pomocí prostředí PowerShell.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: ''
ms.date: 02/08/2019
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 02/08/2019
ms.openlocfilehash: 0fb3e9cd193e570a965d6bbd3e16c86dc39de350
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2019
ms.locfileid: "55984269"
---
# <a name="deploy-the-asdk-from-the-command-line"></a>Nasazení ASDK z příkazového řádku
ASDK je vývoj a testování prostředí, které můžete nasadit k vyhodnocení a k předvedení funkcí služby Azure Stack a služeb. K jeho získání pracovat, musíte připravit prostředí hardwaru a spustit některé skripty (bude to trvat i několik hodin). Potom můžete přihlásit na portály správce a uživatele chcete začít používat Azure Stack.

## <a name="prerequisites"></a>Požadavky 
Příprava hostitelském počítači development kit. Plánování hardwaru, softwaru a síti. Počítač, který je hostitelem development kit (hostitel development kit) musí splňovat hardware, software a požadavky na síť. Je nutné také vybrat možnost pomocí Azure Active Directory (Azure AD) nebo Active Directory Federation Services (AD FS). Ujistěte se, že splňují tyto požadavky před spuštěním nasazení tak, aby proces instalace běží plynule. 

Před nasazením ASDK Ujistěte se, že hardware počítače plánované development kit hostitele, operační systém, účet a konfigurace sítě splňují minimální požadavky pro instalaci ASDK.

**[Projděte si informace o plánování nasazení ASDK](asdk-deploy-considerations.md)**

> [!TIP]
> Můžete použít [Zkontrolujte požadavky na nasazení služby Azure Stack nástroj](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b) po instalaci operačního systému pro potvrzení, že váš hardware splňuje všechny požadavky.

## <a name="download-and-extract-the-deployment-package"></a>Stáhněte a rozbalte balíček pro nasazení
Až se ujistíte, že hostitelského počítače development kit splňuje základní požadavky na instalaci ASDK, je dalším krokem stažení a extrakci ASDK balíček pro nasazení. Balíček pro nasazení obsahuje Cloudbuilder.vhdx soubor, který je virtuální pevný disk, který obsahuje spustitelnou operační systém a instalační soubory služby Azure Stack.

Balíček pro nasazení můžete stáhnout na hostitele development kit, nebo do jiného počítače. Soubory extrahované nasazení trvat až 60 GB volného místa na disku, takže na jiném počítači může pomoct snížit požadavky na hardware pro hostitele development kit.

**[Stažení a extrakci Azure Stack Development Kit (ASDK)](asdk-download.md)**

## <a name="prepare-the-development-kit-host-computer"></a>Příprava hostitelském počítači development kit
Před instalací ASDK v hostitelském počítači, musí být připravené prostředí a systém konfigurován pro spouštění z virtuálního pevného disku. Po provedení tohoto kroku se development kit hostitele spustí Cloudbuilder.vhdx (virtuální pevný disk, který obsahuje spustitelnou operační systém a instalační soubory služby Azure Stack).

Nakonfigurujte na hostitelském počítači ASDK Execution CloudBuilder.vhdx pomocí prostředí PowerShell. Tyto příkazy nakonfigurují hostitelského počítače ASDK Execution stažený a extrahované Azure Stack virtuální pevný disk (CloudBuilder.vhdx). Po dokončení těchto kroků, restartujte hostitelský počítač ASDK.

Pokud chcete nakonfigurovat počítač hostitele ASDK Execution CloudBuilder.vhdx:

  1. Spusťte příkazový řádek jako správce.
  2. Spusťte `bcdedit /copy {current} /d "Azure Stack"`.
  3. Kopírovat (CTRL + C) hodnotu CLSID vrátila, včetně požadované {}"s. Tato hodnota se označuje jako {identifikátor CLSID} a bude nutné ho šlo vložit do (CTRL + V nebo klikněte pravým tlačítkem) ve zbývajících krocích.
  4. Spusťte `bcdedit /set {CLSID} device vhd=[C:]\CloudBuilder.vhdx`. 
  5. Spustit `bcdedit /set {CLSID} osdevice vhd=[C:]\CloudBuilder.vhdx` 
  6. Spustit `bcdedit /set {CLSID} detecthal on` 
  7. Spusťte `bcdedit /default {CLSID}`.
  8. Pokud chcete ověřit nastavení spouštění, spusťte `bcdedit`. 
  9. Ujistěte se, že CloudBuilder.vhdx soubor byl přesunut do kořenové složce jednotky C:\ (C:\CloudBuilder.vhdx) a restartujte hostitelský počítač development kit. Při restartování hostitelského počítače ASDK by měl spustit z pevného disku virtuálního počítače CloudBuilder.vhdx zahájíte ASDK nasazení. 

> [!IMPORTANT]
> Ujistěte se, že máte před restartováním přímé fyzické nebo KVM přístup k hostitelskému počítači development kit. Při prvním spuštění virtuálního počítače, budete vyzváni k dokončení instalace systému Windows Server. Zadejte stejné přihlašovací údaje správce, který jste použili pro přihlášení na hostitelském počítači development kit. 

### <a name="prepare-the-development-kit-host-using-powershell"></a>Příprava hostitele development kit pomocí Powershellu 
Po vývojová sada hostitelský počítač úspěšně spustí do bitové kopie CloudBuilder.vhdx, přihlaste se pomocí stejných přihlašovacích údajů místního správce jste použili k přihlášení na hostitelském počítači development kit (a, které jste zadali jako součást dokončení Windows Server Při instalaci hostitelském počítači spuštěn z virtuálního pevného disku). 

> [!NOTE]
> Volitelně můžete také nakonfigurovat [nastavení telemetrie Azure Stack](asdk-telemetry.md#set-telemetry-level-in-the-windows-registry) *před* instalaci ASDK.

Otevřete konzolu Powershellu se zvýšenými oprávněními a spusťte příkazy v této části nasazení ASDK na hostiteli development kit.

> [!IMPORTANT] 
> Instalace ASDK podporuje přesně jednu síťovou kartu (NIC) sítě. Pokud máte více síťových adaptérů, ujistěte se, že je povolená jenom jedna (a všechny ostatní jsou zakázané) před spuštěním skriptu nasazení.

Můžete nasadit Azure Stack s Azure AD nebo Windows Server AD FS jako zprostředkovatele identity. Azure Stack, poskytovatelů prostředků a další aplikace fungovat stejným způsobem jako s oběma.

> [!TIP]
> Pokud nezadáte žádné parametry instalace (viz InstallAzureStackPOC.ps1 volitelných parametrů a příklady níže), zobrazí se výzva k zadání požadovaných parametrů.

### <a name="deploy-azure-stack-using-azure-ad"></a>Nasazení Azure Stack pomocí služby Azure AD 
Nasazení Azure Stack **pomocí služby Azure AD jako zprostředkovatele identity**, musí mít připojení k Internetu přímo nebo prostřednictvím transparentní proxy server. 

Spusťte následující příkazy Powershellu k nasazení vývojové sady pomocí Azure AD:

  ```powershell
  cd C:\CloudDeployment\Setup     
  $adminpass = Get-Credential Administrator     
  .\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password
  ```

Několik minut, než se do instalace ASDK, se zobrazí výzva přihlašovacích údajů Azure AD. Musíte zadat přihlašovací údaje globálního správce pro vašeho tenanta Azure AD. 

Po nasazení není potřeba oprávnění globálního správce Azure Active Directory. Některé operace však může vyžadovat přihlašovací údaje globálního správce. Například skript instalační program zprostředkovatele prostředků nebo nová funkce vyžaduje oprávnění bylo uděleno. Můžete buď dočasně znovu vytvořit oprávnění účtu globálního správce nebo použijte samostatné globální správce účtu, který je vlastníkem *výchozí předplatné poskytovatele*.

### <a name="deploy-azure-stack-using-ad-fs"></a>Nasazení Azure Stack pomocí služby AD FS 
K nasazení vývojové sady **pomocí služby AD FS jako zprostředkovatele identity**, spusťte následující příkazy Powershellu (stačí přidat parametr - UseADFS): 

  ```powershell
  cd C:\CloudDeployment\Setup     
  $adminpass = Get-Credential Administrator 
  .\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -UseADFS
  ```

V nasazení služby AD FS výchozí razítko adresářové služby se používá jako zprostředkovatele identity. Je výchozí účet pro přihlášení pomocí azurestackadmin@azurestack.local, a heslo se nastaví na zadaný jako součást instalace příkazy prostředí PowerShell.

Proces nasazení může trvat několik hodin, během kterých systém automaticky restartuje jednou. Po úspěšném nasazení se zobrazí konzola Powershellu: **DOKONČENÍ: Akce "Nasazení"**. Pokud se nasazení nezdaří, můžete zkusit spuštění skriptu znovu pomocí parametru-opětovného spuštění. Nebo můžete [znovu nasadit ASDK](asdk-redeploy.md) úplně od začátku.

> [!IMPORTANT]
> Pokud chcete monitorovat průběh nasazení po restartování hostitele ASDK, musíte se přihlásit jako AzureStack\AzureStackAdmin. Pokud se přihlásíte jako místní správce poté, co hostitelského počítače restartovat (a připojený k doméně azurestack.local), zobrazí se průběh nasazení. Nelze znovu spustit nasazení, místo toho se přihlaste jako azurestack chcete ověřit, jestli je spuštěná.


#### <a name="azure-ad-deployment-script-examples"></a>Ukázkové skripty nasazení služby Azure AD
Můžete používat skripty pro celé nasazení služby Azure AD. Tady je pár příkladů komentářem, které obsahují některé volitelné parametry.

Pokud Azure AD identity je přidružený jenom **jeden** adresář Azure AD:
```powershell
cd C:\CloudDeployment\Setup 
$adminpass = Get-Credential Administrator 
$aadcred = Get-Credential "<Azure AD global administrator account name>" 
.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -InfraAzureDirectoryTenantAdminCredential $aadcred -TimeServer 52.168.138.145 #Example time server IP address.
```

Pokud je přidružené k vaší službě Azure AD identity **větší než jedna** adresář Azure AD:
```powershell
cd C:\CloudDeployment\Setup 
$adminpass = Get-Credential Administrator 
$aadcred = Get-Credential "<Azure AD global administrator account name>" #Example: user@AADDirName.onmicrosoft.com 
.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -InfraAzureDirectoryTenantAdminCredential $aadcred -InfraAzureDirectoryTenantName "<Azure AD directory in the form of domainname.onmicrosoft.com or an Azure AD verified custom domain name>" -TimeServer 52.168.138.145 #Example time server IP address.
```

Pokud vaše prostředí nemá server DHCP, je nutné zahrnout další parametry pro jednu z možností výše (k dispozici příklady použití): 

```powershell
.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -InfraAzureDirectoryTenantAdminCredential $aadcred -TimeServer 10.222.112.26
```

### <a name="asdk-installazurestackpocps1-optional-parameters"></a>Volitelné parametry ASDK InstallAzureStackPOC.ps1
|Parametr|Požadované a volitelné|Popis|
|-----|-----|-----|
|AdminPassword|Požaduje se|Nastaví účet místního správce a všechny další uživatelské účty na všech virtuálních počítačích vytvořených jako součást nasazení vývojové sady. Toto heslo musí odpovídat aktuální heslo místního správce na hostiteli.|
|InfraAzureDirectoryTenantName|Požaduje se|Nastaví adresář tenanta. Tento parametr použijte k určení konkrétního adresáře ve kterém má účet AAD oprávnění ke správě více adresářů. Úplný název Tenanta adresáře služby AAD ve formátu. onmicrosoft.com, nebo Azure AD ověřit vlastní název domény.|
|TimeServer|Požaduje se|Tento parametr použijte k určení serveru určený čas. Tento parametr musí být ve formě IP adresy serveru platný čas. Názvy serverů nejsou podporovány.|
|InfraAzureDirectoryTenantAdminCredential|Nepovinné|Nastaví Azure Active Directory uživatelské jméno a heslo. Tyto přihlašovací údaje Azure musí být identifikátor organizace.|
|InfraAzureEnvironment|Nepovinné|Vyberte prostředí Azure, se kterou chcete zaregistrovat toto nasazení Azure Stack. Mezi možnosti patří veřejný Azure, Azure – Čína, Azure – pro státní správu USA.|
|DNSForwarder|Nepovinné|DNS server se vytvoří jako součást nasazení Azure Stack. Povolit počítačům uvnitř řešení k překladu názvů mimo razítka, zadejte existující infrastrukturu DNS server. Server DNS v razítku předá požadavky na řešení Neznámý název k tomuto serveru.|
|Znovu spustit|Nepovinné|Pomocí tohoto příznaku znovu spustit nasazení. Používá se všechny předchozí vstup. Znovu zadávat data dříve k dispozici není podporována, protože jsou generovány a použita pro nasazení několika jedinečné hodnoty.|


## <a name="perform-post-deployment-configurations"></a>Provedení konfigurace po nasazení
Po instalaci ASDK, existuje několik doporučených kontroly po instalaci a změny konfigurace, které by měly být. Můžete ověřit instalaci nainstaloval úspěšně pomocí rutiny test-AzureStack a nainstalujte nástroje pro Azure Stack Powershellu a Githubu. 

Také byste měli obnovit zásady vypršení platnosti hesla, abyste měli jistotu, že heslo pro hostitele development kit platnost pasu nevyprší před vám zkušební období skončí.

> [!NOTE]
> Volitelně můžete také nakonfigurovat [nastavení telemetrie Azure Stack](asdk-telemetry.md#enable-or-disable-telemetry-after-deployment) *po* instalace ASDK.

**[Úlohy nasazení ASDK po](asdk-post-deploy.md)**

## <a name="register-with-azure"></a>Zaregistrujte v Azure
Azure Stack musí registraci v Azure, abyste mohli [stažení položek z Azure marketplace](asdk-marketplace-item.md) do služby Azure Stack.

**[Registrace Azure Stack s Azure](asdk-register.md)**

## <a name="next-steps"></a>Další postup
Blahopřejeme! Po dokončení těchto kroků, budete mít vývojové prostředí sady s oběma [správce](https://adminportal.local.azurestack.external) a [uživatele](https://portal.local.azurestack.external) portálů. 

[Po dokončení instalace ASDK úlohy konfigurace](asdk-post-deploy.md)

