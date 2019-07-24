---
title: Standard Load Balancer a Zóny dostupnosti Azure
titlesuffix: Azure Load Balancer
description: Load Balancer úrovně Standard a zóny dostupnosti
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/27/2018
ms.author: allensu
ms.openlocfilehash: 5ef7de148d5ef4727602b8287164f2aff9ccf822
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/16/2019
ms.locfileid: "68274503"
---
# <a name="standard-load-balancer-and-availability-zones"></a>Load Balancer úrovně Standard a zóny dostupnosti

Standardní SKU Azure Load Balancer podporuje scénáře [zóny dostupnosti](../availability-zones/az-overview.md) . V Standard Load Balancer je k dispozici několik nových konceptů, které vám umožní optimalizovat dostupnost v celém koncovém scénáři tím, že se zarovnají prostředky a distribuují je mezi zónami.  Přečtěte si [zóny dostupnosti](../availability-zones/az-overview.md) , kde najdete pokyny k tomu, co zóny dostupnosti, které oblasti momentálně podporují zóny dostupnosti, a další související koncepty a produkty. Zóny dostupnosti v kombinaci s Standard Load Balancer jsou obsáhlém a flexibilní sada funkcí, která může vytvářet mnoho různých scénářů.  Přečtěte si tento dokument, abyste pochopili tyto [Koncepty](#concepts) a základní [pokyny k návrhu](#design)scénářů.

>[!IMPORTANT]
>V tématu [zóny dostupnosti](../availability-zones/az-overview.md) najdete související témata, včetně informací o konkrétní oblasti.

## <a name="concepts"></a>Zóny dostupnosti koncepty použité pro Load Balancer

Mezi prostředky Load Balancer a skutečnou infrastrukturou nejsou žádné přímé vztahy. Vytvoření Load Balancer instanci nevytvoří. Prostředky Load Balancer jsou objekty, ve kterých můžete vyjádřit, jak by měl Azure naprogramovat svou předem vytvořenou infrastrukturu více tenantů, aby dosáhli scénáře, který chcete vytvořit.  To je důležité v souvislosti s Zóny dostupnosti, protože jeden prostředek Load Balancer může řídit programování infrastruktury ve více Zóny dostupnosti, zatímco se služba redundantní v rámci zóny zobrazuje jako jeden prostředek z pohledu zákazníka.

Funkce prostředku Load Balancer se vyjadřují jako front-end, pravidlo, sonda stavu a definice fondu back-endu.

V kontextu Zóny dostupnosti se chování a vlastnosti prostředku Load Balancer popisují jako redundantní nebo oblasti.  Redundantní zóna a oblast popisují zonality vlastnosti.  V souvislosti s Load Balancer se v zóně – redundantní vždy znamená, že *několik zón a oblastí* znamená izolaci služby do *jedné zóny*.

Veřejné i interní Load Balancer podporují scénáře redundantních a oblastí a oba můžou směrovat provoz napříč zónami podle potřeby (vyrovnávání*zatížení mezi zónami*).

Prostředek Load Balancer sám o sobě není oblast.  A virtuální síť a podsíť jsou vždy oblastní a nikdy nepracují.

### <a name="frontend"></a>Endy

Front-end Load Balancer je konfigurace IP adresy front-endu, která odkazuje buď na prostředek veřejné IP adresy, nebo na privátní IP adresu v rámci sítě virtuálního síťového prostředku.  Vytvoří koncový bod s vyrovnáváním zatížení, ve kterém je vaše služba vystavená.

Prostředek Load Balancer může obsahovat současně i zóny i redundantní front-endy. 

Když je prostředek veřejné IP adresy zaručený pro zónu, zonality (nebo nejeho absence) není proměnlivý.  Pokud chcete změnit nebo vynechat zonality veřejné IP adresy front-endu, je nutné znovu vytvořit veřejnou IP adresu v příslušné zóně.  

Zonality front-endu interního Load Balancer můžete změnit tak, že odeberete a znovu vytvoříte front-end, změníte nebo vynecháte zonality.

Pokud používáte víc front-endu, Projděte si [několik front-endu pro Load Balancer](load-balancer-multivip-overview.md) s důležitými informacemi.

#### <a name="zone-redundant-by-default"></a>Zóna redundantní ve výchozím nastavení

>[!IMPORTANT]
>V tématu [zóny dostupnosti](../availability-zones/az-overview.md) najdete související témata, včetně informací o konkrétní oblasti.

V oblasti s Zóny dostupnosti je Standard Load Balancer front-endu ve výchozím nastavení redundantní v zóně.  Jedna IP adresa front-endu může proběhnout při selhání zóny a dá se použít k přístupu ke všem členům back-end fondu bez ohledu na zónu. To neznamená hitless cestu k datům, ale všechny opakované pokusy nebo obnovení budou úspěšné. Schémata redundance DNS se nevyžadují. Jedna IP adresa front-endu je souběžně obsluhována několika nenezávislými nasazeními infrastruktury ve více Zóny dostupnosti.  Zóna – redundantní znamená, že všechny příchozí nebo odchozí toky jsou obsluhovány více Zóny dostupnosti v oblasti současně pomocí jediné IP adresy.

Jeden nebo více Zóny dostupnosti může selhat a cesta k datům zůstává v pořádku, dokud jedna zóna v oblasti zůstane v dobrém stavu. Výchozí konfigurace zóny je redundantní a nevyžaduje žádné další akce.  

Použijte následující skript k vytvoření veřejné IP adresy redundantní zóny pro interní Standard Load Balancer. Pokud v konfiguraci používáte existující šablony Správce prostředků, přidejte k těmto šablonám oddíl **SKU** .

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

Pomocí následujícího skriptu vytvořte IP adresu redundantní zóny front-endu pro interní Standard Load Balancer. Pokud v konfiguraci používáte existující šablony Správce prostředků, přidejte k těmto šablonám oddíl **SKU** .

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

#### <a name="optional-zone-isolation"></a>Volitelná izolace zóny

Můžete zvolit, aby front-end byl zaručen pro jednu zónu, která se nazývá *oblast front-endu*.  To znamená, že jakýkoliv příchozí nebo odchozí tok je obsluhován jedinou zónou v oblasti.  Vaše sdílená složka front-endu sepravila se stavem zóny.  Cesta k datům není ovlivněná chybami v jiných zónách, než kde byla zaručena. K vystavení IP adresy na jednu zónu dostupnosti můžete použít oblast front-endu.  Kromě toho můžete využít front-endu přímo nebo, pokud se front-end skládá z veřejných IP adres, integrovat je s produktem pro vyrovnávání zatížení DNS, jako je [Traffic Manager](../traffic-manager/traffic-manager-overview.md) , a používat jeden název DNS, který klient bude překládat na více IP adres. .  Tuto možnost můžete použít také k vystavení koncovým bodům pro vyrovnávání zatížení zóny pro samostatné monitorování každé zóny.  Pokud chcete tyto koncepty (zóny redundantní a oblasti pro stejný back-end) kombinovat, Projděte si téma [více front-endu pro Azure Load Balancer](load-balancer-multivip-overview.md).

U veřejné Load Balancer front-endu přidejte parametr *Zones* k veřejné IP adrese, na kterou odkazuje konfigurace IP adresy front-endu.  

U interního front-endu Load Balancer přidejte  do konfigurace protokolu IP front-endu interní Load Balancer parametr Zones. Oblast front-end způsobí, že Load Balancer garantuje IP adresu v podsíti s konkrétní zónou.

Pomocí následujícího skriptu vytvořte v Zóna 1 dostupnosti standardní veřejnou IP adresu pro oblast. Pokud v konfiguraci používáte existující šablony Správce prostředků, přidejte k těmto šablonám oddíl **SKU** .

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

Pomocí následujícího skriptu vytvořte interní Standard Load Balancer front-end v Zóna 1 dostupnosti.

Pokud v konfiguraci používáte existující šablony Správce prostředků, přidejte k těmto šablonám oddíl **SKU** . Také definujte vlastnost **Zones** v konfiguraci protokolu IP front-endu pro podřízený prostředek.

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

Vyrovnávání zatížení mezi zónami je schopnost Load Balancer získat přístup ke koncovému bodu back-endu v libovolné zóně a nezávisle na front-endu a zonality.

Pokud chcete nasazovat a zaručit nasazení v rámci jedné zóny, zarovnejte prostředky back-end a rozmístění back-endu do stejné zóny. Nevyžaduje se žádná další akce.

### <a name="backend"></a>Back-end

Load Balancer spolupracuje s Virtual Machines.  Libovolný virtuální počítač v jedné virtuální síti může být součástí back-endu fondu bez ohledu na to, jestli je nebo není zaručená zóna nebo která zóna byla zaručena.

Pokud chcete zajistit, aby se front-end a back-end s jednou zónou rovnaly a zajistily, umístěte jenom virtuální počítače v rámci stejné zóny do příslušného back-endu.

Pokud chcete virtuální počítače adresovat v rámci více zón, Stačí umístit virtuální počítače z několika zón do stejného back-endu.  Při používání virtuálních počítačů Virtual Machine Scale Sets můžete do stejného back-endu umístit jednu nebo víc sad škálování virtuálního počítače.  Všechny tyto sady škálování virtuálních počítačů můžou být v jedné nebo několika zónách.

### <a name="outbound-connections"></a>Odchozí připojení

[Odchozí připojení](load-balancer-outbound-connections.md) jsou obsluhována všemi zónami a jsou automaticky redundantní v zóně v oblasti s zóny dostupnosti, když je virtuální počítač přidružený k veřejnému Load Balancer a záložnímu front-endovému typu zóny.  Odchozí připojení port SNAT má za sebou selhání zóny.  

Pokud je virtuální počítač přidružený k veřejnému Load Balancer a oblast front-endu, budou odchozí připojení zaručena jedinou zónou.  Odchozí připojení sdílí rozpad s příslušným stavem zóny.

Předběžné přidělení portu SNAT a algoritmus jsou stejné jako u zón nebo bez nich.

### <a name="health-probes"></a>Sondy stavu

Vaše existující definice sondy stavu zůstávají beze Zóny dostupnosti.  Rozšířili jsme ale model stavu na úrovni infrastruktury. 

Pokud používáte zóny s redundantními servery, Load Balancer rozbalí svůj interní stavový model tak, aby nezávisle provedl test dostupnosti virtuálního počítače z každé zóny dostupnosti, a vypnul cesty mezi zónami, které se mohly nezdařily, bez zásahu zákazníka.  Pokud daná cesta není k dispozici z Load Balancer infrastruktury jedné zóny k virtuálnímu počítači v jiné zóně, Load Balancer může tuto chybu detekovat a vyhnout se této chybě. Jiné zóny, které se můžou připojit k tomuto virtuálnímu počítači, můžou dál obsluhovat virtuální počítač ze svých front-endu.  V důsledku toho je možné, že během událostí selhání mohou mít každá zóna mírně odlišnou distribuci toků a současně chránit celkový stav komplexní služby.

## <a name="design"></a>Faktory návrhu

Load Balancer je účelově flexibilní v kontextu Zóny dostupnosti. Můžete si vybrat, že se mají zarovnat do zón, nebo se můžete rozhodnout pro zónu – redundantní.  Vyšší dostupnost může zacházet z ceny zvýšené složitosti a je třeba navrhnout dostupnost pro zajištění optimálního výkonu.  Pojďme se podívat na důležité informace o návrhu.

### <a name="automatic-zone-redundancy"></a>Automatická redundance zóny

Load Balancer usnadňuje jednu IP adresu jako front-redundantní front-end. Redundantní IP adresa v zóně může bezpečně obsluhovat prostředky oblastí v libovolné zóně a může zabývat jedné nebo více selhání zóny, pokud jedna zóna zůstane v dobrém stavu v oblasti. Naopak front-end je omezení služby na jednu zónu a podílí se na ní osud v příslušné zóně.

Zóna – redundance neznamená hitlessou datacestu nebo rovinu ovládacího prvku;  rovina dat je Express. Neredundantní toky v zóně můžou používat libovolné zóny a toky zákazníka použijí všechny v pořádku zóny v oblasti. V případě selhání zóny nebudou ovlivněny provozní toky, které používají zóny v pořádku v daném časovém okamžiku.  Může to mít vliv na přenosové toky pomocí zóny v době selhání zóny, ale aplikace se můžou zotavit. Tyto toky můžou pokračovat ve zbývajících zónách v oblasti po opětovném přenosu nebo opětovném vytvoření, jakmile se Azure Sblíženo se selháním zóny.

### <a name="xzonedesign"></a>Hranice mezi zónami

Je důležité si uvědomit, že při každém přechodu mezi koncovými službami procházejí všechny zóny, takže můžete sdílet rozpad bez jedné zóny, ale potenciálně více zón.  V důsledku toho vaše koncová služba pravděpodobně nezískala žádnou dostupnost v rámci nasazení mimo oblast.

Vyhněte se představení nezamýšlených závislostí mezi zónami, které při použití Zóny dostupnosti nezruší zisky dostupnosti.  Pokud se vaše aplikace skládá z několika součástí a chcete být odolná vůči selhání zóny, musíte se ujistit, že v případě selhání zóny budete mít dostatečné kritické součásti.  Například jediná kritická součást vaší aplikace může mít vliv na celou aplikaci, pokud existuje pouze v jiné zóně než zóny, na kterých se nachází.  Kromě toho zvažte také obnovení zóny a způsob sblížení aplikace. Pojďme se podívat na některé klíčové body a použít je jako inspiraci pro otázky, jak si myslíte podle svého konkrétního scénáře.

- Pokud má vaše aplikace dvě komponenty, jako je IP adresa a virtuální počítač se spravovaným diskem a jsou zaručené v zóně 1 a 2, v případě, že zóna 1 selže, nebude vaše koncová služba zachována, když zóna 1 selže.  Nevybírejte mezi zónami, pokud plně nerozumíte, že vytváříte potenciálně nebezpečný režim selhání.

- Pokud má vaše aplikace dvě komponenty, jako je IP adresa a virtuální počítač se spravovaným diskem, a zaručuje se, že vaše koncová služba bude zachována při selhání zóny 2, zóny 3 nebo obou, pokud se nezdařila zóna 1.  Nemůžete ale přijít o případnou schopnost vaší služby, pokud je všechno, co se vám právě vystavuje, dostupnost front-endu.  Zvažte vývoj rozsáhlejšího modelu stavu a kapacity.  K rozšíření přehledu a možností správy můžete využít koncepty a oblasti v zóně.

- Pokud má vaše aplikace dvě komponenty, jako je například zóna redundantní Load Balancer front-endu a škálování virtuálního počítače mezi zónami ve třech zónách, budou k dispozici prostředky v zónách neovlivněné selháním, ale může dojít ke snížení výkonu vaší koncové kapacity služby. během selhání zóny. Z hlediska infrastruktury může vaše nasazení zamezit selhání jedné nebo více zón a tato akce vyvolá následující otázky:
  - Porozumíte tomu, jak vaše aplikace vede k takovým selháním a snížení kapacity?
  - Potřebujete v rámci služby zabezpečení a v případě potřeby vynutit převzetí služeb při selhání dvojici oblastí?
  - Jak budete monitorovat, zjišťovat a zmírnit takový scénář? Je možné používat diagnostiku Standard Load Balancer k rozšíření monitorování komplexního výkonu služby. Zvažte, co je k dispozici a co může pro kompletní obrázek vyžadovat rozšíření.

- Zóny můžou chyby snadněji pochopit a zahrnout.  Selhání zóny se ale neliší od jiných selhání, když přichází k koncepcím, jako jsou časové limity, opakování a omezení rychlosti algoritmy. I když Azure Load Balancer poskytuje cesty redundantní v zóně a snaží se rychle obnovit na úrovni paketů v reálném čase, může dojít k opakovanému přenosu nebo opětovnému navázání. je důležité pochopit, jak se vaše aplikace kopiemi s úspěšně. Vaše schéma vyrovnávání zatížení bude zachováno, ale je třeba naplánovat následující:
  - Pokud dojde k chybě zóny, bude vaše koncová služba rozumět této službě a pokud dojde ke ztrátě stavu, jak budete obnovovat?
  - Když se zóna vrátí, aplikace porozumí, jak bezpečně konvergovat?

### <a name="zonalityguidance"></a>Redundantní zóna mimo oblast

>[!IMPORTANT]
>V tématu [zóny dostupnosti](../availability-zones/az-overview.md) najdete související témata, včetně informací o konkrétní oblasti.

Redundantní zóna může poskytovat zónu – nezávislá a současně neodolnou možnost s jednou IP adresou pro službu.  Může to snížit složitost.  Redundantní zóna také nabízí mobilitu mezi zónami a může být bezpečně používána na prostředky v libovolné zóně.  Kromě toho je budoucí kontrola v oblastech bez Zóny dostupnosti, což může omezit změny požadované v případě, že oblast získá Zóny dostupnosti.  Syntaxe konfigurace redundantní IP adresy nebo front-endu v zóně se úspěšně provedla v jakékoli oblasti, včetně těch, které nejsou Zóny dostupnosti.

Oblast může poskytnout explicitní záruku pro zónu a sdílet osud se stavem zóny. Přidružení hraniční IP adresy nebo oblasti Load Balancer front-endu může být vhodným nebo rozumným atributem, zejména v případě, že připojený prostředek je virtuální počítač ve stejné zóně.  Nebo možná vaše aplikace vyžaduje explicitní znalosti o tom, ve které zóně se prostředek nachází, a vy chcete důvodně získat informace o dostupnosti v samostatných zónách explicitně.  Můžete zvolit, aby se pro koncovou službu distribuovanou v různých zónách vystavilo několik front-endu (to znamená, že zóna má na front-endové služby pro víc virtuálních počítačů Scale Sets).  A pokud vaše oblast front-endu jsou veřejné IP adresy, můžete použít tyto více front-endu pro vystavení vaší služby s [Traffic Manager](../traffic-manager/traffic-manager-overview.md).  Nebo můžete použít více front-endu k získání informací o stavu jednotlivých zón a přehledy o výkonu prostřednictvím řešení monitorování třetích stran a zpřístupnit celkovou službu pomocí předávacího typu zóny redundantní. Měli byste používat jenom ty prostředky s oblastí front-endu zarovnané do stejné zóny a vyhnout se potenciálně škodlivým scénářům pro různé zóny pro oblasti prostředků.  Prostředky oblasti jsou k dispozici pouze v oblastech, kde Zóny dostupnosti existovat.

Neexistují žádné obecné pokyny, které je lepší volbou než druhá bez znalosti architektury služby.

## <a name="limitations"></a>Omezení

- Zatímco rovina dat je zcela redundantní (není-li zaručena oblastce), provozní operace roviny nejsou plně v zóně redundantní.

## <a name="next-steps"></a>Další postup
- Další informace o [zóny dostupnosti](../availability-zones/az-overview.md)
- Další informace o [Load Balanceru úrovně Standard](load-balancer-standard-overview.md)
- Přečtěte si, jak [vyrovnávat zatížení virtuálních počítačů v rámci zóny pomocí Standard Load Balancer s oblastí front-endu](load-balancer-standard-public-zonal-cli.md) .
- Přečtěte si, jak [vyrovnávat zatížení virtuálních počítačů napříč zónami pomocí Standard Load Balancer s frontou redundantním front-endu](load-balancer-standard-public-zone-redundant-cli.md)
