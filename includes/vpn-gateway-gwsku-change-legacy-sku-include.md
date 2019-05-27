---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/15/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4c232e1ce183c6935d625b5bc9987a4981865ae4
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66159197"
---
Pokud pracujete s modelem nasazení Resource Manager, můžete změnit na nové SKU brány. Když změníte ze starší verze SKU brány na novou skladovou Položku, odstraňte stávající bránu VPN a vytvoření nové brány VPN.

Pracovní postup:

1. Odeberte všechna připojení k bráně virtuální sítě.
2. Odstraňte starou bránu VPN.
3. Vytvořte novou bránu VPN.
4. Aktualizujte místní zařízení VPN novou IP adresou brány VPN (pro připojení typu Site-to-Site).
5. Aktualizujte hodnotu IP adresy brány pro všechny místní síťové brány typu VNet-to-VNet, které se připojují k této bráně.
6. Stáhněte nové klientské balíčky konfigurace klienta VPN pro klienty P2S, kteří se připojují k virtuální síti přes tuto bránu VPN.
7. Znovu vytvořte všechna připojení k bráně virtuální sítě.

Požadavky:

* Přesunout na nové SKU, musí být vaší brány VPN v modelu nasazení Resource Manager.
* Pokud máte brány VPN classic, musí nadále používat starší starší verze SKU této brány, ale můžete změnit velikost mezi starší skladové položky. Nelze změnit na nové SKU.
* Když změníte ze starší verze SKU na nové SKU, budete mít výpadek připojení.
* Při změně na novou SKU brány, se změní veřejnou IP adresu pro bránu VPN. K tomu dochází, i když zadáte stejného objektu veřejné IP adresy, které jste použili dříve.