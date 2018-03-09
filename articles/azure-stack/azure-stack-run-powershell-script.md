---
title: "Nasazení Azure zásobníku Development Kit | Microsoft Docs"
description: "Zjistěte, jak můžete připravit Azure zásobníku Development Kit a spustit skript prostředí PowerShell, který chcete nasadit."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 0ad02941-ed14-4888-8695-b82ad6e43c66
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/27/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: 6a5912117a475c7af028f01ea47a7042677992ca
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
---
# <a name="deploy-the-azure-stack-development-kit"></a>Nasazení Azure zásobníku Development Kit

*Platí pro: Azure zásobníku Development Kit*

K nasazení [Azure zásobníku Development Kit](azure-stack-poc.md), musíte provést následující kroky:

1. [Stažení balíčku pro nasazení](https://azure.microsoft.com/overview/azure-stack/try/?v=try) získat Cloudbuilder.vhdx.
2. Příprava cloudbuilder.vhdx konfigurace počítače (hostitel development kit), na kterém chcete nainstalovat development kit. Po provedení tohoto kroku se spustí Cloudbuilder.vhdx development kit hostitele.
3. Nasaďte development kit na hostiteli development kit.

> [!NOTE]
> Nejlepších výsledků dosáhnete i v případě, že chcete použít odpojené prostředí zásobníku Azure, je nejvhodnější pro nasazení během připojení k Internetu. Tímto způsobem, zkušební verze systému Windows Server 2016 zahrnuté do development kit instalace může být aktivovaný v době nasazení.

## <a name="download-and-extract-the-development-kit"></a>Stažení a extrakci development kit
1. Než začnete stahování, ujistěte se, zda počítač splňuje následující požadavky:

  - Počítač musí mít minimálně 60 GB volné místo na disku k dispozici na čtyři samostatné, stejné logické pevné disky kromě na disk operačního systému.
  - [Rozhraní .NET framework 4.6 (nebo novější verze)](https://aka.ms/r6mkiy) musí být nainstalován.

2. [Přejděte na stránku Začínáme](https://azure.microsoft.com/overview/azure-stack/try/?v=try) kde můžete stáhnout Development Kit zásobník Azure, zadejte svoje údaje a pak klikněte na tlačítko **odeslání**.
3. Stažení a spuštění [kontrolu nasazení pro Azure zásobníku Development Kit](https://go.microsoft.com/fwlink/?LinkId=828735&clcid=0x409) skriptu kontrolu požadovaných součástí. Tento skript samostatné projde požadavcích kontroly provádí instalace pro Azure zásobníku Development Kit. Poskytuje způsob, jak potvrďte, že byly splněny požadavky na hardware a software před stažením větší balíčku pro Azure zásobníku Development Kit.
4. V části **stáhnout software**, klikněte na tlačítko **Azure zásobníku Development Kit**.

  > [!NOTE]
  > Stahování ASDK (AzureStackDevelopmentKit.exe) je approximiately 10GB samostatně. Pokud si zvolíte si také stáhnout soubor ISO zkušební verze systému Windows Server 2016, zvyšuje velikost stažení přibližně 17 GB. Můžete vytvořit a přidat bitovou kopii systému Windows Server 2016 virtuálního počítače v Azure Marketplace zásobníku po dokončení instalace ASDK tento soubor ISO. Všimněte si, že tuto bitovou kopii systému Windows Server 2016 vyhodnocení lze použít pouze s ASDK a je v souladu s licenčními podmínkami ASDK.

5. Po dokončení stahování, klikněte na tlačítko **spustit** ke spuštění ASDK Self-Extractor (AzureStackDevelopmentKit.exe).
6. Přečtěte si a přijměte zobrazených licenční smlouvy z **licenční smlouvy** Self-Extractor průvodce a potom klikněte na tlačítko **Další**.
7. Přečtěte si informace o prohlášení o ochraně osobních údajů zobrazené na **důležité informace** Self-Extractor průvodce a potom klikněte na tlačítko **Další**.
8. Vyberte umístění pro instalační soubory zásobník Azure extrahovat na **vyberte cílové umístění** Self-Extractor průvodce a potom klikněte na tlačítko **Další**. Výchozí umístění je *aktuální složce*\Azure zásobníku Development Kit. 
9. Zkontrolujte souhrn na cílové umístění **připraven k extrahování** Self-Extractor průvodce, a pak klikněte na tlačítko **extrahovat** k extrakci CloudBuilder.vhdx (přibližně 25 GB) a ThirdPartyLicenses.rtf soubory. Dokončení tohoto procesu bude chvíli trvat.
10. Zkopírovat nebo přesunout soubor CloudBuilder.vhdx kořenové jednotce C:\ (C:\CloudBuilder.vhdx) na hostitelském počítači ASDK.

> [!NOTE]
> Po extrahování souborů, můžete odstranit. EXE a. KOŠ soubory k obnovení místa na pevném disku. Nebo si tyto soubory můžete zálohovat, takže nemusíte znovu stáhnout soubory, pokud potřebujete znovu nasadit ASDK.

## <a name="deploy-the-asdk-using-a-guided-experience"></a>Nasazení ASDK pomocí s kompatibilitou
ASDK se dá nasadit pomocí grafického uživatelského rozhraní (GUI) poskytované stažení a spuštění skriptu PowerShell asdk installer.ps1.

> [!NOTE]
> Instalační program uživatelské rozhraní pro Azure zásobníku Development Kit je otevřít z domácích zdrojů skriptu, na základě WCF a prostředí PowerShell.

### <a name="prepare-the-development-kit-host-using-a-guided-user-experience"></a>Příprava hostitele development kit, pomocí informací uživatelské prostředí
Před instalací ASDK na hostitelském počítači, musí být připraveny ASDK prostředí.
1. Přihlaste se jako místní správce na hostitelském počítači ASDK.
2. Ujistěte se, že CloudBuilder.vhdx soubor byl přesunut do kořenového adresáře jednotky C:\ (C:\CloudBuilder.vhdx).
3. Spusťte následující skript pro stažení instalačního souboru development kit (asdk-installer.ps1) z [nástroje úložiště GitHub zásobník Azure](https://github.com/Azure/AzureStack-Tools) k **C:\AzureStack_Installer** složky na vaše hostitelský počítač Development kit:

  ```powershell
  [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
  # Variables
  $Uri = 'https://raw.githubusercontent.com/Azure/AzureStack-Tools/master/Deployment/asdk-installer.ps1'
  $LocalPath = 'C:\AzureStack_Installer'
  # Create folder
  New-Item $LocalPath -Type directory
  # Download file
  Invoke-WebRequest $uri -OutFile ($LocalPath + '\' + 'asdk-installer.ps1')
  ```

4. Ze konzolu se zvýšenými oprávněními prostředí PowerShell, spusťte **C:\AzureStack_Installer\asdk-installer.ps1** skript a potom klikněte na **Příprava prostředí**.
5. Na **vyberte Cloudbuilder vhdx** stránka instalační program, vyhledejte a vyberte **cloudbuilder.vhdx** soubor, který stažené a rozbalené v předchozích krocích. Na této stránce můžete rovněž, Volitelně můžete povolit **přidejte ovladače** zaškrtávací políčko, pokud potřebujete přidat další ovladače k hostitelskému počítači development kit.  
6. Na **volitelné nastavení** zadejte účet místního správce pro hostitelský počítač development kit. 

  > [!IMPORTANT]
  > Pokud nezadáte tyto přihlašovací údaje, budete potřebovat přímo nebo KVM přístup k hostiteli po restartování počítače jako součást nastavení development kit.

7. Tato volitelná nastavení můžete zadat taky na **volitelné nastavení** stránky:
    - **ComputerName**: Tento parametr nastaví název hostitele development kit. Název musí být v souladu s požadavky na plně kvalifikovaný název domény a musí být maximálně 15 znaků nebo méně. Výchozí hodnota je náhodný název generované systémem Windows.
    - **Časové pásmo**: Nastaví časové pásmo pro hostitele development kit. Výchozí hodnota je (UTC-8:00) Tichomoří (USA a Kanada).
    - **Konfiguraci statické IP adresy**: Nastaví nasazení tak, aby používal statickou adresu IP. Jinak když instalační program restartuje do cloudbuilder.vhx, rozhraní sítě jsou nakonfigurované s DHCP.
11. Klikněte na **Další**.
12. Pokud jste v předchozím kroku vybrali konfiguraci statické IP adresy, musíte se teď:
    - Vyberte síťový adaptér. Ujistěte se, zda se můžete připojit k adaptéru před kliknutím na **Další**.
    - Ujistěte se, že **IP adresu**, **brány**, a **DNS** hodnoty jsou správné a potom klikněte na **Další**.
13. Klikněte na tlačítko **Další** ke spuštění procesu přípravy.
14. Určuje, kdy přípravy **dokončeno**, klikněte na tlačítko **Další**.
15. Klikněte na tlačítko **restartovat nyní** spouštění do cloudbuilder.vhdx a pokračujte v procesu nasazení.


### <a name="deploy-the-development-kit-using-a-guided-experience"></a>Nasazení sadě pro vývoj pomocí s kompatibilitou
Jakmile připravíte ASDK hostitelský počítač, se dá nasadit ASDK do bitové kopie CloudBuilder.vhdx pomocí následujících kroků. 
1. Po hostitelský počítač úspěšně spustí do bitové kopie CloudBuilder.vhdx, přihlaste se pomocí přihlašovacích údajů správce, který je zadán v předchozích krocích. 
2. Otevřete konzolu prostředí PowerShell se zvýšenými oprávněními a spusťte **\AzureStack_Installer\asdk-installer.ps1** skript (který může být nyní na jinou jednotku v bitové kopii CloudBuilder.vhdx). Klikněte na **Nainstalovat**.
3. V **typ** rozevíracího seznamu vyberte **cloudu Azure** nebo **služby AD FS**.
    - **Azure Cloud**: nakonfiguruje Azure Active Directory (Azure AD) jako zprostředkovatele identity. Chcete-li použít tuto možnost, budete potřebovat připojení k Internetu, celý název Azure AD directory klienta ve formě *domainname*. onmicrosoft.com nebo Azure AD ověřit přihlašovací údaje správce název a globální vlastní doménu pro Zadaný adresář. 
    - **Služba AD FS**: výchozí razítko adresářové služby se použije jako zprostředkovatele identity. Je výchozí účet pro přihlášení s azurestackadmin@azurestack.local, a k použití hesla je zadaný jako součást instalace.
4. V části **heslo místního správce**v **heslo** zadejte heslo místního správce (který musí odpovídat aktuální heslo místního správce nakonfigurované) a pak klikněte na tlačítko **Další**.
5. Vyberte síťový adaptér používat pro development kit a potom klikněte na **Další**.
6. Vyberte DHCP nebo konfigurace statických sítě pro virtuální počítač BGPNAT01.
    - **DHCP** (výchozí): virtuální počítač získá konfigurace sítě IP ze serveru DHCP.
    - **Statické**: tuto možnost použijte pouze v případě DHCP nelze přiřadit platnou IP adresu pro zásobník Azure pro přístup k Internetu. Statickou IP adresu musí být zadán s délkou maska podsítě ve formátu CIDR (například 10.0.0.5/24).
7. Volitelně můžete nastavte následující hodnoty:
    - **ID sítě VLAN**: Nastaví ID sítě VLAN. Tuto možnost použijte pouze v případě hostitele a AzS BGPNAT01 musíte nakonfigurovat ID sítě VLAN pro přístup k fyzické síti (a Internetu). 
    - **Server DNS pro předávání**: server A DNS je vytvořen jako součást nasazení Azure zásobníku. Pokud chcete umožnit uvnitř řešení překládat názvy mimo razítka, zadejte existující infrastrukturu DNS server. Server DNS v razítko předává Neznámý název k tomuto serveru.
    - **Čas serveru**: to vyžadováno pole nastaví čas serveru, který má být používána development kit. Tento parametr je zadat jako IP adresa serveru doby platnosti. Názvy serverů nejsou podporovány.
  
  > [!TIP]
  > Čas serveru najít IP adresu, navštivte [pool.ntp.org](http:\\pool.ntp.org) nebo příkaz ping time.windows.com. 
  
8. Klikněte na **Další**. 
9. Na **ověření vlastnostech karty síťového rozhraní** stránky, se zobrazí indikátor průběhu. 
    - Pokud se říká **nelze stáhnout aktualizace**, postupujte podle pokynů na stránce.
    - Když uvádí **dokončeno**, klikněte na tlačítko **Další**.
10. Na **Souhrn** klikněte na tlačítko **nasadit**. Zde můžete také zkopírovat příkazy instalace prostředí PowerShell, které se použijí k instalaci sady development kit.
11. Pokud používáte nasazení služby Azure AD, budete vyzváni k zadání přihlašovacích údajů účtu globálního správce Azure AD pár minut po spuštění instalace.
12. Proces nasazení může trvat několik hodin, během které automaticky restartuje se jednou. Pokud je nasazení úspěšná, zobrazí konzole PowerShell: **COMPLETE: akce, nasazení,**. Pokud se nasazení nezdaří, je možné [znovu nasaďte](azure-stack-redeploy.md) z nuly nebo použijte PowerShell následující příkazy, z téhož okna zvýšenými prostředí PowerShell, restartujte nasazení z poslední úspěšné kroku:

  ```powershell
  cd C:\CloudDeployment\Setup
  .\InstallAzureStackPOC.ps1 -Rerun
  ```

   > [!IMPORTANT]
   > Pokud chcete sledovat průběh nasazení, přihlaste se jako azurestack\AzureStackAdmin po restartu hostitele development kit během instalace. Pokud se přihlásíte jako místní správce poté, co je počítač připojený k doméně, zobrazí se průběh nasazení. Není znovu spustit nasazení, místo toho se přihlaste jako azurestack\AzureStackAdmin k ověření, zda je spuštěna.
   

## <a name="deploy-the-asdk-using-powershell"></a>Nasazení ASDK pomocí prostředí PowerShell
Předchozí kroky projít nasazení ASDK pomocí informací uživatelské prostředí. Alternativně můžete použít PowerShell k nasazení ASDK na hostiteli development kit podle kroků v této části.

### <a name="configure-the-asdk-host-computer-to-boot-from-cloudbuildervhdx"></a>Nakonfigurujte na hostitelském počítači ASDK spouštění z CloudBuilder.vhdx
Tyto příkazy bude konfiguraci počítače ASDK hostitel pro spouštění z stažené a rozbalené Azure zásobník virtuálního pevného (CloudBuilder.vhdx). Po dokončení těchto kroků, restartujte počítač ASDK hostitele.

1. Spusťte příkazový řádek jako správce.
2. Spusťte `bcdedit /copy {current} /d "Azure Stack"`.
3. Kopírování (CTRL + C) hodnotu CLSID vrátí, včetně požadované {} "s. Tato hodnota se označuje jako {CLSID} a bude nutné vložit (CTRL + V nebo klikněte pravým tlačítkem) v zbývající kroky.
4. Spusťte `bcdedit /set {CLSID} device vhd=[C:]\CloudBuilder.vhdx`. 
5. Spustit `bcdedit /set {CLSID} osdevice vhd=[C:]\CloudBuilder.vhdx` 
6. Spustit `bcdedit /set {CLSID} detecthal on` 
7. Spusťte `bcdedit /default {CLSID}`.
8. Pokud chcete ověřit nastavení spouštění, spusťte `bcdedit`. 
9. Zajistěte, aby CloudBuilder.vhdx soubor byl přesunut do kořenové jednotky C:\ (C:\CloudBuilder.vhdx) a restartujte počítač development kit hostitele. Při restartu ASDK hostitele by měl nyní výchozí pro spouštění z virtuálního počítače CloudBuilder.vhdx. 

### <a name="prepare-the-development-kit-host-using-powershell"></a>Příprava development kit hostitele pomocí prostředí PowerShell 
Po development kit hostitelský počítač úspěšně spustí do bitové kopie CloudBuilder.vhdx, můžete otevřít konzolu prostředí PowerShell se zvýšenými oprávněními a spusťte příkazy v této části k nasazení ASDK na hostiteli development kit.

> [!IMPORTANT] 
> Instalace ASDK podporuje přesně jedna síťová karta (NIC) pro sítě. Pokud máte několik síťových adaptérů, ujistěte se, že je povolena pouze jedna (a všechny ostatní jsou zakázány) před spuštěním skriptu nasazení.

Zásobník Azure s Azure AD ani AD FS můžete nasadit jako zprostředkovatele identity. Azure zásobníku, zprostředkovatelé prostředků a další aplikace fungovat stejně jako s oběma. Další informace o co je podporováno se službou AD FS v zásobníku Azure, najdete v článku [klíčové funkce a koncepty](.\azure-stack-key-features.md) článku.

> [!TIP]
> Pokud nezadáte žádné parametry instalace (viz InstallAzureStackPOC.ps1 volitelnými parametry a příklady níže), budete vyzváni pro požadované parametry.

### <a name="deploy-azure-stack-using-azure-ad"></a>Nasazení zásobník Azure pomocí služby Azure AD 
K nasazení Azure zásobníku **pomocí služby Azure AD jako zprostředkovatele identity**, musí mít připojení k Internetu, buď přímo nebo prostřednictvím proxy serveru transparentní. Spusťte následující příkazy prostředí PowerShell pro nasazení sadě pro vývoj pomocí služby Azure AD:

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

Proces nasazení může trvat několik hodin, během které doby automaticky restartuje se jednou. Pokud je nasazení úspěšná, zobrazí konzole PowerShell: **COMPLETE: akce, nasazení,**. Pokud se nasazení nezdaří, pokuste se spustit skript znovu s použitím parametru-spustit znovu. Nebo můžete [znovu nasaďte ASDK](.\azure-stack-redeploy.md) od začátku.
> [!IMPORTANT]
> Pokud chcete sledovat průběh nasazení po restartování hostitele ASDK, musíte se přihlásit jako AzureStack\AzureStackAdmin. Pokud se přihlásíte jako místní správce po hostitelský počítač se restartuje (a připojený k doméně azurestack.local), nemáte zobrazen průběh nasazení. Není znovu spustit nasazení, místo toho se přihlaste jako azurestack k ověření, zda je spuštěna.
>

#### <a name="azure-ad-deployment-script-examples"></a>Příklady skriptů nasazení služby Azure AD
Můžete skript celého nasazení služby Azure AD. Zde jsou některé příklady komentáři, které obsahují některé volitelné parametry.

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

Pokud vaše prostředí **nemá** mít DHCP povoleno, musí zahrnovat následující další parametry, které chcete jednu z možností výše (příklad použití zadané): 

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

## <a name="activate-the-administrator-and-tenant-portals"></a>Aktivovat správce a klienta portálech
Po nasazení, které používají Azure AD je nutné aktivovat obou zásobník Azure správce a klienta portálů. Tato aktivace souhlasí s uvedením zásobník Azure portal a Azure Resource Manager správná oprávnění (uvedené na stránce souhlas) pro všechny uživatele adresáři.

- Pro správce portálu, přejděte na https://adminportal.local.azurestack.external/guest/signup, přečtěte si informace a klikněte **přijmout**. Po přijetí, můžete přidat správce služby, kteří nejsou také správci klientů adresáře.

- Pro portál pro klienty, přejděte na https://portal.local.azurestack.external/guest/signup, přečtěte si informace a pak klikněte na tlačítko **přijmout**. Po přijetí, můžete uživatele v adresáři přihlásit na portál pro klienty. 

> [!NOTE] 
> Pokud nejsou aktivovány portálů, pouze správce adresáře může přihlásit a používat portálů. Pokud jiný uživatel přihlásí, zobrazí chybu, která sděluje, že správce nebyla udělena oprávnění ostatním uživatelům. Když správce nepatří do adresáře, který je zaregistrován v Azure zásobníku nativně, musí být adresáři Azure zásobníku připojeno k adrese URL pro aktivaci. Například pokud zásobník Azure je registrován fabrikam.onmicrosoft.com a uživatel s oprávněními správce je admin@contoso.com, přejděte na https://portal.local.azurestack.external/guest/signup/fabrikam.onmicrosoft.com aktivovat na portálu. 

## <a name="reset-the-password-expiration-policy"></a>Resetování zásad vypršení platnosti hesla 
Pokud chcete mít jistotu, že se heslo pro hostitele development kit nevyprší před ukončením svého zkušební období, postupujte podle těchto kroků po nasazení ASDK.

### <a name="to-change-the-password-expiration-policy-from-powershell"></a>Změna zásad vypršení platnosti hesla z prostředí Powershell:
Z prostředí Powershell konzolu se zvýšenými oprávněními spusťte příkaz:

```powershell
Set-ADDefaultDomainPasswordPolicy -MaxPasswordAge 180.00:00:00 -Identity azurestack.local
```

### <a name="to-change-the-password-expiration-policy-manually"></a>Změna zásad vypršení platnosti hesla ručně:
1. Na hostiteli development kit, otevřete **Správa zásad skupiny** a přejděte do **Správa zásad skupiny** – **doménové struktury: azurestack.local** – **domény** – **azurestack.local**.
2. Klikněte pravým tlačítkem na **výchozí zásady domény** a klikněte na tlačítko **upravit**.
3. V editoru zásad skupiny správy, přejděte na **konfigurace počítače** – **zásady** – **nastavení systému Windows** – **nastavení zabezpečení**– **Zásady účtů** – **zásady hesel**.
4. V pravém podokně klikněte dvakrát na **maximální stáří hesla**.
5. V **maximální stáří hesla vlastnosti** dialogové okno, změny **heslo vyprší za** hodnoty na 180 a pak klikněte na **OK**.


## <a name="next-steps"></a>Další postup

[Připojení ke službě Azure Stack](azure-stack-connect-azure-stack.md)

[Instalace prostředí PowerShell pro Azure zásobníku prostředí](azure-stack-powershell-configure-quickstart.md)

[Registrace Azure zásobníku u vašeho předplatného Azure](azure-stack-register.md)



