---
title: Oprávnění Azure RBAC potřebná k používání funkcí
titleSuffix: Azure Network Watcher
description: Zjistěte, jaká oprávnění řízení přístupu na základě role Azure jsou nutná pro práci s funkcemi Network Watcher.
services: network-watcher
documentationcenter: ''
author: damendo
ms.service: network-watcher
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: 47418b9c5235255ff7dbf4a1a151e51e4c9aba58
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98019665"
---
# <a name="azure-role-based-access-control-permissions-required-to-use-network-watcher-capabilities"></a>Oprávnění řízení přístupu na základě role v Azure, která jsou nutná k použití možností Network Watcher

Řízení přístupu na základě role v Azure (Azure RBAC) umožňuje přiřadit jenom konkrétní akce členům vaší organizace, které vyžadují, aby se jejich přiřazené zodpovědnosti dokončily. Pokud chcete používat funkce Network Watcher, účet, ke kterému se přihlašujete do Azure, musí být přiřazený k předdefinovaným rolím [vlastník](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#owner), [přispěvatele](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#contributor)nebo [přispěvatele sítě](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#network-contributor) nebo přiřazený k [vlastní roli](../role-based-access-control/custom-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) , která je přiřazená k akcím uvedeným u jednotlivých funkcí Network Watcher v následujících oddílech. Další informace o možnostech Network Watcher najdete v tématu [co je Network Watcher?](network-watcher-monitoring-overview.md).

## <a name="network-watcher"></a>Network Watcher

| Akce                                                              | Popis                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft. Network/networkWatchers/Read                              | Získat sledovací proces sítě                                          |
| Microsoft. Network/networkWatchers/Write                             | Vytvoření nebo aktualizace sledovacího procesu sítě                             |
| Microsoft. Network/networkWatchers/DELETE                            | Odstranit sledovací proces sítě                                       |

## <a name="nsg-flow-logs"></a>Protokoly toku NSG

| Akce                                                              | Popis                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft. Network/networkWatchers/configureFlowLog/Action           | Konfigurace protokolu toku                                           |
| Microsoft. Network/networkWatchers/queryFlowLogStatus/Action         | Dotaz na stav protokolu toku                                    |

## <a name="connection-troubleshoot"></a>Řešení potíží s připojením

| Akce                                                              | Popis                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft. Network/networkWatchers/connectivityCheck/Action          | Zahájit test řešení potíží s připojením
| Microsoft. Network/networkWatchers/queryTroubleshootResult/Action    | Výsledky dotazu při řešení potíží s připojením                |
| Microsoft. Network/networkWatchers/Troubleshoot/Action               | Spustit test řešení potíží s připojením                             |

## <a name="connection-monitor"></a>Monitorování připojení

| Akce                                                              | Popis                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft. Network/networkWatchers/connectionMonitors/Start/Action   | Spustit monitorování připojení                                     |
| Microsoft. Network/networkWatchers/connectionMonitors/stop/Action    | Zastavit monitorování připojení                                      |
| Microsoft. Network/networkWatchers/connectionMonitors/Query/Action   | Dotaz na monitorování připojení                                     |
| Microsoft. Network/networkWatchers/connectionMonitors/Read           | Získat monitorování připojení                                       |
| Microsoft. Network/networkWatchers/connectionMonitors/Write          | Vytvoření monitorování připojení                                    |
| Microsoft. Network/networkWatchers/connectionMonitors/DELETE         | Odstranit monitorování připojení                                    |

## <a name="packet-capture"></a>Zachycení paketů

| Akce                                                              | Popis                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft. Network/networkWatchers/packetCaptures/queryStatus/Action | Dotaz na stav zachytávání paketů                           |
| Microsoft. Network/networkWatchers/packetCaptures/stop/Action        | Zastavení zachytávání paketů                                          |
| Microsoft. Network/networkWatchers/packetCaptures/Read               | Získání zachytávání paketů                                           |
| Microsoft. Network/networkWatchers/packetCaptures/Write              | Vytvoření zachytávání paketů                                        |
| Microsoft. Network/networkWatchers/packetCaptures/DELETE             | Odstranění zachytávání paketů                                        |

## <a name="ip-flow-verify"></a>Ověření toku protokolu IP

| Akce                                                              | Popis                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft. Network/networkWatchers/ipFlowVerify/Action               | Ověření toku protokolu IP                                              |

## <a name="next-hop"></a>Další směrování

| Akce                                                              | Popis                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft. Network/networkWatchers/nextHop/Action                    | Získat další segment směrování z virtuálního počítače                                     |

## <a name="network-security-group-view"></a>Zobrazení skupin zabezpečení sítě

| Akce                                                              | Popis                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft. Network/networkWatchers/securityGroupView/Action          | Zobrazit skupiny zabezpečení                                           |

## <a name="topology"></a>Topologie

| Akce                                                              | Popis                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft. Network/networkWatchers/Topology/Action                   | Získat topologii                                                   |
| Microsoft. Network/networkWatchers/Topology/Read                     | Stejný jako výše uvedený                                                  |

## <a name="reachability-report"></a>Sestava dostupnosti

| Akce                                                              | Popis                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft. Network/networkWatchers/azureReachabilityReport/Action    | Získání sestavy dostupnosti Azure                               |


## <a name="additional-actions"></a>Další akce

Funkce Network Watcher také vyžadují následující akce:

| Akce:                                                           | Description                                                    |
| ---------                                                           | -------------                                                  |
| Microsoft. Authorization/ \* /Read                                     | Používá se k načtení přiřazení rolí Azure a definic zásad.          |
| Microsoft. Resources/Subscriptions/resourceGroups/Read               | Používá se k zobrazení výčtu všech skupin prostředků v rámci předplatného.    |
| Microsoft. Storage/storageAccounts/Read                              | Slouží k získání vlastností pro zadaný účet úložiště.   |
| Microsoft. Storage/storageAccounts/listServiceSas/Action, </br> Microsoft. Storage/storageAccounts/listAccountSas/Action, <br> Microsoft. Storage/storageAccounts/klíče listkey/Action| Slouží k načtení sdíleného přístupového podpisu (SAS), který umožňuje [zabezpečený přístup k účtu úložiště](../storage/common/storage-sas-overview.md) a zápis do účtu úložiště. |
| Microsoft. COMPUTE/virtualMachines/Read, </br> Microsoft. COMPUTE/virtualMachines/Write| Slouží k přihlášení k virtuálnímu počítači, zachytávání paketů a jeho nahrání do účtu úložiště.|
| Microsoft. COMPUTE/virtualMachines/Extensions/Read </br> Microsoft. COMPUTE/virtualMachines/Extensions/Write| Slouží ke kontrole, jestli je rozšíření Network Watcher k dispozici, a v případě potřeby ho nainstalujte. |
| Microsoft. COMPUTE/virtualMachineScaleSets/Read, </br> Microsoft. COMPUTE/virtualMachineScaleSets/Write| Slouží k přístupu k sadám škálování virtuálních počítačů, zachytávání paketů a jejich nahrání do účtu úložiště.|
| Microsoft. COMPUTE/virtualMachineScaleSets/Extensions/Read, </br> Microsoft. COMPUTE/virtualMachineScaleSets/Extensions/Write| Slouží ke kontrole, jestli je rozšíření Network Watcher k dispozici, a v případě potřeby ho nainstalujte. |
| Microsoft. Insights/alertRules/*                                     | Slouží k nastavení výstrah metrik.                                     |
| Microsoft. support/*                                                 | Slouží k vytvoření a aktualizaci lístků podpory z Network Watcher |