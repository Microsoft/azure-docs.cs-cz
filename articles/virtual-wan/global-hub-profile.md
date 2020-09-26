---
title: Stažení globálních profilů sítě VPN založených na službě Azure Virtual WAN nebo centrálních rozbočovačů Microsoft Docs
description: Přečtěte si, jak Azure Virtual WAN nabízí dva typy připojení pro vzdálené uživatele a jak stáhnout profil.
services: virtual-wan
author: kumudD
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: alzam
ms.openlocfilehash: 6d5c4ba0f1f55119d1ec38296e67ae3e90c52650
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91313718"
---
# <a name="download-a-global-or-hub-based-profile-for-user-vpn-clients"></a>Stáhnout globální profil nebo profil založený na centru pro klienty VPN uživatelů

Azure Virtual WAN nabízí dva typy připojení pro vzdálené uživatele: globální a založené na rozbočovači. V následujících částech najdete informace o tom, jak si můžete stáhnout profil. 

> [!IMPORTANT]
> Ověřování RADIUS podporuje jenom profil založený na rozbočovači.

## <a name="global-profile"></a>Globální profil

Profil odkazuje na nástroj pro vyrovnávání zatížení, který zahrnuje všechna aktivní rozbočovače VPN uživatele. Uživatel se přesměruje do centra, které je nejblíže zeměpisné poloze uživatele. Tento typ připojení je užitečný, když uživatelé často cestují do různých umístění. Stažení **globálního** profilu:

1. Přejděte na virtuální síť WAN.
2. Klikněte na **Konfigurace sítě VPN uživatele**.
3. Zvýrazněte konfiguraci, pro kterou chcete profil stáhnout.
4. Klikněte na **Stáhnout profil VPN uživatele sítě WAN**.

   ![Globální profil](./media/global-hub-profile/global1.png)

## <a name="hub-based-profile"></a>Profil založený na centru

Profil odkazuje na jeden rozbočovač. Uživatel se může připojit pouze k určitému centru pomocí tohoto profilu. Stažení profilu **založeného na rozbočovači** :

1. Přejděte na virtuální síť WAN.
2. Na stránce Přehled klikněte na **centrum** .

    ![Profil centra 1](./media/global-hub-profile/hub1.png)
3. Klikněte na **uživatel VPN (Point to site)**.
4. Klikněte na **Stáhnout profil VPN uživatele virtuálního rozbočovače**.

   ![Profil centra 2](./media/global-hub-profile/hub2.png)
5. Podívejte se na **EAPTLS**.
6. Klikněte na **vygenerovat a stáhnout profil**.

   ![Profil centra 3](./media/global-hub-profile/download.png)

## <a name="next-steps"></a>Další kroky

Další informace o službě Virtual WAN najdete v článku [Přehled služby Virtual WAN](virtual-wan-about.md).
