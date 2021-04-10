---
title: Omezení a omezení – Citus (Velká měřítko) – Azure Database for PostgreSQL
description: Aktuální limity pro skupiny serverů Citus ()
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: cfd57c60c4bfd60976eb28822c696412cabda212
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2021
ms.locfileid: "107023946"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-limits-and-limitations"></a>Azure Database for PostgreSQL – omezení a omezení Citus (-Scale)

Následující část popisuje kapacitu a funkční omezení služby Citus (The scaleing).

## <a name="maximum-connections"></a>Maximální počet připojení

Každé připojení PostgreSQL (dokonce nečinné) využívá alespoň 10 MB paměti, takže je důležité omezit souběžná připojení. Tady jsou limity, které jsme zvolili pro udržení uzlů v pořádku:

* Uzel koordinátora
   * Maximální počet připojení: 300
   * Maximální počet uživatelských připojení: 297
* Pracovní uzel
   * Maximální počet připojení: 600
   * Maximální počet uživatelských připojení: 597

> [!NOTE]
> Ve skupině serverů s povolenými [funkcemi verze Preview](hyperscale-preview-features.md) se omezení připojení ke koordinátorovi mírně liší:
>
> * Maximální počet připojení uzlu Coordinator
>    * 300 pro 0-3 virtuální jádra
>    * 500 pro 4-15 virtuální jádra
>    * 1000 pro 16 virtuální jádra

Pokusy o připojení nad rámec těchto limitů selžou a dojde k chybě. Systém vyhrazuje tři připojení pro uzly monitorování, což je důvod, proč jsou k dispozici tři méně připojení pro uživatelské dotazy než celkem připojení.

### <a name="connection-pooling"></a>Sdružování připojení

Vytváření nových připojení trvá déle. Který funguje u většiny aplikací, které vyžadují mnoho krátkodobých připojení. Doporučujeme použít Pooler připojení, aby se snížily nečinné transakce a znovu opakovaně používaly existující připojení. Další informace najdete v našem [blogovém příspěvku](https://techcommunity.microsoft.com/t5/azure-database-for-postgresql/not-all-postgres-connection-pooling-is-equal/ba-p/825717).

Můžete spustit vlastní připojení Pooler nebo použít PgBouncer spravované v Azure.

#### <a name="managed-pgbouncer-preview"></a>Spravované PgBouncer (Preview)

> [!IMPORTANT]
> Spravované připojení PgBouncer Pooler v (Citus) je aktuálně ve verzi Preview. Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
>
> Úplný seznam dalších nových funkcí najdete v části [funkce ve verzi Preview pro Citus (škálování)](hyperscale-preview-features.md).

Fondy připojení, jako je PgBouncer, umožňují více klientům připojit se k uzlu koordinátora najednou. Aplikace se připojují k Pooler a příkazy Pooler přenáší do cílové databáze.

Když se klienti připojují přes PgBouncer, počet připojení, která se aktivně spouštějí v databázi, se nezmění. Místo toho PgBouncer fronty nadbytečná připojení a spustí je, když je databáze připravena.

Citus () teď nabízí spravovanou instanci PgBouncer pro skupiny serverů (ve verzi Preview). Podporuje až 2 000 současných připojení klientů.
K připojení prostřednictvím PgBouncer postupujte takto:

1. Přejít na stránku **připojovací řetězce** pro skupinu serverů v Azure Portal.
2. Zaškrtněte políčko **připojovací řetězce PgBouncer**. (Uvedené připojovací řetězce se změní.)
3. Aktualizujte klientské aplikace pro připojení k novému řetězci.

## <a name="storage-scaling"></a>Škálování úložiště

Úložiště v koordinátoru a uzlech pracovních procesů je možné škálovat nahoru (vyšší), ale nedá se škálovat dolů (zmenšeno).

## <a name="storage-size"></a>Velikost úložiště

V koordinátorech a uzlech pracovních procesů je podporováno až 2 TiB úložiště. V části dostupné možnosti úložiště a výpočet IOPS [výše](concepts-hyperscale-configuration-options.md#compute-and-storage) najdete pro velikost uzlů a clusterů.

## <a name="database-creation"></a>Vytvoření databáze

Azure Portal poskytuje přihlašovací údaje pro připojení k přesně jedné databázi na skupinu serverů Citus (databáze), `citus` databázi. Vytvoření jiné databáze není aktuálně povoleno a příkaz CREATE DATABASE selže a dojde k chybě.

## <a name="columnar-storage"></a>Sloupcové úložiště

Citus má v současnosti tato omezení se [sloupcovými tabulkami](concepts-hyperscale-columnar.md):

* Komprese je na disku, nikoli v paměti.
* Pouze připojení (bez podpory aktualizace/odstranění)
* Bez recyklace prostoru (například převedené transakce se změnami může stále spotřebovávat místo na disku)
* Žádná podpora indexů, prohledávání indexu nebo skenování rastrového obrázku
* Žádné tidscans
* Žádná ukázková prohledávání
* Podpora informačních zpráv není podporovaná (vložené velké hodnoty nejsou podporované.)
* Žádná podpora pro výpisy konfliktů (s výjimkou akcí nečinnosti bez určení cíle).
* Žádná podpora pro zámky řazené kolekce členů (vybrat... PRO SDÍLENOU SLOŽKU VYBERTE... PRO AKTUALIZACI)
* Žádná podpora serializovatelné úrovně izolace
* Podpora pouze pro server PostgreSQL verze 12 +
* Žádná podpora pro cizí klíče, jedinečná omezení nebo omezení vyloučení
* Žádná podpora pro logické dekódování
* Žádná podpora paralelních kontrol v rámci uzlů
* Žádná podpora pro AFTER... PRO jednotlivé triggery řádků
* Žádné nezaznamenané sloupcové tabulky
* Žádné dočasné sloupcové tabulky

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak na [portálu vytvořit skupinu serverů na úrovni Citus ()](quickstart-create-hyperscale-portal.md).
