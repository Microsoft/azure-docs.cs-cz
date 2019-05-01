---
title: Datové entity
description: Přehled datové entity.
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
manager: Ricardo.Villalobos
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: a382f9b3ce08bba266311c2cc1d5f868f1bc3143
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/30/2019
ms.locfileid: "64934931"
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
