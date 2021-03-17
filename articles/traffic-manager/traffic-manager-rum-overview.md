---
title: Měření reálných uživatelů v Azure Traffic Manager
description: V tomto úvodu se dozvíte, jak Azure Traffic Manager Měření reálných uživatelů fungovat.
services: traffic-manager
documentationcenter: traffic-manager
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: duau
ms.custom: ''
ms.openlocfilehash: 618f8fff532da0f6ae315ad9e4cda35a289949d1
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2021
ms.locfileid: "98183706"
---
# <a name="traffic-manager-real-user-measurements-overview"></a>Přehled funkce Měření reálných uživatelů Traffic Manageru

Když nastavíte profil Traffic Manager pro použití metody směrování výkonu, služba vyhledá, kde požadavky na dotaz DNS přicházejí z, a provede rozhodnutí o směrování, aby tyto žadatele nasměrovala do oblasti Azure, která jim poskytne nejnižší latenci. K tomu je možné využít inteligentní informace o latenci sítě, které Traffic Manager udržuje pro různé sítě koncových uživatelů.

Měření reálných uživatelů vám umožní měřit měření latence sítě do oblastí Azure, od klientských aplikací, které používají koncoví uživatelé, a Traffic Manager tyto informace při rozhodování o směrování vzít v úvahu. Když zvolíte použití Měření reálných uživatelů, můžete zvýšit přesnost směrování pro požadavky přicházející z těchto sítí, kde se nacházejí koncoví uživatelé. 

## <a name="how-real-user-measurements-work"></a>Jak Měření reálných uživatelů pracovat

Měření reálných uživatelů fungují tak, že klientské aplikace měří latenci do oblastí Azure, protože jsou vidět z sítí koncových uživatelů, ve kterých se používají. Pokud máte například webovou stránku, ke které mají přístup uživatelé v různých umístěních (například v oblastech Severní Ameriky), můžete použít Měření reálných uživatelů s metodou směrování výkonu, abyste je získali do nejlepší oblasti Azure, ve které je vaše serverová aplikace hostovaná.

Začíná vložením JavaScriptu poskytnutého v Azure (s jedinečným klíčem) na webových stránkách. Až to uděláte, pokaždé, když uživatel navštíví tuto webovou stránku, se v JavaScriptu dotazuje Traffic Manager k získání informací o oblastech Azure, které by měla změřit. Služba vrátí sadu koncových bodů ke skriptu, který pak tyto oblasti měří po sobě, a to stažením jediného pixelu, který je hostovaný v těchto oblastech Azure, a zaznamená latenci mezi okamžikem odeslání žádosti a časem přijetí prvního bajtu. Tato měření se pak nahlásí zpátky do služby Traffic Manager.

V průběhu času se to děje mnohokrát a v mnoha sítích Traffic Manager získat přesnější informace o vlastnostech latence sítí, ve kterých se nacházejí koncoví uživatelé. Tyto informace začnou zahrnovat rozhodnutí o směrování, která provedla Traffic Manager. Výsledkem je, že vede k vyšší přesnosti v těchto rozhodnutích na základě odeslaného Měření reálných uživatelů.

