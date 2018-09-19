---
title: 'Kurz: Konfigurace Workday pro automatické zřizování uživatelů pomocí Azure Active Directory | Dokumentace Microsoftu'
description: Zjistěte, jak konfigurovat Azure Active Directory a automaticky zřizovat a rušit zřízení uživatelských účtů do Workday.
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
ms.openlocfilehash: 917baa4b0d983df858c64cd0fa5b697b0fbb316c
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2018
ms.locfileid: "46298264"
---
# <a name="tutorial-configure-workday-for-automatic-user-provisioning-preview"></a>Kurz: Konfigurace Workday pro uživatele automatické zřizování (preview)

Cílem tohoto kurzu je zobrazit kroky, které je potřeba provést k importu uživatelů z Workday do služby Active Directory a Azure Active Directory, s volitelné zpětný zápis některých atributů do Workday.

## <a name="overview"></a>Přehled

[Služba zřizování uživatelů Azure Active Directory](../manage-apps/user-provisioning.md) integruje [Workday lidských zdrojů API](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) pro zřízení uživatelských účtů. Toto připojení používá Azure AD povolit následující uživatel pracovní postupy zřizování:

* **Zřizování uživatelů do služby Active Directory** -synchronizovat vybrané skupiny uživatelů z Workday do jednoho nebo více doménovými strukturami Active Directory.

* **Zřizování uživatelů jenom cloudu do služby Azure Active Directory** – v situacích, kde místní služby Active Directory se nepoužívá, uživatelům je možné zřídit přímo z Workday do Azure Active Directory pomocí služba zřizování uživatelů Azure AD. 

* **Zpětný zápis e-mailové adresy k Workday** – služba zřizování uživatelů Azure AD zapisovat e-mailové adresy uživatelů Azure AD zpátky do Workday. 

### <a name="what-human-resources-scenarios-does-it-cover"></a>Jaké scénáře lidských zdrojů se tento zákon vztahuje?

Workday uživatele pracovní postupy zřizování podporuje služby zřizování uživatelů Azure AD povolit automatizaci následující lidské zdroje a scénářů správy životního cyklu identit:

* **Náboru nových zaměstnanců** – při přidání nového zaměstnance do Workday, uživatelský účet se automaticky vytvoří v Active Directory, Azure Active Directory a volitelně Office 365 a [dalším aplikacím SaaS podporované službou Azure AD](../manage-apps/user-provisioning.md), se zpětným zápisem e-mailovou adresu do Workday.

* **Aktualizace atributu a profil zaměstnance** – při aktualizaci záznamu zaměstnance ve Workday (například jeho název, název nebo správce), jeho uživatelský účet se automaticky aktualizují v Active Directory, Azure Active Directory a volitelně Office 365 a [dalším aplikacím SaaS podporované službou Azure AD](../manage-apps/user-provisioning.md).

* **Zaměstnance ukončení** – když se ukončí zaměstnance ve Workday, automaticky se vypne jeho uživatelský účet v Active Directory, Azure Active Directory a volitelně Office 365 a [dalším aplikacím SaaS nepodporuje v Azure AD](../manage-apps/user-provisioning.md).

* **Zaměstnanec znovu hires** – když je rehired zaměstnance ve Workday, jejich starý účet můžete automaticky znovu aktivována nebo znovu zřídit (v závislosti na vašich předvoleb) do služby Active Directory, Azure Active Directory a volitelně Office 365 a [dalším aplikacím SaaS podporované službou Azure AD](../manage-apps/user-provisioning.md).

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Kdo je tento uživatel zřizování řešení, které nejlépe vyhovuje?

Tento uživatel Workday zřizování řešení je v současné době ve verzi public preview a je ideální pro:

* Organizace, které chtějí předem připravené, cloudové řešení pro zřizování Workday uživatelů

* Organizace, které vyžadují přímé uživatele zřizování z Workday do Active Directory nebo Azure Active Directory

* Organizace, které vyžadují, aby uživatelé, které se mají zřídit pomocí dat získaných z modulu Workday HCM (viz [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html))

* Organizace, které vyžadují propojení, přesunutí, a opuštění uživatelům synchronizovat na jeden nebo více lokalita služby Active Directory, domény a organizační jednotky pouze na základě změnit informace o uživateli v modulu Workday HCM (viz [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html))

* Organizace, které používají pro e-mailu Office 365

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-hybrid-note.md)]

## <a name="planning-your-solution"></a>Plánování řešení

Před zahájením integraci Workday, zkontrolujte následující požadavky a přečtěte si následující pokyny týkající se tak, aby odpovídaly vaší aktuální architektura služby Active Directory a zřizování uživatelů požadavky pomocí řešení poskytuje služba Azure Active Directory.

### <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu se předpokládá, že máte následující položky:

