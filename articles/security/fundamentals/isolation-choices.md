---
title: Izolace ve veřejném cloudu Azure | Microsoft Docs
description: Přečtěte si, jak Azure poskytuje izolaci proti uživatelům se zlými úmysly i bez škodlivých uživatelů a nabízí různé možnosti izolace pro architekty.
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
ms.openlocfilehash: c06fb0830ae709918b668ed60efbaaf47a63ce84
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/18/2020
ms.locfileid: "94842834"
---
# <a name="isolation-in-the-azure-public-cloud"></a>Izolace ve veřejném cloudu Azure

Azure umožňuje spouštět aplikace a virtuální počítače na sdílené fyzické infrastruktuře. Jednou z primárních motivů pro spouštění aplikací v cloudovém prostředí je schopnost distribuovat náklady na sdílené prostředky mezi více zákazníků. Tento postup víceklientské architektury vylepšuje efektivitu díky multiplexování prostředků mezi různými zákazníky s nízkými náklady. Bohužel taky představuje riziko sdílení fyzických serverů a dalších prostředků infrastruktury ke spouštění citlivých aplikací a virtuálních počítačů, které můžou patřit k libovolnému a potenciálně škodlivému uživateli.

Tento článek popisuje, jak Azure poskytuje izolaci proti zlomyslným i nebezpečným uživatelům a slouží jako vodítko pro navrhování cloudových řešení tím, že nabízí různé možnosti izolace architektům.

## <a name="tenant-level-isolation"></a>Izolace na úrovni tenanta

Jednou z hlavních výhod cloud computingu je koncept sdílené a běžné infrastruktury napříč mnoha zákazníky současně, což vede k úsporám rozsahu. Tento koncept se nazývá víceklientská architektura. Microsoft pracuje nepřetržitě, aby se zajistilo, že architektura Microsoft Cloud Azure pro více tenantů podporuje zabezpečení, důvěrnost, ochranu osobních údajů, integritu a dostupnost.

V prostředí cloudu se dá klient služby Azure AD definovat jako klient nebo organizace, která vlastní a spravuje konkrétní instanci dané cloudové služby. S platformou identity, kterou poskytuje Microsoft Azure, je tenant jenom vyhrazená instance Azure Active Directory (Azure AD), kterou vaše organizace obdrží a vlastní, když se přihlásí ke cloudové službě Microsoftu.

Každý adresář služby Azure AD je oddělený od ostatních adresářů služby Azure AD. Adresář služby Azure AD byl navržen tak, aby se jednalo o zabezpečený prostředek k použití pouze pro vaši organizaci – stejně jako je podniková kancelářská budova zabezpečeným prostředkem výhradně vaší organizace. Architektura služby Azure AD znemožňuje míchání dat zákazníků a informací o identitě. To znamená, že se uživatelé a správci jednoho adresáře služby Azure AD nemohou dostat – ať už omylem nebo záměrně – k datům v jiném adresáři.

### <a name="azure-tenancy"></a>Architektura Azure

Azure tenant (předplatné Azure) odkazuje na vztah "Customer/fakturace" a jedinečného [tenanta](../../active-directory/develop/quickstart-create-new-tenant.md) v [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md). Izolace na úrovni tenanta v Microsoft Azure se dosahuje pomocí Azure Active Directory a [řízení přístupu na základě role v Azure](../../role-based-access-control/overview.md) , které nabízí. Každé předplatné Azure je přidruženo k jednomu Azure Active Directory (AD) adresáři.

Uživatelé, skupiny a aplikace z tohoto adresáře mohou spravovat prostředky v rámci předplatného Azure. Tato přístupová práva můžete přiřadit pomocí Azure Portal, nástrojů příkazového řádku Azure a rozhraní API pro správu Azure. Tenant Azure AD je logicky izolovaný pomocí hranic zabezpečení, takže žádný zákazník nemůže získat přístup k spoluklientům nebo ho ohrozit, ať už škodlivě, nebo omylem. Služba Azure AD běží na holých serverech, které jsou izolované na odděleném segmentu sítě, kde jsou filtrování paketů na úrovni hostitele a brána Windows Firewall blokuje nežádoucí připojení a provoz.

