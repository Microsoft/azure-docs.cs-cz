---
title: Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? | Dokumenty Microsoft
description: Povolit jednotné přihlašování ke všem SaaS a webových aplikací, které potřebujete pro firmy pomocí Azure Active Directory.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: users-groups-roles
ms.workload: identity
ms.topic: article
ms.date: 07/16/2018
ms.author: barbkess
ms.reviewer: asmalser
ms.custom: it-pro
ms.openlocfilehash: b7ea8a755f1f23c76bb0f7d8120970bb7795e95b
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/17/2018
ms.locfileid: "39091829"
---
# <a name="what-is-application-access-and-single-sign-on-with-azure-active-directory"></a>Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?
Jednotné přihlašování znamená možnost přistupovat ke všem aplikacím a prostředkům, které je potřeba udělat firmy, tak, že stačí, když pomocí jediného uživatelského účtu. Jakmile se přihlásíte, dostanete všechny aplikace, které potřebujete, aniž byste museli znovu ověřovat (třeba zadávat heslo) podruhé.

Řada organizací používá software jako služba (SaaS) aplikací jako je Office 365, Box nebo Salesforce pro produktivity koncových uživatelů. V minulosti IT pracovníci musí jednotlivě vytvářet a aktualizovat uživatelské účty v každé aplikaci SaaS, a uživatelé musí pamatovat heslo pro každou aplikaci SaaS.

Azure Active Directory rozšiřuje místní služby Active Directory do cloudu, uživatelům umožníte používat jejich primární účet organizace nejen přihlásit do jejich zařízení připojených k doméně a firemním prostředkům, ale také z webu a aplikace SaaS potřebná pro své práce.

Proto pouze uživatele není potřeba spravovat víc kopií v úložišti uživatelských jmen a hesel, žádostí o přístup můžete automaticky zřízení nebo zrušení zřízení podle jejich členové skupiny organizace a také jejich stav jako zaměstnanec. Azure Active Directory přináší zabezpečení a zásad správného řízení řízení přístupu, které umožňují centrálně spravovat přístup uživatelů v aplikacích SaaS.

Azure AD umožňuje snadnou integraci se spoustou dnešních oblíbených aplikací SaaS poskytuje správu identit a přístupu a umožňuje uživatelům jednotné přihlašování k aplikacím přímo, nebo Hledat a spouštět z různých portálů, třeba Office 365 nebo přístupového panelu Azure AD.

Architektura integrace se skládá z následujících čtyř hlavních stavebních bloků:

* Jednotné přihlašování umožňuje uživatelům přistupovat k aplikacím SaaS založené na jejich účet organizace ve službě Azure AD. Jednotné přihlašování je co umožňuje uživatelům ověřovat aplikace pomocí jejich jeden účet organizace.
* Zřizování uživatelů umožňuje zřizování uživatelů a zrušení zřizování do cílové SaaS v závislosti na změny provedené v systému Windows Server Active Directory nebo Azure AD. Zřízeného účtu je, co umožňuje uživatelům povoluje použití aplikace, poté, co jste se ověřili pomocí jednotného přihlašování.
* Správa přístupu centralizované aplikací v Azure portal umožňuje přístup k aplikacím SaaS jednoho bodu a správy s možností delegování rozhodování a přístup k aplikaci a jejich schvalování, všem uživatelům v organizaci
* Sjednocené vytváření sestav a monitorování aktivity uživatelů ve službě Azure AD

## <a name="how-does-single-sign-on-with-azure-active-directory-work"></a>Jak funguje jednotné přihlašování pomocí služby Azure Active Directory?
Při přihlášení k aplikaci, které procházejí ověřovacího procesu. jsou-li prokázat, že jsou který říká, že jsou. Bez jednotného přihlašování tento proces ověřování se obvykle provádí tak, že zadáte heslo, které jsou uloženy na úrovni aplikace a uživatelé musí znát heslo.

Azure AD podporuje tři různé způsoby, přihlaste se k aplikacím:

