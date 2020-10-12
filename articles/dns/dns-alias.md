---
title: Přehled záznamů aliasů – Azure DNS
description: V tomto článku se dozvíte o podpoře záznamů aliasů v Microsoft Azure DNS.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 08/09/2019
ms.author: rohink
ms.openlocfilehash: 3378036c4800b274d879743abf937c7860e63ded
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "82926226"
---
# <a name="azure-dns-alias-records-overview"></a>Přehled záznamů aliasů Azure DNS

Záznamy aliasů Azure DNS jsou kvalifikační v sadě záznamů DNS. Můžou na jiné prostředky Azure odkazovat v rámci zóny DNS. Můžete například vytvořit sadu záznamů aliasů, která odkazuje na veřejnou IP adresu Azure místo na záznam A. Vaše sada záznamů aliasu dynamicky odkazuje na instanci služby veřejné IP adresy Azure. V důsledku toho se sada záznamů aliasů během překladu názvů DNS bezproblémově aktualizuje.

Sada záznamů aliasů je podporovaná pro následující typy záznamů v zóně Azure DNS: 

- A
- AAAA
- CNAME

> [!NOTE]
> Pokud máte v úmyslu použít záznam aliasu pro typy záznamů A nebo AAAA tak, aby odkazovaly na [profil Azure Traffic Manager](../traffic-manager/quickstart-create-traffic-manager-profile.md) , je nutné zajistit, aby měl profil Traffic Manager pouze [externí koncové body](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints). Pro externí koncové body v Traffic Manager musíte zadat adresu IPv4 nebo IPv6. V koncových bodech nemůžete použít plně kvalifikované názvy domény (FQDN). V ideálním případě používejte statické IP adresy.

## <a name="capabilities"></a>Možnosti

- **Najeďte na prostředek veřejné IP adresy ze sady záznamů DNS A/AAAA.** Můžete vytvořit sadu záznamů a/AAAA a nastavit ji jako záznam aliasu, aby odkazoval na prostředek veřejné IP adresy (Standard nebo Basic). Sada záznamů DNS se automaticky změní, pokud se změní veřejná IP adresa nebo se odstraní. Dangling záznamy DNS, které odkazují na nesprávné IP adresy, se vyhnete.

   Pro každý prostředek je k dispozici aktuální limit 20 záznamů aliasů.

- **Najeďte na profil Traffic Manager ze sady záznamů DNS A/AAAA/CNAME.** Můžete vytvořit sadu záznamů A/AAAA nebo CNAME a pomocí záznamů aliasů je nasměrovat na profil Traffic Manager. To je zvlášť užitečné v případě, že potřebujete směrovat provoz ve vrcholu zóny, protože se u vrcholu zóny nepodporují tradiční záznamy CNAME. Řekněme například, že váš profil Traffic Manager je myprofile.trafficmanager.net a vaše firemní zóna DNS je contoso.com. Můžete vytvořit sadu záznamů s aliasem typu A/AAAA pro contoso.com (vrchol zóny) a nasměrovat na myprofile.trafficmanager.net.
- **Najeďte na koncový bod Azure Content Delivery Network (CDN)**. To je užitečné při vytváření statických webů s využitím služby Azure Storage a Azure CDN.
- **Najeďte na jinou sadu záznamů DNS ve stejné zóně.** Záznamy aliasů můžou odkazovat na jiné sady záznamů stejného typu. Například sada záznamů CNAME DNS může být aliasem jiné sady záznamů CNAME. Toto uspořádání je užitečné, pokud chcete, aby některé sady záznamů byly aliasy a některé jiné než aliasy.

## <a name="scenarios"></a>Scénáře

Existuje několik běžných scénářů pro záznamy s aliasy.

### <a name="prevent-dangling-dns-records"></a>Zabránit záznamům DNS v dangling

Běžným problémem s tradičními záznamy DNS jsou dangling záznamy. Například záznamy DNS, které nebyly aktualizovány, aby odrážely změny IP adres. K tomuto problému dochází zejména u typů záznamů typu AAAA nebo CNAME.

V případě tradičního záznamu zóny DNS je potřeba, aby v případě, že už neexistuje cílová IP adresa nebo záznam CNAME, byl ručně aktualizovaný záznam DNS, který je k němu přidružený. V některých organizacích nemusí ruční aktualizace probíhat v čase, protože se jedná o problémy se zpracováním nebo oddělení rolí a přidružených úrovní oprávnění. Například role může mít oprávnění k odstranění záznamu CNAME nebo IP adresy, která patří do aplikace. Ale nemá dostatečná oprávnění k aktualizaci záznamu DNS, který odkazuje na tyto cíle. Zpoždění při aktualizaci záznamu DNS může potenciálně způsobit výpadek uživatelů.

