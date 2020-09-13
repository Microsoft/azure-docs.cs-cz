---
title: 'Kurz: Konfigurace zpětného zápisu SAP SuccessFactors v Azure Active Directory | Microsoft Docs'
description: Naučte se konfigurovat zápis atributů zpátky do SAP SuccessFactors ze služby Azure AD.
services: active-directory
author: cmmdesai
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.topic: article
ms.workload: identity
ms.date: 08/05/2020
ms.author: chmutali
ms.openlocfilehash: b185f29cea61b9c366714a1af72648aeee35b61c
ms.sourcegitcommit: 43558caf1f3917f0c535ae0bf7ce7fe4723391f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2020
ms.locfileid: "90017927"
---
# <a name="tutorial-configure-attribute-write-back-from-azure-ad-to-sap-successfactors"></a>Kurz: Konfigurace zpětného zápisu atributu z Azure AD do SAP SuccessFactors
Cílem tohoto kurzu je Ukázat kroky pro atributy zpětného zápisu z Azure AD do SAP SuccessFactors Employee Central. 

## <a name="overview"></a>Přehled

Můžete nakonfigurovat aplikaci SAP SuccessFactors zpětného zápisu pro zápis konkrétních atributů z Azure Active Directory do SAP SuccessFactors Employee Central. Aplikace pro zřizování zpětného zápisu SuccessFactors podporuje přiřazování hodnot následujícím hlavním atributům pro zaměstnance:

* Pracovní E-mail
* Uživatelské jméno
* Firemní telefonní číslo (včetně kódu země, kódu oblasti, čísla a rozšíření)
* Primární příznak podnikového telefonního čísla
* Číslo mobilního telefonu (včetně kódu země, směrové číslo oblasti, číslo)
* Příznak primární mobilní telefon 
* User custom01-Custom15 – atributy
* loginMethod – atribut

> [!NOTE]
> Tato aplikace nemá žádnou závislost na SuccessFactorsch integračních aplikacích pro zřizování uživatelů v Inbound. Můžete ji nakonfigurovat nezávisle na [SuccessFactors do místní](sap-successfactors-inbound-provisioning-tutorial.md) aplikace pro zřizování služby AD nebo [SuccessFactors k](sap-successfactors-inbound-provisioning-cloud-only-tutorial.md) aplikaci zřizování služby Azure AD.

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Na koho se toto řešení pro zřizování uživatelů nejlépe hodí?

Toto řešení pro zajištění zpětného zápisu uživatelů SuccessFactors se ideálním způsobem hodí pro:

* Organizace, které používají Microsoft 365, které chtějí použít autoritativní atributy se zpětným zápisem (jako je e-mailová adresa, telefon, uživatelské jméno) zpátky na SuccessFactors zaměstnance – střed

## <a name="configuring-successfactors-for-the-integration"></a>Konfigurace SuccessFactors pro integraci

Všechny zřizovací konektory SuccessFactors vyžadují přihlašovací údaje účtu SuccessFactors se správnými oprávněními k vyvolání centrálních rozhraní OData API pro zaměstnance. Tato část popisuje kroky pro vytvoření účtu služby v SuccessFactors a udělení příslušných oprávnění. 

