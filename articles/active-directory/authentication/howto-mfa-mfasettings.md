---
title: Konfigurovat ověřování Azure Multi-Factor Authentication | Dokumentace Microsoftu
description: Tento článek popisuje, jak konfigurovat nastavení ověřování Azure Multi-Factor Authentication na webu Azure Portal
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 01/03/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.openlocfilehash: 1d5e7683050c5e1e4216b81e23554724f12d45f9
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/14/2018
ms.locfileid: "39054705"
---
# <a name="configure-azure-multi-factor-authentication-settings"></a>Konfigurovat nastavení ověřování Azure Multi-Factor Authentication

Tento článek vám umožní spravovat ověřování Azure Multi-Factor Authentication teď, když jste do provozu. Zahrnuje různá témata, které vám umožní využít ověřování Azure Multi-Factor Authentication na maximum. Ne všechny funkce jsou k dispozici v každé [verzi Azure Multi-Factor Authentication](concept-mfa-whichversion.md#what-features-do-i-need).

| Funkce | Popis | 
|:--- |:--- |
| [Blokování a odblokování uživatelů](#block-and-unblock-users) |Použijte funkci blokování a odblokování uživatelů uživatelům zabránit v přijetí žádosti o ověření. |
| [Upozornění na podvod](#fraud-alert) |Nakonfigurujte funkce upozornění na podvod, tak, aby vaši uživatelé můžete nahlásit podvodné pokusy o přístup ke svým prostředkům. |
| [Jednorázové přihlášení](#one-time-bypass) |Použít funkci bylo povoleno jednorázové přihlášení pro umožnění uživatelům jednorázově tak ověřit _obcházení_ ověřování službou Multi-Factor Authentication. |
| [Vlastní hlasové zprávy](#custom-voice-messages) |Pomocí funkce vlastní hlasové zprávy pomocí služby Multi-Factor Authentication si vlastní záznamy nebo greetings. |
| [Ukládání do mezipaměti](#caching-in-azure-multi-factor-authentication) |Pomocí ukládání do mezipaměti funkce lze nastavit za určité časové období, tak, aby automaticky úspěšné následné pokusy o ověření. |
| [Důvěryhodné IP adresy](#trusted-ips) |Správci tenanta spravované nebo federované můžete použít funkci důvěryhodné IP adresy obejít dvoustupňové ověřování pro uživatele, kteří přihlásit z firemní intranet. |
| [Hesla aplikací](#app-passwords) |Pomocí funkce heslo aplikace povolují obejít ověřování Multi-Factor Authentication a pokračovat v práci. |
| [Zapamatovat Multi-Factor Authentication pro důvěryhodného zařízení a prohlížeče](#remember-multi-factor-authentication-for-trusted-devices) |Pomocí této funkce lze nezapomeňte důvěryhodná zařízení a prohlížečů pro stanovený počet dnů po má uživatel úspěšně přihlášení pomocí služby Multi-Factor Authentication. |
| [Volitelné ověření metody](#selectable-verification-methods) |Tato funkce umožňuje výběrem seznamu metod ověřování, která uživatelé mají možnost používat. |

## <a name="block-and-unblock-users"></a>Blokování a odblokování uživatelů

Použití _blokovat nebo odblokovat uživatele_ funkci, která uživatelům zabránit v přijetí žádosti o ověření. Jakékoli pokusy o ověření pro blokovaného uživatele budou automaticky odepírány. Uživatelé zůstat blokované 90 dnů od doby, které jsou blokovány.

### <a name="block-a-user"></a>Zablokovat uživatele

1. Přihlaste se na webu [Azure Portal](https://portal.azure.com) jako správce.
2. Přejděte do **Azure Active Directory** > **MFA Server** > **blokování a odblokování uživatelů**.
3. Vyberte **přidat** zablokujete určitému uživateli.
4. Vyberte **replikační skupiny**. Zadejte uživatelské jméno pro blokované uživatele jako datový typ **uživatelské jméno<span></span>@domain.com**. Zadejte komentář **důvod** pole.
5. Vyberte **přidat** dokončete blokování uživatele.

### <a name="unblock-a-user"></a>Odblokovat uživatele

1. Přihlaste se na webu [Azure Portal](https://portal.azure.com) jako správce.
2. Přejděte do **Azure Active Directory** > **MFA Server** > **blokování a odblokování uživatelů**.
3. Vyberte **Odblokovat** v **akce** sloupce vedle uživatele odblokujete.
4. Zadejte komentář **důvod pro odblokování** pole.
5. Vyberte **Odblokovat** dokončete odblokování uživatele.

## <a name="fraud-alert"></a>Výstraha podvodů

Konfigurace _upozornění na podvod_ funkci tak, aby vaši uživatelé můžete nahlásit podvodné pokusy o přístup ke svým prostředkům. Uživatelé mohou zasílat podvod pokusy pomocí mobilní aplikace nebo prostřednictvím svého telefonu.

### <a name="turn-on-fraud-alerts"></a>Zapněte upozornění na podvod

1. Přihlaste se na webu [Azure Portal](https://portal.azure.com) jako správce.
2. Přejděte do **Azure Active Directory** > **MFA Server** > **upozornění na podvod**.

   ![Zapněte upozornění na podvod](./media/howto-mfa-mfasettings/fraudalert.png)

3. Nastavte **povolit uživatelům odesílat upozornění na podvod** nastavení **na**.
4. Vyberte **Uložit**.

### <a name="configuration-options"></a>Možnosti konfigurace

- **Blokování uživatele při nahlášení podvodu**: Pokud uživatel nahlásí podvod, jejich účet zablokován po dobu 90 dnů nebo dokud se odblokuje správce svého účtu. Správce můžete zkontrolovat přihlášení pomocí sestavy přihlášení a provést příslušnou akci, k prevenci podvodného budoucí. Správce pak může [Odblokovat](#unblock-a-user) uživatelského účtu.
- **Kód pro nahlášení podvodu při úvodním pozdravu**: když uživatelé dostanou telefonního hovoru dvoustupňové ověřování, jsou obvykle stiskněte **#** k potvrzení jejich přihlášení. Pro nahlášení podvodu uživatel zadá kód před stisknutím klávesy **#**. Tento kód je **0** ve výchozím nastavení, ale můžete ji přizpůsobit.

  >[!NOTE]
  >Výchozí hlasový pozdrav od Microsoftu vyzvat uživatele, aby stiskněte **0#** odesílat upozornění na možný podvod. Pokud chcete použít jiné než kód **0**, uložte a nahrajte vlastní pozdravy vlastním hlasem s odpovídající pokyny pro uživatele.
  >

### <a name="view-fraud-reports"></a>Zobrazení sestav podvod

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vyberte **Azure Active Directory** > **přihlášení**. Podvod sestava je teď součástí standardních sestav Azure AD přihlášení.

## <a name="one-time-bypass"></a>Jednorázové přihlášení

_Jednorázové přihlášení_ funkce umožňuje uživatele jednorázově ověřit bez provádějících dvoustupňové ověřování. Jednorázové přihlášení je dočasné a vyprší po zadaném počtu sekund. V situacích, kde na mobilní aplikace nebo telefon nepřijímá oznámení nebo telefonní hovor můžete povolit jednorázové přihlášení, má uživatel přístup požadovaný prostředek.

### <a name="create-a-one-time-bypass"></a>Vytvoření jednorázové přihlášení

1. Přihlaste se na webu [Azure Portal](https://portal.azure.com) jako správce.
2. Přejděte do **Azure Active Directory** > **MFA Server** > **jednorázové přihlášení**.

   ![Vytvoření jednorázové přihlášení](./media/howto-mfa-mfasettings/onetimebypass.png)

3. Vyberte **Přidat**.
4. V případě potřeby vyberte replikační skupiny pro jednorázové přihlášení.
5. Zadejte uživatelské jméno jako **uživatelské jméno<span></span>@domain.com**. Zadejte počet sekund, po které by měl trvat jednorázového přihlášení. Zadejte důvod pro jednorázové přihlášení. 
6. Vyberte **Přidat**. Časový limit začne platit okamžitě. Uživatel musí pro přihlášení, než vyprší platnost jednorázového přihlášení. 

### <a name="view-the-one-time-bypass-report"></a>Zobrazit sestavu jednorázové přihlášení

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Přejděte do **služby Active Directory** > **MFA Server** > **jednorázové přihlášení**.

## <a name="custom-voice-messages"></a>Vlastní hlasové zprávy
Můžete si vlastní záznamy nebo greetings pro dvoustupňové ověřování s _vlastní hlasové zprávy_ funkce. Tyto zprávy je možné kromě nebo nahradit záznamy Microsoft.

Než začnete, mějte na paměti následující omezení:

* Podporované formáty souborů jsou ve formátu WAV nebo MP3.
* Maximální velikost souboru je 5 MB.
* Zprávy o ověřování by měl být kratší než 20 sekund. Zprávy, které jsou delší než 20 sekund může způsobit selhání ověřování. Uživatel nemusí odpovídat předtím, než se zpráva dokončí a ověření vyprší časový limit.

### <a name="set-up-a-custom-message"></a>Nastavit vlastní zprávu

1. Přihlaste se na webu [Azure Portal](https://portal.azure.com) jako správce.
2. Přejděte do **Azure Active Directory** > **MFA Server** > **nastavení telefonních hovorů**.

   ![Záznam vlastní telefon zprávy](./media/howto-mfa-mfasettings/phonecallsettings.png)

3. Vyberte **přidat pozdrav**.
4. Zvolte typ pozdrav. Vyberte jazyk.
5. Vyberte MP3 nebo ve formátu WAV zvukový soubor k nahrání.
6. Vyberte **Přidat**.

## <a name="caching-in-azure-multi-factor-authentication"></a>Ukládání do mezipaměti v Azure Multi-Factor Authentication

Můžete nastavit časové období, aby pokusy o ověření po ověření uživatele pomocí _ukládání do mezipaměti_ funkce. Následné pokusy o ověření pro uživatele v rámci zadaného časového období úspěšné automaticky. Ukládání do mezipaměti se používá především místních systémů, jako je například VPN, odesílání více požadavků ověřování, zatímco první požadavek stále probíhá. Tato funkce umožňuje následné žádosti úspěšné automaticky, po úspěšném uživatele v průběhu prvního ověřování. 

>[!NOTE]
>Ukládání do mezipaměti funkce není určena pro použití pro přihlášení k Azure Active Directory (Azure AD).

### <a name="set-up-caching"></a>Nastavení ukládání do mezipaměti 

1. Přihlaste se na webu [Azure Portal](https://portal.azure.com) jako správce.
2. Přejděte do **Azure Active Directory** > **MFA Server** > **pravidla ukládání do mezipaměti**.

   ![Nastavení pravidel ukládání do mezipaměti](./media/howto-mfa-mfasettings/cachingrules.png)

3. Vyberte **Přidat**.
4. Vyberte **typ mezipaměti** z rozevíracího seznamu. Zadejte maximální počet **mezipaměti sekund**. 
5. V případě potřeby vyberte typ ověřování a zadat aplikaci. 
6. Vyberte **Přidat**.

## <a name="trusted-ips"></a>Důvěryhodné IP adresy

_Důvěryhodné IP adresy_ správci tenanta spravované nebo federovaných se používá funkce ověřování Azure Multi-Factor Authentication. Funkce obchází dvoustupňové ověřování pro uživatele, kteří přihlásit z firemní intranet. Tato funkce je k dispozici s plnou verzi Azure Multi-Factor Authentication a ne bezplatnou verzi pro správce. Podrobnosti o tom, jak získat plnou verzi Azure Multi-Factor Authentication najdete v tématu [ověřování Azure Multi-Factor Authentication](multi-factor-authentication.md).

Pokud vaše organizace nasadí rozšíření NPS pro zajištění vícefaktorové ověřování pro místní aplikace Poznámka zdrojové IP adresy se vždycky zobrazí se serverem NPS ověřování pokusit prochází.

| Typ tenanta Azure AD | Možnosti funkcí, které důvěryhodné IP adresy |
|:--- |:--- |
| Spravované |**Konkrétní rozsah IP adres**: správci určit rozsah IP adres, které lze obejít dvoustupňové ověřování pro uživatele, kteří přihlásit z firemní intranet.|
| Federovaná |**Všichni uživatelé federované**: všechny federovaných uživatelů, kteří přihlásit z v rámci organizace může obejít dvoustupňové ověřování. Uživatelé obejít ověření pomocí deklarací identity, který vystaví služba Active Directory Federation Services (AD FS).<br/>**Konkrétní rozsah IP adres**: správci určit rozsah IP adres, které lze obejít dvoustupňové ověřování pro uživatele, kteří přihlásit z firemní intranet. |

Důvěryhodné IP adresy se nebude používat funguje pouze uvnitř firemní intranet. Pokud vyberete **všechny federované uživatele** možnost a uživatel přihlásí z mimo firemní intranet, má uživatel k ověření pomocí dvoustupňového ověření. Proces je stejný, i v případě, uživatel předloží deklaraci identity služby AD FS. 

**Činnost koncového uživatele v síti corpnet**

Když důvěryhodné IP adresy funkce je zakázaná, je potřeba pro prohlížeč toky dvoustupňové ověřování. Hesla aplikací jsou požadovány pro starší aplikace plně funkčního klienta. 

Pokud je povolena funkce důvěryhodné IP adresy, dvoustupňové ověření je *není* vyžadované pro toky prohlížeče. Hesla aplikací jsou *není* vyžadované pro starší aplikace plně funkčního klienta, za předpokladu, že uživatel ještě nevytvořil heslo aplikace. Po heslo aplikace se používá, aby heslo zůstalo vyžaduje. 

**Činnost koncového uživatele mimo síti corpnet**

Bez ohledu na to, zda je povolena funkce důvěryhodné IP adresy je vyžadován pro prohlížeč toky dvoustupňové ověřování. Hesla aplikací jsou požadovány pro starší aplikace plně funkčního klienta. 

### <a name="enable-named-locations-by-using-conditional-access"></a>Pomocí podmíněného přístupu povolit pojmenovaná umístění

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Na levé straně vyberte **Azure Active Directory** > **podmíněného přístupu** > **pojmenovaná umístění**.
3. Vyberte **nové umístění**.
4. Zadejte název pro umístění.
5. Vyberte **označit jako důvěryhodné umístění**.
6. Zadejte rozsah IP adres v zápisu CIDR, třeba **192.168.1.1/24**.
7. Vyberte **Vytvořit**.

### <a name="enable-the-trusted-ips-feature-by-using-conditional-access"></a>Povolit funkci důvěryhodné IP adresy pomocí podmíněného přístupu

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Na levé straně vyberte **Azure Active Directory** > **podmíněného přístupu** > **pojmenovaná umístění**.
3. Vyberte **MFA nakonfigurovat důvěryhodné IP adresy**.
4. Na **nastavení služby** stránce v části **důvěryhodné IP adresy**, zvolte jednu z následujících dvou možností:
   
   * **Pro žádosti od federovaných uživatelů pocházející z mém intranetu**: Chcete-li tuto možnost zvolte, vyberte zaškrtávací políčko. Všechny federovaní uživatelé, kteří přihlašování z podnikové sítě obejít dvoustupňové ověřování s použitím deklarace identity, která je vydala službou AD FS. Zajistěte, aby služba AD FS pravidlo, které přidá deklaraci intranetu na odpovídající provoz. Pokud pravidla buď neexistuje, vytvořte ve službě AD FS následující pravidlo:<br/>

     ```
     c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);
     ```
     
   * **Pro žádosti od konkrétního rozsahu objektů veřejné IP adresy**: tuto možnost, zadejte IP adresy v textovém poli pomocí zápisu CIDR.
   
     * IP adresy, které jsou v rozsahu xxx.xxx.xxx.1 prostřednictvím xxx.xxx.xxx.254, použít zápis jako **xxx.xxx.xxx.0/24**.
     * Pro jednu IP adresu, použít zápis jako **xxx<span></span>.xxx.xxx.xxx/32**.
     
     Zadejte až 50 rozsahy IP adres. Uživatelé, kteří se z těchto IP adres obejít dvoustupňové ověřování.

5. Vyberte **Uložit**.

### <a name="enable-the-trusted-ips-feature-by-using-service-settings"></a>Povolit funkci důvěryhodné IP adresy s použitím nastavení služby

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Na levé straně vyberte **Azure Active Directory** > **uživatelů a skupin** > **všichni uživatelé**.
3. Vyberte **ověřování službou Multi-Factor Authentication**.
4. V části ověřování Multi-Factor Authentication, vyberte **nastavení služby**.
5. Na **nastavení služby** stránce v části **důvěryhodné IP adresy**, zvolte jednu z následujících dvou možností:
   
   * **Pro žádosti od federovaných uživatelů v mém intranetu**: Chcete-li tuto možnost zvolte, vyberte zaškrtávací políčko. Všechny federovaní uživatelé, kteří přihlašování z podnikové sítě obejít dvoustupňové ověřování s použitím deklarace identity, která je vydala službou AD FS. Zajistěte, aby služba AD FS pravidlo, které přidá deklaraci intranetu na odpovídající provoz. Pokud pravidla buď neexistuje, vytvořte ve službě AD FS následující pravidlo:<br/>

     ```
     c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);
     ```

   * **Pro požadavky z podsítí zadaného rozsahu IP adres**: tuto možnost, zadejte IP adresy v textovém poli pomocí zápisu CIDR. 
     
     * IP adresy, které jsou v rozsahu xxx.xxx.xxx.1 prostřednictvím xxx.xxx.xxx.254, použít zápis jako **xxx.xxx.xxx.0/24**.
     * Pro jednu IP adresu, použít zápis jako **xxx<span></span>.xxx.xxx.xxx/32**.
     
     Zadejte až 50 rozsahy IP adres. Uživatelé, kteří se z těchto IP adres obejít dvoustupňové ověřování.

6. Vyberte **Uložit**.

![Povolení důvěryhodné IP adresy s nastavení služby](./media/howto-mfa-mfasettings/trustedips3.png)

## <a name="app-passwords"></a>Hesla aplikací

Některé aplikace, jako je Office 2010 nebo starší a Apple Mail, nepodporují dvoustupňové ověřování. Aplikace nejsou nakonfigurované tak, aby přijímal druhé ověření. Pokud chcete použít tyto aplikace, využijte výhod _hesla aplikací_ funkce. Heslo aplikace místo hesla tradiční slouží k povolení aplikace obejít dvoustupňové ověřování a pokračovat v práci.

Moderní ověřování je podporováno pro klienty Microsoft Office 2013 a novější. Klientech Office 2013, včetně Outlooku, podpora moderních ověřovacích protokolů a dá se povolit pro práci s dvoustupňovým ověřováním. Po povolení klienta hesla aplikací nejsou požadovány pro klienta.

### <a name="considerations-about-app-passwords"></a>Důležité informace o heslech aplikací

Při používání hesel aplikací, zvažte následující důležité skutečnosti:

* Hesla aplikací jsou pouze jednou přešla na aplikaci. Uživatelé nemusíte udržovat přehled o hesla nebo je zadat pokaždé, když.
* Skutečné heslo je automaticky vygenerováno a uživatel jej nezadává. Automaticky vygenerované heslo nejde útočníkovi možnost uhádnout a je bezpečnější.
* Platí omezení 40 hesel na uživatele. 
* Aplikace, které hesla do mezipaměti a jejich použití v místních scénářích můžete začít se nezdařila, protože heslo aplikace není známo mimo pracovní nebo školní účet. Příkladem tohoto scénáře je e-mailů Exchange, které jsou v místním, ale Archivovaná pošta se nachází v cloudu. V tomto scénáři stejné heslo nebude fungovat.
* Po povolení služby Multi-Factor Authentication u účtu uživatele hesel aplikací je možné s nejvíce neprohlížečové klienty, jako je Outlook a Microsoft Skype pro firmy. Akce správy nelze provést s použitím hesel aplikací do neprohlížečových aplikací, jako je Windows PowerShell. Akce, které nelze provést, i v případě, že uživatel má účet správce. Spouštění skriptů prostředí PowerShell, vytvořte účet služby silným heslem a nepovolí účtu pro dvoustupňové ověřování.

>[!WARNING]
>Hesla aplikací nefungují v hybridních prostředích, kde klienti komunikovat s místním a Automatická konfigurace koncových bodů v cloudu. Domény hesla jsou nutné k ověřování místní. Hesla aplikací jsou nutné k ověření pomocí cloudu.
>

### <a name="guidance-for-app-password-names"></a>Pokyny pro názvů hesel aplikací

Názvů hesel aplikací by měly odrážet zařízení, na kterém už používá. Pokud máte přenosný počítač, který má aplikace nezaložené na prohlížeči, jako je Outlook, Word a Excel, vytvořte jedno heslo aplikace s názvem **přenosný počítač** u těchto aplikací. Vytvořit jiné heslo aplikace s názvem **Desktop** pro stejné aplikace, které běží na stolním počítači. 

>[!NOTE]
>Doporučujeme vytvořit jednu aplikaci heslo na zařízení, nikoli heslo jednu aplikaci na aplikaci.

### <a name="federated-or-single-sign-on-app-passwords"></a>Federované jednotné přihlašování aplikaci nebo hesla

Azure AD podporuje jednotné přihlašování (SSO), nebo federace s místní Windows Server Active Directory Domain Services (AD DS). Pokud vaše organizace Federovaná pomocí služby Azure AD a že používáte ověřování Azure Multi-Factor Authentication, zvažte následující body o heslech aplikací.

>[!NOTE]
>Jenom pro zákazníky federované (přihlášení SSO), platí následující body.

* Hesla aplikací, které byly ověřeny pomocí Azure AD a tedy obejít federace. Federace se aktivně používá pouze při nastavování hesla aplikace.
* U federovaných uživatelů (SSO), na rozdíl od pasivního toku není kontaktovat zprostředkovatele Identity (IdP). Hesla aplikací jsou uloženy v pracovní nebo školní účet. Pokud uživatel odejde ze společnosti, informace o uživateli vede do pracovního nebo školního účtu pomocí **DirSync** v reálném čase. Zakázání/odstranění účtu může trvat až tři hodiny pro synchronizaci, což může zpoždění zakázání/odstranění hesla aplikace ve službě Azure AD.
* Místní nastavení služby Access Control klienta nejsou dodržená funkcí hesla aplikace.
* Bez ověřování místní funkce protokolování a auditování je dostupné pro použití s funkcí hesla aplikace.
* Některé pokročilé architektury vyžaduje kombinaci přihlašovacích údajů pro dvoustupňové ověřování s klienty. Tyto přihlašovací údaje můžete zahrnout pracovní nebo školní účet uživatelské jméno a hesla a hesla aplikací. Požadavky závisí na tom, jak se provádí ověřování. Pro klienty, kteří ověřování na základě místní infrastrukturu pracovní nebo školní účet uživatelské jméno a heslo požadované. U klientů, které se ověřují ve službě Azure AD se vyžaduje heslo aplikace.

  Předpokládejme například, že máte následující architektury:

  * Vaše místní instancí Active Directory federovaný pomocí služby Azure AD.
  * Používáte Exchange online.
  * Používáte Skype pro firmy místní.
  * Používáte ověřování Azure Multi-Factor Authentication.

  ![Používání hesel aplikací ve federované organizaci](./media/howto-mfa-mfasettings/federated.png)

  V tomto scénáři použijte následující pověření:

  * Přihlásit se ke Skypu pro firmy, použijte pracovní nebo školní účet uživatelské jméno a heslo.
  * Pro přístup k adresáři v klientovi Outlooku, která se připojuje k systému Exchange online, použijte heslo aplikace.

### <a name="allow-users-to-create-app-passwords"></a>Povolit uživatelům vytvoření hesel aplikací

Ve výchozím nastavení uživatelé nemůžou vytvářet hesla aplikací. Musí být povolena funkce hesla aplikace. Chcete-li umožnit uživatelům vytváření hesel aplikací, použijte následující postup:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Na levé straně vyberte **Azure Active Directory** > **uživatelů a skupin** > **všichni uživatelé**.
3. Vyberte **ověřování službou Multi-Factor Authentication**.
4. V části ověřování Multi-Factor Authentication, vyberte **nastavení služby**.
5. Na **nastavení služby** stránky, vyberte **povolit uživatelům vytvářet hesla aplikací pro přihlášení k neprohlížečovým aplikacím** možnost.

   ![Povolit uživatelům vytvoření hesel aplikací](./media/howto-mfa-mfasettings/trustedips3.png)

### <a name="create-app-passwords"></a>Vytvoření hesel aplikací

Uživatelé můžou vytvářet hesla aplikací při jejich počáteční registraci. Uživatel nemá možnost vytvoření hesel aplikací na konci procesu registrace.

Uživatelé mohou také vytvářet hesla aplikací po registraci. Hesla aplikací lze změnit prostřednictvím nastavení na webu Azure portal nebo na portálu Office 365. Další informace a podrobné pokyny pro uživatele najdete v tématu [co jsou hesla aplikací v Azure Multi-Factor Authentication?](../user-help/multi-factor-authentication-end-user-app-passwords.md)

<a name="remember-multi-factor-authentication-for-devices-that-users-trust"></a>
## <a name="remember-multi-factor-authentication-for-trusted-devices"></a>Zapamatovat Multi-Factor Authentication pro důvěryhodného zařízení
_Zapamatovat Multi-Factor Authentication_ funkce pro zařízení a prohlížeče, které jsou důvěryhodné uživatelem je bezplatná funkce pro všechny uživatele služby Multi-Factor Authentication. Uživatelé mohou obejít následné ověření pro zadaný počet dnů, po jejich jste úspěšně nepřihlásil k zařízení pomocí služby Multi-Factor Authentication. Tato funkce vylepšuje použitelnost minimalizací počet, kolikrát má uživatel k provedení dvoustupňového ověřování na stejném zařízení.

>[!IMPORTANT]
>Pokud dojde k ohrožení účtu nebo zařízení, zapamatování ověřování službou Multi-Factor Authentication pro důvěryhodného zařízení můžou ovlivnit zabezpečení. Pokud účet organizace ohroženo nebo důvěryhodné zařízení dojde ke ztrátě nebo odcizení, měli byste [na všech zařízeních obnovit ověřování službou Multi-Factor Authentication](howto-mfa-userdevicesettings.md#restore-mfa-on-all-remembered-devices-for-a-user).
>
>Akce obnovení odvolá důvěryhodný stav ze všech zařízení a uživatel musí znovu dvoustupňové ověřování. Můžete také Vyzvěte uživatele, aby obnovit ověřování službou Multi-Factor Authentication na zařízení s pokyny uvedenými v [spravovat nastavení pro dvoustupňové ověřování](../user-help/multi-factor-authentication-end-user-manage-settings.md#require-two-step-verification-again-on-a-device-youve-marked-as-trusted).
>

### <a name="how-the-feature-works"></a>Princip funkce

Funkci zapamatovat Multi-Factor Authentication nastaví trvalého souboru cookie v prohlížeči, když uživatel vybere **nezobrazovat dotaz dalších X dní** možnost při přihlášení. Uživatel není vyzván znovu pro ověřování službou Multi-Factor Authentication ze stejného prohlížeče do vypršení platnosti souboru cookie. Když uživatel otevře jiný prohlížeč na stejné zařízení nebo vymaže jejich souborů cookie, jsou vyzváni znovu ověřit. 

**Nezobrazovat dotaz dalších X dní** možnost není zobrazena na neprohlížečové aplikace, bez ohledu na to, jestli aplikace podporuje moderní ověřování. Tyto aplikace použít _obnovovacích tokenů_ , které poskytují nové přístupové tokeny každou hodinu. Po ověření obnovovací token Azure AD ověří, že došlo k poslední dvoustupňové ověření během zadaného počtu dnů. 

Funkci snižuje počet ověření ve službě web apps, které obvykle výzvu pokaždé, když. Tato funkce zvyšuje počet ověření pro moderní ověřování klientů, které obvykle výzvu každých 90 dní.

>[!IMPORTANT]
>**Zapamatovat Multi-Factor Authentication** není kompatibilní s funkcí **neodhlašovat** funkce služby AD FS, když uživatelé provedou dvoustupňové ověřování pro službu AD FS pomocí Azure Multi-Factor Authentication Ověřování serveru nebo řešení třetí strany služby Multi-Factor authentication.
>
>Pokud vaši uživatelé možnost **neodhlašovat** na službu AD FS a také označit zařízení jako důvěryhodné pro ověřování službou Multi-Factor Authentication, se automaticky ověřit uživatele po **zapamatovat Multi-Factor authentication**dnech vyprší. Azure AD vyžaduje novou dvoustupňové ověřování, ale služby AD FS vrátí token se původní deklaraci identity ověřování službou Multi-Factor Authentication a datum, spíše než provádí dvoustupňové ověření znovu. Nastaví tuto reakci mimo smyčku ověření mezi Azure AD a AD FS.
>

### <a name="enable-remember-multi-factor-authentication"></a>Povolit zapamatovat Vícefaktorové ověřování

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Na levé straně vyberte **Azure Active Directory** > **uživatelů a skupin** > **všichni uživatelé**.
3. Vyberte **ověřování službou Multi-Factor Authentication**.
4. V části ověřování Multi-Factor Authentication, vyberte **nastavení služby**.
5. Na **nastavení služby** stránce v části **spravovat zapamatovat Multi-Factor authentication**, vyberte **povolit uživatelům zapamatovat vícefaktorové ověřování na zařízeních, kterým důvěřují**možnost.

   ![Zapamatovat Multi-Factor Authentication pro důvěryhodného zařízení](./media/howto-mfa-mfasettings/remember.png)

6. Nastavte počet dní, aby důvěryhodná zařízení obejít dvoustupňové ověřování. Výchozí hodnota je 14 dní.
7. Vyberte **Uložit**.

### <a name="mark-a-device-as-trusted"></a>Označit zařízení jako důvěryhodný

Když povolíte funkci zapamatovat Multi-Factor Authentication, uživatelé mohou označit zařízení jako důvěryhodná při přihlášení tak, že vyberete **příště se už neptat**.

![Vyberte "Dotaz už nezobrazovat" pro důvěryhodného zařízení](./media/howto-mfa-mfasettings/trusted.png)

## <a name="selectable-verification-methods"></a>Volitelné ověření metody

Můžete použít metody ověřování, které jsou k dispozici pro uživatele s použitím _volitelných ověřovací metody_ funkce. Následující tabulka obsahuje stručný přehled této metody.

Když uživatelé zaregistrují svoje účty pro ověřování Azure Multi-Factor Authentication, uživatel vybrat z možností, které jste povolili jejich upřednostňovaná metoda ověření. Pokyny k procesu registrace uživatele je k dispozici v [nastavit účtu pro dvoustupňové ověřování](../user-help/multi-factor-authentication-end-user-first-time.md).

| Metoda | Popis |
|:--- |:--- |
| Telefonní hovor |Umístí automatizovaný hlasový hovor. Uživatel přijme hovor a stiskem tlačítka # na klávesnici telefonu provede ověření. Telefonní číslo není synchronizována do místní služby Active Directory. |
| Textová zpráva na telefon |Odešle textovou zprávu obsahující ověřovací kód. Bude uživatel vyzván k zadání ověřovacího kódu do rozhraní pro přihlášení. Tento proces se nazývá jednosměrné služby SMS. Obousměrné služby SMS znamená, že uživatel musí text zpět konkrétního kódu. Obousměrné služby SMS je zastaralá a není podporována po 14. listopadu 2018. Uživatelé, kteří jsou konfigurováni pro obousměrné služby SMS se automaticky přepnout do _volání na telefon_ ověření v daném čase.|
| Oznámení přes mobilní aplikaci |Odešle nabízené oznámení do telefonu nebo zaregistrovaného zařízení. Uživatel zobrazí oznámení a vybere **ověřte** dokončete ověření. Aplikace Microsoft Authenticator je dostupná pro [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072), a [iOS](http://go.microsoft.com/fwlink/?Linkid=825073). |
| Ověřovací kód z mobilní aplikace |Aplikace Microsoft Authenticator generuje každých 30 sekund nový ověřovací kód OATH. Ověřovací kód, který uživatel zadá do rozhraní pro přihlášení. Aplikace Microsoft Authenticator je dostupná pro [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072), a [iOS](http://go.microsoft.com/fwlink/?Linkid=825073). |

### <a name="enable-and-disable-verification-methods"></a>Povolení a zákaz metody ověřování

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Na levé straně vyberte **Azure Active Directory** > **uživatelů a skupin** > **všichni uživatelé**.
3. Vyberte **ověřování službou Multi-Factor Authentication**.
4. V části ověřování Multi-Factor Authentication, vyberte **nastavení služby**.
5. Na **nastavení služby** stránce v části **možnosti ověření**, vyberte nebo zrušte výběr metody, které poskytují uživatelům.

   ![Vyberte metodu ověřování](./media/howto-mfa-mfasettings/authmethods.png)

6. Klikněte na **Uložit**.
