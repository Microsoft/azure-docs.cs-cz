---
title: Co je Azure Stack? | Dokumenty Microsoft
description: Zjistěte, jak vám Azure Stack umožňuje provozovat služby Azure ve vašem datovém centru.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 03/29/2019
ms.author: jeffgilb
ms.reviewer: unknown
ms.custom: ''
ms.lastreviewed: 03/29/2019
ms.openlocfilehash: e5e05ad4f2ae7e718e160916144f03bfb74a2a52
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2019
ms.locfileid: "58631280"
---
# <a name="azure-stack-overview"></a>Přehled služby Azure Stack

Azure Stack je rozšířením Azure, která poskytuje způsob, jak spouštět aplikace v místním prostředí a poskytovat služby Azure ve vašem datovém centru. S konzistentní cloudové platformy mohli bez obav organizace podle firemních potřeb rozhodnutí o technologiích, nikoli obchodní rozhodnutí založené na technologii omezení.

## <a name="why-use-azure-stack"></a>Proč používat Azure Stack?

Azure nabízí bohatou platformu pro vývojáře pro vytváření moderních aplikací. Ale některé aplikace založené na cloudu čelí překážky třeba latence, přerušovaným připojením a nařízení. Azure a Azure Stackem odemknout nové případy použití pro řádek určených pro zákazníky a interní obchodní aplikace hybridního cloudu:

- **Hraniční a odpojená řešení**. Adresa zpracovávat data lokálně ve službě Azure Stack a potom agregovat v Azure pro další analýzy s využitím s společné aplikační logiky splňte latence a požadavky na připojení. Můžete dokonce nasadit Azure Stack odpojení od Internetu bez připojení k Azure. Přemýšlejte o výrobní prostory, dopravní, ať se dodává a Moje šachty jako příklady.

- **Cloudové aplikace, které splňují předpisy pohyblivá podle**. Vyvíjejte a nasazujte aplikace v Azure s úplnou flexibilitou k nasazení místně pomocí služby Azure Stack pro splnění zákonných požadavků a požadavků zásad nejsou potřeba žádné změny kódu. Aplikace příklady globální auditování, finanční sestavy, obchodování cizích měn, hraní online her a vykazování výdajů apod.

- **Cloudové aplikace modelu místní**. Pomocí služby Azure, kontejnery, bez serveru a architektury mikroslužeb aktualizovat a rozšiřovat stávající aplikace nebo vytvářejte nové. Procesů DevOps konzistentní použití v Azure v cloudu a Azure Stack místní ke zrychlení aplikací modernizaci aplikací kritických pro chod core.

Azure Stack umožňuje tyto scénáře použití tím, že poskytuje:

- Doručování integrované prostředí pro zprovoznění a zprovoznění pomocí účelových Azure Stack integrované systémy od partnerů pro důvěryhodného hardwaru. Po jeho doručení Azure Stack snadno integruje do datového centra s monitorováním pomocí System Center Operations Manager Management Pack nebo Nagios rozšíření.

- Flexibilní identity management pro Azure a Azure Stackem hybridní prostředí pomocí Azure Active Directory (Azure AD) a využití služby Active Directory Federation Services (AD FS) při odpojení nasazení. 

- Azure konzistentní prostředí pro vývoj aplikací maximalizují produktivitu vývojářů a povolit běžné praktiky DevOps blíží napříč hybridními prostředími.

- Doručování služeb Azure z místní hybridní Cloudová výpočetní výkon. Přijmout společných operačních postupů v Azure a Azure Stack, jak nasadit a provozovat služby Azure IaaS a PaaS pomocí stejné prostředí pro správu a nástrojů jako Azure. Microsoft nabízí nepřetržité inovace Azure do služby Azure Stack, včetně služby Azure, aktualizace stávajících službách a další aplikace Azure Marketplace a Image.

## <a name="azure-stack-architecture"></a>Architektura služby Azure Stack
Azure Stack, které se skládají integrované systémy v stojany 4-16 serverů vytvořených důvěryhodné hardwarových partnerů a doručí přímo do vašeho datového centra. Po jeho doručení poskytovatele řešení bude pracovat nasazovat integrovaný systém a ujistěte se, že řešení Azure stacku splňuje vaše podnikové požadavky. Budete muset připravit tím zajištění všech požadovaných napájení a chlazení vašeho datového centra, ohraničení připojení a další požadavek integrace vyžaduje datacenter jsou na místě. 

