---
title: Odstranění skupiny pomocí služby Azure Active Directory | Dokumentace Microsoftu
description: Zjistěte, jak odstranit skupinu pomocí Azure Active Directory.
services: active-directory
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: lizross
ms.reviewer: krbain
ms.custom: it-pro
ms.openlocfilehash: 014fe487d23a6c75e94ca2708ed15044bd6cf53b
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2018
ms.locfileid: "45574322"
---
# <a name="how-to-delete-a-group-using-azure-active-directory"></a>Postupy: odstranění skupiny pomocí služby Azure Active Directory
Můžete odstranit skupinu pro libovolný počet důvody, ale obvykle bude možné, protože jste:

- Nesprávně nastavena **typ skupiny** nesprávné možnosti

- Vytvoření omylem nesprávné nebo duplicitní skupinu 

- Už nepotřebujete skupiny

## <a name="to-delete-a-group"></a>Chcete odstranit skupinu
1. Přihlaste se k [webu Azure portal](https://portal.azure.com) pomocí účtu globálního správce adresáře.

2. Vyberte **Azure Active Directory**a pak vyberte **skupiny**.

3. Z **skupiny – všechny skupiny** stránky, vyhledejte a vyberte skupinu, kterou chcete odstranit. K těmto krokům použijeme **zásady MDM - východ**.

    ![Stránka skupiny – všechny skupiny, zvýrazněným názvem skupiny](media/active-directory-groups-delete-group/group-all-groups-screen.png)

4. Na **zásady MDM – přehled – východ** stránce a pak vyberte **odstranit**.

    Skupina se odstranila z vašeho tenanta Azure Active Directory.

    ![Zvýrazněný MDM zásady - stránka s přehledem – východ, možností pro odstranění](media/active-directory-groups-delete-group/group-overview-blade.png)

## <a name="next-steps"></a>Další postup

- Pokud omylem odstraníte skupinu, můžete ho vytvořit znovu. Další informace najdete v tématu [vytvoření základní skupiny a přidání členů](active-directory-groups-create-azure-portal.md).

- Pokud omylem odstraníte skupinu Office 365, je možné ji obnovit. Další informace najdete v tématu [obnovení odstraněné skupiny Office 365](../users-groups-roles/groups-restore-deleted.md).
