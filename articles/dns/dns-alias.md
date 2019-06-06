---
title: Azure DNS alias Přehled záznamů
description: Přehled podpory pro záznamů aliasů v Microsoft Azure DNS.
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 5/13/2019
ms.author: victorh
ms.openlocfilehash: b34baa6f1ba91935fc6307dbb1617393786043b9
ms.sourcegitcommit: 18a0d58358ec860c87961a45d10403079113164d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2019
ms.locfileid: "66692848"
---
# <a name="azure-dns-alias-records-overview"></a>Azure DNS alias Přehled záznamů

Azure DNS záznamů aliasů jsou požadavky na sady záznamů DNS. Mohou odkazovat další prostředky Azure z v rámci zóny DNS. Můžete například vytvořit skupinu záznam aliasu, který odkazuje veřejnou IP adresou Azure místo záznam. Alias záznamu sady bodů Azure veřejnou IP adresu instance služby dynamicky. V důsledku toho sadu záznamů alias bezproblémově se aktualizuje sám během překlad DNS.

Sady záznamů alias je podporována pro následující typy záznamů v zóně Azure DNS: 

- A
- AAAA
- CNAME

> [!NOTE]
> Pokud máte v úmyslu použít záznamu o aliasu pro typy záznamů A nebo AAAA tak, aby odkazovala na [profilu Azure Traffic Manageru](../traffic-manager/quickstart-create-traffic-manager-profile.md) Ujistěte se, že profil služby Traffic Manager má pouze [externí koncové body](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints). Musíte zadat adresu IPv4 nebo IPv6 pro externí koncové body Traffic Manageru. V koncových bodech nelze použít plně kvalifikované názvy domén (FQDN). V ideálním případě používání statických IP adres.

## <a name="capabilities"></a>Možnosti

- **Odkazovat na prostředek veřejné IP adresy ze serveru DNS A/AAAA sadu záznamů.** Můžete vytvořit sadu záznamů A/AAAA a nastavte ji alias záznamu sady tak, aby odkazoval na prostředek veřejné IP adresy. Sady záznamů DNS je automaticky, pokud veřejná IP adresa se změní nebo je odstranit. Nepropojená DNS záznamy, které odkazují na nesprávné IP adresy se jim vyhnout.

- **Přejděte na profil Traffic Manageru ze sady záznamů DNS A/AAAA/CNAME.** Můžete vytvořit A/AAAA nebo záznam CNAME nastavit a nasměrovat ho na profil Traffic Manageru pomocí záznamů aliasů. Je užitečné zejména při je potřeba směrovat provoz na vrcholu zóny, protože tradiční záznamy CNAME se nepodporují pro vrcholu zóny. Řekněme například, je váš profil Traffic Manageru myprofile.trafficmanager.net a zónu DNS firmy je contoso.com. Můžete vytvořit alias záznamu sadu typů A/AAAA pro doménu contoso.com (vrcholu zóny) a přejděte na myprofile.trafficmanager.net.
- **Přejděte na koncový bod Azure Content Delivery Network (CDN)** . To je užitečné při vytváření statických webů pomocí služby Azure storage a Azure CDN.
- **Přejděte na jinou sadu záznamů DNS v rámci stejné zóny.** Záznamy aliasů můžou odkazovat na jiné sady záznamů stejného typu. Sady záznamů DNS CNAME může být například alias pro jinou sadu záznamů CNAME. Toto uspořádání je užitečné, pokud chcete, aby některé sady záznamů bude aliasy a některé jiné aliasy.

## <a name="scenarios"></a>Scénáře

Existuje několik běžných scénářů pro záznamů aliasů.

### <a name="prevent-dangling-dns-records"></a>Zabránit nepropojená záznamů DNS

Běžný problém u tradiční záznamů DNS je nepropojená záznamy. Například záznamy DNS, které nebyly aktualizovány tak, aby odrážely změny IP adresy. K danému problému dojde, zejména s A/AAAA, CNAME nebo typy záznamů.

Tradiční zaznamenané zóny DNS Pokud cílová IP adresa nebo CNAME už existuje, záznam DNS s ním spojená je potřeba aktualizovat ručně. V některých organizacích nemusí dojít ruční aktualizace v čase kvůli problémům s procesu nebo kvůli oddělení rolí a úrovní oprávnění přidružené. Například role může mít oprávnění k odstranění CNAME nebo IP adresu, které patří k aplikaci. Ale nemá dostatečná oprávnění k aktualizaci záznamu DNS, který odkazuje na tyto cíle. Zpoždění při aktualizaci záznamu DNS může potenciálně způsobit výpadku pro uživatele.

