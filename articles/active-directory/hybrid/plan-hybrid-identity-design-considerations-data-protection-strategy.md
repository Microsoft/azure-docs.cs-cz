---
title: Hybridní identita Návrh – strategie ochrany dat Azure | Dokumentace Microsoftu
description: Můžete definovat strategie ochrany dat pro hybridní řešení identit plnění obchodních požadavků, které jste definovali.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: e76fd1f4-340a-492a-84d9-e05f3b7cc396
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/13/2017
ms.component: hybrid
ms.author: billmath
ms.custom: seohack1
ms.openlocfilehash: bef5671637b26f4fd0d0c27211edca946c395cdf
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54468552"
---
# <a name="define-data-protection-strategy-for-your-hybrid-identity-solution"></a>Definování strategie ochrany dat pro vaše řešení hybridní identity
V této úloze budete definovat strategie ochrany dat pro vaše řešení hybridní identity plnění obchodních požadavků, který jste definovali v:

* [Určení požadavků na ochranu dat](plan-hybrid-identity-design-considerations-dataprotection-requirements.md)
* [Určení požadavků na správu obsahu](plan-hybrid-identity-design-considerations-contentmgt-requirements.md)
* [Určete požadavky na řízení přístupu](plan-hybrid-identity-design-considerations-accesscontrol-requirements.md)
* [Určení požadavků na reakce na incidenty](plan-hybrid-identity-design-considerations-incident-response-requirements.md)

## <a name="define-data-protection-options"></a>Definování možností ochrany dat
Jak je vysvětleno v [určit požadavky na synchronizaci adresáře](plan-hybrid-identity-design-considerations-directory-sync-requirements.md), Microsoft Azure AD můžete synchronizovat s vaší místní Active Directory Domain Services (AD DS). Tato integrace umožňuje organizacím pomocí služby Azure AD k ověření přihlašovacích údajů uživatelů, když se snaží získat přístup k firemním prostředkům. Můžete to provést pro oba scénáře: data na rest v místním prostředí i v cloudu. Přístup k datům ve službě Azure AD vyžaduje ověřování uživatelů pomocí služby tokenů zabezpečení (STS).

Po ověření, hlavní název uživatele (UPN) je pro čtení z ověřovací token. Pak systém autorizace určuje, replikované oddílu a kontejner odpovídající doméně uživatele. Informace o existenci, povoleného stavu a role uživatele pak pomáhá určit, jestli je přístup k cílovému tenantovi oprávnění pro uživatele v této relaci systému autorizace. Určité akce oprávnění (konkrétně vytvořte uživatele a resetováním hesla) vytvořil záznam pro audit, který správce tenanta se pak používá ke správě snahu o dodržování předpisů nebo vyšetřování.

