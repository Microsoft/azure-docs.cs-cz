---
title: Jak konfigurovat výstrahy zabezpečení | Microsoft Docs
description: Zjistěte, jak konfigurovat výstrahy zabezpečení pro rozšíření Azure Privileged Identity Management.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.component: protection
ms.date: 06/06/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 9b1779ed409a01356615561b1eb6fea0235529f4
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/08/2018
ms.locfileid: "35233916"
---
# <a name="how-to-configure-security-alerts-in-azure-ad-privileged-identity-management"></a>Jak konfigurovat výstrahy zabezpečení v Azure AD Privileged Identity Management
## <a name="security-alerts"></a>Výstrahy zabezpečení
Azure Správa privilegovaných identit (PIM) generuje výstrahy, když je aktivita podezřelého nebo unsafe ve vašem prostředí. Výstrahy, zobrazí na řídicím panelu PIM. Vyberte výstrahu, pokud chcete zobrazit sestavu obsahující seznam uživatelů nebo rolí, které aktivoval výstrahu.

![Výstrahy zabezpečení PIM řídicí panel – snímek obrazovky][1]

| Výstrahy | Severity | Trigger | Doporučení |
| --- | --- | --- | --- |
| **Role se přiřazují mimo PIM** |Vysoký |Uživatel byl trvale přiřadit privilegované role, mimo PIM rozhraní. |Zkontrolujte uživatele v seznamu a zrušte přiřazení, je z privilegované role přiřazené mimo PIM. |
| **Role jsou příliš často aktivován** |Střednědobé používání |V rámci časový limit v nastavení nebyly příliš mnoho opětovných aktivací stejné role. |Kontaktujte uživatele, který chcete zobrazit, proč se aktivaci role mnoho časy. Možná je příliš krátká pro je k dokončení úkolů, nebo možná používáte skripty mohl automaticky aktivovat roli, časový limit. Ujistěte se, že doba trvání aktivace pro jejich roli je nastaven dostatečně dlouhé, aby se pro ně umožňují vykonávat úkoly. |
| **Role nevyžadují službu Multi-Factor authentication pro aktivaci** |Střednědobé používání |Existuje rolí bez vícefaktorového ověřování povoleno v nastavení. |Jsme vícefaktorové ověřování vyžadovat pro nejvíce vysoce privilegované role, ale důrazně vyzývá povolit MFA pro aktivaci všech rolí. |
| **Uživatelé nejsou pomocí jejich privilegované role** |Nízká |Existují oprávněné správce, kteří nedávno neaktivovali jejich rolí. |Spusťte kontrola přístupu k určení uživatelů, kteří již nepotřebují přístup. |
| **Existuje příliš mnoho globální správci** |Nízká |Existuje více globálních správců, než se nedoporučuje. |Pokud máte velký počet globálních správců, je pravděpodobné, že uživatelé jsou stále větší oprávnění než potřebují. Přesunout uživatelé méně privilegované role, nebo vytvořit některé z nich vhodné pro roli místo trvale přiřazená. |

### <a name="severity"></a>Severity
* **Vysoká**: vyžaduje okamžitý zásah kvůli porušení zásad. 
* **Střední**: nevyžaduje okamžitý zásah, ale signály potenciálních porušení zásad.
* **Nízká**: nevyžaduje okamžitý zásah, ale doporučuje změny preferrable zásad.

## <a name="configure-security-alert-settings"></a>Konfigurace nastavení výstrah zabezpečení
Můžete přizpůsobit některé z výstrah zabezpečení v PIM pro práci s prostředím a cíle zabezpečení. Postupujte podle těchto kroků k dosažení okně nastavení:

1. Přihlaste se k [portál Azure](https://portal.azure.com/) a vyberte **Azure AD Privileged Identity Management** dlaždici na řídicím panelu.
2. Vyberte **spravovat privilegované role** > **nastavení** > **výstrahy nastavení**.
   
    ![Přejděte do nastavení výstrah zabezpečení][2]

### <a name="roles-are-being-activated-too-frequently-alert"></a>Výstraha "Role jsou aktivované příliš často"
Tato výstraha aktivuje, pokud uživatel aktivuje stejné privilegované role několikrát v zadaném období. Můžete nakonfigurovat časové období a počet aktivací.

* **Obnovení aktivace časový rámec**: Zadejte počet dnů, hodin, minut a druhé časové období, které chcete použít ke sledování podezřelých obnovení.
* **Počet obnovení aktivace**: Zadejte počet aktivací od 2 do 100, které považujete za worthy výstrahy v časovém rámci jste zvolili. Je toto nastavení můžete změnit přesunutím posuvníku nebo zadáním čísla do textového pole.

### <a name="there-are-too-many-global-administrators-alert"></a>Výstraha "Existuje příliš mnoho globální správci"
PIM aktivuje tuto výstrahu, pokud jsou splněny dva různé kritéria a nakonfigurujete oba dva. Nejprve budete muset dosáhne určité prahové hodnoty globálních správců. Procento vaše přiřazení role celkový druhý, musí být globální správce. Pokud splňujete pouze jeden z těchto měření, výstraha se nezobrazí.  

* **Minimální počet globálních správců**: Zadejte počet globálních správců, od 2 do 100, vezměte v úvahu dobu unsafe.
* **Procento globálních správců**: Zadejte procento správci, kteří jsou globální správci, od 0 % na 100 %, která nebezpečné ve vašem prostředí.

### <a name="administrators-arent-using-their-privileged-roles-alert"></a>Výstraha "Správci nejsou pomocí jejich privilegované role"
Tato výstraha aktivuje, pokud uživatel přejde určitou dobu bez aktivace role.

* **Počet dnů**: Zadejte počet dnů od 0 do 100, který uživatel může přejít bez aktivace role.

## <a name="next-steps"></a>Další postup
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-how-to-configure-security-alerts/PIM_security_dash.png
[2]: ./media/active-directory-privileged-identity-management-how-to-configure-security-alerts/PIM_security_settings.png
