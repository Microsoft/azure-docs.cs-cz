---
title: 'Kurz: Konfigurace Workday pro automatické zřizování uživatelů pomocí Azure Active Directory | Dokumentace Microsoftu'
description: Zjistěte, jak konfigurovat Azure Active Directory a automaticky zřizovat a rušit zřízení uživatelských účtů do Workday.
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
ms.date: 01/19/2019
ms.author: chmutali
ms.openlocfilehash: d4a47130dadb782f41579bd20c4b5e1d1e9978bb
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55188588"
---
# <a name="tutorial-configure-workday-for-automatic-user-provisioning"></a>Kurz: Konfigurace Workday pro automatické zřizování uživatelů

Cílem tohoto kurzu je zobrazit kroky potřebné k provedení importovat pracovní profily z Workday do Active Directory a Azure Active Directory, se volitelné zpětným zápisem e-mailové adresy k Workday.

## <a name="overview"></a>Přehled

[Služba zřizování uživatelů Azure Active Directory](../manage-apps/user-provisioning.md) integruje [Workday lidských zdrojů API](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) pro zřízení uživatelských účtů. Toto připojení používá Azure AD povolit následující uživatel pracovní postupy zřizování:

* **Zřizování uživatelů do služby Active Directory** – zřízení vybrané nastaví uživatelů z Workday do jedné nebo několika domén služby Active Directory.

* **Zřizování uživatelů jenom cloudu do služby Azure Active Directory** – v situacích, kde místní služby Active Directory se nepoužívá, uživatelům je možné zřídit přímo z Workday do Azure Active Directory pomocí služba zřizování uživatelů Azure AD.

* **Zápis zadní e-mailových adres do Workday** – služba zřizování uživatelů Azure AD zapisovat e-mailové adresy uživatelů Azure AD zpátky do Workday.

### <a name="what-human-resources-scenarios-does-it-cover"></a>Jaké scénáře lidských zdrojů se tento zákon vztahuje?

Workday uživatele pracovní postupy zřizování podporuje služby zřizování uživatelů Azure AD povolit automatizaci následující lidské zdroje a scénářů správy životního cyklu identit:

* **Náboru nových zaměstnanců** – při přidání nového zaměstnance do Workday, uživatelský účet se automaticky vytvoří v Active Directory, Azure Active Directory a volitelně Office 365 a [dalším aplikacím SaaS podporované službou Azure AD](../manage-apps/user-provisioning.md), se zpětným zápisem e-mailovou adresu do Workday.

* **Aktualizace atributu a profil zaměstnance** – při aktualizaci záznamu zaměstnance ve Workday (například jeho název, název nebo správce), jeho uživatelský účet se automaticky aktualizují v Active Directory, Azure Active Directory a volitelně Office 365 a [dalším aplikacím SaaS podporované službou Azure AD](../manage-apps/user-provisioning.md).

* **Zaměstnance ukončení** – když se ukončí zaměstnance ve Workday, automaticky se vypne jeho uživatelský účet v Active Directory, Azure Active Directory a volitelně Office 365 a [dalším aplikacím SaaS nepodporuje v Azure AD](../manage-apps/user-provisioning.md).

* **Zaměstnanec rehires** – když je rehired zaměstnance ve Workday, jejich starý účet můžete automaticky znovu aktivována nebo znovu zřídit (v závislosti na vašich předvoleb) do služby Active Directory, Azure Active Directory a volitelně Office 365 a [dalším aplikacím SaaS podporované službou Azure AD](../manage-apps/user-provisioning.md).

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Kdo je tento uživatel zřizování řešení, které nejlépe vyhovuje?

Tento uživatel Workday zřizování řešení je v současné době ve verzi public preview a je ideální pro:

* Organizace, které chtějí předem připravené, cloudové řešení pro zřizování Workday uživatelů

* Organizace, které vyžadují přímé uživatele zřizování z Workday do Active Directory nebo Azure Active Directory

* Organizace, které vyžadují, aby uživatelé, které se mají zřídit pomocí dat získaných z modulu Workday HCM (viz [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html))

* Organizace, které vyžadují propojení, přesunutí, a opuštění uživatelům synchronizovat na jeden nebo více lokalita služby Active Directory, domény a organizační jednotky pouze na základě změnit informace o uživateli v modulu Workday HCM (viz [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html))

* Organizace, které používají pro e-mailu Office 365

## <a name="solution-architecture"></a>Architektura řešení

Tato část popisuje zřizování architekturu řešení pro běžné hybridní prostředí uživatelů začátku do konce. Existují dvě související postupy:

* **Tok autoritativní HR dat z Workday do Active Directory on-premises:** V tomto toku pracovního procesu (jako je například nový Hires přenosy, ukončení) nejprve dojde k událostem v cloudu Workday HR tenanta a potom událost data budou téci do místní služby Active Directory prostřednictvím služby Azure AD a zřizování agenta. V závislosti na události to může vést k vytvoření/aktualizaci/povolení/zakázání operace ve službě AD.
* **Tok e-mailu zpětný zápis z místní služby Active Directory do Workday:** Po vytvoření účtu se ve službě Active Directory, je synchronizovaný s Azure AD pomocí služby Azure AD Connect a atribut e-mailové zdrojem je služba Active Directory je možné zpětně zapsat do Workday.

![Přehled](./media/workday-inbound-tutorial/wd_overview.png)

### <a name="end-to-end-user-data-flow"></a>Tok dat uživatele začátku do konce

1. Tým HR vede Workday HCM transakce pracovního procesu (potencionálního/stěhovací společnost/odešlé jednotky nebo nové zaměstnance/přenosy nebo ukončení)
2. Služba zřizování Azure AD spouští plánované synchronizace identit z Workday HR a identifikuje změny, které je potřeba zpracovat pro synchronizaci s místním Active Directory.
3. Služba zřizování Azure AD s místními AAD Connect zřizování agenta vyvolá s datová část požadavku, který obsahuje operace vytvoření/aktualizaci/povolení/zakázání účtu AD.
4. Agent Azure AD Connect zřizování účtu služby používá k přidání/aktualizaci dat účtu AD.
5. Azure AD Connect / AD synchronizační modul spustí rozdílová synchronizace do o přijetí změn aktualizace ve službě AD.
6. Aktualizace služby Active Directory synchronizované s Azure Active Directory.
7. Pokud je nakonfigurovaný konektor pro Workday zpětný zápis, je zpětný zápis atribut e-mailové do Workday, podle odpovídající atribut používá.

## <a name="planning-your-deployment"></a>Plánování nasazení

Před zahájením integraci Workday, zkontrolujte následující požadavky a přečtěte si následující pokyny týkající se tak, aby odpovídaly vaší aktuální architektura služby Active Directory a zřizování uživatelů požadavky pomocí řešení poskytuje služba Azure Active Directory. Komplexní [plánu nasazení](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-plans) s listy plánování je k dispozici také pomoci při spolupráci s partnerem integraci Workday a HR zúčastněné strany.

Tato část zahrnuje následující aspekty plánování:

