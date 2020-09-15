---
title: Označení aplikace jako aplikace Publisher jako ověřené – Microsoft Identity Platform | Azure
description: Popisuje, jak označit aplikaci jako ověřený vydavatelem. Pokud je aplikace označena jako ověřená vydavatelem, znamená to, že vydavatel ověřil svoji identitu pomocí účtu Microsoft Partner Network, který dokončil proces ověření a přidružil tento účet MPN k registraci aplikace.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 05/08/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: jesakowi
ms.openlocfilehash: acb4b601118b341d14bc5e7c549d22eef23b2cc2
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90085701"
---
# <a name="mark-your-app-as-publisher-verified"></a>Označení aplikace jako ověřené vydavatelem

Pokud je aplikace označena jako ověřená vydavatelem, znamená to, že vydavatel ověřil svoji identitu pomocí svého účtu Microsoft Partner Network (MPN) a přidružil tento účet MPN k registraci aplikace. Tento článek popisuje, jak dokončit proces [ověření vydavatele](publisher-verification-overview.md) .

## <a name="quickstart"></a>Rychlý start
Pokud už jste zaregistrovaní v Microsoft Partner Network (MPN) a splnili jste [požadavky](publisher-verification-overview.md#requirements), můžete začít hned: 

1. Přihlaste se k [portálu pro registraci aplikací](https://aka.ms/PublisherVerificationPreview) pomocí [Multi-Factor Authentication](../fundamentals/concept-fundamentals-mfa-get-started.md)

1. Vyberte aplikaci a klikněte na **branding**. 

1. Kliknutím na **Přidat ID MPN ověřte vydavatele** a zkontrolujte uvedené požadavky.

1. Zadejte ID MPN a klikněte na **ověřit a uložit**.

Další podrobnosti o konkrétních výhodách, požadavcích a nejčastějších dotazech najdete v tématu [Přehled](publisher-verification-overview.md).


## <a name="mark-your-app-as-publisher-verified"></a>Označení aplikace jako ověřené vydavatelem
Ujistěte se, že jste splnili [požadavky](publisher-verification-overview.md#requirements), a pak postupujte podle těchto kroků a označte vaše aplikace jako vydavatel ověřen.  

1. Ujistěte se, že jste přihlášeni pomocí služby [Multi-Factor Authentication](../fundamentals/concept-fundamentals-mfa-get-started.md) k účtu organizace (Azure AD), který je autorizovaný k provádění změn v aplikacích, které chcete označit jako vydavatel, a v účtu MPN v partnerském centru.

    - V Azure AD musí být tento uživatel členem jedné z následujících rolí: správce aplikace, správce cloudové aplikace, globální správce. 

    - V partnerském centru musí mít tento uživatel tyto role: správce MPN, účty správce nebo globální správce (Toto je sdílená role spravovaná ve službě Azure AD). 

1. Přejděte na portál pro registraci aplikací:  

1. Klikněte na aplikaci, kterou chcete označit jako vydavatel, a otevřete okno branding. 

1. Ujistěte se, že je nastavená [doména vydavatele](howto-configure-publisher-domain.md) aplikace. 

1. Zajistěte, aby se doména vydavatele nebo [vlastní doména](../fundamentals/add-custom-domain.md) ověřená DNS v tenantovi shodovala s doménou e-mailové adresy, která se používá během procesu ověřování pro váš účet MPN.

1. Kliknutím na **Přidat ID MPN ověříte vydavatele** v dolní části stránky. 

1. Zadejte **ID MPN**. Toto ID MPN musí být pro: 

    - Platný účet Microsoft Partner Network, který dokončil proces ověření.  

    - Globální účet partnera (PGA) pro vaši organizaci. 

1. Klikněte na tlačítko **ověřit a uložit**. 

1. Počkejte na zpracování žádosti, což může trvat několik minut. 

1. Pokud bylo ověření úspěšné, okno ověření vydavatele se zavře a vrátí se do okna branding. Vedle ověřeného **zobrazovaného názvu vydavatele**se zobrazí modrý ověřený znak. 

1. Uživatelům, kteří se zobrazí výzva k vyjádření souhlasu s vaší aplikací, začne brzy zobrazovat výzvu, i když se tento proces úspěšně dokončí, i když to může trvat delší dobu, než se replika dokončí v celém systému. 

1. Otestujte tuto funkci tak, že se přihlásíte do vaší aplikace a ověříte, že se na obrazovce pro vyjádření souhlasu zobrazí ověřené označení. Pokud jste přihlášeni jako uživatel, který má aplikaci již udělený souhlas, můžete k vynucení výzvy k vyjádření souhlasu použít parametr *prompt = souhlasu* . Tento parametr by se měl používat jenom pro testování a nikdy pevně zakódovaný do požadavků vaší aplikace.

1. Tento postup opakujte podle potřeby u všech dalších aplikací, pro které chcete zobrazit hvězdičku. Microsoft Graph můžete použít k rychlejšímu hromadnému provádění rutin PowerShellu, které budou brzy k dispozici. Další informace najdete v tématu [vytváření volání Microsoft API graphu](troubleshoot-publisher-verification.md#making-microsoft-graph-api-calls) . 

A to je vše! Dejte nám vědět, pokud máte nějakou zpětnou vazbu k procesu, výsledkům nebo funkcím všeobecně. 

## <a name="next-steps"></a>Další kroky
Pokud narazíte na problémy, přečtěte si [informace o řešení potíží](troubleshoot-publisher-verification.md).
