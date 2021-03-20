---
title: Co je služba privátního propojení Azure?
description: Přečtěte si o službě Azure Private Link.
services: private-link
author: sumeetmittal
ms.service: private-link
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: sumi
ms.openlocfilehash: 7983a80da8a5ca9d900e44515b5e078cc9d70d79
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98684182"
---
# <a name="what-is-azure-private-link-service"></a>Co je služba privátního propojení Azure?

Služba privátního propojení Azure je odkazem na vlastní službu, která využívá privátní propojení Azure. Služba, která je spuštěná za [Azure Standard Load Balancer](../load-balancer/load-balancer-overview.md) , se dá povolit pro přístup k privátním odkazům, aby k nim uživatelé mohli přistupovat soukromě z vlastních virtuální sítě. Vaši zákazníci můžou ve své virtuální síti vytvořit privátní koncový bod a namapovat ho k této službě. Tento článek vysvětluje koncepty týkající se strany poskytovatele služeb. 

:::image type="content" source="./media/private-link-service-overview/consumer-provider-endpoint.png" alt-text="Pracovní postup služby privátního propojení" border="true":::

*Obrázek: služba privátního propojení Azure.*

## <a name="workflow"></a>Pracovní postup

![Pracovní postup služby privátního propojení](media/private-link-service-overview/private-link-service-workflow.png)


*Obrázek: pracovní postup služby privátního propojení Azure.*

### <a name="create-your-private-link-service"></a>Vytvoření služby privátního propojení

- Nakonfigurujte svou aplikaci tak, aby běžela za standardním nástrojem pro vyrovnávání zatížení ve vaší virtuální síti. Pokud již máte aplikaci nakonfigurovanou za standardním nástrojem pro vyrovnávání zatížení, můžete tento krok přeskočit.   
- Vytvořte službu privátního propojení odkazující na výše uvedený nástroj pro vyrovnávání zatížení. V procesu výběru nástroje pro vyrovnávání zatížení vyberte konfiguraci protokolu IP front-endu, kde chcete provoz přijímat. Vyberte podsíť pro IP adresy NAT pro službu privátního propojení. Doporučuje se mít v podsíti k dispozici aspoň osm IP adres NAT. U všech uživatelských přenosů se zdá, že pochází z fondu privátních IP adres k poskytovateli služeb. Vyberte odpovídající vlastnosti/nastavení služby privátního propojení.    

    > [!NOTE]
    > Služba privátního propojení Azure je podporovaná jenom na Standard Load Balancer. 
    
### <a name="share-your-service"></a>Sdílení služby

Po vytvoření služby privátního propojení Azure vygeneruje globálně jedinečný pojmenovaný moniker nazvaný "alias" na základě názvu, který zadáte pro vaši službu. Můžete sdílet alias nebo identifikátor URI prostředku vaší služby vašim zákazníkům v režimu offline. Příjemci mohou spustit připojení pomocí privátního propojení pomocí aliasu nebo identifikátoru URI prostředku.
 
### <a name="manage-your-connection-requests"></a>Správa žádostí o připojení

Po zahájení připojení příjemce může poskytovatel služeb přijmout nebo odmítnout žádost o připojení. Všechny požadavky na připojení budou uvedeny v části vlastnost **privateendpointconnections** ve službě privátního propojení.
 
### <a name="delete-your-service"></a>Odstranění služby

Pokud se služba privátního propojení již nepoužívá, můžete ji odstranit. Před odstraněním služby se ale ujistěte, že k ní nejsou přidružená žádná připojení privátních koncových bodů. Můžete odmítat všechna připojení a službu odstranit.

## <a name="properties"></a>Vlastnosti

Služba privátního propojení určuje následující vlastnosti: 

