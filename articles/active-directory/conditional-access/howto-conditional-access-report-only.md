---
title: Konfigurace zásad podmíněného přístupu v režimu pouze sestavy – Azure Active Directory
description: Použití režimu pouze pro sestavy v podmíněném přístupu k podpoře při přijetí
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: d63aaa0103715a928cdd5332de738a473b329f2e
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2020
ms.locfileid: "77186091"
---
# <a name="configure-a-conditional-access-policy-in-report-only-mode-preview"></a>Konfigurace zásad podmíněného přístupu v režimu pouze sestavy (Preview)

Konfigurace zásad podmíněného přístupu v režimu pouze pro sestavy:

1. Přihlaste se k **Azure Portal** jako správce podmíněného přístupu, správce zabezpečení nebo globální správce.
1. Přejděte na **Azure Active Directory** > **zabezpečení** > **podmíněný přístup**.
1. Vyberte **nové zásady**.
1. Podle potřeby konfigurujte podmínky zásad a požadované ovládací prvky pro udělení oprávnění.
1. V části **Povolit zásadu** nastavte přepínač na režim **pouze pro sestavy** .
1. Vyberte **Uložit**.

> [!TIP]
> Můžete upravit stav **Povolit zásadu Povolit** u existující zásady z **na** na **jenom sestavy** , ale zabráníte tak vynucení zásad. 

Zobrazit v protokolech přihlášení služby Azure AD výsledek jenom pro sestavu.

Zobrazení výsledku zásad pouze pro sestavy pro konkrétní přihlášení:

1. Přihlaste se k **Azure Portal** jako čtenář sestav, čtecí modul zabezpečení, správce zabezpečení nebo globální správce.
1. Přejděte na **Azure Active Directory** > **přihlášení**.
1. Vyberte přihlášení nebo přidejte filtry pro zúžení výsledků.
1. V zásuvce **podrobností** vyberte kartu **pouze sestava (Preview)** a zobrazte zásady vyhodnocené během přihlašování.

> [!NOTE]
> Když stahujete protokoly přihlášení, vyberte formát JSON, který bude obsahovat data o výsledku podmíněného přístupu pouze sestavy.

## <a name="set-up-azure-monitor-integration-with-azure-ad"></a>Nastavení Integrace Azure Monitor s využitím Azure AD

Aby bylo možné zobrazit agregovaný dopad zásad podmíněného přístupu pomocí nového sešitu pro podmíněný přístup, musíte Azure Monitor s Azure AD integrovat a exportovat protokoly přihlášení. Pro nastavení této integrace existují dva kroky: 

1. [Zaregistrujte si předplatné Azure monitor a vytvořte pracovní prostor](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).
1. [Exportujte protokoly přihlášení z Azure AD do Azure monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics).

Další informace o cenách Azure Monitor najdete na [stránce Azure monitor ceny](https://azure.microsoft.com/pricing/details/monitor/). Prostředky k odhadování nákladů, nastavení denního limitu nebo přizpůsobení období uchovávání dat najdete v článku, [Správa využití a nákladů pomocí Azure Monitorch protokolů](../../azure-monitor/platform/manage-cost-storage.md#estimating-the-costs-to-manage-your-environment).

## <a name="view-conditional-access-insights-workbook"></a>Zobrazit sešit s informacemi o podmíněném přístupu

Po integraci protokolů služby Azure AD s Azure Monitor můžete sledovat dopad zásad podmíněného přístupu pomocí nových sešitů s přehledem podmíněného přístupu.

1. Přihlaste se k **Azure Portal** jako správce zabezpečení nebo globální správce.
1. Přejděte na **Azure Active Directory** > **sešity**.
1. Vyberte **přehledy podmíněného přístupu (Preview)** .
1. V rozevíracím seznamu **zásady podmíněného přístupu** vyberte jednu nebo víc zásad. Ve výchozím nastavení jsou vybrány všechny povolené zásady.
1. Vyberte časový rozsah (Pokud časový rozsah překračuje dostupnou datovou sadu, sestava zobrazí všechna dostupná data). Jakmile nastavíte **zásady podmíněného přístupu** a parametry **časového rozsahu** , sestava se načte.
   1. Volitelně můžete vyhledat jednotlivé **uživatele** nebo **aplikace** a zúžit tak rozsah sestavy.
1. Vyberte mezi zobrazením dat v časovém rozsahu podle počtu uživatelů nebo počtu přihlášení.
1. V závislosti na **zobrazení dat**zobrazuje **Souhrn dopadu** počet uživatelů nebo přihlášení v rozsahu zvolených parametrů, seskupený podle celkového počtu, **úspěšnosti**, **selhání**, **akce uživatele**a **Nepoužito**. Vyberte dlaždici pro kontrolu přihlášení určitého typu výsledku. 
   1. Pokud jste změnili parametry sešitu, můžete zvolit uložení kopie pro budoucí použití. V horní části sestavy vyberte ikonu Uložit a zadejte název a umístění, do kterého chcete ukládat.
1. Posuňte se dolů a zobrazte rozpis přihlášení pro každou podmínku.
1. Podívejte se na **Podrobnosti o přihlášení** v dolní části sestavy, abyste mohli prozkoumat jednotlivé události přihlašování filtrované podle výše uvedených možností.

> [!TIP] 
> Potřebujete přejít k podrobnostem konkrétního dotazu nebo exportovat přihlašovací údaje? Kliknutím na tlačítko napravo od libovolného dotazu otevřete dotaz v Log Analytics. Vyberte Exportovat pro export do sdíleného svazku clusteru nebo Power BI.

## <a name="common-problems-and-solutions"></a>Nejčastější problémy a jejich řešení

### <a name="why-are-the-queries-in-the-workbook-failing"></a>Proč se dotazy v sešitu nedaří?

Zákazníci si všimli, že dotazy někdy selžou, pokud jsou k sešitu přidruženy nesprávné nebo více pracovních prostorů. Pokud chcete tento problém vyřešit, klikněte v horní části sešitu na **Upravit** a pak na nastavení ozubeného kola. Vyberte a potom odeberte pracovní prostory, které nejsou přidružené k sešitu. K jednotlivým sešitům by měl být přidružen pouze jeden pracovní prostor.

### <a name="why-doesnt-the-conditional-access-policies-dropdown-parameter-contain-my-policies"></a>Proč parametr DropDown zásad podmíněného přístupu neobsahuje moje zásady?

V rozevíracím seznamu zásady podmíněného přístupu se vyplní dotazování nejaktuálnějšího přihlášení během období 4 hodin. Pokud tenant nemá v posledních 4 hodinách žádné přihlášení, je možné, že rozevírací seznam bude prázdný. Pokud je tato prodleva trvalým problémem, například v malých klientech s nečastými přihlášeními, mohou správci upravit dotaz u rozevíracího seznamu zásady podmíněného přístupu a prodloužit dobu trvání dotazu na dobu delší než 4 hodiny.

## <a name="next-steps"></a>Další kroky

[Společné zásady podmíněného přístupu](concept-conditional-access-policy-common.md)

Další informace o sešitech Azure AD najdete v článku [Jak používat Azure monitor sešity pro Azure Active Directory sestavy](../reports-monitoring/howto-use-azure-monitor-workbooks.md).
