---
title: Odstranění skupiny – Azure Active Directory | Microsoft Docs
description: Pokyny, jak odstranit skupinu pomocí Azure Active Directory.
services: active-directory
author: msaburnley
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
ms.openlocfilehash: cf309c10dc5924374d8c3a191f6fd11cceb2ac49
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85604280"
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

    ![Skupiny – stránka všechny skupiny, zvýrazněný název skupiny](media/active-directory-groups-delete-group/group-all-groups-screen.png)

4. Na stránce **Přehled zásad MDM – východ** a pak vyberte **Odstranit**.

    Skupina se odstraní z vašeho tenanta Azure Active Directory.

    ![Zásady MDM – stránka přehled, vybraná možnost Odstranit](media/active-directory-groups-delete-group/group-overview-blade.png)

## <a name="next-steps"></a>Další kroky

- Pokud odstraníte skupinu omylem, můžete ji znovu vytvořit. Další informace najdete v tématu [Postup vytvoření základní skupiny a přidání členů](active-directory-groups-create-azure-portal.md).

- Pokud omylem odstraníte skupinu Office 365, je možné ji obnovit. Další informace najdete v tématu [Obnovení odstraněné skupiny Office 365](../users-groups-roles/groups-restore-deleted.md).
