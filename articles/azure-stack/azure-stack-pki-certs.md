---
title: Integrované systémy pro Azure požadavky na certifikát zásobníku infrastruktura veřejných klíčů pro Azure Stack | Dokumentace Microsoftu
description: Popisuje požadavky na certifikát nasazení Azure Stack infrastruktury veřejných KLÍČŮ pro integrované systémy Azure Stack.
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
ms.date: 10/16/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: a6f18222e5683d2d9663b699a8f6bab399d4f45b
ms.sourcegitcommit: d372d75558fc7be78b1a4b42b4245f40f213018c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2018
ms.locfileid: "51299856"
---
# <a name="azure-stack-public-key-infrastructure-certificate-requirements"></a>Požadavky na certifikáty infrastruktury veřejných klíčů Azure Stack

Azure Stack je síť infrastruktury veřejného pomocí zvenku přístupný veřejné IP adresy přiřazené k malé sadě služby Azure Stack a případně klientské virtuální počítače. Certifikáty PKI s příslušnými názvy DNS pro tyto koncové body služby Azure Stack infrastruktury veřejných jsou potřeba při nasazení Azure stacku. Tento článek obsahuje informace o:

- Jaké certifikáty jsou požadovány pro nasazení Azure Stack
- Proces získání certifikátů vyhovujících tyto specifikace
- Příprava, ověřit a použití těchto certifikátů během nasazení

> [!Note]  
> Během nasazení musíte zkopírovat certifikáty do složky pro nasazení, který odpovídá zprostředkovatele identity, které nasazujete proti (Azure AD nebo AD FS). Pokud použijete jeden certifikát pro všechny koncové body, musíte zkopírovat daný soubor certifikátu do každé složky pro nasazení, jak je uvedeno v následujících tabulkách. Struktura složek je předem připravené na nasazení virtuálním počítači a najdete tady: C:\CloudDeployment\Setup\Certificates. 

## <a name="certificate-requirements"></a>Požadavky na certifikát
Následující seznam popisuje požadavky na certifikáty, které jsou nutné k nasazení Azure Stack: 
- Certifikáty musí být vydány interní certifikační autority nebo veřejné certifikační autority. Pokud se používá z veřejné certifikační autority, musí být součástí image základního operačního systému v rámci aplikace Microsoft důvěryhodné kořenové autoritě. Úplný seznam najdete: https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca 
- Infrastruktury Azure stacku musí mít přístup k síti do publikované v certifikátu umístění seznamu odvolaných certifikátů (CRL) certifikační autority. Tento seznam odvolaných certifikátů musí být koncový bod http
- Při obměně certifikátů, certifikáty musí být že buď vydávány stejné interní certifikační autorita používaná k podepisování certifikátů, které jsou k dispozici v nasazení nebo jakékoli veřejné certifikační autority výše
- Použití certifikátů podepsaných svým držitelem nejsou podporovány.
- Pro nasazení a otočení můžete buď použít jeden certifikát pokrývající všechny obory názvů v pole název subjektu a alternativní název předmětu (SAN) certifikátu, nebo můžete použít jednotlivé certifikáty pro každý obor názvů níže, která službě Azure Stack služby, které máte v plánu využít vyžadují. Oba přístupy vyžadují pomocí zástupných znaků pro koncové body, ve kterém jsou povinné, například **KeyVault** a **KeyVaultInternal**. 
- Tento certifikát PFX šifrování by měl být 3DES. 
- Algoritmus podpisu certifikátu by neměly být SHA1. 
- Certifikát musí být ve formátu PFX, veřejné a soukromé klíče jsou požadována pro instalaci služby Azure Stack. Privátní klíč musí mít místní počítač klíčový atribut nastavit.
- PFX šifrování musí být algoritmus 3DES (Toto je výchozí, při exportu z klienta Windows 10 nebo úložiště certifikátů systému Windows Server 2016).
- Soubory certifikátů pfx musí mít hodnotu "Digitální podpis" a "KeyEncipherment" v poli "Použití klíče".
- Soubory certifikátů pfx musí mít hodnotu "Ověření serveru (1.3.6.1.5.5.7.3.1)" a "Ověření klienta (1.3.6.1.5.5.7.3.2)" v poli "Použití rozšířeného klíče".
- Certifikátu "vystaveno pro:" pole nesmí být stejný jako jeho "Vystavitel:" pole.
- Hesla, aby všechny soubory certifikátů pfx musí být stejná v době nasazování
- Heslo pro soubor pfx certifikátu musí být složité heslo.
- Ujistěte se, že názvy subjektu a alternativní názvy subjektu v rozšíření (x509v3_config) shody alternativní název subjektu. Pole alternativní název subjektu umožňuje určit další hostitele (weby, IP adresy, běžné názvy) která bude chráněná funkcí jedním certifikátem SSL.

> [!NOTE]  
> Vlastní certifikáty podepsaná nejsou podporovány.

> [!NOTE]  
> Přítomnost zprostředkovatel certifikační autority v řetězu vztahy důvěryhodnosti je certifikátu nepodporuje. 

