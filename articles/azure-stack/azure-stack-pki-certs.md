---
title: Azure požadavky na certifikáty infrastruktury veřejných klíčů zásobníku pro Azure zásobníku integrované systémy | Microsoft Docs
description: Popisuje požadavky na nasazení certifikát PKI zásobník Azure pro Azure zásobníku integrované systémy.
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
ms.date: 06/06/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: faf85c34c527dd72889f0fcb5021925b79481163
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/06/2018
ms.locfileid: "34823845"
---
# <a name="azure-stack-public-key-infrastructure-certificate-requirements"></a>Azure požadavky na certifikáty infrastruktury veřejných klíčů zásobníku

Sada Azure má síť infrastruktury veřejných pomocí externě dostupný veřejné IP adresy přiřazené k malého služeb Azure zásobníku a které by mohly mít klientské virtuální počítače. Certifikáty PKI s odpovídající názvy DNS pro tyto koncové body Azure zásobníku infrastruktury veřejných jsou nezbytné při nasazení Azure zásobníku. Tento článek obsahuje informace o:

- Jaké certifikáty se vyžadují k nasazení Azure zásobníku
- Proces získání certifikátů odpovídající tyto specifikace
- Jak připravit, ověření a použití těchto certifikátů během nasazování

> [!NOTE]
> Během nasazení je nutné zkopírovat certifikáty do složky nasazení, která odpovídá zprostředkovatele identity, které nasazujete proti (Azure AD ani AD FS). Pokud použijete jeden certifikát pro všechny koncové body, je nutné zkopírovat tento soubor certifikátu do každé složky pro nasazení, jak je uvedeno v následujících tabulkách. Struktura složek je předem součástí nasazení virtuálního počítače a naleznete na adrese: C:\CloudDeployment\Setup\Certificates. 

## <a name="certificate-requirements"></a>Požadavky na certifikát
Následující seznam popisuje požadavky na certifikát, které jsou nutné k nasazení Azure zásobníku: 
- Certifikáty musí být vystavené z interní certifikační autority nebo veřejné certifikační autority. Pokud se používá z veřejné certifikační autority, musí být zahrnut v bitové kopii základní operační systém v rámci programu Microsoft důvěryhodné kořenové autoritě. Úplný seznam najdete: https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca 
- Vaše infrastruktura Azure zásobníku musí mít přístup k síti do umístění seznamu odvolaných certifikátů (CRL) certifikační autority publikován v certifikátu. Tento seznam odvolaných certifikátů musí být koncový bod http
- Když otáčení certifikátů, certifikáty musí být že buď vystavené stejným interní certifikační autorita používaná k podepisování certifikátů uvedených v nasazení nebo všechny veřejné certifikační autority z výše
- Použití certifikáty podepsané svým držitelem nejsou podporovány.
- Certifikát může být jeden zástupný znak certifikát zahrnující všechny obory názvů v poli Alternativní název předmětu (SAN). Alternativně můžete použít jednotlivé certifikáty pomocí zástupné znaky pro koncové body, jako třeba **acs** a Key Vault, kde jsou vyžadovány. 
- Algoritmus podpisu certifikátu nemůže být SHA1, jako musí být vyšší. 
- Certifikát musí být ve formátu PFX, jako veřejné a soukromé klíče jsou požadovány pro instalaci zásobník Azure. 
- Soubory certifikátů pfx musí mít hodnotu "Digitální podpis" a "KeyEncipherment" v jeho poli "Použití klíče".
- Soubory certifikátů pfx musí mít hodnoty "Ověření serveru (1.3.6.1.5.5.7.3.1)" a "Ověření klienta (1.3.6.1.5.5.7.3.2)" v poli "Použití rozšířeného klíče".
- Tento certifikát "vystaveno pro:" pole nesmí být stejné jako jeho "vydaný:" pole.
- Hesla, aby všechny soubory pfx certifikátů musí být stejný v době nasazení
- Heslo pro soubor pfx certifikátu musí být složité heslo.
- Zajistěte, aby názvy subjektu a alternativní názvy předmětu certifikátů odpovídaly specifikace popsané v tomto článku, aby se zabránilo selhání nasazení.

