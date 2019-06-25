---
title: Řešení Azure VMware podle CloudSimple - CloudSimple kvótu uzlu
description: Popisuje kvóty pro CloudSimple uzly a žádost o zvýšení kvóty.
author: sharaths-cs
ms.author: dikamath
ms.date: 04/30/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: fd1416befb74a7299136ea497eccc8a06b7f0f6a
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/17/2019
ms.locfileid: "67164872"
---
# <a name="cloudsimple-node-quota-limits"></a>Limity kvót CloudSimple uzlu

Čtyři uzly se výchozí množství dostupné ke zřizování, pokud je vaše předplatné povolená pro CloudSimple služby.  Můžete zřídit libovolné [typ uzlu](cloudsimple-node.md) z webu Azure portal.  Minimálně tří uzlů se stejným SKU vyžadovaných pro vytvoření privátního cloudu.  Pokud zřídíte uzly, se zobrazí chyba při pokusu o zřízení dalších uzlů.

## <a name="quota-increase"></a>Zvýšení kvóty

Odešlete žádost o podporu můžete zvýšit kvótu uzlu. Provozní tým služby vyhodnotí žádost a spolupracovat s vámi o zvýšení kvóty uzlu.  Při otevření nového lístku, vyberte následující možnosti:

* Typ problému: **Technické**
* Předplatné: **ID vašeho předplatného**
* Typ služby: **Řešení VMware podle CloudSimple**
* Typ problému: **Kvóta vyhrazené uzly**
* Podtyp problému: **Navyšte kvótu vyhrazené uzly**
* Předmět: **Zvýšení kvóty**

V podrobnostech lístků podpory zadejte požadovaný počet uzlů a uzlů SKU.

Taky se můžete obrátit na obchodního zástupce společnosti Microsoft [ azurevmwaresales@microsoft.com ](mailto:azurevmwaresales@microsoft.com) a navyšte kvótu uzlu v rámci předplatného.  Je potřeba zadat:

* ID předplatného
* Uzel SKU
* Počet dalších uzlů, pro které se požaduje zvýšení kvóty

## <a name="next-steps"></a>Další postup

* [Zřízení uzly](create-nodes.md)
* [Přehled CloudSimple uzly](cloudsimple-node.md)