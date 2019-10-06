---
title: zahrnout soubor
description: zahrnout soubor
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 10/03/2019
ms.custom: include file
ms.openlocfilehash: 7e0396c032a9f3dc26b82648604624446d6ad191
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2019
ms.locfileid: "71978638"
---
## <a name="azure-active-directory-legacy-registration"></a>Registrace starší verze Azure Active Directory

1. V [Azure Portal](https://portal.azure.com)otevřete **Azure Active Directory** v levém podokně a otevřete podokno **vlastnosti** . Do dočasného souboru zkopírujte **ID adresáře**. Tuto hodnotu použijete ke konfiguraci ukázkové aplikace v další části.

    [ID adresáře služby Active Directory @no__t 1Azure](./media/digital-twins-permissions-legacy/aad-app-reg-tenant.png)](./media/digital-twins-permissions-legacy/aad-app-reg-tenant.png#lightbox)

1. V [Azure Portal](https://portal.azure.com)otevřete **Azure Active Directory** v levém podokně a otevřete podokno **Registrace aplikací (starší verze)** . Klikněte na tlačítko **Registrace nové aplikace** .

1. Zadejte popisný název pro registraci této aplikace v poli **název** . Vyberte **Typ aplikace** jako **nativní**a **identifikátor URI pro přesměrování** jako `https://microsoft.com`. Vyberte **Create** (Vytvořit).

    [@no__t – podokno 1Create](./media/digital-twins-permissions-legacy/aad-app-reg-create.png)](./media/digital-twins-permissions-legacy/aad-app-reg-create.png#lightbox)

1.  Otevřete registrovanou aplikaci a zkopírujte hodnotu pole **ID aplikace** do dočasného souboru. Tato hodnota identifikuje vaši aplikaci Azure Active Directory. Pomocí ID aplikace můžete nakonfigurovat ukázkovou aplikaci v následujících oddílech.

    [@no__t – ID aplikace služby Active Directory pro 1Azure](./media/digital-twins-permissions-legacy/aad-app-reg-app-id.png)](./media/digital-twins-permissions-legacy/aad-app-reg-app-id.png#lightbox)

1. Otevřete podokno registrace aplikace. Vyberte **nastavení** > **požadovaná oprávnění**a potom:

   a. V levém horním rohu vyberte **Přidat** a otevřete podokno **Přidat přístup k rozhraní API** .

   b. Vyberte **Vybrat rozhraní API** a vyhledejte **digitální vlákna Azure**. Pokud se vám toto rozhraní API nepodaří najít, vyhledejte místo toho **Azure Smart Spaces**.

   c. Vyberte možnost **digitální vlákna Azure (služba inteligentních prostorů Azure)** a zvolte **Vybrat**.

   d. Zvolte **možnost vybrat oprávnění**. Zaškrtněte políčko delegovaná oprávnění **přístup pro čtení a zápis** a zvolte **Vybrat**.

   e. Vyberte **Hotovo** v podokně **Přidat přístup přes rozhraní API** .

   f. V podokně **požadovaná oprávnění** vyberte tlačítko **udělit oprávnění** a přijměte potvrzení, které se zobrazí. Pokud oprávnění není pro toto rozhraní API uděleno, obraťte se na správce.

      [@no__t – podokno oprávnění 1Required](./media/digital-twins-permissions-legacy/aad-app-req-permissions.png)](./media/digital-twins-permissions-legacy/aad-app-req-permissions.png#lightbox)

 
