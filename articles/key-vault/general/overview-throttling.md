---
title: Pokyny k omezení úložiště klíčů Azure
description: Omezení trezoru klíčů omezuje počet souběžných volání, aby se zabránilo nadměrnému využívání prostředků.
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mbaldwin
ms.openlocfilehash: f32a988ec0d75ca8d8eca04e69edd7226bf283b4
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81432083"
---
# <a name="azure-key-vault-throttling-guidance"></a>Pokyny k omezení úložiště klíčů Azure

Omezení je proces, který zahájíte, který omezuje počet souběžných volání služby Azure, aby se zabránilo nadměrnému využívání prostředků. Azure Key Vault (AKV) je navržený pro zpracování velkého objemu požadavků. Pokud dojde k obrovskému počtu požadavků, omezení požadavků klienta pomáhá udržovat optimální výkon a spolehlivost služby AKV.

Omezení omezení omezení omezení se liší v závislosti na scénáři. Například pokud provádíte velký objem zápisů, možnost omezení je vyšší, než pokud provádíte pouze čtení.

## <a name="how-does-key-vault-handle-its-limits"></a>Jak trezor klíčů zvládá své limity?

Omezení služeb v trezoru klíčů zabraňují zneužití prostředků a zajišťují kvalitu služeb pro všechny klienty trezoru klíčů. Při překročení prahové hodnoty služby trezoru klíčů omezuje všechny další požadavky z tohoto klienta po určitou dobu, vrátí stavový kód HTTP 429 (příliš mnoho požadavků) a požadavek se nezdaří. Neúspěšné požadavky, které vracejí 429 počítat do omezení omezení sledovány trezoru klíčů. 

Trezor klíčů byl původně navržen pro ukládání a načítání tajných klíčů v době nasazení.  Svět se vyvinul a Key Vault se používá za běhu k ukládání a načítání tajných kódů a aplikace a služby často chtějí používat Key Vault jako databázi.  Aktuální limity nepodporují vysokou propustnost.

Trezor klíčů byl původně vytvořen s omezeními zadanými v [limitech služby Azure Key Vault](service-limits.md).  Chcete-li maximalizovat úložiště klíčů prostřednictvím sazeb na dotík, zde je několik doporučených pokynů a doporučených postupů pro maximalizaci propustnosti:
1. Ujistěte se, že máte škrcení na místě.  Klient musí ctít exponenciální zásady back-off pro 429 a ujistěte se, že děláte opakování podle pokynů níže.
1. Rozdělte provoz v trezoru klíčů mezi více trezorů a různých oblastí.   Pro každou doménu zabezpečení/dostupnosti použijte samostatný trezor.   Pokud máte pět aplikací, každý ve dvou oblastech, pak doporučujeme 10 trezorů každý obsahuje tajné kódy jedinečné pro aplikaci a oblast.  Limit pro celé předplatné pro všechny typy transakcí je pětkrát limit úložiště klíčů. Například transakce HSM ostatní na předplatné jsou omezeny na 5 000 transakcí za 10 sekund na odběr. Zvažte ukládání tajných kódů do mezipaměti ve vaší službě nebo aplikaci, abyste také snížili RPS přímo do trezoru klíčů a/nebo zpracovat provoz založený na shlukování.  Můžete také rozdělit provoz mezi různé oblasti, abyste minimalizovali latenci a použili jiné předplatné nebo trezor.  Neodesílejte více než limit předplatného do služby Trezor klíčů v jedné oblasti Azure.
1. Ukládat do mezipaměti tajné klíče, které načtete z úložiště klíčů Azure, v paměti a znovu je použít z paměti, kdykoli je to možné.  Znovu číst z Azure Key Vault pouze v případě, že kopie uložené v mezipaměti přestane fungovat (například proto, že se otočený u zdroje). 
1. Key Vault je určen pro vaše vlastní služby tajemství.   Pokud ukládáte tajné klíče zákazníků (zejména pro scénáře úložiště s vysokou propustností klíče), zvažte umístění klíčů do databáze nebo účtu úložiště s šifrováním a uložení pouze hlavního klíče v úložišti azure key vault.
1. Šifrovat, zalamovat a ověřovat operace veřejného klíče lze provádět bez přístupu k trezoru klíčů, což nejen snižuje riziko omezení, ale také zvyšuje spolehlivost (pokud správně ukládáte materiál veřejného klíče do mezipaměti).
1. Pokud používáte Trezor klíčů k ukládání přihlašovacích údajů pro službu, zkontrolujte, jestli tato služba podporuje ověřování Azure AD k přímému ověření. To snižuje zatížení trezoru klíčů, zvyšuje spolehlivost a zjednodušuje váš kód, protože trezor klíčů teď můžete použít token Azure AD.  Mnoho služeb se přesunuly pomocí Azure AD Auth.  Podívejte se na aktuální seznam na [webu Services, který podporuje spravované identity pro prostředky Azure](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources).
1. Zvažte rozložení zatížení/nasazení po delší dobu, abyste zůstali pod současnými limity RPS.
1. Pokud vaše aplikace obsahuje více uzlů, které potřebují číst stejné tajné klíče, zvažte použití vzoru rozptýlí, kde jedna entita čte tajný klíč z trezoru klíčů a fanoušky do všech uzlů.   Ukládat načtené tajné klíče do mezipaměti pouze v paměti.
Pokud zjistíte, že výše uvedené stále nesplňuje vaše potřeby, vyplňte prosím níže uvedenou tabulku a kontaktujte nás, abyste zjistili, jaká další kapacita může být přidána (příklad je uveden níže pouze pro ilustrační účely).

