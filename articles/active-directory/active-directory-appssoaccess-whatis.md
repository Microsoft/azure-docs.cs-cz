---
title: Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? | Dokumenty Microsoft
description: Můžete povolit jednotné přihlašování ke všem SaaS a webové aplikace, které potřebujete pro firmy pomocí Azure Active Directory.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: users-groups-roles
ms.workload: identity
ms.topic: article
ms.date: 09/11/2017
ms.author: barbkess
ms.reviewer: asmalser
ms.custom: it-pro
ms.openlocfilehash: 19eb9bb593b349d88d8235dc2f1eca448e7646f5
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="what-is-application-access-and-single-sign-on-with-azure-active-directory"></a>Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?
Jednotné přihlašování znamená schopnost přístup ke všem aplikacím a prostředkům, které musíte udělat podnikání, po přihlášení pouze po pomocí jediného uživatelského účtu. Jakmile se přihlásíte, dostanete všechny aplikace, bez nutnosti ověřování (zadejte například heslo) ještě jednou.

Mnoho organizací závisí software jako služba (SaaS) aplikace například Office 365, pole a Salesforce pro produktivita koncového uživatele. V minulosti IT oddělení potřebuje jednotlivě vytvářet a aktualizovat uživatelské účty v každé aplikaci SaaS a uživatelé budou muset Zapamatovat heslo pro každou aplikaci SaaS.

Azure Active Directory rozšiřuje místní služby Active Directory do cloudu, uživatelům umožníte používat jejich primární účet organizace nejen Přihlaste se k jejich zařízení připojených k doméně a prostředkům společnosti, ale také na webu a aplikace SaaS potřebná pro jejich úlohy.

Proto jenom uživatelé nemají ke správě více sad uživatelských jmen a hesel, jejich aplikace přístup může automaticky zřídit nebo zrušte zřízený podle jejich členové skupiny organizace a také jejich stav jako zaměstnanec. Azure Active Directory zavádí zabezpečení a přístup zásad správného řízení ovládacích prvků, které vám umožní centrálně spravovat přístup uživatelů v rámci aplikací SaaS.

Azure AD umožňuje snadnou integraci k mnoha dnešních oblíbených aplikací SaaS; poskytuje správu identit a přístupu a umožňuje uživatelům jednotného přihlašování k aplikacím přímo, nebo zjistit a spusťte je z portálu, jako je Office 365 nebo přístupový panel Azure AD.

Architektura integrace se skládá z následujících čtyř hlavních stavebních bloků:

* Jednotné přihlašování umožňuje uživatelům přístup k jejich SaaS aplikací založených na účet své organizace v Azure AD. Jednotné přihlašování je co umožňuje uživatelům ověření aplikace pomocí jednoho účet své organizace.
* Zřizování uživatelů umožňuje zřizování uživatelů a jeho rušení do cílové SaaS v závislosti na změny provedené v systému Windows Server Active Directory nebo Azure AD. Zřízeného účtu je co umožňuje uživatelům být autorizovaný k použití aplikaci, po mít ověření přes jednotné přihlašování.
* Správa přístupu centralizované aplikací v Azure portálu umožňuje přístup k aplikaci SaaS jednoho bodu a správy, umožňuje aplikaci přístup k rozhodování a schválení všem uživatelům v organizaci
* Sjednocené vytváření sestav a monitorování aktivity uživatelů ve službě Azure AD

## <a name="how-does-single-sign-on-with-azure-active-directory-work"></a>Jak funguje jednotné přihlašování pomocí služby Azure Active Directory?
Když uživatel "přihlásí" k aplikaci, přejde prostřednictvím procesu ověřování tam, kde jsou prokázat, že jsou kdo říká se, že jsou. Bez jednotného přihlašování to se obvykle provádí zadáním hesla, která je uložená v aplikaci a uživatele je potřeba vědět toto heslo.

Azure AD podporuje tři různé způsoby, přihlaste se k aplikacím:

