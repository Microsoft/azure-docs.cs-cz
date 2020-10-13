---
title: Jak nainstalovat a používat zobrazení Log Analytics | Microsoft Docs
description: Naučte se instalovat a používat zobrazení Log Analytics pro Azure Active Directory
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 2290de3c-2858-4da0-b4ca-a00107702e26
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 168b41534f6ea6055294cc9e9cec139853904fea
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91358895"
---
# <a name="install-and-use-the-log-analytics-views-for-azure-active-directory"></a>Instalace a použití zobrazení Log Analytics pro Azure Active Directory

Azure Active Directory zobrazení Log Analytics pomáhá analyzovat a hledat protokoly aktivit Azure AD ve vašem tenantovi Azure AD. Protokoly aktivit Azure AD zahrnují:

* Protokoly auditu: [Sestava aktivity protokoly auditu](concept-audit-logs.md) vám poskytne přístup k historii všech úloh, které provedete ve vašem tenantovi.
* Protokoly přihlašování: pomocí [sestavy aktivita přihlášení](concept-sign-ins.md)můžete určit, kdo provedl úkoly hlášené v protokolech auditu.

## <a name="prerequisites"></a>Požadavky

Pokud chcete používat zobrazení Log Analytics, potřebujete:

* Log Analytics pracovní prostor ve vašem předplatném Azure. Naučte se, jak [vytvořit pracovní prostor Log Analytics](../../azure-monitor/learn/quick-create-workspace.md).
* Nejdřív proveďte kroky pro [Směrování protokolů aktivit služby Azure AD do vašeho pracovního prostoru Log Analytics](howto-integrate-activity-logs-with-log-analytics.md).
* Stáhněte si zobrazení z [úložiště GitHub](https://aka.ms/AADLogAnalyticsviews) do místního počítače.

## <a name="install-the-log-analytics-views"></a>Instalace zobrazení Log Analytics

1. Přejděte do pracovního prostoru Log Analytics. Provedete to tak, že nejprve přejdete na [Azure Portal](https://portal.azure.com) a vyberete **všechny služby**. Do textového pole zadejte **Log Analytics** a vyberte **Log Analytics pracovní prostory**. V rámci požadavků vyberte pracovní prostor, do kterého jste nasměrovali protokoly aktivit.
2. Vyberte **Návrhář zobrazení**, vyberte **importovat** a pak vyberte **zvolit soubor** . tím importujete zobrazení z místního počítače.
3. Vyberte zobrazení, která jste si stáhli z požadavků, a kliknutím na **Uložit** uložte import. Udělejte to pro zobrazení **události zřizování účtu Azure AD** a zobrazení **událostí přihlášení** .

## <a name="use-the-views"></a>Použití zobrazení

1. Přejděte do pracovního prostoru Log Analytics. Provedete to tak, že nejprve přejdete na [Azure Portal](https://portal.azure.com) a vyberete **všechny služby**. Do textového pole zadejte **Log Analytics** a vyberte **Log Analytics pracovní prostory**. V rámci požadavků vyberte pracovní prostor, do kterého jste nasměrovali protokoly aktivit.

2. Až budete v pracovním prostoru, vyberte **Přehled pracovního prostoru**. Měli byste vidět následující tři zobrazení:

    * **Události zřizování účtů Azure AD**: Toto zobrazení ukazuje sestavy týkající se aktivity zřizování auditování, jako je počet nových uživatelů, kteří se zřídili a provisionují chyby, počet uživatelů, kteří se aktualizovaly a aktualizují selhání, a počet nezřízených a odpovídajících selhání uživatelů.    
    * **Události přihlášení**: Toto zobrazení ukazuje nejrelevantnější sestavy týkající se monitorování přihlašovacích aktivit, jako jsou například přihlášení podle aplikace, uživatele, zařízení a také souhrnné zobrazení sledování počtu přihlášení v průběhu času.

3. Vyberte jedno z těchto zobrazení pro přechod do jednotlivých sestav. Výstrahy můžete také nastavit pro libovolný parametr sestavy. Můžete například nastavit výstrahu pro pokaždé, když dojde k chybě přihlášení. Pokud to chcete provést, vyberte nejprve zobrazení **události přihlášení** , vyberte možnost **chyby přihlášení v průběhu** sestavy a potom vyberte **Analytics** . otevře se stránka s podrobnostmi s skutečným dotazem za sestavu. 

    ![Snímek obrazovky zobrazující stránku podrobností analýzy, která obsahuje dotaz na sestavu.](./media/howto-install-use-log-analytics-views/details.png)


4. Vyberte **nastavit výstrahu**a potom vyberte pokaždé, když je v části **kritéria výstrahy** vybraná možnost ** &lt; &gt; nedefinovaná logika vlastního protokolu** . Vzhledem k tomu, že chceme upozornit vždy, když dojde k chybě přihlášení, nastavte **prahovou hodnotu** výchozí logiky výstrahy na **1** a potom vyberte **Hotovo**. 

    ![Konfigurace logiky signálů](./media/howto-install-use-log-analytics-views/configure-signal-logic.png)

5. Zadejte název a popis výstrahy a nastavte závažnost na **Warning**.

    ![Vytvořit pravidlo](./media/howto-install-use-log-analytics-views/create-rule.png)

6. Vyberte skupinu akcí pro upozornění. Obecně to může být tým, kterému chcete poslat oznámení e-mailem nebo textovou zprávou, nebo se může jednat o automatizovaný úkol pomocí webhooků, runbooků, funkcí, Logic Apps nebo externích řešení ITSM. Naučte se [vytvářet a spravovat skupiny akcí v Azure Portal](../../azure-monitor/platform/action-groups.md).

7. Vyberte **vytvořit pravidlo výstrahy** a vytvořte výstrahu. Nyní se zobrazí upozornění pokaždé, když dojde k chybě přihlášení.

## <a name="next-steps"></a>Další kroky

* [Postup analýzy protokolů aktivit pomocí protokolů Azure Monitor](howto-analyze-activity-logs-log-analytics.md)
* [Začínáme s protokoly Azure Monitor v Azure Portal](../../azure-monitor/log-query/get-started-portal.md)