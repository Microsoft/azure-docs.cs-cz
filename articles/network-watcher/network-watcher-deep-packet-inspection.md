---
title: Kontrola paketů pomocí nástroje Azure Network Watcher | Dokumenty společnosti Microsoft
description: Tento článek popisuje, jak pomocí sledování sítě provádět hloubkovou kontrolu paketů shromážděnou z virtuálního míse.
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: 7b907d00-9c35-40f5-a61e-beb7b782276f
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 7d32043ca73e9cf810b3eab5e65cb4b42b599d18
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77152920"
---
# <a name="packet-inspection-with-azure-network-watcher"></a>Kontrola paketů pomocí nástroje Azure Network Watcher

Pomocí funkce zachytávání paketů sledovacího programu Network Watcher můžete zahájit a spravovat relace zachycení na virtuálních počítačích Azure z portálu, prostředí PowerShell, CLI a programově prostřednictvím rozhraní SDK a REST API. Sběr paketů umožňuje řešit scénáře, které vyžadují data na úrovni paketů tím, že poskytuje informace ve snadno použitelném formátu. S využitím volně dostupných nástrojů ke kontrole dat můžete prozkoumat komunikaci odeslanou do a z virtuálních počítačů a získat přehled o síťovém provozu. Mezi příklady použití dat pro sběr paketů patří: zkoumání problémů se sítí nebo aplikací, zjišťování zneužití sítě a pokusů o vniknutí nebo udržování dodržování předpisů. V tomto článku ukážeme, jak otevřít soubor pro sběr paketů poskytovaný sledovacím programem sítě pomocí oblíbeného nástroje s otevřeným zdrojovým kódem. Poskytneme také příklady, které ukazují, jak vypočítat latenci připojení, identifikovat abnormální provoz a prozkoumat statistiky sítě.

## <a name="before-you-begin"></a>Než začnete

