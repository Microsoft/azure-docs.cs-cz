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
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2019
ms.locfileid: "70913935"
---
# <a name="cloudsimple-node-quota-limits"></a>Omezení kvóty uzlu CloudSimple

V případě, že je vaše předplatné pro službu CloudSimple povolené, jsou čtyři uzly výchozím množstvím dostupným k nákupu.  Z Azure Portal můžete koupit libovolný [typ uzlu](cloudsimple-node.md) .  K vytvoření privátního cloudu se vyžadují aspoň tři uzly stejné SKU.  Pokud jste uzly zakoupili, může se při pokusu o zakoupení dalších uzlů zobrazit chyba.

## <a name="quota-increase"></a>Navýšení kvóty

Můžete zvýšit kvótu uzlu odesláním žádosti o podporu. Provozní tým služby vyhodnotí požadavek a bude spolupracovat s vámi k zvýšení kvóty uzlů.  Po otevření nového lístku vyberte následující možnosti:

* Typ problému: **Odbornou**
* Předplatné: **ID vašeho předplatného**
* Typ služby: **Řešení VMware podle CloudSimple**
* Typ problému: **Kvóta vyhrazených uzlů**
* Podtyp problému: **Zvýšení kvóty vyhrazených uzlů**
* Závislosti **Zvýšení kvóty**

V podrobnostech lístku podpory zadejte požadovaný počet uzlů a SKU uzlu.

* SKU uzlu
* Počet dalších uzlů, pro které požadujete zvýšení kvóty

## <a name="next-steps"></a>Další kroky

* [Koupit uzly](create-nodes.md)
* [CloudSimple uzly – přehled](cloudsimple-node.md)
