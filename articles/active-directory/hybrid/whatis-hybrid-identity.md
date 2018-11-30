---
title: Připojení Active Directory s Azure Active Directory | Dokumenty Microsoft
description: Azure AD Connect integruje vaše místní adresáře do služby Azure Active Directory. To umožní poskytovat společnou identitu pro aplikace Office 365, Azure a SaaS integrované s Azure AD.
keywords: Úvod k Azure AD Connect, přehled Azure AD Connect, co je Azure AD Connect, instalace active directory
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 59bd209e-30d7-4a89-ae7a-e415969825ea
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/26/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: c8ab6b6e6bab7451de7d975dde644386fd4cb84e
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2018
ms.locfileid: "52311530"
---
# <a name="hybrid-identity-and-microsoft-identity-solutions"></a>Hybridní identita a řešení pro správu identit od Microsoftu
Řešení hybridní identity v [Microsoft Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md) umožňují synchronizovat místní objekty adresářové služby s Azure AD a zachovat přitom místní správu uživatelů. Při plánování synchronizace místní služby Windows Server Active Directory s Azure AD je nejprve potřeba se rozhodnout, jestli chcete používat spravované identity nebo federovanou identitu. 

- **Spravované identity** – uživatelské účty a skupiny synchronizované z místní služby Active Directory a ověřování uživatelů spravuje Azure.   
- **Federované identity** poskytují lepší kontrolu nad uživateli tím, že oddělují ověřování uživatelů od Azure a delegují ověřování důvěryhodnému místnímu zprostředkovateli identity. 

Při konfiguraci hybridní identity je k dispozici několik možností. Při zvažování, který model identit nejlépe vyhovuje potřebám vaší organizace, je potřeba vzít v úvahu také čas, stávající infrastrukturu, složitost a náklady. Tyto faktory se pro každou organizaci liší a v průběhu času se můžou měnit. Pokud se však vaše požadavky změní, máte možnost přepnout na jiný model identit.

## <a name="managed-identity"></a>Spravovaná identita 

Spravovaná identita představuje nejjednodušší způsob, jak synchronizovat místní objekty adresářové služby (uživatele a skupiny) s Azure AD. 

![Synchronizovaná hybridní identita](./media/whatis-hybrid-identity/managed.png)

Přestože je spravovaná identita nejjednodušší a nejrychlejší metoda, vaši uživatelé stále potřebují samostatné heslo pro cloudové prostředky. Pokud se tomu chcete vyhnout, můžete také (volitelně) [synchronizovat hodnoty hash uživatelských hesel](how-to-connect-password-hash-synchronization.md) s vaším adresářem Azure AD. Synchronizace hodnot hash hesel uživatelům umožní přihlašovat se ke cloudovým prostředkům organizace pomocí stejného uživatelského jména a hesla jako v místním prostředí. Azure AD Connect pravidelně kontroluje změny v místním adresáři a zajišťuje synchronizaci adresáře Azure AD. Když se změní atribut nebo heslo uživatele v místní službě Active Directory, automaticky se aktualizuje i v Azure AD. 

Pro většinu organizací, které pouze potřebují umožnit uživatelům přihlášení k Office 365, aplikacím SaaS nebo jiným prostředkům založeným na Azure AD, se doporučuje výchozí možnost synchronizace hodnot hash hesel. Pokud vám to nestačí, budete se muset rozhodnout mezi předávacím ověřováním a AD FS.

