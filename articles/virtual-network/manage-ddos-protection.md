---
title: Správa Azure DDoS Protection Standard pomocí Azure Portal
titlesuffix: Azure Virtual Network
description: Naučte se, jak pomocí Azure DDoS Protection standardní telemetrie v Azure Monitor zmírnit útok.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/17/2019
ms.author: kumud
ms.openlocfilehash: 706379649b47846b5c020dc76493a98e346c4a8f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91317680"
---
# <a name="manage-azure-ddos-protection-standard-using-the-azure-portal"></a>Správa Azure DDoS Protection Standard pomocí Azure Portal

Naučte se, jak povolit a zakázat distribuovanou ochranu před útoky na DDoS (Denial of Service), a využijte telemetrii k omezení DDoS útoku pomocí Azure DDoS Protection Standard. DDoS Protection Standard chrání prostředky Azure, jako jsou virtuální počítače, nástroje pro vyrovnávání zatížení a aplikační brány, které mají přiřazenou [veřejnou IP adresu](virtual-network-public-ip-address.md) Azure. Další informace o službě DDoS Protection Standard a jejích funkcích najdete v tématu [přehled DDoS Protection úrovně Standard](ddos-protection-overview.md).

Před dokončením všech kroků v tomto kurzu se přihlaste k Azure Portal v https://portal.azure.com rámci pomocí účtu přiřazeného k roli [přispěvatele sítě](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) nebo k [vlastní roli](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , která je přiřazena k příslušným akcím uvedeným v [oprávněních](#permissions-and-restrictions).

Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-ddos-protection-plan"></a>Vytvoření plánu DDoS Protection

Plán DDoS Protection definuje sadu virtuálních sítí, které mají povolený Standard DDoS Protection v rámci předplatných. Můžete nakonfigurovat jeden plán DDoS Protection pro vaši organizaci a propojit virtuální sítě s několika předplatnými do stejného plánu. Plán DDoS Protection sám o sobě souvisí taky s předplatným, které jste vybrali během vytváření plánu. Plán DDoS Protection funguje napříč oblastmi a předplatnými. Příklad: můžete vytvořit plán v oblasti Východ USA a odkaz na předplatné #1 ve vašem tenantovi. Stejný plán může být propojený s virtuálními sítěmi z jiných předplatných v různých oblastech v rámci vašeho tenanta. K předplatnému, ke kterému je přiřazen plán, se účtují měsíční periodická faktura za plán, jakož i poplatky za překročení limitu pro případ, že počet chráněných veřejných IP adres překračuje 100. Další informace o cenách DDoS najdete v [podrobnostech o cenách](https://azure.microsoft.com/pricing/details/ddos-protection/).

