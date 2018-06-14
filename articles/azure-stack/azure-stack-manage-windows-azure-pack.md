---
title: Správa systému Windows Azure Pack virtuálních počítačů z Azure zásobníku | Microsoft Docs
description: Zjistěte, jak spravovat virtuální počítače Windows Azure Pack (WAP) z portálu user portal v Azure zásobníku.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 213c2792-d404-4b44-8340-235adf3f8f0b
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2018
ms.author: mabrigg
ms.openlocfilehash: a7e4896c84938b392a86f4d9609c4932324c785d
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2018
ms.locfileid: "29735820"
---
# <a name="manage-windows-azure-pack-virtual-machines-from-azure-stack"></a>Správa systému Windows Azure Pack virtuálních počítačů z Azure zásobníku

*Platí pro: Azure zásobníku Development Kit*

V sadě Azure zásobníku Development Kit můžete povolit přístup z portálu user portal zásobník Azure pro virtuální počítače běžící v systému Windows Azure Pack klientů. Uživatelé můžou pomocí portálu Azure zásobníku ke správě jejich stávající virtuální počítače IaaS a virtuální sítě. Tyto prostředky jsou k dispozici v systému Windows Azure Pack prostřednictvím základní součásti Service Provider Foundation (SPF) a Virtual Machine Manager (VMM). Konkrétně mohou uživatelé:

* Vyhledejte prostředky
* Zkontrolujte hodnoty konfigurace
* Zastavení nebo spuštění virtuálního počítače
* Připojit k virtuálnímu počítači prostřednictvím protokolu RDP (Remote Desktop)
* Vytvořit a spravovat kontrolní body
* Odstraňte virtuální počítače a virtuální sítě

Tato funkce je poskytovaný konektoru služby Windows Azure Pack pro zásobník Azure (Preview). Tento článek ukazuje, jak nakonfigurovat konektor pro prostředí s jedním uzlem zásobník Azure.

Pro tuto verzi preview konektoru mějte na paměti z následujících akcí:
* Pomocí konektoru služby Windows Azure Pack pouze v testovacích prostředích (pro Azure zásobníku a sady Windows Azure) a není v nasazení v produkčním prostředí.
* Musíte mít Windows Azure Pack aktualizace kumulativní (UR4) bodech 9.1 nebo novější a System Center SPF a VMM UR 9 nebo novější.
* Nástroj VMM a SPF součásti může být System Center 2012 R2 nebo System Center 2016.
* V obou zásobníku Azure a v systému Windows Azure Pack musíte provést kroky konfigurace.
* Pokyny platí pro jiný - Cloud Platform System CPS prostředí.
* Známé problémy najdete v tématu [řešení potíží s Microsoft Azure zásobníku](azure-stack-troubleshooting.md).


## <a name="architecture"></a>Architektura
Následující diagram znázorňuje hlavní komponenty konektoru služby Windows Azure Pack.

![Komponenty konektor služby Windows Azure Pack](media/azure-stack-manage-wap/image1.png)

Všimněte si následující podrobnosti:
* Uživatelský portál Azure zásobníku přistupuje informací o prostředku z obou cloudů (zásobník Azure a sady Windows Azure).
* Uživatel musí mít účet, který je platný v obou prostředích.
* Zásobník Azure user portal musí mít přístup k síti součástí systémem Windows Azure Pack.
* V **WAP** části diagramu, můžete zobrazit nové moduly konektor služby Windows Azure Pack (WAP rozšíření a konektor) a existující Windows Azure Pack rozhraní API pro klienty s komponenty SPF a VMM.


## <a name="identity-management"></a>Správa identit
Rozhraní API služby Windows Azure Pack klient musí důvěřovat zásobník Azure službu tokenů zabezpečení (STS).

Když uživatel provede akci prostřednictvím portálu Azure zásobníku s cílem prostředky sady Windows Azure, portál využívá rozhraní API klienta služby Windows Azure Pack. Proto ověřovací token zadaný uživatel musí pocházet z důvěryhodné služby tokenů zabezpečení. Podívejte se na následující diagram:

