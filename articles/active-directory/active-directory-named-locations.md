---
title: Konfigurace pojmenovaných umístění ve službě Azure Active Directory | Dokumentace Microsoftu
description: Zjistěte, jak konfigurace pojmenovaných umístění.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.component: protection
ms.assetid: f56e042a-78d5-4ea3-be33-94004f2a0fc3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 62a55672a4326df585fc84699dfd72424be362dc
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2018
ms.locfileid: "39627487"
---
# <a name="configure-named-locations-in-azure-active-directory"></a>Konfigurace pojmenovaných umístění ve službě Azure Active Directory

S pojmenovaná umístění lze označit důvěryhodné rozsahy IP adres ve vaší organizaci. Azure Active Directory používá pojmenovaná umístění v rámci:

- Zjištění [rizikových událostí](reports-monitoring/concept-risk-events.md) a snížit počet ohlášených počet falešně pozitivních výsledků.  

- [Podmíněný přístup založený na umístění](conditional-access/location-condition.md).


Tento článek vysvětluje, jak nakonfigurovat pojmenovaná umístění ve vašem prostředí.


## <a name="entry-points"></a>Vstupní body

Dostanete na stránku konfigurace pojmenovaných umístění v **zabezpečení** stránce Azure Active Directory kliknutím v části:

![Vstupní body](./media/active-directory-named-locations/34.png)

- **Podmíněný přístup:**

    - V **spravovat** klikněte na tlačítko **pojmenovaná umístění**.
    
        ![Příkaz pojmenovaná umístění](./media/active-directory-named-locations/06.png)

- **Riziková přihlášení:**

    - Na panelu nástrojů v horní části klikněte na tlačítko **přidat známé rozsahy IP adres**.

       ![Příkaz pojmenovaná umístění](./media/active-directory-named-locations/35.png)



## <a name="configuration-example"></a>Konfigurace – příklad

**Konfigurace pojmenovaných umístění:**

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) jako globální správce.

2. V levém podokně klikněte na tlačítko **Azure Active Directory**.

    ![Propojení služby Azure Active Directory v levém podokně](./media/active-directory-named-locations/01.png)

3. Na **Azure Active Directory** stránku, **zabezpečení** klikněte na tlačítko **podmíněného přístupu**.

    ![Příkaz podmíněného přístupu](./media/active-directory-named-locations/05.png)


4. Na **podmíněného přístupu** stránku, **spravovat** klikněte na tlačítko **pojmenovaná umístění**.

    ![Příkaz pojmenovaná umístění](./media/active-directory-named-locations/06.png)


5. Na **pojmenovaná umístění** klikněte na **nové umístění**.

    ![Nový příkaz umístění](./media/active-directory-named-locations/07.png)


6. Na **nový** stránce, postupujte takto:

    ![Nové okno](./media/active-directory-named-locations/61.png)

    a. V **název** zadejte název pro pojmenované umístění.

    b. V **rozsahy IP adres** zadejte rozsah IP adres. Rozsah IP adres musí být v *Classless Inter-Domain Routing* formát (CIDR Classless).  

    c. Klikněte na možnost **Vytvořit**.



## <a name="next-steps"></a>Další postup

Další informace naleznete v tématu:

- [Podmíněný přístup ve službě Azure Active Directory](active-directory-conditional-access-azure-portal.md).

- [Podmínky umístění podmíněného přístupu Azure Active Directory](conditional-access/location-condition.md)

- [Azure Active Directory rizikových událostí](reports-monitoring/concept-risk-events.md).

- [Sestavy rizikových přihlášení na portálu Azure Active Directory](reports-monitoring/concept-risky-sign-ins.md).  
