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
ms.openlocfilehash: e06b5261ca6923e158c818d236a30cf6ebff189b
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680924"
---
Tato část popisuje, jak provést následující operace změny pro přímý partnerský vztah.

### <a name="add-exchange-peering-connections"></a>Přidání připojení partnerského vztahu exchange

1. Vyberte tlačítko **+ Přidat připojení** a nakonfigurujte nové připojení partnerského vztahu.
    > [!div class="mx-imgBorder"]
    > ![Zobrazení prostředku partnerského vztahu](../media/setup-exchange-modify-addconnection.png)
1. Vyplňte formulář **Připojení partnerského vztahu serveru Exchange** a vyberte **Uložit**. Nápovědu ke konfiguraci připojení partnerského vztahu najdete v krocích v části Vytvoření a zřízení přímého partnerského vztahu.
    > [!div class="mx-imgBorder"]
    > ![Formulář připojení partnerského vztahu serveru Exchange](../media/setup-exchange-modify-savenewconnection.png)

### <a name="remove-exchange-peering-connections"></a>Odebrání připojení partnerského vztahu serveru Exchange

1. Vyberte připojení partnerského vztahu, které chcete odstranit, a pak vyberte **...**  >  **Odstranit připojení**.
    > [!div class="mx-imgBorder"]
    > ![Tlačítko Odstranit připojení](../media/setup-exchange-modify-deleteconnection.png)
1. Do pole **Potvrdit odstranění** zadejte ID prostředku a vyberte **Odstranit**.
    > [!div class="mx-imgBorder"]
    > ![Odstranit potvrzení](../media/setup-exchange-modify-deleteconnectionconfirm.png)

### <a name="add-an-ipv4-or-ipv6-session-on-active-connections"></a>Přidání relace IPv4 nebo IPv6 u aktivních připojení

1. Vyberte připojení partnerského vztahu, které chcete upravit, a pak vyberte **...**  >  **Upravit připojení**.
    > [!div class="mx-imgBorder"]
    > ![Tlačítko Upravit připojení](../media/setup-exchange-modify-editconnection.png)
1. Přidejte **adresu IPv4** nebo informace o **adrese IPv6** a vyberte **Uložit**.
    > [!div class="mx-imgBorder"]
    > ![Změny připojení partnerského vztahu](../media/setup-exchange-modify-editconnectionsettings.png)

### <a name="remove-an-ipv4-or-ipv6-session-on-active-connections"></a>Odebrání relace IPv4 nebo IPv6 u aktivních připojení

Odebrání relace IPv4 nebo IPv6 z existujícího připojení není aktuálně podporováno na portálu. Další informace získáte od [společnosti Microsoft peeringu](mailto:peeringexperience@microsoft.com).