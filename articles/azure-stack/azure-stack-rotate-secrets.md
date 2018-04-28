---
title: Otočit tajných klíčů v zásobníku Azure | Microsoft Docs
description: Naučte se otočit tajných klíčů v zásobníku Azure.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 49071044-6767-4041-9EDD-6132295FA551
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: a158da6fb397b864a439e067ca99d79814e2b8d2
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
---
# <a name="rotate-secrets-in-azure-stack"></a>Otočit tajné klíče v Azure zásobníku

*Tyto pokyny platí pouze pro Azure zásobníku integrované systémy verze 1803 a později. Nepokoušejte se tajný otočení pre-1802 Azure zásobníku verze*

Azure zásobníku využívá různé tajné klíče udržovat zabezpečenou komunikaci mezi služby a prostředky infrastruktury Azure zásobníku.

- **Interní tajné klíče**  
Všechny certifikáty, hesla, zabezpečení řetězce a klíče používané infrastruktury Azure zásobníku bez zásahu operátoru zásobník Azure. 

- **Externí tajné klíče**  
Certifikáty služby infrastruktury pro externího služby, které jsou k dispozici operátorem zásobník Azure. To zahrnuje certifikáty pro následující služby: 
    - Portál správce 
    - Veřejné portálu 
    - Správce Azure Resource Manager 
    - Globální Azure Resource Manager 
    - Správce Keyvault 
    - KeyVault 
    - Služba ACS (včetně objektů blob, table a queue storage) 
    - ADFS<sup>*</sup>
    - Graf<sup>*</sup>

    > <sup>*</sup> Platí jenom Pokud zprostředkovatele identity v prostředí Active Directory Federated Services (AD FS).

> [!NOTE]
> Jiných zabezpečení klíčů a řetězce, včetně BMC a přepínače hesla, správce pořád spustit ručně aktualizovat hesla účtu uživatele a správce. 

Aby udržení integrity infrastruktury Azure zásobníku, třeba operátory možnost pravidelně otočit jejich infrastruktury tajných klíčů v frekvencí, které jsou v souladu s požadavky na zabezpečení organizace.

## <a name="alert-remediation"></a>Výstrahy nápravy

Po tajných klíčů do 30 dní od vypršení platnosti se tyto výstrahy jsou generovány na portálu správce: 

- Vypršení platnosti hesla účtu čekající služby 
- Vnitřní certifikát čekající na vyřízení vypršení platnosti 
- Vypršení platnosti externí certifikát čekající na vyřízení 

Spuštění tajný otočení pomocí následujícího postupu napraví tyto výstrahy.

## <a name="pre-steps-for-secret-rotation"></a>Předběžné kroky pro tajný otočení

1.  Upozorněte uživatele na všechny operace údržby. Naplánujte běžné časové intervaly, co nejvíce, během pracovní doby. Operace údržby může mít vliv na portálu operace a úlohy uživatele.

    > [!note]  
    > Další kroky se projeví pouze v případě otáčení zásobník Azure externí tajných klíčů.

