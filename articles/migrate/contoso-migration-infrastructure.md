---
title: Contoso-nastavení infrastruktury a migrace | Microsoft Docs
description: Zjistěte, jak Contoso nastaví infrastruktury Azure pro migraci do Azure.
services: azure-migrate
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 06/19/2018
ms.author: raynew
ms.openlocfilehash: bf861dc6317a8cc3a3ed862dfd6c133a1dcbe685
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36232300"
---
# <a name="contoso---deploy-a-migration-infrastructure"></a>Contoso – nasazení infrastruktury migrace

Tento článek prověří, jak Contoso nastaví místní a infrastrukturu Azure, v rámci přípravy na migraci do Azure a ke spouštění firmy v hybridním prostředí.

- Je ukázková architektura, která je specifická pro společnost Contoso.
- Zda je nutné, aby všechny elementy, které jsou popsané v článku závisí na vaší strategie migrace. Například pokud vytváříte jenom cloudu nativní aplikace v Azure, můžete potřebovat méně složitých sítě struktura.

Tento dokument je druhý v řadě článků, které dokumentu jak fiktivní společnosti, který se migruje Contoso místních prostředků do cloudu Microsoft Azure. Řada obsahuje základní informace a sadu scénáře nasazení, která ukazuje, jak nastavit infrastruktury, a migrace vyhodnocení vhodnosti místních prostředků pro migraci a spouštět různé typy migrace. Scénáře růst v složitost a jsme přidali další články v čase.

