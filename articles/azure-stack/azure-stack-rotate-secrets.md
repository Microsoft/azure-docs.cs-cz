---
title: Otočit tajné kódy ve službě Azure Stack | Dokumentace Microsoftu
description: Zjistěte, jak otočit vaše tajné kódy ve službě Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/18/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: 27fd02503881ef1f0587ccb0301f8490101d5bcb
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2018
ms.locfileid: "53720641"
---
# <a name="rotate-secrets-in-azure-stack"></a>Otočit tajné kódy ve službě Azure Stack

*Tyto pokyny platí jenom pro Azure Stack integrované systémy verze 1803 a novější. Nepokoušejte tajných kódů otočení na verze pre-1802 Azure Stack*

Azure Stack používá různé tajných kódů k údržbě zabezpečenou komunikaci mezi prostředky infrastruktury Azure stacku a služeb.

- **Interní tajných kódů**  
Všechny certifikáty, hesla, zabezpečené řetězce a klíčů používaných infrastruktury služby Azure Stack bez zásahu operátor Azure stacku. 

- **Externích tajných klíčů**  
Certifikáty služby infrastruktury externích služeb, které jsou k dispozici v operátor Azure stacku. To zahrnuje certifikáty pro následující služby: 
  - Portál správce  
  - Veřejný portál  
  - Správce Azure Resource Manageru  
  - Globální Azure Resource Manageru  
  - Správce služby Keyvault  
  - KeyVault  
  - Hostitel Správce rozšíření  
  - Služby ACS (včetně objektů blob, table a queue storage)  
  - SLUŽBY AD FS *  
  - Graf *  

\* Platí pouze pokud je zprostředkovatel identity prostředí Active Directory Federated Services (AD FS).

> [!Note]  
> Všechny ostatní zabezpečení klíčů a řetězce, včetně BMC a přepněte hesla, správce ručně aktualizovat hesla k účtům uživatelů a správců. 

> [!Note]  
> Od verze Azure Stack 1811 tajných kódů otočení byl oddělen pro interní i externí certifikáty. 

Aby bylo možné udržovat tak integritu infrastruktury Azure stacku, operátory nemusí pravidelně obměňovat svoji infrastrukturu tajných kódů v frekvence, které jsou v souladu s požadavky na zabezpečení organizace.

### <a name="rotating-secrets-with-external-certificates-from-a-new-certificate-authority"></a>Otáčení tajných kódů pomocí externí certifikáty od certifikační autority nový

Azure Stack podporuje v následujících kontextů tajných kódů otočení s externí certifikáty od nového certifikátu autority (CA):

|Nainstalovaný certifikát certifikační Autority|Otočit na certifikační Autority.|Podporováno|Podporované verze Azure Stack|
|-----|-----|-----|-----|
|Z podepsaného svým držitelem|Do firemní sítě|Nepodporuje se||
|Z podepsaného svým držitelem|Pro podepsaný svým držitelem|Nepodporuje se||
|Z podepsaného svým držitelem|Na veřejnou<sup>*</sup>|Podporováno|1803 & později|
|Z organizace|Do firemní sítě|Podporované tak dlouho, dokud zákazníci využívat stejné podnikové certifikační Autority, jak použít při nasazení|1803 & později|
|Z organizace|Pro podepsaný svým držitelem|Nepodporuje se||
|Z organizace|Na veřejnou<sup>*</sup>|Podporováno|1803 & později|
|Z veřejné<sup>*</sup>|Do firemní sítě|Nepodporuje se|1803 & později|
|Z veřejné<sup>*</sup>|Pro podepsaný svým držitelem|Nepodporuje se||
|Z veřejné<sup>*</sup>|Na veřejnou<sup>*</sup>|Podporováno|1803 & později|

<sup>*</sup>Označuje, že veřejné certifikační autority jsou ty, které jsou součástí programu Windows důvěryhodné kořenové. Úplný seznam najdete v článku [Microsoft Trusted Root Certificate Program: Účastníci (od 27. června 2017)](https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca).

## <a name="alert-remediation"></a>Oprava výstrahy

Po do 30 dnů od vypršení platnosti tajných kódů tyto výstrahy jsou generovány v portálu správce: 

- Vypršení platnosti hesla účtu čekající služby 
- Vypršení platnosti vnitřní certifikát čekající na vyřízení 
- Vypršení platnosti externí certifikát čekající na vyřízení 

Spuštění tajných kódů otočení pomocí následujícího postupu se opravit tyto výstrahy.

