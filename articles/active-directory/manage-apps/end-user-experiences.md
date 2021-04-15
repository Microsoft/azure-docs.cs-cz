---
title: Prostředí koncových uživatelů pro aplikace – Azure Active Directory
description: Azure Active Directory (Azure AD) poskytuje několik přizpůsobitelných způsobů, jak nasadit aplikace pro koncové uživatele ve vaší organizaci.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: iangithinji
ms.reviewer: arvindh
ms.openlocfilehash: c555899a65a5e8cf4c8fcc6214e4dcbda3931f08
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374229"
---
# <a name="end-user-experiences-for-applications-in-azure-active-directory"></a>Prostředí koncových uživatelů pro aplikace v Azure Active Directory

Azure Active Directory (Azure AD) poskytuje několik přizpůsobitelných způsobů, jak nasadit aplikace pro koncové uživatele ve vaší organizaci:

* Moje aplikace v Azure AD
* Microsoft 365 spouštěč aplikace
* Přímé přihlášení k federovaným aplikacím
* Přímé odkazy na federované nebo existující aplikace či aplikace založené na hesle

Jaké metody si zvolíte pro nasazení ve vaší organizaci, je vaše rozhodnutí.

## <a name="azure-ad-my-apps"></a>Moje aplikace v Azure AD

Moje aplikace na https://myapps.microsoft.com webu je webový portál, který umožňuje koncovému uživateli s účtem organizace v Azure Active Directory zobrazit a spustit aplikace, kterým jim byl udělen přístup správce Azure AD. Pokud jste koncovým uživatelem s [Azure Active Directory Premium](https://azure.microsoft.com/pricing/details/active-directory/), můžete prostřednictvím mých aplikací využít možnosti samoobslužné správy skupin.

Ve výchozím nastavení jsou všechny aplikace uvedeny společně na jedné stránce. Kolekce ale můžete použít k seskupení souvisejících aplikací a jejich zobrazení na samostatné kartě, což usnadňuje jejich vyhledání. Kolekce můžete například použít k vytvoření logických seskupení aplikací pro konkrétní role úlohy, úkoly, projekty a tak dále. Informace najdete v tématu [Vytvoření kolekcí na portálu moje aplikace](access-panel-collections.md). 

Moje aplikace jsou oddělené od Azure Portal a nevyžadují, aby uživatelé měli předplatné Azure nebo Microsoft 365 předplatné.

Další informace o službě Azure AD moje aplikace najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).

## <a name="microsoft-365-application-launcher"></a>Microsoft 365 spouštěč aplikace

Pro organizace, které nasadily Microsoft 365, se aplikace přiřazené uživatelům prostřednictvím služby Azure AD zobrazí také na portálu Office 365 na adrese [https://portal.office.com/myapps](https://portal.office.com/myapps) . To usnadňuje uživatelům v organizaci, aby mohli spouštět své aplikace, aniž by museli používat druhý portál a je doporučené řešení pro spouštění aplikací pro organizace, které používají Microsoft 365.

Další informace o spouštěčech aplikací Office 365 najdete v tématu o tom, že [se vaše aplikace zobrazuje ve Spouštěči aplikací office 365](/previous-versions/office/office-365-api/).

## <a name="direct-sign-on-to-federated-apps"></a>Přímé přihlášení k federovaným aplikacím

Většina federovaných aplikací, které podporují SAML 2,0, WS-Federation nebo OpenID Connect, taky podporuje možnost spouštění uživatelů v aplikaci a přihlášení prostřednictvím služby Azure AD buď pomocí automatického přesměrování, nebo kliknutím na odkaz pro přihlášení. To se označuje jako přihlašování iniciované poskytovatelem služeb a většina federovaných aplikací v galerii aplikací Azure AD (podrobnosti najdete v dokumentaci propojené z Průvodce konfigurací jednotného přihlašování aplikace v Azure Portal).

## <a name="direct-sign-on-links"></a>Přímé přihlašovací odkazy

Azure AD také podporuje přímé jednotné přihlašování k jednotlivým aplikacím, které podporují jednotné přihlašování založené na heslech, propojené jednotné přihlašování a jakoukoli formu federovaného jednotného přihlašování.

Tyto odkazy jsou speciálně vytvořené adresy URL, které odesílají uživatele prostřednictvím procesu přihlášení služby Azure AD pro konkrétní aplikaci, aniž by museli uživatele spustit z Azure AD moje aplikace nebo Microsoft 365. Tyto **adresy URL pro přístup uživatelů** najdete ve vlastnostech dostupných podnikových aplikací. V Azure Portal vyberte **Azure Active Directory**  >  **podnikových aplikací**. Vyberte aplikaci a pak vyberte **vlastnosti**.

![Příklad adresy URL přístupu uživatele ve vlastnostech Twitteru](media/end-user-experiences/direct-sign-on-link.png)

Tyto odkazy můžete zkopírovat a vložit kdekoli, kde chcete zadat odkaz pro přihlášení k vybrané aplikaci. K tomu může dojít v e-mailu nebo na jakémkoli vlastním webovém portálu, který jste nastavili pro přístup k uživatelským aplikacím. Tady je příklad adresy URL jednotného přihlašování Azure AD pro Twitter:

`https://myapps.microsoft.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

Podobně jako adresy URL specifické pro organizaci pro moje aplikace můžete tuto adresu URL dál upravit přidáním jedné z aktivních nebo ověřených domén pro adresář za doménu *myapps.Microsoft.com* . Tím se zajistí, že se na přihlašovací stránce okamžitě načte jakékoli organizační značky, aniž by uživatel musel zadat své ID uživatele:

`https://myapps.microsoft.com/contosobuild.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

Když ověřený uživatel klikne na jednu z těchto odkazů specifických pro jednotlivé aplikace, nejprve uvidí přihlašovací stránku organizace (za předpokladu, že ještě nejsou přihlášení) a když se přihlásí do své aplikace, aniž by se nejdřív zastavily ve svých aplikacích. Pokud uživatel postrádá požadavky pro přístup k aplikaci, jako je například rozšíření prohlížeče jednotného přihlašování založené na heslech, pak odkaz vyzve uživatele k instalaci chybějícího rozšíření. Adresa URL odkazu také zůstává konstantní, pokud se změní konfigurace jednotného přihlašování pro aplikaci.

Tyto odkazy používají stejné mechanismy řízení přístupu jako moje aplikace a Microsoft 365. pouze uživatelé nebo skupiny, kteří byli přiřazeni k aplikaci v Azure Portal, budou moci úspěšně ověřit. Jakýkoli uživatel, který je neautorizovaný, uvidí ale zprávu s vysvětlením, že jim nebyl udělen přístup, a má odkaz na načtení mých aplikací, aby zobrazil dostupné aplikace, ke kterým mají přístup.

## <a name="next-steps"></a>Další kroky

* [Série rychlý Start při správě aplikací](view-applications-portal.md)
* [Co je jednotné přihlašování?](what-is-single-sign-on.md)
* [Průvodce integrací Azure Active Directory s aplikacemi Začínáme](plan-an-application-integration.md)