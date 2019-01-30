---
title: Oprava problémů s certifikáty pro službu Azure Stack | Dokumentace Microsoftu
description: Kontrola připravenosti Azure Stack můžete zkontrolovat a opravit problémy s certifikáty.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/19/2018
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 11/19/2018
ms.openlocfilehash: 291ecd35947c09f2b50cd8710c90d25960166422
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55242599"
---
# <a name="remediate-common-issues-for-azure-stack-pki-certificates"></a>Řešení běžných potíží s certifikáty infrastruktury veřejných KLÍČŮ Azure Stack

Informace v tomto článku můžete pochopit a řešit obvyklé problémy pro certifikáty Azure Stack PKI. Problémy můžete zjistit, když použijete nástroj prerequisite Checker připravenosti Azure Stack na [ověřování certifikátů Azure Stack infrastruktury veřejných KLÍČŮ](azure-stack-validate-pki-certs.md). Nástroj zkontroluje zajistíte, že certifikáty infrastruktury veřejných KLÍČŮ požadavkům nasazení Azure Stack a Azure Stack tajný klíč otočení a zaznamená výsledky [report.json souboru](azure-stack-validation-report.md).  

## <a name="pfx-encryption"></a>Šifrování PFX

**Selhání** – PFX šifrování není TripleDES SHA1.   

**Náprava** – PFX exportovat soubory s **TripleDES SHA1** šifrování. Toto je výchozí pro všechny klienty s Windows 10, při exportu z modulu snap-in nebo pomocí certifikátu `Export-PFXCertificate`. 

## <a name="read-pfx"></a>Přečtěte si PFX

**Upozornění** – heslo chrání pouze soukromé informace v certifikátu.  

**Náprava** – Export PFX souborů s volitelné nastavení pro **povolit ochranu osobních údajů certifikát**.  

**Selhání** – neplatný soubor PFX.  

**Náprava** -znovu exportovat certifikát pomocí kroků v [připravit Azure Stack infrastruktury veřejných KLÍČŮ certifikátů pro nasazení](azure-stack-prepare-pki-certs.md).

## <a name="signature-algorithm"></a>Algoritmus podpisu

**Selhání** -algoritmus podpisu je SHA1.   
 
**Náprava** – postupujte podle kroků v Azure stacku certifikáty Podepisování generování požadavku na opětovné vygenerování certifikátu podpisu požadavku (žádost o podepsání certifikátu) se podpisový algoritmus SHA256. Odešlete žádost o podepsání certifikátu na certifikační autority to obnášet opětovné vystavení certifikátu.

## <a name="private-key"></a>Privátní klíč

**Selhání** – privátní klíč nebyl nalezen nebo neobsahuje atribut místní počítač.  

**Náprava** – z počítače, generovány žádost o podepsání certifikátu, znovu exportovat certifikát, pomocí kroků v certifikátech připravit Azure Stack infrastruktury veřejných KLÍČŮ pro nasazení. Tyto kroky zahrnují export z úložiště certifikátů místního počítače.

## <a name="certificate-chain"></a>Řetěz certifikátů

**Selhání** – řetěz certifikátů se ještě úplně nenainstalovalo.  

**Náprava** -certifikáty by měl obsahovat řetěz certifikátů dokončení. Znovu exportovat certifikát pomocí kroků v [připravit Azure Stack infrastruktury veřejných KLÍČŮ certifikátů pro nasazení](azure-stack-prepare-pki-certs.md) a vyberte možnost **zahrnout všechny certifikáty cestě k certifikátu, pokud je to možné.**

## <a name="dns-names"></a>Názvy DNS

**Selhání** -DNSNameList na tento certifikát neobsahuje název koncového bodu služby Azure Stack nebo shodou platný zástupný znak. Zástupný znak odpovídá jsou platné pouze pro obor názvů nejvíce vlevo od názvu DNS. Například _*. region.domain.com_ je platná pouze pro *portal.region.domain.com*, nikoli _*. table.region.domain.com_. 
 
**Náprava** – postupujte podle kroků v Azure stacku certifikáty Podepisování generování požadavku se obnovit žádost o podepsání certifikátu se správné názvy DNS pro podporu koncových bodů služby Azure Stack. Znovu odeslat žádost o podepsání certifikátu na certifikační autority a pak postupujte podle kroků v [připravit Azure Stack infrastruktury veřejných KLÍČŮ certifikátů pro nasazení](azure-stack-prepare-pki-certs.md) exportujte certifikát z počítače, který vygeneruje žádost o podepsání certifikátu.  

## <a name="key-usage"></a>Použití klíče

**Selhání** – chybí použití klíče, digitální podpis nebo šifrování klíče, nebo chybí rozšířené použití klíče ověření serveru nebo klienta.  

**Náprava** – postupujte podle kroků v [podepisování generování žádosti o certifikáty Azure Stack](azure-stack-get-pki-certs.md) se obnovit žádost o podepsání certifikátu s atributy správné použití klíče. Znovu odeslat žádost o podepsání certifikátu na certifikační autority a potvrďte, že použití klíče v požadavku není přepsání pro šablonu certifikátu.

