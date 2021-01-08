---
title: Kontrola paketů pomocí Azure Network Watcher | Microsoft Docs
description: Tento článek popisuje, jak pomocí Network Watcher provádět hloubkové kontroly paketů shromážděné z virtuálního počítače.
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: 7b907d00-9c35-40f5-a61e-beb7b782276f
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: 3bd85d6faf05fcf659e9335ee9de3d64198dfa08
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2021
ms.locfileid: "98011165"
---
# <a name="packet-inspection-with-azure-network-watcher"></a>Kontrola paketů pomocí Network Watcher Azure

Pomocí funkce zachytávání paketů Network Watcher můžete iniciovat a spravovat relace zachycení na virtuálních počítačích Azure z portálu, PowerShellu, rozhraní příkazového řádku a programově prostřednictvím sady SDK a REST API. Zachytávání paketů umožňuje řešit scénáře, které vyžadují data na úrovni paketů, poskytováním informací v snadno použitelném formátu. Využití volně dostupných nástrojů pro kontrolu dat vám umožní prozkoumat komunikaci odesílaná do a z vašich virtuálních počítačů a získat přehled o provozu v síti. Mezi příklady použití dat zachytávání paketů patří: zkoumání problémů se sítí nebo aplikacemi, zjišťování zneužití sítě a pokusů o vniknutí nebo zachování dodržování předpisů. V tomto článku se dozvíte, jak otevřít soubor zachycení paketů, který poskytuje Network Watcher s využitím oblíbeného nástroje open source. Nabídneme vám také příklady, jak vypočítat latenci připojení, identifikovat neobvyklý provoz a prozkoumávat statistiky sítě.

## <a name="before-you-begin"></a>Než začnete