* **Federované jednotné přihlašování** umožňuje aplikacím přesměrovat do služby Azure AD pro ověřování uživatelů místo výzvy pro vlastní heslo. Toto je podporováno pro aplikace, který podporu protokoly například SAML 2.0, WS-Federation nebo OpenID Connect a je nejkomplexnější režimu jednotné přihlašování.
* **Založené na heslech jednotné přihlašování** umožňuje zabezpečit ukládání hesel aplikací a přehráním pomocí mobilní aplikace nebo rozšíření webové prohlížeče. Toto využívá existující proces přihlášení poskytuje aplikace, ale umožňuje správci spravovat hesla a nevyžaduje žádný uživatel znát heslo.
* **Existující jednotné přihlašování** umožňuje využívat všechny existující jednotné přihlašování, byl nastavenou službu aplikace, ale umožňuje tyto aplikace propojení na portály Office 365 nebo Azure AD panel přístupu službě Azure AD a také umožňuje další vytváření sestav v Azure AD, pokud existuje spuštění aplikace.

Jakmile uživatel byl ověřen pomocí aplikace, se musí být také záznamu klienta, které se zřídí v aplikaci, která sděluje aplikace kde existuje oprávnění a úroveň přístupu jsou uvnitř aplikace. Zřizování tento záznam účtu může buď dojít automaticky, nebo může k ní dojít ručně správcem před uživateli se nabídnou přístup přihlášení.

 Další informace o těchto režimech jednoho přihlášení a zřizování níže.

### <a name="federated-single-sign-on"></a>Federované jednotné přihlašování
Federované jednotné přihlašování umožňuje uživatelům ve vaší organizaci být automaticky přihlášeni k aplikaci SaaS jiných výrobců službou Azure AD pomocí informací o účtu uživatele z Azure AD.

Pokud jste již byly zaprotokolovány do služby Azure AD, a chcete pro přístup k prostředkům, které jsou řízené aplikace SaaS jiných výrobců, federace v tomto scénáři se eliminuje potřeba pro uživatele, aby se k novému ověření.

Federované jednotné přihlašování s aplikacemi, které podporují 2.0 SAML, WS-Federation, může podporovat Azure AD nebo OpenID connect protokoly.

Viz také: [Správa certifikátů pro federované jednotné přihlašování](active-directory-sso-certs.md)

### <a name="password-based-single-sign-on"></a>Jednotné přihlašování pomocí hesla
Konfigurace založené na heslech jednotné přihlašování umožňuje uživatelům ve vaší organizaci být automaticky přihlášeni k aplikaci SaaS jiných výrobců službou Azure AD pomocí informací o účtu uživatele z aplikace SaaS jiných výrobců. Když povolíte tuto funkci, Azure AD shromažďuje a bezpečně ukládá informace o uživatelském účtu a související heslo.

Azure AD může podporovat založené na heslech jednotné přihlašování pro všechny cloudové aplikaci, která má HTML na přihlašovací stránku. Pomocí modulu plug-in vlastním prohlížeči AAD automatizuje proces přihlašování uživatele prostřednictvím bezpečně načítání pověření aplikací, jako je například uživatelské jméno a heslo z adresáře a zadá tyto přihlašovací údaje do aplikace přihlašovací stránky jménem uživatel. Existují dva případy použití:

