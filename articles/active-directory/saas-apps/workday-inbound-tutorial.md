---
title: 'Kurz: Konfigurace Workday pro automatické zřizování uživatelů pomocí Azure Active Directory | Dokumentace Microsoftu'
description: Zjistěte, jak konfigurovat Azure Active Directory a automaticky zřizovat a rušit zřízení uživatelských účtů do Workday.
services: active-directory
author: cmmdesai
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
ms.author: chmutali
ms.openlocfilehash: 30354ddb010c22dabe5cd69373ae59daaf4a8b46
ms.sourcegitcommit: 96527c150e33a1d630836e72561a5f7d529521b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2018
ms.locfileid: "51346741"
---
# <a name="tutorial-configure-workday-for-automatic-user-provisioning-preview"></a>Kurz: Konfigurace Workday pro uživatele automatické zřizování (preview)

Cílem tohoto kurzu je zobrazit kroky, které je potřeba provést k importu pracovních profilů z Workday do Active Directory a Azure Active Directory, se volitelné zpětným zápisem e-mailové adresy k Workday.

## <a name="overview"></a>Přehled

[Služba zřizování uživatelů Azure Active Directory](../manage-apps/user-provisioning.md) integruje [Workday lidských zdrojů API](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) pro zřízení uživatelských účtů. Toto připojení používá Azure AD povolit následující uživatel pracovní postupy zřizování:

* **Zřizování uživatelů do služby Active Directory** -synchronizovat vybrané skupiny uživatelů z Workday do jedné nebo několika domén služby Active Directory.

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

## <a name="solution-architecture"></a>Architektura řešení

Tato část popisuje zřizování architekturu řešení pro běžné hybridní prostředí uživatelů začátku do konce. Existují dvě související postupy:

* **Autoritativní HR tok dat – z Workday do místní služby Active Directory:** v tomto toku pracovního procesu (jako je například nový Hires přenosy, ukončení) nejprve dojde k událostem v cloudu Workday HR tenanta a potom událost data budou téci do místní aktivní Adresáře prostřednictvím služby Azure AD a agentem zřizování. V závislosti na události to může vést k vytvoření/aktualizaci/povolení/zakázání operace ve službě AD.
* **E-mailu tok zpětný zápis – z místní služby Active Directory do Workday:** po vytvoření účtu se ve službě Active Directory, je synchronizovaný s Azure AD pomocí služby Azure AD Connect a je možné zpětně zapsat atribut e-mailové zdrojem je služba Active Directory do Workday.

![Přehled](./media/workday-inbound-tutorial/wd_overview.png)

### <a name="end-to-end-user-data-flow"></a>Tok dat uživatele začátku do konce

1. Tým HR vede Workday HCM transakce pracovního procesu (potencionálního/stěhovací společnost/odešlé jednotky nebo nové zaměstnance/přenosy nebo ukončení)
2. Služba zřizování Azure AD spouští plánované synchronizace identit z Workday HR a identifikuje změny, které je potřeba zpracovat pro synchronizaci s místním Active Directory.
3. Služba zřizování Azure AD s místními AAD Connect zřizování agenta vyvolá s datová část požadavku, který obsahuje operace vytvoření/aktualizaci/povolení/zakázání účtu AD.
4. Agent Azure AD Connect zřizování účtu služby používá k přidání/aktualizaci dat účtu AD.
5. Azure AD Connect / AD synchronizační modul spustí rozdílová synchronizace do o přijetí změn aktualizace ve službě AD.
6. Aktualizace služby Active Directory synchronizované s Azure Active Directory.
7. Pokud je nakonfigurovaný konektor pro Workday zpětný zápis, je zpětný zápis atribut e-mailové do Workday, podle odpovídající atribut používá.


## <a name="planning-your-deployment"></a>Plánování nasazení

Před zahájením integraci Workday, zkontrolujte následující požadavky a přečtěte si následující pokyny týkající se tak, aby odpovídaly vaší aktuální architektura služby Active Directory a zřizování uživatelů požadavky pomocí řešení poskytuje služba Azure Active Directory.

### <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu se předpokládá, že máte následující položky:

