---
title: Konfigurace trvalosti dat – Premium Azure cache pro Redis
description: Naučte se konfigurovat a spravovat Trvalost dat v mezipaměti Azure úrovně Premium pro instance Redis.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 08/24/2017
ms.openlocfilehash: aaee1c07f0fc8d5b0bba03550986291aea814fcb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88004795"
---
# <a name="how-to-configure-data-persistence-for-a-premium-azure-cache-for-redis"></a>Jak nakonfigurovat Trvalost dat pro mezipaměť Azure Premium pro Redis
Azure cache pro Redis má různé nabídky mezipaměti, které poskytují flexibilitu pro výběr velikosti a funkcí mezipaměti, včetně funkcí úrovně Premium, jako je podpora clusteringu, trvalosti a virtuální sítě. Tento článek popisuje, jak nakonfigurovat trvalost v mezipaměti Azure úrovně Premium pro instanci Redis.

## <a name="what-is-data-persistence"></a>Co je trvalost dat?
[Redis Persistence](https://redis.io/topics/persistence) umožňuje zachovat data uložená v Redis. Můžete také pořizovat snímky a zálohovat data, která můžete načíst v případě selhání hardwaru. Jedná se o obrovský přínos oproti úrovni Basic nebo Standard, kde jsou všechna data uložená v paměti a může dojít ke ztrátě dat v případě selhání, kde jsou uzly mezipaměti mimo provoz. 

Azure cache pro Redis nabízí trvalost Redis pomocí následujících modelů:

* **Trvalost RDB** – když je nakonfigurovaná trvalá databáze RDB (Redis Database), Azure cache pro Redis uchovává snímek mezipaměti Azure pro Redis v binárním formátu Redis na disk na základě konfigurovatelné četnosti zálohování. Pokud dojde k závažné události, která zakáže primární i mezipaměť repliky, mezipaměť se znovu vytvoří pomocí nejnovějšího snímku. Přečtěte si další informace o [výhodách](https://redis.io/topics/persistence#rdb-advantages) a [nevýhodách](https://redis.io/topics/persistence#rdb-disadvantages) trvalého chování RDB.
* **AOF Persistence** – Pokud je nakonfigurovaná trvalá stálost AOF (jenom soubor), mezipaměť Azure pro Redis ukládá každou operaci zápisu do protokolu, který se alespoň jednou uložil za sekundu do účtu Azure Storage. Pokud dojde k závažné události, která zakáže primární i mezipaměť repliky, mezipaměť se rekonstruovat pomocí uložených operací zápisu. Přečtěte si další informace o [výhodách](https://redis.io/topics/persistence#aof-advantages) a [nevýhodách](https://redis.io/topics/persistence#aof-disadvantages) AOF Persistence.

Trvalost zapisuje Redis data do účtu Azure Storage, který vlastníte a spravujete. Můžete nakonfigurovat z nového okna **Azure cache pro Redis** během vytváření mezipaměti a v **nabídce prostředků** pro existující mezipaměti úrovně Premium.

> [!NOTE]
> 
> Azure Storage automaticky šifruje data, když jsou trvalá. Pro šifrování můžete použít vlastní klíče. Další informace najdete v tématu [klíče spravované zákazníkem s Azure Key Vault](/azure/storage/common/storage-service-encryption).
> 
> 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

Po výběru cenové úrovně Premium klikněte na **Redis Persistence**.

![Redis Persistence][redis-cache-persistence]

Kroky v další části popisují, jak nakonfigurovat trvalost Redis pro novou mezipaměť Premium. Po nakonfigurování trvalosti Redis klikněte na **vytvořit** a vytvořte novou mezipaměť Premium s Redis persistencí.

## <a name="enable-redis-persistence"></a>Povolit trvalost Redis

Redis Persistence je povolená v okně **Trvalost dat** , a to tak, že vyberete buď **RDB** , nebo **AOF** Persistence. Pro nové mezipaměti je toto okno k dispozici během procesu vytváření mezipaměti, jak je popsáno v předchozí části. V případě existujících mezipamětí se k oknu **trvalá data** dostanete z **nabídky prostředků** pro vaši mezipaměť.

![Nastavení Redis][redis-cache-settings]


## <a name="configure-rdb-persistence"></a>Konfigurace trvalosti RDB

Chcete-li povolit trvalost RDB, klikněte na položku **RDB**. Pokud chcete zakázat trvalost RDB u dříve povolené mezipaměti Premium, klikněte na **disabled (zakázáno**).

![Trvalost Redis RDB][redis-cache-rdb-persistence]

Pokud chcete interval zálohování nakonfigurovat, vyberte v rozevíracím seznamu **četnost zálohování** . Volby zahrnují **15 minut**, **30 minut**, **60 minut**, **6 hodin**, **12 hodin**a **24 hodin**. Tento interval začíná počítat po úspěšném dokončení předchozí operace zálohování a při každém zahájení zálohování.

Klikněte na **účet úložiště** a vyberte účet úložiště, který se má použít, a vyberte **primární klíč** nebo **sekundární klíč** , který se použije v rozevíracím seznamu **klíč úložiště** . Musíte zvolit účet úložiště ve stejné oblasti, ve které je mezipaměť, a doporučuje se účet **Premium Storage** , protože Premium Storage má vyšší propustnost. 

> [!IMPORTANT]
> Pokud se znovu vygeneruje klíč úložiště pro váš účet trvalosti, musíte znovu nakonfigurovat požadovaný klíč z rozevíracího seznamu **klíč úložiště** .
> 
> 

Kliknutím na tlačítko **OK** uložte konfiguraci trvalosti.

Další zálohování (nebo první zálohování pro nové mezipaměti) se iniciuje po uplynutí intervalu četnosti zálohování.

## <a name="configure-aof-persistence"></a>Konfigurace trvalosti AOF

Pokud chcete povolit trvalost AOF, klikněte na **AOF**. Pokud chcete zakázat AOF Persistence u dříve povolené mezipaměti Premium, klikněte na **disabled (zakázáno**).

![Redis AOF Persistence][redis-cache-aof-persistence]

Pokud chcete nakonfigurovat trvalost AOF, zadejte **první účet úložiště**. Tento účet úložiště musí být ve stejné oblasti jako mezipaměť a doporučuje se účet **Premium Storage** , protože Premium Storage má vyšší propustnost. Volitelně můžete nakonfigurovat další účet úložiště s názvem **druhý účet úložiště**. Pokud je nakonfigurovaný druhý účet úložiště, zapisují se zápisy do mezipaměti repliky do tohoto druhého účtu úložiště. U každého nakonfigurovaného účtu úložiště vyberte **primární klíč** nebo **sekundární klíč** , který se použije v rozevíracím seznamu **klíč úložiště** . 

> [!IMPORTANT]
> Pokud se znovu vygeneruje klíč úložiště pro váš účet trvalosti, musíte znovu nakonfigurovat požadovaný klíč z rozevíracího seznamu **klíč úložiště** .
> 
> 

Když je povolená trvalost AOF, operace zápisu do mezipaměti se uloží do určeného účtu úložiště (nebo účtů, pokud jste nakonfigurovali druhý účet úložiště). V případě závažného selhání, které vychází z primární i mezipaměti repliky, se k opětovnému sestavení mezipaměti použije uložený protokol AOF.

## <a name="persistence-faq"></a>Nejčastější dotazy týkající se trvalosti
Následující seznam obsahuje odpovědi na nejčastější dotazy týkající se trvalosti Azure cache pro Redis.

* [Můžu pro dříve vytvořenou mezipaměť povolit trvalost?](#can-i-enable-persistence-on-a-previously-created-cache)
* [Můžu ve stejnou chvíli povolit AOF a trvalost RDB?](#can-i-enable-aof-and-rdb-persistence-at-the-same-time)
* [Který model trvalosti mám zvolit?](#which-persistence-model-should-i-choose)
* [Co se stane, když se škáluje na jinou velikost a obnoví se záloha, která byla provedena před operací škálování?](#what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation)


### <a name="rdb-persistence"></a>Trvalost RDB
* [Můžu po vytvoření mezipaměti změnit četnost záloh RDB?](#can-i-change-the-rdb-backup-frequency-after-i-create-the-cache)
* [Proč má frekvence zálohování RDB 60 minut více než 60 minut mezi zálohováním?](#why-if-i-have-an-rdb-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups)
* [Co se stane se starými zálohami v RDB, když se vytvoří nová záloha?](#what-happens-to-the-old-rdb-backups-when-a-new-backup-is-made)

### <a name="aof-persistence"></a>AOF Persistence
* [Kdy mám použít druhý účet úložiště?](#when-should-i-use-a-second-storage-account)
* [Ovlivňuje trvalá stálost AOF celou dobu, latenci nebo výkon moje mezipaměti?](#does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache)
* [Jak můžu odebrat druhý účet úložiště?](#how-can-i-remove-the-second-storage-account)
* [Co je přepsání a jak má vliv na moji mezipaměť?](#what-is-a-rewrite-and-how-does-it-affect-my-cache)
* [Co mám očekávat při škálování mezipaměti s povoleným AOF?](#what-should-i-expect-when-scaling-a-cache-with-aof-enabled)
* [Jak se moje AOF data uspořádávají do úložiště?](#how-is-my-aof-data-organized-in-storage)


### <a name="can-i-enable-persistence-on-a-previously-created-cache"></a>Můžu pro dříve vytvořenou mezipaměť povolit trvalost?
Ano, trvalost Redis je možné nakonfigurovat při vytváření mezipaměti i v existujících mezipamětí Premium.

### <a name="can-i-enable-aof-and-rdb-persistence-at-the-same-time"></a>Můžu ve stejnou chvíli povolit AOF a trvalost RDB?

Ne, můžete povolit pouze RDB nebo AOF, ale ne obě současně.

### <a name="which-persistence-model-should-i-choose"></a>Který model trvalosti mám zvolit?

AOF persistenc ukládá každý zápis do protokolu, který má dopad na propustnost, ve srovnání s persistencí RDB, který ukládá zálohy na základě nakonfigurovaného intervalu zálohování s minimálním dopadem na výkon. Pokud je vaším primárním cílem minimalizovat ztrátu dat, můžete zvolit AOF Persistence a snížit propustnost pro vaši mezipaměť. Možnost trvalosti RDB vyberte, pokud chcete zachovat optimální propustnost v mezipaměti, ale přesto chcete mít mechanismus pro obnovení dat.

* Přečtěte si další informace o [výhodách](https://redis.io/topics/persistence#rdb-advantages) a [nevýhodách](https://redis.io/topics/persistence#rdb-disadvantages) trvalého chování RDB.
* Přečtěte si další informace o [výhodách](https://redis.io/topics/persistence#aof-advantages) a [nevýhodách](https://redis.io/topics/persistence#aof-disadvantages) AOF Persistence.

Další informace o výkonu při používání AOF Persistence najdete v tématu [AOF trvalosti vlivu na celou dobu, latenci nebo výkon této mezipaměti?](#does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache)

### <a name="what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation"></a>Co se stane, když se škáluje na jinou velikost a obnoví se záloha, která byla provedena před operací škálování?

Pro zachování RDB i AOF:

* Pokud jste škálované na větší velikost, nebude to mít žádný vliv.
* Pokud jste škálované na menší velikost a máte nastavení vlastní [databáze](cache-configure.md#databases) , které je větší než [limit databází](cache-configure.md#databases) pro novou velikost, data v těchto databázích nebudou obnovena. Další informace najdete v tématu [Nastavení moje vlastní databáze ovlivněná během škálování?](cache-how-to-scale.md#is-my-custom-databases-setting-affected-during-scaling)
* Pokud jste škálované na menší velikost a v menší velikosti není dost místa pro uchovávání všech dat od poslední zálohy, klíče se během procesu obnovení vyřadí, obvykle se použijí zásady vyřazení [AllKeys-LRU](https://redis.io/topics/lru-cache) .

### <a name="can-i-change-the-rdb-backup-frequency-after-i-create-the-cache"></a>Můžu po vytvoření mezipaměti změnit četnost záloh RDB?
Ano, četnost záloh pro trvalost RDB můžete změnit v okně **trvalá data** . Pokyny najdete v tématu Konfigurace Persistence Redis.

### <a name="why-if-i-have-an-rdb-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups"></a>Proč má frekvence zálohování RDB 60 minut více než 60 minut mezi zálohováním?
Interval četnosti zálohování RDB se nespustí, dokud se předchozí proces zálohování úspěšně nedokončí. Pokud je frekvence zálohování 60 minut a trvá dokončení procesu zálohování 15 minut, příští zálohování nebude zahájeno až do 75 minut od počátečního času předchozí zálohy.

### <a name="what-happens-to-the-old-rdb-backups-when-a-new-backup-is-made"></a>Co se stane se starými zálohami v RDB, když se vytvoří nová záloha?
Všechna trvalá zálohování RDB s výjimkou nejaktuálnějšího se odstraní automaticky. K odstranění nemusí dojít hned, ale starší zálohy se neukládají neomezeně.


### <a name="when-should-i-use-a-second-storage-account"></a>Kdy mám použít druhý účet úložiště?

Pokud se domníváte, že máte nad mezipamětí více než očekávanou operaci set, měli byste použít druhý účet úložiště pro AOF Persistence.  Nastavení sekundárního účtu úložiště pomůže zajistit, že vaše mezipaměť nedosáhne limitů šířky pásma úložiště.

### <a name="does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache"></a>Ovlivňuje trvalá stálost AOF celou dobu, latenci nebo výkon moje mezipaměti?

AOF Persistence má vliv na propustnost přibližně o 15% – 20%, pokud je mezipaměť nižší než maximální zatížení (zatížení procesoru a serveru v rámci 90%). Pokud je mezipaměť v rámci těchto limitů, nemělo by dojít k problémům s latencí. Mezipaměť ale dosáhne těchto limitů dřív, než je povolený AOF.

### <a name="how-can-i-remove-the-second-storage-account"></a>Jak můžu odebrat druhý účet úložiště?

Sekundární účet úložiště AOF Persistence můžete odebrat tak, že nastavíte druhý účet úložiště tak, aby byl stejný jako první účet úložiště. Pokyny najdete v tématu [Konfigurace Persistence AOF](#configure-aof-persistence).

### <a name="what-is-a-rewrite-and-how-does-it-affect-my-cache"></a>Co je přepsání a jak má vliv na moji mezipaměť?

Když je soubor AOF dostatečně velký, přepisování se v mezipaměti automaticky zařadí do fronty. Přepsání změní velikost souboru AOF s minimální sadou operací potřebných k vytvoření aktuální datové sady. Během přepisu očekáváte, že při práci s velkými datovými sadami dosáhnete limitů výkonu, a to dřív zvlášť. Přepsání se projeví méně často, protože soubor AOF je větší, ale při výskytu bude trvat poměrně dlouhou dobu.

### <a name="what-should-i-expect-when-scaling-a-cache-with-aof-enabled"></a>Co mám očekávat při škálování mezipaměti s povoleným AOF?

Pokud je soubor AOF v době škálování výrazně velký, očekává se, že operace škálování bude trvat déle, než se očekávalo, protože po dokončení škálování se soubor znovu načte.

Další informace o škálování najdete v tématu [co se stane, když se škáluje na jinou velikost a že se obnovila záloha, která byla provedena před operací škálování?](#what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation)

### <a name="how-is-my-aof-data-organized-in-storage"></a>Jak se moje AOF data uspořádávají do úložiště?

Data uložená v souborech AOF se dělí na několik objektů blob stránky na jeden uzel, aby se zvýšil výkon ukládání dat do úložiště. Následující tabulka uvádí, kolik objektů blob stránky se používá pro každou cenovou úroveň:

| Úroveň Premium | Objekty blob |
|--------------|-------|
| P1           | 4 na horizontálních oddílů    |
| P2           | 8 per horizontálních oddílů    |
| P3           | 16 na horizontálních oddílů   |
| P4           | 20 na horizontálních oddílů   |

Když je clustering povolený, každá horizontálních oddílů v mezipaměti má svou vlastní sadu objektů blob stránky, jak je uvedeno v předchozí tabulce. Například mezipaměť P2 se třemi horizontálních oddílů distribuuje svůj AOF soubor v rámci 24 objektů blob stránky (8 objektů blob na horizontálních oddílů, se 3 horizontálních oddílů).

Po přepisování existují v úložišti dvě sady souborů AOF. Přepisy se vyskytují na pozadí a připojují se k první sadě souborů a při nastavení operací, které jsou odesílány do mezipaměti během zápisu do druhé sady. Zálohování se dočasně ukládá během přepisů v případě selhání, ale po dokončení přepisu se zobrazí výzva.


## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o funkcích Azure cache pro Redis.

* [Mezipaměť Azure pro úrovně služeb Redis Premium](cache-overview.md#service-tiers)

<!-- IMAGES -->

[redis-cache-premium-pricing-tier]: ./media/cache-how-to-premium-persistence/redis-cache-premium-pricing-tier.png

[redis-cache-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-persistence.png

[redis-cache-rdb-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-rdb-persistence.png

[redis-cache-aof-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-aof-persistence.png

[redis-cache-settings]: ./media/cache-how-to-premium-persistence/redis-cache-settings.png
