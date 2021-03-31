---
title: Přehled Zóny DNS a záznamů – Azure DNS
description: Přehled podpory hostování zón a záznamů DNS v Microsoft Azure DNS.
author: rohinkoul
ms.assetid: be4580d7-aa1b-4b6b-89a3-0991c0cda897
ms.service: dns
ms.topic: conceptual
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 12/18/2017
ms.author: rohink
ms.openlocfilehash: 41eb15a38c97532951723f12d1ac74c90c838eb1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "94968192"
---
# <a name="overview-of-dns-zones-and-records"></a>Přehled zón a záznamů DNS

Tato stránka popisuje klíčové koncepty domén, zón DNS a záznamů a sad záznamů DNS a způsob jejich podpor v Azure DNS.

## <a name="domain-names"></a>Názvy domén

Domain Name System je hierarchie domén. Hierarchie začíná od kořenové domény, jejíž název je jednoduše "**.**".  Následují domény nejvyšší úrovně, jako jsou „com“, „net“, „org“, „uk“ nebo „jp“.  Následují domény druhé úrovně, jako jsou „org.uk“ nebo „co.jp“. Domény v hierarchii DNS se globálně distribuují a hostují názvové servery DNS po celém světě.

Registrátor názvu domény je organizace, která vám umožní koupit název domény, například `contoso.com` .  Při nákupu názvu domény získáte právo řídit hierarchii DNS pod tímto názvem, například vám umožní nasměrovat název `www.contoso.com` na web vaší společnosti. Registrátor může doménu hostovat na svých názvových serverech vaším jménem nebo vám umožní zadat alternativní názvové servery.

Azure DNS poskytuje globálně distribuovanou infrastrukturu názvového serveru s vysokou dostupností, kterou můžete použít k hostování vaší domény. Díky hostování domén v Azure DNS můžete spravovat záznamy DNS pomocí stejných přihlašovacích údajů, rozhraní API, nástrojů, fakturace a podpory jako jiné služby Azure.

Azure DNS v současné době nepodporuje nákup názvů domén. Pokud si chcete koupit název domény, musíte použít registrátora názvu domény třetí strany. Registrátor obvykle účtuje malý roční poplatek. Domény je pak možné hostovat v Azure DNS pro správu záznamů DNS. Další informace najdete v tématu [Delegování zón DNS s využitím Azure DNS](dns-domain-delegation.md).

## <a name="dns-zones"></a>Zóny DNS

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="dns-records"></a>Záznamy DNS

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

### <a name="time-to-live"></a>TTL (Time to Live)

Hodnota TTL (Time to Live) určuje, jak dlouho jsou jednotlivé záznamy ukládány do mezipaměti klienty, než se znovu dotazuje. V tomto příkladu je hodnota TTL 3600 sekund nebo 1 hodina.

V Azure DNS je hodnota TTL zadána pro sadu záznamů, nikoli pro každý záznam, takže se stejná hodnota používá pro všechny záznamy v dané sadě záznamů.  Můžete zadat libovolnou hodnotu TTL mezi 1 a 2 147 483 647 sekundami.

### <a name="wildcard-records"></a>Zástupné záznamy

