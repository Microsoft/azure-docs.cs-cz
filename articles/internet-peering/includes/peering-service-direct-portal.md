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
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "81687051"
---
1. Vyberte připojení partnerského vztahu, které chcete povolit pro službu Azure peering Service. Pak vyberte **...**  >  **Upravit připojení**.
    > [!div class="mx-imgBorder"]
    > ![Připojení pro úpravu partnerského vztahu](../media/setup-direct-modify-editconnection.png)
1. V části **použít pro službu partnerského vztahu** vyberte **povoleno** a pak vyberte **Uložit**.
    > [!div class="mx-imgBorder"]
    > ![Připojení partnerského vztahu povolit službu partnerského vztahu](../media/setup-direct-modify-editconnectionsettings-peering-service.png)
1. Na obrazovce **Přehled** se zobrazí podrobnosti o nasazení. Po dokončení nasazení vyberte **Přejít k prostředku**.
    > [!div class="mx-imgBorder"]
    > ![Vaše nasazení je hotové.](../media/setup-direct-modify-overview-deployment-complete.png)

1. V podokně **registrované předpony** vyberte **Přidat registrovanou předponu**.
    > [!div class="mx-imgBorder"]
    > ![Přidat registrovanou předponu](../media/setup-direct-modify-add-registered-prefix.png)
1. Zaregistrujte předponu tak, že vyberete **název** a **předponu** a vyberete **Uložit**.
    > [!div class="mx-imgBorder"]
    >  ![Registrace předpony](../media/setup-direct-modify-register-a-prefix.png) 

1. Po vytvoření předpony se zobrazí v seznamu **registrovaných předpon**. Kliknutím na **název** předpony zobrazíte další podrobnosti.
    > [!div class="mx-imgBorder"]
    > ![Registrované předpony a připojení](../media/setup-direct-modify-registered-prefixes.png)
1. Na stránce registrovaná předpona se zobrazí úplné podrobnosti, které obsahují **klíč předpony** pro každou předponu. Tento klíč se musí poskytnout zákazníkovi, který tuto předponu přidělil poskytovateli poskytovatele internetových služeb. Zákazník pak může zaregistrovat svůj prefix v rámci svého předplatného pomocí tohoto klíče.
    > [!div class="mx-imgBorder"]
    > ![Předpona s klíčem předpony](../media/setup-direct-modify-registered-prefix-detail.png)