---
title: Osvědčené postupy pro dlouhodobě běžící operace v Azure Analysis Services | Microsoft Docs
description: Tento článek popisuje osvědčené postupy pro dlouhodobě běžící operace.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: owend
ms.openlocfilehash: 4e069effae0cb7f834b2c3dac696d05304d841a7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92014830"
---
# <a name="best-practices-for-long-running-operations"></a>Osvědčené postupy pro dlouhotrvající operace

V Azure Analysis Services *uzel* představuje virtuální počítač hostitele, na kterém je spuštěn prostředek serveru. Některé operace, jako je dlouho běžící dotazy, operace aktualizace a synchronizace škálování na více instancí, můžou selhat, pokud se prostředek serveru přesune na jiný uzel. Mezi běžné chybové zprávy v tomto scénáři patří:

- Při pokusu o nalezení dlouho běžící žádosti XMLA došlo k chybě. Je možné, že žádost byla přerušena upgradem služby nebo restartováním serveru.
- Úloha s ID <guid> pro model <database> se zrušila kvůli chybě služby (neaktivity) se zprávou o zrušení žádosti o aktualizaci, protože se zablokovala bez aktualizací. Jedná se o vnitřní problém služby. Pokud se problém opakuje, odešlete prosím úlohu znovu nebo zajistěte, aby se zobrazila žádost o pomoc. "

Existuje mnoho důvodů, proč je možné narušit dlouho běžící operace. Například aktualizace v Azure, například: 
- Opravy operačního systému 
- Aktualizace zabezpečení
- Aktualizace služby Azure Analysis Services
- Service Fabric aktualizace. Service Fabric je komponentou platformy, kterou používá řada cloudových služeb Microsoftu, včetně Azure Analysis Services.

Kromě aktualizací, ke kterým dochází ve službě, je kvůli vyrovnávání zatížení přirozený pohyb služeb napříč uzly. Přesuny uzlů jsou očekávanou součástí cloudové služby. Azure Analysis Services se snaží minimalizovat dopady z přesunů uzlů, ale není možné je zcela eliminovat. 

Kromě přesunů uzlů existují i další chyby, ke kterým může dojít. Například databázový systém zdroje dat může být offline nebo dojde ke ztrátě připojení k síti. Při aktualizaci má oddíl 10 000 000 řádků a při selhání dojde k chybě na řádku 9 millionth, neexistuje způsob, jak v bodě selhání restartovat aktualizaci. Služba musí začít znovu od začátku. 

## <a name="refresh-rest-api"></a>Aktualizovat REST API

Přerušení služby můžou být náročné na dlouhodobě běžící operace, jako je aktualizace dat. Azure Analysis Services obsahuje REST API, které pomáhají zmírnit negativní dopad na přerušení služeb. Další informace najdete v tématu [asynchronní aktualizace s REST API](analysis-services-async-refresh.md).
 
Kromě REST API existují i další přístupy, které můžete použít k minimalizaci potenciálních problémů během dlouhotrvajících operací aktualizace. Hlavním cílem je vyhnout se nutnosti restartování operace aktualizace od začátku a místo toho provádět aktualizace v menších dávkách, které je možné potvrdit ve fázích. 
 
REST API umožňuje takové restartování, ale neumožňuje plnou flexibilitu při vytváření a odstraňování oddílů. Pokud scénář vyžaduje komplexní operace správy dat, mělo by vaše řešení zahrnovat určitou formu dávkování ve své logice. Například při použití transakcí ke zpracování dat v několika samostatných dávkách umožňuje selhání Nevyžadovat restart od začátku, ale místo mezilehlého kontrolního bodu. 
 
## <a name="scale-out-query-replicas"></a>Repliky dotazů se škálováním na více instancí

Bez ohledu na to, jestli používáte REST nebo vlastní logiku, můžou dotazy klientské aplikace při zpracovávání dávek pořád vracet nekonzistentní nebo mezilehlé výsledky. Pokud se při zpracování vyžaduje konzistentní data vrácená dotazy klientské aplikace a data modelu jsou v přechodném stavu, můžete použít [škálování](analysis-services-scale-out.md) na více instancí s replikami dotazů jen pro čtení.

Pomocí replik dotazů jen pro čtení, zatímco se v dávkách provádějí aktualizace, můžou uživatelé klientských aplikací pokračovat v dotazování starého snímku dat v replikách jen pro čtení. Po dokončení aktualizací se dá provést operace synchronizace, aby se repliky jen pro čtení aktualizovaly do aktuálního stavu.


## <a name="next-steps"></a>Další kroky

[Asynchronní aktualizace s využitím rozhraní REST API](analysis-services-async-refresh.md)  
[Škálování služby Azure Analysis Services na více instancí](analysis-services-scale-out.md)  
[Vysoká dostupnost služby Analysis Services](analysis-services-bcdr.md)  
[Pokyny pro opakování služeb Azure](/azure/architecture/best-practices/retry-service-specific)