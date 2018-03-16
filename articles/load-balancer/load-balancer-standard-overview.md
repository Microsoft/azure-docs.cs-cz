---
title: "Přehled služby Azure standardní nástroje pro vyrovnávání zatížení | Microsoft Docs"
description: "Přehled funkcí Azure zatížení vyrovnávání standardní"
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: 
ms.assetid: 
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2018
ms.author: kumud
ms.openlocfilehash: 2d7fcb3ee066fa768615fbf643a0c2e1c1d28498
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="azure-load-balancer-standard-overview-preview"></a>Přehled služby Azure standardní nástroje pro vyrovnávání zatížení (preview)

Standardní SKU pro vyrovnávání zatížení Azure a veřejné IP standardní SKU společně umožňují vytvořit vysoce škálovatelného a spolehlivého architektury. Aplikace, které používají standardní nástroje pro vyrovnávání zatížení můžete využít nové funkce. S nízkou latencí a vysokou propustnost a měřítko jsou k dispozici pro miliony toků pro všechny aplikace, TCP a UDP.

>[!NOTE]
> Standardní SKU pro vyrovnávání zatížení je aktuálně ve verzi preview. Verzi Preview funkci nemusí mít stejnou úroveň dostupnost a spolehlivost jako verze funkce, které jsou obecné dostupnosti. Další informace najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Použít obecně dostupná [základní SKU služby Vyrovnávání zatížení](load-balancer-overview.md) pro vaše produkční služby. Použít [dostupnost zóny Preview](https://aka.ms/availabilityzones) s touto verzí Preview vyžaduje [samostatné registrace](https://aka.ms/availabilityzones), kromě registrace do služby pro vyrovnávání zatížení [standardní preview](#preview-sign-up).

## <a name="why-use-load-balancer-standard"></a>Proč používat standardní nástroje pro vyrovnávání zatížení?

Můžete použít standardní nástroje pro vyrovnávání zatížení pro celou řadu virtuální datových centrech. Nasazení v menším měřítku do zóny s více architekturami velké a komplexní můžete standardní nástroje pro vyrovnávání zatížení využívat následující funkce:

- [Podnikové škálování](#enterprisescale) lze dosáhnout pomocí standardní nástroje pro vyrovnávání zatížení. Tato funkce slouží k jakékoli instanci virtuálního počítače (VM) v rámci virtuální sítě, až 1 000 instancí virtuálních počítačů.

- [Nové diagnostické statistiky](#diagnosticinsights) jsou k dispozici vám pomohou pochopit, Správa a řešení potíží s Tento důležitou součástí virtuální datového centra. Monitorování Azure (preview) slouží k zobrazení, filtrovat a skupiny nové vícerozměrných metriky pro souvislá Datová cesta stavu měření. Monitorujte svá data z front-endu pro virtuální počítač, sondy koncový bod stavu pro pokusy o připojení protokolu TCP a odchozí připojení.

- [Skupin zabezpečení sítě](#nsg) jsou nyní požadované pro všechny instance virtuálního počítače, který je přidružený standardní SKU pro vyrovnávání zatížení nebo veřejné IP standardní SKU. Skupiny zabezpečení sítě (Nsg) zadejte rozšířené zabezpečení pro váš scénář.

- [Vysoké dostupnosti (HA) porty poskytovat vysokou spolehlivostí](#highreliability) a škálování pro virtuální síťové zařízení (NVAs) a v dalších scénářích aplikace. Všechny porty na Azure interní načíst vyrovnávání (ILB) do fondu instancí virtuálních počítačů front-end můžete vyrovnávat zatížení porty HA.

- [Odchozí připojení](#outboundconnections) teď použít nový model přidělení port zdroj síťové adresy překlad (SNAT), který poskytuje vyšší odolnosti a škálování.

- [Načíst vyrovnávání standardní dostupnosti zón](#availabilityzones) lze použít k vytvoření zónově redundantní a oblastmi architektury. Obě tyto architektury může zahrnovat Vyrovnávání zatížení mezi zóny. Můžete dosáhnout zálohování zóny bez závislosti na záznamy DNS. Zónově redundantní ve výchozím nastavení je jedinou IP adresu.  Jedna IP adresa se lze připojit žádné virtuální počítače ve virtuální síti v rámci oblasti, která je v rámci všech zón dostupnosti.


Můžete použít standardní nástroje pro vyrovnávání zatížení buď v interní nebo veřejné konfigurace pro podporu následující základní scénáře:

- Příchozí provoz na dobrý instance back-end můžete vyrovnávat zatížení.
- Port přesměrovat příchozí provoz do jediné instance back-end.
- Převede odchozí provoz z privátní IP adresy v rámci virtuální sítě na veřejnou IP adresu.

### <a name = "enterprisescale"></a>Podnikové škálování

 Použijte standardní nástroje pro vyrovnávání zatížení návrh vysoce výkonné virtuální datové centrum a podporují všechny aplikace TCP nebo UDP. Použijte samostatné instance virtuálních počítačů, nebo až 1 000 instancí virtuálního počítače sadách škálování ve fondu back-end. Nadále používat předávání nízkou latencí, vysoké propustnosti výkonu a možností škálování na miliony toky ve plně spravovaná služba Azure.
 
Standardní nástroje pro vyrovnávání zatížení můžete předat dál provoz na jakoukoli instanci virtuálního počítače ve virtuální síti v oblasti. Velikost fondu back-end může být až 1 000 instancí s libovolnou kombinaci těchto scénářích virtuálních počítačů:

- Samostatné virtuální počítače bez skupiny dostupnosti
- Samostatné virtuální počítače s skupiny dostupnosti
- Sadách škálování virtuálních počítačů, až 1 000 instancí
- Nastaví více škálování virtuálních počítačů
- Prolnutí virtuální počítače a sady škálování virtuálního počítače

Už je požadavek pro skupiny dostupnosti. Můžete použít pro další výhody, které obsahují skupiny dostupnosti.

### <a name = "diagnosticinsights"></a>Přehled diagnostiky

Standardní nástroje pro vyrovnávání zatížení poskytuje nové vícerozměrných diagnostické funkce pro veřejné a vnitřní konfigurace pro vyrovnávání zatížení. Tyto nové metriky jsou k dispozici prostřednictvím monitorování Azure (preview) a využívat všech souvisejících funkcí, včetně možnosti integrovat podřízené příjemci.

| Metrika | Popis |
| --- | --- |
| Dostupnost virtuálních IP adres | Standardní nástroje pro vyrovnávání zatížení vykonává nepřetržitě cesty data z v rámci oblasti na front-endu úplně se zásobníkem SDN, který podporuje virtuální počítač nástroje pro vyrovnávání zatížení. Také v pořádku instancí zůstanou, následuje měření stejnou cestu jako Vyrovnávání zatížení sítě provoz vaší aplikace. Také ověřuje cestu datům, která je používána zákazníky. Měření neviditelná do vaší aplikace a nebudou v konfliktu s dalšími operacemi.|
| Dostupnost vyhrazené IP adresy | Standardní nástroje pro vyrovnávání zatížení používá distribuované stavu zjišťování služby, která monitoruje stav váš koncový bod aplikace podle nastavení konfigurace. Tato metrika poskytuje agregaci nebo na koncový bod filtrované zobrazení každé jednotlivé instance koncového bodu nástroji pro vyrovnávání zatížení fondu.  Uvidíte, jak nástroj pro vyrovnávání zatížení zobrazení stavu aplikace podle vašeho Konfigurace testu stavu.
| SYN paketů | Standardní nástroje pro vyrovnávání zatížení nepodporuje ukončit připojení TCP nebo interakci s TCP nebo UDP paketů toky. Toky a jejich metodou handshake jsou vždy mezi zdrojovým a instance virtuálního počítače. Chcete-li lépe vyřešit vaše scénáře protokolu TCP, je nutné používat SYN čítače pakety zjistit, kolik připojení TCP se pokusy. Metrika hlásí počet paketů TCP SYN, které byly přijaty.|
| Překládat pomocí SNAT připojení | Standardní nástroje pro vyrovnávání zatížení hlásí počet odchozí toky, které jsou masqueraded front-end veřejné IP adresy. Překládat pomocí SNAT porty jsou vyčerpatelným prostředků. Tato metrika mohou poskytnout údaj o tom, jak výraznou je vaše aplikace spoléhat na překládat pomocí SNAT pro odchozí toky původu.  Čítače pro úspěšné i neúspěšné odchozí toky překládat pomocí SNAT hlášení a slouží k řešení potíží a pochopení stavu odchozích toků.|
| Čítače bajtů | Standardní nástroje pro vyrovnávání zatížení sestavy dat zpracovaných za front-endu.|
| Čítače paketů | Standardní nástroje pro vyrovnávání zatížení hlásí paketů zpracovaných za front-endu.|

### <a name = "highreliability"></a>Vysoká spolehlivost

Konfigurace pravidel a ujistěte se, vaše aplikace škálování za vysoce spolehlivé Vyrovnávání zatížení. Můžete nakonfigurovat pravidla pro jednotlivé porty a porty HA můžete vyrovnávat všechny přenosy bez ohledu na číslo portu TCP nebo UDP.  

K odemknutí celou řadu scénářů, včetně vysoké dostupnosti a možností škálování pro interní NVAs můžete použít funkci nové porty HA. Tato funkce je užitečná pro scénáře, kde je nepraktické nebo žádoucí, aby zadat jednotlivé porty. Porty HA poskytovat redundance a škálování tím, že jako velký počet instancí, protože potřebujete. Konfiguraci už není aktivní nebo pasivní scénářů s omezeným přístupem. Vaše konfigurace testu stavu předávají provoz jenom na v pořádku instance chránit vaši službu.

Hodnocení chyb zabezpečení dodavatelé nabízejí plně podporované dodavatele, odolné scénáře zákazníkům. Jediný bod selhání je odebrána a více aktivních instancích jsou podporovaná pro škálování. Je možné škálovat na dva nebo víc instancí, v závislosti na možnostech vašeho zařízení. Obraťte se na dodavatele hodnocení chyb zabezpečení sítě pro další pokyny pro tyto scénáře.

### <a name = "availabilityzones"></a>Dostupnost zóny

[!INCLUDE [availability-zones-preview-statement](../../includes/availability-zones-preview-statement.md)]

Posunutí odolnost vaší aplikace s použitím dostupnosti zón v podporovaných oblastí. Dostupnost zóny jsou aktuálně ve verzi preview do určitých oblastí a vyžadovat další výslovný souhlas.

### <a name="automatic-zone-redundancy"></a>Automatické zálohování zóny

Můžete vybrat, zda nástroj pro vyrovnávání zatížení by měl poskytovat a zónově redundantní nebo oblastmi front-end pro každý z vašich aplikací. Je snadné vytvořit zálohování zóny s standardní nástroje pro vyrovnávání zatížení. Jediné front-end IP adresy je automaticky zónově redundantní. Zónově redundantní front-endu je poskytovaný dostupnosti zón v oblasti současně. Zónově redundantní data cesta vytvořena pro příchozí a odchozí připojení. Zálohování zóny v Azure nevyžaduje více IP adres a záznamy DNS. 

Zálohování zóny je dostupná pro interní nebo veřejné front-end. Zónově redundantní lze veřejnou IP adresu a front-end privátní IP adresu pro interní nástroj pro vyrovnávání zatížení.

Pomocí následujícího skriptu pro vytvoření zónově redundantní veřejné IP adresy pro interní nástroj pro vyrovnávání zatížení. Pokud používáte stávající šablony Resource Manageru ve vaší konfiguraci, přidejte **sku** části tyto šablony.

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "public_ip_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
```

Pomocí následujícího skriptu k vytvoření zónově redundantní front-end IP adresu pro interní nástroj pro vyrovnávání zatížení. Pokud používáte stávající šablony Resource Manageru ve vaší konfiguraci, přidejte **sku** části tyto šablony.

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/loadBalancers",
            "name": "load_balancer_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
            "properties": {
                "frontendIPConfigurations": [
                    {
                        "name": "zone_redundant_frontend",
                        "properties": {
                            "subnet": {
                                "Id": "[variables('subnetRef')]"
                            },
                            "privateIPAddress": "10.0.0.6",
                            "privateIPAllocationMethod": "Static"
                        }
                    },
                ],
```

Pokud vaše veřejná IP adresa front-endu zónově redundantní, stane zónově redundantní odchozí připojení, které jsou automaticky vytvářeny z instancí virtuálních počítačů. Front-endu je chráněn selhání zóny. Vaše přidělení port překládat pomocí SNAT také odolává selhání zóny.

#### <a name="cross-zone-load-balancing"></a>Vyrovnávání zatížení mezi zóny

Je k dispozici v rámci oblasti pro fond back-end pro vyrovnávání zatížení mezi zóny a nabízí nejvyšší flexibilitu pro vaše instance virtuálních počítačů. Front-end přináší toky žádné virtuální počítače ve virtuální síti, bez ohledu na dostupnosti pásmo instance virtuálního počítače.

Můžete také zadat konkrétní zóně pro vaše front-end a back-end instance, chcete-li zarovnat cestu k datům a prostředkům pomocí konkrétní zónu.

Virtuální sítě a podsítě se nikdy omezené zónu. Pouze definuje fond back-end s požadovanou instancím virtuálních počítačů a nastavení konfigurace.

#### <a name="zonal-deployments"></a>Oblastmi nasazení

Jako možnost, můžete nástroj pro vyrovnávání zatížení front-end pro konkrétní zónu zarovnat definováním oblastmi front-endu. Oblastmi front-end obsluhovaného určené jeden dostupnosti zóna jenom. Když front-endu je spojeno s oblastmi instance virtuálních počítačů, můžete zarovnat prostředky do určité zóny.

Veřejná IP adresa, který je vytvořen v konkrétní zónu vždy existuje pouze v této zóně. Není možné změnit pásmo veřejnou IP adresu. Veřejná IP adresa, které lze připojit k prostředkům ve více zónách vytvořte místo toho raději zónově redundantní veřejnou IP adresu.

Pomocí následujícího skriptu k vytvoření oblastmi veřejnou IP adresu v dostupnosti zóny 1. Pokud používáte stávající šablony Resource Manageru ve vaší konfiguraci, přidejte **sku** části tyto šablony.

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "public_ip_standard",
            "location": "region",
            "zones": [ "1" ],
            "sku":
            {
                "name": "Standard"
            },
```

Pomocí následujícího skriptu k vytvoření interní pro vyrovnávání zatížení front-endu do zóny 1 dostupnosti.

Pokud používáte stávající šablony Resource Manageru ve vaší konfiguraci, přidejte **sku** části tyto šablony. Navíc definovat **zón** vlastnost ve front-endové konfiguraci protokolu IP pro podřízený prostředek.

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/loadBalancers",
            "name": "load_balancer_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
            "properties": {
                "frontendIPConfigurations": [
                    {
                        "name": "zonal_frontend_in_az1",
                        "zones": [ "1" ],
                        "properties": {
                            "subnet": {
                                "Id": "[variables('subnetRef')]"
                            },
                            "privateIPAddress": "10.0.0.6",
                            "privateIPAllocationMethod": "Static"
                        }
                    },
                ],
```

Přidejte mezi zóny Vyrovnávání zatížení pro back-end fondu umístěním vaší instance virtuálních počítačů, které jsou ve virtuální síti do fondu.

Standardní nástroje pro vyrovnávání zatížení prostředků je stále místní a zónově redundantní kde dostupnosti jsou podporovány. Můžete nasadit na veřejnou IP adresu nebo interní Vyrovnávání zatížení front-end, který nemá přiřazenou zóny v libovolné oblasti. Podpora pro dostupnost zóny nebude mít vliv na možnosti nasazení. Pokud oblast později získá dostupnost zóny, nasadili veřejné IP adresy nebo interní nástroj pro vyrovnávání zatížení front-end automaticky stane zónově redundantní Zónově redundantní data cesta neznamená 0 % ztráta paketů.

### <a name = "nsg"></a>Skupiny zabezpečení sítě

Načíst vyrovnávání Standard a veřejné IP Standard plně připojit k virtuální síti, která vyžaduje použití skupin zabezpečení sítě (Nsg). Skupiny Nsg umožňují pro tok přenosů seznam povolených adres. Skupiny Nsg můžete použít k získání plnou kontrolu nad provoz do vašeho nasazení. Už máte čekat na další tok provozu tak, aby dokončit.

Skupiny Nsg přidružte podsítě nebo síťová rozhraní (NIC) instance virtuálního počítače ve fondu back-end. Pomocí této konfigurace s standardní nástroje pro vyrovnávání zatížení a veřejné IP Standard, pokud se používá jako veřejná IP adresa úrovni instance. NSG musí explicitně povolených přenos, který chcete povolit, aby tento tok provozu.

Další informace o skupiny Nsg a způsobu jejich použití pro váš scénář naleznete v tématu [skupin zabezpečení sítě](../virtual-network/virtual-networks-nsg.md).

### <a name ="outboundconnections"></a>Odchozí připojení

Standardní nástroje pro vyrovnávání zatížení poskytuje odchozí připojení pro virtuální počítače, které jsou ve virtuální síti, když nástroj pro vyrovnávání zatížení používá port vydávají překládat pomocí SNAT. Algoritmus vydávají port překládat pomocí SNAT poskytuje vyšší odolnosti a škálování.

Pokud prostředek veřejné nástroje pro vyrovnávání zatížení je spojen s instancí virtuálních počítačů, je přepsaná každý zdroj odchozí připojení. Zdroj je přepsaná z prostor virtuální sítě privátní IP adresy front-endu veřejné IP adresy služby Vyrovnávání zatížení.

Při použití odchozí připojení s zónově redundantní front-end, připojení jsou také zónově redundantní a překládat pomocí SNAT port přidělení zůstanou platné i po selhání zóny.

Nového algoritmu v standardní nástroje pro vyrovnávání zatížení preallocates překládat pomocí SNAT porty na síťový adaptér každého virtuálního počítače. Pokud síťový adaptér je přidán do fondu, překládat pomocí SNAT porty jsou souhrnů ještě neumístěných na základě velikosti fondu. V následující tabulce jsou uvedeny preallocations port pro šest úrovně velikostí fond back-end:

| Velikost fondu (instance virtuálních počítačů) | Předběžně přidělené počet portů překládat pomocí SNAT |
| --- | --- |
| 1 - 50 | 1024 |
| 51 - 100 | 512 |
| 101 - 200 | 256 |
| 201 - 400 | 128 |
| 401 - 800 | 64 |
| 801 - 1,000 | 32 |

Porty překládat pomocí SNAT není přímo převede na počet odchozí připojení. Port překládat pomocí SNAT lze znovu použít pro více cílů jedinečný. Podrobnosti najdete v článku [odchozí připojení](load-balancer-outbound-connections.md) článku.

Pokud velikost fondu back-end zvyšuje a přejde do vyšší úrovně, se uvolní polovinu vaší přidělené porty. Připojení, které jsou přiřazeny k vypršení časového limitu regenerovaný portu a musí je znovu vytvořit. Pokusy o připojení úspěšné okamžitě. Pokud velikost fondu back-end snižuje a přejde do nižší úrovně, zvyšuje počet dostupných portů překládat pomocí SNAT. V takovém případě se neovlivní existující připojení.

Standardní nástroje pro vyrovnávání zatížení má možnost Další konfiguraci, která lze použít na základě pravidla. Můžete řídit, které front-endu se používá pro port vydávají překládat pomocí SNAT, když jsou k dispozici více front-end.

Když pouze zatížení vyrovnávání standardní obsluhuje instance virtuálních počítačů, nejsou k dispozici odchozí připojení překládat pomocí SNAT. Tuto možnost můžete obnovit explicitně také přiřazením instance virtuálních počítačů pro vyrovnávání zatížení veřejné. Veřejné IP adresy můžete také přímo přiřadit jako veřejné IP adresy instance úrovni k jednotlivým instancím virtuálních počítačů. Tato možnost konfigurace mohou být požadovány pro některé operační systém a scénáře aplikací. 

### <a name="port-forwarding"></a>Přesměrování portu

Základní a nástroje pro vyrovnávání zatížení zadejte možnost konfigurace příchozích pravidel NAT pro mapování front-end port na jednotlivé instance back-end. Nakonfigurováním tato pravidla můžete vystavit koncové body SSH a Remote Desktop Protocol koncových bodů nebo provádět jiné scénáře aplikací.

Standardní nástroje pro vyrovnávání zatížení nadále poskytuje možnost předávání portů prostřednictvím příchozích pravidel NAT. Při použití s zónově redundantní front-end, příchozích pravidel NAT stane zónově redundantní a zůstanou platné i po selhání zóny.

### <a name="multiple-front-ends"></a>Více front-end

Konfigurace více front-end pro návrh flexibilitu při aplikace vyžadují více jednotlivých IP adres mají být exponovány, jako je například TLS weby nebo koncových bodů skupina dostupnosti AlwaysOn serveru SQL. 

Standardní nástroje pro vyrovnávání zatížení nadále poskytuje více front-end kde je nutné vystavit koncový bod konkrétní aplikaci na jedinečnou IP adresu.

Další informace o konfiguraci více front-end IP adresy najdete v tématu [konfiguraci více IP](load-balancer-multivip-overview.md).

## <a name = "sku"></a>O SKU

SKU jsou dostupné jenom v modelu nasazení Azure Resource Manager. Tato verze preview zavádí dvě SKU pro nástroj pro vyrovnávání zatížení a veřejná IP adresa zdroje: Basic a Standard. Dalo, výkonové charakteristiky, omezení a některé vnitřní chování skladové jednotky se liší. Virtuální počítače můžete použít s buď SKU. SKU pro prostředky pro vyrovnávání zatížení a veřejnou IP adresu, zůstanou volitelných atributů. Když v definici scénář byly vynechány SKU, konfigurace ve výchozím nastavení používá základní SKU.

>[!IMPORTANT]
>Skladová položka prostředku není měnitelný. Skladová položka existující prostředek nesmí změnit.  

### <a name="load-balancer"></a>Load Balancer

[Existující prostředek pro vyrovnávání zatížení](load-balancer-overview.md) stane základní SKU a zůstane obecně dostupné a beze změny.

Standardní SKU pro vyrovnávání zatížení je nový a je momentálně ve verzi preview. 1 srpnu 2017 verze rozhraní API pro Microsoft.Network/loadBalancers přidá **sku** vlastnosti tak, aby definice prostředků:

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/loadBalancers",
            "name": "load_balancer_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
```
Standardní nástroje pro vyrovnávání zatížení je automaticky zóny odolné v oblastech, které nabízejí dostupnost zóny. Pokud nástroj pro vyrovnávání zatížení bylo deklarováno oblastmi, pak není automaticky zóny odolný.

### <a name="public-ip"></a>Veřejná IP adresa

[Existující prostředek veřejné IP adresy](../virtual-network/virtual-network-ip-addresses-overview-arm.md) stane základní SKU a zůstane všeobecně dostupná se všemi jeho schopnosti, výkonové charakteristiky a omezení.

Veřejné IP standardní SKU je nový a je momentálně ve verzi preview. 1 srpnu 2017 verze rozhraní API pro Microsoft.Network/publicIPAddresses přidá **sku** vlastnosti tak, aby definice prostředků:

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "public_ip_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
```

Na rozdíl od veřejných IP základní, který nabízí několik metod přidělení, Public IP Standard vždy používá statického přidělování.

Veřejné IP Standard se automaticky zóny odolné v oblastech, které nabízejí dostupnost zóny. Pokud veřejné IP adresy bylo deklarováno oblastmi, pak není automaticky zóny odolný. Oblastmi veřejnou IP adresu z jednu zónu nelze změnit na jiný.

## <a name="migration-between-skus"></a>Migrace mezi SKU

SKU nejsou měnitelný. Postupujte podle kroků v této části přesunuty z jednoho prostředku SKU do jiného.

### <a name="migrate-from-basic-to-standard-sku"></a>Migrovat ze základní standardní SKU

1. Vytvoření nového prostředku standardní (nástroj pro vyrovnávání zatížení a veřejné IP adresy, podle potřeby). Pravidla znovu vytvořit a sběru dat definice.

2. Vytvořit nový nebo aktualizovat existující skupina NSG na síťový adaptér nebo podsíť, do seznamu povolených IP adres skupinu s vyrovnáváním zatížení provozu, test, stejně jako všechny ostatní přenosy, který chcete povolit.

3. Základní SKU prostředků (nástroj pro vyrovnávání zatížení a veřejné IP adresy, podle vhodnosti) odeberte ze všech instancí virtuálních počítačů. Nezapomeňte také odebrat všechny instance virtuálních počítačů sady dostupnosti.

4. Připojte všechny instance virtuálních počítačů k nové prostředky standardní SKU.

### <a name="migrate-from-standard-to-basic-sku"></a>Migrace z Standard na základní SKU

1. Vytvoření nového prostředku základní (nástroj pro vyrovnávání zatížení a veřejné IP adresy, podle potřeby). Pravidla znovu vytvořit a sběru dat definice. 

2. Standardní SKU prostředků (nástroj pro vyrovnávání zatížení a veřejné IP adresy, podle vhodnosti) odeberte ze všech instancí virtuálních počítačů. Nezapomeňte také odebrat všechny instance virtuálních počítačů sady dostupnosti.

3. Připojte všechny instance virtuálních počítačů k nové prostředky základní SKU.

>[!IMPORTANT]
>
>Existují omezení týkající se použití Basic a standardní SKU.
>
>HA porty a diagnostiky standardní SKU jsou k dispozici pouze v standardní SKU. Nelze migrovat ze standardní SKU základní SKU a uchovávají taky tyto funkce.
>
>Odpovídající identifikátory SKU musí použít pro nástroj pro vyrovnávání zatížení a veřejnou IP adresu prostředky. Nemůžete mít směs základní SKU a standardní SKU prostředků. Nelze připojit virtuální počítač, virtuální počítače v skupiny dostupnosti, nebo nastavte škálování virtuálních počítačů na obě položky současně.
>

## <a name="region-availability"></a>Dostupnost v oblastech

Standardní nástroje pro vyrovnávání zatížení je aktuálně k dispozici ve všech oblastech veřejného cloudu, s výjimkou západní USA.

>[!IMPORTANT]
> Krátké době přístup do oblasti mimo počáteční spuštění oblastí (východní USA 2, střed USA, Severní Evropa, Západ střední USA, západní Evropa, jihovýchodní Asie) vyžaduje registraci předplatného další funkce (AllowLBPreviewWave2 a AllowLBPreviewWave3).  [Postupujte podle těchto kroků](#additionalpreviewregions). Proveďte prosím všechny z nich i v případě, že jste dříve zaregistrovali k AllowLBPreview již.
> Tento požadavek se odeberou v následujících týdnech.

## <a name="sku-service-limits-and-abilities"></a>Omezení služby SKU a možnosti

Azure [omezení služby pro sítě](https://docs.microsoft.com/azure/azure-subscription-service-limits#networking-limits) použít na oblast na předplatné. 

Následující tabulka porovnává omezení a dalo základní nástroje pro vyrovnávání zatížení a standardní SKU:

| Load Balancer | Basic | Standard |
| --- | --- | --- |
| Velikost fondu back-end | až 100 | až 1 000 |
| Fond back-end hranic | Skupina dostupnosti | virtuální síť, oblast |
| Fond back-end návrhu | Virtuální počítače ve skupině dostupnosti, nastavte ve skupině dostupnosti škálování virtuálních počítačů | Všechny instance virtuálního počítače ve virtuální síti |
| HA porty | Nepodporuje se | Dostupné |
| Diagnostika | Omezené, veřejné pouze | Dostupné |
| Dostupnost virtuálních IP adres  | Nepodporuje se | Dostupné |
| Rychlé IP Mobility | Nepodporuje se | Dostupné |
|Scénáře dostupnosti zóny | Pouze oblastmi | Oblastmi, Zónově redundantní, Vyrovnávání zatížení mezi zóny |
| Odchozí překládat pomocí SNAT algoritmus | On-demand | Souhrnů ještě neumístěných |
| Odchozí překládat pomocí SNAT front-end výběr | Nejde konfigurovat více kandidáty | Volitelné konfigurace pro snížení kandidáty |
| Skupina zabezpečení sítě | Volitelné na síťový adaptér a podsítě | Požaduje se |

Následující tabulka porovnává omezení a dalo veřejné IP Basic a standardní SKU:

| Veřejná IP adresa | Basic | Standard |
| --- | --- | --- |
| Scénáře dostupnosti zóny | Pouze oblastmi | Zónově redundantní (výchozí), oblastmi (volitelné) | 
| Rychlé IP Mobility | Nepodporuje se | Dostupné |
| Dostupnost virtuálních IP adres | Nepodporuje se | Dostupné |
| Čítače | Nepodporuje se | Dostupné |
| Skupina zabezpečení sítě | Volitelné na síťový adaptér | Požaduje se |


## <a name="preview-sign-up"></a>Náhled registrace

K účasti ve verzi preview pro standardní SKU pro vyrovnávání zatížení a doprovodné veřejné IP standardní SKU, zaregistrujte své předplatné.  Probíhá registrace vaše předplatné vám umožňuje přístup z prostředí PowerShell nebo Azure CLI 2.0. Pokud chcete zaregistrovat, proveďte následující kroky:

>[!NOTE]
>Registrace funkci standardní nástroje pro vyrovnávání zatížení může trvat až jednu hodinu, globálně začnou platit. Pokud chcete použít zatížení vyrovnávání standardní s [dostupnost zóny](https://aka.ms/availabilityzones), [samostatné registrace](https://aka.ms/availabilityzones) je vyžadován AZ ve verzi Preview.

<a name="additionalpreviewregions"></a>
>[!IMPORTANT]
> Pro krátké době přístup k oblasti mimo počáteční spuštění oblastí (východní USA 2, střed USA, Severní Evropa, Západ střední USA, západní Evropa, jihovýchodní Asie) vyžadují registraci předplatného další funkce (AllowLBPreviewWave2 a AllowLBPreviewWave3).  Následující postup bylo upraveno, aby další předplatné funkce povolit. Proveďte prosím všechny z nich i v případě, že jste dříve zaregistrovali k AllowLBPreview již. Tento požadavek se odeberou v následujících týdnech.


### <a name="sign-up-by-using-azure-cli-20"></a>Přihlásit pomocí Azure CLI 2.0

1. Zaregistrujte funkci zprostředkovatele:

    ```cli
    az feature register --name AllowLBPreview --namespace Microsoft.Network
    az feature register --name AllowLBPreviewWave2 --namespace Microsoft.Network
    az feature register --name AllowLBPreviewWave3 --namespace Microsoft.Network
    ```
    
2. Operace může trvat až 10 minut. Můžete zkontrolovat stav operace pomocí následujícího příkazu:

    ```cli
    az feature list --query "[?name=='Microsoft.Network/AllowLBPreview']" --output json
    az feature list --query "[?name=='Microsoft.Network/AllowLBPreviewWave2']" --output json
    az feature list --query "[?name=='Microsoft.Network/AllowLBPreviewWave3']" --output json
    ```
    
    Pokračujte k dalšímu kroku, když se stav registrace funkce vrátí "Registrovaná" pro každou z výše uvedených funkcí předplatného. Příklad:
   
    ```json
    {
       "id": "/subscriptions/foo/providers/Microsoft.Features/providers/Microsoft.Network/features/AllowLBPreview",
       "name": "Microsoft.Network/AllowLBPreview",
       "properties": {
          "state": "Registered"
       },
       "type": "Microsoft.Features/providers/features"
    }
    ```
    
4. Proveďte registraci ve verzi preview opakováním registrace předplatného s poskytovatelem prostředků:

    ```cli
    az provider register --namespace Microsoft.Network
    ```
    

### <a name="sign-up-by-using-powershell"></a>Zaregistrujte si pomocí prostředí PowerShell

1. Zaregistrujte funkci zprostředkovatele:

    ```powershell
    Register-AzureRmProviderFeature -FeatureName AllowLBPreview -ProviderNamespace Microsoft.Network
    Register-AzureRmProviderFeature -FeatureName AllowLBPreviewWave2 -ProviderNamespace Microsoft.Network
    Register-AzureRmProviderFeature -FeatureName AllowLBPreviewWave3 -ProviderNamespace Microsoft.Network
    ```
    
2. Operace může trvat až 10 minut. Můžete zkontrolovat stav operace pomocí následujícího příkazu:

    ```powershell
    Get-AzureRmProviderFeature -FeatureName AllowLBPreview -ProviderNamespace Microsoft.Network
    Get-AzureRmProviderFeature -FeatureName AllowLBPreviewWave2 -ProviderNamespace Microsoft.Network
    Get-AzureRmProviderFeature -FeatureName AllowLBPreviewWave3 -ProviderNamespace Microsoft.Network
    ```

  Pokračujte k dalšímu kroku, když se stav registrace funkce vrátí "Registrovaná" pro každou z výše uvedených funkcí předplatného. Příklad:

    ```
    FeatureName      ProviderName        RegistrationState
    -----------      ------------        -----------------
    AllowLBPreview   Microsoft.Network   Registered
    ```
    
3. Proveďte registraci ve verzi preview opakováním registrace předplatného s poskytovatelem prostředků:

    ```powershell
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
    ```
 
## <a name="pricing"></a>Ceny

Fakturace standardní SKU pro vyrovnávání zatížení je založena na nakonfigurovaných pravidel a zpracovaná data. Během období preview se vám neúčtují žádné poplatky. Další informace najdete v článku [nástroj pro vyrovnávání zatížení](https://aka.ms/lbpreviewpricing) a [veřejnou IP adresu](https://aka.ms/lbpreviewpippricing) ceny stránky.

Zákazníci se nadále získejte základní SKU služby Vyrovnávání zatížení zdarma.

## <a name="limitations"></a>Omezení

Tato omezení platí v době preview a se mohou změnit:

- Instance back-end pro vyrovnávání zatížení nemohou být umístěny v peered virtuálních sítí v tuto chvíli. Všechny instance back-end musí být ve stejné oblasti.
- SKU nejsou měnitelný. Skladová položka existující prostředek nesmí změnit.
- Obě položky lze použít s samostatný virtuální počítač, instance virtuálního počítače ve skupině dostupnosti, nebo sadu škálování virtuálního počítače. Kombinace virtuálního počítače se nedá použít obě skladových položek najednou. Konfigurace, který obsahuje směs SKU není povoleno.
- Pomocí vnitřní zatížení vyrovnávání Standard pro instanci virtuálního počítače (nebo libovolná součást skupiny dostupnosti) zakáže [výchozí odchozí připojení překládat pomocí SNAT](load-balancer-outbound-connections.md). Tuto možnost na samostatný virtuální počítač, instancím virtuálních počítačů ve skupině dostupnosti nebo škálovací sadu virtuálních počítačů, můžete obnovit. Můžete také obnovit schopnost provádět odchozí připojení. K obnovení těchto schopností, současně vyrovnávání standardní veřejné zatížení, nebo přidělte veřejné IP standardní jako úrovni instance veřejné IP adresy, na stejnou instanci virtuálního počítače. Po dokončení přiřazení portu vydávají překládat pomocí SNAT veřejné IP adresy je zadat znovu.
- Instance virtuálních počítačů může být nutné být seskupeny do sad dostupnosti můžete dosáhnout úplné back-end fondu škálování. Až 150 dostupnosti sady a samostatné virtuální počítače můžete umístit do jednoho fondu back-end.
- Protokol IPv6 není podporován.
- V kontextu dostupnosti zón front-endu není měnitelný z oblastmi na zónově redundantní a naopak. Po front-endu je vytvořen jako zónově redundantní, zůstane zónově redundantní. Po front-endu je vytvořen jako oblastmi, zůstane oblastmi.
- V kontextu dostupnosti zóny nelze z jedné zóny na jiný přesunout oblastmi veřejnou IP adresu.
- [Azure monitorování výstrah](../monitoring-and-diagnostics/monitoring-overview-alerts.md) nejsou podporovány v tuto chvíli.
- Portál zatím nepodporuje rozšířené preview oblasti.  Použijte nástroje klienta, například šablony, Azure CLI 2.0 nebo prostředí PowerShell jako alternativní řešení.
- [Přesunutí operací předplatné](../azure-resource-manager/resource-group-move-resources.md) nejsou podporovány pro standardní SKU LB a PIP prostředky.
- Není k dispozici v západní USA.


## <a name="next-steps"></a>Další postup

- Další informace o [základní nástroje pro vyrovnávání zatížení](load-balancer-overview.md).
- Další informace o [dostupnost zóny](../availability-zones/az-overview.md).
- Další informace o [skupin zabezpečení sítě](../virtual-network/virtual-networks-nsg.md).
- Přečtěte si o některých dalších klíče [sítě možnosti](../networking/networking-overview.md) v Azure.
- Další informace o [metriky, které jsou zveřejněné](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftnetworkloadbalancers) v [Azure monitorování](../monitoring-and-diagnostics/monitoring-overview.md).
