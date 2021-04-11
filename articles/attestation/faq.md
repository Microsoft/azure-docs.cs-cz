---
title: Nejčastější dotazy
description: Odpovědi na nejčastější dotazy týkající se Microsoft Azure ověření identity
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: reference
ms.date: 07/20/2020
ms.author: mbaldwin
ms.openlocfilehash: 9b15a336e97cad1fb03a63ec34f563a4453755ff
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2021
ms.locfileid: "106504188"
---
# <a name="frequently-asked-questions-for-microsoft-azure-attestation"></a>Nejčastější dotazy k ověřování Microsoft Azure

Tento článek obsahuje odpovědi na některé nejběžnější otázky týkající se [ověření identity Azure](overview.md).

Pokud váš problém s Azure není v tomto článku řešen, můžete žádost o podporu Azure odeslat také na [stránce podpory Azure](https://azure.microsoft.com/support/options/).

## <a name="what-is-azure-pck-caching-service-and-its-role-in-enclave-attestation"></a>Co je služba Azure PCK Caching a její role v enklávy ověření identity

Služba Azure PCK Caching definuje základní hodnoty zabezpečení Azure pro uzly [důvěrné výpočetní služby Azure](../confidential-computing/overview.md) od společnosti Intel a ukládá data do mezipaměti. Informace uložené v mezipaměti budou Azure Attestation Identity dál používat při ověřování prostředí TEEs (Trusted Execution Environment).  

Služba Azure PCK Caching:
   - Nabízí vysokou dostupnost 
   - Snižuje závislosti na externě hostovaných službách a připojení k Internetu.
   - Načte nejnovější verze certifikátů Intel, seznamů CRL, informací o důvěryhodných výpočetních základech (TCB) a quote enklávy identitu uzlů z Intel. Služba proto potvrzuje, že se směrný plán Azure Security bude označovat ověřením Azure při ověřování TEEs, významně snižuje počet selhání ověření identity z důvodu neplatnosti nebo odvolání certifikátů Intel.  

## <a name="is-sgx-attestation-supported-by-azure-attestation-in-non-azure-environments"></a>Podporuje ověřování Azure pomocí ověření identity SGX v prostředích mimo Azure

No. Azure Attestation závisí na směrném plánu zabezpečení uvedeného ve službě Azure PCK Caching za účelem ověření TEEs. Služba Azure PCK Caching je aktuálně navržena tak, aby podporovala pouze uzly důvěrného výpočetního prostředí Azure. 

## <a name="what-validations-does-azure-attestation-perform-for-attesting-sgx-enclaves"></a>Jaká ověření provádí Azure Attestation pro ověřování SGX enclaves

Azure Attestation je sjednocený rámec pro vzdálené ověřování různých typů TEEs. Ověření identity Azure:

   - Ověří, jestli důvěryhodný kořen enklávyé citace patří do společnosti Intel.
   - Ověří, jestli enklávy nabídka splňuje základní hodnoty zabezpečení Azure definované službou Azure PCK Caching.
   - Ověří, jestli hodnota hash SHA256 enklávy uložených dat (EHD) v objektu žádosti o ověření identity odpovídá prvním 32 bajtů pole reportData v uvozovkách enklávy.
   - Umožňuje zákazníkům vytvořit poskytovatele ověření identity a nakonfigurovat vlastní zásady. Kromě výše uvedených ověření Azure Attestation vyhodnotí enklávy poptávku na základě zásad. Zásady definují autorizační pravidla pro enklávy a také určují pravidla vystavování pro generování tokenu ověření identity. Pokud chcete ověřit, jestli je v enklávy spuštěný software, můžou zákazníci přidat autorizační pravidla a ověřit, jestli se pole **mrsigner** a **mrenclave** v nabídce enklávy shodují s hodnotami binárních souborů zákazníků.

## <a name="how-can-a-verifier-obtain-the-collateral-for-sgx-attestation-supported-by-azure-attestation"></a>Jak může ověřovatel získat dokumentaci pro ověření identity SGX podporované službou Azure Attestation

Obecně platí, že pro modely ověření identity s Intel jako kořenem důvěryhodnosti se klient ověření identity domluví k rozhraní enklávy API, aby mohl načíst legitimaci enklávy. Rozhraní enklávy API interně volají službu Intel PCK caching pro načtení certifikátů Intel uzlu, který se má ověřit. Certifikáty se používají k podepsání enklávy legitimace a k vygenerování důkazů, které umožňují vzdáleně ověřitelné záruky.  

Stejný postup se dá implementovat pro Azure Attestation. Pokud ale chcete využít výhody nabízené službou Azure PCK Caching, doporučujeme po instalaci účtu Virtual Machine nainstalovat službu [Azure DCAP Library](https://www.nuget.org/packages/Microsoft.Azure.DCAP). Na základě smlouvy s technologií Intel se po instalaci knihovny Azure DCAP Library požadavky na generování legitimace enklávy přesměrují ze služby Intel PCK Caching do služby Azure PCK Caching. Knihovna Azure DCAP je podporována v prostředích založených na systému Windows a Linux.

## <a name="how-to-shift-to-azure-attestation-from-other-attestation-models"></a>Postup přesunutí do Azure Attestation z jiných modelů ověření identity

- Až nainstalujete důvěrný výpočetní virtuální počítač Azure, nainstalujte knihovnu Azure DCAP Library ([Windows/](https://www.nuget.org/packages/Microsoft.Azure.DCAP/) [Linux](https://packages.microsoft.com/ubuntu/18.04/prod/pool/main/a/az-dcap-client/)) a využijte výhody nabízené službou Azure PCK Caching.
- Je potřeba vytvořit klienta vzdáleného ověření identity, který může načíst legitimaci enklávy a odesílat požadavky do Azure Attestation. Podívejte se na [ukázky kódu](/samples/browse/?expanded=azure&terms=attestation) pro referenci. 
- Požadavky na ověření identity je možné odeslat do REST APIového koncového bodu výchozích zprostředkovatelů nebo vlastních poskytovatelů ověřování identity. 
- Rozhraní API Azure Attestation jsou chráněná ověřováním Azure AD. Proto klient, který volá rozhraní API ověření identity, musí být schopný získat a předat platný přístupový token Azure AD v žádosti o ověření identity. 

## <a name="how-can-the-relying-party-verify-the-integrity-of-attestation-token"></a>Jak může předávající strana ověřit integritu tokenu ověření identity

Token ověření identity generovaný službou Azure Attestation je podepsaný pomocí certifikátu podepsaného svým držitelem. Certifikáty se zveřejňují prostřednictvím [koncového bodu metadat OpenID](/rest/api/attestation/metadataconfiguration/get). Předávající strana může načíst podpisové certifikáty z tohoto koncového bodu a provést ověření podpisu tokenu ověření identity. Doba platnosti tokenu ověření je 8 hodin. 

## <a name="how-to-identify-the-certificate-to-be-used-for-signature-verification-from-the-openid-metadata-endpoint"></a>Jak identifikovat certifikát, který se má použít k ověření podpisu z koncového bodu metadat OpenID

Více certifikátů vystavených v koncovém bodu metadat OpenID odpovídá různým případům použití (např. SGX Attestation), které podporuje ověření identity Azure. Podle standardů určených [specifikací RFC 7515](https://tools.ietf.org/html/rfc7515)se certifikát s ID klíče (Kid), který odpovídá parametru *Kid* v hlavičce tokenu ověření, používá k ověření podpisu. Pokud se nenajde žádný vyhovující **Kid** , očekává se, že se vyzkouší všechny certifikáty vystavené koncovým bodem OpenID metadat.

## <a name="is-it-possible-for-the-relying-party-to-share-secrets-with-the-validated-trusted-execution-environments-tees"></a>Je možné, aby předávající strana sdílela tajné klíče s ověřenými důvěryhodnými prostředími pro spuštění (TEEs).

Veřejný klíč generovaný v rámci enklávy může být vyjádřený jako vlastnost enklávy uchovávaných dat (EHD) objektu Request Attestation odesílaného klientem do Azure Attestation. Po potvrzení, že je v poli reportData nabídky vyjádřena hodnota hash SHA256 EHD, Azure Attestation zahrnuje EHD v tokenu ověření identity. Předávající strana může použít EHD z ověřené reakce na ověření identity k šifrování tajných klíčů a jejich sdílení s enklávy. Další informace najdete v tématu [základní koncepty Azure Attestation](basic-concepts.md) .