> Další informace o prostředí integrace datových center Azure Stack, najdete v části [integrace datových center Azure Stack](azure-stack-customer-journey.md).

Azure Stack je založen na standardní hardwarovou odvětví a spravuje se pomocí stejných nástrojů, které už používáte pro správu předplatných Azure. V důsledku toho můžete provést konzistentních procesů DevOps, zda jste připojeni k Azure, nebo ne. 

Architektura služby Azure Stack umožňuje poskytovat služby Azure na hraničních zařízeních pro vzdálených umístění nebo přerušované připojení, odpojení od Internetu. Můžete vytvořit hybridní řešení, které zpracovávat data lokálně ve službě Azure Stack a potom ho agregovat v Azure pro další zpracování a analýzu. A konečně protože Azure Stack je nainstalovaný místně, můžete podle specifických požadavků předpisů nebo zásad Díky flexibilitě cloudových aplikací v místním nasazení beze změny kódu. 

## <a name="deployment-options"></a>Možnosti nasazení

### <a name="production-or-evaluation-environments"></a>Produkční nebo zkušební prostředí
Azure Stack se nabízí ve dvou možnosti nasazení podle svých potřeb, integrované systémy Azure Stack pro použití v produkčním prostředí a Azure Stack Development Kit (ASDK) za vaše rozhodnutí vyzkoušet Azure Stack:

- **Integrované systémy Azure Stack**. Azure Stack, který nabízí integrované systémy Díky partnerství Microsoftu a hardwarových partnerů, vytvoření řešení, která nabízí cloud tempem inovace a výpočetnímu zjednodušení správy. Azure Stack se nabízí jako integrované hardwaru a softwaru system, takže máte flexibility a kontroly, které potřebujete, spolu se schopností na inovace v cloudu. Systémy ve službě Azure Stack integrované v rozsahu od 4 až 16 uzlů a společně podporuje hardwarových partnerů a Microsoft. Integrované systémy Azure Stack slouží k vytvoření nové scénáře a nasazovat nové řešení pro vaše produkční úlohy.

- **Azure Stack Development Kit**. [Azure Stack Development Kit (ASDK)](./asdk/asdk-what-is.md) je jedním uzlem, bezplatné nasazení služby Azure Stack, který můžete použít k vyhodnocení a další informace o službě Azure Stack. Můžete také použít ASDK jako prostředí pro vývojáře k vytváření aplikací pomocí rozhraní API a nástrojů, který je konzistentní s Azure. ASDK však není určena pro použití jako v provozním prostředí a má následující omezení než úplné integrované systémy pro produkční nasazení:

    - Pouze ASDK lze přidružit jeden Azure Active Directory (Azure AD) nebo zprostředkovatele identity služby Active Directory Federation Services (AD FS).
    - Protože komponenty služby Azure Stack jsou nasazené na jeden hostitelský počítač, se omezené fyzické prostředky dostupné pro prostředky tenanta. Tato konfigurace není určen pro škálování nebo pro účely vyhodnocení výkonu.
    - Scénáře pro sítě jsou omezené z důvodu jednom hostiteli a síťovou kartu požadavky na nasazení.

### <a name="connection-models"></a>Modely připojení
Můžete také nasadit Azure Stack buď **připojené** k Internetu (a do Azure) nebo **odpojení** z něj. Tato volba určuje, jaké možnosti jsou k dispozici pro úložiště identit (Azure AD nebo AD FS) a model fakturace (platit při použití na základě fakturace nebo založená na kapacitě fakturace).

> Další informace najdete v tématu důležité informace týkající se [připojené](azure-stack-connected-deployment.md) a [odpojení](azure-stack-disconnected-deployment.md) modely nasazení. 

### <a name="identity-provider"></a>Zprostředkovatel identity 
Azure Stack použije Azure Active Directory (Azure AD) nebo Active Directory Federation Services (AD FS) jako zprostředkovatele identity pro vytvoření identity služby Azure Stack. 

