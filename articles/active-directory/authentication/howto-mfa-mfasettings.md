---
title: Konfigurace Azure Multi-Factor Authentication-Azure Active Directory
description: Tento článek popisuje, jak nakonfigurovat nastavení Azure Multi-Factor Authentication v Azure Portal
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9eab92f044f62a1ca32062753f824ea76a2726a8
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2019
ms.locfileid: "70860285"
---
# <a name="configure-azure-multi-factor-authentication-settings"></a>Konfigurace nastavení služby Azure Multi-Factor Authentication

Tento článek vám pomůže se správou nastavení Multi-Factor Authentication v Azure Portal. Zahrnuje různá témata, která vám pomohou získat z Azure Multi-Factor Authentication na maximum. Ne všechny funkce jsou dostupné v každé verzi Azure Multi-Factor Authentication.

K nastavení souvisejícím s Azure Multi-Factor Authentication můžete přistupovat z Azure Portal tak, že přejdete na **Azure Active Directory** > **MFA**.

![Azure Portal – nastavení Multi-Factor Authentication Azure AD](./media/howto-mfa-mfasettings/multi-factor-authentication-settings-portal.png)

## <a name="settings"></a>Nastavení

Některá z těchto nastavení se vztahují na MFA Server, Azure MFA nebo obojí.

