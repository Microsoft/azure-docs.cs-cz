---
title: Konfigurace metody post pro Azure Media Services V3 REST API volání
description: V tomto článku se dozvíte, jak nakonfigurovat metodu post, aby ji bylo možné použít k volání rozhraní REST API pro Azure Media Services (AMS).
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: aa10cb207011051c30866a3a3ed693e7113b5882
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89297700"
---
# <a name="configure-postman-for-media-services-v3-rest-api-calls"></a>Konfigurace metody post pro Media Services V3 REST API volání

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

V tomto článku se dozvíte, jak nakonfigurovat **metodu post** , aby ji bylo možné použít k volání rozhraní REST api pro Azure Media Services (AMS). V tomto článku se dozvíte, jak importovat soubory prostředí a kolekcí do nástroje **post**. Kolekce obsahuje seskupené definice požadavků HTTP, které volají Azure Media Services (AMS) REST API. Soubor prostředí obsahuje proměnné, které kolekce používá.

Než začnete s vývojem, přečtěte si téma [vývoj s rozhraními api Media Services V3](media-services-apis-overview.md).

## <a name="prerequisites"></a>Požadavky

- [Vytvořte účet Media Services](./create-account-howto.md). Nezapomeňte si pamatovat název skupiny prostředků a název účtu Media Services. 
- Získání informací potřebných pro [přístup k rozhraním API](./access-api-howto.md)
- Nainstalujte klienta [Postman](https://www.getpostman.com/) rozhraní REST, ve kterém můžete spouštět rozhraní REST API používaná v některých kurzech ke službě AMS REST. 

    V příkladech používáme **Postman**, můžete ale zvolit jakýkoli nástroj REST. Další alternativy: **Visual Studio Code** pomocí modulu plug-in REST nebo **Telerik Fiddler**. 

> [!IMPORTANT]
> Přečtěte si [zásady vytváření názvů](media-services-apis-overview.md#naming-conventions).

## <a name="download-postman-files"></a>Stažení souborů nástroje Postman

Naklonujte úložiště GitHub, který obsahuje soubory kolekce a prostředí nástroje Postman.

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-rest-postman.git
 ```

## <a name="configure-postman"></a>Konfigurace nástroje Postman

### <a name="configure-the-environment"></a>Konfigurace prostředí 

1. Otevřete aplikaci pro **vyúčtování** .
2. Na pravé straně obrazovky vyberte možnost **Manage environment** (Spravovat prostředí).

    ![Správa prostředí](./media/develop-with-postman/postman-import-env.png)
4. V dialogovém okně **Manage environment** (Spravovat prostředí) klikněte na **Import**.
2. Vyhledejte soubor `Azure Media Service v3 Environment.postman_environment.json`, který se stáhl při klonování úložiště `https://github.com/Azure-Samples/media-services-v3-rest-postman.git`.
6. Přidá se prostředí služby **Azure Media Service v3 Environment**.

    > [!Note]
    > Aktualizujte přístupové proměnné pomocí hodnot, které jste získali výše v části **Přístup k rozhraní API služby Media Services**.

7. Poklikejte na vybraný soubor a zadejte hodnoty, které jste získali v postupu pro přístup k rozhraní API.
8. Zavřete dialogové okno.
9. Z rozevíracího seznamu vyberte prostředí **Azure Media Service v3 Environment**.

    ![Výběr prostředí](./media/develop-with-postman/choose-env.png)
   
### <a name="configure-the-collection"></a>Konfigurace kolekce

1. Kliknutím na **Import** importujte soubor kolekce.
1. Vyhledejte soubor `Media Services v3.postman_collection.json`, který se stáhl při klonování úložiště `https://github.com/Azure-Samples/media-services-v3-rest-postman.git`.
3. Vyberte soubor **Media Services v3.postman_collection.json**.

    ![Importovat soubor](./media/develop-with-postman/postman-import-collection.png)

## <a name="get-azure-ad-token"></a>Získání tokenu služby Azure AD 

Než začnete pracovat s prostředky AMS v3, potřebujete získat a nastavit token Azure AD pro ověřování instančního objektu.

1. V levém okně aplikace po výběru vyberte "krok 1: získání ověřovacího tokenu AAD".
2. Potom vyberte „Get Azure AD Token for Service Principal Authentication“ (Získat token služby Azure AD pro ověření instančního objektu).
3. Stiskněte **Odeslat**.

    Odešle se následující operace **POST**.

    ```
    https://login.microsoftonline.com/:tenantId/oauth2/token
    ```

4. Vrátí se odpověď s tokenem, která nastaví proměnnou prostředí „AccessToken“ na hodnotu tokenu.  

    ![Získání tokenu AAD](./media/develop-with-postman/postman-get-aad-auth-token.png)

## <a name="troubleshooting"></a>Řešení potíží 

* Pokud se vaše aplikace nezdařila s protokolem HTTP 504: časový limit brány, ujistěte se, že proměnná umístění nebyla explicitně nastavena na jinou hodnotu než očekávané umístění účtu Media Services. 
* Pokud se zobrazí chyba "účet nebyl nalezen", zkontrolujte také, zda je vlastnost umístění v těle zprávy JSON nastavena na umístění, ve kterém je účet Media Services. 

## <a name="see-also"></a>Viz také

- [Vytváření filtrů pomocí Media Services – REST](filters-dynamic-manifest-rest-howto.md)
- [Rozhraní REST API založené na Azure Resource Manageru](https://github.com/Azure-Samples/media-services-v3-arm-templates)

## <a name="next-steps"></a>Další kroky

- [Streamování souborů s REST](stream-files-tutorial-with-rest.md)  
- [Kurz: Kódování vzdáleného souboru na základě adresy URL a streamování videa – REST](stream-files-tutorial-with-rest.md)
