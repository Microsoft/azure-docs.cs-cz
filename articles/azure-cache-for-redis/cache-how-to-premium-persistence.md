---
title: Postup konfigurace trvalosti dat pro Azure Cache úrovně Premium pro Redis
description: Zjistěte, jak konfigurovat a spravovat svou úroveň Premium mezipaměti Azure pro instance Redis trvalost dat
services: azure-cache-for-redis
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: b01cf279-60a0-4711-8c5f-af22d9540d38
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: azure-cache-for-redis
ms.devlang: na
ms.topic: article
ms.date: 08/24/2017
ms.author: wesmc
ms.openlocfilehash: 60f9baf7fb54706dc9d31c6920c0df24173d7b35
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54105832"
---
# <a name="how-to-configure-data-persistence-for-a-premium-azure-cache-for-redis"></a>Postup konfigurace trvalosti dat pro Azure Cache úrovně Premium pro Redis
Mezipaměti Redis Azure má různé mezipaměti nabídek, které poskytují flexibilitu při výběru velikosti mezipaměti a funkcí, včetně novými funkcemi úrovně Premium jako je clustering, trvalé a podpory služby virtual network. Tento článek popisuje postup konfigurace trvalosti v Azure Cache úrovně premium pro instanci Redis.

Informace o dalších prémiových funkcí mezipaměti, naleznete v tématu [Úvod do mezipaměti Azure Redis na úrovni Premium](cache-premium-tier-intro.md).

