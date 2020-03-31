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
ms.openlocfilehash: e3e87e59f5b3c95051d9ee53e4b8d87afe9d9ba8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774559"
---
Tato část popisuje, jak provádět následující operace změny pro přímý partnerský vztah:

### <a name="add-exchange-peering-connections"></a>Přidání připojení partnerského vztahu exchange

1. Klikněte na tlačítko **+ Přidat připojení** nahoře a nakonfigurujte nové připojení partnerského vztahu.
    > [!div class="mx-imgBorder"]
    > ![Zobrazení prostředku partnerského vztahu](../media/setup-exchange-modify-addconnection.png)
1. Vyplňte formulář **Připojení partnerského vztahu serveru Exchange** a klepněte na tlačítko **Uložit**. Nápovědu ke konfiguraci připojení partnerského vztahu naleznete výše uvedené kroky v části Vytvoření a zřízení přímého partnerského vztahu.
    > [!div class="mx-imgBorder"]
    > ![Zobrazení prostředku partnerského vztahu](../media/setup-exchange-modify-savenewconnection.png)

### <a name="remove-exchange-peering-connections"></a>Odebrání připojení partnerského vztahu serveru Exchange

1. Klikněte na peering připojení, které chcete odstranit a pak klikněte na **...**  >  **Tlačítko odstranit připojení.**
    > [!div class="mx-imgBorder"]
    > ![Odstranění připojení partnerského vztahu](../media/setup-exchange-modify-deleteconnection.png)
1. Zadejte ID zdroje do pole **Potvrdit odstranění,** jak je znázorněno ve zvýrazněných polích, a klepněte na **Odstranit**.
    > [!div class="mx-imgBorder"]
    > ![Připojení partnerského vztahu DeleteConfirm](../media/setup-exchange-modify-deleteconnectionconfirm.png)

### <a name="add-ipv4ipv6-session-on-active-connections"></a>Přidání relace IPv4/IPv6 u aktivních připojení

1. Klikněte na peering připojení, které chcete upravit a pak klikněte na **...**  >  **Tlačítko Upravit připojení.**
    > [!div class="mx-imgBorder"]
    > ![Úprava připojení partnerského vztahu](../media/setup-exchange-modify-editconnection.png)
1. Přidejte **adresu IPv4** nebo informace o **adrese IPv6** a klepněte na tlačítko **Uložit**.
    > [!div class="mx-imgBorder"]
    > ![Změnit připojení partnerského vztahu](../media/setup-exchange-modify-editconnectionsettings.png)

### <a name="remove-ipv4ipv6-session-on-active-connections"></a>Odebrání relace IPv4/IPv6 u aktivních připojení

Odebrání relace IPv4/IPv6 z existujícího připojení není aktuálně podporováno na portálu. Obraťte se na [partnerský vztah společnosti Microsoft](mailto:peeringexperience@microsoft.com).