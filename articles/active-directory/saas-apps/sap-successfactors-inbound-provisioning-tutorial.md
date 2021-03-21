---
title: 'Kurz: Konfigurace příchozího zřizování SuccessFactors ve službě AD a Azure AD | Microsoft Docs'
description: Informace o tom, jak nakonfigurovat příchozí zřizování z SuccessFactors
services: active-directory
author: cmmdesai
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.topic: tutorial
ms.workload: identity
ms.date: 01/19/2021
ms.author: chmutali
ms.openlocfilehash: 7b59e0ae2fbb73f341d5254fd2804d50ad141a19
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98953797"
---
# <a name="tutorial-configure-sap-successfactors-to-active-directory-user-provisioning"></a>Kurz: Konfigurace zřizování SAP SuccessFactors pro uživatele služby Active Directory 
Cílem tohoto kurzu je Ukázat kroky, které potřebujete k tomu, abyste uživatelům zřídili SuccessFactors zaměstnanci v rámci služby Active Directory (AD) a Azure AD s volitelným zpětným zápisem e-mailové adresy na SuccessFactors. 

>[!NOTE]
>Tento kurz použijte v případě, že uživatelé, které chcete zřídit z SuccessFactors, potřebují místní účet služby AD a volitelně účet Azure AD. Pokud uživatelé z SuccessFactors potřebují jenom účet Azure AD (jenom pro cloudové uživatele), přečtěte si prosím kurz [konfigurace SAP SuccessFactors na zřizování uživatelů Azure AD](sap-successfactors-inbound-provisioning-cloud-only-tutorial.md) . 


## <a name="overview"></a>Přehled

[Služba zřizování uživatelů Azure Active Directory](../app-provisioning/user-provisioning.md) se integruje s [SuccessFactors zaměstnanci](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html) , aby mohla spravovat životní cyklus identity uživatelů. 

Pracovní postupy zřizování uživatelů SuccessFactors, které podporuje služba zřizování uživatelů Azure AD, umožňují automatizaci následujících scénářů lidských zdrojů a životního cyklu identit:

* **Pronájem nových zaměstnanců** – když se do SuccessFactors přidá nový zaměstnanec, automaticky se vytvoří uživatelský účet ve službě Active Directory, Azure Active Directory a volitelně Microsoft 365 a [Další SaaS aplikace podporované službou Azure AD](../app-provisioning/user-provisioning.md), a to s zpětným zápisem e-mailové adresy na SuccessFactors.

* **Aktualizace atributů a profilů zaměstnanců** – když se v SuccessFactors aktualizuje záznam zaměstnance (například jeho jméno, název nebo manažer), automaticky se aktualizuje jeho uživatelský účet ve službě Active Directory, Azure Active Directory a případně Microsoft 365 a [Další SaaS aplikace podporované službou Azure AD](../app-provisioning/user-provisioning.md).

* **Ukončení zaměstnanců** – když se zaměstnanec v SuccessFactors ukončí, je jejich uživatelský účet automaticky zakázaný ve službě Active Directory, Azure Active Directory a volitelně Microsoft 365 a [Další SaaS aplikace podporované službou Azure AD](../app-provisioning/user-provisioning.md).

* **Pracovní zařazení zaměstnanců** – Pokud se zaměstnanec znovu přiřadí v SuccessFactors, jeho starý účet se dá automaticky znovu aktivovat nebo znovu zřídit (v závislosti na vaší preferenci) se službou Active Directory, Azure Active Directory a volitelně Microsoft 365 a [dalšími SaaS aplikacemi, které Azure AD podporuje](../app-provisioning/user-provisioning.md).

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Na koho se toto řešení pro zřizování uživatelů nejlépe hodí?

Toto SuccessFactors řešení pro zřizování uživatelů Active Directory je ideální pro:

* Organizace, které chtějí předem připravené cloudové řešení pro zřizování uživatelů SuccessFactors