> [!TIP]
> Uživatelská hesla se ukládají v místní službě Windows Server Active Directory v podobě hodnoty hash, která představuje skutečné uživatelské heslo. Hodnota hash je výsledkem jednosměrné matematické funkce (hashovací algoritmus). Neexistuje žádný způsob, jak výsledek jednosměrné funkce převést zpět na heslo v prostém textu. Hodnotu hash hesla není možné použít pro přihlášení k místní síti. Pokud se rozhodnete k synchronizaci hodnot hash hesel, Azure AD Connect extrahuje hodnoty hash hesel z místní služby Active Directory a platí další bezpečnostní zpracování pro hodnotu hash hesla předtím, než se synchronizují do služby Azure AD. Synchronizaci hodnot hash hesel je možné používat také společně se zpětným zápisem hesel a umožnit tak samoobslužné resetování hesla v Azure AD. Kromě toho můžete povolit jednotné přihlašování pro uživatele na počítačích připojených k doméně, kteří jsou připojení k podnikové síti. Uživatelům s povoleným jednotným přihlašováním stačí k zabezpečenému přístupu ke cloudovým prostředkům zadat pouze uživatelské jméno. 
>

## <a name="pass-through-authentication"></a>Předávací ověřování

[Předávací ověřování Azure AD](how-to-connect-pta.md) poskytuje jednoduché řešení ověřování hesel pro služby založené na Azure AD s využitím vaší místní služby Active Directory. Pokud zásady zabezpečení a dodržování předpisů vaší organizace neumožňují odesílat uživatelská hesla, a to ani v podobě hodnoty hash, a potřebujete zajistit pouze podporu jednotného přihlašování na počítači pro zařízení připojená k doméně, doporučujeme zvážit využití předávacího ověřování. Předávací ověřování nevyžaduje žádné nasazení v zóně DMZ, což v porovnání s AD FS zjednodušuje infrastrukturu nasazení. Když se uživatelé přihlásí pomocí Azure AD, tato metoda ověřování ověří jejich hesla přímo v místní službě Active Directory.

![Předávací ověřování](./media/whatis-hybrid-identity/pass-through-authentication.png)

S předávacím ověřováním odpadá potřeba složité síťové infrastruktury a nemusíte ukládat místní hesla v cloudu. V kombinaci s jednotným přihlašováním poskytuje předávací ověřování skutečně integrované prostředí pro přihlašování k Azure AD nebo jiným cloudovým službám.

Předávací ověřování se konfiguruje pomocí nástroje Azure AD Connect, který využívá jednoduchého místního agenta, který naslouchá požadavkům na ověření hesla. Agenta můžete snadno nasadit na více počítačů a zajistit tak vysokou dostupnost a vyrovnávání zatížení. Vzhledem k tomu, že veškerá komunikace je pouze odchozí, není v zóně DMZ potřeba instalovat konektor. Požadavky na počítač serveru pro konektor jsou následující:

- Windows Server 2012 R2 nebo novější
- Připojení k doméně v doménové struktuře prostřednictvím které se uživatelé ověřují

## <a name="federated-identity-ad-fs"></a>Federovaná identita (AD FS)

Pokud chcete mít větší kontrolu nad přístupem uživatelů k Office 365 a dalším cloudovým službám, můžete pomocí služby [Active Directory Federation Services (AD FS)](how-to-connect-fed-whatis.md) nastavit synchronizaci adresářů s jednotným přihlašováním. Při federování přihlašování uživatelů pomocí AD FS se ověřování deleguje na místní server, který ověřuje přihlašovací údaje uživatelů. V tomto modelu se přihlašovací údaje místní služby Active Directory nikdy nepředávají do Azure AD.

![Federovaná identita](./media/whatis-hybrid-identity/federated-identity.png)

Tato metoda přihlašování, která se označuje také jako federace identity, zajišťuje, že se veškeré ověřování uživatelů řídí v místním prostředí, a umožňuje správcům implementovat přísnější úrovně řízení přístupu. Federace identity pomocí AD FS představuje nejsložitější možnost a vyžaduje nasazení dalších serverů v místním prostředí. Federace identity vás také zavazuje poskytovat nepřetržitou podporu pro službu Active Directory a infrastrukturu AD FS. Takto vysoká úroveň podpory je nezbytná z toho důvodu, že v případě nedostupnosti místního přístupu k internetu, řadiče domény nebo serverů AD FS se uživatelé nebudou moct přihlásit ke cloudovým službám.