> [!Note]  
> Prostředí Azure Stack na 1811 předběžné verze se může zobrazit výstrahy pro čekající vnitřní certifikát nebo vypršení platnosti tajných kódů. Tyto výstrahy jsou nesprávné a je třeba ignorovat bez spuštění interní otočení tajného kódu. Upozornění nepřesné vypršení platnosti interní tajných kódů jsou známý problém, který je přeložen v 1811 – interní, tajných kódů nevyprší, dokud prostředí byl aktivní na dva roky. 

## <a name="pre-steps-for-secret-rotation"></a>Kroky prováděné před zpracováním pro rotaci tajného kódu

   > [!IMPORTANT]  
   > Pokud má už jsou hotové tajných kódů otočení ve vašem prostředí Azure Stack musíte aktualizovat systém na verzi 1811 nebo novější, ještě před spuštěním tajných kódů otočení znovu. Tajný otočení musí provést prostřednictvím [privilegovaných koncový bod](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint) a vyžadují přihlašovací údaje Azure Stack operátor. Pokud vaše prostředí Azure Stack operátory nejste jisti, zda byl spuštěn tajných kódů otočení ve vašem prostředí, aktualizujte na 1811 před provedením tajných kódů otočení znovu.

1. Důrazně doporučujeme aktualizovat na verzi 1811 vaší instance služby Azure Stack.

    > [!Note] 
    > Pro verze pre-1811 není potřeba otočit tajných kódů přidat certifikáty rozšíření hostitele. Postupujte podle pokynů v článku [Příprava hostitele rozšíření pro službu Azure Stack](azure-stack-extension-host-prepare.md) přidání rozšíření hostitele certifikátů.

2.  Operátory všimnout výstrahy otevírají a zavírají automaticky během otáčení tajných kódů služby Azure Stack.  Toto chování je očekávané a upozornění můžete ignorovat.  Operátory lze ověřit platnost těchto upozornění spuštěním **testovací AzureStack**.  Pro operátory pomocí nástroje SCOM k monitorování systémy Azure Stack, uvedení v režimu údržby systému zabrání dosažení jejich systémům ITSM tato upozornění ale bude nadále výstrahu, pokud je systém Azure Stack se nestane nedostupnou. 
3. Upozorněte uživatele na jakékoli operace údržby. Naplánujte běžné časová období údržby, co je to možné, během pracovní doby. Operace údržby může ovlivnit uživatelské úlohy a operací na portálu.

    > [!Note]  
    > Další kroky se projeví pouze v případě otáčení externích tajných klíčů Azure Stack.

