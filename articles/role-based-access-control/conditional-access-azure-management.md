---
title: Spravovat přístup pro správu Azure pomocí podmíněného přístupu v Azure Active Directory
description: Další informace o použití podmíněného přístupu ve službě Azure AD můžete spravovat přístup pro správu Azure.
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
ms.date: 09/22/2017
ms.author: rolyon
ms.reviewer: skwan
ms.openlocfilehash: 083cb4eb84746f4a61b51f3573a0bf66110fe1ee
ms.sourcegitcommit: e0834ad0bad38f4fb007053a472bde918d69f6cb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37435044"
---
# <a name="manage-access-to-azure-management-with-conditional-access"></a>Správa přístupu ke správě Azure s podmíněným přístupem

Podmíněný přístup ve službě Azure Active Directory (Azure AD) řídí přístup ke cloudovým aplikacím na základě určitých podmínek, které zadáte. Pokud chcete povolit přístup, můžete vytvořit zásady podmíněného přístupu, které povolí nebo zablokuje přístup založený na tom, zda jsou splněny požadavky v zásadách. 

Obvykle použijete podmíněný přístup pro řízení přístupu k vašim cloudovým aplikacím. Můžete také nastavit zásady pro řízení přístupu pro správu Azure na základě určitých podmínek (třeba rizika přihlášení, umístění nebo zařízení) a vynucují požadavky, jako je ověřování službou Multi-Factor Authentication.

Vytvoření zásad pro správu Azure, vyberte **Microsoft Azure Management** pod **cloudové aplikace** při výběru aplikace, pro kterou chcete zásadu použít.

![Podmíněný přístup pro správu Azure](./media/conditional-access-azure-management/conditional-access-azure-mgmt.png)

Zásady, kterou vytvoříte platí pro všechny koncové body správy Azure, včetně portálu Azure classic, webu Azure portal, Azure Resource Manageru poskytovatele, classic služby API Management a Azure Powershellu.

> [!CAUTION]
> Ujistěte se, že chápete, jak podmíněný přístup funguje před nastavením zásad pro správu přístupu pro správu Azure. Ujistěte se, že nevytvoříte podmínky, které by mohly blokovat vlastní přístup k portálu.

Další informace o tom, jak nastavit a začít používat podmíněný přístup, najdete v části [podmíněného přístupu v Azure Active Directory](../active-directory/active-directory-conditional-access-azure-portal.md).