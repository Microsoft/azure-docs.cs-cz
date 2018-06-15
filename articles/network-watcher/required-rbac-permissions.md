---
title: Oprávnění potřebná k použití možnosti sledovací proces sítě Azure | Microsoft Docs
description: Další informace, které oprávnění pro řízení přístupu na základě role Azure jsou nutné k práci s funkcemi sledovací proces sítě.
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
ms.openlocfilehash: 09f3a1e1d9c6796cb55ae8f0ab18bf8e1b3fa198
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/12/2018
ms.locfileid: "34077875"
---
# <a name="role-based-access-control-permissions-required-to-use-network-watcher-capabilities"></a>Oprávnění pro řízení přístupu na základě rolí k použití možnosti sledovací proces sítě vyžaduje

Řízení přístupu Azure na základě rolí (RBAC) umožňuje přiřadit pouze konkrétní akce členy vaší organizace, který vyžadují k dokončení jejich přiřazené zodpovědnosti. Využívá možnosti sledovací proces sítě, musí mít účet přihlášení do Azure s přiřazenou k [vlastníka](/role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#owner), [Přispěvatel](/role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#contributor), nebo [Přispěvatel sítě](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#network-contributor) předdefinované role, nebo přiřazené [vlastní role](../role-based-access-control/custom-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) přiřazené akce uvedené pro každou funkci sledovací proces sítě v následujících částech. Další informace o možnostech sledovací proces sítě najdete v tématu [co je sledovací proces sítě?](network-watcher-monitoring-overview.md).

## <a name="network-watcher"></a>Network Watcher

| Akce                                                              | Název                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/read                              | Získat sledovací proces sítě                                          |
| Microsoft.Network/networkWatchers/write                             | Vytvořit nebo aktualizovat sledovací proces sítě                             |
| Microsoft.Network/networkWatchers/delete                            | Odstranit sledovací proces sítě                                       |

## <a name="nsg-flow-logs"></a>Tok protokolů NSG

| Akce                                                              | Název                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/configureFlowLog/action           | Konfigurace toku protokolu                                           |
| Microsoft.Network/networkWatchers/queryFlowLogStatus/action         | Dotaz na stav pro tok protokolu                                    |

## <a name="connection-troubleshoot"></a>Řešení potíží s připojení

| Akce                                                              | Název                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/queryTroubleshootResult/action    | Výsledky dotazu připojení k řešení potíží s testu                |
| Microsoft.Network/networkWatchers/troubleshoot/action               | Spustit připojení k řešení potíží s testu                             |

## <a name="connection-monitor"></a>Monitorování připojení

| Akce                                                              | Název                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/connectionMonitors/start/action   | Spustit monitorování připojení                                     |
| Microsoft.Network/networkWatchers/connectionMonitors/stop/action    | Zastavení monitorování připojení                                      |
| Microsoft.Network/networkWatchers/connectionMonitors/query/action   | Dotaz na monitorování připojení                                     |
| Microsoft.Network/networkWatchers/connectionMonitors/read           | Získat monitorování připojení                                       |
| Microsoft.Network/networkWatchers/connectionMonitors/write          | Vytvoření připojení monitorování                                    |
| Microsoft.Network/networkWatchers/connectionMonitors/delete         | Monitorování připojení odstranit                                    |

## <a name="packet-capture"></a>Zachytávání paketů

| Akce                                                              | Název                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/packetCaptures/queryStatus/action | Dotaz na stav zachytávání paketů                           |
| Microsoft.Network/networkWatchers/packetCaptures/stop/action        | Zastavit zachytávání paketů                                          |
| Microsoft.Network/networkWatchers/packetCaptures/read               | Získat zachytávání paketů                                           |
| Microsoft.Network/networkWatchers/packetCaptures/write              | Vytvoření zachytávání paketů                                        |
| Microsoft.Network/networkWatchers/packetCaptures/delete             | Odstranit zachytávání paketů                                        |

## <a name="ip-flow-verify"></a>Ověřte toku IP

| Akce                                                              | Název                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/ipFlowVerify/action               | Ověřte tok IP                                              |

## <a name="next-hop"></a>Další směrování

| Akce                                                              | Název                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/nextHop/action                    | Získání dalšího přechodu z virtuálního počítače                                     |

## <a name="network-security-group-view"></a>Zobrazení skupin zabezpečení sítě

| Akce                                                              | Název                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/securityGroupView/action          | Zobrazení skupiny zabezpečení                                           |

## <a name="topology"></a>Topologie

| Akce                                                              | Název                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/topology/action                   | Získat topologie                                                   |

## <a name="reachability-report"></a>Sestava dostupnosti

| Akce                                                              | Název                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/azureReachabilityReport/action    | Získat zprávu o Azure dostupnosti                               |

## <a name="additional-actions"></a>Další akce

Možnosti sledovací proces sítě také vyžadovat následující akce:

- Microsoft.Storage/Read
- Microsoft.Authorization/Read
- Microsoft.Resources/subscriptions/resourceGroups/Read
- Microsoft.Storage/storageAccounts/listServiceSas/Action
- Microsoft.Storage/storageAccounts/listAccountSas/Action
- Microsoft.Storage/storageAccounts/listKeys/Action
- Microsoft.Compute/virtualMachines/Read
- Microsoft.Compute/virtualMachines/Write
- Microsoft.Compute/virtualMachineScaleSets/Read
- Microsoft.Compute/virtualMachineScaleSets/Write
- Microsoft.Insights/alertRules/*
- Microsoft.Support/*