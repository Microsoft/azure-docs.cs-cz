---
title: Azure Data Factory mapování transformace toku zdroje dat.
description: Azure Data Factory mapování transformace toku zdroje dat.
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 35f4e794caf84aba860b98e68eadcdcd88e77952
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2019
ms.locfileid: "56271748"
---
# <a name="azure-data-factory-mapping-data-flow-source-transformation"></a>Azure Data Factory mapování transformace toku zdroje dat.

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Transformace zdrojového nakonfiguruje zdroje dat, který chcete použít k vložení dat do datového toku. Můžete mít více než 1 zdroje transformovat do jednoho datového toku. Vždy začínají návrh vašich toků dat se zdrojem.

> [!NOTE]
> Každý tok dat vyžaduje alespoň jeden zdroj transformace. Přidejte libovolný počet dalších zdroje podle potřeby

![Možnosti transformace zdroje](media/data-flow/source.png "zdroje")

Váš tok dat zdroj musí být přidružen právě jednu sadu ADF, která definuje tvar a umístění dat k zápisu nebo čtení z.

## <a name="data-flow-staging-areas"></a>Tok dat pracovní oblasti

Tok dat ADF musí dohlednost 5 primární oblasti "pracovní" v rámci Azure a provádí transformaci dat: Azure Blob, 1. generace ADLS, ADLS generace 2, Azure SQL Database a Azure SQL data Warehouse. ADF má přístup k téměř 80 různé nativní konektory, proto zahrnout tyto další zdroje dat do vašeho toku dat, nejprve připravit tato data do jednoho z těchto pěti primární toku dat pracovní oblasti nejdřív pomocí aktivity kopírování:

## <a name="options"></a>Možnosti

### <a name="allow-schema-drift"></a>Povolit schématu odchylek
Pokud zdrojové sloupce se změní často, vyberte Povolit odchylek schématu. Toto nastavení povolí všechna příchozí pole ze zdroje do toku prostřednictvím transformací pro jímku.

### <a name="fail-if-columns-in-the-dataset-are-not-found"></a>Selhat, pokud nejsou nalezeny sloupců v datové sadě
Tato možnost slouží k vynucení ověřování schématu zdroje, který se nezdaří toku dat, pokud nejsou k dispozici sloupce, které se očekává, že ze zdroje.

### <a name="sampling"></a>Vzorkování
Pomocí vzorkování můžete omezit počet řádků ze zdroje.  To je užitečné, když potřebujete jen ukázkou svá zdrojová data pro účely ladění a testování.

### <a name="define-schema"></a>Definovat schéma

![Zdroj transformace](media/data-flow/source2.png "zdroje 2")

### <a name="you-can-modify-the-name-of-the-source-columns-and-their-associated-data-types"></a>Můžete upravit název zdrojové sloupce a jejich přidružených datových typech

Pro typy zdrojových souborů, které nejsou silného typu (to znamená plochých souborů na rozdíl od soubory Parquet) byste měli definovat typy dat pro jednotlivá pole tady v transformaci zdroj, nikoli v datové sadě.

Pokud se nezobrazí názvy sloupců a typy v toku dat, je pravděpodobné, protože nejsou definovány v oddílu definovat schéma jímky. Jenom je potřeba udělat, pokud nepoužíváte zpracování odchylek schématu datového toku.

Tady v "Definovat schéma" karty na transformaci zdroje je kde můžete nastavit datové typy a formáty:

![Zdroj transformace](media/data-flow/source003.png "datové typy")

### <a name="optimize"></a>Optimalizace

![Zdrojové oddíly](media/data-flow/sourcepart.png "dělení")

Na kartě optimalizovat pro transformaci zdroje se zobrazí další dělení typ nazývá "Zdroj". To bude pouze světla – až po dokončení výběru databáze Azure SQL jako zdroj. Je to proto ADF bude chtít paralelizovat připojení ke spuštění velkého dotazy na zdroje služby Azure SQL DB.

Dělení dat v databázi SQL zdroje je volitelný, ale je vhodné pro velké dotazy. Máte dvě možnosti:

### <a name="column"></a>Sloupec

Vyberte sloupec do oddílu na ze zdrojové tabulky. Musíte také nastavit maximální počet připojení.

### <a name="query-condition"></a>Podmínka dotazu

Volitelně můžete při vytváření oddílů připojení na základě dotazu. Pro tuto možnost stačí vložte obsah predikát WHERE. I.e. year > 1980
