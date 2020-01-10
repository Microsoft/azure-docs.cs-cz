---
title: zahrnout soubor
titleSuffix: Azure
description: zahrnout soubor
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: d9ff01cf0180dae7f75d9753ba889d0caddad937
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775339"
---
Tato část popisuje, jak provést následující operace úprav pro přímý partnerský vztah:

### <a name="add-direct-peering-connections"></a>Přidat připojení s přímým partnerským vztahem
1. Klikněte na tlačítko **+ Přidat připojení** v horní části a nakonfigurujte nové připojení partnerského vztahu.
    > [!div class="mx-imgBorder"]
    > ![zobrazení prostředků partnerského vztahu](../media/setup-direct-modify-addconnection.png)
1. Vyplňte formulář s **přímým partnerským vztahem** a klikněte na **Uložit**. Nápovědu ke konfiguraci partnerského připojení najdete v části "vytvoření a zřízení přímého partnerského vztahu" výše.
    > [!div class="mx-imgBorder"]
    > ![zobrazení prostředků partnerského vztahu](../media/setup-direct-modify-savenewconnection.png)

### <a name="remove-direct-peering-connections"></a>Odebrat připojení s přímým partnerským vztahem

Odebrání připojení není na portálu aktuálně podporováno. Kontaktujte [partnerský vztah Microsoftu](mailto:peeringexperience@microsoft.com).

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>Upgradovat nebo downgradovat šířku pásma u aktivních připojení
1. Klikněte na připojení partnerských vztahů, které chcete upravit, a pak klikněte na tlačítko pro úpravy **...**  > **Upravit připojení** .
    > [!div class="mx-imgBorder"]
    > ![úprav připojení partnerských vztahů](../media/setup-direct-modify-editconnection.png)
1. Upravte šířku pásma, jak je znázorněno níže, a pak klikněte na **Uložit**.
    > [!div class="mx-imgBorder"]
    > ![připojení partnerského vztahu pro změnu šířky pásma](../media/setup-direct-modify-editconnectionsettings.png)

### <a name="add-ipv4ipv6-session-on-active-connections"></a>Přidat relaci protokolu IPv4/IPv6 k aktivním připojením.
1. Klikněte na připojení partnerských vztahů, které chcete upravit, a pak klikněte na tlačítko pro úpravy **...**  > **Upravit připojení** , jak vidíte výše.
1. Přidejte **předponu IPv4 relace** nebo informace **předpony IPv6 relace** a klikněte na **Uložit**.

### <a name="remove-ipv4ipv6-session-on-active-connections"></a>Odebrat relaci protokolu IPv4/IPv6 u aktivních připojení.
    Removing a **Session IPv4 prefix** or **Session IPv6 prefix** info is not currently supported on portal. Contact [Microsoft peering](mailto:peeringexperience@microsoft.com).
