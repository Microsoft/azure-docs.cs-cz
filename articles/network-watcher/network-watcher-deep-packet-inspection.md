---
title: Kontroly paketů s sledovací proces sítě Azure | Microsoft Docs
description: Tento článek popisuje, jak používat sledovací proces sítě k provádění hloubkové kontroly paketů shromážděné z virtuálního počítače
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
ms.openlocfilehash: 1ad6ca4abe73336ce9ce3539fdaf2a9d7dd23fa6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23863969"
---
# <a name="packet-inspection-with-azure-network-watcher"></a>Kontroly paketů s sledovací proces sítě Azure

Pomocí paketu funkci zachycení sledovací proces sítě, můžete zahájit a spravovat zachycení relace na virtuální počítače Azure z portálu, prostředí PowerShell, rozhraní příkazového řádku a programově pomocí sady SDK a REST API. Zachytáváním paketů umožňuje adresu scénáře, které vyžadují dat na úrovni paketů tím, že poskytuje informace ve snadno použitelného formátu. Využívání volně dostupných nástrojů k kontrolovat data, můžete prověřit komunikace odesílané do a z virtuálních počítačů a získáte přehled o síťový provoz. Některé příklad použití paketu zachycení dat patří: příčin problémů sítě nebo aplikace, zjišťování sítě pokusy o zneužití a narušení nebo udržování dodržování legislativní předpisů. V tomto článku jsme ukazují, jak otevřít soubor zachytávání paketů poskytuje sledovací proces sítě pomocí nástroje populární open source. Poskytujeme také příklady znázorňující způsob výpočtu latence připojení, identifikovat neobvyklé provoz a zkontrolujte síťových statistiky.

## <a name="before-you-begin"></a>Než začnete