- Přístup k datům ve službě Azure AD vyžaduje ověření uživatele prostřednictvím služby tokenu zabezpečení (STS). Informace o existenci, povoleném stavu a roli uživatele používá autorizační systém k určení, jestli je požadovaný přístup k cílovému tenantovi autorizovaný pro tohoto uživatele v této relaci.

![Architektura Azure](./media/isolation-choices/azure-isolation-fig1.png)

- Klienti jsou diskrétní kontejnery a mezi nimi neexistuje žádný vztah.

- Bez přístupu mezi klienty, pokud ho správce tenanta neudělí prostřednictvím federace nebo zřizování uživatelských účtů z jiných tenantů.

- Fyzický přístup k serverům, které tvoří službu Azure AD a má přímý přístup k systémům back-end služby Azure AD, je omezený.

- Uživatelé Azure AD nemají přístup k fyzickým prostředkům nebo umístěním, a proto není možné obejít kontrolu logických zásad Azure RBAC, které jsou uvedené níže.

V případě potřeby diagnostiky a údržby je provozní model, který využívá systém zvýšení oprávnění za běhu, vyžadován a používán. Azure AD Privileged Identity Management (PIM) zavádí koncept oprávněného správce. oprávnění [Správci](../../active-directory/privileged-identity-management/pim-configure.md) by měli být uživatelé, kteří potřebují privilegovaný přístup nyní, ale ne každý den. Dokud uživatel nepotřebuje přístup, je tato role neaktivní. Jakmile uživatel bude přístup potřebovat, dokončí proces aktivace a na předem určenou dobu se stane aktivním správcem.

![Azure AD Privileged Identity Management](./media/isolation-choices/azure-isolation-fig2.png)

Azure Active Directory hostuje každého tenanta ve vlastním chráněném kontejneru, pomocí zásad a oprávnění pro a v rámci kontejneru výhradně vlastněných a spravovaných klientem.

Koncept kontejnerů tenantů je hluboko v adresářové službě na všech vrstvách, od portálů po trvalé úložiště.

I v případě, že jsou metadata z více Azure Active Directory tenantů uložena na stejném fyzickém disku, neexistuje žádná relace mezi kontejnery, která je definována adresářovou službou, která je následně vyřízena správcem klienta.

### <a name="azure-role-based-access-control-azure-rbac"></a>Řízení přístupu na základě role Azure (Azure RBAC)

[Řízení přístupu na základě role v Azure (Azure RBAC)](../../role-based-access-control/overview.md) pomáhá sdílet různé komponenty dostupné v rámci předplatného Azure tím, že poskytuje jemně odstupňovanou správu přístupu pro Azure. Azure RBAC umožňuje oddělení povinností v rámci vaší organizace a udělení přístupu na základě toho, co uživatelé potřebují k provádění svých úloh. Místo udělení všech neomezených oprávnění v předplatném Azure nebo prostředcích můžete povolení jenom určitých akcí.

Azure RBAC má tři základní role, které se vztahují na všechny typy prostředků:

- **Vlastník** má úplný přístup ke všem prostředkům, včetně práva k delegování přístupu jiným uživatelům.

- **Přispěvatel** může vytvářet a spravovat všechny typy prostředků Azure, ale nemůže udělovat přístup ostatním.

- **Čtenář** si může zobrazit existující prostředky Azure.

![Řízení přístupu na základě role Azure (Azure RBAC)](./media/isolation-choices/azure-isolation-fig3.png)

Zbývající role Azure v Azure umožňují správu konkrétních prostředků Azure. Role Přispěvatel virtuálních počítačů například uživateli umožňuje vytvářet a spravovat virtuální počítače. Neuděluje jim přístup k Virtual Network Azure ani k podsíti, ke které se virtuální počítač připojuje.

[Předdefinované role Azure](../../role-based-access-control/built-in-roles.md) uvádějí role dostupné v Azure. Určuje operace a rozsah, které jednotlivé předdefinované role udělují uživatelům. Pokud chcete definovat vlastní role pro ještě více ovládacích prvků, přečtěte si téma jak vytvořit [vlastní role v Azure RBAC](../../role-based-access-control/custom-roles.md).

Mezi další možnosti Azure Active Directory patří:

