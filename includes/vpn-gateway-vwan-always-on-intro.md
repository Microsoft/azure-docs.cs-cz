---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/12/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4ea97e2dbee87f7ab129c4295276c9024c0212c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80116922"
---
Novou funkcí klienta Windows 10 VPN, Always On, je schopnost udržovat připojení VPN. Pomocí funkce Always On se aktivní profil VPN může automaticky připojit a zůstat připojený na základě aktivačních událostí, jako je přihlášení uživatele, změna stavu sítě nebo aktivní obrazovka zařízení.

Brány s Windows 10 Always On můžete použít k vytvoření trvalých uživatelských tunelových propojení a tunelových propojení zařízení do Azure. Tento článek vám pomůže nakonfigurovat vždy na vpn uživatelského tunelu.

Vždy na VPN připojení zahrnují jeden ze dvou typů tunelů:

* **Tunelové propojení zařízení**: Připojí se k určeným serverům VPN před přihlášením uživatelů k zařízení. Scénáře připojení před přihlášením a správa zařízení používají tunelové propojení zařízení.

* **Uživatelský tunelový propojení**: Připojí se až po přihlášení uživatelů k zařízení. Pomocí uživatelských tunelových propojení můžete přistupovat k prostředkům organizace prostřednictvím serverů VPN.

Tunelové propojení zařízení a tunelové propojení uživatelů pracují nezávisle na jejich profilech VPN. Mohou být připojeny současně a mohou podle potřeby používat různé metody ověřování a další nastavení konfigurace VPN.