2.  Připravte novou sadu nahrazení externí certifikáty. Nové sady odpovídá certifikátu specifikace uvedené v [požadavky na certifikát PKI zásobník Azure](https://docs.microsoft.com/azure/azure-stack/azure-stack-pki-certs).
3.  Úložiště zálohování na certifikáty používané k otočení do zabezpečeného umístění. Pokud vaše otočení běží a pak selže, nahraďte předtím, než můžete znovu spustit, je oběh certifikáty ve sdílené složce záložní kopie. Všimněte si, udržování záložní kopie v zabezpečené umístění zálohy.
3.  Vytvoření sdílení souborů, které je přístupné z ERCS virtuálních počítačů. Sdílené složky musí být možné číst a zapisovatelný **CloudAdmin** identity.
4.  Otevřete konzolu prostředí PowerShell ISE z počítače, kde má přístup ke sdílení souborů. Přejděte do vaší sdílení souborů. 
5.  Spustit **[CertDirectoryMaker.ps1](http://www.aka.ms/azssecretrotationhelper)** vytvořit požadované adresáře pro externí certifikáty.

## <a name="rotating-external-and-internal-secrets"></a>Otáčení externí i interní tajné klíče

Otočení externí i interní tajných klíčů:

1. V nově vytvořený **/certifikáty** adresář vytvořený v předběžné krocích umístit novou sadu externí certifikáty nahrazení struktury adresářů podle formátu uvedeném v části povinné certifikáty z [požadavky na certifikát PKI zásobník Azure](https://docs.microsoft.com/azure/azure-stack/azure-stack-pki-certs#mandatory-certificates).
2. Vytvořit relaci prostředí PowerShell s [privilegované koncový bod](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint) pomocí **CloudAdmin** účtu a uložení relací jako proměnné. Jako parametr v dalším kroku bude pomocí této proměnné.

    > [!IMPORTANT]  
    > Nezadávejte relace, uložit jako proměnnou relace.
    
3. Spustit  **[invoke-command](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/invoke-command?view=powershell-5.1)**. Předat jako proměnnou relace prostředí PowerShell privilegované koncový bod **relace** parametr. 
4. Spustit **Start-SecretRotation** s následujícími parametry:
    - **PfxFilesPath**  
    Zadejte síťovou cestu k adresáři certifikáty vytvořili dříve.  
    - **PathAccessCredential**  
    Objekt PSCredential pro přihlašovací údaje ke sdílené složce. 
    - **CertificatePassword**  
    Zabezpečený řetězec heslo použité pro všechny soubory certifikátů pfx vytvořili.
4. Počkejte, než otočit tajných klíčů.  
Pokud tajný otočení úspěšně dokončí, zobrazí se konzoly **celkový stav akce: Úspěch**. 
5. Po úspěšném dokončení tajný otočení odebrat certifikáty ze sdílené složky vytvořené v kroku předběžné a uložit je do jejich zabezpečené umístění zálohy. 

## <a name="walkthrough-of-secret-rotation"></a>Návod tajný otočení

Následující příklad PowerShell ukazuje, rutiny a parametry pro spuštění kvůli otočit tajných klíčů.

```powershell
#Create a PEP Session
winrm s winrm/config/client '@{TrustedHosts= "<IPofERCSMachine>"}'
$PEPCreds = Get-Credential 
$PEPsession = New-PSSession -computername <IPofERCSMachine> -Credential $PEPCreds -ConfigurationName PrivilegedEndpoint 

#Run Secret Rotation
$CertPassword = ConvertTo-SecureString "Certpasswordhere" -AsPlainText -Force
$CertShareCred = Get-Credential 
$CertSharePath = "<NetworkPathofCertShare>"
Invoke-Command -session $PEPsession -ScriptBlock { 
Start-SecretRotation -PfxFilesPath $using:CertSharePath -PathAccessCredential $using:CertShareCred -CertificatePassword $using:CertPassword }
Remove-PSSession -Session $PEPSession
```
## <a name="rotating-only-internal-secrets"></a>Otáčení jenom interní tajné klíče

Otočení jenom interní tajné klíče zásobník Azure:

1. Vytvořte relaci prostředí PowerShell s [privilegované koncový bod](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint).
2. V relaci privilegované koncový bod spustit **Start-SecretRotation** bez argumentů.

## <a name="start-secretrotation-reference"></a>Spuštění SecretRotation odkaz

Otočí tajné klíče zásobníku systému Azure. Spustit pouze na Azure zásobníku privilegované koncový bod.

### <a name="syntax"></a>Syntaxe

Cesta (výchozí)

```powershell
Start-SecretRotation [-PfxFilesPath <string>] [-PathAccessCredential] <PSCredential> [-CertificatePassword <SecureString>]  
```

### <a name="description"></a>Popis

Rutina Start-SecretRotation otočí tajné klíče infrastruktury Azure zásobníku systému. Ve výchozím nastavení ho otočí všech tajných klíčů vystavený interní infrastruktury síti se vstupem uživatele ho také otočí certifikáty všechny koncové body externí síťové infrastruktury. Když otáčení koncové body externí síťové infrastruktury, by měl počáteční SecretRotation proveden prostřednictvím bloku skriptu Invoke-Command s prostředím Azure zásobníku privilegované koncový bod relace předaná jako parametr relace.
 
### <a name="parameters"></a>Parametry

| Parametr | Typ | Požaduje se | Pozice | Výchozí | Popis |
| -- | -- | -- | -- | -- | -- |
| PfxFilesPath | Řetězec  | False  | S názvem  | Žádný  | Cesta sdílení souborů na **\Certificates** adresář obsahující všechny externí sítě certifikáty koncový bod. Je požadován, pouze když otáčení interních a externích tajných klíčů. Musí být konce adresáře **\Certificates**. |
| CertificatePassword | SecureString | False  | S názvem  | Žádný  | Heslo pro všechny certifikáty, které jsou součástí - PfXFilesPath. Vyžaduje hodnotu, pokud PfxFilesPath získáte při otáčejí interních i externích tajných klíčů. |
|

### <a name="examples"></a>Příklady
 
**Otočit pouze tajné klíče interní infrastruktury**

```powershell  
PS C:\> Start-SecretRotation  
```

Tento příkaz otočí všechna tajemství infrastruktury vystavený zásobník Azure interní síti. Spuštění SecretRotation otočí všechny generované zásobníku tajné klíče, ale protože nejsou k dispozici žádné zadaný certifikáty, nebudou otočený o certifikáty externí koncový bod.  

**Otočit tajné klíče interních a externích infrastruktury**
  
```powershell
PS C:\> Invoke-Command -session $PEPSession -ScriptBlock { 
Start-SecretRotation -PfxFilesPath $using:CertSharePath -PathAccessCredential $using:CertShareCred -CertificatePassword $using:CertPassword } 
Remove-PSSession -Session $PEPSession
```

Tento příkaz otočí všech těchto tajných klíčů infrastruktury vystavený zásobník Azure interní síti, jakož i TLS certifikáty pro koncové body Azure zásobníku externí sítě infrastruktury. Spuštění SecretRotation otočí všechny tajné klíče generované zásobníku a vzhledem k tomu, že jsou k dispozici certifikáty, bude také otočen certifikáty externí koncový bod.  


## <a name="update-the-baseboard-management-controller-bmc-password"></a>Aktualizace hesla řadiče (BMC) správu základní desky

Řadiče pro správu základní desky (BMC) monitoruje fyzickému stavu vašich serverů. Specifikace a pokyny, aktualizace hesla řadiče BMC lišit v závislosti na dodavatele hardwaru, výrobce (OEM). Hesla pro součásti zásobníku Azure v pravidelných cadence by měl aktualizovat.

1. Aktualizujte BMC na fyzických serverech zásobník Azure podle pokynů vaší výrobce OEM. Heslo pro každou BMC ve vašem prostředí musí být stejné.
2. Otevřete koncový bod privilegované v relacích zásobník Azure. Pokyny naleznete v tématu [pomocí privilegované koncový bod v zásobníku Azure](azure-stack-privileged-endpoint.md).
3. Po vaše prostředí PowerShell řádku změnil na **[ERCS virtuálního počítače nebo IP adresu name]: PS >** nebo **[azs-ercs01]: PS >**, v závislosti na prostředí, spusťte `Set-BmcPassword` spuštěním `invoke-command`. Vaše proměnná privilegované koncový bod relace předejte jako parametr. Příklad:

    ```powershell
    # Interactive Version
    $PEip = "<Privileged Endpoint IP or Name>" # You can also use the machine name instead of IP here.
    $PECred = Get-Credential "<Domain>\CloudAdmin" -Message "PE Credentials" 
    $NewBMCpwd = Read-Host -Prompt "Enter New BMC password" -AsSecureString 

    $PEPSession = New-PSSession -ComputerName $PEip -Credential $PECred -ConfigurationName "PrivilegedEndpoint" 

    Invoke-Command -Session $PEPSession -ScriptBlock {
        Set-Bmcpassword -bmcpassword $using:NewBMCpwd
    }
    Remove-PSSession -Session $PEPSession
    ```
    
    Můžete také použít statické verze prostředí PowerShell s hesla jako řádky kódu:
    
    ```powershell
    # Static Version
    $PEip = "<Privileged Endpoint IP or Name>" # You can also use the machine name instead of IP here.
    $PEUser = "<Privileged Endpoint user for exmaple Domain\CloudAdmin>"
    $PEpwd = ConvertTo-SecureString "<Privileged Endpoint Password>" -AsPlainText -Force
    $PECred = New-Object System.Management.Automation.PSCredential ($PEUser, $PEpwd) 
    $NewBMCpwd = ConvertTo-SecureString "<New BMC Password>" -AsPlainText -Force 

    $PEPSession = New-PSSession -ComputerName $PEip -Credential $PECred -ConfigurationName "PrivilegedEndpoint" 

    Invoke-Command -Session $PEPSession -ScriptBlock {
        Set-Bmcpassword -bmcpassword $using:NewBMCpwd
    }
    Remove-PSSession -Session $PEPSession
    ```

## <a name="next-steps"></a>Další postup

[Další informace o zabezpečení Azure zásobníku](azure-stack-security-foundations.md)
