---
title: Řešení potíží se zásadami přístupu ke službě Azure Key trezor
description: Řešení potíží se zásadami přístupu ke službě Azure Key trezor
author: sebansal
ms.author: sebansal
ms.date: 08/10/2020
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.openlocfilehash: 155837802bd19ec1bb4e41484e229e1f5daef658
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2020
ms.locfileid: "92125248"
---
# <a name="troubleshooting-azure-key-vault-access-policy-issues"></a>Řešení potíží se zásadami přístupu ke službě Azure Key trezor

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="how-can-i-identify-how-and-when-key-vaults-are-accessed"></a>Jak zjistím, jak a kdy jsou trezory klíčů k dispozici?

Po vytvoření jednoho nebo více trezorů klíčů budete pravděpodobně chtít sledovat, jak a kdy jsou k vašim trezorům klíčů přistupované a kým. Monitorování můžete provádět povolením protokolování pro Azure Key Vault, pro podrobného průvodce pro povolení protokolování, [Další informace](https://docs.microsoft.com/azure/key-vault/general/logging).

### <a name="how-can-i-monitor-vault-availability-service-latency-periods-or-other-performance-metrics-for-key-vault"></a>Jak můžu monitorovat dostupnost trezoru, doby latence služby nebo další metriky výkonu pro Trezor klíčů?

Když začnete škálovat službu, bude se vyvolávat počet požadavků odeslaných do trezoru klíčů. Taková poptávka má potenciál na zvýšení latence vašich požadavků a v extrémních případech způsobí, že vaše požadavky budou omezené, což bude mít vliv na výkon vaší služby. Můžete monitorovat metriky výkonu trezoru klíčů a získat výstrahy pro konkrétní prahové hodnoty. podrobné pokyny ke konfiguraci monitorování najdete v [článku Další informace](https://docs.microsoft.com/azure/key-vault/general/alert).

### <a name="i-am-not-able-to-modify-access-policy-how-can-it-be-enabled"></a>Nedaří se mi změnit zásady přístupu, jak je možné povolit?
Uživatel musí mít dostatečná oprávnění AAD pro úpravu zásad přístupu. V takovém případě musí mít uživatel vyšší roli přispěvatele.

### <a name="i-am-seeing-unkwown-policy-error-what-does-that-mean"></a>Zobrazuje se chyba zásady Unkwown Co to znamená?
Existují dvě různé možnosti zobrazení zásad přístupu v neznámém oddílu:
* Může se jednat o předchozího uživatele, který měl přístup, a z nějakého důvodu, že uživatel neexistuje.
* Pokud se zásady přístupu přidají přes PowerShell a přidají se zásady přístupu pro identifikátor objectID aplikace místo priciple služby.

### <a name="how-can-i-assign-access-control-per-key-vault-object"></a>Jak můžu přiřazovat řízení přístupu na objekt trezoru klíčů? 

K dostupnosti funkce řízení přístupu na základě tajného kódu/klíče/certifikátu se zobrazí oznámení, [Další informace](https://feedback.azure.com/forums/906355-azure-key-vault/suggestions/32213176-per-secret-key-certificate-access-control)

### <a name="how-can-i-provide-key-vault-authenticate-using-access-control-policy"></a>Jak můžu zajistit ověřování trezoru klíčů pomocí zásad řízení přístupu?

Nejjednodušší způsob, jak ověřit cloudovou aplikaci, která je Key Vault, je spravovaná identita; Podrobnosti najdete v tématu [ověření pro Azure Key Vault](authentication.md) .
Pokud vytváříte aplikaci Prem, provedete místní vývoj nebo jinak nemůžete použít spravovanou identitu, můžete místo toho zaregistrovat instanční objekt ručně a poskytnout přístup k trezoru klíčů pomocí zásad řízení přístupu. Viz [přiřazení zásad řízení přístupu](assign-access-policy-portal.md).

### <a name="how-can-i-give-the-ad-group-access-to-the-key-vault"></a>Jak dá skupině AD přístup k trezoru klíčů?

Udělte skupině AD oprávnění k vašemu trezoru klíčů pomocí příkazu Azure CLI `az keyvault set-policy` nebo rutiny Azure PowerShell Set-AzKeyVaultAccessPolicy. Viz [přiřazení zásad přístupu – rozhraní příkazového řádku](assign-access-policy-cli.md) a [přiřazení zásad přístupu – PowerShell](assign-access-policy-powershell.md).

Aplikace taky potřebuje alespoň jednu roli správy identit a přístupu (IAM) přiřazenou k trezoru klíčů. V opačném případě se nebude moci přihlásit a nezdaří se jim nedostatečná oprávnění pro přístup k předplatnému. U skupin Azure AD se spravovanými identitami může trvat až osm hodin, než se aktualizují tokeny a začnou platit.

### <a name="how-can-i-redeploy-key-vault-with-arm-template-without-deleting-existing-access-policies"></a>Jak můžu znovu nasadit Key Vault se šablonou ARM, aniž byste museli odstraňovat existující zásady přístupu?

V současné době Key Vault opětovné nasazení odstraní všechny zásady přístupu v Key Vault a nahradí je zásadami přístupu v šabloně ARM. Pro zásady přístupu Key Vault neexistuje žádná přírůstková možnost. Pokud chcete zachovat zásady přístupu v Key Vault, musíte si přečíst existující zásady přístupu v Key Vault a naplnit šablonu ARM pomocí těchto zásad, abyste se vyhnuli výpadkům v přístupu.

Další možností, která může pomoci pro tento scénář, je použití rolí RBAC jako alternativního přístupu k zásadám přístupu. Pomocí RBAC můžete znovu nasadit Trezor klíčů bez zadání zásad. Toto řešení si můžete přečíst [tady](https://docs.microsoft.com/azure/key-vault/general/rbac-guide).

### <a name="recommended-troubleshooting-steps-for-following-error-types"></a>Doporučené kroky pro řešení potíží pro následující typy chyb

* HTTP 401: Neověřený požadavek – [Postup při řešení potíží](rest-error-codes.md#http-401-unauthenticated-request)
* HTTP 403: Nedostatečná oprávnění – [Postup při řešení potíží](rest-error-codes.md#http-403-insufficient-permissions)
* HTTP 429: Příliš mnoho požadavků – [Postup při řešení potíží](rest-error-codes.md#http-429-too-many-requests)
* Zkontrolujte, jestli máte oprávnění k odstranění přístupu ke trezoru klíčů: Přečtěte si téma [přiřazení zásad přístupu – CLI](assign-access-policy-cli.md), [přiřazení zásad přístupu – PowerShell](assign-access-policy-powershell.md)nebo [přiřazení zásad přístupu – portál](assign-access-policy-portal.md).
* Pokud máte problém s ověřením v trezoru klíčů prostřednictvím kódu, použijte [sadu SDK pro ověřování](https://azure.github.io/azure-sdk/posts/2020-02-25/defaultazurecredentials.html).

### <a name="what-are-the-best-practices-i-should-implement-when-key-vault-is-getting-throttled"></a>Jaké jsou osvědčené postupy, které by měly být implementovány, když se omezuje Trezor klíčů?
Postupujte podle osvědčených postupů popsaných [tady](overview-throttling.md#how-to-throttle-your-app-in-response-to-service-limits) .

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak řešit potíže s ověřováním trezoru klíčů: [Key Vault Průvodce odstraňováním potíží](rest-error-codes.md).
