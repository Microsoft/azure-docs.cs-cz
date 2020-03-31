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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67174977"
---
Otevřete port nebo vytvoříte koncový bod pro virtuální počítač (VM) v Azure vytvořením síťového filtru v podsíti nebo síťovém rozhraní virtuálního počítače. Tyto filtry, které řídí příchozí i odchozí provoz, umístíte do skupiny zabezpečení sítě připojené k prostředku, který přijímá přenosy.

Příklad v tomto článku ukazuje, jak vytvořit síťový filtr, který používá standardní port TCP 80 (předpokládá se, že jste již spustili příslušné služby a otevřeli všechna pravidla brány firewall operačního systému na virtuálním počítači).

Po vytvoření virtuálního počítače, který je nakonfigurovaný tak, aby zobrazoval webové požadavky na standardním portu TCP 80, můžete:

1. Vytvořte skupinu zabezpečení sítě.

2. Vytvořte příchozí pravidlo zabezpečení umožňující přenos a přiřaďte hodnoty následujícím nastavením:

   - **Rozsahy cílových přístavů**: 80

   - **Rozsahy zdrojových portů**: * (umožňuje libovolný zdrojový port)

   - **Hodnota priority**: Zadejte hodnotu, která má nižší než 65 500 priorit, než je výchozí pravidlo příchozího režimu catch-all.

3. Přidružte skupinu zabezpečení sítě k síťovému rozhraní nebo podsíti virtuálního zařízení.

Přestože tento příklad používá jednoduché pravidlo pro povolení přenosu http, můžete také použít skupiny zabezpečení sítě a pravidla k vytvoření složitějšíkonfigurace sítě. 




