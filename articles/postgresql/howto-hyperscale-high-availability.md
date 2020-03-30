---
title: Konfigurace vysoké dostupnosti – hyperškálování (Citus) – databáze Azure pro PostgreSQL
description: Jak povolit nebo zakázat vysokou dostupnost
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: a8d4b5949b34d16191e9ec10a1dd39faff3660dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74977645"
---
# <a name="configure-hyperscale-citus-high-availability"></a>Konfigurace vysoké dostupnosti hyperškálování (Citus)

Azure Database for PostgreSQL – Hyperscale (Citus) poskytuje vysokou dostupnost (HA), aby se zabránilo prostojům databáze. S povolenou technologií HA získá každý uzel ve skupině serverů pohotovostní režim. Pokud se původní uzel stane nefunkčním, jeho pohotovostní režim bude povýšen tak, aby jej nahradil.

> [!IMPORTANT]
> Vzhledem k tomu, že HA zdvojnásobí počet serverů ve skupině, zdvojnásobí také náklady.

Povolení HA je možné při vytváření skupiny serverů nebo později na kartě **Konfigurace** pro skupinu serverů na webu Azure Portal. Uživatelské rozhraní vypadá podobně v obou případech. Přetáhněte jezdec pro **vysokou dostupnost** na ANO:

![posuvník ha](./media/howto-hyperscale-high-availability/01-ha-slider.png)

Klepnutím na tlačítko **Uložit** použijete výběr. Povolení HA může nějakou dobu trvat, protože rezervy skupiny serverů pohotovostní režimy a datové proudy dat k nim.

Karta **Přehled** pro skupinu serverů zobrazí seznam všech uzlů a jejich pohotovostních režimů spolu se sloupcem **Vysoká dostupnost** označující, zda je ha úspěšně povolena pro každý uzel.

![sloupec ha v přehledu skupiny serverů](./media/howto-hyperscale-high-availability/02-ha-column.png)

### <a name="next-steps"></a>Další kroky

Další informace o [vysoké dostupnosti](concepts-hyperscale-high-availability.md).
