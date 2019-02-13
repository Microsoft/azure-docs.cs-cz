---
title: Azure Data Factory mapování datový tok optimalizovat kartu
description: Optimalizace mapování toků dat pomocí nastavení oddílu kartu Optimalizace pro vytváření dat Azure
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: a83e95793563faab31e6b7183d657126c13ebb54
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56213306"
---
# <a name="azure-data-factory-data-flow-transformation-optimize-tab"></a>Transformace toku dat Azure Data Factory optimalizovat kartu

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Každá transformace toku dat se kartu "Optimalizace". Na kartě Optimalizace obsahuje volitelné nastavení pro konfiguraci schémata dělení pro datové toky.

<img src="media/data-flow/opt001.png" width="800">

Ve výchozím nastavení je "pomocí aktuální dělení". Aktuální dělení dává pokyn Azure Data Factory používat schéma rozdělení oddílů nativní Data proudí běžící na Sparku v Azure Databricks. Obvykle se jedná o doporučený postup.

Existují však instance, kde můžete chtít upravit rozdělení. Například pokud chcete výstup transformace do jednoho souboru do jezera, pak vyberte "jeden oddíl" na kartě optimalizovat vytváření oddílů v transformaci jímky.

Dalším užitečným, kde můžete chtít vykonávat kontrolu nad schémata dělení do oddílů se používají pro transformaci dat je z hlediska výkonu. Úprava dělení dat poskytuje úroveň kontroly nad distribuci vašich dat v rámci výpočetních uzlů a dat lokality optimalizací, které může mít pozitivní i negativní dopady na výkon toku datových.

Pokud chcete změnit dělení na některou transformaci, jednoduše klikněte na kartu Optimalizace a vyberte přepínač "Nastavit dělení". Pak zobrazí s řadou možností vytváření oddílů. Nejlepší metody k implementaci dělení se liší v závislosti na vaší datové svazky, klíče Release candidate, hodnoty null a kardinalitu. Osvědčeným postupem je začít s výchozí dělení a opakujte různých možnostech vytváření oddílů. Můžete otestovat pomocí příkazu Debug spouštět v kanálu a pak zobrazit čas strávený v každá transformace, seskupování a také oddíl využití ze zobrazení monitorování.

<img src="media/data-flow/opt002.png" width="600">

### <a name="round-robin"></a>Kruhové dotazování.

Kruhové dotazování je jednoduchý oddíl, který automaticky rovnoměrně distribuuje data napříč oddíly. Použití kruhovým Pokud nejsou dobrými kandidáty klíče k implementaci solid, inteligentní strategie dělení. Můžete nastavit počet fyzických oddílů.

### <a name="hash"></a>Hodnota hash

Azure Data Factory vytvoří hodnotu hash sloupce, které chcete vytvořit jednotný oddíly tak, že řádky s podobnými hodnotami bude spadat do stejného oddílu. Při použití možnosti Hash test pro možné oddílu zkosení. Můžete nastavit počet fyzických oddílů.

### <a name="dynamic-range"></a>Dynamický rozsah

Dynamický rozsah budou používat dynamické rozsahy Spark na základě sloupce nebo výrazy, které zadáte. Můžete nastavit počet fyzických oddílů. 

### <a name="fixed-range"></a>Dlouhodobý rozsah

Je nutné vytvořit výraz, který poskytuje pevné rozsah hodnot ve sloupcích dělená data. Měli byste rozumět tomu vaše data před použitím této možnosti, pokud se chcete vyhnout Nerovnoměrná distribuce oddílu. Hodnota, která zadejte výraz se použije jako součást funkce oddílu. Můžete nastavit počet fyzických oddílů.

### <a name="key"></a>Klíč

Pokud budete mít dostatečné povědomí o Kardinalita vašich dat, klíče dělení může být dobré oddílu strategie. Klíče dělení vytvoří oddíly pro každou jedinečnou hodnotu ve sloupci. Počet oddílů nelze nastavit, protože číslo bude založena na jedinečné hodnoty v datech.
