---
title: Seznam kompatibilit pro federaci Azure AD
description: Tato stránka obsahuje zprostředkovatelé identity jiných společností než Microsoft, které lze použít k implementaci jednotného přihlašování.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: curtand
ms.assetid: 22c8693e-8915-446d-b383-27e9587988ec
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/23/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 3b2313b79c57a95af40d29bca3d7522c83e10e4c
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/24/2018
ms.locfileid: "42818710"
---
# <a name="azure-ad-federation-compatibility-list"></a>Seznam kompatibilit pro federaci Azure AD
Azure Active Directory poskytuje jednotné přihlašování na a zvýšené zabezpečení přístupu aplikací pro Office 365 a dalším službám Microsoft Online pro implementace čistě cloudové a hybridní bez nutnosti jakékoli řešení třetí strany. Office 365, oblíbili Online službách společnosti Microsoft, je integrovaná se službou Azure Active Directory pro adresářové služby, ověřování a autorizace. Azure Active Directory také poskytuje jednotné přihlašování do tisícovek aplikací SaaS a místních webových aplikací. Azure Active Directory najdete v článku [Galerie aplikací](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps) pro aplikace SaaS podporované. 

## <a name="idp-validation"></a>Ověření zprostředkovatele identity
Pokud vaše organizace používá řešení federace třetích stran, můžete nakonfigurovat jednotné přihlašování pro uživatele v místním Active Directory pomocí služeb Microsoft Online, jako je Office 365, pokud řešení federace třetích stran je kompatibilní s Azure Active Directory.  Máte dotazy týkající se kompatibility obraťte se prosím na svého poskytovatele identity.  Pokud chcete zobrazit seznam zprostředkovatelů identity, kteří dříve jsme otestovali kompatibilitu s Azure AD, společnost Microsoft, klepněte na tlačítko [tady](https://www.microsoft.com/download/details.aspx?id=56843). 

>[!NOTE]
>Microsoft už poskytuje ověřovací testování ke zprostředkovatelům identity nezávislé pro kompatibilitu s Azure Active Directory. Pokud chcete otestovat svůj produkt pro spolupráci prosím použijte tyto [pokyny](https://www.microsoft.com/download/details.aspx?id=56843). 

## <a name="next-steps"></a>Další kroky

- [Integrace místních adresářů do služby Azure Active Directory](active-directory-aadconnect.md)
- [Azure AD Connect a federace](active-directory-aadconnectfed-whatis.md)
