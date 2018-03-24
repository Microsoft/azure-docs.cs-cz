---
title: Měření reálný uživatel v Azure Traffic Manageru | Microsoft Docs
description: Úvod do měření reálný uživatel v Traffic Manageru
services: traffic-manager
documentationcenter: traffic-manager
author: KumudD
manager: timlt
editor: ''
tags: ''
ms.assetid: ''
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: 4e8d808d65c9898d230455d128e3ffc50db303d6
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="traffic-manager-real-user-measurements-overview"></a>Přehled reálného měření uživatele správce provozu

Při nastavování profilu Traffic Manageru lze pomocí této metody směrování výkonu služby zjistí kde pocházejících z požadavků na dotazy DNS a provádí rozhodnutí o směrování směrovat těchto žadatele oblasti Azure, který jim poskytne nejnižší latenci. To se provádí s využitím intelligence latence sítě, která udržuje správce provozu pro sítě jiný koncový uživatel.

Skutečné měření uživatele umožňuje měřit měření latence sítě k oblastem Azure z klientské aplikace, které vaši koncoví uživatelé používat, a mít Traffic Manager vzít v úvahu i tyto informace při rozhodování o směrování. Výběrem používat reálného měření uživatele, můžete zvýšit jeho přesnost směrování pro požadavky přicházející z těchto sítí, kde jsou umístěny koncovým uživatelům. 

## <a name="how-real-user-measurements-work"></a>Jak fungují reálného měření uživatele

Skutečné měření uživatele fungovat tak, že vaše klientské aplikace měr latence k oblastem Azure registrovaného od koncového uživatele sítí, ve kterých se používají. Jako příklad Pokud máte na webové stránce, které je přístupné uživatelům v různých umístěních (například v Severní Americe oblastech), můžete využít power reálného měření uživatele při použití metody směrování podle výkonu získat je nejlepší oblasti Azure v který je hostován vaše serverová aplikace.

Spustí vložením Azure zadané JavaScript (s jedinečný klíč, který v něm) na webových stránkách. Po dokončení, vždy, když uživatel navštíví webová stránka, dotáže se JavaScript Traffic Manager se získat informace o oblastech Azure, že by měl měření. Službu vrací sadu koncových bodů skript, který pak míru tyto oblasti za sebou stažením jedné pixelů hostované v těchto oblastech Azure a poznamenat latenci mezi tím, kdy žádost byla odeslána a čas při přijetí prvního bajtu . Tyto míry jsou pak zpětně hlášeny služby Traffic Manager.

V čase to se stane, kolikrát a v mnoha sítích úvodní do Traffic Manageru získání přesnější informací o charakteristiky latenci sítě, ve kterém jsou umístěny koncovým uživatelům. Tyto informace se spustí, získávání mají být zahrnuty v Traffic Managerem směrování rozhodnutí. V důsledku toho vede k vyšší přesnosti v těchto rozhodujících na základě reálného měření uživatele, odeslána.

Pokud používáte reálného měření uživatele, se účtují, na základě počtu měření odeslaných do Traffic Manageru. Další podrobnosti o cenách najdete na webu [Traffic Manager stránce s cenami](https://azure.microsoft.com/pricing/details/traffic-manager/).

## <a name="next-steps"></a>Další postup
- Další informace o použití [reálného měření uživatele s webovými stránkami](traffic-manager-create-rum-web-pages.md)
- Další informace [fungování Traffic Manager](traffic-manager-overview.md)
- Další informace o [Mobile Center](https://docs.microsoft.com/mobile-center/)
- Další informace o [metody směrování provozu](traffic-manager-routing-methods.md) podporované nástrojem Traffic Manager
- Zjistěte, jak [vytvořit profil správce provozu](traffic-manager-create-profile.md)

