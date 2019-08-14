---
title: Izolace ve veřejném cloudu Azure | Microsoft Docs
description: Přečtěte si o cloudových výpočetních službách, které zahrnují nejrůznější výpočetní instance & služby, které se můžou automaticky škálovat a snížit tak, aby vyhovovaly potřebám vaší aplikace nebo podniku.
services: security
documentationcenter: na
author: UnifyCloud
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: 9ab09c7215827369b3e1fc449af68be307881f51
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2019
ms.locfileid: "68928013"
---
# <a name="isolation-in-the-azure-public-cloud"></a>Izolace ve veřejném cloudu Azure
##  <a name="introduction"></a>Úvod
### <a name="overview"></a>Přehled
Pro pomoc aktuálním a potenciálním zákazníkům Azure se seznámíte s různými možnostmi zabezpečení dostupnými v a okolím platformy Azure, společnost Microsoft vyvinula řadu dokumentů White Paper, přehledů zabezpečení, osvědčených postupů a Kontrolní seznamy.
Témata v oblasti široké a hloubkové oblasti a jsou pravidelně aktualizovány. Tento dokument je součástí této řady, jak je shrnuto v následující části.

### <a name="azure-platform"></a>Platforma Azure
Azure je otevřená a flexibilní platforma cloudových služeb, která podporuje nejširší škálu operačních systémů, programovacích jazyků, architektur, nástrojů, databází a zařízení. Můžete například provést následující věci:
- Spouštění kontejnerů Linux s integrací Docker
- Vytvářejte aplikace pomocí jazyků JavaScript, Python, .NET, PHP, Java a Node. js; ani
- Vytvořte back-endy pro zařízení s iOS, Androidem a Windows.

Microsoft Azure podporuje stejné technologie jako miliony pro vývojáře a odborníky na IT, kteří už využívají a důvěřují jim.

Při sestavování nebo migraci IT prostředků do, poskytovatele veřejné cloudové služby, se spoléháte na možnosti organizace, které chrání vaše aplikace a data se službami a ovládacími prvky, které poskytují ke správě zabezpečení cloudových prostředků.

Infrastruktura Azure je od zařízení až po aplikace navržena tak, aby umožňovala hostování milionů zákazníků současně a poskytovala důvěryhodný základ pro splnění potřeb zabezpečení jednotlivých podniků. Azure navíc poskytuje širokou škálu konfigurovatelných možností zabezpečení a umožňuje jejich nastavování, takže můžete zabezpečení přizpůsobit jedinečným požadavkům svého nasazení. Tento dokument vám pomůže splnit tyto požadavky.

### <a name="abstract"></a>Abstraktní

Microsoft Azure umožňuje spouštět aplikace a virtuální počítače na sdílené fyzické infrastruktuře. Jednou z primárních motivů pro spouštění aplikací v cloudovém prostředí je schopnost distribuovat náklady na sdílené prostředky mezi více zákazníků. Tento postup víceklientské architektury vylepšuje efektivitu díky multiplexování prostředků mezi různými zákazníky s nízkými náklady. Bohužel taky představuje riziko sdílení fyzických serverů a dalších prostředků infrastruktury ke spouštění citlivých aplikací a virtuálních počítačů, které můžou patřit k libovolnému a potenciálně škodlivému uživateli.

Tento článek popisuje, jak Microsoft Azure poskytuje izolaci proti zlomyslným i nebezpečným uživatelům a slouží jako vodítko pro navrhování cloudových řešení tím, že nabízí různé možnosti izolace architektům. Tento dokument White Paper se zaměřuje na technologii řízení zabezpečení na platformě Azure i na zákazníky a nepokouší se řešit SLA, cenové modely a DevOps postupy.

## <a name="tenant-level-isolation"></a>Izolace na úrovni tenanta
Jednou z hlavních výhod cloud computingu je koncept sdílené a běžné infrastruktury napříč mnoha zákazníky současně, což vede k úsporám rozsahu. Tento koncept se nazývá víceklientská architektura. Microsoft pracuje nepřetržitě, aby se zajistilo, že architektura Microsoft Cloud Azure pro více tenantů podporuje zabezpečení, důvěrnost, ochranu osobních údajů, integritu a dostupnost.

V prostředí cloudu se dá klient služby Azure AD definovat jako klient nebo organizace, která vlastní a spravuje konkrétní instanci dané cloudové služby. S platformou identity, kterou poskytuje Microsoft Azure, je tenant jenom vyhrazená instance Azure Active Directory (Azure AD), kterou vaše organizace obdrží a vlastní, když se přihlásí ke cloudové službě Microsoftu.

Každý adresář služby Azure AD je oddělený od ostatních adresářů služby Azure AD. Adresář služby Azure AD byl navržen tak, aby se jednalo o zabezpečený prostředek k použití pouze pro vaši organizaci – stejně jako je podniková kancelářská budova zabezpečeným prostředkem výhradně vaší organizace. Architektura služby Azure AD znemožňuje míchání dat zákazníků a informací o identitě. To znamená, že se uživatelé a správci jednoho adresáře služby Azure AD nemohou dostat – ať už omylem nebo záměrně – k datům v jiném adresáři.