**Článek** | **Podrobnosti** | **Stav**
--- | --- | ---
[Článek 1: Přehled](contoso-migration-overview.md) | Poskytuje přehled strategie migrace společnosti Contoso, řady článku a ukázkových aplikací, které používáme. | K dispozici.
Článek 2: Nasazení infrastruktury Azure (v tomto článku) | Popisuje, jak Contoso připraví jeho místní a infrastrukturu Azure pro migraci. Stejnou infrastrukturu se používá pro všechny scénáře migrace Contoso. | K dispozici.
[Článek 3: Vyhodnocení místních prostředků](contoso-migration-assessment.md) | Ukazuje, jak Contoso spouští posouzení své místní aplikace SmartHotel dvouvrstvá běžící ve VMware. Jejich vyhodnocení aplikace virtuálních počítačů s [Azure migrovat](migrate-overview.md) služby a aplikace databáze systému SQL Server s [Azure databáze migrace pomocníka](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | K dispozici.
[Článek 4: Metody opětovného hostování virtuální počítače Azure a spravované Instance SQL](contoso-migration-rehost-vm-sql-managed-instance.md) | Ukazuje, jak Contoso migruje SmartHotel aplikace do Azure. Jejich migraci virtuálních počítačů front-endu aplikace pomocí [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview)a databáze aplikace pomocí [migrace databáze Azure](https://docs.microsoft.com/azure/dms/dms-overview) služby migrovat do spravované Instance SQL. | K dispozici.
[Článek 5: Pro virtuální počítače Azure opětovným hostováním](contoso-migration-rehost-vm.md) | Ukazuje, jak Contoso migrovat své aplikace SmartHotel virtuální počítače pouze pomocí Site Recovery.
[Článek 6: Opětovným hostováním do Azure virtuálních počítačů a skupin dostupnosti SQL Server](contoso-migration-rehost-vm-sql-ag.md) | Ukazuje, jak Contoso migruje SmartHotel aplikace. Site Recovery používají k migraci aplikace virtuální počítače a migraci databáze službu, kterou chcete migrovat databázi aplikace do skupiny dostupnosti SQL Server. | K dispozici.
[Článek 7: Opětovným hostováním Linux aplikace pro virtuální počítače Azure](contoso-migration-rehost-linux-vm.md) | Ukazuje, jak Contoso migruje aplikace osTicket Linux virtuálních počítačích Azure. | K dispozici.
[Článek 8: Opětovným hostováním Linux aplikace pro virtuální počítače Azure a Azure MySQL Server](contoso-migration-rehost-linux-vm-mysql.md) | Ukazuje, jak Contoso migruje Linux osTicket aplikace pomocí Site Recovery a MySQL Workbench migrace (zálohování a obnovení) do instance serveru MySQL Azure. | K dispozici.

V tomto článku, který Contoso nastavit všechny prvky infrastruktury musí dokončit scénáře migrace. 


## <a name="overview"></a>Přehled

Předtím, než se můžete migrovat do Azure, je velmi důležité, aby Contoso připravit infrastrukturu.  Obecně platí existují pět široký oblastí, které potřebují k vezměte v úvahu:

**Krok 1: Předplatná Azure**: jak se budou nákupu Azure a komunikovat s platformy Azure a službami?  
**Krok 2: Hybridní identita**: jak se budou spravovat a řídit přístup k místním a prostředky Azure po migraci? Jak se rozšířit nebo přesunout do cloudu a správu identit?  
**Krok 3: Zotavení po havárii a odolnost proti**: jak se zajistí jejich aplikace a infrastrukturu odolné Pokud dojde k výpadkům a havárií?  
**Krok 4: Sítě**: jak by se návrhu sítě infrastruktury a navázat připojení mezi jejich místního datového centra a Azure?  
**Krok 5: Zabezpečení**: jak se budou zabezpečené jejich nasazení hybridní/Azure?  
**Krok 6: Zásad správného řízení**: jak se uchová jejich nasazení, v souladu s požadavky na zabezpečení a zásad správného řízení?

## <a name="before-you-start"></a>Než začnete

Než začneme prohlížení infrastrukturu, můžete chtít přečíst některé základní informace o možnosti Azure, které v tomto článku probereme:

- Existuje řada možností k dispozici pro nákup přístupu Azure, včetně průběžné platby, smluv Enterprise (EA) nebo otevřete licencování z prodejce Microsoft nebo z Microsoft Partners vědět jako poskytovatele cloudových řešení (CSP). Další informace o [možností nákupu](https://azure.microsoft.com/pricing/purchase-options/)a přečtěte si informace o tom [předplatná Azure jsou uspořádány](https://azure.microsoft.com/blog/organizing-subscriptions-and-resource-groups-within-the-enterprise/).
- Získat přehled o Azure [správu identit a přístupu](https://www.microsoft.com/en-us/trustcenter/security/identity). Na konkrétní informace o [Azure AD a rozšíření místní AD do cloudu](https://docs.microsoft.com/azure/active-directory/identity-fundamentals). Je užitečné ke stažení e knihu o [správu identit a přístupu (IAM) v hybridním prostředí](https://azure.microsoft.com/resources/hybrid-cloud-identity/).
- Azure poskytuje robustní síťová infrastruktura s možnostmi pro hybridní připojení. Získat přehled o [sítě a sítě řízení přístupu](https://docs.microsoft.com/azure/security/security-network-overview).
- Získejte Úvod do [zabezpečení Azure](https://docs.microsoft.com/azure/security/azure-security)a přečtěte si informace o vytváření plánu pro [zásad správného řízení](https://docs.microsoft.com/azure/security/governance-in-azure).


## <a name="on-premises-architecture"></a>Architektura na místě

Zde je diagram zobrazující aktuální místní infrastrukturu společnosti Contoso.

 ![Architektura contoso](./media/contoso-migration-infrastructure/contoso-architecture.png)  

- Contoso má jeden hlavní datovém centru, které jsou umístěné v městě New York ve východní USA.
- Mají tři další místní pobočky v USA.
- Hlavní datového centra je připojený k Internetu s fiber metro připojení k síti ethernet (500 MB/s).
- U každé větve je místně připojen k Internetu pomocí připojení třída firmy, tunelových propojení IPSec pro síť VPN zpět do hlavní datového centra. To umožňuje jejich celá síť být trvale připojené a optimalizuje připojení k Internetu.
- Hlavní datového centra je plně virtualizovaného s VMware. Mají dva virtualizace hostitele ESXi 6.5 spravovanou vCenter Server verze 6.5.
- Společnost Contoso využívá služby Active Directory pro správu identit a servery DNS v interní síti.
- Běží na virtuálních počítačích VMware řadiče domény v datovém centru. Řadiče domény v místní větví spustit na fyzických serverech.


## <a name="step-1-buy-and-subscribe-to-azure"></a>Krok 1: Koupit a přihlásit se k Azure

Contoso je potřeba zjistit, jak zakoupit Azure, jak architektury odběry a způsobu licencování službám a prostředkům.

### <a name="buy-azure"></a>Koupit Azure

Contoso se bude s [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/). To zahrnuje předem peněžních závazků do Azure, opravňující, aby vám skvělé výhody, včetně flexibilní možnosti fakturace a optimalizované ceny.

- Odhadované Contoso, co bude jejich roční tráví Azure. Když budou podepsané smlouvy, placené první roku v plném znění.
- Contoso musí používat všechny své závazky před roku je nad nebo by došlo ke ztrátě hodnota těchto odběru.
- Pokud z nějakého důvodu jejich překročit jejich závazků a náklady na další, Microsoft je fakturaci pro rozdíl.
- Náklady výše závazku bude na stejnou rychlostí a programů v jejich kontrakt. Neexistují žádné postihy nad.

### <a name="manage-subscriptions"></a>Správa předplatných

Po platícího pro Azure, třeba Contoso zjistit, jak spravovat své předplatné. Mají EA, a proto žádné omezení počtu předplatných Azure se můžete nastavit.

- Azure podnikového zápisu definuje, jak obrazce společnosti používá služby Azure a definuje základní struktura zásad správného řízení.
- Jako první krok Contoso určili struktury (označované jako enterprise vygenerované uživatelské rozhraní pro jejich podnikového zápisu. Používají [v tomto článku](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-manager-subscription-governance) aby mohl snadněji pochopit a návrhu vygenerované uživatelské rozhraní.
- Prozatím se rozhodla používat funkční přístup ke správě svých předplatných Contoso.
    - Uvnitř podniky mají jednoho oddělení IT, které řídí Azure nároky. To bude pouze skupina s odběry.
    - Tento model se budete rozšířit v budoucnosti, aby další podnikové skupiny se můžete zapojit do jako oddělení v podnikového zápisu.
    - Uvnitř IT oddělení společnosti Contoso má strukturovaná obě předplatná, provoz a vývoj.
    - Pokud Contoso vyžaduje další odběry v budoucnu, že budete muset spravovat přístup, zásady a dodržování předpisů pro tyto odběry. Bude se moct k tomu zavedením [skupin pro správu Azure](https://docs.microsoft.com/azure/azure-resource-manager/management-groups-overview), jako další vrstva nad odběry.

    ![Struktura organizace](./media/contoso-migration-infrastructure/enterprise-structure.png) 

### <a name="examine-licensing"></a>Zkontrolujte licencování

S odběry nakonfigurovaný můžete si prohlédnout Contoso jejich licenční Microsoft. Jejich licencování strategie bude záviset na prostředky, které chcete migrovat do Azure, a jak jsou vybrané a nasadit virtuální počítače Azure a služby. 

#### <a name="azure-hybrid-benefit"></a>Zvýhodněné hybridní využití Azure

Při nasazování virtuálních počítačů v Azure, standardní Image zahrnují licenci, která bude Contoso účtují podle minutu pro software používá. Ale Contoso byl dlouhodobé zákazníkem společnosti Microsoft a má udržovat EAs a otevřete licencí pomocí programu software assurance (SA). 

Azure Benefit hybridní poskytuje nákladově efektivní metodu pro migraci Contoso, tím, že se mají uložit na virtuálních počítačích Azure a SQL Server úlohy převod nebo opětovné použití systému Windows Server Datacenter a Standard edition licence pokryté programu Software Assurance. Tato akce povolí Contoso věnovat nižší rychlost založené na výpočetní pro virtuální počítače a serveru SQL Server. [Další informace](https://azure.microsoft.com/pricing/hybrid-benefit/).


#### <a name="license-mobility"></a>Mobilita licencí

Mobilita licencí v rámci SA poskytuje zákazníkům multilicenčního programu společnosti Microsoft jako Contoso flexibilitu nasazení aplikací oprávněné serveru s aktivní SA v Azure. Tím se eliminuje potřeba zakoupit nové licence. S žádné poplatky související mobility existující licence se dá snadno nasadit v Azure. [Další informace](https://azure.microsoft.com/pricing/license-mobility/).

#### <a name="reserve-instances-for-predictable-workloads"></a>Rezerva instancí předvídatelný úlohy

Předvídatelný úlohy jsou ty, které vždy musí být k dispozici s virtuálními počítači spuštěna. Například-obchodní aplikace, například systém SAP ERP.  Na druhé straně nepředvídatelným úlohy jsou ty, které jsou proměnné. Například virtuální počítače, které jsou v průběhu vysokou požadovat a vypnout na dobu mimo špičku.

![Rezervovaná instance](./media/contoso-migration-infrastructure/reserved-instance.png) 

Za pomocí vyhrazené instancí pro konkrétní instance virtuálních počítačů, které věděli, třeba udržovat velké dob trvání intervalu, můžete získat konzoly slevu i seřazený podle priority kapacity. Pomocí [vyhrazenou instancí Azure](https://azure.microsoft.com/pricing/reserved-vm-instances/), společně s Azure hybridní výhody, Contoso můžete ušetřit až 82 % vypnout regulární průběžné platby ceny (duben 2018).


## <a name="step-2-manage-hybrid-identity"></a>Krok 2: Správa hybridní identita

Poskytnutí a řízení přístupu uživatelů k prostředkům Azure s správu identit a přístupu (IAM) je důležitým krokem při stahování společně infrastruktury Azure.  

- Contoso se rozhodnou na jejich místní službu Active Directory rozšířit do cloudu, nikoli vytvořit nový samostatný systém v Azure.
- Uživatel vytvořit využitím Azure Active Directory k tomu.
- Contoso nemají Office 365 na místě, proto potřebují ke zřízení nového Azure AD.
- Office 365 používají Azure AD pro správu uživatelů. Pokud Contoso používali Office 365, by se už máte Azure AD principem a použít je jako jejich primární AD.
- [Další informace](https://support.office.com/article/understanding-office-365-identity-and-azure-active-directory-06a189e7-5ec6-4af2-94bf-a22ea225a7a9) o službě Azure AD pro Office 365 a další [postup přidání odběru](https://docs.microsoft.com/azure/active-directory/active-directory-how-subscriptions-associated-directory) do existující služby Azure AD.

### <a name="create-an-azure-ad"></a>Vytvoření Azure AD

Contoso používají bezplatná edice Azure AD, která má zahrnuté má předplatné Azure. Přidáte nový adresář AD následujícím způsobem:

1. V [portál Azure](http://portal.azure.com/), Contoso přejít na **vytvořit prostředek** > **Identity** > **Azure Active Directory**.
2. V **vytvořit adresář**, určí název adresáře, počáteční název domény a oblasti, ve kterém by vytvořit adresář Azure AD.

    ![Vytvoření služby Azure AD](./media/contoso-migration-infrastructure/azure-ad-create.png) 

    > [!NOTE]
    > Při vytváření adresáře v domainname.onmicrosoft.com formulář obsahuje počáteční název domény. Název nelze změnit ani odstranit. Místo toho je třeba přidat jejich registrovaný název domény do Azure AD.

### <a name="add-the-domain-name"></a>Přidání názvu domény

Chcete-li použít jejich název standardní domény, Contoso muset přidejte jej jako vlastní název do služby Azure AD. Tato možnost umožňuje správci přiřadit známé uživatelská jména. Například uživatel přihlásit pomocí e-mailovou adresu billg@contoso.com, namísto nutnosti billg@contosomigration.onmicrosoft.com. 

Pokud chcete nastavit vlastní název se přidat do adresáře, přidání položky DNS a pak ověřte název ve službě Azure AD.

1. V **vlastní názvy domén** > **přidat vlastní doménu**, zvyšují domény.
2. Položku DNS v Azure budou chtít použít zaregistrovat u svého registrátora domény. 

    - V **vlastní názvy domén** seznamu na vědomí informace DNS pro název. Contoso používají záznam MX.
    - Potřebují přístup k serveru název Uděláte to tak. V případě Contoso se přihlášení k doméně Contoso.com a vytvořit nový záznam MX pro položku DNS poskytovaný Azure AD, pomocí podrobnosti uvedené.  
1. Po rozšíření záznamy DNS, v názvu podrobnosti pro doménu, klepnutím na **ověřte** zkontrolujte vlastní název.

     ![Azure AD DNS](./media/contoso-migration-infrastructure/azure-ad-dns.png) 

### <a name="set-up-on-premises-and-azure-groups-and-users"></a>Nastavení místně a Azure skupin a uživatelů

Teď, když jejich Azure AD je spuštěná, Contoso potřeba přidat zaměstnance, aby místní skupiny AD, které se budou synchronizovat do Azure AD. Doporučujeme vám, že se používají názvy místní skupiny, které odpovídají názvy skupin prostředků v Azure. Díky tomu pak snadno identifikujete odpovídá za účelem synchronizace.

#### <a name="create-resource-groups-in-azure"></a>Vytvoření skupiny prostředků v Azure

Skupiny prostředků Azure shromažďování prostředků Azure. Pomocí ID skupiny prostředků umožňuje Azure k provádění operací s prostředky v rámci skupiny.

- Předplatné Azure může mít více skupin prostředků, ale skupiny prostředků může existovat pouze v rámci jednoho předplatného.
- Kromě toho jedna skupina prostředků může mít několik prostředků, ale prostředek může patřit do jedné skupiny.

Contoso nastavit skupin prostředků Azure, jak shrnuté v následující tabulce.

**Skupina prostředků** | **Podrobnosti**
--- | ---
**ContosoCobRG** | Tato skupina obsahuje všechny prostředky související s kontinuity podnikání (PALICE).  Její součástí jsou trezory, které Contoso se vytvoří při používání služby Azure Site Recovery a služba Azure Backup.<br/><br/> Bude také zahrnovat prostředky používané pro migraci, včetně migraci Azure a služby migrace databáze.
**ContosoDevRG** | Tato skupina obsahuje prostředky pro vývoj a testování.
**ContosoFailoverRG** | Tato skupina slouží jako cílová zóny pro převzal prostředky.
**ContosoNetworkingRG** | Tato skupina obsahuje všechny síťové prostředky.
**ContosoRG** | Tato skupina obsahuje materiály související s výrobní aplikace a databáze.

Vytváření skupin prostředků následujícím způsobem:

1. Na portálu Azure > **skupiny prostředků**, jejich přidání skupiny.
2. Pro každou skupinu určí název, předplatné, do které patří skupině a oblast.
3. Skupiny prostředků se zobrazí v **skupiny prostředků** seznamu.

    ![Skupiny prostředků](./media/contoso-migration-infrastructure/resource-groups.png) 


#### <a name="create-matching-security-groups-on-premises"></a>Vytvořit odpovídající zabezpečení skupiny na místě

1. Ve svojí místní službě Active Directory Contoso nastavit skupiny zabezpečení s názvy, které odpovídají názvy skupin prostředků Azure.
 
    ![Místní skupiny zabezpečení služby AD](./media/contoso-migration-infrastructure/on-prem-ad.png) 

2. Pro účely správy uživatel vytvořit další skupiny, která budou přidány do všech dalších skupin. Tato skupina bude mít práva pro všechny skupiny prostředků v Azure. Omezený počet globální správci budou přidáni do této skupiny.

### <a name="synchronize-ad"></a>Synchronizace AD

Contoso má poskytovat společnou identitu pro přístup k prostředkům na pracovišti i v cloudu. K tomu, že integrovat svojí místní službě Active Directory s Azure AD. Pomocí tohoto modelu:

- Uživatelé a organizace můžete využít výhod jedinou identitu pro přístup k místním aplikacím a cloudových služeb, jako je například Office 365 nebo tisíce jiné weby na Internetu.
- Správci mohou využívat skupiny ve službě Active Directory k implementaci [na základě řízení přístupu Role (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) v Azure.

Usnadňuje integraci, použijte Contoso [nástroj Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect). Když nainstalujete a nakonfigurujete nástroj na řadiči domény, synchronizuje místní místní AD identit do služby Azure AD. 

### <a name="download-the-tool"></a>Stáhněte si nástroj

1. Na portálu Azure, Azure přejde na **Azure Active Directory** > **Azure AD Connect**a stáhne nejnovější verzi nástroje na server budete používat pro synchronizaci.

    ![Stažení AD Connect](./media/contoso-migration-infrastructure/download-ad-connect.png) 

2. Spuštění **AzureADConnect.msi** instalace, použití **použít Expresní nastavení**. Toto je nejběžnější instalace a můžete použít pro jednoduchou doménovou strukturu s synchronizaci hodnoty hash hesla pro ověřování.

    ![Průvodce AD Connect](./media/contoso-migration-infrastructure/ad-connect-wiz1.png) 

3. V **připojit k Azure AD**, jejich zadat pověření pro připojení k Azure AD (ve formě CONTOSO\admin nebo contoso.com\admin).

    ![Průvodce AD Connect](./media/contoso-migration-infrastructure/ad-connect-wiz2.png) 

4. V **připojit ke službě AD DS**, jejich zadejte přihlašovací údaje pro své místní AD.

     ![Průvodce AD Connect](./media/contoso-migration-infrastructure/ad-connect-wiz3.png) 

5. V **připraveno ke konfiguraci**, klepnutím na **po dokončení konfigurace spustit proces synchronizace** pro okamžité spuštění synchronizace. Potom budou nainstalovat.


- Contoso má přímé připojení k Azure. Pokud místní AD je za proxy serverem, přečtěte si to [článku](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-connectivity).
- Po první synchronizaci místního AD objekty můžete zobrazit ve službě Azure AD.

    ![Místní AD v Azure](./media/contoso-migration-infrastructure/on-prem-ad-groups.png) 

- V každé skupině, na základě jejich role jsou reprezentované contoso IT tým.

    ![Místní AD členy v Azure](./media/contoso-migration-infrastructure/on-prem-ad-group-members.png) 

### <a name="set-up-rbac"></a>Nastavit RBAC

Azure [řízení přístupu na základě Role (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) umožňuje vyladění správy přístupu pro Azure. Pomocí řízení přístupu na základě role v Azure můžete uživatelům poskytnout pouze takovou úroveň přístupu, kterou potřebují k provádění svých úloh. Přiřadíte příslušné role RBAC uživatelům, skupinám a aplikacím na úrovni oboru. Předplatné, skupinu prostředků nebo jediný zdroj, může být oboru přiřazení role. 

Contoso nyní přiřadit role do skupiny AD, které se synchronizovaly z místní.

1. V **ControlCobRG** skupinu prostředků, kliknou **přístup k ovládacímu prvku (IAM)** > **přidat**.
2. V **přidat oprávnění** > **Role**, vyberou **Přispěvatel**a vyberte **ContosoCobRG** skupiny AD ze seznamu. Skupina se potom zobrazí v **vybrané členy** seznamu. 
3. To zopakují se stejnými oprávněními pro další skupiny zdrojů (s výjimkou **ContosoAzureAdmins**), můžete přidat oprávnění přispěvatele AD účtu, který odpovídá skupině prostředků.
4. Pro **ContosoAzureAdmins** skupiny AD, se přiřazení **vlastníka** role.

    ![Místní AD členy v Azure](./media/contoso-migration-infrastructure/on-prem-ad-groups.png) 


## <a name="step-3-design-for-resilience-and-disaster"></a>Krok 3: Návrh pro odolnost a po havárii

Prostředky Azure se nasazuje v rámci oblasti.
- Oblasti jsou uspořádány do zeměpisných a požadavky sídle, suverenity, dodržování předpisů a odolnosti dat se uplatní v rámci zeměpisné hranice.
- Oblasti se skládá ze sady datových centrech. Těchto datových center se nasazuje v rámci hraniční se definované latence a připojené prostřednictvím vyhrazenou místní síť s nízkou latencí.
- Každé oblasti Azure je spárován s v jiné oblasti pro odolnost proti chybám.
- Přečtěte si informace o [oblastí Azure](https://azure.microsoft.com/global-infrastructure/regions/)a pochopit [jak jsou spárovat oblasti](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).


Contoso jste se rozhodli přejděte s východní USA 2 (umístěný ve Virginia) jako jejich primární oblasti a střed USA jako jejich sekundární oblast. Existuje několik důvodů pro toto:

- Contoso datacenter se nachází v Brně a považují za latence do nejbližší datového centra.
- Oblast východní USA 2 má všechny služby a produkty, které potřebují používat. Ne všechny oblasti Azure jsou stejné z hlediska produkty a služby, které jsou k dispozici. Můžete zkontrolovat [produkty Azure podle oblasti](https://azure.microsoft.com/global-infrastructure/services/).
- Střed USA je oblast Azure spárované pro východní USA 2.

Jako názor na jejich hybridní prostředí, je potřeba zvážit, jak vytvářet odolnost a strategie zotavení po havárii do jejich oblasti návrhu Contoso. Širokém smyslu strategie rozsahu z jedné oblasti nasazení, který spoléhá na platformu Azure funkce, například jako domén selhání a místní párování pro odolnost, prostřednictvím úplné aktivní-aktivní modelu, ve které cloudové služby a databáze jsou nasazené a údržby Uživatelé ze dvou oblastí.

Jste se rozhodli trvat střední silniční contoso. Budete nasazovat jejich aplikace a prostředky v primární oblasti a zachovat celou infrastrukturu v sekundární oblasti, aby byl připraven fungovat jako v případě havárie kompletní aplikace nebo selhání oblasti úplnou zálohu.


## <a name="step-4-design-a-network-infrastructure"></a>Krok 4: Návrh síťové infrastruktury

V jejich oblasti návrhu zavedené Contoso připravení zvažte strategie sítě. Potřebují vezměte v úvahu jak jejich místního datového centra a Azure připojit a vzájemně komunikovat a navrhování infrastruktury sítě v Azure. Konkrétně potřebují:

**Plánování hybridní připojení k síti**: Zjistěte, jak jste přejdete k připojení sítí mezi místními a Azure.
**Návrh infrastruktury sítě Azure**: Rozhodněte, jak se budete nasazovat sítě prostřednictvím svých oblasti. Jak se sítí komunikují v rámci stejné oblasti a v oblastech.
**Návrh a nastavení sítě Azure**: nastavte sítě Azure a podsítě a rozhodnout, jaké se bude nacházet v nich.

### <a name="plan-hybrid-network-connectivity"></a>Plánování hybridní připojení k síti

Contoso považována za [počet architektury](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/) pro hybridních sítí mezi Azure a jejich místní datacentra. [Další informace](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/considerations) o porovnání možností.

Připomínáme Contoso místní síťové infrastruktury se skládá právě jejich datového centra v New Yorku a místní větví v části východní USA.  Všechny umístění mít obchodní třída připojení k Internetu.  Každé větve je připojen do datového centra prostřednictvím tunelu IPSec VPN přes internet.

![Síť společnosti Contoso](./media/contoso-migration-infrastructure/contoso-networking.png) 

Zde je, jak se rozhodli Contoso implementovat hybridní připojení:

1. Nastavte nové připojení site-to-site VPN mezi datovým centrem Contoso v Brně a dvou oblastech Azure ve východní USA 2 a střed USA.
2. Větev office provoz směřující pro virtuální sítě Azure bude směrovat přes hlavní datovém centru společnosti Contoso. 
3. Jak škálovat jejich nasazení Azure, že budete vytvořit spojení ExpressRoute mezi svá datacentra a oblastech Azure. V takovém případě zachovávají budete připojení site-to-site VPN pro převzetí služeb při selhání pouze pro účely.
    - [Další informace](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/considerations) o výběru mezi hybridní řešení sítě VPN a ExpressRoute.
    - Ověřte [ExpressRoute umístění a podpora](https://docs.microsoft.com/azure/expressroute/expressroute-locations-providers).


**Jenom síť VPN**

![Contoso VPN](./media/contoso-migration-infrastructure/hybrid-vpn.png) 


**Připojení VPN a ExpressRoute**

![Contoso VPN nebo ExpressRoute](./media/contoso-migration-infrastructure/hybrid-vpn-expressroute.png) 


### <a name="design-the-azure-network-infrastructure"></a>Návrh infrastruktury sítě Azure

Je důležité, aby Contoso převádí na místní způsobem, který umožňuje jejich hybridní nasazení zabezpečené a škálovatelné sítě. K tomuto účelu Contoso trvá dlouhodobý přístup a návrhu virtuální sítě (virtuální sítě) odolnost a enterprise, které jsou připravené. [Další informace](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm) o plánování virtuální sítě.

Pro připojení jejich dvou oblastí, se rozhodla Contoso implementovat model rozbočovače rozbočovače sítě:

- V každé oblasti Contoso použije střed a paprsek modelu.
- Pro připojení, sítě a rozbočovače, Contoso použije partnerský vztah Azure sítě.

#### <a name="network-peering"></a>Partnerský vztah sítě

Azure poskytuje sítě partnerský vztah pro připojení virtuální sítě a rozbočovače. Globální partnerský vztah umožňuje připojení mezi virtuálními sítěmi nebo centra v různých oblastech. Místní partnerský vztah připojí virtuálních sítí ve stejné oblasti. VNet peering poskytují řadu výhod:

- Síťový provoz mezi virtuálními sítěmi peered je soukromé.
- Přenos dat mezi virtuální sítě se ukládají na páteřní síti společnosti Microsoft. Při komunikaci mezi virtuální sítě není třeba žádné veřejného Internetu, brány nebo šifrování.
- Partnerský vztah poskytuje výchozí, nízkou latencí, širokopásmové připojení mezi prostředky v různých virtuálních sítí.

[Další informace](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) o partnerském vztahu sítě.

#### <a name="hub-to-hub-across-regions"></a>Hub na hub v oblastech

Contoso nasadí rozbočovač v každé oblasti. Rozbočovač virtuální síť (VNet) je v Azure, který funguje jako centrální bod připojení k síti na pracovišti. Rozbočovače virtuální sítě se připojit k sobě navzájem pomocí partnerský vztah globální virtuální sítě. Partnerský vztah globální sítě VNet v rámci oblasti Azure propojit virtuální sítě.

- Centrum každá oblast je peered do centra jeho partnery v jiné oblasti.
- Rozbočovače je peered do každé sítě v jeho oblasti a může připojit ke všem síťovým prostředkům.

    ![Globální partnerského vztahu](./media/contoso-migration-infrastructure/global-peering.png) 

#### <a name="hub-and-spoke-within-a-region"></a>Střed a paprsek v rámci oblasti

V každé oblasti nasadí Contoso virtuálních sítí pro různé účely, jako ramenem sítě z centra oblast. Virtuální sítě v rámci oblasti slouží k připojení k jejich rozbočovače a navzájem partnerský vztah.

#### <a name="design-the-hub-network"></a>Návrh sítě rozbočovače

V rámci hvězdicové model, který jste vybrali Contoso, potřebují myslíte o provozu z jejich místního datového centra a z Internetu, budou směrovány. Zde je, jak jste se rozhodli společnosti Contoso pro zpracování směrování pro východní USA 2 a střed USA rozbočovače:

- Navrhování jejich síť označuje jako "zpětné c", jak je to cesta ke příchozích pro odchozí síťových paketů podle pokynů.
- Jejich síťovou architekturu má dva hranice, nedůvěryhodné front-end hraniční zóny a zóny důvěryhodných serverů back-end.
- Brána firewall bude mít síťový adaptér v každé zóně, řízení přístupu k důvěryhodné zóny.
- Z Internetu:
    - Přenosy z Internetu, se setkají vyrovnáváním zatížení veřejnou IP adresu v hraniční síti.
    - Tento přenos je veden přes bránu firewall a pravidla brány firewall.
    - Až budete implementovat řízení přístupu sítě, provoz se předají do příslušného umístění do zóny důvěryhodných serverů.
    - Odchozí provoz z virtuální sítě, budou směrovány na Internet pomocí trasy definované uživatelem (udr). Provoz je vynutit přes bránu firewall a prověřovány souladu zásady společnosti Contoso.
- Z datového centra Contoso:
    - Příchozí přenos přes síť VPN site-to-site (nebo ExpressRoute) dotkne veřejnou IP adresu brány Azure VPN.
    - Provoz se směruje přes bránu firewall a pravidla brány firewall.
    - Po použití pravidla přenosy předávaly do interní nástroj (standardní SKU) v podsíti interní zóny důvěryhodných serverů.
    - Odchozí provoz z důvěryhodných podsítě do místního datového centra prostřednictvím sítě VPN je směrován přes brány firewall a pravidla použít před přechodem přes připojení site-to-site VPN.



### <a name="design-and-set-up-azure-networks"></a>Návrh a nastavení sítě Azure

Topologii sítě a směrování v místě Contoso jsou připravení nastavit své sítě Azure a podsítě.

- Contoso bude implementaci privátní sítě A třídu v Azure (0.0.0.0-127.255.255.255). Tento postup funguje, od místní aktuální mají 172.160.0/16 třídy B privátní adresy místo aby mohly být nesmí být žádné překryv mezi rozsahy adres.
- Se chystáte nasadit virtuálních sítí v jejich primární a sekundární oblasti.
- Budete používají zásady vytváření názvů, který zahrnuje předponu **VNET** a zkratka oblast **EUS2** nebo **VLAS**. Pomocí tento standard, bude mít název rozbočovače sítě **VNET-HUB-EUS2** (Východ USA 2), a **VNET-HUB-VLAS** (střed USA).
- Contoso nemají [řešení správy IP adres](https://docs.microsoft.com/windows-server/networking/technologies/ipam/ipam-top), takže je třeba naplánovat směrování sítě bez adres (NAT)


#### <a name="virtual-networks-in-east-us-2"></a>Virtuální sítě v oblasti Východ USA 2

Východní USA 2 je primární oblasti, která Contoso použijete k nasazení služby a prostředky. Zde je, jak se chystáte architekt sítě:

- **Centrum**: Centrum virtuální síť v oblasti Východ USA 2 je centrální bod primární připojení k jejich místní datacentra.
- **Virtuální sítě**: příčkou virtuálních sítí v oblasti Východ USA 2 lze použít k izolování úlohy v případě potřeby. Kromě sítě VNet rozbočovače bude mít Contoso ramenem dvou virtuálních sítí v oblasti Východ USA 2:
    - **VNET-DEV-EUS2**. Tato virtuální síť VNet bude poskytují že vývoj a testovací tým bude plně funkční síť pro projekty vývojářů. Bude fungovat jako výrobní pilotní oblast a bude záviset na provozní infrastruktuře funkce.
    - **Virtuální síť. PRODUKČNÍMU EUS2**: Azure IaaS produkční součásti budou umístěny v této síti. 
    -  Každý virtuální sítě budou mít svůj vlastní jedinečný adresního prostoru, se bez překrytí. Mají v úmyslu konfigurace směrování bez nutnosti adres (NAT)
- **Podsítě**:
    - V každé sítě pro každou vrstvu aplikace nebude podsíť
    - Každá podsíť v produkční síti bude mít odpovídající podsíť ve virtuální síti vývojářů.
    - Kromě toho produkční sítě má podsíť pro řadiče domény.

Virtuální sítě v oblasti Východ USA 2 jsou shrnuty v následující tabulce.

**Virtuální síť** | **rozsah** | **sdílené**
--- | --- | ---
**VNET-HUB-EUS2** | 10.240.0.0/20 | VNET-HUB-CUS2, VNET-DEV-EUS2, VIRTUÁLNÍ SÍŤ. PRODUKČNÍMU EUS2
**VNET-DEV-EUS2** | 10.245.16.0/20 | VNET-HUB-EUS2
**VIRTUÁLNÍ SÍŤ. PRODUKČNÍMU EUS2** | 10.245.32.0/20 | VNET-HUB-EUS2, VIRTUÁLNÍ SÍŤ. PRODUKČNÍMU VLAS

![Centrum nebo ramenem v primární oblasti](./media/contoso-migration-infrastructure/primary-hub-peer.png) 


#### <a name="subnets-in-the-east-us-2-hub-network-vnet-hub-eus2"></a>Podsítě v Východ USA 2 centru sítě (VNET-HUB-EUS2)

**Podsíť/zóny** | **CIDR** | ** Použitelných IP adresách
--- | --- | ---
**IB UntrustZone** | 10.240.0.0/24 | 251
**IB TrustZone** | 10.240.1.0/24 | 251
**OB UntrustZone** | 10.240.2.0/24 | 251
**OB TrustZone** | 10.240.3.0/24 | 251
**GatewaySubnets** | 10.240.10.0/24 | 251


#### <a name="subnets-in-the-east-us-2-dev-network-vnet-dev-eus2"></a>Podsítě v síti Dev 2 v Východ USA (VNET-DEV-EUS2)

Vývojový tým používá virtuální síť Dev jako výrobní pilotní oblast. Má tři podsítě.

**Podsíť** | **CIDR** | **adresy** | **V podsíti.**
--- | --- | --- | ---
**DEV-FE – EUS2** | 10.245.16.0/22 | 1019 | Frontends nebo webovou vrstvu virtuálních počítačů
**DEV-APP-EUS2** | 10.245.20.0/22 | 1019 | Virtuální aplikační vrstvě počítače
**DEV-DB-EUS2** | 10.245.24.0/23 | 507 | Virtuální počítače databáze


#### <a name="subnets-in-the-east-us-2-production-network-vnet-prod-eus2"></a>Podsítě v Východ USA 2 produkční sítě (virtuální sítě EUS2 PRODUKČNÍMU)

Azure IaaS součásti jsou umístěny v produkční sítě. Každá úroveň aplikace má vlastní podsíti. Podsítě shodují s těmi, které v síti vývojářů, a uveďte podsíť pro řadiče domény.

**Podsíť** | **CIDR** | **adresy** | **V podsíti.**
--- | --- | --- | ---
**PRODUKČNÍMU FE – EUS2** | 10.245.32.0/22 | 1019 | Frontends nebo webovou vrstvu virtuálních počítačů
**PRODUKČNÍMU. APLIKACE EUS2** | 10.245.36.0/22 | 1019 | Virtuální aplikační vrstvě počítače
**PRODUKČNÍMU DB-EUS2** | 10.245.40.0/23 | 507 | Virtuální počítače databáze
**PRODUKČNÍMU. ŘADIČ DOMÉNY EUS2** | 10.245.42.0/23 | 251 | Řadič domény virtuální počítače


![Síťová architektura centra](./media/contoso-migration-infrastructure/azure-networks-eus2.png)


#### <a name="virtual-networks-in-central-us-secondary-region"></a>Virtuální sítě v střed USA (sekundární oblast)

Střed USA je sekundární oblasti společnosti Contoso. Zde je, jak se chystáte architekt sítě:

- **Centrum**: rozbočovače virtuální síť v oblasti Východ USA 2 je centrální bod připojení k jejich místního datového centra a ramenem virtuálních sítí v oblasti Východ USA 2 lze použít k izolování úlohy v případě potřeby spravován od něj odděleně servery.
- **Virtuální sítě**: budou mít dvě virtuální sítě v střed USA:
    - VIRTUÁLNÍ SÍŤ PRODUKČNÍMU VLAS. Tato virtuální síť je produkční síť, podobně jako PROD_EUS2 virtuální sítě. 
    - VIRTUÁLNÍ SÍŤ AUTOMATICKÉ OBNOVENÍ SYSTÉMU VLAS. Tato virtuální síť VNet bude fungovat jako umístění, ve kterém se vytváří virtuální počítače po převzetí služeb při selhání z místní nebo jako umístění pro virtuální počítače Azure, které jsou při selhání z primárního na sekundární oblast. Tato síť je podobný produkčních sítích, ale bez všechny řadiče domény, na něm.
    -  Každý virtuální síť v oblasti, budou mít svůj vlastní adresní prostor s bez překrytí. Mají v úmyslu konfigurace směrování bez nutnosti adres (NAT)
- **Podsítě**: podsítí bude navržen podobným způsobem jako pro tyto ve východní USA 2. Výjimkou je, že již nepotřebují podsíť pro řadiče domény.

V následující tabulce jsou shrnuté virtuálních sítí v střed USA.

**Virtuální síť** | **rozsah** | **sdílené**
--- | --- | ---
**VNET-HUB-VLAS** | 10.250.0.0/20 | VNET-HUB-EUS2, VIRTUÁLNÍ SÍŤ. AUTOMATICKÉ OBNOVENÍ SYSTÉMU VLAS, VIRTUÁLNÍ SÍŤ. PRODUKČNÍMU VLAS
**VIRTUÁLNÍ SÍŤ. AUTOMATICKÉ OBNOVENÍ SYSTÉMU VLAS** | 10.255.16.0/20 | VNET-HUB-VLAS, VIRTUÁLNÍ SÍŤ. PRODUKČNÍMU VLAS
**VIRTUÁLNÍ SÍŤ. PRODUKČNÍMU VLAS** | 10.255.32.0/20 | VNET-HUB-VLAS, VIRTUÁLNÍ SÍŤ. AUTOMATICKÉ OBNOVENÍ SYSTÉMU VLAS, VIRTUÁLNÍ SÍŤ. PRODUKČNÍMU EUS2  


![Centrum nebo ramenem v spárované oblasti](./media/contoso-migration-infrastructure/paired-hub-peer.png)


#### <a name="subnets-in-the--central-us-hub-network-vnet-hub-cus"></a>Podsítě v centrální rozbočovač nám sítě (VNET-HUB-VLAS)

**Podsíť** | **CIDR** | **Použitelných IP adresách**
--- | --- | ---
**IB UntrustZone** | 10.250.0.0/24 | 251
**IB TrustZone** | 10.250.1.0/24 | 251
**OB UntrustZone** | 10.250.2.0/24 | 251
**OB TrustZone** | 10.250.3.0/24 | 251
**GatewaySubnet** | 10.250.2.0/24 | 251


#### <a name="subnets-in-the-central-us-production-network-vnet-prod-cus"></a>Podsítě v centrální nám produkční síť (VNET-PRODUKČNÍMU VLAS)

Paralelní k produkční síti v primární oblasti Východ USA 2 není v sekundární oblasti, střed USA produkční sítě. 

**Podsíť** | **CIDR** | **adresy** | **V podsíti.**
--- | --- | --- | ---
**PRODUKČNÍMU FE – VLAS** | 10.255.32.0/22 | 1019 | Frontends nebo webovou vrstvu virtuálních počítačů
**PRODUKČNÍMU. APLIKACE VLAS** | 10.255.36.0/22 | 1019 | Virtuální aplikační vrstvě počítače
**PRODUKČNÍMU DB-VLAS** | 10.255.40.0/23 | 507 | Virtuální počítače databáze
**PRODUKČNÍMU. ŘADIČ DOMÉNY VLAS** | 10.255.42.0/24 | 251 | Řadič domény virtuální počítače

#### <a name="subnets-in-the-central-us-failoverrecovery-network-in-central-us-vnet-asr-cus"></a>Podsítě v síti převzetí služeb při selhání nebo obnovení střed USA ve střed USA (VNET-automatické obnovení systému-VLAS)

Virtuální síť. automatické obnovení systému VLAS síť se používá pro účely převzetí služeb při selhání mezi oblastmi. Site Recovery se použije k replikaci a převzetí služeb při selhání virtuálních počítačích Azure mezi regiony. Taky funguje jako Contoso datacentra do síť Azure pro chráněné úlohy, které zůstávají v místě, ale převzetí služeb při selhání do Azure pro zotavení po havárii.

Virtuální síť. automatické obnovení systému VLAS je základní stejné podsíti jako provozní virtuální síť v oblasti Východ USA 2, ale bez nutnosti podsíť řadiče domény.

**Podsíť** | **CIDR** | **adresy** | **V podsíti.**
--- | --- | --- | ---
**AUTOMATICKÉ OBNOVENÍ SYSTÉMU FE – VLAS** | 10.255.16.0/22 | 1019 | Frontends nebo webovou vrstvu virtuálních počítačů
**AUTOMATICKÉ OBNOVENÍ SYSTÉMU. APLIKACE VLAS** | 10.255.20.0/22 | 1019 | Virtuální aplikační vrstvě počítače
**AUTOMATICKÉ OBNOVENÍ SYSTÉMU DB-VLAS** | 10.255.24.0/23 | 507 | Virtuální počítače databáze

![Síťová architektura centra](./media/contoso-migration-infrastructure/azure-networks-cus.png)

#### <a name="configure-peered-connections"></a>Konfigurace peered připojení

Centrum v každé oblasti bude peered k rozbočovači v jiné oblasti a všechny virtuální sítě v rámci oblasti rozbočovače. To umožňuje pro rozbočovače komunikovat a chcete-li zobrazit všechny virtuální sítě v rámci oblasti. Poznámky:

- Partnerský vztah vytvoří připojení obou stranách. Jeden od inicializace partnera na první sítě VNet a jiné jeden na druhý virtuální sítě.
- V hybridním nasazení provoz, který uplyne mezi rovnocennými počítači musí být viditelné z připojení VPN mezi Azure a místního datového centra. Chcete-li povolit, jsou některé konkrétní nastavení, které musejí být nastaveny na peered připojení.

Pro všechna připojení z ramenem virtuální sítě pomocí rozbočovače do místního datového centra Contoso musí povolit provoz přesměrovaná a propojeními VPN Gateway.

##### <a name="domain-controller"></a>Řadič domény

Pro řadiče domény v síti VNET-PRODUKČNÍMU EUS2 Contoso chce tok mezi EUS2 rozbočovače nebo produkční síť i přes připojení VPN k místnímu provozu. K tomu je nutné povolit následující:

1. **Povolit předávané pakety** a **povolit konfigurace brány přenosu** peered připojení. V našem příkladu by to byl VNET-HUB-EUS2 připojení VNET-PRODUKČNÍMU-EUS2.

    ![Partnerské vztahy](./media/contoso-migration-infrastructure/peering1.png)

2. **Povolit přesměrovaných přenosů** a **používat vzdálené brány** na druhé straně partnerského vztahu, na virtuální síť – PRODUKČNÍMU-EUS2 připojení VNET-HUB-EUS2.

    ![Partnerské vztahy](./media/contoso-migration-infrastructure/peering2.png)

3. Na místě, které bude nastavit statickou trasu, která přesměruje místní provoz směrovat přes tunelové propojení VPN do virtuální sítě. Na bráně, která poskytuje tunelového připojení sítě VPN ze společnosti Contoso do Azure by dokončit konfiguraci. Společnost Contoso využívá pro tento Windows pro směrování a vzdálený přístup.

    ![Partnerské vztahy](./media/contoso-migration-infrastructure/peering3.png)

##### <a name="production-networks"></a>Produkčních sítích 

Síť spoked sdílené neuvidí spoked sdílené sítě v jiné oblasti prostřednictvím rozbočovače.

Pro společnosti Contoso produkční sítě v obou oblastech zobrazíte mezi sebou musí vytvořit přímé peered připojení pro virtuální síť. PRODUKČNÍMU EUS2 a ECYKLACI. PRODUKČNÍMU VLAS. 

![Partnerské vztahy](./media/contoso-migration-infrastructure/peering4.png)

### <a name="set-up-dns"></a>Nastavení DNS

Když nasadíte prostředky ve virtuálních sítích, máte několik možností pro překlad názvu domény. Můžete použít překlad poskytovaný Azure nebo zadejte servery DNS pro rozlišení. Typ překladu adres, které můžete použít závisí na tom, jak vaše prostředky musí vzájemně komunikovat. Získat [informace](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#azure-provided-name-resolution) o službě Azure DNS.

Contoso jste se rozhodli, službu Azure DNS není vhodné v jejich hybridním prostředí. Místo toho se budete využívat své místní servery DNS.

- Vzhledem k tomu, že toto je síť hybridní všechny virtuální počítače na místní a v Azure musí být schopný přeložit názvy fungovat správně. To znamená, že vlastní nastavení DNS je nutné použít na všechny virtuální sítě.
- Contoso aktuálně máte řadiče domény nasazené v datovém centru společnosti Contoso a v pobočkách. Jejich primární servery DNS nejsou CONTOSODC1(172.16.0.10) a CONTOSODC2(172.16.0.1)
- Při nasazení virtuální sítě, nastaví se místní řadiče domény má být použit jako server DNS v sítě. 
- Konfigurace, při použití vlastní DNS na virtuální sítě, musí být Azure rekurzivní překladače IP adresa (například 168.63.129.16) přidá do seznamu DNS.  K tomuto účelu Contoso nakonfiguruje nastavení serveru DNS na každý virtuální sítě. Například vlastní nastavení DNS pro síť VNET-HUB-EUS2 vypadat takto:
    
    ![Vlastní DNS](./media/contoso-migration-infrastructure/custom-dns.png)

Kromě jim řadiče místní domény Contoso se chystáte implementovat čtyři další pro podporu své sítě Azure, dva jsou pro každou oblast. Zde je, co se budete nasazovat v Azure.

**Oblast** | **ŘADIČ DOMÉNY** | **Virtuální síť** | **Podsíť** | **IP adresa**
--- | --- | --- | --- | ---
EUS2 | CONTOSODC3 | VIRTUÁLNÍ SÍŤ. PRODUKČNÍMU EUS2 | PRODUKČNÍMU. ŘADIČ DOMÉNY EUS2 | 10.245.42.4
EUS2 | CONTOSODC4 | VIRTUÁLNÍ SÍŤ. PRODUKČNÍMU EUS2 | PRODUKČNÍMU. ŘADIČ DOMÉNY EUS2 | 10.245.42.5
VLAS | CONTOSODC5 | VIRTUÁLNÍ SÍŤ. PRODUKČNÍMU VLAS | PRODUKČNÍMU. ŘADIČ DOMÉNY VLAS | 10.255.42.4
VLAS | CONTOSODC6 | VIRTUÁLNÍ SÍŤ. PRODUKČNÍMU VLAS | PRODUKČNÍMU. ŘADIČ DOMÉNY VLAS | 10.255.42.4

Po nasazení řadiče domény v místě, Contoso potřeba aktualizovat nastavení služby DNS v sítích na buď oblast pro zahrnutí nových řadičů domény do jejich seznam serverů DNS.



#### <a name="set-up-domain-controllers-in-azure"></a>Nastavení řadiče domény v Azure

Po aktualizaci nastavení sítě, Contoso připraveni k sestavení se jim řadiče domény v Azure.

1. Na portálu Azure se nasadit nové virtuální počítače Windows serveru odpovídající virtuální síť.
2. Skupiny dostupnosti je vytvořena v každém umístění pro virtuální počítač. Skupiny dostupnosti, postupujte takto:
    - Ujistěte se, že prostředky infrastruktury Azure odděluje virtuálních počítačů do různých infrastruktur v oblasti Azure. 
    -  Umožňuje Contoso nároku 99,95 % SLA pro virtuální počítače v Azure.  [Další informace](https://docs.microsoftcom/azure/virtual-machines/windows/regions-and-availability#availability-sets).

    ![Skupiny dostupnosti](./media/contoso-migration-infrastructure/availability-group.png) 
3. Po nasazení virtuálního počítače budou pera rozhraní sítě pro virtuální počítač. Zde nastavují privátní IP adresu, které se statické a zadejte platnou adresu.

    ![SÍŤOVÝ ADAPTÉR VIRTUÁLNÍHO POČÍTAČE](./media/contoso-migration-infrastructure/vm-nic.png)

4. Nyní jsou připojit nový datový disk k virtuálnímu počítači. Tento disk obsahuje databázi služby Active Directory a sdílené složky sysvol. 
    - Velikost disku určí počet IOPS, která podporuje.
    - V čase velikost disku možná muset zvýšit s růstem prostředí.
    - Jednotka by neměl nastavena pro čtení a zápis pro použití mezipaměti u hostitele. Databáze služby Active Directory nepodporují to.

     ![Active Directory disku](./media/contoso-migration-infrastructure/ad-disk.png)

5. Po přidání disku připojit se k virtuálnímu počítači prostřednictvím vzdálené plochy a spusťte Správce serveru.
6. Potom v **Souborová služba a služba úložiště**, jejich spuštění Průvodce vytvořením svazku, zajistíte, že jednotka je dané písmeno F: nebo vyšší na místní počítač.

     ![Průvodce vytvořením svazku](./media/contoso-migration-infrastructure/volume-wizard.png)

7. Ve Správci serveru zvyšují **Active Directory Domain Services** role. Potom se nakonfigurovat virtuální počítač jako řadič domény.

      ![Role serveru](./media/contoso-migration-infrastructure/server-role.png)  

9. Po virtuální počítač nakonfigurovaný jako řadič domény a restartuje, otevřete Správce DNS a nakonfigurujte překladač Azure DNS jako server pro předávání.  To umožňuje řadič domény pro předávání dotazů DNS, které nelze vyřešit v Azure DNS.

    ![Server DNS pro předávání](./media/contoso-migration-infrastructure/dns-forwarder.png)

10. Nyní pak aktualizujte vlastní nastavení DNS pro každý virtuální síť s příslušného řadiče domény pro oblast virtuální sítě. Patří mezi ně řadiče místní domény v seznamu.

### <a name="set-up-active-directory"></a>Nastavení služby Active Directory

AD je důležité služba ve vytváření sítí a musí být správně nakonfigurované. Contoso se vytvořit AD weby pro datovém centru společnosti Contoso a EUS2 a VLAS oblasti.  

1. Uživatel vytvořit dva nové lokality (AZURE EUS2 a AZURE VLAS) společně s webu datového centra (ContosoDatacenter).
2. Po vytvoření lokalit, jejich vytvoření podsítí v lokalitách, tak, aby odpovídaly virtuálních sítí a datacenter.

    ![Řadič domény podsítě](./media/contoso-migration-infrastructure/dc-subnets.png)

3. Pak vytvořte dva propojení lokalit pro všechno, co připojení. Řadiče domény by pak přesunout na jejich umístění.

    ![Řadič domény odkazy](./media/contoso-migration-infrastructure/dc-links.png)

4. Poté, co je všechno nastavené, replikační topologie služby Active Directory je na místě.
    
    ![Replikace řadiče domény](./media/contoso-migration-infrastructure/ad-resolution.png)

5. V Centrum správy služby Active Directory v místě jsou uvedeny s všechno úplný seznam řadičů domény a webů.

    ![Centrum pro správu AD](./media/contoso-migration-infrastructure/ad-center.png)

## <a name="step-5-plan-for-governance"></a>Krok 5: Plánování zásad správného řízení

Azure poskytuje řadu ovládacích prvků zásad správného řízení v rámci služeb a platformy Azure. [Další informace](https://docs.microsoft.com/azure/security/governance-in-azure) pro základní znalosti možností.

Protože konfiguraci identity a řízení přístupu, Contoso již byl zahájen uvést některé aspekty zabezpečení a zásad správného řízení na místě. Obecně existují tři oblasti, která je nutné vzít v úvahu:

- **Zásady**: zásady v Azure platí a vynucuje pravidla a efekty přes vaše prostředky, takže prostředky zůstanou splňovat podnikové požadavky a SLA.
- **Zamkne**: Azure umožňuje zámku odběry, skupinách prostředků a jiné prostředky, tak, aby může být pouze upravit tyto s autoritou Uděláte to tak.
- **Značky**: prostředky lze řídit, auditovat a spravovat pomocí značek. Značky připojit k prostředkům, poskytuje informace o prostředky nebo vlastníci metadat.

### <a name="set-up-policies"></a>Nastavení zásad

Služba Azure zásady vyhodnotí vaše prostředky, kontrolu pro ty, které nejsou kompatibilní s definice zásady, které máte na místě. Například může mít zásadu, která jenom umožňuje určitý typ virtuálních počítačů, nebo vyžaduje prostředků tak, aby měl s konkrétní značkou tag. 

Azure zásady zadejte definici zásady a přiřazení zásad zadejte obor, ve které bude použito zásadu. Rozsah musí být v rozsahu skupiny pro správu do skupiny prostředků. [Další informace](https://docs.microsoft.com/azure/azure-policy/create-manage-policy) o vytváření a Správa zásad.

Contoso má začít pracovat s několika zásad:

- Chtějí zásadu a zajistit, že prostředky lze nasadit pouze v oblastech EUS2 a VLAS.
- Chtějí omezit SKU virtuálních počítačů do pouze schválené SKU. Záměrem je zajistit, že se nepoužívají nákladné SKU virtuálních počítačů.

#### <a name="limit-resources-to-regions"></a>Limit prostředky do oblasti

Společnosti Contoso použijte definice předdefinovaných zásad **povolené umístění** k omezení prostředků oblasti.

1. Na portálu Azure klikněte na tlačítko **všechny služby**a vyhledejte **zásad**.
2. Vyberte **přiřazení** > **přiřazení zásad**.
3. V seznamu zásad, vyberte **povolené umístění**.
4. Nastavit **oboru** na název předplatného Azure a vyberte dvou oblastí v seznamu povolených.

    ![Zásady povolené oblastí](./media/contoso-migration-infrastructure/policy-region.png)

5. Ve výchozím nastavení je zásada nastavená s **Odepřít**, znamená, že pokud někdo spustí nasazení v odběru, který není v EUS2 nebo VLAS, instalace se nezdaří. Zde je, co se stane, když někdo v rámci předplatného Contoso pokusí nastavit nasazení v západní USA.

    ![Zásady se nezdařilo](./media/contoso-migration-infrastructure/policy-failed.png)

#### <a name="allow-specific-vm-skus"></a>Povolit konkrétní SKU virtuálních počítačů

Contoso použije definice předdefinovaných zásad **měly virtuální počítače SKU** pro omezení typů virtuálních počítačů, které lze vytvořit v rámci předplatného. 

![Zásady SKU](./media/contoso-migration-infrastructure/policy-sku.png)



#### <a name="check-policy-compliance"></a>Kontrola dodržování zásad

Zásady přejděte platit okamžitě a Contoso můžete zkontrolovat prostředky pro dodržování předpisů. 

1. Na portálu Azure klikněte **dodržování předpisů** odkaz.
2. Zobrazí se na řídicím panelu dodržování předpisů. Můžete k podrobnostem další podrobnosti.

    ![Dodržování zásad](./media/contoso-migration-infrastructure/policy-compliance.png)


### <a name="set-up-locks"></a>Nastavit zámky.

Contoso má dlouho používá rozhraní ITIL pro správu jejich systémy. Jedním z nejdůležitějších aspektů rozhraní je změnit ovládací prvek a Contoso chce zajistit, že řízení změn je implementovaná v jejich nasazení Azure.

Contoso se chystáte implementovat zámky následujícím způsobem:

- Žádné produkční nebo převzetí služeb při selhání součásti musí být ve skupině prostředků, které je jen pro čtení zámku.  To znamená, že k úpravě nebo odstranění produkční položky, musí odebrat zámek. 
- Skupiny prostředků není produkční bude mít CanNotDelete zámky. To znamená, že oprávnění uživatelé mohou číst nebo upravit prostředek, ale nelze ho proto odstranit.

[Další informace](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources) o zámky.

### <a name="set-up-tagging"></a>Nastavit označování

Ke sledování prostředků, jak jste přidány, bude stále důležité pro společnost Contoso prostředky přidružit příslušné oddělení, zákazníků a prostředí. 

Kromě poskytování informací o prostředcích a vlastníky, povolí značky společnosti Contoso agregační a skupiny prostředků a tato data použít pro účely vracení peněz.

Contoso je potřeba vizualizovat svoje prostředky Azure způsobem, který dává smysl pro jejich podnikání. Pro příklad, ale role nebo oddělení. Všimněte si, že nebudete muset prostředky jsou umístěny ve stejné skupině prostředků se stejnou značkou. K tomuto účelu vytvoří Contoso taxonomii jednoduchých značek tak, aby všichni používají stejné značky.

**Název značky** | **Hodnota**
--- | ---
Nákladové středisko | 12345: musí být platný nákladové středisko ze SAP.
Organizační jednotku | Název organizační jednotky (od SAP). Odpovídá CostCenter.
ApplicationTeam | E-mailový alias týmu, který vlastní podporu pro aplikace.
Název_katalogu | Název aplikace nebo ShareServices, za katalogu služeb, který prostředek podporuje.
Portálu Service Manager | E-mailový alias Service Manager ITIL pro prostředek.
COBPriority | Priorita pro BCDR nastavit podle firmy. Hodnoty 1-5.
ENV | VÝVOJ, STG, PRODUKČNÍMU jsou možné hodnoty. Představující, vývoj, přípravné nebo produkční prostředí.

Příklad: 

 ![Azure značky](./media/contoso-migration-infrastructure/azure-tag.png)

Po vytvoření značky, bude Contoso přejděte zpět a vytvořit nové definice Azure zásady a přiřazení vynutit používání potřebné značky celé organizace.


## <a name="step-6-consider-security"></a>Krok 6: Zvažte zabezpečení

Zabezpečení je velmi důležitý v cloudu a Azure nabízí širokou škálu zabezpečení nástroje a možnosti. Ty vám pomohou vytvořit zabezpečené řešení, na zabezpečené platformy Azure. Čtení [důvěru důvěryhodné cloudu](https://azure.microsoft.com/overview/trusted-cloud/) Další informace o zabezpečení Azure.

Existuje několik hlavní součásti Contoso vzít v úvahu

- **Azure Security Center**: Azure Security Center nabízí Správa jednotná zabezpečení a ochrana před internetovými útoky pokročilé napříč hybridní Cloudová pracovní prostředí. Se službou Security Center můžete používat zásady zabezpečení napříč úlohami, omezit vystavení hrozbám a detekovat útoky a reagovat na ně.  [Další informace](https://docs.microsoft.com/azure/security-center/security-center-intro).
- **Skupin zabezpečení (Nsg) sítě**: NSG je filtr (brány firewall), který obsahuje seznam zabezpečení pravidla, která při použití, povolit nebo odepřít síťový provoz na prostředky, které jsou připojené k virtuálním sítím Azure. [Další informace](https://docs.microsoft.com/azure/virtual-network/security-overview).
- **Šifrování dat**: Azure Disk Encryption je funkce, která pomáhá šifrování disky virtuálního počítače s Windows a Linux IaaS. [Další informace](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest).

### <a name="work-with-the-azure-security-center"></a>Práce s Azure Security Center

Contoso je hledá rychlý přehled do postavení zabezpečení jejich nové hybridní cloud a konkrétně Azure zatížení.  V důsledku toho Contoso se rozhodla implementovat Azure Security Center počínaje následující funkce: 

- Centralizovaná správa zásad
- Průběžné hodnocení
- Užitečná doporučení 

#### <a name="centralize-policy-management"></a>Centralizovat správu zásad

S centralizovaných zásad správy bude Contoso zajištění souladu s požadavky na zabezpečení podle centrálně spravovat zásady zabezpečení v celé prostředí. Se můžete rychle a jednoduše implementovat zásady, která se vztahuje na všechny svoje prostředky Azure.

![Zásady zabezpečení](./media/contoso-migration-infrastructure/security-policy.png)

#### <a name="assess-and-action"></a>Vyhodnocení a akce

Contoso využije vyhodnocení průběžné zabezpečení, která monitoruje zabezpečení počítačů, sítí, úložiště, data a aplikace; Chcete-li zjistit potenciální potíže se zabezpečením. 

- Security Center analyzuje stav zabezpečení společnosti Contoso výpočetní infrastruktury a datových prostředků a Azure aplikací a služeb.
- Průběžné assessment pomáhá provozní tým Contoso ke zjištění možných problémů se zabezpečením, jako je například systémy s chybějícími aktualizacemi zabezpečení nebo vystavené síťové porty. 
- Zejména Contoso chce Ujistěte se, že všechny jejich virtuální počítače jsou chráněné. Security Center pomáhá s tím, ověření stavu virtuálního počítače a provádění doporučení seřazený podle priority a možné použít k nápravě ohrožení zabezpečení než jejich jste zneužití.

![Monitorování](./media/contoso-migration-infrastructure/monitoring.png)

### <a name="work-with-nsgs"></a>Práce s skupiny Nsg

Contoso můžete omezit síťový provoz na prostředky ve virtuální síti pomocí skupin zabezpečení sítě.

- Skupina zabezpečení sítě obsahuje seznam pravidel zabezpečení, která povolují nebo odepírají příchozí nebo odchozí síťový provoz v závislosti na zdrojové nebo cílové IP adrese, portu a protokolu.
- Při použití k podsíti, pravidla se používají ke všem prostředkům v podsíti. Kromě síťových rozhraní jedná se o instancí služby Azure, které jsou nasazené v podsíti.
- Skupiny zabezpečení aplikací (ASGs) umožňují nakonfigurovat jako přirozené rozšíření strukturu aplikace, abyste pro virtuální počítače skupiny zabezpečení sítě a definovat zásady zabezpečení sítě na základě těchto skupin.
    - Skupiny zabezpečení aplikace znamená, že vaše zásady zabezpečení ve velkém měřítku bez ruční údržbu explicitní IP adres můžete znovu použít. O složitost explicitních IP adres a několika skupin pravidel se stará platforma a vy se tak můžete zaměřit na obchodní logiku.
    - Skupinu zabezpečení aplikace můžete zadat jako zdroj a cíl v pravidlu zabezpečení. Po definování vaše zásady zabezpečení můžete vytvořit virtuální počítače a přiřazení síťové adaptéry virtuálních počítačů do skupiny. 


Contoso se implementovat kombinaci skupiny Nsg a ASGs. Je zajímají o správě NSG. Jsou obavy o nadměrné využití skupiny Nsg a složitost, které může znamenat pro jejich provozní personál.  Myslete na to které přijaly dva klíče objekty, používají obecně platí:

- Veškerý provoz do a ze všech podsítí (severojižní), budou platit pravidlo NSG, s výjimkou GatewaySubnets v sítích rozbočovače.
- Pomocí skupin Nsg podsítě a síťový adaptér Nsg budou chráněné všechny brány firewall nebo řadič domény.
- Všechny produkční aplikace bude mít ASGs použít.


Contoso má integrovaný model jak to bude vypadat pro své aplikace.

![Zabezpečení](./media/contoso-migration-infrastructure/asg.png)


Skupiny Nsg přidružená ASGs nakonfigurují nejnižší oprávnění k zajištění, že který povoleny pouze paketů můžete toku z jedné části sítě do cíle.

**Akce** | **Název** | **Zdroj** | **cíl** | **Port**
--- | --- | --- | --- | --- 
Povolit | AllowiInternetToFE | VNET-HUB-EUS1/IB-TrustZone | APP1-FE 80 A 443
Povolit | AllowWebToApp | APP1-FE | APP1-DB | 1433
Povolit | AllowAppToDB | APLIKACE SERVERU APP1 | Všechny | Všechny
Odepřít | DenyAllInbound | Všechny | Všechny | Všechny

### <a name="encrypt-data"></a>Šifrování dat

Integruje se službou Azure Key Vault pomáhá řídit a spravovat disku šifrovacích klíčů a tajných klíčů v trezoru klíčů předplatné Azure Disk Encryption. Zajišťuje, že všechna data na disky virtuálních počítačů jsou zašifrovaná umístěná v úložišti Azure.  

- Contoso bylo zjištěno, že konkrétní virtuální počítače vyžadovat šifrování.
- Se chystáte použít šifrování pro virtuální počítače s zákazníka, důvěrné, nebo ICP data.


## <a name="conclusion"></a>Závěr

V tomto článku Contoso nastavit jejich infrastrukturu Azure a nastavit nebo plánované infrastruktury zásad pro předplatné Azure, hybridní identifikovat, zotavení po havárii, sítí, zásady správného řízení a zabezpečení. 

Ne všechny kroky, které Contoso dokončit tady jsou povinné pro migraci do cloudu. V jejich případě se chtěli plánování infrastruktury sítě, které lze použít pro všechny typy migrace a jsou v bezpečí, odolné a škálovatelné. 

Pomocí této infrastruktury na místě jsou připravení přesunout a vyzkoušejte migrace.

## <a name="next-steps"></a>Další postup

Jako první scénář migrace, se chystáte Contoso [vyhodnocení jejich místní SmartHotel Dvojúrovňová aplikace pro migraci na Azure](contoso-migration-assessment.md). 
