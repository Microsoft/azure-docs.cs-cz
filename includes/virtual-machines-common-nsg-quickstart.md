---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 05/17/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: d9c8a0e6a3bd6d79a11ee0d0dab0500a209e5571
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38944219"
---
Otevření portu nebo vytvoření koncového bodu, virtuálního počítače (VM) v Azure vytvořte filtr sítě pro podsíť nebo síťové rozhraní virtuálního počítače. Tyto filtry, které řídí příchozí a odchozí přenosy dat, umístíte na skupinu zabezpečení sítě, který je připojen k prostředku, který přijímá provoz.

Použijeme běžným Příkladem webového provozu na portu 80. Jakmile budete mít virtuální počítač, který je nakonfigurovaný pro obsloužení webové požadavky na standardní TCP port 80 (nezapomeňte spustit příslušné služby a otevřít žádná pravidla brány firewall operačního systému na virtuálním počítači a), můžete:

1. Vytvořte skupinu zabezpečení sítě.
2. Vytvořte příchozí pravidlo povolení provozu s využitím:
   * Rozsah cílových portů "80"
   * Rozsah zdrojových portů z "*" (povoluje jakéhokoli zdrojového portu)
   * hodnotu priority méně 65 500 (Chcete-li být odepřít vyšší prioritou než pokrývající vše výchozí příchozí pravidlo)
3. Přidružte síťové rozhraní virtuálního počítače nebo podsítě skupinu zabezpečení sítě.

Můžete vytvořit složitější síť konfigurace zabezpečení prostředí pomocí skupin zabezpečení sítě a pravidel. Náš příklad používá jenom jednu nebo dvě pravidla, která umožňují provoz protokolu HTTP nebo vzdálenou správu. Další informace naleznete na následujícím ["Další informace"](#more-information-on-network-security-groups) části nebo [co je skupina zabezpečení sítě?](../articles/virtual-network/security-overview.md)

