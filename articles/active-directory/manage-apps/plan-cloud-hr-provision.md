---
title: Plánování aplikace cloudového HR pro Azure Active Directory zřizování uživatelů
description: Tento článek popisuje proces nasazení integrující systémy cloudového HR, jako je například Workday a Sucessfactors, s Azure Active Directory. Integrace služby Azure AD s vaším systémem cloudového HR má za následek kompletní systém pro správu životního cyklu identit.
services: active-directory
author: martincoetzer
manager: CelesteDG
tags: azuread
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 11/22/2019
ms.author: martinco
ms.reviewer: arvindha
ms.openlocfilehash: 6f72371077aab813cc22c9bbbe755fdfaa9ac00a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75433823"
---
# <a name="plan-cloud-hr-application-to-azure-active-directory-user-provisioning"></a>Plánování aplikace cloudového HR pro Azure Active Directory zřizování uživatelů

Historicky se zaměstnanci IT spoléhali na ruční metody vytváření, aktualizace a odstraňování zaměstnanců, jako je například nahrávání souborů CSV nebo vlastní skripty pro synchronizaci dat zaměstnanců. Tyto zřizovací procesy jsou náchylné k chybám, nezabezpečené a nenáročné na správu.

Aby bylo možné bezproblémově spravovat kompletní životní cyklus identity zaměstnanců, dodavatelů nebo podmíněných pracovníků, [Služba zřizování uživatelů Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) nabízí integraci s cloudovou aplikacemi lidských zdrojů (HR), jako je Workday nebo SuccessFactors.

Azure AD pomocí této integrace povolí následující pracovní postupy aplikace cloudového HR:

- **Zřizování uživatelů pro AD** – vybrané sady uživatelů z aplikace cloudového HR do jedné nebo více domén služby Active Directory (AD).
- **Zřizování pouze cloudových uživatelů pro Azure AD** – ve scénářích, kdy se služba AD nepoužívá, zřídí uživatele přímo z aplikace cloudového HR do Azure AD.
- **Zapište zpátky do aplikace cloudového HR.** – Zapište e-mailové adresy a atributy uživatelského jména z Azure AD zpátky do aplikace Cloude HR.

