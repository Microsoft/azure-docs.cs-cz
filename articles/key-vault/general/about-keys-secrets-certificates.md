---
title: Přehled Azure Key Vault klíčů, tajných kódů a certifikátů
description: Přehled rozhraní Azure Key Vault REST a podrobností pro vývojáře pro klíče, tajné klíče a certifikáty.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: overview
ms.date: 04/17/2020
ms.author: mbaldwin
ms.openlocfilehash: f2a33b4011b0bb89ca17010fc787aebe8c17fc92
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102183926"
---
# <a name="azure-key-vault-keys-secrets-and-certificates-overview"></a>Přehled Azure Key Vault klíčů, tajných kódů a certifikátů

Azure Key Vault umožňuje aplikacím a uživatelům Microsoft Azure ukládat a používat několik typů tajných dat a klíčů. Poskytovatel prostředků Key Vault podporuje dva typy prostředků: trezory a spravované HSM.

## <a name="dns-suffixes-for-base-url"></a>Přípony DNS pro základní adresu URL
 Následující tabulka ukazuje příponu DNS základní adresy URL používané koncovým bodem datové roviny pro trezory a spravované fondy HSM v různých cloudových prostředích.

Cloudové prostředí | Přípona DNS pro trezory | Přípona DNS pro spravovanou HSM
---|---|---
Cloud Azure | . vault.azure.net | . managedhsm.azure.net
Cloud Azure Čína | . vault.azure.cn | Nepodporováno
Azure pro vládu USA | . vault.usgovcloudapi.net | Nepodporováno
Cloud pro Azure němčina | . vault.microsoftazure.de | Nepodporováno
|||


## <a name="object-types"></a>Typy objektů
 V následující tabulce jsou uvedeny typy objektů a jejich přípony v základní adrese URL.

typ objektu|Přípona adresy URL|Trezory|Spravované fondy HSM
--|--|--|--
**Kryptografické klíče**||
Klíče chráněné pomocí HSM|/keys|Podporováno|Podporováno
Klíče chráněné softwarem|/keys|Podporováno|Nepodporováno
**Další typy objektů**||
Tajné kódy|/secrets|Podporováno|Nepodporováno
Certifikáty|/certificates|Podporováno|Nepodporováno
Klíče účtu úložiště|/Storage|Podporováno|Nepodporováno
|||
- **Kryptografické klíče**: podporuje více typů klíčů a algoritmů a umožňuje použití klíčů chráněných softwarem a HSM. Další informace najdete v tématu [o klíčích](../keys/about-keys.md).
- **Tajné** kódy: poskytuje zabezpečené úložiště tajných kódů, jako jsou hesla a databázové připojovací řetězce. Další informace najdete v tématu [o tajných klíčích](../secrets/about-secrets.md).
- **Certifikáty**: podporuje certifikáty, které jsou postavené na klíčích a tajných klíčích a přidávají funkci automatického obnovení. Další informace najdete v tématu [o certifikátech](../certificates/about-certificates.md).
- **Klíče účtu Azure Storage**: můžou spravovat klíče účtu Azure Storage za vás. Interně Key Vault možné vypsat (synchronizovat) klíče s účtem Azure Storage a pravidelně je znovu vygenerovat (otočit) klíče. Další informace najdete v tématu [Správa klíčů účtu úložiště pomocí Key Vault](../secrets/overview-storage-keys.md).

Obecnější informace o Key Vault najdete v tématu [o Azure Key Vault](overview.md). Další informace o spravovaných fondech HSM najdete v tématu Co je [Azure Key Vault spravovaný modul HSM?](../managed-hsm/overview.md) .


## <a name="data-types"></a>Typy dat

V tématu Specifikace JOSE najdete relevantní datové typy pro klíče, šifrování a podepisování.  