* Organizace, které vyžadují přímé zřizování uživatelů z SuccessFactors do Active Directory

* Organizace, které vyžadují zřízení uživatelů pomocí dat získaných od [SuccessFactors zaměstnanců (ES)](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html)

* Organizace, které vyžadují, aby se do jedné nebo více doménových struktur služby Active Directory, domén a organizačních jednotek připojovaly k synchronizaci s jednou nebo více doménovými strukturami, a to na základě informací o změně zjištěných v [SuccessFactors zaměstnanci (ES)](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html)

* Organizace, které používají Microsoft 365 k e-mailu

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
7. Pokud je [aplikace pro zpětný zápis SuccessFactors](sap-successfactors-writeback-tutorial.md) nakonfigurovaná, zapisuje zpátky atribut e-mailu na SuccessFactors na základě používaného odpovídajícího atributu.

## <a name="planning-your-deployment"></a>Plánování nasazení

Konfigurace zřizování uživatelů cloudového prostředí s využitím cloudu z SuccessFactors do AD vyžaduje výrazné plánování zahrnující různé aspekty, jako je například:
* Nastavení agenta zřizování Azure AD Connect 
* Počet SuccessFactors aplikací pro zřizování uživatelů služby AD pro nasazení
* Porovnání ID, mapování atributů, transformace a rozsah filtru

Podrobné pokyny k těmto tématům najdete v [plánu nasazení cloudového HR](../app-provisioning/plan-cloud-hr-provision.md) . Informace o podporovaných entitách, podrobnostech o zpracování a způsobu přizpůsobení integrace pro různé scénáře týkající se lidských zdrojů najdete v referenčních informacích k [integraci SAP SuccessFactors](../app-provisioning/sap-successfactors-integration-reference.md) . 

## <a name="configuring-successfactors-for-the-integration"></a>Konfigurace SuccessFactors pro integraci

Běžným požadavkem na všechny SuccessFactors zřizovacích konektorů je to, že vyžadují přihlašovací údaje účtu SuccessFactors se správnými oprávněními k vyvolání rozhraní API OData SuccessFactors. Tato část popisuje kroky pro vytvoření účtu služby v SuccessFactors a udělení příslušných oprávnění. 

