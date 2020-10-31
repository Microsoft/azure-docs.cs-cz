---
title: Co je zřizování na základě lidských zdrojů pomocí Azure Active Directory? | Dokumentace Microsoftu
description: Popisuje přehled zřizování na základě lidských zdrojů.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 10/30/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45b8ed75a87d1ac638e5ca55058a3d1ad7fcdffa
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93135321"
---
# <a name="what-is-hr-driven-provisioning"></a>Co je zřizování na základě lidských zdrojů?

![Zřizování lidských zdrojů](./media/what-is-hr-driven-provisioning/cloud2a.png)

Zřizování na základě lidských zdrojů je proces vytváření digitálních identit založený na systému lidských zdrojů.  Systémy HR se stanou spouštěcími autoritami pro tyto nově vytvořené digitální identity a často se jedná o výchozí bod pro četné procesy zřizování.  Například pokud se nový zaměstnanec připojí do vaší společnosti, vytvoří se v systému lidských zdrojů.  Vytvoření, aktivuje zřizování uživatelského účtu ve službě Active Directory a poté Azure AD Connect zřídí tento účet do služby Azure AD atd.

Zřizování na základě lidských zdrojů může být založené na místním nebo cloudovém prostředí.

## <a name="on-premises-based-hr-provisioning"></a>Zřizování na základě místních lidských zdrojů
Místní zřizování na základě lidských zdrojů se dosahuje pomocí místního systému pro personální prostředí a prostředků pro zřizování nových digitálních identit.

Systémy PERSONÁLního oddělení pocházejí z nejrůznějších balíčků, softwarových sad a můžou používat SQL servery, adresáře LDAP atd.

V současné době řešení zajišťování místních lidských zdrojů v rámci Microsoftu používají Microsoft Identity Manager k aktivaci zřizování, když se v těchto systémech pro personál vytvoří nová identita.

Pomocí MIM můžete zřídit uživatele z vašich místních systémů pro personální prostředí se službou Active Directory nebo Azure AD.

Informace o Microsoft Identity Manager a systémech, které podporuje, najdete v dokumentaci k [Microsoft Identity Manager](https://docs.microsoft.com/microsoft-identity-manager/microsoft-identity-manager-2016) .

[!INCLUDE [active-directory-hr-provisioning.md](../../../includes/active-directory-hr-provisioning.md)]



## <a name="next-steps"></a>Další kroky 
- [Co je Správa životního cyklu identit](what-is-identity-lifecycle-management.md)
- [Co je zřizování?](what-is-provisioning.md)
- [Co je zřizování aplikací?](what-is-app-provisioning.md)
- [Co je mezi adresářovým zřizováním?](what-is-inter-directory-provisioning.md)
- [Co je zřizování adresáře?](what-is-inter-directory-provisioning.md)