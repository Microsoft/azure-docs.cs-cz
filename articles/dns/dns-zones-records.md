---
title: Přehled zón a záznamů DNS – Azure DNS
description: Přehled podpory pro hostování zón DNS a záznamů v Microsoft Azure DNS.
author: rohinkoul
ms.assetid: be4580d7-aa1b-4b6b-89a3-0991c0cda897
ms.service: dns
ms.topic: conceptual
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 12/18/2017
ms.author: rohink
ms.openlocfilehash: 19189af6424960b8e20be686af745b10f2d8578b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265152"
---
# <a name="overview-of-dns-zones-and-records"></a>Přehled dns zón a záznamů

Tato stránka vysvětluje klíčové koncepty domén, zón DNS a záznamů DNS a sad záznamů a jak jsou podporovány ve službě Azure DNS.

## <a name="domain-names"></a>Názvy domén

Domain Name System je hierarchie domén. Hierarchie začíná od 'root' domény, jejíž název je jednoduše :**.**'.  Následují domény nejvyšší úrovně, jako jsou „com“, „net“, „org“, „uk“ nebo „jp“.  Následují domény druhé úrovně, jako jsou „org.uk“ nebo „co.jp“. Domény v hierarchii DNS jsou globálně distribuovány a hostovány názvovými servery DNS po celém světě.

Registrátor názvů domén je organizace, která umožňuje zakoupit název `contoso.com`domény, například .  Zakoupením názvu domény získáte právo řídit hierarchii DNS pod tímto názvem, `www.contoso.com` například můžete název nasměrovat na web vaší společnosti. Registrátor může doménu hostovat vaším jménem ve svých vlastních názvových serverech nebo vám může umožnit zadat alternativní názvové servery.

Azure DNS poskytuje globálně distribuovanou infrastrukturu názvového serveru s vysokou dostupností, kterou můžete použít k hostování domény. Hostováním domén ve službě Azure DNS můžete spravovat záznamy DNS se stejnými přihlašovacími údaji, nástroji, nástroji, fakturací a podporou jako ostatní služby Azure.

Azure DNS aktuálně nepodporuje nákup názvů domén. Chcete-li zakoupit název domény, musíte použít registrátora doménových jmen třetích stran. Registrátor obvykle účtuje malý roční poplatek. Domény pak můžete hostovat v Azure DNS pro správu záznamů DNS. Další informace najdete v tématu [Delegování zón DNS s využitím Azure DNS](dns-domain-delegation.md).

## <a name="dns-zones"></a>Zóny DNS

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="dns-records"></a>Záznamy DNS

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

### <a name="time-to-live"></a>Doba života

Doba live, nebo TTL, určuje, jak dlouho každý záznam je ukládán klienty před opětovným dotazem. Ve výše uvedeném příkladu ttl je 3600 sekund nebo 1 hodina.

Ve službě Azure DNS je Hodnota TTL určena pro sadu záznamů, nikoli pro každý záznam, takže stejná hodnota se používá pro všechny záznamy v rámci této sady záznamů.  Můžete zadat libovolnou hodnotu TTL mezi 1 a 2,147,483,647 sekund.

### <a name="wildcard-records"></a>Záznamy se zástupnými symboly

