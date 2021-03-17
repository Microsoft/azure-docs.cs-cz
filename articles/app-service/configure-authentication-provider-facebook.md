---
title: Konfigurace ověřování na Facebooku
description: Naučte se nakonfigurovat ověřování na Facebooku jako zprostředkovatele identity pro App Service nebo Azure Functions aplikaci.
ms.assetid: b6b4f062-fcb4-47b3-b75a-ec4cb51a62fd
ms.topic: article
ms.date: 06/06/2019
ms.custom:
- seodec18
- fasttrack-edit
ms.openlocfilehash: b6aad323c0d6fa8f59c9fad203640c477b162503
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "80519959"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-facebook-login"></a>Konfigurace App Service nebo Azure Functions aplikace pro použití přihlašovacích údajů na Facebooku

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Tento článek popisuje, jak nakonfigurovat Azure App Service nebo Azure Functions pro použití Facebooku jako poskytovatele ověřování.

K dokončení postupu v tomto článku potřebujete účet Facebook s ověřenou e-mailovou adresou a mobilním telefonním číslem. Pokud chcete vytvořit nový účet Facebook, použijte [Facebook.com].

## <a name="register-your-application-with-facebook"></a><a name="register"> </a>Registrace aplikace pomocí Facebooku

1. Navštivte web pro [vývojáře na Facebooku] a přihlaste se pomocí přihlašovacích údajů k účtu Facebook.

   Pokud nemáte účet Facebooku pro vývojáře **, vyberte Začínáme** a postupujte podle pokynů k registraci.
1. Vyberte **Moje aplikace**  >  **Přidat novou aplikaci**.
1. Do pole **Zobrazovaný název** :
   1. Zadejte jedinečný název vaší aplikace.
   1. Zadejte svůj **kontaktní e-mail**.
   1. Vyberte **vytvořit ID aplikace**.
   1. Dokončete kontrolu zabezpečení.

   Otevře se řídicí panel vývojář pro novou facebookovou aplikaci.
1. Vyberte možnost **řídicí panel**–  >  **přihlášení**k  >  **Set up**  >  **webu**Facebook.
1. V levém navigačním panelu v části **přihlášení na Facebooku**vyberte **Nastavení**.
1. Do pole **platné identifikátory URI pro přesměrování OAuth** zadejte `https://<app-name>.azurewebsites.net/.auth/login/facebook/callback` . Nezapomeňte nahradit `<app-name>` názvem vaší aplikace Azure App Service.
1. Vyberte **Uložit změny**.
1. V levém podokně vyberte **Nastavení**  >  **základní**. 
1. V poli **tajný kód aplikace** vyberte **Zobrazit**. Zkopírujte hodnoty **ID aplikace** a **tajný klíč aplikace**. Později je můžete použít ke konfiguraci aplikace App Service v Azure.

   > [!IMPORTANT]
   > Tajný klíč aplikace je důležité bezpečnostní pověření. Nesdílejte tento tajný klíč s kýmkoli ani ho distribuujte v klientské aplikaci.
   >

1. Účet Facebook, který jste použili k registraci aplikace, je správce aplikace. V tuto chvíli se k této aplikaci můžou přihlásit jenom správci.

   Pokud chcete ověřit jiné účty Facebook, vyberte možnost **Kontrola aplikace** a umožněte ** \<your-app-name> veřejnému** veřejnému přístupu pro přístup k aplikaci pomocí ověřování na Facebooku.

## <a name="add-facebook-information-to-your-application"></a><a name="secrets"> </a>Přidání informací o Facebooku do aplikace

1. Přihlaste se k [Azure Portal] a přejděte do aplikace App Service.
1. Vyberte **Nastavení**  >  **ověřování/autorizace**a ujistěte se, že je **zapnuté** **ověřování App Service** .
1. Vyberte **Facebook**a pak vložte do hodnot ID aplikace a tajné hodnoty aplikace, které jste získali dříve. Povolte všechny rozsahy, které vaše aplikace vyžaduje.
1. Vyberte **OK**.

   ![Snímek obrazovky s nastavením Facebooku mobilní aplikace][0]

    Ve výchozím nastavení App Service poskytuje ověřování, ale neomezuje autorizovaný přístup k obsahu a rozhraním API vašeho webu. Musíte autorizovat uživatele v kódu vaší aplikace.
1. Volitelné Pokud chcete omezit přístup jenom na uživatele ověřované přes Facebook, nastavte **akci, která se má provést, když se žádost neověřuje** na **Facebooku**. Když nastavíte tuto funkci, aplikace vyžaduje ověření všech požadavků. Také přesměruje všechny neověřené požadavky na Facebook pro ověřování.

   > [!CAUTION]
   > Omezení přístupu tímto způsobem se vztahuje na všechna volání aplikace, která nemusí být žádoucí pro aplikace, které mají veřejně dostupnou domovskou stránku, stejně jako v mnoha aplikacích s jednou stránkou. Pro takové aplikace může být vhodnější použití **anonymních požadavků (žádná akce)** , aby aplikace ručně spouštěla ověřování. Další informace najdete v tématu [tok ověřování](overview-authentication-authorization.md#authentication-flow).

1. Vyberte **Uložit**.

Nyní jste připraveni použít Facebook pro ověřování ve vaší aplikaci.

## <a name="next-steps"></a><a name="related-content"> </a>Další kroky

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->
[0]: ./media/app-service-mobile-how-to-configure-facebook-authentication/mobile-app-facebook-settings.png

<!-- URLs. -->
[Vývojáři Facebooku]: https://go.microsoft.com/fwlink/p/?LinkId=268286
[facebook.com]: https://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet/
[Azure Portal]: https://portal.azure.com/
