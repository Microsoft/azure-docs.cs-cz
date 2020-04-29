---
title: Autorizace přístupu ke konfiguraci aplikace Azure pomocí Azure Active Directory
description: Povolit RBAC autorizaci přístupu ke konfigurační instanci aplikace Azure
author: lisaguthrie
ms.author: lcozzens
ms.date: 02/13/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 18fa1b60b15b7eef96efa8dcc4fbf9cd7c4dc7f7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "77472619"
---
# <a name="authorize-access-to-azure-app-configuration-using-azure-active-directory"></a>Autorizace přístupu ke konfiguraci aplikace Azure pomocí Azure Active Directory
Konfigurace aplikací Azure podporuje použití Azure Active Directory (Azure AD) k autorizaci požadavků na instance konfigurace aplikace.  Azure AD umožňuje použít řízení přístupu na základě role (RBAC) k udělení oprávnění objektu zabezpečení.  Objekt zabezpečení může být uživatel nebo [instanční objekt aplikace](../active-directory/develop/app-objects-and-service-principals.md).  Další informace o rolích a přiřazení rolí najdete v tématu [porozumění různým rolím](../role-based-access-control/overview.md).

## <a name="overview"></a>Přehled
Požadavky vytvořené objektem zabezpečení (uživatelem nebo aplikací) pro přístup k prostředku konfigurace aplikace musí být autorizovány.  S Azure AD je přístup k prostředku v procesu se dvěma kroky.
1. Identita objektu zabezpečení je ověřena a je vrácen token OAuth 2,0.  Název prostředku pro vyžádání tokenu je `https://login.microsoftonline.com/{tenantID}` , kde `{tenantID}` odpovídá ID tenanta Azure Active Directory, do kterého objekt služby patří.
2. Token se předává jako součást požadavku službě konfigurace aplikace k autorizaci přístupu k zadanému prostředku.

Krok ověřování vyžaduje, aby žádost o aplikaci obsahovala přístupový token OAuth 2,0 za běhu.  Pokud je aplikace spuštěná v rámci entity Azure, jako je Azure Functions aplikace, Webová aplikace Azure nebo virtuální počítač Azure, může k přístupu k prostředkům použít spravovanou identitu.  Informace o tom, jak ověřit požadavky provedené spravovanou identitou do konfigurace aplikace Azure, najdete v tématu [ověření přístupu k prostředkům konfigurace aplikace Azure pomocí Azure Active Directory a spravovaných identit pro prostředky Azure](howto-integrate-azure-managed-service-identity.md).

Autorizační krok vyžaduje, aby se k objektu zabezpečení přiřadila jedna nebo více rolí RBAC. Konfigurace aplikací Azure poskytuje role RBAC, které zahrnují sady oprávnění pro prostředky konfigurace aplikací. Role, které jsou přiřazeny objektu zabezpečení, určují oprávnění poskytnutá objektu zabezpečení. Další informace o rolích RBAC najdete v tématu [předdefinované role RBAC pro konfiguraci aplikací Azure](#built-in-rbac-roles-for-azure-app-configuration). 

## <a name="assign-rbac-roles-for-access-rights"></a>Přiřazení rolí RBAC pro přístupová práva
Azure Active Directory (Azure AD) autorizuje přístupová práva k zabezpečeným prostředkům prostřednictvím [řízení přístupu na základě role (RBAC)](../role-based-access-control/overview.md).

Když je role RBAC přiřazená k objektu zabezpečení Azure AD, poskytuje Azure přístup k těmto prostředkům pro daný objekt zabezpečení. Přístup je omezený na prostředek konfigurace aplikace. Objekt zabezpečení služby Azure AD může být uživatel nebo instanční objekt nebo [spravovaná identita pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="built-in-rbac-roles-for-azure-app-configuration"></a>Předdefinované role RBAC pro konfiguraci aplikací Azure
Azure poskytuje následující předdefinované role RBAC pro autorizaci přístupu ke konfiguračním datům aplikací pomocí Azure AD a OAuth:

- Vlastník dat konfigurace aplikace Azure: Tato role slouží k udělení přístupu pro čtení a zápis k prostředkům konfigurace aplikace.
- Čtečka dat pro Azure App Configuration: tuto roli použijte, pokud chcete udělit přístup pro čtení k prostředkům konfigurace aplikace.
- Přispěvatel: pomocí této role udělte správci přístup ke službě bez udělení přístupu k datům uloženým v instanci konfigurace aplikace.

## <a name="next-steps"></a>Další kroky
Další informace o použití [spravovaných identit](howto-integrate-azure-managed-service-identity.md) ke správě služby konfigurace aplikací.