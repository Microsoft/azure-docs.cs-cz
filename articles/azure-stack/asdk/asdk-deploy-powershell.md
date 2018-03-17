---
title: "Nasazení Azure zásobníku – prostředí PowerShell | Microsoft Docs"
description: "V tomto kurzu nainstalujte ASDK z příkazového řádku."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 48ccccaba6b7f5780f1d42dfbe5d9747c5e30292
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/17/2018
---
# <a name="tutorial-deploy-the-asdk-from-the-command-line"></a>Kurz: nasazení ASDK z příkazového řádku
V tomto kurzu nasadíte Azure zásobníku Development Kit (ASDK) z příkazového řádku v testovacím prostředí. 

ASDK je testovací a vývojové prostředí, kterou můžete nasadit k vyhodnocení a předvedení funkcí Azure zásobníku a služby. Se dá stáhnout spuštěná, musíte připravit prostředí hardwaru a spustit některé skripty (bude to trvat i několik hodin). Potom můžete přihlásit k portálů správců a uživatelů a začít používat Azure zásobníku.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Stažení a extrakci balíčku pro nasazení
> * Příprava hostitelský počítač development kit 
> * Provede konfigurace po nasazení
> * Zaregistrovat Azure

## <a name="prerequisites"></a>Požadavky 
Připravte development kit hostitelský počítač. Plánování hardwaru, softwaru a síti. Počítač, který je hostitelem development kit (hostitel development kit) musí splňovat požadavky na hardware, software a síť. Také musí zvolit pomocí Azure Active Directory (Azure AD) nebo Active Directory Federation Services (AD FS). Ujistěte se, že jste před spuštěním nasazení tak, aby proces instalace plynule v souladu se tyto požadavky. 

Před nasazením ASDK, ujistěte se, že hardware počítače plánované development kit hostitele, operační systém, účet a konfigurace sítě splňují minimální požadavky pro instalaci ASDK.

**[Přečtěte si informace o plánování nasazení ASDK](asdk-deploy-considerations.md)**

> [!TIP]
> Můžete použít [Zkontrolujte požadavky na nasazení Azure zásobníku nástroj](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b) po instalaci operačního systému na potvrďte, že váš hardware splňuje všechny požadavky.

## <a name="download-and-extract-the-deployment-package"></a>Stažení a extrakci balíčku pro nasazení
Jakmile ověříte, že hostitelského počítače development kit splňuje základní požadavky pro instalaci ASDK, je dalším krokem pro stažení a extrakci ASDK balíčku pro nasazení. Balíček pro nasazení obsahuje Cloudbuilder.vhdx soubor, který je virtuální pevný disk, který obsahuje spouštěcí operační systém a instalační soubory protokolů Azure.

Balíček pro nasazení můžete stáhnout na development kit hostitele nebo do jiného počítače. Soubory extrahované nasazení trvat až 60 GB volného místa na disku, aby pomocí jiného počítače může pomoct snížit požadavky na hardware pro hostitele development kit.

**[Stažení a extrakci Azure zásobníku Development Kit (ASDK)](asdk-download.md)**

## <a name="prepare-the-development-kit-host-computer"></a>Příprava hostitelský počítač development kit
Před instalací ASDK na hostitelském počítači, musí být připraveno prostředí a systém konfigurován pro spouštění z virtuálního pevného disku. Po provedení tohoto kroku se development kit hostitele spustí Cloudbuilder.vhdx (virtuální pevný disk obsahující spouštěcí operační systém a instalační soubory zásobník Azure).

Nakonfigurujte na hostitelském počítači ASDK spouštění z CloudBuilder.vhdx pomocí prostředí PowerShell. Tyto příkazy konfiguraci počítače ASDK hostitel pro spouštění z stažené a rozbalené Azure zásobník virtuálního pevného (CloudBuilder.vhdx). Po dokončení těchto kroků, restartujte počítač ASDK hostitele.

