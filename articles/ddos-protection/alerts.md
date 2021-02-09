---
title: Zobrazení a konfigurace výstrah DDoS Protection pro Azure DDoS Protection Standard
description: Naučte se zobrazovat a konfigurovat výstrahy DDoS Protection pro Azure DDoS Protection Standard.
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/28/2020
ms.author: yitoh
ms.openlocfilehash: 2d72027082ed2b57b28a15a736c35801ba88188c
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2021
ms.locfileid: "99832517"
---
# <a name="view-and-configure-ddos-protection-alerts"></a>Zobrazení a konfigurace upozornění ochrany před útoky DDoS

Azure DDoS Protection Standard poskytuje podrobné přehledy a vizualizace útoků pomocí DDoSch analýz. Zákazníci, kteří chrání své virtuální sítě před útoky DDoS, mají podrobnější přehled o útokech na útoky a akcích podniknutých za účelem zmírnění útoku prostřednictvím sestav o zmírnění útoků, & protokolů pro zmírnění rizik. Bohatá telemetrie se zveřejňuje prostřednictvím Azure Monitor včetně podrobných metrik během doby trvání útoku DDoS. Výstrahy je možné nakonfigurovat pro libovolnou Azure Monitor metriky, které jsou vystavené v DDoS Protection. Protokolování se dá dál integrovat s [Azure Sentinel](../sentinel/connect-azure-ddos-protection.md), Splunk (Azure Event Hubs), OMS Log Analytics a Azure Storage pro pokročilou analýzu prostřednictvím rozhraní diagnostiky Azure monitor.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Konfigurace výstrah prostřednictvím Azure Monitor
> * Konfigurace výstrah prostřednictvím portálu
> * Zobrazit výstrahy v Azure Security Center
> * Ověření a otestování výstrah

## <a name="prerequisites"></a>Požadavky

- Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.
- Než budete moct dokončit kroky v tomto kurzu, musíte nejdřív vytvořit [plán Azure DDoS Standard](manage-ddos-protection.md) a na virtuální síti musí být povolený DDoS Protection Standard.
- DDoS sleduje veřejné IP adresy přiřazené k prostředkům v rámci virtuální sítě. Pokud ve virtuální síti nemáte žádné prostředky s veřejnými IP adresami, musíte nejdřív vytvořit prostředek s veřejnou IP adresou. Pro [služby Azure](../virtual-network/virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network) (včetně nástrojů pro vyrovnávání zatížení Azure, kde jsou virtuální počítače back-end ve virtuální síti), s výjimkou prostředí Azure App Service můžete monitorovat veřejnou IP adresu všech prostředků nasazených prostřednictvím Správce prostředků (ne Classic). Pokud chcete pokračovat v tomto kurzu, můžete rychle vytvořit virtuální počítač s [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nebo [Linuxem](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) .     

## <a name="configure-alerts-through-azure-monitor"></a>Konfigurace výstrah prostřednictvím Azure Monitor

Pomocí těchto šablon budete moci konfigurovat výstrahy pro všechny veřejné IP adresy, u kterých jste povolili diagnostické protokolování. Proto aby bylo možné používat tyto šablony výstrah, budete nejprve potřebovat Log Analytics pracovní prostor s povoleným diagnostickým nastavením. Viz [zobrazení a konfigurace diagnostického protokolování DDoS](diagnostic-logging.md).

### <a name="azure-monitor-alert-rule"></a>Azure Monitor pravidlo výstrahy

Toto [Azure monitor pravidlo výstrahy](https://aka.ms/DDOSmitigationstatus) spustí jednoduchý dotaz k detekci, kdy dochází ke zmírnění aktivní DDoS. To označuje potenciální útok. Skupiny akcí lze použít k vyvolání akcí v důsledku výstrahy.

[![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Network-Security%2Fmaster%2FAzure%2520DDoS%2520Protection%2FAzure%2520Monitor%2520Alert%2520-%2520DDoS%2520Mitigation%2520Started%2FDDoSMitigationStarted.json)

### <a name="azure-monitor-alert-rule-with-logic-app"></a>Azure Monitor pravidlo výstrahy s aplikací logiky

Tato [Šablona](https://aka.ms/ddosalert) nasadí nezbytné komponenty DDoS výstrahy týkající se zmírnění rizik: Azure monitor pravidlo výstrahy, skupinu akcí a aplikaci logiky. Výsledkem procesu je e-mailová výstraha s podrobnostmi o tom, jaké IP adresy jsou v útoku, včetně informací o prostředku přidruženému k IP adrese. Vlastník prostředku se přidá jako příjemce e-mailu spolu s týmem zabezpečení. Také se provede test dostupnosti základní aplikace a výsledky se zahrnou do e-mailové výstrahy.

[![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Network-Security%2Fmaster%2FAzure%2520DDoS%2520Protection%2FDDoS%2520Mitigation%2520Alert%2520Enrichment%2FEnrich-DDoSAlert.json)

## <a name="configure-alerts-through-portal"></a>Konfigurace výstrah prostřednictvím portálu

Můžete vybrat libovolnou z dostupných metrik ochrany DDoS, abyste byli upozorněni, když dojde k aktivnímu zmírnění během útoku pomocí konfigurace výstrahy Azure Monitor. 

1. Přihlaste se k [Azure Portal](https://portal.azure.com/) a přejděte k plánu DDoS Protection.
2. V oblasti **Monitorování** vyberte **Metriky**.
3. V šedém navigačním panelu vyberte **nové pravidlo výstrahy**. 
4. Zadejte nebo vyberte vlastní hodnoty nebo zadejte následující příklady hodnot, přijměte zbývající výchozí hodnoty a pak vyberte **vytvořit pravidlo upozornění**:

    |Nastavení                  |Hodnota                                                                                               |
    |---------                |---------                                                                                           |
    | Rozsah                   | Vyberte **Vybrat prostředek**. </br> Vyberte **předplatné** , které obsahuje veřejnou IP adresu, kterou chcete protokolovat, vyberte **veřejnou IP adresu** pro **typ prostředku** a pak vyberte konkrétní veřejnou IP adresu, pro kterou chcete protokolovat metriky. </br> Vyberte **Hotovo**. | 
    | Podmínka | Vyberte **možnost vybrat podmínku**. </br> V části Název signálu vyberte **v části útok na DDoS nebo ne**. </br> V části **operátor** vyberte **větší než nebo rovno**. </br> V části **typ agregace** vyberte **Maximum**. </br> V části **prahová hodnota** zadejte *1*. V případě **útoku pod DDoS nebo** nemetriky znamená **0** znamená, že se nebudete útočit, ale **1** znamená, že jste u něj útok. </br> Vyberte **Hotovo**. | 
    | Akce | Vyberte **Přidat skupiny akcí**. </br> Vyberte **Vytvořit skupinu akcí**. </br> V části **oznámení** vyberte v části **Typ oznámení** možnost **E-mail/zpráva SMS/nabízení/hlas**. </br> Do **pole název** zadejte _MyUnderAttackEmailAlert_. </br> Klikněte na tlačítko Upravit a pak vyberte **e-mail** a tolik z následujících možností, které požadujete, a pak vyberte **OK**. </br> Vyberte **Zkontrolovat a vytvořit**. | 
    | Podrobnosti pravidla výstrahy | V části **název pravidla výstrahy** zadejte _MyDdosAlert_. |

Během několika minut detekce útoku byste měli obdržet e-mail od Azure Monitor metriky, která vypadá podobně jako na následujícím obrázku:

![Upozornění na útok](./media/manage-ddos-protection/ddos-alert.png)

Můžete si taky přečíst další informace o [konfiguraci webhooků](../azure-monitor/platform/alerts-webhooks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a [Logic Apps](../logic-apps/logic-apps-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) pro vytváření výstrah.

## <a name="view-alerts-in-azure-security-center"></a>Zobrazit výstrahy v Azure Security Center

Azure Security Center poskytuje seznam [výstrah zabezpečení](../security-center/security-center-managing-and-responding-alerts.md), s informacemi, které vám pomůžou prozkoumat a opravit problémy. Pomocí této funkce získáte jednotný přehled o výstrahách, včetně výstrah souvisejících s útokem na DDoS, a akcí provedených k tomu, aby se útok zmírnil prakticky v reálném čase.
K dispozici jsou dvě konkrétní výstrahy, které se zobrazí při detekci a zmírnění útoků DDoS:

- **Zjistil se útok DDoS pro veřejnou IP adresu**: Tato výstraha se vygeneruje, když služba DDoS Protection zjistí, že jedna z vašich veřejných IP adres je cílem útoku DDoS.
- **Útok DDoS na veřejnou IP** adresu: Tato výstraha se vygeneruje v případě, že dojde ke zmírnění útoku na veřejnou IP adresu.
Chcete-li zobrazit výstrahy, otevřete **Security Center** v Azure Portal. V části **Ochrana před hrozbami** vyberte **výstrahy zabezpečení**. Na následujícím snímku obrazovky vidíte příklad výstrah DDoS útoku.

![DDoS výstraha v Azure Security Center](./media/manage-ddos-protection/ddos-alert-asc.png)

Výstrahy obsahují obecné informace o veřejné IP adrese, která se nachází v rámci útoku, geograficky a informací o analýze hrozeb a nápravných krocích.

## <a name="validate-and-test"></a>Ověřit a otestovat

Chcete-li simulovat útok DDoS a ověřit tak výstrahy, přečtěte si téma [ověření detekce DDoS](test-through-simulations.md).

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

- Konfigurace výstrah prostřednictvím Azure Monitor
- Konfigurace výstrah prostřednictvím portálu
- Zobrazit výstrahy v Azure Security Center
- Ověření a otestování výstrah

Informace o testování a simulaci DDoS útoku najdete v průvodci simulacem testování:

> [!div class="nextstepaction"]
> [Testování prostřednictvím simulací](test-through-simulations.md)
