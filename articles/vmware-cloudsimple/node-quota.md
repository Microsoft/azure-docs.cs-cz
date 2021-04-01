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
ms.openlocfilehash: 83dc9e26e03eb955d88340d1ed21084d4e685ed8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "77019634"
---
# <a name="cloudsimple-node-quota-limits"></a>Omezení kvóty uzlu CloudSimple

V případě, že je vaše předplatné pro službu CloudSimple povolené, jsou čtyři uzly výchozím množstvím dostupným k nákupu.  Z Azure Portal můžete koupit libovolný [typ uzlu](cloudsimple-node.md) .  K vytvoření privátního cloudu se vyžadují aspoň tři uzly stejné SKU.  Pokud jste uzly zakoupili, může se při pokusu o zakoupení dalších uzlů zobrazit chyba.

## <a name="quota-increase"></a>Navýšení kvóty

Můžete zvýšit kvótu uzlu odesláním žádosti o podporu. Provozní tým služby vyhodnotí požadavek a bude spolupracovat s vámi k zvýšení kvóty uzlů.  Po otevření nového lístku vyberte následující možnosti:

* Typ problému: **technický**
* Předplatné: **ID vašeho předplatného**
* Typ služby: **řešení VMware podle CloudSimple**
* Typ problému: **kvóta vyhrazených uzlů**
* Podtyp problému: **zvýšení kvóty vyhrazených uzlů**
* Předmět: **zvýšení kvóty**

V podrobnostech lístku podpory zadejte požadovaný počet uzlů a SKU uzlu.

* SKU uzlu
* Počet dalších uzlů, pro které požadujete zvýšení kvóty

## <a name="next-steps"></a>Další kroky

* [Nákup uzlů](create-nodes.md)
* [CloudSimple uzly – přehled](cloudsimple-node.md)
