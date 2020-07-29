---
title: Řešení potíží s hlavičkou požadavku je příliš velké nebo 400 Chybný požadavek v Azure Cosmos DB
description: Jak diagnostikovat a opravit příliš velkou výjimku v hlavičce požadavku
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 942af52c43a1d4526a42f20df40abf3f510e0ee6
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/28/2020
ms.locfileid: "87294175"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-request-header-too-large-message"></a>Diagnostika a řešení potíží s hlavičkou požadavku Azure Cosmos DB příliš velkou zprávu
V hlavičce požadavku je vyvolána příliš velká zpráva s kódem chyby HTTP 400. K této chybě dojde, pokud se velikost hlavičky požadavku zvětšila příliš velká a přesahuje maximální povolenou velikost. Doporučujeme, abyste používali nejnovější verzi sady SDK. Ujistěte se, že používáte alespoň verzi 3. x nebo 2. x, protože tyto verze přidávají do zprávy výjimky trasování velikosti hlavičky.

## <a name="troubleshooting-steps"></a>Postup při řešení potíží
Hlavička požadavku je příliš velká zpráva, pokud je relace nebo token pro pokračování příliš velká. Následující části popisují příčinu a řešení problému v jednotlivých kategoriích.

### <a name="1-session-token-too-large"></a>1. token relace je moc velký.

#### <a name="cause"></a>Příčina:
400 Chybný požadavek je pravděpodobně způsoben velkým objemem tokenu relace. Pokud jsou tyto příkazy pravdivé, potvrdí, že token relace je příliš velký.

* K této chybě dochází při operaci Point, jako je vytváření, čtení, aktualizace a další. tam, kde není k dispozici token pro pokračování.
* Výjimka byla spuštěna bez provedení změn v aplikaci. Token relace roste jako počet oddílů, které se v kontejneru zvyšují. Počet oddílů, které se zvyšují, s nárůstem objemu dat nebo se zvyšuje propustnost.

#### <a name="temporary-mitigation"></a>Dočasné zmírnění rizika: 
Obnovte všechny tokeny relace restartováním klientské aplikace. Token relace se nakonec zvětší zpátky na předchozí velikost, která způsobila daný problém. Proto použijte řešení v další části, abyste se tomuto problému zcela vyhnuli.

#### <a name="solution"></a>Řešení:
1. Postupujte podle pokynů v článku tipy pro výkon [rozhraní .NET V3](performance-tips-dotnet-sdk-v3-sql.md) nebo [.NET v2](performance-tips.md) a převeďte aplikaci na použití přímého režimu připojení s protokolem TCP. Přímý režim s protokolem TCP nemá omezení velikosti záhlaví, jako je protokol HTTP, takže se tomuto problému vyhne. Ujistěte se, že používáte nejnovější verzi sady SDK, která obsahuje opravu pro operace dotazů, pokud není k dispozici spolupráce služby.
2. Pokud režim přímého připojení s protokolem TCP není možností pro vaše úlohy, můžete ho zmírnit změnou [úrovně konzistence klienta](how-to-manage-consistency.md). Token relace se používá pouze pro konzistenci relací, což je výchozí úroveň konzistence pro Azure Cosmos DB. Jiné úrovně konzistence nepoužívají token relace.

### <a name="2-continuation-token-too-large"></a>2. token pro pokračování je moc velký.

#### <a name="cause"></a>Příčina:
400 Chybný požadavek se děje na operacích dotazu, kde se používá token pro pokračování. Pokud token pro pokračování vzrostl příliš velkou nebo pokud různé dotazy mají jiné velikosti tokenů pro pokračování.
    
#### <a name="solution"></a>Řešení:
1. Postupujte podle pokynů v článku tipy pro výkon [rozhraní .NET V3](performance-tips-dotnet-sdk-v3-sql.md) nebo [.NET v2](performance-tips.md) a převeďte aplikaci na použití přímého režimu připojení s protokolem TCP. Přímý režim s protokolem TCP nemá omezení velikosti záhlaví, jako je protokol HTTP, takže se tomuto problému vyhne. 
3. Pokud režim přímého připojení s protokolem TCP není možností pro vaše zatížení, zkuste nastavit `ResponseContinuationTokenLimitInKb` možnost. Tuto možnost najdete v části `FeedOptions` pro v2 nebo `QueryRequestOptions` v v3.

## <a name="next-steps"></a>Další kroky
* [Diagnostika a řešení potíží](troubleshoot-dot-net-sdk.md) při použití Azure Cosmos DB .NET SDK
* Informace o zásadách výkonu pro [.NET V3](performance-tips-dotnet-sdk-v3-sql.md) a [.NET v2](performance-tips.md)
