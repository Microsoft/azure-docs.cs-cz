---
title: Klíčových funkcích a konceptech v Azure stacku | Dokumentace Microsoftu
description: Další informace o klíčových funkcích a konceptech v Azure stacku.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: 09ca32b7-0e81-4a27-a6cc-0ba90441d097
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/14/2019
ms.author: jeffgilb
ms.reviewer: unknown
ms.openlocfilehash: 1b533c945fdcfc3d1072a7d8a513126ca3f1f72a
ms.sourcegitcommit: 70471c4febc7835e643207420e515b6436235d29
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2019
ms.locfileid: "54303580"
---
# <a name="key-features-and-concepts-in-azure-stack"></a>Klíčových funkcích a konceptech v Azure stacku
Pokud se službou Microsoft Azure Stack teprve začínáte, může být užitečné tyto podmínky a popis funkcí.

## <a name="personas"></a>Osoby
Existují dva typy prvků uživatele pro Microsoft Azure Stack, operátor a uživatele.

* Azure Stack **operátor** Azure Stack můžete nakonfigurovat tak, že správa nabídek, plánů, služeb, kvóty a ceny k poskytnutí prostředků pro svého tenanta uživatele. Operátory také spravovat kapacity a reakce na výstrahy.  
* Azure Stack **uživatele** (označované také jako tenant) využívá služby, které nabízí operátor. Uživatele můžete zřizovat, monitorovat a spravovat služby, které jsou odběru jste přihlášeni, jako jsou webové aplikace, úložiště a virtuální počítače.

## <a name="portal"></a>Portál
Primární metody komunikace s Microsoft Azure Stack jsou portál pro správu, portál user portal a PowerShell.

Na portálech Azure stacku každého využívají samostatných instancí služby Azure Resource Manageru. Operátor používá na portálu pro správu ke správě služby Azure Stack a provádět kroky, jako je vytvoření tenanta nabídek. Portál user portal (také označované jako portál pro klienty) poskytuje samoobslužné prostředí pro používání cloudové prostředky, jako jsou virtuální počítače, účty storage a web apps. Další informace najdete v tématu [používání portálů správce a uživatele Azure stacku](azure-stack-manage-portals.md).

## <a name="identity"></a>Identita 
Azure Stack používá jako zprostředkovatel identity Azure Active Directory (Azure AD) nebo Active Directory Federation Services (AD FS).  

### <a name="azure-active-directory"></a>Azure Active Directory
Azure AD je zprostředkovatel identity založené na cloudu, více tenantů od Microsoftu. Většina hybridních scénářů používat jako úložiště identit Azure AD.

### <a name="active-directory-federation-services"></a>Active Directory Federation Services
Můžete používat Active Directory Federation Services (AD FS) pro odpojené nasazení služby Azure Stack. Poskytovatele prostředků služby Azure Stack a další aplikace fungovat stejně jako s Azure AD téměř stejným způsobem se službou AD FS. Azure Stack zahrnuje svoji vlastní instanci služby Active Directory a Active Directory Graph API. Azure Stack Development Kit podporuje následující scénáře služby AD FS:

- Přihlaste se k nasazení pomocí služby AD FS.
- Vytvoření virtuálního počítače s tajnými kódy ve službě Key Vault
- Vytvořte trezor pro ukládání a přístup k tajných kódů
- Vytvoření vlastní role RBAC v předplatném
- Přiřazení uživatelů k rolím RBAC pro prostředky
- Vytvoření role RBAC systémová prostřednictvím Azure Powershellu
- Přihlaste se jako uživatel prostřednictvím Azure Powershellu
- Vytvoření služby objekty zabezpečení použít pro přihlášení k Azure Powershellu


## <a name="regions-services-plans-offers-and-subscriptions"></a>Oblasti, služby, plány, nabídky a předplatné
Ve službě Azure Stack služby jsou poskytována pro tenanty používající oblastí, předplatná, nabídky a plány. Tenanti mohou přihlásit k několika nabídky. Nabídek může mít jednoho nebo několika plánů a plány může mít jednu nebo víc služeb.

![](media/azure-stack-key-features/image4.png)

