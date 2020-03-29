---
title: Odstranění skupiny – Služba Azure Active Directory | Dokumenty společnosti Microsoft
description: Pokyny k odstranění skupiny pomocí služby Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: ajburnle
ms.reviewer: krbain
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: bdba55e0655a13e65e403f5da73fcb69db5dbca5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68561920"
---
# <a name="delete-a-group-using-azure-active-directory"></a>Odstranění skupiny pomocí služby Azure Active Directory
Skupinu Azure Active Directory (Azure AD) můžete odstranit z libovolného počtu důvodů, ale obvykle to bude proto, že:

- Nesprávně nastavte **typ skupiny** na nesprávnou možnost.

- Omylem vytvořil nesprávnou nebo duplicitní skupinu. 

- Už skupinu nepotřebuji.

## <a name="to-delete-a-group"></a>Odstranění skupiny
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí účtu globálního správce daného adresáře.

2. Vyberte **Službu Azure Active Directory**a pak vyberte **Skupiny**.

3. Na stránce **Skupiny – všechny skupiny** vyhledejte a vyberte skupinu, kterou chcete odstranit. Pro tyto kroky použijeme **zásady MDM – východ**.

    ![Stránka Skupiny -Všechny skupiny, zvýrazněný název skupiny](media/active-directory-groups-delete-group/group-all-groups-screen.png)

4. Na **stránce Zásady MDM – Východní přehled** a potom vyberte **Odstranit**.

    Skupina se odstraní z vašeho klienta Služby Azure Active Directory.

    ![Zásady MDM – stránka Východní přehled, zvýrazněná možnost odstranění](media/active-directory-groups-delete-group/group-overview-blade.png)

## <a name="next-steps"></a>Další kroky

- Pokud skupinu omylem odstraníte, můžete ji vytvořit znovu. Další informace naleznete [v tématu Jak vytvořit základní skupinu a přidat členy](active-directory-groups-create-azure-portal.md).

- Pokud skupinu Office 365 omylem odstraníte, možná ji budete moct obnovit. Další informace najdete [v tématu Obnovení odstraněné skupiny Office 365](../users-groups-roles/groups-restore-deleted.md).
