---
title: 'Kurz: Konfigurace SuccessFactors příchozího zřizování v Azure Active Directory | Microsoft Docs'
description: Informace o tom, jak nakonfigurovat příchozí zřizování z SuccessFactors
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
ms.openlocfilehash: 85f3c8b9bc4167350b8a56f118128b89df142611
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2019
ms.locfileid: "74896919"
---
# <a name="tutorial-configure-sap-successfactors-to-active-directory-user-provisioning-preview"></a>Kurz: Konfigurace zřizování SAP SuccessFactors pro uživatele služby Active Directory (Preview)
Cílem tohoto kurzu je Ukázat kroky, které je třeba provést při importu dat pracovních procesů z SuccessFactors zaměstnanců do služby Active Directory a Azure Active Directory, s volitelným zpětným zápisem e-mailové adresy SuccessFactors.

## <a name="overview"></a>Přehled

[Služba zřizování uživatelů Azure Active Directory](../manage-apps/user-provisioning.md) se integruje s [SuccessFactors zaměstnanci](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html) , aby mohla spravovat životní cyklus identity uživatelů. 

Pracovní postupy zřizování uživatelů SuccessFactors, které podporuje služba zřizování uživatelů Azure AD, umožňují automatizaci následujících scénářů lidských zdrojů a životního cyklu identit:

* **Najímání nových zaměstnanců** – když se do SuccessFactors přidá nový zaměstnanec, automaticky se vytvoří uživatelský účet ve službě Active Directory, Azure Active Directory a volitelně Office 365 a [Další SaaS aplikace, které Azure AD podporuje](../manage-apps/user-provisioning.md), a to s zpětným zápisem e-mailové adresy na SuccessFactors.

* **Aktualizace atributů a profilů zaměstnanců** – když se v SuccessFactors aktualizuje záznam zaměstnance (například jeho jméno, název nebo manažer), automaticky se aktualizuje jeho uživatelský účet ve službě Active Directory, Azure Active Directory a volitelně v Office 365 a [dalších SaaS aplikacích, které Azure AD podporuje](../manage-apps/user-provisioning.md).

* **Ukončení zaměstnanců** – když se zaměstnanec v SuccessFactors ukončí, je jeho uživatelský účet automaticky zakázaný ve službě Active Directory, Azure Active Directory a volitelně Office 365 a [Další SaaS aplikace podporované službou Azure AD](../manage-apps/user-provisioning.md).

* **Pracovní zařazení zaměstnanců** – Pokud se zaměstnanec znovu přiřadí v SuccessFactors, jeho starý účet se dá automaticky znovu aktivovat nebo znovu zřídit (v závislosti na vaší preferenci) se službou Active Directory, Azure Active Directory a volitelně Office 365 a [dalšími SaaS aplikacemi, které Azure AD podporuje](../manage-apps/user-provisioning.md).

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Na koho se toto řešení pro zřizování uživatelů nejlépe hodí?

Toto SuccessFactors řešení pro zřizování uživatelů Active Directory je ideální pro:

* Organizace, které chtějí předem připravené cloudové řešení pro zřizování uživatelů SuccessFactors

* Organizace, které vyžadují přímé zřizování uživatelů z SuccessFactors do Active Directory

* Organizace, které vyžadují zřízení uživatelů pomocí dat získaných od [SuccessFactors zaměstnanců (ES)](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html)

* Organizace, které vyžadují, aby se do jedné nebo více doménových struktur služby Active Directory, domén a organizačních jednotek připojovaly k synchronizaci s jednou nebo více doménovými strukturami, a to na základě informací o změně zjištěných v [SuccessFactors zaměstnanci (ES)](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html)

* Organizace, které používají Office 365 k e-mailu

## <a name="solution-architecture"></a>Architektura řešení

Tato část popisuje kompletní architekturu řešení zřizování uživatelů pro běžná hybridní prostředí. Existují dva související toky:

