---
title: Mapování sloupce vzory v toku dat pro vytváření dat Azure
description: Zjistěte, jak pomocí Azure Data Factory sloupec vzory v mapování toku dat můžete vytvořit vzorce zobecněný šablon pro pole v toku dat bez ohledu na základní metadata schématu transformují
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 08cdaafe00b7dc586ea75f6ff03fdb89107edee9
ms.sourcegitcommit: 087ee51483b7180f9e897431e83f37b08ec890ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/31/2019
ms.locfileid: "66430754"
---
# <a name="azure-data-factory-mapping-data-flows-column-patterns"></a>Azure data factory mapování dat toky vzory sloupec

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Několik transformace toku dat objekt pro vytváření dat Azure podporují nápad "Vzory sloupce" tak, že můžete vytvořit šablony sloupců na základě způsobů namísto pevně zakódované sloupce. Tato funkce v rámci Tvůrce výrazů můžete použít k definování vzorků tak, aby odpovídaly sloupce pro transformaci nemusíte mít názvy přesně, konkrétních polí. Vzory jsou užitečné, pokud příchozí zdrojového pole změnit často, zejména v případě změna sloupců v textových souborů nebo databáze NoSQL. Tato podmínka je někdy označovány jako "Schéma odchylek".

![sloupec vzory](media/data-flow/columnpattern2.png "vzory sloupec")

Sloupec vzory jsou užitečné pro zpracování scénářů schématu odchylek i obecné scénáře. Je vhodné pro podmínky Pokud nejste schopni plně znát názvy jednotlivých sloupců. Můžete vzorku odpovídají na název sloupce a sloupce datový typ a sestavení výrazu pro transformaci, která bude provádět tuto operaci pro jakékoli pole v datovém proudu, který odpovídá vaší `name`  &  `type` vzory.

Při přidávání výraz k transformaci, která přijímá vzory, zvolte "Přidat sloupec vzor". Sloupec vzory umožňuje schématu odchylek sloupce odpovídající vzory.

Při sestavování vzorů sloupec šablony, použijte `$$` ve výrazu představující odkaz na každé odpovídající pole ze vstupního datového proudu.

Pokud se rozhodnete použít jednu z funkcí Tvůrce výrazů regulární výraz, následně můžete $1 $2, 3 $... Chcete-li odkazovat dílčích vzorů odpovídat z regulárnímu výrazu.

Příkladem scénáře sloupec vzorek je používání SOUČTU s řadou příchozí pole. Agregace SUM výpočty jsou v agregované transformace. Součet pak můžete použít na všechny výskyty typy polí, které odpovídají "celočíselné" a pak pomocí $$ odkazovat na jednotlivé shody ve výrazu.