> [!NOTE]
> Tento plán nasazení vás provede nasazením pracovních postupů vaší aplikace cloudového HR pomocí zřizování uživatelů Azure AD. Informace o nasazení automatického zřizování uživatelů do aplikací SaaS najdete v tématu [plánování automatického zřizování uživatelů](https://aka.ms/deploymentplans/provisioning).

## <a name="enabled-hr-scenarios"></a>Povolené scénáře pro personální oddělení

Služba zřizování uživatelů Azure AD umožňuje automatizaci následujících scénářů správy životního cyklu identity na základě lidských zdrojů:

- Připravují se **noví zaměstnanci** – když se do aplikace cloudového HR přidá nový zaměstnanec, automaticky se vytvoří uživatelský účet ve službě AD a Azure AD s možností napsat zpátky e-mailovou adresu a atributy uživatelského jména do aplikace cloudového hr.
- **Aktualizace atributů a profilů zaměstnanců** – když se v aplikaci cloudového HR aktualizuje záznam zaměstnance (například jeho jméno, název nebo manažer), automaticky se aktualizuje jeho uživatelský účet ve službě AD a Azure AD.
- **Ukončení zaměstnanců** – když se zaměstnanec v aplikaci cloudového HR ukončí, jejich uživatelský účet se automaticky zakáže ve službě AD a v Azure AD.
- **Pracovní zařazení zaměstnanců** – když se zaměstnanec znovu přiřadí do aplikace cloudového HR, jejich starý účet se dá automaticky znovu aktivovat nebo znovu zřídit ve službě AD a Azure AD.

## <a name="who-is-this-integration-best-suited-for"></a>Pro kterou tuto integraci nejlépe vyhovuje?

Integrace aplikace pro cloudovou HR se zřizováním uživatelů Azure AD je ideální pro organizace, které:

- přání předem připravené cloudové řešení pro zřizování uživatelů v cloudu
- vyžadovat přímé zřizování uživatelů z aplikace cloudového HR do AD nebo Azure AD
- vyžadovat, aby se uživatelé zřídili pomocí dat získaných z aplikace cloudového HR
- vyžadovat připojení, přesun a ukončení synchronizace uživatelů s jednou nebo více doménovými strukturami služby AD, doménami a organizačními jednotkami, které jsou založeny pouze na informacích o změně zjištěných v aplikaci cloudového HR
- použít Office 365 k e-mailu

## <a name="learn"></a>Získejte informace

Zřizování uživatelů vytvoří základ pro průběžné řízení identit a vylepšuje kvalitu obchodních procesů, které spoléhají na data autoritativní identity.

### <a name="terms"></a>Výrazy

Tento článek používá následující výrazy:

- **Zdrojový systém** – úložiště uživatelů, které Azure AD zřídí (například aplikace CLOUDového HR, například Workday a SuccessFactors)
- **Cílový systém** – úložiště uživatelů, na které Azure AD zřídí (například AD, Azure AD, Office 365 nebo jiné aplikace SaaS)
- **Připojení – stěhovací společnosti – Leavers proces** – termín používaný pro nová přijetí, převody a ukončení pomocí aplikace cloudového HR jako systému záznamů. Proces se dokončí, když služba úspěšně zřídí nezbytné atributy pro cílový systém.

### <a name="key-benefits"></a>Klíčové výhody

Tato funkce zřizování IT na základě lidských zdrojů nabízí významné obchodní výhody, jak je uvedeno níže:

- **Zvyšte produktivitu** – nyní můžete automatizovat přiřazování uživatelských účtů, licencí Office 365 a poskytovat přístup ke skupinám klíčů. Automatizace přiřazení poskytuje novým lidem okamžitý přístup ke svým nástrojům úloh a zvyšuje produktivitu.
- **Řízení rizik** – zabezpečení můžete zvýšit automatizací změn na základě stavu zaměstnanců nebo členství ve skupinách pomocí toku dat z aplikace cloudového hr. Automatizace změn zajišťuje, že identity uživatelů a přístup k klíčovým aplikacím se automaticky aktualizují, když uživatelé přecházejí nebo odejdou z organizace.
- **Dodržování předpisů a zásad správného řízení** – Azure AD podporuje nativní protokoly auditu pro požadavky uživatelů na zřizování, které provádí aplikace ze zdrojového i cílového systému. Auditování umožňuje sledovat, kdo má přístup k aplikacím z jedné obrazovky.
- **Správa nákladů** – Automatické zřizování snižuje náklady tím, že se vyhne neefektivitám a lidským chybám přidruženým k ručnímu zřizování. Omezuje nutnost vlastních vyvinutých řešení pro zřizování uživatelů pomocí starších a zastaralých platforem.

### <a name="licensing"></a>Správa licencí

Pokud chcete nakonfigurovat aplikaci cloudového HR na integraci zřizování uživatelů Azure AD, budete potřebovat platnou [licenci Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) a licenci pro aplikaci cloudového HR, jako je například Workday nebo SuccessFactors.

Pro každého uživatele, který se bude nacházet z aplikace cloudového HR a je zajištěná v rámci služby AD nebo Azure AD, potřebujete také platnou licenci na předplatného Azure AD Premium P1 nebo vyšší. Jakýkoli nesprávný počet licencí vlastněných v aplikaci cloudového HR může vést k chybám při zřizování uživatelů.

### <a name="prerequisites"></a>Požadavky

- Přístup globálního správce služby Azure AD ke konfiguraci Azure AD Connectho agenta zřizování.
- Instance testu a výroby aplikace cloudového HR.
- Oprávnění správce v aplikaci cloudového HR k vytvoření uživatele Integration System a provedení změn v datech testování zaměstnanců pro účely testování.
- V případě zřizování uživatelů pro službu AD se pro hostování [agenta Azure AD Connectho zřizování](https://go.microsoft.com/fwlink/?linkid=847801)vyžaduje server se systémem Windows Server 2012 nebo vyšší s modulem runtime .NET 4.7.1 +.
- [Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-azure-ad-connect) pro synchronizaci uživatelů mezi službami AD a Azure AD.

### <a name="training-resources"></a>Školicí materiály

| **Materiály** | **Odkaz a popis** |
|:-|:-|
| Videa | [Co je zřizování uživatelů v aktivním adresáři Azure?](https://youtu.be/_ZjARPpI6NI) |
| | [Jak nasadit zřizování uživatelů v aktivním adresáři Azure?](https://youtu.be/pKzyts6kfrw) |
| Kurzy | Podívejte se na [Seznam kurzů, jak integrovat aplikace SaaS s Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) . |
| | [Kurz: Konfigurace pracovního dne pro Automatické zřizování uživatelů](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#frequently-asked-questions-faq) |
| Časté otázky | [Automatizované zřizování uživatelů](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning#what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning) |
| | [Zřizování z Workday do Azure AD](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#frequently-asked-questions-faq) |

### <a name="solution-architecture"></a>Architektura řešení

Následující příklad popisuje architekturu řešení zřizování koncových uživatelů pro běžná hybridní prostředí a zahrnuje:

- **Směrodatný tok dat o lidských přenosech – od aplikace cloudového HR až po AD** – v tomto toku se v tenantovi aplikace CLOUDového HR SPUSTÍ událost HR (spojovací procesy – stěhovací společnosti-Leavers). Služba zřizování služby Azure AD a Agent pro zřizování Azure AD Connect zřídí data uživatelů z tenanta aplikace cloudového HR do AD. V závislosti na události to může vést k vytváření, aktualizaci, povolování a zakazování operací ve službě AD.
- **Synchronizace s Azure AD a e-mailem s zpětným zápisem a uživatelským jménem z místní reklamy do cloudové aplikace pro personální** prostředí – Jakmile se účty aktualizují ve službě AD, synchronizují se s Azure AD prostřednictvím Azure AD Connect a e-mailové adresy a atributy uživatelského jména se dají zapsat zpátky do tenanta aplikace cloudového hr.

![Diagram pracovního postupu](./media/plan-cloudhr-provisioning/plan-cloudhr-provisioning-img1.png)

#### <a name="description-of-workflow"></a>Popis pracovního postupu

Hlavní kroky uvedené v diagramu jsou:  

1. **Tým HR** provádí transakce v tenantovi cloudové aplikace.
2. **Služba zřizování Azure AD** spouští naplánované cykly z tenanta aplikace cloudového HR a identifikuje změny, které je potřeba zpracovat pro synchronizaci se službou AD.
3. **Služba zřizování Azure AD** vyvolá agenta Azure AD Connect zřizování s datovou částí požadavku obsahující účet služby AD, který obsahuje operace vytvořit/aktualizovat/povolit/zakázat.
4. **Agent zřizování Azure AD Connect** pro správu dat účtu AD používá účet služby.
5. **Azure AD Connect** spustí rozdílovou [synchronizaci](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis) s AKTUALIZACEMI Pull ve službě AD.
6. Aktualizace **AD** se synchronizují se službou Azure AD.
7. Buněk e-mailový atribut **služby Azure AD zřizování** a uživatelské jméno z Azure AD do tenanta aplikace cloudového hr.

## <a name="plan-the-deployment-project"></a>Plánování projektu nasazení

Při určování strategie pro toto nasazení v prostředí zvažte potřeby vaší organizace.

### <a name="engage-the-right-stakeholders"></a>Zapojení správných zúčastněných stran

Když projekty technologie selžou, obvykle to vznikne z důvodu neshodných očekávání na dopad, výsledky a zodpovědnosti. Pokud se chcete těmto nástrah vyhnout, ujistěte se, že jste si [jisti, že jste připravují správné zúčastněné strany](https://aka.ms/deploymentplans) a že role účastníků v projektu jsou dobře srozumitelné při dokumentaci zúčastněných stran a jejich vstupu a accountabilities projektu.

Musíte zahrnout zástupce z organizace pro personální oddělení, který může poskytovat vstupy na stávající obchodní procesy a nároky pracovního procesu a požadavky na zpracování dat úloh.

### <a name="plan-communications"></a>Plán komunikace

Komunikace je zásadní pro úspěch jakékoli nové služby. Měli byste aktivně komunikovat s vašimi uživateli, jak se změní, když se změní, a jak získat podporu v případě, že dojde k problémům.

### <a name="plan-a-pilot"></a>Plánování pilotního projektu

Integrace obchodních procesů a pracovních postupů identity z aplikace cloudového HR do cílových systémů vyžaduje značné množství ověření dat, transformaci dat, čištění dat a kompletní testování před nasazením řešení do produkčního prostředí.

Doporučujeme, aby se počáteční konfigurace spustila v [pilotním prostředí](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-plans#best-practices-for-a-pilot) před tím, než se bude škálovat na všechny uživatele v produkčním prostředí.

## <a name="select-cloud-hr-provisioning-connector-apps"></a>Výběr aplikací konektoru pro cloudové personální zajištění

Aby se usnadnily pracovní postupy zřizování Azure AD mezi aplikací cloudového HR a AD, je k dispozici několik aplikací konektoru pro zřizování, které můžete přidat z Galerie aplikací Azure AD:

- **Aplikace cloudového HR do zřizování uživatelů služby AD** – Tato aplikace konektoru pro zřizování usnadňuje zřizování uživatelských účtů z aplikace cloudového HR do jedné domény služby AD. Pokud máte více domén, můžete přidat jednu instanci této aplikace z Galerie aplikací Azure AD pro každou doménu služby Active Directory, pro kterou potřebujete zřídit.
- **Aplikace cloudového HR do zřizování uživatelů Azure AD** – když Azure AD Connect je nástroj, který se má použít k synchronizaci uživatelů služby AD s Azure AD, tato aplikace konektoru pro zřizování se dá použít k usnadnění zřizování uživatelů jenom pro cloud z aplikace cloudového HR do jednoho TENANTA Azure AD.
- **Zpětný zápis aplikace v cloudu HR** – Tato aplikace zřizovacího konektoru usnadňuje zpětný zápis e-mailových adres uživatele ze služby Azure AD do aplikace cloudového hr.

Níže uvedený obrázek obsahuje například seznam aplikací konektoru Workday dostupných v galerii aplikací Azure AD.

![Galerie aplikací Azure Active Directoryového portálu](./media/plan-cloudhr-provisioning/plan-cloudhr-provisioning-img2.png)

### <a name="decision-flowchart"></a>Vývojový diagram rozhodování

Pomocí níže uvedeného diagramu rozhodnutí určete, které aplikace pro zajištění cloudového HR jsou relevantní pro váš scénář.

![Vývojový diagram rozhodování](./media/plan-cloudhr-provisioning/plan-cloudhr-provisioning-img3.png)

## <a name="design-azure-ad-connect-provisioning-agent-deployment-topology"></a>Návrh Azure AD Connect zřizování topologie nasazení agenta

Integrace zřizování mezi aplikací cloudového HR a AD vyžaduje tyto čtyři komponenty:

- Tenant aplikace cloudového HR
- Aplikace konektoru zřizování
- Agent zřizování Azure AD Connect
- AD domain

Topologie nasazení agenta Azure AD Connect zřizování závisí na počtu klientů aplikace cloudového HR a podřízených domén AD, které chcete integrovat. Pokud máte víc domén služby Active Directory, záleží na tom, jestli jsou domény AD souvislé [nebo nesouvislé.](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/disjoint-namespace)

Na základě vašeho rozhodnutí vyberte jeden z scénářů nasazení:

- Tenant aplikace s jedním cloudovým HRm – > cílit na jednu nebo více podřízených domén AD v důvěryhodné doménové struktuře
- Tenant aplikace s jedním cloudovým HRm – > cílit více podřízených domén v oddělené doménové struktuře služby AD

### <a name="single-cloud-hr-app-tenant---single-or-multiple-target-ad-child-domains-in-a-trusted-forest"></a>Tenant aplikace s jedním cloudovým HRm – > jednu nebo více cílových doménových podřízených služby AD v důvěryhodné doménové struktuře

Doporučujeme následující konfiguraci výroby:

|Požadavek|Doporučení|
|:-|:-|
|Počet agentů zřizování Azure AD Connect k nasazení|2 (pro zajištění vysoké dostupnosti a převzetí služeb při selhání)
|Počet aplikací zřizovacích konektorů, které se mají nakonfigurovat|Jedna aplikace na podřízenou doménu|
|Hostitel serveru pro agenta zřizování Azure AD Connect|Windows 2012 R2 + s dohledem nad geograficky umístěnými řadiči domény AD</br>Může existovat vedle služby Azure AD Connect Service|

![Tok do místních agentů](./media/plan-cloudhr-provisioning/plan-cloudhr-provisioning-img4.png)

### <a name="single-cloud-hr-app-tenant---target-multiple-child-domains-in-a-disjoint-ad-forest"></a>Tenant aplikace s jedním cloudovým HRm – > cílit více podřízených domén v oddělené doménové struktuře služby AD

Tento scénář zahrnuje zřizování uživatelů z aplikace cloudového HR do domén v nesouvislých doménových strukturách AD.

Doporučujeme následující konfiguraci výroby:

|Požadavek|Doporučení|
|:-|:-|
|Počet agentů zřizování Azure AD Connect pro místní nasazení|2 na jednu nesouvislou doménovou strukturu služby AD|
|Počet aplikací zřizovacích konektorů, které se mají nakonfigurovat|Jedna aplikace na podřízenou doménu|
|Hostitel serveru pro agenta zřizování Azure AD Connect|Windows 2012 R2 + s dohledem nad geograficky umístěnými řadiči domény AD</br>Může existovat vedle služby Azure AD Connect Service|

![Tenant aplikace pro jediný Cloud je nesouvislá doménová struktura služby AD](./media/plan-cloudhr-provisioning/plan-cloudhr-provisioning-img5.png)

### <a name="azure-ad-connect-provisioning-agent-requirements"></a>Požadavky na agenty zřizování Azure AD Connect

Cloudové řešení pro zřizování uživatelů přes Cloud vyžaduje nasazení jednoho nebo více Azure AD Connect agentů zřizování na serverech se systémem Windows 2012 R2 nebo novějším s minimálním počtem 4 GB paměti RAM a .NET 4.7.1 + runtime. Zajistěte, aby měl hostitelský server síťový přístup k cílové doméně služby AD.

V rámci přípravy Prem prostředí Průvodce konfigurací agenta Azure AD Connectho zřizování registruje agenta u vašeho tenanta Azure AD, [otevře porty](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#open-ports), [umožní přístup k adresám URL](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#allow-access-to-urls)a podporuje [konfiguraci proxy odchozího protokolu HTTPS](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication).

Agent zřizování používá ke komunikaci s doménami AD účet služby. Před instalací agenta doporučujeme vytvořit účet služby v části Uživatelé a počítače služby AD, které splňují následující požadavky:

- Heslo, jehož platnost nevyprší
- Delegovaná oprávnění řízení pro čtení, vytváření, odstraňování a správu uživatelských účtů

Můžete vybrat řadiče domény, které by měly zpracovávat požadavky zřizování. Pokud máte několik geograficky distribuovaných řadičů domény, nainstalujte agenta zřizování ve stejné lokalitě jako vaše preferované řadiče domény, aby se zlepšila spolehlivost a výkon kompletního řešení.

Pro zajištění vysoké dostupnosti můžete nasadit více než jednoho Azure AD Connect agenta zřizování a zaregistrovat ho pro zpracování stejné sady místních domén AD.

## <a name="plan-scoping-filters-and-attribute-mapping"></a>Plánování filtrů oborů a mapování atributů

Když povolíte zřizování z aplikace cloudového HR do služby AD nebo Azure AD, Azure Portal řídí hodnoty atributu prostřednictvím mapování atributů.

### <a name="define-scoping-filters"></a>Definování filtrů oborů

Pomocí [filtrů oborů](https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters) definujte pravidla založená na atributech, která určují, kteří uživatelé by se měli zřídit z aplikace cloudového HR do AD nebo Azure AD.

Když inicializujete spojovací procesy, shromážděte následující požadavky:

- Používá aplikace cloudového HR k zapojení zaměstnanců a podmíněných pracovníků?
- Plánujete použití aplikace cloudového HR k zřizování uživatelů Azure AD ke správě zaměstnanců a podmíněných pracovníků?
- Plánujete zavedení aplikace cloudového HR do zřizování uživatelů Azure AD jenom pro podmnožinu uživatelů aplikace cloudového HR (například jenom zaměstnanci)?

V závislosti na vašich požadavcích můžete při konfiguraci mapování atributů nastavit pole **obor zdrojového objektu** a vybrat, které sady uživatelů v aplikaci cloudového HR by měly být v rozsahu pro zřizování služby AD. Pro běžně používané filtry oborů najdete informace v kurzu cloudové lidské aplikace.

### <a name="determine-matching-attributes"></a>Zjistit, které atributy se shodují

Díky zřizování získáte možnost párovat stávající účty mezi zdrojovým a cílovým systémem. Když integrujete aplikaci cloudového HR se službou Azure AD Provisioning, můžete [nakonfigurovat mapování atributů](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-automatic-user-provisioning-portal#mappings) , abyste zjistili, jaká data by měla být z aplikace CLOUDového HR předávána do služby AD nebo Azure AD.

Když inicializujete spojovací procesy, shromážděte následující požadavky:

- Jaké je jedinečné ID v této aplikaci cloudového HR, které se používá k identifikaci jednotlivých uživatelů?
- Jak můžete v perspektivě životního cyklu identity zpracovávat rekoupování? Zachovají si staré ID zaměstnanců?
- Zpracováváte budoucí přinájmy a účty AD předem pro ně vytvořené?
- Jak se v perspektivě životního cyklu identit řídíte zaměstnancem pro podmíněnou konverzi pracovního procesu nebo jinak?
- Uchovávají převedené uživatele svůj starý účet AD nebo získají nové?

V závislosti na vašem požadavku Azure AD podporuje přímé mapování atributů na atributy, poskytování konstantních hodnot nebo [psaní výrazů pro mapování atributů](https://docs.microsoft.com/azure/active-directory/active-directory-saas-writing-expressions-for-attribute-mappings). Díky této flexibilitě získáte maximální kontrolu nad tím, co se bude naplnit v atributu cílové aplikace. Pomocí [Microsoft Graph API](https://docs.microsoft.com/azure/active-directory/manage-apps/export-import-provisioning-configuration) a Graph Exploreru můžete exportovat mapování atributů zřizování uživatelů a schéma do souboru JSON a importovat ho zpátky do Azure AD.

**Ve výchozím nastavení**se atribut v aplikaci cloudového HR, který představuje jedinečné ID zaměstnance, používá jako odpovídající atribut *mapovaný k jedinečnému atributu ve službě AD.* Například ve scénáři aplikace Workday je atribut *Workday* *WorkerID* namapován na atribut AD *ČísloZaměstnance* .

Můžete nastavit více vyhovujících atributů a přiřadit prioritu porovnání. Jsou vyhodnocovány v porovnání s prioritou. Jakmile se najde shoda, nevyhodnocují se žádné další odpovídající atributy.

Můžete také [přizpůsobit výchozí mapování atributů](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes#understanding-attribute-mapping-types) , například změnit nebo odstranit existující mapování atributů, nebo vytvořit nová mapování atributů podle vašich obchodních potřeb. Seznam vlastních atributů k mapování najdete v kurzu aplikace cloudového HR (například pracovní [den](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#planning-workday-to-active-directory-user-attribute-mapping-and-transformations)).

### <a name="determine-user-account-status"></a>Určení stavu uživatelského účtu

Ve výchozím nastavení aplikace zřizovacího konektoru mapuje **stav uživatelských profilů lidských zdrojů** na **stav uživatelského účtu** ve službě AD nebo Azure AD a určí, jestli se má uživatelský účet povolit nebo zakázat.

Při zahájení procesu JOIN/Leavers shromážděte následující požadavky:

| Proces | Požadavky |
| - | - |
| **Odpojování** | Jak můžete v perspektivě životního cyklu identity zpracovávat rekoupování? Zachovají si staré ID zaměstnanců? |
| | Můžete zpracovávat budoucí přinájmy a vytvářet účty služby AD předem? Vytvářejí se tyto účty v povoleném nebo zakázaném stavu? |
| | Jak se v perspektivě životního cyklu identit řídíte zaměstnancem pro podmíněnou konverzi pracovního procesu nebo jinak? |
| | Uchovávají převedené uživatele svůj starý účet AD nebo získají nové? |
| **Leavers** | Jsou ukončení pro zaměstnance a podřízení zaměstnanců v REKLAMě zpracovávány jinak? |
| | Jaká data jsou zvažována pro zpracování ukončení uživatele? |
| | Jak ovlivňují převody zaměstnanců a podprocesů u stávajících účtů služby AD? |
| | Jak se zpracovává operace "zrušení" ve službě AD? Operace zrušení se musí zpracovat, pokud se ve službě AD vytvoří budoucí pronájem v rámci služby AD jako součást spojovacího procesu. |

V závislosti na vašich požadavcích můžete logiku mapování přizpůsobit pomocí [výrazů Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/functions-for-customizing-application-data) , aby byl účet AD povolený nebo zakázaný na základě kombinace datových bodů.

### <a name="map-cloud-hr-app-to-ad-user-attributes"></a>Mapování aplikace cloudového HR na atributy uživatelů AD

Každá aplikace cloudového HR je dodávána s výchozími mapováními aplikace cloudového HR ke službě AD.

Po zahájení procesu Joiners/Stěhovacís/Leavers shromážděte následující požadavky:

| Proces | Požadavky |
| - | - |
| **Odpojování** | Je proces vytváření účtu AD ruční, automatizovaná nebo částečně automatizovaná? |
| | Plánujete rozšířit vlastní atributy z aplikace cloudového HR do služby AD? |
| **Stěhovací společnosti** | Jaké atributy chcete zpracovat pokaždé, když se v aplikaci cloudového HR uskuteční operace "stěhovací společnosti"? |
| | Provádíte v době aktualizace uživatelů všechna konkrétní ověření atributů? Pokud ano, uveďte prosím podrobnosti. |
| **Leavers** | Jsou ukončení pro zaměstnance a podřízení zaměstnanců v REKLAMě zpracovávány jinak? |
| | Jaká data jsou zvažována pro zpracování ukončení uživatele? |
| | Jak ovlivňují převody zaměstnanců a podprocesů u stávajících účtů služby AD? |

V závislosti na vašich požadavcích můžete mapování upravit tak, aby splňovalo vaše cíle integrace. Seznam vlastních atributů k mapování najdete v kurzu konkrétní aplikace cloudového HR (například pracovní [den](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#planning-workday-to-active-directory-user-attribute-mapping-and-transformations)).

### <a name="generate-unique-attribute-value"></a>Generovat jedinečnou hodnotu atributu

Když zahájíte spojovací procesy, možná budete muset při nastavování atributů jako CN, samAccountName a UPN s jedinečnými omezeními generovat jedinečné hodnoty atributů.

Funkce Azure AD [SelectUniqueValues](https://docs.microsoft.com/azure/active-directory/manage-apps/functions-for-customizing-application-data#selectuniquevalue) vyhodnotí všechna pravidla a potom zkontroluje hodnotu generovanou v cílovém systému pro jedinečnost. Viz příklad [vygenerování jedinečné hodnoty pro atribut userPrincipalName (UPN)](https://docs.microsoft.com/azure/active-directory/manage-apps/functions-for-customizing-application-data#generate-unique-value-for-userprincipalname-upn-attribute).

> [!NOTE]
> Tato funkce je momentálně podporovaná jenom pro zřizování uživatelů z Workday do služby Active Directory. Nedá se použít s jinými zřizovacími aplikacemi.

### <a name="configure-ad-ou-container-assignment"></a>Konfigurace přiřazení kontejneru organizační jednotky AD

Je to běžný požadavek na umístění uživatelských účtů služby Active Directory do kontejnerů na základě obchodních jednotek, umístění a oddělení. Když zahájíte proces stěhovací společnosti a v případě, že dojde ke změně organizace pro dozor, bude pravděpodobně nutné přesunout uživatele z jedné organizační jednotky do jiné ve službě AD.

Pomocí funkce [Switch ()](https://docs.microsoft.com/azure/active-directory/manage-apps/functions-for-customizing-application-data#switch) nakonfigurujte obchodní logiku pro přiřazení organizační jednotky a namapujte ji na atribut AD *parentDistinguishedName*.

Například pokud chcete vytvořit uživatele v organizační jednotce na základě atributu HR "obec", můžete použít následující výraz.

`
Switch([Municipality], "OU=Default,OU=Users,DC=contoso,DC=com", "Dallas", "OU=Dallas,OU=Users,DC=contoso,DC=com", "Austin", "OU=Austin,OU=Users,DC=contoso,DC=com", "Seattle", "OU=Seattle,OU=Users,DC=contoso,DC=com", "London", "OU=London,OU=Users,DC=contoso,DC=com")
`

Pokud je v tomto výrazu hodnota správní hodnoty: Praha, Austin, Seattle nebo Londýn, bude uživatelský účet vytvořen v odpovídající organizační jednotce. Pokud se neshoduje, vytvoří se účet ve výchozí organizační jednotce.

## <a name="plan-for-password-delivery-of-new-user-accounts"></a>Plán pro doručování nových uživatelských účtů v hesle

Při zahájení procesu připojení budete muset nastavit a doručovat dočasné heslo nových uživatelských účtů. S cloudovým zajištěním pro zřizování uživatelů Azure AD můžete pro uživatele zavést funkci [samoobslužného resetování hesla](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr) (SSPR) služby Azure AD 1. den.

SSPR je jednoduchý způsob, jak správcům IT umožnit uživatelům resetovat hesla nebo odemykat jejich účtů. Můžete zřídit atribut **mobilní číslo** z aplikace cloudového HR do AD a synchronizovat ho s Azure AD. Jakmile je atribut **mobilní číslo** v Azure AD, můžete pro jeho účet povolit SSPR, takže noví uživatelé můžou k ověřování použít registrované a ověřené mobilní číslo.

## <a name="plan-for-initial-cycle"></a>Plánování počátečního cyklu

Když se služba zřizování Azure AD poprvé spustí, provede [počáteční cyklus](https://docs.microsoft.com/azure/active-directory/manage-apps/how-provisioning-works#initial-cycle) v rámci aplikace cloudového HR a vytvoří snímek všech uživatelských objektů v aplikaci cloudového hr. Doba potřebná pro počáteční cykly je přímo závislá na tom, kolik uživatelů se ve zdrojovém systému nachází. Počáteční cyklus pro některé klienty cloudových lidských aplikací s více než 100 000 uživateli může trvat dlouhou dobu.

**Pro klienty aplikace pro velký cloudový personál (> 30 000) doporučujeme** spustit počáteční cyklus v progresivních fázích a spustit přírůstkové aktualizace až po ověření, že ve službě AD jsou pro různé scénáře zřizování uživatelů nastavené správné atributy. Postupujte podle následujícího pořadí:

1. Spusťte počáteční cyklus pouze pro omezené skupiny uživatelů nastavením [filtru oborů](#plan-scoping-filters-and-attribute-mapping).
2. Ověřte, jestli zřizování účtů AD a hodnoty atributů nastavené pro uživatele vybrané pro první spuštění. Pokud výsledek splňuje vaše očekávání, rozbalte filtr rozsah a postupně uveďte více uživatelů a ověřte výsledky pro druhý běh.

Jakmile budete spokojeni s výsledky počátečního cyklu pro testovací uživatele, můžete spustit [přírůstkové aktualizace](https://docs.microsoft.com/azure/active-directory/manage-apps/how-provisioning-works#incremental-cycles).

## <a name="plan-testing-and-security"></a>Plánování testování a zabezpečení

V každé fázi nasazení z počátečního pilotu prostřednictvím povolení zřizování uživatelů se ujistěte, že testujete, že výsledky jsou očekávané a že budete auditovat cykly zřizování.

### <a name="plan-testing"></a>Plánování testování

Jakmile nakonfigurujete aplikaci cloudového HR na zřizování uživatelů Azure AD, budete muset spustit testové případy, abyste ověřili, jestli Toto řešení splňuje požadavky vaší organizace.

|Scénáře|Očekávané výsledky|
|:-|:-|
|Nový zaměstnanec zařazení do aplikace Cloud HR| – Uživatelský účet se zřídí ve službě AD.</br>– Uživatelé se mohou přihlašovat k aplikacím AD-Domain a provádět požadované akce.</br>– Pokud je nakonfigurovaná synchronizace AAD Connect, vytvoří se taky uživatelský účet ve službě Azure AD.
|Uživatel se ukončil v aplikaci cloudového HR.|– Uživatelský účet je ve službě AD zakázaný.</br>– Uživatel se nemůže přihlásit k žádným podnikovým aplikacím chráněným službou AD.
|V aplikaci cloudového HR se aktualizuje uživatelská kontrolní organizace.|Na základě mapování atributů se uživatelský účet přesune z jedné organizační jednotky do druhé ve službě AD.|
|Manažer uživatele aktualizací lidských aplikací v aplikaci Cloud HR|Pole manažera v AD se aktualizuje tak, aby odráželo jméno nového manažera.|
|HR rekoupí zaměstnance do nové role.|Chování závisí na tom, jak je aplikace cloudového HR nakonfigurovaná tak, aby generovala ID zaměstnanců:</br>– Pokud se původní ID zaměstnance znovu použije pro opětovné zařazení, pak konektor povolí pro uživatele existující účet služby AD.</br>– Pokud získají nové ID zaměstnance, konektor vytvoří pro uživatele nový účet služby AD.|
|HR převede zaměstnance na pracovníka smlouvy nebo naopak.|Vytvoří se nový účet AD pro nového a starý účet se v datu účinnosti převodu zakáže.|

Pomocí výše uvedených výsledků určete, jak převést automatickou implementaci zřizování uživatelů do produkčního prostředí založeného na vašich zavedených časových osách.

> [!TIP]
> Při obnovování testovacího prostředí s využitím produkčních dat k odebrání/maskování citlivých údajů (osobně identifikovatelných údajů) z hlediska ochrany osobních údajů a standardů zabezpečení doporučujeme používat techniky, jako je snižování dat a čištění dat.

### <a name="plan-security"></a>Plánování zabezpečení

V rámci nasazení nové služby je běžné, že je nutné provést kontrolu zabezpečení. Pokud je kontrola zabezpečení povinná nebo ještě neproběhla, přečtěte si prosím spoustu [dokumentů White Paper](https://www.microsoft.com/download/details.aspx?id=36391) pro Azure AD, které poskytují přehled o identitě jako službu.

### <a name="plan-rollback"></a>Naplánování vrácení zpět

Pokud implementace zřizování uživatelů v cloudovém prostředí v produkčním prostředí nefunguje podle potřeby, následující kroky vrácení zpět vám můžou pomoct při návratu do předchozího známého funkčního stavu:

1. Zkontrolujte [souhrnnou sestavu zřizování](check-status-user-account-provisioning.md#getting-provisioning-reports-from-the-azure-portal) a [protokoly zřizování](check-status-user-account-provisioning.md#provisioning-logs-preview) (informace najdete v tématu [Správa zřizování uživatelů cloudových lidských aplikací](#manage-your-configuration)), abyste zjistili, jaké nesprávné operace byly provedeny na ovlivněných uživatelích a skupinách.
2. Poslední známý dobrý stav uživatelů a skupin, které jsou ovlivněné, se dá určit prostřednictvím protokolů auditu zřizování nebo kontrolou cílových systémů (Azure AD nebo AD).
3. Spolupracujte s vlastníkem aplikace a aktualizujte uživatele nebo skupiny, které ovlivnily přímo v aplikaci, pomocí posledních známých hodnot stavu dobrý stav.

## <a name="deploy-the-cloud-hr-app"></a>Nasazení aplikace cloudového HR

Zvolte prosím aplikaci cloudového HR, která se rovná vašim požadavkům na řešení.

Kurz – odkaz [: Konfigurace pracovního dne pro Automatické zřizování uživatelů](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#planning-your-deployment) pro kroky, které potřebujete udělat, abyste importovali pracovní profily z Workday do AD i do Azure AD, a to s volitelným zpětným zápisem e-mailové adresy a uživatelského jména do Workday.

## <a name="manage-your-configuration"></a>Správa konfigurace

Azure AD může poskytovat další poznatky k zřizování uživatelů a provoznímu stavu vaší organizace prostřednictvím protokolů auditu a sestav.

### <a name="gain-insights-from-reports-and-logs"></a>Získání přehledů ze sestav a protokolů

Po úspěšném [počátečním cyklu](https://docs.microsoft.com/azure/active-directory/manage-apps/how-provisioning-works#initial-cycle)bude služba zřizování Azure AD i nadále spouštět přírůstkové přírůstkové aktualizace po neomezenou dobu v intervalech definovaných v kurzech specifických pro jednotlivé aplikace, dokud nedojde k jedné z následujících událostí:

- Služba se ručně zastavila a nový počáteční cyklus se aktivoval pomocí [Azure Portal](https://portal.azure.com/) nebo pomocí příslušného příkazu [Microsoft Graph API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview) .
- Nový počáteční cyklus se spustí z důvodu změny mapování atributů nebo filtrů oborů.
- Proces zřizování přejde do karantény z důvodu vysoké míry chyb a zůstane v karanténě po dobu více než čtyř týdnů, kdy bude automaticky vypnut.

Pokud si chcete projít tyto události a všechny další aktivity, které provádí služba zřizování, [Naučte se kontrolovat protokoly a získávat zprávy o aktivitě zřizování](https://docs.microsoft.com/azure/active-directory/manage-apps/check-status-user-account-provisioning).

#### <a name="azure-monitor-logs"></a>Protokoly služby Azure Monitor

Všechny aktivity prováděné službou zřizování se zaznamenávají v **protokolech auditu Azure AD**. Protokoly auditu Azure AD můžete směrovat do protokolů Azure Monitor k další analýze. **Protokoly Azure monitor (také označované jako Log Analytics pracovní prostor)** umožňují dotazovat se na data a vyhledávat události, analyzovat trendy a provádět korelace napříč různými zdroji dat. Podívejte se na toto [video](https://youtu.be/MP5IaCTwkQg) , kde se dozvíte o výhodách použití protokolů Azure monitor pro protokoly Azure AD v praktických scénářích uživatelů.

Můžete nainstalovat [zobrazení Log Analytics pro protokoly aktivit služby Azure AD](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-install-use-log-analytics-views) a získat tak přístup k [předem vytvořeným sestavám](https://github.com/AzureAD/Deployment-Plans/tree/master/Log%20Analytics%20Views) , které se seznámí s událostmi zřizování ve vašem prostředí.

Další informace najdete v tématu [Analýza protokolů aktivit Azure AD pomocí protokolů Azure monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics) .

### <a name="manage-personal-data"></a>Správa osobních údajů

Agent Azure AD Connect zřizování nainstalovaný na Windows serveru vytvoří protokoly v protokolu událostí Windows, které můžou obsahovat osobní údaje v závislosti na mapování atributů služby cloudového HR. Aby bylo možné dodržovat závazky týkající se ochrany osobních údajů uživatelů, můžete nastavit naplánovanou úlohu Windows pro vymazání protokolu událostí a zajistit, aby žádná data nebyla zachována déle než 48 hodin.

Služba zřizování Azure AD negeneruje sestavy, provádí analýzy ani neposkytuje přehledy po dobu 30 dnů. Proto služba zřizování Azure AD neukládá, zpracovává ani neuchovává žádná data déle než 30 dnů. 

### <a name="troubleshoot"></a>Řešení potíží

Pokud chcete vyřešit všechny problémy, které se můžou během zřizování zapnout, přečtěte si následující odkazy:

- [Problém s konfigurací zřizování uživatelů pro aplikaci Galerie Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem)
- [Synchronizace atributu z místní služby Active Directory do Azure AD kvůli zřizování pro aplikaci](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning-sync-attributes-for-mapping)
- [Zřizování uživatelů pro aplikaci Galerie Azure AD trvá několik hodin.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish)
- [Při ukládání přihlašovacích údajů správce při konfiguraci zřizování uživatelů pro aplikaci Galerie Azure Active Directory došlo k potížím.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem-storage-limit)
- [Pro aplikaci Galerie Azure AD se nezřídí žádní uživatelé.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem-no-users-provisioned)
- [Pro aplikaci Galerie Azure AD se zřizuje nesprávná sada uživatelů.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem-wrong-users-provisioned)
- [Nastavení Prohlížeč událostí Windows pro řešení potíží s agentem](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#setting-up-windows-event-viewer-for-agent-troubleshooting)
- [Nastavení protokolů auditu Azure Portal pro řešení potíží se službou](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#setting-up-azure-portal-audit-logs-for-service-troubleshooting)
- [Porozumění protokolům pro operace vytvoření účtu uživatele služby AD](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#understanding-logs-for-ad-user-account-create-operations)
- [Principy protokolů pro operace aktualizace pro správce](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#understanding-logs-for-manager-update-operations)
- [Řešení běžně zjištěných chyb](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#resolving-commonly-encountered-errors)

### <a name="next-steps"></a>Další kroky

- [Zápis výrazů pro mapování atributů](https://docs.microsoft.com/azure/active-directory/manage-apps/functions-for-customizing-application-data)
- [Přehled rozhraní API pro synchronizaci Azure AD](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)
- [Přeskočit odstranění uživatelských účtů, které přesahují rozsah](https://docs.microsoft.com/azure/active-directory/manage-apps/skip-out-of-scope-deletions)
- [Agent zřizování Azure AD Connect: Historie verzí](https://docs.microsoft.com/azure/active-directory/manage-apps/provisioning-agent-release-version-history)
