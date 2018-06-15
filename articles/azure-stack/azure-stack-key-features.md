---
title: Klíčové funkce a koncepty v zásobníku Azure | Microsoft Docs
description: Další informace o klíčových funkcích a koncepty v zásobníku Azure.
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
ms.date: 05/10/2018
ms.author: jeffgilb
ms.reviewer: ''
ms.openlocfilehash: 851530910c702d388cd4dc8607bf09ecb5fa44e0
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34198469"
---
# <a name="key-features-and-concepts-in-azure-stack"></a>Klíčové funkce a koncepty v Azure zásobníku
Pokud jste nový do protokolů Microsoft Azure, může být užitečné tyto podmínky a popis funkcí.

## <a name="personas"></a>Osoby
Existují dva typy uživatelů pro Microsoft Azure zásobníku, operátor cloudu (zprostředkovatel) a klient (příjemce).

* A **operátor cloudu** můžete konfigurovat Azure zásobníku a spravovat nabízí, plánů, služby, kvóty a ceny k poskytnutí prostředků pro své klienty.  Operátoři cloudu také spravovat kapacitu a reakce na výstrahy.  
* A **klienta** (také označované jako uživatel) využívá služby, které nabízí správce cloudu. Klienty můžete zřizovat, sledovat a spravovat služby, které se odebírá, jako jsou webové aplikace, úložiště a virtuálních počítačů.

## <a name="portal"></a>Portál
Primární metody interakci s Microsoft Azure zásobníku jsou portálu správce, portál user portal a prostředí PowerShell.

Portály zásobník Azure jsou každý zajišťované samostatné instance nástroje Azure Resource Manager.  Operátor cloudu pomocí portálu správce ke správě Azure zásobníku a k provádění akcí, jako je vytvoření klienta nabídky.  Portál user portal (také označované jako portál klienta) poskytuje samoobslužné prostředí pro využívání prostředků cloudu, jako jsou virtuální počítače, účty úložiště a webové aplikace. Další informace najdete v tématu [používání Azure zásobníku uživatelů a správců portálů](azure-stack-manage-portals.md).

## <a name="identity"></a>Identita 
Azure zásobníku používá jako zprostředkovatel identity Azure Active Directory (AAD) nebo Active Directory Federation Services (AD FS).  

### <a name="azure-active-directory"></a>Azure Active Directory
Azure Active Directory je poskytovatele cloudové víceklientské identit společnosti Microsoft.  Většina hybridní scénáře použití služby Azure Active Directory jako úložiště identit.

### <a name="active-directory-federation-services"></a>Služba AD FS (Active Directory Federation Services)
Můžete se rozhodnout používat Active Directory Federation Services (AD FS) pro odpojené nasazení Azure zásobníku.  Azure zásobníku, zprostředkovatelé prostředků a další aplikace fungovat stejně jako s Azure Active Directory mnohem stejným způsobem se službou AD FS. Azure Stack zahrnuje svou vlastní instanci služby AD FS a služby Active Directory a Active Directory Graph API. Azure Development Kit zásobníku podporuje následující scénáře služby AD FS:

- Přihlaste se k nasazení pomocí služby AD FS.
- Vytvoření virtuálního počítače pomocí tajných klíčů v Key Vault
- Vytvoření trezoru pro ukládání nebo přístupu k tajné klíče
- Vytvořit vlastní role RBAC v předplatném
- Přiřadit uživatele k rolím RBAC na prostředky
- Vytvářet systémové role RBAC pomocí prostředí Azure PowerShell
- Přihlaste se jako uživatel pomocí prostředí Azure PowerShell
- Vytvoření služby objekty je použít k přihlášení k prostředí Azure PowerShell


## <a name="regions-services-plans-offers-and-subscriptions"></a>Oblasti, služby, plánů, nabídky a odběry
V zásobníku Azure se dodávají služby klientům pomocí oblastí, odběry, nabídky a plány. Klienti se mohou přihlásit k více nabídky. Nabízí může mít jeden nebo více plánů a plány může mít jednu nebo více služeb.

![](media/azure-stack-key-features/image4.png)

Příklad hierarchie odběrů klienta nabídky, každý s různými plánů a služeb.

### <a name="regions"></a>Oblasti
Zásobník oblasti jsou základní prvek škálování a správu. Organizace může mít více oblastí s prostředky, které jsou k dispozici v každé oblasti. Oblasti mohou mít i jiné služby nabídky, které jsou k dispozici. V Azure zásobníku Development Kit je podporována pouze jedna oblast a automaticky s názvem *místní*.

