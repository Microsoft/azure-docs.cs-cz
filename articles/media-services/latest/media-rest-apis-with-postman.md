---
title: Konfigurace postman pro Azure Media Services v3 VOLÁNÍ ROZHRANÍ REST API
description: Tento článek ukazuje, jak nakonfigurovat Postman tak, aby jej lze použít k volání Azure Media Services (AMS) REST API.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2019
ms.author: juliako
ms.openlocfilehash: 872dad95fc5b536c51e251612f40439da020a059
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75779633"
---
# <a name="configure-postman-for-media-services-v3-rest-api-calls"></a>Konfigurace pošťáka pro volání rozhraní REST V3 v3

Tento článek ukazuje, jak nakonfigurovat **Postman** tak, aby jej lze použít k volání Azure Media Services (AMS) REST API. Článek ukazuje, jak importovat prostředí a soubory kolekce do **Postman**. Kolekce obsahuje seskupené definice požadavků HTTP, které volají Azure Media Services (AMS) REST API. Soubor prostředí obsahuje proměnné, které kolekce používá.

Než začnete vyvíjet, přečtěte [si přehled Vývoj pomocí mediálních služeb v3 API](media-services-apis-overview.md).

## <a name="prerequisites"></a>Požadavky

- [Vytvořte účet mediálních služeb](create-account-cli-how-to.md). Nezapomeňte si zapamatovat název skupiny prostředků a název účtu Mediální služby. 
- Získání informací [potřebných](access-api-cli-how-to.md) pro přístup k apim
- Nainstalujte klienta [Postman](https://www.getpostman.com/) rozhraní REST, ve kterém můžete spouštět rozhraní REST API používaná v některých kurzech ke službě AMS REST. 

    V příkladech používáme **Postman**, můžete ale zvolit jakýkoli nástroj REST. Další alternativy jsou: **Visual Studio Code** s pluginem REST nebo **Telerik Fiddler**. 

> [!IMPORTANT]
> Zkontrolujte [konvence pojmenování](media-services-apis-overview.md#naming-conventions).

## <a name="download-postman-files"></a>Stažení souborů nástroje Postman

Naklonujte úložiště GitHub, který obsahuje soubory kolekce a prostředí nástroje Postman.

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-rest-postman.git
 ```

## <a name="configure-postman"></a>Konfigurace nástroje Postman

### <a name="configure-the-environment"></a>Konfigurace prostředí 

1. Otevřete aplikaci **Pošťák.**
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

Než začnete manipulovat s prostředky AMS v3, musíte získat a nastavit token Azure AD pro ověřování jistiny služby.

1. V levém okně aplikace Postman vyberte "Krok 1: Získat token auth AAD".
2. Potom vyberte „Get Azure AD Token for Service Principal Authentication“ (Získat token služby Azure AD pro ověření instančního objektu).
3. Stiskněte **Odeslat**.

    Odešle se následující operace **POST**.

    ```
    https://login.microsoftonline.com/:tenantId/oauth2/token
    ```

4. Vrátí se odpověď s tokenem, která nastaví proměnnou prostředí „AccessToken“ na hodnotu tokenu.  

    ![Získání tokenu AAD](./media/develop-with-postman/postman-get-aad-auth-token.png)

## <a name="troubleshooting"></a>Řešení potíží 

* Pokud se vaše aplikace nezdaří s "HTTP 504: Gateway Timeout", ujistěte se, že proměnná umístění nebyla explicitně nastavena na jinou hodnotu než očekávané umístění účtu Media Services. 
* Pokud se zobrazí chyba "účet nebyl nalezen", zkontrolujte také, zda je vlastnost umístění ve zprávě Body JSON nastavena na umístění, ve které se nachází účet Mediálních služeb. 

## <a name="see-also"></a>Viz také

- [Nahrání souborů do účtu Mediálních služeb – REST](upload-files-rest-how-to.md)
- [Vytváření filtrů pomocí mediálních služeb – REST](filters-dynamic-manifest-rest-howto.md)
- [Rozhraní REST API založené na Azure Resource Manageru](https://github.com/Azure-Samples/media-services-v3-arm-templates)

## <a name="next-steps"></a>Další kroky

- [Streamujte soubory pomocí rest](stream-files-tutorial-with-rest.md).  
- [Kurz: Zakódujte vzdálený soubor na základě adresy URL a streamujte video - REST](stream-files-tutorial-with-rest.md)