| Funkce | Popis |
| ------- | ----------- |
| Uzamčení účtu | Dočasné uzamčení účtů ve službě Multi-Factor Authentication, pokud je v řádku příliš mnoho zamítnutých pokusů o ověření. Tato funkce se vztahuje jenom na uživatele, kteří do ověřování zadají kód PIN. (MFA Server) |
| [Blokovat/odblokovat uživatele](#block-and-unblock-users) | Slouží k blokování, aby konkrétní uživatelé mohli přijímat žádosti Multi-Factor Authentication. Všechny pokusy o ověření blokovaného uživatele budou automaticky odepřeny. Uživatelé zůstávají zablokovaný po dobu 90 dnů od doby, kdy jsou zablokované. |
| [Výstraha týkající se podvodů](#fraud-alert) | Konfigurace nastavení souvisejících s možností uživatelů hlásit podvodné žádosti o ověření |
| [Oznámení](#notifications) | Povolí oznámení událostí z MFA serveru. |
| [Tokeny OATH](concept-authentication-methods.md#oath-hardware-tokens-public-preview) | Používá se v cloudových prostředích Azure MFA ke správě tokenů OATH pro uživatele. |
| [Nastavení telefonního hovoru](#phone-call-settings) | Nakonfigurujte nastavení související s telefonními hovory a pozdravy pro cloudová a místní prostředí. |
| Zprostředkovatelé | Tím se zobrazí všechna existující zprostředkovatelé ověřování, které jste mohli k vašemu účtu přidružit. Od 1. září 2018 se nevytvoří Noví zprostředkovatelé ověřování. |

## <a name="manage-mfa-server"></a>Spravovat MFA Server

Nastavení v této části platí jenom pro MFA Server.

| Funkce | Popis |
| ------- | ----------- |
| Nastavení serveru | Stáhněte si MFA Server a vygenerujte aktivační přihlašovací údaje pro inicializaci vašeho prostředí. |
| [Jednorázové přihlášení](#one-time-bypass) | Umožní uživateli ověřování bez provádění dvoustupňového ověřování po dobu omezeného času. |
| [Pravidla ukládání do mezipaměti](#caching-rules) |  Ukládání do mezipaměti se primárně používá v případě, že místní systémy, jako je například síť VPN, odesílají více žádostí o ověření v době, kdy stále probíhá první žádost. Tato funkce umožňuje, aby následné požadavky byly automaticky úspěšné a poté, co uživatel úspěšně provedl první ověření. |
| Stav serveru | Podívejte se na stav místních MFA serverů, včetně verze, stavu, IP adresy a času poslední komunikace a data. |

## <a name="activity-report"></a>Sestava aktivit

Zde dostupné sestavy jsou specifické pro MFA Server (místní). Pro sestavy Azure MFA (Cloud) se zobrazí sestava přihlášení ve službě Azure AD.

## <a name="block-and-unblock-users"></a>Blokování a odblokování uživatelů

Pomocí funkce _Blokovat a odblokovat uživatele_ zabráníte uživatelům přijímat žádosti o ověření. Všechny pokusy o ověření blokovaného uživatele budou automaticky odepřeny. Uživatelé zůstávají zablokovaný po dobu 90 dnů od doby, kdy jsou zablokované.

### <a name="block-a-user"></a>Zablokovat uživatele

1. Přihlaste se na webu [Azure Portal](https://portal.azure.com) jako správce.
2. Přejděte na **Azure Active Directory** > **zablokovat nebo odblokovat** **MFA** > uživatele.
3. Vyberte možnost **Přidat** a zablokovat uživatele.
4. Vyberte **replikační skupinu**. Zadejte uživatelské jméno blokovaného uživatele jako **uživatelské jméno\@Domain.com**. Zadejte komentář do pole **důvod** .
5. Kliknutím na tlačítko **Přidat** dokončete blokování uživatele.

### <a name="unblock-a-user"></a>Odblokovat uživatele

1. Přihlaste se na webu [Azure Portal](https://portal.azure.com) jako správce.
2. Přejděte na **Azure Active Directory** > **zablokovat nebo odblokovat** **MFA** > uživatele.
3. U sloupce **Akce** vedle uživatele, který chcete odblokovat, vyberte možnost **odblokovat** .
4. Zadejte komentář do pole **důvod odblokování** .
5. Výběrem **odblokování** dokončete odblokování uživatele.

## <a name="fraud-alert"></a>Upozornění na podvod

Nakonfigurujte funkci _Upozornění na podvod_ , aby vaši uživatelé mohli nahlásit podvodné pokusy o přístup k jejich prostředkům. Uživatelé můžou nahlásit pokusy o podvod pomocí mobilní aplikace nebo přes telefon.

### <a name="turn-on-fraud-alerts"></a>Zapnout výstrahy podvodů

1. Přihlaste se na webu [Azure Portal](https://portal.azure.com) jako správce.
2. Vyhledejte **Azure Active Directory** > **výstrahu týkající se podvodů** **MFA** > .
3. Nastavte nastavení **povoleno uživatelům odeslat výstrahy na podvod** do **zapnuto**.
4. Vyberte **Uložit**.

### <a name="configuration-options"></a>Možnosti konfigurace

* **Zablokovat uživatele při nahlášení podvodu**: Pokud uživatel nahlásí podvod, je jejich účet zablokován po dobu 90 dnů nebo dokud správce neblokuje svůj účet. Správce může kontrolovat přihlášení pomocí sestavy přihlášení a provést vhodná opatření, aby se předešlo budoucímu podvodování. Správce pak může [odblokovat](#unblock-a-user) účet uživatele.
* **Kód pro nahlášení podvodu při úvodním pozdravu**: Když uživatelé obdrží telefonní hovor, aby provedl dvoustupňové ověřování, normálně **#** si potvrdí jejich přihlášení. Chcete-li nahlásit podvod, uživatel zadá kód před **#** stisknutím klávesy. Tento kód je ve výchozím nastavení **0** , ale můžete ho přizpůsobit.

   >[!NOTE]
   >Výchozí Hlasové pozdravy od Microsoftu přidávají uživatelům pokyn k odeslání výstrahy na podvod po stisku **0 #** . Pokud chcete použít jiný kód než **0**, zaznamenejte a nahrajte vlastní hlasové pozdravy s vhodnými pokyny pro uživatele.
   >

### <a name="view-fraud-reports"></a>Zobrazení sestav podvodů

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vyberte **Azure Active Directory** > **přihlášení**. Sestava podvodů je teď součástí standardní sestavy přihlášení ke službě Azure AD.

## <a name="notifications"></a>Oznámení

Nakonfigurujte e-mailové adresy pro uživatele, kteří budou dostávat e-maily s upozorněním na podvod

![Ukázka e-mailu s upozorněním na podvod oznámení](./media/howto-mfa-mfasettings/multi-factor-authentication-fraud-alert-email.png)

## <a name="phone-call-settings"></a>Nastavení telefonních hovorů

### <a name="caller-id"></a>ID volajícího

**ID volajícího MFA** – Toto je počet uživatelů, kteří se uvidí na telefonu. Povolena jsou pouze čísla založená na USA.

>[!NOTE]
>Když Multi-Factor Authentication volání jsou umístěna prostřednictvím veřejné telefonní sítě, někdy jsou směrována prostřednictvím dopravce, který nepodporuje ID volajícího. Z tohoto důvodu není ID volajícího zaručeno, i když je systém Multi-Factor Authentication vždy odesílá.

### <a name="custom-voice-messages"></a>Vlastní hlasové zprávy

Můžete použít vlastní nahrávky nebo přání pro dvoustupňové ověřování pomocí funkce _vlastních hlasových zpráv_ . Tyto zprávy lze použít společně s nebo k nahrazení nahrávek společnosti Microsoft.

Než začnete, mějte na paměti následující omezení:

* Podporované formáty souborů jsou. wav a. mp3.
* Limit velikosti souboru je 5 MB.
* Ověřovací zprávy by měly být kratší než 20 sekund. Zprávy, které jsou delší než 20 sekund, mohou způsobit selhání ověřování. Uživatel nemusí reagovat před dokončením zprávy a vypršením časového limitu ověřování.

### <a name="custom-message-language-behavior"></a>Vlastní chování jazyka zprávy

Když se na uživatele přehraje vlastní hlasová zpráva, bude jazyk zprávy záviset na těchto faktorech:

* Jazyk aktuálního uživatele.
  * Jazyk zjištěný prohlížečem uživatele.
  * Jiné scénáře ověřování se můžou chovat jinak.
* Jazyk všech dostupných vlastních zpráv.
  * Tento jazyk volí správce při přidání vlastní zprávy.

Pokud je například jenom jedna vlastní zpráva, má jazyk němčina:

* Uživatel, který se ověří v německém jazyce, si přečte vlastní Německoovou zprávu.
* Uživatel, který se ověřuje v angličtině, uslyší standardní anglickou zprávu.

### <a name="set-up-a-custom-message"></a>Nastavení vlastní zprávy

1. Přihlaste se na webu [Azure Portal](https://portal.azure.com) jako správce.
1. Přejděte na **Azure Active Directory** > nastavení**ověřování** > na**telefonních hovorech**MFA.
1. Vyberte **Přidat pozdrav**.
1. Vyberte typ pozdravu.
1. Vyberte jazyk.
1. Vyberte zvukový soubor. mp3 nebo. wav, který chcete nahrát.
1. Vyberte **Přidat**.

### <a name="custom-voice-message-defaults"></a>Nastavení vlastních hlasových zpráv

Ukázkové skripty pro vytváření vlastních zpráv

| Název zprávy | Skript |
| --- | --- |
| Ověření bylo úspěšné. | Vaše přihlášení bylo úspěšně ověřeno. Nashledanou. |
| Výzva k zadání linky | Děkujeme, že používáte ověřovací systém přihlašování od Microsoftu. Pokračujte stisknutím klávesy libry. |
| Potvrzení podvodu | Bylo odesláno upozornění na podvod. Pokud chcete odblokovat svůj účet, obraťte se na helpdesk IT oddělení vaší společnosti. |
| Pozdrav při podvodu (Standard) | Děkujeme, že používáte ověřovací systém přihlašování od Microsoftu. Potvrďte ověření stisknutím křížku klávesy. Pokud jste toto ověřování nezahájili, může se někdo pokusit o přístup k vašemu účtu. Pokud chcete odeslat výstrahu o podvodech, stiskněte prosím nulový křížek. Tím se upozorní tým IT vaší společnosti a zablokují se další pokusy o ověření. |
| Podvod ohlásil, že byla odeslána výstraha o podvodech. | Pokud chcete odblokovat svůj účet, obraťte se na helpdesk IT oddělení vaší společnosti. |
| Aktivace | Děkujeme, že používáte ověřovací systém přihlašování od Microsoftu. Potvrďte ověření stisknutím křížku klávesy. |
| Ověření bylo odepřeno – opakovaný pokus. | Ověření bylo odepřeno. |
| Opakovat (Standard) | Děkujeme, že používáte ověřovací systém přihlašování od Microsoftu. Potvrďte ověření stisknutím křížku klávesy. |
| Pozdrav (Standard) | Děkujeme, že používáte ověřovací systém přihlašování od Microsoftu. Potvrďte ověření stisknutím křížku klávesy. |
| Pozdrav (PIN) | Děkujeme, že používáte ověřovací systém přihlašování od Microsoftu. Zadejte prosím PIN kód následovaný křížkem a dokončete ověření. |
| Pozdrav při podvodu (PIN) | Děkujeme, že používáte ověřovací systém přihlašování od Microsoftu.  Zadejte prosím PIN kód následovaný křížkem a dokončete ověření. Pokud jste toto ověřování nezahájili, může se někdo pokusit o přístup k vašemu účtu. Pokud chcete odeslat výstrahu o podvodech, stiskněte prosím nulový křížek. Tím se upozorní tým IT vaší společnosti a zablokují se další pokusy o ověření. |
| Opakovat (PIN) | Děkujeme, že používáte ověřovací systém přihlašování od Microsoftu. Zadejte prosím PIN kód následovaný křížkem a dokončete ověření. |
| Výzva k zadání linky – po číslicích | Pokud už v tomto rozšíření máte, pokračujte stisknutím křížku. |
| Ověření bylo zamítnuto. | Je nám líto, ale teď vás nemůžeme přihlásit. Zkuste to prosím znova později. |
| Pozdrav při aktivaci (Standard) | Děkujeme, že používáte ověřovací systém přihlašování od Microsoftu. Potvrďte ověření stisknutím křížku klávesy. |
| Opakovaný pokus o aktivaci (Standard) | Děkujeme, že používáte ověřovací systém přihlašování od Microsoftu. Potvrďte ověření stisknutím křížku klávesy. |
| Pozdrav při aktivaci (PIN) | Děkujeme, že používáte ověřovací systém přihlašování od Microsoftu. Zadejte prosím PIN kód následovaný křížkem a dokončete ověření. |
| Výzva k zadání linky – před číslicemi | Děkujeme, že používáte ověřovací systém přihlašování od Microsoftu. Převeďte prosím toto volání na rozšíření... |

## <a name="one-time-bypass"></a>Jednorázové přihlášení

Funkce _jednorázového_ přihlášení umožňuje uživateli ověřování jednorázově bez provedení dvoustupňového ověřování. Vynechání je dočasné a po zadaném počtu sekund vyprší platnost. V situacích, kdy mobilní aplikace nebo telefon nepřijímá oznámení nebo telefonní hovor, můžete umožnit jednorázové přihlášení, aby uživatel mohl získat přístup k požadovanému prostředku.

### <a name="create-a-one-time-bypass"></a>Vytvoření jednorázového přihlášení

1. Přihlaste se na webu [Azure Portal](https://portal.azure.com) jako správce.
2. Přejděte na **Azure Active Directory** > **jednorázové přihlášení** **MFA** > .
3. Vyberte **Přidat**.
4. V případě potřeby vyberte replikační skupinu pro vynechání.
5. Zadejte uživatelské jméno jako **uživatelské\@jméno Domain.com**. Zadejte počet sekund, po které má být vynechání naposledy. Zadejte důvod pro obejití.
6. Vyberte **Přidat**. Časový limit se projeví okamžitě. Uživatel se musí přihlásit předtím, než vyprší doba přihlášení k jednorázovému přihlášení.

### <a name="view-the-one-time-bypass-report"></a>Zobrazit sestavu jednorázového přihlášení

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Přejděte na **Azure Active Directory** > **jednorázové přihlášení** **MFA** > .

## <a name="caching-rules"></a>Pravidla ukládání do mezipaměti

Můžete nastavit časové období, které umožní pokusy o ověření po ověření uživatele pomocí funkce _ukládání do mezipaměti_ . Následné pokusy o ověření pro uživatele v zadaném časovém období jsou automaticky úspěšné. Ukládání do mezipaměti se primárně používá v případě, že místní systémy, jako je například síť VPN, odesílají více žádostí o ověření v době, kdy stále probíhá první žádost. Tato funkce umožňuje, aby následné požadavky byly automaticky úspěšné a poté, co uživatel úspěšně provedl první ověření.

>[!NOTE]
>Funkce ukládání do mezipaměti není určena k použití pro přihlášení k Azure Active Directory (Azure AD).

### <a name="set-up-caching"></a>Nastavení ukládání do mezipaměti

1. Přihlaste se na webu [Azure Portal](https://portal.azure.com) jako správce.
2. Přejděte na **Azure Active Directory** > **pravidla pro ukládání do mezipaměti** **MFA** > .
3. Vyberte **Přidat**.
4. Z rozevíracího seznamu vyberte **typ mezipaměti** . Zadejte maximální počet **sekund mezipaměti**.
5. V případě potřeby vyberte typ ověřování a zadejte aplikaci.
6. Vyberte **Přidat**.

## <a name="mfa-service-settings"></a>Nastavení služby MFA

Nastavení pro hesla aplikací, důvěryhodné IP adresy, možnosti ověřování a zapamatování služby Multi-Factor Authentication pro Azure Multi-Factor Authentication najdete v nastavení služby. K nastavení služby se dá získat pøístup z Azure Portal tak, že přejdete na **Azure Active Directory** > **MFA** > **Začínáme** > **Konfigurovat** > **Další cloudové Nastavení vícefaktorového ověřování**.

![Nastavení služby Azure Multi-Factor Authentication](./media/howto-mfa-mfasettings/multi-factor-authentication-settings-service-settings.png)

## <a name="app-passwords"></a>Hesla aplikací

Některé aplikace, například Office 2010 nebo starší a Apple Mail před iOS 11, nepodporují dvoustupňové ověřování. Aplikace nejsou nakonfigurované, aby přijímaly druhé ověření. Chcete-li používat tyto aplikace, využijte funkci _hesla aplikací_ . Místo tradičního hesla můžete použít heslo aplikace, abyste aplikaci mohli obejít dvoustupňové ověřování a pokračovat v práci.

Moderní ověřování se podporuje u klientů systém Microsoft Office 2013 a později. Klienti Office 2013, včetně Outlooku, podporují moderní protokoly ověřování a dají se povolit pro práci s dvoustupňové ověřováním. Po povolení klienta se pro klienta nevyžadují hesla aplikací.

>[!NOTE]
>Hesla aplikací nefungují s použitím zásad vícefaktorového ověřování založeného na podmíněném přístupu a moderního ověřování.

### <a name="considerations-about-app-passwords"></a>Informace o heslech aplikací

Při používání hesel aplikací Vezměte v úvahu následující důležité body:

* Hesla aplikací se zadávají jenom jednou pro každou aplikaci. Uživatelé nemusí sledovat hesla nebo je zadávat pokaždé.
* Vlastní heslo se vygeneruje automaticky a uživatel ho nezadal. Automaticky vygenerované heslo je těžší, aby útočník mohl odhadnout a je bezpečnější.
* Pro každého uživatele je povolený limit 40 hesel.
* Aplikace, které ukládá hesla do mezipaměti a používají je v místních scénářích, můžou začít selhat, protože heslo aplikace není známé mimo pracovní nebo školní účet. Příkladem tohoto scénáře jsou e-maily systému Exchange, které jsou místní, ale Archivovaná pošta je v cloudu. V tomto scénáři stejné heslo nefunguje.
* Po povolení Multi-Factor Authentication na účtu uživatele se hesla aplikací dají použít u většiny klientů bez prohlížeče, jako je Outlook a Microsoft Skype pro firmy. Akce správy se nedají provádět pomocí hesel aplikací přes neprohlížečové aplikace, jako je Windows PowerShell. Akce se nedají provést, i když má uživatel účet správce. Chcete-li spustit skripty prostředí PowerShell, vytvořte účet služby se silným heslem a nepovolujte účet pro dvoustupňové ověřování.

>[!WARNING]
>Hesla aplikací nefungují v hybridních prostředích, kde klienti komunikují s místními i cloudy pro automatické zjišťování koncových bodů. K místnímu ověřování se vyžadují doménová hesla. K ověřování pomocí cloudu se vyžadují hesla aplikací.

### <a name="guidance-for-app-password-names"></a>Pokyny pro názvy hesel aplikací

Názvy hesel aplikací by měly odrážet zařízení, ve kterém se používají. Pokud máte přenosný počítač, který obsahuje neprohlížečové aplikace, jako je Outlook, Word a Excel, vytvořte pro tyto aplikace jedno heslo aplikace s názvem **notebook** . Vytvořte další heslo aplikace s názvem **Desktop** pro stejné aplikace, které běží na stolním počítači.

>[!NOTE]
>Doporučujeme vytvořit jedno heslo aplikace na jedno zařízení, nikoli jedno heslo aplikace na aplikaci.

### <a name="federated-or-single-sign-on-app-passwords"></a>Hesla federovaného nebo jednotného přihlašování k aplikacím

Azure AD podporuje federaci nebo jednotné přihlašování (SSO) s místním Windows serverem Active Directory Domain Services (služba AD DS). Pokud je vaše organizace federované s Azure AD a používáte Azure Multi-Factor Authentication, zvažte následující body hesla aplikací.

>[!NOTE]
>Následující body se vztahují jenom na zákazníky federovaného (SSO).

* Hesla aplikací ověřuje služba Azure AD, a proto se nepoužívají federace. Federace se aktivně používá jenom při nastavování hesel aplikací.
* Poskytovatel identity (IdP) se nekontaktuje na uživatele federovaného (SSO), na rozdíl od pasivního toku. Hesla aplikací se ukládají v pracovním nebo školním účtu. Pokud uživatel odejde ze společnosti, informace o uživateli se budou předávat do pracovního nebo školního účtu pomocí **DirSync** v reálném čase. Operace zakázat/odstranit může trvat až tři hodiny, než se synchronizuje, což může zpozdit zakázání a odstranění hesla aplikace ve službě Azure AD.
* Nastavení místních Access Control klienta nedodržuje funkce hesla aplikací.
* Pro použití s funkcí hesla aplikací nejsou k dispozici žádné místní možnosti protokolování a auditování ověřování.
* Některé pokročilé architektury vyžadují kombinaci přihlašovacích údajů pro dvoustupňové ověřování s klienty. Tyto přihlašovací údaje můžou zahrnovat uživatelské jméno a heslo školního účtu a hesla aplikací. Požadavky závisí na tom, jak je ověřování prováděno. U klientů, kteří se ověřují v místní infrastruktuře, je nutné zadat uživatelské jméno nebo uživatelské jméno a heslo školního účtu. U klientů, kteří se ověřují v Azure AD, je vyžadováno heslo aplikace.

  Předpokládejme například, že máte následující architekturu:

  * Vaše místní instance Active Directory je federované s Azure AD.
  * Používáte Exchange Online.
  * Používáte Skype pro firmy místně.
  * Používáte Azure Multi-Factor Authentication.

  V tomto scénáři použijete následující přihlašovací údaje:

  * Pokud se chcete přihlásit ke službě Skype pro firmy, použijte uživatelské jméno nebo uživatelské jméno a heslo školního účtu.
  * Pokud chcete získat přístup k adresáři z klienta Outlooku, který se připojuje k Exchangi Online, použijte heslo aplikace.

### <a name="allow-users-to-create-app-passwords"></a>Umožňuje uživatelům vytvářet hesla aplikací.

Ve výchozím nastavení uživatelé nemůžou vytvářet hesla aplikací. Musí být povolená funkce hesla aplikací. Chcete-li uživatelům umožnit vytváření hesel aplikací, použijte následující postup:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Na levé straně vyberte **Azure Active Directory** > **Uživatelé**.
3. Vyberte **Multi-Factor Authentication**.
4. V části Multi-Factor Authentication vyberte **nastavení služby**.
5. Na stránce **nastavení služby** zaškrtněte políčko **dovolit uživatelům vytvářet hesla aplikací pro přihlášení k neprohlížečovým aplikacím** .

### <a name="create-app-passwords"></a>Vytvoření hesel aplikací

Uživatelé můžou během počáteční registrace vytvářet hesla aplikací. Uživatel má možnost vytvářet hesla aplikací na konci procesu registrace.

Uživatelé můžou po registraci vytvářet taky hesla aplikací. Další informace a podrobné pokyny pro uživatele najdete v tématu [co jsou hesla aplikací v Azure Multi-Factor Authentication?](../user-help/multi-factor-authentication-end-user-app-passwords.md)

## <a name="trusted-ips"></a>Důvěryhodné IP adresy

Funkce _důvěryhodných IP adres_ služby Azure Multi-Factor Authentication se používá pro správce spravovaného nebo federovaného tenanta. Tato funkce obchází dvoustupňové ověřování pro uživatele, kteří se přihlásí z intranetu společnosti. Tato funkce je k dispozici v plné verzi služby Azure Multi-Factor Authentication, nikoli na bezplatné verzi pro správce. Podrobnosti o tom, jak získat úplnou verzi služby Azure Multi-Factor Authentication, najdete v tématu [Azure Multi-Factor Authentication](multi-factor-authentication.md).

> [!NOTE]
> Důvěryhodné IP adresy MFA a podmíněný přístup s názvem umístění fungují jenom s adresami IPV4.

Pokud vaše organizace nasadí rozšíření serveru NPS za účelem poskytování MFA pro místní aplikace, Všimněte si, že zdrojová IP adresa se vždy jeví jako server NPS, se kterým se snaží ověřování natékat.

| Typ tenanta Azure AD | Možnosti funkcí důvěryhodných IP adres |
|:--- |:--- |
| Spravovaní |**Konkrétní rozsah IP adres**: Správci určují rozsah IP adres, které mohou obejít dvoustupňové ověřování u uživatelů, kteří se přihlásí z intranetu společnosti.|
| Federovaná |**Všichni federované uživatelé**: Všichni federované uživatelé, kteří se přihlásí z organizace v organizaci, můžou obejít dvoustupňové ověřování. Uživatel obejít ověřování pomocí deklarace identity, která je vydaná Active Directory Federation Services (AD FS) (AD FS).<br/>**Konkrétní rozsah IP adres**: Správci určují rozsah IP adres, které mohou obejít dvoustupňové ověřování u uživatelů, kteří se přihlásí z intranetu společnosti. |

Obejití důvěryhodných IP adres funguje pouze uvnitř intranetu společnosti. Pokud vyberete možnost **všechny federované uživatele** a uživatel se přihlásí mimo intranet společnosti, musí se uživatel ověřit pomocí dvoustupňového ověřování. Proces je stejný i v případě, že uživatel prezentuje AD FS deklarací identity. 

### <a name="end-user-experience-inside-of-corpnet"></a>Činnost koncového uživatele v rámci Corpnet

Pokud je funkce důvěryhodných IP adres zakázaná, pro toky v prohlížeči se vyžaduje dvoustupňové ověřování. Pro starší verze klientských aplikací jsou vyžadovány hesla aplikací.

Pokud je povolená funkce důvěryhodných IP adres, pro toky v prohlížeči *se nevyžaduje* dvoustupňové ověřování. Hesla aplikací se pro starší aplikace s bohatou verzí klienta *nevyžadují,* Pokud uživatel nevytvořil heslo aplikace. Po použití hesla aplikace zůstane heslo povinné. 

### <a name="end-user-experience-outside-corpnet"></a>Činnost koncového uživatele mimo Corpnet

Bez ohledu na to, jestli je povolená funkce důvěryhodných IP adres, se pro toky v prohlížeči vyžaduje dvoustupňové ověřování. Pro starší verze klientských aplikací jsou vyžadovány hesla aplikací.

### <a name="enable-named-locations-by-using-conditional-access"></a>Povolit pojmenovaná umístění pomocí podmíněného přístupu

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Na levé straně vyberte **Azure Active Directory** > **zabezpečení** > **podmíněný přístup** > **pojmenované umístění**.
3. Vyberte **nové umístění**.
4. Zadejte název umístění.
5. Vyberte **Označit jako důvěryhodné umístění**.
6. Zadejte rozsah IP adres v zápisu CIDR jako **192.168.1.1/24**.
7. Vyberte **Vytvořit**.

### <a name="enable-the-trusted-ips-feature-by-using-conditional-access"></a>Povolení funkce důvěryhodných IP adres pomocí podmíněného přístupu

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Na levé straně vyberte **Azure Active Directory** > **zabezpečení** >  **podmíněný přístup** > **pojmenované umístění**.
3. Vyberte **Konfigurovat důvěryhodné IP adresy MFA**.
4. Na stránce **nastavení služby** v části **důvěryhodné IP adresy**vyberte některou z následujících dvou možností:

   * **Pro žádosti od federovaných uživatelů pocházejících z mého intranetu**: Tuto možnost vyberte zaškrtnutím políčka. Všichni federované uživatelé, kteří se přihlásí z podnikové sítě, obcházejí dvoustupňové ověřování pomocí deklarace identity, která je vydaná AD FS. Ujistěte se, že AD FS má pravidlo pro přidání deklarace identity intranetu do příslušného provozu. Pokud pravidlo neexistuje, vytvořte v AD FS následující pravidlo:

      `c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);`

   * **Pro požadavky z konkrétního rozsahu veřejných IP adres**: Tuto možnost vyberte, pokud chcete zadat IP adresy do textového pole pomocí zápisu CIDR.
      * Pro IP adresy, které jsou v rozsahu xxx. xxx. xxx. 1 až XXX. xxx. xxx. 254, použijte notaci jako **xxx. xxx. xxx. 0/24**.
      * Pro jednu IP adresu použijte Notation, jako je **xxx.xxx.xxx.xxx/32**.
      * Zadejte až 50 rozsahů IP adres. Uživatelé, kteří se přihlásí z těchto IP adres, obcházejí dvoustupňové ověřování.

5. Vyberte **Uložit**.

### <a name="enable-the-trusted-ips-feature-by-using-service-settings"></a>Povolení funkce důvěryhodných IP adres pomocí nastavení služby

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Na levé straně vyberte **Azure Active Directory** > **Uživatelé**.
3. Vyberte **Multi-Factor Authentication**.
4. V části Multi-Factor Authentication vyberte **nastavení služby**.
5. Na stránce **nastavení služby** v části **důvěryhodné IP adresy**vyberte jednu (nebo obě) z následujících dvou možností:

   * **Pro žádosti od federovaných uživatelů v mém intranetu**: Tuto možnost vyberte zaškrtnutím políčka. Všichni federované uživatelé, kteří se přihlásí z podnikové sítě, obcházejí dvoustupňové ověřování pomocí deklarace identity, která je vydaná AD FS. Ujistěte se, že AD FS má pravidlo pro přidání deklarace identity intranetu do příslušného provozu. Pokud pravidlo neexistuje, vytvořte v AD FS následující pravidlo:

      `c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);`

   * **Požadavky z určeného rozsahu podsítí IP adres**: Tuto možnost vyberte, pokud chcete zadat IP adresy do textového pole pomocí zápisu CIDR.
      * Pro IP adresy, které jsou v rozsahu xxx. xxx. xxx. 1 až XXX. xxx. xxx. 254, použijte notaci jako **xxx. xxx. xxx. 0/24**.
      * Pro jednu IP adresu použijte Notation, jako je **xxx.xxx.xxx.xxx/32**.
      * Zadejte až 50 rozsahů IP adres. Uživatelé, kteří se přihlásí z těchto IP adres, obcházejí dvoustupňové ověřování.

6. Vyberte **Uložit**.

## <a name="verification-methods"></a>Metody ověřování

Můžete zvolit metody ověřování, které jsou k dispozici pro vaše uživatele. Následující tabulka poskytuje stručný přehled metod.

Když uživatelé zaregistrují své účty pro Azure Multi-Factor Authentication, zvolí si upřednostňovanou metodu ověření z možností, které jste povolili. Pokyny k procesu registrace uživatele najdete v části [Nastavení účtu pro dvoustupňové ověřování](../user-help/multi-factor-authentication-end-user-first-time.md).

| Metoda | Popis |
|:--- |:--- |
| Telefonní hovor |Místo automatizovaného hlasového hovoru. Uživatel odpoví na volání a při ověřování stiskne na klávesnici číslo. Telefonní číslo není synchronizované s místní službou Active Directory. |
| Textová zpráva na telefon |Pošle textovou zprávu obsahující ověřovací kód. Uživateli se zobrazí výzva k zadání ověřovacího kódu do přihlašovacího rozhraní. Tento proces se nazývá jednosměrný server SMS. Dvoucestné SMS znamená, že uživatel musí zpětně vytvořit text konkrétního kódu. Obousměrná zpráva SMS je zastaralá a není podporovaná ani po 14. listopadu 2018. Uživatelé, kteří jsou nakonfigurováni jako obousměrný server SMS, jsou automaticky přepnuti na ověřování _telefonem_ v daném čase.|
| Oznámení přes mobilní aplikaci |Odešle nabízené oznámení na telefon nebo registrované zařízení. Uživatel zobrazí oznámení a vybere **ověření** pro dokončení ověření. Aplikace Microsoft Authenticator je dostupná pro [Windows Phone](https://go.microsoft.com/fwlink/?Linkid=825071), [Android](https://go.microsoft.com/fwlink/?Linkid=825072)a [iOS](https://go.microsoft.com/fwlink/?Linkid=825073). |
| Ověřovací kód z mobilní aplikace nebo hardwarového tokenu |Aplikace Microsoft Authenticator generuje nový ověřovací kód OATH každých 30 sekund. Uživatel zadá ověřovací kód do přihlašovacího rozhraní. Aplikace Microsoft Authenticator je dostupná pro [Windows Phone](https://go.microsoft.com/fwlink/?Linkid=825071), [Android](https://go.microsoft.com/fwlink/?Linkid=825072)a [iOS](https://go.microsoft.com/fwlink/?Linkid=825073). |

### <a name="enable-and-disable-verification-methods"></a>Povolit a zakázat metody ověřování

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Na levé straně vyberte **Azure Active Directory** > **Uživatelé**.
3. Vyberte **Multi-Factor Authentication**.
4. V části Multi-Factor Authentication vyberte **nastavení služby**.
5. Na stránce **nastavení služby** v části **Možnosti ověřování**vyberte nebo zrušte výběr metod, které chcete uživatelům poskytnout.
6. Klikněte na **Uložit**.

Další podrobnosti o použití metod ověřování najdete v článku [co jsou metody ověřování](concept-authentication-methods.md).

## <a name="remember-multi-factor-authentication"></a>Zapamatovat Multi-Factor Authentication

Funkce _Zapamatovat Multi-Factor Authentication_ pro zařízení a prohlížeče, které jsou důvěryhodné pro uživatele, je bezplatná funkce pro všechny Multi-Factor Authentication uživatele. Po úspěšném přihlášení k zařízení pomocí Multi-Factor Authentication můžou uživatelé obejít následná ověření po dobu určitého počtu dnů. Tato funkce vylepšuje použitelnost tím, že minimalizuje počet pokusů, které uživatel provede dvoustupňové ověřování na stejném zařízení.

>[!IMPORTANT]
>Pokud dojde k ohrožení bezpečnosti účtu nebo zařízení, může to mít vliv na zapamatování Multi-Factor Authentication důvěryhodných zařízení. Pokud dojde k ohrožení bezpečnosti podnikového účtu nebo dojde ke ztrátě nebo odcizení důvěryhodného zařízení, měli byste [Multi-Factor Authentication obnovit na všech zařízeních](howto-mfa-userdevicesettings.md#restore-mfa-on-all-remembered-devices-for-a-user).
>
>Akce obnovit odvolá důvěryhodný stav ze všech zařízení a uživatel musí provést dvoustupňové ověřování znovu. Můžete taky dát uživatelům pokyn, aby obnovili Multi-Factor Authentication na svých vlastních zařízeních s pokyny v tématu [Správa nastavení pro dvoustupňové ověřování](../user-help/multi-factor-authentication-end-user-manage-settings.md#turn-on-two-factor-verification-prompts-on-a-trusted-device).

### <a name="how-the-feature-works"></a>Jak funkce funguje

Funkce zapamatovat Multi-Factor Authentication nastaví trvalý soubor cookie v prohlížeči, když uživatel vybere možnost po **X dnech znovu se neptat** při přihlášení. Uživatel není znovu vyzván k Multi-Factor Authentication z tohoto stejného prohlížeče, dokud neskončí platnost souboru cookie. Pokud uživatel otevře jiný prohlížeč na stejném zařízení nebo vymaže soubory cookie, zobrazí se jim výzva k ověření.

Možnost **příště nedotazování na X dní** se nezobrazí v neprohlížečových aplikacích bez ohledu na to, jestli aplikace podporuje moderní ověřování. Tyto aplikace používají _aktualizační tokeny_ , které každou hodinu poskytují nové přístupové tokeny. Při ověření obnovovacího tokenu Azure AD zkontroluje, že poslední dvoustupňové ověřování proběhlo během zadaného počtu dnů.

Tato funkce snižuje počet ověřování ve webových aplikacích, které se obvykle zobrazují při každém dotazu. Tato funkce zvyšuje počet ověřování pro klienty moderních ověřování, kteří se normálně vyzvat každých 90 dní. Může také zvýšit počet ověřování v kombinaci se zásadami podmíněného přístupu.

>[!IMPORTANT]
>Funkce **Zapamatovat Multi-Factor Authentication** není kompatibilní s funkcí **zůstat přihlášenou** AD FS, když uživatelé provedou dvoustupňové ověřování pro AD FS prostřednictvím Azure Multi-Factor Authentication Server nebo multi-Factoru třetí strany. řešení ověřování.
>
>Pokud uživatelé vyberou možnost **zůstat přihlášeni** na AD FS a také označí své zařízení jako důvěryhodné pro Multi-Factor Authentication, uživatel nebude automaticky ověřený po vypršení časového **limitu zapamatování služby Multi-Factor Authentication** . Azure AD si vyžádá nové dvoustupňové ověřování, ale AD FS vrátí token s původní Multi-Factor Authentication deklarací a datem, místo aby se znovu provádělo dvoustupňové ověřování. **Tato reakce nastavuje smyčku ověřování mezi Azure AD a AD FS.**
>

### <a name="enable-remember-multi-factor-authentication"></a>Povolit zapamatovat Multi-Factor Authentication

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Na levé straně vyberte **Azure Active Directory** > **Uživatelé**.
3. Vyberte **Multi-Factor Authentication**.
4. V části Multi-Factor Authentication vyberte **nastavení služby**.
5. Na stránce **nastavení služby** **Spravovat zapamatování vícefaktorového ověřování**vyberte možnost **dovolit uživatelům pamatovat si vícefaktorové ověřování na zařízeních, která důvěřují** .
6. Nastavte počet dní, po který mají důvěryhodná zařízení obejít dvoustupňové ověřování. Výchozí hodnota je 14 dní.
7. Vyberte **Uložit**.

### <a name="mark-a-device-as-trusted"></a>Označení zařízení jako důvěryhodného

Po povolení funkce zapamatovat Multi-Factor Authentication uživatelé můžou zařízení označit jako důvěryhodné, když se přihlásí, a to tak, že se po přihlášení vybere **příště už**nezobrazovat.

## <a name="next-steps"></a>Další kroky

[Úprava brandingu přihlašovací stránky služby Azure AD](../fundamentals/customize-branding.md)
