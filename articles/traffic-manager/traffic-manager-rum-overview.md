---
title: Měření reálných uživatelů ve službě Azure Traffic Manager
description: Úvod do měření reálných uživatelů ve službě Traffic Manager
services: traffic-manager
documentationcenter: traffic-manager
author: asudbring
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: allensu
ms.custom: ''
ms.openlocfilehash: cbde96f135aa52ada9bb10b47ca81687992e5833
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67070988"
---
# <a name="traffic-manager-real-user-measurements-overview"></a>Přehled Traffic Manager Real User Measurements

Při nastavování profilu Traffic Manageru metody směrování výkonu zjistí služba kde požadavků na dotazy DNS přicházející ze zařízení a díky rozhodování o směrování směrovat tyto žadatele na základě oblasti Azure, který jim poskytuje nejnižší latenci. Toho lze dosáhnout využitím intelligence latence sítě, která Traffic Manageru udržuje pro různé koncové uživatele sítě.

Měření Real User Measurements umožňuje měřit měření latence sítě do oblastí Azure, z klientských aplikací, které vaši koncoví uživatelé použít, a mít Traffic Manageru zvažte tyto informace i při rozhodování o směrování. Kliknutím na použít měření Real User Measurements můžete zvýšit jeho přesnost směrování pro požadavky přicházející z těchto sítí, kde jsou umístěné vaše koncové uživatele. 

## <a name="how-real-user-measurements-work"></a>Jak fungují měření Real User Measurements

Měření Real User Measurements fungovat tak, že vaše latence klienta aplikace míru do oblastí Azure znázorněný od koncových uživatelů sítí, ve kterých se používají. Například pokud máte webovou stránku, která se využívají uživatelé v různých umístěních (například v oblastech Severní Ameriky), můžete použít měření Real User Measurements pomocí metody směrování podle výkonu na tom, abyste doporučené oblasti Azure, ve kterém je váš server aplikace hostovaná.

Spustí s využitím vkládání zadaného jazyka JavaScript v Azure (s jedinečný klíč v něm) na webových stránkách. Po dokončení, pokaždé, když uživatel navštíví tuto webovou stránku, dotazuje JavaScript Traffic Manager, chcete-li získat informace o oblastech Azure, která by měla měření. Služba vrátí sadu koncových bodů do skriptu, který pak míru těchto oblastí postupně stažením image jeden pixel hostované v těchto oblastech Azure a poznamenat latence mezi časem žádost byla odeslána a čas přijetí prvního bajtu . Tato měření se pak hlášeny zpět ke službě Traffic Manager.

V průběhu času k tomu dojde v mnoha případech a napříč mnoha sítím, což vede k Traffic Manageru získávání přesnější informace o vlastnostech latence sítě, ve kterém se nacházejí vaši koncoví uživatelé. Tyto informace se spustí, získávání mají být zahrnuty v rozhodování o směrování provádí Traffic Manager. V důsledku toho vede k vyšší přesnost v těchto rozhodnutí založené na reálných uživatelů odeslána.

Při použití měření Real User Measurements účtují na základě počtu měření odesílané do Traffic Manageru. Podrobné informace o cenách najdete [Traffic Manageru stránce s cenami](https://azure.microsoft.com/pricing/details/traffic-manager/).

## <a name="next-steps"></a>Další postup
- Další informace o použití [měření Real User Measurements s webovými stránkami](traffic-manager-create-rum-web-pages.md)
- Přečtěte si [jak funguje Traffic Manager](traffic-manager-overview.md)
- Další informace o [Mobile Center](https://docs.microsoft.com/mobile-center/)
- Další informace o [metody směrování provozu](traffic-manager-routing-methods.md) podporované nástrojem Traffic Manager
- Zjistěte, jak [vytvořit profil služby Traffic Manager](traffic-manager-create-profile.md)

