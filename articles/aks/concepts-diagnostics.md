---
title: Přehled diagnostiky Azure Kubernetes Service (AKS)
description: Přečtěte si o vlastních diagnostikách clusterů ve službě Azure Kubernetes.
services: container-service
author: yunjchoi
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: yunjchoi
ms.openlocfilehash: ee11221e5484a796b8dbbcb10a323288d3e74756
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2021
ms.locfileid: "107011554"
---
# <a name="azure-kubernetes-service-diagnostics-preview-overview"></a>Přehled služby Azure Kubernetes Service Diagnostics (Preview)

Řešení potíží s clustery Azure Kubernetes Service (AKS) hraje důležitou roli při údržbě clusteru, zejména v případě, že cluster používá klíčové úlohy. Diagnostika AKS je inteligentní, samoobslužné prostředí s možností diagnostiky:
* Pomáhá identifikovat a řešit problémy v clusteru. 
* Je cloudově nativní.
* Nevyžaduje žádnou další konfiguraci ani fakturační náklady.

Tato funkce je teď ve verzi Public Preview. 

## <a name="open-aks-diagnostics"></a>Otevřít diagnostiku AKS

Přístup k diagnostice AKS:

1. V [Azure Portal](https://portal.azure.com)přejděte na svůj cluster Kubernetes.
1. Klikněte na **Diagnostika a řešení problémů** v levém navigačním panelu, který otevře diagnostiku AKS.
1. Vyberte kategorii, která nejlépe popisuje problém vašeho clusteru, například _problémy s uzlem clusteru_, podle:
    * Pomocí klíčových slov na dlaždici domovské stránky.
    * Zadání klíčového slova, které nejlépe popisuje váš problém na panelu hledání.

![Domovská stránka](./media/concepts-diagnostics/aks-diagnostics-homepage.png)

## <a name="view-a-diagnostic-report"></a>Zobrazit diagnostickou sestavu

Po kliknutí na kategorii můžete zobrazit diagnostickou zprávu specifickou pro váš cluster. Diagnostické sestavy inteligentně volají všechny problémy v clusteru s ikonami stavů. Můžete přejít k podrobnostem každého tématu kliknutím na **Další informace** zobrazíte podrobný popis:
* Problémy
* Doporučené akce
* Odkazy na užitečné dokumenty
* Související metriky
* Protokolování dat 

Diagnostické sestavy se generují na základě aktuálního stavu clusteru po spuštění různých kontrol. Můžou být užitečné pro určení problému clusteru a porozumění dalším krokům k vyřešení tohoto problému.

![Diagnostická sestava](./media/concepts-diagnostics/diagnostic-report.png)

![Rozšířená diagnostická sestava](./media/concepts-diagnostics/node-issues.png)

## <a name="cluster-insights"></a>Přehledy clusterů

V rámci služby **cluster Insights** jsou k dispozici následující diagnostické kontroly.

### <a name="cluster-node-issues"></a>Problémy s uzlem v clusteru

Uzel clusteru vyhledává problémy související s uzlem, které způsobují neočekávané chování clusteru.

- Problémy s připraveností uzlů
- Selhání uzlu
- Nedostatečné prostředky
- Uzlu chybí konfigurace IP adresy.
- Selhání uzlu CNI
- Uzel nebyl nalezen
- Napájení uzlu vypnuto
- Selhání ověřování uzlu
- Kube uzlu – proxy zastaralé

### <a name="create-read-update--delete-crud-operations"></a>Vytváření, čtení, aktualizace & odstranění (CRUD) operací

Operace CRUD vyhledává všechny operace CRUD, které způsobují problémy v clusteru.

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

Správa identit a zabezpečení detekuje chyby ověřování a autorizace, které brání komunikaci s clusterem.

- Selhání autorizace uzlů
- chyby 401
- Chyby 403

## <a name="next-steps"></a>Další kroky

* Shromažďovat protokoly, které vám pomůžou při řešení problémů s clustery pomocí [AKS Periscope](https://aka.ms/aksperiscope).

* Přečtěte si [část postupy třídění](/azure/architecture/operator-guides/aks/aks-triage-practices) v provozní příručce AKS Day-2.

* Vystavte své dotazy nebo připomínky na webu [UserVoice](https://feedback.azure.com/forums/914020-azure-kubernetes-service-aks) přidáním "[diag]" v názvu.