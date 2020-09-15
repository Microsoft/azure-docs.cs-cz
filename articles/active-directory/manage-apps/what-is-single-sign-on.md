---
title: Co je jednotné přihlašování Azure (SSO)?
description: Přečtěte si, jak jednotné přihlašování (SSO) funguje s Azure Active Directory. Použijte jednotné přihlašování, aby uživatelé nemuseli pamatovat hesla pro každou aplikaci. K zjednodušení správy správy účtů taky používejte jednotné přihlašování.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: overview
ms.date: 12/03/2019
ms.author: kenwith
ms.reviewer: arvindh, japere
ms.custom: contperfq1
ms.openlocfilehash: 1eaef57f46bf6373fdd2a73575bb028904ef108b
ms.sourcegitcommit: 6e1124fc25c3ddb3053b482b0ed33900f46464b3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90561646"
---
# <a name="what-is-single-sign-on-sso"></a>Co je jednotné přihlašování (SSO)?

Jednotné přihlašování znamená, že se uživatel nemusí přihlašovat ke každé aplikaci, kterou používají. Uživatel se přihlásí jednou a přihlašovací údaje se používají i pro jiné aplikace.

Pokud jste koncový uživatel, pravděpodobně nezáleží na podrobnostech jednotného přihlašování. Chcete používat jenom aplikace, které vám pomůžou být produktivní bez nutnosti zadávat heslo. Své aplikace můžete najít na adrese: https://myapps.microsoft.com .
 
Pokud jste správcem nebo IT specialistem, přečtěte si článek, kde najdete další informace o jednotném přihlašování a způsobu jeho implementace v Azure.

## <a name="single-sign-on-basics"></a>Základy jednotného přihlašování
Jednotné přihlašování poskytuje obří přestupnější informace o tom, jak se uživatelé přihlásí a používají aplikace. Systémy ověřování založené na jednotném přihlašování se často nazývají moderní ověřování. Moderní ověřování a jednotné přihlašování spadají do kategorie výpočetní aplikace s názvem Správa identit a přístupu (IAM). Pokud chcete pochopit, co je možné jednotné přihlašování, podívejte se na toto video.

Základy ověřování: Základy | Azure Active Directory

> [!VIDEO https://www.youtube.com/embed/fbSVgC8nGz4]

## <a name="single-sign-on-with-web-applications"></a>Jednotné přihlašování s webovými aplikacemi
Webové aplikace jsou neuvěřitelně oblíbené. Webové aplikace jsou hostovány různými společnostmi a zpřístupňují se jako služba. Mezi oblíbené příklady webových aplikací patří Microsoft 365, GitHub a Salesforce a existují tisíce dalších. Lidé přistupují k webovým aplikacím pomocí webového prohlížeče na svém počítači. Jednotné přihlašování umožňuje lidem přecházet mezi různými webovými aplikacemi, aniž by se museli přihlašovat víckrát.

Další informace o tom, jak jednotné přihlašování funguje s webovými aplikacemi, najdete v těchto dvou videích.

Základy ověřování: webové aplikace | Azure Active Directory

> [!VIDEO https://www.youtube.com/embed/tCNcG1lcCHY]

Základy ověřování: webové jednotné přihlašování | Azure Active Directory

> [!VIDEO https://www.youtube.com/embed/51B-jSOBF8U]

## <a name="cloud-versus-on-premises-hosted-apps"></a>Cloudové a místní hostované aplikace
Způsob implementace jednotného přihlašování závisí na tom, kde je aplikace hostovaná. Hostování v důsledku způsobu, jakým se směruje síťový provoz pro přístup k aplikaci. Pokud je aplikace hostovaná a přístupná přes místní síť, která se nazývá místní aplikace, není potřeba, aby uživatelé měli přístup k Internetu, aby mohli aplikaci používat. Pokud je aplikace hostována jinde, označovanou jako cloudová hostovaná aplikace, uživatelé budou potřebovat přístup k Internetu, aby mohli aplikaci používat.

> [!TIP]
> Aplikace hostované v cloudu se označují také jako aplikace SaaS (software jako služba). 

Jednotné přihlašování pro aplikace hostované v cloudu je jednoduché. Sdělte poskytovateli identity, který ví, že se používá pro aplikaci. A potom nakonfigurujete aplikaci tak, aby důvěřovala zprostředkovateli identity. Informace o tom, jak používat Azure AD jako zprostředkovatele identity pro aplikaci, najdete v části [rychlý start série při správě aplikací](add-application-portal.md).

> [!TIP]
> Pojem Cloud a Internet se často používají k zaměnitelné. Důvodem je to, že se v diagramech sítě musí dělat. Je běžné, že velké počítačové sítě s obrazcem cloudu v diagramu jsou neproveditelné, protože není možné nakreslit všechny součásti. Internet je nejužitečnější síť, takže je snadné je snadno použít. Nicméně jakákoli počítačová síť může být mince v cloudu.

Pro místní aplikace založené na aplikaci můžete použít také jednotné přihlašování. Technologie, která se stane místním přihlašováním, se nazývá proxy aplikace. Další informace o této službě najdete v tématu [Možnosti jednotného přihlašování](sso-options.md).

## <a name="multiple-identity-providers"></a>Více zprostředkovatelů identity
Když nastavíte jednotné přihlašování pro práci mezi více zprostředkovateli identity, nazývá se to federace. Pokud se chcete dozvědět, jak federace funguje, podívejte se na toto video.

Základy ověřování: federace | Azure Active Directory

> [!VIDEO https://www.youtube.com/embed/CjarTgjKcX8]


## <a name="next-steps"></a>Další kroky
* [Série rychlý Start při správě aplikací](view-applications-portal.md)
* [Možnosti jednotného přihlašování](sso-options.md)
