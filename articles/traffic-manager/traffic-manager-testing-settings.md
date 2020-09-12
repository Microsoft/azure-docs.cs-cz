---
title: Ověření nastavení Azure Traffic Manager
description: V tomto článku se dozvíte, jak ověřit nastavení Traffic Manager a testovat metodu směrování provozu.
services: traffic-manager
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/16/2017
ms.author: duau
ms.openlocfilehash: 4bb7458d4258dffb074d663e5cf712c7a0245cc7
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89401516"
---
# <a name="verify-traffic-manager-settings"></a>Ověření nastavení Traffic Manageru

Chcete-li otestovat nastavení Traffic Manager, je nutné mít více klientů, v různých umístěních, z nichž lze spustit testy. Pak postupně přeneste koncové body v profilu Traffic Manager.

* Nastavte hodnotu TTL služby DNS na nízká, aby se změny rozšířily rychle (například 30 sekund).
* Zjistěte, jaké IP adresy Azure Cloud Services a websites jsou v profilu, který testujete.
* Použijte nástroje, které vám umožní přeložit název DNS na IP adresu a zobrazit tuto adresu.

Kontrolujete, že názvy DNS se překládají na IP adresy koncových bodů ve vašem profilu. Názvy by se měly vyřešit způsobem konzistentním s metodou směrování provozu definovanou v profilu Traffic Manager. K překladu názvů DNS můžete použít nástroje, jako je například **nslookup** nebo **dig** .

Následující příklady vám pomůžou otestovat profil Traffic Manager.

### <a name="check-traffic-manager-profile-using-nslookup-and-ipconfig-in-windows"></a>Kontrolovat profil Traffic Manager pomocí příkazu nslookup a ipconfig ve Windows

1. Otevřete příkaz nebo spusťte příkazový řádek Windows PowerShellu jako správce.
2. Zadejte `ipconfig /flushdns` , chcete-li vyprázdnit mezipaměť překladače DNS.
3. Zadejte příkaz `nslookup <your Traffic Manager domain name>`. Například následující příkaz zkontroluje název domény s předponou *MyApp. contoso.*

    ```powershell
    nslookup myapp.contoso.trafficmanager.net
    ```

    Typický výsledek zobrazuje následující informace:

    + Název DNS a IP adresa serveru DNS, ke kterému se přistupovalo, aby se tento Traffic Manager název domény vyřešil.
    + Název domény Traffic Manager, který jste zadali na příkazovém řádku za "nslookup" a IP adresu, na kterou se Traffic Manager doména řeší. Druhá IP adresa je důležité, abyste ji zkontrolovali. Měl by odpovídat veřejné virtuální IP (VIP) adrese pro jednu z cloudových služeb nebo webů v profilu Traffic Manager, který testujete.

## <a name="how-to-test-the-failover-traffic-routing-method"></a>Postup testování metody směrování provozu převzetí služeb při selhání

1. Ponechte všechny koncové body nahoru.
2. Pomocí jednoho klienta si vyžádejte překlad DNS pro název domény vaší společnosti pomocí příkazu nslookup nebo podobného nástroje.
3. Ujistěte se, že přeložená IP adresa odpovídá primárnímu koncovému bodu.
4. Odpojte primární koncový bod nebo odeberte soubor monitorování, aby Traffic Manager pomohlo, že aplikace nefunguje.
5. Počkejte, až bude hodnota TTL (Time-to-Live) služby DNS profilu Traffic Manager a dalších dvou minut. Pokud je například vaše hodnota TTL pro DNS 300 sekund (5 minut), musíte počkat na sedm minut.
6. Vyprázdněte mezipaměť klienta DNS a vyžádejte si překlad DNS pomocí nástroje Nslookup. V systému Windows můžete mezipaměť DNS vyprázdnit pomocí příkazu ipconfig/flushdns.
7. Zajistěte, aby přeložená IP adresa odpovídala vašemu sekundárnímu koncovému bodu.
8. Opakujte tento postup a postupně přepněte jednotlivé koncové body. Ověřte, že DNS vrátí IP adresu dalšího koncového bodu v seznamu. Až budou všechny koncové body vypnuté, měli byste znovu získat IP adresu primárního koncového bodu.

## <a name="how-to-test-the-weighted-traffic-routing-method"></a>Postup testování metody směrování váženého provozu

1. Ponechte všechny koncové body nahoru.
2. Pomocí jednoho klienta si vyžádejte překlad DNS pro název domény vaší společnosti pomocí příkazu nslookup nebo podobného nástroje.
3. Ujistěte se, že přeložená IP adresa odpovídá jednomu z vašich koncových bodů.
4. Vyprázdněte mezipaměť klienta DNS a opakujte kroky 2 a 3 pro každý koncový bod. Pro každý z vašich koncových bodů byste měli vidět různé IP adresy.

## <a name="how-to-test-the-performance-traffic-routing-method"></a>Postup testování metody směrování provozu výkonu

Aby bylo možné efektivně otestovat metodu směrování provozu výkonu, je nutné mít klienty nacházející se v různých částech světa. Můžete vytvářet klienty v různých oblastech Azure, které je možné použít k otestování služeb. Pokud máte globální síť, můžete se vzdáleně přihlásit ke klientům v jiných částech světa a spustit testy z těchto počítačů.

Alternativně jsou k dispozici bezplatné webové vyhledávání služby DNS a dig služby. Některé z těchto nástrojů umožňují kontrolu překladu názvů DNS z různých míst po celém světě. Příklady najdete v tématu vyhledávání DNS. Služby třetích stran, jako je Gomez nebo vystoupení, se dají použít k potvrzení, že vaše profily distribuují provoz podle očekávání.

## <a name="next-steps"></a>Další kroky

* [Informace o metodách směrování provozu Traffic Manager](traffic-manager-routing-methods.md)
* [Důležité informace o výkonu nástroje Traffic Manager](traffic-manager-performance-considerations.md)
* [Řešení potíží při sníženém výkonu Traffic Manageru](traffic-manager-troubleshooting-degraded.md)
