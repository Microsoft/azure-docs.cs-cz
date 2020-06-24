---
title: Vytvoření & publikování dokumentace jednotného přihlašování pro vaši aplikaci
description: Pokyny pro nezávislé výrobce softwaru při integraci s Azure Active Directory
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: kenwith
ms.reviewer: jeeds
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7e2436175fc68d76dabb98fb8ca4c9c27cde007b
ms.sourcegitcommit: bc943dc048d9ab98caf4706b022eb5c6421ec459
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/14/2020
ms.locfileid: "84763308"
---
# <a name="create-and-publish-single-sign-on-documentation-for-your-application"></a>Vytvoření a publikování dokumentace jednotného přihlašování pro vaši aplikaci   

## <a name="documentation-on-your-site"></a>Dokumentace na vašem webu

Snadné přijetí je významným faktorem při rozhodování podnikového softwaru. Jasná dokumentace, která se dá snadno sledovat, podporuje vaše zákazníky v cestě k přijetí a snižuje náklady na podporu. Při práci s tisíci výrobců softwaru si Microsoft viděli, co funguje.

Doporučujeme, aby vaše dokumentace na webu obsahovala minimálně následující položky.

* Seznámení s funkcemi jednotného přihlašování

  * Podporované protokoly

  * Verze a SKU

  * Seznam podporovaných zprostředkovatelů identity s odkazy na dokumentaci

* Licenční informace pro vaši aplikaci

* Řízení přístupu na základě role pro konfiguraci jednotného přihlašování

* Kroky konfigurace jednotného přihlašování

  * Prvky konfigurace uživatelského rozhraní pro SAML s očekávanými hodnotami od poskytovatele

  * Informace o poskytovateli služeb, které se mají předat zprostředkovatelům identity

* Pokud OIDC/OAuth

  * Seznam oprávnění vyžadovaných pro vyjádření souhlasu s obchodními odůvodněními

* Postup testování pro uživatele pilotního nasazení

* Informace o řešení potíží, včetně chybových kódů a zpráv

* Mechanismy podpory pro zákazníky

## <a name="documentation-on-the-microsoft-site"></a>Dokumentace na webu společnosti Microsoft

Při vytváření seznamu aplikací pomocí Azure Active Directory Galerie aplikací, která také publikuje vaši aplikaci v Azure Marketplace, společnost Microsoft vygeneruje dokumentaci pro naše vzájemné zákazníky, kteří vysvětlují podrobný proces. [Tady](https://aka.ms/appstutorial)vidíte příklad. Tato dokumentace je vytvořena na základě odeslání do galerie a můžete ji snadno aktualizovat, pokud provedete změny aplikace pomocí účtu GitHub.

## <a name="next-steps"></a>Další kroky

[Vypsání aplikace v galerii aplikací Azure AD](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/Default.aspx)