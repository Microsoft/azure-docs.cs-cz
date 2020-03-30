---
title: 'Kurz: Konfigurace zpětného zápisu faktorů úspěchu ve službě Azure Active Directory | Dokumenty společnosti Microsoft'
description: Zjistěte, jak nakonfigurovat zpětný zápis atributu na SuccessFactors z Azure AD
services: active-directory
author: cmmdesai
documentationcenter: na
manager: jodadzie
ms.assetid: ad255bd4-9e50-43a1-a92b-359215868b6b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2019
ms.author: chmutali
ms.openlocfilehash: 2de0cdd32428884170f549afacdbd52c3a10c93f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77060044"
---
# <a name="tutorial-configure-attribute-writeback-from-azure-ad-to-sap-successfactors-preview"></a>Kurz: Konfigurace zpětného zápisu atributu z Azure AD na SAP SuccessFactors (Preview)
Cílem tohoto kurzu je zobrazit kroky, které je třeba provést k zpětnému zápisu atributy z Azure AD na SuccessFactors Employee Central. Jediným atributem, který je aktuálně podporován pro zpětný zápis, je atribut e-mailu. 

## <a name="overview"></a>Přehled

Po nastavení integrace příchozí zřizování pomocí SuccessFactors na místní aplikaci pro zřizování [služby AD](sap-successfactors-inbound-provisioning-tutorial.md) nebo SuccessFactors do aplikace pro zřizování [Azure AD](sap-successfactors-inbound-provisioning-cloud-only-tutorial.md) můžete volitelně nakonfigurovat aplikaci SuccessFactors Writeback tak, aby e-mailovou adresu zapisovala zpět na SuccessFactors. 

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Pro koho je toto zřizovací řešení uživatele nejvhodnější?

Toto řešení zřizování uživatelů odepisování SuccessFactors writeback je ideální pro:

* Organizace používající Office 365, které chtějí odepsat autoritativní atributy spravované IT (například e-mailovou adresu) zpět na SuccessFactors

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

## <a name="configuring-successfactors-writeback"></a>Konfigurace zpětného zápisu faktorů úspěchu

Tato část obsahuje postup pro 

* [Přidání aplikace konektoru zřizování a konfigurace připojení k SuccessFactors](#part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors)
* [Konfigurace mapování atributů](#part-2-configure-attribute-mappings)
* [Povolení a spuštění zřizování uživatelů](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors"></a>Část 1: Přidání aplikace konektoru zřizování a konfigurace připojení k SuccessFactors

**Konfigurace zpětného zápisu SuccessFactors:**

1. Přejděte na <https://portal.azure.com>.

2. Na levém navigačním panelu vyberte **Azure Active Directory.**

3. Vyberte **podnikové aplikace**, potom **všechny aplikace**.

4. Vyberte **Přidat aplikaci**a vyberte kategorii **Vše.**

5. Vyhledejte **successfactors writeback**a přidejte tuto aplikaci z galerie.

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
    >![Azure Portal](./media/sap-successfactors-inbound-provisioning/sfwb-provisioning-creds.png)

   * Po úspěšném uložení přihlašovacích údajů se v části **Mapování** zobrazí výchozí mapování **Synchronizovat uživatele služby Azure Active Directory s faktory úspěchu.**

### <a name="part-2-configure-attribute-mappings"></a>Část 2: Konfigurace mapování atributů

V této části nakonfigurujete způsob toku uživatelských dat z faktorů Úspěchdo služby Active Directory.

1. Na kartě Zřizování v části **Mapování**klikněte na **Synchronizovat uživatele služby Azure Active Directory s faktory úspěchu**.

1. V poli **Obor zdrojového objektu** můžete vybrat, které sady uživatelů ve službě Azure AD by měly být považovány za zpětný zápis, definováním sady filtrů založených na atributech. Výchozí obor je "všichni uživatelé ve službě Azure AD". 
   > [!TIP]
   > Při konfiguraci zřizovací aplikace poprvé, budete muset otestovat a ověřit mapování atributů a výrazy a ujistěte se, že je vám požadovaný výsledek. Společnost Microsoft doporučuje používat filtry oborů v části **Obor zdrojového objektu** k testování mapování s několika testovacími uživateli z Azure AD. Jakmile ověříte, že mapování funguje, můžete filtr odebrat nebo jej postupně rozbalit tak, aby zahrnoval více uživatelů.

1. Pole **Akce cílového objektu** podporuje pouze operaci **Aktualizace.**

1. V části **Mapování atributů** můžete změnit jenom odpovídající ID, které se používá k propojení profilu uživatele SuccessFactors s uživatelem Azure AD a který atribut ve službě Azure AD slouží jako zdroj e-mailu. 
    >[!div class="mx-imgBorder"]
    >![Azure Portal](./media/sap-successfactors-inbound-provisioning/sfwb-attribute-mapping.png)

   >[!NOTE]
   >Zpětné zápisu SuccessFactors podporuje pouze atribut e-mailu. Nepoužívejte **přidat nové mapování** k přidání nových atributů. 

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

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy o aktivitě zřizování.](../app-provisioning/check-status-user-account-provisioning.md)
* [Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi SuccessFactors a Azure Active Directory](successfactors-tutorial.md)
* [Zjistěte, jak integrovat další aplikace SaaS s Azure Active Directory](tutorial-list.md)
* [Přečtěte si, jak exportovat a importovat konfigurace zřizování](../app-provisioning/export-import-provisioning-configuration.md)

