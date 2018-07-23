---
title: Přehled záznamy a zóny DNS – Azure DNS | Dokumentace Microsoftu
description: Přehled podpory pro hostování v Microsoft Azure DNS záznamy a zóny DNS.
services: dns
documentationcenter: na
author: vhorne
manager: jeconnoc
editor: ''
ms.assetid: be4580d7-aa1b-4b6b-89a3-0991c0cda897
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 12/18/2017
ms.author: victorh
ms.openlocfilehash: 7f69d77ac7a6c2a17ef2568f0c7edaef2e1ee3f5
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2018
ms.locfileid: "39174413"
---
# <a name="overview-of-dns-zones-and-records"></a>Přehled záznamů a zón DNS

Tato stránka vysvětluje hlavní Principy domén, zóny DNS a záznamy DNS a sad záznamů a jak jsou v Azure DNS podporované.

## <a name="domain-names"></a>Názvy domén

Domain Name System je hierarchie domén. Hierarchie začíná od kořenové domény, jejíž název je jednoduše „**.**“.  Následují domény nejvyšší úrovně, jako jsou „com“, „net“, „org“, „uk“ nebo „jp“.  Následují domény druhé úrovně, jako jsou „org.uk“ nebo „co.jp“. Domény v hierarchii DNS jsou globálně distribuované a hostované názvovými servery DNS po celém světě.

Registrátora názvu domény je organizace, která umožňuje zakoupit název domény, jako je například "contoso.com".  Nákupu názvu domény vám právo na kontrolu hierarchii DNS pod daným jménem, například umožňuje přímé název "www.contoso.com" na web vaší společnosti. Doménový Registrátor může hostovat domény v jeho vlastní názvové servery za vás nebo vám umožňují určit alternativní názvové servery.

Azure DNS poskytuje infrastrukturu serveru globálně distribuovaná, vysokou dostupnost názvu, který můžete použít k hostování vaší domény. Hostovat v Azure DNS, můžete spravovat svoje DNS záznamy pomocí stejných přihlašovacích údajů, rozhraní API, nástrojů, fakturace a podpory jako u ostatních služeb Azure.

Azure DNS aktuálně nepodporuje nákup názvů domén. Pokud chcete zakoupit název domény, budete muset použít registrátora názvu domén třetí strany. Doménový Registrátor obvykle účtuje malý roční poplatek. Domény pak se dají hostovat v Azure DNS pro správu záznamů DNS. Zobrazit [delegování domény do Azure DNS](dns-domain-delegation.md) podrobnosti.

## <a name="dns-zones"></a>Zóny DNS

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="dns-records"></a>Záznamy DNS

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

### <a name="time-to-live"></a>Time-to-live

Hodnota time to live nebo TTL, určuje, jak dlouho každý záznam se uloží do mezipaměti klienty před právě fokusu. V předchozím příkladu je hodnota TTL 3 600 sekund nebo 1 hodina.

V Azure DNS TTL se zadává pro sadu záznamů, ne pro jednotlivé záznamy, takže stejnou hodnotu se používá pro všechny záznamy v sadě.  Můžete zadat libovolnou hodnotu TTL mezi 1 a 2 147 483 647 sekundami.

### <a name="wildcard-records"></a>Záznamy se zástupným znakem