> [!IMPORTANT]
> Toto je klíčová rozhodnutí bod! Výběrem nabídky Azure AD nebo AD FS jako zprostředkovatele identity je jednorázový rozhodnutí, které je nutné provést v době nasazení. Nedá se změnit později bez nutnosti nového nasazení celého systému.

Azure AD je zprostředkovatel identity založené na cloudu, více tenantů od Microsoftu. Většina hybridní scénáře s nasazeními připojeného k Internetu pomocí Azure AD jako úložiště identit. Můžete však použít Active Directory Federation Services (AD FS) pro odpojené nasazení služby Azure Stack. Poskytovatele prostředků služby Azure Stack a další aplikace fungovat stejně jako s Azure AD téměř stejným způsobem se službou AD FS. Azure Stack zahrnuje svoji vlastní instanci služby Active Directory a Active Directory Graph API. 

> Další informace o aspektech týkajících se identity Azure Stack na [přehled identity pro službu Azure Stack](azure-stack-identity-overview.md).

## <a name="how-is-azure-stack-managed"></a>Jak se spravuje Azure Stack?
Po nasazení služby Azure Stack integrované systémy pro nasazení nebo instalace ASDK primární metody komunikace s Azure Stack jsou portál pro správu, portál user portal a PowerShell. Na portálech Azure stacku každého využívají samostatných instancí služby Azure Resource Manageru. **Operátor Azure stacku** používá na portálu pro správu ke správě služby Azure Stack a k třeba vytvořit tenanta nabídek a udržování stavu a monitorování stavu integrovaný systém. Portál user portal (také označované jako portál pro klienty) poskytuje samoobslužné prostředí pro používání cloudové prostředky, jako jsou virtuální počítače, účty storage a web apps. 

> Další informace o správě služby Azure Stack pomocí portálu pro správu, najdete v článku použití [rychlý start portálu pro správu služby Azure Stack](azure-stack-manage-portals.md).

Jako operátor Azure stacku, můžete doručovat širokou škálu služeb a aplikací, jako například [virtuálních počítačů](azure-stack-tutorial-tenant-vm.md), [webové aplikace](azure-stack-app-service-overview.md), s vysokou dostupností [systému SQL Server](azure-stack-tutorial-sql.md), a [MySQL Server](azure-stack-tutorial-mysql.md) databází. Můžete také použít [šablon Azure Resource Manageru pro rychlý start Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates) k nasazení služby SharePoint, Exchange a další. 

Pomocí portálu pro správu, můžete [konfigurace služby Azure Stack, aby mohl poskytovat služby](azure-stack-plan-offer-quota-overview.md) pro tenanty používající plány, kvóty, nabídky a předplatné. Tenanta uživatelé se mohou přihlásit k několika nabídky. Nabídek může mít jednoho nebo několika plánů a plány může mít jednu nebo víc služeb. Operátory také spravovat kapacity a reakce na výstrahy. 

Pokud je nakonfigurovaná Azure Stack, **uživatele Azure stacku** (označované také jako tenant) využívá služby, které nabízí operátor. Uživatele můžete zřizovat, monitorovat a spravovat služby, které jsou odběru jste přihlášeni, jako jsou webové aplikace, úložiště a virtuální počítače.

> Další informace o správě Azure Stack, včetně toho, co účtů použijte, pokud typické odpovědnosti operátora, co říct uživatelům a jak získat pomoc, najdete v tématu [základy Azure stacku správu](azure-stack-manage-basics.md).

## <a name="resource-providers"></a>Poskytovatelé prostředků 
Poskytovatelé prostředků jsou webové služby, které tvoří základ pro všechny Azure Stack IaaS a PaaS služby. Azure Resource Manageru závisí na různých prostředků poskytovatele za účelem poskytnutí přístupu ke službám. Každý poskytovatel prostředků vám pomůže nakonfigurovat a řídit jeho příslušné prostředky. Správce služeb můžete také přidat nové vlastní prostředek zprostředkovatele. 

