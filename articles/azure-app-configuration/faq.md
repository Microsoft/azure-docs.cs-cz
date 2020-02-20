---
title: Nejčastější dotazy ke konfiguraci aplikací Azure | Microsoft Docs
description: Nejčastější dotazy týkající se konfigurace aplikací Azure
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: 4bcd8f042563fa381832fd629061a822f71e844a
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2020
ms.locfileid: "77467585"
---
# <a name="azure-app-configuration-faq"></a>Nejčastější dotazy ke konfiguraci aplikací Azure

Tento článek obsahuje nejčastější dotazy týkající se konfigurace aplikací Azure.

## <a name="how-is-app-configuration-different-from-azure-key-vault"></a>Jak se liší konfigurace aplikace od Azure Key Vault?

Konfigurace aplikací je navržená pro odlišnou sadu případů použití: pomáhá vývojářům spravovat nastavení aplikace a dostupnost funkcí řízení. Cílem je zjednodušit mnoho úloh práce se složitými konfiguračními daty.

Konfigurace aplikací podporuje:

- Hierarchické obory názvů
- označování
- Rozsáhlé dotazy
- Dávkové načítání
- Specializované operace správy
- Uživatelské rozhraní pro správu funkcí

Konfigurace aplikace je doplňkem Key Vault a obě by se měly používat souběžně ve většině nasazení aplikace.

## <a name="should-i-store-secrets-in-app-configuration"></a>Mám tajné klíče ukládat do konfigurace aplikace?

I když konfigurace aplikace poskytuje zesílené zabezpečení, Key Vault je stále nejlepším místem pro ukládání tajných klíčů aplikací. Key Vault poskytuje šifrování na úrovni hardwaru, podrobné zásady přístupu a operace správy, jako je například rotace certifikátu.

Můžete vytvořit konfigurační hodnoty aplikace, které odkazují na tajné kódy uložené v Key Vault. Další informace najdete v tématu [použití Key Vault odkazů v aplikaci ASP.NET Core](./use-key-vault-references-dotnet-core.md).

## <a name="does-app-configuration-encrypt-my-data"></a>Šifruje konfigurace aplikace moje data?

Ano. Konfigurace aplikace šifruje všechny hodnoty klíčů, které obsahuje, a šifruje síťovou komunikaci. Názvy klíčů a popisky se používají jako indexy pro načítání konfiguračních dat a nejsou zašifrované.

## <a name="how-is-app-configuration-different-from-azure-app-service-settings"></a>Jak se liší konfigurace aplikace od nastavení Azure App Service?

Azure App Service umožňuje definovat nastavení aplikace pro každou instanci App Service. Tato nastavení jsou předána jako proměnné prostředí do kódu aplikace. Pokud chcete, můžete přiřadit nastavení k určitému slotu nasazení. Další informace najdete v tématu [Konfigurace nastavení aplikace](/azure/app-service/configure-common#configure-app-settings).

Naproti tomu konfigurace aplikací Azure umožňuje definovat nastavení, která se dají sdílet mezi více aplikacemi. To zahrnuje aplikace běžící v App Service i jiné platformy. Kód aplikace přistupuje k těmto nastavením prostřednictvím zprostředkovatelů konfigurace pro .NET a Java, prostřednictvím sady Azure SDK nebo přímo přes rozhraní REST API.

Můžete také importovat a exportovat nastavení mezi App Service a konfigurací aplikace. Tato možnost umožňuje rychle nastavit nové úložiště konfigurace aplikace na základě stávajících nastavení App Service. Konfiguraci můžete sdílet taky s existující aplikací, která spoléhá na nastavení App Service.

## <a name="are-there-any-size-limitations-on-keys-and-values-stored-in-app-configuration"></a>Existují nějaká omezení velikosti klíčů a hodnot uložených v konfiguraci aplikace?

Pro jednu položku klíč-hodnota je povolený limit 10 KB.

## <a name="how-should-i-store-configurations-for-multiple-environments-test-staging-production-and-so-on"></a>Jak mám ukládat konfigurace pro více prostředí (test, fázování, produkce atd.)?

V současné době určujete, kdo má přístup ke konfiguraci aplikace na úrovni na úrovni úložiště. Pro každé prostředí, které vyžaduje různá oprávnění, použijte samostatné úložiště. Tento přístup vám dává nejlepší izolaci zabezpečení.

## <a name="what-are-the-recommended-ways-to-use-app-configuration"></a>Jaké jsou doporučené způsoby použití konfigurace aplikace?

Viz [osvědčené postupy](./howto-best-practices.md).

## <a name="how-much-does-app-configuration-cost"></a>Kolik stojí konfigurace aplikace?

Existují dvě cenové úrovně: 1) úroveň Free a 2) úroveň Standard.

