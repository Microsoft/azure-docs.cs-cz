---
title: Požadavky na nasazení pro Azure Active Directory Samoobslužné resetování hesla
description: Přečtěte si o požadavcích na nasazení a strategii pro úspěšnou implementaci samoobslužného resetování hesla služby Azure AD.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: baselden
author: barbaraselden
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 55fc4c0b5f3e2bbf57c972ad4a7fd2c9bdd3810e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104579191"
---
# <a name="plan-an-azure-active-directory-self-service-password-reset-deployment"></a>Plánování nasazení samoobslužného resetování hesla Azure Active Directory

> [!IMPORTANT]
> Tento plán nasazení nabízí doprovodné materiály a osvědčené postupy pro nasazení samoobslužného resetování hesla služby Azure AD (SSPR).
>
> **Pokud jste koncový uživatel a potřebujete se vrátit zpátky k účtu, pokračujte na [https://aka.ms/sspr](https://aka.ms/sspr)**.

[Samoobslužné resetování hesla (SSPR)](https://www.youtube.com/watch?v=pS3XwfxJrMo) je funkce Azure Active Directory (AD), která uživatelům umožňuje resetovat hesla bez kontaktování pracovníků oddělení IT o nápovědu. Uživatelé můžou rychle odblokovat a pokračovat v práci bez ohledu na to, kde jsou nebo jsou v denní době. Díky tomu, že si zaměstnanci můžou odblokovat vlastní odblokování, může vaše organizace snížit neproduktivní dobu a náklady na vysokou podporu pro nejčastější problémy související s heslem.

SSPR má následující klíčové funkce:

* Samoobslužná služba umožňuje koncovým uživatelům resetovat hesla, jejichž platnost vypršela nebo nevypršela, aniž by kontaktovali správce nebo technickou podporu.
* [Zpětný zápis hesla](./concept-sspr-writeback.md) umožňuje správu místních hesel a řešení uzamčení účtu i přes Cloud.
* Sestavy aktivit správy hesel dávají správcům přehled o resetování hesel a aktivitě registrace, ke kterým dochází ve své organizaci.

V této příručce pro nasazení se dozvíte, jak naplánovat a otestovat SSPR zavedení.

Chcete-li rychle zobrazit SSPR v akci a pak se vrátit k pochopení dalších hledisek nasazení:

> [!div class="nextstepaction"]
> [Povolit Samoobslužné resetování hesla (SSPR)](tutorial-enable-sspr.md)

## <a name="learn-about-sspr"></a>Další informace o SSPR

Přečtěte si další informace o SSPR. Podívejte [se, jak to funguje: Samoobslužné resetování hesla služby Azure AD](./concept-sspr-howitworks.md).

### <a name="key-benefits"></a>Klíčové výhody

Klíčové výhody povolování SSPR jsou:

* **Spravujte náklady**. SSPR snižuje náklady na podporu tím, že umožňuje uživatelům resetovat hesla sami. Také snižuje náklady na čas ztracený z důvodu ztracených hesel a uzamčení. 

* **Intuitivní uživatelské prostředí**. Nabízí intuitivní proces registrace uživatelů v jednom čase, který umožňuje uživatelům resetovat hesla a odblokovat účty na vyžádání z libovolného zařízení nebo místa. SSPR umožňuje uživatelům rychleji se vrátit k práci a zvýšit produktivitu.

* **Flexibilita a zabezpečení**. SSPR umožňuje podnikům přístup k zabezpečení a flexibilitě, které poskytuje cloudová platforma. Správci mohou změnit nastavení tak, aby vyhovovaly novým požadavkům na zabezpečení a tyto změny převedly uživatelům bez narušení jejich přihlášení.

* **Robustní auditování a sledování využití**. Organizace může zajistit, aby obchodní systémy zůstaly zabezpečené, zatímco její uživatelé resetují svoje vlastní hesla. Robustní protokoly auditu obsahují informace o každém kroku procesu resetování hesla. Tyto protokoly jsou k dispozici z rozhraní API a umožňují uživateli importovat data do SIEM (incidentu zabezpečení) podle vlastního výběru.

### <a name="licensing"></a>Licencování

Azure Active Directory je licence vázaná na každého uživatele, což znamená, že každý uživatel vyžaduje odpovídající licenci pro funkce, které používají. Pro SSPR doporučujeme licencování na základě skupin. 

Pokud chcete porovnat edice a funkce a povolit skupinu nebo uživatele licencování, přečtěte si téma [požadavky na licencování pro Samoobslužné resetování hesla služby Azure AD](./concept-sspr-licensing.md).

Další informace o cenách najdete v tématu [Azure Active Directory ceny](https://azure.microsoft.com/pricing/details/active-directory/).

### <a name="prerequisites"></a>Předpoklady

* Funkční tenant Azure AD, který má přiřazenou alespoň zkušební licenci. V případě potřeby [ho vytvořte zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Účet s oprávněními globálního správce


### <a name="training-resources"></a>Školicí materiály

| Zdroje informací| Odkaz a popis |
| - | - |
| Videa| [Poskytněte uživatelům lepší škálovatelnost IT](https://youtu.be/g9RpRnylxS8) 
| |[Co je samoobslužné resetování hesla?](https://youtu.be/hc97Yx5PJiM)|
| |[Nasazení samoobslužného resetování hesla](https://www.youtube.com/watch?v=Pa0eyqjEjvQ&index=18&list=PLLasX02E8BPBm1xNMRdvP6GtA6otQUqp0)|
| |[Jak povolit a nakonfigurovat SSPR ve službě Azure AD](https://www.youtube.com/watch?v=rA8TvhNcCvQ)|
| |[Jak nakonfigurovat Samoobslužné resetování hesla pro uživatele ve službě Azure AD?](https://azure.microsoft.com/resources/videos/self-service-password-reset-azure-ad/) |
| |[Postupy [Příprava uživatelů na] registrace [jejich] informací o zabezpečení pro Azure Active Directory](https://youtu.be/gXuh0XS18wA) |
| Online kurzy|[Správa identit v Microsoft Azure Active Directory](https://www.pluralsight.com/courses/microsoft-azure-active-directory-managing-identities) Využijte SSPR a poskytněte uživatelům moderní a chráněné prostředí. Viz téma "[správa Azure Active Directory uživatelů a skupin](https://app.pluralsight.com/library/courses/microsoft-azure-active-directory-managing-identities/table-of-contents)". |
|Placené kurzy Pluralsight |[Problémy se správou identit a přístupu](https://www.pluralsight.com/courses/identity-access-management-issues) Seznamte se s informacemi o IAM a zabezpečení, které jsou ve vaší organizaci důležité. Viz zejména modul "jiné metody ověřování".|
| |[Začínáme se sadou Microsoft Enterprise Mobility Suite](https://www.pluralsight.com/courses/microsoft-enterprise-mobility-suite-getting-started) Seznamte se s osvědčenými postupy pro rozšíření místních prostředků do cloudu způsobem, který umožňuje ověřování, autorizaci, šifrování a zabezpečené mobilní prostředí. Viz zejména "modul konfigurace pokročilých funkcí Microsoft Azure Active Directory Premium.
|Kurzy |[Dokončení zavedení pilotního resetování hesla samoobslužné služby Azure AD](./tutorial-enable-sspr.md) |
| |[Povolení zpětného zápisu hesla](./tutorial-enable-sspr-writeback.md) |
| |[Resetování hesla Azure AD z přihlašovací obrazovky pro Windows 10](./howto-sspr-windows.md) |
| Časté otázky|[Nejčastější dotazy ke správě hesel](./active-directory-passwords-faq.md) |


### <a name="solution-architecture"></a>Architektura řešení

Následující příklad popisuje architekturu řešení resetování hesla pro běžná hybridní prostředí.

![Diagram architektury řešení](./media/howto-sspr-deployment//solutions-architecture.png)

Popis pracovního postupu

Pokud chcete resetovat heslo, uživatelé přejdou na [portál pro resetování hesla](https://aka.ms/sspr). Musí ověřit, jestli se dřív zaregistrovala metoda nebo metody ověřování, aby bylo možné prokázat jejich identitu. Pokud heslo úspěšně resetuje, zahájí proces resetování.

* Pro uživatele jenom pro Cloud ukládá SSPR nové heslo do Azure AD. 

* Pro hybridní uživatele SSPR zapisuje zpátky heslo do služby Active Directory on-Prem prostřednictvím služby Azure AD Connect. 

Poznámka: pro uživatele, kteří mají zakázanou [synchronizaci hodnot hash hesel (kosmetice)](../hybrid/whatis-phs.md) , SSPR ukládá hesla pouze v Prem službě Active Directory.

### <a name="best-practices"></a>Osvědčené postupy

Uživatelům můžete rychle zaregistrovat nasazením SSPR spolu s jinou oblíbenou aplikací nebo službou v organizaci. Tato akce vygeneruje velký objem přihlášení a provede registraci jednotky.

Před nasazením SSPR se můžete rozhodnout, že určíte počet a průměrné náklady na každé volání resetování hesla. Tato data po nasazení můžete použít k zobrazení hodnoty, kterou SSPR přináší organizaci.

#### <a name="enable-combined-registration-for-sspr-and-mfa"></a>Povolit kombinovanou registraci pro SSPR a MFA

Společnost Microsoft doporučuje, aby organizace povolily kombinované prostředí registrace pro SSPR a vícefaktorové ověřování. Pokud povolíte toto kombinované registrační prostředí, uživatelé budou muset jenom jednou vybrat svoje registrační informace a povolit obě funkce.

Kombinované možnosti registrace nevyžadují, aby organizace povolovaly Multi-Factor Authentication SSPR i Azure AD. Kombinovaná registrace poskytuje organizacím lepší uživatelské prostředí. Další informace najdete v tématu [o registraci kombinovaných zabezpečení](concept-registration-mfa-sspr-combined.md) .

## <a name="plan-the-deployment-project"></a>Plánování projektu nasazení

Při určování strategie pro toto nasazení v prostředí zvažte potřeby vaší organizace.

### <a name="engage-the-right-stakeholders"></a>Zapojení správných zúčastněných stran

Když projekty technologie selžou, obvykle to vznikne z důvodu neshodných očekávání na dopad, výsledky a zodpovědnosti. Pokud se chcete těmto nástrah vyhnout, ujistěte se, že jste si [jisti, že jste připravují správné zúčastněné strany](../fundamentals/active-directory-deployment-plans.md) a že role účastníků v projektu jsou dobře srozumitelné při dokumentaci zúčastněných stran a jejich vstupu a accountabilities projektu.

#### <a name="required-administrator-roles"></a>Požadované role správce


| Obchodní role/osoby| Role Azure AD (v případě potřeby) |
| - | - |
| HelpDesk úrovně 1| Správce hesel |
| HelpDesk úrovně 2| Správce uživatelů |
| Správce SSPR| Globální správce |


### <a name="plan-communications"></a>Plán komunikace

Komunikace je zásadní pro úspěch jakékoli nové služby. Měli byste aktivně komunikovat s vašimi uživateli, jak se změní, když se změní, a jak získat podporu v případě, že dojde k problémům. V [materiálech pro uvedení samoobslužného resetování hesla na webu Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=56768) najdete nápady, jak naplánovat strategii komunikace koncovým uživatelům.

### <a name="plan-a-pilot"></a>Plánování pilotního projektu

Doporučujeme, aby počáteční konfigurace SSPR byla v testovacím prostředí. Začněte s pilotní skupinou tím, že povolíte SSPR pro podmnožinu uživatelů ve vaší organizaci. Podívejte [se na osvědčené postupy pro pilotní nasazení](../fundamentals/active-directory-deployment-plans.md).

Chcete-li vytvořit skupinu, přečtěte si téma [Vytvoření skupiny a přidání členů v Azure Active Directory](../fundamentals/active-directory-groups-create-azure-portal.md). 

## <a name="plan-configuration"></a>Konfigurace plánu

K povolení SSPR spolu s doporučenými hodnotami jsou nutná následující nastavení.

| Plošný | Nastavení | Hodnota |
| --- | --- | --- |
| **Vlastnosti SSPR** | Samoobslužné resetování hesla povoleno | **Vybraná** skupina pro pilotní/ **vše** pro produkci |
| **Metody ověřování** | Metody ověřování vyžadované k registraci | Vždy 1, než je vyžadováno pro resetování |
|   | Metody ověřování vyžadované k resetování | Jedna nebo dvě |
| **Evidenc** | Při přihlášení vyžadovat registraci uživatelů | Yes |
|   | Počet dní před vyzváním uživatelů k potvrzení ověřovacích informací | 90 – 180 dnů |
| **Oznámení** | Upozornit uživatele na resetování hesla | Yes |
|   | Upozornit všechny správce na resetování hesla jiného správce | Yes |
| **Přizpůsobení** | Přizpůsobení odkazu na helpdesk | Yes |
|   | E-mail nebo adresa URL vlastního helpdesku | Web podpory nebo e-mailová adresa |
| **Místní integrace** | Zápis hesel zpátky do místní služby AD | Yes |
|   | Povolí uživatelům odemknout účet bez resetování hesla. | Yes |

### <a name="sspr-properties"></a>Vlastnosti SSPR

Při povolování SSPR vyberte v pilotním prostředí příslušnou skupinu zabezpečení.

* Pro zajištění registrace SSPR pro všechny doporučujeme použít možnost **vše** .
* V opačném případě vyberte příslušnou skupinu zabezpečení Azure AD nebo AD.

### <a name="authentication-methods"></a>Metody ověřování

Když je povolený SSPR, uživatelé můžou resetovat heslo jenom v případě, že mají data přítomná v metodách ověřování, které správce povolil. Metody zahrnují telefon, oznámení o ověřovací aplikaci, bezpečnostní otázky atd. Další informace najdete v tématu [co jsou metody ověřování?](./concept-authentication-methods.md).

Doporučujeme následující nastavení metody ověřování:

* Nastavte **metody ověřování vyžadované k registraci** aspoň na jedno číslo, které se vyžaduje pro resetování. Povolení více ověřování dává uživatelům flexibilitu, když je potřeba resetovat.

* Nastavte **počet metod požadovaných k resetování** na úroveň odpovídající vaší organizaci. Jeden vyžaduje nejméně tření, zatímco dva můžou zvýšit stav zabezpečení. 

Poznámka: uživatel musí mít metody ověřování nakonfigurované v [zásadách a omezeních hesel v Azure Active Directory](./concept-sspr-policy.md).

### <a name="registration-settings"></a>Nastavení registrace

Nastavte **vyžadovat, aby se uživatelé zaregistrovali při přihlašování** k **Ano**. Toto nastavení vyžaduje, aby se uživatelé zaregistrovali při přihlašování a zajistili, že budou chráněni všichni uživatelé.

Nastavte **počet dní, než se uživatelům zobrazí výzva k potvrzení ověřovacích informací** do rozmezí **90** až **180** dnů, pokud vaše organizace nepotřebuje kratší časový rámec.

### <a name="notifications-settings"></a>Nastavení oznámení

Nakonfigurujte nastavení **upozorní uživatele na resetování hesla** a **upozorněte všechny správce, když si jiní správci resetují heslo** na **Ano**. Výběr možnosti **Ano** u obou zvyšuje zabezpečení tím, že zajistí, že uživatelé budou vědět, kdy se heslo resetuje. Také zajišťuje, že všichni správci budou vědět, když správce změní heslo. Pokud uživatelé nebo správci obdrží oznámení a nezahájili změnu, můžou hned ohlásit potenciální potíže se zabezpečením.

### <a name="customization-settings"></a>Nastavení přizpůsobení

Je důležité přizpůsobit e-mail nebo adresu URL helpdesku, aby se zajistilo, že uživatelé s problémy můžou získat pomoc hned. Tuto možnost nastavte na společnou e-mailovou adresu helpdesku nebo webovou stránku, se kterou uživatelé znají. 

Další informace najdete v tématu [přizpůsobení funkce Azure AD pro Samoobslužné resetování hesla](./howto-sspr-customization.md).

### <a name="password-writeback"></a>Zpětný zápis hesla

**Zpětný zápis hesla** je povolený pomocí [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) a zapisuje do stávajícího místního adresáře v reálném čase resetování hesla v cloudu zpátky do stávajícího místního adresáře. Další informace najdete v tématu [co je zpětný zápis hesla?](./concept-sspr-writeback.md)

Doporučujeme následující nastavení:

* Zajistěte, aby se **hesla pro zápis do místní služby AD** nastavila na **Ano**. 
* Nastavte možnost **povoleno uživatelům odemknout účet bez resetování hesla** na **Ano**.

Ve výchozím nastavení Azure AD odemkne účty, když provede resetování hesla.

### <a name="administrator-password-setting"></a>Nastavení hesla správce

Účty správců mají zvýšená oprávnění. Místní organizace nebo správci domény nemůžou resetovat hesla pomocí SSPR. Místní účty správců mají následující omezení:

* v prostředí Prem se dá změnit jenom heslo.
* skryté otázky a odpovědi nemůžete nikdy používat jako metodu pro resetování hesla.

Doporučujeme, abyste nesynchronizoval vaše účty pro správu služby Active Directory Prem s Azure AD.

### <a name="environments-with-multiple-identity-management-systems"></a>Prostředí s více systémy správy identit

Některá prostředí mají více systémů správy identit. Místní správci identit, jako je Oracle AM a SiteMinder, vyžadují synchronizaci se službou AD pro hesla. To můžete provést pomocí nástroje, jako je například služba PCNS (Password Change Notification Service) s Microsoft Identity Manager (MIM). Informace o tomto složitějším scénáři najdete v článku [nasazení služby MIM Password Change Notification Service na řadiči domény](/microsoft-identity-manager/deploying-mim-password-change-notification-service-on-domain-controller).

## <a name="plan-testing-and-support"></a>Plánování testování a podpory

V každé fázi nasazení od počátečních pilotních skupin v rámci organizace zajistěte, aby výsledky byly očekávané.

### <a name="plan-testing"></a>Plánování testování

Chcete-li zajistit, že vaše nasazení funguje podle očekávání, naplánujte sadu testovacích případů pro ověření implementace. Chcete-li vyhodnotit testovací případy, budete potřebovat testovacího uživatele bez správce s heslem. Pokud potřebujete vytvořit uživatele, přečtěte si téma [Přidání nových uživatelů do Azure Active Directory](../fundamentals/add-users-azure-active-directory.md).

Následující tabulka obsahuje užitečné testovací scénáře, které můžete použít k dokumentaci očekávaných výsledků vaší organizace na základě vašich zásad.
<br>


| Obchodní případ| Očekávané výsledky |
| - | - |
| Portál SSPR je přístupný v podnikové síti.| Určeno vaší organizací |
| Portál SSPR je přístupný z oblasti mimo podnikovou síť.| Určeno vaší organizací |
| Resetování hesla uživatele z prohlížeče, pokud uživatel není povolen pro resetování hesla| Uživatel nemůže získat přístup k toku resetování hesla. |
| Resetovat heslo uživatele z prohlížeče, pokud uživatel není zaregistrovaný k resetování hesla| Uživatel nemůže získat přístup k toku resetování hesla. |
| Přihlášení uživatele, když se vynutilo registrace resetování hesla| Vyzve uživatele k registraci informací o zabezpečení. |
| Uživatel se přihlásí, když se registrace resetování hesla dokončí.| Vyzve uživatele k registraci informací o zabezpečení. |
| Portál SSPR je dostupný, když uživatel nemá licenci.| Je přístupné |
| Resetování hesla uživatele z připojené aplikace Windows 10 Azure AD nebo uzamčené obrazovky zařízení připojené ke službě Azure AD| Uživatel může resetovat heslo. |
| SSPR registrace a data o využití jsou k dispozici správcům téměř v reálném čase.| Je k dispozici prostřednictvím protokolů auditu |

Můžete se také podívat na [kompletní zkušební nasazení samoobslužného resetování hesla služby Azure AD](./tutorial-enable-sspr.md). V tomto kurzu umožníte pilotní zavedení SSPR ve vaší organizaci a testování pomocí účtu bez oprávnění správce.

### <a name="plan-support"></a>Plán podpory

I když SSPR obvykle nevytváří problémy s uživatelem, je důležité připravit pracovníky podpory, aby mohli řešit problémy, které mohou nastat. I když může správce resetovat heslo pro koncové uživatele prostřednictvím portálu Azure AD, je lepší tento problém vyřešit prostřednictvím samoobslužného procesu podpory.

Pokud chcete, aby váš tým podpory byl úspěšný, můžete vytvořit Nejčastější dotazy na základě dotazů, které obdržíte od uživatelů. Tady je pár příkladů:

| Scénáře| Description |
| - | - |
| Uživatel nemá k dispozici žádné registrované metody ověřování.| Uživatel se pokusí resetovat heslo, ale nemá žádnou z metod ověřování, které jsou k dispozici (například: opustil svůj mobilní telefon na domácím a nemůže získat přístup k e-mailu). |
| Uživatel nepřijímá text nebo volá na svém Office nebo na mobilním telefonu.| Uživatel se snaží ověřit svoji identitu prostřednictvím textu nebo volání, ale nepřijímá text nebo volání. |
| Uživatel nemá přístup k portálu pro resetování hesla.| Uživatel chce resetovat heslo, ale nemá povoleno resetování hesla a nemůže získat přístup k této stránce, aby mohl aktualizovat hesla. |
| Uživatel nemůže nastavit nové heslo.| Uživatel dokončil ověřování během toku resetování hesla, ale nemůže nastavit nové heslo. |
| Uživatel nevidí odkaz pro resetování hesla na zařízení s Windows 10.| Uživatel se pokusí resetovat heslo z zamykací obrazovky Windows 10, ale zařízení buď není připojené ke službě Azure AD, nebo zásady zařízení Intune nejsou povolené. |

### <a name="plan-rollback"></a>Naplánování vrácení zpět

Postup vrácení nasazení zpět:

* pro jednoho uživatele Odeberte uživatele ze skupiny zabezpečení. 

* pro skupinu odeberte skupinu z konfigurace SSPR.

* Pro všechny zakažte SSPR pro tenanta Azure AD.

## <a name="deploy-sspr"></a>Nasazení samoobslužného resetování hesla

Před nasazením se ujistěte, že jste provedli následující akce:

1. Bylo vytvořeno a zahájeno provádění vašeho [plánu komunikace](#plan-communications).

1. Bylo zjištěno vhodné [nastavení konfigurace](#plan-configuration).

1. Identifikují se uživatelé a skupiny pro [pilotní](#plan-a-pilot) a produkční prostředí.

1. Bylo [zjištěno nastavení konfigurace](#plan-configuration) pro registraci a samoobslužnou službu.

1. Pokud máte hybridní prostředí, [nakonfiguruje se zpětný zápis hesla](#password-writeback) .


**Nyní jste připraveni k nasazení SSPR.**

Kompletní podrobné pokyny ke konfiguraci následujících oblastí najdete v tématu [Povolení samoobslužného resetování hesla](./tutorial-enable-sspr.md#enable-self-service-password-reset) .

1. [Metody ověřování](./concept-authentication-methods.md)

1. [Nastavení registrace](./concept-registration-mfa-sspr-combined.md)

1. [Nastavení oznámení](#notifications-settings)

1. [Nastavení přizpůsobení](./howto-sspr-customization.md)

1. [Místní integrace](./tutorial-enable-sspr-writeback.md)

### <a name="enable-sspr-in-windows"></a>Povolení SSPR ve Windows
V počítačích se systémem Windows 7, 8, 8,1 a 10 můžete [uživatelům povolit resetování hesla na přihlašovací obrazovce Windows](./howto-sspr-windows.md) .

## <a name="manage-sspr"></a>Správa SSPR

Azure AD může poskytovat další informace o výkonu SSPR prostřednictvím auditů a sestav.

### <a name="password-management-activity-reports"></a>Sestavy aktivit správy hesel 

K měření výkonu SSPR můžete použít předem připravené sestavy na Azure Portal. Pokud máte patřičnou licenci, můžete také vytvořit vlastní dotazy. Další informace najdete v tématu [Možnosti vytváření sestav pro správu hesel služby Azure AD](./howto-sspr-reporting.md) .

> [!NOTE]
>  Musíte být [globálním správcem](../roles/permissions-reference.md)a musíte se vyjádřit, že se tato data budou shromažďovat pro vaši organizaci. Pokud se chcete přihlásit, musíte navštívit kartu pro vytváření sestav nebo protokoly auditu na portálu Azure Portal aspoň jednou. Data nebudou pro vaši organizaci shromažďována.

Protokoly auditu pro registraci a resetování hesla jsou k dispozici po dobu 30 dnů. Pokud audit zabezpečení v rámci vaší společnosti vyžaduje delší dobu uchovávání, je nutné tyto protokoly exportovat a spotřebovat do nástroje SIEM, jako je například [Azure Sentinel](../../sentinel/connect-azure-active-directory.md), Splunk nebo ArcSight.

![Snímek obrazovky pro vytváření sestav SSPR](./media/howto-sspr-deployment/sspr-reporting.png)

### <a name="authentication-methods--usage-and-insights"></a>Metody ověřování – využití a přehledy

[Využití a přehledy](./howto-authentication-methods-activity.md) umožňují pochopit, jak metody ověřování pro funkce, jako je Azure AD MFA a SSPR, fungují ve vaší organizaci. Tato funkce vytváření sestav poskytuje vaší organizaci prostředky pro pochopení, jaké metody se registrují a jak je používat.

### <a name="troubleshoot"></a>Řešení potíží

* Přečtěte si téma [řešení potíží samoobslužného resetování hesla](./troubleshoot-sspr.md) . 

* Dodržujte [Nejčastější dotazy týkající se správy hesel](./active-directory-passwords-faq.md) 

### <a name="helpful-documentation"></a>Užitečná dokumentace

* [Co jsou metody ověřování?](./concept-authentication-methods.md)

* [Jak to funguje: Samoobslužné resetování hesla služby Azure AD?](./concept-sspr-howitworks.md)

* [Přizpůsobení funkce Azure AD pro Samoobslužné resetování hesla](./howto-sspr-customization.md)

* [Zásady resetování hesel a omezení v Azure Active Directory](./concept-sspr-policy.md)

* [Co je zpětný zápis hesla?](./concept-sspr-writeback.md)

## <a name="next-steps"></a>Další kroky

* Pokud chcete začít nasazovat SSPR, přečtěte si téma [Povolení samoobslužného resetování hesla služby Azure AD](tutorial-enable-sspr.md) .

* [Zvažte implementaci ochrany heslem Azure AD.](./concept-password-ban-bad.md)

* [Zvažte implementaci inteligentního uzamčení Azure AD.](./howto-password-smart-lockout.md)