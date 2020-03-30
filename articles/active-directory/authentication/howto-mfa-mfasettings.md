---
title: Konfigurace azure vícefaktorového ověřování – Azure Active Directory
description: Tento článek popisuje, jak nakonfigurovat nastavení Azure Multi-Factor Authentication na webu Azure Portal
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 077032e4fe3886d5bf9a678dffdffca1a5802091
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263800"
---
# <a name="configure-azure-multi-factor-authentication-settings"></a>Konfigurace nastavení služby Azure Multi-Factor Authentication

Tento článek vám pomůže spravovat nastavení vícefaktorového ověřování na webu Azure Portal. Zahrnuje různá témata, která vám pomůžou maximálně využít Azure Multi-Factor Authentication. Ne všechny funkce jsou k dispozici ve všech verzích Azure Multi-Factor Authentication.

K nastavení souvisejícímu s vícefaktorovým ověřováním Azure můžete přistupovat z portálu Azure tak, že přejdete na **Azure Active Directory** > **Security** > **MFA**.

![Portál Azure – nastavení vícefaktorového ověřování Azure AD](./media/howto-mfa-mfasettings/multi-factor-authentication-settings-portal.png)

## <a name="settings"></a>Nastavení

Některá z těchto nastavení platí pro server MFA, Azure MFA nebo obojí.