### <a name="services"></a>Služby
Microsoft Azure zásobníku umožňuje poskytovatelům k poskytování širokou škálu služeb a aplikací, jako je například virtuální počítače, SQL Server databáze, SharePoint, Exchange a další.

### <a name="plans"></a>Plány
Plány jsou seskupení jednu nebo více služeb. Jako zprostředkovatele vytvořte plány, které nabízejí klientům. Vaši tenanti se pak můžou přihlásit k odběru nabídek, aby mohli používat plány a služby, které k nim patří.

Každá služba, přidat do plánu můžete nakonfigurovat nastavení kvót, které vám pomohou spravovat vaše kapacita cloudu. Kvóty můžou obsahovat omezení, jako je například virtuální počítač, paměti RAM a procesoru omezení a se použijí na uživatele předplatné. Kvóty mohou být rozlišovány podle umístění. Plán obsahující výpočetní služby z oblasti A může mít například kvótu dva virtuální počítače, 4 GB paměti RAM a 10 jader procesoru.

Při vytváření nabídky, můžete použít Správce služby **základního plánu**. Tyto základní plány jsou zahrnuté ve výchozím nastavení, pokud se klient přihlásí k této nabídky. Když se uživatel přihlásí (a vytvoření odběru), má uživatel přístup do všech poskytovatelů prostředků zadaný v těchto plánech základní (s odpovídající kvóty).

Správce služeb můžete zahrnout taky **rozšíření plány** v nabídku. Plány rozšíření nejsou zahrnuté ve výchozím nastavení v rámci předplatného. Rozšíření plány jsou další plány (kvóty) k dispozici v nabídku, vlastník předplatného můžete přidat do svých předplatných.

### <a name="offers"></a>Nabídky
Nabídky jsou skupiny jeden nebo více plánů, které zprostředkovatelé představovat klientům koupit (přihlášení k odběru). Nabízejí Alpha může například obsahovat plán A obsahující sadu výpočetní služby a plánování B obsahující sadu úložiště a síťové služby.

Nabídku dodává se sadou základní plány a Správce služeb můžete vytvořit rozšíření plány, které klienty můžete přidat do své předplatné.

### <a name="subscriptions"></a>Předplatná
Předplatné je, jak klienti koupit vaší nabídky. Předplatné je kombinací klienta s nabídku. Klient může mít odběry více nabídky. Každé předplatné se týká jenom jednu nabídku. Odběry klienta rozhodněte, které plány/služby přístupem.

Odběry pomáhají poskytovatelů uspořádání a přístup k službám a prostředkům cloudu.

Pro správce vytvoří se během nasazení odběru výchozí zprostředkovatel. Toto předplatné slouží ke správě Azure zásobníku, nasadit další zprostředkovatelé prostředků a vytvořte plány a nabízí pro klienty. Není vhodné používat ke spuštění úloh zákazníka a aplikací. Počínaje verzí 1804, dva další odběry doplnit výchozí zprostředkovatel předplatného; Měření předplatného a spotřeba předplatné. Tyto doplňky usnadnit oddělení správy základní infrastruktury, zprostředkovatelé dalších prostředků a úloh.  

## <a name="azure-resource-manager"></a>Azure Resource Manager
Pomocí Azure Resource Manager můžete pracovat s vaše prostředky infrastruktury v modelu na základě šablon, deklarativní.   Poskytuje jednotné rozhraní, které můžete použít k nasazení a správě součástí vašeho řešení. Úplné informace a pokyny najdete v tématu [přehled Azure Resource Manageru](../azure-resource-manager/resource-group-overview.md).

### <a name="resource-groups"></a>Skupiny prostředků
Skupiny prostředků jsou kolekce prostředků, služeb a aplikací – a má každý prostředek typu, například virtuálních počítačů, virtuálních sítí, veřejné IP adresy, účty úložiště a weby. Každý prostředek, musí být ve skupině prostředků a tak skupiny prostředků pomáhají logicky uspořádat prostředky, například zatížení nebo umístění.  V zásobníku Microsoft Azure jsou prostředkům, například plány a nabízí taky spravovat v skupiny prostředků.

Na rozdíl od [Azure](../azure-resource-manager/resource-group-move-resources.md), prostředky nelze přesouvat mezi skupinami prostředků. Když máte zobrazeny vlastnosti prostředku nebo skupinu prostředků na portálu správy Azure zásobníku *přesunout* tlačítko je zašedlá a není k dispozici. 
 