* [Vytvoření nebo identifikace uživatelského účtu rozhraní API v SuccessFactors](#createidentify-api-user-account-in-successfactors)
* [Vytvoření role oprávnění API](#create-an-api-permissions-role)
* [Vytvoření skupiny oprávnění pro uživatele rozhraní API](#create-a-permission-group-for-the-api-user)
* [Udělení role oprávnění skupině oprávnění](#grant-permission-role-to-the-permission-group)

### <a name="createidentify-api-user-account-in-successfactors"></a>Vytvoření nebo identifikace uživatelského účtu rozhraní API v SuccessFactors
Spolupracujte s týmem správce SuccessFactors nebo partnerem pro implementaci a vytvořte nebo identifikujte uživatelský účet v SuccessFactors, který se použije k vyvolání rozhraní OData API. Přihlašovací údaje uživatelského jména a hesla tohoto účtu se budou vyžadovat při konfiguraci zřizovacích aplikací ve službě Azure AD. 

### <a name="create-an-api-permissions-role"></a>Vytvoření role oprávnění API

1. Přihlaste se k SAP SuccessFactors pomocí uživatelského účtu, který má přístup k centru pro správu.
1. Vyhledejte *možnosti spravovat role oprávnění* a pak ve výsledcích hledání vyberte **Spravovat role oprávnění** .
  ![Správa rolí oprávnění](./media/sap-successfactors-inbound-provisioning/manage-permission-roles.png)
1. V seznamu role oprávnění klikněte na **vytvořit nový**.
    > [!div class="mx-imgBorder"]
    > ![Vytvořit novou roli oprávnění](./media/sap-successfactors-inbound-provisioning/create-new-permission-role-1.png)
1. Přidejte název a **Popis** **role** nové role oprávnění. Název a popis by měl označovat, že role je určena pro oprávnění k použití rozhraní API.
    > [!div class="mx-imgBorder"]
    > ![Podrobnosti role oprávnění](./media/sap-successfactors-inbound-provisioning/permission-role-detail.png)
1. V části nastavení oprávnění klikněte na **oprávnění...**, přejděte dolů na seznam oprávnění a klikněte na **spravovat nástroje pro integraci**. Zaškrtněte políčko, pokud **chcete, aby správce mohl přistupovat k rozhraní OData API prostřednictvím základního ověřování**.
    > [!div class="mx-imgBorder"]
    > ![Správa integračních nástrojů](./media/sap-successfactors-inbound-provisioning/manage-integration-tools.png)
1. Posuňte se dolů ve stejném poli a vyberte **centrální rozhraní API pro zaměstnance**. Přidáním oprávnění, jak je vidět níže, můžete číst pomocí rozhraní ODATA API a upravit pomocí rozhraní ODATA API. Vyberte možnost upravit, pokud chcete použít stejný účet ke zpětnému zápisu do SuccessFactors scénáře. 
    > [!div class="mx-imgBorder"]
    > ![Oprávnění ke čtení zápisu](./media/sap-successfactors-inbound-provisioning/odata-read-write-perm.png)

1. V poli stejná oprávnění klikněte na **uživatelská oprávnění – > údaje o zaměstnancích** a zkontrolujte atributy, které může účet služby číst z klienta SuccessFactors. Chcete-li například načíst atribut *username* z SuccessFactors, ujistěte se, že pro tento atribut je uděleno oprávnění "View". Podobně zkontrolujte každý atribut pro oprávnění zobrazit. 

    > [!div class="mx-imgBorder"]
    > ![Oprávnění k datům zaměstnanců](./media/sap-successfactors-inbound-provisioning/review-employee-data-permissions.png)
   

    >[!NOTE]
    >Úplný seznam atributů načtených touto zřizovací aplikací najdete v [referenčních informacích k atributům SuccessFactors](../app-provisioning/sap-successfactors-attribute-reference.md) .

1. Klikněte na **Hotovo**. Klikněte na **Save Changes** (Uložit změny).

### <a name="create-a-permission-group-for-the-api-user"></a>Vytvoření skupiny oprávnění pro uživatele rozhraní API

1. V centru pro správu SuccessFactors vyhledejte *možnosti spravovat skupiny oprávnění* a pak ve výsledcích hledání vyberte **Spravovat skupiny oprávnění** .
    > [!div class="mx-imgBorder"]
    > ![Správa skupin oprávnění](./media/sap-successfactors-inbound-provisioning/manage-permission-groups.png)
1. V okně Spravovat skupiny oprávnění klikněte na **vytvořit nový**.
    > [!div class="mx-imgBorder"]
    > ![Přidat novou skupinu](./media/sap-successfactors-inbound-provisioning/create-new-group.png)
1. Přidejte název skupiny pro novou skupinu. Název skupiny by měl označovat, že skupina je určena pro uživatele rozhraní API.
    > [!div class="mx-imgBorder"]
    > ![Název skupiny oprávnění](./media/sap-successfactors-inbound-provisioning/permission-group-name.png)
1. Přidejte členy do skupiny. Můžete například vybrat **uživatelské jméno** z rozevírací nabídky fond osob a pak zadat uživatelské jméno účtu rozhraní API, které se bude používat pro integraci. 
    > [!div class="mx-imgBorder"]
    > ![Přidání členů skupiny](./media/sap-successfactors-inbound-provisioning/add-group-members.png)
1. Kliknutím na **Hotovo** dokončíte vytváření skupiny oprávnění.

### <a name="grant-permission-role-to-the-permission-group"></a>Udělení role oprávnění skupině oprávnění

1. V centru pro správu SuccessFactors vyhledejte *možnosti spravovat role oprávnění* a pak ve výsledcích hledání vyberte **Spravovat role oprávnění** .
1. V **seznamu role oprávnění** vyberte roli, kterou jste vytvořili pro oprávnění používání rozhraní API.
1. V části **udělení této role na...**, klikněte na tlačítko **Přidat..** ..
1. V rozevírací nabídce vyberte **skupinu oprávnění** a pak kliknutím na **Vybrat...** otevřete okno skupiny, kde můžete hledat a vybrat skupinu vytvořenou výše. 
    > [!div class="mx-imgBorder"]
    > ![Přidat skupinu oprávnění](./media/sap-successfactors-inbound-provisioning/add-permission-group.png)
1. Zkontrolujte roli oprávnění udělení skupiny oprávnění. 
    > [!div class="mx-imgBorder"]
    > ![Podrobnosti role a skupiny oprávnění](./media/sap-successfactors-inbound-provisioning/permission-role-group.png)
1. Klikněte na **Save Changes** (Uložit změny).

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

3. Vyberte **podnikové aplikace** a pak **všechny aplikace**.

4. Vyberte **Přidat aplikaci** a vyberte kategorii **vše** .

5. Vyhledejte **SuccessFactors do zřizování uživatelů služby Active Directory** a přidejte tuto aplikaci z galerie.

6. Až se aplikace přidá a zobrazí se obrazovka s podrobnostmi aplikace, vyberte **zřizování** .

7. Změnit režim **zřizování**  na **automaticky**

8. Pokud chcete stáhnout agenta pro zřizování, klikněte na zobrazený informační banner. 
   >[!div class="mx-imgBorder"]
   >![Stáhnout agenta](./media/workday-inbound-tutorial/pa-download-agent.png "Stáhnout agenta – obrazovka")

### <a name="part-2-install-and-configure-on-premises-provisioning-agents"></a>Část 2: instalace a konfigurace místních agentů zřizování

Aby bylo možné zřídit místní službu Active Directory, musí být agent zřizování nainstalován na server připojený k doméně, který má síťový přístup k požadované doméně služby Active Directory.

Přeneste stažený instalační program agenta na hostitele serveru a podle kroků uvedených [v části Install agent](../cloud-sync/how-to-install.md) dokončete konfiguraci agenta.

### <a name="part-3-in-the-provisioning-app-configure-connectivity-to-successfactors-and-active-directory"></a>Část 3: v aplikaci zřizování nakonfigurujte možnosti připojení k SuccessFactors a Active Directory.
V tomto kroku navážeme připojení k SuccessFactors a službě Active Directory v Azure Portal. 

1. V Azure Portal se vraťte do aplikace SuccessFactors do služby Active Directory pro zřizování uživatelů vytvořenou v [části 1](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent) .

1. Dokončete část **přihlašovací údaje správce** následujícím způsobem:

   * **Uživatelské jméno správce** – zadejte uživatelské jméno uživatelského účtu rozhraní SuccessFactors API s připojením ID společnosti. Má formát: **UserName \@ companyID**

   * **Heslo správce –** Zadejte heslo uživatelského účtu rozhraní SuccessFactors API. 

   * **Adresa URL tenanta –** Zadejte název koncového bodu SuccessFactors OData API Services. Zadejte pouze název hostitele serveru bez protokolu HTTP nebo HTTPS. Tato hodnota by měla vypadat takto: **<API-server-name>. SuccessFactors.com**.

   * **Doménová struktura služby Active Directory –** Název domény služby Active Directory, jak je zaregistrován u agenta. Pomocí rozevírací nabídky vyberte cílovou doménu pro zřizování. Tato hodnota je obvykle řetězec jako: *contoso.com*

   * **Kontejner služby Active Directory –** Zadejte rozlišující název kontejneru, kde by měl agent ve výchozím nastavení vytvářet uživatelské účty.
        Příklad: *ou = Users, DC = contoso, DC = com*
        > [!NOTE]
        > Toto nastavení se dá přehrát jenom pro vytváření uživatelských účtů, pokud není atribut *parentDistinguishedName* nakonfigurovaný v mapování atributů. Toto nastavení se nepoužívá pro operace vyhledávání a aktualizace uživatelů. Celý podstrom domény spadá do rozsahu operace hledání.

   * **E-mail s oznámením –** Zadejte svou e-mailovou adresu a zaškrtněte políčko Odeslat e-mail, pokud dojde k chybě.
     > [!NOTE]
     > Služba zřizování Azure AD pošle e-mailové oznámení, pokud úloha zřizování přejde do stavu [karantény](../app-provisioning/application-provisioning-quarantine-status.md) .

   * Klikněte na tlačítko **Testovat připojení** . Pokud je test připojení úspěšný, klikněte na tlačítko **Uložit** v horní části. Pokud se to nepovede, poklikejte na to, jestli jsou přihlašovací údaje SuccessFactors a přihlašovací údaje služby AD nakonfigurované na instalaci agenta platné.
     >[!div class="mx-imgBorder"]
     >![Azure Portal](./media/sap-successfactors-inbound-provisioning/sf2ad-provisioning-creds.png)

   * Po úspěšném uložení přihlašovacích údajů se v oddílu **mapování** zobrazí výchozí mapování **synchronizovat uživatele SuccessFactors do místní služby Active Directory** .

### <a name="part-4-configure-attribute-mappings"></a>4. část: Konfigurace mapování atributů

V této části nakonfigurujete, jak budou data uživatelů z SuccessFactors do služby Active Directory přetékat.

1. Na kartě zřizování v části **mapování** klikněte na **synchronizovat uživatele SuccessFactors do místní služby Active Directory**.

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
   > Výchozím chováním modulu zřizování je zakázat nebo odstranit uživatele, kteří se přestanou přidělovat z oboru. To nemusí být žádoucí v SuccessFactors integrace se službou AD. Pokud chcete přepsat toto výchozí chování, přečtěte si článek [přeskočení odstranění uživatelských účtů, které se nacházejí mimo rozsah](../app-provisioning/skip-out-of-scope-deletions.md) .
  
1. V poli **Akce cílového objektu** můžete globálně filtrovat akce prováděné ve službě Active Directory. **Vytváření** a **aktualizace** jsou nejběžnější.

1. V části **mapování atributů** můžete definovat, jak se jednotlivé atributy SuccessFactors mapují na atributy služby Active Directory.

     >[!NOTE]
     >Úplný seznam atributu SuccessFactors podporovaného aplikací najdete v [referenčních informacích k atributům SuccessFactors](../app-provisioning/sap-successfactors-attribute-reference.md) .

1. Kliknutím na existující mapování atributů ho aktualizujte nebo kliknutím na **Přidat nové mapování** v dolní části obrazovky přidejte nová mapování. Jednotlivé mapování atributů podporují tyto vlastnosti:

      * **Typ mapování**

         * **Direct** – zapíše hodnotu atributu SuccessFactors do atributu AD bez jakýchkoli změn.

         * **Konstanta** – zapsat statickou konstantní hodnotu řetězce na atribut AD

         * **Výraz** – umožňuje napsat vlastní hodnotu atributu AD na základě jednoho nebo více atributů SuccessFactors. [Další informace najdete v tomto článku o výrazech](../app-provisioning/functions-for-customizing-application-data.md).

      * **Zdrojový atribut** – atribut uživatele z SuccessFactors

      * **Výchozí hodnota** – volitelné. Pokud zdrojový atribut má prázdnou hodnotu, mapování místo toho zapíše tuto hodnotu.
            Nejběžnější konfigurací je ponecháno toto prázdné.

      * **Cílový atribut** – atribut uživatele ve službě Active Directory.

      * **Porovnává objekty pomocí tohoto atributu** – bez ohledu na to, jestli se má toto mapování použít k jednoznačné identifikaci uživatelů mezi SuccessFactors a Active Directory. Tato hodnota je obvykle nastavena v poli ID pracovního procesu pro SuccessFactors, což je obvykle namapováno na jeden z atributů ID zaměstnance ve službě Active Directory.

      * **Priorita porovnání** – lze nastavit více vyhovujících atributů. Pokud existuje více, vyhodnotí se v pořadí definovaném tímto polem. Jakmile se najde shoda, nevyhodnocují se žádné další odpovídající atributy.

      * **Použít toto mapování**

         * **Vždycky** – použít toto mapování na akce vytvoření a aktualizace uživatele

         * **Pouze během vytváření** – použít toto mapování pouze při akcích vytvoření uživatele

1. Pokud chcete uložit mapování, klikněte na **Uložit** v horní části Attribute-Mapping části.

Po dokončení konfigurace mapování atributů můžete otestovat zřizování pro jednoho uživatele pomocí [zřizování na vyžádání](../app-provisioning/provision-on-demand.md) a pak [Povolit a spustit službu zřizování uživatelů](#enable-and-launch-user-provisioning).

## <a name="enable-and-launch-user-provisioning"></a>Povolení a spuštění zřizování uživatelů

Po dokončení konfigurace aplikace SuccessFactors Provisioning a ověření pro jednoho uživatele pomocí [zřizování na vyžádání](../app-provisioning/provision-on-demand.md)můžete zapnout službu zřizování v tomto Azure Portal.

> [!TIP]
> Ve výchozím nastavení se při zapnutí služby zřizování spustí operace zřizování pro všechny uživatele v oboru. Pokud dojde k chybám při mapování nebo potížích s daty SuccessFactors, úloha zřizování může selhat a přejít do stavu karantény. Aby k tomu nedocházelo, doporučujeme nakonfigurovat filtr **oboru zdrojového objektu** a otestovat mapování atributů s několika testovacími uživateli pomocí [zřizování na vyžádání](../app-provisioning/provision-on-demand.md) před spuštěním úplné synchronizace pro všechny uživatele. Jakmile ověříte, že mapování funguje a poskytuje požadované výsledky, můžete buď odebrat filtr, nebo ho postupně rozšířit, aby zahrnoval více uživatelů.

1. Přejděte na okno **zřizování** a klikněte na **Spustit zřizování**.

1. Tato operace spustí počáteční synchronizaci, což může trvat proměnlivý počet hodin v závislosti na tom, kolik uživatelů je v tenantovi SuccessFactors. V indikátoru průběhu můžete sledovat průběh cyklu synchronizace. 

1. Na kartě **protokoly auditu** v Azure Portal můžete kdykoli zjistit, jaké akce služba zřizování provedla. Protokoly auditu vypíše všechny jednotlivé události synchronizace prováděné službou zřizování, například které uživatele čtou z SuccessFactors a následně se přidají nebo aktualizují ve službě Active Directory. 

1. Po dokončení počáteční synchronizace bude na kartě **zřizování** napsána Sestava souhrnu auditu, jak je znázorněno níže.

   > [!div class="mx-imgBorder"]
   > ![Indikátor průběhu zřizování](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="next-steps"></a>Další kroky

* [Další informace o podporovaných atributech SuccessFactors pro příchozí zřizování](../app-provisioning/sap-successfactors-attribute-reference.md)
* [Informace o tom, jak nakonfigurovat zpětný zápis e-mailu na SuccessFactors](sap-successfactors-writeback-tutorial.md)
* [Zjistěte, jak procházet protokoly a získat sestavy aktivit zřizování](../app-provisioning/check-status-user-account-provisioning.md).
* [Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi SuccessFactors a Azure Active Directory](successfactors-tutorial.md)
* [Naučte se integrovat další aplikace SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Naučte se exportovat a importovat vaše konfigurace zřizování.](../app-provisioning/export-import-provisioning-configuration.md)