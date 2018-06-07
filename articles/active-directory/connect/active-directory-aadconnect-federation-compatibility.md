---
title: Seznam kompatibilit pro federaci Azure AD
description: Tato stránka obsahuje poskytovatelů identit třetích stran, které lze použít k implementaci jednotného přihlašování.
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
ms.date: 04/27/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: f5e85a551181a3dd4618db2611a99675329cfabd
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34592004"
---
# <a name="azure-ad-federation-compatibility-list"></a>Seznam kompatibilit pro federaci Azure AD
Azure Active Directory poskytuje jednotné přihlašování a rozšířené zabezpečení přístupu aplikace pro Office 365 a dalším službám Microsoft Online pro implementace jenom pro cloud a hybridní bez nutnosti jakékoli řešení třetí strany. Office 365, jako je většina Online službách společnosti Microsoft, je integrovaná do Azure Active Directory directory services, ověřování a autorizace. Jednotné přihlašování k tisícům aplikací SaaS také poskytuje Azure Active Directory a místní webové aplikace. Azure Active Directory najdete v části [galerii aplikací](https://azuremarketplace.microsoft.com/marketplace/apps) podporovaných aplikací SaaS. 

## <a name="idp-validation"></a>Ověřování deklarací identity
Pokud vaše organizace používá řešení federování třetích stran, můžete nakonfigurovat jednotné přihlašování pro vaše uživatele místní služby Active Directory s služeb Microsoft Online services, např. Office 365, zadaný řešení federování třetích stran, které je kompatibilní s Azure Chcete-li služby Active Directory.  Otázky ohledně kompatibility obraťte se na svého poskytovatele identity.  Pokud chcete zobrazit seznam zprostředkovatelů identity, kteří dříve byly testovány pro kompatibilitu s Azure AD, společnost Microsoft, klikněte na tlačítko [zde](https://www.microsoft.com/download/details.aspx?id=56843). 

>[!NOTE]
>Společnost Microsoft poskytuje už ověření testování do zprostředkovatelů nezávislou identitu pro kompatibilitu s Azure Active Directory. Pokud chcete otestovat svůj produkt funkční spolupráce naleznete v těchto [pokyny](https://www.microsoft.com/download/details.aspx?id=56843). 

## <a name="next-steps"></a>Další kroky

- [Integrace místních adresářů do služby Azure Active Directory](active-directory-aadconnect.md)
- [Azure AD Connect a federace](active-directory-aadconnectfed-whatis.md)