### <a name="azure-resource-manager-templates"></a>Šablony Azure Resource Manageru
S Azure Resource Manager, můžete vytvořit šablonu (ve formátu JSON), která definuje nasazení a konfiguraci vaší aplikace. Tato šablona se označuje jako šablonu Azure Resource Manageru a nabízí deklarativní způsob, jak definovat nasazení. Pomocí šablony můžete aplikaci opakovaně nasadit v průběhu životního cyklu a mít přitom jistotu, že se prostředky nasadí konzistentně.

## <a name="resource-providers-rps"></a>Zprostředkovatelé prostředků (RPs)
Zprostředkovatelé prostředků jsou webové služby, které tvoří základ pro všechny založené na Azure IaaS a PaaS služby. Azure Resource Manager závisí na jiné RPs poskytnout přístup ke službám.

Existují čtyři základní RPs: sítě, úložiště, výpočetního prostředí a KeyVault. Každý z těchto RPs vám pomůže konfigurovat a řídit její příslušné prostředky. Správce služeb můžete také přidat nové vlastní prostředek zprostředkovatele.

### <a name="compute-rp"></a>Výpočetní RP
Výpočetní prostředek zprostředkovatele (CRP) umožňuje klientům zásobník Azure vytvářet své vlastní virtuální počítače. CRP zahrnuje schopnost vytvářet virtuální počítače, jakož i rozšíření virtuálního počítače. Služba rozšíření virtuálního počítače, pomáhá poskytovat schopnosti IaaS pro virtuální počítače Windows a Linux.  Jako příklad můžete zřídit virtuální počítač s Linuxem a spouštění skriptů Bash během nasazení nakonfigurovat virtuální počítač CRP.

### <a name="network-rp"></a>Sítě RP
Zprostředkovatel prostředků sítě (NRP) přináší řadu funkcí softwarem definovanou síť (SDN) a síťové funkce virtualizace (NFV) pro daný privátní cloud.  Vytvořit prostředky jako softwaru zatížení vyrovnávání, veřejné IP adresy, skupin zabezpečení sítě, virtuální sítě můžete použít NRP.

### <a name="storage-rp"></a>Úložiště RP
RP úložiště nabízí čtyři Azure konzistentní služby úložiště: Objekt blob, tabulky, fronty a Správa účtů. Nabízí také Cloudová služba správy úložiště usnadnit správu služby zprostředkovatele služby Azure konzistentní úložiště. Úložiště Azure poskytuje flexibilitu při uložení a načtení velké objemy nestrukturovaných dat, například dokumentů a mediálních souborů pomocí Azure BLOB, a strukturovaných NoSQL na základě dat pomocí tabulky Azure. Další informace o Azure Storage najdete v tématu [Úvod do Microsoft Azure Storage](../storage/common/storage-introduction.md).

#### <a name="blob-storage"></a>Blob Storage
BLOB storage ukládá žádné datové sady. Objekt blob může být jakýkoli druh textu nebo binárních dat, jako je dokument, soubor médií nebo instalátor aplikace. Table storage ukládá strukturované datové sady. Table Storage je datové úložiště na bázi NoSQL typu klíč-atribut, které umožňuje rychlý vývoj a přístup k velkým objemům dat. Queue storage poskytuje spolehlivé zasílání zpráv pro zpracování pracovního postupu a pro komunikaci mezi součástmi cloudových služeb.

Každý objekt blob se organizuje v kontejneru. Kontejnery také nabízejí praktický způsob přiřazení zásad zabezpečení skupinám objektů. Účet úložiště může obsahovat libovolný počet kontejnerů a kontejner může obsahovat libovolný počet objektů BLOB až do limitu 500 TB kapacity účtu úložiště. Úložiště Blob nabízí tři typy objektů blob – objekty blob bloku, doplňovací objekty blob a objekty blob stránky (disky). Objekty blob bloku jsou optimalizované pro streamování a ukládání cloudových objektů a jsou dobrou volbou pro ukládání dokumentů, souborů médií, záloh atd. Doplňovací objekty blob jsou podobné objektům blob bloku, ale jsou optimalizované pro doplňovací operace. Doplňovací objekt blob se může aktualizovat jen přidáním nového bloku na konec. Doplňovací objekty blob jsou dobrou volbou pro takové scénáře, jako je například protokolování, kde se nová data potřebují zapisovat jen na konec objektu blob. Objekty BLOB stránky jsou optimalizované pro zastoupení disků IaaS a podporují náhodné zapíše a může být až 1 TB. Disk IaaS připojení přes síť k virtuálnímu počítači Azure je virtuální pevný disk uložený jako objekt blob.

