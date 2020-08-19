---
title: Řešení potíží se zásadami přístupu ke službě Azure Key trezor
description: Řešení potíží se zásadami přístupu ke službě Azure Key trezor
author: sebansal
ms.author: sebansal
ms.date: 08/10/2020
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.openlocfilehash: f31782fb4e91b72f51d6f0550fe9010cba7ca3d6
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88585453"
---
# <a name="troubleshooting-azure-key-vault-access-policy-issues"></a>Řešení potíží se zásadami přístupu ke službě Azure Key trezor

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="how-can-i-identify-how-and-when-key-vaults-are-accessed"></a>Jak zjistím, jak a kdy jsou trezory klíčů k dispozici?
Po vytvoření jednoho nebo více trezorů klíčů budete pravděpodobně chtít sledovat, jak a kdy jsou k vašim trezorům klíčů přistupované a kým. Můžete to udělat tak, že povolíte protokolování pro Azure Key Vault. podrobné pokyny k povolení protokolování najdete v tématu [Další informace](https://docs.microsoft.com/azure/key-vault/general/logging).

### <a name="how-can-i-monitor-vault-availability-service-latency-periods-or-other-performance-metrics-for-key-vault"></a>Jak můžu monitorovat dostupnost trezoru, doby latence služby nebo další metriky výkonu pro Trezor klíčů?
Po zahájení škálování služby se zvýší počet požadavků odeslaných do trezoru klíčů. To má potenciál na zvýšení latence vašich požadavků a v extrémních případech způsobit omezení požadavků, což ovlivní výkon vaší služby. Můžete monitorovat metriky výkonu trezoru klíčů a získat výstrahy pro konkrétní prahové hodnoty. podrobné pokyny ke konfiguraci monitorování najdete v [článku Další informace](https://docs.microsoft.com/azure/key-vault/general/alert).

### <a name="how-can-i-assign-access-control-per-key-vault-object"></a>Jak můžu přiřazovat řízení přístupu na objekt trezoru klíčů? 
K dostupnosti funkce řízení přístupu na základě tajného kódu/klíče/certifikátu se zobrazí oznámení, [Další informace](https://feedback.azure.com/forums/906355-azure-key-vault/suggestions/.32213176-per-secret-key-certificate-access-control)

### <a name="how-can-i-provide-key-vault-authenticate-using-access-control-policy"></a>Jak můžu zajistit ověřování trezoru klíčů pomocí zásad řízení přístupu?
Nejjednodušší způsob, jak ověřit cloudovou aplikaci, která je Key Vault, je spravovaná identita; Podrobnosti najdete v tématu [použití spravované identity App Service pro přístup k Azure Key Vault]( https://docs.microsoft.com/azure/key-vault/general/managed-identity) .
Pokud vytváříte aplikaci Prem, provádíte místní vývoj nebo pokud nemůžete použít spravovanou identitu, můžete místo toho zaregistrovat instanční objekt ručně a poskytnout přístup k trezoru klíčů pomocí zásad řízení přístupu. [Další informace](https://docs.microsoft.com/azure/key-vault/general/group-permissions-for-apps)najdete v tématu.


### <a name="how-can-i-give-the-ad-group-access-to-the-key-vault"></a>Jak dá skupině AD přístup k trezoru klíčů?
Udělte skupině AD oprávnění k vašemu trezoru klíčů pomocí rozhraní příkazového řádku Azure CLI AZ Key trezor set-Policy nebo rutiny Azure PowerShell set-AzKeyVaultAccessPolicy. Příklady najdete [v části poskytnutí aplikace, skupiny Azure AD nebo přístupu uživatele k vašemu trezoru klíčů](https://docs.microsoft.com/azure/key-vault/general/group-permissions-for-apps#give-the-principal-access-to-your-key-vault).

Aplikace taky potřebuje alespoň jednu roli správy identit a přístupu (IAM) přiřazenou k trezoru klíčů. V opačném případě se nebude moci přihlásit a nezdaří se jim nedostatečná oprávnění pro přístup k předplatnému. Skupiny Azure AD se spravovanými identitami můžou vyžadovat až 8hr k aktualizaci tokenu a začnou platit.

### <a name="how-can-i-redeploy-key-vault-with-arm-template-without-deleting-existing-access-policies"></a>Jak můžu znovu nasadit Key Vault se šablonou ARM, aniž byste museli odstraňovat existující zásady přístupu?
V současné době Key Vault ARM redopleyment odstraní všechny zásady přístupu v Key Vault a nahradí je zásadami přístupu v šabloně ARM. Pro zásady přístupu Key Vault neexistuje žádná přírůstková možnost. Pokud chcete zachovat zásady přístupu v Key Vault potřebujete číst stávající zásady přístupu v Key Vault a naplnit šablonu ARM pomocí těchto zásad, aby nedocházelo k výpadkům přístupu.

### <a name="recommended-troubleshooting-steps-for-following-error-types"></a>Doporučené kroky pro řešení potíží pro následující typy chyb
* HTTP 401: neověřený požadavek – [Postup řešení potíží](https://docs.microsoft.com/azure/key-vault/general/rest-error-codes#http-401-unauthenticated-request)
* HTTP 403: nedostatečná oprávnění – [Postup řešení potíží](https://docs.microsoft.com/azure/key-vault/general/rest-error-codes#http-403-insufficient-permissions)
* HTTP 429: příliš mnoho požadavků – [Postup řešení potíží](https://docs.microsoft.com/azure/key-vault/general/rest-error-codes#http-429-too-many-requests)
* Zkontrolujte, jestli k trezoru klíčů máte přístupová oprávnění k odstranění: [Zásady přístupu služby Key Vault](https://docs.microsoft.com/azure/key-vault/general/group-permissions-for-apps)
* Pokud máte problém s ověřením v trezoru klíčů prostřednictvím kódu, použijte [sadu SDK pro ověřování](https://azure.github.io/azure-sdk/posts/2020-02-25/defaultazurecredentials.html).

### <a name="what-are-the-best-practices-i-should-implement-when-key-vault-is-getting-throttled"></a>Jaké jsou osvědčené postupy, které by měly být implementovány, když se omezuje Trezor klíčů?
Postupujte podle osvědčených postupů popsaných [tady](https://docs.microsoft.com/azure/key-vault/general/overview-throttling#how-to-throttle-your-app-in-response-to-service-limits) .

## <a name="next-steps"></a>Další kroky

Naučte se řešit chyby ověřování trezoru klíčů. [Průvodce odstraňováním potíží s Key Vault](https://docs.microsoft.com/azure/key-vault/general/rest-error-codes)
