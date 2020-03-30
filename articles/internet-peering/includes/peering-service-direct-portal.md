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
ms.openlocfilehash: 60752cf1b3c05ab7817083e70310ba7b40227dec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80129963"
---
1. Klikněte na peering připojení, které chcete povolit pro Peering Service a pak klikněte na **...**  >  **Tlačítko Upravit připojení.**
    > [!div class="mx-imgBorder"]
    > ![Úprava připojení partnerského vztahu](../media/setup-direct-modify-editconnection.png)
1. V části ***Použít pro službu partnerského vztahu***klikněte na **Povoleno** a **Uložit**.
    > [!div class="mx-imgBorder"]
    > ![Služba partnerského vztahu umožňuje partnerský vztah](../media/setup-direct-modify-editconnectionsettings-peering-service.png)
1. Na obrazovce Přehled se zobrazí podrobnosti o nasazení. Po dokončení nasazení klikněte na **Přejít na prostředek**.
    > [!div class="mx-imgBorder"]
    > ![Vaše nasazení je dokončeno.](../media/setup-direct-modify-overview-deployment-complete.png)

1. Poté se zobrazí v části Nastavení **registrovaných předpon**. Klikněte na **Přidat registrovanou předponu**.
    > [!div class="mx-imgBorder"]
    > ![Registrované předpony a připojení](../media/setup-direct-modify-add-registered-prefix.png)
1. Registrace předpony výběrem **názvu** a **předpony** a **kliknutí** uložit
    > [!div class="mx-imgBorder"]
    >  ![Registrace předpony](../media/setup-direct-modify-register-a-prefix.png) 

1. Po vytvoření předpony se zobrazí v seznamu registrovaných předpon. Kliknutím na **název** předpony zobrazíte další podrobnosti.
    > [!div class="mx-imgBorder"]
    > ![Registrované předpony a připojení](../media/setup-direct-modify-registered-prefixes.png)
1. Na stránce registrované předpony se zobrazí úplné podrobnosti, které obsahují **klíč předpony** pro každou předponu. Tento klíč bude nutné poskytnout zákazníkovi přidělenou tuto předponu od poskytovatele služeb Internetu. Zákazník pak může zaregistrovat svou předponu v rámci svého předplatného s tímto klíčem.
    > [!div class="mx-imgBorder"]
    > ![Předpona s předponou](../media/setup-direct-modify-registered-prefix-detail.png)