---
title: Povolit poskytovatele cloudové služby ke správě svého předplatného Azure zásobníku | Microsoft Docs
description: Povolte poskytovatele služeb k odběru v zásobníku Azure.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2018
ms.author: mabrigg
ms.reviewer: alfredop
ms.openlocfilehash: f0cff8f575b87872c0032854f1916b140d7fd62b
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="enable-a-cloud-service-provider-to-manage-your-azure-stack-subscription"></a>Povolit poskytovatele cloudové služby ke správě svého předplatného Azure zásobníku

*Platí pro: Azure zásobníku integrované systémy*

Pokud používáte Azure zásobníku se poskytovatele cloudové služby (CSP), můžete ke správě svého vlastního předplatného pro přístup k prostředkům v Azure a Azure zásobníku. Můžete taky umožnit zprostředkovateli spravovat své předplatné pro vás. Tento článek ukazuje, jak na:

 * Udělte přístup poskytovatele vaší služby vašeho předplatného.
 * Zajistěte, aby že na poskytovatele služby můžete spravovat služby.

> [!Note]
>  Pokud zprostředkovatel kryptografických služeb není Správa účtu, a můžete přeskočit následující kroky, nelze CSP Spravovat předplatné zásobník Azure za vás.

## <a name="manage-your-subscription-with-a-cloud-service-provider"></a>Spravovat předplatné u poskytovatele cloudové služby

Přidejte zprostředkovatele kryptografických služeb jako **uživatele** do vašeho předplatného.

1. Přidejte zprostředkovatelem kryptografických služeb jako uživatel guest s rolí uživatele do adresáře klienta.  Pokyny k přidání uživatele, najdete v části [přidání nových uživatelů do služby Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory)
2. Poskytovatel CSP vytvoří místní odběr zásobník Azure za vás.
3. Jste připraveni začít používat Azure zásobníku.
4. Poskytovatel cloudových služeb měli vytvořit prostředek v rámci vašeho předplatného, chcete-li ověřit, že se můžete také spravovat vaše prostředky. Například můžete [vytvoření virtuálního počítače s Windows pomocí portálu Azure zásobníku](azure-stack-quick-windows-portal.md).

## <a name="enable-the-cloud-service-provider-to-manage-your-subscription-using-rbac-rights"></a>Povolit poskytovateli cloudové služby ke správě svého předplatného pomocí RBAC práva

Přidejte zprostředkovatele kryptografických služeb jako **vlastníka** do vašeho předplatného.

1. Přidejte zprostředkovatelem kryptografických služeb jako uživatel guest do vašeho adresáře klienta.  Pokyny k přidání uživatele, najdete v části [přidání nových uživatelů do služby Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory)
2. Přidáte roli vlastníka na uživatele guest CSP. Pokyny k přidání uživatele CSP k vašemu předplatnému, najdete v části [Use Role-Based řízení přístupu ke správě přístupu k prostředkům předplatného Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)
3. Poskytovatel CSP vytvoří místní odběr zásobník Azure za vás.
4. Jste připraveni začít používat Azure zásobníku.
5. Poskytovatel cloudových služeb měli vytvořit prostředek v rámci vašeho předplatného, chcete-li ověřit, že umožňuje spravovat prostředky.

## <a name="next-steps"></a>Další postup

Další informace o tom, jak načíst informace o využití prostředků z zásobník Azure najdete v tématu [využití a cenách služby Azure zásobníku](../azure-stack-billing-and-chargeback.md).