Azure DNS podporuje [záznamy se zástupným znakem](https://en.wikipedia.org/wiki/Wildcard_DNS_record). Záznamy se zástupným znakem se vrátila v reakci na jakémkoli dotazu s odpovídajícím názvem (pokud nebude nalezena bližší shoda v sadě záznamů bez zástupného znaku). Azure DNS podporuje sady záznamů zástupných znaků u všech typů záznamů s výjimkou NS a SOA.

K vytvoření sady záznamů zástupných znaků, použijte název sady záznamů "\*". Alternativně můžete také použít název s "\*"jako jeho krajní levou, například"\*.foo".

### <a name="caa-records"></a>Záznamy CAA

Záznamy CAA umožňuje vlastníkům domén určit, které certifikačními autoritami (CA) mají oprávnění k vystavování certifikátů pro jejich domény. To umožňuje certifikační autority, aby se zabránilo chybně vystavování certifikátů v některých případech. Záznamy CAA mají tři vlastnosti:
* **Příznaky**: to je celé číslo mezi 0 a 255, používá k reprezentování kritické příznak, který má zvláštní význam za [RFC](https://tools.ietf.org/html/rfc6844#section-3)
* **Značka**: řetězec ASCII, který může být jedna z následujících akcí:
    * **problém**: použít, pokud chcete zadat certifikačních autorit, které jsou povolené pro vydávání certifikátů (všechny typy)
    * **issuewild**: použít, pokud chcete zadat certifikačních autorit, které jsou povolené pro vydávání certifikátů (pouze zástupné certifikáty)
    * **iodef**: Zadejte e-mailovou adresu nebo název hostitele, ke kterému může upozornit certifikačních autorit pro požadavky certifikátů neoprávněným problém
* **Hodnota**: hodnota pro konkrétní značku zvolené

### <a name="cname-records"></a>Záznamy CNAME

Sady záznamů CNAME nemůžou existovat současně s jinými sadami záznamů se stejným názvem. Například nelze vytvořit sadu záznamů CNAME s relativním názvem "www" a záznam s relativním názvem "www", ve stejnou dobu.

Protože vrchol zóny (název = '\@") vždy obsahuje sady záznamů NS a SOA, které byly vytvořené při vytvoření zóny, nelze vytvořit záznam CNAME, nastavte ve vrcholu zóny.

Tato omezení vznikají z norem DNS a nejsou omezení Azure DNS.

### <a name="ns-records"></a>Záznamy NS

Sada ve vrcholu zóny záznamů NS (název "\@") se vytváří automaticky s každou zónou DNS a je automaticky odstraní při odstranění zóny (nelze jej odstranit samostatně).

Tato sada záznamů obsahuje názvy názvových serverů Azure DNS přiřazených k zóně. Můžete přidat další názvové servery pro tento záznam NS nastavit podporuje společné hosting domén s více než jednoho poskytovatele DNS. Můžete také upravit hodnotu TTL a metadata pro tuto sadu záznamů. Ale nejde odebrat ani změnit předem naplněných názvové servery Azure DNS. 

To platí pouze pro záznam NS, nastavte ve vrcholu zóny. Jinými sadami záznamů NS v pásmu (jak se používá k delegování podřízené zóny) můžete vytvořit, upravit a odstraněn bez omezení.

### <a name="soa-records"></a>Záznamy SOA

Sady záznamů SOA se automaticky vytvoří ve vrcholu každé zóny (název = '\@") a automaticky odstraněna při odstranění zóny.  Záznamy SOA nelze vytvořit ani odstranit samostatně.

Všechny vlastnosti záznamu SOA s výjimkou vlastnost "hostitel", která je předem nakonfigurovaný k odkazování na název primární název serveru poskytuje Azure DNS můžete upravit.

### <a name="spf-records"></a>Záznamy SPF

[!INCLUDE [dns-spf-include](../../includes/dns-spf-include.md)]

### <a name="srv-records"></a>Vytvoření záznamů SRV služby

[Záznamy SRV](https://en.wikipedia.org/wiki/SRV_record) různých služeb používají k určení umístění serveru. Při zadávání záznam SRV v Azure DNS:

* *Služby* a *protokol* musí být zadaný jako součást názvu sady záznamů s předponou podtržítka.  Například "\_sip.\_ TCP.Name ".  Pro záznam ve vrcholu zóny, je nutné určit "\@"v názvu záznamu jednoduše použít službu a protokol, například"\_sip.\_ TCP ".
* *Priority*, *váha*, *port*, a *cílové* jsou zadány jako parametry každý záznam v sadě záznamů.

### <a name="txt-records"></a>Záznamy TXT

Záznamy TXT se používají k mapování názvů domén na libovolných textových řetězců. Se používají ve více aplikacích, zejména související s konfigurací e-mailu, jako [odesílatele zásad Framework (SPF)](https://en.wikipedia.org/wiki/Sender_Policy_Framework) a [DomainKeys identifikovat e-mailu (DKIM)](https://en.wikipedia.org/wiki/DomainKeys_Identified_Mail).

Standardy DNS povolit jeden záznam TXT tak, aby obsahovala více řetězců, každý z nich mohou být dlouhé až 254 znaků. Použití více řetězců jsou zřetězeny klienty a považován za jeden řetězec.

Při volání REST API služby Azure DNS, je třeba zadat každého řetězce TXT samostatně.  Při použití na webu Azure portal, Powershellu nebo rozhraní příkazového řádku rozhraní by měl zadáte jeden řetězec na záznam, který v případě potřeby je automaticky rozdělit do segmentů 254 znaků.

Více řetězců v záznamu DNS, neměly by být zaměňovány s více záznamů TXT v sadě záznamů TXT.  Sady záznamů TXT může obsahovat několik záznamů *každý z nich* může obsahovat více řetězců.  Azure DNS podporuje všechny záznamy TXT (napříč všemi záznamy v kombinaci) Celkový počet řetězec délku maximálně 1024 znaků.

## <a name="tags-and-metadata"></a>Značky a metadat

### <a name="tags"></a>Značky

Značky jsou páry název hodnota a slouží k označování prostředků pomocí Azure Resource Manageru.  Azure Resource Manageru pomocí značek filtrované zobrazení vaší faktuře za Azure a také umožňuje nastavit zásady, na kterém jsou požadované značky. Další informace o značkách najdete v tématu [Použití značek k uspořádání prostředků Azure](../azure-resource-manager/resource-group-using-tags.md).

Azure DNS podporuje u prostředků zóny DNS pomocí značky Azure Resource Manageru.  Nepodporuje značky na sady záznamů DNS, i když jako alternativu 'metadata' je podporován v sad záznamů DNS, jak je popsáno níže.

### <a name="metadata"></a>Metadata

Jako alternativu k záznamu nastavit značky Azure DNS podporuje zadávání poznámek do sad záznamů pomocí 'metadat'.  Podobně jako u značky se metadata vám umožní přidružit každá sada záznamů dvojice název hodnota.  To může být užitečné, například na záznam účel každá sada záznamů.  Na rozdíl od značky metadata nelze použít k poskytování filtrované zobrazení vaší faktuře za Azure a nelze zadat v zásadách Azure Resource Manageru.

## <a name="etags"></a>Značek etag

Předpokládejme, že dva lidé, případně dva procesy zkuste upravit záznam DNS ve stejnou dobu. Která z nich wins? A vítězem ví, že jste se přepsat změny, které vytvořil někdo jiný?

Azure DNS pomocí značek etag bezpečně zpracování souběžných změn do stejného prostředku. Jsou oddělené od značek etag [Azure Resource Manageru 'Značky'](#tags). Každý prostředek DNS (zóny nebo sadu záznamů) má značku Etag s ním spojená. Pokaždé, když se načte prostředek, se také načte Etag. Při aktualizaci prostředku, můžete předat zpět značku Etag, Azure DNS můžete ověřit, že značky Etag na server odpovídá. Protože každá aktualizace prostředku výsledkem Etag, znovu se generuje, neshoda značek Etag označuje, že došlo ke změně souběžných. Značek etag můžete také použít při vytváření nového prostředku k zajištění, že prostředek ještě neexistuje.

Ve výchozím nastavení používá Azure DNS Powershellu značek entit k blokování souběžných změn zón a sad záznamů. Volitelný *-přepsat* přepínače lze potlačit kontroly Etag, v takovém případě všechny souběžných se přepíše změny, ke kterým došlo.

Na úrovni rozhraní REST API pro Azure DNS značek etag určeny pomocí hlavičky protokolu HTTP.  Jejich chování je uveden v následující tabulce:

| Záhlaví | Chování |
| --- | --- |
| Žádný |PUT vždy úspěšné (žádné kontroly Etag) |
| If-match <etag> |PUT úspěšná, pouze pokud prostředek existuje a Značka Etag odpovídá |
| If-match * |PUT úspěšná, pouze pokud existuje prostředek |
| If-none-match * |PUT úspěšná, pouze pokud prostředek neexistuje. |


## <a name="limits"></a>Omezení

Následující výchozí omezení platí při používání Azure DNS:

[!INCLUDE [dns-limits](../../includes/dns-limits.md)]

## <a name="next-steps"></a>Další postup

* Pokud chcete začít používat Azure DNS, zjistěte, jak [vytvořit zónu DNS](dns-getstarted-create-dnszone-portal.md) a [vytvořit záznamy DNS](dns-getstarted-create-recordset-portal.md).
* Pokud chcete migrovat existující zónu DNS, zjistěte, jak [import a export souboru zóny DNS](dns-import-export.md).
