---
title: Správa Azure DDoS ochrany standardní pomocí portálu Azure | Microsoft Docs
description: Naučte se používat Azure DDoS ochrany standardní telemetrie v Azure monitorování zmírnit útok.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/29/2018
ms.author: jdial
ms.openlocfilehash: dd094f2b9cdb9b5eb164dda2925d094cafa7cd89
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
ms.locfileid: "33895608"
---
# <a name="manage-azure-ddos-protection-standard-using-the-azure-portal"></a>Správa Azure DDoS ochrany standardní pomocí portálu Azure

Zjistěte, jak povolit a zakázat distribuovaná útok na dostupnost služby (Denial) ochrany, a zmírnit útoku DDoS s Azure DDoS ochrany standardní pomocí telemetrie. DDoS ochrany standardní chrání prostředky Azure, jako jsou virtuální počítače, nástroje pro vyrovnávání zatížení a aplikačních bran, které mají Azure [veřejnou IP adresu](virtual-network-public-ip-address.md) přiřazen. Další informace o DDoS ochrany standardní a jeho funkce, najdete v části [DDoS ochrany standardní přehled](ddos-protection-overview.md).

Před dokončením všechny kroky v tomto kurzu, přihlaste se k portálu Azure v https://portal.azure.com s účet přidělený [Přispěvatel sítě](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) role nebo [vlastní role](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) přiřazené příslušné akce uvedené v [oprávnění](#permissions).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="create-a-ddos-protection-plan"></a>Vytvořit plán ochranu DDoS

