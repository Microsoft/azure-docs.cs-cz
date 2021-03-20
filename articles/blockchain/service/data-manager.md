---
title: Co je blockchain Data Manager pro službu Azure blockchain
description: Blockchain Data Manager pro zachycení, transformuje a doručuje Blockchainá data pro Event Grid témata.
ms.date: 11/04/2019
ms.topic: conceptual
ms.reviewer: chroyal
ms.openlocfilehash: 78c93880007259267b26cf53e93de722be1c7323
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "77209439"
---
# <a name="what-is-blockchain-data-manager-for-azure-blockchain-service"></a>Co je Blockchain Data Manager pro Azure Blockchain Service?

Blockchain Data Manager zachycuje, transformuje a doručuje data transakcí služby Azure blockchain a Azure Event Grid témata poskytující spolehlivou a škálovatelnou integraci blockchain v rámci služeb Azure.

Ve většině scénářů Enterprise Blockchain je hlavní kniha blockchain jednou součástí řešení. Například pro přenos assetu z jedné entity do jiné potřebujete mechanismus pro odeslání transakce. Pak budete potřebovat mechanismus pro čtení dat hlavní knihy, abyste zajistili, že transakce byla přijata, a výsledné změny stavu jsou pak integrovány do kompletního řešení. Pokud v tomto příkladu napíšete pro přenos assetů inteligentní kontrakt, můžete k integraci aplikací a úložišť dat mimo řetěz použít Data Manager blockchain. Pokud se příklad přenosu assetu přenáší do blockchain, jsou události a změny stavu vlastností doručovány pomocí služby blockchain Data Manager prostřednictvím Event Grid. Pak můžete použít více možných obslužných rutin událostí pro Event Grid k uložení dat blockchain mimo řetěz nebo reakci na změny stavu v reálném čase.

Blockchain Data Manager provádí tři hlavní funkce: zachytit, transformovat a dodat.

![Funkce Data Manager blockchain](./media/data-manager/functions.png)

## <a name="capture"></a>Zachytávání

Každá instance blockchain Data Manager se připojuje k jednomu uzlu členské transakce služby Azure blockchain. Připojení, které zajišťuje správné řízení přístupu k zákaznickým datům, může vytvořit pouze uživatelé s přístupem k uzlu transakce. Blockchain Data Manager instance spolehlivě zachycuje všechna nezpracovaná a nezpracovaná data transakcí z uzlu transakce a je možné ji škálovat na podporu podnikových úloh.

## <a name="transform"></a>Transformace

Blockchain Data Manager můžete použít k dekódování událostí a stavu vlastností tím, že v blockchain Data Manager nakonfigurujete aplikace inteligentních smluv. Chcete-li přidat inteligentní kontrakt, zadejte kontrakt ABI a kód. Blockchain Data Manager používá artefakty inteligentních kontraktů k dekódování a zjišťování adres smluv. Po přidání aplikace blockchain do instance blockchain Data Manager dynamicky zjistí adresu inteligentního kontraktu, když se inteligentní kontrakt nasadí do konsorcia a pošle Dekódovatelné události a stav vlastnosti do nakonfigurovaných cílů.

## <a name="deliver"></a>Doručování

Blockchain Data Manager podporuje vícenásobná odchozí připojení Event Grid tématu pro všechny dané instance služby blockchain Data Manager. Blockchain data můžete odesílat do jednoho cíle nebo odesílat blockchain data do více cílů. Pomocí Data Manager blockchain můžete vytvořit škálovatelné řešení publikování dat založené na událostech pro jakékoli nasazení blockchain.

## <a name="configuration-options"></a>Možnosti konfigurace

Blockchain Data Manager můžete nakonfigurovat tak, aby vyhovovala potřebám vašeho řešení. Můžete například zřídit:

* Jedna instance blockchain Data Manager pro člena služby Azure blockchain.
* Blockchain Data Manager instance na uzel transakce služby Azure blockchain. Například uzly privátních transakcí mohou mít svou vlastní instanci blockchain Data Manager, aby bylo možné zachovat důvěrnost.
* Instance Data Manager blockchain může podporovat více výstupních připojení. Jednu instanci blockchain Data Manager lze použít ke správě všech integračních bodů publikování dat pro člena služby Azure blockchain.

## <a name="next-steps"></a>Další kroky

Zkuste [vytvořit instanci Blockchain data Manager](data-manager-portal.md) pro člena služby Azure blockchain.