Příklad hierarchie odběrů tenanta nabídky, každý s použitím různých plánů a služeb.

### <a name="regions"></a>Oblasti
Oblasti Azure Stack, které jsou základního prvku objektu škálování a správy. Organizace může mít více oblastí s prostředky, které jsou k dispozici v jednotlivých oblastech. Oblasti mohou mít i jiné služby nabídek, které jsou k dispozici. V Azure Stack Development Kit je podporována pouze jedna oblast a automaticky s názvem *místní*.

### <a name="services"></a>Služby
Microsoft Azure Stack umožňuje poskytovatelům poskytovat celou řadu služeb a aplikací, jako jsou virtuální počítače, SQL Server databáze, SharePoint, Exchange a další.

### <a name="plans"></a>Plány
Plány představují seskupení jedné nebo více služeb. Jako poskytovatel můžete vytvořit plány, které nabídnete svým tenantům. Vaši tenanti se pak můžou přihlásit k odběru nabídek, aby mohli používat plány a služby, které k nim patří.

Každá služba přidány do plánu můžete nakonfigurovat s nastavením kvóta vám pomohou při správě kapacity cloudu. Kvóty může obsahovat omezení, jako je například omezení virtuálních počítačů, RAM a využití procesoru a se vztahují na předplatné uživatele. Kvóty můžou lišit podle umístění. Plán obsahující výpočetních služeb, z oblasti A může mít například kvóty dva virtuální počítače, 4 GB paměti RAM a 10 jader procesoru.

Při vytváření nabídky, Správce služeb můžete zahrnout **základní plán**. Tyto základní plány jsou zahrnuté ve výchozím nastavení, pokud se klient přihlásí k této nabídce. Když uživatel přihlásí (a vytvoření odběru), má uživatel přístup pro všechny poskytovatele prostředků podle těchto základních plánů (s odpovídajícím kvóty).

Správce služeb můžete také zahrnout **doplňkové plány** do nabídky. Doplňkové plány nejsou zahrnuté ve výchozím nastavení v rámci předplatného. Doplňkové plány jsou další plány (kvóty) k dispozici v nabídce, vlastník předplatného můžete přidat do svých předplatných.

### <a name="offers"></a>Nabídky
Nabídky jsou skupiny jednoho nebo několika plánů, které poskytovatelé předkládají tenantům ke koupi (přihlášení k odběru). Alfa nabízí může například obsahovat plánu A který obsahuje sadu výpočetních služeb a plánování B obsahující sadu služeb úložiště a sítě.

Nabídka obsahuje sadu základních plánů a správci služby můžou vytvářet doplňkové plány, které klienty můžete přidat do svého předplatného.

### <a name="subscriptions"></a>Předplatná
Předplatné je, jak zakoupit vaše nabídky tenantů. Předplatné je kombinací tenanta nabídkou. Klient může mít předplatná na více nabídky. Každé předplatné se týká jenom jednu nabídku. Předplatná tenanta rozhodněte, které plány/služby přístupem.

Předplatných pomůžou poskytovatelé uspořádat a přístup ke cloudovým prostředkům a službám.

Pro správce je vytvořen výchozí předplatné poskytovatele. během nasazení. Toto předplatné je možné spravovat služby Azure Stack, nasadit další poskytovatele prostředků a vytvořte plány a nabídky pro klienty. Není vhodné používat pro spouštění úloh zákazníka a aplikací. Počínaje verzí 1804, dva další předplatná doplňují výchozí zprostředkovatel předplatné; předplatné měření a využití předplatného. Tyto doplňky usnadňují oddělení správu základní infrastruktury, poskytovatelů dalších prostředků a úloh.  

## <a name="azure-resource-manager"></a>Azure Resource Manager
Pomocí Azure Resource Manageru můžete pracovat s prostředky infrastruktury založené na šablonách, deklarativní modelu.   Poskytuje jednotné rozhraní, který vám pomůže nasadit a spravovat komponenty svého řešení. Úplné informace a pokyny najdete v tématu [přehled Azure Resource Manageru](../azure-resource-manager/resource-group-overview.md).