Plán ochranu DDoS definuje sadu virtuální sítě, které mají DDoS ochrany standard povolena ve předplatných. Můžete nakonfigurovat jeden plán ochranu DDoS pro vaši organizaci a propojení virtuální sítě od více předplatných do stejného plánu. Samotný plán ochranu DDoS je také přidružené k předplatnému, kterou jste vybrali při vytváření plánu. Předplatné plánu je přidružena k způsobuje měsíčních nákladů opakování pro plán, jakož i Nadlimitní poplatky, i v případě, že počet chráněných veřejné IP adresy překračuje 100. Další informace o cenách DDoS najdete v tématu [podrobnosti o cenách](https://azure.microsoft.com/pricing/details/ddos-protection/).

Vytvoření více než jeden plán není nutné pro většinu organizací. Plán nelze přesunout mezi předplatnými. Pokud chcete změnit předplatné plán plán, budete muset [odstranit existující plán](#work-with-ddos-protection-plans) a vytvořte novou.

1. Vyberte **vytvořit prostředek** v levém horním rohu portálu Azure.
2. Vyhledejte *DDoS*. Když **plán ochranu DDos** se zobrazí ve výsledcích hledání, vyberte ho.
3. Vyberte **Vytvořit**.
4. Zadejte nebo vyberte vlastní hodnoty, nebo zadejte, nebo vyberte tyto hodnoty příklad a potom vyberte **vytvořit**:

    |Nastavení        |Hodnota                                              |
    |---------      |---------                                          |
    |Název           | myDdosProtectionPlan                              |
    |Předplatné   | Vyberte své předplatné.                         |
    |Skupina prostředků | Vyberte **vytvořit nový** a zadejte *myResourceGroup* |
    |Umístění       | Východ USA                                           |

## <a name="enable-ddos-for-a-new-virtual-network"></a>Povolit DDoS pro nové virtuální sítě

1. Vyberte **vytvořit prostředek** v levém horním rohu portálu Azure.
2. Vyberte **Sítě** a pak vyberte **Virtuální síť**.
3. Zadejte nebo vyberte vlastní hodnoty, zadejte nebo vyberte tyto hodnoty příklad, přijměte zbývající výchozí hodnoty a potom vyberte **vytvořit**:

    | Nastavení         | Hodnota                                                        |
    | ---------       | ---------                                                    |
    | Název            | myVirtualNetwork                                             |
    | Předplatné    | Vyberte své předplatné.                                    |
    | Skupina prostředků  | Vyberte **použít existující**a potom vyberte **myResourceGroup** |
    | Umístění        | Východ USA                                                      |
    | Ochrana proti útoku DDos | Vyberte **standardní** a potom v části **ochrana proti útoku DDoS**, vyberte **myDdosProtectionPlan**. Plán, který jste vybrali může být v předplatném stejný nebo jiný než virtuální sítě, ale oba odběry musí být přidruženy ke stejné klienta Azure Active Directory.|

Virtuální síť nelze přesunout do jiné skupiny prostředků nebo předplatného, pokud je povoleno DDoS Standard pro virtuální síť. Pokud potřebujete přesunout virtuální síť s DDoS Standard povolené, nejdřív zakázat DDoS Standard, přesunout virtuální síť a pak povolení DDoS standard. Po přesunu se obnoví automaticky laděná zásad prahové hodnoty pro všechny chráněné veřejné IP adresy ve virtuální síti.

## <a name="enable-ddos-for-an-existing-virtual-network"></a>Povolit DDoS pro existující virtuální síť

1. Vytvořit plán ochranu DDoS pomocí kroků v [vytvořit plán ochranu DDoS](#create-a-ddos-protection-plan), pokud nemáte existující plán ochranu DDoS.
2. Vyberte **vytvořit prostředek** v levém horním rohu portálu Azure.
3. Zadejte název virtuální sítě, který chcete povolit DDoS ochrany Standard pro v **hledání prostředků, služeb a dokumentace pole** v horní části portálu. Pokud název virtuální sítě se zobrazí ve výsledcích hledání, vyberte ho.
4. Vyberte **ochrana proti útoku DDoS**v části **nastavení**.
5. Vyberte **standardní**. V části **plán ochranu DDoS**, vyberte existující plán ochranu DDoS nebo plánu, který jste vytvořili v kroku 1 a pak vyberte **Uložit**. Plán, který jste vybrali může být v předplatném stejný nebo jiný než virtuální sítě, ale oba odběry musí být přidruženy ke stejné klienta Azure Active Directory.

## <a name="disable-ddos-for-a-virtual-network"></a>Zakázat DDoS pro virtuální síť

1. Zadejte název virtuální sítě chcete zakázat DDoS ochrany standard pro v **hledání prostředků, služeb a dokumentace pole** v horní části portálu. Pokud název virtuální sítě se zobrazí ve výsledcích hledání, vyberte ho.
2. Vyberte **ochrana proti útoku DDoS**v části **nastavení**.
3. Vyberte **základní** pod **plán ochranu DDoS** a pak vyberte **Uložit**.

## <a name="work-with-ddos-protection-plans"></a>Práce se plány ochrany DDoS

1. Vyberte **všechny služby** levé horní části portálu.
2. Zadejte *DDoS* v **filtru** pole. Když **plánů ochrany DDoS** nezobrazí ve výsledcích, vyberte ho.
3. Vyberte plán ochranu, které chcete zobrazit v seznamu.
4. Jsou uvedeny všechny virtuální sítě přidružený k plánu.
5. Pokud chcete odstranit plán, musí nejprve oddělit všechny virtuální sítě z něj. Chcete-li oddělit plán z virtuální sítě, najdete v části [DDoS zakažte pro virtuální síť](#disable-ddos-for-a-virtual-network).

## <a name="configure-alerts-for-ddos-protection-metrics"></a>Konfigurace výstrah pro DDoS ochrany metriky

Můžete vybrat všechny dostupné metriky DDoS ochrany vás upozorní, když dojde active zmírnění při útoku pomocí konfigurace výstrah monitorování Azure. Pokud jsou splněny podmínky, zadaná adresa obdrží upozornění e-mailu:

1. Vyberte **všechny služby** levé horní části portálu.
2. Zadejte *monitorování* v **filtru** pole. Když **monitorování** se zobrazí ve výsledcích, vyberte ho.
3. Vyberte **metriky** pod **SDÍLENÉ služby**.
4. Zadejte, nebo vyberte vlastní hodnoty, nebo zadejte následující hodnoty příklad, přijměte zbývající výchozí hodnoty a potom vyberte **OK**:

    |Nastavení                  |Hodnota                                                                                               |
    |---------                |---------                                                                                           |
    |Název                     | myDdosAlert                                                                                        |
    |Předplatné             | Vyberte odběr, který obsahuje veřejnou IP adresu, kterou chcete přijímat výstrahy týkající se.        |
    |Skupina prostředků           | Vyberte skupinu prostředků, která obsahuje veřejnou IP adresu, kterou chcete přijímat výstrahy týkající se.      |
    |Prostředek                 | Vyberte veřejnou IP adresu, která obsahuje veřejnou IP adresu, kterou chcete přijímat výstrahy týkající se. DDoS sleduje veřejné IP adresy přiřazené k prostředkům v rámci virtuální sítě. Pokud nemáte žádné prostředky s veřejné IP adresy ve virtuální síti, musíte nejprve vytvořit prostředek s veřejnou IP adresu. Můžete monitorovat veřejnou IP adresu všechny prostředky nasazené prostřednictvím Resource Manager (ne classic) uvedené v [virtuální sítě pro služby Azure](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network), s výjimkou prostředí Azure App Service a Azure VPN Gateway. Chcete-li pokračovat v tomto kurzu, můžete rychle vytvořit [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nebo [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuálního počítače.                   |
    |Metrika                   | V části DDoS útokům nebo ne                                                                            |
    |Mezní hodnota                | 1 - **1** znamená, můžete v útoku. **0** znamená nejsou napadené.                         |
    |Období                   | Vyberte libovolnou hodnotu zvolíte.                                                                   |
    |Upozornit e-mailem         | Zaškrtněte políčko                                                                                  |
    |Další správce | Zadejte e-mailovou adresu, pokud si nejste e-mailu vlastník, Přispěvatel nebo Čtenář pro předplatné. |

    Během několika minut detekce útoku obdržíte e-mail z Azure monitorování metriky, které bude vypadat podobně jako na následujícím obrázku:

    ![Útok výstrahy](./media/manage-ddos-protection/ddos-alert.png)


Pro simulaci útoku DDoS ověření upozornění, najdete v části [ověření DDoS detekce](#validate-ddos-detection).

Můžete si také přečíst informace o [konfigurace webhooky](../monitoring-and-diagnostics/insights-webhooks-alerts.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a [aplikace logiky](../logic-apps/logic-apps-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) pro vytvoření výstrahy.

## <a name="configure-logging-for-ddos-protection-metrics"></a>Konfigurace protokolování pro DDoS ochrany metriky

1. Vyberte **všechny služby** levé horní části portálu.
2. Zadejte *monitorování* v **filtru** pole. Když **monitorování** se zobrazí ve výsledcích, vyberte ho.
3. V části **nastavení**, vyberte **nastavení pro diagnostiku**.
4. Vyberte **předplatné** a **skupiny prostředků** obsahující veřejnou IP adresu, které chcete protokolovat.
5. Vyberte **veřejnou IP adresu** pro **typ prostředku**, pak vyberte konkrétní veřejnou IP adresu chcete protokolovat metriky pro.
6. Vyberte **zapněte diagnostiku shromažďovat následující data** a potom vyberte jako mnoho z těchto možností, jak budete potřebovat:

    - **Archiv na účet úložiště**: Data se zapisují do účtu Azure Storage. Další informace o této možnosti najdete v tématu [archivu diagnostické protokoly](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Datový proud do centra událostí**: umožňuje protokol příjemce pro vyzvednutí protokolů pomocí centra událostí Azure. Služba Event hubs povolit integraci s Splunk nebo jiných systémů SIEM. Další informace o této možnosti najdete v tématu [Stream diagnostických protokolů do centra událostí](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Odeslat k analýze protokolů**: zapisuje protokoly ke službě Analýza protokolů Azure OMS. Další informace o této možnosti najdete v tématu [shromažďovat protokoly pro použití v analýzy protokolů](../log-analytics/log-analytics-azure-storage.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Pro simulaci útoku DDoS ověření protokolování, najdete v části [ověření DDoS detekce](#validate-ddos-detection).

## <a name="use-ddos-protection-telemetry"></a>Pomocí telemetrie ochrana proti útoku DDoS

Telemetrie na útok je zajišťováno prostřednictvím Azure monitorování v reálném čase. Telemetrie je k dispozici pouze po dobu, která veřejnou IP adresu je pod zmírnění dopadů. Nevidíte telemetrie před nebo po zmírnit útok.

1. Vyberte **všechny služby** levé horní části portálu.
2. Zadejte *monitorování* v **filtru** pole. Když **monitorování** se zobrazí ve výsledcích, vyberte ho.
3. Vyberte **metriky**v části **SDÍLENÉ služby**.
4. Vyberte **předplatné** a **skupiny prostředků** obsahující veřejnou IP adresu, které chcete telemetrie.
5. Vyberte **veřejnou IP adresu** pro **typ prostředku**, pak vyberte konkrétní veřejnou IP adresu chcete telemetrie.
6. Řadu **dostupné metriky** zobrazí na levé straně obrazovky. Tyto metriky, pokud vybraná, vykreslovacích v **Azure monitorování metriky grafu** na obrazovce Přehled.

Metriky názvy představovat různých typů paketů a bajtů oproti paketů, základní konstrukce názvů značky na jednotlivé metriky následujícím způsobem:

- **Název značky zahozené** (například **příchozí pakety vyřadit DDoS**): počet paketů očistí ochrany systémem DDoS jejich vyřadit.
- **Název značky předané** (například **příchozí pakety předají DDoS**): počet paketů, které jsou předávány DDoS systému do cílového umístění VIP – provoz, který nebyl nefiltruje.
- **Žádný název značky** (například **příchozí pakety DDoS**): Celkový počet paketů, které byly dodány do čištění dat systému – představující součet pakety vyřazen a předávat.

Pro simulaci útoku DDoS ověření telemetrie, najdete v části [ověření DDoS detekce](#validate-ddos-detection).

## <a name="view-ddos-mitigation-policies"></a>Zobrazit zásady zmírnění DDoS

DDoS ochrany standardní použije tři zásady automaticky laděná zmírnění (TCP SYN, TCP a UDP) pro každou veřejnou IP adresu k chráněnému prostředku ve virtuální síti, který má DDoS povolena. Prahové hodnoty zásad můžete zobrazit výběrem **TCP příchozí pakety k aktivaci zmírnění DDoS** a **UDP příchozí pakety k aktivaci DDoS zmírnění** metriky, jak je znázorněno na následujícím obrázku:

![Zásady omezení rizik zobrazení](./media/manage-ddos-protection/view-mitigation-policies.png)

Zásady prahové hodnoty jsou automaticky nakonfigurován pomocí Azure machine learning síť provoz profilace. Pouze v případě porušení zabezpečení nebyla prahovou hodnotu zásad DDoS zmírnění dojde k pro IP adresu v útoku.

## <a name="validate-ddos-detection"></a>Ověření DDoS detekce

Má spolupráci se společností Microsoft [BreakingPoint cloudu](https://www.ixiacom.com/products/breakingpoint-cloud) k sestavení rozhraní, kde můžete vytvářet přenosy dat s povoleným ochrana proti útoku DDoS veřejné IP adresy pro simulace. Simulace zarážek cloudu umožňuje:

- Ověření, jak Ochrana proti útoku DDoS Microsoft Azure chrání před útoky DDoS vašich prostředků Azure
- Optimalizace váš proces reakcí na incidenty v rámci útoku DDoS
- Dodržování předpisů DDoS dokumentu
- Cvičení týmům zabezpečení sítě

## <a name="permissions"></a>Oprávnění

Pro práci s DDoS plánů ochrany, musí mít váš účet přiřazenou k [Přispěvatel sítě](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) rolí nebo [vlastní](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) role, která je přiřazena příslušné akce uvedené v následující tabulce:

| Akce                                            | Název                                     |
| ---------                                         | -------------                            |
| Microsoft.Network/ddosProtectionPlans/read        | Číst plán ochranu DDoS              |
| Microsoft.Network/ddosProtectionPlans/write       | Vytvořit nebo aktualizovat plán ochranu DDoS  |
| Microsoft.Network/ddosProtectionPlans/delete      | Odstranit plán ochranu DDoS            |
| Microsoft.Network/ddosProtectionPlans/join/action | Připojení k plán ochranu DDoS              |

Povolit DDoS ochranu pro virtuální síť, váš účet musí také mít přiřazenou odpovídající [akce pro virtuální sítě](manage-virtual-network.md#permissions).

## <a name="next-steps"></a>Další postup

- Vytvoření a použití [Azure zásad](policy-samples.md) pro virtuální sítě