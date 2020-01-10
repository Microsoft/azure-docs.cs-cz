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
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774559"
---
Tato část popisuje, jak provést následující operace úprav pro přímý partnerský vztah:

### <a name="add-exchange-peering-connections"></a>Přidat připojení partnerských vztahů Exchange

1. Klikněte na tlačítko **+ Přidat připojení** v horní části a nakonfigurujte nové připojení partnerského vztahu.
    > [!div class="mx-imgBorder"]
    > ![zobrazení prostředků partnerského vztahu](../media/setup-exchange-modify-addconnection.png)
1. Vyplňte formulář **připojení partnerského vztahu systému Exchange** a klikněte na **Uložit**. Nápovědu ke konfiguraci partnerského připojení najdete v části "vytvoření a zřízení přímého partnerského vztahu" výše.
    > [!div class="mx-imgBorder"]
    > ![zobrazení prostředků partnerského vztahu](../media/setup-exchange-modify-savenewconnection.png)

### <a name="remove-exchange-peering-connections"></a>Odebrat připojení partnerských vztahů Exchange

1. Klikněte na připojení partnerských vztahů, které chcete odstranit, a potom klikněte na tlačítko **...**  > **Odstranit připojení** .
    > [!div class="mx-imgBorder"]
    > ![připojení partnerského vztahu odstranění](../media/setup-exchange-modify-deleteconnection.png)
1. Do pole **Potvrdit odstranění** zadejte ID prostředku, jak je uvedeno ve zvýrazněných polích a klikněte na **Odstranit**.
    > [!div class="mx-imgBorder"]
    > ](../media/setup-exchange-modify-deleteconnectionconfirm.png) DeleteConfirm připojení ![partnerských vztahů

### <a name="add-ipv4ipv6-session-on-active-connections"></a>Přidat relaci IPv4/IPv6 pro aktivní připojení

1. Klikněte na připojení partnerských vztahů, které chcete upravit, a pak klikněte na tlačítko pro úpravy **...**  > **Upravit připojení** .
    > [!div class="mx-imgBorder"]
    > ![úprav připojení partnerských vztahů](../media/setup-exchange-modify-editconnection.png)
1. Přidejte **adresu IPv4** nebo informace o **adrese IPv6** a klikněte na **Uložit**.
    > [!div class="mx-imgBorder"]
    > ![upravit připojení partnerských vztahů](../media/setup-exchange-modify-editconnectionsettings.png)

### <a name="remove-ipv4ipv6-session-on-active-connections"></a>Odebrat relaci protokolu IPv4/IPv6 u aktivních připojení

Odebrání relace IPv4/IPv6 ze stávajícího připojení není aktuálně na portálu podporováno. Kontaktujte [partnerský vztah Microsoftu](mailto:peeringexperience@microsoft.com).