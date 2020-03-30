---
title: Správa azure ddos standard ochrany pomocí portálu Azure
titlesuffix: Azure Virtual Network
description: Zjistěte, jak používat azure ddos protection standard telemetrie v Azure Monitor umírněte útok.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/17/2019
ms.author: kumud
ms.openlocfilehash: 786b21e7571ed173d2da90f587a5b76d8c92a13d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279725"
---
# <a name="manage-azure-ddos-protection-standard-using-the-azure-portal"></a>Správa azure ddos standard ochrany pomocí portálu Azure

Zjistěte, jak povolit a zakázat distribuovanou ochranu proti odmítnutí služby (DDoS) a pomocí telemetrie zmírnit útok DDoS pomocí azure ddos protection standard. DDoS Protection Standard chrání prostředky Azure, jako jsou virtuální počítače, nástroje pro vyrovnávání zatížení a aplikační brány, ke kterým je přiřazena [veřejná IP adresa](virtual-network-public-ip-address.md) Azure. Další informace o standardu DDoS Protection Standard a jeho možnostech naleznete v [přehledu standardu ochrany DDoS](ddos-protection-overview.md).

Před dokončením všech kroků v tomto kurzu https://portal.azure.com se přihlaste k portálu Azure pomocí účtu přiřazeného k roli [přispěvatele sítě](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) nebo [k vlastní roli,](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) které jsou přiřazeny příslušné akce uvedené v [části Oprávnění](#permissions).

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="create-a-ddos-protection-plan"></a>Vytvoření plánu ochrany DDoS

Plán ochrany DDoS definuje sadu virtuálních sítí, které mají povolen standard ochrany DDoS napříč předplatnými. Můžete nakonfigurovat jeden plán ochrany DDoS pro vaši organizaci a propojit virtuální sítě z více předplatných se stejným plánem. Plán ochrany DDoS sám je také spojena s odběrem, které vyberete při vytváření plánu. Plán ochrany DDoS funguje napříč oblastmi a předplatnými. Příklad -můžete vytvořit plán v oblasti východ USA a odkaz na předplatné #1 ve vašem tenantovi. Stejný plán lze propojit s virtuálními sítěmi z jiných předplatných v různých oblastech, napříč vaším tenantem. Předplatné, k jehož odběru je plán přidružen, je spojeno s měsíčním opakovaným vyúčtováním plánu a poplatky za nadlimitní prodej v případě, že počet chráněných veřejných IP adres překročí 100. Další informace o cenách DDoS naleznete v [podrobnostech o cenách](https://azure.microsoft.com/pricing/details/ddos-protection/).

