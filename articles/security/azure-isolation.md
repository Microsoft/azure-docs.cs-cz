---
title: Izolace ve veřejném cloudu Azure | Dokumentace Microsoftu
description: Přečtěte si o cloudové výpočetní služby, které zahrnují široké škály výpočetních instancí a služby, které je možné škálovat směrem nahoru a dolů automaticky podle potřeb vaší aplikace nebo enterprise.
services: security
documentationcenter: na
author: UnifyCloud
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: f5d1c66cb049ab9ec52db619d55a4bb3e485e4b2
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56109839"
---
# <a name="isolation-in-the-azure-public-cloud"></a>Izolace ve veřejném cloudu Azure
##  <a name="introduction"></a>Úvod
### <a name="overview"></a>Přehled
Pomáhat aktuální a budoucí Azure zákazníky, pochopit a využívat různé související se zabezpečením možnosti dostupné v, a obaluje platformy Azure, společnost Microsoft vyvinula řadu dokumenty White Paper, přehledy o zabezpečení, osvědčené postupy a Kontrolní seznamy.
Témata z hlediska rozebírají v rozsahu a jsou pravidelně aktualizovány. Tento dokument je části této série dle souhrnu v následující části abstraktní.

### <a name="azure-platform"></a>Azure Platform
Azure je otevřená a flexibilní Cloudová platforma služby, který podporuje nejširší škálu operačních systémů, programovacích jazyků, architektur, nástrojů, databází a zařízení. Můžete například provést následující věci:
- Používejte kontejnery Linuxu s integrací Dockeru;
- Vytvářejte aplikace pomocí jazyka JavaScript, Python, .NET, PHP, Java a Node.js, a
- Tvořte back EndY pro iOS, Android a Windows zařízení.

Microsoft Azure podporuje technologie miliony vývojářů a IT profesionály už spoléhají a kterým důvěřují.

Při sestavení nebo migraci prostředků IT k poskytovateli služby veřejného cloudu, jste závislí na schopnosti této organizace k ochraně vašich aplikací a dat s ovládacími prvky poskytují ke správě zabezpečení vašich cloudových prostředků a služeb.

Infrastruktura Azure je od zařízení až po aplikace navržena tak, aby umožňovala hostování milionů zákazníků současně a poskytovala důvěryhodný základ pro splnění potřeb zabezpečení jednotlivých podniků. Azure navíc poskytuje širokou škálu konfigurovatelných možností zabezpečení a umožňuje jejich nastavování, takže můžete zabezpečení přizpůsobit jedinečným požadavkům svého nasazení. Tento dokument vám pomůže splnit tyto požadavky.

### <a name="abstract"></a>Abstraktní

Microsoft Azure umožňuje spouštět aplikace a virtuální počítače (VM) na sdílené fyzické infrastruktuře. Jeden z primárního ekonomické motivace pro spouštění aplikací v cloudovém prostředí je schopný distribuovat náklady na prostředky sdílené mezi více zákazníků. Tento postup víceklientská zvyšuje efektivitu prostředky multiplexní mezi různými zákazníky s nízkými náklady. Bohužel také přináší riziko sdílení fyzických serverů a dalších prostředků infrastruktury pro spouštění citlivých aplikací a virtuálních počítačů, které může patřit do libovolného a potenciálně škodlivých uživatele.

Tento článek popisuje, jak Microsoft Azure zajišťuje izolaci uživatelů se zlými úmysly a uživatele bez zlých a slouží jako vodítko při navrhování řešení v cloudu tím, že nabízí různé možnosti izolace architekty. Tento dokument white paper se zaměřuje na technologie platformy Azure a kontrolní mechanismy zabezpečení určených pro zákazníky a nebude pokoušet o adresu smlouvy o úrovni služeb, cenové modely a aspekty postupů DevOps.

## <a name="tenant-level-isolation"></a>Izolace na úrovni tenanta
Jedna z hlavních výhod cloud computingu je koncept sdílené, běžné infrastruktury řady zákazníků současně, což vede k cenové výhody. Tento koncept se nazývá více tenantů. Microsoft spolupracuje průběžně k zajištění, že architektura více tenantů Microsoft Azure Cloud podporuje zabezpečení, důvěrnosti, ochrany osobních údajů, integritu a dostupnost standardy.

V prostředí cloudu se dá klient služby Azure AD definovat jako klient nebo organizace, která vlastní a spravuje konkrétní instanci dané cloudové služby. S platformou identity Microsoft Azure poskytuje klient je jednoduše vyhrazenou instancí služby Azure Active Directory (Azure AD), který vaše organizace obdrží a vlastní, když se přihlásí ke cloudové službě Microsoftu.

Každý adresář služby Azure AD je oddělený od ostatních adresářů služby Azure AD. Adresář služby Azure AD byl navržen tak, aby se jednalo o zabezpečený prostředek k použití pouze pro vaši organizaci – stejně jako je podniková kancelářská budova zabezpečeným prostředkem výhradně vaší organizace. Architektura služby Azure AD znemožňuje míchání dat zákazníků a informací o identitě. To znamená, že se uživatelé a správci jednoho adresáře služby Azure AD nemohou dostat – ať už omylem nebo záměrně – k datům v jiném adresáři.

