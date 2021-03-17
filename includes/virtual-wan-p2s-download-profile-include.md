---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 02/05/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: eea8cb61ce98b2394abff6995e5cc89f00a7cf46
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2021
ms.locfileid: "99628906"
---
1. Na stránce pro virtuální síť WAN vyberte **Konfigurace sítě VPN uživatele**.
1. Na stránce **konfigurace VPN uživatele** vyberte konfiguraci a pak vyberte **Stáhnout profil VPN pro uživatele sítě WAN**. Když si stáhnete konfiguraci na úrovni sítě WAN, získáte integrovaný profil VPN uživatele s Traffic Manager. Další informace o globálních profilech nebo profilu založeném na rozbočovači najdete v tématu [profily centra](../articles/virtual-wan/global-hub-profile.md). Scénáře převzetí služeb při selhání jsou zjednodušené s globálním profilem.

   
   Pokud z nějakého důvodu není rozbočovač k dispozici, integrovaná správa provozu, kterou služba poskytuje, zajišťuje připojení (přes jiný rozbočovač) k prostředkům Azure pro uživatele typu Point-to-site. Konfiguraci sítě VPN pro konkrétní centrum si můžete vždycky stáhnout tak, že přejdete do centra. V části **Uživatelská síť VPN (Ukázat na lokalitu)** stáhněte profil **VPN uživatele** virtuálního rozbočovače.
1. Na stránce **Stáhnout profil VPN uživatele sítě WAN** vyberte **typ ověřování** a pak vyberte **generovat a stáhnout profil**. Balíček profilu se vygeneruje a soubor ZIP obsahující nastavení konfigurace se stáhne.
