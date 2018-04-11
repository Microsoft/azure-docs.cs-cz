---
title: "Spravovat přístup pro správu Azure pomocí podmíněného přístupu v Azure Active Directory"
description: "Další informace o použití podmíněného přístupu ve službě Azure AD můžete spravovat přístup k správy Azure."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: bryanla
ms.assetid: 0adc8b11-884e-476c-8c43-84f9bf12a34b
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/22/2017
ms.author: skwan
ms.openlocfilehash: 22d0e53c201853e2c316089479ffbd4d9e5d92be
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/18/2018
---
# <a name="manage-access-to-azure-management-with-conditional-access"></a>Správa přístupu k Azure správy s podmíněným přístupem

Podmíněný přístup v Azure Active Directory (Azure AD) řídí přístup k cloudových aplikací na základě určitých podmínek, které zadáte. Povolit přístup, je vytvořit zásady podmíněného přístupu, které povolit nebo blokovat přístup podle toho, zda jsou splněny požadavky na v zásadách. 

Podmíněný přístup k řízení přístupu se obvykle používají pro cloudové aplikace. Můžete také nastavit zásady řízení přístupu pro správu Azure, které jsou založené na určité podmínky (třeba přihlašovací riziko, umístění nebo zařízení) a vynucování požadavky, jako je vícefaktorové ověřování.

Pokud chcete vytvořit zásadu pro správu Azure, vyberte **Microsoft Azure Management** pod **cloudových aplikací** při výběru aplikace, do které chcete zásadu použít.

![Podmíněný přístup pro správu Azure](./media/conditional-access-azure-mgmt.png)

Zásada, kterou vytvoříte platí pro všechny koncové body správy Azure, včetně portálu Azure classic, portálu Azure, Azure Resource Manager poskytovatele, klasické rozhraní API pro správu služby a prostředí Azure PowerShell.

> [!CAUTION]
> Zajistěte, aby vám pochopit, jak podmíněný přístup funguje před nastavením zásad pro správu přístupu k správu Azure. Ujistěte se, že nevytvoříte podmínky, které by mohly blokovat vlastní přístup k portálu.

Další informace o tom, jak nastavit a používat podmíněný přístup, najdete v části [podmíněného přístupu v Azure Active Directory](active-directory-conditional-access-azure-portal.md).