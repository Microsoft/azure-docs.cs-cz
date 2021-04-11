---
title: Preview úrovně Basic – Citus (Basic) – Azure Database for PostgreSQL
description: Základní vrstva s jedním uzlem pro Azure Database for PostgreSQL-Citus (škálování na úrovni Basic)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: c8cad5eb0983715a7cc7c18249243e93a2c498d7
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2021
ms.locfileid: "107023957"
---
# <a name="basic-tier-preview"></a>Úroveň Basic (Preview)

> [!IMPORTANT]
> Úroveň Basic úrovně Citus () je aktuálně ve verzi Preview.  Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
>
> Úplný seznam dalších nových funkcí najdete v části [funkce ve verzi Preview pro Citus (škálování)](hyperscale-preview-features.md).

Úroveň Basic v Azure Database for PostgreSQL-Citus) představuje jednoduchý způsob, jak vytvořit malou skupinu serverů, kterou můžete později škálovat. Přestože skupiny serverů na úrovni Standard mají uzel koordinátora a alespoň dva pracovní uzly, na úrovni Basic běží vše v jednom uzlu databáze.

Kromě použití méně uzlů má úroveň Basic všechny funkce úrovně Standard. Podobně jako na úrovni Standard podporuje vysokou dostupnost, čtení replik a sloupcové úložiště tabulek, a to mimo jiné funkce.

## <a name="choosing-basic-vs-standard-tier"></a>Výběr úrovně Basic vs Standard

Úroveň Basic může být ekonomicky výhodná možnost nasazení pro počáteční vývoj, testování a průběžnou integraci. Používá jediný uzel databáze a prezentuje stejné rozhraní API SQL jako úroveň Standard. Můžete testovat aplikace s využitím úrovně Basic a později se [dostupňovat na úroveň Standard](howto-hyperscale-scale-grow.md#add-worker-nodes) s jistotou, že rozhraní zůstává stejné.

Úroveň Basic je také vhodná pro menší úlohy v produkčním prostředí (jakmile se vychází z verze Preview do všeobecné dostupnosti). *V rámci* úrovně Basic je prostor pro vertikální škálování zvýšením počtu virtuální jádra serveru.

Pokud je potřeba větší měřítko hned, použijte úroveň Standard. Nejnižší povolená skupina serverů má jeden uzel koordinátora a dva pracovní procesy. Můžete použít více uzlů na základě vašeho použití, jak je popsáno v naší [počáteční změně velikosti](howto-hyperscale-scale-initial.md) .

## <a name="next-steps"></a>Další kroky

* Naučte se [zřídit základní úroveň](quickstart-create-hyperscale-basic-tier.md) .
* Až budete připraveni, přečtěte si téma [postupné promoci](howto-hyperscale-scale-grow.md#add-worker-nodes) z úrovně Basic na úroveň Standard.
* Možnost [sloupcového úložiště](concepts-hyperscale-columnar.md) je dostupná jak na úrovni Basic, tak na úrovni Standard.