### <a name="azure-tenancy"></a>Architektura Azure
Azure tenant (předplatné Azure) odkazuje na vztah "Customer/fakturace" a jedinečného [tenanta](../../active-directory/develop/quickstart-create-new-tenant.md) v [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md). Izolace na úrovni tenanta v Microsoft Azure se dosahuje pomocí Azure Active Directory a [ovládacích prvků založených na rolích](../../role-based-access-control/overview.md) , které nabízí. Každé předplatné Azure je přidruženo k jednomu Azure Active Directory (AD) adresáři.

Uživatelé, skupiny a aplikace z tohoto adresáře mohou spravovat prostředky v rámci předplatného Azure. Tato přístupová práva můžete přiřadit pomocí Azure Portal, nástrojů příkazového řádku Azure a rozhraní API pro správu Azure. Tenant Azure AD je logicky izolovaný pomocí hranic zabezpečení, takže žádný zákazník nemůže získat přístup k spoluklientům nebo ho ohrozit, ať už škodlivě, nebo omylem. Služba Azure AD běží na holých serverech, které jsou izolované na odděleném segmentu sítě, kde jsou filtrování paketů na úrovni hostitele a brána Windows Firewall blokuje nežádoucí připojení a provoz.

- Přístup k datům ve službě Azure AD vyžaduje ověření uživatele prostřednictvím služby tokenu zabezpečení (STS). Informace o existenci, povoleném stavu a roli uživatele používá autorizační systém k určení, jestli je požadovaný přístup k cílovému tenantovi autorizovaný pro tohoto uživatele v této relaci.

![Architektura Azure](./media/isolation-choices/azure-isolation-fig1.png)


- Klienti jsou diskrétní kontejnery a mezi nimi neexistuje žádný vztah.

- Bez přístupu mezi klienty, pokud ho správce tenanta neudělí prostřednictvím federace nebo zřizování uživatelských účtů z jiných tenantů.

- Fyzický přístup k serverům, které tvoří službu Azure AD a má přímý přístup k systémům back-end služby Azure AD, je omezený.

- Uživatelé Azure AD nemají přístup k fyzickým prostředkům nebo umístěním, a proto není možné obejít kontroly logických zásad RBAC uvedené níže.

V případě potřeby diagnostiky a údržby je provozní model, který využívá systém zvýšení oprávnění za běhu, vyžadován a používán. Azure AD Privileged Identity Management (PIM) zavádí koncept oprávněného správce. Oprávnění [Správci](../../active-directory/privileged-identity-management/pim-configure.md) by měli být uživatelé, kteří potřebují privilegovaný přístup teď, ale ne každý den. Dokud uživatel nepotřebuje přístup, je tato role neaktivní. Jakmile uživatel bude přístup potřebovat, dokončí proces aktivace a na předem určenou dobu se stane aktivním správcem.

![Azure AD Privileged Identity Management](./media/isolation-choices/azure-isolation-fig2.png)

Azure Active Directory hostuje každého tenanta ve vlastním chráněném kontejneru, pomocí zásad a oprávnění pro a v rámci kontejneru výhradně vlastněných a spravovaných klientem.

Koncept kontejnerů tenantů je hluboko v adresářové službě na všech vrstvách, od portálů po trvalé úložiště.

I v případě, že jsou metadata z více Azure Active Directory tenantů uložena na stejném fyzickém disku, neexistuje žádná relace mezi kontejnery, která je definována adresářovou službou, která je následně vyřízena správcem klienta.

### <a name="azure-role-based-access-control-rbac"></a>Access Control na základě rolí v Azure (RBAC)
Správa [na Access Control základě rolí v Azure (RBAC)](../../role-based-access-control/overview.md) vám pomůže sdílet různé komponenty dostupné v rámci předplatného Azure tím, že poskytuje jemně odstupňovanou správu přístupu pro Azure. Azure RBAC umožňuje oddělení povinností v rámci vaší organizace a udělení přístupu na základě toho, co uživatelé potřebují k provádění svých úloh. Místo udělení všech neomezených oprávnění v předplatném Azure nebo prostředcích můžete povolení jenom určitých akcí.

Azure RBAC má tři základní role, které se vztahují na všechny typy prostředků:

- **Vlastník** má úplný přístup ke všem prostředkům, včetně práva k delegování přístupu jiným uživatelům.

- **Přispěvatel** může vytvářet a spravovat všechny typy prostředků Azure, ale nemůže udělovat přístup ostatním.

- **Čtenář** si může zobrazit existující prostředky Azure.

![Access Control na základě rolí Azure](./media/isolation-choices/azure-isolation-fig3.png)

Zbývající role RBAC v Azure umožňují správu konkrétních prostředků Azure. Například role Přispěvatel virtuálních počítačů umožňuje uživateli vytvářet a spravovat virtuální počítače. Neuděluje jim přístup k Virtual Network Azure ani k podsíti, ke které se virtuální počítač připojuje.

[Předdefinované role RBAC](../../role-based-access-control/built-in-roles.md) uvádějí role dostupné v Azure. Určuje operace a rozsah, které jednotlivé předdefinované role udělují uživatelům. Pokud chcete definovat vlastní role pro ještě více ovládacích prvků, přečtěte si téma jak vytvořit [vlastní role v Azure RBAC](../../role-based-access-control/custom-roles.md).

