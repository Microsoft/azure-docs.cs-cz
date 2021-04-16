---
title: Zjišťování, připojení a zkoumání dat v synapse pomocí Azure dosah
description: Průvodce zjišťováním dat, jejich propojením a zkoumáním v synapse
author: Rodrigossz
ms.service: synapse-analytics
ms.subservice: ''
ms.topic: how-to
ms.date: 12/16/2020
ms.author: rosouz
ms.reviewer: jrasnick
ms.openlocfilehash: f98507fa72f4503700bf39393063dd1ecc650e91
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567889"
---
# <a name="discover-connect-and-explore-data-in-synapse-using-azure-purview"></a>Zjišťování, připojení a zkoumání dat v synapse pomocí Azure dosah 

V tomto dokumentu se naučíte typ interakcí, které můžete provést při registraci účtu Azure dosah do synapse. 

## <a name="prerequisites"></a>Požadavky 

- [Účet Azure dosah](../../purview/create-catalog-portal.md) 
- [Pracovní prostor synapse](../quickstart-create-workspace.md) 
- [Připojení účtu Azure dosah k synapse](quickstart-connect-azure-purview.md) 

## <a name="using-azure-purview-in-synapse"></a>Používání Azure dosah v synapse 

Použití Azure dosah v synapse vyžaduje, abyste měli přístup k tomuto účtu dosah. Synapse projde vaším oprávněním dosah. Pokud máte například roli oprávnění kurátor, budete moci upravovat metadata prověřená službou Azure dosah. 

### <a name="data-discovery-search-datasets"></a>Zjišťování dat: hledání datových sad 

K vyhledání dat registrovaných a zkontrolovaných službou Azure dosah můžete použít panel hledání v horním středu pracovního prostoru synapse. Ujistěte se, že jste vybrali možnost Azure dosah, kde můžete vyhledat všechna data vaší organizace. 

[![Hledání prostředků Azure dosah](./media/purview-access.png)](./media/purview-access.png#lightbox)

## <a name="azure-purview-actions"></a>Akce dosah Azure 

Tady je seznam funkcí Azure dosah, které jsou k dispozici v synapse: 
- **Přehled** metadat 
- Zobrazení a úprava **schématu** metadat pomocí klasifikací, pojmů, datových typů a popisů 
- Umožňuje zobrazit **čárové** účely pro pochopení závislostí a analýzu dopadů. Další informace o najdete v tématu o vydaných [řádcích](../../purview/catalog-lineage-user-guide.md) .
- Zobrazení a úprava **kontaktů** s cílem zjistit, kdo je vlastníkem nebo odborníkem přes datovou sadu 
- V **souvislosti** s pochopením hierarchických závislostí konkrétní datové sady. Toto prostředí je užitečné k procházení hierarchie dat.

## <a name="actions-that-you-can-perform-over-datasets-with-synapse-resources"></a>Akce, které můžete provádět pomocí datových sad s synapse prostředky 

### <a name="connect-data-to-synapse"></a>Připojení dat k synapse 

- Můžete vytvořit **novou propojenou službu** pro synapse. Tato akce bude nutná k kopírování dat do synapse nebo jejich použití v datovém centru (pro podporované zdroje dat, jako je ADLSg2). 
- U objektů, jako jsou soubory, složky nebo tabulky, můžete přímo vytvořit **novou integrační datovou sadu** a využít existující propojenou službu, pokud už je vytvořená. 

Ještě nemůžeme odvodit, pokud existuje existující propojená služba nebo integrační datová sada. 

###  <a name="develop-in-synapse"></a>Vývoj v synapse 

Existují tři akce, které můžete provést: **Nový skript SQL**, **Nový Poznámkový blok** a **Nový tok dat**. 

Pomocí **nového skriptu SQL** v závislosti na typu podpory můžete: 
- Zobrazte horní 100 řádků, abyste pochopili tvar dat. 
- Vytvoření externí tabulky z synapse SQL Database 
- Načtení dat do databáze SQL synapse 
 
Pomocí **nového poznámkového bloku** můžete: 
- Načtení dat do datového rámce Spark 
- Vytvořte tabulku Spark (pokud to uděláte přes Parquet formát, vytvoří také tabulku fondu SQL bez serveru). 
 
S **novým tokem dat** můžete vytvořit integrační datovou sadu, která může být použita jako zdroj v kanálu toku dat. Tok dat je funkce vývojářů bez kódu, která by prováděla transformaci dat. Další informace o [použití toku dat v synapse](../quickstart-data-flow.md).

##  <a name="nextsteps"></a>Další kroky 

- [Registrace a kontrola prostředků Azure synapse v Azure dosah](../../purview/register-scan-azure-synapse-analytics.md)
- [Jak vyhledávat data v Azure dosah Data Catalog](../../purview/how-to-search-catalog.md)