-   **Algorithm** – podporovaný algoritmus pro klíčové operace, například RSA1_5  
-   **šifrovaný text-hodnota** – oktety textu šifry, kódované pomocí Base64URL  
-   **Digest-Value** – výstup algoritmu hash, kódovaný pomocí Base64URL  
-   **typ klíče** – jeden z podporovaných typů klíčů, například RSA (Rivest-Shamir-Adleman).  
-   oktety ve formátu **prostého** textu a prostého textu kódované pomocí Base64URL  
-   **Signatura-hodnota** – výstup algoritmu podpisu kódovanýho pomocí Base64URL  
-   **base64URL** – binární hodnota kódovaná v BASE64URL [RFC4648]  
-   **Boolean** – buď true, nebo false  
-   **Identita** – identita z Azure Active Directory (AAD).  
-   **IntDate** – Desítková hodnota JSON představující počet sekund od roku 1970-01-01T0:0: 0Z UTC až do zadaného data a času UTC. Podrobnosti týkající se data a času, obecně a UTC, najdete v tématu RFC3339.  

## <a name="objects-identifiers-and-versioning"></a>Objekty, identifikátory a správa verzí

Objekty uložené v Key Vault jsou tvořeny verzí pokaždé, když je vytvořena nová instance objektu. Každé verzi je přiřazen jedinečný identifikátor a adresa URL. Při prvním vytvoření objektu se mu přidaný jedinečný identifikátor verze a označen jako aktuální verze objektu. Vytvoření nové instance se stejným názvem objektu udělí novému objektu jedinečný identifikátor verze, což způsobí, že se stane aktuální verzí.  

Objekty v Key Vault lze adresovat zadáním verze nebo vynecháním verze pro operace s aktuální verzí objektu. Například při zadání klíče s názvem `MasterKey` a provedení operací bez určení verze způsobí, že systém použije nejnovější dostupnou verzi. Provádění operací s identifikátorem specifickým pro verzi způsobí, že systém použije určitou konkrétní verzi objektu.  

### <a name="vault-name-and-object-name"></a>Název trezoru a názvu objektu
Objekty se jednoznačně identifikují v rámci Key Vault pomocí adresy URL. Žádné dva objekty v systému nemají stejnou adresu URL, bez ohledu na geografickou polohu. Úplná adresa URL objektu se nazývá identifikátor objektu. Adresa URL se skládá z předpony, která identifikuje Key Vault, typ objektu, uživatelem poskytnutý název objektu a verzi objektu. Název objektu rozlišuje velká a malá písmena. Identifikátory, které neobsahují verzi objektu, jsou označovány jako základní identifikátory.  

Další informace najdete v tématu [ověřování, žádosti a odpovědi](authentication-requests-and-responses.md) .

Identifikátor objektu má následující obecný formát (v závislosti na typu kontejneru):  

- **Pro trezory**: `https://{vault-name}.vault.azure.net/{object-type}/{object-name}/{object-version}`  

- **Pro spravované fondy HSM**: `https://{hsm-name}.managedhsm.azure.net/{object-type}/{object-name}/{object-version}`  

> [!NOTE]
> Viz [Podpora typu objektu](#object-types) pro typy objektů podporované jednotlivými typy kontejnerů.

Kde:  

| Element | Popis |  
|-|-|  
|`vault-name` nebo `hsm-name`|Název trezoru nebo spravovaného fondu HSM ve službě Microsoft Azure Key Vault.<br /><br />Názvy trezorů a názvy spravovaných fondů HSM jsou vybrány uživatelem a jsou globálně jedinečné.<br /><br />Název trezoru a název spravovaného fondu HSM musí být řetězec znaků 3-24, který obsahuje pouze 0-9, a-z, a-Z a-.|  
|`object-type`|Typ objektu, "Keys", "tajné" nebo "certifikáty".|  
|`object-name`|`object-name`Je název zadaný uživatelem pro a musí být jedinečný v rámci Key Vault. Název musí být řetězec znaků 1-127, který začíná písmenem a obsahuje pouze 0-9, a-z, A-Z a-.|  
|`object-version`|`object-version`Je systémem generovaný identifikátor řetězce znaků 32, který lze volitelně použít k adresování jedinečné verze objektu.|  

## <a name="next-steps"></a>Další kroky

- [Informace o klíčích](../keys/about-keys.md)
- [Informace o tajných kódech](../secrets/about-secrets.md)
- [Informace o certifikátech](../certificates/about-certificates.md)
- [Ověřování, žádosti a odpovědi](../general/authentication-requests-and-responses.md)
- [Průvodce vývojáře pro službu Key Vault](../general/developers-guide.md)
