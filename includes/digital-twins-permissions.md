---
title: zahrnout soubor
description: zahrnout soubor
services: digital-twins
author: alinamstanciu
ms.service: digital-twins
ms.topic: include
ms.date: 09/19/2018
ms.author: alinast
ms.custom: include file
ms.openlocfilehash: 1887efd741f4779a5186707d60b27ca66fc3c06f
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2018
ms.locfileid: "51283971"
---
1. Z levého navigačního panelu na webu [Azure Portal](https://portal.azure.com) otevřete **Azure Active Directory** a pak otevřete podokno **Vlastnosti**. Do dočasného souboru zkopírujte **ID adresáře**. Tuto hodnotu použijete při konfiguraci ukázkové aplikace v následující části.

    ![Azure Active Directory – ID adresáře](./media/digital-twins-permissions/aad-app-reg-tenant.png)

1. Otevřete podokno **Registrace aplikací** a pak klikněte na tlačítko **Registrace nové aplikace**.
    
    ![Azure Active Directory – Registrace nové aplikace](./media/digital-twins-permissions/aad-app-reg-start.png)

1. Do pole **Název** zadejte popisný název této registrace aplikace. Jako **Typ aplikace** zvolte **_Nativní_** a jako **Identifikátor URI pro přesměrování** zvolte **_https://microsoft.com_**. Klikněte na možnost **Vytvořit**.

    ![Azure Active Directory – Vytvoření registrace aplikace](./media/digital-twins-permissions/aad-app-reg-create.png)

1. Otevřete zaregistrovanou aplikaci a zkopírujte hodnotu pole **ID aplikace** do dočasného souboru. Tato hodnota identifikuje vaši aplikaci Azure Active Directory. ID aplikace použijete při konfiguraci ukázkové aplikace v následujících částech.

    ![Azure Active Directory – ID aplikace](./media/digital-twins-permissions/aad-app-reg-app-id.png)

1. Otevřete podokno registrace vaší aplikace a klikněte na **Nastavení** > **Požadovaná oprávnění**:
    - Kliknutím na **Přidat** v levém horním rohu otevřete podokno **Přidat přístup přes rozhraní API**.
    - Klikněte na **Výběr rozhraní API** a vyhledejte **Azure Digital Twins**. Pokud se vám toto rozhraní API nepodaří najít, vyhledejte místo toho **Azure Smart Spaces**.
    - Vyberte možnost **Azure Digital Twins (služba Azure Smart Spaces)** a klikněte na **Vybrat**.
    - Klikněte na **Vybrat oprávnění**. Zaškrtněte políčko u delegovaného oprávnění **Přístup pro čtení i zápis** a klikněte na **Vybrat**.
    - V podokně **Přidat přístup přes rozhraní API** klikněte na **Hotovo**.
    - V podokně **Požadovaná oprávnění** klikněte na tlačítko **Udělit oprávnění** a přijměte potvrzení, které se zobrazí.

       ![Azure Active Directory – Přidání rozhraní API do registrace aplikace](./media/digital-twins-permissions/aad-app-req-permissions.png)