![Diagram ověřování konektoru služby Windows Azure Pack](media/azure-stack-manage-wap/image2.png)

Ve vývojovém prostředí sady Windows Azure Pack a zásobník Azure mají nezávislé identity zprostředkovatele. Uživatelé s přístupem obou prostředích z portálu Azure zásobníku musí mít stejné uživatelské jméno hlavní název (UPN) v obou poskytovatelů identit. Například účet  *azurestackadmin@azurestack.local*  by měla existovat také ve tokenů zabezpečení pro sadu Windows Azure. Tam, kde služby AD FS není nastavena pro podporu odchozí vztah důvěry, bude ze součástí sady Windows Azure (rozhraní API pro klienty) vytvořit vztah důvěryhodnosti k Azure zásobníku instanci služby AD FS.

## <a name="prerequisites"></a>Požadavky

### <a name="download-the-windows-azure-pack-connector"></a>Stažení konektoru služby Windows Azure Pack
Na [Microsoft Download Center](https://aka.ms/wapconnectorazurestackdlc), stáhněte si soubor .exe a rozbalte ho do místního počítače. Později zkopírujte obsah na počítači, který můžete získat přístup k prostředí systému Windows Azure Pack.

### <a name="deployment-option-requirement"></a>Požadavek na možnost nasazení
K integraci se sadou Windows Azure Pack, můžete nasadit zásobník Azure pomocí služby AD FS možnost nebo Azure Active Directory.

### <a name="connectivity-requirements"></a>Požadavky na připojení
1. Z počítače, na kterém přístup k portálu Azure zásobník uživatele, ujistěte se, že získat přístup k portálu Windows Azure Pack klienta prostřednictvím webového prohlížeče.
2. Virtuální počítač AzS WASP01 v zásobníku Azure musí být možné se připojit k počítači portálu Windows Azure Pack klienta. Ping.exe použijte k ověření připojení k síti. 
3.  Musí mít platné certifikáty pro nový konektor služby. Tyto certifikáty SSL musí být vydán důvěryhodnou certifikační autoritou (CA). Nelze použít certifikáty podepsané svým držitelem. Certifikáty SSL musí být důvěryhodný zásobník Azure (konkrétně AzS WASP01 virtuálního počítače) a jiný počítač, který klient může používat pro přístup k portálu user portal zásobník Azure.
 
    >[!NOTE]
    Protože AzS WASP01 používá systém Windows Server s možností instalace jádra serveru, můžete nástroj příkazového řádku, jako je například Certutil.ext provést import certifikátu. Může například zkopírovat soubor .cer do c:\temp na AzS WASP01 a poté spusťte příkaz **"CA" "c:\temp\certname.cer" certutil - addstore**.

4.  K navázání připojení RDP k Windows Azure Pack klientské virtuální počítače prostřednictvím portálu Azure zásobníku, prostředí Windows Azure Pack musí umožňovat provoz vzdálené plochy pro virtuální počítače klientů.
5.  Pro připojení mezi Azure zásobníku klientské virtuální počítače a virtuální počítače Windows Azure Pack klientů, jejich externí IP adresy musí být směrovatelné mezi těmito dvěma prostředími. Tyto možnosti připojení může také zahrnovat přidružení serveru DNS pro překlad názvů virtuálního počítače mezi těmito prostředími.

### <a name="iis-requirements"></a>Požadavky na služby IIS
Počítač, který je hostitelem portálu Windows Azure Pack klienta (který může být u fyzického hostitele, virtuální počítač nebo více virtuálních počítačů) musí mít příponu URL přepisování IIS nainstalovaná. Pokud ještě není nainstalovaný, si můžete stáhnout z [zde](https://www.iis.net/downloads/microsoft/url-rewrite). Pokud více virtuálních počítačů hostovat portál pro klienty, nainstalujte rozšíření na každém virtuálním počítači.

## <a name="configure-azure-stack"></a>Konfigurace Azure zásobníku
Před konfigurací konektoru služby Windows Azure Pack, musíte povolit režim více cloudu na portálu Azure zásobník uživatele. Tento režim umožňuje uživatelům vybrat z které cloudu pro přístup k prostředkům.

Pokud chcete povolit režim více cloudu, musí spusťte skript přidat AzurePackConnector.ps1 po nasazení Azure zásobníku. Následující tabulka popisuje parametry skriptu.


|  Parametr | Popis | Příklad: |   
| -------- | ------------- | ------- |  
| AzurePackClouds | Identifikátory URI konektorů Windows Azure Pack. Tyto identifikátory URI by měl odpovídat portály klientů Windows Azure Pack. | @{CloudName = "AzurePack1"; CloudEndpoint = "https://waptenantportal1:40005"},@{CloudName = "AzurePack2"; CloudEndpoint = "https://waptenantportal2:40005"}<br><br>  (Ve výchozím nastavení je hodnota portu 40005.) |  
| AzureStackCloudName | Popisek představují místní cloudu Azure zásobníku.| "AzureStack" |
| DisableMultiCloud | Přepínač zakázat režim více cloudu.| neuvedeno |
| | |

Přidat AzurePackConnector.ps1 skript můžete spustit okamžitě po nasazení nebo novější. Pro spuštění skriptu okamžitě po nasazení, použijte stejné relaci prostředí Windows PowerShell, kde Azure zásobníku nasazení dokončit. Jinak můžete otevřít novou relaci prostředí Windows PowerShell jako správce (přihlášení jako účet azurestackadmin).

1. Spusťte skript AzurePackConnector.ps1 přidat pomocí následujících příkazů (s hodnotami specifické pro vaše prostředí). Všimněte si, že skript přidat AzurePackConnector umožňuje přidat více než jeden koncový bod konektor služby Windows Azure Pack.
 
 ```powershell
    $cred = New-Object System.Management.Automation.PSCredential("cloudadmin@azurestack.local", `
    (ConvertTo-SecureString -String "<password>" -AsPlainText -Force))
    $session = New-PSSession -ComputerName 'azs-ercs01' `
     -Credential $cred `
     -ConfigurationName PrivilegedEndpoint `
     -Authentication Credssp

    # Enable Multicloud
    Invoke-Command -Session $session -ScriptBlock { Add-AzurePackConnector -AzurePackClouds `
    @{CloudName = "AzurePack_1"; CloudEndpoint = "https://waptenantportal1:40005"},`
    @{CloudName = "AzurePack_2"; CloudEndpoint = "https://waptenantportal2:40005" } `
    -AzureStackCloudName "AzureStack" }

 ```
> [!NOTE]
> V aktuální sestavení se vyskytl problém kde po skončení AzurePackConnector přidat skript, zůstane ve smyčce dotazování pro po delší dobu (několik minut) až do ukončení. Po zobrazení zprávy **podrobné: krok 'konfigurace Azure Pack konektor, stav: 'Úspěch'**, můžete zastavit nebo počkejte, dokud zastaví samostatně. Rozdíl ho nebude Ujistěte se, protože konfigurace má již bylo úspěšné.

2. Poznamenejte si výstup souborů, které jsou vytvářeny pomocí tohoto skriptu, jeden pro každé prostředí systému Windows Azure Pack, který jste zadali. Soubory jsou umístěny na: \\\su1fileserver\SU1_Infrastructure_1\AzurePackConnectorOutput. Tyto soubory obsahují informace, které je potřeba ke konfiguraci prostředí Windows Azure Pack cíl. Tento soubor můžete předat jako parametr skript později v těchto pokynech. Tento soubor obsahuje následující informace:

    * **AzurePackConnectorEndpoint**: obsahuje koncový bod pro službu konektoru služby Windows Azure Pack.
    * **AuthenticationIdentityProviderPartner**: obsahuje následující dvojice hodnot:
        * Podpisový certifikát, který rozhraní API klienta služby Windows Azure Pack musí důvěřovat přijímat volání z portálu rozšíření Azure zásobníku ověřovací Token.

        * "Sféry" přidružené podpisový certifikát. For example: https://adfs.local.azurestack.global.external/adfs/c1d72562-534e-4aa5-92aa-d65df289a107/.

3.  Přejděte do složky, která obsahuje výstupní soubory (\\su1fileserver\SU1_Infrastructure_1\AzurePackConnectorOutput) a zkopírujte soubory do místního počítače. Soubory bude vypadat podobně jako tento: AzurePack-06-27-15-50.txt.

4.  Otestujte konfiguraci.

    a. Otevřete prohlížeč a přihlaste se k portálu pro uživatele Azure zásobníku (https://portal.local.azurestack.external/).
    
    b. Po přihlášení jako klient a zatížení portálu se zobrazí chyby nebude moci načíst předplatná nebo rozšíření z cloudu Azure Pack. Klikněte na tlačítko **OK** zavřít tyto zprávy. (Tyto chybové zprávy zmizí po dokončení konfigurace sady Windows Azure.)

    c. Upozornění **cloudu** rozevíracím seznamu v levém horním rohu portálu.

    ![Selektor cloudu v uživatelském rozhraní Azure zásobníku](media/azure-stack-manage-wap/image3.png)

## <a name="configure-windows-azure-pack"></a>Konfigurace systému Windows Azure Pack
Tento konektor preview jenom pro verzi sady Windows Azure vyžaduje ruční konfigurace.

>[!IMPORTANT]
Pro tuto verzi preview pomocí konektoru služby Windows Azure Pack pouze v testovacích prostředích a není v nasazení v produkčním prostředí.

1.  Instalace souborů MSI konektor portálu virtuálního počítače klienta Windows Azure Pack a nainstalujte certifikáty. (Pokud máte více virtuálních počítačů portálu klienta, musíte provést tento krok na každém virtuálním počítači.)

    a. V Průzkumníku souborů, zkopírujte **WAPConnector** složky (co jste předtím stáhli) do složky s názvem **c:\temp** ve virtuálním počítači portálu klienta.

    b. Otevřete konzolu nebo RDP připojení k virtuálnímu počítači portálu klienta.

    c. Přejděte do adresáře **c:\temp\wapconnector\setup\scripts**a spusťte **instalace Connector.ps1** skript, který nainstaluje tři soubory Instalační služby MSI. Jsou vyžadovány žádné parametry.

     ```powershell
    cd C:\temp\wapconnector\setup\scripts\

    .\Install-Connector.ps1
    ```
     d. Přejděte do adresáře **c:\inetpub** a ověřte, zda jsou nainstalovány tři nové lokality:

       * MgmtSvc-Connector

       * MgmtSvc-ConnectorExtension

       * MgmtSvc-ConnectorController

    e. Ze stejné **c:\temp\wapconnector\setup\scripts** složky, spusťte **konfigurace Certificates.ps1** skriptu instalace certifikátů. Ve výchozím nastavení použije stejný certifikát, který je k dispozici pro web portál klienta v systému Windows Azure Pack. Ujistěte se, že toto je platný certifikát (důvěřují virtuální počítač Azure zásobníku AzS-WASP01 a klientský počítač, který má přístup k portálu Azure zásobníku). Komunikace, jinak nebude fungovat. (Alternativně můžete explicitně předat kryptografický otisk certifikátu jako parametr pomocí parametru - kryptografický otisk.)

     ```powershell
        cd C:\temp\wapconnector\setup\scripts\

        .\Configure-Certificates.ps1
    ```

    f. Chcete-li dokončit konfiguraci tyto tři služby, spusťte **konfigurace WapConnector.ps1** skript pro aktualizaci parametry v souboru Web.config.

    |  Parametr | Popis | Příklad: |   
    | -------- | ------------- | ------- |  
    | TenantPortalFQDN | Portál klienta Windows Azure Pack plně kvalifikovaný název domény. | tenant.contoso.com | 
    | TenantAPIFQDN | Systému Windows Azure Pack klienta API plně kvalifikovaný název domény. | tenantapi.contoso.com  |
    | AzureStackPortalFQDN | Zásobník Azure portál user portal plně kvalifikovaný název domény. | portal.local.azurestack.external |
    | | |
    
     ```powershell
    .\Configure-WapConnector.ps1 -TenantPortalFQDN "tenant.contoso.com" `
         -TenantAPIFQDN "tenantapi.contoso.com" `
         -AzureStackPortalFQDN "portal.local.azurestack.external"
    ```
    g. Pokud máte více virtuálních počítačů portálu klienta, opakujte krok 1 pro každý z těchto virtuálních počítačů.

2. Instalace nového MSI rozhraní API klienta na každém virtuálním počítači rozhraní API klienta služby Windows Azure Pack.

    a. Pokud nástroj pro vyrovnávání zatížení se používá, můžete označit virtuální počítač jako offline.

    b. V Průzkumníku souborů, zkopírujte **WAPConnector** složky do složky s názvem **c:\temp** na každém počítači, rozhraní API pro klienty.

    c. Zkopírujte soubor AzurePackConnectorOutput.txt, který jste uložili dřív, k **c:\temp\WAPConnector**.

    d. Otevřete konzolu nebo RDP připojení k virtuálnímu počítači rozhraní API klienta zkopírovali soubory.
    
    e. Přejděte do adresáře **c:\temp\wapconnector\setup\scripts**a spusťte **aktualizace TenantAPI.ps1**. Tato nová verze rozhraní API pro klienty WAP obsahuje změnu aktivujte vztah důvěryhodnosti jenom s aktuální služby tokenů zabezpečení, ale taky s instancí služby AD FS v zásobníku Azure.

     ```powershell
    cd C:\temp\wapconnector\setup\packages\

    .\Update-TenantAPI.ps1
    ```

    f.  Opakujte krok 2 na žádným jiným virtuálním počítačem spuštěno rozhraní API klienta.
3. Z **pouze jeden** virtuálních počítačů rozhraní API klienta, spusťte skript TrustAzureStack.ps1 konfigurace, který chcete přidat vztah důvěryhodnosti mezi rozhraní API pro klienty a instance služby AD FS v zásobníku Azure. Musíte použít účet správce systému přístup k databázi Microsoft.MgmtSvc.Store. Tento skript má následující parametry:

    | Parametr | Popis | Příklad: |
    | --------- | ------------| ------- |
   | SqlServer | Název systému SQL Server, který obsahuje databázi Microsoft.MgmtSvc.Store. Tento parametr je povinný. | SQLServer | 
   | datový soubor | Výstupního souboru, který byl vygenerován při konfiguraci režimu více cloudu Azure zásobníku dříve. Tento parametr je povinný. | AzurePack-06-27-15-50.txt | 
   | PromptForSqlCredential | Označuje, že skript by se zobrazit výzva interaktivně pro ověřování SQL pověření použít při připojení k instanci systému SQL Server. Zadané přihlašovací údaje musí mít dostatečná oprávnění k odinstalaci databází, schémat a odstranit přihlášení uživatele. Pokud je zadaný none, skript předpokládá, že tento aktuální uživatelský kontext má přístup. | Je potřeba žádná hodnota. |
   |  |  |

    Pokud si nejste jisti SQL Server, aby používal, můžete zjistit. Připojte se k počítači rozhraní API pro klienty, použijte tento příkaz Unprotect-MgmtSvc zrušit ochranu souboru Web.config rozhraní API klienta, a vyhledejte název serveru v připojovacím řetězci. Mějte na paměti ke spuštění chránit MgmtSvc znovu k ochraně souboru Web.config rozhraní API klienta.

  ```powershell
  cd C:\temp\wapconnector\setup\scripts\

 .\Configure-TrustAzureStack.ps1 -SqlServer "SQLServer" `
       -DataFile "C:\temp\wapconnector\AzurePackConnectorOutput.txt"
  ```

