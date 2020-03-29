---
title: Seznam kompatibilit pro federaci Azure AD
description: Tato stránka obsahuje jiné zprostředkovatele identit, které lze použít k implementaci jednotného přihlášení.
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
ms.topic: conceptual
ms.date: 08/23/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 54f5090101c486562e33de56402db348c6038c8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60244762"
---
# <a name="azure-ad-federation-compatibility-list"></a>Seznam kompatibilit pro federaci Azure AD
Azure Active Directory poskytuje jednotné přihlašování a rozšířené zabezpečení přístupu k aplikacím pro Office 365 a další služby Microsoft Online pro hybridní a cloudové implementace bez nutnosti řešení třetích stran. Office 365, stejně jako většina online služeb Microsoftu, je integrovaný s Azure Active Directory pro adresářové služby, ověřování a autorizaci. Azure Active Directory také poskytuje jednotné přihlašování k tisícům aplikací SaaS a místních webových aplikací. V [galerii aplikací](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps) Azure Active Directory najdete podporované aplikace SaaS. 

## <a name="idp-validation"></a>Ověření idp
Pokud vaše organizace používá federační řešení třetí strany, můžete nakonfigurovat jednotné přihlašování pro místní uživatele služby Active Directory pomocí služeb Microsoft Online, jako je například Office 365, za předpokladu, že federační řešení třetí strany je kompatibilní s Azure. Active Directory.  Máte-li dotazy týkající se kompatibility, obraťte se na poskytovatele identity.  Pokud chcete zobrazit seznam poskytovatelů identit, kteří byli dříve testováni na kompatibilitu s Azure AD, microsoft, klikněte [sem](https://www.microsoft.com/download/details.aspx?id=56843). 

>[!NOTE]
>Společnost Microsoft již neposkytuje testování ověření kompatibility s azure active directory nezávislými poskytovateli identit. Chcete-li produkt otestovat na interoperabilitu, přečtěte si tyto [pokyny](https://www.microsoft.com/download/details.aspx?id=56843). 

## <a name="next-steps"></a>Další kroky

- [Integrace místních adresářů do služby Azure Active Directory](whatis-hybrid-identity.md)
- [Azure AD Connect a federace](how-to-connect-fed-whatis.md)
