---
title: Nástroje pro vyrovnávání zatížení Azure standardní a dostupnosti zón | Microsoft Docs
description: Nástroj pro vyrovnávání zatížení standardní a dostupnosti zón
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2018
ms.author: kumud
ms.openlocfilehash: 9f5a68972015f54e2333199652075cda2535a3c8
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="standard-load-balancer-and-availability-zones"></a>Nástroj pro vyrovnávání zatížení standardní a dostupnosti zón

Podporuje Azure Vyrovnávání zatížení standardní SKU [dostupnost zóny](../availability-zones/az-overview.md) scénáře. Několik nových konceptů jsou k dispozici s standardní pro vyrovnávání zatížení, které umožňují optimalizovat dostupnosti ve vašem scénáři začátku do konce zarovnání prostředky zón a také rozprostřít do zóny.  Zkontrolujte [dostupnost zóny](../availability-zones/az-overview.md) pro co dostupnost zóny jsou pokyny, které oblasti v současné době podporují dostupnost zóny a další související koncepty a produkty. Dostupnost zóny v kombinaci s nástroj pro vyrovnávání zatížení je sada funkcí obsáhlou a flexibilní, můžete vytvořit mnoho různých scénářů.  Přečtěte si tento dokument pochopit tyto [koncepty](#concepts) a základní scénáře [návrh pokyny](#design).

>[!NOTE]
>Zkontrolujte [dostupnost zóny Preview](https://aka.ms/availabilityzones) další související témata. 

## <a name="concepts"></a> Koncepty dostupnost zóny použít nástroj pro vyrovnávání zatížení

Není žádný přímý vztah mezi prostředky pro vyrovnávání zatížení a skutečný infrastruktury; vytvoření služby Vyrovnávání zatížení nepodporuje vytvoření instance. Prostředky nástroje pro vyrovnávání zatížení jsou objekty v rámci kterých můžete express, jak by měla Azure programu jeho předem infrastruktuře víceklientské k dosažení scénáře, který chcete vytvořit.  To je důležité v kontextu dostupnosti zón, protože jeden prostředek pro vyrovnávání zatížení můžete řídit programování infrastruktury ve více zónách dostupnosti, zatímco zónově redundantní služby se zobrazí jako jeden prostředek z hlediska zákazníka.

Funkce prostředek pro vyrovnávání zatížení jsou vyjádřené jako front-end, pravidlo, test stavu a definici fondu back-end.

V kontextu dostupnosti zóny jsou popsány chování a vlastnosti prostředku, nástroj pro vyrovnávání zatížení jako zónově redundantní nebo oblastmi.  Zónově redundantní a oblastmi popisují zonality vlastnosti.  V kontextu služby Vyrovnávání zatížení, zónově redundantní vždy znamená *všechny zóny* a oblastmi znamená zaručující služby *jedné oblasti*.

Veřejné a interní nástroj pro vyrovnávání zatížení podporují scénáře zónově redundantní a oblastmi a podle potřeby, jak může směrovat přenosy mezi zóny (*Vyrovnávání zatížení mezi pásem*).

Samotný prostředek pro vyrovnávání zatížení je místní a nikdy oblastmi.  A virtuálních sítí a podsítí jsou vždy regionálního a nikdy oblastmi.

### <a name="frontend"></a>Front-end

Konfigurace IP front-endu odkazující na prostředek veřejné IP adresy nebo privátní IP adresy v rámci podsítě virtuální sítě prostředku je front-end pro vyrovnávání zatížení.  Ho tvoří koncový bod Vyrovnávání zatížení, kde je vystaven služby.

Nástroj pro vyrovnávání zatížení prostředků může obsahovat oblastmi a zónově redundantní frontends současně.

Pokud prostředek veřejné IP má byla zaručena na zónu, zonality (nebo neexistenci) není měnitelný.  Pokud chcete změnit nebo vynechejte zonality z veřejných IP front-endu, budete muset znovu vytvořit veřejné IP adresy v příslušnou zónu.  

Odebráním a znovu vytvořit front-endu, změna nebo vynechání zonality, můžete změnit zonality front-end interní pro vyrovnávání zatížení.

Pokud používáte více frontends, zkontrolujte [více frontends nástroje pro vyrovnávání zatížení](load-balancer-multivip-overview.md) důležité informace.

#### <a name="zone-redundant-by-default"></a>Zónově redundantní ve výchozím nastavení

V oblasti dostupnosti zón je nástroj pro vyrovnávání zatížení front-end zónově redundantní ve výchozím nastavení.  Na jednom front-endovou IP adresu přežijí selhání zóny a můžete použít k dosažení všech členů fondu back-end bez ohledu na zóny. To neznamená cesta hitless data, ale žádné opakování nebo reestablishment bude úspěšné. Schémata redundance DNS nejsou nutné. Jednu IP adresu front-endu obsluhuje nezávislé infrastruktury nasazení v každé zóně dostupnosti současně.  Zónově redundantní znamená, že všechny příchozí nebo odchozí toky zpracovává všechny zóny dostupnosti v oblasti současně pomocí jediné IP adresy.

Jeden nebo více zón dostupnosti může selhat a cestu k datům odolává stejně dlouho jako jednu zónu v oblasti i nadále v pořádku. Zónově redundantní konfigurace je výchozí a nevyžaduje žádné další akce.  Když v oblasti získá možnost podpory dostupnost zóny, stane stávající front-end zónově redundantní automaticky.

Pomocí následujícího skriptu pro vytvoření zónově redundantní veřejné IP adresy pro interní standardní nástroj pro vyrovnávání zatížení. Pokud používáte stávající šablony Resource Manageru ve vaší konfiguraci, přidejte **sku** části tyto šablony.

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

Pomocí následujícího skriptu k vytvoření zónově redundantní front-end IP adresy pro interní standardní nástroj pro vyrovnávání zatížení. Pokud používáte stávající šablony Resource Manageru ve vaší konfiguraci, přidejte **sku** části tyto šablony.

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

#### <a name="optional-zone-guarantee"></a>Volitelné zóny záruku

Můžete mít front-end záruku, že jedné oblasti, která se označuje jako *oblastmi front-endu*.  To znamená, že všechny příchozí nebo odchozí tok je poskytovaný jedné oblasti v oblasti.  Vaše front-endu sdílí rozklad s stav zóny.  Cesta k datům není ovlivněna selhání v zónách než kdy byla zaručena. Oblastmi frontends můžete použít ke zveřejnění IP adresu podle dostupnosti zóny.  Navíc můžete využívat oblastmi frontends přímo nebo, pokud front-endu se skládá z veřejných IP adres integrovat s produktu jako Vyrovnávání zatížení DNS [Traffic Manager](../traffic-manager/traffic-manager-overview.md) a používat jeden název DNS, která bude klient vyřešit více oblastmi IP adres.  To můžete použít ke zveřejnění na zónu zatížení koncové body s vyrovnáváním jednotlivě monitorování každou zónu.  Pokud chcete přizpůsobte tyto koncepty (zónově redundantní a oblastmi pro stejnou back-end), přečtěte si [více frontends nástroje pro vyrovnávání zatížení Azure](/load-balancer-multivip-overview.md).

Pro veřejné front-end pro vyrovnávání zatížení, přidáte *zón* parametr pro veřejné IP adresy odkazuje front-endovou konfiguraci protokolu IP.  

Pro interní front-end pro vyrovnávání zatížení, přidejte *zón* parametr pro konfiguraci IP front-endu interní nástroj pro vyrovnávání zatížení. Oblastmi front-endu způsobí, že nástroj pro vyrovnávání zatížení zaručit IP adresu v podsíti pro konkrétní zónu.

Pomocí následujícího skriptu k vytvoření oblastmi standardní veřejnou IP adresu v dostupnosti zóny 1. Pokud používáte stávající šablony Resource Manageru ve vaší konfiguraci, přidejte **sku** části tyto šablony.

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

Pomocí následujícího skriptu k vytvoření k interní nástroj pro vyrovnávání zatížení front-endu v dostupnosti zóny 1.

Pokud používáte stávající šablony Resource Manageru ve vaší konfiguraci, přidejte **sku** části tyto šablony. Navíc definovat **zón** vlastnosti v konfiguraci IP front-endu pro podřízený prostředek.

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

### <a name="cross-zone-load-balancing"></a>Vyrovnávání zatížení mezi zóny

Vyrovnávání zatížení mezi zóny je schopnost Vyrovnávání zatížení k dosažení koncový bod back-end v každé zóně a je nezávislý na front-endové a jeho zonality.

Pokud chcete zarovnat a zaručit nasazení v jedné oblasti, zarovnejte oblastmi front-endové a back-endové oblastmi prostředky do stejné zóny. Není vyžadována žádná další akce.

### <a name="backend"></a>Back-end

Nástroj pro vyrovnávání zatížení funguje s virtuálními počítači.  Žádné virtuální počítače v jedné virtuální sítě můžou být součástí fondu typu back end bez ohledu na to, zda byla zaručena na zónu, nebo bylo zaručeno, které zóny.

Pokud chcete zarovnat a zaručit front-endové a back-end pomocí jedné oblasti, pouze umístíte virtuální počítače v rámci stejné zóny do příslušných back-end fondu.

Pokud chcete na adresu virtuální počítače napříč několika zón, jednoduše umístíte virtuální počítače z několika zón do stejného fondu back-end.  Při použití škálování virtuálních počítačů sad, můžete umístit jeden nebo více sady škálování virtuálního počítače do stejného fondu back-end.  A každý z těchto sady škálování virtuálního počítače může být v jedné nebo několika zón.

### <a name="outbound-connections"></a>Odchozí připojení

[Odchozí připojení](load-balancer-outbound-connections.md) zpracovává všechny zóny a jsou automaticky zónově redundantní v oblasti dostupnosti zón, když virtuální počítač přiřazen k veřejné nástroje pro vyrovnávání zatížení a zónově redundantní front-endu.  Přidělení port překládat pomocí SNAT odchozí připojení i po selhání zóny.  

Naopak pokud je virtuální počítač přiřazený k veřejné nástroje pro vyrovnávání zatížení a oblastmi front-endu, odchozí připojení je zaručeno rutinou jedné oblasti.  Odchozí připojení sdílet rozklad s stav příslušných zóny.

Předběžné přidělení překládat pomocí SNAT portu a algoritmus je stejný s nebo bez zón.

### <a name="health-probes"></a>Sondy stavu

Vaše stávající definice test stavu zůstane tak, jak jsou bez zón dostupnosti.  Ale byly rozšířeny health model na úrovni infrastruktury. 

Při použití zónově redundantní frontends, nástroj pro vyrovnávání zatížení rozšíří jeho vnitřní stav modelu nezávisle testu dostupnosti virtuálního počítače ze zóny každý dostupnosti a vypnout cesty napříč zón, které může bez zásahu zákazníka selhaly.  Pokud zadaná cesta není k dispozici od infrastruktury pro vyrovnávání zatížení jednu zónu pro virtuální počítač v jiné zóně, nástroj pro vyrovnávání zatížení můžete zjišťovat a vyhnout této chybě. Další zóny, kteří mohou být využity tento virtuální počítač můžete nadále poskytovat virtuální počítač z jejich odpovídajících frontends.  V důsledku toho je možné, že během události chyb, každou zónu může mít mírně odlišný toku distribuce při ochraně celkový stav služby začátku do konce.

## <a name="design"></a> Aspekty návrhu

Nástroj pro vyrovnávání zatížení je účelově flexibilní v kontextu dostupnosti zóny. Můžete zarovnat do zóny nebo můžete být zónově redundantní.  Zvýšené dostupnosti můžou mít za cenu vyšší složitost a je třeba navrhnout dostupnosti pro optimální výkon.  Podívejme se na některé důležité aspekty návrhů.

### <a name="automatic-zone-redundancy"></a>Automatické zálohování zóny

Nástroj pro vyrovnávání zatížení usnadňuje mít jednu IP adresu jako zónově redundantní front-endu. Zónově redundantní IP adresa může bezpečně obsluhovat oblastmi prostředků v každé zóně a přežijí jeden nebo více selhání zóny, dokud jednu zónu zůstane v rámci oblasti v pořádku. Naopak oblastmi front-end, který je služby k jedné zóny a sdílené složky rozklad s příslušnými zóny.

Zálohování zóny neznamená hitless datapath nebo rovině řízení;  je výslovně roviny data. Zónově redundantní toky můžete použít žádné zóny a zákazníka toky budou používat všechny zóny v pořádku v oblasti. V případě selhání zóny nebude mít dopad tok přenosů dat pomocí pořádku zón v tomto bodě v čase.  Může být ovlivněný tok přenosů dat pomocí zónu v době selhání zóny, ale můžete obnovit aplikace a tyto toky může pokračovat ve zbývajících pořádku zóny v rámci oblasti opakování přenosu nebo reestablishment po Azure má konvergované kolem selhání zóny.

### <a name="xzonedesign"></a> Zóna hranice mezi

Je důležité si uvědomit, že kdykoli služby začátku do konce protne zóny, rozklad sdílíte s není jednu zónu, ale potenciálně několika zón.  V důsledku toho služby začátku do konce nemusí se dostalo dostupnosti proveďte přes jiný oblastmi nasazení.

Nedošlo k neúmyslnému zóny napříč závislosti, které bude nezruší se tím zvýšení dostupnosti při použití zón dostupnosti zavedení.  Pokud vaše aplikace skládá z několika součástí a chcete být odolné vůči selhání zóny, musí postará k zachování dostatečná důležité součásti v případě selhání zóny.  Například jeden zásadní součástí pro aplikaci může ovlivnit celá aplikace existuje pouze v zóně než zbývající zón.  Kromě toho se také zvážit obnovení zóny a jak se vaše aplikace sladí. Umožňuje zkontrolovat některé klíčové body a použijte je jako inspiraci pro dotazy pečlivě promyslete konkrétní scénář.

- Pokud vaše aplikace má dvě součásti jako IP adresu a virtuální počítač s spravovaných disků a jste zaručit v zóně 1 a nebude přežijí zóny 2, když se zóna 1 nezdaří služby začátku do konce při zóny 1 se nezdaří.  Nemáte křížová zón, pokud plně pochopit, že vytváříte potenciálně nebezpečné selhání režimu.

- Pokud vaše aplikace má dvě součásti, jako jsou IP adresy a virtuální počítač s spravované disku a jsou zaručit zónově redundantní a zónu 1 v uvedeném pořadí, služby začátku do konce zůstávají v platnosti zóny selhání zóny 2, zónu 3, nebo obojí Pokud zóny 1 se nezdařilo.  Však ztratíte některé možnost do důvod o stavu služby pokud všechny, které jsou sledování dostupnosti front-endu.  Zvažte, vývoji rozsáhlejší modelu stavu a kapacity.  Bude pravděpodobně možné společně použít zónově redundantní a oblastmi koncepty rozbalte přehledy a možnosti správy.

- Pokud aplikace obsahuje dvě komponenty, jako jsou zónově redundantní front-endové služby Vyrovnávání zatížení a škálovací sadu virtuálních počítačů mezi zóny v tři zóny, vaše prostředky v zónách není ovlivněn selhání bude k dispozici, ale služby začátku do konce může být narušena z hlediska kapacita při selhání zóny. Z hlediska infrastruktuře nasazení přežijí jeden nebo více selhání zóny a to vyvolá na následující otázky:
  - Víte, jak vaše aplikace důvodů, proč o tyto chyby a sníženou kapacity?
  - Je potřeba mít bezpečnostní opatření ve službě vynutit převzetí služeb při selhání pár oblast v případě potřeby?
  - Jak budete monitorovat, zjišťovat a zmírnit takové situaci? Bude pravděpodobně možné použít nástroj pro vyrovnávání zatížení diagnostiky k posílení monitorování výkon služby začátku do konce. Zvažte, co je k dispozici a co rozšíření potřebovat k dokončení obrázku.

- Zóny můžete nastavit selhání snadněji porozuměl jsem jim a obsažené.  Selhání zóny je však nejsou jiné než jiné chyby, pokud jde o koncepty jako vypršení časových limitů opakování a omezení rychlosti algoritmy. I když nástroj pro vyrovnávání zatížení Azure poskytuje zónově redundantní cesty a pokusí se rychle obnovit na úrovni paketů v reálném čase, opakovaných přenosů nebo reestablishments může dojít během navázání k chybě a je důležité pochopit, jak vaše aplikace copes s selhání. Zůstávají v platnosti schéma vyrovnávání zatížení, ale je potřeba naplánovat následující:
  - Pokud se nezdaří zónu, služby začátku do konce pochopit to a pokud dojde ke ztrátě stavu, jak bude obnovení?
  - Po návratu zónu aplikace pochopit, jak bezpečně sloučit?

### <a name="zonalityguidance"></a> Zone-redundant versus zonal

Zónově redundantní můžete poskytnout zóny na úlohách a na stejný čas odolné možnost s jednu IP adresu adrese pro službu.  Pak může snížit složitost.  Zónově redundantní také má mobility napříč zóny a můžete bezpečně používat na prostředky v každé zóně.  Je také budoucí testování v oblastech bez zón dostupnosti, které můžete omezit na změny po oblast získat dostupnost zóny.  Konfigurace syntaxe zónově redundantní IP adresu nebo front-endu úspěšné v libovolné oblasti, včetně těch bez zón dostupnosti.

Oblastmi můžete poskytnout explicitní záruku na zónu, sdílení rozklad s stav zóny. Přidružení oblastmi IP adresu nebo oblastmi front-endu nástroj pro vyrovnávání zatížení může být žádoucí nebo přiměřené atribut obzvláště pokud připojené prostředku je oblastmi virtuálního počítače ve stejné zóny.  Nebo možná vaše aplikace vyžaduje explicitní znalosti o zóně, ve které je prostředek umístěn ve a chcete explicitně důvodu o dostupnosti v samostatných zón.  Můžete se rozhodnout vystavit několika oblastmi frontends služby začátku do konce distribuovaná do zóny (který je za oblastmi frontends zóny pro více škálování oblastmi virtuálních počítačů nastaví).  A pokud jsou vaše oblastmi frontends veřejné IP adresy, můžete použít tyto několika oblastmi frontends pro vystavení služby prostřednictvím [Traffic Manager](../traffic-manager/traffic-manager-overview.md).  Nebo můžete několika oblastmi frontends získat za zóny přehled stavu a výkonu prostřednictvím sledování řešení třetí strany a vystavit službu celkové s zónově redundantní front-endu. Pouze by měla sloužit oblastmi prostředky s oblastmi frontends zarovnán stejné zóny a vyhnout se potenciálně škodlivé scénáře zóny mezi oblastmi prostředky.  Oblastmi prostředky existují jenom v oblastech, kde existují dostupnost zóny.

Neexistuje žádné obecné pokyny, že jeden je vhodnější než jiné, aniž by věděly, architektura služby.

## <a name="limitations"></a>Omezení

- Při datové roviny je plně zónově redundantní (Pokud byl zadán oblastmi záruku), operace rovině řízení nejsou plně redundantní pro zónu.

## <a name="next-steps"></a>Další postup
- Další informace o [dostupnost zóny](../availability-zones/az-overview.md)
- Další informace o [nástroj pro vyrovnávání zatížení](load-balancer-standard-overview.md)
