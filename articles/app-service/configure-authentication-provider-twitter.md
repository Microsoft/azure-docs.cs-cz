---
title: Konfigurace ověřování na Twitteru – Azure App Service
description: Přečtěte si, jak nakonfigurovat ověřování na Twitteru pro vaši aplikaci App Services.
services: app-service
documentationcenter: ''
author: mattchenderson
manager: syntaxc4
editor: ''
ms.assetid: c6dc91d7-30f6-448c-9f2d-8e91104cde73
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/19/2018
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: 728eacdcb0ee0d0bee878ff4764b1ca5e430c59c
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70088200"
---
# <a name="how-to-configure-your-app-service-application-to-use-twitter-login"></a>Postup konfigurace aplikace App Service pro použití služby Twitter Login
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

V tomto tématu se dozvíte, jak nakonfigurovat Azure App Service pro použití služby Twitter jako poskytovatele ověřování.

Chcete-li dokončit postup v tomto tématu, musíte mít účet na Twitteru s ověřenou e-mailovou adresou a telefonním číslem. Pokud chcete vytvořit nový účet na Twitteru, navštivte <a href="https://go.microsoft.com/fwlink/p/?LinkID=268287" target="_blank">Twitter.com</a>.

## <a name="register"> </a>Registrace aplikace na Twitteru
1. Přihlaste se k [Azure Portal]a přejděte do aplikace. Zkopírujte **adresu URL**. Použijete ho ke konfiguraci aplikace Twitter.
2. Přejděte na web pro [Vývojáři na Twitteru] na Twitteru, přihlaste se pomocí přihlašovacích údajů k účtu Twitteru a klikněte na **vytvořit novou aplikaci**.
3. Zadejte **název** a **Popis** nové aplikace. Do **adresy URL** vaší aplikace vložte hodnotu **Web** . Potom pro **adresu URL zpětného volání**vložte **adresu URL zpětného volání** , kterou jste zkopírovali dříve. Toto je vaše mobilní aplikace, která je připojená s cestou, */.auth/Login/Twitter/callback*. Například, `https://contoso.azurewebsites.net/.auth/login/twitter/callback`. Ujistěte se, že používáte schéma HTTPS.
4. V dolní části stránky si přečtěte a přijměte podmínky. Pak klikněte na **vytvořit aplikaci Twitter**. Tato registrace aplikace zobrazí podrobnosti o aplikaci.
5. Klikněte na kartu **Nastavení** , zaškrtněte políčko **povolí použití této aplikace pro přihlášení**k Twitteru a pak klikněte na **aktualizovat nastavení**.
6. Vyberte kartu **klíče a přístupové tokeny** . Poznamenejte si hodnoty **klíč příjemce (klíč rozhraní API)** a tajného kódu **příjemce (tajný klíč rozhraní API)** .
   
   > [!NOTE]
   > Tajný klíč příjemce je důležité bezpečnostní pověření. Nesdílejte tento tajný klíč s kýmkoli ani ho distribuujte s vaší aplikací.
   > 
   > 

## <a name="secrets"> </a>Přidání informací o Twitteru do aplikace
1. Zpátky v [Azure Portal]přejděte do aplikace. Klikněte na **Nastavení**a pak na **ověřování nebo autorizaci**.
2. Pokud není povolená funkce ověřování/autorizace, zapněte přepínač na **zapnuto**.
3. Klikněte na **Twitter**. Vložte ID aplikace a hodnoty tajného klíče aplikace, které jste získali dříve. Pak klikněte na **OK**.
   
   ![][1]
   
   Ve výchozím nastavení App Service poskytuje ověřování, ale neomezuje autorizovaný přístup k obsahu a rozhraním API vašeho webu. Musíte autorizovat uživatele v kódu vaší aplikace.
4. Volitelné Pokud chcete omezit přístup k vašemu webu jenom na uživatele ověřené Twitterem, nastavte **akci, která se má provést, když se žádost neověřuje** na **Twitteru**. To vyžaduje, aby všechny požadavky byly ověřené a všechny neověřené požadavky se přesměrovaly na Twitter pro ověřování.

> [!CAUTION]
> Omezení přístupu tímto způsobem se vztahuje na všechna volání aplikace, která nemusí být žádoucí pro aplikace, které mají veřejně dostupnou domovskou stránku, stejně jako v mnoha aplikacích s jednou stránkou. U takových aplikací může být upřednostňována možnost **povolení anonymních požadavků (bez akce)** , pokud se aplikace ručně spouští samotné přihlášení, jak je popsáno [zde](overview-authentication-authorization.md#authentication-flow).

5. Klikněte na **Uložit**.

Nyní jste připraveni použít Twitter pro ověřování ve vaší aplikaci.

## <a name="related-content"> </a>Související obsah
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-twitter-authentication/app-service-twitter-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-twitter-authentication/mobile-app-twitter-settings.png

<!-- URLs. -->

[Vývojáři na Twitteru]: https://go.microsoft.com/fwlink/p/?LinkId=268300
[Azure Portal]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-xamarin-ios-get-started-users.md