#### <a name="table-storage"></a>Úložiště Table
Úložiště Table je úložiště klíčů/atributů NoSQL společnosti Microsoft – má návrh bez schémat, takže je odlišný od tradičních relačních databází. Vzhledem k tomu, že chybí schémata úložiště dat, je snadné data přizpůsobovat potřebám vaší aplikace měnícím. Úložiště Table se snadno používá, takže vývojáři můžou aplikace vytvářet rychle. Úložiště Table je úložiště typu klíč-atribut – to znamená, že každá hodnota v tabulce je uložená se typovým názvem vlastnosti. název vlastnosti se může použít pro filtrování a upřesnění kritérií výběru. Kolekce vlastností a jejich hodnot tvoří entitu. Protože schémata nedostatečná úložiště tabulek dvě entity ve stejné tabulce můžou obsahovat různé kolekce vlastností a tyto vlastnosti můžou být různých typů. Úložiště Table Storage můžete používat k ukládání flexibilních datových sad, například uživatelských dat pro webové aplikace, adresářů, informací o zařízení a dalších typů metadat, které vaše služba vyžaduje. V tabulce můžete uložit libovolný počet entit a účet úložiště může obsahovat libovolný počet tabulek, až do limitu kapacity účtu úložiště.

#### <a name="queue-storage"></a>Queue Storage
Úložiště Azure Queue zajišťuje cloudový přenos zpráv mezi součástmi aplikace. Při navrhování aplikací pro škálování ve větším měřítku jsou jednotlivé součásti aplikací často nepropojené, aby je bylo možné škálovat nezávisle. Queue Storage zajišťuje asynchronní přenos zpráv pro komunikaci mezi součástmi aplikace bez ohledu na to, jestli běží v cloudu, na desktopu, na místním serveru nebo na mobilním zařízení. Queue Storage také podporuje správu asynchronních úloh a pracovní postupy procesů sestavování buildů.

### <a name="keyvault"></a>KeyVault
KeyVault RP poskytuje správu a auditování tajné údaje, jako jsou hesla a certifikáty. Jako příklad klienta zajistit hesla správce nebo klíče při nasazení virtuálního počítače použít KeyVault RP.

## <a name="high-availability-for-azure-stack"></a>Vysoká dostupnost pro Azure zásobníku
*Platí pro: Azure 1802 zásobníku nebo vyšší verze.*

K dosažení vysoké dostupnosti systému produkční více virtuálních počítačů v Azure, jsou virtuální počítače umístěny v nastavení dostupnosti, který se šíří je napříč více domén selhání a aktualizace domény. Tímto způsobem [virtuálních počítačích nasazených v nastavení dostupnosti](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) jsou fyzicky izolované od sebe navzájem na samostatný server stojany umožňující selhání odolnost, jak je znázorněno v následujícím diagramu:

  ![Azure zásobníku vysokou dostupnost](media/azure-stack-key-features/high-availability.png)

### <a name="availability-sets-in-azure-stack"></a>Sady dostupnosti v Azure zásobníku
Při infrastruktury Azure zásobníku již odolné vůči selhání, základní technologii (clustering převzetí služeb při selhání) stále způsobuje výpadky pro virtuální počítače na ovlivněné fyzickém serveru, pokud dojde k selhání hardwaru. Azure zásobníku podporuje, že sada dostupnosti s maximálně tři domén selhání kvůli souladu s Azure.

- **Poruch domény**. Virtuální počítače umístěny v nastavení dostupnosti bude fyzicky izolované od sebe navzájem jako rovnoměrně rozloží přes více domén selhání (zásobník Azure uzlů). Pokud dojde k selhání hardwaru, virtuálních počítačů z domény se nezdařilo selhání bude být restartován v jiných doménách selhání, ale, pokud je to možné, zachovány v domén selhání samostatné z jiných virtuálních počítačů ve stejné sadě dostupnosti. Když hardware přejde do režimu online, virtuálních počítačů bude možné znovu vyrovnána kvůli udržení vysoké dostupnosti. 
 
