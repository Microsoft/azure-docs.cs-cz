---
title: Měření reálných uživatelů ve Správci provozu Azure
description: V tomto úvodu se dozvíte, jak azure traffic manager měření reálných uživatelů funguje.
services: traffic-manager
documentationcenter: traffic-manager
author: rohinkoul
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: rohink
ms.custom: ''
ms.openlocfilehash: e38e1255b1a84ab5d3fd37e16eb65c76001dbfa1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938437"
---
# <a name="traffic-manager-real-user-measurements-overview"></a>Přehled funkce Měření reálných uživatelů Traffic Manageru

Když nastavíte profil Traffic Manageru pro použití metody směrování výkonu, služba se podívá na to, odkud pocházejí požadavky na dotaz DNS, a rozhodne o směrování, aby tyto požadavky nasměrovala do oblasti Azure, která jim poskytuje nejnižší latenci. Toho lze dosáhnout využitím inteligentní latence sítě, kterou Traffic Manager udržuje pro různé sítě koncových uživatelů.

Měření reálných uživatelů umožňuje měřit měření latence sítě do oblastí Azure, z klientských aplikací, které používají vaši koncoví uživatelé, a mít Traffic Manager zvážit tyto informace také při rozhodování o směrování. Pokud se rozhodnete použít měření skutečných uživatelů, můžete zvýšit přesnost směrování požadavků přicházejících z těch sítí, kde jsou vaši koncoví uživatelé umístěni. 

## <a name="how-real-user-measurements-work"></a>Jak fungují měření reálných uživatelů

Měření reálných uživatelů fungují tak, že vaše klientské aplikace měří latenci do oblastí Azure, jak je to vidět ze sítí koncových uživatelů, ve kterých se používají. Máte-li například webovou stránku, ke které mají uživatelé přístup na různých místech (například v oblastech Severní Ameriky), můžete použít měření skutečných uživatelů s metodou směrování výkonu, abyste je dostali do nejlepší oblasti Azure, ve které je váš server aplikace je hostována.

Začíná vložením JavaScriptu do Azure (s jedinečným klíčem) do vašich webových stránek. Jakmile se tak stane, kdykoli uživatel navštíví tuto webovou stránku, JavaScript se dotazuje Traffic Managerzískat informace o oblastech Azure, které by měl měřit. Služba vrátí sadu koncových bodů do skriptu, který pak měří tyto oblasti postupně stažením jednoho obrazového bodu hostované v těchto oblastech Azure a s uvedením latence mezi časem odeslání požadavku a časem, kdy byl přijat první bajt . Tato měření jsou pak hlášeny zpět do služby Traffic Manager.

V průběhu času k tomu dochází mnohokrát a v mnoha sítích, což vede k tomu, že Traffic Manager získává přesnější informace o charakteristikách latence sítí, ve kterých jsou vaši koncoví uživatelé umístěni. Tyto informace se začnou začleovat do rozhodnutí o směrování učiněných traffic managerem. V důsledku toho vede ke zvýšení přesnosti těchto rozhodnutí na základě odeslaných měření skutečných uživatelů.

Při použití měření reálných uživatelů se vám bude účtovat na základě počtu měření odeslaných do Traffic Manageru. Další podrobnosti o cenách najdete na [stránce s cenami v Traffic Manageru](https://azure.microsoft.com/pricing/details/traffic-manager/).

## <a name="faqs"></a>Nejčastější dotazy

* [Jaké jsou výhody používání měření reálných uživatelů?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-are-the-benefits-of-using-real-user-measurements)

* [Můžu použít měření reálných uživatelů s oblastmi mimo Azure?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-real-user-measurements-with-non-azure-regions)

* [Která metoda směrování těží z měření reálných uživatelů?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#which-routing-method-benefits-from-real-user-measurements)

* [Musím povolit měření reálných uživatelů každý profil zvlášť?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#do-i-need-to-enable-real-user-measurements-each-profile-separately)

* [Jak vypnu měření reálných uživatelů pro své předplatné?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-do-i-turn-off-real-user-measurements-for-my-subscription)

* [Mohu použít měření reálných uživatelů s jinými klientskými aplikacemi než webovými stránkami?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-real-user-measurements-with-client-applications-other-than-web-pages)

* [Kolik měření se provádí pokaždé, když je vykreslena moje webová stránka s povolenými měřeními skutečných uživatelů?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-many-measurements-are-made-each-time-my-real-user-measurements-enabled-web-page-is-rendered)

* [Došlo ke zpoždění, než se na mé webové stránce spustí skript Měření skutečných uživatelů?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#is-there-a-delay-before-real-user-measurements-script-runs-in-my-webpage)

* [Můžu měření reálných uživatelů používat jenom s oblastmi Azure, které chci měřit?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-real-user-measurements-with-only-the-azure-regions-i-want-to-measure)

* [Mohu omezit počet měření provedených na konkrétní číslo?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-limit-the-number-of-measurements-made-to-a-specific-number)

* [Mohu vidět měření provedená klientskou aplikací v rámci měření reálných uživatelů?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-see-the-measurements-taken-by-my-client-application-as-part-of-real-user-measurements)

* [Mohu upravit měřicí skript poskytovaný traffic managerem?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-modify-the-measurement-script-provided-by-traffic-manager)

* [Bude možné, aby ostatní viděli klíč, který používám s měřením skutečných uživatelů?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#will-it-be-possible-for-others-to-see-the-key-i-use-with-real-user-measurements)

* [Mohou ostatní zneužívat můj klíč RUM?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-others-abuse-my-rum-key)

* [Musím měřicí JavaScript vložit na všechny své webové stránky?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#do-i-need-to-put-the-measurement-javascript-in-all-my-web-pages)

* [Mohou být informace o mých koncových uživatelích identifikovány traffic managerem, pokud používám měření reálných uživatelů?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-information-about-my-end-users-be-identified-by-traffic-manager-if-i-use-real-user-measurements)

* [Má webová stránka měření reálných uživatelských měření musí být pomocí Traffic Manager pro směrování?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#does-the-webpage-measuring-real-user-measurements-need-to-be-using-traffic-manager-for-routing)

* [Musím hostovat nějakou službu v oblastech Azure, abych ji mohl používat s měřením reálných uživatelů?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#do-i-need-to-host-any-service-on-azure-regions-to-use-with-real-user-measurements)

* [Zvýší se využití šířky pásma Azure, když použiju měření reálných uživatelů?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#will-my-azure-bandwidth-usage-increase-when-i-use-real-user-measurements)

## <a name="next-steps"></a>Další kroky
- Naučte se používat [měření reálných uživatelů s webovými stránkami](traffic-manager-create-rum-web-pages.md)
- [Zjistěte, jak Traffic Manager funguje](traffic-manager-overview.md)
- Další informace o [mobilním centru](https://docs.microsoft.com/mobile-center/)
- Další informace o [metodách směrování provozu](traffic-manager-routing-methods.md) podporovaných traffic managerem
- Přečtěte si, jak [vytvořit profil Traffic Manageru](traffic-manager-create-profile.md)