|Vlastnost |Vysvětlení  |
|---------|---------|
|Stav zřizování (provisioningState)  |Vlastnost jen pro čtení, která uvádí aktuální stav zřizování pro službu privátního propojení. Příslušné stavy zřizování jsou: "odstraňování; Nepovedlo se Úspěchu Aktualizuje se. Po úspěšném stavu zřizování jste úspěšně zřídili službu privátních odkazů.        |
|Alias (alias)     | Alias je globálně jedinečný řetězec jen pro čtení vaší služby. Pomůže vám to maskovat zákaznická data pro vaši službu a zároveň vytvořit snadnou sdílenou složku pro vaši službu. Když vytvoříte službu privátního propojení, Azure vytvoří alias pro vaši službu, kterou můžete sdílet se svými zákazníky. Vaši zákazníci můžou pomocí tohoto aliasu požádat o připojení ke službě.          |
|Viditelnost (viditelnost)     | Viditelnost je vlastnost, která řídí nastavení expozice pro službu privátního propojení. Poskytovatelé služeb se můžou rozhodnout omezit expozici své služby na předplatná pomocí oprávnění řízení přístupu na základě role (Azure RBAC), omezené sady předplatných nebo všech předplatných Azure.          |
|Automatické schvalování (automatické schválení)    |   Automatické schválení řídí automatizovaný přístup ke službě privátního propojení. Odběry zadané v seznamu automatického schvalování se schvalují automaticky, když se v těchto předplatných požaduje připojení z privátních koncových bodů.          |
|Load Balancer konfigurace IP adresy front-endu (loadBalancerFrontendIpConfigurations)    |    Služba Private Link je vázaná na IP adresu front-endu Standard Load Balancer. Veškerý provoz určený pro službu se dostane do front-endu služby SLB. Můžete nakonfigurovat pravidla služby SLB pro přesměrování tohoto provozu do příslušných back-end fondů, kde běží vaše aplikace. Konfigurace IP adresy front-endu pro vyrovnávání zatížení se liší od konfigurace IP adres NAT.      |
|Konfigurace protokolu IP NAT (IPConfiguration)    |    Tato vlastnost odkazuje na konfiguraci protokolu IP NAT (překladu síťových adres) pro službu privátního propojení. IP adresu NAT si můžete vybrat z libovolné podsítě ve virtuální síti poskytovatele služeb. Služba privátního propojení provádí NAT cílového umístění na straně cíle na přenosy privátního propojení. Tím se zajistí, že nedojde ke konfliktu IP adres mezi zdrojovým a cílovým adresním prostorem (poskytovatelem služeb). Na straně cíle (na straně poskytovatele služeb) se IP adresa NAT zobrazí jako zdrojová IP adresa pro všechny pakety přijaté službou a cílovou IP adresou pro všechny pakety odesílané vaší službou.       |
|Připojení privátního koncového bodu (privateEndpointConnections)     |  Tato vlastnost zobrazí seznam privátních koncových bodů připojujících se ke službě privátního propojení. Ke stejné službě privátního propojení se může připojit několik privátních koncových bodů a poskytovatel služeb může řídit stav jednotlivých privátních koncových bodů.        |
|Protokol proxy serveru TCP v2 (EnableProxyProtocol)     |  Tato vlastnost umožňuje poskytovateli služeb použít k načtení informací o připojení pro příjemce služby TCP proxy v2. Poskytovatel služeb zodpovídá za nastavení konfigurace přijímače, aby mohl analyzovat hlavičku proxy protokolu v2.        |
|||


### <a name="details"></a>Podrobnosti

- Služba privátního propojení je dostupná ze schválených privátních koncových bodů v libovolné veřejné oblasti. K privátnímu koncovému bodu se dá získat přístup ze stejné virtuální sítě, s virtuální sítěem partnerským vztahem, globálně partnerským virtuální sítě a místně pomocí privátních připojení VPN nebo ExpressRoute. 
 
- Při vytváření služby privátního propojení se pro životní cyklus prostředku vytvoří síťové rozhraní. Toto rozhraní nejde spravovat od zákazníka.
 
