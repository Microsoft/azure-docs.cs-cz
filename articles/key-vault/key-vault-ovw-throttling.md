---
title: Doprovodné materiály k omezování služby Azure Key Vault
description: Omezení služby Key Vault omezuje počet souběžných volání, aby se zabránilo nadměrnému využití prostředků.
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mbaldwin
ms.openlocfilehash: 28e79dffb206e8a62410bf3b4e0e239879b51224
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806673"
---
# <a name="azure-key-vault-throttling-guidance"></a>Doprovodné materiály k omezování služby Azure Key Vault

Omezení šířky pásma je proces, který spustíte funkci, která omezuje počet souběžných volání do služby Azure, aby se zabránilo nadměrnému využití prostředků. Azure Key Vaultu (AKV) je navržená k velkému počtu požadavků. Pokud dojde k příliš velkého počtu požadavků, omezování požadavků vašeho klienta pomáhá udržet optimální výkon a spolehlivost služby službou AZURE.

Omezení se liší v závislosti na scénáři. Například pokud provádíte velké objemy zápisy, možnost pro omezení je vyšší než pokud pouze provádíte čtení.

## <a name="how-does-key-vault-handle-its-limits"></a>Jak Key Vault zpracovává jeho omezení?

Omezení služby v Key Vault zabrání zneužití prostředků a zajišťují kvalitní službu pro všechny klienty Key Vault. Pokud dojde k překročení prahové hodnoty služby, Key Vault omezí další požadavky klienta na určitou dobu, vrátí stavový kód HTTP 429 (příliš mnoho požadavků) a požadavek se nezdařil. Neúspěšné požadavky, které vracejí 429 počtu směrem k limitům omezení sledovaných pomocí Key Vault. 

Key Vault byla původně navržena tak, aby se používala k ukládání a načítání tajných klíčů v době nasazení.  Svět se vyvinul a Key Vault se používá za běhu k ukládání a načítání tajných klíčů a často aplikace a služby chtějí používat Key Vault jako databázi.  Aktuální limity nepodporují vysoké míry propustnosti.

Byl původně vytvořen Key Vault s omezeními určenými v [omezeních služby Azure Key Vault](key-vault-service-limits.md).  Pokud chcete maximalizovat Key Vault prostřednictvím sazeb za PUT, tady jsou některé doporučené pokyny/Osvědčené postupy pro maximalizaci propustnosti:
1. Ujistěte se, že máte omezení na místě.  Klient musí dodržovat exponenciální back-vypnuté zásady pro 429 a ujistit se, že provádíte opakování podle pokynů níže.
1. Rozdělte Key Vault provoz mezi několik trezorů a různých oblastí.   Pro každou doménu zabezpečení/dostupnosti použijte samostatný trezor.   Pokud máte pět aplikací, každé ve dvou oblastech, doporučujeme vám, aby každý z nich měl 10 trezorů, které obsahují jedinečné tajné údaje pro aplikaci a oblast.  Limit pro všechny typy transakcí v rámci celého předplatného je pětkrát omezením každého trezoru klíčů. Například modul HSM – ostatní transakce v rámci předplatného jsou omezeny na 5 000 transakcí za 10 sekund na jedno předplatné. Zvažte uložení tajného klíče do mezipaměti v rámci služby nebo aplikace, abyste snížili RPS přímo do trezoru klíčů nebo provozování provozu na bázi shlukování.  Můžete také rozdělit přenosy mezi různými oblastmi, abyste minimalizovali latenci a používali jiné předplatné nebo trezor.  Neodesílejte Key Vault službě v jedné oblasti Azure více než limit předplatného.
1. Ukládání tajných kódů do mezipaměti, které načtete z Azure Key Vault paměti, a kdykoli je to možné, opakované použití z paměti.  Znovu se Přečtěte z Azure Key Vault jenom v případě, že kopírování v mezipaměti přestane fungovat (např. protože se vytočilo ve zdroji). 
1. Key Vault je navržená pro vaše tajná klíčová služba.   Pokud ukládáte tajné kódy zákazníků (zejména pro scénáře úložiště klíčů s vysokou propustností), zvažte vložení klíčů do databáze nebo účtu úložiště s šifrováním a uložení pouze hlavního klíče do Azure Key Vault.
1. Šifrování, zabalení a ověření operací s veřejným klíčem se dá provádět bez přístupu k Key Vault, což snižuje riziko omezování, ale také zlepšuje spolehlivost (Pokud jste správně zaznamenali ukládání materiálu veřejných klíčů do mezipaměti).
1. Pokud použijete Key Vault k uložení přihlašovacích údajů pro službu, ověřte, zda tato služba podporuje ověřování AAD přímo pro ověřování. Tím se snižuje zatížení Key Vault, zlepšuje spolehlivost a zjednodušuje váš kód, protože Key Vault nyní může použít token AAD.  Mnohé služby se přesunuly na použití ověřování AAD.  Podívejte se na aktuální seznam [služeb, které podporují spravované identity pro prostředky Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources).
1. Zvažte rovnoměrné rozložení zátěže nebo nasazení během delší doby, aby bylo možné zůstat v rámci současných RPS limitů.
1. Pokud vaše aplikace obsahuje více uzlů, které potřebují přečtení stejných tajných kódů, zvažte použití vzorku ventilátoru, kde jedna entita přečte tajný klíč z Key Vault a ventilátory pro všechny uzly.   Ukládat načtené tajné klíče do mezipaměti pouze v paměti.
Pokud zjistíte, že výše uvedené pořád ještě nesplňuje vaše požadavky, vyplňte prosím níže uvedenou tabulku a kontaktujte nás, abyste zjistili, jakou další kapacitu je možné přidat (například níže pro ilustrativní účely).

