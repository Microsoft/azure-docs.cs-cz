---
title: Postup konfigurace výstrah zabezpečení | Dokumentace Microsoftu
description: Zjistěte, jak konfigurovat výstrahy zabezpečení u rozšíření Azure Privileged Identity Management.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: pim
ms.date: 06/06/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 8df9bc7c332a83e9761ea71dddfbfbfaa3ae5154
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2018
ms.locfileid: "39622149"
---
# <a name="how-to-configure-security-alerts-in-azure-ad-privileged-identity-management"></a>Postup konfigurace výstrah zabezpečení ve službě Azure AD Privileged Identity Management
## <a name="security-alerts"></a>Výstrahy zabezpečení
Azure Privileged Identity Management (PIM) generuje výstrahy, když je aktivita podezřelého nebo nebezpečné ve vašem prostředí. Když se aktivuje upozornění, zobrazí na řídicím panelu PIM. Vyberte výstrahu, pokud chcete zobrazit sestavu obsahující seznam uživatelů nebo rolí, které aktivuje výstrahu.

![Výstrahy zabezpečení řídicí panel PIM – snímek obrazovky](./media/pim-how-to-configure-security-alerts/PIM_security_dash.png)

| Výstrahy | Severity | Trigger | Doporučení |
| --- | --- | --- | --- |
| **Role se přiřazení mimo PIM** |Vysoký |Uživatel byl trvale přiřazenou privilegovanou roli, mimo PIM rozhraní. |Projděte si uživatelé v seznamu a rušit přiřazení, je z privilegované role přiřazené mimo PIM. |
| **Role se aktivuje příliš často** |Střednědobé používání |Došlo k příliš mnoho otestovaného stejný atribut role v nastavení povoleném čase. |Kontaktujte uživatele zobrazíte, proč jejich aktivaci role tolik času. Časový limit je pravděpodobně příliš krátká pro jejich dokončení jejich úloh, nebo možná používáte skripty mohl automaticky aktivovat roli. Ujistěte se, že doba trvání aktivace kvůli své roli je nastaven dostatečně dlouhé pro ně k provádění svých úloh. |
| **Role nevyžadují ověřování službou Multi-Factor Authentication pro aktivaci** |Střednědobé používání |Jsou role bez MFA povolen v nastavení. |Doporučujeme vyžadovat vícefaktorové ověřování pro největší vysoce privilegované role, ale důrazně doporučujeme povolit MFA pro aktivaci všech rolí. |
| **Uživatelé nepoužívají svoje privilegované role** |Nízká |Existují oprávněné správce, které se nedávno neaktivovali jejich role. |Zahájení kontroly přístupu k určení uživatelů, kteří už nepotřebují přístup. |
| **Existuje příliš mnoho globálních správců** |Nízká |Existuje více globálních správců, než se nedoporučuje. |Pokud máte velký počet globálních správců, je pravděpodobné, že uživatelé získávají více oprávnění než potřebují. Přesuňte uživatele k méně privilegovaným rolím nebo nastavit některé z nich oprávněné pro roli místo přiřazena trvale. |

### <a name="severity"></a>Severity
* **Vysoká**: vyžaduje okamžitý zásah kvůli porušení zásad. 
* **Střední**: není potřeba reagovat okamžitě, ale signály potenciální porušení zásad.
* **Nízká**: není potřeba reagovat okamžitě, ale navrhuje změna preferrable zásad.

## <a name="configure-security-alert-settings"></a>Konfigurace nastavení výstrah zabezpečení
Můžete přizpůsobit některé z výstrah zabezpečení ve službě PIM pro práci s vaším prostředím a cíle zabezpečení. Postupujte podle těchto kroků k dosažení okno nastavení:

1. Přihlaste se k [webu Azure portal](https://portal.azure.com/) a vyberte **Azure AD Privileged Identity Management** dlaždici na řídicím panelu.
2. Vyberte **spravovat privilegované role** > **nastavení** > **nastavení upozornění**.
   
    ![Přejděte do nastavení výstrah zabezpečení](./media/pim-how-to-configure-security-alerts/PIM_security_settings.png)

### <a name="roles-are-being-activated-too-frequently-alert"></a>Výstraha "Role jsou aktivované příliš často"
Tato výstraha se aktivuje, pokud uživatel aktivuje stejné privilegovaných rolí více než jednou v zadaném období. Můžete nakonfigurovat časové období a počet aktivací.

* **Časový rámec prodloužení aktivace**: Zadejte dnů, hodin, minut a druhá časové období, které chcete použít ke sledování podezřelých obnovení.
* **Počet prodloužení aktivace**: Zadejte počet aktivací, od 2 do 100, které považujete za albertových upozornění v časovém rámci, které jste zvolili. Je toto nastavení můžete změnit přesunutím posuvníku nebo zadáním čísla do textového pole.

### <a name="there-are-too-many-global-administrators-alert"></a>Výstraha "Existuje příliš mnoho globálních správců"
PIM aktivuje toto upozornění, pokud jsou splněny dva různých kritérií a můžete nakonfigurovat obě z nich. Nejprve budete muset dosáhne určité prahové hodnoty globální správci. Za druhé procento celkové role přiřazení musí být globální správce. Pokud splňujete pouze jeden z těchto měření, výstraha se nezobrazí.  

* **Minimální počet globálních správců**: Zadejte počet globálních správců, od 2 do 100, vezměte v úvahu nebezpečné množství.
* **Procento globálních správců**: Zadejte procento správce, kteří jsou globálními správci, od 0 % do 100 %, to nebezpečné ve vašem prostředí.

### <a name="administrators-arent-using-their-privileged-roles-alert"></a>Výstraha "Správci nebudou používat svoje privilegované role"
Tato výstraha se aktivuje, pokud uživatel přejde určitou dobu bez aktivace role.

* **Počet dnů**: Zadejte počet dnů od 0 do 100, který uživatel přejít bez aktivace role.

## <a name="next-steps"></a>Další postup
[!INCLUDE [active-directory-privileged-identity-management-toc](../../../includes/active-directory-privileged-identity-management-toc.md)]
