---
title: zahrnout soubor
titleSuffix: Azure
description: zahrnout soubor
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: include
ms.date: 3/18/2020
ms.author: derekol
ms.openlocfilehash: e5804aa1b005e670d8b430b1c0a3bd62efd0bb06
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687051"
---
1. Vyberte připojení partnerského vztahu, které chcete povolit pro službu Partnerského vztahu Azure. Pak vyberte **...**  >  **Upravit připojení**.
    > [!div class="mx-imgBorder"]
    > ![Připojení partnerského vztahu Upravit připojení](../media/setup-direct-modify-editconnection.png)
1. V části **Použít pro službu partnerského vztahu**vyberte **Povoleno** a pak vyberte **Uložit**.
    > [!div class="mx-imgBorder"]
    > ![Připojení partnerského vztahu povolit službu partnerského vztahu](../media/setup-direct-modify-editconnectionsettings-peering-service.png)
1. Na obrazovce **Přehled** se zobrazí podrobnosti o nasazení. Po dokončení nasazení vyberte **Přejít na prostředek**.
    > [!div class="mx-imgBorder"]
    > ![Vaše nasazení je dokončeno.](../media/setup-direct-modify-overview-deployment-complete.png)

1. V podokně **Registrované předpony** vyberte **Přidat registrovanou předponu**.
    > [!div class="mx-imgBorder"]
    > ![Přidání registrované předpony](../media/setup-direct-modify-add-registered-prefix.png)
1. Zaregistrujte předponu výběrem **položky Name** a **Prefix** a výběrem **možnosti Uložit**.
    > [!div class="mx-imgBorder"]
    >  ![Registrace předpony](../media/setup-direct-modify-register-a-prefix.png) 

1. Po vytvoření předpony se zobrazí v seznamu **registrovaných předpon**. Chcete-li zobrazit další podrobnosti, vyberte **název** předpony.
    > [!div class="mx-imgBorder"]
    > ![Registrované předpony a připojení](../media/setup-direct-modify-registered-prefixes.png)
1. Na stránce registrované předpony se zobrazí úplné podrobnosti, které obsahují **klávesu Předpona** pro každou předponu. Tento klíč musí být poskytnut zákazníkovi přidělené tuto předponu od poskytovatele služeb Internetu. Zákazník pak může zaregistrovat svou předponu v rámci svého předplatného pomocí tohoto klíče.
    > [!div class="mx-imgBorder"]
    > ![Předpona s předponou](../media/setup-direct-modify-registered-prefix-detail.png)