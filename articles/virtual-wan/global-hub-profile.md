---
title: Stažení globálních nebo centrálních profilů VPN azure virtuální sítě WAN | Dokumenty společnosti Microsoft
description: Seznamte se s automatizovaným škálovatelným připojením mezi větvemi virtuální sítě WAN, dostupnými oblastmi a partnery.
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 1/31/2020
ms.author: alzam
ms.openlocfilehash: 3b7e765dbd024d46939e8989993f0c882b2a8f4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76965226"
---
# <a name="download-a-global-or-hub-based-profile-for-user-vpn-clients"></a>Stažení globálního nebo centrálního profilu pro klienty VPN uživatelů

Azure Virtual WAN nabízí dva typy připojení pro vzdálené uživatele: globální a hub založené. Pomocí následujících částí se dozvíte o profilu a stáhněte si ho.

## <a name="global-profile"></a>Globální profil

Profil odkazuje na nástroj pro vyrovnávání zatížení, který zahrnuje všechny aktivní rozbočovače VPN uživatele. Uživatel je přesměrován do rozbočovače, který je nejblíže k zeměpisné poloze uživatele. Tento typ připojení je užitečný, když uživatelé často cestují do různých míst. Stažení **globálního** profilu:

1. Přejděte na virtuální síť WAN.
2. Klepněte na **položku Konfigurace uživatelské sítě VPN**.
3. Zvýrazněte konfiguraci, pro kterou chcete profil stáhnout.
4. Klikněte na **stáhnout profil vpn uživatele sítě WAN**.

   ![Globální profil](./media/global-hub-profile/global1.png)

## <a name="hub-based-profile"></a>Profil založený na rozbočovači

Profil odkazuje na jeden rozbočovač. Uživatel se může připojit pouze k určitému rozbočovači pomocí tohoto profilu. Stažení profilu **centra:**

1. Přejděte na virtuální síť WAN.
2. Na stránce Přehled klikněte na **Centrum.**

    ![Profil rozbočovače 1](./media/global-hub-profile/hub1.png)
3. Klikněte na **Uživatelská síť VPN (přejděte na web).**
4. Klikněte na **Stáhnout profil vpn uživatele virtuálního centra**.

   ![Profil rozbočovače 2](./media/global-hub-profile/hub2.png)
5. Zkontrolujte **EAPTLS**.
6. Klepněte na **tlačítko Generovat a stáhnout profil**.

   ![Profil rozbočovače 3](./media/global-hub-profile/download.png)

## <a name="next-steps"></a>Další kroky

Další informace o službě Virtual WAN najdete v článku [Přehled služby Virtual WAN](virtual-wan-about.md).
