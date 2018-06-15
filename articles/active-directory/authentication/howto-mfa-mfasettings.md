---
title: Konfigurace Azure Multi-Factor Authentication | Microsoft Docs
description: Tento článek popisuje, jak konfigurovat nastavení ověřování Azure Multi-Factor Authentication na portálu Azure
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 01/03/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.openlocfilehash: 509df5274a5e44342390036511689fa8f8146807
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
ms.locfileid: "33869797"
---
# <a name="configure-azure-multi-factor-authentication-settings"></a>Konfigurovat nastavení ověřování Azure Multi-Factor Authentication

Tento článek pomáhá spravovat Azure Multi-Factor Authentication teď, když jste spuštěný a funkční. Vysvětluje různé témata, které umožňují využívat naplno Azure Multi-Factor Authentication. Ne všechny funkce jsou dostupné v každé [verzi Azure Multi-Factor Authentication](concept-mfa-whichversion.md#what-features-do-i-need).

| Funkce | Popis | 
|:--- |:--- |
| [Blokovat nebo odblokovat uživatele](#block-and-unblock-users) |Použijte funkci blokování nebo odblokování uživatelů uživatelům zabránit v přijetí žádosti o ověření. |
| [Upozornění na podvod](#fraud-alert) |Nakonfigurujte funkci upozornění na podvod tak, aby vaši uživatelé mohou zasílat zprávy podvodné pokouší získat přístup k jejich prostředky. |
| [Jednorázové přihlášení](#one-time-bypass) |Použít funkci jednorázové přihlášení umožňuje uživatelům jednorázově podle ověřit _obcházení_ služby Multi-Factor Authentication. |
| [Vlastní hlasové zprávy](#custom-voice-messages) |Použijte funkci vlastní hlasové zprávy pro používání služby Multi-Factor Authentication vlastní záznamy nebo pozdrav. |
| [Ukládání do mezipaměti](#caching-in-azure-multi-factor-authentication) |Nastavte na určité časové období, aby automaticky úspěšné následné pokusy o ověření použijte funkci ukládání do mezipaměti. |
| [Důvěryhodné IP adresy](#trusted-ips) |Správci spravované nebo federované klienta můžete použít funkci důvěryhodné IP adresy obejít dvoustupňové ověření pro uživatele, kteří se přihlašují z firemní intranet. |
| [Hesla aplikací](#app-passwords) |Použijte funkci heslo aplikace povolení aplikace obejít ověřování Multi-Factor Authentication a pokračovat v práci. |
| [Zapamatovat Vícefaktorové ověřování pro důvěryhodná zařízení a prohlížeče](#remember-multi-factor-authentication-for-trusted-devices) |Pomocí této funkce pamatovat důvěryhodné zařízení a prohlížeče pro sadu počet dnů po má uživatel úspěšně přihlášené pomocí služby Multi-Factor Authentication. |
| [Volitelný ověření metody](#selectable-verification-methods) |Tato funkce slouží k výběru seznamu metod ověřování, které mohou uživatelé používat. |

## <a name="block-and-unblock-users"></a>Blokovat nebo odblokovat uživatele

Použití _blokovat a odblokovat uživatele_ funkci tak, aby uživatelé nedostávali žádosti o ověření. Jakékoli pokusy o ověření pro blokované uživatele jsou automaticky odepírány. Uživatelé zůstat blokované 90 dnů od doby, která jsou blokovány.

### <a name="block-a-user"></a>Zablokovat uživatele

1. Přihlaste se na webu [Azure Portal](https://portal.azure.com) jako správce.
2. Přejděte do **Azure Active Directory** > **MFA Server** > **zablokovat nebo odblokovat uživatele**.
3. Vyberte **přidat** zablokujete určitému uživateli.
4. Vyberte **replikační skupiny**. Zadejte uživatelské jméno pro blokované uživatele, který **uživatelské jméno<span></span>@domain.com**. Zadejte komentář do **důvod** pole.
5. Vyberte **přidat** ukončíte blokování uživatele.

### <a name="unblock-a-user"></a>Odblokovat uživatele

1. Přihlaste se na webu [Azure Portal](https://portal.azure.com) jako správce.
2. Přejděte do **Azure Active Directory** > **MFA Server** > **zablokovat nebo odblokovat uživatele**.
3. Vyberte **Odblokovat** v **akce** sloupce vedle uživatele odblokujete.
4. Zadejte komentář do **důvod pro odblokování** pole.
5. Vyberte **Odblokovat** ukončíte odblokování uživatele.

## <a name="fraud-alert"></a>Výstraha podvodů

Konfigurace _upozornění na podvod_ funkci tak, aby vaši uživatelé mohou zasílat zprávy podvodné pokouší získat přístup k jejich prostředky. Uživatelé mohou zasílat zprávy podvod pokusy pomocí mobilní aplikace nebo prostřednictvím svůj telefon.

### <a name="turn-on-fraud-alerts"></a>Zapnout upozornění na podvod

1. Přihlaste se na webu [Azure Portal](https://portal.azure.com) jako správce.
2. Přejděte do **Azure Active Directory** > **MFA Server** > **upozornění na podvod**.

   ![Zapnout upozornění na podvod](./media/howto-mfa-mfasettings/fraudalert.png)

3. Nastavte **povolit uživatelům odesílat upozornění na podvod** nastavení **na**.
4. Vyberte **Uložit**.

### <a name="configuration-options"></a>Možnosti konfigurace

- **Blokování uživatele při nahlášení podvodu**: Pokud uživatel sestavy podvod, jejich účet je blokován 90 dní nebo dokud správce odblokuje svého účtu. Správce může zkontrolovat přihlášení pomocí sestavy přihlášení a proveďte příslušné akce, aby se zabránilo budoucí podvodů. Správce pak může [Odblokovat](#unblock-a-user) uživatelského účtu.
- **Kód pro nahlášení podvodu při úvodním pozdravu**: když uživatelé obdrží telefonní hovor provést dvoustupňové ověřování, se za normálních okolností stiskněte **#** potvrďte jejich přihlášení. Pro nahlášení podvodu uživatel zadá kód před stiskněte **#**. Tento kód je **0** ve výchozím nastavení, ale můžete jej upravit.

  >[!NOTE]
  >Výchozí hlasový pozdrav od Microsoftu vyzvat uživatele, aby stiskněte **0#** odeslat upozornění na možný podvod. Pokud chcete použít jiné než kód **0**, zaznamenejte a odeslat vlastní vlastní hlasový pozdrav příslušné pokyny pro uživatele.
  >

### <a name="view-fraud-reports"></a>Zobrazit sestavy podvod

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vyberte **Azure Active Directory** > **přihlášení**. Sestava podvod je nyní součástí standardních sestav Azure AD přihlášení.

## <a name="one-time-bypass"></a>Jednorázové přihlášení

_Jednorázové přihlášení_ funkce umožňuje uživatelům jednorázově ověřit bez provedení dvoustupňové ověřování. Toto jednorázové přihlášení je dočasné a vyprší po zadaném počtu sekund. V situacích, kde mobilní aplikaci nebo phone nepřijímá oznámení nebo telefonní hovor můžete povolit jednorázové přihlášení, má uživatel přístup požadovaný prostředek.

### <a name="create-a-one-time-bypass"></a>Vytvoření jednorázové přihlášení

1. Přihlaste se na webu [Azure Portal](https://portal.azure.com) jako správce.
2. Přejděte do **Azure Active Directory** > **MFA Server** > **jednorázové přihlášení**.

   ![Vytvoření jednorázové přihlášení](./media/howto-mfa-mfasettings/onetimebypass.png)

3. Vyberte **Přidat**.
4. V případě potřeby vyberte replikační skupiny pro toto jednorázové přihlášení.
5. Zadejte uživatelské jméno jako **uživatelské jméno<span></span>@domain.com**. Zadejte počet sekund, které by měl poslední toto jednorázové přihlášení. Zadejte důvod pro toto jednorázové přihlášení. 
6. Vyberte **Přidat**. Časový limit přestane platit okamžitě. Uživatel potřebuje k přihlášení, než vyprší platnost jednorázového přihlášení. 

### <a name="view-the-one-time-bypass-report"></a>Zobrazit sestavu jednorázové přihlášení

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Přejděte do **služby Active Directory** > **MFA Server** > **jednorázové přihlášení**.

## <a name="custom-voice-messages"></a>Vlastní hlasové zprávy
Můžete použít vlastní záznamy nebo přivítání pro dvoustupňové ověření pomocí _vlastní hlasové zprávy_ funkce. Tyto zprávy lze kromě toho na nebo nahradit záznamy Microsoft.

Než začnete, mějte na paměti následující omezení:

* Podporované formáty souborů jsou ve formátu WAV nebo MP3.
* Maximální velikost souboru je 5 MB.
* Ověřování zprávy musí být kratší než 20 sekund. Zprávy, které jsou delší než 20 sekund může způsobit selhání ověření. Uživatel nemusí odpovídat předtím, než dokončí zprávy a ověření vyprší časový limit.

### <a name="set-up-a-custom-message"></a>Nastavit vlastní zprávu

1. Přihlaste se na webu [Azure Portal](https://portal.azure.com) jako správce.
2. Přejděte do **Azure Active Directory** > **MFA Server** > **telefonní hovor nastavení**.

   ![Záznam vlastní telefonní zprávy](./media/howto-mfa-mfasettings/phonecallsettings.png)

3. Vyberte **přidat pozdravu**.
4. Vyberte typ pozdrav. Vyberte jazyk.
5. Vyberte MP3 nebo WAV zvukový soubor k odeslání.
6. Vyberte **Přidat**.

## <a name="caching-in-azure-multi-factor-authentication"></a>Ukládání do mezipaměti v Azure Multi-Factor Authentication

Můžete nastavit časové období, aby pokusy o ověření po ověření uživatele pomocí _ukládání do mezipaměti_ funkce. Následné pokusy o ověření pro uživatele v rámci zadaného časového období úspěšné automaticky. Ukládání do mezipaměti se používá hlavně při místních systémů, jako je například VPN, odesílat více požadavků na ověření při prvním požadavku stále probíhá. Tato funkce umožňuje následných žádostí úspěšné automaticky po úspěšné uživatele v průběhu prvního ověřování. 

>[!NOTE]
>Funkci ukládání do mezipaměti není určena pro použití pro přihlášení ke službě Azure Active Directory (Azure AD).

### <a name="set-up-caching"></a>Nastavení ukládání do mezipaměti 

1. Přihlaste se na webu [Azure Portal](https://portal.azure.com) jako správce.
2. Přejděte do **Azure Active Directory** > **MFA Server** > **ukládání do mezipaměti pravidla**.

   ![Nastavení ukládání do mezipaměti pravidla](./media/howto-mfa-mfasettings/cachingrules.png)

3. Vyberte **Přidat**.
4. Vyberte **typ mezipaměti** z rozevíracího seznamu. Zadejte maximální počet **mezipaměti sekund**. 
5. V případě potřeby vyberte typ ověřování a určete aplikace. 
6. Vyberte **Přidat**.

## <a name="trusted-ips"></a>Důvěryhodné IP adresy

_Důvěryhodné IP adresy_ správci spravované nebo federované klienta se používá funkce služby Azure Multi-Factor Authentication. Funkci obchází dvoustupňové ověřování pro uživatele, kteří se přihlašují z firemní intranet. Tato funkce je k dispozici plnou verzi Azure Multi-Factor Authentication a ne bezplatnou verzi pro správce. Podrobnosti o tom, jak získat plnou verzi Azure Multi-Factor Authentication najdete v tématu [Azure Multi-Factor Authentication](multi-factor-authentication.md).

Pokud vaše organizace nasazuje NPS rozšíření pro zajištění vícefaktorového ověřování pro místní aplikace Poznámka zdrojovou IP adresu se vždy zobrazí jako ověřování pokus prochází serveru NPS.

| Typ klienta Azure AD | Možnosti funkcí, které důvěryhodné IP adresy |
|:--- |:--- |
| Spravované |**Konkrétní rozsah IP adres**: správci určit rozsah IP adres, které mohou obejít dvoustupňové ověřování pro uživatele, kteří se přihlašují z firemní intranet.|
| Federovaná |**Všichni uživatelé federovaný**: všechny federované uživatele, kteří se přihlašují z uvnitř organizace mohou obejít dvoustupňové ověřování. Uživatelé obejít ověření pomocí deklarace identity, který je vydán ve službě Active Directory Federation Services (AD FS).<br/>**Konkrétní rozsah IP adres**: správci určit rozsah IP adres, které mohou obejít dvoustupňové ověřování pro uživatele, kteří se přihlašují z firemní intranet. |

Důvěryhodné IP adresy nepoužívat funguje pouze z uvnitř firemní intranet. Pokud jste vybrali **všechny federované uživatele** možnost a uživatel přihlásí z mimo firemní intranet, má uživatel k ověření pomocí dvoustupňové ověřování. Proces je stejné i v případě, že uživatel uvede deklaraci identity služby AD FS. 

**Činnost koncového uživatele v síti corpnet**

Při vypnuté funkci důvěryhodné IP adresy je dvoustupňové ověření je vyžadována pro toky prohlížeče. Hesla aplikací jsou požadovány pro starší aplikace plně funkčního klienta. 

Pokud je povolena funkce důvěryhodné IP adresy, dvoustupňové ověření je *není* požadované pro toky prohlížeče. Hesla aplikací jsou *není* vyžadované pro starší aplikace plně funkčního klienta, za předpokladu, že uživatel nebyl vytvořen heslo aplikace. Po heslo aplikace se používá, zůstane vyžaduje heslo. 

**Činnost koncového uživatele mimo corpnet**

Bez ohledu na to, jestli je povolená funkce důvěryhodné IP adresy je vyžadován pro prohlížeč toky dvoustupňové ověřování. Hesla aplikací jsou požadovány pro starší aplikace plně funkčního klienta. 

### <a name="enable-named-locations-by-using-conditional-access"></a>Povolení s názvem umístění pomocí podmíněného přístupu

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Na levé straně vyberte **Azure Active Directory** > **podmíněného přístupu** > **s názvem umístění**.
3. Vyberte **nové umístění**.
4. Zadejte název pro umístění.
5. Vyberte **označit jako důvěryhodné umístění**.
6. Zadejte rozsah IP adres v notaci CIDR, třeba **192.168.1.1/24**.
7. Vyberte **Vytvořit**.

### <a name="enable-the-trusted-ips-feature-by-using-conditional-access"></a>Povolit funkci důvěryhodné IP adresy pomocí podmíněného přístupu

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Na levé straně vyberte **Azure Active Directory** > **podmíněného přístupu** > **s názvem umístění**.
3. Vyberte **MFA konfigurovat důvěryhodné IP adresy**.
4. Na **nastavení služby** v části **důvěryhodné IP adresy**, zvolit z následujících dvou možností:
   
   * **Pro žádosti od federovaných uživatelů pocházející z mém intranetu**: Chcete-li tuto možnost zvolte, zaškrtněte políčko. Všechny federovaní uživatelé, kteří přihlášení z podnikové sítě obejít dvoustupňové ověření pomocí deklarace identity, který je vydán službou AD FS. Zajistěte, aby služba AD FS na pravidlo můžete přidat intranetu deklarace identity odpovídající provoz. Pokud pravidlo neexistuje, vytvořte ve službě AD FS následující pravidlo:<br/>

     ```
     c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);
     ```
     
   * **Pro žádosti od určitého rozsahu veřejné IP adresy**: Chcete-li tuto možnost zvolte, zadejte IP adresy do textového pole pomocí notace CIDR.
   
     * Pro IP adresy, které jsou v rozsahu xxx.xxx.xxx.1 prostřednictvím xxx.xxx.xxx.254, použijte zápis jako **xxx.xxx.xxx.0/24**.
     * Pro jednu IP adresu, použijte zápis jako **xxx<span></span>.xxx.xxx.xxx/32**.
     
     Zadejte až 50 rozsahy IP adres. Uživatelé, kteří se přihlašují z těchto IP adres nepoužívat dvoustupňové ověřování.

5. Vyberte **Uložit**.

### <a name="enable-the-trusted-ips-feature-by-using-service-settings"></a>Povolit funkci důvěryhodné IP adresy pomocí nastavení služby

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Na levé straně vyberte **Azure Active Directory** > **uživatelů a skupin** > **všichni uživatelé**.
3. Vyberte **služby Multi-Factor Authentication**.
4. V části ověřování Multi-Factor Authentication, vyberte **nastavení služby**.
5. Na **nastavení služby** v části **důvěryhodné IP adresy**, zvolit z následujících dvou možností:
   
   * **Pro žádosti od federovaných uživatelů v mém intranetu**: Chcete-li tuto možnost zvolte, zaškrtněte políčko. Všechny federovaní uživatelé, kteří přihlášení z podnikové sítě obejít dvoustupňové ověření pomocí deklarace identity, který je vydán službou AD FS. Zajistěte, aby služba AD FS na pravidlo můžete přidat intranetu deklarace identity odpovídající provoz. Pokud pravidlo neexistuje, vytvořte ve službě AD FS následující pravidlo:<br/>

     ```
     c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);
     ```

   * **Pro žádosti od podsítí zadaný rozsah IP adres**: Chcete-li tuto možnost zvolte, zadejte IP adresy do textového pole pomocí notace CIDR. 
     
     * Pro IP adresy, které jsou v rozsahu xxx.xxx.xxx.1 prostřednictvím xxx.xxx.xxx.254, použijte zápis jako **xxx.xxx.xxx.0/24**.
     * Pro jednu IP adresu, použijte zápis jako **xxx<span></span>.xxx.xxx.xxx/32**.
     
     Zadejte až 50 rozsahy IP adres. Uživatelé, kteří se přihlašují z těchto IP adres nepoužívat dvoustupňové ověřování.

6. Vyberte **Uložit**.

![Povolit důvěryhodné IP adresy pomocí nastavení služby](./media/howto-mfa-mfasettings/trustedips3.png)

## <a name="app-passwords"></a>Hesla aplikací

Některé aplikace, jako je Office 2010 nebo starší a Apple Mail, nepodporují dvoustupňové ověřování. Aplikace není nakonfigurované tak, aby přijímal druhé ověření. K používání těchto aplikací, využívat výhod _hesla aplikací_ funkce. Umožňuje aplikaci, aby obešla dvoustupňové ověřování a pokračovat v práci můžete použít heslo aplikace místo tradiční heslo.

Moderní ověřování je podporováno pro klienty, Microsoft Office 2013 a novější. Klienti Office 2013, Outlook, včetně podpory moderních ověřovacích protokolů a lze je povolit pro práci s dvoustupňové ověřování. Po povolení klienta hesla aplikací nejsou požadované pro klienta.

### <a name="considerations-about-app-passwords"></a>Důležité informace o heslech aplikací

Při používání hesel aplikací, zvažte následující důležité skutečnosti:

* Hesla aplikací jsou pouze jednou přešla na aplikaci. Uživatelé nemají k udržování přehledu o hesla nebo je zadat pokaždé, když.
* Vlastní heslo se vygeneruje automaticky a není zadané uživatelem. Automaticky vygenerované heslo je pro útočníka tak snadno uhodnout obtížnější a je bezpečnější.
* Existuje omezení 40 hesel na uživatele. 
* Aplikace, které hesla do mezipaměti a použít je v místní scénářích můžete začít se nezdařila, protože heslo aplikace není známa mimo pracovní nebo školní účet. Příklad tohoto scénáře je e-mailů Exchange, které jsou na místě, ale Archivovaná pošta se nachází v cloudu. V tomto scénáři stejné heslo nebude fungovat.
* Po povolení služby Multi-Factor Authentication na uživatelský účet hesla aplikací můžete použít s klienty nejvíce prohlížeče, jako je Outlook a Microsoft Skype pro firmy. Akce správy není možné pomocí hesla aplikací prostřednictvím neprohlížečové aplikace, jako je Windows PowerShell. Akce, které nelze provést, i když má uživatel účet správce. Ke spouštění skriptů prostředí PowerShell, vytvoření účtu služby silným heslem a nepovolíte účet pro dvoustupňové ověření.

>[!WARNING]
>Hesla aplikací nepodporují v hybridních prostředích, kde klienti komunikaci s místní a cloudové Automatická konfigurace koncových bodů. Hesla domény jsou vyžadovaný k ověření na místě. Hesla aplikací jsou vyžadovaný k ověření s cloudem.
>

### <a name="guidance-for-app-password-names"></a>Pokyny pro názvů hesel aplikací

Názvů hesel aplikací vytvořena podle zařízení, na kterém se používá. Pokud máte přenosný počítač, který má neprohlížečové aplikace, jako je Outlook, Word a Excel, vytvořit jedno heslo aplikace s názvem **přenosný počítač** pro tyto aplikace. Vytvořit další heslo aplikace s názvem **plochy** pro stejné aplikace, které běží na počítač. 

>[!NOTE]
>Doporučujeme vytvořit jednu aplikaci heslo na zařízení, nikoli heslo jednu aplikaci na aplikaci.

### <a name="federated-or-single-sign-on-app-passwords"></a>Federované nebo jeden přihlašování hesel aplikací

Azure AD podporuje federaci nebo jednotné přihlašování (SSO) s místními systému Windows Server Active Directory Domain Services (AD DS). Pokud je vaše organizace Federovaná pomocí Azure AD a používáte Azure Multi-Factor Authentication, zvažte následující body o heslech aplikací.

>[!NOTE]
>Následující body se vztahuje pouze na federované zákazníků (SSO).

* Hesla aplikací jsou ověřit pomocí Azure AD a proto obcházet federace. Federace se aktivně používá pouze v případě, že je nastavení hesla aplikací.
* Pro federované uživatele (SSO), na rozdíl od pasivního toku není kontaktovat zprostředkovatele Identity (IdP). Hesla aplikací jsou uloženy v pracovní nebo školní účet. Pokud uživatel odejde ze společnosti, informace o uživateli toků pro pracovní nebo školní účet pomocí **DirSync** v reálném čase. Zakázání/odstranění účtu může trvat až tři hodiny k synchronizaci, což může zpoždění zakázání/odstranění hesla aplikace v Azure AD.
* Místní nastavení řízení přístupu klienta není dodrženo funkce hesla aplikace.
* Bez ověřování místní protokolování nebo auditování funkce je k dispozici pro použití s funkcí hesla aplikací.
* Některé pokročilé architektury vyžaduje kombinaci přihlašovacích údajů pro dvoustupňové ověření s klienty. Tyto přihlašovací údaje může obsahovat pracovní nebo školní účet uživatelského jména a hesla a hesla aplikací. Požadavky závisí na tom, jak se provádí ověřování. Pro klienty, kteří se ověřují se na místní infrastrukturu, pracovní nebo školní účet uživatelské jméno a heslo, které jsou povinná. U klientů, kteří se ověřují se Azure AD se vyžaduje heslo aplikace.

  Předpokládejme například, že máte následující architektura:

  * Vaše místní instance služby Active Directory je sdružených se službou Azure AD.
  * Používáte Exchange online.
  * Používáte Skype pro firmy na místě.
  * Používáte Azure Multi-Factor Authentication.

  ![Používání hesel aplikací v organizaci, federované](./media/howto-mfa-mfasettings/federated.png)

  V tomto scénáři použijte následující pověření:

  * Chcete-li přihlásit na Skype pro firmy, použijte pracovní nebo školní účet uživatelské jméno a heslo.
  * Pro přístup k adresáři z klienta aplikace Outlook, která se připojuje k Exchange online, použijte heslo aplikace.

### <a name="allow-users-to-create-app-passwords"></a>Povolit uživatelům vytvoření hesel aplikací

Ve výchozím nastavení uživatelé nemůžou vytvářet hesla aplikací. Musí být povolena funkce hesla aplikace. Pokud chcete uživatelům umožnit vytvoření hesel aplikací, použijte následující postup:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Na levé straně vyberte **Azure Active Directory** > **uživatelů a skupin** > **všichni uživatelé**.
3. Vyberte **služby Multi-Factor Authentication**.
4. V části ověřování Multi-Factor Authentication, vyberte **nastavení služby**.
5. Na **nastavení služby** vyberte **povolit uživatelům vytvářet hesla aplikací pro přihlášení k neprohlížečovým aplikacím** možnost.

   ![Povolit uživatelům vytvoření hesel aplikací](./media/howto-mfa-mfasettings/trustedips3.png)

### <a name="create-app-passwords"></a>Vytvoření hesel aplikací

Uživatelé mohou vytvářet hesla aplikací během jejich počáteční registrace. Uživatel má možnost vytvořit hesla aplikací na konci procesu registrace.

Uživatelé mohou také vytvářet hesla aplikací po registraci. Hesla aplikací lze změnit pomocí nastavení v portálu Azure nebo na portálu Office 365. Další informace a podrobné pokyny pro uživatele najdete v tématu [co jsou hesla aplikací v Azure Multi-Factor Authentication?](./../../multi-factor-authentication/end-user/multi-factor-authentication-end-user-app-passwords.md)

<a name="remember-multi-factor-authentication-for-devices-that-users-trust"></a>
## <a name="remember-multi-factor-authentication-for-trusted-devices"></a>Zapamatovat Vícefaktorové ověřování pro důvěryhodná zařízení
_Zapamatovat Vícefaktorové ověřování_ funkce pro zařízení a prohlížeče, které jsou důvěryhodné uživatelem je bezplatná funkce pro všechny uživatele služby Multi-Factor Authentication. Uživatelé mohou obejít dodatečných ověřování pro zadaný počet dnů, po jejich jste úspěšně přihlášeného k zařízení pomocí služby Multi-Factor Authentication. Tato funkce vylepšuje použitelnost a minimalizovat počet, kolikrát má uživatel k provedení dvoustupňové ověřování na stejné zařízení.

>[!IMPORTANT]
>Když v ohrožení účtu nebo zařízení, nezapomeňte vícefaktorového ověřování pro důvěryhodná zařízení může ovlivnit zabezpečení. Pokud podnikové účty nebo ztráty nebo odcizení důvěryhodné zařízení, měli byste [obnovit Multi-Factor Authentication na všech zařízeních](howto-mfa-userdevicesettings.md#restore-mfa-on-all-remembered-devices-for-a-user).
>
>Akce obnovení odvolá důvěryhodný stav ze všech zařízení a uživatele je potřeba provést dvoustupňové ověření znovu. Můžete také Vyzvěte uživatele, aby-li obnovit služby Multi-Factor Authentication na jejich vlastní zařízení s pokyny uvedenými v [spravovat nastavení pro dvoustupňové ověření](./../../multi-factor-authentication/end-user/multi-factor-authentication-end-user-manage-settings.md#require-two-step-verification-again-on-a-device-youve-marked-as-trusted).
>

### <a name="how-the-feature-works"></a>Princip funkce

Funkce služby Multi-Factor Authentication zapamatovat nastaví trvalého souboru cookie v prohlížeči, když uživatel vybere **nezobrazovat dotaz dalších X dní** možnost při přihlášení. Uživatel nebude vyzván k znovu pro službu Multi-Factor Authentication z této stejný prohlížeč do vypršení platnosti souboru cookie. Pokud uživatel otevře jiný prohlížeč na stejné zařízení nebo vymaže jejich souborů cookie, budou se znovu zobrazí výzva k ověření. 

**Nezobrazovat dotaz dalších X dní** možnost není zobrazena u neprohlížečových aplikací, bez ohledu na to, jestli aplikace podporuje moderní ověřování. Tyto aplikace použít _obnovovacích tokenů_ , zadejte nové přístupové tokeny každou hodinu. Po ověření se token obnovení, Azure AD ověří, že poslední dvoustupňové ověřování došlo k chybě během zadaného počtu dnů. 

Funkci snižuje počet ověření na webové aplikace, které obvykle výzvu pokaždé, když. Funkci zvyšuje počet ověření pro moderní ověřování klientů, které obvykle výzvu každých 90 dní.

>[!IMPORTANT]
>**Zapamatovat Vícefaktorové ověřování** funkce není kompatibilní s **zůstat přihlášeni** funkce služby AD FS, když uživatelé provést dvoustupňové ověřování pro službu AD FS pomocí Azure Multi-Factor Ověřování serveru nebo řešení třetí strany služby Multi-Factor authentication.
>
>Pokud uživatelé vyberou **zůstat přihlášeni** služby AD FS a také označit jejich zařízení jako důvěryhodné pro ověřování Multi-Factor Authentication, není automaticky ověření uživatele po **zapamatovat vícefaktorové ověřování**počtu dnů. Azure AD požadavky čerstvé dvoustupňové ověření, ale služby AD FS vrátí token s původní deklarace identity služby Multi-Factor Authentication a datum, místo provedení dvoustupňové ověření znovu. Nastaví tento reakce mimo smyčku ověření mezi Azure AD a služby AD FS.
>

### <a name="enable-remember-multi-factor-authentication"></a>Povolit zapamatovat Vícefaktorové ověřování

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Na levé straně vyberte **Azure Active Directory** > **uživatelů a skupin** > **všichni uživatelé**.
3. Vyberte **služby Multi-Factor Authentication**.
4. V části ověřování Multi-Factor Authentication, vyberte **nastavení služby**.
5. Na **nastavení služby** v části **spravovat zapamatovat vícefaktorové ověřování**, vyberte **povolit uživatelům zapamatovat vícefaktorové ověřování na zařízeních důvěřují**možnost.

   ![Zapamatovat Vícefaktorové ověřování pro důvěryhodná zařízení](./media/howto-mfa-mfasettings/remember.png)

6. Nastavte počet dní, aby důvěryhodných zařízení obejít dvoustupňové ověření. Výchozí hodnota je 14 dnů.
7. Vyberte **Uložit**.

### <a name="mark-a-device-as-trusted"></a>Označit jako důvěryhodné zařízení

Když povolíte funkci vícefaktorového ověřování zapamatovat, uživatelé mohou označit zařízení jako důvěryhodné při přihlášení výběrem **dotaz již nezobrazovat**.

![Vyberte "Dotaz již nezobrazovat" pro důvěryhodná zařízení](./media/howto-mfa-mfasettings/trusted.png)

## <a name="selectable-verification-methods"></a>Volitelný ověření metody

Můžete zvolit metody ověřování, které jsou k dispozici pro vaše uživatele pomocí _metody volitelný ověření_ funkce. Následující tabulka obsahuje stručný přehled metod.

Když uživatelé zaregistrují svoje účty pro Azure Multi-Factor Authentication, vybírá jejich metoda upřednostňované ověření z možností, které jste povolili. Pokyny pro registraci uživatelů je k dispozici v [nastavit účtu pro dvoustupňové ověření](../../multi-factor-authentication/end-user/multi-factor-authentication-end-user-first-time.md).

| Metoda | Popis |
|:--- |:--- |
| Telefonní hovor |Umístí automatický hlasový hovor. Uživatel přijme hovor a stiskem tlačítka # na klávesnici telefonu provede ověření. Telefonní číslo není synchronizován do místní služby Active Directory. |
| Textová zpráva na telefon |Odešle textovou zprávu obsahující ověřovací kód. Bude uživatel vyzván k zadání ověřovacího kódu do rozhraní přihlášení. Tento proces se nazývá jednosměrné služby SMS. Obousměrné služby SMS znamená, že uživatel musí text zpět konkrétní kód. Obousměrné služby SMS je zastaralá a po 14 listopadu 2018 není podporováno. Uživatelé, kteří jsou konfigurováni pro obousměrné služby SMS se automaticky přepnout do _volání na telefon_ ověření v daném čase.|
| Oznámení přes mobilní aplikaci |Odešle nabízených oznámení do telefonu nebo zaregistrovaného zařízení. Zobrazení oznámení a vybere uživatele **ověřte** k dokončení ověření. Je k dispozici pro aplikaci Microsoft Authenticator [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072), a [iOS](http://go.microsoft.com/fwlink/?Linkid=825073). |
| Ověřovací kód z mobilní aplikace |Aplikace Microsoft Authenticator generuje každých 30 sekund nový ověřovací kód OATH. Uživatel zadá ověřovací kód do rozhraní přihlášení. Je k dispozici pro aplikaci Microsoft Authenticator [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072), a [iOS](http://go.microsoft.com/fwlink/?Linkid=825073). |

### <a name="enable-and-disable-verification-methods"></a>Povolení a zákaz metody ověření

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Na levé straně vyberte **Azure Active Directory** > **uživatelů a skupin** > **všichni uživatelé**.
3. Vyberte **služby Multi-Factor Authentication**.
4. V části ověřování Multi-Factor Authentication, vyberte **nastavení služby**.
5. Na **nastavení služby** v části **možnosti ověření**, výběrem nebo zrušením výběru metody, které se uživatelům poskytnout.

   ![Vyberte metodu ověřování](./media/howto-mfa-mfasettings/authmethods.png)

6. Klikněte na **Uložit**.
