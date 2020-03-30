---
title: Co je služba Azure Private Link?
description: Přečtěte si o službě Azure Private Link.
services: private-link
author: sumeetmittal
ms.service: private-link
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: sumi
ms.openlocfilehash: 2cc6c577abdb3698ef6aca1f1f04d239f09d119c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280427"
---
# <a name="what-is-azure-private-link-service"></a>Co je služba Azure Private Link?

Služba Azure Private Link je odkaz na vaši vlastní službu, která je založená na Azure Private Link. Vaše služba, která běží za [Azure Standard Balancer](../load-balancer/load-balancer-standard-overview.md) může být povolena pro přístup privátní odkaz tak, aby spotřebitelé k vaší službě přístup soukromě z vlastních virtuálních sítí. Vaši zákazníci můžete vytvořit soukromý koncový bod uvnitř své virtuální sítě a mapovat na tuto službu. Tento článek vysvětluje koncepty související se stranou poskytovatele služeb. 

## <a name="workflow"></a>Pracovní postup

![Pracovní postup služby Private Link](media/private-link-service-overview/private-link-service-workflow.png)

### <a name="create-your-private-link-service"></a>Vytvořte si službu private link

- Nakonfigurujte aplikaci tak, aby běžela za standardním nástrojem pro vyrovnávání zatížení ve vaší virtuální síti. Pokud již máte aplikaci nakonfigurovanou za standardním vyrovnáváním zatížení, můžete tento krok přeskočit.   
- Vytvořte službu private link, která odkazuje na výše uvedený vyvažovač zatížení. V procesu výběru nástroje pro vyrovnávání zatížení zvolte konfiguraci ip adres front-endu, kde chcete přijímat přenosy. Zvolte podsíť pro adresy IP na nat pro službu Private Link Service. Doporučuje se mít v podsíti k dispozici alespoň osm adres IP adres NAT. Zdá se, že veškerý spotřebitelský provoz pochází z tohoto fondu privátních IP adres poskytovateli služeb. Zvolte příslušné vlastnosti/nastavení služby Private Link Service.    

    > [!NOTE]
    > Služba Azure Private Link Service se podporuje jenom ve standardním vyvažovači zatížení. 
    
### <a name="share-your-service"></a>Sdílení služeb

Po vytvoření služby Private Link Azure vygeneruje globálně jedinečný název zástupný název s názvem "alias" na základě názvu, který zadáte pro vaši službu. Identifikátor URI aliasu nebo prostředku služby můžete sdílet se zákazníky offline. Spotřebitelé mohou spustit připojení private link pomocí aliasu nebo identifikátoru URI prostředku.
 
### <a name="manage-your-connection-requests"></a>Správa žádostí o připojení

Poté, co příjemce zahájí připojení, může poskytovatel služeb přijmout nebo odmítnout požadavek na připojení. Všechny požadavky na připojení budou uvedeny pod **privateendpointconnections** vlastnost na private link služby.
 
### <a name="delete-your-service"></a>Smazání služby

Pokud služba Private Link již není používána, můžete ji odstranit. Před odstraněním služby však ujistěte se, že k ní nejsou přidružena žádná připojení soukromého koncového bodu. Můžete odmítnout všechna připojení a odstranit službu.

## <a name="properties"></a>Vlastnosti

Služba Private Link určuje následující vlastnosti: 

