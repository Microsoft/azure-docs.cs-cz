---
title: "Povolit poskytovatele cloudové služby ke správě svého předplatného Azure zásobníku | Microsoft Docs"
description: "Povolte poskytovatele služeb k odběru v zásobníku Azure."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2018
ms.author: mabrigg
ms.reviewer: alfredop
ms.openlocfilehash: 4bc5644425aa11fb210d81095e4166baefc6432e
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2018
---
# <a name="enable-a-cloud-service-provider-to-manage-your-azure-stack-subscription"></a>Povolit poskytovatele cloudové služby ke správě svého předplatného Azure zásobníku

*Platí pro: Azure zásobníku integrované systémy*

Pokud používáte Azure zásobníku se poskytovatele cloudové služby (CSP), může být váš přístup k prostředkům ve vašem předplatném Azure a v zásobníku Azure spravovaných zprostředkovatelem služby. Nebo může správě svého vlastního předplatného. Tento článek vypadá na tom, jak můžete povolit poskytovatele služeb pro vaše předplatné vaším jménem přístup a ujistěte se, že poskytovatel služeb můžete spravovat služby.

> [!Note]  
>  Pokud tyto kroky jsou přeskočeny, a zprostředkovatel kryptografických služeb není již Správa účtu, potom CSP nebude možné ke správě svého předplatného Azure zásobníku vaším jménem.

## <a name="manage-your-subscription-with-a-cloud-service-provider"></a>Spravovat předplatné u poskytovatele cloudové služby

1. Přidejte zprostředkovatelem kryptografických služeb jako uživatel guest s rolí uživatele do adresáře klienta.  Pokyny k přidání uživatele, najdete v části [přidání nových uživatelů do služby Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory)
2. Zprostředkovatel kryptografických služeb pak vytvoří místní odběr zásobník Azure za vás.
3. Jste připraveni začít používat Azure zásobníku.
3. Poskytovatel cloudových služeb by pak vytvořte prostředek v rámci vašeho předplatného, chcete-li ověřit, že se můžete také spravovat vaše prostředky. Například můžete [vytvoření virtuálního počítače s Windows pomocí portálu Azure zásobníku](azure-stack-quick-windows-portal.md).

## <a name="enable-the-cloud-service-provider-to-manage-your-subscription-using-rbac-rights"></a>Povolit poskytovateli cloudové služby ke správě svého předplatného pomocí RBAC práva

Do svého předplatného přidáte CSP jako vlastníka. 

1. Přidejte zprostředkovatelem kryptografických služeb jako uživatel guest. k roli vlastníka do vašeho adresáře klienta.  Pokyny k přidání uživatele, najdete v části [přidání nových uživatelů do služby Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory)
2. Přidáte roli vlastníka na uživatele guest CSP. Pokyny k přidání uživatele CSP k vašemu předplatnému, najdete v části [Use Role-Based řízení přístupu ke správě přístupu k prostředkům předplatného Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure)
3. Zprostředkovatel kryptografických služeb pak vytvoří místní odběr zásobník Azure za vás.
4. Jste připraveni začít používat Azure zásobníku.
5. Poskytovatel cloudových služeb by pak vytvořte prostředek v rámci vašeho předplatného, chcete-li ověřit, že umožňuje spravovat prostředky. 

## <a name="next-steps"></a>Další postup

  - Další informace o tom, jak načíst informace o využití prostředků z zásobník Azure najdete v tématu [využití a cenách služby Azure zásobníku](../azure-stack-billing-and-chargeback.md).