1. **Správce spravuje přihlašovací údaje** – správci mohou vytvořit a spravovat aplikace pověření a přiřaďte tyto přihlašovací údaje uživatele nebo skupiny, kteří potřebují přístup k aplikaci. V těchto případech koncového uživatele není potřeba znát přihlašovací údaje, ale stále jednoduše tak, že na ni kliknete v jejich přístupového panelu nebo prostřednictvím zadaný odkaz získá přístup jediné přihlášení k aplikaci. To umožňuje obě, Správa životního cyklu přihlašovacích údajů správce, jakož i pohodlí pro koncové uživatele, kterým se nemusíte mějte na paměti, nebo spravovat hesla konkrétní aplikaci. Přihlašovací údaje jsou zamaskované z koncový uživatel během automatizovaného procesu přihlášení; jsou ale technicky zjistitelný uživatelem pomocí nástroje pro ladění webových a uživatelům a správcům by měl podle stejné zásady zabezpečení, jako v případě, že přihlašovací údaje prezentovaly přímo uživatelem. Zadaný správce přihlašovacích údajů jsou užitečné při poskytování přístupu účtu, která je sdílena mezi mnoha uživateli, jako je například sociálních médií nebo aplikace pro sdílení dokumentu.
2. **Spravuje přihlašovací údaje uživatele** – správci můžete přiřadit aplikace pro koncové uživatele nebo skupiny a povolit koncovým uživatelům zadat svoje vlastní přihlašovací údaje přímo při přístupu k aplikaci poprvé v jejich přístupového panelu. Tím se vytvoří v zájmu usnadnění pro koncové uživatele, kterým se nemusíte průběžně zadejte konkrétní aplikaci hesla při každém přístupu k aplikaci. Uživatelé mohou i nadále spravovat hesla aktualizaci nebo odstranění je podle potřeby. Tento případ použití mohou sloužit také jako taktování kamenem správu správy přihlašovacích údajů, které správce může nastavit nové přihlašovací údaje pro aplikace v budoucnosti beze změny aplikační přístup prostředí koncového uživatele.

V obou případech přihlašovací údaje jsou uložené v šifrovaném stavu v adresáři a jsou pouze předán přes protokol HTTPS během automatizovaného procesu přihlášení. Pomocí založené na heslech jednotné přihlašování, Azure AD nabízí řešení pro aplikace, které nejsou podporovat protokoly federační správu přístupu pohodlný identity.

Jednotné přihlašování založené na heslech spoléhá na rozšíření prohlížeče bezpečně načítat aplikace a informace o uživateli z Azure AD a použít ji ke službě. Tuto funkci podporovat většinu aplikací SaaS jiných výrobců, které jsou podporovány službou Azure AD.

Pro jednotné přihlašování založené na heslech může být prohlížeče koncového uživatele:
* Internet Explorer 11 – Windows 7 nebo novější.
* Hraniční Windows 10 Anniversary Edition nebo novější. 
* Chrome – V systému Windows 7 nebo novější a v systému MacOS X nebo novější
* Firefox 26.0 nebo později – do systému Windows XP SP2 nebo novější a v systému Mac OS X 10,6 nebo novější

### <a name="existing-single-sign-on"></a>Existující jednotné přihlašování
Při konfiguraci jednotné přihlašování pro aplikace, portál Azure poskytuje třetí možnost z "existující Single Sign-On". Tato možnost jenom umožňuje správci vytvořit odkaz na aplikaci a umístěte ji na panel přístupu pro vybraného uživatele.

Například pokud je aplikace, který je nakonfigurovaný k ověřování uživatelů pomocí Active Directory Federation Services 2.0, správce slouží možnost "existující Single Sign-On" k vytvoření odkazu na na panel přístupu. Když uživatelé přistupují k odkazu, musí se ověřit pomocí Active Directory Federation Services 2.0 nebo jakoukoli existující jeden přihlašování řešení je k dispozici v aplikaci.

### <a name="user-provisioning"></a>Zřizování uživatelů
U aplikací, vyberte možnost Azure AD umožňuje zřizování automatizované uživatele a jeho rušení účtů v jiných výrobců aplikace SaaS v rámci portálu pro správu Azure, pomocí informací o vašem Windows Server Active Directory nebo Azure AD identity. Pokud má uživatel oprávnění ve službě Azure AD pro jednu z těchto aplikací, účet se dají automaticky vytvořit (zřízená) cílové aplikace SaaS.

Při odstranění uživatele nebo jeho informace o změny ve službě Azure AD, tyto změny se projeví také v aplikaci SaaS. To znamená, konfigurace automatizované identity životního cyklu správy umožňuje správcům řídit a poskytnout automatické zřizování a jeho rušení z aplikací SaaS. Ve službě Azure AD je povolena tato automatizace správy životního cyklu identit podle zřizování uživatelů.

Další informace najdete v tématu [automatické zřizování uživatelů a jeho rušení pro aplikace SaaS](active-directory-saas-app-provisioning.md)

