---
title: Nejčastější dotazy ke konfiguraci aplikací Azure
description: Nejčastější dotazy týkající se konfigurace aplikací Azure
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: lcozzens
ms.openlocfilehash: 25187fd055f40e8b32d840ead2a9c54882446b88
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80348791"
---
# <a name="azure-app-configuration-faq"></a>Nejčastější dotazy ke konfiguraci aplikací Azure

Tento článek odpovídá na nejčastější dotazy týkající se konfigurace aplikací Azure.

## <a name="how-is-app-configuration-different-from-azure-key-vault"></a>Jak se konfigurace aplikací liší od Azure Key Vault?

Konfigurace aplikace pomáhá vývojářům spravovat nastavení aplikací a řídit dostupnost funkcí. Jeho cílem je zjednodušit mnoho úkolů práce se složitými konfiguračními daty.

Konfigurace aplikace podporuje:

- Hierarchické obory názvů
- Označování
- Rozsáhlé dotazy
- Dávkové načítání
- Specializované manažerské operace
- Uživatelské rozhraní pro správu funkcí

Konfigurace aplikace doplňuje trezor klíčů a tyto dvě by měly být používány vedle sebe ve většině nasazení aplikací.

## <a name="should-i-store-secrets-in-app-configuration"></a>Mám ukládat tajné klíče v konfiguraci aplikace?

Přestože konfigurace aplikace poskytuje posílené zabezpečení, trezor klíčů je stále nejlepším místem pro ukládání tajných kódů aplikací. Trezor klíčů poskytuje šifrování na úrovni hardwaru, zásady podrobného přístupu a operace správy, jako je například střídání certifikátů.

Můžete vytvořit hodnoty konfigurace aplikace, které odkazují na tajné klíče uložené v trezoru klíčů. Další informace najdete [v tématu Použití odkazů na trezor klíčů v aplikaci ASP.NET Core](./use-key-vault-references-dotnet-core.md).

## <a name="does-app-configuration-encrypt-my-data"></a>Šifruje konfigurace aplikace moje data?

Ano. Konfigurace aplikace šifruje všechny hodnoty klíče, které obsahuje, a šifruje síťovou komunikaci. Názvy klíčů a popisky se používají jako indexy pro načítání konfiguračních dat a nejsou šifrovány.

## <a name="how-is-app-configuration-different-from-azure-app-service-settings"></a>Jak se konfigurace aplikací liší od nastavení služby Azure App Service?