* **Autoritativní tok dat o lidských přenosech – od SuccessFactors k místní službě Active Directory:** V těchto událostech pracovního procesu toku (například nových náborů, přenosů, ukončení) se nejdříve narazí v cloudu SuccessFactors zaměstnanci a data událostí se budou nacházet do místní služby Active Directory prostřednictvím služby Azure AD a zřizovacího agenta. V závislosti na události to může vést k vytváření, aktualizaci, povolování a zakazování operací ve službě AD.
* **Tok zpětného zápisu e-mailu – z místní služby Active Directory do SuccessFactors:** Jakmile se účet vytvoří ve službě Active Directory, synchronizuje se s Azure AD prostřednictvím Azure AD Connect synchronizace a atribut e-mailu se dá zapsat zpátky do SuccessFactors.

  ![Přehled](./media/sap-successfactors-inbound-provisioning/sf2ad-overview.png)

### <a name="end-to-end-user-data-flow"></a>Koncový tok dat uživatele

1. Tým HR provádí pracovní transakce (Spojovacíky/stěhovací společnosti/Leaversy nebo noví zaměstnanci, přenosy a ukončení) v SuccessFactors zaměstnanci – střed
2. Služba zřizování Azure AD spouští naplánované synchronizace identit z SuccessFactors ES a identifikuje změny, které je potřeba zpracovat pro synchronizaci s místní službou Active Directory.
3. Služba zřizování Azure AD vyvolá místního agenta pro zřizování Azure AD Connect s datovou částí požadavku obsahující účet služby AD, který obsahuje operace vytvořit/aktualizovat/povolit/zakázat.
4. Agent zřizování Azure AD Connect používá účet služby k přidání nebo aktualizaci dat účtu AD.
5. Modul Azure AD Connect Sync spouští rozdílovou synchronizaci pro vyžádání aktualizací ve službě AD.
6. Aktualizace služby Active Directory se synchronizují s Azure Active Directory.
7. Pokud je konektor pro zpětný zápis SuccessFactors nakonfigurovaný, zapisuje zpátky atribut e-mailu a uživatelské jméno do SuccessFactors, a to na základě používaného odpovídajícího atributu.

## <a name="planning-your-deployment"></a>Plánování nasazení

Konfigurace zřizování uživatelů cloudového prostředí s využitím cloudu z SuccessFactors do AD vyžaduje výrazné plánování zahrnující různé aspekty, jako je například:
* Nastavení agenta zřizování Azure AD Connect 
* Počet SuccessFactors aplikací pro zřizování uživatelů služby AD pro nasazení
* Porovnání ID, mapování atributů, transformace a rozsah filtru

Podrobné pokyny k těmto tématům najdete v [plánu nasazení cloudového HR](../manage-apps/plan-cloud-hr-provision.md) . 

## <a name="configuring-successfactors-for-the-integration"></a>Konfigurace SuccessFactors pro integraci

Běžným požadavkem na všechny SuccessFactors zřizovacích konektorů je to, že vyžadují přihlašovací údaje účtu SuccessFactors se správnými oprávněními k vyvolání rozhraní API OData SuccessFactors. Tato část popisuje kroky pro vytvoření účtu služby v SuccessFactors a udělení příslušných oprávnění. 