Pro většinu organizací není nutné vytvářet více než jeden plán. Plán nelze přesunout mezi předplatnými. Pokud chcete změnit předplatné, ve kterém je plán, je nutné [odstranit stávající plán](#work-with-ddos-protection-plans) a vytvořit nový.

1. V levém horním rohu Azure Portal vyberte **vytvořit prostředek** .
2. Vyhledejte *DDoS*. Pokud se ve výsledcích hledání zobrazí **plán DDoS Protection** , vyberte ho.
3. Vyberte **Vytvořit**.
4. Zadejte nebo vyberte vlastní hodnoty, nebo zadejte nebo vyberte následující příklady hodnot a pak vyberte **vytvořit**:

    |Nastavení        |Hodnota                                              |
    |---------      |---------                                          |
    |Název           | myDdosProtectionPlan                              |
    |Předplatné   | Vyberte své předplatné.                         |
    |Skupina prostředků | Vyberte **vytvořit nové** a zadejte *myResourceGroup* . |
    |Umístění       | USA – východ                                           |

## <a name="enable-ddos-for-a-new-virtual-network"></a>Povolení DDoS pro novou virtuální síť

1. V levém horním rohu Azure Portal vyberte **vytvořit prostředek** .
2. Vyberte **Sítě** a pak vyberte **Virtuální síť**.
3. Zadejte nebo vyberte vlastní hodnoty, zadejte nebo vyberte následující příklady hodnot, přijměte zbývající výchozí hodnoty a pak vyberte **vytvořit**:

    | Nastavení         | Hodnota                                                        |
    | ---------       | ---------                                                    |
    | Název            | myVirtualNetwork                                             |
    | Předplatné    | Vyberte své předplatné.                                    |
    | Skupina prostředků  | Vyberte **použít existující**a pak vyberte **myResourceGroup** . |
    | Umístění        | USA – východ                                                      |
    | DDoS Protection Standard | Vyberte **Povolit**. Vybraný plán může být ve stejném nebo jiném předplatném, než je virtuální síť, ale oba odběry musí být přidružené ke stejnému Azure Active Directory tenantovi.|

Virtuální síť nejde přesunout do jiné skupiny prostředků nebo předplatného, pokud je DDoS standard pro virtuální síť povolený. Pokud potřebujete přesunout virtuální síť se zapnutou DDoS standardem, zakažte nejprve DDoS Standard, přesuňte virtuální síť a pak povolte DDoS Standard. Po přesunu se automaticky vyladěné prahové hodnoty zásad pro všechny chráněné veřejné IP adresy ve virtuální síti resetují.

## <a name="enable-ddos-for-an-existing-virtual-network"></a>Povolení DDoS pro existující virtuální síť

1. Vytvořte plán DDoS Protection tak, že vyplníte kroky v tématu [Vytvoření plánu DDoS Protection](#create-a-ddos-protection-plan), pokud nemáte existující plán DDoS Protection.
2. V levém horním rohu Azure Portal vyberte **vytvořit prostředek** .
3. Do **pole Hledat prostředky, služby a dokumenty** v horní části portálu zadejte název virtuální sítě, pro kterou chcete povolit DDoS Protection Standard. Pokud se ve výsledcích hledání zobrazí název virtuální sítě, vyberte ji.
4. V části **Nastavení**vyberte **DDoS Protection**.
5. Vyberte **Standard**. V části **plán ochrany DDoS**vyberte existující plán DDoS Protection nebo plán, který jste vytvořili v kroku 1, a pak vyberte **Uložit**. Vybraný plán může být ve stejném nebo jiném předplatném, než je virtuální síť, ale oba odběry musí být přidružené ke stejnému Azure Active Directory tenantovi.

**Příkazy** 
- Azure CLI: [AZ Network DDoS-Protection Create](https://docs.microsoft.com/cli/azure/network/ddos-protection?view=azure-cli-latest#az-network-ddos-protection-create)
- PowerShell: [New-AzDdosProtectionPlan](https://docs.microsoft.com/powershell/module/Az.Network/New-AzDdosProtectionPlan?view=azps-2.8.0)
 

## <a name="disable-ddos-for-a-virtual-network"></a>Zakázat DDoS pro virtuální síť

1. Do **pole Hledat prostředky, služby a dokumenty** v horní části portálu zadejte název virtuální sítě, pro kterou chcete zakázat DDoS Protection Standard. Pokud se ve výsledcích hledání zobrazí název virtuální sítě, vyberte ji.
2. Vyberte **v části DDoS Protection Standard**možnost **Zakázat**.

**Příkazy** 
- Azure CLI: [AZ Network DDoS-Protection Delete](https://docs.microsoft.com/cli/azure/network/ddos-protection?view=azure-cli-latest#az-network-ddos-protection-delete)
- PowerShell: [Remove-AzDdosProtectionPlan](https://docs.microsoft.com/powershell/module/az.network/remove-azddosprotectionplan?view=azps-3.2.0)

## <a name="work-with-ddos-protection-plans"></a>Práce s plány DDoS Protection

1. Vyberte **všechny služby** nahoře, vlevo na portálu.
2. Do pole **filtru** zadejte *DDoS* . Pokud se ve výsledcích zobrazí **plány ochrany DDoS Protection** , vyberte ji.
3. V seznamu vyberte plán ochrany, který chcete zobrazit.
4. Zobrazí se všechny virtuální sítě přidružené k tomuto plánu.
5. Pokud chcete plán odstranit, musíte nejdřív z něj oddělit všechny virtuální sítě. Pokud chcete zrušit přidružení plánu z virtuální sítě, přečtěte si téma [zakázání DDoS pro virtuální síť](#disable-ddos-for-a-virtual-network).

## <a name="configure-alerts-for-ddos-protection-metrics"></a>Konfigurace výstrah pro metriky DDoS Protection

Můžete vybrat libovolnou z dostupných metrik ochrany DDoS, abyste byli upozorněni, když dojde k aktivnímu zmírnění během útoku pomocí konfigurace výstrahy Azure Monitor. Po splnění podmínek načte zadaná adresa e-mail s upozorněním:

1. Vyberte **všechny služby** nahoře, vlevo na portálu.
2. Do pole **Filtr** zadejte *monitor* . Když se **monitor** zobrazí ve výsledcích, vyberte ho.
3. V části **sdílené služby**vyberte **metriky** .
4. Zadejte nebo vyberte vlastní hodnoty nebo zadejte následující příklady hodnot, přijměte zbývající výchozí hodnoty a pak vyberte **OK**:

    |Nastavení                  |Hodnota                                                                                               |
    |---------                |---------                                                                                           |
    |Název                     | myDdosAlert                                                                                        |
    |Předplatné             | Vyberte předplatné, které obsahuje veřejnou IP adresu, pro kterou chcete dostávat upozornění.        |
    |Skupina prostředků           | Vyberte skupinu prostředků obsahující veřejnou IP adresu, pro kterou chcete dostávat upozornění.      |
    |Prostředek                 | Vyberte veřejnou IP adresu obsahující veřejnou IP adresu, pro kterou chcete dostávat upozornění. DDoS sleduje veřejné IP adresy přiřazené k prostředkům v rámci virtuální sítě. Pokud ve virtuální síti nemáte žádné prostředky s veřejnými IP adresami, musíte nejdřív vytvořit prostředek s veřejnou IP adresou. Veřejnou IP adresu všech prostředků nasazených prostřednictvím Správce prostředků (ne Classic) můžete sledovat ve [službě Virtual Network pro služby Azure](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network), s výjimkou prostředí Azure App Service a Azure VPN Gateway. Pokud chcete pokračovat v tomto kurzu, můžete rychle vytvořit virtuální počítač s [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nebo [Linuxem](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) .                   |
    |Metrika                   | V části útok DDoS nebo ne                                                                            |
    |Prahová hodnota                | 1- **1** znamená útok. **0** znamená, že nejste napadeni.                         |
    |Období                   | Vyberte libovolnou hodnotu, kterou zvolíte.                                                                   |
    |Odeslat E-mail e-mailem         | Zaškrtněte políčko.                                                                                  |
    |Další správce | Pokud nejste vlastníkem e-mailu, přispěvatele nebo čtenáře pro předplatné, zadejte svou e-mailovou adresu. |

    Během několika minut detekce útoku obdržíte e-mail od Azure Monitor metriky, které vypadají podobně jako na následujícím obrázku:

    ![Upozornění na útok](./media/manage-ddos-protection/ddos-alert.png)


Chcete-li simulovat útok DDoS k ověření vaší výstrahy, přečtěte si téma [ověření detekce DDoS](#validate-ddos-detection).

Můžete si taky přečíst další informace o [konfiguraci webhooků](../azure-monitor/platform/alerts-webhooks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a [Logic Apps](../logic-apps/logic-apps-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) pro vytváření výstrah.

## <a name="use-ddos-protection-telemetry"></a>Použití telemetrie DDoS Protection

Telemetrii k útoku je poskytována prostřednictvím Azure Monitor v reálném čase. Telemetrie je k dispozici jenom po dobu, po kterou je veřejná IP adresa v oblasti zmírnění rizik. Telemetrii se nezobrazuje před nebo po útoku na hrozby.

1. Vyberte **všechny služby** nahoře, vlevo na portálu.
2. Do pole **Filtr** zadejte *monitor* . Když se **monitor** zobrazí ve výsledcích, vyberte ho.
3. V části **sdílené služby**vyberte **metriky**.
4. Vyberte **předplatné** a **skupinu prostředků** , které obsahují veřejnou IP adresu, pro kterou chcete telemetrie.
5. Vyberte možnost **Veřejná IP adresa** pro **typ prostředku**a pak vyberte konkrétní veřejnou IP adresu, pro kterou chcete telemetrie.
6. Na levé straně obrazovky se zobrazí řada **dostupných metrik** . Tyto metriky, když je tato možnost vybraná, se graficky **Azure monitor v grafu metriky** na obrazovce Přehled.
7. Vyberte typ **agregace** jako **Max** .

Názvy metrik obsahují různé typy paketů a bajty vs. pakety se základní konstrukcí názvů značek na jednotlivých metrikách, a to následujícím způsobem:

- **Vynechaný název značky** (například **odhozené příchozí pakety DDoS**): počet paketů vyřazených/provedených systémem ochrany DDoS.
- **Název předané značky** (například **příchozí pakety předané DDoS**): počet paketů předaných systémem DDoS do cílové VIP – přenos, který nebyl filtrován.
- **Žádný název značky** (například **příchozí pakety DDoS**): celkový počet paketů předaných do systému čištění dat, který představuje součet vyřazených a předaných paketů.

Chcete-li simulovat útok DDoS k ověření telemetrie, přečtěte si téma [ověření detekce DDoS](#validate-ddos-detection).

## <a name="view-ddos-mitigation-policies"></a>Zobrazit zásady zmírnění rizik v DDoS

DDoS Protection Standard aplikuje tři automaticky optimalizované zásady pro zmírnění rizik (TCP SYN, TCP & UDP) pro každou veřejnou IP adresu chráněného prostředku ve virtuální síti, ve které je povolený DDoS. Prahové hodnoty zásad si můžete zobrazit tak, že vyberete  **příchozí pakety TCP, které aktivují DDoS** a **příchozí pakety UDP pro aktivaci DDoSch rizikových** metrik s typem **agregace** jako Max, jak je znázorněno na následujícím obrázku:

![Zobrazit zásady zmírňování](./media/manage-ddos-protection/view-mitigation-policies.png)

Prahové hodnoty zásad se automaticky konfigurují prostřednictvím profilace síťového provozu založeného na službě Azure Machine Learning. Pokud dojde k narušení prahové hodnoty zásad, DDoS se na útok v případě útoku na hrozby.

## <a name="configure-ddos-attack-analytics"></a>Konfigurace analýzy útoků DDoS
Azure DDoS Protection Standard poskytuje podrobné přehledy a vizualizace útoků pomocí DDoSch analýz. Zákazníci, kteří chrání své virtuální sítě před útoky DDoS, mají podrobnější přehled o útokech na útoky a akcích podniknutých za účelem zmírnění útoku prostřednictvím sestav o zmírnění útoků, & protokolů pro zmírnění rizik. 

## <a name="configure-ddos-attack-mitigation-reports"></a>Konfigurace sestav o zmírnění útoků DDoS
Sestavy o zmírnění útoku využívají data protokolu NetFlow, která jsou agregovaná a poskytují podrobné informace o útoku na prostředek. V případě útoku na veřejné IP prostředky začne generování sestav hned po zahájení zmírnění. Pro celé období zmírnění rizika bude vygenerována přírůstková zpráva každých 5 minut a hlášení o zmírňování po zmírnění. K tomu je potřeba zajistit, aby v případě, že útok DDoS trvá delší dobu, budete moct zobrazit nejaktuálnější snímek sestavy o zmírnění každých 5 minut a kompletní souhrn po překročení omezení útoku. 

1. Vyberte **všechny služby** nahoře, vlevo na portálu.
2. Do pole **Filtr** zadejte *monitor* . Když se **monitor** zobrazí ve výsledcích, vyberte ho.
3. V části **Nastavení**vyberte **nastavení diagnostiky**.
4. Vyberte **předplatné** a **skupinu prostředků** obsahující veřejnou IP adresu, kterou chcete protokolovat.
5. Vyberte možnost **Veřejná IP adresa** pro **typ prostředku**a pak vyberte konkrétní veřejnou IP adresu, pro kterou chcete metriky protokolovat.
6. Vyberte **zapnout diagnostiku pro shromáždění protokolu DDoSMitigationReports** a pak vyberte tolik z následujících možností, kolik budete potřebovat:

    - **Archivace do účtu úložiště**: data se zapisují do účtu Azure Storage. Další informace o této možnosti najdete v tématu [archivní protokoly prostředků](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Streamování do centra událostí**: umožňuje přijímači protokolu vybírat protokoly pomocí centra událostí Azure. Centra událostí umožňují integraci s Splunk nebo jinými systémy SIEM. Další informace o této možnosti najdete v tématu [streamování protokolů prostředků do centra událostí](../azure-monitor/platform/resource-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Odeslat do Log Analytics**: zapisuje protokoly do služby Azure monitor. Další informace o této možnosti najdete v tématu [shromáždění protokolů pro použití v protokolech Azure monitor](../azure-monitor/platform/collect-azure-metrics-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Mezi přírůstkové & sestavy o zmírnění útoků po útoku patří následující pole:
- Vektory útoku
- Statistika provozu
- Důvod pro vyřazené pakety
- Zapojené protokoly
- Prvních 10 zdrojových zemí nebo oblastí
- Prvních 10 zdrojů čísla ASN

## <a name="configure-ddos-attack-mitigation-flow-logs"></a>Konfigurace protokolů toku pro zmírnění útoků DDoS
Protokoly o omezeních toků útoků na útoky umožňují kontrolovat vyřazený provoz, předávané přenosy a další zajímavé databody v rámci aktivního DDoS útoku téměř v reálném čase. Konstantní datový proud těchto dat můžete ingestovat do systémů SIEM prostřednictvím centra událostí pro monitorování téměř v reálném čase, provádět potenciální akce a řešit nutnost operací obrany. 

1. Vyberte **všechny služby** nahoře, vlevo na portálu.
2. Do pole **Filtr** zadejte *monitor* . Když se **monitor** zobrazí ve výsledcích, vyberte ho.
3. V části **Nastavení**vyberte **nastavení diagnostiky**.
4. Vyberte **předplatné** a **skupinu prostředků** obsahující veřejnou IP adresu, kterou chcete protokolovat.
5. Vyberte možnost **Veřejná IP adresa** pro **typ prostředku**a pak vyberte konkrétní veřejnou IP adresu, pro kterou chcete metriky protokolovat.
6. Vyberte **zapnout diagnostiku pro shromáždění protokolu DDoSMitigationFlowLogs** a pak vyberte tolik z následujících možností, kolik budete potřebovat:

    - **Archivace do účtu úložiště**: data se zapisují do účtu Azure Storage. Další informace o této možnosti najdete v tématu [archivní protokoly prostředků](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Streamování do centra událostí**: umožňuje přijímači protokolu vybírat protokoly pomocí centra událostí Azure. Centra událostí umožňují integraci s Splunk nebo jinými systémy SIEM. Další informace o této možnosti najdete v tématu [streamování protokolů prostředků do centra událostí](../azure-monitor/platform/resource-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Odeslat do Log Analytics**: zapisuje protokoly do služby Azure monitor. Další informace o této možnosti najdete v tématu [shromáždění protokolů pro použití v protokolech Azure monitor](../azure-monitor/platform/collect-azure-metrics-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
1. Pokud chcete zobrazit data protokolů toků v Azure Analytics, můžete si ukázkový řídicí panel naimportovat z https://github.com/Azure/Azure-Network-Security/tree/master/Azure%20DDoS%20Protection/Azure%20DDoS%20Protection%20Workbook

Protokoly toku budou obsahovat následující pole: 
- Zdrojová IP adresa
- Cílová IP adresa
- Zdrojový port 
- Cílový port 
- Typ protokolu 
- Akce prováděná během zmírnění

Analýza útoků bude fungovat jenom v případě, že je ve virtuální síti veřejné IP adresy povolený DDoS Protection Standard. 

## <a name="validate-ddos-detection"></a>Ověřit detekci DDoS

Microsoft spolupracuje s [cloudem BreakingPoint](https://www.ixiacom.com/products/breakingpoint-cloud) a vytváří rozhraní, ve kterém můžete vygenerovat provoz proti DDoS Protection veřejné IP adresy pro simulace. Simulace cloudu zarážky umožňuje:

- Ověření, jak Microsoft Azure DDoS Protection chrání vaše prostředky Azure před útoky DDoS
- Optimalizace procesu reakce na incidenty při útoku DDoS
- DDoS dodržování předpisů v dokumentu
- Školení týmů zabezpečení sítě

## <a name="view-ddos-protection-alerts-in-azure-security-center"></a>Zobrazit výstrahy DDoS Protection v Azure Security Center

Azure Security Center poskytuje seznam [výstrah zabezpečení](/azure/security-center/security-center-managing-and-responding-alerts), s informacemi, které vám pomůžou prozkoumat a opravit problémy. Pomocí této funkce získáte jednotný přehled o výstrahách, včetně výstrah souvisejících s útokem na DDoS, a akcí provedených k tomu, aby se útok zmírnil prakticky v reálném čase.
K dispozici jsou dvě konkrétní výstrahy, které se zobrazí při detekci a zmírnění útoků DDoS:

- **Zjistil se útok DDoS pro veřejnou IP adresu**: Tato výstraha se vygeneruje, když služba DDoS Protection zjistí, že jedna z vašich veřejných IP adres je cílem útoku DDoS.
- **Útok DDoS na veřejnou IP**adresu: Tato výstraha se vygeneruje v případě, že dojde ke zmírnění útoku na veřejnou IP adresu.
Chcete-li zobrazit výstrahy, otevřete **Security Center** v Azure Portal. V části **Ochrana před hrozbami**vyberte **výstrahy zabezpečení**. Na následujícím snímku obrazovky vidíte příklad výstrah DDoS útoku.

![DDoS výstraha v Azure Security Center](./media/manage-ddos-protection/ddos-alert-asc.png)

Výstrahy obsahují obecné informace o veřejné IP adrese, která se nachází v rámci útoku, geograficky a informací o analýze hrozeb a nápravných krocích.

## <a name="permissions-and-restrictions"></a>Oprávnění a omezení

Aby bylo možné pracovat s plány ochrany DDoS Protection, musí být váš účet přiřazen k roli [Přispěvatel sítě](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) nebo k [vlastní](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) roli, která je přiřazena k příslušným akcím uvedeným v následující tabulce:

| Akce                                            | Name                                     |
| ---------                                         | -------------                            |
| Microsoft. Network/ddosProtectionPlans/Read        | Přečíst plán DDoS Protection              |
| Microsoft. Network/ddosProtectionPlans/Write       | Vytvořit nebo aktualizovat plán DDoS Protection  |
| Microsoft. Network/ddosProtectionPlans/DELETE      | Odstranění plánu DDoS Protection            |
| Microsoft. Network/ddosProtectionPlans/JOIN/Action | Připojit se k plánu DDoS Protection              |

Aby bylo možné povolit DDoS ochranu pro virtuální síť, musí mít váš účet také přiřazeny příslušné [akce pro virtuální sítě](manage-virtual-network.md#permissions).

### <a name="azure-policy"></a>Azure Policy
Pro zákazníky, kteří mají různá předplatná a kteří chtějí zajistit, aby byl pro řízení nákladů nasazený jeden plán pro Azure DDoS Protection Standard, můžete pomocí Azure Policy [omezit vytváření Azure DDoS Protection standardních plánů](https://github.com/Azure/Azure-Network-Security/tree/master/Azure%20DDoS%20Protection/Restrict%20creation%20of%20Azure%20DDoS%20Protection%20Standard%20Plans%20with%20Azure%20Policy). Tato zásada zablokuje vytváření jakýchkoli plánů DDoS, pokud předplatné předtím neoznačilo výjimku. V této zásadě se zobrazí také seznam všech předplatných, která mají nasazený plán DDoS, ale neměly by se jim označit jako nedodržující předpisy. 

## <a name="next-steps"></a>Další kroky

- Vytvoření a přiřazení [Azure Policy definice](policy-samples.md) pro virtuální sítě
