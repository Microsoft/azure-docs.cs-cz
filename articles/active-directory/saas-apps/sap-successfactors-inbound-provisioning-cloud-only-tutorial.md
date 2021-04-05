---
title: 'Kurz: Konfigurace SuccessFactors příchozího zřizování v Azure Active Directory | Microsoft Docs'
description: Naučte se konfigurovat příchozí zřizování z SuccessFactors do Azure AD.
services: active-directory
author: cmmdesai
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.topic: tutorial
ms.workload: identity
ms.date: 01/19/2021
ms.author: chmutali
ms.openlocfilehash: a39f7701d0244eff7f914908a6708dfc14873b35
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98954851"
---
# <a name="tutorial-configure-sap-successfactors-to-azure-ad-user-provisioning"></a>Kurz: Konfigurace zřizování SAP SuccessFactors pro uživatele Azure AD
Cílem tohoto kurzu je Ukázat kroky, které musíte provést při zřizování pracovních dat od SuccessFactors zaměstnanců do Azure Active Directory, s volitelným zpětným zápisem e-mailové adresy SuccessFactors. 

>[!NOTE]
>Tento kurz použijte v případě, že uživatelé, které chcete zřídit z SuccessFactors, jsou jenom uživatelé s podporou cloudu, kteří nepotřebují místní účet služby AD. Pokud uživatelé vyžadují jenom místní účet služby AD nebo účet AD i Azure AD, přečtěte si prosím kurz [konfigurace SAP SuccessFactors na zřizování uživatelů služby Active Directory](sap-successfactors-inbound-provisioning-tutorial.md#overview) . 

## <a name="overview"></a>Přehled

[Služba zřizování uživatelů Azure Active Directory](../app-provisioning/user-provisioning.md) se integruje s [SuccessFactors zaměstnanci](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html) , aby mohla spravovat životní cyklus identity uživatelů. 

Pracovní postupy zřizování uživatelů SuccessFactors, které podporuje služba zřizování uživatelů Azure AD, umožňují automatizaci následujících scénářů lidských zdrojů a životního cyklu identit:

* Připravují se **noví zaměstnanci** – když se do SuccessFactors přidá nový zaměstnanec, automaticky se vytvoří uživatelský účet v Azure Active Directory a volitelně Microsoft 365 a [Další SaaS aplikace, které Azure AD podporuje](../app-provisioning/user-provisioning.md), a to s zpětným zápisem e-mailové adresy na SuccessFactors.

* **Aktualizace atributů a profilů zaměstnanců** – když se v SuccessFactors aktualizuje záznam zaměstnance (například jeho jméno, název nebo manažer), automaticky se aktualizuje jeho uživatelský účet Azure Active Directory a případně Microsoft 365 a [Další aplikace SaaS, které Azure AD podporuje](../app-provisioning/user-provisioning.md).

* **Ukončení zaměstnanců** – když se zaměstnanec v SuccessFactors ukončí, jejich uživatelský účet se automaticky zakáže v Azure Active Directory a volitelně Microsoft 365 a [Další SaaS aplikace podporované službou Azure AD](../app-provisioning/user-provisioning.md).

* **Pracovní zařazení zaměstnanců** – když se zaměstnanec v SuccessFactorsě odkoupí, jeho starý účet se dá automaticky znovu aktivovat nebo znovu zřídit (v závislosti na vaší preferenci) a volitelně Microsoft 365 a [Další SaaS aplikace, Azure Active Directory které Azure AD podporuje](../app-provisioning/user-provisioning.md).

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Na koho se toto řešení pro zřizování uživatelů nejlépe hodí?

Tato SuccessFactors Azure Active Directory řešení zřizování uživatelů je ideální pro:

* Organizace, které chtějí předem připravené cloudové řešení pro zřizování uživatelů SuccessFactors

* Organizace, které vyžadují přímé zřizování uživatelů od SuccessFactors po Azure Active Directory

* Organizace, které vyžadují zřízení uživatelů pomocí dat získaných od [SuccessFactors zaměstnanců (ES)](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html)

* Organizace, které používají Microsoft 365 k e-mailu

## <a name="solution-architecture"></a>Architektura řešení

Tato část popisuje kompletní architekturu řešení zřizování uživatelů jenom pro cloudové uživatele. Existují dva související toky:

* **Směrodatný tok dat o personálním oddělení – od SuccessFactors po Azure Active Directory:** V těchto událostech pracovního procesu toku (například nových náborů, přenosů, ukončení) se nejdříve narazí v cloudu SuccessFactors zaměstnanci a pak se data události nastanou do Azure Active Directory. V závislosti na události to může vést k vytváření, aktualizaci, povolování a zakazování operací v Azure AD.
* **Tok zpětného zápisu e-mailu – z místní služby Active Directory do SuccessFactors:** Po vytvoření účtu v Azure Active Directory se hodnota atributu e-mail nebo hlavní název uživatele vygenerovaný ve službě Azure AD může zapsat zpátky do SuccessFactors.

  ![Přehled](./media/sap-successfactors-inbound-provisioning/sf2aad-overview.png)

### <a name="end-to-end-user-data-flow"></a>Koncový tok dat uživatele

1. Tým HR provádí pracovní transakce (Spojovacíky/stěhovací společnosti/Leaversy nebo noví zaměstnanci, přenosy a ukončení) v SuccessFactors zaměstnanci – střed
2. Služba zřizování Azure AD spouští naplánované synchronizace identit z SuccessFactors ES a identifikuje změny, které je potřeba zpracovat pro synchronizaci s místní službou Active Directory.
3. Služba zřizování Azure AD tuto změnu určí a vyvolá pro uživatele ve službě Azure AD operaci vytvořit/aktualizovat/povolit/zakázat.
4. Pokud je nakonfigurovaná [aplikace pro zpětný zápis SuccessFactors](sap-successfactors-writeback-tutorial.md) , načte se e-mailová adresa uživatele z Azure AD. 
5. Služba zřizování Azure AD zapisuje zpátky atribut e-mailu na SuccessFactors na základě používaného odpovídajícího atributu.

## <a name="planning-your-deployment"></a>Plánování nasazení

Konfigurace zřizování uživatelů cloudu s využitím cloudu z SuccessFactors do Azure AD vyžaduje výrazné plánování zahrnující různé aspekty, jako je:

* Určení odpovídajícího ID 
* Mapování atributů
* Transformace atributu 
* Filtry oborů

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

## <a name="configuring-user-provisioning-from-successfactors-to-azure-ad"></a>Konfigurace zřizování uživatelů z SuccessFactors do Azure AD

Tato část popisuje kroky pro zřizování uživatelských účtů z SuccessFactors do Azure AD.

* [Přidání aplikace zřizovacího konektoru a konfigurace připojení k SuccessFactors](#part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors)
* [Konfigurace mapování atributů](#part-2-configure-attribute-mappings)
* [Povolení a spuštění zřizování uživatelů](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors"></a>Část 1: Přidání aplikace konektoru pro zřizování a konfigurace připojení k SuccessFactors

**Konfigurace SuccessFactors na zřizování služby Azure AD:**

1. Přejděte na <https://portal.azure.com>.

2. V levém navigačním panelu vyberte **Azure Active Directory**

3. Vyberte **podnikové aplikace** a pak **všechny aplikace**.

4. Vyberte **Přidat aplikaci** a vyberte kategorii **vše** .

5. Vyhledejte **SuccessFactors pro Azure Active Directory zřizování uživatelů** a přidejte tuto aplikaci z galerie.

6. Až se aplikace přidá a zobrazí se obrazovka s podrobnostmi aplikace, vyberte **zřizování** .

7. Změnit režim **zřizování**  na **automaticky**

8. Dokončete část **přihlašovací údaje správce** následujícím způsobem:

   * **Uživatelské jméno správce** – zadejte uživatelské jméno uživatelského účtu rozhraní SuccessFactors API s připojením ID společnosti. Má formát: **UserName \@ companyID**

   * **Heslo správce –** Zadejte heslo uživatelského účtu rozhraní SuccessFactors API. 

   * **Adresa URL tenanta –** Zadejte název koncového bodu SuccessFactors OData API Services. Zadejte pouze název hostitele serveru bez protokolu HTTP nebo HTTPS. Tato hodnota by měla vypadat takto: **API-Server-Name.SuccessFactors.com**.

   * **E-mail s oznámením –** Zadejte svou e-mailovou adresu a zaškrtněte políčko Odeslat e-mail, pokud dojde k chybě.
    > [!NOTE]
    > Služba zřizování Azure AD pošle e-mailové oznámení, pokud úloha zřizování přejde do stavu [karantény](../app-provisioning/application-provisioning-quarantine-status.md) .

   * Klikněte na tlačítko **Testovat připojení** . Pokud je test připojení úspěšný, klikněte na tlačítko **Uložit** v horní části. Pokud se to nepovede, dvakrát ověřte, že jsou přihlašovací údaje SuccessFactors a adresa URL platné.
    >[!div class="mx-imgBorder"]
    >![Azure Portal](./media/sap-successfactors-inbound-provisioning/sf2aad-provisioning-creds.png)

   * Po úspěšném uložení přihlašovacích údajů se v oddílu **mapování** zobrazí výchozí mapování **synchronizovat uživatele SuccessFactors s Azure Active Directory**

### <a name="part-2-configure-attribute-mappings"></a>Část 2: Konfigurace mapování atributů

V této části nakonfigurujete, jak budou data uživatelů z SuccessFactors do služby Active Directory přetékat.

1. Na kartě zřizování v části **mapování** klikněte na **synchronizovat uživatele SuccessFactors a Azure Active Directory**.

1. V poli **obor zdrojového objektu** můžete vybrat, které sady uživatelů v SuccessFactors by měly být v oboru pro zřizování do Azure AD, a to definováním sady filtrů založených na atributech. Výchozí obor je "Všichni uživatelé v SuccessFactors". Příklady filtrů:

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
   > Výchozím chováním modulu zřizování je zakázat nebo odstranit uživatele, kteří se přestanou přidělovat z oboru. To nemusí být žádoucí v SuccessFactors integrace služby Azure AD. Pokud chcete přepsat toto výchozí chování, přečtěte si článek [přeskočení odstranění uživatelských účtů, které se nacházejí mimo rozsah](../app-provisioning/skip-out-of-scope-deletions.md) .
  
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
   > ![Indikátor průběhu zřizování](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="next-steps"></a>Další kroky

* [Další informace o podporovaných atributech SuccessFactors pro příchozí zřizování](../app-provisioning/sap-successfactors-attribute-reference.md)
* [Informace o tom, jak nakonfigurovat zpětný zápis e-mailu na SuccessFactors](sap-successfactors-writeback-tutorial.md)
* [Zjistěte, jak procházet protokoly a získat sestavy aktivit zřizování](../app-provisioning/check-status-user-account-provisioning.md).
* [Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi SuccessFactors a Azure Active Directory](successfactors-tutorial.md)
* [Naučte se integrovat další aplikace SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Naučte se exportovat a importovat vaše konfigurace zřizování.](../app-provisioning/export-import-provisioning-configuration.md)