Azure DNS podporuje [záznamy se zástupným znakem](https://en.wikipedia.org/wiki/Wildcard_DNS_record). Zástupné záznamy se vrátí v reakci na libovolný dotaz s odpovídajícím názvem (Pokud neexistuje bližší shoda ze sady záznamů bez zástupných znaků). Azure DNS podporuje sady záznamů se zástupnými znaky pro všechny typy záznamů s výjimkou NS a SOA.

Chcete-li vytvořit sadu záznamů se zástupnými znaky, použijte název sady záznamů ' \* '. Alternativně můžete použít také název s ' \* ' jako jeho levý krajní popisek, například ' \* . foo '.

### <a name="caa-records"></a>Záznamy CAA

CAA záznamy umožňují vlastníkům domény určit, které certifikační autority (CA) mají oprávnění k vydávání certifikátů pro svou doménu. Certifikační autority tak můžou v některých případech zabránit chybnému vystavování certifikátů. Záznamy CAA mají tři vlastnosti:
* **Flags**: Toto je celé číslo mezi 0 a 255, které slouží k reprezentaci kritického příznaku, který má zvláštní význam na základě [RFC](https://tools.ietf.org/html/rfc6844#section-3) .
* **Tag**: řetězec ASCII, který může být jeden z následujících:
    * **problém**: použijte tento postup, pokud chcete zadat certifikační autority, které mají povolené vystavování certifikátů (všechny typy).
    * **issuewild**: Toto použijte, pokud chcete zadat certifikační autority, které mají povolené vystavování certifikátů (jenom certifikáty se zástupnými znaky).
    * **iodef**: Zadejte e-mailovou adresu nebo název hostitele, na který můžou certifikační autority upozorňovat na žádosti o neautorizovaný certifikát certifikátu.
* **Value (hodnota**): vybraná hodnota pro konkrétní značku

### <a name="cname-records"></a>Záznamy CNAME

Sady záznamů CNAME nemůžou existovat současně s jinými sadami záznamů se stejným názvem. Například nemůžete vytvořit sadu záznamů CNAME s relativním názvem "www" a záznamem A s relativním názvem "www".

Vzhledem k tomu, že vrchol zóny (název = ' \@ ) vždy obsahuje sady záznamů NS a SOA, které byly vytvořeny při vytvoření zóny, nemůžete na vrcholu zóny vytvořit sadu záznamů CNAME.

Tato omezení se projeví u standardů DNS a nejedná se o omezení Azure DNS.

### <a name="ns-records"></a>Záznamy NS

Záznam NS nastavený na vrcholu zóny (název \@ ) se vytvoří automaticky s každou zónou DNS a automaticky se odstraní při odstranění zóny (nedá se odstranit samostatně).

Tato sada záznamů obsahuje názvy Azure DNS názvových serverů přiřazených k zóně. Do této sady záznamů NS můžete přidat další názvové servery, aby se podporovaly domény spoluhostování s více než jedním poskytovatelem DNS. Můžete také upravit hodnotu TTL a metadata této sady záznamů. Nemůžete ale odebrat ani změnit předem vyplněné Azure DNS názvové servery. 

To platí jenom pro záznam NS nastavený na vrcholu zóny. Jiné sady záznamů NS v zóně (jako používané pro delegování podřízených zón) se dají vytvářet, upravovat a odstraňovat bez omezení.

### <a name="soa-records"></a>Záznamy SOA

Sada záznamů SOA je vytvořena automaticky na vrcholu každé zóny (název = ' \@ ') a automaticky se odstraní při odstranění zóny.  Záznamy SOA nelze vytvářet ani odstraňovat samostatně.

Můžete upravit všechny vlastnosti záznamu SOA s výjimkou vlastnosti Host, která je předem nakonfigurovaná tak, aby odkazovala na název primárního názvového serveru, který poskytuje Azure DNS.

Sériové číslo zóny v záznamu SOA není automaticky aktualizováno při provedení změn v záznamech v zóně. Dá se aktualizovat ručně úpravou záznamu SOA, pokud je to potřeba.

### <a name="spf-records"></a>Záznamy SPF

[!INCLUDE [dns-spf-include](../../includes/dns-spf-include.md)]

### <a name="srv-records"></a>Záznamy SRV

[Záznamy SRV](https://en.wikipedia.org/wiki/SRV_record) jsou používány různými službami k určení umístění serveru. Při zadávání záznamu SRV v Azure DNS:

* *Služba* a *protokol* musí být zadány jako součást názvu sady záznamů s předponami podtržítka.  Například " \_ SIP. \_ tcp.name '.  V případě záznamu ve vrcholu zóny není nutné \@ v názvu záznamu zadat ' ', stačí použít službu a protokol, například ' \_ SIP. \_ protokol TCP.
* *Priorita*, *váha*, *port* a *cíl* jsou zadány jako parametry každého záznamu v sadě záznamů.

### <a name="txt-records"></a>Záznamy TXT

Záznamy TXT slouží k mapování názvů domén na libovolné textové řetězce. Používají se ve více aplikacích, zejména v souvislosti s konfigurací e-mailu, jako je například [zásada pro odesílatele (Sender Policy Framework)](https://en.wikipedia.org/wiki/Sender_Policy_Framework) a [DomainKeys identifikovaný e-mail (DKIM)](https://en.wikipedia.org/wiki/DomainKeys_Identified_Mail).

Standardy DNS umožňují, aby jeden záznam TXT obsahoval více řetězců, z nichž každý může mít délku až 254 znaků. Tam, kde je použito více řetězců, jsou zřetězeny klienty a považovány za jeden řetězec.

Při volání Azure DNS REST API je nutné zadat jednotlivé řetězce TXT samostatně.  Při použití rozhraní Azure Portal, PowerShellu nebo rozhraní příkazového řádku byste měli zadat jeden řetězec na záznam, který je v případě potřeby automaticky rozdělený na segmenty 254 znaků.

Vícenásobné řetězce v záznamu DNS by neměly být zaměňovány pomocí více záznamů TXT v sadě záznamů TXT.  Sada záznamů TXT může obsahovat několik záznamů, *z nichž každá* může obsahovat více řetězců.  Azure DNS podporuje celkovou délku řetězce v každé sadě záznamů TXT (v kombinaci všech záznamů) až 1024 znaků.

## <a name="tags-and-metadata"></a>Značky a metadata

### <a name="tags"></a>Značky

Značky jsou seznam párů název-hodnota a používají Azure Resource Manager k označení prostředků.  Azure Resource Manager používá značky k povolení filtrovaných zobrazení vaší faktury za Azure a také umožňuje nastavit zásady, které vyžadují značky. Další informace o značkách najdete v článku o [použití značek k uspořádání prostředků Azure](../azure-resource-manager/management/tag-resources.md).

Azure DNS podporuje použití značek Azure Resource Manager v prostředcích zóny DNS.  Nepodporuje značky pro sady záznamů DNS, i když jako alternativní metadata se v sadách záznamů DNS podporují, jak je vysvětleno níže.

### <a name="metadata"></a>Metadata

Jako alternativu k značkám sad záznamů Azure DNS podporuje přidávání záznamů do poznámek pomocí metadat.  Podobně jako u značek vám metadata umožňují přidružit páry název-hodnota ke každé sadě záznamů.  To může být užitečné, například pro záznam účelu každé sady záznamů.  Na rozdíl od značek nejde metadata použít k poskytnutí filtrovaného zobrazení vaší faktury Azure a nedá se zadat v zásadách Azure Resource Manager.

## <a name="etags"></a>Značek entit

Předpokládejme, že se dva lidé nebo dva procesy pokusí změnit záznam DNS současně. Kterou jednu službu WINS? A ví, že přepsané změny vytvořil někdo jiný?

Azure DNS používá značky ETag k bezpečnému zpracování souběžných změn stejného prostředku. Značky ETag jsou oddělené od [Azure Resource Manager značek](#tags). K každému prostředku DNS (zóně nebo sadě záznamů) je přidružená značka ETag. Pokaždé, když je načten prostředek, je také načtena značka ETag. Když aktualizujete prostředek, můžete se rozhodnout Převrátit značku ETag, aby Azure DNS mohl ověřit, zda se shoduje značka ETag na serveru. Vzhledem k tomu, že každá aktualizace prostředku má za následek opětovné vygenerování značky ETag, neshoda značek ETag indikuje, že došlo k souběžné změně. Značky ETag lze také použít při vytváření nového prostředku, abyste zajistili, že prostředek ještě neexistuje.

Ve výchozím nastavení používá Azure DNS PowerShell k blokování souběžných změn zón a sad záznamů pomocí značek ETag. Pomocí přepínače Optional *overwrite* lze potlačit kontroly značek ETag. v takovém případě jsou přepsány všechny souběžné změny, ke kterým došlo.

Na úrovni Azure DNS REST API jsou značky ETag zadány pomocí hlaviček protokolu HTTP.  Jejich chování je uvedené v následující tabulce:

| Hlavička | Chování |
| --- | --- |
| Žádné |Úspěšné vložení (žádné kontroly ETag) |
| If-Match \<etag> |Pokaždé, když existuje prostředek a shody ETag, se vloží jenom úspěšně. |
| If-Match * |Podávat jenom úspěšné, pokud prostředek existuje |
| If-None-Match * |Pokud prostředek neexistuje, operace PUT se zdaří. |


## <a name="limits"></a>Omezení

Při použití Azure DNS platí následující výchozí omezení:

[!INCLUDE [dns-limits](../../includes/dns-limits.md)]

## <a name="next-steps"></a>Další kroky

* Pokud chcete začít používat Azure DNS, přečtěte si, jak [vytvořit ZÓNU DNS](./dns-getstarted-portal.md) a [vytvořit záznamy DNS](./dns-getstarted-portal.md).
* Pokud chcete migrovat existující zónu DNS, přečtěte si, jak [importovat a exportovat soubor zóny DNS](dns-import-export.md).