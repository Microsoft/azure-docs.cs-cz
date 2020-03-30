---
title: Izolace ve veřejném cloudu Azure | Dokumenty společnosti Microsoft
description: Zjistěte, jak Azure poskytuje izolaci proti škodlivým i nesezlým uživatelům a nabízí architektům různé možnosti izolace.
services: security
documentationcenter: na
author: UnifyCloud
manager: rkarlin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2019
ms.author: TomSh
ms.openlocfilehash: c6e74e7992326d2a4b8fe24510742422b005c2e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280310"
---
# <a name="isolation-in-the-azure-public-cloud"></a>Izolace ve veřejném cloudu Azure
Azure umožňuje spouštět aplikace a virtuální počítače (VM) na sdílené fyzické infrastruktuře. Jednou z hlavních ekonomických motivací pro spouštění aplikací v cloudovém prostředí je možnost distribuovat náklady na sdílené prostředky mezi více zákazníků. Tato praxe vícenájemního pronájmu zvyšuje efektivitu multiplexováním zdrojů mezi různorodými zákazníky při nízkých nákladech. Bohužel také zavádí riziko sdílení fyzických serverů a dalších prostředků infrastruktury pro spuštění citlivých aplikací a virtuálních počítačů, které mohou patřit libovolnému a potenciálně škodlivému uživateli.

Tento článek popisuje, jak Azure poskytuje izolaci proti škodlivým i nese zlými úmysly uživatelů a slouží jako vodítko pro navrhování cloudových řešení tím, že nabízí různé možnosti izolace architektům.

## <a name="tenant-level-isolation"></a>Izolace na úrovni klienta
Jednou z hlavních výhod cloud computingu je koncepce sdílené společné infrastruktury pro mnoho zákazníků současně, což vede k úsporám z rozsahu. Tento koncept se nazývá multi-nájem. Společnost Microsoft neustále pracuje na tom, aby architektura více klientů Microsoft Cloud Azure podporovala standardy zabezpečení, důvěrnosti, ochrany osobních údajů, integrity a dostupnosti.

V prostředí cloudu se dá klient služby Azure AD definovat jako klient nebo organizace, která vlastní a spravuje konkrétní instanci dané cloudové služby. S platformou identit poskytovanou Microsoft Azure je tenant jednoduše vyhrazenou instancí Služby Azure Active Directory (Azure AD), kterou vaše organizace přijímá a vlastní, když se zaregistruje ke cloudové službě Microsoftu.

Každý adresář služby Azure AD je oddělený od ostatních adresářů služby Azure AD. Adresář služby Azure AD byl navržen tak, aby se jednalo o zabezpečený prostředek k použití pouze pro vaši organizaci – stejně jako je podniková kancelářská budova zabezpečeným prostředkem výhradně vaší organizace. Architektura služby Azure AD znemožňuje míchání dat zákazníků a informací o identitě. To znamená, že se uživatelé a správci jednoho adresáře služby Azure AD nemohou dostat – ať už omylem nebo záměrně – k datům v jiném adresáři.

### <a name="azure-tenancy"></a>Azure Nájem
Azure tenanta (Předplatné Azure) odkazuje na vztah "zákazník/fakturace" a jedinečný [klient](../../active-directory/develop/quickstart-create-new-tenant.md) ve [službě Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md). Izolace na úrovni tenanta v Microsoft Azure se dosahuje pomocí Služby Azure Active Directory a [ovládacích prvků založených na rolích,](../../role-based-access-control/overview.md) které nabízí. Každé předplatné Azure je přidružené k jednomu adresáři Služby Azure Active Directory (AD).

Uživatelé, skupiny a aplikace z tohoto adresáře můžete spravovat prostředky v předplatném Azure. Tato přístupová práva můžete přiřadit pomocí portálu Azure, nástrojů příkazového řádku Azure a api pro správu Azure. Klient Azure AD je logicky izolované pomocí hranice zabezpečení tak, aby žádný zákazník přístup nebo ohrozit co tenantů, buď se zlými úmysly nebo náhodně. Azure AD běží na "holé metal" servery izolované na segmentu oddělené sítě, kde filtrování paketů na úrovni hostitele a Brána Firewall systému Windows blokovat nežádoucí připojení a provoz.

- Přístup k datům ve službě Azure AD vyžaduje ověření uživatele prostřednictvím služby tokenů zabezpečení (STS). Informace o existenci uživatele, povoleném stavu a roli používá autorizační systém k určení, zda je požadovaný přístup k cílovému klientovi v této relaci autorizován pro tohoto uživatele.

![Azure Nájem](./media/isolation-choices/azure-isolation-fig1.png)


- Klienti jsou diskrétní kontejnery a neexistuje žádný vztah mezi nimi.