> [!TIP]
> Pokud se rozhodnete využít federaci pomocí služby Active Directory Federation Services (AD FS), můžete volitelně nastavit synchronizaci hodnot hash hesel jako záložní řešení pro případ, že vaše infrastruktura AD FS selže.
>

## <a name="common-scenarios-and-recommendations"></a>Běžné scénáře a doporučení

Tady najdete několik běžných scénářů souvisejících s hybridní identitou a správou přístupu a doporučení, jaká možnost (nebo možnosti) hybridní identity by mohla být pro každý z nich nejvhodnější.

|Požadavky:|PHS a SSO<sup>1</sup>| PTA a SSO<sup>2</sup> | AD FS<sup>3</sup>|
|-----|-----|-----|-----|
|Automatická synchronizace nových uživatelských, kontaktních a skupinových účtů vytvořených v místní službě Active Directory do cloudu|![Doporučené](./media/whatis-hybrid-identity/ic195031.png)| ![Doporučené](./media/whatis-hybrid-identity/ic195031.png) |![Doporučené](./media/whatis-hybrid-identity/ic195031.png)|
|Nastavení tenanta pro hybridní scénáře Office 365|![Doporučené](./media/whatis-hybrid-identity/ic195031.png)| ![Doporučené](./media/whatis-hybrid-identity/ic195031.png) |![Doporučené](./media/whatis-hybrid-identity/ic195031.png)|
|Povolení přihlašování a přístupu uživatelů ke cloudovým službám pomocí místního hesla|![Doporučené](./media/whatis-hybrid-identity/ic195031.png)| ![Doporučené](./media/whatis-hybrid-identity/ic195031.png) |![Doporučené](./media/whatis-hybrid-identity/ic195031.png)|
|Implementace jednotného přihlašování pomocí podnikových přihlašovacích údajů|![Doporučené](./media/whatis-hybrid-identity/ic195031.png)| ![Doporučené](./media/whatis-hybrid-identity/ic195031.png) |![Doporučené](./media/whatis-hybrid-identity/ic195031.png)|
|Zajištění, aby se žádné hodnoty hash hesel neukládaly v cloudu| |![Doporučené](./media/whatis-hybrid-identity/ic195031.png)|![Doporučené](./media/whatis-hybrid-identity/ic195031.png)|
|Povolení cloudových řešení vícefaktorového ověřování| |![Doporučené](./media/whatis-hybrid-identity/ic195031.png)|![Doporučené](./media/whatis-hybrid-identity/ic195031.png)|
|Povolení místních řešení vícefaktorového ověřování| | |![Doporučené](./media/whatis-hybrid-identity/ic195031.png)|
|Podpora ověřování uživatelů pomocí čipové karty<sup>4</sup>| | |![Doporučené](./media/whatis-hybrid-identity/ic195031.png)|
|Zobrazování oznámení o vypršení platnosti hesla na portálu Office a na ploše Windows 10| | |![Doporučené](./media/whatis-hybrid-identity/ic195031.png)|

