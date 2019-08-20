---
title: zahrnout soubor
description: zahrnout soubor
services: digital-twins
author: alinamstanciu
ms.service: digital-twins
ms.topic: include
ms.date: 08/16/2019
ms.author: alinast
ms.custom: include file
ms.openlocfilehash: ef6b395aeff18a63f52f58e2477679b48a19b002
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2019
ms.locfileid: "69624746"
---
1. V [Azure Portal](https://portal.azure.com)otevřete **Azure Active Directory** v levém podokně a otevřete podokno **vlastnosti** . Do dočasného souboru zkopírujte **ID adresáře**. Tuto hodnotu použijete ke konfiguraci ukázkové aplikace v další části.

    ![ID adresáře Azure Active Directory](./media/digital-twins-permissions-legacy/aad-app-reg-tenant.png)

1. V [Azure Portal](https://portal.azure.com)otevřete **Azure Active Directory** v levém podokně a otevřete podokno **Registrace aplikací (starší verze)** . Klikněte na tlačítko **Registrace nové aplikace** .

1. Zadejte popisný název pro registraci této aplikace v poli **název** . Vyberte **Typ aplikace** jako **nativní**a **identifikátor URI** pro přesměrování `https://microsoft.com`jako. Vyberte **Vytvořit**.

    ![Vytvořit podokno](./media/digital-twins-permissions-legacy/aad-app-reg-create.png)

1.  Otevřete registrovanou aplikaci a zkopírujte hodnotu pole **ID aplikace** do dočasného souboru. Tato hodnota identifikuje vaši aplikaci Azure Active Directory. Pomocí ID aplikace můžete nakonfigurovat ukázkovou aplikaci v následujících oddílech.

    ![ID aplikace Azure Active Directory](./media/digital-twins-permissions-legacy/aad-app-reg-app-id.png)

1. Otevřete podokno registrace aplikace. Vyberte **Nastavení** > **požadovaná oprávnění**a potom:

   a. V levém horním rohu vyberte **Přidat** a otevřete podokno **Přidat přístup k rozhraní API** .

   b. Vyberte **Vybrat rozhraní API** a vyhledejte **digitální vlákna Azure**. Pokud se vám toto rozhraní API nepodaří najít, vyhledejte místo toho **Azure Smart Spaces**.

   c. Vyberte možnost **digitální vlákna Azure (služba inteligentních prostorů Azure)** a zvolte **Vybrat**.

   d. Zvolte **možnost vybrat oprávnění**. Zaškrtněte políčko delegovaná oprávnění **přístup pro čtení a zápis** a zvolte **Vybrat**.

   e. Vyberte **Hotovo** v podokně **Přidat přístup přes rozhraní API** .

   f. V podokně **požadovaná oprávnění** vyberte tlačítko **udělit oprávnění** a přijměte potvrzení, které se zobrazí. Pokud oprávnění není pro toto rozhraní API uděleno, obraťte se na správce.

      ![Podokno požadovaná oprávnění](./media/digital-twins-permissions-legacy/aad-app-req-permissions.png)

 
