---
title: Nejčastější dotazy ke službě Cloudyn v Azure
description: Zjistěte, jak s využitím portálu Cloudyn řešit běžné nepřímé potíže s podnikovým nastavením a získat odpovědi na další nejčastější dotazy.
author: bandersmsft
ms.author: banders
ms.date: 10/23/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cloudyn
ms.reviewer: benshy
ms.custom: seodec18
ROBOTS: NOINDEX
ms.openlocfilehash: 92a62de4223d2ec6d0441840b90fdce20c656d02
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2020
ms.locfileid: "92546224"
---
# <a name="frequently-asked-questions-for-cloudyn"></a>Nejčastější dotazy ke službě Cloudyn

Tento článek popisuje některé běžné otázky týkající se služby Cloudyn. Pokud máte dotazy týkající se služby Cloudyn, můžete využít [Nejčastější dotazy ke službě Cloudyn](https://social.msdn.microsoft.com/Forums/en-US/231bf072-2c71-4121-8339-ac9d868137b9/faqs-for-cloudyn-cost-management?forum=Cloudyn).

[!INCLUDE [cloudyn-note](../../../includes/cloudyn-note.md)]

## <a name="how-can-i-resolve-common-indirect-enterprise-setup-problems"></a>Jak vyřešit běžné nepřímé potíže s podnikovým nastavením?

Pokud jste uživatel se smlouvou Enterprise nebo Cloud Solution Provider (CSP), můžou se vám při prvním použití portálu Cloudyn zobrazit následující zprávy:

- The specified API key is not a top level enrollment key (Zadaný klíč rozhraní API není klíč registru nejvyšší úrovně) – zobrazí se v průvodci **nastavením služby Cloudyn** .
- Direct Enrollment – No (Přímá registrace – ne) – zobrazí se na portálu se smlouvou Enterprise.
- No usage data was found for the last 30 days. Please contact your distributor to make sure markup was enabled for your Azure account (Za posledních 30 dní nebyla nalezena žádná data o využití. Ověřte prosím u svého distributora, jestli byly pro váš účet Azure povoleny revize) – zobrazí se na portálu Cloudyn.

Z předchozích zpráv vyplývá, že jste si smlouvu Azure Enterprise zakoupili přes prodejce nebo poskytovatele CSP. Abyste mohli zobrazovat data ve službě Cloudyn, musí váš prodejce nebo poskytovatel CSP pro váš účet Azure povolit _revize_ .

Tady je postup řešení těchto potíží:

1. Váš prodejce musí pro váš účet povolit _revize_ . Postup najdete v [pokynech k nepřímé registraci zákazníka](https://ea.azure.com/api/v3Help/v2IndirectCustomerOnboardingGuide).

2. Vygenerujte klíč smlouvy Azure Enterprise pro použití se službou Cloudyn.

Službu Cloudyn může povolit jenom správce služeb Azure. Oprávnění spolusprávce k tomu nestačí.

Než budete moct vygenerovat klíč rozhraní API smlouvy Azure Enterprise k nastavení služby Cloudyn, musíte podle následujících pokynů povolit rozhraní API pro fakturaci Azure:

- [Přehled rozhraní API pro vytváření sestav pro podnikové zákazníky](../manage/enterprise-api.md)
- [Rozhraní API pro vytváření sestav na podnikovém portálu Microsoft Azure](https://ea.azure.com/helpdocs/reportingAPI) v části o **povolení přístupu k datům pro rozhraní API**


Také může být potřeba, abyste udělili oprávnění _zobrazovat poplatky_ v rozhraní API pro fakturaci správcům oddělení, vlastníkům účtů a podnikovým správcům.

## <a name="why-dont-i-see-optimizer-recommendations"></a>Proč se mi nezobrazují doporučení služby Optimizer?

Doporučující informace jsou k dispozici pouze pro účty, které jsou aktivovány. V kategoriích sestav služby **Optimizer** nenajdete žádné doporučující informace pro účty, které jsou *unactivated* (neaktivované). Patří sem kategorie:

- Optimization Manager (Správce optimalizace)
- Sizing Optimization (Optimalizace velikosti)
- Inefficiencies (Nedostatečná efektivita)

Pokud nemůžete zobrazit žádná data služby Optimizer s doporučeními, pravděpodobně máte neaktivované účty. Chcete-li aktivovat účet, musíte ho zaregistrovat pomocí přihlašovacích údajů Azure.

Aktivace účtu:

1.    Na portálu Cloudyn klikněte v pravém horním rohu na **Settings** (Nastavení) a vyberte **Cloud Accounts** (Účty v cloudu).
2.    Na kartě Microsoft Azure Accounts (Účty Microsoft Azure) vyhledejte účty, které mají **neaktivované** předplatné.
3.    Napravo od neaktivovaného účtu klikněte na symbol pro **úpravy** , který se podobá tužce.
4.    ID vašeho tenanta a ID sazby se zjistí automaticky. Klikněte na **Další** .
5.    Budete přesměrováni na Azure Portal. Přihlaste se k portálu a autorizujte kolektor Cloudyn pro přístup k datům Azure.
6.    Následně budete přesměrováni na stránku pro správu účtů služby Cloudyn a vaše předplatné se aktualizuje se stavem účtu **active** (aktivní). Zobrazí symbol zelené značky zaškrtnutí.
7.    Pokud u některých předplatných zelenou značku zaškrtnutí nevidíte, znamená to, že nemáte oprávnění vytvářet pro ně aplikaci pro čtení (CloudynCollector). Je potřeba, aby krok 3 a 4 zopakoval uživatel, který má k těmto předplatným vyšší oprávnění.  

Po dokončení předchozích kroků uvidíte doporučení služby Optimizer do jednoho až dvou dnů. Může však trvat až pět dní, než budou k dispozici úplná data optimalizace.


## <a name="how-do-i-enable-suspended-or-locked-out-users"></a>Jak povolím pozastavené nebo uzamčené uživatele?

Nejprve se podíváme na nejběžnější scénář, který způsobuje, že uživatelské účty přejdou do stavu *initiallySuspended* .

> Admin1 může být poskytovatel řešení Microsoft Cloud nebo uživatel se smlouvou Enterprise. Jeho organizace je připravená začít používat Cloudyn.  Zaregistruje se přes Azure Portal a přihlásí se k portálu Cloudyn. Uživatel, který si zaregistruje službu Cloudyn a přihlásí se k portálu Cloudyn, se stává *primárním správcem* Admin1. Admin1 nevytváří žádné uživatelské účty. Pomocí portálu Cloudyn však vytvoří účty Azure a nastaví hierarchii entit. Admin1 informuje správce tenanta Admin2, že se musí zaregistrovat do služby Cloudyn a přihlásit se k portálu Cloudyn.
>
> Admin2 se zaregistruje prostřednictvím webu Azure Portal. Když se ale pokusí přihlásit k portálu Cloudyn, zobrazí se mu chyba s oznámením, že účet je **pozastaven** . Primární správce, Admin1, je upozorněn na pozastavení účtu. Admin1 musí aktivovat účet uživatele Admin2 a udělit *přístup k entitám správce* pro příslušné entity a umožnit přístup ke správě uživatelů a aktivaci uživatelských účtů.


Pokud obdržíte upozornění s žádostí o povolení přístupu pro uživatele, musíte aktivovat uživatelský účet.

Postup aktivace uživatelského účtu:

1. Přihlaste se do služby Cloudyn pomocí účtu uživatele pro správu Azure, který jste použili k nastavení služby Cloudyn. Nebo se přihlaste pomocí uživatelského účtu, kterému byl udělen přístup správce.
2. Vyberte symbol ozubeného kolečka v pravém horním rohu a vyberte možnost **User Management** (Správa uživatelů).
3. Vyhledejte uživatele, vyberte symbol tužky a pak uživatele upravte.
4. V části **User status** (Stav uživatele) změňte stav z **Suspended** (Pozastaveno) na **Active** (Aktivní).

Uživatelské účty Cloudyn se připojují pomocí jednotného přihlašování z Azure. Pokud uživatel chybně zadá heslo, může se stát, že se Cloudyn uzamkne, i když bude mít uživatel přístup k Azure.

Pokud změníte svou e-mailovou adresu v Cloudyn na jinou, než je výchozí adresa v Azure, je možné, že se váš účet uzamkne. Může se zobrazit stav initiallySuspended. Pokud je váš uživatelský účet uzamčený, obraťte se na alternativního správce a resetujte svůj účet.

Doporučujeme vytvořit alespoň dva účty správců Cloudyn pro případ, že se jeden z účtů uzamkne.

Pokud se nemůžete přihlásit k portálu Cloudyn, ujistěte se, že používáte správnou adresu URL pro přihlášení ke službě Cloudyn. Použijte [https://azure.cloudyn.com](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/CloudynMainBlade).

Vyhněte se použití přímé adresy URL Cloudyn `https://app.cloudyn.com`.

## <a name="how-do-i-activate-unactivated-accounts-with-azure-credentials"></a>Jak aktivovat neaktivované účty pomocí přihlašovacích údajů Azure?

Jakmile služba Cloudyn zjistí vaše účty Azure, data nákladů se ihned zobrazí v sestavách založených na nákladech. Aby však služba Cloudyn poskytovala data o využití a výkonu, je třeba pro příslušné účty zaregistrovat přihlašovací údaje Azure. Pokyny najdete v tématu [Přidání účtu nebo aktualizace předplatného](activate-subs-accounts.md#add-an-account-or-update-a-subscription).

Pokud chcete přidat přihlašovací údaje Azure pro účet, na portálu Cloudyn vyberte symbol úprav napravo od názvu účtu, nikoli předplatné.

Až se přihlašovací údaje Azure přidají do služby Cloudyn, zobrazí se tento účet jako _unactivated_ (neaktivovaný).

## <a name="how-do-i-add-multiple-accounts-and-entities-to-an-existing-subscription"></a>Jak přidat několik účtů a entit do stávajícího předplatného?

K přidání dalších smluv Enterprise do předplatného Cloudyn se používají další entity. Další informace najdete v tématu [Vytváření a správa entit](tutorial-user-access.md#create-and-manage-entities).

Pro poskytovatele CSP:

Pokud chcete přidat další účty CSP k entitě, vyberte při vytváření nové entity **MSP Access** (Přístup MSP) místo **Enterprise** . Pokud je váš účet zaregistrován jako smlouva Enterprise a chcete přidat přihlašovací údaje CSP, budou pracovníci podpory Cloudyn pravděpodobně potřebovat upravit nastavení vašeho účtu. Pokud jste předplatitelem Azure, můžete vytvořit novou žádost o podporu na webu Azure Portal. Vyberte **Nápověda a podpora** a potom **Nová žádost o podporu** .

## <a name="currency-symbols-in-cloudyn-reports"></a>Symboly měny v sestavách Cloudyn

Můžete mít několik účtů Azure a používat různé měny. V sestavách nákladů ve službě Cloudyn se ale nezobrazuje více než jeden typ měny na sestavu.

Pokud máte více předplatných s použitím různých měn, zobrazí se měna nadřazené entity a měny jejích podřízených entit v dolarech **$** . Náš doporučený postup je zabránit používání různých měn ve stejné hierarchii entit. To znamená, že by vaše předplatná uspořádaná ve struktuře entit měla používat stejnou měnu.

Cloudyn automaticky detekuje vaši měnu předplatného ve smlouvě Enterprise a správně ji uvede v sestavách.  Pro účty CSP a Azure s přímým přístupem na webu však zobrazuje Cloudyn jenom dolary **$** .

## <a name="what-are-cloudyn-data-refresh-timelines"></a>Co jsou časové osy aktualizace dat Cloudyn?

Cloudyn používá následující časové osy aktualizace dat:

- **Initial** (Počáteční): Po nastavení může trvat až 24 hodin, než se data nákladů zobrazí ve službě Cloudyn. Může také trvat až 10 dní, než Cloudyn shromáždí dostatek dat pro zobrazení doporučení velikosti.
- **Daily** (Denní): Od desátého dne po konec každého měsíce by ve službě Cloudyn měla být data v aktuálním stavu od předchozího dne přibližně v čase UTC + 3 do následujícího dne.
- **Monthly** (Měsíční): V době od prvního do desátého dne v každém měsíci může Cloudyn data zobrazit pouze z konce předchozího měsíce.

Cloudyn zpracovává data za předchozí den, pokud jsou k dispozici úplná data. Data z předchozího dne jsou většinou ve službě Cloudyn k dispozici denně v čase UTC + 3. Je možné, že zpracování některých dat, jako jsou značky, může trvat dalších 24 hodin.

Data za aktuální měsíc nelze shromáždit na začátku příslušného měsíce. V té době dokončují poskytovatelé služeb fakturaci za předchozí měsíc. Data za předchozí měsíc se zobrazí ve službě Cloudyn 5 až 10 dní po začátku každého měsíce. Do té doby se vám z předchozího měsíce můžou zobrazit jenom náklady spojené s amortizací. Je možné, že neuvidíte data denní fakturace ani využití. Jakmile jsou data k dispozici, Cloudyn je zpracuje zpětně. Po zpracování se všechna měsíční data zobrazí mezi pátým a desátým dnem v měsíci.

Pokud nastane zpoždění s odesláním dat z Azure do služby Cloudyn, budou data stále zaznamenána v Azure. Data se přenesou do služby Cloudyn po obnovení připojení.

## <a name="cost-fluctuations-in-cloudyn-cost-reports"></a>Kolísání nákladů v sestavách nákladů Cloudyn

V sestavách nákladů se může objevit kolísání nákladů, když poskytovatelé cloudových služeb odešlou aktualizované fakturační soubory. K výkyvům nákladů dochází při přijímání nových souborů od poskytovatele cloudových služeb mimo obvyklý denní nebo měsíční plán generování sestav. Změny nákladů nejsou výsledkem přepočtů ve službě Cloudyn.

V průběhu měsíce představují všechny fakturační soubory odeslané vaším poskytovatelem cloudových služeb odhad denních nákladů. Někdy se data často aktualizují – občas i několikrát denně. Aktualizace jsou častější v AWS než v Azure. Celkové náklady by měly zůstat stabilní, pokud je výpočet fakturace za předchozí měsíc dokončený a konečný fakturační soubor je přijatý. Obvykle je to do desátého dne v měsíci.

Změny se projeví, když od poskytovatele cloudové služby obdržíte vyrovnání nákladů. Jedním z příkladů je příjem kreditů. Změny mohou nastat i za několik měsíců po uzavření příslušného měsíce. Změny se zobrazí pokaždé, když poskytovatel cloudových služeb provede přepočet. Cloudyn aktualizuje své historické údaje, aby se zajistilo, že se přepočítají všechny úpravy. Ověřuje také, že se náklady zobrazují správně v sestavách.

## <a name="how-can-a-direct-csp-configure-cloudyn-access-for-indirect-csp-customers-or-partners"></a>Jak může přímý poskytovatel cloudových služeb nakonfigurovat přístup ke službě Cloudyn pro nepřímé zákazníky nebo partnery CSP?

Pokyny najdete v tématu [Konfigurace přístupu nepřímých poskytovatelů CSP v Cloudyn](quick-register-csp.md#configure-indirect-csp-access-in-cloudyn).

## <a name="what-causes-the-optimizer-menu-item-to-appear"></a>Co způsobí zobrazení položky nabídky Optimizer?

Po přidání přístupu Azure Resource Manager a shromáždění dat by se měla zobrazit možnost **Optimizer** . Pokud chcete aktivovat přístup Azure Resource Manager, přečtěte si téma [Jak aktivovat neaktivované účty pomocí přihlašovacích údajů Azure?](#how-do-i-activate-unactivated-accounts-with-azure-credentials)

## <a name="is-cloudyn-agent-based"></a>Je služba Cloudyn založená na agentech?

Ne. Agenti se nepoužívají. Data metrik virtuálních počítačů Azure se shromažďují z rozhraní API Microsoft Insights. Pokud chcete shromáždit data metrik z virtuálních počítačů Azure, musí mít virtuální počítače povolené nastavení diagnostiky.

## <a name="do-cloudyn-reports-show-more-than-one-ad-tenant-per-report"></a>Zobrazuje se v sestavách Cloudyn více tenantů služby AD na jednu sestavu?

Ano. Můžete [vytvořit odpovídající entitu cloudového účtu](tutorial-user-access.md#create-and-manage-entities) pro každého tenanta služby Active Directory. Pak můžete zobrazit data všech vašich tenantů Azure AD a dalších poskytovatelů cloudových platforem, včetně Amazon Web Services a Google Cloud Platform.