- Služba privátního propojení musí být nasazená ve stejné oblasti jako virtuální síť a Standard Load Balancer.  
 
- Jedna služba privátního propojení je dostupná z více privátních koncových bodů patřících různým virtuální sítě, předplatným a/nebo klientům služby Active Directory. Připojení je vytvořeno prostřednictvím pracovního postupu připojení. 
 
- Ve stejném Standard Load Balancer lze pomocí různých konfigurací front-end IP adres vytvořit více služeb privátních propojení. Existují omezení počtu služeb privátních odkazů, které můžete vytvořit pro jednotlivé Standard Load Balancer a pro každé předplatné. Podrobnosti najdete v tématu [omezení Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).
 
- Služba privátního propojení může mít na sebe propojenou více konfigurací IP adres NAT. Výběr více než jedné konfigurace protokolu IP NAT může pomáhat poskytovatelům služeb škálovat. V dnešní době můžou poskytovatelé služeb přiřadit až osm IP adres NAT na jednu službu privátního propojení. U každé IP adresy překladu adres (NAT) můžete přiřadit více portů pro připojení TCP a tak škálovat. Po přidání více IP adres NAT do služby privátního propojení nemůžete IP adresy NAT odstranit. K tomu je potřeba zajistit, aby při odstraňování IP adres překladu adres (NAT) nedošlo k ovlivnění aktivních připojení.


## <a name="alias"></a>Alias

**Alias** je globálně jedinečný název vaší služby. Pomůže vám to maskovat zákaznická data pro vaši službu a zároveň vytvořit snadnou sdílenou složku pro vaši službu. Když vytvoříte službu privátního propojení, Azure vytvoří alias pro vaši službu, který můžete sdílet se svými zákazníky. Vaši zákazníci můžou pomocí tohoto aliasu požádat o připojení ke službě.

Alias se skládá ze tří částí: *prefix*. *Identifikátor GUID* *Přípona*

- Předpona je název služby. Můžete si vybrat vlastní předponu. Po vytvoření aliasu ho už nemůžete změnit, takže ho můžete vhodně vybrat.  
- Identifikátor GUID bude poskytován platformou. To pomáhá nastavit globálně jedinečný název. 
- Přípona je připojená pomocí Azure: *region*. Azure. privatelinkservice. 

Úplný alias: *prefix* {GUID}. *oblast*. Azure. privatelinkservice  

## <a name="control-service-exposure"></a>Řízení expozice služby

Služba privátního propojení vám nabízí možnosti řízení expozice vaší služby prostřednictvím nastavení viditelnosti. Službu můžete nastavit jako soukromou pro spotřebu z různých virtuální sítě, které vlastníte (jenom oprávnění Azure pro řízení přístupu), omezit vystavení omezené sadě předplatných, kterým důvěřujete, nebo zveřejnit jako veřejnou, aby všechna předplatná Azure mohla vyžadovat připojení ve službě privátních odkazů. Nastavení viditelnosti rozhodují, jestli se příjemce může ke službě připojit, nebo ne. 

## <a name="control-service-access"></a>Řízení přístupu ke službě

Příjemci s expozicí (řízenou nastavením viditelnosti) do vaší služby privátního propojení můžou v virtuální sítě vytvořit privátní koncový bod a požádat o připojení ke službě privátních odkazů. Připojení privátního koncového bodu se vytvoří ve stavu "čeká" v objektu služby privátního propojení. Poskytovatel služeb zodpovídá za to, že funguje na žádosti o připojení. Můžete buď schválit připojení, odmítat připojení, nebo připojení odstranit. Pouze připojení, která jsou schválena, mohou odesílat data do služby privátního propojení.

