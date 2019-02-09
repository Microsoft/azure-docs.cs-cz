---
title: Neočekávaná chyba při povolování spuštění aplikace | Dokumentace Microsoftu
description: Tento článek popisuje chyby, které mohou nastat při vyjadřování souhlasu s aplikací a co můžete dělat o nich
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: celested
ms.reviewer: asteen
ms.openlocfilehash: 24cf2da101b3d72e985e9c797e61131d6755b989
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2019
ms.locfileid: "55959353"
---
# <a name="unexpected-error-when-performing-consent-to-an-application"></a>Neočekávaná chyba při povolování spuštění aplikace

Tento článek popisuje chyby, které se mohou vyskytnout při vyjadřování souhlasu s aplikace. Pokud řešíte výzev k udělení souhlasu neočekávané, které neobsahují žádné chybové zprávy, přečtěte si téma [scénáře ověřování pro službu Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-scenarios).

Mnoho aplikací, které se integrují s Azure Active Directory, vyžadují oprávnění pro přístup k dalším prostředkům mohl fungovat. Když jsou tyto prostředky také integrované s Azure Active Directory, oprávnění pro přístup k nim je často požadovaná pomocí běžné rozhraní pro udělování souhlasu. Zobrazí se výzva k povolení spuštění, který obvykle probíhá prvním spuštění aplikace se používá, ale může vyskytovat i na pozdější použití aplikace.

Musí být splněny pro uživatele na oprávnění, která aplikace vyžaduje souhlas určité podmínky. Nejsou-li tyto podmínky splněny, může dojít k následující chybě.

## <a name="requesting-not-authorized-permissions-error"></a>Vyžádání chyba Neautorizováno oprávnění
* **AADSTS90093:** &lt;clientAppDisplayName&gt; vyžaduje jeden nebo více oprávnění, ke kterým máte Neautorizováno k udělení. Obraťte se na správce, kteří můžou udělit souhlas s vaším jménem této aplikace.

Tato chyba nastane, pokud uživatel, který není správcem společnosti se pokusí použít aplikaci, která žádá o oprávnění, která lze udělit pouze správce. Tuto chybu můžete vyřešit microsoftem nebo správcem udělení přístupu k aplikaci jménem organizace.

## <a name="policy-prevents-granting-permissions-error"></a>Zásada zabraňuje udělení oprávnění chyba
* **AADSTS90093:** Správce &lt;tenanta tenantDisplayName&gt; nastavil zásadu, která vám brání v poskytování &lt;název aplikace&gt; oprávnění vyžaduje. Obraťte se na správce &lt;tenanta tenantDisplayName&gt;, který můžete udělit oprávnění k této aplikaci za vás.

Tato chyba nastane, pokud správce společnosti vypne možnost, aby uživatelé vyjádřili souhlas aplikacím, potom se pokusí použít aplikaci, která vyžaduje souhlas uživatele bez oprávnění správce. Tuto chybu můžete vyřešit microsoftem nebo správcem udělení přístupu k aplikaci jménem organizace.

## <a name="intermittent-problem-error"></a>Chyba přerušovaně docházelo k problému
* **AADSTS90090:** Vypadá to procesu přihlašování došlo k chybě občasný problém záznam jste se pokusili získat udělit oprávnění &lt;clientAppDisplayName&gt;. Zkuste to znovu později.

Tato chyba označuje, že má došlo k chybě na straně přerušované služby. Se dá vyřešit pokusem o souhlas s aplikací znovu.

## <a name="resource-not-available-error"></a>Prostředek není k dispozici – chyba
* **AADSTS65005:** Aplikace &lt;clientAppDisplayName&gt; požadovaná oprávnění pro přístup k prostředku &lt;resourceAppDisplayName&gt; , který není k dispozici. 

Obraťte se na vývojáře aplikace.

##  <a name="resource-not-available-in-tenant-error"></a>Prostředek není dostupný v tenantovi chyba
* **AADSTS65005:** &lt;clientAppDisplayName&gt; žádá o přístup k prostředku &lt;resourceAppDisplayName&gt; , který není k dispozici ve vaší organizaci &lt;tenanta tenantDisplayName &gt;. 

Zajistěte, aby byl tento prostředek k dispozici nebo se obraťte na správce &lt;tenanta tenantDisplayName&gt;.

## <a name="permissions-mismatch-error"></a>Chybová zpráva o neshodě oprávnění
* **AADSTS65005:** Požadovaná aplikace svolení k přístupu k prostředku &lt;resourceAppDisplayName&gt;. Tento požadavek se nezdařil, protože se neshoduje, jak byla předem nakonfigurované aplikace při registraci aplikace. Obraťte se na aplikace vendor.* *

Tyto chyby, které všechny dojít, když aplikace, kterou uživatel se pokouší o souhlas s žádá o oprávnění pro přístup k aplikaci prostředku, který nebyl nalezen v adresáři organizace (tenant). Tato situace může nastat z několika důvodů:

-   Vývojář aplikace klienta má své aplikace správně nakonfigurována, by ji požádáte o přístup k je neplatný prostředek. V takovém případě musí vývojář aplikace aktualizovat konfiguraci klientské aplikace k vyřešení tohoto problému.

-   Instanční objekt služby představuje cílové aplikace prostředků neexistuje v organizaci, nebo existovala v minulosti, ale byla odebrána. K vyřešení tohoto problému, musí být zřízená instančního objektu pro aplikace prostředků v organizaci, aby klientská aplikace můžete požádat o oprávnění k němu. Instanční objekt služby je možné zřídit různými způsoby v závislosti na typu aplikace, včetně:

    -   Získání předplatného pro aplikace prostředků (Microsoft publikované aplikace)

    -   Vyjadřuje se souhlas se aplikace prostředků

    -   Udělení oprávnění aplikace prostřednictvím portálu Azure portal

    -   Přidání aplikace v galerii aplikací Azure AD

## <a name="next-steps"></a>Další postup 

[Aplikace, oprávnění a vyjádření souhlasu v Azure Active Directory (koncového bodu v1)](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent)<br>

[Rozsahy, oprávnění a vyjádření souhlasu v Azure Active Directory (koncový bod v2.0)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)


