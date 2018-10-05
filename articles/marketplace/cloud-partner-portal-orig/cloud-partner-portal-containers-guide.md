---
title: Kontejnery | Dokumentace Microsoftu
description: Kroky k publikování image kontejneru.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 5653e4b2ec9aa5e21a107611b9d4142168630d4a
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2018
ms.locfileid: "48809353"
---
<a name="publishing-a-container-image-in-the-cloud-partner-portal"></a>Publikování Image kontejneru na portál Cloud Partner
========================================================

Tento článek popisuje, jak publikovat novou image kontejneru v portál partnerů cloudu.

Pomocí následujících kroků publikovat novou image kontejneru.

1. Vyberte **nová nabídka** a pak vyberte **kontejnery**

    ![Vyberte možnost kontejnery pro nové nabídky.](media/cpp-containers-guide/azure-container-offer.png)

2. Na kartě nabízejí nastavení, v rámci nabídky Identity, zadejte **ID nabídky**, **ID vydavatele**, a **název**.

    ![Nabídka Identity](media/cpp-containers-guide/containers-offer-settings.png)

3. Na kartě skladové položky vyberte **novou skladovou Položku**.
    >[!NOTE]
    >Můžete přidat více než jedna skladová položka.

    ![Nové skladové položky řádku](media/cpp-containers-guide/containers-sku-settings.png)

4. Zadejte informace o SKU a kontejner. Každý SKU odpovídá image kontejneru. Existují dvě části do skladové položky:

    -   Skladová položka metadat
    -   Metadata kontejneru

    Skladová položka metadata obsahují informace o zobrazení pro Image kontejneru.

    ![Skladová položka metadat](media/cpp-containers-guide/containers-sku-details.png)

    Metadata kontejneru má referenční informace podrobnosti úložiště vaší image v Azure container registry (ACR). Azure Marketplace pak zkopíruje tuto image do registru veřejného marketplace a je k dispozici pro zákazníky po certifikaci. Všechny žádosti od uživatele Azure využívat image kontejneru se obsluhují z registru kontejneru webu Marketplace.

   ![Metadata kontejneru](media/cpp-containers-guide/containers-image-repository.png)

    Podrobnosti o Imagi úložiště na předchozím snímku obrazovky obsahuje následující pole:

    -   **ID předplatného** – id předplatného Azure, kde je k dispozici registru ACR.
    -   **Název skupiny prostředků** – název skupiny prostředků z registru ACR.
    -   **Název registru** – název registru The ACR.
    -   **Název úložiště** – název úložiště. Po nastavení, tato hodnota není možné později změnit. To je třeba uvést jedinečný název tak, aby žádné jiné nabídky v rámci vašeho účtu má stejný název.
    -   **Uživatelské jméno** – uživatelské jméno přidružené k ACR (uživatelské jméno správce).
    -   **Heslo** – heslo přidružené k služby ACR.

    >[!NOTE]
    >Uživatelské jméno a heslo jsou nezbytné k zajištění, že partneři mají přístup do služby ACR uvedených v procesu publikování.

    Při publikování image kontejneru, můžete také zadat jednu nebo více značek bitové kopie. Kromě značka obrázku partneři mohou také poskytnout SHA přehledu. Nezapomeňte si přidat **testovací značka** do bitové kopie, aby mohli identifikovat bitovou kopii při testování.

5. Na kartě Marketplace přidáte vlastní obsah specifické pro marketing.

    ![Informace z Marketplace](media/cpp-containers-guide/containers-marketplace-tab.png)

6. Na kartě podporu zadejte informace podpoře a technickým kontaktem.

   ![Informace pro získání podpory](media/cpp-containers-guide/containers-support-tab.png)

7. Vyberte **publikovat** publikování nabídky. Vyberete-li publikovat, zobrazí se vám časovou osu, která ukazuje kroky zahrnuté pro publikování image kontejneru.
