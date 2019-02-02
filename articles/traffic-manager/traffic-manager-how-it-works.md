---
title: Jak funguje Azure Traffic Manager | Dokumentace Microsoftu
description: Tento článek vám pomůže pochopit, jak Traffic Manager směruje Azure provoz pro vysoký výkon a dostupnost vašich webových aplikací
services: traffic-manager
documentationcenter: ''
author: kumudd
manager: twooley
editor: ''
ms.assetid: ''
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/25/2018
ms.author: kumud
ms.openlocfilehash: 26d61c1b263a8fa7ff4f0ff5b2888f1d900e772e
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2019
ms.locfileid: "55567838"
---
# <a name="how-traffic-manager-works"></a>Jak funguje Traffic Manager

Azure Traffic Manager umožňuje řídit distribuci provozu mezi koncových bodů vaší aplikace. Koncový bod je jakákoli internetová služba hostovaná v rámci nebo mimo Azure.

Traffic Manager poskytuje dva klíčové výhody:

- Distribuce provozu podle jednoho z několika [metody směrování provozu](traffic-manager-routing-methods.md)
- [Nepřetržité monitorování koncových bodů](traffic-manager-monitoring.md) a automatické převzetí služeb při selhání, když dojde k selhání koncových bodů

Když se klient pokouší připojit ke službě, musí nejdřív se přeložit název DNS služby na IP adresu. Klient pak připojí k této IP adresy pro přístup ke službě.

**Nejdůležitější bod je pochopit, že Traffic Manager pracuje na úrovni DNS.**  Nasměrování klientů na určité služby koncové body na základě pravidel metodu směrování provozu Traffic Manageru pomocí služby DNS. Klienti připojení k vybraný koncový bod **přímo**. Traffic Manager se proxy server nebo bránu. Traffic Manager nezná provoz mezi klientem a služby předávání.

## <a name="traffic-manager-example"></a>Příklad Traffic Manageru

Společnosti Contoso Corp vyvinuli nový portál pro partnery. Adresa URL pro tento portál je https://partners.contoso.com/login.aspx. Aplikace je hostována ve třech oblastech Azure. Pokud chcete zlepšit dostupnost a maximalizovat výkon globální, používají Traffic Manager za účelem distribuce provozu klienta na nejbližší dostupný koncový bod.

Získat tuto konfiguraci, proveďte následující kroky:

1. Nasazení tři instance příslušnou službu. Názvy DNS tato nasazení jsou ".net us.cloudapp společnosti contoso", "contoso-eu.cloudapp .net" a "contoso-asia.cloudapp .net".
2. Vytvořit profil služby Traffic Manager, s názvem "contoso.trafficmanager.net" a nakonfigurujte ho na použití metody směrování provozu "Výkonu" ve třech koncových bodech.
* Konfigurace názvu domény individuální "partners.contoso.com" na "contoso.trafficmanager.net" pomocí záznamu DNS CNAME.

![Konfigurace DNS Traffic Manageru][1]

> [!NOTE]
> Pokud používáte vlastní doménu s Azure Traffic Managerem, musíte použít záznam CNAME tak, aby odkazoval název vaší vlastní domény na váš název domény Traffic Manageru. Standardy DNS nepovolují můžete vytvořit záznam CNAME v "vrcholu" (neboli kořenového) domény. Proto nelze vytvořit záznam CNAME pro "contoso.com" (někdy nazývané "základní" doména). Můžete vytvořit pouze záznam CNAME pro doménu v části "contoso.com", jako je "www.contoso.com". Toto omezení obejít, doporučujeme, abyste svoji doménu DNS hostovat na [Azure DNS](../dns/dns-overview.md) a pomocí [záznamů aliasů](../dns/tutorial-alias-tm.md) tak, aby odkazoval na svůj profil traffic Manageru. Případně můžete použít jednoduchý přesměrování protokolu HTTP k přímé požadavky pro "contoso.com" na alternativní název, například "www.contoso.com".

### <a name="how-clients-connect-using-traffic-manager"></a>Připojení klientů pomocí Traffic Manageru

Pokračováním z předchozího příkladu, když klient požádá o stránku https://partners.contoso.com/login.aspx, klient provede následující kroky přeložit název DNS a naváže připojení:

