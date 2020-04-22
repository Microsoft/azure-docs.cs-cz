---
title: Klíče, tajné klíče a certifikáty azure trezoru klíčů – Azure Key Vault
description: Přehled rozhraní Azure Key Vault REST a podrobnosti o vývojáři klíčů, tajných kódů a certifikátů.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: overview
ms.date: 04/17/2020
ms.author: mbaldwin
ms.openlocfilehash: 241efab246dc903981da570a4191f93cc744bca7
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726442"
---
# <a name="about-keys-secrets-and-certificates"></a>Klíče, tajné klíče a certifikáty

Azure Key Vault umožňuje aplikacím a uživatelům Microsoft Azure ukládat a používat několik typů tajných dat a dat klíče:

- Kryptografické klíče: Podporuje více typů klíčů a algoritmů a umožňuje použití modulů hardwarového zabezpečení (HSM) pro klíče s vysokou hodnotou. Další informace naleznete v [tématu O klíčích](../keys/about-keys.md).
- Tajné klíče: Poskytuje zabezpečené ukládání tajných kódů, jako jsou hesla a připojovací řetězce databáze. Další informace naleznete v tématu [O tajných informacích](../secrets/about-secrets.md).
- Certifikáty: Podporuje certifikáty, které jsou postaveny na klíče a tajných kódů a přidat funkci automatického obnovení. Další informace naleznete v [tématu O certifikátech](../certificates/about-certificates.md).
- Azure Storage: Můžete spravovat klíče účtu Azure Storage za vás. Trezor klíčů může interně vypsat (synchronizovat) klíče pomocí účtu úložiště Azure a pravidelně je regenerovat (otáčet). Další informace naleznete v [tématu Správa klíčů účtu úložiště pomocí trezoru klíčů](../secrets/overview-storage-keys.md).

Další obecné informace o trezoru klíčů naleznete v tématu [O azure key vault](overview.md).

## <a name="data-types"></a>Typy dat

Klíče, šifrování a podepisování naleznete ve specifikacích JOSE pro příslušné datové typy.  

-   **algoritmus** - podporovaný algoritmus pro klíčovou operaci, například RSA1_5  
-   **ciphertext-value** - šifrovací textové oktety, kódované pomocí Base64URL  
-   **digest-value** - výstup algoritmu hash, kódovaný pomocí Base64URL  
-   **typ klíče** - jeden z podporovaných typů klíčů, například RSA (Rivest-Shamir-Adleman).  
-   **hodnota prostého textu** - oktety ve formátu prostého textu, kódované pomocí base64URL  
-   **podpis-hodnota** - výstup podpisového algoritmu, kódovaný pomocí Base64URL  
-   **base64URL** - základní 64URL [RFC4648] kódovaná binární hodnota  
-   **logická** - buď pravdivá nebo nepravdivá  
-   **Identita** – identita ze služby Azure Active Directory (AAD).  
-   **IntDate** - desetinná hodnota JSON představující počet sekund od 1970-01-01T0:0:0Z UTC až do zadaného data a času UTC. Podrobnosti o datu/časech obecně a zejména u UTC viz RFC3339.  

## <a name="objects-identifiers-and-versioning"></a>Objekty, identifikátory a správa verzí

Objekty uložené v trezoru klíčů jsou verzí při každém vytvoření nové instance objektu. Každé verzi je přiřazen jedinečný identifikátor a adresa URL. Při prvním vytvoření objektu je mu přidělen jedinečný identifikátor verze a je označen jako aktuální verze objektu. Vytvoření nové instance se stejným názvem objektu dává novému objektu jedinečný identifikátor verze, což způsobuje, že se stane aktuální verzí.  

Objekty v trezoru klíčů lze adresovat pomocí aktuálního identifikátoru nebo identifikátoru specifického pro verzi. Například vzhledem ke klíči s názvem `MasterKey`, provádění operací s aktuálním identifikátorem způsobí, že systém použije nejnovější dostupnou verzi. Provádění operací s identifikátorem specifickým pro verzi způsobí, že systém použije tuto konkrétní verzi objektu.  

Objekty jsou jednoznačně identifikovány v trezoru klíčů pomocí adresy URL. Žádné dva objekty v systému mají stejnou adresu URL, bez ohledu na geografické umístění. Úplná adresa URL objektu se nazývá Identifikátor objektu. Adresa URL se skládá z předpony, která identifikuje trezor klíčů, typ objektu, uživatelem poskytnutý název objektu a verzi objektu. Název objektu je malá a velká písmena a neměnné. Identifikátory, které neobsahují verzi objektu, se označují jako základní identifikátory.  

Další informace naleznete v [tématu Ověřování, požadavky a odpovědi](authentication-requests-and-responses.md)

Identifikátor objektu má následující obecný formát:  

`https://{keyvault-name}.vault.azure.net/{object-type}/{object-name}/{object-version}`  

Kde:  

|||  
|-|-|  
|`keyvault-name`|Název trezoru klíčů ve službě Microsoft Azure Key Vault.<br /><br /> Názvy trezorů klíčů jsou vybrány uživatelem a jsou globálně jedinečné.<br /><br /> Název trezoru klíčů musí být řetězec 3-24 znaků, který obsahuje pouze 0-9, a-z, A-Z a -.|  
|`object-type`|Typ objektu, "klíče", "tajné klíče" nebo "certifikáty".|  
|`object-name`|A `object-name` je uživatel zadaný název pro a musí být jedinečný v trezoru klíčů. Název musí být řetězec znaků 1-127, který obsahuje pouze 0-9, a-z, A-Z a -.|  
|`object-version`|A `object-version` je systémem generovaný, 32 znakový řetězec identifikátor, který se volitelně používá k adresování jedinečnou verzi objektu.|  

## <a name="next-steps"></a>Další kroky

- [Informace o klíčích](../keys/about-keys.md)
- [Informace o tajných kódech](../secrets/about-secrets.md)
- [Informace o certifikátech](../certificates/about-certificates.md)
- [Ověřování, požadavky a odpovědi](../general/authentication-requests-and-responses.md)
- [Průvodce vývojáře pro Key Vault](../general/developers-guide.md)