Azure App Service umožňuje definovat nastavení aplikace pro každou instanci služby App Service. Tato nastavení jsou předány jako proměnné prostředí do kódu aplikace. Pokud chcete, můžete přidružit nastavení ke konkrétnímu slotu nasazení. Další informace naleznete v [tématu Konfigurace nastavení aplikace](/azure/app-service/configure-common#configure-app-settings).

Naproti tomu Azure App Configuration umožňuje definovat nastavení, která lze sdílet mezi více aplikací. To zahrnuje aplikace spuštěné ve službě App Service a další platformy. Kód aplikace přistupuje k těmto nastavením prostřednictvím zprostředkovatelů konfigurace pro rozhraní .NET a Java, prostřednictvím sady Azure SDK nebo přímo prostřednictvím rozhraní REST API.

Můžete také importovat a exportovat nastavení mezi službou App Service a Konfigurací aplikace. Tato funkce umožňuje rychle nastavit nový obchod konfigurace aplikací na základě stávajících nastavení služby App Service. Můžete také sdílet konfiguraci s existující aplikací, která závisí na nastavení služby App Service.

## <a name="are-there-any-size-limitations-on-keys-and-values-stored-in-app-configuration"></a>Existují nějaká omezení velikosti klíčů a hodnot uložených v konfiguraci aplikace?

Pro jednu položku s hodnotou klíče je limit 10 KB.

## <a name="how-should-i-store-configurations-for-multiple-environments-test-staging-production-and-so-on"></a>Jak mám ukládat konfigurace pro více prostředí (test, pracovní, produkční a tak dále)?

Můžete určit, kdo má přístup ke konfiguraci aplikace na úrovni pro každý obchod. Pro každé prostředí, které vyžaduje různá oprávnění, použijte samostatné úložiště. Tento přístup poskytuje nejlepší izolaci zabezpečení.

Pokud nepotřebujete izolaci zabezpečení mezi prostředími, můžete použít popisky k rozlišení mezi hodnotami konfigurace. [Pomocí popisků povolit různé konfigurace pro různá prostředí](./howto-labels-aspnet-core.md) poskytuje úplný příklad.

## <a name="what-are-the-recommended-ways-to-use-app-configuration"></a>Jaké jsou doporučené způsoby použití konfigurace aplikace?

Podívejte se na [osvědčené postupy](./howto-best-practices.md).

## <a name="how-much-does-app-configuration-cost"></a>Kolik stojí konfigurace aplikace?

Existují dvě cenové úrovně:

- Úroveň Free
- Standardní úroveň.

Pokud jste vytvořili úložiště před zavedením úrovně Standard, automaticky se přesune do úrovně Free při obecné dostupnosti. Můžete si vybrat upgrade na úroveň Standard nebo zůstat na úrovni Free.

Úložiště nelze snížit na úroveň Standard na úroveň Free. Můžete vytvořit nové úložiště ve vrstvě Free a pak importovat konfigurační data do tohoto úložiště.

## <a name="which-app-configuration-tier-should-i-use"></a>Kterou úroveň konfigurace aplikace mám použít?

Obě úrovně konfigurace aplikace nabízejí základní funkce, včetně nastavení konfigurace, příznaků funkcí, odkazů trezoru klíčů, základních operací správy, metrik a protokolů.

Níže jsou uvedeny důležité informace pro výběr úrovně.

- **Prostředky na odběr**: Prostředek se skládá z jednoho úložiště konfigurace. Každé předplatné je omezeno na jedno úložiště konfigurace v bezplatné vrstvě. Předplatná může mít neomezený počet úložišť konfigurace ve standardní vrstvě.
- **Úložiště na prostředek**: V bezplatné vrstvě je každé úložiště konfigurace omezeno na 10 MB úložiště. Ve standardní vrstvě může každé úložiště konfigurace používat až 1 GB úložiště.
- **Historie klíčů**: Konfigurace aplikace ukládá historii všech změn provedených v klíčích. Ve volné vrstvě je tato historie uložena po dobu sedmi dnů. Ve standardní úrovni je tato historie uložena po dobu 30 dnů.
- **Požadavky na den:** Obchody s bezplatnými úrovněmi jsou omezeny na 1 000 požadavků za den. Jakmile úložiště dosáhne 1 000 požadavků, vrátí stavový kód HTTP 429 pro všechny požadavky až do půlnoci UTC.

    U obchodů se standardní úrovní je v denním poplatku zahrnuto prvních 200 000 požadavků každý den. Další požadavky se účtují jako nadbytku.

- **Smlouva o úrovni služeb**: Standardní úroveň má smlouvu SLA s 99,9% dostupností. Úroveň free nemá sla.
- **Funkce zabezpečení**: Obě vrstvy zahrnují základní funkce zabezpečení, včetně šifrování pomocí klíčů spravovaných microsoftem, ověřování pomocí HMAC nebo Azure Active Directory, podporu RBAC a spravovanou identitu. Úroveň Standard nabízí pokročilejší funkce zabezpečení, včetně podpory privátního spojení a šifrování pomocí klíčů spravovaných zákazníkem.
- **Cena:** Obchody se standardní úrovní mají poplatek za denní využití. Za žádosti po denním přidělování se účtuje také poplatek za nadbytk. Použití bezplatného obchodu úrovní není zdarma.

## <a name="can-i-upgrade-a-store-from-the-free-tier-to-the-standard-tier-can-i-downgrade-a-store-from-the-standard-tier-to-the-free-tier"></a>Můžu upgradovat úložiště z úrovně Free na úroveň Standard? Můžu snížit úroveň Standard z úrovně Standard na úroveň Free?

Z úrovně Free můžete kdykoli upgradovat na úroveň Standard.

Úložiště nelze snížit na úroveň Standard na úroveň Free. Můžete vytvořit nové úložiště ve vrstvě Free a pak [importovat konfigurační data do tohoto úložiště](howto-import-export-data.md).

## <a name="are-there-any-limits-on-the-number-of-requests-made-to-app-configuration"></a>Existují nějaká omezení počtu požadavků na konfiguraci aplikace?

Konfigurační obchody na úrovni Free jsou omezeny na 1 000 požadavků za den. Úložiště konfigurace na úrovni Standard může dojít k dočasné omezení při požadavek sazba překročí 20 000 požadavků za hodinu.

Když úložiště dosáhne svého limitu, vrátí stavový kód HTTP 429 pro všechny požadavky provedené do vypršení časového období. Záhlaví `retry-after-ms` v odpovědi poskytuje doporučenou čekací dobu (v milisekundách) před opakováním požadavku.

Pokud vaše aplikace pravidelně dochází http stavový kód 429 odpovědi, zvažte jeho přepracování snížit počet požadavků. Další informace naleznete v tématu [Reduce requests made to App Configuration](./howto-best-practices.md#reduce-requests-made-to-app-configuration)

## <a name="my-application-receives-http-status-code-429-responses-why"></a>Aplikace obdrží stavový kód HTTP 429 odpovědí. Proč?

Za těchto okolností obdržíte odpověď na stavový kód HTTP 429:

* Překročení denního limitu požadavků pro obchod na úrovni Free.
* Dočasné omezení z důvodu vysoké míry požadavků pro úložiště ve vrstvě Standard.
* Nadměrné využití šířky pásma.
* Pokus o vytvoření nebo úpravu klíče při překročení nabídky úložiště.

Zkontrolujte tělo 429 odpověď z konkrétního důvodu, proč se požadavek nezdařil.

## <a name="how-can-i-receive-announcements-on-new-releases-and-other-information-related-to-app-configuration"></a>Jak mohu dostávat oznámení o nových verzích a dalších informacích souvisejících s konfigurací aplikací?

Přihlaste se k odběru našeho [úložiště oznámení GitHub](https://github.com/Azure/AppConfiguration-Announcements).

## <a name="how-can-i-report-an-issue-or-give-a-suggestion"></a>Jak mohu nahlásit problém nebo podat návrh?

Můžete nás kontaktovat přímo na [GitHubu](https://github.com/Azure/AppConfiguration/issues).

## <a name="next-steps"></a>Další kroky

* [Služba Azure App Configuration](./overview.md)
