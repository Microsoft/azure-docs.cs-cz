---
title: Nejčastější dotazy pro Azure náklady na správu | Microsoft Docs
description: Poskytuje odpovědi na některé běžné otázky o Azure náklady na správu.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 04/26/2018
ms.topic: troubleshooting
ms.service: cost-management
manager: dougeby
ms.custom: ''
ms.openlocfilehash: af6e15a86107aeb74a20c3e78a8f91f07d6d377a
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="frequently-asked-questions-for-azure-cost-management"></a>Nejčastější dotazy pro Azure náklady na správu

Tento článek se zaměřuje na některé běžné dotazy týkající se Azure náklady na správu (také označované jako Cloudyn). Pokud máte dotazy týkající se náklady na správu, můžete je v požádat [časté otázky k Azure náklady na správu](https://social.msdn.microsoft.com/Forums/en-US/231bf072-2c71-4121-8339-ac9d868137b9/faqs-for-azure-cost-management-by-cloudyn?forum=Cloudyn).

## <a name="how-can-i-resolve-common-indirect-enterprise-setup-problems"></a>Jak můžete řešení běžných problémů s instalací nepřímých enterprise?

Při prvním použití portálu Cloudyn, může zobrazit následující zprávy, pokud se uživatel s microsoftem smlouvu Enterprise nebo Cloud Solution Provider (CSP):

- "K zadanému klíči rozhraní API není klíč nejvyšší úrovně zápisu" zobrazí v **nastavit až Azure náklady na správu** průvodce.
- "Přímá registrace – ne" zobrazí na portálu smlouvy Enterprise.
- "Za posledních 30 dní nebyla nalezena žádná data o využití. Obraťte se na vaše distributora a ujistěte se, že jste povolili značek k účtu Azure"zobrazí na portálu Cloudyn.

Předchozí zprávy naznačují, že jste zakoupili smlouvu Enterprise Agreement Azure přes prodejce nebo poskytovatel CSP. Svého prodejce nebo zprostředkovatele kryptografických služeb je potřeba povolit _značek_ pro vaši Azure účet tak, aby vaše data můžete zobrazit v Cloudyn.

Chcete-li opravit problémy:

1. Váš prodejce je potřeba povolit _značek_ pro váš účet. Pokyny najdete v tématu [nepřímých Průvodce registrací ve službě zákazníka](https://ea.azure.com/api/v3Help/v2IndirectCustomerOnboardingGuide).

2. Vygenerování klíče smlouvou Azure Enterprise pro použití s Cloudyn. Pokyny najdete v tématu [přidání vaše EA Azure](https://support.cloudyn.com/hc/en-us/articles/210429585-Adding-Your-AZURE-EA) nebo [jak najít vaše EA registrace ID a klíč rozhraní API](https://youtu.be/u_phLs_udig).

Pouze správce služby Azure můžete povolit náklady na správu. Oprávnění správce společné nestačí.

Než můžete vygenerovat klíč rozhraní API smlouvy Enterprise Azure nastavit Cloudyn, musíte povolit fakturace rozhraní API služby Azure podle těchto pokynů:

- [Přehled rozhraní API pro vytváření sestav pro podnikové zákazníky](../billing/billing-enterprise-api.md)
- [Portál Microsoft Azure enterprise rozhraní API pro vytváření sestav](https://ea.azure.com/helpdocs/reportingAPI) pod **povolení přístupu k rozhraní API datům**


Také možná budete muset poskytnout správci oddělení, účet vlastníků a enterprise administrators oprávnění k _zobrazit poplatky_ s rozhraním API fakturace.

## <a name="why-dont-i-see-optimizer-recommendations"></a>Proč nevidím doporučení pro optimalizaci?

Informace o doporučení je dostupná jenom pro účty, které jsou aktivované. Neuvidíte žádné informace doporučení v **Optimalizátor** sestavy kategorie pro účty, které jsou *neaktivovaných*, včetně:

- Optimalizace Manager
- Změna velikosti optimalizace
- Umožňuje zvýšit efektivitu

Pokud nelze zobrazit žádná data Optimalizátor doporučení, pravděpodobně máte účty, které neaktivovaných. Chcete-li aktivovat účet, zaregistrovat ji pomocí přihlašovacích údajů Azure.

Aktivovat účet:

1.  Na portálu Cloudyn klikněte v pravém horním rohu na **Settings** (Nastavení) a vyberte **Cloud Accounts** (Účty v cloudu).
2.  Na kartě účty Microsoft Azure vyhledejte účty, které mají **neaktivovaných** předplatné.
3.  Napravo od neaktivovaných účet, klikněte **upravit** symbol, který vypadá takto: tužky.
4.  ID a rychlost ID klienta je automaticky zjištěna. Klikněte na **Další**.
5.  Budete přesměrováni na portálu Azure. Přihlaste se k portálu a autorizaci Cloudyn kolekce pro přístup k datům v Azure.
6.  V dalším kroku budete přesměrováni na stránku správy Cloudyn účty a předplatného je aktualizováno **active** stav účtu. Zobrazuje symbol zelená značka zaškrtnutí.
7.  Pokud nevidíte symbol zeleného zaškrtnutí pro jeden nebo více odběrů, znamená to, že nemáte oprávnění k vytvoření čtečky aplikace (CloudynCollector) pro předplatné. Uživatel s vyšší oprávnění pro odběr je potřeba zopakujte kroky 3 a 4.  

Po dokončení předchozího postupu můžete zobrazit doporučení pro optimalizaci do jedné nebo dvou dnů. Však může trvat až pět dní před úplná optimalizace data nejsou k dispozici.


## <a name="how-do-i-enable-suspended-or-locked-out-users"></a>Jak povolit odloženou nebo zamčený uživatelů?

Pokud obdržíte výstrahu s požadavkem pro povolení přístupu pro uživatele, musíte aktivovat uživatelský účet.

Chcete-li aktivovat uživatelský účet:

1. Přihlaste se k Cloudyn pomocí účtu Azure administrativní uživatel, který jste použili k nastavení Cloudyn. Nebo, přihlaste se pomocí uživatelského účtu, který byl udělen přístup správce.

2. Vyberte symbol ozubené kolečko v pravém horním rohu a vyberte **Správa uživatelů**.

3. Vyhledejte uživatele, vyberte symbol tužky a pak upravte uživatele.

4. V části **stav uživatele**, změnit stav z **pozastaveno** k **Active**.

Uživatelské účty Cloudyn připojit pomocí jednotného přihlašování z Azure. Pokud uživatel mistypes své heslo, se může zamknout z Cloudyn, i když se pořád přístup k Azure.

Pokud změníte e-mailovou adresu v Cloudyn z výchozí adresu v Azure, můžete získat uzamčení účtu. Může se zobrazit "stav initiallySuspended." Pokud je váš uživatelský účet uzamčen, obraťte se na alternativní správce k resetování vašeho účtu.

Doporučujeme vytvořit aspoň dva účty správců Cloudyn, v případě, že jeden z účtů získá uzamčen.

Pokud nemůžete se přihlásit k portálu Cloudyn, ujistěte se, že používáte správnou adresu URL Azure náklady na správu pro přihlášení k Cloudyn. Použití [ https://azure.cloudyn.com ](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/CloudynMainBlade).

Nepoužívejte přímá adresa URL Cloudyn https://app.cloudyn.com.

## <a name="how-do-i-activate-unactivated-accounts-with-azure-credentials"></a>Jak lze aktivovat neaktivovaných účtů s přihlašovacími údaji Azure?

Jakmile Cloudyn zjistí vaše účty Azure, náklady na data je okamžitě uvedená v sestav na základě nákladů. Ale pro Cloudyn poskytující data o využití a výkonu, musíte zaregistrovat vaše přihlašovací údaje Azure pro účty. Pokyny najdete v tématu [přidat Azure Resource Manager](https://support.cloudyn.com/hc/en-us/articles/212784085-Adding-Azure-Resource-Manager).

Přidat přihlašovací údaje Azure pro účet, na portálu Cloudyn, vyberte symbol upravit napravo od název účtu není předplatné.

Dokud vaše přihlašovací údaje Azure jsou přidány do Cloudyn, účet se zobrazí jako _zrušení aktivovaný_.

## <a name="how-do-i-add-multiple-accounts-and-entities-to-an-existing-subscription"></a>Jak přidat více účtů a entity k existujícímu předplatnému?

Další entity slouží k další Enterprise smlouvy přidávat do Cloudyn předplatné. Následující odkazy popisují, jak přidat další entity:

- [Přidání Entity](https://support.cloudyn.com/hc/en-us/articles/212016145-Adding-an-Entity) článku
- [Definování hierarchii s náklady na entity](https://support.cloudyn.com/hc/en-us/articles/115005142529-Video-Defining-your-hierarchy-with-Cost-Entities) video

Pro poskytovatele cloudových služeb:

Chcete-li přidat další účty CSP na entitu, vyberte **MSP přístup** místo **Enterprise** při vytváření nové entity. Pokud váš účet je registrován jako smlouvu Enterprise Agreement a chcete přidat přihlašovací údaje poskytovatele CSP, pracovníky technické podpory Cloudyn možná muset upravit nastavení svého účtu. Pokud jste placené Azure odběratele, můžete vytvořit novou žádost o podporu na portálu Azure. Vyberte **Nápověda a podpora**a potom vyberte **nová žádost o podporu**.

## <a name="currency-symbols-in-cloudyn-reports"></a>Symboly měny v sestavách Cloudyn

Můžete mít více účtů Azure používají různé měny. Náklady na sestavy v Cloudyn ale nezobrazovat více než jeden typ Měna na sestavu.

Pokud máte více předplatných, pomocí různých měn, nadřazená entita a jeho podřízené entity měny se zobrazují v USD **$**. Naše navrhované osvědčeným postupem je vyhýbat se používání různých měny ve stejné hierarchii entity. Jinými slovy Všechna předplatná uspořádány do strukturu entity měli používat současně.

Cloudyn automaticky zjišťuje vaše předplatné Měna smlouvy Enterprise a uvede správně v sestavách.  Však Cloudyn zobrazí pouze USD **$** pro zprostředkovatele kryptografických služeb a účtů Azure web přímo.

## <a name="what-are-cloudyn-data-refresh-timelines"></a>Co jsou Cloudyn data aktualizujte časové osy?

Cloudyn má následující aktualizace časových dat:

- **Počáteční**: Po nastavení, může trvat až 24 hodin k zobrazení dat náklady v Cloudyn. Také může trvat až 10 dnů pro Cloudyn ke shromažďování dostatek dat zobrazit doporučení pro změnu velikosti.
- **Denní**: desetinu dni na konci každého měsíce, Cloudyn by měla zobrazit vaše data aktuální z předchozí den následující po o UTC + 3 Další den.
- **Měsíční**: od prvního dne desetinu den každý měsíc, Cloudyn může zobrazit data jenom až do konce předchozího měsíce.

Cloudyn zpracovává data za předchozí den, kdy je k dispozici úplné data z předchozí den. Předchozí den dat je obvykle dostupná v Cloudyn podle o UTC + 3 každý den. Některá data, jako je například značky, může trvat dalších 24 hodin ke zpracování.

Data pro aktuální měsíc není k dispozici pro kolekci na začátku každého měsíce. Během období dokončit poskytovatelé služeb svoje fakturace předchozího měsíce. Předchozí měsíc data se zobrazí v Cloudyn 5 na 10 dnů po začátku každý měsíc. Během této doby může zobrazit pouze amortizovaný náklady z předchozího měsíce. Nemusíte to vidět denní data o fakturaci a využití. Jakmile data k dispozici, Cloudyn procesy zpětně. Po zpracování se zobrazí všechny měsíční data mezi pátý den a desetinu den každý měsíc.

Pokud dojde ke zpoždění odesílání dat z Azure do Cloudyn, data se stále zaznamená do Azure. Data se přenáší do Cloudyn obnovení připojení.

## <a name="how-can-a-direct-csp-configure-cloudyn-access-for-indirect-csp-customers-or-partners"></a>Jak můžete přímé CSP konfigurace Cloudyn přístupu pro nepřímou CSP zákazníky nebo partnery?

Pokyny najdete v tématu [nakonfigurujte nepřímý přístup CSP ve Cloudyn](quick-register-csp.md#configure-indirect-csp-access-in-cloudyn).

## <a name="what-causes-the-optimizer-menu-item-to-appear"></a>Co způsobí, že položky nabídky Optimalizátor zobrazí?

Po přidání přístupu Azure Resource Manager a data se shromažďují, měli byste vidět **Optimalizátor** možnost. Pokud chcete aktivovat přístup správce Azure Resource Manager, najdete v části [jak aktivovat neaktivovaných účtů s přihlašovacími údaji Azure?](#how-do-i-activate-unactivated-accounts-with-azure-credentials)

## <a name="is-cost-managementcloudyn-agent-based"></a>Je na základě náklady na správu nebo Cloudyn agenta?

Ne. Agenti nejsou používány. Shromáždění dat metriky virtuálního počítače Azure pro virtuální počítače z rozhraní API pro přehledy společnosti Microsoft. Pokud chcete shromažďovat metriky data z virtuálních počítačů Azure, potřebují mít povolené nastavení diagnostiky.

## <a name="do-cloudyn-reports-show-more-than-one-ad-tenant-per-report"></a>Cloudyn sestavy zobrazit více než jeden klient AD na sestavu?

Ano. Můžete [vytvořit odpovídající entita účet cloudu](tutorial-user-access.md#create-entities) pro každého klienta AD, které máte. Potom můžete zobrazit všechny vaše data klienta Azure AD a jiných poskytovatelů cloudu platformy, včetně Amazon Web Services a Google Cloud Platform.
