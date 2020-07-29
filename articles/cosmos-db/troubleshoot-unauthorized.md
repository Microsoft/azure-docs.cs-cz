---
title: Řešení potíží s Azure Cosmos DB neoprávněnou výjimkou
description: Jak diagnostikovat a opravit neautorizovanou výjimku
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 872780ff65dabe5a931ae712c0f99b6e4f3e726d
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/28/2020
ms.locfileid: "87294101"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-unauthorized-exception"></a>Diagnostika a řešení potíží s Azure Cosmos DB neoprávněnou výjimkou

HTTP 401: podpis MAC nalezený v požadavku HTTP není stejný jako vypočítaný podpis.
Pokud se vám zobrazila následující chybová zpráva 401: Podpis MAC v požadavku HTTP neodpovídá vypočítanému podpisu, může to být způsobeno následujícími scénáři.

Starší sady SDK: výjimka se může zobrazit jako neplatná výjimka JSON namísto správné 401 neoprávněné výjimky. Novější sady SDK správně zpracovávají tento scénář a poskytnou platnou chybovou zprávu.

## <a name="troubleshooting-steps"></a>Postup při řešení potíží
Následující seznam obsahuje známé příčiny a řešení neoprávněné výjimky.

### <a name="1-key-was-not-properly-rotated-is-the-most-common-scenario"></a>1. klíč nebyl správně otočen, jedná se o nejběžnější scénář.
K chybě 401 kvůli podpisu MAC dochází krátce po obměně klíčů a nakonec k ní přestane docházet bez nutnosti provádět jakékoli změny. 

#### <a name="solution"></a>Řešení:
Došlo k obměně klíče, která se neřídila [osvědčenými postupy](secure-access-to-data.md#key-rotation). Střídání klíčů Cosmos DB účtu může trvat několik sekund, a to i v závislosti na velikosti účtu Cosmos DB.

### <a name="2-the-key-is-misconfigured"></a>2. klíč je špatně nakonfigurovaný. 
401 problém s podpisem MAC bude konzistentní a proběhne u všech volání pomocí tohoto klíče.

#### <a name="solution"></a>Řešení:
Klíč je nesprávně nakonfigurovaný v aplikaci a používá nesprávný klíč pro účet nebo se nezkopíroval celý klíč.

### <a name="3-the-application-is-using-the-read-only-keys-for-write-operations"></a>3. aplikace používá klíče jen pro čtení pro operace zápisu.
401 potíží s podpisem MAC se vyskytuje jenom u operací zápisu, jako je vytváření nebo nahrazování, ale požadavek na čtení je úspěšný.

#### <a name="solution"></a>Řešení:
Přepněte aplikaci tak, aby používala klíč pro čtení/zápis, aby bylo možné operace úspěšně dokončit.

### <a name="4-race-condition-with-create-container"></a>4. konflikt časování s vytvořením kontejneru
401 problém s podpisem MAC se krátce po vytvoření kontejneru zobrazuje. K tomu dojde pouze v případě, že vytváření kontejneru bylo dokončeno.

#### <a name="solution"></a>Řešení:
Při vytváření kontejneru dochází ke konfliktu časování. Instance aplikace se pokouší získat přístup ke kontejneru před dokončením jeho vytváření. Nejběžnější scénář pro tento konflikt časování je, pokud je aplikace spuštěná a kontejner se odstranil a znovu vytvoří se stejným názvem. Sada SDK se pokusí použít nový kontejner, ale vytváření kontejneru stále probíhá, takže nemá klíče.

## <a name="next-steps"></a>Další kroky
* [Diagnostika a řešení potíží](troubleshoot-dot-net-sdk.md) při použití Azure Cosmos DB .NET SDK
* Informace o zásadách výkonu pro [.NET V3](performance-tips-dotnet-sdk-v3-sql.md) a [.NET v2](performance-tips.md)