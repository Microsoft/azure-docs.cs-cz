---
title: 'Kurz: Konfigurace zpětného zápisu do Workday v Azure Active Directory | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat zpětný zápis atributů z Azure AD na Workday.
services: active-directory
author: cmmdesai
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.topic: article
ms.workload: identity
ms.date: 05/26/2020
ms.author: chmutali
ms.openlocfilehash: 8c76bddc0fae024b0dd2bdd27d6b1e10d71dec71
ms.sourcegitcommit: 43558caf1f3917f0c535ae0bf7ce7fe4723391f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2020
ms.locfileid: "90017468"
---
# <a name="tutorial-configure-attribute-writeback-from-azure-ad-to-workday"></a>Kurz: Konfigurace zpětného zápisu atributu z Azure AD na Workday
Cílem tohoto kurzu je Ukázat kroky, které musíte provést při zpětném zápisu atributů z Azure AD do Workday. Aplikace pro zřizování zpětného zápisu do pracovního dne podporuje přiřazení hodnot k následujícím atributům Workday:
* Pracovní E-mail 
* Uživatelské jméno Workday
* Telefonní číslo pracovní pevné (včetně kódu země, oblasti kódu, čísla a rozšíření)
* Příznak primárního telefonního čísla pro pracovní pevné
* Mobilní číslo pracovní (včetně kódu země, směrové číslo oblasti, číslo)
* Použít primární příznak pracovní mobilní

## <a name="overview"></a>Přehled

Když nastavíte příchozí integraci zřizování pomocí [Workday do místní](workday-inbound-tutorial.md) aplikace pro zřizování služby AD nebo [Workday do služby Azure AD](workday-inbound-cloud-only-tutorial.md) zřizování, můžete volitelně nakonfigurovat aplikaci pro zpětný zápis do Workday, aby zapisovala kontaktní údaje, jako je pracovní e-mail a telefonní číslo do Workday. 

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Na koho se toto řešení pro zřizování uživatelů nejlépe hodí?

Toto řešení pro zpětný zápis uživatelů v pracovní den je ideální pro:

* Organizace, které používají Microsoft 365, které chtějí ke zpětným voláním používat autoritativní atributy (jako je e-mailová adresa, uživatelské jméno a telefonní číslo) zpátky do Workday

## <a name="configure-integration-system-user-in-workday"></a>Konfigurace uživatele integračního systému v Workday