## <a name="key-size"></a>Velikost klíče

**Selhání** -Key velikost je menší než 2048.

**Náprava** – postupujte podle kroků v [podepisování generování žádosti o certifikáty Azure Stack](azure-stack-get-pki-certs.md) obnovit žádost o podepsání certifikátu se správnou délku klíče (2048) a potom znovu odeslat žádost o podepsání certifikátu na certifikační autority.

## <a name="chain-order"></a>Pořadí v řetězci

**Selhání** – pořadí v řetězu certifikátů je nesprávný.  

**Náprava** -znovu exportovat certifikát pomocí kroků v [připravit Azure Stack infrastruktury veřejných KLÍČŮ certifikátů pro nasazení](azure-stack-prepare-pki-certs.md) a vyberte možnost **zahrnout všechny certifikáty cestě k certifikátu, pokud je to možné.** Ujistěte se, že je vybrána pouze listový certifikát pro export. 

## <a name="other-certificates"></a>Další certifikáty

**Selhání** – PFX balíček obsahuje certifikáty, které nejsou listový certifikát nebo celý řetěz certifikátů.  

**Náprava** -znovu exportovat certifikát pomocí kroků v [připravit Azure Stack infrastruktury veřejných KLÍČŮ certifikátů pro nasazení](azure-stack-prepare-pki-certs.md)a vyberte možnost **zahrnout všechny certifikáty cestě k certifikátu, pokud je to možné.** Ujistěte se, že je vybrána pouze listový certifikát pro export.

## <a name="fix-common-packaging-issues"></a>Řešení běžných problémů balení

**AzsReadinessChecker** obsahuje pomocné rutiny volat `Repair-AzsPfxCertificate`, který by mohl importovat, a pak export PFX souboru pro řešení běžných problémů balení, včetně: 
 - *Šifrování PFX* není TripleDES SHA1
 - *Privátní klíč* chybí atribut místní počítač.
 - *Řetěz certifikátů* je neúplný nebo má nesprávné. Místní počítač musí obsahovat řetěz certifikátů, pokud balíček PFX.
 - *Další certifikáty*.
 
`Repair-AzsPfxCertificate` vám nemůže pomoci, pokud je potřeba vygenerovat nový soubor CSR a opakujte certifikát. 

### <a name="prerequisites"></a>Požadavky

Na počítači, na kterém je nástroj spuštěn musí být splněné následující požadavky: 
 - Windows 10 nebo Windows Server 2016 s připojením k Internetu.
 - Prostředí PowerShell 5.1 nebo novější. K ověření verze, spusťte následující rutinu prostředí PowerShell a pak si projděte *hlavní* a *menší* verze:

   ```powershell
   $PSVersionTable.PSVersion
   ```
 - Konfigurace [prostředí PowerShell pro Azure Stack](azure-stack-powershell-install.md). 
 - Stáhněte si nejnovější verzi [Microsoft Azure Stack připravenosti kontrola](https://aka.ms/AzsReadinessChecker) nástroj.

### <a name="import-and-export-an-existing-pfx-file"></a>Import a export existujícího souboru PFX

1. Na počítači, který splňuje požadavky otevřete Správce příkazový řádek Powershellu a spusťte následující příkaz k instalaci AzsReadinessChecker:
  
   ```powershell
   Install-Module Microsoft.AzureStack.ReadinessChecker -Force
   ```

2. Z příkazového řádku PowerShell spusťte následující rutinu k nastavení hesla PFX. Nahraďte *PFXpassword* skutečné heslem:
 
   ```powershell
   $password = Read-Host -Prompt PFXpassword -AsSecureString
   ```

3. Z příkazového řádku PowerShell spuštěním následujícího exportovat nový soubor PFX:
   - Pro `-PfxPath`, zadejte cestu k souboru PFX pracujete. V následujícím příkladu je tato cesta `.\certificates\ssl.pfx`.
   - Pro `-ExportPFXPath`, zadejte umístění a název souboru PFX pro export. V následujícím příkladu je tato cesta `.\certificates\ssl_new.pfx`:

   ```powershell
   Repair-AzsPfxCertificate -PfxPassword $password -PfxPath .\certificates\ssl.pfx -ExportPFXPath .\certificates\ssl_new.pfx
   ```  

4. Jakmile nástroj dokončí, prohlédněte si výstup pro úspěch:
 
   ```PowerShell
   Repair-AzsPfxCertificate v1.1809.1005.1 started.
   Starting Azure Stack Certificate Import/Export
   Importing PFX .\certificates\ssl.pfx into Local Machine Store
   Exporting certificate to .\certificates\ssl_new.pfx
   Export complete. Removing certificate from the local machine store.
   Removal complete.
   Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
   Repair-AzsPfxCertificate Completed
   ```

## <a name="next-steps"></a>Další postup

[Další informace o zabezpečení Azure stacku](azure-stack-rotate-secrets.md)
