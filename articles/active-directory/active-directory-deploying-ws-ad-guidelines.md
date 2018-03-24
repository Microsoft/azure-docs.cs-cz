---
title: Pokyny pro nasazení systému Windows Server Active Directory na virtuálních počítačích Azure | Microsoft Docs
description: Pokud znáte postup nasazení služby AD Domain Services a federační služby AD na místní, zjistěte, jak fungují na virtuálních počítačích Azure.
services: active-directory
documentationcenter: ''
author: femila
manager: mtillman
editor: ''
ms.assetid: 04df4c46-e6b6-4754-960a-57b823d617fa
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/20/2018
ms.author: femila
ms.openlocfilehash: c2d58e056cdb285be51d259492e11e6ae37b253e
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="guidelines-for-deploying-windows-server-active-directory-on-azure-virtual-machines"></a>Pokyny pro nasazení systému Windows Server Active Directory na virtuálních počítačích Azure
Tento článek vysvětluje rozdíly mezi nasazení systému Windows Server Active Directory Domain Services (AD DS) a služby Active Directory Federation Services (AD FS) místně a jejich nasazení na virtuálních počítačích Microsoft Azure.

## <a name="scope-and-audience"></a>Obor a cílová skupina
Článek je určený pro ty již došlo k nasazení služby Active Directory v místě. Vysvětluje rozdíly mezi na Microsoft Azure virtuální počítače nebo Azure virtual Network a nasazeních služby Active Directory tradičních místních nasazení služby Active Directory. Virtuální počítače Azure a virtuálních sítí Azure jsou součástí služby infrastruktury jako služba (IaaS) nabídky pro organizace využívat výpočetní prostředky v cloudu.