Konfigurace hostitelského počítače ASDK spouštění z CloudBuilder.vhdx:

  1. Spusťte příkazový řádek jako správce.
  2. Spusťte `bcdedit /copy {current} /d "Azure Stack"`.
  3. Kopírování (CTRL + C) hodnotu CLSID vrátí, včetně požadované {} "s. Tato hodnota se označuje jako {CLSID} a bude nutné vložit (CTRL + V nebo klikněte pravým tlačítkem) v zbývající kroky.
  4. Spusťte `bcdedit /set {CLSID} device vhd=[C:]\CloudBuilder.vhdx`. 
  5. Spustit `bcdedit /set {CLSID} osdevice vhd=[C:]\CloudBuilder.vhdx` 
  6. Spustit `bcdedit /set {CLSID} detecthal on` 
  7. Spusťte `bcdedit /default {CLSID}`.
  8. Pokud chcete ověřit nastavení spouštění, spusťte `bcdedit`. 
  9. Zajistěte, aby CloudBuilder.vhdx soubor byl přesunut do kořenové jednotky C:\ (C:\CloudBuilder.vhdx) a restartujte počítač development kit hostitele. Při restartování ASDK hostitelský počítač by měl spuštění z pevného disku virtuálního počítače CloudBuilder.vhdx zahájíte ASDK nasazení. 

> [!IMPORTANT]
> Zajistěte, abyste měli přímé fyzický nebo KVM přístup k hostitelskému počítači development kit před restartováním ji. Při prvním spuštění virtuálního počítače, budete vyzváni k dokončení instalace systému Windows Server. Zadejte stejné přihlašovací údaje správce, které jste použili k přihlášení do development kit hostitelský počítač. 

### <a name="prepare-the-development-kit-host-using-powershell"></a>Příprava development kit hostitele pomocí prostředí PowerShell 
Po development kit hostitelský počítač úspěšně spustí do bitové kopie CloudBuilder.vhdx, přihlaste se pomocí stejných přihlašovacích údajů místního správce jste použili k přihlášení do počítače development kit hostitele (a že jste zadali jako součást dokončení systému Windows Server Při instalaci na hostitelském počítači spuštěn z virtuálního pevného disku). 

