---
title: 'Kurz: Konfigurace příchozího zřizování Workday v Azure Active Directory | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat příchozí zřizování z Workday do Azure AD.
services: active-directory
author: cmmdesai
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.topic: tutorial
ms.workload: identity
ms.date: 05/26/2020
ms.author: chmutali
ms.openlocfilehash: ef4381f305292b366348aa3729209dc3f5e8c87b
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954085"
---
# <a name="tutorial-configure-workday-to-azure-ad-user-provisioning"></a>Kurz: Konfigurace zřizování uživatelů v Workday na Azure AD
Cílem tohoto kurzu je Ukázat kroky, které je třeba provést při zřizování pracovních dat z Workday do Azure Active Directory. 

>[!NOTE]
>Tento kurz použijte v případě, že uživatelé, které chcete zřídit z Workday, jsou jenom uživatelé cloudu, kteří nepotřebují místní účet služby AD. Pokud uživatelé požadují jenom místní účet služby AD nebo účet AD i Azure AD, přečtěte si prosím kurz [Konfigurace pracovního dne pro zřizování uživatelů služby Active Directory](workday-inbound-tutorial.md) . 

## <a name="overview"></a>Přehled

[Služba zřizování uživatelů Azure Active Directory](../app-provisioning/user-provisioning.md) se integruje s [rozhraním API Workday pro lidské zdroje](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) , aby bylo možné zřídit uživatelské účty. Pracovní postupy zřizování uživatelů Workday, které podporuje služba zřizování uživatelů Azure AD, umožňují automatizaci následujících scénářů lidských zdrojů a životního cyklu identit:

* Připravují se **noví zaměstnanci** – když se do Workday přidá nový zaměstnanec, automaticky se vytvoří uživatelský účet v Azure Active Directory a volitelně Microsoft 365 a [jiné aplikace SaaS, které Azure AD podporuje](../app-provisioning/user-provisioning.md), s zpětným zápisem e-mailové adresy do Workday.

* **Aktualizace atributů a profilů zaměstnanců** – když se v Workday aktualizuje záznam zaměstnance (například jeho jméno, název nebo manažer), automaticky se aktualizuje jeho uživatelský účet Azure Active Directory a případně Microsoft 365 a [Další aplikace SaaS podporované službou Azure AD](../app-provisioning/user-provisioning.md).

* **Ukončení zaměstnanců** – když se zaměstnanec v Workday ukončí, jejich uživatelský účet se automaticky zakáže v Azure Active Directory a volitelně Microsoft 365 a [Další SaaS aplikace podporované službou Azure AD](../app-provisioning/user-provisioning.md).

* **Pracovní zařazení zaměstnanců** – když se zaměstnanec v Workday odkoupí, jeho starý účet se dá automaticky znovu aktivovat nebo znovu zřídit (v závislosti na vaší preferenci) a volitelně Microsoft 365 a další SaaS aplikace, Azure Active Directory které [Azure AD podporuje](../app-provisioning/user-provisioning.md).

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Na koho se toto řešení pro zřizování uživatelů nejlépe hodí?

Tento pracovní den pro Azure Active Directory řešení zřizování uživatelů je ideálně vhodný pro:

* Organizace, které chtějí předem připravené cloudové řešení pro zřizování uživatelů v Workday

* Organizace, které vyžadují přímé zřizování uživatelů z Workday do Azure Active Directory

* Organizace, které vyžadují, aby se uživatelé zřídili pomocí dat získaných z Workday

* Organizace, které používají Microsoft 365 k e-mailu

## <a name="solution-architecture"></a>Architektura řešení

Tato část popisuje kompletní architekturu řešení zřizování uživatelů jenom pro cloudové uživatele. Existují dva související toky:

* **Autoritativní tok dat o lidských přenosech – od pracovního dne po Azure Active Directory:** V těchto událostech pracovního procesu toku (jako jsou nová přijetí, převody, ukončení) se nejdříve objeví v Workday a data událostí se budou nacházet Azure Active Directory. V závislosti na události to může vést k vytváření, aktualizaci, povolování a zakazování operací v Azure AD.
* **Tok zpětného zápisu – od místní služby Active Directory až po pracovní den:** Jakmile se účet vytvoří ve službě Active Directory, synchronizuje se s Azure AD prostřednictvím Azure AD Connect a informace, jako je e-mailová adresa, uživatelské jméno a telefonní číslo, se dají zapsat zpátky do Workday.

  ![Přehled](./media/workday-inbound-tutorial/workday-cloud-only-provisioning.png)

