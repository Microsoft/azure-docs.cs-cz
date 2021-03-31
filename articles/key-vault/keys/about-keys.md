---
title: O klíčích – Azure Key Vault
description: Přehled rozhraní REST Azure Key Vault a podrobností pro vývojáře pro klíče.
services: key-vault
author: amitbapat
manager: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: overview
ms.date: 02/17/2021
ms.author: ambapat
ms.openlocfilehash: 3c4bb61217c7b972220a55a4837c2b3db980f2ca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101095991"
---
# <a name="about-keys"></a>Informace o klíčích

Azure Key Vault poskytuje dva typy prostředků pro ukládání a správu kryptografických klíčů. Trezory podporují klíče chráněné softwarem a modulem hardwarového zabezpečení (HSM). Spravovaná HSM podporuje jenom klíče chráněné HSM. 

|Typ prostředku|Metody ochrany klíčů|Základní adresa URL koncového bodu datové roviny|
|--|--|--|
| **Trezory** | Chráněný softwarem<br/><br/>a<br/><br/>Chráněná HSM (s SKU Premium)</li></ul> | https://{trezor-Name}. trezor. Azure. NET |
| * * Spravované HSM * * | Chráněná HSM | https://{HSM-Name}. managedhsm. Azure. NET |
||||

- **Trezory** – trezory poskytují nízký náklady, snadnou nasazování, víceklientské využití pro více tenantů (kde je dostupné) a vysoce dostupné řešení pro správu klíčů, které je vhodné pro většinu běžných scénářů cloudových aplikací.
- **Spravovaná HSM** spravovaná modul HSM nabízí jeden tenant (tam, kde je k dispozici), vysoce dostupný HSM pro ukládání a správu kryptografických klíčů. Nejlépe vhodná pro aplikace a scénáře použití, které zpracovávají klíče s vysokými hodnotami. Také pomáhá splnit nejpřísnější požadavky na zabezpečení, dodržování předpisů a předpisy. 

> [!NOTE]
> Trezory také umožňují kromě kryptografických klíčů ukládat a spravovat několik typů objektů, jako jsou tajné klíče, certifikáty a klíče účtu úložiště.

Kryptografické klíče v Key Vault jsou reprezentovány jako objekty webového klíče JSON [JWK]. Specifikace JavaScript Object Notation (JSON) a JOSE (JavaScript Object Signing and Encryption) jsou:

-   [Webový klíč JSON (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key)  
-   [Webové šifrování JSON (JWE)](http://tools.ietf.org/html/draft-ietf-jose-json-web-encryption)  
-   [Webové algoritmy JSON (JWA)](http://tools.ietf.org/html/draft-ietf-jose-json-web-algorithms)  
-   [Webový podpis JSON (JWS)](https://tools.ietf.org/html/draft-ietf-jose-json-web-signature) 

Základní specifikace JWK/JWA se také rozšiřují tak, aby umožňovaly typy klíčů jedinečné pro Azure Key Vault a spravované implementace HSM. 

Klíče chráněné HSM (označované také jako klíče HSM) se zpracovávají v modulu HSM (Hardware Security Module) a vždy zůstávají v hranici ochrany modulem HSM. 

- Trezory používají ověřený **Standard FIPS 140-2 úrovně 2** HSM k ochraně klíčů HSM ve sdílené back-ENDOVÉ infrastruktuře HSM. 
- Spravovaný HSM používá pro ochranu vašich klíčů ověřované moduly HSM **140-2 úrovně 3** . Každý fond HSM je izolovaná instance jednoho tenanta s vlastní [doménou zabezpečení](../managed-hsm/security-domain.md) , která poskytuje úplnou izolaci šifrování ze všech ostatních HSM sdílejících stejnou hardwarovou infrastrukturu.

Tyto klíče jsou chráněné ve fondech HSM pro jeden tenant. Můžete importovat RSA, EC a symetrický klíč, v měkkém tvaru nebo vyexportovat z podporovaného zařízení HSM. Můžete také vygenerovat klíče ve fondech HSM. Když naimportujete klíče HSM pomocí metody popsané ve [specifikaci BYOK (Přineste si vlastní klíč)](../keys/byok-specification.md), umožníte klíčům zabezpečeného přenosu klíčů do spravovaných fondů HSM. 

Další informace o geografických hranicích najdete v tématu [Microsoft Azure Trust Center](https://azure.microsoft.com/support/trust-center/privacy/) .

## <a name="key-types-and-protection-methods"></a>Typy klíčů a metody ochrany

Key Vault podporuje klíče RSA a ES. Spravovaný HSM podporuje RSA, EC a symetrický klíč. 

### <a name="hsm-protected-keys"></a>Klíče chráněné pomocí HSM

|Typ klíče|Trezory (jenom SKU úrovně Premium)|Spravované HSM|
|--|--|--|
|**ES-HSM**: eliptický klíč křivky | Podporováno | Podporováno|
|**RSA-HSM**: klíč RSA|Podporováno|Podporováno|
|**zámořské modul HSM**: symetrický klíč|Nepodporováno|Podporováno|
|||

### <a name="software-protected-keys"></a>Klíče chráněné softwarem

|Typ klíče|Trezory|Spravované HSM|
|--|--|--|
**RSA**: klíč RSA chráněný softwarem|Podporováno|Nepodporováno
**ES**: klíč eliptické křivky "software-Protected"|Podporováno|Nepodporováno
|||

### <a name="compliance"></a>Dodržování předpisů

|Typ a cíl klíče|Dodržování předpisů|
|---|---|
|Klíče chráněné softwarem v trezorech (SKU Premium & Standard) | FIPS 140-2 úrovně 1|
|Klíče chráněné HSM v trezorech (SKU úrovně Premium)| FIPS 140-2 Level 2|
|Klíče chráněné HSM ve spravovaném modulu HSM|FIPS 140-2 úrovně 3|
|||



Podrobnosti o jednotlivých typech klíčů, algoritmech, operacích, atributech a značkách najdete v tématu [typy klíčů, algoritmy a operace](about-keys-details.md) .

## <a name="next-steps"></a>Další kroky
- [Informace o službě Key Vault](../general/overview.md)
- [Informace o službě Managed HSM](../managed-hsm/overview.md)
- [Informace o tajných kódech](../secrets/about-secrets.md)
- [Informace o certifikátech](../certificates/about-certificates.md)
- [Přehled Key Vault REST API](../general/about-keys-secrets-certificates.md)
- [Ověřování, žádosti a odpovědi](../general/authentication-requests-and-responses.md)
- [Průvodce vývojáře pro službu Key Vault](../general/developers-guide.md)
