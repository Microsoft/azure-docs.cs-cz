---
title: Contoso – nastavení infrastruktury migrace | Dokumentace Microsoftu
description: Zjistěte, jak společnosti Contoso nastaví infrastruktury Azure pro migraci do Azure.
services: azure-migrate
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 06/19/2018
ms.author: raynew
ms.openlocfilehash: bf1406c8e361e0a1433b0e26c477c3c34e987fcf
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38562754"
---
# <a name="contoso---deploy-a-migration-infrastructure"></a>Contoso – nasazení infrastruktury migrace

Tento článek zkoumá, jak společnosti Contoso nastaví místní a infrastrukturu Azure v rámci přípravy na migraci do Azure a vedení firmy v hybridním prostředí.

- Je ukázkové architektury, které jsou specifické pro společnost Contoso.
- Ať už budete potřebovat všechny prvky, které jsou popsané v článku, závisí na strategii migrace. Například pokud vytváříte jen aplikace nativní pro cloud v Azure, může být nutné strukturu méně složité sítě.

Tento dokument je druhý sérii článků, které dokumentu jak fiktivní společnosti, který se migruje Contoso místních prostředků do cloudu Microsoft Azure. Obsahuje základní informace a sadu scénáře nasazení, která ukazuje, jak nastavit infrastrukturu migrace vyhodnotit vhodnost migrace místních prostředků a spouštět různé druhy migrace. Scénáře jejich složitost v a budeme přidávat další články v čase.