* [Vytvoření nebo identifikace uživatelského účtu rozhraní API v SuccessFactors](#createidentify-api-user-account-in-successfactors)
* [Vytvoření role oprávnění API](#create-an-api-permissions-role)
* [Vytvoření skupiny oprávnění pro uživatele rozhraní API](#create-a-permission-group-for-the-api-user)
* [Udělení role oprávnění skupině oprávnění](#grant-permission-role-to-the-permission-group)

### <a name="createidentify-api-user-account-in-successfactors"></a>Vytvoření nebo identifikace uživatelského účtu rozhraní API v SuccessFactors
Spolupracujte s týmem správce SuccessFactors nebo partnerem pro implementaci a vytvořte nebo identifikujte uživatelský účet v SuccessFactors, který se použije k vyvolání rozhraní OData API. Přihlašovací údaje uživatelského jména a hesla tohoto účtu se budou vyžadovat při konfiguraci zřizovacích aplikací ve službě Azure AD. 

### <a name="create-an-api-permissions-role"></a>Vytvoření role oprávnění API

* Přihlaste se k SAP SuccessFactors pomocí uživatelského účtu, který má přístup k centru pro správu.
* Vyhledejte *možnosti spravovat role oprávnění*a pak ve výsledcích hledání vyberte **Spravovat role oprávnění** .
  ![spravovat role oprávnění](./media/sap-successfactors-inbound-provisioning/manage-permission-roles.png)
* V seznamu role oprávnění klikněte na **vytvořit nový**.
  > [!div class="mx-imgBorder"]
  > ![vytvořit novou roli oprávnění](./media/sap-successfactors-inbound-provisioning/create-new-permission-role-1.png)
* Přidejte název a **Popis** **role** nové role oprávnění. Název a popis by měl označovat, že role je určena pro oprávnění k použití rozhraní API.
  > [!div class="mx-imgBorder"]
  > Podrobnosti role ![oprávnění](./media/sap-successfactors-inbound-provisioning/permission-role-detail.png)
* V části nastavení oprávnění klikněte na **oprávnění...** , přejděte dolů na seznam oprávnění a klikněte na **spravovat nástroje pro integraci**. Zaškrtněte políčko, pokud **chcete, aby správce mohl přistupovat k rozhraní OData API prostřednictvím základního ověřování**.
  > [!div class="mx-imgBorder"]
  > ![Správa integračních nástrojů](./media/sap-successfactors-inbound-provisioning/manage-integration-tools.png)
* Posuňte se dolů ve stejném poli a vyberte **centrální rozhraní API pro zaměstnance**. Přidáním oprávnění, jak je vidět níže, můžete číst pomocí rozhraní ODATA API a upravit pomocí rozhraní ODATA API. Vyberte možnost upravit, pokud chcete použít stejný účet ke zpětnému zápisu do SuccessFactors scénáře. 
  > [!div class="mx-imgBorder"]
  > ![oprávnění ke čtení zápisu](./media/sap-successfactors-inbound-provisioning/odata-read-write-perm.png)
* Klikněte na **Hotovo**. Klikněte na tlačítko **uložit změny**.

### <a name="create-a-permission-group-for-the-api-user"></a>Vytvoření skupiny oprávnění pro uživatele rozhraní API

* V centru pro správu SuccessFactors vyhledejte *možnosti spravovat skupiny oprávnění*a pak ve výsledcích hledání vyberte **Spravovat skupiny oprávnění** .
  > [!div class="mx-imgBorder"]
  > ![spravovat skupiny oprávnění](./media/sap-successfactors-inbound-provisioning/manage-permission-groups.png)
* V okně Spravovat skupiny oprávnění klikněte na **vytvořit nový**.
  > [!div class="mx-imgBorder"]
  > ![přidat novou](./media/sap-successfactors-inbound-provisioning/create-new-group.png) skupiny
* Přidejte název skupiny pro novou skupinu. Název skupiny by měl označovat, že skupina je určena pro uživatele rozhraní API.
  > [!div class="mx-imgBorder"]
  > název skupiny oprávnění ![](./media/sap-successfactors-inbound-provisioning/permission-group-name.png)
* Přidejte členy do skupiny. Můžete například vybrat **uživatelské jméno** z rozevírací nabídky fond osob a pak zadat uživatelské jméno účtu rozhraní API, které se bude používat pro integraci. 
  > [!div class="mx-imgBorder"]
  > ![Přidání členů skupiny](./media/sap-successfactors-inbound-provisioning/add-group-members.png)
* Kliknutím na **Hotovo** dokončíte vytváření skupiny oprávnění.

### <a name="grant-permission-role-to-the-permission-group"></a>Udělení role oprávnění skupině oprávnění

* V centru pro správu SuccessFactors vyhledejte *možnosti spravovat role oprávnění*a pak ve výsledcích hledání vyberte **Spravovat role oprávnění** .
* V **seznamu role oprávnění**vyberte roli, kterou jste vytvořili pro oprávnění používání rozhraní API.
* V části **udělení této role na...** , klikněte na tlačítko **Přidat..** ..
* V rozevírací nabídce vyberte **skupinu oprávnění** a pak kliknutím na **Vybrat...** otevřete okno skupiny, kde můžete hledat a vybrat skupinu vytvořenou výše. 
  > [!div class="mx-imgBorder"]
  > ![přidat skupinu oprávnění](./media/sap-successfactors-inbound-provisioning/add-permission-group.png)
* Zkontrolujte roli oprávnění udělení skupiny oprávnění. 
  > [!div class="mx-imgBorder"]
  > ![role oprávnění a podrobností skupiny](./media/sap-successfactors-inbound-provisioning/permission-role-group.png)
* Klikněte na tlačítko **uložit změny**.

## <a name="configuring-user-provisioning-from-successfactors-to-active-directory"></a>Konfigurace zřizování uživatelů z SuccessFactors na službu Active Directory

Tato část popisuje kroky pro zřizování uživatelských účtů z SuccessFactors do jednotlivých domén služby Active Directory v rámci rozsahu integrace.

* [Přidání aplikace zřizovacího konektoru a stažení agenta zřizování](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent)
* [Instalace a konfigurace místních agentů zřizování](#part-2-install-and-configure-on-premises-provisioning-agents)
* [Konfigurace připojení k SuccessFactors a službě Active Directory](#part-3-in-the-provisioning-app-configure-connectivity-to-successfactors-and-active-directory)
* [Konfigurace mapování atributů](#part-4-configure-attribute-mappings)
* [Povolení a spuštění zřizování uživatelů](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent"></a>Část 1: Přidání aplikace konektoru pro zřizování a stažení agenta zřizování

**Konfigurace SuccessFactors na zřizování služby Active Directory:**

1. Přejděte na <https://portal.azure.com>.

2. V levém navigačním panelu vyberte **Azure Active Directory**

3. Vyberte **podnikové aplikace**a pak **všechny aplikace**.

4. Vyberte **Přidat aplikaci**a vyberte kategorii **vše** .

5. Vyhledejte **SuccessFactors do zřizování uživatelů služby Active Directory**a přidejte tuto aplikaci z galerie.

6. Až se aplikace přidá a zobrazí se obrazovka s podrobnostmi aplikace, vyberte **zřizování** .

7. Změnit režim **zřizování** na **automaticky**

8. Pokud chcete stáhnout agenta pro zřizování, klikněte na zobrazený informační banner. 
   > [!div class="mx-imgBorder"]
   > ![Stáhnout agenta](./media/sap-successfactors-inbound-provisioning/download-pa-agent.png "Stáhnout agenta – obrazovka")


### <a name="part-2-install-and-configure-on-premises-provisioning-agents"></a>Část 2: instalace a konfigurace místních agentů zřizování

Aby bylo možné zřídit místní službu Active Directory, musí být agent zřizování nainstalován na serveru, který má .NET 4.7.1 + Framework a síťový přístup k požadovaným doménám služby Active Directory.

> [!TIP]
> Verzi rozhraní .NET Framework na serveru můžete ověřit podle [zde](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed)uvedených pokynů.
> Pokud server nemá nainstalované rozhraní .NET 4.7.1 nebo vyšší, můžete si ho stáhnout [odsud.](https://support.microsoft.com/help/4033342/the-net-framework-4-7-1-offline-installer-for-windows)  

Přeneste stažený instalační program agenta na hostitele serveru a podle níže uvedených kroků dokončete konfiguraci agenta.

1. Přihlaste se k systému Windows Server, na který chcete nainstalovat nového agenta.

1. Spusťte instalační program agenta zřizování, vyjádřete podmínky a klikněte na tlačítko **nainstalovat** .

   ![Instalační obrazovka](./media/workday-inbound-tutorial/pa_install_screen_1.png "Instalační obrazovka")
   
1. Po dokončení instalace se Průvodce spustí a zobrazí se obrazovka **připojit Azure AD** . Kliknutím na tlačítko **ověřit** se připojte k instanci Azure AD.

   ![Připojit Azure AD](./media/workday-inbound-tutorial/pa_install_screen_2.png "Připojení Azure AD")
   
1. Ověřte se u vaší instance Azure AD pomocí přihlašovacích údajů globálního správce.

   ![Ověřování správce](./media/workday-inbound-tutorial/pa_install_screen_3.png "Ověřování správce")

   > [!NOTE]
   > Přihlašovací údaje správce Azure AD se používají jenom pro připojení k vašemu tenantovi Azure AD. Agent neukládá pověření místně na serveru.

1. Po úspěšném ověření pomocí služby Azure AD se zobrazí obrazovka **připojit ke službě Active Directory** . V tomto kroku zadejte název domény služby Active Directory a klikněte na tlačítko **Přidat adresář** .

   ![Přidat adresář](./media/workday-inbound-tutorial/pa_install_screen_4.png "Přidat adresář")
  
1. Nyní budete vyzváni k zadání přihlašovacích údajů požadovaných pro připojení k doméně služby AD. Na stejné obrazovce můžete použít **prioritu vybrat řadič domény** a zadat řadiče domény, které by měl agent použít pro odesílání žádostí o zřízení.

   ![Přihlašovací údaje domény](./media/workday-inbound-tutorial/pa_install_screen_5.png)
   
1. Po nakonfigurování domény instalační program zobrazí seznam nakonfigurovaných domén. Na této obrazovce můžete opakovat krok #5 a #6 přidat další domény, kliknutím na tlačítko **Další** pokračujte v registraci agenta.

   ![Nakonfigurované domény](./media/workday-inbound-tutorial/pa_install_screen_6.png "Nakonfigurované domény")

   > [!NOTE]
   > Pokud máte více domén služby AD (například na.contoso.com, emea.contoso.com), přidejte každou doménu do seznamu jednotlivě.
   > Pouze přidání nadřazené domény (např. contoso.com) není dostačující. Musíte zaregistrovat každou podřízenou doménu s agentem.
   
1. Zkontrolujte podrobnosti o konfiguraci a kliknutím na **Potvrdit** agenta zaregistrujte.
  
   ![Potvrdit obrazovku](./media/workday-inbound-tutorial/pa_install_screen_7.png "Potvrdit obrazovku")
   
1. Průvodce konfigurací zobrazí průběh registrace agenta.
  
   ![Registrace agenta](./media/workday-inbound-tutorial/pa_install_screen_8.png "Registrace agenta")
   
1. Po úspěšné registraci agenta můžete kliknutím na tlačítko **ukončit** průvodce ukončit.
  
   ![Ukončit obrazovku](./media/workday-inbound-tutorial/pa_install_screen_9.png "Ukončit obrazovku")
   
1. Ověřte instalaci agenta a ujistěte se, že je spuštěný, a to tak, že otevřete modul snap-in služby a vyhledáte službu s názvem "Microsoft Azure AD připojit zřizování agent".
  
   ![Služby](./media/workday-inbound-tutorial/services.png)

### <a name="part-3-in-the-provisioning-app-configure-connectivity-to-successfactors-and-active-directory"></a>Část 3: v aplikaci zřizování nakonfigurujte možnosti připojení k SuccessFactors a Active Directory.
V tomto kroku navážeme připojení k SuccessFactors a službě Active Directory v Azure Portal. 

1. V Azure Portal se vraťte do aplikace SuccessFactors do služby Active Directory pro zřizování uživatelů vytvořenou v [části 1](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent) .

1. Dokončete část **přihlašovací údaje správce** následujícím způsobem:

   * **Uživatelské jméno správce** – zadejte uživatelské jméno uživatelského účtu rozhraní SuccessFactors API s připojením ID společnosti. Má formát: **username\@companyID**

   * **Heslo správce –** Zadejte heslo uživatelského účtu rozhraní SuccessFactors API. 

   * **Adresa URL tenanta –** Zadejte název koncového bodu SuccessFactors OData API Services. Zadejte pouze název hostitele serveru bez protokolu HTTP nebo HTTPS. Tato hodnota by měla vypadat takto: **< API-server-name >. SuccessFactors. com**.

   * **Doménová struktura služby Active Directory –** Název domény služby Active Directory, jak je zaregistrován u agenta. Pomocí rozevírací nabídky vyberte cílovou doménu pro zřizování. Tato hodnota je obvykle řetězec jako: *contoso.com*

   * **Kontejner služby Active Directory –** Zadejte rozlišující název kontejneru, kde by měl agent ve výchozím nastavení vytvářet uživatelské účty.
        Příklad: *ou = Users, DC = contoso, DC = com*
        > [!NOTE]
        > Toto nastavení se dá přehrát jenom pro vytváření uživatelských účtů, pokud není atribut *parentDistinguishedName* nakonfigurovaný v mapování atributů. Toto nastavení se nepoužívá pro operace vyhledávání a aktualizace uživatelů. Celý podstrom domény spadá do rozsahu operace hledání.

   * **E-mail s oznámením –** Zadejte svou e-mailovou adresu a zaškrtněte políčko Odeslat e-mail, pokud dojde k chybě.
         > [!NOTE]
         > The Azure AD Provisioning Service sends email notification if the provisioning job goes into a [quarantine](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning#quarantine) state.

   * Klikněte na tlačítko **Testovat připojení** . Pokud je test připojení úspěšný, klikněte na tlačítko **Uložit** v horní části. Pokud se to nepovede, poklikejte na to, jestli jsou přihlašovací údaje SuccessFactors a přihlašovací údaje služby AD nakonfigurované na instalaci agenta platné.
    >[!div class="mx-imgBorder"]
    >![Azure Portal](./media/sap-successfactors-inbound-provisioning/sf2ad-provisioning-creds.png)

   * Po úspěšném uložení přihlašovacích údajů se v oddílu **mapování** zobrazí výchozí mapování **synchronizovat uživatele SuccessFactors do místní služby Active Directory** .

### <a name="part-4-configure-attribute-mappings"></a>4\. část: Konfigurace mapování atributů

V této části nakonfigurujete, jak budou data uživatelů z SuccessFactors do služby Active Directory přetékat.

1. Na kartě zřizování v části **mapování**klikněte na **synchronizovat uživatele SuccessFactors do místní služby Active Directory**.

1. V poli **obor zdrojového objektu** můžete vybrat, které sady uživatelů v SuccessFactors mají být v oboru pro zřizování služby AD, definováním sady filtrů založených na atributech. Výchozí obor je "Všichni uživatelé v SuccessFactors". Příklady filtrů:

   * Příklad: určení oboru pro uživatele s personIdExternal mezi 1000000 a 2000000 (s výjimkou 2000000)

      * Atribut: personIdExternal

      * Operátor: shoda regulárního výrazu

      * Hodnota: (1 [0-9] [0-9] [0-9] [0-9] [0-9] [0-9])

   * Příklad: pouze zaměstnanci a ne saminí pracovníci

      * Atribut: ČísloZaměstnance

      * Operátor: není NULL

   > [!TIP]
   > Při první konfiguraci zřizovací aplikace budete muset otestovat a ověřit mapování atributů a výrazy, abyste se ujistili, že vám poskytne požadovaný výsledek. Microsoft doporučuje pomocí filtrů oborů v **oboru zdrojového objektu** testovat mapování s několika testovacími uživateli z SuccessFactors. Jakmile ověříte, že mapování funguje, můžete buď odebrat filtr, nebo ho postupně rozšířit, aby zahrnoval více uživatelů.

   > [!CAUTION] 
   > Výchozím chováním modulu zřizování je zakázat nebo odstranit uživatele, kteří se přestanou přidělovat z oboru. To nemusí být žádoucí v SuccessFactors integrace se službou AD. Pokud chcete přepsat toto výchozí chování, přečtěte si článek [přeskočení odstranění uživatelských účtů, které se nacházejí mimo rozsah](../manage-apps/skip-out-of-scope-deletions.md) .
  
1. V poli **Akce cílového objektu** můžete globálně filtrovat akce prováděné ve službě Active Directory. **Vytváření** a **aktualizace** jsou nejběžnější.

1. V části **mapování atributů** můžete definovat, jak se jednotlivé atributy SuccessFactors mapují na atributy služby Active Directory.

1. Kliknutím na existující mapování atributů ho aktualizujte nebo kliknutím na **Přidat nové mapování** v dolní části obrazovky přidejte nová mapování. Jednotlivé mapování atributů podporují tyto vlastnosti:

      * **Typ mapování**

         * **Direct** – zapíše hodnotu atributu SuccessFactors do atributu AD bez jakýchkoli změn.

         * **Konstanta** – zapsat statickou konstantní hodnotu řetězce na atribut AD

         * **Výraz** – umožňuje napsat vlastní hodnotu atributu AD na základě jednoho nebo více atributů SuccessFactors. [Další informace najdete v tomto článku o výrazech](../manage-apps/functions-for-customizing-application-data.md).

      * **Zdrojový atribut** – atribut uživatele z SuccessFactors

      * **Výchozí hodnota** – volitelné. Pokud zdrojový atribut má prázdnou hodnotu, mapování místo toho zapíše tuto hodnotu.
            Nejběžnější konfigurací je ponecháno toto prázdné.

      * **Cílový atribut** – atribut uživatele ve službě Active Directory.

      * **Porovnává objekty pomocí tohoto atributu** – bez ohledu na to, jestli se má toto mapování použít k jednoznačné identifikaci uživatelů mezi SuccessFactors a Active Directory. Tato hodnota je obvykle nastavena v poli ID pracovního procesu pro SuccessFactors, což je obvykle namapováno na jeden z atributů ID zaměstnance ve službě Active Directory.

      * **Priorita porovnání** – lze nastavit více vyhovujících atributů. Pokud existuje více, vyhodnotí se v pořadí definovaném tímto polem. Jakmile se najde shoda, nevyhodnocují se žádné další odpovídající atributy.

      * **Použít toto mapování**

         * **Vždycky** – použít toto mapování na akce vytvoření a aktualizace uživatele

         * **Pouze během vytváření** – použít toto mapování pouze při akcích vytvoření uživatele

1. Pokud chcete uložit mapování, klikněte na **Uložit** v horní části oddílu mapování atributů.

Po dokončení konfigurace mapování atributů teď můžete [Povolit a spustit službu zřizování uživatelů](#enable-and-launch-user-provisioning).

## <a name="enable-and-launch-user-provisioning"></a>Povolení a spuštění zřizování uživatelů

Po dokončení konfigurace aplikace SuccessFactors Provisioning můžete službu zřizování zapnout v Azure Portal.

> [!TIP]
> Ve výchozím nastavení se při zapnutí služby zřizování spustí operace zřizování pro všechny uživatele v oboru. Pokud dojde k chybám při mapování nebo při potížích s daty Workday, úloha zřizování může selhat a přejít do stavu karantény. Aby k tomu nedocházelo, doporučujeme nakonfigurovat filtr **oboru zdrojového objektu** a otestovat mapování atributů s několika testovacími uživateli před spuštěním úplné synchronizace pro všechny uživatele. Jakmile ověříte, že mapování funguje a poskytuje požadované výsledky, můžete buď odebrat filtr, nebo ho postupně rozšířit, aby zahrnoval více uživatelů.

1. Na kartě **zřizování** nastavte **stav zřizování** na **zapnuto**.

2. Klikněte na **Uložit**.

3. Tato operace spustí počáteční synchronizaci, což může trvat proměnlivý počet hodin v závislosti na tom, kolik uživatelů je v tenantovi SuccessFactors. V indikátoru průběhu můžete sledovat průběh cyklu synchronizace. 

4. Na kartě **protokoly auditu** v Azure Portal můžete kdykoli zjistit, jaké akce služba zřizování provedla. Protokoly auditu vypíše všechny jednotlivé události synchronizace prováděné službou zřizování, například které uživatele se čtou z pracovního dne a následně se přidají nebo aktualizují ve službě Active Directory. 

5. Po dokončení počáteční synchronizace bude na kartě **zřizování** napsána Sestava souhrnu auditu, jak je znázorněno níže.

   > [!div class="mx-imgBorder"]
   > indikátor průběhu zřizování ![](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy pro aktivitu zřizování.](../manage-apps/check-status-user-account-provisioning.md)
* [Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi SuccessFactors a Azure Active Directory](successfactors-tutorial.md)
* [Naučte se integrovat další aplikace SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Naučte se exportovat a importovat vaše konfigurace zřizování.](../manage-apps/export-import-provisioning-configuration.md)













