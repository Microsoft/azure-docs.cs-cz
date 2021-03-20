---
title: Řešení potíží s chybou "zpráva" je příliš velká "nebo 400 Chybný požadavek v Azure Cosmos DB
description: Naučte se diagnostikovat a opravovat příliš velkou výjimku v hlavičce požadavku.
author: j82w
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.custom: devx-track-dotnet
ms.openlocfilehash: a14503bc56777563b6360143efaa86a136b22278
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "93340511"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-request-header-too-large-message"></a>Diagnostika a řešení potíží Azure Cosmos DB zpráva "moc velká hlavička" žádosti
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Zpráva "požadavek je příliš velká" se vyvolala s kódem chyby HTTP 400. K této chybě dochází, pokud se velikost hlavičky požadavku zvětšila tak, aby byla větší než maximální povolená velikost. Doporučujeme, abyste používali nejnovější verzi sady SDK. Použijte alespoň verzi 3. x nebo 2. x, protože tyto verze přidávají do zprávy výjimky trasování velikosti hlavičky.

## <a name="troubleshooting-steps"></a>Postup při řešení potíží
Zpráva "příliš velká hlavička žádosti" se zobrazí, pokud je relace nebo token pro pokračování příliš velká. Následující části popisují příčinu problému a jeho řešení v jednotlivých kategoriích.

### <a name="session-token-is-too-large"></a>Token relace je příliš velký.

#### <a name="cause"></a>Příčina:
Je-li token relace příliš velký, pravděpodobně došlo k chybě 400 chybného požadavku. Pokud jsou splněny následující příkazy, token relace je příliš velký:

* K chybě dochází při operacích s bodem, jako je vytvoření, čtení a aktualizace, kde není k dispozici token pro pokračování.
* Výjimka byla spuštěna bez provedení změn v aplikaci. Token relace roste, protože počet oddílů se zvětšuje v kontejneru. Počet oddílů se zvyšuje podle objemu dat, nebo pokud se zvyšuje propustnost.

#### <a name="temporary-mitigation"></a>Dočasné zmírnění rizika: 
Obnovte všechny tokeny relace restartováním klientské aplikace. Nakonec se token relace zvětší na předchozí velikost, která tento problém způsobila. Chcete-li se tomuto problému zcela vyhnout, použijte řešení v následující části.

#### <a name="solution"></a>Řešení:
1. Postupujte podle pokynů v článcích tipy pro výkon [rozhraní .NET V3](performance-tips-dotnet-sdk-v3-sql.md) nebo [.NET v2](performance-tips.md) . Převeďte aplikaci tak, aby používala přímý režim připojení s protokolem TCP (Transmission Control Protocol). Režim přímého připojení s protokolem TCP nemá omezení velikosti záhlaví, jako je protokol HTTP, takže se tomuto problému vyhne. Ujistěte se, že používáte nejnovější verzi sady SDK, která obsahuje opravu pro operace dotazů, pokud není k dispozici spolupráce služby.
1. Pokud režim přímého připojení s protokolem TCP není pro vaše zatížení dostupný, můžete ho zmírnit změnou [úrovně konzistence klienta](how-to-manage-consistency.md). Token relace se používá pouze pro konzistenci relací, což je výchozí úroveň konzistence pro Azure Cosmos DB. Jiné úrovně konzistence nepoužívají token relace.

### <a name="continuation-token-is-too-large"></a>Token pro pokračování je moc velký.

#### <a name="cause"></a>Příčina:
V operacích dotazu, kde je použit token pokračování v případě, že byl token pokračování vypěstován příliš velký nebo pokud různé dotazy mají jiné velikosti tokenů pro pokračování, dojde k chybě 400.
    
#### <a name="solution"></a>Řešení:
1. Postupujte podle pokynů v článcích tipy pro výkon [rozhraní .NET V3](performance-tips-dotnet-sdk-v3-sql.md) nebo [.NET v2](performance-tips.md) . Převeďte aplikaci tak, aby používala přímý režim připojení s protokolem TCP. Režim přímého připojení s protokolem TCP nemá omezení velikosti záhlaví, jako je protokol HTTP, takže se tomuto problému vyhne. 
1. Pokud režim přímého připojení s protokolem TCP není pro vaše zatížení možnost, nastavte `ResponseContinuationTokenLimitInKb` možnost. Tuto možnost můžete najít v `FeedOptions` v v2 nebo `QueryRequestOptions` v3.

## <a name="next-steps"></a>Další kroky
* [Diagnostikujte a řešte](troubleshoot-dot-net-sdk.md) potíže při použití sady Azure Cosmos DB .NET SDK.
* Seznamte se s pokyny k výkonu pro [.NET V3](performance-tips-dotnet-sdk-v3-sql.md) a [.NET v2](performance-tips.md).
