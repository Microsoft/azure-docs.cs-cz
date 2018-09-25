---
title: Azure DNS alias Přehled záznamů
description: Přehled podpory pro záznamů aliasů v Microsoft Azure DNS.
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 9/25/2018
ms.author: victorh
ms.openlocfilehash: 05a6a1700de2b8b334b40d9efd9b8d79e9e7241f
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46957554"
---
# <a name="azure-dns-alias-records-overview"></a>Azure DNS alias Přehled záznamů

Azure záznamů aliasů DNS jsou kvalifikace na sadu záznamů DNS, který umožňuje odkazovat na další prostředky Azure z v rámci zóny DNS. Můžete například vytvořit alias sady záznamů, odkazující veřejné IP adrese Azure záznam. Protože sady záznamů alias odkazuje na instanci služby Azure veřejnou IP adresu dynamicky, sadu záznamů alias bezproblémově se aktualizuje sám během překlad DNS.

Sady záznamů alias je podporována pro následující typy záznamů v zóně Azure DNS: A a AAAA, CNAME. 

> [!NOTE]
> Alias záznamy A nebo AAAA typy záznamů pro Traffic Manager se podporují jenom pro typy externích koncových bodů. Je nutné zadat adresu IPv4 nebo IPv6 (v ideálním případě statických IP adres) v závislosti na externí koncové body Traffic Manageru.

## <a name="capabilities"></a>Možnosti

- **Ukazovat na prostředek veřejné IP adresy z DNS sady záznamů A/AAAA**. Můžete vytvořit sadu záznamů A/AAAA a nastavte ji sady záznamů v aliasu tak, aby odkazoval na prostředek veřejné IP adresy.
- **Přejděte na profil Traffic Manageru ze sady záznamů DNS A/AAAA/CNAME**. Kromě možnosti tak, aby odkazoval na CNAME profilu Traffic Manageru (například: contoso.trafficmanager.net) ze sady záznamů DNS CNAME, můžete teď také ukázat na profil Traffic Manageru, který má externí koncové body, od A nebo AAAA sady záznamů ve službě DNS zóny.
   > [!NOTE]
   > Alias záznamy A nebo AAAA typy záznamů pro Traffic Manager se podporují jenom pro typy externích koncových bodů. Je nutné zadat adresu IPv4 nebo IPv6 (v ideálním případě statických IP adres) v závislosti na externí koncové body Traffic Manageru.
- **Přejděte na jinou sadu záznamů DNS v rámci stejné zóny**. Záznamů aliasů můžete odkazovat na jiné sady záznamů stejného typu. Například můžete mít na sadu záznamů DNS CNAME do jiné sady záznamů CNAME stejného typu jako alias. To je užitečné, pokud chcete mít některé sady záznamů se aliasy a některé jako jiné aliasy z hlediska chování.

## <a name="scenarios"></a>Scénáře
Existuje několik běžných scénářů pro záznamů aliasů:

### <a name="prevent-dangling-dns-records"></a>Zabránit nepropojená záznamů DNS
Z v rámci zóny Azure DNS záznamů aliasů umožňuje úzce udržovat přehled o životním cyklu Azure prostředky, jako jsou veřejné IP adresy a Traffic Manager profilu. Jednou z běžných potíží s tradiční záznamů DNS je "nepropojená záznamy", zejména s A/AAAA, CNAME nebo typy záznamů. 

Tradiční zaznamenané zóny DNS Pokud cílová IP adresa nebo CNAME už existuje, záznam DNS zóny nemá žádné informace o tom a je potřeba ručně aktualizovat. V některých organizacích Tento ruční aktualizace nemusí dojít v čase nebo může být problematický z důvodu oddělení rolí a úrovní oprávnění přidružené.

Například role, který má oprávnění k odstranění CNAME nebo IP adresy, které patří k aplikaci nemusí mít dostatečná oprávnění k aktualizaci záznamu DNS, který odkazuje na tyto cíle. V důsledku toho může být časovou prodlevu mezi při odstranění IP adresy nebo CNAME a záznam DNS, odkazuje na ho Odebereme, která by mohla způsobit výpadek pro koncové uživatele.

Záznamů aliasů odebrat složitým řešením při tomto scénáři a pomáhá zabránit nepropojená odkazu. Při záznamu DNS je kvalifikován jako záznamu o aliasu tak, aby odkazoval na veřejnou IP adresu nebo profil Traffic Manageru a těchto základních prostředků se odstraní, odebere se záznam alias DNS také ve stejnou dobu. Tím se zajistí, že koncoví uživatelé nikdy dochází kvůli výpadku.

### <a name="update-dns-zones-automatically-when-application-ips-change"></a>Aktualizovat DNS zóny automaticky při změně IP adresy aplikace

Podobně jako v předchozím scénáři, pokud aplikace je přesunut nebo pokud základní virtuální počítač se restartuje, vytvoří se záznam aliasu se aktualizuje automaticky při změně IP adresy pro tento základní prostředek veřejné IP adresy. Pokud koncoví uživatelé jsou směrované na jinou aplikaci, která obsahuje starou IP adresu se můžete vyhnout potenciální bezpečnostní rizika.

### <a name="host-load-balanced-applications-at-the-zone-apex"></a>Hostování aplikací s vyrovnáváním zatížení ve vrcholu zóny

Protokol DNS brání přiřazení čehokoli jiného, než záznam A nebo AAAA ve vrcholu zóny (Příklad: contoso.com). Toto představuje problém, a počet vlastníků aplikace, kteří mají zatížení s vyrovnáváním aplikace za na Traffic managera, protože nebylo možné tak, aby odkazoval na profil Traffic Manageru z vrcholu zóny. Počet vlastníků aplikace byly v důsledku toho musí použít alternativní řešení. Například přesměrování v aplikační vrstvě pro přesměrování z vrcholu zóny do jiné domény (z contoso.com na www.contoso.com). To představuje jediný bod selhání z hlediska funkci přesměrování.

Pomocí záznamů aliasů tento problém již neexistuje. Počet vlastníků aplikace teď může odkazovat jejich záznamu vrcholu zóny na profil Traffic Manageru, který má externí koncové body. Díky této funkci můžete počet vlastníků aplikace odkazovat na stejný profil Traffic Manageru, který se používá u všech ostatních domén v rámci jejich zóny DNS. Například contoso.com a www.contoso.com může i odkazovat na stejný profil Traffic Manageru, dokud profil služby Traffic Manager má jenom externí nakonfigurované koncové body.

## <a name="next-steps"></a>Další postup

Další informace najdete informace o záznamů aliasů, najdete v článcích naleznete:

- [Kurz: Konfigurace záznamu o aliasu pro odkazování na Azure veřejnou IP adresu](tutorial-alias-pip.md)
- [Kurz: Konfigurace záznamu o aliasu pro podporu vrcholu názvy domén s Traffic Managerem](tutorial-alias-tm.md)
- [Nejčastější dotazy k DNS](https://docs.microsoft.com/azure/dns/dns-faq#alias-records)