Tento článek probírá některé scénáře předem nakonfigurovaná na zachytáváním paketů, která byla dříve spuštěna. Tyto scénáře ukazují možnosti, které jsou přístupné kontrolou zachytáváním paketů. Tento scénář používá [WireShark](https://www.wireshark.org/) kontrola zachytáváním paketů.

Tento scénář předpokládá, že jste již spustili zachytáváním paketů na virtuálním počítači. Další informace o vytvoření návštěvu zachytávání paketů [zachytávali portálu spravovat pakety](network-watcher-packet-capture-manage-portal.md) nebo se zbytkem navštivte stránky [Správa Zachytávali pakety REST API](network-watcher-packet-capture-manage-rest.md).

## <a name="scenario"></a>Scénář

V tomto scénáři můžete:

* Zkontrolujte zachytávání paketů

## <a name="calculate-network-latency"></a>Vypočítat latence sítě

V tomto scénáři ukážeme, jak zobrazit čas počáteční odezvy (požadavku) ke kterým dochází mezi dva koncové body konverzace protokolu TCP (Transmission Control).

Po vytvoření připojení protokolu TCP, postupujte podle prvních tří paketů odeslaných za připojení vzor obvykle označuje jako třícestný handshake. Prověřením první dva paketů odeslaných za této metody handshake počáteční požadavek od klienta a na odpověď od serveru jsme můžete vypočítat latence při toto připojení bylo vytvořeno. Tato čekací doba se označuje jako čas odezvy (požadavku). Další informace o protokolu TCP a třícestné naleznete v následujícím zdroji. https://support.microsoft.com/en-US/Help/172983/Explanation-of-the-three-way-handshake-VIA-TCP-IP

### <a name="step-1"></a>Krok 1

Spusťte WireShark

### <a name="step-2"></a>Krok 2

Zatížení **CAP** soubor z vaší zachytáváním paketů. Tento soubor nachází v objektu blob se uloží do našich místně na virtuálním počítači, v závislosti na tom, jak jste nakonfigurovali.

### <a name="step-3"></a>Krok 3

Počáteční čas odezvy (požadavku) v TCP konverzace zobrazíte jsme bude pouze vidí první dva pakety zahrnutých v TCP handshake. Použijeme v třícestné, první dva pakety, které jsou [SYN], [SYN, ACK] paketů. Že jsou pojmenované pro příznaky, které jsou nastaveny v hlavičce protokolu TCP. V tomto scénáři se nepoužijí poslední paketů v handshake paket [ACK]. Klient odešle paket [SYN]. Po přijetí je server odešle paket [ACK] jako potvrzení přijetí SYN z klienta. Využití fakt, že odpověď serveru vyžaduje minimální režii, jsme vypočítat požadavku odečtením čas [SYN, ACK] byl přijat paket klienta podle času [SYN] byl odeslán paket klientem.

Pomocí WireShark tato hodnota je vypočítána pro nás.

První dva pakety snadno zobrazit v třícestné TCP, jsme bude využívat funkce pro filtrování poskytované WireShark.

Pokud chcete použít filtr v WireShark, rozbalte položku "Transmission Control Protocol" segmentu paketu [SYN] na vašem snímku a prozkoumat příznaky nastaveny v hlavičce protokolu TCP.

Vzhledem k tomu, že chceme filtrujte všechny [SYN] a [SYN, ACK] paketů, v části Příznaky cofirm, že je bit Syn nastaven na hodnotu 1, a potom klikněte pravým tlačítkem na Syn bit -> použít jako filtr -> vybrané.

![Obrázek 7][7]

### <a name="step-4"></a>Krok 4

Teď, když jste provedli filtrování okna pouze zobrazíte pakety s nastaveným bitem [SYN], můžete snadno vybrat konverzace, které vás zajímají zobrazíte počáteční požadavku. Jednoduchý způsob, jak zobrazit požadavku v WireShark jednoduše klikněte na rozevírací nabídce, označen "SEQ/ACK" analysis. Zobrazí se požadavku zobrazí. V takovém případě požadavku byla 0.0022114 sekund nebo 2.211 ms.

![Obrázek 8][8]

## <a name="unwanted-protocols"></a>Nežádoucí protokoly

Můžete mít mnoho aplikací běžících na instanci virtuálního počítače, které jste nasadili v Azure. Mnoho z těchto aplikací komunikují přes síť, možná bez explicitního oprávnění. Uložení síťovou komunikaci pomocí zachytáváním paketů, jsme prozkoumat jak aplikace mluvíme v síti a vyhledejte všechny problémy.

V tomto příkladu jsme Zkontrolujte předchozí spustili zachytáváním paketů pro nežádoucí protokoly, které můžou signalizovat neověřené komunikace z aplikace běžící na vašem počítači.

### <a name="step-1"></a>Krok 1

Pomocí stejné zachycení v předchozím scénáři, klikněte na **statistiky** > **protokol hierarchie**

![protokol hierarchie nabídky][2]

Zobrazí se okno hierarchie protokolu. Toto zobrazení obsahuje seznam všech protokolů, které byly používány během relaci zachytávání a počet paketů odeslaných a přijatých pomocí protokolů. Toto zobrazení může být užitečné pro hledání nežádoucí síťový provoz na virtuální počítače nebo v síti.

![protokol hierarchie otevřít][3]

Jak vidíte na následujícím snímku obrazovky, se přenosů dat pomocí BitTorrent protokol, který se používá pro sdílení souborů typu peer to peer. Jako správce neočekáváte najdete v části BitTorrent provozu na tomto konkrétní virtuální počítače. Nyní si vědom tento provoz, můžete odebrat typu peer to peer software, který nainstalovat na tomto virtuálním počítači, nebo blokování provozu pomocí skupin zabezpečení sítě nebo brána Firewall. Kromě toho může zvolit paketu zachycení podle plánu, aby do použití protokolu na virtuálních počítačích můžete zkontrolovat pravidelně. Příklad o tom, jak automatizovat úlohy sítě v azure najdete v článku [monitorování síťových prostředků pomocí azure automation](network-watcher-monitor-with-azure-automation.md)

## <a name="finding-top-destinations-and-ports"></a>Hledání hlavní cíle a porty

Principy typů přenosů, koncových bodů a porty přenášená přes je důležité při sledování nebo řešení potíží s aplikací a prostředkům v síti. Použití souboru zachytávání paketů z výše, jsme můžete rychle zjistěte hlavní cíle, které naše počítač komunikuje s a porty využívání.

### <a name="step-1"></a>Krok 1

Pomocí stejné zachycení v předchozím scénáři, klikněte na **statistiky** > **IPv4 statistiky** > **cíle a porty**

![okno zachytávání paketů][4]

### <a name="step-2"></a>Krok 2

Jak jsme projděte výsledků, které vystupoval řádek, nebyly k dispozici více připojení na portu 111. Nejčastěji používaných portů byla 3389, což je Vzdálená plocha a zbývající jsou dynamické porty RPC.

Tento provoz může to znamenat nic, je port, který byl použit pro mnoho připojení a neznámý správci.

![Obrázek 5][5]

### <a name="step-3"></a>Krok 3

Teď, když jsme určili out of místní port jsme můžete filtrovat naší zachycení na port.

Filtr v tomto scénáři by byl:

```
tcp.port == 111
```

Jsme do textového pole filtru zadejte text filtru z výše a stiskněte klávesu enter.

![Obrázek 6][6]

Ve výsledcích bychom můžete vidět, že veškerý provoz pochází z místního virtuálního počítače ve stejné podsíti. Pokud stále nemáte Chápeme, proč dochází k tento provoz, jsme můžete prohlédnout další paketů, chcete-li zjistit, proč v provádění těchto volání na portu 111. Tyto informace můžeme proveďte příslušnou akci.

## <a name="next-steps"></a>Další kroky

Další informace o dalších diagnostických funkcí sledovací proces sítě navštivte stránky [Přehled monitorování sítě Azure](network-watcher-monitoring-overview.md)

[1]: ./media/network-watcher-deep-packet-inspection/figure1.png
[2]: ./media/network-watcher-deep-packet-inspection/figure2.png
[3]: ./media/network-watcher-deep-packet-inspection/figure3.png
[4]: ./media/network-watcher-deep-packet-inspection/figure4.png
[5]: ./media/network-watcher-deep-packet-inspection/figure5.png
[6]: ./media/network-watcher-deep-packet-inspection/figure6.png
[7]: ./media/network-watcher-deep-packet-inspection/figure7.png
[8]: ./media/network-watcher-deep-packet-inspection/figure8.png