## <a name="example"></a>Příklad:
Následující příklad ukazuje dokončení nasazení konektoru služby Windows Azure Pack v konfiguraci s jedním uzlem zásobník Azure a dvě instalace systému Windows Azure Pack Express. (Každý Expresní instalace je v jednom počítači, s názvy příklad *wapcomputer1* a*wapcomputer2*.)

```powershell
# Run the following script on the Azure Stack host
$cred = New-Object System.Management.Automation.PSCredential("cloudadmin@azurestack.local",`
     (ConvertTo-SecureString -String "p@ssw0rd" -AsPlainText -Force))
$session = New-PSSession -ComputerName 'azs-ercs01' -Credential $cred `
     -ConfigurationName PrivilegedEndpoint -Authentication Credssp
 
# Enable Multicloud
invoke-command -Session $session -ScriptBlock { Add-AzurePackConnector -AzurePackClouds `
     @{CloudName = "AzurePack_1"; CloudEndpoint = "https://wapcomputer1.contoso.com:40005"},`
     @{CloudName = "AzurePack_2"; CloudEndpoint = "https://wapcomputer2.contoso.com:40005"}`
     -AzureStackCloudName "AzureStack" }  

```
Stáhněte si soubor .exe z [Microsoft Download Center](https://aka.ms/wapconnectorazurestackdlc), rozbalte ho a zkopírujte do složky WAPConnector pro **c:\temp** složky v počítači Windows Azure Pack. Zkopírujte soubory, které byly generovány jako výstup v předchozí skript (umístěné v \\\su1fileserver\SU1_Infrastructure_1\AzurePackConnectorOutput) k **c:\temp\WAPConnector** složky. (Soubory budou vypadá podobně jako tento: AzurePack-06-27-15-50.txt.) Potom spusťte následující skript, jednou za instance sady Windows Azure:

 ```powershell
