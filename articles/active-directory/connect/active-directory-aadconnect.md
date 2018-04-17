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
ms.date: 03/19/2018
ms.author: billmath
ms.openlocfilehash: bd2b65c31e7736346957dde4edfb7c32894c833e
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="integrate-your-on-premises-directories-with-azure-active-directory"></a>Integrace místních adresářů do služby Azure Active Directory
Azure AD Connect integruje vaše místní adresáře do služby Azure Active Directory. To umožní poskytovat společnou identitu pro uživatele pro aplikace Office 365, Azure a SaaS integrované s Azure AD. V tomto tématu vás provedeme kroky plánování, nasazení a provozu. Jedná se o kolekci odkazů na témata, která souvisejí s touto oblastí.

> [!IMPORTANT]
> [Azure AD Connect je nejlepší způsob, jak propojit místní adresář s Azure AD a Office 365. Teď je nejvhodnější doba pro upgrade na Azure AD Connect z Azure Active Directory Sync (DirSync) nebo Azure AD Sync pro Windows, protože tyto nástroje jsou už zastaralé a jejich podpora skončila 13. dubna 2017.](active-directory-aadconnect-dirsync-deprecated.md)  Navíc:
> 
> - Synchronizace uživatelů do služby Azure AD je **bezplatnou funkcí**, která nevyžaduje, aby zákazníci měli placené předplatné.
>- Synchronizovaní uživatelé **automaticky nezískají** *žádnou* licenci. Správci mají nad přiřazováním licencí stále plnou kontrolu. 
> - Microsoft doporučuje správcům IT všechny uživatele synchronizovat. Nejen že to odblokuje přístup uživatelů ke všem integrovaným prostředkům Azure AD, ale navíc správci IT získají mnohem lepší představu o tom, k jakým aplikacím uživatelé přistupují. 

![Co je služba Azure AD Connect](media/active-directory-aadconnect/arch.png)

## <a name="why-use-azure-ad-connect"></a>Proč používat Azure AD Connect
Integrace místních adresářů se službou Azure AD zvyšuje produktivitu uživatelů tím, že jim poskytuje společnou identitu pro přístup ke cloudovým i místním prostředkům. Uživatelé a organizace můžou využívat následujících výhod:

* Uživatelé můžou používat jedinou identitu pro přístup k místním aplikacím i ke cloudovým službám, jako je například Office 365.
* Získáváte jeden nástroj, který umožňuje snadné nasazení pro synchronizaci a přihlašování.
* Přináší nejnovější schopnosti pro vaše scénáře. Azure AD Connect nahrazuje starší verze nástrojů pro integraci identity, jako jsou například DirSync nebo Azure AD Sync. Další informace najdete v článku o [orovnání nástrojů pro integraci adresáře hybridní identity](../active-directory-hybrid-identity-design-considerations-tools-comparison.md).

### <a name="how-azure-ad-connect-works"></a>Jak Azure AD Connect funguje
Azure Active Directory Connect obsahuje tři hlavní komponenty: synchronizační služby, volitelná komponenta AD FS (Active Directory Federation Services) a monitorovací komponenta nazvaná [Azure AD Connect Health](../connect-health/active-directory-aadconnect-health.md).

<center>![Sada komponent Azure AD Connect](./media/active-directory-aadconnect-how-it-works/AADConnectStack2.png)
</center>

* Synchronizace – tato komponenta odpovídá za vytváření uživatelů, skupin a dalších objektů. Také zajišťuje, aby se informace o identitě místních uživatelů a skupin shodovaly s cloudem.
* AD FS – tato volitelná součást Azure AD Connect se dá použít ke konfiguraci hybridního prostředí, které používá místní infrastrukturu AD FS. Tu mohou organizace využít k při řešení komplexních nasazení, jako je například jednotné přihlašování s připojením k doméně, vynucování zásad přihlašování do AD a vícefaktorové ověřování (MFA) pomocí čipové karty nebo řešení jiného výrobce.
* Monitorování stavu – Azure AD Connect Health může poskytovat robustní monitorování a centrální umístění na portálu Azure Portal, kde je možné zobrazit tuto aktivitu. Další informace najdete v článku [Azure Active Directory Connect Health](../connect-health/active-directory-aadconnect-health.md).

