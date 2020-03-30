---
title: 'Kurz: Konfigurace příchozího zřizování faktorů úspěchu ve službě Azure Active Directory | Dokumenty společnosti Microsoft'
description: Zjistěte, jak nakonfigurovat příchozí zřizování z SuccessFactors
services: active-directory
author: cmmdesai
documentationcenter: na
manager: jodadzie
ms.assetid: 1ff90231-1312-463e-8949-7d976e433fc3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/05/2019
ms.author: chmutali
ms.openlocfilehash: d9317a68c8967fbe0728e8c47e59dd33367c6163
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249682"
---
# <a name="tutorial-configure-sap-successfactors-to-active-directory-user-provisioning-preview"></a>Kurz: Konfigurace sap successfactors pro zřizování uživatelů služby Active Directory (Preview)
Cílem tohoto kurzu je zobrazit kroky, které je třeba provést k zajištění uživatelů z SuccessFactors Employee Central do služby Active Directory (AD) a Azure AD, s volitelným zpětným zápisem e-mailové adresy successfactors. Tato integrace je ve verzi Public Preview a podporuje načítání více než [70+ uživatelských atributů](../app-provisioning/sap-successfactors-attribute-reference.md) z SuccessFactors Employee Central.

>[!NOTE]
>Tento kurz použijte, pokud uživatelé, které chcete zřídit z SuccessFactors potřebují místní účet služby AD a volitelně účet Azure AD. Pokud uživatelé z SuccessFactors potřebují jenom účet Azure AD (uživatelé pouze pro cloud), přečtěte si prosím kurz o [konfiguraci SAP SuccessFactors pro](sap-successfactors-inbound-provisioning-cloud-only-tutorial.md) zřizování uživatelů Azure AD. 


## <a name="overview"></a>Přehled

[Služba zřizování uživatelů služby Azure Active Directory](../app-provisioning/user-provisioning.md) se integruje s [centrálou zaměstnanců SuccessFactors Za](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html) účelem správy životního cyklu identity uživatelů. 

Pracovní postupy zřizování uživatelů SuccessFactors podporované službou zřizování uživatelů Azure AD umožňují automatizaci následujících lidských prostředků a scénářů správy životního cyklu identit:

* **Nábor nových zaměstnanců** – když je do SuccessFactors přidán nový zaměstnanec, automaticky se vytvoří uživatelský účet ve službě Active Directory, azure active directory a volitelně office 365 a [dalších aplikacích SaaS podporovaných službou Azure AD](../app-provisioning/user-provisioning.md)s zpětným zápisem e-mailové adresy na SuccessFactors.

* **Aktualizace atributů a profilů zaměstnance** – když se záznam zaměstnance aktualizuje v faktorech úspěchu (například jejich jméno, titul nebo správce), jejich uživatelský účet se automaticky aktualizuje ve službě Active Directory, službě Azure Active Directory a volitelně v Office 365 a [dalších aplikacích SaaS podporovaných službou Azure AD](../app-provisioning/user-provisioning.md).

* **Ukončení zaměstnanců** – když je zaměstnanec ukončen v SuccessFactors, jeho uživatelský účet se automaticky deaktivuje ve službě Active Directory, Azure Active Directory a volitelně Office 365 a [dalších aplikacích SaaS podporovaných službou Azure AD](../app-provisioning/user-provisioning.md).

* Opětovné přijetí zaměstnanců – když je zaměstnanec znovu přijat v SuccessFactors, jeho starý účet může být automaticky znovu aktivován nebo znovu zřízen (v závislosti na vašich preferencích) do **služby** Active Directory, Služby Azure Active Directory a volitelně Office 365 a [dalších aplikací SaaS podporovaných službou Azure AD](../app-provisioning/user-provisioning.md).

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Pro koho je toto zřizovací řešení uživatele nejvhodnější?

Toto řešení zřizování uživatelů služby Active Directory je ideální pro:

* Organizace, které si přejí předem vytvořené cloudové řešení pro zřizování uživatelů SuccessFactors

* Organizace, které vyžadují přímé zřizování uživatelů z SuccessFactors do služby Active Directory

* Organizace, které vyžadují, aby uživatelé byli zřízeni pomocí dat získaných z [SuccessFactors Employee Central (EC)](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html)