* **Federované jednotné přihlašování** umožňuje aplikacím pro přesměrování do služby Azure AD pro ověřování uživatelů místo vás vyzve k zadání vlastní heslo. Federované jednotné přihlašování se podporuje pro aplikace, které podporují protokoly, například SAML 2.0, WS-Federation nebo OpenID Connect a bohaté možnosti režim jednotného přihlašování.
* **Založené na heslech jednotného přihlašování** umožňuje zabezpečené uložení hesel aplikace a opakované přehrání pomocí rozšíření webového prohlížeče nebo mobilní aplikace. Založené na heslech jednotného přihlašování využívá existující proces poskytovaný aplikací, ale umožňuje správcům spravovat hesla a nevyžaduje, aby uživatel heslo znal.
* **Existující jednotné přihlašování** Azure AD a využít všechny existující jednotné přihlašování, který už dřív představovala nastavit pro aplikace, ale umožňuje tyto aplikace propojit s portály panel přístupu k Office 365 nebo Azure AD a také umožňuje další vytváření sestav v Azure AD, když existuje spuštění aplikace.

Jakmile uživatel byl ověřen s aplikací, potřebují také mít záznam účtu zřízené na úrovni aplikace, které řekne aplikaci, ve kterém jsou oprávnění a úroveň přístupu uvnitř aplikace. Zřizování tento záznam účtu můžete buď automaticky provedou, nebo to může nastat, ručně správcem před uživateli se nabídnou přístup pomocí jednotného přihlašování.

 Další informace o těchto režimech jednotné přihlašování a zřizování níže.

### <a name="federated-single-sign-on"></a>Federované jednotné přihlašování
Federované jednotné přihlašování umožňuje uživatelům ve vaší organizaci být automaticky přihlášeni k aplikaci SaaS třetích stran pomocí Azure AD využívajícího informace o uživatelském účtu z Azure AD.

V tomto scénáři když již bylo přihlášení do služby Azure AD a chcete získat přístup k prostředkům, které jsou řízené aplikaci SaaS třetí strany, federace eliminuje potřebu uživatele ověřit.

Azure AD může podporovat federované jednotné přihlašování s aplikacemi, které podporují SAML 2.0, WS-Federation nebo OpenID connect protokoly.

Viz také: [Správa certifikátů pro federované jednotné přihlašování](manage-certificates-for-federated-single-sign-on.md)

### <a name="password-based-single-sign-on"></a>Jednotné přihlašování pomocí hesla
Konfigurace založené na heslech jednotné přihlašování umožňuje uživatelům ve vaší organizaci být automaticky přihlášeni k aplikaci SaaS třetích stran pomocí Azure AD využívajícího informace o uživatelském účtu v aplikaci SaaS třetí strany. Když povolíte tuto funkci, Azure AD shromažďuje a bezpečně uloží informace o uživatelském účtu a související heslo.

Azure AD může podporovat založené na heslech jednotného přihlašování pro všechny cloudové aplikace, která má založeného na HTML přihlašovací stránky. Pomocí modulu plug-in vlastním prohlížeči AAD automatizuje proces přihlašování prostřednictvím bezpečně načítání aplikace přihlašovacích údajů, jako je například uživatelské jméno a heslo z adresáře a vloží tyto přihlašovací údaje na přihlašovací stránce aplikace jménem uživatele. Existují dva případy:

