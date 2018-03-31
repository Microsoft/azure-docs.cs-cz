---
title: 'Ověření pomocí rozhraní REST API Mobile Engagement: ruční instalace'
description: Popisuje, jak ručně nastavit ověřování rozhraní REST API Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 2e79f9c9-41e4-45ac-b427-3b8338675163
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 0d71908b34ddf8313aa45014420c9e63a00078c9
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="authenticate-with-mobile-engagement-rest-apis---manual-setup"></a>Ověření pomocí Mobile Engagement REST API – ruční instalaci
> [!IMPORTANT]
> Na 3/31/2018 IT vyřadí Azure Mobile Engagement. Tato stránka bude odstraněna krátce po.
> 

Tato dokumentace je dodatek k [ověřit pomocí rozhraní API REST Mobile Engagement](mobile-engagement-api-authentication.md). Ujistěte se, přečtěte si tento článek nejprve vám pomůže porozumět kontextu. Popisuje také jiný způsob, jak to jednorázové ověření instalace pro rozhraní API REST Mobile Engagement pomocí portálu Azure.

> [!NOTE]
> Následující pokyny jsou založené na [této příručce služby Active Directory](../azure-resource-manager/resource-group-create-service-principal-portal.md). Že jsou přizpůsobené požadavky na ověřování pro zapojení mobilních rozhraní API. Na ně odkazují, pokud chcete pochopit následující kroky podrobně.

1. Přihlaste se k účtu Azure prostřednictvím [portál Azure](https://portal.azure.com/).
2. Vyberte **služby Active Directory** v levém podokně.

   ![Vyberte možnost Active Directory][1]

3. Chcete-li zobrazit aplikace ve vašem adresáři, vyberte **registrace aplikace**.

   ![Zobrazit aplikace][3]

4. Vyberte **nové registrace aplikace**.

   ![Přidání aplikace][4]

5. Zadejte název aplikace. Ponechat typ aplikace jako **webové aplikace nebo rozhraní API**a pak vyberte **Další** tlačítko. Můžete zadat všechny fiktivní adresy URL pro **adresa URL přihlašování**. Nejsou použity pro tento scénář a adresy URL sami nejsou ověřené.

   Po dokončení, máte aplikaci Azure Active Directory (Azure AD) s názvem, který jste zadali. Je vaše **AD\_aplikace\_název**, takže je nutné poznamenejte si ho.

   ![Název aplikace][8]

7. Vyberte název aplikace.

8. Najít **ID aplikace** a poznamenejte si ho. Je ID klienta, který se použije jako **klienta\_ID** pro vaše rozhraní API volání.

   ![Najít ID aplikace][10]

9. Najít **klíče** části na pravé straně.

   ![Část klíče][11]

10. Vytvořte nový klíč a potom ho zkopírujete okamžitě. Znovu se nezobrazí.

    ![Podokno klíče s podrobnosti klíče][12]

    > [!IMPORTANT]
    > Tento klíč vyprší na konci dobu, která jste zadali. Ujistěte se, že ho obnovit, když nastane čas, jinak hodnota rozhraní API ověřování fungovat. Pokud se domníváte, že tento klíč je ohrožené zabezpečení, můžete odstranit a znovu vytvořit.
    >
    
11. Vyberte **koncové body** tlačítka v horní části stránky. Zkopírujte **koncový bod TOKENU OAUTH 2.0**.

    ![Zkopírujte koncový bod][14]

16. Tento koncový bod je v následující podobě, kde je identifikátor GUID v adrese URL vaší **TENANT_ID**: `https://login.microsoftonline.com/<GUID>/oauth2/token`

17. Dále je nutné nakonfigurovat oprávnění v této aplikaci. Chcete-li spustit proces, přejděte na [portál Azure](https://portal.azure.com).

18. Vyberte **skupiny prostředků**a pak vyhledejte **MobileEngagement** skupinu prostředků.

    ![Najít MobileEngagement][15]

19. Vyberte **MobileEngagement** prostředku, skupiny a pak vyberte **všechna nastavení**.

    ![Přejděte do nastavení pro MobileEngagement][16]

20. Vyberte **uživatelé** v **nastavení** části. Pak přidat uživatele, vyberte **přidat**.

    ![Přidání uživatele][17]

21. Klikněte na tlačítko **vyberte roli**.

    ![Výběr role][18]

22. Vyberte **vlastníka**.

    ![Vyberte vlastníka jako roli][19]

23. Vyhledávání pro název vaší aplikace **AD\_aplikace\_název**, do vyhledávacího pole. Tento název není ve výchozím nastavení v tomto poli. Když zjistíte, je, vyberte ho. Pak klikněte na tlačítko **vyberte** v dolní části.

    ![Vyberte název][20]

24. V **přidat přístup** části, se jeví jako **1 uživatel, 0 skupiny**. Pro potvrzení změny, vyberte **OK**.

    ![Potvrďte uživatel přidaný][21]

Teď jste dokončili požadované konfigurace služby Azure AD a jsou nastavena na volání rozhraní API.

<!-- Images -->
[1]: ./media/mobile-engagement-api-authentication-manual/active-directory.png
[2]: ./media/mobile-engagement-api-authentication-manual/active-directory-details.png
[3]: ./media/mobile-engagement-api-authentication-manual/view-applications.png
[4]: ./media/mobile-engagement-api-authentication-manual/add-icon.png
[5]: ./media/mobile-engagement-api-authentication-manual/what-do-you-want-to-do.png
[6]: ./media/mobile-engagement-api-authentication-manual/tell-us-about-your-application.png
[7]: ./media/mobile-engagement-api-authentication-manual/app-properties.png
[8]: ./media/mobile-engagement-api-authentication-manual/aad-app.png
[9]: ./media/mobile-engagement-api-authentication-manual/configure-menu.png
[10]: ./media/mobile-engagement-api-authentication-manual/client-id.png
[11]: ./media/mobile-engagement-api-authentication-manual/client-secret.png
[12]: ./media/mobile-engagement-api-authentication-manual/keys.png
[13]: ./media/mobile-engagement-api-authentication-manual/view-endpoints.png
[14]: ./media/mobile-engagement-api-authentication-manual/app-endpoints.png
[15]: ./media/mobile-engagement-api-authentication-manual/resource-groups.png
[16]: ./media/mobile-engagement-api-authentication-manual/resource-groups-settings.png
[17]: ./media/mobile-engagement-api-authentication-manual/add-users.png
[18]: ./media/mobile-engagement-api-authentication-manual/add-role.png
[19]: ./media/mobile-engagement-api-authentication-manual/select-role.png
[20]: ./media/mobile-engagement-api-authentication-manual/add-user-select.png
[21]: ./media/mobile-engagement-api-authentication-manual/add-access-final.png



