---
title: Řešení potíží s odchozími připojeními v Azure Load Balancer
description: Řešení běžných problémů s odchozím připojením prostřednictvím Azure Load Balancer.
services: load-balancer
author: erichrt
ms.service: load-balancer
ms.topic: troubleshooting
ms.date: 05/7/2020
ms.author: errobin
ms.openlocfilehash: 516576f4e005cc9fe2303945ecb1a13489908a5d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94696349"
---
# <a name="troubleshooting-outbound-connections-failures"></a><a name="obconnecttsg"></a> Řešení potíží s chybami odchozích připojení

Tento článek je určený k tomu, aby se při odchozích připojeních z Azure Load Balancer mohla objevit řešení pro běžné problémy. Většina problémů s odchozím připojením, ke kterým se zákazníci setkávají, je způsobená vyčerpáním portů SNAT a vypršením časového limitu připojení, který vede k vyřazen Tento článek popisuje kroky pro zmírnění každého z těchto problémů.

## <a name="managing-snat-pat-port-exhaustion"></a><a name="snatexhaust"></a> Správa vyčerpání portů SNAT (PAT)
[Dočasné porty](load-balancer-outbound-connections.md) používané pro [Pat](load-balancer-outbound-connections.md) jsou prostředek exhaustible, jak je popsáno na [samostatném virtuálním počítači bez veřejné IP adresy](load-balancer-outbound-connections.md) a [virtuálního počítače s vyrovnáváním zatížení bez veřejné IP adresy](load-balancer-outbound-connections.md). Pomocí [tohoto](./load-balancer-standard-diagnostics.md#how-do-i-check-my-snat-port-usage-and-allocation) průvodce můžete monitorovat využití dočasných portů a porovnat je s vaším aktuálním přidělením, abyste zjistili riziko nebo potvrzení vyčerpání SNAT.

Pokud víte, že zahajujete mnoho odchozích připojení TCP nebo UDP ke stejné cílové IP adrese a portu, a zjistíte, že se nezdařila odchozí připojení nebo pokud se vám doporučuje podpora, že vyčerpáte porty SNAT (předběžně přidělené [dočasné porty](load-balancer-outbound-connections.md#preallocatedports) používané [Pat](load-balancer-outbound-connections.md)), máte několik obecných možností zmírnění. Projděte si tyto možnosti a rozhodněte, co je k dispozici a co nejlépe vyhovuje vašemu scénáři. Je možné, že jeden nebo více může pomáhat při správě tohoto scénáře.

Pokud se vám nedaří pochopit chování odchozího připojení, můžete použít statistiku protokolu IP stack (netstat). Nebo může být užitečné sledovat chování připojení pomocí zachycení paketů. Tato zachytávání paketů můžete provést v hostovaném operačním systému vaší instance nebo použít [Network Watcher pro zachytávání paketů](../network-watcher/network-watcher-packet-capture-manage-portal.md). 

## <a name="manually-allocate-snat-ports-to-maximize-snat-ports-per-vm"></a><a name ="manualsnat"></a>Ruční přidělení portů SNAT pro maximalizaci portů SNAT na virtuální počítač
Jak je definováno na předem [přidělených portech](load-balancer-outbound-connections.md#preallocatedports), nástroj pro vyrovnávání zatížení automaticky přidělí porty na základě počtu virtuálních počítačů v back-endu. Ve výchozím nastavení se to provede uvážlivě, aby se zajistila škálovatelnost. Pokud znáte maximální počet virtuálních počítačů, které budete mít v back-endu, můžete porty SNAT přidělit ručně v každém odchozím pravidle. Pokud například víte, že budete mít maximálně 10 virtuálních počítačů, můžete k jednomu virtuálnímu počítači přidělit porty 6 400 SNAT a nikoli výchozí 1 024. 

## <a name="modify-the-application-to-reuse-connections"></a><a name="connectionreuse"></a>Úprava aplikace pro opětovné použití připojení 
Můžete snížit poptávku za dočasné porty, které se používají pro SNAT, a to tak, že znovu použijete připojení ve vaší aplikaci. Opakované použití připojení je zvláště důležité pro protokoly jako HTTP/1.1, kde je výchozí nastavení opětovného použití připojení. A další protokoly, které používají protokol HTTP jako jejich přenos (například REST), můžou mít výhodu. 

Opakované použití je vždy lepší než individuální, atomické připojení TCP pro každý požadavek. Opakované použití má za následek více výkonné, velmi efektivní transakce TCP.

## <a name="modify-the-application-to-use-connection-pooling"></a><a name="connection pooling"></a>Úprava aplikace pro použití sdružování připojení
Ve své aplikaci můžete použít schéma sdružování připojení, ve kterém jsou požadavky interně distribuovány přes pevně danou sadu připojení (podle toho, co je to možné). Toto schéma omezuje počet dočasných používaných portů a vytvoří předvídatelné prostředí. Toto schéma může také zvýšit propustnost žádostí tím, že umožňuje více souběžných operací, když je jedno připojení blokováno na reakci operace.  

Sdružování připojení může již existovat v rámci rozhraní, které používáte k vývoji aplikace nebo nastavení konfigurace aplikace. Sdružování připojení můžete kombinovat s opětovným použitím připojení. Vaše vícenásobné požadavky pak spotřebovávají pevný a předvídatelný počet portů na stejnou cílovou IP adresu a port. Požadavky také využívají efektivní využití transakcí TCP, které snižují latenci a využití prostředků. Transakce UDP můžou také těžit z toho důvodu, že správa počtu toků UDP se může vyhnout podmínkám výfuku a spravovat využití portů SNAT.

## <a name="modify-the-application-to-use-less-aggressive-retry-logic"></a><a name="retry logic"></a>Upravit aplikaci tak, aby používala méně agresivní logiku opakování
Pokud jsou [předběžně přidělené dočasné porty](load-balancer-outbound-connections.md#preallocatedports) používané pro [Pat](load-balancer-outbound-connections.md) vyčerpány nebo dojde k selhání aplikace, agresivní nebo nepřímý pokus o opakování při nedecay a omezení rychlostií služby Logic způsobuje vyčerpání nebo uchování. Můžete snížit poptávku za dočasné porty pomocí méně agresivní logiky opakování. 

Dočasné porty mají časový limit nečinnosti 4 minut (není upravitelný). Pokud jsou opakované pokusy příliš agresivní, vyčerpání není nijak jasné. Proto zvažte, jak--a jak často--vaše aplikace pokusy o opakování jsou důležitou součástí návrhu.

## <a name="assign-a-public-ip-to-each-vm"></a><a name="assignilpip"></a>Přiřazení veřejné IP adresy každému virtuálnímu počítači
Přiřazení veřejné IP adresy změní váš scénář na [veřejnou IP adresu virtuálního počítače](load-balancer-outbound-connections.md). Pro virtuální počítač jsou k dispozici všechny dočasné porty veřejné IP adresy, které se používají pro každý virtuální počítač. (Na rozdíl od scénářů, kdy jsou dočasné porty veřejné IP adresy sdílené se všemi virtuálními počítači přidruženými k příslušnému back-end fondu.) Existují kompromisy, které je potřeba vzít v úvahu, například dodatečné náklady na veřejné IP adresy a potenciální dopad na filtrování velkého počtu jednotlivých IP adres.

>[!NOTE] 
>Tato možnost není k dispozici pro role webového pracovního procesu.

## <a name="use-multiple-frontends"></a><a name="multifesnat"></a>Použít více front-endu
Při použití veřejných Standard Load Balancer přiřadíte [více front-ENDOVÉ IP adresy pro odchozí připojení](load-balancer-outbound-connections.md) a [vynásobte počet dostupných portů SNAT](load-balancer-outbound-connections.md#preallocatedports).  Vytvořte konfiguraci IP adresy front-endu, pravidlo a back-end fond pro aktivaci programování SNAT na veřejnou IP adresu front-endu.  Pravidlo nemusí fungovat a sonda stavu nemusí být úspěšná.  Pokud používáte více front-endu pro příchozí i (nikoli jenom pro odchozí), měli byste použít vlastní sondy stavu, abyste zajistili spolehlivost.

>[!NOTE]
>Ve většině případů je vyčerpání portů SNAT označením špatného návrhu.  Ujistěte se, že rozumíte tomu, proč vyčerpáte porty před použitím více front-endu pro přidání portů SNAT.  Můžete maskovat problém, který může vést k selhání později.

## <a name="scale-out"></a><a name="scaleout"></a>Horizontální navýšení kapacity
Předem [přidělené porty](load-balancer-outbound-connections.md#preallocatedports) se přiřazují na základě velikosti fondu back-endu a seskupené do vrstev, aby se minimalizovalo přerušení, když se některé porty musí přidělit, aby se vešly do další větší úrovně velikosti fondu back-endu.  Můžete mít možnost zvýšit využití portu SNAT pro daný front-end tím, že back-end fond nastavíte na maximální velikost pro danou vrstvu.  Mějte na paměti, že k efektivnímu horizontálnímu navýšení kapacity aplikace bez rizika vyčerpání SNAT je potřeba výchozí přidělení portu.

Například dva virtuální počítače ve fondu back-end budou mít 1024 až až na konfiguraci IP adres, což umožňuje celkem 2048 portů SNAT pro nasazení.  Pokud se nasazení zvýšilo na 50 virtuálních počítačů, a to i v případě, že počet předvázaných portů zůstává na virtuálním počítači konstantní, může nasazení použít celkem 51 200 portů SNAT (50 x 1024).  Pokud chcete horizontální navýšení kapacity nasazení, zkontrolujte počet předdefinovaných [portů](load-balancer-outbound-connections.md#preallocatedports) na jednu vrstvu, abyste se ujistili, že budete škálovat měřítko na maximum pro příslušnou vrstvu.  Pokud jste v předchozím příkladu zvolili horizontální navýšení kapacity na 51 a nikoli 50 instancí, budete postupovat až na další vrstvu a skončíte s méně porty SNAT na jeden virtuální počítač i v celkovém počtu.

Pokud nakonfigurujete horizontální navýšení úrovně velikosti fondu back-end, je možné, že některá z vašich odchozích připojení vyprší časový limit, pokud je potřeba znovu přidělit přidělené porty.  Pokud používáte jenom některé z vašich portů SNAT, horizontální navýšení kapacity v nejbližší větší velikosti fondu back-endu je bezvýznamnými.  Po každém přesunu na další úroveň fondu back-end se existující porty znovu přidělí.  Pokud nechcete, aby to bylo provedeno, je nutné nasazovat nasazení na velikost vrstvy.  Nebo se ujistěte, že se vaše aplikace může v případě potřeby detekovat a opakovat.  Možnost udržení protokolu TCP může pomoci při detekci, kdy porty SNAT již nefungují, protože se znovu přidělují.

## <a name="use-keepalives-to-reset-the-outbound-idle-timeout"></a><a name="idletimeout"></a>Obnovení odchozího nečinného časového limitu pomocí kontroly stavu
Odchozí připojení mají časový limit nečinnosti 4 minut. Tento časový limit je přizpůsobitelný prostřednictvím [odchozích pravidel](outbound-rules.md). K aktualizaci toku nečinnosti můžete také použít přenos (například kontroly stavu protokolu TCP) nebo obnovení aplikační vrstvy a v případě potřeby resetovat časový limit nečinnosti.  

Při použití kontroly udržení protokolu TCP je stačí povolit je na jedné straně připojení. Například stačí, abyste je povolili na straně serveru pouze k resetování časovače nečinnosti toku a není nutné, aby obě strany iniciovaly kontroly stavu protokolu TCP.  Pro aplikační vrstvu, včetně konfigurací klient-server databáze, existují podobné koncepty.  Na straně serveru můžete zjistit, jaké možnosti se v případě kontroly a nastavení pro konkrétní aplikaci existují.

## <a name="next-steps"></a>Další kroky
Pořád se snažíme vylepšit prostředí našich zákazníků. Pokud máte problémy s odchozím připojením, která nejsou uvedená nebo vyřešena v tomto článku, odešlete zpětnou vazbu prostřednictvím GitHubu v dolní části této stránky a my vám budeme vydávat zpětnou vazbu, jakmile to bude možné.