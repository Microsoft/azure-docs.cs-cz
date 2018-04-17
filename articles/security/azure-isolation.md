---
title: Izolace ve veřejném cloudu Azure | Microsoft Docs
description: Další informace o cloudové výpočetní služby, které zahrnují široký výběr výpočetních instancích & služby, které je možné škálovat nahoru či dolů automaticky podle potřeb vaší aplikace nebo enterprise.
services: security
documentationcenter: na
author: UnifyCloud
manager: swadhwa
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: 996079e0062bf1e24ae2bf24354a94167e6adff3
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="isolation-in-the-azure-public-cloud"></a>Izolace ve veřejném cloudu Azure
##  <a name="introduction"></a>Úvod
### <a name="overview"></a>Přehled
Pomůže Azure aktuální a potenciální zákazníky, pochopit a využívat různé související se zabezpečením možnosti dostupné v a které obaluje platformy Azure, společnost Microsoft vyvinula řadu dokumenty White Paper, zabezpečení přehledy, osvědčené postupy a Kontrolní seznamy.
Témata v rozsahu z hlediska spektra a hloubky a jsou pravidelně aktualizovány. Tento dokument je součástí této řady dle souhrnu v následující části abstraktní.

### <a name="azure-platform"></a>Platformy Azure
Azure je platforma otevřené a flexibilní cloudové služby, která podporuje nejširší výběr operačních systémů, programovací jazyky, rozhraní, nástroje, databází a zařízení. Můžete například provést následující věci:
- Spusťte Linux kontejnery s integrace Dockeru;
- Vývoj aplikací pomocí jazyka JavaScript, Python, .NET, PHP, Java a Node.js; a
- Sestavení back EndY pro iOS, Android a Windows zařízení.

Microsoft Azure podporuje stejné technologie miliony vývojářů a IT profesionály již spoléhají na a vztah důvěryhodnosti.

Pokud sestavení nebo IT prostředky se mají migrovat, poskytovatele služeb veřejného cloudu, můžete se spoléhat na dané organizace dalo k ochraně vašich aplikací a dat se službami a ovládací prvky poskytují ke správě zabezpečení vaše cloudové prostředky.

Infrastruktura Azure je od zařízení až po aplikace navržena tak, aby umožňovala hostování milionů zákazníků současně a poskytovala důvěryhodný základ pro splnění potřeb zabezpečení jednotlivých podniků. Azure navíc poskytuje širokou škálu konfigurovatelných možností zabezpečení a umožňuje jejich nastavování, takže můžete zabezpečení přizpůsobit jedinečným požadavkům svého nasazení. Tento dokument vám tyto požadavky splňují pomůže.

### <a name="abstract"></a>Abstraktní

Microsoft Azure umožňuje spouštění aplikací a virtuální počítače (VM) na sdílený fyzické infrastruktuře. Jeden z prvotní hospodářského motivace pro spuštění aplikace v prostředí cloudu je schopnost distribuovat náklady na sdílených prostředků mezi více zákazníků. Tento postup víceklientský ke zlepšení efektivity multiplexní prostředky mezi různorodých zákazníků s nízkými náklady. Bohužel se také zavádí riziko sdílení fyzických serverů a spuštění citlivých aplikací a virtuálních počítačů, které může patřit k libovolné a potenciálně škodlivého uživatelský jiných prostředků infrastruktury.

Tento článek popisuje, jak Microsoft Azure poskytuje izolaci proti škodlivým i škodlivý uživatele a funguje jako vodítko pro architekturu řešení cloud tím, že nabízí různé možnosti izolace pro architekty. Tento dokument white paper se zaměřuje na technologii platformy Azure a ovládací prvky zabezpečení zákazníka přístupem a nebude pokoušet o adresu SLA, ceny modely a DevOps postupem aspekty.

## <a name="tenant-level-isolation"></a>Úroveň izolace klienta
Jeden z primární výhody technologie cloud computing je koncept sdílené běžné infrastruktury současně napříč řady zákazníků, vede k úsporám z rozsahu. Tento koncept se nazývá víceklientská architektura. Microsoft funguje nepřetržitě zajistit, že víceklientské architektuře Microsoft Azure Cloud podporuje zabezpečení, důvěrnost, ochrany osobních údajů, integrity a dostupnosti standardy.

V prostředí cloudu se dá klient služby Azure AD definovat jako klient nebo organizace, která vlastní a spravuje konkrétní instanci dané cloudové služby. Microsoft Azure poskytuje identitu platformy klient je jednoduše vyhrazenou instancí služby Azure Active Directory (Azure AD), vaše organizace obdrží a vlastní, když se přihlásí ke cloudové službě Microsoftu.

Každý adresář služby Azure AD je oddělený od ostatních adresářů služby Azure AD. Adresář služby Azure AD byl navržen tak, aby se jednalo o zabezpečený prostředek k použití pouze pro vaši organizaci – stejně jako je podniková kancelářská budova zabezpečeným prostředkem výhradně vaší organizace. Architektura služby Azure AD znemožňuje míchání dat zákazníků a informací o identitě. To znamená, že se uživatelé a správci jednoho adresáře služby Azure AD nemohou dostat – ať už omylem nebo záměrně – k datům v jiném adresáři.

