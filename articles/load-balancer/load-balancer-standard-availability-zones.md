---
title: Azure Load balancer úrovně Standard a zóny dostupnosti
titlesuffix: Azure Load Balancer
description: Load Balancer úrovně Standard a zóny dostupnosti
services: load-balancer
documentationcenter: na
author: KumudD
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/08/2018
ms.author: kumud
ms.openlocfilehash: 2c4503b6ff065e98c49fe3f4e06b63cbeb7d1770
ms.sourcegitcommit: e68df5b9c04b11c8f24d616f4e687fe4e773253c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/20/2018
ms.locfileid: "53652740"
---
# <a name="standard-load-balancer-and-availability-zones"></a>Load Balancer úrovně Standard a zóny dostupnosti

Standardní skladová jednotka Azure Load Balancer podporuje [zóny dostupnosti](../availability-zones/az-overview.md) scénáře. Několik nových konceptů jsou k dispozici Load balanceru úrovně Standard, které umožňují vám umožní optimalizovat tak, že zarovnání prostředky se zónami a jejich distribuci napříč zónami dostupnosti ve vašem scénáři začátku do konce.  Kontrola [zóny dostupnosti](../availability-zones/az-overview.md) pokyny, co jsou zóny dostupnosti, ve kterých oblastech se aktuálně podporují zóny dostupnosti a druhý související koncepty a produkty. Zóny dostupnosti v kombinaci s Load balanceru úrovně Standard jsou sady funkce obsáhlém a flexibilní, můžete vytvořit mnoho různých scénářů.  Přečtěte si tento dokument, abyste umět nakonfigurovat [koncepty](#concepts) a základní scénář [pokyny k návrhu](#design).

>[!NOTE]
>Kontrola [zóny dostupnosti](https://aka.ms/availabilityzones) další související témata. 

## <a name="concepts"></a> Koncepce zón dostupnosti u nástroje pro vyrovnávání zatížení

Není žádný přímý vztah mezi prostředky nástroje pro vyrovnávání zatížení a infrastruktury skutečný; Vytvoření nástroje pro vyrovnávání zatížení nevytváří instanci. Prostředků nástroje pro vyrovnávání zatížení jsou objekty, ve kterém můžete vyjádřit, jak Azure by měl program jeho předem připravených víceklientské infrastruktury, scénář, který chcete vytvořit.  To je důležité v rámci zóny dostupnosti, protože jeden prostředek nástroje pro vyrovnávání zatížení můžete řídit programování infrastruktury v několika zónami dostupnosti, zatímco zónově redundantní služby se zobrazí jako jeden prostředek z hlediska zákazníků.

Funkce nástroje pro vyrovnávání zatížení prostředků jsou vyjádřeny jako front-end, pravidla, sondu stavu a definice fondu back-endu.

V rámci zóny dostupnosti jsou popsané chování a vlastností prostředku nástroje pro vyrovnávání zatížení jako zónové a zónově redundantní.  Zónově redundantní a zónové popisují zonality vlastnost.  V rámci nástroje pro vyrovnávání zatížení, zónově redundantní vždy znamená, že *všechny zóny* a zónové prostředky služby k zajištění *jedné zóně*.

Veřejné a vnitřní nástroj pro vyrovnávání zatížení podporují zónové a zónově redundantní scénáře a podle potřeby, jak může směrovat provoz mezi zónami (*Vyrovnávání zatížení mezi zónami*).

Prostředek nástroje pro vyrovnávání zatížení, samotného je místní a nikdy oblastmi.  A virtuální sítě a podsítě jsou vždy místní a nikdy oblastmi.

### <a name="frontend"></a>Front-end

Front-endu nástroje pro vyrovnávání zatížení je konfigurace IP front-endu na prostředek veřejné IP adresy nebo privátní IP adresu v rámci podsítě prostředku virtuální sítě.  Vymezuje koncový bod s vyrovnáváním zatížení, kde je vystaven vaší služby.

Prostředek nástroje pro vyrovnávání zatížení může obsahovat současně zónové a zónově redundantních front-endů. 

Pokud prostředek veřejné IP adresy má byla zaručeno, že do zóny, zonality (nebo neexistenci) není měnitelný.  Pokud chcete změnit nebo vynechejte zonality veřejnou front-endovou IP, budete muset znovu vytvořit veřejnou IP adresu v příslušné oblasti.  

Odebráním a opětovné vytvoření front-endu, změna nebo vynechání zonality můžete změnit zonality front-end interního nástroje Load Balancer.

Pokud používáte několik front-endů, zkontrolujte [několik front-endů pro nástroj pro vyrovnávání zatížení](load-balancer-multivip-overview.md) důležité informace.

#### <a name="zone-redundant-by-default"></a>Zónově redundantní ve výchozím nastavení

V oblasti se zónami dostupnosti se zónově redundantní ve výchozím nastavení front-end Load balanceru úrovně Standard.  IP adresa front-endu jeden přežijí selhání zóny a může být použité k dosažení všech členů fondu back-endu bez ohledu na to, zóna. Cesta k datům hitless neznamená to však reestablishment ani opakování proběhne úspěšně. Schémata redundance DNS se nevyžadují. Front-endu jednu IP adresu obsluhují současně více nezávislých infrastrukturu nasazení v několika zónami dostupnosti.  Zónově redundantní znamená, že všechny příchozí nebo odchozí toky obsluhuje více zón dostupnosti v oblasti současně používat jednu IP adresu.

Jeden nebo více zónách dostupnosti, i když selže na cestu k datům odolává tak dlouho, dokud jedna zóna v oblasti zůstane v dobrém stavu. Zónově redundantní konfigurace je výchozí a nevyžaduje žádné další akce.  Když v oblasti získá možnost podporují zóny dostupnosti, stane stávající front-end zónově redundantní automaticky.

Pomocí následujícího skriptu vytvořte zónově redundantní veřejnou IP adresu pro vaše interní Load balanceru úrovně Standard. Pokud používáte existující šablony Resource Manageru ve vaší konfiguraci, přidejte **sku** části tyto šablony.

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

Pomocí následujícího skriptu vytvořte zónově redundantních front-endovou IP adresu pro vaše interní Load balanceru úrovně Standard. Pokud používáte existující šablony Resource Manageru ve vaší konfiguraci, přidejte **sku** části tyto šablony.

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

#### <a name="optional-zone-guarantee"></a>Záruka volitelné zóny

Můžete zvolit, aby front-endu, zaručeno, že k jedné oblasti, což se označuje jako *zónové front-endu*.  To znamená, že se že všechny příchozí nebo odchozí tok je obsluhuje jednu zónu v oblasti.  Vaše front-endu sdílí pracuje s stavu zóny.  Cesta k datům není ovlivněn selhání v oblastech než ve kterém byla zaručená. Oblastmi front-endů můžete použít ke zveřejnění IP adresa na zónu dostupnosti.  Navíc můžete využívat oblastmi front-endů přímo nebo, když se skládá z veřejné IP adresy front-endu integrovat se službou Vyrovnávání zatížení produktu jako DNS [Traffic Manageru](../traffic-manager/traffic-manager-overview.md) a používat jeden název DNS, který se přeloží klienta více oblastmi IP adres.  To můžete použít ke zveřejnění na zóny s vyrovnáváním zatížení koncových bodech, které jednotlivě sledovat každou zónu.  Pokud chcete kombinovat tyto koncepty (zónově redundantní a zónové pro stejný back-end), přečtěte si [několik front-endů pro Azure Load Balancer](load-balancer-multivip-overview.md).

Pro veřejný nástroj pro vyrovnávání zatížení front-end, přidáte *zóny* parametr na veřejnou IP adresu odkazuje front-endová konfigurace protokolu IP.  

Interní nástroj pro vyrovnávání zatížení front-endu, přidejte *zóny* parametrů pro vnitřní konfigurace protokolu IP front-endu nástroje pro vyrovnávání zatížení. Oblastmi front-endu způsobí, že nástroj pro vyrovnávání zatížení pro zajištění IP adresy v podsíti pro konkrétní zónu.

Pomocí následujícího skriptu vytvořte oblastmi standardní veřejnou IP adresu v zóně 1 dostupnosti. Pokud používáte existující šablony Resource Manageru ve vaší konfiguraci, přidejte **sku** části tyto šablony.

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

Pomocí následujícího skriptu vytvořte interní nástroj pro vyrovnávání zatížení front-endem v zóně 1 dostupnosti.

Pokud používáte existující šablony Resource Manageru ve vaší konfiguraci, přidejte **sku** části tyto šablony. Navíc definovat **zóny** vlastnosti v konfiguraci protokolu IP front-endu pro podřízený prostředek.

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

### <a name="cross-zone-load-balancing"></a>Vyrovnávání zatížení mezi zónami

Vyrovnávání zatížení mezi zónami je schopnost služby Load Balancer oslovit koncový bod back-end v každé zóně a to nezávisle na front-endu a jeho zonality.

Pokud chcete zarovnat a zaručit nasazení v rámci jedné oblasti, zarovnání oblastmi front-endu a zónové back-endovým prostředkům do stejné zóny. Nevyžaduje se žádná další akce.

### <a name="backend"></a>Back-end

Nástroj pro vyrovnávání zatížení funguje s virtuálními počítači.  Všech virtuálních počítačů v jedné virtuální sítě můžou být součástí fondu back-endu bez ohledu na to, zda byla zaručeno do zóny nebo zóně, ve které byla zaručeno, že.

Pokud chcete zarovnat a zaručit front-endových a back-endu pomocí jedné oblasti, pouze umístíte virtuální počítače ve stejné zóně do příslušných back-endový fond.

Pokud chcete adresy virtuálních počítačů napříč několika zónami, jednoduše umístěte virtuální počítače z několika zónami do stejného back-endový fond.  Pokud použijete virtuálního počítače škálovací sady, můžete umístit jeden nebo více škálovací sady virtuálních počítačů do stejného back-endový fond.  A každý z těchto škálovací sady virtuálních počítačů může být v jedné nebo několika zónami.

### <a name="outbound-connections"></a>Odchozí připojení

[Odchozí připojení](load-balancer-outbound-connections.md) obsluhují všechny zóny a jsou automaticky zónově redundantní v oblasti se zónami dostupnosti, pokud virtuální počítač přidružený veřejný Load Balancer a zónově redundantních front-endu.  Odchozí připojení SNAT port přidělení selhání zóny.  

Naopak pokud virtuální počítač je spojen s veřejný Load Balancer a zónové front-endu, odchozí připojení je zaručeno poskytovat podle jedné oblasti.  Odchozí připojení sdílet pracuje s stav příslušných zóny.

Předběžné přidělení SNAT portu a algoritmus je stejná s nebo bez zón.

### <a name="health-probes"></a>Sondy stavu

Vaše existující definice sondy stavu zůstane tak, jak jsou, bez zón dostupnosti.  Ale jsme rozšířili modelu stavu na úrovni infrastruktury. 

Při použití zónově redundantních front-endů, nástroje pro vyrovnávání zatížení se rozšíří jeho vnitřního stavu modelu nezávisle na sobě testu dostupnosti virtuálního počítače z každá zóna dostupnosti a vypnout cest napříč zónami, které mohlo dojít k selhání bez zásahu zákazníka.  Pokud zadaná cesta není k dispozici z infrastruktury nástroje pro vyrovnávání zatížení jednu zónu na virtuální počítač v jiné zóně, nástroje pro vyrovnávání zatížení můžete zjišťovat a vyhnout se této chyby. Další zóny, kteří můžou kontaktovat tento virtuální počítač můžete nadále sloužit virtuálního počítače z jejich odpovídajících front-endů.  V důsledku toho je možné, že během události selhání každé zóně může mít distribuce mírně odlišný tok při ochraně celkový stav služby začátku do konce.

## <a name="design"></a> Aspekty návrhu

Nástroj pro vyrovnávání zatížení je záměrně flexibilní v rámci zóny dostupnosti. Můžete také zarovnat zóny nebo můžete být zónově redundantní.  Zajištění vyšší dostupnosti můžete vrátit za cenu zvýšení složitosti a je třeba navrhnout dostupnosti pro zajištění optimálního výkonu.  Pojďme se podívat na některé důležité aspekty návrhu.

### <a name="automatic-zone-redundancy"></a>Automatické zálohování zóny

Nástroj pro vyrovnávání zatížení usnadňuje mít jednu IP adresu jako zónově redundantních front-endu. Zónově redundantní IP adresa může bezpečně sloužit zónovým prostředkem v každé zóně a tak dlouho, dokud jednu zónu zůstane v dobrém stavu v rámci oblasti přežijí nejméně jednomu selhání zóny. Naopak oblastmi front-endu je snížení služby na jednu zónu a sdílené složky rozpadu příslušných zónu.

Redundanci zón neznamená hitless datapath nebo rovina řízení;  je výslovně rovina dat. Zónově redundantní toků můžete použít všechny zóny a toků na základě budou používat všechny zóny v pořádku v oblasti. V případě selhání zóny přenosové toky pomocí v dobrém stavu zóny v daném okamžiku v čase vliv.  Přenosové toky pomocí zóny v době selhání zóna může mít vliv, ale můžete obnovit aplikací a tyto toky můžete pokračovat ve zbývajících v dobrém stavu zóny v rámci oblasti opakovaný přenos zpráv nebo reestablishment po Azure se sloučila kolem selhání zóny.

### <a name="xzonedesign"></a> Překračují hranice zóny

Je důležité pochopit, že kdykoli služby začátku do konce překročí zóny, sdílet pracuje s není jednu zónu, ale potenciálně několika zónami.  V důsledku toho začátku do konce služby nemusí získali jakékoli dostupnost přes-zónové nasazení.

Vyhýbejte se nežádoucích závislosti mezi zónami, které se nezruší se tím zvýšení dostupnosti při použití zón dostupnosti.  Pokud vaše aplikace se skládá z několika součástí a chcete být odolné vůči selhání zóny, musí postará k zajištění přežití dostatečná kritických komponent v případě selhání zóny.  Například jeden zásadní součástí pro vaši aplikaci může ovlivnit celé aplikace existuje pouze v zóně než zbývající zóna nebo zóny.  Kromě toho se také zvažte obnovení zóny a jak se vaše aplikace sloučit. Pojďme zkontrolovat některé klíčové body a použijte je jako inspirace pro dotazy pečlivě promyslete váš konkrétní scénář.

- Pokud aplikace obsahuje dvě komponenty, jako jsou IP adresy a virtuálních počítačů při použití spravovaného disku a jste zaručeno v zóně 1 a v zóně 2, při selhání vaší služby začátku do konce zóny 1 nebude překonat když zóna 1 se nezdaří.  Není napříč zóny, není-li plně chápete, že vytváříte režim potenciálně nebezpečné selhání.

- Pokud vaše aplikace má dvě součásti, jako jsou IP adresy a virtuální počítač s spravovaný disk a je zaručena zónově redundantní a zóna 1, respektive, vaše služba začátku do konce přežije zkázu zóny selhání v zóně 2 pro, zóna 3, nebo obojí Pokud zóna 1 se nezdařilo.  Však ztratíte nějakou možnost argumentovat o stav vaší služby. Pokud vše, co se vyčistily dostupnosti front-endu.  Zvažte možnost zavedení rozsáhlejší modelu stavu a kapacity.  Můžete společně použít zónové a zónově redundantní koncepty rozbalte insight a zlepšit možnosti správy.

- Pokud aplikace obsahuje dvě komponenty, jako jsou zónově redundantních front-endu nástroje pro vyrovnávání zatížení a škálovací sady virtuálních počítačů napříč zónami ve třech zónách, vaše prostředky v zónách není vliv selhání bude k dispozici, ale může mít snížený výkon vaší kapacity služby začátku do konce během selhání zóny. Z hlediska infrastruktury nasazení přežijí nejméně jednoho zóny, a to vyvolá na následující otázky:
  - Chápete, jak vaše aplikace důvodů, proč o takové selhání a snížení kapacity?
  - Je třeba mít bezpečnostní opatření ve své službě do páru oblastí v případě potřeby vynutit převzetí služeb při selhání?
  - Jak se můžete sledovat, jejich detekci a zmírnění takový scénář? Je možné, že se můžete pomocí diagnostiky Load balanceru úrovně Standard k posílení monitorování výkonu vaší služby začátku do konce. Zvažte, co je k dispozici a rozšíření co možná bude nutné pro dokončení obrázek.

- Zónám lze chyby snadněji porozuměl jsem jim a obsažené.  Selhání zóny se ale nijak neliší od jiné chyby při rozhodování o koncepty, jako jsou časové limity, opakovaných pokusů a omezení rychlosti algoritmy. I když Azure Load Balancer poskytuje zónově redundantní cesty a pokusí se rychle, obnovení na úrovni paketů v reálném čase, opakovaných nebo reestablishments může dojít během mozkového onemocnění v chybě a je důležité pochopit, jak vaše aplikace copes s selhání. Přežije zkázu schéma služby Vyrovnávání zatížení, ale je potřeba naplánovat následující:
  - Pokud zónu nezdaří, vaše služba začátku do konce chápe to a pokud dojde ke ztrátě stavu, jak se vám obnoví?
  - Po návratu zónu, aplikace pochopit, jak bezpečně sloučit?

### <a name="zonalityguidance"></a> Zónově redundantní a oblastmi

Zónově redundantní můžete zadat zónu nezávislá a na stejné možnosti čas odolné jednu IP adresu pro službu.  Pak ji lze omezit složitost.  Zónově redundantní také má mobility napříč zónami a lze jej bezpečně používat na prostředky v každé zóně.  Je také budoucnost v oblastech, které nemají zóny dostupnosti, které můžete omezit změny po oblast získat zóny dostupnosti.  Syntaxe konfigurace zónově redundantní IP adresu nebo front-endu úspěšná v libovolné oblasti, včetně těch, které nemají zóny dostupnosti.

Zónové může poskytnout explicitní záruka se zónou sdílení pracuje s stavu zóny. Přidružení oblastmi IP adresu nebo oblastmi front-endu nástroje pro vyrovnávání zatížení může být žádoucí nebo přiměřené atribut zejména v Pokud je připojené prostředků virtuálního počítače s oblastmi ve stejné zóně.  Nebo možná vaše aplikace vyžaduje explicitní znalosti o zóně, ve které je prostředek umístěn v a chcete explicitně odůvodnitelný v samostatných zón dostupnosti.  Můžete také zveřejnit několika oblastmi front-endů pro službu začátku do konce distribuované napříč zónami (tedy za zónu oblastmi front-endů pro více oblastmi virtuálního počítače škálovací sady).  Pokud vaše oblastmi front-endů jsou veřejné IP adresy, můžete pomocí těchto více oblastmi front-endů pro vystavení služby s [Traffic Manageru](../traffic-manager/traffic-manager-overview.md).  Nebo můžete použít několik front-endů zónové a získat za zónu přehledy stavu a výkonu prostřednictvím monitorování řešení třetích stran tak a zpřístupnit s zónově redundantních front-endové služby. Pouze by měla sloužit zónové prostředky s oblastmi front-endů zarovnán do stejné zóny a vyhnout potenciálně škodlivé napříč zónami scénáře pro zónové prostředky.  Zónové prostředky existují jenom v oblastech, kde existují zóny dostupnosti.

Neexistuje žádné obecné pokyny, že jeden je vhodnější než ten druhý bez znalosti architektury služby.

## <a name="limitations"></a>Omezení

- Zatímco dat roviny je plně zónově redundantní (Pokud byl zadán oblastmi se zárukou), operace roviny řízení nejsou plně zónově redundantní.

## <a name="next-steps"></a>Další postup
- Další informace o [zóny dostupnosti](../availability-zones/az-overview.md)
- Další informace o [Load Balanceru úrovně Standard](load-balancer-standard-overview.md)
- Zjistěte, jak [vyrovnávat zatížení virtuálních počítačů v rámci zóny pomocí Load balanceru úrovně Standard s oblastmi front-endu](load-balancer-standard-public-zonal-cli.md)
- Zjistěte, jak [vyrovnávat zatížení virtuálních počítačů napříč zónami Load balanceru úrovně Standard pomocí zónově redundantních front-endu](load-balancer-standard-public-zone-redundant-cli.md)
