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
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96010763"
---
Pokud pracujete s modelem nasazení Správce prostředků, můžete přejít na nové SKU brány. Když změníte ze starší skladové položky brány na novou SKLADOVOU položku, odstraníte stávající bránu VPN a vytvoříte novou bránu VPN.

Pracovního postupu

1. Odeberte všechna připojení k bráně virtuální sítě.
2. Odstraňte starou bránu VPN.
3. Vytvořte novou bránu VPN.
4. Aktualizujte místní zařízení VPN novou IP adresou brány VPN (pro připojení typu Site-to-Site).
5. Aktualizujte hodnotu IP adresy brány pro všechny místní síťové brány typu VNet-to-VNet, které se připojují k této bráně.
6. Stáhněte nové klientské balíčky konfigurace klienta VPN pro klienty P2S, kteří se připojují k virtuální síti přes tuto bránu VPN.
7. Znovu vytvořte všechna připojení k bráně virtuální sítě.

Požadavky:

* Pokud chcete přejít na nové SKU, musí být vaše brána VPN v modelu nasazení Správce prostředků.
* Pokud máte klasickou bránu VPN, musíte pro tuto bránu používat starší starší verze SKU, ale můžete změnit velikost mezi staršími SKU. Nemůžete změnit na nové SKU.
* Při změně z původní SKU na novou SKU budete mít výpadek připojení.
* Při přechodu na novou skladovou jednotku brány se změní veřejná IP adresa pro vaši bránu VPN. K tomu dojde i v případě, že zadáte stejný objekt veřejné IP adresy, který jste použili dříve.