* Platné předplatné s přístupem globální správce Azure AD Premium P1
* Implementace klienta Workday pro účely testování a integrace
* Oprávnění správce ve Workday a vytvořte uživatele systému integrace, provést změny k testování daty o zaměstnancích pro účely testování
* Pro zřizování uživatelů služby Active Directory, je potřeba hostitelský server připojený k doméně se službou Windows 2012 nebo vyšší [místního agenta synchronizace](https://go.microsoft.com/fwlink/?linkid=847801)
* [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) synchronizace mezi službami Active Directory a Azure AD

### <a name="solution-architecture"></a>Architektury řešení

Azure AD poskytuje širokou škálu konektorů, které vám pomůžou vyřešit zřizování a správě identit. životní cyklus z Workday Active Directory, Azure AD, aplikacím SaaS a zřizování. Které funkce můžete používat a jak nastavit řešení se budou lišit v závislosti na prostředí a požadavky vaší organizace. Jako první krok trvat akcie o tom, kolik z následujících akcí jsou k dispozici a nasazené ve vaší organizaci:

* Kolik doménových struktur Active Directory se používá?
* Kolik domény služby Active Directory se používá?
* Kolik Active Directory organizační jednotky (OU) se používají?
* Kolik tenantů Azure Active Directory se používá?
* Existují uživatelé, kteří potřebují zřízené služby Active Directory a Azure Active Directory (například "hybridní" uživatelé)?
* Existují uživatelé, kteří potřebují zřízené služby Azure Active Directory, ale ne Active Directory (například "výhradně cloudový" uživatelé)?
* Potřeba e-mailové adresy uživatele na možné zpětně zapsat do Workday?

Jakmile budete mít odpovědi na tyto otázky, můžete naplánovat vaši Workday zřizování nasazení podle pokynů níže.

#### <a name="using-provisioning-connector-apps"></a>Použití zřizování aplikací konektoru

Azure Active Directory podporuje předem integrovaných konektorů zřizování Workday a mnoho dalších aplikací SaaS.

Jeden konektor zřizování rozhraní s rozhraním API jednozdrojového systému a poskytování dat umožňuje jeden cílový systém. Většina zřizování konektory, které podporuje Azure AD jsou určené pro jednu zdrojovou a cílovou systému (například Azure AD k ServiceNow) a lze nastavit tak, že přidáte aplikaci dotyčný z Galerie aplikací Azure AD (například ServiceNow).

Existuje vztah 1: 1 mezi zřizování instance konektoru a instancemi aplikací ve službě Azure AD:

| Zdrojový systém | Cílový systém |
| ---------- | ---------- |
| Azure AD tenant | Aplikace SaaS |

Při práci s Workday a služby Active Directory, existují ale více zdrojové a cílové systémů, aby bylo považováno za:

| Zdrojový systém | Cílový systém | Poznámky |
| ---------- | ---------- | ---------- |
| Pracovní den | Doménová struktura Active Directory | Každé doménové struktuře je považován za odlišné cílový systém |
| Pracovní den | Azure AD tenant | Podle potřeby uživatelů jenom cloudu |
| Doménová struktura Active Directory | Azure AD tenant | Tento tok zařizuje služba AAD Connect ještě dnes |
| Azure AD tenant | Pracovní den | Pro zpětný zápis e-mailových adres |

Pro usnadnění těchto více pracovních postupů na více systémů zdroj a cíl, Azure AD poskytuje více zřizování aplikací konektoru, které lze přidat z Galerie aplikací Azure AD:

![Galerie aplikací AAD](./media/workday-inbound-tutorial/WD_Gallery.PNG)

* **WORKDAY do Active Directory zřizování** – tato aplikace usnadňuje účet zřizování uživatelů z Workday do jedné doménové struktury služby Active Directory. Pokud máte více doménových struktur, můžete přidat jednu instanci této aplikace v galerii aplikací Azure AD pro každou doménovou strukturu služby Active Directory, které je potřeba zřídit.

* **K Azure AD zřizování WORKDAY** – AAD Connect je nástroj, který se má použít k synchronizaci uživatelů do Azure Active Directory, tato aplikace je možné pro usnadnění zřizování výhradně cloudový uživatelů z Workday do jednoho Azure Active Directory Tenanta Active Directory.

* **Zpětný zápis WORKDAY** – tato aplikace umožňuje zpětný zápis e-mailové adresy uživatele ze služby Azure Active Directory do Workday.

> [!TIP]
> Pravidelné "Workday" aplikace se používá pro nastavení jednotné přihlašování mezi Workday a Azure Active Directory. 

Jak nastavit a nakonfigurovat tyto speciální zřizování aplikací konektoru je předmětem zbývající části tohoto kurzu. Aplikace, které provedete konfiguraci bude záviset na systémy, které je potřeba zřídit a kolik doménové struktury služby Active Directory a Azure AD tenantů se ve vašem prostředí.

![Přehled](./media/workday-inbound-tutorial/WD_Overview.PNG)

## <a name="configure-a-system-integration-user-in-workday"></a>Konfigurace uživatele systému integrace ve Workday
Běžné požadavky všech konektorů zřizování Workday je, že se že vyžadují přihlašovací údaje pro integraci Workday systémový účet pro připojení k rozhraní API Workday lidských zdrojů. Tato část popisuje, jak vytvořit systémový integrátor účet ve Workday.

> [!NOTE]
> Je možné obejít tento postup a místo toho použijte účet globálního správce Workday jako systémový účet integrace. To může fungovat pro ukázky, ale nedoporučuje se používat pro nasazení v produkčním prostředí.

### <a name="create-an-integration-system-user"></a>Vytvořit uživatele systému integrace

**Chcete-li vytvořit uživatele systému integrace:**

1. Přihlaste se pomocí účtu správce tenanta Workday. V **Workday Workbench**, zadejte vytvořit uživatel do vyhledávacího pole a potom klikněte na tlačítko **vytvořit uživatele systému integrace**.

    ![Vytvořit uživatele](./media/workday-inbound-tutorial/IC750979.png "pro vytvoření uživatele")
2. Dokončení **vytvořit uživatele systému integrace** úkol zadáním uživatelského jména a hesla pro nového uživatele systému integrace.  
 * Nechte **vyžadují nové heslo při dalším přihlašování** možnost není zaškrtnuto, protože tento uživatel bude protokolování prostřednictvím kódu programu.
 * Nechte **minut časového limitu relace** s výchozí hodnotou 0, což zabrání relace uživatele vypršení časového limitu předčasně ukončen.

    ![Vytvořte uživatele systému integrace](./media/workday-inbound-tutorial/IC750980.png "vytvořte uživatele systému integrace")

### <a name="create-a-security-group"></a>Vytvořte skupinu zabezpečení
Budete muset vytvořit novou skupinu zabezpečení systému integrace bez omezení a přiřadit uživatele.

**Vytvoření skupiny zabezpečení:**

1. Zadejte do vyhledávacího pole vytvořte skupinu zabezpečení a potom klikněte na **vytvořit skupinu zabezpečení**.

    ![Skupiny CreateSecurity](./media/workday-inbound-tutorial/IC750981.png "CreateSecurity skupiny")
2. Dokončení **vytvořit skupinu zabezpečení** úloh.  
3. Vyberte **skupiny zabezpečení systému integrace (neomezená)** z **typ části klienty zabezpečení skupiny** rozevíracího seznamu.
4. Vytvořte skupinu zabezpečení, ke kterému se členy explicitně přidávat.

    ![Skupiny CreateSecurity](./media/workday-inbound-tutorial/IC750982.png "CreateSecurity skupiny")

### <a name="assign-the-integration-system-user-to-the-security-group"></a>Přiřadit ke skupině zabezpečení uživatele systému integrace

**Pokud chcete přiřadit uživatele systému integrace:**

1. Upravit skupinu zabezpečení zadejte do vyhledávacího pole a potom klikněte na tlačítko **upravit skupinu zabezpečení**.

    ![Upravit skupinu zabezpečení](./media/workday-inbound-tutorial/IC750983.png "upravit skupinu zabezpečení")
1. Vyhledejte a vyberte novou skupinu zabezpečení integrace podle názvu.

    ![Upravit skupinu zabezpečení](./media/workday-inbound-tutorial/IC750984.png "upravit skupinu zabezpečení")
2. Přidání nového uživatele systému integrace do nové skupiny zabezpečení. 

    ![Systémová skupina zabezpečení](./media/workday-inbound-tutorial/IC750985.png "systémová skupina zabezpečení")  

### <a name="configure-security-group-options"></a>Konfigurovat možnosti zabezpečení skupiny
V tomto kroku budete udělit oprávnění zásad pro pracovníka data do skupiny zabezpečení domény zabezpečení.

**Chcete-li nakonfigurovat možnosti pro skupiny zabezpečení:**

1. Zadejte **zásady zabezpečení domény** ve vyhledávacím poli a pak kliknutím na odkaz **zásady zabezpečení domény pro funkční oblast**.  

    ![Zásady zabezpečení domény](./media/workday-inbound-tutorial/IC750986.png "zásady zabezpečení domény")  
2. Hledání pro systém, vyberte **systému** funkční oblast.  Klikněte na **OK**.  

    ![Zásady zabezpečení domény](./media/workday-inbound-tutorial/IC750987.png "zásady zabezpečení domény")  
3. V seznamu zásad zabezpečení pro funkční oblasti systému rozbalte **bezpečnostní správu** a vyberte zásady zabezpečení domény **externí zřizování účtů**.  

    ![Zásady zabezpečení domény](./media/workday-inbound-tutorial/IC750988.png "zásady zabezpečení domény")  
1. Klikněte na tlačítko **upravit oprávnění**a pak klikněte na **upravit oprávnění** stránky dialogového okna Přidat novou skupinu zabezpečení do seznamu skupiny zabezpečení s **získat** a **Put**  integrace oprávnění.

    ![Upravit oprávnění](./media/workday-inbound-tutorial/IC750989.png "upravit oprávnění")  

1. Opakujte kroky 1 – 4 výše pro všechny zbývající zásady zabezpečení:

| Operace | Zásady zabezpečení domény |
| ---------- | ---------- | 
| Operace GET a Put | Dat pracovního procesu: Veřejný pracovní sestavy |
| Operace GET a Put | Data pracovních procesů: Kontaktní informace o práci |
| Získat | Data pracovních procesů: Všechny pozice |
| Získat | Pracovní Data: Personální informace o aktuální |
| Získat | Data pracovních procesů: Název firmy na pracovní profil |


### <a name="activate-security-policy-changes"></a>Aktivace změny zásad zabezpečení

**Aktivace změny zásad zabezpečení:**

1. Zadejte aktivovat do vyhledávacího pole a potom klikněte na odkaz **aktivovat čekající změny zásad zabezpečení**.

    ![Aktivovat](./media/workday-inbound-tutorial/IC750992.png "aktivovat") 
2. Začátek úlohy aktivovat čekající změny zásad zabezpečení pro účely auditování zadejte komentář a potom klikněte na **OK**. 

    ![Aktivovat čekající zabezpečení](./media/workday-inbound-tutorial/IC750993.png "aktivovat čekající zabezpečení")  
1. Dokončení úlohy na další obrazovce zaškrtnutím políčka **potvrdit**a potom klikněte na tlačítko **OK**.

    ![Aktivovat čekající zabezpečení](./media/workday-inbound-tutorial/IC750994.png "aktivovat čekající zabezpečení")  

## <a name="configuring-user-provisioning-from-workday-to-active-directory"></a>Konfigurace zřizování uživatelů z Workday do Active Directory
Postupujte podle těchto pokynů ke konfiguraci uživatelského účtu pro každou doménovou strukturu služby Active Directory, které vyžadují zřizování pro zřizování z Workday.

### <a name="planning"></a>Plánování

Před konfigurací zřizování uživatelů pro doménovou strukturu služby Active Directory, zvažte následující otázky. Odpovědi na tyto otázky určí, jak filtry oborů a mapování atributů musí být nastavena. 

* **Co uživatelé ve Workday je potřeba zřídit na těchto doménových strukturách služby Active Directory?**

   * *Příklad: Uživatelů Workday "Společnost" atributu obsahuje hodnotu "Contoso", kde atribut "Worker_Type" obsahuje "Normální"*

* **Jak se uživatelé směrují do jiné organizační jednotky (OU)?**

   * *Příklad: Uživatelé se směrují do organizační jednotky, které odpovídají umístění sady office, jak je definováno ve Workday "Magistrát" a "Country_Region_Reference" atributy*

* **Jak by měl být následující atributy vyplní ve službě Active Directory?**

   * Běžný název (cn)
      * *Příklad: Použití hodnota Workday User_ID, úmluvu lidské zdroje*
      
   * ID zaměstnance (employeeId)
      * *Příklad: Použijte hodnotu Workday Worker_ID*
      
   * Název účtu SAM (sAMAccountName)
      * *Příklad: Použití hodnota Workday User_ID, filtruje prostřednictvím Azure AD zřizování výraz odebrat neplatné znaky.*
      
   * Hlavní název uživatele (userPrincipalName)
      * *Příklad: Hodnota Workday User_ID, použijte s Azure AD zřizování výraz do názvu domény*

* **Jak by uživatelé si měly odpovídat mezi Workday a služby Active Directory?**

  * *Příklad: Uživatelé s konkrétní aplikací Workday "Worker_ID" hodnota jsou porovnány s uživatelů služby Active Directory, kde "employeeID" mají stejnou hodnotu. Pokud není nalezena hodnota Worker_ID ve službě Active Directory, vytvořte nového uživatele.*
  
* **Doménové struktury služby Active Directory už obsahuje ID požadované pro odpovídající logiku pro práci uživatelů?**

  * *Příklad: Pokud je toto nové nasazení Workday, důrazně doporučujeme, že služby Active Directory se předvyplní správné hodnoty Workday Worker_ID (nebo jedinečnou hodnotu ID podle výběru) zajistit odpovídající logiky co nejjednodušší.*
    
    
### <a name="part-1-adding-the-provisioning-connector-app-and-creating-the-connection-to-workday"></a>Část 1: Přidání zřizování konektoru aplikace a vytvoření připojení k Workday

**Konfigurace Workday do Active Directory zřizování:**

1.  Přejděte na <https://portal.azure.com>.

2.  V levém navigačním panelu vyberte **Azure Active Directory**

3.  Vyberte **podnikové aplikace**, pak **všechny aplikace**.

4.  Vyberte **přidat aplikaci**a vyberte **všechny** kategorie.

5.  Vyhledejte **zřizování Workday do Active Directory**a přidejte tuto aplikaci z galerie.

6.  Jakmile aplikace přidána, a na obrazovce s podrobnostmi aplikace se zobrazí, vyberte **zřizování**

7.  Změnit **zřizování** **režimu** k **automatické**

8.  Dokončení **přihlašovacích údajů správce** části následujícím způsobem:

   * **Uživatelské jméno správce** – zadejte uživatelské jméno účtu systému integraci Workday, se připojí název domény tenantu. **By měla vypadat podobně jako: username@contoso4**

   * **Heslo správce –** zadejte heslo účtu systému integraci Workday

   * **Adresa URL tenanta** zadejte adresu URL pro koncový bod webové služby Workday pro vašeho tenanta. To by měl vypadat takto: https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources, kde contoso4 se nahradí názvem vašeho tenanta správné a wd3 impl nahrazuje řetězcem správné prostředí.

   * **Doménové struktury služby Active Directory -** "Name" Active Directory doménové struktury, vrácené rutinu powershellu Get-ADForest. Toto je obvykle řetězec jako: *contoso.com*

   * **Kontejner služby Active Directory -** zadejte řetězec kontejneru, který obsahuje všechny uživatele v doménové struktuře AD. Příklad: *organizační jednotky = standardní uživatelé, OU = Users, DC = contoso, DC = test*

   * **E-mailové oznámení –** zadejte svou e-mailovou adresu a zaškrtněte políčko "Odeslat e-mail, pokud dojde k selhání".

   * Klikněte na tlačítko **Test připojení** tlačítko. Pokud je test připojení úspěšný, klikněte na tlačítko **Uložit** tlačítko v horní části. Pokud selže, zkontrolujte, že přihlašovací údaje Workday jsou platné ve Workday. 

![portál Azure](./media/workday-inbound-tutorial/WD_1.PNG)

### <a name="part-2-configure-attribute-mappings"></a>Část 2: Konfigurace mapování atributů 

V této části můžete nakonfigurovat uživatele tok dat z Workday do Active Directory.

1.  Na kartě zřizování v rámci **mapování**, klikněte na tlačítko **synchronizovat Workday zaměstnanců, kteří mají místní**.

2.  V **obor zdrojového objektu** pole, můžete vybrat, které skupiny uživatelů ve Workday by měla být v oboru pro zřizování do služby AD, díky definování sady filtrů založený na atributu. Výchozí obor je "všichni uživatelé ve Workday". Příklad filtrů:

   * Příklad: Obor pro uživatele s ID pracovních procesů mezi 1000000 a 2000000

      * Atribut: WorkerID

      * Operátor: Regulárního výrazu shody

      * Hodnota: (1[0-9][0-9][0-9][0-9][0-9][0-9])

   * Příklad: Pouze zaměstnanci a nikoli závislé pracovní procesy 

      * Atribut: EmployeeID

      * Operátor: Není rovno hodnotě NULL

3.  V **akce cílového objektu** pole, můžete globálně filtrovat, jaké akce můžou být proveden na Active Directory. **Vytvoření** a **aktualizace** rozsahu je nejvíce.

4.  V **mapování atributů** oddílu, můžete definovat jak jednotlivé Workday atributy mapování atributů služby Active Directory.

5. Klikněte na existující mapování atributů ji aktualizovat, nebo klikněte na tlačítko **přidat nové mapování** v dolní části obrazovky, chcete-li přidat nové mapování. Mapování individuální atribut podporuje tyto vlastnosti:

      * **Typ mapování**

         * **Přímé** – hodnota atributu Workday zapíše atribut AD, a to bez jediné změny

         * **Konstantní** -zapsat hodnotu statické, konstantní řetězec pro atribut AD

         * **Výraz** – umožňuje psát vlastní hodnotu pro atribut AD založené na jeden nebo více atributů Workday. [Další informace najdete v článku o výrazech](../manage-apps/functions-for-customizing-application-data.md).

      * **Zdrojový atribut** – atribut uživatele z Workday. Pokud hledáte atribut není k dispozici, najdete v článku [přizpůsobení seznamu atributů uživatelů Workday](#customizing-the-list-of-workday-user-attributes).

      * **Výchozí hodnota** – volitelné. Pokud zdrojový atribut má prázdnou hodnotu, mapování zapíše tuto hodnotu.
            Nejběžnější konfigurací je toto pole nechat prázdné.

      * **Cílový atribut** – atribut uživatele ve službě Active Directory.

      * **Párování objektů pomocí tohoto atributu** – jestli má toto mapování použít k jednoznačné identifikaci uživatele mezi Workday a služby Active Directory. To se obvykle nastavuje na pole ID pracovního procesu pro Workday, která se obvykle mapuje na jeden z atributů ID zaměstnance ve službě Active Directory.

      * **Priorita porovnávání** – více shodné atributy lze nastavit. Když je více, jsou vyhodnocovány v pořadí určeném parametrem toto pole. Jakmile se najde shoda, žádné další odpovídající atributy jsou vyhodnocovány.

      * **Použít toto mapování**
       
         * **Vždy** – použít toto mapování na obou vytvoření uživatele a aktualizovat akce

         * **Jenom během vytváření** -použít toto mapování jenom na akce vytvoření uživatele

6. Chcete-li uložit mapování, klikněte na tlačítko **Uložit** v horní části mapování atributů.

![portál Azure](./media/workday-inbound-tutorial/WD_2.PNG)

**Níže je několik příkladů mapování atributů mezi Workday a Active Directory, se některé běžné výrazy**

-   Výraz, který se mapuje na atribut parentDistinguishedName slouží k zřizování uživatelů pro různé organizační jednotky, na základě jednoho nebo více atributů zdroj Workday. Tento příklad umístí uživatele na základě různých organizačních jednotek ve městě se.

-   Atribut userPrincipalName ve službě Active Directory je generována zřetězením ID uživatele Workday s příponou domény

-   [Je dokumentace ke službě na psaní výrazů zde](../manage-apps/functions-for-customizing-application-data.md). To zahrnuje příklady o tom, jak odeberte speciální znaky.

  
| WORKDAY ATRIBUT | ATRIBUT SLUŽBY ACTIVE DIRECTORY |  ODPOVÍDAJÍCÍ ID? | VYTVOŘIT / AKTUALIZOVAT |
| ---------- | ---------- | ---------- | ---------- |
| **WorkerID**  |  EmployeeID | **Ano** | Zapisovat pouze při vytváření |
| **UserId**    |  CN    |   |   Zapisovat pouze při vytváření |
| **Připojte se k ("@", [ID], "contoso.com")**   | userPrincipalName (Hlavní název uživatele)     |     | Zapisovat pouze při vytváření 
| **Nahraďte (Mid (Nahraďte (\[UserID\],, "(\[ \\ \\ / \\ \\ \\ \\ \\ \\ \[ \\\\\]\\\\:\\\\;\\ \\|\\\\=\\\\,\\\\+\\\\\*\\ \\? \\ \\ &lt; \\ \\ &gt; \]) "," ",), 1, 20)," ([\\\\.) \* \$] (file:///\\.) *$)", , "", , )**      |    SAMAccountName            |     |         Zapisovat pouze při vytváření |
| **Přepínač (\[aktivní\],, "0", "True", "1")** |  accountDisabled      |     | Vytváření a aktualizace |
| **Jméno**   | givenName       |     |    Vytváření a aktualizace |
| **LastName**   |   sériové číslo   |     |  Vytváření a aktualizace |
| **PreferredNameData**  |  displayName |     |   Vytváření a aktualizace |
| **Společnosti**         | Společnosti   |     |  Vytváření a aktualizace |
| **SupervisoryOrganization**  | Oddělení  |     |  Vytváření a aktualizace |
| **ManagerReference**   | manažer  |     |  Vytváření a aktualizace |
| **BusinessTitle**   |  název     |     |  Vytváření a aktualizace | 
| **AddressLineData**    |  streetAddress  |     |   Vytváření a aktualizace |
| **Obec**   |   l   |     | Vytváření a aktualizace |
| **CountryReferenceTwoLetter**      |   co |     |   Vytváření a aktualizace |
| **CountryReferenceTwoLetter**    |  c  |     |         Vytváření a aktualizace |
| **CountryRegionReference** |  St     |     | Vytváření a aktualizace |
| **WorkSpaceReference** | physicalDeliveryOfficeName    |     |  Vytváření a aktualizace |
| **PSČ**  |   PSČ  |     | Vytváření a aktualizace |
| **PrimaryWorkTelephone**  |  telephoneNumber   |     | Vytváření a aktualizace |
| **Fax**      | facsimileTelephoneNumber     |     |    Vytváření a aktualizace |
| **Mobilní zařízení**  |    Mobilní zařízení       |     |       Vytváření a aktualizace |
| **LocalReference** |  preferredLanguage  |     |  Vytváření a aktualizace |                                               
| **Přepínač (\[magistrát\], "organizační jednotky standardní uživatelé, OU = uživatelů, OU = výchozí, OU = umístění, DC = = contoso, DC = com", "Dallas", "organizační jednotky standardní uživatelé, OU = uživatelů, OU = Dallas, OU = umístění, DC = = contoso, DC = com", "Austin", "organizační jednotky standardní uživatelé, OU = Uživatelé, OU = Austin, OU = umístění, DC = = contoso, DC = com ","Seattle"," organizační jednotky standardní uživatelé, OU = uživatelů, OU = Seattle, OU = umístění, DC = = contoso, DC = com ","Londýn"," organizační jednotky standardní uživatelé, OU = uživatelů, OU = Londýn, OU = umístění, DC = = contoso, DC = com ")**  | parentDistinguishedName     |     |  Vytváření a aktualizace |
  
### <a name="part-3-configure-the-on-premises-synchronization-agent"></a>Část 3: Konfigurace místního agenta synchronizace

Chcete-li zřídit do místní služby Active Directory, musí být agenta nainstalovanou na serveru připojeném k doméně v doménové struktuře služby Active Directory přání. Správce domény (nebo Enterprise Admins) se vyžadují přihlašovací údaje k dokončení postupu.

**[Agenta synchronizace s místními tady si můžete stáhnout](https://go.microsoft.com/fwlink/?linkid=847801)**

Po instalaci agenta, spuštěním následujících příkazů Powershellu ke konfiguraci agenta pro vaše prostředí.

**Příkaz #1**

> Agent "C:\Program Files\Microsoft Azure AD Connect, zřizování Agent\Modules\AADSyncAgent" CD\\moduly\\AADSyncAgent

> Import-Module "C:\Program Files\Microsoft Azure AD Connect zřizování Agent\Modules\AADSyncAgent\AADSyncAgent.psd1"

**Příkaz #2**

> Přidat ADSyncAgentActiveDirectoryConfiguration

* Vstup: "Název adresáře", zadejte název doménové struktuře Active Directory zadané v části \#2
* Vstup: Uživatelské jméno správce a heslo pro doménovou strukturu služby Active Directory

>[!TIP]
> Pokud se zobrazí chybová zpráva "vztah mezi důvěryhodné domény a primární doménou selhal", je vzhledem k tomu, že místní počítač je v prostředí, kde jsou nakonfigurované více doménových struktur služby Active Directory nebo doménami a nakonfigurovat alespoň jeden vztah důvěryhodnosti relace je buď nebo nejsou funkční. Chcete-li problém vyřešit, opravte nebo odeberte vztah porušení vztahu důvěryhodnosti.

**Příkaz #3**

> Přidat ADSyncAgentAzureActiveDirectoryConfiguration

* Vstup: Uživatelské jméno globálního správce a heslo pro vašeho tenanta Azure AD

>[!IMPORTANT]
>V současné době je známý problém s přihlašovací údaje globálního správce nebudou fungovat, pokud používají vlastní doménu (Příklad: admin@contoso.com). Alternativním řešením je vytvořit a používat účet globálního správce se doménu onmicrosoft.com (Příklad: admin@contoso.onmicrosoft.com)

>[!IMPORTANT]
>V současné době není známý problém s přihlašovací údaje globálního správce nebudou fungovat, pokud mají povolené ověřování službou Multi-Factor Authentication. Jako alternativní řešení zakažte ověřování službou Multi-Factor Authentication pro globální správce.

**Příkaz #4**

> Get-AdSyncAgentProvisioningTasks

* Akce: Zkontrolujte, že data jsou vrácena. Tento příkaz automaticky zjišťuje Workday zřizování aplikací ve vašem tenantovi Azure AD. Příklad výstupu:

> Název: Moje doménové struktuře Active Directory
>
> Povoleno: True
>
> DirectoryName : mydomain.contoso.com
>
> Credentialed: False
>
> Identifikátor: WDAYdnAppDelta.c2ef8d247a61499ba8af0a29208fb853.4725aa7b-1103-41e6-8929-75a5471a5203

**Příkaz #5**

> Start AdSyncAgentSynchronization-automatické

**Příkaz #6**

> aadsyncagent net stop

**Příkaz #7**

> příkaz net start aadsyncagent

>[!TIP]
>Kromě příkazů "net" v prostředí Powershell služby agenta synchronizace lze také spustit a zastavit pomocí **Services.msc**. Pokud dojde k chybám při spuštění příkazů Powershellu, ujistěte se, **Microsoft Agent Azure AD Connect zřizování** běží v **Services.msc**.

![Služby](./media/workday-inbound-tutorial/Services.png)  

**Další konfigurace pro zákazníky v rámci Evropské unie**

Pokud váš tenant Azure Active Directory se nachází v jedné z datových center EU, postupujte podle dalších kroků.

1. Otevřít **Services.msc**a zastavit **Microsoft Agent Azure AD Connect zřizování** služby.
2. Přejděte do složky instalace agenta (Příklad: C:\Program Files\Microsoft agenta Azure AD Connect zřizování).
3. Otevřít **SyncAgnt.exe.config** v textovém editoru.
4. Nahraďte https://manage.hub.syncfabric.windowsazure.com/Management s **https://eu.manage.hub.syncfabric.windowsazure.com/Management**
5. Nahraďte https://provision.hub.syncfabric.windowsazure.com/Provisioning s **https://eu.provision.hub.syncfabric.windowsazure.com/Provisioning**
6. Uložit **SyncAgnt.exe.config** souboru.
7. Otevřít **Services.msc**a spusťte **Microsoft Agent Azure AD Connect zřizování** služby.

**Řešení potíží s agentem**

[Protokolu událostí Windows](https://technet.microsoft.com/library/cc722404(v=ws.11).aspx) počítače hostujícího agenta v systému Windows Server obsahuje události pro všechny operace prováděné tímto agentem. Chcete-li zobrazit tyto události:
    
1. Otevřít **Eventvwr.msc**.
2. Vyberte **protokoly Windows > aplikace**.
3. Zobrazit všechny události zapsané podle zdroje **AADSyncAgent**. 
4. Zkontrolujte chyby a upozornění.

Pokud dojde k problému oprávnění pomocí přihlašovacích údajů služby Active Directory nebo Azure Active Directory v příkazech prostředí Powershell k dispozici, zobrazí se chybu, jako je například tento: 
    
![Protokoly událostí](./media/workday-inbound-tutorial/Windows_Event_Logs.png) 


### <a name="part-4-start-the-service"></a>Část 4: Spuštění služby
Po dokončení části 1-3 můžete spustit službu zřizování zpět na portálu Azure portal.

1.  V **zřizování** kartu, nastavte **stavu zřizování** k **na**.

2. Klikněte na **Uložit**.

3. Tím se spustí počáteční synchronizaci, to může trvat proměnný počet hodin v závislosti na počtu uživatelů, kteří jsou ve Workday.

4. V okamžiku, zkontrolujte **protokoly auditu** kartu na webu Azure Portal najdete v článku jaké akce proběhla zřizovací služba. Protokoly auditu zobrazuje všechny události jednotlivou synchronizaci provést zřizovací služba, například kteří uživatelé jsou číst z Workday a pak následně přidáním nebo aktualizací do služby Active Directory. **[Zobrazit zřizování průvodce vytvářením sestav pro podrobné pokyny o tom, jak načíst protokoly auditu](../manage-apps/check-status-user-account-provisioning.md)**

1.  Zkontrolujte, [protokolu událostí Windows](https://technet.microsoft.com/library/cc722404(v=ws.11).aspx) na počítači s Windows serverem hostování agenta k nějakým novým chybám nebo upozornění. Tyto události jsou zobrazitelné spuštěním **Eventvwr.msc** na serveru a vyberete **protokoly Windows > aplikace**. Všechny zprávy týkající se zřizování jsou zaznamenány v rámci zdroje **AADSyncAgent**.

6. Jeden dokončeno, bude zapisovat souhrnnou sestavu auditu **zřizování** kartu, jak je znázorněno níže.

![portál Azure](./media/workday-inbound-tutorial/WD_3.PNG)


## <a name="configuring-user-provisioning-to-azure-active-directory"></a>Konfigurace zřizování uživatelů pro Azure Active Directory
Jak nakonfigurovat zřizování do služby Azure Active Directory závisí na požadavcích zřizování, jak je uvedeno v následující tabulce.

| Scénář | Řešení |
| -------- | -------- |
| **Uživatelé musí být zřízená služby Active Directory a Azure AD** | Použití  **[AAD Connect](../hybrid/whatis-hybrid-identity.md)** |
| **Uživatelé musí zřídit pouze do služby Active Directory** | Použití  **[AAD Connect](../hybrid/whatis-hybrid-identity.md)** |
| **Uživatelé musí být zřízená pouze Azure AD (jenom v cloudu)** | Použití **Workday do Azure Active Directory zřizování** aplikace v galerii aplikací |

Pokyny k nastavení služby Azure AD Connect najdete v článku [dokumentace ke službě Azure AD Connect](../hybrid/whatis-hybrid-identity.md).

Následující části popisují nastavení připojení mezi Workday a Azure AD ke zřízení uživatelů pouze cloudu.

> [!IMPORTANT]
> Podle níže uvedeného postupu, pouze pokud máte uživatelů pouze cloudu, které je potřeba zřídit do služby Azure AD a ne místní služby Active Directory.

### <a name="part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday"></a>Část 1: Přidání konektoru aplikace zřizování Azure AD a vytvoření připojení k Workday

**Postup konfigurace Workday do Azure Active Directory zřizování uživatelů jenom cloudu:**

1.  Přejděte do části <https://portal.azure.com> (Soubor > Nový > Jiné).

2.  V levém navigačním panelu vyberte **Azure Active Directory**

3.  Vyberte **podnikové aplikace**, pak **všechny aplikace**.

4.  Vyberte **přidat aplikaci**a pak vyberte **všechny** kategorie.

5.  Vyhledejte **Workday do Azure AD zřizování**a přidejte tuto aplikaci z galerie.

6.  Jakmile aplikace přidána, a na obrazovce s podrobnostmi aplikace se zobrazí, vyberte **zřizování**

7.  Změnit **zřizování** **režimu** k **automatické**

8.  Dokončení **přihlašovacích údajů správce** části následujícím způsobem:

   * **Uživatelské jméno správce** – zadejte uživatelské jméno účtu systému integraci Workday, se připojí název domény tenantu. By měla vypadat podobně jako: username@contoso4

   * **Heslo správce –** zadejte heslo účtu systému integraci Workday

   * **Adresa URL tenanta** zadejte adresu URL pro koncový bod webové služby Workday pro vašeho tenanta. To by měl vypadat takto: https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources, kde contoso4 se nahradí názvem vašeho tenanta správné a wd3 impl nahrazuje řetězcem správné prostředí. Pokud je tato adresa URL není známý, prosím pracujete s Workday integrace partnerů nebo podporu zástupce určit správnou adresu URL k použití.

   * **E-mailové oznámení –** zadejte svou e-mailovou adresu a zaškrtněte políčko "Odeslat e-mail, pokud dojde k selhání".

   * Klikněte na tlačítko **Test připojení** tlačítko.

   * Pokud je test připojení úspěšný, klikněte na tlačítko **Uložit** tlačítko v horní části. Pokud se to nezdaří, zkontrolujte, že adresa URL Workday a přihlašovací údaje jsou platné ve Workday.

### <a name="part-2-configure-attribute-mappings"></a>Část 2: Konfigurace mapování atributů 

V této části můžete nakonfigurovat jak uživatelská data z Workday do Azure Active Directory pro toky uživatelů pouze cloudu.

1. Na kartě zřizování v rámci **mapování**, klikněte na tlačítko **synchronizaci pracovních procesů do Azure AD**.

2. V **obor zdrojového objektu** pole, můžete vybrat, které skupiny uživatelů ve Workday by měla být v oboru pro zřizování do služby Azure AD tak, že definujete sady filtrů založený na atributu. Výchozí obor je "všichni uživatelé ve Workday". Příklad filtrů:

   * Příklad: Obor pro uživatele s ID pracovních procesů mezi 1000000 a 2000000

      * Atribut: WorkerID

      * Operátor: Regulárního výrazu shody

      * Hodnota: (1[0-9][0-9][0-9][0-9][0-9][0-9])

   * Příklad: Pouze závislé pracovní procesy a není regulární zaměstnanců

      * Atribut: ContingentID

      * Operátor: Není rovno hodnotě NULL

3. V **akce cílového objektu** pole, můžete globálně filtrovat, jaké akce můžou být proveden na Azure AD. **Vytvoření** a **aktualizace** rozsahu je nejvíce.

4. V **mapování atributů** oddílu, můžete definovat jak jednotlivé Workday atributy mapování atributů služby Active Directory.

5. Klikněte na existující mapování atributů ji aktualizovat, nebo klikněte na tlačítko **přidat nové mapování** v dolní části obrazovky, chcete-li přidat nové mapování. Mapování individuální atribut podporuje tyto vlastnosti:

   * **Typ mapování**

      * **Přímé** – hodnota atributu Workday zapíše atribut AD, a to bez jediné změny

      * **Konstantní** -zapsat hodnotu statické, konstantní řetězec pro atribut AD

      * **Výraz** – umožňuje psát vlastní hodnotu pro atribut AD založené na jeden nebo více atributů Workday. [Další informace najdete v článku o výrazech](../manage-apps/functions-for-customizing-application-data.md).

   * **Zdrojový atribut** – atribut uživatele z Workday. Pokud hledáte atribut není k dispozici, najdete v článku [přizpůsobení seznamu atributů uživatelů Workday](#customizing-the-list-of-workday-user-attributes).

   * **Výchozí hodnota** – volitelné. Pokud zdrojový atribut má prázdnou hodnotu, mapování zapíše tuto hodnotu.
            Nejběžnější konfigurací je toto pole nechat prázdné.

   * **Cílový atribut** – atribut uživatele ve službě Azure AD.

   * **Párování objektů pomocí tohoto atributu** – jestli má toto mapování použít k jednoznačné identifikaci uživatele mezi Workday a Azure AD. To se obvykle nastavuje na pole ID pracovního procesu pro Workday, která se obvykle mapuje na atribut ID zaměstnance (nová) nebo atributu rozšíření ve službě Azure AD.

   * **Priorita porovnávání** – více shodné atributy lze nastavit. Když je více, jsou vyhodnocovány v pořadí určeném parametrem toto pole. Jakmile se najde shoda, žádné další odpovídající atributy jsou vyhodnocovány.

   * **Použít toto mapování**

     * **Vždy** – použít toto mapování na obou vytvoření uživatele a aktualizovat akce

     * **Jenom během vytváření** -použít toto mapování jenom na akce vytvoření uživatele

6. Chcete-li uložit mapování, klikněte na tlačítko **Uložit** v horní části mapování atributů.

### <a name="part-3-start-the-service"></a>Část 3: Spuštění služby
Po dokončení části 1-2 je spustit službu zřizování.

1. V **zřizování** kartu, nastavte **stavu zřizování** k **na**.

2. Klikněte na **Uložit**.

3. Tím se spustí počáteční synchronizaci, to může trvat proměnný počet hodin v závislosti na počtu uživatelů, kteří jsou ve Workday.

4. Jednotlivé synchronizační události lze zobrazit v **protokoly auditu** kartu. **[Zobrazit zřizování průvodce vytvářením sestav pro podrobné pokyny o tom, jak načíst protokoly auditu](../manage-apps/check-status-user-account-provisioning.md)**

5. Jeden dokončeno, bude zapisovat souhrnnou sestavu auditu **zřizování** kartu, jak je znázorněno níže.

## <a name="configuring-writeback-of-email-addresses-to-workday"></a>Konfigurace zpětného zápisu e-mailových adres do Workday
Postupujte podle těchto pokynů a nakonfigurovat zpětný zápis e-mailové adresy uživatele ze služby Azure Active Directory do Workday.

### <a name="part-1-adding-the-provisioning-connector-app-and-creating-the-connection-to-workday"></a>Část 1: Přidání zřizování konektoru aplikace a vytvoření připojení k Workday

**Konfigurace Workday do Active Directory zřizování:**

1. Přejděte na <https://portal.azure.com>.

2. V levém navigačním panelu vyberte **Azure Active Directory**

3. Vyberte **podnikové aplikace**, pak **všechny aplikace**.

4. Vyberte **přidat aplikaci**a pak **všechny** kategorie.

5. Vyhledejte **zpětný zápis Workday**a přidejte tuto aplikaci z galerie.

6. Jakmile aplikace přidána, a na obrazovce s podrobnostmi aplikace se zobrazí, vyberte **zřizování**

7. Změnit **zřizování** **režimu** k **automatické**

8. Dokončení **přihlašovacích údajů správce** části následujícím způsobem:

   * **Uživatelské jméno správce** – zadejte uživatelské jméno účtu systému integraci Workday, se připojí název domény tenantu. By měla vypadat podobně jako: username@contoso4

   * **Heslo správce –** zadejte heslo účtu systému integraci Workday

   * **Adresa URL tenanta** zadejte adresu URL pro koncový bod webové služby Workday pro vašeho tenanta. To by měl vypadat takto: https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources, kde contoso4 se nahradí názvem vašeho tenanta správné a wd3 impl nahrazuje řetězcem správné prostředí (v případě potřeby).

   * **E-mailové oznámení –** zadejte svou e-mailovou adresu a zaškrtněte políčko "Odeslat e-mail, pokud dojde k selhání".

   * Klikněte na tlačítko **Test připojení** tlačítko. Pokud je test připojení úspěšný, klikněte na tlačítko **Uložit** tlačítko v horní části. Pokud se to nezdaří, zkontrolujte, že adresa URL Workday a přihlašovací údaje jsou platné ve Workday.

### <a name="part-2-configure-attribute-mappings"></a>Část 2: Konfigurace mapování atributů 

V této části můžete nakonfigurovat uživatele tok dat z Workday do Active Directory.

1. Na kartě zřizování v rámci **mapování**, klikněte na tlačítko **synchronizace Azure AD uživatelům Workday**.

2. V **obor zdrojového objektu** pole, můžete volitelně můžete filtrovat, které skupiny uživatelů v Azure Active Directory by měla mít své e-mailové adresy zapisovat zpátky do Workday. Výchozí obor je "všichni uživatelé ve službě Azure AD". 

3. V **mapování atributů** části, aktualizovat odpovídající ID k označení atributu v Azure Active Directory, kde je uložen na ID pro pracovníka Workday nebo ID zaměstnance. Oblíbené odpovídajícím metodou je synchronizovat Workday pracovního procesu ID nebo ID zaměstnance na extensionAttribute1 15 ve službě Azure AD a pak pomocí tohoto atributu ve službě Azure AD pro vyhledání uživatelů zpět ve Workday. 

4. Chcete-li uložit mapování, klikněte na tlačítko **Uložit** v horní části mapování atributů.

### <a name="part-3-start-the-service"></a>Část 3: Spuštění služby
Po dokončení části 1-2 je spustit službu zřizování.

1. V **zřizování** kartu, nastavte **stavu zřizování** k **na**.

2. Klikněte na **Uložit**.

3. Tím se spustí počáteční synchronizaci, to může trvat proměnný počet hodin v závislosti na počtu uživatelů, kteří jsou ve Workday.

4. Jednotlivé synchronizační události lze zobrazit v **protokoly auditu** kartu. **[Zobrazit zřizování průvodce vytvářením sestav pro podrobné pokyny o tom, jak načíst protokoly auditu](../manage-apps/check-status-user-account-provisioning.md)**

5. Jeden dokončeno, bude zapisovat souhrnnou sestavu auditu **zřizování** kartu, jak je znázorněno níže.

## <a name="customizing-the-list-of-workday-user-attributes"></a>Přizpůsobení seznamu atributů uživatelů Workday
Workday zřizování aplikací pro služby Active Directory a Azure AD, které obsahují výchozí seznam atributů uživatelů Workday můžete vybírat. Tyto seznamy však nejsou komplexní. WORKDAY podporuje stovky uživatelské atributy, které může být standardní nebo jedinečné pro vašeho tenanta Workday. 

Služba zřizování Azure AD podporuje možnost přizpůsobit si seznam nebo atribut Workday zahrnout všechny atributy v [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) operace rozhraní API pro lidské zdroje.

Chcete-li to provést, musíte použít [Workday Studio](https://community.workday.com/studio-download) extrahovat výrazy XPath, které reprezentují atributy, které chcete použít a pak je přidejte do vaší konfigurace zřizování pomocí editoru rozšířeného atributu na webu Azure Portal.

**K načtení výraz XPath pro atributů uživatelů Workday:**

1. Stáhněte a nainstalujte [Workday Studio](https://community.workday.com/studio-download). Budete potřebovat účet Workday komunity pro přístup k Instalační služby.

2. Stáhněte soubor jazyce Workday Human_Resources WDSL z této adresy URL: https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Human_Resources.wsdl

3. Spusťte Workday Studio.

4. Na panelu příkazů vyberte **Workday > Test webové služby v testování** možnost.

5. Vyberte **externí**a vyberte soubor Human_Resources WSDL jste si stáhli v kroku 2.

    ![WORKDAY Studio](./media/workday-inbound-tutorial/WDstudio1.PNG)

6. Nastavte **umístění** pole `https://IMPL-CC.workday.com/ccx/service/TENANT/Human_Resources`, ale "IMPL-CC" nahraďte skutečným typ instance a "TENANT" s vaším jménem skutečné tenanta.

7. Nastavte **operace** k **Get_Workers**

8.  Klikněte na malé **konfigurace** odkaz podokna požadavku nebo odpovědi k nastavení pověření Workday. Zkontrolujte **ověřování**a pak zadejte uživatelské jméno a heslo pro váš účet integrace systému Workday. Je potřeba formátovat jako uživatelské jméno name@tenanta nechat **WS-Security UsernameToken** zaškrtnutou možnost.

    ![WORKDAY Studio](./media/workday-inbound-tutorial/WDstudio2.PNG)

9. Vyberte **OK**.

10. **Žádosti** podokně Vložit v souboru XML níže a nastavte **Employee_ID** ID zaměstnance skutečné uživatele ve vašem tenantovi Workday. Vyberte uživatele, který má atribut vyplní, který chcete extrahovat.

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
 
11. Klikněte na tlačítko **odeslat požadavek** (zelená šipka) ke spuštění příkazu. Pokud úspěšné, odpověď by se měla objevit v **odpovědi** podokně. Zkontrolujte odpovědi k zajištění, že má data uživatelské ID, které jste zadali a ne o chybu.

12. V případě úspěchu, zkopírujte data XML z **odpovědi** podokně a uložte ho jako soubor XML.

13. Vyberte v nástroje příkazového řádku z Workday Studio se **soubor > Otevřít soubor...**  a otevřete soubor XML, který jste uložili. Tím otevřete v editoru Workday Studio XML.

    ![WORKDAY Studio](./media/workday-inbound-tutorial/WDstudio3.PNG)

14. Ve stromové struktuře souborů Procházet **/env: Obálka > env: text > wd:Get_Workers_Response > wd:Response_Data > wd: pracovního procesu** najít vaše uživatelská data. 

15. V části **wd: pracovního procesu**vyhledejte atributu, který chcete přidat a vyberte ji.

16. Zkopírujte výraz XPath pro váš vybraný atribut z celkového počtu **cesta k dokumentu** pole.

1. Odeberte **/env:Envelope / env / wd:Get_Workers_Response / wd:Response_Data /** předpony ze zkopírovaného výrazu.

18. Pokud se poslední položky zkopírované výrazu je uzel (Příklad: "/ wd: Birth_Date"), potom připojte **/text()** na konci výrazu. Toto není nutné v případě, že poslední položka je atribut (Příklad: "/@wd: typu").

19. Výsledek by měl vypadat `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`. Jedná se zkopírovat na web Azure Portal.


**Přidání vaší vlastní atribut uživatele Workday do vaší konfigurace zřizování:**

1. Spusťte [webu Azure portal](https://portal.azure.com)a přejděte do části zřizování z Workday, vaší aplikací, zřizování, jak je popsáno dříve v tomto kurzu.

2. Nastavte **stavu zřizování** k **vypnout**a vyberte **Uložit**. To vám pomůže zajistit, že vaše změny se projeví pouze v případě, že jste připraveni.

3. V části **mapování**vyberte **synchronizovat pracovníků místní** (nebo **synchronizaci pracovních procesů do Azure AD**).

4. Posuňte se dolů na další obrazovku a vyberte **zobrazit pokročilé možnosti**.

5. Vyberte **upravit seznam atributů pro Workday**.

    ![WORKDAY Studio](./media/workday-inbound-tutorial/WDstudio_AAD1.PNG)

6. Přejděte do dolní části seznamu atributů k umístění vstupních polí.

7. Pro **název**, zadejte zobrazovaný název pro atribut.

8. Pro **typ**, vyberte typ, který odpovídajícím způsobem odpovídá atributu (**řetězec** klienty jsou nejčastěji).

9. Pro **výraz rozhraní API**, zadejte výraz XPath, který jste zkopírovali z Workday Studio. Příklad: `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`

10. Vyberte **přidejte atribut**.

    ![WORKDAY Studio](./media/workday-inbound-tutorial/WDstudio_AAD2.PNG)

11. Vyberte **Uložit** výše a potom **Ano** do dialogového okna. Uzavření obrazovky mapování atributů, pokud je stále otevřen.

12. Zpět na hlavní **zřizování** kartu, vyberte možnost **synchronizovat pracovníků místní** (nebo **synchronizaci pracovních procesů do Azure AD**) znovu.

13. Vyberte **přidat nové mapování**.

14. Nový atribut by se měla objevit v **zdrojový atribut** seznamu.

15. Podle potřeby přidejte mapování pro nový atribut.

16. Až budete hotovi, nezapomeňte nastavit **stavu zřizování** zpět **na** a uložit.

## <a name="known-issues"></a>Známé problémy

* Při spuštění **přidat ADSyncAgentAzureActiveDirectoryConfiguration** příkaz prostředí Powershell v současné době je známý problém s přihlašovací údaje globálního správce nebudou fungovat, pokud používají vlastní doménu (Příklad: admin@contoso.com) . Jako alternativní řešení, vytvořit a používat účet globálního správce ve službě Azure AD s doménu onmicrosoft.com (Příklad: admin@contoso.onmicrosoft.com).

* Zápis dat do atributu thumbnailphoto nastavuje uživatele v místní službě Active Directory se momentálně nepodporuje.

* "Workday do služby Azure AD" konektor není aktuálně podporovaný v tenantů Azure AD, kde je povolena AAD Connect.  

* Předchozí problém s protokoly auditu se nezobrazují v tenantů Azure AD se nachází v rámci Evropské unie byl vyřešen. Však další agent konfigurace je nutná pro klienty Azure AD v EU. Podrobnosti najdete v tématu [část 3: Konfigurace místního agenta synchronizace](#Part 3: Configure the on-premises synchronization agent)

## <a name="managing-personal-data"></a>Správa osobních údajů

Workday zřizování řešení pro službu Active Directory vyžaduje aby byl nainstalován na serveru připojeném k doméně agent synchronizace a Tento agent vytváří protokoly v protokolu událostí Windows, který může obsahovat identifikovatelné osobní údaje.

## <a name="next-steps"></a>Další postup

* [Zjistěte, jak kontrolovat protokoly a získat sestavy o zřizování aktivity](../manage-apps/check-status-user-account-provisioning.md)
* [Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Workday a Azure Active Directory](workday-tutorial.md)
* [Naučte se integrovat s Azure Active Directory dalším aplikacím SaaS](tutorial-list.md)

