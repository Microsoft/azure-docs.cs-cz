---
title: Kontrolu paketů pomocí služby Azure Network Watcher | Dokumentace Microsoftu
description: Tento článek popisuje, jak používat Network Watcher k provádění hloubkové kontroly paketů shromážděných z virtuálního počítače
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 7b907d00-9c35-40f5-a61e-beb7b782276f
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 1c30e679b250c2d6c6b03a1fe5d70dbd06acf052
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55818358"
---
# <a name="packet-inspection-with-azure-network-watcher"></a>Kontrolu paketů pomocí služby Azure Network Watcher

Funkce zachytávání paketů služby Network Watcher můžete spustit a spravovat relace zachycení snímku na virtuálních počítačích Azure na portálu, Powershellu, rozhraní příkazového řádku a programově prostřednictvím sady SDK a rozhraní REST API. Zachytávání paketů se můžete k řešení scénářů, které vyžadují dat na úrovni paketů tím, že poskytuje informace ve snadno použitelného formátu. Využívání volně dostupných nástrojů ke kontrole dat, můžete prozkoumat komunikace do a z vašich virtuálních počítačů a získat přehled o provozu vaší sítě. Mezi příklad použití paketů zachycení dat patří: zkoumání potíží s sítě nebo aplikace, zjišťování sítě pokusy o zneužití a narušení nebo udržování dodržování legislativních předpisů. V tomto článku vám ukážeme, jak otevřít soubor zachycení paketu poskytuje sledovací proces sítě pomocí nástroje, Oblíbené open source. Poskytneme také příklady ukazující, jak vypočítat latence připojení, identifikovat abnormální provoz a zkontrolovat sítě statistiky.

## <a name="before-you-begin"></a>Před zahájením