* [Vytvoření nebo identifikace uživatelského účtu rozhraní API v SuccessFactors](#createidentify-api-user-account-in-successfactors)
* [Vytvoření role oprávnění API](#create-an-api-permissions-role)
* [Vytvoření skupiny oprávnění pro uživatele rozhraní API](#create-a-permission-group-for-the-api-user)
* [Udělení role oprávnění skupině oprávnění](#grant-permission-role-to-the-permission-group)

### <a name="createidentify-api-user-account-in-successfactors"></a>Vytvoření nebo identifikace uživatelského účtu rozhraní API v SuccessFactors
Spolupracujte s týmem správce SuccessFactors nebo partnerem pro implementaci a vytvořte nebo identifikujte uživatelský účet v SuccessFactors, který se použije k vyvolání rozhraní OData API. Přihlašovací údaje uživatelského jména a hesla tohoto účtu se budou vyžadovat při konfiguraci zřizovacích aplikací ve službě Azure AD. 

### <a name="create-an-api-permissions-role"></a>Vytvoření role oprávnění API

1. Přihlaste se k SAP SuccessFactors pomocí uživatelského účtu, který má přístup k centru pro správu.
1. Vyhledejte *možnosti spravovat role oprávnění*a pak ve výsledcích hledání vyberte **Spravovat role oprávnění** .

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

1. Posuňte se dolů ve stejném poli a vyberte **centrální rozhraní API pro zaměstnance**. Přidáním oprávnění, jak je vidět níže, můžete číst pomocí rozhraní ODATA API a upravit pomocí rozhraní ODATA API. Vyberte možnost upravit, pokud plánujete použít stejný účet pro SuccessFactors scénář zpětného zápisu. 

   > [!div class="mx-imgBorder"]
   > ![Oprávnění ke čtení zápisu](./media/sap-successfactors-inbound-provisioning/odata-read-write-perm.png)

1. Klikněte na **Hotovo**. Klikněte na **Save Changes** (Uložit změny).

### <a name="create-a-permission-group-for-the-api-user"></a>Vytvoření skupiny oprávnění pro uživatele rozhraní API

1. V centru pro správu SuccessFactors vyhledejte *možnosti spravovat skupiny oprávnění*a pak ve výsledcích hledání vyberte **Spravovat skupiny oprávnění** .

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

1. V centru pro správu SuccessFactors vyhledejte *možnosti spravovat role oprávnění*a pak ve výsledcích hledání vyberte **Spravovat role oprávnění** .
1. V **seznamu role oprávnění**vyberte roli, kterou jste vytvořili pro oprávnění používání rozhraní API.
1. V části **udělení této role na...**, klikněte na tlačítko **Přidat..** ..
1. V rozevírací nabídce vyberte **skupinu oprávnění** a pak kliknutím na **Vybrat...** otevřete okno skupiny, kde můžete hledat a vybrat skupinu vytvořenou výše. 

   > [!div class="mx-imgBorder"]
   > ![Přidat skupinu oprávnění](./media/sap-successfactors-inbound-provisioning/add-permission-group.png)

1. Zkontrolujte roli oprávnění udělení skupiny oprávnění. 
   > [!div class="mx-imgBorder"]
   > ![Podrobnosti role a skupiny oprávnění](./media/sap-successfactors-inbound-provisioning/permission-role-group.png)

1. Klikněte na **Save Changes** (Uložit změny).

## <a name="preparing-for-successfactors-writeback"></a>Příprava pro zpětný zápis SuccessFactors

Aplikace pro zřizování zpětného zápisu SuccessFactors používá určité hodnoty *kódu* pro nastavení e-mailů a telefonních čísel v centru zaměstnanců. Tyto hodnoty *kódu* jsou nastaveny jako konstantní hodnoty v tabulce mapování atributů a jsou pro každou instanci SuccessFactors odlišné. V této části se používá [post](https://www.postman.com/downloads/) k načtení hodnot kódu. K posílání požadavků HTTP můžete použít [oblé](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler) nebo jakýkoli jiný podobný nástroj. 

### <a name="download-and-configure-postman-with-your-successfactors-tenant"></a>Stažení a konfigurace příspěvku s vaším tenant SuccessFactors

1. Stáhnout [příspěvek](https://www.postman.com/downloads/)
1. V aplikaci pro vystavení vytvořte novou kolekci. Zavolejte ho "SuccessFactors". 

   > [!div class="mx-imgBorder"]
   > ![Nová kolekce po](./media/sap-successfactors-inbound-provisioning/new-postman-collection.png)

1. Na kartě autorizace zadejte přihlašovací údaje uživatele rozhraní API nakonfigurovaného v předchozí části. Nakonfigurujte typ jako "základní ověřování". 

   > [!div class="mx-imgBorder"]
   > ![Autorizace po autorizaci](./media/sap-successfactors-inbound-provisioning/postman-authorization.png)

1. Konfiguraci uložte. 

### <a name="retrieve-constant-value-for-emailtype"></a>Načíst konstantní hodnotu pro emailType

1. V poli pro odeslání klikněte na tlačítko se třemi tečkami (...) spojené s kolekcí SuccessFactors a přidejte "nový požadavek" nazvaný "získat e-mailové typy", jak je znázorněno níže. 

   > [!div class="mx-imgBorder"]
   > ![Žádost o odeslání e-mailu ](./media/sap-successfactors-inbound-provisioning/postman-email-request.png)

1. Otevřete panel žádosti "získat typ e-mailu". 
1. Do pole získat adresu URL přidejte následující adresu URL, která nahrazuje `successFactorsAPITenantName` tenanta rozhraní API vaší instance SuccessFactors. 
   `https://<successfactorsAPITenantName>/odata/v2/Picklist('ecEmailType')?$expand=picklistOptions&$select=picklistOptions/id,picklistOptions/externalCode&$format=json`

   > [!div class="mx-imgBorder"]
   > ![Typ e-mailu po odeslání](./media/sap-successfactors-inbound-provisioning/postman-get-email-type.png)

1. Karta autorizace zdědí ověřování nakonfigurované pro kolekci. 
1. Pro vyvolání volání rozhraní API klikněte na Odeslat. 
1. V těle odpovědi si prohlédněte sadu výsledků JSON a vyhledejte ID odpovídající `externalCode = B` . 

   > [!div class="mx-imgBorder"]
   > ![Odpověď typu poštovního e-mailu](./media/sap-successfactors-inbound-provisioning/postman-email-type-response.png)

1. Poznamenejte si tuto hodnotu jako konstantu pro použití s *emailType* v tabulce mapování atributů.

### <a name="retrieve-constant-value-for-phonetype"></a>Načíst konstantní hodnotu pro phoneType

1. V poli pro odeslání klikněte na tlačítko se třemi tečkami (...) spojené s kolekcí SuccessFactors a přidejte "nový požadavek" nazvaný "získat telefonní typy", jak je znázorněno níže. 

   > [!div class="mx-imgBorder"]
   > ![Žádost o telefon po telefonu](./media/sap-successfactors-inbound-provisioning/postman-phone-request.png)

1. Otevřete panel žádosti "získat typ telefonu". 
1. Do pole získat adresu URL přidejte následující adresu URL, která nahrazuje `successFactorsAPITenantName` tenanta rozhraní API vaší instance SuccessFactors. 
   `https://<successfactorsAPITenantName>/odata/v2/Picklist('ecPhoneType')?$expand=picklistOptions&$select=picklistOptions/id,picklistOptions/externalCode&$format=json`

   > [!div class="mx-imgBorder"]
   > ![Typ pro telefon Get](./media/sap-successfactors-inbound-provisioning/postman-get-phone-type.png)

1. Karta autorizace zdědí ověřování nakonfigurované pro kolekci. 
1. Pro vyvolání volání rozhraní API klikněte na Odeslat. 
1. V těle odpovědi si prohlédněte sadu výsledků JSON a vyhledejte *ID* odpovídající `externalCode = B` a `externalCode = C` . 

   > [!div class="mx-imgBorder"]
   > ![Po telefonu](./media/sap-successfactors-inbound-provisioning/postman-phone-type-response.png)

1. Poznamenejte si tyto hodnoty jako konstanty pro použití s *businessPhoneType* a *cellPhoneType* v tabulce mapování atributů.

## <a name="configuring-successfactors-writeback-app"></a>Konfigurace aplikace pro zpětný zápis SuccessFactors

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

7. Změnit režim **zřizování** **Mode** na **automaticky**

8. Dokončete část **přihlašovací údaje správce** následujícím způsobem:

   * **Uživatelské jméno správce** – zadejte uživatelské jméno uživatelského účtu rozhraní SuccessFactors API s připojením ID společnosti. Má formát: **UserName \@ companyID**

   * **Heslo správce –** Zadejte heslo uživatelského účtu rozhraní SuccessFactors API. 

   * **Adresa URL tenanta –** Zadejte název koncového bodu SuccessFactors OData API Services. Zadejte pouze název hostitele serveru bez protokolu HTTP nebo HTTPS. Tato hodnota by měla vypadat takto: `api4.successfactors.com` .

   * **E-mail s oznámením –** Zadejte svou e-mailovou adresu a zaškrtněte políčko Odeslat e-mail, pokud dojde k chybě.
    > [!NOTE]
    > Služba zřizování Azure AD pošle e-mailové oznámení, pokud úloha zřizování přejde do stavu [karantény](/azure/active-directory/manage-apps/application-provisioning-quarantine-status) .

   * Klikněte na tlačítko **Testovat připojení** . Pokud je test připojení úspěšný, klikněte na tlačítko **Uložit** v horní části. Pokud se to nepovede, dvakrát ověřte, že jsou přihlašovací údaje SuccessFactors a adresa URL platné.
    >[!div class="mx-imgBorder"]
    >![Azure Portal](./media/sap-successfactors-inbound-provisioning/sfwb-provisioning-creds.png)

   * Po úspěšném uložení přihlašovacích údajů se v oddílu **mapování** zobrazí výchozí mapování. Aktualizujte stránku, pokud mapování atributů není viditelné.  

### <a name="part-2-configure-attribute-mappings"></a>Část 2: Konfigurace mapování atributů

V této části nakonfigurujete, jak budou data uživatelů z SuccessFactors do služby Active Directory přetékat.

1. Na kartě zřizování v části **mapování**klikněte na možnost **zřídit Azure Active Directory uživatelé**.

1. V poli **obor zdrojového objektu** můžete vybrat, které sady uživatelů ve službě Azure AD by se měly považovat za zpětný zápis definováním sady filtrů založených na atributech. Výchozí obor je "Všichni uživatelé v Azure AD". 
   > [!TIP]
   > Při první konfiguraci zřizovací aplikace budete muset otestovat a ověřit mapování atributů a výrazy, abyste se ujistili, že vám poskytne požadovaný výsledek. Microsoft doporučuje pomocí filtrů oborů v **oboru zdrojového objektu** testovat mapování s několika testovacími uživateli z Azure AD. Jakmile ověříte, že mapování funguje, můžete buď odebrat filtr, nebo ho postupně rozšířit, aby zahrnoval více uživatelů.

1. Pole **Akce cílového objektu** podporuje pouze operaci **aktualizace** .

1. V tabulce mapování v části **mapování atributů** lze následující atributy Azure Active Directory namapovat na SuccessFactors. Následující tabulka uvádí pokyny k namapování atributů se zpětným zápisem. 

   | \# | Atribut Azure AD | SuccessFactors – atribut | Poznámky |
   |--|--|--|--|
   | 1 | Zaměstnance | personIdExternal | Ve výchozím nastavení tento atribut odpovídá identifikátoru. Místo hodnoty ČísloZaměstnance můžete použít jakýkoli jiný atribut Azure AD, který může hodnotu v SuccessFactors ukládat, jako je personIdExternal.    |
   | 2 | pošta | e-mail | Mapovat zdroj atributu e-mailu Pro účely testování je možné mapovat userPrincipalName na e-mail. |
   | 3 | 8448 | emailType | Tato hodnota konstanty je hodnota ID SuccessFactors přidružené k obchodnímu e-mailu. Aktualizujte tuto hodnotu tak, aby odpovídala vašemu SuccessFactors prostředí. Postup pro nastavení této hodnoty naleznete v části [načtení konstantní hodnoty pro emailType](#retrieve-constant-value-for-emailtype) . |
   | 4 | true | emailIsPrimary | Tento atribut slouží k nastavení podnikového e-mailu jako primárního v SuccessFactors. Pokud obchodní e-mail není primární, nastavte tento příznak na false. |
   | 5 | userPrincipalName (Hlavní název uživatele) | [custom01 – custom15] | Pomocí **Přidat nové mapování**můžete volitelně napsat userPrincipalName nebo libovolný atribut Azure AD pro vlastní atribut dostupný v objektu uživatele SuccessFactors.  |
   | 6 | on-Prem-samAccountName | username | Pomocí **Přidat nové mapování**můžete volitelně mapovat místní atribut sAMAccountName na SuccessFactors atribut UserName. |
   | 7 | Jednotné přihlašování | loginMethod | Pokud je tenant SuccessFactors nastavený pro [částečné jednotné přihlašování](https://apps.support.sap.com/sap/support/knowledge/en/2320766), pak pomocí příkazu přidat nové mapování můžete volitelně nastavit loginMethod na konstantní hodnotu "SSO" nebo "PWD". |
   | 8 | telephoneNumber | businessPhoneNumber | Toto mapování použijte k Flow *telephoneNumber* z Azure AD až SuccessFactors Business/Work telefonního čísla. |
   | 9 | 10605 | businessPhoneType | Tato hodnota konstanty je hodnota ID SuccessFactors přidružená k firemnímu telefonu. Aktualizujte tuto hodnotu tak, aby odpovídala vašemu SuccessFactors prostředí. Postup pro nastavení této hodnoty naleznete v části [načtení konstantní hodnoty pro phoneType](#retrieve-constant-value-for-phonetype) . |
   | 10 | true | businessPhoneIsPrimary | Pomocí tohoto atributu můžete nastavit primární příznak pro firemní telefonní číslo. Platné hodnoty jsou true nebo false. |
   | 11 | mobil | cellPhoneNumber | Toto mapování použijte k Flow *telephoneNumber* z Azure AD až SuccessFactors Business/Work telefonního čísla. |
   | 12 | 10606 | cellPhoneType | Tato hodnota konstanty je hodnota ID SuccessFactors přidružená k mobilnímu telefonu. Aktualizujte tuto hodnotu tak, aby odpovídala vašemu SuccessFactors prostředí. Postup pro nastavení této hodnoty naleznete v části [načtení konstantní hodnoty pro phoneType](#retrieve-constant-value-for-phonetype) . |
   | 13 | false (nepravda) | cellPhoneIsPrimary | Pomocí tohoto atributu můžete nastavit primární příznak pro číslo mobilního telefonu. Platné hodnoty jsou true nebo false. |
 
1. Ověřte a zkontrolujte mapování atributů. 
 
    >[!div class="mx-imgBorder"]
    >![Mapování atributu zpětného zápisu](./media/sap-successfactors-inbound-provisioning/writeback-attribute-mapping.png)

1. Kliknutím na **Uložit** uložte mapování. V dalším kroku aktualizujeme výrazy rozhraní API pro cestu JSON pro použití kódů phoneType ve vaší instanci SuccessFactors. 
1. Vyberte **Zobrazit pokročilé možnosti**. 

    >[!div class="mx-imgBorder"]
    >![Zobrazit upřesňující možnosti](./media/sap-successfactors-inbound-provisioning/show-advanced-options.png)

1. Klikněte na **Upravit seznam atributů pro SuccessFactors**. 

   > [!NOTE] 
   > Pokud se v Azure Portal nezobrazuje možnost **Upravit seznam atributů pro SuccessFactors** , použijte adresu URL *https://portal.azure.com/?Microsoft_AAD_IAM_forceSchemaEditorEnabled=true* pro přístup ke stránce. 

1. Sloupec **výrazu rozhraní API** v tomto zobrazení zobrazuje výrazy cesty JSON používané konektorem. 
1. Aktualizujte výrazy cesty JSON pro telefon do zaměstnání a mobilní telefon, aby se použila hodnota ID (*businessPhoneType* a *cellPhoneType*) odpovídající vašemu prostředí. 

    >[!div class="mx-imgBorder"]
    >![Změna cesty JSON pro telefon](./media/sap-successfactors-inbound-provisioning/phone-json-path-change.png)

1. Kliknutím na **Uložit** uložte mapování.

## <a name="enable-and-launch-user-provisioning"></a>Povolení a spuštění zřizování uživatelů

Po dokončení konfigurace aplikace SuccessFactors Provisioning můžete službu zřizování zapnout v Azure Portal.

> [!TIP]
> Ve výchozím nastavení se při zapnutí služby zřizování spustí operace zřizování pro všechny uživatele v oboru. Pokud dojde k chybám při mapování nebo potížích s daty, úloha zřizování může selhat a přejít do stavu karantény. Aby k tomu nedocházelo, doporučujeme nakonfigurovat filtr **oboru zdrojového objektu** a otestovat mapování atributů s několika testovacími uživateli před spuštěním úplné synchronizace pro všechny uživatele. Jakmile ověříte, že mapování funguje a poskytuje požadované výsledky, můžete buď odebrat filtr, nebo ho postupně rozšířit, aby zahrnoval více uživatelů.

1. Na kartě **zřizování** nastavte **stav zřizování** na **zapnuto**.

2. Klikněte na **Uložit**.

3. Tato operace spustí počáteční synchronizaci, což může trvat proměnlivý počet hodin v závislosti na tom, kolik uživatelů je v tenantovi SuccessFactors. V indikátoru průběhu můžete sledovat průběh cyklu synchronizace. 

4. Na kartě **protokoly auditu** v Azure Portal můžete kdykoli zjistit, jaké akce služba zřizování provedla. Protokoly auditu vypíše všechny jednotlivé události synchronizace prováděné službou zřizování, například které uživatele čtou z centrálního zaměstnance a následně se přidají nebo aktualizují ve službě Active Directory. 

5. Po dokončení počáteční synchronizace bude na kartě **zřizování** napsána Sestava souhrnu auditu, jak je znázorněno níže.

   > [!div class="mx-imgBorder"]
   > ![Indikátor průběhu zřizování](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="supported-scenarios-known-issues-and-limitations"></a>Podporované scénáře, známé problémy a omezení

Přečtěte si [část scénáře zpětného zápisu](../app-provisioning/sap-successfactors-integration-reference.md#writeback-scenarios) v referenční příručce integrace SAP SuccessFactors. 

## <a name="next-steps"></a>Další kroky

* [Referenční informace o integraci podrobně do Azure AD a SAP SuccessFactors](../app-provisioning/sap-successfactors-integration-reference.md)
* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy pro aktivitu zřizování.](../app-provisioning/check-status-user-account-provisioning.md)
* [Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi SuccessFactors a Azure Active Directory](successfactors-tutorial.md)
* [Naučte se integrovat další aplikace SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Naučte se exportovat a importovat vaše konfigurace zřizování.](../app-provisioning/export-import-provisioning-configuration.md)

