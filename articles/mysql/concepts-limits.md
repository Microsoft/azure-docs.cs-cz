---
title: Omezení v Azure databáze pro databázi MySQL
description: Tento článek popisuje omezení v Azure Database pro databázi MySQL, jako je například počet připojení a možnosti úložiště modul.
services: mysql
author: kamathsun
ms.author: sukamat
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 03/20/2018
ms.openlocfilehash: 2fa69182b4238cfd19fcc9571e4327512e9528c1
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="limitations-in-azure-database-for-mysql"></a>Omezení v Azure databáze pro databázi MySQL
Následující části popisují kapacity, modul úložiště s podporou, oprávnění podpory, podpora příkaz manipulaci dat a funkční omezení ve službě databázového. Viz také [obecná omezení](https://dev.mysql.com/doc/mysql-reslimits-excerpt/5.6/en/limits.html) vztahuje k modulu databáze MySQL.

## <a name="service-tier-maximums"></a>Maximální hodnoty úroveň služby
Azure databáze MySQL, má více úrovní služeb, které lze vybírat při vytváření serveru. Další informace najdete v tématu [Azure Database pro databázi MySQL cenové úrovně](concepts-pricing-tiers.md).  

Takto je maximální počet připojení, výpočetní jednotky a úložiště v jednotlivých úrovních služeb: 

|**Cenová úroveň**| **Výpočetní generování**|**vCore(s)**| **Maximální počet připojení**|
|---|---|---|---|
|Basic| Gen 4| 1| 50|
|Basic| Gen 4| 2| 100|
|Basic| Gen 5| 1| 50|
|Basic| Gen 5| 2| 100|
|Obecné použití| Gen 4| 2| 300|
|Obecné použití| Gen 4| 4| 625|
|Obecné použití| Gen 4| 8| 1250|
|Obecné použití| Gen 4| 16| 2500|
|Obecné použití| Gen 4| 32| 5000|
|Obecné použití| Gen 5| 2| 300|
|Obecné použití| Gen 5| 4| 625|
|Obecné použití| Gen 5| 8| 1250|
|Obecné použití| Gen 5| 16| 2500|
|Obecné použití| Gen 5| 32| 5000|
|Paměťově optimalizované| Gen 5| 2| 600|
|Paměťově optimalizované| Gen 5| 4| 1250|
|Paměťově optimalizované| Gen 5| 8| 2500|
|Paměťově optimalizované| Gen 5| 16| 5000|

Když se dosáhne příliš mnoha připojení, může se zobrazit chybová zpráva:
> Chyba 1040 (08004): Příliš mnoho připojení

## <a name="storage-engine-support"></a>Podpora modulu úložiště

### <a name="supported"></a>Podporováno
- [InnoDB](https://dev.mysql.com/doc/refman/5.7/en/innodb-introduction.html)
- [MEMORY](https://dev.mysql.com/doc/refman/5.7/en/memory-storage-engine.html)

### <a name="unsupported"></a>Nepodporovaný
- [MyISAM](https://dev.mysql.com/doc/refman/5.7/en/myisam-storage-engine.html)
- [BLACKHOLE](https://dev.mysql.com/doc/refman/5.7/en/blackhole-storage-engine.html)
- [ARCHIV](https://dev.mysql.com/doc/refman/5.7/en/archive-storage-engine.html)
- [FEDEROVANÉ](https://dev.mysql.com/doc/refman/5.7/en/federated-storage-engine.html)

## <a name="privilege-support"></a>Podpora oprávnění

### <a name="unsupported"></a>Nepodporovaný
- DBA role: mnoha parametry serveru a nastavení můžete nechtěně snížit výkon serveru nebo negate ACID vlastnosti databázového systému. Jako takový zachování integrity služby a SLA na úrovni produktu, tato služba nevystavuje roli správce databáze. Výchozí uživatelský účet, který je vytvořený, když je vytvořena nová instance databáze, umožňuje provádět většinu příkazů DDL a jazyk DML instance spravované databáze. 
- Oprávnění SUPERUŽIVATELE: podobně [SUPER oprávnění](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html#priv_super) je také omezen.

## <a name="data-manipulation-statement-support"></a>Podpora příkaz manipulace dat

### <a name="supported"></a>Podporováno
- DATA VSTUPNÍ_SOUBOR zatížení - podporována, ale musíte zadat parametr [místní], který směřuje na cestu UNC (úložiště Azure připojené prostřednictvím XSMB).

### <a name="unsupported"></a>Nepodporovaný
- VYBERTE... DO VÝSTUPNÍ_SOUBOR

## <a name="functional-limitations"></a>Funkční omezení

### <a name="scale-operations"></a>Operace škálování
- Dynamické škálování serverů napříč cenové úrovně se aktuálně nepodporuje. To znamená přepínání mezi Basic, obecné účely a paměťově optimalizované cenové úrovně.
- Zmenšuje velikost úložiště serveru není podporována.

### <a name="server-version-upgrades"></a>Upgrady verze serveru
- Automatické migrace mezi verzemi modul hlavní databáze není aktuálně podporována.

### <a name="point-in-time-restore"></a>Obnovení do bodu v čase
- Obnovení na jinou službu vrstvy nebo výpočetní jednotky a velikost úložiště není povoleno.
- Obnovení odstraněného server není podporováno.

## <a name="functional-limitations"></a>Funkční omezení

### <a name="subscription-management"></a>Správa předplatného
- Dynamicky přesunutí předem vytvořené serverů mezi předplatné a skupina prostředků není aktuálně podporováno.

## <a name="current-known-issues"></a>Aktuální známé problémy
- Instance serveru MySQL zobrazí verze nesprávný serveru po navázání připojení. Správný server verze instance, použijte vyberte version(); příkaz příkazového řádku MySQL.

## <a name="next-steps"></a>Další postup
- [Co je dostupné na jednotlivých úrovních služby](concepts-pricing-tiers.md)
- [Podporované verze databáze MySQL](concepts-supported-versions.md)
