---
title: Řešení Azure VMware (AVS) – kvóta uzlu AVS
description: Popisuje omezení kvóty pro uzly služby AVS a postup, jak požádat o zvýšení kvóty.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: fa1b056c8c96fb09def63ca1cd696fc2da5e9bed
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2020
ms.locfileid: "77019634"
---
# <a name="avs-node-quota-limits"></a>Omezení kvóty uzlu AVS

Když je vaše předplatné služby AVS povolené, jsou čtyři uzly výchozím množstvím dostupným k nákupu. Z Azure Portal můžete koupit libovolný [typ uzlu](cloudsimple-node.md) . K vytvoření privátního cloudu služby AVS se vyžadují aspoň tři uzly stejné SKU. Pokud jste uzly zakoupili, může se při pokusu o zakoupení dalších uzlů zobrazit chyba.

## <a name="quota-increase"></a>Navýšení kvóty

Můžete zvýšit kvótu uzlu odesláním žádosti o podporu. Provozní tým služby vyhodnotí požadavek a bude spolupracovat s vámi k zvýšení kvóty uzlů. Po otevření nového lístku vyberte následující možnosti:

* Typ problému: **technický**
* Předplatné: **ID vašeho předplatného**
* Typ služby: **řešení VMware pomocí služby AVS**
* Typ problému: **kvóta vyhrazených uzlů**
* Podtyp problému: **zvýšení kvóty vyhrazených uzlů**
* Předmět: **zvýšení kvóty**

V podrobnostech lístku podpory zadejte požadovaný počet uzlů a SKU uzlu.

* SKU uzlu
* Počet dalších uzlů, pro které požadujete zvýšení kvóty

## <a name="next-steps"></a>Další kroky

* [Koupit uzly](create-nodes.md)
* [Přehled uzlů pro funkci AVS](cloudsimple-node.md)
