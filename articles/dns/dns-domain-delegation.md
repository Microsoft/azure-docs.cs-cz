---
title: Azure DNS delegation overview
description: Zjistěte, jak změnit delegování domény a pomocí názvových serverů Azure DNS umožněte hosting domén.
services: dns
author: asudbring
ms.service: dns
ms.date: 2/19/2019
ms.author: allensu
ms.topic: conceptual
ms.openlocfilehash: 3d8a87e199736bf51fcdc051c17c2fded3402b79
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74212088"
---
# <a name="delegation-of-dns-zones-with-azure-dns"></a>Delegování zón DNS s využitím Azure DNS

Azure DNS vám umožňuje hostovat zónu DNS a spravovat záznamy DNS pro doménu v Azure. Mají-li se dotazy DNS pro doménu dostat k Azure DNS, musí doména být delegovaná z nadřazené domény do Azure DNS. Pamatujte, že Azure DNS není doménový registrátor. Tento článek vysvětluje princip fungování delegování domén a ukazuje, jak lze domény delegovat do Azure DNS.

## <a name="how-dns-delegation-works"></a>Jak funguje delegování DNS

### <a name="domains-and-zones"></a>Domény a zóny

Domain Name System je hierarchie domén. Hierarchie začíná od kořenové domény, jejíž název je jednoduše „ **.** “.  Následují domény nejvyšší úrovně, jako jsou „com“, „net“, „org“, „uk“ nebo „jp“.  Pod doménami nejvyšší úrovně jsou domény druhé úrovně, jako jsou „org.uk“ nebo „co.jp“.  A tak dále. Domény v hierarchii DNS jsou hostované pomocí oddělených zón DNS. Tyto zóny jsou globálně distribuované a hostované názvovými servery DNS po celém světě.

**Zóna DNS** – Doména je jedinečný název v systému DNS (Domain Name System), například contoso.com. Zóna DNS se používá k hostování záznamů DNS pro konkrétní doménu. Například doména contoso.com může obsahovat několik záznamů DNS, třeba mail.contoso.com (pro poštovní server) a www.contoso.com (pro web).

**Doménový registrátor** – Doménový registrátor je společnost, která poskytuje názvy internetových domén. Ověří, zda je internetová doména, kterou chcete použít, volná a umožní vám ji zakoupit. Jakmile je název domény registrovaný, stanete se jejím právoplatným vlastníkem. Pokud již máte internetovou doménu, použijete pro delegování do Azure DNS současného doménového registrátora.

For more information about accredited domain registrars, see [ICANN-Accredited Registrars](https://www.icann.org/registrar-reports/accredited-list.html).

### <a name="resolution-and-delegation"></a>Překládání a delegování

Existují dva typy serverů DNS:

* *Autoritativní* server DNS hostí zóny DNS. Odpovídá pouze na dotazy DNS pro záznamy v těchto zónách.
* *Rekurzivní* server DNS nehostuje zóny DNS. Odpovídá na všechny dotazy DNS voláním autoritativních serverů DNS, které shromáždí potřebná data.

Azure DNS poskytuje autoritativní službu DNS.  Neposkytuje rekurzivní službu DNS. Cloud Services a virtuální počítače v Azure se automaticky konfigurují, aby používaly rekurzivní službu DNS, která se poskytuje samostatně jako součást infrastruktury Azure. Informace o tom, jak změnit tato nastavení DNS, najdete v tématu [Překlad názvů v Azure](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

Klient DNS v počítačích nebo na mobilních zařízeních obvykle pro všechny dotazy DNS klientské aplikace volá rekurzivní server DNS.

Když rekurzivní server DNS obdrží dotaz na záznam DNS, jako například www.contoso.com, nejprve musí najít názvový server, který hostuje zónu pro doménu contoso.com. Aby našel názvový server, začne u kořenových názvových serverů a vyhledá názvové servery, které hostují zónu „com“. Následně se dotazuje názvových serverů „com“ a tak najde názvové servery, které hostují zónu contoso.com.  Nakonec se těchto názvových serverů může dotázat na www.contoso.com.

Tento postup se nazývá překlad názvu DNS. Přesněji řečeno, překlad DNS zahrnuje ještě další kroky, jako třeba sledování záznamů CNAME, ale pro pochopení, jak funguje delegování DNS, to není důležité.

Jak nadřazená zóna „ukáže“ na názvové servery pro podřízenou zónu? Používá k tomu speciální typ záznamu DNS, který se nazývá záznam NS (NS zastupuje „názvový server“). Například kořenová zóna obsahuje záznamy NS pro „com“ a ukazuje názvové servery pro zónu „com“. Zóna „com“ pak obsahuje záznamy NS pro contoso.com, které ukazují názvové servery pro zónu contoso.com. Nastavení záznamů NS v nadřazené zóně pro podřízenou zónu se nazývá delegování domény.

Následující obrázek ukazuje příklad dotazu DNS. Contoso.net a partners.contoso.net jsou zóny Azure DNS.

![Názvový server DNS](./media/dns-domain-delegation/image1.png)

1. Klient si vyžádá `www.partners.contoso.net` z místního serveru DNS.
2. Místní server DNS záznam nemá, proto vytvoří požadavek na svůj kořenový názvový server.
3. Kořenový názvový server záznam nemá, ale zná adresu názvového serveru `.net`, kterou poskytne serveru DNS.
4. The local DNS server sends the request to the `.net` name server.
5. The `.net` name server does not have the record but does know the address of the `contoso.net` name server. In this case, it responds with the address of the name server for the DNS zone hosted in Azure DNS.
6. The local DNS server sends the request to the name server for the `contoso.net` zone hosted in Azure DNS.
7. The zone `contoso.net` does not have the record but knows the name server for `partners.contoso.net` and responds with the address. In this case, it is a DNS zone hosted in Azure DNS.
8. The local DNS server sends the request to the name server for the `partners.contoso.net` zone.
9. The `partners.contoso.net` zone has the A record and responds with the IP address.
10. The local DNS server provides the IP address to the client
11. Klient se připojí k webu `www.partners.contoso.net`.

Každé delegování má ve skutečnosti dvě kopie záznamů NS – jednu v nadřazené zóně, která ukazuje na podřízenou zónu, a druhou v samotné podřízené zóně. Zóna contoso.net obsahuje záznamy NS pro contoso.net (vedle záznamů NS v „net“). Tyto záznamy se nazývají záznamy autoritativních NS a nacházejí se na vrcholu podřízené zóny.

## <a name="next-steps"></a>Další kroky

Zjistěte, jak [delegovat doménu do Azure DNS](dns-delegate-domain-azure-dns.md).

