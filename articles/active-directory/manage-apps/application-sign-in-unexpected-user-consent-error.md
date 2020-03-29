---
title: Neočekávaná chyba při provádění souhlasu s žádostí | Dokumenty společnosti Microsoft
description: Popisuje chyby, ke kterým může dojít během procesu souhlasu s aplikací, a co s nimi můžete dělat.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6dff3be9a9bc7fd897f340e5fe6a4775a4914810
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "65824944"
---
# <a name="unexpected-error-when-performing-consent-to-an-application"></a>Neočekávaná chyba při provádění souhlasu s aplikací

Tento článek popisuje chyby, ke kterým může dojít během procesu souhlasu s aplikací. Pokud řešíte neočekávané výzvy k souhlasu, které neobsahují žádné chybové zprávy, přečtěte si článek [Scénáře ověřování pro Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-scenarios).

Mnoho aplikací, které se integrují se službou Azure Active Directory, vyžaduje oprávnění pro přístup k jiným prostředkům, aby fungovaly. Pokud jsou tyto prostředky také integrované se službou Azure Active Directory, oprávnění k přístupu k nim se často požaduje pomocí rámce společného souhlasu. Zobrazí se výzva k souhlasu, ke které obvykle dochází při prvním použití aplikace, ale může dojít také při následném použití aplikace.

Pro uživatele musí být splněny určité podmínky, aby mohl souhlasit s oprávněními, která aplikace vyžaduje. Pokud tyto podmínky nejsou splněny, může dojít k následujícím chybám.

## <a name="requesting-not-authorized-permissions-error"></a>Požadavek na chybu neautorizovaných oprávnění
* **AADSTS90093:** &lt;clientAppDisplayName&gt; požaduje jedno nebo více oprávnění, která nejste oprávněni udělit. Obraťte se na správce, který může souhlasit s touto aplikací vaším jménem.

K této chybě dochází, když se uživatel, který není správcem společnosti, pokusí použít aplikaci, která požaduje oprávnění, která může udělit pouze správce. Tuto chybu lze vyřešit správcem, který uděluje přístup k aplikaci jménem své organizace.

## <a name="policy-prevents-granting-permissions-error"></a>Zásada zabraňuje chybě udělení oprávnění.
* **AADSTS90093:** Správce &lt;tenantDisplayName&gt; nastavil zásadu, která zabraňuje udělení &lt;názvu&gt; aplikace oprávnění, která požaduje. Obraťte se &lt;na&gt;správce tenantdisplayname , který může udělit oprávnění k této aplikaci vaším jménem.

K této chybě dochází, když správce společnosti vypne možnost, aby uživatelé uchylovat k aplikacím, pak uživatel bez správce pokusí použít aplikaci, která vyžaduje souhlas. Tuto chybu lze vyřešit správcem, který uděluje přístup k aplikaci jménem své organizace.

## <a name="intermittent-problem-error"></a>Občasná chyba problému
* **AADSTS90090:** Vypadá to, že proces přihlášení došlo k občasnému problému se &lt;záznamem oprávnění, která jste se pokusili udělit clientAppDisplayName&gt;. opakujte akci později.

Tato chyba označuje, že došlo k občasnému problému na straně služby. To lze vyřešit pokusem o souhlas s aplikací znovu.

## <a name="resource-not-available-error"></a>Chyba zdroje není k dispozici
* **AADSTS65005:** Klient &lt;aplikaceAppDisplayName&gt; si vyžádal oprávnění &lt;pro přístup&gt; k prostředku AppDisplayName, který není k dispozici. 

Obraťte se na vývojáře aplikace.

##  <a name="resource-not-available-in-tenant-error"></a>Prostředek není k dispozici v chybě klienta
* **AADSTS65005:** &lt;&gt; clientAppDisplayName požaduje přístup k &lt;prostředku ProstředkuAppDisplayName,&gt; který &lt;není k&gt;dispozici ve vaší organizaci tenantDisplayName . 

Ujistěte se, že tento prostředek &lt;je&gt;k dispozici, nebo obraťte se na správce tenantDisplayName .

## <a name="permissions-mismatch-error"></a>Chyba neshody oprávnění
* **AADSTS65005:** Aplikace požádala o &lt;souhlas s&gt;přístupem k prostředku ResourceDisplayName . Tento požadavek se nezdařil, protože neodpovídá tomu, jak byla aplikace předkonfigurována během registrace aplikace. Obraťte se na dodavatele aplikace.**

Tyto chyby všechny dojít, když aplikace uživatel se snaží souhlas it žádá o oprávnění pro přístup k aplikaci prostředků, které nelze nalézt v adresáři organizace (tenanta). Tato situace může nastat z několika důvodů:

-   Vývojář klientské aplikace nakonfiguroval svou aplikaci nesprávně, což způsobilo, že požádal o přístup k neplatnému prostředku. V takovém případě musí vývojář aplikace aktualizovat konfiguraci klientské aplikace, aby tento problém vyřešil.

-   Instanční objekt představující cílovou aplikaci prostředků neexistuje v organizaci nebo existoval v minulosti, ale byl odebrán. Chcete-li tento problém vyřešit, instanční objekt pro aplikaci prostředků musí být zřízena v organizaci, aby klientská aplikace může požádat o oprávnění k němu. Instanční objekt služby lze zřídit několika způsoby, v závislosti na typu aplikace, včetně:

    -   Získání předplatného pro aplikaci prostředků (aplikace publikované společností Microsoft)

    -   Souhlas s aplikací prostředků

    -   Udělení oprávnění k aplikaci prostřednictvím portálu Azure

    -   Přidání aplikace z Galerie aplikací Azure AD

## <a name="next-steps"></a>Další kroky 

[Aplikace, oprávnění a souhlas ve službě Azure Active Directory (koncový bod v1)](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent)<br>

[Obory, oprávnění a souhlas ve službě Azure Active Directory (koncový bod v2.0)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)


