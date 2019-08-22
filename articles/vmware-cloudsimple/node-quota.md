---
title: Řešení Azure VMware podle CloudSimple – kvóta uzlu CloudSimple
description: Popisuje omezení kvót pro uzly CloudSimple a způsob, jak požádat o zvýšení kvóty.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 5da91f37d197e9d427343a09d0376076c356e92d
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877815"
---
# <a name="cloudsimple-node-quota-limits"></a>Omezení kvóty uzlu CloudSimple

V případě, že je vaše předplatné pro službu CloudSimple povolené, jsou čtyři uzly výchozím množstvím dostupným k nákupu.  Z Azure Portal můžete koupit libovolný [typ uzlu](cloudsimple-node.md) .  K vytvoření privátního cloudu se vyžadují aspoň tři uzly stejné SKU.  Pokud jste uzly zakoupili, může se při pokusu o zakoupení dalších uzlů zobrazit chyba.

## <a name="quota-increase"></a>Zvýšení kvóty

Můžete zvýšit kvótu uzlu odesláním žádosti o podporu. Provozní tým služby vyhodnotí požadavek a bude spolupracovat s vámi k zvýšení kvóty uzlů.  Po otevření nového lístku vyberte následující možnosti:

* Typ problému: **Odbornou**
* Předplatné: **ID vašeho předplatného**
* Typ služby: **Řešení VMware podle CloudSimple**
* Typ problému: **Kvóta vyhrazených uzlů**
* Podtyp problému: **Zvýšení kvóty vyhrazených uzlů**
* Závislosti **Zvýšení kvóty**

V podrobnostech lístku podpory zadejte požadovaný počet uzlů a SKU uzlu.

Můžete také kontaktovat svého zástupce účet Microsoft na adrese [azurevmwaresales@microsoft.com](mailto:azurevmwaresales@microsoft.com) , abyste zvýšili kvótu uzlu v rámci vašeho předplatného.  Bude nutné zadat:

* ID předplatného
* SKU uzlu
* Počet dalších uzlů, pro které požadujete zvýšení kvóty

## <a name="next-steps"></a>Další kroky

* [Koupit uzly](create-nodes.md)
* [CloudSimple uzly – přehled](cloudsimple-node.md)