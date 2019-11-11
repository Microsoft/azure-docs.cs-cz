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
ms.openlocfilehash: 8d286cbab33a1fb6a2d2a2cb70caed11b21af735
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2019
ms.locfileid: "73904093"
---
# <a name="azure-app-configuration-faq"></a>Nejčastější dotazy ke konfiguraci aplikací Azure

Tento článek obsahuje nejčastější dotazy týkající se konfigurace aplikací Azure.

## <a name="how-is-app-configuration-different-from-azure-key-vault"></a>V čem se App Configuration liší od služby Azure Key Vault?

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

Ano. Konfigurace aplikace šifruje všechny hodnoty klíčů, které obsahuje, a šifruje síťovou komunikaci. Názvy klíčů se používají jako indexy pro načítání konfiguračních dat a nejsou zašifrované.

## <a name="how-is-app-configuration-different-from-azure-app-service-settings"></a>Jak se liší konfigurace aplikace od nastavení Azure App Service?

Azure App Service umožňuje definovat nastavení aplikace pro každou instanci App Service. Tato nastavení jsou předána jako proměnné prostředí do kódu aplikace. V případě potřeby můžete přiřadit nastavení k určitému slotu nasazení. Další informace najdete v tématu [Konfigurace nastavení aplikace](/azure/app-service/configure-common#configure-app-settings).

Naproti tomu konfigurace aplikací Azure umožňuje definovat nastavení, která se dají sdílet mezi více aplikacemi, včetně aplikací běžících v App Service. K těmto nastavením lze v kódu aplikace získat pøístup prostřednictvím zprostředkovatelů konfigurace pro .NET a Java, prostřednictvím sady Azure SDK nebo přímo přes rozhraní REST API.

Můžete také importovat a exportovat nastavení mezi App Service a konfigurací aplikace. To vám umožní rychle nastavit nové úložiště konfigurace aplikace na základě stávajících nastavení App Service nebo snadno sdílet konfiguraci s existující aplikací, která spoléhá na nastavení App Service.

## <a name="are-there-any-size-limitations-on-keys-and-values-stored-in-app-configuration"></a>Existují nějaká omezení velikosti klíčů a hodnot uložených v konfiguraci aplikace?

Existuje limit 10 KB pro jednu položku klíč-hodnota.

## <a name="how-should-i-store-configurations-for-multiple-environments-test-staging-production-and-so-on"></a>Jak mám ukládat konfigurace pro více prostředí (test, fázování, produkce atd.)?

V současné době můžete řídit, kdo má přístup k konfiguraci aplikace na úrovni na úrovni úložiště. Pro každé prostředí, které vyžaduje různá oprávnění, použijte samostatné úložiště. Tento přístup vám dává nejlepší izolaci zabezpečení.

## <a name="what-are-the-recommended-ways-to-use-app-configuration"></a>Jaké jsou doporučené způsoby použití konfigurace aplikace?

Viz [osvědčené postupy](./howto-best-practices.md).

## <a name="how-much-does-app-configuration-cost"></a>Kolik stojí konfigurace aplikace?

Službu je možné používat v rámci verze Public Preview.

## <a name="how-can-i-receive-announcements-on-new-releases-and-other-information-related-to-app-configuration"></a>Jak mohu dostávat oznámení o nových verzích a dalších informacích souvisejících s konfigurací aplikace?

Přihlaste se k odběru našeho [úložiště oznámení GitHubu](https://github.com/Azure/AppConfiguration-Announcements).

## <a name="how-can-i-report-an-issue-or-give-a-suggestion"></a>Jak mohu ohlásit problém nebo podat návrh?

Můžete nás kontaktovat přímo na [GitHubu](https://github.com/Azure/AppConfiguration/issues).

## <a name="next-steps"></a>Další kroky

* [Informace o konfiguraci aplikací Azure](./overview.md)
