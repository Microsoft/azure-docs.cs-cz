---
title: 'Kurz: Konfigurace Workday pro zřizování automatické uživatelů s Azure Active Directory | Microsoft Docs'
description: Informace o konfiguraci Azure Active Directory a automaticky zřizovat a zrušte zřízení uživatelských účtů do Workday.
services: active-directory
author: asmalser-msft
documentationcenter: na
manager: mtillman
ms.assetid: 1a2c375a-1bb1-4a61-8115-5a69972c6ad6
ms.service: active-directory
ms.component: saas-app-tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/18/2018
ms.author: asmalser
ms.openlocfilehash: 262c864a9e580ab5e2ebb0d4fc1e6ec16adeacb3
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "36334322"
---
# <a name="tutorial-configure-workday-for-automatic-user-provisioning"></a>Kurz: Konfigurace Workday pro zřizování automatické uživatelů

Cílem tohoto kurzu je tak, aby zobrazovalo kroky, které je třeba provést import osoby z Workday do služby Active Directory a Azure Active Directory, s volitelné zpětný zápis některých atributů k Workday.

## <a name="overview"></a>Přehled

[Zřizování služby Azure Active Directory uživatelů](../active-directory-saas-app-provisioning.md) se integruje s [Workday lidských zdrojů API](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) aby bylo možné vytvořit uživatelské účty. Azure AD toto připojení používá povolit zřizování pracovních následujících uživatelů:

* **Zřizování uživatelů do služby Active Directory** -synchronizovat vybrané skupiny uživatelů z Workday do jednoho nebo více doménových struktur služby Active Directory.

* **Zřizování cloudové uživatelům Azure Active Directory** -hybridní uživatelů, kteří se nacházejí ve službě Active Directory a Azure Active Directory může být zřízen do druhé pomocí [AAD Connect](../connect/active-directory-aadconnect.md). Uživatelé, kteří jsou jenom pro cloud však může být zřízen přímo z Workday do Azure pomocí uživatele Azure AD zřizování služby Active Directory.

* **Zpětný zápis e-mailových adres k Workday** -uživatele Azure AD zřizování služby můžete zápisu vybrané atributy uživatele Azure AD zpátky do Workday, jako je například e-mailovou adresu.

### <a name="what-human-resources-scenarios-does-it-cover"></a>Jaké scénáře lidských zdrojů netýká?

Workday uživatele pracovní postupy zřizování podporována službou Azure AD uživatele zřizování Povolte následující lidské zdroje a scénářů správy životního cyklu identity:

* **Zařazení nového zaměstnanců** – při přidání nového zaměstnance do Workday, uživatelský účet se automaticky vytvoří v Active Directory, Azure Active Directory a volitelně Office 365 a [jiné aplikace SaaS podporovaný službou Azure AD](../active-directory-saas-app-provisioning.md), se zpětným zápisem e-mailovou adresu k Workday.

* **Zaměstnanec atribut a profil aktualizace** – když záznam se aktualizuje v Workday (například jeho název, název nebo správce), jejich uživatelské účty se automaticky aktualizuje v Active Directory, Azure Active Directory a volitelně Office 365 a [jiné aplikace SaaS podporovaný službou Azure AD](../active-directory-saas-app-provisioning.md).

* **Zaměstnanec ukončení** – Pokud zaměstnanec je ukončen v Workday, jejich uživatelský účet je automaticky zakázán v Active Directory, Azure Active Directory a volitelně Office 365 a [jiné aplikace SaaS nepodporuje v Azure AD](../active-directory-saas-app-provisioning.md).

* **Zaměstnanec znovu najímá** – Pokud zaměstnanec je rehired ve Workday, jejich starý účet můžete automaticky znovu aktivovat nebo znovu zřídit (v závislosti na vaši volbu) služby Active Directory, Azure Active Directory a volitelně Office 365 a [jiné aplikace SaaS podporovaný službou Azure AD](../active-directory-saas-app-provisioning.md).

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Kdo je tento uživatel zřizování řešení nejlépe vhodné pro?

Tento uživatel Workday zřizování řešení je v současné době ve verzi public preview a je ideální pro:

* Organizace, které požadavky předem připravené, cloudové řešení pro zřizování uživatelů Workday

* Organizace, které vyžadují zřizování přímé uživatelů z Workday do služby Active Directory nebo Azure Active Directory

* Organizace, které vyžaduje, aby uživatel zřídí pomocí dat získaných z Workday HCM modulu (viz [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html))

* Organizace, které vyžadují připojení, přesunutí, a ponechat uživatelům synchronizovat s nejméně jedné doménové struktury, domény a organizační jednotky pouze na základě změnit informace o uživateli v modulu Workday HCM (viz [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html))

* Organizace v Office 365 e-mailu

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-hybrid-note.md)]

## <a name="planning-your-solution"></a>Plánování řešení

Před zahájením svoji integraci s Workday, zkontrolujte následující požadavky a přečtěte si následující pokyny o tom, jak odpovídají aktuální architektura služby Active Directory a uživatelské požadavky zřizování s solution(s) poskytuje služba Azure Active Directory.

### <a name="prerequisites"></a>Požadavky

Scénář uvedených v tomto kurzu se předpokládá, že už máte následující položky:

* Platné předplatné Azure AD Premium P1 s přístupem globální správce
* Implementace klienta Workday pro účely testování a integrace
* Oprávnění správce v Workday můžete vytvořit uživatele systému integrace a provést změny k testování zaměstnanec data pro účely testování
* Pro zřizování uživatelů do služby Active Directory, je potřeba hostitel připojený k doméně serveru spuštěna služba Windows 2012 nebo vyšší [místní agent synchronizace](https://go.microsoft.com/fwlink/?linkid=847801)
* [Azure AD Connect](../connect/active-directory-aadconnect.md) pro synchronizaci mezi službou Active Directory a Azure AD

### <a name="solution-architecture"></a>Architektura řešení

Azure AD poskytuje bohatou sadu zřizování konektory, které vám pomůžou vyřešit zřizování a identity životní cyklus správy z Workday do služby Active Directory, Azure AD, aplikace SaaS a nad rámec. Které funkce bude používat a jak nastavit řešení se budou lišit v závislosti na prostředí a požadavky vaší organizace. Jako první krok udělejte si inventuru kolik následující jsou přítomna a nasazené ve vaší organizaci:

* Kolik doménových struktur Active Directory, jsou používány?
* Kolik domény služby Active Directory, jsou používány?
* Kolik Active Directory organizačních jednotek (OU), jsou používány?
* Kolik klientů služby Azure Active Directory, jsou používány?
* Existují uživatelé, kteří musí být zřízená služby Active Directory a Azure Active Directory (například "hybridní" uživatelů)?
* Existují uživatelé, kteří musí být zřízená Azure Active Directory, ale není Active Directory (například "jen cloudu" uživatelů)?
* E-mailové adresy uživatele potřebujete možné zpětně zapsat do Workday?

Až budete mít odpovědi na tyto otázky, můžete naplánovat vaší Workday zřizování nasazení podle pokynů níže.

#### <a name="using-provisioning-connector-apps"></a>Pomocí aplikací, zřizování konektoru

Azure Active Directory podporuje předem integrovaných zřizování konektory pro Workday a mnoho dalších aplikací SaaS.

Jeden konektor zřizování rozhraní s rozhraním API systému jednoho zdroje a pomáhá poskytování dat do jednoho cílového systému. Většina zřizování konektory, které podporuje Azure AD jsou pro jeden zdroj a cíl systému (například Azure AD ServiceNow) a můžete nastavit tak, že přidání aplikace dotyčném z galerii aplikací Azure AD (například ServiceNow).

Ve službě Azure AD je relace 1: 1 mezi zřizování konektor instancí a instance aplikace:

| Zdrojový systém | Cílový systém |
| ---------- | ---------- |
| Klientovi služby Azure AD | Aplikace SaaS |

Při práci s Workday a služby Active Directory, existuje však několik zdrojové a cílové systémy tak, aby se dalo považovat za:

| Zdrojový systém | Cílový systém | Poznámky |
| ---------- | ---------- | ---------- |
| WORKDAY | Doménová struktura Active Directory | Každé doménové struktuře je považován za odlišné cílový systém |
| WORKDAY | Klientovi služby Azure AD | Podle potřeby pro uživatele jenom pro cloud |
| Doménová struktura Active Directory | Klientovi služby Azure AD | Tento tok se zpracovává souborem AAD Connect dnes |
| Klientovi služby Azure AD | WORKDAY | Pro zpětný zápis e-mailové adresy |

Pro usnadnění těchto více pracovních postupů na několika zdrojové a cílové systémy, Azure AD poskytuje více zřizování konektor aplikace, které můžete přidat z galerii aplikací Azure AD:

![Galerie aplikací v AAD](./media/workday-inbound-tutorial/WD_Gallery.PNG)

* **WORKDAY Active Directory zřízení** -tuto aplikaci usnadňuje účet zřizování uživatelů z Workday do jedné doménové struktury služby Active Directory. Pokud máte více doménových struktur, můžete přidat z galerii aplikací Azure AD pro každou doménovou strukturu služby Active Directory, které budete muset zřídit na jednu instanci této aplikace.

* **WORKDAY do Azure AD zřizování** – AAD Connect je nástroj, který se má použít pro synchronizaci služby Active Directory uživatelům Azure Active Directory, tato aplikace lze použít k usnadnění zřizování jenom pro cloud uživatelů z Workday do jednoho Azure Klienta Active Directory.

* **Zpětný zápis WORKDAY** -tuto aplikaci usnadňuje zpětný zápis e-mailové adresy uživatele ze služby Azure Active Directory k Workday.

> [!TIP]
> Regulární "Workday" aplikace se používá pro nastavení jednotného přihlašování mezi Workday a Azure Active Directory. 

Jak připravit a nakonfigurovat tyto speciální zřizování konektor aplikace je předmětem zbývající části tohoto kurzu. Které aplikace provedete konfiguraci bude záviset na systémy, které budete muset zřízení a kolik doménové struktury služby Active Directory a Azure AD klienti používají ve vašem prostředí.

![Přehled](./media/workday-inbound-tutorial/WD_Overview.PNG)

## <a name="configure-a-system-integration-user-in-workday"></a>Konfigurace uživatele systému integrace ve Workday
Běžné požadavek všechny konektory zřizování Workday je, že se že vyžadují přihlašovací údaje pro účet Workday systému integrace se připojit k rozhraní API Workday lidských zdrojů. Tato část popisuje, jak vytvořit účet integrátor systému Workday.

> [!NOTE]
> Je možné tento postup vynechat a místo toho použít účet globálního správce Workday jako účet integrace systému. To může bez problémů fungují pro ukázky, ale nedoporučuje se používat pro nasazení v produkčním prostředí.

### <a name="create-an-integration-system-user"></a>Vytvořit uživatele s integrace systému

**Chcete-li vytvořit uživatele systému integrace:**

1. Přihlaste se pomocí účtu správce klienta Workday. V **Workday Workbench**, zadejte do vyhledávacího pole vytvořte uživatele a pak klikněte na tlačítko **vytvořit uživatele systému integrace**.

    ![Vytvořit uživatele](./media/workday-inbound-tutorial/IC750979.png "vytvořit uživatele")
2. Dokončení **vytvořit uživatele systému integrace** úlohu zadáním uživatelské jméno a heslo pro nového uživatele systému integrace.  
 * Ponechte **vyžadují nové heslo při dalším přihlášení** možnost není zaškrtnuto, protože tento uživatel bude protokolování prostřednictvím kódu programu.
 * Ponechte **minut časový limit relace** s jeho výchozí hodnotu 0, které zabrání uživatelských relací předčasně časového limitu.

    ![Vytvořte uživatele systému integrace](./media/workday-inbound-tutorial/IC750980.png "vytvořte uživatele systému integrace")

### <a name="create-a-security-group"></a>Vytvořte skupinu zabezpečení
Budete muset vytvořit skupinu zabezpečení systému integrace bez omezení a přiřaďte uživatele do ní.

**Vytvoření skupiny zabezpečení:**

1. Zadejte do vyhledávacího pole vytvořte skupinu zabezpečení a potom klikněte na **vytvořit skupinu zabezpečení**.

    ![Skupiny CreateSecurity](./media/workday-inbound-tutorial/IC750981.png "CreateSecurity skupiny")
2. Dokončení **vytvořit skupinu zabezpečení** úloh.  
3. Vyberte **skupina zabezpečení systému integrace (neomezená)** z **typ z nevyužívá dělené tabulky skupiny zabezpečení** rozevíracího seznamu.
4. Vytvořte skupinu zabezpečení, do které budou členy explicitně přidány.

    ![Skupiny CreateSecurity](./media/workday-inbound-tutorial/IC750982.png "CreateSecurity skupiny")

### <a name="assign-the-integration-system-user-to-the-security-group"></a>Přiřaďte uživatele systému integrace do skupiny zabezpečení

**Přiřazení uživatele systému integrace:**

1. Do vyhledávacího pole zadejte skupinu zabezpečení upravit a pak klikněte na **upravit skupinu zabezpečení**.

    ![Upravit skupinu zabezpečení](./media/workday-inbound-tutorial/IC750983.png "upravit skupinu zabezpečení")
1. Vyhledejte a vyberte novou skupinu zabezpečení integrace podle názvu.

    ![Upravit skupinu zabezpečení](./media/workday-inbound-tutorial/IC750984.png "upravit skupinu zabezpečení")
2. Přidání nového uživatele systému integrace do nové skupiny zabezpečení. 

    ![Skupina zabezpečení systému](./media/workday-inbound-tutorial/IC750985.png "skupina zabezpečení systému")  

### <a name="configure-security-group-options"></a>Konfigurovat možnosti pro skupiny zabezpečení
V tomto kroku budete zabezpečení domény udělte oprávnění zásad pro pracovní data do skupiny zabezpečení.

**Chcete-li nakonfigurovat možnosti pro skupiny zabezpečení:**

1. Zadejte **zásady zabezpečení domény** do pole hledání a klikněte na odkaz **zásady zabezpečení domény pro funkční oblast**.  

    ![Zásady zabezpečení domény](./media/workday-inbound-tutorial/IC750986.png "zásady zabezpečení domény")  
2. Vyhledejte systému a vyberte **systému** funkční oblast.  Klikněte na **OK**.  

    ![Zásady zabezpečení domény](./media/workday-inbound-tutorial/IC750987.png "zásady zabezpečení domény")  
3. V seznamu zásad zabezpečení pro funkční oblast systému rozbalte **zabezpečení správy** a vyberte zásady zabezpečení domény **zřizování externího účtu**.  

    ![Zásady zabezpečení domény](./media/workday-inbound-tutorial/IC750988.png "zásady zabezpečení domény")  
1. Klikněte na tlačítko **upravit oprávnění**a pak klikněte na **upravit oprávnění** stránky dialogové okno Přidat novou skupinu zabezpečení do seznamu skupin zabezpečení s **získat** a **Put**  integrace oprávnění.

    ![Upravit oprávnění](./media/workday-inbound-tutorial/IC750989.png "upravit oprávnění")  

1. Opakujte kroky 1 – 4 výše pro každou z těchto zbývající zásady zabezpečení:

| Operace | Zásady zabezpečení domény |
| ---------- | ---------- | 
| Operace GET a Put | Worker dat: Sestavy veřejné pracovního procesu |
| Operace GET a Put | Pracovní Data: Kontaktní informace o práci |
| Získat | Dat pracovníků: Všechny pozice |
| Získat | Pracovní Data: Personální informace aktuální |
| Získat | Data pracovního procesu: Název firmy na profilu pracovní |


### <a name="activate-security-policy-changes"></a>Aktivovat změny zásad zabezpečení

**Chcete-li aktivovat změny zásad zabezpečení:**

1. Zadejte do vyhledávacího pole aktivovat a potom klikněte na odkaz **aktivovat čekající změny zásad zabezpečení**.

    ![Aktivovat](./media/workday-inbound-tutorial/IC750992.png "aktivovat") 
2. Zahájit úlohu aktivovat čekající změny zásad zabezpečení tak, že zadáte komentář pro účely auditování a potom klikněte na **OK**. 

    ![Aktivovat čekající na vyřízení zabezpečení](./media/workday-inbound-tutorial/IC750993.png "aktivovat čekající na vyřízení zabezpečení")  
1. Dokončení úlohy na další obrazovce zaškrtnutím políčka **potvrdit**a potom klikněte na **OK**.

    ![Aktivovat čekající na vyřízení zabezpečení](./media/workday-inbound-tutorial/IC750994.png "aktivovat čekající na vyřízení zabezpečení")  

## <a name="configuring-user-provisioning-from-workday-to-active-directory"></a>Konfiguraci zřizování uživatelů z Workday do služby Active Directory
Postupujte podle těchto pokynů ke konfiguraci zřizování z Workday pro každou doménovou strukturu služby Active Directory, které vyžadují zřizování pro uživatelský účet.

### <a name="planning"></a>Plánování

Před konfigurací zřizování uživatelů do doménové struktury služby Active Directory, zvažte následující otázky. Odpovědi na tyto otázky určí, jak oboru filtrů a mapování atributů je nutné nastavit. 

* **Co uživatelé v Workday muset zřídit na těchto doménových strukturách služby Active Directory?**

   * *Příklad: Uživatelé, kde atribut "Společnosti" Workday hodnotu "Contoso" a "Worker_Type" atribut obsahuje "Normální"*

* **Jak se směrují uživatelů do jiné organizační jednotky (OU)?**

   * *Příklad: Uživatelé jsou směrovány do organizační jednotky, které odpovídají pobočce, jak jsou definovány v Workday "Okres" a "Country_Region_Reference" atributy*

* **Jak by měla být následující atributy vyplní ve službě Active Directory?**

   * Běžný název (cn)
      * *Příklad: Funkce User_ID Workday hodnotu, použijte jako sada podle lidských zdrojů*
      
   * ID zaměstnance (employeeId)
      * *Příklad: Použijte hodnotu Workday Worker_ID*
      
   * Název účtu SAM (sAMAccountName)
      * *Příklad: Použijte hodnotu funkce User_ID Workday, filtrovaný pomocí Azure AD zřizování výraz odebrat neplatné znaky*
      
   * Hlavní název uživatele (userPrincipalName)
      * *Příklad: Použití funkce User_ID Workday hodnotu s Azure AD zřizování výraz k zadání názvu domény*

* **Jak by uživatelé si měly odpovídat mezi Workday a služby Active Directory?**

  * *Příklad: Uživatelé s konkrétní Workday "Worker_ID" hodnoty se shodují s uživateli služby Active Directory, kde "employeeID" mají stejnou hodnotu. Pokud hodnota Worker_ID nebyl nalezen ve službě Active Directory, vytvořte nového uživatele.*
  
* **Doménové struktury služby Active Directory již obsahuje ID požadované pro odpovídající logiku pro práci uživatelů?**

  * *Příklad: Pokud je to nové nasazení Workday, důrazně doporučujeme, aby se předem vyplněny správné hodnoty Workday Worker_ID (nebo jedinečné ID hodnotu volby), aby odpovídající logiku co nejjednodušší služby Active Directory.*
    
    
### <a name="part-1-adding-the-provisioning-connector-app-and-creating-the-connection-to-workday"></a>Část 1: Přidání zřizování konektor aplikace a vytvoření připojení k Workday

**Postup konfigurace Workday zřízení služby Active Directory:**

1.  Přejděte na <https://portal.azure.com>.

2.  V levém navigačním panelu, vyberte **Azure Active Directory**

3.  Vyberte **podnikové aplikace, které**, pak **všechny aplikace**.

4.  Vyberte **přidat aplikaci**a vyberte **všechny** kategorie.

5.  Vyhledejte **Workday zřizování do služby Active Directory**a přidejte tuto aplikaci z galerie.

6.  Po přidání aplikace a obrazovce s podrobnostmi aplikace je uvedené, vyberte **zřizování**

7.  Změna **zřizování** **režimu** k **automatické**

8.  Dokončení **přihlašovací údaje správce** části následujícím způsobem:

   * **Uživatelské jméno správce** – zadejte uživatelské jméno účtu systému integrace Workday, s připojeným názvem domény klienta. **By měl vypadat podobně jako: username@contoso4**

   * **Heslo správce –** zadejte heslo účtu Workday integrace systému

   * **URL – klienta** zadejte adresu URL pro koncový bod webové služby Workday pro vašeho klienta. To by měl vypadat jako: https://wd3-impl-services1.workday.com/ccx/service/contoso4, kde contoso4 se nahradí název vašeho klienta správné a wd3 impl se nahradí řetězce správné prostředí.

   * **Doménové struktury služby Active Directory -** "Název" služby Active Directory doménové struktury, jak ho vrátila prostředí PowerShell Get-ADForest. Toto je obvykle řetězec jako: *contoso.com*

   * **Kontejner služby Active Directory -** zadejte řetězec kontejneru, který obsahuje všechny uživatele v doménové struktuře AD. Příklad: *OU = standardní uživatelé, OU = Users, DC = contoso, DC = test*

   * **E-mailové oznámení –** zadejte e-mailovou adresu a zaškrtněte políčko "odeslání e-mailu, pokud dojde k selhání".

   * Klikněte **Test připojení** tlačítko. Pokud je test připojení úspěšný, klikněte na tlačítko **Uložit** tlačítka v horní části. Pokud se nezdaří, zkontrolujte, zda přihlašovací údaje do Workday jsou platné ve Workday. 

![Azure Portal](./media/workday-inbound-tutorial/WD_1.PNG)

### <a name="part-2-configure-attribute-mappings"></a>Část 2: Konfigurace mapování atributů 

V této části nakonfigurujete, jak jsou data uživatele z Workday do služby Active Directory.

1.  Na kartě zřizování pod **mapování**, klikněte na tlačítko **synchronizovat pracovníkům Workday OnPremises**.

2.  V **zdrojového objektu oboru** pole, můžete vybrat, které skupiny uživatelů v Workday by měla být v oboru pro zřizování do služby AD, definováním sadu filtrů založená na atributu. Výchozí obor je "všichni uživatelé v Workday". Příklad filtrů:

   * Příklad: Obor pro uživatele s ID pracovní mezi 1000000 a 2000000

      * Atribut: WorkerID

      * Operátor: Shoda REGEX

      * Hodnota: (1[0-9][0-9][0-9][0-9][0-9][0-9])

   * Příklad: Pouze zaměstnanci a není contingent pracovníkům 

      * Atribut: EmployeeID

      * Operátor: Není NULL.

3.  V **cílový objekt akce** pole, můžete globálně filtrovat, které akce budou moci provést na službě Active Directory. **Vytvoření** a **aktualizace** jsou nejběžnější.

4.  V **mapování atributů** části, můžete definovat, jak jednotlivé Workday mapování atributů pro atributy služby Active Directory.

5. Klikněte na existující mapování atributů jej aktualizovat, nebo klikněte na tlačítko **přidat nové mapování** v dolní části obrazovky pro přidání nových mapování. Mapování u jednotlivých atribut podporuje tyto vlastnosti:

      * **Typ mapování**

         * **Přímé** – zapíše hodnotu atributu Workday do atribut AD bez úprav

         * **Konstantní** -zapsat hodnotu statické, konstantní řetězec do atribut AD

         * **Výraz** – umožňuje psát vlastní hodnotu do atribut AD založené na jeden nebo více atributů Workday. [Další informace najdete v článku na výrazy](../active-directory-saas-writing-expressions-for-attribute-mappings.md).

      * **Zdrojový atribut** -atribut uživatele z Workday. Pokud hledáte atribut neexistuje, přečtěte si téma [přizpůsobení v seznamu atributů uživatele Workday](#customizing-the-list-of-workday-user-attributes).

      * **Výchozí hodnota** – volitelné. Pokud zdrojový atribut má prázdnou hodnotu, mapování zapíše tuto hodnotu.
            Nejběžnější konfigurace je nechte pole prázdné.

      * **Atribut target** – atribut uživatele ve službě Active Directory.

      * **Objekty používající tento atribut odpovídat** – jestli toto mapování se má použít k jednoznačné identifikaci uživatele mezi Workday a služby Active Directory. To se obvykle nastavuje na pole ID pracovní pro Workday, který je obvykle namapována na jeden z atributů ID zaměstnance ve službě Active Directory.

      * **Odpovídající prioritu** – více odpovídající atributy lze nastavit. Pokud existuje více vyhodnocují se v pořadí podle tohoto pole. Jakmile je nalezena shoda, žádné další odpovídající atributy vyhodnocují.

      * **Použít toto mapování**
       
         * **Vždy** – použít toto mapování na obou vytvoření uživatele a aktualizovat akce

         * **Pouze během vytváření** -použít toto mapování pouze na akcí vytvoření uživatele

6. Chcete-li uložit vaše mapování, klikněte na tlačítko **Uložit** v horní části mapování atributů.

![Azure Portal](./media/workday-inbound-tutorial/WD_2.PNG)

**Tady jsou některé příklad mapování atributů mezi Workday a služby Active Directory s některé běžné výrazy**

-   Výraz, který se mapuje na atribut parentDistinguishedName slouží ke zřízení uživatelů pro různé organizační jednotky, na základě jednoho nebo více atributů zdroj Workday. Tento příklad zde umístí uživatelům v jiné organizační jednotky, na základě v jakém městě se.

-   Atribut userPrincipalName ve službě Active Directory je generována zřetězením ID uživatele Workday s příponou domény

-   [Je dokumentaci na zapisují se výrazy zde](../active-directory-saas-writing-expressions-for-attribute-mappings.md). To zahrnuje příklady, jak odebrat speciální znaky.

  
| WORKDAY ATRIBUT | ATRIBUT SLUŽBY ACTIVE DIRECTORY |  ODPOVÍDAJÍCÍ ID? | VYTVOŘIT / AKTUALIZOVAT |
| ---------- | ---------- | ---------- | ---------- |
| **WorkerID**  |  Číslo zaměstnance | **Ano** | Zapisovat pouze na vytvoření |
| **UserId**    |  CN    |   |   Zapisovat pouze na vytvoření |
| **Připojení k ("@", [UserID] "contoso.com")**   | userPrincipalName     |     | Zapisovat pouze na vytvoření 
| **Replace(Mid(Replace(\[UserID\], , "(\[\\\\/\\\\\\\\\\\\\[\\\\\]\\\\:\\\\;\\\\|\\\\=\\\\,\\\\+\\\\\*\\\\?\\\\&lt;\\\\&gt;\])", , "", , ), 1, 20), , "([\\\\.)\*\$](file:///\\.)*$)", , "", , )**      |    název účtu SAM            |     |         Zapisovat pouze na vytvoření |
| **Přepínače (\[Active\],, "0", "True", "1")** |  accountDisabled      |     | Vytvoření + aktualizace |
| **FirstName**   | givenName       |     |    Vytvoření + aktualizace |
| **LastName**   |   sn   |     |  Vytvoření + aktualizace |
| **PreferredNameData**  |  displayName |     |   Vytvoření + aktualizace |
| **Společnosti**         | společnosti   |     |  Vytvoření + aktualizace |
| **SupervisoryOrganization**  | oddělení  |     |  Vytvoření + aktualizace |
| **ManagerReference**   | manažer  |     |  Vytvoření + aktualizace |
| **BusinessTitle**   |  název     |     |  Vytvoření + aktualizace | 
| **AddressLineData**    |  streetAddress  |     |   Vytvoření + aktualizace |
| **Okres**   |   l   |     | Vytvoření + aktualizace |
| **CountryReferenceTwoLetter**      |   co |     |   Vytvoření + aktualizace |
| **CountryReferenceTwoLetter**    |  c  |     |         Vytvoření + aktualizace |
| **CountryRegionReference** |  St     |     | Vytvoření + aktualizace |
| **WorkSpaceReference** | physicalDeliveryOfficeName    |     |  Vytvoření + aktualizace |
| **PSČ**  |   PSČ  |     | Vytvoření + aktualizace |
| **PrimaryWorkTelephone**  |  telephoneNumber   |     | Vytvoření + aktualizace |
| **Fax**      | facsimileTelephoneNumber     |     |    Vytvoření + aktualizace |
| **Mobile**  |    mobilní       |     |       Vytvoření + aktualizace |
| **LocalReference** |  preferredLanguage  |     |  Vytvoření + aktualizace |                                               
| **Přepínače (\[okres\], "organizační jednotky standardní uživatelé, OU = Uživatelé, OU = výchozí, OU = umístění, DC = = contoso, DC = com", "Dallas", "organizační jednotky standardní uživatelé, OU = Uživatelé, OU = Dallas, OU = umístění, DC = = contoso, DC = com", "Austinu", "organizační jednotky standardní uživatelé, OU = Uživatelé, OU = Austinu, OU = umístění, DC = = contoso, DC = com ","Seattle"," organizační jednotky standardní uživatelé, OU = Uživatelé, OU = Seattle, OU = umístění, DC = = contoso, DC = com ","Praha"," organizační jednotky standardní uživatelé, OU = = oj uživatelé Londýn, OU = umístění, DC = = contoso, DC = com ")**  | parentDistinguishedName     |     |  Vytvoření + aktualizace |
  
### <a name="part-3-configure-the-on-premises-synchronization-agent"></a>Část 3: Konfigurace agenta synchronizace na místě

Chcete-li zřídit do služby Active Directory v místě, musí být agent nainstalován na serveru připojeném k doméně v doménové struktuře služby Active Directory desire. Správce domény (nebo Enterprise Admins) se vyžadují přihlašovací údaje k dokončení tohoto postupu.

**[Si můžete stáhnout zde agent synchronizace na místě](https://go.microsoft.com/fwlink/?linkid=847801)**

Po instalaci agenta, spusťte příkazy prostředí Powershell následující konfigurace agenta pro vaše prostředí.

**Příkaz #1**

> CD "C:\Program Files\Microsoft Azure AD Connect, zřizování Agent\Modules\AADSyncAgent" agenta\\moduly\\AADSyncAgent

> Import-Module "C:\Program Files\Microsoft Azure AD Connect zřizování Agent\Modules\AADSyncAgent\AADSyncAgent.psd1"

**Příkaz #2**

> Přidat ADSyncAgentActiveDirectoryConfiguration

* Vstup: "Název adresáře", zadejte název doménové struktury AD zadané v části \#2
* Vstup: Jméno a heslo správce pro doménovou strukturu služby Active Directory

**Příkaz #3**

> Přidat ADSyncAgentAzureActiveDirectoryConfiguration

* Vstup: Globální jméno a heslo správce pro vašeho tenanta Azure AD

>[!IMPORTANT]
>V současné době se o známý problém s přihlašovacími údaji globální správce nefunguje, pokud používají vlastní domény (Příklad: admin@contoso.com). Jako alternativní řešení, vytvořit a použít účet globálního správce s doméně onmicrosoft.com (Příklad: admin@contoso.onmicrosoft.com)

>[!IMPORTANT]
>V současné době není známý problém s přihlašovacími údaji globální správce nefunguje, pokud mají povolenou službou Multi-Factor authentication. Jako alternativní řešení zakázání služby Multi-Factor authentication pro globální správce.


**Příkaz #4**

> Get-AdSyncAgentProvisioningTasks

* Akce: Zkontrolujte, že data jsou vrácena. Tento příkaz automaticky vyhledá Workday zřizování aplikací v klientovi služby Azure AD. Příklad výstupu:

> Název: AD doménové struktury
>
> Povoleno: True
>
> DirectoryName : mydomain.contoso.com
>
> Credentialed: False
>
> Identifikátor: WDAYdnAppDelta.c2ef8d247a61499ba8af0a29208fb853.4725aa7b-1103-41e6-8929-75a5471a5203

**Příkaz #5**

> Spuštění AdSyncAgentSynchronization-automatické

**Příkaz #6**

> net stop aadsyncagent

**Příkaz #7**

> net start aadsyncagent

>[!TIP]
>Kromě "net" příkazy v prostředí Powershell, Služba agenta synchronizace lze také spustit a zastavit pomocí **Services.msc**. Pokud dojde k chybám při spouštění příkazů prostředí Powershell, ujistěte se, že **Microsoft Azure AD Connect zřizování Agent** běží v **Services.msc**.

![Služby](./media/workday-inbound-tutorial/Services.png)  

**Další konfiguraci pro zákazníky v Evropské unie**

Pokud klienta služby Azure Active Directory se nachází v jedné z datových center Evropa, postupujte podle níže další kroky.

1. Otevřete **Services.msc**a zastavení **Microsoft Azure AD Connect zřizování Agent** služby.
2. Přejděte do složky pro instalaci agenta (Příklad: C:\Program Files\Microsoft Azure AD připojit zřizování Agent).
3. Otevřete **SyncAgnt.exe.config** v textovém editoru.
4. Nahraďte https://manage.hub.syncfabric.windowsazure.com/Management s **https://eu.manage.hub.syncfabric.windowsazure.com/Management**
5. Nahraďte https://provision.hub.syncfabric.windowsazure.com/Provisioning s **https://eu.provision.hub.syncfabric.windowsazure.com/Provisioning**
6. Uložit **SyncAgnt.exe.config** souboru.
7. Otevřete **Services.msc**a spusťte **Microsoft Azure AD Connect zřizování Agent** služby.

**Řešení potíží s agenta**

[Protokolu událostí systému Windows](https://technet.microsoft.com/library/cc722404(v=ws.11).aspx) v systému Windows Server obsahuje počítače, který je hostitelem agenta události pro všechny operace prováděné tímto agentem. Pokud chcete zobrazit tyto události:
    
1. Otevřete **Eventvwr.msc**.
2. Vyberte **protokoly systému Windows > aplikace**.
3. Zobrazit všechny události zaznamenané v části zdroj **AADSyncAgent**. 
4. Zkontrolujte chyby a upozornění.

Pokud dojde k potížím oprávnění s buď služby Active Directory nebo Azure Active Directory přihlašovacích údajů zadaných v příkazy prostředí Powershell, zobrazí se chyba jako je tato: 
    
![Protokoly událostí](./media/workday-inbound-tutorial/Windows_Event_Logs.png) 


### <a name="part-4-start-the-service"></a>Část 4: Spuštění služby
Jakmile částí 1 – 3 byly dokončeny, můžete spustit službu zřizování zpět na portálu Azure.

1.  V **zřizování** nastavte **Stav zřizování** k **na**.

2. Klikněte na **Uložit**.

3. Tato akce spustí počáteční synchronizace může trvat proměnný počet hodin v závislosti na tom, kolik uživatelé jsou v Workday.

4. Kdykoli, zkontrolujte **protokoly auditu** na webu Azure portal zobrazíte jaké akce byla provedena zřizování služby. Protokoly auditu zobrazuje všechny události jednotlivých synchronizace provádí zřizování služby, například která bude číst z Workday a pak následně přidány nebo aktualizaci uživatelů do služby Active Directory. **[Naleznete v Průvodci zřizování generování sestav pro podrobné pokyny o tom, jak číst protokoly auditu](../active-directory-saas-provisioning-reporting.md)**

1.  Zkontrolujte [protokolu událostí systému Windows](https://technet.microsoft.com/library/cc722404(v=ws.11).aspx) na počítače Windows serveru, který je hostitelem agenta pro všechny nové chyby nebo upozornění. Tyto události je možné zobrazit spuštěním **Eventvwr.msc** na serveru a výběrem **protokoly systému Windows > aplikace**. Všechny zprávy týkající se zřizování se protokolují pod zdroj **AADSyncAgent**.

6. Byla dokončena, bude zapisovat souhrnné zprávy o auditu **zřizování** kartě, jak je uvedeno níže.

![Azure Portal](./media/workday-inbound-tutorial/WD_3.PNG)


## <a name="configuring-user-provisioning-to-azure-active-directory"></a>Konfiguraci zřizování uživatelů do Azure Active Directory
Jak nakonfigurovat zřizování do Azure Active Directory bude záviset na zřizování požadavky, jak je podrobně uvedeno v následující tabulce.

| Scénář | Řešení |
| -------- | -------- |
| **Uživatelé musí být zřízená služby Active Directory a Azure AD** | Použití  **[AAD Connect](../connect/active-directory-aadconnect.md)** |
| **Uživatelé musí být zřízená služby Active Directory pouze** | Použití  **[AAD Connect](../connect/active-directory-aadconnect.md)** |
| **Uživatelé musí být zřízená pouze Azure AD (jenom pro cloud)** | Použití **Workday zřízení Azure Active Directory** aplikace v galerii aplikací |

Pokyny k nastavení služby Azure AD Connect, najdete v článku [dokumentace Azure AD Connect](../connect/active-directory-aadconnect.md).

Následující části popisují nastavení připojení mezi Workday a Azure AD pro zřízení uživatele jenom pro cloud.

> [!IMPORTANT]
> Pokud máte jenom pro cloud uživatelům, kteří potřebují zřídit do služby Azure AD a není místní služby Active Directory pouze podle níže uvedeného postupu.

### <a name="part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday"></a>Část 1: Přidání aplikace zřizování konektor služby Azure AD a vytvoření připojení k Workday

**Postup konfigurace Workday zřízení Azure Active Directory pro uživatele jenom pro cloud:**

1.  Přejděte do části <https://portal.azure.com> (Soubor > Nový > Jiné).

2.  V levém navigačním panelu, vyberte **Azure Active Directory**

3.  Vyberte **podnikové aplikace, které**, pak **všechny aplikace**.

4.  Vyberte **přidat aplikaci**a pak vyberte **všechny** kategorie.

5.  Vyhledejte **Workday zřízení Azure AD**a přidejte tuto aplikaci z galerie.

6.  Po přidání aplikace a obrazovce s podrobnostmi aplikace je uvedené, vyberte **zřizování**

7.  Změna **zřizování** **režimu** k **automatické**

8.  Dokončení **přihlašovací údaje správce** části následujícím způsobem:

   * **Uživatelské jméno správce** – zadejte uživatelské jméno účtu systému integrace Workday, s připojeným názvem domény klienta. By měl vypadat podobně jako: username@contoso4

   * **Heslo správce –** zadejte heslo účtu Workday integrace systému

   * **URL – klienta** zadejte adresu URL pro koncový bod webové služby Workday pro vašeho klienta. To by měl vypadat jako: https://wd3-impl-services1.workday.com/ccx/service/contoso4, kde contoso4 se nahradí název vašeho klienta správné a wd3 impl se nahradí řetězce správné prostředí. Pokud je tato adresa URL není znám, spojte se s vaším Workday integrace partnera nebo podporu zástupcem určit správnou adresu URL používat.

   * **E-mailové oznámení –** zadejte e-mailovou adresu a zaškrtněte políčko "odeslání e-mailu, pokud dojde k selhání".

   * Klikněte **Test připojení** tlačítko.

   * Pokud je test připojení úspěšný, klikněte na tlačítko **Uložit** tlačítka v horní části. Pokud se nezdaří, zkontrolujte, zda jsou platná ve Workday Workday URL a pověření.

### <a name="part-2-configure-attribute-mappings"></a>Část 2: Konfigurace mapování atributů 

V této části nakonfigurujete uživatelská data tok z Workday do Azure Active Directory pro uživatele jenom pro cloud.

1. Na kartě zřizování pod **mapování**, klikněte na tlačítko **pracovníci synchronizovat do Azure AD**.

2. V **zdrojového objektu oboru** pole, můžete vybrat, které skupiny uživatelů v Workday by měla být v oboru pro zřizování do služby Azure AD, definováním sadu filtrů založená na atributu. Výchozí obor je "všichni uživatelé v Workday". Příklad filtrů:

   * Příklad: Obor pro uživatele s ID pracovní mezi 1000000 a 2000000

      * Atribut: WorkerID

      * Operátor: Shoda REGEX

      * Hodnota: (1[0-9][0-9][0-9][0-9][0-9][0-9])

   * Příklad: Pouze contingent pracovníků a ne regulární zaměstnanci

      * Atribut: ContingentID

      * Operátor: Není NULL.

3. V **cílový objekt akce** pole, můžete globálně filtrovat, které akce budou moci provést na Azure AD. **Vytvoření** a **aktualizace** jsou nejběžnější.

4. V **mapování atributů** části, můžete definovat, jak jednotlivé Workday mapování atributů pro atributy služby Active Directory.

5. Klikněte na existující mapování atributů jej aktualizovat, nebo klikněte na tlačítko **přidat nové mapování** v dolní části obrazovky pro přidání nových mapování. Mapování u jednotlivých atribut podporuje tyto vlastnosti:

   * **Typ mapování**

      * **Přímé** – zapíše hodnotu atributu Workday do atribut AD bez úprav

      * **Konstantní** -zapsat hodnotu statické, konstantní řetězec do atribut AD

      * **Výraz** – umožňuje psát vlastní hodnotu do atribut AD založené na jeden nebo více atributů Workday. [Další informace najdete v článku na výrazy](../active-directory-saas-writing-expressions-for-attribute-mappings.md).

   * **Zdrojový atribut** -atribut uživatele z Workday. Pokud hledáte atribut neexistuje, přečtěte si téma [přizpůsobení v seznamu atributů uživatele Workday](#customizing-the-list-of-workday-user-attributes).

   * **Výchozí hodnota** – volitelné. Pokud zdrojový atribut má prázdnou hodnotu, mapování zapíše tuto hodnotu.
            Nejběžnější konfigurace je nechte pole prázdné.

   * **Atribut target** – atribut uživatele ve službě Azure AD.

   * **Objekty používající tento atribut odpovídat** – jestli toto mapování se má použít k jednoznačné identifikaci uživatele mezi Workday a Azure AD. To se obvykle nastavuje na pole ID pracovní pro Workday, který je obvykle namapována do atribut rozšíření nebo atribut ID zaměstnance (Nový) ve službě Azure AD.

   * **Odpovídající prioritu** – více odpovídající atributy lze nastavit. Pokud existuje více vyhodnocují se v pořadí podle tohoto pole. Jakmile je nalezena shoda, žádné další odpovídající atributy vyhodnocují.

   * **Použít toto mapování**

     * **Vždy** – použít toto mapování na obou vytvoření uživatele a aktualizovat akce

     * **Pouze během vytváření** -použít toto mapování pouze na akcí vytvoření uživatele

6. Chcete-li uložit vaše mapování, klikněte na tlačítko **Uložit** v horní části mapování atributů.

### <a name="part-3-start-the-service"></a>Část 3: Spuštění služby
Jakmile částí 1 – 2 byly dokončeny, můžete spustit službu zřizování.

1. V **zřizování** nastavte **Stav zřizování** k **na**.

2. Klikněte na **Uložit**.

3. Tato akce spustí počáteční synchronizace může trvat proměnný počet hodin v závislosti na tom, kolik uživatelé jsou v Workday.

4. Jednotlivé synchronizační události lze zobrazit v **protokoly auditu** kartě. **[Naleznete v Průvodci zřizování generování sestav pro podrobné pokyny o tom, jak číst protokoly auditu](../active-directory-saas-provisioning-reporting.md)**

5. Byla dokončena, bude zapisovat souhrnné zprávy o auditu **zřizování** kartě, jak je uvedeno níže.

## <a name="configuring-writeback-of-email-addresses-to-workday"></a>Konfigurace e-mailové adresy k Workday zpětný zápis
Postupujte podle těchto pokynů můžete nakonfigurovat zpětný zápis e-mailové adresy uživatele ze služby Azure Active Directory k Workday.

### <a name="part-1-adding-the-provisioning-connector-app-and-creating-the-connection-to-workday"></a>Část 1: Přidání zřizování konektor aplikace a vytvoření připojení k Workday

**Postup konfigurace Workday zřízení služby Active Directory:**

1. Přejděte na <https://portal.azure.com>.

2. V levém navigačním panelu, vyberte **Azure Active Directory**

3. Vyberte **podnikové aplikace, které**, pak **všechny aplikace**.

4. Vyberte **přidat aplikaci**, vyberte **všechny** kategorie.

5. Vyhledejte **zpětný zápis Workday**a přidejte tuto aplikaci z galerie.

6. Po přidání aplikace a obrazovce s podrobnostmi aplikace je uvedené, vyberte **zřizování**

7. Změna **zřizování** **režimu** k **automatické**

8. Dokončení **přihlašovací údaje správce** části následujícím způsobem:

   * **Uživatelské jméno správce** – zadejte uživatelské jméno účtu systému integrace Workday, s připojeným názvem domény klienta. By měl vypadat podobně jako: username@contoso4

   * **Heslo správce –** zadejte heslo účtu Workday integrace systému

   * **URL – klienta** zadejte adresu URL pro koncový bod webové služby Workday pro vašeho klienta. To by měl vypadat jako: https://wd3-impl-services1.workday.com/ccx/service/contoso4, kde contoso4 se nahradí název vašeho klienta správné a wd3 impl se nahradí řetězce správné prostředí (v případě potřeby).

   * **E-mailové oznámení –** zadejte e-mailovou adresu a zaškrtněte políčko "odeslání e-mailu, pokud dojde k selhání".

   * Klikněte **Test připojení** tlačítko. Pokud je test připojení úspěšný, klikněte na tlačítko **Uložit** tlačítka v horní části. Pokud se nezdaří, zkontrolujte, zda jsou platná ve Workday Workday URL a pověření.

### <a name="part-2-configure-attribute-mappings"></a>Část 2: Konfigurace mapování atributů 

V této části nakonfigurujete, jak jsou data uživatele z Workday do služby Active Directory.

1. Na kartě zřizování pod **mapování**, klikněte na tlačítko **synchronizaci Azure AD uživatelům Workday**.

2. V **zdrojového objektu oboru** pole, můžete volitelně filtrovat, které skupiny uživatelů v Azure Active Directory musí mít své e-mailové adresy zapisovat zpátky do Workday. Výchozí obor je "všichni uživatelé ve službě Azure AD". 

3. V **mapování atributů** části, můžete definovat, jak jednotlivé Workday mapování atributů pro atributy služby Active Directory. Existuje mapování pro e-mailovou adresu ve výchozím nastavení. Odpovídající ID však musí aktualizovat pro vyhledání uživatelů ve službě Azure Active Directory s jejich odpovídající položky Workday. Oblíbené odpovídající metoda je synchronizovat s ID pracovní Workday nebo ID zaměstnance na extensionAttribute1 15 ve službě Azure AD a pak pomocí tohoto atributu ve službě Azure AD pro vyhledání uživatelů zpět v Workday.

4. Chcete-li uložit vaše mapování, klikněte na tlačítko **Uložit** v horní části mapování atributů.

### <a name="part-3-start-the-service"></a>Část 3: Spuštění služby
Jakmile částí 1 – 2 byly dokončeny, můžete spustit službu zřizování.

1. V **zřizování** nastavte **Stav zřizování** k **na**.

2. Klikněte na **Uložit**.

3. Tato akce spustí počáteční synchronizace může trvat proměnný počet hodin v závislosti na tom, kolik uživatelé jsou v Workday.

4. Jednotlivé synchronizační události lze zobrazit v **protokoly auditu** kartě. **[Naleznete v Průvodci zřizování generování sestav pro podrobné pokyny o tom, jak číst protokoly auditu](../active-directory-saas-provisioning-reporting.md)**

5. Byla dokončena, bude zapisovat souhrnné zprávy o auditu **zřizování** kartě, jak je uvedeno níže.

## <a name="customizing-the-list-of-workday-user-attributes"></a>Přizpůsobení seznamu Workday uživatelské atributy
Workday zřizování aplikací pro služby Active Directory a Azure AD oba obsahují výchozí seznam atributů uživatele Workday můžete vybrat z. Tyto seznamy však nejsou komplexní. WORKDAY podporuje mnoho stovky uživatelské atributy, které mohou být buď standardní nebo jedinečné pro vašeho klienta Workday. 

Zřizování služby Azure AD podporuje přizpůsobit seznam nebo atribut Workday zahrnout všechny atributy v [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) operace rozhraní API lidských zdrojů.

Chcete-li to provést, musíte použít [Workday Studio](https://community.workday.com/studio-download) k extrakci XPath výrazy, které představují atributy, které chcete použít a poté je přidejte do zřizování konfigurace pomocí editoru pokročilé atribut na portálu Azure.

**Načtení výraz XPath pro atribut Workday uživatele:**

1. Stáhněte a nainstalujte [Workday Studio](https://community.workday.com/studio-download). Budete potřebovat účet Workday komunity pro přístup k Instalační služby.

2. Stáhněte soubor jazyce Workday Human_Resources WDSL z této adresy URL: https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Human_Resources.wsdl

3. Spusťte Workday Studio.

4. Panelu příkazů vyberte **Workday > Test webové služby v Tester** možnost.

5. Vyberte **externí**a vyberte soubor Human_Resources WSDL jste si stáhli v kroku 2.

    ![WORKDAY Studio](./media/workday-inbound-tutorial/WDstudio1.PNG)

6. Nastavte **umístění** do `https://IMPL-CC.workday.com/ccx/service/TENANT/Human_Resources`, ale nahraďte skutečným "IMPL-CC" instance typu a "Klienta" s název vašeho skutečné klienta.

7. Nastavit **operace** k **Get_Workers**

8.  Klikněte na tlačítko malým **konfigurace** odkaz níže podokna požadavků a odpovědí a nastavte přihlašovací údaje Workday. Zkontrolujte **ověřování**a pak zadejte uživatelské jméno a heslo pro účet Workday integrace systému. Nezapomeňte formátu uživatelské jméno jako name@tenanta nechat **UsernameToken WS-Security** možnost.

    ![WORKDAY Studio](./media/workday-inbound-tutorial/WDstudio2.PNG)

9. Vyberte **OK**.

10. **Požadavku** podokně vložením XML níže a nastavte **Employee_ID** na ID zaměstnance reálného uživatele ve vašem klientovi Workday. Vyberte uživatele, který má atribut vyplněný, které chcete extrahovat.

    ```
    <?xml version="1.0" encoding="UTF-8"?>
    <env:Envelope xmlns:env="http://schemas.xmlsoap.org/soap/envelope/" xmlns:xsd="http://www.w3.org/2001/XMLSchema">
      <env:Body>
        <wd:Get_Workers_Request xmlns:wd="urn:com.workday/bsvc" wd:version="v21.1">
          <wd:Request_References wd:Skip_Non_Existing_Instances="true">
            <wd:Worker_Reference>
              <wd:ID wd:type="Employee_ID">21008</wd:ID>
            </wd:Worker_Reference>
          </wd:Request_References>
          <wd:Response_Group>
            <wd:Include_Reference>true</wd:Include_Reference>
            <wd:Include_Personal_Information>true</wd:Include_Personal_Information>
            <wd:Include_Employment_Information>true</wd:Include_Employment_Information>
            <wd:Include_Management_Chain_Data>true</wd:Include_Management_Chain_Data>
            <wd:Include_Organizations>true</wd:Include_Organizations>
            <wd:Include_Reference>true</wd:Include_Reference>
            <wd:Include_Transaction_Log_Data>true</wd:Include_Transaction_Log_Data>
            <wd:Include_Photo>true</wd:Include_Photo>
            <wd:Include_User_Account>true</wd:Include_User_Account>
            <wd:Include_Roles>true</wd:Include_Roles>
          </wd:Response_Group>
        </wd:Get_Workers_Request>
      </env:Body>
    </env:Envelope>
    ```
 
11. Klikněte **odeslat požadavek** (zelenou šipku) k provedení příkazu. Pokud úspěšné, odpověď by se měla objevit v **odpovědi** podokně. Zkontrolujte odpověď na Ujistěte se, že má datové jste zadali ID uživatele a není k chybě.

12. Pokud bylo úspěšné, zkopírujte soubor XML z **odpovědi** podokně a uložte ho jako soubor XML.

13. V nástroje příkazového řádku z Workday Studio, vyberte **soubor > Otevřít soubor...**  a otevřít soubor XML, který jste uložili. Otevře se v editoru Workday Studio XML.

    ![WORKDAY Studio](./media/workday-inbound-tutorial/WDstudio3.PNG)

14. Ve stromu souboru Procházet **/env: obálky > env: text > wd:Get_Workers_Response > wd:Response_Data > wd: pracovní** k nalezení dat uživatele. 

15. V části **wd: pracovní**, najít atribut, který chcete přidat a vyberte ho.

16. Zkopírujte výraz XPath pro vaše vybraný atribut mimo **cesta k dokumentu** pole.

1. Odeberte **/env:Envelope / env:Body / wd:Get_Workers_Response / wd:Response_Data nebo** předponu z zkopírovaný výraz.

18. Pokud je poslední položky v zkopírovaný výraz uzlem (Příklad: "/ wd: Birth_Date"), pak připojit **/text()** na konci výrazu. Toto není nutné v případě, že poslední položka je atributem (Příklad: "/@wd: typu").

19. Výsledek by měl být přibližně `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`. Toto je co zkopírujete do portálu Azure.


**Přidání vaše vlastní atribut uživatele Workday do konfiguraci zřizování:**

1. Spusťte [portál Azure](https://portal.azure.com)a přejděte do části zřizování konce pracovní doby zřizování aplikace, jak je popsáno výše v tomto kurzu.

2. Nastavit **Stav zřizování** k **vypnout**a vyberte **Uložit**. To vám pomůže, ujistěte se, že vaše změny se projeví pouze v případě, že je vše připraveno.

3. V části **mapování**, vyberte **synchronizovat pracovníkům OnPremises** (nebo **pracovníci synchronizovat do Azure AD**).

4. Přejděte k dolnímu okraji na další obrazovce a vyberte **zobrazit rozšířené možnosti**.

5. Vyberte **upravit seznam atributů pro Workday**.

    ![WORKDAY Studio](./media/workday-inbound-tutorial/WDstudio_AAD1.PNG)

6. Přejděte do dolní části seznamu atributů a kde jsou vstupní pole.

7. Pro **název**, zadejte zobrazovaný název pro vaše atribut.

8. Pro **typ**, vyberte typ, který odpovídá správně k atributu (**řetězec** je nejběžnější).

9. Pro **rozhraní API výraz**, zadejte výraz XPath, který jste zkopírovali z Workday Studio. Příklad: `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`

10. Vyberte **přidejte atribut**.

    ![WORKDAY Studio](./media/workday-inbound-tutorial/WDstudio_AAD2.PNG)

11. Vyberte **Uložit** výše a potom **Ano** do dialogového okna. Na obrazovce mapování atributů zavřete, pokud je stále otevřen.

12. Zpět na hlavní **zřizování** vyberte **synchronizovat pracovníkům OnPremises** (nebo **pracovníci synchronizovat do Azure AD**) znovu.

13. Vyberte **přidat nové mapování**.

14. Nový atribut by se měla zobrazit v **zdrojový atribut** seznamu.

15. Podle potřeby přidáte mapování pro nový atribut.

16. Po dokončení, nezapomeňte nastavit **Stav zřizování** zpět na **na** a uložte.

## <a name="known-issues"></a>Známé problémy

* Při spuštění **přidat ADSyncAgentAzureActiveDirectoryConfiguration** příkaz prostředí Powershell, je v současné době známý problém s přihlašovacími údaji globální správce nefunguje, pokud používají vlastní domény (Příklad: admin@contoso.com) . Jako alternativní řešení, vytvořit a použít účet globálního správce ve službě Azure AD s doméně onmicrosoft.com (Příklad: admin@contoso.onmicrosoft.com).

* Předchozí problém s protokoly auditu nejsou uvedena v klienty Azure AD, které jsou umístěné v Evropské unie byl vyřešen. Konfigurace dalších agenta je však nutná pro klienty Azure AD v EU. Podrobnosti najdete v tématu [část 3: Konfigurace agenta synchronizace na místě](#Part 3: Configure the on-premises synchronization agent)

## <a name="managing-personal-data"></a>Správa osobní data

Workday zřizování řešení pro službu Active Directory vyžaduje, aby byl nainstalován na serveru připojeném k doméně agent synchronizace, a Tento agent vytváří protokoly v protokolu událostí systému Windows, která může obsahovat identifikovatelné osobní údaje.

## <a name="next-steps"></a>Další postup

* [Zjistěte, jak získat sestavy o zřizování aktivity a zkontrolujte protokoly](../active-directory-saas-provisioning-reporting.md)
* [Naučte se konfigurovat jednotné přihlašování mezi Workday a Azure Active Directory](workday-tutorial.md)
* [Zjistěte, jak integrovat dalších aplikací SaaS Azure Active Directory](tutorial-list.md)