* [Požadavky](#prerequisites)
* [Výběr zřizování aplikací konektor k nasazení](#selecting-provisioning-connector-apps-to-deploy)
* [Plánování nasazení agenta Azure AD Connect zřizování](#planning-deployment-of-azure-ad-connect-provisioning-agent)
* [Integrace s více domén služby Active Directory](#integrating-with-multiple-active-directory-domains)
* [Plánování Workday mapování atributů služby Active Directory uživatele a transformace](#planning-workday-to-active-directory-user-attribute-mapping-and-transformations)

### <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu se předpokládá, že máte následující položky:

* Platný Azure AD Premium P1 nebo vyšší předplatné s přístupem globální správce
* Implementace klienta Workday pro účely testování a integrace
* Oprávnění správce ve Workday a vytvořte uživatele systému integrace, provést změny k testování daty o zaměstnancích pro účely testování
* Pro zřizování uživatelů služby Active Directory, je potřeba hostitelský server s Windows serverem 2012 nebo vyšší s .NET 4.7.1+ runtime [místní agentem zřizování](https://go.microsoft.com/fwlink/?linkid=847801)
* [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) za synchronizaci uživatelů mezi službami Active Directory a Azure AD

### <a name="selecting-provisioning-connector-apps-to-deploy"></a>Výběr zřizování aplikací konektor k nasazení

Pro usnadnění pracovní postupy zřizování Workday od služby Active Directory, Azure AD poskytuje více zřizování aplikací konektoru, které lze přidat z Galerie aplikací Azure AD:

![Galerie aplikací AAD](./media/workday-inbound-tutorial/wd_gallery.png)

* **WORKDAY do Active Directory uživatele zřizování** – tato aplikace usnadňuje účet zřizování uživatelů z Workday na jedinou doménu služby Active Directory. Pokud máte více domén, můžete přidat jednu instanci této aplikace v galerii aplikací Azure AD pro každou doménu služby Active Directory je potřeba zřídit.

* **Pro uživatele Azure AD zřizování WORKDAY** – AAD Connect je nástroj, který se má použít k synchronizaci uživatelů do Azure Active Directory, tato aplikace je možné pro usnadnění zřizování výhradně cloudový uživatelů z Workday do jednoho Azure Active Directory Tenanta Active Directory.

* **Zpětný zápis WORKDAY** – tato aplikace umožňuje zpětný zápis e-mailové adresy uživatele ze služby Azure Active Directory do Workday.

> [!TIP]
> Pravidelné "Workday" aplikace se používá pro nastavení jednotné přihlašování mezi Workday a Azure Active Directory.

Vývojový diagram rozhodování níže slouží k identifikaci, které aplikace zřizování Workday jsou relevantní pro váš scénář.
    ![Vývojový diagram rozhodnutí](./media/workday-inbound-tutorial/wday_app_flowchart.png "rozhodnutí vývojového diagramu")

Přejděte do příslušné části v tomto kurzu pomocí obsahu.

### <a name="planning-deployment-of-azure-ad-connect-provisioning-agent"></a>Plánování nasazení agenta Azure AD Connect zřizování

> [!NOTE]
> Tato část platí jenom v případě, že plánujete nasazení Workday do Active Directory uživatele zřizování aplikace. To můžete přeskočit, pokud nasazujete zpětný zápis Workday nebo Workday uživatele Azure AD App zřizování.

Workday k zřizování uživatelů AD řešení vyžaduje nasazení jeden nebo více zřizování agentů na serverech se systémem Windows 2012 R2 nebo novější s minimálně 4 GB paměti RAM a .NET 4.7.1+ modulu runtime. Před instalací agenta zřizování musí vzít v úvahu následující aspekty:

* Zkontrolujte, zda serveru hostitele se spuštěným agentem zřizování má přístup k síti do cílové AD domény
* Průvodce konfigurací agenta zřizování agenta zaregistruje v nástroji vašeho tenanta Azure AD a proces registrace vyžaduje přístup k *. msappproxy.net přes SSL port 443. Ujistěte se, že odchozí pravidla brány firewall jsou na místě, aby tato komunikace. Podporuje agenta [konfigurace odchozího proxy HTTPS](#how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication).
* Zřizování Agent používá ke komunikaci s místní účet služby AD domény. Před instalací agenta doporučujeme vytvořit účet služby s oprávněními správce domény a hesla, nemá prošlou platnost.  
* Během konfigurace zřizování agenta můžete vybrat, řadiče domény, které by měl zpracovat žádosti o zřízení. Pokud máte několik řadičů domény geograficky distribuované, nainstalujte agenta zřizování ve stejné lokalitě jako vašich řadičů domény upřednostňované ke zlepšení spolehlivosti a výkonu – ucelené řešení
* Pro zajištění vysoké dostupnosti můžete nasadit více než jednoho agenta pro zřizování a zaregistrujte ho ke zpracování stejné sady místních domén AD.

> [!IMPORTANT]
> V produkčním prostředí společnost Microsoft doporučuje, abyste měli aspoň 3 zřizování agentů nakonfigurovaných s vašeho tenanta Azure AD pro zajištění vysoké dostupnosti.

### <a name="integrating-with-multiple-active-directory-domains"></a>Integrace s více domén služby Active Directory

> [!NOTE]
> Tato část platí jenom v případě, že plánujete nasazení Workday do Active Directory uživatele zřizování aplikace. To můžete přeskočit, pokud nasazujete zpětný zápis Workday nebo Workday uživatele Azure AD App zřizování.

V závislosti na topologii služby Active Directory je potřeba rozhodnout, počet aplikací konektoru zřizování uživatelů a počet zřizování agentů ke konfiguraci. Tady jsou některé běžné vzory nasazení, které můžete použít při plánování nasazení.

#### <a name="deployment-scenario-1--single-workday-tenant---single-ad-domain"></a>Scénář nasazení #1: Jednoho Tenanta Workday -> jeden AD domény

V tomto scénáři máte jednoho tenanta Workday a chcete zřídit uživatele ve službě jediný cíl domény AD. Tady je konfigurace doporučené produkčního prostředí pro toto nasazení.

|   |   |
| - | - |
| Ne. zřizování agentů k nasazení na místě | 3 (pro zajištění vysoké dostupnosti a převzetí služeb při selhání) |
| Ne. z Workday do AD uživatele zřizování aplikací ke konfiguraci na webu Azure portal | 1 |

  ![Scénář 1](./media/workday-inbound-tutorial/dep_scenario1.png)

#### <a name="deployment-scenario-2--single-workday-tenant---multiple-child-ad-domains"></a>Scénář nasazení #2: Jednoho Tenanta Workday -> více podřízené AD domény

Tento scénář zahrnuje zřizování uživatelů z Workday do více cílové AD podřízené domény v doménové struktuře. Tady je konfigurace doporučené produkčního prostředí pro toto nasazení.

|   |   |
| - | - |
| Ne. zřizování agentů k nasazení na místě | 3 (pro zajištění vysoké dostupnosti a převzetí služeb při selhání) |
| Ne. z Workday do AD uživatele zřizování aplikací ke konfiguraci na webu Azure portal | jedna aplikace za podřízené domény |

  ![Scénář 2](./media/workday-inbound-tutorial/dep_scenario2.png)

#### <a name="deployment-scenario-3--single-workday-tenant---disjoint-ad-forests"></a>Scénář nasazení #3: Jednoho Tenanta Workday -> doménových struktur AD nesouvislý

Tento scénář zahrnuje zřizování uživatelů z Workday do domén v nesouvislé doménových struktur AD. Tady je konfigurace doporučené produkčního prostředí pro toto nasazení.

|   |   |
| - | - |
| Ne. zřizování agentů k nasazení na místě | 3 za nesouvislý doménové struktuře Active Directory |
| Ne. z Workday do AD uživatele zřizování aplikací ke konfiguraci na webu Azure portal | jedna aplikace za podřízené domény |

  ![Scénář 3](./media/workday-inbound-tutorial/dep_scenario3.png)

### <a name="planning-workday-to-active-directory-user-attribute-mapping-and-transformations"></a>Plánování Workday mapování atributů služby Active Directory uživatele a transformace

> [!NOTE]
> Tato část platí jenom v případě, že plánujete nasazení Workday do Active Directory uživatele zřizování aplikace. To můžete přeskočit, pokud nasazujete zpětný zápis Workday nebo Workday uživatele Azure AD App zřizování.

Před konfigurací zřizování uživatelů k doméně služby Active Directory, zvažte následující otázky. Odpovědi na tyto otázky určí, jak filtry oborů a mapování atributů musí být nastavena.

* **Co uživatelé ve Workday je potřeba zřídit na těchto doménových strukturách služby Active Directory?**

  * *Příklad: Uživatelé, kde Workday "Společnost" atributu obsahuje hodnotu "Contoso", a atribut "Worker_Type" obsahuje "Normální"*

* **Jak se uživatelé směrují do jiné organizační jednotky (OU)?**

  * *Příklad: Uživatelé se směrují do organizační jednotky, které odpovídají umístění sady office, jak je definováno ve Workday "Magistrát" a "Country_Region_Reference" atributy*

* **Jak by měl být následující atributy vyplní ve službě Active Directory?**

  * Běžný název (cn)
    * *Příklad: Použijte hodnotu Workday User_ID úmluvu lidské zdroje*

  * ID zaměstnance (employeeId)
    * *Příklad: Použijte hodnotu Workday Worker_ID*

  * Název účtu SAM (sAMAccountName)
    * *Příklad: Použijte hodnotu Workday User_ID, filtruje prostřednictvím Azure AD zřizování výraz odebrat neplatné znaky.*

  * Hlavní název uživatele (userPrincipalName)
    * *Příklad: Hodnota Workday User_ID pomocí Azure AD zřizování výraz do názvu domény*

* **Jak by uživatelé si měly odpovídat mezi Workday a služby Active Directory?**

  * *Příklad: Uživatelé s konkrétní aplikací Workday "Worker_ID" hodnota jsou porovnány s uživatelů služby Active Directory, kde "employeeID" mají stejnou hodnotu. Pokud není nalezena hodnota Worker_ID ve službě Active Directory, vytvořte nového uživatele.*
  
* **Doménové struktury služby Active Directory už obsahuje ID požadované pro odpovídající logiku pro práci uživatelů?**

  * *Příklad: Pokud toto nastavení se nové nasazení Workday, doporučuje se předvyplní správné hodnoty Workday Worker_ID (nebo jedinečnou hodnotu ID podle výběru) zajistit odpovídající logiky co nejjednodušší služby Active Directory.*

Jak nastavit a nakonfigurovat tyto speciální zřizování aplikací konektoru je předmětem zbývající části tohoto kurzu. Aplikace, které provedete konfiguraci bude záviset na systémy, které je potřeba zřídit a kolik domény služby Active Directory a Azure AD tenantů se ve vašem prostředí.

## <a name="configure-integration-system-user-in-workday"></a>Konfigurace uživatele systému integrace ve Workday

Běžné požadavky všechny Workday konektory zřizování je, že se vyžadují přihlašovací údaje uživatele systému integrace Workday pro připojení k rozhraní API Workday lidských zdrojů. Tato část popisuje, jak vytvořit uživatele systému integrace ve Workday a obsahuje následující oddíly:

* [Vytváří se o uživatele systému integrace](#creating-an-integration-system-user)
* [Vytvoření skupiny zabezpečení služby integrace](#creating-an-integration-security-group)
* [Konfigurace oprávnění zásad zabezpečení domény](#configuring-domain-security-policy-permissions)
* [Konfigurace oprávnění zásad zabezpečení obchodního procesu](#configuring-business-process-security-policy-permissions)
* [Aktivace změny zásad zabezpečení](#activating-security-policy-changes)

> [!NOTE]
> Je možné obejít tento postup a místo toho použijte účet globálního správce Workday jako systémový účet integrace. To může fungovat pro ukázky, ale nedoporučuje se používat pro nasazení v produkčním prostředí.

### <a name="creating-an-integration-system-user"></a>Vytváří se o uživatele systému integrace

**Chcete-li vytvořit uživatele systému integrace:**

1. Přihlaste se pomocí účtu správce tenanta Workday. V **aplikací Workday**, zadejte vytvořit uživatel do vyhledávacího pole a potom klikněte na tlačítko **vytvořit uživatele systému integrace**.

    ![Vytvořit uživatele](./media/workday-inbound-tutorial/wd_isu_01.png "pro vytvoření uživatele")
2. Dokončení **vytvořit uživatele systému integrace** úkol zadáním uživatelského jména a hesla pro nového uživatele systému integrace.  
  
* Nechte **vyžadují nové heslo při dalším přihlašování** možnost není zaškrtnuto, protože tento uživatel bude protokolování prostřednictvím kódu programu.
* Nechte **minut časového limitu relace** s výchozí hodnotou 0, což zabrání relace uživatele vypršení časového limitu předčasně ukončen.
* Vyberte možnost **povolit relace uživatelského rozhraní** poskytuje další úroveň zabezpečení, který brání uživatele s heslem systému integrace protokolování do Workday.

    ![Vytvořte uživatele systému integrace](./media/workday-inbound-tutorial/wd_isu_02.png "vytvořte uživatele systému integrace")

### <a name="creating-an-integration-security-group"></a>Vytvoření skupiny zabezpečení služby integrace

V tomto kroku vytvoříte skupinu zabezpečení systému integrace neomezeným nebo omezeným ve Workday a přiřadit uživatele systému integrace vytvořili v předchozím kroku do této skupiny.

**Vytvoření skupiny zabezpečení:**

1. Zadejte do vyhledávacího pole vytvořte skupinu zabezpečení a potom klikněte na **vytvořit skupinu zabezpečení**.

    ![CreateSecurity Group](./media/workday-inbound-tutorial/wd_isu_03.png "CreateSecurity Group")
2. Dokončení **vytvořit skupinu zabezpečení** úloh. 

  * Existují dva typy skupin zabezpečení ve Workday:
    * **Aktivace:** Všechny členy skupiny zabezpečení přístupné všechny instance data zabezpečená pomocí skupiny zabezpečení.
    * **Omezené:** Všechny členy skupiny zabezpečení měli kontextové podmnožinu dat instancí (řádků), ke kterým přístup do skupiny zabezpečení.
  * Poraďte se vašeho partnera integraci Workday a vybrat typ skupiny zabezpečení pro integraci.
  * Jakmile budete znát typ skupiny, vyberte **skupiny zabezpečení systému integrace (neomezená)** nebo **skupiny zabezpečení systému integrace (neomezená)** z **typ klienty zabezpečení Skupiny** rozevíracího seznamu.

    ![CreateSecurity Group](./media/workday-inbound-tutorial/wd_isu_04.png "CreateSecurity Group")

3. Po úspěšném vytvoření skupiny zabezpečení, zobrazí se stránka, kde můžete přiřadit členy do skupiny zabezpečení. Přidání nového uživatele systému integrace vytvořili v předchozím kroku do této skupiny zabezpečení. Pokud používáte *omezené* skupiny zabezpečení, budete také muset vybrat obor příslušné organizace.

    ![Edit Security Group](./media/workday-inbound-tutorial/wd_isu_05.png "Edit Security Group")

### <a name="configuring-domain-security-policy-permissions"></a>Konfigurace oprávnění zásad zabezpečení domény

V tomto kroku budete "zabezpečení domény" udělit oprávnění zásad pro pracovníka data do skupiny zabezpečení.

**Jak nakonfigurovat oprávnění zásad zabezpečení domény:**

1. Zadejte **konfigurace zabezpečení domény** ve vyhledávacím poli a pak kliknutím na odkaz **sestava konfigurace zabezpečení domény**.  

    ![Zásady zabezpečení domény](./media/workday-inbound-tutorial/wd_isu_06.png "zásady zabezpečení domény")  
2. V **domény** textového pole hledání z následujících domén a přidat je do filtru jeden po druhém.  
   * *Zřizování externího účtu*
   * *Data pracovních procesů: Veřejný pracovní sestavy*
   * *Data osob: Kontaktní informace o práci*
   * *Data pracovních procesů: Všechny pozice*
   * *Data pracovních procesů: Aktuální personální informace*
   * *Data pracovních procesů: Název firmy na pracovní profil*

    ![Zásady zabezpečení domény](./media/workday-inbound-tutorial/wd_isu_07.png "zásady zabezpečení domény")  

    ![Zásady zabezpečení domény](./media/workday-inbound-tutorial/wd_isu_08.png "zásady zabezpečení domény") 

    Klikněte na **OK**.

3. V sestavě, která se zobrazí, vyberte tři tečky (...), která se zobrazí vedle **externí zřizování účtů** a klikněte na možnost nabídky **domény -> Upravit oprávnění zásad zabezpečení**

    ![Zásady zabezpečení domény](./media/workday-inbound-tutorial/wd_isu_09.png "zásady zabezpečení domény")  

4. Na **upravit oprávnění zásad zabezpečení domény** stránky, přejděte dolů do části **oprávnění integrace**. Klikněte na znaménko "+" Přidat skupinu systému integrace do seznamu skupiny zabezpečení s **získat** a **umístit** integrace oprávnění.

    ![Upravit oprávnění](./media/workday-inbound-tutorial/wd_isu_10.png "upravit oprávnění")  

5. Klikněte na znaménko "+" Přidat skupinu systému integrace do seznamu skupiny zabezpečení s **získat** a **umístit** integrace oprávnění.

    ![Upravit oprávnění](./media/workday-inbound-tutorial/wd_isu_11.png "upravit oprávnění")  

6. Opakujte kroky 3 až 5 výše pro všechny zbývající zásady zabezpečení:

   | Operace | Zásady zabezpečení domény |
   | ---------- | ---------- |
   | Operace GET a Put | Data pracovních procesů: Veřejný pracovní sestavy |
   | Operace GET a Put | Data osob: Kontaktní informace o práci |
   | Získat | Data pracovních procesů: Všechny pozice |
   | Získat | Data pracovních procesů: Aktuální personální informace |
   | Získat | Data pracovních procesů: Název firmy na pracovní profil |

### <a name="configuring-business-process-security-policy-permissions"></a>Konfigurace oprávnění zásad zabezpečení obchodního procesu

V tomto kroku budete udělit oprávnění zásad pro pracovníka data do skupiny zabezpečení "zabezpečení obchodních procesů". Tento krok je nutný k nastavení konektoru aplikace zpětný zápis Workday.

**Jak nakonfigurovat oprávnění zásad zabezpečení obchodní proces:**

1. Zadejte **obchodní proces zásad** ve vyhledávacím poli a pak kliknutím na odkaz **upravit zásady zabezpečení obchodní proces** úloh.  

    ![Firemní zásady zabezpečení procesu](./media/workday-inbound-tutorial/wd_isu_12.png "firemní zásady zabezpečení procesu")  

2. V **typ obchodního procesu** textového pole hledání *kontakt* a vyberte **kontakt změnu** obchodních procesů a klikněte na tlačítko **OK**.

    ![Firemní zásady zabezpečení procesu](./media/workday-inbound-tutorial/wd_isu_13.png "firemní zásady zabezpečení procesu")  

3. Na **upravit zásady zabezpečení obchodní proces** stránce, přejděte **udržovat kontaktní údaje (webová služba)** oddílu.

    ![Firemní zásady zabezpečení procesu](./media/workday-inbound-tutorial/wd_isu_14.png "firemní zásady zabezpečení procesu")  

4. Vyberte a přidejte novou skupinu zabezpečení systému integrace do seznamu skupin zabezpečení, které lze iniciovat žádosti webové služby. Klikněte na **provádí**. 

    ![Firemní zásady zabezpečení procesu](./media/workday-inbound-tutorial/wd_isu_15.png "firemní zásady zabezpečení procesu")  

### <a name="activating-security-policy-changes"></a>Aktivace změny zásad zabezpečení

**Aktivace změny zásad zabezpečení:**

1. Zadejte aktivovat do vyhledávacího pole a potom klikněte na odkaz **aktivovat čekající změny zásad zabezpečení**.

    ![Aktivovat](./media/workday-inbound-tutorial/wd_isu_16.png "aktivovat")

1. Začátek úlohy aktivovat čekající změny zásad zabezpečení pro účely auditování zadejte komentář a potom klikněte na **OK**.
1. Dokončení úlohy na další obrazovce zaškrtnutím políčka **potvrdit**a potom klikněte na tlačítko **OK**.

    ![Aktivovat čekající zabezpečení](./media/workday-inbound-tutorial/wd_isu_18.png "aktivovat čekající zabezpečení")  

## <a name="configuring-user-provisioning-from-workday-to-active-directory"></a>Konfigurace zřizování uživatelů z Workday do Active Directory

Tato část obsahuje postup pro účet zřizování uživatelů z Workday pro každou doménu služby Active Directory v rámci oboru integraci vašich.

* [Instalace a konfigurace místní zřizování agentů](#part-1-install-and-configure-on-premises-provisioning-agents)
* [Přidání zřizování konektoru aplikace a vytvoření připojení k Workday](#part-2-adding-the-provisioning-connector-app-and-creating-the-connection-to-workday)
* [Nakonfigurujte mapování atributů](#part-3-configure-attribute-mappings)
* [Povolení a spuštění zřizování uživatelů](#enable-and-launch-user-provisioning)

### <a name="part-1-install-and-configure-on-premises-provisioning-agents"></a>Část 1: Instalace a konfigurace místní zřizování agentů

Ke zřízení do místní služby Active Directory, musí se nainstaluje agent na serveru, který má rozhraní .NET Framework 4.7.1+ a síť, přístup k požadované domén služby Active Directory.

> [!TIP]
> Verze rozhraní .NET framework můžete zkontrolovat na vašem serveru pomocí pokynů uvedených [tady](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed).
> Pokud server nemá .NET 4.7.1 nebo vyšší, můžete ho stáhnout z [tady](https://support.microsoft.com/help/4033342/the-net-framework-4-7-1-offline-installer-for-windows).  

Po nasazení .NET 4.7.1+ si můžete stáhnout **[zde agentem zřizování v místním](https://go.microsoft.com/fwlink/?linkid=847801)** a postupujte podle kroků uvedených níže a dokončete tak konfiguraci agenta.

1. Připojte se k Windows serveru, kam chcete nainstalovat nového agenta.
2. Spusťte instalační program agenta zřizování, souhlas s podmínkami a kliknutím na **nainstalovat** tlačítko.

   ![Nainstalujte obrazovky](./media/workday-inbound-tutorial/pa_install_screen_1.png "nainstalovat obrazovky")
3. Po dokončení instalace, průvodce se spustí a zobrazí se **Azure AD Connect** obrazovky. Klikněte na **ověřit** tlačítko pro připojení k vaší instanci Azure AD.

   ![Azure AD Connect](./media/workday-inbound-tutorial/pa_install_screen_2.png "připojení Azure AD")
1. Ověření k vaší instanci Azure AD pomocí přihlašovacích údajů globálního správce.

   ![Správce ověřování](./media/workday-inbound-tutorial/pa_install_screen_3.png "Správce ověřování")

> [!NOTE]
> Přihlašovací údaje správce Azure AD slouží jenom k propojení vašeho tenanta Azure AD. Agent neukládá přihlašovací údaje místně na serveru.

1. Po úspěšném ověření přes Azure AD, se zobrazí **připojení služby Active Directory** obrazovky. V tomto kroku, zadejte název domény AD a klikněte na **přidat adresář** tlačítko.

   ![Přidejte adresář](./media/workday-inbound-tutorial/pa_install_screen_4.png "přidat adresář")
  
1. Můžete teď výzva k zadání přihlašovacích údajů potřebných pro připojení k doméně AD. Na stejné obrazovce, můžete použít **vyberte prioritu řadič domény** k určení řadiče domény, které agent by měly používat pro odesílání žádosti o zřízení.

   ![Domain Credentials](./media/workday-inbound-tutorial/pa_install_screen_5.png)
1. Po dokončení konfigurace domény, instalační program zobrazí seznam nakonfigurované domény. Na této obrazovce můžete opakovat krok #5 a 6 # pro přidání dalších domén nebo klikněte na **Další** přejděte na registraci agenta.

   ![Nakonfigurované domény](./media/workday-inbound-tutorial/pa_install_screen_6.png "nakonfigurované domény")

   > [!NOTE]
   > Pokud máte více domén AD (např. na.contoso.com, emea.contoso.com) a pak přidejte všechny domény jednotlivě do seznamu. Přidání pouze nadřazené domény (např. contoso.com) není dostatečná. Je nutné zaregistrovat každou podřízenou doménu s agentem.
1. Zkontrolujte podrobnosti o konfiguraci a klikněte na **potvrdit** k registraci agenta.
  
   ![Zkontrolujte obrazovky](./media/workday-inbound-tutorial/pa_install_screen_7.png "potvrďte obrazovky")
1. Průvodce konfigurací zobrazí průběh registrace agenta.
  
   ![Registrace agenta](./media/workday-inbound-tutorial/pa_install_screen_8.png "registrace agenta")
1. Po úspěšné registraci agenta můžete kliknout na **ukončit** ukončíte průvodce.
  
   ![Ukončete obrazovky](./media/workday-inbound-tutorial/pa_install_screen_9.png "ukončit obrazovky")
1. Ověření instalace agenta a ujistěte se, že běží tak, že otevřete modul Snap-In "Služby" a vyhledejte službu s názvem "Microsoft Azure AD Connect zřizování agenta"
  
   ![Služby](./media/workday-inbound-tutorial/services.png)

### <a name="part-2-adding-the-provisioning-connector-app-and-creating-the-connection-to-workday"></a>Část 2: Přidání zřizování konektoru aplikace a vytvoření připojení k Workday

**Konfigurace Workday do Active Directory zřizování:**

1. Přejděte na <https://portal.azure.com>.

2. V levém navigačním panelu vyberte **Azure Active Directory**

3. Vyberte **podnikové aplikace**, pak **všechny aplikace**.

4. Vyberte **přidat aplikaci**a vyberte **všechny** kategorie.

5. Vyhledejte **zřizování Workday do Active Directory**a přidejte tuto aplikaci z galerie.

6. Jakmile aplikace přidána, a na obrazovce s podrobnostmi aplikace se zobrazí, vyberte **zřizování**

7. Změnit **zřizování** **režimu** k **automatické**

8. Dokončení **přihlašovacích údajů správce** části následujícím způsobem:

   * **Uživatelské jméno správce** – zadejte uživatelské jméno účtu systému integraci Workday, se připojí název domény tenantu. By měl vypadat přibližně jako: **username@tenant_name**

   * **Heslo správce –** zadejte heslo účtu systému integraci Workday

   * **Adresa URL tenanta** zadejte adresu URL pro koncový bod webové služby Workday pro vašeho tenanta. Tato hodnota by měl vypadat takto: https://wd3-impl-services1.workday.com/ccx/service/contoso4, kde *contoso4* se nahradí názvem vašeho tenanta správné a *wd3 impl* se nahradí řetězcem správné prostředí.

   * **Doménové struktury služby Active Directory -** "Name" doménu služby Active Directory, je registrovaný u agenta. Pomocí rozevíracího seznamu můžete vybrat cílovou doménu pro zřizování. Tato hodnota je obvykle řetězec jako: *contoso.com*

   * **Kontejner služby Active Directory -** zadejte rozlišující název kontejneru, kde vytváříte uživatelské účty ve výchozím nastavení agenta.
        Příklad: *Organizační jednotky = standardní uživatelé, OU = Users, DC = contoso, DC = test*
> [!NOTE]
> Toto nastavení pouze dodává do hry pro vytváření účtů uživatelů, pokud *parentDistinguishedName* atribut není nakonfigurovaný v mapování atributů. Toto nastavení nepoužívají pro hledání uživatele nebo aktualizace operace. Sub stromu celé doméně spadá do rozsahu operace vyhledávání.

   * **E-mailové oznámení –** zadejte svou e-mailovou adresu a zaškrtněte políčko "Odeslat e-mail, pokud dojde k selhání".

> [!NOTE]
> Služba zřizování Azure AD odešle e-mailové oznámení, pokud úloha zřizování přejde do [karantény](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning#quarantine) stavu.

   * Klikněte na tlačítko **Test připojení** tlačítko. Pokud je test připojení úspěšný, klikněte na tlačítko **Uložit** tlačítko v horní části. Pokud selže, zkontrolujte, že jsou platné přihlašovací údaje Workday a přihlašovací údaje služby AD na instalaci agenta je nakonfigurované.

     ![portál Azure](./media/workday-inbound-tutorial/wd_1.png)

   * Jakmile přihlašovací údaje jsou úspěšně, uloží **mapování** oddíl se zobrazí výchozí mapování **synchronizovat pracovníkům Workday v místní službě Active Directory**

### <a name="part-3-configure-attribute-mappings"></a>3. část: Nakonfigurujte mapování atributů

V této části můžete nakonfigurovat uživatele tok dat z Workday do Active Directory.

1. Na kartě zřizování v rámci **mapování**, klikněte na tlačítko **synchronizovat pracovníkům Workday v místní službě Active Directory**.

2. V **obor zdrojového objektu** pole, můžete vybrat, které skupiny uživatelů ve Workday by měla být v oboru pro zřizování do služby AD, díky definování sady filtrů založený na atributu. Výchozí obor je "všichni uživatelé ve Workday". Příklad filtrů:

   * Příklad: Obor pro uživatele s ID pracovních procesů mezi 1000000 a 2000000

      * Atribut: WorkerID

      * Operátor: Regulárního výrazu shody

      * Hodnota: (1[0-9][0-9][0-9][0-9][0-9][0-9])

   * Příklad: Pouze zaměstnanci a nikoli závislé pracovní procesy

      * Atribut: EmployeeID

      * Operátor: NENÍ ROVNO HODNOTĚ NULL

> [!TIP]
> Při první konfiguraci zřizování aplikací, je potřeba otestovat a ověřit mapování atributů a výrazy, abyste měli jistotu, že se vám umožňuje požadovaný výsledek. Společnost Microsoft doporučuje použití nepříznivě filtruje podle **obor zdrojového objektu** otestovat mapování s několika testovacích uživatelů z Workday. Jakmile si ověříte, že pracujete mapování, pak můžete odebrat filtr nebo rozbalte postupně tak, aby obsahovala více uživatelů.

3. V **akce cílového objektu** pole, můžete globálně filtrovat, jaké akce se provádí na základě Active Directory. **Vytvoření** a **aktualizace** rozsahu je nejvíce.

4. V **mapování atributů** oddílu, můžete definovat jak jednotlivé Workday atributy mapování atributů služby Active Directory.

5. Klikněte na existující mapování atributů ji aktualizovat, nebo klikněte na tlačítko **přidat nové mapování** v dolní části obrazovky, chcete-li přidat nové mapování. Mapování individuální atribut podporuje tyto vlastnosti:

      * **Typ mapování**

         * **Přímé** – hodnota atributu Workday zapíše atribut AD, a to bez jediné změny

         * **Konstantní** -zapsat hodnotu statické, konstantní řetězec pro atribut AD

         * **Výraz** – umožňuje psát vlastní hodnotu pro atribut AD založené na jeden nebo více atributů Workday. [Další informace najdete v článku o výrazech](../manage-apps/functions-for-customizing-application-data.md).

      * **Zdrojový atribut** – atribut uživatele z Workday. Pokud hledáte atribut není k dispozici, najdete v článku [přizpůsobení seznamu atributů uživatelů Workday](#customizing-the-list-of-workday-user-attributes).

      * **Výchozí hodnota** – volitelné. Pokud zdrojový atribut má prázdnou hodnotu, mapování zapíše tuto hodnotu.
            Nejběžnější konfigurací je toto pole nechat prázdné.

      * **Cílový atribut** – atribut uživatele ve službě Active Directory.

      * **Párování objektů pomocí tohoto atributu** – jestli má toto mapování použít k jednoznačné identifikaci uživatele mezi Workday a služby Active Directory. Tato hodnota je obvykle nastavena pole ID pracovního procesu pro Workday, která se obvykle mapuje na jeden z atributů ID zaměstnance ve službě Active Directory.

      * **Priorita porovnávání** – více shodné atributy lze nastavit. Když je více, jsou vyhodnocovány v pořadí určeném parametrem toto pole. Jakmile se najde shoda, žádné další odpovídající atributy jsou vyhodnocovány.

      * **Použít toto mapování**

         * **Vždy** – použít toto mapování na obou vytvoření uživatele a aktualizovat akce

         * **Jenom během vytváření** -použít toto mapování jenom na akce vytvoření uživatele

6. Chcete-li uložit mapování, klikněte na tlačítko **Uložit** v horní části mapování atributů.

   ![portál Azure](./media/workday-inbound-tutorial/wd_2.png)

#### <a name="below-are-some-example-attribute-mappings-between-workday-and-active-directory-with-some-common-expressions"></a>Níže je několik příkladů mapování atributů mezi Workday a Active Directory, se některé běžné výrazy

* Výraz, který se mapuje *parentDistinguishedName* atribut slouží k zřizování uživatelů pro různé organizační jednotky, na základě jednoho nebo více atributů zdroj Workday. Tento příklad umístí uživatele na základě různých organizačních jednotek ve městě se.

* *UserPrincipalName* atribut v Active Directory je generována pomocí funkce duplicit [SelectUniqueValue](../manage-apps/functions-for-customizing-application-data.md#selectuniquevalue) , která zkontroluje existenci vygenerovanou hodnotu v doméně cílové AD a pouze Pokud bude jednoznačný, nastaví ho.  

* [Je dokumentace ke službě na psaní výrazů zde](../manage-apps/functions-for-customizing-application-data.md). Tato část obsahuje příklady o tom, jak odeberte speciální znaky.

| WORKDAY ATRIBUT | ATRIBUT SLUŽBY ACTIVE DIRECTORY |  ODPOVÍDAJÍCÍ ID? | VYTVOŘIT / AKTUALIZOVAT |
| ---------- | ---------- | ---------- | ---------- |
| **WorkerID**  |  EmployeeID | **Ano** | Zapisovat pouze při vytváření |
| **PreferredNameData**    |  CN    |   |   Zapisovat pouze při vytváření |
| **SelectUniqueValue (připojit k ("@", připojte se k (".", \[FirstName\], \[LastName\]), "contoso.com"), připojte se k ("@", připojte se k (".", Mid (\[FirstName\], 1, 1), \[LastName\]), "contoso.com"), připojte se k ("@", připojte se k (".", Mid (\[FirstName\], 1, 2), \[LastName\]), "contoso.com"))**   | userPrincipalName (Hlavní název uživatele)     |     | Zapisovat pouze při vytváření 
| **Nahraďte(Mid(Nahraďte(\[UserID\], , "(\[\\\\/\\\\\\\\\\\\\[\\\\\]\\\\:\\\\;\\\\|\\\\=\\\\,\\\\+\\\\\*\\\\?\\\\&lt;\\\\&gt;\])", , "", , ), 1, 20), , "([\\\\.)\*\$](file:///\\.)*$)", , "", , )**      |    sAMAccountName            |     |         Zapisovat pouze při vytváření |
| **Přepínač (\[aktivní\],, "0", "PRAVDA", "1", "Nepravda")** |  accountDisabled      |     | Vytváření a aktualizace |
| **Jméno**   | givenName       |     |    Vytváření a aktualizace |
| **LastName**   |   sériové číslo   |     |  Vytváření a aktualizace |
| **PreferredNameData**  |  displayName |     |   Vytváření a aktualizace |
| **Společnosti**         | Společnosti   |     |  Vytváření a aktualizace |
| **SupervisoryOrganization**  | Oddělení  |     |  Vytváření a aktualizace |
| **ManagerReference**   | manažer  |     |  Vytváření a aktualizace |
| **BusinessTitle**   |  název     |     |  Vytváření a aktualizace | 
| **AddressLineData**    |  streetAddress  |     |   Vytváření a aktualizace |
| **Obec**   |   l   |     | Vytváření a aktualizace |
| **CountryReferenceTwoLetter**      |   co |     |   Vytváření a aktualizace |
| **CountryReferenceTwoLetter**    |  c  |     |         Vytváření a aktualizace |
| **CountryRegionReference** |  St     |     | Vytváření a aktualizace |
| **WorkSpaceReference** | physicalDeliveryOfficeName    |     |  Vytváření a aktualizace |
| **PostalCode**  |   PSČ  |     | Vytváření a aktualizace |
| **PrimaryWorkTelephone**  |  telephoneNumber   |     | Vytváření a aktualizace |
| **Fax**      | facsimileTelephoneNumber     |     |    Vytváření a aktualizace |
| **Mobile**  |    Mobilní zařízení       |     |       Vytváření a aktualizace |
| **LocalReference** |  preferredLanguage  |     |  Vytváření a aktualizace |                                               
| **Přepínač (\[magistrát\], "organizační jednotky standardní uživatelé, OU = uživatelů, OU = výchozí, OU = umístění, DC = = contoso, DC = com", "Dallas", "organizační jednotky standardní uživatelé, OU = uživatelů, OU = Dallas, OU = umístění, DC = = contoso, DC = com", "Austin", "organizační jednotky standardní uživatelé, OU = Uživatelé, OU = Austin, OU = umístění, DC = = contoso, DC = com ","Seattle"," organizační jednotky standardní uživatelé, OU = uživatelů, OU = Seattle, OU = umístění, DC = = contoso, DC = com ","Londýn"," organizační jednotky standardní uživatelé, OU = uživatelů, OU = Londýn, OU = umístění, DC = = contoso, DC = com ")**  | parentDistinguishedName     |     |  Vytváření a aktualizace |

Po dokončení konfigurace mapování atributů, můžete nyní [povolit a spustit služba zřizování uživatelů](#enable-and-launch-user-provisioning).

## <a name="configuring-user-provisioning-to-azure-ad"></a>Konfigurace zřizování uživatelů pro Azure AD

Následující části popisují postup konfigurace zřizování uživatelů z Workday do služby Azure AD pro čistě cloudových nasazení.

* [Přidání konektoru aplikace zřizování Azure AD a vytvoření připojení k Workday](#part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday)
* [Nakonfigurujte mapování atributů Workday a Azure AD](#part-2-configure-workday-and-azure-ad-attribute-mappings)
* [Povolení a spuštění zřizování uživatelů](#enable-and-launch-user-provisioning)

> [!IMPORTANT]
> Podle níže uvedeného postupu, pouze pokud máte uživatelů pouze cloudu, které je potřeba zřídit do služby Azure AD a ne místní služby Active Directory.

### <a name="part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday"></a>Část 1: Přidání konektoru aplikace zřizování Azure AD a vytvoření připojení k Workday

**Postup konfigurace Workday do Azure Active Directory zřizování uživatelů jenom cloudu:**

1. Přejděte do části <https://portal.azure.com> (Soubor > Nový > Jiné).

2. V levém navigačním panelu vyberte **Azure Active Directory**

3. Vyberte **podnikové aplikace**, pak **všechny aplikace**.

4. Vyberte **přidat aplikaci**a pak vyberte **všechny** kategorie.

5. Vyhledejte **Workday do Azure AD zřizování**a přidejte tuto aplikaci z galerie.

6. Jakmile aplikace přidána, a na obrazovce s podrobnostmi aplikace se zobrazí, vyberte **zřizování**

7. Změnit **zřizování** **režimu** k **automatické**

8. Dokončení **přihlašovacích údajů správce** části následujícím způsobem:

   * **Uživatelské jméno správce** – zadejte uživatelské jméno účtu systému integraci Workday, se připojí název domény tenantu. By měla vypadat podobně jako: username@contoso4

   * **Heslo správce –** zadejte heslo účtu systému integraci Workday

   * **Adresa URL tenanta** zadejte adresu URL pro koncový bod webové služby Workday pro vašeho tenanta. Tato hodnota by měl vypadat takto: https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources, kde *contoso4* se nahradí názvem vašeho tenanta správné a *wd3 impl* se nahradí řetězcem správné prostředí. Pokud je tato adresa URL není známý, prosím pracujete s Workday integrace partnerů nebo podporu zástupce určit správnou adresu URL k použití.

   * **E-mailové oznámení –** zadejte svou e-mailovou adresu a zaškrtněte políčko "Odeslat e-mail, pokud dojde k selhání".

   * Klikněte na tlačítko **Test připojení** tlačítko.

   * Pokud je test připojení úspěšný, klikněte na tlačítko **Uložit** tlačítko v horní části. Pokud se to nezdaří, zkontrolujte, že adresa URL Workday a přihlašovací údaje jsou platné ve Workday.

### <a name="part-2-configure-workday-and-azure-ad-attribute-mappings"></a>Část 2: Nakonfigurujte mapování atributů Workday a Azure AD

V této části můžete nakonfigurovat jak uživatelská data z Workday do Azure Active Directory pro toky uživatelů pouze cloudu.

1. Na kartě zřizování v rámci **mapování**, klikněte na tlačítko **synchronizaci pracovních procesů do Azure AD**.

2. V **obor zdrojového objektu** pole, můžete vybrat, které skupiny uživatelů ve Workday by měla být v oboru pro zřizování do služby Azure AD tak, že definujete sady filtrů založený na atributu. Výchozí obor je "všichni uživatelé ve Workday". Příklad filtrů:

   * Příklad: Obor pro uživatele s ID pracovních procesů mezi 1000000 a 2000000

      * Atribut: WorkerID

      * Operátor: Regulárního výrazu shody

      * Hodnota: (1[0-9][0-9][0-9][0-9][0-9][0-9])

   * Příklad: Pouze závislé pracovní procesy a ne regulární zaměstnance

      * Atribut: ContingentID

      * Operátor: NENÍ ROVNO HODNOTĚ NULL

3. V **akce cílového objektu** pole, můžete globálně filtrovat, jaké akce se provádí v Azure AD. **Vytvoření** a **aktualizace** rozsahu je nejvíce.

4. V **mapování atributů** oddílu, můžete definovat jak jednotlivé Workday atributy mapování atributů služby Active Directory.

5. Klikněte na existující mapování atributů ji aktualizovat, nebo klikněte na tlačítko **přidat nové mapování** v dolní části obrazovky, chcete-li přidat nové mapování. Mapování individuální atribut podporuje tyto vlastnosti:

   * **Typ mapování**

      * **Přímé** – hodnota atributu Workday zapíše atribut AD, a to bez jediné změny

      * **Konstantní** -zapsat hodnotu statické, konstantní řetězec pro atribut AD

      * **Výraz** – umožňuje psát vlastní hodnotu pro atribut AD založené na jeden nebo více atributů Workday. [Další informace najdete v článku o výrazech](../manage-apps/functions-for-customizing-application-data.md).

   * **Zdrojový atribut** – atribut uživatele z Workday. Pokud hledáte atribut není k dispozici, najdete v článku [přizpůsobení seznamu atributů uživatelů Workday](#customizing-the-list-of-workday-user-attributes).

   * **Výchozí hodnota** – volitelné. Pokud zdrojový atribut má prázdnou hodnotu, mapování zapíše tuto hodnotu.
            Nejběžnější konfigurací je toto pole nechat prázdné.

   * **Cílový atribut** – atribut uživatele ve službě Azure AD.

   * **Párování objektů pomocí tohoto atributu** – jestli se tento atribut slouží k jednoznačné identifikaci uživatele mezi Workday a Azure AD. Tato hodnota je obvykle nastavena pole ID pracovního procesu pro Workday, která se obvykle mapuje na atribut ID zaměstnance (Nový) nebo atributu rozšíření ve službě Azure AD.

   * **Priorita porovnávání** – více shodné atributy lze nastavit. Když je více, jsou vyhodnocovány v pořadí určeném parametrem toto pole. Jakmile se najde shoda, žádné další odpovídající atributy jsou vyhodnocovány.

   * **Použít toto mapování**

     * **Vždy** – použít toto mapování na obou vytvoření uživatele a aktualizovat akce

     * **Jenom během vytváření** -použít toto mapování jenom na akce vytvoření uživatele

6. Chcete-li uložit mapování, klikněte na tlačítko **Uložit** v horní části mapování atributů.

Po dokončení konfigurace mapování atributů, můžete nyní [povolit a spustit služba zřizování uživatelů](#enable-and-launch-user-provisioning).

## <a name="configuring-writeback-of-email-addresses-to-workday"></a>Konfigurace zpětného zápisu e-mailových adres do Workday

Postupujte podle těchto pokynů a nakonfigurovat zpětný zápis e-mailové adresy uživatele ze služby Azure Active Directory do Workday.

* [Přidání konektoru aplikace zpětného zápisu a vytvoření připojení k Workday](#part-1-adding-the-writeback-connector-app-and-creating-the-connection-to-workday)
* [Nakonfigurujte zpětný zápis mapování atributů](#part-2-configure-writeback-attribute-mappings)
* [Povolení a spuštění zřizování uživatelů](#enable-and-launch-user-provisioning)

### <a name="part-1-adding-the-writeback-connector-app-and-creating-the-connection-to-workday"></a>Část 1: Přidání konektoru aplikace zpětného zápisu a vytvoření připojení k Workday

**Ke konfiguraci konektoru Workday zpětného zápisu:**

1. Přejděte na <https://portal.azure.com>.

2. V levém navigačním panelu vyberte **Azure Active Directory**

3. Vyberte **podnikové aplikace**, pak **všechny aplikace**.

4. Vyberte **přidat aplikaci**a pak **všechny** kategorie.

5. Vyhledejte **zpětný zápis Workday**a přidejte tuto aplikaci z galerie.

6. Jakmile aplikace přidána, a na obrazovce s podrobnostmi aplikace se zobrazí, vyberte **zřizování**

7. Změnit **zřizování** **režimu** k **automatické**

8. Dokončení **přihlašovacích údajů správce** části následujícím způsobem:

   * **Uživatelské jméno správce** – zadejte uživatelské jméno účtu systému integraci Workday, se připojí název domény tenantu. By měla vypadat podobně jako: *username@contoso4*

   * **Heslo správce –** zadejte heslo účtu systému integraci Workday

   * **Adresa URL tenanta** zadejte adresu URL pro koncový bod webové služby Workday pro vašeho tenanta. Tato hodnota by měl vypadat takto: https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources, kde *contoso4* se nahradí názvem vašeho tenanta správné a *wd3 impl* nahrazuje řetězcem správné prostředí (v případě potřeby).

   * **E-mailové oznámení –** zadejte svou e-mailovou adresu a zaškrtněte políčko "Odeslat e-mail, pokud dojde k selhání".

   * Klikněte na tlačítko **Test připojení** tlačítko. Pokud je test připojení úspěšný, klikněte na tlačítko **Uložit** tlačítko v horní části. Pokud se to nezdaří, zkontrolujte, že adresa URL Workday a přihlašovací údaje jsou platné ve Workday.

### <a name="part-2-configure-writeback-attribute-mappings"></a>Část 2: Nakonfigurujte zpětný zápis mapování atributů

V této části můžete nakonfigurovat způsob, jakým probíhá zpětný zápis atributů ze služby Azure AD k Workday.

1. Na kartě zřizování v rámci **mapování**, klikněte na tlačítko **synchronizace Azure Active Directory Users do Workday**.

2. V **obor zdrojového objektu** pole, můžete volitelně můžete filtrovat, které skupiny uživatelů v Azure Active Directory je jejich e-mailové adresy zapisovat zpátky do Workday. Výchozí obor je "všichni uživatelé ve službě Azure AD".

3. V **mapování atributů** části, aktualizovat odpovídající ID k označení atributu v Azure Active Directory, kde je uložen na ID pro pracovníka Workday nebo ID zaměstnance. Oblíbené odpovídajícím metodou je synchronizovat Workday pracovního procesu ID nebo ID zaměstnance na extensionAttribute1 15 ve službě Azure AD a pak pomocí tohoto atributu ve službě Azure AD pro vyhledání uživatelů zpět ve Workday.

4. Chcete-li uložit mapování, klikněte na tlačítko **Uložit** v horní části mapování atributů.

Po dokončení konfigurace mapování atributů, můžete nyní [povolit a spustit služba zřizování uživatelů](#enable-and-launch-user-provisioning). 

## <a name="enable-and-launch-user-provisioning"></a>Povolení a spuštění zřizování uživatelů

Po dokončení konfigurace zřizování aplikací Workday můžete zapnout službu zřizování na webu Azure Portal.

> [!TIP]
> Ve výchozím nastavení při zapnutí služby zřizování se opraví, zahájí se zřizování provoz pro všechny uživatele v oboru. Pokud jsou v problémy data mapování nebo Workday chyby, úlohy zřizování může selhat a přejde do stavu karantény. Se tomu Pokud chcete vyhnout, doporučujeme, doporučujeme, abyste konfiguraci **obor zdrojového objektu** filtr a testování vaší mapování atributů s několika testovacích uživatelů před spuštěním úplné synchronizace pro všechny uživatele. Jakmile si ověříte, že mapování fungují a jsou díky tomu získáte požadovaných výsledků, pak můžete buď odeberte filtr nebo rozbalte postupně tak, aby obsahovala více uživatelů.

1. V **zřizování** kartu, nastavte **stavu zřizování** k **na**.

2. Klikněte na **Uložit**.

3. Tato operace spustí počáteční synchronizaci, to může trvat proměnný počet hodin v závislosti na tom, kolik uživatelů je v tenantovi Workday. 

4. V okamžiku, zkontrolujte **protokoly auditu** kartu na webu Azure Portal najdete v článku jaké akce proběhla zřizovací služba. Protokoly auditu zobrazuje všechny události jednotlivou synchronizaci provést zřizovací služba, například kteří uživatelé jsou číst z Workday a pak následně přidáním nebo aktualizací do služby Active Directory. Odkazovat [Poradce při potížích s](#troubleshooting-provisioning-issues) části pokyny, jak kontrolovat protokoly auditu a opravte chyby zřizování.

5. Po dokončení počáteční synchronizace bude zapisovat souhrnnou sestavu auditu **zřizování** kartu, jak je znázorněno níže.

   ![portál Azure](./media/workday-inbound-tutorial/wd_3.png)

## <a name="frequently-asked-questions-faq"></a>Nejčastější dotazy

* **Dotazy možnosti řešení**
  * [Při zpracování nové zařazení z Workday, jak řešení nastavte heslo pro nový uživatelský účet ve službě Active Directory?](#when-processing-a-new-hire-from-workday-how-does-the-solution-set-the-password-for-the-new-user-account-in-active-directory)
  * [Podporuje řešení pro odesílání e-mailová oznámení po dokončení operace zřízení?](#does-the-solution-support-sending-email-notifications-after-provisioning-operations-complete)
  * [Jak spravovat doručování hesel u nových zaměstnanců a bezpečně poskytují mechanismus pro resetování hesla?](#how-do-i-manage-delivery-of-passwords-for-new-hires-and-securely-provide-a-mechanism-to-reset-their-password)
  * [Řešení mezipaměti Workday uživatelské profily v cloudové službě Azure AD nebo ve vrstvě zřizování agenta?](#does-the-solution-cache-workday-user-profiles-in-the-azure-ad-cloud-or-at-the-provisioning-agent-layer)
  * [Přiřazení podporu řešení místní skupiny AD pro uživatele?](#does-the-solution-support-assigning-on-premises-ad-groups-to-the-user)
  * [K dotazování a aktualizaci pracovních profilů Workday které Workday rozhraní API používá řešení?](#which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles)
  * [Můžete nakonfigurovat tenantovi Workday HCM s dva tenanty Azure AD?](#can-i-configure-my-workday-hcm-tenant-with-two-azure-ad-tenants)
  * [Proč zřizování aplikace "Workday do služby Azure AD" uživatelů není podporováno, pokud jsme nasadili Azure AD Connect?](#why-workday-to-azure-ad-user-provisioning-app-is-not-supported-if-we-have-deployed-azure-ad-connect)
  * [Jak navrhnout zlepšení nebo požádat o nové funkce související s integrací Workday a Azure AD?](#how-do-i-suggest-improvements-or-request-new-features-related-to-workday-and-azure-ad-integration)

* **Zřizování agenta dotazy**
  * [Co je všeobecně dostupné verze zřizování agenta?](#what-is-the-ga-version-of-the-provisioning-agent)
  * [Jak poznám, že verze agenta Moje zřizování?](#how-do-i-know-the-version-of-my-provisioning-agent)
  * [Microsoft automaticky nabízené aktualizace zřizování agenta?](#does-microsoft-automatically-push-provisioning-agent-updates)
  * [Mohu nainstalovat agenta zřizování na stejný server se službou AAD Connect?](#can-i-install-the-provisioning-agent-on-the-same-server-running-aad-connect)
  * [Jak nakonfigurovat zřizování agenta pro použití proxy serveru pro odchozí komunikaci pomocí protokolu HTTP?](#how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication)
  * [Jak zajistit agentem zřizování schopné komunikovat s tenantem Azure AD a brány firewall blokují porty vyžadované službou agenta?](#how-do-i-ensure-that-the-provisioning-agent-is-able-to-communicate-with-the-azure-ad-tenant-and-no-firewalls-are-blocking-ports-required-by-the-agent)
  * [Jak můžu rušit registraci domény přidružené k Moje zřizování agenta?](#how-do-i-de-register-the-domain-associated-with-my-provisioning-agent)
  * [Jak odinstaluji zřizování agenta?](#how-do-i-uninstall-the-provisioning-agent)
  
* **WORKDAY na AD atribut mapování a konfigurace dotazy**
  * [Jak zálohovat nebo exportovat pracovní kopie Workday zřizování mapování atributu a schéma?](#how-do-i-back-up-or-export-a-working-copy-of-my-workday-provisioning-attribute-mapping-and-schema)
  * [Mám k dispozici vlastní atributy ve Workday a služby Active Directory. Konfigurace řešení pro práci s Moje vlastní atributy](#i-have-custom-attributes-in-workday-and-active-directory-how-do-i-configure-the-solution-to-work-with-my-custom-attributes)
  * [Můžete zřídit fotky uživatele z Workday do Active Directory?](#can-i-provision-users-photo-from-workday-to-active-directory)
  * [Jak synchronizovat mobilní čísla z Workday podle souhlasu uživatele pro použití veřejného?](#how-do-i-sync-mobile-numbers-from-workday-based-on-user-consent-for-public-usage)
  * [Jak formátovat zobrazovaná jména ve službě AD na základě atributů oddělení, země nebo město a regionální odchylky popisovač uživatele?](#how-do-i-format-display-names-in-ad-based-on-the-users-departmentcountrycity-attributes-and-handle-regional-variances)
  * [Jak lze pomocí SelectUniqueValue vytvořit jedinečné hodnoty pro atribut samAccountName?](#how-can-i-use-selectuniquevalue-to-generate-unique-values-for-samaccountname-attribute)
  * [Jak odebrat znaky s diakritikou a převést je na normální Anglická písmena abecedy?](#how-do-i-remove-characters-with-diacritics-and-convert-them-into-normal-english-alphabets)

### <a name="solution-capability-questions"></a>Dotazy možnosti řešení

#### <a name="when-processing-a-new-hire-from-workday-how-does-the-solution-set-the-password-for-the-new-user-account-in-active-directory"></a>Při zpracování nové zařazení z Workday, jak řešení nastavte heslo pro nový uživatelský účet ve službě Active Directory?

Při místní agentem zřizování získá požadavek na vytvoření nového účtu AD, automaticky generuje komplexní náhodné heslo splňující požadavky na složitost hesla definované serveru služby AD a tuto hodnotu nastaví v objektu user. Toto heslo se neprotokolují kdekoli.

#### <a name="does-the-solution-support-sending-email-notifications-after-provisioning-operations-complete"></a>Podporuje řešení pro odesílání e-mailová oznámení po dokončení operace zřízení?

Ne, odesílání e-mailová oznámení po dokončení zřizování operací není v aktuální verzi podporováno.

#### <a name="how-do-i-manage-delivery-of-passwords-for-new-hires-and-securely-provide-a-mechanism-to-reset-their-password"></a>Jak spravovat doručování hesel u nových zaměstnanců a bezpečně poskytují mechanismus pro resetování hesla?

Jednou z konečné kroky při zřizování nového účtu AD je doručování dočasné heslo přiřazené k účtu uživatele AD. Řada podniků dál používat tradiční přístup správce uživatele, který pak předá heslo pro nový pracovní zařazení/contingent doručování dočasné heslo. Tento proces je chyba e zabudované zabezpečení a je k dispozici možnost implementovat lepší přístup pomocí možností Azure AD k dispozici.

Jako součást procesu náborovou HR týmy obvykle spustíte kontrolu na pozadí a ověřte číslo mobilního telefonu nové zařazení. S aplikací Workday s integrací zřizování uživatelů služby AD můžete sestavovat nad tuto skutečnost a zavedení samoobslužné hesla resetovat možnosti uživatele na 1. den. Toho lze dosáhnout šíření atribut "Číslo mobilního telefonu" nové zařazení z Workday do AD a pak ze služby AD do služby Azure AD pomocí AAD Connect. Jakmile je k dispozici ve službě Azure AD "Mobilní číslo", můžete povolit [samoobslužné resetování hesla (SSPR)](../authentication/howto-sspr-authenticationdata.md) pro účet uživatele, takže na 1 den, nové zařazení použít číslo mobilního telefonu zaregistrovaná a ověřené pro ověřování.

#### <a name="does-the-solution-cache-workday-user-profiles-in-the-azure-ad-cloud-or-at-the-provisioning-agent-layer"></a>Řešení mezipaměti Workday uživatelské profily v cloudové službě Azure AD nebo ve vrstvě zřizování agenta?

Ne, řešení nespravuje mezipaměti uživatelských profilů. Služba zřizování Azure AD slouží pouze jako zpracovatel dat, čtení dat z Workday a zápis do cílové služby Active Directory nebo Azure AD. V části [Správa osobních údajů](#managing-personal-data) podrobnosti související s uchovávání informací o ochraně osobních údajů a dat uživatele.

#### <a name="does-the-solution-support-assigning-on-premises-ad-groups-to-the-user"></a>Přiřazení podporu řešení místní skupiny AD pro uživatele?

Tato funkce není aktuálně podporována. Doporučené řešení je k nasazení, který se dotazuje koncový bod Azure AD Graph API pro data protokolu auditování skript prostředí PowerShell a pomocí, který můžete aktivovat scénáře, jako je přiřazení skupiny. Tento skript Powershellu můžete připojit plánovače úloh a nasazené na stejném izolovaném prostoru s agentem zřizování.  

#### <a name="which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles"></a>K dotazování a aktualizaci pracovních profilů Workday které Workday rozhraní API používá řešení?

Řešení aktuálně používá následující rozhraní API Workday:

* Get_Workers (v21.1) pro načítání informací o pracovního procesu
* Maintain_Contact_Information (v26.1) pro funkci zpětného zápisu pracovní e-mailu

#### <a name="can-i-configure-my-workday-hcm-tenant-with-two-azure-ad-tenants"></a>Můžete nakonfigurovat tenantovi Workday HCM s dva tenanty Azure AD?

Ano, tato konfigurace je podporovaná. Tady je obecný postup pro konfiguraci tohoto scénáře:

* Nasazení agentem Zřizování #1 a zaregistrujte je v tenantovi Azure AD #1.
* Nasazení agentem zřizování #2 a zaregistrujte je v tenantovi Azure AD #2.
* Podle "Podřízené domény", která bude spravovat každý Agent zřizování, každého agenta konfigurovat s domény. Jeden agent může zpracovávat více domén.
* Na webu Azure portal Workday do aplikace zřizování uživatelů AD každého klienta nainstalovat a nakonfigurovat ji s příslušnými doménami.

#### <a name="why-workday-to-azure-ad-user-provisioning-app-is-not-supported-if-we-have-deployed-azure-ad-connect"></a>Proč zřizování aplikace "Workday do služby Azure AD" uživatelů není podporováno, pokud jsme nasadili Azure AD Connect?

Při použití služby Azure AD v hybridním režimu (pokud ji obsahuje směs cloud + místních uživatelů), je důležité mít jasná definice "zdroj autority". Hybridní scénáře obvykle vyžadují nasazení služby Azure AD Connect. Po nasazení služby Azure AD Connect místní AD je zdroj autority. Zavedení Workday do konektoru služby Azure AD do kombinaci může vést k situaci, ve kterém by mohl přepsat hodnoty atributů Workday potenciálně hodnoty nastavené pomocí služby Azure AD Connect. Proto použití zřizování aplikace "Workday do služby Azure AD" není podporováno, pokud Azure AD Connect je povolena. V takových situacích doporučujeme používat "Workday k zřizování uživatelů AD" aplikace pro získávání uživatelů do místní služby AD a pak je synchronizovat do služby Azure AD pomocí služby Azure AD Connect.

#### <a name="how-do-i-suggest-improvements-or-request-new-features-related-to-workday-and-azure-ad-integration"></a>Jak navrhnout zlepšení nebo požádat o nové funkce související s integrací Workday a Azure AD?

Vaše zpětná vazba je vysoce Vážíme si toho, abychom nastavení orientace budoucí vydané verze a vylepšení. Vítáme veškerá zpětná vazba a doporučujeme, abyste odešlete svůj nápad nebo zlepšování návrh v [fóru pro zpětnou vazbu služby Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory). Pro konkrétní nápady, připomínky související s integrací Workday, vyberte kategorii *aplikací SaaS* a vyhledávání pomocí klíčových slov *Workday* najít existující zpětnou vazbu související s pracovního dne.

![Aplikace UserVoice SaaS](media/workday-inbound-tutorial/uservoice_saas_apps.png)

![UserVoice Workday](media/workday-inbound-tutorial/uservoice_workday_feedback.png)

Při návrhu nový nápad, zkontrolujte Pokud někdo jiný již navrhl podobné funkce. V takovém případě můžete hlasovat až žádosti o funkce nebo vylepšení. Můžete také ponechat komentáře týkající se vašemu případu použití konkrétní svou podporu myšlenkou a ukazují, jak bude tato funkce užitečné pro vás moc.

### <a name="provisioning-agent-questions"></a>Zřizování agenta dotazy

#### <a name="what-is-the-ga-version-of-the-provisioning-agent"></a>Co je všeobecně dostupné verze zřizování agenta?

* Verze GA agentem zřizování je 1.1.30 a vyšší.
* Pokud vaše verze agenta je nižší než 1.1.30, používáte verzi public preview a je automaticky aktualizována na verzi GA Pokud má server, který hostuje agenta .NET 4.7.1 modulu runtime.
  * Je možné [zkontrolujte verze rozhraní .NET](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) nainstalovali na server. Pokud server není spuštěn .NET 4.7.1, můžete si [stáhněte a nainstalujte .NET 4.7.1](https://support.microsoft.com/help/4033342/the-net-framework-4-7-1-offline-installer-for-windows). Zřizování agenta bude automaticky aktualizovat na verzi GA po instalaci rozhraní .NET 4.7.1.

#### <a name="how-do-i-know-the-version-of-my-provisioning-agent"></a>Jak poznám, že verze agenta Moje zřizování?

* Přihlaste se k Windows serveru, kde je nainstalovaný Agent zřizování.
* Přejděte na **ovládací panely** -> **odinstalovat nebo změnit Program** nabídky
* Vyhledejte verzi odpovídající položce **Microsoft Agent Azure AD Connect zřizování**

  ![portál Azure](./media/workday-inbound-tutorial/pa_version.png)

#### <a name="does-microsoft-automatically-push-provisioning-agent-updates"></a>Microsoft automaticky nabízené aktualizace zřizování agenta?

Ano, společnost Microsoft automaticky aktualizuje agentem zřizování. Můžete zakázat automatické aktualizace pomocí zastavení služby Windows **aktualizační služby Microsoft Azure AD Connect agenta**.

#### <a name="can-i-install-the-provisioning-agent-on-the-same-server-running-aad-connect"></a>Mohu nainstalovat agenta zřizování na stejný server se službou AAD Connect?

Ano, můžete nainstalovat agenta zřizování na stejném serveru, na kterém běží, AAD Connect.

#### <a name="at-the-time-of-configuration-the-provisioning-agent-prompts-for-azure-ad-admin-credentials-does-the-agent-store-the-credentials-locally-on-the-server"></a>Při konfiguraci agenta zřizování vyzve k zadání přihlašovacích údajů správce Azure AD. Agent ukládá přihlašovací údaje místně na serveru?

Během konfigurace zřizování agenta vyzve k zadání přihlašovacích údajů správce Azure AD pouze pro připojení k vašemu tenantovi Azure AD. Neukládá přihlašovací údaje místně na serveru. Ale nezachová přihlašovací údaje použité pro připojení k *místní domény služby Active Directory* v místní heslo trezor Windows.

#### <a name="how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication"></a>Jak nakonfigurovat zřizování agenta pro použití proxy serveru pro odchozí komunikaci pomocí protokolu HTTP?

Podporuje zřizování agenta odchozího proxy serveru. Můžete ho nakonfigurovat tak, že upravíte konfigurační soubor agenta **C:\Program Files\Microsoft Azure AD Connect zřizování Agent\AADConnectProvisioningAgent.exe.config**. Přidejte následující řádky do něj, na konci souboru, těsně před uzavírací `</configuration>` značky.
Proměnné [proxy serveru] a [port proxy serveru] nahraďte názvem vašeho proxy serveru a portu hodnoty.

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

#### <a name="how-do-i-ensure-that-the-provisioning-agent-is-able-to-communicate-with-the-azure-ad-tenant-and-no-firewalls-are-blocking-ports-required-by-the-agent"></a>Jak zajistit agentem zřizování schopné komunikovat s tenantem Azure AD a brány firewall blokují porty vyžadované službou agenta?

Můžete také zkontrolovat, zda mají všechny požadované porty otevřete tak, že otevřete [nástroj pro testování konektoru porty](https://aadap-portcheck.connectorporttest.msappproxy.net/) z vaší místní sítě. Další zelené zaškrtnutí znamená větší odolnost proti chybám.

Pokud chcete mít jistotu, že nástroj poskytuje správných výsledků, nezapomeňte na následující:

* Spusťte nástroj v prohlížeči ze serveru, kam jste nainstalovali agenta zřizování.
* Ujistěte se, že všechny proxy nebo brány firewall pro agenta zřizování jsou použita také na této stránce. To můžete udělat v aplikaci Internet Explorer, tak, že přejdete do **Nastavení -> Možnosti Internetu -> připojení -> Nastavení místní sítě**. Na této stránce se zobrazí pole "Použití Proxy serveru pro svůj LAN". Zaškrtněte toto políčko a vložte adresu proxy serveru do pole "Address".

#### <a name="can-one-provisioning-agent-be-configured-to-provision-multiple-ad-domains"></a>Lze nastavit jeden Agent zřizování ke zřízení více domén AD?

Ano, jeden zřizování Agent může být nakonfigurován pro zpracování více domén AD jako agenta má dohled řadiče příslušné domény. Společnost Microsoft doporučuje skupinu 3 zřizování agentů stejná sada domény AD slouží k zajištění vysoké dostupnosti a poskytuje podporu převzetí služeb při nastavování.

#### <a name="how-do-i-de-register-the-domain-associated-with-my-provisioning-agent"></a>Jak můžu rušit registraci domény přidružené k Moje zřizování agenta?

* Z portálu Azure portal získejte *ID tenanta* vašeho tenanta Azure AD.
* Připojte se k Windows serveru se spuštěným agentem zřizování.
* Otevřete powershell jako správce Windows.
* Přejděte do adresáře, který obsahuje skripty registrace a spuštěním následujících příkazů nahraďte \[ID tenanta\] parametr s hodnotou ID tenanta.

  ```powershell
  cd “C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\RegistrationPowershell\Modules\PSModulesFolder”
  Import-Module "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\RegistrationPowershell\Modules\PSModulesFolder\AppProxyPSModule.psd1"
  Get-PublishedResources -TenantId "[tenant ID]"
  ```

* Ze seznamu agentů, které se zobrazují – kopírování hodnoty "id" pole z tohoto prostředku jehož *resourceName* rovná se na název domény AD.
* Vložte id do tento příkaz a spusťte v Powershellu.

  ```powershell
  Remove-PublishedResource -ResourceId "[resource ID]" -TenantId "[tenant ID]"
  ```

* Znovu spusťte Průvodce konfigurací agenta.
* Všechny ostatní agenty, které byly dřív přiřazené k této doméně bude třeba překonfigurovat.

#### <a name="how-do-i-uninstall-the-provisioning-agent"></a>Jak odinstaluji zřizování agenta?

* Přihlaste se k Windows serveru, kde je nainstalovaný Agent zřizování.
* Přejděte na **ovládací panely** -> **odinstalovat nebo změnit Program** nabídky
* Odinstalujte následující programy:
  * Zřizování agenta Microsoft Azure AD Connect
  * Microsoft Azure AD Connect Updater agenta
  * Microsoft Azure AD Connect, zřizovací balíček agenta

### <a name="workday-to-ad-attribute-mapping-and-configuration-questions"></a>WORKDAY na AD atribut mapování a konfigurace dotazy

#### <a name="how-do-i-back-up-or-export-a-working-copy-of-my-workday-provisioning-attribute-mapping-and-schema"></a>Jak zálohovat nebo exportovat pracovní kopie Workday zřizování mapování atributu a schéma?

Exportovat konfiguraci zřizování uživatelů Workday můžete použít Microsoft Graph API. Použijte postup v části [export a import konfigurace Workday zřizování atribut u mapování uživatele](#exporting-and-importing-your-configuration) podrobnosti.

#### <a name="i-have-custom-attributes-in-workday-and-active-directory-how-do-i-configure-the-solution-to-work-with-my-custom-attributes"></a>Mám k dispozici vlastní atributy ve Workday a služby Active Directory. Konfigurace řešení pro práci s Moje vlastní atributy

Řešení podporuje vlastní atributy Workday a služby Active Directory. Chcete-li přidat vlastní atributy do schématu mapování, otevřete **mapování atributů** okno a přejděte dolů k části rozbalte **zobrazit pokročilé možnosti**. 

![Upravit seznam atributů](./media/workday-inbound-tutorial/wd_edit_attr_list.png)

Chcete-li přidat svoje vlastní atributy Workday, vyberte možnost *upravit seznam atributů pro Workday* a pokud chcete přidat svoje vlastní atributy AD, vyberte možnost *upravit seznam atributů pro na místní služby Active Directory*.

Viz také:

* [Přizpůsobení seznamu atributů uživatelů Workday](#customizing-the-list-of-workday-user-attributes)

#### <a name="how-do-i-configure-the-solution-to-only-update-attributes-in-ad-based-on-workday-changes-and-not-create-any-new-ad-accounts"></a>Jak konfigurovat řešení tak, aby pouze aktualizovat atributy ve službě AD na základě změn Workday a všechny nové účty AD?

Tato konfigurace lze dosáhnout nastavením **akce cílového objektu** v **mapování atributů** okno, jak je znázorněno níže:

![Akce aktualizace](./media/workday-inbound-tutorial/wd_target_update_only.png)

Zaškrtněte políčko "Úpravy" pro pouze operace aktualizace toku z Workday do služby AD. 

#### <a name="can-i-provision-users-photo-from-workday-to-active-directory"></a>Můžete zřídit fotky uživatele z Workday do Active Directory?

Řešení aktuálně nepodporuje nastavení binární atributy, jako *thumbnailphoto nastavuje* a *jpegPhoto* ve službě Active Directory.

#### <a name="how-do-i-sync-mobile-numbers-from-workday-based-on-user-consent-for-public-usage"></a>Jak synchronizovat mobilní čísla z Workday podle souhlasu uživatele pro použití veřejného?

* Přejdete "Zřizování" okno aplikace zřizování Workday.
* Klikněte na mapování atributů 
* V části **mapování**vyberte **synchronizovat pracovníkům Workday v místní službě Active Directory** (nebo **synchronizaci pracovních procesů Workday do služby Azure AD**).
* Na stránce Mapování atributů posuňte se dolů a zaškrtněte políčko "Zobrazit pokročilé možnosti".  Klikněte na **upravit seznam atributů pro Workday**
* V okně, které se otevře, vyhledejte atribut "Mobilní" a klikněte na řádek, můžete upravit **výraz rozhraní API** ![Mobile GDPR](./media/workday-inbound-tutorial/mobile_gdpr.png)

* Nahradit **výraz rozhraní API** s následující nový výraz, který načte číslo mobilního telefonu pracovní pouze v případě, že "Veřejné využití příznak" je nastaven na hodnotu "True" ve Workday.

    ```
     wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Contact_Data/wd:Phone_Data[translate(string(wd:Phone_Device_Type_Reference/@wd:Descriptor),'abcdefghijklmnopqrstuvwxyz','ABCDEFGHIJKLMNOPQRSTUVWXYZ')='MOBILE' and translate(string(wd:Usage_Data/wd:Type_Data/wd:Type_Reference/@wd:Descriptor),'abcdefghijklmnopqrstuvwxyz','ABCDEFGHIJKLMNOPQRSTUVWXYZ')='WORK' and string(wd:Usage_Data/@wd:Public)='1']/@wd:Formatted_Phone
    ```

* Uložení seznamu atributů.
* Uložení mapování atributů.
* Vymazat aktuální stav a znovu spusťte úplnou synchronizaci.

#### <a name="how-do-i-format-display-names-in-ad-based-on-the-users-departmentcountrycity-attributes-and-handle-regional-variances"></a>Jak formátovat zobrazovaná jména ve službě AD na základě atributů oddělení, země nebo město a regionální odchylky popisovač uživatele?

Je běžné požadavky na konfiguraci *displayName* atributu ve službě AD tak, aby také poskytuje informace o oddělení a země daného uživatele. Pro třeba pokud Jan Macek pracuje v oddělení marketingu v USA, můžete jeho *displayName* zobrazí jako *Macek Jan (Marketing-US)*.

Zde je, jak můžete zpracovávat takové požadavky pro vytváření *CN* nebo *displayName* k patřit atributy jako společnosti, organizační jednotky, město nebo zemi.

* Každý atribut Workday jsou načítány s použitím základní rozhraní API jazyka XPATH výraz, který je možné konfigurovat **mapování atributů -> Upřesnit části -> Upravit seznam atributů pro Workday**. Tady je výchozí výraz XPATH rozhraní API pro Workday *PreferredFirstName*, *PreferredLastName*, *společnosti* a *SupervisoryOrganization* atributy.

     [!div class="mx-tdCol2BreakAll"]
     | WORKDAY atribut | Výraz XPATH rozhraní API |
     | ----------------- | -------------------- |
     | PreferredFirstName | wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Name_Data/wd:Preferred_Name_Data/wd:Name_Detail_Data/wd:First_Name/text() |
     | PreferredLastName | wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Name_Data/wd:Preferred_Name_Data/wd:Name_Detail_Data/wd:Last_Name/text() |
     | Společnost | wd:Worker/wd:Worker_Data/wd:Organization_Data/wd:Worker_Organization_Data[wd:Organization_Data/wd:Organization_Type_Reference/wd:ID[@wd:type='Organization_Type_ID']='Company']/wd:Organization_Reference/@wd:Descriptor |
     | SupervisoryOrganization | WD:Worker / wd:Worker_Data / wd:Organization_Data / wd:Worker_Organization_Data / wd:Organization_Data [wd:Organization_Type_Reference / wd:ID [@wd:type= "Organization_Type_ID"] = 'Dozoru'] /wd:Organization_Name/text() |
  
   Potvrzení se svým týmem Workday platí výše výraz rozhraní API pro konfiguraci klienta Workday. Pokud třeba, můžete je upravit podle popisu v části [přizpůsobení seznamu atributů uživatelů Workday](#customizing-the-list-of-workday-user-attributes).

* Informace o zemi k dispozici ve Workday podobně jsou načítány s použitím následující výraz XPATH: *wd:Worker / wd:Worker_Data / wd:Employment_Data / wd:Position_Data / wd:Business_Site_Summary_Data / wd:Address_Data / wd:Country_Reference*

     Existují 5 související země atributy, které jsou k dispozici v části seznamu atribut Workday.

     | WORKDAY atribut | Výraz XPATH rozhraní API |
     | ----------------- | -------------------- |
     | CountryReference | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/wd:ID[@wd:type='ISO_3166-1_Alpha-3_Code']/text() |
     | CountryReferenceFriendly | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/@wd:Descriptor |
     | CountryReferenceNumeric | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/wd:ID[@wd:type='ISO_3166-1_Numeric-3_Code']/text() |
     | CountryReferenceTwoLetter | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/wd:ID[@wd:type='ISO_3166-1_Alpha-2_Code']/text() |
     | CountryRegionReference | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Region_Reference/@wd:Descriptor |

  Se svým týmem Workday potvrďte, že výše uvedené rozhraní API výrazy jsou platné pro konfiguraci klienta Workday. Pokud třeba, můžete je upravit podle popisu v části [přizpůsobení seznamu atributů uživatelů Workday](#customizing-the-list-of-workday-user-attributes).

* Chcete-li sestavit výraz atribut správné mapování, určete, které Workday atribut "vynuceně" představuje uživatelské jméno, poslední název, země a oddělení. Řekněme, že jsou atributy *PreferredFirstName*, *PreferredLastName*, *CountryReferenceTwoLetter* a *SupervisoryOrganization* v uvedeném pořadí. Může být využit k sestavení výrazu pro AD *displayName* atribut následujícím způsobem můžete zobrazit zobrazovaný název, jako je *Macek Jan (Marketing-US)*.

    ```
     Append(Join(", ",[PreferredLastName],[PreferredFirstName]), Join(""," (",[SupervisoryOrganization],"-",[CountryReferenceTwoLetter],")"))
    ```
    Jakmile budete mít pravý výraz, upravit mapování atributů tabulky a změnit *displayName* mapování atributů, jak je znázorněno níže:   ![DisplayName mapování](./media/workday-inbound-tutorial/wd_displayname_map.png)

* Rozšíření příkladu výše, Pojďme say byste chtěli převést názvy měst přicházející z Workday do Zkrácený tvar vlastností hodnoty a použít ho k vytváření zobrazované názvy, jako *Smith, Jan (Kuba)* nebo *Doe, Jana (NYC)*, pak lze tohoto výsledku dosáhnout pomocí výraz přepínače s pracovního dne *magistrát* atribut jako určujícím proměnné.

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
  * [Přepnout syntaxe funkce](../manage-apps/functions-for-customizing-application-data.md#switch)
  * [Připojte se k syntaxe funkce](../manage-apps/functions-for-customizing-application-data.md#join)
  * [Připojit syntaxe funkce](../manage-apps/functions-for-customizing-application-data.md#append)

#### <a name="how-can-i-use-selectuniquevalue-to-generate-unique-values-for-samaccountname-attribute"></a>Jak lze pomocí SelectUniqueValue vytvořit jedinečné hodnoty pro atribut samAccountName?

Řekněme, že chcete vygenerovat jedinečné hodnoty *samAccountName* atribut, pomocí kombinace *FirstName* a *LastName* atributy z Workday. Níže uvedené je výraz, který můžete začít s:

```
SelectUniqueValue(
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,1), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , ),
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,2), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , ),
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,3), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , ),
)
```

Jak funguje výše uvedeném výrazu: Pokud je uživatel Jan Macek, nejprve se pokusí vygenerovat JSmith, v případě JSmith již existuje, poté vygeneruje JoSmith, pokud existuje, který generuje JohSmith. Výraz také zajišťuje, že hodnota generovaná splňuje omezení délky a speciální znaky omezení spojená s *samAccountName*.

Viz také:

* [MID syntaxe funkce](../manage-apps/functions-for-customizing-application-data.md#mid)
* [Nahraďte syntaxe funkce](../manage-apps/functions-for-customizing-application-data.md#replace)
* [Syntaxe SelectUniqueValue – funkce](../manage-apps/functions-for-customizing-application-data.md#selectuniquevalue)

#### <a name="how-do-i-remove-characters-with-diacritics-and-convert-them-into-normal-english-alphabets"></a>Jak odebrat znaky s diakritikou a převést je na normální Anglická písmena abecedy?

Použijte funkci [NormalizeDiacritics](../manage-apps/functions-for-customizing-application-data.md#normalizediacritics) odeberte speciální znaky v křestní jméno a příjmení uživatele, při vytváření e-mailovou adresu nebo CN hodnotu pro uživatele.

## <a name="troubleshooting-tips"></a>Rady pro řešení potíží

Tato část poskytuje konkrétní pokyny týkající se řešení potíží s zřizování problémy s vaší integraci Workday pomocí protokolů auditu Azure AD a protokoly Prohlížeče událostí systému Windows Server. Je postavená na obecné kroky pro řešení potíží a koncepty zaznamenány [kurzu: Vytváření sestav o zřizování automatické uživatelských účtů](../manage-apps/check-status-user-account-provisioning.md)

Tato část zahrnuje následující aspekty řešení potíží:

* [Nastavení prohlížeče událostí Windows pro řešení potíží s agentem](#setting-up-windows-event-viewer-for-agent-troubleshooting)
* [Nastavení webu Azure portal protokoly auditu pro řešení potíží s služby](#setting-up-azure-portal-audit-logs-for-service-troubleshooting)
* [Operace vytvoření Principy protokoly pro uživatelský účet AD](#understanding-logs-for-ad-user-account-create-operations)
* [Operace aktualizace Principy protokoly pro správce](#understanding-logs-for-manager-update-operations)
* [Běžně řešení došlo k chybám](#resolving-commonly-encountered-errors)

### <a name="setting-up-windows-event-viewer-for-agent-troubleshooting"></a>Nastavení prohlížeče událostí Windows pro řešení potíží s agentem

* Připojte se k počítači systému Windows Server, které se nasadí Agent zřizování
* Otevřít **Prohlížeč událostí systému Windows Server** aplikace klasické pracovní plochy.
* Vyberte **protokoly Windows > aplikace**.
* Použití **filtrovat aktuální protokol...** možnost zobrazit všechny události zapsané podle zdroje **AAD. Connect.ProvisioningAgent** a vyloučit události s ID události "5", zadáním filtru-"5", jak je znázorněno níže.

  ![Prohlížeč událostí Windows](media/workday-inbound-tutorial/wd_event_viewer_01.png))

* Klikněte na tlačítko **OK** a seřadit výsledky zobrazit podle **datum a čas** sloupce.

### <a name="setting-up-azure-portal-audit-logs-for-service-troubleshooting"></a>Nastavení webu Azure portal protokoly auditu pro řešení potíží s služby

* Spustit [webu Azure portal](https://portal.azure.com)a přejděte **protokoly auditu** část vaší Workday zřizování aplikací.
* Použití **sloupce** tlačítko na stránce protokoly auditu chcete zobrazit pouze následující sloupce v zobrazení (data, aktivity, stav, důvod stavu). Tato konfigurace zajistí, že se můžete zaměřit pouze na data, která jsou relevantní pro řešení potíží.

  ![Sloupce protokolu auditu](media/workday-inbound-tutorial/wd_audit_logs_00.png)

* Použití **cílové** a **rozsah** parametrů k filtrování zobrazení dotazu. 
  * Nastavte **cílové** parametr "ID pracovního procesu" nebo "ID zaměstnance" dotazu objektu Workday pracovního procesu.
  * Nastavit **rozsah** příslušné časového období nad tím, které chcete prozkoumat chyby nebo problémy s přidělení přístupových práv.

  ![Filtry protokolu auditu](media/workday-inbound-tutorial/wd_audit_logs_01.png)

### <a name="understanding-logs-for-ad-user-account-create-operations"></a>Operace vytvoření Principy protokoly pro uživatelský účet AD

Když se zjistí nové zařazení ve Workday (Řekněme, že s ID zaměstnance *21023*), Azure AD, služba se pokusí vytvořit nový uživatelský účet AD pro pracovníka a v procesu zřizování vytvoří 4 záznamů protokolu auditu, jak je popsáno níže:

  [ ![Operace vytvoření protokolu auditu](media/workday-inbound-tutorial/wd_audit_logs_02.png) ](media/workday-inbound-tutorial/wd_audit_logs_02.png#lightbox)

Když kliknete na některý z záznamů protokolu auditu **podrobnosti o aktivitě** otevře se stránka. Co **podrobnosti o aktivitě** zobrazí se stránka pro jednotlivé typy záznamů protokolu.

* **WORKDAY Import** záznam: Tento záznam v protokolu zobrazuje informace pracovního procesu načtených z Workday. Pomocí informací v *další podrobnosti* část záznam protokolu k řešení potíží s načítáním dat z Workday. Příklad záznamu se zobrazí pod spolu s odkazy na tom, jak interpretovat jednotlivá pole.

  ```JSON
  ErrorCode : None  // Use the error code captured here to troubleshoot Workday issues
  EventName : EntryImportAdd // For full sync, value is "EntryImportAdd" and for delta sync, value is "EntryImport"
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID (usually the Worker ID or Employee ID field)
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the record
  ```

* **AD Import** záznam: Tento záznam v protokolu zobrazuje informace o účtu načtených ze služby AD. Jak při vytváření původního uživatele neexistuje žádný účet AD *důvod stavu aktivity* indikuje, že se nenašel žádný účet s hodnotou atributu ID pro porovnávání ve službě Active Directory. Pomocí informací v *další podrobnosti* část záznam protokolu k řešení potíží s načítáním dat z Workday. Příklad záznamu se zobrazí pod spolu s odkazy na tom, jak interpretovat jednotlivá pole.

  ```JSON
  ErrorCode : None // Use the error code captured here to troubleshoot Workday issues
  EventName : EntryImportObjectNotFound // Implies that object was not found in AD
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID
  ```

  Najít odpovídající této operaci importu AD záznamy protokolu zřizování agenta, otevřete protokoly Prohlížeče událostí Windows a používat **najít...** možnost nabídky k vyhledání položky protokolu obsahující hodnotu atributu odpovídající vlastnost ID nebo připojení (v tomto případě *21023*).

  ![Vyhledávání](media/workday-inbound-tutorial/wd_event_viewer_02.png)

  Vyhledejte položku s *ID události = 9*, který bude poskytovat LDAP vyhledávací filtr používaný agentem pro načtení účet AD. Můžete ověřit, zda se jedná o správné vyhledávací filtr pro načtení jedinečného uživatele položky.

  ![Vyhledávání protokolu LDAP](media/workday-inbound-tutorial/wd_event_viewer_03.png)

  Záznam, který následuje ji *ID události = 2* zaznamenává výsledek operace hledání a když se nevrátí žádné výsledky.

  ![Výsledky LDAP](media/workday-inbound-tutorial/wd_event_viewer_04.png)

* **Akce synchronizace pravidel** záznam: Tento záznam v protokolu zobrazuje výsledky pravidla mapování atributů a nakonfigurované filtry oborů spolu s zřizování akci, která se mají provést ke zpracování příchozích událostí Workday. Pomocí informací v *další podrobnosti* část záznam protokolu pro řešení potíží s akce synchronizace. Příklad záznamu se zobrazí pod spolu s odkazy na tom, jak interpretovat jednotlivá pole.

  ```JSON
  ErrorCode : None // Use the error code captured here to troubleshoot sync issues
  EventName : EntrySynchronizationAdd // Implies that the object will be added
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the profile in Workday
  ```

  Pokud dojde k problémům s výrazech atributů mapování nebo příchozích dat Workday má problémy (například: prázdná, nebo hodnota null pro povinné atributy), pak si můžete všimnout selhání v této fázi s kódem chyby poskytuje podrobnosti o tomto selhání.

* **AD Export** záznam: Tento záznam v protokolu zobrazuje výsledek operace vytváření účtu AD společně s hodnoty atributů, které byly nastavené v procesu. Pomocí informací v *další podrobnosti* operace vytvoření záznamu protokolu k řešení potíží s účtem. Příklad záznamu se zobrazí pod spolu s odkazy na tom, jak interpretovat jednotlivá pole. V části "Další podrobnosti o", "EventName" je nastavena na "EntryExportAdd", "JoiningProperty" je nastavena na hodnotu atributu ID pro porovnávání, "SourceAnchor" je nastavena na WorkdayID (WID) přidružené k záznamu a "TargetAnchor" je nastavena na Hodnota "ObjectGuid" AD atribut nově vytvořeného uživatele. 

  ```JSON
  ErrorCode : None // Use the error code captured here to troubleshoot AD account creation issues
  EventName : EntryExportAdd // Implies that object will be created
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the profile in Workday
  TargetAnchor : 83f0156c-3222-407e-939c-56677831d525 // set to the value of the AD "objectGuid" attribute of the new user
  ```

  Najít odpovídající touto operací exportu AD záznamy protokolu zřizování agenta, otevřete protokoly Prohlížeče událostí Windows a používat **najít...** možnost nabídky k vyhledání položky protokolu obsahující hodnotu atributu odpovídající vlastnost ID nebo připojení (v tomto případě *21023*).  

  Vyhledejte záznam HTTP POST odpovídající časová razítka operace exportu se *ID události = 2*. Tento záznam bude obsahovat hodnoty atributů, zřizovací služba odesílat agentem zřizování.

  [![Přidat SCIM](media/workday-inbound-tutorial/wd_event_viewer_05.png)](media/workday-inbound-tutorial/wd_event_viewer_05.png#lightbox)

  Okamžitě po výše uvedené události měla by existovat jiné události, která zachytává odpovědi vytvořit operace účtu AD. Tato událost vrátí nový objectGuid vytvořený ve službě AD a je nastavená na atribut TargetAnchor ve zřizovací službě.

  [![Přidat SCIM](media/workday-inbound-tutorial/wd_event_viewer_06.png)](media/workday-inbound-tutorial/wd_event_viewer_06.png#lightbox)

### <a name="understanding-logs-for-manager-update-operations"></a>Vysvětlení protokolů pro operace update manager

Atribut manager je ve službě AD atribut typu odkaz. Zřizovací služba nenastaví atribut manager jako součást operace vytváření uživatele. Místo toho je nastaven atribut manager jako součást *aktualizovat* operaci po vytvoření účtu služby AD pro uživatele. Rozbalení výše uvedený příklad, Řekněme, že nové zařazení s ID zaměstnance "21451" se aktivuje ve Workday a nové zařazení správce (*21023*) už má účet AD. V tomto scénáři prohledávání protokolů auditu pro uživatele 21451 zobrazuje 5 položek.

  [ ![Aktualizace Správce](media/workday-inbound-tutorial/wd_audit_logs_03.png) ](media/workday-inbound-tutorial/wd_audit_logs_03.png#lightbox)

První 4 záznamy jsou jako ty, které Prozkoumali jsme uživatele v rámci operace vytvoření. 5. záznam je export spojené s atributem update manager. Záznam protokolu zobrazuje výsledek AD účet správce aktualizace operace, která se provádí pomocí manažera *objectGuid* atribut.

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

### <a name="resolving-commonly-encountered-errors"></a>Běžně řešení došlo k chybám

Tato část popisuje běžně zobrazené chyby zřizování uživatelů Workday a o tom, jak problém vyřešit. Chyby jsou seskupené, následujícím způsobem:

* [Chyby zřizování agenta](#provisioning-agent-errors)
* [Chyby připojení](#connectivity-errors)
* [Chyby při vytváření účtu uživatele AD](#ad-user-account-creation-errors)
* [Chyby aktualizace účtu uživatele AD](#ad-user-account-update-errors)

#### <a name="provisioning-agent-errors"></a>Chyby zřizování agenta

|#|Chyba scénář |Možných příčin|Doporučené řešení|
|--|---|---|---|
|1.| Chyba při instalaci agentem zřizování s chybovou zprávou:  *Služby "Microsoft Agent Azure AD Connect zřizování" (AADConnectProvisioningAgent) se nepovedlo spustit. Ověřte, zda máte dostatečná oprávnění ke spuštění systému.* | Tato chyba obvykle zobrazí pokud se pokoušíte nainstalovat zřizování agenta v řadiči domény a zásad skupiny brání službě ve spuštění.  Také se dochází, pokud máte předchozí verzi agenta spuštěného a neodinstalovali před zahájením nové instalace.| Zřizování agenta nainstalujte na server – řadič domény. Ujistěte se, že jsou před instalací nového agenta odinstalovat předchozí verze agenta.|
|2.| Služba Windows 'Microsoft Agent Azure AD Connect zřizování' je v *počáteční* stavu a přepnutí do *systémem* stavu. | Jako součást instalace agenta Průvodce vytvoří místní účet (**NT Service\\AADConnectProvisioningAgent**) na serveru a to je **přihlášení** účet použitý pro spuštění Služba. Pokud zásady zabezpečení na serveru Windows zabrání spuštění služby místním účtům, dojde k této chybě. | Otevřít *konzoly služby*. Klikněte pravým tlačítkem na službu Windows "Microsoft Agent Azure AD Connect zřizování" a na kartě přihlášení zadejte účet správce domény, na kterém běží služba. Restartujte službu. |
|3.| Při konfiguraci zřizování agenta v doméně AD v kroku *připojení služby Active Directory*, Průvodce trvá dlouhou dobu pokusu o načtení schématu AD a časem i vypršení časového. | Tato chyba obvykle zobrazí-li průvodce není schopen obraťte se na serveru řadiče domény služby AD kvůli problémům s bránou firewall. | Na *připojení služby Active Directory* obrazovce Průvodce při zadávání přihlašovacích údajů pro vaši doménu AD, je možné volat *vyberte prioritu řadič domény*. Tuto možnost použijte k výběru řadiče domény, který je ve stejné lokalitě jako agent serveru a ujistěte se, že neexistují žádná pravidla firewallu neblokují komunikaci. |

#### <a name="connectivity-errors"></a>Chyby připojení

Pokud zřizovací služba se nemůže připojit k Workday nebo Active Directory, mohlo by dojít ke zřizování přejde do stavu v karanténě. Následující tabulku použijte k řešení problémů s připojením.

|#|Chyba scénář |Možných příčin|Doporučené řešení|
|--|---|---|---|
|1.| Po kliknutí na **Test připojení**, zobrazí se chybová zpráva: *Došlo k chybě připojení k službě Active Directory. Ujistěte se prosím, že je spuštěn Agent zřizování v místním a má nakonfigurovanou správnou doménu služby Active Directory.* | Tato chyba obvykle zobrazí if zřizování agenta není spuštěný nebo je brána firewall blokuje komunikaci mezi službami Azure AD a agentem zřizování. Může také uvidíte tuto chybu, pokud doména není nakonfigurována v Průvodci agenta. | Otevřít *služby* konzolu na serveru Windows k potvrzení, že je agent spuštěn. Otevřete Průvodce zřizování agenta a potvrďte, že je správné domény zaregistrovaný s agentem.  |
|2.| Zřizování úloha přejde do stavu karantény přes víkendy (pracovní dny po ne) a dostaneme e-mailové oznámení, že dojde k chybě se synchronizací. | Jednou z běžných příčin této chyby je plánované výpadky Workday. Pokud používáte klienta Workday implementace, mějte prosím na paměti, že Workday je naplánováno časové prodlevy pro jeho implementaci tenanty prostřednictvím víkendy (obvykle z pátku soboty) a během tohoto období Workday zřizování aplikací můžou směřovat do umístit do karantény stavu, protože není možné se připojit k Workday. Získá zpět do normálního stavu po implementaci klienta Workday je zpátky do online režimu. Ve výjimečných případech může také uvidíte tuto chybu, pokud heslo uživatele systému integrace změněna z důvodu aktualizace tenanta nebo pokud obsahuje příslušný účet uzamčen nebo vypršení platnosti stavu. | Obraťte se na svého partnera Workday správce nebo integrace zobrazíte, když Workday naplánuje výpadek oznámení ignorovat během doby výpadku a zkontrolujte dostupnost po Workday instance je zpátky do online režimu.  |


#### <a name="ad-user-account-creation-errors"></a>Chyby při vytváření účtu uživatele AD

|#|Chyba scénář |Možných příčin|Doporučené řešení|
|--|---|---|---|
|1.| Export selhání operace v protokolu auditu se zprávou *Chyba: OperationsError SvcErr: Došlo k chybě operace. Žádný odkaz na dokonalá není nakonfigurovaná adresářové služby. Adresářová služba je proto nelze vydat odkazy na objekty mimo tato doménová struktura.* | Tato chyba se obvykle zobrazí if *kontejner služby Active Directory* organizační jednotce není správně nastavena nebo pokud dojde k problémům s mapování výraz používá pro *parentDistinguishedName*. | Zkontrolujte *kontejner služby Active Directory* parametr organizační jednotku pro překlepy. Pokud používáte *parentDistinguishedName* v mapování atributů Ujistěte se, že se vždycky vyhodnotí jako známé kontejner v rámci domény AD. Zkontrolujte, *exportovat* protokoly událostí auditu zobrazíte vygenerovanou hodnotu. |
|2.| Exportujte selhání operace v protokolu auditu s kódem chyby: *SystemForCrossDomainIdentityManagementBadResponse* a zpráva *Chyba: ConstraintViolation-AtrErr: Hodnota v požadavku není platná. Hodnota pro atribut nebyl v přípustném rozsahu hodnot. Podrobnosti o \nError: CONSTRAINT_ATT_TYPE - společnosti*. | Zatímco tato chyba je specifické pro *společnosti* atributu, se zobrazí tato chyba dalších vlastností, jako je *CN* také. Tato chyba se zobrazí z důvodu omezení schématu vynucená AD. Ve výchozím nastavení, jako jsou atributy *společnosti* a *CN* ve službě AD mají maximální limit 64 znaků. Pokud je hodnota pocházející z Workday víc než 64 znaků, se zobrazí tato chybová zpráva. | Zkontrolujte *exportovat* události z protokolů auditu a zobrazit tak hodnotu pro atribut hlášené v chybové zprávě. Vezměte v úvahu zkracování hodnoty pocházejí z Workday pomocí [Mid](../manage-apps/functions-for-customizing-application-data.md#mid) funkce nebo změnit mapování na atribut AD, který nemá podobné omezení délky.  |

#### <a name="ad-user-account-update-errors"></a>Chyby aktualizace účtu uživatele AD

Během procesu aktualizace účtu uživatele AD zřizovací Služba čte informace z Workday a AD, spouští atribut pravidla mapování a určuje, pokud při jakékoli změně potřeb se projeví. Odpovídajícím způsobem se aktivuje událost aktualizace. Pokud kterýkoli z těchto kroků dojde k selhání, je zaznamenána v protokolech auditování. Řešení potíží s běžnými chybami aktualizací použijte následující tabulku.

|#|Chyba scénář |Možných příčin|Doporučené řešení|
|--|---|---|---|
|1.| Selhání akce synchronizace pravidel v protokolu auditu se zprávou *položkou EventName = EntrySynchronizationError a ErrorCode = EndpointUnavailable*. | Tato chyba zobrazí, pokud zřizovací služby nelze načíst data profilu uživatele ze služby Active Directory kvůli chybě zpracování se tak v místním agentem zřizování. | V protokolech prohlížeče událostí zřizování agenta pro chybové události, které signalizují potíže s operace čtení (filtrovat podle ID události č. 2). |
|2.| Atribut manager ve službě AD neaktualizuje pro některé uživatele ve službě AD. | Nejpravděpodobnější příčinou této chyby je, pokud používáte pravidel stanovení rozsahu inicializací a manažer uživatele není součástí rozsahu. Může také narazíte na potíže Pokud manažera odpovídající ID atributu (např. EmployeeID) nebyl nalezen v cílové doméně AD nebo není nastavený na správnou hodnotu. | Zkontrolujte filtr oborů a přidejte uživatele správce v oboru. Zkontrolujte profil daného manažera ve službě AD, abyste měli jistotu, že je hodnota atributu odpovídající ID. |

## <a name="managing-your-configuration"></a>Správa konfigurace

Tato část popisuje, jak můžete dále rozšířit, přizpůsobení a správa konfigurace zřizování uživatelů řízené Workday. To zahrnuje následující témata:

* [Přizpůsobení seznamu atributů uživatelů Workday](#customizing-the-list-of-workday-user-attributes)  
* [Export a import konfigurace](#exporting-and-importing-your-configuration)

### <a name="customizing-the-list-of-workday-user-attributes"></a>Přizpůsobení seznamu atributů uživatelů Workday

Workday zřizování aplikací pro služby Active Directory a Azure AD, které obsahují výchozí seznam atributů uživatelů Workday můžete vybírat. Tyto seznamy však nejsou komplexní. WORKDAY podporuje stovky uživatelské atributy, které může být standardní nebo jedinečné pro vašeho tenanta Workday.

Služba zřizování Azure AD podporuje možnost přizpůsobit si seznam nebo atribut Workday zahrnout všechny atributy v [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) operace rozhraní API pro lidské zdroje.

Chcete-li provést tuto změnu, je nutné použít [Workday Studio](https://community.workday.com/studio-download) extrahovat výrazy XPath, které reprezentují atributy, které chcete použít a pak je přidejte do vaší konfigurace zřizování pomocí editoru rozšířeného atributu na webu Azure Portal .

**K načtení výraz XPath pro atributů uživatelů Workday:**

1. Stáhněte a nainstalujte [Workday Studio](https://community.workday.com/studio-download). Budete potřebovat účet Workday komunity pro přístup k Instalační služby.

2. Stáhněte si soubor Workday Human_Resources WSDL z této adresy URL: https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Human_Resources.wsdl

3. Spusťte Workday Studio.

4. Na panelu příkazů vyberte **Workday > Test webové služby v testování** možnost.

5. Vyberte **externí**a vyberte soubor Human_Resources WSDL jste si stáhli v kroku 2.

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio1.png)

6. Nastavte **umístění** pole `https://IMPL-CC.workday.com/ccx/service/TENANT/Human_Resources`, ale "IMPL-CC" nahraďte skutečným typ instance a "TENANT" s vaším jménem skutečné tenanta.

7. Nastavte **operace** k **Get_Workers**

8.  Klikněte na malé **konfigurace** odkaz podokna požadavku nebo odpovědi k nastavení pověření Workday. Zkontrolujte **ověřování**a pak zadejte uživatelské jméno a heslo pro váš účet integrace systému Workday. Je potřeba formátovat jako uživatelské jméno name@tenanta nechat **WS-Security UsernameToken** zaškrtnutou možnost.

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio2.png)

9. Vyberte **OK**.

10. V **žádosti** podokně Vložit v souboru XML níže a nastavte **Employee_ID** ID zaměstnance skutečné uživatele ve vašem tenantovi Workday. Vyberte uživatele, který má atribut vyplní, který chcete extrahovat.

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

11. Klikněte na tlačítko **odeslat požadavek** (zelená šipka) ke spuštění příkazu. Pokud úspěšné, odpověď by se měla objevit v **odpovědi** podokně. Zkontrolujte odpovědi k zajištění, že má data uživatelské ID, které jste zadali a ne o chybu.

12. V případě úspěchu, zkopírujte data XML z **odpovědi** podokně a uložte ho jako soubor XML.

13. Vyberte v nástroje příkazového řádku z Workday Studio se **soubor > Otevřít soubor...**  a otevřete soubor XML, který jste uložili. Tato akce otevře soubor v editoru Workday Studio XML.

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio3.png)

14. Ve stromové struktuře souborů Procházet   **/env: Obálka > env: Text > wd:Get_Workers_Response > wd:Response_Data > wd: Pracovník** najít vaše uživatelská data.

15. V části **wd: Pracovník**vyhledejte atributu, který chcete přidat a vyberte ji.

16. Zkopírujte výraz XPath pro váš vybraný atribut z celkového počtu **cesta k dokumentu** pole.

17. Odeberte **/env:Envelope / env / wd:Get_Workers_Response / wd:Response_Data /** předpony ze zkopírovaného výrazu.

18. Pokud se poslední položky zkopírované výrazu je uzel (Příklad: "/ wd: Birth_Date"), potom připojte **/text()** na konci výrazu. Toto není nutné v případě, že poslední položka je atribut (Příklad: "/@wd: typu").

19. Výsledek by měl vypadat `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`. Tato hodnota se budou kopírovat do portálu Azure portal.

**Přidání vaší vlastní atribut uživatele Workday do vaší konfigurace zřizování:**

1. Spusťte [webu Azure portal](https://portal.azure.com)a přejděte do části zřizování z Workday, vaší aplikací, zřizování, jak je popsáno dříve v tomto kurzu.

2. Nastavte **stavu zřizování** k **vypnout**a vyberte **Uložit**. Tento krok vám pomůže zajistit, že vaše změny se projeví pouze v případě, že jste připraveni.

3. V části **mapování**vyberte **synchronizovat pracovníkům Workday v místní službě Active Directory** (nebo **synchronizaci pracovních procesů Workday do služby Azure AD**).

4. Posuňte se dolů na další obrazovku a vyberte **zobrazit pokročilé možnosti**.

5. Vyberte **upravit seznam atributů pro Workday**.

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio_aad1.png)

6. Přejděte do dolní části seznamu atributů k umístění vstupních polí.

7. Pro **název**, zadejte zobrazovaný název pro atribut.

8. Pro **typ**, vyberte typ, který odpovídajícím způsobem odpovídá atributu (**řetězec** klienty jsou nejčastěji).

9. Pro **výraz rozhraní API**, zadejte výraz XPath, který jste zkopírovali z Workday Studio. Příklad: `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`

10. Vyberte **přidejte atribut**.

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio_aad2.png)

11. Vyberte **Uložit** výše a potom **Ano** do dialogového okna. Uzavření obrazovky mapování atributů, pokud je stále otevřen.

12. Zpět na hlavní **zřizování** kartu, vyberte možnost **synchronizovat pracovníkům Workday v místní službě Active Directory** (nebo **synchronizaci pracovních procesů do Azure AD**) znovu.

13. Vyberte **přidat nové mapování**.

14. Nový atribut by se měla objevit v **zdrojový atribut** seznamu.

15. Podle potřeby přidejte mapování pro nový atribut.

16. Až budete hotovi, nezapomeňte nastavit **stavu zřizování** zpět **na** a uložit.

### <a name="exporting-and-importing-your-configuration"></a>Export a import konfigurace

Tato část popisuje, jak použít Microsoft Graph API a Graph Exploreru k exportu zřizování Workday mapování atributů a schéma do souboru JSON a naimportovat ho zpátky do služby Azure AD.

#### <a name="step-1-retrieve-your-workday-provisioning-app-service-principal-id-object-id"></a>Krok 1: Načíst vaše Workday ID zřizování instanční objekt služby aplikace (ID objektu)

1. Spusťte [webu Azure portal](https://portal.azure.com)a přejděte do sekce Properties vaší Workday zřizování aplikací.
1. V části Vlastnosti zřizování aplikace zkopírujte hodnotu identifikátoru GUID přidružený *ID objektu* pole. Tato hodnota se také nazývá **ServicePrincipalId** a vaše aplikace se použije v Graph Exploreru operace.

   ![ID instančního objektu aplikace WORKDAY](./media/workday-inbound-tutorial/wd_export_01.png)

#### <a name="step-2-sign-into-microsoft-graph-explorer"></a>Krok 2: Přihlaste se k Microsoft Graph Exploreru

1. Spusťte [Microsoft Graph Exploreru](https://developer.microsoft.com/graph/graph-explorer)
1. Klikněte na tlačítko "Přihlásit se společnost Microsoft" a přihlášení pomocí přihlašovacích údajů Azure AD globálního správce nebo správce aplikace.

    ![Přihlášení grafu](./media/workday-inbound-tutorial/wd_export_02.png)

1. Po úspěšném přihlášení zobrazí se podrobnosti uživatelského účtu v levém podokně.

#### <a name="step-3-retrieve-the-provisioning-job-id-of-the-workday-provisioning-app"></a>Krok 3: Načtení ID úlohy zřizování Workday zřizování aplikací

V aplikaci Microsoft Graph Explorer, spuštěním následujícího dotazu GET nahrazení [servicePrincipalId] s **ServicePrincipalId** extrahují z [kroku 1](#step-1-retrieve-your-workday-provisioning-app-service-principal-id-object-id).

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs
```

Odpověď se zobrazí, jak je znázorněno níže. Zkopírujte atribut"id" k dispozici v odpovědi. Tato hodnota je **ProvisioningJobId** a bude použit pro načtení základní metadata schématu.

   [![Zřízení Id úlohy](./media/workday-inbound-tutorial/wd_export_03.png)](./media/workday-inbound-tutorial/wd_export_03.png#lightbox)

#### <a name="step-4-download-the-provisioning-schema"></a>Krok 4: Stáhněte si zřizování schématu

V aplikaci Microsoft Graph Explorer spusťte následující dotaz GET, nahraďte [servicePrincipalId] a [ProvisioningJobId] s ServicePrincipalId a ProvisioningJobId načíst v předchozích krocích.

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

Kopírování objektu JSON z odpovědi a uložte ho do souboru k vytvoření zálohy schématu.

#### <a name="step-5-import-the-provisioning-schema"></a>Krok 5: Importovat schéma zřizování

> [!CAUTION]
> Tento krok proveďte jenom v případě, že budete muset upravit schéma pro konfiguraci, kterou nelze změnit pomocí webu Azure portal nebo pokud budete potřebovat obnovit konfiguraci z dříve zálohovaný soubor s platnou a pracovní schématu.

V aplikaci Microsoft Graph Explorer, nakonfigurujte následující dotaz PUT, nahraďte [servicePrincipalId] a [ProvisioningJobId] ServicePrincipalId a ProvisioningJobId načíst v předchozích krocích.

```http
    PUT https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

Na kartě "Tělo požadavku" zkopírujte obsah souboru schématu JSON.

   [![Text žádosti](./media/workday-inbound-tutorial/wd_export_04.png)](./media/workday-inbound-tutorial/wd_export_04.png#lightbox)

Na kartě "Záhlaví požadavku" přidáte atribut hlavička Content-Type s hodnotou "application/json"

   [![Hlavičky žádosti](./media/workday-inbound-tutorial/wd_export_05.png)](./media/workday-inbound-tutorial/wd_export_05.png#lightbox)

Klikněte na tlačítko "Spustit dotaz" Chcete-li importovat nové schéma.

## <a name="managing-personal-data"></a>Správa osobních údajů

Workday zřizování řešení pro službu Active Directory vyžaduje instalaci na Windows serveru v místním zřizování agenta a Tento agent vytváří protokoly v protokolu událostí Windows, který může obsahovat osobní údaje v závislosti na vaší Workday do atributu AD mapování. Pro dosažení souladu se závazky ochrany osobních údajů uživatele, můžete zajistit, že žádná data se uchovávají v případě, že protokoly za 48 hodin nastavením Windows naplánovaná úloha vymazání protokolu událostí.

Služba zřizování Azure AD, které patří do **zpracovatelem** kategorie klasifikace podle nařízení GDPR. Jako zpracovatelem kanál služba poskytuje služby pro zpracování dat klíče partnerů a spotřebitelů end. Služba zřizování Azure AD negeneruje uživatelská data a nemá žádnou kontrolu nezávislé, jaké osobní data se shromažďují a jak se používají. Načítání dat, agregace, analýzy a generování sestav ve službě Azure AD služby zřizování jsou založeny na stávajících datech enterprise.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-hybrid-note.md)]

S ohledem na uchovávání dat služba zřizování Azure AD nepodporuje generování sestav, provádět analýzy ani ukázala po uplynutí 30 dnů. Služba zřizování Azure AD, proto není ukládají, zpracovávají nebo zachovat žádná data se po uplynutí 30 dnů. Tento návrh je kompatibilní s nařízení GDPR, Microsoft o ochraně osobních údajů dodržování předpisů a zásad uchovávání dat služby Azure AD.

## <a name="next-steps"></a>Další postup

* [Zjistěte, jak kontrolovat protokoly a získat sestavy o zřizování aktivity](../manage-apps/check-status-user-account-provisioning.md)
* [Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Workday a Azure Active Directory](workday-tutorial.md)
* [Naučte se integrovat s Azure Active Directory dalším aplikacím SaaS](tutorial-list.md)
* [Další informace o použití rozhraní Microsoft Graph API ke správě konfigurace zřizování](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)
