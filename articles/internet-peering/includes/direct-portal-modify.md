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
ms.openlocfilehash: 3894bf046ed4ee3f068e43dbc5bc5b7f2a1002b4
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681073"
---
Tato část popisuje, jak provést následující operace změny pro přímý partnerský vztah.

### <a name="add-direct-peering-connections"></a>Přidání připojení přímého partnerského vztahu
1. Vyberte tlačítko **+ Přidat připojení** a nakonfigurujte nové připojení partnerského vztahu.
    > [!div class="mx-imgBorder"]
    > ![Zobrazení prostředku partnerského vztahu](../media/setup-direct-modify-addconnection.png)

1. Vyplňte formulář **Přímé připojení partnerského vztahu** a vyberte **Uložit**. Nápovědu ke konfiguraci připojení partnerského vztahu najdete v krocích v části Vytvoření a zřízení přímého partnerského vztahu.
    > [!div class="mx-imgBorder"]
    > ![Formulář přímého připojení partnerského vztahu](../media/setup-direct-modify-savenewconnection.png)

### <a name="remove-direct-peering-connections"></a>Odebrání připojení přímého partnerského vztahu

Odebrání připojení není aktuálně podporováno na webu Azure Portal. Další informace získáte od [společnosti Microsoft peeringu](mailto:peeringexperience@microsoft.com).

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>Upgrade nebo snížení šířky pásma u aktivních připojení
1. Vyberte připojení partnerského vztahu, které chcete upravit, a pak vyberte **...**  >  **Upravit připojení**.
    > [!div class="mx-imgBorder"]
    > ![Upravit připojení](../media/setup-direct-modify-editconnection.png)

1. Upravte šířku pásma přesunutím jezdce a pak vyberte **Uložit**.
    > [!div class="mx-imgBorder"]
    > ![Změna šířky pásma](../media/setup-direct-modify-editconnectionsettings.png)

### <a name="add-ipv4-or-ipv6-session-information-on-active-connections"></a>Přidání informací o relaci IPv4 nebo IPv6 u aktivních připojení
1. Vyberte připojení partnerského vztahu, které chcete upravit, a pak vyberte **...**  >  **Upravte připojení** podle obrázku v kroku 1.
1. Zadejte informace **o předponě IPv4** relace nebo o **předponě IPv6** a vyberte **Uložit**.

### <a name="remove-ipv4-or-ipv6-session-information-on-active-connections"></a>Odebrání informací o relaci IPv4 nebo IPv6 u aktivních připojení
Odebrání informací **o předponě IPv4 relace** nebo **předponě IPv6** relace není na portálu aktuálně podporováno. Další informace získáte od [společnosti Microsoft peeringu](mailto:peeringexperience@microsoft.com).
