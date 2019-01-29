---
title: Plán instalace Azure AD Connect a Azure AD Connect Health | Dokumenty Microsoft
description: Tento dokument obsahuje přehled možností instalace a cest, které jsou k dispozici pro instalaci Azure AD Connect a Connect Health.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/18/2018
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: 0befcdd1a0708358d2ba034c7ec61a626a543be8
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55183743"
---
# <a name="azure-ad-connect-and-azure-ad-connect-health-installation-roadmap"></a>Plán instalace Azure AD Connect a Azure AD Connect Health

## <a name="install-azure-ad-connect"></a>Instalace služby Azure AD Connect

> [!IMPORTANT]
> Microsoft nepodporuje úpravy ani provoz synchronizace služby Azure AD Connect mimo formálně zdokumentované akce. Některé z těchto akcí můžou způsobit nekonzistentní nebo nepodporovaný stav synchronizace služby Azure AD Connect. Microsoft proto nemůže pro taková nasazení poskytovat technickou podporu.

Azure AD Connect si můžete stáhnout ze stránek [Stažení softwaru](https://go.microsoft.com/fwlink/?LinkId=615771).

| Řešení | Scénář |
| --- | --- |
| Ještě než začnete – [hardware a požadavky](how-to-connect-install-prerequisites.md) |<li>Kroky, které je třeba provést ještě před zahájením instalace služby Azure AD Connect.</li> |
| [Expresní nastavení](how-to-connect-install-express.md) |<li>Toto nastavení doporučujeme použít, pokud máte jednu doménovou strukturu AD.</li> <li>Uživatelé se přihlašují se stejným heslem a používá se synchronizace hesel.</li> |
| [Vlastní nastavení](how-to-connect-install-custom.md) |<li>Toto nastavení použijte, pokud máte více doménových struktur. Podporuje mnoho místních [topologií](plan-connect-topologies.md).</li> <li>Upravte možnost přihlašování, jako například průchozí ověřování, systém ADFS pro federaci nebo použití zprostředkovatele identity od jiného výrobce.</li> <li>Přizpůsobte funkce synchronizace, jako je například filtrování nebo zpětný zápis.</li> |
| [Upgrade z nástroje DirSync](how-to-dirsync-upgrade-get-started.md) |<li>Používá se, pokud již máte existující server DirSync.</li> |
| [Upgrade z Azure AD Sync nebo z Azure AD Connect](how-to-upgrade-previous-version.md) |<li>Můžete si vybrat z několika různých metod.</li> |

[Po instalaci](how-to-connect-post-installation.md) byste měli ověřit, jestli všechno funguje podle očekávání, a přiřadit uživatelům licence.

### <a name="next-steps-to-install-azure-ad-connect"></a>Další kroky instalace služby Azure AD Connect
|Téma |Odkaz|  
| --- | --- |
|Stažení služby Azure AD Connect | [Stažení služby Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771)|
|Instalace s expresním nastavením | [Expresní instalace služby Azure AD Connect](./how-to-connect-install-express.md)|
|Instalace s vlastním nastavením | [Vlastní instalace služby Azure AD Connect](./how-to-connect-install-custom.md)|
|Upgrade z nástroje DirSync | [Upgrade ze synchronizačního nástroje služby Azure AD (DirSync)](./how-to-dirsync-upgrade-get-started.md)|
|Po instalaci | [Ověření instalace a přiřazení licencí](how-to-connect-post-installation.md)|

### <a name="learn-more-about-install-azure-ad-connect"></a>Další informace o instalaci Azure AD Connect
Také asi bude chtít připravit na problematiku [provozu](how-to-connect-sync-operations.md). Možná byste měli mít pohotovostní server, abyste mohli jednoduše převzít funkce při selhání v případě [havárie](how-to-connect-sync-operations.md#disaster-recovery). Pokud plánujete často měnit konfiguraci, měli byste uvažovat o serveru v [pracovním režimu](how-to-connect-sync-operations.md#staging-mode).

|Téma |Odkaz|  
| --- | --- |
|Podporované topologie | [Topologie pro Azure AD Connect](plan-connect-topologies.md)|
|Koncepty návrhu | [Koncepty návrhu Azure AD Connect](plan-connect-design-concepts.md)|
|Účty použité k instalaci | [Další informace o účtech a oprávněních služby Azure AD Connect](reference-connect-accounts-permissions.md)|
|Provozní plánování | [Synchronizace Azure AD Connect: Provozní úlohy a důležité informace](how-to-connect-sync-operations.md)|
|Možnosti přihlášení uživatele | [Možnosti přihlášení uživatele Azure AD Connect](plan-connect-user-signin.md)|

## <a name="configure-sync-features"></a>Konfigurace synchronizačních funkcí
Azure AD Connect obsahuje několik funkcí, které můžete volitelně zapnout nebo které jsou ve výchozím nastavení povolené. Některé funkce mohou v rámci určitých scénářů a topologií vyžadovat další konfiguraci.

[Filtrování](how-to-connect-sync-configure-filtering.md) se používá, pokud chcete omezit, které objekty jsou synchronizovány do Azure AD. Ve výchozím nastavení jsou synchronizováni všichni uživatelé, kontakty, skupiny a počítače s Windows 10. Můžete měnit filtrování podle domén, organizačních jednotek nebo atributů.

[Synchronizace hodnot hash hesel](how-to-connect-password-hash-synchronization.md) synchronizuje hodnoty hash hesel ve službě Active Directory do služby Azure AD. Koncový uživatel může používat stejné heslo v místní síti i na cloudu, a přitom ho spravovat pouze na jednom místě. Protože se jako autorita používá vaše místní služba Active Directory, můžete také použít vlastní zásady hesel.

[Zpětný zápis hesel](../authentication/quickstart-sspr.md) umožňuje uživatelům měnit a resetovat vlastní hesla v cloudu a používat přitom vaše místní zásady hesel.

[Zpětný zápis zařízení](how-to-connect-device-writeback.md) umožňuje zpětně zapisovat do místní služby Active Directory zařízení registrovaná v Azure AD, takže se dají použít pro podmíněný přístup.

Funkce pro [prevenci náhodného odstranění](how-to-connect-sync-feature-prevent-accidental-deletes.md) je ve výchozím nastavení zapnutá a chrání cloudový adresář před příliš mnoha odstraněními najednou. Na jedno spuštění implicitně povoluje 500 odstranění. Toto nastavení se dá změnit v závislosti na velikosti vaší organizace.

[Automatický upgrade](how-to-connect-install-automatic-upgrade.md) je po expresní instalaci ve výchozím nastavení povolený. Zajišťuje, že služba Azure AD Connect bude vždy v aktuální verzi.

### <a name="next-steps-to-configure-sync-features"></a>Další kroky při konfiguraci funkcí synchronizace
|Téma |Odkaz|  
| --- | --- |
|Konfigurace filtrování | [Synchronizace Azure AD Connect: Konfigurace filtrování](how-to-connect-sync-configure-filtering.md)|
|Synchronizace hodnot hash hesel | [Synchronizace hodnoty hash hesel](how-to-connect-password-hash-synchronization.md)|
|Předávací ověřování | [Předávací ověřování](how-to-connect-pta.md)
|Zpětný zápis hesla | [Začínáme se správou hesel](../authentication/quickstart-sspr.md)|
|Zpětný zápis zařízení | [Povolení zpětného zápisu zařízení v Azure AD Connect](how-to-connect-device-writeback.md)|
|Prevence náhodného odstranění | [Synchronizace Azure AD Connect: Prevence náhodného odstranění](how-to-connect-sync-feature-prevent-accidental-deletes.md)|
|Automatický upgrade | [Azure AD Connect: Automatický upgrade](how-to-connect-install-automatic-upgrade.md)|

## <a name="customize-azure-ad-connect-sync"></a>Přizpůsobení synchronizace služby Azure AD Connect
Synchronizace Azure AD Connect se dodává s výchozí konfigurací, která by měla fungovat pro většinu zákazníků a většinu topologií. Vždycky ale dochází k situacím, kdy výchozí konfigurace nestačí a je třeba ji upravit. V této části a v propojených tématech je popsáno, jak provádět podporované změny.

Pokud jste s topologií synchronizace dosud nepracovali, je vhodné se seznámit se základními principy a terminologií, které jsou popsány v [technických konceptech](how-to-connect-sync-technical-concepts.md). Azure AD Connect navazuje na MIIS2003, ILM2007 a FIM2010. Přestože některé věci zůstávají stejné, došlo také k mnoha změnám.

[Výchozí konfigurace](concept-azure-ad-connect-sync-default-configuration.md) předpokládá, že v konfiguraci může být více než jedna doménová struktura. V těchto topologiích může být objekt uživatele reprezentován v jiné doménové struktuře jako kontakt. Uživatel také může mít poštovní schránku propojenou s jinou doménovou strukturou prostředku. Chování výchozí konfigurace je popsáno v článku o [uživatelích a kontaktech](concept-azure-ad-connect-sync-user-and-contacts.md).

Model konfigurace v synchronizaci se označuje [deklarativní zřizování](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md). Pokročilé toky atributů využívají [funkce](reference-connect-sync-functions-reference.md) k vyjádření transformací atributů. Celou konfiguraci můžete zobrazit a prozkoumat pomocí nástrojů, které se dodávají se službou Azure AD Connect. Pokud potřebujete provést změny konfigurace, řiďte se [osvědčenými postupy](how-to-connect-sync-best-practices-changing-default-configuration.md). Budete pak snadněji moci přejít na nové verze.

### <a name="next-steps-to-customize-azure-ad-connect-sync"></a>Další kroky k přizpůsobení synchronizace služby Azure AD Connect
|Téma |Odkaz|  
| --- | --- |
|Všechny články o synchronizaci služby Azure AD Connect | [Synchronizace služby Azure AD Connect](how-to-connect-sync-whatis.md)|
|Technické koncepty | [Synchronizace Azure AD Connect: Technické koncepce](how-to-connect-sync-technical-concepts.md)|
|Principy výchozí konfigurace | [Synchronizace Azure AD Connect: Principy výchozí konfigurace](concept-azure-ad-connect-sync-default-configuration.md)|
|Principy uživatelů a kontaktů | [Synchronizace Azure AD Connect: Principy uživatelů a kontaktů](concept-azure-ad-connect-sync-user-and-contacts.md)|
|Deklarativní zřizování | [Synchronizace služby Azure AD Connect: Principy výrazů deklarativního zřizování](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md)|
|Změna výchozí konfigurace | [Osvědčené postupy pro změnu výchozí konfigurace](how-to-connect-sync-best-practices-changing-default-configuration.md)|

## <a name="configure-federation-features"></a>Konfigurace funkcí federace

Azure AD Connect poskytuje několik funkcí, které zjednodušují federaci se službou Azure AD pomocí AD FS a správu důvěryhodnosti federace. Azure AD Connect podporuje AD FS na Windows Serveru 2012R2 nebo novějším.

[Nahrajte certifikát SSL farmy služby AD FS](how-to-connect-fed-ssl-update.md) i v případě, že ke správě důvěryhodnosti federace nepoužíváte Azure AD Connect.

Podle potřeby můžete svou farmu rozšířit [přidáním serveru AD FS](how-to-connect-fed-management.md#addadfsserver).

[Opravit důvěryhodnost](how-to-connect-fed-management.md#repairthetrust) s Azure AD můžete pár kliknutími.

Služba AD FS se dá nakonfigurovat tak, aby podporovala [více domén](how-to-connect-install-multiple-domains.md). Můžete mít například více hlavních domén, které potřebujete použít pro federaci.

Pokud server AD FS nemá nakonfigurované automatické aktualizace certifikátů z Azure AD nebo používáte jiné řešení než AD FS, budete upozorněni, že musíte [aktualizovat certifikáty](how-to-connect-fed-o365-certs.md).

### <a name="next-steps-to-configure-federation-features"></a>Další kroky pro konfiguraci funkcí federace
|Téma |Odkaz|  
| --- | --- |
|Všechny články o službě AD FS | [Azure AD Connect a federace](how-to-connect-fed-whatis.md)|
|Konfigurace služby ADFS se subdoménami | [Podpora více domén pro federaci s Azure AD](how-to-connect-install-multiple-domains.md)|
|Správa farmy služby AD FS | [Správa služby AD FS a vlastní nastavení se službou Azure AD Connect](how-to-connect-fed-management.md)|
|Ruční aktualizace federačních certifikátů | [Obnovení federačních certifikátů pro Office 365 a Azure AD](how-to-connect-fed-o365-certs.md)|


## <a name="get-started-with-azure-ad-connect-health"></a>Začínáme se službou Azure AD Connect Health
Pokud chcete začít se službou Azure AD Connect Health, použijte následující postup:

1. [Získejte předplatné služby Azure AD Premium](../fundamentals/active-directory-get-started-premium.md) nebo [začněte se zkušební verzí](https://azure.microsoft.com/trial/get-started-active-directory/).
2. [Stáhněte a nainstalujte agenty služby Azure AD Connect Health](#download-and-install-azure-ad-connect-health-agent) na servery identity.
3. Prohlédněte si řídicí panel služby Azure AD Connect Health na adrese [https://aka.ms/aadconnecthealth](https://aka.ms/aadconnecthealth).

> [!NOTE]
> Nezapomeňte, že než na řídicím panelu služby Azure AD Connect Health uvidíte nějaká data, je třeba na cílové servery nainstalovat agenty služby Azure AD Connect Health.
>
>

## <a name="download-and-install-azure-ad-connect-health-agent"></a>Stažení a instalace agenta služby Azure AD Connect Health
* Ověřte, že [splňujete požadavky](how-to-connect-health-agent-install.md#requirements) pro službu Azure AD Connect Health.
* Začínáme s využitím Azure AD Connect Health pro službu AD FS
    * [Stažení agenta Azure AD Connect Health pro službu AD FS](https://go.microsoft.com/fwlink/?LinkID=518973)
    * [Pokyny k instalaci](how-to-connect-health-agent-install.md#installing-the-azure-ad-connect-health-agent-for-ad-fs)
* Začínáme s využitím Azure AD Connect Health pro synchronizaci
    * [Stažení a instalace nejnovější verze služby Azure AD Connect](https://go.microsoft.com/fwlink/?linkid=615771) Agent služby Health pro synchronizaci se nainstaluje jako součást instalace služby Azure AD Connect (verze 1.0.9125.0 nebo vyšší).
* Začínáme s využitím Azure AD Connect Health pro službu AD DS
    * [Stažení agenta služby Azure AD Connect Health pro službu AD DS](https://go.microsoft.com/fwlink/?LinkID=820540)
    * [Pokyny k instalaci](how-to-connect-health-agent-install.md#installing-the-azure-ad-connect-health-agent-for-ad-ds)


## <a name="azure-ad-connect-health-portal"></a>Portál služby Azure AD Connect Health
Portál služby Azure AD Connect Health umožňuje zobrazovat upozornění, monitorování výkonu a analýzy využívání. Hlavní okno služby Azure AD Connect Health najdete na následující adrese URL: https://aka.ms/aadconnecthealth. Je to v podstatě hlavní obrazovka této služby. V hlavním okně uvidíte možnost **Rychlý start**, služby v rámci Azure AD Connect Health a další možnosti konfigurace. Prohlédněte si následující snímek obrazovky a stručné vysvětlení uvedené pod ním. Jakmile nasadíte agenty, služba Health automaticky identifikuje služby, které Azure AD Connect Health monitoruje.

> [!NOTE]
> Informace o licencích najdete v [nejčastějších dotazech ke službě Azure AD Connect Health](reference-connect-health-faq.md) nebo na [stránce s cenami služby Azure AD](https://aka.ms/aadpricing).
    
![Portál služby Azure AD Connect Health](./media/whatis-hybrid-identity-health/portalsidebar.png)

* **Rychlý Start**: Když vyberete tuto možnost, **rychlý Start** se otevře okno. Výběrem možnosti **Získat nástroje** si můžete stáhnout agenta služby Azure AD Connect Health. Máte také přístup k dokumentaci a můžete nám poskytnout zpětnou vazbu.
* **Azure Active Directory Connect (sync)**: Tato možnost ukazuje vaše servery Azure AD Connect, které aktuálně monitoruje služba Azure AD Connect Health. Položka **Chyby synchronizace** ukazuje základní chyby synchronizace vaší první zaregistrované služby synchronizace podle kategorií. Po výběru položky **Služby synchronizace** se otevře okno s informacemi o vašich serverech Azure AD Connect. Další informace o těchto možnostech najdete v tématu [Používání služby Azure AD Connect Health pro synchronizaci](how-to-connect-health-sync.md).
* **Active Directory Federation Services**: Tato možnost ukazuje všechny služby AD FS, které aktuálně monitoruje služba Azure AD Connect Health. Po výběru některé instance se otevře okno s informacemi o příslušné instanci služby. Tyto informace zahrnují přehled, vlastnosti, výstrahy, monitorování a analýzu využití. Další informace o těchto možnostech najdete v tématu [Používání služby Azure AD Connect Health se službou AD FS](how-to-connect-health-adfs.md).
* **Active Directory Domain Services**: Tato možnost ukazuje všechny doménové struktury AD DS, které aktuálně monitoruje služba Azure AD Connect Health. Po výběru některé doménové struktury se otevře okno s informacemi o příslušné doménové struktuře. Tyto informace zahrnují přehled základních informací, řídicí panel Řadiče domény, řídicí panel Stav replikace, upozornění a monitorování. Další informace o těchto možnostech najdete v tématu [Používání služby Azure AD Connect Health se službou AD DS](how-to-connect-health-adds.md).
* **Konfigurace**: Tato část obsahuje možnosti, jak zapnout nebo vypnout následující:

  - Položka **Nastavení** obsahuje základní konfigurace vašich agentů. Automatický upgrade nastavení umožňuje automaticky aktualizovat na nejnovější verzi agenta Azure AD Connect Health: Které budou automaticky aktualizovány na nejnovější verze Agent Azure AD Connect Health Jakmile budou k dispozici. Tato možnost je ve výchozím nastavení zapnutá. Povolí Microsoftu přístup k datům o stavu adresáře Azure AD pro řešení potíží s pouze pro účely: Pokud je tato možnost povolena, Microsoft bude moci zobrazit stejná data, která se zobrazí. Tyto informace můžou pomoci při řešení potíží a problémů. Tato možnost je ve výchozím nastavení zakázána.
* **Řízení přístupu na základě role (IAM):** V této části se spravuje přístup k datům služby Connect Health na základě role. 

## <a name="next-steps"></a>Další kroky

- [Hardware a předpoklady](how-to-connect-install-prerequisites.md) 
- [Expresní nastavení](how-to-connect-install-express.md)
- [Vlastní nastavení](how-to-connect-install-custom.md)
- [Synchronizace hodnot hash hesel](how-to-connect-password-hash-synchronization.md)|
- [Předávací ověřování](how-to-connect-pta.md)
- [Azure AD Connect a federace](how-to-connect-fed-whatis.md)
- [Instalace agentů Azure AD Connect Health](how-to-connect-health-agent-install.md) 
- [Synchronizace služby Azure AD Connect](how-to-connect-sync-whatis.md)
