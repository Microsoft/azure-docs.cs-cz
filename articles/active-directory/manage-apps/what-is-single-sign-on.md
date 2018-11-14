---
title: Jednotné přihlašování k aplikacím – Azure Active Directory | Dokumentace Microsoftu
description: Zjistěte, jak vybrat jedinou metodu přihlašování, při konfiguraci aplikací v Azure Active Directory (Azure AD). Použití jednotného přihlašování, aby uživatelé nemuseli si pamatuje hesla pro každou aplikaci a zjednodušit správu správu účtů.
services: active-directory
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 11/12/2018
ms.author: barbkess
ms.reviewer: arvindh
ms.openlocfilehash: 5dedef82fcae2b50a58af924f5748c900adf96e0
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2018
ms.locfileid: "51624603"
---
# <a name="single-sign-on-to-applications-in-azure-active-directory"></a>Jednotné přihlašování k aplikacím v Azure Active Directory
Zjistěte, jak zvolit nejvhodnější jednotné přihlašování metodu při konfiguraci aplikací v Azure Active Directory (Azure AD). 

- **S jednotným přihlašováním**, uživatelům přihlásit se jednou pomocí jednoho účtu pro přístup k zařízení připojených k doméně, firemním prostředkům, software jako služba (SaaS) aplikací a webových aplikací. Po přihlášení uživatele můžou spouštět aplikace z portálu Office 365 nebo přístupového panelu Azure AD MyApps. Správci mohou centralizovat správu účtů uživatelů a automaticky přidat nebo odebrat přístup uživatelů k aplikacím na základě členství ve skupině. 

- **Bez jednotného přihlašování**, uživatelé musí pamatovat si hesla specifické pro aplikaci a přihlaste se pro danou aplikaci. IT pracovníci musí vytvořit a aktualizovat uživatelské účty pro každou aplikaci, jako je Office 365, Box nebo Salesforce. Uživatelé musí pamatovat si hesla a ztrácet čas přihlášení pro danou aplikaci.

Tento článek popisuje metody přihlašování a vám pomůže vybrat nejvhodnější metodu pro vaše aplikace.

## <a name="choosing-a-single-sign-on-method"></a>Volba metody jednotné přihlašování

Existuje několik způsobů, jak nakonfigurovat aplikaci pro jednotné přihlašování. Volba metody jednotného přihlašování pro aplikace závisí na konfiguraci aplikace k ověřování. Všechny jednotné přihlašování metody, s výjimkou zakázaná, automaticky přihlásit uživatele aplikace bez nutnosti druhý přihlašování.  

- Cloudové aplikace použít pro jednotné přihlašování SAML, založené na heslech, propojené nebo zakázáno metody. Jazyk SAML je nejbezpečnější jednotné přihlašování metodou.
- U místních aplikací můžete použít založené na heslech, integrované Windows ověření, založené na hlavičkách, propojené nebo zakázáno metody pro jednotné přihlašování. Volby místní fungovat, když aplikace jsou konfigurovány pro Proxy aplikací. 

Tato vývojový diagram pomáhá při rozhodování, jakou metodu jednotné přihlašování je nejvhodnější pro vaše konkrétní potřeby. 

![Zvolte jednotné přihlašování – metoda](./media/what-is-single-sign-on/choose-single-sign-on-method.png)

Následující tabulka shrnuje metody přihlašování a odkazy na další podrobnosti. 

