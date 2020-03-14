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
ms.openlocfilehash: 2783c828f96eeb3539e2266eaf1d6d590a6af4c4
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/14/2020
ms.locfileid: "79370959"
---
Novou funkcí klienta sítě VPN s Windows 10, která je vždycky zapnutá, je možnost udržovat připojení VPN. Díky službě Always On se aktivní profil sítě VPN může připojit automaticky a zůstat připojený na triggerech, jako jsou přihlášení uživatele, změna stavu sítě nebo obrazovka zařízení aktivní.

Brány virtuální sítě Azure s Windows 10 se vždycky používají k navázání trvalých tunelů uživatelů a tunelových připojení zařízení do Azure. Tento článek vám pomůže nakonfigurovat tunelové propojení uživatelů VPN typu Always On.

Připojení k síti VPN Always On zahrnuje jeden ze dvou typů tunelů:

* **Tunelové propojení zařízení**: připojí se k zadaným serverům sítě VPN, než se uživatelé přihlásí k zařízení. Scénáře připojení před přihlášením a Správa zařízení používají tunelové zařízení.

* **Tunelové propojení uživatelů**: připojuje se až po přihlášení uživatelů k zařízení. Pomocí tunelů uživatelů můžete získat přístup k prostředkům organizace prostřednictvím serverů VPN.

Tunely zařízení a tunely uživatelů fungují nezávisle na jejich profilech sítě VPN. Můžou být připojené ve stejnou dobu a můžou podle potřeby používat jiné metody ověřování a další nastavení konfigurace VPN.
