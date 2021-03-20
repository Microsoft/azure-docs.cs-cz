---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 09/12/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: ec6cbcbc93fe87634c87caeb0041b75ec916a22f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "67174977"
---
Otevřete port nebo vytvořte koncový bod s virtuálním počítačem v Azure vytvořením síťového filtru v podsíti nebo síťovém rozhraní virtuálního počítače. Tyto filtry umístíte, což řídí příchozí i odchozí provoz, ve skupině zabezpečení sítě připojené k prostředku, který přijímá provoz.

V příkladu v tomto článku se dozvíte, jak vytvořit filtr sítě, který používá standardní port TCP 80 (předpokládá se, že jste už spustili příslušné služby a na virtuálním počítači jste otevřeli všechna pravidla firewallu operačního systému).

Po vytvoření virtuálního počítače, který je nakonfigurovaný tak, aby sloužil webovým požadavkům na standardním portu TCP 80, můžete:

1. Vytvoření skupiny zabezpečení sítě

2. Vytvořte příchozí pravidlo zabezpečení, které povolí přenos, a přiřaďte hodnoty k následujícímu nastavení:

   - **Rozsahy cílových portů**: 80

   - **Rozsahy zdrojových portů**: * (umožňuje libovolný zdrojový port)

   - **Hodnota priority**: zadejte hodnotu, která je menší než 65 500 a vyšší Priorita, než je výchozí pravidlo catch-All Deny příchozí.

3. Přidružte skupinu zabezpečení sítě k síťovému rozhraní nebo podsíti virtuálního počítače.

I když tento příklad používá jednoduché pravidlo pro povolení přenosů HTTP, můžete k vytváření složitějších konfigurací sítě použít taky skupiny zabezpečení sítě a pravidla. 