### <a name="end-to-end-user-data-flow"></a>Koncový tok dat uživatele

1. Tým HR provádí pracovní transakce (Spojovacíky/stěhovací společnosti/Leaversy nebo noví zaměstnanci, přenosy a ukončení) v Workday zaměstnanec – střed
2. Služba zřizování Azure AD spouští naplánované synchronizace identit z pracovního dne ES a identifikuje změny, které je potřeba zpracovat pro synchronizaci s místní službou Active Directory.
3. Služba zřizování Azure AD tuto změnu určí a vyvolá pro uživatele ve službě Azure AD operaci vytvořit/aktualizovat/povolit/zakázat.
4. Pokud je aplikace se [zpětným zápisem do pracovního](workday-writeback-tutorial.md) dne nakonfigurovaná, načte z Azure AD atributy, jako je e-mail, uživatelské jméno a telefonní číslo. 
5. Služba zřizování Azure AD nastavuje e-mail, uživatelské jméno a telefonní číslo v Workday.

## <a name="planning-your-deployment"></a>Plánování nasazení

Konfigurace cloudového zřizování pro cloudové úsilí z Workday do Azure AD vyžaduje výrazné plánování zahrnující různé aspekty, jako například:

* Určení odpovídajícího ID 
* Mapování atributů
* Transformace atributu 
* Filtry oborů

Podrobné pokyny k těmto tématům najdete v [plánu nasazení cloudového HR](../app-provisioning/plan-cloud-hr-provision.md) . 

## <a name="configure-integration-system-user-in-workday"></a>Konfigurace uživatele integračního systému v Workday