Záznamy aliasů zabraňují dangling odkazování tak, že je životní cyklus záznamu DNS na prostředek Azure pevně spojený. Předpokládejme například, že záznam DNS, který je kvalifikován jako záznam aliasu, odkazuje na veřejnou IP adresu nebo profil Traffic Manager. Pokud tyto podkladové prostředky odstraníte, bude se záznam aliasu DNS nacházet v prázdné sadě záznamů. Již neodkazuje na odstraněný prostředek.

### <a name="update-dns-record-set-automatically-when-application-ip-addresses-change"></a>Aktualizace sady záznamů DNS automaticky při změně IP adres aplikace

Tento scénář je podobný předchozímu. Možná dojde k přesunutí aplikace nebo restartování základního virtuálního počítače. Záznam alias se pak automaticky aktualizuje, když se změní IP adresa pro základní prostředek veřejné IP adresy. Tím se vyhnete potenciálním bezpečnostním rizikům při přesměrování uživatelů na jinou aplikaci, která má přiřazenou starou veřejnou IP adresu.

### <a name="host-load-balanced-applications-at-the-zone-apex"></a>Aplikace s vyrovnáváním zatížení hostitele ve vrcholu zóny

Protokol DNS brání přiřazení záznamů CNAME ve vrcholu zóny. Například pokud vaše doména je contoso.com; můžete vytvořit záznamy CNAME pro somelabel.contoso.com; ale nemůžete vytvořit záznam CNAME pro contoso.com sebe sama.
Toto omezení představuje problém pro vlastníky aplikace, kteří mají aplikace s vyrovnáváním zatížení za [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md). Vzhledem k tomu, že použití profilu Traffic Manager vyžaduje vytvoření záznamu CNAME, není možné nasměrovat profil Traffic Manager ze vrcholu zóny.

Tento problém je vyřešen pomocí záznamů aliasů. Na rozdíl od záznamů CNAME se záznamy aliasů vytvoří ve vrcholu zóny a vlastníci aplikace je můžou použít k ukázání záznamu vrcholu zóny na Traffic Manager profil, který má externí koncové body. Vlastníci aplikace odkazují na stejný profil Traffic Manager, který se používá pro jakoukoliv jinou doménu v rámci zóny DNS.

Například contoso.com a webová \. contoso.com mohou odkazovat na stejný profil Traffic Manager. Další informace o použití záznamů aliasů v profilech Azure Traffic Manager najdete v části Další kroky.

### <a name="point-zone-apex-to-azure-cdn-endpoints"></a>Vrchol zóny bodu Azure CDN koncových bodů

Stejně jako profil Traffic Manager můžete také záznamy aliasů použít k ukázání vrcholu zóny DNS na koncové body Azure CDN. To je užitečné při vytváření statických webů s využitím služby Azure Storage a Azure CDN. Pak můžete k webu přejít bez předplatného "www" na název DNS.

Pokud se například váš statický web jmenuje `www.contoso.com` , můžou vaši uživatelé přistupovat k webu pomocí bez nutnosti předřadit `contoso.com` službu WWW do názvu DNS.

Jak bylo popsáno dříve, záznamy CNAME nejsou podporovány ve vrcholu zóny. Proto nemůžete použít záznam CNAME k nasměrování contoso.com na koncový bod CDN. Místo toho můžete použít záznam aliasu k ukázání vrcholu zóny na koncový bod CDN přímo.

> [!NOTE]
> Ukazatel na vrcholy zóny na koncové body CDN pro Azure CDN z Akamai není aktuálně podporován.

## <a name="next-steps"></a>Další kroky

Další informace o záznamech aliasů najdete v následujících článcích:

- [Kurz: Konfigurace záznamu aliasu, který odkazuje na veřejnou IP adresu Azure](tutorial-alias-pip.md)
- [Kurz: Konfigurace záznamu aliasu pro podporu vrcholů názvů domén ve službě Traffic Manager](tutorial-alias-tm.md)
- [Nejčastější dotazy k DNS](https://docs.microsoft.com/azure/dns/dns-faq#alias-records)
