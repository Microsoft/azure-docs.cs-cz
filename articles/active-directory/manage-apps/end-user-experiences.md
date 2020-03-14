---
title: Prostředí koncových uživatelů pro aplikace – Azure Active Directory
description: Azure Active Directory (Azure AD) poskytuje několik přizpůsobitelných způsobů, jak nasadit aplikace pro koncové uživatele ve vaší organizaci.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: mimart
ms.reviewer: arvindh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 72b3e37a423442194b81e3d10ecc7157201ff8be
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79266621"
---
# <a name="end-user-experiences-for-applications-in-azure-active-directory"></a>Prostředí koncových uživatelů pro aplikace v Azure Active Directory

Azure Active Directory (Azure AD) poskytuje několik přizpůsobitelných způsobů, jak nasadit aplikace pro koncové uživatele ve vaší organizaci:

* Přístupový panel Azure AD
* Spouštěč aplikace Office 365
* Přímé přihlášení k federovaným aplikacím
* Přímé odkazy na federované nebo existující aplikace či aplikace založené na hesle

Jaké metody si zvolíte pro nasazení ve vaší organizaci, je vaše rozhodnutí.

## <a name="azure-ad-access-panel"></a>Přístupový panel Azure AD

Přístupový panel na https://myapps.microsoft.com je webový portál, který umožňuje koncovému uživateli s účtem organizace v Azure Active Directory zobrazit a spustit aplikace, kterým jim byl udělen přístup správce Azure AD. Pokud jste koncovým uživatelem s [Azure Active Directory Premium](https://azure.microsoft.com/pricing/details/active-directory/), můžete pomocí přístupového panelu využít také možnosti samoobslužné správy skupin.

![Snímek obrazovky se zobrazí na portálu Azure AD Access panel](media/what-is-single-sign-on/azure-ad-access-panel.png)

Ve výchozím nastavení jsou všechny aplikace uvedeny společně na jedné stránce. Kolekce ale můžete použít k seskupení souvisejících aplikací a jejich zobrazení na samostatné kartě, což usnadňuje jejich vyhledání. Kolekce můžete například použít k vytvoření logických seskupení aplikací pro konkrétní role úlohy, úkoly, projekty a tak dále. Informace najdete v tématu [Jak používat kolekce mých aplikací k přizpůsobení panelů přístupu uživatelů](access-panel-collections.md). 

Přístupový panel je oddělený od Azure Portal a nevyžaduje, aby uživatelé měli předplatné Azure nebo předplatné Office 365.

Další informace o přístupovém panelu Azure AD najdete v tématu [Úvod do přístupového panelu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="office-365-application-launcher"></a>Spouštěč aplikace Office 365

V organizacích, které mají nasazené Office 365, se na portálu Office 365 na [https://portal.office.com/myapps](https://portal.office.com/myapps)zobrazí také aplikace přiřazené uživatelům prostřednictvím služby Azure AD. To usnadňuje uživatelům v organizaci, aby mohli spouštět své aplikace, aniž by museli používat druhý portál a je doporučené řešení pro spouštění aplikací pro organizace, které používají Office 365.

![Snímek obrazovky zobrazující portál Office 365](./media/end-user-experiences/microsoft-365-portal-office-com.png)

Další informace o spouštěčech aplikací Office 365 najdete v tématu o tom, že [se vaše aplikace zobrazuje ve Spouštěči aplikací office 365](https://msdn.microsoft.com/office/office365/howto/connect-your-app-to-o365-app-launcher).

## <a name="direct-sign-on-to-federated-apps"></a>Přímé přihlášení k federovaným aplikacím

Většina federovaných aplikací, které podporují SAML 2,0, WS-Federation nebo OpenID Connect, taky podporuje možnost spouštění uživatelů v aplikaci a přihlášení prostřednictvím služby Azure AD buď pomocí automatického přesměrování, nebo kliknutím na odkaz pro přihlášení. To se označuje jako přihlašování iniciované poskytovatelem služeb a většina federovaných aplikací v galerii aplikací Azure AD (podrobnosti najdete v dokumentaci propojené z Průvodce konfigurací jednotného přihlašování aplikace v Azure Portal).

![Příklad přihlašovací stránky mobilní aplikace](./media/end-user-experiences/workdaymobile.png)

## <a name="direct-sign-on-links"></a>Přímé přihlašovací odkazy

Azure AD také podporuje přímé jednotné přihlašování k jednotlivým aplikacím, které podporují jednotné přihlašování založené na heslech, propojené jednotné přihlašování a jakoukoli formu federovaného jednotného přihlašování.

Tyto odkazy jsou speciálně vytvořené adresy URL, které odesílají uživatele prostřednictvím procesu přihlášení služby Azure AD pro konkrétní aplikaci, aniž by museli uživatele spustit z přístupového panelu Azure AD nebo ze sady Office 365. Tyto **adresy URL pro přístup uživatelů** najdete ve vlastnostech dostupných podnikových aplikací. V Azure Portal vyberte **Azure Active Directory** > **podnikových aplikací**. Vyberte aplikaci a pak vyberte **vlastnosti**.

![Příklad adresy URL přístupu uživatele ve vlastnostech Twitteru](media/end-user-experiences/direct-sign-on-link.png)

Tyto odkazy můžete zkopírovat a vložit kdekoli, kde chcete zadat odkaz pro přihlášení k vybrané aplikaci. K tomu může dojít v e-mailu nebo na jakémkoli vlastním webovém portálu, který jste nastavili pro přístup k uživatelským aplikacím. Tady je příklad adresy URL jednotného přihlašování Azure AD pro Twitter:

`https://myapps.microsoft.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

Podobně jako adresy URL pro přístupovou aplikaci pro konkrétní organizace můžete tuto adresu URL dál upravit přidáním jedné z aktivních nebo ověřených domén pro adresář za doménu myapps.microsoft.com. Tím se zajistí, že se na přihlašovací stránce okamžitě načte jakékoli organizační značky, aniž by uživatel musel zadat své ID uživatele:

`https://myapps.microsoft.com/contosobuild.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

Když ověřený uživatel klikne na jednu z těchto odkazů specifických pro jednotlivé aplikace, nejprve uvidí přihlašovací stránku organizace (za předpokladu, že ještě nejsou přihlášeni), a když se přihlásí do své aplikace, aniž by bylo třeba na přístupovém panelu zastavovat. Pokud uživatel postrádá požadavky pro přístup k aplikaci, jako je například rozšíření prohlížeče jednotného přihlašování založené na heslech, pak odkaz vyzve uživatele k instalaci chybějícího rozšíření. Adresa URL odkazu také zůstává konstantní, pokud se změní konfigurace jednotného přihlašování pro aplikaci.

Tyto odkazy používají stejné mechanismy řízení přístupu jako přístupový panel a Office 365 a budou moci úspěšně ověřit pouze uživatelé nebo skupiny, kteří byli přiřazeni k aplikaci v Azure Portal. Jakýkoli uživatel, který je neautorizovaný, se však zobrazí zpráva s vysvětlením, že jim nebyl udělen přístup, a má odkaz na načtení přístupového panelu, ve kterém zobrazí dostupné aplikace, ke kterým mají přístup.

## <a name="next-steps"></a>Další kroky

Plány nasazení najdete v tématu [Azure Active Directory plány nasazení](../fundamentals/active-directory-deployment-plans.md) .