## <a name="what-is-data-persistence"></a>Co je trvalost dat?
[Trvalost redis](https://redis.io/topics/persistence) umožňuje trvalé uchování dat uložených v Redis. Můžete také pořizovat snímky a zálohovat data, které se dají načíst v případě selhání hardwaru. Toto je obrovskou výhodu oproti úrovně Basic nebo Standard, ve kterém všechna data jsou uložená v paměti a můžou být potenciální ztráty dat v případě selhání, kde jsou uzly mezipaměti vypnuté. 

Mezipaměti Redis Azure nabízí trvalost Redis pomocí následující modely:

* **Trvalost RDB** -trvalost při RDB (databáze Redis) je nakonfigurován, mezipaměti Azure Redis potrvají snímku ukládání do mezipaměti Azure Redis v Redis binární formát na disk podle konfigurovat četnost zálohování. Případě katastrofické události, která zakáže primárním i replikovaném mezipaměti mezipaměti je znovu vytvořena, pomocí nejnovější snímek. Další informace o [výhody](https://redis.io/topics/persistence#rdb-advantages) a [nevýhody](https://redis.io/topics/persistence#rdb-disadvantages) trvalého RDB.
* **Trvalost AOF** -trvalost při AOF (soubor pouze připojit) je nakonfigurován, mezipaměti Azure Redis ukládá všechny operace zápisu do protokolu, který je uložen alespoň jednou za sekundu do účtu služby Azure Storage. Případě katastrofické události, která zakáže primárním i replikovaném mezipaměti mezipaměti je znovu vytvořena, pomocí operace zápisu uložené. Další informace o [výhody](https://redis.io/topics/persistence#aof-advantages) a [nevýhody](https://redis.io/topics/persistence#aof-disadvantages) AOF trvalého.

Trvalost nastaven z **nové mezipaměti Redis Azure** během vytváření mezipaměti a v okně **nabídce prostředků** existující premium ukládá do mezipaměti.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

Po výběru cenové úrovně premium, klikněte na tlačítko **trvalost Redis**.

![Trvalost redis][redis-cache-persistence]

Kroky v další části popisují postup konfigurace trvalosti Redis na nová mezipaměť premium. Po nakonfigurování trvalosti Redis klikněte na tlačítko **vytvořit** vytvořit nová mezipaměť premium s trvalostí Redis.

## <a name="enable-redis-persistence"></a>Povolit trvalost Redis

Redis je povolena trvalost na **trvalost dat Redis** okno výběrem buď **RDB** nebo **AOF** trvalosti. Pro nové mezipaměti toto okno se přistupuje během procesu vytváření mezipaměti, jak je popsáno v předchozí části. Pro stávající mezipaměti **trvalost dat Redis** okno přistupuje z **nabídce prostředků** ke svojí mezipaměti.

![Redis nastavení][redis-cache-settings]


## <a name="configure-rdb-persistence"></a>Konfigurace trvalosti RDB

Chcete-li zapnout stálost RDB, klikněte na tlačítko **RDB**. Pokud chcete zakázat trvalost RDB do mezipaměti povolený premium, klikněte na tlačítko **zakázané**.

![Redis do RDB trvalosti][redis-cache-rdb-persistence]

Pokud chcete nakonfigurovat interval zálohování, vyberte **četnost zálohování** z rozevíracího seznamu. Možnosti jsou **15 minut**, **30 minut**, **60 minut**, **6 hodin**, **12 hodin**a **24 hodin**. Tento interval, spustí odpočítávání po úspěšném dokončení předchozí operace zálohování a po jeho uplynutí se zahájí novou zálohu.

Klikněte na tlačítko **účtu úložiště** výběrem účtu úložiště, který chcete použít a zvolte buď **primární klíč** nebo **sekundární klíč** ze **klíč úložiště** rozevíracího seznamu. Musíte zvolit účet úložiště ve stejné oblasti jako mezipaměť a **Premium Storage** účtu se doporučuje, protože služby premium storage má vyšší propustnost. 

> [!IMPORTANT]
> Pokud je znovu vygenerovat klíč úložiště pro váš účet trvalost, je nutné překonfigurovat požadovaný klíč **klíč úložiště** rozevíracího seznamu.
> 
> 

Klikněte na tlačítko **OK** uložení konfigurace trvalosti.

Příští zálohování (nebo první zálohy pro nové mezipaměti) je zahájeno, jakmile uplyne četnost zálohování.

## <a name="configure-aof-persistence"></a>Konfigurace trvalosti AOF

Chcete-li zapnout stálost AOF, klikněte na tlačítko **AOF**. Chcete-li zakázat AOF trvalost pro mezipaměť, povolený premium, klikněte na tlačítko **zakázané**.

![AOF trvalost redis][redis-cache-aof-persistence]

Konfigurace trvalosti AOF, zadejte **první účet úložiště**. Tento účet úložiště musí být ve stejné oblasti jako mezipaměť a **Premium Storage** účtu se doporučuje, protože služby premium storage má vyšší propustnost. Volitelně můžete nakonfigurovat ještě účet úložiště s názvem **druhého účtu úložiště**. Pokud je nakonfigurovaný druhý účet úložiště, zapíšou se do druhého účtu úložiště zapisuje do mezipaměti se repliky. Pro každý účet úložiště, zvolte buď **primární klíč** nebo **sekundární klíč** ze **klíč úložiště** rozevíracího seznamu. 

> [!IMPORTANT]
> Pokud je znovu vygenerovat klíč úložiště pro váš účet trvalost, je nutné překonfigurovat požadovaný klíč **klíč úložiště** rozevíracího seznamu.
> 
> 

Když je povolena trvalost AOF, zápisu operace do mezipaměti se ukládají do účtu úložiště určeného (nebo účty, pokud jste nakonfigurovali druhého účtu úložiště). V případě závažného selhání, která přebírá seznam primárním i replikovaném mezipaměti uložené protokolu AOF slouží k opětovné sestavení mezipaměti.

## <a name="persistence-faq"></a>Trvalost – nejčastější dotazy
Následující seznam obsahuje odpovědi na nejčastější dotazy o mezipaměti Azure pro trvalost Redis.

* [Můžete povolit trvalost pro mezipaměť, dříve vytvořený?](#can-i-enable-persistence-on-a-previously-created-cache)
* [Můžete povolit trvalost AOF a RDB ve stejnou dobu?](#can-i-enable-aof-and-rdb-persistence-at-the-same-time)
* [Který model trvalého mám zvolit?](#which-persistence-model-should-i-choose)
* [Co se stane, když mám škálování na jinou velikost a, která byla vytvořená před provedením operace škálování je obnovit zálohu?](#what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation)


### <a name="rdb-persistence"></a>Trvalost RDB
* [Můžete změnit po vytvoření mezipaměti požadovanou četnost zálohování RDB?](#can-i-change-the-rdb-backup-frequency-after-i-create-the-cache)
* [Proč mám četnost zálohování v RDB 60 minut existuje více než 60 minut mezi zálohy?](#why-if-i-have-an-rdb-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups)
* [Co se stane starší zálohy RDB, když se provádí novou zálohu?](#what-happens-to-the-old-rdb-backups-when-a-new-backup-is-made)

### <a name="aof-persistence"></a>AOF trvalosti
* [Kdy mám použít druhého účtu úložiště?](#when-should-i-use-a-second-storage-account)
* [Nemá vliv AOF přetrvávání v průběhu, latence nebo výkonu mezipaměť?](#does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache)
* [Jak lze odebrat druhého účtu storage?](#how-can-i-remove-the-second-storage-account)
* [Co je na přepisování kódu a jak to ovlivní Můj mezipaměti?](#what-is-a-rewrite-and-how-does-it-affect-my-cache)
* [Co můžu očekávat, když škálování mezipaměti s AOF povoleno?](#what-should-i-expect-when-scaling-a-cache-with-aof-enabled)
* [Jakým způsobem je organizována AOF data ve službě storage?](#how-is-my-aof-data-organized-in-storage)


### <a name="can-i-enable-persistence-on-a-previously-created-cache"></a>Můžete povolit trvalost pro mezipaměť, dříve vytvořený?
Ano, trvalost Redis je možné nakonfigurovat při vytváření mezipaměti a na stávající mezipaměti úrovně premium.

### <a name="can-i-enable-aof-and-rdb-persistence-at-the-same-time"></a>Můžete povolit trvalost AOF a RDB ve stejnou dobu?

Ne, můžete povolit pouze RDB nebo AOF, ale ne obojí současně.

### <a name="which-persistence-model-should-i-choose"></a>Který model trvalého mám zvolit?

Trvalost AOF při každém zápisu uloží do protokolu, který má dopad na propustnost, ve srovnání s trvalostí RDB, což jim zálohování podle nakonfigurovaný interval zálohování, s minimálním dopadem na výkon. Trvalost AOF zvolte, pokud primární cílem je minimalizovat ztrátu dat, a dokáže zpracovat pokles propustnosti ke svojí mezipaměti. Pokud chcete zachovat v mezipaměti optimální propustnosti, ale stále má mechanismus pro obnovení dat, zvolte RDB trvalosti.

* Další informace o [výhody](https://redis.io/topics/persistence#rdb-advantages) a [nevýhody](https://redis.io/topics/persistence#rdb-disadvantages) trvalého RDB.
* Další informace o [výhody](https://redis.io/topics/persistence#aof-advantages) a [nevýhody](https://redis.io/topics/persistence#aof-disadvantages) AOF trvalého.

Další informace o výkonu při použití AOF trvalost, naleznete v tématu [AOF nemá vliv trvalost v průběhu, latence nebo výkonu mezipaměť?](#does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache)

### <a name="what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation"></a>Co se stane, když mám škálování na jinou velikost a, která byla vytvořená před provedením operace škálování je obnovit zálohu?

RDB a AOF trvalosti:

* Když provedete škálování na větší velikost, nebude to mít žádný vliv.
* Pokud provedete škálování menší velikost a máte vlastní [databází](cache-configure.md#databases) nastavení, která je větší než [limit databáze](cache-configure.md#databases) pro novou velikost, neobnoví se data v těchto databázích. Další informace najdete v tématu [je Moje vlastní databáze nastavení ovlivněných během změny měřítka?](cache-how-to-scale.md#is-my-custom-databases-setting-affected-during-scaling)
* Pokud provedete škálování menší velikost a menší velikost pro uložení všech dat z poslední zálohy není dostatek místa, během procesu obnovení, obvykle s využitím bude vyloučena klíče [allkeys lru](https://redis.io/topics/lru-cache) zásady vyřazení.

### <a name="can-i-change-the-rdb-backup-frequency-after-i-create-the-cache"></a>Můžete změnit po vytvoření mezipaměti požadovanou četnost zálohování RDB?
Ano, můžete změnit četnost zálohování RDB trvalosti na **trvalost dat Redis** okno. Pokyny najdete v tématu [konfigurovat Redis persistence](#configure-redis-persistence).

### <a name="why-if-i-have-an-rdb-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups"></a>Proč mám četnost zálohování v RDB 60 minut existuje více než 60 minut mezi zálohy?
Interval četnosti zálohování RDB trvalost nespustí, dokud se předchozí proces zálohování byla úspěšně dokončena. Pokud trvá 15 minut pro proces zálohování a úspěšně dokončete požadovanou četnost zálohování je 60 minut, další zálohování začnou účtovat až 75 minut po času zahájení předchozí zálohy.

### <a name="what-happens-to-the-old-rdb-backups-when-a-new-backup-is-made"></a>Co se stane starší zálohy RDB, když se provádí novou zálohu?
Všechna zálohování RDB trvalost s výjimkou nejnovějšího se automaticky odstraní. Odstranění nemusí dojít okamžitě, ale nejsou starší zálohy zachovat po neomezenou dobu.


### <a name="when-should-i-use-a-second-storage-account"></a>Kdy mám použít druhého účtu úložiště?

AOF trvalosti by měl použít druhého účtu úložiště, když budete přesvědčeni, že máte vyšší než očekávané sadě operace do mezipaměti.  Nastavení účtu sekundárního úložiště pomáhá zajistit, že mezipaměť nemá přístup do omezení šířky pásma úložiště.

### <a name="does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache"></a>Nemá vliv AOF přetrvávání v průběhu, latence nebo výkonu mezipaměť?

Trvalost AOF ovlivní propustnost podle přibližně 15 – 20 % mezipaměti je nižší než maximální zatížení (procesoru a Server načíst obě části 90 %). Zde by neměly být problémy s latencí při mezipaměti je v rámci těchto omezení. Mezipaměť se tato omezení však dříve dosáhnout s AOF povoleno.

### <a name="how-can-i-remove-the-second-storage-account"></a>Jak lze odebrat druhého účtu storage?

Nastavením druhého účtu úložiště bude stejný jako první účet úložiště můžete odebrat účet AOF trvalost sekundárního úložiště. Pokyny najdete v tématu [trvalost nakonfigurujte AOF](#configure-aof-persistence).

### <a name="what-is-a-rewrite-and-how-does-it-affect-my-cache"></a>Co je na přepisování kódu a jak to ovlivní Můj mezipaměti?

Jakmile je soubor AOF dostatečně velký, přepisování je automaticky zařazeno do fronty v mezipaměti. Přepsání změní velikost souboru AOF s minimální sadu operací, které jsou potřeba k vytvoření aktuální datové sady. Během přepisů můžete očekávat dříve dosažení omezení výkonu, zejména při práci s velkými datovými sadami. Přepisů docházet k méně často AOF soubor bude větší, ale bude trvat poměrně dlouhou dobu, kdy se stane.

### <a name="what-should-i-expect-when-scaling-a-cache-with-aof-enabled"></a>Co můžu očekávat, když škálování mezipaměti s AOF povoleno?

Pokud AOF souboru chvíli škálování je výrazně velký, pak očekávat, že se operace škálování trvá déle než obvykle, protože ho bude možné znovu načíst tento soubor po dokončení změny velikosti.

Další informace o škálování najdete v tématu [co se stane, když mám škálování na jinou velikost a, která byla vytvořená před provedením operace škálování je obnovit zálohu?](#what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation)

### <a name="how-is-my-aof-data-organized-in-storage"></a>Jakým způsobem je organizována AOF data ve službě storage?

Data uložená v soubory AOF je rozdělen do více objekty BLOB stránky podle počtu uzlů pro zvýšení výkonu při ukládání dat do úložiště. Následující tabulka uvádí, kolik objekty BLOB stránky se používají u jednotlivých cenových úrovní:

| Úroveň Premium | Objekty blob |
|--------------|-------|
| P1           | 4 za horizontální oddíl    |
| P2           | 8 / horizontálních oddílů    |
| P3           | 16 za horizontální oddíl   |
| P4           | 20 na horizontální oddíl   |

Při zapnuté funkci clustering, každý horizontální oddíl v mezipaměti má svou vlastní sadu objektů BLOB stránky, jak je uvedeno v předchozí tabulce. Například mezipaměť P2 se tři horizontální oddíly distribuuje svůj soubor AOF mezi 24 objekty BLOB stránky (8 objektů blob za horizontální oddíl s 3 horizontální oddíly).

Po revize existují dvě sady soubory AOF v úložišti. Přepisů probíhá na pozadí a připojení k první sadu souborů, zatímco množinové operace, které se odesílají do mezipaměti při přepsání připojení k druhé sadě. Zálohy jsou dočasně uloženy během přepisů v případě selhání, ale bude po dokončení na přepisování kódu okamžitě odstraněna.


## <a name="next-steps"></a>Další postup
Další informace o použití další prémiových funkcí mezipaměti.

* [Úvod do mezipaměti Azure Redis na úrovni Premium](cache-premium-tier-intro.md)

<!-- IMAGES -->

[redis-cache-premium-pricing-tier]: ./media/cache-how-to-premium-persistence/redis-cache-premium-pricing-tier.png

[redis-cache-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-persistence.png

[redis-cache-rdb-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-rdb-persistence.png

[redis-cache-aof-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-aof-persistence.png

[redis-cache-settings]: ./media/cache-how-to-premium-persistence/redis-cache-settings.png
