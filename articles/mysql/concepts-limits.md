---
title: Omezení v Azure databáze pro databázi MySQL
description: Tento článek popisuje omezení v Azure Database pro databázi MySQL, jako je například počet připojení a možnosti úložiště modul.
services: mysql
author: kamathsun
ms.author: sukamat
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 06/04/2018
ms.openlocfilehash: 3ec78b9aad45500a92a8f46f4bb2e654f97da8cb
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35264880"
---
# <a name="limitations-in-azure-database-for-mysql"></a>Omezení v Azure databáze pro databázi MySQL
Následující části popisují kapacity, modul úložiště s podporou, oprávnění podpory, podpora příkaz manipulaci dat a funkční omezení ve službě databázového. Viz také [obecná omezení](https://dev.mysql.com/doc/mysql-reslimits-excerpt/5.6/en/limits.html) vztahuje k modulu databáze MySQL.

## <a name="maximum-connections"></a>Maximální počet připojení
Maximální počet připojení za cenová úroveň a vCores jsou následující: 

|**Cenová úroveň**|**vCore(s)**| **Maximální počet připojení**|
|---|---|---|
|Basic| 1| 50|
|Basic| 2| 100|
|Obecné použití| 2| 300|
|Obecné použití| 4| 625|
|Obecné použití| 8| 1250|
|Obecné použití| 16| 2500|
|Obecné použití| 32| 5000|
|Paměťově optimalizované| 2| 600|
|Paměťově optimalizované| 4| 1250|
|Paměťově optimalizované| 8| 2500|
|Paměťově optimalizované| 16| 5000|

Při připojení přesahuje limit, může se zobrazit chybová zpráva:
> Chyba 1040 (08004): Příliš mnoho připojení

## <a name="storage-engine-support"></a>Podpora modulu úložiště

### <a name="supported"></a>Podporováno
- [InnoDB](https://dev.mysql.com/doc/refman/5.7/en/innodb-introduction.html)
- [PAMĚŤ](https://dev.mysql.com/doc/refman/5.7/en/memory-storage-engine.html)

### <a name="unsupported"></a>Nepodporovaný
- [MyISAM](https://dev.mysql.com/doc/refman/5.7/en/myisam-storage-engine.html)
- [SMĚROVAČE BLACKHOLE](https://dev.mysql.com/doc/refman/5.7/en/blackhole-storage-engine.html)
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

### <a name="subscription-management"></a>Správa předplatného
- Dynamicky přesunutí předem vytvořené serverů mezi předplatné a skupina prostředků není aktuálně podporováno.

## <a name="current-known-issues"></a>Aktuální známé problémy
- Instance serveru MySQL zobrazí verze nesprávný serveru po navázání připojení. Správný server verze instance, použijte vyberte version(); příkaz příkazového řádku MySQL.

## <a name="next-steps"></a>Další postup
- [Co je dostupné na jednotlivých úrovních služby](concepts-pricing-tiers.md)
- [Podporované verze databáze MySQL](concepts-supported-versions.md)