Záznamů aliasů předcházet nepropojená odkazy pevné párování životní cyklus záznam DNS k prostředku Azure. Představte si třeba záznam DNS, který je kvalifikován jako záznamu o aliasu tak, aby odkazoval na veřejné IP adresy nebo profil služby Traffic Manager. Pokud těchto základních prostředků se odstraní, odebere se záznam aliasu DNS ve stejnou dobu.

### <a name="update-dns-record-set-automatically-when-application-ip-addresses-change"></a>Při změně IP adres aplikace automaticky aktualizovat DNS record-set

Tento scénář je podobný předchozímu. Možná se přesune aplikace nebo základní virtuální počítač se restartuje. Záznam aliasu pak se automaticky aktualizuje při změně IP adresu pro základní veřejný IP prostředek. Tím se vyhnete potenciální bezpečnostní rizika směruje uživatele do jiné aplikace, které se přiřadila staré veřejnou IP adresu.

### <a name="host-load-balanced-applications-at-the-zone-apex"></a>Hostování aplikací s vyrovnáváním zatížení ve vrcholu zóny

Protokol DNS brání přiřazení CNAME záznamů ve vrcholu zóny. Například pokud vaše doména je contoso.com; můžete vytvořit záznamy CNAME pro somelable.contoso.com; ale nelze vytvořit záznam CNAME pro doménu contoso.com, samotného.
Toto omezení představuje problém pro počet vlastníků aplikace, kteří mají aplikace s vyrovnáváním zatížení za [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md). Protože pomocí profilu Traffic Manageru se vyžaduje vytvoření záznamu CNAME, není možné tak, aby odkazoval na profil Traffic Manageru z vrcholu zóny.

Tento problém můžete vyřešit pomocí záznamů aliasů. Na rozdíl od záznamů CNAME alias záznamy se dají vytvářet ve vrcholu zóny a vlastníky aplikace. můžete ji použít k jejich záznamu vrcholu zóny přejděte na profil Traffic Manageru, který má externí koncové body. Počet vlastníků aplikace může odkazovat na stejný profil Traffic Manageru, který se používá u všech ostatních domén v rámci jejich zóny DNS.

Například contoso.com a www\.contoso.com může odkazovat na stejný profil Traffic Manageru. Další informace o záznamů aliasů pomocí profilů Azure Traffic Manageru, najdete v části Další kroky.

### <a name="point-zone-apex-to-azure-cdn-endpoints"></a>Přejděte na vrcholu zóny koncových bodů Azure CDN

Stejně jako profil služby Traffic Manager můžete také použít záznamů aliasů tak, aby odkazoval vaše vrcholu zóny DNS na koncové body Azure CDN. To je užitečné při vytváření statických webů pomocí služby Azure storage a Azure CDN. Potom můžete přistupovat na webu bez předřazení "www" na název DNS.

Například pokud váš statický web název www.contoso.com, vaši uživatelé mohou k webu pomocí contoso.com bez nutnosti předřaďte www jako název DNS.

Jak je popsáno výše, záznamy CNAME nejsou podporovány ve vrcholu zóny. Proto nelze použít záznam CNAME tak, aby odkazovala na váš koncový bod CDN contoso.com. Místo toho můžete použít záznamu o aliasu tak, aby odkazoval na vrcholu zóny přímo do koncového bodu CDN.

> [!NOTE]
> Přejdete na vrcholu zóny koncových bodů CDN pro Azure CDN od Akamai se aktuálně nepodporuje.

## <a name="next-steps"></a>Další postup

Další informace o záznamů aliasů, naleznete v následujících článcích:

- [Kurz: Konfigurace záznamu o aliasu odkazovat na veřejnou IP adresu Azure](tutorial-alias-pip.md)
- [Kurz: Konfigurace záznamu o aliasu pro podporu vrcholu názvy domén s Traffic Managerem](tutorial-alias-tm.md)
- [Nejčastější dotazy k DNS](https://docs.microsoft.com/azure/dns/dns-faq#alias-records)
