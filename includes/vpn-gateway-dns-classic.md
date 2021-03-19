---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/08/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 0c5e3a095b312d36cb05a78e84a13bc2a73d95d5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "91875538"
---
Nastavení DNS nejsou požadovanou součástí této konfigurace, ale služba DNS je nutná, pokud chcete překlad názvů mezi virtuálními počítači. Zadání hodnoty nevytvoří nový server DNS. Server DNS, jehož IP adresu zadáte, by měl být server DNS, který dokáže přeložit názvy pro prostředky, ke kterým se připojujete.

Po vytvoření virtuální sítě můžete přidat IP adresu serveru DNS, aby bylo možné zpracovávat překlad názvů. Otevřete nastavení pro virtuální síť, vyberte servery DNS a přidejte IP adresu serveru DNS, který chcete použít pro překlad názvů.

1. Vyhledejte virtuální síť na portálu.
2. Na stránce pro virtuální síť v části **Nastavení** vyberte **servery DNS**.
3. Přidejte server DNS.
4. Nastavení uložíte tak, že v horní části stránky vyberete **Uložit** .