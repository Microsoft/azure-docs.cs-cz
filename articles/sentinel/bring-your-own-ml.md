---
title: Přineste si vlastní ML do služby Azure Sentinel | Microsoft Docs
description: Tento článek vysvětluje, jak vytvořit a používat vlastní algoritmy strojového učení pro analýzu dat v Azure Sentinel.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/23/2020
ms.author: yelevin
ms.openlocfilehash: 2164b8ac6e62b8826d5879da07384769c503bfb5
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2021
ms.locfileid: "107598597"
---
# <a name="bring-your-own-machine-learning-ml-into-azure-sentinel"></a>Přineste si vlastní Machine Learning (ML) do Azure Sentinel

Machine Learning (ML) je jedním z hlavních přijetí služby Azure Sentinel a jedním z hlavních atributů, které je nastavily. Azure Sentinel nabízí ML v několika prostředích: integrovaný modul korelace [fúze](fusion.md) a poznámkové bloky Jupyter a nově dostupná platforma pro sestavení (vlastní ml). 

Modely detekce ML se můžou přizpůsobit individuálním prostředím a změnám v chování uživatelů, snížit falešně pozitivní výsledky a identifikovat hrozby, které se nenašly s tradičním přístupem. Řada bezpečnostních organizací rozumí hodnotě ML pro zabezpečení, i když ne mnohé z nich mají luxus profesionály, kteří mají zkušenosti v oblasti zabezpečení i ML. Navrhli jsme rozhraní, které jsme zde předložili pro organizace a odborníky z oblasti IT, aby se s námi mohli ve své cestě na ML Organizace novinkou do ML nebo bez nezbytných odborných znalostí můžou dosáhnout významné ochrany z vestavěných funkcí ML služby Azure Sentinel.

:::image type="content" source="./media/bring-your-own-ml/machine-learning-framework.png" alt-text="Architektura strojového učení":::

## <a name="what-is-the-bring-your-own-machine-learning-byo-ml-platform"></a>Jaká je vaše vlastní Platforma Machine Learning (vlastní-ML)?