Tento článek prochází některé předem nakonfigurované scénáře na zachycení paketu, který byl spuštěn dříve. Tyto scénáře ilustrují možnosti, ke kterým lze získat přístup kontrolou sběru paketů. Tento scénář používá [WireShark](https://www.wireshark.org/) ke kontrole sběru paketů.

Tento scénář předpokládá, že jste již spustili sběr paketů ve virtuálním počítači. Chcete-li se dozvědět, jak vytvořit sběr paketů, navštivte [možnost Spravovat zachytávání paketů pomocí portálu](network-watcher-packet-capture-manage-portal.md) nebo rest na stránce [Správa zachytávání paketů pomocí rozhraní REST API](network-watcher-packet-capture-manage-rest.md).

## <a name="scenario"></a>Scénář

V tomto scénáři:

* Kontrola zachycení paketu

## <a name="calculate-network-latency"></a>Výpočet latence sítě

V tomto scénáři ukážeme, jak zobrazit počáteční doba odezvy (RTT) konverzace protokolu TCP (Transmission Control Protocol), ke které dochází mezi dvěma koncovými body.

Při navázání připojení TCP první tři pakety odeslané v připojení postupujte podle vzoru běžně označované jako třícestný handshake. Zkoumáním prvních dvou paketů odeslaných v tomto handshake, počáteční požadavek od klienta a odpověď ze serveru, můžeme vypočítat latence při vytvoření tohoto připojení. Tato latence se označuje jako doba odezvy (RTT). Další informace o protokolu TCP a třícestné handshake naleznete v následujícím prostředku. [https://support.microsoft.com/en-us/help/172983/explanation-of-the-three-way-handshake-via-tcp-ip](https://support.microsoft.com/en-us/help/172983/explanation-of-the-three-way-handshake-via-tcp-ip)

### <a name="step-1"></a>Krok 1

Spuštění WireShark

### <a name="step-2"></a>Krok 2

Načtěte soubor **CAP** ze sběru paketů. Tento soubor lze nalézt v objektu blob byl uložen v našem místně na virtuálním počítači, v závislosti na tom, jak jste jej nakonfigurovali.

### <a name="step-3"></a>Krok 3

Chcete-li zobrazit počáteční doba odezvy (RTT) v konverzacích TCP, budeme se dívat pouze na první dva pakety, které se účastní handshake Protokolu TCP. Budeme používat první dva pakety v třícestném handshake, které jsou pakety [SYN], [SYN, ACK]. Jsou pojmenovány pro příznaky nastavené v záhlaví Protokolu TCP. Poslední paket v paketu handshake, paket [ACK], nebude v tomto scénáři použit. Paket [SYN] je odeslán klientem. Jakmile je server přijat, odešle paket [ACK] jako potvrzení o přijetí SYN od klienta. S využitím skutečnosti, že odpověď serveru vyžaduje velmi malou režii, vypočítáme RTT odečtením doby, kdy byl paket [SYN, ACK] klientem přijat v době, kdy byl paket [SYN] odeslán klientem.

Pomocí WireShark je tato hodnota vypočtena pro nás.

Chcete-li snadněji zobrazit první dva pakety v TCP třícestné handshake, budeme využívat filtrování schopnosti poskytované WireShark.

Chcete-li použít filtr v wiresharku, rozbalte segment "Transmission Control Protocol" paketu [SYN] v zachycení a zkontrolujte příznaky nastavené v hlavičce Protokolu TCP.

Vzhledem k tomu, že se snažíme filtrovat na všech [SYN] a [SYN, ACK] pakety, pod příznaky potvrzují, že syn bit je nastavena na 1, pak klikněte pravým tlačítkem myši na Syn bit -> použít jako filtr -> vybrán.

![obrázek 7][7]

### <a name="step-4"></a>Krok 4

Nyní, když jste filtrovali okno, abyste viděli pouze pakety s bitovou sadou [SYN], můžete snadno vybrat konverzace, které vás zajímají, a zobrazit počáteční RTT. Jednoduchý způsob, jak zobrazit RTT v WireShark jednoduše klikněte na rozbalovací označené "SEQ / ACK" analýzy. Poté se zobrazí rtt. V tomto případě rtt byl 0,0022114 sekundy, nebo 2.211 ms.

![Obrázek 8][8]

## <a name="unwanted-protocols"></a>Nežádoucí protokoly

Můžete mít mnoho aplikací spuštěných na instanci virtuálního počítače, kterou jste nasadili v Azure. Mnoho z těchto aplikací komunikovat v síti, možná bez vašeho výslovného svolení. Pomocí zachytávání paketů k ukládání síťové komunikace můžeme zjistit, jak aplikace mluví v síti, a hledat případné problémy.

V tomto příkladu zkontrolujeme předchozí spuštění sběru paketů pro nežádoucí protokoly, které mohou znamenat neoprávněnou komunikaci z aplikace spuštěné v počítači.

### <a name="step-1"></a>Krok 1

Použití stejného zachycení v předchozím scénáři klepněte na**položku Hierarchie protokolu** **statistiky** > 

![nabídka hierarchie protokolů][2]

Zobrazí se okno hierarchie protokolů. Toto zobrazení obsahuje seznam všech protokolů, které byly používány během relace sběru, a počtu paketů odeslaných a přijatých pomocí protokolů. Toto zobrazení může být užitečné pro hledání nežádoucích síťových přenosů ve virtuálních počítačích nebo v síti.

![otevřena hierarchie protokolů][3]

Jak můžete vidět v následujícím snímání obrazovky, tam byl provoz pomocí protokolu BitTorrent, který se používá pro peer to peer sdílení souborů. Jako správce neočekáváte, že uvidíte BitTorrent provoz na tomto konkrétním virtuálním stroji. Nyní, když jste si vědomi tohoto provozu, můžete odebrat software peer to peer, který byl nainstalován na tomto virtuálním počítači, nebo zablokovat provoz pomocí skupin zabezpečení sítě nebo brány firewall. Kromě toho se můžete rozhodnout spustit sběr paketů podle plánu, takže můžete pravidelně kontrolovat použití protokolu na virtuálních počítačích. Příklad, jak automatizovat síťové úlohy v Azure, najdete na stránce [Monitorování síťových prostředků pomocí azure automation](network-watcher-monitor-with-azure-automation.md)

## <a name="finding-top-destinations-and-ports"></a>Hledání nejlepších destinací a přístavů

Pochopení typů provozu, koncových bodů a portů komunikovaných je důležité při monitorování nebo řešení potíží s aplikacemi a prostředky v síti. S využitím souboru pro zachycení paketů shora se můžeme rychle naučit nejlepší cíle, se kterými náš virtuální virtuální modul komunikuje, a používané porty.

### <a name="step-1"></a>Krok 1

Použití stejného zachycení v předchozím scénáři klikněte **na statistika** > **IPv4 Statistika** > **cíle a porty**

![okno pro digitalizaci paketů][4]

### <a name="step-2"></a>Krok 2

Když se podíváme na výsledky, linka vyčnívá, na portu 111 bylo více spojení. Nejpoužívanější port byl 3389, což je vzdálená plocha, a zbývající jsou dynamické porty RPC.

Zatímco tento provoz může znamenat nic, je port, který byl použit pro mnoho připojení a je neznámý správce.

![Obrázek 5][5]

### <a name="step-3"></a>Krok 3

Nyní, když jsme určili mimo mísu, můžeme filtrovat naše zachycení na základě přístavu.

Filtr v tomto scénáři by byl:

```
tcp.port == 111
```

Do textového pole filtru zadáme text filtru shora a stiskneme klávesu Enter.

![Obrázek 6][6]

Z výsledků můžeme vidět veškerý provoz pochází z místního virtuálního počítače ve stejné podsíti. Pokud stále nerozumíme tomu, proč k tomuto provozu dochází, můžeme dále zkontrolovat pakety, abychom zjistili, proč tato volání provádí na portu 111. S těmito informacemi můžeme přijmout vhodná opatření.

## <a name="next-steps"></a>Další kroky

Informace o dalších diagnostických funkcích nástroje Network Watcher najdete v přehledu [monitorování sítě Azure](network-watcher-monitoring-overview.md)

[1]: ./media/network-watcher-deep-packet-inspection/figure1.png
[2]: ./media/network-watcher-deep-packet-inspection/figure2.png
[3]: ./media/network-watcher-deep-packet-inspection/figure3.png
[4]: ./media/network-watcher-deep-packet-inspection/figure4.png
[5]: ./media/network-watcher-deep-packet-inspection/figure5.png
[6]: ./media/network-watcher-deep-packet-inspection/figure6.png
[7]: ./media/network-watcher-deep-packet-inspection/figure7.png
[8]: ./media/network-watcher-deep-packet-inspection/figure8.png