Při použití Měření reálných uživatelů se fakturuje na základě počtu měření odesílaných do Traffic Manager. Další podrobnosti o cenách najdete na [stránce s cenami Traffic Manager](https://azure.microsoft.com/pricing/details/traffic-manager/).

## <a name="faqs"></a>Nejčastější dotazy

* [Jaké jsou výhody použití Měření reálných uživatelů?](./traffic-manager-faqs.md#what-are-the-benefits-of-using-real-user-measurements)

* [Můžu Měření reálných uživatelů používat s oblastmi mimo Azure?](./traffic-manager-faqs.md#can-i-use-real-user-measurements-with-non-azure-regions)

* [Jakou metodu směrování přináší výhody Měření reálných uživatelů?](./traffic-manager-faqs.md#which-routing-method-benefits-from-real-user-measurements)

* [Potřebuji Měření reálných uživatelů každý profil samostatně?](./traffic-manager-faqs.md#do-i-need-to-enable-real-user-measurements-each-profile-separately)

* [Návody vypnout Měření reálných uživatelů pro moje předplatné?](./traffic-manager-faqs.md#how-do-i-turn-off-real-user-measurements-for-my-subscription)

* [Můžu Měření reálných uživatelů použít s klientskými aplikacemi jinými než webovými stránkami?](./traffic-manager-faqs.md#can-i-use-real-user-measurements-with-client-applications-other-than-web-pages)

* [Kolik měření se provádí pokaždé, když se vykreslí webová stránka s povoleným Měření reálných uživatelů?](./traffic-manager-faqs.md#how-many-measurements-are-made-each-time-my-real-user-measurements-enabled-web-page-is-rendered)

* [Je zpoždění před spuštěním skriptu Měření reálných uživatelů na naší webové stránce?](./traffic-manager-faqs.md#is-there-a-delay-before-real-user-measurements-script-runs-in-my-webpage)

* [Můžu použít Měření reálných uživatelů jenom v oblastech Azure, které chci změřit?](./traffic-manager-faqs.md#can-i-use-real-user-measurements-with-only-the-azure-regions-i-want-to-measure)

* [Můžu omezit počet provedených měření na konkrétní číslo?](./traffic-manager-faqs.md#can-i-limit-the-number-of-measurements-made-to-a-specific-number)

* [Můžu zobrazit měření prováděná klientskou aplikací jako součást Měření reálných uživatelů?](./traffic-manager-faqs.md#can-i-see-the-measurements-taken-by-my-client-application-as-part-of-real-user-measurements)

* [Můžu změnit skript měření, který poskytuje Traffic Manager?](./traffic-manager-faqs.md#can-i-modify-the-measurement-script-provided-by-traffic-manager)

* [Bude možné, že ostatní uživatelé uvidí klíč, který používá Měření reálných uživatelů?](./traffic-manager-faqs.md#will-it-be-possible-for-others-to-see-the-key-i-use-with-real-user-measurements)

* [Můžou jiné zneužít svůj klíč rumu?](./traffic-manager-faqs.md#can-others-abuse-my-rum-key)

* [Potřebuji do všech mých webových stránek přidat měření JavaScriptu?](./traffic-manager-faqs.md#do-i-need-to-put-the-measurement-javascript-in-all-my-web-pages)

* [Můžou se informace o mých koncových uživatelích identifikovat pomocí Traffic Manager, když používám Měření reálných uživatelů?](./traffic-manager-faqs.md#can-information-about-my-end-users-be-identified-by-traffic-manager-if-i-use-real-user-measurements)

* [Musí webová stránka měřící Měření reálných uživatelů používat Traffic Manager pro směrování?](./traffic-manager-faqs.md#does-the-webpage-measuring-real-user-measurements-need-to-be-using-traffic-manager-for-routing)

* [Potřebuji pro použití s Měření reálných uživatelů hostovat jakoukoli službu v oblastech Azure?](./traffic-manager-faqs.md#do-i-need-to-host-any-service-on-azure-regions-to-use-with-real-user-measurements)

* [Zvýší se využití šířky pásma Azure při použití Měření reálných uživatelů?](./traffic-manager-faqs.md#will-my-azure-bandwidth-usage-increase-when-i-use-real-user-measurements)

## <a name="next-steps"></a>Další kroky
- Naučte se používat [měření reálných uživatelů s webovými stránkami](traffic-manager-create-rum-web-pages.md)
- Informace [o tom, jak Traffic Manager funguje](traffic-manager-overview.md)
- Další informace o [Mobile Center](/mobile-center/)
- Další informace o [metodách směrování provozu](traffic-manager-routing-methods.md) , které podporuje Traffic Manager
- Informace o tom, jak [vytvořit profil Traffic Manager](./quickstart-create-traffic-manager-profile.md)