## <a name="get-started-with-the-azure-ad-application-gallery"></a>Začínáme s galerii aplikací Azure AD
Jste připravení začít? K nasazení jednotného přihlašování mezi službou Azure AD a aplikace SaaS, které vaše organizace používá, postupujte podle těchto pokynů.

### <a name="using-the-azure-ad-application-gallery"></a>Pomocí galerii aplikací Azure AD
[Azure Active Directory Application Gallery](https://azure.microsoft.com/marketplace/active-directory/all/) poskytuje seznam aplikací, které zaručeně podporují forma jednotné přihlašování s Azure Active Directory.

![Galerie online aplikace Azure](media/active-directory-appssoaccess-whatis/onlineappgallery.png)

Zde jsou některé tipy pro hledání aplikace podle jaké funkce podporují:

* Azure AD podporuje automatické zřizování a jeho rušení pro všechny aplikace "Doporučený" [Azure Active Directory Application Gallery](https://azure.microsoft.com/marketplace/active-directory/all/).
* Seznam federované aplikace, které podporují konkrétně federovaného jednotného přihlašování pomocí protokolu, například SAML, WS-Federation, nebo OpenID Connect najdete [zde](http://social.technet.microsoft.com/wiki/contents/articles/20235.azure-active-directory-application-gallery-federated-saas-apps.aspx).

Jakmile jste najde aplikaci, můžete začít používat podle postupujte podle podrobné pokyny uvedené v galerii aplikací a v portálu správy Azure umožňující jednotného přihlašování.

### <a name="application-not-in-the-gallery"></a>Aplikace není v galerii?
Pokud vaše aplikace nebyl nalezen v galerii aplikací Azure AD, máte tyto možnosti:

* **Přidat neuvedené aplikaci používáte** -použít vlastní kategorii v galerii aplikací v rámci portálu správy Azure připojit neuvedené aplikaci, která vaše organizace používá. Přidáním jakékoli aplikace, který podporuje SAML 2.0 jako federované aplikace nebo jakékoli aplikace, která má HTML na přihlašovací stránku jako heslo jednotného přihlašování k aplikaci. Další podrobnosti najdete v tomto článku na [přidání vlastní aplikace](application-config-sso-how-to-configure-federated-sso-non-gallery.md).
* **Přidat vlastní aplikaci vyvíjíte** – Pokud jste vytvořili aplikaci sami, postupujte podle pokynů v dokumentaci pro vývojáře Azure AD k implementaci federovaného jednotného přihlašování nebo zřizování s využitím Azure AD graph API. Další informace naleznete v následujících zdrojích:
  
  * [Scénáře ověřování pro Azure AD](active-directory-authentication-scenarios.md)
  * [https://github.com/AzureADSamples/WebApp-MultiTenant-OpenIdConnect-DotNet](https://github.com/AzureADSamples/WebApp-MultiTenant-OpenIdConnect-DotNet)
  * [https://github.com/AzureADSamples/WebApp-WebAPI-MultiTenant-OpenIdConnect-DotNet](https://github.com/AzureADSamples/WebApp-WebAPI-MultiTenant-OpenIdConnect-DotNet)
  * [https://github.com/AzureADSamples/NativeClient-WebAPI-MultiTenant-WindowsStore](https://github.com/AzureADSamples/NativeClient-WebAPI-MultiTenant-WindowsStore)
* **Žádosti o aplikace integrační** -žádosti o podporu pro aplikace, musíte pomocí [fóru pro zpětnou vazbu Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory/).

### <a name="using-the-azure-portal"></a>Použití webu Azure Portal
Rozšíření Active Directory na portálu Azure můžete použít ke konfiguraci aplikace jednotného přihlašování. Jako první krok je nutné vybrat adresáře z oddílu služby Active Directory na portálu:

![][2]

Pokud chcete spravovat aplikace SaaS jiných výrobců, můžete přepnout na kartě aplikace vybraného adresáře. Toto zobrazení umožňuje správcům:

* Přidat nové aplikace z Galerie Azure AD, jakož i aplikace, které vyvíjíte
* Odstranit integrované aplikace
* Spravovat aplikace, které již mají integrované

Typické úlohy správy pro aplikace SaaS jiných výrobců jsou:

* Povolení jednotného přihlašování s Azure AD, heslem jednotného přihlašování nebo, pokud je k dispozici pro cíl SaaS, federovaného jednotného přihlašování
* Volitelně můžete povolit pro uživatele, zřizování a jeho rušení (Správa životního cyklu identity) zřizování uživatelů
* Pro aplikace, kde je povoleno zřizování uživatelů výběr uživatelů, kteří mají přístup k dané aplikaci

Pro aplikace Galerie, které podporují federované jednotné přihlašování konfigurace obvykle musíte zadat další konfiguraci nastavení jako třeba certifikáty a metadat pro vytvoření federovaného vztahu důvěryhodnosti mezi aplikací třetích stran a Azure AD. Průvodce konfigurací vás provede podrobnosti a poskytuje snadný přístup k datům specifické pro aplikace SaaS a pokyny.

Galerie aplikace, které podporují automatické zřizování uživatelů musíte poskytnout oprávnění Azure AD spravovat vaše účty v aplikaci SaaS. Minimálně je třeba zadat přihlašovací údaje Azure AD by měl používat při ověřování přes do cílové aplikace. Jestli se nastavení konfigurace musí být zadané závisí na požadavky na aplikace.

## <a name="deploying-azure-ad-integrated-applications-to-users"></a>Nasazení služby Azure AD integrovaných aplikací pro uživatele
Azure AD poskytuje několik způsobů přizpůsobitelné k nasazení aplikací pro koncové uživatele ve vaší organizaci:

* Azure AD přístupového panelu
* Spouštěč aplikace Office 365
* Přímé přihlášení k federovaným aplikacím
* Přímé odkazy na federované nebo existující aplikace či aplikace založené na hesle

Které metody rozhodnete nasadit ve vaší organizaci je vašeho vlastního rozhodnutí.

### <a name="azure-ad-access-panel"></a>Azure AD přístupového panelu
Přístupovému panelu na adrese https://myapps.microsoft.com je webový portál, který umožňuje koncový uživatel s účtem organizace v Azure Active Directory k zobrazení a spuštění cloudové aplikace ke kterým mají byl udělen přístup správce Azure AD. Pokud jste koncovým uživatelem s [Azure Active Directory Premium](https://azure.microsoft.com/pricing/details/active-directory/), můžete také používat skupiny samoobslužné služby možnosti správy prostřednictvím na přístupovém panelu.

![Azure AD přístupového panelu](media/active-directory-appssoaccess-whatis/azure-ad-access-panel.png)

Přístupový Panel je nezávislý na portálu Azure a nevyžaduje, aby uživatelé měli předplatného Azure nebo předplatné služeb Office 365.

Další informace o přístupový panel Azure AD, najdete v článku [Úvod k přístupovému panelu](active-directory-saas-access-panel-introduction.md).

### <a name="office-365-application-launcher"></a>Spouštěč aplikace Office 365
Pro organizace, které jste nasadili Office 365, aplikace přiřazené k uživatelům prostřednictvím služby Azure AD se také zobrazí na portálu Office 365 v https://portal.office.com/myapps. To je snadný a pohodlný pro uživatele v organizaci spustíte svoje aplikace bez nutnosti použít portál druhý a je doporučenou aplikaci spuštění řešení pro organizace používá Office 365.

![][4]

Další informace o Spouštěč aplikace Office 365 najdete v tématu [mít vaše aplikace se zobrazí v Spouštěč aplikace Office 365](https://msdn.microsoft.com/office/office365/howto/connect-your-app-to-o365-app-launcher).

### <a name="direct-sign-on-to-federated-apps"></a>Přímé přihlášení k federovaným aplikacím
Nejvíce federovaným aplikacím, které podporují SAML 2.0 a WS-Federation, OpenID connect také podporu možnosti pro spuštění na aplikaci, a potom získat přihlášení prostřednictvím služby Azure AD pomocí automatické přesměrování nebo kliknutím na odkaz pro přihlášení uživatele. To se označuje jako poskytovatel služeb-zahájí přihlašování a nejvíce federované aplikace v galerii aplikací Azure AD podporují tento (viz dokumentace odkazované z Průvodce konfigurace přihlášení aplikace v portálu správy Azure podrobnosti).

![][5]

### <a name="direct-sign-on-links-for-federated-password-based-or-existing-apps"></a>Přímé odkazy přihlášení pro federované, založené na heslech nebo existující aplikace
Azure AD podporuje přímé jeden přihlašování odkazy na jednotlivých aplikací, které podporují založené na heslech jednotné přihlašování, existující jednotné přihlašování a jakoukoli formu federované jednotné přihlašování.

Tyto odkazy jsou specificky vytvořený adresy URL, které odeslat uživatele prostřednictvím přihlášení Azure AD v procesu pro danou aplikaci bez nutnosti spuštění je uživatele z přístupového panelu Azure AD nebo Office 365. Tyto jedné adresy URL přihlašování najdete v části kartu řídicí panel všechny předem integrované aplikace v oddílu služby Active Directory portál pro správu Azure, jak je vidět na tomto snímku obrazovky.

![][6]

Tyto odkazy můžete kopírovat a vkládat data odkudkoli byste chtěli poskytnout přihlašovací odkaz na vybranou aplikaci. To může být v e-mailu nebo v jakékoli vlastní webový portál, který jste nastavili pro přístup k aplikaci uživatele. Tady je příklad Azure AD přímé jeden přihlašovací adresa URL pro Twitter:

`https://myapps.microsoft.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

Podobně jako u adres URL organizace specifické pro přístupový panel, můžete dále přizpůsobit tuto adresu URL jednu aktivní nebo ověřené domény pro váš adresář přidáním po myapps.microsoft.com domény. To zajistí, že organizace branding je načíst okamžitě na přihlašovací stránce bez nutnosti nejprve zadejte své ID uživatele uživatele:

`https://myapps.microsoft.com/contosobuild.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

Když oprávněný uživatel klikne na jeden z těchto odkazů specifické pro aplikace, se nejprve zobrazí jejich organizace přihlašovací stránka (za předpokladu, že se ještě nejste přihlášeni) a po přihlášení přesměrováni do své aplikace bez zastavení na panel přístupu. Pokud uživatel chybí požadavky pro přístup k aplikaci, jako je například rozšíření prohlížeče založené na heslech jednotné přihlašování, odkaz vyzve uživatele k instalaci chybějící rozšíření. Adresu URL odkazu také konstantní, pokud změny v jedné konfiguraci přihlášení pro aplikaci.

Tyto odkazy použít stejné mechanismy řízení přístupu jako panel přístupu a Office 365 a pouze tito uživatelé nebo skupiny, kteří mají přiřazený k aplikaci v portálu správy Azure budete moct úspěšně ověřit. Každý uživatel, který není autorizovaný se však zobrazí zprávu s vysvětlením, že nebyl udělen přístup a mají odkaz na zatížení na přístupovém panelu, chcete-li zobrazit dostupné aplikace, ke kterým mají přístup.

## <a name="related-articles"></a>Související články
* [Rejstřík článků o správě aplikací ve službě Azure Active Directory](active-directory-apps-index.md)
* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vyhledání aplikace neschválená cloudových aplikací s Cloud App Discovery](active-directory-cloudappdiscovery-whatis.md)
* [Úvod do správy přístupu k aplikacím](active-directory-managing-access-to-apps.md)
* [Porovnání funkcí pro správu externí identity ve službě Azure AD](active-directory-b2b-compare-external-identities.md)

<!--Image references-->
[1]: ./media/active-directory-appssoaccess-whatis/onlineappgallery.png
[2]: ./media/active-directory-appssoaccess-whatis/azuremgmtportal.png
[3]: ./media/active-directory-appssoaccess-whatis/accesspanel.png
[4]: ./media/active-directory-appssoaccess-whatis/officeapphub.png
[5]: ./media/active-directory-appssoaccess-whatis/workdaymobile.png
[6]: ./media/active-directory-appssoaccess-whatis/deeplink.png
