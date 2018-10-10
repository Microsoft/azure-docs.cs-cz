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
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2018
ms.locfileid: "48888549"
---
Otevření portu nebo vytvoření koncového bodu, virtuálního počítače (VM) v Azure tak, že vytvoříte filtr sítě v podsíti nebo síťovému rozhraní virtuálního počítače. Tyto filtry, které řídí příchozí a odchozí přenosy dat, umístíte na skupinu zabezpečení sítě, který je připojen k prostředku, který přijímá provoz.

V příkladu v tomto článku ukazuje, jak vytvořit filtr sítě, který používá standardní port TCP 80 (se předpokládá, že jste již zahájeno příslušné služby a otevřít žádná pravidla brány firewall operačního systému na virtuálním počítači).

Po vytvoření virtuálního počítače, který je nakonfigurovaný pro obsloužení webové požadavky na standardní port TCP 80, můžete:

1. Vytvořte skupinu zabezpečení sítě.

2. Vytvoření pravidla zabezpečení příchozích dat umožňuje provoz a přiřadit hodnoty následujícího nastavení:

   - **Rozsahy cílových portů**: 80

   - **Zdrojové rozsahy portů**: * (umožňuje jakéhokoli zdrojového portu)

   - **Hodnota priority**: Zadejte hodnotu, která je menší než příchozí pravidlo odepřít 65,500 a vyšší prioritou než výchozí pokrývající vše.

3. Přidružte síťové rozhraní virtuálního počítače nebo podsítě skupinu zabezpečení sítě.

Přestože tento příklad používá jednoduché pravidlo pro povolení provozu HTTP, můžete také použít skupiny zabezpečení sítě a pravidla k vytvoření složitějších konfigurací sítě. 




