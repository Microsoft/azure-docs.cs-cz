---
title: Konfigurace s názvem umístění v Azure Active Directory | Microsoft Docs
description: Zjistěte, jak nakonfigurovat s názvem umístění.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: f56e042a-78d5-4ea3-be33-94004f2a0fc3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 4c8aba8faf4ce91445209eb46abea17423f57a77
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34589327"
---
# <a name="configure-named-locations-in-azure-active-directory"></a>Konfigurace s názvem umístění v Azure Active Directory

Pomocí pojmenovaného umístění můžete označit důvěryhodné rozsahy IP adres ve vaší organizaci. S názvem umístění v rámci Azure Active Directory používá:

- Ke zjištění [rizik události](active-directory-reporting-risk-events.md) a snížit počet hlášených falešně pozitivních zjištění.  

- [Podmíněného přístupu na základě umístění](active-directory-conditional-access-locations.md).


Tento článek vysvětluje, jak můžete nakonfigurovat s názvem umístění ve vašem prostředí.


## <a name="entry-points"></a>Vstupní body

Dostanete na stránku konfigurace s názvem umístění **zabezpečení** části stránky Azure Active Directory kliknutím:

![Vstupní body](./media/active-directory-named-locations/34.png)

- **Podmíněný přístup:**

    - V **spravovat** klikněte na tlačítko **s názvem umístění**.
    
        ![Příkaz pojmenované umístění](./media/active-directory-named-locations/06.png)

- **Rizikové přihlášení:**

    - Na panelu nástrojů v horní části klikněte na tlačítko **přidat známé rozsahy IP adres**.

       ![Příkaz pojmenované umístění](./media/active-directory-named-locations/35.png)



## <a name="configuration-example"></a>Příklad konfigurace

**Konfigurace s názvem umístění:**

1. Přihlaste se k [portál Azure](https://portal.azure.com) jako globální správce.

2. V levém podokně klikněte na **Azure Active Directory**.

    ![V levém podokně na odkaz Azure Active Directory](./media/active-directory-named-locations/01.png)

3. Na **Azure Active Directory** stránky v **zabezpečení** klikněte na tlačítko **podmíněného přístupu**.

    ![Příkaz podmíněného přístupu](./media/active-directory-named-locations/05.png)


4. Na **podmíněného přístupu** stránky v **spravovat** klikněte na tlačítko **s názvem umístění**.

    ![Příkaz pojmenované umístění](./media/active-directory-named-locations/06.png)


5. Na **s názvem umístění** klikněte na tlačítko **nové umístění**.

    ![Příkaz nové umístění](./media/active-directory-named-locations/07.png)


6. Na **nový** proveďte následující:

    ![Nové okno](./media/active-directory-named-locations/61.png)

    a. V **název** zadejte název pro vaše s názvem umístění.

    b. V **rozsahy IP adres** zadejte rozsah adres IP. Rozsah IP adres musí být ve *směrování mezi doménami* formátu (CIDR).  

    c. Klikněte na možnost **Vytvořit**.



## <a name="next-steps"></a>Další postup

Další informace naleznete v tématu:

- [Podmíněný přístup v Azure Active Directory](active-directory-conditional-access-azure-portal.md).

- [Podmínky umístění v Azure Active Directory podmíněného přístupu](active-directory-conditional-access-locations.md)

- [Azure Active Directory rizikových událostech](active-directory-reporting-risk-events.md).

- [Sestava rizikové přihlášení na portálu Azure Active Directory](active-directory-reporting-security-risky-sign-ins.md).  
