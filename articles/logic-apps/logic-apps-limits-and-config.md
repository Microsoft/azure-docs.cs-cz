---
title: Omezení a konfigurace – Azure Logic Apps | Microsoft Docs
description: Služba omezení a hodnoty konfigurace pro Azure Logic Apps
services: logic-apps
documentationcenter: ''
author: jeffhollan
manager: anneta
editor: ''
ms.assetid: 75b52eeb-23a7-47dd-a42f-1351c6dfebdc
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: LADocs; jehollan
ms.openlocfilehash: 740a31f4b5e0ffe9fe2ae04d3dac51cf25ab3055
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="logic-apps-limits-and-configuration"></a>Omezení aplikace logiky a konfigurace

Tento článek popisuje aktuální omezení a podrobnosti o konfiguraci pro Azure Logic Apps.

## <a name="limits"></a>Omezení

### <a name="http-request-limits"></a>Omezení požadavků HTTP

Zde jsou limity pro jeden požadavek HTTP nebo volání konektor:

#### <a name="timeout"></a>Vypršení časového limitu

| Název | Omezení | Poznámky | 
| ---- | ----- | ----- | 
| Časový limit žádosti | 120 sekund | [Asynchronní vzor](../logic-apps/logic-apps-create-api-app.md) nebo [dokud smyčky](logic-apps-control-flow-loops.md) můžete odpovídajícím způsobem podle potřeby | 
|||| 

#### <a name="message-size"></a>Velikost zpráv

| Název | Omezení | Poznámky | 
| ---- | ----- | ----- | 
| Velikost zpráv | 100 MB | 100 MB nemusí podporovat některé konektory a rozhraní API. | 
| Limit vyhodnocení výrazu | 131 072 znaků | `@concat()`, `@base64()`, `string` nesmí být delší než toto omezení. | 
|||| 

#### <a name="retry-policy"></a>Zásady opakování

| Název | Omezení | Poznámky | 
| ---- | ----- | ----- | 
| Opakované pokusy | 90 | Výchozí hodnota je 4. Můžete nakonfigurovat [opakujte zásad parametr](../logic-apps/logic-apps-workflow-actions-triggers.md). | 
| Maximální zpoždění při opakování | 1 den | Můžete nakonfigurovat [opakujte zásad parametr](../logic-apps/logic-apps-workflow-actions-triggers.md). | 
| Minimální zpoždění při opakování | 5 s | Můžete nakonfigurovat [opakujte zásad parametr](../logic-apps/logic-apps-workflow-actions-triggers.md). |
|||| 

### <a name="run-duration-and-retention"></a>Doba trvání spuštění a jejich uchovávání

Zde jsou limity pro spuštění aplikace logiky jeden:

| Název | Omezení | 
| ---- | ----- | 
| Doba trvání spuštění | 90 dnů | 
| Uchování úložiště | čas zahájení 90 dnů od spuštění | 
| Interval opakování min. | 1 sekunda </br>Pro s plán služby App Service logic apps: 15 sekund | 
| Maximální interval opakování | 500 dnů | 
||| 

