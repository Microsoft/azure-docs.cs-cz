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
ms.openlocfilehash: 895e7347feea3190ba9bdc273200d2985dfaa0d1
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2020
ms.locfileid: "89661817"
---
# <a name="azure-ad-federation-compatibility-list"></a>Seznam kompatibilit pro federaci Azure AD
Azure Active Directory poskytuje jednotné přihlašování a vylepšené zabezpečení přístupu k aplikacím pro Microsoft 365 a další online služby Microsoftu pro hybridní a cloudové implementace bez nutnosti řešení třetích stran. Microsoft 365, jako je většina online služeb Microsoftu, je integrovaná do Azure Active Directory pro adresářové služby, ověřování a autorizaci. Azure Active Directory taky poskytuje jednotné přihlašování k tisícům aplikací SaaS a místních webových aplikací. Podporované aplikace SaaS najdete v [galerii aplikací](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps) Azure Active Directory. 

## <a name="idp-validation"></a>IDP ověření
Pokud vaše organizace používá řešení federace třetí strany, můžete nakonfigurovat jednotné přihlašování pro místní uživatele služby Active Directory s online službami Microsoftu, jako je například Microsoft 365, za předpokladu, že řešení federace třetí strany je kompatibilní s Azure Active Directory.  Pokud máte otázky týkající se kompatibility, obraťte se prosím na svého poskytovatele identity.  Pokud se chcete podívat na seznam zprostředkovatelů identity, kteří už dříve otestovali kompatibilitu se službou Azure AD, najdete je v dokumentaci k [kompatibilitě zprostředkovatele identity Azure AD](https://www.microsoft.com/download/details.aspx?id=56843). 

>[!NOTE]
>Microsoft už neposkytuje ověřovací testování pro nezávislé zprostředkovatele identity kvůli kompatibilitě s Azure Active Directory. Pokud chcete svůj produkt testovat pro interoperabilitu, přečtěte si tyto [pokyny](https://www.microsoft.com/download/details.aspx?id=56843). 

## <a name="next-steps"></a>Další kroky

- [Integrace místních adresářů do služby Azure Active Directory](whatis-hybrid-identity.md)
- [Azure AD Connect a federace](how-to-connect-fed-whatis.md)
