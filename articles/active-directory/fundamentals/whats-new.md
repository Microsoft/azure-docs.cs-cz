---
title: Co je nového? Poznámky – Azure Active Directory | Dokumentace Microsoftu
description: Zjistěte, co je nové službě Azure Active Directory, jako je nejnovější poznámky k verzi, známé problémy, opravy chyb, zastaralé funkce a nadcházející změny.
services: active-directory
author: eross-msft
manager: daveba
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: lizross
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.openlocfilehash: 0847096674d446940ed3fed3390457e3d2e894bf
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55248448"
---
# <a name="whats-new-in-azure-active-directory"></a>Co je nového ve službě Azure Active Directory?

>Dostanete o tom, kdy revidovat tuto stránku aktualizace zkopírováním a vložením tuto adresu URL: `https://docs.microsoft.com/api/search/rss?search=%22release+notes+for+azure+AD%22&locale=en-us` do vaší ![ikona RSS](./media/whats-new/feed-icon-16x16.png) kanálů.

Azure AD obdrží vylepšení průběžně. Abyste mohli používat aktuální pomocí nejnovější vývoj, tento článek poskytuje informace o:

- Nejnovější vydané verzi
- Známé problémy
- Opravy chyb
- Zastaralé funkce
- Plány pro změny

Tato stránka se aktualizuje každý měsíc, takže návštěvě pravidelně. Pokud hledáte položky, které jsou starší než šest měsíců, najdete je [archiv co je nového ve službě Azure Active Directory](whats-new-archive.md).

---
## <a name="january-2019"></a>2019. ledna

### <a name="new-azure-ad-application-proxy-cookie-settings"></a>Nové nastavení souborů cookie Proxy aplikací Azure AD

**Typ:** Nová funkce  
**Kategorie služby:** Proxy aplikace  
**Funkce produktu:** Řízení přístupu

Zavedli jsme tři nové nastavení souborů cookie, k dispozici pro vaše aplikace, které jsou publikované prostřednictvím Proxy aplikací:

- **Použijte soubor cookie jenom pro protokol HTTP.** Nastaví **HTTPOnly** příznak na soubory cookie přístupu a relace Proxy aplikací. Zapnutím tohoto nastavení poskytuje další bezpečnostní výhody, jako je například zabránění kopírování nebo úprava souborů cookie pomocí skriptování na straně klienta. Doporučujeme vám zapnout tento příznak (zvolte **Ano**) pro další výhody.

- **Použití zabezpečeného souboru cookie.** Nastaví **Secure** příznak na soubory cookie přístupu a relace Proxy aplikací. Zapnutím tohoto nastavení poskytuje další bezpečnostní výhody, ujistěte se, že soubory cookie jenom při přenosu TLS zabezpečené kanály, jako je například HTTPS. Doporučujeme vám zapnout tento příznak (zvolte **Ano**) pro další výhody.

- **Použijte soubor cookie trvalý.** Zabrání vypršení platnosti při zavření prohlížeče soubory cookie přístup. Tyto soubory cookie poslední po dobu platnosti přístupového tokenu. Soubory cookie jsou však obnovit, pokud je dosaženo času vypršení platnosti nebo pokud uživatel ručně odstraní soubor cookie. Doporučujeme ponechat výchozí nastavení **ne**, pouze zapnout v nastavení pro starší aplikace, které není sdílení souborů cookie mezi procesy.

