---
title: Odstranění skupiny – Azure Active Directory | Microsoft Docs
description: Pokyny, jak odstranit skupinu pomocí Azure Active Directory.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 08/29/2018
ms.author: ajburnle
ms.reviewer: krbain
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 800c1742b49fce7e1adf8c3ca22181cfb7d0a085
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89565503"
---
# <a name="delete-a-group-using-azure-active-directory"></a>Odstranění skupiny pomocí Azure Active Directory
Skupinu Azure Active Directory (Azure AD) můžete odstranit z libovolného počtu důvodů, ale obvykle to bude být způsobeno tím, že:

- Nesprávně nastavili jste **typ skupiny** na nesprávnou možnost.

- Omylem se vytvořila nesprávná nebo duplicitní skupina. 

- Tuto skupinu už nepotřebujete.

## <a name="to-delete-a-group"></a>Odstranění skupiny
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí účtu globálního správce daného adresáře.

2. Vyberte **Azure Active Directory**a pak vyberte **skupiny**.

3. Na stránce **skupiny – všechny skupiny** vyhledejte a vyberte skupinu, kterou chcete odstranit. Pro tyto kroky použijeme **zásady MDM – východ**.

    ![Stránka Groups-All skupiny, název skupiny se zvýrazní](media/active-directory-groups-delete-group/group-all-groups-screen.png)

4. Na stránce **Přehled zásad MDM – východ** a pak vyberte **Odstranit**.

    Skupina se odstraní z vašeho tenanta Azure Active Directory.

    ![Zásady MDM – stránka přehled, vybraná možnost Odstranit](media/active-directory-groups-delete-group/group-overview-blade.png)

## <a name="next-steps"></a>Další kroky

- Pokud odstraníte skupinu omylem, můžete ji znovu vytvořit. Další informace najdete v tématu [Postup vytvoření základní skupiny a přidání členů](active-directory-groups-create-azure-portal.md).

- Pokud odstraníte Microsoft 365 skupinu omylem, je možné, že ji budete moct obnovit. Další informace najdete v tématu [Obnovení odstraněné skupiny Office 365](../users-groups-roles/groups-restore-deleted.md).
