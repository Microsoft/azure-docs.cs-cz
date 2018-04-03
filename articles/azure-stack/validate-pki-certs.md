---
title: Ověření certifikátů infrastruktury veřejných klíčů Azure zásobníku pro nasazení Azure zásobníku integrované systémy | Microsoft Docs
description: Popisuje, jak k ověření certifikátů PKI zásobník Azure pro Azure zásobníku integrované systémy.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: b38e3cc45d14645611c0cd804f2bfa66047810f0
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2018
---
# <a name="validate-azure-stack-pki-certificates"></a>Ověření Azure zásobníku infrastruktury veřejných KLÍČŮ certifikátů
Nástroj Azure zásobníku certifikát kontrolu popsané v tomto článku je poskytnutou výrobcem OEM součástí souboru deploymentdata.json, aby bylo možné ověřit, zda [generované certifikáty PKI](azure-stack-get-pki-certs.md) jsou vhodné pro před nasazením. Certifikáty by měly být ověřené pomocí dostatek času k testování a získání certifikátů znova vydat v případě potřeby. 

Nástroj pro kontrolu certifikátu (Certchecker) provádí následující kontroly:

- **Přečtěte si PFX**. Kontroluje platný soubor PFX, správné heslo a zobrazí upozornění, pokud informace o veřejném není chráněn heslem. 
- **Algoritmus podpisu**. Ověří, že algoritmus podpisu není SHA1 
- **Privátní klíč**. Ověří privátní klíč je k dispozici a je exportován s atributem místního počítače. 
- **Řetěz certifikátů**. Ověří, že se řetěz certifikátů včetně clusterových pro certifikáty podepsané svým držitelem. 
- **Názvy DNS**. Zkontroluje, síť SAN obsahuje relevantní názvy DNS pro každý koncový bod nebo pokud podpůrný nachází zástupný znak. 
- **Použití klíče**. Kontroluje, použití klíče digitální podpis a šifrování klíče a rozšířené použití klíče obsahuje ověření serveru a ověřování klientů. 
- **Velikost klíče**. Ověří, zda se že velikost klíče je 2048 nebo větší 
- **Zřetězené pořadí**. Zkontroluje správnost pořadí jiné vytváření řetězu certifikátů. 
- **Další certifikáty**. Zkontrolujte, že žádné další certifikáty byly zabaleny v PFX než listu relevantní certifikát a jeho řetězec. 
- **Žádný profil**. Ověří, že nového uživatele můžete načíst PFX data bez profilu uživatele načíst, mimicking chování účty gMSA během obsluhy certifikátu.   

> [!IMPORTANT]
> Soubor PFX certifikátu PKI a hesla by měla považují za citlivé informace.

## <a name="prerequisites"></a>Požadavky
Váš systém by měl splňovat následující požadavky před ověřování certifikátů infrastruktury veřejných KLÍČŮ pro nasazení Azure zásobníku:
- CertChecker (v PartnerToolKit pod \utils\certchecker)
- Certifikáty SSL exportovat následující [pokyny k přípravě](prepare-pki-certs.md)
- DeploymentData.json
- Windows 10 nebo Windows Server 2016

## <a name="perform-certificate-validation"></a>Ověření certifikátu
Pomocí těchto kroků můžete připravit a ověřit certifikáty PKI zásobník Azure: 

1. Extrahujte obsah <partnerToolkit>\utils\certchecker do nového adresáře, například **c:\certchecker**.

2. Otevřete PowerShell jako správce a změňte adresář na složku certchecker:

  ```powershell
  cd c:\certchecker
  ```
 
3. Vytvořte strukturu adresářů pro certifikáty spuštěním následujících příkazů prostředí PowerShell:

  ```powershell 
  $directories = "ACS","ADFS","Admin Portal","ARM Admin","ARM Public","Graph","KeyVault","KeyVaultInternal","Public Portal" 
  $destination = '.\certs' 
  $directories | % { New-Item -Path (Join-Path $destination $PSITEM) -ItemType Directory -Force}  
  ```

  >  [!NOTE]
  >  Pokud je poskytovatel identity pro nasazení Azure zásobník Azure AD, odeberte **služby AD FS** a **grafu** adresáře. 

4. Umístíte certifikátů v adresáři odpovídající vytvořili v předchozím kroku, třeba: 
  - c:\certchecker\Certs\ACS\CustomerCertificate.pfx,  
  - c:\certchecker\Certs\Admin Portal\CustomerCertificate.pfx  
  - c:\certchecker\Certs\ARM Admin\CustomerCertificate.pfx  
  - a tak dále... 

5. Kopírování **deploymentdata.json** do **c:\certchecker** adresáře.