### <a name="azure-tenancy"></a>Azure klientů
Azure klientů (předplatné Azure) odkazuje na relaci "zákazníka nebo billing" a jedinečný [klienta](https://docs.microsoft.com/azure/active-directory/develop/active-directory-howto-tenant) v [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis). Úrovně izolaci klientů v Microsoft Azure je dosaženo pomocí služby Azure Active Directory a [ovládací prvky založené na rolích](https://docs.microsoft.com/azure/role-based-access-control/overview) nabízené ho. Každé předplatné Azure je přidružen jeden adresář Azure Active Directory (AD).

Uživatelé, skupiny a aplikací z adresáře, můžete spravovat prostředky v rámci předplatného Azure. Můžete přiřadit tyto přístupová práva pomocí portálu Azure, nástroje příkazového řádku Azure a rozhraní API pro správu Azure. Klient služby Azure AD je logicky izolované pomocí hranice zabezpečení, aby žádné zákazníka lze zobrazit nebo ohrozit společné klientů, závadně nebo náhodně. Azure AD je spuštěna na serverech "holý počítač" izolované v segmentu oddělené sítě, kde filtrování paketů na úrovni hostitele a brány Windows Firewall blokovat nežádoucí připojení a provozu.

- Přístup k datům ve službě Azure AD vyžaduje ověření uživatele pomocí služby tokenů zabezpečení (STS). Informace o existence, povoleném stavu a role uživatele se používá ověřování systému k určení, zda je požadovaný přístup do cílového klienta autorizovaný pro tohoto uživatele v této relaci.

![Azure klientů](./media/azure-isolation/azure-isolation-fig1.png)


- Klienti používají diskrétní kontejnery a neexistuje žádný vztah mezi tyto.

- Žádný přístup mezi klienty, pokud ho správce klienta uděluje prostřednictvím federaci nebo zřizování uživatelských účtů z jiných klientů.

- Fyzický přístup k serverům, které tvoří služby Azure AD a přímý přístup k Azure AD back-end systémy, je omezen.

- Azure AD Uživatelé nemají přístup k fyzické prostředky nebo umístění, a proto není možné vynechat logické RBAC zásad kontroly uvádí následující.

Pro diagnostiku a potřebuje údržby je provozní model, který využívá systém zvýšení oprávnění za běhu vyžaduje a používat. Azure AD Privileged Identity Management (PIM) zavádí koncepci oprávněné správce. [Oprávněné admins](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) by měla být uživatelům, kteří potřebují privilegovaný přístup teď a potom, ale ne každý den. Role je neaktivní, dokud uživatel potřebuje přístup, pak se dokončit proces aktivace a stane aktivní správce po předem určenou dobu.

![Azure AD Privileged Identity Management](./media/azure-isolation/azure-isolation-fig2.png)

Azure Active Directory je hostitelem každého klienta v vlastní chráněné kontejneru, zásadami a oprávnění a v kontejneru výhradně vlastněna a řízena klienta.

Koncept kontejnery klienta je úzce ingrained v adresářové službě na všechny vrstvy, z portálů úplně do trvalého úložiště.

I v případě, že metadata od víc klientů služby Azure Active Directory je uložený na stejném fyzickém disku, není žádný vztah mezi kontejnerů než co je definováno adresářová služba, která zase závisí správce klienta.

### <a name="azure-role-based-access-control-rbac"></a>Řízení přístupu Azure na základě rolí (RBAC)
[Azure na základě rolí řízení přístupu (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) umožňuje sdílet různé součásti, které jsou k dispozici v rámci předplatného Azure tím, že poskytuje vyladění správy přístupu pro Azure. Azure RBAC umožňuje oddělit povinností v rámci vaší organizace a udělit přístup podle toho, co uživatelé potřebují k provádění svých úloh. Namísto udělení každý uživatel neomezený oprávnění v předplatného Azure nebo prostředky, můžete povolit jenom určité akce.

Azure RBAC má tři základní role, které platí pro všechny typy prostředků:

- **Vlastník** má úplný přístup ke všem prostředkům, včetně právo delegovat přístup k ostatním.

- **Přispěvatel** můžete vytvářet a spravovat všechny typy prostředků Azure, ale nelze udělit přístup ostatním uživatelům.

- **Čtečka** můžete zobrazit stávající prostředky Azure.

![Řízení přístupu na základě role Azure](./media/azure-isolation/azure-isolation-fig3.png)

Zbytek role RBAC v Azure povolit správu konkrétních prostředků Azure. Například role Přispěvatel virtuálních počítačů umožňuje uživatelům vytvářet a spravovat virtuální počítače. Nedává je přístup k službě Azure Virtual Network nebo podsíť, které se virtuální počítač připojí k.

[Předdefinované role RBAC](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) vytvořte seznam rolí v Azure k dispozici. Určuje operace a rozsah, který každé předdefinované role uděluje uživatelům. Pokud zjišťujete, kam můžete definovat vlastní role pro ještě větší kontrolu, najdete v části jak sestavit [vlastní role v Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/custom-roles).

Některé další možnosti pro Azure Active Directory patří:
- Azure AD umožňuje jednotné přihlašování pro aplikace SaaS, bez ohledu na to, kde jsou hostované. Některé aplikace jsou federované pomocí Azure AD, jiné používají jednotné přihlašování pomocí hesla. Federované aplikace také podporují zřizování uživatelů a [heslo překlenutí vyrovnávací paměti](https://www.techopedia.com/definition/31415/password-vault).

- Přístup k datům v rámci [Azure Storage](https://azure.microsoft.com/services/storage/) je řízen prostřednictvím ověřování. Každý účet úložiště má primární klíč ([klíč účtu úložiště](https://docs.microsoft.com/azure/storage/storage-create-storage-account), nebo SAK) a sekundární tajný klíč (sdílený přístupový podpis, nebo SAS).

- Azure AD poskytuje Identity jako služby pomocí federování pomocí [Active Directory Federation Services](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-azure-adfs), synchronizace a replikace s místních adresářů.

- [Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication) je služba služby Multi-Factor authentication, která od uživatelů vyžaduje přihlášení ověřili pomocí mobilní aplikace, telefonního hovoru nebo textové zprávy. Můžete použít s Azure AD k zabezpečené místní prostředků s Azure Multi-Factor Authentication server a také se vlastní aplikace a adresáře pomocí sady SDK.

- [Azure AD Domain Services](https://azure.microsoft.com/services/active-directory-ds/) umožňuje připojit virtuální počítače Azure k doméně služby Active Directory bez nasazení řadiče domény. Může přihlásit do těchto virtuálních počítačů pomocí svoje podnikové přihlašovací údaje služby Active Directory a spravovat virtuální počítače připojené k doméně pomocí zásad skupiny k vynucení směrné plány zabezpečení na všechny vaše virtuální počítače Azure.

- [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) poskytuje službu správy vysokou dostupností globální identity pro určených aplikace, která je škálovatelná pro stovky milionů identit. Dá se integrovat do mobilních i webových platforem. Uživatele můžete přihlásit ke všem aplikacím, které prostřednictvím přizpůsobitelné pomocí svých účtů na sociálních nebo vytváření přihlašovacích údajů.

### <a name="isolation-from-microsoft-administrators--data-deletion"></a>Izolace z Microsoft Administrators & odstranění dat
Společnost Microsoft má silné míry data chránit před nevhodný přístup, nebo použijte neoprávněné osoby. Tyto provozní postupy a ovládací prvky jsou zajišťované [Online služby podmínky](http://aka.ms/Online-Services-Terms), které nabízejí smluvními závazky, které řídí přístup k datům.

-   Microsoft technici nemají výchozí úroveň přístupu k datům v cloudu. Místo toho že je uděleno oprávnění, pod správu dohledu, pouze v případě potřeby. Tento přístup je pečlivě řídí a protokolovat a odvolat, když už ho nepotřebují.

-   Microsoft může najímáme jiné společnosti k poskytování omezených služeb jejím jménem. Subdodavatelů může přistupovat k datům zákazníka pouze k poskytování služeb, ke kterým, si najímáme mají poskytovat a mají zakázáno používat za žádným jiným účelem. Dál platí že jsou svázané smluvně na zachovávat mlčenlivost o informace o našich zákazníků.

Služby pro firmy s auditované certifikace například ISO/IEC 27001 jsou pravidelně ověřit pomocí Microsoft a podniky, akreditované auditu, které provést ukázka audity na ověřit identitu této přístup pouze pro účely legitimní podnikání. Kdykoli a z jakéhokoli důvodu jsou k dispozici vždy zákaznických údajů.

Pokud odstraníte všechna data, odstraní Microsoft Azure data, včetně všech v mezipaměti nebo záložní kopie. Pro služby ve oboru, které do 90 dnů po skončení období uchování dojde k odstranění. (V oboru služby jsou definovány v části podmínky pro zpracování dat naše [Online služby podmínky](http://aka.ms/Online-Services-Terms).)

Pokud je disk disková jednotka používané pro úložiště vyskytne selhání hardwaru, je bezpečně [vymazat nebo zničeno](https://www.microsoft.com/trustcenter/Privacy/You-own-your-data) než Microsoft vrátí ji do výrobce pro nahrazení nebo oprava. Data na jednotce se přepíšou zajistit, že data nelze obnovit a to jakýmkoli způsobem.

## <a name="compute-isolation"></a>Výpočetní izolace
Microsoft Azure poskytuje různé cloudové výpočetní služby, které zahrnují široký výběr výpočetních instancích & služby, které je možné škálovat nahoru či dolů automaticky podle potřeb vaší aplikace nebo enterprise. Tyto instance výpočetní a služby nabízejí izolace na více úrovních k zabezpečení dat, aniž by došlo ke ztrátě flexibilitu při konfiguraci této poptávky zákazníků.

### <a name="hyper-v--root-os-isolation-between-root-vm--guest-vms"></a>Technologie Hyper-V a kořenové OS izolaci mezi kořenové virtuálních počítačů & hostované virtuální počítače
Výpočetní platformě Azure je založena na virtualizaci počítače, což znamená, že veškerý kód zákazníka provede ve virtuálním počítači technologie Hyper-V. Na každém uzlu Azure (nebo koncový bod sítě) je Hypervisor, který běží přímo nad hardwarem a rozděluje uzlu do proměnné číslo z hostované virtuální počítače (VM).


![Technologie Hyper-V a kořenové OS izolaci mezi kořenové virtuálních počítačů & hostované virtuální počítače](./media/azure-isolation/azure-isolation-fig4.jpg)


Každý uzel má také jeden speciální kořenové virtuální počítač, který se spouští hostitelským operačním systémem. Kritické hranic je izolaci kořenu virtuální počítač z hostované virtuální počítače a hostovaný virtuální počítače od sebe navzájem, spravuje hypervisoru a kořenový operačního systému. Párování hypervisoru/root OS využívá společnosti Microsoft desetiletí prostředí zabezpečení operačního systému a novější learning od společnosti Microsoft Hyper-V, k poskytování silnou izolaci virtuálních počítačů hosta.

Platforma Azure používá virtualizované prostředí. Uživatelské instance fungovat jako samostatné virtuální počítače, které nemají přístup k serveru fyzického hostitele, a tato izolace se vynucuje pomocí fyzického procesoru úrovně oprávnění (prstenec-0 nebo prstenec-3).

Úroveň 0 má nejvyšší oprávnění, úroveň 3 nejnižší. Hostovaný operační systém běží v 1 menší privilegovaných prstenec, aplikace a spustit v nejnižšími oprávněními prstenec 3. Tato virtualizace fyzických prostředků vede k naprostému oddělení hostovaného operačního systému od hypervisoru. Důsledkem je další oddělení zabezpečení mezi těmito dvěma stranami.

Azure hypervisor funguje jako micro jádra a předává všechny požadavky na hardware přístup z hostované virtuální počítače na hostitele pro zpracování pomocí sdílené paměti rozhraní s názvem VMBus. Tento postup brání uživatelům v získání přímého přístupu k systému pro čtení, zápis a spouštění a snižuje riziko sdílení systémových prostředků.

### <a name="advanced-vm-placement-algorithm--protection-from-side-channel-attacks"></a>Pokročilé algoritmus umístění virtuálních počítačů a ochranu před útoky straně kanálu
Všechny cross-VM útok zahrnuje dva kroky: umístění virtuálních počítačů řízenou nežádoucí osoba na stejném hostiteli jako jeden z napadeného virtuální počítače a potom před nedodržením ukrást citlivé postižené informace nebo ovlivnit její výkon pro greed nebo vandalismu hranice izolace. Microsoft Azure poskytuje pomocí služby pokročilé algoritmus umístění virtuálních počítačů a ochranu před útoky kanál všechny známé straně, které jsou včetně virtuálních počítačů aktivní sousedním ochrany na oba kroky.

### <a name="the-azure-fabric-controller"></a>Kontroler prostředků infrastruktury Azure
Kontroleru prostředků infrastruktury Azure zodpovídá za přidělování prostředků infrastruktury pro úlohy klientů a spravuje jednosměrná komunikace z hostitele pro virtuální počítače. Algoritmus umístění virtuálních počítačů kontroleru prostředků infrastruktury Azure je složitější a téměř nemožné k předvídání jako fyzické úrovni hostitele.

![Kontroler prostředků infrastruktury Azure](./media/azure-isolation/azure-isolation-fig5.png)

Azure hypervisor vynucuje paměti a proces oddělení mezi virtuálními počítači a bezpečně směruje síťový provoz klientům hostovaného operačního systému. Tím se eliminuje možnost a straně kanál útoku na úrovni virtuálního počítače.

V Azure, kořenu virtuálního počítače je speciální: jeho operačním systémem, posílené názvem kořenové operačního systému hostujícím agenta prostředků infrastruktury (IM). DM se zase používají ke správě agentů hosta (GA) v rámci hostované operační systémy na zákazníka virtuálních počítačů. DM také spravovat uzly úložiště.

Kolekce Azure hypervisoru kořenový operačního systému nebo DM a zákazník virtuálních počítačů nebo plynu se skládá z výpočetního uzlu. DM spravuje kontroleru prostředků infrastruktury (FC), která již existuje mimo uzly výpočetního prostředí a úložiště (výpočetní clustery a clustery úložiště jsou spravovány samostatné FCs). Pokud zákazník aktualizací konfigurační soubor jejich aplikace je spuštěna, FC komunikuje s FA, poté kontaktuje plynu, který oznámení o změně konfigurace aplikace. V případě selhání hardwaru bude FC automaticky vyhledat dostupného hardwaru a restartujte virtuální počítač existuje.

![Kontroler prostředků infrastruktury Azure](./media/azure-isolation/azure-isolation-fig6.jpg)

Komunikace z Kontroleru prostředků infrastruktury pro agenta je jednosměrná. Agent implementuje službou pomocí protokolu SSL, která pouze odpovídá na požadavky z řadiče. Nebude možné inicializovat připojení ke kontroleru nebo jiných privilegované interní uzlů. FC zpracovává všechny odpovědi, jako kdyby nedůvěryhodné.


![Kontroleru prostředků infrastruktury](./media/azure-isolation/azure-isolation-fig7.png)

Izolace rozšiřuje z kořenové virtuálního počítače z hostované virtuální počítače a hostovaný virtuální počítače od sebe navzájem. Výpočetní uzly jsou i izolované od uzlů úložiště v zájmu lepší ochrany.


Hypervisor a hostitele operačního systému zadat síťový paket - filtry pomohou zajistit, že nedůvěryhodné virtuální počítače nelze generovat falešné provoz nebo přijímat přenosy, které nebyly upraveny, směrovat provoz na koncové body chráněné infrastruktury, nebo odesílání a přijímání nevhodný přenosy všesměrového vysílání.


### <a name="additional-rules-configured-by-fabric-controller-agent-to-isolate-vm"></a>Další pravidla konfigurovat tak, že Agent Kontroleru prostředků infrastruktury k izolaci virtuálních počítačů
Ve výchozím nastavení je veškerý provoz blokovány při vytvoření virtuálního počítače a pak agenta kontroleru prostředků infrastruktury nakonfiguruje filtr paketů přidat pravidla a výjimky, které umožňují oprávněným provoz.

Existují dvě kategorie pravidel, která jsou naprogramovaný tak:

-   **Počítač pravidla konfigurace nebo infrastruktury:** ve výchozím nastavení, všechna komunikace je zablokovaná. Existují výjimky, které umožňují virtuálního počítače do odesílat a přijímat přenosy DHCP a DNS. Virtuální počítače můžete také odesílá data na "veřejná" internet a odesílá data na ostatní virtuální počítače v rámci stejné virtuální síti Azure a je server aktivace operačního systému. Virtuální počítače seznam povolených odchozí cíle nezahrnuje Azure směrovač podsítě, správu Azure a další vlastnosti společnosti Microsoft.

-   **Role konfigurační soubor:** definuje příchozí seznamy řízení přístupu (ACL) na základě modelu služby klienta.

### <a name="vlan-isolation"></a>Izolace sítě VLAN
Existují tři sítí VLAN v každém clusteru:

![Izolace sítě VLAN](./media/azure-isolation/azure-isolation-fig8.jpg)


-   Hlavní sítě VLAN – propojení uzlů nedůvěryhodné zákazníka

-   Sítě VLAN FC – obsahuje důvěryhodné FCs a podporuje systémy

-   Zařízení sítě VLAN – obsahuje důvěryhodné sítě a jiných zařízeních infrastruktury

Komunikace je povolen ze sítě VLAN FC hlavní sítě VLAN, ale nelze inicializovat z hlavní sítě VLAN, FC sítě VLAN. Komunikace se taky zablokuje od hlavní sítě VLAN do zařízení sítě VLAN. To zaručuje, že i v případě, že dojde k narušení uzlu se systémem kód zákazníka, nelze útokům uzly na FC nebo zařízení sítě VLAN.

## <a name="storage-isolation"></a>Izolace úložiště
### <a name="logical-isolation-between-compute-and-storage"></a>Logická izolace mezi výpočetního prostředí a úložiště
Microsoft Azure jako součást základní návrh odděluje výpočtů na základě virtuálního počítače z úložiště. Toto rozdělení umožňuje výpočtů a úložiště nezávisle, škálovat usnadnit vám umožňuje víceklientská a izolace.

Proto Azure Storage spuštěna na samostatných s žádné síťové připojení k Azure Compute kromě logicky. [To](https://msenterprise.global.ssl.fastly.net/vnext/PDFs/A01_AzureSecurityWhitepaper20160415c.pdf) znamená, že když je vytvořen virtuální disk, místo na disku není přidělená pro své celý kapacity. Místo toho je vytvořen tabulky, který mapuje adresy na virtuálním disku do oblastí na fyzickém disku a tato tabulka je původně prázdné. **Při prvním zákazník zapisuje data na virtuální disk, je přiděleno místo na fyzickém disku a ukazatel k němu je umístěn v tabulce.**
### <a name="isolation-using-storage-access-control"></a>Řízení přístupu úložiště pomocí izolace
**Řízení přístupu v Azure Storage** má model řízení jednoduché přístupu. Každé předplatné Azure můžete vytvořit jeden nebo více účtů úložiště. Každý účet úložiště má jeden tajný klíč, který se používá k řízení přístupu ke všem datům v daném účtu úložiště.

![Řízení přístupu úložiště pomocí izolace](./media/azure-isolation/azure-isolation-fig9.png)

**Přístup k datům Azure Storage (včetně tabulek)** se dá řídit přes [SAS (sdíleného přístupového podpisu)](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1) token, který uděluje obor přístupu. SAS je vytvořeny šablonou dotazu (URL) podepsané [SAK (klíč účtu úložiště)](https://msdn.microsoft.com/library/azure/ee460785.aspx). Který [podepsané URL](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1) může dostat k jinému procesu (který se delegovala), který lze potom zadejte podrobnosti dotazu a podání žádosti o služby úložiště. SAS můžete udělit přístup založené na čase klientům, aniž by odhalil tajný klíč účtu úložiště.

SAS znamená, že jsme můžete udělit, že klient omezené oprávnění k objektům v našich účtu úložiště v zadaném časovém intervalu a se zadanou sadou oprávnění. Tato omezená oprávnění jsme můžete udělit bez nutnosti sdílet klíče pro přístup k účtu.

### <a name="ip-level-storage-isolation"></a>Izolace úrovně úložiště IP
Můžete určit brány firewall a definovat rozsah IP adres pro klienty důvěryhodné. Rozsah IP adres umožňuje připojení pouze klienti, kteří mají IP adresu v definovaném rozsahu k [Azure Storage](https://docs.microsoft.com/azure/storage/storage-security-guide).

Úložiště dat IP se dají chránit před neoprávněnými uživateli prostřednictvím sítě mechanismus, který se používá k přidělení vyhrazené nebo vyhrazené tunelového propojení přenosů dat do úložiště IP.

### <a name="encryption"></a>Šifrování
Azure nabízí následující typy šifrování k ochraně dat:
-   Šifrování během přenosu

-   Šifrování v klidovém stavu

#### <a name="encryption-in-transit"></a>Šifrování během přenosu
Šifrování během přenosu je mechanismus ochrany dat při přenosu v sítích. S Azure Storage můžete zabezpečit pomocí dat:

-   [Šifrování transportní vrstvy](https://docs.microsoft.com/azure/storage/storage-security-guide#encryption-in-transit), jako je například HTTPS při přenosu dat do nebo z Azure Storage.

-   [Propojit šifrování](../storage/common/storage-security-guide.md#using-encryption-during-transit-with-azure-file-shares), jako je například šifrování protokolu SMB 3.0 pro sdílené složky Azure File.

-   [Šifrování na straně klienta](https://docs.microsoft.com/azure/storage/storage-security-guide#using-client-side-encryption-to-secure-data-that-you-send-to-storage), k šifrování dat, než se přenese do úložiště a k dešifrování dat po se přenáší z úložiště.

#### <a name="encryption-at-rest"></a>Šifrování v klidovém stavu
Pro mnoho společností [šifrování dat v klidovém stavu](https://blogs.microsoft.com/cybertrust/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) je povinný krok k suverenity data o ochraně osobních údajů a dodržování předpisů a data. Existují tři Azure funkcí, které poskytují šifrování dat, která je "v klidovém stavu":

-   [Šifrování služby úložiště](https://docs.microsoft.com/azure/storage/storage-security-guide#encryption-at-rest) umožňuje požadovat, aby službu úložiště automaticky šifrování dat při zápisu do služby Azure Storage.

-   [Šifrování na straně klienta](https://docs.microsoft.com/azure/storage/storage-security-guide#client-side-encryption) také poskytuje funkci šifrování v klidovém stavu.

-   [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) umožňuje šifrování disků operačního systému a datové disky používané virtuálním počítačem IaaS.

#### <a name="azure-disk-encryption"></a>Azure Disk Encryption
[Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) pro virtuální počítače (VM) vám pomůže adresu zabezpečení organizace a požadavky na dodržování předpisů šifrováním vaše disky virtuálního počítače (včetně spouštěcí a datovými disky) s klíči a zásad řízení v [klíče služby Azure Trezor](https://azure.microsoft.com/services/key-vault/).

Šifrování disku řešení pro systém Windows je založeno na [Microsoft BitLocker Drive Encryption](https://technet.microsoft.com/library/cc732774.aspx), a Linux řešení je založena na [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt).

Řešení podporuje následující scénáře pro virtuální počítače IaaS, pokud jsou povolené v Microsoft Azure:
-   Integrace s Azure Key Vault

-   Úroveň Standard virtuálních počítačů: A, D, DS, G, GS a tak dále, virtuální počítače IaaS řady

-   Povolení šifrování v systému Windows a virtuálních počítačů IaaS Linux

-   Zakázáním šifrování na operačního systému a datové disky pro virtuální počítače IaaS Windows

-   Zakázáním šifrování na datových jednotkách pro virtuální počítače IaaS Linux

-   Povolení šifrování na virtuální počítače IaaS se systémem Windows klientského operačního systému

-   Povolení šifrování u svazků s připojení cesty

-   Povolení šifrování u virtuálních počítačů Linux, které jsou nakonfigurované s diskem prokládání (RAID) pomocí [mdadm](https://en.wikipedia.org/wiki/Mdadm)

-   Povolení šifrování na virtuální počítače s Linuxem pomocí [LVM (Správce logických svazku)](https://msdn.microsoft.com/library/windows/desktop/bb540532) pro datové disky

-   Povolení šifrování na virtuálních počítačích Windows, které jsou nakonfigurované pomocí prostorů úložiště

-   Jsou podporovány všechny veřejné oblasti Azure

Řešení nepodporuje následující scénáře, funkce a technologie ve verzi:

-   Úroveň Basic virtuálních počítačů IaaS

-   Zakázáním šifrování na jednotce operačního systému pro virtuální počítače IaaS Linux

-   Virtuální počítače IaaS, která jsou vytvořená pomocí klasického metody vytvoření virtuálního počítače

-   Integrace s vaší místní služby správy klíčů

-   Soubory Azure (systém souborů sdíleného), Network File System (NFS), dynamické svazky a virtuální počítače Windows, které jsou nakonfigurované s systémy na bázi softwaru diskového pole RAID

## <a name="sql-azure-database-isolation"></a>Izolace databáze Azure SQL
SQL Database je služba v cloudu Microsoftu poskytující relační databáze založené na předním databázovém stroji Microsoft SQL Server a schopné obsloužit i klíčové úlohy. SQL Database nabízí předvídatelný data izolaci na úrovni účtu, geography / oblast na základě a na základě v síti – všechny s téměř nulové správy.

### <a name="sql-azure-application-model"></a>Model aplikace Azure SQL

[Microsoft SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-get-started) databáze je služba relační databáze založené na cloudu založený na technologie SQL Server. Poskytuje vysoce dostupná, škálovatelná, víceklientské databáze služba hostovaná společností Microsoft v cloudu.

Z hlediska aplikací SQL Azure poskytuje následující hierarchie: Každá úroveň má jeden mnoho omezení úrovně níže.

![Model aplikace Azure SQL](./media/azure-isolation/azure-isolation-fig10.png)

Účet a předplatné jsou koncepty platformy Microsoft Azure pro přidružení fakturace a správy.

Logické servery a databáze jsou specifické pro službu SQL Azure koncepty a jsou spravované pomocí služby SQL Azure, zadaný OData a TSQL rozhraní nebo přes portál SQL Azure, která je integrována do portálu Azure.

Servery SQL Azure nejsou fyzický nebo virtuální počítač instancí, místo toho jsou kolekce databází, sdílení zásad správy a zabezpečení, které jsou uloženy v proto názvem "logické hlavní" databáze.

![SQL Azure](./media/azure-isolation/azure-isolation-fig11.png)

Logické hlavní databáze patří:

-   Přihlášeních SQL použitá pro připojení k serveru

-   Pravidla brány firewall

Fakturace a využití související informace pro SQL Azure, databáze ze stejného logického serveru nemusí být ve stejné fyzické instanci v clusteru SQL Azure, místo toho aplikace musíte zadat název databáze cílové při připojování.

Z hlediska zákazníka je vytvořit logický server v grafické geografické oblasti při vytvoření skutečné serveru se stane v jednom z clusterů v oblasti.

### <a name="isolation-through-network-topology"></a>Izolace prostřednictvím topologie sítě

Při vytvoření logického serveru a jeho název DNS je zaregistrován, název DNS body na adresu "brány virtuální adresa IP. proto volané v konkrétní datové centrum, kde je umístěn server.

Za VIP (virtuální IP adresy) máme kolekci služeb bezstavové brány. Obecně platí získat brány podílejí po koordinaci potřeby mezi více zdrojů dat (hlavní databázi, databázi uživatele atd.). Služby brány implementovat následující:
-   **Proxy připojení TDS.** To zahrnuje vyhledání uživatele databáze v clusteru back-end, implementace přihlašovací sekvence a pak předávání paketů TDS back-end a back.

-   **Správa databáze.** To zahrnuje implementace kolekci pracovních postupů provádět operace CREATE/ALTER/DROP database. Operace databáze může vyvolat analýzy rozšíření TDS paketů nebo explicitní rozhraní API OData.

-   Operace CREATE/ALTER/DROP přihlášení nebo uživatele

-   Operace správy logický server prostřednictvím rozhraní API OData

![Izolace prostřednictvím topologie sítě](./media/azure-isolation/azure-isolation-fig12.png)

Úroveň za brány se nazývá "back-end". Toto je, kde je všechna data uložená způsobem vysoce dostupný. Každá položka dat je uvedená patří "oddíl" nebo "převzetí služeb při selhání jednotka", každý z nich má aspoň tři repliky. Repliky jsou uloženy a replikují stroj SQL Server a spravuje převzetí služeb při selhání systému často označuje jako "prostředky infrastruktury".

Obecně platí systém back-end nekomunikuje odchozí do jiných systémů jako bezpečnostní opatření. Toto je vyhrazená pro systémy ve vrstvě front-endu (brány). Vrstva počítače brány mají omezenou oprávnění na počítačích back-end, aby se minimalizoval prostor pro útoky jako vhodný mechanismus obrany zabezpečení.

### <a name="isolation-by-machine-function-and-access"></a>Izolace tak, že počítač funkce a přístup
Azure SQL (se skládá z služby spuštěné na jiný počítač funkce. SQL Azure je rozdělené do cloudu databáze "back-end" a "front-end" (nebo správu brány) prostředích s obecné Princip pouze probíhající provoz do back-end a ne na. Front-endu prostředí může komunikovat se okolní uživatelé jiných služeb a obecně platí, má jenom omezené oprávnění v back-end (dostatek volat vstupní body, které je nutné vyvolat).

## <a name="networking-isolation"></a>Izolace sítě
Nasazení Azure má několik vrstev izolace sítě. Následující diagram znázorňuje různé úrovně izolace sítě, které Azure poskytuje zákazníkům. Tyto vrstvy jsou nativní v samotné platformě Azure a uživatelsky definované funkce. Příchozí z Internetu, Azure DDoS poskytuje izolaci proti rozsáhlé útoky na Azure. Další vrstva izolace je zákazník definovaný veřejné IP adresy (koncových bodů), které se používají k určení, které přenos dat přes cloudovou službu do virtuální sítě. Nativní Azure virtuální izolace sítě zajišťuje úplný izolaci od všech ostatních sítí, a že jenom přenosy dat prostřednictvím uživateli nakonfigurovanému cesty a metody. Tyto cesty a metody jsou další vrstva, kde skupiny Nsg, UDR a síťových virtuálního zařízení slouží k vytvoření hranice izolace k ochraně nasazení aplikací v síti chráněné.

![Izolace sítě](./media/azure-isolation/azure-isolation-fig13.png)

**Izolace provozu:** A [virtuální sítě](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) je hranice izolace přenosů na platformě Azure. Virtuální počítače (VM) v jednu virtuální síť nemůže komunikovat přímo do virtuálních počítačů v jinou virtuální síť, i v případě, že jsou obě virtuální sítě vytvořené pomocí tentýž zákazník. Izolace je kritické vlastnosti, které zajišťuje, aby virtuální počítače zákazníka a komunikace zůstane privátní virtuální sítě.

[Podsíť](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview#subnets) nabízí další úroveň izolace s ve virtuální síti na základě rozsahu IP. IP adresy ve virtuální síti, virtuální sítě lze rozdělit do několika podsítí pro organizaci a zabezpečení. Virtuální počítače a instance rolí PaaS nasazené do podsítí (stejných nebo různých) v rámci jedné virtuální sítě můžou navzájem komunikovat bez jakékoli další konfigurace. Můžete také nakonfigurovat [skupinu zabezpečení sítě (Nsg)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview#network-security-groups-nsg) chcete povolit nebo odepřít síťový provoz do instance virtuálních počítačů na základě pravidel, které jsou nakonfigurované v seznamu řízení přístupu (ACL) NSG. Skupiny NSG můžou být přidružené buď k podsítím, nebo k jednotlivým instancím virtuálních počítačů v této podsíti. Pokud je skupina zabezpečení sítě přidružená k podsíti, pravidla seznamu ACL platí pro všechny instance virtuálních počítačů v této podsíti.

## <a name="next-steps"></a>Další kroky

- [Možnosti izolace sítě pro počítače v systému Windows Azure virtuální sítě](https://azure.microsoft.com/blog/network-isolation-options-for-machines-in-windows-azure-virtual-networks/)

To zahrnuje classic front-end a back-end scénář, kde počítače v konkrétní back endovou síť nebo podsíť může povolit jenom určité klienty nebo jiným počítačům připojit k o konkrétní koncový bod na základě o povolených IP adres.

- [Výpočetní izolace](https://msenterprise.global.ssl.fastly.net/vnext/PDFs/A01_AzureSecurityWhitepaper20160415c.pdf)

Microsoft Azure poskytuje různé výpočetní služby založené na cloudu, které zahrnují široký výběr výpočetní instance a službám, které je možné škálovat nahoru či dolů automaticky podle potřeb vaší aplikace nebo enterprise.

- [Izolace úložiště](https://msenterprise.global.ssl.fastly.net/vnext/PDFs/A01_AzureSecurityWhitepaper20160415c.pdf)

Microsoft Azure odděluje výpočetní zákazníka na základě virtuálního počítače z úložiště. Toto rozdělení umožňuje výpočtů a úložiště nezávisle, škálovat usnadnit vám umožňuje víceklientská a izolace. Proto Azure Storage spuštěna na samostatných s žádné síťové připojení k Azure Compute kromě logicky. Všechny žádosti o spuštění pomocí protokolu HTTP nebo HTTPS na základě volby zákazníka.