# Install Connector components
cd C:\temp\WAPConnector\Setup\Scripts
.\Install-Connector.ps1

# Configure Certificates for the new Connector services
.\Configure-Certificates.ps1

# Configure the Connector services
.\Configure-WapConnector.ps1 -TenantPortalFQDN "wapcomputer1.contoso.com" `
     -TenantAPIFQDN "wapcomputer1.contoso.com" `
     -AzureStackPortalFQDN "portal.local.azurestack.external"

# Install the updated TenantAPI
.\Update-TenantAPI.ps1

# Establish trust with the Azure Stack AD FS
.\Configure-TrustAzureStack.ps1 -SqlServer "wapcomputer1" `
     -DataFile "C:\temp\wapconnector\AzurePack-06-27-15-50.txt" 

```
## <a name="troubleshooting-tips"></a>Rady pro řešení potíží
1.  Zkontrolujte, zda je síťové připojení mezi Azure zásobníku a sady Windows Azure. Tyto možnosti připojení musí být mezi libovolného počítače klienta, který má přístup k portálu Azure zásobníku a portálu virtuálního počítače klienta Windows Azure Pack, kde běží nové služby konektoru.
2.  Zajistěte, aby všechny zadán správnost plně kvalifikované názvy domén.
3.  Zajistěte, aby certifikáty SSL v nových služeb konektoru jsou důvěryhodné v Azure zásobníku (konkrétně AzS WASP01 virtuálního počítače) a všechny ostatní počítače klienta může používat pro přístup k portálu user portal zásobník Azure.
4. Známé problémy najdete v tématu [řešení potíží s Microsoft Azure zásobníku](azure-stack-troubleshooting.md).


## <a name="next-steps"></a>Další postup
[Používání portálů správce a uživatele v Azure zásobníku](azure-stack-manage-portals.md)
