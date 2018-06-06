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
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34684978"
---
Otevření portu nebo vytvořte koncového bodu, virtuální počítač (VM) v Azure vytvořením filtr sítě na podsíť nebo rozhraní sítě virtuálních počítačů. Tyto filtry, které řídí příchozí a odchozí přenosy, můžete umístit na skupinu zabezpečení sítě, který je připojen k prostředku, který přijímá provoz.

Použijeme Běžným příkladem webové přenosy na portu 80. Jakmile je virtuální počítač, který je nakonfigurován tak, aby webové požadavky na standardní TCP port 80 (nezapomeňte spustit příslušné služby a otevřete všechna pravidla brány firewall operačního systému na virtuálním počítači i), můžete:

1. Vytvořte skupinu zabezpečení sítě.
2. Vytvoření příchozího pravidla umožňuje provoz se:
   * Rozsah cílových portů "80"
   * Rozsah zdrojových portů z "*" (což jakéhokoli zdrojového portu)
   * hodnotou priority menší 65 500 (tak, aby se odepřel vyšší priorita než catch všechny výchozí pravidla pro příchozí)
3. Skupina zabezpečení sítě přidružte rozhraní sítě virtuálních počítačů nebo podsíť.

Můžete vytvořit konfiguraci komplexní sítě na zabezpečit vaše prostředí pomocí skupin zabezpečení sítě a pravidla. Naše Ukázka používá jenom jednu nebo dvě pravidla, která povolit provoz protokolu HTTP nebo pro vzdálenou správu. Další informace najdete v tématu následující ["Další informace"](#more-information-on-network-security-groups) části nebo [co je skupina zabezpečení sítě?](../articles/virtual-network/security-overview.md)

