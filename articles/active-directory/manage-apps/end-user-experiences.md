---
title: Prostředí koncových uživatelů pro aplikace – Azure Active Directory
description: Azure Active Directory (Azure AD) poskytuje několik přizpůsobitelných způsobů nasazení aplikací koncovým uživatelům ve vaší organizaci.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266621"
---
# <a name="end-user-experiences-for-applications-in-azure-active-directory"></a>Prostředí koncových uživatelů pro aplikace ve službě Azure Active Directory

Azure Active Directory (Azure AD) poskytuje několik přizpůsobitelných způsobů nasazení aplikací koncovým uživatelům ve vaší organizaci:

* Přístupový panel Azure AD
* Spouštěč aplikací Office 365
* Přímé přihlášení k federovaným aplikacím
* Přímé odkazy na federované nebo existující aplikace či aplikace založené na hesle

Které metody se rozhodnete nasadit ve vaší organizaci, je vaše uvážení.

## <a name="azure-ad-access-panel"></a>Přístupový panel Azure AD

Přístupový panel https://myapps.microsoft.com na webu je webový portál, který umožňuje koncovému uživateli s účtem organizace ve službě Azure Active Directory zobrazit a spustit aplikace, ke kterým jim správce Azure AD udělil přístup. Pokud jste koncový uživatel s [Azure Active Directory Premium](https://azure.microsoft.com/pricing/details/active-directory/), můžete také využít možnosti samoobslužné skupiny správy prostřednictvím přístupového panelu.

![Snímek obrazovky zobrazuje portál přístupového panelu Azure AD](media/what-is-single-sign-on/azure-ad-access-panel.png)

Ve výchozím nastavení jsou všechny aplikace uvedeny společně na jedné stránce. Ale můžete použít kolekce seskupit související aplikace a prezentovat je na samostatné kartě, což usnadňuje jejich nalezení. Kolekce můžete například použít k vytvoření logických seskupení aplikací pro konkrétní role úlohy, úkoly, projekty a tak dále. Další informace naleznete v tématu [Jak pomocí kolekcí Moje aplikace přizpůsobit panely pro přístup uživatelů](access-panel-collections.md). 

Přístupový panel je oddělený od portálu Azure a nevyžaduje, aby uživatelé měli předplatné Azure nebo předplatné Office 365.

Další informace o přístupovém panelu Azure AD najdete v [úvodu přístupového panelu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="office-365-application-launcher"></a>Spouštěč aplikací Office 365

Pro organizace, které nasadily Office 365, se aplikace přiřazené uživatelům prostřednictvím Azure [https://portal.office.com/myapps](https://portal.office.com/myapps)AD zobrazí také na portálu Office 365 na webu . To usnadňuje a usnadňuje uživatelům v organizaci spouštět své aplikace bez nutnosti používat druhý portál a je doporučeným řešením pro spouštění aplikací pro organizace používající Office 365.

![Snímek obrazovky ukazuje portál Office 365](./media/end-user-experiences/microsoft-365-portal-office-com.png)

Další informace o spouštěči aplikací Office 365 najdete [v tématu Nastavení aplikace ve spouštěči aplikací Office 365](https://msdn.microsoft.com/office/office365/howto/connect-your-app-to-o365-app-launcher).

## <a name="direct-sign-on-to-federated-apps"></a>Přímé přihlášení k federovaným aplikacím

Většina federovaných aplikací, které podporují saml 2.0, WS-Federation nebo OpenID připojit také podporují možnost pro uživatele spustit v aplikaci a pak získat přihlášeni prostřednictvím Služby Azure AD buď automatické přesměrování nebo kliknutím na odkaz pro přihlášení. To se označuje jako přihlášení iniciované poskytovatelem služeb a většina federovaných aplikací v galerii aplikací Azure AD to podporuje (podrobnosti najdete v dokumentaci propojené z průvodce konfigurací jednotného přihlášení aplikace na webu Azure Portal).

![Příklad přihlašovací stránky mobilní aplikace](./media/end-user-experiences/workdaymobile.png)

## <a name="direct-sign-on-links"></a>Přímé přihlašovací odkazy

Azure AD také podporuje přímé jednotné přihlašování odkazy na jednotlivé aplikace, které podporují jednotné přihlašování založené na heslech, propojené jednotné přihlašování a jakoukoli formu federovaného jednotného přihlášení.

Tyto odkazy jsou speciálně vytvořené adresy URL, které odesílají uživatele prostřednictvím procesu přihlášení azure ad pro konkrétní aplikaci bez nutnosti uživatele spustit z přístupového panelu Azure AD nebo Office 365. Tyto **adresy URL pro přístup uživatelů** naleznete pod vlastnostmi dostupných podnikových aplikací. Na webu Azure Portal vyberte aplikace **Azure Active Directory** > **Enterprise**. Vyberte aplikaci a pak vyberte **Vlastnosti**.

![Příklad adresy URL pro přístup uživatelů ve vlastnostech Twitteru](media/end-user-experiences/direct-sign-on-link.png)

Tyto odkazy lze zkopírovat a vložit kamkoli, kam chcete poskytnout přihlašovací odkaz na vybranou aplikaci. Může se to nacházet v e-mailu nebo na libovolném vlastním webovém portálu, který jste nastavili pro přístup k uživatelským aplikacím. Tady je příklad adresy URL přímého jednotného přihlašování Azure AD pro Twitter:

`https://myapps.microsoft.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

Podobně jako adresy URL přístupového panelu specifické pro organizaci můžete tuto adresu URL dále přizpůsobit přidáním jedné z aktivních nebo ověřených domén pro svůj adresář za myapps.microsoft.com domény. Tím zajistíte, že všechny organizační značky se načte okamžitě na přihlašovací stránce, aniž by uživatel musel nejprve zadat své ID uživatele:

`https://myapps.microsoft.com/contosobuild.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

Když oprávněný uživatel klikne na jeden z těchto odkazů specifických pro aplikaci, nejprve uvidí svou stránku pro přihlášení do organizace (za předpokladu, že ještě nejsou přihlášeni) a po přihlášení jsou přesměrováni do své aplikace, aniž by se nejprve zastavili na přístupovém panelu. Pokud uživateli chybí předpoklady pro přístup k aplikaci, jako je například rozšíření prohlížeče s jedním znakem založené na heslech, zobrazí odkaz uživatele výzvu k instalaci chybějícího rozšíření. Adresa URL odkazu také zůstává konstantní, pokud se změní konfigurace jednotného přihlášení pro aplikaci.

Tyto odkazy používají stejné mechanismy řízení přístupu jako přístupový panel a Office 365 a pouze uživatelé nebo skupiny, které byly přiřazeny k aplikaci na webu Azure Portal, budou moci úspěšně ověřit. Každý uživatel, který je neoprávněný, se však zobrazí zpráva s vysvětlením, že mu nebyl udělen přístup, a bude mu poskytnut odkaz na načtení přístupového panelu k zobrazení dostupných aplikací, ke kterým mají přístup.

## <a name="next-steps"></a>Další kroky

Plány nasazení najdete v tématu [Plány nasazení služby Azure Active Directory.](../fundamentals/active-directory-deployment-plans.md)