Informace o tom, jak vytvořit uživatelský účet integračního systému s oprávněním k načtení dat pracovního procesu, najdete v části věnované [konfiguraci systému pro integraci uživatele](workday-inbound-tutorial.md#configure-integration-system-user-in-workday) . 

## <a name="configure-user-provisioning-from-workday-to-azure-ad"></a>Konfigurace zřizování uživatelů z Workday do Azure AD

Následující části popisují kroky pro konfiguraci zřizování uživatelů z Workday do Azure AD pro cloudová nasazení.

* [Přidává se aplikace konektoru zřizování Azure AD a vytvoření připojení k Workday.](#part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday)
* [Konfigurace služby Workday a mapování atributů Azure AD](#part-2-configure-workday-and-azure-ad-attribute-mappings)
* [Povolení a spuštění zřizování uživatelů](#enable-and-launch-user-provisioning)

### <a name="part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday"></a>Část 1: Přidání aplikace konektoru zřizování Azure AD a vytvoření připojení k Workday

**Konfigurace pracovního dne pro Azure Active Directory zřizování jenom pro cloudové uživatele:**

1. Přejděte na <https://portal.azure.com>.

2. V Azure Portal vyhledejte a vyberte **Azure Active Directory**.

3. Vyberte **podnikové aplikace** a pak **všechny aplikace**.

4. Vyberte **Přidat aplikaci** a pak vyberte kategorii **vše** .

5. Vyhledejte v **Workday zřizování uživatelů Azure AD** a přidejte tuto aplikaci z galerie.

6. Až se aplikace přidá a zobrazí se obrazovka s podrobnostmi aplikace, vyberte **zřizování**.

7. Změňte režim **zřizování**  na **automaticky**.

8. Dokončete část **přihlašovací údaje správce** následujícím způsobem:

   * **Workday username** – zadejte uživatelské jméno účtu Integration System v Workday s názvem domény tenanta, který je připojený. By měl vypadat nějak takto: username@contoso4

   * **Heslo pracovního dne –** Zadejte heslo účtu systému pro integraci Workday.

   * **Adresa URL rozhraní API webových služeb Workday –** Zadejte adresu URL koncového bodu webových služeb Workday pro vašeho tenanta. Adresa URL určuje verzi rozhraní API webových služeb Workday, kterou používá konektor. 
   
     | Formát URL | Použitá verze rozhraní API WWS | Jsou vyžadovány změny XPATH |
     |------------|----------------------|------------------------|
     | https://####.workday.com/ccx/service/tenantName | v 21.1 | No |
     | https://####.workday.com/ccx/service/tenantName/Human_Resources | v 21.1 | No |
     | https://####.workday.com/ccx/service/tenantName/Human_Resources/v##.# | v # #. # | Yes |

      > [!NOTE]
     > Pokud v adrese URL nejsou zadané žádné informace o verzi, aplikace používá WWS (Workday Web Services) v 21.1 a pro výchozí výrazy rozhraní API XPATH dodávané s aplikací se nevyžadují žádné změny. Pokud chcete použít konkrétní verzi rozhraní WWS API, zadejte v adrese URL číslo verze. <br>
     > Příklad: `https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources/v34.0` <br>
     > <br> Pokud používáte rozhraní API WWS v 30.0 +, než zapnete úlohu zřizování, aktualizujte prosím **výrazy rozhraní API XPath** v části **mapování atributů – > upřesnit možnosti – > upravit seznam atributů pro Workday** , který odkazuje na část [Správa konfigurace](workday-inbound-tutorial.md#managing-your-configuration) a [referenčních atributů Workday](../app-provisioning/workday-attribute-reference.md#xpath-values-for-workday-web-services-wws-api-v30).  

   * **E-mail s oznámením –** Zadejte svou e-mailovou adresu a zaškrtněte políčko Odeslat e-mail, pokud dojde k chybě.

   * Klikněte na tlačítko **Testovat připojení** .

   * Pokud je test připojení úspěšný, klikněte na tlačítko **Uložit** v horní části. Pokud se to nepovede, poklikejte na to, že adresa URL a přihlašovací údaje pracovního dne jsou platné v Workday.

### <a name="part-2-configure-workday-and-azure-ad-attribute-mappings"></a>Část 2: Konfigurace mapování atributů Workday a Azure AD

V této části nakonfigurujete způsob, jakým budou data uživatelů z Workday Azure Active Directory jenom pro uživatele, kteří jsou jenom pro Cloud.

1. Na kartě zřizování v části **mapování** klikněte na **synchronizovat pracovní procesy s Azure AD**.

2. V poli **obor zdrojového objektu** můžete vybrat, které sady uživatelů v Workday by měly být v oboru pro zřizování do Azure AD, a to definováním sady filtrů založených na atributech. Výchozí obor je "Všichni uživatelé v Workday". Příklady filtrů:

   * Příklad: určení oboru pro uživatele s ID pracovních procesů mezi 1000000 a 2000000

      * Atribut: WorkerID

      * Operátor: shoda regulárního výrazu

      * Hodnota: (1 [0-9] [0-9] [0-9] [0-9] [0-9] [0-9])

   * Příklad: pouze podprocesní pracovníci a ne běžné zaměstnance

      * Atribut: ContingentID

      * Operátor: není NULL

3. V poli **Akce cílového objektu** můžete globálně filtrovat akce prováděné v Azure AD. **Vytváření**  a **aktualizace** jsou nejběžnější.

4. V části **mapování atributů** můžete definovat, jak se mají jednotlivé atributy Workday mapovat na atributy služby Active Directory.

5. Kliknutím na existující mapování atributů ho aktualizujte nebo kliknutím na **Přidat nové mapování** v dolní části obrazovky přidejte nová mapování. Jednotlivé mapování atributů podporují tyto vlastnosti:

   * **Typ mapování**

      * **Direct** – zapíše hodnotu atributu Workday do atributu AD bez jakýchkoli změn.

      * **Konstanta** – zapsat statickou konstantní hodnotu řetězce na atribut AD

      * **Výraz** – umožňuje napsat vlastní hodnotu atributu AD na základě jednoho nebo více atributů Workday. [Další informace najdete v tomto článku o výrazech](../app-provisioning/functions-for-customizing-application-data.md).

   * **Zdrojový atribut** – atribut uživatele z pracovního dne. Pokud atribut, který hledáte, není k dispozici, přečtěte si téma [přizpůsobení seznamu atributů uživatele Workday](workday-inbound-tutorial.md#customizing-the-list-of-workday-user-attributes).

   * **Výchozí hodnota** – volitelné. Pokud zdrojový atribut má prázdnou hodnotu, mapování místo toho zapíše tuto hodnotu.
            Nejběžnější konfigurací je ponecháno toto prázdné.

   * **Atribut target** – atribut uživatele v Azure AD.

   * **Porovnává objekty pomocí tohoto atributu** – bez ohledu na to, jestli se má tento atribut použít k jednoznačné identifikaci uživatelů mezi Workday a Azure AD. Tato hodnota je obvykle nastavena v poli ID pracovního procesu pro Workday, což je obvykle namapováno na atribut ID zaměstnance (nový) nebo na atribut rozšíření v Azure AD.

   * **Priorita porovnání** – lze nastavit více vyhovujících atributů. Pokud existuje více, vyhodnotí se v pořadí definovaném tímto polem. Jakmile se najde shoda, nevyhodnocují se žádné další odpovídající atributy.

   * **Použít toto mapování**

     * **Vždycky** – použít toto mapování na akce vytvoření a aktualizace uživatele

     * **Pouze během vytváření** – použít toto mapování pouze při akcích vytvoření uživatele

6. Pokud chcete uložit mapování, klikněte na **Uložit** v horní části Attribute-Mapping části.


## <a name="enable-and-launch-user-provisioning"></a>Povolení a spuštění zřizování uživatelů

Po dokončení konfigurace aplikace pro zřizování Workday můžete službu zřizování zapnout v Azure Portal.

> [!TIP]
> Ve výchozím nastavení se při zapnutí služby zřizování spustí operace zřizování pro všechny uživatele v oboru. Pokud dojde k chybám při mapování nebo při potížích s daty Workday, úloha zřizování může selhat a přejít do stavu karantény. Aby k tomu nedocházelo, doporučujeme nakonfigurovat filtr **oboru zdrojového objektu** a otestovat mapování atributů s několika testovacími uživateli před spuštěním úplné synchronizace pro všechny uživatele. Jakmile ověříte, že mapování funguje a poskytuje požadované výsledky, můžete buď odebrat filtr, nebo ho postupně rozšířit, aby zahrnoval více uživatelů.

1. Na kartě **zřizování** nastavte **stav zřizování** na **zapnuto**.

2. Klikněte na **Uložit**.

3. Tato operace spustí počáteční synchronizaci, což může trvat proměnlivý počet hodin v závislosti na tom, kolik uživatelů je v tenantovi pracovního dne. V indikátoru průběhu můžete sledovat průběh cyklu synchronizace. 

4. Na kartě **protokoly auditu** v Azure Portal můžete kdykoli zjistit, jaké akce služba zřizování provedla. Protokoly auditu vypíše všechny jednotlivé události synchronizace prováděné službou zřizování, například to, kteří uživatelé se čtou z pracovního dne a následně se přidají nebo aktualizují Azure Active Directory. 

5. Po dokončení počáteční synchronizace bude na kartě **zřizování** napsána Sestava souhrnu auditu, jak je znázorněno níže.

   > [!div class="mx-imgBorder"]
   > ![Indikátor průběhu zřizování](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="next-steps"></a>Další kroky

* [Další informace o scénářích Integrace Azure AD a Workday a volání webové služby](../app-provisioning/workday-integration-reference.md)
* [Další informace o podporovaných atributech Workday pro příchozí zřizování](../app-provisioning/workday-attribute-reference.md)
* [Informace o tom, jak nakonfigurovat zpětný zápis do Workday](workday-writeback-tutorial.md)
* [Zjistěte, jak procházet protokoly a získat sestavy aktivit zřizování](../app-provisioning/check-status-user-account-provisioning.md).
* [Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Workday a Azure Active Directory](workday-tutorial.md)
* [Naučte se exportovat a importovat vaše konfigurace zřizování.](../app-provisioning/export-import-provisioning-configuration.md)


