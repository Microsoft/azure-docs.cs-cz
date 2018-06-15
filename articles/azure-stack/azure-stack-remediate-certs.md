---
title: Opravit problémy s certifikátem pro Azure zásobníku | Microsoft Docs
description: Pomocí kontroly připravenosti zásobník Azure zkontrolujte a opravte problémy s certifikátem.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/08/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 0d2c4d848f861e4e07dbd0de4609344955ca26f7
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
ms.locfileid: "33937848"
---
# <a name="remediate-common-issues-for-azure-stack-pki-certificates"></a>Řešení běžných problémů pro certifikáty PKI zásobník Azure
Informace v tomto článku vám může pomoct pochopit a řešit obvyklé problémy pro certifikáty PKI zásobník Azure. Můžete zjistit problémy při použití nástroje Kontrola připravenosti zásobník Azure k [ověření certifikátů PKI zásobník Azure](azure-stack-validate-pki-certs.md). Nástroj zkontroluje, zkontrolujte, zda splňují požadavky infrastruktury veřejných KLÍČŮ nasazení zásobník Azure a Azure zásobníku tajný klíč otočení certifikáty a přihlásí výsledky [report.json soubor](azure-stack-validation-report.md).  

## <a name="read-pfx"></a>Čtení PFX
**Upozornění** -heslo chrání pouze soukromé informace v certifikátu.  
**Náprava** -doporučujeme exportovat soubory PFX s volitelné nastavení pro **povolit ochranu osobních údajů certifikát**.  

**Selhání** – neplatný soubor PFX.  
**Náprava** -znovu exportovat certifikát pomocí kroků v [Příprava Azure zásobníku infrastruktury veřejných KLÍČŮ certifikátů pro nasazení](azure-stack-prepare-pki-certs.md).

## <a name="signature-algorithm"></a>Algoritmus podpisu
**Selhání** -algoritmus podpisu je SHA1.    
**Náprava** – postupujte podle kroků v zásobníku Azure certifikáty Podepisování generování žádost znovu vygenerovat požadavek (Podepsání certifikátu) s podpis algoritmus SHA256. Potom odešlete znovu soubor CSR k to obnášet opětovné vystavení certifikátu certifikační autority.

## <a name="private-key"></a>Privátní klíč
**Selhání** -privátní klíč nebyl nalezen nebo neobsahuje atribut místní počítač.  
**Náprava** – z počítače, který generuje oddělení služeb zákazníkům, znovu exportujte certifikát pomocí kroků v certifikátech Příprava Azure zásobníku infrastruktury veřejných KLÍČŮ pro nasazení. Tyto kroky zahrnují export z úložiště certifikátů místního počítače.

## <a name="certificate-chain"></a>Řetěz certifikátů
**Selhání** -řetěz certifikátů není úplný.  
**Náprava** -certifikáty by měl obsahovat řetězec dokončení certifikátů.  Znovu exportovat certifikát pomocí kroků v [Příprava Azure zásobníku infrastruktury veřejných KLÍČŮ certifikátů pro nasazení](azure-stack-prepare-pki-certs.md) a vyberte možnost **zahrnout všechny certifikáty na cestě k certifikátu, pokud je to možné.**

## <a name="dns-names"></a>Názvy DNS
**Selhání** -DNSNameList na certifikátu neobsahuje název koncového bodu služby Azure zásobníku nebo platný zástupný shoda.  Zástupný znak odpovídá jsou platné pouze pro obor názvů nejvíce vlevo název DNS. Například _*. region.domain.com_ je platná pouze pro *portal.region.domain.com*, nikoli _*. table.region.domain.com_.  
**Náprava** – postupujte podle kroků v zásobníku Azure certifikáty Podepisování generování žádost znovu vygenerovat CSR s správné názvy DNS pro podporu koncové body Azure zásobníku. Odešlete znovu CSR certifikační autoritě a potom postupujte podle kroků v [Příprava Azure zásobníku infrastruktury veřejných KLÍČŮ certifikátů pro nasazení](azure-stack-prepare-pki-certs.md) exportujte certifikát z počítače, které vygenerovalo oddělení služeb zákazníkům.  

## <a name="key-usage"></a>Použití klíče
**Selhání** – použití klíče chybí digitální podpis nebo šifrování klíče, orEnhanced použití klíče chybí serveru ověřování nebo ověřování klientů.  
**Náprava** – postupujte podle kroků v [zásobník Azure certifikáty Podepisování generování požadavku](azure-stack-get-pki-certs.md) znovu vygenerovat CSR s atributy správné použití klíče.  Znovu odeslat soubor CSR k certifikační autoritě a potvrďte, že šablony certifikátu není přepsal použití klíče v požadavku.