|Vlastnost |Vysvětlení  |
|---------|---------|
|Stát zřizování (zřizováníStát)  |Vlastnost jen pro čtení, která uvádí aktuální stav zřizování pro službu Private Link. Použitelné stavy zřizování jsou: "Odstranění; Nezdařilo se; Úspěšná; Aktualizace". Když je stav zřizování "Úspěšné", úspěšně jste zřídit službu Private Link.        |
|Alias (alias)     | Alias je globálně jedinečný řetězec jen pro čtení pro vaši službu. Pomáhá maskovat zákaznická data pro vaši službu a zároveň vytváří snadno sdílet název pro vaši službu. Když vytvoříte službu Private Link, Azure vygeneruje alias pro vaši službu, který můžete sdílet se zákazníky. Vaši zákazníci mohou tento alias použít k vyžádání připojení k vaší službě.          |
|Viditelnost (viditelnost)     | Viditelnost je vlastnost, která řídí nastavení expozice pro službu Private Link. Poskytovatelé služeb se můžou rozhodnout omezit vystavení své službě na předplatná s oprávněními řízení přístupu (RBAC) na základě rolí, omezenou sadou předplatných nebo všemi předplatnými Azure.          |
|Automatické schválení (automatické schválení)    |   Automatické schvalování řídí automatizovaný přístup ke službě Private Link. Odběry zadané v seznamu automatického schvalování jsou schváleny automaticky při vyžádání připojení z privátní koncové body v těchto předplatných.          |
|Front-endová konfigurace IP nástroje pro vyrovnávání zatížení (loadBalancerFrontendIpConfigurations)    |    Služba Private Link je vázána na ip adresu front-endu standardního vykladače zatížení. Veškerý provoz určený pro službu dosáhne frontend s slb. Můžete nakonfigurovat pravidla SLB směrovat tento provoz do příslušných back-endových fondů, kde jsou spuštěny vaše aplikace. Konfigurace ip adres nástroje pro vyrovnávání zatížení se liší od konfigurací IP adres NAT.      |
|Konfigurace IP ADRES NAT (ipKonfigurace)    |    Tato vlastnost odkazuje na konfiguraci IP adres NAT (Network Address Translation) pro službu Private Link. IP adresou NAT lze vybrat z libovolné podsítě ve virtuální síti poskytovatele služeb. Služba Private Link provádí na přenosy soukromého spojení na cílové straně na straně sítě. Tím je zajištěno, že nedojde ke konfliktu IP mezi zdrojovým (na straně spotřebitele) a cílovým (poskytovatelem) adresního prostoru. Na cílové straně (na straně poskytovatele služeb) se IP adresa NAT zobrazí jako zdrojová IP adresa pro všechny pakety přijaté vaší službou a cílovou IP adresou pro všechny pakety odeslané vaší službou.       |
|Připojení privátního koncového bodu (privateEndpointConnections)     |  Tato vlastnost uvádí soukromé koncové body, které se připojují ke službě Private Link. Ke stejné službě Private Link se může připojit více soukromých koncových bodů a poskytovatel služeb může řídit stav jednotlivých privátních koncových bodů.        |
|TCP Proxy V2 (EnableProxyProtocol)     |  Tato vlastnost umožňuje poskytovateli služeb použít proxy server tcp v2 k načtení informací o připojení o spotřebiteli služby. Poskytovatel služeb je zodpovědný za nastavení konfigurace příjemce, aby bylo možné analyzovat proxy protokol v2 záhlaví.        |
|||


### <a name="details"></a>Podrobnosti

- Služba Private Link je přístupná ze schválených privátních koncových bodů ve stejné oblasti. K privátnímu koncovému bodu se dostanete ze stejné virtuální sítě, místně partnerských virtuálních sítí, globálně partnerských virtuálních sítí a v prostorách pomocí privátních připojení VPN nebo ExpressRoute. 
 
- Při vytváření služby Private Link Service je vytvořeno síťové rozhraní pro životní cyklus prostředku. Toto rozhraní není spravovatelné zákazníkem.
 
- Služba Private Link Service musí být nasazena ve stejné oblasti jako virtuální síť a standardní nástroj pro vyrovnávání zatížení.  
 
- K jedné službě private link lze přistupovat z více soukromých koncových bodů, které patří do různých virtuálních sítí, předplatných nebo klientů služby Active Directory. Připojení je navázáno prostřednictvím pracovního postupu připojení. 
 
- Více služeb Private Link lze vytvořit na stejném standardním nástroji pro vyrovnávání zatížení pomocí různých front-endových konfigurací IP adres. Počet služeb Private Link, které můžete vytvořit na standardní vyrovnávání zatížení a na jedno předplatné, je limitován. Podrobnosti najdete v [tématu Limity Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits).
 
- Služba Private Link může mít více než jednu konfiguraci IP adres NAT. Výběr více než jedné konfigurace IP adres NAT může poskytovatelům služeb pomoci škálovat. Poskytovatelé služeb mohou dnes přiřadit až osm IP adres NAT na službu Private Link. S každou ADRESOu IP na nat můžete přiřadit více portů pro připojení TCP a tím horizontálnínavýšení kapacity. Po přidání více ADRES IP NAT do služby Private Link nelze adresy IP adres NAT odstranit. To se provádí, aby se zajistilo, že aktivní připojení nejsou ovlivněny při odstranění ADRES IP NAT.


## <a name="alias"></a>Alias

**Alias** je globálně jedinečný název pro vaši službu. Pomáhá maskovat zákaznická data pro vaši službu a zároveň vytváří snadno sdílet název pro vaši službu. Když vytvoříte službu Private Link, Azure vygeneruje alias pro vaši službu, který můžete sdílet se zákazníky. Vaši zákazníci mohou tento alias použít k vyžádání připojení k vaší službě.

Alias se skládá ze tří částí: *Prefix*. *Identifikátor GUID*. *Přípona*

- Předpona je název služby. Můžete si vybrat vlastní předponu. Po vytvoření aliasu jej nelze změnit, proto vyberte předponu odpovídajícím způsobem.  
- GUID bude poskytovánplatformou. To pomáhá, aby název globálně jedinečný. 
- Příponu připojí Azure: *region*.azure.privatelinkservice 

Kompletní alias: *Prefix*. {GUID}. *region*.azure.privatelinkservice  

## <a name="control-service-exposure"></a>Expozice kontrolní ch odjite

Služba Private Link poskytuje možnosti řízení expozice vaší služby prostřednictvím nastavení Viditelnost. Službu můžete nastavit jako soukromou pro spotřebu z různých virtuálních sítí ,, které vlastníte (pouze oprávnění RBAC), omezit vystavení omezené sadě předplatných, kterým důvěřujete, nebo ji zveřejnit, aby všechna předplatná Azure mohla požadovat připojení na soukromém spojení. Služby. Nastavení viditelnosti rozhoduje o tom, zda se spotřebitel může připojit k vaší službě nebo ne. 

