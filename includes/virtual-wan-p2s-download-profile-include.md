---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/06/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 317d75dae83e8529bda25897b77824d8cd36e72e
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2020
ms.locfileid: "95561290"
---
1. Na stránce pro virtuální síť WAN vyberte **Konfigurace sítě VPN uživatele**.
1. V horní části stránky vyberte **Stáhnout uživatel konfigurace sítě VPN**. Když si stáhnete konfiguraci na úrovni sítě WAN, získáte integrovaný profil VPN uživatele s Traffic Manager. Další informace o globálních profilech nebo profilu založeném na rozbočovači najdete v tématu [profily centra](../articles/virtual-wan/global-hub-profile.md). Scénáře převzetí služeb při selhání jsou zjednodušené s globálním profilem.

   Pokud z nějakého důvodu není rozbočovač k dispozici, integrovaná správa provozu, kterou služba poskytuje, zajišťuje připojení (přes jiný rozbočovač) k prostředkům Azure pro uživatele typu Point-to-site. Konfiguraci sítě VPN pro konkrétní centrum si můžete vždycky stáhnout tak, že přejdete do centra. V části **Uživatelská síť VPN (Ukázat na lokalitu)** stáhněte profil **VPN uživatele** virtuálního rozbočovače.
1. Až se soubor dokončí, vyberte odkaz pro stažení.
1. K nakonfigurování klientů VPN použijte profilový soubor.