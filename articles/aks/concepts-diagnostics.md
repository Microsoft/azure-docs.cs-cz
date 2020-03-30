---
title: Přehled diagnostiky služby Azure Kubernetes (AKS)
description: Další informace o samodiagnostiku clusterů ve službě Azure Kubernetes Service.
services: container-service
author: yunjchoi
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: yunjchoi
ms.openlocfilehash: 03bb7b786280dfcbb64190adac51b8d001d59c18
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79126603"
---
# <a name="azure-kubernetes-service-diagnostics-preview-overview"></a>Přehled diagnostiky služeb Azure Kubernetes (preview)

Řešení problémů s clustery Služby Azure Kubernetes (AKS) je důležitou součástí údržby clusteru, zejména pokud váš cluster spouštějí kritické úlohy. AKS Diagnostics je inteligentní, samodiagnostické prostředí, které vám pomůže identifikovat a vyřešit problémy ve vašem clusteru. AKS Diagnostika je nativní pro cloud a můžete ji použít bez dalších nákladů na konfiguraci nebo fakturaci.

Tato funkce je nyní ve verzi Public Preview.

## <a name="open-aks-diagnostics"></a>Otevřít diagnostiku AKS

Přístup k diagnostice AKS:

- Přejděte do clusteru Kubernetes na [webu Azure Portal](https://portal.azure.com).
- Klikněte na **Diagnostika a řešení problémů** v levé navigaci, která otevře Diagnostika AKS.
- Zvolte kategorii, která nejlépe vystichne problém vašeho clusteru pomocí klíčových slov na dlaždici domovské stránky, nebo zadejte klíčové slovo, které nejlépe popisuje váš problém na vyhledávacím panelu, například _Problémy uzlu clusteru_.

![Domovská stránka](./media/concepts-diagnostics/aks-diagnostics-homepage.png)

## <a name="view-a-diagnostic-report"></a>Zobrazení diagnostické sestavy

Po kliknutí na kategorii můžete zobrazit diagnostickou sestavu specifickou pro váš cluster. Diagnostická sestava inteligentně volá, pokud je v clusteru nějaký problém se stavovými ikonami. Kliknutím na další informace můžete přejít k podrobnostem kliknutím na **další informace** a zobrazit podrobný popis problému, doporučené akce, odkazy na užitečné dokumenty, související metriky a data protokolování. Diagnostické sestavy jsou inteligentně generovány na základě aktuálního stavu clusteru po spuštění různých kontrol. Diagnostické sestavy mohou být užitečným nástrojem pro určení problému clusteru a nalezení dalších kroků k vyřešení problému.

![Diagnostická sestava](./media/concepts-diagnostics/diagnostic-report.png)

![Rozšířená diagnostická sestava](./media/concepts-diagnostics/node-issues.png)

## <a name="cluster-insights"></a>Přehledy clusteru

Následující diagnostické kontroly jsou k dispozici v **Cluster Insights**.

### <a name="cluster-node-issues"></a>Problémy s uzlem clusteru

Problémy s uzlem clusteru kontroluje problémy související s uzlem, které mohou způsobit neočekávanou chounutí clusteru.

- Problémy s připraveností uzlu
- Selhání uzlu
- Nedostatek prostředků
- Uzel chybí konfigurace PROTOKOLU IP
- Selhání uzlu CNI
- Uzel nebyl nalezen.
- Vypnutí uzlu
- Selhání ověřování uzlu
- Uzel kube-proxy zastaralé

### <a name="create-read-update--delete-operations"></a>Vytváření, čtení, aktualizace & odstranění

Crud Operations kontroluje všechny operace CRUD, které mohou způsobit problémy ve vašem clusteru.

- Chyba operace odstranění operace podsítě v provozu
- Chyba operace odstranění skupiny zabezpečení sítě
- Chyba operace odstranění operace v provozní tabulce v provozu
- Referenční chyba zřizování zdrojů
- Chyba operace odstranění operace veřejné IP adresy
- Selhání nasazení z důvodu kvóty nasazení
- Chyba operace z důvodu zásad organizace
- Chybějící registrace předplatného
- Chyba zřizování rozšíření virtuálního virtuálního montovana
- Kapacita podsítě
- Chyba překročena kvóta

### <a name="identity-and-security-management"></a>Správa identit a zabezpečení

Správa identit a zabezpečení detekuje chyby ověřování a autorizace, které mohou bránit komunikaci s vaším clusterem.

- Selhání autorizace uzlu
- 401 chyb
- Chyby 403

## <a name="next-steps"></a>Další kroky

Shromažďujte protokoly, které vám pomohou dále řešit problémy s clustery pomocí [aks periskopu](https://aka.ms/aksperiscope).

Zveřejněte své dotazy nebo zpětnou vazbu na [uservoice](https://feedback.azure.com/forums/914020-azure-kubernetes-service-aks) přidáním "[Diag]" v názvu.
