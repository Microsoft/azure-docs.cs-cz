---
author: baanders
description: zahrnutí souboru pro možné další požadavky v nastavení digitálních vláken Azure
ms.service: digital-twins
ms.topic: include
ms.date: 7/22/2020
ms.author: baanders
ms.openlocfilehash: 230304677b78f00b2d1288c846f8bf704cd8a497
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2020
ms.locfileid: "87407433"
---
Je možné, že vaše organizace vyžaduje další akce od vlastníků předplatného, aby úspěšně nastavila registraci aplikace (a proto dokončila nastavení použitelné instance digitálního vlákna Azure). Požadované kroky se můžou lišit v závislosti na konkrétním nastavení vaší organizace.

Tady jsou některé běžné potenciální aktivity, které může vlastník potřebovat udělat. Tyto a jiné operace lze provést ze stránky [*aplikace Azure AD registrace*](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) v Azure Portal.
* Udělení souhlasu správce k registraci aplikace Pro všechny registrace aplikací v rámci vašeho předplatného může být ve službě Azure AD ve vaší organizaci globálně zapnutý *souhlas správce* . Pokud ano, bude muset vlastník vybrat toto tlačítko pro vaši společnost na stránce *oprávnění API* pro registraci aplikace, aby byla platná pro registraci aplikace:

    :::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/portal/grant-admin-consent.png" alt-text="Zobrazení portálu pro tlačítko udělení souhlasu správce v části oprávnění rozhraní API":::
  - Pokud byl souhlas udělen úspěšně, měla by položka pro digitální vlákna Azure zobrazit hodnotu *stavu* _udělenou pro **(vaše společnost)** _ .
   
    :::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/portal/granted-admin-consent-done.png" alt-text="Zobrazení portálu souhlasu správce udělené pro společnost pod oprávněními rozhraní API":::
* Aktivace přístupu veřejného klienta
* Nastavit konkrétní adresy URL odpovědí pro přístup k webu a desktopu
* Povolení pro toky implicitního ověřování OAuth2

Další informace o registraci aplikace a jejích různých možnostech instalace najdete v tématu [*Registrace aplikace s platformou Microsoft Identity*](https://docs.microsoft.com/graph/auth-register-app-v2).

Teď máte k dispozici instanci digitálních vláken Azure, která je připravená k použití, přiřadili jste oprávnění ke správě IT a měli byste nastavit oprávnění pro klientské aplikace pro přístup k ní.