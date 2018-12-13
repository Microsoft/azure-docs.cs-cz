---
title: Abstraktní Azure Functions a zabezpečení platformy bez serveru
description: Abstrakce pro dokument white paper Azure Functions a bez serveru zabezpečení platformy.
author: TomShinder
ms.author: TomSh
ms.date: 06/21/2018
ms.topic: article
ms.service: security
ms.openlocfilehash: 87010b3256fb8eef6871d76f80db2999760386b4
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/13/2018
ms.locfileid: "53320731"
---
# <a name="azure-functions-and-serverless-platform-security"></a>Služba Azure Functions a zabezpečení platformy bez serveru
## <a name="abstract"></a>Abstraktní
Většina podniků potřebovat značné množství prostředků a času ke správě serverů, což zvyšuje náklady. Pokud podniky může používat méně prostředků ke správě serverů, mohli soustředit na vytváření skvělých aplikací.  

Architektura bez serveru pomáhá přesně to, protože infrastruktury potřebné ke spuštění a škálování aplikací je spravována za vás. Architektura bez serveru představuje abstrakci serverů, infrastruktury a operačních systémů. Výpočetních funkcích bez serveru je řízená reakcemi na události a triggery, které jsou všechny probíhající v téměř v reálném čase – v cloudu. 

Jako plně spravovanou službu Správa serveru a plánování kapacity jsou pro vývojáře neviditelné. Rozhraní bez serveru umožňuje vývoj a nasazení aplikace bez serveru s využitím Azure Functions. Je rozhraní příkazového řádku (CLI), která nabízí struktury a automatizace, které vám pomůžou vytvářet propracované, založené na událostech a bez serveru architektury skládá z funkcí a události. Funkce Azure je nezávislé jednotku nasazení, jako jsou mikroslužby. Je to pouze kód, nasadit v cloudu, který je nejčastěji zapsán pro provedení jedné úlohy.

Bez ohledu na výhody bez serveru zabezpečení má svůj vlastní rizikové faktory řešit. Bez serveru přístup nemá zavést nové otázky zabezpečení, ale vyžaduje mít přístup k existující otázky zabezpečení. Dokument white paper, zaměřuje na tyto otázky týkající se zabezpečení: 
* Výhody platformy bez serveru
* Problémy se zabezpečením ve výpočetním prostředí bez serveru
* Důležité problémy se zabezpečením a jejich zmírnění v rámci Azure
* Zabezpečení platforma Microsoftu bez serveru

[Stáhněte si dokument white paper](https://azure.microsoft.com/mediahandler/files/resourcefiles/azure-functions-serverless-platform-security/Microsoft%20Serverless%20Platform.pdf)