## <a name="key-size"></a>Velikost klíče
**Selhání** -klíč velikost je menší než 2048    
**Náprava** – postupujte podle kroků v [zásobník Azure certifikáty Podepisování generování požadavku](azure-stack-get-pki-certs.md) znovu vygenerovat CSR s správná délka klíče (2048) a pak znovu odeslat soubor CSR k certifikační autoritě.

## <a name="chain-order"></a>Řetězec pořadí
**Selhání** -pořadí v řetězu certifikátů je nesprávný.  
**Náprava** -znovu exportovat certifikát pomocí kroků v [Příprava Azure zásobníku infrastruktury veřejných KLÍČŮ certifikátů pro nasazení](azure-stack-prepare-pki-certs.md) a vyberte možnost **zahrnout všechny certifikáty na cestě k certifikátu, pokud je to možné.** Zkontrolujte, že je vybraný jenom certifikát listu pro export. 

## <a name="other-certificates"></a>Další certifikáty
**Selhání** -PFX balíčku obsahuje certifikáty, které nejsou typu list certifikát nebo součástí řetězu certifikátů.  
**Náprava** -znovu exportovat certifikát pomocí kroků v [Příprava Azure zásobníku infrastruktury veřejných KLÍČŮ certifikátů pro nasazení](azure-stack-prepare-pki-certs.md)a vyberte možnost **zahrnout všechny certifikáty na cestě k certifikátu, pokud je to možné.** Zkontrolujte, že je vybraný jenom certifikát listu pro export.

## <a name="fix-common-packaging-issues"></a>Běžné problémy balení
AzsReadinessChecker můžete importovat a poté exportovat soubor PFX pro běžné problémy balení, včetně: 
 - *Privátní klíč* chybí atribut místní počítač.
 - *Řetěz certifikátů* je neúplná nebo nesprávné. (Místní počítač se musí obsahovat řetěz certifikátů, pokud balíček PFX neexistuje.) 
 - *Další certifikáty*.
AzsReadinessChecker nemůže však pomoct, pokud je nutné vygenerovat novou CSR a znovu vystavit certifikát. 

### <a name="prerequisites"></a>Požadavky
Na místě v počítači, kde je nástroj spuštěn musí být splněné následující předpoklady: 
 - Windows 10 nebo Windows Server 2016 s připojením k Internetu.
 - Prostředí PowerShell 5.1 nebo novější. Zkontrolujte vaši verzi, spusťte následující příkaz prostředí PowerShell a poté zkontrolovat *hlavní* verze a *menší* verze.

   > `$PSVersionTable.PSVersion`
 - Konfigurace [prostředí PowerShell pro Azure zásobníku](azure-stack-powershell-install.md). 
 - Stáhněte si nejnovější verzi [Kontrola připravenosti zásobníku Microsoft Azure](https://aka.ms/AzsReadinessChecker) nástroj.

### <a name="import-and-export-an-existing-pfx-file"></a>Import a export existující soubor PFX
1. Na počítači, který splňuje požadavky otevřete řádku pro správu prostředí PowerShell a spusťte následující příkaz pro instalaci AzsReadinessChecker  
   > `Install-Module Microsoft.AzureStack.ReadinessChecker- Force`

2. Na řádku prostředí PowerShell spusťte následující nastavit heslo PFX. Nahraďte *PFXpassword* s vlastní heslo. 
   > `$password = Read-Host -Prompt PFXpassword -AsSecureString`

3. Z řádku prostředí PowerShell spusťte následující příkaz pro export nový soubor PFX.
   - Pro *- PfxPath*, zadejte cestu k souboru PFX pracujete.  V následujícím příkladu je tato cesta *.\certificates\ssl.pfx*.
   - Pro *- ExportPFXPath*, zadejte umístění a název souboru PFX pro export.  V následujícím příkladu je tato cesta *.\certificates\ssl_new.pfx*

   > `Start-AzsReadinessChecker -PfxPassword $password -PfxPath .\certificates\ssl.pfx -ExportPFXPath .\certificates\ssl_new.pfx`  

4. Jakmile nástroj dokončí, prohlédněte si výstup pro úspěch: ![výsledky](./media/azure-stack-remediate-certs/remediate-results.png)

## <a name="next-steps"></a>Další postup
[Další informace o zabezpečení Azure zásobníku](azure-stack-rotate-secrets.md)