* Organizace, které vyžadují připojení, přesunutí a ponechání uživatelů synchronizovat s jednou nebo více doménovými strukturami, doménami a organizačními uživateli služby Active Directory pouze na základě informací o změnách zjištěných v centru [zaměstnanců SuccessFactors (ES)](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html)

* Organizace používající Office 365 pro e-mail

## <a name="solution-architecture"></a>Architektura řešení

Tato část popisuje architekturu řešení zřizování koncových uživatelů pro běžná hybridní prostředí. Existují dva související toky:

* **Autoritativní tok dat lidských zdrojů – od SuccessFactors až po místní službu Active Directory:** V tomto události toku pracovního procesu (například nové najímá, převody, ukončení) nejprve dojít v cloudu SuccessFactors Employee Central a potom data událostí toky do místní hospo- active directory prostřednictvím Azure AD a zřizování agenta. V závislosti na události může vést k vytvoření/aktualizaci/povolení/zakázání operací ve službě AD.
* **Tok zpětného zápisu e-mailu – z místního adresáře Active Directory do successuFaktory:** Po dokončení vytvoření účtu ve službě Active Directory se synchronizuje s Azure AD prostřednictvím synchronizace Azure AD Connect a atribut e-mailu lze zapsat zpět na SuccessFactors.

  ![Přehled](./media/sap-successfactors-inbound-provisioning/sf2ad-overview.png)

### <a name="end-to-end-user-data-flow"></a>Tok dat koncových uživatelů

1. Hr tým provádí pracovní transakce (Truhláři/Stěhováci/Opouštějící nebo Nové najímá/převádí/ukončení) v SuccessFactors Employee Central
2. Služba zřizování Azure AD spouští naplánované synchronizace identit z SuccessFactors EC a identifikuje změny, které je třeba zpracovat pro synchronizaci s místní službou Active Directory.
3. Služba zřizování Azure AD vyvolá místního agenta azure ad connect provisioning agenta s datovou částí požadavku obsahující operace vytvoření/aktualizace/povolení/zakázání účtu Služby AD.
4. Agent zřizování Připojení Azure AD používá účet služby k přidání nebo aktualizaci dat účtu služby AD.
5. Modul Synchronizace připojení Azure AD spustí synchronizaci delta pro vyžádat aktualizace ve službě AD.
6. Aktualizace služby Active Directory se synchronizují se službou Azure Active Directory.
7. Pokud [successfactors writeback aplikace](sap-successfactors-writeback-tutorial.md) je nakonfigurován, zapíše zpět e-mail atribut SuccessFactors, na základě odpovídající atribut použít.

## <a name="planning-your-deployment"></a>Plánování nasazení

Konfigurace zřizování uživatelů řízených cloudovým hr z SuccessFactors do AD vyžaduje značné plánování zahrnující různé aspekty, jako jsou:
* Nastavení agenta zřizování Azure AD Connect 
* Počet successů faktorů pro zřizování aplikací uživatelů služby AD k nasazení
* Odpovídající ID, mapování atributů, transformace a oborové filtry

Komplexní pokyny k těmto tématům najdete v [plánu nasazení cloudových hr.](../app-provisioning/plan-cloud-hr-provision.md) 

## <a name="configuring-successfactors-for-the-integration"></a>Konfigurace faktorů úspěchu pro integraci

Běžným požadavkem všech konektorů zřizování SuccessFactors je, že vyžadují pověření účtu SuccessFactors se správnými oprávněními k vyvolání rozhraní API OData SuccessFactors. Tato část popisuje kroky k vytvoření účtu služby v SuccessFactors a udělit příslušná oprávnění. 

