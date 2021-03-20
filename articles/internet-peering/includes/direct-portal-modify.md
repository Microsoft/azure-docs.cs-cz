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
ms.openlocfilehash: c3212acd5015edbb639b8904b885c718d654e5b4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96356257"
---
Tato část popisuje, jak provést následující operace úprav pro přímý partnerský vztah.

### <a name="add-direct-peering-connections"></a>Přidat připojení s přímým partnerským vztahem
1. Vyberte tlačítko **+ Přidat připojení** a nakonfigurujte nové připojení partnerského vztahu.
    > [!div class="mx-imgBorder"]
    > ![Stránka AshburnPeering-Connections obsahuje seznam připojení. Tlačítko + Přidat připojení je zvýrazněné.](../media/setup-direct-modify-addconnection.png)

1. Vyplňte formulář **přímého partnerského připojení** a vyberte **Uložit**. Nápovědu ke konfiguraci připojení partnerských vztahů najdete v části Postup vytvoření a zřízení přímého partnerského vztahu.
    > [!div class="mx-imgBorder"]
    > ![Formulář přímého připojení partnerského vztahu](../media/setup-direct-modify-savenewconnection.png)

### <a name="remove-direct-peering-connections"></a>Odebrat připojení s přímým partnerským vztahem

Odebrání připojení se v Azure Portal aktuálně nepodporuje. Další informace získáte u [partnerského vztahu Microsoftu](mailto:peeringexperience@microsoft.com).

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>Upgradovat nebo downgradovat šířku pásma u aktivních připojení
1. Vyberte připojení partnerského vztahu, které chcete upravit, a pak vyberte **...**  >  **Upravit připojení**.
    > [!div class="mx-imgBorder"]
    > ![Upravit připojení](../media/setup-direct-modify-editconnection.png)

1. Upravte šířku pásma přesunutím posuvníku a potom vyberte **Uložit**.
    > [!div class="mx-imgBorder"]
    > ![Úprava šířky pásma](../media/setup-direct-modify-editconnectionsettings.png)

### <a name="add-ipv4-or-ipv6-session-information-on-active-connections"></a>Přidat informace o relaci protokolu IPv4 nebo IPv6 pro aktivní připojení
1. Vyberte připojení partnerského vztahu, které chcete upravit, a pak vyberte **...**  >  **Upravte připojení** , jak je znázorněno v kroku 1.
1. Zadejte **předponu IPv4 relace** nebo informace **předpony IPv6 relace** a vyberte **Uložit**.

### <a name="remove-ipv4-or-ipv6-session-information-on-active-connections"></a>Odebrat informace o relaci protokolu IPv4 nebo IPv6 pro aktivní připojení
Na portálu se aktuálně nepodporují informace o odebrání **předpony IPv4 relace** nebo **předpony IPv6 relace** . Další informace získáte u [partnerského vztahu Microsoftu](mailto:peeringexperience@microsoft.com).
