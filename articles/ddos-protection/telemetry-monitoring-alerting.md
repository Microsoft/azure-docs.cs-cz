---
title: Zobrazení a konfigurace telemetrie ochrany před útoky DDoS
description: Přečtěte si, jak zobrazit a nakonfigurovat telemetrii DDoS Protection.
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: 5c80e5e611c275c2a2262963aa0759075fca836b
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94989390"
---
# <a name="view-and-configure-ddos-protection-telemetry"></a>Zobrazení a konfigurace telemetrie ochrany před útoky DDoS

Azure DDoS Protection Standard poskytuje podrobné přehledy a vizualizace útoků pomocí DDoSch analýz. Zákazníci, kteří chrání své virtuální sítě před útoky DDoS, mají podrobnější přehled o útokech na útoky a akcích podniknutých za účelem zmírnění útoku prostřednictvím sestav o zmírnění útoků, & protokolů pro zmírnění rizik. Bohatá telemetrie se zveřejňuje prostřednictvím Azure Monitor včetně podrobných metrik během doby trvání útoku DDoS. Výstrahy je možné nakonfigurovat pro libovolnou Azure Monitor metriky, které jsou vystavené v DDoS Protection. Protokolování se dá dál integrovat s [Azure Sentinel](../sentinel/connect-azure-ddos-protection.md), Splunk (Azure Event Hubs), OMS Log Analytics a Azure Storage pro pokročilou analýzu prostřednictvím rozhraní diagnostiky Azure monitor.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Konfigurace výstrah pro metriky DDoS Protection
> * Použití telemetrie DDoS Protection
> * Zobrazit zásady zmírnění rizik v DDoS
> * Zobrazit výstrahy DDoS Protection v Azure Security Center

## <a name="prerequisites"></a>Požadavky

- Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.
- Než budete moct dokončit kroky v tomto kurzu, musíte nejdřív vytvořit [Plán Standard Protection pro Azure DDoS](manage-ddos-protection.md).

## <a name="configure-alerts-for-ddos-protection-metrics"></a>Konfigurace výstrah pro metriky DDoS Protection

Můžete vybrat libovolnou z dostupných metrik ochrany DDoS, abyste byli upozorněni, když dojde k aktivnímu zmírnění během útoku pomocí konfigurace výstrahy Azure Monitor. Po splnění podmínek načte zadaná adresa e-mail s upozorněním:

