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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "67174887"
---
Pokud pracujete s modelem nasazení Resource Manager, můžete změnit na nové brány skum. Když změníte ze starší brány Skladové položky na novou skladovou položku, odstraníte existující bránu VPN a vytvoříte novou bránu VPN.

Pracovního postupu:

1. Odeberte všechna připojení k bráně virtuální sítě.
2. Odstraňte starou bránu VPN.
3. Vytvořte novou bránu VPN.
4. Aktualizujte místní zařízení VPN novou IP adresou brány VPN (pro připojení typu Site-to-Site).
5. Aktualizujte hodnotu IP adresy brány pro všechny místní síťové brány typu VNet-to-VNet, které se připojují k této bráně.
6. Stáhněte nové klientské balíčky konfigurace klienta VPN pro klienty P2S, kteří se připojují k virtuální síti přes tuto bránu VPN.
7. Znovu vytvořte všechna připojení k bráně virtuální sítě.

Požadavky:

* Chcete-li přejít na nové sloky, musí být brána VPN v modelu nasazení Správce prostředků.
* Pokud máte klasickou bránu VPN, musíte pokračovat v používání starších starších virtuálních zařízení pro tuto bránu, ale můžete změnit velikost mezi staršími virtuálními sadami. Nelze změnit na nové sku.
* Budete mít prostoje připojení při změně ze starší skladové položky na novou skladovou položku.
* Při přechodu na novou skladovou položku brány se změní veřejná IP adresa brány VPN. K tomu dochází i v případě, že zadáte stejný objekt veřejné IP adresy, který jste použili dříve.