Překročení omezení pro spuštění doba trvání nebo uchovávání úložiště v toku vaší normálním zpracování [obraťte se na tým Logic Apps](mailto://logicappsemail@microsoft.com) nápovědu k vašim požadavkům.

### <a name="looping-and-debatching-limits"></a>Ve smyčce a debatching omezení

Zde jsou limity pro spuštění aplikace logiky jeden:

| Název | Omezení | Poznámky | 
| ---- | ----- | ----- | 
| Foreach – položky | 100,000 | Můžete použít [dotaz akce](../connectors/connectors-native-query.md) vyfiltrujete větší pole, podle potřeby. | 
| Dokud iterací | 5 000 | | 
| SplitOn položky | 100,000 | | 
| ForEach paralelismus | 50 | Výchozí hodnota je 20. <p>Konkrétní úroveň paralelního zpracování v smyčka typu ForEach, nastavit `runtimeConfiguration` vlastnost `foreach` akce. <p>Postupně spustit smyčka typu ForEach, nastavte `operationOptions` vlastnost "Sekvenční" v `foreach` akce. | 
|||| 

### <a name="throughput-limits"></a>Omezení propustnosti

Zde jsou limity pro instanci aplikace logiky jeden:

| Název | Omezení | Poznámky | 
| ----- | ----- | ----- | 
| Akce spuštěních za 5 minut | 100,000 | Pokud chcete zvýšit limit než 300 000, můžete spustit aplikace logiky `High Throughput` režimu. Ke konfiguraci režimu vysoké propustnosti, v části `runtimeConfiguration` prostředku pracovního postupu, nastavte `operationOptions` vlastnost `OptimizedForHighThroughput`. <p>**Poznámka:**: režimu vysoké propustnosti je ve verzi preview. Navíc můžete distribuovat zatížení mezi více aplikacemi v případě potřeby. | 
| Souběžných volání odchozí akce | ~2,500 | Snižte počet souběžných požadavků nebo zkrátit dobu trvání podle potřeby. | 
| Koncový bod modulu runtime: souběžných příchozí volání |~1,000 | Snižte počet souběžných požadavků nebo zkrátit dobu trvání podle potřeby. | 
| Modul runtime koncový bod: počet volání za 5 minut pro čtení  | 60,000 | Můžete rozdělit zatížení mezi více aplikacemi podle potřeby. | 
| Koncový bod modulu runtime: vyvolat volání za 5 minut| 45,000 |Můžete rozdělit zatížení mezi více aplikacemi podle potřeby. | 
|||| 

Překročení těchto mezních hodnot v normálním zpracování nebo spuštění zátěžové testování, které může tato omezení překročí [obraťte se na tým Logic Apps](mailto://logicappsemail@microsoft.com) nápovědu k vašim požadavkům.

### <a name="logic-app-definition-limits"></a>Omezení definici aplikace logiky

Zde jsou limity pro definici aplikace logiky jeden:

| Název | Omezení | Poznámky | 
| ---- | ----- | ----- | 
| Akce za pracovního postupu | 500 | Pokud chcete rozšířit tento limit, můžete přidat vnořené pracovní postupy, podle potřeby. |
| Povolené akce hloubky vnoření | 8 | Pokud chcete rozšířit tento limit, můžete přidat vnořené pracovní postupy, podle potřeby. | 
| Pracovní postupy na oblast na předplatné | 1000 | | 
| Aktivační události za pracovního postupu | 10 | | 
| Přepínač oboru případech limit | 25 | | 
| Počet proměnných za pracovního postupu | 250 | | 
| Maximální počet znaků na jednu výraz | 8 192 | | 
| Maximální počet `trackedProperties` velikost ve znacích | 16,000 | 
| `action`/`trigger` Název omezení | 80 | | 
| `description` omezení délky | 256 | | 
| `parameters` Limit | 50 | | 
| `outputs` Limit | 10 | | 
|||| 

<a name="custom-connector-limits"></a>

### <a name="custom-connector-limits"></a>Vlastní konektor omezení

Tato omezení platí pro vlastní konektory, které můžete vytvořit z webových rozhraní API.

| Název | Omezení | 
| ---- | ----- | 
| Počet vlastních konektorů, které můžete vytvořit | 1 000 na předplatné Azure | 
| Počet požadavků za minutu pro každé připojení vytvořené vlastním konektorem | 500 požadavků pro každé připojení vytvořené příslušným konektorem |
||| 

### <a name="integration-account-limits"></a>Limity účtu integrace

Tady jsou limity pro artefakty, které můžete přidat k účtu integrace.

| Název | Omezení | Poznámky | 
| ---- | ----- | ----- | 
| Schéma | 8 MB | Můžete použít [identifikátor URI objektu blob](../logic-apps/logic-apps-enterprise-integration-schemas.md) odeslat soubory větší než 2 MB. | 
| Mapu (soubor XSLT) | 2 MB | | 
| Modul runtime koncový bod: počet volání za 5 minut pro čtení | 60,000 | Můžete distribuovat zatížení napříč více účtů, podle potřeby. | 
| Koncový bod modulu runtime: vyvolat volání za 5 minut | 45,000 | Můžete distribuovat zatížení napříč více účtů, podle potřeby. | 
| Modul runtime koncový bod: počet volání za 5 minut pro sledování | 45,000 | Můžete distribuovat zatížení napříč více účtů, podle potřeby. | 
| Koncový bod modulu runtime: blokování souběžných volání | ~1,000 | Snížení počtu souběžných požadavků nebo zkrátit dobu trvání podle potřeby. | 
|||| 

Tyto limity platí počtu artefaktů, které můžete přidat k účtu integrace.

#### <a name="free-pricing-tier"></a>Volná cenová úroveň

| Název | Omezení | Poznámky | 
| ---- | ----- | ----- | 
| Smlouvy | 10 | | 
| Jiné typy artefaktů | 25 | Typy artefaktů obsahují partnery, schémata, certifikáty a mapy. Každý typ může obsahovat až do maximálního počtu artefaktů. | 
|||| 

#### <a name="standard-pricing-tier"></a>Standardní cenovou úroveň.

| Název | Omezení | Poznámky | 
| ---- | ----- | ----- | 
| Jakýkoli typ artefaktů | 500 | Typy artefaktů obsahují smlouvy, partnery, schémata, certifikáty a mapy. Každý typ může obsahovat až do maximálního počtu artefaktů. | 
|||| 

### <a name="b2b-protocols-as2-x12-edifact-message-size"></a>Velikost zprávy protokoly B2B (AS2, X12, EDIFACT)

Zde jsou omezení, která se týkají B2B protokoly:

| Název | Omezení | Poznámky | 
| ---- | ----- | ----- | 
| AS2 | 50 MB | Kódování a dekódování se vztahuje | 
| X12 | 50 MB | Kódování a dekódování se vztahuje | 
| EDIFACT | 50 MB | Kódování a dekódování se vztahuje | 
|||| 

<a name="configuration"></a>

## <a name="configuration-ip-addresses"></a>Konfigurace: IP adresy

### <a name="logic-apps-service"></a>Služba aplikace logiky

Volání, které aplikace logiky přímo provede, který je prostřednictvím [HTTP](../connectors/connectors-native-http.md) nebo [HTTP + Swagger](../connectors/connectors-native-http-swagger.md) nebo jiné požadavky HTTP pocházet z IP adresy v tomto seznamu.

|Oblasti aplikace logiky|Odchozí IP|
|-----------------|-----------|
|Austrálie – východ|13.75.149.4, 104.210.91.55, 104.210.90.241|
|Austrálie – jihovýchod|13.73.114.207, 13.77.3.139, 13.70.159.205|
|Brazílie – jih|191.235.82.221, 191.235.91.7, 191.234.182.26|
|Střední Kanada|52.233.29.92, 52.228.39.241, 52.228.39.244|
|Východní Kanada|52.232.128.155, 52.229.120.45, 52.229.126.25|
|Střed Indie|52.172.154.168, 52.172.186.159, 52.172.185.79|
|Střed USA|13.67.236.125, 104.208.25.27, 40.122.170.198|
|Východní Asie|13.75.94.173, 40.83.127.19, 52.175.33.254|
|Východ USA|13.92.98.111, 40.121.91.41, 40.114.82.191|
|Východní USA 2|40.84.30.147, 104.208.155.200, 104.208.158.174|
|Japonsko – východ|13.71.158.3, 13.73.4.207, 13.71.158.120|
|Japonsko – západ|40.74.140.4, 104.214.137.243, 138.91.26.45|
|Střed USA – sever|168.62.248.37, 157.55.210.61, 157.55.212.238|
|Severní Evropa|40.113.12.95, 52.178.165.215, 52.178.166.21|
|Střed USA – jih|104.210.144.48, 13.65.82.17, 13.66.52.232|
|Jihovýchodní Asie|13.76.133.155, 52.163.228.93, 52.163.230.166|
|Indie – jih|52.172.50.24, 52.172.55.231, 52.172.52.0|
|Západní střed USA|52.161.27.190, 52.161.18.218, 52.161.9.108|
|Západní Evropa|40.68.222.65, 40.68.209.23, 13.95.147.65|
|Indie – západ|104.211.164.80, 104.211.162.205, 104.211.164.136|
|Západní USA|52.160.92.112, 40.118.244.241, 40.118.241.243|
|Západní USA 2|13.66.210.167, 52.183.30.169, 52.183.29.132|
|Spojené království – jih|51.140.74.14, 51.140.73.85, 51.140.78.44|
|Spojené království – západ|51.141.54.185, 51.141.45.238, 51.141.47.136|
| | |

### <a name="connectors"></a>Konektory

Volání, [konektory](../connectors/apis-list.md) zkontrolujte pocházet z IP adresy v tomto seznamu.

|Oblasti aplikace logiky|Odchozí IP|
|-----------------|-----------|
|Austrálie – východ|40.126.251.213|
|Austrálie – jihovýchod|40.127.80.34|
|Brazílie – jih|191.232.38.129|
|Střední Kanada|52.233.31.197, 52.228.42.205, 52.228.33.76, 52.228.34.13|
|Východní Kanada|52.229.123.98, 52.229.120.178, 52.229.126.202, 52.229.120.52|
|Střed Indie|104.211.98.164|
|Střed USA|40.122.49.51|
|Východní Asie|23.99.116.181|
|Východ USA|191.237.41.52|
|Východní USA 2|104.208.233.100|
|Japonsko – východ|40.115.186.96|
|Japonsko – západ|40.74.130.77|
|Střed USA – sever|65.52.218.230|
|Severní Evropa|104.45.93.9|
|Střed USA – jih|104.214.70.191|
|Jihovýchodní Asie|13.76.231.68|
|Indie – jih|104.211.227.225|
|Západní Evropa|40.115.50.13|
|Indie – západ|104.211.161.203|
|Západní USA|104.40.51.248|
|Spojené království – jih|51.140.80.51|
|Spojené království – západ|51.141.47.105|
| | | 

## <a name="next-steps"></a>Další postup  

* [Vytvoření první aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)  
* [Běžných příkladů a scénářů](../logic-apps/logic-apps-examples-and-scenarios.md)
* [Video: Automatizovat firemní procesy s Logic Apps](http://channel9.msdn.com/Events/Build/2016/T694) 
* [Video: Integrovat systémy s Logic Apps](http://channel9.msdn.com/Events/Build/2016/P462)
