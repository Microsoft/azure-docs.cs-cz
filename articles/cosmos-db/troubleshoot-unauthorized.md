---
title: Řešení potíží s Azure Cosmos DB neoprávněnými výjimkami
description: Naučte se diagnostikovat a opravovat neoprávněné výjimky.
author: j82w
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: aa9bf1fd706ccf6064893f1141be5e5b2f185ff3
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2020
ms.locfileid: "94411163"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-unauthorized-exceptions"></a>Diagnostika a řešení potíží s Azure Cosmos DB neoprávněnými výjimkami
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

HTTP 401: podpis MAC nalezený v požadavku HTTP není stejný jako vypočítaný podpis.
Pokud jste obdrželi chybovou zprávu 401 "podpis MAC nalezený v požadavku HTTP není stejný jako vypočítaný podpis," může to být způsobeno následujícími scénáři ".

Pro starší sady SDK se výjimka může zobrazit jako neplatná výjimka JSON namísto správné 401 neoprávněné výjimky. Novější sady SDK správně zpracovávají tento scénář a poskytnou platnou chybovou zprávu.

## <a name="troubleshooting-steps"></a>Postup při řešení potíží
Následující seznam obsahuje známé příčiny a řešení neautorizovaných výjimek.

### <a name="the-key-wasnt-properly-rotated-is-the-most-common-scenario"></a>Klíč nebyl správně otočen, jedná se o nejběžnější scénář.
Podpis 401 MAC se krátce po rotaci klíčů zobrazuje a nakonec se zastaví bez jakýchkoli změn. 

#### <a name="solution"></a>Řešení:
Klíč se otočí a nesledoval [osvědčené postupy](secure-access-to-data.md#key-rotation). Střídání klíčů Azure Cosmos DB účtu může trvat několik sekund, a to i v závislosti na velikosti účtu Azure Cosmos DB.

### <a name="the-key-is-misconfigured"></a>Klíč je špatně nakonfigurovaný. 
Problém s podpisem MAC 401 bude konzistentní a proběhne u všech volání, která tento klíč používají.

#### <a name="solution"></a>Řešení:
Klíč je nesprávně nakonfigurovaný v aplikaci a používá nesprávný klíč pro účet, nebo se nepovedlo zkopírovat celý klíč.

### <a name="the-application-is-using-the-read-only-keys-for-write-operations"></a>Aplikace používá klíče jen pro čtení pro operace zápisu.
Problém s podpisem MAC 401 se vyskytuje jenom u operací zápisu, jako je vytváření nebo nahrazování, ale úspěšné požadavky na čtení.

#### <a name="solution"></a>Řešení:
Přepněte aplikaci tak, aby používala klíč pro čtení/zápis, aby bylo možné operace úspěšně dokončit.

### <a name="race-condition-with-create-container"></a>Podmínka časování s vytvořením kontejneru
Problém s podpisem 401 MAC se krátce po vytvoření kontejneru zobrazuje. K tomuto problému dochází pouze v případě, že vytvoření kontejneru bylo dokončeno.

#### <a name="solution"></a>Řešení:
Existuje konflikt časování s vytvořením kontejneru. Instance aplikace se pokouší o přístup k kontejneru před dokončením vytváření kontejneru. Nejběžnější scénář pro tento konflikt časování je, pokud je aplikace spuštěná a kontejner se odstranil a znovu vytvoří se stejným názvem. Sada SDK se pokusí použít nový kontejner, ale vytvoření kontejneru stále probíhá, takže nemá klíče.

## <a name="next-steps"></a>Další kroky
* [Diagnostikujte a řešte](troubleshoot-dot-net-sdk.md) potíže při použití sady Azure Cosmos DB .NET SDK.
* Seznamte se s pokyny k výkonu pro [.NET V3](performance-tips-dotnet-sdk-v3-sql.md) a [.NET v2](performance-tips.md).
* [Diagnostikujte a řešte](troubleshoot-java-sdk-v4-sql.md) potíže při použití Azure Cosmos DB Java v4 SDK.
* Seznamte se s pokyny pro výkon pro [Java v4 SDK](performance-tips-java-sdk-v4-sql.md).