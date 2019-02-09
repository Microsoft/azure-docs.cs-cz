---
title: Činnost koncového uživatele pro aplikace – Azure Active Directory | Dokumentace Microsoftu
description: Azure Active Directory (Azure AD) poskytuje několik přizpůsobitelné způsobů, jak nasadit aplikace koncovým uživatelům ve vaší organizaci.
services: active-directory
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 11/09/2018
ms.author: celested
ms.reviewer: arvindh
ms.openlocfilehash: 285fd4be7953c9965ea3f91580753e05bcb4d89d
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2019
ms.locfileid: "55964385"
---
# <a name="end-user-experiences-for-applications-in-azure-active-directory"></a>Činnost koncového uživatele pro aplikace v Azure Active Directory
Azure Active Directory (Azure AD) poskytuje několik přizpůsobitelné způsobů, jak nasadit aplikace koncovým uživatelům ve vaší organizaci:

* Azure AD přístupového panelu
* Spouštěč aplikací Office 365
* Přímé přihlášení k federovaným aplikacím
* Přímé odkazy na federované nebo existující aplikace či aplikace založené na hesle

Metody, které budete chtít nasadit ve vaší organizaci je vaše podle vlastního uvážení.

## <a name="azure-ad-access-panel"></a>Azure AD přístupového panelu
Přístupový Panel na https://myapps.microsoft.com je webový portál, který umožňuje koncovým uživatelům s účtem organizace ve službě Azure Active Directory k zobrazení a spuštění cloudových aplikací na které byly udělen přístup správce Azure AD. Pokud jste koncový uživatel s [Azure Active Directory Premium](https://azure.microsoft.com/pricing/details/active-directory/), můžete také využít možnosti správy samoobslužné skupiny přes přístupový Panel.

![Azure AD Access Panel](media/what-is-single-sign-on/azure-ad-access-panel.png)

Přístupový Panel je nezávislý na webu Azure Portal a nevyžaduje, aby uživatelé měli k předplatnému Azure nebo předplatné služeb Office 365.

Další informace o přístupovém panelu Azure AD, najdete v článku [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="office-365-application-launcher"></a>Spouštěč aplikací Office 365
Organizace, které jste nasadili Office 365, aplikace přiřazené k uživatelům prostřednictvím služby Azure AD se také zobrazí na portálu Office 365 na https://portal.office.com/myapps. To umožňuje snadné a pohodlné pro uživatele v organizaci spouštění svých aplikací bez nutnosti použít portál druhý a je doporučenou aplikaci spouštění řešení pro organizace používá Office 365.

![](./media/what-is-single-sign-on/officeapphub.png)

Další informace o Spouštěče aplikací Office 365 najdete v tématu [mít vaše aplikace se zobrazí ve Spouštěči aplikací Office 365](https://msdn.microsoft.com/office/office365/howto/connect-your-app-to-o365-app-launcher).

## <a name="direct-sign-on-to-federated-apps"></a>Přímé přihlášení k federovaným aplikacím
Nejvíce federovaným aplikacím, které podporují protokol SAML 2.0, WS-Federation nebo OpenID connect také podporu možnost uživatelů ke spouštění na úrovni aplikace a pak získejte přihlášení prostřednictvím služby Azure AD automatické přesměrování nebo kliknutím na odkaz pro přihlášení. To se označuje jako poskytovatel služeb-zahájí přihlašování a nejvíce federované aplikace v galerii aplikací Azure AD podporují tento (viz dokumentace odkazované z průvodce Konfigurace jednotného přihlašování aplikace na webu Azure Portal podrobnosti).

![](./media/what-is-single-sign-on/workdaymobile.png)

## <a name="direct-sign-on-links"></a>Přímé odkazy přihlašování
Azure AD podporuje také přímý jednotné přihlašování odkazy na jednotlivých aplikací, které podporují založené na heslech jednotného přihlašování, propojené jednotného přihlašování a jakoukoli formu federované jednotné přihlašování.

Tyto odkazy jsou specificky vytvořený adresy URL, které odesílají uživatele prostřednictvím přihlášení procesu služby Azure AD pro danou aplikaci bez nutnosti spuštění uživatele ze služby Azure AD je přístup k panelu nebo v Office 365. Tyto adresy jednotné přihlašování najdete v části řídicího panelu kartu všechny předem integrované aplikace v sekci Active Directory na webu Azure portal, jak je znázorněno v následujícím snímku obrazovky.

![](./media/what-is-single-sign-on/deeplink.png)

Tyto odkazy můžete zkopírovat a vložit kamkoli chcete poskytnout odkaz přihlášení na vybrané aplikaci. Může to být e-mailem nebo v jakékoli vlastní webový portál, který jste vytvořili pro přístup k aplikaci uživatele. Tady je příklad služby Azure AD s přímým přístupem jednotné přihlašování – adresy URL pro Twitter:

`https://myapps.microsoft.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

Podobně jako u adresy URL specifické pro organizaci na přístupovém panelu, můžete dále přizpůsobit tuto adresu URL tak, že přidáte jednu aktivní nebo ověřených domén pro svůj adresář myapps.microsoft.com doména. Tím se zajistí, že organizace branding se načetl okamžitě na přihlašovací stránku bez nutnosti nechat napřed zadejte svoje ID uživatele:

`https://myapps.microsoft.com/contosobuild.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

Když oprávněný uživatel klikne na jednu z těchto odkazů specifické pro aplikaci, se nejprve najdete v jejich organizační přihlašovací stránky (za předpokladu, že se ještě nejste přihlášeni) a po přihlášení se přesměrují do své aplikace bez zastavení se nejprve na přístupovém panelu. Pokud uživatel chybí požadavky na přístup k aplikaci, jako je například rozšíření prohlížeče jednotné přihlašování pomocí hesla, odkaz vyzve uživatele k instalaci chybějících rozšíření. Adresa URL odkazu také konstantní, pokud se jednotné přihlašování – konfigurace pro aplikaci změní.

Tyto odkazy použít stejné mechanismy řízení přístupu jako na přístupovém panelu a Office 365 a pouze uživatele nebo skupiny, kteří mají přiřazený k aplikaci na webu Azure Portal budete moct úspěšně ověřit. Každý uživatel, který nemá oprávnění se ale zobrazí se zpráva s vysvětlením, aby nebyl udělen přístup a disponují odkaz k načtení přístupového panelu zobrazíte dostupné aplikace, ke kterým mají přístup.

## <a name="next-steps"></a>Další postup
Plány nasazení, najdete v části [plány nasazení služby Azure Active Directory](../fundamentals/active-directory-deployment-plans.md)