- Žádný přístup mezi tenanty, pokud správce tenanta uděluje prostřednictvím federace nebo zřizování uživatelských účtů od jiných klientů.

- Fyzický přístup k serverům, které tvoří službu Azure AD a přímý přístup k back-endovým systémům Azure AD, je omezen.

- Uživatelé Azure AD nemají přístup k fyzickým prostředkům nebo umístěním, a proto není možné, aby obejít logické kontroly zásad RBAC uvedené v následujícím textu.

Pro diagnostiku a údržbu potřeby je vyžadován provozní model, který využívá systém zvýšení oprávnění just-in-time. Azure AD Privilegované Správy identit (PIM) zavádí koncept způsobilé ho správce. [Způsobilými správci](../../active-directory/privileged-identity-management/pim-configure.md) by měli být uživatelé, kteří tu a tam potřebují privilegovaný přístup, ale ne každý den. Dokud uživatel nepotřebuje přístup, je tato role neaktivní. Jakmile uživatel bude přístup potřebovat, dokončí proces aktivace a na předem určenou dobu se stane aktivním správcem.

![Azure AD Privileged Identity Management](./media/isolation-choices/azure-isolation-fig2.png)

Služba Azure Active Directory hostuje každého klienta ve vlastním chráněném kontejneru se zásadami a oprávněními pro kontejner a v jeho rámci, který vlastní a spravuje výhradně klient.

Koncept kontejnerů tenanta je hluboce zakořeněný v adresářové službě ve všech vrstvách, od portálů až po trvalé úložiště.

I v případě, že metadata z více klientů služby Azure Active Directory je uložena na stejném fyzickém disku, neexistuje žádný vztah mezi kontejnery než co je definováno adresářovou službou, což je zase diktováno správcem klienta.

### <a name="azure-role-based-access-control-rbac"></a>Řízení přístupu na základě rolí Azure (RBAC)
[Řízení přístupu na základě rolí Azure (RBAC)](../../role-based-access-control/overview.md) vám pomůže sdílet různé součásti dostupné v rámci předplatného Azure tím, že poskytuje jemně odstupňovanou správu přístupu pro Azure. Azure RBAC umožňuje oddělit povinnosti v rámci vaší organizace a udělit přístup na základě toho, co uživatelé potřebují k provádění svých úloh. Místo toho, aby všichni neomezená oprávnění v předplatném Azure nebo prostředky, můžete povolit pouze určité akce.

Azure RBAC má tři základní role, které platí pro všechny typy prostředků:

- **Vlastník** má plný přístup ke všem prostředkům, včetně práva delegovat přístup k ostatním.

- **Přispěvatel** může vytvářet a spravovat všechny typy prostředků Azure, ale nemůže udělit přístup ostatním.

- **Reader** může zobrazit existující prostředky Azure.

![Řízení přístupu na základě role v Azure](./media/isolation-choices/azure-isolation-fig3.png)

Zbývající role RBAC v Azure umožňují správu konkrétních prostředků Azure. Role Přispěvatel virtuálních počítačů například uživateli umožňuje vytvářet a spravovat virtuální počítače. Neposkytuje jim přístup k virtuální síti Azure nebo podsíti, ke které se virtuální počítač připojuje.

[Předdefinované role RBAC](../../role-based-access-control/built-in-roles.md) uvádějí role dostupné v Azure. Určuje operace a obor, které každá předdefinovaná role uděluje uživatelům. Pokud chcete definovat své vlastní role pro ještě větší kontrolu, podívejte se, jak vytvořit [vlastní role v Azure RBAC](../../role-based-access-control/custom-roles.md).