![Připojení zařízení pomocí služby Traffic Manager][2]

1. Klient odešle dotaz DNS na jeho nakonfigurované rekurzivní službu DNS přeložit název "partners.contoso.com". Rekurzivní službu DNS, někdy říká "místní služby DNS' služba, není hostitelem DNS domény přímo. Klient místo toho off-loads práci při kontaktování různé autoritativní služby DNS na Internetu potřeba přeložit název DNS.
2. Rekurzivní službu DNS pro překlad názvu DNS, najde názvové servery pro doménu "contoso.com". Potom kontaktuje tyto názvové servery k vyžádání záznam DNS "partners.contoso.com". Servery DNS contoso.com vrátit záznam CNAME, který odkazuje na contoso.trafficmanager.net.
3. V dalším kroku rekurzivní službu DNS najde názvové servery pro doménu "trafficmanager.net", které jsou k dispozici ve službě Azure Traffic Manageru. Potom odešle požadavek pro záznam DNS "contoso.trafficmanager.net" na tyto servery DNS.
4. Požadavek přijmou názvové servery Traffic Manageru. Rozhodne na základě koncového bodu:

    - Nakonfigurovaný stav každého koncového bodu (zakázáno koncových bodů nejsou vráceny)
    - Aktuální stav každého koncového bodu, počítáno od kontroly stavu Traffic Manageru. Další informace najdete v tématu [monitorování koncových bodů Traffic Manageru](traffic-manager-monitoring.md).
    - Vybrané metody směrování provozu. Další informace najdete v tématu [metody směrování Traffic Manageru](traffic-manager-routing-methods.md).

5. Zvolený koncový bod se vrátí jako jiný záznam DNS CNAME. V takovém případě se dejte nám Předpokládejme, že se vrátí contoso us.cloudapp.net.
6. V dalším kroku rekurzivní službu DNS najde názvové servery pro doménu "cloudapp.net". Kontaktuje tyto názvové servery požádat o rozhraní .net contoso-us.cloudapp záznam DNS. Vrátí se záznam DNS "A", který obsahuje IP adresu koncového bodu služby umístěné v USA.
7. Rekurzivní službu DNS konsoliduje výsledky a vrátí jednu odpověď DNS klientovi.
8. Klient obdrží výsledky DNS a připojí k dané IP adrese. Klient připojí ke koncovému bodu aplikace služby přímo, není pomocí Traffic Manageru. Protože je koncový bod HTTPS, klient provádí potřebné handshake SSL/TLS a pak požadavek HTTP GET "/ login.aspx" stránky.

Rekurzivní službu DNS ukládá do mezipaměti odpovědi DNS, které obdrží. Překladač DNS v klientském zařízení také ukládá do mezipaměti výsledek. Ukládání do mezipaměti umožňuje další dotazy DNS k zodpovězení rychleji pomocí dat z mezipaměti namísto dotazování další názvové servery. Doba trvání mezipaměti je určen vlastností "time-to-live" (TTL) každého záznamu DNS. Nižší hodnoty za následek rychlejší vypršení platnosti mezipaměti a tedy další doby odezvy na názvové servery Traffic Manageru. Delší hodnoty znamenají, že může trvat delší dobu směrovat přenos dat mimo neúspěšné koncový bod. Traffic Manager umožňuje nakonfigurovat interval TTL, ZÍSKÁ dříve v odpovědi DNS Traffic Manageru v rozsahu od 0 sekund a tak vysoké jako 2 147 483 647 sekund (maximální rozsah, který je kompatibilní s [definicí RFC 1035](https://www.ietf.org/rfc/rfc1035.txt)), protože umožňují výběr hodnoty, které bude nejlépe Vyrovnává potřebám vaší aplikace.


## <a name="next-steps"></a>Další postup

Další informace o Traffic Manageru [koncový bod monitorování a automatickému převzetí služeb při selhání](traffic-manager-monitoring.md).

Další informace o Traffic Manageru [metody směrování provozu](traffic-manager-routing-methods.md).

<!--Image references-->
[1]: ./media/traffic-manager-how-traffic-manager-works/dns-configuration.png
[2]: ./media/traffic-manager-how-traffic-manager-works/flow.png

