---
title: Entity dat | Dokumentace Microsoftu
description: Přehled datové entity.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Ricardo.Villalobos
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 579856ab87aaf8d051f2e3c161bb2d0e2f693ed5
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54446574"
---
# <a name="data-entities"></a>Datové entity

Tento článek definuje a poskytuje přehled o datových entitách. Obsahuje informace o možnostech datové entity, scénáře, které podporují, kategorií, které se používají a metody pro jejich vytváření.

## <a name="overview"></a>Přehled

Entity dat je abstrakcí fyzického provádění databázových tabulek. Například v normalizované tabulky velká část dat pro jednotlivé zákazníky můžou být uložená v tabulce zákazníků a pak ostatní můžou být rozložená v malou sadu souvisejících tabulek. V tomto případě data entity zákazník koncept se zobrazí jako jedna denormalizovaným zobrazení, ve kterém každý řádek obsahuje všechna data z tabulky se zákazníky a její související tabulky. Entity dat zapouzdřuje koncept business do formátu, který usnadňuje vývoj a integraci. Abstrahovanou povaze dat entity může zjednodušit vývoj aplikací a vlastní nastavení. Později abstrakci také insulates kód aplikace z nevyhnutelné provozu mezi verzemi fyzické tabulek.

Shrnutí: Entity dat poskytuje koncepční abstrakce a zapouzdření (denormalizovaným zobrazení) základní schémata tabulek pro reprezentaci dat klíče koncepty a funkce.

## <a name="capabilities"></a>Možnosti

Entity dat má následující možnosti:

- Nahradí Rozbíhající a fragmentované koncepty AXD, Entity Framework Import/Export dat (DIXF) a agregace dotazů s jeden koncept.
- Poskytuje jedné sadě k zachycení obchodní logiky a aktivovat scénáře jako import/export, integrace a programování.
- Bude primární mechanismus pro export a import balíčků dat pro scénáře, Application Lifecycle Management (ALM) a ukázková data.
- Může být vystavena jako služby OData a potom použít v tabulkovém – vizuální styl synchronní integrační scénáře a integrace aplikace Microsoft Office.

Zobrazit [datové entity](https://docs.microsoft.com/dynamics365/operations/dev-itpro/data-entities/data-entities) Další informace.
