---
title: Oprávnění RBAC potřebná k použití funkcí
titleSuffix: Azure Network Watcher
description: Zjistěte, která oprávnění řízení přístupu na základě rolí Azure jsou vyžadována pro práci s funkcemi Sledování sítě.
services: network-watcher
documentationcenter: ''
author: damendo
ms.service: network-watcher
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: damendo
ms.openlocfilehash: f8743f19d6cd262ad140659be55a4fc57e842564
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840549"
---
# <a name="role-based-access-control-permissions-required-to-use-network-watcher-capabilities"></a>Oprávnění řízení přístupu založená na rolích potřebná k použití funkcí sledování sítě

Řízení přístupu na základě rolí Azure (RBAC) umožňuje přiřadit pouze konkrétní akce členům vaší organizace, které potřebují k dokončení jejich přiřazené odpovědnosti. Chcete-li používat funkce Sledování sítě, musí být účet, se kterým se přihlásíte do Azure, přiřazen k předdefinovaným rolím [vlastníka](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#owner), [přispěvatele](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#contributor)nebo [síťového přispěvatele](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#network-contributor) nebo přiřazen k [vlastní roli,](../role-based-access-control/custom-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) které jsou přiřazeny akce uvedené pro každou funkci sledování sítě v následujících částech. Další informace o možnostech sledovacího programu sítě naleznete v tématu [Co je sledování sítě?](network-watcher-monitoring-overview.md).

## <a name="network-watcher"></a>Network Watcher

| Akce                                                              | Popis                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/read                              | Získání sledovacího procesu sítě                                          |
| Microsoft.Network/networkWatchers/write                             | Vytvoření nebo aktualizace sledovacího procesu sítě                             |
| Microsoft.Network/networkWatchers/delete                            | Odstranění sledovacího procesu sítě                                       |

## <a name="nsg-flow-logs"></a>Protokoly toku NSG

| Akce                                                              | Popis                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/configureFlowLog/action           | Konfigurace protokolu toku                                           |
| Microsoft.Network/networkWatchers/queryFlowLogStatus/action         | Stav dotazu pro protokol toku                                    |

## <a name="connection-troubleshoot"></a>Řešení potíží s připojením

| Akce                                                              | Popis                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/connectivityCheck/action          | Zahájení testu potíží s připojením
| Microsoft.Network/networkWatchers/queryTroubleshootResult/action    | Výsledky testu potíží s připojením dotazu                |
| Microsoft.Network/networkWatchers/troubleshoot/action               | Spuštění testu potíží s připojením                             |

## <a name="connection-monitor"></a>Monitor připojení

| Akce                                                              | Popis                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/connectionMonitors/start/action   | Spuštění monitoru připojení                                     |
| Microsoft.Network/networkWatchers/connectionMonitory/stop/action    | Zastavení monitoru připojení                                      |
| Microsoft.Network/networkWatchers/connectionMonitors/query/action   | Dotaz na monitor připojení                                     |
| Microsoft.Network/networkWatchers/connectionMonitors/read           | Získání monitoru připojení                                       |
| Microsoft.Network/networkWatchers/connectionMonitors/write          | Vytvoření monitorování připojení                                    |
| Microsoft.Network/networkWatchers/connectionMonitors/delete         | Odstranění monitoru připojení                                    |

## <a name="packet-capture"></a>Zachytávání paketů

| Akce                                                              | Popis                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/packetCaptures/queryStatus/action | Dotaz na stav sběru paketů                           |
| Microsoft.Network/networkWatchers/packetCaptures/stop/action        | Zastavení sběru paketů                                          |
| Microsoft.Network/networkWatchers/packetCaptures/read               | Získání zachycení paketu                                           |
| Microsoft.Network/networkWatchers/packetCaptures/write              | Vytvoření zachycení paketu                                        |
| Microsoft.Network/networkWatchers/packetCaptures/delete             | Odstranění zachycení paketu                                        |

## <a name="ip-flow-verify"></a>Ověření toku protokolu IP

| Akce                                                              | Popis                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/ipFlowVerify/action               | Ověření toku IP                                              |

## <a name="next-hop"></a>Další směrování

| Akce                                                              | Popis                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/nextHop/action                    | Získání dalšího směrování z virtuálního virtuálního mě                                     |

## <a name="network-security-group-view"></a>Zobrazení skupin zabezpečení sítě

| Akce                                                              | Popis                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/securityGroupView/action          | Zobrazit skupiny zabezpečení                                           |

## <a name="topology"></a>Topologie

| Akce                                                              | Popis                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/topologie/akce                   | Získat topologii                                                   |

## <a name="reachability-report"></a>Sestava dosažitelnosti

| Akce                                                              | Popis                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/azureReachabilityReport/action    | Získání sestavy dostupnosti Azure                               |


## <a name="additional-actions"></a>Další opatření

Funkce sledování sítě také vyžadují následující akce:

| Akce                                                           | Popis                                                    |
| ---------                                                           | -------------                                                  |
| Microsoft.Authorization/\*/Read                                     | Slouží k načtení přiřazení rolí RBAC a definic zásad.          |
| Microsoft.Resources/subscriptions/resourceGroups/Read               | Slouží k vytvoření výčtu všech skupin prostředků v předplatném.    |
| Microsoft.Storage/storageAccounts/Read                              | Slouží k získání vlastností pro zadaný účet úložiště.   |
| Microsoft.Storage/storageAccounts/listServiceSas/Action, </br> Microsoft.Storage/storageAccounts/listAccountSas/Action, <br> Microsoft.Storage/storageAccounts/listKeys/Action| Slouží k načtení sdílených přístupových podpisů (SAS), které umožňují [zabezpečený přístup k účtu úložiště](https://docs.microsoft.com/azure/storage/common/storage-sas-overview) a zápis do účtu úložiště. |
| Microsoft.Compute/virtualMachines/Read, </br> Microsoft.Compute/virtualMachines/Write| Slouží k přihlášení k virtuálnímu účtu, zachytávání paketů a jeho nahrání do účtu úložiště.|
| Microsoft.Compute/virtualMachines/extensions/Read </br> Microsoft.Compute/virtualMachines/extensions/Write| Slouží ke kontrole, zda je k dispozici rozšíření Sledování sítě, a v případě potřeby je nainstalovat. |
| Microsoft.Compute/virtualMachineScaleSets/Read, </br> Microsoft.Compute/virtualMachineScaleSets/Write| Používá se k přístupu k škálovacím soupravám virtuálních strojů, k zachycení paketů a jejich nahrání do účtu úložiště|
| Microsoft.Compute/virtualMachineScaleSets/extensions/Read, </br> Microsoft.Compute/virtualMachineScaleSets/extensions/Write| Slouží ke kontrole, zda je k dispozici rozšíření Sledování sítě, a v případě potřeby je nainstalovat. |
| Microsoft.Insights/alertRules/*                                     | Slouží k nastavení upozornění na metriky.                                     |
| Microsoft.Support/*                                                 | Slouží k vytváření a aktualizaci lístků podpory z programu Sledování sítě. |
