---
title: Konfigurace nástroje Postman pro volání REST API služby Azure Media Services
description: Zjistěte, jak konfigurace nástroje Postman pro volání REST API služby Media Services.
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
ms.date: 04/11/2019
ms.author: juliako
ms.openlocfilehash: a2171ff8a4354a59ec2f790f9bf38b7a687419ca
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2019
ms.locfileid: "59543872"
---
# <a name="configure-postman-for-media-services-rest-api-calls"></a>Konfigurace nástroje Postman pro volání REST API služby Media Services

V tomto článku se dozvíte, jak nakonfigurovat **Postman** takže ho můžete použít k volání rozhraní REST API služby Azure Media Services (AMS). Tento článek ukazuje, jak importovat prostředí a kolekce souborů do **Postman**. Kolekce obsahuje seskupené definice požadavků HTTP, které volání rozhraní REST API služby Azure Media Services (AMS). Prostředí obsahuje proměnné, které jsou používány v kolekci.

Než začnete s vývojem, projděte si [vývoj s využitím rozhraní API služby Media Services v3](media-services-apis-overview.md).

## <a name="prerequisites"></a>Požadavky

- [Vytvoření účtu Media Services](create-account-cli-how-to.md). Ujistěte se, že si pamatovat název skupiny prostředků a název účtu Media Services. 
- Získání informací potřebných k [přístup k rozhraním API](access-api-cli-how-to.md)
- Nainstalujte klienta [Postman](https://www.getpostman.com/) rozhraní REST, ve kterém můžete spouštět rozhraní REST API používaná v některých kurzech ke službě AMS REST. 

    V příkladech používáme **Postman**, můžete ale zvolit jakýkoli nástroj REST. Další možnosti jsou: **Visual Studio Code** pomocí modulu plug-in REST nebo **Telerik Fiddler**. 

## <a name="download-postman-files"></a>Stažení souborů nástroje Postman

Naklonujte úložiště GitHub, který obsahuje soubory kolekce a prostředí nástroje Postman.

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-rest-postman.git
 ```

## <a name="configure-postman"></a>Konfigurace nástroje Postman

Tato část popisuje konfiguraci nástroje Postman.

### <a name="configure-the-environment"></a>Konfigurace prostředí 

1. Spusťte nástroj **Postman**.
2. Na pravé straně obrazovky vyberte možnost **Manage environment** (Spravovat prostředí).

    ![Správa prostředí](./media/develop-with-postman/postman-import-env.png)
4. V dialogovém okně **Manage environment** (Spravovat prostředí) klikněte na **Import**.
2. Vyhledejte soubor `Azure Media Service v3 Environment.postman_environment.json`, který se stáhl při klonování úložiště `https://github.com/Azure-Samples/media-services-v3-rest-postman.git`.
6. Přidá se prostředí služby **Azure Media Service v3 Environment**.

    > [!Note]
    > Aktualizujte přístupové proměnné pomocí hodnot, které jste získali výše v části **Přístup k rozhraní API služby Media Services**.

7. Dvakrát klikněte na vybraný soubor a zadejte hodnoty, které jste získali podle pokynů přístup k rozhraní API.
8. Zavřete dialogové okno.
9. Z rozevíracího seznamu vyberte prostředí **Azure Media Service v3 Environment**.

    ![Výběr prostředí](./media/develop-with-postman/choose-env.png)
   
### <a name="configure-the-collection"></a>Konfigurace kolekce

1. Kliknutím na **Import** importujte soubor kolekce.
1. Vyhledejte soubor `Media Services v3.postman_collection.json`, který se stáhl při klonování úložiště `https://github.com/Azure-Samples/media-services-v3-rest-postman.git`.
3. Vyberte soubor **Media Services v3.postman_collection.json**.

    ![Importovat soubor](./media/develop-with-postman/postman-import-collection.png)

## <a name="get-azure-ad-token"></a>Získání tokenu služby Azure AD 

Než začnete manipulace s prostředky AMS v3 potřebujete k získání a nastavení tokenu Azure AD pro ověřování instančního objektu.

1. V levém okně Postman, vyberte "krok 1: Získání ověřování AAD tokenu".
2. Potom vyberte „Get Azure AD Token for Service Principal Authentication“ (Získat token služby Azure AD pro ověření instančního objektu).
3. Stiskněte **Odeslat**.

    Odešle se následující operace **POST**.

    ```
    https://login.microsoftonline.com/:tenantId/oauth2/token
    ```

4. Vrátí se odpověď s tokenem, která nastaví proměnnou prostředí „AccessToken“ na hodnotu tokenu.  

    ![Získání tokenu AAD](./media/develop-with-postman/postman-get-aad-auth-token.png)

## <a name="see-also"></a>Další informace najdete v tématech

- [Nahrání souborů do účtu Azure Media Services – REST](upload-files-rest-how-to.md)
- [Vytváření filtrů pomocí Media Services – REST](filters-dynamic-manifest-rest-howto.md)
- [Rozhraní REST API založené na Azure Resource Manageru](https://github.com/Azure-Samples/media-services-v3-arm-templates)

## <a name="next-steps"></a>Další postup

- [Stream souborů s využitím REST](stream-files-tutorial-with-rest.md).  
- [Kurz: Kódování vzdáleného souboru na základě adresy URL a streamování videa – REST](stream-files-tutorial-with-rest.md)
