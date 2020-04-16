---
title: Doporučené postupy pro dlouhotrvající operace ve službě Azure Analysis Services | Dokumenty společnosti Microsoft
description: Tento článek popisuje osvědčené postupy pro dlouhotrvající operace.
author: minewiskan
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: owend
ms.openlocfilehash: 3f6b2194cc422a827bbc7a15c012173b3f814b52
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428105"
---
# <a name="best-practices-for-long-running-operations"></a>Doporučené postupy pro dlouhotrvající provoz

Ve službě Azure Analysis Services *představuje uzel* hostitelský virtuální počítač, ve kterém je spuštěn prostředek serveru. Některé operace, jako jsou dlouho běžící dotazy, operace aktualizace a synchronizace horizontálního navýšení kapacity dotazu, mohou selhat, pokud se prostředek serveru přesune do jiného uzlu. Běžné chybové zprávy v tomto scénáři patří:

- "Při pokusu o vyhledání dlouho spuštěného požadavku XMLA došlo k chybě. Požadavek mohl být přerušen upgradem služby nebo restartováním serveru."
- "Úloha s<guid>ID<database>pro model " byla zrušena z důvodu chyby služby (nečinnosti) se zprávou Zrušení požadavku na aktualizaci, protože byla zablokovaná bez jakýchkoli aktualizací. Jedná se o interní služby problém. Pokud k tomuto problému dochází opakovaně, odešlete úlohu nebo odešlete lístek znovu."

Existuje mnoho důvodů, proč mohou být dlouhotrvající operace narušeny. Například aktualizace v Azure, jako jsou: 
- Opravy operačního systému 
- Aktualizace zabezpečení
- Aktualizace služby Azure Analysis Services
- Aktualizace service fabric. Service Fabric je součást platformy používaná řadou cloudových služeb Microsoftu, včetně Služby Azure Analysis Services.

Kromě aktualizací, ke kterým dochází ve službě, je přirozený přesun služeb mezi uzly z důvodu vyrovnávání zatížení. Pohyby uzlů jsou očekávanou součástí cloudové služby. Azure Analysis Services se snaží minimalizovat dopady z pohybu uzlů, ale není možné je zcela eliminovat. 

Kromě pohybu uzlu existují další selhání, ke kterým může dojít. Databázový systém zdroje dat může být například offline nebo dojde ke ztrátě připojení k síti. Pokud během aktualizace oddíl má 10 milionů řádků a dojde k selhání na 9 miliontý řádek, neexistuje žádný způsob, jak restartovat aktualizaci v místě selhání. Služba musí začít znovu od začátku. 

## <a name="refresh-rest-api"></a>Aktualizovat rozhraní REST API

Přerušení služeb může být náročné pro dlouhotrvající operace, jako je aktualizace dat. Azure Analysis Services obsahuje rozhraní REST API, které pomáhá zmírnit negativní dopady přerušení služeb. Další informace naleznete [v tématu Asynchronní aktualizace pomocí rozhraní REST API](analysis-services-async-refresh.md).
 
Kromě rozhraní REST API existují další přístupy, které můžete použít k minimalizaci potenciálních problémů během dlouhotrvajících operací aktualizace. Hlavním cílem je, aby se zabránilo nutnosti restartovat operaci aktualizace od začátku a místo toho provádět aktualizace v menších dávkách, které mohou být potvrzeny ve fázích. 
 
Rozhraní REST API umožňuje takové restartování, ale neumožňuje plnou flexibilitu vytváření a odstraňování oddílů. Pokud scénář vyžaduje komplexní operace správy dat, vaše řešení by měla obsahovat nějakou formu dávkování v jeho logice. Například pomocí transakcí ke zpracování dat ve více samostatných dávkách umožňuje selhání není vyžadovat restartování od začátku, ale místo toho z mezilehlého kontrolního bodu. 
 
## <a name="scale-out-query-replicas"></a>Repliky dotazů s horizontálním navýšením kapacity

Bez ohledu na to, zda používáte REST nebo vlastní logiku, dotazy klientských aplikací mohou stále vracet nekonzistentní nebo zprostředkující výsledky při zpracování dávek. Pokud konzistentní data vrácená dotazy klientských aplikací je vyžadována při zpracování a data modelu je v zprostředkujícím stavu, můžete použít [horizontální navýšení kapacity](analysis-services-scale-out.md) s replikami dotazů jen pro čtení.

Pomocí replik dotazů jen pro čtení, zatímco aktualizace se provádí v dávkách, uživatelé klientské aplikace můžete pokračovat dotaz na starý snímek dat na repliky jen pro čtení. Po dokončení aktualizace lze provést operaci Synch, která aktualizuje repliky jen pro čtení.


## <a name="next-steps"></a>Další kroky

[Asynchronní aktualizace s využitím rozhraní REST API](analysis-services-async-refresh.md)  
[Škálování služby Azure Analysis Services na více instancí](analysis-services-scale-out.md)  
[Analýza Services vysoká dostupnost](analysis-services-bcdr.md)  
[Pokyny k opakování pro služby Azure](https://docs.microsoft.com/azure/architecture/best-practices/retry-service-specific)   

