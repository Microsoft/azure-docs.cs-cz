---
title: Konfigurace vysoké dostupnosti – Citus () – Azure Database for PostgreSQL
description: Jak povolit nebo zakázat vysokou dostupnost
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 07/27/2020
ms.openlocfilehash: 46b842994cbcf7efe66d5992c79246d77626e268
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "90907388"
---
# <a name="configure-hyperscale-citus-high-availability"></a>Konfigurace vysoké dostupnosti Citus (High-Scale)

Azure Database for PostgreSQL – Citus (vysoká úroveň) poskytuje vysokou dostupnost (HA), aby nedocházelo k výpadkům databáze. Když je povolený HA, každý uzel ve skupině serverů získá pohotovostní režim. Pokud se původní uzel změní na není v pořádku, bude jeho pohotovostní režim povýšen na jeho náhradu.

> [!IMPORTANT]
> Vzhledem k tomu, že HA nazdvojnásobuje počet serverů ve skupině, bude také dvojnásobek nákladů.

Povolení vysoké dostupnosti je možné během vytváření skupiny serverů nebo později na kartě **COMPUTE + úložiště** pro skupinu serverů v Azure Portal. Uživatelské rozhraní vypadá podobně v obou případech. Přetáhněte posuvník pro **vysokou dostupnost** z ne na Ano:

:::image type="content" source="./media/howto-hyperscale-high-availability/01-ha-slider.png" alt-text="posuvník ha":::

Kliknutím na tlačítko **Uložit** použijte výběr. Povolení HA může nějakou dobu trvat, protože skupiny serverů zřídí pohotovostní data a proudy dat.

Karta **Přehled** pro skupinu serverů zobrazí seznam všech uzlů a jejich pohotovostních a společně se sloupcem s **vysokou dostupností** , který označuje, jestli je u každého uzlu úspěšně povolený ha.

:::image type="content" source="./media/howto-hyperscale-high-availability/02-ha-column.png" alt-text="sloupec ha ve skupině serverů – přehled":::

### <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [vysoké dostupnosti](concepts-hyperscale-high-availability.md).