### <a name="resource-groups"></a>Skupiny prostředků
Skupiny prostředků je kolekce prostředků, služeb a aplikací – a každý prostředek má typ, jako jsou virtuální počítače, virtuální sítě, veřejné IP adresy, účty úložiště a websites. Každý prostředek musí být ve skupině prostředků a tak skupiny prostředků pomáhají logicky uspořádat prostředky, jako například úlohy nebo umístění. Ve službě Azure Stack jsou také spravované prostředky, jako jsou plány a nabídky ve skupinách prostředků.

Na rozdíl od [Azure](../azure-resource-manager/resource-group-move-resources.md), prostředky služby Azure Stack nelze přesouvat mezi skupinami prostředků. Když máte zobrazeny vlastnosti prostředku nebo skupinu prostředků na portálu pro správu služby Azure Stack *přesunout* šedě a není k dispozici je tlačítko. Kromě toho používání **změnit skupinu prostředků** nebo **změnit předplatné** také akce ze skupiny prostředků nebo vlastností položky skupiny prostředků nepodporuje. Všechny provedené pokusy o přesunutí se operace nezdaří.
 
### <a name="azure-resource-manager-templates"></a>Šablony Azure Resource Manageru
S Azure Resource Manageru můžete vytvořit šablonu (ve formátu JSON), která definuje nasazení a konfiguraci vaší aplikace. Tato šablona se označuje jako šablony Azure Resource Manageru a nabízí deklarativní způsob, jak definovat nasazení. Pomocí šablony můžete aplikaci opakovaně nasadit v průběhu životního cyklu a mít přitom jistotu, že se prostředky nasadí konzistentně.

## <a name="resource-providers-rps"></a>Poskytovatelé prostředků (předávajících stran)
Poskytovatelé prostředků jsou webové služby, které tvoří základ pro všechny založené na Azure IaaS a PaaS služby. Azure Resource Manageru závisí na jiné RPs a zajistit tak přístup ke službám.

Existují čtyři základní RPs: Síť, úložiště, výpočetní prostředky a trezor klíčů. Každá z těchto RPs umožňuje konfigurovat a spravovat jeho příslušné prostředky. Správce služeb můžete také přidat nové vlastní prostředek zprostředkovatele.

### <a name="compute-rp"></a>COMPUTE RP
Poskytovatele výpočetních prostředků (CRP) umožňuje klientům služby Azure Stack můžete vytvořit své vlastní virtuální počítače. CRP zahrnuje schopnost vytvářet virtuální počítače, stejně jako rozšíření virtuálního počítače. Do rozšíření služby Virtual Machines pomáhá poskytovat schopnosti IaaS pro virtuální počítače s Windows a Linux.  Jako příklad slouží ke zřízení virtuálního počítače s Linuxem a spouštění skriptů Bash během nasazování do konfigurace virtuálního počítače CRP.

### <a name="network-rp"></a>Síť poskytovatele prostředků
Poskytovatel prostředků sítě (NRP) nabízí řadu funkce softwarově definované sítě (SDN) a síťové funkce virtualizace (NFV) pro daný privátní cloud.  Vytvoření zdroje, jako jsou softwaru zatížení vyrovnávání, veřejné IP adresy, skupiny zabezpečení sítě, virtuální sítě můžete použít NRP.

### <a name="storage-rp"></a>Úložiště RP
Poskytovatele prostředků úložiště zajišťuje čtyř služeb konzistentních s Azure storage: Objekt blob, tabulky, fronty a správu účtů. Také nabízí Cloudová služba správy úložiště pro usnadnění správy služeb poskytovatele služeb konzistentních s Azure Storage. Azure Storage nabízí flexibilitu při ukládání a načítání velkého objemu nestrukturovaných dat, jako jsou dokumenty a mediální soubory s objekty BLOB Azure, a strukturované NoSQL na základě dat pomocí tabulek Azure. Další informace o Azure Storage najdete v tématu [Úvod do Microsoft Azure Storage](../storage/common/storage-introduction.md).

