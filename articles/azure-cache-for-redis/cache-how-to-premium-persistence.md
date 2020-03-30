---
title: Konfigurace trvalosti dat – premium Azure Cache for Redis
description: Zjistěte, jak nakonfigurovat a spravovat trvalost dat, vaše instance Azure Cache úrovně Premium pro redis
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 08/24/2017
ms.openlocfilehash: 84a5b4784a36fb22ae50a7a1ec4fcb7e5ef5b7c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80245272"
---
# <a name="how-to-configure-data-persistence-for-a-premium-azure-cache-for-redis"></a>Konfigurace trvalosti dat pro prémiovou mezipaměť Azure pro Redis
Azure Cache for Redis má různé nabídky mezipaměti, které poskytují flexibilitu při výběru velikosti mezipaměti a funkcí, včetně funkcí úrovně Premium, jako je clustering, trvalost a podpora virtuálních sítí. Tento článek popisuje, jak nakonfigurovat trvalost v premium Azure Cache pro redis instance.

Informace o dalších funkcích mezipaměti premium najdete [v tématu Úvod do azure cache pro úroveň Redis Premium](cache-premium-tier-intro.md).

## <a name="what-is-data-persistence"></a>Co je trvalost dat?
[Redis perzis umožňuje](https://redis.io/topics/persistence) zachovat data uložená v Redis. Můžete také pořizovat snímky a zálohovat data, která můžete načíst v případě selhání hardwaru. To je obrovská výhoda oproti základní nebo standardní úroveň, kde jsou všechna data uložena v paměti a může dojít ke ztrátě potenciálních dat v případě selhání, kde jsou uzly mezipaměti mimo provoz. 

Azure Cache for Redis nabízí trvalost Redis pomocí následujících modelů:

* **Trvalost RDB** – při konfiguraci trvalosti rdb (databáze Redis) Azure Cache for Redis zachová snímek azure cache pro Redis v binárním formátu Redis na disk na základě konfigurovatelné frekvence zálohování. Pokud dojde ke katastrofické události, která zakáže primární i replikovou mezipaměť, bude mezipaměť rekonstruována pomocí nejnovějšího snímku. Přečtěte si další informace o [výhodách](https://redis.io/topics/persistence#rdb-advantages) a [nevýhodách](https://redis.io/topics/persistence#rdb-disadvantages) trvalosti RDB.
* **AOF trvalost** – když je nakonfigurována trvalost AOF (připojit pouze soubor), Azure Cache for Redis uloží každou operaci zápisu do protokolu, který se uloží alespoň jednou za sekundu do účtu Azure Storage. Pokud dojde ke katastrofické události, která zakáže primární i replikovou mezipaměť, bude mezipaměť rekonstruována pomocí uložených operací zápisu. Další informace o [výhodách](https://redis.io/topics/persistence#aof-advantages) a [nevýhodách](https://redis.io/topics/persistence#aof-disadvantages) trvalosti AOF.

Trvalá nařizovat e-mailem zapisuje data Redis do účtu Azure Storage, který vlastníte a spravujete. Můžete nakonfigurovat z **okna New Azure Cache for Redis** během vytváření mezipaměti a v **nabídce Prostředky** pro existující mezipaměti premium.

> [!NOTE]
> 
> Azure Storage automaticky šifruje data, když je trvalá. Pro šifrování můžete použít vlastní klíče. Další informace naleznete v [tématu Klíče spravované zákazníky pomocí služby Azure Key Vault](/azure/storage/common/storage-service-encryption).
> 
> 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

Po výběru úrovně prémiové ceny klepněte na **položku Trvalost redis**.

![Redis vytrvalost][redis-cache-persistence]

Kroky v další části popisují, jak nakonfigurovat trvalost redis u nové mezipaměti premium. Po konfiguraci trvalosti redisu klikněte na **Vytvořit** a vytvořte novou mezipaměť premium s trvalostí redisu.

## <a name="enable-redis-persistence"></a>Povolit trvalost Redis

Trvalosti redis je povolena na datové okno **trvalosti dat** výběrem trvalosti **RDB** nebo **AOF.** U nových mezipamětí je toto okno přístupné během procesu vytváření mezipaměti, jak je popsáno v předchozí části. U existujících mezipamětí je okno **Trvalosti dat** přístupné z **nabídky Prostředek** pro vaši mezipaměť.

![Nastavení Redis][redis-cache-settings]


## <a name="configure-rdb-persistence"></a>Konfigurace trvalosti rdb

Chcete-li povolit trvalost RDB, klepněte na tlačítko **RDB**. Chcete-li zakázat trvalost rdb v dříve povolené mezipaměti pro prémii, klepněte na tlačítko **Zakázáno**.

![Trvalost Redis RDB][redis-cache-rdb-persistence]

Chcete-li nakonfigurovat interval zálohování, vyberte frekvenci **zálohování** z rozevíracího seznamu. Volby zahrnují **15 minut**, **30 minut**, **60 minut**, 6 **hodin**, **12 hodin**a **24 hodin**. Tento interval začíná odpočítávání po úspěšném dokončení předchozí operace zálohování a po ukončení nové zálohy.

Kliknutím na **Účet úložiště** vyberte účet úložiště, který chcete použít, a v rozevíracím tématu **Klíč úložiště** vyberte buď primární **klíč,** nebo **sekundární klíč.** Musíte zvolit účet úložiště ve stejné oblasti jako mezipaměť a účet **úložiště Premium** se doporučuje, protože úložiště premium má vyšší propustnost. 

> [!IMPORTANT]
> Pokud je klíč úložiště pro váš účet trvalosti znovu vygenerován, je nutné změnit konfiguraci požadovaného klíče z rozevíracího souboru **Klíč úložiště.**
> 
> 

Klepnutím na **tlačítko OK** uložte konfiguraci trvalosti.

Další záloha (nebo první záloha pro nové mezipaměti) je zahájena po uplynutí intervalu frekvence zálohování.

## <a name="configure-aof-persistence"></a>Konfigurovat trvalost aof

Chcete-li povolit trvalost aof, klepněte na tlačítko **AOF**. Chcete-li zakázat trvalost aof v dříve povolené mezipaměti pro prémii, klepněte na tlačítko **Zakázáno**.

![Trvalost Redis AOF][redis-cache-aof-persistence]

Chcete-li konfigurovat trvalost AOF, zadejte **první účet úložiště**. Tento účet úložiště musí být ve stejné oblasti jako mezipaměť a účet **úložiště Premium** se doporučuje, protože úložiště úrovně Premium má vyšší propustnost. Volitelně můžete nakonfigurovat další účet úložiště s názvem **Druhý účet úložiště**. Pokud je nakonfigurován druhý účet úložiště, zápisy do mezipaměti repliky jsou zapsány do tohoto druhého účtu úložiště. Pro každý nakonfigurovaný účet úložiště zvolte buď **primární klíč,** nebo **sekundární klíč,** který chcete použít z rozevíracího prostředí **Klíč úložiště.** 

> [!IMPORTANT]
> Pokud je klíč úložiště pro váš účet trvalosti znovu vygenerován, je nutné změnit konfiguraci požadovaného klíče z rozevíracího souboru **Klíč úložiště.**
> 
> 

Pokud je povolena trvalosti AOF, operace zápisu do mezipaměti jsou uloženy do určeného účtu úložiště (nebo účty, pokud jste nakonfigurovali druhý účet úložiště). V případě katastrofické selhání, které převezme primární i replika mezipaměti, uložené AOF protokolu se používá k opětovnému sestavení mezipaměti.

## <a name="persistence-faq"></a>Nejčastější dotazy týkající se trvalosti
Následující seznam obsahuje odpovědi na běžně kladené otázky týkající se Azure Cache for Redis trvalosti.

* [Mohu povolit trvalost v dříve vytvořené mezipaměti?](#can-i-enable-persistence-on-a-previously-created-cache)
* [Mohu současně povolit trvalost AOF a RDB?](#can-i-enable-aof-and-rdb-persistence-at-the-same-time)
* [Jaký model perzistence si mám vybrat?](#which-persistence-model-should-i-choose)
* [Co se stane, pokud došlo k zmenšení na jinou velikost a obnovení zálohy, která byla provedena před operací škálování?](#what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation)


### <a name="rdb-persistence"></a>Trvalost RDB
* [Mohu po vytvoření mezipaměti změnit frekvenci zálohování RDB?](#can-i-change-the-rdb-backup-frequency-after-i-create-the-cache)
* [Proč, když mám frekvenci zálohování RDB 60 minut, mezi zálohami je více než 60 minut?](#why-if-i-have-an-rdb-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups)
* [Co se stane se starými zálohami RDB, když je provedena nová záloha?](#what-happens-to-the-old-rdb-backups-when-a-new-backup-is-made)

### <a name="aof-persistence"></a>AOF trvalost
* [Kdy mám použít druhý účet úložiště?](#when-should-i-use-a-second-storage-account)
* [Má aof trvalost ovlivnit v celém, latence nebo výkon mé mezipaměti?](#does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache)
* [Jak můžu odebrat druhý účet úložiště?](#how-can-i-remove-the-second-storage-account)
* [Co je přepsání a jak ovlivňuje mou mezipaměť?](#what-is-a-rewrite-and-how-does-it-affect-my-cache)
* [Co mám očekávat při škálování mezipaměti s povolenou funkcí AOF?](#what-should-i-expect-when-scaling-a-cache-with-aof-enabled)
* [Jak jsou moje data AOF uspořádána v úložišti?](#how-is-my-aof-data-organized-in-storage)


### <a name="can-i-enable-persistence-on-a-previously-created-cache"></a>Mohu povolit trvalost v dříve vytvořené mezipaměti?
Ano, trvalost Redis lze nakonfigurovat při vytváření mezipaměti i na existujících mezipamětích premium.

### <a name="can-i-enable-aof-and-rdb-persistence-at-the-same-time"></a>Mohu současně povolit trvalost AOF a RDB?

Ne, můžete povolit pouze RDB nebo AOF, ale ne obojí současně.

### <a name="which-persistence-model-should-i-choose"></a>Jaký model perzistence si mám vybrat?

AOF trvalost uloží každý zápis do protokolu, což má určitý dopad na propustnost, ve srovnání s trvalost RDB, která šetří zálohy na základě nakonfigurovaného intervalu zálohování, s minimálním dopadem na výkon. Pokud je primárním cílem minimalizace ztráty dat, můžete zvolte trvalost aof, pokud je vaším primárním cílem minimalizovat ztrátu dat a můžete zpracovat snížení propustnosti mezipaměti. Zvolte trvalost RDB, pokud chcete zachovat optimální propustnost v mezipaměti, ale přesto chcete mechanismus pro obnovu dat.

* Přečtěte si další informace o [výhodách](https://redis.io/topics/persistence#rdb-advantages) a [nevýhodách](https://redis.io/topics/persistence#rdb-disadvantages) trvalosti RDB.
* Další informace o [výhodách](https://redis.io/topics/persistence#aof-advantages) a [nevýhodách](https://redis.io/topics/persistence#aof-disadvantages) trvalosti AOF.

Další informace o výkonu při použití trvalosti AOF naleznete v [tématu Má aof trvalost ovlivnit v celém, latence nebo výkon mé mezipaměti?](#does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache)

### <a name="what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation"></a>Co se stane, pokud došlo k zmenšení na jinou velikost a obnovení zálohy, která byla provedena před operací škálování?

Pro perzistenci RDB i AOF:

* Pokud jste škálovali na větší velikost, neexistuje žádný dopad.
* Pokud jste škálovali na menší velikost a máte vlastní [databáze](cache-configure.md#databases) nastavení, které je větší než [limit databáze](cache-configure.md#databases) pro novou velikost, data v těchto databázích není obnovena. Další informace naleznete v [tématu Je nastavení vlastních databází ovlivněno během škálování?](cache-how-to-scale.md#is-my-custom-databases-setting-affected-during-scaling)
* Pokud jste škálovali na menší velikost a v menší velikosti není dostatek místa pro uložení všech dat z poslední zálohy, klíče budou během procesu obnovení vyřazeny, obvykle pomocí zásady vyřazení [allkeys-lru.](https://redis.io/topics/lru-cache)

### <a name="can-i-change-the-rdb-backup-frequency-after-i-create-the-cache"></a>Mohu po vytvoření mezipaměti změnit frekvenci zálohování RDB?
Ano, můžete změnit frekvenci zálohování pro trvalost RDB v okně **trvalosti dat.** Pokyny naleznete v tématu Konfigurace trvalosti redisu.

### <a name="why-if-i-have-an-rdb-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups"></a>Proč, když mám frekvenci zálohování RDB 60 minut, mezi zálohami je více než 60 minut?
Interval frekvence zálohování trvalost RDB se nespustí, dokud nebude úspěšně dokončen předchozí proces zálohování. Pokud je frekvence zálohování 60 minut a proces zálohování trvá 15 minut, další záloha se spustí až 75 minut po počátečním čase předchozí zálohy.

### <a name="what-happens-to-the-old-rdb-backups-when-a-new-backup-is-made"></a>Co se stane se starými zálohami RDB, když je provedena nová záloha?
Všechny zálohy trvalosti RDB s výjimkou poslední ho automaticky odstraní. K tomuto odstranění nemusí dojít okamžitě, ale starší zálohy nejsou trvalé neomezeně dlouho.


### <a name="when-should-i-use-a-second-storage-account"></a>Kdy mám použít druhý účet úložiště?

Měli byste použít druhý účet úložiště pro trvalost AOF, pokud se domníváte, že máte vyšší než očekávané operace nastavení v mezipaměti.  Nastavení účtu sekundárního úložiště pomáhá zajistit, aby vaše mezipaměť nedosáhla omezení šířky pásma úložiště.

### <a name="does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache"></a>Má aof trvalost ovlivnit v celém, latence nebo výkon mé mezipaměti?

AOF trvalost ovlivňuje propustnost o přibližně 15 % – 20 %, pokud je mezipaměť nižší než maximální zatížení (zatížení procesoru a serveru pod 90 %). Při ukládání do mezipaměti v těchto limitech by neměly existovat problémy s latencí. Mezipaměť však dosáhne těchto limitů dříve s povolenou službou AOF.

### <a name="how-can-i-remove-the-second-storage-account"></a>Jak můžu odebrat druhý účet úložiště?

Účet sekundárního úložiště AOF můžete odebrat nastavením druhého účtu úložiště na stejný jako první účet úložiště. Pokyny naleznete v [tématu Konfigurace trvalosti aof](#configure-aof-persistence).

### <a name="what-is-a-rewrite-and-how-does-it-affect-my-cache"></a>Co je přepsání a jak ovlivňuje mou mezipaměť?

Pokud je soubor AOF dostatečně velký, je přepsáno automaticky zařazeno do mezipaměti. Přepsání změní velikost souboru AOF s minimální sadou operací potřebných k vytvoření aktuální datové sady. Během přepisování očekávejte, že dosáhnete omezení výkonu dříve, zejména při práci s velkými datovými sadami. K přepisům dochází méně často, protože soubor AOF se zvětšuje, ale bude trvat značné množství času, když se to stane.

### <a name="what-should-i-expect-when-scaling-a-cache-with-aof-enabled"></a>Co mám očekávat při škálování mezipaměti s povolenou funkcí AOF?

Pokud soubor AOF v době škálování je výrazně velký, pak očekávejte, že operace škálování trvat déle, než bylo očekáváno, protože bude znovu načítat soubor po dokončení škálování.

Další informace o škálování najdete v tématu [Co se stane, když mám měřítko na jinou velikost a obnovení zálohy, která byla provedena před operací škálování?](#what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation)

### <a name="how-is-my-aof-data-organized-in-storage"></a>Jak jsou moje data AOF uspořádána v úložišti?

Data uložená v souborech AOF jsou rozdělena do více objektů BLOB stránky na uzel, aby se zvýšil výkon ukládání dat do úložiště. V následující tabulce je zobrazeno, kolik objektů BLOB stránky se používá pro každou cenovou úroveň:

| Úroveň Premium | Objekty blob |
|--------------|-------|
| P1           | 4 na střep    |
| P2           | 8 na střep    |
| P3           | 16 na střep   |
| P4           | 20 na střep   |

Je-li clustering povolen, má každý úlomek v mezipaměti vlastní sadu objektů BLOB stránky, jak je uvedeno v předchozí tabulce. Například mezipaměť P2 se třemi úlomky distribuuje svůj soubor AOF přes 24 objektů BLOB stránky (8 objektů BLOB na šiřidlo, se třemi úlomky).

Po přepsání existují v úložišti dvě sady souborů AOF. K přepsání dochází na pozadí a připojit k první sadě souborů, zatímco nastavit operace, které jsou odesílány do mezipaměti během přepsání připojit k druhé sadě. Záloha je dočasně uložena během přepisů v případě selhání, ale po dokončení přepsání je okamžitě odstraněna.


## <a name="next-steps"></a>Další kroky
Přečtěte si, jak používat více funkcí prémiové mezipaměti.

* [Úvod do azure cache pro úroveň Redis Premium](cache-premium-tier-intro.md)

<!-- IMAGES -->

[redis-cache-premium-pricing-tier]: ./media/cache-how-to-premium-persistence/redis-cache-premium-pricing-tier.png

[redis-cache-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-persistence.png

[redis-cache-rdb-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-rdb-persistence.png

[redis-cache-aof-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-aof-persistence.png

[redis-cache-settings]: ./media/cache-how-to-premium-persistence/redis-cache-settings.png