Tento článek prochází některými předkonfigurovanými scénáři při zachytávání paketů, který byl spuštěn dříve. Tyto scénáře ilustrují možnosti, ke kterým lze získat pøístup pomocí kontroly zachytávání paketů. Tento scénář používá nástroj [Wireshark](https://www.wireshark.org/) ke kontrole zachytávání paketů.

Tento scénář předpokládá, že jste už spustili zachytávání paketů na virtuálním počítači. Informace o tom, jak vytvořit zachytávání paketů, najdete v tématu Správa zachytávání paketů pomocí [portálu](network-watcher-packet-capture-manage-portal.md) nebo se zbytkem v tématu [Správa zachytávání paketů pomocí REST API](network-watcher-packet-capture-manage-rest.md).

## <a name="scenario"></a>Scénář

V tomto scénáři:

* Kontrola zachytávání paketů

## <a name="calculate-network-latency"></a>Výpočet latence sítě

V tomto scénáři ukážeme, jak zobrazit prvotní dobu odezvy (RTT) konverzace protokolu TCP (Transmission Control Protocol), která se vyskytuje mezi dvěma koncovými body.

Po navázání připojení TCP se první tři pakety odeslané v rámci připojení řídí vzorem, který se běžně označuje jako Třícestný handshake. Kontrolou prvních dvou paketů odeslaných v této signalizaci, počáteční požadavek od klienta a odpověď ze serveru můžeme vypočítat latenci při navázání připojení. Tato latence je označována jako čas odezvy (RTT). Další informace o protokolu TCP a prostředcích metody handshake najdete v následujících zdrojích informací. [https://support.microsoft.com/en-us/help/172983/explanation-of-the-three-way-handshake-via-tcp-ip](https://support.microsoft.com/en-us/help/172983/explanation-of-the-three-way-handshake-via-tcp-ip)

### <a name="step-1"></a>Krok 1

Spustit nástroj WireShark

### <a name="step-2"></a>Krok 2

Načtěte soubor **. Cap** z vašeho zachytávání paketů. Tento soubor najdete v objektu blob, který se uložil místně na virtuálním počítači v závislosti na tom, jak jste ho nakonfigurovali.

### <a name="step-3"></a>Krok 3

Chcete-li zobrazit počáteční dobu odezvy v konverzaci TCP, prohlížíme jenom první dva pakety, které jsou součástí metody handshake protokolu TCP. První dva pakety budeme používat v třícestných handshakích, což jsou pakety [SYN], [SYN, ACK]. Jsou pojmenovány pro příznaky nastavené v hlavičce protokolu TCP. V tomto scénáři se nepoužije poslední paket v paketu handshake, paketu [ACK]. Klient nástroje odesílá paket [SYN]. Jakmile se dodrží, server odešle paket [ACK] jako potvrzení přijetí SYN od klienta. Využitím faktu, že odezva serveru vyžaduje velmi nízkou režii, vypočítáme čas RTT odečtením času, kdy klient přijal paket [SYN, ACK], časem, kdy klient odeslal paket s časem [SYN].

Pomocí WireShark se tato hodnota počítá pro nás.

Pro snazší zobrazení prvních dvou paketů v rámci třícestných handshakí protokolu TCP využijeme funkce filtrování, které poskytuje nástroj WireShark.

Chcete-li použít filtr v nástroji WireShark, rozbalte segment přenosového protokolu (Transmission Control Protocol) paketu [SYN] v záznamu a prověřte příznaky nastavené v hlavičce protokolu TCP.

Vzhledem k tomu, že se chystáme filtrovat všechny pakety [SYN] a [SYN, ACK], v části Příznaky potvrďte, že je bit syn nastavený na hodnotu 1, a pak klikněte pravým tlačítkem na bit syn > použít jako filtr-> vybrány.

![Obrázek 7][7]

### <a name="step-4"></a>Krok 4

Teď, když jste okno vyfiltroval, aby se zobrazily jenom pakety s nastaveným bitem [SYN], můžete snadno vybrat konverzace, které vás zajímají, a zobrazit počáteční čas RTT. Jednoduchý způsob, jak zobrazit čas v nástroji WireShark, stačí kliknout na rozevírací seznam s označením analýza SEQ/ACK. Zobrazí se zobrazená doba odezvy. V tomto případě byla čas RTT 0,0022114 sekund nebo 2,211 MS.

![Obrázek 8][8]

## <a name="unwanted-protocols"></a>Nechtěné protokoly

V instanci virtuálního počítače, kterou jste nasadili v Azure, můžete mít spuštěný spousta aplikací. Mnohé z těchto aplikací komunikují přes síť, třeba bez výslovného svolení. Pomocí zachycení paketů k ukládání síťové komunikace můžeme prozkoumat, jak aplikace mluví na síti a hledat případné problémy.

V tomto příkladu si projdeme předchozí spuštěné zachytávání paketů pro nechtěné protokoly, které mohou ukazovat na neoprávněnou komunikaci z aplikace spuštěné na vašem počítači.

### <a name="step-1"></a>Krok 1

Použití stejného zachycení v předchozím scénáři klikněte na   >  **hierarchie protokolu** statistiky.

![Nabídka hierarchie protokolu][2]

Zobrazí se okno hierarchie protokolu. Toto zobrazení uvádí seznam všech protokolů, které se během relace zachycení používaly, a počet paketů odeslaných a přijatých pomocí protokolů. Toto zobrazení může být užitečné při hledání nežádoucího síťového provozu na virtuálních počítačích nebo v síti.

![otevřená hierarchie protokolu][3]

Jak vidíte na následujícím snímku obrazovky, probíhal provoz pomocí protokolu BitTorrent, který se používá pro sdílení souborů peer-to-peer. Jako správce neočekáváte, aby se na těchto konkrétních virtuálních počítačích zobrazil provoz BitTorrent. Nyní si o tomto provozu můžete odebrat partnerský software, který je nainstalovaný na tomto virtuálním počítači, nebo zablokovat provoz pomocí skupin zabezpečení sítě nebo brány firewall. Kromě toho se můžete rozhodnout spustit zachytávání paketů podle plánu, abyste mohli pravidelně kontrolovat používání protokolu na virtuálních počítačích. Příklad automatizace síťových úloh v Azure najdete v tématu [monitorování síťových prostředků pomocí Azure Automation](network-watcher-monitor-with-azure-automation.md) .

## <a name="finding-top-destinations-and-ports"></a>Hledání hlavních cílů a portů

Porozumění typům provozu, koncovým bodům a portům, které jsou předávány, je důležité při monitorování nebo řešení potíží s aplikacemi a prostředky ve vaší síti. Pomocí souboru zachycení paketů z výše uvedeného se můžeme rychle naučit nejdůležitější cíle, se kterými náš virtuální počítač komunikuje, a porty, které se využívají.

### <a name="step-1"></a>Krok 1

Pomocí stejného zachycení v předchozím scénáři klikněte na **Statistika**  >  **IPv4**  >  **cíle a porty** .

![okno zachytávání paketů][4]

### <a name="step-2"></a>Krok 2

Jak se podíváme na výsledky, na které řádek stojí, bylo jich na portu 111 víc. Nejčastěji používaný port byl 3389, což je vzdálená plocha a zbývající jsou dynamické porty RPC.

I když tento provoz nemusí znamenat nic, jedná se o port, který se použil pro mnoho připojení a který je pro správce neznámý.

![Obrázek 5][5]

### <a name="step-3"></a>Krok 3

Teď, když jsme zjistili, že jsme neumístili port, můžeme na základě portu vyfiltrovat naše zachycení.

Filtr v tomto scénáři by byl:

```
tcp.port == 111
```

Do textového pole filtru zadejte text filtru a stiskněte ENTER.

![Obrázek 6][6]

Z výsledků můžeme vidět, že veškerý provoz pochází z místního virtuálního počítače ve stejné podsíti. Pokud stále nerozumíme tomu, proč k tomuto provozu dochází, můžeme dále zkontrolovat pakety a zjistit, proč tato volání provádějí na portu 111. S těmito informacemi můžeme provést příslušnou akci.

## <a name="next-steps"></a>Další kroky

Další informace o dalších diagnostických funkcích Network Watcher najdete v tématu [Přehled monitorování sítě Azure](network-watcher-monitoring-overview.md) .

[1]: ./media/network-watcher-deep-packet-inspection/figure1.png
[2]: ./media/network-watcher-deep-packet-inspection/figure2.png
[3]: ./media/network-watcher-deep-packet-inspection/figure3.png
[4]: ./media/network-watcher-deep-packet-inspection/figure4.png
[5]: ./media/network-watcher-deep-packet-inspection/figure5.png
[6]: ./media/network-watcher-deep-packet-inspection/figure6.png
[7]: ./media/network-watcher-deep-packet-inspection/figure7.png
[8]: ./media/network-watcher-deep-packet-inspection/figure8.png













