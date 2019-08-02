---
title: Abstraktní Azure Functions a zabezpečení platformy bez serveru
description: Abstrakce pro Azure Functions a dokument White Paper zabezpečení platformy bez serveru.
author: TomShinder
ms.author: TomSh
ms.date: 06/21/2018
ms.topic: article
ms.service: security
ms.subservice: security-fundamentals
ms.openlocfilehash: cf59f6343960a164be66ae27170d722eb26dc173
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68727702"
---
# <a name="azure-functions-and-serverless-platform-security"></a>Zabezpečení platformy Azure Functions a bez serveru
## <a name="abstract"></a>Abstraktní
Většina podniků potřebuje značné množství prostředků a času pro správu serverů, které zvyšují náklady. Pokud podniky můžou použít méně prostředků pro správu serverů, můžou se zaměřit na vytváření skvělých aplikací.  

Computing bez serveru vám pomůže to udělat, protože infrastruktura, kterou potřebujete ke spouštění a škálování aplikací, je spravovaná za vás. Architektura bez serveru představuje abstrakci serverů, infrastruktury a operačních systémů. Computing bez serveru je založený na reakci na události a triggery, které se probíhají téměř v reálném čase v cloudu. 

V rámci plně spravované služby je plánování správy serverů a kapacity pro vývojáře neviditelné. Architektura bez serveru vám pomůže vyvíjet a nasazovat aplikace bez serveru pomocí Azure Functions. Jedná se o rozhraní příkazového řádku (CLI), které nabízí strukturu a automatizaci, které vám pomůžou sestavovat sofistikované architektury bez serveru založené na událostech, které se skládají z funkcí a událostí. Funkce Azure je nezávislá jednotka nasazení, jako je mikroslužba. Je to jenom kód nasazený v cloudu, který se nejčastěji zapisuje za účelem provedení jedné úlohy.

Bez ohledu na výhody nabízí zabezpečení bez serveru vlastní rizikové faktory, se kterými se můžete vypořádat. Přístup bez serveru nezavádí nové aspekty zabezpečení, ale vyžaduje přístup k existujícím problémům se zabezpečením. Tento dokument White Paper se zaměřuje na tyto bezpečnostní otázky: 
* Výhody platformy bez serveru
* Problémy se zabezpečením v výpočetním prostředí bez serveru
* Kritické problémy zabezpečení a zmírnění rizik v souvislosti s Azure
* Zabezpečení platformy Microsoft bez serveru

[Stáhnout dokument White Paper](https://azure.microsoft.com/mediahandler/files/resourcefiles/azure-functions-serverless-platform-security/Microsoft%20Serverless%20Platform.pdf)

