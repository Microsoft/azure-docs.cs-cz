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
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "81680924"
---
Tato část popisuje, jak provést následující operace úprav pro přímý partnerský vztah.

### <a name="add-exchange-peering-connections"></a>Přidat připojení partnerských vztahů Exchange

1. Vyberte tlačítko **+ Přidat připojení** a nakonfigurujte nové připojení partnerského vztahu.
    > [!div class="mx-imgBorder"]
    > ![Zobrazení prostředků partnerského vztahu](../media/setup-exchange-modify-addconnection.png)
1. Vyplňte formulář **připojení partnerského vztahu systému Exchange** a vyberte **Uložit**. Nápovědu ke konfiguraci připojení partnerských vztahů najdete v části Postup vytvoření a zřízení přímého partnerského vztahu.
    > [!div class="mx-imgBorder"]
    > ![Formulář připojení partnerského vztahu Exchange](../media/setup-exchange-modify-savenewconnection.png)

### <a name="remove-exchange-peering-connections"></a>Odebrat připojení partnerských vztahů Exchange

1. Vyberte připojení partnerského vztahu, které chcete odstranit, a pak vyberte **...**  >  **Odstraňte připojení**.
    > [!div class="mx-imgBorder"]
    > ![Tlačítko pro odstranění připojení](../media/setup-exchange-modify-deleteconnection.png)
1. Do pole **Potvrdit odstranění** zadejte ID prostředku a vyberte **Odstranit**.
    > [!div class="mx-imgBorder"]
    > ![Odstranit potvrzení](../media/setup-exchange-modify-deleteconnectionconfirm.png)

### <a name="add-an-ipv4-or-ipv6-session-on-active-connections"></a>Přidání relace protokolu IPv4 nebo IPv6 na aktivní připojení

1. Vyberte připojení partnerského vztahu, které chcete upravit, a pak vyberte **...**  >  **Upravit připojení**.
    > [!div class="mx-imgBorder"]
    > ![Tlačítko Upravit připojení](../media/setup-exchange-modify-editconnection.png)
1. Přidejte **adresu IPv4** nebo informace o **adrese IPv6** a vyberte **Uložit**.
    > [!div class="mx-imgBorder"]
    > ![Změny připojení partnerských vztahů](../media/setup-exchange-modify-editconnectionsettings.png)

### <a name="remove-an-ipv4-or-ipv6-session-on-active-connections"></a>Odebrání relace protokolu IPv4 nebo IPv6 v aktivních připojeních

Odebrání relace protokolu IPv4 nebo IPv6 ze stávajícího připojení není aktuálně na portálu podporováno. Další informace získáte u [partnerského vztahu Microsoftu](mailto:peeringexperience@microsoft.com).