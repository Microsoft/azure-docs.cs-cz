---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 2c1a4a1931bc2e38b0bee5f90518b01fdf4767a1
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "30196761"
---
Pokud pracujete s modelem nasazení Resource Manager, můžete změnit na nový SKU brány. Když změníte ze starší verze skladová položka brány na nové SKU, odstraňte existující bránu VPN a vytvořte novou bránu VPN.

Pracovní postup:

1. Odeberte všechna připojení k bráně virtuální sítě.
2. Odstraňte starou bránu VPN.
3. Vytvořte novou bránu VPN.
4. Aktualizujte místní zařízení VPN novou IP adresou brány VPN (pro připojení typu Site-to-Site).
5. Aktualizujte hodnotu IP adresy brány pro všechny místní síťové brány typu VNet-to-VNet, které se připojují k této bráně.
6. Stáhněte nové klientské balíčky konfigurace klienta VPN pro klienty P2S, kteří se připojují k virtuální síti přes tuto bránu VPN.
7. Znovu vytvořte všechna připojení k bráně virtuální sítě.

Aspekty:

* Pro přesun do nového SKU, musí být vaše brána sítě VPN v modelu nasazení Resource Manager.
* Pokud máte classic brána sítě VPN, musí pokračovat pomocí starší SKU starší verzi této brány, ale můžete změnit velikost mezi starší verze SKU. Nelze změnit na nový SKU.
* Při změně ze starší verze SKU pro nové SKU budete mít připojení k výpadku.
* Při změně k nové bráně SKU, veřejné IP adresy pro bránu VPN změní. K tomu dojde i v případě, že zadáte stejný veřejné IP adresy objekt, který jste použili předtím.