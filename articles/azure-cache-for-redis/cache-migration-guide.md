---
title: Migrace na Azure Cache for Redis
description: Naučte se migrovat stávající mezipaměť do Azure cache pro Redis.
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/22/2020
ms.author: yegu
ms.openlocfilehash: 5de4e1b465cfc3ced59f8fe34a7f397324b4a225
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2020
ms.locfileid: "92537622"
---
# <a name="migrate-to-azure-cache-for-redis"></a>Migrace na Azure Cache for Redis
Tento článek popisuje několik přístupů k migraci stávající mezipaměti Redis spuštěné v místním prostředí nebo v jiné cloudové službě do mezipaměti Azure pro Redis.

## <a name="migration-scenarios"></a>Scénáře migrace
Open Source Redis může běžet v mnoha výpočetních prostředích. Mezi běžné příklady patří:

- **Místní** mezipaměť Redis spuštěné v soukromých datacentrech.
- **Cloudové virtuální počítače** – Redis mezipaměti běžící na virtuálních počítačích Azure, AWS EC2 a tak dále.
- **Hostingové** služby spravované službami Redis, jako je například AWS ElastiCache.
- **Různé oblasti** – Redis mezipaměti umístěné v jiné oblasti Azure.

Pokud takovou mezipaměť máte, možná ji budete moct přesunout do mezipaměti Azure pro Redis s minimálním přerušením nebo výpadkem.

## <a name="migration-options"></a>Možnosti migrace

Existují různé způsoby, jak můžete přepínat z jedné mezipaměti do druhé. V závislosti na tom, kde je mezipaměť a jak s ní vaše aplikace pracuje, bude jedna metoda užitečnější než ostatní. Některé z často používaných strategií migrace jsou popsané níže.

   | Možnost       | Výhody | Nevýhody |
   | ------------ | ---------- | ------------- |
   | Vytvořit novou mezipaměť | Nejjednodušší pro implementaci. | Je nutné znovu naplnit data do nové mezipaměti, což nemusí fungovat s mnoha aplikacemi. |
   | Export a import dat pomocí souboru RDB | Obecně kompatibilní s jakoukoliv mezipamětí Redis Cache. | Některá data mohou být ztracena, pokud jsou zapsána do existující mezipaměti po vygenerování souboru RDB. | 
   | Duální zápis dat do dvou mezipamětí | Nedochází ke ztrátě dat ani výpadkům. Nepřerušené operace stávající mezipaměti. Jednodušší testování nové mezipaměti. | Potřebuje dvě mezipaměti po delší dobu. | 
   | Programové migrace dat | Úplná kontrola nad tím, jak se data přesunou | Vyžaduje vlastní kód. | 

### <a name="create-a-new-azure-cache-for-redis"></a>Vytvoření nové mezipaměti Azure pro Redis

Tento přístup je technicky nemigrací. Pokud nezáleží na ztrátě dat, nejjednodušší způsob, jak přejít do mezipaměti Azure pro Redis, je vytvoření instance mezipaměti a připojení aplikace k ní. Pokud například použijete Redis jako mezipaměť se záznamy databáze, můžete snadno znovu sestavit mezipaměť od nuly.

K implementaci této možnosti slouží obecné kroky:

1. Vytvořte novou mezipaměť Azure pro instanci Redis.

2. Aktualizujte aplikaci tak, aby používala novou instanci.

3. Odstraňte starou instanci Redis.

### <a name="export-data-to-an-rdb-file-and-import-it-into-azure-cache-for-redis"></a>Export dat do souboru RDB a jejich import do Azure cache pro Redis

