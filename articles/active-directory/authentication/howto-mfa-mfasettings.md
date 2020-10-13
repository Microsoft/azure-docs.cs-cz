---
title: Konfigurace Azure Multi-Factor Authentication-Azure Active Directory
description: Přečtěte si, jak nakonfigurovat nastavení pro Azure Multi-Factor Authentication v Azure Portal
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 06/05/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.custom: contperfq4
ms.openlocfilehash: 29187ef99ce6628359819739ab5e4b34213eeb8a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91441628"
---
# <a name="configure-azure-multi-factor-authentication-settings"></a>Konfigurace nastavení služby Azure Multi-Factor Authentication

Pokud chcete přizpůsobit činnost koncového uživatele pro Azure Multi-Factor Authentication, můžete nakonfigurovat možnosti pro nastavení, jako jsou prahové hodnoty uzamčení účtu nebo výstrahy a oznámení o podvodech. Některá nastavení jsou přímo v Azure Portal pro Azure Active Directory (Azure AD), která jsou na samostatném portálu Azure Multi-Factor Authentication.

V Azure Portal jsou k dispozici následující nastavení Azure Multi-Factor Authentication:

| Funkce | Popis |
| ------- | ----------- |
| [Uzamčení účtu](#account-lockout) | Dočasné uzamčení účtů pomocí Azure Multi-Factor Authentication v případě, že je na řádku příliš mnoho zamítnutých pokusů o ověření. Tato funkce se vztahuje jenom na uživatele, kteří do ověřování zadají kód PIN. (MFA Server) |
| [Blokovat/odblokovat uživatele](#block-and-unblock-users) | Zablokovat konkrétním uživatelům, aby mohli přijímat žádosti o Multi-Factor Authentication Azure Všechny pokusy o ověření zablokovaných uživatelů se automaticky zamítnou. Uživatelé zůstávají zablokovaný po dobu 90 dnů od doby, kdy jsou zablokované nebo jsou ručně odblokované. |
| [Výstraha podvodů](#fraud-alert) | Nakonfigurujte nastavení, která uživatelům umožní nahlásit podvodné žádosti o ověření. |
| [Oznámení](#notifications) | Povolí oznámení událostí z MFA serveru. |
| [Tokeny OATH](concept-authentication-oath-tokens.md) | Používá se v cloudových prostředích Azure MFA ke správě tokenů OATH pro uživatele. |
| [Nastavení telefonního hovoru](#phone-call-settings) | Nakonfigurujte nastavení související s telefonními hovory a pozdravy pro cloudová a místní prostředí. |
| Zprostředkovatelé | Tím se zobrazí všechna existující zprostředkovatelé ověřování, které jste mohli k vašemu účtu přidružit. Od 1. září 2018 se nevytvoří Noví zprostředkovatelé ověřování. |

![Azure Portal – nastavení Multi-Factor Authentication Azure AD](./media/howto-mfa-mfasettings/multi-factor-authentication-settings-portal.png)

## <a name="account-lockout"></a>Uzamčení účtu

Aby se zabránilo opakovaným pokusům o MFA v rámci útoku, nastavení uzamčení účtu vám umožní určit, kolik neúspěšných pokusů se má povolit, než se účet po určitou dobu zamkne. Nastavení uzamčení účtu se aplikují jenom v případě, že se pro výzvu MFA zadá kód PIN.

K dispozici jsou následující nastavení:

* Počet odmítnutí MFA pro aktivaci uzamčení účtu
* Počet minut, než se obnoví čítač uzamčení účtu
* Počet minut, než se automaticky odblokuje účet

Chcete-li nakonfigurovat nastavení uzamčení účtů, proveďte následující nastavení:

1. Přihlaste se k [Azure Portal](https://portal.azure.com) jako správce.
1. Přejděte na **Azure Active Directory**  >  **zabezpečení**  >  **MFA**  >  **účtu**MFA.
1. Zadejte hodnoty vyžadovat pro vaše prostředí a pak vyberte **Uložit**.

    ![Snímek obrazovky s nastavením uzamčení účtu v Azure Portal](./media/howto-mfa-mfasettings/account-lockout-settings.png)

## <a name="block-and-unblock-users"></a>Blokování a odblokování uživatelů

Pokud došlo ke ztrátě nebo odcizení zařízení uživatele, můžete u přidruženého účtu zablokovat pokusy o Multi-Factor Authentication Azure. Všechny pokusy služby Azure Multi-Factor Authentication pro blokované uživatele budou automaticky odepřeny. Uživatelé zůstanou zablokovaní po dobu 90 dnů od data zablokování.

### <a name="block-a-user"></a>Blokování uživatele

Pokud chcete uživatele zablokovat, proveďte následující kroky:

1. Přejděte na **Azure Active Directory**  >  **zabezpečení**  >  **MFA**  >  **blokovat/odblokovat uživatele**.
1. Vyberte možnost **Přidat** a zablokovat uživatele.
1. Vyberte **replikační skupinu**a pak zvolte *Azure default*.

    Zadejte uživatelské jméno blokovaného uživatele jako `username\@domain.com` a pak zadejte komentář do pole *důvod* .
1. Až budete připraveni, vyberte **OK** a zablokujte uživatele.

### <a name="unblock-a-user"></a>Odblokování uživatele

Chcete-li odblokovat uživatele, proveďte následující kroky:

1. Přejděte na **Azure Active Directory**  >  **zabezpečení**  >  **MFA**  >  **blokovat/odblokovat uživatele**.
1. Ve sloupci *Akce* vedle požadovaného uživatele vyberte **odblokovat**.
1. Zadejte komentář do pole *důvod odblokování* .
1. Až budete připraveni, vyberte **OK** pro odblokování uživatele.

## <a name="fraud-alert"></a>Výstraha podvodů

Funkce upozornění na podvod uživatelům umožňuje nahlásit podvodné pokusy o přístup k jejich prostředkům. Když se přijme neznámá a podezřelá výzva MFA, můžou uživatelé ohlásit pokus o podvod pomocí aplikace Microsoft Authenticator nebo přes telefon.

K dispozici jsou následující možnosti konfigurace upozornění na podvod:

* **Automaticky blokovat uživatele, kteří nahlásili podvod**: Pokud uživatel nahlásí podvod, pokusy o ověření Azure MFA pro uživatelský účet jsou zablokované na 90 dní nebo dokud správce neblokuje svůj účet. Správce může kontrolovat přihlášení pomocí sestavy přihlášení a provést vhodná opatření, aby se předešlo budoucímu podvodování. Správce pak může [odblokovat](#unblock-a-user) účet uživatele.
* **Kód pro nahlášení podvodu při úvodním pozdravu**: když uživatelé obdrží telefonní hovor, aby provedl ověřování Multi-Factor Authentication, normálně se jim **#** potvrdili, že se přihlásí. Chcete-li nahlásit podvod, uživatel zadá kód před stisknutím klávesy **#** . Tento kód je ve výchozím nastavení **0** , ale můžete ho přizpůsobit.

   > [!NOTE]
   > Výchozí Hlasové pozdravy od Microsoftu přidávají uživatelům pokyn k odeslání výstrahy na podvod po stisku **0 #** . Pokud chcete použít jiný kód než **0**, zaznamenejte a nahrajte vlastní hlasové pozdravy s vhodnými pokyny pro uživatele.

Pokud chcete povolit a nakonfigurovat výstrahy podvodů, proveďte následující kroky:

1. Přejděte na **Azure Active Directory**  >  **Security**  >  **MFA**  >  **Výstraha o podvodech**v zabezpečení MFA.
1. Nastavte nastavení *povoleno uživatelům odeslat výstrahy na podvod* do **zapnuto**.
1. Nakonfigurujte *Automatické blokování uživatelů, kteří nahlásí podvod* nebo *kód, aby nahlásili podvod při počátečním nastavení pozdravu* podle potřeby.
1. Až budete připraveni, vyberte **Uložit**.

### <a name="view-fraud-reports"></a>Zobrazení sestav podvodů

Vyberte **Azure Active Directory**  >  **přihlašovací**  >  **údaje pro ověřování**. Sestava podvodů je teď součástí standardní sestavy přihlášení ke službě Azure AD a zobrazí se v **části Podrobnosti o výsledku** jako MFA zamítnuté. zadalo se podvodový kód.
 
## <a name="notifications"></a>Oznámení

E-mailová oznámení se dají nakonfigurovat, když uživatelé nahlásí výstrahy na podvod. Tato oznámení se většinou odesílají správcům identity, protože jsou pravděpodobně ohroženy přihlašovací údaje uživatelského účtu. Následující příklad ukazuje, jak vypadá e-mailová zpráva s upozorněním na podvod:

![Příklad e-mailu s upozorněním na podvod](./media/howto-mfa-mfasettings/multi-factor-authentication-fraud-alert-email.png)

Chcete-li konfigurovat oznámení o výstrahách podvodů, proveďte následující nastavení:

1. Přejděte na **Azure Active Directory**  >  **Security**  >  **Multi-Factor Authentication**zabezpečení a  >  **oznámení**.
1. Do následujícího pole zadejte e-mailovou adresu, kterou chcete přidat.
1. Pokud chcete odebrat stávající e-mailovou adresu, vyberte možnost **...** vedle požadované e-mailové adresy a pak vyberte **Odstranit**.
1. Až budete připraveni, vyberte **Uložit**.

## <a name="oath-tokens"></a>Tokeny OATH

Azure AD podporuje použití tokenů SHA-1 TOTP OATH, které aktualizují kódy každých 30 nebo 60 sekund. Zákazníci si můžou tyto tokeny koupit od dodavatele dle svého výběru.

Tokeny OATH TOTP jsou obvykle dodávány s tajným klíčem neboli osivem předem naprogramované v tokenu. Tyto klíče musí být zadané ve službě Azure AD, jak je popsáno v následujících krocích. Tajné klíče jsou omezené na 128 znaků, které nemusí být kompatibilní se všemi tokeny. Tajný klíč může obsahovat pouze znaky *a-z* nebo *a-z* a číslice *1-7*a musí být kódovány v *Base32*.

Programovatelné hardwarové tokeny OATH TOTP, které je možné znovu vyhodnotit, můžete nastavit pomocí Azure AD v toku nastavení softwarového tokenu.

Hardwarové tokeny OATH jsou podporovány v rámci verze Public Preview. Další informace o verzích Preview najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) verze Preview.

![Nahrávají se tokeny OATH do okna tokeny OATH MFA.](media/concept-authentication-methods/mfa-server-oath-tokens-azure-ad.png)

Po získání tokenů musí být nahrané ve formátu textového souboru s oddělovači (CSV), včetně hlavního názvu uživatele, sériového čísla, tajného klíče, časového intervalu, výrobce a modelu, jak je znázorněno v následujícím příkladu:

```csv
upn,serial number,secret key,time interval,manufacturer,model
Helga@contoso.com,1234567,1234567abcdef1234567abcdef,60,Contoso,HardwareKey
```

> [!NOTE]
> Ujistěte se, že jste do souboru CSV zahrnuli řádek záhlaví.

Po správném formátování jako souboru CSV se správce může přihlásit k Azure Portal, přejít k **Azure Active Directory > tokeny Oath zabezpečení > MFA >** a nahrát výsledný soubor CSV.

V závislosti na velikosti souboru CSV může zpracování trvat několik minut. Kliknutím na tlačítko **aktualizovat** zobrazíte aktuální stav. Pokud v souboru dojde k chybám, můžete si stáhnout soubor CSV se seznamem případných chyb, které můžete vyřešit. Názvy polí ve staženém souboru CSV se liší od nahrané verze.

Až budou všechny chyby vyřešené, může správce aktivovat každý klíč tak, že vybere možnost **aktivovat** pro token a vstoupí do jednorázového hesla zobrazeného na tokenu.

Uživatelé můžou mít kombinaci až pěti hardwarových tokenů OATH nebo ověřovacích aplikací, jako je například aplikace Microsoft Authenticator, nakonfigurovaná pro použití kdykoli.

## <a name="phone-call-settings"></a>Nastavení telefonního hovoru

Pokud uživatelé dostanou telefonní hovory na výzvy MFA, můžete nakonfigurovat své prostředí, jako je ID volajícího nebo hlasové pozdravy, které uslyší.

Pokud jste v USA nenakonfigurovali ID volajícího MFA, telefonní hovory od Microsoftu pocházejí z následujících čísel. Pokud používáte filtry spamu, ujistěte se, že tato čísla vyloučíte:

* *+ 1 (866) 539 4191*
* *+ 1 (855) 330 8653*
* *+ 1 (877) 668 6536*

> [!NOTE]
> Když jsou volání Azure Multi-Factor Authenticationa prostřednictvím veřejné telefonní sítě, někdy jsou volání směrována přes dopravce, který nepodporuje ID volajícího. Z tohoto důvodu není ID volajícího zaručené, i když ho Azure Multi-Factor Authentication vždycky odesílá. To platí pro telefonní hovory i pro textové zprávy, které poskytuje Azure Multi-Factor Authentication. Pokud potřebujete ověřit, jestli je textová zpráva z Azure Multi-Factor Authentication, přečtěte si informace [o tom, jaké krátké kódy SMS se používají pro posílání zpráv?](multi-factor-authentication-faq.md#what-sms-short-codes-are-used-for-sending-sms-messages-to-my-users)

Pokud chcete nakonfigurovat vlastní číslo ID volajícího, proveďte následující kroky:

1. Přejděte na **Azure Active Directory**  >  nastavení**zabezpečení**  >  **MFA**–  >  **telefonní hovor**.
1. Nastavte číslo **ID volajícího MFA** na číslo, které chcete uživatelům zobrazit na telefonu. Povolena jsou pouze čísla založená na USA.
1. Až budete připraveni, vyberte **Uložit**.

### <a name="custom-voice-messages"></a>Vlastní hlasové zprávy

Pomocí funkce vlastních hlasových zpráv můžete pro Azure Multi-Factor Authentication použít vlastní nahrávky nebo pozdravy. Tyto zprávy lze použít také k nahrazení výchozích nahrávek společnosti Microsoft.

Než začnete, mějte na paměti následující omezení:

* Podporované formáty souborů jsou *. wav* a *. mp3*.
* Limit velikosti souboru je 1 MB.
* Ověřovací zprávy by měly být kratší než 20 sekund. Zprávy, které jsou delší než 20 sekund, mohou způsobit selhání ověřování. Uživatel nemusí reagovat před dokončením zprávy a vypršením časového limitu ověřování.

### <a name="custom-message-language-behavior"></a>Vlastní chování jazyka zprávy

Když se na uživatele přehraje vlastní hlasová zpráva, jazyk zprávy závisí na následujících faktorech:

* Jazyk aktuálního uživatele.
  * Jazyk zjištěný prohlížečem uživatele.
  * Jiné scénáře ověřování se můžou chovat jinak.
* Jazyk všech dostupných vlastních zpráv.
  * Tento jazyk volí správce při přidání vlastní zprávy.

Pokud je například jenom jedna vlastní zpráva, má jazyk němčina:

* Uživatel, který se ověří v německém jazyce, si přečte vlastní Německoovou zprávu.
* Uživatel, který se ověřuje v angličtině, uslyší standardní anglickou zprávu.

### <a name="custom-voice-message-defaults"></a>Nastavení vlastních hlasových zpráv

Pomocí následujících ukázkových skriptů můžete vytvořit vlastní zprávy. Tyto fráze jsou výchozími hodnotami, pokud nekonfigurujete vlastní zprávy:

| Název zprávy | Skript |
| --- | --- |
| Ověřování bylo úspěšné. | Vaše přihlášení bylo úspěšně ověřeno. Nashledanou. |
| Výzva k rozšíření | Děkujeme, že používáte ověřovací systém přihlašování od Microsoftu. Pokračujte stisknutím klávesy libry. |
| Potvrzení podvodu | Bylo odesláno upozornění na podvod. Pokud chcete odblokovat svůj účet, obraťte se na helpdesk IT oddělení vaší společnosti. |
| Pozdrav při podvodu (Standard) | Děkujeme, že používáte ověřovací systém přihlašování od Microsoftu. Potvrďte ověření stisknutím křížku klávesy. Pokud jste toto ověřování nezahájili, může se někdo pokusit o přístup k vašemu účtu. Pokud chcete odeslat výstrahu o podvodech, stiskněte prosím nulový křížek. Tím se upozorní tým IT vaší společnosti a zablokují se další pokusy o ověření. |
| Podvod ohlásil, že byla odeslána výstraha o podvodech. | Pokud chcete odblokovat svůj účet, obraťte se na helpdesk IT oddělení vaší společnosti. |
| Aktivace | Děkujeme, že používáte ověřovací systém přihlašování od Microsoftu. Potvrďte ověření stisknutím křížku klávesy. |
| Ověřování se zamítlo znovu | Ověření bylo odepřeno. |
| Opakovat (Standard) | Děkujeme, že používáte ověřovací systém přihlašování od Microsoftu. Potvrďte ověření stisknutím křížku klávesy. |
| Pozdrav (Standard) | Děkujeme, že používáte ověřovací systém přihlašování od Microsoftu. Potvrďte ověření stisknutím křížku klávesy. |
| Pozdrav (PIN) | Děkujeme, že používáte ověřovací systém přihlašování od Microsoftu. Zadejte prosím PIN kód následovaný křížkem a dokončete ověření. |
| Pozdrav při podvodu (PIN) | Děkujeme, že používáte ověřovací systém přihlašování od Microsoftu.  Zadejte prosím PIN kód následovaný křížkem a dokončete ověření. Pokud jste toto ověřování nezahájili, může se někdo pokusit o přístup k vašemu účtu. Pokud chcete odeslat výstrahu o podvodech, stiskněte prosím nulový křížek. Tím se upozorní tým IT vaší společnosti a zablokují se další pokusy o ověření. |
| Opakovat (PIN) | Děkujeme, že používáte ověřovací systém přihlašování od Microsoftu. Zadejte prosím PIN kód následovaný křížkem a dokončete ověření. |
| Výzva k zadání rozšíření za číslicemi | Pokud už v tomto rozšíření máte, pokračujte stisknutím křížku. |
| Ověřování odepřeno | Je nám líto, ale teď vás nemůžeme přihlásit. Zkuste to později. |
| Pozdrav při aktivaci (Standard) | Děkujeme, že používáte ověřovací systém přihlašování od Microsoftu. Potvrďte ověření stisknutím křížku klávesy. |
| Opakovaný pokus o aktivaci (Standard) | Děkujeme, že používáte ověřovací systém přihlašování od Microsoftu. Potvrďte ověření stisknutím křížku klávesy. |
| Pozdrav při aktivaci (PIN) | Děkujeme, že používáte ověřovací systém přihlašování od Microsoftu. Zadejte prosím PIN kód následovaný křížkem a dokončete ověření. |
| Výzva k rozšíření před číslicemi | Děkujeme, že používáte ověřovací systém přihlašování od Microsoftu. Převeďte prosím toto volání na rozšíření... |

### <a name="set-up-a-custom-message"></a>Nastavení vlastní zprávy

Chcete-li použít vlastní zprávy, proveďte následující kroky:

1. Přejděte na **Azure Active Directory**  >  nastavení**zabezpečení**  >  **MFA**–  >  **telefonní hovor**.
1. Vyberte **Přidat pozdrav**.
1. Vyberte **typ** pozdravu, jako je například *pozdrav (Standard)* nebo  *ověřování proběhlo úspěšně*.
1. Vyberte **jazyk**založený na předchozí části o [chování vlastního jazyka zprávy](#custom-message-language-behavior).
1. Vyhledejte a vyberte zvukový soubor *. mp3* nebo *. wav* , který chcete nahrát.
1. Až budete připraveni, vyberte **Přidat**a pak **Uložit**.

## <a name="mfa-service-settings"></a>Nastavení služby MFA

Nastavení pro hesla aplikací, důvěryhodné IP adresy, možnosti ověřování a zapamatování služby Multi-Factor Authentication pro Azure Multi-Factor Authentication najdete v nastavení služby. Toto je více než u starší verze portálu a není součástí běžného portálu Azure AD.

K nastavení služby se dá získat pøístup z Azure Portal tak, že přejdete na **Azure Active Directory**  >  **zabezpečení**  >  **MFA**  >  **Začínáme**  >  **Konfigurovat**  >  **Další cloudová nastavení MFA**. Otevře se nové okno nebo karta s dalšími možnostmi *nastavení služby* .

## <a name="trusted-ips"></a>Důvěryhodné IP adresy

Funkce _důvěryhodných IP adres_ v Azure Multi-Factor Authentication obchází výzvy služby Multi-Factor Authentication pro uživatele, kteří se přihlásí z definovaného rozsahu IP adres. Můžete nastavit rozsahy důvěryhodných IP adres pro vaše místní prostředí, když se uživatelé nacházejí v jednom z těchto umístění, takže se nezobrazuje žádná Multi-Factor Authenticationa Azure.

> [!NOTE]
> Důvěryhodné IP adresy můžou zahrnovat rozsahy privátních IP adres jenom v případě, že použijete MFA Server. Pro cloudové Multi-Factor Authentication Azure můžete použít jenom rozsahy veřejných IP adres.
>
> Rozsahy IPv6 se podporují jenom v rozhraní [pojmenované umístění (Preview)](../conditional-access/location-condition.md#preview-features) .

Pokud vaše organizace nasadí rozšíření serveru NPS za účelem poskytování MFA pro místní aplikace, Všimněte si, že zdrojová IP adresa se vždy jeví jako server NPS, se kterým se snaží ověřování natékat.

| Typ tenanta Azure AD | Možnosti funkcí důvěryhodné IP adresy |
|:--- |:--- |
| Spravované |**Konkrétní rozsah IP adres**: Správci URČUJÍ rozsah IP adres, které mohou obejít službu Multi-Factor Authentication pro uživatele, kteří se přihlásí z intranetu společnosti. Lze nakonfigurovat maximálně 50 důvěryhodných rozsahů IP adres.|
| Federovaní |**Všichni federované uživatelé**: všichni federované uživatelé, kteří se přihlásí z organizace, můžou obejít službu Multi-Factor Authentication. Uživatel obejít ověřování pomocí deklarace identity, která je vydaná Active Directory Federation Services (AD FS) (AD FS).<br/>**Konkrétní rozsah IP adres**: Správci URČUJÍ rozsah IP adres, které mohou obejít službu Multi-Factor Authentication pro uživatele, kteří se přihlásí z intranetu společnosti. |

Důvěryhodná IP adresa funguje jenom v intranetu společnosti. Pokud vyberete možnost **všechny federované uživatele** a uživatel se přihlásí mimo intranet společnosti, musí se uživatel ověřit pomocí služby Multi-Factor Authentication. Proces je stejný i v případě, že uživatel prezentuje AD FS deklarací identity.

### <a name="end-user-experience-inside-of-corpnet"></a>Činnost koncového uživatele v rámci Corpnet

Pokud je funkce důvěryhodných IP adres zakázaná, je pro toky v prohlížeči vyžadováno Multi-Factor Authentication. Pro starší verze klientských aplikací jsou vyžadovány hesla aplikací.

Při použití důvěryhodných IP adres se Multi-Factor Authentication nevyžaduje pro toky v prohlížeči. Pro starší verze klientských aplikací nejsou hesla aplikací vyžadována, pokud uživatel nevytvořil heslo aplikace. Po použití hesla aplikace zůstane heslo povinné.

### <a name="end-user-experience-outside-corpnet"></a>Činnost koncového uživatele mimo Corpnet

Bez ohledu na to, jestli jsou definované důvěryhodné IP adresy, se pro toky v prohlížeči vyžaduje vícefaktorové ověřování. Pro starší verze klientských aplikací jsou vyžadovány hesla aplikací.

### <a name="enable-named-locations-by-using-conditional-access"></a>Povolit pojmenovaná umístění pomocí podmíněného přístupu

Pravidla podmíněného přístupu můžete použít k definování pojmenovaných umístění pomocí následujících kroků:

1. V Azure Portal vyhledejte a vyberte **Azure Active Directory**a pak přejděte na **zabezpečení**  >  **podmíněný přístup**  >  **s názvem umístění**.
1. Vyberte **nové umístění**.
1. Zadejte název umístění.
1. Vyberte **Označit jako důvěryhodné umístění**.
1. Zadejte rozsah IP adres v notaci CIDR pro vaše prostředí, například *40.77.182.32/27*.
1. Vyberte **Vytvořit**.

### <a name="enable-the-trusted-ips-feature-by-using-conditional-access"></a>Povolení funkce důvěryhodných IP adres pomocí podmíněného přístupu

Pokud chcete povolit důvěryhodné IP adresy pomocí zásad podmíněného přístupu, proveďte následující kroky:

1. V Azure Portal vyhledejte a vyberte **Azure Active Directory**a pak přejděte na **zabezpečení**  >   **podmíněný přístup**  >  **s názvem umístění**.
1. Vyberte **Konfigurovat důvěryhodné IP adresy MFA**.
1. Na stránce **nastavení služby** v části **důvěryhodné IP adresy**vyberte některou z následujících dvou možností:

   * **Pro žádosti od federovaných uživatelů pocházejících z mého intranetu**: Pokud chcete tuto možnost vybrat, zaškrtněte políčko. Všem federovaným uživatelům, kteří se přihlásí z podnikové sítě, obejít ověřování Multi-Factor Authentication pomocí deklarace identity, která je vydaná AD FS. Ujistěte se, že AD FS má pravidlo pro přidání deklarace identity intranetu do příslušného provozu. Pokud pravidlo neexistuje, vytvořte v AD FS následující pravidlo:

      `c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);`

   * **Pro požadavky z konkrétního rozsahu veřejných IP**adres: Pokud chcete zvolit tuto možnost, zadejte IP adresy do textového pole pomocí zápisu CIDR.
      * Pro IP adresy, které jsou v rozsahu xxx. xxx. xxx. 1 až XXX. xxx. xxx. 254, použijte notaci jako **xxx. xxx. xxx. 0/24**.
      * Pro jednu IP adresu použijte Notation, jako je **xxx.xxx.xxx.xxx/32**.
      * Zadejte až 50 rozsahů IP adres. Uživatelé, kteří se přihlásí z těchto IP adres, obejít službu Multi-Factor Authentication.

1. Vyberte **Uložit**.

### <a name="enable-the-trusted-ips-feature-by-using-service-settings"></a>Povolení funkce důvěryhodných IP adres pomocí nastavení služby

Pokud nechcete používat zásady podmíněného přístupu k povolení důvěryhodných IP adres, můžete nakonfigurovat *nastavení služby* pro Azure Multi-Factor Authentication pomocí následujících kroků:

1. V Azure Portal vyhledejte a vyberte **Azure Active Directory**a pak zvolte **Uživatelé**.
1. Vyberte **Multi-Factor Authentication**.
1. V části Multi-Factor Authentication vyberte **nastavení služby**.
1. Na stránce **nastavení služby** v části **důvěryhodné IP adresy**vyberte jednu (nebo obě) z následujících dvou možností:

   * **Pro žádosti od federovaných uživatelů v mém intranetu**: Pokud chcete vybrat tuto možnost, zaškrtněte políčko. Všem federovaným uživatelům, kteří se přihlásí z podnikové sítě, obejít ověřování Multi-Factor Authentication pomocí deklarace identity, která je vydaná AD FS. Ujistěte se, že AD FS má pravidlo pro přidání deklarace identity intranetu do příslušného provozu. Pokud pravidlo neexistuje, vytvořte v AD FS následující pravidlo:

      `c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);`

   * **Pro požadavky ze zadaného rozsahu podsítí IP adres**: Chcete-li zvolit tuto možnost, zadejte IP adresy do textového pole pomocí zápisu CIDR.
      * Pro IP adresy, které jsou v rozsahu xxx. xxx. xxx. 1 až XXX. xxx. xxx. 254, použijte notaci jako **xxx. xxx. xxx. 0/24**.
      * Pro jednu IP adresu použijte Notation, jako je **xxx.xxx.xxx.xxx/32**.
      * Zadejte až 50 rozsahů IP adres. Uživatelé, kteří se přihlásí z těchto IP adres, obejít službu Multi-Factor Authentication.

1. Vyberte **Uložit**.

## <a name="verification-methods"></a>Metody ověřování

Na portálu nastavení služby můžete zvolit metody ověřování, které jsou k dispozici pro uživatele. Když uživatelé zaregistrují své účty pro Azure Multi-Factor Authentication, zvolí si upřednostňovanou metodu ověření z možností, které jste povolili. Pokyny pro proces registrace uživatele najdete v části [Nastavení účtu pro službu Multi-Factor Authentication](../user-help/multi-factor-authentication-end-user-first-time.md).

K dispozici jsou následující metody ověřování:

| Metoda | Popis |
|:--- |:--- |
| Zavolat na telefon |Místo automatizovaného hlasového hovoru. Uživatel přijme hovor a stiskem tlačítka # na klávesnici telefonu provede ověření. Telefonní číslo není synchronizované s místní službou Active Directory. |
| Textová zpráva na telefon |Pošle textovou zprávu obsahující ověřovací kód. Uživateli se zobrazí výzva k zadání ověřovacího kódu do přihlašovacího rozhraní. Tento proces se nazývá jednosměrný server SMS. Dvoucestné SMS znamená, že uživatel musí zpětně vytvořit text konkrétního kódu. Obousměrná zpráva SMS je zastaralá a není podporovaná ani po 14. listopadu 2018. Správci by měli povolit jinou metodu pro uživatele, kteří dříve používali oboustranný SMS.|
| Oznámení prostřednictvím mobilní aplikace |Odešle nabízené oznámení na telefon nebo registrované zařízení. Uživatel zobrazí oznámení a vybere **ověření** pro dokončení ověření. Aplikace Microsoft Authenticator je dostupná pro [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6), [Android](https://go.microsoft.com/fwlink/?Linkid=825072)a [iOS](https://go.microsoft.com/fwlink/?Linkid=825073). |
| Ověřovací kód z mobilní aplikace nebo hardwarového tokenu |Aplikace Microsoft Authenticator generuje nový ověřovací kód OATH každých 30 sekund. Uživatel zadá ověřovací kód do přihlašovacího rozhraní. Aplikace Microsoft Authenticator je dostupná pro [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6), [Android](https://go.microsoft.com/fwlink/?Linkid=825072)a [iOS](https://go.microsoft.com/fwlink/?Linkid=825073). |

Další informace najdete v tématu [dostupné metody ověřování a ověřování ve službě Azure AD?](concept-authentication-methods.md)

### <a name="enable-and-disable-verification-methods"></a>Povolit a zakázat metody ověřování

Pokud chcete povolit nebo zakázat metody ověřování, proveďte následující kroky:

1. V Azure Portal vyhledejte a vyberte **Azure Active Directory**a pak zvolte **Uživatelé**.
1. Vyberte **Multi-Factor Authentication**.
1. V části Multi-Factor Authentication vyberte **nastavení služby**.
1. Na stránce **nastavení služby** v části **Možnosti ověřování**vyberte nebo zrušte výběr metod, které chcete uživatelům poskytnout.
1. Klikněte na **Uložit**.

## <a name="remember-multi-factor-authentication"></a>Zapamatovat Multi-Factor Authentication

Funkce _Zapamatovat Multi-Factor Authentication_ umožňuje uživatelům obejít následná ověření po dobu určitého počtu dnů po úspěšném přihlášení k zařízení pomocí Multi-Factor Authentication. Pro zvýšení použitelnosti a minimalizaci počtu pokusů, kolikrát musí uživatel provést MFA na stejném zařízení, vyberte dobu trvání 90 dní nebo více.

> [!IMPORTANT]
> Pokud dojde k ohrožení bezpečnosti účtu nebo zařízení, může to mít vliv na zapamatování Multi-Factor Authentication důvěryhodných zařízení. Pokud dojde k ohrožení bezpečnosti podnikového účtu nebo dojde ke ztrátě nebo odcizení důvěryhodného zařízení, měli byste [odvolat relace MFA](howto-mfa-userdevicesettings.md).
>
> Akce obnovit odvolá důvěryhodný stav ze všech zařízení a uživatel je nutný k opětovnému provedení služby Multi-Factor Authentication. Můžete taky dát uživatelům pokyn, aby obnovili Multi-Factor Authentication na svých vlastních zařízeních, jak je uvedeno v [možnosti Správa nastavení pro vícefaktorové ověřování](../user-help/multi-factor-authentication-end-user-manage-settings.md#turn-on-two-factor-verification-prompts-on-a-trusted-device).

### <a name="how-the-feature-works"></a>Jak funkce funguje

Funkce zapamatovat Multi-Factor Authentication nastaví trvalý soubor cookie v prohlížeči, když uživatel vybere možnost po **X dnech znovu se neptat** při přihlášení. Uživatel není znovu vyzván k Multi-Factor Authentication z tohoto stejného prohlížeče, dokud neskončí platnost souboru cookie. Pokud uživatel otevře jiný prohlížeč na stejném zařízení nebo vymaže soubory cookie, zobrazí se jim výzva k ověření.

Možnost **příště nedotazování na X dní** se nezobrazí v neprohlížečových aplikacích bez ohledu na to, jestli aplikace podporuje moderní ověřování. Tyto aplikace používají _aktualizační tokeny_ , které každou hodinu poskytují nové přístupové tokeny. Při ověření obnovovacího tokenu Azure AD zkontroluje, jestli se poslední služba Multi-Factor Authentication stala během zadaného počtu dnů.

Tato funkce snižuje počet ověřování ve webových aplikacích, které se obvykle zobrazují při každém dotazu. Tato funkce může zvýšit počet ověřování pro klienty moderního ověřování, kteří se obvykle dotazují každých 90 dní, pokud je nakonfigurovaná nižší doba trvání. Může také zvýšit počet ověřování v kombinaci se zásadami podmíněného přístupu.

> [!IMPORTANT]
> Funkce **Zapamatovat Multi-Factor Authentication** není kompatibilní s funkcí **zůstat přihlášenou** AD FS, když uživatelé provedou vícefaktorové ověřování pro AD FS prostřednictvím Azure Multi-Factor Authentication Server nebo řešení Multi-Factor Authentication jiného výrobce.
>
> Pokud uživatelé vyberou možnost **zůstat přihlášeni** na AD FS a také označí své zařízení jako důvěryhodné pro Multi-Factor Authentication, uživatel nebude automaticky ověřený po vypršení časového **limitu zapamatování služby Multi-Factor Authentication** . Azure AD vyžaduje nové služby Multi-Factor Authentication, ale AD FS vrátí token s původní Multi-Factor Authentication deklarací a datem, místo aby se služba Multi-Factor Authentication znovu prováděla. **Tato reakce nastavuje smyčku ověřování mezi Azure AD a AD FS.**
>
> Funkce **pamatovat Multi-Factor Authentication** není kompatibilní s uživateli B2B a při přihlašování k pozváným klientům se nebude zobrazovat pro uživatele B2B.
>

### <a name="enable-remember-multi-factor-authentication"></a>Povolit zapamatovat Multi-Factor Authentication

Pokud chcete povolit a nakonfigurovat možnost pro uživatele, aby si zapamatovali stav MFA a výzvy pro obejití, proveďte následující kroky:

1. V Azure Portal vyhledejte a vyberte **Azure Active Directory**a pak zvolte **Uživatelé**.
1. Vyberte **Multi-Factor Authentication**.
1. V části Multi-Factor Authentication vyberte **nastavení služby**.
1. Na stránce **nastavení služby** v části **zapamatování vícefaktorového ověřování**vyberte možnost **dovolit uživatelům zapamatovat vícefaktorové ověřování u zařízení, která důvěřují** .
1. Nastavte počet dní, po které mají důvěryhodná zařízení obejít službu Multi-Factor Authentication. Pro optimální uživatelské prostředí prodlužte dobu trvání až *90* nebo více dní.
1. Vyberte **Uložit**.

### <a name="mark-a-device-as-trusted"></a>Označení zařízení jako důvěryhodného

Po povolení funkce zapamatovat Multi-Factor Authentication uživatelé můžou zařízení po přihlášení označit jako důvěryhodné, a to tak, že si vyberete možnost dotaz **už**nezobrazovat.

## <a name="next-steps"></a>Další kroky

Další informace o dostupných metodách pro použití v Azure Multi-Factor Authentication najdete v tématu [Jaké metody ověřování a ověřování jsou k dispozici v Azure Active Directory?](concept-authentication-methods.md)
