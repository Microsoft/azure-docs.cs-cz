---
title: 'Kurz: Konfigurace zpětného zápisu SuccessFactors v Azure Active Directory | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat zpětný zápis atributů na SuccessFactors ze služby Azure AD.
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
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2020
ms.locfileid: "77060044"
---
# <a name="tutorial-configure-attribute-writeback-from-azure-ad-to-sap-successfactors-preview"></a>Kurz: Konfigurace zpětného zápisu atributu z Azure AD na SAP SuccessFactors (Preview)
Cílem tohoto kurzu je Ukázat kroky, které musíte provést při zpětném zápisu atributů z Azure AD do SuccessFactors zaměstnanců centrálně. Jediným atributem, který je aktuálně podporován pro zpětný zápis, je atribut e-mail. 

## <a name="overview"></a>Přehled

Když nastavíte příchozí integraci zřizování s použitím [SuccessFactors do místní](sap-successfactors-inbound-provisioning-tutorial.md) aplikace pro zřizování služby AD nebo [SuccessFactors do služby Azure AD](sap-successfactors-inbound-provisioning-cloud-only-tutorial.md) Provisioning, můžete volitelně nakonfigurovat aplikaci SuccessFactors zpětného zápisu tak, aby zapisovala e-mailovou adresu zpátky na SuccessFactors. 

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Na koho se toto řešení pro zřizování uživatelů nejlépe hodí?

Toto řešení pro zajištění zpětného zápisu uživatelů SuccessFactors se ideálním způsobem hodí pro:

* Organizace využívající Office 365, které chtějí vracet autoritativní atributy spravované IT (například e-mailové adresy) zpět do SuccessFactors

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
* Klikněte na **Hotovo**. Klikněte na **Uložit změny**.

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
* Klikněte na **Uložit změny**.

## <a name="configuring-successfactors-writeback"></a>Konfigurace zpětného zápisu SuccessFactors

Tato část popisuje kroky pro 

* [Přidání aplikace zřizovacího konektoru a konfigurace připojení k SuccessFactors](#part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors)
* [Konfigurace mapování atributů](#part-2-configure-attribute-mappings)
* [Povolení a spuštění zřizování uživatelů](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors"></a>Část 1: Přidání aplikace konektoru pro zřizování a konfigurace připojení k SuccessFactors

**Konfigurace zpětného zápisu SuccessFactors:**

1. Přejděte na <https://portal.azure.com>.

2. V levém navigačním panelu vyberte **Azure Active Directory**

3. Vyberte **podnikové aplikace**a pak **všechny aplikace**.

4. Vyberte **Přidat aplikaci**a vyberte kategorii **vše** .

5. Vyhledejte **zpětný zápis SuccessFactors**a přidejte tuto aplikaci z galerie.

6. Až se aplikace přidá a zobrazí se obrazovka s podrobnostmi aplikace, vyberte **zřizování** .

7. Změnit režim **zřizování** na **automaticky**

8. Dokončete část **přihlašovací údaje správce** následujícím způsobem:

   * **Uživatelské jméno správce** – zadejte uživatelské jméno uživatelského účtu rozhraní SuccessFactors API s připojením ID společnosti. Má formát: **username\@companyID**

   * **Heslo správce –** Zadejte heslo uživatelského účtu rozhraní SuccessFactors API. 

   * **Adresa URL tenanta –** Zadejte název koncového bodu SuccessFactors OData API Services. Zadejte pouze název hostitele serveru bez protokolu HTTP nebo HTTPS. Tato hodnota by měla vypadat takto: **API-Server-Name.SuccessFactors.com**.

   * **E-mail s oznámením –** Zadejte svou e-mailovou adresu a zaškrtněte políčko Odeslat e-mail, pokud dojde k chybě.
    > [!NOTE]
    > Služba zřizování Azure AD pošle e-mailové oznámení, pokud úloha zřizování přejde do stavu [karantény](/azure/active-directory/manage-apps/application-provisioning-quarantine-status) .

   * Klikněte na tlačítko **Testovat připojení** . Pokud je test připojení úspěšný, klikněte na tlačítko **Uložit** v horní části. Pokud se to nepovede, dvakrát ověřte, že jsou přihlašovací údaje SuccessFactors a adresa URL platné.
    >[!div class="mx-imgBorder"]
    >![Azure Portal](./media/sap-successfactors-inbound-provisioning/sfwb-provisioning-creds.png)

   * Po úspěšném uložení přihlašovacích údajů se v oddílu **mapování** zobrazí výchozí mapování **synchronizovat Azure Active Directory SuccessFactors uživatelé** .

### <a name="part-2-configure-attribute-mappings"></a>Část 2: Konfigurace mapování atributů

V této části nakonfigurujete, jak budou data uživatelů z SuccessFactors do služby Active Directory přetékat.

1. Na kartě zřizování v části **mapování**klikněte na **synchronizovat Azure Active Directory uživatelé SuccessFactors**.

1. V poli **obor zdrojového objektu** můžete vybrat, které sady uživatelů v Azure AD by se měly považovat za zpětný zápis, a to definováním sady filtrů založených na atributech. Výchozí obor je "Všichni uživatelé v Azure AD". 
   > [!TIP]
   > Při první konfiguraci zřizovací aplikace budete muset otestovat a ověřit mapování atributů a výrazy, abyste se ujistili, že vám poskytne požadovaný výsledek. Microsoft doporučuje pomocí filtrů oborů v **oboru zdrojového objektu** testovat mapování s několika testovacími uživateli z Azure AD. Jakmile ověříte, že mapování funguje, můžete buď odebrat filtr, nebo ho postupně rozšířit, aby zahrnoval více uživatelů.

1. Pole **Akce cílového objektu** podporuje pouze operaci **aktualizace** .

1. V části **mapování atributů** můžete změnit jenom odpovídající ID, které se používá k propojení profilu uživatele SuccessFactors s uživatelem Azure AD a který atribut ve službě Azure AD slouží jako zdroj e-mailu. 
    >[!div class="mx-imgBorder"]
    >![Azure Portal](./media/sap-successfactors-inbound-provisioning/sfwb-attribute-mapping.png)

   >[!NOTE]
   >Zpětný zápis SuccessFactors podporuje pouze atribut e-mail. Nepoužívejte prosím **Přidat nové mapování** pro přidání nových atributů. 

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

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy pro aktivitu zřizování.](../app-provisioning/check-status-user-account-provisioning.md)
* [Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi SuccessFactors a Azure Active Directory](successfactors-tutorial.md)
* [Naučte se integrovat další aplikace SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Naučte se exportovat a importovat vaše konfigurace zřizování.](../app-provisioning/export-import-provisioning-configuration.md)

