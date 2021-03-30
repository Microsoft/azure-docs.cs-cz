---
title: Správa přístupu ke správě Azure pomocí podmíněného přístupu v Azure AD
description: Přečtěte si o použití podmíněného přístupu ve službě Azure AD ke správě přístupu ke správě Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: skwan
ms.assetid: 0adc8b11-884e-476c-8c43-84f9bf12a34b
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/15/2019
ms.author: rolyon
ms.reviewer: skwan
ms.openlocfilehash: 2a52635dbaa7a76034f3a535b099320a901e8c07
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "83758771"
---
# <a name="manage-access-to-azure-management-with-conditional-access"></a>Správa přístupu ke správě Azure pomocí podmíněného přístupu

> [!CAUTION]
> Ujistěte se, že rozumíte tomu, jak podmíněný přístup funguje, než nastavíte zásadu pro správu přístupu ke správě Azure. Ujistěte se, že nevytváříte podmínky, které by mohly blokovat váš vlastní přístup k portálu.

Podmíněný přístup v Azure Active Directory (Azure AD) řídí přístup ke cloudovým aplikacím na základě konkrétních podmínek, které zadáte. Pokud chcete přístup udělit, vytvořte zásady podmíněného přístupu, které povolí nebo zablokují přístup na základě toho, jestli jsou požadavky v zásadě splněné nebo ne. 

Obvykle používáte podmíněný přístup k řízení přístupu k vašim cloudovým aplikacím. Můžete také nastavit zásady pro řízení přístupu ke správě Azure na základě určitých podmínek (například rizika přihlašování, umístění nebo zařízení) a vynutilit požadavky, jako je Multi-Factor Authentication.

Pokud chcete vytvořit zásadu pro správu Azure, při výběru aplikace, pro kterou chcete zásadu použít, vyberte v části **cloudové aplikace** **Microsoft Azure Správa** .

![Podmíněný přístup pro správu Azure](./media/conditional-access-azure-management/conditional-access-azure-mgmt.png)

Zásada, kterou vytvoříte, se vztahuje na všechny koncové body správy Azure, včetně následujících:

- portál Azure
- Poskytovatel Azure Resource Manager
- Rozhraní API pro správu klasických služeb
- Azure PowerShell
- Portál pro správu předplatných sady Visual Studio
- Azure DevOps
- Portál Azure Data Factory

Všimněte si, že zásada platí pro Azure PowerShell, která volá rozhraní API Azure Resource Manager. Nevztahuje se na [Azure AD PowerShell](/powershell/azure/active-directory/install-adv2), který volá Microsoft Graph.

Další informace o tom, jak nastavit ukázkovou zásadu pro povolení podmíněného přístupu pro správu Microsoft Azure, najdete v článku [podmíněný přístup: vyžadovat MFA pro správu Azure](../active-directory/conditional-access/howto-conditional-access-policy-azure-management.md).