1. **Spravuje správce přihlašovacích údajů** – správci mohou vytvořit a spravovat přihlašovací údaje aplikace a přiřadit tyto přihlašovací údaje uživatele nebo skupiny, kteří potřebují přístup k aplikaci. V těchto případech koncového uživatele není potřeba znát přihlašovací údaje, ale stále získává přístup jednotné přihlašování k aplikaci jednoduše tak, že po kliknutí na jejich přístupovém panelu nebo přes zadané propojení. Tento proces povolí obě, správu životního cyklu přihlašovacích údajů správce, jakož i pohodlí pro koncové uživatele, kterým není nutné si pamatovat nebo Správa hesel konkrétní aplikace. Přihlašovací údaje jsou zakódovány od koncového uživatele při automatizovaného procesu přihlašování; jsou ale technicky zjistitelné tímto uživatelem, pomocí nástroje pro ladění webové a uživatelům a správcům postupujte podle stejné zásady pro zabezpečení jako v případě, že přihlašovací údaje prezentovaly přímo uživatelem. Pokud správce přihlašovacích údajů jsou užitečné při poskytování přístupu k účtu, který je sdílen mezi mnoha uživateli, jako jsou sociální média nebo dokument aplikace pro sdílení obsahu.
2. **Spravuje přihlašovací údaje** – správci mohou přiřadit aplikace pro koncové uživatele nebo skupiny a povolit koncovým uživatelům zadat svoje vlastní přihlašovací údaje přímo při přístupu k aplikaci poprvé na jejich přístupovém panelu. Tím se vytvoří v zájmu usnadnění pro koncové uživatele, kterým se nemusí průběžně zadání hesel konkrétní aplikace pokaždé, když přistupují k aplikaci. Uživatelé můžou dál spravovat hesla aktualizace ani odstranění je podle potřeby. Tento případ použití také slouží jako odrazový můstek do správy pověření, kterým správce může nastavit nové přihlašovací údaje pro aplikaci v budoucnu beze změny prostředí aplikací přístup koncového uživatele.

V obou případech se přihlašovací údaje jsou uložené v šifrovaném stavu v adresáři a jsou předány pouze přes protokol HTTPS během procesu automatické přihlašování. Použití založené na heslech jednotného přihlašování Azure AD nabízí pohodlný přístup k řešení pro správu identit aplikace, které nejsou schopný zajistit podporu federace protokoly.

Jednotné přihlašování založené na heslech spoléhá na rozšíření prohlížeče, bezpečně načítat aplikace a informace specifické pro uživatele ze služby Azure AD a použít ji ke službě. Většina aplikací SaaS třetích stran, které jsou podporovány službou Azure AD podporují tuto funkci.

Pro jednotné přihlašování založené na heslech může být koncového uživatele prohlížeče:
* Internet Explorer 11 – ve Windows 7 nebo novější
* Edge ve Windows 10 Anniversary Edition nebo novější 
* Chrome – Ve Windows 7 nebo novější a v systému MacOS X nebo novější
* Firefox 26.0 nebo později – na Windows XP SP2 nebo novější a v systému Mac OS X 10,6 nebo novější

### <a name="existing-single-sign-on"></a>Existující jednotné přihlašování
Při konfiguraci jednotného přihlašování pro aplikaci, na webu Azure portal nabízí třetí možnost z "existující Single Sign-On". Tato možnost jednoduše umožňuje správcům vytvořit odkaz na aplikaci a umístěte ho na panel přístupu pro vybraného uživatele.

Například pokud je aplikace, která je nakonfigurovaná k ověřování uživatelů pomocí služby Active Directory Federation Services 2.0, správce použít možnost "existující Single Sign-On" pro vytvoření odkazu na něj na přístupovém panelu. Když uživatelé přejdou na odkaz, musí ověřit pomocí Active Directory Federation Services 2.0 nebo jakýkoli existující jednotné přihlašování řešení poskytuje aplikace.

### <a name="user-provisioning"></a>Zřizování uživatelů
Pro výběr aplikace Azure AD umožňuje zřizování automatizované uživatelských účtů v aplikacích SaaS třetí strany z webu Azure portal a jeho rušení pomocí údajů systému Windows Server Active Directory nebo Azure AD identity. Pokud má uživatel oprávnění ve službě Azure AD pro jednu z těchto aplikací, účet můžete automaticky se vytvoří v cílové aplikaci SaaS (zřízená).

Při odstranění uživatele nebo informace o jejich změny ve službě Azure AD, tyto změny se projeví také v aplikaci SaaS. To znamená, že správa životního cyklu identit automatické konfigurace umožňuje správcům řídit a poskytnout automatické zřizování a zrušení zřizování aplikací SaaS. Ve službě Azure AD je tato automatizace správy životního cyklu identit zajišťuje zřizování uživatelů.