4. Spustit **[testovací AzureStack](https://docs.microsoft.com/azure/azure-stack/azure-stack-diagnostic-test)** a potvrďte všechny výstupy testů jsou v dobrém stavu před otáčení tajných kódů.
5. Přidejte novou sadu nahrazení externí certifikáty. Nová sada odpovídá certifikát požadavky uvedené v [požadavky na certifikát Azure Stack PKI](https://docs.microsoft.com/azure/azure-stack/azure-stack-pki-certs). Můžete generovat žádost o podepsání certifikátu (CSR) pro zakoupení nebo vytváří nové certifikáty pomocí postupu uvedeného v [vygenerovat certifikáty PKI](https://docs.microsoft.com/azure/azure-stack/azure-stack-get-pki-certs) a je připravili k použití ve vašem prostředí Azure Stack pomocí kroků v [ Přidejte certifikáty infrastruktury veřejných KLÍČŮ služby Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-prepare-pki-certs). Ověřte certifikáty připravíte podle kroků uvedených v [ověřit certifikáty PKI](https://docs.microsoft.com/azure/azure-stack/azure-stack-validate-pki-certs). 
6.  Store zálohováním do certifikátů používaných pro rotaci do zabezpečeného umístění. Pokud vaše otočení spustí a pak se nezdaří, nahraďte certifikáty ve sdílené složce záložní kopie předtím, než znovu spustíte otočení. Mějte na paměti, uchovávání záložních kopií v zabezpečené umístění zálohy.
7.  Vytvoření sdílené složky, ke kterým přistupujete z ERCS virtuálních počítačů. Sdílené složky musí být čtení a zápisu pro **CloudAdmin** identity.
8.  Otevřete konzoly ISE Powershellu na počítači, kde máte přístup ke sdílení souborů. Přejděte na vaši sdílenou složku. 
9.  Spustit **[CertDirectoryMaker.ps1](https://www.aka.ms/azssecretrotationhelper)** k vytvoření požadované adresáře pro externí certifikáty.

## <a name="rotating-external-secrets"></a>Otáčení externích tajných klíčů

Obměna externích tajných klíčů:

1. V rámci nově vytvořené **/certifikáty** adresář vytvořený v kroky prováděné před zpracováním, umístěte novou sadu nahrazení externí certifikáty do struktury adresářů podle formátu uvedených v části povinné certifikáty nástroje [požadavky na certifikát Azure Stack PKI](https://docs.microsoft.com/azure/azure-stack/azure-stack-pki-certs#mandatory-certificates).
2. Vytvořte relaci Powershellu s [privilegovaných koncový bod](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint) pomocí **CloudAdmin** účtu a uložit tyto relace jako proměnnou. Tato proměnná bude používat jako parametr v dalším kroku.

    > [!IMPORTANT]  
    > Nezadávejte relace, relace uložit jako proměnnou.
    
3. Spustit  **[invoke-command](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/invoke-command?view=powershell-5.1)**. Předání jako proměnnou relace Powershellu privilegovaných koncový bod **relace** parametru. 
4. Spustit **Start SecretRotation** s následujícími parametry:
    - **PfxFilesPath**  
    Zadejte síťovou cestu k adresáři certifikáty vytvořili dříve.  
    - **PathAccessCredential**  
    Objekt PSCredential pro přihlašovací údaje ke sdílené složce. 
    - **CertificatePassword**  
    Zabezpečený řetězec heslo použité pro všechny vytvořené soubory certifikátů pfx.
4. Počkejte, dokud otočit tajných klíčů. Externí tajných kódů otočení obvykle trvá přibližně jednu hodinu. Po úspěšném dokončení otočení tajného kódu se zobrazí konzola **celkový stav akce: Úspěch**. 
    > [!Note]  
    > Pokud selže tajných kódů otočení, postupujte podle pokynů v chybové zprávě a znovu spusťte **start secretrotation** s **– opětovné spuštění** parametr. 

    ```PowerShell  
    Start-SecretRotation -Rerun
    ```
    Obraťte se na podporu Pokud dochází k opakované otočení tajných kódů chyb.
5. Po úspěšném dokončení tajných kódů otáčení odebrat certifikáty ze sdílené složky vytvořené v kroku předběžné a uložit je do jejich zabezpečené umístění zálohy. 

## <a name="walkthrough-of-secret-rotation"></a>Návod k otočení tajného kódu

Následující příklad PowerShell ukazuje, rutiny a parametry k otočení tajných klíčů.

```powershell
#Create a PEP Session
winrm s winrm/config/client '@{TrustedHosts= "<IPofERCSMachine>"}'
$PEPCreds = Get-Credential 
$PEPsession = New-PSSession -computername <IPofERCSMachine> -Credential $PEPCreds -ConfigurationName PrivilegedEndpoint 

#Run Secret Rotation
$CertPassword = ConvertTo-SecureString "Certpasswordhere" -Force
$CertShareCred = Get-Credential 
$CertSharePath = "<NetworkPathofCertShare>"
Invoke-Command -session $PEPsession -ScriptBlock { 
Start-SecretRotation -PfxFilesPath $using:CertSharePath -PathAccessCredential $using:CertShareCred -CertificatePassword $using:CertPassword }
Remove-PSSession -Session $PEPSession
```

## <a name="rotating-only-internal-secrets"></a>Otáčení pouze interní tajných kódů

> [!Note]  
> Interní otočení tajný kód lze provádět pouze pokud máte podezření, že interní tajného kódu došlo k napadení škodlivým entity, nebo Pokud dostanete výstrahu (při sestavování 1811 nebo novější) označující, že interní certifikáty se blíží vypršení platnosti. Prostředí Azure Stack na 1811 předběžné verze se může zobrazit výstrahy pro čekající vnitřní certifikát nebo vypršení platnosti tajných kódů. Tyto výstrahy jsou nesprávné a je třeba ignorovat bez spuštění interní otočení tajného kódu. Upozornění nepřesné vypršení platnosti interní tajných kódů jsou známý problém, který je přeložen v 1811 – interní, tajných kódů nevyprší, dokud prostředí byl aktivní na dva roky.

Obměna pouze interní tajných kódů služby Azure Stack:

1. Vytvořte relaci Powershellu s [privilegovaných koncový bod](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint).
2. V relaci privilegovaných koncový bod spustit **Start SecretRotation-interní**.

    > [!Note]  
    > Prostředí Azure Stack na verze pre-1811 nevyžadují **– interní** příznak. **Start-SecretRotation** otočí pouze interní tajných kódů.

3. Počkejte, dokud otočit tajných klíčů.  
Po úspěšném dokončení otočení tajného kódu se zobrazí konzola **celkový stav akce: Úspěch**. 
    > [!Note]  
    > Pokud selže tajných kódů otočení, postupujte podle pokynů v chybové zprávě a znovu spusťte **start secretrotation** s **– interní** a **– spusťte znovu** parametry.  

    ```PowerShell  
    Start-SecretRotation -Internal -Rerun
    ```
    Obraťte se na podporu Pokud dochází k opakované otočení tajných kódů chyb.

## <a name="start-secretrotation-reference"></a>Odkaz na začátku SecretRotation

Otočí tajných kódů systému Azure Stack. Provést pouze proti koncovému bodu privilegovaných pro Azure Stack.

### <a name="syntax"></a>Syntaxe

#### <a name="for-external-secret-rotation"></a>Pro externí otočení tajného kódu

```Powershell  
Start-SecretRotation [-PfxFilesPath <string>] [-PathAccessCredential] <PSCredential> [-CertificatePassword <SecureString>]  
```

#### <a name="for-internal-secret-rotation"></a>Pro interní otočení tajného kódu 

```Powershell  
Start-SecretRotation [-Internal]  
```

#### <a name="for-external-secret-rotation-rerun"></a>Pro externí tajných kódů otočení znovu spustit 

```Powershell  
Start-SecretRotation [-Rerun]
```

#### <a name="for-internal-secret-rotation-rerun"></a>Pro interní tajných kódů otočení znovu spustit 

```Powershell  
Start-SecretRotation [-Rerun] [-Internal]
```
### <a name="description"></a>Popis

**Start SecretRotation** rutiny otočí infrastruktury tajemství systémech pro Azure Stack. Ve výchozím nastavení se otočí jenom certifikáty všechny koncové body externí síťové infrastruktury. Použít s interní příznak - interní infrastruktury tajných kódů otočí. Při obměně koncových bodů externí síťové infrastruktury, **Start SecretRotation** by měl být spuštěn s **Invoke-Command** blok skriptu s prostředím Azure Stack na privilegovaný koncový bod relace předaného jako parametr relace.
 
### <a name="parameters"></a>Parametry

| Parametr | Typ | Požaduje se | Umístění | Výchozí | Popis |
| -- | -- | -- | -- | -- | -- |
| PfxFilesPath | Řetězec  | False  | s názvem  | Žádný  | Cesta sdílení souborů na **\Certificates** síťové adresáře, který obsahuje všechny externí certifikáty koncový bod. Povinné pouze v případě otáčení externích tajných klíčů nebo všech tajných kódů. Adresář end musí být **\Certificates**. |
| CertificatePassword | SecureString | False  | s názvem  | Žádný  | Heslo pro všechny certifikáty v PfXFilesPath – k dispozici. Požadovaná hodnota, pokud PfxFilesPath víceklientského interních i externích tajných kódů se otočí. |
| Interní | Řetězec | False | s názvem | Žádný | Interní příznak musí použít, kdykoli operátory Azure stacku chce otočit interní infrastruktury tajných kódů. |
| PathAccessCredential | PSCredential | False  | s názvem  | Žádný  | Přihlašovací údaje prostředí PowerShell pro sdílení souborů systému **\Certificates** síťové adresáře, který obsahuje všechny externí certifikáty koncový bod. Povinné pouze v případě otáčení externích tajných klíčů nebo všech tajných kódů.  |
| Spustit znovu | SwitchParameter | False  | s názvem  | Žádný  | Opětovné spuštění se musí použít kdykoli otočení tajného kódu je opakování po neúspěšném pokusu. |

### <a name="examples"></a>Příklady
 
#### <a name="rotate-only-internal-infrastructure-secrets"></a>Otočit pouze interní infrastruktury tajných kódů

To je potřeba použít prostřednictvím služby Azure Stack [prostředí na privilegovaný koncový bod](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint).

```powershell  
PS C:\> Start-SecretRotation  -Internal
```

Tento příkaz otočí všechny tajné klíče infrastruktury vystavený k interní síti Azure Stack. 

#### <a name="rotate-only-external-infrastructure-secrets"></a>Otočit pouze tajných kódů externí infrastruktury  

```powershell  
PS C:\> Invoke-Command -session $PEPSession -ScriptBlock {  

Start-SecretRotation -PfxFilesPath $using:CertSharePath -PathAccessCredential $using:CertShareCred -CertificatePassword $using:CertPassword }  

Remove-PSSession -Session $PEPSession
```

Tento příkaz otočí certifikáty protokolu TLS pro koncové body služby Azure Stack externí síťové infrastruktury.   

**Otočit infrastruktury interních a externích tajných klíčů**
  
```powershell
PS C:\> Invoke-Command -session $PEPSession -ScriptBlock { 
Start-SecretRotation -PfxFilesPath $using:CertSharePath -PathAccessCredential $using:CertShareCred -CertificatePassword $using:CertPassword } 
Remove-PSSession -Session $PEPSession
```

Tento příkaz otočí všechny tajné klíče infrastruktury vystavený k interní síti Azure Stack, stejně jako certifikáty protokolu TLS pro koncové body služby Azure Stack externí síťové infrastruktury. Start-SecretRotation otočí všech tajných kódů generovaných zásobníku a vzhledem k tomu, že jsou k dispozici certifikáty, bude také otočen certifikáty externí koncový bod.  


## <a name="update-the-baseboard-management-controller-bmc-credential"></a>Aktualizovat pověření pro řadič pro správu základní desky

Řadič pro správu základní desky (BMC) monitoruje fyzickému stavu vašich serverů. Požadavky a pokyny, aktualizuje se uživatelské jméno a heslo pro správu základní desky se liší v závislosti na dodavatele hardwaru, výrobce OEM (OEM). Měli byste aktualizovat hesla pro Azure Stack součásti v pravidelných intervalech.

1. Aktualizujte správu základní desky na fyzických serverech služby Azure Stack podle pokynů vaší výrobce OEM. Název uživatelského účtu a heslo pro každou BMC ve vašem prostředí musí být stejné.
2. Otevřete koncový bod privilegovaných ve službě Azure Stack relacích. Pokyny najdete v tématu [pomocí privilegovaných koncového bodu ve službě Azure Stack](azure-stack-privileged-endpoint.md).
3. Po Powershellu řádek byl změněn na **[adresa IP nebo virtuální počítač ERCS name]: PS >** nebo **[azs-ercs01]: PS >**, v závislosti na prostředí, spusťte `Set-BmcCredemtial` spuštěním `invoke-command`. Vaše proměnná privilegovaných koncový bod relace předáte jako parametr. Příklad:

    ```powershell
    # Interactive Version
    $PEip = "<Privileged Endpoint IP or Name>" # You can also use the machine name instead of IP here.
    $PECred = Get-Credential "<Domain>\CloudAdmin" -Message "PE Credentials" 
    $NewBMCpwd = Read-Host -Prompt "Enter New BMC password" -AsSecureString
    $NewBMCuser = Read-Host -Prompt "Enter New BMC user name"

    $PEPSession = New-PSSession -ComputerName $PEip -Credential $PECred -ConfigurationName "PrivilegedEndpoint" 

    Invoke-Command -Session $PEPSession -ScriptBlock {
        # Parameter BMCPassword is mandatory, while the BMCUser parameter is optional.
        Set-Bmccredential -bmcpassword $using:NewBMCpwd -bmcuser $using:NewBMCuser
    }
    Remove-PSSession -Session $PEPSession
    ```
    
    Statické verze prostředí PowerShell můžete použít také s hesla jako řádky kódu:
    
    ```powershell
    # Static Version
    $PEip = "<Privileged Endpoint IP or Name>" # You can also use the machine name instead of IP here.
    $PEUser = "<Privileged Endpoint user for example Domain\CloudAdmin>"
    $PEpwd = ConvertTo-SecureString "<Privileged Endpoint Password>" -Force
    $PECred = New-Object System.Management.Automation.PSCredential ($PEUser, $PEpwd) 
    $NewBMCpwd = ConvertTo-SecureString "<New BMC Password>" -Force
    $NewBMCuser = "<New BMC User name>" 

    $PEPSession = New-PSSession -ComputerName $PEip -Credential $PECred -ConfigurationName "PrivilegedEndpoint" 

    Invoke-Command -Session $PEPSession -ScriptBlock {
        Set-Bmccredential -bmcpassword $using:NewBMCpwd -bmcuser $using:NewBMCuser
    }
    Remove-PSSession -Session $PEPSession
    ```

## <a name="next-steps"></a>Další postup

[Další informace o zabezpečení Azure stacku](azure-stack-security-foundations.md)
