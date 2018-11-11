---
title: Jak nainstalovat a používat zobrazení Log Analytics pro Azure Active Directory (preview) | Dokumentace Microsoftu
description: Zjistěte, jak nainstalovat a používat zobrazení Log Analytics pro Azure Active Directory (preview)
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 2290de3c-2858-4da0-b4ca-a00107702e26
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 10/01/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 11cc6a69b8072fca0639da5e517a39c22645710e
ms.sourcegitcommit: d372d75558fc7be78b1a4b42b4245f40f213018c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2018
ms.locfileid: "51300502"
---
# <a name="install-and-use-the-log-analytics-views-for-azure-active-directory"></a>Nainstalovat a používat zobrazení Log Analytics pro Azure Active Directory

Azure Active Directory Log Analytics zobrazení pomáhá analyzovat a protokoly aktivit vyhledávání Azure AD ve vašem tenantovi Azure AD. Aktivit služby Azure AD protokolů zahrnují:

* Protokoly auditu: [sestava aktivit protokolů auditu](concept-audit-logs.md) dává vám přístup k historii každé úlohy, které se provádí ve vašem tenantovi.
* Protokoly přihlášení: S [sestavy aktivit přihlašování](concept-sign-ins.md), můžete určit, kdo provedl úlohy, které jsou hlášeny v protokolech auditování.

## <a name="prerequisites"></a>Požadavky

Pokud chcete použít v zobrazeních Log Analytics, budete potřebovat:

* Pracovní prostor Log Analytics ve vašem předplatném Azure. Zjistěte, jak [vytvořit pracovní prostor Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace).
* Nejprve dokončete postup [trasy Azure AD protokolů aktivit do pracovního prostoru Log Analytics](howto-integrate-activity-logs-with-log-analytics.md).
* Stáhněte si zobrazení z [úložiště GitHub](https://aka.ms/AADLogAnalyticsviews) do místního počítače.

## <a name="install-the-log-analytics-views"></a>Instalaci zobrazení Log Analytics

1. Přejděte do pracovního prostoru Log Analytics. Chcete-li to provést, nejprve přejděte do [webu Azure portal](https://portal.azure.com) a vyberte **všechny služby**. Typ **Log Analytics** textového pole a vyberte **Log Analytics**. Vyberte pracovní prostor, můžete směrovat protokoly aktivit, jako součást požadavků.
2. Vyberte **Návrhář zobrazení**vyberte **importovat** a pak vyberte **zvolit soubor** pro import zobrazení ze svého místního počítače.
3. Vyberte zobrazení, které jste si stáhli z požadavků a vyberte **Uložit** uložit importu. Provést tuto akci **události zřizování účtu služby Azure AD** zobrazení a **události přihlášení** zobrazení.

## <a name="use-the-views"></a>Použijte zobrazení

1. Přejděte do pracovního prostoru Log Analytics. Chcete-li to provést, nejprve přejděte do [webu Azure portal](https://portal.azure.com) a vyberte **všechny služby**. Typ **Log Analytics** textového pole a vyberte **Log Analytics**. Vyberte pracovní prostor, můžete směrovat protokoly aktivit, jako součást požadavků.

2. Jakmile budete v pracovním prostoru, vyberte **shrnutí pracovního prostoru**. Měli byste vidět následující tři zobrazení:

    * **Azure AD účet zřizování události**: Toto zobrazení uvádí sestavy k auditování aktivity zajišťování na aktivitu, jako je počet nových uživatelů, které jsou zřízené a selhání zřizování, počet uživatelů, aktualizovat a aktualizovat při selhání a počet uživatelů zruší a odpovídající selhání.    
    * **Události přihlášení**: Toto zobrazení uvádí relevantní zprávy související s monitorování aktivit přihlašování, jako je například přihlášení aplikace, uživatelů, zařízení, stejně jako přehled sledování počet přihlášení v čase.

3. Vyberte některou z těchto zobrazení přejít v jednotlivých sestavách. Můžete také nastavit výstrahy na některý z parametrů sestavy. Například můžeme nastavit výstrahu týkající se pokaždé, když dochází k chybě přihlášení. Chcete-li to provést, vyberte nejdřív **události přihlášení** zobrazit, vyberte možnost **chyby přihlášení v průběhu času** sestavy a pak vyberte **Analytics** otevřete stránku podrobností s samotný dotaz dané sestavy. 

    ![Podrobnosti](./media/howto-install-use-log-analytics-views/details.png)


4. Vyberte **nastavit upozornění**a pak vyberte **je prohledávání protokolů pokaždé, když se vlastní &lt;logika se nedefinovala&gt;**  pod **kritéria upozornění** oddílu. Protože chceme upozornění vždy, když dochází k chybě přihlášení, nastavte **prahová hodnota** z výchozí logika upozornění na **1** a pak vyberte **provádí**. 

    ![Konfigurace logiky signálů](./media/howto-install-use-log-analytics-views/configure-signal-logic.png)

5. Zadejte název a popis výstrahy a nastavit závažnost **upozornění**.

    ![Vytvořit pravidlo](./media/howto-install-use-log-analytics-views/create-rule.png)

6. Vyberte skupinu akcí s výstrahou. Obecně platí může to být buď tým, které chcete zasílat oznámení prostřednictvím e-mailu nebo textovou zprávu nebo může být automatizaci úloh pomocí webhooků, runbooků, functions, logic apps nebo externí řešení ITSM. Zjistěte, jak [vytváření a Správa skupin akcí na webu Azure Portal](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups).

7. Vyberte **vytvořit pravidlo upozornění** k vytvoření upozornění. Teď budete upozorněni pokaždé, když dochází k chybě přihlášení.

## <a name="next-steps"></a>Další postup

* [Jak analyzovat aktivity protokolů v Log Analytics](howto-analyze-activity-logs-log-analytics.md)
* [Začínáme se službou Log Analytics na portálu Azure portal](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-analytics-portal)
