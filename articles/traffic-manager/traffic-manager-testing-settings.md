---
title: Ověření nastavení Azure Traffic Manageru
description: Tento článek vám pomůže ověřit nastavení Traffic Manageru.
services: traffic-manager
author: kumudd
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/16/2017
ms.author: kumud
ms.openlocfilehash: 1e954e3c4ebba245d91cfb84ab583b314150e5b2
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2019
ms.locfileid: "54200781"
---
# <a name="verify-traffic-manager-settings"></a>Ověření nastavení Traffic Manageru

Pro testování nastavení Traffic Manageru, musíte mít více klientů v různých umístěních, ze kterých můžete spustit testy. Přepněte koncových bodů ve vašem profilu Traffic Manageru jeden po druhém.

* Nastavte hodnotu hodnotu TTL pro DNS s nízkou tak, aby se změny rozšíří rychle (například 30 sekund).
* Znáte IP adresy služby Azure cloud services a weby v profilu, který testujete.
* Pomocí nástrojů, které vám umožní přeložit název DNS na IP adresu a zobrazit tuto adresu.

Jsou kontroluje se, že přeložit názvy DNS na IP adresami koncových bodů ve vašem profilu. Musí se překládat názvy v souladu s metodu směrování provozu, který je definován v profilu služby Traffic Manager. Můžete použít nástroje jako **nslookup** nebo **dig** k překladu názvů DNS.

Následující příklady můžete otestovat svůj profil Traffic Manageru.

### <a name="check-traffic-manager-profile-using-nslookup-and-ipconfig-in-windows"></a>Zkontrolujte profil služby Traffic Manager pomocí nslookup a konfiguraci protokolu IP ve Windows

1. Spusťte příkaz nebo příkazový řádek Windows Powershellu jako správce.
2. Typ `ipconfig /flushdns` vyprázdnit mezipaměť DNS překladač.
3. Zadejte `nslookup <your Traffic Manager domain name>`. Například následující příkaz ověří název domény s předponou *myapp.contoso*

        nslookup myapp.contoso.trafficmanager.net

    Typické výsledek zobrazí následující informace:

    + Název DNS a IP adresu serveru DNS používané, chcete-li vyřešit tento název domény Traffic Manageru.
    + Název domény Traffic Manageru, jste zadali na příkazovém řádku po "nástroje nslookup" a IP adresy, ke které se přeloží doménu Traffic Manageru. Druhá IP adresa je důležité zkontrolovat. Měl by odpovídat veřejnou virtuální IP (VIP) Adresa pro jedno cloudových služeb nebo webových stránek v profilu služby Traffic Manager, kterou testujete.

## <a name="how-to-test-the-failover-traffic-routing-method"></a>Otestování převzetí služeb při selhání metodu směrování provozu

1. Nechte si všechny koncové body.
2. Pomocí jednoho klienta, požádat o překlad názvů DNS pro název domény vaší společnosti použití příkazu nslookup nebo podobného nástroje.
3. Zajistěte, aby odpovídal přeložit IP adresu primárního koncového bodu.
4. Snížilo primárního koncového bodu nebo odebrat soubor monitorování tak, že Traffic Manager domnívá, že aplikace je mimo provoz.
5. Počkejte DNS Time-to-Live (TTL) profil Traffic Manageru a další dvě minuty. Například pokud váš DNS hodnotě TTL je 300 sekund (5 minut), je musí počkat sedm minut.
6. Vyprázdnění DNS klienta mezipaměti a žádosti o překlad názvů DNS pomocí nástroje nslookup. Ve Windows můžete vyprázdnit mezipaměť DNS pomocí příkazu ipconfig/flushdns.
7. Zajistěte, aby odpovídal přeložit IP adresu sekundárního koncového bodu.
8. Opakujte proces pak Probíhá ukončování každý koncový bod. Ověřte, že server DNS vrátí IP adresu další koncový bod v seznamu. Když všechny koncové body jsou vypnuté, by měl znovu získat IP adresu primárního koncového bodu.

## <a name="how-to-test-the-weighted-traffic-routing-method"></a>Jak otestovat metodu směrování provozu vážený

1. Nechte si všechny koncové body.
2. Pomocí jednoho klienta, požádat o překlad názvů DNS pro název domény vaší společnosti použití příkazu nslookup nebo podobného nástroje.
3. Ujistěte se, že přeložit IP adresa odpovídá jednomu z vašich koncových bodů.
4. Vyprázdnění mezipaměti klienta DNS a opakujte kroky 2 a 3 pro každý koncový bod. Měli byste vidět různé IP adresy, které vrátí pro každou z vašich koncových bodů.

## <a name="how-to-test-the-performance-traffic-routing-method"></a>Jak otestovat metodu směrování provozu výkonu

Efektivní testování metodu směrování provozu výkonu, musí mít klienti umístění v různých částech světa. Vytvoření klientů v různých oblastech Azure, které můžete použít k testování vašich služeb. Pokud máte globální síť, můžete vzdáleně Přihlaste se ke klientům v ostatních částech světa a spuštění testů z něj.

Alternativně existují webové vyhledávání DNS u bezplatných služeb a ponořte se služeb, které jsou k dispozici. Některé z těchto nástrojů vám tak umožnili ke kontrole překlad názvů DNS z různých míst po celém světě. Proveďte na hledání "Vyhledávání DNS" příklady. Služby třetích stran, jako je Gomez nebo hlavní vystoupení slouží k potvrzení, že jsou vaše profily podle očekávání distribuci provozu.

## <a name="next-steps"></a>Další postup

* [O metodách směrování provozu Traffic Manageru](traffic-manager-routing-methods.md)
* [Důležité informace o výkonu nástroje Traffic Manager](traffic-manager-performance-considerations.md)
* [Řešení potíží při sníženém výkonu Traffic Manageru](traffic-manager-troubleshooting-degraded.md)