Přesouvá data z místního datacentra do služby Azure Storage prostřednictvím připojení k Internetu nemusí být vždy proveditelné kvůli objemu dat, dostupnost šířky pásma nebo další důležité informace. [Služby Import/Export úložiště Azure](../../storage/common/storage-import-export-service.md) poskytuje možnost založené na hardwaru pro uvádění/načítání velkých objemů dat v úložišti objektů blob. Umožňuje odeslat [šifrované nástrojem BitLocker](https://technet.microsoft.com/library/dn306081#BKMK_BL2012R2) pevných disků přímo na adresu datacentra Azure, kde operátorům cloudu nahrajte obsah do účtu úložiště, nebo vaše data Azure můžou stahovat pro vaše jednotky vrátí. Pouze šifrovanými disky jsou přijati pro tento proces (pomocí klíče Bitlockeru generovaných během instalace úlohy samotné služby). Klíč nástroje BitLocker je k dispozici do Azure samostatně, díky tomu mimo pásmo klíč sdílení.

Protože přenášených dat může proběhnout v různých scénářích, je také důležité vědět, že Microsoft Azure používá [virtuální sítě](https://azure.microsoft.com/documentation/services/virtual-network/) izolovat provoz klientů z jiné, použití opatření, jako je například hostitele a hostovaného úroveň brány firewall, filtrování protokolu IP paketu, port blokování a koncové body HTTPS. Většina interních komunikací Azure, včetně infrastruktury infrastruktury a infrastruktury zákazníka (v místním prostředí), ale je také šifrovaný. Jiný scénář odstranění důležitý je komunikace v rámci Datacenter Azure; Sítě, aby zajistil, že žádný virtuální počítač můžete vydávat se za nebo tajně poslouchat na IP adresu jiného spravuje Microsoft. Protokol TLS/SSL se používá při přístupu k Azure Storage nebo Azure SQL Database nebo při připojení ke Cloudovým službám. Správci zákazníka v tomto případě zodpovídá za získání certifikátu TLS/SSL a nasadit ho do svého tenanta infrastruktury. Přesun provozu dat mezi virtuálními počítači ve stejném nasazení nebo mezi tenanty v jednom nasazení prostřednictvím Microsoft Azure Virtual Network se dá chránit prostřednictvím šifrované komunikační protokoly, jako je například HTTPS, SSL/TLS nebo jiné.

V závislosti na tom, jak jste odpověděli na otázky v [určit požadavky na ochranu dat](plan-hybrid-identity-design-considerations-dataprotection-requirements.md), by měl být schopní určit, jak chcete chránit vaše data a jak hybridní řešení identit vám mohou pomoci tento proces. Následující tabulka ukazuje možnosti podporované v Azure, které jsou k dispozici pro každý scénář ochrany dat.

| Možnosti ochrany dat | V klidovém stavu uložených v cloudu | V rozhraní rest místní | Při přenosu |
| --- | --- | --- | --- |
| Šifrování jednotky nástrojem BitLocker |X |X | |
| SQL Server k šifrování databází |X |X | |
| Šifrování virtuálního počítače na virtuální počítač | | |X |
| SSL/TLS | | |X |
| Síť VPN | | |X |

> [!NOTE]
> Čtení [dodržování předpisů u funkcí](https://azure.microsoft.com/support/trust-center/services/) na [Microsoft Azure Trust Center](https://azure.microsoft.com/support/trust-center/) pro další informace o certifikacích, které jsou kompatibilní s jednotlivé služby Azure.
> Protože možnosti ochrany dat použít s více vrstvami přístup, se nedají použít pro tuto úlohu porovnání mezi těmito možnostmi. Ujistěte se, že se využívá pro každý stav dat k dispozici všechny možnosti.
>
>

## <a name="define-content-management-options"></a>Definování možností správy obsahu

Jednou z výhod používání služby Azure AD ke správě hybridní infrastrukturu identit je, že proces je zcela transparentní z hlediska koncového uživatele. Uživatel pokusí získat přístup ke sdílenému prostředku, prostředek vyžaduje ověření, má uživatel k odeslání žádosti o ověření do služby Azure AD, aby bylo možné získat token a přístup k prostředku. Celý tento proces probíhá na pozadí bez zásahu uživatele. 

Organizace, které se zájmem o ochraně osobních údajů dat obvykle vyžadují klasifikace dat pro jejich řešení. Pokud svoji aktuální místní infrastrukturu se už používá klasifikaci dat, je možné použít Azure AD jako hlavní úložiště pro identitu uživatele. Běžné nástroje, že je využívat místně pro klasifikaci dat se nazývá [sada nástrojů klasifikace dat](https://msdn.microsoft.com/library/Hh204743.aspx) pro systém Windows Server 2012 R2. Tento nástroj může pomoct identifikovat, klasifikovat a chránit data na souborových serverech ve vašem privátním cloudu. Je také možné použít [automatické klasifikace souborů](https://technet.microsoft.com/library/hh831672.aspx) ve Windows serveru 2012 k provedení této úlohy.

Pokud vaše organizace nemá klasifikace dat v místě, ale je potřeba chránit citlivé soubory bez nutnosti přidávat nové servery místně, můžou použít Microsoft [Azure Rights Management Service](https://technet.microsoft.com/library/JJ585026.aspx).  Azure RMS pomocí šifrování, identit a zásad autorizace napomohla zabezpečení souborů a e-mailu a funguje napříč více zařízeními – telefony, tablety a počítače. Protože Azure RMS je Cloudová služba, není potřeba explicitně konfigurovat vztahy důvěryhodnosti s dalšími organizacemi, abyste s nimi mohli sdílet chráněný obsah. Pokud už máte Office 365 nebo adresář Azure AD, je automaticky dostupná podpora spolupráce mezi organizacemi. Také můžete synchronizovat jenom atributy adresáře, které Azure RMS potřebuje pro podporu společné identity pro místní účty Active Directory, pomocí Azure Active Directory synchronizační služby (AAD Sync) nebo Azure AD Connect.

Důležitou součástí správy obsahu je pochopit, kdo přistupuje k který prostředek, proto je důležité pro řešení správy identit funkce bohaté možnosti protokolování. Azure AD poskytuje víc než 30 dní, včetně protokolu:

* Změny v členství v roli (ex: uživatel přidaný k roli globálního správce)
* Aktualizace přihlašovacích údajů (ex: změny hesla)
* Správa domén (ex: ověření vlastní domény, odebrání domény)
* Přidání nebo odebrání aplikací
* Správa uživatelů (např: Přidání, odebrání, aktualizace uživatele)
* Přidání nebo odebrání licencí

> [!NOTE]
> Čtení [zabezpečení Microsoft Azure a Správa protokolů auditu](https://download.microsoft.com/download/B/6/C/B6C0A98B-D34A-417C-826E-3EA28CDFC9DD/AzureSecurityandAuditLogManagement_11132014.pdf) Další informace o možnosti protokolování v Azure.
> V závislosti na tom, jak jste odpověděli na otázky v [určení požadavků na správu obsahu](plan-hybrid-identity-design-considerations-contentmgt-requirements.md), byste měli určit, jak mají obsah, který se spravují v hybridní řešení identit. Když jsou všechny možnosti v tabulce 6 může integrovat s Azure AD, je důležité určit, která možnost je vhodnější pro vaše obchodní potřeby.
>
>

| Možnosti správy obsahu | Výhody | Nevýhody |
| --- | --- | --- |
| Centralizovaný místně (služba Active Directory Rights Management Server) |Plnou kontrolu nad serverovou infrastrukturu za klasifikace dat <br> Funkce integrované ve Windows serveru, není nutné pro další licence nebo předplatné <br> Je možné integrovat se službou Azure AD v hybridním scénáři <br> Podporuje funkce IRM (Správa) práva informace ve službě Microsoft Online services, jako je Exchange Online a SharePoint Online, jakož i služeb Office 365 <br> Podporuje místní serverové produkty Microsoftu, jako je například Exchange Server, SharePoint Server a souborové servery, na kterých běží Windows Server a infrastrukturu klasifikace souborů (FCI). |Vyšší údržby (vypořádat s aktualizací, konfiguraci a potenciální upgrady), od IT vlastní Server <br> Vyžaduje serverovou infrastrukturu v místním<br> Nepodporuje nativně využít možnosti Azure |
| Centralizovaný v cloudu (Azure RMS) |Snazší správa ve srovnání s místním řešením <br> Je možné integrovat se službou AD DS v hybridním scénáři <br>  Plně integrované s Azure AD <br> Nevyžaduje, aby místní server za účelem nasazení služby <br> Podporuje místní serverové produkty Microsoftu, jako je například Exchange Server, SharePoint, Server a souborové servery, na kterých běží Windows Server a souboru klasifikace infrastruktury (FCI) <br> Oddělení IT, může mít plnou kontrolu nad klíčem svého tenanta pomocí funkce BYOK. |Vaše organizace musí mít cloudové předplatné podporující službu RMS <br> Vaše organizace musí mít adresář Azure AD pro podporu ověřování uživatelů pro službu RMS |
| Hybridní (integrovaná s Azure RMS, v místním Active Directory Rights Management Server) |Tento scénář nahromadí výhody, centralizovaný místně i v cloudu. |Vaše organizace musí mít cloudové předplatné podporující službu RMS <br> Vaše organizace musí mít adresář Azure AD pro podporu ověřování uživatelů pro službu RMS, <br> Vyžaduje připojení mezi cloudové služby Azure a místní infrastruktury |

## <a name="define-access-control-options"></a>Definování možností řízení přístupu
S využitím ověřování, autorizace a řízení přístupu určené k dispozici ve službě Azure AD můžete povolit vaší společnosti použití centrální identity úložiště současně uživatelům a partnery používat jednotné přihlašování (SSO) jak je znázorněno na následujícím obrázku:

![](./media/plan-hybrid-identity-design-considerations/centralized-management.png)

Centralizovaná správa a plně integraci s další adresáře

Azure Active Directory poskytuje jednotné přihlašování do tisícovek aplikací SaaS a místních webových aplikací. Zobrazit [seznam kompatibility federace Azure Active Directory: poskytovatelů identit třetích stran, které lze použít k implementaci jednotného přihlašování](how-to-connect-fed-compatibility.md) článku najdete další podrobnosti o výrobců jednotné přihlašování, které testoval Microsoft. Tato funkce umožňuje organizaci implementovat celou řadu scénářů B2B současně si zachováte kontrolu správu identit a přístupů. Ale během B2B návrhu procesu, je důležité porozumět metodu ověřování, který používá partnera a ověřte, jestli tato metoda se nepodporuje v Azure. V současné době Azure AD podporuje následující metody:

* Security Assertion Markup Language (SAML)
* OAuth
* Kerberos
* Tokeny
* Certifikáty

> [!NOTE]
> Přečtěte si [ověřovací protokoly Active Directory Azure](https://msdn.microsoft.com/library/azure/dn151124.aspx) znát podrobnosti o jednotlivých protokolů a možnosti v Azure.
>
>

Podpora služby Azure AD, mobilní obchodní aplikace pomocí stejných možností ověřování Mobile Services snadno umožňuje uživatelům přihlášení do svých mobilních aplikací pomocí svých firemních přihlašovacích údajů služby Active Directory. Pomocí této funkce se podporuje Azure AD jako zprostředkovatele identity v Mobile Services společně s další zprostředkovatelé identity již podporovalo (což zahrnuje Accounts Microsoft, Facebooku ID, Google ID a Twitter ID). Pokud místní aplikace používat přihlašovací údaje uživatele v AD DS vaší společnosti, musí být transparentní přístup od partnerů a uživatele z cloudu. Můžete spravovat uživatele podmíněného řízení přístupu (cloudové) webové aplikace, webové rozhraní API, cloudovým službám Microsoftu, aplikacím SaaS třetích stran a nativní (mobilní) klientské aplikace a využívat výhod zabezpečení, auditování, vše v jednom vytváření sestav umístíte. Doporučujeme však ověřit implementace v neprodukčním prostředí nebo s omezený počet uživatelů.

> [!TIP]
> je důležité zmínit, že Azure AD nemá zásady skupiny služby AD DS má. Pokud chcete vynucovat zásady pro zařízení, musíte řešení správy mobilních zařízení, jako [Microsoft Intune](https://technet.microsoft.com/library/jj676587.aspx).
>
>

Po ověření uživatele pomocí služby Azure AD, je důležité vyhodnotit úroveň přístupu, kterou má uživatel. Úroveň přístupu, kterou má uživatel na prostředku se může lišit. Zatímco Azure AD můžete přidat další vrstvu zabezpečení, řízení přístupu k některým prostředkům, mějte na paměti, že vlastního prostředku může mít také svůj vlastní seznam řízení přístupu samostatně, jako je řízení přístupu pro soubory uložené na souborovém serveru. Následující obrázek shrnuje úrovně řízení přístupu, které máte v hybridním scénáři:

![](./media/plan-hybrid-identity-design-considerations/accesscontrol.png)

Každá interakce v diagramu, ukázalo v obrázek X představuje jeden scénář přístup ovládací prvek, který se dá pokrýt komponentami služby Azure AD. Dál budete mít popis jednotlivých scénářů:

  1. Podmíněný přístup k aplikacím, které jsou hostované místně: Registrovaná zařízení se zásadami přístupu můžete použít pro aplikace, které jsou nakonfigurovány pro použití služby AD FS pomocí Windows serveru 2012 R2.

  2. Řízení přístupu k webu Azure portal:  Azure také umožňuje řídit přístup k portálu pomocí řízení přístupu na základě role (RBAC)). Tato metoda umožňuje společnosti chcete omezit počet operací, které jednotlivec může udělat na webu Azure Portal. Pomocí RBAC pro řízení přístupu na portál, můžete správci IT delegovat přístup pomocí následujících postupů správy přístupu:

   - Přiřazení role na základě skupin: Přístup můžete přiřadit skupinám Azure AD, které se synchronizují z vaší místní služby Active Directory. To vám umožňuje využít stávající investice, které vaše organizace provedla v nástrojů a procesů pro správu skupin. Můžete také použít funkci delegované skupiny pro správu služby Azure AD Premium.
   - Použití předdefinované role v Azure: Můžete použít tři role – vlastník, Přispěvatel a Čtenář, ujistěte se, že uživatelé a skupiny mají oprávnění pouze úlohy, které potřebují ke své práci.
   -  Granulární přístup k prostředkům: Můžete je přiřadit role uživatelů a skupin pro konkrétní předplatné, skupinu prostředků nebo jednotlivých prostředků Azure, jako je například na webu nebo v databázi. Tímto způsobem můžete zajistit, aby uživatelé měli přístup ke všem prostředkům, které potřebují a bez přístupu k prostředkům, které není potřeba spravovat.

   > [!NOTE]
   > Pokud se vytváření aplikací a chcete přizpůsobit řízení přístupu pro ně, je také možné pro autorizaci použít role aplikace Azure AD. Přečtěte si [WebApp-RoleClaims-DotNet příklad](https://github.com/AzureADSamples/WebApp-RoleClaims-DotNet) o tom, jak vytvářet aplikace pro použití této funkce.


  3. Podmíněný přístup pro aplikace Office 365 s Microsoft Intune:  Správci IT mohou poskytnout zásady podmíněného přístupu zařízení k podnikovým prostředkům, zatímco zároveň umožnili pracovníkům s vhodnými zařízeními přístup ke službám zabezpečení. 
  
  4. Podmíněný přístup pro aplikace Saas: [Tato funkce](https://cloudblogs.microsoft.com/enterprisemobility/2015/06/25/azure-ad-conditional-access-preview-update-more-apps-and-blocking-access-for-users-not-at-work/) umožňuje konfigurovat pravidla přístupu k ověření službou Multi-Factor Authentication podle aplikace a k možnosti zablokovat přístup pro uživatele není v důvěryhodné síti. Můžete použít pravidla ověřování službou Multi-Factor Authentication pro všechny uživatele, které jsou přiřazeny k aplikaci nebo jenom pro uživatele v rámci určených skupinách zabezpečení. Uživatelé mohou vyloučit z požadavek na ověření službou Multi-Factor Authentication, jejich přístupu k aplikaci z IP adresy, který v rámci organizace sítě.

Protože možnosti pro řízení přístupu použít s více vrstvami přístup, se nedají použít pro tuto úlohu porovnání mezi těmito možnostmi. Ujistěte se, že jsou využívat všechny možnosti, které jsou k dispozici pro jednotlivé scénáře, které vyžadují řízení přístupu k vašim prostředkům.

## <a name="define-incident-response-options"></a>Definování možností reakce na incidenty
Azure AD vám můžou pomoct IT na potenciální rizika zabezpečení identit v prostředí pomocí sledování činnosti uživatelů. IT můžete použít Azure AD Access a sestavy využití získat náhled do integrity a zabezpečení adresáře vaší organizace. Pomocí těchto informací správce IT může zjistit, kde můžou být potenciální bezpečnostní rizika, mohli odpovídajícím způsobem plánovat pro zmírnění rizika.  [Předplatné Azure AD Premium](../fundamentals/active-directory-get-started-premium.md) obsahuje sadu sestavy zabezpečení, které můžete povolit IT pro získání těchto informací. [Sestavy Azure AD](../reports-monitoring/overview-reports.md) jsou zařazené do kategorií následujícím způsobem:

* **Sestavy anomálií**: Obsahovat události přihlášení, které jevily jako neobvyklé. Cílem je informovat vás tyto činnosti a vám umožní provádět rozhodnutí o tom, zda je podezřelé události.
* **Integrované sestavy aplikace**: Poskytuje přehled o používání cloudových aplikací ve vaší organizaci. Azure Active Directory nabízí integraci s tisíci cloudových aplikací.
* **Zprávy o chybách**: Označení chyb, které mohou nastat při zřizování účtů do externí aplikace.
* **Sestavy pro konkrétní uživatele**: Zobrazí zařízení nebo znak v dat o aktivitách pro konkrétního uživatele.
* **Protokoly aktivit**: V posledních 24 hodin, posledních 7 dnů, nebo posledních 30 dní, jakož i změny skupiny aktivit a aktivita resetování a registraci hesla obsahovat záznam všechny auditované události.

> [!TIP]
> Jiné sestavy, které může také pomoct reakce na incidenty tým pracující na případ je [uživatel s uniklými přihlašovacími údaji](https://cloudblogs.microsoft.com/enterprisemobility/2015/06/15/azure-active-directory-premium-reporting-now-detects-leaked-credentials/) sestavy. Tato sestava zobrazí žádná shoda mezi seznamu uniklými přihlašovacími údaji a tenanta.
>


Další důležité integrované sestavy ve službě Azure AD, který lze použít při šetření incidentů a jsou:

* **Aktivita resetování hesla**: správce poskytnout přehled o tom, jak aktivně resetování hesla se používá v organizaci.
* **Registrace aktivita resetování hesla**: poskytuje přehledy, do kterých se uživatelé zaregistrovali svoje metody pro resetování hesla, a metody, které jste vybrali.
* **Skupiny aktivit**: poskytuje historii změn do skupiny (ex: přidávání a odebírání uživatelů), které byly zahájeny v přístupovém panelu.

Kromě základní možnosti vytváření sestav Azure AD Premium, které můžete použít během procesu šetření reakcí na incidenty, může oddělení IT také Využijte výhod sestavy auditu získávat informace:

* Změny v členství v roli (například uživatel přidaný k roli globálního správce)
* Aktualizace přihlašovacích údajů (třeba změny hesel)
* Správa domén (například ověření vlastní domény, odebrání domény)
* Přidání nebo odebrání aplikací
* Správa uživatelů (například přidání, odebrání, aktualizace uživatele)
* Přidání nebo odebrání licencí

Protože možnosti pro reakce na incidenty použít s více vrstvami přístup, porovnání mezi tyto možnosti neplatí pro tuto úlohu. Ujistěte se, že jsou využívat všechny možnosti, které jsou k dispozici pro každý scénář, který vyžaduje, abyste používat funkce generování sestav Azure AD jako součást procesu reakce na incidenty vaší společnosti.

## <a name="next-steps"></a>Další postup
[Určení úloh správy hybridní identity](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)

## <a name="see-also"></a>Viz také
[Přehled aspektů návrhu](plan-hybrid-identity-design-considerations-overview.md)