Pro organizace, které mají prostředky ML a chtějí sestavovat přizpůsobené modely ML pro své jedinečné obchodní potřeby, nabízíme **platformu vlastní-ml**. Platforma využívá [Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks) / [Apache Spark](http://spark.apache.org/) prostředí a poznámkové bloky Jupyter k tvorbě prostředí ml. Poskytuje následující komponenty:

- balíček vlastní-ML, který obsahuje knihovny, které vám pomůžou s přístupem k datům a nasdílení výsledků zpátky do Log Analytics (LA), abyste mohli výsledky integrovat s detekcí, šetřením a lovem. 

- Šablony algoritmu ML, které můžete přizpůsobit tak, aby vyhovovaly konkrétním problémům se zabezpečením ve vaší organizaci. 

- ukázkové poznámkové bloky pro výuku modelu a naplánování modelu hodnocení. 

Kromě toho můžete využít vlastní modely ML a/nebo vlastní prostředí Spark pro integraci s ověřovacími službami Azure.

S platformou vlastní-ML můžete začít vytvářet vlastní modely ML: 

- Poznámkový blok s ukázkovými daty vám pomůže zajistit ucelený zážitek, aniž byste se museli starat o zpracování produkčních dat.

- Balíček integrovaný do prostředí Spark omezuje problémy a vztření při správě infrastruktury.

- Knihovny podporují přesuny dat. Školicí a vyhodnocovací poznámkové bloky ukazují kompletní prostředí a slouží jako šablona, kterou můžete přizpůsobit vašemu prostředí.

### <a name="use-cases"></a>Případy použití
 
Platforma vlastní-ML a balíček výrazně zkracují čas a úsilí, které budete potřebovat k vytváření vlastních detekcí ML, a nemají možnost řešit konkrétní problémy se zabezpečením v Azure Sentinel. Platforma podporuje následující případy použití:

**Školením algoritmu ml získáte přizpůsobený model:** Můžete využít existující algoritmus ML (sdílenou společností Microsoft nebo komunitou uživatelů) a snadno ho naučit na svých vlastních datech a získat tak přizpůsobený model ML, který lépe vyhovuje vašim datům a prostředí.

**Úprava šablony algoritmu ml pro získání přizpůsobeného modelu:** Můžete upravit šablonu algoritmu ML (sdílenou společností Microsoft nebo komunitou uživatelů) a vyškolit upravený algoritmus na vlastní data a odvodit přizpůsobený model tak, aby vyhovoval vašemu konkrétnímu problému.

**Vytvořte si vlastní model:** Vytvořte si svůj vlastní model od začátku pomocí platformy a nástrojů vlastní-ML služby Azure Sentinel.

**Integrujte svoje prostředí datacihly/Spark:** Integrujte svoje existující prostředí datacihly/Spark do Azure Sentinel a pomocí knihoven vlastní a šablon Sestavujte modely ML v jejich jedinečných situacích.

**Importujte vlastní model ml:** Můžete naimportovat vlastní modely ML a pomocí vlastní platformy a nástrojů je integrovat s Sentinel Azure.

**Nasdílejte algoritmus ml:** Nasdílejte algoritmus ML pro komunitu k přijetí a přizpůsobení.

**Použití ml k zajištění SecOps:** Využijte svůj vlastní model ml a výsledky pro lov, detekci, šetření a reakci.

V tomto článku se dozvíte, jaké součásti platformy vlastní-ML a jak využít platformu a algoritmus přístupu k prostředkům neobvyklé k doručování přizpůsobeného zjišťování ML s použitím Azure Sentinel.

## <a name="azure-databricksspark-environment"></a>Prostředí Azure Databricks/Spark

[Apache Spark™](http://spark.apache.org/) předávané v rámci zjednodušení velkých objemů dat tím, že poskytuje sjednocené rozhraní pro vytváření datových kanálů. Azure Databricks to ještě víc díky tomu, že poskytuje cloudovou platformu s nulovou správou vytvořenou kolem Sparku. Doporučujeme používat datacihly pro vaši platformu vlastní-ML, abyste se mohli soustředit na hledání odpovědí, které vedou k okamžitému dopadu na vaši firmu, a neřešit datové kanály a problémy s platformou.
Pokud už máte datacihly nebo jiné prostředí Sparku a chcete raději použít stávající nastavení, balíček vlastní-ML bude fungovat i na nich. 

## <a name="byo-ml-package"></a>Balíček vlastní-ML

Balíček vlastní ML obsahuje osvědčené postupy a výzkum Microsoftu na front-endu ML pro zabezpečení. V tomto balíčku poskytujeme následující seznam nástrojů, notebooků a šablon algoritmů pro problémy se zabezpečením.

| Název souboru | Description |
| --------- | ----------- |
| azure_sentinel_utilities. WHL | Obsahuje nástroje pro čtení objektů BLOB z Azure a zápis do Log Analytics. |
| AnomalousRASampleData | Poznámkový blok znázorňuje použití modelu přístupu k prostředkům neobvyklé ve službě Azure Sentinel s vygenerovaným školením a testováním ukázkových dat. |
| AnomalousRATraining. ipynb | Poznámkový blok pro výuku algoritmu, sestavení a uložení modelů. |
| AnomalousRAScoring. ipynb | Poznámkový blok pro naplánování spuštění modelu, vizualizace výsledku a zápis skóre zpět do Azure Sentinel. |
|

První šablona algoritmu ML, kterou nabízíme, je pro [detekci přístupu k prostředkům neobvyklé](https://github.com/Azure/Azure-Sentinel/tree/master/BYOML). Vychází z algoritmu pro filtrování spolupráce a je vyškolen v protokolech přístupu ke sdíleným složkám systému Windows (události zabezpečení s ID události 5140). K klíčovým informacím potřebným pro tento model v protokolu patří párování uživatelů a prostředků. 

## <a name="example-walkthrough-anomalous-file-share-access-detection"></a>Příklad návodu: detekce přístupu ke sdílení souborů neobvyklé 

Teď, když jste se seznámili s klíčovými součástmi platformy vlastní-ML, tady je příklad, který vám ukáže, jak pomocí platformy a komponent doručovat přizpůsobenou detekci ML.

### <a name="setup-the-databricksspark-environment"></a>Nastavení prostředí datacihly/Spark

Pokud ho ještě nemáte, budete muset nastavit vlastní prostředí datacihly. Pokyny najdete v dokumentu [rychlý Start pro datacihly](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal?tabs=azure-portal) .

### <a name="auto-export-instruction"></a>Pokyn k automatickému exportu

Pokud chcete vytvářet vlastní modely ML na základě vlastních dat ve službě Azure Sentinel, budete muset exportovat data z Log Analytics do prostředku úložiště objektů BLOB nebo centra událostí, aby model ML mohl k němu přistupovat z datových cihl. Naučte se ingestovat [data do Azure Sentinel](connect-data-sources.md).

V tomto příkladu budete potřebovat data školení pro protokol přístupu ke sdílené složce v úložišti objektů BLOB v Azure. Formát dat je popsán v poznámkovém bloku a v knihovnách.

Data můžete automaticky exportovat z Log Analytics pomocí [rozhraní příkazového řádku Azure (CLI)](/cli/azure/monitor/log-analytics). 

Aby bylo možné příkazy spustit, musíte mít přiřazenou roli **Přispěvatel** v pracovním prostoru Log Analytics, účtu úložiště a prostředku EventHub. 

Tady je ukázková sada příkazů pro nastavení automatického exportu:

```azurecli

az –version

# Login with Azure CLI
 az login

# List all Log Analytics clusters
 az monitor log-analytics cluster list

# Set to specific subscription
 az account set --subscription "SUBSCRIPTION_NAME"
 
# Export to Storage - all tables
 az monitor log-analytics workspace data-export create --resource-group "RG_NAME" --workspace-name "WS_NAME" -n LAExportCLIStr --destination “DESTINATION_NAME" --enable "true" --tables SecurityEvent
 
# Export to EventHub - all tables
 az monitor log-analytics workspace data-export create --resource-group "RG_NAME" --workspace-name "WS_NAME" -n LAExportCLIEH --destination “DESTINATION_NAME" --enable "true" --tables SecurityEvent Heartbeat"]

# List export settings
az monitor log-analytics workspace data-export list --resource-group "RG_NAME" --workspace-name "WS_NAME"

# Delete export setting
 az monitor log-analytics workspace data-export delete --resource-group "RG_NAME" --workspace-name "WS_NAME" --name "NAME"
```

### <a name="export-custom-data"></a>Exportovat vlastní data

Pro vlastní data, která nejsou Log Analytics automatického exportu podporovaná, můžete k přesunu dat použít aplikaci logiky nebo jiná řešení. Můžete se podívat na blog a skript pro [export Log Analytics dat do úložiště objektů BLOB](https://techcommunity.microsoft.com/t5/azure-monitor/log-analytics-data-export-preview/ba-p/1783530) .

### <a name="correlate-with-data-outside-of-azure-sentinel"></a>Korelace s daty mimo Azure Sentinel

Data mimo Azure Sentinel můžete také přenést do úložiště objektů BLOB nebo do centra událostí a korelovat je s daty Sentinel Azure a vytvořit tak modely ML. 
 
### <a name="copy-and-install-the-related-packages"></a>Kopírovat a nainstalovat související balíčky

Zkopírujte balíček vlastní-ML z úložiště GitHub Azure Sentinel popsané výše do vašeho prostředí datacihly. Pak otevřete poznámkové bloky a podle pokynů v poznámkovém bloku nainstalujte požadované knihovny do svých clusterů.

### <a name="model-training-and-scoring"></a>Školení a bodování modelu

Podle pokynů v obou poznámkových blocích změňte konfigurace podle vlastního prostředí a prostředků, postupujte podle pokynů pro vytvoření modelu a sestavte model a naplánujte, aby se v modelu vyhodnoceny příchozí protokoly přístupu ke sdíleným složkám souborů.

### <a name="write-results-to-log-analytics"></a>Zapsat výsledky do Log Analytics

Jakmile získáte hodnocení naplánované, můžete pomocí modulu v poznámkovém bloku pro vyhodnocování zapsat výsledky skóre do pracovního prostoru Log Analytics přidruženého k vaší instanci Azure Sentinel.

### <a name="check-results-in-azure-sentinel"></a>Výsledky kontroly v Azure Sentinel

Pokud se chcete podívat na výsledky skóre spolu s odpovídajícími podrobnostmi protokolu, vraťte se zpátky na svůj portál Sentinel Azure. V části **protokoly** > vlastní protokoly uvidíte výsledky v tabulce **AnomalousResourceAccessResult_CL** (nebo vlastní název tabulky). Tyto výsledky můžete použít k vylepšení šetření a loveckého prostředí.

:::image type="content" source="./media/bring-your-own-ml/anomalous-resource-access-logs.png" alt-text="protokoly přístupu k prostředkům neobvyklé":::

### <a name="build-custom-analytics-rule-with-ml-results"></a>Sestavení vlastního pravidla analýzy pomocí ML výsledků

Jakmile ověříte, že jsou výsledky ML v tabulce vlastních protokolů a Vy jste spokojeni s věrným skóre, můžete na základě výsledků vytvořit detekci. Z portálu Sentinel Azure můžete přejít na **analýzy** a [vytvořit nové pravidlo detekce](tutorial-detect-threats-custom.md). Níže je uveden příklad ukazující dotaz použitý k vytvoření detekce.

:::image type="content" source="./media/bring-your-own-ml/create-byo-ml-analytics-rule.png" alt-text="vytvořit vlastní pravidlo analýzy pro zjišťování B Y O M L":::

### <a name="view-and-respond-to-incidents"></a>Zobrazit incidenty a reagovat na ně
Když nastavíte pravidlo analýzy na základě výsledků ML, pokud jsou výsledky nad prahovou hodnotou, kterou jste v dotazu nastavili, vygeneruje se incident a zobrazí se na stránce **incidenty** ve službě Azure Sentinel. 

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste zjistili, jak používat platformu vlastní-ML služby Azure Sentinel k vytváření nebo importování vlastních algoritmů strojového učení pro analýzu dat a detekci hrozeb.

- Podívejte se na příspěvky o strojovém učení a spoustě dalších relevantních témat na [blogu Sentinel Azure](https://aka.ms/azuresentinelblog).