Akci schválení připojení lze automatizovat pomocí vlastnosti automatického schvalování ve službě privátního propojení. Automatické schválení je schopnost, aby poskytovatelé služeb předem schválili sadu předplatných pro automatický přístup ke své službě. Aby poskytovatelé služeb mohli přidat do seznamu automatického schválení, budou muset uživatelé sdílet své odběry do offline režimu. Automatické schválení je podmnožinou pole viditelnosti. Viditelnost ovládá nastavení expozice, zatímco automatické schvalování kontroluje nastavení schválení pro vaši službu. Pokud zákazník požádá o připojení z předplatného v seznamu automatického schválení, připojení se automaticky schválí a naváže se připojení. Poskytovatelé služeb nemusí žádost už ručně schválit. Na druhé straně, pokud zákazník požádá o připojení z předplatného v poli viditelnosti, nikoli v poli automatického schválení, požadavek se dostane k poskytovateli služeb, ale poskytovatel služeb musí připojení ručně schválit.

## <a name="getting-connection-information-using-tcp-proxy-v2"></a>Získání informací o připojení pomocí proxy serveru TCP v2

Při použití služby Private Link je zdrojová IP adresa paketů přicházejících z privátního koncového bodu přeložena (NAT) na straně poskytovatele služeb pomocí IP adresy NAT přidělené z virtuální sítě poskytovatele. Aplikace proto obdrží přidělenou IP adresu překladu adres (NAT) místo skutečné zdrojové IP adresy příjemců služby. Pokud vaše aplikace potřebuje skutečnou zdrojovou IP adresu ze strany spotřebitele, můžete ve službě povolit protokol proxy a načíst informace z hlavičky protokolu proxy serveru. Kromě zdrojové IP adresy je v záhlaví protokolu proxy také identifikátor LinkID privátního koncového bodu. Kombinace zdrojové IP adresy a identifikátor LinkID může pomáhat poskytovatelům služeb jednoznačně identifikovat jejich příjemce. Další informace o protokolu proxy najdete [tady](https://www.haproxy.org/download/1.8/doc/proxy-protocol.txt). 

Tyto informace jsou zakódovány pomocí vlastního vektoru typu s délkou Value (TLV) následujícím způsobem:

Podrobnosti vlastního TLV:

|Pole |Délka (oktety)  |Popis  |
|---------|---------|----------|
|Typ  |1        |PP2_TYPE_AZURE (0xEE)|
|Délka  |2      |Délka hodnoty|
|Hodnota  |1     |PP2_SUBTYPE_AZURE_PRIVATEENDPOINT_LINKID (0x01)|
|  |4        |UINT32 (4 bajty) představující LINKID privátního koncového bodu. Kódováno ve formátu Little endian.|

 > [!NOTE]
 > Poskytovatel služeb zodpovídá za zajištění, že služba za standardním nástrojem pro vyrovnávání zatížení je nakonfigurovaná tak, aby analyzovala hlavičku protokolu proxy podle [specifikace](https://www.haproxy.org/download/1.8/doc/proxy-protocol.txt) , když je na službě privátního propojení povolený protokol proxy. V případě, že je ve službě privátního propojení povolený protokol proxy a služba poskytovatele služeb není nakonfigurovaná k analýze hlavičky, požadavek selže. Obdobně se požadavek nezdaří, pokud služba poskytovatele služeb očekává hlavičku proxy protokolu, ale toto nastavení není povolené ve službě privátních odkazů. Po povolení nastavení protokolu proxy bude hlavička protokolu serveru proxy také obsažena v testu stavu HTTP/TCP z hostitele do virtuálních počítačů back-end, a to i v případě, že v hlavičce nebudou žádné informace o klientovi. 

## <a name="limitations"></a>Omezení

Následující jsou známá omezení při použití služby privátního propojení:
- Podporováno pouze v Standard Load Balancer 
- Podporuje jenom přenosy IPv4.
- Podporuje jenom přenosy TCP a UDP.

## <a name="next-steps"></a>Další kroky
- [Vytvoření služby privátního propojení pomocí Azure PowerShell](create-private-link-service-powershell.md)
- [Vytvoření služby privátního propojení pomocí Azure CLI](create-private-link-service-cli.md)