## <a name="mandatory-certificates"></a>Povinné certifikáty
V tabulce v této části jsou popsány certifikáty infrastruktury veřejných KLÍČŮ veřejný koncový bod služby Azure Stack, které jsou požadovány pro Azure AD a nasazení služby AD FS Azure Stack. Požadavky na certifikát jsou seskupené podle oblasti, jakož i obory názvů používané a certifikáty, které jsou požadovány pro každý obor názvů. Tabulka taky popisuje složku, ve kterém poskytovatele řešení zkopíruje různé certifikáty na veřejný koncový bod. 

Jsou vyžadovány certifikáty s příslušnými názvy DNS pro každý koncový bod veřejné infrastruktury Azure stacku. Název DNS každý koncový bod je vyjádřená ve formátu:  *&lt;předpony >.&lt; oblast >. &lt;plně kvalifikovaný název domény >*. 

Pro vaše nasazení [Oblast] a [externalfqdn] hodnoty musí odpovídat oblasti a názvy externích domén, které jste zvolili pro váš systém Azure Stack. Jako příklad, pokud byl název oblasti *Redmond* a názvem externí domény *contoso.com*, názvy DNS by mít formát *&lt;předpony >. redmond.contoso.com*. *&lt;Předpony >* hodnoty jsou předem definovanou společností Microsoft k popisu koncový bod zabezpečené pomocí certifikátu. Kromě toho  *&lt;předpony >* hodnoty infrastruktury externí koncové body, závisí na služby Azure Stack, který používá určitého koncového bodu. 

> [!note]  
> Pro produkční prostředí doporučujeme jednotlivé certifikáty jsou generovány pro každý koncový bod a zkopírován do příslušné adresáře. Pro vývojové prostředí certifikáty lze zadat jako jeden zástupný certifikát pokrývající všechny obory názvů v subjektu a alternativní název předmětu (SAN) polí zkopírována do všech adresářů. Jeden certifikát, které pokrývají všechny koncové body a služby je nezabezpečené stav, proto jenom pro vývojové prostředí. Nezapomeňte, že obě možnosti vyžadují, abyste pomocí certifikátů se zástupnými znaky pro koncové body, jako **acs** a kde se vyžadují služby Key Vault. 

| Složka pro nasazení | Požadovaný certifikát subjektu a alternativní názvy subjektu (SAN) | Obor (podle oblasti) | SubDomain namespace |
|-------------------------------|------------------------------------------------------------------|----------------------------------|-----------------------------|
| Veřejný portál | portál. &lt;oblast >. &lt;plně kvalifikovaný název domény > | Portály | &lt;oblast >. &lt;plně kvalifikovaný název domény > |
| Portál pro správu | adminportal. &lt;oblast >. &lt;plně kvalifikovaný název domény > | Portály | &lt;oblast >. &lt;plně kvalifikovaný název domény > |
| Veřejné Azure Resource Manageru | Správa. &lt;oblast >. &lt;plně kvalifikovaný název domény > | Azure Resource Manager | &lt;oblast >. &lt;plně kvalifikovaný název domény > |
| Správce Azure Resource Manageru | adminmanagement. &lt;oblast >. &lt;plně kvalifikovaný název domény > | Azure Resource Manager | &lt;oblast >. &lt;plně kvalifikovaný název domény > |
| ACSBlob | *.blob.&lt;region>.&lt;fqdn><br>(Certifikát SSL typu Wildcard) | Blob Storage | objekt BLOB. &lt;oblast >. &lt;plně kvalifikovaný název domény > |
| ACSTable | * .table. &lt;oblast >. &lt;plně kvalifikovaný název domény ><br>(Certifikát SSL typu Wildcard) | Table Storage | Tabulka. &lt;oblast >. &lt;plně kvalifikovaný název domény > |
| ACSQueue | * .queue. &lt;oblast >. &lt;plně kvalifikovaný název domény ><br>(Certifikát SSL typu Wildcard) | Queue Storage | fronty. &lt;oblast >. &lt;plně kvalifikovaný název domény > |
| KeyVault | * .vault. &lt;oblast >. &lt;plně kvalifikovaný název domény ><br>(Certifikát SSL typu Wildcard) | Key Vault | trezor. &lt;oblast >. &lt;plně kvalifikovaný název domény > |
| KeyVaultInternal | *.adminvault. &lt;oblast >. &lt;plně kvalifikovaný název domény ><br>(Certifikát SSL typu Wildcard) |  Interní služby Keyvault |  adminvault. &lt;oblast >. &lt;plně kvalifikovaný název domény > |
| Hostitel Správce rozšíření | *.adminhosting. \<oblast >. \<plně kvalifikovaný název domény > (zástupné certifikáty SSL) | Hostitel Správce rozšíření | adminhosting. \<oblast >. \<plně kvalifikovaný název domény > |
| Veřejná rozšiřující hostitele | * .hosting. \<oblast >. \<plně kvalifikovaný název domény > (zástupné certifikáty SSL) | Veřejná rozšiřující hostitele | hostování. \<oblast >. \<plně kvalifikovaný název domény > |