* [Vytvoření/identifikace uživatelského účtu rozhraní API v faktorech úspěchu](#createidentify-api-user-account-in-successfactors)
* [Vytvoření role oprávnění rozhraní API](#create-an-api-permissions-role)
* [Vytvoření skupiny oprávnění pro uživatele rozhraní API](#create-a-permission-group-for-the-api-user)
* [Udělit roli oprávnění skupině oprávnění](#grant-permission-role-to-the-permission-group)

### <a name="createidentify-api-user-account-in-successfactors"></a>Vytvoření/identifikace uživatelského účtu rozhraní API v faktorech úspěchu
Spolupracujte se svým týmem správce SuccessFactors nebo implementačním partnerem a vytvořte nebo identifikujte uživatelský účet v faktorech Úspěchu, který se použije k vyvolání rozhraní API OData. Přihlašovací údaje uživatelského jména a hesla tohoto účtu budou vyžadovány při konfiguraci zřizovacích aplikací ve službě Azure AD. 

### <a name="create-an-api-permissions-role"></a>Vytvoření role oprávnění rozhraní API

* Přihlaste se do SAP SuccessFactors pomocí uživatelského účtu, který má přístup do Centra pro správu.
* Vyhledejte *položku Spravovat role oprávnění*a ve výsledcích hledání vyberte Spravovat role **oprávnění.**
  ![Správa rolí oprávnění](./media/sap-successfactors-inbound-provisioning/manage-permission-roles.png)
* V seznamu rolí oprávnění klepněte na **tlačítko Vytvořit nový**.
  > [!div class="mx-imgBorder"]
  > ![Vytvořit novou roli oprávnění](./media/sap-successfactors-inbound-provisioning/create-new-permission-role-1.png)
* Přidejte **název role** a **popis** nové role oprávnění. Název a popis by měl y označovat, že role je pro oprávnění k použití rozhraní API.
  > [!div class="mx-imgBorder"]
  > ![Podrobnosti role oprávnění](./media/sap-successfactors-inbound-provisioning/permission-role-detail.png)
* V části Nastavení oprávnění klikněte na **Oprávnění...**, potom posuňte seznam oprávnění dolů a klepněte na **příkaz Spravovat nástroje pro integraci**. Zaškrtněte políčko **Povolit správci přístup k rozhraní OData API prostřednictvím základního ověřování**.
  > [!div class="mx-imgBorder"]
  > ![Správa integračních nástrojů](./media/sap-successfactors-inbound-provisioning/manage-integration-tools.png)
* Posuňte se dolů ve stejném poli a vyberte **rozhraní API Employee Central .** Přidejte oprávnění, jak je znázorněno níže číst pomocí Rozhraní ODATA API a upravovat pomocí Rozhraní ODATA API. Pokud chcete použít stejný účet pro scénář Writeback to SuccessFactors, vyberte možnost úprav. 
  > [!div class="mx-imgBorder"]
  > ![Oprávnění ke čtení zápisu](./media/sap-successfactors-inbound-provisioning/odata-read-write-perm.png)

  >[!NOTE]
  >Úplný seznam atributů načtených touto zřizovací aplikací naleznete v [referenční příručce atributu SuccessFactors](../app-provisioning/sap-successfactors-attribute-reference.md)

* Klikněte na **Hotovo**. Klikněte na **Save Changes** (Uložit změny).

### <a name="create-a-permission-group-for-the-api-user"></a>Vytvoření skupiny oprávnění pro uživatele rozhraní API

* V Centru pro správu SuccessFactors vyhledejte *položku Správa skupin oprávnění*a ve výsledcích hledání vyberte **Spravovat skupiny oprávnění.**
  > [!div class="mx-imgBorder"]
  > ![Správa skupin oprávnění](./media/sap-successfactors-inbound-provisioning/manage-permission-groups.png)
* V okně Spravovat skupiny oprávnění klepněte na **tlačítko Vytvořit nový**.
  > [!div class="mx-imgBorder"]
  > ![Přidat novou skupinu](./media/sap-successfactors-inbound-provisioning/create-new-group.png)
* Přidejte název skupiny pro novou skupinu. Název skupiny by měl označovat, že skupina je pro uživatele rozhraní API.
  > [!div class="mx-imgBorder"]
  > ![Název skupiny oprávnění](./media/sap-successfactors-inbound-provisioning/permission-group-name.png)
* Přidejte členy do skupiny. Můžete například vybrat **uživatelské jméno** z rozbalovací nabídky Fond osob a potom zadat uživatelské jméno účtu rozhraní API, který bude použit pro integraci. 
  > [!div class="mx-imgBorder"]
  > ![Přidání členů skupiny](./media/sap-successfactors-inbound-provisioning/add-group-members.png)
* Chcete-li dokončit vytváření skupiny oprávnění, klepněte na **tlačítko Hotovo.**

### <a name="grant-permission-role-to-the-permission-group"></a>Udělit roli oprávnění skupině oprávnění

* V Centru pro správu SuccessFactors vyhledejte položku *Správa rolí oprávnění*a ve výsledcích hledání vyberte Spravovat role **oprávnění.**
* V **seznamu rolí oprávnění**vyberte roli, kterou jste vytvořili pro oprávnění k využití rozhraní API.
* V části **Udělit tuto roli...** klikněte na tlačítko **Přidat...**
* V rozevírací nabídce vyberte **Skupina oprávnění** a kliknutím na **Vybrat...** otevřete okno Skupiny a vyhledejte a vyberte skupinu vytvořenou výše. 
  > [!div class="mx-imgBorder"]
  > ![Přidat skupinu oprávnění](./media/sap-successfactors-inbound-provisioning/add-permission-group.png)
* Zkontrolujte udělení role oprávnění skupině oprávnění. 
  > [!div class="mx-imgBorder"]
  > ![Podrobnosti role oprávnění a skupiny](./media/sap-successfactors-inbound-provisioning/permission-role-group.png)
* Klikněte na **Save Changes** (Uložit změny).

## <a name="configuring-user-provisioning-from-successfactors-to-active-directory"></a>Konfigurace zřizování uživatelů z SuccessFactors do služby Active Directory

Tato část obsahuje postup pro zřizování uživatelských účtů z SuccessFactors do každé domény služby Active Directory v rámci vaší integrace.

* [Přidejte aplikaci konektor uzdu a stáhněte si agenta zřizování](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent)
* [Instalace a konfigurace místního agenta zřizování](#part-2-install-and-configure-on-premises-provisioning-agents)
* [Konfigurace připojení k successfactors a službě Active Directory](#part-3-in-the-provisioning-app-configure-connectivity-to-successfactors-and-active-directory)
* [Konfigurace mapování atributů](#part-4-configure-attribute-mappings)
* [Povolení a spuštění zřizování uživatelů](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent"></a>Část 1: Přidání aplikace konektoru zřizování a stažení agenta zřizování

**Postup konfigurace faktorů úspěchu pro zřizování služby Active Directory:**

1. Přejděte na <https://portal.azure.com>.

2. Na levém navigačním panelu vyberte **Azure Active Directory.**

3. Vyberte **podnikové aplikace**, potom **všechny aplikace**.

4. Vyberte **Přidat aplikaci**a vyberte kategorii **Vše.**

5. Vyhledejte **successfactors na Zřizování uživatelů služby Active Directory**a přidejte tuto aplikaci z galerie.

6. Po přidání aplikace a zobrazení obrazovky s podrobnostmi o aplikaci vyberte **Zřídit**

7. Změna **režimu** **zřizování** na **automatický**

8. Kliknutím na informační banner, který se zobrazí, stáhněte si zprostředkovatele zřizování. 
   > [!div class="mx-imgBorder"]
   > ![Stáhnout agenta](./media/sap-successfactors-inbound-provisioning/download-pa-agent.png "Stáhnout obrazovku agenta")


### <a name="part-2-install-and-configure-on-premises-provisioning-agents"></a>Část 2: Instalace a konfigurace místního agenta zřizování

Chcete-li zřídit službu Active Directory místně, musí být zřizovací agent nainstalován na serveru, který má rozhraní .NET 4.7.1+ Framework a síťový přístup k požadovaným doménám služby Active Directory.

> [!TIP]
> Verzi rozhraní .NET framework na serveru můžete zkontrolovat podle pokynů [uvedených zde](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed).
> Pokud server nemá nainstalovanou rozhraní .NET 4.7.1 nebo vyšší, můžete si ji stáhnout [zde](https://support.microsoft.com/help/4033342/the-net-framework-4-7-1-offline-installer-for-windows).  

Přeneste instalátor staženého agenta na hostitele serveru a dokončete konfiguraci agenta podle následujících kroků.

1. Přihlaste se k systému Windows Server, kde chcete nainstalovat nového agenta.

1. Spusťte instalační program zprostředkovatele zřizování, odsouhlaste podmínky a klikněte na tlačítko **Instalovat.**

   ![Obrazovka Instalace](./media/workday-inbound-tutorial/pa_install_screen_1.png "Obrazovka Instalace")
   
1. Po dokončení instalace se průvodce spustí a zobrazí se obrazovka **Connect Azure AD.** Kliknutím na tlačítko **Ověřit** se připojíte k instanci Azure AD.

   ![Připojení Azure AD](./media/workday-inbound-tutorial/pa_install_screen_2.png "Připojení Azure AD")
   
1. Ověřte se do instance Azure AD pomocí přihlašovacích údajů globálního správce.

   ![Admin Auth](./media/workday-inbound-tutorial/pa_install_screen_3.png "Admin Auth")

   > [!NOTE]
   > Přihlašovací údaje správce Azure AD se používají jenom pro připojení k tenantovi Azure AD. Agent neukládá pověření místně na serveru.

1. Po úspěšném ověření pomocí služby Azure AD se zobrazí obrazovka **Připojit službu Active Directory.** V tomto kroku zadejte název domény služby AD a klikněte na tlačítko **Přidat adresář.**

   ![Přidat adresář](./media/workday-inbound-tutorial/pa_install_screen_4.png "Přidat adresář")
  
1. Nyní budete vyzváni k zadání pověření potřebných pro připojení k doméně služby AD. Na stejné obrazovce můžete použít **prioritu select řadiče domény** k určení řadičů domény, které by měl agent používat pro odesílání požadavků na zřízení.

   ![Pověření domény](./media/workday-inbound-tutorial/pa_install_screen_5.png)
   
1. Po konfiguraci domény instalační program zobrazí seznam nakonfigurovaných domén. Na této obrazovce můžete opakovat krok #5 a #6 přidat další domény nebo kliknout na **další** a přejít k registraci agenta.

   ![Nakonfigurované domény](./media/workday-inbound-tutorial/pa_install_screen_6.png "Nakonfigurované domény")

   > [!NOTE]
   > Pokud máte více domén AD (např. na.contoso.com, emea.contoso.com), přidejte každou doménu jednotlivě do seznamu.
   > Přidání nadřazené domény (např. contoso.com) není dostatečné. Každou podřízenou doménu je nutné zaregistrovat u agenta.
   
1. Zkontrolujte podrobnosti konfigurace a kliknutím na **Potvrdit** zaregistrujte agenta.
  
   ![Obrazovka Potvrdit](./media/workday-inbound-tutorial/pa_install_screen_7.png "Obrazovka Potvrdit")
   
1. Průvodce konfigurací zobrazuje průběh registrace agenta.
  
   ![Registrace agenta](./media/workday-inbound-tutorial/pa_install_screen_8.png "Registrace agenta")
   
1. Jakmile je registrace agenta úspěšná, můžete kliknutím na **ukončit** průvodce ukončit.
  
   ![Výstupní obrazovka](./media/workday-inbound-tutorial/pa_install_screen_9.png "Výstupní obrazovka")
   
1. Ověřte instalaci agenta a ujistěte se, že je spuštěn otevřením modulu snap-in Služby a vyhledejte službu s názvem "Microsoft Azure AD Connect Provisioning Agent"
  
   ![Služby](./media/workday-inbound-tutorial/services.png)

### <a name="part-3-in-the-provisioning-app-configure-connectivity-to-successfactors-and-active-directory"></a>Část 3: V aplikaci zřizování nakonfigurujte připojení k SuccessFactors a Active Directory
V tomto kroku navážeme na webu Azure Portal připojení s SuccessFactors a Active Directory. 

1. Na webu Azure Portal se vraťte k aplikaci SuccessFactors to Active Directory User Provisioning App vytvořené v [části 1](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent)

1. Dokončete oddíl **Pověření správce** takto:

   * **Admin Uživatelské jméno** – Zadejte uživatelské jméno SuccessFactors API uživatelského účtu, s ID společnosti připojen. Má formát: **uživatelské\@jméno companyID**

   * **Admin heslo -** Zadejte heslo uživatelského účtu rozhraní API SuccessFactors. 

   * **Adresa URL klienta –** Zadejte název koncového bodu služby SuccessFactors OData API. Zadejte pouze název hostitele serveru bez http nebo https. Tato hodnota by měla vypadat **takto:<api-server-name>.successfactors.com**.

   * **Doménová struktura služby Active Directory -** "Název" domény služby Active Directory, jak je registrovánu u agenta. Pomocí rozevíracího souboru vyberte cílovou doménu pro zřizování. Tato hodnota je obvykle řetězec jako: *contoso.com*

   * **Kontejner služby Active Directory -** Zadejte dn kontejneru, kde by měl agent ve výchozím nastavení vytvářet uživatelské účty.
        Příklad: *OU=Uživatelé,DC=contoso,DC=com*
        > [!NOTE]
        > Toto nastavení se používá pouze pro vytváření uživatelských účtů, pokud atribut *parentDistinguishedName* není v mapování atributů nakonfigurován. Toto nastavení se nepoužívá pro operace vyhledávání uživatelů nebo aktualizace. Celý podstrom domény spadá do rozsahu operace vyhledávání.

   * **E-mail s oznámením –** Zadejte svou e-mailovou adresu a zaškrtněte políčko Odeslat e-mail, pokud dojde k selhání.
    > [!NOTE]
    > Služba zřizování Azure AD odešle e-mailové oznámení, pokud úloha zřizování přejde do [stavu karantény.](/azure/active-directory/manage-apps/application-provisioning-quarantine-status)

   * Klepněte na tlačítko **Testovat připojení.** Pokud je test připojení úspěšný, klepněte na tlačítko **Uložit** nahoře. Pokud se nezdaří, zkontrolujte, zda jsou platná pověření SuccessFactors a pověření služby AD nakonfigurovaná v nastavení agenta.
    >[!div class="mx-imgBorder"]
    >![Azure Portal](./media/sap-successfactors-inbound-provisioning/sf2ad-provisioning-creds.png)

   * Po úspěšném uložení pověření se v části **Mapování** zobrazí výchozí mapování **Synchronizovat uživatele SuccessFactors users do místní služby Active Directory.**

### <a name="part-4-configure-attribute-mappings"></a>Část 4: Konfigurace mapování atributů

V této části nakonfigurujete způsob toku uživatelských dat z faktorů Úspěchdo služby Active Directory.

1. Na kartě Zřizování v části **Mapování**klepněte na **tlačítko Synchronizovat uživatele SuccessFactors users do místní služby Active Directory**.

1. V poli **Obor zdrojového objektu** můžete výběrem sad uživatelů v SuccessFactors, které by měly být v oboru pro zřizování do služby AD, definováním sady filtrů založených na atributech. Výchozí obor je "všichni uživatelé v SuccessFactors". Příklady filtrů:

   * Příklad: Rozsah pro uživatele s personIdExternal mezi 1000000 a 2000000 (kromě 2000000)

      * Atribut: personIdExternal

      * Operátor: REGEX Shoda

      * Hodnota: (1[0-9][0-9][0-9][0-9][0-9][0-9])

   * Příklad: Pouze zaměstnanci a nepodmínění pracovníci

      * Atribut: ID zaměstnance

      * Operátor: NENÍ NULL

   > [!TIP]
   > Při konfiguraci zřizovací aplikace poprvé, budete muset otestovat a ověřit mapování atributů a výrazy a ujistěte se, že je vám požadovaný výsledek. Společnost Microsoft doporučuje používat filtry oborů v části **obor zdrojového objektu** k testování mapování s několika testovacími uživateli z SuccessFactors. Jakmile ověříte, že mapování funguje, můžete filtr odebrat nebo jej postupně rozbalit tak, aby zahrnoval více uživatelů.

   > [!CAUTION] 
   > Výchozí chování zřizovacího modulu je zakázat nebo odstranit uživatele, kteří přejdou mimo rozsah. To nemusí být žádoucí ve vaší SuccessFactors do integrace ad. Chcete-li přepsat toto výchozí chování, přečtěte si článek [Přeskočit odstranění uživatelských účtů, které jsou mimo rozsah.](../app-provisioning/skip-out-of-scope-deletions.md)
  
1. V poli **Akce cílového objektu** můžete globálně filtrovat, jaké akce se provádějí ve službě Active Directory. **Vytvořit** a **aktualizovat** jsou nejběžnější.

1. V části **Mapování atributů** můžete definovat, jak se jednotlivé atributy SuccessFactors mapují na atributy služby Active Directory.

  >[!NOTE]
  >Úplný seznam atributu SuccessFactors podporovaný aplikací naleznete v [části SuccessFactors Attribute Reference](../app-provisioning/sap-successfactors-attribute-reference.md)


1. Kliknutím na existující mapování atributů jej aktualizujte nebo kliknutím na **Přidat nové mapování** v dolní části obrazovky přidejte nová mapování. Mapování jednotlivých atributů podporuje tyto vlastnosti:

      * **Typ mapování**

         * **Direct** – zapíše hodnotu atributu SuccessFactors do atributu AD bez eserech.

         * **Konstanta** – do atributu AD napíše statickou hodnotu konstantního řetězce

         * **Výraz** – umožňuje zapsat vlastní hodnotu do atributu AD na základě jednoho nebo více atributů SuccessFactors. [Další informace naleznete v tomto článku o výrazech](../app-provisioning/functions-for-customizing-application-data.md).

      * **Atribut zdroj** – atribut uživatele z SuccessFactors

      * **Výchozí hodnota** – volitelné. Pokud má zdrojový atribut prázdnou hodnotu, mapování místo toho zapíše tuto hodnotu.
            Nejběžnější konfigurace je ponechat toto prázdné.

      * **Cílový atribut** – atribut uživatele ve službě Active Directory.

      * **Shoda objektů pomocí tohoto atributu** – Zda toto mapování by měly být použity k jednoznačné identifikaci uživatelů mezi SuccessFactors a Active Directory. Tato hodnota je obvykle nastavena v poli ID pracovníka pro Faktory úspěchu, které je obvykle mapováno na jeden z atributů ID zaměstnance ve službě Active Directory.

      * **Odpovídající priorita** – lze nastavit více odpovídajících atributů. Pokud existuje více, jsou vyhodnoceny v pořadí definovaném tímto polem. Jakmile je nalezena shoda, nejsou vyhodnoceny žádné další odpovídající atributy.

      * **Použít toto mapování**

         * **Vždy** – použít toto mapování na vytváření uživatelů a aktualizovat akce

         * **Pouze při vytváření** – toto mapování použít pouze u akcí vytvoření uživatele

1. Mapování můžete uložit kliknutím na **Uložit** v horní části oddílu Mapování atributů.

Po dokončení konfigurace mapování atributů můžete nyní [povolit a spustit službu zřizování uživatelů](#enable-and-launch-user-provisioning).

## <a name="enable-and-launch-user-provisioning"></a>Povolení a spuštění zřizování uživatelů

Po dokončení SuccessFactors zřizování konfigurace aplikací, můžete zapnout zřizování služby na webu Azure Portal.

> [!TIP]
> Ve výchozím nastavení při zapnutí zřizovací služby zahájí operace zřizování pro všechny uživatele v oboru. Pokud jsou chyby v mapování nebo SuccessFactors problémy s daty, pak úloha zřizování může selhat a přejít do stavu karantény. Chcete-li tomu zabránit, doporučujeme jako osvědčený postup konfiguraci filtru **oboru zdrojového objektu** a testování mapování atributů s několika testovacími uživateli před spuštěním úplné synchronizace pro všechny uživatele. Jakmile ověříte, že mapování funguje a dáváte vám požadované výsledky, můžete filtr odebrat nebo jej postupně rozšířit tak, aby zahrnoval více uživatelů.

1. Na kartě **Zřizování** nastavte **stav zřizování** **na Zapnuto**.

2. Klikněte na **Uložit**.

3. Tato operace spustí počáteční synchronizaci, která může trvat proměnný počet hodin v závislosti na tom, kolik uživatelů je v tenantovi SuccessFactors. Můžete zkontrolovat indikátor průběhu ke sledování průběhu cyklu synchronizace. 

4. Kdykoli zkontrolujte kartu **Protokoly auditu** na webu Azure Portal a zjistěte, jaké akce služba zřizování provedla. Protokoly auditu uvádí všechny jednotlivé události synchronizace prováděné zřizovací službou, například které uživatelé jsou čteny z SuccessFactors a následně přidány nebo aktualizovány do služby Active Directory. 

5. Po dokončení počáteční synchronizace sepíše souhrnnou zprávu auditu na kartu **Zřizování,** jak je znázorněno níže.

   > [!div class="mx-imgBorder"]
   > ![Panel průběhu zřizování](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="next-steps"></a>Další kroky

* [Další informace o podporovaných atributech SuccessFactors pro příchozí zřizování](../app-provisioning/sap-successfactors-attribute-reference.md)
* [Naučte se, jak nakonfigurovat zpětný zápis e-mailu na SuccessFactors](sap-successfactors-writeback-tutorial.md)
* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy o aktivitě zřizování.](../app-provisioning/check-status-user-account-provisioning.md)
* [Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi SuccessFactors a Azure Active Directory](successfactors-tutorial.md)
* [Zjistěte, jak integrovat další aplikace SaaS s Azure Active Directory](tutorial-list.md)
* [Přečtěte si, jak exportovat a importovat konfigurace zřizování](../app-provisioning/export-import-provisioning-configuration.md)
