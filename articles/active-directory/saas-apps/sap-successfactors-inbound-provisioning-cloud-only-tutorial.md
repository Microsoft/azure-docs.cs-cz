---
title: 'Kurz: Konfigurace příchozího zřizování faktorů úspěchu ve službě Azure Active Directory | Dokumenty společnosti Microsoft'
description: Zjistěte, jak nakonfigurovat příchozí zřizování z Faktorů úspěchu do Azure AD
services: active-directory
author: cmmdesai
documentationcenter: na
manager: jodadzie
ms.assetid: fac4f61e-d942-4429-a298-9ba74db95077
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2019
ms.author: chmutali
ms.openlocfilehash: 09501a80d6ddcbbc9fa6cc08e36f47beb13d1663
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063218"
---
# <a name="tutorial-configure-sap-successfactors-to-azure-ad-user-provisioning-preview"></a>Kurz: Konfigurace SAP SuccessFactors pro zřizování uživatelů Azure AD (Preview)
Cílem tohoto kurzu je zobrazit kroky, které je třeba provést k zajištění dat pracovníka z SuccessFactors Employee Central do služby Azure Active Directory, s volitelným zpětným zápisem e-mailové adresy na SuccessFactors. Tato integrace je ve verzi Public Preview a podporuje načítání více než [70+ uživatelských atributů](../app-provisioning/sap-successfactors-attribute-reference.md) z SuccessFactors Employee Central. 

