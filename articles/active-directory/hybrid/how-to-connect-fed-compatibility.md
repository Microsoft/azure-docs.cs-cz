---
title: Seznam kompatibilit pro federaci Azure AD
description: Tato stránka obsahuje zprostředkovatelé identity jiných společností než Microsoft, které lze použít k implementaci jednotného přihlašování.
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
ms.topic: article
ms.date: 08/23/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 56447f04e63c588e2192d350ce9188f8a15c604b
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54464727"
---
# <a name="azure-ad-federation-compatibility-list"></a>Seznam kompatibilit pro federaci Azure AD
Azure Active Directory poskytuje jednotné přihlašování na a zvýšené zabezpečení přístupu aplikací pro Office 365 a dalším službám Microsoft Online pro implementace čistě cloudové a hybridní bez nutnosti jakékoli řešení třetí strany. Office 365, oblíbili Online službách společnosti Microsoft, je integrovaná se službou Azure Active Directory pro adresářové služby, ověřování a autorizace. Azure Active Directory také poskytuje jednotné přihlašování do tisícovek aplikací SaaS a místních webových aplikací. Azure Active Directory najdete v článku [Galerie aplikací](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps) pro aplikace SaaS podporované. 

## <a name="idp-validation"></a>Ověření zprostředkovatele identity
Pokud vaše organizace používá řešení federace třetích stran, můžete nakonfigurovat jednotné přihlašování pro uživatele v místním Active Directory pomocí služeb Microsoft Online, jako je Office 365, pokud řešení federace třetích stran je kompatibilní s Azure Active Directory.  Máte dotazy týkající se kompatibility obraťte se prosím na svého poskytovatele identity.  Pokud chcete zobrazit seznam zprostředkovatelů identity, kteří dříve jsme otestovali kompatibilitu s Azure AD, společnost Microsoft, klepněte na tlačítko [tady](https://www.microsoft.com/download/details.aspx?id=56843). 

>[!NOTE]
>Microsoft už poskytuje ověřovací testování ke zprostředkovatelům identity nezávislé pro kompatibilitu s Azure Active Directory. Pokud chcete otestovat svůj produkt pro spolupráci prosím použijte tyto [pokyny](https://www.microsoft.com/download/details.aspx?id=56843). 

## <a name="next-steps"></a>Další kroky

- [Integrace místních adresářů do služby Azure Active Directory](whatis-hybrid-identity.md)
- [Azure AD Connect a federace](how-to-connect-fed-whatis.md)