Azure DNS podporuje [záznamy se zástupným znakem](https://en.wikipedia.org/wiki/Wildcard_DNS_record). Záznamy se zástupnými kódy jsou vráceny jako odpověď na libovolný dotaz s odpovídajícím názvem (pokud neexistuje bližší shoda ze sady záznamů bez zástupných symbolů). Azure DNS podporuje sady záznamů se zástupnými symboly pro všechny typy záznamů s výjimkou NS a SOA.

Chcete-li vytvořit sadu záznamů se zástupnými\*symboly, použijte název sady záznamů ' . Případně můžete také použít název s\*názvem " " jako štítkem\*nejvíce vlevo, například '.foo'.

### <a name="caa-records"></a>Záznamy CAA

Záznamy úřadu CAA umožňují vlastníkům domény určit, které certifikační autority (CA) jsou oprávněny vydávat certifikáty pro svou doménu. To certifikačním autoritám umožňuje za určitých okolností zabránit chybnému vydávání certifikátů. Záznamy CAA mají tři vlastnosti:
* **Příznaky**: Toto je celé číslo mezi 0 a 255, slouží k reprezentaci kritický příznak, který má zvláštní význam na [RFC](https://tools.ietf.org/html/rfc6844#section-3)
* **Tag**: řetězec ASCII, který může být jedním z následujících:
    * **problém**: Použijte tuto možnost, pokud chcete zadat certifikáty, které mohou vydávat certifikáty (všechny typy)
    * **issuewild**: Použijte tuto možnost, pokud chcete zadat certifikáty, které mohou vydávat certifikáty (pouze zástupné certifikáty)
    * **iodef**: zadejte e-mailovou adresu nebo název hostitele, na které mohou certifikáty upozorňovat na neoprávněné žádosti o vydání certifikátu
* **Hodnota**: hodnota pro vybranou značku

### <a name="cname-records"></a>Záznamy CNAME

Sady záznamů CNAME nemůžou existovat současně s jinými sadami záznamů se stejným názvem. Například nelze vytvořit sadu záznamů CNAME s relativním názvem www a záznam A s relativním názvem www současně.

Vzhledem k tomu, že\@vrchol zóny (název = ' ' ) vždy obsahuje sady záznamů NS a SOA, které byly vytvořeny při vytvoření zóny, nelze vytvořit záznam CNAME nastavený na vrcholu zóny.

Tato omezení vyplývají ze standardů DNS a nejsou omezení Azure DNS.

### <a name="ns-records"></a>NS záznamy

Záznam NS nastavený na vrcholu zóny\@(název " ") je vytvořen automaticky s každou zónou DNS a při odstranění zóny se automaticky odstraní (nelze ji odstranit samostatně).

Tato sada záznamů obsahuje názvy názvových serverů Azure DNS přiřazených k zóně. Do této sady záznamů NS můžete přidat další názvové servery, které podporují co-hostingové domény s více než jedním poskytovatelem DNS. Můžete také upravit TTL a metadata pro tuto sadu záznamů. Předem vyplněné názvové servery Azure DNS však nelze odebrat ani upravit. 

To platí pouze pro záznam NS nastavený na vrcholu zóny. Jiné sady záznamů NS ve vaší zóně (jak se používá k delegování podřízených zón) lze vytvořit, upravit a odstranit bez omezení.

### <a name="soa-records"></a>SOA záznamy

Sada záznamů SOA je vytvořena automaticky na vrcholu každé\@zóny (název = ') a při odstranění zóny se automaticky odstraní.  Záznamy SOA nelze vytvořit ani odstranit samostatně.

Můžete upravit všechny vlastnosti záznamu SOA s výjimkou vlastnosti "host", která je předem nakonfigurována tak, aby odkazovala na název primárního názvového serveru poskytovaného službou Azure DNS.

Sériové číslo zóny v záznamu SOA se při provádění změn záznamů v zóně automaticky neaktualizuje. V případě potřeby jej lze aktualizovat ručně úpravou záznamu SOA.

### <a name="spf-records"></a>Záznamy SPF

[!INCLUDE [dns-spf-include](../../includes/dns-spf-include.md)]

### <a name="srv-records"></a>Záznamy SRV

[Záznamy SRV](https://en.wikipedia.org/wiki/SRV_record) jsou používány různými službami k určení umístění serveru. Při zadávání záznamu SRV ve službě Azure DNS:

* *Služba* a *protokol* musí být zadány jako součást názvu sady záznamů s předponou podtržítkem.  Například ,\_doušek. \_tcp.name'.  Pro záznam na vrcholu zóny není třeba v\@názvu záznamu specifikovat " " , jednoduše použijte\_službu a protokol, například ' sip. \_tcp".
* *Priorita*, *hmotnost*, *port*a *cíl* jsou určeny jako parametry každého záznamu v sadě záznamů.

### <a name="txt-records"></a>TXT záznamy

Záznamy TXT se používají k mapování názvů domén na libovolné textové řetězce. Používají se ve více aplikacích, zejména v souvislosti s konfigurací e-mailu, jako je například [sender policy framework (SPF)](https://en.wikipedia.org/wiki/Sender_Policy_Framework) a [DomainKeys Identified Mail (DKIM)](https://en.wikipedia.org/wiki/DomainKeys_Identified_Mail).

Standardy DNS umožňují, aby jeden záznam TXT obsahoval více řetězců, z nichž každý může mít délku až 254 znaků. Pokud se používá více řetězců, jsou zřetězené klienty a považovány za jeden řetězec.

Při volání rozhraní AZURE DNS REST API je třeba zadat každý řetězec TXT samostatně.  Při použití portálu Azure, rozhraní PowerShell nebo ROZHRANÍ PŘÍKAZOVÉHO ŘÁDKU byste měli zadat jeden řetězec na záznam, který je v případě potřeby automaticky rozdělen do segmentů s 254 znaky.

Více řetězců v záznamu DNS by nemělo být zaměňováno s více záznamy TXT v sadě záznamů TXT.  Sada záznamů TXT může obsahovat více záznamů, *z nichž každý* může obsahovat více řetězců.  Azure DNS podporuje celkovou délku řetězce až 1024 znaků v každé sadě záznamů TXT (napříč všemi záznamy dohromady).

## <a name="tags-and-metadata"></a>Značky a metadata

### <a name="tags"></a>Značky

Značky jsou seznam párů název-hodnota a používají Azure Resource Manager k označení prostředků.  Azure Resource Manager používá značky k povolení filtrovaných zobrazení vaší účtu Azure a také umožňuje nastavit zásady, na které značky jsou požadovány. Další informace o značkách najdete v článku o [použití značek k uspořádání prostředků Azure](../azure-resource-manager/management/tag-resources.md).

Azure DNS podporuje používání značek Azure Resource Manager u prostředků zóny DNS.  Nepodporuje značky na sadách záznamů DNS, i když jako alternativní 'metadata' je podporován na dns sady záznamů, jak je vysvětleno níže.

### <a name="metadata"></a>Metadata

Jako alternativu k značkám sady záznamů azure DNS podporuje anotace sad záznamů pomocí "metadat".  Podobně jako značky, metadata umožňuje přidružit dvojice název-hodnota s každou sadu záznamů.  To může být užitečné, například pro záznam účelu každé sady záznamů.  Na rozdíl od značek metadata nelze použít k poskytování filtrované zobrazení vaší azure bill a nelze zadat v zásadách Azure Resource Manager.

## <a name="etags"></a>Etags

Předpokládejme, že dva osoby nebo dva procesy se pokusí upravit záznam DNS současně. Který z nich vyhraje? A ví vítěz, že přepsal změny vytvořené někým jiným?

Azure DNS používá etags ke zpracování souběžných změn na stejný prostředek bezpečně. Etags jsou oddělené od [azure resource manager 'značky'](#tags). Ke každému prostředku DNS (zóně nebo sadě záznamů) je přidružen etag. Při každém načtení prostředku je načíst jeho Etag je také načten. Při aktualizaci prostředku, můžete se rozhodnout předat zpět Etag, takže Azure DNS můžete ověřit, že Etag na serveru odpovídá. Vzhledem k tomu, že každá aktualizace prostředku má za následek regenerovánou etag, neshoda etag označuje souběžné změny došlo. Etags lze také použít při vytváření nového prostředku k zajištění, že prostředek již neexistuje.

Ve výchozím nastavení azure DNS PowerShell používá etags blokovat souběžné změny zón a sad záznamů. Přepínač *volitelného přepsání* lze použít k potlačení kontrol etag, v takovém případě jsou přepsány všechny souběžné změny, ke kterým došlo.

Na úrovni rozhraní AZURE DNS REST API etags jsou určeny pomocí hlavičky HTTP.  Jejich chování je uvedeno v následující tabulce:

| Hlavička | Chování |
| --- | --- |
| Žádný |PUT vždy uspěje (žádné kontroly Etag) |
| > \<elektronického označení etag |PUT úspěšné pouze v případě, že prostředek existuje a Etag zápasy |
| Pokud se shoduje * |PUT úspěšné pouze v případě, že zdroj existuje |
| Pokud-žádná-shoda * |PUT pouze úspěšné, pokud zdroj neexistuje |


## <a name="limits"></a>Omezení

Při použití Azure DNS platí následující výchozí limity:

[!INCLUDE [dns-limits](../../includes/dns-limits.md)]

## <a name="next-steps"></a>Další kroky

* Pokud chcete začít používat Azure DNS, přečtěte si, jak [vytvořit zónu DNS](dns-getstarted-create-dnszone-portal.md) a [vytvořit záznamy DNS](dns-getstarted-create-recordset-portal.md).
* Chcete-li migrovat existující zónu DNS, přečtěte si, jak [importovat a exportovat soubor zóny DNS](dns-import-export.md).
