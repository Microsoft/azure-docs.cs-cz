---
title: Seznam kompatibilit pro federaci Azure AD
description: Tato stránka obsahuje poskytovatele identity od jiných dodavatelů než od Microsoftu, které se dají použít k implementaci jednotného přihlašování.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: 22c8693e-8915-446d-b383-27e9587988ec
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/23/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f3ca659535c225ca6736fe7bba8aa5b0120c684c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85359819"
---
# <a name="azure-ad-federation-compatibility-list"></a>Seznam kompatibilit pro federaci Azure AD
Azure Active Directory poskytuje jednotné přihlašování a vylepšené zabezpečení přístupu k aplikacím pro Office 365 a další online služby Microsoftu pro hybridní a cloudové implementace bez nutnosti řešení třetích stran. Sada Office 365, stejně jako většina online služeb společnosti Microsoft, je integrovaná s Azure Active Directory pro adresářové služby, ověřování a autorizaci. Azure Active Directory taky poskytuje jednotné přihlašování k tisícům aplikací SaaS a místních webových aplikací. Podporované aplikace SaaS najdete v [galerii aplikací](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps) Azure Active Directory. 

## <a name="idp-validation"></a>IDP ověření
Pokud vaše organizace používá řešení federace třetí strany, můžete nakonfigurovat jednotné přihlašování pro místní uživatele služby Active Directory pomocí online služeb Microsoftu, jako je například Office 365, a to za předpokladu, že řešení federace třetí strany je kompatibilní s Azure Active Directory.  Pokud máte otázky týkající se kompatibility, obraťte se prosím na svého poskytovatele identity.  Pokud se chcete podívat na seznam zprostředkovatelů identity, kteří už dříve otestovali kompatibilitu s Azure AD, klikněte na [sem](https://www.microsoft.com/download/details.aspx?id=56843). 

>[!NOTE]
>Microsoft už neposkytuje ověřovací testování pro nezávislé zprostředkovatele identity kvůli kompatibilitě s Azure Active Directory. Pokud chcete svůj produkt testovat pro interoperabilitu, přečtěte si tyto [pokyny](https://www.microsoft.com/download/details.aspx?id=56843). 

## <a name="next-steps"></a>Další kroky

- [Integrace místních adresářů do služby Azure Active Directory](whatis-hybrid-identity.md)
- [Azure AD Connect a federace](how-to-connect-fed-whatis.md)