| Název trezoru | Oblast trezoru | Typ objektu (tajný klíč, klíč nebo certifikát) | Operace * | Typ kódu | Délka klíče nebo křivka | Klíč HSM?| Je potřeba RPS stabilního stavu. | Požadovaná špička RPS |
|--|--|--|--|--|--|--|--|--|
| https://mykeyvault.vault.azure.net/ | | Klíč | Podepsat | EC | P-256 | Ne | 200 | 1 000 |

Úplný seznam možných hodnot \* najdete v tématu [Azure Key Vault operace](/rest/api/keyvault/key-operations).

Pokud je další kapacita schválena, pamatujte na to, že v důsledku zvýšení kapacity se zvyšuje následující:
1. Změny modelu konzistence dat. Jakmile je trezor povolený v seznamu s další kapacitou propustnosti, konzistence dat Key Vault služby garantuje změny (nutné pro splnění vyššího objemu RPS, protože základní Azure Storage služba nemůže zůstat zapnutá).  V kostce:
  1. **Bez povoleného výpisu**: služba Key Vault bude odpovídat výsledkům operace zápisu (např. SecretSet, CreateKey) hned v následných voláních (např. SecretGet, symbol.
  1. **Při povoleném výpisu**: služba Key Vault odráží výsledky operace zápisu (např. SecretSet, CreateKey) během následujících volání během 60 sekund (např. SecretGet, symbol.
1. Kód klienta musí dodržovat zásady pro obnovení po 429 opakování. Klientský kód, který volá službu Key Vault, nesmí okamžitě opakovat Key Vault žádosti, když obdrží kód odpovědi 429.  Průvodce omezením Azure Key Vault publikovaný tady doporučuje použití exponenciálního omezení rychlostiu při příjmu kódu odpovědi HTTP 429.

Pokud budete mít obchodní případ pro vyšší omezení limity, kontaktujte nás prosím.

## <a name="how-to-throttle-your-app-in-response-to-service-limits"></a>Omezení vaší aplikace v reakci na omezení služby

Níže jsou uvedené **osvědčené postupy** , které byste měli implementovat, když je vaše služba omezená:
- Snížit počet operací na požadavek.
- Snižte frekvenci požadavků.
- Vyhněte se okamžité opakování. 
    - Všechny požadavky kumulovat proti vaší limity využití.

Při implementaci vaší aplikace pro zpracování chyb, použijte k detekci potřebu omezování na straně klienta kód chyby protokolu HTTP 429. Pokud požadavek selže s kódem chyby HTTP 429 znovu, že máte stále omezení služby Azure. Dál používat doporučené straně klienta omezování metoda, opakování žádosti, dokud nebude úspěšný.

Kód, který implementuje exponenciální regresí je uveden níže. 
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


Použití tohoto kódu v klientské C# aplikaci je jednoduché. 

### <a name="recommended-client-side-throttling-method"></a>Doporučenou metodou omezování na straně klienta

Na kód chyby protokolu HTTP 429 začněte omezování klienta pomocí exponenciálního omezení rychlosti přístupu:

1. Počkejte 1 sekundu, opakování žádosti
2. Pokud i nadále omezený počkejte 2 sekundy, zkuste žádost
3. Pokud i nadále omezený čekání 4 sekundy opakujte požadavek
4. Pokud i nadále omezený čekání 8 sekund opakujte požadavek
5. Pokud i nadále omezený čekání 16 sekund opakujte požadavek

V tomto okamžiku jste by neměly zobrazovat kódy odpovědí protokolu HTTP 429.

## <a name="see-also"></a>Další informace najdete v tématech

Lepší orientaci omezení využití sítě v cloudu Microsoftu, najdete v části [model omezení využití sítě](https://docs.microsoft.com/azure/architecture/patterns/throttling).

