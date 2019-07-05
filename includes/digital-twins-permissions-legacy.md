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
ms.openlocfilehash: 9a5b3b04287a8b732d01bd8fe4610e073332da0d
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478963"
---
1. V [webu Azure portal](https://portal.azure.com), otevřete **Azure Active Directory** v levém podokně a pak otevřete **vlastnosti** podokně. Do dočasného souboru zkopírujte **ID adresáře**. Tuto hodnotu použijete ke konfiguraci a ukázkové aplikace v další části.

    ![ID adresáře Azure Active Directory](./media/digital-twins-permissions-legacy/aad-app-reg-tenant.png)

1. V [webu Azure portal](https://portal.azure.com), otevřete **Azure Active Directory** v levém podokně a pak otevřete **registrace aplikací (starší verze)** podokně. Vyberte **registrace nové aplikace** tlačítko.

1. Zadejte popisný název pro tuto registraci aplikace v **název** pole. Zvolte **typ aplikace** jako **nativní**, a **identifikátor URI pro přesměrování** jako `https://microsoft.com`. Vyberte **Vytvořit**.

    ![Vytvořit podokno](./media/digital-twins-permissions-legacy/aad-app-reg-create.png)

1.  Otevřete registrovaná aplikace a zkopírujte hodnotu **ID aplikace** pole do dočasného souboru. Tato hodnota identifikuje vaši aplikaci Azure Active Directory. ID aplikace použijete ke konfiguraci ukázkovou aplikaci v následujících částech.

    ![ID aplikace Azure Active Directory](./media/digital-twins-permissions-legacy/aad-app-reg-app-id.png)

1. Otevřete podokno registrace vaší aplikace. Vyberte **nastavení** > **požadovaná oprávnění**a pak:

   a. Vyberte **přidat** v levém horním rohu otevřete **přístup přes rozhraní API přidat** podokně.

   b. Vyberte **vyberte rozhraní API** a vyhledejte **digitální dvojče Azure**. Pokud se vám toto rozhraní API nepodaří najít, vyhledejte místo toho **Azure Smart Spaces**.

   c. Vyberte **digitální dvojče Azure (Azure Service pro inteligentní mezery)** možnost a vyberte **vyberte**.

   d. Zvolte **vyberte oprávnění**. Vyberte **přístup pro čtení a zápis** delegovaná oprávnění zaškrtněte políčko a zvolte **vyberte**.

   e. Vyberte **provádí** v **přístup přes rozhraní API přidat** podokně.

   f. V **požadovaná oprávnění** podokně, vyberte **udělit oprávnění** tlačítko a přijetí potvrzení, které se zobrazí. Pokud pro toto rozhraní API není uděleno oprávnění, obraťte se na správce.

      ![Podokně požadovaná oprávnění](./media/digital-twins-permissions-legacy/aad-app-req-permissions.png)

 