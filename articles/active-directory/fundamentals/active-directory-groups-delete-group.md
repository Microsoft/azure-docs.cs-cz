---
title: Odstranění skupiny – Azure Active Directory | Dokumentace Microsoftu
description: Pokyny ohledně toho, jak odstranit skupinu pomocí Azure Active Directory.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: lizross
ms.reviewer: krbain
ms.custom: it-pro, seodec18
ms.openlocfilehash: 61f6d68e873b6c8b8b901e5e065065ed42513572
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2019
ms.locfileid: "55100191"
---
# <a name="delete-a-group-using-azure-active-directory"></a>Odstranění skupiny pomocí služby Azure Active Directory
Můžete odstranit skupinu Azure Active Directory (Azure AD) pro z nejrůznějších důvodů, ale obvykle bude možné, protože jste:

- Nesprávně nastavena **typ skupiny** na nesprávnou možnost.

- Vytvoření omylem nesprávné nebo duplicitní skupiny. 

- Už nepotřebujete skupině.

## <a name="to-delete-a-group"></a>Chcete odstranit skupinu
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí účtu globálního správce daného adresáře.

2. Vyberte **Azure Active Directory**a pak vyberte **skupiny**.

3. Z **skupiny – všechny skupiny** stránky, vyhledejte a vyberte skupinu, kterou chcete odstranit. K těmto krokům použijeme **zásady MDM - východ**.

    ![Stránka skupiny – všechny skupiny, zvýrazněným názvem skupiny](media/active-directory-groups-delete-group/group-all-groups-screen.png)

4. Na **zásady MDM – přehled – východ** stránce a pak vyberte **odstranit**.

    Skupina se odstranila z vašeho tenanta Azure Active Directory.

    ![Zvýrazněný MDM zásady - stránka s přehledem – východ, možností pro odstranění](media/active-directory-groups-delete-group/group-overview-blade.png)

## <a name="next-steps"></a>Další postup

- Pokud omylem odstraníte skupinu, můžete ho vytvořit znovu. Další informace najdete v tématu [vytvoření základní skupiny a přidání členů](active-directory-groups-create-azure-portal.md).

- Pokud omylem odstraníte skupinu Office 365, je možné ji obnovit. Další informace najdete v tématu [obnovení odstraněné skupiny Office 365](../users-groups-roles/groups-restore-deleted.md).
