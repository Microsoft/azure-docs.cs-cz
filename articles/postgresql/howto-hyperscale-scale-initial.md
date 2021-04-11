---
title: Počáteční velikost skupiny serverů – Citus (velký rozsah) – Azure Database for PostgreSQL
description: Vyberte správnou počáteční velikost pro váš případ použití.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 04/07/2021
ms.openlocfilehash: 8b87cfc8276d13ccc7e12a4901489ea0b1e770a5
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2021
ms.locfileid: "107012502"
---
# <a name="pick-initial-size-for-hyperscale-citus-server-group"></a>Vybrat počáteční velikost pro skupinu serverů Citus (velký rozsah)

Velikost skupiny serverů, počtu uzlů i jejich kapacity hardwaru, se [snadno mění](howto-hyperscale-scale-grow.md). Pořád ale musíte zvolit počáteční velikost pro novou skupinu serverů. Zde jsou některé tipy pro rozumnou volbu.

## <a name="use-cases"></a>Případy použití

Citus (velká škála) se často používá následujícími způsoby.

### <a name="multi-tenant-saas"></a>Víceklientský software jako služba (SaaS)

Při migraci do škálování (Citus) z existující instance databáze PostgreSQL s jedním uzlem vyberte cluster, ve kterém se celkový počet pracovních virtuální jádra a paměti RAM rovná původní instanci. V takových scénářích jsme viděli 3x vylepšení výkonu, protože horizontálního dělení vylepšuje využití prostředků a povoluje menší indexy atd.

Jediným rozhodnutím je vCore Count. Přidělení paměti RAM je aktuálně určeno na základě počtu vCore, jak je popsáno na stránce [Možnosti konfigurace Citus ()](concepts-hyperscale-configuration-options.md) .
Uzel koordinátora nevyžaduje jako pracovní procesy tolik paměti RAM, ale neexistuje způsob, jak zvolit RAM a virtuální jádra nezávisle.

### <a name="real-time-analytics"></a>Analýza v reálném čase

Total virtuální jádra: při práci s daty v paměti RAM můžete očekávat lineární vylepšení výkonu na úrovni Citus (s poměrem k počtu jader pracovních procesů). Pro určení správného počtu virtuální jádra pro vaše potřeby zvažte aktuální latenci pro dotazy v databázi s jedním uzlem a požadovanou latenci v rámci škálování (Citus). Aktuální latenci vydělte požadovanou latencí a výsledek zaokrouhlete.

Pracovní paměť RAM: nejlepším případem je poskytnout dostatek paměti, že většina pracovní sady se vejde do paměti. Typ dotazů, které vaše aplikace používá, ovlivňuje požadavky na paměť. Pokud chcete zjistit, kolik paměti vyžaduje, můžete spustit příkaz vysvětlit analyzovat na dotazu. Pamatujte na to, že virtuální jádra a paměť RAM se škálují společně, jak je popsáno v článku [Možnosti konfigurace pro Citus (AutoScale)](concepts-hyperscale-configuration-options.md) .

## <a name="choosing-a-hyperscale-citus-tier"></a>Výběr úrovně Citus (škálování na úrovni)

> [!IMPORTANT]
> Úroveň Basic úrovně Citus () je aktuálně ve verzi Preview.  Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
>
> Úplný seznam dalších nových funkcí najdete v části [funkce ve verzi Preview pro Citus (škálování)](hyperscale-preview-features.md).

Výše uvedené části poskytují představu o tom, kolik virtuální jádra a kolik paměti RAM je potřeba pro každý případ použití. Tyto požadavky můžete splnit pomocí volby mezi dvěma úrovněmi škálování (Citus): úroveň Basic a úroveň Standard.

Úroveň Basic používá jeden uzel databáze k provedení zpracování, zatímco úroveň Standard umožňuje více uzlů. Vrstvy jsou jinak identické a nabízejí stejné funkce. V některých případech je možné škálovat virtuální jádra a místo na disku na jednom uzlu a v ostatních případech vyžaduje spolupráci více uzlů.

Porovnání vrstev najdete na stránce věnované konceptům na [úrovni Basic](concepts-hyperscale-tiers.md) .

## <a name="next-steps"></a>Další kroky

- [Škálování skupiny serverů](howto-hyperscale-scale-grow.md)
- Přečtěte si další informace o [možnostech výkonu](concepts-hyperscale-configuration-options.md)skupiny serverů.
