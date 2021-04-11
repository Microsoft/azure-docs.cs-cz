---
title: Řešení potíží se Azure Cosmos DB špatnými výjimkami žádostí
description: Naučte se diagnostikovat a opravovat chybné výjimky žádostí, jako je vstupní obsah nebo klíč oddílu, není platný, protože klíč oddílu se neshoduje s Azure Cosmos DB.
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 04/06/2021
ms.author: maquaran
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: e0ce00331eb524d064fd6e7c5205be8b66d4dbc2
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2021
ms.locfileid: "106556076"
---
# <a name="diagnose-and-troubleshoot-bad-request-exceptions-in-azure-cosmos-db"></a>Diagnostika a řešení potíží s chybnými výjimkami žádostí v Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Stavový kód HTTP 400 představuje neplatnou data, nebo chybí požadované parametry.

## <a name="missing-the-id-property"></a><a name="missing-id-property"></a>Chybí vlastnost ID.
V tomto scénáři je běžné zobrazit tuto chybu:

*Vstupní obsah je neplatný, protože požadované vlastnosti-' ID; – chybí*

Odezva s touto chybou znamená, že dokument JSON, který se odesílá do služby, nemá požadovanou vlastnost ID.

### <a name="solution"></a>Řešení
Určuje `id` vlastnost s řetězcovou hodnotou jako podle [specifikace REST](/rest/api/cosmos-db/documents) jako součást dokumentu, sady SDK negeneruje hodnoty pro tuto vlastnost.

## <a name="invalid-partition-key-type"></a><a name="invalid-partition-key-type"></a>Neplatný typ klíče oddílu
V tomto scénáři je běžné zobrazit chyby jako:

*Klíč oddílu... je neplatný.*

Odezva s touto chybou znamená, že hodnota klíče oddílu je neplatného typu.

### <a name="solution"></a>Řešení
Hodnota klíče oddílu by měla být řetězec nebo číslo, ujistěte se, že hodnota je očekávaného typu.

## <a name="wrong-partition-key-value"></a><a name="wrong-partition-key-value"></a>Nesprávná hodnota klíče oddílu
V tomto scénáři je běžné zobrazit tuto chybu:

*PartitionKey extrahovaný z dokumentu se neshoduje s tím, který je zadaný v záhlaví.*

Odezva s touto chybou znamená, že provádíte operaci a předáte hodnotu klíče oddílu, která neodpovídá hodnotě textu dokumentu pro očekávanou vlastnost. Pokud je cesta ke klíči oddílu kolekce `/myPartitionKey` , dokument obsahuje vlastnost `myPartitionKey` s názvem, která neodpovídá hodnotě, která byla zadána jako hodnota klíče oddílu při volání metody sady SDK.

### <a name="solution"></a>Řešení
Odešle parametr hodnoty klíče oddílu, který odpovídá hodnotě vlastnosti dokumentu.

## <a name="next-steps"></a>Další kroky
* [Diagnostikujte a řešte](troubleshoot-dot-net-sdk.md) potíže při použití sady Azure Cosmos DB .NET SDK.
* Seznamte se s pokyny k výkonu pro [.NET V3](performance-tips-dotnet-sdk-v3-sql.md) a [.NET v2](performance-tips.md).
* [Diagnostikujte a řešte](troubleshoot-java-sdk-v4-sql.md) potíže při použití Azure Cosmos DB Java v4 SDK.
* Seznamte se s pokyny pro výkon pro [Java v4 SDK](performance-tips-java-sdk-v4-sql.md).
