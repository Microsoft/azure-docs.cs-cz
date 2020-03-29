---
title: Konfigurace zásad podmíněného přístupu v režimu pouze pro sestavu – Služba Azure Active Directory
description: Použití režimu pouze pro sestavu v podmíněném přístupu k podpoře při přijetí
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3d9bec829d7fb3e76eb243bda73423303670585e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295119"
---
# <a name="configure-a-conditional-access-policy-in-report-only-mode-preview"></a>Konfigurace zásad podmíněného přístupu v režimu pouze pro sestavu (náhled)

Postup konfigurace zásad podmíněného přístupu v režimu pouze pro sestavu:

> [!IMPORTANT]
> Pokud vaše organizace ještě nemá, [Nastavte azure monitor integrace s Azure AD](#set-up-azure-monitor-integration-with-azure-ad). Tento proces musí proběhnut dříve, než budou údaje k dispozici ke kontrole.

1. Přihlaste se k **portálu Azure** jako správce podmíněného přístupu, správce zabezpečení nebo globální správce.
1. Přejděte na azure **active directory** > **zabezpečení** > podmíněný**přístup**.
1. Vyberte **nové zásady**.
1. Podle potřeby nakonfigurujte podmínky zásad a požadované ovládací prvky udělení.
1. V části **Povolit zásady** nastavte přepínač do **režimu pouze pro sestavu.**
1. Vyberte **Uložit**.

> [!TIP]
> Můžete upravit **povolit** stav zásad existující zásady z **Zapnuto** **pouze pro sestavu,** ale tím zakážete vynucení zásad. 

Zobrazení výsledku pouze sestavy v protokolech přihlášení Azure AD.

Chcete-li zobrazit výsledek zásady pouze sestavy pro konkrétní přihlášení:

1. Přihlaste se k **portálu Azure** jako čtečka sestav, čtečka zabezpečení, správce zabezpečení nebo globální správce.
1. Přejděte na**přihlašovací" služby** **Azure Active Directory** > .
1. Vyberte přihlášení nebo přidejte filtry pro zúžení výsledků.
1. V zásuvce **Podrobnosti** vyberte kartu **Pouze sestava (Náhled), chcete-li** zobrazit zásady vyhodnocené během přihlášení.

> [!NOTE]
> Při stahování protokolů přihlášení zvolte formát JSON, aby zahrnovala data výsledků pouze pro podmíněné přístupy.

## <a name="set-up-azure-monitor-integration-with-azure-ad"></a>Nastavení integrace Azure Monitoru pomocí Azure AD

Chcete-li zobrazit souhrnný dopad zásad podmíněného přístupu pomocí nového sešitu Přehledy podmíněného přístupu, musíte integrovat Azure Monitor s Azure AD a exportovat protokoly přihlášení. Tuto integraci lze nastavit dvěma kroky: 

1. [Zaregistrujte si předplatné Azure Monitoru a vytvořte pracovní prostor](/azure/azure-monitor/learn/quick-create-workspace).
1. [Exportujte protokoly přihlášení z Azure AD do Azure Monitoru](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics).

Další informace o cenách Azure Monitoru najdete na [stránce s cenami Azure Monitoru](https://azure.microsoft.com/pricing/details/monitor/). Zdroje pro odhad nákladů, nastavení denního limitu nebo přizpůsobení doby uchovávání dat najdete v článku [Správa využití a nákladů pomocí protokolů monitorování Azure](../../azure-monitor/platform/manage-cost-storage.md#estimating-the-costs-to-manage-your-environment).

## <a name="view-conditional-access-insights-workbook"></a>Zobrazit sešit Přehledy podmíněného přístupu

Po integraci protokolů Azure AD pomocí Azure Monitoru můžete sledovat dopad zásad podmíněného přístupu pomocí nových sešitů přehledů podmíněného přístupu.

1. Přihlaste se k **portálu Azure** jako správce zabezpečení nebo globální správce.
1. Přejděte do**sešitů** **služby Azure Active Directory** > .
1. Vyberte **přehledy podmíněného přístupu (náhled).**
1. V rozevíracím souboru **Zásady podmíněného přístupu** vyberte jednu nebo více zásad. Všechny povolené zásady jsou vybrány ve výchozím nastavení.
1. Vyberte časový rozsah (pokud časový rozsah překročí dostupnou datovou sadu, sestava zobrazí všechna dostupná data). Po nastavení **zásad podmíněného přístupu** a časového **rozsahu** se sestava načte.
   1. Volitelně můžete vyhledat jednotlivé **uživatele** nebo **aplikace** a zúžit rozsah sestavy.
1. Vyberte mezi zobrazením dat v časovém rozsahu podle počtu uživatelů nebo počtu přihlášení.
1. V závislosti na **zobrazení dat**zobrazuje **souhrn dopadu** počet uživatelů nebo přihlášení v rozsahu zvolených parametrů seskupených podle celkového počtu, **úspěchu**, **neúspěchu**, **požadované akce uživatele**a **nepoužito .** Vyberte dlaždici, která zkontroluje přihlášení určitého typu výsledku. 
   1. Pokud jste změnili parametry sešitu, můžete uložit kopii pro budoucí použití. V horní části sestavy vyberte ikonu uložit a zadejte název a umístění, do kterých chcete uložit.
1. Posunutím dolů zobrazíte rozdělení přihlášení pro každou podmínku.
1. Zobrazení **podrobností o přihlášení** v dolní části sestavy prozkoumat jednotlivé události přihlášení filtrované podle výše uvedených výběrů.

> [!TIP] 
> Potřebujete přejít k podrobnostem o konkrétním dotazu nebo exportovat přihlašovací údaje? Vyberte tlačítko napravo od libovolného dotazu a otevřete dotaz v Log Analytics. Vyberte Exportovat, chcete-li exportovat do CSV nebo Power BI.

## <a name="common-problems-and-solutions"></a>Běžné problémy a řešení

### <a name="why-are-the-queries-in-the-workbook-failing"></a>Proč se dotazy v sešitu nedaří?

Zákazníci si všimli, že dotazy někdy selžou, pokud jsou k sešitu přidruženy nesprávné nebo více pracovních prostorů. Pokud chcete tento problém vyřešit, klikněte v horní části sešitu na **Upravit** a potom na ozubené kolo Nastavení. Vyberte a odeberte pracovní prostory, které nejsou přidruženy k sešitu. Ke každému sešitu by měl být přidružen pouze jeden pracovní prostor.

### <a name="why-doesnt-the-conditional-access-policies-dropdown-parameter-contain-my-policies"></a>Proč rozevírací parametr Zásady podmíněného přístupu neobsahuje mé zásady?

Rozevírací seznam Zásady podmíněného přístupu je naplněn dotazem na nejnovější přihlášení po dobu 4 hodin. Pokud tenant nemá žádné přihlášení v posledních 4 hodinách, je možné, že rozevírací rozbalovací soubor bude prázdný. Pokud toto zpoždění je trvalý problém, například v malých tenantů s zřídka přihlášení, správci můžete upravit dotaz pro zásady podmíněného přístupu rozevírací rozbalovací a prodloužit čas pro dotaz na dobu delší než 4 hodiny.

## <a name="next-steps"></a>Další kroky

[Podmíněné přístupové běžné zásady](concept-conditional-access-policy-common.md)

Další informace o sešitech Azure AD najdete v článku [Jak používat sešity Azure Monitoru pro sestavy Azure Active Directory](../reports-monitoring/howto-use-azure-monitor-workbooks.md).
