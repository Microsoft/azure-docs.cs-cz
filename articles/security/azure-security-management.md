---
title: Zlepšení zabezpečení vzdálené správy v Azure | Dokumentace Microsoftu
description: Tento článek popisuje kroky pro zlepšení zabezpečení vzdálené správy při správě prostředí Microsoft Azure včetně cloudových služeb, služby Virtual Machines a vlastních aplikací.
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 2431feba-3364-4a63-8e66-858926061dd3
ms.service: security
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: terrylan
ms.openlocfilehash: edfabf1f93c78cf29ff3561f437053df11e15bd5
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2018
ms.locfileid: "37857476"
---
# <a name="security-management-in-azure"></a>Správa zabezpečení v Azure
Předplatitelé služby Azure mohou svoje cloudová prostředí spravovat z více zařízení. Můžou k tomu využívat pracovní stanice, počítače vývojářů a dokonce i privilegovaná zařízení koncových uživatelů, která mají oprávnění ke konkrétním úlohám. V některých případech se funkce správy provádějí prostřednictvím webových konzol, například [portálu Azure](https://azure.microsoft.com/features/azure-portal/). V ostatních případech můžou existovat přímá připojení k Azure z místních systémů prostřednictvím virtuálních privátních sítí (VPN), terminálových služeb, protokolů klientských aplikací nebo (v kódu) rozhraní API pro správu služby Azure (SMAPI). Kromě toho můžou být koncové body klienta buď připojené k doménám nebo izolované a nespravované, jako například tablety nebo smartphony.

Přestože funkce správy a více přístupů nabízejí bohatou sadu možností, může tato variabilita výrazně zvýšit rizika při nasazení cloudu. Může být obtížné spravovat, sledovat a auditovat akce správy. Tato variabilita může také zvýšit ohrožení bezpečnosti prostřednictvím neregulovaného přístupu ke koncovým bodům klientů, které se používají pro správu cloudových služeb. Používání obecných nebo osobních pracovních stanic k vývoji a správě infrastruktury otevírá možnosti útoků z nečekaných směrů, například prohlížení webu (např. útok typu watering hole) nebo e-mailu (např. sociální inženýrství a phishing).

![][1]

Pravděpodobnost útoku v tomto prostředí narůstá, protože je náročné vytvořit zásady a mechanismy zabezpečení, které by odpovídajícím způsobem řídily přístup k rozhraním Azure (například rozhraní SMAPI) z rozmanitých koncových bodů.

### <a name="remote-management-threats"></a>Vzdálená správa hrozeb
Útočníci často usilují o získání privilegovaného přístupu získáním přihlašovacích údajů k účtu (například prolomením hesla hrubou silou, phishingem nebo sběrem přihlašovacích údajů) nebo se pokouší zmást uživatele, aby spustili škodlivý kód (například neúmyslným stažením ze škodlivých webů nebo ze škodlivých příloh e-mailu). V prostředí cloudu spravovaného na dálku může průnik na účet vést k vyššímu riziku, protože přístup pak není omezený místem ani časem.

Dokonce i s přísnou kontrolou primárních účtů správců je možné zneužít slabá místa ve strategii zabezpečení uživatelských účtů nižší úrovně. Nedostatek odpovídajícího bezpečnostního školení může vést k průnikům prostřednictvím náhodného zpřístupnění informací o účtu.

Pokud pracovní stanici uživatele používáte i pro úlohy správy, může být ohrožena na mnoha různých místech. Ohrožení vzniká, když uživatel prohlíží web, používá nástroje třetích stran nebo nástroje open source, popřípadě když uživatel otevře škodlivý soubor dokumentu, který obsahuje trojského koně.

Obecně platí, že nejvíce cílených útoků, které mají za následek úniky dat, zneužívá prohlížeče, moduly plug-in (například Flash, PDF, Java) a spear phishing (e-mail) na stolních počítačích. Když jsou používány pro vývoj nebo správu jiných prostředků, můžou tyto počítače mít oprávnění na úrovni správce nebo na úrovni služby, aby mohly přistupovat k serverům Live nebo k síťovým zařízením.

### <a name="operational-security-fundamentals"></a>Základy provozního zabezpečení
Pokud chcete zajistit bezpečnější správu a provoz, můžete prostor k útokům na klienta minimalizovat tím, že omezíte počet možných vstupních bodů. Můžete to provést prostřednictvím zásad zabezpečení: „oddělení povinností“ a „odloučení prostředí“.

Vzájemnou izolací citlivých funkcí snížíte pravděpodobnost situace, kdy chyba na jedné úrovni povede k průniku do jiné úrovně. Příklady:

* Úkoly správy nikdy nekombinujte s činnostmi, které mohou vést k ohrožení zabezpečení (například malware v e-mailu správce, který potom nakazí server infrastruktury).
* Pracovní stanice používaná pro velmi citlivé operace nesmí být stejným systémem, který používáte pro vysoce rizikové účely, například prohlížení internetu.

Omezte prostor k útokům na systém tím, že odeberete nepotřebný software. Příklad:

* Běžná pracovní stanice určená pro správu, podporu nebo vývoj nevyžaduje instalaci e-mailového klienta nebo jiných aplikací zvyšujících produktivitu, pokud je hlavním účelem takového zařízení správa cloudových služeb.

Klientské systémy, které mají přístup správce k součástem infrastruktury, musí podléhat nejpřísnějším možným zásadám pro snižování rizik zabezpečení. Příklady:

* Zásady zabezpečení mohou zahrnovat nastavení zásad skupiny, které odepřou otevřený přístup k internetu z určitého zařízení a které používají omezující konfiguraci brány firewall.
* V případě potřeby přímého přístupu použijte VPN se zabezpečením pomocí protokolu IP (IPsec).
* Nakonfigurujte samostatné domény služby Active Directory pro správu a vývoj.
* Izolujte a filtrujte síťový provoz pracovní stanice používané ke správě.
* Používejte antimalwarový software.
* Zaveďte vícefaktorové ověřování a snižte riziko krádeže přihlašovacích údajů.

Sloučením přístupových prostředků a odstraněním nespravovaných koncových bodů také zjednodušíte úlohy správy.

### <a name="providing-security-for-azure-remote-management"></a>Zajištění zabezpečení pro vzdálenou správu Azure
Azure nabízí mechanismy zabezpečení na podporu správců, kteří spravují cloudové služby Azure a virtuální počítače. Mezi tyto mechanismy patří:

* ověřování a [řízení přístupu na bázi rolí](../role-based-access-control/role-assignments-portal.md)
* sledování, protokolování a auditování
* certifikáty a šifrovaná komunikace
* webový portál pro správu
* filtrování síťových paketů

S konfigurací zabezpečení na straně klienta a s bránou pro správu nasazenou v datovém centru můžete omezit a sledovat přístup správce ke cloudovým aplikacím a datům.

> [!NOTE]
> Některá doporučení v tomto článku můžou vést k vyšší spotřebě datových, síťových nebo počítačových prostředků a můžou zvýšit náklady na licence nebo předplatné.
>
>

## <a name="hardened-workstation-for-management"></a>Posílené pracovní stanice pro správu
Cílem posílení pracovní stanice je odstranění všech funkcí, kromě těch nejdůležitějších (které jsou nutné pro provoz), abyste maximálně zmenšili prostor k útoku. Posílení systému zahrnuje minimalizaci počtu nainstalovaných služeb a aplikací, omezení provádění aplikací, omezení přístupu k síti jenom na to nejnutnější a udržování systému v plně aktuálním stavu. Kromě toho používání posílené pracovní stanice pro správu odlučuje nástroje a činnosti správy od jiných úloh koncových uživatelů.

V rámci místního podnikového prostředí můžete omezit prostor k útoku na fyzickou infrastrukturu pomocí vyhrazené správy sítí, serverových místností s přístupem na kartu a pracovních stanic, které běží v chráněných oblastech sítě. V případě cloudu nebo modelu hybridního IT může být péče o služby zabezpečené správy složitější z důvodu nedostatku fyzického přístupu k prostředkům IT. Implementace řešení ochrany vyžaduje pečlivou konfiguraci softwaru, procesy zaměřené na zabezpečení a všestranné zásady.

Používáním softwaru s minimálními oprávněními na uzamknuté pracovní stanici pro správu cloudu (a pro vývoj aplikací) můžete snížit riziko bezpečnostních incidentů, protože tak můžete standardizovat prostředí vzdálené správy a vývoje. Konfigurace posílené pracovní stanice pomůže s ochranou před narušením zabezpečení účtů, které se používají ke správě důležitých cloudových prostředků, protože uzavře řadu běžných cest, kudy přichází malware a různá zneužití. K řízení a izolování chování klientského systému a ke zmírnění hrozeb, včetně prohlížení e-mailu a internetu, můžete konkrétně použít [nástroj Windows AppLocker](http://technet.microsoft.com/library/dd759117.aspx) a technologii Hyper-V.

Na posílené pracovní stanici správce používá běžný uživatelský účet (která blokuje akce na úrovni pro správu) a přidružené aplikace se řídí pomocí seznamu povolených položek. Základní prvky posílené pracovní stanice jsou následující:

* Aktivní vyhledávání a opravy chyb. Nasazení antimalwarového softwaru, provádění pravidelných prověřování ohrožení zabezpečení a včasné aktualizace všech pracovních stanic pomocí nejnovějších aktualizací zabezpečení.
* Omezená funkčnost. Odinstalování všech nepotřebných aplikací a zakázání nepotřebných služeb (spouštění).
* Posílení zabezpečení sítě. Používání pravidel brány Windows Firewall a povolení jenom platných IP adres, portů a adres URL, které se týkají správy Azure. Kontrola, jestli jsou příchozí vzdálená připojení k dané pracovní stanici také blokovaná.
* Omezení spouštění. Povolení jenom jedné sady předdefinovaných spustitelných souborů, které jsou potřebné pro spuštění správy, (označované jako „výchozí odmítnutí“). Ve výchozím nastavení nesmíte dát uživatelům oprávnění ke spuštění libovolného programu, pokud není výslovně uveden v seznamu povolených položek.
* Nejnižší oprávnění. Uživatelé pracovní stanice pro správu nesmí mít žádná oprávnění správce na samotném místním počítači. Tímto způsobem nemůžou změnit konfiguraci systému nebo systémové soubory, ať už úmyslně nebo náhodně.

Všechno uvedené můžete vynutit pomocí [objektů zásad skupiny](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-administer-group-policy) (GPO) ve službě Active Directory Domain Services (AD DS) a jejich použití prostřednictvím vaší (místní) domény pro správu na všechny účty pro správu.

### <a name="managing-services-applications-and-data"></a>Správa služeb, aplikací a dat
Konfigurace cloudových služeb Azure se provádí prostřednictvím portálu Azure nebo rozhraní SMAPI, prostřednictvím rozhraní příkazového řádku Windows PowerShell nebo uživatelské aplikace, která tato rozhraní RESTful využívá. Mezi služby, které tyto mechanismy využívají, patří Azure Active Directory (Azure AD), Azure Storage, weby Azure, Azure Virtual Network a další.

Virtuální počítač – nasazené aplikace podle potřeby poskytují svoje vlastní klientské nástroje a rozhraní, například konzola Microsoft Management Console (MMC), konzola pro správu podniku (například Microsoft System Center nebo Windows Intune) nebo jiná aplikace pro správu – například Microsoft SQL Server Management Studio. Tyto nástroje jsou obvykle umístěny v podnikovém prostředí nebo v klientské síti. Můžou záviset na konkrétních síťových protokolech, například na protokolu RDP (Remote Desktop Protocol), které vyžadují přímé stavové připojení. Některé z nich můžou mít povolené webové rozhraní, které nesmí být zveřejněno nebo zpřístupněno na internetu.

Přístup k správě služeb infrastruktury a platformy Azure můžete omezit pomocí [vícefaktorového ověřování](../active-directory/authentication/multi-factor-authentication.md), [certifikátů X.509 pro správu](https://blogs.msdn.microsoft.com/azuresecurity/2015/07/13/certificate-management-in-azure-dos-and-donts/) a pravidel brány firewall. Portál Azure a rozhraní SMAPI vyžadují protokol TLS (Transport Layer Security). Služby a aplikace, které nasadíte do Azure, vyžadují, abyste udělali ochranná opatření, která vhodným způsobem závisí na vaší aplikaci. Tyto mechanismy můžete snadněji povolit prostřednictvím konfigurace standardizované posílené pracovní stanice.

### <a name="management-gateway"></a>Brána pro správu
Pokud chcete centralizovat veškerý přístup pro správu a zjednodušit sledování a protokolování, můžete nasadit vyhrazený server [brány vzdálené plochy](https://technet.microsoft.com/library/dd560672) (brána VP) v místní síti, který je připojený k prostředí Azure.

Brána vzdálené plochy je proxy služba protokolu RDP založená na zásadách, která vynucuje požadavky na zabezpečení. Zavedení brány VP spolu s architekturou NAP (Network Access Protection) pro Windows Server pomáhá zajistit, že se připojí pouze klienti, kteří splňují konkrétní bezpečnostní kritéria stanovená objekty zásad skupiny (GPO) ve službě Active Directory Domain Services (AD DS). Navíc platí:

* Na bráně VP zřiďte [certifikát pro správu Azure](http://msdn.microsoft.com/library/azure/gg551722.aspx), aby byla jediným hostitelem s povoleným přístupem k webu Azure Portal.
* Připojte bránu VP ke stejné [doméně pro správu](http://technet.microsoft.com/library/bb727085.aspx) jako pracovní stanice správce. Je to nezbytné, když používáte VPN typu site-to-site s protokolem IPsec nebo ExpressRoute v rámci domény, která má jednosměrný vztah důvěryhodnosti k Azure AD, nebo pokud federujete přihlašovací údaje mezi místní instancí AD DS a Azure AD.
* Pokud chcete, aby brána VP ověřila platnost názvu klientského počítače (propojení s doménou) a povolila přístup k webu Azure Portal, na konfigurujte [zásady autorizace připojení klienta](http://technet.microsoft.com/library/cc753324.aspx).
* Protokol IPsec pro [Azure VPN](https://azure.microsoft.com/documentation/services/vpn-gateway/) slouží k ochraně před odposloucháváním provozu správy a před odcizením tokenu. Popřípadě zvažte použití izolovaného internetového odkazu prostřednictvím [Azure ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/).
* Povolte vícefaktorové ověřování (prostřednictvím služby [Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md)) nebo ověřování pomocí čipové karty, aby ho mohli používat správci, kteří se připojují prostřednictvím brány VP.
* Pokud chcete minimalizovat počet povolených koncových bodů pro správu, nakonfigurujte v Azure [omezení IP adres](http://azure.microsoft.com/blog/2013/08/27/confirming-dynamic-ip-address-restrictions-in-windows-azure-web-sites/) nebo [skupiny zabezpečení sítě](../virtual-network/security-overview.md) prostředku.

## <a name="security-guidelines"></a>Pokyny pro zabezpečení
Obecně platí, že zabezpečení pracovních stanic správce používaných s cloudem je podobné postupům používaným pro všechny místní pracovní stanice – například minimalizovaná oprávnění k sestavení a omezující oprávnění. Některé jedinečné aspekty správy cloudu se více podobají vzdálené nebo podnikové správě mimo IP síť. Patří sem používání a auditování přihlašovacích údajů, vzdálený přístup s rozšířeným zabezpečením a detekce a reakce na ohrožení.

### <a name="authentication"></a>Authentication
K omezení zdrojových IP adres pro přístup k nástrojům pro správu a požadavkům na audit přístupu můžete použít přihlašovací omezení Azure. Abyste Azure pomohli s identifikací klientů pro správu (pracovní stanice nebo aplikace), můžete nakonfigurovat rozhraní SMAPI (prostřednictvím zákazníkem vyvinutých nástrojů, například rutin Windows PowerShell) a web Azure Portal tak, aby na straně klienta vyžadovaly instalaci certifikátů pro správu (kromě certifikátů SSL). V případě přístupu pro správce doporučujeme vyžadovat vícefaktorové ověřování.

Některé aplikace nebo služby, které nasadíte do Azure, můžou mít vlastní mechanismy ověřování pro přístup koncových uživatelů i správce, zatímco ostatní plně využívají výhody Azure AD. V závislosti na tom, jestli federujete přihlašovací údaje prostřednictvím služby Active Directory Federation Services (AD FS), vám synchronizace adresářů nebo správa uživatelských účtů výhradně v cloudu a pomocí [Microsoft Identity Manager](https://technet.microsoft.com/library/mt218776.aspx) (část Azure AD Premium) pomůže se správou životních cyklů identit mezi prostředky.

### <a name="connectivity"></a>Připojení
K zabezpečení připojení klientů k virtuálním sítím Azure je k dispozici několik mechanismů. Dva z těchto mechanismů [VPN typu site-to-site](https://channel9.msdn.com/series/Azure-Site-to-Site-VPN) (S2S) a [VPN typu point-to-site](../vpn-gateway/vpn-gateway-point-to-site-create.md) (P2S) umožňují používání standardního protokolu IPsec (S2S) nebo SSTP ([Secure Socket Tunneling Protocol](https://technet.microsoft.com/magazine/2007.06.cableguy.aspx)) (P2S) pro šifrování a tunelové propojení. Když se Azure připojuje ke správě veřejně přístupných služeb Azure, například k webu Azure Portal, vyžaduje protokol HTTPS (Hypertext Transfer Protocol Secure).

Samostatná posílená pracovní stanice, která není k Azure připojená prostřednictvím brány VP, musí k vytvoření počátečního připojení ke službě Azure Virtual Network používat síť VPN typu Point-to-Site s protokolem SSTP a potom musí vytvořit připojení RDP k jednotlivým virtuální počítačům zevnitř tunelu VPN.

### <a name="management-auditing-vs-policy-enforcement"></a>Auditování správy vs. vynucení zásad
K zabezpečení procesů správy můžete obvykle použít dva přístupy: auditování a vynucování zásad. Provádění obou zajistí všestrannou kontrolu, ale nemusí být ve všech situacích možné. Kromě toho má každý přístup různé úrovně rizika, nákladů a úsilí, které souvisejí se správou zabezpečení, zejména v oblasti úrovně vztahu k  důvěryhodnosti jednotlivců i systémových architektur.

Sledování, protokolování a auditování poskytují základ pro sledování a pochopení aktivit správy. Podrobné auditování všech akcí nemusí být vždy proveditelné vzhledem k množství dat, která jsou při tom vytvářena. Přesto za osvědčený postup považujeme auditování účinnosti zásad správy.

Vynucení zásad zahrnující přísnou kontrolu přístupu staví programové mechanismy na místo, které může řídit akce správce, a pomáhá zajistit používání všech možných ochranných opatření. Protokolování zajišťuje důkazy o vynucení a také záznamy o tom, kdo to udělal, odkud a kdy. Protokolování také umožňuje provádět auditování a porovnávání informací o tom, jak správci dodržují zásady, a poskytuje důkazy o aktivitách.

## <a name="client-configuration"></a>Konfigurace klienta
V případě posílených pracovních stanic doporučujeme, tři primární konfigurace. Největšími rozdíly mezi nimi jsou náklady, použitelnost a přístupnost při zachování podobného profilu zabezpečení u všech možností. Následující tabulka nabízí krátkou analýzu výhod a rizik každé z nich. (Všimněte si, že „podnikovým počítačem“ se rozumí stolní počítač ve standardní konfiguraci, který se nasazuje pro všechny uživatele domény bez ohledu na jejich role.)

| Konfigurace | Výhody | Nevýhody |
| --- | --- | --- |
| Samostatná posílená pracovní stanice |Přísně řízená pracovní stanice |vyšší náklady na vyhrazené stolní počítače |
| - | Menší riziko zneužití aplikací |Zvýšené úsilí vynaložené na správu |
| - | Jasné oddělení povinností | - |
| Podnikový počítač jako virtuální počítač |Snížení náklady na hardware | - |
| - | Odloučení role a aplikací | - |
| Windows používá šifrování jednotky pomocí BitLockeru |Kompatibilita s většinou počítačů |Sledování prostředků |
| - | Nákladová efektivnost a přenositelnost | - |
| - | Izolované prostředí správy |- |

Je důležité, aby posílená pracovní stanice byla hostitelem a ne hostem a aby neobsahovala nic, co by stálo mezi hostitelským operačním systémem a hardwarem. Dodržování „zásady čistého zdroje“ (také označovaného jako „bezpečný původ“) znamená, že hostitel musí být posílený nejvíc. V opačném případě bude posílená pracovní stanice (host) terčem útoků na systém, na kterém je hostovaná.

Funkce správy můžete odloučit ještě pomocí vyhrazených imagů systému pro každou posílenou pracovní stanici, která je vybavená jenom nástroji a oprávněními potřebnými pro správu vybraných aplikací Azure a cloudu s konkrétními místními GPO v AD DS pro nezbytné úlohy.

V případě prostředí IT, která nemají místní infrastrukturu (například žádný přístup k místní instanci AD DS pro GPO, protože všechny servery jsou v cloudu), můžou služby jako [Microsoft Intune](https://technet.microsoft.com/library/jj676587.aspx) zjednodušit nasazování a údržbu konfigurací pracovních stanic.

### <a name="stand-alone-hardened-workstation-for-management"></a>Samostatná posílená pracovní stanice určená pro správu
V případě samostatné posílené pracovní stanice mají správci stolní nebo přenosný počítač používaný pro úlohy správy a jiný, samostatný stolní nebo přenosný počítač pro úlohy, které nepotřebují žádné oprávnění. Pracovní stanice vyhrazená pro správu služeb Azure nepotřebuje instalaci dalších aplikací. Kromě toho používání pracovních stanic, které podporují [služby TPM (Trusted Platform Module)](https://technet.microsoft.com/library/cc766159) (TPM) nebo podobné technologie šifrování na úrovni hardwaru, usnadňuje ověřování zařízení a ochranu před napadením. TPM může také podporovat ochranu celého svazku systémové jednotky jejím [šifrováním pomocí BitLockeru](https://technet.microsoft.com/library/cc732774.aspx).

V případě scénáře se samostatnou posílenou pracovní stanicí (viz následující obrázek) je místní instance brány Windows Firewall (nebo brány firewall klienta jiného výrobce) nakonfigurovaná tak, aby blokovala příchozí připojení, například RDP. Správce se může přihlásit k posílené pracovní stanici a spustit relaci RDP, která se připojuje k Azure po navázání připojení VPN ke službě Azure Virtual Network, ale nemůže se přihlásit k podnikovému počítači pomocí RDP, aby se připojil k samotné posílené pracovní stanici.

![][2]

### <a name="corporate-pc-as-virtual-machine"></a>Podnikový počítač jako virtuální počítač
V případech, kdy je samostatná posílená pracovní stanice nákladově nevýhodná nebo nevhodná, se může posílená pracovní stanice stát hostitelem virtuálního počítače určeného k provádění úloh, které nepotřebují žádné oprávnění.

![][3]

Abyste zabránili několika bezpečnostním rizikům, která mohou vyplývat z používání jedné pracovní stanice pro správu systémů a další každodenní pracovní úlohy, můžete na posílenou pracovní stanici nasadit virtuální počítač Windows Hyper-V. Tento virtuální počítač můžete používat jako podnikový počítač. Prostředí s podnikovými počítači může zůstat izolované od hostitele. Tím omezíte prostor k útokům a oddělíte denní aktivity uživatele (například příjem e-mailů) od citlivých úloh správy.

Virtuální počítač s podnikovým počítačem běží v chráněném prostoru a poskytuje uživatelské aplikace. Hostitel zůstává „čistým zdrojem“ a vynucuje přísné síťové zásady v kořenovém operačním systému (například blokuje přístup RDP z virtuálního počítače).

### <a name="windows-to-go"></a>Windows To Go
Jinou alternativou k požadavku na samostatnou posílenou pracovní stanici je jednotka [Windows To Go](https://technet.microsoft.com/library/hh831833.aspx). Jedná se o funkci, která podporuje možnost spouštění z USB na straně klienta. Windows To Go uživatelům umožňuje spouštění kompatibilního počítače s izolovaným imagem systému, která běží na šifrovaném USB flash disku. Nabízí další ovládací prvky pro vzdáleně spravované koncové body, protože image může plně spravovat podniková skupina IT pomocí přísných bezpečnostních zásad, minimalistického sestavení operačního systému a podpory čipu TPM.

Na obrázku níže představuje přenosný image systém připojený k doméně, který je předem nakonfigurovaný pro připojení výhradně k Azure, vyžaduje vícefaktorové ověřování a blokuje veškerý provoz, který se netýká správy. Pokud uživatel spustí stejný počítač se standardním podnikovým imagem a pokusí se přistoupit k bráně VP, aby se dostal k nástrojům pro správu Azure, relace se zablokuje. Windows To Go se stává operačním systémem na kořenové úrovni a nejsou potřeba žádné další vrstvy (hostitelský operační systém, hypervisor, virtuální počítač), které můžou být zranitelnější vůči útokům zvenku.

![][4]

Je důležité si uvědomit, že USB flash disky se dají ztratit mnohem snadněji než průměrný stolní počítač. K šifrování celého svazku používejte BitLocker společně se silným heslem. Podstatně tím snížíte pravděpodobnost, že útočník image disku využije ke škodlivým účelům. Pokud USB flash disk ztratíte, použijte odvolání a [vydání nového certifikátu pro správu](https://technet.microsoft.com/library/hh831574.aspx) a rychlé resetování hesla, abyste omezili možnost zpřístupnění citlivých údajů. Protokoly auditu správy jsou uloženy v Azure a ne v klientovi. Tím se ještě víc omezuje riziko potenciální ztráty dat.

## <a name="best-practices"></a>Osvědčené postupy
Když spravujete aplikace a data v Azure, vezměte v úvahu následující pokyny.

### <a name="dos-and-donts"></a>Pro a proti
Nepředpokládejte, že po uzamčení pracovní stanice už není nutné splnit další běžné požadavky na zabezpečení. Potenciální riziko je vyšší kvůli vyšší úrovni přístupu, které obvykle mají účty správců. V následující tabulce jsou uvedeny příklady rizik a jim odpovídající bezpečné postupy.

| Chybný postup | Správný postup |
| --- | --- |
| Neposílejte přihlašovací údaje pro přístup správce nebo jiné tajné údaje e-mailem (např. SSL nebo certifikáty pro správu). |Zachovávejte důvěrnost tak, že názvy účtů a hesla sdělíte ústně (ale neukládejte je do hlasové pošty), provádějte vzdálenou instalaci certifikátů klienta a serveru (prostřednictvím šifrované relace), stahujte z chráněných síťových sdílených položek nebo data předávejte ručně pomocí vyměnitelného média. |
| - | Životní cykly certifikátu pro správu spravujte proaktivně. |
| Neukládejte hesla účtů do úložiště aplikací (například tabulek, webů SharePoint nebo sdílených složek) v nezašifrované podobě nebo bez použití algoritmu hash. |Vytvořte zásady pro správu zabezpečení a zásady pro posilování systému a používejte je na svoje vývojové prostředí. |
| - | K zajištění řádného přístup k webům Azure s protokoly SSL a TLS používejte pravidla pro připnutí certifikátu ze sady nástrojů [Enhanced Mitigation Experience Toolkit 5.5](https://technet.microsoft.com/security/jj653751). |
| Nesdílejte účty a hesla mezi správci a nepoužívejte stejné heslo pro několik účtů nebo služeb (zejména v případě účtů na sociálních sítích nebo účtů pro jiné běžné aktivity). |Vytvořte si vyhrazený účet Microsoft pro správu svého předplatného Azure – účet, který nebudete používat pro osobní e-maily. |
| Konfigurační soubory neposílejte e-mailem. |Konfigurační soubory a profily vždy instalujte z důvěryhodného zdroje (například ze šifrovaného USB flash disku). Nepoužívejte mechanismy, který se dají snadno ohrozit, například e-mail. |
| Nepoužívejte slabá nebo jednoduchá přihlašovací hesla. |Prosazujte zásady silných hesel, cykly vypršení platnosti (změna po prvním použití), časové limity konzoly a automatické uzamykání účtů. K přístupu do úložiště hesel používejte systém pro správu hesel klientů s vícefaktorovým ověřováním. |
| Nevystavujte porty pro správu přístupu z internetu. |Uzamkněte porty a IP adresy Azure a omezte tak přístup ke správě. Další informace najdete v dokumentu white paper k [zabezpečení sítě Azure](http://download.microsoft.com/download/4/3/9/43902EC9-410E-4875-8800-0788BE146A3D/Windows%20Azure%20Network%20Security%20Whitepaper%20-%20FINAL.docx). |
| - | Používejte bránu firewall, připojení VPN a architektury NAP pro všechna připojení správy. |

## <a name="azure-operations"></a>Provoz Azure
Provozní technici a zaměstnanci podpory Microsoftu, kteří přistupují k produkčním systémům Azure, používají [posílené pracovní stanice s virtuálními počítači](#stand-alone-hardened-workstation-for-management), které jsou na nich zřízené pro potřeby interního přístupu k podnikové síti a k aplikacím (například e-mailu, intranetu atd.). Všechny počítače pracovních stanic pro správu jsou vybavené čipy TPM a spouštěcí jednotka hostitele je zašifrovaná pomocí BitLockeru. Počítače jsou připojené ke speciální organizační jednotce (OU) v primární podnikové doméně Microsoftu.

Posilování systému se vynucuje prostřednictvím zásad skupiny s centralizovanou aktualizací softwaru. Protokoly událostí z auditování a analýz (například zabezpečení a AppLockeru) jsou shromažďovány z pracovních stanic pro správ a ukládány do centrálního umístění.

Kromě toho se pro připojení k produkční síti Azure používají pracovní stanice typu „jump box“, které jsou v síti Microsoftu a které vyžadují dvoufaktorové ověřování.

## <a name="azure-security-checklist"></a>Kontrolní seznam zabezpečení Azure
Minimalizace počtu úloh, které můžou správci provádět na posílené pracovní stanici, usnadní minimalizaci prostoru k útokům na prostředí pro vývoj a správu. K ochraně posílené pracovní stanice používejte následující technologie:

* Posílení Internet Exploreru. Prohlížeč Internet Explorer (nebo jakýkoli jiný webový prohlížeč) je klíčovou vstupní branou škodlivého kódu, protože navazuje rozsáhlá připojení k externím serverům. Projděte zásady svého klienta a vynuťte běh v chráněném režimu, zákaz doplňků, zákaz stahování souborů a používejte filtrování [Microsoft SmartScreen](https://technet.microsoft.com/library/jj618329.aspx). Ověřte, že se zobrazují upozornění týkající se zabezpečení. Využijte výhody nastavení zón internetu a vytvořte si seznam důvěryhodných webů, pro které máte nakonfigurované přiměřené posílení zabezpečení. Blokujte všechny ostatní weby a kódy v prohlížeči, například ActiveX a Javu.
* Standardní uživatel. Práce v režimu standardního uživatele přináší řadu výhod. Největší z nich je ta, že krádež přihlašovacích údajů správce prostřednictvím malwaru je podstatně obtížnější. Standardní uživatelský účet kromě toho nemá oprávnění vyšší úrovně v kořenovém operačním systému a mnoho možností konfigurace a rozhraní API je ve výchozím nastavení uzamčeno.
* AppLocker. K omezení programů a skriptů, které můžou uživatelé spouštět, můžete použít [AppLocker](http://technet.microsoft.com/library/ee619725.aspx). AppLocker můžete spustit v režimu auditování nebo vynucení. Ve výchozím nastavení má AppLocker povolovací pravidlo, které umožňuje uživatelům s tokenem správce spouštět všechny kódy na straně klienta. Účelem tohoto pravidla je ochrana správců před tím, aby sami sebe uzamkli a platí jenom pro zvýšené tokeny. Další informace najdete v článku o integritě kódu jako součásti [zabezpečení jádra](http://technet.microsoft.com/library/dd348705.aspx) systému Windows Server.
* Podepisování kódu. Podepisování kódu všech nástrojů a skriptů, které správci používají, nabízí ovladatelné mechanismy pro nasazování zásad, které slouží k uzamčení aplikací. Škálování algoritmů hash neodpovídá rychlým změnám kódu a cesty k souborům neposkytují vysokou úroveň zabezpečení. Doporučujeme, abyste zkombinovali pravidla AppLockeru se [zásadami spouštění](http://technet.microsoft.com/library/ee176961.aspx) prostředí PowerShell, které umožňuje [spouštění](http://technet.microsoft.com/library/hh849812.aspx) jenom konkrétních podepsaných kódů a skriptů.
* Zásady skupiny. Vytvořte globální zásady správy, které se budou používat na libovolné pracovní stanice domény, které se používají pro správu (a zablokujte přístup ze všech ostatních), a na uživatelské účty na těchto pracovních stanicích ověřené.
* Zřizování s rozšířeným zabezpečením. Zabezpečte svůj image posílené pracovní stanice, abyste byli lépe chráněni před manipulací. Používejte bezpečnostní opatření, například šifrování a izolaci k ukládání imagů, virtuálních počítačů a skriptů a omezte přístup (použijte třeba auditovatelný proces vrácení a rezervace).
* Opravy chyb. Udržujte konzistentní sestavení (nebo mějte samostatné image pro vývoj, provoz a další úlohy správy), pravidelně kontrolujte změny a malware, udržujte sestavení v aktuálním stavu a počítače aktivujte jenom v případě potřeby.
* Šifrování. Zajistěte, aby pracovní stanice pro správu byly vybaveny čipy TPM pro bezpečnější povolování [systému souborů EFS](https://technet.microsoft.com/library/cc700811.aspx) (Encrypting File System) a BitLockeru. Pokud používáte Windows To Go, používejte jenom šifrované USB klíče spolu s BitLockerem.
* Řízení. K řízení všech rozhraní Windows pro správce, například sdílení souborů, používejte GPO v AD DS. Zahrňte pracovní stanice pro správu do procesů auditování, sledování a protokolování. Sledujte všechny přístupy a chování správců a vývojářů.

## <a name="summary"></a>Souhrn
Používání konfigurace posílené pracovní stanice ke správě cloudových služeb Azure, služby Virtual Machines a aplikací vám může pomoct s omezením řady rizik a hrozeb, které vyplývají ze vzdálené správy kritické infrastruktury IT. Azure i Windows poskytují mechanismy, které můžete použít k ochraně a řízení komunikace, ověřování a chování klienta.

## <a name="next-steps"></a>Další kroky
Následující prostředky vám poskytnou další obecné informace o Azure a souvisejících službách Microsoftu jako doplnění ke konkrétním položkám, na které odkazujeme v tomto dokumentu:

* [Zabezpečení privilegovaného přístupu](https://technet.microsoft.com/library/mt631194.aspx) – technické podrobnosti o navrhování a vytváření zabezpečených pracovních stanic pro správu v prostředí Azure
* [Centrum zabezpečení Microsoft](https://microsoft.com/en-us/trustcenter/cloudservices/azure) – další informace o možnostech platformy Azure, které chrání prostředky infrastruktury Azure, a úlohách, které běží na Azure
* [Středisko Microsoft Security Response Center](https://technet.microsoft.com/en-us/security/dn440717.aspx) – místo pro nahlášení chyby zabezpečení včetně problémů s Azure, popřípadě použijte e-mail [secure@microsoft.com](mailto:secure@microsoft.com)
* [Blog o bezpečnosti Azure](http://blogs.msdn.com/b/azuresecurity/) – nejnovější informace o zabezpečení Azure

<!--Image references-->
[1]: ./media/azure-security-management/typical-management-network-topology.png
[2]: ./media/azure-security-management/stand-alone-hardened-workstation-topology.png
[3]: ./media/azure-security-management/hardened-workstation-enabled-with-hyper-v.png
[4]: ./media/azure-security-management/hardened-workstation-using-windows-to-go-on-a-usb-flash-drive.png