Mezi další funkce služby Azure Active Directory patří:
- Azure AD podporuje jednotné přihlašování (SSO) k aplikacím SaaS bez ohledu na to, kde jsou hostované. Některé aplikace jsou federované pomocí Azure AD, jiné používají jednotné přihlašování pomocí hesla. Federované aplikace mohou také podporovat zřizování uživatelů a [trezor hesel](https://www.techopedia.com/definition/31415/password-vault).

- Přístup k datům v rámci [Azure Storage](https://azure.microsoft.com/services/storage/) je řízen prostřednictvím ověřování. Každý účet úložiště má primární klíč[(klíč účtu úložiště](../../storage/common/storage-create-storage-account.md)nebo SAK) a sekundární tajný klíč (sdílený přístupový podpis nebo SAS).

- Azure AD poskytuje identity jako služba prostřednictvím federace pomocí [služby AD FS ,](../../active-directory/hybrid/how-to-connect-fed-azure-adfs.md)synchronizace a replikace s místními adresáři.

- [Azure Multi-Factor Authentication](../../active-directory/authentication/multi-factor-authentication.md) je vícefaktorová ověřovací služba, která vyžaduje, aby uživatelé ověřovali přihlášení pomocí mobilní aplikace, telefonního hovoru nebo textové zprávy. S Azure AD ho můžou používat k zabezpečení místních prostředků pomocí serveru Azure Multi-Factor Authentication a také s vlastními aplikacemi a adresáři pomocí sady SDK.

- [Azure AD Domain Services](https://azure.microsoft.com/services/active-directory-ds/) umožňuje připojit virtuální počítače Azure k doméně služby Active Directory bez nasazení řadičů domény. K těmto virtuálním počítačům se můžete přihlásit pomocí firemních přihlašovacích údajů služby Active Directory a spravovat virtuální počítače spojené s doménou pomocí zásad skupiny k vynucení směrných plánů zabezpečení na všech virtuálních počítačích Azure.

- [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) poskytuje vysoce dostupnou službu pro správu globálníidentity pro aplikace orientované na spotřebitele, která se škáluje na stovky milionů identit. Dá se integrovat do mobilních i webových platforem. Vaši spotřebitelé se mohou přihlásit ke všem vašim aplikacím prostřednictvím přizpůsobitelných prostředí pomocí svých stávajících účtů na sociálních sítích nebo vytvořením přihlašovacích údajů.

### <a name="isolation-from-microsoft-administrators--data-deletion"></a>Izolace od správců společnosti Microsoft & odstranění dat
Společnost Microsoft přijímá přísná opatření k ochraně vašich dat před nevhodným přístupem nebo používáním neoprávněnými osobami. Tyto provozní procesy a ovládací prvky jsou podpořeny [podmínkami online služeb](https://aka.ms/Online-Services-Terms), které nabízejí smluvní závazky, kterými se řídí přístup k vašim údajům.

-   Technici společnosti Microsoft nemají výchozí přístup k vašim datům v cloudu. Místo toho jim je pod dohledem vedení udělen přístup pouze v případě potřeby. Tento přístup je pečlivě kontrolována a protokolována a odvolán, když již není potřeba.

-   Společnost Microsoft může najmout jiné společnosti, aby jejím jménem poskytovaly omezené služby. Subdodavatelé mají přístup k zákaznickým datům pouze za účelem poskytování služeb, které jsme si najali, aby je poskytovali, a je jim zakázáno je používat k jakémukoli jinému účelu. Dále jsou smluvně zavázáni zachovávat důvěrnost informací našich zákazníků.

Obchodní služby s auditovanými certifikacemi, jako je ISO/IEC 27001, jsou pravidelně ověřovány společností Microsoft a akreditovanými auditorskými společnostmi, které provádějí vzorové audity, aby tento přístup potvrdily, a to pouze pro legitimní obchodní účely. Vždy můžete přistupovat ke svým vlastním zákaznickým datům kdykoli a z jakéhokoli důvodu.

Pokud odstraníte všechna data, Microsoft Azure odstraní data, včetně všech kopií uložených v mezipaměti nebo záložních kopií. U služeb v oboru k tomuto odstranění dojde do 90 dnů po skončení doby uchovávání. (Služby in-scope jsou definovány v části Podmínky zpracování dat v podmínkách online [služeb](https://aka.ms/Online-Services-Terms).)

Pokud disková jednotka použitá pro úložiště utrpí selhání hardwaru, je bezpečně [vymazána nebo zničena](https://microsoft.com/trustcenter/privacy/you-own-your-data) dříve, než ji společnost Microsoft vrátí výrobci k výměně nebo opravě. Data na jednotce jsou přepsána, aby bylo zajištěno, že data nelze žádným způsobem obnovit.

## <a name="compute-isolation"></a>Výpočetní izolace
Microsoft Azure poskytuje různé cloudové výpočetní služby, které zahrnují široký výběr výpočetních instancí & služby, které se můžou automaticky škálovat nahoru a dolů, aby vyhovovaly potřebám vaší aplikace nebo podniku. Tyto výpočetní instance a služby nabízejí izolaci na více úrovních pro zabezpečení dat bez obětování flexibility v konfiguraci, kterou zákazníci požadují.

### <a name="isolated-virtual-machine-sizes"></a>Izolované velikosti virtuálních strojů

[!INCLUDE [virtual-machines-common-isolation](../../../includes/virtual-machines-common-isolation.md)]

### <a name="dedicated-hosts"></a>Vyhrazení hostitelé
Kromě izolovaných hostitelů popsaných v předchozí části azure také nabízí vyhrazené hostitele. Vyhrazení hostitelé v Azure je služba, která poskytuje fyzické servery, které můžou hostovat jeden nebo více virtuálních počítačů a které jsou vyhrazené pro jedno předplatné Azure. Vyhrazení hostitelé poskytují izolaci hardwaru na úrovni fyzického serveru. Žádné jiné virtuální počítače se umístí na vaše hostitele. Vyhrazení hostitelé jsou nasazeni ve stejných datových centrech a sdílejí stejnou síť ovou a základní infrastrukturu úložiště jako ostatní neizolovaní hostitelé. Další informace najdete v podrobném přehledu [vyhrazených hostitelů Azure](https://docs.microsoft.com/azure/virtual-machines/windows/dedicated-hosts).

### <a name="hyper-v--root-os-isolation-between-root-vm--guest-vms"></a>Hyper-V & izolace kořenového operačního operačního služby mezi virtuálními virtuálními & hostované virtuální ch odchovátko
Výpočetní platforma Azure je založená na virtualizaci počítače, což znamená, že veškerý kód zákazníka se spouští ve virtuálním počítači Hyper-V. Na každém uzlu Azure (nebo koncovém síťovém bodě) je hypervisor, který běží přímo přes hardware a rozděluje uzel na proměnný počet virtuálních počítačů hostovaného počítače (VM).


![Hyper-V & izolace kořenového operačního operačního služby mezi virtuálními virtuálními & hostované virtuální ch odchovátko](./media/isolation-choices/azure-isolation-fig4.jpg)


Každý uzel má také jeden speciální kořenový virtuální virtuální ms, který spouští hostitelský operační systém. Kritická hranice je izolace kořenového virtuálního uživatele od hostovaných virtuálních ms a hostovaných virtuálních ms od sebe navzájem, spravované hypervisorem a kořenovým osem. Párování hypervisoru a root os využívá desítky let zkušeností se zabezpečením operačního systému společnosti Microsoft a novější mučící se z technologie Hyper-V společnosti Microsoft, aby zajistilo silnou izolaci hostujících virtuálních aplikací.

Platforma Azure používá virtualizované prostředí. Uživatelské instance fungují jako samostatné virtuální počítače, které nemají přístup k fyzickému hostitelskému serveru.

Hypervisor Azure funguje jako mikrojádro a předává všechny požadavky na přístup hardwaru z virtuálních počítačů hosta hostiteli ke zpracování pomocí rozhraní sdílené paměti nazývaného VMBus. Tento postup brání uživatelům v získání přímého přístupu k systému pro čtení, zápis a spouštění a snižuje riziko sdílení systémových prostředků.

### <a name="advanced-vm-placement-algorithm--protection-from-side-channel-attacks"></a>Pokročilý algoritmus umístění virtuálních vod & ochranu před útoky na postranní kanály
Jakýkoli útok mezi virtuálními počítači zahrnuje dva kroky: umístění virtuálního počítače ovládaného protivníkem na stejného hostitele jako jeden z virtuálních počítačů oběti a následné překročení hranice izolace, aby buď ukradli citlivé informace o oběti nebo ovlivnili jeho výkon pro chamtivost nebo vandalismus. Microsoft Azure poskytuje ochranu v obou krocích pomocí pokročilého algoritmu umístění virtuálních počítačů a ochrany před všemi známými útoky na straně kanálu, včetně hlučných sousedních virtuálních počítačů.

### <a name="the-azure-fabric-controller"></a>Řadič Azure Fabric
Řadič Azure Fabric je zodpovědný za přidělování prostředků infrastruktury pro úlohy klienta a spravuje jednosměrnou komunikaci z hostitele do virtuálních počítačů. Algoritmus umístění virtuálního počítače řadiče infrastruktury Azure je vysoce sofistikované a téměř nemožné předpovědět jako úroveň fyzického hostitele.

![Řadič Azure Fabric](./media/isolation-choices/azure-isolation-fig5.png)

Hypervisor Azure vynucuje oddělení paměti a procesů mezi virtuálními počítači a bezpečně směruje síťový provoz do klienty hostovaného operačního systému. To eliminuje možnost útoku a útoku na straně kanálu na úrovni virtuálního viceprezidenta.

V Azure je kořenový virtuální počítač zvláštní: spouští posílený operační systém s názvem kořenový operační systém, který hostuje agenta infrastruktury (DM). Fa se zase používají ke správě agentů hosta (GA) v rámci operačních ses hosta na virtuálních počítačích zákazníků. Fa také spravovat uzly úložiště.

Kolekce hypervisoru Azure, root OS/DP a virtuálních počítačích/ga zákazníků zahrnuje výpočetní uzel. Fa jsou spravovány řadičem infrastruktury (FC), který existuje mimo výpočetní a úložné uzly (výpočetní a úložné clustery jsou spravovány samostatnými řadiči F). Pokud zákazník aktualizuje konfigurační soubor své aplikace, když je spuštěn, FC komunikuje s DM, který pak kontaktuje ga, které upozorní aplikaci na změnu konfigurace. V případě selhání hardwaru fc automaticky najde dostupný hardware a restartuje virtuální počítač tam.

![Řadič infrastruktury Azure](./media/isolation-choices/azure-isolation-fig6.jpg)

Komunikace z řadiče prostředků infrastruktury agentovi je jednosměrná. Agent implementuje službu chráněnou protokolem SSL, která reaguje pouze na požadavky z řadiče. Nemůže iniciovat připojení k řadiči nebo jiným privilegovaným interním uzlům. FC zachází se všemi odpověďmi, jako by byly nedůvěryhodné.


![Řadič prostředků infrastruktury](./media/isolation-choices/azure-isolation-fig7.png)

Izolace se rozšiřuje z kořenového virtuálního uživatele z virtuálních virtuálních měn hosta a virtuálních virtuálních zařízení hosta od sebe navzájem. Výpočetní uzly jsou také izolované od uzlů úložiště pro zvýšenou ochranu.


Hypervisor a hostitelský operační systém poskytují síťový paket - filtry, které pomáhají zajistit, že nedůvěryhodné virtuální počítače nemohou generovat podpěrný provoz nebo přijímat přenosy, které jim nejsou adresovány, směrovat přenos y koncovým bodům chráněné infrastruktury nebo odesílat a přijímat nevhodný přenosový provoz.


### <a name="additional-rules-configured-by-fabric-controller-agent-to-isolate-vm"></a>Další pravidla nakonfigurovaná agentem řadiče prostředků infrastruktury k izolátí virtuálního počítače
Ve výchozím nastavení je veškerý provoz blokován při vytvoření virtuálního počítače a agent řadiče prostředků infrastruktury nakonfiguruje filtr paketů tak, aby přidával pravidla a výjimky umožňující autorizovaný provoz.

Existují dvě kategorie pravidel, která jsou naprogramována:

-   **Pravidla konfigurace nebo infrastruktury stroje:** Ve výchozím nastavení je veškerá komunikace blokována. Existují výjimky, které umožňují virtuálnímu počítači odesílat a přijímat přenosy DHCP a DNS. Virtuální počítače můžou taky odesílat provoz na "veřejný" internet a odesílat provoz jiným virtuálním počítačům v rámci stejné virtuální sítě Azure a aktivačního serveru operačního systému. Seznam povolených odchozích cílů virtuálních počítačů nezahrnuje podsítě směrovače Azure, správu Azure a další vlastnosti Microsoftu.

-   **Konfigurační soubor role:** To definuje příchozí seznamy řízení přístupu (ACLs) na základě modelu služby klienta.

### <a name="vlan-isolation"></a>Izolace sítě VLAN
V každém clusteru jsou tři sítě VLAN:

![Izolace sítě VLAN](./media/isolation-choices/azure-isolation-fig8.jpg)


-   Hlavní síť VLAN – propojuje nedůvěryhodné zákaznické uzly

-   FC VLAN – obsahuje důvěryhodné cfc a podpůrné systémy

-   Zařízení VLAN – obsahuje důvěryhodná síťová a další zařízení infrastruktury

Komunikace z FC VLAN je povolena do hlavní sítě VLAN, ale nelze ji zahájit z hlavní sítě VLAN do sítě FC VLAN. Komunikace je také blokována z hlavní sítě VLAN do zařízení VLAN. To zajišťuje, že i v případě, že uzel se spuštěným kódem zákazníka je ohrožena, nemůže zaútočit uzly na FC nebo zařízení VLAN.

## <a name="storage-isolation"></a>Izolace úložiště
### <a name="logical-isolation-between-compute-and-storage"></a>Logická izolace mezi výpočetními prostředky a úložištěm
Jako součást svého základního návrhu Microsoft Azure odděluje výpočty založené na virtuálních počítačích od úložiště. Toto oddělení umožňuje výpočtu a úložiště škálovat nezávisle, což usnadňuje poskytování víceklientských a izolovaných.

Azure Storage proto běží na samostatném hardwaru bez připojení k síti azure compute s výjimkou logicky. To znamená, že při vytvoření virtuálního disku není místo na disku přiděleno pro celou kapacitu. Místo toho je vytvořena tabulka, která mapuje adresy na virtuálním disku do oblastí na fyzickém disku a tato tabulka je zpočátku prázdná. **Při prvním zápisu dat zákazníka na virtuální disk je přiděleno místo na fyzickém disku a v tabulce je umístěn ukazatel na něj.**
### <a name="isolation-using-storage-access-control"></a>Izolace pomocí ovládacího prvku přístupu k úložišti
**Řízení přístupu ve službě Azure Storage** má jednoduchý model řízení přístupu. Každé předplatné Azure můžete vytvořit jeden nebo více účtů úložiště. Každý účet úložiště má jeden tajný klíč, který se používá k řízení přístupu ke všem datům v tomto účtu úložiště.

![Izolace pomocí ovládacího prvku přístupu k úložišti](./media/isolation-choices/azure-isolation-fig9.png)

**Přístup k datům úložiště Azure (včetně tabulek)** lze řídit pomocí tokenu [SAS (sdílený přístupový podpis),](../../storage/common/storage-dotnet-shared-access-signature-part-1.md) který uděluje přístup s vymezeným oborem. SAS je vytvořen prostřednictvím šablony dotazu (URL), podepsané sadou [SAK (klíč účtu úložiště).](https://msdn.microsoft.com/library/azure/ee460785.aspx) Tato [podepsaná adresa URL](../../storage/common/storage-dotnet-shared-access-signature-part-1.md) může být poskytnuta jinému procesu (tj. delegovanému), který pak může vyplnit podrobnosti o dotazu a požádat o službu úložiště. SAS umožňuje udělit přístup na základě času klientům bez odhalení tajného klíče účtu úložiště.

SAS znamená, že můžeme udělit klientovi omezená oprávnění, objektům v našem účtu úložiště po určitou dobu a se zadanou sadou oprávnění. Tato omezená oprávnění můžeme udělit, aniž bychom museli sdílet přístupové klíče vašeho účtu.

### <a name="ip-level-storage-isolation"></a>Izolace úložiště na úrovni IP
Můžete vytvořit brány firewall a definovat rozsah IP adres pro důvěryhodné klienty. S rozsahem IP adres se k [Úložišti Azure Storage](../../storage/blobs/security-recommendations.md)mohou připojit jenom klienti, kteří mají IP adresu v rámci definovaného rozsahu .

Data úložiště IP mohou být chráněna před neoprávněnými uživateli prostřednictvím síťového mechanismu, který se používá k přidělení vyhrazeného nebo vyhrazeného tunelového propojení provozu do úložiště IP.

### <a name="encryption"></a>Šifrování
Azure nabízí následující typy šifrování k ochraně dat:
-   Šifrování během přenosu

-   Šifrování v klidovém stavu

#### <a name="encryption-in-transit"></a>Šifrování během přenosu
Šifrování při přenosu je mechanismus ochrany dat při přenosu v sítích. S Azure Storage můžete zabezpečit data pomocí:

-   [Šifrování na úrovni přenosu](../../storage/blobs/security-recommendations.md), jako je například HTTPS při přenosu dat do nebo z Azure Storage.

-   [Šifrování vodičů](../../storage/blobs/security-recommendations.md), jako je šifrování SMB 3.0 pro sdílené složky Azure.

-   [Šifrování na straně klienta](../../storage/blobs/security-recommendations.md), šifrování dat před jejich přenosem do úložiště a dešifrování dat po jejich přenosu z úložiště.

#### <a name="encryption-at-rest"></a>Šifrování v klidovém stavu
Pro mnoho organizací je [šifrování dat v klidovém stavu](isolation-choices.md) povinným krokem k ochraně osobních údajů, dodržování předpisů a suverenitě dat. Existují tři funkce Azure, které poskytují šifrování dat, která je "v klidovém stavu":

-   [Šifrování služby storage service](../../storage/blobs/security-recommendations.md) umožňuje požadovat, aby služba úložiště automaticky šifrovala data při zápisu do úložiště Azure.

-   [Šifrování na straně klienta](../../storage/blobs/security-recommendations.md) také poskytuje funkci šifrování v klidovém stavu.

-   [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) umožňuje šifrovat disky operačního systému a datové disky používané virtuálním počítačem IaaS.

#### <a name="azure-disk-encryption"></a>Azure Disk Encryption
[Azure Disk Encryption](../azure-security-disk-encryption-overview.md) pro virtuální počítače (VMs) vám pomůže řešit požadavky na zabezpečení organizace a dodržování předpisů šifrováním disků virtuálních počítačů (včetně spouštěcích a datových disků) pomocí klíčů a zásad, které řídíte v [trezoru klíčů Azure](https://azure.microsoft.com/services/key-vault/).

Řešení Šifrování disku pro systém Windows je založeno na [nástroji Microsoft BitLocker Drive Encryption](https://technet.microsoft.com/library/cc732774.aspx)a linuxové řešení je založeno na [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt).

Řešení podporuje následující scénáře pro virtuální počítače IaaS, pokud jsou povolené v Microsoft Azure:
-   Integrace s trezorem klíčů Azure

-   Virtuální virtuální ms standardní úrovně: A, D, DS, G, GS a tak dále, řady Virtuální chodů IaaS

-   Povolení šifrování na virtuálních počítačích Windows a Linux IaaS

-   Zakázání šifrování na operačních jednotkách a datových jednotkách pro virtuální servery Windows IaaS

-   Zakázání šifrování na datových discích pro virtuální počítače IaaS s Linuxem

-   Povolení šifrování na virtuálních počítačích IaaS se systémem Windows Client OS

-   Povolení šifrování na svazcích s přípojnými cestami

-   Povolení šifrování na virtuálních počítačích s Linuxem, které jsou nakonfigurovány s prokládáním disku (RAID) pomocí [mdadm](https://en.wikipedia.org/wiki/Mdadm)

-   Povolení šifrování na virtuálních počítačích SB S IP pomocí [LVM(Logical Volume Manager)](https://msdn.microsoft.com/library/windows/desktop/bb540532) pro datové disky

-   Povolení šifrování na virtuálních počítačích se systémem Windows, které jsou nakonfigurované pomocí prostorů úložiště

-   Všechny veřejné oblasti Azure jsou podporované

Řešení nepodporuje následující scénáře, funkce a technologie ve verzi:

-   Virtuální měřidla IaaS základní úrovně

-   Zakázání šifrování na jednotce operačního systému pro virtuální počítače Linux IaaS

-   Virtuální aplikace IaaS, které se vytvořily pomocí metody vytváření klasického virtuálního mísy

-   Integrace s místní službou správy klíčů

-   Soubory Azure (sdílený souborový systém), síťový systém souborů (NFS), dynamické svazky a virtuální počítače windows, které jsou nakonfigurované pomocí softwarových systémů RAID

## <a name="sql-azure-database-isolation"></a>Izolace databáze SQL Azure
SQL Database je služba v cloudu Microsoftu poskytující relační databáze založené na předním databázovém stroji Microsoft SQL Server a schopné obsloužit i klíčové úlohy. SQL Database nabízí předvídatelnou izolaci dat na úrovni účtu, zeměpisné oblasti založené a založené na sítích – to vše s téměř nulovou správou.

### <a name="sql-azure-application-model"></a>Aplikační model SQL Azure

[Microsoft SQL Azure](../../sql-database/sql-database-single-database-get-started.md) Databáze je cloudová relační databázová služba založená na technologiích SERVERU SQL Server. Poskytuje vysoce dostupnou, škálovatelnou databázovou službu s více klienty hostovohouji společností Microsoft v cloudu.

Z hlediska aplikace SQL Azure poskytuje následující hierarchii: Každá úroveň má 1:N omezení úrovně níže.

![Aplikační model SQL Azure](./media/isolation-choices/azure-isolation-fig10.png)

Účet a předplatné jsou koncepty platformy Microsoft Azure pro přidružení fakturace a správy.

Logické servery a databáze jsou specifické pro koncepty SQL Azure a jsou spravované pomocí SQL Azure, poskytovaných rozhraní OData a TSQL nebo prostřednictvím portálu SQL Azure, který se integroval do portálu Azure.

Servery SQL Azure nejsou fyzické instance nebo instance virtuálních počítače, místo toho jsou kolekce databází, zásady správy sdílení a zabezpečení, které jsou uloženy v takzvané "logické hlavní" databázi.

![SQL Azure](./media/isolation-choices/azure-isolation-fig11.png)

Mezi hlavní logické databáze patří:

-   Přihlášení SQL slouží k připojení k serveru

-   Pravidla brány firewall

Informace související s fakturací a používáním databází SQL Azure ze stejného logického serveru nejsou zaručeny na stejné fyzické instanci v clusteru SQL Azure, místo toho musí aplikace při připojování zadat název cílové databáze.

Z pohledu zákazníka je logický server vytvořen v geograficky grafické oblasti, zatímco skutečné vytvoření serveru probíhá v jednom z clusterů v oblasti.

### <a name="isolation-through-network-topology"></a>Izolace prostřednictvím topologie sítě

Při vytvoření logického serveru a registraci jeho názvu DNS odkazuje název DNS na takzvanou adresu "Gateway VIP" v konkrétním datovém centru, kde byl server umístěn.

Za VIP (virtuální IP adresa) máme sbírku bezstavových služeb brány. Obecně platí, že brány zapojit, když je nutná koordinace mezi více zdrojů dat (hlavní databáze, databáze uživatelů, atd.). Služby brány implementují následující:
-   **Proxy připojení TDS.** To zahrnuje vyhledání uživatelské databáze v back-endovém clusteru, implementaci přihlašovací sekvence a následné předávání paketů TDS do back-endu a zpět.

-   **Správa databáze.** To zahrnuje implementaci kolekce pracovních postupů k provedení operace databáze CREATE/ALTER/DROP. Databázové operace lze vyvolat buď sledováním paketů TDS nebo explicitními datovými datovými daty.

-   CREATE/ALTER/DROP přihlášení/uživatelské operace

-   Operace správy logického serveru prostřednictvím rozhraní OData API

![Izolace prostřednictvím topologie sítě](./media/isolation-choices/azure-isolation-fig12.png)

Úroveň za branami se nazývá "back-end". To je místo, kde jsou všechna data uložena ve vysoce dostupným způsobem. Každá část dat je prý patří do "oddíl" nebo "jednotka převzetí služeb při selhání", každý z nich má alespoň tři repliky. Repliky jsou uloženy a replikovány modulem SQL Server a spravovány systémem převzetí služeb při selhání, který se často označuje jako "fabric".

Obecně platí, že systém back-end nekomunikuje odchozí do jiných systémů jako bezpečnostní opatření. To je vyhrazeno pro systémy v front-endu (brána) vrstvy. Počítače úrovně brány mají omezená oprávnění na back-endových počítačích, aby se minimalizovala plocha útoku jako mechanismus ochrany do hloubky.

### <a name="isolation-by-machine-function-and-access"></a>Izolace podle funkce stroje a přístupu
SQL Azure (skládá se ze služeb spuštěných na různých funkcích počítače. SQL Azure je rozdělena do "back-endu" cloudové databáze a "front-end" (brána/správa) prostředí, s obecným principem provozu pouze přechod do back-endu a ne ven. Prostředí front-endu může komunikovat s vnějším světem jiných služeb a obecně má pouze omezená oprávnění v back-endu (stačí volat vstupní body, které potřebuje k vyvolání).

## <a name="networking-isolation"></a>Izolace sítí
Nasazení Azure má několik vrstev izolace sítě. Následující diagram znázorňuje různé vrstvy izolace sítě Azure poskytuje zákazníkům. Tyto vrstvy jsou nativní v samotné platformě Azure a funkce definované zákazníkem. Příchozí z internetu, Azure DDoS poskytuje izolaci proti rozsáhlé útoky proti Azure. Další vrstva izolace je zákazníkem definované veřejné IP adresy (koncové body), které se používají k určení, který provoz může projít přes cloudovou službu do virtuální sítě. Nativní izolace virtuální sítě Azure zajišťuje úplnou izolaci od všech ostatních sítí a provoz protéká jenom prostřednictvím uživatelem nakonfigurovaných cest a metod. Tyto cesty a metody jsou další vrstva, kde nsgs, UDR a síťová virtuální zařízení lze použít k vytvoření hranice izolace k ochraně nasazení aplikací v chráněné síti.

![Izolace sítí](./media/isolation-choices/azure-isolation-fig13.png)

**Izolace provozu:** [Virtuální síť](../../virtual-network/virtual-networks-overview.md) je hranice izolace provozu na platformě Azure. Virtuální počítače (VM) v jedné virtuální síti nemůže komunikovat přímo do virtuálních počítačů v jiné virtuální síti, i když obě virtuální sítě jsou vytvořeny stejným zákazníkem. Izolace je kritická vlastnost, která zajišťuje, že virtuální počítače zákazníků a komunikace zůstanou privátní v rámci virtuální sítě.

[Podsíť](../../virtual-network/virtual-networks-overview.md) nabízí další vrstvu izolace ve virtuální síti na základě rozsahu IP adres. IP adresy ve virtuální síti, můžete rozdělit virtuální síť do více podsítí pro organizaci a zabezpečení. Virtuální počítače a instance rolí PaaS nasazené do podsítí (stejných nebo různých) v rámci jedné virtuální sítě můžou navzájem komunikovat bez jakékoli další konfigurace. Skupiny [zabezpečení sítě (NSG)](../../virtual-network/virtual-networks-overview.md) můžete také nakonfigurovat tak, aby povolovaly nebo odepíraly síťový provoz instanci virtuálního počítače na základě pravidel nakonfigurovaných v seznamu řízení přístupu (ACL) skupiny zabezpečení sítě. Skupiny NSG můžou být přidružené buď k podsítím, nebo k jednotlivým instancím virtuálních počítačů v této podsíti. Pokud je skupina zabezpečení sítě přidružená k podsíti, pravidla seznamu ACL platí pro všechny instance virtuálních počítačů v této podsíti.

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [možnostech izolace sítě pro počítače ve virtuálních sítích Windows Azure](https://azure.microsoft.com/blog/network-isolation-options-for-machines-in-windows-azure-virtual-networks/). To zahrnuje klasický front-end ový a back-endový scénář, kdy počítače v určité back-endové nebo podsíti mohou povolit připojení k určitému koncovému bodu pouze určitým klientům nebo jiným počítačům na základě seznamu povolených adres IP.

- Další informace o [izolaci virtuálních strojů v Azure](../../virtual-machines/windows/isolation.md). Azure Compute nabízí velikosti virtuálních strojů, které jsou izolované pro konkrétní typ hardwaru a vyhrazené pro jednoho zákazníka.
