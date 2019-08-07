---
title: Řešení Azure VMware podle CloudSimple – kvóta uzlu CloudSimple
description: Popisuje omezení kvót pro uzly CloudSimple a způsob, jak požádat o zvýšení kvóty.
author: sharaths-cs
ms.author: dikamath
ms.date: 04/30/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: e765d7c87f2f941a5e2d558b71c4e5a71d2df9b1
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816667"
---
# <a name="cloudsimple-node-quota-limits"></a>Omezení kvóty uzlu CloudSimple

Pokud je pro službu CloudSimple povolené předplatné, jsou čtyři uzly pro zřizování výchozí dostupné množství.  Z Azure Portal můžete zřídit libovolný [typ uzlu](cloudsimple-node.md) .  K vytvoření privátního cloudu se vyžaduje minimálně tři uzly stejné SKU.  Pokud jste uzly zřídili, může se při pokusu o zřízení dalších uzlů zobrazit chyba.

## <a name="quota-increase"></a>Zvýšení kvóty

Můžete zvýšit kvótu uzlu odesláním žádosti o podporu. Provozní tým služby vyhodnotí požadavek a bude spolupracovat s vámi k zvýšení kvóty uzlů.  Po otevření nového lístku vyberte následující možnosti:

* Typ problému: **Odbornou**
* Předplatné: **ID vašeho předplatného**
* Typ služby: **Řešení VMware podle CloudSimple**
* Typ problému: **Kvóta vyhrazených uzlů**
* Podtyp problému: **Zvýšení kvóty vyhrazených uzlů**
* Předmět: **Zvýšení kvóty**

V podrobnostech lístku podpory zadejte požadovaný počet uzlů a SKU uzlu.

Můžete také kontaktovat svého zástupce účet Microsoft na adrese [azurevmwaresales@microsoft.com](mailto:azurevmwaresales@microsoft.com) , abyste zvýšili kvótu uzlu v rámci vašeho předplatného.  Bude nutné zadat:

* ID předplatného
* SKU uzlu
* Počet dalších uzlů, pro které požadujete zvýšení kvóty

## <a name="next-steps"></a>Další postup

* [Zřizování uzlů](create-nodes.md)
* [CloudSimple uzly – přehled](cloudsimple-node.md)