---
title: Oprávnění vyžadovaná používat funkce služby Azure Network Watcher | Dokumentace Microsoftu
description: Zjistěte, jaká oprávnění řízení přístupu na základě rolí Azure jsou vyžadována pro práci s funkcemi Network Watcher.
services: network-watcher
documentationcenter: ''
author: jimdial
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: network-watcher
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: jdial
ms.openlocfilehash: 429f7862901814fbd2017c395706fbfa2c345f72
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/15/2018
ms.locfileid: "53434575"
---
# <a name="role-based-access-control-permissions-required-to-use-network-watcher-capabilities"></a>Oprávnění pro řízení přístupu na základě rolí vyžaduje používat funkce služby Network Watcher

Řízení přístupu Azure na základě rolí (RBAC) umožňuje členům vaší organizace, které požadují k dokončení jejich přiřazené povinnosti přiřadit jenom na konkrétní akce. Pomocí možnosti Network Watcher, účet přihlášení do Azure, musí mít přiřazenou k [vlastníka](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#owner), [Přispěvatel](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#contributor), nebo [Přispěvatel sítě](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#network-contributor) předdefinované role, nebo přiřazeno [vlastní roli](../role-based-access-control/custom-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) , který je přiřazen akce u jednotlivých schopnostech Network Watcheru v následujících částech. Další informace o službě Network Watcher najdete v tématu [co je Network Watcher?](network-watcher-monitoring-overview.md).

## <a name="network-watcher"></a>Network Watcher

| Akce                                                              | Název                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/read                              | Získat network watcher                                          |
| Microsoft.Network/networkWatchers/write                             | Vytvořit nebo aktualizovat sledovací proces sítě                             |
| Microsoft.Network/networkWatchers/delete                            | Odstranit sledovací proces sítě                                       |

## <a name="nsg-flow-logs"></a>Protokoly toku NSG

| Akce                                                              | Název                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/configureFlowLog/action           | Konfigurace protokolů toku                                           |
| Microsoft.Network/networkWatchers/queryFlowLogStatus/action         | Stav dotazu protokolu toku                                    |

## <a name="connection-troubleshoot"></a>Řešení potíží s připojením

| Akce                                                              | Název                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/connectivityCheck/action          | Zahájení připojení k řešení potíží s testu
| Microsoft.Network/networkWatchers/queryTroubleshootResult/action    | Výsledky dotazu připojení k řešení potíží s testu                |
| Microsoft.Network/networkWatchers/troubleshoot/action               | Spuštění připojení, řešení potíží s testu                             |

## <a name="connection-monitor"></a>Monitorování připojení

| Akce                                                              | Název                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/connectionMonitors/start/action   | Spustit monitorování připojení                                     |
| Microsoft.Network/networkWatchers/connectionMonitors/stop/action    | Zastavit monitorování připojení                                      |
| Microsoft.Network/networkWatchers/connectionMonitors/query/action   | Dotaz monitorování připojení                                     |
| Microsoft.Network/networkWatchers/connectionMonitors/read           | Získání monitorování připojení                                       |
| Microsoft.Network/networkWatchers/connectionMonitors/write          | Vytvoření monitorování připojení                                    |
| Microsoft.Network/networkWatchers/connectionMonitors/delete         | Odstranit monitorování připojení                                    |

## <a name="packet-capture"></a>Zachytávání paketů

| Akce                                                              | Název                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/packetCaptures/queryStatus/action | Dotaz na stav zachytávání paketů                           |
| Microsoft.Network/networkWatchers/packetCaptures/stop/action        | Zastavit zachytávání paketů                                          |
| Microsoft.Network/networkWatchers/packetCaptures/read               | Získat zachytávání paketů                                           |
| Microsoft.Network/networkWatchers/packetCaptures/write              | Vytvoření zachytávání paketů                                        |
| Microsoft.Network/networkWatchers/packetCaptures/delete             | Odstranit zachycení paketů                                        |

## <a name="ip-flow-verify"></a>Ověření toku protokolu IP

| Akce                                                              | Název                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/ipFlowVerify/action               | Ověření IP toku                                              |

## <a name="next-hop"></a>Další směrování

| Akce                                                              | Název                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/nextHop/action                    | Získejte další segment směrování z virtuálního počítače                                     |

## <a name="network-security-group-view"></a>Zobrazení skupin zabezpečení sítě

| Akce                                                              | Název                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/securityGroupView/action          | Zobrazení skupin zabezpečení                                           |

## <a name="topology"></a>Topologie

| Akce                                                              | Název                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/topology/action                   | Získat topologii                                                   |

## <a name="reachability-report"></a>Sestava dostupnosti

| Akce                                                              | Název                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/azureReachabilityReport/action    | Získat zprávu o dostupnosti Azure                               |

## <a name="additional-actions"></a>Další akce

Možnosti sledovací proces sítě také vyžaduje následující akce:

- Microsoft.Authorization/ \* /čtení
- Microsoft.Resources/subscriptions/resourceGroups/Read
- Microsoft.Storage/storageAccounts/Read
- Microsoft.Storage/storageAccounts/listServiceSas/Action
- Microsoft.Storage/storageAccounts/listAccountSas/Action
- Microsoft.Storage/storageAccounts/listKeys/Action
- Microsoft.Compute/virtualMachines/Read
- Microsoft.Compute/virtualMachines/Write
- Microsoft.Compute/virtualMachines/extensions/Read
- Microsoft.Compute/virtualMachines/extensions/Write
- Microsoft.Compute/virtualMachineScaleSets/Read
- Microsoft.Compute/virtualMachineScaleSets/Write
- Microsoft.Compute/virtualMachineScaleSets/extensions/Read
- Microsoft.Compute/virtualMachineScaleSets/extensions/Write
- Microsoft.Insights/alertRules/*
- Microsoft.Support/*
