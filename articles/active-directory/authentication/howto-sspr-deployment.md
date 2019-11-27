---
title: Nasazení samoobslužného resetování hesla – Azure Active Directory
description: Strategie pro úspěšnou implementaci samoobslužného resetování hesla služby Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: b9f340ad12fbf26190a17bc4df97bfc95473093c
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2019
ms.locfileid: "74381290"
---
# <a name="deploy-azure-ad-self-service-password-reset"></a>Nasazení samoobslužného resetování hesla Azure AD

> [!NOTE]
> Tato příručka vysvětluje Samoobslužné resetování hesla a způsob jejich nasazení. Pokud hledáte Nástroj pro Samoobslužné resetování hesla, který se vrátí k vašemu účtu, pokračujte na [https://aka.ms/sspr](https://aka.ms/sspr). 

Samoobslužné resetování hesla (SSPR) je funkce Azure Active Directory, která zaměstnancům umožňuje resetovat hesla bez nutnosti kontaktovat pracovníky oddělení IT. Před použitím služby se zaměstnanci musí zaregistrovat pro Samoobslužné resetování hesla nebo je zaregistrovat. Během registrace zvolí zaměstnanec jednu nebo více metod ověřování povolených organizací.

SSPR umožňuje zaměstnancům rychle se odblokovat a pokračovat v práci bez ohledu na to, kde jsou, nebo v denní době. Díky tomu, že uživatelé můžou odblokovat samy sebe, může vaše organizace zkrátit neproduktivní dobu a náklady na vysokou podporu pro nejběžnější problémy související s heslem.

Pomůže uživatelům rychle se zaregistrovat tím, že nasadí SSPR spolu s jinou aplikací nebo službou ve vaší organizaci. Tato akce vygeneruje velký objem přihlášení a provede registraci jednotky.

Před nasazením SSPR organizace mohou chtít určit, kolik v průběhu času proběhne volání technické podpory pro resetování hesla, a průměrné náklady na každé volání. Můžou k tomu použít toto nasazení dat, aby se zobrazila hodnota, kterou SSPR přináší vaší organizaci.  

## <a name="how-sspr-works"></a>Jak funguje SSPR

1. Když se uživatel pokusí resetovat heslo, musí ověřit jejich dříve registrovanou metodu ověřování nebo metody, aby prokázal jejich identitu.
1. Pak uživatel zadá nové heslo.
   1. V případě pouze cloudových uživatelů je nové heslo uloženo v Azure Active Directory. Další informace najdete v článku [jak SSPR funguje](concept-sspr-howitworks.md#how-does-the-password-reset-portal-work).
   1. Pro hybridní uživatele se heslo zapisuje zpátky do místní služby Active Directory prostřednictvím služby Azure AD Connect. Další informace najdete v článku [co je zpětný zápis hesla](concept-sspr-writeback.md#how-password-writeback-works).

## <a name="licensing-considerations"></a>Požadavky na licencování

Azure Active Directory je licence vázaná na každého uživatele, což znamená, že každý uživatel musí mít odpovídající licenci na funkce, které využívají.

Další informace o licencování najdete na stránce s [cenami Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/) .

## <a name="enable-combined-registration-for-sspr-and-mfa"></a>Povolit kombinovanou registraci pro SSPR a MFA

Společnost Microsoft doporučuje, aby organizace povolily kombinované prostředí registrace pro SSPR a vícefaktorové ověřování. Pokud povolíte toto kombinované registrační prostředí, uživatelé budou muset jenom jednou vybrat svoje registrační informace a povolit obě funkce.

![Registrace informací o kombinovaném zabezpečení](./media/howto-sspr-deployment/combined-security-info.png)

V kombinovaném prostředí pro registraci není nutné, aby organizace povolily SSPR i službu Azure Multi-Factor Authentication. Kombinované prostředí pro registraci poskytuje organizacím lepší uživatelské prostředí v porovnání s tradičními jednotlivými komponentami. Další informace o kombinované registraci a o tom, jak ji povolit, najdete v článku [o registraci informací o zabezpečení (Preview)](concept-registration-mfa-sspr-combined.md) .

## <a name="plan-the-configuration"></a>Plánování konfigurace

K povolení SSPR spolu s doporučenými hodnotami jsou nutná následující nastavení.

| Oblast | Nastavení | Hodnota |
| --- | --- | --- |
| **Vlastnosti SSPR** | Samoobslužné resetování hesla povoleno | **Vybraná** skupina pro pilotní/ **vše** pro produkci |
| **Metody ověřování** | Metody ověřování vyžadované k registraci | Vždy 1, než je vyžadováno pro resetování |
|   | Metody ověřování vyžadované k resetování | Jedna nebo dvě |
| **Registrace** | Při přihlášení vyžadovat registraci uživatelů | Ano |
|   | Počet dní, než se uživatelům zobrazí výzva k opětovnému potvrzení ověřovacích informací | 90 – 180 dnů |
| **Oznámení** | Upozornit uživatele na resetování hesla | Ano |
|   | Upozornit všechny správce na resetování hesla jiného správce | Ano |
| **Uživatelských** | Přizpůsobení odkazu na helpdesk | Ano |
|   | E-mail nebo adresa URL vlastního helpdesku | Web podpory nebo e-mailová adresa |
| **Místní integrace** | Zápis hesel zpátky do místní služby AD | Ano |
|   | Povolí uživatelům odemknout účet bez resetování hesla. | Ano |

### <a name="sspr-properties-recommendations"></a>Doporučení k vlastnostem SSPR

Při povolování samoobslužného resetování hesla vyberte skupinu zabezpečení, která se má použít během pilotního nasazení.

Pokud plánujete službu spustit podrobněji, doporučujeme, abyste použili možnost vše a vynutili SSPR pro všechny v organizaci. Pokud nemůžete nastavit na vše, vyberte příslušnou skupinu zabezpečení Azure AD nebo skupinu AD synchronizovanou do Azure AD.

### <a name="authentication-methods"></a>Metody ověřování

Nastavte metody ověřování vyžadované k registraci aspoň na jedno číslo, které se vyžaduje pro resetování. Povolení více uživatelům umožní pružnou flexibilitu, když je potřeba resetovat.

Nastavte **počet metod požadovaných k resetování** na úroveň odpovídající vaší organizaci. Jeden vyžaduje nejméně tření, zatímco dva můžou zvýšit stav zabezpečení.

Podrobné informace o tom, jaké metody ověřování jsou k dispozici pro SSPR, předem definované bezpečnostní otázky a jak vytvářet vlastní bezpečnostní otázky, najdete v tématu [co jsou metody ověřování](concept-authentication-methods.md) .

### <a name="registration-settings"></a>Nastavení registrace

Nastavte **vyžadovat, aby se uživatelé zaregistrovali při přihlašování** k **Ano**. Toto nastavení znamená, že uživatelé mají při přihlašování nuceně zaregistrovat, aby všichni uživatelé byli chráněni.

Nastavte **počet dní, než se uživatelům zobrazí výzva k opětovnému potvrzení ověřovacích informací** do rozmezí **90** až **180** dnů, pokud vaše organizace nepotřebuje kratší časový rámec.

### <a name="notifications-settings"></a>Nastavení oznámení

Nakonfigurujte nastavení **upozorní uživatele na resetování hesla** a **upozorněte všechny správce, když si jiní správci resetují heslo** na **Ano**. Výběr možnosti **Ano** u obou zvyšuje zabezpečení tím, že zajistí, že uživatelé budou vědět, kdy se heslo obnovilo, a že všichni správci mají vědět, když správce změní heslo. Pokud uživatelé nebo správci obdrží takové oznámení a nezahájili změnu, můžou hned ohlásit potenciální porušení zabezpečení.

### <a name="customization"></a>Přizpůsobení

Je důležité přizpůsobit **e-mail nebo adresu URL helpdesku** , aby uživatelé, kteří se s nimi setkávají, mohli rychle získat pomoc. Tuto možnost nastavte na společnou e-mailovou adresu helpdesku nebo webovou stránku, se kterou uživatelé znají.

### <a name="on-premises-integration"></a>Místní integrace

Pokud máte hybridní prostředí, zajistěte, aby se **hesla pro zápis zpět do místní služby AD** nastavila na **Ano**. Nastavte také možnost dovolit uživatelům odemknout účet bez resetování hesla na Ano, protože jim nabízí větší flexibilitu.

### <a name="changingresetting-passwords-of-administrators"></a>Změna nebo resetování hesel správců

Účty správců jsou speciální účty se zvýšenými oprávněními. Pokud je chcete zabezpečit, platí následující omezení pro změnu hesel správců:

- Místní správci nebo správci domény nemůžou resetovat svoje heslo pomocí SSPR. Můžou změnit jenom svoje heslo ve svém místním prostředí. Proto doporučujeme, abyste v Azure AD nesynchronizoval účty správce služby AD Prem.
- Správce nemůže použít tajné otázky & odpovědi jako metodu pro resetování hesla.

### <a name="environments-with-multiple-identity-management-systems"></a>Prostředí s více systémy správy identit

Pokud existuje více systémů správy identit v rámci prostředí, jako jsou místní správci identit, jako jsou například Oracle AM, SiteMinder nebo jiné systémy, může být nutné synchronizovat hesla zapsaná ve službě Active Directory pomocí nástroje, jako je například Služba PCNS (Password Change Notification Service) s Microsoft Identity Manager (MIM). Informace o tomto složitějším scénáři najdete v článku [nasazení služby MIM Password Change Notification Service na řadiči domény](https://docs.microsoft.com/microsoft-identity-manager/deploying-mim-password-change-notification-service-on-domain-controller).

## <a name="plan-deployment-and-support-for-sspr"></a>Plánování nasazení a podpory pro SSPR

### <a name="engage-the-right-stakeholders"></a>Zapojení správných zúčastněných stran

Když projekty technologie selžou, obvykle to vznikne z důvodu neshodných očekávání na dopad, výsledky a zodpovědnosti. Aby se tyto nástrahy nezobrazovaly, ujistěte se, že jste si jisti, že jste připravují správné zúčastněné strany a že role účastníků v projektu jsou dobře srozumitelné při dokumentaci zúčastněných stran a jejich vstupu a zodpovědnosti projektu.

### <a name="communications-plan"></a>Plán komunikace

Komunikace je zásadní pro úspěch jakékoli nové služby. Proaktivně komunikujte s uživateli, jak používat službu a co můžou dělat, aby vám pomohly získat pomoc, pokud něco nefunguje podle očekávání. V [materiálech pro uvedení samoobslužného resetování hesla na webu Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=56768) najdete nápady, jak naplánovat strategii komunikace koncovým uživatelům.

### <a name="testing-plan"></a>Plán testování

Chcete-li zajistit, že vaše nasazení funguje podle očekávání, měli byste naplánovat sadu testovacích případů, které použijete k ověření implementace. Následující tabulka obsahuje několik užitečných testovacích scénářů, které můžete použít k dokumentaci očekávaných výsledků vaší organizace na základě vašich zásad.

| Obchodní zdůvodnění | Očekávaný výsledek |
| --- | --- |
| Portál SSPR je přístupný v podnikové síti. | Určeno vaší organizací |
| Portál SSPR je přístupný z oblasti mimo podnikovou síť. | Určeno vaší organizací |
| Resetování hesla uživatele z prohlížeče, pokud uživatel není povolen pro resetování hesla | Uživatel nemůže získat přístup k toku resetování hesla. |
| Resetovat heslo uživatele z prohlížeče, pokud uživatel není zaregistrovaný k resetování hesla | Uživatel nemůže získat přístup k toku resetování hesla. |
| Uživatel se přihlásí, když se vynutila registrace resetování hesla. | Uživatel je vyzván k registraci informací o zabezpečení. |
| Uživatel se přihlásí, když se dokončila registrace resetování hesla. | Uživatel není vyzván k registraci informací o zabezpečení. |
| Portál SSPR je dostupný, když uživatel nemá licenci. | Je přístupné |
| Resetování hesla uživatele z připojeného zařízení Windows 10 Azure AD nebo zamykací obrazovky připojené k Azure AD po registraci uživatele | Uživatel může resetovat heslo. |
| SSPR registrace a data o využití jsou k dispozici správcům téměř v reálném čase. | Je k dispozici prostřednictvím protokolů auditu |

### <a name="support-plan"></a>Plán podpory

I když SSPR obvykle nevytváří problémy s uživatelem, je důležité mít pracovníky podpory připravené na řešení problémů, které mohou nastat.

Správce může změnit nebo resetovat heslo pro koncové uživatele prostřednictvím portálu Azure AD, což je lepší při řešení tohoto problému prostřednictvím samoobslužného procesu podpory.

V části provozní příručka tohoto dokumentu vytvořte seznam pomocných případů a jejich pravděpodobný příčinu a vytvořte průvodce pro řešení.

### <a name="auditing-and-reporting"></a>Auditování a vytváření sestav

Po nasazení mnoho organizací chce zjistit, jak nebo jestli se ve skutečnosti používá Samoobslužné resetování hesla (SSPR). Funkce vytváření sestav, kterou Azure Active Directory (Azure AD), vám pomůže odpovědět na otázky pomocí předem vytvořených sestav.

Protokoly auditu pro registraci a resetování hesla jsou k dispozici po dobu 30 dnů. Proto pokud audit zabezpečení v rámci podniku vyžaduje delší dobu uchovávání, je nutné tyto protokoly exportovat a spotřebovat do nástroje SIEM, jako je například [Azure Sentinel](../../sentinel/connect-azure-active-directory.md), Splunk nebo ArcSight.

V tabulce, podobně jako v následujícím příkladu, zdokumentujte plán zálohování, systém a příslušné strany. Nesmíte oddělit auditování a zálohy vytváření sestav, ale měli byste mít samostatnou zálohu, ze které můžete obnovit problém.

|   | Frekvence stahování | Cílový systém | Zodpovědná strana |
| --- | --- | --- | --- |
| Auditování zálohování |   |   |   |
| Zálohování vytváření sestav |   |   |   |
| Záloha zotavení po havárii |   |   |   |

## <a name="implementation"></a>Implementace

Implementace probíhá ve třech fázích:

- Konfigurace uživatelů a licencí
- Konfigurace Azure AD SSPR pro registraci a samoobslužné služby
- Konfigurace Azure AD Connect pro zpětný zápis hesla

### <a name="communicate-the-change"></a>Oznámit změnu

Zahajte implementaci komunikačního plánu, který jste vytvořili ve fázi plánování.

### <a name="ensure-groups-are-created-and-populated"></a>Zajistěte vytváření a naplnění skupin.

Odkažte na část metody ověřování hesla plánování a ujistěte se, že skupiny pro pilotní nebo produkční implementaci jsou k dispozici a že se do těchto skupin přidaly všichni odpovídající uživatelé.

### <a name="apply-licenses"></a>Použít licence

Skupiny, které se chystáte implementovat, musí mít přiřazenou licenci Azure AD Premium. Licence můžete přiřadit přímo ke skupině nebo můžete použít existující zásady licencování, jako je například PowerShell nebo licencování na základě skupin.

Informace o přiřazování licencí skupinám uživatelů najdete v článku [přiřazení licencí uživatelům podle členství ve skupině v Azure Active Directory](../users-groups-roles/licensing-groups-assign.md).

### <a name="configure-sspr"></a>Konfigurace SSPR

#### <a name="enable-groups-for-sspr"></a>Povolení skupin pro SSPR

1. Přístup k Azure Portal s účtem správce.
1. Vyberte všechny služby a do pole Filtr zadejte Azure Active Directory a pak vyberte Azure Active Directory.
1. V okně služby Active Directory vyberte resetování hesla.
1. V podokně Vlastnosti vyberte možnost vybrané. Pokud chcete, aby všichni uživatelé byli povoleni, vyberte vše.
1. V okně výchozí zásady resetování hesla zadejte název první skupiny, vyberte ji a potom klikněte na vybrat v dolní části obrazovky a v horní části obrazovky vyberte Uložit.
1. Tento postup opakujte pro každou skupinu.

#### <a name="configure-the-authentication-methods"></a>Konfigurace metod ověřování

Odkaz na vaše plánování z části metody ověřování hesla plánování v tomto dokumentu.

1. Vyberte možnost registrace, v části vyžadovat, aby se uživatel zaregistroval při přihlašování, vyberte Ano a pak nastavte počet dní před vypršením platnosti a pak vyberte Uložit.
1. Vyberte oznámení a nakonfigurujte podle vašeho plánu a pak vyberte Uložit.
1. Vyberte vlastní nastavení a nakonfigurujte podle vašeho plánu a pak vyberte Uložit.
1. Vyberte místní integraci a nakonfigurujte podle vašeho plánu a pak vyberte Uložit.

### <a name="enable-sspr-in-windows"></a>Povolení SSPR ve Windows

Zařízení s Windows 10, která používají verzi 1803 nebo vyšší, která jsou připojená k Azure AD nebo se připojila k hybridní službě Azure AD, můžou resetovat hesla na přihlašovací obrazovce pro Windows. Informace a kroky pro konfiguraci této možnosti najdete v článku [resetování hesla Azure AD na přihlašovací obrazovce](tutorial-sspr-windows.md) .

### <a name="configure-password-writeback"></a>Konfigurace zpětného zápisu hesla

Postup konfigurace zpětného zápisu hesla pro vaši organizaci najdete v článku [Postupy: Konfigurace zpětného zápisu hesla](howto-sspr-writeback.md).

## <a name="manage-sspr"></a>Správa SSPR

Požadované role pro správu funkcí přidružených k samoobslužnému resetování hesla.

| Obchodní role/osoby | Role Azure AD (v případě potřeby) |
| :---: | :---: |
| HelpDesk úrovně 1 | Správce hesel |
| HelpDesk úrovně 2 | Správce uživatele |
| Správce SSPR | Globální správce |

### <a name="support-scenarios"></a>Scénáře podpory

Pokud chcete vašemu týmu podpory povolit úspěch, můžete vytvořit Nejčastější dotazy na základě otázek, které od uživatelů dostanete. Následující tabulka obsahuje běžné scénáře podpory.

| Scénáře | Popis |
| --- | --- |
| Uživatel nemá k dispozici žádné registrované metody ověřování. | Uživatel se pokusí resetovat heslo, ale nemá žádnou z metod ověřování, které zaregistroval (například: opustil mobilní telefon v domácnosti a nemá přístup k e-mailu). |
| Uživatel nepřijímá text ani nevolá na svém Office nebo na mobilním telefonu. | Uživatel se snaží ověřit svoji identitu prostřednictvím textu nebo volání, ale nepřijímá text nebo volání. |
| Uživatel nemá přístup k portálu pro resetování hesla. | Uživatel chce resetovat heslo, ale nemá povoleno resetování hesla, a proto nemůže získat přístup k této stránce, aby mohl aktualizovat hesla. |
| Uživatel nemůže nastavit nové heslo. | Uživatel dokončil ověřování během toku resetování hesla, ale nemůže nastavit nové heslo. |
| Uživatel nevidí odkaz pro resetování hesla na zařízení s Windows 10. | Uživatel se pokusí resetovat heslo z zamykací obrazovky Windows 10, ale zařízení buď není připojené ke službě Azure AD, nebo není povolené zásady zařízení Intune. |

Pro další řešení potíží můžete také zahrnout další informace, jako je třeba následující.

- Které skupiny jsou povolené pro SSPR.
- Které metody ověřování jsou nakonfigurovány.
- Zásady přístupu související s podnikovou sítí v systému nebo.
- Postup řešení potíží pro běžné scénáře.

V online dokumentaci můžete také informace o řešení potíží samoobslužného resetování hesla, abyste porozuměli obecným krokům při řešení potíží s nejběžnějšími scénáři SSPR.

## <a name="next-steps"></a>Další kroky

- [Zvažte implementaci ochrany heslem Azure AD.](concept-password-ban-bad.md)

- [Zvažte implementaci inteligentního uzamčení Azure AD.](howto-password-smart-lockout.md)