Vytvoření více než jednoho plánu není vyžadováno pro většinu organizací. Plán nelze přesunout mezi předplatnými. Pokud chcete změnit předplatné plánu je, budete muset [odstranit existující plán](#work-with-ddos-protection-plans) a vytvořit nový.

1. V levém horním rohu portálu Azure vyberte **Vytvořit prostředek.**
2. Vyhledejte *DDoS*. Když se ve výsledcích hledání zobrazí **plán ochrany DDoS,** vyberte ho.
3. Vyberte **Vytvořit**.
4. Zadejte nebo vyberte své vlastní hodnoty nebo zadejte nebo vyberte následující příkladové hodnoty a pak vyberte **Vytvořit**:

    |Nastavení        |Hodnota                                              |
    |---------      |---------                                          |
    |Name (Název)           | myDdosProtectionPlan                              |
    |Předplatné   | Vyberte své předplatné.                         |
    |Skupina prostředků | Vyberte **Vytvořit nový** a zadejte *myResourceGroup* |
    |Umístění       | USA – východ                                           |

## <a name="enable-ddos-for-a-new-virtual-network"></a>Povolení DDoS pro novou virtuální síť

1. V levém horním rohu portálu Azure vyberte **Vytvořit prostředek.**
2. Vyberte **Sítě** a pak vyberte **Virtuální síť**.
3. Zadejte nebo vyberte vlastní hodnoty, zadejte nebo vyberte následující příkladové hodnoty, přijměte zbývající výchozí hodnoty a pak vyberte **Vytvořit**:

    | Nastavení         | Hodnota                                                        |
    | ---------       | ---------                                                    |
    | Name (Název)            | myVirtualNetwork                                             |
    | Předplatné    | Vyberte své předplatné.                                    |
    | Skupina prostředků  | Vyberte **Použít existující**a pak **vyberte myResourceGroup** |
    | Umístění        | USA – východ                                                      |
    | Ochrana DDoS | Vyberte **Standardní** a potom v části **Ochrana DDoS**vyberte **myDdosProtectionPlan**. Vybraný plán může být ve stejném nebo jiném předplatném než virtuální síť, ale obě předplatná musí být přidružena ke stejnému tenantovi služby Azure Active Directory.|

Virtuální síť nelze přesunout do jiné skupiny prostředků nebo předplatného, pokud je pro virtuální síť povolen a povolen ddos standard. Pokud potřebujete přesunout virtuální síť s povoleným standardem DDoS, nejprve zakažte DDoS Standard, přesuňte virtuální síť a pak povolte standard DDoS. Po přesunutí se vynulují prahové hodnoty zásad automatického ladění pro všechny chráněné veřejné IP adresy ve virtuální síti.

## <a name="enable-ddos-for-an-existing-virtual-network"></a>Povolení DDoS pro existující virtuální síť

1. Vytvořte plán ochrany DDoS dokončením kroků v [části Vytvoření plánu ochrany DDoS](#create-a-ddos-protection-plan), pokud nemáte existující plán ochrany DDoS.
2. V levém horním rohu portálu Azure vyberte **Vytvořit prostředek.**
3. Do **pole Hledat prostředky, služby a dokumenty** v horní části portálu zadejte název virtuální sítě, pro kterou chcete povolit standard ochrany DDoS. Až se ve výsledcích hledání zobrazí název virtuální sítě, vyberte ji.
4. V části **NASTAVENÍ**vyberte **možnost Ochrana DDoS**.
5. Vyberte **standardní**. V části **Plán ochrany DDoS**vyberte existující plán ochrany DDoS nebo plán, který jste vytvořili v kroku 1, a pak vyberte **Uložit**. Vybraný plán může být ve stejném nebo jiném předplatném než virtuální síť, ale obě předplatná musí být přidružena ke stejnému tenantovi služby Azure Active Directory.

**Příkazy** 
- Azure CLI: [vytvoření ochrany ddos sítě az](https://docs.microsoft.com/cli/azure/network/ddos-protection?view=azure-cli-latest#az-network-ddos-protection-create)
- Powershell: [New-AzDdosProtectionPlan](https://docs.microsoft.com/powershell/module/Az.Network/New-AzDdosProtectionPlan?view=azps-2.8.0)
 

## <a name="disable-ddos-for-a-virtual-network"></a>Zakázání DDoS pro virtuální síť

1. Do **pole Hledat prostředky, služby a dokumenty** v horní části portálu zadejte název virtuální sítě, pro kterou chcete zakázat standard ochrany DDoS. Až se ve výsledcích hledání zobrazí název virtuální sítě, vyberte ji.
2. V části **NASTAVENÍ**vyberte **možnost Ochrana DDoS**.
3. V části **Plán ochrany DDoS** vyberte **Základní** a pak vyberte **Uložit**.

**Příkazy** 
- Azure CLI: [odstranění ochrany před sítí az](https://docs.microsoft.com/cli/azure/network/ddos-protection?view=azure-cli-latest#az-network-ddos-protection-delete)
- Powershell: [Remove-AzDdosProtectionPlan](https://docs.microsoft.com/powershell/module/az.network/remove-azddosprotectionplan?view=azps-3.2.0)

## <a name="work-with-ddos-protection-plans"></a>Práce s plány ochrany DDoS

1. Vyberte **Všechny služby** nahoře, vlevo od portálu.
2. Do pole **Filtr** zadejte *DDoS.* Když se ve výsledcích zobrazí **plány ochrany DDoS,** vyberte je.
3. Ze seznamu vyberte plán ochrany, který chcete zobrazit.
4. Všechny virtuální sítě přidružené k plánu jsou uvedeny.
5. Pokud chcete plán odstranit, musíte od něj nejprve oddělit všechny virtuální sítě. Pokud chcete oddělovat plán od virtuální sítě, přečtěte si informace [o zakázání ddos pro virtuální síť](#disable-ddos-for-a-virtual-network).

## <a name="configure-alerts-for-ddos-protection-metrics"></a>Konfigurace výstrah pro metriky ochrany DDoS

Můžete vybrat některou z dostupných metrik ochrany DDoS, která vás upozorní, když během útoku dojde k aktivnímu zmírnění rizika, pomocí konfigurace výstrahazure monitoru. Pokud jsou splněny podmínky, zadaná adresa obdrží e-mail s upozorněním:

1. Vyberte **Všechny služby** nahoře, vlevo od portálu.
2. Do pole **Filtr** zadejte *Monitor.* Když se ve výsledcích zobrazí **monitor,** vyberte ho.
3. V části **SDÍLENÉ SLUŽBY**vyberte **Metriky** .
4. Zadejte nebo vyberte vlastní hodnoty nebo zadejte následující ukázkové hodnoty, přijměte zbývající výchozí hodnoty a pak vyberte **OK**:

    |Nastavení                  |Hodnota                                                                                               |
    |---------                |---------                                                                                           |
    |Name (Název)                     | myDdosAlert                                                                                        |
    |Předplatné             | Vyberte předplatné obsahující veřejnou IP adresu, pro kterou chcete dostávat upozornění.        |
    |Skupina prostředků           | Vyberte skupinu prostředků obsahující veřejnou IP adresu, pro kterou chcete dostávat výstrahy.      |
    |Prostředek                 | Vyberte veřejnou IP adresu obsahující veřejnou IP adresu, pro kterou chcete dostávat výstrahy. DDoS monitoruje veřejné IP adresy přiřazené k prostředkům v rámci virtuální sítě. Pokud nemáte žádné prostředky s veřejnými IP adresami ve virtuální síti, musíte nejprve vytvořit prostředek s veřejnou IP adresou. Veřejnou IP adresu můžete sledovat u všech prostředků nasazených prostřednictvím Správce prostředků (ne klasické) uvedených ve [virtuální síti pro služby Azure](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network), s výjimkou prostředí Azure App Service Environments a Azure VPN Gateway. Chcete-li pokračovat v tomto kurzu, můžete rychle vytvořit virtuální počítač [se systémem Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nebo [Linux.](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)                   |
    |Metrika                   | Pod DDoS útokem nebo ne                                                                            |
    |Prahová hodnota                | 1 - **1** znamená, že jste pod útokem. **0** znamená, že nejste pod útokem.                         |
    |Období                   | Vyberte jakoukoli hodnotu, kterou zvolíte.                                                                   |
    |Upozornit e-mailem         | Zaškrtněte políčko.                                                                                  |
    |Další správce | Pokud nejste vlastníkem e-mailu, přispěvatelem nebo čtenářem předplatného, zadejte svou e-mailovou adresu. |

    Během několika minut od zjištění útoku obdržíte e-mail z metrik Azure Monitoru, který vypadá podobně jako na následujícím obrázku:

    ![Upozornění na útok](./media/manage-ddos-protection/ddos-alert.png)


Informace o simulaci útoku DDoS k ověření výstrahy naleznete [v tématu Ověření detekce DDoS](#validate-ddos-detection).

Další informace o [konfiguraci webhooků](../azure-monitor/platform/alerts-webhooks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a [aplikací logiky](../logic-apps/logic-apps-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) pro vytváření výstrah.

## <a name="use-ddos-protection-telemetry"></a>Použití telemetrie ochrany DDoS

Telemetrie pro útok se poskytuje prostřednictvím Azure Monitoru v reálném čase. Telemetrie je k dispozici pouze po dobu, po kterou je veřejná IP adresa zmírňující. Nevidíte telemetrie před nebo po útoku je zmírněna.

1. Vyberte **Všechny služby** nahoře, vlevo od portálu.
2. Do pole **Filtr** zadejte *Monitor.* Když se ve výsledcích zobrazí **monitor,** vyberte ho.
3. V části **SDÍLENÉ SLUŽBY**vyberte **metriky**.
4. Vyberte skupinu **Odběr** a **Prostředky,** které obsahují veřejnou IP adresu, pro kterou chcete telemetrii.
5. Vyberte **veřejnou IP adresu** pro **typ prostředku**a vyberte konkrétní veřejnou IP adresu, pro kterou chcete telemetrii.
6. Na levé straně obrazovky se zobrazí řada **dostupných metrik.** Tyto metriky, pokud je vybrána, jsou grafovány v **grafu metrikmonitorování Azure monitoru** na obrazovce přehledu.
7. Výběr typu **agregace** jako **Max**

Názvy metrik představují různé typy paketů a bajty vs pakety se základní konstrukcí názvů značek na každé metrike takto:

- **Vynechání názvu značky** (například **Příchozí pakety vynecháno DDoS):** Počet paketů vynechány nebo vydrhnuty systémem ochrany DDoS.
- **Předaný název značky** (například **Příchozí pakety předaných DDoS**): Počet paketů předaných systémem DDoS cílové virtuální ip – provoz, který nebyl filtrován.
- **Žádný název značky** (například **Příchozí pakety DDoS**): Celkový počet paketů, které byly předány do systému čištění dat – což představuje součet paketů, které byly vynechány a předány.

Chcete-li simulovat útok DDoS k ověření telemetrie, [přečtěte si informace o ověření detekce DDoS](#validate-ddos-detection).

## <a name="view-ddos-mitigation-policies"></a>Zobrazit zásady zmírňování změny zabezpečení DDoS

DDoS Protection Standard používá tři zásady automatického vyladění zmírňování (TCP SYN, TCP & UDP) pro každou veřejnou IP adresu chráněného prostředku ve virtuální síti, která má povoleno DDoS. Prahové hodnoty zásad můžete zobrazit výběrem **příchozích paketů TCP pro aktivaci paketů zmírnění DDoS** a **Příchozí udp pro aktivaci metrik zmírnění DDoS** s **typem agregace** jako "Max", jak je znázorněno na následujícím obrázku:

![Zobrazit zásady zmírnění](./media/manage-ddos-protection/view-mitigation-policies.png)

Prahové hodnoty zásad se nakonfigurují automaticky prostřednictvím profilování síťového provozu založeného na strojovém učení Azure. Pouze v případě, že je porušena prahová hodnota zásad ke zmírnění DDoS dojít pro IP adresu pod útokem.

## <a name="configure-ddos-attack-analytics"></a>Konfigurace analýzy útoků DDoS
Azure DDoS Protection standard poskytuje podrobné informace o útoku a vizualizaci pomocí Analýzy Útoků DDoS. Zákazníci chránící své virtuální sítě před útoky DDoS mají podrobný přehled o provozu útoku a akcích přijatých ke zmírnění útoku prostřednictvím zpráv o zmírnění útoku & protokoly toku ke zmírnění rizik. 

## <a name="configure-ddos-attack-mitigation-reports"></a>Konfigurace sestav zmírnění útoku DDoS
Sestavy zmírnění útoku používají data protokolu Netflow, která jsou agregována, k poskytnutí podrobných informací o útoku na váš prostředek. Kdykoli je veřejný prostředek IP napaden, generování sestavy se spustí, jakmile začne zmírnění. K dispozici bude přírůstková sestava vygenerována každých 5 minut a zpráva po zmírnění po ukončení platnosti po celou dobu zmírnění. Tím zajistíte, že v případě, že útok DDoS bude pokračovat po delší dobu, budete moci zobrazit nejnovější snímek zprávy o zmírnění rizik každých 5 minut a úplný souhrn po ukončení zmírnění útoku. 

1. Vyberte **Všechny služby** nahoře, vlevo od portálu.
2. Do pole **Filtr** zadejte *Monitor.* Když se ve výsledcích zobrazí **monitor,** vyberte ho.
3. V části **NASTAVENÍ**vyberte **Možnost Nastavení diagnostiky**.
4. Vyberte skupinu **Odběr** a **Prostředky,** které obsahují veřejnou IP adresu, kterou chcete protokolovat.
5. Vyberte **veřejnou IP adresu** pro **typ prostředku**a vyberte konkrétní veřejnou IP adresu, pro kterou chcete zaznamenávat metriky.
6. Vyberte **Zapnout diagnostiku, chcete-li shromáždit protokol DDoSMitigationReports,** a pak vyberte tolik z následujících možností, kolik potřebujete:

    - **Archivovat do účtu úložiště**: Data se zapisují do účtu Azure Storage. Další informace o této možnosti naleznete v [tématu Archivní diagnostické protokoly](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Datový proud do centra událostí**: Umožňuje příjemci protokolu vyzvednout protokoly pomocí Centra událostí Azure. Event huby umožňují integraci s Splunk nebo jinými systémy SIEM. Další informace o této možnosti najdete v [tématu Streamování diagnostických protokolů do centra událostí](../azure-monitor/platform/resource-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Odeslat do analýzy protokolů:** Zapíše protokoly do služby Azure Monitor. Další informace o této možnosti najdete [v tématu Shromažďování protokolů pro použití v protokolech Azure Monitor](../azure-monitor/platform/collect-azure-metrics-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Jak přírůstkové & zprávy o zmírnění následků útoku, zahrnují následující pole
- Útočné vektory
- Dopravní statistiky
- Důvod vynechaná pakety
- Zapojené protokoly
- 10 hlavních zdrojových zemí nebo oblastí
- Top 10 zdroj ASNs

## <a name="configure-ddos-attack-mitigation-flow-logs"></a>Konfigurace protokolů toku zmírnění útoku DDoS
Protokoly toku ke zmírnění útoku umožňují zkontrolovat zrušený provoz, předávaný provoz a další zajímavé datové body během aktivního útoku DDoS v téměř reálném čase. Můžete ingestovat konstantní proud těchto dat do systémů SIEM prostřednictvím centra událostí pro monitorování téměř v reálném čase, provádět potenciální akce a řešit potřebu vašich obranných operací. 

1. Vyberte **Všechny služby** nahoře, vlevo od portálu.
2. Do pole **Filtr** zadejte *Monitor.* Když se ve výsledcích zobrazí **monitor,** vyberte ho.
3. V části **NASTAVENÍ**vyberte **Možnost Nastavení diagnostiky**.
4. Vyberte skupinu **Odběr** a **Prostředky,** které obsahují veřejnou IP adresu, kterou chcete protokolovat.
5. Vyberte **veřejnou IP adresu** pro **typ prostředku**a vyberte konkrétní veřejnou IP adresu, pro kterou chcete zaznamenávat metriky.
6. Vyberte **Zapnout diagnostiku shromažďovat Protokol DDoSMitigationFlowLogs** a pak vyberte tolik z následujících možností, jak budete potřebovat:

    - **Archivovat do účtu úložiště**: Data se zapisují do účtu Azure Storage. Další informace o této možnosti naleznete v [tématu Archivní diagnostické protokoly](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Datový proud do centra událostí**: Umožňuje příjemci protokolu vyzvednout protokoly pomocí Centra událostí Azure. Event huby umožňují integraci s Splunk nebo jinými systémy SIEM. Další informace o této možnosti najdete v [tématu Streamování diagnostických protokolů do centra událostí](../azure-monitor/platform/resource-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Odeslat do analýzy protokolů:** Zapíše protokoly do služby Azure Monitor. Další informace o této možnosti najdete [v tématu Shromažďování protokolů pro použití v protokolech Azure Monitor](../azure-monitor/platform/collect-azure-metrics-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
1. Chcete-li zobrazit data protokolů toku na řídicím panelu Azure Analytics, můžete importovat ukázkový řídicí panel zhttps://github.com/Anupamvi/Azure-DDoS-Protection/raw/master/flowlogsbyip.zip

Protokoly toku budou mít následující pole: 
- Zdrojová IP adresa
- Cílová IP adresa
- Zdrojový port 
- Cílový port 
- Typ protokolu 
- Opatření přijatá během zmírňování



## <a name="validate-ddos-detection"></a>Ověřit detekci DDoS

Společnost Microsoft spolupracuje se společností [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) na vytvoření rozhraní, kde můžete generovat provoz proti veřejným IP adresám s podporou ochrany DDoS pro simulace. Simulace služby BreakPoint Cloud umožňuje:

- Ověření, jak ochrana DDoS Azure chrání prostředky Azure před útoky DDoS
- Optimalizujte proces reakce na incidenty v rámci útoku DDoS
- Dodržování předpisů ddos dokumentu
- Školení týmů zabezpečení sítě

## <a name="view-ddos-protection-alerts-in-azure-security-center"></a>Zobrazení výstrah ochrany DDoS v Azure Security Center

Azure Security Center poskytuje seznam [výstrah zabezpečení](/azure/security-center/security-center-managing-and-responding-alerts), s informacemi, které vám pomohou prozkoumat a napravit problémy. Pomocí této funkce získáte jednotné zobrazení výstrah, včetně výstrah souvisejících s útokem DDoS a akcí přijatých ke zmírnění útoku v blízké době.
Existují dvě konkrétní výstrahy, které se zobrazí pro všechny detekce a zmírnění útoků DDoS:

- **Útok DDoS zjištěn pro veřejnou IP adresu**: Tato výstraha je generována, když služba ochrany DDoS zjistí, že jedna z vašich veřejných IP adres je cílem útoku DDoS.
- **DDoS Attack mitigated for Public IP**: Tato výstraha je generována, když byl zmírněn útok na veřejnou IP adresu.
Pokud chcete výstrahy zobrazit, otevřete **Centrum zabezpečení** na webu Azure Portal. V části **Ochrana před hrozbami**vyberte **možnost Výstrahy zabezpečení**. Následující snímek obrazovky ukazuje příklad upozornění na útok DDoS.

![Výstraha DDoS v Centru zabezpečení Azure](./media/manage-ddos-protection/ddos-alert-asc.png)

Výstrahy zahrnují obecné informace o veřejné IP adrese, která je pod útokem, geo a informace o hrozbách a kroky nápravy.

## <a name="permissions"></a>Oprávnění

Chcete-li pracovat s plány ochrany DDoS, musí být váš účet přiřazen k roli [přispěvatele sítě](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) nebo [k vlastní](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) roli, které jsou přiřazeny příslušné akce uvedené v následující tabulce:

| Akce                                            | Name (Název)                                     |
| ---------                                         | -------------                            |
| Microsoft.Network/ddosProtectionPlans/read        | Přečtěte si plán ochrany DDoS              |
| Microsoft.Network/ddosProtectionPlans/write       | Vytvoření nebo aktualizace plánu ochrany DDoS  |
| Microsoft.Network/ddosProtectionPlans/delete      | Odstranění plánu ochrany DDoS            |
| Microsoft.Network/ddosProtectionPlans/join/action | Připojte se k plánu ochrany DDoS              |

Chcete-li povolit ochranu DDoS pro virtuální síť, musí být vašemu účtu také přiřazeny příslušné [akce pro virtuální sítě](manage-virtual-network.md#permissions).

## <a name="next-steps"></a>Další kroky

- Vytvoření a použití [zásad Azure](policy-samples.md) pro virtuální sítě