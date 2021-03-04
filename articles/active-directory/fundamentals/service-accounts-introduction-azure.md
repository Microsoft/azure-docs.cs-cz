---
title: Úvod k zabezpečení účtů služby Azure Active Directory
description: Vysvětlení typů účtů služeb dostupných v Azure Active Directory.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 3/1/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 049025a5d871f1dd26e5dab498756aa44d2ebfe2
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101693071"
---
# <a name="introduction-to-securing-azure-service-accounts"></a>Úvod k zabezpečení účtů služeb Azure

Existují tři typy účtů služeb, které jsou nativně Azure Active Directory: spravované identity, instanční objekty a účty služeb založené na uživatelích. Účty služeb jsou speciálním typem účtu, který je určený k zastupování nelidské entity, jako je například aplikace, rozhraní API nebo jiná služba. Tyto entity pracují v rámci kontextu zabezpečení poskytnutého účtem služby. 

## <a name="types-of-azure-active-directory-service-accounts"></a>Typy účtů Azure Active Directory služby

Pro služby hostované v Azure doporučujeme používat spravovanou identitu, pokud je to možné, a instanční objekt, pokud ne. Spravované identity se nedají použít pro služby hostované mimo Azure. V takovém případě doporučujeme instanční objekt. Pokud můžete použít spravovanou identitu nebo instanční objekt, udělejte to. Nedoporučujeme používat uživatelský účet Azure Active Directory jako instanční objekt. Souhrn najdete v následující tabulce.
 

| Hostování služeb| Spravovaná identita| Instanční objekt| Uživatelský účet Azure |
| - | - | - | - |
|Služba je hostovaná v Azure.| Ano. <br>Doporučuje se, pokud služba <br>podporuje spravovanou identitu.| Ano.| Nedoporučuje se. |
| Služba není hostovaná v Azure.| No| Yes. Doporučil.| Nedoporučuje se. |
| Služba je více tenantů.| No| Yes. Doporučil.| No. |


## <a name="managed-identities"></a>Spravované identity

Spravované identity jsou identity zabezpečené Azure Active Directory (Azure AD) vytvořené za účelem poskytování identit pro prostředky Azure. Existují [dva typy spravovaných identit](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview#managed-identity-types): 
 
* Spravované identity přiřazené systémem se dají přiřadit přímo k instanci služby. 

* Spravované identity přiřazené uživatelem je možné vytvořit jako samostatný prostředek. 

Další informace najdete v tématu [zabezpečení spravovaných identit](service-accounts-managed-identities.md). Obecné informace o spravovaných identitách najdete v tématu [co jsou spravované identity pro prostředky Azure?](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

## <a name="service-principals"></a>Instanční objekty

Pokud nemůžete použít spravovanou identitu k reprezentaci vaší aplikace, použijte instanční objekt. Instanční objekty lze použít současně s jedním klientem i s více klienty aplikace. 

Instanční objekt je místní reprezentace objektu aplikace v jednom tenantovi služby Azure AD. Funguje jako identita instance aplikace, definuje, kdo má přístup k aplikaci a k jakým prostředkům může aplikace přistupovat. Instanční objekt se vytvoří v (místní) pro každého tenanta, kde se aplikace používá, a odkazuje na globálně jedinečný objekt aplikace. Tenant zabezpečuje přihlašování a přístup k prostředkům objektu služby.

Existují dva mechanismy ověřování pomocí instančních objektů – klientské certifikáty a tajné klíče klienta. Certifikáty jsou bezpečnější: Pokud je to možné, používejte klientské certifikáty. Na rozdíl od klientských tajných klíčů nelze klientské certifikáty omylem vkládat do kódu.

Informace o zabezpečení instančních objektů najdete v tématu zabezpečení instančních objektů.

 
## <a name="next-steps"></a>Další kroky


Další informace o zabezpečení účtů služeb Azure najdete v těchto tématech:

[Zabezpečení spravovaných identit](service-accounts-managed-identities.md)

[Zabezpečení instančních objektů](service-accounts-principal.md)

[Řízení účtů služeb Azure](service-accounts-governing-azure.md)



