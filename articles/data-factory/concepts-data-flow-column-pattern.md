---
title: Mapování sloupce vzory v toku dat pro vytváření dat Azure
description: Azure Data Factory mapování dat sloupce vzory v toku se používají k vytváření vzorce zobecněný šablon pro pole v toku dat bez ohledu na základní metadata schématu transformují
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: dd397259ebcd6afe34a47a6f853d826a845a1244
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56213289"
---
# <a name="azure-data-factory-mapping-data-flow-concepts"></a>Mapování datového toku koncepty služby Azure Data Factory

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Několik transformace toku dat objekt pro vytváření dat Azure podporují nápad "Vzory sloupce" tak, že můžete vytvořit šablony sloupců na základě způsobů namísto pevně zakódované sloupce. Tato funkce v rámci Tvůrce výrazů můžete použít k definování vzorků tak, aby odpovídaly sloupce pro transformaci nemusíte mít xact, názvy konkrétních polí. Vzory jsou užitečné, pokud příchozí zdrojového pole změnit často, zejména v případě změna sloupců v textových souborů nebo databáze NoSQL. To se někdy označuje jako "Schéma odchylek".

![sloupec vzory](media/data-flow/columnpattern2.png "vzory sloupec")

Sloupec vzory jsou užitečné pro zpracování scénářů schématu odchylek i obecné scénáře. Je vhodné pro podmínky Pokud nejste schopni plně znát názvy jednotlivých sloupců. Můžete vzorku odpovídají na název sloupce a sloupce datový typ a sestavení výrazu pro transformaci, která bude provádět tuto operaci pro jakékoli pole v datovém proudu, který odpovídá vaší `name`  &  `type` vzory.

Při přidávání výraz k transformaci, která přijímá vzory, zvolte "Přidat sloupec vzor". Sloupec vzory umožňuje schématu odchylek sloupce odpovídající vzory.

Při sestavování vzorů sloupec šablony, použijte `$$` ve výrazu představující odkaz na každé odpovídající pole ze vstupního datového proudu.

Pokud se rozhodnete použít jednu z funkcí Tvůrce výrazů regulární výraz, následně můžete $1, 2 USD, $3... odkazovat dílčích odpovídat z regulárnímu výrazu.

Příkladem scénáře sloupec vzorek je používání SOUČTU s řadou příchozí pole. Agregace SUM výpočty jsou v agregované transformace. Můžete pak provede součet na všechny výskyty typy polí tuto shodu "celočíselné" a pak pomocí $$ odkazovat na jednotlivé shody ve výrazu.