> [!NOTE]
> Vlastní certifikáty podepsaná nejsou podporovány.

> [!NOTE]
> Přítomnost prostředník certifikačních autorit v řetězu vztahy důvěryhodnosti je certifikát na podporována. 

## <a name="mandatory-certificates"></a>Povinné certifikáty
V tabulce v této části jsou certifikáty infrastruktury veřejných KLÍČŮ veřejný koncový bod Azure zásobníku, které jsou požadovány pro obě Azure AD a nasazení zásobník Azure AD FS. Požadavky na certifikát jsou seskupené podle oblasti, jakož i oborů názvů používaných a certifikáty, které se vyžadují pro každý obor názvů. V tabulce jsou popsány také složku, ve kterém poskytovatele řešení zkopíruje různé certifikáty za veřejný koncový bod. 

Certifikáty s odpovídající názvy DNS pro každý koncový bod Azure zásobníku infrastruktury veřejných se vyžadují. Název DNS každý koncový bod je vyjádřen ve formátu:  *&lt;předpony >.&lt; oblast >. &lt;plně kvalifikovaný název domény >*. 

Pro vaše nasazení [Oblast] a [externalfqdn] hodnoty musí odpovídat oblasti a názvy externí domény, které jste zvolili pro systém Azure zásobníku. Jako příklad, pokud byl název oblasti *Redmond* a název domény externího byl *contoso.com*, názvy DNS by mít formát *&lt;předpony >. redmond.contoso.com*. *&lt;Předpony >* hodnoty jsou předem definovanou společností Microsoft k popisu zabezpečené certifikát koncového bodu. Kromě toho  *&lt;předpony >* hodnoty koncových bodů externí infrastruktury závisí na službu Azure zásobníku, která používá konkrétní koncový bod. 

> [!note]  
> Certifikáty může být zadaný jako certifikát jeden zástupný znak zahrnující všechny obory názvů v polích subjektu a alternativní název předmětu (SAN) zkopírovány do všech adresářů, nebo jako jednotlivé certifikáty pro každý koncový bod zkopírován do příslušné adresáře. Pamatujte si, že obě možnosti vyžadují, abyste pomocí certifikáty se zástupnými znaky pro koncové body, jako například **acs** a Key Vault, kde jsou vyžadovány. 

| Složky pro nasazení | Požadovaný certifikát subjektu a alternativní názvy subjektu (SAN) | Obor (podle oblasti) | SubDomain namespace |
|-------------------------------|------------------------------------------------------------------|----------------------------------|-----------------------------|
| Veřejné portálu | portál. &lt;oblast >. &lt;plně kvalifikovaný název domény > | Portály | &lt;oblast >. &lt;plně kvalifikovaný název domény > |
| Portál pro správu | adminportal. &lt;oblast >. &lt;plně kvalifikovaný název domény > | Portály | &lt;oblast >. &lt;plně kvalifikovaný název domény > |
| Veřejný Azure Resource Manager | Správa. &lt;oblast >. &lt;plně kvalifikovaný název domény > | Azure Resource Manager | &lt;oblast >. &lt;plně kvalifikovaný název domény > |
| Správce Azure Resource Manager | adminmanagement. &lt;oblast >. &lt;plně kvalifikovaný název domény > | Azure Resource Manager | &lt;oblast >. &lt;plně kvalifikovaný název domény > |
| ACSBlob | *.blob.&lt;region>.&lt;fqdn><br>(Certifikát SSL typu Wildcard) | Blob Storage | objekt BLOB. &lt;oblast >. &lt;plně kvalifikovaný název domény > |
| ACSTable | * .table. &lt;oblast >. &lt;plně kvalifikovaný název domény ><br>(Certifikát SSL typu Wildcard) | Table Storage | Tabulka. &lt;oblast >. &lt;plně kvalifikovaný název domény > |
| ACSQueue | * .queue. &lt;oblast >. &lt;plně kvalifikovaný název domény ><br>(Certifikát SSL typu Wildcard) | Queue Storage | fronty. &lt;oblast >. &lt;plně kvalifikovaný název domény > |
| KeyVault | * .vault. &lt;oblast >. &lt;plně kvalifikovaný název domény ><br>(Certifikát SSL typu Wildcard) | Key Vault | trezor. &lt;oblast >. &lt;plně kvalifikovaný název domény > |
| KeyVaultInternal | *.adminvault. &lt;oblast >. &lt;plně kvalifikovaný název domény ><br>(Certifikát SSL typu Wildcard) |  Interní Keyvault |  adminvault. &lt;oblast >. &lt;plně kvalifikovaný název domény > |