1. Vyberte **všechny služby** nahoře, vlevo na portálu.
2. Do pole **Filtr** zadejte *monitor* . Když se **monitor** zobrazí ve výsledcích, vyberte ho.
3. V části **sdílené služby** vyberte **metriky** .
4. Zadejte nebo vyberte vlastní hodnoty nebo zadejte následující příklady hodnot, přijměte zbývající výchozí hodnoty a pak vyberte **OK**:

    |Nastavení                  |Hodnota                                                                                               |
    |---------                |---------                                                                                           |
    |Název                     | Zadejte _MyDdosAlert_.                                                                                |
    |Předplatné             | Vyberte předplatné, které obsahuje veřejnou IP adresu, pro kterou chcete dostávat upozornění.        |
    |Skupina prostředků           | Vyberte skupinu prostředků obsahující veřejnou IP adresu, pro kterou chcete dostávat upozornění.      |
    |Prostředek                 | Vyberte veřejnou IP adresu obsahující veřejnou IP adresu, pro kterou chcete dostávat upozornění. DDoS sleduje veřejné IP adresy přiřazené k prostředkům v rámci virtuální sítě. Pokud ve virtuální síti nemáte žádné prostředky s veřejnými IP adresami, musíte nejdřív vytvořit prostředek s veřejnou IP adresou. Veřejnou IP adresu všech prostředků nasazených prostřednictvím Správce prostředků (ne Classic) můžete sledovat ve [službě Virtual Network pro služby Azure](../virtual-network/virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network), s výjimkou prostředí Azure App Service a Azure VPN Gateway. Pokud chcete pokračovat v tomto kurzu, můžete rychle vytvořit virtuální počítač s [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nebo [Linuxem](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) .                   |
    |Metrika                   | Vyberte **v části útok na DDoS nebo ne**.                                                                |
    |Prahová hodnota                | 1- **1** znamená útok. **0** znamená, že nejste napadeni.                         |
    |Období                   | Vyberte libovolnou hodnotu, kterou zvolíte.                                                                   |
    |Odeslat E-mail e-mailem         | Zaškrtněte políčko.                                                                                 |
    |Další správce | Pokud nejste vlastníkem e-mailu, přispěvatele nebo čtenáře pro předplatné, zadejte svou e-mailovou adresu. |

    Během několika minut detekce útoku obdržíte e-mail od Azure Monitor metriky, které vypadají podobně jako na následujícím obrázku:

    ![Upozornění na útok](./media/manage-ddos-protection/ddos-alert.png)


Chcete-li simulovat útok DDoS k ověření vaší výstrahy, přečtěte si téma [ověření detekce DDoS](test-through-simulations.md).

Můžete si taky přečíst další informace o [konfiguraci webhooků](../azure-monitor/platform/alerts-webhooks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a [Logic Apps](../logic-apps/logic-apps-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) pro vytváření výstrah.

## <a name="use-ddos-protection-telemetry"></a>Použití telemetrie DDoS Protection

Telemetrii k útoku je poskytována prostřednictvím Azure Monitor v reálném čase. Telemetrie je k dispozici jenom po dobu, po kterou je veřejná IP adresa v oblasti zmírnění rizik. Telemetrii se nezobrazuje před nebo po útoku na hrozby.

1. Vyberte **všechny služby** nahoře, vlevo na portálu.
2. Do pole **Filtr** zadejte *monitor* . Když se **monitor** zobrazí ve výsledcích, vyberte ho.
3. V části **sdílené služby** vyberte **metriky**.
4. Vyberte **předplatné** a **skupinu prostředků** , které obsahují veřejnou IP adresu, pro kterou chcete telemetrie.
5. Vyberte možnost **Veřejná IP adresa** pro **typ prostředku** a pak vyberte konkrétní veřejnou IP adresu, pro kterou chcete telemetrie.
6. Na levé straně obrazovky se zobrazí řada **dostupných metrik** . Tyto metriky, když je tato možnost vybraná, se graficky **Azure monitor v grafu metriky** na obrazovce Přehled.
7. Vyberte typ **agregace** jako **Max** .

Názvy metrik obsahují různé typy paketů a bajty vs. pakety se základní konstrukcí názvů značek na jednotlivých metrikách, a to následujícím způsobem:

- **Vynechaný název značky** (například **odhozené příchozí pakety DDoS**): počet paketů vyřazených/provedených systémem ochrany DDoS.
- **Název předané značky** (například **příchozí pakety předané DDoS**): počet paketů předaných systémem DDoS do cílové VIP – přenos, který nebyl filtrován.
- **Žádný název značky** (například **příchozí pakety DDoS**): celkový počet paketů předaných do systému čištění dat, který představuje součet vyřazených a předaných paketů.

Toto [Azure monitor pravidlo výstrahy](https://github.com/Azure/Azure-Network-Security/tree/master/Azure%20DDoS%20Protection/Azure%20Monitor%20Alert%20-%20DDoS%20Mitigation%20Started) spustí jednoduchý dotaz k detekci, kdy dochází ke zmírnění aktivní DDoS. Chcete-li simulovat útok DDoS k ověření telemetrie, přečtěte si téma [ověření detekce DDoS](test-through-simulations.md). 

## <a name="view-ddos-mitigation-policies"></a>Zobrazit zásady zmírnění rizik v DDoS

DDoS Protection Standard aplikuje tři automaticky optimalizované zásady pro zmírnění rizik (TCP SYN, TCP & UDP) pro každou veřejnou IP adresu chráněného prostředku ve virtuální síti, ve které je povolený DDoS. Prahové hodnoty zásad si můžete zobrazit tak, že vyberete  **příchozí pakety TCP, které aktivují DDoS** a **příchozí pakety UDP pro aktivaci DDoSch rizikových** metrik s typem **agregace** jako Max, jak je znázorněno na následujícím obrázku:

![Zobrazit zásady zmírňování](./media/manage-ddos-protection/view-mitigation-policies.png)

Prahové hodnoty zásad se automaticky konfigurují prostřednictvím profilace síťového provozu založeného na službě Azure Machine Learning. Pokud dojde k narušení prahové hodnoty zásad, DDoS se na útok v případě útoku na hrozby.

## <a name="view-ddos-protection-alerts-in-azure-security-center"></a>Zobrazit výstrahy DDoS Protection v Azure Security Center

Azure Security Center poskytuje seznam [výstrah zabezpečení](../security-center/security-center-managing-and-responding-alerts.md), s informacemi, které vám pomůžou prozkoumat a opravit problémy. Pomocí této funkce získáte jednotný přehled o výstrahách, včetně výstrah souvisejících s útokem na DDoS, a akcí provedených k tomu, aby se útok zmírnil prakticky v reálném čase.
K dispozici jsou dvě konkrétní výstrahy, které se zobrazí při detekci a zmírnění útoků DDoS:

- **Zjistil se útok DDoS pro veřejnou IP adresu**: Tato výstraha se vygeneruje, když služba DDoS Protection zjistí, že jedna z vašich veřejných IP adres je cílem útoku DDoS.
- **Útok DDoS na veřejnou IP** adresu: Tato výstraha se vygeneruje v případě, že dojde ke zmírnění útoku na veřejnou IP adresu.
Chcete-li zobrazit výstrahy, otevřete **Security Center** v Azure Portal. V části **Ochrana před hrozbami** vyberte **výstrahy zabezpečení**. Na následujícím snímku obrazovky vidíte příklad výstrah DDoS útoku.

![DDoS výstraha v Azure Security Center](./media/manage-ddos-protection/ddos-alert-asc.png)

Výstrahy obsahují obecné informace o veřejné IP adrese, která se nachází v rámci útoku, geograficky a informací o analýze hrozeb a nápravných krocích.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

- Konfigurace výstrah pro metriky DDoS Protection
- Použití telemetrie DDoS Protection
- Zobrazit zásady zmírnění rizik v DDoS
- Zobrazit výstrahy DDoS Protection v Azure Security Center

Pokud se chcete dozvědět, jak nakonfigurovat sestavy zmírnění útoků a protokoly toků, pokračujte k dalšímu kurzu.

> [!div class="nextstepaction"]
> [Konfigurace protokolů toků a sestav omezení rizik útoků DDoS](reports-and-flow-logs.md)