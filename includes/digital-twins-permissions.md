---
title: zahrnout soubor
description: zahrnout soubor
services: digital-twins
author: alinamstanciu
ms.service: digital-twins
ms.topic: include
ms.date: 06/26/2019
ms.author: alinast
ms.custom: include file
ms.openlocfilehash: f03ee57867185eac946be5b596477bf942643587
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67459195"
---
>[!NOTE]
>Mějte na paměti, že starší verze registraci aplikace AAD, který se používá zde bude brzy ukončena. V této části budou aktualizovány, jakmile digitální dvojče Azure jsou plně integrované s [registrace nové aplikace AAD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app). Mezitím můžete vyzkoušet nové registrace AAD aplikace. Všimněte si, že budete muset použít [veřejným klientem (mobilní aplikace a destkopu)](https://docs.microsoft.com/azure/active-directory/develop/v2-app-types#mobile-and-native-apps) pro *identifikátor URI pro přesměrování*. 

1. V [webu Azure portal](https://portal.azure.com), otevřete **Azure Active Directory** v levém podokně a pak otevřete **vlastnosti** podokně. Do dočasného souboru zkopírujte **ID adresáře**. Tuto hodnotu použijete ke konfiguraci a ukázkové aplikace v další části.

    ![ID adresáře Azure Active Directory](./media/digital-twins-permissions/aad-app-reg-tenant.png)

1. V [webu Azure portal](https://portal.azure.com), otevřete **Azure Active Directory** v levém podokně a pak otevřete **registrace aplikací (starší verze)** podokně. Vyberte **registrace nové aplikace** tlačítko.

1. Zadejte popisný název pro tuto registraci aplikace v **název** pole. Zvolte **typ aplikace** jako **nativní**, a **identifikátor URI pro přesměrování** jako `https://microsoft.com`. Vyberte **Vytvořit**.

    ![Vytvořit podokno](./media/digital-twins-permissions/aad-app-reg-create.png)

1.  Otevřete registrovaná aplikace a zkopírujte hodnotu **ID aplikace** pole do dočasného souboru. Tato hodnota identifikuje vaši aplikaci Azure Active Directory. ID aplikace použijete ke konfiguraci ukázkovou aplikaci v následujících částech.

    ![ID aplikace Azure Active Directory](./media/digital-twins-permissions/aad-app-reg-app-id.png)

1. Otevřete podokno registrace vaší aplikace. Vyberte **nastavení** > **požadovaná oprávnění**a pak:

   a. Vyberte **přidat** v levém horním rohu otevřete **přístup přes rozhraní API přidat** podokně.

   b. Vyberte **vyberte rozhraní API** a vyhledejte **digitální dvojče Azure**. Pokud se vám toto rozhraní API nepodaří najít, vyhledejte místo toho **Azure Smart Spaces**.

   c. Vyberte **digitální dvojče Azure (Azure Service pro inteligentní mezery)** možnost a vyberte **vyberte**.

   d. Zvolte **vyberte oprávnění**. Vyberte **přístup pro čtení a zápis** delegovaná oprávnění zaškrtněte políčko a zvolte **vyberte**.

   e. Vyberte **provádí** v **přístup přes rozhraní API přidat** podokně.

   f. V **požadovaná oprávnění** podokně, vyberte **udělit oprávnění** tlačítko a přijetí potvrzení, které se zobrazí. Pokud pro toto rozhraní API není uděleno oprávnění, obraťte se na správce.

      ![Podokně požadovaná oprávnění](./media/digital-twins-permissions/aad-app-req-permissions.png)

 