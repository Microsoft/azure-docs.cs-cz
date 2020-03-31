---
title: Co je Blockchain Data Manager pro službu Azure Blockchain
description: Blockchain Data Manager pro zachycení, transformaci a dodává data blockchainu do témat Event Grid.
ms.date: 11/04/2019
ms.topic: conceptual
ms.reviewer: chroyal
ms.openlocfilehash: 78c93880007259267b26cf53e93de722be1c7323
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77209439"
---
# <a name="what-is-blockchain-data-manager-for-azure-blockchain-service"></a>Co je Blockchain Data Manager pro Azure Blockchain Service?

Blockchain Data Manager zachycuje, transformuje a doručuje transakční data služby Azure Blockchain Service do témat Azure Event Grid, která poskytují spolehlivou a škálovatelnou integraci blockchainové knihy se službami Azure.

Ve většině scénářů podnikového blockchainu je hlavní kniha blockchainu jednou součástí řešení. Chcete-li například převést majetek z jedné entity do druhé, potřebujete mechanismus pro odeslání transakce. Potom potřebujete mechanismus pro čtení dat hlavní knihy, abyste zajistili, že k transakci došlo, byla přijata a výsledné změny stavu jsou pak integrovány s řešením dokončení. V tomto příkladu pokud napíšete inteligentní smlouvu pro přenos datových zdrojů, můžete pomocí Správce dat Blockchain integrovat aplikace mimo řetězec a úložiště dat. V příkladu převodu aktiv, když je aktivum převedeno na blockchain, události a změny stavu majetku jsou dodávány blockchainovým datovým managerem prostřednictvím Event Grid. Potom můžete použít více možných obslužných rutin událostí pro Event Grid k ukládání dat blockchain u řetězu nebo reagovat na změny stavu v reálném čase.

Blockchain Data Manager plní tři hlavní funkce: zachycování, transformaci a doručování.

![Funkce Správce dat blockchainu](./media/data-manager/functions.png)

## <a name="capture"></a>Zachycování

Každá instance Správce dat Blockchain se připojuje k jednomu členskému uzlu služby Azure Blockchain Service. Pouze uživatelé s přístupem k uzlu transakce můžete vytvořit připojení zajišťující správné řízení přístupu k datům zákazníka. Instance Správce dat Blockchain spolehlivě zachycuje všechna nezpracovaná data bloků a nezpracovaných transakcí z transakčního uzlu a může škálovat pro podporu podnikových úloh.

## <a name="transform"></a>Transformace

Správce dat Blockchain můžete použít k dekódování událostí a stavu vlastností konfigurací inteligentních smluvních aplikací v rámci Správce dat Blockchain. Chcete-li přidat inteligentní smlouvy, zadejte smlouvu ABI a bytecode. Blockchain Data Manager používá inteligentní artefakty smlouvy k dekódování a zjišťování adres smluv. Po přidání blockchainové aplikace do instance Blockchain Data Manager dynamicky zjišťuje adresu inteligentní smlouvy při nasazení inteligentní smlouvy do konsorcia a odešle dekódované události a stav vlastnosti do nakonfigurovaných cílů.

## <a name="deliver"></a>Doručování

Blockchain Data Manager podporuje více odchozích připojení Event Grid Topic pro danou instanci Blockchain Data Manager. Data blockchainu můžete odesílat do jednoho cíle nebo data blockchainu do více destinací. Pomocí Správce dat blockchainu můžete vytvořit škálovatelné řešení pro publikování dat založené na událostech pro jakékoli nasazení blockchainu.

## <a name="configuration-options"></a>Možnosti konfigurace

Správce dat blockchainu můžete nakonfigurovat tak, aby vyhovoval potřebám vašeho řešení. Můžete například zřídit:

* Jedna instance Správce dat blockchainu pro člena služby Azure Blockchain Service.
* Instance Správce dat blockchain u transakčního uzlu služby Azure Blockchain Service. Například uzly soukromých transakcí mohou mít vlastní instanci Správce dat Blockchain, aby byla zachována důvěrnost.
* Instance Správce dat Blockchain může podporovat více výstupních připojení. Jednu instanci Správce dat blockchainu lze použít ke správě všech integračních bodů pro publikování dat pro člena služby Azure Blockchain.

## <a name="next-steps"></a>Další kroky

Zkuste [vytvořit instanci Správce dat blockchainu](data-manager-portal.md) pro člena služby Azure Blockchain Service.