| Metoda jednotného přihlašování | Typy aplikací | Kdy je použít |
| :------ | :------- | :----- |
| [SAML](#saml-sso) | Jen v cloudu | Použijte protokol SAML, kdykoli je to možné. SAML funguje v případě aplikací, které jsou nakonfigurovány pro použití jednoho z protokolů SAML.|
| [Založené na heslech](#password-based-sso) | cloudové a místní | Použijte, když se aplikace ověřuje pomocí uživatelského jména a hesla. Založené na heslech jednotné přihlašování umožňuje zabezpečené uložení hesel aplikace a jejich přehrání pomocí rozšíření webového prohlížeče nebo mobilní aplikace. Tato metoda používá existující přihlašovací proces poskytovaný aplikací, ale umožňuje správcům spravovat hesla. |
| [Propojené](#linked-sso) | cloudové a místní | Používejte propojené jednotného přihlašování aplikace je nakonfigurovaná pro jednotné přihlašování v jiném identifikaci zprostředkovatele služby. Tato možnost nepřidá jednotné přihlašování k aplikaci. Aplikace může být však již jednotného přihlašování implementované pomocí jiné služby, jako je Active Directory Federation Services.|
| [Disabled](#disabled-sso) (Zakázáno) | cloudové a místní | Když není připraveno ke konfiguraci jednotného přihlašování aplikace zakazuje použití jednotného přihlašování. Uživatelé potřebují k zadání uživatelského jména a hesla při každém spuštění této aplikace.|
| [Ověření integrované Windows (IWA)](#integrated-windows-authentication-iwa-sso) | jen místně | Použít tuto metodu jednotné přihlašování pro aplikace, které používají [integrované ověřování Windows (IWA)](/aspnet/web-api/overview/security/integrated-windows-authentication), nebo s deklaracemi identity aplikace. Konektory Proxy aplikací pomocí protokolu Kerberos omezené delegování (KCD) k ověřování uživatelů k aplikaci. | 
| [Založené na hlavičkách](#header-based-sso) | jen místně | Použijte založeným na hlavičkách jednotného přihlašování, pokud aplikace používá hlavičky pro ověřování. Založené na hlavičkách jednotného přihlašování vyžaduje PingAccess pro Azure Active Directory. Proxy aplikace ověřuje uživatele pomocí služby Azure AD a potom předává provoz přes službu konektoru.  | 

## <a name="saml-sso"></a>JEDNOTNÉ PŘIHLAŠOVÁNÍ SAML
S **SAML jednotného přihlašování**, Azure AD ověří k aplikaci pomocí účtu uživatele Azure AD. Azure AD komunikuje informace přihlašování k aplikaci pomocí připojení protokolu. S založené na SAML jednotného přihlašování můžete mapování uživatelů na konkrétní aplikační role na základě pravidel, které definujete váš SAML deklaracemi identity

Založené na SAML jednotného přihlašování je:

- Bezpečnější než založené na heslech jednotného přihlašování a všechny ostatní přihlašování metody.
- Naše doporučenou metodou pro jednotné přihlašování.

Založené na SAML jednotného přihlašování se podporuje pro aplikace, které používají některá z těchto protokolů:

- SAML 2.0
- WS-Federation
- OpenID connect

Konfigurace aplikace pro založené na SAML jednotného přihlašování, naleznete v tématu [založené na SAML nakonfigurovat jednotné přihlašování](configure-single-sign-on-portal.md). Mnoho aplikací navíc [specifické pro aplikaci kurzy](../saas-apps/tutorial-list.md) , který jednotlivé kroky konfigurace založené na SAML jednotného přihlašování pro určité aplikace. 

Další informace o tom, jak funguje protokol SAML, naleznete v tématu [jednotné přihlašování – protokol SAML](../develop/single-sign-on-saml-protocol.md).

## <a name="password-based-sso"></a>Jednotné přihlašování pomocí hesla
Aplikace založené na heslech přihlašování, ověřuje aplikace pomocí uživatelského jména a hesla. Koncoví uživatelé přihlásit k aplikaci při prvním čas, když přístup. Po první přihlášení poskytuje Azure Active Directory uživatelské jméno a heslo k aplikaci. 

Založené na heslech jednotné přihlašování pomocí stávajícího procesu ověřování poskytované aplikací. Když povolíte heslem jednotného přihlašování pro aplikace, služby Azure AD shromažďuje a bezpečně uloží uživatelské jméno a heslo pro aplikaci. Přihlašovací údaje uživatele jsou uloženy v šifrovaném stavu v adresáři. 

Použití založené na heslech jednotné přihlášení při:

- Aplikace nemůže podporovat protokol SAML pro jednotné přihlašování.
- Aplikace se ověřuje pomocí uživatelského jména a hesla místo záhlaví a přístupové tokeny.

Založené na heslech jednotného přihlašování se podporuje pro každou aplikaci založené na cloudu, který má založeného na HTML přihlašovací stránky. Uživatele můžete použít některý z následujících prohlížečů:

- Internet Explorer 11 na Windows 7 nebo novější
- Edge ve Windows 10 Anniversary Edition nebo novější 
- Chrome ve Windows 7 nebo novější a v systému MacOS X nebo novější
- Firefox 26.0 nebo novější, Windows XP SP2 nebo novější a na Mac OS X 10.6 nebo novější

Konfigurování cloudové aplikace založené na heslech jednotné přihlašování, naleznete v tématu [konfigurace aplikace pro heslo jednotného přihlašování](application-sign-in-problem-password-sso-gallery.md#configure-the-application-for-password-single-sign-on).

Pokud chcete nakonfigurovat místní aplikaci pro jednotné přihlašování prostřednictvím Proxy aplikací, najdete v článku [heslo vaulting pro jednotné přihlašování s Proxy aplikací](application-proxy-configure-single-sign-on-password-vaulting.md)

### <a name="managing-credentials-for-password-based-sso"></a>Správa přihlašovacích údajů pro jednotné přihlašování pomocí hesla

Azure AD pro ověření uživatele k aplikaci, načte přihlašovací údaje uživatele z adresáře a přejde na přihlašovací stránku vaší aplikace.  Přihlašovací údaje uživatele Azure AD bezpečně předá prostřednictvím rozšíření webového prohlížeče nebo mobilní aplikace. Tento proces umožňuje správci spravovat přihlašovací údaje uživatele a nevyžaduje, aby uživatelům Zapamatovat heslo.

> [!IMPORTANT]
> Přihlašovací údaje jsou zakódovány od koncového uživatele během procesu automatické přihlašování. Přihlašovací údaje jsou však zjistitelné pomocí nástrojů pro ladění webů. Uživatelé a správci musí postupovat podle stejné zásady pro zabezpečení, jako kdyby jste zadali přihlašovací údaje přímo uživatelem.

Hesla pro každou aplikaci je možné spravovat buď pomocí Správce Azure AD nebo uživateli.

Když správce Azure AD spravuje přihlašovací údaje:  

- Uživatel není nutné resetovat nebo zapamatujte si uživatelské jméno a heslo. Má uživatel přístup kliknutím na jejich přístupovém panelu, nebo prostřednictvím poskytnutý odkaz aplikaci.
- Správce může provádět úkoly správy na přihlašovací údaje. Správce může například aktualizovat přístup k aplikacím podle uživatele členství ve skupinách a stav zaměstnance.
- Správce může použít přihlašovací údaje pro správu k zajištění přístupu k aplikacím, které jsou sdílené mezi mnoha uživateli. Správce například můžete umožnit všem uživatelům, kdo má přístup k aplikaci přístup na sociálních médií nebo do dokumentu aplikace pro sdílení obsahu.

Když koncový uživatel spravuje přihlašovací údaje:

- Uživatelé mohou spravovat svá hesla, aktualizace nebo odstranění je podle potřeby. 
- Správci budou mít pořád povolený nastavit nové přihlašovací údaje pro aplikaci.


## <a name="linked-sso"></a>Propojené jednotného přihlašování
Propojené přihlašování umožňuje Azure AD za účelem poskytování jednotného přihlašování k aplikaci, která už je nakonfigurovaný pro jednotné přihlašování v jiné službě. Propojené aplikace může zobrazit koncovým uživatelům na portálu Office 365 nebo portálu Azure AD MyApps. Uživatele můžete například spustit aplikaci, která je nakonfigurovaná pro jednotné přihlašování v Active Directory Federation Services 2.0 (AD FS) z portálu Office 365. Další vytváření sestav je také k dispozici pro propojené aplikace, které jsou spouštěny z portálu Office 365 nebo portálu Azure AD MyApps. 

Použití propojené jednotného přihlašování pro:

- Poskytují konzistentní uživatelské prostředí, zatímco migrace aplikací po určitou dobu. Pokud se migrace aplikací do Azure Active Directory, můžete použít propojené jednotného přihlašování můžete rychle publikovat odkazy na všechny aplikace, kterou chcete migrovat.  Uživatelé mohou najít všechny odkazy [portálu MyApps](../user-help/active-directory-saas-access-panel-introduction.md) nebo [Spouštěče aplikací Office 365](https://support.office.com/article/meet-the-office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a). Uživatelé nebudou vědět, že přistupuje propojené aplikace nebo migrované aplikace.  

Jakmile uživatel byl ověřen s propojených aplikací, je potřeba vytvořit předtím, než se koncovému uživateli poskytuje přístup pomocí jednotného přihlašování záznam účtu. Zřizování tento záznam účtu můžete buď automaticky provedou, nebo to může nastat, ručně správcem.

## <a name="disabled-sso"></a>Jednotné přihlašování zakázáno

Zakázané režim znamená, že jednotné přihlašování se nepoužívá pro aplikaci. Pokud je jednotné přihlašování zakázáno, uživatele může být nutné k ověření dvakrát. Nejdřív ověřit uživatele Azure AD a potom se přihlásí do aplikace. 

Režim jednotného přihlašování zakazuje použití:

- Pokud si nejste připraveni integrovat aplikace s Azure AD jednotného přihlašování, nebo
- Pokud testujete další aspekty aplikace, nebo
- Jako vrstvu zabezpečení, které místní aplikaci, která nevyžaduje, aby uživatele bude možné ověřit. Se zakázanou potřebuje ověřit uživatele. 

## <a name="integrated-windows-authentication-iwa-sso"></a>Integrované ověřování Windows (IWA) jednotného přihlašování

Proxy aplikací Azure AD poskytuje jednotné přihlašování (SSO) k aplikacím, které používají [integrované ověřování Windows (IWA)](/aspnet/web-api/overview/security/integrated-windows-authentication), nebo s deklaracemi identity aplikace. Pokud vaše aplikace používá IWA, Proxy aplikace ověřuje aplikace s použitím protokolu Kerberos omezené delegování (KCD). Pro aplikace s deklaracemi identity, která vztahy důvěryhodnosti služby Azure Active Directory jednotné přihlašování funguje, protože již byl uživatel ověřen pomocí služby Azure AD.

Režim integrovaného ověřování Windows jednotné přihlašování:

- K poskytnutí jednotného přihlašování k místní aplikaci, která se ověřuje pomocí IWA. 

Pokud chcete nakonfigurovat místní aplikaci pro IWA, naleznete v tématu [omezeného delegování protokolu Kerberos pro jednotné přihlašování do aplikací pomocí Proxy aplikace](application-proxy-configure-single-sign-on-with-kcd.md). 

### <a name="how-single-sign-on-with-kcd-works"></a>Jak jednotné přihlašování s KCD funguje
Tento diagram popisuje tok, když uživatel získá přístup k místní aplikaci, která používá IWA.

![Vývojový diagram ověřování Microsoft AAD](./media/application-proxy-configure-single-sign-on-with-kcd/AuthDiagram.png)

1. Uživatel zadá adresu URL pro přístup k místní aplikaci pomocí služby Proxy aplikací.
2. Proxy aplikace přesměruje požadavek preauthenticate ověřování služby Azure AD. V tuto chvíli Azure AD použije všechny příslušné ověřování a zásady autorizace, jako je například vícefaktorové ověřování. Pokud uživatel je ověřen, Azure AD vytvoří token a odešle se uživateli.
3. Uživatel se předá token Proxy aplikací.
4. Proxy aplikace ověří token a načte hlavní název uživatele (UPN) z tokenu. Ke konektoru prostřednictvím zabezpečeného kanálu obousměrně ověřeného pak odešle požadavek, hlavní název uživatele a hlavní název služby (SPN).
5. Konektor používá vyjednávání protokolu Kerberos omezené delegování (KCD) se v místním AD zosobňování uživatele získá token protokolu Kerberos k aplikaci.
6. Služby Active Directory odešle token protokolu Kerberos pro použití konektoru.
7. Konektor odešle původní požadavek na aplikační server pomocí obdrženého ze služby AD token protokolu Kerberos.
8. Aplikace odešle odpověď na konektoru, který je pak vrácen do Proxy aplikace služby a nakonec na uživatele.

## <a name="header-based-sso"></a>Jednotné přihlašování založené na hlavičkách

Založené na hlavičkách jednotné přihlašování funguje pro aplikace, které k ověřování používají hlavičky protokolu HTTP. Tato metoda přihlašování využívá ověřování třetích stran služby zvané PingAccess. Potřebuje uživatel jen pro ověření do služby Azure AD. 

Použití založené na hlavičkách jednotné přihlášení při:

- Proxy aplikací a Pingaccessem jsou nakonfigurovány pro aplikaci

Postup konfigurace ověřování založené na hlavičkách, naleznete v tématu [ověřování založené na hlavičkách pro jednotné přihlašování s Proxy aplikací](application-proxy-configure-single-sign-on-with-ping-access.md). 

### <a name="what-is-pingaccess-for-azure-ad"></a>Co je PingAccess pro Azure AD?

Pomocí PingAccess pro Azure AD, uživatelé mohou přístupu a jeden přihlašování k aplikacím, které k ověřování používají hlavičky. Proxy aplikací bude tyto aplikace stejně jako jakýkoli jiný, k ověření přístupu pomocí Azure AD a následným předáním provoz přes službu konektoru. Když dojde k ověření, přeloží služby PingAccess přístupový token Azure AD do záhlaví ve formátu, který je odeslán do aplikace.

Uživatelé nebudou Všimněte si, že něco jinak při přihlášení k vašim podnikovým aplikacím používat. Mohou i nadále pracovat z kdekoli na jakémkoli zařízení. Konektory Proxy aplikací směrovat vzdálený provoz pro všechny aplikace a budou dál k vyrovnávání zatížení automaticky.

### <a name="how-do-i-get-a-license-for-pingaccess"></a>Jak získám licence PingAccess?

Vzhledem k tomu, že tento scénář je dostupná prostřednictvím Azure Active Directory a PingAccess partnerství, potřebují licence pro obě služby. Předplatná Azure Active Directory Premium však obsahují PingAccess licence basic, která zahrnuje až 20 aplikací. Pokud je potřeba publikovat více než 20 aplikací založené na hlavičkách, můžete získat další licenci z PingAccess. 

Další informace najdete v článku [Edice služby Azure Active Directory](../fundamentals/active-directory-whatis.md).

## <a name="related-articles"></a>Související články
* [Kurzy integrace aplikací SaaS pomocí Azure Active Directory](../saas-apps/tutorial-list.md)
* [Kurz ke konfiguraci jednotného přihlašování](configure-single-sign-on-portal.md)
* [Úvod do správy přístupu k aplikacím](what-is-access-management.md)
* Odkaz ke stažení: [plánu nasazení přihlašování](http://aka.ms/SSODeploymentPlan).


