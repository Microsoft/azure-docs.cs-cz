---
title: Nejčastější dotazy ke službě Azure Cost Management | Dokumentace Microsoftu
description: Poskytuje odpovědi na některé běžné otázky o službě Azure Cost Management.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 07/20/2018
ms.topic: troubleshooting
ms.service: cost-management
manager: dougeby
ms.custom: ''
ms.openlocfilehash: 255056390cdbdbee49eba47f8168618929b386c8
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2018
ms.locfileid: "39187253"
---
# <a name="frequently-asked-questions-for-azure-cost-management"></a>Nejčastější dotazy ke službě Azure Cost Management

Tento článek se zabývá některými běžnými dotazy o službě Azure Cost Management (označované také jako Cloudyn). Pokud máte dotazy týkající se Cost Management, můžete požádat je na [nejčastější dotazy k Azure Cost Management](https://social.msdn.microsoft.com/Forums/en-US/231bf072-2c71-4121-8339-ac9d868137b9/faqs-for-azure-cost-management-by-cloudyn?forum=Cloudyn).

## <a name="how-can-i-resolve-common-indirect-enterprise-setup-problems"></a>Jak můžu řešit běžných problémů s instalací nepřímé enterprise?

Při prvním použití portálu Cloudyn, může zobrazit následující zprávy, pokud jste uživatelem smlouvu Enterprise nebo Cloud Solution Provider (CSP):

- "Zadaný klíč rozhraní API není klíč nejvyšší úrovně zápisu" zobrazí v **nastavte si Azure Cost Management** průvodce.
- "Přímá registrace – ne" zobrazí na portálu Enterprise Agreement.
- "Za posledních 30 dní se nenašla žádná data o využití. Obraťte se prosím na vaše distributora, abyste měli jistotu, že kód byl povolen pro váš účet Azure"zobrazí na portálu Cloudyn.

Z předchozích zpráv vyplývá, že jste si smlouvu Azure Enterprise zakoupili přes prodejce nebo poskytovatele CSP. Prodejce nebo poskytovatel CSP je potřeba povolit _značek_ pro účet Azure, mohli zobrazit vaše data ve službě Cloudyn.

Tady je postup řešení těchto potíží:

1. Váš prodejce musí pro váš účet povolit _revize_. Postup najdete v [pokynech k nepřímé registraci zákazníka](https://ea.azure.com/api/v3Help/v2IndirectCustomerOnboardingGuide).

2. Vygenerujete klíč smlouvy Azure Enterprise pro použití s Cloudyn. Pokyny najdete v tématu [přidání Azure EA](https://support.cloudyn.com/hc/en-us/articles/210429585-Adding-Your-AZURE-EA) nebo [jak najít svůj EA registrace ID a klíč rozhraní API](https://youtu.be/u_phLs_udig).

Službu Cost Management může povolit jenom správce služeb Azure. Oprávnění spolusprávce k tomu nestačí.

Než můžete vygenerovat klíč rozhraní API smlouvy Azure Enterprise k nastavení Cloudyn, musíte povolit API pro fakturaci Azure podle pokynů na adrese:

- [Přehled rozhraní API pro vytváření sestav pro podnikové zákazníky](../billing/billing-enterprise-api.md)
- [Rozhraní API pro vytváření sestav na podnikovém portálu Microsoft Azure](https://ea.azure.com/helpdocs/reportingAPI) v části o **povolení přístupu k datům pro rozhraní API**


Také může být potřeba, abyste udělili oprávnění _zobrazovat poplatky_ v rozhraní API pro fakturaci správcům oddělení, vlastníkům účtů a podnikovým správcům.

## <a name="why-dont-i-see-optimizer-recommendations"></a>Proč nevidím doporučení pro optimalizaci?

Informace o doporučení dostupná jenom pro účty, které jsou aktivované. Neuvidíte žádné doporučení informace v **optimalizace** sestavy kategorie pro účty, které jsou *neaktivovaných*, včetně:

- Optimalizace správce
- Optimalizace velikosti
- Nedostatečné efektivity

Pokud nelze zobrazit žádná data doporučení pro optimalizaci, pravděpodobně máte účty, které neaktivovaných. K aktivaci účtu, musíte ho zaregistrovat pomocí přihlašovacích údajů Azure.

Chcete-li aktivovat účet:

1.  Na portálu Cloudyn klikněte v pravém horním rohu na **Settings** (Nastavení) a vyberte **Cloud Accounts** (Účty v cloudu).
2.  Na kartě účty Microsoft Azure, vyhledejte účty, které mají **neaktivovaných** předplatného.
3.  Napravo od neaktivovaných účet, klikněte na tlačítko **upravit** symbol, který vypadá podobně jako tužka.
4.  Váš tenant ID a ID sazby je automaticky rozpoznán. Klikněte na **Další**.
5.  Budete přesměrováni na webu Azure portal. Přihlaste se k portálu a autorizaci Cloudyn kolektor přístup k datům v Azure.
6.  V dalším kroku budete přesměrováni na stránku Správa účty Cloudyn a vaše předplatné se aktualizuje s **aktivní** stav účtu. Zobrazuje zelená značka zaškrtnutí symbol.
7.  Pokud se nezobrazí zelená značka zaškrtnutí symbolů pro jeden nebo více předplatných, znamená to, že nemáte oprávnění k vytvoření čtečky aplikace (CloudynCollector) pro předplatné. Uživatel s vyšší oprávnění u předplatného je potřeba zopakujte kroky 3 a 4.  

Po dokončení předchozích kroků můžete zobrazit doporučení pro optimalizaci jedné až dvou dnů. Však může trvat až pět dní před úplná optimalizace data jsou k dispozici.


## <a name="how-do-i-enable-suspended-or-locked-out-users"></a>Jak povolím pozastavená nebo uzamčený uživatelů?

Nejprve Pojďme se podívat nanejvýš běžný scénář, který způsobí, že uživatelské účty, chcete-li získat *initiallySuspended*.

> Admin1 může být uživatel Microsoft Cloud Solution Provider a smlouvy Enterprise. Jeho organizace je začít využívat Cost Management.  Osoba zaregistruje na webu Azure portal a přihlásí na portálu Cloudyn. Jako osobě, která registruje službu Cost Management a přihlásí do portálu Cloudyn, se *primární správce*. Admin1 nevytvoří všechny uživatelské účty. Ale na portálu Cloudyn mohl vytvořit účty Azure a nastaví hierarchie entit. Admin1 informuje Admin2, správce klienta, který potřebuje k registraci ve službě Cost Management a přihlaste se k portálu Cloudyn.

> Admin2 zaregistruje na webu Azure portal. Ale při snaží se přihlaste k portálu Cloudyn, že získá chyba s oznámením o svém účtu je **pozastaveno**. Primární správce Admin1, je oznámení o pozastavení účtu. Admin1 musí aktivovat Admin2 na účet a udělte *přístup správce entity* pro příslušné entity a umožňuje přístup ke správě uživatelů a aktivní uživatelský účet.


Pokud se zobrazí výstraha se požadavek na povolit přístup pro uživatele, musíte aktivovat uživatelský účet.

Chcete-li aktivovat uživatelský účet:

1. Přihlaste se do Cloudyn pomocí účtu Azure administrativního uživatele, který jste použili k nastavení Cloudyn. Nebo, přihlaste se pomocí uživatelského účtu, který byl udělen přístup správce.
2. Vyberte symbol ozubeného kolečka v pravém horním rohu a vyberte **Správa uživatelů**.
3. Vyhledejte uživatele, vyberte symbol tužky a upravte uživatele.
4. V části **stav uživatele**, změňte stav z **pozastaveno** k **aktivní**.

Uživatelské účty Cloudyn připojit s použitím jednotné přihlašování z Azure. Pokud uživatel mistypes své heslo, se může nezůstanete zamknutí mimo Cloudyn, i když jsou i nadále přístup k Azure.

Pokud změníte e-mailovou adresu ve službě Cloudyn z výchozí adresy v Azure, můžete získat váš účet uzamčen. Může zobrazovat "stav initiallySuspended." Pokud váš uživatelský účet je uzamčen, obraťte se na správce alternativní obnovení vašeho účtu.

Doporučujeme vytvořit aspoň dva účty Cloudyn správce v případě, že jeden z účtů získá uzamčen.

Pokud nemůžete se přihlásit k portálu Cloudyn, ujistěte se, že používáte správnou adresu URL Azure Cost Management pro přihlášení do Cloudyn. Použití [ https://azure.cloudyn.com ](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/CloudynMainBlade).

Vyhněte se pomocí přímé adresy URL Cloudyn https://app.cloudyn.com.

## <a name="how-do-i-activate-unactivated-accounts-with-azure-credentials"></a>Jak si můžu aktivovat neaktivovaných účty pomocí přihlašovacích údajů Azure?

Poté, co zjistí účtů Azure Cloudyn, datům služby cost okamžitě součástí sestavy založené na náklady. Ale pro Cloudyn poskytují data o využití a výkonu, budete muset zaregistrovat přihlašovací údaje pro účty Azure. Pokyny najdete v tématu [přidat Azure Resource Manageru](https://support.cloudyn.com/hc/en-us/articles/212784085-Adding-Azure-Resource-Manager).

Přidat Azure přihlašovací údaje k účtu na portálu Cloudyn, vyberte symbol úpravy napravo od názvu účtu, ne předplatného.

Dokud přihlašovací údaje Azure jsou přidány do Cloudyn, účet se zobrazí jako _zrušení aktivované_.

## <a name="how-do-i-add-multiple-accounts-and-entities-to-an-existing-subscription"></a>Jak přidat více účtů a entity k existujícímu předplatnému?

Další entity se používají k přidání dalších smlouvy Enterprise s předplatným Cloudyn. Následující odkazy popisují, jak přidat další entity:

- [Přidání Entity](https://support.cloudyn.com/hc/en-us/articles/212016145-Adding-an-Entity) článku
- [Definování hierarchii s Cost Entities](https://support.cloudyn.com/hc/en-us/articles/115005142529-Video-Defining-your-hierarchy-with-Cost-Entities) videa

Pro CSP:

Chcete-li přidat další účty poskytovatele CSP s entitou, **přístup účastník programu MSP** místo **Enterprise** při vytváření nové entity. Pokud váš účet je zaregistrovaný jako uzavřenou smlouvu Enterprise Agreement a chcete přidat přihlašovací údaje CSP, pracovníci podpory Cloudyn možná muset upravit nastavení svého účtu. Pokud jste placené předplatitelem Azure, můžete vytvořit novou žádost o podporu na webu Azure Portal. Vyberte **Nápověda a podpora**a pak vyberte **nová žádost o podporu**.

## <a name="currency-symbols-in-cloudyn-reports"></a>Symboly měny v sestavách Cloudyn

Můžete mít několika účtům Azure používají různé měny. Sestavy nákladů ve službě Cloudyn však není uveden více než jeden typ currency na sestavu.

Pokud máte více předplatných, které používají různé měny, nadřazená entita a jeho podřízené entity měny se zobrazují v USD **$**. Naše navrhované osvědčeným postupem je vyhýbat se použití různých měnách ve stejné hierarchii entity. Jinými slovy všechna vaše předplatná uspořádány do struktury entity používali stejné měně.

Cloudyn automaticky detekuje vaše předplatné měny smlouvy Enterprise a zobrazí správně v sestavách.  Zobrazí se však pouze Cloudyn USD **$** pro CSP a účty Azure přímo na webu.

## <a name="what-are-cloudyn-data-refresh-timelines"></a>Co jsou Cloudyn data aktualizujte časové osy?

Cloudyn zahrnuje následující aktualizace časových dat:

- **Počáteční**: Po nastavení, může trvat až 24 hodin, chcete-li zobrazit náklady na data ve službě Cloudyn. Můžete taky využít až 10 dní pro Cloudyn shromažďovat dostatek dat k zobrazení doporučení velikosti.
- **Denní**: po desátém dni na konci každého měsíce Cloudyn by měla zobrazit vaše data aktuální z předchozího dne po o UTC + 3 dalšího dne.
- **Měsíční**: od prvního dne desátý den v měsíci, může být Cloudyn zobrazit data jenom do konce předchozího měsíce.

Cloudyn zpracovává data pro předchozího dne, když jsou k dispozici úplná data z předchozího dne. Data z předchozího dne je obvykle k dispozici ve službě Cloudyn podle o UTC + 3 každý den. Některá data, jako jsou například značky, může trvat dalších 24 hodin ke zpracování.

Data pro aktuální měsíc nejsou k dispozici pro kolekci na začátku v každém měsíci. Během doby dokončení poskytovatelé služeb své fakturace za předchozí měsíc. Předchozí měsíc data se zobrazí v Cloudyn 5 do 10 dnů po zahájení každý měsíc. Během této doby se může zobrazit jenom amortizované náklady na z předchozího měsíce. Nemusíte to vidět dat o denním využití nebo fakturace. Až bude dostupná data, Cloudyn ji zpracuje zpětně. Po zpracování, zobrazí se všechny měsíční data mezi páté den a desátý den každého měsíce.

Pokud dochází ke zpoždění odesílání dat z Azure do Cloudyn, data se stále zaznamená do Azure. Data se přenesou do Cloudyn, když se obnoví připojení.

## <a name="cost-fluctuations-in-cloudyn-cost-reports"></a>Cenově kolísání v sestavách nákladů Cloudyn

Sestavy nákladů můžete zobrazit náklady kolísání pokaždé, když se aktualizované soubory fakturační odeslat poskytovatelů cloudových služeb. Kolísá dojít, když nové soubory, které byly přijaty z poskytovatele cloudových služeb mimo obvyklého denní nebo měsíční plán vytváření sestav. Cenově změny není výsledkem přepočet Cloudyn. 

V průběhu měsíce jsou všechny fakturační soubory odeslané poskytovatele cloudových služeb odhad denních nákladů. Někdy se často aktualizuje data – čas od času víc než jednou za den. Aktualizace jsou častější od AWS do Azure. Nákladů celkový počet položek by měla zůstat po dokončení výpočet fakturace za předchozí měsíc a po přijetí poslední fakturace souboru. Obvykle pomocí 10 měsíce.

Když dostanete úpravy náklady od poskytovatele cloudových služeb dojde ke změnám. Příjem kredity je jedním z příkladů. Změny můžou nastat měsíců po byla uzavřena v příslušném měsíci. Změny jsou zobrazeny pokaždé, když je přepočet provedené poskytovatele cloudových služeb. Cloudyn aktualizuje její historická data, abyste měli jistotu, že všechny úpravy přepočítána. Také ověří, že přesně zobrazených náklady v sestavy.

## <a name="how-can-a-direct-csp-configure-cloudyn-access-for-indirect-csp-customers-or-partners"></a>Jak můžete s přímým přístupem CSP konfigurovat Cloudyn přístup pro nepřímé zákazníky nebo partnery CSP?

Pokyny najdete v tématu [konfigurace přístupu nepřímých poskytovatelů CSP v Cloudyn](quick-register-csp.md#configure-indirect-csp-access-in-cloudyn).

## <a name="what-causes-the-optimizer-menu-item-to-appear"></a>Co způsobí, že Optimalizátor položka nabídky Zobrazit?

Po přidání přístupu k Azure Resource Manageru a data se shromažďují, měli byste vidět **optimalizace** možnost. Chcete-li aktivovat přístup do Azure Resource Manageru, přečtěte si téma [jak si můžu aktivovat neaktivovaných účty pomocí přihlašovacích údajů Azure?](#how-do-i-activate-unactivated-accounts-with-azure-credentials)

## <a name="is-cost-managementcloudyn-agent-based"></a>Je na základě Cost Management/Cloudyn agenta?

Ne. Agenti nejsou používány. Shromáždění dat metriky virtuálního počítače Azure pro virtuální počítače z rozhraní API Microsoft Insights. Pokud chcete shromažďovat data metrik z virtuálních počítačů Azure, musí mít povolené nastavení diagnostiky.

## <a name="do-cloudyn-reports-show-more-than-one-ad-tenant-per-report"></a>V sestavách Cloudyn zobrazit více než jednoho tenanta AD na sestavu?

Ano. Je možné [vytvořit odpovídající entity účtu cloudu](tutorial-user-access.md#create-and-manage-entities) pro každého tenanta AD, který máte. Pak můžete zobrazit všechny data tenanta Azure AD a dalších poskytovatelů cloudu platformy, včetně Amazon Web Services a Google Cloud Platform.
