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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775339"
---
Tato část popisuje, jak provádět následující operace změny pro přímý partnerský vztah:

### <a name="add-direct-peering-connections"></a>Přidání připojení přímého partnerského vztahu
1. Klikněte na tlačítko **+ Přidat připojení** nahoře a nakonfigurujte nové připojení partnerského vztahu.
    > [!div class="mx-imgBorder"]
    > ![Zobrazení prostředku partnerského vztahu](../media/setup-direct-modify-addconnection.png)
1. Vyplňte formulář **Přímé připojení partnerského vztahu** a klepněte na tlačítko **Uložit**. Nápovědu ke konfiguraci připojení partnerského vztahu naleznete výše uvedené kroky v části Vytvoření a zřízení přímého partnerského vztahu.
    > [!div class="mx-imgBorder"]
    > ![Zobrazení prostředku partnerského vztahu](../media/setup-direct-modify-savenewconnection.png)

### <a name="remove-direct-peering-connections"></a>Odebrání připojení přímého partnerského vztahu

Odebrání připojení není aktuálně podporováno na portálu. Obraťte se na [partnerský vztah společnosti Microsoft](mailto:peeringexperience@microsoft.com).

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>Upgrade nebo snížení šířky pásma u aktivních připojení
1. Klikněte na peering připojení, které chcete upravit a pak klikněte na **...**  >  **Tlačítko Upravit připojení.**
    > [!div class="mx-imgBorder"]
    > ![Úprava připojení partnerského vztahu](../media/setup-direct-modify-editconnection.png)
1. Upravte šířku pásma, jak je znázorněno níže, a klepněte na tlačítko **Uložit**.
    > [!div class="mx-imgBorder"]
    > ![Změnit šířku pásma připojení partnerského vztahu](../media/setup-direct-modify-editconnectionsettings.png)

### <a name="add-ipv4ipv6-session-on-active-connections"></a>Přidejte relaci IPv4/IPv6 u aktivních připojení.
1. Klikněte na peering připojení, které chcete upravit a pak klikněte na **...**  > Upravit tlačítko **připojení,** jak je znázorněno výše.
1. Přidejte **předponu IPv4** relace nebo informace o **předponě IPv6** relace a klepněte na tlačítko **Uložit**.

### <a name="remove-ipv4ipv6-session-on-active-connections"></a>Odebrání relace IPv4/IPv6 u aktivních připojení.
    Removing a **Session IPv4 prefix** or **Session IPv6 prefix** info is not currently supported on portal. Contact [Microsoft peering](mailto:peeringexperience@microsoft.com).
