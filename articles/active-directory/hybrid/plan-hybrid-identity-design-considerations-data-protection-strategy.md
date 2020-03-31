---
title: Návrh hybridní identity – strategie ochrany dat Azure | Dokumenty společnosti Microsoft
description: Definujete strategii ochrany dat pro řešení hybridní identity tak, aby splňovala obchodní požadavky, které jste definovali.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: e76fd1f4-340a-492a-84d9-e05f3b7cc396
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: e0186d862968259aae73071cfecd7d62443d0256
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67109357"
---
# <a name="define-data-protection-strategy-for-your-hybrid-identity-solution"></a>Definujte strategii ochrany dat pro řešení hybridní identity
V této úloze definujete strategii ochrany dat pro řešení hybridní identity tak, aby splňovala obchodní požadavky, které jste definovali v:

* [Určení požadavků na ochranu údajů](plan-hybrid-identity-design-considerations-dataprotection-requirements.md)
* [Určení požadavků na správu obsahu](plan-hybrid-identity-design-considerations-contentmgt-requirements.md)
* [Určení požadavků na řízení přístupu](plan-hybrid-identity-design-considerations-accesscontrol-requirements.md)
* [Určení požadavků na reakce na incidenty](plan-hybrid-identity-design-considerations-incident-response-requirements.md)

## <a name="define-data-protection-options"></a>Definování možností ochrany dat
Jak je vysvětleno v [části Určit požadavky na synchronizaci adresářů](plan-hybrid-identity-design-considerations-directory-sync-requirements.md), microsoft azure ad můžete synchronizovat s místní služby Active Directory Domain Services (AD DS). Tato integrace umožňuje organizacím používat Azure AD k ověření přihlašovacích údajů uživatelů při pokusu o přístup k podnikovým prostředkům. Můžete to provést pro oba scénáře: data v klidovém stavu v místním prostředí a v cloudu. Přístup k datům ve službě Azure AD vyžaduje ověření uživatele prostřednictvím služby tokenů zabezpečení (STS).

Po ověření se hlavní jméno uživatele (UPN) čte z ověřovacího tokenu. Systém autorizace pak určuje replikovaný oddíl a kontejner odpovídající doméně uživatele. Informace o existenci uživatele, povoleném stavu a roli pak pomohou autorizačnímu systému určit, zda je pro uživatele v této relaci povolen přístup k cílovému klientovi. Některé autorizované akce (konkrétně vytvoření resetování uživatele a hesla) vytvoří záznam auditu, který pak správce klienta používá ke správě úsilí o dodržování předpisů nebo šetření.