6. V okně Powershellu spusťte následující příkazy: 

  ```powershell
  $password = Read-Host -Prompt "Enter PFX Password" -AsSecureString 
  .\CertChecker.ps1 -CertificatePath .\Certs\ -pfxPassword $password -deploymentDataJSONPath .\DeploymentData.json  
  ```

7. Výstup by měl obsahovat OK pro všechny certifikáty a kontroluje všechny atributy: 

  ```powershell
  Starting Azure Stack Certificate Validation 1.1802.221.1
  Testing: ADFS\ContosoSSL.pfx
    Read PFX: OK
    Signature Algorithm: OK
    Private Key: OK
    Cert Chain: OK
    DNS Names: OK
    Key Usage: OK
    Key Size: OK
    Chain Order: OK
    Other Certificates: OK
    No Profile: OK
  Testing: KeyVaultInternal\ContosoSSL.pfx
    Read PFX: OK
    Signature Algorithm: OK
    Private Key: OK
    Cert Chain: OK
    DNS Names: OK
    Key Usage: OK
    Key Size: OK
    Chain Order: OK
    Other Certificates: OK
    No Profile: OK
  Testing: ACS\ContosoSSL.pfx
  WARNING: Pre-1803 certificate structure. The folder structure for Azure Stack 1803 and above is: ACSBlob, ACSQueue, ACSTable instead of ACS folder. Refer to deployment documentation for further informat
  ion.
    Read PFX: OK
    Signature Algorithm: OK
    Private Key: OK
    Cert Chain: OK
    DNS Names: OK
    Key Usage: OK
    Key Size: OK
    Chain Order: OK
    Other Certificates: OK
    No Profile: OK
  Detailed log can be found C:\CertChecker\CertChecker.log 
  ```

### <a name="known-issues"></a>Známé problémy 
**Příznaky**: Certchecker předčasně ukončí a zobrazí se následující chyba: 
> Selhalo

> Podrobnosti: Tento příkaz nelze spustit kvůli chybě: název adresáře je neplatný. 

**Příčina**: spuštěna certchecker.ps1 ze omezující složky, například, c:\temp nebo % temp %. 

**Řešení**: nástroj certchecker přesunout do nového adresáře, například C:\CertChecker 


**Příznaky**: Certchecker dává upozornění týkající se používání předběžné 1803 (jako v příkladu výše v kroku 7):

> [!WARNING]
> Struktura 1803 bez certifikátu. Složka struktury pro 1803 zásobník Azure a vyšší je: ACSTable ACSBlob, ACSQueue, místo složku ACS. Další informace naleznete v dokumentaci k nasazení.

**Příčina**: CertChecker zjistil použití jediné složce služby ACS, je to v pořádku pro nasazení před 1803. Pro verzi Azure zásobníku 1803 a vyšší nasazení změny ACSBlob ACSTable, ACSQueue, struktura složek. Certchecker byl aktualizován již pro tuto funkci podporují.

**Řešení**: Pokud nasazení 1802, není třeba žádné akce. Pokud nasazení 1803 a vyšší, nahraďte ACSTable, ACSQueue, ACSBlob služby ACS a zkopírujte autority ACS do příslušné složky.

**Příznaky**: testy se přeskočí.

**Příčina**: CertChecker přeskočí určité testy, pokud nejsou splněny závislost:
- Další certifikáty se přeskočí, pokud se nezdaří řetěz certifikátů.
- Žádný profil se přeskočí, pokud:
  - Není možnost vytvořit dočasný uživatele a spustit powershell jako tento uživatel omezení zásady zabezpečení.
  - Privátní klíč kontrola selže.

**Řešení**: podle pokynů uvedených nástroje v sekci podrobností pod každý certifikát sadu testů.


## <a name="prepare-deployment-script-certificates"></a>Příprava nasazení skriptu certifikáty 
V posledním kroku všechny certifikáty, které jste připravili muset být umístěny v adresáři odpovídající na hostiteli nasazení. Na hostiteli nasazení vytvořte složku s názvem. Certifikáty ** a umístěte soubory exportovaný certifikát v odpovídající podsložky v zadané [povinné certifikáty](https://docs.microsoft.com/azure/azure-stack/azure-stack-pki-certs#mandatory-certificates) části:

```
\Certificates
\ACS\ssl.pfx
\Admin Portal\ssl.pfx
\ARM Admin\ssl.pfx
\ARM Public\ssl.pfx
\KeyVault\ssl.pfx
\KeyVaultInternal\ssl.pfx
\Public Portal\ssl.pfx
\ADFS\ssl.pfx*
\Graph\ssl.pfx*
```

<sup>*</sup> Certifikáty označené hvězdičkou * jsou vyžadovány pouze při použití služby AD FS jako úložiště identit.


## <a name="next-steps"></a>Další postup
[Integrace identit Datacenter](azure-stack-integrate-identity.md)