---
title: Postup konfigurace trvalosti dat pro mezipaměť Azure Redis Cache Premium
description: Zjistěte, jak konfigurovat a spravovat vaše instance Azure Redis Cache Premium vrstvu trvalosti dat
services: redis-cache
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: b01cf279-60a0-4711-8c5f-af22d9540d38
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 08/24/2017
ms.author: wesmc
ms.openlocfilehash: 270158bbf85a58a48a367a091ad2b09a9d114b2b
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/19/2018
ms.locfileid: "27910840"
---
# <a name="how-to-configure-data-persistence-for-a-premium-azure-redis-cache"></a>Postup konfigurace trvalosti dat pro mezipaměť Azure Redis Cache Premium
Azure Redis Cache má jiný mezipaměti nabídky, které poskytují flexibilitu při výběru velikost mezipaměti a funkce, včetně funkce úrovně Premium, jako je clustering, trvalosti a podpory služby virtual network. Tento článek popisuje postup konfigurace trvalosti v instanci služby Azure Redis Cache premium.

Informace o dalších funkcí mezipaměti premium najdete v tématu [Úvod do Azure Redis Cache Premium vrstvy](cache-premium-tier-intro.md).

## <a name="what-is-data-persistence"></a>Co je trvalosti dat?
[Trvalost redis](https://redis.io/topics/persistence) umožňuje zachovat data uložená v Redis. Můžete také pořizovat snímky a zálohovat data, která budete moct načíst v případě selhání hardwaru. Toto je obrovskou výhodu přes úroveň Basic nebo Standard kde všechna data jsou uloženy v paměti a může být potenciální ztrátě dat v případě selhání, kde jsou uzly mezipaměti dolů. 

Azure Redis Cache nabízí trvalosti Redis pomocí těchto modelů:

* **Trvalost RDB** -trvalost při RDB (databáze Redis) je nakonfigurován, Azure Redis Cache potrvají snímek mezipaměti Redis v Redis binární formát disku podle konfigurovat četnost zálohování. Pokud dojde k závažné události, zakazující primární server a repliky mezipaměti, do mezipaměti je znovu vytvořena, pomocí poslední snímek. Další informace o [výhody](https://redis.io/topics/persistence#rdb-advantages) a [nevýhody](https://redis.io/topics/persistence#rdb-disadvantages) z trvalost RDB.
* **Trvalost AOF** -trvalost při AOF (pouze připojit soubor) je nakonfigurován, Azure Redis Cache uloží každé operace zápisu do protokolu, který je uložený aspoň jednou za sekundu do účtu Azure Storage. Pokud dojde k závažné události, zakazující primární server a repliky mezipaměti, do mezipaměti je znovu vytvořena, pomocí operace uložené zápisu. Další informace o [výhody](https://redis.io/topics/persistence#aof-advantages) a [nevýhody](https://redis.io/topics/persistence#aof-disadvantages) z AOF trvalost.

Trvalost je nakonfigurovaný z **nová mezipaměť Redis** okno při vytvoření mezipaměti a na **prostředků nabídky** existující premium ukládá do mezipaměti.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

Jakmile je vybraná cenová úroveň premium, klikněte na možnost **trvalosti Redis**.

![Trvalost redis][redis-cache-persistence]

Kroky v následující části popisují postup konfigurace trvalosti Redis na nové mezipaměti premium. Po nakonfigurování trvalosti Redis klikněte na tlačítko **vytvořit** k vytvoření nové mezipaměti premium s trvalosti Redis.

## <a name="enable-redis-persistence"></a>Povolit trvalost Redis

Redis je povolena trvalost na **trvalosti dat Redis** okna tak, že buď zvolíte **RDB** nebo **AOF** trvalost. U nových mezipamětí toto okno přistupuje během procesu vytváření mezipaměti, jak je popsáno v předchozí části. Pro existující mezipamětí **trvalosti dat Redis** okno je k němu přistupovat z **prostředků nabídky** ke svojí mezipaměti.

![Nastavení redis][redis-cache-settings]


## <a name="configure-rdb-persistence"></a>Konfigurace trvalosti RDB

Chcete-li zapnout stálost RDB, klikněte na tlačítko **RDB**. Chcete-li zakázat trvalost RDB na dříve povoleno premium mezipaměti, klikněte na tlačítko **zakázané**.

![RDB trvalosti redis][redis-cache-rdb-persistence]

Pokud chcete nakonfigurovat interval zálohování, vyberte **četnost zálohování** z rozevíracího seznamu. Vybrat možnost **15 minut**, **30 minut**, **60 minut**, **6 hodin**, **12 hodin**a **24 hodin**. Tento interval spustí počítání po úspěšném dokončení předchozí operace zálohování a po jeho uplynutí se zahájí novou zálohu.

Klikněte na tlačítko **účet úložiště** a vyberte účet úložiště, který chcete použít a vyberte buď **primární klíč** nebo **sekundární klíč** z **klíč úložiště** rozevíracího seznamu. Musíte zvolit účet úložiště ve stejné oblasti jako do mezipaměti a **Storage úrovně Premium** účet je doporučená, protože úložiště premium je vyšší propustnost. 

> [!IMPORTANT]
> Pokud se znovu vygeneruje klíč úložiště pro váš účet trvalost, je nutné překonfigurovat požadovaný klíč **klíč úložiště** rozevíracího seznamu.
> 
> 

Klikněte na tlačítko **OK** trvalost konfiguraci uložíte.

Další zálohování (nebo první zálohy u nových mezipamětí) je zahájena po uplynutí intervalu četnost zálohování.

## <a name="configure-aof-persistence"></a>Konfigurace trvalosti AOF

Chcete-li zapnout stálost AOF, klikněte na tlačítko **AOF**. Chcete-li zakázat trvalost AOF na dříve povoleno premium mezipaměti, klikněte na tlačítko **zakázané**.

![AOF trvalosti redis][redis-cache-aof-persistence]

Konfigurace trvalosti AOF, zadejte **první účet úložiště**. Tento účet úložiště musí být ve stejné oblasti jako do mezipaměti a **Storage úrovně Premium** účet je doporučená, protože úložiště premium je vyšší propustnost. Volitelně můžete nakonfigurovat účet úložiště s názvem **druhého účtu úložiště**. Pokud je nakonfigurovaný druhý účet úložiště, zapíšou se do této druhé účet úložiště zápisy do mezipaměti repliky. Pro každý účet úložiště zvolit buď **primární klíč** nebo **sekundární klíč** z **klíč úložiště** rozevíracího seznamu. 

> [!IMPORTANT]
> Pokud se znovu vygeneruje klíč úložiště pro váš účet trvalost, je nutné překonfigurovat požadovaný klíč **klíč úložiště** rozevíracího seznamu.
> 
> 

Když je povolena trvalost AOF, zápisu operace do mezipaměti se uloží do účtu úložiště určený (nebo účty, pokud jste nakonfigurovali druhého účtu úložiště). V případě selhání závažné, která přebírá dolů primární server a repliky mezipaměť protokol uložené AOF slouží k opětovné sestavení mezipaměti.

## <a name="persistence-faq"></a>Trvalost – nejčastější dotazy
Následující seznam obsahuje odpovědi na nejčastější dotazy týkající se Azure Redis Cache trvalost.

* [Můžete povolit trvalost na dříve vytvořenou mezipaměti?](#can-i-enable-persistence-on-a-previously-created-cache)
* [Můžete povolit trvalost AOF a RDB současně?](#can-i-enable-aof-and-rdb-persistence-at-the-same-time)
* [Zvolte trvalost modelu](#which-persistence-model-should-i-choose)
* [Co se stane, pokud mají I škálovat na jinou velikost a je obnovit zálohu, která byla vytvořená před provedením operace škálování?](#what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation)


### <a name="rdb-persistence"></a>Trvalost RDB
* [Můžete změnit po vytvoření mezipaměti je četnost záloh RDB?](#can-i-change-the-rdb-backup-frequency-after-i-create-the-cache)
* [Proč Pokud četnost zálohování v RDB 60 minut je víc než 60 minut mezi zálohování?](#why-if-i-have-an-rdb-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups)
* [Co se stane starší zálohy RDB, když je proveden novou zálohu?](#what-happens-to-the-old-rdb-backups-when-a-new-backup-is-made)

### <a name="aof-persistence"></a>Trvalost AOF
* [Kdy je vhodné použít druhého účtu úložiště?](#when-should-i-use-a-second-storage-account)
* [Nemá vliv trvalost AOF v celé, latenci nebo výkonu Moje mezipaměti?](#does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache)
* [Jak můžete odebrat druhého účtu úložiště?](#how-can-i-remove-the-second-storage-account)
* [Co je přepisování a jak se týká Moje mezipaměti?](#what-is-a-rewrite-and-how-does-it-affect-my-cache)
* [Co lze očekávat při škálování mezipaměť s AOF povolené?](#what-should-i-expect-when-scaling-a-cache-with-aof-enabled)
* [Uspořádání AOF data v úložišti](#how-is-my-aof-data-organized-in-storage)


### <a name="can-i-enable-persistence-on-a-previously-created-cache"></a>Můžete povolit trvalost na dříve vytvořenou mezipaměti?
Ano, trvalosti Redis lze nakonfigurovat při vytváření mezipaměti i na existující prémiových mezipamětí.

### <a name="can-i-enable-aof-and-rdb-persistence-at-the-same-time"></a>Můžete povolit trvalost AOF a RDB současně?

Ne, můžete povolit pouze RDB nebo AOF, ale ne obojí ve stejnou dobu.

### <a name="which-persistence-model-should-i-choose"></a>Zvolte trvalost modelu

Trvalost AOF uloží každém zápisu do protokolu, který má určitý dopad na propustnost, porovnat s trvalost RDB, které ukládá zálohování podle nakonfigurovaný interval zálohování s minimálním dopadem na výkon. Trvalost AOF zvolte, pokud vaše primární cílem je minimalizovat ztrátu dat a dokáže zpracovat pokles v propustnost pro mezipaměť. Pokud chcete zachovat optimální propustnosti v mezipaměti, ale stále chcete mechanismus pro obnovení dat, vyberte trvalost RDB.

* Další informace o [výhody](https://redis.io/topics/persistence#rdb-advantages) a [nevýhody](https://redis.io/topics/persistence#rdb-disadvantages) z trvalost RDB.
* Další informace o [výhody](https://redis.io/topics/persistence#aof-advantages) a [nevýhody](https://redis.io/topics/persistence#aof-disadvantages) z AOF trvalost.

Další informace o výkonu při použití AOF trvalosti najdete v tématu [AOF nemá vliv trvalost v celé, latenci nebo výkonu Moje mezipaměti?](#does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache)

### <a name="what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation"></a>Co se stane, pokud mají I škálovat na jinou velikost a je obnovit zálohu, která byla vytvořená před provedením operace škálování?

RDB a AOF trvalosti:

* Pokud máte škálovat na větší velikost, neexistuje žádný vliv.
* Pokud máte škálovat na menší velikost, a máte vlastní [databáze](cache-configure.md#databases) nastavení, která je větší než [databáze limit](cache-configure.md#databases) pro novou velikost, není obnovit data v těchto databází. Další informace najdete v tématu [je vlastní databází. nastavení ovlivněných během změny měřítka?](cache-how-to-scale.md#is-my-custom-databases-setting-affected-during-scaling)
* Pokud máte škálovat na menší velikost a menší velikost pro uložení všech dat z poslední zálohy není dostatek místa, klíče bude vyloučena během procesu obnovení, obvykle pomocí [allkeys lru](http://redis.io/topics/lru-cache) zásady vyřazení.

### <a name="can-i-change-the-rdb-backup-frequency-after-i-create-the-cache"></a>Můžete změnit po vytvoření mezipaměti je četnost záloh RDB?
Ano, můžete změnit četnost záloh pro trvalost RDB na **trvalosti dat Redis** okno. Pokyny najdete v tématu [konfigurace Redis trvalost](#configure-redis-persistence).

### <a name="why-if-i-have-an-rdb-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups"></a>Proč Pokud četnost zálohování v RDB 60 minut je víc než 60 minut mezi zálohování?
Interval RDB trvalost četnost záloh se nespustí, dokud předchozí proces zálohování byla úspěšně dokončena. Pokud je četnost záloh je 60 minut a trvá 15 minut pro proces zálohování úspěšně dokončit, další zálohování nezačne 75 minut po času zahájení z předchozí zálohy.

### <a name="what-happens-to-the-old-rdb-backups-when-a-new-backup-is-made"></a>Co se stane starší zálohy RDB, když je proveden novou zálohu?
Všechny zálohy trvalost RDB s výjimkou nejnovějšího se automaticky odstraní. Odstranění nemusí dojít okamžitě, ale nejsou starší zálohy trvalé bez omezení.


### <a name="when-should-i-use-a-second-storage-account"></a>Kdy je vhodné použít druhého účtu úložiště?

AOF trvalosti měli použít druhý účet úložiště, pokud se domníváte, že máte vyšší než očekávaný množinové operace v mezipaměti.  Nastavení účtu sekundární úložiště pomáhá zajistit, že mezipaměť nebude dosáhnout omezení šířky pásma úložiště.

### <a name="does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache"></a>Nemá vliv trvalost AOF v celé, latenci nebo výkonu Moje mezipaměti?

Trvalost AOF ovlivní propustnost podle přibližně 15 % – 20 % při mezipaměti je menší než maximální zatížení (procesoru a Server načíst i v části 90 %). By neměl být problémy s latencí při mezipaměť je v rámci těchto mezních hodnot. Mezipaměti bude tyto limity však dříve dosáhnout s AOF povolena.

### <a name="how-can-i-remove-the-second-storage-account"></a>Jak můžete odebrat druhého účtu úložiště?

Můžete odebrat účet sekundární úložiště trvalosti AOF nastavením druhý účet úložiště, aby byla stejná jako první účet úložiště. Pokyny najdete v tématu [AOF konfigurace trvalosti](#configure-aof-persistence).

### <a name="what-is-a-rewrite-and-how-does-it-affect-my-cache"></a>Co je přepisování a jak se týká Moje mezipaměti?

Když se soubor AOF stane dostatečně velké na to, přepište je automaticky zařazeny do fronty v mezipaměti. Přepsání změní velikost souboru AOF s minimální sadu operací, které jsou nutné k vytvoření aktuální datové sady. Během přepisů očekává dříve dosažení omezení výkonu, zejména při plánování práce s velkými datovými sadami. Přepisů docházet k méně často AOF souboru se změní na větší, ale bude trvat poměrně dlouhou dobu, kdy se stane.

### <a name="what-should-i-expect-when-scaling-a-cache-with-aof-enabled"></a>Co lze očekávat při škálování mezipaměť s AOF povolené?

Pokud je soubor AOF v době škálování výrazně velký, pak očekávají, že se operace škálování trvá déle, než se očekávalo vzhledem k tomu, že se bude znovu načíst soubor po dokončení škálování.

Další informace o škálování najdete v tématu [co se stane, když I mít škálovat na jinou velikost a je obnovit zálohu, která byla vytvořená před provedením operace škálování?](#what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation)

### <a name="how-is-my-aof-data-organized-in-storage"></a>Uspořádání AOF data v úložišti

Data uložená v souborech AOF je rozdělen do více objekty BLOB stránky na uzel, pokud chcete zvýšit výkon při ukládání dat do úložiště. Následující tabulka zobrazuje, kolik objekty BLOB stránky se používají u jednotlivých cenových úrovní:

| Úroveň Premium | Objekty blob |
|--------------|-------|
| P1           | 4 na horizontální oddíl    |
| P2           | 8 na horizontální oddíl    |
| P3           | 16 na horizontální oddíl   |
| P4           | 20 na horizontální oddíl   |

Pokud je povoleno clustering, každý horizontálního oddílu v mezipaměti má vlastní sadu objektů BLOB stránky, jak je uvedeno v předchozí tabulce. Například P2 mezipaměť tři horizontálních oddílů rozděluje jeho AOF souboru na 24 objekty BLOB stránky (8 objekty BLOB na horizontální oddíl s 3 horizontálních oddílů).

Po přepsání dvě sady AOF soubory existují v úložišti. Přepisů probíhat na pozadí a připojit k první sadu souborů, zatímco připojení množinové operace, které se odesílají do mezipaměti při přepsání v druhé sadě. Zálohy se dočasně ukládají během přepisů v případě selhání, ale bude po dokončení přepište okamžitě odstraněna.


## <a name="next-steps"></a>Další postup
Naučte se používat další funkce mezipaměti premium.

* [Úvod do Azure Redis Cache na úrovni Premium](cache-premium-tier-intro.md)

<!-- IMAGES -->

[redis-cache-premium-pricing-tier]: ./media/cache-how-to-premium-persistence/redis-cache-premium-pricing-tier.png

[redis-cache-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-persistence.png

[redis-cache-rdb-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-rdb-persistence.png

[redis-cache-aof-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-aof-persistence.png

[redis-cache-settings]: ./media/cache-how-to-premium-persistence/redis-cache-settings.png