| Název trezoru | Oblast trezoru | Typ objektu (tajný klíč, klíč nebo certifikát) | Provoz (operace)* | Typ klíče | Délka klíče nebo křivka | HSM klíč?| Potřeba RPS v ustáleném stavu | Špičkový RPS potřebný |
|--|--|--|--|--|--|--|--|--|
| https://mykeyvault.vault.azure.net/ | | Klíč | Znaménko | EC | P-256 | Ne | 200 | 1000 |

\*Úplný seznam možných hodnot naleznete v tématu [Azure Key Vault operations](/rest/api/keyvault/key-operations).

Pokud je schválena dodatečná kapacita, vezměte prosím na vědomí následující v důsledku zvýšení kapacity:
1. Změny modelu konzistence dat. Jakmile je úložiště vypsáno s další kapacitou propustnosti, konzistence dat služby Key Vault zaručuje změny (nezbytné pro splnění vyššího objemu RPS, protože základní služba Azure Storage nemůže držet krok).  Ani náhodou:
  1. **Bez povolení výpis**: Služba Trezor klíčů bude odrážet výsledky operace zápisu (např. SecretSet, CreateKey) okamžitě v následných voláních (např. SecretGet, KeySign).
  1. **S povolit výpis**: Key Vault služba bude odrážet výsledky operace zápisu (např. SecretSet, CreateKey) do 60 sekund v následných voláních (např. SecretGet, KeySign).
1. Klientský kód musí respektovat zásady back-off pro 429 opakování. Klientský kód volající službu Trezor klíčů nesmí okamžitě opakovat požadavky trezoru klíčů, když obdrží kód odpovědi 429.  Pokyny k omezení úložiště klíčů Azure, které jsou zde publikovány, doporučuje použít exponenciální zpětný dotaz při příjmu kódu odpovědi 429 Http.

Pokud máte platný obchodní případ pro vyšší limity plynu, kontaktujte nás.

## <a name="how-to-throttle-your-app-in-response-to-service-limits"></a>Jak omezit aplikaci v reakci na omezení služeb

Následující jsou **doporučené postupy, které** byste měli implementovat, když je vaše služba omezena:
- Snižte počet operací na požadavek.
- Snižte četnost požadavků.
- Vyhněte se okamžitým opakováním. 
    - Všechny požadavky narůstají v porovnání s vašimi limity využití.

Při implementaci zpracování chyb vaší aplikace použijte kód chyby HTTP 429 ke zjištění potřeby omezení na straně klienta. Pokud se požadavek nezdaří znovu s kódem chyby HTTP 429, stále dochází k omezení služby Azure. Pokračujte v používání doporučené metody omezení na straně klienta a opakujte požadavek, dokud nebude úspěšný.

Kód, který implementuje exponenciální backoff je uveden níže. 
```
SecretClientOptions options = new SecretClientOptions()
    {
        Retry =
        {
            Delay= TimeSpan.FromSeconds(2),
            MaxDelay = TimeSpan.FromSeconds(16),
            MaxRetries = 5,
            Mode = RetryMode.Exponential
         }
    };
    var client = new SecretClient(new Uri(https://keyVaultName.vault.azure.net"), new DefaultAzureCredential(),options);
                                 
    //Retrieve Secret
    secret = client.GetSecret(secretName);
```


Použití tohoto kódu v klientské aplikaci C# je jednoduché. 

### <a name="recommended-client-side-throttling-method"></a>Doporučená metoda omezení na straně klienta

Na kódu chyby HTTP 429 začněte přiškrcení klienta pomocí exponenciálního přístupu backoff:

1. Počkejte 1 sekundu, požadavek na opakování
2. Pokud je stále omezen počkejte 2 sekundy, opakujte požadavek
3. Pokud je stále omezen počkejte 4 sekundy, opakujte požadavek
4. Pokud je stále omezen počkejte 8 sekund, opakujte požadavek
5. Pokud je stále omezen počkejte 16 sekund, opakujte požadavek

V tomto okamžiku byste neměli dostávat kódy odpovědí HTTP 429.

## <a name="see-also"></a>Viz také

Hlubší orientaci omezení na Microsoft Cloud, najdete v tématu [škrcení vzor](https://docs.microsoft.com/azure/architecture/patterns/throttling).

