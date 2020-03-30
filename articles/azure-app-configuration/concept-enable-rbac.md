---
title: Autorizace přístupu ke konfiguraci aplikací Azure pomocí Služby Azure Active Directory
description: Povolení autorizace přístupu k instanci konfigurace aplikace Azure RBAC
author: lisaguthrie
ms.author: lcozzens
ms.date: 02/13/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 18fa1b60b15b7eef96efa8dcc4fbf9cd7c4dc7f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77472619"
---
# <a name="authorize-access-to-azure-app-configuration-using-azure-active-directory"></a>Autorizace přístupu ke konfiguraci aplikací Azure pomocí Služby Azure Active Directory
Azure App Configuration podporuje použití Azure Active Directory (Azure AD) k autorizaci požadavků na instance konfigurace aplikací.  Azure AD umožňuje použít řízení přístupu na základě rolí (RBAC) udělit oprávnění k objektu zabezpečení.  Zaregistrovaný objekt zabezpečení může být uživatel nebo [instanční objekt služby aplikace](../active-directory/develop/app-objects-and-service-principals.md).  Další informace o rolích a přiřazeních rolí najdete v [tématu Principy různých rolí](../role-based-access-control/overview.md).

## <a name="overview"></a>Přehled
Požadavky provedené objektu zabezpečení (uživatelem nebo aplikací) pro přístup k prostředku konfigurace aplikace musí být autorizovány.  S Azure AD, přístup k prostředku je dvoustupňový proces.
1. Identita objektu zabezpečení je ověřena a je vrácen token OAuth 2.0.  Název prostředku pro vyžádání `https://login.microsoftonline.com/{tenantID}` tokenu je, kde `{tenantID}` odpovídá ID klienta služby Azure Active Directory, do kterého patří instanční objekt.
2. Token je předán jako součást požadavku na službu Konfigurace aplikace autorizovat přístup k zadanému prostředku.

Krok ověřování vyžaduje, aby požadavek aplikace obsahoval přístupový token OAuth 2.0 za běhu.  Pokud aplikace běží v rámci entity Azure, jako je například aplikace Azure Functions, Azure Web App nebo virtuální počítač Azure, můžete použít spravovanou identitu pro přístup k prostředkům.  Informace o tom, jak ověřovat požadavky spravované identity na azure app configuration, najdete [v tématu Ověřování přístupu k prostředkům konfigurace aplikací Azure pomocí Azure Active Directory a spravovaných identit pro prostředky Azure](howto-integrate-azure-managed-service-identity.md).

Krok autorizace vyžaduje, aby byla k objektu zabezpečení přiřazena jedna nebo více rolí RBAC. Azure App Configuration poskytuje role RBAC, které zahrnují sady oprávnění pro prostředky konfigurace aplikace. Role, které jsou přiřazeny k objektu zabezpečení určit oprávnění poskytnutá objektu zabezpečení. Další informace o rolích RBAC najdete [v tématu předdefinované role RBAC pro konfiguraci aplikací Azure](#built-in-rbac-roles-for-azure-app-configuration). 

## <a name="assign-rbac-roles-for-access-rights"></a>Přiřazení rolí RBAC pro přístupová práva
Azure Active Directory (Azure AD) autorizuje přístupová práva k zabezpečeným prostředkům prostřednictvím [řízení přístupu na základě rolí (RBAC).](../role-based-access-control/overview.md)

Když je role RBAC přiřazena k objektu zabezpečení Azure AD, Azure uděluje přístup k těmto prostředkům pro tento objekt zabezpečení. Přístup je vymezen k prostředku konfigurace aplikace. Zaregistrovaný objekt zabezpečení Azure AD může být uživatel nebo instanční objekt služby aplikace nebo [spravovaná identita pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="built-in-rbac-roles-for-azure-app-configuration"></a>Integrované role RBAC pro konfiguraci aplikací Azure
Azure poskytuje následující integrované role RBAC pro autorizaci přístupu k datům konfigurace aplikací pomocí Azure AD a OAuth:

- Vlastník konfiguračních dat aplikace Azure: Pomocí této role můžete udělit přístup pro čtení a zápis k prostředkům konfigurace aplikace.
- Azure App Configuration Data Reader: Tato role slouží k poskytnutí přístupu pro čtení k prostředkům konfigurace aplikace.
- Přispěvatel: Tuto roli použijte k udělení přístupu správce ke službě bez udělení přístupu k datům uloženým v instanci Konfigurace aplikace.

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o používání [spravovaných identit](howto-integrate-azure-managed-service-identity.md) ke správě služby Konfigurace aplikací.