Open Source Redis definuje standardní mechanismus pro pořízení snímku datové sady v paměti mezipaměti a jejich uložení do souboru. Tento soubor nazvaný RDB může být čten jinou Redis Cache. [Azure cache pro Redis úrovně Premium](cache-overview.md#service-tiers) podporuje import dat do instance mezipaměti pomocí souborů RDB. Soubor RDB můžete použít k přenosu dat z existující mezipaměti do Azure cache pro Redis.

> [!IMPORTANT]
> Formát souboru RDB se může mezi Redis verzemi měnit a nemusí udržet zpětnou kompatibilitu. Verze Redis mezipaměti, ze které exportujete, by měla být stejná nebo nižší než verze poskytovaná službou Azure cache pro Redis.
>

K implementaci této možnosti slouží obecné kroky:

1. Vytvořte novou mezipaměť Azure pro instanci Redis na úrovni Premium, která má stejnou velikost jako (nebo větší než) stávající mezipaměť.

2. Uložte snímek stávající mezipaměti Redis. Redis můžete [nakonfigurovat tak, aby pravidelně ukládaly snímky](https://redis.io/topics/persistence) , nebo můžete proces spustit ručně pomocí příkazů [Save](https://redis.io/commands/save) nebo [BGSAVE](https://redis.io/commands/bgsave) . Soubor RDB se ve výchozím nastavení nazývá "dump. RDB" a bude umístěn v cestě zadané v konfiguračním souboru *Redis. conf* .

    > [!NOTE]
    > Pokud migrujete data v rámci Azure cache pro Redis, přečtěte si [tyto pokyny, jak exportovat soubor RDB](cache-how-to-import-export-data.md) , nebo místo toho použít [rutinu exportu prostředí PowerShell](/powershell/module/azurerm.rediscache/export-azurermrediscache?view=azurermps-6.13.0&viewFallbackFrom=azurermps-6.4.0) .
    >

3. Zkopírujte soubor RDB do účtu služby Azure Storage v oblasti, ve které se nachází vaše nová mezipaměť. Pro tuto úlohu můžete použít AzCopy.

4. Importujte soubor RDB do nové mezipaměti pomocí těchto [instrukcí pro import](cache-how-to-import-export-data.md) nebo [rutiny importu PowerShellu](/powershell/module/azurerm.rediscache/import-azurermrediscache?view=azurermps-6.13.0&viewFallbackFrom=azurermps-6.4.0).

5. Aktualizujte aplikaci tak, aby používala novou instanci mezipaměti.

### <a name="write-to-two-redis-caches-simultaneously-during-migration-period"></a>Zápis do dvou mezipamětí Redis současně během období migrace

Místo přesunu dat přímo mezi mezipamětí můžete použít svou aplikaci k zápisu dat do existující mezipaměti i nového, kterou nastavíte. Aplikace bude ve výchozím stavu nadále číst data z existující mezipaměti. Pokud má nová mezipaměť potřebná data, přepnete aplikaci do této mezipaměti a vyřadíte starou ji. Řekněme, že například použijete Redis jako úložiště relace a relace aplikace jsou platné po dobu sedmi dnů. Po zapsání do dvou mezipamětí za týden budete mít jistotu, že nová mezipaměť obsahuje všechny informace o relaci bez vypršení platnosti. Od tohoto okamžiku se můžete bezpečně spoléhat, aniž byste se museli zabývat ztrátou dat.

K implementaci této možnosti slouží obecné kroky:

1. Vytvořte novou mezipaměť Azure pro instanci Redis na úrovni Premium, která má stejnou velikost jako (nebo větší než) stávající mezipaměť.

2. Upravte kód aplikace pro zápis do nové i původní instance.

3. Pokračovat v čtení dat z původní instance, dokud není nová instance dostatečně naplněná daty.

4. Aktualizuje kód aplikace pro čtení a zápis pouze z nové instance.

5. Odstraňte původní instanci.

### <a name="migrate-programmatically"></a>Migrace prostřednictvím kódu programu

Vlastní proces migrace můžete vytvořit programově čtením dat z existující mezipaměti a jejich zápisem do mezipaměti Azure pro Redis. Tento [Open source nástroj](https://github.com/deepakverma/redis-copy) lze použít ke kopírování dat z jedné instance Azure cache pro instanci Redis do jiné. Tento nástroj je užitečný pro přesun dat mezi instancemi mezipaměti v různých oblastech mezipaměti Azure. K dispozici je také [kompilovaná verze](https://github.com/deepakverma/redis-copy/releases/download/alpha/Release.zip) . Můžete také najít zdrojový kód, který bude užitečnou příručkou pro psaní vlastního nástroje pro migraci.

> [!NOTE]
> Microsoft tuto nástroj oficiálně nepodporuje. 
>

K implementaci této možnosti slouží obecné kroky:

1. Vytvořte virtuální počítač v oblasti, ve které je umístěná existující mezipaměť. Pokud je vaše datová sada velká, vyberte poměrně výkonný virtuální počítač pro snížení doby kopírování.

2. Vytvořte novou mezipaměť Azure pro instanci Redis.

3. Vyprázdněte data z nové mezipaměti, abyste měli jistotu, že je prázdná. Tento krok je nutný, protože samotný nástroj pro kopírování nepřepisuje žádný existující klíč v cílové mezipaměti.

    > [!IMPORTANT]
    > Ujistěte se, že se nevyprazdňuje ze zdrojové mezipaměti.
    >

4. K automatizaci kopírování dat ze zdrojové mezipaměti do cíle použijte aplikaci, jako je open source nástroj výše. Mějte na paměti, že proces kopírování může chvíli trvat, než se dokončí v závislosti na velikosti datové sady.

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o funkcích Azure cache pro Redis.

* [Mezipaměť Azure pro úrovně služeb Redis](cache-overview.md#service-tiers)
* [Import dat](cache-how-to-import-export-data.md#import)