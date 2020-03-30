---
title: Ověření nastavení Azure Traffic Manageru
description: V tomto článku se dozvíte, jak ověřit nastavení traffic manageru a otestovat metodu směrování provozu.
services: traffic-manager
author: rohinkoul
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/16/2017
ms.author: rohink
ms.openlocfilehash: 94ab5e550f0053fa19b9b93f1d67690211543325
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938384"
---
# <a name="verify-traffic-manager-settings"></a>Ověření nastavení Traffic Manageru

Chcete-li otestovat nastavení traffic manageru, musíte mít více klientů na různých místech, ze kterých můžete spustit testy. Potom převeďte koncové body v profilu Traffic Manageru dolů jeden po druhém.

* Nastavte nízkou hodnotu TTL DNS tak, aby se změny rychle množila (například 30 sekund).
* Seznamte se s IP adresami cloudových služeb Azure a webů v profilu, který testujete.
* Používejte nástroje, které umožňují přeložit název DNS na adresu IP a tuto adresu zobrazit.

Kontrolujete, zda se názvy DNS překládá na ip adresy koncových bodů ve vašem profilu. Názvy by měly být řešeny způsobem, který je v souladu s metodou směrování provozu definovanou v profilu traffic manageru. Můžete použít nástroje, jako **je nslookup** nebo **dig** k vyřešení názvů DNS.

Následující příklady vám pomohou otestovat profil Traffic Manageru.

### <a name="check-traffic-manager-profile-using-nslookup-and-ipconfig-in-windows"></a>Kontrola profilu Traffic Manageru pomocí nslookup a ipconfig v systému Windows

1. Otevřete příkaz nebo výzvu prostředí Windows PowerShell jako správce.
2. Zadejte `ipconfig /flushdns` vyprázdnění mezipaměti překladače DNS.
3. Zadejte `nslookup <your Traffic Manager domain name>`. Následující příkaz například zkontroluje název domény s předponou *myapp.contoso*

        nslookup myapp.contoso.trafficmanager.net

    Typický výsledek ukazuje následující informace:

    + Název DNS a adresa IP serveru DNS, ke které se přistupuje k překladu tohoto názvu domény Traffic Manageru.
    + Název domény Traffic Manageru, který jste zadali na příkazovém řádku po "nslookup" a IP adrese, na kterou se doména Traffic Manageru překládá. Druhá IP adresa je důležitá pro kontrolu. Měla by odpovídat veřejné virtuální IP adrese (VIP) pro jednu z cloudových služeb nebo webů v profilu Traffic Manageru, který testujete.

## <a name="how-to-test-the-failover-traffic-routing-method"></a>Jak otestovat metodu směrování provozu převzetí služeb při selhání

1. Ponechte všechny koncové body nahoru.
2. Pomocí jednoho klienta požádejte o překlad DNS pro název domény vaší společnosti pomocí nslookup nebo podobného nástroje.
3. Ujistěte se, že vyřešená adresa IP odpovídá primárnímu koncovému bodu.
4. Převeďte primární koncový bod nebo odeberte monitorovací soubor tak, aby si Traffic Manager myslel, že aplikace je vypnutá.
5. Počkejte na dns time-to-live (TTL) profilu Traffic Manager plus další dvě minuty. Pokud je například ttl DNS 300 sekund (5 minut), musíte počkat sedm minut.
6. Vyprázdněte mezipaměť klienta DNS a požádejte o překlad DNS pomocí nslookup. V systému Windows můžete vyprázdnit mezipaměť DNS pomocí příkazu ipconfig /flushdns.
7. Ujistěte se, že vyřešená adresa IP odpovídá sekundárnímu koncovému bodu.
8. Opakujte proces a srážlivě každý koncový bod. Ověřte, zda služba DNS vrací adresu IP dalšího koncového bodu v seznamu. Když všechny koncové body jsou mimo, měli byste získat IP adresu primárního koncového bodu znovu.

## <a name="how-to-test-the-weighted-traffic-routing-method"></a>Jak otestovat metodu směrování vážených dopravních směrů

1. Ponechte všechny koncové body nahoru.
2. Pomocí jednoho klienta požádejte o překlad DNS pro název domény vaší společnosti pomocí nslookup nebo podobného nástroje.
3. Ujistěte se, že vyřešená adresa IP odpovídá jednomu z vašich koncových bodů.
4. Vyprázdněte mezipaměť klienta DNS a opakujte kroky 2 a 3 pro každý koncový bod. Měli byste vidět různé IP adresy vrácené pro každý z vašich koncových bodů.

## <a name="how-to-test-the-performance-traffic-routing-method"></a>Jak otestovat metodu směrování provozu výkonu

Chcete-li efektivně otestovat metodu směrování provozu výkonu, musíte mít klienty umístěné v různých částech světa. Můžete vytvořit klienty v různých oblastech Azure, které lze použít k testování vašich služeb. Pokud máte globální síť, můžete se vzdáleně přihlásit ke klientům v jiných částech světa a spustit testy odtud.

Případně jsou k dispozici bezplatné webové služby vyhledávání DNS a dig. Některé z těchto nástrojů vám dávají možnost kontrolovat překlad názvů DNS z různých míst po celém světě. Proveďte vyhledávání na "DNS vyhledávání" pro příklady. Služby třetích stran, jako je Gomez nebo Keynote, lze použít k potvrzení, že vaše profily distribuují provoz podle očekávání.

## <a name="next-steps"></a>Další kroky

* [O metodách směrování provozu traffic manageru](traffic-manager-routing-methods.md)
* [Důležité informace o výkonu nástroje Traffic Manager](traffic-manager-performance-considerations.md)
* [Řešení potíží při sníženém výkonu Traffic Manageru](traffic-manager-troubleshooting-degraded.md)