Pro ty, které se s nasazení služby AD, najdete v článku [Průvodce nasazením služby AD DS](https://technet.microsoft.com/library/cc753963) nebo [plánování nasazení služby AD FS](https://technet.microsoft.com/library/dn151324.aspx) podle potřeby.

Tento článek předpokládá, že čtečka se seznámit s následující koncepty:

* Nasazení systému Windows Server AD DS a Správa
* Nasazení a konfiguraci DNS pro podporu infrastruktury a systému Windows Server AD DS
* Nasazení systému Windows Server AD FS a Správa
* Nasazení, konfiguraci a správu předávající strany aplikací (weby a webové služby), které můžou využívat tokeny služby Windows Server AD FS
* Virtuální počítač obecné koncepty, jako jsou postup konfigurace virtuálního počítače, virtuální disky a virtuální sítě

V tomto článku jsou uvedeny požadavky pro hybridním scénáři nasazení, ve které systému Windows Server AD DS nebo AD FS se částečně nasadit místně a částečně nasazují na virtuálních počítačích Azure. Dokument nejprve popisuje důležité rozdíly mezi systémem Windows Server AD DS a AD FS na virtuálních počítačích Azure a místními a důležité rozhodovací body, které ovlivňují návrhu a nasazení. Zbývající papíru vysvětluje pokyny pro každou z rozhodovací body podrobněji a jak se má použít pro různé scénáře nasazení podle pokynů.

Tento článek se nezabývá konfigurace [Azure Active Directory](http://azure.microsoft.com/services/active-directory/), což je služba založené na REST, která poskytuje funkce řízení správy a přístupu identit pro cloudové aplikace. Azure Active Directory (Azure AD) a Windows Server služby AD DS se ale nedají používat společně zajistit do řešení pro správu přístupu a identit pro hybridní dnešních IT prostředí a moderní aplikace. Chcete-li pochopit rozdíly a vztahy mezi službami Windows Server AD DS a Azure AD, zvažte následující:

1. Windows Server AD DS může běžet v cloudu na virtuálních počítačích Azure a při použití Azure rozšířit vaše místní datacentra do cloudu.
2. Můžete poskytnout vaši uživatelé jednotného přihlašování k softwaru jako služba (SaaS) pomocí služby Azure AD. Microsoft Office 365 používá tuto technologii, například a aplikace běžící v Azure nebo jiných platforem cloudu jej také použít.
3. Můžete použít Azure AD (jeho služby Řízení přístupu) tak, aby uživatelé přihlásit pomocí identit ze sítě Facebook, Google, Microsoft a jiných poskytovatelů identit, které aplikace, které jsou hostované v cloudu nebo místně.

Další informace o těchto rozdílech najdete v tématu [Azure Identity](fundamentals-identity.md).

## <a name="related-resources"></a>Související prostředky
Můžete stáhnout a spustit [posouzení připravenosti na virtuální počítač Azure](https://www.microsoft.com/download/details.aspx?id=40898). Hodnocení bude automaticky kontrolovat v místním prostředí a vytvářet vlastní sestavy založené na pokyny v tomto tématu, vám pomůže při migraci v prostředí Azure nalezen.

Doporučujeme také nejdřív zkontrolovali kurzy, příručky a videa, které se týkají následující témata:

* [Konfigurace virtuální sítě jenom pro Cloud na portálu Azure](../virtual-network/quick-create-portal.md)
* [Konfigurace VPN typu Site-to-Site na portálu Azure](../vpn-gateway/vpn-gateway-site-to-site-create.md)
* [Instalace nové doménové struktury služby Active Directory na virtuální síť Azure](active-directory-new-forest-virtual-machine.md)
* [Instalace repliky řadiče domény služby Active Directory v Azure](active-directory-install-replica-active-directory-domain-controller.md)
* [Microsoft Azure IaaS IT specialista: Základy (01) virtuálního počítače](https://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)
* [Microsoft Azure IaaS IT specialista: (05) vytváření virtuální sítě a připojení mezi různými místy](https://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)

## <a name="introduction"></a>Úvod
Základní požadavky pro nasazení systému Windows Server Active Directory na virtuálních počítačích Azure se velmi málo liší od jeho nasazení na místní virtuální počítače (a do určité míry fyzických počítačů). Například v případě systému Windows Server AD DS, pokud řadičů domén (DC), které nasazujete na virtuálních počítačích Azure jsou repliky v existující místní podnikové doméně nebo doménové struktuře, pak nasazení Azure lze zacházet z velké části stejným způsobem, jak můžete zacházet s jakoukoli jinou lokalitu další Windows Server Active Directory. To znamená podsítě musí být definován v systému Windows Server AD DS, lokalitu vytvořen, podsítě, odkazy na tento web a připojený k jiných lokalitách pomocí odkazů na příslušné lokality. Existují však určité rozdíly, které jsou společné pro všechna nasazení Azure a některé, který se liší podle scénář konkrétní nasazení. Níže jsou uvedeny dva základní rozdíly:

### <a name="azure-virtual-machines-may-need-connectivity-to-the-on-premises-corporate-network"></a>Virtuální počítače Azure může být nutné připojení k podnikové síti místně.
Připojení Azure virtuální počítače zpět do podnikové sítě na místní vyžaduje virtuální síť Azure, která zahrnuje site-to-site nebo bodu lokality virtuální privátní sítě (VPN) komponentu bezproblémově připojit virtuální počítače Azure a místní počítače. Tato součást VPN může také povolit místní členské počítače domény pro přístup k doméně systému Windows Server Active Directory jejichž řadičích domény hostovaných výhradně na virtuálních počítačích Azure. Je důležité si uvědomit ale, že v případě selhání sítě VPN ověřování a další operace, které jsou závislé na Windows Server Active Directory se také nezdaří. Když uživatelé mohou být schopni přihlašování pomocí stávající do mezipaměti přihlašovací údaje, všechny peer-to-peer nebo se nezdaří pokusy o ověření klient server, pro které lístky muset ještě vydávají nebo se staly zastaralé.

V tématu [virtuální sítě](http://azure.microsoft.com/documentation/services/virtual-network/) pro předvedení video a seznam podrobných návodů, včetně [konfigurovat síť Site-to-Site VPN na portálu Azure](../vpn-gateway/vpn-gateway-site-to-site-create.md).

> [!NOTE]
> Windows Server Active Directory můžete taky nasadit v Azure virtuální síti, která nemá připojení k místní síti. Podle pokynů v tomto tématu, ale předpokládá, že virtuální sítě Azure je použít, protože poskytuje IP adresách možnosti, které jsou nezbytné pro Windows Server.
> 
> 

### <a name="static-ip-addresses-can-be-configured-with-azure-powershell"></a>Statické IP adresy můžete nakonfigurovat pomocí prostředí Azure PowerShell
Dynamické adresy jsou přiděleny ve výchozím nastavení, ale použijte rutinu Set-AzureStaticVNetIP, pokud chcete místo toho přiřadit statickou IP adresu. Tuto rutinu nastaví statickou IP adresu, která zachová prostřednictvím službou opravy a vypnutí nebo restartování virtuálního počítače. Další informace najdete v tématu [statické interní IP adresu pro virtuální počítače](http://azure.microsoft.com/blog/static-internal-ip-address-for-virtual-machines/). Můžete také nakonfigurovat statickou IP adresu při vytváření virtuálního počítače na portálu Azure, jak je uvedeno níže. Další informace najdete v tématu [vytvoření virtuálního počítače se statickou veřejnou IP adresu pomocí portálu Azure](../virtual-network/virtual-network-deploy-static-pip-arm-portal.md).

![snímek obrazovky kroku přidejte statickou IP adresu, při vytváření virtuálního počítače](media/active-directory-deploying-ws-ad-guidelines/static-ip.png)

## <a name="BKMK_Glossary"></a>Termíny a definice
Následuje seznam není vyčerpávající podmínky pro různé Azure technologie, které se bude odkazovat v tomto článku.

* **Virtuální počítače Azure**: nabídky IaaS v Azure, která umožňuje zákazníkům nasadit virtuální počítače se systémem téměř všechny tradičně místní zatížení serveru.
* **Virtuální síť Azure**: síťové služby v Azure, která umožňuje zákazníkům vytvářet a spravovat virtuální sítě v Azure a bezpečně je odkaz na jejich vlastní místní síťové infrastruktury pomocí virtuální privátní sítě (VPN).
* **Virtuální IP adresy**: internetového IP adresu, která není vázán na konkrétní adaptér počítači nebo v síti. Cloudové služby jsou přiřazeny virtuální IP adresu pro příjem síťový provoz, který je přesměrován na virtuální počítač Azure. Virtuální IP adresa je vlastnost cloudových služeb, která může obsahovat jeden nebo více virtuálních počítačích Azure. Všimněte si také, že virtuální sítě Azure může obsahovat jeden nebo více cloudových služeb. Virtuální IP adresy poskytují nativní funkce Vyrovnávání zatížení.
* **Dynamickou IP adresu**: Toto je IP adresu, která je jenom interní. Ho by měl být nakonfigurovaný jako statickou IP adresu (pomocí rutiny Set-AzureStaticVNetIP) pro virtuální počítače, které jsou hostiteli rolí serveru řadiče domény a DNS.
* **Služba opravy**: proces, ve kterém Azure automaticky vrátí služby do stavu spuštěno znovu po zjistí, že služba selhala. Služba opravy je jedním z aspektů Azure, který podporuje dostupnost a odolnost. Při nepravděpodobné, výsledek následující služby opravy incident pro řadič domény spuštěn na virtuálním počítači je podobná neplánované restart, ale má několik vedlejší účinky:
  
  * Virtuální síťový adaptér ve virtuálním počítači se změní.
  * Změní adresu MAC virtuálního síťového adaptéru
  * ID procesoru nebo procesoru virtuálního počítače se změní.
  * Konfigurace IP virtuální síťový adaptér se nezmění, dokud, virtuální počítač je připojen k virtuální síti a jde o statickou adresu IP Virtuálního počítače.
  
  Žádná z těchto projevů ovlivnit Windows Server Active Directory, protože nemá žádné závislosti na adresu MAC nebo ID procesoru/procesoru, a všechna nasazení systému Windows Server Active Directory v Azure – se doporučují můžou běžet na virtuální síť Azure, jak je uvedeno výš.

## <a name="is-it-safe-to-virtualize-windows-server-active-directory-domain-controllers"></a>Je bezpečné pro virtualizaci řadičů domény Windows Server Active Directory?
Nasazení systému Windows Server Active Directory řadiče domény na virtuálních počítačích Azure se vztahují stejné pokyny jako spuštěná řadiče domény na místě ve virtuálním počítači. Spuštění virtualizovaných řadičů domény je bezpečné postupem, tak dlouho, dokud se řídit pokyny pro zálohování a obnovení řadiče domény. Další informace o omezení a pokyny pro spuštění virtualizovaných řadičů domény najdete v tématu [spouštění řadičů domény v Hyper-V](https://technet.microsoft.com/library/dd363553).

Hypervisory zadejte nebo trivialize technologie, které může způsobit problémy pro mnoho distribuovaných systémů, včetně systému Windows Server Active Directory. Například na fyzickém serveru, můžete vytvořit kopii disku nebo pomocí nepodporované metody vrátit stav serveru, včetně použití sítě SAN a tak dále, ale učinit na fyzickém serveru je mnohem obtížnější než obnovení snímku virtuálního počítače v hypervisoru. Azure nabízí funkce, které může mít za následek stejné nežádoucího stavu. Například byste neměli kopírovat soubory virtuálního pevného disku řadičů domény s namísto provádění pravidelných záloh, protože je obnovení může mít za následek podobné situaci pomocí funkce obnovení snímku.

Takové odvolání zavést bublinách USN, které může vést k trvale odlišných stavy řadiče domény. Například, může způsobit problémy:

* Přetrvávajících odstraněných objektů
* Nekonzistentní hesla
* Hodnoty atributů nekonzistentní
* Schéma se neshoduje, pokud je hlavní server schémat vrácena zpět

Další informace o tom, jak dopad na řadičích domény najdete v tématu [hodnoty USN a vrácení hodnot USN](https://technet.microsoft.com/library/virtual_active_directory_domain_controller_virtualization_hyperv.aspx#usn_and_usn_rollback).

Od verze Windows Server 2012, [další bezpečnostní opatření jsou součástí služby AD DS](https://technet.microsoft.com/library/hh831734.aspx). Tato bezpečnostní opatření pomoc při ochraně virtualizovaných řadičů domény na výše uvedené problémy, tak dlouho, dokud základní platformu hypervisoru VM-GenerationID podporuje. Azure podporuje VM-GenerationID, což znamená, že řadiče domény se systémem Windows Server 2012 nebo novější na Azure virtuální počítače mají další bezpečnostní opatření.

> [!NOTE]
> Měli byste vypnout a restartovat virtuální počítač, který spouští role řadiče domény v Azure v rámci hostovaného operačního systému místo použití **vypnout** možnost v Azure Portal. V současné době pomocí portálu vypnutí virtuálního počítače způsobí, že virtuální počítač k zrušení přiřazení. Deallocated virtuální počítač má výhodu v podobě není nabíhání poplatků za, ale také obnoví VM-GenerationID, což je žádoucí, aby řadič domény. Při obnovení VM-GenerationID invocationID databázi služby AD DS je také obnovit, se zahodí fond RID a adresáři SYSVOL je označena jako neautoritativní. Další informace najdete v tématu [Úvod do virtualizace služby Active Directory Domain Services (AD DS)](https://technet.microsoft.com/library/hh831734.aspx) a [bezpečná virtualizace DFSR](http://blogs.technet.com/b/filecab/archive/2013/04/05/safely-virtualizing-dfsr.aspx).
> 
> 

## <a name="why-deploy-windows-server-ad-ds-on-azure-virtual-machines"></a>Proč nasazení systému Windows Server AD DS na virtuálních počítačích Azure?
Mnoho scénářů nasazení systému Windows Server AD DS jsou vhodné pro nasazení jako virtuální počítače na platformě Azure. Předpokládejme například, že máte společnosti v Evropě, který potřebuje k ověřování uživatelů ve vzdáleném umístění v Asii. Společnost nebyla nasazena dříve systému Windows Server Active Directory řadiče domény v Asii kvůli náklady na nasazení je a omezené znalosti ke správě serverů po nasazení. V důsledku toho jsou žádosti o ověření z Asie obsluhovány pomocí řadiče domény v Evropě s získat výsledky. V takovém případě můžete nasadit řadič domény na virtuální počítač, který jste zadali, je nutné spustit v datovém centru Azure v Asii. Připojení řadiče domény do Azure virtuální sítě, který je připojený přímo do vzdáleného umístění bude zlepšit výkon ověřování.

Azure je také vhodné jako náhrada na jinak nákladná po havárii (DR) obnovení lokality. Relativně nízká – náklady na hostování malý počet řadičů domény a jedné virtuální sítě v Azure představuje alternativu atraktivní.

Nakonec můžete nasadit síťové aplikace v Azure, například SharePoint, které vyžaduje Windows Server Active Directory, ale má žádná závislost na místní sítě nebo podnikové Windows Server Active Directory. V takovém případě nasazení izolované doménové struktuře v Azure pro SharePoint splňovat požadavky na serveru je optimální. Znovu nasazení síťových aplikací, které vyžadují připojení k místní síti a podnikové služby Active Directory je také podporována.

> [!NOTE]
> Vzhledem k tomu, které poskytuje připojení vrstvy 3, součásti sítě VPN, který poskytuje připojení mezi virtuální sítě Azure a místní sítě můžete také povolit členské servery, které spustit místně využít řadiče domény provozované jako virtuální počítače Azure v Azure virtuální síť. Ale pokud síť VPN není k dispozici, komunikace mezi místní počítače a řadiče domény založené na Azure nebude fungovat, což je ověřování a různých dalších chyb.  
> 
> 

## <a name="contrasts-between-deploying-windows-server-active-directory-domain-controllers-on-azure-virtual-machines-versus-on-premises"></a>Uvádí vedle sebe mezi nasazením řadiče domény Windows Server Active Directory ve virtuálních počítačích Azure a místními
* Pro každý scénář nasazení systému Windows Server Active Directory, která obsahuje více než jeden virtuální počítač je třeba použít virtuální sítě Azure konzistenci IP adres. Všimněte si, že tato příručka předpokládá, že řadiče domény běží na virtuální síť Azure.
* Stejně jako u řadičů domény na místě, se doporučuje statické IP adresy. Statickou IP adresu můžete nakonfigurovat jenom pomocí prostředí Azure PowerShell. V tématu [statické interní IP adresu pro virtuální počítače](http://azure.microsoft.com/blog/static-internal-ip-address-for-virtual-machines/) další podrobnosti. Pokud máte monitorování systémů nebo jiná řešení, které kontrolují pro konfiguraci statických IP adres v rámci hostovaného operačního systému, můžete přiřadit stejné statické IP adresy na vlastnostech síťového adaptéru virtuálního počítače. Ale mějte na paměti, že budou zahozeny síťový adaptér, pokud zde nevyskytlo službou opravy virtuálního počítače nebo je vypnutý na portálu, a program navrátil jeho adresy. V takovém případě statickou IP adresu v hostovaném potřebovat k obnovení.
* Nasazení virtuálních počítačů ve virtuální síti nenaznačuje (ani vyžadovat) připojení zpět k místní síti; virtuální síť jenom umožňuje tuto možnost. Musíte vytvořit virtuální síť pro privátní komunikaci mezi Azure a v místní síti. Je třeba nasadit koncový bod sítě VPN v místní síti. Síť VPN se otevře z Azure do místní sítě. Další informace najdete v tématu [Přehled virtuálních sítí](../virtual-network/virtual-networks-overview.md) a [konfigurovat síť Site-to-Site VPN na portálu Azure](../vpn-gateway/vpn-gateway-site-to-site-create.md).

> [!NOTE]
> Možnost [vytvořit síť VPN point-to-site](../vpn-gateway/vpn-gateway-point-to-site-create.md) je k dispozici pro jednotlivé počítače se systémem Windows přímé připojení k virtuální sítě Azure.
> 
> 

* Bez ohledu na to, jestli vytvoříte virtuální sítě nebo Ne, vytvořit Azure poplatky za odchozí přenosy, ale není příchozí. Různých volbách návrhu Windows Server Active Directory může ovlivnit, kolik odchozí provoz generuje pomocí nasazení. Například nasazení řadiče domény jen pro čtení (RODC) omezuje odchozí provoz protože nereplikuje odchozí. Je však nutné rozhodnutí o nasazení řadič porovnat potřeba provádět operace zápisu u řadiče domény a [kompatibility](https://technet.microsoft.com/library/cc755190) , aplikace a služby v lokalitě mají s řadičů jen pro čtení. Další informace o poplatky za provozu najdete v tématu [Azure ceny na přehled](http://azure.microsoft.com/pricing/).
* Při dokončení řídit přes jaké serveru kapacita disku prostředky používat pro virtuální počítače na místní, jako je například paměti RAM a atd., v Azure je nutné vybrat ze seznamu velikostí předkonfigurované serveru. Pro řadič domény datový disk k uložení databáze systému Windows Server Active Directory potřeba kromě disku operačního systému.

## <a name="can-you-deploy-windows-server-ad-fs-on-azure-virtual-machines"></a>Můžete nasadit systém Windows Server AD FS na virtuálních počítačích Azure?
Ano, můžete nasadit systém Windows Server AD FS na virtuálních počítačích Azure a [osvědčené postupy pro nasazení služby AD FS](https://technet.microsoft.com/library/dn151324.aspx) místní rovněž vztahují na nasazení služby AD FS v Azure. Ale některé z osvědčených postupů, jako Vyrovnávání zatížení a vysoké dostupnosti vyžadovat technologie nad rámec co služby AD FS nabízí sám sebe. Musí být poskytnuta odpovídající infrastruktury. Umožňuje zkontrolovat některé z těchto osvědčené postupy a zjistit, jak lze dosáhnout pomocí virtuální sítě Azure a virtuální počítače Azure.

1. **Nikdy neměli zveřejňovat servery tokenu služba TOKENŮ zabezpečení přímo k Internetu.**
   
    To je důležité, protože služba tokenů zabezpečení vydává tokeny zabezpečení. V důsledku toho servery služby tokenů zabezpečení, například servery služby AD FS zacházet se stejnou úrovní ochrany jako řadič domény. Pokud dojde k narušení služby tokenů zabezpečení, uživatelé se zlými úmysly mít možnost vystavovat tokeny přístupu potenciálně obsahující deklarace identity podle vlastního uvážení pro aplikace předávající strany a dalších serverů služby tokenů zabezpečení v důvěřujících organizace.
2. **Nasazení řadiče domény služby Active Directory pro všechny uživatele domény ve stejné síti jako servery služby AD FS.**
   
    Servery služby AD FS používat k ověřování uživatelů služby Active Directory Domain Services. Doporučujeme k nasazení řadičů domény ve stejné síti jako servery služby AD FS. To poskytuje provozní kontinuitu, v případě propojení mezi síť Azure a místní sítě je poškozený a umožňuje nižší latenci a vyšší výkon pro přihlášení.
3. **Nasazení několika uzlů služby AD FS pro vysokou dostupnost a vyrovnávání zatížení.**
   
    Ve většině případů nepřijatelné selhání aplikace, která umožňuje služby AD FS, protože aplikace, které vyžadují tokeny zabezpečení jsou často klíčové operace. Výsledkem je a protože služby AD FS se nyní nachází v kritické cestu k přístupu k kritické podnikové procesy, službu AD FS musí být vysoce dostupný prostřednictvím více serverů služby AD FS a proxy servery služby AD FS. K dosažení distribuční požadavků, jsou nástroje pro vyrovnávání zatížení obvykle nasazují před proxy AD FS servery a servery služby AD FS.
4. **Nasaďte jeden nebo více uzlů Proxy webových aplikací pro přístup k Internetu.**
   
    Když uživatelé potřebovat přístup k aplikacím, které jsou chráněné službou AD FS, službu AD FS musí být dostupný z Internetu. Toho lze dosáhnout nasazením služby Proxy webových aplikací. Důrazně doporučujeme nasadit více než jeden uzel pro účely vysokou dostupnost a vyrovnávání zatížení.
5. **Omezení přístupu k prostředkům v interní síti z uzlů Proxy webových aplikací.**
   
    Povolit externí uživatele pro přístup k služby AD FS z Internetu, musíte nasadit Proxy webových aplikací uzly (nebo Proxy služby AD FS v dřívějších verzích systému Windows Server). Uzly proxy webové aplikace se zveřejňují přímo k Internetu. Nejsou nemusí být připojené k doméně a potřebují pouze přístup k serveru služby AD FS přes porty TCP 443 a 80. Důrazně doporučujeme, že je blokované komunikaci pro všechny ostatní počítače (zejména řadiče domény).
   
    To je obvykle dosažených místní prostřednictvím DMZ. Brány firewall pomocí seznamu povolených IP adres režimu operace omezit provoz od DMZ k místní síti (tedy jenom provoz ze zadaných IP adres a pomocí zadaného portů je povolený a všechny ostatní přenosy jsou blokovány).

Následující diagram ukazuje a tradiční místní nasazení služby AD FS.

![Diagram nasazení tradičních místních Active Directory Federation Services](media/active-directory-deploying-ws-ad-guidelines/ADFS_onprem.png)

Ale protože Azure nenabízí nativní, funkce plné brány firewall, další možnosti muset použít k omezení přenosu. V následující tabulce jsou uvedeny jednotlivé možnosti a jeho výhody a nevýhody.

| Možnost | Výhody | Nevýhodou |
| --- | --- | --- |
| [Seznamy ACL síť Azure](../virtual-network/virtual-networks-acl.md) |Méně nákladná a jednodušší počáteční konfigurace |Konfigurace seznamu ACL další síťové vyžadována, když se do nasazení přidají nových virtuálních počítačů |
| [Barracuda NG firewall](https://www.barracuda.com/products/ngfirewall) |Seznam povolených adres režimu operace a vyžaduje žádná konfigurace seznamu ACL sítě |Vyšší náklady a složitost pro počáteční nastavení |

Podrobný postup nasazení služby AD FS v tomto případě jsou následující:

1. Vytvoření virtuální sítě s připojením mezi různými místy, buď pomocí sítě VPN nebo [ExpressRoute](http://azure.microsoft.com/services/expressroute/).
2. Nasazení řadiče domény ve virtuální síti. Tento krok je volitelný, ale doporučené.
3. Nasaďte servery připojené k doméně služby AD FS, ve virtuální síti.
4. Vytvoření [s vyrovnáváním zatížení interní](http://azure.microsoft.com/blog/internal-load-balancing/) , obsahuje servery služby AD FS a používá nový privátní IP adresy v rámci virtuální sítě (dynamickou IP adresu).
   
   1. Aktualizujte DNS vytvořit plně kvalifikovaný název domény tak, aby odkazoval na privátní (dynamické) IP adresu sady Interní skupinu s vyrovnáváním zatížení.
5. Vytvoření cloudové služby (nebo samostatné virtuální sítě) pro uzly Proxy webových aplikací.
6. Nasazení služby Proxy webových aplikací uzly v cloudové služby nebo virtuální sítě
   
   1. Vytvořte sadu externí skupinu s vyrovnáváním zatížení, který obsahuje uzel Proxy webových aplikací.
   2. Aktualizujte externí DNS název (FQDN) tak, aby odkazoval na cloud service veřejné IP adresy (virtuální IP adresa).
   3. Konfigurace proxy služby AD FS použít plně kvalifikovaný název domény, která odpovídá sadu interní skupinu s vyrovnáváním zatížení pro servery služby AD FS.
   4. Aktualizujte na základě deklarace webovým serverům používat externí plně kvalifikovaný název domény pro jejich poskytovatele deklarací identity.
7. Omezení přístupu mezi Proxy webových aplikací pro všechny počítače ve virtuální síti služby AD FS.

Pokud chcete omezit přenosy, je potřeba nakonfigurovat pro jenom přenosy na porty TCP 80 a 443 sadu Vyrovnávání zatížení pro danou službu Vyrovnávání zatížení Azure interní a veškerý provoz do interní dynamickou IP adresu skupinu s vyrovnáváním zatížení je vyřadit.

![Diagram sítě ACL služby AD FS heslem, TCP 443 a 80 povolené.](media/active-directory-deploying-ws-ad-guidelines/ADFS_ACLs.png)

Servery služby AD FS by byl povolen pouze podle následujících zdrojů:

* Nástroje pro vyrovnávání zatížení Azure interní.
* IP adresa správce v místní síti.

> [!WARNING]
> Návrh musí uzly Proxy webových aplikací zabránit v dosažení ostatní virtuální počítače v virtuální síť Azure nebo žádná umístění v místní síti. Můžete provést konfiguraci pravidel brány firewall v místní zařízení u připojení Express Route nebo zařízení VPN pro připojení VPN typu site-to-site.
> 
> 

Nevýhodou k této možnosti je potřeba nakonfigurovat na různých zařízeních, včetně služby Vyrovnávání zatížení pro vnitřní, serverů služby AD FS a žádné jiné servery, které se přidají do virtuální sítě v síti seznamy ACL. Pokud žádné zařízení je přidaný do nasazení bez konfigurace seznamů ACL sítě pro omezení přenosu do ní, celé nasazení může být ohrožena. Pokud někdy změně IP adres uzlů Proxy webových aplikací, musí se obnovit sítě seznamy řízení přístupu (což znamená, že proxy serverů by měla být nakonfigurované na používání [statické IP adresy dynamické](http://azure.microsoft.com/blog/static-internal-ip-address-for-virtual-machines/)).

![Služba AD FS v Azure s sítě ACL.](media/active-directory-deploying-ws-ad-guidelines/ADFS_Azure.png)

Další možností je použít [Barracuda NG Firewall](https://www.barracuda.com/products/ngfirewall) zařízení pro řízení přenosu mezi servery služby AD FS a proxy servery služby AD FS. Tato možnost je v souladu s osvědčenými postupy pro zabezpečení a vysokou dostupnost a vyžaduje menší správy po počáteční instalaci, protože zařízení brány Firewall NG Barracuda poskytuje seznam povolených adres režimu správy brány firewall a může být nainstalována přímo na virtuální síť Azure. Která eliminuje potřebu nakonfigurovat seznamy ACL sítě při každém přidání nového serveru do nasazení. Ale tuto možnost přidá počáteční nasazení složitost a náklady.

V takovém případě se místo jeden nasadí dvě virtuální sítě. Zavoláme vám je VNet1 a VNet2. VNet1 proxy serverů a VNet2 obsahuje STSs a síťové připojení zpět k podnikové síti. VNet1 je proto fyzicky (i když prakticky) izolované od VNet2 a pak z podnikové sítě. VNet1 je připojen k VNet2 pomocí technologie speciální tunelu známé jako přenosu nezávislé sítě architektura (VPDI). Tunelové propojení VPDI je připojen ke každému z virtuální sítě pomocí brány firewall Barracuda NG – jeden Barracuda na všech virtuálních sítí.  Pro vysokou dostupnost se doporučuje nasazení dvou barakudy v každé virtuální síti; jednu aktivní, je další pasivní. Nabízejí velmi bohaté fungující brána firewall může možnosti, které umožňují nám tak, aby napodoboval operaci tradičních místních DMZ v Azure.

![Služba AD FS v Azure s bránou firewall.](media/active-directory-deploying-ws-ad-guidelines/ADFS_Azure_firewall.png)

Další informace najdete v tématu [služby AD FS: rozšíření deklaracemi místní front-endu aplikace k Internetu](#BKMK_CloudOnlyFed).

### <a name="an-alternative-to-ad-fs-deployment-if-the-goal-is-office-365-sso-alone"></a>Alternativu k nasazení služby AD FS, pokud je cílem Office 365 SSO samostatně
Je jinou alternativou k nasazení AD FS úplně, pokud je vaším cílem je pouze povolit přihlášení pro Office 365. V takovém případě můžete jednoduše nasazení nástroje DirSync se heslo synchronizace místními a dosáhnout stejné konečný výsledek s minimálním nasazení složitost, protože tento přístup nevyžaduje služby AD FS a Azure.

Následující tabulka porovnává fungování procesy přihlášení s i bez nasazení služby AD FS.

| Office 365 jeden přihlašování pomocí služby AD FS a DirSync | Office 365 stejné přihlášení pomocí nástroje DirSync + synchronizace hesla |
| --- | --- |
| 1. Uživatel přihlásí k podnikové síti a je ověřen u systému Windows Server Active Directory. |1. Uživatel přihlásí k podnikové síti a je ověřen u systému Windows Server Active Directory. |
| 2. Uživatel se pokusí o přístup k Office 365 (jsem @contoso.com). |2. Uživatel se pokusí o přístup k Office 365 (jsem @contoso.com). |
| 3. Office 365 přesměruje uživatele do služby Azure AD. |3. Office 365 přesměruje uživatele do služby Azure AD. |
| 4. Vzhledem k tomu, že Azure AD nemůže ověřit uživatele a jste srozuměni s tím, že existuje vztah důvěryhodnosti s AD FS na místě, přesměruje uživatele ve službě AD FS. |4. Azure AD nemůže přímo přijímat lístky protokolu Kerberos a neexistuje žádný vztah důvěryhodnosti, je vyžaduje, aby uživatel zadal pověření. |
| 5. Uživatel odešle lístek protokolu Kerberos do tokenů zabezpečení AD FS. |5. Uživatel zadá stejné místní heslo, a Azure AD ověří je před uživatelské jméno a heslo, které byl synchronizován pomocí nástroje DirSync. |
| 6. Služba AD FS transformuje lístek protokolu Kerberos na požadovaný token formátu nebo nároky a přesměruje uživatele na Azure AD. |6. Azure AD přesměruje uživatele na Office 365. |
| 7. Uživatel se ověřuje na Azure AD (jiné transformace dojde). |7. Uživatel může přihlásit k Office 365 a aplikace Outlook Web Access pomocí tokenu Azure AD. |
| 8. Azure AD přesměruje uživatele na Office 365. | |
| 9. Uživatel je přihlášen bezobslužně Office 365. | |

V Office 365 s nástrojem DirSync s scénář synchronizace hesel (žádné AD FS) jednotné přihlašování je nahrazena "stejné přihlášení" kde "stejné" jednoduše znamená, že uživatelé musí znovu zadat jejich stejné místních přihlašovacích údajů při přístupu k Office 365. Všimněte si, že tato data můžete zapamatovávají prohlížeče uživatele snížit další výzvy.

### <a name="additional-food-for-thought"></a>Další myšlenku for jídlo
* Pokud nasadíte proxy služby AD FS na virtuální počítač Azure, je potřeba připojení k serverům služby AD FS. Pokud jsou na místě, doporučuje se, že využíváte připojení site-to-site VPN poskytovaných virtuální sítě, aby uzly Proxy webových aplikací ke komunikaci s jejich serverech AD FS.
* Pokud nasadíte server služby AD FS na virtuální počítač Azure, je nutné připojení k řadiče domény Windows Server Active Directory, úložiště atributů a konfigurační databáze a může také vyžadovat ExpressRoute nebo připojení site-to-site VPN mezi virtuální síť Azure a místní sítě.
* Poplatky za platí pro všechny přenosy z virtuálního počítače Azure (odchozí provoz). Pokud náklady na řízení faktor, se doporučuje nasazení uzly Proxy webových aplikací v Azure, a služby AD FS servery místní. Pokud jsou servery služby AD FS nasazená na virtuálních počítačích Azure také, dodatečné poplatky budou vám být účtovány ověřuje místní uživatele. Výchozí přenos způsobuje náklady bez ohledu na to, zda je procházení ExpressRoute nebo připojení site-to-site VPN.
* Pokud se rozhodnete použít Azure nativní server možnosti vyrovnávání zatížení pro vysokou dostupnost serverů služby AD FS, Všimněte si, že vyrovnávání zatížení poskytuje sondy, které se používají k určení stavu virtuálních počítačů v rámci cloudové služby. V případě virtuálních počítačích Azure (na rozdíl od role web nebo worker) je nutné použít vlastní test paměti vzhledem k tomu, že agent, který reaguje na výchozí sondy se nenachází na virtuálních počítačích Azure. Pro jednoduchost, můžete použít vlastní test paměti TCP – to vyžaduje pouze, k určení stavu virtuálního počítače se úspěšně navázat připojení TCP (segment TCP SYN odesílají a odpověděla s segment TCP SYN ACK). Můžete nakonfigurovat vlastní test paměti k použít libovolný port TCP, ke které jsou aktivně naslouchá virtuálních počítačů.

> [!NOTE]
> Počítače, které potřebují ke zveřejnění stejnou sadu porty přímo k Internetu (například port 80 a 443) nelze sdílet stejné cloudové služby. Proto se doporučuje vytvořit vyhrazené cloudové služby pro servery systému Windows Server AD FS, aby se zabránilo potenciální překrývá mezi požadavky na porty pro aplikace a služby Windows Server AD FS.
> 
> 

## <a name="deployment-scenarios"></a>Scénáře nasazení
Následující část popisuje scénáře nasazení běžná k upozornění na důležité informace, které musí vzít v úvahu. Každý scénář má odkazy na další podrobnosti o rozhodnutí a faktory vzít v úvahu.

1. [Služba AD DS: Nasazení aplikace podporující AD DS s žádný požadavek na připojení k podnikové síti](#BKMK_CloudOnly)
   
    Například služby SharePoint internetového je nasazen na virtuální počítač Azure. Aplikace nemá žádné závislosti v prostředcích podnikové sítě. Tato aplikace vyžaduje Windows Server AD DS, ale nevyžaduje podnikové služby AD DS pro systém Windows Server.
2. [Službu AD FS: Rozšíření deklaracemi místní front-endu aplikace k Internetu](#BKMK_CloudOnlyFed)
   
    Například deklaracemi identity aplikace, který byl úspěšně nasadit místně a používat firemní uživatelé musí být přístupný z Internetu. Aplikace musí nelze přistupovat přímo přes Internet pomocí své vlastní firemní identity obchodními partnery a také existující podnikovým uživatelům.
3. [Služby AD DS: Nasazení Windows Server AD DS podporující aplikaci, která vyžaduje připojení k podnikové síti](#BKMK_HybridExt)
   
    Například aplikace podporující LDAP, který podporuje ověřování integrované v systému Windows a Windows Server AD DS se používá jako úložiště pro data konfigurace a profil uživatele je nasazen na virtuální počítač Azure. Je žádoucí, aby aplikace využít existující podnikové systému Windows Server AD DS a poskytovat jednotné přihlašování. Aplikace není pracujícím s deklaracemi.

### <a name="BKMK_CloudOnly"></a>1. Služba AD DS: Nasazení aplikace podporující AD DS s žádný požadavek na připojení k podnikové síti
![Nasazení služby AD DS jenom pro cloud](media/active-directory-deploying-ws-ad-guidelines/ADDS_cloud.png)
**obrázek 1**

#### <a name="description"></a>Popis
SharePoint je nasazen na virtuální počítač Azure a aplikace nemá žádné závislosti v prostředcích podnikové sítě. Aplikace systému Windows Server AD DS však nevyžaduje *není* vyžadují podnikové služby Windows Server AD DS. Vzhledem k tomu, že jsou uživatelé samoobslužné zřízené prostřednictvím aplikace do domény systému Windows Server AD DS, která je také hostovaná v cloudu na virtuálních počítačích Azure, je požadováno žádné protokolu Kerberos nebo federované vztahy důvěryhodnosti.

#### <a name="scenario-considerations-and-how-technology-areas-apply-to-the-scenario"></a>Aspekty scénář a jak používat technologie oblastí se scénářem
* [Topologie sítě](#BKMK_NetworkTopology): vytvoření virtuální sítě Azure bez připojení mezi různými místy (také označované jako připojení site-to-site).
* [Konfigurace nasazení řadiče domény](#BKMK_DeploymentConfig): nasaďte nový řadič domény do nové, jednou doménou, doménové struktury Windows Server Active Directory. To by měly být nasazeny společně se serverem DNS systému Windows.
* [Topologie lokality služby Active Directory pro Windows Server](#BKMK_ADSiteTopology): používal výchozí server Windows Server Active Directory (všechny počítače se bude v Default-First-Site-Name).
* [Adresování IP a DNS](#BKMK_IPAddressDNS):
  
  * Nastavení statické IP adresy pro řadič domény pomocí rutiny Set-AzureStaticVNetIP Azure PowerShell.
  * Instalace a konfigurace serveru Windows Server DNS na počet řadičů domény v Azure.
  * Konfigurujte vlastnosti virtuální síť s názvem a IP adresu virtuálního počítače, který je hostitelem role řadiče domény a DNS serveru.
* [Globální katalog](#BKMK_GC): prvního řadiče domény v doménové struktuře musí být server globálního katalogu. Další řadiče domény by také nakonfigurovat jako GC, protože v doménová struktura jedinou doménu, globálního katalogu nevyžaduje žádné další práce z řadiče domény.
* [Umístění databáze systému Windows Server AD DS a adresáře SYSVOL](#BKMK_PlaceDB): Přidat datový disk do řadiče domény se spuštěným jako virtuální počítače Azure, aby bylo možné uložit databázi systému Windows Server Active Directory, protokoly a adresáře SYSVOL.
* [Zálohování a obnovení](#BKMK_BUR): Určete, kam chcete ukládat zálohy stavu systému. V případě potřeby přidáte další datový disk k virtuálnímu počítači řadiče domény pro ukládání záloh.

### <a name="BKMK_CloudOnlyFed"></a>2 AD FS: rozšíření deklaracemi místní front-endu aplikace k Internetu
![Federaci se připojení mezi různými místy](media/active-directory-deploying-ws-ad-guidelines/Federation_xprem.png)
**na obrázku 2**

#### <a name="description"></a>Popis
Deklaracemi identity aplikace, která byla úspěšně nasadit místně a firemní uživatelé používat, musí se přístupný přímo z Internetu. Aplikace slouží jako front-end webové k databázi SQL, do které ukládá data. SQL Server používá aplikace jsou také umístěny v podnikové síti. Dva STSs systému Windows Server AD FS a nástroj na Vyrovnávání zatížení se nasadit místně pro poskytnutí přístupu k podnikovým uživatelům. Nyní aplikaci musí být kromě přistupovat přímo přes Internet obchodními partnery pomocí své vlastní firemní identity a také existující podnikovým uživatelům.

Ve snaze zjednodušit a vyhovovaly nasazení a konfigurace tento nový požadavek, bude rozhodnuto, že dva další webové frontends a dva proxy servery služby Windows Server AD FS nainstalovat na virtuálních počítačích Azure. Všechny čtyři virtuální počítače se zveřejní přímo k Internetu a budou poskytovat připojení k místní síti pomocí Azure Virtual Network site-to-site VPN funkce.

#### <a name="scenario-considerations-and-how-technology-areas-apply-to-the-scenario"></a>Aspekty scénář a jak používat technologie oblastí se scénářem
* [Topologie sítě](#BKMK_NetworkTopology): vytvoření virtuální sítě Azure a [nakonfigurovat připojení mezi různými místy](../vpn-gateway/vpn-gateway-site-to-site-create.md).
  
  > [!NOTE]
  > Pro jednotlivé certifikáty, Windows Server AD FS Ujistěte se, že adresa URL definované v šabloně certifikátu a výsledné certifikáty dosažitelný z instance systému Windows Server AD FS spuštěné v Azure. To může vyžadovat připojení mezi různými místy pro částí vaši infrastrukturu PKI. Pro příklad Pokud koncový bod seznamu CRL je založený na LDAP a je hostovaná výhradně na místě, pak mezi různými místy, připojení se bude vyžadovat. Pokud to není žádoucí, může být nutné použít certifikáty vydané certifikační Autority, jejichž seznam CRL je přístupný prostřednictvím Internetu.
  > 
  > 
* [Konfigurace cloudových služeb](#BKMK_CloudSvcConfig): Zajistěte, abyste měli dva cloudové služby v pořadí poskytují dvě vyrovnáváním zatížení virtuální IP adresy. První cloudové služby virtuální IP adresy budete přesměrováni na dva systému Windows Server AD FS proxy virtuální počítače na porty 80 a 443. Windows Server AD FS proxy virtuálních počítačů bude nakonfigurován tak, aby odkazoval na IP adresu služby Vyrovnávání zatížení na místě tohoto naskenováno předních STSs Windows Server AD FS. Druhý cloudové služby virtuální IP adresy budete přesměrováni na dva virtuální počítače znovu spustit front-endu webové na porty 80 a 443. Nakonfigurujte vlastní test paměti k zajištění, že pro vyrovnávání zatížení pouze přesměruje přenosy na funkční systému Windows Server AD FS proxy serveru a webového front-endu virtuálních počítačů.
* [Konfigurace federačního serveru](#BKMK_FedSrvConfig): konfigurovat Windows Server AD FS jako federační server (STS) ke generování tokenů zabezpečení pro doménovou strukturu Windows Server Active Directory vytvořené v cloudu. Nastavit federačních vztahy důvěryhodnosti zprostředkovatele deklarací identity s různé partnery, kterou chcete akceptovat identity pocházející z a nakonfigurovat různé aplikace, kterou chcete vygenerovat tokeny pro vztahy důvěryhodnosti předávající strany.
  
    Ve většině scénářů Windows Server AD FS proxy servery jsou nasazeny v postavení internetového z bezpečnostních důvodů při jejich systému Windows Server AD FS federation protějšky zůstat izolované od přímé připojení k Internetu. Bez ohledu na to váš scénář nasazení musíte nakonfigurovat cloudové služby s virtuální IP adresy, které zajistí veřejně vystavené IP adresu a port, který se bude moct Vyrovnávání zatížení mezi dvě instance systému Windows Server AD FS služby tokenů zabezpečení nebo instance proxy.
* [Konfigurace vysoké dostupnosti systému Windows Server AD FS](#BKMK_ADFSHighAvail): je třeba nasadit farmu služby Windows Server AD FS se aspoň dva servery pro převzetí služeb při selhání a vyrovnávání zatížení. Můžete zvažte možnost použití pro konfigurační data služby AD FS Windows serveru interní databáze Windows (WID) a použít funkci Vyrovnávání zatížení interní systému Azure k distribuci příchozích požadavků na serverech ve farmě.

Další informace najdete v tématu [Průvodce nasazením služby AD DS](https://technet.microsoft.com/library/cc753963).

### <a name="BKMK_HybridExt"></a>3. Služby AD DS: Nasazení Windows Server AD DS podporující aplikaci, která vyžaduje připojení k podnikové síti
![Nasazení služby AD DS mezi různými místy](media/active-directory-deploying-ws-ad-guidelines/ADDS_xprem.png)
**obrázek 3**

#### <a name="description"></a>Popis
Aplikace podporující LDAP je nasazen na virtuální počítač Azure. Podporuje ověřování integrované v systému Windows a Windows Server AD DS se používá jako úložiště pro konfiguraci a uživatelská data profilu. Cílem je pro aplikace pro využít existující podnikové AD DS systému Windows Server a poskytovat jednotné přihlašování. Aplikace není pracujícím s deklaracemi. Uživatelé se taky potřebujete přístup k aplikaci přímo z Internetu. K optimalizaci výkonu a nákladů, bude rozhodnuto, že dva řadiče domény Další, které jsou součástí podnikové doméně nasadit souběžně s aplikací v Azure.

#### <a name="scenario-considerations-and-how-technology-areas-apply-to-the-scenario"></a>Aspekty scénář a jak používat technologie oblastí se scénářem
* [Topologie sítě](#BKMK_NetworkTopology): vytvoření virtuální sítě Azure s [připojení mezi různými místy](../vpn-gateway/vpn-gateway-site-to-site-create.md).
* [Metoda instalace](#BKMK_InstallMethod): nasazovat repliky řadičů domény z domény Windows Server Active Directory společnosti. Pro repliky řadiče domény můžete do virtuálního počítače nainstalujte Windows Server AD DS a volitelně můžete použít funkci instalovat z média (IFM) ke snížení objemu dat, která musí být replikována na nový řadič domény během instalace. Podívejte se kurz [instalace repliky řadiče domény služby Active Directory v Azure](active-directory-install-replica-active-directory-domain-controller.md). I když použijete instalaci z média, může být efektivnější vytvářet virtuální řadič domény na místní a přesunout do cloudu namísto replikace během instalace systému Windows Server AD DS a celý virtuální pevný Disk (VHD). Pro zabezpečení se doporučuje odstranit virtuální pevný disk z místní sítě, jakmile byla zkopírována do Azure.
* [Topologie lokality služby Active Directory pro Windows Server](#BKMK_ADSiteTopology): vytvoření nového webu Azure v Active Directory Sites and Services. Vytvoření objektu Windows Server Active Directory podsítě představují virtuální síť Azure a do lokality přidat podsíť. Vytvořte nové propojení lokalit, která zahrnuje novou lokalitu Azure a lokality, ve kterém se nachází, aby bylo možné ovládat a optimalizovat Windows Server Active Directory provoz do a z Azure virtuální síť Azure koncovým bodem VPN.
* [Adresování IP a DNS](#BKMK_IPAddressDNS):
  
  * Nastavení statické IP adresy pro řadič domény pomocí rutiny Set-AzureStaticVNetIP Azure PowerShell.
  * Instalace a konfigurace serveru Windows Server DNS na počet řadičů domény v Azure.
  * Konfigurujte vlastnosti virtuální síť s názvem a IP adresu virtuálního počítače, který je hostitelem role řadiče domény a DNS serveru.
* [Geograficky distribuovaná řadiče domény](#BKMK_DistributedDCs): podle potřeby nakonfigurujte další virtuální sítě. Pokud vaše topologie lokality služby Active Directory vyžaduje, aby řadiče domény v zeměpisných oblastí, které odpovídají různých oblastech Azure, než kterou chcete vytvořit lokalit služby Active Directory odpovídajícím způsobem.
* [Řadiče domény jen pro čtení](#BKMK_RODC): můžete nasadit řadič v Azure lokality, v závislosti na požadavcích na provádění zápisu operace u řadiče domény a kompatibility aplikací a služeb v síti s řadičů jen pro čtení. Další informace o kompatibilitě aplikací najdete v tématu [příručka kompatibility aplikace řadiče domény jen pro čtení](https://technet.microsoft.com/library/cc755190).
* [Globální katalog](#BKMK_GC): GC jsou potřeba k žádosti o přihlášení služby v doménových strukturách s více doménami. Pokud nenasazujete serverem globálního katalogu v lokalitě Azure, se budou vynakládá odchozí přenosy podle požadavků na ověření způsobit dotazy GC v jiných lokalitách. Chcete-li minimalizovat, aby provoz, můžete povolit univerzální skupinu členství ukládání do mezipaměti pro webu Azure v Active Directory Sites and Services.
  
    Pokud nasadíte globální Katalog, nakonfigurujte propojení lokalit a náklady na propojení sítí tak, aby globálního katalogu v lokalitě Azure není upřednostňovaný jako zdrojový řadič domény pomocí jiných GC, které je potřeba replikovat stejnou částečné doménových oddílů.
* [Umístění databáze systému Windows Server AD DS a adresáře SYSVOL](#BKMK_PlaceDB): Přidat datový disk do řadiče domény se spuštěným na virtuálních počítačích Azure, aby bylo možné uložit databázi systému Windows Server Active Directory, protokoly a adresáře SYSVOL.
* [Zálohování a obnovení](#BKMK_BUR): Určete, kam chcete ukládat zálohy stavu systému. V případě potřeby přidáte další datový disk k virtuálnímu počítači řadiče domény pro ukládání záloh.

## <a name="deployment-decisions-and-factors"></a>Rozhodnutí o nasazení a faktory
Tato tabulka shrnuje technologické oblasti Windows Server Active Directory, které může mít v uvedených scénářů a odpovídající rozhodnutí, která vzít v úvahu, s odkazy na další rozpis naleznete níže. Některé oblasti technologie nemusí být použitelná pro každý scénář nasazení a některé oblasti technologie může být pro scénář nasazení větší význam než ostatní technologické oblasti.

Například pokud nasadíte ve virtuální síti repliky řadiče domény a doménové struktury má pouze jednu doménu, potom se rozhodnete nasadit server globálního katalogu v takovém případě nebude pro tento scénář nasazení důležité protože nevytvoří žádné další replikace požadavky. Na druhé straně Pokud doménová struktura obsahuje několik domén, pak rozhodnutí o nasazení na globální katalog ve virtuální síti může ovlivnit dostupnou šířku pásma, výkon, ověřování, vyhledávání v adresáři a tak dále.

| Oblast technologie Windows Server Active Directory | Rozhodnutí | Faktory |
| --- | --- | --- |
| [Topologie sítě](#BKMK_NetworkTopology) |Vytvoření virtuální sítě? |<li>Požadavky na přístup k prostředkům Corp</li> <li>Authentication</li> <li>Správa účtů</li> |
| [Konfigurace nasazení řadiče domény](#BKMK_DeploymentConfig) |<li>Nasazení samostatné doménové struktuře bez žádné vztahy důvěryhodnosti?</li> <li>Nasazení nové doménové struktury s federací?</li> <li>Nasazení nové doménové struktury s vztah důvěryhodnosti doménové struktury Windows Server Active Directory nebo pomocí protokolu Kerberos?</li> <li>Doménová struktura Corp rozšířit nasazení repliky řadiče domény?</li> <li>Doménová struktura Corp rozšířit nasazení nové podřízené domény nebo větve doménové struktury?</li> |<li>Zabezpečení</li> <li>Dodržování předpisů</li> <li>Náklady</li> <li>Odolnost proti chybám a odolnost proti chybám</li> <li>Kompatibilita aplikací</li> |
| [Topologie lokality služby Active Directory pro Windows Server](#BKMK_ADSiteTopology) |Jak můžete nakonfigurovat podsítě, weby a spojení sítí s Azure Virtual Network pro provoz a minimalizuje náklady? |<li>Definice podsítě a lokality</li> <li>Vlastnosti propojení lokalit a upozornění na změnu</li> <li>Komprese replikace</li> |
| [Adresování IP a DNS](#BKMK_IPAddressDNS) |Postup konfigurace IP adresy a překlad? |<li>Použijte rutinu Set-AzureStaticVNetIP použití přiřadit statickou IP adresu</li> <li>Nainstalujte server DNS systému Windows Server a nakonfigurujte vlastnosti virtuální sítě s názvem a IP adresu virtuálního počítače, který je hostitelem role řadiče domény a DNS serveru</li> |
| [Geograficky distribuovaná řadiče domény](#BKMK_DistributedDCs) |Jak replikovat do řadiče domény na samostatné virtuální sítě? |Pokud vaše topologie lokality služby Active Directory vyžaduje, aby řadiče domény v zeměpisných oblastí, které odpovídá různých oblastech Azure, než kterou chcete vytvořit lokalit služby Active Directory odpovídajícím způsobem. [Nakonfigurujte virtuální síť k virtuální síti připojení](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md) k replikaci mezi řadiči domény v samostatných virtuálních sítích. |
| [Řadiče domény jen pro čtení](#BKMK_RODC) |Použít jen pro čtení nebo zapisovatelného řadiče domény? |<li>HBI nebo PII atributy filtru</li> <li>Filtrovat tajné klíče</li> <li>Odchozí přenosy limit</li> |
| [Globální katalog](#BKMK_GC) |Nainstalovat globálního katalogu? |<li>Pro doménovou strukturu jednou doménou Ujistěte se, GC všechny řadiče domény</li> <li>Pro doménová struktura GC jsou nutné k ověření.</li> |
| [Metoda instalace](#BKMK_InstallMethod) |Postup instalace řadiče domény v Azure? |Buď: <li>Instalace služby AD DS pomocí prostředí Windows PowerShell nebo nástroj Dcpromo</li> <li>Přesunout virtuální pevný disk jako místní virtuální řadič domény</li> |
| [Umístění databáze systému Windows Server AD DS a adresáře SYSVOL](#BKMK_PlaceDB) |Kam se mají ukládat systému Windows Server AD DS databáze, protokoly a adresáře SYSVOL? |Změňte Dcpromo.exe výchozí hodnoty. Tyto důležité soubory služby Active Directory *musí* umístit do Azure datových disků místo disky operačního systému, které implementují ukládání do mezipaměti. |
| [Zálohování a obnovení](#BKMK_BUR) |Jak chránit a obnovit data? |Vytváření záloh stavu systému |
| [Konfigurace federačního serveru](#BKMK_FedSrvConfig) |<li>Nasazení nové doménové struktury s federací v cloudu?</li> <li>Nasazení služby AD FS na místě a vystavit proxy server v cloudu?</li> |<li>Zabezpečení</li> <li>Dodržování předpisů</li> <li>Náklady</li> <li>Přístup k aplikacím obchodní partnery</li> |
| [Konfigurace cloudových služeb](#BKMK_CloudSvcConfig) |Cloudová služba je implicitně nasadit při prvním vytvoření virtuálního počítače. Je třeba nasadit další cloudové služby? |<li>Virtuální počítač nebo virtuální počítače vyžaduje přímé ohrožení k Internetu?</li> <li> Vyžaduje službu Vyrovnávání zatížení?</li> |
| [Požadavky na federační server pro veřejné a privátní IP adres (dynamické IP adresy a virtuální IP adresy)](#BKMK_FedReqVIPDIP) |<li>Instance systému Windows Server AD FS musí přejít přímo z Internetu?</li> <li>Vyžaduje aplikaci nasazuje v cloudu vlastní internetový IP adresu a port?</li> |Vytvořte jeden cloudových služeb pro každý virtuální IP adresy, který je požadován pro vaše nasazení |
| [Konfigurace vysoké dostupnosti systému Windows Server AD FS](#BKMK_ADFSHighAvail) |<li>Počet uzlů v mé serverové farmy služby Windows Server AD FS?</li> <li>Kolik uzly k nasazení v mé farmy služby Windows Server AD FS proxy?</li> |Odolnost proti chybám a odolnost proti chybám |

### <a name="BKMK_NetworkTopology"></a>Topologie sítě
Aby bylo možné splnit konzistence adresu IP a DNS požadavky systému Windows Server AD DS, je nutné nejprve vytvořit [virtuální síť Azure](../virtual-network/virtual-networks-overview.md) a k němu připojí virtuální počítače. Při jeho vytváření, musí rozhodnout, zda případně rozšířit připojení k podnikové síti místní, který transparentně virtuální počítače Azure připojí k místní počítače – můžete toho dosáhnout pomocí tradičních technologií, VPN a vyžaduje, aby koncový bod VPN být odhalen na okraje podnikové sítě. To znamená sítě VPN je zahájena z Azure k podnikové síti, ne naopak.

Všimněte si, že další poplatky při rozšiřování virtuální sítě do místní sítě nad rámec standardní poplatky, které platí pro každý virtuální počítač. Konkrétně existují poplatky za dobu využití procesoru brány virtuální sítě Azure a pro odchozí přenosy generované každý virtuální počítač, který komunikuje s místní počítače prostřednictvím sítě VPN. Další informace o poplatky provoz sítě najdete v tématu [Azure ceny na přehled](http://azure.microsoft.com/pricing/).

### <a name="BKMK_DeploymentConfig"></a>Konfigurace nasazení řadiče domény
Způsob konfigurace řadiče domény závisí na požadavky na službu, že kterou chcete spustit v Azure. Například můžete nasadit nové doménové struktuře, izolované z vlastní podnikové doménové struktury pro testování testování konceptu, nové aplikace nebo některých jiných krátkodobou projekt, který vyžaduje adresářové služby, ale nejsou specifické přístup k interním firemním prostředkům.

Jako výhody izolované doménové struktuře, které řadiče domény se nereplikuje s místní řadičů domény, výsledkem je generováno systémem samostatně, méně odchozí síťový provoz přímo snížení nákladů. Další informace o poplatky provoz sítě najdete v tématu [Azure ceny na přehled](http://azure.microsoft.com/pricing/).

Například předpokládejme, že máte požadavky na ochranu osobních údajů pro službu, ale služba závisí na přístup k vaší interní Windows Server Active Directory. Pokud jste oprávněni data hostitele pro službu v cloudu, můžete nasadit novou podřízenou doménu pro svoji interní doménovou strukturu v Azure. V takovém případě můžete nasadit řadič domény pro nové podřízené domény (bez globálního katalogu, abyste pomohli adresu aspekty ochrany osobních údajů). Tento scénář, společně s repliku nasazení řadiče domény, vyžaduje virtuální sítě pro připojení k síti s vaší místní řadiče domény.

Pokud vytváříte novou doménovou strukturu, vyberte, zda chcete použít [vztahy důvěryhodnosti služby Active Directory](https://technet.microsoft.com/library/cc771397) nebo [federačních vztahy důvěryhodnosti](https://technet.microsoft.com/library/dd807036). Vyrovnávat požadavky závisí na kompatibility, zabezpečení, dodržování předpisů, náklady a odolnost proti chybám. Chcete-li například využít výhod [selektivní ověřování](https://technet.microsoft.com/library/cc755844) můžete zvolit nasazení nové doménové struktury v Azure a vytvoření vztahu důvěryhodnosti Windows Server Active Directory mezi místní doménové struktury a doménové struktury cloudu. Pokud je aplikace používající deklarace identity, ale můžete nasadit federačních vztahy důvěryhodnosti místo vztahy důvěryhodnosti doménové struktury služby Active Directory. Dalším faktorem bude náklady na replikaci více dat tím, že rozšíří vaše místní Windows Server Active Directory do cloudu nebo vytvářet více odchozí přenosy dat v důsledku ověřování a zátěž dotazu.

Požadavky pro dostupnost a odolnost proti chybám také ovlivnit volbu. Například pokud dojde k přerušení na odkaz, aplikace využívat vztah důvěryhodnosti protokolu Kerberos nebo federace důvěřovat, jsou všechny pravděpodobně zcela přerušený Pokud jste nasadili dostatečná infrastrukturu v Azure. Konfigurace alternativních nasazení, třeba repliky řadiče domény (zapisovatelného nebo řadičů jen pro čtení) zvýšit pravděpodobnost schopnost tolerovat odkaz výpadků.

### <a name="BKMK_ADSiteTopology"></a>Topologie lokality služby Active Directory pro Windows Server
Je třeba správně definovat lokalit a propojení lokalit, chcete-li optimalizovat přenosy a minimalizovat náklady. Lokality, propojení lokalit a podsítí ovlivnit topologii replikace mezi řadiče domény a tok provozu ověřování. Vezměte v úvahu následující poplatky za provozu a pak nasadit a nakonfigurovat podle požadavků vašemu scénáři nasazení řadiče domény:

* Je nominální poplatek za hodinu pro bránu samotnou:
  
  * Může být spuštění a zastavení podle potřeby
  * Pokud je zastavena, virtuální počítače Azure jsou izolované od podnikové sítě
* Příchozí provoz je zdarma
* Se účtuje odchozí přenosy, podle [Azure ceny na přehled](http://azure.microsoft.com/pricing/). Vlastnosti lokality propojení mezi místními servery a sítěmi cloudu můžete optimalizovat následujícím způsobem:
  
  * Pokud používáte více virtuálních sítí, propojení lokalit a jejich náklady vhodně nakonfigurujte zabránit upřednostněním webu Azure na jeden, který může poskytovat stejné úrovně služeb bez poplatků systému Windows Server AD DS. Můžete také zakázat most všechny lokality možnost propojení (BASL), (která je povolena ve výchozím nastavení). Tím se zajistí, že pouze přímo připojené lokality replikovat sebou. Řadiče domény v přechodně připojené lokality již nejsou schopny replikace navzájem přímo, ale musí replikovat pomocí běžných web nebo weby. Pokud z nějakého důvodu k dispozici zprostředkující lokalit, replikace mezi řadiče domény v lokalitách přechodně připojené nedojde, i v případě, že připojení mezi lokalitami je k dispozici. Nakonec kde žádoucí zůstanou části chování, díky přechodné replikaci, vytvořte lokality mostů propojení lokalit, které obsahují odpovídající propojení lokalit a webů, jako podnikové sítě na místě.
  * [Konfigurace náklady na propojení sítí](https://technet.microsoft.com/library/cc794882) správně aby se zabránilo neúmyslnému provoz. Například pokud **zkuste další nejbližší lokalitu** nastavení povoleno, zkontrolujte virtuální síťové lokality nejsou další co nejbližší zvýšením náklady na propojení lokalit objektu, který připojí webu Azure zpět k podnikové síti.
  * Konfigurace propojení lokalit [intervaly](https://technet.microsoft.com/library/cc794878) a [plány](https://technet.microsoft.com/library/cc816906) závislosti na požadavcích konzistence a počet změn objektů. Zarovnává plán replikace s latencí tolerance. Řadiče domény replikovat pouze poslední stav hodnoty, takže snížení intervalu replikace můžete ušetřili náklady, pokud je objekt dostatečná frekvence změny.
* Pokud je minimalizovat náklady prioritu, zajistěte replikace je naplánována a oznámení o změně není povolen. Toto je výchozí konfigurace při replikaci mezi lokalitami. Toto není důležité, pokud nasazujete řadič ve virtuální síti, protože RODC nebudou replikovat změny odchozí. Ale pokud nasazujete zapisovatelný řadič domény, můžete Ujistěte se, že odkaz na lokalitu není nakonfigurován pro replikaci s nepotřebné frekvence aktualizace. Pokud nasadíte server globálního katalogu (GC), ujistěte se, že každé jiné lokality, která obsahuje globální Katalog replikuje oddílů domény ze zdrojového řadiče domény v lokalitě, která je propojená s propojení lokalit nebo lokality odkazy, které mají nižší náklady než globální Katalog na webu Azure.
* Je možné stále dále snižuje objem síťového přenosu vytvořená replikací mezi lokalitami tak, že změníte algoritmus komprese replikace. Řídí algoritmus komprese algoritmus komprese HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\NTDS\Parameters\Replicator položky REG_DWORD registru. Výchozí hodnota je 3, která koreluje s algoritmus Xpress komprimovat. Můžete změnit hodnotu na 2, který algoritmus se změní na MSZip. Ve většině případů to zvýší kompresi, ale dělá to tak za cenu využití procesoru. Další informace najdete v tématu [jak služby Active Directory funguje replikační topologie](https://technet.microsoft.com/library/cc755994).

### <a name="BKMK_IPAddressDNS"></a>Adresování IP a DNS
Virtuální počítače Azure jsou ve výchozím nastavení přidělené "pronajaté DHCP adresy". Protože dynamické adresy virtuální sítě Azure zachovat s virtuálním počítačem po dobu jeho existence virtuálního počítače, jsou splněny požadavky na služby Windows Server AD DS.

Výsledkem je když použijete dynamickou adresu v Azure, jste v vliv pomocí statické IP adresy, protože je směrovatelné dobu zapůjčení, a dobu zapůjčení rovná životnost cloudové služby.

Dynamické adresy je však zrušeno, pokud je virtuální počítač vypnout. Chcete-li zabránit navrácena IP adresu, můžete [přiřadit statickou IP adresu pomocí Set-AzureStaticVNetIP](http://social.technet.microsoft.com/wiki/contents/articles/23447.how-to-assign-a-private-static-ip-to-an-azure-vm.aspx).

Pro překlad IP adres nasadit vlastní (nebo využívat stávající) infrastruktuře serverů DNS; Azure DNS nevyhovuje potřebám řešení pokročilé název služby systému Windows Server AD DS. Například ho nepodporuje dynamické záznamy SRV a tak dále. Překlad je položky kritické konfigurace pro klienty připojené k doméně a řadiče domény. Řadiče domény musí být schopný registrace záznamy o prostředcích a překlad záznamy o prostředcích další řadič domény.
Odolnost proti chybám a výkonu z důvodů je optimální pro instalaci služby Windows Server DNS na řadičích domény spuštěné v Azure. Potom nakonfigurujte vlastnosti virtuální síť Azure s názvem a IP adresu serveru DNS. Při spuštění nástroje ostatní virtuální počítače ve virtuální síti, jejich překladač nastavení klienta DNS budou nakonfigurovány pomocí serveru DNS v rámci dynamické přidělování IP adres.

> [!NOTE]
> Nemůže připojit k místní počítače k doméně služby Active Directory systému Windows Server AD DS, která je hostovaná v Azure přímo přes Internet. Požadavky na port pro služby Active Directory a připojení k doméně operace vykreslit ho nepraktické přímo vystavit potřebné porty a v platnosti, jako celý řadič domény k Internetu.
> 
> 

Virtuální počítače zaregistrovat jejich název DNS automaticky při spuštění nebo když dojde ke změně názvu.

Další informace o tomto příkladu a další příklad, který ukazuje, jak zřídit první virtuální počítač a na něm nainstalovat službu AD DS najdete v tématu [instalaci nové doménové struktury služby Active Directory v Microsoft Azure](active-directory-new-forest-virtual-machine.md). Další informace o používání prostředí Windows PowerShell najdete v tématu [nainstalovat Azure PowerShell](/powershell/azureps-cmdlets-docs) a [rutiny pro správu Azure](/powershell/module/azurerm.compute/#virtual_machines).

### <a name="BKMK_DistributedDCs"></a>Geograficky distribuovaná řadiče domény
Azure nabízí výhody při hostování více řadičů domény v různých virtuálních sítích:

* Odolnost proti chybám více lokalit
* Fyzické blízkosti firemních poboček (nižší latenci)

Informace o konfiguraci přímou komunikaci mezi virtuálními sítěmi najdete v tématu [konfigurace virtuální sítě pro připojení k virtuální síti](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

### <a name="BKMK_RODC"></a>Řadiče domény jen pro čtení
Musíte zvolit, jestli nasazení řadiče domény jen pro čtení či pro zápis. Vám může být sklon k nasazení řadičů jen pro čtení, protože nebude mít fyzické kontrolu nad je však řadičů jen pro čtení jsou určené k nasazení v umístění, kde jejich fyzické zabezpečení jsou v ohrožení, jako je firemní pobočky.

Azure nemá k dispozici fyzické bezpečnostní riziko pobočkové sítě, ale stále může prokázat řadičů jen pro čtení, být cenově efektivnější, protože funkce, které poskytují představují vhodné řešení do těchto prostředí, i když velmi různých důvodů. Například pro mít žádná odchozí replikace a jsou dokáže selektivně naplnit tajné klíče (hesla). Nevýhodou nedostatečná těchto tajných klíčů může vyžadovat odchozí přenosy na vyžádání a ověří, zda uživatel nebo počítač ověří. Ale tajných klíčů můžete selektivně naplněnými a uložili do mezipaměti.

Řadičů jen pro čtení poskytnout další výhody v a kolem HBI a PII obavy, protože můžete přidat, že atributy, které obsahují citlivá data do RODC filtrovat sadu atributů (DM). DM je přizpůsobitelnou sadu atributů, které nejsou replikována do řadičů jen pro čtení. Jako pojistku můžete použít DM, v případě, že nejsou povoleny nebo nechcete, aby k ukládání identifikovatelné osobní údaje nebo HBI v Azure. Další informace najdete v tématu [RODC filtrovat sadu atributů [(https://technet.microsoft.com/library/cc753459)].

Ujistěte se, že aplikace bude kompatibilní s řadičů jen pro čtení, kterou plánujete použít. Mnoho aplikací systému Windows Server Active Directory povolena funkce fungují dobře u řadičů jen pro čtení, ale některé aplikace mohou provádět neefektivnímu nebo nezdaří, pokud nemají přístup k zapisovatelný řadič domény. Další informace najdete v tématu [kompatibility příručka aplikace pro řadiče domény jen pro čtení](https://technet.microsoft.com/library/cc755190).

### <a name="BKMK_GC"></a>Globální katalog
Je třeba vybrat, zda se má nainstalovat na globální katalog (GC). V jedné doméně doménové struktury měli byste nakonfigurovat všechny řadiče domény jako servery globálního katalogu. Protože bude mít žádný další replikace provoz se nebude zvýšit náklady.

V doménové struktuře s více doménami jsou nezbytné k rozšíření univerzálního členství ve skupinách během procesu ověřování GC. Pokud nenasazujete globální Katalog, vygeneruje úlohy ve virtuální síti, které ověřují se řadič domény v Azure nepřímo provozu odchozí ověřování k dotazování GC místní při každém pokusu o ověření.

Náklady spojené s GC jsou méně předvídatelný, protože hostují všechny domény (v rámci). Pokud úlohy hostitelem služby internetového a ověřuje uživatele ve službě Windows Server AD DS, může být náklady na úplně nepředvídatelné. Chcete-li omezit GC dotazy mimo cloud web během ověřování, můžete [povolit univerzální skupiny členství ukládání do mezipaměti](https://technet.microsoft.com/library/cc816928).

### <a name="BKMK_InstallMethod"></a>Metoda instalace
Je třeba vybrat postup instalace řadiče domény ve virtuální síti:

* Povýšení nového řadiče domény. Další informace najdete v tématu [nainstalovat novou doménovou strukturu služby Active Directory na virtuální síť Azure](active-directory-new-forest-virtual-machine.md).
* Přesunete virtuální pevný disk jako místní virtuální řadič domény do cloudu. V takovém případě musíte zajistit, že místní virtuální řadič domény je "přesunut," Ne "zkopírovaný" nebo "klonovaný."

Virtuální počítače pouze Azure můžete použijte u řadičů domény (na rozdíl od Azure "web" nebo "pracovník" role virtuálních počítačů). Jsou odolné a odolnost stavu pro řadič domény se vyžaduje. Virtuální počítače Azure jsou navrženy pro úlohy, jako například řadiče domény.

Nepoužívejte nástroj SYSPREP k nasazení nebo klonování řadiče domény. Možnost klonování řadičů domény se pouze k dispozici od verze Windows serveru 2012. Funkce klonování vyžaduje podporu VMGenerationID v základní hypervisoru. Technologie Hyper-V ve Windows serveru 2012 a Azure virtuálních sítích obě podporují VMGenerationID, dodavatelů softwaru třetích stran virtualizace.

### <a name="BKMK_PlaceDB"></a>Umístění databáze systému Windows Server AD DS a adresáře SYSVOL
Určete, kam se najít databázi systému Windows Server AD DS, protokoly a adresáře SYSVOL. Musí být nasazený v Azure datových disků.

> [!NOTE]
> Azure datových disků jsou omezeny na 4 TB.
> 
> 

Ve výchozím nastavení provádět dat diskových jednotek není mezipaměti zápisy. Data diskové jednotky, které jsou připojené k virtuálnímu počítači pomocí zápisu prostřednictvím ukládání do mezipaměti. Zápis prostřednictvím mezipaměti díky, že zápis se zaměřuje na odolná úložiště Azure, před transakce je dokončena z pohledu operačního systému Virtuálního počítače. Poskytuje odolnost za cenu mírně nižší zápisy.

To je důležité pro Windows Server AD DS, protože zápis disku ukládání do mezipaměti by způsobila neplatnost předpoklady řadič domény. Windows Server AD DS pokus zakázání ukládání do mezipaměti, ale je na disk vstupně-výstupní operace systému ji přijmout. Selhání zakázání ukládání do mezipaměti, za určitých okolností zavést vrácení hodnot USN, které jsou výsledkem přetrvávání odstraněných objektů a jiné problémy.

Jako osvědčený postup pro virtuálních řadičů domény postupujte takto:

* Nastavte předvolby mezipaměti hostitele na Azure datový disk pro žádnou. To brání problémy s ukládání do mezipaměti pro operace služby AD DS.
* Uložit databázi, protokoly a SYSVOL na buď stejný datový disk nebo samostatné datových disků. Obvykle se jedná o samostatném disku, disk použitý pro samotný operační systém. Klíče takeaway je, že databáze systému Windows Server AD DS a adresáři SYSVOL nesmí být uložené na typ disku operačního systému Azure. Ve výchozím nastavení nainstaluje procesu instalace služby AD DS těchto součástí v % SystemRoot %, což není doporučeno pro Azure.

### <a name="BKMK_BUR"></a>Zálohování a obnovení
Uvědomte si, co je a není podporována pro zálohování a obnovení řadiče domény obecně, a přesněji řečeno, soubory, které jsou spuštěna ve virtuálním počítači. V tématu [zálohování a obnovení aspekty virtualizovaných řadičů domény](https://technet.microsoft.com/library/virtual_active_directory_domain_controller_virtualization_hyperv#backup_and_restore_considerations_for_virtualized_domain_controllers).

Vytvořte zálohování stavu systému pomocí pouze zálohovací software, který má konkrétně informace o zálohování požadavky pro Windows Server AD DS, jako je zálohování serveru.

Zkopírujte nebo klonovat soubory virtuálního pevného disku řadičů domény s namísto provádění pravidelných záloh. Obnovení někdy měli požadováno, v tom pomocí klonovaného nebo zkopírovaný virtuální pevné disky bez systému Windows Server 2012 a podporovaném hypervisoru zavedete bublinách USN.

### <a name="BKMK_FedSrvConfig"></a>Konfigurace federačního serveru
Konfigurace systému Windows Server AD FS federačních serverů (STSs) závisí částečně na tom, jestli potřebujete přístup k prostředkům v místní síti aplikace, které chcete nasadit v Azure.

Pokud aplikace splňovat následující kritéria, můžete nasadit aplikace v izolaci z vaší místní sítě.

* Přijmou tokenů zabezpečení SAML
* Jsou exposable k Internetu
* Není přístupu k prostředkům na místě

V takovém případě nakonfigurujte Windows Server AD FS STSs následujícím způsobem:

1. Konfigurace doménové struktury izolované jednou doménou v Azure.
2. Zajištění federovaný přístup k doménové struktuře pomocí konfigurace farmy federačních serverů služby AD FS Windows serveru.
3. Konfigurace služby systému Windows Server AD FS (farmy federačních serverů a farmu proxy federačních serverů) v místní doménové struktuře.
4. Vytvořte vztah důvěryhodnosti federace mezi místními a Azure instance služby AD FS Windows serveru.

Na druhé straně Pokud aplikace potřebuje přístup k místním prostředkům, vám může nakonfigurovat služby AD FS Windows serveru s aplikací v Azure následujícím způsobem:

1. Nakonfigurujte připojení mezi místní sítí a Azure.
2. Konfigurace farmy federačních serverů služby Windows Server AD FS v místní doménové struktuře.
3. Konfigurace farmy proxy federačních serverů služby AD FS Windows serveru na Azure.

Tato konfigurace nabízí výhodu v podobě snížení expozice místních prostředků, podobně jako konfigurace služby systému Windows Server AD FS s aplikacemi v hraniční síti.

Všimněte si, v obou těchto případech můžete vytvořit vztahy důvěryhodnosti s více poskytovatelů identit, pokud je potřeba business-to-business spolupráce.

### <a name="BKMK_CloudSvcConfig"></a>Konfigurace cloudových služeb
Cloudové služby jsou nezbytné, pokud chcete vystavit virtuálních počítačů přímo k Internetu nebo vystavit internetovou aplikaci Vyrovnávání zatížení sítě. To je možné, protože jednotlivých cloudových služeb nabízí jediné konfigurovat virtuální IP adresy.

### <a name="BKMK_FedReqVIPDIP"></a>Požadavky na federační server pro veřejné a privátní IP adres (dynamické IP adresy a virtuální IP adresy)
Každý virtuální počítač Azure obdrží dynamickou IP adresu. Dynamickou IP adresu je privátní adresa přístupné pouze v rámci Azure. Ve většině případů však bude nutné nakonfigurovat virtuální IP adresu pro vaše nasazení služby AD FS Windows serveru. Virtuální IP adresa je nutné vystavit koncových bodů služby AD FS Windows serveru k Internetu a se použije pro ověřování a průběžnou správu federovanými partnery a klienty. Virtuální IP adresa je vlastnost cloudové služby, který obsahuje jeden nebo více virtuálních počítačích Azure. Pokud deklaracemi identity aplikace nasazené v Azure a Windows Server AD FS internetového a sdílené složky Běžné porty, každá bude vyžadovat své vlastní virtuální IP adresy a proto bude nutné vytvořit jeden cloudové služby pro aplikace a druhý pro Windows Server AD FS.

Definice podmínek virtuální IP adresy a dynamickou IP adresu, najdete v části [termíny a definice](#BKMK_Glossary).

### <a name="BKMK_ADFSHighAvail"></a>Konfigurace vysoké dostupnosti systému Windows Server AD FS
I když je možné nasadit samostatné služby AD FS Windows serveru federační služby, doporučujeme nasadit farmu s alespoň dvěma uzly pro tokenů zabezpečení AD FS a proxy servery pro provozní prostředí.

V tématu [aspekty topologie služby AD FS 2.0 nasazení](https://technet.microsoft.com/library/gg982489) v [AD Průvodce návrhem služby FS 2.0](https://technet.microsoft.com/library/dd807036) rozhodnout, které možnosti konfigurace nasazení nejlepší podle konkrétních potřeb.

> [!NOTE]
> Chcete-li získat Vyrovnávání zatížení pro Windows Server AD FS koncových bodů v Azure, nakonfigurujte všechny členy farmy služby AD FS Windows serveru v rámci stejné cloudové služby a pomocí funkce Vyrovnávání zatížení Azure pro protokol HTTP (s výchozí 80) a porty protokolu HTTPS (výchozí hodnota 443). Další informace najdete v tématu [Azure pro vyrovnávání zatížení testu](https://msdn.microsoft.com/library/azure/jj151530).
> Windows Server Vyrovnávání zatížení sítě (NLB) není podporována v Azure.
> 
> 

