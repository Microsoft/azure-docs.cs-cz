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
ms.openlocfilehash: e1a9256cc9a015a5d8286de5e5bd7b61ed915a3b
ms.sourcegitcommit: 5abc3919a6b99547f8077ce86a168524b2aca350
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/07/2020
ms.locfileid: "91812671"
---
1. Na stránce pro virtuální síť WAN vyberte **Konfigurace sítě VPN uživatele**.
1. V horní části stránky vyberte **Stáhnout uživatel konfigurace sítě VPN**. Když si stáhnete konfiguraci na úrovni sítě WAN, získáte integrovaný profil VPN uživatele s Traffic Manager. Další informace o globálních profilech nebo profilu založeném na rozbočovači najdete v tématu [profily centra](https://docs.microsoft.com/azure/virtual-wan/global-hub-profile). Scénáře převzetí služeb při selhání jsou zjednodušené s globálním profilem.

   Pokud z nějakého důvodu není rozbočovač k dispozici, integrovaná správa provozu, kterou služba poskytuje, zajišťuje připojení (přes jiný rozbočovač) k prostředkům Azure pro uživatele typu Point-to-site. Konfiguraci sítě VPN pro konkrétní centrum si můžete vždycky stáhnout tak, že přejdete do centra. V části **Uživatelská síť VPN (Ukázat na lokalitu)** stáhněte profil **VPN uživatele** virtuálního rozbočovače.
1. Až se soubor dokončí, vyberte odkaz pro stažení.
1. K nakonfigurování klientů VPN použijte profilový soubor.