> <sup>1</sup> Synchronizace hodnot hash hesel a jednotné přihlašování.
>
> <sup>2</sup> Předávací ověřování a jednotné přihlašování. 
>
> <sup>3</sup> Federované jednotné přihlašování pomocí AD FS.
>
> <sup>4</sup> AD FS je možné integrovat s podnikovou infrastrukturou veřejných klíčů a umožnit tak přihlašování pomocí certifikátů. Těmito certifikáty můžou být softwarové certifikáty nasazené přes důvěryhodné kanály zřizování, jako MDM nebo GPO, nebo certifikáty čipových karet (včetně karet PIV/CAC) nebo Hello pro firmy (důvěryhodnost certifikátu). Další informace o podpoře ověřování pomocí čipové karty najdete v [tomto blogovém příspěvku](https://blogs.msdn.microsoft.com/samueld/2016/07/19/adfs-certauth-aad-o365/).
>

## <a name="what-is-azure-ad-connect"></a>Co je Azure AD Connect?

Azure AD Connect je nástroj od Microsoftu, jehož účelem je splnit a zajistit cíle hybridní identity.  To umožní poskytovat společnou identitu pro uživatele pro aplikace Office 365, Azure a SaaS integrované s Azure AD.  Má následující funkce:
    
- [Synchronizace](how-to-connect-sync-whatis.md) – tato komponenta odpovídá za vytváření uživatelů, skupin a dalších objektů. Také zajišťuje, aby se informace o identitě místních uživatelů a skupin shodovaly s cloudem.  Odpovídá za synchronizaci hodnot hash hesel se službou Azure AD.
- [Synchronizace hodnot hash hesel](how-to-connect-password-hash-synchronization.md) – Volitelná komponenta, která uživatelům umožňuje používat stejné heslo v místním prostředí i v cloudu díky tomu, že synchronizuje hodnotu hash uživatelského hesla s Azure AD.
-   [AD FS a integrace federace](how-to-connect-fed-whatis.md) – federace je volitelná součást Azure AD Connect, která se dá použít ke konfiguraci hybridního prostředí pomocí místní infrastruktury služby AD FS. Poskytuje také možnosti správy služby AD FS, jako je obnovení certifikátů a další nasazení serverů služby AD FS.
-   [Předávací ověřování](how-to-connect-pta.md) – další volitelná komponenta, která umožňuje uživatelům používat stejné heslo místně i v cloudu, ale nevyžaduje dodatečnou infrastrukturu federovaného prostředí.
-   [PingFederate a integrace federace](how-to-connect-install-custom.md#configuring-federation-with-pingfederate) – Další možnost federace, která umožňuje využívat jako zprostředkovatele identity PingFederate.
-   [Monitorování stavu](whatis-hybrid-identity-health.md) – Azure AD Connect Health poskytuje robustní monitorování a centrální umístění na webu Azure Portal, kde je možné tuto aktivitu zobrazit. 


![Co je služba Azure AD Connect](./media/whatis-hybrid-identity/arch.png)



## <a name="what-is-azure-ad-connect-health"></a>Co je Azure AD Connect Health?

Služba Azure Active Directory (Azure AD) Connect Health pomáhá monitorovat místní infrastrukturu identity a synchronizační služby a lépe proniknout do jejich funkce. Umožňuje udržovat spolehlivé propojení s Office 365 a službami Microsoft Online Services tím, že zajišťuje monitorování klíčových komponent identity, jako jsou servery služby Active Directory Federation Services (AD FS), servery služby Azure AD Connect (neboli synchronizační stroj), řadiče domény služby Active Directory atd. Také udržuje klíčové datové body těchto komponent dobře přístupné, takže můžete snadno získat přehled o jejich používání a dalších důležitých statistikách, abyste se mohli kvalifikovaně rozhodnout.

Další informace najdete v článku [Portál služby Azure AD Connect Health](https://aka.ms/aadconnecthealth). Na portálu služby Azure AD Connect Health můžete zobrazovat upozornění, monitorování výkonu, analýzy využívání a další informace. Azure AD Connect Health umožňuje mít na jednom místě a v jediném přehledu informace o stavu klíčových komponent identity.

![Co je Azure AD Connect Health](./media/whatis-hybrid-identity-health/aadconnecthealth2.png)


Se zvyšujícím se počtem funkcí ve službě Azure AD Connect Health poskytuje portál jediný řídicí panel přehledu identity. Získáte ještě rozsáhlejší, kvalitnější a propojenější prostředí, které vaši uživatelé budou moci využít ke zvýšení efektivity své práce.


## <a name="why-use-azure-ad-connect"></a>Proč používat Azure AD Connect?
Integrace místních adresářů se službou Azure AD zvyšuje produktivitu uživatelů tím, že jim poskytuje společnou identitu pro přístup ke cloudovým i místním prostředkům. Uživatelé a organizace můžou využívat následujících výhod:

* Uživatelé můžou používat jedinou identitu pro přístup k místním aplikacím i ke cloudovým službám, jako je například Office 365.
* Získáváte jeden nástroj, který umožňuje snadné nasazení pro synchronizaci a přihlašování.
* Přináší nejnovější schopnosti pro vaše scénáře. Azure AD Connect nahrazuje starší verze nástrojů pro integraci identity, jako jsou například DirSync nebo Azure AD Sync. Další informace najdete v článku o [orovnání nástrojů pro integraci adresáře hybridní identity](plan-hybrid-identity-design-considerations-tools-comparison.md).

## <a name="why-use-azure-ad-connect-health"></a>Proč používat službu Azure AD Connect Health?
Když integrujete místní adresáře se službou Azure AD, zvýší se produktivita vašich uživatelů skrze společnou identitu pro přístup ke cloudovým i místním prostředkům. Spolu s touto integrací ale přicházejí i další výzvy. Je třeba zajistit, aby toto prostředí bylo v pořádku a uživatelé měli spolehlivý přístup k místním i cloudovým prostředkům z jakéhokoli zařízení. Azure AD Connect Health pomáhá s monitorováním a získáváním přehledu o místní infrastruktuře identity, která se používá pro přístup k Office 365 nebo k jiným aplikacím služby Azure AD. Stačí jednoduše nainstalovat agenta na každý z vašich místních serverů identity.

### <a name="azure-ad-connect-health-for-ad-fshow-to-connect-health-adfsmd"></a>[Azure AD Connect Health pro službu AD FS](how-to-connect-health-adfs.md)
Azure AD Connect Health pro službu AD FS podporuje službu AD FS 2.0 v systémech Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 a Windows Server 2016. Podporuje také monitorování proxy serveru služby AD FS a proxy serverů webových aplikací, které poskytují ověřování pro přístup z extranetu. Díky snadné a rychlé instalaci agenta služby Health poskytuje Azure AD Connect Health pro službu AD FS sadu klíčových funkcí.

#### <a name="key-benefits-and-best-practices"></a>Klíčové výhody a osvědčené postupy

- *Rozšířené zabezpečení*
  - [Trendy uzamčení extranetu](how-to-connect-health-adfs.md#usage-analytics-for-ad-fs)
  - [Sestava neúspěšných přihlášení](how-to-connect-health-adfs.md#risky-ip-report-public-preview) 
  - [Vyhovění ochraně osobních údajů](reference-connect-health-user-privacy.md)    
- *Upozornění na všechny [kritické systémové problémy služby AD FS](how-to-connect-health-alert-catalog.md#alerts-for-active-directory-federation-services)*
    - Konfigurace serveru a dostupnost 
    - [Výkon a možnosti připojení](how-to-connect-health-adfs.md#performance-monitoring-for-ad-fs) 
  - Pravidelná údržba    
- *Snadné nasazení a správa*
  - Rychlá [instalace agenta](how-to-connect-health-agent-install.md#installing-the-azure-ad-connect-health-agent-for-ad-fs) 
  - Automatický upgrade agenta na nejnovější verzi 
  - Dostupnost dat na portálu během několika minut    
- *Detailní [metriky využití](how-to-connect-health-adfs.md#usage-analytics-for-ad-fs)* 
  - Využití nejčastějších aplikací
  - Umístění v síti a připojení TCP
  - Žádosti o tokeny pro každý server    
- *Skvělé uživatelské prostředí* 
  - Styl řídicího panelu z webu Azure Portal
  - [Upozornění prostřednictvím e-mailů](how-to-connect-health-adfs.md#alerts-for-ad-fs)    

#### <a name="feature-highlight"></a>Nejvýznamnější funkce

*   Monitorování s upozorněními, která oznamují, pokud služba AD FS nebo proxy servery služby AD FS nejsou v pořádku
*   E-mailová oznámení pro kritické výstrahy
*   Trendy v datech o výkonu, které se hodí pro plánování kapacity služby AD FS
*   Analýza využití pro přihlášení ke službě AD FS s pivoty (aplikace, uživatelé, umístění v síti atd.)
*   Sestavy pro službu AD FS, například 50 uživatelů s největším počtem chybně zadaných kombinací uživatelského jména a hesla z poslední IP adresy
*   Sestava rizikových IP adres pro neúspěšná přihlášení ke službě AD FS

Tady najdete další informace o [Používání služby Azure AD Connect Health se službou AD FS](how-to-connect-health-adfs.md).

### <a name="azure-ad-connect-health-for-synchow-to-connect-health-syncmd"></a>[Azure AD Connect Health pro synchronizaci](how-to-connect-health-sync.md)
Služba Azure AD Connect Health pro synchronizaci monitoruje a poskytuje informace o synchronizacích, ke kterým dochází mezi místní službou Active Directory a službou Azure AD. Služba Azure AD Connect Health pro synchronizaci poskytuje následující sadu klíčových funkcí:

* Monitorování s upozorněními v případě, že server Azure AD Connect neboli synchronizační stroj není v pořádku
* E-mailová oznámení pro kritické výstrahy
* Synchronizace statistik provozu včetně přehledů latencí pro operace synchronizace a trendy v různých operacích, jako jsou přidání, aktualizace nebo odstranění
* Rychlý přehled informací o vlastnostech synchronizace a posledním úspěšném exportu do služby Azure AD
* Sestavy chyb synchronizace na úrovni objektu \(nevyžadují službu Azure AD Premium\)

Tady najdete další informace o [Používání služby Azure AD Connect Health k synchronizaci](how-to-connect-health-sync.md).

### <a name="azure-ad-connect-health-for-ad-dshow-to-connect-health-addsmd"></a>[Azure AD Connect Health pro službu AD DS](how-to-connect-health-adds.md)
Azure AD Connect Health pro službu Active Directory Domain Services (AD DS) podporuje monitorování řadičů domény nainstalovaných v systémech Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 a Windows Server 2016. Instalace agenta služby Health umožňuje monitorování místního prostředí AD DS z cloudu. Služba Azure AD Connect Health pro AD DS poskytuje následující sadu klíčových funkcí:

* Monitorování upozornění, aby se zjistilo, kdy řadiče domény nejsou v pořádku, a e-mailová oznámeními pro kritická upozornění
* Řídicí panel Řadiče domény, který poskytuje rychlý přehled kondice a provozního stavu řadičů domény
* Řídicí panel Stav replikace s nejnovějšími informacemi o replikaci a s odkazy na průvodce odstraňováním potíží při zjištění chyb
* Rychlý přístup odkudkoli ke grafům s daty o výkonu oblíbených čítačů výkonu, které jsou nezbytné pro účely monitorování a řešení potíží

Tady najdete další informace o [Používání služby Azure AD Connect Health se službou AD DS](how-to-connect-health-adds.md).

## <a name="next-steps"></a>Další postup


- [Hardware a předpoklady](how-to-connect-install-prerequisites.md) 
- [Expresní nastavení](how-to-connect-install-express.md)
- [Vlastní nastavení](how-to-connect-install-custom.md)
- [Synchronizace hodnot hash hesel](how-to-connect-password-hash-synchronization.md)|
- [Předávací ověřování](how-to-connect-pta.md)
- [Azure AD Connect a federace](how-to-connect-fed-whatis.md)
- [Instalace agentů Azure AD Connect Health](how-to-connect-health-agent-install.md) 
- [Synchronizace služby Azure AD Connect](how-to-connect-sync-whatis.md)
- [Historie verzí](reference-connect-version-history.md)
- [Porovnání nástrojů pro integraci adresářů](plan-hybrid-identity-design-considerations-tools-comparison.md)
- [Azure AD Connect – nejčastější dotazy](reference-connect-faq.md)