#### <a name="blob-storage"></a>Blob Storage
BLOB storage ukládá všechny datové sady. Objekt blob může být jakýkoli druh textu nebo binárních dat, jako je dokument, soubor médií nebo instalátor aplikace. Table storage ukládá strukturované datové sady. Table Storage je datové úložiště na bázi NoSQL typu klíč-atribut, které umožňuje rychlý vývoj a přístup k velkým objemům dat. Queue storage poskytuje spolehlivé zasílání zpráv pro zpracování úloh a komunikaci mezi komponentami cloudových služeb.

Každý objekt blob se organizuje podle kontejneru. Kontejnery také nabízejí praktický způsob přiřazení zásad zabezpečení skupinám objektů. Účet úložiště může obsahovat libovolný počet kontejnerů a kontejner může obsahovat libovolný počet objektů BLOB až do 500 TB kapacity účtu úložiště. Úložiště Blob nabízí tři typy objektů blob – objekty blob bloku, doplňovací objekty blob a objekty blob stránky (disky). Objekty blob bloku jsou optimalizované pro streamování a ukládání cloudových objektů a jsou dobrou volbou pro ukládání dokumentů, souborů médií, záloh atd. Doplňovací objekty blob jsou podobné objektům blob bloku, ale jsou optimalizované pro doplňovací operace. Doplňovací objekt blob se může aktualizovat jen přidáním nového bloku na konec. Doplňovací objekty blob jsou dobrou volbou pro takové scénáře, jako je například protokolování, kde se nová data potřebují zapisovat jen na konec objektu blob. Objekty BLOB stránky jsou optimalizované pro zastoupení disků IaaS a podporují náhodné zápisy a může mít až 1 TB. Disk IaaS připojení přes síť k virtuálnímu počítači Azure je virtuální pevný disk uložený jako objekt blob.

#### <a name="table-storage"></a>Úložiště Table
Úložiště Table je úložiště klíčů/atributů typu NoSQL od Microsoftu – návrh bez schémat, rozdíl od tradičních relačních databází. Protože schémata nedostatečné úložiště dat, je snadné data přizpůsobovat měnícím potřebám vaší aplikace. Úložiště Table se snadno používá, takže vývojáři můžou aplikace vytvářet rychle. Úložiště Table je úložiště typu klíč-atribut – to znamená, že každá hodnota v tabulce je uložená se typovým názvem vlastnosti. název vlastnosti se může použít pro filtrování a upřesnění kritérií výběru. Kolekce vlastností a jejich hodnot tvoří entitu. Od schémata nedostatečné úložiště tabulek dvě entity ve stejné tabulce můžou obsahovat různé kolekce vlastností a tyto vlastnosti můžou být různých typů. Úložiště Table Storage můžete používat k ukládání flexibilních datových sad, například uživatelských dat pro webové aplikace, adresářů, informací o zařízení a dalších typů metadat, které vaše služba vyžaduje. V tabulce můžete uložit libovolný počet entit a účet úložiště může obsahovat libovolný počet tabulek, až do limitu kapacity účtu úložiště.

#### <a name="queue-storage"></a>Queue Storage
Úložiště Azure Queue zajišťuje cloudový přenos zpráv mezi součástmi aplikace. Při navrhování aplikací pro škálování ve větším měřítku jsou jednotlivé součásti aplikací často nepropojené, aby je bylo možné škálovat nezávisle. Queue Storage zajišťuje asynchronní přenos zpráv pro komunikaci mezi součástmi aplikace bez ohledu na to, jestli běží v cloudu, na desktopu, na místním serveru nebo na mobilním zařízení. Queue Storage také podporuje správu asynchronních úloh a pracovní postupy procesů sestavování buildů.

### <a name="keyvault"></a>KeyVault
Trezor klíčů RP poskytuje správu a auditování tajné kódy, jako jsou hesla a certifikáty. Jako příklad tenanta můžete KeyVault RP uvést správce hesel nebo klíčů během nasazení virtuálního počítače.

## <a name="high-availability-for-azure-stack"></a>Vysoká dostupnost pro Azure Stack
Abyste dosáhli vysoké dostupnosti systému produkčního prostředí více virtuálních počítačů v Azure, umístěte virtuální počítače [dostupnosti](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) se šíří mezi různými doménami selhání a aktualizačních doménách. V menších škálování služby Azure Stack doménu selhání ve skupině dostupnosti je definován jako jeden uzel v jednotce škálování.  