- **Aktualizovat domény**. Aktualizace domény jsou jiné Azure konceptu, které poskytuje vysokou dostupnost v nastavení dostupnosti. Doména aktualizace je logické skupiny základní hardware, který můžete projít údržby ve stejnou dobu. Virtuální počítače umístěné ve stejné doméně, aktualizace se restartuje společně během plánované údržby. Jako klienty, vytvořte virtuální počítače v rámci skupiny dostupnosti, platformu Azure automaticky rozděluje virtuálních počítačů na těchto aktualizaci domény. V zásobníku Azure jsou virtuální počítače za provozu proběhne migrace na jiné online hostitele v clusteru, než jejich základní hostitel je aktualizovat. Vzhledem k tomu, že neexistuje žádné výpadky klienta během hostitele aktualizace, aktualizace domény funkce v zásobníku Azure existuje pouze pro Kompatibilita šablon s Azure. 

### <a name="upgrade-scenarios"></a>Scénářích upgradu 
Virtuální počítače ve skupinách dostupnosti, které byly vytvořeny před zásobník Azure verze 1802 mají výchozí počet selhání a aktualizace domény (1 a 1 v uvedeném pořadí). K dosažení vysoké dostupnosti pro virtuální počítače v těchto existující skupiny dostupnosti, musíte nejprve odstranit stávající virtuální počítače a potom je znovu nasaďte do nové dostupnosti nastavit s správný počet selhání a aktualizace domény, jak je popsáno v [změn sadu dostupnosti pro virtuální počítač s Windows](https://docs.microsoft.com/azure/virtual-machines/windows/change-availability-set). 

Pro sady škálování virtuálního počítače se interně vytvoří skupinu dostupnosti s výchozí doménu a aktualizace počet domén selhání (3 a 5 v uvedeném pořadí). Počty všechny škálovací sady virtuálních počítačů před 1802 aktualizací budou umístěny do sada dostupnosti s vytvořit výchozí selhání a aktualizace domény (1 a 1 v uvedeném pořadí). Pokud chcete aktualizovat tyto instance sady škálování virtuálního počítače k dosažení novější šíření, škálovat sady škálování virtuálního počítače podle počtu instancí, které existovaly před aktualizací 1802 a pak odstraňte starší instance škálovací sady virtuálních počítačů. 

## <a name="role-based-access-control-rbac"></a>Řízení přístupu (RBAC) na základě role
RBAC můžete udělit přístup k systému oprávněným uživatelům, skupinám a službám pomocí jejich přiřazení role v předplatné, skupinu prostředků nebo úrovni jednotlivých prostředků. Každá role určuje úroveň přístupu, které uživatele, skupiny nebo službu má prostředky Microsoft Azure zásobníku.

Azure RBAC má tři základní rolí, které platí pro všechny typy prostředků: vlastník, Přispěvatel a čtečky. Vlastník má úplný přístup ke všem prostředkům, včetně právo delegovat přístup k ostatním. Přispěvatel můžete vytvořit a spravovat všechny typy prostředků Azure, ale nelze udělit přístup ostatním uživatelům. Čtečka lze zobrazit pouze existující prostředky Azure. Zbytek role RBAC v Azure povolit správu konkrétních prostředků Azure. Pro instanci role Přispěvatel virtuálních počítačů umožňuje vytváření a správu virtuálních počítačů, ale neumožňuje správu virtuální síť nebo podsíť, které se virtuální počítač připojí k.

## <a name="usage-data"></a>Údaje o využití
Microsoft Azure zásobníku shromažďuje a agreguje data o využití napříč všech poskytovatelů prostředků a přenáší ho do Azure pro zpracování obchodu Spojených států v Azure. Data o využití shromážděné v zásobníku Azure lze zobrazit pomocí rozhraní REST API. Není Azure konzistentní rozhraní API pro klienty a také zprostředkovatele a delegovat rozhraní API poskytovatele získat data o využití ve všech předplatných klienta. Tato data lze integrovat s externího nástroje nebo služby pro fakturace nebo vrácení peněz. Po použití bylo zpracováno obchodu Azure, lze zobrazit na portálu Azure fakturace.

## <a name="in-development-build-of-azure-stack-development-kit"></a>Sestavení v vývoj Azure zásobníku Development Kit
Sestavení v vývoj umožní – inovátoři vyhodnotit nejnovější verzi Azure zásobníku Development Kit. Jsou přírůstkové sestavení podle nejnovější hlavní verzi. Zatímco hlavní verze bude dále vydané každých několik měsíců, bude občas verze sestavení v vývoj mezi hlavní verze.

Sestavení v vývoj bude poskytovat následující výhody:
- Opravy chyb
- Nové funkce
- Další vylepšení

## <a name="next-steps"></a>Další postup
[Vyhodnotit Azure zásobníku Development Kit](azure-stack-deploy-overview.md)

