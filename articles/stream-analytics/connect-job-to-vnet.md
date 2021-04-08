---
title: Připojení úloh Stream Analytics k prostředkům v Azure Virtual Network (VNET)
description: Tento článek popisuje, jak připojit úlohu Azure Stream Analytics k prostředkům, které jsou ve virtuální síti.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/04/2021
ms.custom: devx-track-js
ms.openlocfilehash: 4701cb4122b4196b08b2a427b34d49c7784b91a7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98878235"
---
# <a name="connect-stream-analytics-jobs-to-resources-in-an-azure-virtual-network-vnet"></a>Připojení úloh Stream Analytics k prostředkům v Azure Virtual Network (VNet)

Vaše úlohy Stream Analytics provedou odchozí připojení ke vstupnímu a výstupnímu prostředku Azure pro zpracování dat v reálném čase a výsledkem vzniku výsledků. Tyto vstupní a výstupní prostředky (například Azure Event Hubs a Azure SQL Database) můžou být za bránou firewall Azure nebo v Azure Virtual Network (virtuální síť). Služba Stream Analytics pracuje na sítích, které nemůžou být přímo zahrnuté do vašich síťových pravidel.

Existují však dva způsoby, jak bezpečně připojit Stream Analytics úlohy ke vstupnímu a výstupnímu prostředku v takových scénářích.
* Použití privátních koncových bodů v clusterech Stream Analytics.
* Pomocí režimu ověřování spravovaných identit spolu s nastavením povoluje sítě důvěryhodných služeb.

Vaše úloha Stream Analytics nepřijímá žádné příchozí připojení.

## <a name="private-endpoints-in-stream-analytics-clusters"></a>Soukromé koncové body v clusterech Stream Analytics.
[Clustery Stream Analytics](./cluster-overview.md) jsou jeden tenant vyhrazený výpočetní cluster, ve kterém můžete spouštět úlohy Stream Analytics. V clusteru Stream Analytics můžete vytvořit spravované privátní koncové body, které umožní, aby všechny úlohy spuštěné v clusteru provedly zabezpečené odchozí připojení k vašim vstupním a výstupním prostředkům.

Vytváření privátních koncových bodů v clusteru Stream Analytics je [operace dvou kroků](./private-endpoints.md). Tato možnost je nejvhodnější pro středně velké úlohy streamování, protože minimální velikost Stream Analytics clusteru je 36 SUs (i když je 36 SUs může sdílet různé úlohy v různých předplatných nebo prostředích, jako je vývoj, testování a produkce).

## <a name="managed-identity-authentication-with-allow-trusted-services-configuration"></a>Spravované ověřování identity s konfigurací povolení důvěryhodných služeb
Některé služby Azure poskytují nastavení **Povolit důvěryhodné sítě služeb Microsoftu** , které v případě povolení umožňuje vašim Stream Analytics úlohám zabezpečeně se připojit k vašemu prostředku pomocí silného ověřování. Tato možnost umožňuje připojit vaše úlohy ke vstupnímu a výstupnímu prostředku bez vyžadování Stream Analytics clusteru a privátních koncových bodů. Konfigurace úlohy pro použití této techniky je operace se dvěma kroky:
* Při konfiguraci vstupu nebo výstupu v úloze Stream Analytics použijte režim ověřování spravovaných identit.
* Udělte konkrétním Stream Analytics úlohám explicitní přístup k cílovým prostředkům přiřazením role Azure k spravované identitě přiřazené systémem v rámci úlohy. 

Povolení možnosti **Povolit důvěryhodným službám Microsoftu** neuděluje paušální přístup k žádné úloze. Díky tomu máte plnou kontrolu nad tím, které konkrétní Stream Analytics úlohy budou mít k prostředkům zabezpečený přístup. 

Vaše úlohy se můžou připojit k následujícím službám Azure pomocí této techniky:
1. [BLOB Storage nebo Azure Data Lake Storage Gen2](./blob-output-managed-identity.md) – může to být účet úložiště vaší úlohy, vstup nebo výstup streamování.
2. [Azure Event Hubs](./event-hubs-managed-identity.md) – může se jednat o vstup nebo výstup streamování vaší úlohy.

Pokud se vaše úlohy potřebují připojit k jiným vstupním nebo výstupním typům, můžete napsat z Stream Analytics Event Hubs nejprve výstup a potom do libovolného cílového umístění pomocí Azure Functions. Pokud chcete přímo zapisovat z Stream Analytics do jiných typů výstupu zabezpečených ve virtuální síti nebo bráně firewall, jediná možnost je použít v clusterech Stream Analytics privátní koncové body.

## <a name="next-steps"></a>Další kroky

* [Vytvoření a odebrání privátních koncových bodů v clusterech Stream Analytics](./private-endpoints.md)
* [Připojení k Event Hubs ve virtuální síti pomocí spravovaného ověřování identity](./event-hubs-managed-identity.md)
* [Připojení ke službě BLOB Storage a ADLS Gen2 ve virtuální síti pomocí spravovaného ověřování identity](./blob-output-managed-identity.md)