---
title: Návrh hybridní identity – strategie ochrany dat Azure | Microsoft Docs
description: Strategii ochrany dat pro vaše řešení hybridní identity definujete tak, aby splňovala vámi definované podnikové požadavky.
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
ms.openlocfilehash: bac3f53def6db1038a6dd7e45d7933daa22df9f0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98703848"
---
# <a name="define-data-protection-strategy-for-your-hybrid-identity-solution"></a>Definování strategie ochrany dat pro vaše řešení hybridní identity
V této úloze definujete strategii ochrany dat pro vaše řešení hybridní identity, aby splňovala obchodní požadavky, které jste definovali v nástroji:

* [Určení požadavků na ochranu dat](plan-hybrid-identity-design-considerations-dataprotection-requirements.md)
* [Určení požadavků na správu obsahu](plan-hybrid-identity-design-considerations-contentmgt-requirements.md)
* [Určení požadavků na řízení přístupu](plan-hybrid-identity-design-considerations-accesscontrol-requirements.md)
* [Určení požadavků na reakce na incidenty](plan-hybrid-identity-design-considerations-incident-response-requirements.md)

## <a name="define-data-protection-options"></a>Definovat možnosti ochrany dat
Jak je vysvětleno v tématu [Určení požadavků na synchronizaci adresářů](plan-hybrid-identity-design-considerations-directory-sync-requirements.md), Microsoft Azure AD se mohou synchronizovat s místními Active Directory Domain Services (služba AD DS). Tato integrace umožňuje organizacím pomocí Azure AD ověřovat přihlašovací údaje uživatelů při pokusu o přístup k podnikovým prostředkům. To můžete udělat pro oba scénáře: místní data v místním prostředí i v cloudu. Přístup k datům ve službě Azure AD vyžaduje ověření uživatele prostřednictvím služby tokenu zabezpečení (STS).

Po ověření se hlavní název uživatele (UPN) přečte z ověřovacího tokenu. Pak autorizační systém určí replikovaný oddíl a kontejner odpovídající doméně uživatele. Informace o existenci, povoleném stavu a roli uživatele pak pomáhají autorizačnímu systému zjistit, jestli je pro uživatele v této relaci autorizovaný přístup k cílovému tenantovi. Některé autorizované akce (konkrétně vytvořit uživatele a resetování hesla) vytvoří záznam pro audit, který správce klienta potom použije ke správě úsilí nebo vyšetřování dodržování předpisů.

