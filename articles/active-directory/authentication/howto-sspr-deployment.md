---
title: Důležité informace o nasazení samoobslužného hesla služby Azure Active Directory
description: Informace o aspektech nasazení a strategii pro úspěšnou implementaci samoobslužného resetování hesla Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: baselden
author: barbaraselden
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8bec28b98a8d2640b5a8034569d49077ce6b4177
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81450985"
---
# <a name="plan-an-azure-active-directory-self-service-password-reset-deployment"></a>Plánování samoobslužného resetování hesla služby Azure Active Directory

> [!IMPORTANT]
> Tento plán nasazení nabízí pokyny a osvědčené postupy pro nasazení samoobslužné resetování hesla Azure AD (SSPR).
>
> **Pokud jste a koncový uživatel a potřebujete se vrátit [https://aka.ms/sspr](https://aka.ms/sspr)ke svému účtu, přejděte na . **

[Samoobslužné resetování hesla (SSPR)](https://www.youtube.com/watch?v=tnb2Qf4hTP8) je funkce služby Azure Active Directory (AD), která uživatelům umožňuje resetovat hesla bez kontaktování pracovníků IT o pomoc. Uživatelé se mohou rychle odblokovat a pokračovat v práci bez ohledu na to, kde jsou nebo denní dobu. Tím, že zaměstnanci odblokují sami sebe, může vaše organizace snížit neproduktivní čas a vysoké náklady na podporu pro většinu běžných problémů souvisejících s heslem.

Program SSPR má následující klíčové funkce:

* Samoobslužná služba umožňuje koncovým uživatelům resetovat hesla, jejichž platnost vypršela nebo jejichž platnost nevypršela, aniž by museli kontaktovat správce nebo helpdesk s žádostí o podporu.
* [Password Writeback](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-writeback) umožňuje správu místních hesel a řešení uzamčení účtu prostředem cloudu.
* Sestavy aktivit správy hesel poskytují správcům přehled o aktivitě resetování hesla a registrace, ke kterým dochází v jejich organizaci.

Tato příručka pro nasazení ukazuje, jak naplánovat a otestovat zavedení samovlastního pr.

Chcete-li rychle zobrazit sspr v akci a pak se vrátit k pochopení další chod nasazení:

> [!div class="nextstepaction"]
> [Povolit samoobslužné resetování hesla (SSPR)](tutorial-enable-sspr.md)

## <a name="learn-about-sspr"></a>Další informace o sspr

Další informace o sspr. Podívejte se na [článek Jak to funguje: Samoobslužné resetování hesla Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks).

### <a name="key-benefits"></a>Klíčové výhody

Hlavní výhody povolení programu na jesad ové řešení je:

* **Správa nákladů**. Samoresetsnižuje náklady na podporu IT tím, že umožňuje uživatelům resetovat hesla na vlastní pěst. Také snižuje náklady na čas ztracený v důsledku ztracených hesel a uzamčení. 

* **Intuitivní uživatelské prostředí**. Poskytuje intuitivní jednorázový proces registrace uživatelů, který umožňuje uživatelům resetovat hesla a odblokovat účty na vyžádání z libovolného zařízení nebo umístění. SSPR umožňuje uživatelům vrátit se do práce rychleji a být produktivnější.

* **Flexibilita a bezpečnost**. Program SSPR umožňuje podnikům přístup k zabezpečení a flexibilitě, kterou poskytuje cloudová platforma. Správci mohou změnit nastavení tak, aby vyhovovalo novým požadavkům na zabezpečení, a tyto změny zavést uživatelům, aniž by došlo k narušení jejich přihlášení.

* **Robustní auditování a sledování využití**. Organizace může zajistit, aby podnikové systémy zůstaly zabezpečené, zatímco její uživatelé resetují svá vlastní hesla. Robustní protokoly auditu obsahují informace o každém kroku procesu resetování hesla. Tyto protokoly jsou k dispozici z rozhraní API a umožňují uživateli importovat data do systému monitorování incidentů a událostí (SIEM) zabezpečení.

### <a name="licensing"></a>Licencování

Služba Azure Active Directory je licencována pro jednotlivé uživatele, což znamená, že každý uživatel vyžaduje příslušnou licenci pro funkce, které používá. Doporučujeme skupinové licencování pro samoodovou licenci. 

Pokud chcete porovnat edice a funkce a povolit skupinové nebo uživatelské licencování, přečtěte si [článek Licenční požadavky na samoobslužné resetování hesla služby Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-licensing).

Další informace o cenách najdete v [tématu Ceny služby Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

### <a name="prerequisites"></a>Požadavky

* Funkční tenant Azure AD, který má přiřazenou alespoň zkušební licenci. V případě potřeby [si jej vytvořte zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Účet s oprávněními globálního správce


### <a name="training-resources"></a>Zdroje pro školení

| Zdroje a prostředky| Odkaz a popis |
| - | - |
| Videa| [Ušetřte svým uživatelům lepší škálovatelnost IT](https://youtu.be/g9RpRnylxS8) 
| |[Co je samoobslužné resetování hesla?](https://youtu.be/hc97Yx5PJiM)|
| |[Nasazení samoobslužného resetování hesla](https://www.youtube.com/watch?v=Pa0eyqjEjvQ&index=18&list=PLLasX02E8BPBm1xNMRdvP6GtA6otQUqp0)|
| |[Jak nakonfigurovat samoobslužné resetování hesla pro uživatele ve službě Azure AD?](https://azure.microsoft.com/resources/videos/self-service-password-reset-azure-ad/) |
| |[Jak [připravit uživatele na] registraci [jejich] informací o zabezpečení pro Službu Azure Active Directory](https://youtu.be/gXuh0XS18wA) |
| Online kurzy|[Správa identit ve službě Microsoft Azure Active Directory](https://www.pluralsight.com/courses/microsoft-azure-active-directory-managing-identities) Pomocí sspr poskytnout uživatelům moderní, chráněné prostředí. Podívejte se hlavně na modul["Správa uživatelů a skupin služby Azure Active Directory".](https://app.pluralsight.com/library/courses/microsoft-azure-active-directory-managing-identities/table-of-contents) |
|Placené kurzy Pluralsight |[Problematika správy identity a přístupu](https://www.pluralsight.com/courses/identity-access-management-issues) Seznamte se s problémy s IAM a zabezpečením, o kterých je třeba ve vaší organizaci vědět. Viz zejména modul "Jiné metody ověřování".|
| |[Začínáme s microsoft enterprise mobility suite](https://www.pluralsight.com/courses/microsoft-enterprise-mobility-suite-getting-started) Seznamte se s osvědčenými postupy pro rozšíření místních prostředků do cloudu způsobem, který umožňuje ověřování, autorizaci, šifrování a zabezpečené mobilní prostředí. Podívejte se hlavně na modul Konfigurace pokročilých funkcí služby Microsoft Azure Active Directory Premium.
|Kurzy |[Dokončení pilotního programu pro samoobslužné resetování hesla Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-pilot) |
| |[Povolení zpětného zápisu hesla](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-writeback) |
| |[Resetování hesla Azure AD z přihlašovací obrazovky pro Windows 10](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-windows) |
| Nejčastější dotazy|[Nejčastější dotazy týkající se správy hesel](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-faq) |


### <a name="solution-architecture"></a>Architektura řešení

Následující příklad popisuje architekturu řešení pro obnovení hesla pro běžná hybridní prostředí.

![diagram architektury řešení](./media/howto-sspr-deployment//solutions-architecture.png)

Popis pracovního postupu

Chcete-li obnovit heslo, přejděte uživatelé na [portál pro obnovení hesla](https://aka.ms/sspr). Musí ověřit dříve registrovanou metodu ověřování nebo metody, aby prokázali svou identitu. Pokud úspěšně resetují heslo, zahájí proces obnovení.

* Pro uživatele pouze pro cloud, samoreset ukládá nové heslo ve službě Azure AD. 

* Pro hybridní uživatele samoobslužné resetovací příkazy odepíše heslo do služby Active Directory on-prem prostřednictvím služby Azure AD Connect. 

Poznámka: Pro uživatele, kteří mají [zakázánu synchronizaci hash hesel (PHS),](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-phs) ukládají samoresetování hesla pouze do služby Active Directory.

### <a name="best-practices"></a>Osvědčené postupy

Můžete pomoci uživatelům zaregistrovat rychle nasazením sspr vedle jiné oblíbené aplikace nebo služby v organizaci. Tato akce vygeneruje velký objem přihlášení a bude řídit registraci.

Před nasazením sspr, můžete se rozhodnout určit číslo a průměrné náklady na každé volání resetování hesla. Tato data po nasazení můžete zobrazit hodnotu SSPR přináší do organizace.

#### <a name="enable-combined-registration-for-sspr-and-mfa"></a>Povolit kombinovanou registraci pro sspr a vícefaktorové finanční požadavky

Společnost Microsoft doporučuje, aby organizace povolily kombinované možnosti registrace pro samoobslužné ověřování pravosti a vícefaktorové ověřování. Pokud povolíte toto kombinované registrační prostředí, uživatelé potřebují pouze vybrat své registrační informace jednou povolit obě funkce.

Kombinované možnosti registrace nevyžaduje organizace povolit samoobslužné ověřování služeb při selhání a Azure vícefaktorové ověřování. Kombinovaná registrace poskytuje organizacím lepší uživatelské prostředí. Další informace naleznete [v tématu Kombinovaná registrace bezpečnostních informací](concept-registration-mfa-sspr-combined.md)

## <a name="plan-the-deployment-project"></a>Plánování projektu nasazení

Zvažte vaše organizační potřeby při určování strategie pro toto nasazení ve vašem prostředí.

### <a name="engage-the-right-stakeholders"></a>Zapojte správné zúčastněné strany

Když technologické projekty selžou, obvykle tak činí kvůli neodpovídajícím očekáváním ohledně dopadu, výsledků a odpovědností. Abyste se těmto nástrahám vyhnuli, [ujistěte se, že zapojujete správné zúčastněné strany](https://aka.ms/deploymentplans) a že role zúčastněných stran v projektu jsou dobře pochopeny dokumentováním zúčastněných stran a jejich vstupů a odpovědnosti za projekt.

#### <a name="required-administrator-roles"></a>Požadované role správce


| Obchodní role/Persona| Role Azure AD (v případě potřeby) |
| - | - |
| Asistenční službu úrovně 1| Správce hesel |
| Asistenční služba úrovně 2| Správce uživatele |
| Správce služby SSPR| Globální správce |


### <a name="plan-communications"></a>Plán komunikace

Komunikace je rozhodující pro úspěch každé nové služby. Měli byste proaktivně komunikovat s uživateli, jak se jejich zkušenosti změní, kdy se změní a jak získat podporu, pokud se vyskytnou problémy. Informace o tom, jak naplánovat strategii komunikace s koncovými uživateli, nawebujete materiály pro [obnovení samoobslužného hesla v centru pro stahování Microsoft.](https://www.microsoft.com/download/details.aspx?id=56768)

### <a name="plan-a-pilot"></a>Naplánujte si pilotní projekt

Doporučujeme, aby počáteční konfigurace sspr je v testovacím prostředí. Začněte s pilotní skupinou povolením programu SSPR pro podmnožinu uživatelů ve vaší organizaci. Viz [Doporučené postupy pro pilota](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-plans).

Pokud chcete vytvořit skupinu, přečtěte si, jak [vytvořit skupinu a přidat členy ve službě Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-groups-create-azure-portal). 

## <a name="plan-configuration"></a>Konfigurace plánu

Následující nastavení jsou vyžadována pro povolení sspr spolu s doporučenými hodnotami.

| Oblast | Nastavení | Hodnota |
| --- | --- | --- |
| **Vlastnosti sspr** | Samoobslužné resetování hesla povoleno | **Vybraná** skupina pro pilota / **Vše** pro výrobu |
| **Metody ověřování** | Metody ověřování potřebné k registraci | Vždy o 1 více, než je požadováno pro resetování |
|   | Metody ověřování potřebné k resetování | Jeden nebo dva |
| **Registrace** | Při přihlášení vyžadovat registraci uživatelů | Ano |
|   | Počet dní před vyzváním uživatelů k potvrzení ověřovacích informací | 90 – 180 dní |
| **Oznámení** | Upozornit uživatele na resetování hesla | Ano |
|   | Upozornit všechny správce na resetování hesla jiného správce | Ano |
| **Přizpůsobení** | Přizpůsobit odkaz helpdesku | Ano |
|   | E-mail s vlastní technickou pomocí nebo adresa URL | Web podpory nebo e-mailová adresa |
| **Místní integrace** | Zápis hesel do místní ad | Ano |
|   | Povolit uživatelům odemknout účet bez resetování hesla | Ano |

### <a name="sspr-properties"></a>Vlastnosti sspr

Při povolení programu SSPR zvolte příslušnou skupinu zabezpečení v pilotním prostředí.

* Chcete-li vynutit registraci sspr pro všechny, doporučujeme použít **možnost Vše.**
* V opačném případě vyberte příslušnou skupinu zabezpečení Azure AD nebo AD.

### <a name="authentication-methods"></a>Metody ověřování

Pokud je povoleno pole MSPR, uživatelé mohou obnovit své heslo pouze v případě, že mají data v metodách ověřování, které správce povolil. Metody zahrnují telefon, oznámení aplikace Authenticator, bezpečnostní otázky atd. Další informace naleznete v tématu [Co jsou metody ověřování?](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-methods).

Doporučujeme následující nastavení metody ověřování:

* Nastavte **metody ověřování potřebné k registraci** alespoň o jeden více, než je číslo potřebné k resetování. Povolení více násobného ověřování poskytuje uživatelům flexibilitu při resetování.

* Nastavte **počet metod potřebných k obnovení** na úroveň odpovídající vaší organizaci. Jeden vyžaduje nejmenší tření, zatímco dva mohou zvýšit vaši bezpečnostní pozici. 

Poznámka: Uživatel musí mít metody ověřování nakonfigurované v [zásadách a omezeních hesla ve službě Azure Active Directory](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy).

### <a name="registration-settings"></a>Nastavení registrace

Nastavit **vyžadovat registraci uživatelů při přihlášení** k **ano**. Toto nastavení vyžaduje, aby se uživatelé při přihlášení zaregistrovali a zajistili tak ochranu všech uživatelů.

Nastavte počet dní, po jejichž potvrzení **budou uživatelé vyzváni k opětovnému potvrzení ověřovacích údajů** na **90** až **180** dní, pokud vaše organizace nepotřebuje kratší časový rámec.

### <a name="notifications-settings"></a>Nastavení oznámení

Nakonfigurujte **uživatele na resetování hesla** i Upozornit všechny **správce, když ostatní správci resetují své heslo** na **Hodnotu Ano**. Výběrem **možnosti Ano** na obou zvyšuje tezi tím, že zajišťujete, že uživatelé jsou si vědomi při resetování hesla. Také zajišťuje, že všichni správci jsou vědomi, když správce změní heslo. Pokud uživatelé nebo správci obdrží oznámení a změnu neiniciovali, mohou okamžitě nahlásit potenciální problém se zabezpečením.

### <a name="customization-settings"></a>Nastavení vlastního nastavení

Je důležité přizpůsobit e-mail technické podpory nebo adresu URL, aby uživatelé, kteří mají problémy, mohli okamžitě získat pomoc. Tuto možnost nastavte na běžnou e-mailovou adresu technické podpory nebo webovou stránku, kterou uživatelé znají. 

Další informace naleznete [v tématu Customize the Azure AD functionality for self-service password reset](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-customization).

### <a name="password-writeback"></a>Zpětný zápis hesla

**Zpětný zápis hesla** je povolený s [Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-hybrid-identity) a zapisuje resetování hesla v cloudu zpět do existujícího místního adresáře v reálném čase. Další informace naleznete v tématu [Co je zpětný zápis hesla?](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-writeback)

Doporučujeme následující nastavení:

* Ujistěte se, že **je nastavení hesel pro zápis do místní hodu AD** nastaveno na **ano**. 
* Nastavte možnost **Povolit uživatelům odemknout účet bez resetování hesla** na **Ano**.

Ve výchozím nastavení Azure AD odemkne účty při obnovení hesla.

### <a name="administrator-password-setting"></a>Nastavení hesla správce

Účty správce mají zvýšená oprávnění. Místní správci rozlehlé sítě nebo domény nemohou resetovat svá hesla pomocí samoososti. Místní účty správců mají následující omezení:

* mohou měnit své heslo pouze v prostředí on-prem.
* nikdy nelze použít tajné otázky a odpovědi jako metodu k resetování hesla.

Doporučujeme nesynchronizovat účty správce služby Active Directory s Azure AD.

### <a name="environments-with-multiple-identity-management-systems"></a>Prostředí s více systémy správy identit

Některá prostředí mají více systémů správy identit. Místní správci identit, jako jsou Oracle AM a SiteMinder, vyžadují synchronizaci hesel se službou AD. Můžete to provést pomocí nástroje, jako je služba oznámení o změně hesla (PCNS) se Správcem identit (MIM) společnosti Microsoft. Informace o tomto složitějším scénáři naleznete v článku [Nasazení služby oznámení o změně hesla MIM na řadiči domény](https://docs.microsoft.com/microsoft-identity-manager/deploying-mim-password-change-notification-service-on-domain-controller).

## <a name="plan-testing-and-support"></a>Plánování testování a podpora

V každé fázi nasazení od počátečních pilotních skupin až po celou organizaci zajistěte, aby výsledky byly podle očekávání.

### <a name="plan-testing"></a>Plánování testování

Chcete-li zajistit, že vaše nasazení funguje podle očekávání, naplánujte sadu testovacích případů k ověření implementace. K posouzení testovacích případů potřebujete uživatele testu bez oprávnění správce s heslem. Pokud potřebujete vytvořit uživatele, přečtěte si informace o [přidání nových uživatelů do služby Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory).

Následující tabulka obsahuje užitečné testovací scénáře, které můžete použít k dokumentaci očekávaných výsledků vašich organizací na základě zásad.
<br>


| Obchodní případ| Očekávané výsledky |
| - | - |
| Portál SSPR je přístupný z podnikové sítě| Určeno vaší organizací |
| Portál SSPR je přístupný mimo podnikovou síť| Určeno vaší organizací |
| Resetování uživatelského hesla z prohlížeče, pokud uživatel není povolen pro resetování hesla| Uživatel nemá přístup k toku resetování hesla |
| Resetovat uživatelské heslo z prohlížeče, pokud se uživatel nezaregistroval k obnovení hesla| Uživatel nemá přístup k toku resetování hesla |
| Uživatel se přihlásí při vynucení k registraci resetování hesla| Vyzve uživatele k registraci informací o zabezpečení. |
| Uživatel se přihlásí po dokončení registrace resetování hesla.| Vyzve uživatele k registraci informací o zabezpečení. |
| Portál SSPR je přístupný, pokud uživatel nemá licenci| Je přístupný |
| Resetování uživatelského hesla z windows 10 Azure AD připojeno nebo hybridní Azure AD připojen zařízení zámek obrazovky| Uživatel může obnovit heslo |
| Údaje o registraci a používání sspr jsou k dispozici správcům téměř v reálném čase| Je k dispozici prostřednictvím protokolů auditu |

Můžete také odkazovat na [Complete out azure ad samoobslužné heslo resetování pilotní role](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-pilot). V tomto kurzu povolíte pilotní zavedení samoobslužného správě a součásti SSPR ve vaší organizaci a otestujete pomocí účtu bez oprávnění správce.

### <a name="plan-support"></a>Podpora plánu

Zatímco přisytná náhlavní líbací, která obvykle nevytváří problémy s uživateli, je důležité připravit pracovníky podpory na řešení problémů, které mohou nastat. Zatímco správce můžete obnovit heslo pro koncové uživatele prostřednictvím portálu Azure AD, je lepší pomoci vyřešit problém prostřednictvím samoobslužného procesu podpory.

Chcete-li povolit úspěch týmu podpory, můžete vytvořit nejčastější dotazy na základě otázek, které obdržíte od uživatelů. Tady je pár příkladů:

| Scénáře| Popis |
| - | - |
| Uživatel nemá k dispozici žádné registrované metody ověřování.| Uživatel se pokouší obnovit své heslo, ale nemá žádnou z metod ověřování, které zaregistroval, k dispozici (Příklad: nechal svůj mobilní telefon doma a nemá přístup k e-mailu) |
| Uživatel nepřijímá textovou zprávu nebo volání na své kanceláři nebo mobilním telefonu| Uživatel se pokouší ověřit svou identitu prostřednictvím textové zprávy nebo hovoru, ale nepřijímá textovou zprávu/hovor. |
| Uživatel nemá přístup k portálu pro resetování hesla| Uživatel chce obnovit své heslo, ale není povolen pro resetování hesla a nemůže získat přístup ke stránce pro aktualizaci hesel. |
| Uživatel nemůže nastavit nové heslo| Uživatel dokončí ověření během toku resetování hesla, ale nemůže nastavit nové heslo. |
| Uživatel i) na zařízení s Windows 10 neuvidí odkaz na resetovací heslo| Uživatel se pokouší obnovit heslo ze zamykací obrazovky Windows 10, ale zařízení se buď nepřipojí k Azure AD, nebo zásady zařízení Intune nejsou povolené. |

### <a name="plan-rollback"></a>Vrácení plánu zpět

Vrácení nasazení zpět:

* pro jednoho uživatele odeberte uživatele ze skupiny zabezpečení 

* pro skupinu odeberte skupinu z konfigurace programu SSPR

* Pro všechny, zakázat sspr pro klienta Azure AD

## <a name="deploy-sspr"></a>Nasazení samoobslužného resetování hesla

Před nasazením se ujistěte, že jste provedli následující kroky:

1. Vytvořeno a zahájeno provádění [komunikačního plánu](#plan-communications).

1. Bylo zjištěno příslušné [nastavení konfigurace](#plan-configuration).

1. Identifikovali uživatele a skupiny pro [pilotní](#plan-a-pilot) a produkční prostředí.

1. [Určeno nastavení konfigurace](#plan-configuration) pro registraci a samoobslužné služby.

1. [Nakonfigurovaný zpětný zápis hesla,](#password-writeback) pokud máte hybridní prostředí.


**Nyní jste připraveni k nasazení sspr!**

Informace o konfiguraci následujících oblastí naleznete v tématu [Povolení samoobslužného resetování hesla.](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-pilot#enable-self-service-password-reset)

1. [Metody ověřování](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-methods)

1. [Nastavení registrace](https://docs.microsoft.com/azure/active-directory/authentication/concept-registration-mfa-sspr-combined)

1. [Nastavení oznámení](#notifications-settings)

1. [Nastavení vlastního nastavení](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-customization)

1. [Místní integrace](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-writeback)

### <a name="enable-sspr-in-windows"></a>Povolení sspr v systému Windows
U počítačů se systémem Windows 7, 8, 8.1 a 10 můžete [uživatelům povolit resetování hesla na přihlašovací obrazovce systému Windows](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-windows)

## <a name="manage-sspr"></a>Správa sspr

Azure AD můžete poskytnout další informace o výkonu sspr prostřednictvím auditů a sestav.

### <a name="password-management-activity-reports"></a>Sestavy aktivit správy hesel 

Předem sestavené sestavy na webu Azure Portal můžete použít k měření výkonu samooselného pr. Pokud máte příslušnou licenci, můžete také vytvořit vlastní dotazy. Další informace najdete v tématu [Možnosti vytváření sestav pro správu hesel Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-reporting)

> [!NOTE]
>  Musíte být [globálním správcem](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)a musíte se přihlásit, aby byla tato data shromážděna pro vaši organizaci. Chcete-li se přihlásit, musíte alespoň jednou navštívit kartu Přehledy nebo protokoly auditu na webu Azure Portal. Do té doby se data neshromažďují pro vaši organizaci.

Protokoly auditu pro registraci a resetování hesla jsou k dispozici po dobu 30 dnů. Pokud auditování zabezpečení v rámci vaší společnosti vyžaduje delší uchovávání informací, protokoly je třeba exportovat a spotřebovávat do nástroje SIEM, jako je [Například Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-active-directory), Splunk nebo ArcSight.

![Snímek obrazovky s hlášením sspr](./media/howto-sspr-deployment/sspr-reporting.png)

### <a name="authentication-methods--usage-and-insights"></a>Metody ověřování – využití a přehledy

[Využití a přehledy](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-methods-usage-insights) umožňují pochopit, jak ve vaší organizaci fungují metody ověřování pro funkce, jako je Azure MFA a SSPR. Tato funkce vykazování poskytuje vaší organizaci prostředky k pochopení, jaké metody se registrují a jak je používat.

### <a name="troubleshoot"></a>Řešení potíží

* Odkazovat na [Poradce při potížích samoobslužné resetování hesla](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-troubleshoot) 

* Sledování [nejčastějších dotazů na správu hesel](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-faq) 

### <a name="helpful-documentation"></a>Užitečná dokumentace

* [Co jsou metody ověřování?](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-methods)

* [Jak to funguje: Azure AD samoobslužné heslo resetování?](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks)

* [Přizpůsobení funkce Azure AD pro samoobslužné resetování hesla](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-customization)

* [Zásady resetování hesel a omezení v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy)

* [Co je zpětný zápis hesla?](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-writeback)

## <a name="next-steps"></a>Další kroky

* Pokud chcete začít nasazovat [samoobslužné resetování hesla Azure AD, přečtěte si článek Povolení samoobslužného hesla Azure AD.](tutorial-enable-sspr.md)

* [Zvažte implementaci ochrany heslem Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad)

* [Zvažte implementaci azure ad inteligentní uzamčení](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-smart-lockout)