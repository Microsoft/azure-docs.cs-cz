---
author: baanders
description: zahrnutí souboru pro možné další požadavky v nastavení digitálních vláken Azure
ms.service: digital-twins
ms.topic: include
ms.date: 7/22/2020
ms.author: baanders
ms.openlocfilehash: c2668bdda5002ebd2a34b8a2ffa5885263aec0c9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88009635"
---
Je možné, že vaše organizace vyžaduje další akce od vlastníků nebo správců předplatného, aby úspěšně nastavila registraci aplikace (a proto dokončila nastavení použitelné instance digitálních vláken Azure). Požadované kroky se můžou lišit v závislosti na konkrétním nastavení vaší organizace.

Tady jsou některé běžné potenciální aktivity, které může být potřeba udělat s vlastníkem nebo správcem. Tyto a jiné operace lze provést ze stránky [*aplikace Azure AD registrace*](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) v Azure Portal.
* Udělení souhlasu správce k registraci aplikace Pro všechny registrace aplikací v rámci vašeho předplatného může být ve službě Azure AD ve vaší organizaci globálně zapnutý *souhlas správce* . Pokud ano, bude muset vlastník nebo správce vybrat toto tlačítko pro vaši společnost na stránce *oprávnění rozhraní API* pro registraci aplikace, aby byla platná pro registraci aplikace:

    :::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/portal/grant-admin-consent.png" alt-text="Zobrazení portálu pro tlačítko udělení souhlasu správce v části oprávnění rozhraní API":::
  - Pokud byl souhlas udělen úspěšně, měla by položka pro digitální vlákna Azure zobrazit hodnotu *stavu* _udělenou pro **(vaše společnost)** _ .
   
    :::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/portal/granted-admin-consent-done.png" alt-text="Zobrazení portálu pro tlačítko udělení souhlasu správce v části oprávnění rozhraní API":::
* Aktivace přístupu veřejného klienta
* Nastavit konkrétní adresy URL odpovědí pro přístup k webu a desktopu
* Povolení pro toky implicitního ověřování OAuth2

Další informace o registraci aplikace a jejích různých možnostech instalace najdete v tématu [*Registrace aplikace s platformou Microsoft Identity*](https://docs.microsoft.com/graph/auth-register-app-v2).

Teď máte k dispozici instanci digitálních vláken Azure, která je připravená k použití, přiřadili jste oprávnění ke správě IT a měli byste nastavit oprávnění pro klientské aplikace pro přístup k ní.