Informace o tom, jak vytvořit uživatelský účet integračního systému s oprávněním k načtení dat pracovního procesu, najdete v části věnované [konfiguraci systému pro integraci uživatele](workday-inbound-tutorial.md#configure-integration-system-user-in-workday) . 

## <a name="configuring-azure-ad-attribute-writeback-to-workday"></a>Konfigurace zpětného zápisu atributů Azure AD na Workday

Podle těchto pokynů nakonfigurujte zpětný zápis e-mailových adres a uživatelského jména uživatele z Azure Active Directory na Workday.

* [Přidání aplikace konektoru zpětného zápisu a vytvoření připojení k Workday](#part-1-adding-the-writeback-connector-app-and-creating-the-connection-to-workday)
* [Konfigurovat mapování atributů zpětného zápisu](#part-2-configure-writeback-attribute-mappings)
* [Povolení a spuštění zřizování uživatelů](#enable-and-launch-user-provisioning)

### <a name="part-1-adding-the-writeback-connector-app-and-creating-the-connection-to-workday"></a>Část 1: Přidání aplikace konektoru pro zpětný zápis a vytvoření připojení k Workday

**Konfigurace konektoru pro zápis do Workday:**

1. Přejděte na <https://portal.azure.com>.

2. V Azure Portal vyhledejte a vyberte **Azure Active Directory**.

3. Vyberte **podnikové aplikace**a pak **všechny aplikace**.

4. Vyberte **Přidat aplikaci**a pak vyberte kategorii **vše** .

5. Vyhledejte **zpětný zápis do Workday**a přidejte tuto aplikaci z galerie.

6. Až se aplikace přidá a zobrazí se obrazovka s podrobnostmi aplikace, vyberte **zřizování**.

7. Změňte režim **zřizování** **Mode** na **automaticky**.

8. Dokončete část **přihlašovací údaje správce** následujícím způsobem:

   * **Uživatelské jméno správce** – zadejte uživatelské jméno účtu Integration System v Workday s názvem domény tenanta, který je připojený. By měl vypadat nějak takto: *UserName \@ contoso4*

   * **Heslo správce –** Zadejte heslo účtu systému pro integraci Workday.

   * **Adresa URL tenanta –** Zadejte adresu URL koncového bodu webových služeb Workday pro vašeho tenanta. Tato hodnota by měla vypadat takto: `https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources` , kde *contoso4* nahrazuje správný název tenanta a *WD3-impl* se nahradí správným řetězcem prostředí (v případě potřeby).

   * **E-mail s oznámením –** Zadejte svou e-mailovou adresu a zaškrtněte políčko Odeslat e-mail, pokud dojde k chybě.

   * Klikněte na tlačítko **Testovat připojení** . Pokud je test připojení úspěšný, klikněte na tlačítko **Uložit** v horní části. Pokud se to nepovede, poklikejte na to, že adresa URL a přihlašovací údaje pracovního dne jsou platné v Workday.

### <a name="part-2-configure-writeback-attribute-mappings"></a>Část 2: Konfigurace mapování atributů zpětného zápisu

V této části nakonfigurujete, jak atributy zpětného zápisu z Azure AD do Workday budou. 

1. Na kartě zřizování v části **mapování**klikněte na název mapování.

2. V poli **obor zdrojového objektu** můžete volitelně filtrovat, které sady uživatelů v Azure Active Directory by měly být součástí zpětného zápisu. Výchozí obor je "Všichni uživatelé v Azure AD".

3. V části **mapování atributů** aktualizujte ID odpovídajícího identifikátoru tak, aby označoval atribut v Azure Active Directory, kde je uloženo ID pracovního procesu Workday nebo ID zaměstnance. Oblíbenou metodou porovnávání je synchronizace ID pracovního procesu Workday nebo ID zaměstnance ve extensionAttribute1-15 ve službě Azure AD a pak použijte tento atribut ve službě Azure AD tak, aby odpovídal uživatelům zpátky v Workday.

4. Obvykle namapujete atribut pro Azure AD *userPrincipalName* na atribut Workday *UserID* a namapujete atribut *mail* služby Azure AD na atribut *EmailAddress* pracovního dne. 

     >[!div class="mx-imgBorder"]
     >![Azure Portal](./media/workday-inbound-tutorial/workday-writeback-mapping.png)

5. Pomocí níže uvedených pokynů namapujte hodnoty atributů telefonního čísla z Azure AD na Workday. 

     | Atribut telefon do pracovního dne | Očekávaná hodnota | Pokyny pro mapování |
     |-------------------------|----------------|------------------|
     | WorkphoneLandlineIsPrimary | true nebo false | Konstanta nebo mapování výrazu, jejichž výstupem je hodnota řetězce "true" nebo "false". |
     | WorkphoneLandlineCountryCodeName | [Tři číslice ISO 3166-1 – kód země](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-3) | Mapování konstant nebo výrazů, jejichž výstupem je tři písmena kódu země. |
     | WorkphoneLandlineCountryCodeNumber | [Mezinárodní země volající kód](https://en.wikipedia.org/wiki/List_of_country_calling_codes) | Mapování konstant nebo výrazů, jejichž výstupem je platný kód země (bez znaménka +). |
     | WorkphoneLandlineNumber | Celé telefonní číslo včetně směrového čísla oblasti | Namapujte na atribut *telephoneNumber* . Použijte regulární výraz pro odebrání prázdných znaků, závorek a kódů zemí. Viz následující příklad. |
     | WorkphoneLandlineExtension | Číslo rozšíření | Pokud *telephoneNumber* obsahuje rozšíření, použijte regulární výraz pro extrakci hodnoty. |
     | WorkphoneMobileIsPrimary | true nebo false | Mapování konstant nebo mapování výrazů, jejichž výstupem je hodnota řetězce "true" nebo "false" |
     | WorkphoneMobileCountryCodeName | [Tři číslice ISO 3166-1 – kód země](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-3) | Mapování konstant nebo výrazů, jejichž výstupem je tři písmena kódu země. |
     | WorkphoneMobileCountryCodeNumber | [Mezinárodní země volající kód](https://en.wikipedia.org/wiki/List_of_country_calling_codes) | Mapování konstant nebo výrazů, jejichž výstupem je platný kód země (bez znaménka +). |
     | WorkphoneMobileNumber | Celé telefonní číslo včetně směrového čísla oblasti | Namapujte na *mobilní* atribut. Použijte regulární výraz pro odebrání prázdných znaků, závorek a kódů zemí. Viz následující příklad. |

     > [!NOTE]
     > Při vyvolání webové služby Change_Work_Contact Workday pošle služba Azure AD následující konstantní hodnoty: <br>
     > * **Communication_Usage_Type_ID** je nastavená na konstantní řetězec "Work". <br>
     > * **Phone_Device_Type_ID** je nastavené na konstantní řetězec "mobilní" pro čísla mobilních telefonů a "pevné" pro telefonní čísla pevné. <br>
     > 
     > Pokud váš tenant Workday používá jiné Type_IDs, dojde k selhání zpětného zápisu. Chcete-li těmto chybám zabránit, můžete použít úlohu pracovní **ID** pro pracovní službu Workday a aktualizovat Type_IDs tak, aby odpovídaly hodnotám používaným službou Azure AD. <br>
     >  

     **Výrazy referenčního regulárního výrazu – příklad 1**

     Použijte níže uvedený regulární výraz, pokud je telefonní číslo ve službě Azure AD nastaveno pomocí formátu požadovaného pro Samoobslužné resetování hesla (SSPR). <br>
     Příklad: Pokud je hodnota telefonního čísla + 1 1112223333-> pak výraz Regex bude výstup 1112223333

     ```C#
     Replace([telephoneNumber], , "\\+(?<isdCode>\\d* )(?<phoneNumber>\\d{10})", , "${phoneNumber}", , )
     ```

     **Výrazy referenčního regulárního výrazu – příklad 2**

     Použijte níže uvedený regulární výraz, pokud je telefonní číslo ve službě Azure AD nastaveno pomocí formátu (XXX) XXX-XXXX. <br>
     Příklad: Pokud je hodnota telefonního čísla (111) 222-3333-> pak výraz Regex bude výstup 1112223333

     ```C#
     Replace([mobile], , "[()\\s-]+", , "", , )
     ```

6. Pokud chcete uložit mapování, klikněte na **Uložit** v horní části oddílu mapování atributů.

## <a name="enable-and-launch-user-provisioning"></a>Povolení a spuštění zřizování uživatelů

Po dokončení konfigurace aplikace pro zřizování Workday můžete službu zřizování zapnout v Azure Portal.

> [!TIP]
> Ve výchozím nastavení se při zapnutí služby zřizování spustí operace zřizování pro všechny uživatele v oboru. Pokud dojde k chybám při mapování nebo při potížích s daty Workday, úloha zřizování může selhat a přejít do stavu karantény. Aby k tomu nedocházelo, doporučujeme nakonfigurovat filtr **oboru zdrojového objektu** a otestovat mapování atributů s několika testovacími uživateli před spuštěním úplné synchronizace pro všechny uživatele. Jakmile ověříte, že mapování funguje a poskytuje požadované výsledky, můžete buď odebrat filtr, nebo ho postupně rozšířit, aby zahrnoval více uživatelů.

1. Na kartě **zřizování** nastavte **stav zřizování** na **zapnuto**.

2. Klikněte na **Uložit**.

3. Tato operace spustí počáteční synchronizaci, což může trvat proměnlivý počet hodin v závislosti na tom, kolik uživatelů se nachází ve zdrojovém adresáři. V indikátoru průběhu můžete sledovat průběh cyklu synchronizace. 

4. Na kartě **protokoly auditu** v Azure Portal můžete kdykoli zjistit, jaké akce služba zřizování provedla. Protokoly auditu vypíše všechny jednotlivé události synchronizace prováděné službou zřizování, například to, kteří uživatelé jsou importováni ze zdroje a exportováni do cílové aplikace.  

5. Po dokončení počáteční synchronizace bude na kartě **zřizování** napsána Souhrnná sestava, jak je znázorněno níže.

     > [!div class="mx-imgBorder"]
     > ![Indikátor průběhu zřizování](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy pro aktivitu zřizování.](../app-provisioning/check-status-user-account-provisioning.md)
* [Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Workday a Azure Active Directory](workday-tutorial.md)
* [Naučte se integrovat další aplikace SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Naučte se exportovat a importovat vaše konfigurace zřizování.](../app-provisioning/export-import-provisioning-configuration.md)

