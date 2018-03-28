---
title: Azure požadavky na certifikáty infrastruktury veřejných klíčů zásobníku pro Azure zásobníku integrované systémy | Microsoft Docs
description: Popisuje požadavky na nasazení certifikát PKI zásobník Azure pro Azure zásobníku integrované systémy.
services: azure-stack
documentationcenter: ''
author: mabriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: 455c74ca808f71258a12166c2e36bdd73d9a3e20
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="azure-stack-public-key-infrastructure-certificate-requirements"></a>Požadavky na certifikát Azure zásobníku infrastruktura veřejných klíčů
Sada Azure má síť infrastruktury veřejných pomocí externě dostupný veřejné IP adresy přiřazené k malého služeb Azure zásobníku a které by mohly mít klientské virtuální počítače. Certifikáty PKI s odpovídající názvy DNS pro tyto koncové body Azure zásobníku infrastruktury veřejných jsou nezbytné při nasazení Azure zásobníku. Tento článek obsahuje informace o:

- Jaké certifikáty se vyžadují k nasazení Azure zásobníku
- Proces získání certifikátů odpovídající tyto specifikace
- Jak připravit, ověření a použití těchto certifikátů během nasazování

> [!NOTE]
> Během nasazení je nutné zkopírovat certifikáty do složky nasazení, která odpovídá zprostředkovatele identity, které nasazujete proti (Azure AD ani AD FS). Pokud použijete jeden certifikát pro všechny koncové body, je nutné zkopírovat tento soubor certifikátu do každé složky pro nasazení, jak je uvedeno v následujících tabulkách. Struktura složek je předem součástí nasazení virtuálního počítače a naleznete na adrese: C:\CloudDeployment\Setup\Certificates. 

## <a name="certificate-requirements"></a>Požadavky na certifikát
Následující seznam popisuje požadavky na certifikát, které jsou nutné k nasazení Azure zásobníku: 
- Certifikáty musí být vystavené z interní certifikační autority nebo veřejné certifikační autority. Pokud se používá z veřejné certifikační autority, musí být zahrnut v bitové kopii základní operační systém v rámci programu Microsoft důvěryhodné kořenové autoritě. Úplný seznam najdete: https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca 
- Certifikát může být jeden zástupný znak certifikát zahrnující všechny obory názvů v poli Alternativní název předmětu (SAN). Alternativně můžete vytvořit jednotlivé certifikáty pomocí zástupných znaků pro koncové body, například služby acs a Key Vault, kde jsou vyžadovány. 
- Algoritmus podpisu certifikátu nemůže být SHA1, jako musí být vyšší. 
- Certifikát musí být ve formátu PFX, jako veřejné a soukromé klíče jsou požadovány pro instalaci zásobník Azure. 
- Soubory certifikátů pfx musí mít hodnotu "Digitální podpis" a "KeyEncipherment" v jeho poli "Použití klíče".
- Soubory certifikátů pfx musí mít hodnoty "Ověření serveru (1.3.6.1.5.5.7.3.1)" a "Ověření klienta (1.3.6.1.5.5.7.3.2)" v poli "Použití rozšířeného klíče".
- Tento certifikát "vystaveno pro:" pole nesmí být stejné jako jeho "vydaný:" pole.
- Hesla, aby všechny soubory pfx certifikátů musí být stejný v době nasazení
- Zajistěte, aby názvy subjektu a alternativní názvy předmětu certifikátů odpovídaly specifikace popsané v tomto článku, aby se zabránilo selhání nasazení.

> [!NOTE]
> Přítomnost prostředník certifikačních autorit v řetězu vztahy důvěryhodnosti je certifikát na podporována. 

## <a name="mandatory-certificates"></a>Povinné certifikáty
V tabulce v této části jsou certifikáty infrastruktury veřejných KLÍČŮ veřejný koncový bod Azure zásobníku, které jsou požadovány pro obě Azure AD a nasazení zásobník Azure AD FS. Požadavky na certifikát jsou seskupené podle oblasti, jakož i oborů názvů používaných a certifikáty, které se vyžadují pro každý obor názvů. V tabulce jsou popsány také složku, ve kterém poskytovatele řešení zkopíruje různé certifikáty za veřejný koncový bod. 

Certifikáty s odpovídající názvy DNS pro každý koncový bod Azure zásobníku infrastruktury veřejných se vyžadují. Název DNS každý koncový bod je vyjádřen ve formátu:  *&lt;předpony >.&lt; oblast >. &lt;plně kvalifikovaný název domény >*. 

Pro vaše nasazení [Oblast] a [externalfqdn] hodnoty musí odpovídat oblasti a názvy externí domény, které jste zvolili pro systém Azure zásobníku. Jako příklad, pokud byl název oblasti *Redmond* a název domény externího byl *contoso.com*, názvy DNS by mít formát *&lt;předpony >. redmond.contoso.com*.  *&lt;Předpony >* hodnoty jsou předem definovanou společností Microsoft k popisu zabezpečené certifikát koncového bodu. Kromě toho  *&lt;předpony >* hodnoty koncových bodů externí infrastruktury závisí na službu Azure zásobníku, která používá konkrétní koncový bod. 