**Článek** | **Podrobnosti** | **Stav**
--- | --- | ---
[Článek 1: Přehled](contoso-migration-overview.md) | Poskytuje přehled strategie migrace společnosti Contoso, článek řady a ukázkové aplikace, které používáme. | K dispozici.
Článek 2: Nasazení infrastruktury Azure (Tento článek) | Popisuje, jak společnosti Contoso připraví jeho místní a infrastrukturu Azure na migraci. Stejnou infrastrukturu se používá pro všechny scénáře migrace Contoso. | K dispozici.
[Článek 3: Posouzení místních prostředků](contoso-migration-assessment.md) | Ukazuje, jak společnosti Contoso spuštění posouzení jejich místní dvouvrstvé SmartHotel aplikace spuštěné ve VMware. Jejich posouzení virtuálních počítačů aplikace s [Azure Migrate](migrate-overview.md) služby a databáze aplikace SQL serveru s [Azure Database Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | K dispozici.
[Článek 4: Metody opětovného hostování virtuálních počítačů Azure a spravované Instance SQL](contoso-migration-rehost-vm-sql-managed-instance.md) | Ukazuje, jak společnosti Contoso migruje SmartHotel aplikace do Azure. Migraci virtuálního počítače front-endu aplikace pomocí [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview)a aplikace pomocí databáze [Azure Database Migration](https://docs.microsoft.com/azure/dms/dms-overview) služby k migraci do spravované Instance SQL. | K dispozici.
[Článek 5: Změna hostitele na virtuálních počítačích Azure](contoso-migration-rehost-vm.md) | Ukazuje, jak migrovat své aplikace SmartHotel virtuálních počítačů pomocí Site Recovery pouze Contoso.
[Článek 6: Změna hostitele na virtuálních počítačích Azure a skupiny dostupnosti SQL serveru](contoso-migration-rehost-vm-sql-ag.md) | Ukazuje, jak společnosti Contoso migruje SmartHotel aplikace. Používají Site Recovery k migraci aplikace, virtuální počítače a Database Migration service, jak migrovat databázi aplikace do skupiny dostupnosti SQL serveru. | K dispozici.
[Článek 7: Změna hostitele Linuxovou aplikaci do virtuálních počítačů Azure](contoso-migration-rehost-linux-vm.md) | Ukazuje, jak společnosti Contoso migruje Linuxovou aplikaci osTicket na virtuální počítače Azure. | K dispozici.
[Článek 8: Změna hostitele Linuxovou aplikaci na virtuálních počítačích Azure a Azure serveru MySQL](contoso-migration-rehost-linux-vm-mysql.md) | Ukazuje, jak společnosti Contoso migruje Linux osTicket aplikace pomocí Site Recovery a aplikace MySQL Workbench k migraci (zálohování a obnovení) na instanci serveru Azure MySQL. | K dispozici.

V tomto článku Contoso nastavit všechny prvky infrastruktury, které potřebují k dokončení scénáře migrace. 


## <a name="overview"></a>Přehled

Předtím, než že můžete migrovat do Azure, je velmi důležité, že Contoso připravit infrastrukturu.  Obecně platí jsou pěti široké oblastí, které potřebují k zamyslet:

**Krok 1: Předplatná Azure**: jak se při nákupu Azure a komunikovat s platformou Azure a službami?  
**Krok 2: Hybridní identita**: jak budete spravovat a řídit přístup k místním a prostředky Azure po migraci? Jak se rozšířit nebo přesunout správy identit do cloudu?  
**Krok 3: Zotavení po havárii a odolnost**: jak se zajistí, že jejich aplikace a infrastruktury jsou odolné výpadků a havárií dojde-li?  
**Krok 4: Sítě**: jak by měl návrh síťové infrastruktury a navázat připojení mezi místním datacentrem a Azure?  
**Krok 5: Zabezpečení**: jak se jejich zabezpečení jejich nasazení hybridní nebo Azure?  
**Krok 6: Zásad správného řízení**: jak se udržují v souladu s požadavky na zabezpečení a zásad správného řízení nasazení?

## <a name="before-you-start"></a>Než začnete

Než začneme, prohlížení infrastrukturu, můžete chtít načíst některé základní informace o možnosti Azure, které si popíšeme v tomto článku:

- K dispozici řadu možností pro zakoupení Azure přístup, včetně s průběžnými platbami, smlouvy Enterprise (EA) nebo programu Open Licensing od prodejců v systému Microsoft nebo z Microsoft Partners znát pod některým z poskytovatele Cloud Solution Provider (CSP). Další informace o [možnosti nákupu](https://azure.microsoft.com/pricing/purchase-options/)a přečtěte si, jak [předplatná Azure jsou uspořádané](https://azure.microsoft.com/blog/organizing-subscriptions-and-resource-groups-within-the-enterprise/).
- Získejte přehled o Azure [správu identit a přístupu](https://www.microsoft.com/en-us/trustcenter/security/identity). Zejména, přečtěte si o [Azure AD a rozšíření místní služby AD do cloudu](https://docs.microsoft.com/azure/active-directory/identity-fundamentals). Je užitečné ke stažení e knihy o [Správa identit a přístupu (IAM) v hybridním prostředí](https://azure.microsoft.com/resources/hybrid-cloud-identity/).
- Azure poskytuje robustní síťová infrastruktura s možnostmi pro hybridní připojení. Získejte přehled o [sítě a sítě řízení přístupu](https://docs.microsoft.com/azure/security/security-network-overview).
- Úvod k [zabezpečení Azure](https://docs.microsoft.com/azure/security/azure-security)a přečtěte si informace o vytváření plánu pro [zásad správného řízení](https://docs.microsoft.com/azure/security/governance-in-azure).


## <a name="on-premises-architecture"></a>Místní architektury

Zde je diagram znázorňující aktuální místní infrastrukturou společnosti Contoso.

 ![Architektura contoso](./media/contoso-migration-infrastructure/contoso-architecture.png)  

- Contoso má jeden hlavní datové centrum, které jsou umístěné v města New York východní USA.
- Mají tři další místní větve ve Spojených státech amerických.
- Hlavní datové centrum je připojený k Internetu pomocí fiber metro ethernetová připojení (500 MB/s).
- Každou větev je místně připojen k Internetu pomocí obchodní třídy připojení s IPSec VPN tunely zpět do hlavního datového centra. To umožňuje jejich celá síť trvale připojený k Internetu a optimalizuje připojení k Internetu.
- Hlavní datové centrum je plně virtualizovaný s VMware. Mají dva virtualizace hostitele ESXi 6.5 spravované přes vCenter Server verze 6.5.
- Contoso používá službu Active Directory pro správu identit a servery DNS v interní síti.
- Běží na virtuálních počítačích VMware řadiče domény v datovém centru. Řadiče domény v místní větve spustit na fyzických serverech.


## <a name="step-1-buy-and-subscribe-to-azure"></a>Krok 1: Koupit a přihlášení k odběru do Azure

Contoso potřebuje zjistit, jak koupit Azure, navrhovat předplatná a licence služby a prostředky.

### <a name="buy-azure"></a>Koupit Azure

Contoso jsou dál [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/). To zahrnuje zavážou k využívání služeb Azure, opravňující jim získají skvělé výhody – včetně flexibilních možností fakturace a optimalizované ceny.

- Odhadovaná Contoso, co se bude jejich roční náklady na Azure. Při jejich podepsání smlouvy, placené první rok v plném rozsahu.
- Contoso je potřeba použít všechny své závazky dřív, než skončí v roce, nebo budete dojít ke ztrátě hodnoty těchto dolarů.
- Pokud z nějakého důvodu by být delší než jejich počáteční závazek a věnovat víc, Microsoft je fakturaci pro rozdíl.
- Žádné náklady vynaložené výše závazku bude účtovat stejným sazby a programů v jejich smlouvy. Neexistují žádné následky nad.

### <a name="manage-subscriptions"></a>Správa předplatných

Po platit za Azure, třeba Contoso zjistit, jak spravovat svá předplatná. Mají smlouvu EA, a proto neomezený počet předplatných Azure, můžete nastavit.

- Registrace Azure Enterprise definuje způsob, jakým společnosti tvar a používá služby Azure a definuje strukturu zásad správného řízení core.
- Jako první krok Contoso určili struktuře (označované jako vygenerované uživatelské rozhraní enterprise k registraci jejich organizace. Používají [v tomto článku](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-manager-subscription-governance) aby to pomohl ostatním pochopit a navrhnout vygenerované uživatelské rozhraní.
- Prozatím se rozhodl používat funkční přístup ke správě předplatných Contoso.
    - Uvnitř jejich enterprise mají jednoho oddělení IT, která určuje rozpočet Azure. Toto bude jedinou skupinu s předplatnými.
    - Tento model se bude rozšíření v budoucnu, tak, aby další podnikové skupiny se může připojit k jako oddělením ve podnikového zápisu.
    - V IT oddělení společnosti Contoso má strukturované dva odběry, produkcí a vývojem.
    - Pokud Contoso vyžaduje další předplatná v budoucnu, budete potřebovat ke správě přístupu, zásady a dodržování předpisů u těchto předplatných. Budou moct udělat zavedením [skupin pro správu Azure](https://docs.microsoft.com/azure/azure-resource-manager/management-groups-overview), jako další vrstvu nad předplatných.

    ![Struktura organizace](./media/contoso-migration-infrastructure/enterprise-structure.png) 

### <a name="examine-licensing"></a>Prozkoumejte licencování

S předplatnými nakonfigurovaný můžete si prohlédnout Contoso svých programů společnosti Microsoft. Jejich strategie správy licencí závisí na prostředky, které chcete migrovat do Azure a jak jsou vybrané a nasadit virtuální počítače Azure a služby. 

#### <a name="azure-hybrid-benefit"></a>Zvýhodněné hybridní využití Azure

Při nasazování virtuálních počítačů v Azure, standardní Image zahrnují licenci, která se bude účtovat Contoso po minutách software používaný. Ale Contoso byl dlouhodobé zákazníků společnosti Microsoft a má udržuje EAs a otevřete licencí s programem software assurance (SA). 

Zvýhodněné hybridní využití Azure poskytuje nákladově efektivní metodu pro migraci Contoso, neboť jim umožňuje ušetřit na virtuálních počítačích Azure a SQL Server úloh tím, že převod nebo opakovaného použití v systému Windows Server Datacenter a Standard edition licence kryté službou Software Assurance. To vám umožní Contoso nižší závislosti výpočetní sazbu za virtuální počítače a serveru SQL Server. [Další informace](https://azure.microsoft.com/pricing/hybrid-benefit/).


#### <a name="license-mobility"></a>License Mobility

Mobilitu licencí v rámci programu SA poskytuje zákazníkům multilicenčních programů společnosti Microsoft, například Contoso flexibilitu při nasazování nárokovatelných serverových aplikací s aktivním programem SA v Azure. Tím se eliminuje potřeba kupovat nové licence. S žádné poplatky za související s mobilitou můžete stávající licence snadno nasadit v Azure. [Další informace](https://azure.microsoft.com/pricing/license-mobility/).

#### <a name="reserve-instances-for-predictable-workloads"></a>Rezervované instance pro úlohy s předvídatelným

Předvídatelné úlohy jsou ty, které vždy musí být k dispozici s virtuálními počítači spuštěná. Například řádek obchodní aplikace například systém SAP ERP.  Na druhé straně nepředvídatelnými úlohami jsou ty, které jsou proměnné. Například virtuální počítače, které jsou vysoce poptávky a vypnout v době mimo špičku.

![Rezervovaná instance](./media/contoso-migration-infrastructure/reserved-instance.png) 

Výměnou za použití rezervované instance pro konkrétní instance virtuálního počítače, o kterých ví, třeba udržovat velké dob trvání doby, konzoly můžete získat slevu i kapacitu s určenou prioritou. Pomocí [Azure Reserved Instances](https://azure.microsoft.com/pricing/reserved-vm-instances/)společně s programem Azure Hybrid Benefit Contoso můžete ušetřit až 82 % slevu regulární s průběžnými platbami ceny (duben 2018).


## <a name="step-2-manage-hybrid-identity"></a>Krok 2: Správa hybridní identita

Poskytuje tak a řízení přístupu uživatelů k prostředkům Azure pomocí správy identit a přístupu (IAM) je důležitým krokem při přijímání změn dohromady infrastrukturou Azure.  

- Contoso rozhodnout o svojí místní službě Active Directory rozšířit do cloudu, místo vytvoření nového samostatného systému v Azure.
- Vytvoří služby založené na Azure Active Directory k tomu.
- Contoso nemají Office 365 na místě, proto je nutné zřizovat nové služby Azure AD.
- Office 365 používá Azure AD pro správu uživatelů. Pokud Contoso používali Office 365, by se už máte Princip Azure AD a použít je jako svoje primární AD.
- [Další informace](https://support.office.com/article/understanding-office-365-identity-and-azure-active-directory-06a189e7-5ec6-4af2-94bf-a22ea225a7a9) o službě Azure AD pro Office 365 a další [jak přidat předplatné](https://docs.microsoft.com/azure/active-directory/active-directory-how-subscriptions-associated-directory) do existující služby Azure AD.

### <a name="create-an-azure-ad"></a>Vytvoření Azure AD

Contoso se pomocí bezplatné edice Azure AD, který je součástí s předplatným Azure. Přidejte nový adresář AD následujícím způsobem:

1. V [webu Azure portal](http://portal.azure.com/), Contoso přejít na **vytvořit prostředek** > **Identity** > **Azure Active Directory**.
2. V **vytvořit adresář**, určí název adresáře, počáteční název domény a oblasti, ve kterém se vytvoří v adresáři Azure AD.

    ![Vytvoření služby Azure AD](./media/contoso-migration-infrastructure/azure-ad-create.png) 

    > [!NOTE]
    > Při vytváření adresáře má počáteční název domény v domainname.onmicrosoft.com formuláře. Název nelze změnit ani odstranit. Místo toho musí přidat jejich registrovaný název domény do Azure AD.

### <a name="add-the-domain-name"></a>Přidání názvu domény

Použití názvu standardní domény Contoso potřeba ho přidat do služby Azure AD jako vlastní název. Tato možnost umožňuje správcům přiřazovat známé uživatelská jména. Například se uživatel může přihlásit se pomocí e-mailovou adresu billg@contoso.com, namísto nutnosti billg@contosomigration.onmicrosoft.com. 

Pokud chcete nastavit vlastní název, přidejte ho do adresáře, přidání položky DNS a ověřte název ve službě Azure AD.

1. V **vlastní názvy domén** > **přidat vlastní doménu**, přidávají domény.
2. Pro použití v Azure, které potřebují k registraci u svého registrátora domény záznam DNS. 

    - V **vlastní názvy domén** seznamu, poznamenejte si informace o DNS pro název. Contoso používá záznam MX.
    - Potřebují přístup k názvu serveru k tomu. V případě společnosti Contoso se protokolují do domény Contoso.com a vytvoří nový záznam MX pro záznam DNS pomocí Azure AD využívajícího informace, které jste si poznamenali k dispozici.  
1. Po rozšíření záznamů DNS v názvu podrobnosti pro doménu, klepnutím na **ověřte** zkontrolujte vlastní název.

     ![Azure AD DNS](./media/contoso-migration-infrastructure/azure-ad-dns.png) 

### <a name="set-up-on-premises-and-azure-groups-and-users"></a>Nastavit místní a skupiny služby Azure a uživatelé

Teď, když své služby Azure AD je vytvořená a spuštěná, Contoso je nutné přidat zaměstnancům, aby místní skupiny AD, které se bude synchronizovat se službou Azure AD. Doporučujeme vám, které používají místní názvy skupin, které shodovat s názvy skupin prostředků v Azure. Díky tomu pak snadno identifikujete shody pro účely synchronizace.

#### <a name="create-resource-groups-in-azure"></a>Vytvoření skupiny prostředků v Azure

Skupiny prostředků Azure shromažďování prostředků Azure. Pomocí ID skupiny prostředků umožňují na platformě Azure k provedení operací s prostředky v rámci skupiny.

- Předplatné Azure může mít více skupin prostředků, ale skupina prostředků může existovat pouze v rámci jednoho předplatného.
- Kromě toho jedna skupina prostředků může mít několik prostředků, ale prostředek může patřit pouze do jedné skupiny.

Contoso nastavit skupiny prostředků Azure dle souhrnu v následující tabulce.

**Skupina prostředků** | **Podrobnosti**
--- | ---
**ContosoCobRG** | Tato skupina obsahuje všechny prostředky vztahující se k provozní kontinuity podnikání (PALICE).  Zahrnuje trezorů, které Contoso se vytvoří při použití služby Azure Site Recovery a služba Azure Backup.<br/><br/> Bude také zahrnovat prostředky používané pro migraci, včetně Azure Migrate a Database Migration Service.
**ContosoDevRG** | Tato skupina obsahuje prostředky pro vývoj a testování.
**ContosoFailoverRG** | Tato skupina slouží jako cílová zóna se nezdařilo u prostředků.
**ContosoNetworkingRG** | Tato skupina obsahuje všechny síťové prostředky.
**ContosoRG** | Tato skupina obsahuje materiály související s produkčních aplikací a databází.

Vytváření skupiny prostředků následujícím způsobem:

1. Na webu Azure Portal > **skupiny prostředků**, přidávají skupiny.
2. Pro každou skupinu, zadejte název, předplatné, do které patří skupině a oblast.
3. Skupiny prostředků se zobrazí v **skupiny prostředků** seznamu.

    ![Skupiny prostředků](./media/contoso-migration-infrastructure/resource-groups.png) 


#### <a name="create-matching-security-groups-on-premises"></a>Odpovídající zabezpečení skupiny místní vytvoření

1. Ve svojí místní službě Active Directory Contoso nastavit skupiny zabezpečení s názvy, které odpovídají názvy skupin prostředků Azure.
 
    ![Místní skupiny zabezpečení služby AD](./media/contoso-migration-infrastructure/on-prem-ad.png) 

2. Pro účely správy jejich vytvoření další skupiny, které se přidají do všech ostatních skupin. Tato skupina bude mít práva pro všechny skupiny prostředků v Azure. Omezený počet globální správci se přidají do této skupiny.

### <a name="synchronize-ad"></a>Synchronizace AD

Contoso má poskytovat společnou identitu pro přístup k prostředkům místně i v cloudu. Provedete to tak, že svojí místní službě Active Directory integrace s Azure AD. V tomto modelu:

- Uživatelé a organizace využít jednu identitu pro přístup k aplikacím – místních a cloudových služeb, jako je Office 365 nebo jiných webů na Internetu.
- Správci můžou využívat skupiny ve službě AD k implementaci [na základě řízení přístupu Role (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) v Azure.

Pro usnadnění integrace, použijte Contoso [nástroj Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect). Když nainstalujete a nakonfigurujete nástroj na řadiči domény, synchronizuje místní místních identit AD do služby Azure AD. 

### <a name="download-the-tool"></a>Stáhněte si nástroj

1. Na webu Azure Portal, Azure, přejde na **Azure Active Directory** > **Azure AD Connect**a stáhne nejnovější verzi nástroje na serveru při použití pro synchronizaci.

    ![Stažení AD Connect](./media/contoso-migration-infrastructure/download-ad-connect.png) 

2. Spuštění **AzureADConnect.msi** instalace, použití **použít Expresní nastavení**. Toto je nejběžnější instalace a jde použít pro jednoduchou doménovou strukturu s synchronizaci hodnot hash hesel pro ověřování.

    ![Průvodce AD Connect](./media/contoso-migration-infrastructure/ad-connect-wiz1.png) 

3. V **připojit ke službě Azure AD**, zadejte přihlašovací údaje pro připojení k Azure AD (ve formuláři CONTOSO\admin nebo contoso.com\admin).

    ![Průvodce AD Connect](./media/contoso-migration-infrastructure/ad-connect-wiz2.png) 

4. V **připojit ke službě AD DS**, zadejte přihlašovací údaje pro své místní AD.

     ![Průvodce AD Connect](./media/contoso-migration-infrastructure/ad-connect-wiz3.png) 

5. V **připraveno ke konfiguraci**, kliknou **po dokončení konfigurace spustit proces synchronizace** pro okamžité spuštění synchronizace. Pak nainstalují.


- Contoso má přímé připojení k Azure. Pokud místní AD je za proxy serverem, přečtěte si tento [článku](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-connectivity).
- Po první synchronizaci pro místní objekty AD si můžete prohlédnout ve službě Azure AD.

    ![Místní AD v Azure](./media/contoso-migration-infrastructure/on-prem-ad-groups.png) 

- Contoso IT tým jsou reprezentovány v každé skupině, na základě jejich role.

    ![Místní AD členů v Azure](./media/contoso-migration-infrastructure/on-prem-ad-group-members.png) 

### <a name="set-up-rbac"></a>Nastavení RBAC

Azure [řízení přístupu na základě Role (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) umožňuje přesnou správu přístupu pro Azure. Pomocí řízení přístupu na základě role v Azure můžete uživatelům poskytnout pouze takovou úroveň přístupu, kterou potřebují k provádění svých úloh. Můžete přiřadit příslušné role RBAC uživatelům, skupinám a aplikacím na úrovni oboru. Obor přiřazení role může být předplatné, skupinu prostředků nebo jediný prostředek. 

Contoso nyní přiřadit role do skupiny AD, které jsou synchronizované z místní.

1. V **ControlCobRG** skupinu prostředků, klikněte na tlačítko **řízení přístupu (IAM)** > **přidat**.
2. V **přidat oprávnění** > **Role**, vyberou **Přispěvatel**a vyberte **ContosoCobRG** skupiny AD ze seznamu. Skupiny se pak objeví v **Vybraní členové** seznamu. 
3. Opakujte tento postup se stejnými oprávněními pro jiné skupiny prostředků (s výjimkou **ContosoAzureAdmins**), přidáním oprávnění přispěvatele do účtu AD, který odpovídá skupině prostředků.
4. Pro **ContosoAzureAdmins** skupiny AD, přiřadí **vlastníka** role.

    ![Místní AD členů v Azure](./media/contoso-migration-infrastructure/on-prem-ad-groups.png) 


## <a name="step-3-design-for-resilience-and-disaster"></a>Krok 3: Návrh pro odolnost a po havárii

Prostředky Azure se nasadí v rámci oblasti.
- Oblasti jsou uspořádané do zeměpisných oblastí a požadavky na rezidenci, suverenitu, dodržování předpisů a odolnosti dat se uplatní v zeměpisné oblasti.
- Oblast se skládá ze sady datových centrech. Tato datová centra jsou nasazené hraniční hranic s definovanou latencí a připojené prostřednictvím vyhrazené místní sítě s nízkou latencí.
- Každá oblast Azure je spárovaná s jinou oblastí pro odolnost proti chybám.
- Přečtěte si informace o [oblastí Azure](https://azure.microsoft.com/global-infrastructure/regions/)a seznamte se s [jak spárované oblasti](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).


Contoso rozhodli pro USA – východ 2 (nachází se ve Virginii) jako svoje primární oblasti a USA (střed) jako jejich sekundární oblasti. Existuje několik důvodů:

- Contoso datacenter se nachází v New Yorku, a jsou považovány za čekací doba na nejbližší datové centrum.
- Oblast východní USA 2 má všechny služby a produkty, které potřebují používat. Ne všechny oblasti Azure jsou stejné z hlediska produktů a služeb, které jsou k dispozici. Můžete zkontrolovat [produkty Azure podle oblastí](https://azure.microsoft.com/global-infrastructure/services/).
- USA (střed) je spárované oblasti Azure v oblasti východní USA 2.

Jako uvažují o svém hybridním prostředí, je potřeba zvážit, jakým způsobem chcete sestavit jejich oblasti návrhu odolnosti a strategii zotavení po havárii Contoso. Široce strategie rozsahu nasazení v jedné oblasti, které spoléhá na funkce jako doménami selhání a oblastní párování, díky kterému pro odolnost, prostřednictvím úplné aktivní / aktivní model cloudové služby a databáze jsou nasazené a údržby platformou Azure. uživatelé z dvou oblastech.

Contoso se rozhodli využít střední cestách. Budete nasazovat své aplikace a prostředky v primární oblasti a zachovat celou infrastrukturu v sekundární oblasti, tak, aby byl připraven tak, aby fungoval jako úplné zálohování v případě havárie výsledné aplikace nebo selhání oblasti.


## <a name="step-4-design-a-network-infrastructure"></a>Krok 4: Návrh síťové infrastruktury

V jejich oblasti návrhu v místě společnosti Contoso připraveni vzít v úvahu síťové strategie. Potřebují rozmyslete si, jak jejich místním datacentrem a Azure a komunikovat mezi sebou a postup návrhu sítě infrastruktury v Azure. Konkrétně je nutné:

**Plánování hybridního připojení k síti**: Zjistěte, jak jsou teď k propojení sítí napříč místními a Azure.
**Návrh infrastruktury Azure network**: Rozhodněte, jak se bude nasazení sítě přes oblasti. Jak se sítí komunikují v rámci stejné oblasti a oblastmi.
**Návrh a nastavení sítě Azure**: nastavení sítě Azure a podsítě a rozhodnout, co se bude nacházet v nich.

### <a name="plan-hybrid-network-connectivity"></a>Plánování hybridního připojení k síti

Contoso považovat za [počet architektury](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/) pro hybridní sítě mezi Azure a jejich místní datacentra. [Přečtěte si další](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/considerations) srovnávající možnosti.

Připomínáme Contoso místní síťové infrastruktury se skládá právě z svá datacentra v New Yorku, a místní větve ve východní části USA.  Všechna místa mít obchodní třídy připojení k Internetu.  Každé větve je připojen k datovému centru prostřednictvím tunelu IPSec VPN přes internet.

![Síť společnosti Contoso](./media/contoso-migration-infrastructure/contoso-networking.png) 

Zde je, jak společnosti Contoso rozhodla implementovat hybridní možnosti připojení:

1. Nastavte nové připojení VPN typu site-to-site mezi datovým centrem Contoso v New Yorku a tyto dvě oblasti Azure v oblastech východní USA 2 a USA (střed).
2. Větev office provoz směřující k virtuálním sítím Azure bude směrovat přes hlavní datové centrum společnosti Contoso. 
3. Jak se vertikálně navýšit kapacitu jejich nasazení v Azure, budete navázání spojení ExpressRoute mezi svá datacentra a oblastí Azure. Pokud k tomu dojde, budete připojení site-to-site VPN pro převzetí služeb při selhání pouze pro účely zachování.
    - [Další informace](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/considerations) o výběru mezi hybridní řešení sítě VPN a ExpressRoute.
    - Ověřte [ExpressRoute umístění a podporu](https://docs.microsoft.com/azure/expressroute/expressroute-locations-providers).


**Pouze sítě VPN**

![Contoso VPN](./media/contoso-migration-infrastructure/hybrid-vpn.png) 


**Připojení VPN a ExpressRoute**

![Contoso VPN/ExpressRoute](./media/contoso-migration-infrastructure/hybrid-vpn-expressroute.png) 


### <a name="design-the-azure-network-infrastructure"></a>Návrh infrastruktury sítě Azure

Je velmi důležité, že Contoso umístí sítí místo tak, aby díky jejich hybridní nasazení bezpečná a škálovatelná. K tomuto účelu Contoso trvá dlouhodobý přístup a návrhu virtuálních sítí (VNets) odolnost a připraveno pro podniky. [Další informace](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm) o plánování virtuální sítě.

Připojit jejich dvou oblastech, se rozhodl implementaci modelu rozbočovače k centrální síti Contoso:

- V rámci každé oblasti bude Contoso používají model střed a paprsek.
- Pro připojení, sítě a rozbočovače, Contoso použije Azure vnet peering.

#### <a name="network-peering"></a>Vnet peering

Azure poskytuje sítě partnerských vztahů pro připojení virtuálních sítí a rozbočovače. Globální partnerský vztah umožňuje připojení mezi virtuálními sítěmi/hubs v různých oblastech. Místní vytvoření partnerského vztahu se připojí virtuální sítě ve stejné oblasti. Partnerský vztah virtuální sítě poskytují celou řadu výhod:

- Síťový provoz mezi partnerskými virtuálními sítěmi je privátní.
- Provoz mezi virtuálními sítěmi, zůstane na páteřní síti Microsoftu. Při komunikaci mezi virtuálními sítěmi je požadována bez veřejného Internetu, brány nebo šifrování.
- Vytvoření partnerského vztahu představuje výchozí, s nízkou latencí a velkou šířku pásma při propojení prostředků v různých virtuálních sítích.

[Další informace](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) o vnet peering.

#### <a name="hub-to-hub-across-regions"></a>Centrum pro rozbočovač napříč oblastmi

Contoso nasadí rozbočovač v jednotlivých oblastech. Centrum je virtuální síť (VNet) v Azure, která funguje jako ústřední bod připojení k vaší místní síti. Centrum virtuálních sítí se připojit k sobě navzájem pomocí globální VNet peering. Globální VNet peering se připojí virtuální sítě v různých oblastech Azure.

- Centrum v jednotlivých oblastech má partnerský vztah pro své Centrum partnera v jiné oblasti.
- Centrum je v partnerském vztahu do každé sítě v jeho oblast a může připojit ke všem síťovým prostředkům.

    ![Globální partnerský vztah](./media/contoso-migration-infrastructure/global-peering.png) 

#### <a name="hub-and-spoke-within-a-region"></a>Střed a paprsek v rámci oblasti

V rámci každé oblasti nasadí Contoso virtuálních sítí pro různé účely, jako paprsky sítě z centra oblasti. Virtuální sítě v rámci oblasti slouží k připojení do svého centra a k sobě navzájem partnerský vztah.

#### <a name="design-the-hub-network"></a>Návrh centrální síti

V rámci model střed a paprsek, který jste zvolili Contoso musí přemýšlet o tom, provoz z jejich místní datacentra a z Internetu, se budou směrovat. Zde je, jak společnosti Contoso rozhodli jste se zpracovávají směrování pro USA – východ 2 a USA (střed) hubs:

- Jejich navrhování sítě označované jako "reverzní c", je to cesta, která příchozí pro odchozí síťové pakety podle pokynů.
- Jejich síťové architektury má dvě hranice, zóně nedůvěryhodné front-endu hraniční a zóny důvěryhodných serverů back-end.
- Brána bude mít na síťový adaptér v každé zóně, řízení přístupu do zóny důvěryhodných serverů.
- Z Internetu:
    - Přenosy z Internetu, se setkají s vyrovnáváním zatížení veřejnou IP adresu v hraniční síti.
    - Tento provoz je směrován přes bránu firewall, v souladu s pravidly brány firewall.
    - Po dokončení implementace jsou řízení přístupu k síti, provoz se předají do příslušného umístění, do zóny důvěryhodných serverů.
    - Odchozí provoz z virtuální sítě se budou směrovat do Internetu použijte trasy definované uživatelem (udr). Provoz se vynucené přes bránu firewall a kontrole souladu se zásadami společnosti Contoso.
- Z datového centra společnosti Contoso:
    - Příchozí provoz přes VPN typu site-to-site (nebo ExpressRoute) narazí na veřejnou IP adresu brány Azure VPN.
    - Provoz je směrován přes bránu firewall a můžou se pravidla brány firewall.
    - Po použití pravidla přenosy předávaly do interního nástroje (standardní skladové položky) na důvěryhodné zóny interní podsítě.
    - Odchozí provoz z podsítě pro důvěryhodného do místního datového centra prostřednictvím sítě VPN je směrován přes brány firewall a pravidla platila před přechodem přes připojení site-to-site VPN.



### <a name="design-and-set-up-azure-networks"></a>Návrh a nastavení sítě Azure

Síť a směrování topologie v místě jsou připravení nastavit své sítě Azure a podsítě Contoso.

- Contoso se implementace třídy A privátní sítě v Azure (0.0.0.0-127.255.255.255). Tento postup funguje, od místních aktuální mají třídy B privátní adresní prostor 172.160.0/16 tak můžou být nesmí být žádné překrytí rozsahy adres.
- Teď se k nasazení virtuální sítě v jejich primárních a sekundárních oblastech.
- Budou používat zásady vytváření názvů, který obsahuje předponu **VNET** a oblasti – zkratka **EUS2** nebo **kapacitní jednotky**. Pomocí této normy, bude mít název sítě centra **připojení typu VNET-HUB-EUS2** (východní USA 2), a **připojení typu VNET-HUB-kapacitních jednotek** (centrální USA).
- Contoso nemají [řešení IPAM](https://docs.microsoft.com/windows-server/networking/technologies/ipam/ipam-top), takže je třeba naplánovat směrováním v síti bez překladu adres.


#### <a name="virtual-networks-in-east-us-2"></a>Virtuální sítě v oblasti východní USA 2

Východní USA 2 je primární oblasti, kterou Contoso použije k nasazení prostředků a služeb. Zde je, jak se tedy rozhodnete architekt sítě:

- **Centrum**: virtuální síť v oblasti východní USA 2 centrum je ústřední bod primární připojení k jejich místnímu datovému centru.
- **Virtuální sítě**: paprsků virtuálních sítí v oblasti východní USA 2, lze použít k izolaci úloh v případě potřeby. Kromě virtuální síť centra budou mít Contoso paprsku dvou virtuálních sítí v oblasti východní USA 2:
    - **PŘIPOJENÍ TYPU VNET-DEV-EUS2**. Tuto virtuální síť se poskytují že vývojové a testovací tým bude plně funkční síť pro vývojových projektů. Bude fungovat jako oblast pilotního nasazení produkčního prostředí a bude záviset na provozní infrastruktuře na funkci.
    - **Připojení typu VNET-PROD-EUS2**: komponenty produkčního prostředí Azure IaaS budou umístěny v této síti. 
    -  Každá virtuální síť bude mít svůj vlastní jedinečný adresní prostor se nepřekrývá. Tito pracovníci budou konfigurace směrování bez NAT
- **Podsítě**:
    - Bude obsahovat podsíť každá síť pro každou vrstvu aplikace
    - Každá podsíť v produkční síti bude mít odpovídající podsítě ve virtuální síti vývoj.
    - Kromě toho produkční síť obsahuje podsítě pro řadiče domény.

V následující tabulce jsou shrnuté virtuálních sítí v oblasti východní USA 2.

**Virtuální síť** | **rozsah** | **Peer**
--- | --- | ---
**PŘIPOJENÍ TYPU VNET-HUB-EUS2** | 10.240.0.0/20 | PŘIPOJENÍ TYPU VNET-HUB-CUS2, PŘIPOJENÍ TYPU VNET-DEV-EUS2, PŘIPOJENÍ TYPU VNET-PROD-EUS2
**PŘIPOJENÍ TYPU VNET-DEV-EUS2** | 10.245.16.0/20 | PŘIPOJENÍ TYPU VNET-HUB-EUS2
**PŘIPOJENÍ TYPU VNET-PROD-EUS2** | 10.245.32.0/20 | PŘIPOJENÍ TYPU VNET-HUB-EUS2, PŘIPOJENÍ TYPU VNET-PROD-KAPACITNÍ JEDNOTKY

![Centra/paprsky v primární oblasti](./media/contoso-migration-infrastructure/primary-hub-peer.png) 


#### <a name="subnets-in-the-east-us-2-hub-network-vnet-hub-eus2"></a>Podsítě ve východní USA 2 centra sítě (virtuální síť-HUB-EUS2)

**Podsíť/zóny** | **CIDR** | ** Použitelných IP adres
--- | --- | ---
**IB UntrustZone** | 10.240.0.0/24 | 251
**IB TrustZone** | 10.240.1.0/24 | 251
**OB UntrustZone** | 10.240.2.0/24 | 251
**OB TrustZone** | 10.240.3.0/24 | 251
**GatewaySubnets** | 10.240.10.0/24 | 251


#### <a name="subnets-in-the-east-us-2-dev-network-vnet-dev-eus2"></a>Podsítě ve východní USA 2 vývojový sítě (VNET-DEV-EUS2)

Vývoj virtuální síť se používá vývojový tým jako oblast pilotního nasazení produkčního prostředí. Má tři podsítě.

**Podsíť** | **CIDR** | **Adresy** | **V podsíti**
--- | --- | --- | ---
**DEV-FE – EUS2** | 10.245.16.0/22 | 1019 | Front-Endů/webovou vrstvu virtuálních počítačů
**DEV-APP-EUS2** | 10.245.20.0/22 | 1019 | Virtuální počítače vrstvy aplikace
**DEV-DB-EUS2** | 10.245.24.0/23 | 507 | Virtuální počítače databáze


#### <a name="subnets-in-the-east-us-2-production-network-vnet-prod-eus2"></a>Podsítě ve východní USA 2 produkční sítě (VNET-PROD-EUS2)

Komponenty Azure IaaS jsou umístěné v podnikové síti. Každá úroveň aplikace má vlastní podsíti. Podsítě odpovídají polím v síti Dev, a uveďte podsítě pro řadiče domény.

**Podsíť** | **CIDR** | **Adresy** | **V podsíti**
--- | --- | --- | ---
**PROD-FE – EUS2** | 10.245.32.0/22 | 1019 | Front-Endů/webovou vrstvu virtuálních počítačů
**PROD. APLIKACE EUS2** | 10.245.36.0/22 | 1019 | Virtuální počítače vrstvy aplikace
**PROD-DB-EUS2** | 10.245.40.0/23 | 507 | Virtuální počítače databáze
**PROD. ŘADIČ DOMÉNY EUS2** | 10.245.42.0/23 | 251 | Virtuální počítače řadiče domény


![Centrum architektury sítě](./media/contoso-migration-infrastructure/azure-networks-eus2.png)


#### <a name="virtual-networks-in-central-us-secondary-region"></a>Virtuální sítě v oblasti střed USA (sekundární oblast)

USA (střed) je sekundární oblasti společnosti Contoso. Zde je, jak se tedy rozhodnete architekt sítě:

- **Centrum**: centrum, virtuální síť v oblasti východní USA 2 je ústřední bod připojení na jejich místním datovým centrem a paprsek virtuálních sítí v oblasti východní USA 2, lze použít k izolaci úloh v případě potřeby spravují odděleně od ostatních paprsků.
- **Virtuální sítě**: mají dvě virtuální sítě v oblasti střed USA:
    - PŘIPOJENÍ TYPU VNET-PROD-KAPACITNÍ JEDNOTKY. Tuto virtuální síť je produkční sítě, podobně jako VNET PROD_EUS2. 
    - PŘIPOJENÍ TYPU VNET-ASR-KAPACITNÍ JEDNOTKY. Tuto virtuální síť bude sloužit jako umístění, ve kterém se vytvoří virtuální počítače po převzetí služeb při selhání z místního nebo jako umístění pro virtuální počítače Azure, které při selhání z primární do sekundární oblasti. Tato síť se podobá produkčních sítích, ale bez všechny řadiče domény na něj.
    -  Každá virtuální síť v oblasti budou mít svůj vlastní adresní prostor se nepřekrývá. Tito pracovníci budou konfigurace směrování bez NAT
- **Podsítě**: podsítě bude navržený v podobným způsobem jako ty v oblastech východní USA 2. Výjimkou je, že nepotřebují podsíť pro řadiče domény.

Virtuální sítě v oblasti střed USA jsou shrnuty v následující tabulce.

**Virtuální síť** | **rozsah** | **Peer**
--- | --- | ---
**PŘIPOJENÍ TYPU VNET-HUB-KAPACITNÍ JEDNOTKY** | 10.250.0.0/20 | PŘIPOJENÍ TYPU VNET-HUB-EUS2, VIRTUÁLNÍ SÍTĚ – AZURE SITE RECOVERY-KAPACITNÍ JEDNOTKY, PŘIPOJENÍ TYPU VNET-PROD-KAPACITNÍ JEDNOTKY
**KAPACITNÍ JEDNOTKY VIRTUÁLNÍ SÍTĚ AZURE SITE RECOVERY** | 10.255.16.0/20 | PŘIPOJENÍ TYPU VNET-HUB-KAPACITNÍ JEDNOTKY, PŘIPOJENÍ TYPU VNET-PROD-KAPACITNÍ JEDNOTKY
**PŘIPOJENÍ TYPU VNET-PROD-KAPACITNÍ JEDNOTKY** | 10.255.32.0/20 | PŘIPOJENÍ TYPU VNET-HUB-KAPACITNÍ JEDNOTKY, VIRTUÁLNÍ SÍTĚ – AZURE SITE RECOVERY-KAPACITNÍ JEDNOTKY, PŘIPOJENÍ TYPU VNET-PROD-EUS2  


![Centra/paprsky v spárované oblasti](./media/contoso-migration-infrastructure/paired-hub-peer.png)


#### <a name="subnets-in-the--central-us-hub-network-vnet-hub-cus"></a>Podsítě v centrální USA centrální síti (virtuální síť centra CUS)

**Podsíť** | **CIDR** | **Použitelných IP adres**
--- | --- | ---
**IB UntrustZone** | 10.250.0.0/24 | 251
**IB TrustZone** | 10.250.1.0/24 | 251
**OB UntrustZone** | 10.250.2.0/24 | 251
**OB TrustZone** | 10.250.3.0/24 | 251
**Podsíť brány** | 10.250.2.0/24 | 251


#### <a name="subnets-in-the-central-us-production-network-vnet-prod-cus"></a>Podsítě v centrální nás produkční sítě (VNET-PROD-kapacitní jednotky)

Paralelní do produkční sítě v primární oblasti USA – východ 2 je produkční sítě v sekundární oblasti USA (střed). 

**Podsíť** | **CIDR** | **Adresy** | **V podsíti**
--- | --- | --- | ---
**PROD-FE – KAPACITNÍ JEDNOTKY** | 10.255.32.0/22 | 1019 | Front-Endů/webovou vrstvu virtuálních počítačů
**KAPACITNÍ JEDNOTKY PRODUKČNÍ APLIKACE** | 10.255.36.0/22 | 1019 | Virtuální počítače vrstvy aplikace
**PROD-DB-KAPACITNÍ JEDNOTKY** | 10.255.40.0/23 | 507 | Virtuální počítače databáze
**KAPACITNÍ JEDNOTKY PROD ŘADIČE DOMÉNY** | 10.255.42.0/24 | 251 | Virtuální počítače řadiče domény

#### <a name="subnets-in-the-central-us-failoverrecovery-network-in-central-us-vnet-asr-cus"></a>Podsítě v síti převzetí služeb při selhání nebo obnovení centrální USA v oblasti střed USA (virtuální sítě Azure Site Recovery CUS)

Kapacitní jednotky virtuální sítě Azure Site Recovery síť se používá pro účely převzetí služeb při selhání mezi oblastmi. Site Recovery se použije k replikaci a převzetí služeb při selhání virtuálních počítačů Azure mezi regiony. Taky funguje jako datacenter společnosti Contoso na síť Azure pro chráněné úlohy, které zůstanou v místním, ale převzetí služeb při selhání do Azure pro zotavení po havárii.

Kapacitní jednotky virtuální sítě Azure Site Recovery je základní stejné podsíti jako produkční virtuální síť v oblasti východní USA 2, ale bez nutnosti pro podsíť řadiče domény.

**Podsíť** | **CIDR** | **Adresy** | **V podsíti**
--- | --- | --- | ---
**AZURE SITE RECOVERY FE – KAPACITNÍ JEDNOTKY** | 10.255.16.0/22 | 1019 | Front-Endů/webovou vrstvu virtuálních počítačů
**AZURE SITE RECOVERY APP-KAPACITNÍ JEDNOTKY** | 10.255.20.0/22 | 1019 | Virtuální počítače vrstvy aplikace
**AZURE SITE RECOVERY DB-KAPACITNÍ JEDNOTKY** | 10.255.24.0/23 | 507 | Virtuální počítače databáze

![Centrum architektury sítě](./media/contoso-migration-infrastructure/azure-networks-cus.png)

#### <a name="configure-peered-connections"></a>Nakonfigurovat partnerské připojení

Centrum v jednotlivých oblastech se jde navázat partnerský vztah k centru v jiné oblasti a všechny virtuální sítě v rámci oblasti rozbočovače. To umožňuje centra ke komunikaci a chcete-li zobrazit všechny virtuální sítě v rámci oblasti. Poznámky:

- Vytvoření partnerského vztahu se vytvoří připojení oboustranný. Od zahájení druhé strany na první virtuální sítě a jiný v druhé sítě VNet.
- V hybridním nasazení provozu, který předává mezi rovnocennými počítači musí být viditelné z připojení VPN mezi místním datacentrem a Azure. Chcete-li povolit, existují některé konkrétní nastavení, které musí být nastavena na partnerské připojení.

Pro všechna připojení z virtuální sítě paprsků prostřednictvím centra k místnímu datovému centru společnosti Contoso musí povolit provoz tak, aby předané a propojeními VPN Gateway.

##### <a name="domain-controller"></a>Řadič domény

Pro řadiče domény v síti VNET-PROD-EUS2 Contoso chce, aby se tok mezi EUS2 centra/produkční síť i přes připojení VPN do místního provozu. K tomu je nutné povolit následující:

1. **Povolit přesměrovaný přenos** a **povolit průchod konfigurace brány** partnerské připojení. V našem příkladu to může být připojení typu VNET-HUB-EUS2 připojení VNET-PROD-EUS2.

    ![Partnerské vztahy](./media/contoso-migration-infrastructure/peering1.png)

2. **Povolit přesměrovaný přenos** a **používat vzdálené brány** na druhé straně partnerského vztahu, na připojení typu VNET-PROD-EUS2 připojení VNET-HUB-EUS2.

    ![Partnerské vztahy](./media/contoso-migration-infrastructure/peering2.png)

3. Místní se nastavit statickou trasu, která směruje místní provoz směrovat přes tunelové propojení sítě VPN do virtuální sítě. Konfigurace by dokončení na bráně, která poskytuje tunelového připojení sítě VPN ze společnosti Contoso do Azure. Společnost Contoso využívá k tomu Windows směrování a vzdálený přístup.

    ![Partnerské vztahy](./media/contoso-migration-infrastructure/peering3.png)

##### <a name="production-networks"></a>Produkční sítě 

Síť spoked peer neuvidí spoked partnerské sítě v jiné oblasti přes rozbočovač.

Pro společnosti Contoso produkčních sítích v obou oblastech zobrazíte mezi sebou potřebují vytvořit přímé partnerské propojení pro připojení typu VNET-PROD-EUS2 a zakázat-PROD-kapacitní jednotky. 

![Partnerské vztahy](./media/contoso-migration-infrastructure/peering4.png)

### <a name="set-up-dns"></a>Nastavení DNS

Při nasazování prostředků ve virtuálních sítích, máte několik možností pro překlad názvů domén. Můžete použít překlad poskytovaný platformou Azure, nebo zadejte servery DNS pro překlad. Typ překladu adres, které používáte závisí na tom, jak vaše prostředky musejí komunikovat mezi sebou. Získat [informace](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#azure-provided-name-resolution) o službě Azure DNS.

Contoso se rozhodli, že služba Azure DNS není vhodná ve svém hybridním prostředí. Místo toho jsou teď využít svoje místní servery DNS.

- Protože se jedná o hybridní síť všechny virtuální počítače místní a v Azure musí být schopné překládat názvy fungovat správně. To znamená, že je nutné použít vlastní nastavení DNS na všechny virtuální sítě.
- Contoso aktuálně máte řadiče domény nasazené v datovém centru společnosti Contoso a v pobočkách. Jejich primárních serverů DNS jsou CONTOSODC1(172.16.0.10) a CONTOSODC2(172.16.0.1)
- Při nasazení virtuální sítě, nastaví se pro použití jako server DNS v sítích místních řadičích domény. 
- Chcete-li nastavit tuto konfiguraci, při využití vlastního DNS ve virtuální síti, musí být Azure rekurzivní překladače IP adresu (například adresy 168.63.129.16) přidané do seznamu DNS.  K tomuto účelu Contoso nakonfiguruje nastavení serveru DNS na každé virtuální síti. Například vlastní nastavení DNS pro připojení typu VNET-HUB-EUS2 sítě by měl vypadat takto:
    
    ![Vlastní DNS](./media/contoso-migration-infrastructure/custom-dns.png)

Kromě svých řadičů domény s místními Contoso se chystáte implementovat čtyři další k podpoře Azure sítí, dvěma pro každou oblast. Zde je, co se budete nasazovat v Azure.

**Oblast** | **ŘADIČ DOMÉNY** | **Virtuální síť** | **Podsíť** | **IP adresa**
--- | --- | --- | --- | ---
EUS2 | CONTOSODC3 | PŘIPOJENÍ TYPU VNET-PROD-EUS2 | PROD. ŘADIČ DOMÉNY EUS2 | 10.245.42.4
EUS2 | CONTOSODC4 | PŘIPOJENÍ TYPU VNET-PROD-EUS2 | PROD. ŘADIČ DOMÉNY EUS2 | 10.245.42.5
KAPACITNÍ JEDNOTKY | CONTOSODC5 | PŘIPOJENÍ TYPU VNET-PROD-KAPACITNÍ JEDNOTKY | KAPACITNÍ JEDNOTKY PROD ŘADIČE DOMÉNY | 10.255.42.4
KAPACITNÍ JEDNOTKY | CONTOSODC6 | PŘIPOJENÍ TYPU VNET-PROD-KAPACITNÍ JEDNOTKY | KAPACITNÍ JEDNOTKY PROD ŘADIČE DOMÉNY | 10.255.42.4

Po nasazení místních řadičích domény, třeba Contoso aktualizace nastavení DNS v sítích na buď oblasti mají být zahrnuty nových řadičů domény jejich seznam serverů DNS.



#### <a name="set-up-domain-controllers-in-azure"></a>Nastavení řadiče domény v Azure

Po aktualizaci nastavení sítě, Contoso připraveni k sestavení svých řadičů domény v Azure.

1. Na webu Azure Portal jejich nasazení nového virtuálního počítače Windows serveru do příslušné virtuální sítě.
2. Vytváření skupiny dostupnosti v každém umístění pro virtuální počítač. Skupiny dostupnosti, postupujte takto:
    - Ujistěte se, že prostředky infrastruktury Azure rozděluje virtuálních počítačů do jiné infrastruktury v oblasti Azure. 
    -  Umožňuje společnosti Contoso se 99,95 % smlouva SLA pro virtuální počítače v Azure.  [Další informace](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets).

    ![Skupina dostupnosti](./media/contoso-migration-infrastructure/availability-group.png) 
3. Po nasazení virtuálního počítače, pera síťové rozhraní virtuálního počítače. Tady, nastavují privátní IP adresy statické a zadejte platnou adresu.

    ![SÍŤOVÉ ROZHRANÍ VIRTUÁLNÍHO POČÍTAČE](./media/contoso-migration-infrastructure/vm-nic.png)

4. Nyní jsou připojit nový datový disk k virtuálnímu počítači. Tento disk obsahuje databázi služby Active Directory a sdílené složky sysvol. 
    - Velikost disku určí počet vstupně-výstupních operací, který ji podporuje.
    - V čase velikost disku může být nutné zvýšit růst objemu prostředí.
    - Jednotka neměla být nastavena pro čtení a zápis pro použití mezipaměti u hostitele. Databáze služby Active Directory to nepodporuje.

     ![Active Directory disku](./media/contoso-migration-infrastructure/ad-disk.png)

5. Po přidání disku, připojení k virtuálnímu počítači přes vzdálenou plochu a otevřete Správce serveru.
6. Potom v **Souborová služba a služba úložiště**, spuštění Průvodce vytvořením svazku, zajištění, že jednotka je daný písmenem F: nebo vyšší na místním virtuálním počítači.

     ![Průvodce vytvořením svazku](./media/contoso-migration-infrastructure/volume-wizard.png)

7. Ve Správci serveru, přidejte **Active Directory Domain Services** role. Poté se nakonfigurovat virtuální počítač jako řadič domény.

      ![Role serveru](./media/contoso-migration-infrastructure/server-role.png)  

9. Po virtuální počítač je nakonfigurovaný jako řadič domény a restartován, otevřete Správce DNS a nakonfigurovat překladač Azure DNS jako server pro předávání.  To umožňuje řadič domény pro předávání dotazů DNS, které nelze vyřešit v Azure DNS.

    ![Server DNS pro předávání](./media/contoso-migration-infrastructure/dns-forwarder.png)

10. Pomocí příslušného řadiče domény pro oblasti virtuální sítě, aktualizujte vlastní nastavení DNS pro každou virtuální síť. Patří mezi ně řadiče místní domény v seznamu.

### <a name="set-up-active-directory"></a>Další informace o Site Recovery scénáře.

AD je důležité služby v síti a musí být správně nakonfigurované. Contoso se sestaví servery AD pro datové centrum společnosti Contoso a pro oblasti EUS2 a kapacitní jednotky.  

1. Vytvoří dvě nové lokality (AZURE EUS2 a AZURE CUS) spolu s web datového centra (ContosoDatacenter).
2. Po vytvoření webů, jejich vytvoření podsítí v lokalitách, tak, aby odpovídaly virtuální sítě a datacenter.

    ![Řadič domény podsítě](./media/contoso-migration-infrastructure/dc-subnets.png)

3. Potom vytvořte dvě spojení sítí pro všechno, co připojení. Řadiče domény by měl poté přesunut do jejich umístění.

    ![Řadič domény odkazy](./media/contoso-migration-infrastructure/dc-links.png)

4. Po dokončení konfigurace všechno, co replikační topologie služby Active Directory je na místě.
    
    ![Replikace řadiče domény](./media/contoso-migration-infrastructure/ad-resolution.png)

5. Všechno dokončeno seznam řadičů domény a servery zobrazují v Centru pro správu v místním Active Directory.

    ![Centrum pro správu AD](./media/contoso-migration-infrastructure/ad-center.png)

## <a name="step-5-plan-for-governance"></a>Krok 5: Plánování zásad správného řízení

Azure poskytuje celou řadu ovládacích prvků zásad správného řízení napříč službami a platformou Azure. [Přečtěte si další](https://docs.microsoft.com/azure/security/governance-in-azure) pro základní znalosti o možnosti.

Tak, jak nakonfigurovat identit a řízení přístupu, Contoso jste už začali chcete změnit některé aspekty zabezpečení a zásad správného řízení na místo. Obecně existují tři oblasti, které potřebují ke zvážení:

- **Zásady**: platí zásady v Azure a vynucuje pravidla a efekty u vašich prostředků tak, aby prostředky i nadále dodržovalo firemní požadavky a smlouvy o úrovni služeb.
- **Zamkne**: vám Azure povolí zámek předplatná, skupiny prostředků a další prostředky, tak, aby se moci upravovat jenom ti s autoritou Uděláte to tak.
- **Značky**: prostředky lze řídit, auditovat a spravovat pomocí značek. Značky připojit k prostředkům, poskytnutí informací o prostředky a vlastníci metadat.

### <a name="set-up-policies"></a>Nastavení zásad

Službu Azure Policy vyhodnotí prostředky, vyhledává ty, které nejsou kompatibilní s definic zásad, které máte na místě. Například může mít zásadu, která pouze umožňuje určitého typu virtuálních počítačů, nebo vyžaduje prostředky s konkrétní značkou. 

Zásady Azure zadejte definici zásady a přiřazení zásad zadejte obor, ve kterém mají zásady uplatnit. Rozsah musí být v rozsahu skupiny pro správu do skupiny prostředků. [Přečtěte si](https://docs.microsoft.com/azure/azure-policy/create-manage-policy) o vytváření a Správa zásad.

Contoso se chcete začít pracovat s několika zásad:

- Chtějí zásadu, která Ujistěte se, že prostředky je možné nasadit jenom v oblastech EUS2 a kapacitní jednotky.
- Chce omezit SKU virtuálních počítačů na pouze schválené SKU. Záměrem je zajistit, že nejsou použity nákladné skladové položky virtuálních počítačů.

#### <a name="limit-resources-to-regions"></a>Omezení prostředků do oblastí

Contoso použijte předdefinovanou definici zásady **povolená umístění** k omezení prostředků oblastech.

1. Na webu Azure Portal, klikněte na tlačítko **všechny služby**a vyhledejte **zásady**.
2. Vyberte **přiřazení** > **přiřazení zásad**.
3. Vyberte v seznamu zásad **povolená umístění**.
4. Nastavte **oboru** název předplatného Azure a vyberte tyto dvě oblasti v seznamu povolených aplikací.

    ![Zásady povolené oblastí](./media/contoso-migration-infrastructure/policy-region.png)

5. Ve výchozím nastavení je zásada nastavená s **Odepřít**, to znamená, že pokud, někdo spustí nasazení v rámci předplatného, který není v EUS2 nebo kapacitní jednotky, instalace se nezdaří. Zde je, co se stane, když někdo v rámci předplatného Contoso pokusí nastavení nasazení v oblasti západní USA.

    ![Zásady se nezdařilo](./media/contoso-migration-infrastructure/policy-failed.png)

#### <a name="allow-specific-vm-skus"></a>Povolit konkrétní skladové položky virtuálních počítačů

Společnost Contoso použije předdefinovanou definici zásady **měly virtuální počítače SKU** pro omezení typů virtuálních počítačů, které mohou být vytvořeny v rámci předplatného. 

![Skladová položka zásad](./media/contoso-migration-infrastructure/policy-sku.png)



#### <a name="check-policy-compliance"></a>Zkontrolovat dodržování zásad

Zásady vejdou v platnost okamžitě a Contoso můžete zkontrolovat prostředky pro dodržování předpisů. 

1. Na webu Azure Portal, klikněte na tlačítko **dodržování předpisů** odkaz.
2. Zobrazí se řídicí panel pro dodržování předpisů. Další podrobnosti můžete procházet hierarchii.

    ![Dodržování zásad](./media/contoso-migration-infrastructure/policy-compliance.png)


### <a name="set-up-locks"></a>Nastavení uzamčení

Contoso dlouho používá rozhraní ITIL pro správu jejich systémů. Jedním z nejdůležitějších aspektů rozhraní framework je řízení změn a Contoso chce se ujistit, že řízení změn je implementována v jejich nasazení Azure.

Contoso se chystáte implementovat zámky následujícím způsobem:

- Jakékoli produkční nebo převzetí služeb při selhání součásti musí být ve skupině prostředků, který má zámek ReadOnly.  To znamená, že k úpravě nebo odstranění položek produkčního prostředí, musí odebrat zámek. 
- Skupiny prostředků v neprodukčním prostředí budou mít CanNotDelete zámky. To znamená, že oprávnění uživatelé můžou číst nebo upravovat prostředek, ale nelze odstranit.

[Další informace](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources) o zámky.

### <a name="set-up-tagging"></a>Nastavit značky

Ke sledování prostředky, jako jste přidali, je čím dál důležitější pro Contoso na prostředky přidružit příslušné oddělení, zákazníků a prostředí. 

Kromě poskytování informací o prostředcích a vlastníky, značky vám umožní Contoso, agregace a skupiny prostředků a použít tato data pro účely vracení peněz.

Contoso je potřeba vizualizovat svoje prostředky Azure způsobem, který dává smysl pro jejich podnikání. Pro příklad, ale role nebo oddělení. Všimněte si, že prostředky nemusí nacházet ve stejné skupině prostředků se stejnou značkou. K tomuto účelu vytvoří Contoso taxonomie jednoduché značky tak, aby všichni používají stejný značky.

**Název značky** | **Hodnota**
--- | ---
Nákladové středisko | 12345: musí to být platný nákladových středisek od SAPU.
Organizační jednotku | Název organizační jednotky (od SAPU.). Nákladové středisko shody.
ApplicationTeam | E-mailový alias týmu, který vlastní podporu pro aplikaci.
CatalogName | Název aplikace nebo ShareServices, za katalogu služeb, kterou prostředek podporuje.
Portálu Service Manager | E-mailový alias ITIL Service Manager pro prostředek.
COBPriority | Priorita nastavena obchod pro zajištění BCDR. Hodnoty 1 – 5.
ENV | Možné hodnoty jsou PROD DEV, STG. Představující, vývoj, přípravným a produkčním prostředím.

Příklad: 

 ![Azure značky](./media/contoso-migration-infrastructure/azure-tag.png)

Po vytvoření značky Contoso přejděte zpět a vytvoření nových definic Azure policy a přiřazení k vynucení použití požadované značky celé organizace.


## <a name="step-6-consider-security"></a>Krok 6: Zvažte zabezpečení

V cloudu je zásadní zabezpečení a Azure nabízí širokou škálu nástrojů zabezpečení a možnosti. Ty umožňují vytvářet bezpečná řešení, na zabezpečené platformě Azure. Čtení [jistota v důvěryhodném cloudu](https://azure.microsoft.com/overview/trusted-cloud/) získat další informace o zabezpečení Azure.

Existuje několik aspektů hlavní pro společnost Contoso vzít v úvahu

- **Azure Security Center**: Azure Security Center zajišťuje jednotnou správu zabezpečení a pokročilou ochranu před hrozbami napříč hybridními cloudovými úlohami. Se službou Security Center můžete používat zásady zabezpečení napříč úlohami, omezit vystavení hrozbám a detekovat útoky a reagovat na ně.  [Další informace](https://docs.microsoft.com/azure/security-center/security-center-intro).
- **Skupiny zabezpečení sítě (Nsg)**: skupiny zabezpečení sítě je filtr (Brána firewall), který obsahuje seznam zabezpečení pravidla, která při použití, povolit nebo odpírají síťový provoz prostředků připojených k virtuálním sítím Azure. [Další informace](https://docs.microsoft.com/azure/virtual-network/security-overview).
- **Šifrování dat**: Azure Disk Encryption je funkce, která umožňuje šifrovat disky virtuálních počítačů Windows a Linuxem v režimu IaaS. [Další informace](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest).

### <a name="work-with-the-azure-security-center"></a>Práce s Azure Security Center

Contoso je hledáte rychlý přehled o stavu zabezpečení jejich nové hybridní cloud a konkrétně jeho úlohami Azure.  Proto Contoso se rozhodla implementovat Azure Security Center spuštění s následujícími funkcemi: 

- Centralizovaná správa zásad
- Nepřetržité posuzování
- Užitečná doporučení 

#### <a name="centralize-policy-management"></a>Centralizujte si správu zásad

Centralizovaná správa zásad bude Contoso zajistit soulad s požadavky na zabezpečení díky centrální správě zásad zabezpečení napříč prostředími celý. Je můžete rychle a jednoduše implementovat zásady, které se vztahuje na všechny svoje prostředky Azure.

![Zásady zabezpečení](./media/contoso-migration-infrastructure/security-policy.png)

#### <a name="assess-and-action"></a>Vyhodnocení a akce

Contoso bude využívat posouzení zabezpečení, která monitoruje zabezpečení počítačů, sítí, úložiště, dat a aplikací. Chcete-li zjistit potenciální potíže se zabezpečením. 

- Security Center analyzuje stav zabezpečení výpočetních společnosti Contoso, infrastruktury a datovým prostředkům a aplikace Azure a služeb.
- Nepřetržité posuzování pomáhá provozní tým Contoso zjistit potenciální problémy se zabezpečením, jako jsou systémy s chybějícími aktualizacemi zabezpečení nebo zveřejněné síťové porty. 
- Zejména Contoso chce zajistit, aby že všechny své virtuální počítače jsou chráněné. Security Center pomáhá to ověření stavu virtuálních počítačů a provádění užitečným doporučení k nápravě ohrožení zabezpečení dříve, než jste zneužít.

![Monitorování](./media/contoso-migration-infrastructure/monitoring.png)

### <a name="work-with-nsgs"></a>Práce pomocí skupin zabezpečení sítě

Společnost Contoso může omezení síťového provozu směřujícího do prostředků ve virtuální síti pomocí skupin zabezpečení sítě.

- Skupina zabezpečení sítě obsahuje seznam pravidel zabezpečení, která povolují nebo odepírají příchozí nebo odchozí síťový provoz v závislosti na zdrojové nebo cílové IP adrese, portu a protokolu.
- Při použití k podsíti, pravidla se použijí na všechny prostředky v podsíti. Kromě síťových rozhraní jedná se instance služeb Azure v podsíti nasazené.
- Skupiny zabezpečení aplikací (asg) umožňují konfigurovat zabezpečení sítě jako přirozené rozšíření struktury aplikace, umožňuje seskupení virtuálních počítačů a definovat zásady zabezpečení sítě na základě těchto skupin.
    - Skupiny zabezpečení aplikací znamená, že můžete znovu použít zásady zabezpečení ve velkém měřítku bez potřeby ruční údržby explicitních IP adres. O složitost explicitních IP adres a několika skupin pravidel se stará platforma a vy se tak můžete zaměřit na obchodní logiku.
    - Skupinu zabezpečení aplikace můžete zadat jako zdroj a cíl v pravidlu zabezpečení. Po definování zásady zabezpečení můžete vytvořit virtuální počítače a přiřaďte síťová rozhraní virtuálního počítače do skupiny. 


Contoso implementuje směs skupin Nsg a asg. Je zajímají o správě skupiny zabezpečení sítě. Jsou to obavy o nadměrnému využití skupin zabezpečení sítě a složitosti, může to znamenat pro jejich provozní personál.  S myslete na to které přijaly dvě klíčové objekty, které používají obecným pravidlem:

- Veškerý provoz do a ze všech podsítí (sever jih), budou platit pravidlo skupiny zabezpečení sítě, s výjimkou GatewaySubnets v sítích rozbočovače.
- Skupiny Nsg pro podsíť a síťovou kartu skupin zabezpečení sítě budou chráněné všechny brány firewall nebo řadič domény.
- Všechny produkční aplikace bude mít skupiny asg použít.


Contoso má integrované modelu, jak to bude vypadat pro své aplikace.

![Zabezpečení](./media/contoso-migration-infrastructure/asg.png)


Skupiny zabezpečení sítě přidružené k skupiny asg se nakonfigurují s nejnižšími oprávněními, aby, která povoluje jenom pakety do konečného tok z jedné části sítě.

**Akce** | **Název** | **Zdroj** | **Cíl** | **Port**
--- | --- | --- | --- | --- 
Povolit | AllowiInternetToFE | Připojení typu VNET-HUB-EUS1/IB-TrustZone | POČÍTAČ APP1-FE 80, 443
Povolit | AllowWebToApp | FE POČÍTAČE APP1 | POČÍTAČ APP1-DB | 1433
Povolit | AllowAppToDB | APLIKACE SERVERU APP1 | Všechny | Všechny
Odepřít | DenyAllInbound | Všechny | Všechny | Všechny

### <a name="encrypt-data"></a>Šifrování dat

Azure Disk Encryption se integruje se službou Azure Key Vault a pomáhá řídit a spravovat šifrování disku klíčů a tajných kódů v předplatném služby key vault. Zajišťuje, že všechna data na discích virtuálních počítačů jsou zašifrovaná rest ve službě Azure storage.  

- Contoso bylo zjištěno, že konkrétní virtuální počítače vyžadovat šifrování.
- Se teď použít šifrování pro virtuální počítače s zákazníka, důvěrné, nebo data ICP.


## <a name="conclusion"></a>Závěr

V tomto článku Contoso nastavit infrastrukturu Azure a nastavit nebo plánované infrastruktury zásad pro předplatné Azure, hybridní identifikovat, zotavení po havárii, sítí, zásady správného řízení a zabezpečení. 

Všechny kroky, které Contoso dokončit, tady jsou požadovány pro migraci do cloudu. V jednoduchém případě se chtěli plánování síťové infrastruktury, který lze použít pro všechny typy migrace a jsou zabezpečené, odolné a škálovatelné. 

Pomocí této infrastruktury na místě budou připravené pro přesun a vyzkoušejte si migraci.

## <a name="next-steps"></a>Další postup

Jako první scénář migrace se chystáte Contoso [vyhodnotit své místní SmartHotel dvouvrstvém aplikaci pro migraci do Azure](contoso-migration-assessment.md). 
