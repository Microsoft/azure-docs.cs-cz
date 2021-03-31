---
title: Seznámení s diagnostikou konfigurace sítě v Azure Network Watcher | Microsoft Docs
description: Tato stránka poskytuje přehled diagnostiky konfigurace Network Watcher sítě
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/15/2020
ms.author: damendo
ms.openlocfilehash: 5feef79a08789ad381b0c93cb938abd9effdfcc8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "102502004"
---
# <a name="introduction-to-network-configuration-diagnostics-in-azure-network-watcher"></a>Seznámení s diagnostikou konfigurace sítě v Azure Network Watcher

Nástroj pro diagnostiku konfigurace sítě pomáhá zákazníkům pochopit, které přenosové toky budou ve vašem Azure Virtual Network povolené nebo zakázané, spolu s podrobnými informacemi pro ladění. Může vám porozumět tomu, jestli jsou pravidla NSG správně nakonfigurovaná. 

## <a name="pre-requisites"></a>Požadavky
Pokud chcete používat diagnostiku konfigurace sítě, Network Watcher musí být ve vašem předplatném povolené. Pokud chcete povolit, přečtěte si téma [vytvoření instance služby Azure Network Watcher](./network-watcher-create.md) .

## <a name="background"></a>Pozadí

- Vaše prostředky v Azure jsou připojené přes virtuální sítě (virtuální sítě) a podsítě. Zabezpečení těchto virtuální sítě a podsítí se dá spravovat pomocí skupiny zabezpečení sítě (NSG).
- NSG obsahuje seznam pravidel zabezpečení, která povolují nebo zakazují síťový provoz pro prostředky, ke kterým je připojen. Skupin zabezpečení sítě je možné přidružit k podsítím, jednotlivým virtuálním počítačům nebo jednotlivým síťovým rozhraním (nic) připojeným k virtuálním počítačům. 
- Všechny toky provozu ve vaší síti se vyhodnocují pomocí pravidel v příslušných NSG.
- Pravidla se vyhodnocují v závislosti na čísle priority od nejnižší po nejvyšší. 

## <a name="how-does-network-configuration-diagnostic-work"></a>Jak funguje Diagnostika konfigurace sítě? 

Pro daný tok nástroj NCD spustí simulaci toku a vrátí, zda byl tok povolen (nebo odepřen) a podrobné informace o pravidlech, která povolují nebo zakazují tok.  Zákazníci musí poskytnout podrobnosti o toku, jako je zdroj, cíl, protokol atd. Nástroj vrátí, zda byl provoz povolen nebo odepřen, NSG pravidla vyhodnocená pro zadaný tok a výsledky vyhodnocení pro každé pravidlo.

## <a name="next-steps"></a>Další kroky

Použití diagnostiky konfigurace sítě prostřednictvím dalších rozhraní
 - [REST API](/rest/api/network-watcher/networkwatchers/getnetworkconfigurationdiagnostic)
 - [PowerShell](/powershell/module/az.network/invoke-aznetworkwatchernetworkconfigurationdiagnostic)
 - [Azure CLI](/cli/azure/network/watcher#az_network_watcher_run_configuration_diagnostic)