| Funkce | Popis |
| ------- | ----------- |
| Uzamčení účtu | Dočasně uzamkněte účty ve službě vícefaktorového ověřování, pokud je v řadě příliš mnoho pokusů o odepření ověření. Tato funkce se vztahuje pouze na uživatele, kteří zadají kód PIN k ověření. (Server MFA) |
| [Blokovat/odblokovat uživatele](#block-and-unblock-users) | Slouží k zablokování konkrétních uživatelů, aby mohli přijímat požadavky na vícefaktorové ověřování. Všechny pokusy o ověření zablokovaných uživatelů se automaticky zamítnou. Uživatelé zůstanou zablokovaní po dobu 90 dnů od data zablokování. |
| [Výstraha podvodů](#fraud-alert) | Konfigurace nastavení souvisejících se schopností uživatelů nahlásit podvodné žádosti o ověření |
| [Oznámení](#notifications) | Povolte oznámení o událostech ze serveru MFA. |
| [Tokeny OATH](concept-authentication-methods.md#oath-hardware-tokens-public-preview) | Používá se v cloudových prostředích Azure MFA ke správě tokenů OATH pro uživatele. |
| [Nastavení telefonního hovoru](#phone-call-settings) | Nakonfigurujte nastavení související s telefonními hovory a pozdravy pro cloudová a místní prostředí. |
| Poskytovatelé | Zobrazí se všichni stávající poskytovatelé ověřování, které jste mohli přidružit ke svému účtu. Září 2018 nemusí být vytvořeni noví poskytovatelé ověřování. |

## <a name="manage-mfa-server"></a>Správa serveru MFA

Nastavení v této části jsou pouze pro server MFA.

| Funkce | Popis |
| ------- | ----------- |
| Nastavení serveru | Stažení serveru MFA a generování aktivačních pověření pro inicializaci prostředí |
| [Jednorázový obtok](#one-time-bypass) | Umožněte uživateli ověření bez omezeného provedení dvoustupňového ověření. |
| [Pravidla ukládání do mezipaměti](#caching-rules) |  Ukládání do mezipaměti se používá především v případě, že místní systémy, jako je například VPN, odesílají více žádostí o ověření, zatímco první požadavek stále probíhá. Tato funkce umožňuje následné požadavky úspěšné automaticky, poté, co uživatel uspěje první ověření probíhá. |
| Stav serveru | Podívejte se na stav místních serverů MFA včetně verze, stavu, IP adresy a času a data poslední komunikace. |

## <a name="activity-report"></a>Sestava aktivity

Sestavy, které jsou zde k dispozici, jsou specifické pro server MFA (místně). Pro sestavy Azure MFA (cloud) viz sestavy přihlášení ve službě Azure AD.

## <a name="block-and-unblock-users"></a>Blokování a odblokování uživatelů

Pomocí funkce _blokovat a odblokovat uživatele_ zabráníte uživatelům přijímat požadavky na ověření. Všechny pokusy o ověření zablokovaných uživatelů se automaticky zamítnou. Uživatelé zůstanou zablokovaní po dobu 90 dnů od data zablokování.

### <a name="block-a-user"></a>Zablokování uživatele

1. Přihlaste se k [portálu Azure](https://portal.azure.com) jako správce.
2. Přejděte na Azure **Active Directory** > **Security** > **MFA** > **Block/unblock users**.
3. Vyberte **Přidat,** chcete-li blokovat uživatele.
4. Vyberte **replikační skupinu**. Zadejte uživatelské jméno blokovaného uživatele jako **uživatelské jméno\@domain.com**. Do pole **Důvod** zadejte poznámku.
5. Chcete-li dokončit blokování uživatele, vyberte **přidat.**

### <a name="unblock-a-user"></a>Odblokování uživatele

1. Přihlaste se k [portálu Azure](https://portal.azure.com) jako správce.
2. Přejděte na Azure **Active Directory** > **Security** > **MFA** > **Block/unblock users**.
3. Ve sloupci **Akce** vedle uživatele odblokujte možnost **Odblokovat.**
4. Do pole **Důvod odblokování** zadejte komentář.
5. Chcete-li dokončit odblokování uživatele, vyberte **odblokovat.**

## <a name="fraud-alert"></a>Výstraha podvodů

Nakonfigurujte funkci upozornění na _podvod,_ aby uživatelé mohli nahlásit podvodné pokusy o přístup ke svým prostředkům. Uživatelé mohou nahlásit pokusy o podvod pomocí mobilní aplikace nebo prostřednictvím svého telefonu.

### <a name="turn-on-fraud-alerts"></a>Zapnutí upozornění na podvody

1. Přihlaste se k [portálu Azure](https://portal.azure.com) jako správce.
2. Přejděte na výstrahu zabezpečení**služby** >  **Azure Active Directory** > **MFA** > **pro podvody**.
3. Nastavte nastavení **Povolit uživatelům odesílat upozornění na podvody** **na zapnuto**.
4. Vyberte **Uložit**.

### <a name="configuration-options"></a>Možnosti konfigurace

* **Blokovat uživatele, když je podvod nahlášen**: Pokud uživatel nahlásí podvod, jeho účet je zablokován po dobu 90 dnů nebo dokud správce neodblokuje jejich účet. Správce může zkontrolovat přihlášení pomocí sestavy přihlášení a přijmout vhodná opatření, aby se zabránilo budoucím podvodům. Správce pak může [odblokovat](#unblock-a-user) uživatelský účet.
* **Kód pro nahlášení podvodu během počátečního pozdravu**: Když uživatelé **#** obdrží telefonní hovor k provedení dvoustupňového ověření, obvykle stisknou potvrzení přihlášení. Chcete-li nahlásit podvod, uživatel **#** před stisknutím tlačítka zadá kód . Tento kód je ve výchozím nastavení **0,** ale můžete jej přizpůsobit.

   >[!NOTE]
   >Výchozí hlasové pozdravy od společnosti Microsoft instruují uživatele, aby stiskli **hodnotu 0#** a odeslali upozornění na podvod. Pokud chcete použít jiný kód než **0**, nahrajte a nahrajte vlastní hlasové pozdravy s příslušnými pokyny pro uživatele.
   >

### <a name="view-fraud-reports"></a>Zobrazit zprávy o podvodech

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. Vyberte**přihlášení k**službě Azure **Active Directory** > . Sestava podvodů je teď součástí standardní sestavy přihlášení azure ad.

## <a name="notifications"></a>Oznámení

Zde nakonfigurujte e-mailové adresy pro uživatele, kteří obdrží e-maily s upozorněním na podvody.

![Ukázka e-mailu s upozorněním na oznámení o podvodu](./media/howto-mfa-mfasettings/multi-factor-authentication-fraud-alert-email.png)

## <a name="phone-call-settings"></a>Nastavení telefonního hovoru

### <a name="caller-id"></a>ID volajícího

**MFA id volajícího** - Toto je číslo, které vaši uživatelé uvidí na svém telefonu. Jsou povolena pouze čísla založená na USA.

>[!NOTE]
>Když jsou vícefaktorová volání ověřování umístěna prostřednictvím veřejné telefonní sítě, někdy jsou směrována prostřednictvím operátora, který nepodporuje ID volajícího. Z tohoto důvodu není zaručeno ID volajícího, i když systém vícefaktorového ověřování vždy odešle.

Ve Spojených státech, pokud jste nenakonfigurovali ID volajícího MFA, hlasové hovory od Microsoftu pocházejí z následujících čísel: +1 (866) 539 4191, +1 (855) 330 8653 a +1 (877) 668 6536. Pokud používáte filtry nevyžádané pošty, nezapomeňte tato čísla vyloučit.

### <a name="custom-voice-messages"></a>Vlastní hlasové zprávy

Vlastní nahrávky nebo pozdravy můžete použít pro dvoustupňové ověření pomocí _funkce vlastních hlasových zpráv._ Tyto zprávy lze použít jako doplněk k nebo k nahrazení nahrávek společnosti Microsoft.

Než začnete, mějte na paměti následující omezení:

* Podporované formáty souborů jsou WAV a .mp3.
* Limit velikosti souboru je 1 MB.
* Ověřovací zprávy by měly být kratší než 20 sekund. Zprávy delší než 20 sekund mohou způsobit selhání ověření. Uživatel nemusí odpovědět před dokončením zprávy a časovým časem ověření.

### <a name="custom-message-language-behavior"></a>Chování jazyka vlastní zprávy

Při přehrávání vlastní hlasové zprávy uživateli závisí jazyk zprávy na těchto faktorech:

* Jazyk aktuálního uživatele.
  * Jazyk zjištěný prohlížečem uživatele.
  * Jiné scénáře ověřování se mohou chovat odlišně.
* Jazyk všech dostupných vlastních zpráv.
  * Tento jazyk je vybrán správcem při přidání vlastní zprávy.

Například pokud existuje pouze jedna vlastní zpráva, s jazykem němčiny:

* Uživatel, který se ověří v německém jazyce, uslyší vlastní německou zprávu.
* Uživatel, který se ověří v angličtině, uslyší standardní anglickou zprávu.

### <a name="set-up-a-custom-message"></a>Nastavení vlastní zprávy

1. Přihlaste se k [portálu Azure](https://portal.azure.com) jako správce.
1. Přejděte na**nastavení telefonních hovorů** **služby Azure Active Directory** > **Security** > **MFA** > .
1. Vyberte **Přidat pozdrav**.
1. Zvolte typ pozdravu.
1. Zvolte jazyk.
1. Vyberte zvukový soubor .mp3 nebo WAV, který chcete nahrát.
1. Vyberte **Přidat**.

### <a name="custom-voice-message-defaults"></a>Výchozí nastavení vlastníhlasové zprávy

Ukázkové skripty pro vytváření vlastních zpráv.

| Název zprávy | Skript |
| --- | --- |
| Ověření bylo úspěšné. | Vaše přihlášení bylo úspěšně ověřeno. Nashledanou. |
| Výzva k rozšíření | Děkujeme vám, že používáte systém ověřování přihlášení společnosti Microsoft. Pokračujte stisknutím klávesy pound. |
| Potvrzení o podvodu | Bylo odesláno upozornění na podvod. Chcete-li svůj účet odblokovat, obraťte se na oddělení it oddělení vaší společnosti. |
| Pozdrav za podvod (standardní) | Děkujeme vám, že používáte systém ověřování přihlášení společnosti Microsoft. Chcete-li dokončit ověření, stiskněte tlačítko libra. Pokud jste toto ověření nezahájili, může se někdo pokoušet o přístup k vašemu účtu. Prosím, stiskněte nulovou libru, abyste odeslali upozornění na podvod. Tím upozorníte it tým vaší společnosti a zablokujete další pokusy o ověření. |
| Nahlášený podvod Bylo odesláno upozornění na podvod. | Chcete-li svůj účet odblokovat, obraťte se na oddělení it oddělení vaší společnosti. |
| Aktivace | Děkujeme vám, že používáte systém ověřování přihlášení společnosti Microsoft. Chcete-li dokončit ověření, stiskněte tlačítko libra. |
| Opakované ověření bylo odepřeno. | Ověření bylo odmítnuto. |
| Opakování (standardní) | Děkujeme vám, že používáte systém ověřování přihlášení společnosti Microsoft. Chcete-li dokončit ověření, stiskněte tlačítko libra. |
| Pozdrav (standardní) | Děkujeme vám, že používáte systém ověřování přihlášení společnosti Microsoft. Chcete-li dokončit ověření, stiskněte tlačítko libra. |
| Pozdrav (PIN) | Děkujeme vám, že používáte systém ověřování přihlášení společnosti Microsoft. Zadejte kód PIN následovaný klíčem libry k dokončení ověření. |
| Pozdrav za podvod (PIN) | Děkujeme vám, že používáte systém ověřování přihlášení společnosti Microsoft.  Zadejte kód PIN následovaný klíčem libry k dokončení ověření. Pokud jste toto ověření nezahájili, může se někdo pokoušet o přístup k vašemu účtu. Prosím, stiskněte nulovou libru, abyste odeslali upozornění na podvod. Tím upozorníte it tým vaší společnosti a zablokujete další pokusy o ověření. |
| Opakovat (PIN) | Děkujeme vám, že používáte systém ověřování přihlášení společnosti Microsoft. Zadejte kód PIN následovaný klíčem libry k dokončení ověření. |
| Výzva k rozšíření po číslicích | Pokud již v tomto rozšíření, stiskněte tlačítko libra pokračovat. |
| Ověřování bylo odepřeno. | Je mi líto, ale v tuto chvíli vás nemůžeme zapsat. Zkuste to prosím znovu později. |
| Aktivační pozdrav (standardní) | Děkujeme vám, že používáte systém ověřování přihlášení společnosti Microsoft. Chcete-li dokončit ověření, stiskněte tlačítko libra. |
| Pokus o aktivaci (standardní) | Děkujeme vám, že používáte systém ověřování přihlášení společnosti Microsoft. Chcete-li dokončit ověření, stiskněte tlačítko libra. |
| Aktivační pozdrav (PIN) | Děkujeme vám, že používáte systém ověřování přihlášení společnosti Microsoft. Zadejte kód PIN následovaný klíčem libry k dokončení ověření. |
| Výzva k rozšíření před číslicemi | Děkujeme vám, že používáte systém ověřování přihlášení společnosti Microsoft. Přeneste prosím toto volání na prodloužení ... |

## <a name="one-time-bypass"></a>Jednorázový obtok

Jednorázová funkce _obejití_ umožňuje uživateli ověřit jeden čas bez provedení dvoustupňového ověření. Obtok je dočasný a vyprší po zadaném počtu sekund. V situacích, kdy mobilní aplikace nebo telefon nepřijímá oznámení nebo telefonní hovor, můžete povolit jednorázový obtok, aby měl uživatel přístup k požadovanému prostředku.

### <a name="create-a-one-time-bypass"></a>Vytvoření jednorázového obtoku

1. Přihlaste se k [portálu Azure](https://portal.azure.com) jako správce.
2. Přejděte na Azure **Active Directory** > **Security** > **MFA** > **Jednorázový bypass**.
3. Vyberte **Přidat**.
4. V případě potřeby vyberte replikační skupinu pro obtok.
5. Zadejte uživatelské jméno jako **uživatelské jméno\@domain.com**. Zadejte počet sekund, po které má bypřettt. Zadejte důvod obtoku.
6. Vyberte **Přidat**. Lhůta vstoupí v platnost okamžitě. Uživatel se musí přihlásit před vypršením jednorázového bypassu.

### <a name="view-the-one-time-bypass-report"></a>Zobrazit jednorázovou sestavu obchvatu

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. Přejděte na Azure **Active Directory** > **Security** > **MFA** > **Jednorázový bypass**.

## <a name="caching-rules"></a>Pravidla ukládání do mezipaměti

Můžete nastavit časové období, které umožní pokusy o ověření po ověření uživatele pomocí funkce _ukládání do mezipaměti._ Následné pokusy o ověření pro uživatele v zadaném časovém období se automaticky podaří. Ukládání do mezipaměti se používá především v případě, že místní systémy, jako je například VPN, odesílají více žádostí o ověření, zatímco první požadavek stále probíhá. Tato funkce umožňuje následné požadavky úspěšné automaticky, poté, co uživatel uspěje první ověření probíhá.

>[!NOTE]
>Funkce ukládání do mezipaměti není určena pro přihlášení do služby Azure Active Directory (Azure AD).

### <a name="set-up-caching"></a>Nastavení ukládání do mezipaměti

1. Přihlaste se k [portálu Azure](https://portal.azure.com) jako správce.
2. Přejděte na**pravidla ukládání do mezipaměti služby** **Azure Active Directory** > **Security** > **MFA** > .
3. Vyberte **Přidat**.
4. V rozevíracím seznamu vyberte **typ mezipaměti.** Zadejte maximální počet **sekund mezipaměti**.
5. V případě potřeby vyberte typ ověřování a zadejte aplikaci.
6. Vyberte **Přidat**.

## <a name="mfa-service-settings"></a>Nastavení služby MFA

Nastavení pro hesla aplikací, důvěryhodné IP adresy, možnosti ověření a zapamatování vícefaktorového ověřování pro azure multi-factor authentication najdete v nastavení služby. K nastavení služby lze přistupovat z portálu Azure procházením služby **Azure Active Directory** > **Security** > **MFA** > **Začínáme** > **Konfigurovat** > další**cloudová nastavení MFA**.

![Nastavení služby Azure Multi-Factor Authentication](./media/howto-mfa-mfasettings/multi-factor-authentication-settings-service-settings.png)

Rozsahy důvěryhodných adres IP mohou být soukromé nebo veřejné.

## <a name="app-passwords"></a>Hesla aplikací

Některé aplikace, například Office 2010 nebo starší a Apple Mail před iOS 11, nepodporují dvoustupňové ověření. Aplikace nejsou nakonfigurovány tak, aby přijímali druhé ověření. Chcete-li tyto aplikace používat, využijte funkci _hesel aplikací._ Místo tradičního hesla můžete aplikaci umožnit, aby se pomocí dvoustupňového ověření obešla a pokračovala v práci.

Moderní ověřování je podporováno pro klienty Microsoft Office 2013 a novější. Klienti Office 2013 včetně Outlooku podporují moderní ověřovací protokoly a můžou pracovat s dvoustupňovým ověřováním. Po povolení klienta nejsou pro klienta vyžadována hesla aplikací.

>[!NOTE]
>Hesla aplikací nefungují s vícefaktorovými zásadami ověřování založenými na podmíněném přístupu a s moderním ověřováním.

### <a name="considerations-about-app-passwords"></a>Důležité informace o heslech aplikací

Při používání hesel aplikací zvažte následující důležité body:

* Hesla aplikací se zadávají pouze jednou pro aplikaci. Uživatelé nemusí sledovat hesla nebo je pokaždé zadávat.
* Skutečné heslo je automaticky generováno a není dodáno uživatelem. Automaticky generované heslo je pro útočníka těžší uhodnout a je bezpečnější.
* Existuje limit 40 hesel na uživatele.
* Aplikace, které ukládat hesla do mezipaměti a používat je v místních scénářích může začít selhat, protože heslo aplikace není známo mimo pracovní nebo školní účet. Příkladem tohoto scénáře jsou e-maily exchange, které jsou místní, ale archivovaná pošta je v cloudu. V tomto scénáři nefunguje stejné heslo.
* Po povolení vícefaktorového ověřování u uživatelského účtu lze hesla aplikací používat u většiny klientů, kteří nejsou v prohlížeči, jako je Outlook a Microsoft Skype pro firmy. Akce správy nelze provádět pomocí hesel aplikací prostřednictvím aplikací, které nejsou v prohlížeči, jako je například Prostředí Windows PowerShell. Akce nelze provést ani v případě, že má uživatel účet pro správu. Chcete-li spustit skripty prostředí PowerShell, vytvořte účet služby se silným heslem a nepovolujte účet pro dvoustupňové ověření.

>[!WARNING]
>Hesla aplikací nefungují v hybridních prostředích, kde klienti komunikují s místními i cloudovými koncovými body s automatickým zjišťováním. Hesla domény jsou vyžadována k ověření místně. Hesla aplikací jsou vyžadována k ověření pomocí cloudu.

### <a name="guidance-for-app-password-names"></a>Pokyny pro názvy hesel aplikace

Názvy hesel aplikací by měly odrážet zařízení, na kterém se používají. Pokud máte přenosný počítač, který má aplikace, které nejsou součástí prohlížeče, jako je Outlook, Word a Excel, vytvořte pro tyto aplikace jedno heslo aplikace s názvem **Přenosný počítač.** Vytvořte jiné heslo aplikace s názvem **Plocha** pro stejné aplikace, které běží ve vašem stolním počítači.

>[!NOTE]
>Doporučujeme vytvořit jedno heslo aplikace pro zařízení, nikoli jedno heslo aplikace pro aplikaci.

### <a name="federated-or-single-sign-on-app-passwords"></a>Hesla federovaných nebo přihlašovaných aplikací

Azure AD podporuje federace nebo jednotné přihlašování (SSO) s místní službou Windows Server Active Directory Domain Services (AD DS). Pokud je vaše organizace federovaná pomocí Azure AD a používáte Azure Multi-Factor Authentication, zvažte následující body o heslech aplikací.

>[!NOTE]
>Následující body platí pouze pro federované zákazníky (SSO).

* Hesla aplikací jsou ověřeny Azure AD a proto obejít federace. Federace se aktivně používá pouze při nastavování hesel aplikací.
* Zprostředkovatel identity (IdP) není kontaktován pro federované uživatele (SSO), na rozdíl od pasivního toku. Hesla aplikací jsou uložena v pracovním nebo školním účtu. Pokud uživatel opustí společnost, informace o uživateli proudí do pracovního nebo školního účtu pomocí **DirSync** v reálném čase. Zakázání nebo odstranění účtu může trvat až tři hodiny k synchronizaci, což může zpozdit zakázání nebo odstranění hesla aplikace ve službě Azure AD.
* Místní nastavení řízení přístupu klienta nejsou dodržena funkcí hesel aplikace.
* Není k dispozici žádná funkce protokolování a auditování místního ověřování pro použití s funkcí hesel aplikace.
* Některé pokročilé architektury vyžadují kombinaci pověření pro dvoustupňové ověření s klienty. Tato pověření mohou zahrnovat uživatelské jméno a hesla pracovního nebo školního účtu a hesla aplikací. Požadavky závisí na způsobu ověřování. Pro klienty, kteří se ověřují podle místní infrastruktury, uživatelské jméno pracovního nebo školního účtu a heslo je povinné. Pro klienty, kteří se ověřují ve službě Azure AD, je vyžadováno heslo aplikace.

  Předpokládejme například, že máte následující architekturu:

  * Vaše místní instance služby Active Directory je federována pomocí služby Azure AD.
  * Používáte Exchange online.
  * Používáte Skype pro firmy místně.
  * Používáte Azure Multi-Factor Authentication.

  V tomto scénáři použijete následující pověření:

  * Pokud se chcete přihlásit ke Skypu pro firmy, použijte uživatelské jméno a heslo svého pracovního nebo školního účtu.
  * Chcete-li získat přístup k adresáři z klienta Outlooku, který se připojuje k Exchange online, použijte heslo aplikace.

### <a name="allow-users-to-create-app-passwords"></a>Povolit uživatelům vytvářet hesla aplikací

Ve výchozím nastavení uživatelé nemohou vytvářet hesla aplikací. Funkce hesel aplikace musí být povolena. Chcete-li uživatelům poskytnout možnost vytvářet hesla aplikací, použijte následující postup:

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. Vlevo vyberte **možnost Uživatelé služby Azure Active Directory** > **Users**.
3. Vyberte **vícefaktorové ověřování**.
4. V části Vícefaktorové ověřování vyberte **nastavení služby**.
5. Na stránce **Nastavení služby** vyberte možnost **Povolit uživatelům vytvářet hesla aplikací pro přihlášení k aplikacím, které nejsou v prohlížeči.**

### <a name="create-app-passwords"></a>Vytváření hesel aplikací

Uživatelé mohou vytvářet hesla aplikací během počáteční registrace. Uživatel má možnost vytvořit hesla aplikací na konci procesu registrace.

Uživatelé mohou také vytvářet hesla aplikací po registraci. Další informace a podrobné kroky pro uživatele najdete v tématu [Co jsou hesla aplikací v Azure Multi-Factor Authentication?](../user-help/multi-factor-authentication-end-user-app-passwords.md)

## <a name="trusted-ips"></a>Důvěryhodné IP adresy

Funkce _Důvěryhodné IP adresy_ azure vícefaktorového ověřování používají správci spravovaného nebo federovaného tenanta. Tato funkce obchází dvoustupňové ověření pro uživatele, kteří se přihlašují z podnikového intranetu. Tato funkce je k dispozici s plnou verzí Azure Multi-Factor Authentication a ne bezplatnou verzi pro správce. Podrobnosti o tom, jak získat plnou verzi Azure Multi-Factor Authentication, najdete v [tématu Azure Multi-Factor Authentication](multi-factor-authentication.md).

> [!NOTE]
> Důvěryhodné ip adresy mfa a s názvem umístění podmíněného přístupu fungují pouze s adresami IPV4.

Pokud vaše organizace nasadí rozšíření NPS poskytovat vícefaktorové ověřování místním aplikacím, poznamenejte si, že zdrojová adresa IP se bude vždy jevit jako server NPS, kterým protéká pokus o ověření.

| Typ klienta Azure AD | Možnosti funkcí důvěryhodných IP serverů |
|:--- |:--- |
| Spravovaní |**Specifický rozsah ADRES IP**: Správci určují rozsah adres IP, které mohou obejít dvoustupňové ověření pro uživatele, kteří se přihlašují z podnikového intranetu. Lze nakonfigurovat maximálně 50 důvěryhodných rozsahů IP adres.|
| Federovaní |**Všichni federující uživatelé**: Všichni federující uživatelé, kteří se přihlásí zevnitř organizace, mohou obejít dvoustupňové ověření. Ověření uživatelé obejdou pomocí deklarace, která je vydána službou AD FS (Active Directory Federation Services).<br/>**Specifický rozsah ADRES IP**: Správci určují rozsah adres IP, které mohou obejít dvoustupňové ověření pro uživatele, kteří se přihlašují z podnikového intranetu. |

Obejít důvěryhodné IP adresy funguje pouze zevnitř podnikového intranetu. Pokud vyberete možnost **Všichni federující uživatelé** a uživatel se přihlásí mimo firemní intranet, musí se uživatel ověřit pomocí dvoustupňového ověření. Proces je stejný i v případě, že uživatel zobrazí deklaraci ad FS. 

### <a name="end-user-experience-inside-of-corpnet"></a>Zkušenosti koncových uživatelů uvnitř korpusu

Pokud je funkce Důvěryhodné IP adresy zakázána, je pro toky prohlížeče vyžadováno dvoustupňové ověření. Hesla aplikací jsou vyžadována pro starší bohaté klientské aplikace.

Pokud je povolena funkce Důvěryhodné IP adresy, *dvoustupňové* ověření není nutné pro toky prohlížeče. Hesla aplikací *nejsou* vyžadována pro starší bohaté klientské aplikace za předpokladu, že uživatel nevytvořil heslo aplikace. Po použití hesla aplikace zůstane vyžadováno. 

### <a name="end-user-experience-outside-corpnet"></a>Zkušenosti koncových uživatelů mimo korpus

Bez ohledu na to, zda je povolena funkce důvěryhodné IP adresy, je pro toky prohlížeče vyžadováno dvoustupňové ověření. Hesla aplikací jsou vyžadována pro starší bohaté klientské aplikace.

### <a name="enable-named-locations-by-using-conditional-access"></a>Povolení pojmenovaných umístění pomocí podmíněného přístupu

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. Vlevo vyberte umístění**s podmíněným přístupem k** > **zabezpečení služby** >  **Azure Active Directory** > **S názvem**.
3. Vyberte **nové umístění**.
4. Zadejte název umístění.
5. Vyberte **možnost Označit jako důvěryhodné umístění**.
6. Zadejte rozsah IP v cidr notaci jako **192.168.1.1/24**.
7. Vyberte **Vytvořit**.

### <a name="enable-the-trusted-ips-feature-by-using-conditional-access"></a>Povolení funkce Důvěryhodné ip adresy pomocí podmíněného přístupu

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. Vlevo vyberte umístění**s podmíněným přístupem k** > **zabezpečení služby** >   **Azure Active Directory** > **S názvem**.
3. Vyberte **konfigurovat důvěryhodné ip adresy MFA**.
4. Na stránce **Nastavení služby** vyberte v části **Důvěryhodné IP adresy**některou z následujících dvou možností:

   * **U požadavků od federovaných uživatelů pocházejících z mého intranetu**: Chcete-li tuto možnost zvolit, zaškrtněte toto políčko. Všichni federovaní uživatelé, kteří se přihlašují z podnikové sítě, obejdou dvoustupňové ověření pomocí deklarace, která je vydána službou AD FS. Ujistěte se, že služba AD FS má pravidlo pro přidání deklarace intranetu do příslušného provozu. Pokud pravidlo neexistuje, vytvořte ve sponě ad FS následující pravidlo:

      `c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);`

   * **Pro požadavky z určitého rozsahu veřejných IP adres**: Chcete-li zvolit tuto možnost, zadejte IP adresy do textového pole pomocí zápisu CIDR.
      * Pro IP adresy, které jsou v rozsahu xxx.xxx.xxx.1 až xxx.xxx.xxx.254, použijte notaci jako **xxx.xxx.xxx.0/24**.
      * Pro jednu adresu IP použijte notaci jako **xxx.xxx.xxx.xxx/32**.
      * Zadejte až 50 rozsahů IP adres. Uživatelé, kteří se přihlašují z těchto IP adres, obejdou dvoustupňové ověření.

5. Vyberte **Uložit**.

### <a name="enable-the-trusted-ips-feature-by-using-service-settings"></a>Povolení funkce Důvěryhodné ip adresy pomocí nastavení služby

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. Vlevo vyberte **možnost Uživatelé služby Azure Active Directory** > **Users**.
3. Vyberte **vícefaktorové ověřování**.
4. V části Vícefaktorové ověřování vyberte **nastavení služby**.
5. Na stránce **Nastavení služby** v části **Důvěryhodné IP adresy**zvolte jednu (nebo obě) z následujících dvou možností:

   * **U požadavků od federovaných uživatelů v intranetu**: Chcete-li tuto možnost zvolit, zaškrtněte toto políčko. Všichni federovaní uživatelé, kteří se přihlašují z podnikové sítě, obejdou dvoustupňové ověření pomocí deklarace, která je vydána službou AD FS. Ujistěte se, že služba AD FS má pravidlo pro přidání deklarace intranetu do příslušného provozu. Pokud pravidlo neexistuje, vytvořte ve sponě ad FS následující pravidlo:

      `c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);`

   * **Pro požadavky ze zadaného rozsahu podsítí IP adres**: Chcete-li zvolit tuto možnost, zadejte ip adresy do textového pole pomocí zápisu CIDR.
      * Pro IP adresy, které jsou v rozsahu xxx.xxx.xxx.1 až xxx.xxx.xxx.254, použijte notaci jako **xxx.xxx.xxx.0/24**.
      * Pro jednu adresu IP použijte notaci jako **xxx.xxx.xxx.xxx/32**.
      * Zadejte až 50 rozsahů IP adres. Uživatelé, kteří se přihlašují z těchto IP adres, obejdou dvoustupňové ověření.

6. Vyberte **Uložit**.

## <a name="verification-methods"></a>Metody ověřování

Můžete zvolit metody ověření, které jsou k dispozici pro uživatele. Následující tabulka obsahuje stručný přehled metod.

Když uživatelé zaregistrují své účty pro azure vícefaktorové ověřování, zvolí si upřednostňovanou metodu ověření z možností, které jste povolili. Pokyny pro proces registrace uživatele jsou uvedeny v části [Nastavení účtu pro dvoustupňové ověření](../user-help/multi-factor-authentication-end-user-first-time.md).

| Metoda | Popis |
|:--- |:--- |
| Volání na telefon |Umístí automatický hlasový hovor. Uživatel přijme hovor a stiskem tlačítka # na klávesnici telefonu provede ověření. Telefonní číslo není synchronizováno s místní službou Active Directory. |
| Textová zpráva do telefonu |Odešle textovou zprávu, která obsahuje ověřovací kód. Uživatel je vyzván k zadání ověřovacího kódu do přihlašovacího rozhraní. Tento proces se nazývá jednosměrná SMS. Obousměrná SMS znamená, že uživatel musí text zpět konkrétní kód. Obousměrná SMS je již po 14. listopadu 2018 zastaralá a není podporována. Správci by měli povolit jinou metodu pro uživatele, kteří dříve používali obousměrnou sms.|
| Oznámení prostřednictvím mobilní aplikace |Odešle nabízené oznámení do telefonu nebo registrovaného zařízení. Uživatel zobrazí oznámení a vybere **ověření ověřit.** Aplikace Microsoft Authenticator je k dispozici pro [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6), [Android](https://go.microsoft.com/fwlink/?Linkid=825072)a [iOS](https://go.microsoft.com/fwlink/?Linkid=825073). |
| Ověřovací kód z mobilní aplikace nebo hardwarového tokenu |Aplikace Microsoft Authenticator generuje každých 30 sekund nový ověřovací kód OATH. Uživatel zadá ověřovací kód do přihlašovacího rozhraní. Aplikace Microsoft Authenticator je k dispozici pro [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6), [Android](https://go.microsoft.com/fwlink/?Linkid=825072)a [iOS](https://go.microsoft.com/fwlink/?Linkid=825073). |

### <a name="enable-and-disable-verification-methods"></a>Povolení a zakázání metod ověření

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. Vlevo vyberte **možnost Uživatelé služby Azure Active Directory** > **Users**.
3. Vyberte **vícefaktorové ověřování**.
4. V části Vícefaktorové ověřování vyberte **nastavení služby**.
5. Na stránce **Nastavení služby** v části **Možnosti ověření**vyberte nebo zrušte výběr metod, které mají být poskytnuty uživatelům.
6. Klikněte na **Uložit**.

Další podrobnosti o použití metod ověřování naleznete v článku [Jaké jsou metody ověřování](concept-authentication-methods.md).

## <a name="remember-multi-factor-authentication"></a>Zapamatovat si vícefaktorové ověřování

Funkce _zapamatování vícefaktorového ověřování_ pro zařízení a prohlížeče, kterým uživatel důvěřuje, je bezplatná funkce pro všechny uživatele vícefaktorového ověřování. Po úspěšném přihlášení k zařízení přes Multi-Factor Authentication můžou uživatelé obejít následná ověření během určitého počtu dnů. Tato funkce zvyšuje použitelnost tím, že minimalizuje počet, kolikrát musí uživatel provést dvoustupňové ověření na stejném zařízení.

>[!IMPORTANT]
>Pokud dojde k ohrožení zabezpečení účtu nebo zařízení, může mít zapamatování vícefaktorového ověřování pro důvěryhodná zařízení vliv na zabezpečení. Pokud dojde k ohrožení podnikového účtu nebo ztrátě nebo odcizení důvěryhodného zařízení, měli byste [odvolat relace vícefaktorové registrace](howto-mfa-userdevicesettings.md).
>
>Akce obnovení odvolá důvěryhodný stav ze všech zařízení a uživatel je povinen provést dvoustupňové ověření znovu. Můžete také dát uživatelům pokyn, aby obnovili vícefaktorové ověřování na svých vlastních zařízeních pomocí pokynů v části [Správa nastavení pro dvoustupňové ověřování](../user-help/multi-factor-authentication-end-user-manage-settings.md#turn-on-two-factor-verification-prompts-on-a-trusted-device).

### <a name="how-the-feature-works"></a>Jak funkce funguje

Funkce zapamatovat vícefaktorové ověřování nastaví v prohlížeči trvalý soubor cookie, když uživatel při přihlášení vybere možnost **Neptat se znovu** na možnost X dní. Uživatel není znovu vyzván k vícefaktorovému ověřování ze stejného prohlížeče, dokud cookie nevyprší. Pokud uživatel otevře jiný prohlížeč na stejném zařízení nebo vymaže soubory cookie, zobrazí se výzva k ověření znovu.

Možnost **Neptat se znovu na X dní** se nezobrazuje v aplikacích bez prohlížeče, bez ohledu na to, zda aplikace podporuje moderní ověřování. Tyto aplikace používají _obnovovací tokeny,_ které poskytují nové přístupové tokeny každou hodinu. Při ověření obnovovacího tokenu Azure AD zkontroluje, že došlo k poslednímu dvoustupňovému ověření během zadaného počtu dní.

Tato funkce snižuje počet ověřování ve webových aplikacích, které obvykle vyzve pokaždé. Tato funkce zvyšuje počet ověřování pro moderní klienty ověřování, které obvykle výzvu každých 90 dní. Může také zvýšit počet ověřování v kombinaci se zásadami podmíněného přístupu.

>[!IMPORTANT]
>Funkce **zapamatovat vícefaktorové ověřování** není kompatibilní s funkcí **keep me signed in** ve službě AD FS, když uživatelé provádějí dvoustupňové ověření služby AD FS prostřednictvím serveru Azure Multi-Factor Authentication Server nebo řešení vícefaktorového ověřování třetí strany.
>
>Pokud uživatelé **zvolí, aby mě udrželi přihlášeni ve** službě AD FS a také označili své zařízení jako důvěryhodné pro vícefaktorové ověřování, uživatel není automaticky ověřen po vypršení počtu dní **zapamatování vícefaktorového ověřování.** Azure AD požaduje nové dvoustupňové ověření, ale služba AD FS vrátí token s původní deklarací a datem vícefaktorového ověřování, místo aby znovu provedla dvoustupňové ověření. **Tato reakce spustí ověřovací smyčku mezi Azure AD a AD FS.**
>
>Funkce **zapamatování vícefaktorového ověřování** není kompatibilní s uživateli B2B a nebude viditelná pro uživatele B2B při přihlašování k pozvaným klientům.
>

### <a name="enable-remember-multi-factor-authentication"></a>Povolení zapamatování vícefaktorového ověřování

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. Vlevo vyberte **možnost Uživatelé služby Azure Active Directory** > **Users**.
3. Vyberte **vícefaktorové ověřování**.
4. V části Vícefaktorové ověřování vyberte **nastavení služby**.
5. Na stránce **Nastavení služby** **můžete spravovat možnost zapamatování vícefaktorového ověřování**, vyberte možnost **Povolit uživatelům zapamatování vícefaktorového ověřování na zařízeních, kterým důvěřují.**
6. Nastavte počet dní, po které mohou důvěryhodná zařízení obejít dvoustupňové ověření. Výchozí hodnota je 14 dní.
7. Vyberte **Uložit**.

### <a name="mark-a-device-as-trusted"></a>Označení zařízení jako důvěryhodného

Po povolení funkce zapamatování vícefaktorového ověřování mohou uživatelé při přihlášení označit zařízení jako důvěryhodné tak, že zvolí **možnost Nezobrazovat další požadavky**.

## <a name="next-steps"></a>Další kroky

[Úprava značky přihlašovací stránky Azure AD](../fundamentals/customize-branding.md)
