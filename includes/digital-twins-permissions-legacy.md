---
title: zahrnout soubor
description: zahrnout soubor
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 08/16/2019
ms.custom: include file
ms.openlocfilehash: 690ff402c632bfc28f1a9a218677e9772f5a3200
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2019
ms.locfileid: "71949950"
---
1. V [Azure Portal](https://portal.azure.com)otevřete **Azure Active Directory** v levém podokně a otevřete podokno **vlastnosti** . Zkopírujte **ID adresáře** do dočasného souboru. Tuto hodnotu použijete ke konfiguraci ukázkové aplikace v další části.

    [ID adresáře služby Active Directory @no__t 1Azure](./media/digital-twins-permissions-legacy/aad-app-reg-tenant.png)](./media/digital-twins-permissions-legacy/aad-app-reg-tenant.png#lightbox)

1. V [Azure Portal](https://portal.azure.com)otevřete **Azure Active Directory** v levém podokně a otevřete podokno **Registrace aplikací (starší verze)** . Klikněte na tlačítko **Registrace nové aplikace** .

1. Zadejte popisný název pro registraci této aplikace v poli **název** . Vyberte **Typ aplikace** jako **nativní**a **identifikátor URI pro přesměrování** jako `https://microsoft.com`. Vyberte **vytvořit**.

    [@no__t – podokno 1Create](./media/digital-twins-permissions-legacy/aad-app-reg-create.png)](./media/digital-twins-permissions-legacy/aad-app-reg-create.png#lightbox)

1.  Otevřete registrovanou aplikaci a zkopírujte hodnotu pole **ID aplikace** do dočasného souboru. Tato hodnota identifikuje vaši aplikaci Azure Active Directory. Pomocí ID aplikace můžete nakonfigurovat ukázkovou aplikaci v následujících oddílech.

    [@no__t – ID aplikace služby Active Directory pro 1Azure](./media/digital-twins-permissions-legacy/aad-app-reg-app-id.png)](./media/digital-twins-permissions-legacy/aad-app-reg-app-id.png#lightbox)

1. Otevřete podokno registrace aplikace. Vyberte **nastavení** > **požadovaná oprávnění**a potom:

   a. V levém horním rohu vyberte **Přidat** a otevřete podokno **Přidat přístup k rozhraní API** .

   b. Vyberte **Vybrat rozhraní API** a vyhledejte **digitální vlákna Azure**. Pokud vaše hledání nenajde rozhraní API, místo toho vyhledejte **inteligentní prostory Azure** .

   r. Vyberte možnost **digitální vlákna Azure (služba inteligentních prostorů Azure)** a zvolte **Vybrat**.

   trojrozměrné. Zvolte **možnost vybrat oprávnění**. Zaškrtněte políčko delegovaná oprávnění **přístup pro čtení a zápis** a zvolte **Vybrat**.

   cerebrální. Vyberte **Hotovo** v podokně **Přidat přístup přes rozhraní API** .

   FJ. V podokně **požadovaná oprávnění** vyberte tlačítko **udělit oprávnění** a přijměte potvrzení, které se zobrazí. Pokud oprávnění není pro toto rozhraní API uděleno, obraťte se na správce.

      [@no__t – podokno oprávnění 1Required](./media/digital-twins-permissions-legacy/aad-app-req-permissions.png)](./media/digital-twins-permissions-legacy/aad-app-req-permissions.png#lightbox)

 
