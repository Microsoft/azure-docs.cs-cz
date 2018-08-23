---
title: Povolit poskytovatele cloudové služby ke správě vašich předplatných Azure Stack | Dokumentace Microsoftu
description: Povolte poskytovatele služby pro přístup k předplatnému ve službě Azure Stack.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2018
ms.author: brenduns
ms.reviewer: alfredop
ms.openlocfilehash: f309b86578f340040927735c067656158f3198fc
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/16/2018
ms.locfileid: "42057181"
---
# <a name="enable-a-cloud-service-provider-to-manage-your-azure-stack-subscription"></a>Povolit poskytovatele cloudové služby ke správě vašich předplatných Azure Stack

*Platí pro: integrované systémy Azure Stack*

Pokud používáte Azure Stack se Cloud Service Provider (CSP), můžete ke správě svého vlastního předplatného pro přístup k prostředkům v Azure a ve službě Azure Stack. Můžete taky umožnit zprostředkovateli spravovat své předplatné pro vás. Tento článek vám ukáže, jak do:

 * Udělte přístup k vaší službě poskytovatele předplatného.
 * Zkontrolujte, že poskytovatel služeb můžete spravovat vaši službu.

> [!Note]
>  Pokud zprostředkovatel kryptografických služeb se správou vašeho účtu a můžete přeskočit následující kroky, zprostředkovatel kryptografických služeb nemůže spravovat předplatné služby Azure Stack pro vás.

## <a name="manage-your-subscription-with-a-cloud-service-provider"></a>Správa předplatného pro poskytovatele cloudových služeb

Přidat zprostředkovatele kryptografických služeb jako **uživatele** do vašeho předplatného.

1. Poskytovatel cloudových služeb jako uživatel typu Host s rolí uživatele přidejte do svého tenanta adresáře.  Postup pro přidání uživatele najdete v tématu [přidání nových uživatelů do služby Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory)
2. Poskytovatel CSP vytvoří místní předplatnému služby Azure Stack pro vás.
3. Jste připravení začít používat Azure Stack.
4. Poskytovatel cloudových služeb by měl vytvořit prostředek v rámci vašeho předplatného, chcete-li ověřit, že vaše prostředky spravovat. Například může [vytvoření virtuálního počítače s Windows pomocí portálu Azure Stack](azure-stack-quick-windows-portal.md).

## <a name="enable-the-cloud-service-provider-to-manage-your-subscription-using-rbac-rights"></a>Povolit poskytovatele cloudové služby ke správě svého předplatného pomocí oprávnění RBAC

Přidat zprostředkovatele kryptografických služeb jako **vlastníka** do vašeho předplatného.

1. Přidání poskytovatel cloudových služeb jako uživatel typu Host do tenanta adresáře.  Postup pro přidání uživatele najdete v tématu [přidání nových uživatelů do služby Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory)
2. Přidáte roli vlastníka na uživatele typu Host CSP. Pokyny k přidání uživatele CSP ke svému předplatnému, najdete v článku [Use Role-Based řízení přístupu ke správě přístupu k prostředkům předplatného Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)
3. Poskytovatel CSP vytvoří místní předplatnému služby Azure Stack pro vás.
4. Jste připravení začít používat Azure Stack.
5. Poskytovatel cloudových služeb by měl vytvořit prostředek v rámci vašeho předplatného, chcete-li ověřit, že můžou spravovat vaše prostředky.

## <a name="next-steps"></a>Další postup

Další informace o tom, jak načíst informace o využití prostředků ze služby Azure Stack, najdete v článku [využití a fakturace ve službě Azure Stack](../azure-stack-billing-and-chargeback.md).