## <a name="control-service-access"></a>Přístup k kontrolní službě

Spotřebitelé, kteří mají expozici (řízenou nastavením viditelnosti) vaší služby Private Link, mohou ve svých virtuálních sítích vytvořit soukromý koncový bod a požádat o připojení ke službě Private Link. Připojení soukromého koncového bodu bude vytvořeno ve stavu Čekající na vyřízení na objektu služby Private Link. Poskytovatel služeb je odpovědný za jednání na základě požadavku na připojení. Připojení můžete schválit, odmítnout nebo připojení odstranit. Pouze připojení, která jsou schválena, mohou odesílat přenosy do služby Private Link.

Akce schválení připojení lze automatizovat pomocí vlastnosti automatického schvalování ve službě Private Link. Automatické schvalování je možnost pro poskytovatele služeb předem schválit sadu předplatných pro automatizovaný přístup k jejich službě. Zákazníci budou muset sdílet svá předplatná offline, aby je měli poskytovatelé služeb přidat do seznamu automatického schvalování. Automatické schvalování je podmnožinou pole viditelnosti. Viditelnost řídí nastavení expozice, zatímco automatické schvalování řídí nastavení schválení pro vaši službu. Pokud zákazník požaduje připojení z předplatného v seznamu automatického schválení, připojení je automaticky schváleno a připojení je navázáno. Poskytovatelé služeb již nemusí žádost ručně schvalovat. Na druhou stranu pokud zákazník požaduje připojení z předplatného v poli viditelnosti a nikoli v poli automatického schvalování, požadavek se dostane k poskytovateli služeb, ale poskytovatel služeb musí ručně schválit připojení.

## <a name="getting-connection-information-using-tcp-proxy-v2"></a>Získání informací o připojení pomocí protokolu TCP Proxy v2

Při použití služby privátního propojení je zdrojovou IP adresou paketů přicházejících z privátního koncového bodu síťová adresa přeložená (NAT) na straně poskytovatele služeb pomocí IP adres NAT přidělené z virtuální sítě poskytovatele. Proto aplikace obdrží přidělenou IP adresu NAT namísto skutečné zdrojové IP adresy spotřebitelů služeb. Pokud vaše aplikace potřebuje skutečnou zdrojovou IP adresu ze strany příjemce, můžete ve službě povolit protokol proxy a načíst informace z hlavičky protokolu proxy. Kromě zdrojové IP adresy, hlavička proxy protokolu také nese LinkID privátníkoncový koncový bod. Kombinace zdrojové IP adresy a LinkID může poskytovatelům služeb pomoci jednoznačně identifikovat jejich spotřebitele. Pro více informací o proxy protokolu, navštivte [zde](https://www.haproxy.org/download/1.8/doc/proxy-protocol.txt). 

Tyto informace jsou kódovány pomocí vlastního vektoru Type-Length-Value (TLV) následujícím způsobem:

Vlastní podrobnosti TLV:

|Pole |Délka (oktety)  |Popis  |
|---------|---------|----------|
|Typ  |1        |PP2_TYPE_AZURE (0xEE)|
|Délka  |2      |Délka hodnoty|
|Hodnota  |1     |PP2_SUBTYPE_AZURE_PRIVATEENDPOINT_LINKID (0x01)|
|  |4        |UINT32 (4 bajty) představující LINKID privátní koncový bod. Kódováno v malém endian formátu.|

 > [!NOTE]
 > Poskytovatel služeb je zodpovědný za to, že služba za standardním vyrovnáváním zatížení je nakonfigurována tak, aby analyzovat hlavičku protokolu proxy podle [specifikace,](https://www.haproxy.org/download/1.8/doc/proxy-protocol.txt) pokud je ve službě privátního propojení povolen proxy protokol. Požadavek se nezdaří, pokud je u služby privátního propojení povoleno nastavení protokolu proxy, ale služba poskytovatele služeb není nakonfigurována tak, aby byla hlavička analyzována. Podobně požadavek se nezdaří, pokud služba poskytovatele služeb očekává hlavičku protokolu proxy, zatímco nastavení není povoleno ve službě privátního propojení. Jakmile je povoleno nastavení protokolu proxy, bude hlavička protokolu proxy také zahrnuta do sond stavu HTTP/TCP z hostitele do virtuálních počítačů back-endu, i když v záhlaví nebudou žádné informace o klientovi. 

## <a name="limitations"></a>Omezení

Následující jsou známá omezení při použití služby Private Link:
- Podporováno pouze u standardního vykladače zatížení 
- Podporuje pouze přenosy IPv4
- Podporuje pouze přenosy TCP

## <a name="next-steps"></a>Další kroky
- [Vytvoření služby privátního propojení pomocí Azure PowerShellu](create-private-link-service-powershell.md)
- [Vytvoření služby privátního propojení pomocí příkazového příkazového příkazu Azure](create-private-link-service-cli.md)
