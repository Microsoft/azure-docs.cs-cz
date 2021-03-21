---
title: Autorizace přístupu ke konfiguraci aplikace Azure pomocí Azure Active Directory
description: Povolení služby Azure RBAC pro autorizaci přístupu ke konfigurační instanci aplikace Azure
author: AlexandraKemperMS
ms.author: alkemper
ms.date: 05/26/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: f29be1807dfcc314c89d30301107670a970263ce
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102172871"
---
# <a name="authorize-access-to-azure-app-configuration-using-azure-active-directory"></a>Autorizace přístupu ke konfiguraci aplikace Azure pomocí Azure Active Directory
Kromě použití kódu HMAC (hash-based Message Authentication Code) podporuje konfigurace aplikací Azure používání služby Azure Active Directory (Azure AD) k autorizaci požadavků na instance konfigurace aplikace.  Azure AD umožňuje použít řízení přístupu na základě role Azure (Azure RBAC) k udělení oprávnění objektu zabezpečení.  Objekt zabezpečení může být uživatel, [spravovaná identita](../active-directory/managed-identities-azure-resources/overview.md) nebo [instanční objekt aplikace](../active-directory/develop/app-objects-and-service-principals.md).  Další informace o rolích a přiřazení rolí najdete v tématu [porozumění různým rolím](../role-based-access-control/overview.md).

## <a name="overview"></a>Přehled
Žádosti, které provedou objekt zabezpečení pro přístup k prostředku konfigurace aplikace, musí být autorizované. S Azure AD je přístup k prostředku v procesu se dvěma kroky:
1. Identita objektu zabezpečení je ověřena a je vrácen token OAuth 2,0.  Název prostředku pro vyžádání tokenu je `https://login.microsoftonline.com/{tenantID}` , kde `{tenantID}` odpovídá ID tenanta Azure Active Directory, do kterého objekt služby patří.
2. Token se předává jako součást požadavku službě konfigurace aplikace k autorizaci přístupu k zadanému prostředku.

Krok ověřování vyžaduje, aby žádost o aplikaci obsahovala přístupový token OAuth 2,0 za běhu.  Pokud je aplikace spuštěná v rámci entity Azure, jako je Azure Functions aplikace, Webová aplikace Azure nebo virtuální počítač Azure, může k přístupu k prostředkům použít spravovanou identitu.  Informace o tom, jak ověřit požadavky provedené spravovanou identitou do konfigurace aplikace Azure, najdete v tématu [ověření přístupu k prostředkům konfigurace aplikace Azure pomocí Azure Active Directory a spravovaných identit pro prostředky Azure](howto-integrate-azure-managed-service-identity.md).

Autorizační krok vyžaduje, aby se k objektu zabezpečení přiřadila jedna nebo více rolí Azure. Konfigurace aplikací Azure poskytuje role Azure, které zahrnují sady oprávnění pro prostředky konfigurace aplikací. Role, které jsou přiřazeny objektu zabezpečení, určují oprávnění poskytnutá objektu zabezpečení. Další informace o rolích Azure najdete v tématu [předdefinované role Azure pro konfiguraci aplikací Azure](#azure-built-in-roles-for-azure-app-configuration). 

## <a name="assign-azure-roles-for-access-rights"></a>Přiřazení rolí Azure pro přístupová práva
Azure Active Directory (Azure AD) autorizuje přístupová práva k zabezpečeným prostředkům prostřednictvím [řízení přístupu na základě role Azure (Azure RBAC)](../role-based-access-control/overview.md).

Když je role Azure přiřazená k objektu zabezpečení Azure AD, poskytuje Azure přístup k těmto prostředkům pro daný objekt zabezpečení. Přístup je omezený na prostředek konfigurace aplikace. Objekt zabezpečení služby Azure AD může být uživatel nebo instanční objekt nebo [spravovaná identita pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="azure-built-in-roles-for-azure-app-configuration"></a>Předdefinované role Azure pro konfiguraci aplikací Azure
Azure poskytuje následující předdefinované role Azure pro autorizaci přístupu ke konfiguračním datům aplikací pomocí Azure AD a OAuth:

- **Vlastník konfiguračních dat aplikací**: pomocí této role můžete k datům konfigurace aplikace udělit přístup pro čtení, zápis a odstranění. Tím se neudělí přístup k prostředku konfigurace aplikace.
- **Čtečka dat konfigurace aplikace**: tuto roli použijte, pokud chcete udělit přístup pro čtení k datům konfigurace aplikace. Tím se neudělí přístup k prostředku konfigurace aplikace.
- **Přispěvatel**: pomocí této role můžete spravovat prostředek konfigurace aplikace. I když k datům konfigurace aplikace můžete přistupovat pomocí přístupových klíčů, tato role neuděluje přímý přístup k datům pomocí Azure AD.
- **Čtecí modul**: tuto roli použijte, pokud chcete udělit přístup pro čtení prostředku konfigurace aplikace. Tím se neudělí přístup k přístupovým klíčům prostředku ani k datům uloženým v konfiguraci aplikace.

## <a name="next-steps"></a>Další kroky
Další informace o použití [spravovaných identit](howto-integrate-azure-managed-service-identity.md) ke správě služby konfigurace aplikací.