Pokud jste úložiště vytvořili před zavedením úrovně Standard, při obecné dostupnosti se automaticky přesune na úroveň Free. Můžete vybrat, jestli chcete upgradovat na úroveň Standard, nebo zůstat na úrovni Free, pokud to vyhovuje vašim potřebám.

## <a name="which-app-configuration-tier-should-i-use"></a>Jakou úroveň konfigurace aplikace mám použít?

Obě úrovně konfigurace aplikací nabízejí základní funkce, včetně nastavení konfigurace, příznaků funkcí, Key Vault odkazů, základních operací správy, metrik a protokolů.

Níže jsou uvedeny požadavky pro výběr vrstvy.

- **Prostředky na předplatné**: prostředek se skládá z jednoho úložiště konfigurace. Každé předplatné je omezené na jedno úložiště konfigurace v bezplatné úrovni. Předplatná můžou mít neomezený počet úložišť konfigurace na úrovni Standard.
- **Úložiště na prostředek**: na úrovni Free je každé úložiště konfigurace omezené na 10 MB úložiště. Na úrovni Standard může každé úložiště konfigurace využívat až 1 GB úložiště.
- **Historie klíčů**: Konfigurace aplikace ukládá historii všech změn provedených v klíčích. Na úrovni Free je tato historie uložená po dobu sedmi dní. Na úrovni Standard se tato historie ukládá po dobu 30 dnů.
- **Požadavky za den**: úložiště úrovně Free se omezuje na 1 000 požadavků za den. Jakmile obchod dosáhne 1 000 požadavků, vrátí stavový kód HTTP 429 pro všechny požadavky do půlnoci UTC.

    V případě obchodů úrovně Standard jsou první 200 000 žádosti každý den zahrnuté do denního poplatku. Další požadavky se účtují jako nadlimitní využití.

- **Smlouva o úrovni služeb**: úroveň Standard má smlouvu SLA s 99,9% dostupností. Úroveň Free nemá smlouvu SLA.
- **Funkce zabezpečení**: obě úrovně zahrnují základní funkce zabezpečení, včetně šifrování pomocí klíčů spravovaných Microsoftem, ověřování pomocí HMAC nebo Azure Active Directory, podpora RBAC a spravované identity. Úroveň Standard nabízí pokročilejší funkce zabezpečení, včetně podpory a šifrování privátních odkazů pomocí klíčů spravovaných zákazníkem.
- **Náklady**: obchody úrovně Standard mají denní poplatek za využití. Pro žádosti po denním přidělení se taky účtuje poplatky za nadlimitní využití. K používání úložiště úrovně Free se neúčtují žádné náklady.

## <a name="can-i-upgrade-a-store-from-the-free-tier-to-the-standard-tier-can-i-downgrade-a-store-from-the-standard-tier-to-the-free-tier"></a>Můžu upgradovat obchod z úrovně Free na úroveň Standard? Můžu z úrovně Standard snížit obchod na úroveň Free?

Můžete kdykoli upgradovat z úrovně Free na úroveň Standard.

Nemůžete downgradovat úložiště z úrovně Standard na úroveň Free. Můžete vytvořit nové úložiště na úrovni Free a pak [importovat konfigurační data do tohoto úložiště](howto-import-export-data.md).

## <a name="how-can-i-receive-announcements-on-new-releases-and-other-information-related-to-app-configuration"></a>Jak mohu dostávat oznámení o nových verzích a dalších informacích souvisejících s konfigurací aplikace?

Přihlaste se k odběru našeho [úložiště oznámení GitHubu](https://github.com/Azure/AppConfiguration-Announcements).

## <a name="how-can-i-report-an-issue-or-give-a-suggestion"></a>Jak mohu ohlásit problém nebo podat návrh?

Můžete nás kontaktovat přímo na [GitHubu](https://github.com/Azure/AppConfiguration/issues).

## <a name="next-steps"></a>Další kroky

* [Informace o konfiguraci aplikací Azure](./overview.md)
