---
title: Azure Monitor sešitů pro sestavy | Microsoft Docs
description: Naučte se používat Azure Monitor sešity pro sestavy Azure Active Directory.
services: active-directory
author: MarkusVi
manager: daveba
ms.assetid: 4066725c-c430-42b8-a75b-fe2360699b82
ms.service: active-directory
ms.devlang: ''
ms.topic: how-to
ms.tgt_pltfrm: ''
ms.workload: identity
ms.subservice: report-monitor
ms.date: 10/30/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: b84c38b28b51f867160272883465242fc81ff2bf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100588054"
---
# <a name="how-to-use-azure-monitor-workbooks-for-azure-active-directory-reports"></a>Použití Azure Monitorch sešitů pro sestavy Azure Active Directory

> [!IMPORTANT]
> Aby bylo možné optimalizovat podkladové dotazy v tomto sešitu, klikněte na tlačítko Upravit, klikněte na ikonu nastavení a vyberte pracovní prostor, ve kterém chcete spustit tyto dotazy. V sešitech ve výchozím nastavení se vyberou všechny pracovní prostory, ve kterých směrujete protokoly služby Azure AD. 

Chcete:

- Seznámení se s vlivem [zásad podmíněného přístupu](../conditional-access/overview.md) na přihlašovací prostředí vašich uživatelů?

- Řešení potíží s přihlašováním kvůli lepšímu zobrazení stavu přihlášení vaší organizace a rychlé řešení problémů?

- Víte, kdo pro přihlášení k prostředí používá starší verze ověřování? ( [Blokováním staršího ověřování](../conditional-access/block-legacy-authentication.md)můžete zlepšit ochranu vašeho klienta.)

- Potřebujete pochopit dopad zásad podmíněného přístupu ve vašem tenantovi?

- Chcete mít možnost kontrolovat: dotazy protokolu přihlášení, sešit, na kolik uživatelům bylo uděleno nebo odepřen přístup, a také počet uživatelů, kteří se při přístupu k prostředkům používají zásady podmíněného přístupu?

- Zajímá Vás hlubší porozumění: podrobnosti o sešitu za podmínku, aby bylo možné určit dopad zásad v kontextu na podmínku, včetně platformy zařízení, stavu zařízení, klientské aplikace, rizika přihlašování, umístění a aplikace?

- Získejte podrobnější přehled o dotazech na přihlášení. Tento sešit oznamuje, kolik uživatelů bylo uděleno nebo odepřeno přístupu, a kolik uživatelů při přístupu k prostředkům nepoužívá zásady podmíněného přístupu.

- Abychom vám pomohli vyřešit tyto otázky, Azure Active Directory poskytuje sešity pro monitorování. [Azure monitor sešity](../../azure-monitor/visualize/workbooks-overview.md) kombinují text, analytické dotazy, metriky a parametry do propracovaných interaktivních sestav.



Tento článek:

- Předpokládá, že máte zkušenosti s [vytvářením interaktivních sestav pomocí monitorování sešitů](../../azure-monitor/visualize/workbooks-overview.md).

- Vysvětluje, jak pomocí monitorovat sešity pochopit účinek zásad podmíněného přístupu, vyřešit problémy s přihlášením a identifikovat starší verze ověřování.
 


## <a name="prerequisites"></a>Předpoklady

Pokud chcete používat monitorované sešity, potřebujete:

- Tenant Azure Active Directory s licencí Premium (P1 nebo P2). Přečtěte si, jak [získat licenci](../fundamentals/active-directory-get-started-premium.md)na úrovni Premium.

- [Pracovní prostor služby Log Analytics](../../azure-monitor/logs/quick-create-workspace.md).