Infrastruktury služby Azure Stack je již odolné vůči selhání, základní technologie (clustering převzetí služeb při selhání) stále způsobí některé výpadek pro virtuální počítače na ovlivněné fyzickém serveru, pokud dojde k selhání hardwaru. Azure Stack podporuje s dostupnosti s délkou maximálně tři domény selhání pro zajištění konzistence s Azure.

- **Domény selhání**. Virtuální počítače umístěné ve skupině dostupnosti budou fyzicky izolované od sebe navzájem tím, že rozprostírá co nejrovnoměrněji rozložené přes víc domén selhání (uzly Azure Stack). Pokud dojde k selhání hardwaru, virtuálních počítačů z neúspěšných doména bude být restartování v jiných doménách selhání, ale pokud je to možné udržovat v samostatných doménách selhání z jiných virtuálních počítačů ve stejné sadě dostupnosti. Když hardware vrátí do režimu online, virtuálních počítačů bude možné znovu vyrovnána udržet vysokou dostupnost. 
 
- **Aktualizační domény**. Aktualizační domény jsou jiné Azure pojem, který poskytuje vysokou dostupnost ve skupinách dostupnosti. Aktualizační doména je logická skupina hardwarových komponent, které můžete provést údržbu ve stejnou dobu. Virtuální počítače umístěné ve stejné aktualizační domény je bude během plánované údržby restartují společně. Tenanti vytvářet virtuální počítače v rámci skupiny dostupnosti, Platforma Azure automaticky zajistí distribuci s virtuální počítače mezi nimi aktualizačními doménami. Ve službě Azure Stack jsou virtuální počítače za provozu migrovat na jiné online hostitelích v clusteru předtím, než se aktualizuje jejich základního hostitele. Protože neexistuje žádný výpadek tenanta při aktualizaci hostitele, funkci aktualizace domény ve službě Azure Stack existuje pouze pro šablony kompatibilitu s Azure. 

## <a name="role-based-access-control-rbac"></a>Řízení přístupu (RBAC) na základě rolí
Můžete použít RBAC pro udělení přístupu systému oprávněným uživatelům, skupinám a službám přiřazením role na předplatné, skupinu prostředků nebo úrovni jednotlivých prostředků. Každá role určuje požadovanou úroveň přístupu uživatele, skupiny nebo služby má prostředky Microsoft Azure Stack.

Azure RBAC má tři základní role, které platí pro všechny typy prostředků: Vlastník, Přispěvatel a čtenář. Vlastník má úplný přístup ke všem prostředkům, včetně práva na delegovat přístup ostatním uživatelům. Přispěvatel můžete vytvořit a spravovat všechny typy prostředků Azure, ale nemůže udělovat přístup ostatním uživatelům. Čtečka lze zobrazit pouze existující prostředky Azure. Ostatní role RBAC v Azure umožňují správu konkrétních prostředků Azure. Pro instanci role Přispěvatel virtuálních počítačů umožňuje vytváření a správa virtuálních počítačů, ale neumožňuje správu virtuální síť nebo podsíť, která se připojí k virtuálnímu počítači.

## <a name="usage-data"></a>Údaje o využití
Microsoft Azure Stack shromažďuje a agreguje data o využití přes všechny poskytovatele prostředků a je odesílá do Azure pro zpracování službou Azure commerce. Využití dat shromážděných ve službě Azure Stack lze zobrazit pomocí rozhraní REST API. Je konzistentních s Azure rozhraní API pro klienty i poskytovatele a delegované poskytovatele rozhraní API k získání dat o používání napříč všemi předplatnými tenanta. Tato data je možné integrovat pomocí externího nástroje nebo služby pro účely fakturace nebo vrácení peněz. Jakmile se využití se zpracovalo Azure průmyslu, lze zobrazit ve fakturačním portálu Azure.

## <a name="next-steps"></a>Další postup
[Základy správy](azure-stack-manage-basics.md)

