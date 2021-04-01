---
title: Autorizace přístupu pomocí Azure Active Directory
description: Tento článek poskytuje informace o autorizaci přístupu k prostředkům služby signalizace Azure pomocí Azure Active Directory.
author: terencefan
ms.author: tefa
ms.date: 08/03/2020
ms.service: signalr
ms.topic: conceptual
ms.openlocfilehash: 37c2e41b5c81f941245b895ecd144baee3b9db6d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97797479"
---
# <a name="authorize-access-to-azure-signalr-service-resources-using-azure-active-directory"></a>Autorizace přístupu k prostředkům služby signalizace Azure pomocí Azure Active Directory
Služba signalizace Azure podporuje používání služby Azure Active Directory (Azure AD) k autorizaci požadavků do prostředků služby Azure Signal. Pomocí Azure AD můžete použít řízení přístupu na základě role (RBAC) k udělení oprávnění objektu zabezpečení, který může být uživatel nebo instanční objekt služby. Další informace o rolích a přiřazení rolí najdete v tématu [Principy různých rolí](../role-based-access-control/overview.md).

## <a name="overview"></a>Přehled
Když se objekt zabezpečení (aplikace) pokusí o přístup k prostředku služby signalizace Azure, musí být požadavek autorizovaný. S Azure AD je přístup k prostředku v procesu se dvěma kroky. 

 1. Nejprve je ověřená identita objektu zabezpečení a je vrácen token OAuth 2,0. Název prostředku pro vyžádání tokenu `https://signalr.azure.com/` .
 2. V dalším kroku se token předává jako součást požadavku službě Azure Signaler k autorizaci přístupu k zadanému prostředku.

Krok ověřování vyžaduje, aby žádost o aplikaci obsahovala přístupový token OAuth 2,0 za běhu. Pokud je váš server rozbočovače spuštěný v rámci entity Azure, jako je třeba virtuální počítač Azure, sada škálování virtuálního počítače nebo aplikace Azure Functions, může pro přístup k prostředkům použít spravovanou identitu. Informace o tom, jak ověřit požadavky provedené spravovanou identitou na službu Azure Signal, najdete v tématu [ověření přístupu k prostředkům služby signalizace Azure pomocí Azure Active Directory a spravovaných identit pro prostředky Azure](authenticate-managed-identity.md). 

Autorizační krok vyžaduje, aby se k objektu zabezpečení přiřadila jedna nebo více rolí RBAC. Služba signalizace Azure poskytuje role RBAC, které zahrnují sady oprávnění pro prostředky služby Azure Signal. Role, které jsou přiřazeny objektu zabezpečení, určují oprávnění, která bude mít objekt zabezpečení. Další informace o rolích RBAC najdete v tématu [předdefinované role služby Azure Signal Service](#azure-built-in-roles-for-azure-signalr-service). 

Server centra signálů, který není spuštěný v rámci entity Azure, se může taky autorizovat pomocí Azure AD. Informace o tom, jak požádat o přístupový token a použít ho k autorizaci žádostí o prostředky služby Azure Signal, najdete v tématu [ověření přístupu ke službě Azure Signal Service pomocí Azure AD z aplikace](authenticate-application.md). 

## <a name="azure-built-in-roles-for-azure-signalr-service"></a>Předdefinované role Azure pro službu Signaler

- [Server aplikace signálu]
- [Čtenář služby signaler]
- [Vlastník služby signaler]

## <a name="assign-rbac-roles-for-access-rights"></a>Přiřazení rolí RBAC pro přístupová práva
Azure Active Directory (Azure AD) autorizuje přístupová práva k zabezpečeným prostředkům prostřednictvím [řízení přístupu na základě role (RBAC)](../role-based-access-control/overview.md). Služba signalizace Azure definuje sadu předdefinovaných rolí Azure, které zahrnují společné sady oprávnění používaných pro přístup ke službě signalizace Azure, a můžete také definovat vlastní role pro přístup k prostředku.

Když je role RBAC přiřazená k objektu zabezpečení Azure AD, poskytuje Azure přístup k těmto prostředkům pro daný objekt zabezpečení. Přístup může být vymezený na úrovni předplatného, skupiny prostředků nebo libovolného prostředku služby signalizace Azure. Objekt zabezpečení služby Azure AD může být uživatel nebo aplikace nebo [spravovaná identita pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="built-in-roles-for-azure-signalr-service"></a>Předdefinované role pro službu Azure Signal Service
Azure poskytuje následující předdefinované role Azure pro autorizaci přístupu k prostředku služby signalizace Azure pomocí Azure AD a OAuth:

### <a name="signalr-app-server"></a>Server aplikace Signal

Pomocí této role získáte přístup k získání dočasného přístupového klíče pro podepisování tokenů klienta.

### <a name="signalr-serverless-contributor"></a>Přispěvatel bez serveru pro signál

Tuto roli použijte, pokud chcete, aby přístup získal token klienta ze služby Azure Signal Service přímo.

## <a name="next-steps"></a>Další kroky

Podívejte se na následující související články:

- [Ověření aplikace pomocí Azure AD pro přístup ke službě Azure Signal](authenticate-application.md)
- [Ověřování spravované identity pomocí Azure AD pro přístup ke službě Azure Signal](authenticate-managed-identity.md)