---
title: 'Kurz: Konfigurace pracovního dne pro automatické zřizování uživatelů pomocí služby Azure Active Directory | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat službu Azure Active Directory tak, aby automaticky zřašovala a zřašovala uživatelské účty do Workday.
services: active-directory
author: cmmdesai
documentationcenter: na
manager: daveba
ms.assetid: 1a2c375a-1bb1-4a61-8115-5a69972c6ad6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/16/2019
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: d7eb01f3997ac4ab2e439c00f07990c51ec3e3d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80370353"
---
# <a name="tutorial-configure-workday-for-automatic-user-provisioning"></a>Kurz: Konfigurace pracovního dne pro automatické zřizování uživatelů

Cílem tohoto kurzu je zobrazit kroky, které je třeba provést k importu profilů pracovníků z Workday do služby Active Directory a služby Azure Active Directory s volitelným zpětným zápisem e-mailové adresy a uživatelského jména do workday.

## <a name="overview"></a>Přehled

[Služba zřizování uživatelů služby Azure Active Directory](../app-provisioning/user-provisioning.md) se integruje s [rozhraním Workday Personal Resources API](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) za účelem zřizování uživatelských účtů. Azure AD používá toto připojení k povolení následujících pracovních postupů zřizování uživatelů:

* **Zřizování uživatelů do služby Active Directory** – zřizování vybraných sad uživatelů z pracovního dne do jedné nebo více domén služby Active Directory.

* **Zřizování uživatelů pouze cloudu do služby Azure Active Directory** – ve scénářích, kde se nepoužívá místní služba Active Directory, můžou být uživatelé zřizováni přímo z Workday do Služby Azure Active Directory pomocí služby zřizování uživatelů Azure AD.

* **Odepsat e-mailovou adresu a uživatelské jméno do Workday** – služba zřizování uživatelů Azure AD můžete zapsat e-mailové adresy a uživatelské jméno z Azure AD zpět do Workday.

### <a name="what-human-resources-scenarios-does-it-cover"></a>Jaké scénáře lidských zdrojů se zabývá?

Pracovní postupy zřizování uživatelů Workday podporované službou zřizování uživatelů Azure AD umožňují automatizaci následujících lidských zdrojů a scénářů správy životního cyklu identit:

* **Nábor nových zaměstnanců** – když se do Workday přidá nový zaměstnanec, automaticky se vytvoří uživatelský účet ve službě Active Directory, azure active directory a volitelně office 365 a [dalších aplikacích SaaS podporovaných službou Azure AD](../app-provisioning/user-provisioning.md)s zpětným zápisem e-mailové adresy do Workday.

* **Aktualizace atributů a profilů zaměstnanců** – když se záznam zaměstnance aktualizuje v Workday (například jejich jméno, titul nebo správce), jejich uživatelský účet se automaticky aktualizuje ve službě Active Directory, službě Azure Active Directory a volitelně office 365 a [dalších aplikacích SaaS podporovaných službou Azure AD](../app-provisioning/user-provisioning.md).

* **Ukončení zaměstnanců** – když je zaměstnanec ukončen v Workday, jeho uživatelský účet se automaticky deaktivuje ve službě Active Directory, Azure Active Directory a volitelně Office 365 a [dalších aplikacích SaaS podporovaných službou Azure AD](../app-provisioning/user-provisioning.md).

* Opětovné přijetí zaměstnanců – když je zaměstnanec znovu přijat v Workday, jeho starý účet může být automaticky znovu aktivován nebo znovu zřízen (v závislosti na vašich preferencích) do **služby** Active Directory, služby Azure Active Directory a volitelně Office 365 a [dalších aplikací SaaS podporovaných službou Azure AD](../app-provisioning/user-provisioning.md).

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Pro koho je toto zřizovací řešení uživatele nejvhodnější?

Toto řešení zřizování uživatelů Workday je ideální pro:

* Organizace, které si přejí předem vytvořené cloudové řešení pro zřizování uživatelů Workday

* Organizace, které vyžadují přímé zřizování uživatelů z Workday do služby Active Directory nebo Azure Active Directory

* Organizace, které vyžadují, aby uživatelé byli zřízeni pomocí dat získaných z modulu Workday HCM (viz [Get_Workers)](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html)

* Organizace, které vyžadují připojení, přesunutí a ponechání uživatelů synchronizovat s jednou nebo více doménovými strukturami, doménami a organizačními jednotkami služby Active Directory pouze na základě informací o změnách zjištěných v modulu HCM Workday (viz [Get_Workers)](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html)

* Organizace používající Office 365 pro e-mail

## <a name="solution-architecture"></a>Architektura řešení

Tato část popisuje architekturu řešení zřizování koncových uživatelů pro běžná hybridní prostředí. Existují dva související toky:

* **Autoritativní tok dat lidských zdrojů – od pracovního dne k místnímu službě Active Directory:** V tomto události toku pracovního procesu (například nové najímá, převody, ukončení) nejprve dojít v cloudu Tenant Hr Workday a potom data událostí toky do místní služby Active Directory prostřednictvím Azure AD a zřizovacíagent. V závislosti na události může vést k vytvoření/aktualizaci/povolení/zakázání operací ve službě AD.
* **Tok zpětného zápisu e-mailu a uživatelského jména – z místní služby Active Directory do pracovního dne:** Po dokončení vytvoření účtu ve službě Active Directory se synchronizuje s Azure AD prostřednictvím Služby Azure AD Connect a atribut e-mailu a uživatelského jména se dá zapsat zpět do workday.

![Přehled](./media/workday-inbound-tutorial/wd_overview.png)

### <a name="end-to-end-user-data-flow"></a>Tok dat koncových uživatelů

1. Hr tým provádí pracovní transakce (Truhláři/Stěhováci/Opouštějící nebo Nové najímá/převádí/ukončuje) v Workday HCM
2. Služba zřizování Azure AD spouští naplánované synchronizace identit z pracovního dne HR a identifikuje změny, které je třeba zpracovat pro synchronizaci s místní službou Active Directory.
3. Služba zřizování Azure AD vyvolá místního agenta azure ad connect provisioning agenta s datovou částí požadavku obsahující operace vytvoření/aktualizace/povolení/zakázání účtu Služby AD.
4. Agent zřizování Připojení Azure AD používá účet služby k přidání nebo aktualizaci dat účtu služby AD.
5. Modul Azure AD Connect / AD Sync spustí synchronizaci delta pro vyžádat aktualizace ve službě AD.
6. Aktualizace služby Active Directory se synchronizují se službou Azure Active Directory.
7. Pokud je nakonfigurován konektor zpětného zápisu pracovníden, odepíše atribut e-mailu a uživatelské jméno do Workday na základě použitého odpovídajícího atributu.

## <a name="planning-your-deployment"></a>Plánování nasazení

Před zahájením integrace Workday zkontrolujte níže uvedené požadavky a přečtěte si následující pokyny, jak sladit aktuální architekturu služby Active Directory a požadavky na zřizování uživatelů s řešeními poskytovanými službou Azure Active Directory. K dispozici je také komplexní [plán nasazení](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-plans) s plánovacími listy, který vám pomůže spolupracovat s vaším partnerem pro integraci workday a zúčastněnými stranami v oblasti lidských zdrojů.

Tato část se zabývá následujícími aspekty plánování:

* [Požadavky](#prerequisites)
* [Výběr aplikací konektorů pro zřizování, které chcete nasadit](#selecting-provisioning-connector-apps-to-deploy)
* [Plánování nasazení agenta azure ad připojení zřizování](#planning-deployment-of-azure-ad-connect-provisioning-agent)
* [Integrace s více doménami služby Active Directory](#integrating-with-multiple-active-directory-domains)
* [Plánování pracovního dne do mapování a transformací uživatelských atributů služby Active Directory](#planning-workday-to-active-directory-user-attribute-mapping-and-transformations)

### <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že již máte následující položky:

* Platná licence předplatného Azure AD Premium P1 nebo vyšší pro každého uživatele, který bude pocházet z Workday a zřízendo místní služby Active Directory nebo Azure Active Directory.
* Přístup globálního správce Azure AD ke konfiguraci agenta zřizování
* Tenant implementace Workday pro účely testování a integrace
* Oprávnění správce v programu Workday k vytvoření uživatele systémové integrace a provádění změn v testování dat zaměstnanců pro účely testování
* Pro zřizování uživatelů do služby Active Directory je k hostování [místního agenta zřizování](https://go.microsoft.com/fwlink/?linkid=847801) vyžadován server se systémem Windows Server 2012 nebo vyšším s rozhraním runtime .NET 4.7.1+
* [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) pro synchronizaci uživatelů mezi službou Active Directory a Azure AD

### <a name="selecting-provisioning-connector-apps-to-deploy"></a>Výběr aplikací konektorů pro zřizování, které chcete nasadit

Pro usnadnění zřizování pracovních postupů mezi Workday a Active Directory, Azure AD poskytuje více zřizovací konektor aplikace, které můžete přidat z galerie aplikací Azure AD:

![Galerie aplikací Azure AD](./media/workday-inbound-tutorial/wd_gallery.png)

* **Workday to Active Directory User Provisioning** – Tato aplikace usnadňuje zřizování uživatelských účtů z Pracovního dne do jedné domény služby Active Directory. Pokud máte více domén, můžete přidat jednu instanci této aplikace z galerie aplikací Azure AD pro každou doménu služby Active Directory, kterou potřebujete zřídit.

* **Zřizování uživatelů azure ad** – zatímco Azure AD Connect je nástroj, který by se měl použít k synchronizaci uživatelů služby Active Directory do služby Azure Active Directory, tato aplikace se dá použít k usnadnění zřizování uživatelů pouze v cloudu z Workday do jednoho klienta Azure Active Directory.

* **Zpětný zápis do pracovního dne** – tato aplikace usnadňuje zpětné zápisy e-mailových adres uživatelů z Azure Active Directory do Workday.

> [!TIP]
> Běžná aplikace "Workday" se používá k nastavení jednotného přihlášení mezi Workday a Azure Active Directory.

Pomocí níže uvedeného diagramu rozhodovacího plynu určete, které zřizovací aplikace Workday jsou relevantní pro váš scénář.
    ![Vývojový diagram rozhodnutí](./media/workday-inbound-tutorial/wday_app_flowchart.png "Vývojový diagram rozhodnutí")

Pomocí obsahu přejděte do příslušné části tohoto kurzu.

### <a name="planning-deployment-of-azure-ad-connect-provisioning-agent"></a>Plánování nasazení agenta azure ad připojení zřizování

> [!NOTE]
> Tato část je relevantní pouze v případě, že máte v plánu nasadit Workday do služby Zřizování uživatelů služby Active Directory App. Můžete přeskočit, pokud nasazujete Workday Writeback nebo Workday do aplikace Azure AD User Provisioning App.

Řešení Zřizování uživatelů workday to AD vyžaduje nasazení jednoho nebo více agentů zřizování na serverech se systémem Windows 2012 R2 nebo vyšším s minimálně 4 GB paměti RAM a za běhu .NET 4.7.1+. Před instalací agenta zřizování je třeba vzít v úvahu následující skutečnosti:

* Ujistěte se, že hostitelský server se systémem Zřizovací agent má síťový přístup k cílové doméně služby AD
* Průvodce konfigurací agenta zřízeného agenta zaregistruje agenta s tenantem Azure AD a proces registrace vyžaduje přístup k *.msappproxy.net přes port TLS 443. Ujistěte se, že jsou na místě pravidla odchozí brány firewall, která tuto komunikaci umožňují. Agent podporuje [konfiguraci odchozího proxy serveru HTTPS](#how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication).
* Agent zřizování používá účet služby ke komunikaci s místní doménou služby AD. Před instalací agenta se doporučuje vytvořit účet služby s oprávněními správce domény a heslem, jehož platnost nevyprší.  
* Během konfigurace agenta zřizování můžete vybrat řadiče domény, které by měly zpracovávat požadavky na zřizování. Pokud máte několik geograficky distribuovaných řadičů domény, nainstalujte agenta zřizování do stejné lokality jako upřednostňované řadiče domény, abyste zlepšili spolehlivost a výkon komplexního řešení.
* Pro vysokou dostupnost můžete nasadit více než jednoho agenta zřizování a zaregistrovat jej pro zpracování stejné sady místních domén služby AD.

> [!IMPORTANT]
> V produkčních prostředích Společnost Microsoft doporučuje, abyste měli minimálně 3 agenty zřizování nakonfigurované s klientem Azure AD pro vysokou dostupnost.

### <a name="integrating-with-multiple-active-directory-domains"></a>Integrace s více doménami služby Active Directory

> [!NOTE]
> Tato část je relevantní pouze v případě, že máte v plánu nasadit Workday do služby Zřizování uživatelů služby Active Directory App. Můžete přeskočit, pokud nasazujete Workday Writeback nebo Workday do aplikace Azure AD User Provisioning App.

V závislosti na topologii služby Active Directory se budete muset rozhodnout o počtu aplikací konektoru zřizování uživatelů a počtu agentů zřizování, které chcete konfigurovat. Níže jsou uvedeny některé běžné vzory nasazení, které můžete odkazovat na při plánování nasazení.

#### <a name="deployment-scenario-1--single-workday-tenant---single-ad-domain"></a>Scénář nasazení #1: Jeden tenant Workday -> jedné doméně služby AD

V tomto scénáři máte jednoho tenanta Workday a chcete zřídit uživatele do jedné cílové domény služby AD. Zde je doporučená konfigurace produkčního prostředí pro toto nasazení.

|   |   |
| - | - |
| Ne. zřizovacích agentů pro nasazení v místním prostředí | 3 (pro vysokou dostupnost a převzetí služeb při selhání) |
| Ne. zpracovního dne do zřizovacích aplikací pro uživatele služby AD ke konfiguraci na webu Azure Portal | 1 |

  ![Scénář 1](./media/workday-inbound-tutorial/dep_scenario1.png)

#### <a name="deployment-scenario-2--single-workday-tenant---multiple-child-ad-domains"></a>Scénář nasazení #2: Jeden tenant workday -> více podřízených domén služby AD

Tento scénář zahrnuje zřizování uživatelů z Workday do více cílových podřízených domén služby AD v doménové struktuře. Zde je doporučená konfigurace produkčního prostředí pro toto nasazení.

|   |   |
| - | - |
| Ne. zřizovacích agentů pro nasazení v místním prostředí | 3 (pro vysokou dostupnost a převzetí služeb při selhání) |
| Ne. zpracovního dne do zřizovacích aplikací pro uživatele služby AD ke konfiguraci na webu Azure Portal | jedna aplikace na podřízenou doménu |

  ![Scénář 2](./media/workday-inbound-tutorial/dep_scenario2.png)

#### <a name="deployment-scenario-3--single-workday-tenant---disjoint-ad-forests"></a>Scénář nasazení #3: Jeden tenant workday -> nesouvislé doménové struktury služby AD

Tento scénář zahrnuje zřizování uživatelů z Workday do domén v nesouvislých doménových strukturách služby AD. Zde je doporučená konfigurace produkčního prostředí pro toto nasazení.

|   |   |
| - | - |
| Ne. zřizovacích agentů pro nasazení v místním prostředí | 3 na nesouvislou doménovou strukturu AD |
| Ne. zpracovního dne do zřizovacích aplikací pro uživatele služby AD ke konfiguraci na webu Azure Portal | jedna aplikace na podřízenou doménu |

  ![Scénář 3](./media/workday-inbound-tutorial/dep_scenario3.png)

### <a name="planning-workday-to-active-directory-user-attribute-mapping-and-transformations"></a>Plánování pracovního dne do mapování a transformací uživatelských atributů služby Active Directory

> [!NOTE]
> Tato část je relevantní pouze v případě, že máte v plánu nasadit Workday do služby Zřizování uživatelů služby Active Directory App. Můžete přeskočit, pokud nasazujete Workday Writeback nebo Workday do aplikace Azure AD User Provisioning App.

Před konfigurací zřizování uživatelů do domény služby Active Directory zvažte následující otázky. Odpovědi na tyto otázky určí, jak je třeba nastavit filtry oborů a mapování atributů.

* **Které uživatele v workday ujet do této doménové struktury služby Active Directory?**

  * *Příklad: Uživatelé, u kterých atribut Workday "Company" obsahuje hodnotu "Contoso" a atribut "Worker_Type" obsahuje "Regular"*

* **Jak jsou uživatelé směrováni do různých organizačních jednotek??**

  * *Příklad: Uživatelé jsou směrováni do ru, které odpovídají umístění kanceláře, jak je definováno v atributech Workday "Obec" a "Country_Region_Reference".*

* **Jak by měly být ve službě Active Directory naplněny následující atributy?**

  * Běžný název (cn)
    * *Příklad: Použijte hodnotu User_ID Pracovní den nastavenou lidskými zdroji*

  * ID zaměstnance (EmployeeId)
    * *Příklad: Použití hodnoty Worker_ID pracovní den*

  * Název účtu SAM (sAMAccountName)
    * *Příklad: Použijte hodnotu Workday User_ID filtrovanou pomocí výrazu zřizování Azure AD k odebrání nelegálních znaků*

  * Hlavní jméno uživatele (userPrincipalName)
    * *Příklad: Použijte hodnotu Workday User_ID s výrazem zřizování Azure AD k připojení názvu domény*

* **Jak by měli být uživatelé spárováni mezi workday a službou Active Directory?**

  * *Příklad: Uživatelé s určitou hodnotou Workday "Worker_ID" jsou spárováni s uživateli služby Active Directory, kde má "EmployeeID" stejnou hodnotu. Pokud Worker_ID hodnota není ve službě Active Directory nalezena, vytvořte nového uživatele.*
  
* **Obsahuje doménová struktura služby Active Directory již ID uživatelů požadovaná pro odpovídající logiku?**

  * *Příklad: Pokud je toto nastavení nové nasazení Workday, doporučuje me ze služby Active Directory, aby byla předem vyplněna správnou hodnotou Workday Worker_ID (nebo jedinečnou hodnotou ID volby), aby byla odpovídající logika co nejjednodušší.*

Jak nastavit a nakonfigurovat tyto speciální zřizovací konektor aplikace je předmětem zbývající části tohoto kurzu. Které aplikace, které se rozhodnete konfigurovat, budou záviset na tom, kterým systémům je třeba zřídit a kolik domén služby Active Directory a klientů Azure AD se ve vašem prostředí zobrazí.

## <a name="configure-integration-system-user-in-workday"></a>Konfigurace uživatele integračního systému v aplikaci Workday

Běžným požadavkem všech konektorů pro zřizování Workday je, že vyžadují pověření uživatele integračního systému Workday pro připojení k rozhraní Workday PERSONAL Resources API. Tato část popisuje, jak vytvořit uživatele integračního systému v Workday a má následující části:

* [Vytvoření uživatele integračního systému](#creating-an-integration-system-user)
* [Vytvoření skupiny zabezpečení integrace](#creating-an-integration-security-group)
* [Konfigurace oprávnění zásad zabezpečení domény](#configuring-domain-security-policy-permissions)
* [Konfigurace oprávnění zásad zabezpečení obchodního procesu](#configuring-business-process-security-policy-permissions)
* [Aktivace změn zásad zabezpečení](#activating-security-policy-changes)

> [!NOTE]
> Je možné tento postup obejít a místo toho použít účet globálního správce Workday jako účet systémové integrace. To může fungovat dobře pro ukázky, ale není doporučeno pro produkční nasazení.

### <a name="creating-an-integration-system-user"></a>Vytvoření uživatele integračního systému

**Vytvoření uživatele integračního systému:**

1. Přihlaste se ke svému tenantovi Workday pomocí účtu správce. V **aplikaci Workday zadejte**do vyhledávacího pole vytvořit uživatele a klepněte na příkaz **Vytvořit integrační ho systému .**

   ![Vytvořit uživatele](./media/workday-inbound-tutorial/wd_isu_01.png "Vytvořit uživatele")
2. Dokončete úlohu **Vytvořit integrační systém uživatele** zadáním uživatelského jména a hesla pro nového uživatele integračního systému.  
  
   * Ponechte možnost **Vyžadovat nové heslo při dalším přihlášení** nezaškrtnutou, protože tento uživatel se bude přihlašovat programově.
   * Ponechte **minuty časového limitu relace** s výchozí hodnotou 0, která zabrání předčasně vypršení časového limitu relací uživatele.
   * Vyberte možnost **Nepovolit relace uživatelského rozhraní,** protože poskytuje přidanou vrstvu zabezpečení, která zabraňuje uživateli s heslem integračního systému v přihlášení do Workday.

   ![Vytvořit uživatele integračního systému](./media/workday-inbound-tutorial/wd_isu_02.png "Vytvořit uživatele integračního systému")

### <a name="creating-an-integration-security-group"></a>Vytvoření skupiny zabezpečení integrace

V tomto kroku vytvoříte skupinu zabezpečení systému integrace bez omezení nebo omezení integrace v aplikaci Workday a přiřadíte uživateli integračního systému vytvořenému v předchozím kroku této skupině.

**Vytvoření skupiny zabezpečení:**

1. Do vyhledávacího pole zadejte vytvořit skupinu zabezpečení a klepněte na tlačítko **Vytvořit skupinu zabezpečení**.

    ![Vytvořit skupinu zabezpečení](./media/workday-inbound-tutorial/wd_isu_03.png "Vytvořit skupinu zabezpečení")
2. Dokončete úkol **Vytvořit skupinu zabezpečení.** 

   * V workday existují dva typy skupin zabezpečení:
     * **Bez omezení:** Všichni členové skupiny zabezpečení mají přístup ke všem instancím dat zabezpečeným skupinou zabezpečení.
     * **Omezená:** Všichni členové skupiny zabezpečení mají kontextový přístup k podmnožině instancí dat (řádků), ke kterým má skupina zabezpečení přístup.
   * Vyberte příslušný typ skupiny zabezpečení pro integraci u svého partnera pro integraci Workday.
   * Jakmile znáte typ skupiny, vyberte **skupinu zabezpečení integration systému (Bez omezení)** nebo **skupinu zabezpečení integration systému (omezená)** z rozevíracího souboru **Typ skupiny zabezpečení klienta.**

     ![Vytvořit skupinu zabezpečení](./media/workday-inbound-tutorial/wd_isu_04.png "Vytvořit skupinu zabezpečení")

3. Po úspěšném vytvoření skupiny zabezpečení se zobrazí stránka, na které můžete přiřadit členy ke skupině zabezpečení. Přidejte nového uživatele integračního systému vytvořeného v předchozím kroku do této skupiny zabezpečení. Pokud používáte *skupinu zabezpečení s omezeným zabezpečením,* budete také muset vybrat příslušný obor organizace.

    ![Upravit skupinu zabezpečení](./media/workday-inbound-tutorial/wd_isu_05.png "Upravit skupinu zabezpečení")

### <a name="configuring-domain-security-policy-permissions"></a>Konfigurace oprávnění zásad zabezpečení domény

V tomto kroku udělíte skupině zabezpečení domény oprávnění zásad zabezpečení domény pro pracovní data.

**Postup konfigurace oprávnění zásad zabezpečení domény:**

1. Do vyhledávacího pole zadejte **konfiguraci zabezpečení domény** a klikněte na odkaz **Domain Security Configuration Report**.  

    ![Zásady zabezpečení domény](./media/workday-inbound-tutorial/wd_isu_06.png "Zásady zabezpečení domény")  
2. V textovém poli **Doména** vyhledejte následující domény a přidejte je do filtru jeden po druhém.  
   * *Zřizování externích účtů*
   * *Data pracovníka: Sestavy veřejných pracovníků*
   * *Osobní údaje: Pracovní kontaktní informace*
   * *Údaje o pracovníkovi: Všechny pozice*
   * *Údaje o pracovníkovi: Aktuální informace o personálním obsazení*
   * *Data pracovníka: Obchodní hlava na profilu pracovníka*
   * *Účty pracovního dne*
   
     ![Zásady zabezpečení domény](./media/workday-inbound-tutorial/wd_isu_07.png "Zásady zabezpečení domény")  

     ![Zásady zabezpečení domény](./media/workday-inbound-tutorial/wd_isu_08.png "Zásady zabezpečení domény") 

     Klikněte na tlačítko **OK**.

3. V zobrazené sestavě vyberte tři tečky (...), které se zobrazí vedle **externího zřizování účtů,** a klikněte na možnost nabídky **Domain -> Edit Security Policy Permissions**

    ![Zásady zabezpečení domény](./media/workday-inbound-tutorial/wd_isu_09.png "Zásady zabezpečení domény")  

4. Na stránce **Upravit oprávnění zásad zabezpečení domény** přejděte dolů do části Oprávnění k **integraci**. Kliknutím na znaménko "+" přidáte skupinu integračního systému do seznamu skupin zabezpečení s oprávněními k integraci **Get** and **Put.**

    ![Upravit oprávnění](./media/workday-inbound-tutorial/wd_isu_10.png "Upravit oprávnění")  

5. Kliknutím na znaménko "+" přidáte skupinu integračního systému do seznamu skupin zabezpečení s oprávněními k integraci **Get** and **Put.**

    ![Upravit oprávnění](./media/workday-inbound-tutorial/wd_isu_11.png "Upravit oprávnění")  

6. Opakujte kroky 3-5 výše pro každou z těchto zbývajících zásad zabezpečení:

   | Operace | Zásady zabezpečení domény |
   | ---------- | ---------- |
   | Získat a dát | Data pracovníka: Sestavy veřejných pracovníků |
   | Získat a dát | Osobní údaje: Pracovní kontaktní informace |
   | Získat | Údaje o pracovníkovi: Všechny pozice |
   | Získat | Údaje o pracovníkovi: Aktuální informace o personálním obsazení |
   | Získat | Data pracovníka: Obchodní hlava na profilu pracovníka |
   | Získat a dát | Účty pracovního dne |

### <a name="configuring-business-process-security-policy-permissions"></a>Konfigurace oprávnění zásad zabezpečení obchodního procesu

V tomto kroku udělíte skupině zabezpečení podniku "zabezpečení obchodního procesu" pro data pracovníka. Tento krok je vyžadován pro nastavení konektoru aplikace Zpět zápisu Workday.

**Postup konfigurace oprávnění zásad zabezpečení obchodního procesu:**

1. Do vyhledávacího pole zadejte **zásady obchodního procesu** a klikněte na odkaz **Upravit úlohu zásad zabezpečení podniku.**  

    ![Zásady zabezpečení obchodních procesů](./media/workday-inbound-tutorial/wd_isu_12.png "Zásady zabezpečení obchodních procesů")  

2. V textovém poli **Typ obchodního procesu** vyhledejte *položku Kontakt* a vyberte **položku Změnit** obchodní proces a klepněte na tlačítko **OK**.

    ![Zásady zabezpečení obchodních procesů](./media/workday-inbound-tutorial/wd_isu_13.png "Zásady zabezpečení obchodních procesů")  

3. Na stránce **Upravit zásady zabezpečení podnikových procesů** přejděte do části **Udržovat kontaktní informace (webová služba).**

    ![Zásady zabezpečení obchodních procesů](./media/workday-inbound-tutorial/wd_isu_14.png "Zásady zabezpečení obchodních procesů")  

4. Vyberte a přidejte novou skupinu zabezpečení integračního systému do seznamu skupin zabezpečení, které mohou iniciovat požadavek webových služeb. Klikněte na **Hotovo**. 

    ![Zásady zabezpečení obchodních procesů](./media/workday-inbound-tutorial/wd_isu_15.png "Zásady zabezpečení obchodních procesů")  

### <a name="activating-security-policy-changes"></a>Aktivace změn zásad zabezpečení

**Aktivace změn zásad zabezpečení:**

1. Do vyhledávacího pole zadejte aktivovat a klikněte na odkaz **Aktivovat čekající změny zásad zabezpečení**.

    ![Aktivovat](./media/workday-inbound-tutorial/wd_isu_16.png "Aktivovat")

1. Spusťte úlohu Aktivovat čekající změny zásad zabezpečení zadáním komentáře pro účely auditování a klepnutím na tlačítko **OK**.
1. Úkol na další obrazovce dokončete zaškrtnutím políčka **Potvrdit**a klepnutím na tlačítko **OK**.

    ![Aktivovat čekající zabezpečení](./media/workday-inbound-tutorial/wd_isu_18.png "Aktivovat čekající zabezpečení")  

## <a name="configuring-user-provisioning-from-workday-to-active-directory"></a>Konfigurace zřizování uživatelů z pracovního dne do služby Active Directory

Tato část obsahuje postup pro zřizování uživatelských účtů z Workday do každé domény služby Active Directory v rámci integrace.

* [Přidejte aplikaci konektor uzdu a stáhněte si agenta zřizování](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent)
* [Instalace a konfigurace místního agenta zřizování](#part-2-install-and-configure-on-premises-provisioning-agents)
* [Konfigurace připojení k workday a službě Active Directory](#part-3-in-the-provisioning-app-configure-connectivity-to-workday-and-active-directory)
* [Konfigurace mapování atributů](#part-4-configure-attribute-mappings)
* [Povolení a spuštění zřizování uživatelů](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent"></a>Část 1: Přidání aplikace konektoru zřizování a stažení agenta zřizování

**Postup konfigurace zřizování služby Workday na službu Active Directory:**

1. Přejděte do části <https://portal.azure.com> (Soubor > Nový > Jiné).

2. Na webu Azure Portal vyhledejte a vyberte **Službu Azure Active Directory**.

3. Vyberte **podnikové aplikace**, potom **všechny aplikace**.

4. Vyberte **Přidat aplikaci**a vyberte kategorii **Vše.**

5. Vyhledejte **zřizování pracovního dne do služby Active Directory**a přidejte tuto aplikaci z galerie.

6. Po přidání aplikace a zobrazení obrazovky s podrobnostmi o aplikaci vyberte **Zřídit**.

7. Změňte **režim** **zřizování** na **automatický**.

8. Kliknutím na informační banner, který se zobrazí, stáhněte si zprostředkovatele zřizování. 

   ![Stáhnout agenta](./media/workday-inbound-tutorial/pa-download-agent.png "Stáhnout obrazovku agenta")


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

### <a name="part-3-in-the-provisioning-app-configure-connectivity-to-workday-and-active-directory"></a>Část 3: V aplikaci zřizování nakonfigurujte připojení k Workday a Active Directory
V tomto kroku navážeme připojení k Workday a Active Directory na webu Azure Portal. 

1. Na webu Azure Portal se vraťte k aplikaci Zřizování uživatelů služby Active Directory, která je vytvořena v [části 1.](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent)

1. Dokončete oddíl **Pověření správce** takto:

   * **Uživatelské jméno správce** – zadejte uživatelské jméno účtu systému integrace Workday s připojeným názvem domény klienta. Mělo by to vypadat jako: **uživatelské jméno\@tenant_name**

   * **Admin heslo -** Zadejte heslo účtu systému integrace Workday

   * **Adresa URL klienta –** Zadejte adresu URL koncového bodu webových služeb Workday pro vašeho tenanta. Tato hodnota by https://wd3-impl-services1.workday.com/ccx/service/contoso4měla vypadat takto: , kde *contoso4* je nahrazen správný název klienta a *wd3-impl* je nahrazen řetězec správné prostředí.

   * **Doménová struktura služby Active Directory -** "Název" domény služby Active Directory, jak je registrovánu u agenta. Pomocí rozevíracího souboru vyberte cílovou doménu pro zřizování. Tato hodnota je obvykle řetězec jako: *contoso.com*

   * **Kontejner služby Active Directory -** Zadejte dn kontejneru, kde by měl agent ve výchozím nastavení vytvářet uživatelské účty.
        Příklad: *OU =Standardní uživatelé,OU=Uživatelé,DC=contoso,DC=test*
        
     > [!NOTE]
     > Toto nastavení se používá pouze pro vytváření uživatelských účtů, pokud atribut *parentDistinguishedName* není v mapování atributů nakonfigurován. Toto nastavení se nepoužívá pro operace vyhledávání uživatelů nebo aktualizace. Celý podstrom domény spadá do rozsahu operace vyhledávání.

   * **E-mail s oznámením –** Zadejte svou e-mailovou adresu a zaškrtněte políčko Odeslat e-mail, pokud dojde k selhání.

     > [!NOTE]
     > Služba zřizování Azure AD odešle e-mailové oznámení, pokud úloha zřizování přejde do [stavu karantény.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)

   * Klepněte na tlačítko **Testovat připojení.** Pokud je test připojení úspěšný, klepněte na tlačítko **Uložit** nahoře. Pokud se nezdaří, zkontrolujte, zda jsou platná pověření Workday a pověření služby AD nakonfigurovaná v nastavení agenta.

     ![portál Azure](./media/workday-inbound-tutorial/wd_1.png)

   * Po úspěšném uložení pověření se v části **Mapování** zobrazí výchozí mapování **Synchronizovat pracovníky pracovního dne do místní služby Active Directory.**

### <a name="part-4-configure-attribute-mappings"></a>Část 4: Konfigurace mapování atributů

V této části nakonfigurujete způsob toku uživatelských dat z pracovního dne do služby Active Directory.

1. Na kartě Zřizování v části Mapování klepněte na **položku Synchronizovat pracovníky pracovního dne do místní služby Active Directory**. **Mappings**

1. V poli **Obor zdrojového objektu** můžete výběrem sad uživatelů v Workday, které mají být v oboru pro zřizování do služby AD, definováním sady filtrů založených na atributech. Výchozí obor je "všichni uživatelé v Workday". Příklady filtrů:

   * Příklad: Obor pro uživatele s ID pracovníka mezi 1000000 a 2000000 (kromě 2000000)

      * Atribut: WorkerID

      * Operátor: REGEX Shoda

      * Hodnota: (1[0-9][0-9][0-9][0-9][0-9][0-9])

   * Příklad: Pouze zaměstnanci a nepodmínění pracovníci

      * Atribut: ID zaměstnance

      * Operátor: NENÍ NULL

   > [!TIP]
   > Při konfiguraci zřizovací aplikace poprvé, budete muset otestovat a ověřit mapování atributů a výrazy a ujistěte se, že je vám požadovaný výsledek. Společnost Microsoft doporučuje používat filtry oborů v části **Obor zdrojového objektu** k testování mapování s několika testovacími uživateli z Workday. Jakmile ověříte, že mapování funguje, můžete filtr odebrat nebo jej postupně rozbalit tak, aby zahrnoval více uživatelů.

   > [!CAUTION] 
   > Výchozí chování zřizovacího modulu je zakázat nebo odstranit uživatele, kteří přejdou mimo rozsah. To nemusí být žádoucí v integraci Workday to AD. Chcete-li přepsat toto výchozí chování, přečtěte si článek [Přeskočit odstranění uživatelských účtů, které jsou mimo rozsah.](../app-provisioning/skip-out-of-scope-deletions.md)
  
1. V poli **Akce cílového objektu** můžete globálně filtrovat, jaké akce se provádějí ve službě Active Directory. **Vytvořit** a **aktualizovat** jsou nejběžnější.

1. V části **Mapování atributů** můžete definovat, jak se jednotlivé atributy Workday mapují na atributy služby Active Directory.

1. Kliknutím na existující mapování atributů jej aktualizujte nebo kliknutím na **Přidat nové mapování** v dolní části obrazovky přidejte nová mapování. Mapování jednotlivých atributů podporuje tyto vlastnosti:

      * **Typ mapování**

         * **Direct** – zapíše hodnotu atributu Workday do atributu AD bez eseři.

         * **Konstanta** – do atributu AD napíše statickou hodnotu konstantního řetězce

         * **Výraz** – umožňuje zapsat vlastní hodnotu do atributu Služby AD na základě jednoho nebo více atributů Workday. [Další informace naleznete v tomto článku o výrazech](../app-provisioning/functions-for-customizing-application-data.md).

      * **Atribut zdroje** - Atribut uživatele z Workday. Pokud hledačatributů není k dispozici, [přečtěte si část Přizpůsobení seznamu atributů uživatele Workday](#customizing-the-list-of-workday-user-attributes).

      * **Výchozí hodnota** – volitelné. Pokud má zdrojový atribut prázdnou hodnotu, mapování místo toho zapíše tuto hodnotu.
            Nejběžnější konfigurace je ponechat toto prázdné.

      * **Cílový atribut** – atribut uživatele ve službě Active Directory.

      * **Shodovat objekty pomocí tohoto atributu** – zda toto mapování by měly být použity k jednoznačné identifikaci uživatelů mezi Workday a Active Directory. Tato hodnota je obvykle nastavena v poli ID pracovníka pro Workday, které je obvykle mapováno na jeden z atributů ID zaměstnance ve službě Active Directory.

      * **Odpovídající priorita** – lze nastavit více odpovídajících atributů. Pokud existuje více, jsou vyhodnoceny v pořadí definovaném tímto polem. Jakmile je nalezena shoda, nejsou vyhodnoceny žádné další odpovídající atributy.

      * **Použít toto mapování**

         * **Vždy** – použít toto mapování na vytváření uživatelů a aktualizovat akce

         * **Pouze při vytváření** – toto mapování použít pouze u akcí vytvoření uživatele

1. Mapování můžete uložit kliknutím na **Uložit** v horní části oddílu Mapování atributů.

   ![portál Azure](./media/workday-inbound-tutorial/wd_2.png)

#### <a name="below-are-some-example-attribute-mappings-between-workday-and-active-directory-with-some-common-expressions"></a>Níže jsou uvedeny některé příklady mapování atributů mezi Workday a Active Directory, s některými běžnými výrazy

* Výraz, který se mapuje na atribut *parentDistinguishedName,* se používá k zřizování uživatele různým ruům na základě jednoho nebo více atributů zdroje Workday. Tento příklad zde umístí uživatele do různých ouslužeb na základě toho, v jakém městě se nacházejí.

* Atribut *userPrincipalName* ve službě Active Directory je generován pomocí funkce de-duplikace [SelectUniqueValue,](../app-provisioning/functions-for-customizing-application-data.md#selectuniquevalue) která kontroluje existenci generované hodnoty v cílové doméně Služby AD a nastavuje ji pouze v případě, že je jedinečná.  

* [Zde je dokumentace k psaní výrazů](../app-provisioning/functions-for-customizing-application-data.md). Tato část obsahuje příklady odebrání speciálních znaků.

| ATRIBUT PRACOVNÍ DEN | ATRIBUT SLUŽBY ACTIVE DIRECTORY |  Odpovídající ID? | VYTVOŘIT / AKTUALIZOVAT |
| ---------- | ---------- | ---------- | ---------- |
| **ID pracovníka**  |  EmployeeID | **Ano** | Napsáno pouze na vytvoření |
| **PreferredNameData**    |  Kn    |   |   Napsáno pouze na vytvoření |
| **SelectUniqueValue(\@Join(" ", \[Join(".", FirstName\] \[, LastName\]\@, "contoso.com"), Join("\[", Join(".", Mid( FirstName\], 1, 1), \[LastName\], "contoso.com"), Join("\@", Join(".", Mid(\[FirstName\], 1, 2), \[LastName\], "contoso.com"))**   | userPrincipalName (Hlavní název uživatele)     |     | Napsáno pouze na vytvoření 
| **Replace(Mid(Replace(\[\]UserID ,\[\\\\/\\\\\\\\\\\\\[\\\\\]\\\\,\\\\\\ "( : ; \\\|\\\\=\\\\,\\\\+\\\\\*\\\\? \\\\\\), ", , "", , , 1, 20), , "([ .) \\ &lt; \\ \\ &gt; \] \*](file:///\\ \$.) *$)", , "", , )**      |    sAMAccountName            |     |         Napsáno pouze na vytvoření |
| **Přepínač(\[\]Aktivní , , "0", "Pravda", "1", "False")** |  accountDisabled      |     | Vytvořit + aktualizovat |
| **Jméno**   | givenName       |     |    Vytvořit + aktualizovat |
| **Lastname**   |   sn   |     |  Vytvořit + aktualizovat |
| **PreferredNameData**  |  displayName |     |   Vytvořit + aktualizovat |
| **Společnost**         | company   |     |  Vytvořit + aktualizovat |
| **Organizace dohledu**  | Oddělení  |     |  Vytvořit + aktualizovat |
| **SprávceReference**   | manager  |     |  Vytvořit + aktualizovat |
| **Obchodní název**   |  title     |     |  Vytvořit + aktualizovat | 
| **Data řádku adresy**    |  Streetaddress  |     |   Vytvořit + aktualizovat |
| **Obec**   |   l   |     | Vytvořit + aktualizovat |
| **CountryReferenceTwoLetter**      |   Co |     |   Vytvořit + aktualizovat |
| **CountryReferenceTwoLetter**    |  c  |     |         Vytvořit + aktualizovat |
| **Odkaz na oblast země** |  st     |     | Vytvořit + aktualizovat |
| **WorkSpaceReference** | physicalDeliveryOfficeName    |     |  Vytvořit + aktualizovat |
| **Postalcode**  |   Postalcode  |     | Vytvořit + aktualizovat |
| **Primárnípracovní telefon**  |  phoneČíslo   |     | Vytvořit + aktualizovat |
| **Fax**      | facsimileČíslo telefonu     |     |    Vytvořit + aktualizovat |
| **mobilních**  |    mobil       |     |       Vytvořit + aktualizovat |
| **Místní odkaz** |  preferredLanguage  |     |  Vytvořit + aktualizovat |                                               
| **Přepínač(\[Obec,\]"OU = standardní uživatelé, OU = uživatelé, OU = výchozí, OU = umístění, DC = contoso, DC = com", "Dallas", "OU = standardní uživatelé, OU = uživatelé, OU = Dallas, OU = umístění, DC = contoso, DC = com", "Austin", "OU =Standard Users,OU=Users,OU=Austin,OU=Locations,DC=contoso,DC=com", "Seattle", "OU=Standard Users,OU=Users,OU=Seattle,OU=Locations,DC=contoso,DC=com", "London", "OU=Standard Users,OU=Users,OU=OU=Locations,DC=contoso,DC=com")**  | parentDistinguishedName     |     |  Vytvořit + aktualizovat |

Po dokončení konfigurace mapování atributů můžete nyní [povolit a spustit službu zřizování uživatelů](#enable-and-launch-user-provisioning).

## <a name="configuring-user-provisioning-to-azure-ad"></a>Konfigurace zřizování uživatelů do Azure AD

Následující části popisují kroky pro konfiguraci zřizování uživatelů z Workday do Azure AD pro nasazení pouze pro cloud.

* [Přidání aplikace konektoru zřizování Azure AD a vytvoření připojení k Workday](#part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday)
* [Konfigurace mapování atributů Workday a Azure AD](#part-2-configure-workday-and-azure-ad-attribute-mappings)
* [Povolení a spuštění zřizování uživatelů](#enable-and-launch-user-provisioning)

> [!IMPORTANT]
> Postupujte pouze podle níže uvedeného postupu, pokud máte pouze cloud uživatele, které je potřeba zřídit do Služby Azure AD a ne místně active directory.

### <a name="part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday"></a>Část 1: Přidání aplikace konektoru zřizování Azure AD a vytvoření připojení k Workday

**Konfigurace zřizování služby Workday to Azure Active Directory pro uživatele pouze pro cloud:**

1. Přejděte do části <https://portal.azure.com> (Soubor > Nový > Jiné).

2. Na webu Azure Portal vyhledejte a vyberte **Službu Azure Active Directory**.

3. Vyberte **podnikové aplikace**, potom **všechny aplikace**.

4. Vyberte **Přidat aplikaci**a pak vyberte kategorii **Vše.**

5. Vyhledejte **workday do azure ad zřizování**a přidejte tuto aplikaci z galerie.

6. Po přidání aplikace a zobrazení obrazovky s podrobnostmi o aplikaci vyberte **Zřídit**.

7. Změňte **režim** **zřizování** na **automatický**.

8. Dokončete oddíl **Pověření správce** takto:

   * **Uživatelské jméno správce** – zadejte uživatelské jméno účtu systému integrace Workday s připojeným názvem domény klienta. By měl vypadat něco jako:username@contoso4

   * **Admin heslo -** Zadejte heslo účtu systému integrace Workday

   * **Adresa URL klienta –** Zadejte adresu URL koncového bodu webových služeb Workday pro vašeho tenanta. Tato hodnota by https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resourcesměla vypadat takto: , kde *contoso4* je nahrazen správný název klienta a *wd3-impl* je nahrazen řetězec správné prostředí. Pokud tato adresa URL není známa, obraťte se na svého partnera pro integraci workday nebo zástupce podpory a určete správnou adresu URL, kterou chcete použít.

   * **E-mail s oznámením –** Zadejte svou e-mailovou adresu a zaškrtněte políčko Odeslat e-mail, pokud dojde k selhání.

   * Klepněte na tlačítko **Testovat připojení.**

   * Pokud je test připojení úspěšný, klepněte na tlačítko **Uložit** nahoře. Pokud se nezdaří, zkontrolujte, zda adresa URL workday a přihlašovací údaje jsou platné v Workday.

### <a name="part-2-configure-workday-and-azure-ad-attribute-mappings"></a>Část 2: Konfigurace mapování atributů Workday a Azure AD

V této části nakonfigurujete, jak toky uživatelských dat z Workday do Služby Azure Active Directory pro uživatele pouze pro cloud.

1. Na kartě Zřizování v části **Mapování**klikněte na **Synchronizovat pracovníky do Azure AD**.

2. V poli **Obor zdrojového objektu** můžete vybrat, které sady uživatelů v Workday by měly být v oboru pro zřizování do služby Azure AD, definováním sady filtrů založených na atributech. Výchozí obor je "všichni uživatelé v Workday". Příklady filtrů:

   * Příklad: Obor pro uživatele s ID pracovníka mezi 1000000 a 2000000

      * Atribut: WorkerID

      * Operátor: REGEX Shoda

      * Hodnota: (1[0-9][0-9][0-9][0-9][0-9][0-9])

   * Příklad: Pouze kontingentní pracovníci a ne pravidelní zaměstnanci

      * Atribut: ContingentID

      * Operátor: NENÍ NULL

3. V poli **Akce cílového objektu** můžete globálně filtrovat, jaké akce se provádějí ve službě Azure AD. **Vytvořit** a **aktualizovat** jsou nejběžnější.

4. V části **Mapování atributů** můžete definovat, jak se jednotlivé atributy Workday mapují na atributy služby Active Directory.

5. Kliknutím na existující mapování atributů jej aktualizujte nebo kliknutím na **Přidat nové mapování** v dolní části obrazovky přidejte nová mapování. Mapování jednotlivých atributů podporuje tyto vlastnosti:

   * **Typ mapování**

      * **Direct** – zapíše hodnotu atributu Workday do atributu AD bez eseři.

      * **Konstanta** – do atributu AD napíše statickou hodnotu konstantního řetězce

      * **Výraz** – umožňuje zapsat vlastní hodnotu do atributu Služby AD na základě jednoho nebo více atributů Workday. [Další informace naleznete v tomto článku o výrazech](../app-provisioning/functions-for-customizing-application-data.md).

   * **Atribut zdroje** - Atribut uživatele z Workday. Pokud hledačatributů není k dispozici, [přečtěte si část Přizpůsobení seznamu atributů uživatele Workday](#customizing-the-list-of-workday-user-attributes).

   * **Výchozí hodnota** – volitelné. Pokud má zdrojový atribut prázdnou hodnotu, mapování místo toho zapíše tuto hodnotu.
            Nejběžnější konfigurace je ponechat toto prázdné.

   * **Cílový atribut** – atribut uživatele ve službě Azure AD.

   * **Shoda objekty pomocí tohoto atributu** – Zda tento atribut by měl být použit k jednoznačné identifikaci uživatelů mezi Workday a Azure AD. Tato hodnota se obvykle nastavuje v poli ID pracovníka pro Workday, které se obvykle mapuje na atribut ID zaměstnance (nový) nebo atribut rozšíření ve službě Azure AD.

   * **Odpovídající priorita** – lze nastavit více odpovídajících atributů. Pokud existuje více, jsou vyhodnoceny v pořadí definovaném tímto polem. Jakmile je nalezena shoda, nejsou vyhodnoceny žádné další odpovídající atributy.

   * **Použít toto mapování**

     * **Vždy** – použít toto mapování na vytváření uživatelů a aktualizovat akce

     * **Pouze při vytváření** – toto mapování použít pouze u akcí vytvoření uživatele

6. Mapování můžete uložit kliknutím na **Uložit** v horní části oddílu Mapování atributů.

Po dokončení konfigurace mapování atributů můžete nyní [povolit a spustit službu zřizování uživatelů](#enable-and-launch-user-provisioning).

## <a name="configuring-azure-ad-attribute-writeback-to-workday"></a>Konfigurace zpětného zápisu atributu Azure AD na Workday

Podle těchto pokynů nakonfigurujte zpětný zápis e-mailových adres uživatelů a uživatelského jména z Azure Active Directory do Workday.

* [Přidání aplikace Konektor zpětného zápisu a vytvoření připojení k Workday](#part-1-adding-the-writeback-connector-app-and-creating-the-connection-to-workday)
* [Konfigurace mapování atributů zpětného zápisu](#part-2-configure-writeback-attribute-mappings)
* [Povolení a spuštění zřizování uživatelů](#enable-and-launch-user-provisioning)

### <a name="part-1-adding-the-writeback-connector-app-and-creating-the-connection-to-workday"></a>Část 1: Přidání aplikace konektor zpětného zápisu a vytvoření připojení k Workday

**Konfigurace konektoru zpětného zápisu pracovního dne:**

1. Přejděte do části <https://portal.azure.com> (Soubor > Nový > Jiné).

2. Na webu Azure Portal vyhledejte a vyberte **Službu Azure Active Directory**.

3. Vyberte **podnikové aplikace**, potom **všechny aplikace**.

4. Vyberte **Přidat aplikaci**a pak vyberte kategorii **Vše.**

5. Vyhledejte **zpětný zápis pracovního dne**a přidejte tuto aplikaci z galerie.

6. Po přidání aplikace a zobrazení obrazovky s podrobnostmi o aplikaci vyberte **Zřídit**.

7. Změňte **režim** **zřizování** na **automatický**.

8. Dokončete oddíl **Pověření správce** takto:

   * **Uživatelské jméno správce** – zadejte uživatelské jméno účtu systému integrace Workday s připojeným názvem domény klienta. By měl vypadat něco jako: *uživatelské jméno\@contoso4*

   * **Admin heslo -** Zadejte heslo účtu systému integrace Workday

   * **Adresa URL klienta –** Zadejte adresu URL koncového bodu webových služeb Workday pro vašeho tenanta. Tato hodnota by https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resourcesměla vypadat takto: , kde *contoso4* je nahrazen správný název klienta a *wd3-impl* je nahrazen správný řetězec prostředí (v případě potřeby).

   * **E-mail s oznámením –** Zadejte svou e-mailovou adresu a zaškrtněte políčko Odeslat e-mail, pokud dojde k selhání.

   * Klepněte na tlačítko **Testovat připojení.** Pokud je test připojení úspěšný, klepněte na tlačítko **Uložit** nahoře. Pokud se nezdaří, zkontrolujte, zda adresa URL workday a přihlašovací údaje jsou platné v Workday.

### <a name="part-2-configure-writeback-attribute-mappings"></a>Část 2: Konfigurace mapování atributů zpětného zápisu

V této části nakonfigurujete, jak tok atributů zpětného zápisu z Azure AD do Workday. V současné době konektor podporuje pouze zpětný zápis e-mailové adresy a uživatelského jména na Workday.

1. Na kartě Zřizování v části **Mapování**klikněte na **Synchronizovat uživatele služby Azure Active Directory s workday**.

2. V poli **Obor zdrojového objektu** můžete volitelně filtrovat, které sady uživatelů ve službě Azure Active Directory by měly mít své e-mailové adresy zapsané zpět do Workday. Výchozí obor je "všichni uživatelé ve službě Azure AD".

3. V části **Mapování atributů** aktualizujte odpovídající ID a označte atribut ve službě Azure Active Directory, kde je uloženo ID pracovníka pracovního dne nebo ID zaměstnance. Oblíbenou metodou párování je synchronizace ID pracovního procesu workday nebo ID zaměstnance s extensionAttribute1-15 ve službě Azure AD a pak použijte tento atribut ve službě Azure AD tak, aby odpovídal uživatelům zpět v Workday.

4. Obvykle mapujete atribut *UserPrincipalName* služby Azure AD na atribut Workday *UserID* a namapujete atribut *pošty* Azure AD na atribut *E-mailová adresa Workday.* Mapování můžete uložit kliknutím na **Uložit** v horní části oddílu Mapování atributů.

Po dokončení konfigurace mapování atributů můžete nyní [povolit a spustit službu zřizování uživatelů](#enable-and-launch-user-provisioning).

## <a name="enable-and-launch-user-provisioning"></a>Povolení a spuštění zřizování uživatelů

Po dokončení konfigurace zřizovací aplikace Workday, můžete zapnout zřizovací služby na webu Azure Portal.

> [!TIP]
> Ve výchozím nastavení při zapnutí zřizovací služby zahájí operace zřizování pro všechny uživatele v oboru. Pokud jsou chyby v mapování nebo Workday data problémy, pak úloha zřizování může selhat a přejít do stavu karantény. Chcete-li tomu zabránit, doporučujeme jako osvědčený postup konfiguraci filtru **oboru zdrojového objektu** a testování mapování atributů s několika testovacími uživateli před spuštěním úplné synchronizace pro všechny uživatele. Jakmile ověříte, že mapování funguje a dáváte vám požadované výsledky, můžete filtr odebrat nebo jej postupně rozšířit tak, aby zahrnoval více uživatelů.

1. Na kartě **Zřizování** nastavte **stav zřizování** **na Zapnuto**.

2. Klikněte na **Uložit**.

3. Tato operace spustí počáteční synchronizaci, která může trvat proměnný počet hodin v závislosti na tom, kolik uživatelů je v tenantovi Workday. 

4. Kdykoli zkontrolujte kartu **Protokoly auditu** na webu Azure Portal a zjistěte, jaké akce služba zřizování provedla. Protokoly auditu uvádí všechny jednotlivé události synchronizace prováděné zřizovací službou, například které uživatelé jsou čteny mimo Pracovní den a následně přidány nebo aktualizovány do služby Active Directory. Pokyny ke kontrole protokolů auditování a opravě chyb zřizování naleznete v části Poradce při potížích.

5. Po dokončení počáteční synchronizace sepíše souhrnnou zprávu auditu na kartu **Zřizování,** jak je znázorněno níže.

   ![portál Azure](./media/workday-inbound-tutorial/wd_3.png)

## <a name="frequently-asked-questions-faq"></a>Časté otázky

* **Otázky týkající se schopností řešení**
  * [Jak řešení při zpracování nového přijetí ze služby Workday nastavuje heslo pro nový uživatelský účet ve službě Active Directory?](#when-processing-a-new-hire-from-workday-how-does-the-solution-set-the-password-for-the-new-user-account-in-active-directory)
  * [Podporuje řešení odesílání e-mailových oznámení po dokončení operací zřizování?](#does-the-solution-support-sending-email-notifications-after-provisioning-operations-complete)
  * [Jak mohu spravovat doručování hesel pro nové zaměstnance a bezpečně poskytnout mechanismus pro resetování hesla?](#how-do-i-manage-delivery-of-passwords-for-new-hires-and-securely-provide-a-mechanism-to-reset-their-password)
  * [Ukládá řešení do mezipaměti uživatelské profily Workday v cloudu Azure AD nebo ve vrstvě agenta zřizování?](#does-the-solution-cache-workday-user-profiles-in-the-azure-ad-cloud-or-at-the-provisioning-agent-layer)
  * [Podporuje řešení přiřazování místních skupin AD uživateli?](#does-the-solution-support-assigning-on-premises-ad-groups-to-the-user)
  * [Která pracovní pole API používá řešení k dotazování a aktualizaci pracovních profilů Workday?](#which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles)
  * [Můžu nakonfigurovat svého tenanta HCM workday se dvěma tenanty Azure AD?](#can-i-configure-my-workday-hcm-tenant-with-two-azure-ad-tenants)
  * [Proč "Workday to Azure AD" zřizování aplikace uživatele není podporována, pokud jsme nasadili Azure AD Connect?](#why-workday-to-azure-ad-user-provisioning-app-is-not-supported-if-we-have-deployed-azure-ad-connect)
  * [Jak navrhnu vylepšení nebo požádám o nové funkce související s integrací Workday a Azure AD?](#how-do-i-suggest-improvements-or-request-new-features-related-to-workday-and-azure-ad-integration)

* **Zřizování agent otázky**
  * [Jaká je ga verze zřizovacího agenta?](#what-is-the-ga-version-of-the-provisioning-agent)
  * [Jak poznám verzi svého zřizovacího agenta?](#how-do-i-know-the-version-of-my-provisioning-agent)
  * [Má Microsoft automaticky nabízeny zřizovací agent aktualizace?](#does-microsoft-automatically-push-provisioning-agent-updates)
  * [Můžu nainstalovat agenta zřizování na stejný server se systémem Azure AD Connect?](#can-i-install-the-provisioning-agent-on-the-same-server-running-azure-ad-connect)
  * [Jak nakonfiguruji agenta zřizování tak, aby používal proxy server pro odchozí komunikaci HTTP?](#how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication)
  * [Jak zajistím, že agent zřizování je schopen komunikovat s tenantem Azure AD a žádné brány firewall blokují porty vyžadované agentem?](#how-do-i-ensure-that-the-provisioning-agent-is-able-to-communicate-with-the-azure-ad-tenant-and-no-firewalls-are-blocking-ports-required-by-the-agent)
  * [Jak zruším registraci domény přidružené k mému agentovi zřizování?](#how-do-i-de-register-the-domain-associated-with-my-provisioning-agent)
  * [Jak odinstaluji agenta zřizování?](#how-do-i-uninstall-the-provisioning-agent)
  
* **Otázky mapování a konfigurace atributů Workday to AD**
  * [Jak lze zálohovat nebo exportovat pracovní kopii mapování atributů a schématu zřizování pracovního dne?](#how-do-i-back-up-or-export-a-working-copy-of-my-workday-provisioning-attribute-mapping-and-schema)
  * [Mám vlastní atributy v Workday a Active Directory. Jak nakonfiguruji řešení tak, aby fungovalo s vlastními atributy?](#i-have-custom-attributes-in-workday-and-active-directory-how-do-i-configure-the-solution-to-work-with-my-custom-attributes)
  * [Mohu zřídit fotografii uživatele z pracovního dne do služby Active Directory?](#can-i-provision-users-photo-from-workday-to-active-directory)
  * [Jak synchronizuji mobilní čísla z Workday na základě souhlasu uživatele s veřejným používáním?](#how-do-i-sync-mobile-numbers-from-workday-based-on-user-consent-for-public-usage)
  * [Jak naformátuji zobrazovaná jména ve službě AD na základě atributů oddělení/země/města uživatele a zpracovat regionální odchylky?](#how-do-i-format-display-names-in-ad-based-on-the-users-departmentcountrycity-attributes-and-handle-regional-variances)
  * [Jak lze použít SelectUniqueValue ke generování jedinečných hodnot pro atribut samAccountName?](#how-can-i-use-selectuniquevalue-to-generate-unique-values-for-samaccountname-attribute)
  * [Jak odstraním znaky s diakritikou a převedu je na normální anglické abecedy?](#how-do-i-remove-characters-with-diacritics-and-convert-them-into-normal-english-alphabets)

### <a name="solution-capability-questions"></a>Otázky týkající se schopností řešení

#### <a name="when-processing-a-new-hire-from-workday-how-does-the-solution-set-the-password-for-the-new-user-account-in-active-directory"></a>Jak řešení při zpracování nového přijetí ze služby Workday nastavuje heslo pro nový uživatelský účet ve službě Active Directory?

Když místní zřizovací agent získá požadavek na vytvoření nového účtu služby AD, automaticky vygeneruje složité náhodné heslo navržené tak, aby splňovalo požadavky na složitost hesla definované serverem AD a nastaví to u objektu uživatele. Toto heslo není nikde zaznamenáno.

#### <a name="does-the-solution-support-sending-email-notifications-after-provisioning-operations-complete"></a>Podporuje řešení odesílání e-mailových oznámení po dokončení operací zřizování?

Ne, odesílání e-mailových oznámení po dokončení operací zřizování není v aktuální verzi podporováno.

#### <a name="how-do-i-manage-delivery-of-passwords-for-new-hires-and-securely-provide-a-mechanism-to-reset-their-password"></a>Jak mohu spravovat doručování hesel pro nové zaměstnance a bezpečně poskytnout mechanismus pro resetování hesla?

Jedním z posledních kroků při zřizování nového účtu služby AD je doručení dočasného hesla přiřazeného k účtu služby AD uživatele. Mnoho podniků stále používá tradiční přístup doručení dočasného hesla manažerovi uživatele, který pak předá heslo novému pracovníkovi přijetí/podmíněného pracovního poměru. Tento proces má vlastní chybu zabezpečení a je k dispozici možnost implementovat lepší přístup pomocí funkcí Azure AD.

V rámci náborového procesu hr týmy obvykle prověřují minulost a prověřují mobilní číslo nového zaměstnance. S integrace workday to AD User Provisioning můžete stavět na této skutečnosti a zavést funkci samoobslužného resetování hesla pro uživatele v den 1. Toho lze dosáhnout šířením atributu "Mobilní číslo" nového přijetí z Workday do AD a pak ze služby AD do služby Azure AD pomocí služby Azure AD Connect. Jakmile "Mobilní číslo" je k dispozici ve službě Azure AD, můžete povolit [samoobslužné resetování hesla (SSPR)](../authentication/howto-sspr-authenticationdata.md) pro uživatelský účet, tak, aby v den 1, nový pronájem můžete použít registrované a ověřené číslo mobilního telefonu pro ověřování.

#### <a name="does-the-solution-cache-workday-user-profiles-in-the-azure-ad-cloud-or-at-the-provisioning-agent-layer"></a>Ukládá řešení do mezipaměti uživatelské profily Workday v cloudu Azure AD nebo ve vrstvě agenta zřizování?

Ne, řešení neudržuje mezipaměť uživatelských profilů. Zřizovací služba Azure AD jednoduše funguje jako procesor dat, čtení dat z Workday a zápis do cílové služby Active Directory nebo Azure AD. Podrobnosti týkající se ochrany osobních údajů a uchovávání dat uživatele naleznete v části [Správa osobních údajů.](#managing-personal-data)

#### <a name="does-the-solution-support-assigning-on-premises-ad-groups-to-the-user"></a>Podporuje řešení přiřazování místních skupin AD uživateli?

Tato funkce není aktuálně podporována. Doporučeným zástupným zástupem je nasazení skriptu prostředí PowerShell, který se dotazuje koncového bodu rozhraní MICROSOFT Graph API na [data protokolu auditování](https://docs.microsoft.com/graph/api/resources/azure-ad-auditlog-overview?view=graph-rest-beta) a použije se k aktivaci scénářů, jako je přiřazení skupiny. Tento skript prostředí PowerShell lze připojit k plánovači úloh a nasadit na stejné pole se systémem zřizovacího agenta.  

#### <a name="which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles"></a>Která pracovní pole API používá řešení k dotazování a aktualizaci pracovních profilů Workday?

Řešení aktuálně používá následující pracovní pole API:

* Get_Workers (v21.1) pro načítání informací o pracovníkovi
* Maintain_Contact_Information (v26.1) pro funkci zpětný zápis pracovního e-mailu
* Update_Workday_Account (v31.2) pro funkci zpětného zápisu uživatelského jména

#### <a name="can-i-configure-my-workday-hcm-tenant-with-two-azure-ad-tenants"></a>Můžu nakonfigurovat svého tenanta HCM workday se dvěma tenanty Azure AD?

Ano, tato konfigurace je podporována. Zde jsou kroky na vysoké úrovni pro konfiguraci tohoto scénáře:

* Nasaďte agenta zřizování #1 a zaregistrujte ho u #1 tenanta Azure AD.
* Nasaďte agenta zřizování #2 a zaregistrujte ho u #2 klienta Azure AD.
* Na základě "Podřízené domény", které bude spravovat každý agent zřizování, nakonfigurujte každého agenta s doménami. Jeden agent může zpracovat více domén.
* Na webu Azure Portal nastavte aplikaci Workday to AD User Provisioning V každém klientovi a nakonfigurujte ji s příslušnými doménami.

#### <a name="why-workday-to-azure-ad-user-provisioning-app-is-not-supported-if-we-have-deployed-azure-ad-connect"></a>Proč "Workday to Azure AD" zřizování aplikace uživatele není podporována, pokud jsme nasadili Azure AD Connect?

Když Azure AD se používá v hybridním režimu (kde obsahuje kombinaci cloud + místní uživatele), je důležité mít jasnou definici "zdroj autority". Hybridní scénáře obvykle vyžadují nasazení služby Azure AD Connect. Při nasazení služby Azure AD Connect je místní služba AD zdrojem autority. Zavedení konektoru Workday to Azure AD do mixu může vést k situaci, kdy hodnoty atributů Workday mohou potenciálně přepsat hodnoty nastavené službou Azure AD Connect. Proto použití zřizování "Workday to Azure AD" aplikace není podporována, když je povolena Azure AD Connect. V takových situacích doporučujeme použít zřizovací aplikaci "Workday to AD User" pro získání uživatelů do místní hodu a jejich synchronizaci do Azure AD pomocí Azure AD Connect.

#### <a name="how-do-i-suggest-improvements-or-request-new-features-related-to-workday-and-azure-ad-integration"></a>Jak navrhnu vylepšení nebo požádám o nové funkce související s integrací Workday a Azure AD?

Vaše zpětná vazba je vysoce ceněna, protože nám pomáhá nastavit směr pro budoucí verze a vylepšení. Uvítáme veškerou zpětnou vazbu a doporučujeme vám odeslat svůj nápad nebo návrh zlepšení ve [fóru pro zpětnou vazbu azure ad](https://feedback.azure.com/forums/169401-azure-active-directory). Pro konkrétní zpětnou vazbu související s integrací Workday vyberte kategorii *Aplikace SaaS* a vyhledejte pomocí klíčových slov *Workday* existující zpětnou vazbu týkající se workday.

![Aplikace UserVoice SaaS](media/workday-inbound-tutorial/uservoice_saas_apps.png)

![UserVoice Pracovní den](media/workday-inbound-tutorial/uservoice_workday_feedback.png)

Při navrhování nového nápadu zkontrolujte, zda již podobnou funkci nenavrhl někdo jiný. V takovém případě můžete hlasovat o funkci nebo žádosti o vylepšení. Můžete také zanechat komentář týkající se vašeho konkrétního případu použití, abyste ukázali svou podporu pro myšlenku a ukázali, jak bude tato funkce pro vás také cenná.

### <a name="provisioning-agent-questions"></a>Zřizování agent otázky

#### <a name="what-is-the-ga-version-of-the-provisioning-agent"></a>Jaká je ga verze zřizovacího agenta?

* Ga verze zřizovacího agenta je 1.1.30 a vyšší.
* Pokud je verze agenta menší než 1.1.30, spustíte verzi Public Preview a bude automaticky aktualizována na verzi GA, pokud má server hostující agenta runtime .NET 4.7.1.
  * Můžete [zkontrolovat verzi rozhraní .NET](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) nainstalovanou na serveru. Pokud server nepoužívá rozhraní .NET 4.7.1, můžete [stáhnout a nainstalovat rozhraní .NET 4.7.1](https://support.microsoft.com/help/4033342/the-net-framework-4-7-1-offline-installer-for-windows). Váš zřizovací agent bude automaticky aktualizován na verzi GA po instalaci .NET 4.7.1.

#### <a name="how-do-i-know-the-version-of-my-provisioning-agent"></a>Jak poznám verzi svého zřizovacího agenta?

* Přihlaste se k serveru Windows, kde je nainstalován agent zřizování.
* Přejít na**nabídku Odinstalovat nebo změnit program v** **Ovládacích panelech** -> 
* Vyhledejte verzi odpovídající položce **Microsoft Azure AD Connect Provisioning Agent**

  ![portál Azure](./media/workday-inbound-tutorial/pa_version.png)

#### <a name="does-microsoft-automatically-push-provisioning-agent-updates"></a>Má Microsoft automaticky nabízeny zřizovací agent aktualizace?

Ano, Společnost Microsoft automaticky aktualizuje zřizovacího agenta. Automatické aktualizace můžete zakázat zastavením služby Windows **Microsoft Azure AD Connect Agent Updater**.

#### <a name="can-i-install-the-provisioning-agent-on-the-same-server-running-azure-ad-connect"></a>Můžu nainstalovat agenta zřizování na stejný server se systémem Azure AD Connect?

Ano, můžete nainstalovat zřizovacíagent na stejném serveru, který běží Azure AD Connect.

#### <a name="at-the-time-of-configuration-the-provisioning-agent-prompts-for-azure-ad-admin-credentials-does-the-agent-store-the-credentials-locally-on-the-server"></a>V době konfigurace zřizovací agent vyzve k zadání přihlašovacích údajů správce Azure AD. Ukládá agent pověření místně na serveru?

Během konfigurace vyzve agenta zřizování přihlašovací ch pověření správce Azure AD, abyste se jen připojili k tenantovi Azure AD. Neukládá pověření místně na serveru. Zachová však pověření používaná k připojení k *místní doméně služby Active Directory* v místním trezoru hesel systému Windows.

#### <a name="how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication"></a>Jak nakonfiguruji agenta zřizování tak, aby používal proxy server pro odchozí komunikaci HTTP?

Agent zřizování podporuje použití odchozí proxy serveru. Můžete jej nakonfigurovat úpravou konfiguračního souboru agenta **C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\AADConnectProvisioningAgent.exe.config**. Přidejte do něj následující řádky ke konci souboru `</configuration>` těsně před uzavírací značku.
Nahraďte proměnné [proxy-server] a [proxy-port] názvem proxy serveru a hodnotami portů.

```xml
    <system.net>
          <defaultProxy enabled="true" useDefaultCredentials="true">
             <proxy
                usesystemdefault="true"
                proxyaddress="http://[proxy-server]:[proxy-port]"
                bypassonlocal="true"
             />
         </defaultProxy>
    </system.net>
```

#### <a name="how-do-i-ensure-that-the-provisioning-agent-is-able-to-communicate-with-the-azure-ad-tenant-and-no-firewalls-are-blocking-ports-required-by-the-agent"></a>Jak zajistím, že agent zřizování je schopen komunikovat s tenantem Azure AD a žádné brány firewall blokují porty vyžadované agentem?

Můžete také zkontrolovat, zda máte všechny požadované porty otevřené otevřením [nástroje Connector Ports Test Tool](https://aadap-portcheck.connectorporttest.msappproxy.net/) z místní sítě. Více zelených značek znamená větší odolnost proti chybám.

Chcete-li se ujistit, že nástroj poskytuje správné výsledky, ujistěte se, že:

* Otevřete nástroj v prohlížeči ze serveru, na kterém jste nainstalovali agenta zřizování.
* Ujistěte se, že všechny proxy servery nebo brány firewall, které se vztahují na vašeho agenta zřizování jsou také použity na této stránce. To lze provést v aplikaci Internet Explorer najdete **v nastavení -> Možnosti Internetu -> Připojení -> LAN Nastavení**. Na této stránce se zobrazí pole "Použít proxy server pro vaši síť LAN". Toto políčko zaškrtněte a adresu proxy vložte do pole Adresa.

#### <a name="can-one-provisioning-agent-be-configured-to-provision-multiple-ad-domains"></a>Může být jeden agent zřizování nakonfigurován tak, aby zřazoval více domén služby AD?

Ano, jednoho agenta zřizování lze nakonfigurovat tak, aby zpracovával více domén služby AD, pokud má zorné pole na příslušné řadiče domény. Společnost Microsoft doporučuje nastavit skupinu 3 zřizovacích agentů obsluhujících stejnou sadu domén služby AD, aby byla zajištěna vysoká dostupnost a poskytovala podporu převzetí služeb při selhání.

#### <a name="how-do-i-de-register-the-domain-associated-with-my-provisioning-agent"></a>Jak zruším registraci domény přidružené k mému agentovi zřizování?

* Z webu Azure Portal získejte *ID klienta* vašeho klienta Azure AD.
* Přihlaste se k serveru Windows se systémem Zřizovací agent.
* Otevřete prostředí PowerShell jako správce systému Windows.
* Změňte adresář obsahující registrační skripty a spusťte následující \[příkazy, které nahradí parametr ID\] klienta hodnotou ID klienta.

  ```powershell
  cd “C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\RegistrationPowershell\Modules\PSModulesFolder”
  Import-Module "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\RegistrationPowershell\Modules\PSModulesFolder\AppProxyPSModule.psd1"
  Get-PublishedResources -TenantId "[tenant ID]"
  ```

* Ze seznamu agentů, kteří se zobrazí `id` – zkopírujte hodnotu pole z tohoto prostředku, jehož *název_prostředku* se rovná názvu domény služby AD.
* Vložte hodnotu ID do tohoto příkazu a spusťte příkaz v Prostředí PowerShell.

  ```powershell
  Remove-PublishedResource -ResourceId "[resource ID]" -TenantId "[tenant ID]"
  ```

* Znovu spusťte průvodce konfigurací agenta.
* Ostatní agenti, kteří byli dříve přiřazeni k této doméně, budou muset být překonfigurováni.

#### <a name="how-do-i-uninstall-the-provisioning-agent"></a>Jak odinstaluji agenta zřizování?

* Přihlaste se k serveru Windows, kde je nainstalován agent zřizování.
* Přejít na**nabídku Odinstalovat nebo změnit program v** **Ovládacích panelech** -> 
* Odinstalujte následující programy:
  * Agent zřizování připojení Microsoft Azure AD
  * Aktualizace agenta microsoft Azure AD Connect
  * Balíček agenta zřizování připojení Microsoft Azure AD

### <a name="workday-to-ad-attribute-mapping-and-configuration-questions"></a>Otázky mapování a konfigurace atributů Workday to AD

#### <a name="how-do-i-back-up-or-export-a-working-copy-of-my-workday-provisioning-attribute-mapping-and-schema"></a>Jak lze zálohovat nebo exportovat pracovní kopii mapování atributů a schématu zřizování pracovního dne?

Rozhraní Microsoft Graph API můžete použít k exportu konfigurace zřizování uživatelů pracovního dne. Podrobnosti naleznete v krocích v části [Export a import konfigurace mapování atributů zřizování uživatelů pracovního dne.](#exporting-and-importing-your-configuration)

#### <a name="i-have-custom-attributes-in-workday-and-active-directory-how-do-i-configure-the-solution-to-work-with-my-custom-attributes"></a>Mám vlastní atributy v Workday a Active Directory. Jak nakonfiguruji řešení tak, aby fungovalo s vlastními atributy?

Řešení podporuje vlastní atributy Workday a Active Directory. Chcete-li přidat vlastní atributy do schématu mapování, otevřete okno **Mapování atributů** a posuňte se dolů a rozbalte oddíl **Zobrazit upřesňující možnosti**. 

![Upravit seznam atributů](./media/workday-inbound-tutorial/wd_edit_attr_list.png)

Chcete-li přidat vlastní atributy Pracovníden, vyberte možnost *Upravit seznam atributů pro pracovní den* a přidejte vlastní atributy služby AD, vyberte možnost Upravit seznam *atributů pro místní službu Active Directory*.

Viz také:

* [Přizpůsobení seznamu atributů uživatele Workday](#customizing-the-list-of-workday-user-attributes)

#### <a name="how-do-i-configure-the-solution-to-only-update-attributes-in-ad-based-on-workday-changes-and-not-create-any-new-ad-accounts"></a>Jak nakonfiguruji řešení tak, aby aktualizovalo jenom atributy ve službě AD na základě změn pracovního dne a nevytvářelo žádné nové účty služby AD?

Této konfigurace lze dosáhnout nastavením **akce cílového objektu** v okně **Mapování atributů,** jak je znázorněno níže:

![Akce aktualizace](./media/workday-inbound-tutorial/wd_target_update_only.png)

Zaškrtněte políčko "Aktualizovat", aby se z pracovního dne do služby AD přetekly pouze operace aktualizace. 

#### <a name="can-i-provision-users-photo-from-workday-to-active-directory"></a>Mohu zřídit fotografii uživatele z pracovního dne do služby Active Directory?

Řešení v současné době nepodporuje nastavení binárních atributů, jako jsou *thumbnailPhoto* a *jpegPhoto* ve službě Active Directory.

#### <a name="how-do-i-sync-mobile-numbers-from-workday-based-on-user-consent-for-public-usage"></a>Jak synchronizuji mobilní čísla z Workday na základě souhlasu uživatele s veřejným používáním?

* Přejděte okno "Zřizování" vaší aplikace pro zřizování pracovního dne.
* Klikněte na mapování atributů 
* V části Mapování vyberte **synchronizovat pracovníky pracovního dne do místního služby Active Directory** (nebo **synchronizovat pracovníky pracovního dne do služby Azure AD).** **Mappings**
* Na stránce Mapování atributů přejděte dolů a zaškrtněte políčko "Zobrazit rozšířené možnosti".  Klikněte na **Upravit atribut seznamu pro Pracovní den**
* V okně, které se otevře, vyhledejte atribut "Mobile" a klikněte na řádek, abyste mohli upravit **výraz** ![API Mobile GDPR](./media/workday-inbound-tutorial/mobile_gdpr.png)

* Nahraďte **výraz rozhraní API** následujícím novým výrazem, který načte číslo mobilního telefonu práce pouze v případě, že je příznak veřejného použití v workday nastaven na hodnotu "True".

    ```
     wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Contact_Data/wd:Phone_Data[translate(string(wd:Phone_Device_Type_Reference/@wd:Descriptor),'abcdefghijklmnopqrstuvwxyz','ABCDEFGHIJKLMNOPQRSTUVWXYZ')='MOBILE' and translate(string(wd:Usage_Data/wd:Type_Data/wd:Type_Reference/@wd:Descriptor),'abcdefghijklmnopqrstuvwxyz','ABCDEFGHIJKLMNOPQRSTUVWXYZ')='WORK' and string(wd:Usage_Data/@wd:Public)='1']/@wd:Formatted_Phone
    ```

* Uložte seznam atributů.
* Uložte mapování atributů.
* Vymažte aktuální stav a restartujte úplnou synchronizaci.

#### <a name="how-do-i-format-display-names-in-ad-based-on-the-users-departmentcountrycity-attributes-and-handle-regional-variances"></a>Jak naformátuji zobrazovaná jména ve službě AD na základě atributů oddělení/země/města uživatele a zpracovat regionální odchylky?

Je běžným požadavkem nakonfigurovat atribut *displayName* ve službě AD tak, aby také poskytoval informace o oddělení uživatele a zemi nebo oblasti. Pokud například John Smith pracuje v oddělení marketingu v USA, můžete chtít, aby se jeho *displayName* zobrazoval jako *Smith, John (Marketing-US)*.

Zde je, jak můžete zpracovat tyto požadavky pro vytváření *CN* nebo *displayName* zahrnout atributy, jako je společnost, organizační jednotka, město nebo země nebo oblast.

* Každý atribut Workday je načten pomocí podkladového výrazu rozhraní API XPATH, který lze konfigurovat v **seznamu atributů Mapování -> Rozšířený oddíl -> Upravit atribut pro Workday**. Zde je výchozí výraz rozhraní API XPATH pro atributy Workday *PreferredFirstName*, *PreferredLastName*, *Company* a *SupervisoryOrganization.*

     | Atribut Pracovní den | Výraz XPATH rozhraní API |
     | ----------------- | -------------------- |
     | PreferredFirstName | wd:Pracovník/wd:Worker_Data/wd:Personal_Data/wd:Name_Data/wd:Preferred_Name_Data/wd:Name_Detail_Data/wd:First_Name/text() |
     | PreferredLastName | wd:Pracovník/wd:Worker_Data/wd:Personal_Data/wd:Name_Data/wd:Preferred_Name_Data/wd:Name_Detail_Data/wd:Last_Name/text() |
     | Společnost | wd:Worker/wd:Worker_Data/wd:Organization_Data/wd:Worker_Organization_Data[wd:Organization_Data/wd:Organization_Type_Reference/wd:ID[@wd:type='Organization_Type_ID']='Společnost']/wd:Organization_Reference/@wd:Descriptor |
     | Organizace dohledu | wd:Worker/wd:Worker_Data/wd:Organization_Data/wd:Worker_Organization_Data/wd:Organization_Data[wd:Organization_Type_Reference/wd:ID[@wd:type='Organization_Type_ID']='Supervisory']/wd:Organization_Name/text() |
  
   Potvrďte se svým týmem Workday, že výše uvedený výraz rozhraní API je platný pro konfiguraci klienta Workday. V případě potřeby je můžete upravit tak, jak je popsáno v části [Přizpůsobení seznamu uživatelských atributů Workday](#customizing-the-list-of-workday-user-attributes).

* Podobně informace o zemi přítomné v Workday jsou načteny pomocí následujícího XPATH: *wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference*

     V části Seznam atributů Pracovní den je k dispozici 5 atributů souvisejících se zemí.

     | Atribut Pracovní den | Výraz XPATH rozhraní API |
     | ----------------- | -------------------- |
     | Odkaz na zemi | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/wd:ID[@wd:type='ISO_3166-1_Alpha-3_Code']/text() |
     | CountryReferenceFriendly | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/@wd:Descriptor |
     | ZeměReferenceNumeric | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/wd:ID[@wd:type='ISO_3166-1_Numeric-3_Code']/text() |
     | CountryReferenceTwoLetter | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/wd:ID[@wd:type='ISO_3166-1_Alpha-2_Code']/text() |
     | Odkaz na oblast země | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Region_Reference/@wd:Descriptor |

  Potvrďte se svým týmem Workday, že výše uvedené výrazy rozhraní API jsou platné pro konfiguraci klienta Workday. V případě potřeby je můžete upravit tak, jak je popsáno v části [Přizpůsobení seznamu uživatelských atributů Workday](#customizing-the-list-of-workday-user-attributes).

* Chcete-li vytvořit správný výraz mapování atributů, určete, který atribut Workday "autoritativně" představuje jméno, příjmení uživatele, zemi nebo oblast a oddělení. Řekněme, že atributy jsou *PreferredFirstName*, *PreferredLastName*, *CountryReferenceTwoLetter* a *SupervisoryOrganization.* Můžete použít k vytvoření výrazu pro atribut *AD displayName* následujícím způsobem, abyste získali zobrazovaný název jako *Smith, Jan (Marketing-US).*

    ```
     Append(Join(", ",[PreferredLastName],[PreferredFirstName]), Join(""," (",[SupervisoryOrganization],"-",[CountryReferenceTwoLetter],")"))
    ```
    Jakmile budete mít správný výraz, upravte tabulku Mapování atributů a upravte mapování atributů *displayName,* jak je znázorněno níže: ![Mapování vlastností DisplayName](./media/workday-inbound-tutorial/wd_displayname_map.png)

* Rozšíření výše uvedeného příkladu, řekněme, že chcete převést názvy měst pocházející z Workday do zkrácené hodnoty a pak použít k vytvoření zobrazované *názvy,* jako je *Například Smith, John (CHI)* nebo *Doe, Jane (NYC)*, pak tento výsledek lze dosáhnout pomocí výrazu Switch s Atribut emoty Workday jako determinant proměnné.

     ```
    Switch
    (
      [Municipality],
      Join(", ", [PreferredLastName], [PreferredFirstName]),  
           "Chicago", Append(Join(", ",[PreferredLastName], [PreferredFirstName]), "(CHI)"),
           "New York", Append(Join(", ",[PreferredLastName], [PreferredFirstName]), "(NYC)"),
           "Phoenix", Append(Join(", ",[PreferredLastName], [PreferredFirstName]), "(PHX)")
    )
     ```
    Viz také:
  * [Přepnout syntaxi funkce](../app-provisioning/functions-for-customizing-application-data.md#switch)
  * [Syntaxe funkce spojení](../app-provisioning/functions-for-customizing-application-data.md#join)
  * [Syntaxe funkce připojení](../app-provisioning/functions-for-customizing-application-data.md#append)

#### <a name="how-can-i-use-selectuniquevalue-to-generate-unique-values-for-samaccountname-attribute"></a>Jak lze použít SelectUniqueValue ke generování jedinečných hodnot pro atribut samAccountName?

Řekněme, že chcete generovat jedinečné hodnoty pro atribut *samAccountName* pomocí kombinace atributů *FirstName* a *LastName* z Workday. Níže je uveden výraz, který můžete začít s:

```
SelectUniqueValue(
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,1), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , ),
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,2), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , ),
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,3), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , )
)
```

Jak výše uvedený výraz funguje: Pokud je uživatel John Smith, nejprve se pokusí generovat JSmith, pokud JSmith již existuje, pak generuje JoSmith, pokud existuje, generuje JohSmith. Výraz také zajišťuje, že vygenerovaná hodnota splňuje omezení délky a omezení speciálních znaků spojené s *samAccountName*.

Viz také:

* [Syntaxe střední funkce](../app-provisioning/functions-for-customizing-application-data.md#mid)
* [Nahradit syntaxi funkce](../app-provisioning/functions-for-customizing-application-data.md#replace)
* [Syntaxe funkce SelectUniqueValue](../app-provisioning/functions-for-customizing-application-data.md#selectuniquevalue)

#### <a name="how-do-i-remove-characters-with-diacritics-and-convert-them-into-normal-english-alphabets"></a>Jak odstraním znaky s diakritikou a převedu je na normální anglické abecedy?

Pomocí funkce [NormalizeDiacritics](../app-provisioning/functions-for-customizing-application-data.md#normalizediacritics) odeberte speciální znaky v křestním jménu a příjmení uživatele při vytváření e-mailové adresy nebo hodnoty CN pro uživatele.

## <a name="troubleshooting-tips"></a>Rady pro řešení potíží

Tato část obsahuje konkrétní pokyny, jak řešit problémy s zřizováním s integrací Workday pomocí protokolů auditování Azure AD a Windows Server Event Viewer. Vychází z obecných kroků a konceptů řešení potíží zachycených v [kurzu: Vytváření přehledů o automatickém zřizování uživatelských účtů.](../app-provisioning/check-status-user-account-provisioning.md)

Tato část popisuje následující aspekty řešení potíží:

* [Nastavení prohlížeče událostí systému Windows pro řešení potíží s agentem](#setting-up-windows-event-viewer-for-agent-troubleshooting)
* [Nastavení protokolů auditu portálu Azure pro řešení potíží se službou](#setting-up-azure-portal-audit-logs-for-service-troubleshooting)
* [Principy protokolů pro operace vytvoření uživatelského účtu ad](#understanding-logs-for-ad-user-account-create-operations)
* [Principy protokolů pro operace aktualizace správce](#understanding-logs-for-manager-update-operations)
* [Řešení běžně se vyskytujících chyb](#resolving-commonly-encountered-errors)

### <a name="setting-up-windows-event-viewer-for-agent-troubleshooting"></a>Nastavení prohlížeče událostí systému Windows pro řešení potíží s agentem

* Přihlášení k počítači se systémem Windows Server, ve kterém je nasazen agent zřizování
* Sem otevřete desktopovou aplikaci **Windows Server Event Viewer.**
* Vyberte **položku Windows Logs > aplikace**.
* Použít **aktuální protokol filtru...** možnost zobrazit všechny události zaznamenané pod zdrojovým **akustem. Connect.ProvisioningAgent** a vyloučit události s ID události "5", zadáním filtru "-5", jak je znázorněno níže.

  ![Prohlížeč událostí systému Windows](media/workday-inbound-tutorial/wd_event_viewer_01.png))

* Klikněte na **OK** a seřaďte výsledný pohled podle **sloupce Datum a čas.**

### <a name="setting-up-azure-portal-audit-logs-for-service-troubleshooting"></a>Nastavení protokolů auditu portálu Azure pro řešení potíží se službou

* Spusťte [portál Azure](https://portal.azure.com)a přejděte do části **Protokoly auditu** v aplikaci zřizování pracovního dne.
* Pomocí tlačítka **Sloupce** na stránce Protokoly auditu můžete zobrazit pouze následující sloupce v zobrazení (datum, aktivita, stav, důvod stavu). Tato konfigurace zajišťuje, že se zaměřujete pouze na data, která jsou relevantní pro řešení potíží.

  ![Auditovat sloupce protokolu](media/workday-inbound-tutorial/wd_audit_logs_00.png)

* K filtrování zobrazení použijte parametry dotazu **Cíl** a **Rozsah dat.** 
  * Nastavte parametr **cílového** dotazu na "ID pracovníka" nebo "ID zaměstnance" pracovního objektu Workday.
  * Nastavte **rozsah dat** na vhodné časové období, po které chcete prozkoumat chyby nebo problémy s zřizováním.

  ![Filtry protokolu auditu](media/workday-inbound-tutorial/wd_audit_logs_01.png)

### <a name="understanding-logs-for-ad-user-account-create-operations"></a>Principy protokolů pro operace vytvoření uživatelského účtu ad

Když je zjištěn nový nájem v Workday (řekněme s ID zaměstnance *21023*), služba zřizování Azure AD se pokusí vytvořit nový uživatelský účet AD pro pracovníka a v procesu vytvoří 4 záznamy protokolu auditu, jak je popsáno níže:

  [![Auditovat protokol vytvořit ops](media/workday-inbound-tutorial/wd_audit_logs_02.png)](media/workday-inbound-tutorial/wd_audit_logs_02.png#lightbox)

Když kliknete na některý ze záznamů protokolu auditu, otevře se stránka **Podrobnosti o aktivitě.** Zde je stránka **Podrobnosti o aktivitě** zobrazena pro každý typ záznamu protokolu.

* Záznam **importu pracovního dne:** Tento záznam protokolu zobrazuje informace o pracovníkovi načtené z pracovního dne. Pomocí informací v části *Další podrobnosti* záznamu protokolu můžete řešit problémy s načítáním dat z workday. Příklad záznamu je uveden níže spolu s odkazy na způsob interpretace jednotlivých polí.

  ```JSON
  ErrorCode : None  // Use the error code captured here to troubleshoot Workday issues
  EventName : EntryImportAdd // For full sync, value is "EntryImportAdd" and for delta sync, value is "EntryImport"
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID (usually the Worker ID or Employee ID field)
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the record
  ```

* Záznam **importu ad:** Tento záznam protokolu zobrazuje informace o účtu načteném ze ad. Vzhledem k tomu, že při počátečním vytvoření uživatele neexistuje žádný účet služby AD, *důvod stavu aktivity* bude znamenat, že ve službě Active Directory nebyl nalezen žádný účet s hodnotou atributu Odpovídající ID. Pomocí informací v části *Další podrobnosti* záznamu protokolu můžete řešit problémy s načítáním dat z workday. Příklad záznamu je uveden níže spolu s odkazy na způsob interpretace jednotlivých polí.

  ```JSON
  ErrorCode : None // Use the error code captured here to troubleshoot Workday issues
  EventName : EntryImportObjectNotFound // Implies that object was not found in AD
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID
  ```

  Chcete-li najít záznamy protokolu agenta zřizování odpovídající této operaci importu služby AD, otevřete protokoly prohlížeče událostí systému Windows a použijte **najít...** možnost nabídky pro vyhledání položek protokolu obsahujících hodnotu atributu Odpovídající ID/Joining Property (v tomto případě *21023).*

  ![Vyhledávání](media/workday-inbound-tutorial/wd_event_viewer_02.png)

  Vyhledejte položku s *ID události = 9*, která vám poskytne vyhledávací filtr LDAP používaný agentem k načtení účtu ad. Můžete ověřit, zda se jedná o správný vyhledávací filtr pro načtení jedinečných uživatelských položek.

  ![Vyhledávání LDAP](media/workday-inbound-tutorial/wd_event_viewer_03.png)

  Záznam, který bezprostředně následuje s *ID události = 2,* zachycuje výsledek operace hledání a pokud vrátil nějaké výsledky.

  ![Výsledky protokolu LDAP](media/workday-inbound-tutorial/wd_event_viewer_04.png)

* **Záznam akce pravidla synchronizace:** Tento záznam protokolu zobrazuje výsledky pravidel mapování atributů a nakonfigurované filtry oborů spolu s akcí zřizování, která bude provedena ke zpracování události příchozího pracovního dne. Informace v části *Další podrobnosti* záznamu protokolu použijte k řešení problémů s akcí synchronizace. Příklad záznamu je uveden níže spolu s odkazy na způsob interpretace jednotlivých polí.

  ```JSON
  ErrorCode : None // Use the error code captured here to troubleshoot sync issues
  EventName : EntrySynchronizationAdd // Implies that the object will be added
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the profile in Workday
  ```

  Pokud se objeví problémy s výrazy mapování atributů nebo příchozí data Workday má problémy (například: prázdná nebo nulová hodnota pro požadované atributy), pak budete pozorovat selhání v této fázi s ErrorCode poskytující podrobnosti o selhání.

* Záznam **exportu ad:** Tento záznam protokolu zobrazuje výsledek operace vytvoření účtu ad spolu s hodnotami atributů, které byly v procesu nastaveny. Pomocí informací v části *Další podrobnosti* záznamu protokolu můžete řešit problémy s operací vytvoření účtu. Příklad záznamu je uveden níže spolu s odkazy na způsob interpretace jednotlivých polí. V části "Další podrobnosti" je "EventName" nastavenna na "EntryExportAdd", "JoiningProperty" je nastavena na hodnotu odpovídající ID atribut, "SourceAnchor" je nastavena na WorkdayID (WID) spojené se záznamem a "TargetAnchor" je nastavena na hodnota atributu AD "ObjectGuid" nově vytvořeného uživatele. 

  ```JSON
  ErrorCode : None // Use the error code captured here to troubleshoot AD account creation issues
  EventName : EntryExportAdd // Implies that object will be created
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the profile in Workday
  TargetAnchor : 83f0156c-3222-407e-939c-56677831d525 // set to the value of the AD "objectGuid" attribute of the new user
  ```

  Chcete-li najít záznamy protokolu agenta zřizování odpovídající této operaci exportu služby AD, otevřete protokoly prohlížeče událostí systému Windows a použijte **najít...** možnost nabídky pro vyhledání položek protokolu obsahujících hodnotu atributu Odpovídající ID/Joining Property (v tomto případě *21023).*  

  Vyhledejte záznam HTTP POST odpovídající časovému razítku operace exportu s *ID události = 2*. Tento záznam bude obsahovat hodnoty atributů odeslané zřizovací službou agentovi zřizování.

  [![Přidat SCIM](media/workday-inbound-tutorial/wd_event_viewer_05.png)](media/workday-inbound-tutorial/wd_event_viewer_05.png#lightbox)

  Bezprostředně po výše uvedené události by měla být další událost, která zachycuje odpověď operace vytvořit účet služby AD. Tato událost vrátí nový objektGuid vytvořený ve službě AD a je nastaven jako atribut TargetAnchor ve službě zřizování.

  [![Přidat SCIM](media/workday-inbound-tutorial/wd_event_viewer_06.png)](media/workday-inbound-tutorial/wd_event_viewer_06.png#lightbox)

### <a name="understanding-logs-for-manager-update-operations"></a>Principy protokolů pro operace aktualizace správce

Atribut správce je referenční atribut ve službě AD. Služba zřizování nenastavuje atribut správce jako součást operace vytvoření uživatele. Atribut správce je spíše nastaven jako součást operace *aktualizace* po vytvoření účtu služby AD pro uživatele. Když rozbalíte výše uvedený příklad, řekněme, že v workday je aktivován nový zaměstnanecký účet s ID zaměstnance "21451" a manažer nového zaměstnance (*21023*) již má účet Služby AD. V tomto scénáři hledání protokoly auditu pro uživatele 21451 zobrazí 5 položek.

  [![Aktualizace správce](media/workday-inbound-tutorial/wd_audit_logs_03.png)](media/workday-inbound-tutorial/wd_audit_logs_03.png#lightbox)

První 4 záznamy jsou jako ty, které jsme prozkoumali jako součást operace vytvoření uživatele. 5. záznam je export přidružený k aktualizaci atributu správce. Záznam protokolu zobrazuje výsledek operace aktualizace správce účtů služby AD, která se provádí pomocí atributu *objectGuid* správce.

  ```JSON
  // Modified Properties
  Name : manager
  New Value : "83f0156c-3222-407e-939c-56677831d525" // objectGuid of the user 21023

  // Additional Details
  ErrorCode : None // Use the error code captured here to troubleshoot AD account creation issues
  EventName : EntryExportUpdate // Implies that object will be created
  JoiningProperty : 21451 // Value of the Workday attribute that serves as the Matching ID
  SourceAnchor : 9603bf594b9901693f307815bf21870a // WorkdayID of the user
  TargetAnchor : 43b668e7-1d73-401c-a00a-fed14d31a1a8 // objectGuid of the user 21451

  ```

### <a name="resolving-commonly-encountered-errors"></a>Řešení běžně se vyskytujících chyb

Tato část popisuje běžně vidět chyby s workday uživatele zřizování a jak ji vyřešit. Chyby jsou seskupeny takto:

* [Chyby agenta zřizování](#provisioning-agent-errors)
* [Chyby připojení](#connectivity-errors)
* [Chyby při vytváření uživatelského účtu ad](#ad-user-account-creation-errors)
* [Chyby aktualizace uživatelského účtu služby AD](#ad-user-account-update-errors)

#### <a name="provisioning-agent-errors"></a>Chyby agenta zřizování

|#|Chybový scénář |Pravděpodobné příčiny|Doporučené rozlišení|
|--|---|---|---|
|1.| Při instalaci zřizovacího agenta s chybovou zprávou: *Službu Agent zřizování připojení Microsoft Azure AD (AADConnectProvisioningAgent) se nepodařilo spustit. Ověřte, zda máte dostatečná oprávnění ke spuštění systému.* | Tato chyba se obvykle zobrazí, pokud se pokoušíte nainstalovat agenta zřizování na řadič domény a zásady skupiny brání spuštění služby.  Je také vidět, pokud máte předchozí verzi agenta spuštěna a jste ji odinstalovali před zahájením nové instalace.| Nainstalujte zřizovacího agenta na server, který není dc. Před instalací nového agenta se ujistěte, že jsou odinstalovány předchozí verze agenta.|
|2.| Služba Windows "Microsoft Azure AD Connect Provisioning Agent" je ve stavu *Spuštění* a nepřepíná do stavu *Spuštění.* | V rámci instalace vytvoří průvodce agentem na serveru místní účet (**NT Service\\AADConnectProvisioningAgent),** který se používá pro spuštění služby. Pokud zásady zabezpečení na serveru systému Windows brání místním účtům ve spuštění služeb, dojde k této chybě. | Otevřete *konzolu Služby*. Klikněte pravým tlačítkem myši na službu Windows "Microsoft Azure AD Connect Provisioning Agent" a na kartě přihlášení zadejte účet správce domény pro spuštění služby. Restartujte službu. |
|3.| Při konfiguraci zřizovacího agenta s doménou služby AD v kroku *Připojit službu Active Directory*trvá průvodce dlouhou dobu při pokusu o načtení schématu služby AD a nakonec časový modus out. | K této chybě obvykle dojde v případě, že se průvodce kvůli problémům s bránou firewall nemůže spojit se serverem řadiče domény AD. | Na obrazovce *Průvodce připojením služby Active Directory* je při poskytování pověření pro doménu služby AD k dispozici možnost nazvaná Vybrat *prioritu řadiče domény*. Tuto možnost použijte k výběru řadiče domény, který je ve stejné síti jako server agenta, a ujistěte se, že komunikaci neblokují žádná pravidla brány firewall. |

#### <a name="connectivity-errors"></a>Chyby připojení

Pokud se zřizovací služba nemůže připojit k Workday nebo Active Directory, může způsobit, že zřizování přejde do stavu v karanténě. V následující tabulce můžete řešit problémy s připojením.

|#|Chybový scénář |Pravděpodobné příčiny|Doporučené rozlišení|
|--|---|---|---|
|1.| Po kliknutí na **možnost Testovat připojení**se zobrazí chybová zpráva: Při *připojování ke službě Active Directory došlo k chybě. Zkontrolujte, zda je spuštěn místní zprostředkovatel zřizování a zda je nakonfigurován se správnou doménou služby Active Directory.* | Tato chyba se obvykle zobrazí, pokud agent zřizování není spuštěnnebo je brána firewall blokuje komunikaci mezi Azure AD a zřizovacíagent. Tato chyba se může zobrazit také v případě, že doména není v Průvodci agentem nakonfigurována. | Otevřete konzolu *Služby* na serveru windows a ověřte, zda je agent spuštěn. Otevřete průvodce zřizovacím agentem a ověřte, zda je u agenta zaregistrována správná doména.  |
|2.| Úloha zřizování přejde do stavu karantény během víkendů (Pá-Sat) a my dostaneme e-mailové oznámení, že došlo k chybě se synchronizací. | Jednou z běžných příčin této chyby je plánovaná údržba aplikace Workday. Pokud používáte tenanta implementace Workday, mějte na paměti, že Workday má naplánované výpadky tenantů implementace na víkendy (obvykle od pátečního večera do sobotního rána) a během této doby můžou aplikace zřizování Workday přejít do stavu karantény, protože se k Workday nemůžou připojit. Jakmile bude tenant implementace Workday opět online, vrátí se zpět do normálního stavu. Ve výjimečných případech k této chybě může dojít také v případě, že se kvůli aktualizaci tenanta změní heslo uživatele systému integrace nebo pokud dojde k uzamčení nebo vypršení platnosti účtu. | Zjistěte od svého správce Workday nebo partnera pro integraci období plánovaných výpadků Workday, abyste během výpadků mohli ignorovat upozornění a zkontrolovat dostupnost, jakmile bude instance Workday opět online.  |


#### <a name="ad-user-account-creation-errors"></a>Chyby při vytváření uživatelského účtu ad

|#|Chybový scénář |Pravděpodobné příčiny|Doporučené rozlišení|
|--|---|---|---|
|1.| Selhání operace exportu v protokolu auditu se zprávou *Chyba: OperationsError-SvcErr: Došlo k chybě operace. Pro adresářovou službu nebyl nakonfigurován žádný nadřazený odkaz. Adresářová služba proto nemůže vydávat odkazy na objekty mimo tuto doménovou strukturu.* | Tato chyba se obvykle zobrazí, pokud není správně nastavena neměnná *aktivita kontejneru služby Active Directory* nebo pokud se jedná o problémy s mapováním výrazů používaným pro *parentDistinguishedName*. | Překlepy zkontrolujte parametr OU *kontejneru služby Active Directory.* Pokud v mapování atributů používáte *parentDistinguishedName*, ujistěte se, že se vždy vyhodnotí jako známý kontejner v rámci domény AD. Zkontrolujte událost *Export* v protokolech auditu, abyste viděli vygenerovanou hodnotu. |
|2.| Selhání operace exportu v protokolu auditu s kódem *chyby: SystemForCrossDomainIdentityManagementBadResponse* a zpráva *Chyba: ConstraintViolation-AtrErr: Hodnota v požadavku je neplatná. Hodnota atributu nebyla v přijatelném rozsahu hodnot. \nPodrobnosti o chybě: CONSTRAINT_ATT_TYPE - společnost*. | Zatímco tato chyba je specifická pro atribut *společnosti,* může se zobrazit tato chyba pro jiné atributy, jako je *CN* také. Tato chyba se zobrazí z důvodu omezení schématu vynucené ad. Ve výchozím nastavení mají atributy jako *společnost* a *cn* ve službě AD horní limit 64 znaků. Pokud je hodnota přicházející z Workday více než 64 znaků, zobrazí se tato chybová zpráva. | Zkontrolujte událost *Export* v protokolech auditu, abyste viděli hodnotu atributu uvedeného v chybové zprávě. Zvažte zkrácení hodnoty přicházející z Workday pomocí [mid](../app-provisioning/functions-for-customizing-application-data.md#mid) funkce nebo změna mapování na atribut Služby AD, který nemá podobné omezení délky.  |

#### <a name="ad-user-account-update-errors"></a>Chyby aktualizace uživatelského účtu služby AD

Během procesu aktualizace uživatelského účtu služby AD služba zřizování čte informace z Workday i AD, spustí pravidla mapování atributů a určí, zda se jakákoli změna musí projevit. V souladu s tím je spuštěna událost aktualizace. Pokud některý z těchto kroků dojde k selhání, je zaznamenána v protokolech auditu. V následující tabulce můžete řešit běžné chyby aktualizací.

|#|Chybový scénář |Pravděpodobné příčiny|Doporučené rozlišení|
|--|---|---|---|
|1.| Selhání akce pravidla synchronizace v protokolu auditu se zprávou *EventName = EntrySynchronizationError a ErrorCode = EndpointUnavailable*. | Tato chyba se zobrazí, pokud služba zřizování není schopna načíst data profilu uživatele ze služby Active Directory z důvodu chyby zpracování, které došlo u místního agenta zřizování. | Zkontrolujte protokoly Prohlížeče událostí zřizovacího agenta, zda nezaznamenávají chybové události, které označují problémy s operací čtení (Filtr podle ID události #2). |
|2.| Atribut správce ve službě AD se pro určité uživatele služby AD neaktualizuje. | Nejpravděpodobnější příčinou této chyby je, pokud používáte pravidla oboru a správce uživatele není součástí oboru. Tento problém můžete spustit také v případě, že odpovídající atribut ID manažera (např. id zaměstnance) není nalezen v cílové doméně služby AD nebo není nastaven na správnou hodnotu. | Zkontrolujte filtr oboru a přidejte uživatele správce do oboru. Zkontrolujte profil manažera ve službách AD a ujistěte se, že existuje hodnota odpovídajícího atributu ID. |

## <a name="managing-your-configuration"></a>Správa konfigurace

Tato část popisuje, jak můžete dále rozšířit, přizpůsobit a spravovat konfiguraci zřizování uživatelů řízených pracovníden. Zahrnuje následující témata:

* [Přizpůsobení seznamu atributů uživatele Workday](#customizing-the-list-of-workday-user-attributes)  
* [Export a import vaší konfigurace](#exporting-and-importing-your-configuration)

### <a name="customizing-the-list-of-workday-user-attributes"></a>Přizpůsobení seznamu atributů uživatele Workday

Aplikace pro zřizování pracovního dne pro službu Active Directory a Azure AD obsahují výchozí seznam atributů uživatele Workday, ze kterých si můžete vybrat. Tyto seznamy však nejsou úplné. Workday podporuje mnoho stovek možných uživatelských atributů, které mohou být standardní nebo jedinečné pro vašeho tenanta Workday.

Služba zřizování Azure AD podporuje možnost přizpůsobit seznam nebo atribut Workday tak, aby zahrnovala všechny atributy vystavené v [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) operace rozhraní API lidských zdrojů.

Chcete-li provést tuto změnu, musíte použít [Workday Studio](https://community.workday.com/studio-download) extrahovat xpath výrazy, které představují atributy, které chcete použít a pak je přidat do konfigurace zřizování pomocí editoru rozšířených atributů na webu Azure Portal.

**Chcete-li načíst výraz XPath pro atribut uživatele Workday:**

1. Stáhněte a nainstalujte [Workday Studio](https://community.workday.com/studio-download). Pro přístup k instalačnímu programu budete potřebovat komunitní účet Workday.

2. Stáhněte si soubor Workday Human_Resources WSDL z této adresy URL:https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Human_Resources.wsdl

3. Spusťte Workday Studio.

4. Na panelu příkazů vyberte možnost **Pracovní den > Testovací webová služba v testeru.**

5. Vyberte **Externí**a vyberte Human_Resources souborWSDL, který jste stáhli v kroku 2.

    ![Pracovní studio](./media/workday-inbound-tutorial/wdstudio1.png)

6. Nastavte **Location** pole Umístění `https://IMPL-CC.workday.com/ccx/service/TENANT/Human_Resources`na , ale nahradí "IMPL-CC" s vaším skutečným typem instance a "TENANT" s vaším skutečným názvem klienta.

7. Nastavit **operaci** na **Get_Workers**

8.  Kliknutím na malý **odkaz konfigurace** pod podokny Požadavek/odpověď nastavte pověření pracovního dne. Zaškrtněte **políčko Ověřování**a zadejte uživatelské jméno a heslo pro účet integračního systému Workday. Nezapomeňte naformátovat uživatelské jméno\@jako klienta jména a ponechat vybranou možnost **WS-Security UsernameToken.**

    ![Pracovní studio](./media/workday-inbound-tutorial/wdstudio2.png)

9. Vyberte **OK**.

10. V podokně **Požadavek** vložte do níže uvedeného xml a nastavte **Employee_ID** na ID zaměstnance skutečného uživatele ve vašem tenantovi Workday. Vyberte uživatele, který má naplnění atributu, který chcete extrahovat.

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <env:Envelope xmlns:env="http://schemas.xmlsoap.org/soap/envelope/" xmlns:xsd="https://www.w3.org/2001/XMLSchema">
      <env:Body>
        <wd:Get_Workers_Request xmlns:wd="urn:com.workday/bsvc" wd:version="v21.1">
          <wd:Request_References wd:Skip_Non_Existing_Instances="true">
            <wd:Worker_Reference>
              <wd:ID wd:type="Employee_ID">21008</wd:ID>
            </wd:Worker_Reference>
          </wd:Request_References>
          <wd:Response_Group>
            <wd:Include_Reference>true</wd:Include_Reference>
            <wd:Include_Personal_Information>true</wd:Include_Personal_Information>
            <wd:Include_Employment_Information>true</wd:Include_Employment_Information>
            <wd:Include_Management_Chain_Data>true</wd:Include_Management_Chain_Data>
            <wd:Include_Organizations>true</wd:Include_Organizations>
            <wd:Include_Reference>true</wd:Include_Reference>
            <wd:Include_Transaction_Log_Data>true</wd:Include_Transaction_Log_Data>
            <wd:Include_Photo>true</wd:Include_Photo>
            <wd:Include_User_Account>true</wd:Include_User_Account>
          <wd:Include_Roles>true</wd:Include_Roles>
          </wd:Response_Group>
        </wd:Get_Workers_Request>
      </env:Body>
    </env:Envelope>
    ```

11. Příkaz spusťte klepnutím na **příkaz Odeslat požadavek** (zelená šipka). V případě úspěchu by se odpověď měla zobrazit v podokně **odpověď.** Zkontrolujte odpověď, abyste se ujistili, že obsahuje data id uživatele, které jste zadali, a nikoli chybu.

12. Pokud je úspěšná, zkopírujte XML z podokna **Odpověď** a uložte jej jako soubor XML.

13. Na panelu příkazů workday studia vyberte **Soubor > otevřít soubor...** a otevřete uložený soubor XML. Tato akce otevře soubor v editoru XML workday studio.

    ![Pracovní studio](./media/workday-inbound-tutorial/wdstudio3.png)

14. Ve stromu souborů projděte **/env: Obálka > env: Body > wd:Get_Workers_Response > wd:Response_Data > wd: Worker** najít data uživatele.

15. V **části wd: Worker**najděte atribut, který chcete přidat, a vyberte ho.

16. Zkopírujte výraz XPath pro vybraný atribut z pole **Cesta dokumentu.**

17. Odeberte předponu **/env:Envelope/env:Body/wd:Get_Workers_Response/wd:Response_Data/** z kopírovaného výrazu.

18. Pokud je poslední položkou v kopírovaném výrazu uzel (příklad: "/wd: Birth_Date"), pak přidejte **/text()** na konec výrazu. To není nutné, pokud poslední položka je/@wd: atribut (příklad: " typ").

19. Výsledek by měl `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`být něco jako . Tato hodnota je to, co zkopírujete na portál Azure.

**Přidání vlastního uživatelského atributu Workday do konfigurace zřizování:**

1. Spusťte [portál Azure](https://portal.azure.com)a přejděte do části Zřizování zřizovací aplikace Workday, jak je popsáno výše v tomto kurzu.

2. Nastavte **stav zřizování** na **Vypnuto**a vyberte **Uložit**. Tento krok pomůže zajistit, že se změny projeví až po připravenosti.

3. V části Mapování vyberte **synchronizovat pracovníky pracovního dne do místního služby Active Directory** (nebo **synchronizovat pracovníky pracovního dne do služby Azure AD).** **Mappings**

4. Přejděte do dolní části další obrazovky a vyberte **Zobrazit upřesňující možnosti**.

5. Vyberte **Upravit seznam atributů pro pracovní den**.

    ![Pracovní studio](./media/workday-inbound-tutorial/wdstudio_aad1.png)

6. Přejděte na konec seznamu atributů na místo, kde jsou vstupní pole.

7. Do **pole Název**zadejte zobrazovaný název atributu.

8. V **části Typ**vyberte typ, který odpovídajícím způsobem odpovídá vašemu atributu ( Nejběžnější je**řetězec).**

9. Do **výrazu rozhraní API**zadejte výraz XPath, který jste zkopírovali ze služby Workday Studio. Příklad: `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`

10. Vyberte **Přidat atribut**.

    ![Pracovní studio](./media/workday-inbound-tutorial/wdstudio_aad2.png)

11. Vyberte **Uložit** výše a pak **Ano** do dialogového okna. Pokud je obrazovka Mapování atributů stále otevřená, zavřete ji.

12. Zpět na hlavní kartě **Zřizování** vyberte znovu **synchronizovat pracovníky pracovního dne do místního active directory** (nebo **synchronizovat pracovníky do Azure AD).**

13. Vyberte **Přidat nové mapování**.

14. Nový atribut by se nyní měl zobrazit v seznamu **atributů Zdroj.**

15. Podle potřeby přidejte mapování pro nový atribut.

16. Po dokončení nezapomeňte nastavit **stav zřizování** zpět **na Zapnuto** a uložit.

### <a name="exporting-and-importing-your-configuration"></a>Export a import vaší konfigurace

Viz článek [Export a import konfigurace zřizování](../app-provisioning/export-import-provisioning-configuration.md)

## <a name="managing-personal-data"></a>Správa osobních údajů

Řešení zřizování workday pro službu Active Directory vyžaduje instalaci zřizovacího agenta na místní server windows a tento agent vytvoří protokoly v protokolu událostí systému Windows, které mohou obsahovat osobní údaje v závislosti na atributu Workday to AD Mapování. Chcete-li splnit povinnosti ochrany osobních údajů uživatelů, můžete zajistit, že žádná data budou zachována v protokolech událostí po 48 hodinách nastavením naplánované úlohy systému Windows pro vymazání protokolu událostí.

Zřizování Azure AD spadá do kategorie **zpracování dat** klasifikace GDPR. Jako kanál zpracovatele dat poskytuje služba služby zpracování dat klíčovým partnerům a koncovým spotřebitelům. Služba zřizování Azure AD negeneruje uživatelská data a nemá žádnou nezávislou kontrolu nad tím, jaká osobní data se shromažďují a jak se používají. Načítání dat, agregace, analýza a vytváření sestav ve službě zřizování Azure AD jsou založeny na existujících podnikových datech.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-hybrid-note.md)]

Pokud jde o uchovávání dat, služba zřizování Azure AD negeneruje sestavy, neprovádí analýzy ani neposkytuje přehledy po 30 dnech. Proto služba zřizování Azure AD neukládá, nezpracovává ani neuchovává žádná data po dobu 30 dnů. Tento návrh je v souladu s předpisy GDPR, předpisy pro dodržování předpisů microsoftu o ochraně osobních údajů a zásadami uchovávání dat azure ad.

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy o aktivitě zřizování.](../app-provisioning/check-status-user-account-provisioning.md)
* [Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Workday a Azure Active Directory](workday-tutorial.md)
* [Zjistěte, jak integrovat další aplikace SaaS s Azure Active Directory](tutorial-list.md)
* [Zjistěte, jak pomocí rozhraní API Microsoft Graphu spravovat konfigurace zřizování](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)