- [Přístup](../../azure-monitor/logs/manage-access.md#manage-access-using-workspace-permissions) k pracovnímu prostoru Log Analytics
- Následující role v Azure Active Directory (Pokud přistupujete Log Analytics prostřednictvím Azure Active Directoryového portálu)
    - Správce zabezpečení
    - Čtenář zabezpečení
    - Čtečka sestav
    - Globální správce

## <a name="roles"></a>Role
Musíte být v jedné z následujících rolí a mít [přístup k podkladovým Log Analytics](../../azure-monitor/logs/manage-access.md#manage-access-using-azure-permissions) pracovním prostorům pro správu sešitů:
-   Globální správce
-   Správce zabezpečení
-   Čtenář zabezpečení
-   Čtečka sestav
-   Správce aplikace

## <a name="workbook-access"></a>Přístup k sešitu 

Přístup k sešitům:

1. Přihlaste se na [Azure Portal](https://portal.azure.com).

1. Přejděte na **Azure Active Directory**  >  **monitorování**  >  **sešitů**. 

1. Vyberte sestavu nebo šablonu nebo na panelu nástrojů vyberte **otevřít**. 

![Hledání sešitů Azure Monitor ve službě Azure AD](./media/howto-use-azure-monitor-workbooks/azure-monitor-workbooks-in-azure-ad.png)

## <a name="sign-in-analysis"></a>Analýza přihlášení

Chcete-li získat přístup k sešitu analýzy přihlašování, vyberte v části **využití** možnost **přihlášení**. 

Tento sešit obsahuje následující trendy při přihlašování:

- Všechna přihlášení

- Success

- Nedokončená akce uživatele

- Selhání

Jednotlivé trendy můžete filtrovat podle následujících kategorií:

- Časové rozmezí

- Aplikace

- Uživatelé

![Analýza přihlášení](./media/howto-use-azure-monitor-workbooks/43.png)


Pro každý trend získáte rozpis podle následujících kategorií:

- Umístění

    ![Přihlášení podle umístění](./media/howto-use-azure-monitor-workbooks/45.png)

- Zařízení

    ![Přihlášení podle zařízení](./media/howto-use-azure-monitor-workbooks/46.png)


## <a name="sign-ins-using-legacy-authentication"></a>Přihlášení pomocí starší verze ověřování 


Pokud chcete získat přístup k sešitu pro přihlášení, která používají [starší verze ověřování](../conditional-access/block-legacy-authentication.md), v části **využití** vyberte **přihlášení pomocí starší verze ověřování**. 

Tento sešit obsahuje následující trendy při přihlašování:

- Všechna přihlášení

- Success


Jednotlivé trendy můžete filtrovat podle následujících kategorií:

- Časové rozmezí

- Aplikace

- Uživatelé

- Protokoly

![Přihlášení pomocí starší verze ověřování](./media/howto-use-azure-monitor-workbooks/47.png)


Pro každý trend získáte rozpis podle aplikace a protokolu.

![Starší verze ověřování – přihlášení podle aplikace a protokolu](./media/howto-use-azure-monitor-workbooks/48.png)



## <a name="sign-ins-by-conditional-access"></a>Přihlášení podle podmíněného přístupu 


Chcete-li získat přístup k sešitu pro přihlášení podle [zásad podmíněného](../conditional-access/overview.md)přístupu, vyberte v části **podmíněný přístup** možnost **přihlášení podle podmíněného přístupu**. 

Tento sešit zobrazuje trendy pro zakázané přihlášení. Jednotlivé trendy můžete filtrovat podle následujících kategorií:

- Časové rozmezí

- Aplikace

- Uživatelé

![Přihlášení pomocí podmíněného přístupu](./media/howto-use-azure-monitor-workbooks/49.png)


Pro zakázaná přihlášení získáte rozpis stavu podmíněného přístupu.

![Snímek obrazovky zobrazuje stav podmíněného přístupu a Poslední přihlášení.](./media/howto-use-azure-monitor-workbooks/conditional-access-status.png)


## <a name="conditional-access-insights"></a>Přehled podmíněného přístupu

### <a name="overview"></a>Přehled

Sešity obsahují dotazy protokolu přihlášení, které mohou správcům IT pomáhat monitorovat dopad zásad podmíněného přístupu ve svém tenantovi. Máte možnost nahlásit, kolik uživatelů bylo uděleno nebo odepřeno přístupu. Sešit obsahuje přehledy o tom, kolik uživatelů se v době přihlášení na základě atributů těchto uživatelů vynechá. Obsahuje podrobnosti za podmínku, takže dopad zásad může být kontextem na podmínku, včetně platformy zařízení, stavu zařízení, klientské aplikace, rizika přihlášení, umístění a aplikace.

### <a name="instructions"></a>Pokyny 
Pokud chcete získat přístup k sešitu pro přehledy podmíněného přístupu, vyberte v části Podmíněný přístup sešit s **informacemi o podmíněném** přístupu. V tomto sešitu se zobrazuje očekávaný dopad na jednotlivé zásady podmíněného přístupu ve vašem tenantovi. V rozevíracím seznamu vyberte jednu nebo více zásad podmíněného přístupu a zužte rozsah sešitu použitím následujících filtrů: 

- **Časové rozmezí**

- **Uživatel**

- **Aplikace**

- **Zobrazení dat**

![Snímek obrazovky se zobrazí v podokně podmíněný přístup, kde můžete vybrat zásadu podmíněného přístupu.](./media/howto-use-azure-monitor-workbooks/access-insights.png)


Souhrn dopadů zobrazuje počet uživatelů nebo přihlášení, pro které měly vybrané zásady určitý výsledek. Total je počet uživatelů nebo přihlášení, pro které se vybrané zásady vyhodnotily ve vybraném časovém rozsahu. Kliknutím na dlaždici vyfiltrujete data v sešitu podle tohoto typu výsledku. 

![Snímek obrazovky zobrazuje dlaždice, které slouží k filtrování výsledků, jako je součet, úspěch a selhání.](./media/howto-use-azure-monitor-workbooks/impact-summary.png)

V tomto sešitu se také zobrazuje dopad vybraných zásad, které jsou rozdělené podle všech šesti podmínek: 
- **Stav zařízení**
- **Platforma zařízení**
- **Klientské aplikace**
- **Riziko přihlášení**
- **Umístění**
- **Aplikace**

![Snímek obrazovky zobrazuje podrobnosti z filtru celkový počet přihlášení.](./media/howto-use-azure-monitor-workbooks/device-platform.png)

Můžete také prozkoumat jednotlivá přihlášení filtrovaná podle parametrů vybraných v sešitu. Vyhledávejte jednotlivé uživatele, seřazené podle četnosti přihlašování a Prohlédněte si jejich odpovídající přihlašovací události. 

![Snímek obrazovky zobrazuje jednotlivá přihlášení, která můžete zkontrolovat.](./media/howto-use-azure-monitor-workbooks/filtered.png)

## <a name="sign-ins-by-grant-controls"></a>Přihlášení pomocí ovládacích prvků grant

Chcete-li získat přístup k sešitu pro přihlášení prostřednictvím [udělení ovládacích prvků](../conditional-access/controls.md), v části **podmíněný přístup** vyberte možnost **přihlášení pomocí udělení ovládacích prvků**. 

Tento sešit obsahuje následující zakázané trendy přihlašování:

- Vyžadování MFA
 
- Vyžadování podmínek použití

- Vyžadovat prohlášení o zásadách ochrany osobních údajů

- Jiné


Jednotlivé trendy můžete filtrovat podle následujících kategorií:

- Časové rozmezí

- Aplikace

- Uživatelé

![Přihlášení pomocí ovládacích prvků grant](./media/howto-use-azure-monitor-workbooks/50.png)


Pro každý trend získáte rozpis podle aplikace a protokolu.

![Rozpis nedávných přihlášení](./media/howto-use-azure-monitor-workbooks/51.png)




## <a name="sign-ins-failure-analysis"></a>Analýza selhání přihlášení

Použijte sešit **analýzy selhání přihlášení** k řešení chyb s:

- Přihlášení
- Zásady podmíněného přístupu
- Starší verze ověřování 


Chcete-li získat přístup k přihlášení podle dat podmíněného přístupu, vyberte v části **Poradce při potížích** možnost **přihlášení pomocí starší verze ověřování**. 

Tento sešit obsahuje následující trendy při přihlašování:

- Všechna přihlášení

- Success

- Akce čeká na vyřízení

- Selhání


Jednotlivé trendy můžete filtrovat podle následujících kategorií:

- Časové rozmezí

- Aplikace

- Uživatelé

![Řešení potíží s přihlášením](./media/howto-use-azure-monitor-workbooks/52.png)


Při řešení potíží s přihlášením vám Azure Monitor poskytuje rozpis podle následujících kategorií:

- Hlavní chyby

    ![Shrnutí hlavních chyb](./media/howto-use-azure-monitor-workbooks/53.png)

- Přihlášení čekající na akci uživatele

    ![Shrnutí přihlášení čekajících na akci uživatele](./media/howto-use-azure-monitor-workbooks/54.png)






## <a name="next-steps"></a>Další kroky

[Vytváření interaktivních sestav pomocí sledování sešitů](../../azure-monitor/visualize/workbooks-overview.md).