Přesunutí dat z místního datového centra do Úložiště Azure přes připojení k Internetu nemusí být vždy proveditelné z důvodu objemu dat, dostupnosti šířky pásma nebo jiných aspektů. [Služba importu a exportu úložiště Azure](../../storage/common/storage-import-export-service.md) poskytuje hardwarovou možnost pro umístění nebo načítání velkých objemů dat do úložiště objektů blob. Umožňuje odesílat pevné disky [šifrované nástrojem BitLocker](https://technet.microsoft.com/library/dn306081#BKMK_BL2012R2) přímo do datového centra Azure, kde cloudoví operátoři nahrávají obsah do vašeho účtu úložiště, nebo si můžou stáhnout data Azure do vašich disků a vrátit se k vám. Pro tento proces jsou přijímány pouze šifrované disky (pomocí klíče nástroje BitLocker generovaného samotnou službou během instalace úlohy). Klíč BitLocker je k dispozici azure samostatně, a tím poskytuje sdílení klíče mimo pásmo.

Vzhledem k tomu, že data při přenosu mohou probíhat v různých scénářích, je také důležité vědět, že Microsoft Azure používá [virtuální sítě](https://azure.microsoft.com/documentation/services/virtual-network/) k izolaci provozu klientů od sebe navzájem, za použití opatření, jako jsou brány firewall na úrovni hostitele a hosta, filtrování paketů IP, blokování portů a koncové body HTTPS. Většina interní komunikace Azure, včetně infrastruktury k infrastruktuře a infrastruktury pro zákazníka (místní), je však také šifrovaná. Dalším důležitým scénářem je komunikace v rámci datových center Azure; Společnost Microsoft spravuje sítě a zajišťuje, že žádný virtuální počítač nemůže zosobnit nebo odposlouchávat IP adresu jiného. TLS/SSL se používá při přístupu k Azure Storage nebo SQL databází, nebo při připojování ke cloudovým službám. V takovém případě je správce zákazníka zodpovědný za získání certifikátu TLS/SSL a jeho nasazení do infrastruktury klienta. Datový provoz mezi virtuálními počítači ve stejném nasazení nebo mezi klienty v jednom nasazení prostřednictvím virtuální sítě Microsoft Azure lze chránit prostřednictvím šifrovaných komunikačních protokolů, jako je HTTPS, SSL/TLS nebo jiné.

V závislosti na tom, jak jste odpověděli na otázky v [části Určit požadavky na ochranu dat](plan-hybrid-identity-design-considerations-dataprotection-requirements.md), byste měli být schopni určit, jak chcete chránit data a jak vám řešení hybridní identity může pomoci s tímto procesem. V následující tabulce jsou uvedeny možnosti podporované Azure, které jsou k dispozici pro každý scénář ochrany dat.

| Možnosti ochrany údajů | V klidu v cloudu | V klidu v místním prostředí | V tranzitu |
| --- | --- | --- | --- |
| BitLocker Drive Encryption |× |× | |
| SQL Server pro šifrování databází |× |× | |
| Šifrování mezi virtuálními virtuálními virtuálními body | | |× |
| SSL/TLS | | |× |
| Síť VPN | | |× |

> [!NOTE]
> Přečtěte si [dodržování předpisů podle funkce](https://azure.microsoft.com/support/trust-center/services/) na Webu Microsoft Azure Trust [Center,](https://azure.microsoft.com/support/trust-center/) abyste se dozvěděli více o certifikacích, které každá služba Azure splňuje.
> Vzhledem k tomu, že možnosti ochrany dat používají vícevrstvý přístup, porovnání mezi těmito možnostmi se pro tento úkol nevztahuje. Ujistěte se, že využíváte všechny možnosti, které jsou k dispozici pro každý stav dat.
>
>

## <a name="define-content-management-options"></a>Definování možností správy obsahu

Jednou z výhod použití Azure AD ke správě infrastruktury hybridní identity je, že proces je plně transparentní z pohledu koncového uživatele. Uživatel se pokusí o přístup ke sdílenému prostředku, prostředek vyžaduje ověření, uživatel musí odeslat požadavek na ověření do služby Azure AD, aby získal token a získal k prostředku přístup. Celý tento proces probíhá na pozadí, bez interakce s uživatelem. 

Organizace, které se obávají ochrany osobních údajů, obvykle vyžadují klasifikaci dat pro své řešení. Pokud jejich aktuální místní infrastruktura už používá klasifikaci dat, je možné použít Azure AD jako hlavní úložiště pro identitu uživatele. Běžný nástroj, který se používá místně pro klasifikaci dat se nazývá [Data Classification Toolkit](https://msdn.microsoft.com/library/Hh204743.aspx) pro Windows Server 2012 R2. Tento nástroj může pomoci identifikovat, klasifikovat a chránit data na souborových serverech v privátním cloudu. K provedení tohoto úkolu je také možné použít [automatickou klasifikaci souborů](https://technet.microsoft.com/library/hh831672.aspx) v systému Windows Server 2012.

Pokud vaše organizace nemá klasifikaci dat zavedenou, ale potřebuje chránit citlivé soubory bez nutnosti přidávat nové servery místně, může používat [službu](https://technet.microsoft.com/library/JJ585026.aspx)Microsoft Azure Rights Management Service .  Azure RMS používá zásady šifrování, identity a autorizace, které pomáhají zabezpečit vaše soubory a e-maily, a funguje na různých zařízeních – telefonech, tabletech a počítačích. Vzhledem k tomu, že Azure RMS je cloudová služba, není nutné explicitně konfigurovat vztahy důvěryhodnosti s jinými organizacemi, než s nimi budete moci sdílet chráněný obsah. Pokud už mají služby Office 365 nebo adresář Azure AD, je automaticky dostupná podpora spolupráce mezi organizacemi. Můžete také synchronizovat pouze atributy adresáře, které Azure RMS potřebuje k podpoře společné identity pro vaše místní účty Active Directory, pomocí služby Azure Active Directory Synchronizeization Services (AAD Sync) nebo Azure AD Connect.

Důležitou součástí správy obsahu je pochopit, kdo přistupuje ke kterému prostředku, proto je pro řešení správy identit důležitá funkce bohatého protokolování. Azure AD poskytuje protokolování za 30 dní, včetně:

* Změny členství v rolích (například: uživatel přidán do role globálního správce)
* Aktualizace pověření (například změny hesla)
* Správa domény (například ověření vlastní domény, odebrání domény)
* Přidání nebo odebrání aplikací
* Správa uživatelů (například přidání, odebrání, aktualizace uživatele)
* Přidání nebo odebrání licencí

> [!NOTE]
> Další informace o možnostech protokolování v Azure [načtete na správu protokolování](https://download.microsoft.com/download/B/6/C/B6C0A98B-D34A-417C-826E-3EA28CDFC9DD/AzureSecurityandAuditLogManagement_11132014.pdf) microsoft azure a auditu.
> V závislosti na tom, jak jste odpověděli na otázky v [části Určit požadavky na správu obsahu](plan-hybrid-identity-design-considerations-contentmgt-requirements.md), byste měli být schopni určit, jak chcete obsah spravovat v řešení hybridní identity. Zatímco všechny možnosti zveřejněné v tabulce 6 jsou schopné integrace s Azure AD, je důležité definovat, které jsou vhodnější pro vaše obchodní potřeby.
>
>

| Možnosti správy obsahu | Výhody | Nevýhody |
| --- | --- | --- |
| Centralizované místní (Server pro správu přístupových práv služby Active Directory) |Úplná kontrola nad serverovou infrastrukturou odpovědnou za klasifikaci dat <br> Integrované funkce v systému Windows Server, není třeba další licence nebo předplatného <br> Lze integrovat s Azure AD v hybridním scénáři <br> Podporuje funkce správy informačních práv (IRM) ve službách Microsoft Online, jako jsou Exchange Online a SharePoint Online, a také v Office 365. <br> Podporuje místní serverové produkty Microsoft, jako je Exchange Server, SharePoint Server a souborové servery, které provozují Windows Server a File Classification Infrastructure (FCI). |Vyšší údržba (držet krok s aktualizacemi, konfigurací a potenciálními upgrady), protože IT vlastní server <br> Vyžadovat místní serverovou infrastrukturu<br> Nevyužívá funkce Azure nativně |
| Centralizované v cloudu (Azure RMS) |Snadnější správa ve srovnání s místním řešením <br> Lze integrovat se službou AD DS v hybridním scénáři <br>  Plně integrovaná s Azure AD <br> K nasazení služby nevyžaduje místní server. <br> Podporuje místní serverové produkty Microsoft, jako je Exchange Server, SharePoint, Server a souborové servery se systémem Windows Server a Klasifikace souborů, infrastruktura (FCI) <br> IT, může mít úplnou kontrolu nad klíčem svého klienta s funkcí BYOK. |Vaše organizace musí mít cloudové předplatné, které podporuje službu RMS. <br> Vaše organizace musí mít adresář Azure AD pro podporu ověřování uživatelů pro SLUŽBU RMS. |
| Hybridní (Azure RMS integrovaný se serverem pro místní správu práv služby Active Directory) |Tento scénář shromažďuje výhody, centralizované místně a v cloudu. |Vaše organizace musí mít cloudové předplatné, které podporuje službu RMS. <br> Vaše organizace musí mít adresář Azure AD pro podporu ověřování uživatelů pro službu RMS, <br> Vyžaduje připojení mezi cloudovou službou Azure a místní infrastrukturou. |

## <a name="define-access-control-options"></a>Definování možností řízení přístupu
Využitím možností ověřování, autorizace a řízení přístupu, které jsou k dispozici ve službě Azure AD, můžete vaší společnosti povolit používat centrální úložiště identit a zároveň umožnit uživatelům a partnerům používat jednotné přihlašování (SSO), jak je znázorněno na následujícím obrázku:

![centralizovaná správa](./media/plan-hybrid-identity-design-considerations/centralized-management.png)

Centralizovaná správa a plná integrace s ostatními adresáři

Azure Active Directory poskytuje jednotné přihlašování k tisícům aplikací SaaS a místních webových aplikací. Další podrobnosti o třetí straně jednotného přihlašování, které byly testovány společností Microsoft, najdete v [seznamu kompatibility federace Azure Active Directory: zprostředkovatelé identit třetích stran, které lze použít k implementaci článku jednotného přihlašování.](how-to-connect-fed-compatibility.md) Tato funkce umožňuje organizaci implementovat různé scénáře B2B při zachování kontroly správy identity a přístupu. Však během procesu návrhu B2B je důležité pochopit metodu ověřování, která se používá partnera a ověřit, pokud je tato metoda podporována Azure. V současné době jsou podporovány následující metody Azure AD:

* Jazyk značek kontrolního výrazu zabezpečení (SAML)
* OAuth
* Kerberos
* Tokeny
* Certifikáty

> [!NOTE]
> Přečtěte si [protokoly Azure Active Directory Authentication Protocols,](https://msdn.microsoft.com/library/azure/dn151124.aspx) abyste se dozvěděli další podrobnosti o jednotlivých protokolech a jeho možnostech v Azure.
>
>

Pomocí podpory Azure AD, mobilní obchodní aplikace můžete použít stejné snadné nastavení mobilní služby ověřování, aby zaměstnanci mohli přihlásit do svých mobilních aplikací s jejich firemní pověření služby Active Directory. Díky této funkci je Azure AD podporována jako poskytovatel identity v mobilních službách spolu s dalšími poskytovateli identit, kteří už jsou podporovaní (mezi něž patří účty Microsoft, Facebook ID, Google ID a Twitter ID). Pokud místní aplikace používají přihlašovací údaje uživatele umístěné ve službě AD DS společnosti, měl by být přístup od partnerů a uživatelů přicházejících z cloudu transparentní. Můžete spravovat řízení podmíněného přístupu uživatele do (cloudových) webových aplikací, webového rozhraní API, cloudových služeb Microsoftu, aplikací SaaS třetích stran a nativních (mobilních) klientských aplikací a mít výhody zabezpečení, auditování a vykazování vše v jednom Místo. Doporučuje se však ověřit implementaci v neprodukčním prostředí nebo s omezeným počtem uživatelů.

> [!TIP]
> je důležité zmínit, že Azure AD nemá zásady skupiny jako AD DS má. Chcete-li vynutit zásady pro zařízení, potřebujete řešení pro správu mobilních zařízení, jako je [Microsoft Intune](https://technet.microsoft.com/library/jj676587.aspx).
>
>

Jakmile je uživatel ověřen pomocí Azure AD, je důležité vyhodnotit úroveň přístupu, který má uživatel. Úroveň přístupu, kterou má uživatel k prostředku, se může lišit. Zatímco Azure AD můžete přidat další vrstvu zabezpečení řízením přístupu k některým prostředkům, mějte na paměti, že prostředek sám může mít také svůj vlastní seznam řízení přístupu samostatně, jako je například řízení přístupu pro soubory umístěné na souborovém serveru. Následující obrázek shrnuje úrovně řízení přístupu, které můžete mít v hybridním scénáři:

![řízení přístupu](./media/plan-hybrid-identity-design-considerations/accesscontrol.png)

Každá interakce v diagramu zobrazené na obrázku X představuje jeden scénář řízení přístupu, který může být pokryta Azure AD. Níže máte popis každého scénáře:

1. Podmíněný přístup k aplikacím, které jsou hostované místně: Pro aplikace, které jsou nakonfigurované pro použití služby AD FS se systémem Windows Server 2012 R2, můžete používat registrovaná zařízení s zásadami přístupu.

2. Řízení přístupu na portál Azure: Azure také umožňuje řídit přístup k portálu pomocí řízení přístupu na základě rolí (RBAC)). Tato metoda umožňuje společnosti omezit počet operací, které jednotlivec může provést na webu Azure Portal. Pomocí RBAC k řízení přístupu k portálu mohou správci IT delegovat přístup pomocí následujících přístupů pro správu přístupu:

   - Přiřazení rolí na základě skupiny: Můžete přiřadit přístup ke skupinám Azure AD, které lze synchronizovat z místní služby Active Directory. To vám umožní využít stávající investice, které vaše organizace provedla v nástrojích a procesech pro správu skupin. Můžete také použít funkci správy delegované skupiny Azure AD Premium.
   - Použijte předdefinované role v Azure: Můžete použít tři role – vlastníka, přispěvatele a čtenáře, abyste zajistili, že uživatelé a skupiny budou mít oprávnění k pouze úlohám, které potřebují ke své práci.
   -  Podrobný přístup k prostředkům: Můžete přiřadit role uživatelům a skupinám pro konkrétní předplatné, skupinu prostředků nebo jednotlivé prostředky Azure, jako je web nebo databáze. Tímto způsobem můžete zajistit, aby uživatelé měli přístup ke všem prostředkům, které potřebují, a žádný přístup k prostředkům, které nepotřebují spravovat.

   > [!NOTE]
   > Pokud vytváříte aplikace a chcete přizpůsobit řízení přístupu pro ně, je také možné použít role aplikací Azure AD pro autorizaci. Projděte si tento [příklad WebApp-RoleClaims-DotNet](https://github.com/AzureADSamples/WebApp-RoleClaims-DotNet) o tom, jak vytvořit aplikaci pro použití této funkce.


3. Podmíněný přístup pro aplikace Office 365 s Microsoft Intune: Správci IT můžou zřit zásady zařízení podmíněného přístupu k zabezpečení podnikových prostředků a zároveň umožnit pracovníkům s informacemi na kompatibilních zařízeních přístup ke službám. 
  
4. Podmíněný přístup pro aplikace Saas: [Tato funkce](https://cloudblogs.microsoft.com/enterprisemobility/2015/06/25/azure-ad-conditional-access-preview-update-more-apps-and-blocking-access-for-users-not-at-work/) umožňuje konfigurovat pravidla přístupu k vícefaktorovému ověřování pro aplikaci a možnost blokovat přístup pro uživatele, kteří nejsou v důvěryhodné síti. Pravidla vícefaktorového ověřování můžete použít pro všechny uživatele, kteří jsou přiřazeni k aplikaci, nebo pouze pro uživatele v rámci určených skupin zabezpečení. Uživatelé mohou být vyloučeni z požadavku vícefaktorového ověřování, pokud přistupují k aplikaci z adresy IP, která je v síti organizace.

Vzhledem k tomu, že možnosti pro řízení přístupu používají vícevrstvý přístup, porovnání mezi těmito možnostmi nejsou pro tento úkol použitelné. Ujistěte se, že využíváte všechny možnosti, které jsou k dispozici pro každý scénář, který vyžaduje řízení přístupu k prostředkům.

## <a name="define-incident-response-options"></a>Definování možností reakce na incident
Azure AD můžete pomoci IT k identifikaci potenciálních bezpečnostních rizik v prostředí sledováním aktivity uživatele. ODDĚLENÍ IT můžou pomocí sestav přístupu a využití Azure AD získat přehled o integritě a zabezpečení adresáře vaší organizace. Pomocí těchto informací může správce IT lépe určit, kde mohou spočívat bezpečnostní rizika, aby mohl odpovídajícím způsobem plánovat zmírnění těchto rizik.  [Předplatné Azure AD Premium](../fundamentals/active-directory-get-started-premium.md) má sadu sestav zabezpečení, které můžou it služby povolit k získání těchto informací. [Sestavy Azure AD](../reports-monitoring/overview-reports.md) jsou rozděleny do kategorií takto:

* **Sestavy anomálií**: Obsahují události přihlášení, které byly shledány jako anomální. Cílem je, abyste si uvědomili takovou aktivitu a umožnili vám určit, zda je událost podezřelá.
* **Sestava integrovaných aplikací**: Poskytuje přehled o tom, jak se cloudové aplikace používají ve vaší organizaci. Azure Active Directory nabízí integraci s tisíci cloudových aplikací.
* **Zprávy o chybách**: Označte chyby, ke kterým může dojít při zřizování účtů do externích aplikací.
* **Sestavy specifické pro uživatele**: Zobrazení dat aktivity zařízení/přihlášení pro konkrétního uživatele.
* **Protokoly aktivit**: Obsahují záznam všech auditovaných událostí za posledních 24 hodin, posledních 7 dní nebo posledních 30 dní, stejně jako změny aktivity skupiny a aktivity resetování hesla a registrace.

> [!TIP]
> Další sestavou, která může také pomoci týmu reakce na incident, který pracuje na případu, je [uživatel se zprávou o leaked credentials.](https://cloudblogs.microsoft.com/enterprisemobility/2015/06/15/azure-active-directory-premium-reporting-now-detects-leaked-credentials/) Tato sestava zobrazí všechny shody mezi seznam emitovaných přihlašovacích údajů a tenanta.
>


Další důležité předdefinované sestavy ve službě Azure AD, které lze použít během vyšetřování reakce na incidenty a jsou:

* **Aktivita pro resetování hesla**: poskytněte správci přehled o tom, jak se v organizaci aktivně používá resetování hesla.
* **Aktivita registrace obnovení hesla**: poskytuje přehled o tom, kteří uživatelé zaregistrovali své metody pro resetování hesla a které metody vybrali.
* **Aktivita skupiny**: Poskytuje historii změn ve skupině (např. přidaných nebo odebraných uživatelů), které byly zahájeny na přístupovém panelu.

Kromě základní možnosti vytváření sestav Azure AD Premium, které můžete použít během procesu vyšetřování reakce na incident, it můžete také využít zprávy o auditu k získání informací, jako jsou:

* Změny členství v rolích (například uživatel přidaný do role globálního správce)
* Aktualizace pověření (například změny hesla)
* Správa domény (například ověření vlastní domény, odebrání domény)
* Přidání nebo odebrání aplikací
* Správa uživatelů (například přidání, odebrání, aktualizace uživatele)
* Přidání nebo odebrání licencí

Vzhledem k tomu, že možnosti pro reakci na incidenty používají vícevrstvý přístup, porovnání mezi těmito možnostmi se pro tento úkol nepoužije. Ujistěte se, že využíváte všechny možnosti, které jsou k dispozici pro každý scénář, který vyžaduje, abyste používali funkci vykazování Azure AD jako součást procesu reakce na incidenty vaší společnosti.

## <a name="next-steps"></a>Další kroky
[Určení úloh správy hybridní identity](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)

## <a name="see-also"></a>Viz také
[Přehled aspekty návrhu](plan-hybrid-identity-design-considerations-overview.md)