>[!NOTE]
>Tento kurz použijte, pokud uživatelé, které chcete zřídit z SuccessFactors jsou pouze cloud uživatelé, kteří nepotřebují místní účet služby AD. Pokud uživatelé vyžadují pouze místní účet AD nebo účet Služby AD i Azure AD, přečtěte si kurz o [konfiguraci SAP SuccessFactors pro](sap-successfactors-inbound-provisioning-tutorial.md#overview) zřizování uživatelů služby Active Directory. 

## <a name="overview"></a>Přehled

[Služba zřizování uživatelů služby Azure Active Directory](../app-provisioning/user-provisioning.md) se integruje s [centrálou zaměstnanců SuccessFactors Za](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html) účelem správy životního cyklu identity uživatelů. 

Pracovní postupy zřizování uživatelů SuccessFactors podporované službou zřizování uživatelů Azure AD umožňují automatizaci následujících lidských prostředků a scénářů správy životního cyklu identit:

* **Nábor nových zaměstnanců** – když je do SuccessFactors přidán nový zaměstnanec, automaticky se vytvoří uživatelský účet ve službě Azure Active Directory a volitelně Office 365 a [dalších aplikacíCh SaaS podporovaných službou Azure AD](../app-provisioning/user-provisioning.md)s zpětným zápisem e-mailové adresy na SuccessFactors.

* **Aktualizace atributů a profilů zaměstnance** – když se záznam zaměstnance aktualizuje v faktorech úspěchu (například jejich jméno, titul nebo správce), jejich uživatelský účet se automaticky aktualizuje Azure Active Directory a volitelně Office 365 a [další aplikace SaaS podporované službou Azure AD](../app-provisioning/user-provisioning.md).

* **Ukončení zaměstnanců** – když je zaměstnanec ukončen v SuccessFactors, jeho uživatelský účet se automaticky deaktivuje ve službě Azure Active Directory a volitelně Office 365 a [dalších aplikací SaaS podporovaných službou Azure AD](../app-provisioning/user-provisioning.md).

* **Opětovné přijetí zaměstnanců** – když je zaměstnanec znovu přijat v SuccessFactors, jeho starý účet může být automaticky znovu aktivován nebo znovu zřízen (v závislosti na vašich preferencích) do služby Azure Active Directory a volitelně Office 365 a [dalších aplikací SaaS podporovaných službou Azure AD](../app-provisioning/user-provisioning.md).

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Pro koho je toto zřizovací řešení uživatele nejvhodnější?

Toto řešení zřizování uživatelů služby Azure Active Directory je ideální pro:

* Organizace, které si přejí předem vytvořené cloudové řešení pro zřizování uživatelů SuccessFactors

* Organizace, které vyžadují přímé zřizování uživatelů z SuccessFactors do Služby Azure Active Directory

* Organizace, které vyžadují, aby uživatelé byli zřízeni pomocí dat získaných z [SuccessFactors Employee Central (EC)](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html)

* Organizace používající Office 365 pro e-mail

## <a name="solution-architecture"></a>Architektura řešení

Tato část popisuje architekturu řešení zřizování koncových uživatelů pro uživatele pouze v cloudu. Existují dva související toky:

* **Autoritativní tok dat lidských zdrojů – od faktorů úspěchu po Azure Active Directory:** V tomto události toku pracovního procesu (například nové najímá, převody, ukončení) nejprve dojít v cloudu SuccessFactors Employee Central a potom data událostí toky do služby Azure Active Directory. V závislosti na události může vést k vytvoření/aktualizovat/povolit/zakázat operace ve službě Azure AD.
* **Tok zpětného zápisu e-mailu – z místního adresáře Active Directory do successuFaktory:** Po dokončení vytvoření účtu ve službě Azure Active Directory, hodnota atributu e-mailu nebo UPN generované ve službě Azure AD lze zapsat zpět do SuccessFactors.

  ![Přehled](./media/sap-successfactors-inbound-provisioning/sf2aad-overview.png)

### <a name="end-to-end-user-data-flow"></a>Tok dat koncových uživatelů

1. Hr tým provádí pracovní transakce (Truhláři/Stěhováci/Opouštějící nebo Nové najímá/převádí/ukončení) v SuccessFactors Employee Central
2. Služba zřizování Azure AD spouští naplánované synchronizace identit z SuccessFactors EC a identifikuje změny, které je třeba zpracovat pro synchronizaci s místní službou Active Directory.
3. Služba zřizování Azure AD určuje změnu a vyvolá operaci vytvoření/aktualizace/povolení/zakázání pro uživatele ve službě Azure AD.
4. Pokud [successFactors writeback aplikace](sap-successfactors-writeback-tutorial.md) je nakonfigurovaná, pak e-mailová adresa uživatele se načte z Azure AD. 
5. Služba zřizování Azure AD odepíše atribut e-mailu na SuccessFactors na základě použitého odpovídajícího atributu.

## <a name="planning-your-deployment"></a>Plánování nasazení

Konfigurace zřizování uživatelů řízených cloudovým hr z SuccessFactors do Azure AD vyžaduje značné plánování pokrývající různé aspekty, jako jsou:

* Určení ID shody 
* Mapování atributů
* Transformace atributu 
* Filtry oborů

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

## <a name="configuring-user-provisioning-from-successfactors-to-azure-ad"></a>Konfigurace zřizování uživatelů z SuccessFactors do Azure AD

Tato část obsahuje kroky pro zřizování uživatelských účtů z SuccessFactors do Azure AD.

* [Přidání aplikace konektoru zřizování a konfigurace připojení k SuccessFactors](#part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors)
* [Konfigurace mapování atributů](#part-2-configure-attribute-mappings)
* [Povolení a spuštění zřizování uživatelů](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors"></a>Část 1: Přidání aplikace konektoru zřizování a konfigurace připojení k SuccessFactors

**Konfigurace SuccessFactors pro zřizování Azure AD:**

1. Přejděte na <https://portal.azure.com>.

2. Na levém navigačním panelu vyberte **Azure Active Directory.**

3. Vyberte **podnikové aplikace**, potom **všechny aplikace**.

4. Vyberte **Přidat aplikaci**a vyberte kategorii **Vše.**

5. Vyhledejte **SuccessFactors na Azure Active Directory User Zřizování**a přidejte tuto aplikaci z galerie.

6. Po přidání aplikace a zobrazení obrazovky s podrobnostmi o aplikaci vyberte **Zřídit**

7. Změna **režimu** **zřizování** na **automatický**

8. Dokončete oddíl **Pověření správce** takto:

   * **Admin Uživatelské jméno** – Zadejte uživatelské jméno SuccessFactors API uživatelského účtu, s ID společnosti připojen. Má formát: **uživatelské\@jméno companyID**

   * **Admin heslo -** Zadejte heslo uživatelského účtu rozhraní API SuccessFactors. 

   * **Adresa URL klienta –** Zadejte název koncového bodu služby SuccessFactors OData API. Zadejte pouze název hostitele serveru bez http nebo https. Tato hodnota by měla vypadat takto: **api-server-name.successfactors.com**.

   * **E-mail s oznámením –** Zadejte svou e-mailovou adresu a zaškrtněte políčko Odeslat e-mail, pokud dojde k selhání.
    > [!NOTE]
    > Služba zřizování Azure AD odešle e-mailové oznámení, pokud úloha zřizování přejde do [stavu karantény.](/azure/active-directory/manage-apps/application-provisioning-quarantine-status)

   * Klepněte na tlačítko **Testovat připojení.** Pokud je test připojení úspěšný, klepněte na tlačítko **Uložit** nahoře. Pokud se nezdaří, zkontrolujte, zda jsou platná pověření SuccessFactors a adresa URL.
    >[!div class="mx-imgBorder"]
    >![Azure Portal](./media/sap-successfactors-inbound-provisioning/sf2aad-provisioning-creds.png)

   * Po úspěšném uložení přihlašovacích údajů se v části **Mapování** zobrazí výchozí mapování **Synchronizovat uživatele SuccessFactors users do služby Azure Active Directory.**

### <a name="part-2-configure-attribute-mappings"></a>Část 2: Konfigurace mapování atributů

V této části nakonfigurujete způsob toku uživatelských dat z faktorů Úspěchdo služby Active Directory.

1. Na kartě Zřizování v části **Mapování**klikněte na **synchronizovat uživatele SuccessFactors users do služby Azure Active Directory**.

1. V poli **Obor zdrojového objektu** můžete vybrat, které sady uživatelů v SuccessFactors by měly být v oboru pro zřizování do služby Azure AD, definováním sady filtrů založených na atributech. Výchozí obor je "všichni uživatelé v SuccessFactors". Příklady filtrů:

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
   > Výchozí chování zřizovacího modulu je zakázat nebo odstranit uživatele, kteří přejdou mimo rozsah. To nemusí být žádoucí ve vaší SuccessFactors integrace Azure AD. Chcete-li přepsat toto výchozí chování, přečtěte si článek [Přeskočit odstranění uživatelských účtů, které jsou mimo rozsah.](../app-provisioning/skip-out-of-scope-deletions.md)
  
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
> Ve výchozím nastavení při zapnutí zřizovací služby zahájí operace zřizování pro všechny uživatele v oboru. Pokud jsou chyby v mapování nebo Workday data problémy, pak úloha zřizování může selhat a přejít do stavu karantény. Chcete-li tomu zabránit, doporučujeme jako osvědčený postup konfiguraci filtru **oboru zdrojového objektu** a testování mapování atributů s několika testovacími uživateli před spuštěním úplné synchronizace pro všechny uživatele. Jakmile ověříte, že mapování funguje a dáváte vám požadované výsledky, můžete filtr odebrat nebo jej postupně rozšířit tak, aby zahrnoval více uživatelů.

1. Na kartě **Zřizování** nastavte **stav zřizování** **na Zapnuto**.

2. Klikněte na **Uložit**.

3. Tato operace spustí počáteční synchronizaci, která může trvat proměnný počet hodin v závislosti na tom, kolik uživatelů je v tenantovi SuccessFactors. Můžete zkontrolovat indikátor průběhu ke sledování průběhu cyklu synchronizace. 

4. Kdykoli zkontrolujte kartu **Protokoly auditu** na webu Azure Portal a zjistěte, jaké akce služba zřizování provedla. Protokoly auditu uvádí všechny jednotlivé události synchronizace prováděné zřizovací službou, například které uživatelé jsou čteny mimo Pracovní den a následně přidány nebo aktualizovány do služby Active Directory. 

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