|Složky pro nasazení|Požadovaný certifikát subjektu a alternativní názvy subjektu (SAN)|Obor (podle oblasti)|SubDomain namespace|
|-----|-----|-----|-----|
|Veřejné portálu|portal.*&lt;region>.&lt;fqdn>*|Portály|*&lt;region>.&lt;fqdn>*|
|Portál pro správu|adminportal.*&lt;region>.&lt;fqdn>*|Portály|*&lt;region>.&lt;fqdn>*|
|Veřejný Azure Resource Manager|management.*&lt;region>.&lt;fqdn>*|Azure Resource Manager|*&lt;region>.&lt;fqdn>*|
|Správce Azure Resource Manager|adminmanagement.*&lt;region>.&lt;fqdn>*|Azure Resource Manager|*&lt;region>.&lt;fqdn>*|
|ACS<sup>1</sup>|Jeden více subdomény certifikát se zástupným znakem s názvy subjektu alternativní pro:<br>&#42;.blob.*&lt;region>.&lt;fqdn>*<br>&#42;.queue.*&lt;region>.&lt;fqdn>*<br>&#42;.table.*&lt;region>.&lt;fqdn>*|Úložiště|blob.*&lt;region>.&lt;fqdn>*<br>Tabulka.  *&lt;oblast >.&lt; plně kvalifikovaný název domény >*<br>fronty.  *&lt;oblast >.&lt; plně kvalifikovaný název domény >*|
|KeyVault|&#42;.vault.*&lt;region>.&lt;fqdn>*<br>(Certifikát SSL typu Wildcard)|Key Vault|trezor.  *&lt;oblast >.&lt; plně kvalifikovaný název domény >*|
|KeyVaultInternal|&#42;.adminvault.*&lt;region>.&lt;fqdn>*<br>(Certifikát SSL typu Wildcard)|Internal Keyvault|adminvault.  *&lt;oblast >.&lt; plně kvalifikovaný název domény >*|
|
<sup>1</sup> certifikát ACS vyžaduje tři zástupné sítě SAN na jeden certifikát. Všechny veřejné certifikační autority nemusí podporovat více zástupné sítí SAN na jeden certifikát. 

Pokud nasadíte zásobník Azure pomocí režimu nasazení služby Azure AD, stačí vyžádat certifikáty uvedené v předchozí tabulce. Ale pokud nasazujete zásobník Azure pomocí režimu nasazení služby AD FS, musíte také požádat o certifikáty, které jsou popsané v následující tabulce:

|Složky pro nasazení|Požadovaný certifikát subjektu a alternativní názvy subjektu (SAN)|Obor (podle oblasti)|SubDomain namespace|
|-----|-----|-----|-----|
|ADFS|adfs.*&lt;region>.&lt;fqdn>*<br>(Certifikát SSL)|ADFS|*&lt;region>.&lt;fqdn>*|
|Graph|graph.*&lt;region>.&lt;fqdn>*<br>(Certifikát SSL)|Graph|*&lt;region>.&lt;fqdn>*|
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
|SQL, MySQL|SQL a MySQL|&#42;.dbadapter.*&lt;region>.&lt;fqdn>*<br>(Certifikát SSL typu Wildcard)|dbadapter.*&lt;region>.&lt;fqdn>*|
|App Service|Certifikát SSL výchozí web provoz|&#42;.appservice.*&lt;region>.&lt;fqdn>*<br>&#42;.scm.appservice.*&lt;region>.&lt;fqdn>*<br>(Certifikát SSL typu Wildcard více doménami<sup>1</sup>)|appservice.*&lt;region>.&lt;fqdn>*<br>scm.appservice.*&lt;region>.&lt;fqdn>*|
|App Service|Rozhraní API|api.appservice.*&lt;region>.&lt;fqdn>*<br>(Certifikát SSL<sup>2</sup>)|appservice.*&lt;region>.&lt;fqdn>*<br>scm.appservice.*&lt;region>.&lt;fqdn>*|
|App Service|FTP|ftp.appservice.*&lt;region>.&lt;fqdn>*<br>(Certifikát SSL<sup>2</sup>)|appservice.*&lt;region>.&lt;fqdn>*<br>scm.appservice.*&lt;region>.&lt;fqdn>*|
|App Service|SSO|sso.appservice.*&lt;region>.&lt;fqdn>*<br>(Certifikát SSL<sup>2</sup>)|appservice.*&lt;region>.&lt;fqdn>*<br>scm.appservice.*&lt;region>.&lt;fqdn>*|

<sup>1</sup> vyžaduje jeden certifikát s více alternativní názvy subjektu zástupný znak. Více zástupné sítí SAN na jeden certifikát nemusí podporovat všechny veřejné certifikačních autorit 

<sup>2</sup> A &#42;.appservice. *&lt;oblast >. &lt;plně kvalifikovaný název domény >* zástupný certifikát nelze použít namísto tyto tři certifikáty (api.appservice. *&lt;oblast >. &lt;plně kvalifikovaný název domény >*, ftp.appservice. *&lt;oblast >. &lt;plně kvalifikovaný název domény >*a sso.appservice. *&lt;oblast >. &lt;plně kvalifikovaný název domény >*. Služby App Service explicitně vyžaduje použití samostatných certifikátů pro tyto koncové body. 

## <a name="learn-more"></a>Další informace
Zjistěte, jak [vygenerujete certifikáty infrastruktury veřejných KLÍČŮ pro nasazení Azure zásobníku](azure-stack-get-pki-certs.md). 

## <a name="next-steps"></a>Další postup
[Integrace identit](azure-stack-integrate-identity.md)