* Platné předplatné s přístupem globální správce Azure AD Premium P1
* Implementace klienta Workday pro účely testování a integrace
* Oprávnění správce ve Workday a vytvořte uživatele systému integrace, provést změny k testování daty o zaměstnancích pro účely testování
* Pro zřizování uživatelů služby Active Directory, server s Windows serverem 2012 nebo vyšší s využitím .NET 4.7 + modulu runtime se vyžaduje pro hostování [místní agentem zřizování](https://go.microsoft.com/fwlink/?linkid=847801)
* [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) synchronizace mezi službami Active Directory a Azure AD

### <a name="planning-considerations"></a>Aspekty plánování

Azure AD poskytuje širokou škálu konektorů, které vám pomůžou vyřešit zřizování a správě identit. životní cyklus z Workday Active Directory, Azure AD, aplikacím SaaS a zřizování. Které funkce můžete používat a jak nastavit řešení se budou lišit v závislosti na prostředí a požadavky vaší organizace. Jako první krok trvat akcie o tom, kolik z následujících akcí jsou k dispozici a nasazené ve vaší organizaci:

* Kolik doménových struktur Active Directory se používá?
* Kolik domény služby Active Directory se používá?
* Kolik Active Directory organizační jednotky (OU) se používají?
* Kolik tenantů Azure Active Directory se používá?
* Existují uživatelé, kteří potřebují zřízené služby Active Directory a Azure Active Directory (například "hybridní" uživatelé)?
* Existují uživatelé, kteří potřebují zřízené služby Azure Active Directory, ale ne Active Directory (například "výhradně cloudový" uživatelé)?
* Potřeba e-mailové adresy uživatele na možné zpětně zapsat do Workday?

Jakmile budete mít odpovědi na tyto otázky, můžete naplánovat vaši Workday zřizování nasazení podle pokynů níže.

#### <a name="planning-deployment-of-aad-connect-provisioning-agent"></a>Plánování nasazení agenta zřizování služby AAD Connect

Workday k zřizování uživatelů AD řešení vyžaduje nasazení jeden nebo více zřizování agentů na serverech se systémem Windows 2012 R2 nebo novější s minimálně 4 GB paměti RAM a .NET 4.7 + modulu runtime. Před instalací agenta zřizování musí vzít v úvahu následující aspekty:

* Zkontrolujte, zda serveru hostitele se spuštěným agentem zřizování má přístup k síti do cílové AD domény
* Průvodce konfigurací agenta zřizování agenta zaregistruje v nástroji vašeho tenanta Azure AD a proces registrace vyžaduje přístup k *. msappproxy.net na portu 8082. Ujistěte se, že odchozí pravidla brány firewall jsou na místě, aby tato komunikace.
* Zřizování Agent používá ke komunikaci s místní účet služby AD domény. Před instalací agenta doporučujeme vytvořit účet služby oprávnění ke čtení a zápis vlastnosti uživatele a heslo, které, nemá prošlou platnost.  
* Během konfigurace zřizování agenta můžete vybrat, řadiče domény, které by měl zpracovat žádosti o zřízení. Pokud máte několik řadičů domény geograficky distribuované, nainstalujte agenta zřizování ve stejné lokalitě jako vašich řadičů domény upřednostňované ke zlepšení spolehlivosti a výkonu – ucelené řešení
* Pro zajištění vysoké dostupnosti můžete nasadit více než jednoho agenta pro zřizování a zaregistrujte ho ke zpracování stejné sady místních domén AD.

> [!IMPORTANT]
> V produkčním prostředí společnost Microsoft doporučuje, abyste měli aspoň 3 zřizování agentů nakonfigurovaných s vašeho tenanta Azure AD pro zajištění vysoké dostupnosti.

#### <a name="selecting-provisioning-connector-apps-to-deploy"></a>Výběr zřizování aplikací konektor k nasazení

Při integraci Workday a služby Active Directory, existuje více zdrojové a cílové systémů, aby bylo považováno za:

| Zdrojový systém | Cílový systém | Poznámky |
| ---------- | ---------- | ---------- |
| Pracovní den | Domény služby Active Directory | Každá doména je považován za odlišné cílový systém |
| Pracovní den | Azure AD tenant | Podle potřeby uživatelů jenom cloudu |
| Doménová struktura Active Directory | Azure AD tenant | Tento tok zařizuje služba AAD Connect ještě dnes |
| Azure AD tenant | Pracovní den | Pro zpětný zápis e-mailových adres |

Pro usnadnění pracovní postupy zřizování Workday od služby Active Directory, Azure AD poskytuje více zřizování aplikací konektoru, které lze přidat z Galerie aplikací Azure AD:

![Galerie aplikací AAD](./media/workday-inbound-tutorial/wd_gallery.png)

* **WORKDAY do Active Directory zřizování** – tato aplikace usnadňuje účet zřizování uživatelů z Workday na jedinou doménu služby Active Directory. Pokud máte více domén, můžete přidat jednu instanci této aplikace v galerii aplikací Azure AD pro každou doménu služby Active Directory je potřeba zřídit.

* **K Azure AD zřizování WORKDAY** – AAD Connect je nástroj, který se má použít k synchronizaci uživatelů do Azure Active Directory, tato aplikace je možné pro usnadnění zřizování výhradně cloudový uživatelů z Workday do jednoho Azure Active Directory Tenanta Active Directory.

* **Zpětný zápis WORKDAY** – tato aplikace umožňuje zpětný zápis e-mailové adresy uživatele ze služby Azure Active Directory do Workday.

> [!TIP]
> Pravidelné "Workday" aplikace se používá pro nastavení jednotné přihlašování mezi Workday a Azure Active Directory. 

#### <a name="determine-workday-to-ad-user-attribute-mapping-and-transformations"></a>Určení Workday mapování atributů uživatele AD a transformace

Před konfigurací zřizování uživatelů k doméně služby Active Directory, zvažte následující otázky. Odpovědi na tyto otázky určí, jak filtry oborů a mapování atributů musí být nastavena.

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



Jak nastavit a nakonfigurovat tyto speciální zřizování aplikací konektoru je předmětem zbývající části tohoto kurzu. Aplikace, které provedete konfiguraci bude záviset na systémy, které je potřeba zřídit a kolik domény služby Active Directory a Azure AD tenantů se ve vašem prostředí.



## <a name="configure-integration-system-user-in-workday"></a>Konfigurace uživatele systému integrace ve Workday

Běžné požadavky všech konektorů zřizování Workday je, že se že vyžadují přihlašovací údaje pro integraci Workday systémový účet pro připojení k rozhraní API Workday lidských zdrojů. Tato část popisuje, jak vytvořit uživatele systému integrace ve Workday.

> [!NOTE]
> Je možné obejít tento postup a místo toho použijte účet globálního správce Workday jako systémový účet integrace. To může fungovat pro ukázky, ale nedoporučuje se používat pro nasazení v produkčním prostředí.

### <a name="create-an-integration-system-user"></a>Vytvořit uživatele systému integrace

**Chcete-li vytvořit uživatele systému integrace:**

1. Přihlaste se pomocí účtu správce tenanta Workday. V **aplikací Workday**, zadejte vytvořit uživatel do vyhledávacího pole a potom klikněte na tlačítko **vytvořit uživatele systému integrace**.

    ![Vytvořit uživatele](./media/workday-inbound-tutorial/wd_isu_01.png "pro vytvoření uživatele")
2. Dokončení **vytvořit uživatele systému integrace** úkol zadáním uživatelského jména a hesla pro nového uživatele systému integrace.  
 * Nechte **vyžadují nové heslo při dalším přihlašování** možnost není zaškrtnuto, protože tento uživatel bude protokolování prostřednictvím kódu programu.
 * Nechte **minut časového limitu relace** s výchozí hodnotou 0, což zabrání relace uživatele vypršení časového limitu předčasně ukončen.
 * Vyberte možnost **povolit relace uživatelského rozhraní** poskytuje další úroveň zabezpečení, který brání uživatele s heslem systému integrace protokolování do Workday. 

    ![Vytvořte uživatele systému integrace](./media/workday-inbound-tutorial/wd_isu_02.png "vytvořte uživatele systému integrace")

### <a name="create-a-security-group"></a>Vytvořte skupinu zabezpečení
V tomto kroku vytvoříte skupinu zabezpečení systému integrace bez omezení ve Workday a přiřadit uživatele systému integrace vytvořili v předchozím kroku do této skupiny.

**Vytvoření skupiny zabezpečení:**

1. Zadejte do vyhledávacího pole vytvořte skupinu zabezpečení a potom klikněte na **vytvořit skupinu zabezpečení**.

    ![Skupiny CreateSecurity](./media/workday-inbound-tutorial/wd_isu_03.png "CreateSecurity skupiny")
2. Dokončení **vytvořit skupinu zabezpečení** úloh.  
   * Vyberte **skupiny zabezpečení systému integrace (neomezená)** z **typ části klienty zabezpečení skupiny** rozevíracího seznamu.

    ![Skupiny CreateSecurity](./media/workday-inbound-tutorial/wd_isu_04.png "CreateSecurity skupiny")

3. Po úspěšném vytvoření skupiny zabezpečení, zobrazí se stránka, kde můžete přiřadit členy do skupiny zabezpečení. Přidání nového uživatele systému integrace do této skupiny zabezpečení a vyberte obor příslušné organizace.
![Upravit skupinu zabezpečení](./media/workday-inbound-tutorial/wd_isu_05.png "upravit skupinu zabezpečení")
 
### <a name="configure-domain-security-policy-permissions"></a>Konfigurace oprávnění zásad zabezpečení domény
V tomto kroku budete "zabezpečení domény" udělit oprávnění zásad pro pracovníka data do skupiny zabezpečení.

**Jak nakonfigurovat oprávnění zásad zabezpečení domény:**

1. Zadejte **konfigurace zabezpečení domény** ve vyhledávacím poli a pak kliknutím na odkaz **sestava konfigurace zabezpečení domény**.  

    ![Zásady zabezpečení domény](./media/workday-inbound-tutorial/wd_isu_06.png "zásady zabezpečení domény")  
2. V **domény** textového pole hledání z následujících domén a přidat je do filtru jeden po druhém.  
   * *Zřizování externího účtu*
   * *Dat pracovního procesu: Veřejný pracovní sestavy*
   * *Data osob: Kontaktní informace o práci*
   * *Data pracovních procesů: Všechny pozice*
   * *Pracovní Data: Personální informace o aktuální*
   * *Data pracovních procesů: Název firmy na pracovní profil*
 
    ![Zásady zabezpečení domény](./media/workday-inbound-tutorial/wd_isu_07.png "zásady zabezpečení domény")  

    ![Zásady zabezpečení domény](./media/workday-inbound-tutorial/wd_isu_08.png "zásady zabezpečení domény") 

    Klikněte na **OK**.

3. V sestavě, která se zobrazí, vyberte tři tečky (...), která se zobrazí vedle **externí zřizování účtů** a klikněte na možnost nabídky **domény -> Upravit oprávnění zásad zabezpečení**

    ![Zásady zabezpečení domény](./media/workday-inbound-tutorial/wd_isu_09.png "zásady zabezpečení domény")  

4. Na **upravit oprávnění zásad zabezpečení domény** stránky, přejděte dolů do části **oprávnění integrace**. Klikněte na znaménko "+" Přidat skupinu systému integrace do seznamu skupiny zabezpečení s **získat** a **umístit** integrace oprávnění.

    ![Upravit oprávnění](./media/workday-inbound-tutorial/wd_isu_10.png "upravit oprávnění")  

5. Klikněte na znaménko "+" Přidat skupinu systému integrace do seznamu skupiny zabezpečení s **získat** a **umístit** integrace oprávnění.

    ![Upravit oprávnění](./media/workday-inbound-tutorial/wd_isu_11.png "upravit oprávnění")  

6. Opakujte kroky 3 až 5 výše pro všechny zbývající zásady zabezpečení:

   | Operace | Zásady zabezpečení domény |
   | ---------- | ---------- | 
   | Operace GET a Put | Dat pracovního procesu: Veřejný pracovní sestavy |
   | Operace GET a Put | Data osob: Kontaktní informace o práci |
   | Získat | Data pracovních procesů: Všechny pozice |
   | Získat | Pracovní Data: Personální informace o aktuální |
   | Získat | Data pracovních procesů: Název firmy na pracovní profil |

### <a name="configure-business-process-security-policy-permissions"></a>Konfigurace oprávnění zásad zabezpečení obchodního procesu
V tomto kroku budete udělit oprávnění zásad pro pracovníka data do skupiny zabezpečení "zabezpečení obchodních procesů". Toto je nezbytné k nastavení konektoru aplikace zpětný zápis Workday. 

**Jak nakonfigurovat oprávnění zásad zabezpečení obchodní proces:**

1. Zadejte **obchodní proces zásad** ve vyhledávacím poli a pak kliknutím na odkaz **upravit zásady zabezpečení obchodní proces** úloh.  

    ![Firemní zásady zabezpečení procesu](./media/workday-inbound-tutorial/wd_isu_12.png "firemní zásady zabezpečení procesu")  

2. V **typ obchodního procesu** textového pole hledání *kontakt* a vyberte **kontakt změnu** obchodních procesů a klikněte na tlačítko **OK**.

    ![Firemní zásady zabezpečení procesu](./media/workday-inbound-tutorial/wd_isu_13.png "firemní zásady zabezpečení procesu")  

3. Na **upravit zásady zabezpečení obchodní proces** stránce, přejděte **udržovat kontaktní údaje (webová služba)** oddílu.

    ![Firemní zásady zabezpečení procesu](./media/workday-inbound-tutorial/wd_isu_14.png "firemní zásady zabezpečení procesu")  

4. Vyberte a přidejte novou skupinu zabezpečení systému integrace do seznamu skupin zabezpečení, které lze iniciovat žádosti webové služby. Klikněte na **provádí**. 

    ![Firemní zásady zabezpečení procesu](./media/workday-inbound-tutorial/wd_isu_15.png "firemní zásady zabezpečení procesu")  

 
### <a name="activate-security-policy-changes"></a>Aktivace změny zásad zabezpečení

**Aktivace změny zásad zabezpečení:**

1. Zadejte aktivovat do vyhledávacího pole a potom klikněte na odkaz **aktivovat čekající změny zásad zabezpečení**.

    ![Aktivovat](./media/workday-inbound-tutorial/wd_isu_16.png "aktivovat") 
2. Začátek úlohy aktivovat čekající změny zásad zabezpečení pro účely auditování zadejte komentář a potom klikněte na **OK**. 

    ![Aktivovat čekající zabezpečení](./media/workday-inbound-tutorial/wd_isu_17.png "aktivovat čekající zabezpečení")  
1. Dokončení úlohy na další obrazovce zaškrtnutím políčka **potvrdit**a potom klikněte na tlačítko **OK**.

    ![Aktivovat čekající zabezpečení](./media/workday-inbound-tutorial/wd_isu_18.png "aktivovat čekající zabezpečení")  

## <a name="configuring-user-provisioning-from-workday-to-active-directory"></a>Konfigurace zřizování uživatelů z Workday do Active Directory

Postupujte podle těchto pokynů ke konfiguraci zřizování z Workday pro každou doménu služby Active Directory v rámci oboru integraci vašich uživatelský účet.

### <a name="part-1-install-and-configure-on-premises-provisioning-agents"></a>Část 1: Instalace a konfigurace místní zřizování agentů

Ke zřízení do místní služby Active Directory, musí na serveru, který má .NET 4.7 + nainstaluje agent rozhraní Framework a síti přístup k požadované domén služby Active Directory.

> [!TIP]
> Verze rozhraní .NET framework můžete zkontrolovat na vašem serveru pomocí pokynů uvedených [tady](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed).
> Pokud server nemá .NET 4.7 nebo vyšší, můžete ho stáhnout z [tady](https://support.microsoft.com/help/3186497/the-net-framework-4-7-offline-installer-for-windows).  

Po nasazení .NET 4.7 + si můžete stáhnout **[zde agentem zřizování v místním](https://go.microsoft.com/fwlink/?linkid=847801)** a postupujte podle kroků uvedených níže a dokončete tak konfiguraci agenta.

1. Přihlaste se k Windows serveru, kam chcete nainstalovat nového agenta.
2. Spusťte instalační program agenta zřizování, souhlas s podmínkami a kliknutím na **nainstalovat** tlačítko.
![Nainstalujte obrazovky](./media/workday-inbound-tutorial/pa_install_screen_1.png "nainstalovat obrazovky")

3. Po dokončení instalace, průvodce se spustí a zobrazí se **Azure AD Connect** obrazovky. Klikněte na **ověřit** tlačítko pro připojení k vaší instanci Azure AD.
![Azure AD Connect](./media/workday-inbound-tutorial/pa_install_screen_2.png "připojení Azure AD")

4. Ověření k vaší instanci Azure AD pomocí přihlašovacích údajů globálního správce. 
![Správce ověřování](./media/workday-inbound-tutorial/pa_install_screen_3.png "Správce ověřování")

5. Po úspěšném ověření přes Azure AD, se zobrazí **připojení služby Active Directory** obrazovky. V tomto kroku, zadejte název domény AD a klikněte na **přidat adresář** tlačítko.
![Přidejte adresář](./media/workday-inbound-tutorial/pa_install_screen_4.png "přidat adresář")

6. Můžete teď výzva k zadání přihlašovacích údajů potřebných pro připojení k doméně AD. Na stejné obrazovce, můžete použít **vyberte prioritu řadič domény** k určení řadiče domény, které agent by měly používat pro odesílání žádosti o zřízení.
![Přihlašovací údaje pro doménu](./media/workday-inbound-tutorial/pa_install_screen_5.png "přihlašovací údaje pro doménu")

7. Po dokončení konfigurace domény, instalační program zobrazí seznam nakonfigurované domény. Na této obrazovce můžete opakovat krok #5 a 6 # pro přidání dalších domén nebo klikněte na **Další** přejděte na registraci agenta. 
![Nakonfigurované domény](./media/workday-inbound-tutorial/pa_install_screen_6.png "nakonfigurované domény")

   > [!NOTE]
   > Pokud máte více domén AD (např. na.contoso.com, emea.contoso.com) a pak přidejte všechny domény jednotlivě do seznamu. Přidání pouze nadřazené domény (např. contoso.com) není dostatečná a doporučuje registraci každou podřízenou doménu s agentem. 

8. Zkontrolujte podrobnosti o konfiguraci a klikněte na **potvrdit** k registraci agenta. 
![Zkontrolujte obrazovky](./media/workday-inbound-tutorial/pa_install_screen_7.png "potvrďte obrazovky")

9. Průvodce konfigurací zobrazí průběh registrace agenta.
![Registrace agenta](./media/workday-inbound-tutorial/pa_install_screen_8.png "registrace agenta")

10. Po úspěšné registraci agenta můžete kliknout na **ukončit** ukončíte průvodce. 
![Ukončete obrazovky](./media/workday-inbound-tutorial/pa_install_screen_9.png "ukončit obrazovky")

11. Ověření instalace agenta a ujistěte se, že běží tak, že otevřete modul Snap-In Editor "Služby" a vyhledat službu s názvem "Microsoft Azure AD Connect zřizování agenta" ![služby](./media/workday-inbound-tutorial/services.png)  


**Řešení potíží s agentem**

[Protokolu událostí Windows](https://technet.microsoft.com/library/cc722404(v=ws.11).aspx) počítače hostujícího agenta v systému Windows Server obsahuje události pro všechny operace prováděné tímto agentem. Chcete-li zobrazit tyto události:
    
1. Otevřít **Eventvwr.msc**.
2. Vyberte **protokoly Windows > aplikace**.
3. Zobrazit všechny události zapsané podle zdroje **AAD. Connect.ProvisioningAgent**. 
4. Zkontrolujte chyby a upozornění.

    
### <a name="part-2-adding-the-provisioning-connector-app-and-creating-the-connection-to-workday"></a>Část 2: Přidání zřizování konektoru aplikace a vytvoření připojení k Workday

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

   * **Doménové struktury služby Active Directory -** "Name" doménu služby Active Directory, je registrovaný u agenta. Toto je obvykle řetězec jako: *contoso.com*

   * **Kontejner služby Active Directory -** zadejte rozlišující název kontejneru, kde vytváříte uživatelské účty ve výchozím nastavení agenta. 
        Příklad: *organizační jednotky = standardní uživatelé, OU = Users, DC = contoso, DC = test*
> [!NOTE]
> Toto nastavení pouze dodává do hry pro vytváření účtů uživatelů, pokud *parentDistinguishedName* atribut není nakonfigurovaný v mapování atributů. Toto nastavení nepoužívají pro hledání uživatele nebo aktualizace operace. Sub stromu celé doméně spadá do rozsahu operace vyhledávání.
   * **E-mailové oznámení –** zadejte svou e-mailovou adresu a zaškrtněte políčko "Odeslat e-mail, pokud dojde k selhání".
> [!NOTE]
> Služba zřizování Azure AD odešle e-mailové oznámení, pokud úloha zřizování přejde do [karantény](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning#quarantine) stavu.

   * Klikněte na tlačítko **Test připojení** tlačítko. Pokud je test připojení úspěšný, klikněte na tlačítko **Uložit** tlačítko v horní části. Pokud selže, zkontrolujte, že jsou platné přihlašovací údaje Workday a přihlašovací údaje služby AD na instalaci agenta je nakonfigurované.

![portál Azure](./media/workday-inbound-tutorial/wd_1.png)

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
| **Nahraďte(Mid(Nahraďte(\[UserID\], , "(\[\\\\/\\\\\\\\\\\\\[\\\\\]\\\\:\\\\;\\\\|\\\\=\\\\,\\\\+\\\\\*\\\\?\\\\&lt;\\\\&gt;\])", , "", , ), 1, 20), , "([\\\\.)\*\$](file:///\\.)*$)", , "", , )**      |    SAMAccountName            |     |         Zapisovat pouze při vytváření |
| **Přepínač (\[aktivní\],, "0", "PRAVDA", "1", "Nepravda")** |  accountDisabled      |     | Vytváření a aktualizace |
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

**Ke konfiguraci konektoru Workday zpětného zápisu:**

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

    ![WORKDAY Studio](./media/workday-inbound-tutorial/wdstudio1.png)

6. Nastavte **umístění** pole `https://IMPL-CC.workday.com/ccx/service/TENANT/Human_Resources`, ale "IMPL-CC" nahraďte skutečným typ instance a "TENANT" s vaším jménem skutečné tenanta.

7. Nastavte **operace** k **Get_Workers**

8.  Klikněte na malé **konfigurace** odkaz podokna požadavku nebo odpovědi k nastavení pověření Workday. Zkontrolujte **ověřování**a pak zadejte uživatelské jméno a heslo pro váš účet integrace systému Workday. Je potřeba formátovat jako uživatelské jméno name@tenanta nechat **WS-Security UsernameToken** zaškrtnutou možnost.

    ![WORKDAY Studio](./media/workday-inbound-tutorial/wdstudio2.png)

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

    ![WORKDAY Studio](./media/workday-inbound-tutorial/wdstudio3.png)

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

    ![WORKDAY Studio](./media/workday-inbound-tutorial/wdstudio_aad1.png)

6. Přejděte do dolní části seznamu atributů k umístění vstupních polí.

7. Pro **název**, zadejte zobrazovaný název pro atribut.

8. Pro **typ**, vyberte typ, který odpovídajícím způsobem odpovídá atributu (**řetězec** klienty jsou nejčastěji).

9. Pro **výraz rozhraní API**, zadejte výraz XPath, který jste zkopírovali z Workday Studio. Příklad: `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`

10. Vyberte **přidejte atribut**.

    ![WORKDAY Studio](./media/workday-inbound-tutorial/wdstudio_aad2.png)

11. Vyberte **Uložit** výše a potom **Ano** do dialogového okna. Uzavření obrazovky mapování atributů, pokud je stále otevřen.

12. Zpět na hlavní **zřizování** kartu, vyberte možnost **synchronizovat pracovníků místní** (nebo **synchronizaci pracovních procesů do Azure AD**) znovu.

13. Vyberte **přidat nové mapování**.

14. Nový atribut by se měla objevit v **zdrojový atribut** seznamu.

15. Podle potřeby přidejte mapování pro nový atribut.

16. Až budete hotovi, nezapomeňte nastavit **stavu zřizování** zpět **na** a uložit.

## <a name="known-issues"></a>Známé problémy

* Zápis dat do atributu thumbnailphoto nastavuje uživatele v místní službě Active Directory se momentálně nepodporuje.

* "Workday do služby Azure AD" konektor není aktuálně podporovaný v tenantů Azure AD, kde je povolena AAD Connect.  

## <a name="managing-personal-data"></a>Správa osobních údajů

Workday zřizování řešení pro službu Active Directory vyžaduje aby byl nainstalován na serveru připojeném k doméně agent synchronizace a Tento agent vytváří protokoly v protokolu událostí Windows, který může obsahovat identifikovatelné osobní údaje.

## <a name="next-steps"></a>Další postup

* [Zjistěte, jak kontrolovat protokoly a získat sestavy o zřizování aktivity](../manage-apps/check-status-user-account-provisioning.md)
* [Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Workday a Azure Active Directory](workday-tutorial.md)
* [Naučte se integrovat s Azure Active Directory dalším aplikacím SaaS](tutorial-list.md)

