---
title: Ověřte nastavení Azure Traffic Manageru | Microsoft Docs
description: Tento článek vám pomůže ověřte nastavení Traffic Manager
services: traffic-manager
documentationcenter: ''
author: kumudd
manager: timlt
editor: ''
ms.assetid: 2180b640-596e-4fb2-be59-23a38d606d12
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/16/2017
ms.author: kumud
ms.openlocfilehash: aadff1806a7cb22347283143563467366e857569
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23876352"
---
# <a name="verify-traffic-manager-settings"></a>Ověřte nastavení Traffic Manager

Můžete zkontrolovat nastavení Traffic Manager, musíte mít více klientů v různých umístěních, ze kterých můžete spustit testy. Potom přepněte koncových bodů ve vašem profilu Traffic Manageru dolů po jednom.

* Nastavte hodnotu DNS TTL nízkou tak, aby změny rozšíří rychle (například 30 sekund).
* Znáte IP adresy služby Azure cloud services a weby v profilu, který testujete.
* Pomocí nástrojů, které umožňují přeložit název DNS na IP adresu a zobrazit tuto adresu.

Probíhá kontrola, že přeložit názvy DNS na IP adresy koncových bodů ve vašem profilu. Musí se překládat názvy v souladu s metodu směrování provozu, který je definován v profilu Traffic Manageru. Můžete použít nástroje jako **nslookup** nebo **prozkoumat** k překladu názvů DNS.

Následující příklady můžete otestovat váš profil Traffic Manageru.

### <a name="check-traffic-manager-profile-using-nslookup-and-ipconfig-in-windows"></a>Zkontrolujte profil služby Traffic Manager pomocí nástroje nslookup a ipconfig v systému Windows

1. Otevřete příkaz či řádku prostředí Windows PowerShell jako správce.
2. Typ `ipconfig /flushdns` k vyprázdnit mezipaměť DNS překladač.
3. Zadejte `nslookup <your Traffic Manager domain name>`. Například následující příkaz ověří název domény s předponou *myapp.contoso*

        nslookup myapp.contoso.trafficmanager.net

    Obvykle za následek zobrazí následující informace:

    + Název DNS a IP adresa serveru DNS, přistupuje na tento název domény Traffic Manageru.
    + Název domény Traffic Manageru, jste zadali na příkazovém řádku po "nástroje nslookup" a IP adresu, na kterou se přeloží doménu Traffic Manageru. Druhou IP adresu je důležité zkontrolovat. Měla by se shodovat veřejné virtuální adresy IP (VIP) pro některý z cloudové služby nebo webů v profil služby Traffic Manager, který testujete.

## <a name="how-to-test-the-failover-traffic-routing-method"></a>Postup testování metodu směrování provozu převzetí služeb při selhání

1. Ponechejte si všechny koncové body.
2. Pomocí jednoho klienta, požadavky na překlad DNS pro název domény vaší společnosti, pomocí nástroje nslookup nebo podobného nástroje.
3. Zajistěte, aby odpovídal přeložit IP adresu primární koncový bod.
4. Přeneste dolů váš primární koncový bod nebo odeberte soubor monitorování, aby se tento Traffic Manager se domnívá, že aplikace je mimo provoz.
5. Počkejte DNS Time-to-Live (TTL) profil služby Traffic Manager plus dalších dvou minut. Například pokud vaše TTL DNS je 300 sekund (5 minut), je nutné počkat sedm minut.
6. Vyprázdnění vaší DNS klienta mezipaměti a žádosti o překlad DNS pomocí nástroje nslookup. V systému Windows můžete vyprázdnit mezipaměť DNS pomocí příkazu ipconfig/flushdns.
7. Zajistěte, aby odpovídal přeložit IP adresu sekundární koncový bod.
8. Opakujte tento postup, zase ukončování každý koncový bod. Ověřte, že DNS vrátí IP adresu další koncového bodu v seznamu. Když jsou všechny koncové body dolů, musí znovu získat IP adresu primární koncový bod.

## <a name="how-to-test-the-weighted-traffic-routing-method"></a>Postup testování metodu směrování provozu vyvážené

1. Ponechejte si všechny koncové body.
2. Pomocí jednoho klienta, požadavky na překlad DNS pro název domény vaší společnosti, pomocí nástroje nslookup nebo podobného nástroje.
3. Zkontrolujte, zda přeložit IP adresa odpovídá jeden z koncových bodů.
4. Vyprázdnění mezipaměti klienta DNS a zopakujte kroky 2 a 3 pro každý koncový bod. Měli byste vidět různé IP adresy vrátí pro každou z koncových bodů.

## <a name="how-to-test-the-performance-traffic-routing-method"></a>Postup testování metodu směrování provozu výkonu

K testování efektivně metodu směrování provozu výkonu, musí mít klienti nacházející se v různých částech světa. Klienty můžete vytvořit v různých oblastech Azure, které lze použít k testování vašich služeb. Pokud máte globální sítě, můžete vzdáleně Přihlaste se na klienty v dalších částech světa a spouštění testů z ní.

Alternativně existují uvolněte webové vyhledávání DNS a víc služeb, které jsou k dispozici. Některé z těchto nástrojů získáte možnost zkontrolujte překlad názvu DNS z různých míst po celém světě. Proveďte hledání na "Vyhledávání DNS" příklady. Služby třetích stran, jako je Gomez nebo //Build slouží k potvrzení, že jsou vaše profily distribuci provoz podle očekávání.

## <a name="next-steps"></a>Další kroky

* [O metodách směrování provozu Traffic Manager](traffic-manager-routing-methods.md)
* [Důležité informace o výkonu nástroje Traffic Manager](traffic-manager-performance-considerations.md)
* [Řešení potíží při sníženém výkonu Traffic Manageru](traffic-manager-troubleshooting-degraded.md)
