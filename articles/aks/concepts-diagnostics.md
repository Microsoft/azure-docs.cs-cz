---
title: Přehled diagnostiky Azure Kubernetes Service (AKS)
description: Přečtěte si o vlastních diagnostikách clusterů ve službě Azure Kubernetes.
services: container-service
author: yunjchoi
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: yunjchoi
ms.openlocfilehash: e8921152177d3e4534ca9fb48cf209aed6e1b27c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96183358"
---
# <a name="azure-kubernetes-service-diagnostics-preview-overview"></a>Přehled služby Azure Kubernetes Service Diagnostics (Preview)

Řešení potíží s clustery Azure Kubernetes Service (AKS) je důležitou součástí údržby clusteru, zejména v případě, že cluster používá klíčové úlohy. Diagnostika AKS je inteligentní a samoobslužné prostředí, které vám pomůže identifikovat a vyřešit problémy v clusteru. Diagnostika AKS je v cloudu nativní a můžete ji použít bez dalších konfigurací nebo fakturačních nákladů.

Tato funkce je teď ve verzi Public Preview.

## <a name="open-aks-diagnostics"></a>Otevřít diagnostiku AKS

Přístup k diagnostice AKS:

- V [Azure Portal](https://portal.azure.com)přejděte na svůj cluster Kubernetes.
- Klikněte na **Diagnostika a řešení problémů** v levém navigačním panelu, který otevře diagnostiku AKS.
- Vyberte kategorii, která nejlépe popisuje potíže s clusterem pomocí klíčových slov na dlaždici domovské stránky, nebo zadejte klíčové slovo, které nejlépe popisuje váš problém na panelu hledání, například _problémy s uzlem v clusteru_.

![Domovská stránka](./media/concepts-diagnostics/aks-diagnostics-homepage.png)

## <a name="view-a-diagnostic-report"></a>Zobrazit diagnostickou sestavu

Po kliknutí na kategorii můžete zobrazit diagnostickou zprávu specifickou pro váš cluster. Diagnostická sestava inteligentně vyvolá, pokud se ve vašem clusteru vyskytnou nějaké problémy s ikonami stavu. Můžete přejít k podrobnostem každého tématu kliknutím na **Další informace** zobrazíte podrobný popis problému, doporučené akce, odkazy na užitečné dokumenty, související metriky a data protokolování. Diagnostické sestavy se inteligentně generují na základě aktuálního stavu clusteru po spuštění různých kontrol. Diagnostické sestavy můžou být užitečným nástrojem pro určení problému clusteru a vyhledání dalších kroků k vyřešení problému.

![Diagnostická sestava](./media/concepts-diagnostics/diagnostic-report.png)

![Rozšířená diagnostická sestava](./media/concepts-diagnostics/node-issues.png)

## <a name="cluster-insights"></a>Přehledy clusterů

V rámci služby **cluster Insights** jsou k dispozici následující diagnostické kontroly.

### <a name="cluster-node-issues"></a>Problémy s uzlem v clusteru

Uzel clusteru vyhledává problémy související s uzlem, které by mohly způsobit neočekávané chování clusteru.

- Problémy s připraveností uzlů
- Selhání uzlu
- Nedostatečné prostředky
- Uzlu chybí konfigurace IP adresy.
- Selhání uzlu CNI
- Uzel nebyl nalezen
- Napájení uzlu vypnuto
- Selhání ověřování uzlu
- Kube uzlu – proxy zastaralé

### <a name="create-read-update--delete-operations"></a>Vytváření, čtení, aktualizace & odstranění operací

Operace CRUD vyhledává všechny operace CRUD, které mohou způsobovat problémy v clusteru.

- Chyba operace odstranění podsítě při použití
- Chyba operace odstranění skupiny zabezpečení sítě
- Chyba operace odstranění směrovací tabulky v aplikaci
- Odkazovaná chyba zřizování prostředků
- Chyba operace odstranění veřejné IP adresy
- Nasazení se nepovedlo kvůli kvótě nasazení.
- Chyba operace kvůli zásadám organizace
- Chybí registrace předplatného.
- Chyba zřizování rozšíření virtuálního počítače
- Kapacita podsítě
- Chyba překročení kvóty

### <a name="identity-and-security-management"></a>Správa identit a zabezpečení

Správa identit a zabezpečení detekuje chyby ověřování a autorizace, které mohou bránit komunikaci s clusterem.

- Selhání autorizace uzlů
- chyby 401
- Chyby 403

## <a name="next-steps"></a>Další kroky

Shromažďovat protokoly, které vám pomůžou při řešení problémů s clustery pomocí [AKS Periscope](https://aka.ms/aksperiscope).

Přečtěte si [část postupy třídění](/azure/architecture/operator-guides/aks/aks-triage-practices) v provozní příručce AKS Day-2.

Vystavte své dotazy nebo připomínky na webu [UserVoice](https://feedback.azure.com/forums/914020-azure-kubernetes-service-aks) přidáním "[diag]" v názvu.