Pokud provádíte nasazení Azure Stack pomocí režimu nasazení služby Azure AD, stačí pro žádosti o certifikáty uvedené v předchozí tabulce. Nicméně pokud provádíte nasazení Azure Stack pomocí režimu nasazení služby AD FS, musíte také požádat o certifikáty jsou popsané v následující tabulce:

|Složka pro nasazení|Požadovaný certifikát subjektu a alternativní názvy subjektu (SAN)|Obor (podle oblasti)|SubDomain namespace|
|-----|-----|-----|-----|
|ADFS|služby AD FS.  *&lt;oblast >.&lt; plně kvalifikovaný název domény >*<br>(Certifikát SSL)|ADFS|*&lt;oblast >. &lt;plně kvalifikovaný název domény >*|
|Graph|graf.  *&lt;oblast >.&lt; plně kvalifikovaný název domény >*<br>(Certifikát SSL)|Graph|*&lt;oblast >. &lt;plně kvalifikovaný název domény >*|
|

> [!IMPORTANT]
> Všechny certifikáty, které jsou uvedené v této části musí mít stejné heslo. 

## <a name="optional-paas-certificates"></a>Volitelné certifikáty PaaS
Pokud plánujete nasadit další služby Azure Stack PaaS (SQL, MySQL a App Service) po Azure Stack byla nasazena a nakonfigurována, budete muset požádat o další certifikáty pro koncové body služeb modelu PaaS. 

> [!IMPORTANT]
> Certifikáty, které používáte pro poskytovatele prostředků App Service, SQL a MySQL musí mít stejné kořenové autority používané pro globální koncové body služby Azure Stack. 

Následující tabulka popisuje koncové body a certifikáty, které jsou nutné pro adaptéry SQL a MySQL a pro službu App Service. Není nutné zkopírovat tyto certifikáty do složky pro nasazení Azure Stack. Tyto certifikáty se místo toho zadejte při instalaci poskytovatele dalších prostředků. 

|Obor (podle oblasti)|Certifikát|Požadovaný certifikát subjektu a alternativní názvy subjektu (SAN)|SubDomain namespace|
|-----|-----|-----|-----|
|SQL, MySQL|SQL a MySQL|&#42;.dbadapter.  *&lt;oblast >.&lt; plně kvalifikovaný název domény >*<br>(Certifikát SSL typu Wildcard)|dbadapter.  *&lt;oblast >.&lt; plně kvalifikovaný název domény >*|
|App Service|Webové přenosy výchozí certifikát SSL|&#42;.appservice.*&lt;region>.&lt;fqdn>*<br>&#42;.scm.appservice.*&lt;region>.&lt;fqdn>*<br>&#42;.sso.appservice.*&lt;region>.&lt;fqdn>*<br>(Certifikát SSL typu Wildcard více domény<sup>1</sup>)|appservice.*&lt;region>.&lt;fqdn>*<br>scm.appservice.*&lt;region>.&lt;fqdn>*|
|App Service|Rozhraní API|api.appservice.*&lt;region>.&lt;fqdn>*<br>(Certifikát SSL<sup>2</sup>)|appservice.*&lt;region>.&lt;fqdn>*<br>scm.appservice.*&lt;region>.&lt;fqdn>*|
|App Service|FTP|ftp.appservice.*&lt;region>.&lt;fqdn>*<br>(Certifikát SSL<sup>2</sup>)|appservice.*&lt;region>.&lt;fqdn>*<br>scm.appservice.*&lt;region>.&lt;fqdn>*|
|App Service|Jednotné přihlašování|sso.appservice.*&lt;region>.&lt;fqdn>*<br>(Certifikát SSL<sup>2</sup>)|appservice.*&lt;region>.&lt;fqdn>*<br>scm.appservice.*&lt;region>.&lt;fqdn>*|

<sup>1</sup> vyžaduje jeden certifikát s více alternativní názvy subjektu zástupný znak. Všechny veřejné certifikační autority nemusí podporovat více zástupných znaků sítě SAN na jeden certifikát 

<sup>2</sup> A &#42;.appservice. *&lt;oblast >. &lt;plně kvalifikovaný název domény >* zástupný certifikát nelze použít namísto tyto tři certifikáty (api.appservice. *&lt;oblast >. &lt;plně kvalifikovaný název domény >*, ftp.appservice. *&lt;oblast >. &lt;plně kvalifikovaný název domény >* a sso.appservice. *&lt;oblast >. &lt;plně kvalifikovaný název domény >*. Použití samostatných certifikátů služby App Service explicitně vyžaduje pro tyto koncové body. 

## <a name="learn-more"></a>Další informace
Zjistěte, jak [vygenerovat certifikáty PKI pro nasazení Azure stacku](azure-stack-get-pki-certs.md). 

## <a name="next-steps"></a>Další postup
[Integrace identit](azure-stack-integrate-identity.md)