## <a name="install-azure-ad-connect"></a>Instalace služby Azure AD Connect
Azure AD Connect si můžete stáhnout ze stránek [Stažení softwaru](http://go.microsoft.com/fwlink/?LinkId=615771).

| Řešení | Scénář |
| --- | --- |
| Ještě než začnete – [hardware a požadavky](active-directory-aadconnect-prerequisites.md) |<li>Kroky, které je třeba provést ještě před zahájením instalace služby Azure AD Connect.</li> |
| [Expresní nastavení](active-directory-aadconnect-get-started-express.md) |<li>Toto nastavení doporučujeme použít, pokud máte jednu doménovou strukturu AD.</li> <li>Uživatelé se přihlašují se stejným heslem a používá se synchronizace hesel.</li> |
| [Vlastní nastavení](active-directory-aadconnect-get-started-custom.md) |<li>Toto nastavení použijte, pokud máte více doménových struktur. Podporuje mnoho místních [topologií](active-directory-aadconnect-topologies.md).</li> <li>Upravte možnost přihlašování, jako například službu AD FS pro federaci nebo použití zprostředkovatele identity od jiného výrobce.</li> <li>Přizpůsobte funkce synchronizace, jako je například filtrování nebo zpětný zápis.</li> |
| [Upgrade z nástroje DirSync](active-directory-aadconnect-dirsync-upgrade-get-started.md) |<li>Používá se, pokud již máte existující server DirSync.</li> |
| [Upgrade z Azure AD Sync nebo z Azure AD Connect](active-directory-aadconnect-upgrade-previous-version.md) |<li>Můžete si vybrat z několika různých metod.</li> |

[Po instalaci](active-directory-aadconnect-whats-next.md) byste měli ověřit, jestli všechno funguje podle očekávání, a přiřadit uživatelům licence.

### <a name="next-steps-to-install-azure-ad-connect"></a>Další kroky instalace služby Azure AD Connect
|Téma |Odkaz|  
| --- | --- |
|Stažení služby Azure AD Connect | [Stažení služby Azure AD Connect](http://go.microsoft.com/fwlink/?LinkId=615771)|
|Instalace s expresním nastavením | [Expresní instalace služby Azure AD Connect](./active-directory-aadconnect-get-started-express.md)|
|Instalace s vlastním nastavením | [Vlastní instalace služby Azure AD Connect](./active-directory-aadconnect-get-started-custom.md)|
|Upgrade z nástroje DirSync | [Upgrade ze synchronizačního nástroje služby Azure AD (DirSync)](./active-directory-aadconnect-dirsync-upgrade-get-started.md)|
|Po instalaci | [Ověření instalace a přiřazení licencí](active-directory-aadconnect-whats-next.md)|

### <a name="learn-more-about-install-azure-ad-connect"></a>Další informace o instalaci Azure AD Connect
Také asi bude chtít připravit na problematiku [provozu](active-directory-aadconnectsync-operations.md). Možná byste měli mít pohotovostní server, abyste mohli jednoduše převzít funkce při selhání v případě [havárie](active-directory-aadconnectsync-operations.md#disaster-recovery). Pokud plánujete často měnit konfiguraci, měli byste uvažovat o serveru v [pracovním režimu](active-directory-aadconnectsync-operations.md#staging-mode).

|Téma |Odkaz|  
| --- | --- |
|Podporované topologie | [Topologie pro Azure AD Connect](active-directory-aadconnect-topologies.md)|
|Koncepty návrhu | [Koncepty návrhu Azure AD Connect](active-directory-aadconnect-design-concepts.md)|
|Účty použité k instalaci | [Další informace o účtech a oprávněních služby Azure AD Connect](./active-directory-aadconnect-accounts-permissions.md)|
|Provozní plánování | [Synchronizace Azure AD Connect: Provozní úlohy a požadavky](active-directory-aadconnectsync-operations.md)|
|Možnosti přihlášení uživatele | [Možnosti přihlášení uživatele Azure AD Connect](active-directory-aadconnect-user-signin.md)|

## <a name="configure-sync-features"></a>Konfigurace synchronizačních funkcí
Azure AD Connect obsahuje několik funkcí, které můžete volitelně zapnout nebo které jsou ve výchozím nastavení povolené. Některé funkce mohou v rámci určitých scénářů a topologií vyžadovat další konfiguraci.

[Filtrování](active-directory-aadconnectsync-configure-filtering.md) se používá, pokud chcete omezit, které objekty jsou synchronizovány do Azure AD. Ve výchozím nastavení jsou synchronizováni všichni uživatelé, kontakty, skupiny a počítače s Windows 10. Můžete měnit filtrování podle domén, organizačních jednotek nebo atributů.

[Synchronizace hodnot hash hesel](active-directory-aadconnectsync-implement-password-hash-synchronization.md) synchronizuje hodnoty hash hesel ve službě Active Directory do služby Azure AD. Koncový uživatel může používat stejné heslo v místní síti i na cloudu, a přitom ho spravovat pouze na jednom místě. Protože se jako autorita používá vaše místní služba Active Directory, můžete také použít vlastní zásady hesel.

[Zpětný zápis hesel](../active-directory-passwords-getting-started.md) umožňuje uživatelům měnit a resetovat vlastní hesla v cloudu a používat přitom vaše místní zásady hesel.

[Zpětný zápis zařízení](active-directory-aadconnect-feature-device-writeback.md) umožňuje zpětně zapisovat do místní služby Active Directory zařízení registrovaná v Azure AD, takže se dají použít pro podmíněný přístup.

Funkce pro [prevenci náhodného odstranění](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) je ve výchozím nastavení zapnutá a chrání cloudový adresář před příliš mnoha odstraněními najednou. Na jedno spuštění implicitně povoluje 500 odstranění. Toto nastavení se dá změnit v závislosti na velikosti vaší organizace.

[Automatický upgrade](active-directory-aadconnect-feature-automatic-upgrade.md) je po expresní instalaci ve výchozím nastavení povolený. Zajišťuje, že služba Azure AD Connect bude vždy v aktuální verzi.

### <a name="next-steps-to-configure-sync-features"></a>Další kroky při konfiguraci funkcí synchronizace
|Téma |Odkaz|  
| --- | --- |
|Konfigurace filtrování | [Synchronizace Azure AD Connect: Konfigurace filtrování](active-directory-aadconnectsync-configure-filtering.md)|
|Synchronizace hodnot hash hesel | [Synchronizace Azure AD Connect: Implementace synchronizace hodnot hash hesel](active-directory-aadconnectsync-implement-password-hash-synchronization.md)|
|Zpětný zápis hesla | [Začínáme se správou hesel](../active-directory-passwords-getting-started.md)|
|Zpětný zápis zařízení | [Povolení zpětného zápisu zařízení v Azure AD Connect](active-directory-aadconnect-feature-device-writeback.md)|
|Prevence náhodného odstranění | [Synchronizace Azure AD Connect: Prevence náhodného odstranění](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md)|
|Automatický upgrade | [Azure AD Connect: Automatický upgrade](active-directory-aadconnect-feature-automatic-upgrade.md)|

## <a name="customize-azure-ad-connect-sync"></a>Přizpůsobení synchronizace služby Azure AD Connect
Synchronizace Azure AD Connect se dodává s výchozí konfigurací, která by měla fungovat pro většinu zákazníků a většinu topologií. Vždycky ale dochází k situacím, kdy výchozí konfigurace nestačí a je třeba ji upravit. V této části a v propojených tématech je popsáno, jak provádět podporované změny.

Pokud jste s topologií synchronizace dosud nepracovali, je vhodné se seznámit se základními principy a terminologií, které jsou popsány v [technických konceptech](active-directory-aadconnectsync-technical-concepts.md). Azure AD Connect navazuje na MIIS2003, ILM2007 a FIM2010. Přestože některé věci zůstávají stejné, došlo také k mnoha změnám.

[Výchozí konfigurace](active-directory-aadconnectsync-understanding-default-configuration.md) předpokládá, že v konfiguraci může být více než jedna doménová struktura. V těchto topologiích může být objekt uživatele reprezentován v jiné doménové struktuře jako kontakt. Uživatel také může mít poštovní schránku propojenou s jinou doménovou strukturou prostředku. Chování výchozí konfigurace je popsáno v článku o [uživatelích a kontaktech](active-directory-aadconnectsync-understanding-users-and-contacts.md).

Model konfigurace v synchronizaci se označuje [deklarativní zřizování](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md). Pokročilé toky atributů využívají [funkce](active-directory-aadconnectsync-functions-reference.md) k vyjádření transformací atributů. Celou konfiguraci můžete zobrazit a prozkoumat pomocí nástrojů, které se dodávají se službou Azure AD Connect. Pokud potřebujete provést změny konfigurace, řiďte se [osvědčenými postupy](active-directory-aadconnectsync-best-practices-changing-default-configuration.md). Budete pak snadněji moci přejít na nové verze.

### <a name="next-steps-to-customize-azure-ad-connect-sync"></a>Další kroky k přizpůsobení synchronizace služby Azure AD Connect
|Téma |Odkaz|  
| --- | --- |
|Všechny články o synchronizaci služby Azure AD Connect | [Synchronizace služby Azure AD Connect](active-directory-aadconnectsync-whatis.md)|
|Technické koncepty | [Synchronizace služby Azure AD Connect: Technické koncepty](active-directory-aadconnectsync-technical-concepts.md)|
|Principy výchozí konfigurace | [Synchronizace služby Azure AD Connect: Principy výchozí konfigurace](active-directory-aadconnectsync-understanding-default-configuration.md)|
|Principy uživatelů a kontaktů | [Synchronizace služby Azure AD Connect: Principy uživatelů a kontaktů](active-directory-aadconnectsync-understanding-users-and-contacts.md)|
|Deklarativní zřizování | [Synchronizace služby Azure AD Connect: Principy výrazů deklarativního zřizování](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md)|
|Změna výchozí konfigurace | [Osvědčené postupy pro změnu výchozí konfigurace](active-directory-aadconnectsync-best-practices-changing-default-configuration.md)|

## <a name="configure-federation-features"></a>Konfigurace funkcí federace

Azure AD Connect poskytuje několik funkcí, které zjednodušují federaci se službou Azure AD pomocí AD FS a správu důvěryhodnosti federace. Azure AD Connect podporuje AD FS na Windows Serveru 2012R2 nebo novějším.

[Nahrajte certifikát SSL farmy služby AD FS](active-directory-aadconnectfed-ssl-update.md) i v případě, že ke správě důvěryhodnosti federace nepoužíváte Azure AD Connect.

Podle potřeby můžete svou farmu rozšířit [přidáním serveru AD FS](active-directory-aadconnect-federation-management.md#addadfsserver).

[Opravit důvěryhodnost](active-directory-aadconnect-federation-management.md#repairthetrust) s Azure AD můžete pár kliknutími.

Služba AD FS se dá nakonfigurovat tak, aby podporovala [více domén](active-directory-aadconnect-multiple-domains.md). Můžete mít například více hlavních domén, které potřebujete použít pro federaci.

Pokud váš server služby AD FS není nakonfigurován pro automatické aktualizace certifikátů z Azure AD nebo pokud používáte jiné řešení než AD FS, budete upozorněni, když bude třeba [aktualizovat certifikáty](active-directory-aadconnect-o365-certs.md).

### <a name="next-steps-to-configure-federation-features"></a>Další kroky pro konfiguraci funkcí federace
|Téma |Odkaz|  
| --- | --- |
|Všechny články o službě AD FS | [Azure AD Connect a federace](active-directory-aadconnectfed-whatis.md)|
|Konfigurace služby ADFS se subdoménami | [Podpora více domén pro federaci s Azure AD](active-directory-aadconnect-multiple-domains.md)|
|Správa farmy služby AD FS | [Správa služby AD FS a vlastní nastavení se službou Azure AD Connect](active-directory-aadconnect-federation-management.md)|
|Ruční aktualizace federačních certifikátů | [Obnovení federačních certifikátů pro Office 365 a Azure AD](active-directory-aadconnect-o365-certs.md)|

## <a name="more-information-and-references"></a>Další informace a odkazy
|Téma |Odkaz|  
| --- | --- |
|Historie verzí | [Historie verzí](active-directory-aadconnect-version-history.md)|
|Porovnání DirSync, Azure ADSync a Azure AD Connect | [Porovnání nástrojů pro integraci adresářů](../active-directory-hybrid-identity-design-considerations-tools-comparison.md)|
|Seznam kompatibility pro Azure AD bez služby AD FS | [Seznam kompatibilit pro federaci Azure AD](active-directory-aadconnect-federation-compatibility.md)|
|Konfigurace zprostředkovatele identity SAML 2.0|[Použití zprostředkovatele identity (IdP) SAML 2.0 pro jednotné přihlašování](active-directory-aadconnect-federation-saml-idp.md)|
|Synchronizované atributy | [Synchronizované atributy](active-directory-aadconnectsync-attributes-synchronized.md)|
|Monitorování pomocí služby Azure AD Connect Health | [Azure AD Connect Health](../connect-health/active-directory-aadconnect-health.md)|
|Nejčastější dotazy | [Azure AD Connect – nejčastější dotazy](active-directory-aadconnect-faq.md)|

**Další zdroje**

Prezentace Ignite 2015 na rozšíření místních adresářů do cloudu.

> [!VIDEO https://channel9.msdn.com/Events/Ignite/2015/BRK3862/player]
> 
> 