### <a name="for-azure-stack-environment-on-pre-1803-versions"></a>Pro prostředí Azure zásobníku 1803 předběžné verze

|Složky pro nasazení|Požadovaný certifikát subjektu a alternativní názvy subjektu (SAN)|Obor (podle oblasti)|SubDomain namespace|
|-----|-----|-----|-----|
|Veřejné portálu|Portál.  *&lt;oblast >.&lt; plně kvalifikovaný název domény >*|Portály|*&lt;oblast >. &lt;plně kvalifikovaný název domény >*|
|Portál pro správu|Adminportal.  *&lt;oblast >.&lt; plně kvalifikovaný název domény >*|Portály|*&lt;oblast >. &lt;plně kvalifikovaný název domény >*|
|Veřejný Azure Resource Manager|Správa.  *&lt;oblast >.&lt; plně kvalifikovaný název domény >*|Azure Resource Manager|*&lt;oblast >. &lt;plně kvalifikovaný název domény >*|
|Správce Azure Resource Manager|Adminmanagement.  *&lt;oblast >.&lt; plně kvalifikovaný název domény >*|Azure Resource Manager|*&lt;oblast >. &lt;plně kvalifikovaný název domény >*|
|ACS<sup>1</sup>|Jeden více subdomény certifikát se zástupným znakem s názvy subjektu alternativní pro:<br>&#42;.blob.*&lt;region>.&lt;fqdn>*<br>&#42;.queue.*&lt;region>.&lt;fqdn>*<br>&#42;.Table.  *&lt;oblast >.&lt; plně kvalifikovaný název domény >*|Úložiště|objekt BLOB.  *&lt;oblast >.&lt; plně kvalifikovaný název domény >*<br>Tabulka.  *&lt;oblast >.&lt; plně kvalifikovaný název domény >*<br>fronty.  *&lt;oblast >.&lt; plně kvalifikovaný název domény >*|
|KeyVault|&#42;.Vault.  *&lt;oblast >.&lt; plně kvalifikovaný název domény >*<br>(Certifikát SSL typu Wildcard)|Key Vault|trezor.  *&lt;oblast >.&lt; plně kvalifikovaný název domény >*|
|KeyVaultInternal|&#42;.adminvault.  *&lt;oblast >.&lt; plně kvalifikovaný název domény >*<br>(Certifikát SSL typu Wildcard)|Interní Keyvault|adminvault.  *&lt;oblast >.&lt; plně kvalifikovaný název domény >*|
|
<sup>1</sup> certifikát ACS vyžaduje tři zástupné sítě SAN na jeden certifikát. Všechny veřejné certifikační autority nemusí podporovat více zástupné sítí SAN na jeden certifikát. 

Pokud nasadíte zásobník Azure pomocí režimu nasazení služby Azure AD, stačí vyžádat certifikáty uvedené v předchozí tabulce. Ale pokud nasazujete zásobník Azure pomocí režimu nasazení služby AD FS, musíte také požádat o certifikáty, které jsou popsané v následující tabulce:

|Složky pro nasazení|Požadovaný certifikát subjektu a alternativní názvy subjektu (SAN)|Obor (podle oblasti)|SubDomain namespace|
|-----|-----|-----|-----|
|ADFS|Služba AD FS.  *&lt;oblast >.&lt; plně kvalifikovaný název domény >*<br>(Certifikát SSL)|ADFS|*&lt;oblast >. &lt;plně kvalifikovaný název domény >*|
|Graph|Graf.  *&lt;oblast >.&lt; plně kvalifikovaný název domény >*<br>(Certifikát SSL)|Graph|*&lt;oblast >. &lt;plně kvalifikovaný název domény >*|
|