- Azure AD podporuje jednotné přihlašování (SSO) k aplikacím SaaS bez ohledu na to, kde jsou hostované. Některé aplikace jsou federované pomocí Azure AD, jiné používají jednotné přihlašování pomocí hesla. Federované aplikace mohou také podporovat zřizování uživatelů a vytváření [hesel](https://www.techopedia.com/definition/31415/password-vault).

- Přístup k datům v rámci [Azure Storage](https://azure.microsoft.com/services/storage/) je řízen prostřednictvím ověřování. Každý účet úložiště má primární klíč ([klíč účtu úložiště](../../storage/common/storage-account-create.md)nebo SAK) a sekundární tajný klíč (sdílený přístupový podpis nebo SAS).

- Azure AD poskytuje identitu jako službu prostřednictvím federace pomocí [Active Directory Federation Services (AD FS)](/windows-server/identity/ad-fs/deployment/how-to-connect-fed-azure-adfs), synchronizace a replikace s místními adresáři.

- [Azure AD Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md) je služba Multi-Factor Authentication, která vyžaduje, aby uživatelé ověřili přihlášení pomocí mobilní aplikace, telefonního hovoru nebo textové zprávy. Dá se použít s Azure AD k zabezpečení místních prostředků pomocí serveru Azure Multi-Factor Authentication a také s vlastními aplikacemi a adresáři pomocí sady SDK.

- [Azure AD Domain Services](https://azure.microsoft.com/services/active-directory-ds/) umožňuje připojit virtuální počítače Azure k doméně služby Active Directory bez nasazení řadičů domény. K těmto virtuálním počítačům se můžete přihlásit pomocí svých podnikových přihlašovacích údajů Active Directory a spravovat virtuální počítače připojené k doméně pomocí Zásady skupiny pro vymáhání standardních hodnot zabezpečení na všech virtuálních počítačích Azure.

- [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) poskytuje vysoce dostupnou službu pro správu globálních identit pro klientské aplikace, které se škálují na stovky milionů identit. Dá se integrovat do mobilních i webových platforem. Vaši uživatelé se můžou přihlásit ke všem aplikacím prostřednictvím přizpůsobitelného prostředí pomocí svých stávajících účtů na sociálních sítích nebo vytvořit přihlašovací údaje.

### <a name="isolation-from-microsoft-administrators--data-deletion"></a>Izolace od správců Microsoftu & odstranění dat

Společnost Microsoft používá silné míry, které chrání vaše data před neoprávněným přístupem nebo používáním neautorizovaných osob. Tyto provozní procesy a kontroly jsou zajištěné podmínkami pro [online služby](https://aka.ms/Online-Services-Terms), které nabízí smluvní závazky, které řídí přístup k vašim datům.

- Technici Microsoftu nemají výchozí přístup k vašim datům v cloudu. Místo toho mají přístup v rámci správy pod dohledem oprávnění pouze v případě potřeby. Tento přístup se pečlivě kontroluje a protokoluje a odvolá se, když už nepotřebujete.
- Společnost Microsoft může zajímat jiné společnosti, aby jejím jménem poskytovaly omezené služby. Subdodavatelé můžou získat přístup k zákaznickým datům jenom za účelem poskytování služeb, ke kterým jsme je přijali, a jejich používání je zakázáno pro žádné jiné účely. Kromě toho jsou smluvní strany vázány na zachování důvěrnosti informací o zákaznících.

Firemní služby s auditovanými certifikacemi, jako jsou ISO/IEC 27001, pravidelně ověřují společnost Microsoft a schválené auditory, které provádějí vzorové audity k ověření přístupu, a to jenom pro legitimní obchodní účely. K zákaznickým datům můžete kdykoli kdykoli přistupovat a z jakéhokoli důvodu.

Pokud odstraníte všechna data, Microsoft Azure data odstraní, včetně všech uložených v mezipaměti nebo záložních kopií. Pro služby v oboru se k odstranění dojde během 90 dnů po skončení doby uchování. (V části obory služeb jsou definovány služby pro zpracování dat v rámci [podmínek pro online služby](https://aka.ms/Online-Services-Terms).)

Pokud disková jednotka používaná pro úložiště má selhání hardwaru, je bezpečně [smazána nebo zničena](https://microsoft.com/trustcenter/privacy/you-own-your-data) předtím, než ji Microsoft vrátí výrobci pro nahrazení nebo opravu. Data na jednotce se přepíší, aby se zajistilo, že data nebude možné obnovit jakýmkoli způsobem.

## <a name="compute-isolation"></a>Izolace výpočtů

Microsoft Azure poskytuje různé cloudové výpočetní služby, které zahrnují rozsáhlou škálu výpočetních instancí & služeb, které se můžou automaticky škálovat a snížit, aby vyhovovaly potřebám vaší aplikace nebo podniku. Tato výpočetní instance a služba nabízí izolaci na více úrovních pro zabezpečení dat bez omezení flexibility v konfiguraci, kterou zákazníci požadují.

### <a name="isolated-virtual-machine-sizes"></a>Izolované velikosti virtuálních počítačů

[!INCLUDE [virtual-machines-common-isolation](../../../includes/virtual-machines-common-isolation.md)]

### <a name="dedicated-hosts"></a>Vyhrazení hostitelé

Kromě izolovaných hostitelů popsaných v předchozí části nabízí Azure také vyhrazené hostitele. Vyhrazení hostitelé v Azure je služba, která poskytuje fyzické servery, které můžou hostovat jeden nebo víc virtuálních počítačů a které jsou vyhrazené pro jedno předplatné Azure. Vyhrazení hostitelé poskytují izolaci hardwaru na úrovni fyzického serveru. Do hostitelů nebudou umístěny žádné další virtuální počítače. Vyhrazení hostitelé se nasazují ve stejných datových centrech a sdílejí stejnou síť a základní infrastrukturu úložiště jako ostatní, neizolované hostitele. Další informace najdete v podrobném přehledu [vyhrazených hostitelů Azure](../../virtual-machines/dedicated-hosts.md).

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

V Azure je kořenový virtuální počítač speciální: používá posílený operační systém, který se nazývá kořenový operační systém, který hostuje agenta prostředků infrastruktury (FA). V případě, že se v hostovaných operačních systémech na virtuálních počítačích zákazníků používají služby hosta (GA), používá se ve funkci. A také umožňuje spravovat uzly úložiště.

Kolekce hypervisoru Azure, kořenového operačního systému/DM a virtuálních počítačů a prostředků zákazníka se skládá z výpočetního uzlu. Objekt, který se spravuje pomocí řadiče prostředků infrastruktury (FC), který existuje mimo výpočetní a úložné uzly (výpočetní prostředky a clustery úložiště jsou spravované pomocí samostatného programu FCs). Pokud zákazník aktualizuje konfigurační soubor aplikace, když je spuštěný, technologie FC komunikuje s FA, který pak kontaktuje na základě změny konfigurace. V případě selhání hardwaru bude FC automaticky najít dostupný hardware a restartovat virtuální počítač.

![Kontroler prostředků infrastruktury Azure](./media/isolation-choices/azure-isolation-fig6.jpg)

Komunikace mezi řadičem infrastruktury a agentem je jednosměrná. Agent implementuje službu chráněnou protokolem SSL, která reaguje jenom na žádosti z kontroleru. Nejde iniciovat připojení k řadiči nebo jiným privilegovaným interním uzlům. FC zpracovává všechny odpovědi, jako kdyby byly nedůvěryhodné.

![Kontroler prostředků infrastruktury](./media/isolation-choices/azure-isolation-fig7.png)

Izolace se rozšíří z kořenového virtuálního počítače z virtuálních počítačů hosta a virtuálních počítačů hosta od sebe navzájem. Výpočetní uzly jsou také izolované od uzlů úložiště pro zvýšení ochrany.

Hypervisor a hostitelský operační systém poskytují filtry síťového paketu, které vám pomůžou zajistit, aby nedůvěryhodní virtuální počítače nemohly vygenerovat falešný provoz nebo přijímat provoz, který se na ně nevztahují, přímý provoz na koncové body chráněné infrastruktury nebo odesílali a přijímali nevhodný provoz všesměrového vysílání.

### <a name="additional-rules-configured-by-fabric-controller-agent-to-isolate-vm"></a>Další pravidla konfigurovaná agentem řadiče prostředků infrastruktury k izolaci virtuálního počítače

Ve výchozím nastavení se při vytvoření virtuálního počítače zablokuje veškerý provoz a Agent kontroleru Fabric nakonfiguruje filtr paketů, aby přidal pravidla a výjimky, které povolují autorizovaný provoz.

Existují dvě kategorie pravidel, která jsou naprogramována:

- **Konfigurace počítače nebo pravidla infrastruktury:** Ve výchozím nastavení je veškerá komunikace blokovaná. Existují výjimky, které umožní virtuálnímu počítači odesílat a přijímat přenosy DHCP a DNS. Virtuální počítače mohou také odesílat provoz do "veřejného" Internetu a odesílat provoz do jiných virtuálních počítačů v rámci stejného Virtual Network služby Azure a z aktivačního serveru operačního systému. Seznam virtuálních počítačů s povolenými odchozími cíli nezahrnuje podsítě směrovačů Azure, správu Azure a další vlastnosti Microsoftu.
- **Konfigurační soubor role:** Tím se definuje příchozí Access Control seznamy (ACL) založené na modelu služby klienta.

### <a name="vlan-isolation"></a>Izolace sítě VLAN

V každém clusteru jsou tři sítě VLAN:

![Izolace sítě VLAN](./media/isolation-choices/azure-isolation-fig8.jpg)

- Hlavní síť VLAN – propojení nedůvěryhodných zákaznických uzlů
- SÍŤ VLAN s technologií FC – obsahuje důvěryhodné nástroje FCs a podpůrné systémy
- SÍŤ VLAN zařízení – obsahuje důvěryhodnou síť a jiná zařízení infrastruktury

Komunikace je povolená z sítě VLAN FC k hlavní síti VLAN, ale nedá se iniciovat z hlavní sítě VLAN pro síť VLAN s technologií FC. Komunikace je taky blokovaná z hlavní sítě VLAN do sítě VLAN zařízení. To zaručuje, že i když dojde k ohrožení bezpečnosti uzlu se spuštěným kódem zákazníka, nemůže dojít k útoku na uzly v sítích typu FC nebo zařízení.

## <a name="storage-isolation"></a>Izolace úložiště

### <a name="logical-isolation-between-compute-and-storage"></a>Logická izolace mezi výpočetními a úložnými službami

V rámci svého základního návrhu Microsoft Azure odděluje výpočetní modul založený na virtuálních počítačích ze služby Storage. Toto oddělení umožňuje nezávisle škálovat výpočetní a úložné úložiště, což usnadňuje zajištění víceklientské architektury a izolace.

Proto Azure Storage spouští na samostatném hardwaru bez připojení k síti Azure COMPUTE s výjimkou logických. To znamená, že při vytvoření virtuálního disku není místo na disku přiděleno pro celou kapacitu. Místo toho se vytvoří tabulka, která mapuje adresy na virtuálním disku na oblasti na fyzickém disku a tato tabulka je zpočátku prázdná. **Když zákazník poprvé zapíše data na virtuálním disku, přidělí se místo na fyzickém disku a v tabulce se umístí ukazatel na něj.**

### <a name="isolation-using-storage-access-control"></a>Izolace pomocí řízení přístupu k úložišti

**Access Control v Azure Storage** má jednoduchý model řízení přístupu. Každé předplatné Azure může vytvořit jeden nebo více účtů úložiště. Každý účet úložiště má jeden tajný klíč, který se používá k řízení přístupu ke všem datům v daném účtu úložiště.

![Izolace pomocí řízení přístupu k úložišti](./media/isolation-choices/azure-isolation-fig9.png)

**Přístup k datům Azure Storage (včetně tabulek)** se dá řídit pomocí tokenu [SAS (sdíleného přístupového podpisu)](../../storage/common/storage-sas-overview.md) , který uděluje oborový přístup. SAS se vytvoří pomocí šablony dotazu (URL) podepsané pomocí [sak (klíč účtu úložiště)](/previous-versions/azure/reference/ee460785(v=azure.100)). Tato [podepsaná adresa URL](../../storage/common/storage-sas-overview.md) může být předána jinému procesu (to znamená delegovaný), který pak může vyplnit podrobnosti dotazu a vytvořit požadavek služby úložiště. SAS umožňuje udělit klientům přístup na základě času bez odhalení tajného klíče účtu úložiště.

SAS znamená, že můžeme udělit omezená oprávnění klienta k objektům v našem účtu úložiště po určitou dobu a se zadanou sadou oprávnění. Tato omezená oprávnění můžeme udělit bez nutnosti sdílení přístupových klíčů k vašemu účtu.

### <a name="ip-level-storage-isolation"></a>Izolace úložiště na úrovni protokolu IP

Můžete navázat brány firewall a definovat rozsah IP adres pro důvěryhodné klienty. S rozsahem IP adres se můžou [Azure Storage](../../storage/blobs/security-recommendations.md)připojit jenom klienti, kteří mají IP adresu v rámci definovaného rozsahu.

Data úložiště IP je možné chránit před neautorizovanými uživateli prostřednictvím síťového mechanismu, který se používá k přidělení vyhrazeného nebo vyhrazeného tunelového přenosu do úložiště IP.

### <a name="encryption"></a>Šifrování

Azure nabízí pro ochranu dat následující typy šifrování:

- Šifrování během přenosu
- Šifrování neaktivních uložených dat

#### <a name="encryption-in-transit"></a>Šifrování při přenosu

Šifrování při přenosu je mechanismus ochrany dat při přenosu mezi sítěmi. Pomocí Azure Storage můžete zabezpečit data pomocí:

- [Šifrování na úrovni přenosu](../../storage/blobs/security-recommendations.md), jako je například https při přenosu dat do nebo z Azure Storage.
- [Šifrování kabelů](../../storage/blobs/security-recommendations.md), jako je například šifrování SMB 3,0 pro sdílené složky Azure.
- [Šifrování na straně klienta](../../storage/blobs/security-recommendations.md), aby se data zašifroval před přenosem do úložiště a dešifrují data poté, co se přenesou z úložiště.

#### <a name="encryption-at-rest"></a>Šifrování v klidovém umístění

V případě mnoha organizací je [šifrování dat v klidovém](isolation-choices.md) případě povinný krok k ochraně dat, dodržování předpisů a suverenity dat. Existují tři funkce Azure, které poskytují šifrování dat v klidovém znění:

- [Šifrování služby Storage](../../storage/blobs/security-recommendations.md) umožňuje požádat, aby služba úložiště při zápisu do Azure Storage automaticky zašifroval data.
- [Šifrování na straně klienta](../../storage/blobs/security-recommendations.md) také poskytuje funkci šifrování v klidovém umístění.
- [Azure Disk Encryption](./azure-disk-encryption-vms-vmss.md) slouží k šifrování disků s operačním systémem a datových disků, které používá virtuální počítač s IaaS.

#### <a name="azure-disk-encryption"></a>Azure Disk Encryption

[Azure Disk Encryption](./azure-disk-encryption-vms-vmss.md) pro virtuální počítače pomáhá řešit požadavky organizace na zabezpečení a dodržování předpisů tím, že šifruje disky virtuálních počítačů (včetně spouštěcích a datových disků) pomocí klíčů a zásad, které řídíte v [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).

Řešení pro šifrování disků pro Windows je založené na [Microsoft nástroj BitLocker Drive Encryption](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc732774(v=ws.11))a řešení pro Linux je založené na [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt).

Řešení podporuje následující scénáře pro virtuální počítače s IaaS, když jsou povolené v Microsoft Azure:

- Integrace s Azure Key Vault
- Virtuální počítače úrovně Standard: a, D, DS, G, GS a tak dále, řady IaaS Series
- Povolení šifrování na virtuálních počítačích s Windows a Linux IaaS
- Zakázání šifrování v operačním systému a datových jednotkách pro virtuální počítače s Windows IaaS
- Zakázání šifrování u datových jednotek pro virtuální počítače s IaaS Linux
- Povolení šifrování na virtuálních počítačích s IaaS, na kterých běží klientský operační systém Windows
- Povolení šifrování u svazků s cestami připojení
- Povolení šifrování pro virtuální počítače se systémem Linux, které jsou konfigurovány pomocí diskového disku RAID, pomocí [mdadm](https://en.wikipedia.org/wiki/Mdadm)
- Povolení šifrování pro virtuální počítače se systémem Linux pomocí [LVM (Správce logických svazků)](/windows/win32/fileio/about-volume-management) pro datové disky
- Povolení šifrování u virtuálních počítačů s Windows nakonfigurovaných pomocí prostorů úložiště
- Podporují se všechny veřejné oblasti Azure.

Řešení nepodporuje následující scénáře, funkce a technologie v této verzi:

- Virtuální počítače IaaS úrovně Basic
- Zakázání šifrování na jednotce operačního systému pro virtuální počítače s IaaS Linux
- IaaS virtuální počítače, které jsou vytvořené pomocí metody vytváření virtuálních počítačů Classic
- Integrace s místní službou správy klíčů
- Soubory Azure (Shared File System), systém souborů NFS (Network File System), dynamické svazky a virtuální počítače s Windows, které jsou nakonfigurované s využitím softwarových systémů RAID

## <a name="sql-database-isolation"></a>SQL Database izolaci

SQL Database je služba v cloudu Microsoftu poskytující relační databáze založené na předním databázovém stroji Microsoft SQL Server a schopné obsloužit i klíčové úlohy. SQL Database nabízí předvídatelné izolaci dat na úrovni účtu, geografickou oblast a oblast založenou na síti – to vše s téměř nulovou správou.

### <a name="sql-database-application-model"></a>SQL Database aplikační model

[Microsoft SQL Database](../../azure-sql/database/single-database-create-quickstart.md) je cloudová služba relačních databází založená na SQL serverch technologiích. Poskytuje vysoce dostupnou a škálovatelnou databázovou službu pro více tenantů, která je hostována Microsoftem v cloudu.

V perspektivě aplikace SQL Database poskytuje následující hierarchii: Každá úroveň má jednu z níže uvedených úrovní.

![SQL Database aplikační model](./media/isolation-choices/azure-isolation-fig10.png)

Účet a předplatné jsou Microsoft Azure konceptech platforem pro přidružení fakturace a správy.

Logické servery a databáze SQL jsou SQL Database specifických konceptech a jsou spravované pomocí SQL Database, poskytovaného rozhraní OData a TSQL nebo prostřednictvím Azure Portal.

Servery v SQL Database nejsou fyzické nebo instance virtuálních počítačů, místo toho se jedná o kolekce databází, správu sdílení a zásady zabezpečení, které jsou uložené v, označované jako "logická hlavní" databáze.

![SQL Database](./media/isolation-choices/azure-isolation-fig11.png)

Mezi logické hlavní databáze patří:

- Přihlášení SQL používaná k připojení k serveru
- Pravidla brány firewall

Informace o fakturaci a využití pro databáze ze stejného serveru nejsou zaručené ve stejné fyzické instanci v clusteru, ale při připojování musí aplikace zadat název cílové databáze.

V perspektivě zákazníka se server vytvoří v geograficky grafické oblasti, přičemž skutečné vytvoření serveru se stane v jednom z clusterů v dané oblasti.

### <a name="isolation-through-network-topology"></a>Izolace prostřednictvím síťové topologie

Když se vytvoří server a zaregistruje se jeho název DNS, bude název DNS ukazovat na adresu "virtuální IP adresa brány" v konkrétním datovém centru, kde byl server umístěný.

Za VIP (virtuální IP adresa) máme kolekci bezstavových služeb brány. Obecně platí, že brány se dostanou, když je potřeba koordinace mezi několika zdroji dat (hlavní databáze, uživatelská databáze atd.). Služby brány implementují toto:

- **Proxy připojení TDS.** To zahrnuje vyhledání uživatelské databáze v clusteru back-end, implementaci sekvence přihlášení a přesměrování paketů TDS do back-endu a back-endu.
- **Správa databází.** To zahrnuje implementaci kolekce pracovních postupů pro vytváření, změny a ukládání databázových operací. Databázové operace mohou být vyvolány buď pomocí sledování paketů TDS nebo explicitních rozhraní API OData.
- Vytvoření, změna nebo zrušení operací přihlášení/uživatele
- Operace správy serveru přes rozhraní OData API

![Izolace prostřednictvím síťové topologie](./media/isolation-choices/azure-isolation-fig12.png)

Vrstva za bránami se nazývá "back-end". To je místo, kde jsou všechna data uložena ve vysoce dostupném způsobem. Každá část dat je označována jako patřící do oddílu "partition" nebo "jednotka převzetí služeb při selhání", přičemž každý z nich má alespoň tři repliky. Repliky se ukládají a replikují pomocí modulu SQL Server a spravují se systémem převzetí služeb při selhání, který se často označuje jako "prostředky infrastruktury".

Obecně platí, že back-end systém nekomunikuje s jinými systémy jako bezpečnostní opatření. To je vyhrazeno pro systémy na front-endové úrovni (brány). Počítače vrstvy brány mají omezená oprávnění na back-endové počítače, aby se minimalizoval prostor pro útoky jako důkladný mechanismus pro útok.

### <a name="isolation-by-machine-function-and-access"></a>Izolace podle funkce počítače a přístupu

SQL Database (se skládá ze služeb spuštěných v různých funkcích počítače). SQL Database je rozdělené do cloudové databáze back-endu a "front-end" (brány/správy), přičemž obecný princip provozu směřuje jenom do back-endu. Prostředí front-end může komunikovat s vnějším světem jiných služeb a obecně má pouze omezená oprávnění v back-endu (dostatečně pro volání vstupních bodů, které musí vyvolat).

## <a name="networking-isolation"></a>Izolace sítě

Nasazení Azure má několik vrstev izolace sítě. Následující diagram znázorňuje různé vrstvy izolace sítě, které Azure poskytuje zákazníkům. Tyto vrstvy se nativně nacházejí v rámci samotné platformy Azure i v uživatelsky definovaných funkcích. Díky příchozímu přenosu z Internetu poskytuje Azure DDoS izolaci proti útokům ve velkém měřítku proti Azure. Další izolovanou vrstvou jsou uživatelsky definované veřejné IP adresy (koncové body), které slouží k určení provozu, který může projít cloudovou službou do virtuální sítě. Nativní izolace virtuální sítě Azure zajišťuje kompletní izolaci ze všech ostatních sítí a přenos dat pouze prostřednictvím uživatelem nakonfigurovaných cest a metod. Tyto cesty a metody jsou další vrstvou, kde skupin zabezpečení sítě, UDR a síťová virtuální zařízení je možné použít k vytvoření hranic izolace pro ochranu nasazení aplikací v chráněné síti.

![Izolace sítě](./media/isolation-choices/azure-isolation-fig13.png)

**Izolace provozu:** [Virtuální síť](../../virtual-network/virtual-networks-overview.md) je hranice izolace provozu na platformě Azure. Virtuální počítače v jedné virtuální síti nemůžou komunikovat přímo s virtuálními počítači v jiné virtuální síti, a to i v případě, že oba virtuální sítě vytvoří stejný zákazník. Izolace je kritická vlastnost, která zajišťuje, že virtuální počítače a komunikace zákazníků zůstávají privátní v rámci virtuální sítě.

[Podsíť](../../virtual-network/virtual-networks-overview.md) nabízí další vrstvu izolace ve virtuální síti založenou na rozsahu IP adres. IP adresy ve virtuální síti můžete pro organizaci a zabezpečení rozdělit virtuální síť do několika podsítí. Virtuální počítače a instance rolí PaaS nasazené do podsítí (stejných nebo různých) v rámci jedné virtuální sítě můžou navzájem komunikovat bez jakékoli další konfigurace. [Skupinu zabezpečení sítě (skupin zabezpečení sítě)](../../virtual-network/virtual-networks-overview.md) můžete také nakonfigurovat tak, aby povolovala nebo odmítala síťový provoz do instance virtuálního počítače na základě pravidel nakonfigurovaných v seznamu řízení přístupu (ACL) NSG. Skupiny NSG můžou být přidružené buď k podsítím, nebo k jednotlivým instancím virtuálních počítačů v této podsíti. Pokud je skupina zabezpečení sítě přidružená k podsíti, pravidla seznamu ACL platí pro všechny instance virtuálních počítačů v této podsíti.

## <a name="next-steps"></a>Další kroky

- Seznamte [se s možnostmi izolace sítě pro počítače ve virtuálních sítích Windows Azure](https://azure.microsoft.com/blog/network-isolation-options-for-machines-in-windows-azure-virtual-networks/). To zahrnuje Klasický scénář front-endu a back-endu, ve kterém můžou počítače v určité back-mailové síti nebo v podsítích umožňovat konkrétním koncovým bodů připojení jenom určitých klientů nebo jiných počítačů na základě seznamu povolených IP adres.

- Přečtěte si informace o [izolaci virtuálních počítačů v Azure](../../virtual-machines/isolation.md). Azure COMPUTE nabízí velikosti virtuálních počítačů, které jsou izolované na konkrétní typ hardwaru a vyhrazené pro jednoho zákazníka.