Tento článek probírá některé předkonfigurovaných scénářů v zachytávání paketů, které bylo předchozí spuštění úspěšné. Tyto scénáře ukazují možnosti, které mohou být přístupné kontrolou zachytávání paketů. Tento scénář využívá [WireShark](https://www.wireshark.org/) ke kontrole zachytávání paketů.

Tento scénář předpokládá, že jste již spustili zachytávání paketů na virtuálním počítači. Se naučíte vytvořit návštěvu zachytávání paketů [zachycuje paketů spravovat pomocí portálu](network-watcher-packet-capture-manage-portal.md) nebo s využitím REST návštěvou [správa pomocí rozhraní REST API zaznamená paketů](network-watcher-packet-capture-manage-rest.md).

## <a name="scenario"></a>Scénář

V tomto scénáři můžete:

* Zkontrolujte zachytávání paketů

## <a name="calculate-network-latency"></a>Vypočítat latence sítě

V tomto scénáři vám ukážeme, jak chcete-li zobrazit čas počáteční odezvy (požadavku) ke kterým dochází mezi dva koncové body konverzace protokolu TCP (Transmission Control).

Po vytvoření připojení TCP, postupujte podle prvních tří pakety odeslané v připojení vzor se obvykle označuje jako třícestný handshake. Prozkoumáním první dva pakety odeslané v tomto handshake prvotní žádosti z klienta a odpověď ze serveru, můžeme vypočítat latenci, když toto připojení bylo vytvořeno. Tato čekací doba je označován jako doba odezvy (požadavku). Další informace o protokolu TCP a handshake trojcestných odkazovat následující prostředek. https://support.microsoft.com/en-us/help/172983/explanation-of-the-three-way-handshake-via-tcp-ip

### <a name="step-1"></a>Krok 1

Spusťte WireShark

### <a name="step-2"></a>Krok 2

Zatížení **CAP** souborů z vašeho zachytávání paketů. Tento soubor můžete najít v objektu blob se uloží do našich místně na virtuálním počítači, v závislosti na tom, jak jste je nakonfigurovali.

### <a name="step-3"></a>Krok 3

Chcete-li zobrazit počáteční doba odezvy (požadavku) v konverzacích TCP, bude pouze být se díváme na první dva pakety součástí metodu handshake protokolu TCP. Použijeme první dva pakety třícestné, které jsou [SYN], [SYN, ACK] paketů. Jsou pojmenovány pro nastavení příznaků v hlavičce protokolu TCP. V tomto scénáři se nepoužije poslední paketů v handshake paket [ACK]. Klient odešle paket [SYN]. Po přijetí je server odešle paket [ACK] jako potvrzení o příjmu SYN z klienta. Využívá skutečnost, že odpověď serveru vyžaduje velmi nízkou režií, výpočtu požadavku tak, že čas [SYN, ACK] byl přijat paket klienta podle času [SYN] byl odeslán paket klientem.

Pomocí WireShark tato hodnota se vypočítá pro nás.

Chcete-li zobrazit snadněji první dva pakety v třícestné TCP, jsme budou využívat filtrování funkci, kterou poskytuje WireShark.

Použijte filtr v WireShark, rozbalte segmentu "Transmission Control Protocol" [SYN] paketu na vaše snímku a zkontrolujte nastavení příznaků v hlavičce protokolu TCP.

Protože chceme filtrovat podle všech [SYN] a [SYN ACK] paketů, v části Příznaky potvrďte, že je Syn bit nastaven na hodnotu 1 a pak klikněte pravým tlačítkem na Syn bit -> použít jako filtr -> vybrané.

![Obrázek 7][7]

### <a name="step-4"></a>Krok 4

Teď, když vyfiltrování okno viděli jenom pakety s nastaveným bitem [SYN], můžete snadno vybrat konverzace, které vás zajímají zobrazíte počáteční požadavku. Jednoduchý způsob, jak zobrazit požadavku v WireShark jednoduše klikněte na rozevírací nabídku označený "SEQ/ACK" analýzy. Zobrazí se požadavku zobrazí. V takovém případě požadavku bylo 0.0022114 sekund nebo 2.211 ms.

![Obrázek 8][8]

## <a name="unwanted-protocols"></a>Nežádoucí protokoly

Může mít mnoho aplikací běžících na instanci virtuálního počítače, které jste nasadili v Azure. Mnohé z těchto aplikací komunikovat přes síť, možná bez explicitní oprávnění. K ukládání komunikaci sítě pomocí zachytávání paketů, můžete prozkoumáme jak aplikace jsou kdekoliv v síti a vyhledejte všechny problémy.

V tomto příkladu je kontrola předchozí spustili zachytávání paketů protokolů nežádoucí, jež mohou značit neověřené komunikace z aplikace běžící na vašem počítači.

### <a name="step-1"></a>Krok 1

Pomocí stejné zachycení v předchozím scénáři, klikněte na **statistiky** > **protokol hierarchie**

![protokol hierarchie nabídky][2]

Zobrazí se okno Protokol hierarchie. Toto zobrazení obsahuje seznam všech protokolů, které byly použity během relace zachycení a počet paketů odeslaných a přijatých pomocí protokolů. Tento náhled může být užitečné pro vyhledání nežádoucí síťový provoz na virtuální počítače nebo v síti.

![protokol hierarchie otevřít][3]

Jak je vidět na následujícím snímku obrazovky, došlo k provozu s využitím BitTorrent protokol, který se používá pro sdílení souborů typu peer to peer. Jako správce nepočítáte naleznete v tématu BitTorrent provoz na tuto konkrétní virtuální počítače. Nyní si vědom tento provoz, můžete odstranit typu peer to peer software nainstalovaný na tomto virtuálním počítači, nebo blokovat provoz pomocí skupiny zabezpečení sítě nebo brána Firewall. Kromě toho se můžete rozhodnout pro spustit zachytávání paketů podle plánu, abyste mohli kontrolovat použití protokolu na virtuálních počítačích pravidelně. Příklad toho, jak automatizovat sítí v azure najdete na webu [sledování síťových prostředků pomocí azure automation](network-watcher-monitor-with-azure-automation.md)

## <a name="finding-top-destinations-and-ports"></a>Hlavní zjištění cíle a porty

Principy typů přenosů, koncové body a přenášená přes porty je důležitý při monitorování a řešení potíží s aplikací a prostředků ve vaší síti. Využitím paketů zachycení souboru výše jsme můžete rychle se naučíte nejčastější cíle, které naše virtuální počítač komunikuje s a porty využívané.

### <a name="step-1"></a>Krok 1

Pomocí stejné zachycení v předchozím scénáři, klikněte na **statistiky** > **IPv4 statistiky** > **cíle a porty**

![Interval zachytávání paketů][4]

### <a name="step-2"></a>Krok 2

Když se díváme přes výsledky, které se odlišuje řádku, došlo k více připojení na portu 111. Nejpoužívanější portu byla 3389, což je Vzdálená plocha a zbývající jsou RPC dynamické porty.

Může to znamenat tento provoz nic, je port, který se použil pro mnoho připojení a není známý správci.

![Obrázek 5][5]

### <a name="step-3"></a>Krok 3

Teď, když jsme zjistili, jestli výstupního portu místě, můžeme filtrovat naše zachycení založené na portu.

Filtr v tomto scénáři by byl:

```
tcp.port == 111
```

Jsme do filtru textového pole zadejte text filtru výše a stiskněte enter.

![Obrázek 6][6]

Z výsledků vidíme, že veškerý provoz pochází z místního virtuálního počítače ve stejné podsíti. Pokud stále nemáte rozumí tomu, proč dochází k tento provoz, jsme další kontrolu paketů zjistit, proč se tato volání na portu 111. Pomocí těchto informací můžeme provést příslušnou akci.

## <a name="next-steps"></a>Další postup

Další informace o dalších diagnostických funkcí služby Network Watcher najdete [Přehled monitorování sítě Azure](network-watcher-monitoring-overview.md)

[1]: ./media/network-watcher-deep-packet-inspection/figure1.png
[2]: ./media/network-watcher-deep-packet-inspection/figure2.png
[3]: ./media/network-watcher-deep-packet-inspection/figure3.png
[4]: ./media/network-watcher-deep-packet-inspection/figure4.png
[5]: ./media/network-watcher-deep-packet-inspection/figure5.png
[6]: ./media/network-watcher-deep-packet-inspection/figure6.png
[7]: ./media/network-watcher-deep-packet-inspection/figure7.png
[8]: ./media/network-watcher-deep-packet-inspection/figure8.png