Přesouvání dat z místního datacentra do Azure Storage přes připojení k internetu nemusí být vždy proveditelné kvůli objemu dat, dostupnosti šířky pásma nebo jiným hlediskům. [Služba Azure Storage import/export](../../import-export/storage-import-export-service.md) nabízí hardwarovou možnost pro vkládání a načítání velkých objemů dat v úložišti objektů BLOB. Umožňuje posílat jednotky pevného disku [šifrované bitlockerem](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn306081(v=ws.11)#BKMK_BL2012R2) přímo do datového centra Azure, kde operátoři cloudu odesílají obsah do svého účtu úložiště, nebo si můžou stáhnout vaše data Azure na vaše jednotky a vrátit se na vás. Pro tento proces jsou přijímány pouze šifrované disky (pomocí klíče nástroje BitLocker vygenerovaného službou samotný během nastavení úlohy). Klíč BitLockeru se poskytuje pro Azure samostatně, čímž se zajišťuje sdílení klíčů od sebe.

Vzhledem k tomu, že data v přenosech můžou probíhat v různých scénářích, je také důležité znát, že Microsoft Azure používá [virtuální sítě](https://azure.microsoft.com/documentation/services/virtual-network/) k izolaci provozu klientů od sebe, což využívá míry, jako jsou brány firewall na úrovni hostitele a hosta, filtrování paketů IP, blokování portů a koncové body https. Většina interní komunikace v Azure, včetně infrastruktury pro infrastrukturu a z infrastruktury na zákazníka (v místním prostředí), je ale taky šifrovaná. Dalším důležitým scénářem je komunikace v datových centrech Azure. Společnost Microsoft spravuje sítě, aby se zajistilo, že žádný virtuální počítač nemůže zosobnit nebo eavesdrop na IP adrese jiného. TLS/SSL se používá při přístupu k databázím Azure Storage nebo SQL nebo při připojení k Cloud Services. V takovém případě vám správce zákazníka zodpovídá za získání certifikátu TLS/SSL a jeho nasazení do své klientské infrastruktury. Přenos dat mezi Virtual Machines ve stejném nasazení nebo mezi klienty v jednom nasazení prostřednictvím Microsoft Azure Virtual Network se dá chránit prostřednictvím šifrovaných komunikačních protokolů, jako jsou HTTPS, SSL/TLS nebo jiné.

V závislosti na tom, jak jste odpověděli na otázky v části [Určení požadavků na ochranu dat](plan-hybrid-identity-design-considerations-dataprotection-requirements.md), byste měli být schopní určit, jak chcete chránit vaše data a jak vám řešení hybridní identity může s tímto procesem pomoct. Následující tabulka ukazuje možnosti podporované v Azure, které jsou k dispozici pro jednotlivé scénáře ochrany dat.

| Možnosti ochrany dat | V klidovém umístění v cloudu | Místní místní umístění | Při přenosu |
| --- | --- | --- | --- |
| BitLocker Drive Encryption |× |× | |
| SQL Server k šifrování databází |× |× | |
| Šifrování z virtuálního počítače do virtuálního počítače | | |× |
| SSL/TLS | | |× |
| Síť VPN | | |× |

> [!NOTE]
> Načtěte si [dodržování předpisů podle funkcí](https://azure.microsoft.com/support/trust-center/services/) v [centru pro Microsoft Azure zabezpečení](https://azure.microsoft.com/support/trust-center/) a zjistěte více o certifikaci, se kterými je každá služba Azure kompatibilní.
> Vzhledem k tomu, že možnosti ochrany dat používají Multilayer přístup, není pro tento úkol možné porovnat tyto možnosti. Ujistěte se, že využijete všechny možnosti, které jsou k dispozici pro každý stav dat.
>
>

## <a name="define-content-management-options"></a>Definování možností správy obsahu

Jednou z výhod používání Azure AD ke správě hybridní infrastruktury identit je, že proces je plně transparentní z pohledu koncového uživatele. Uživatel se pokusí o přístup ke sdílenému prostředku, prostředek vyžaduje ověření. uživatel musí odeslat žádost o ověření do služby Azure AD, aby získal token a měl přístup k prostředku. K tomuto celému procesu dochází na pozadí bez zásahu uživatele. 

Organizace, které mají obavy týkající se ochrany osobních údajů dat, obvykle vyžadují klasifikaci dat pro jejich řešení. Pokud již stávající místní infrastruktura používá klasifikaci dat, je možné použít Azure AD jako hlavní úložiště pro identitu uživatele. Běžným nástrojem, který se používá v místním prostředí pro klasifikaci dat, se říká [Sada nástrojů klasifikace dat](/previous-versions/tn-archive/hh204743(v=technet.10)) pro Windows Server 2012 R2. Tento nástroj vám může pomáhat identifikovat, klasifikovat a chránit data na souborových serverech v privátním cloudu. K provedení této úlohy je také možné použít [automatickou klasifikaci souborů](/windows-server/identity/solution-guides/deploy-automatic-file-classification--demonstration-steps-) ve Windows Serveru 2012.

Pokud vaše organizace nemá zavedenu klasifikaci dat, ale potřebuje chránit citlivé soubory bez nutnosti přidávat nové servery místně, můžou použít [službu Microsoft Azure Rights Management](/azure/information-protection/what-is-azure-rms).  Azure RMS používá zásady šifrování, identity a autorizace k zabezpečení souborů a e-mailů a funguje na různých zařízeních – na telefonech, tabletech a počítačích. Vzhledem k tomu, že Azure RMS je cloudová služba, není potřeba explicitně konfigurovat vztahy důvěryhodnosti s jinými organizacemi, abyste s nimi mohli sdílet chráněný obsah. Pokud již mají Microsoft 365 nebo adresář služby Azure AD, je spolupráce v organizacích automaticky podporována. Můžete také synchronizovat pouze atributy adresáře, které Azure RMS potřebují k podpoře společné identity pro místní účty služby Active Directory, a to pomocí služby Azure Active Directory Synchronization Services (Azure AD Sync) nebo Azure AD Connect.

Důležitou součástí správy obsahu je pochopení toho, kdo přistupuje k danému prostředku, proto je pro řešení správy identit důležité bohatou možnost protokolování. Azure AD poskytuje protokol po dobu 30 dnů, včetně:

* Změny členství v rolích (např. uživatel přidaný do role globálního správce)
* Aktualizace přihlašovacích údajů (např. změny hesla)
* Správa domén (např. ověření vlastní domény, odebrání domény)
* Přidávání nebo odebírání aplikací
* Správa uživatelů (např. Přidání, odebrání a aktualizace uživatele)
* Přidání nebo odebrání licencí

> [!NOTE]
> Další informace o možnostech protokolování v Azure najdete v [Microsoft Azure Security and Audit správě protokolů](https://download.microsoft.com/download/B/6/C/B6C0A98B-D34A-417C-826E-3EA28CDFC9DD/AzureSecurityandAuditLogManagement_11132014.pdf) .
> V závislosti na tom, jak jste odpověděli na otázky v části [Určení požadavků na správu obsahu](plan-hybrid-identity-design-considerations-contentmgt-requirements.md), byste měli být schopní určit, jak se má obsah spravovat ve vašem řešení hybridní identity. I když jsou všechny možnosti zveřejněné v tabulce 6 schopné integraci s Azure AD, je důležité definovat, který z nich je vhodnější pro vaše obchodní potřeby.
>
>

| Možnosti správy obsahu | Výhody | Nevýhody |
| --- | --- | --- |
| Centralizované místní (Active Directory Rights Management Server) |Úplná kontrola nad infrastrukturou serveru odpovědnou za klasifikaci dat <br> Integrovaná funkce v systému Windows Server, nevyžaduje se licence nebo předplatné navíc <br> V hybridním scénáři se dá integrovat s Azure AD. <br> Podporuje funkce IRM (Správa přístupových práv k informacím) v online službách Microsoftu, jako je Exchange Online a SharePoint Online, i Microsoft 365 <br> Podporuje místní serverové produkty společnosti Microsoft, například server Exchange Server, SharePoint Server a souborové servery se systémem Windows Server a infrastrukturou klasifikace souborů (FCI). |Vyšší Údržba (průběžně s aktualizacemi, konfigurací a potenciálními upgrady), vzhledem k tomu, že je vlastníkem serveru <br> Vyžadovat místní serverovou infrastrukturu<br> Nevyužívá nativně možnosti Azure |
| Centralizované v cloudu (Azure RMS) |Snazší správa ve srovnání s místním řešením <br> Dá se integrovat s služba AD DS v hybridním scénáři. <br>  Plně integrovaná se službou Azure AD <br> Pro nasazení služby nevyžaduje místní server. <br> Podporuje místní serverové produkty Microsoftu, jako jsou Exchange Server, SharePoint, server a souborové servery, na kterých běží Windows Server a klasifikace souborů, infrastruktura (FCI). <br> Může mít úplnou kontrolu nad klíčem svého tenanta pomocí funkce BYOK. |Vaše organizace musí mít cloudové předplatné, které podporuje RMS. <br> Aby mohla organizace podporovat ověřování uživatelů služby RMS, musí mít adresář Azure AD. |
| Hybrid (Azure RMS integrovaný s, místní Active Directory Rights Management Server) |Tento scénář shromažďuje výhody obou, centralizovaných místně i v cloudu. |Vaše organizace musí mít cloudové předplatné, které podporuje RMS. <br> Aby mohla organizace podporovat ověřování uživatelů služby RMS, musí mít adresář Azure AD. <br> Vyžaduje připojení mezi cloudovou službou Azure a místní infrastrukturou. |

## <a name="define-access-control-options"></a>Definování možností řízení přístupu
Využitím možností ověřování, autorizace a řízení přístupu, které jsou k dispozici v Azure AD, můžete společnosti povolit, aby používala centrální úložiště identit, a současně umožňuje uživatelům a partnerům používat jednotné přihlašování (SSO), jak je znázorněno na následujícím obrázku:

![Centralizovaná správa](./media/plan-hybrid-identity-design-considerations/centralized-management.png)

Centralizovaná správa a plná integrace s ostatními adresáři

Azure Active Directory poskytuje jednotné přihlašování k tisícům aplikací SaaS a místních webových aplikací. Další informace o jednotném přihlašování, které byly testovány společností Microsoft, najdete v [seznamu Azure Active Directory kompatibilita federace: poskytovatelé identit třetích stran, které se dají použít k implementaci jednotného přihlašování](how-to-connect-fed-compatibility.md) . Tato funkce umožňuje organizaci implementovat celou řadu scénářů B2B a přitom udržet kontrolu nad správou identit a přístupu. Během procesu návrhu B2B je ale důležité pochopit metodu ověřování, kterou partner používá, a ověřit, jestli je tato metoda podporovaná Azure. Služba Azure AD v současné době podporuje následující metody:

* Security Assertion Markup Language (SAML)
* OAuth
* Kerberos
* Tokeny
* Certifikáty

> [!NOTE]
> Pokud chcete získat další podrobnosti o každém protokolu a jeho schopnostech v Azure, přečtěte si [Azure Active Directory ověřovací protokoly](/previous-versions/azure/dn151124(v=azure.100)) .
>
>

Díky podpoře Azure AD můžou mobilní obchodní aplikace používat stejné možnosti snadného Mobile Services ověřování, které zaměstnancům umožňují přihlašovat se k mobilním aplikacím pomocí svých podnikových přihlašovacích údajů služby Active Directory. S touto funkcí se Azure AD podporuje jako poskytovatel identity v Mobile Services společně s ostatními zprostředkovateli identity, kteří už jsou podporovaní (včetně účtů Microsoft, Facebooku ID, Google ID a Twitteru). Pokud místní aplikace používají přihlašovací údaje uživatele nacházející se v služba AD DS společnosti, musí být přístup od partnerů a uživatelů pocházejících z cloudu transparentní. Řízení podmíněného přístupu uživatele můžete spravovat na (cloudové) webové aplikace, webové rozhraní API, cloudové služby Microsoftu, aplikace SaaS třetích stran a nativní (mobilní) klientské aplikace a využívat výhody zabezpečení, auditování a vytváření sestav na jednom místě. Doporučuje se ale ověřit implementaci v neprodukčním prostředí nebo s omezeným počtem uživatelů.

> [!TIP]
> je důležité uvést, že služba Azure AD nemá Zásady skupiny jako služba AD DS. Aby bylo možné vyhovět zásadám pro zařízení, potřebujete řešení pro správu mobilních zařízení, například [Microsoft Intune](/mem/intune/).
>
>

Po ověření uživatele pomocí Azure AD je důležité vyhodnotit úroveň přístupu, kterou má uživatel. Úroveň přístupu, kterou má uživatel nad prostředkem, se může lišit. Zatímco Azure AD může přidat další vrstvu zabezpečení řízením přístupu k některým prostředkům, pamatujte na to, že samotný prostředek může mít také vlastní seznam řízení přístupu samostatně, jako je například řízení přístupu pro soubory umístěné na souborovém serveru. Následující obrázek shrnuje úrovně řízení přístupu, které můžete mít v hybridním scénáři:

![řízení přístupu](./media/plan-hybrid-identity-design-considerations/accesscontrol.png)

Každá interakce v diagramu znázorněná na obrázku X představuje jeden scénář řízení přístupu, který může být pokrytý službou Azure AD. Pod popisem jednotlivých scénářů:

1. Podmíněný přístup k aplikacím, které jsou hostované místně: u registrovaných zařízení můžete použít zásady přístupu pro aplikace, které jsou nakonfigurované pro použití AD FS s Windows Serverem 2012 R2.

2. Access Control k Azure Portal: Azure vám taky umožní řídit přístup k portálu pomocí řízení přístupu na základě role Azure (Azure RBAC)). Tato metoda umožňuje společnosti omezit počet operací, které může jednotlivec dělat v Azure Portal. Pomocí služby Azure RBAC pro řízení přístupu k portálu mohou správci IT delegovat přístup pomocí následujících přístupů ke správě přístupu:

   - Přiřazení role na základě skupin: můžete přiřadit přístup ke skupinám Azure AD, které se dají synchronizovat z vaší místní služby Active Directory. Díky tomu můžete využít stávající investice, které vaše organizace provedla při správě skupin a jejich zpracování. Můžete také použít funkci delegovaná Správa skupin Azure AD Premium.
   - Použijte předdefinované role v Azure: můžete použít tři role – vlastník, přispěvatel a čtenář, abyste zajistili, že uživatelé a skupiny mají oprávnění dělat jenom úkoly, které potřebují ke své práci.
   -  Podrobný přístup k prostředkům: role můžete přiřadit uživatelům a skupinám pro konkrétní předplatné, skupinu prostředků nebo jednotlivý prostředek Azure, jako je například web nebo databáze. Tímto způsobem můžete zajistit, že uživatelé budou mít přístup ke všem prostředkům, které potřebují, a nemají přístup k prostředkům, které nepotřebují spravovat.

   > [!NOTE]
   > Pokud vytváříte aplikace a chcete pro ně přizpůsobit řízení přístupu, je také možné použít aplikační role Azure AD pro autorizaci. Přečtěte si tento [příklad WebApp-RoleClaims-dotnet](https://github.com/AzureADSamples/WebApp-RoleClaims-DotNet) , jak sestavit aplikaci pro použití této funkce.


3. Podmíněný přístup pro Microsoft 365 aplikace s Microsoft Intune: Správci IT můžou zřídit zásady pro zařízení podmíněného přístupu pro zabezpečení podnikových prostředků, ale zároveň umožnit pracovníkům s informacemi o vyhovujících zařízeních přístup ke službám. 
  
4. Podmíněný přístup pro aplikace SaaS: [Tato funkce](https://cloudblogs.microsoft.com/enterprisemobility/2015/06/25/azure-ad-conditional-access-preview-update-more-apps-and-blocking-access-for-users-not-at-work/) umožňuje konfigurovat pravidla přístupu k Multi-Factor Authentication pro jednotlivé aplikace a možnost blokovat přístup pro uživatele, kteří nejsou v důvěryhodné síti. Pravidla služby Multi-Factor Authentication můžete použít pro všechny uživatele, kteří jsou přiřazeni k aplikaci nebo pouze pro uživatele v rámci zadaných skupin zabezpečení. Pokud uživatelé přistupují k aplikaci z IP adresy, která je v síti organizace, mohou být z požadavku služby Multi-Factor Authentication vyloučeni.

Vzhledem k tomu, že možnosti pro řízení přístupu používají přístup Multilayer, není pro tento úkol možné porovnat tyto možnosti. Ujistěte se, že používáte všechny možnosti dostupné pro každý scénář, který vyžaduje řízení přístupu k prostředkům.

## <a name="define-incident-response-options"></a>Definování možností reakce na incidenty
Azure AD může IT oddělení pomoci s potenciálním zabezpečením identit v prostředí pomocí sledování aktivity uživatele. K získání přehledu o integritě a zabezpečení adresáře vaší organizace může použít sestavy přístupu a využití Azure AD. S těmito informacemi může správce IT lépe určit, kde můžou být potenciální bezpečnostní rizika, aby mohli přiměřeně naplánovat zmírnění těchto rizik.  [Azure AD Premium předplatné](../fundamentals/active-directory-get-started-premium.md) obsahuje sadu sestav zabezpečení, které jim umožní získat tyto informace. [Sestavy služby Azure AD](../reports-monitoring/overview-reports.md) jsou zařazené do kategorií takto:

* **Sestavy anomálií**: obsahují události přihlášení, které byly nalezeny pro neobvyklé. Cílem je, abyste věděli o takové činnosti a umožnili jste se určit, jestli je událost podezřelá.
* **Sestava integrované aplikace**: poskytuje přehled o tom, jak se ve vaší organizaci používají cloudové aplikace. Azure Active Directory nabízí integraci s tisíci cloudových aplikací.
* **Zprávy o chybách**: uveďte chyby, ke kterým může dojít při zřizování účtů externích aplikací.
* **Sestavy specifické pro uživatele**: zobrazení dat o aktivitách zařízení nebo přihlášení pro konkrétního uživatele
* **Protokoly aktivit**: obsahují záznam všech auditovaných událostí za posledních 24 hodin, posledních 7 dní nebo posledních 30 dní, jakož i změny aktivity skupiny a resetování hesla a aktivity registrace.

> [!TIP]
> Další sestava, která může také týmu reakcí na incidenty pracovat na případu, je [uživatel s nevrácenými](https://cloudblogs.microsoft.com/enterprisemobility/2015/06/15/azure-active-directory-premium-reporting-now-detects-leaked-credentials/) zprávami pověření. Tato sestava rozsvítí všechny shody mezi nevráceným seznamem přihlašovacích údajů a vaším klientem.
>


Další důležité předdefinované sestavy v Azure AD, které se dají použít během vyšetřování reakce na incidenty a jsou:

* **Aktivita resetování hesla**: poskytněte správci přehledy o tom, jak se v organizaci používá aktivní resetování hesla.
* Přihlašovací **aktivita pro resetování hesla**: poskytuje přehled o tom, kteří uživatelé zaregistrovali své metody pro resetování hesla a které metody jsou vybrané.
* **Aktivita skupiny**: poskytuje historii změn skupiny (např. přidaných nebo odebraných uživatelů), které byly iniciovány na přístupovém panelu.

Kromě základních funkcí vytváření sestav Azure AD Premium, které můžete použít během procesu vyšetřování reakce na incidenty, může také využít sestavy auditu k získání informací, jako jsou například:

* Změny členství v rolích (například uživatel přidaný do role globálního správce)
* Aktualizace přihlašovacích údajů (například změny hesla)
* Správa domén (například ověření vlastní domény, odebrání domény)
* Přidávání nebo odebírání aplikací
* Správa uživatelů (například přidání, odebrání a aktualizace uživatele)
* Přidání nebo odebrání licencí

Vzhledem k tomu, že možnosti pro reakci na incidenty používají přístup Multilayer, není pro tento úkol možné porovnat tyto možnosti. Ujistěte se, že využijete všechny možnosti dostupné pro každý scénář, který vyžaduje, abyste jako součást procesu reakce na incident vaší společnosti používali funkci vytváření sestav Azure AD.

## <a name="next-steps"></a>Další kroky
[Určení úloh správy hybridních identit](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)

## <a name="see-also"></a>Viz také
[Přehled otázek návrhu](plan-hybrid-identity-design-considerations-overview.md)