Mezi další možnosti Azure Active Directory patří:
- Azure AD umožňuje jednotné přihlašování SaaS aplikacím bez ohledu na to, kde jsou hostované. Některé aplikace jsou federované pomocí Azure AD, jiné používají jednotné přihlašování pomocí hesla. Federované aplikace mohou také podporovat zřizování uživatelů a vytváření [hesel](https://www.techopedia.com/definition/31415/password-vault).

- Přístup k datům v rámci [Azure Storage](https://azure.microsoft.com/services/storage/) je řízen prostřednictvím ověřování. Každý účet úložiště má primární klíč ([klíč účtu úložiště](../../storage/common/storage-create-storage-account.md)nebo SAK) a sekundární tajný klíč (sdílený přístupový podpis nebo SAS).

- Azure AD poskytuje identitu jako službu prostřednictvím federace pomocí [Active Directory Federation Services (AD FS)](../../active-directory/hybrid/how-to-connect-fed-azure-adfs.md), synchronizace a replikace s místními adresáři.

- [Azure Multi-Factor Authentication](../../active-directory/authentication/multi-factor-authentication.md) je služba Multi-Factor Authentication, která vyžaduje, aby uživatelé ověřili přihlášení pomocí mobilní aplikace, telefonního hovoru nebo textové zprávy. Dá se použít s Azure AD k zabezpečení místních prostředků pomocí Azure Multi-Factor Authentication serveru a také s vlastními aplikacemi a adresáři pomocí sady SDK.

- [Azure AD Domain Services](https://azure.microsoft.com/services/active-directory-ds/) umožňuje připojit virtuální počítače Azure k doméně služby Active Directory bez nasazení řadičů domény. K těmto virtuálním počítačům se můžete přihlásit pomocí svých podnikových přihlašovacích údajů Active Directory a spravovat virtuální počítače připojené k doméně pomocí Zásady skupiny pro vymáhání standardních hodnot zabezpečení na všech virtuálních počítačích Azure.

- [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) poskytuje vysoce dostupnou službu pro správu globálních identit pro klientské aplikace, které se škálují na stovky milionů identit. Dá se integrovat do mobilních i webových platforem. Vaši uživatelé se můžou přihlásit ke všem aplikacím prostřednictvím přizpůsobitelného prostředí pomocí svých stávajících účtů na sociálních sítích nebo vytvořit přihlašovací údaje.

### <a name="isolation-from-microsoft-administrators--data-deletion"></a>Izolace od správců Microsoftu & odstranění dat
Společnost Microsoft používá silné míry, které chrání vaše data před neoprávněným přístupem nebo používáním neautorizovaných osob. Tyto provozní procesy a kontroly jsou zajištěné podmínkami pro [online služby](https://aka.ms/Online-Services-Terms), které nabízí smluvní závazky, které řídí přístup k vašim datům.

-   Technici Microsoftu nemají výchozí přístup k vašim datům v cloudu. Místo toho mají přístup v rámci správy pod dohledem oprávnění pouze v případě potřeby. Tento přístup se pečlivě kontroluje a protokoluje a odvolá se, když už nepotřebujete.

-   Společnost Microsoft může zajímat jiné společnosti, aby jejím jménem poskytovaly omezené služby. Subdodavatelé můžou získat přístup k zákaznickým datům jenom za účelem poskytování služeb, ke kterým jsme je přijali, a jejich používání je zakázáno pro žádné jiné účely. Kromě toho jsou smluvní strany vázány na zachování důvěrnosti informací o zákaznících.

Firemní služby s auditovanými certifikacemi, jako jsou ISO/IEC 27001, pravidelně ověřují společnost Microsoft a schválené auditory, které provádějí vzorové audity k ověření přístupu, a to jenom pro legitimní obchodní účely. K zákaznickým datům můžete kdykoli kdykoli přistupovat a z jakéhokoli důvodu.

Pokud odstraníte všechna data, Microsoft Azure data odstraní, včetně všech uložených v mezipaměti nebo záložních kopií. Pro služby v oboru se k odstranění dojde během 90 dnů po skončení doby uchování. (V části obory služeb jsou definovány služby pro zpracování dat v rámci [podmínek pro online služby](https://aka.ms/Online-Services-Terms).)

Pokud disková jednotka používaná pro úložiště má selhání hardwaru, je bezpečně [smazána nebo zničena](https://microsoft.com/trustcenter/privacy/you-own-your-data) předtím, než ji Microsoft vrátí výrobci pro nahrazení nebo opravu. Data na jednotce se přepíší, aby se zajistilo, že data nebude možné obnovit jakýmkoli způsobem.

## <a name="compute-isolation"></a>Izolace výpočtů
Microsoft Azure poskytuje různé cloudové výpočetní služby, které zahrnují rozsáhlou škálu výpočetních instancí & služeb, které se můžou automaticky škálovat a snížit, aby vyhovovaly potřebám vaší aplikace nebo podniku. Tato výpočetní instance a služba nabízí izolaci na více úrovních pro zabezpečení dat bez omezení flexibility v konfiguraci, kterou zákazníci požadují.

### <a name="isolated-virtual-machine-sizes"></a>Izolované velikosti virtuálních počítačů
Azure Compute nabízí velikosti virtuálních počítačů, které jsou izolované na konkrétní typ hardwaru a vyhrazené pro jednoho zákazníka.  Tyto velikosti virtuálních počítačů jsou nejvhodnější pro úlohy, které vyžadují vysoký stupeň izolace od ostatních zákazníků a kterých se týkají například požadavky na dodržování předpisů a zákonné požadavky.  Zákazníci si také můžou rozdělit prostředky těchto izolovaných virtuálních počítačů s využitím [podpory Azure pro vnořené virtuální počítače](https://azure.microsoft.com/blog/nested-virtualization-in-azure/).

Využitím izolované velikosti zaručujete, že váš virtuální počítač bude jediným operačním systémem, který běží na konkrétní instanci serveru.  Mezi aktuální nabídky izolovaných virtuálních počítačů patří:
* Standard_E64is_v3
* Standard_E64i_v3
* Standard_M128ms
* Standard_GS5
* Standard_G5
* Standard_DS15_v2
* Standard_D15_v2
* Standard_F72s_v2

Další informace o každé izolované velikosti, která je k dispozici, najdete [tady](../../virtual-machines/windows/sizes-memory.md).

### <a name="hyper-v--root-os-isolation-between-root-vm--guest-vms"></a>Hyper-V & izolaci kořenového operačního systému mezi kořenovým virtuálním počítačem & virtuálních počítačů hosta
Výpočetní platforma Azure je založená na virtualizaci počítačů – to znamená, že veškerý kód zákazníka se spouští ve virtuálním počítači Hyper-V. U každého uzlu Azure (nebo síťového koncového bodu) je k dispozici hypervisor, který běží přímo na hardwaru, a rozděluje uzel na proměnný počet hostů Virtual Machines (virtuálních počítačů).


![Hyper-V & izolaci kořenového operačního systému mezi kořenovým virtuálním počítačem & virtuálních počítačů hosta](./media/isolation-choices/azure-isolation-fig4.jpg)


Každý uzel má také jeden speciální kořenový virtuální počítač, který spouští hostitelský operační systém. Kritická hranice je izolace kořenového virtuálního počítače z virtuálních počítačů hosta a virtuálních počítačů hosta od sebe od sebe spravované hypervisorem a kořenovým operačním systémem. Párování hypervisoru/kořenového operačního systému využívá desítkové prostředí zabezpečení operačních systémů od Microsoftu a nabízí další novinky od technologie Hyper-V od společnosti Microsoft, která poskytuje silnou izolaci virtuálních počítačů hosta.

Platforma Azure používá virtualizované prostředí. Uživatelské instance pracují jako samostatné virtuální počítače, které nemají přístup k fyzickému hostitelskému serveru.

Hypervisor Azure funguje jako mikrojádro a předá požadavky na přístup k hardwaru z hostovaných virtuálních počítačů do hostitele ke zpracování pomocí rozhraní Shared-Memory s názvem VMBus. Tento postup brání uživatelům v získání přímého přístupu k systému pro čtení, zápis a spouštění a snižuje riziko sdílení systémových prostředků.

### <a name="advanced-vm-placement-algorithm--protection-from-side-channel-attacks"></a>Pokročilý algoritmus umístění virtuálních počítačů & ochrana před útoky na straně kanálu
Každý útok mezi virtuálními počítači se skládá ze dvou kroků: umístění virtuálního počítače ovládaného nežádoucí osoba na stejném hostiteli jako jeden z těchto virtuálních počítačů a následná porušení hranice izolace, aby buď ukrást citlivé informace o oběti, nebo ovlivnila výkon pro Greed nebo neoprávněné účely. Microsoft Azure poskytuje ochranu v obou krocích pomocí pokročilého algoritmu umístění virtuálních počítačů a ochrany před všemi útoky známých stran, včetně nehlučných sousedních počítačů.

### <a name="the-azure-fabric-controller"></a>Kontroler prostředků infrastruktury Azure
Kontroler prostředků infrastruktury Azure zodpovídá za přidělování prostředků infrastruktury klientským úlohám a spravuje jednosměrnou komunikaci z hostitele s virtuálními počítači. Virtuální počítač, ve kterém je řadič prostředků Azure Fabric, je vysoce sofistikovaný a skoro možná předpověď na úrovni fyzického hostitele.

![Kontroler prostředků infrastruktury Azure](./media/isolation-choices/azure-isolation-fig5.png)

Hypervisor Azure vynutil oddělení paměti a procesů mezi virtuálními počítači a bezpečně směruje síťový provoz do klientů hostovaného operačního systému. Tím se eliminuje možnost útoku na kanál na úrovni virtuálního počítače a na straně stran.

V Azure je kořenový virtuální počítač speciální: používá posílený operační systém, který se nazývá kořenový operační systém, který hostuje agenta prostředků infrastruktury (FA). V rámci služby Host operačních systémech na virtuálních počítačích zákazníků se používá v systému, ve kterém se dají spravovat agenti hosta (GA). A také umožňuje spravovat uzly úložiště.

Kolekce hypervisoru Azure, kořenového operačního systému/DM a virtuálních počítačů a prostředků zákazníka se skládá z výpočetního uzlu. Objekt, který se spravuje pomocí řadiče prostředků infrastruktury (FC), který existuje mimo výpočetní a úložné uzly (výpočetní prostředky a clustery úložiště jsou spravované pomocí samostatného programu FCs). Pokud zákazník aktualizuje konfigurační soubor aplikace, když je spuštěný, technologie FC komunikuje s FA, který pak kontaktuje na základě změny konfigurace. V případě selhání hardwaru bude FC automaticky najít dostupný hardware a restartovat virtuální počítač.

![Kontroler prostředků infrastruktury Azure](./media/isolation-choices/azure-isolation-fig6.jpg)

Komunikace mezi řadičem infrastruktury a agentem je jednosměrná. Agent implementuje službu chráněnou protokolem SSL, která reaguje jenom na žádosti z kontroleru. Nejde iniciovat připojení k řadiči nebo jiným privilegovaným interním uzlům. FC zpracovává všechny odpovědi, jako kdyby byly nedůvěryhodné.


![Kontroler prostředků infrastruktury](./media/isolation-choices/azure-isolation-fig7.png)

Izolace se rozšíří z kořenového virtuálního počítače z virtuálních počítačů hosta a virtuálních počítačů hosta od sebe navzájem. Výpočetní uzly jsou také izolované od uzlů úložiště pro zvýšení ochrany.


Hypervisor a operační systém hostitele poskytují filtry síťového paketu, které vám pomůžou zajistit, aby nedůvěryhodní virtuální počítače nemohly generovat falešný provoz nebo přijímaly provoz, který na ně neřeší, přímý provoz do koncových bodů chráněné infrastruktury nebo jejich odesílání a příjem. nevhodný přenos všesměrového vysílání.


### <a name="additional-rules-configured-by-fabric-controller-agent-to-isolate-vm"></a>Další pravidla konfigurovaná agentem řadiče prostředků infrastruktury k izolaci virtuálního počítače
Ve výchozím nastavení se při vytvoření virtuálního počítače zablokuje veškerý provoz a Agent kontroleru Fabric nakonfiguruje filtr paketů, aby přidal pravidla a výjimky, které povolují autorizovaný provoz.

Existují dvě kategorie pravidel, která jsou naprogramována:

-   **Konfigurace počítače nebo pravidla infrastruktury:** Ve výchozím nastavení je veškerá komunikace blokovaná. Existují výjimky, které umožní virtuálnímu počítači odesílat a přijímat přenosy DHCP a DNS. Virtuální počítače mohou také odesílat provoz do "veřejného" Internetu a odesílat provoz do jiných virtuálních počítačů v rámci stejného Virtual Network služby Azure a z aktivačního serveru operačního systému. Seznam virtuálních počítačů s povolenými odchozími cíli nezahrnuje podsítě směrovačů Azure, správu Azure a další vlastnosti Microsoftu.

-   **Konfigurační soubor role:** Tím se definuje příchozí Access Control seznamy (ACL) založené na modelu služby klienta.

### <a name="vlan-isolation"></a>Izolace sítě VLAN
V každém clusteru jsou tři sítě VLAN:

![Izolace sítě VLAN](./media/isolation-choices/azure-isolation-fig8.jpg)


-   Hlavní síť VLAN – propojení nedůvěryhodných zákaznických uzlů

-   SÍŤ VLAN s technologií FC – obsahuje důvěryhodné nástroje FCs a podpůrné systémy

-   SÍŤ VLAN zařízení – obsahuje důvěryhodnou síť a jiná zařízení infrastruktury

Komunikace je povolená z sítě VLAN FC k hlavní síti VLAN, ale nedá se iniciovat z hlavní sítě VLAN pro síť VLAN s technologií FC. Komunikace je taky blokovaná z hlavní sítě VLAN do sítě VLAN zařízení. To zaručuje, že i když dojde k ohrožení bezpečnosti uzlu se spuštěným kódem zákazníka, nemůže dojít k útoku na uzly v sítích typu FC nebo zařízení.

## <a name="storage-isolation"></a>Izolace úložiště
### <a name="logical-isolation-between-compute-and-storage"></a>Logická izolace mezi výpočetními a úložnými službami
V rámci svého základního návrhu Microsoft Azure odděluje výpočetní modul založený na virtuálních počítačích ze služby Storage. Toto oddělení umožňuje nezávisle škálovat výpočetní a úložné úložiště, což usnadňuje zajištění víceklientské architektury a izolace.

Proto Azure Storage spouští na samostatném hardwaru bez připojení k síti Azure COMPUTE s výjimkou logických. [To](https://msenterprise.global.ssl.fastly.net/vnext/PDFs/A01_AzureSecurityWhitepaper20160415c.pdf) znamená, že při vytvoření virtuálního disku není místo na disku přiděleno pro celou kapacitu. Místo toho se vytvoří tabulka, která mapuje adresy na virtuálním disku na oblasti na fyzickém disku a tato tabulka je zpočátku prázdná. **Když zákazník poprvé zapíše data na virtuálním disku, přidělí se místo na fyzickém disku a v tabulce se umístí ukazatel na něj.**
### <a name="isolation-using-storage-access-control"></a>Izolace pomocí řízení přístupu k úložišti
**Access Control v Azure Storage** má jednoduchý model řízení přístupu. Každé předplatné Azure může vytvořit jeden nebo více účtů úložiště. Každý účet úložiště má jeden tajný klíč, který se používá k řízení přístupu ke všem datům v daném účtu úložiště.

![Izolace pomocí řízení přístupu k úložišti](./media/isolation-choices/azure-isolation-fig9.png)

**Přístup k datům Azure Storage (včetně tabulek)** se dá řídit pomocí tokenu [SAS (sdíleného přístupového podpisu)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md) , který uděluje oborový přístup. SAS se vytvoří pomocí šablony dotazu (URL) podepsané pomocí [sak (klíč účtu úložiště)](https://msdn.microsoft.com/library/azure/ee460785.aspx). Tato [podepsaná adresa URL](../../storage/common/storage-dotnet-shared-access-signature-part-1.md) může být předána jinému procesu (to znamená delegovaný), který pak může vyplnit podrobnosti dotazu a vytvořit požadavek služby úložiště. SAS umožňuje udělit klientům přístup na základě času bez odhalení tajného klíče účtu úložiště.

SAS znamená, že můžeme udělit omezená oprávnění klienta k objektům v našem účtu úložiště po určitou dobu a se zadanou sadou oprávnění. Tato omezená oprávnění můžeme udělit bez nutnosti sdílení přístupových klíčů k vašemu účtu.

### <a name="ip-level-storage-isolation"></a>Izolace úložiště na úrovni protokolu IP
Můžete navázat brány firewall a definovat rozsah IP adres pro důvěryhodné klienty. S rozsahem IP adres se můžou [Azure Storage](../../storage/common/storage-security-guide.md)připojit jenom klienti, kteří mají IP adresu v rámci definovaného rozsahu.

Data úložiště IP je možné chránit před neautorizovanými uživateli prostřednictvím síťového mechanismu, který se používá k přidělení vyhrazeného nebo vyhrazeného tunelového přenosu do úložiště IP.

### <a name="encryption"></a>Šifrování
Azure nabízí pro ochranu dat následující typy šifrování:
-   Šifrování při přenosu

-   Šifrování v klidovém stavu

#### <a name="encryption-in-transit"></a>Šifrování při přenosu
Šifrování při přenosu je mechanismus ochrany dat při přenosu mezi sítěmi. Pomocí Azure Storage můžete zabezpečit data pomocí:

-   [Šifrování na úrovni přenosu](../../storage/common/storage-security-guide.md), jako je například https při přenosu dat do nebo z Azure Storage.

-   [Šifrování kabelů](../../storage/common/storage-security-guide.md#using-encryption-during-transit-with-azure-file-shares), jako je například šifrování SMB 3,0 pro sdílené složky Azure.

-   [Šifrování na straně klienta](../../storage/common/storage-security-guide.md), aby se data zašifroval před přenosem do úložiště a dešifrují data poté, co se přenesou z úložiště.

#### <a name="encryption-at-rest"></a>Šifrování v klidovém umístění
V případě mnoha organizací je [šifrování dat v klidovém](isolation-choices.md) případě povinný krok k ochraně dat, dodržování předpisů a suverenity dat. Existují tři funkce Azure, které poskytují šifrování dat v klidovém znění:

-   [Šifrování služby Storage](../../storage/common/storage-security-guide.md) umožňuje požádat, aby služba úložiště při zápisu do Azure Storage automaticky zašifroval data.

-   [Šifrování na straně klienta](../../storage/common/storage-security-guide.md) také poskytuje funkci šifrování v klidovém umístění.

-   [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) slouží k šifrování disků s operačním systémem a datových disků, které používá virtuální počítač s IaaS.

#### <a name="azure-disk-encryption"></a>Azure Disk Encryption
[Azure Disk Encryption](../azure-security-disk-encryption-overview.md) pro virtuální počítače pomáhá řešit požadavky organizace na zabezpečení a dodržování předpisů tím, že šifruje disky virtuálních počítačů (včetně spouštěcích a datových disků) pomocí klíčů a zásad, které řídíte v [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).

Řešení pro šifrování disků pro Windows je založené na [Microsoft nástroj BitLocker Drive Encryption](https://technet.microsoft.com/library/cc732774.aspx)a řešení pro Linux je založené na [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt).

Řešení podporuje následující scénáře pro virtuální počítače s IaaS, když jsou povolené v Microsoft Azure:
-   Integrace s Azure Key Vault

-   Virtuální počítače úrovně Standard: Virtuální počítače řady a, D, DS, G, GS a tak dále, Series IaaS

-   Povolení šifrování na virtuálních počítačích s Windows a Linux IaaS

-   Zakázání šifrování v operačním systému a datových jednotkách pro virtuální počítače s Windows IaaS

-   Zakázání šifrování u datových jednotek pro virtuální počítače s IaaS Linux

-   Povolení šifrování na virtuálních počítačích s IaaS, na kterých běží klientský operační systém Windows

-   Povolení šifrování u svazků s cestami připojení

-   Povolení šifrování pro virtuální počítače se systémem Linux, které jsou konfigurovány pomocí diskového disku RAID, pomocí [mdadm](https://en.wikipedia.org/wiki/Mdadm)

-   Povolení šifrování pro virtuální počítače se systémem Linux pomocí [LVM (Správce logických svazků)](https://msdn.microsoft.com/library/windows/desktop/bb540532) pro datové disky

-   Povolení šifrování u virtuálních počítačů s Windows nakonfigurovaných pomocí prostorů úložiště

-   Podporují se všechny veřejné oblasti Azure.

Řešení nepodporuje následující scénáře, funkce a technologie v této verzi:

-   Virtuální počítače IaaS úrovně Basic

-   Zakázání šifrování na jednotce operačního systému pro virtuální počítače s IaaS Linux

-   IaaS virtuální počítače, které jsou vytvořené pomocí metody vytváření virtuálních počítačů Classic

-   Integrace s místní službou správy klíčů

-   Soubory Azure (Shared File System), systém souborů NFS (Network File System), dynamické svazky a virtuální počítače s Windows, které jsou nakonfigurované s využitím softwarových systémů RAID

## <a name="sql-azure-database-isolation"></a>SQL Azure izolaci databáze
SQL Database je služba v cloudu Microsoftu poskytující relační databáze založené na předním databázovém stroji Microsoft SQL Server a schopné obsloužit i klíčové úlohy. SQL Database nabízí předvídatelné izolaci dat na úrovni účtu, geografickou oblast a oblast založenou na síti – to vše s téměř nulovou správou.

### <a name="sql-azure-application-model"></a>SQL Azure aplikační model

[SQL Azure Microsoftu](../../sql-database/sql-database-single-database-get-started.md) Database je cloudová služba relačních databází založená na SQL Server technologiích. Poskytuje vysoce dostupnou a škálovatelnou databázovou službu pro více tenantů, která je hostována Microsoftem v cloudu.

Z perspektivy aplikace SQL Azure poskytuje následující hierarchii: Každá úroveň má omezení 1: n níže.

![SQL Azure aplikační model](./media/isolation-choices/azure-isolation-fig10.png)

Účet a předplatné jsou Microsoft Azure konceptech platforem pro přidružení fakturace a správy.

Logické servery a databáze jsou SQL Azure specifických konceptech a jsou spravované pomocí SQL Azure, poskytovaných rozhraními OData a TSQL nebo prostřednictvím portálu SQL Azure, který je integrovaný do Azure Portal.

SQL Azure servery nejsou fyzické nebo instance virtuálních počítačů, místo toho se jedná o kolekce databází, správu sdílení a zásady zabezpečení, které jsou uložené v, označované jako "logická hlavní" databáze.

![SQL Azure](./media/isolation-choices/azure-isolation-fig11.png)

Mezi logické hlavní databáze patří:

-   Přihlášení SQL používaná k připojení k serveru

-   Pravidla brány firewall

Informace o fakturaci a využití pro databáze SQL Azure ze stejného logického serveru nejsou zaručené ve stejné fyzické instanci v clusteru SQL Azure, místo toho při připojování musí aplikace zadat název cílové databáze.

V perspektivě zákazníka se logický Server vytvoří v geograficky grafické oblasti, přičemž skutečné vytvoření serveru se stane v jednom z clusterů v této oblasti.

### <a name="isolation-through-network-topology"></a>Izolace prostřednictvím síťové topologie

Když se vytvoří logický Server a zaregistruje se jeho název DNS, bude název DNS ukazovat na adresu "virtuální IP adresa brány" v konkrétním datovém centru, kde byl server umístěn.

Za VIP (virtuální IP adresa) máme kolekci bezstavových služeb brány. Obecně platí, že brány se dostanou, když je potřeba koordinace mezi několika zdroji dat (hlavní databáze, uživatelská databáze atd.). Služby brány implementují toto:
-   **Proxy připojení TDS.** To zahrnuje vyhledání uživatelské databáze v clusteru back-end, implementaci sekvence přihlášení a přesměrování paketů TDS do back-endu a back-endu.

-   **Správa databází.** To zahrnuje implementaci kolekce pracovních postupů pro vytváření, změny a ukládání databázových operací. Databázové operace mohou být vyvolány buď pomocí sledování paketů TDS nebo explicitních rozhraní API OData.

-   Vytvoření, změna nebo zrušení operací přihlášení/uživatele

-   Operace správy logických serverů přes rozhraní OData API

![Izolace prostřednictvím síťové topologie](./media/isolation-choices/azure-isolation-fig12.png)

Vrstva za bránami se nazývá "back-end". To je místo, kde jsou všechna data uložena ve vysoce dostupném způsobem. Každá část dat je označována jako patřící do oddílu "partition" nebo "jednotka převzetí služeb při selhání", přičemž každý z nich má alespoň tři repliky. Repliky se ukládají a replikují pomocí modulu SQL Server a spravují se systémem převzetí služeb při selhání, který se často označuje jako "prostředky infrastruktury".

Obecně platí, že back-end systém nekomunikuje s jinými systémy jako bezpečnostní opatření. To je vyhrazeno pro systémy na front-endové úrovni (brány). Počítače vrstvy brány mají omezená oprávnění na back-endové počítače, aby se minimalizoval prostor pro útoky jako důkladný mechanismus pro útok.

### <a name="isolation-by-machine-function-and-access"></a>Izolace podle funkce počítače a přístupu
SQL Azure (se skládá ze služeb spuštěných v různých funkcích počítače). SQL Azure je rozdělené do cloudové databáze back-endu a "front-end" (brány/správy), přičemž obecný princip provozu směřuje jenom do back-endu. Prostředí front-end může komunikovat s vnějším světem jiných služeb a obecně má pouze omezená oprávnění v back-endu (dostatečně pro volání vstupních bodů, které musí vyvolat).

## <a name="networking-isolation"></a>Izolace sítě
Nasazení Azure má několik vrstev izolace sítě. Následující diagram znázorňuje různé vrstvy izolace sítě, které Azure poskytuje zákazníkům. Tyto vrstvy se nativně nacházejí v rámci samotné platformy Azure i v uživatelsky definovaných funkcích. Díky příchozímu přenosu z Internetu poskytuje Azure DDoS izolaci proti útokům ve velkém měřítku proti Azure. Další izolovanou vrstvou jsou uživatelsky definované veřejné IP adresy (koncové body), které slouží k určení provozu, který může projít cloudovou službou do virtuální sítě. Nativní izolace virtuální sítě Azure zajišťuje kompletní izolaci ze všech ostatních sítí a přenos dat pouze prostřednictvím uživatelem nakonfigurovaných cest a metod. Tyto cesty a metody jsou další vrstvou, kde skupin zabezpečení sítě, UDR a síťová virtuální zařízení je možné použít k vytvoření hranic izolace pro ochranu nasazení aplikací v chráněné síti.

![Izolace sítě](./media/isolation-choices/azure-isolation-fig13.png)

**Izolace provozu:** [Virtuální síť](../../virtual-network/virtual-networks-overview.md) je hranice izolace provozu na platformě Azure. Virtuální počítače v jedné virtuální síti nemůžou komunikovat přímo s virtuálními počítači v jiné virtuální síti, a to i v případě, že oba virtuální sítě vytvoří stejný zákazník. Izolace je kritická vlastnost, která zajišťuje, že virtuální počítače a komunikace zákazníků zůstávají privátní v rámci virtuální sítě.

[Podsíť](../../virtual-network/virtual-networks-overview.md) nabízí další vrstvu izolace ve virtuální síti založenou na rozsahu IP adres. IP adresy ve virtuální síti můžete pro organizaci a zabezpečení rozdělit virtuální síť do několika podsítí. Virtuální počítače a instance rolí PaaS nasazené do podsítí (stejných nebo různých) v rámci jedné virtuální sítě můžou navzájem komunikovat bez jakékoli další konfigurace. [Skupinu zabezpečení sítě (skupin zabezpečení sítě)](../../virtual-network/virtual-networks-overview.md) můžete také nakonfigurovat tak, aby povolovala nebo odmítala síťový provoz do instance virtuálního počítače na základě pravidel nakonfigurovaných v seznamu řízení přístupu (ACL) NSG. Skupiny NSG můžou být přidružené buď k podsítím, nebo k jednotlivým instancím virtuálních počítačů v této podsíti. Pokud je skupina zabezpečení sítě přidružená k podsíti, pravidla seznamu ACL platí pro všechny instance virtuálních počítačů v této podsíti.

## <a name="next-steps"></a>Další kroky

- [Možnosti izolace sítě pro počítače ve virtuálních sítích Windows Azure](https://azure.microsoft.com/blog/network-isolation-options-for-machines-in-windows-azure-virtual-networks/)

To zahrnuje Klasický scénář front-endu a back-endu, ve kterém můžou počítače v určité back-mailové síti nebo v podsítí povolit jenom určité klienty nebo jiné počítače, aby se připojili ke konkrétnímu koncovému bodu na základě seznamu povolených IP adres.

- [Izolace výpočtů](https://msenterprise.global.ssl.fastly.net/vnext/PDFs/A01_AzureSecurityWhitepaper20160415c.pdf)

Microsoft Azure poskytuje různé cloudové výpočetní služby, které zahrnují rozsáhlou škálu výpočetních instancí & služeb, které se můžou automaticky škálovat a snížit tak, aby vyhovovaly potřebám vaší aplikace nebo podniku.

- [Izolace úložiště](https://msenterprise.global.ssl.fastly.net/vnext/PDFs/A01_AzureSecurityWhitepaper20160415c.pdf)

Microsoft Azure odděluje výpočet založený na virtuálním počítači od zákazníka od úložiště. Toto oddělení umožňuje nezávisle škálovat výpočetní a úložné úložiště, což usnadňuje zajištění víceklientské architektury a izolace. Proto Azure Storage spouští na samostatném hardwaru bez připojení k síti Azure COMPUTE s výjimkou logických. Všechny požadavky běží přes HTTP nebo HTTPS na základě výběru zákazníka.