> [!NOTE]
> Volitelně můžete také nakonfigurovat [nastavení telemetrie zásobník Azure](asdk-telemetry.md#set-telemetry-level-in-the-windows-registry) *před* instalaci ASDK.

Otevřete konzolu prostředí PowerShell se zvýšenými oprávněními a spusťte příkazy v této části k nasazení ASDK na hostiteli development kit.

> [!IMPORTANT] 
> Instalace ASDK podporuje přesně jedna síťová karta (NIC) pro sítě. Pokud máte několik síťových adaptérů, ujistěte se, že je povolena pouze jedna (a všechny ostatní jsou zakázány) před spuštěním skriptu nasazení.

Můžete nasadit zásobník Azure s Azure AD nebo Windows Server AD FS jako zprostředkovatele identity. Azure zásobníku, zprostředkovatelé prostředků a další aplikace fungovat stejně jako s oběma.

> [!TIP]
> Pokud nezadáte žádné parametry instalace (viz InstallAzureStackPOC.ps1 volitelnými parametry a příklady níže), zobrazí se výzva pro požadované parametry.

### <a name="deploy-azure-stack-using-azure-ad"></a>Nasazení zásobník Azure pomocí služby Azure AD 
K nasazení Azure zásobníku **pomocí služby Azure AD jako zprostředkovatele identity**, musí mít připojení k Internetu, buď přímo nebo prostřednictvím proxy serveru transparentní. 

Spusťte následující příkazy prostředí PowerShell pro nasazení sadě pro vývoj pomocí služby Azure AD:

  ```powershell
  cd C:\CloudDeployment\Setup     
  $adminpass = Get-Credential Administrator     
  .\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password
  ```

Několik minut do ASDK instalace budete vyzváni k přihlašovací údaje Azure AD. Je třeba zadat pověření globálního správce pro vašeho tenanta Azure AD. 

### <a name="deploy-azure-stack-using-ad-fs"></a>Nasazení zásobník Azure pomocí služby AD FS 
K nasazení development kit **pomocí služby AD FS jako zprostředkovatele identity**, spusťte následující příkazy prostředí PowerShell (stačí přidat parametr - UseADFS): 

  ```powershell
  cd C:\CloudDeployment\Setup     
  $adminpass = Get-Credential Administrator 
  .\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -UseADFS
  ```

V nasazení služby AD FS výchozí razítko adresářové služby se používá jako zprostředkovatele identity. Je výchozí účet pro přihlášení s azurestackadmin@azurestack.local, a heslo bude nastavena pro zadaný jako součást instalace příkazy prostředí PowerShell.

Proces nasazení může trvat několik hodin, během které doby automaticky restartuje se jednou. Pokud je nasazení úspěšná, zobrazí konzole PowerShell: **COMPLETE: akce, nasazení,**. Pokud se nasazení nezdaří, pokuste se spustit skript znovu s použitím parametru-spustit znovu. Nebo můžete [znovu nasaďte ASDK](asdk-redeploy.md) od začátku.

> [!IMPORTANT]
> Pokud chcete sledovat průběh nasazení po restartování hostitele ASDK, musíte se přihlásit jako AzureStack\AzureStackAdmin. Pokud se přihlásíte jako místní správce po hostitelský počítač se restartuje (a připojený k doméně azurestack.local), nemáte zobrazen průběh nasazení. Není znovu spustit nasazení, místo toho se přihlaste jako azurestack k ověření, zda je spuštěna.


#### <a name="azure-ad-deployment-script-examples"></a>Příklady skriptů nasazení služby Azure AD
Můžete skript celého nasazení služby Azure AD. Tady je několik komentáři příkladů, které obsahují některé volitelné parametry.

Pokud Azure AD identity je přiřadit pouze ke **jeden** adresář Azure AD:
```powershell
cd C:\CloudDeployment\Setup 
$adminpass = Get-Credential Administrator 
$aadcred = Get-Credential "<Azure AD global administrator account name>" 
.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -InfraAzureDirectoryTenantAdminCredential $aadcred -TimeServer 52.168.138.145 #Example time server IP address.
```

Pokud Azure AD identity přidružen **větší než jedna** adresář Azure AD:
```powershell
cd C:\CloudDeployment\Setup 
$adminpass = Get-Credential Administrator 
$aadcred = Get-Credential "<Azure AD global administrator account name>" #Example: user@AADDirName.onmicrosoft.com 
.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -InfraAzureDirectoryTenantAdminCredential $aadcred -InfraAzureDirectoryTenantName "<Azure AD directory in the form of domainname.onmicrosoft.com or an Azure AD verified custom domain name>" -TimeServer 52.168.138.145 #Example time server IP address.
```

Pokud vaše prostředí nemá povolený protokol DHCP, musíte zahrnout další parametry jednu z možností výše (příklad použití zadané): 

```powershell
.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -InfraAzureDirectoryTenantAdminCredential $aadcred -NatIPv4Subnet 10.10.10.0/24 -NatIPv4Address 10.10.10.3 -NatIPv4DefaultGateway 10.10.10.1 -TimeServer 10.222.112.26
```

### <a name="asdk-installazurestackpocps1-optional-parameters"></a>Volitelné parametry ASDK InstallAzureStackPOC.ps1
|Parametr|Požadované a volitelné|Popis|
|-----|-----|-----|
|AdminPassword|Požaduje se|Nastaví účet místního správce a všechny další uživatelské účty pro všechny virtuální počítače vytvořené jako součást nasazení development kit. Toto heslo se musí shodovat aktuální heslo místního správce na hostiteli.|
|InfraAzureDirectoryTenantName|Požaduje se|Nastaví adresář tenanta. Tento parametr použijte k určení konkrétního adresáře kde AAD účet má oprávnění ke správě více adresářů. Úplný název klienta služby AAD Directory ve formátu. onmicrosoft.com nebo Azure AD ověřit vlastní název domény.|
|TimeServer|Požaduje se|Tento parametr použijte k určení serveru určitý čas. Tento parametr je zadat jako IP adresa serveru doby platnosti. Názvy serverů nejsou podporovány.|
|InfraAzureDirectoryTenantAdminCredential|Nepovinné|Nastaví Azure Active Directory uživatelské jméno a heslo. Tyto přihlašovací údaje Azure musí být identifikátor organizace.|
|InfraAzureEnvironment|Nepovinné|Vyberte prostředí Azure, pro který chcete zaregistrovat toto nasazení Azure zásobníku. Mezi možnosti patří veřejný Azure, Azure – Čína, Azure - US Government.|
|DNSForwarder|Nepovinné|DNS server je vytvořen jako součást nasazení Azure zásobníku. Pokud chcete umožnit uvnitř řešení překládat názvy mimo razítka, zadejte existující infrastrukturu DNS server. Server DNS v razítko předává Neznámý název k tomuto serveru.|
|NatIPv4Address|Podpora překladu síťových adres DHCP vyžaduje|Nastaví statickou IP adresu pro MAS BGPNAT01. Tento parametr použijte jenom v případě, že protokol DHCP nemůže přiřadit platnou IP adresu pro přístup k internetu.|
|NatIPv4Subnet|Podpora překladu síťových adres DHCP vyžaduje|Předpona podsítě IP používají protokolu DHCP pro podporu NAT. Tento parametr použijte jenom v případě, že protokol DHCP nemůže přiřadit platnou IP adresu pro přístup k internetu.|
|PublicVlanId|Nepovinné|Nastaví ID sítě VLAN. Tento parametr použijte pouze na hostitele a MAS BGPNAT01 musíte nakonfigurovat ID sítě VLAN pro přístup k fyzické síti (a Internetu). Například.\InstallAzureStackPOC.ps1-Verbose - PublicVLan 305|
|Spustit znovu|Nepovinné|Pomocí tohoto příznaku znovu spustit nasazení. Všechny předchozí vstup se používá. Znovu zadávat data, dříve poskytnuté není podporována, protože několik jedinečné hodnoty jsou generovat a používat pro nasazení.|


## <a name="perform-post-deployment-configurations"></a>Provede konfigurace po nasazení
Po instalaci ASDK, existuje několik doporučených kontroly po instalaci a změny konfigurace, které by měly být. Můžete ověřit instalaci byla nainstalována úspěšně pomocí rutiny test-AzureStack a nainstalujte nástroje pro Azure PowerShell zásobníku a Githubu. 

Také byste měli obnovit zásad vypršení platnosti hesla a ujistěte se, že se heslo pro hostitele development kit nevyprší před ukončením svého zkušební období.

> [!NOTE]
> Volitelně můžete také nakonfigurovat [nastavení telemetrie zásobník Azure](asdk-telemetry.md#enable-or-disable-telemetry-after-deployment) *po* instalaci ASDK.

**[POST ASDK úlohy nasazení](asdk-post-deploy.md)**

## <a name="register-with-azure"></a>Zaregistrovat Azure
Je nutné, aby bylo možné zaregistrovat zásobník Azure s Azure [stažení položky Azure marketplace](asdk-marketplace-item.md) do protokolů Azure.

**[Zaregistrovat Azure zásobník Azure](asdk-register.md)**

## <a name="next-steps"></a>Další postup
Blahopřejeme! Po dokončení těchto kroků, budete mít vývojovém prostředí sady s oběma [správce](https://adminportal.local.azurestack.external) a [uživatele](https://portal.local.azurestack.external) portálů. 

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Stažení a extrakci balíčku pro nasazení
> * Příprava hostitelský počítač development kit 
> * Provede konfigurace po nasazení
> * Zaregistrovat Azure

Přechodu na v dalším kurzu se dozvíte, jak přidat položku zásobník Azure marketplace.

> [!div class="nextstepaction"]
> [Přidání položky zásobníku Azure marketplace.](asdk-marketplace-item.md)