Další informace najdete v tématu [automatické zřizování uživatelů a jeho rušení pro aplikace SaaS](../active-directory-saas-app-provisioning.md)

## <a name="get-started-with-the-azure-ad-application-gallery"></a>Začínáme s Galerie aplikací Azure AD
Chcete začít? Nasadit jednotné přihlašování mezi Azure AD a aplikace SaaS, které vaše organizace používá, postupujte podle následujících pokynů.

### <a name="using-the-azure-ad-application-gallery"></a>Pomocí Galerie aplikací Azure AD
[Galerie aplikací Azure Active Directory](https://azure.microsoft.com/marketplace/active-directory/all/) poskytuje seznam aplikací, které jsou známé podporovat určitou formu jednotné přihlašování s Azure Active Directory.

![Galerie aplikací Azure online](media/what-is-single-sign-on/onlineappgallery.png)

Zde jsou některé tipy pro hledání ve funkcích, které podporují aplikace:

* Azure AD podporuje automatické zřizování a jeho rušení pro všemi aplikacemi "Doporučené" v [Galerie aplikací Azure Active Directory](https://azure.microsoft.com/marketplace/active-directory/all/).
* Seznam federovaným aplikacím, které specificky podporují federované jednotné přihlašování pomocí protokolu, jako je SAML, WS-Federation nebo OpenID Connect najdete [tady](http://social.technet.microsoft.com/wiki/contents/articles/20235.azure-active-directory-application-gallery-federated-saas-apps.aspx).

Jakmile jste nalezené vaší aplikace, můžete začít podle podrobných pokynů v galerii aplikací a na webu Azure Portal můžete povolit jednotné přihlašování.

### <a name="application-not-in-the-gallery"></a>Aplikace není v galerii?
Pokud vaše aplikace nebyl nalezen v galerii aplikací Azure AD, máte tyto možnosti:

* **Přidání neuvedené aplikace používáte** – vlastní kategorii v galerii aplikací na webu Azure portal slouží k připojení neuvedené aplikace, která vaše organizace používá. Můžete přidat libovolné aplikace, který podporuje SAML 2.0 jako federované aplikace nebo aplikací, která založeného na HTML přihlašovací stránky jako heslo jednotného přihlašování k aplikaci. Další podrobnosti najdete v tomto článku [přidání vlastní aplikace](../application-config-sso-how-to-configure-federated-sso-non-gallery.md).
* **Přidat vlastní aplikaci, kterou vyvíjíte** – Pokud jste vyvinuli aplikaci sami, postupujte podle pokynů v dokumentaci pro vývojáře Azure AD k provedení federovaného jednotného přihlašování nebo zřizování s využitím Azure AD graph API. Další informace naleznete v následujících zdrojích:
  
  * [Scénáře ověřování pro Azure AD](../active-directory-authentication-scenarios.md)
  * [https://github.com/AzureADSamples/WebApp-MultiTenant-OpenIdConnect-DotNet](https://github.com/AzureADSamples/WebApp-MultiTenant-OpenIdConnect-DotNet)
  * [https://github.com/AzureADSamples/WebApp-WebAPI-MultiTenant-OpenIdConnect-DotNet](https://github.com/AzureADSamples/WebApp-WebAPI-MultiTenant-OpenIdConnect-DotNet)
  * [https://github.com/AzureADSamples/NativeClient-WebAPI-MultiTenant-WindowsStore](https://github.com/AzureADSamples/NativeClient-WebAPI-MultiTenant-WindowsStore)
* **Požádat o integraci aplikace** -požádat o podporu pro aplikaci pomocí [fóru názorů na Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory/).

### <a name="using-the-azure-portal"></a>Použití webu Azure Portal
Rozšíření služby Active Directory na webu Azure Portal můžete nakonfigurovat použití jednotného přihlašování. Jako první krok budete muset z oddílu služby Active Directory na portálu vyberte adresář:

![](./media/what-is-single-sign-on/azuremgmtportal.png)

Správa aplikací SaaS třetích stran, můžete přepnout do vybraného adresáře na kartě aplikace. Toto zobrazení umožňuje správcům:

* Přidat z Galerie Azure AD, jakož i aplikace, které vyvíjíte nové aplikace
* Odstranit integrované aplikace
* Správa aplikací, které jste už integrovali

Typické úlohy správy pro aplikace SaaS třetích stran jsou:

* Povolení jednotného přihlašování s heslem jednotného přihlašování, nebo pokud je k dispozici pro cíl SaaS federované jednotné přihlašování Azure AD
* Volitelně můžete povolení zřizování pro uživatele, zřizování a jeho rušení (Správa životního cyklu identit) uživatelů
* Pro aplikace 
* tam, kde je povolená zřizování uživatelů, výběru kteří uživatelé mají přístup k dané aplikaci

Pro aplikace z galerie, které podporují federované jednotné přihlašování konfigurace obvykle vyžaduje, abychom vám poskytli další nastavení konfigurace jako jsou certifikáty a metadata k vytvoření federovaného vztahu důvěryhodnosti mezi aplikací třetích stran a Azure AD. Průvodce konfigurací vás provede podrobnosti a poskytuje snadný přístup k datům specifické pro aplikace SaaS a pokyny.

Galerie aplikací, které podporují automatické zřizování uživatelů musíte poskytnout oprávnění Azure AD ke správě vašich účtů v aplikaci SaaS. Přinejmenším budete muset poskytnout přihlašovací údaje Azure AD by měla používat při ověřování přes k cílové aplikaci. Určuje, zda další nastavení konfigurace musí být zadané závisí na požadavcích aplikace.

## <a name="deploying-azure-ad-integrated-applications-to-users"></a>Nasazení aplikace Azure integrované s Active Directory uživatelům
Azure AD poskytuje několik přizpůsobitelné způsobů, jak nasadit aplikace koncovým uživatelům ve vaší organizaci:

* Azure AD přístupového panelu
* Spouštěč aplikací Office 365
* Přímé přihlášení k federovaným aplikacím
* Přímé odkazy na federované nebo existující aplikace či aplikace založené na hesle

Metody, které budete chtít nasadit ve vaší organizaci je vaše podle vlastního uvážení.

### <a name="azure-ad-access-panel"></a>Azure AD přístupového panelu
Přístupový Panel na https://myapps.microsoft.com je webový portál, který umožňuje koncovým uživatelům s účtem organizace ve službě Azure Active Directory k zobrazení a spuštění cloudových aplikací na které byly udělen přístup správce Azure AD. Pokud jste koncový uživatel s [Azure Active Directory Premium](https://azure.microsoft.com/pricing/details/active-directory/), můžete také využít možnosti správy samoobslužné skupiny přes přístupový Panel.

![Panel přístupu Azure AD](media/what-is-single-sign-on/azure-ad-access-panel.png)

Přístupový Panel je nezávislý na webu Azure Portal a nevyžaduje, aby uživatelé měli k předplatnému Azure nebo předplatné služeb Office 365.

Další informace o přístupovém panelu Azure AD, najdete v článku [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

### <a name="office-365-application-launcher"></a>Spouštěč aplikací Office 365
Organizace, které jste nasadili Office 365, aplikace přiřazené k uživatelům prostřednictvím služby Azure AD se také zobrazí na portálu Office 365 na https://portal.office.com/myapps. To umožňuje snadné a pohodlné pro uživatele v organizaci spouštění svých aplikací bez nutnosti použít portál druhý a je doporučenou aplikaci spouštění řešení pro organizace používá Office 365.

![](./media/what-is-single-sign-on/officeapphub.png)

Další informace o Spouštěče aplikací Office 365 najdete v tématu [mít vaše aplikace se zobrazí ve Spouštěči aplikací Office 365](https://msdn.microsoft.com/office/office365/howto/connect-your-app-to-o365-app-launcher).

### <a name="direct-sign-on-to-federated-apps"></a>Přímé přihlášení k federovaným aplikacím
Nejvíce federovaným aplikacím, které podporují protokol SAML 2.0, WS-Federation nebo OpenID connect také podporu možnost uživatelů ke spouštění na úrovni aplikace a pak získejte přihlášení prostřednictvím služby Azure AD automatické přesměrování nebo kliknutím na odkaz pro přihlášení. To se označuje jako poskytovatel služeb-zahájí přihlašování a nejvíce federované aplikace v galerii aplikací Azure AD podporují tento (viz dokumentace odkazované z průvodce Konfigurace jednotného přihlašování aplikace na webu Azure Portal podrobnosti).

![](./media/what-is-single-sign-on/workdaymobile.png)

### <a name="direct-sign-on-links-for-federated-password-based-or-existing-apps"></a>Přímé odkazy přihlašování pro federované, založené na heslech nebo existující aplikace
Azure AD podporuje také přímý jednotné přihlašování – odkazy na jednotlivých aplikací, které podporují založené na heslech jednotného přihlašování, existující jednotné přihlašování a jakoukoli formu federované jednotné přihlašování.

Tyto odkazy jsou specificky vytvořený adresy URL, které odesílají uživatele prostřednictvím přihlášení procesu služby Azure AD pro danou aplikaci bez nutnosti spuštění uživatele ze služby Azure AD je přístup k panelu nebo v Office 365. Tyto adresy jednotné přihlašování najdete v části řídicího panelu kartu všechny předem integrované aplikace v sekci Active Directory na webu Azure portal, jak je znázorněno v následujícím snímku obrazovky.

![](./media/what-is-single-sign-on/deeplink.png)

Tyto odkazy můžete zkopírovat a vložit kamkoli chcete poskytnout odkaz přihlášení na vybrané aplikaci. Může to být e-mailem nebo v jakékoli vlastní webový portál, který jste vytvořili pro přístup k aplikaci uživatele. Tady je příklad služby Azure AD s přímým přístupem jednotné přihlašování – adresy URL pro Twitter:

`https://myapps.microsoft.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

Podobně jako u adresy URL specifické pro organizaci na přístupovém panelu, můžete dále přizpůsobit tuto adresu URL tak, že přidáte jednu aktivní nebo ověřených domén pro svůj adresář myapps.microsoft.com doména. Tím se zajistí, že organizace branding se načetl okamžitě na přihlašovací stránku bez nutnosti nechat napřed zadejte svoje ID uživatele:

`https://myapps.microsoft.com/contosobuild.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

Když oprávněný uživatel klikne na jednu z těchto odkazů specifické pro aplikaci, se nejprve najdete v jejich organizační přihlašovací stránky (za předpokladu, že se ještě nejste přihlášeni) a po přihlášení se přesměrují do své aplikace bez zastavení se nejprve na přístupovém panelu. Pokud uživatel chybí požadavky na přístup k aplikaci, jako je například rozšíření prohlížeče jednotné přihlašování pomocí hesla, odkaz vyzve uživatele k instalaci chybějících rozšíření. Adresa URL odkazu také konstantní, pokud se jednotné přihlašování – konfigurace pro aplikaci změní.

Tyto odkazy použít stejné mechanismy řízení přístupu jako na přístupovém panelu a Office 365 a pouze uživatele nebo skupiny, kteří mají přiřazený k aplikaci na webu Azure Portal budete moct úspěšně ověřit. Každý uživatel, který nemá oprávnění se ale zobrazí se zpráva s vysvětlením, aby nebyl udělen přístup a disponují odkaz k načtení přístupového panelu zobrazíte dostupné aplikace, ke kterým mají přístup.

## <a name="related-articles"></a>Související články
* [Rejstřík článků o správě aplikací ve službě Azure Active Directory](../active-directory-apps-index.md)
* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](../saas-apps/tutorial-list.md)
* [Nastavení Cloud Discovery](/cloud-app-security/set-up-cloud-discovery)
* [Úvod ke správě přístupu k aplikacím](what-is-access-management.md)
* [Porovnání možností správy externí identity ve službě Azure AD](../active-directory-b2b-compare-b2c.md)