Další informace o nové soubory cookie najdete v tématu [nastavení souborů Cookie pro přístup k místním aplikacím v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2019"></a>Nové federované aplikace k dispozici v galerii aplikací Azure AD – leden 2019

**Typ:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Funkce produktu:** Integrace třetích stran
 
V lednu 2019 jsme přidali podporu těchto 35 nových aplikací s federací do Galerie aplikací:

[Firstbird](https://docs.microsoft.com/azure/active-directory/saas-apps/firstbird-tutorial), [Folloze](https://docs.microsoft.com/azure/active-directory/saas-apps/folloze-tutorial), [talentu palety](https://docs.microsoft.com/azure/active-directory/saas-apps/talent-palette-tutorial), [Infor CloudSuite](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloud-suite-tutorial), [Cisco zastřešující](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-umbrella-tutorial), [Zscaler Správce přístupu k Internetu](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-internet-access-administrator-tutorial), [vypršení platnosti připomenutí](https://docs.microsoft.com/azure/active-directory/saas-apps/expiration-reminder-tutorial), [InstaVR prohlížeč](https://docs.microsoft.com/azure/active-directory/saas-apps/instavr-viewer-tutorial), [CorpTax](https://docs.microsoft.com/azure/active-directory/saas-apps/corptax-tutorial), [příkaz](https://app.verb.net/login), [MazeMap odkaz](https://docs.microsoft.com/azure/active-directory/saas-apps/mazemaplink-tutorial), [OpenLattice](https://openlattice.com/agora), [TheOrgWiki](https://www.theorgwiki.com/signup), [Pavaso digitální zavřete](https://docs.microsoft.com/azure/active-directory/saas-apps/pavaso-digital-close-tutorial), [ Sada nástrojů GoodPractice](https://docs.microsoft.com/azure/active-directory/saas-apps/goodpractice-toolkit-tutorial), [cloudové služby PICCO](https://docs.microsoft.com/azure/active-directory/saas-apps/cloud-service-picco-tutorial), [AuditBoard](https://docs.microsoft.com/azure/active-directory/saas-apps/auditboard-tutorial), [iProva](https://docs.microsoft.com/azure/active-directory/saas-apps/iprova-tutorial), [Workable](https://docs.microsoft.com/azure/active-directory/saas-apps/workable-tutorial), [ CallPlease](https://webapp.callplease.com/create-account/create-account.html), [systém jednotného přihlašování GTNexus](https://docs.microsoft.com/azure/active-directory/saas-apps/gtnexus-sso-module-tutorial), [CBRE ServiceInsight](https://docs.microsoft.com/azure/active-directory/saas-apps/cbre-serviceinsight-tutorial), [Deskradar](https://docs.microsoft.com/azure/active-directory/saas-apps/deskradar-tutorial), [Coralogixv](https://docs.microsoft.com/azure/active-directory/saas-apps/coralogix-tutorial), [Signagelive](https://docs.microsoft.com/azure/active-directory/saas-apps/signagelive-tutorial), [ARECH pro podniky](https://docs.microsoft.com/azure/active-directory/saas-apps/ares-for-enterprise-tutorial), [K2 pro Office 365](https://www.k2.com/O365), [Xledger](https://www.xledger.net/), [ iDiD správce](https://docs.microsoft.com/azure/active-directory/saas-apps/idid-manager-tutorial), [HighGear](https://docs.microsoft.com/azure/active-directory/saas-apps/highgear-tutorial), [Visitly](https://docs.microsoft.com/azure/active-directory/saas-apps/visitly-tutorial), [Korn Ferry ALP](https://docs.microsoft.com/azure/active-directory/saas-apps/korn-ferry-alp-tutorial), [CaféX portálu (schůzky) ](https://docs.microsoft.com/azure/active-directory/saas-apps/caf%c3%a9xportal%28meetings%29-tutorial), [Acadia](https://docs.microsoft.com/azure/active-directory/saas-apps/acadia-tutorial), [Adoddle cSaas platformy](https://docs.microsoft.com/azure/active-directory/saas-apps/adoddle-csaas-platform-tutorial)        

Další informace o aplikacích najdete v tématu [integrace aplikací SaaS pomocí Azure Active Directory](https://aka.ms/appstutorial). Další informace o zobrazení aplikace v galerii aplikací Azure AD najdete v tématu [uvedení aplikace v galerii aplikací Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-azure-ad-identity-protection-enhancements-public-preview"></a>Nová vylepšení Azure AD Identity Protection (verze Public preview)

**Typ:** Změněná funkce  
**Kategorie služby:** Identity Protection  
**Funkce produktu:** Zabezpečení a ochrana identity

S potěšením oznamujeme, že jsme přidali tato vylepšení nabídky Azure AD Identity Protection ve verzi public preview, včetně:

- Aktualizované a více integrované uživatelské rozhraní

- Další rozhraní API

- Vylepšené rizika prostřednictvím strojového učení

- Celý produkt koordinaci rizikoví uživatelé a rizikových přihlášení

Další informace o rozšířeních najdete v části [co je Azure Active Directory Identity Protection (Aktualizovat)?](https://aka.ms/IdentityProtectionDocs) Další informace a sdílet své myšlenky pomocí pokynů v rámci produktu.

---

### <a name="new-app-lock-feature-for-the-microsoft-authenticator-app-on-ios-and-android-devices"></a>Nová funkce zámku aplikace pro aplikaci Microsoft Authenticator v Iosu a zařízení s Androidem

**Typ:** Nová funkce  
**Kategorie služby:** Aplikace Microsoft Authenticator  
**Funkce produktu:** Zabezpečení a ochrana identity

K lepšímu zabezpečení jednorázového hesla, informace o aplikaci a nastavení aplikace, můžete zapnout funkci App Lock v aplikaci Microsoft Authenticator. Zapnutí zámku aplikace znamená, že budete ke správě ověřování pomocí svůj PIN kód nebo biometrické pokaždé, když otevřete aplikaci Microsoft Authenticator.

Další informace najdete v tématu [aplikaci Microsoft Authenticator nejčastější dotazy k](https://docs.microsoft.com/azure/active-directory/user-help/microsoft-authenticator-app-faq).

---

### <a name="enhanced-azure-ad-privileged-identity-management-pim-export-capabilities"></a>Vylepšené Azure AD Privileged Identity Management (PIM) exportovat funkce

**Typ:** Nová funkce  
**Kategorie služby:** Privileged Identity Management  
**Funkce produktu:** Privileged Identity Management

Správci služby privileged Identity Management (PIM) můžete nyní exportovat všechna přiřazení rolí aktivní a bude možné pro určitý prostředek, který obsahuje přiřazení rolí pro všechny podřízené prostředky. Dříve bylo obtížné pro správce, chcete-li získat úplný seznam přiřazení rolí pro předplatné a měli zajišťující export přiřazení rolí pro každou konkrétní prostředek.

Další informace najdete v tématu [zobrazit historii aktivit a auditu pro role prostředků Azure v PIM](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

---

## <a name="novemberdecember-2018"></a>Listopad/prosince 2018

### <a name="users-removed-from-synchronization-scope-no-longer-switch-to-cloud-only-accounts"></a>Uživatelé Odebraní z oboru synchronizace žádný delší přepínač na účty jenom cloudu

**Typ:** Pevné  
**Kategorie služby:** Správa uživatelů  
**Funkce produktu:** Adresář

>[!Important]
>Jsme zjistili a pochopení vaší frustrace kvůli tato oprava. Proto jsme jste vrátit zpět tuto změnu až do doby, že bychom mohli vyřešit snazší pro vás k implementaci ve vaší organizaci.

Opravili jsme chybu, ve kterém by se nesprávně přepnout příznaku DirSyncEnabled uživatele do **False** při objektu služby Active Directory Domain Services (AD DS) byl vyloučen ze synchronizace obor a poté přesunut do koše v Azure AD na následující cyklus synchronizace. V důsledku této opravy, pokud uživatel je vyloučen z rozsah synchronizace a potom obnovit z Azure Koš služby Active Directory, uživatelský účet zůstane synchronizovaná z místní služby AD, podle očekávání a nejde je spravovat v cloudu, protože bude nadála zdrojem authority (SoA) místní služby AD.

Před touto opravou došlo k problému při příznaku DirSyncEnabled byl přepnut na hodnotu False. To zadali nesprávné dojem, která tyto účty se převedly na objekty jenom cloudu a, který může spravovat účty v cloudu. Však účty stále zachována jejich SoA jako místní a všechny synchronizované vlastnosti (stínové atributy) pocházející z místní služby AD. Tuto podmínku způsobil několik problémů ve službě Azure AD a dalších cloudových úloh (jako je Exchange Online), která má zpracovávat tyto účty synchronizovat ze služby AD, ale se nyní chová podobně jako účty jenom cloudu.

V tuto chvíli je jediný způsob, jak skutečně převod účtu synchronizované z AD výhradně cloudový účet tím, že zakážete nástroje DirSync na úrovni tenanta, který aktivuje back-endová operace pro přenos SoA. Tento typ změny SoA vyžaduje (ale není omezena pouze na) čištění všechny místní související s atributy (jako je například LastDirSyncTime a atributy stínové) a odešle signál do jiných cloudových úloh jeho odpovídající objekt převést na výhradně cloudový účet příliš .

Tato oprava proto zabraňuje s přímým přístupem aktualizace u atributu ImmutableID uživatele synchronizovat ze služby AD, které se v některých scénářích v minulosti byly zapotřebí. Záměrné ImmutableID objektu ve službě Azure AD, jak již název napovídá, měl by být neměnné. Nové funkce, které jsou implementovány v klientovi Azure AD Connect Health a synchronizace Azure AD Connect jsou k dispozici v těchto situacích:

- **Ve velkém měřítku ImmutableID aktualizace pro mnoho uživatelů v rámci fázované přístupu**
  
  Například budete muset provést migraci zdlouhavé doménovými strukturami služby AD DS. Řešení: Použití služby Azure AD Connect k **konfigurovat zdrojové ukotvení** a jako uživatel provede migraci, zkopírovat existující hodnoty ImmutableID ze služby Azure AD do místní služby AD DS uživatele atribut ms-DS-konzistence – Guid nové doménové struktury. Další informace najdete v tématu [pomocí ms-DS-ConsistencyGuid jako parametru sourceAnchor](/azure/active-directory/hybrid/plan-connect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor).

- **Ve velkém měřítku ImmutableID aktualizace pro mnoho uživatelů v jednom kroku**

  Například při implementaci služby Azure AD Connect uděláte chybu, a teď je potřeba změnit atribut SourceAnchor. Řešení: Zakázat nástroje DirSync na úrovni tenanta a zrušte všechny neplatné hodnoty ImmutableID. Další informace najdete v tématu [vypnout synchronizaci adresářů pro Office 365](/office365/enterprise/turn-off-directory-synchronization).

- **Rematch místní uživatel s existujícím uživatelem ve službě Azure AD** například uživatel, který je znovu vytvořit ve službě AD DS vygeneruje duplicitní v účtu služby Azure AD namísto rematching s existující účet služby Azure AD (osamocený objekt). Řešení: Přemapujte zdrojové ukotvení/ImmutableID pomocí Azure AD Connect Health na webu Azure Portal. Další informace najdete v tématu [Orphaned objekt scénář](/azure/active-directory/hybrid/how-to-connect-health-diagnose-sync-errors#orphaned-object-scenario).

### <a name="breaking-change-updates-to-the-audit-and-sign-in-logs-schema-through-azure-monitor"></a>Zásadní změna: Aktualizace auditu a protokolů přihlášení schématu prostřednictvím služby Azure Monitor

**Typ:** Změněná funkce  
**Kategorie služby:** Vytváření sestav  
**Funkce produktu:** Monitorování a sestavy

Audit i přihlášení datové proudy protokolu v nástroji Azure Monitor jsme aktuálně publikování, můžete bez problémů integrovat soubory protokolů pomocí nástrojů systému SIEM nebo pomocí Log Analytics. Na základě vaší zpětné vazby a během přípravy na tuto funkci ohlášení obecné dostupnosti, vám s tím následující změny naše schématu. První týden v lednu provede tyto změny ve schématu a jeho aktualizace související dokumentaci.

#### <a name="new-fields-in-the-audit-schema"></a>Nová pole ve schématu auditu
Přidáváme nový **typ operace** pole pro zadání typu operace provedena na prostředku. Například **přidat**, **aktualizace**, nebo **odstranit**.

#### <a name="changed-fields-in-the-audit-schema"></a>Změněné pole ve schématu auditu
Ve schématu auditu se mění následující pole:

|Název pole|Co se změnilo|Staré hodnoty|Nové hodnoty|
|----------|------------|----------|----------|
|Kategorie|To bylo **název služby** pole. Je teď **kategorií auditu** pole. **Název služby** byl přejmenován na **loggedByService** pole.|<ul><li>Zřizování účtů</li><li>Základní adresář</li><li>Samoobslužné resetování hesla</li></ul>|<ul><li>Správa uživatelů</li><li>Správa skupin</li><li>Správa aplikací</li></ul>|
|targetResources|Zahrnuje **TargetResourceType** na nejvyšší úrovni.|&nbsp;|<ul><li>Zásada</li><li>Aplikace</li><li>Uživatel</li><li>Skupina</li></ul>|
|loggedByService|Obsahuje název služby, která vygeneruje protokol auditu.|Null|<ul><li>Zřizování účtů</li><li>Základní adresář</li><li>Samoobslužné resetování hesla</li></ul>|
|Výsledek|Poskytuje výsledek funkce protokolů auditu. Dříve to bylo uvedené, ale zobrazujeme skutečnou hodnotu.|<ul><li>0</li><li>1</li></ul>|<ul><li>Úspěch</li><li>Selhání</li></ul>|

#### <a name="changed-fields-in-the-sign-in-schema"></a>Změněné pole ve schématu přihlášení
Následující pole jsou změny ve schématu přihlášení:

|Název pole|Co se změnilo|Staré hodnoty|Nové hodnoty|
|----------|------------|----------|----------|
|appliedConditionalAccessPolicies|To bylo **conditionalaccessPolicies** pole. Je teď **appliedConditionalAccessPolicies** pole.|Beze změny|Beze změny|
|conditionalAccessStatus|Poskytuje výsledek funkce stav zásad podmíněného přístup při přihlášení. Dříve to bylo uvedené, ale zobrazujeme skutečnou hodnotu.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Úspěch</li><li>Selhání</li><li>Nevztahuje se.</li><li>Zakázáno</li></ul>|
|appliedConditionalAccessPolicies: výsledek|Poskytuje výsledek funkce jednotlivých stavu podmíněného přístupu zásad při přihlášení. Dříve to bylo uvedené, ale zobrazujeme skutečnou hodnotu.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Úspěch</li><li>Selhání</li><li>Nevztahuje se.</li><li>Zakázáno</li></ul>|

Další informace o schématu najdete v tématu [protokolů auditu Azure AD interpretaci schématu ve službě Azure Monitor (preview)](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema)

---

### <a name="identity-protection-improvements-to-the-supervised-machine-learning-model-and-the-risk-score-engine"></a>Vylepšení ochrany identit na pod dohledem modelu strojového učení a modul určení skóre rizika

**Typ:** Změněná funkce  
**Kategorie služby:** Identity Protection  
**Funkce produktu:** Skóre rizika

Vylepšení související s Identity Protection uživatele a přihlašování riziko posouzení modul může pomoct zlepšit přesnost riziko uživatele a pokrytí. Správci si všimnout, že úroveň rizika uživatele je již přímo propojený úroveň rizika konkrétních detekcí a že je zvýšení počet a úroveň události rizikových přihlášení.

Detekce rizik jsou nyní vyhodnocovány pomocí technik strojového učení modelu, který se vypočítává riziko uživatele model detekcí a další funkce přihlášení uživatele. Na základě tohoto modelu, Správce může najít uživatele s vysokým rizikem skóre, i v případě detekce přidružené k tomuto uživateli je střední nebo nízké riziko. 

---

### <a name="administrators-can-reset-their-own-password-using-the-microsoft-authenticator-app-public-preview"></a>Správci můžou resetovat vlastní hesla pomocí aplikace Microsoft Authenticator (Public preview)

**Typ:** Změněná funkce  
**Kategorie služby:** Samoobslužné resetování hesla  
**Funkce produktu:** Ověřování uživatelů

Správci služby Azure AD teď můžou resetovat svoje vlastní hesla použijte v oznámeních aplikací Microsoft Authenticator nebo kód z libovolné mobilní ověřovací aplikace nebo hardwarového tokenu. Pokud chcete resetovat vlastní heslo, správci teď budou moct používat dva z následujících metod:

- Oznámení aplikace Microsoft Authenticator

- Jiné mobilní ověřovací aplikace / hardwaru token kódu

- Email

- Telefonní hovor

- Textová zpráva

Další informace o používání aplikace Microsoft Authenticator k resetování hesel najdete v tématu [resetování hesla pomocí samoobslužné služby Azure AD – mobilní aplikace a samoobslužné resetování HESLA (ve verzi Preview)](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks#mobile-app-and-sspr-preview)

---

### <a name="new-azure-ad-cloud-device-administrator-role-public-preview"></a>Nová role správce cloudových zařízení Azure AD (Public preview)

**Typ:** Nová funkce  
**Kategorie služby:** Registrace a správa zařízení  
**Funkce produktu:** Řízení přístupu

Správci mohou přiřadit uživatele k nové roli správce cloudových zařízení provádět úlohy správce cloudové zařízení. Uživatelé s rolí správce v zařízení cloudu můžete povolit, zakázat a odstraňovat zařízení ve službě Azure AD, spolu se nebudou moct číst klíče nástroje BitLocker systému Windows 10 (pokud existuje) na webu Azure Portal.

Další informace o rolích a oprávněních najdete v tématu [přiřazení rolí správce v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)

---

### <a name="manage-your-devices-using-the-new-activity-timestamp-in-azure-ad-public-preview"></a>Správa zařízení pomocí nové časové razítko aktivity ve službě Azure AD (Public preview)

**Typ:** Nová funkce  
**Kategorie služby:** Registrace a správa zařízení  
**Funkce produktu:** Správa životního cyklu zařízení

Uvědomujeme si, že v čase musíte aktualizace a vyřazení zařízení vaší organizace ve službě Azure AD, abyste se vyhnuli nutnosti zastaralá zařízení předsazení ve vašem prostředí. Smyslem tohoto procesu, teď Azure AD aktualizuje vaše zařízení s časovým razítkem nové aktivity pomáhají spravovat životní cyklus vašeho zařízení.

Další informace o tom, jak získat a použít tento časové razítko, naleznete v tématu [How To: Správa zastaralých zařízení ve službě Azure AD](https://docs.microsoft.com/azure/active-directory/devices/manage-stale-devices)

---

### <a name="administrators-can-require-users-to-accept-a-terms-of-use-on-each-device"></a>Správci můžou vyžadovat, aby uživatelé přijali podmínky použití na všech zařízeních

**Typ:** Nová funkce  
**Kategorie služby:** Podmínky použití  
**Funkce produktu:** Zásady správného řízení
 
Správci mohou nyní zapnout **vyžadují, aby uživatelé vyjádřili souhlas na všech zařízeních** možnost vyžadovat, aby vaši uživatelé tak, aby přijímal vaše podmínky použití na všech zařízeních, že používáte ve svém tenantovi.

Další informace najdete v tématu [jednotlivá zařízení podmínky použití části Azure Active Directory Terms of použít funkci](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou#per-device-terms-of-use).

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-a-recurring-schedule"></a>Správci můžou konfigurovat podmínky použití vypršení platnosti podle opakovaného plánu

**Typ:** Nová funkce  
**Kategorie služby:** Podmínky použití  
**Funkce produktu:** Zásady správného řízení
 

Správci mohou nyní zapnout **vyprší souhlasy** vyprší možnosti nastavte podmínky použití pro všechny vaše uživatele podle zadaného plánu opakování. Plán může být každoročně, bi – roční, čtvrtletně nebo každý měsíc. Po vypršení platnosti podmínky použití, musí uživatelé znovu přijali jako.

Další informace najdete v tématu [přidat podmínky použití části Azure Active Directory Terms of použít funkci](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou#add-terms-of-use).

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-each-users-schedule"></a>Správci můžou konfigurovat podmínky použití vyprší na základě plánu každého uživatele

**Typ:** Nová funkce  
**Kategorie služby:** Podmínky použití  
**Funkce produktu:** Zásady správného řízení

Správci teď mohou určit dobu trvání tento uživatel musí znovu přijali jako podmínky použití. Například Správci mohou určit, že uživatelé musí znovu přijali jako podmínky použití každých 90 dní.

Další informace najdete v tématu [přidat podmínky použití části Azure Active Directory Terms of použít funkci](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou#add-terms-of-use).
 
---

### <a name="new-azure-ad-privileged-identity-management-pim-emails-for-azure-active-directory-roles"></a>E-mailem na nový Azure AD Privileged Identity Management (PIM) pro role Azure Active Directory

**Typ:** Nová funkce  
**Kategorie služby:** Privileged Identity Management  
**Funkce produktu:** Privileged Identity Management
 
Zákazníci, kteří používají Azure AD Privileged Identity Management (PIM) teď můžou přijímat týdenní e-maily ověřování algoritmem digest, včetně následujících informací za posledních sedm dní:

- Přehled role nejvyšší oprávněných a trvalých přiřazení

- Počet uživatelů aktivace role

- Počet uživatelů přidružených k rolím v PIM

- Počet uživatelů přidružených k rolím mimo PIM

- Počet uživatelů "provedeny trvalé" v PIM

Další informace o PIM a k dispozici e-mailových oznámení najdete v tématu [e-mailová oznámení v PIM](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-email-notifications).

---

### <a name="group-based-licensing-is-now-generally-available"></a>Licencování na základě skupiny je teď obecně dostupná

**Typ:** Změněná funkce  
**Kategorie služby:** Ostatní  
**Funkce produktu:** Adresář

Licencování na základě skupiny je mimo verzi public preview a je teď obecně dostupná. Jako součást této obecné verzi jsme provedli tato funkce větší škálovatelnost a přidali možnost opětovné zpracování licencí přiřazení na základě skupin pro jednoho uživatele a možnost použití skupinové licencování s Office 365 E3 nebo A3 licence.

Další informace o licencování pro skupiny, najdete v části [co je založené na skupinách licencování v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-licensing-whatis-azure-portal)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2018"></a>Nové federované aplikace k dispozici v galerii aplikací Azure AD –. listopadu 2018

**Typ:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Funkce produktu:** Integrace třetích stran
 
V listopadu 2018 jsme přidali podporu těchto 26 nových aplikací s federací do Galerie aplikací:

[CoreStack](https://cloud.corestack.io/site/login), [HubSpot](https://docs.microsoft.com/azure/active-directory/saas-apps/HubSpot-tutorial), [GetThere](https://docs.microsoft.com/azure/active-directory/saas-apps/getthere-tutorial), [stínovat Pe](https://docs.microsoft.com/azure/active-directory/saas-apps/grape-tutorial), [upl.hodina](https://getehour.com/try-now), [Consent2Go](https://docs.microsoft.com/azure/active-directory/saas-apps/Consent2Go-tutorial), [Appinux](https://docs.microsoft.com/azure/active-directory/saas-apps/appinux-tutorial), [DriveDollar](https://www.drivedollar.com/Account/Login), [Useall](https://docs.microsoft.com/azure/active-directory/saas-apps/useall-tutorial), [nekonečné Campus](https://docs.microsoft.com/azure/active-directory/saas-apps/infinitecampus-tutorial), [Alaya](https://alayagood.com/en/demo/), [ HeyBuddy](https://docs.microsoft.com/azure/active-directory/saas-apps/heybuddy-tutorial), [Wrike SAML](https://docs.microsoft.com/azure/active-directory/saas-apps/wrike-tutorial), [odchylek](https://docs.microsoft.com/azure/active-directory/saas-apps/drift-tutorial), [Zenegy pro firmy centrální 365](https://accounting.zenegy.com/), [Everbridge člen portál](https://docs.microsoft.com/azure/active-directory/saas-apps/everbridge-tutorial), [IDEO](https://profile.ideo.com/users/sign_up), [Ivanti portálu Service Manager (ISM)](https://docs.microsoft.com/azure/active-directory/saas-apps/ivanti-service-manager-tutorial), [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-tutorial), [jednotného přihlašování Allbound](https://docs.microsoft.com/azure/active-directory/saas-apps/allbound-sso-tutorial), [složitých aplikací – Klasické testu](https://test.plexonline.com/signon), [složitých aplikací – klasické](https://www.plexonline.com/signon), [složitých aplikací – Test uživatelského rozhraní](https://test.cloud.plex.com/sso), [složitých aplikací – UX](https://cloud.plex.com/sso), [složitých aplikací – IAM](https://accounts.plex.com/), [Mě - pro péči o děti záznamů, účast a finanční sledování systému](https://getcrafts.ca/craftsregistration) 

Další informace o aplikacích najdete v tématu [integrace aplikací SaaS pomocí Azure Active Directory](https://aka.ms/appstutorial). Další informace o zobrazení aplikace v galerii aplikací Azure AD najdete v tématu [uvedení aplikace v galerii aplikací Azure Active Directory](https://aka.ms/azureadapprequest).

---

## <a name="october-2018"></a>Říjen 2018

### <a name="azure-ad-logs-now-work-with-azure-log-analytics-public-preview"></a>Protokoly Azure AD teď fungují s Azure Log Analytics (Public Preview)

**Typ:** Nová funkce  
**Kategorie služby:** Vytváření sestav  
**Funkce produktu:** Monitorování a sestavy

S potěšením oznamujeme, že teď můžou přesměrovávat protokolů služby Azure AD do služby Azure Log Analytics. Tato funkce nejžádanějších pomáhá poskytnout ještě lepší přístup k analytics pro vaši firmu, operace a zabezpečení, jakož i způsob, jak pomoc při sledování infrastruktury. Další informace najdete v tématu [protokoly aktivit Azure Active Directory v Azure Log Analytics teď k dispozici](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-Activity-logs-in-Azure-Log-Analytics-now/ba-p/274843) blogu.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2018"></a>V galerii aplikací Azure AD jsou k dispozici nové federované aplikace – říjen 2018

**Typ:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Funkce produktu:** Integrace třetích stran
 
V říjnu 2018 jsme přidali podporu těchto 14 nových aplikací s federací do Galerie aplikací:

[Mé body ocenění](https://docs.microsoft.com/azure/active-directory/saas-apps/myawardpoints-tutorial), [Vibe HCM](https://docs.microsoft.com/azure/active-directory/saas-apps/vibehcm-tutorial), ambyint, [MyWorkDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/myworkdrive-tutorial), [BorrowBox](https://docs.microsoft.com/azure/active-directory/saas-apps/borrowbox-tutorial), Dialpad, [virtuální prostředí ON24](https://docs.microsoft.com/azure/active-directory/saas-apps/on24-tutorial), [RingCentral](https://docs.microsoft.com/azure/active-directory/saas-apps/ringcentral-tutorial), [Zscalerem tři](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-three-tutorial), [Phraseanet](https://docs.microsoft.com/azure/active-directory/saas-apps/phraseanet-tutorial), [Appraisd](https://docs.microsoft.com/azure/active-directory/saas-apps/appraisd-tutorial), [Workspot řízení](https://docs.microsoft.com/azure/active-directory/saas-apps/workspotcontrol-tutorial), [Shuccho Navi](https://docs.microsoft.com/azure/active-directory/saas-apps/shucchonavi-tutorial), [Glassfrog](https://docs.microsoft.com/azure/active-directory/saas-apps/glassfrog-tutorial)

Další informace o aplikacích najdete v tématu [integrace aplikací SaaS pomocí Azure Active Directory](https://aka.ms/appstutorial). Další informace o zobrazení aplikace v galerii aplikací Azure AD najdete v tématu [uvedení aplikace v galerii aplikací Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="azure-ad-domain-services-email-notifications"></a>E-mailová oznámení Azure AD Domain Services

**Typ:** Nová funkce  
**Kategorie služby:** Azure AD Domain Services  
**Funkce produktu:** Azure AD Domain Services

Azure AD Domain Services poskytuje upozornění na portálu Azure portal o nesprávné konfigurace nebo problémy s vaší spravované domény. Tyto výstrahy obsahují podrobné pokyny, takže se můžete pokusit opravit problémy, aniž by museli kontaktovat podporu.

Od října, budete mít k přizpůsobení nastavení oznámení pro spravované domény, pokud dojde k nové výstrahy, e-mail je odeslán do určeným skupiny lidí, tím eliminuje nutnost neustále podívejte se na portál pro aktualizace.

Další informace najdete v tématu [nastavení oznámení ve službě Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-notifications).

---

### <a name="azure-ad-portal-supports-using-the-forcedelete-domain-api-to-delete-custom-domains"></a>Portál Azure AD podporuje odstraňování vlastních domén pomocí rozhraní API domény ForceDelete 

**Typ:** Změněná funkce  
**Kategorie služby:** Správa adresářů  
**Funkce produktu:** Adresář

S radostí oznamujeme, že teď můžete použít domény ForceDelete rozhraní API odstranit vaši vlastní názvy domén asynchronně přejmenováním odkazy, jako jsou uživatelé, skupiny a aplikace z vlastního názvu domény (contoso.com) zpět na počáteční výchozí doména název ( contoso.onmicrosoft.com).

Tato změna vám umožní rychleji odstranit názvů vlastních domén, pokud vaše organizace již název používá, nebo pokud je třeba použít název domény pomocí jiné služby Azure AD.

Další informace najdete v tématu [odstranit vlastní název domény](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-manage#delete-a-custom-domain-name).

---

## <a name="september-2018"></a>Září 2018
 
### <a name="updated-administrator-role-permissions-for-dynamic-groups"></a>Aktualizovala se oprávnění role správce pro dynamické skupiny

**Typ:** Pevné  
**Kategorie služby:** Správa skupin  
**Funkce produktu:** Spolupráce

Vyřešili jsme problém, konkrétní správce rolí teď můžete vytvářet a aktualizovat pravidla dynamického členství, aniž byste museli být vlastníkem skupiny.

Role jsou:

- Globální správce nebo zapisovač společnosti

- Správce služby Intune

- Správce uživatelských účtů

Další informace najdete v tématu [vytvoření dynamické skupiny a zkontrolovat stav](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule)

---

### <a name="simplified-single-sign-on-sso-configuration-settings-for-some-third-party-apps"></a>Zjednodušená nastavení konfigurace jednotného přihlašování pro některé aplikace třetích stran

**Typ:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Funkce produktu:** Jednotné přihlašování

Uvědomujeme si, že nastavení si jednotné přihlašování (SSO) k softwaru jako služby (SaaS) aplikací může být náročné vzhledem k jedinečné povaze konfiguraci jednotlivých aplikací. Sestavili jsme zjednodušené konfigurační prostředí k automaticky vyplní nastavení konfigurace jednotného přihlašování pro následující aplikace SaaS třetích stran:

- Zendesk

- ArcGis Online

- Jamf Pro

Chcete-li začít používat toto prostředí jedním kliknutím, přejděte **webu Azure portal** > **Konfigurace jednotného přihlašování** stránku pro aplikaci. Další informace najdete v tématu [integrace aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)

---

### <a name="azure-active-directory---where-is-your-data-located-page"></a>Azure Active Directory – Kde se ukládají vaše data? stránka

**Typ:** Nová funkce  
**Kategorie služby:** Ostatní  
**Funkce produktu:** GoLocal

Vyberte oblast vaší společnosti **Azure Active Directory – kde se nachází vaše data** stránky k zobrazení, které datové centrum Azure jsou uloženy vaše data služby Azure AD v klidovém stavu pro všechny služby Azure AD. Informace můžete filtrovat podle konkrétní služby Azure AD pro oblasti vaší společnosti.

Přístup k této funkci a další informace najdete v tématu [Azure Active Directory – kde se nachází vaše data](https://aka.ms/AADDataMap).

---

### <a name="new-deployment-plan-available-for-the-my-apps-access-panel"></a>Pro přístupový panel Moje aplikace je k dispozici nový plán nasazení

**Typ:** Nová funkce  
**Kategorie služby:** Moje aplikace  
**Funkce produktu:** Jednotné přihlašování

Podívejte se na nový plán nasazení, který je k dispozici pro Moje aplikace přístupového panelu (https://aka.ms/deploymentplans).
Moje aplikace přístupový panel poskytuje uživatelům na jednom místě vyhledat a získat přístup k jejich aplikacím. Tento portál poskytuje také uživatelům samoobslužné služby příležitosti, jako je žádost o přístup k aplikacím a skupinám nebo správy přístupu k těmto prostředkům jménem jiných uživatelů.

Další informace najdete v tématu [novinky na portálu Moje aplikace?](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction)

---

### <a name="new-troubleshooting-and-support-tab-on-the-sign-ins-logs-page-of-the-azure-portal"></a>Nová karta Řešení potíží a podpora na stránce Protokoly přihlášení na portálu Azure Portal

**Typ:** Nová funkce  
**Kategorie služby:** Vytváření sestav  
**Funkce produktu:** Monitorování a sestavy

Nové **řešení potíží a podpora** kartě **přihlášení** stránky Azure portal, má pomoci správce a pracovníky technické podpory řešit problémy spojené s přihlášení Azure AD. Tato nová karta poskytuje kód chyby, chybová zpráva a doporučeními k nápravě (pokud existuje) vám pomohou při řešení problému. Pokud nemůžete vyřešit problém, jsme také získáte nový způsob, jak vytvořit lístek podpory pomocí **kopírování do schránky** prostředí, která naplní **ID žádosti** a **Datum(UTC)** pole pro soubor protokolu v vašeho lístku podpory.  

![Protokoly Přihlaste se na nové kartě](media/whats-new/troubleshooting-and-support.png)

---

### <a name="enhanced-support-for-custom-extension-properties-used-to-create-dynamic-membership-rules"></a>Vylepšená podpora vlastních vlastností rozšíření, které se používají k vytvoření pravidel dynamického členství

**Typ:** Změněná funkce  
**Kategorie služby:** Správa skupin  
**Funkce produktu:** Spolupráce

S touto aktualizací můžete kliknout **získat vlastnosti rozšíření vlastních** propojení z Tvůrce dynamický uživatel pravidlo skupiny, zadejte svoje ID a jedinečných aplikací a zobrazit úplný seznam vlastností vlastního rozšíření má použít při vytváření dynamické pravidla členství pro uživatele. Tento seznam můžete také aktualizovat, k získání nových vlastností vlastního rozšíření pro tuto aplikaci.

Další informace o použití rozšíření vlastních vlastností pro pravidla dynamického členství, naleznete v tématu [rozšíření vlastností a vlastností vlastního rozšíření](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership#extension-properties-and-custom-extension-properties)

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nové schválené klientské aplikace pro podmíněný přístup na základě aplikace Azure AD

**Typ:** Plánování změn  
**Kategorie služby:** Podmíněný přístup  
**Funkce produktu:** Identita zabezpečení a ochrany

Tyto aplikace jsou na seznamu [schválené klientské aplikace](https://docs.microsoft.com/azure/active-directory/conditional-access/technical-reference#approved-client-app-requirement):

- Microsoft To-Do

- Microsoft Stream

Další informace naleznete v tématu:

- [Na základě aplikace podmíněný přístup Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="new-support-for-self-service-password-reset-from-the-windows-7881-lock-screen"></a>Nová podpora samoobslužného resetování hesla ze zamykací obrazovky ve Windows 7/8/8.1

**Typ:** Nová funkce  
**Kategorie služby:** SSPR  
**Funkce produktu:** Ověřování uživatelů

Po nastavení této nové funkce, se uživatelům zobrazí odkaz na resetování hesla z **Zámek** obrazovky zařízení se systémem Windows 7, Windows 8 nebo Windows 8.1. Když kliknete na tento odkaz, může uživatel v průvodci pomocí stejného procesu resetování hesla pomocí webového prohlížeče.

Další informace najdete v tématu [povolení heslo resetovat z Windows 7, 8 a 8.1](https://aka.ms/ssprforwindows78)

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Oznámení o změně: Autorizační kódy se již nebude k dispozici pro opakované použití 

**Typ:** Plánování změn  
**Kategorie služby:** Ověřování (přihlášení)  
**Funkce produktu:** Ověřování uživatelů

Od 15. listopadu 2018, Azure AD, přestane přijímat předchozích ověřovací kódy pro aplikace. Tato změna zabezpečení pomáhá vám Azure AD podle specifikace OAuth a vynutí u koncových bodů v1 i v2.

Pokud vaše aplikace opakovaně používá autorizační kódy k získání tokenů pro několik prostředků, doporučujeme použít kód pro získání tokenu obnovení a pak pomocí tohoto tokenu obnovení získat další tokeny pro jiné prostředky. Autorizační kódy lze použít pouze jednou, ale obnovovací tokeny lze použít více než jednou v několika prostředcích. Aplikace, která se pokusí znovu použít ověřovacího kódu během toku OAuth kódu se zobrazí chyba invalid_grant.

Tato a další související s protokoly změny najdete v tématu [úplný seznam co je nového v ověřování](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2018"></a>V galerii aplikací Azure AD jsou k dispozici nové federované aplikace – září 2018

**Typ:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Funkce produktu:** Integrace třetích stran
 
V září 2018 jsme přidali podporu těchto 16 nové aplikace s federací do Galerie aplikací:

[Uberflip](https://docs.microsoft.com/azure/active-directory/saas-apps/uberflip-tutorial), [Comeet náboru softwaru](https://docs.microsoft.com/azure/active-directory/saas-apps/comeetrecruitingsoftware-tutorial), [Workteam](https://docs.microsoft.com/azure/active-directory/saas-apps/workteam-tutorial), [ArcGIS Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/arcgisenterprise-tutorial), [Nuclino](https://docs.microsoft.com/azure/active-directory/saas-apps/nuclino-tutorial), [ JDA Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/jdacloud-tutorial), [Snowflake](https://docs.microsoft.com/azure/active-directory/saas-apps/snowflake-tutorial), NavigoCloud, [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-tutorial), join.me, [ZephyrSSO](https://docs.microsoft.com/azure/active-directory/saas-apps/zephyrsso-tutorial), [Silverback](https://docs.microsoft.com/azure/active-directory/saas-apps/silverback-tutorial), Riverbed Xirrus EasyPass, [jednotného přihlašování Rackspace](https://docs.microsoft.com/azure/active-directory/saas-apps/rackspacesso-tutorial), Enlyft jednotné přihlašování pro Azure, SurveyMonkey [Convene](https://docs.microsoft.com/azure/active-directory/saas-apps/convene-tutorial), [dmarcian](https://docs.microsoft.com/azure/active-directory/saas-apps/dmarcian-tutorial)

Další informace o aplikacích najdete v tématu [integrace aplikací SaaS pomocí Azure Active Directory](https://aka.ms/appstutorial). Další informace o zobrazení aplikace v galerii aplikací Azure AD najdete v tématu [uvedení aplikace v galerii aplikací Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="support-for-additional-claims-transformations-methods"></a>Podporují se další metody transformace deklarací identity

**Typ:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Funkce produktu:** Jednotné přihlašování

Zavedli jsme nové metody transformace deklarací identity, ToLower() byly a ToUpper(), který je možné použít na tokeny SAML ze SAML podle **Konfigurace jednotného přihlašování** stránky.

Další informace najdete v tématu [přizpůsobení deklarací identity vystavených v tokenu SAML pro podnikové aplikace ve službě Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)

---

### <a name="updated-saml-based-app-configuration-ui-preview"></a>Aktualizovalo se uživatelské rozhraní konfigurace aplikací založených na SAML (Preview)

**Typ:** Změněná funkce  
**Kategorie služby:** Podnikové aplikace  
**Funkce produktu:** Jednotné přihlašování

Jako součást naší uživatelské rozhraní pro konfiguraci aktualizovaná aplikace založené na SAML získáte:

- Názorný postup aktualizované prostředí pro konfiguraci vaší aplikace založené na SAML.

- Větší přehled o tom, co je chybějící nebo nesprávné ve vaší konfiguraci.

- Možnost přidat více e-mailové adresy pro oznámení o vypršení platnosti certifikátu.

- Nové metody transformace deklarací identity, ToLower() byly a ToUpper() a další.

- Způsob, jak nahrát vlastní token podpisový certifikát pro podnikové aplikace.

- Způsob, jak nastavit formátu NameID pro aplikace SAML a způsob, jak nastavit hodnotu NameID jako rozšíření adresáře.

K zapnutí nastavení v této aktualizaci zobrazení, klikněte na tlačítko **vyzkoušet naše nové rozhraní** odkaz z horní části **Single Sign-On** stránky. Další informace najdete v tématu [kurzu: Konfigurace založené na SAML jednotného přihlašování pro aplikaci pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-single-sign-on-portal).

---

## <a name="august-2018"></a>Srpen 2018

### <a name="changes-to-azure-active-directory-ip-address-ranges"></a>Změnily se rozsahy IP adres služby Azure Active Directory

**Typ:** Plánování změn  
**Kategorie služby:** Ostatní  
**Funkce produktu:** Platforma

Zavádíme větší rozsahy IP adres do služby Azure AD, což znamená, že pokud jste nakonfigurovali rozsahy adres IP adres Azure AD pro brány firewall, směrovače nebo skupin zabezpečení sítě, musíte je aktualizovat. Tato aktualizace nyní proto nebudete muset změnit vaše brána firewall, směrovače nebo konfigurace rozsah IP skupiny zabezpečení sítě znovu, když přidá nové koncové body služby Azure AD. 

Síťový provoz se přesouvá na tyto nové oblasti v následujících dvou měsících. Pokud chcete pokračovat bez přerušení služby, je nutné přidat tyto aktualizované hodnoty na IP adresy před 10. září 2018:

- 20.190.128.0/18 

- 40.126.0.0/18 

Důrazně doporučujeme není odebírání staré rozsahy IP adres, dokud všechny přenosy v síti se přesunula do nové oblasti. Aktualizace, o přesun a o tom, kdy můžete odebrat starý rozsahy, naleznete v tématu [Office 365 – adresy URL a rozsahy IP adres](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Oznámení o změně: Autorizační kódy se již nebude k dispozici pro opakované použití 

**Typ:** Plánování změn  
**Kategorie služby:** Ověřování (přihlášení)  
**Funkce produktu:** Ověřování uživatelů

Od 15. listopadu 2018, Azure AD, přestane přijímat předchozích ověřovací kódy pro aplikace. Tato změna zabezpečení pomáhá vám Azure AD podle specifikace OAuth a vynutí u koncových bodů v1 i v2.

Pokud vaše aplikace opakovaně používá autorizační kódy k získání tokenů pro několik prostředků, doporučujeme použít kód pro získání tokenu obnovení a pak pomocí tohoto tokenu obnovení získat další tokeny pro jiné prostředky. Autorizační kódy lze použít pouze jednou, ale obnovovací tokeny lze použít více než jednou v několika prostředcích. Aplikace, která se pokusí znovu použít ověřovacího kódu během toku OAuth kódu se zobrazí chyba invalid_grant.

Tato a další související s protokoly změny najdete v tématu [úplný seznam co je nového v ověřování](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes).
 
---

### <a name="converged-security-info-management-for-self-service-password-sspr-and-multi-factor-authentication-mfa"></a>Sloučená správa bezpečnostních údajů pro samoobslužné resetování hesla (SSPR) a Multi-Factor Authentication (MFA)

**Typ:** Nová funkce  
**Kategorie služby:** SSPR  
**Funkce produktu:** Ověřování uživatelů
 
Tato nová funkce umožňuje uživatelům spravovat svoje bezpečnostní údaje (například telefonní číslo, mobilní aplikace a tak dále) pro samoobslužné resetování HESLA a vícefaktorové ověřování v jednom umístění a prostředí; ve srovnání s dříve, ve kterém bylo provedeno jako ve dvou různých umístěních.

Tato Sblížená funkce také funguje pro uživatele, kteří používají samoobslužné resetování HESLA nebo vícefaktorové ověřování. Navíc pokud vaše organizace nemá vynucení registrace MFA nebo samoobslužné resetování HESLA, osoby můžete zaregistrovat i jakékoli MFA nebo samoobslužné resetování HESLA metody informace o zabezpečení povolené ve vaší organizaci na portálu Moje aplikace.

Toto je vyjádření souhlasu se ve verzi public preview. Správci můžou zapnout nové prostředí (v případě potřeby) pro vybranou skupinu nebo pro všechny uživatele v tenantovi. Další informace o sblížené prostředí, najdete v článku [sblíženého prostředí blogu](https://cloudblogs.microsoft.com/enterprisemobility/2018/08/06/mfa-and-sspr-updates-now-in-public-preview/)

---

### <a name="new-http-only-cookies-setting-in-azure-ad-application-proxy-apps"></a>Nové nastavení souborů cookie HTTP-Only v aplikacích Proxy aplikací služby Azure AD

**Typ:** Nová funkce  
**Kategorie služby:** Proxy aplikace  
**Funkce produktu:** Řízení přístupu

Existuje nová nastavení volané, **soubory cookie Mitigating** ve vašich aplikacích Proxy aplikací. Toto nastavení zajišťuje dodatečné zabezpečení včetně příznak HTTPOnly v hlavičce odpovědi protokolu HTTP pro obě aplikace Proxy přístupu a relace soubory cookie, zastavením přístup k souboru cookie ze skriptu na straně klienta a další brání akcím, jako je kopírování nebo Úprava souboru cookie. I když tento příznak nebyla použita dříve, soubory cookie byly vždy šifrovaný a přenáší prostřednictvím připojení SSL k ochraně proti nesprávné změny.

Toto nastavení není kompatibilní s aplikacemi využívajícími ovládací prvky ActiveX, jako je vzdálené plochy. Pokud jste v této situaci, doporučujeme vám, vypněte toto nastavení.

Další informace o nastavení souborů cookie Mitigating najdete v tématu [publikování aplikací pomocí Proxy aplikací Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-publish-azure-portal).

---

### <a name="privileged-identity-management-pim-for-azure-resources-supports-management-group-resource-types"></a>Privileged Identity Management (PIM) pro prostředky Azure podporuje typy prostředků Skupina pro správu

**Typ:** Nová funkce  
**Kategorie služby:** Privileged Identity Management  
**Funkce produktu:** Privileged Identity Management
 
Aktivace a přiřazení nastavení Just-In-Time se teď může používat pro skupinu pro správu typy prostředků, stejně jako již máte pro předplatná, skupiny prostředků a prostředky (jako jsou virtuální počítače, aplikační služby a další). Každý, kdo má roli, která poskytuje přístup správce pro skupinu pro správu kromě toho zjišťovat a spravovat daný prostředek v PIM.

Další informace o PIM a prostředků Azure najdete v tématu [zjistit a spravovat prostředky Azure s použitím Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-resource-roles-discover-resources)
 
---

### <a name="application-access-preview-provides-faster-access-to-the-azure-ad-portal"></a>Přístup k aplikacím (Preview) nabízí rychlejší přístup k portálu služby Azure AD

**Typ:** Nová funkce  
**Kategorie služby:** Privileged Identity Management  
**Funkce produktu:** Privileged Identity Management
 
V současné době při aktivaci role pomocí PIM, může trvat více než 10 minut, než se oprávnění, než se projeví. Pokud se rozhodnete použít přístup k aplikaci, která je aktuálně ve verzi public preview, správci mohou přistupovat k portálu Azure AD, jakmile se dokončí požadavek na aktivaci.

Přístup k aplikacím v současné době pouze podporuje prostředí portálu Azure AD a prostředků Azure. Další informace o přístupu k PIM a aplikace najdete v tématu [co je Azure AD Privileged Identity Management?](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure)
 
---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2018"></a>V galerii aplikací Azure AD jsou k dispozici nové federované aplikace – srpen 2018

**Typ:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Funkce produktu:** Integrace třetích stran
 
V srpna 2018 jsme přidali podporu těchto 16 nové aplikace s federací do Galerie aplikací:

[Hornbill](https://docs.microsoft.com/azure/active-directory/saas-apps/hornbill-tutorial), [Bridgeline nevázaných](https://docs.microsoft.com/azure/active-directory/saas-apps/bridgelineunbound-tutorial), [Sauce Labs – mobilní a webové testování](https://docs.microsoft.com/azure/active-directory/saas-apps/saucelabs-mobileandwebtesting-tutorial), [Meta sítě konektor](https://docs.microsoft.com/azure/active-directory/saas-apps/metanetworksconnector-tutorial), [způsobů](https://docs.microsoft.com/azure/active-directory/saas-apps/waywedo-tutorial), [Spotinst](https://docs.microsoft.com/azure/active-directory/saas-apps/spotinst-tutorial), [ProMaster (podle Inlogik)](https://docs.microsoft.com/azure/active-directory/saas-apps/promaster-tutorial), SchoolBooking, [4me](https://docs.microsoft.com/azure/active-directory/saas-apps/4me-tutorial), [dokumentace](https://docs.microsoft.com/azure/active-directory/saas-apps/DOSSIER-tutorial), [N2F - výdajů sestavy](https://docs.microsoft.com/azure/active-directory/saas-apps/n2f-expensereports-tutorial), [živém chatu Comm100](https://docs.microsoft.com/azure/active-directory/saas-apps/comm100livechat-tutorial), [SafeConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/safeconnect-tutorial), [ZenQMS](https://docs.microsoft.com/azure/active-directory/saas-apps/zenqms-tutorial), [eLuminate](https://docs.microsoft.com/azure/active-directory/saas-apps/eluminate-tutorial), [ Dovetale](https://docs.microsoft.com/azure/active-directory/saas-apps/dovetale-tutorial).

Další informace o aplikacích najdete v tématu [integrace aplikací SaaS pomocí Azure Active Directory](https://aka.ms/appstutorial). Další informace o zobrazení aplikace v galerii aplikací Azure AD najdete v tématu [uvedení aplikace v galerii aplikací Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="native-tableau-support-is-now-available-in-azure-ad-application-proxy"></a>V Proxy aplikací služby Azure AD je teď k dispozici nativní podpora Tableau

**Typ:** Změněná funkce  
**Kategorie služby:** Proxy aplikace  
**Funkce produktu:** Řízení přístupu

S naší aktualizaci z OpenID Connect protokolu udělení kódu OAuth 2.0 pro naše předběžné ověřovací protokol máte již provádět žádnou další konfiguraci Tableau pomocí Proxy aplikací. Tato změna protokol vám také pomůže Proxy aplikací, lepší podpora více moderních aplikací s použitím pouze přesměrování protokolu HTTP, které jsou běžně podporovány značky jazyka JavaScript a HTML.

Další informace o našich nativní podporu pro Tableau, naleznete v tématu [Proxy aplikací Azure AD teď s nativní podporou Tableau](https://blogs.technet.microsoft.com/applicationproxyblog/2018/08/14/azure-ad-application-proxy-now-with-native-tableau-support).

---

### <a name="new-support-to-add-google-as-an-identity-provider-for-b2b-guest-users-in-azure-active-directory-preview"></a>Nově se podporuje přidání Googlu jako zprostředkovatele identity pro uživatele B2B typu host v Azure Active Directory (Preview)

**Typ:** Nová funkce  
**Kategorie služby:** B2B  
**Funkce produktu:** B2B/B2C

Nastavením federace se službou Google ve vaší organizaci, můžete nechat pozvaný Gmail při přihlášení uživatelů sdílené aplikacím a prostředkům pomocí svých existujících účtů Google, aniž byste museli vytvářet osobní Account Microsoft (MSA) nebo účet Azure AD.

Toto je vyjádření souhlasu se ve verzi public preview. Další informace o Google federace, naleznete v tématu [Google přidat jako zprostředkovatele identity pro uživatele typu Host B2B](https://docs.microsoft.com/azure/active-directory/b2b/google-federation).

---

## <a name="july-2018"></a>Červenec 2018

### <a name="improvements-to-azure-active-directory-email-notifications"></a>Vylepšení e-mailových oznámení služby Azure Active Directory

**Typ:** Změněná funkce  
**Kategorie služby:** Ostatní  
**Funkce produktu:** Správa životního cyklu identit
 
Azure Active Directory (Azure AD) e-mailů nyní funkce aktualizovaný návrh, jakož i změny odesílatele e-mailové adresy a zobrazované jméno odesílatele, při odeslání z následující služby:
 
- Kontroly přístupu Azure AD
- Azure AD Connect Health 
- Azure AD Identity Protection 
- Azure AD Privileged Identity Management
- Oznámení podnikových aplikací vypršení platnosti certifikátu
- Oznámení služby zřizování podnikových aplikací
 
E-mailová oznámení se odešlou z následujících e-mailové adresy a zobrazované jméno:

- E-mailová adresa: azure-noreply@microsoft.com
- Zobrazovaný název: Microsoft Azure
 
Příklad některé z nových e-mailu návrhů a další informace najdete v tématu [e-mailová oznámení v Azure AD PIM](https://go.microsoft.com/fwlink/?linkid=2005832).

---

### <a name="azure-ad-activity-logs-are-now-available-through-azure-monitor"></a>Protokoly aktivit Azure AD jsou teď k dispozici prostřednictvím Azure Monitoru

**Typ:** Nová funkce  
**Kategorie služby:** Vytváření sestav  
**Funkce produktu:** Monitorování a sestavy

Protokoly aktivit Azure AD jsou teď dostupné ve verzi public preview pro Azure Monitor (Sledování služby celou platformu Azure). Azure Monitor nabízí dlouhodobé uchování a bezproblémové integrace. kromě tato vylepšení:

- Dlouhodobé uchovávání přesměrováním souborů protokolu do účtu Azure storage.

- Bezproblémová integrace SIEM, aniž by bylo potřeba psát nebo udržovat vlastní skripty.

- Bezproblémová integrace s vlastními řešeními, analytické nástroje nebo řešení správy incidentů.

Další informace o těchto nových funkcích najdete v blogovém [protokoly aktivit Azure AD ve službě Azure Monitor Diagnostika je teď ve verzi public preview](https://cloudblogs.microsoft.com/enterprisemobility/2018/07/26/azure-ad-activity-logs-in-azure-monitor-diagnostics-now-in-public-preview/) a naši dokumentaci [protokoly aktivit Azure Active Directory v Azure Monitorování (preview)](https://docs.microsoft.com/azure/active-directory/reporting-azure-monitor-diagnostics-overview).

---

### <a name="conditional-access-information-added-to-the-azure-ad-sign-ins-report"></a>Do sestav o přihlašování k Azure AD se přidaly informace o podmíněném přístupu

**Typ:** Nová funkce  
**Kategorie služby:** Vytváření sestav  
**Funkce produktu:** Zabezpečení a ochrana identity
 
Umožňuje tato aktualizace můžete zjistit, které zásady jsou vyhodnocovány, když se uživatel přihlásí spolu s výsledky zásady. Kromě toho sestavu teď obsahuje typ klientské aplikace použitých uživatelem, abyste mohli identifikovat provozu starší verzi protokolu. Položky sestavy lze nyní také vyhledávat ID korelace, které najdete v chybové zprávě přístupných a slouží k identifikaci a řešení potíží s odpovídající žádost o přihlášení.

---

### <a name="view-legacy-authentications-through-sign-ins-activity-logs"></a>Zobrazení starších verzí ověřování prostřednictvím protokolů aktivit přihlášení

**Typ:** Nová funkce  
**Kategorie služby:** Vytváření sestav  
**Funkce produktu:** Monitorování a sestavy
 
Se zavedením **klientskou aplikaci** pole v aktivit přihlašování protokoly, zákazníci můžou teď najdete v tématu uživatelé, které používají starší verze ověřování. Zákazníci budou moct přistupovat k těmto informacím pomocí rozhraní Graph API MS přihlášení nebo prostřednictvím přihlášení protokoly aktivit v portálu Azure AD, kde můžete použít **klientskou aplikaci** ovládací prvek pro filtrování starší verze ověřování. Přečtěte si další podrobnosti naleznete v dokumentaci.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2018"></a>V galerii aplikací Azure AD jsou k dispozici nové federované aplikace – červenec 2018

**Typ:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Funkce produktu:** Integrace třetích stran
 
V červenci 2018 jsme přidali podporu těchto 16 nové aplikace s federací do Galerie aplikací:

[Inovace centra](https://docs.microsoft.com/azure/active-directory/saas-apps/innovationhub-tutorial), [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-tutorial), [některých jednotného přihlašování správce](https://docs.microsoft.com/azure/active-directory/saas-apps/certainadminsso-tutorial), pracovní PSUC [iPass SmartConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/ipasssmartconnect-tutorial), [Matic záznam dění na monitoru-O](https://docs.microsoft.com/azure/active-directory/saas-apps/screencast-tutorial) , PowerSchool Unified učebny, [Eli připojování](https://docs.microsoft.com/azure/active-directory/saas-apps/elionboarding-tutorial), [Podpora vzdáleného Bomgar](https://docs.microsoft.com/azure/active-directory/saas-apps/bomgarremotesupport-tutorial), [Nimblex](https://docs.microsoft.com/azure/active-directory/saas-apps/nimblex-tutorial), [Imagineer WebVision](https://docs.microsoft.com/azure/active-directory/saas-apps/imagineerwebvision-tutorial) , [Insight4GRC](https://docs.microsoft.com/azure/active-directory/saas-apps/insight4grc-tutorial), [SecureW2 JoinNow konektor](https://docs.microsoft.com/azure/active-directory/saas-apps/securejoinnow-tutorial), [Kanbanize](https://review.docs.microsoft.com/azure/active-directory/saas-apps/kanbanize-tutorial), [SmartLPA](https://review.docs.microsoft.com/azure/active-directory/saas-apps/smartlpa-tutorial), [základní dovednosti](https://docs.microsoft.com/azure/active-directory/saas-apps/skillsbase-tutorial)

Další informace o aplikacích najdete v tématu [integrace aplikací SaaS pomocí Azure Active Directory](https://aka.ms/appstutorial). Další informace o zobrazení aplikace v galerii aplikací Azure AD najdete v tématu [uvedení aplikace v galerii aplikací Azure Active Directory](https://aka.ms/azureadapprequest).

---
 
### <a name="new-user-provisioning-saas-app-integrations---july-2018"></a>Nové integrace aplikací SaaS pro zřizování uživatelů – červenec 2018

**Typ:** Nová funkce  
**Kategorie služby:** Zřizování aplikací  
**Funkce produktu:** Integrace třetích stran
 
Azure AD umožňuje automatizovat vytváření, údržbu a odebírání uživatelských identit v aplikacích SaaS, jako je například Dropbox, Salesforce, ServiceNow a další. Pro července 2018 jsme přidali podporu pro následující aplikace v galerii aplikací Azure AD zřizování uživatelů:

- [Cisco Spark](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-spark-provisioning-tutorial)

- [Cisco WebEx](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-webex-provisioning-tutorial)

- [Bonusly](https://docs.microsoft.com/azure/active-directory/saas-apps/bonusly-provisioning-tutorial)

Seznam všech aplikací, které podporují zřizování uživatelů ve galerii Azure AD najdete v tématu [integrace aplikací SaaS pomocí Azure Active Directory](https://aka.ms/appstutorial).

---

### <a name="connect-health-for-sync---an-easier-way-to-fix-orphaned-and-duplicate-attribute-sync-errors"></a>Connect Health for Sync – Snazší způsob, jak opravovat chyby synchronizace osamocených a duplicitních atributů

**Typ:** Nová funkce  
**Kategorie služby:** AD Connect  
**Funkce produktu:** Monitorování a sestavy
 
Azure AD Connect Health uvádí samoobslužnou nápravu můžete zvýraznit a oprava chyb synchronizace. Tato funkce odstraňování potíží s duplicitním atributem chyby synchronizace a opravy objekty, které jsou osamocené ze služby Azure AD. Tuto diagnostiku má následující výhody:

- Chyby synchronizace duplicitním atributem, poskytují konkrétní opravy může zúžit oblast

- Oprava se vztahuje vyhrazené scénáře služby Azure AD, řešení chyb v jediném kroku

- Žádný upgrade nebo konfigurace je potřeba zapnout a použít tuto funkci

Další informace najdete v tématu [diagnostikovat a opravovat chyby synchronizace duplicitním atributem](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health-diagnose-sync-errors)

---

### <a name="visual-updates-to-the-azure-ad-and-msa-sign-in-experiences"></a>Vizuální aktualizace prostředí pro přihlašování k Azure AD a MSA

**Typ:** Změněná funkce  
**Kategorie služby:** Azure AD  
**Funkce produktu:** Ověřování uživatelů

Aktualizovali jsme v uživatelském rozhraní společnosti Microsoft online services přihlašovacího prostředí, například pro Office 365 a Azure. Díky této změně obrazovky, méně zaplněnou a jednodušší. Další informace o této změně najdete v tématu [nadcházející vylepšení prostředí pro přihlášení k Azure AD](https://cloudblogs.microsoft.com/enterprisemobility/2018/04/04/upcoming-improvements-to-the-azure-ad-sign-in-experience/) blogu.

---

### <a name="new-release-of-azure-ad-connect---july-2018"></a>Nová verze služby Azure AD Connect – červenec 2018

**Typ:** Změněná funkce  
**Kategorie služby:** Zřizování aplikací  
**Funkce produktu:** Správa životního cyklu identit

Nejnovější verze služby Azure AD Connect zahrnuje: 

- Opravy chyb a aktualizací možnosti podpory 

- Obecná dostupnost integrace Federovat Ping

- Aktualizace na nejnovější verzi klienta SQL 2012 

Další informace o této aktualizaci najdete v tématu [Azure AD Connect: Historie vydaných verzí](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history)

---

### <a name="updates-to-the-terms-of-use-tou-end-user-ui"></a>Aktualizace uživatelského rozhraní podmínek použití pro koncové uživatele

**Typ:** Změněná funkce  
**Kategorie služby:** Podmínky použití  
**Funkce produktu:** Zásady správného řízení

Aktualizujeme přijetí řetězce v uživatelském rozhraní podmínek použití koncového uživatele.

**Aktuální text.** Aby bylo možné získat přístup k prostředkům [tenantName], je nutné přijmout podmínky použití.<br>**Nový text.** Aby bylo možné získat přístup k prostředku [tenantName], musí přečíst podmínky použití.

**Aktuální text:** Zvolíte-li znamená, že souhlasíte se všemi výše uvedené podmínky použití přijmout.<br>**Nový text:** Klikněte prosím na potvrzení, že budete mít přečetl(a) a rozumím jí podmínky použití přijmout.

---
 
### <a name="pass-through-authentication-supports-legacy-protocols-and-applications"></a>Předávací ověřování podporuje starší verze protokolů a aplikací

**Typ:** Změněná funkce  
**Kategorie služby:** Ověřování (přihlášení)  
**Funkce produktu:** Ověřování uživatelů
 
Předávací ověřování nyní podporuje starší protokoly a aplikace. Tato omezení jsou nyní plně podporovány:

- Přihlášení uživatelů do starší klientské aplikace Office, Office 2010 a Office 2013, bez nutnosti moderní ověřování.

- Přístup k sdílení kalendáře a volném informací v Exchangi hybridní prostředí s Office 2010 pouze.

- Uživatelská přihlášení ke Skypu pro firmy klientských aplikací bez moderního ověřování.

- Přihlašování uživatelů k prostředí PowerShell verze 1.0.

- Programu registrace zařízení Apple (Apple DEP), pomocí Průvodce nastavením iOS. 

---
 
### <a name="converged-security-info-management-for-self-service-password-reset-and-multi-factor-authentication"></a>Sloučená správa bezpečnostních údajů pro samoobslužné resetování hesla a Multi-Factor Authentication

**Typ:** Nová funkce  
**Kategorie služby:** SSPR  
**Funkce produktu:** Ověřování uživatelů

Tato nová funkce umožňuje uživatelům spravovat svoje bezpečnostní údaje (například telefonní číslo, e-mailovou adresu, mobilní aplikace a tak dále) pro samoobslužné resetování hesla (SSPR) a Vícefaktorové ověřování (MFA) v jednom prostředí. Uživatelé už muset zaregistrovat stejné bezpečnostní údaje pro samoobslužné resetování HESLA a vícefaktorové ověřování ve dvou různých prostředích. Toto nové prostředí platí také pro uživatele, kteří mají samoobslužné resetování HESLA nebo vícefaktorové ověřování.

Pokud organizace není vynucení registrace MFA nebo samoobslužné resetování HESLA, uživatelé můžou registrovat svoje bezpečnostní údaje prostřednictvím **Moje aplikace** portálu. Odtud uživatelé můžou registrovat jakékoli metody pro vícefaktorové ověřování a samoobslužné resetování HESLA povoleno. 

Toto je vyjádření souhlasu se ve verzi public preview. Správce můžete pro vybrané skupiny uživatelů nebo všechny uživatele v tenantovi zapnout na nové prostředí (v případě potřeby).

---
 
### <a name="use-the-microsoft-authenticator-app-to-verify-your-identity-when-you-reset-your-password"></a>Ověřování identity pomocí aplikace Microsoft Authenticator při resetování hesla

**Typ:** Změněná funkce  
**Kategorie služby:** SSPR  
**Funkce produktu:** Ověřování uživatelů

Tato funkce umožňuje bez oprávnění správce, potvrdit svou identitu při resetování hesla pomocí oznámení nebo kód v aplikaci Microsoft Authenticator (nebo jakoukoli ověřovací aplikaci). Po zapnutí admins tento samoobslužné resetování hesla – metoda, uživatelé, kteří se zaregistrovali prostřednictvím aka.ms/mfasetup nebo aka.ms/setupsecurityinfo mobilní aplikace můžete použít své mobilní aplikace jako metodu ověřování při resetování hesla.

Oznámení mobilní aplikace je možné zapnout jenom na jako součást zásady, které vyžaduje dvě metody k resetování hesla.

---