> [!IMPORTANT]
> Všechny certifikáty, které jsou uvedené v této části musí mít stejné heslo. 

## <a name="optional-paas-certificates"></a>Volitelné PaaS certifikáty
Pokud plánujete nasadit další služby Azure zásobníku PaaS (SQL, MySQL a služby App Service) po zásobník Azure byla nasazena a nakonfigurována, budete muset požádat o další certifikáty pro koncové body služby PaaS. 

> [!IMPORTANT]
> Certifikáty, které používáte pro poskytovatele prostředků služby App Service, SQL a MySQL musí mít stejnou kořenovou autoritou jako používaných pro globální koncové body Azure zásobníku. 

Následující tabulka popisuje koncové body a certifikáty potřebné pro adaptéry SQL a MySQL a pro službu App Service. Nemusíte tyto certifikáty zkopírovat do složky pro nasazení Azure zásobníku. Tyto certifikáty se místo toho zadejte při instalaci dalších prostředků poskytovatelů. 

|Obor (podle oblasti)|Certifikát|Požadovaný certifikát subjektu a alternativní názvy subjektu (SAN)|SubDomain namespace|
|-----|-----|-----|-----|
|SQL, MySQL|SQL a MySQL|&#42;.dbadapter.  *&lt;oblast >.&lt; plně kvalifikovaný název domény >*<br>(Certifikát SSL typu Wildcard)|dbadapter.  *&lt;oblast >.&lt; plně kvalifikovaný název domény >*|
|App Service|Certifikát SSL výchozí web provoz|&#42;.appservice.*&lt;region>.&lt;fqdn>*<br>&#42;.scm.appservice.*&lt;region>.&lt;fqdn>*<br>&#42;.sso.appservice.*&lt;region>.&lt;fqdn>*<br>(Certifikát SSL typu Wildcard více doménami<sup>1</sup>)|appservice.*&lt;region>.&lt;fqdn>*<br>scm.appservice.*&lt;region>.&lt;fqdn>*|
|App Service|Rozhraní API|api.appservice.*&lt;region>.&lt;fqdn>*<br>(Certifikát SSL<sup>2</sup>)|appservice.*&lt;region>.&lt;fqdn>*<br>scm.appservice.*&lt;region>.&lt;fqdn>*|
|App Service|FTP|ftp.appservice.*&lt;region>.&lt;fqdn>*<br>(Certifikát SSL<sup>2</sup>)|appservice.*&lt;region>.&lt;fqdn>*<br>scm.appservice.*&lt;region>.&lt;fqdn>*|
|App Service|Jednotné přihlašování|sso.appservice.*&lt;region>.&lt;fqdn>*<br>(Certifikát SSL<sup>2</sup>)|appservice.*&lt;region>.&lt;fqdn>*<br>scm.appservice.*&lt;region>.&lt;fqdn>*|

<sup>1</sup> vyžaduje jeden certifikát s více alternativní názvy subjektu zástupný znak. Více zástupné sítí SAN na jeden certifikát nemusí podporovat všechny veřejné certifikačních autorit 

<sup>2</sup> A &#42;.appservice. *&lt;oblast >. &lt;plně kvalifikovaný název domény >* zástupný certifikát nelze použít namísto tyto tři certifikáty (api.appservice. *&lt;oblast >. &lt;plně kvalifikovaný název domény >*, ftp.appservice. *&lt;oblast >. &lt;plně kvalifikovaný název domény >* a sso.appservice. *&lt;oblast >. &lt;plně kvalifikovaný název domény >*. Služby App Service explicitně vyžaduje použití samostatných certifikátů pro tyto koncové body. 

## <a name="learn-more"></a>Další informace
Zjistěte, jak [vygenerujete certifikáty infrastruktury veřejných KLÍČŮ pro nasazení Azure zásobníku](azure-stack-get-pki-certs.md). 

## <a name="next-steps"></a>Další postup
[Integrace identit](azure-stack-integrate-identity.md)