### <a name="foundational-resource-providers"></a>Poskytovatelé základních prostředků 
Existují tři základní poskytovatelů prostředků IaaS: COMPUTE, Network a Storage:

- **COMPUTE**. Poskytovatele výpočetních prostředků umožňuje klientům služby Azure Stack můžete vytvořit své vlastní virtuální počítače. Poskytovateli prostředků Compute zahrnuje schopnost vytvářet virtuální počítače, stejně jako rozšíření virtuálního počítače. Do rozšíření služby Virtual Machines pomáhá poskytovat schopnosti IaaS pro virtuální počítače s Windows a Linux.  Například můžete použít poskytovatele výpočetních prostředků ke zřízení virtuálního počítače s Linuxem a spouštění skriptů Bash během nasazování do konfigurace virtuálního počítače.
- **Síť poskytovatele prostředků**. Poskytovatel síťových prostředků přináší řadu funkce softwarově definované sítě (SDN) a síťové funkce virtualizace (NFV) pro daný privátní cloud. Poskytovatel síťových prostředků můžete použít k vytvoření zdroje, jako jsou nástroje pro vyrovnávání zatížení softwaru, veřejné IP adresy, skupiny zabezpečení sítě a virtuální sítě.
- **Poskytovatel prostředků úložiště**. Poskytovatel prostředků úložiště zajišťuje čtyř služeb konzistentních s Azure storage: [blob](https://docs.microsoft.com/azure/storage/common/storage-introduction#blob-storage), [fronty](https://docs.microsoft.com/azure/storage/common/storage-introduction#queue-storage), [tabulky](https://docs.microsoft.com/azure/storage/common/storage-introduction#table-storage)a správu trezoru klíčů účtu správy a auditování tajné kódy, jako jsou hesla a certifikáty. Poskytovatel prostředků úložiště také nabízí Cloudová služba správy úložiště pro usnadnění správy služeb poskytovatele služeb konzistentních s Azure Storage. Azure Storage nabízí flexibilitu při ukládání a načítání velkého objemu nestrukturovaných dat, jako jsou dokumenty a mediální soubory s objekty BLOB Azure, a strukturované NoSQL na základě dat pomocí tabulek Azure. 

### <a name="optional-resource-providers"></a>Poskytovatelé prostředků volitelné
Existují tři volitelné PaaS poskytovatelů prostředků, které můžete nasadit a používat s Azure Stack: Poskytovatelé prostředků App Service, SQL Server a MySQL Server:

- **App Service**. [Azure App Service ve službě Azure Stack](azure-stack-app-service-overview.md) platform-as-a-service (PaaS) nabídka Microsoft Azure je k dispozici pro Azure Stack. Tato služba umožňuje vaší interní nebo externí zákazníkům vytvářet webové rozhraní API a Azure Functions aplikací pro jakoukoliv platformu nebo zařízení. 
- **SQL Server**. Použití [poskytovatele prostředků SQL serveru](azure-stack-sql-resource-provider.md) nabízí databáze SQL jako služba Azure Stack. Po instalaci poskytovatele prostředků a připojte ho k jedné nebo více instancí systému SQL Server, můžete vytvořit vy a vaši uživatelé databází pro aplikace nativní pro cloud, weby, které používají SQL a jiné úlohy, které používají SQL.
- **MySQL Server**. Použití [poskytovatele prostředků MySQL Server](azure-stack-mysql-resource-provider-deploy.md) vystavit databází MySQL jako služby Azure Stack. Poskytovatele prostředků MySQL běží jako služba na virtuálním počítači (VM) systému Windows Server 2016 Server Core.

## <a name="providing-high-availability"></a>Zajištění vysoké dostupnosti
Abyste dosáhli vysoké dostupnosti systému produkčního prostředí více virtuálních počítačů v Azure, umístěte virtuální počítače [dostupnosti](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) se šíří mezi různými doménami selhání a aktualizačních doménách. V menších škálování služby Azure Stack doménu selhání ve skupině dostupnosti je definován jako jeden uzel v jednotce škálování.  

Infrastruktury služby Azure Stack je již odolné vůči selhání, základní technologie (clustering převzetí služeb při selhání) stále způsobí některé výpadek pro virtuální počítače na ovlivněné fyzickém serveru, pokud dojde k selhání hardwaru. Azure Stack podporuje s dostupnosti s délkou maximálně tři domény selhání pro zajištění konzistence s Azure.

- **Domény selhání**. Virtuální počítače umístěné ve skupině dostupnosti budou fyzicky izolované od sebe navzájem tím, že rozprostírá co nejrovnoměrněji rozložené přes víc domén selhání (uzly Azure Stack). Pokud dojde k selhání hardwaru, virtuálních počítačů z neúspěšných doména bude být restartování v jiných doménách selhání, ale pokud je to možné udržovat v samostatných doménách selhání z jiných virtuálních počítačů ve stejné sadě dostupnosti. Když hardware vrátí do režimu online, virtuálních počítačů bude možné znovu vyrovnána udržet vysokou dostupnost. 
 
- **Aktualizační domény**. Aktualizační domény jsou jiné Azure pojem, který poskytuje vysokou dostupnost ve skupinách dostupnosti. Aktualizační doména je logická skupina hardwarových komponent, které můžete provést údržbu ve stejnou dobu. Virtuální počítače umístěné ve stejné aktualizační domény je bude během plánované údržby restartují společně. Tenanti vytvářet virtuální počítače v rámci skupiny dostupnosti, Platforma Azure automaticky zajistí distribuci s virtuální počítače mezi nimi aktualizačními doménami. Ve službě Azure Stack jsou virtuální počítače za provozu migrovat na jiné online hostitelích v clusteru předtím, než se aktualizuje jejich základního hostitele. Protože neexistuje žádný výpadek tenanta při aktualizaci hostitele, funkci aktualizace domény ve službě Azure Stack existuje pouze pro šablony kompatibilitu s Azure. 

## <a name="role-based-access-control"></a>Řízení přístupu podle rolí
Na základě řízení přístupu Role (RBAC) můžete použít k udělení přístupu systému oprávněným uživatelům, skupinám a službám přiřazením role na předplatné, skupinu prostředků nebo úrovni jednotlivých prostředků. Každá role určuje požadovanou úroveň přístupu uživatele, skupiny nebo služby má prostředky Microsoft Azure Stack.

Azure Stack RBAC má tři základní role, které platí pro všechny typy prostředků: Vlastník, Přispěvatel a čtenář. Vlastník má úplný přístup ke všem prostředkům, včetně práva na delegovat přístup ostatním uživatelům. Přispěvatel můžete vytvořit a spravovat všechny typy prostředků Azure, ale nemůže udělovat přístup ostatním uživatelům. Čtečka lze zobrazit pouze existující prostředky. Ostatní role RBAC povolit správu konkrétních prostředků Azure. Pro instanci role Přispěvatel virtuálních počítačů umožňuje vytváření a správa virtuálních počítačů, ale neumožňuje správu virtuální síť nebo podsíť, která se připojí k virtuálnímu počítači.

> Zobrazit [řízení přístupu Manage Role-Based](azure-stack-manage-permissions.md) Další informace. 

## <a name="reporting-usage-data"></a>Generování sestav dat využití
Microsoft Azure Stack shromažďuje a agreguje data o využití přes všechny poskytovatele prostředků a je odesílá do Azure pro zpracování službou Azure commerce. Využití dat shromážděných ve službě Azure Stack lze zobrazit pomocí rozhraní REST API. Je konzistentních s Azure rozhraní API pro klienty i poskytovatele a delegované poskytovatele rozhraní API k získání dat o používání napříč všemi předplatnými tenanta. Tato data je možné integrovat pomocí externího nástroje nebo služby pro účely fakturace nebo vrácení peněz. Jakmile se využití se zpracovalo Azure průmyslu, lze zobrazit ve fakturačním portálu Azure.

> Další informace o [generování sestav dat využití služby Azure Stack na Azure](azure-stack-usage-reporting.md).

## <a name="next-steps"></a>Další postup

[Porovnání služby Azure Stack a globální Azure](compare-azure-azure-stack.md)

[Základy správy](azure-stack-manage-basics.md)

[Rychlý start: použití portálu pro správu služby Azure Stack](azure-stack-manage-portals.md)