### <a name="azure-tenancy"></a>Azure Tenancy
Tenantů Azure (předplatné Azure) odkazuje na vztah "zákazník/billing" a jedinečný [tenanta](https://docs.microsoft.com/azure/active-directory/develop/active-directory-howto-tenant) v [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis). Izolace na úrovni tenanta v Microsoft Azure můžete dosáhnout použitím služby Azure Active Directory a [ovládacích prvků na základě rolí](https://docs.microsoft.com/azure/role-based-access-control/overview) nabízené ho. Každé předplatné Azure je přidružený jeden adresář Azure Active Directory (AD).

Uživatelům, skupinám a aplikacím z tohoto adresáře můžou spravovat prostředky v předplatném Azure. Můžete přiřadit tato přístupová práva pomocí webu Azure portal, nástrojů příkazového řádku Azure a rozhraní API pro správu Azure. Klient služby Azure AD je logicky izolovaný s použitím hranic zabezpečení tak, aby žádný zákazník získat přístup nebo ohrozit společné tenantů speciálně nebo neúmyslně. Azure AD je spuštěna na "holé počítače" serverech izolované v segmentu oddělené sítě, kde filtrování paketů na úrovni hostitele a brány Windows Firewall blokuje nežádoucí připojení a provoz.

- Přístup k datům ve službě Azure AD vyžaduje ověřování uživatelů pomocí služby tokenů zabezpečení (STS). Informace o existenci, povoleného stavu a role uživatele se používá ověřování systému k určení, zda požadovaný přístup k cílovému tenantovi je autorizovaný pro tento uživatel v této relaci.

![Azure Tenancy](./media/azure-isolation/azure-isolation-fig1.png)


- Tenanti jsou samostatné kontejnery a není žádný vztah mezi těmito.

- Žádný přístup mezi klienty, pokud správce tenanta mu udělí prostřednictvím federace nebo zřizování uživatelských účtů z jiných tenantů.

- Fyzický přístup k serverům, které tvoří službu Azure AD a přímý přístup k systémům back endové služby Azure AD, je omezen.

- Azure AD, uživatelé nemají přístup k fyzické prostředky nebo umístění, a proto není možné vynechat logické RBAC zásad kontroly uvádí následující.

Pro diagnostiku a potřebu údržby provozní model, který využívá systém elevací oprávnění just-in-time je povinné a použít. Azure AD Privileged Identity Management (PIM) zavádí pojem oprávněného správce. [Oprávnění správci](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) by měl být uživatele, kteří potřebují privilegovaný přístup, a poté, ale ne každý den. Dokud uživatel nepotřebuje přístup, je tato role neaktivní. Jakmile uživatel bude přístup potřebovat, dokončí proces aktivace a na předem určenou dobu se stane aktivním správcem.

![Azure AD Privileged Identity Management](./media/azure-isolation/azure-isolation-fig2.png)

Azure Active Directory je hostitelem každého tenanta do vlastního kontejneru chráněné, zásady a oprávnění a v rámci kontejneru výhradně vlastněna a řízena tenanta.

Koncept kontejnery tenanta je podmínkou pro výrazně ingrained v adresářové službě na všech úrovních, od portály úplně do trvalého úložiště.

I v případě, že metadata z více tenantů Azure Active Directory je uložen na stejném fyzickém disku, není žádný vztah mezi kontejnery než definovaná v adresářové službě, která zase závisí správce tenanta.

### <a name="azure-role-based-access-control-rbac"></a>Řízení přístupu Azure na základě rolí (RBAC)
[Azure na základě rolí řízení přístupu (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) vám umožní sdílet různé součásti, které jsou k dispozici v rámci předplatného Azure tím, že poskytuje propracovanou správu přístupu pro Azure. Azure RBAC umožňuje oddělit úlohy v rámci vaší organizace a udělit přístup podle uživatelé potřebují k provádění svých úloh. Ne všichni poskytuje neomezená oprávnění v předplatném Azure nebo prostředky, můžete povolit pouze určité akce.

Azure RBAC má tři základní role, které platí pro všechny typy prostředků:

- **Vlastník** má úplný přístup ke všem prostředkům, včetně práva na delegovat přístup ostatním uživatelům.

- **Přispěvatel** můžete vytvářet a spravovat všechny typy prostředků Azure, ale nemůže udělovat přístup ostatním uživatelům.

- **Čtečka** můžete zobrazit existující prostředky Azure.

![Řízení přístupu na základě rolí Azure](./media/azure-isolation/azure-isolation-fig3.png)

Ostatní role RBAC v Azure umožňují správu konkrétních prostředků Azure. Třeba role Přispěvatel virtuálních počítačů umožňuje uživatelům vytvářet a spravovat virtuální počítače. Ho nezískává přístup na Azure Virtual Network nebo podsíť, která se připojí k virtuálnímu počítači.

[Předdefinované role RBAC](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) seznamu rolí v Azure k dispozici. Určuje konzole operations Console a obor, který každý předdefinovaná role uděluje uživatelům. Pokud potřebujete definovat vlastní role pro ještě větší kontrolu, uvidíte, jak sestavit [vlastní role Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/custom-roles).

Některé další možnosti pro Azure Active Directory patří:
- Azure AD umožňuje jednotné přihlašování k aplikacím SaaS, bez ohledu na to, kde jsou hostované. Některé aplikace jsou federované pomocí Azure AD, jiné používají jednotné přihlašování pomocí hesla. Federované aplikace můžou také podporovat zřizování uživatelů a [ukládání hesel do trezoru](https://www.techopedia.com/definition/31415/password-vault).

- Přístup k datům v rámci [Azure Storage](https://azure.microsoft.com/services/storage/) je řízen prostřednictvím ověřování. Každý účet storage má primární klíč ([klíč účtu úložiště](https://docs.microsoft.com/azure/storage/storage-create-storage-account), zkráceně SAK) a sekundární tajný klíč (sdílený přístupový podpis, nebo SAS).

- Azure AD poskytuje identitu jako službu prostřednictvím federace s využitím [Active Directory Federation Services](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-azure-adfs), synchronizace a replikace v místních adresářích.

- [Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication) je služba služby Multi-Factor authentication, která vyžaduje, aby uživatelé ověření přihlášení pomocí mobilní aplikace, telefonního hovoru nebo textové zprávy. Můžete použít s Azure AD ke zabezpečené místní prostředky pomocí ověřování Azure Multi-Factor Authentication server a také u vlastních aplikací a adresáři používajícími sadu SDK.

- [Azure AD Domain Services](https://azure.microsoft.com/services/active-directory-ds/) umožňuje připojit virtuální počítače Azure k doméně služby Active Directory bez nutnosti nasazovat řadiče domény. Můžete přihlásit k těmto virtuálním počítačům pomocí svých firemních přihlašovacích údajů služby Active Directory a spravovat virtuální počítače připojené k doméně pomocí zásad skupiny k vynucovat pravidla pro všechny virtuální počítače Azure.

- [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) poskytuje službu správy s vysokou dostupností globálních identit pro zákaznické aplikace s možností škálování na stovky milionů identit. Dá se integrovat do mobilních i webových platforem. Zákazníky můžete přihlásit ke všem vašim aplikacím přes přizpůsobitelné prostředí pomocí svých existujících účtů v sociálních sítích nebo vytváření přihlašovacích údajů.

### <a name="isolation-from-microsoft-administrators--data-deletion"></a>Izolaci od správců Microsoft a odstranění dat
Společnost Microsoft má silné opatření k ochraně vašich dat před neoprávněném přístupu nebo použití neoprávněné osoby. Tyto provozní procesy a kontroly se zálohují [podmínky Online služeb](https://aka.ms/Online-Services-Terms), které nabízejí smluvní závazky, které řídí přístup k vašim datům.

-   Technici Microsoftu nemají výchozí přístup k datům v cloudu. Místo toho, získají přístup, v části Přehled správy, pouze v případě potřeby. Tento přístup je pečlivě řídí a protokolují a odvolán, pokud už je nepotřebujete.

-   Microsoft může pověřit další organizace k poskytování omezených služeb svým jménem. Subdodavatelé může přístup k zákaznickým datům jenom k poskytování služeb pro který jsme je pověřili k poskytování a mají zakázáno používat za žádným jiným účelem. Kromě toho jsou smluvně vázány, aby zachovávaly důvěrnost informací našich zákazníků.

Obchodní služby s využitím certifikace toto auditování se provádí, jako jsou ISO/IEC 27001 jsou pravidelně ověření od Microsoftu a akreditovanými auditorskými, které provádějí vzorkové audity, ověřit tento přístup pouze pro účely hlediska podniku oprávněný požadavek. Kdykoli a z jakéhokoli důvodu můžete vždycky přistupovat k datům zákazníků.

Pokud odstraníte všechna data, Microsoft Azure odstraní data, včetně žádné uložené v mezipaměti nebo záložní kopie. Pro příslušné služby, které do 90 dnů po uplynutí doby uchování dojde k odstranění. (V oboru služby jsou definovány v části podmínky pro zpracování dat naše [podmínky Online služeb](https://aka.ms/Online-Services-Terms).)

Pokud na disku používané pro úložiště odkážete selhání hardwaru, se zabezpečeně [vymaže nebo zničení](https://microsoft.com/trustcenter/privacy/you-own-your-data) dříve, než je Microsoft vrátí výrobce pro nahrazení nebo opravit. Data na jednotce se přepíší zajistit, že data nelze obnovit všechny prostředky.

## <a name="compute-isolation"></a>COMPUTE úrovně izolace
Microsoft Azure nabízí různé cloudové výpočetní služby, které zahrnují široké škály výpočetních instancí a služby, které je možné škálovat směrem nahoru a dolů automaticky podle potřeb vaší aplikace nebo enterprise. Tyto výpočetní instance a service nabízejí izolaci na více úrovních, zabezpečení dat, aniž byste museli obětovat flexibilitu při konfiguraci této poptávky zákazníků.

### <a name="isolated-virtual-machine-sizes"></a>Velikosti virtuálních počítačů izolované
Azure Compute nabízí velikosti virtuálních počítačů, které jsou izolované na konkrétní typ hardwaru a vyhrazené pro jednoho zákazníka.  Tyto velikosti virtuálních počítačů jsou nejvhodnější pro úlohy, které vyžadují vysoký stupeň izolace od ostatních zákazníků a kterých se týkají například požadavky na dodržování předpisů a zákonné požadavky.  Zákazníci můžou dál rozdělit prostředky těchto virtuálních počítačů izolované pomocí vybrat [podpora Azure pro vnořených virtuálních počítačích](https://azure.microsoft.com/blog/nested-virtualization-in-azure/).

Použití izolovaného velikost zaručuje, že váš virtuální počítač bude pouze jeden běží na tuto instanci konkrétní server.  Aktuální nabídky izolované virtuálního počítače zahrnují:
* Standard_E64is_v3
* Standard_E64i_v3
* Standard_M128ms
* Standard_GS5
* Standard_G5
* Standard_DS15_v2
* Standard_D15_v2

Další informace o jednotlivých izolované velikost dostupné [tady](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory).

### <a name="hyper-v--root-os-isolation-between-root-vm--guest-vms"></a>Technologie Hyper-V a kořenové OS izolaci mezi kořenové virtuálních počítačů a virtuálních počítačů hosta
Výpočetní Platforma Azure je založena na počítači virtualizace – to znamená, že veškerý kód zákazníků provede ve virtuálním počítači Hyper-V. Na každém uzlu Azure (nebo koncový bod sítě) je hypervisoru, který běží přímo nad hardwarem a uzel rozdělí číslo proměnné z hostované virtuální počítače (VM).


![Technologie Hyper-V a kořenové OS izolaci mezi kořenové virtuálních počítačů a virtuálních počítačů hosta](./media/azure-isolation/azure-isolation-fig4.jpg)


Každý uzel má také jeden speciální kořenový virtuální počítač, který spouští hostitelským operačním systémem. Je důležité hranice izolace kořenový virtuální počítač od virtuálních počítačů hosta a virtuální počítače hosta od sebe, spravuje hypervisoru a kořenové operačního systému. Párování hypervisoru/root operační systém využívá Microsoftu desítky let zkušeností zabezpečení operačního systému a novější learning od společnosti Microsoft Hyper-V, k poskytování silnou izolaci virtuálních počítačů hosta.

Platforma Azure používá virtualizované prostředí. Uživatelské instance se chovají jako samostatné virtuální počítače, které nemají přístup k fyzickému hostitelskému serveru.

Azure hypervisor chová jako mikrojádro a předává všechny požadavky na hardware přístup z hostované virtuální počítače ke zpracování hostiteli prostřednictvím rozhraní sdílené paměti, nazývá sběrnice VMBus. Tento postup brání uživatelům v získání přímého přístupu k systému pro čtení, zápis a spouštění a snižuje riziko sdílení systémových prostředků.

### <a name="advanced-vm-placement-algorithm--protection-from-side-channel-attacks"></a>Pokročilé algoritmus umístění virtuálního počítače a ochrana před útoky na straně kanálu
Všechny mezi VM útoku zahrnuje dva kroky: uvedení Virtuálním počítači spravovanými nežádoucí osoba na stejném hostiteli jako jeden z victim virtuální počítače a pak porušení oddělovací hranice ukrást citlivé victim informace nebo mít vliv na jeho výkon pro greed nebo vandalismu. Microsoft Azure poskytuje ochranu na oba kroky pomocí pokročilé algoritmus umístění virtuálního počítače a ochranu před útoky všechny známé na straně kanálu včetně hlučného souseda virtuálních počítačů.

### <a name="the-azure-fabric-controller"></a>Kontroler prostředků infrastruktury Azure
Kontroler prostředků infrastruktury Azure zodpovídá za přidělování prostředků infrastruktury pro úlohy klientů a spravuje jednosměrnou komunikaci z hostitele do virtuálních počítačů. Algoritmus umístění virtuálního počítače kontroler prostředků infrastruktury Azure je vysoce sofistikovanými a předvídání jako fyzické úrovni hostitele bylo téměř nemožné.

![Kontroler prostředků infrastruktury Azure](./media/azure-isolation/azure-isolation-fig5.png)

Azure hypervisor vynucuje oddělení paměti a zpracování mezi virtuálními počítači a bezpečně směruje síťový provoz do hostovaného operačního systému klientů. Tím se eliminuje možnost a na straně kanálu útoku na úrovni virtuálního počítače.

V Azure, je speciální kořenu virtuálního počítače: běží posílené operačním systémem názvem kořenového operačního systému, který je hostitelem agenta do prostředků infrastruktury (IM). FAs se pak používají ke správě agentů hosta (GA) v rámci hostovaných operačních systémů na virtuální počítače zákazníků. Služba fAs také spravovat uzly úložiště.

OS/FA kořenové kolekce Azure hypervisor, a zákazníka virtuální počítače/plynu se skládá z výpočetního uzlu. Služba fAs spravuje kontroler prostředků infrastruktury (FC), které existuje mimo úložnou a výpočetní uzly (výpočetní a úložné clustery spravují samostatné FCs). Pokud zákazník aktualizuje jejich aplikace a konfigurační soubor je spuštěný, FC komunikuje s DM, poté kontaktuje plynu, která upozorní aplikaci změny konfigurace. V případě selhání hardwaru bude FC automaticky vyhledat dostupného hardwaru a restartujte virtuální počítač existuje.

![Kontroler prostředků infrastruktury Azure](./media/azure-isolation/azure-isolation-fig6.jpg)

Komunikace z kontroler prostředků infrastruktury pro agenta je jednosměrná. Agent implementuje služby chráněný SSL, která pouze odpovídá na požadavky z kontroleru. Nebude možné inicializovat připojení ke kontroleru nebo jiných privilegovaných interní uzly. FC zpracuje všechny odpovědi, jako kdyby nedůvěryhodné.


![Kontroler prostředků infrastruktury](./media/azure-isolation/azure-isolation-fig7.png)

Izolace sahá od kořenového virtuálního počítače z virtuálních počítačů hosta a virtuální počítače hosta od sebe. Výpočetní uzly jsou izolované od uzly úložiště v zájmu lepší ochrany.


Hypervisor hostitelský operační systém zadejte síťového paketu – filtry, které pomáhají zajistit, že nedůvěryhodné virtuálních počítačů nemůžete generování podvodného provozu nebo přijímat přenosy není adresován jim, směrování provozu do koncových bodů chráněné infrastruktury a posílání a přijímání nevhodný přenosy všesměrového vysílání.


### <a name="additional-rules-configured-by-fabric-controller-agent-to-isolate-vm"></a>Další pravidla nakonfigurovaná agentem Kontroleru prostředků infrastruktury k izolaci virtuálních počítačů
Ve výchozím nastavení když je vytvořen virtuální počítač a potom agent kontroleru prostředků infrastruktury Konfiguruje filtr paketů pro přidání pravidel a výjimek byl povolen autorizovaný provoz blokován veškerý provoz.

Existují dvě kategorie pravidel, které jsou naprogramovány:

-   **Pravidla konfigurace nebo infrastrukturu počítače:** Ve výchozím nastavení je veškerá komunikace blokovaná. Existují výjimky, které umožňují virtuálnímu počítači odesílat a přijímat komunikaci DHCP a DNS. Virtuální počítače můžete také odesílat provoz do "veřejného" Internetu a odesílat provoz do jiné virtuální počítače v rámci stejné virtuální síti Azure a aktivace operačního systému serveru. Seznam povolených cílů odchozí komunikace virtuálních počítačů neobsahuje podsítě směrovačů Azure, správu Azure a dalších vlastností Microsoft.

-   **Konfigurační soubor rolí:** Definuje příchozí seznamů řízení přístupu (ACL) na základě vašeho tenanta služby modelu.

### <a name="vlan-isolation"></a>Izolace sítě VLAN
Existují tři virtuální místní sítě v každém clusteru:

![Izolace sítě VLAN](./media/azure-isolation/azure-isolation-fig8.jpg)


-   Hlavní VLAN – propojení uzlů nedůvěryhodné zákazníka

-   Síť VLAN FC – obsahuje důvěryhodné FCs a podpůrných systémů

-   Zařízení sítě VLAN – obsahuje důvěryhodné sítě a jiných zařízeních infrastruktury

Komunikace je povolené ze sítě VLAN FC hlavní sítě VLAN, ale nelze inicializovat z hlavní sítě VLAN, sítě VLAN FC. Komunikace se taky zablokuje od hlavní sítě VLAN, zařízení sítě VLAN. To zaručuje, že i v případě, že dojde k narušení uzlu se systémem zákaznického kódu nelze útoku uzly na FC nebo zařízení sítě VLAN.

## <a name="storage-isolation"></a>Izolace úložiště
### <a name="logical-isolation-between-compute-and-storage"></a>Logickou izolaci mezi výpočetní výkon a úložiště
Microsoft Azure jako součást svých základních návrhu, odděluje výpočetní založená na virtuálních počítačích ze služby storage. Toto oddělení umožňuje výpočetní výkon a úložiště nezávisle na sobě škálovat usnadňují poskytují více tenantů a izolaci.

Proto úložiště Azure běží na samostatné hardwaru bez připojení k síti pro Azure Compute s výjimkou logicky. [To](https://msenterprise.global.ssl.fastly.net/vnext/PDFs/A01_AzureSecurityWhitepaper20160415c.pdf) znamená, že když se vytvoří virtuální disk, není přiděleno místo na disku pro jeho celková kapacita. Místo toho je tabulka vytvořená, která se mapuje na oblasti na fyzickém disku adresy na virtuálním disku a je původně prázdné tabulky. **Při prvním zákazník zapisuje data na virtuálním disku není přiděleno místo na fyzickém disku a ukazatel na ni je umístěn v tabulce.**
### <a name="isolation-using-storage-access-control"></a>Řízení přístupu úložiště pomocí izolace
**Řízení přístupu v Azure Storage** má model řízení snadný přístup. Každé předplatné Azure můžete vytvořit jeden nebo více účtů úložiště. Každý účet úložiště obsahuje jediný tajný klíč, který se používá k řízení přístupu ke všem datům v účtu úložiště.

![Řízení přístupu úložiště pomocí izolace](./media/azure-isolation/azure-isolation-fig9.png)

**Přístup k datům služby Azure Storage (včetně tabulek)** se dá řídit přes [SAS (sdíleným přístupovým podpisům)](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1) token, který uděluje oboru přístupu. SAS se vytvořeny šablonou dotazu (URL) podepsané [SAK (klíč účtu úložiště)](https://msdn.microsoft.com/library/azure/ee460785.aspx). Který [podepsané URL](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1) může dostat k jinému procesu (která je přidělena), který lze potom vyplňte podrobnosti dotazu a být odeslán požadavek na službu storage. SAS můžete udělit přístup podle času klientům, aniž by odhalil tajný klíč účtu úložiště.

SAS znamená, že jsme udělit že klienta omezená oprávnění k objektům v náš účet úložiště na dobu určitou dobu a s konkrétní sadou oprávnění. Aniž byste museli sdílet přístupové klíče vašeho účtu jsme můžete udělit takto omezenými oprávněními.

### <a name="ip-level-storage-isolation"></a>Úložiště na úrovni izolace IP
Můžete určit brány firewall a definovat rozsah IP adres pro klienty důvěryhodné. S rozsahem IP adres pouze klienti, kteří mají IP adresu v definovaném rozsahu můžete připojit k [služby Azure Storage](https://docs.microsoft.com/azure/storage/storage-security-guide).

Data úložiště IP se dají chránit před neoprávněnými uživateli prostřednictvím sítě mechanismus, který se používá k přidělení vyhrazené nebo vyhrazené tunel provozu do úložiště IP.

### <a name="encryption"></a>Šifrování
Azure nabízí následující typy šifrování pro ochranu dat:
-   Šifrování během přenosu

-   Šifrování v klidovém stavu

#### <a name="encryption-in-transit"></a>Šifrování během přenosu
Šifrování během přenosu sítí je mechanismus ochrany dat při přenosu napříč sítěmi. S Azure Storage můžete svázat data s využitím:

-   [Šifrování na úrovni přenosu](https://docs.microsoft.com/azure/storage/storage-security-guide#encryption-in-transit), jako je například HTTPS při přenosu dat do nebo z úložiště Azure.

-   [Propojí šifrování](../storage/common/storage-security-guide.md#using-encryption-during-transit-with-azure-file-shares), jako například šifrování protokolu SMB 3.0 pro sdílené složky Azure.

-   [Šifrování na straně klienta](https://docs.microsoft.com/azure/storage/storage-security-guide#using-client-side-encryption-to-secure-data-that-you-send-to-storage)pro šifrování dat před přenosu do služby storage a k dešifrování dat po převedení mimo úložiště.

#### <a name="encryption-at-rest"></a>Šifrování v klidovém stavu
Pro mnoho společností [šifrování dat v klidovém stavu](https://docs.microsoft.com/azure/security/azure-isolation) je povinný krok směrem k suverenita dat o ochraně osobních údajů, dodržování předpisů a data. Existují tři funkce Azure, které poskytují šifrování dat, která jsou "neaktivní":

-   [Šifrování služby Storage](https://docs.microsoft.com/azure/storage/storage-security-guide#encryption-at-rest) umožňuje požadovat, že služba úložiště automaticky šifrování dat při zápisu do služby Azure Storage.

-   [Šifrování na straně klienta](https://docs.microsoft.com/azure/storage/storage-security-guide#client-side-encryption) také poskytuje funkci šifrování v klidovém stavu.

-   [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) umožňuje šifrovat disky s operačním systémem a datové disky používané virtuálním počítači s IaaS.

#### <a name="azure-disk-encryption"></a>Azure Disk Encryption
[Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) pro virtuální počítače (VM) vám pomůže splnit organizační zabezpečení a požadavky na dodržování předpisů tím, že šifruje disky virtuálních počítačů (včetně spouštěcích a datových disků) pomocí klíčů a zásad řízení v [klíče služby Azure Trezor](https://azure.microsoft.com/services/key-vault/).

Řešení Disk Encryption pro Windows je založené na [Microsoft BitLocker Drive Encryption](https://technet.microsoft.com/library/cc732774.aspx), a řešení pro Linux je založen na [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt).

Řešení podporuje následující scénáře pro virtuální počítače IaaS, pokud je povolen v Microsoft Azure:
-   Integrace se službou Azure Key Vault

-   Úroveň Standard virtuálních počítačů: A, D, DS, G, GS a tak dále, řadu virtuálních počítačů IaaS

-   Povolení šifrování na Windows a virtuálních počítačů IaaS s Linuxem

-   Zakázáním šifrování u operačního systému a datové disky pro virtuální počítače IaaS s Windows

-   Zakázáním šifrování na datových jednotkách virtuálních počítačů IaaS s Linuxem

-   Povolení šifrování na virtuální počítače IaaS, spuštěný klientský operační systém Windows

-   Povolení šifrování na svazcích s cestami k připojení

-   Povolení šifrování na virtuální počítače s Linuxem, které jsou nakonfigurované s diskem prokládání (RAID) s použitím [mdadm](https://en.wikipedia.org/wiki/Mdadm)

-   Povolení šifrování na virtuální počítače s Linuxem pomocí [LVM (Správce logických svazků)](https://msdn.microsoft.com/library/windows/desktop/bb540532) datových disků

-   Povolení šifrování na virtuálních počítačích s Windows, které jsou nakonfigurované pomocí prostorů úložiště

-   Jsou podporovány všech veřejných oblastech Azure

Toto řešení nepodporuje následující scénáře, funkce a technologie ve vydané verzi:

-   Úroveň Basic virtuálních počítačů IaaS

-   Zakázáním šifrování na disku s operačním systémem pro virtuální počítače IaaS s Linuxem

-   Virtuální počítače IaaS, které jsou vytvořeny pomocí klasické metody vytvoření virtuálního počítače

-   Integrace s vaší místní služba správy klíčů

-   Služba soubory Azure (sdílený systém souborů), Network File System (NFS), dynamických svazků a virtuálních počítačů s Windows, které jsou nakonfigurované pro systémy založené na softwaru diskového pole RAID

## <a name="sql-azure-database-isolation"></a>Izolace databáze SQL Azure
SQL Database je služba v cloudu Microsoftu poskytující relační databáze založené na předním databázovém stroji Microsoft SQL Server a schopné obsloužit i klíčové úlohy. SQL Database nabízí předvídatelný data izolace na úrovni účtu, zeměpisné oblasti nebo oblasti a v síti – vše s téměř bez nutnosti jakékoli správy.

### <a name="sql-azure-application-model"></a>Aplikační Model SQL Azure

[Microsoft SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-get-started) Database je služba relační databáze založené na cloudu založená na technologiích systému SQL Server. Poskytuje vysoce dostupné, škálovatelné a víceklientská databáze služba hostovaná společností Microsoft v cloudu.

Perspektiva SQL Azure z aplikace poskytuje následující hierarchie: Každá úroveň má jeden mnoho omezení úrovní níže.

![Aplikační Model SQL Azure](./media/azure-isolation/azure-isolation-fig10.png)

Účet a předplatné se koncepty platformy Microsoft Azure pro přidružení fakturace a správy.

A jejich logické servery jsou specifické pro službu SQL Azure a spravují s použitím SQL Azure, pokud rozhraní OData a TSQL nebo přes portál SQL Azure, který integrovaná v portálu Azure portal.

Servery SQL Azure nejsou fyzické nebo instancí virtuálních počítačů, místo toho jsou kolekce databází, sdílení zásad správy a zabezpečení, které jsou uloženy v proto volaná "logické hlavní" databáze.

![SQL Azure](./media/azure-isolation/azure-isolation-fig11.png)

Logické hlavní databáze patří:

-   Pro připojení k serveru přihlášeních SQL

-   Pravidla brány firewall

Při připojování, fakturaci a využití související informace pro SQL Azure, že databáze ze stejného logického serveru nemusí být ve stejné fyzické instanci v clusteru SQL Azure, místo toho aplikace musíte zadat název cílové databáze.

Z hlediska zákazníků vytvoření logického serveru v grafické geografické oblasti, zatímco skutečná vytvoření serveru se stane v jednom z clusterů v oblasti.

### <a name="isolation-through-network-topology"></a>Izolace prostřednictvím topologie sítě

Při vytvoření logického serveru a jeho název DNS je zaregistrovaný, název DNS odkazuje na adresu "Brána virtuální IP adresy" proto volaná v konkrétním datovém centru umístění serveru.

Za virtuální IP adresy (virtuální IP adresa) máme kolekce služeb bezstavové brány. Obecně platí získejte brány podílejí po koordinaci potřeby mezi více zdroji dat (hlavní databázi, uživatelské databázi atd.). Služba brány implementovat následující:
-   **Proxy připojení TDS.** To zahrnuje vyhledání uživatele databáze v back-endu clusteru, implementace přihlašovací sekvence a následně předávání paketů TDS back-endu a back.

-   **Správa databáze.** To zahrnuje implementace kolekce pracovních postupů provádět operace CREATE/ALTER/DROP database. Databázové operace lze vyvolat pomocí analýzy rozšíření TDS paketů nebo explicitní rozhraní API pro OData.

-   Operace CREATE/ALTER/DROP login/uživatele

-   Operace správy logický server prostřednictvím rozhraní API OData

![Izolace prostřednictvím topologie sítě](./media/azure-isolation/azure-isolation-fig12.png)

Úroveň za brány se nazývá "back-end". Je to, kde jsou všechna data uložená způsobem s vysokou dostupností. Každá část dat se říká, že patří do "oddílu" nebo "převzetí služeb při selhání jednotky", každý z nich s alespoň tři repliky. Repliky jsou uloženy a replikované modulem SQL serveru a spravovat převzetí služeb při selhání systému často označuje jako "prostředky infrastruktury".

Obecně platí back endovému systému není odchozí komunikaci s jinými systémy důvodu bezpečnostního opatření. To je vyhrazený pro systémy vrstvy front-endu (brány). Vrstva počítače brány máte omezená oprávnění na back-end počítačích minimalizovat jako vhodný mechanismus v obrany pro možný útok.

### <a name="isolation-by-machine-function-and-access"></a>Izolace podle funkce Machine a přístup
SQL Azure (se skládá ze služeb spuštěných v jiném počítači funkce. SQL Azure je rozdělen na Cloudová databáze "back-end" a "front-end" (brány a správy) prostředích s zásadně budete pouze provoz do back-end a ne out. Front-endového prostředí můžete komunikaci s vnějším světem dalších služeb a obecně platí, má jenom omezené oprávnění v back endu (aby bylo možné volat vstupních bodů, které potřebuje k vyvolání).

## <a name="networking-isolation"></a>Izolace sítě
Nasazení Azure má několik úrovní izolace sítě. Následující diagram znázorňuje různé úrovně izolace sítě, které Azure poskytuje zákazníkům. Tyto vrstvy jsou nativní v samotné platformy Azure a uživatelsky definované funkce. Příchozí z Internetu, Azure DDoS zajišťuje izolaci proti ve velkém měřítku útoky na Azure. Další vrstva izolace je uživatelem definovaný veřejné IP adresy (koncových bodů), které se používají k určení, jaký provoz můžete předat prostřednictvím cloudové služby k virtuální síti. Nativní Azure virtual izolace sítě zajistí, že naprosté izolaci od všech ostatních sítí a že provoz pouze prochází nakonfigurovaná uživatelem cesty a metody. Tyto cesty a metody jsou další vrstvy, kde skupin zabezpečení sítě, směrování definovaného uživatelem a síťových virtuálních zařízení slouží k vytváření hranic izolace k ochraně nasazení aplikací do chráněné síti.

![Izolace sítě](./media/azure-isolation/azure-isolation-fig13.png)

**Izolace provozu:** A [virtuální sítě](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) je hranicí izolace provozu na platformě Azure. Virtuální počítače (VM) v jedné virtuální síti nemůže komunikovat přímo do virtuálních počítačů v jiné virtuální síti, i když obě virtuální sítě jsou vytvořeny tentýž zákazník. Izolace je důležité vlastnost, která zajišťuje virtuální počítače zákazníků a komunikace zůstávají privátní virtuální sítě.

[Podsíť](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) nabízí další úroveň izolace ve virtuální síti na základě rozsahu IP. IP adresy ve virtuální síti, virtuální síť můžete rozdělit do několika podsítí organizačních a bezpečnostních důvodů. Virtuální počítače a instance rolí PaaS nasazené do podsítí (stejných nebo různých) v rámci jedné virtuální sítě můžou navzájem komunikovat bez jakékoli další konfigurace. Můžete taky nakonfigurovat [skupinu zabezpečení sítě (Nsg)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) povolit nebo zamítnout síťový provoz do instancí virtuálních počítačů na základě pravidel, které jsou nakonfigurované v seznamu řízení přístupu (ACL) skupiny zabezpečení sítě. Skupiny NSG můžou být přidružené buď k podsítím, nebo k jednotlivým instancím virtuálních počítačů v této podsíti. Pokud je skupina zabezpečení sítě přidružená k podsíti, pravidla seznamu ACL platí pro všechny instance virtuálních počítačů v této podsíti.

## <a name="next-steps"></a>Další kroky

- [Možnosti izolace sítě pro počítače se ve Windows Azure Virtual Networks](https://azure.microsoft.com/blog/network-isolation-options-for-machines-in-windows-azure-virtual-networks/)

To zahrnuje classic front-endu a back-end scénář, kde počítače v konkrétní back endovou síť nebo podsíť může povolit jenom určitá klienty nebo jiným počítačům připojit do určitého koncového bodu na seznamu povolených IP adres na základě.

- [COMPUTE úrovně izolace](https://msenterprise.global.ssl.fastly.net/vnext/PDFs/A01_AzureSecurityWhitepaper20160415c.pdf)

Microsoft Azure nabízí, různé cloudové výpočetní služby, které zahrnují široké škály výpočetních instancí a služby, které je možné škálovat směrem nahoru a dolů automaticky podle potřeb vaší aplikace nebo enterprise.

- [Izolace úložiště](https://msenterprise.global.ssl.fastly.net/vnext/PDFs/A01_AzureSecurityWhitepaper20160415c.pdf)

Microsoft Azure odděluje výpočetní založená na virtuálních počítačích zákazníků ze služby storage. Toto oddělení umožňuje výpočetní výkon a úložiště nezávisle na sobě škálovat usnadňují poskytují více tenantů a izolaci. Proto úložiště Azure běží na samostatné hardwaru bez připojení k síti pro Azure Compute s výjimkou logicky. Všechny žádosti o spuštění prostřednictvím protokolu HTTP nebo HTTPS, na základě volby zákazníka.

