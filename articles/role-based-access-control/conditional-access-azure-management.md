---
title: Správa přístupu ke správě Azure pomocí podmíněného přístupu ve službě Azure AD
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
ms.openlocfilehash: f3341f1c30a1581b8507652c322c00581e3972aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77137411"
---
# <a name="manage-access-to-azure-management-with-conditional-access"></a>Správa přístupu ke správě Azure pomocí podmíněného přístupu

> [!CAUTION]
> Ujistěte se, že jste pochopili, jak funguje podmíněný přístup před nastavením zásady pro správu přístupu ke správě Azure. Ujistěte se, že nevytváříte podmínky, které by mohly blokovat váš vlastní přístup k portálu.

Podmíněný přístup ve službě Azure Active Directory (Azure AD) řídí přístup ke cloudovým aplikacím na základě konkrétních podmínek, které zadáte. Chcete-li povolit přístup, vytvořte zásady podmíněného přístupu, které umožňují nebo blokují přístup na základě toho, zda jsou splněny požadavky v zásadách. 

Podmíněný přístup se obvykle používá k řízení přístupu ke cloudovým aplikacím. Můžete také nastavit zásady pro řízení přístupu ke správě Azure na základě určitých podmínek (například rizika přihlášení, umístění nebo zařízení) a vynucovat požadavky, jako je vícefaktorové ověřování.

Pokud chcete vytvořit zásadu pro správu Azure, vyberte **Microsoft Azure Management** v části **Cloudové aplikace** při výběru aplikace, na kterou se mají zásady použít.

![Podmíněný přístup pro správu Azure](./media/conditional-access-azure-management/conditional-access-azure-mgmt.png)

Zásady, které vytvoříte, platí pro všechny koncové body správy Azure, včetně následujících:

- portál Azure
- Zprostředkovatel Azure Resource Manageru
- Klasická řešení API pro správu služeb
- Azure PowerShell
- Portál pro správce předplatných Visual Studia
- Azure DevOps
- Portál Azure Data Factory

Všimněte si, že zásady platí pro Azure PowerShell, který volá rozhraní API Azure Resource Manager. Nevztahuje se na [Azure AD PowerShell](/powershell/azure/active-directory/install-adv2), který volá Microsoft Graph.


Další informace o nastavení a používání podmíněného přístupu najdete [v tématu Podmíněný přístup ve službě Azure Active Directory](../active-directory/active-directory-conditional-access-azure-portal.md).
