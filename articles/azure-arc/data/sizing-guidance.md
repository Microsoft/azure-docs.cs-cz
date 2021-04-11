---
title: Pokyny pro stanovení velikosti
description: Naplánujte velikost nasazení datových služeb s podporou ARC Azure.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 3bbd778eabf150b734b04e004006dfeea2254ec4
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106077464"
---
# <a name="sizing-guidance"></a>Pokyny pro změnu velikosti

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="overview-of-sizing-guidance"></a>Přehled pokynů pro změnu velikosti

Při plánování nasazení datových služeb Azure ARC byste měli naplánovat správné množství výpočetních prostředků, paměti a úložiště, které bude nutné pro spuštění řadiče dat ARC Azure a pro počet PostgreSQL skupin serverů s ochranou velikostí, které budete nasazovat.  Vzhledem k tomu, že datové služby s podporou ARC Azure jsou nasazené na Kubernetes, můžete v průběhu času přidáním dalších výpočetních uzlů nebo úložiště zvýšit kapacitu do clusteru Kubernetes.  Tato příručka poskytuje pokyny pro minimální požadavky a poskytuje pokyny pro doporučené velikosti pro některé běžné požadavky.

## <a name="general-sizing-requirements"></a>Požadavky na obecné velikosti

> [!NOTE]
> Pokud nejste obeznámeni s koncepty v tomto článku, můžete si přečíst další informace o [správě prostředků Kubernetes](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/) a [zápisu Kubernetes velikosti](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/#resource-units-in-kubernetes).

Počty jader musí být celočíselná hodnota větší nebo rovna hodnotě jedna.

Při použití azdata pro nasazení by se měly hodnoty paměti zadat v mocnině dvou čísel, tj. pomocí přípon: Ki, mi nebo GI.

Mezní hodnoty musí být vždy větší než hodnota požadavku, pokud je zadána.

Mezní hodnoty pro jádra jsou fakturovatelná metrika na spravované instanci SQL a PostgreSQL skupiny serverů s možností škálování.

## <a name="minimum-deployment-requirements"></a>Minimální požadavky na nasazení

Minimální velikost nasazení datových služeb s podporou Azure ARC je možné považovat za řadič dat ARC Azure a jednu spravovanou instanci SQL a jednu PostgreSQL skupinu serverů s jedním škálováním na dva pracovní uzly.  Pro tuto konfiguraci potřebujete aspoň 16 GB paměti RAM a 4 jádra _dostupné_ kapacity v clusteru Kubernetes.  Měli byste zajistit, abyste měli minimální velikost uzlu Kubernetes 8 GB paměti RAM a 4 jádra a celkovou kapacitu 16 GB paměti RAM dostupnou ve všech uzlech Kubernetes.  Například můžete mít 1 uzel v 32 GB paměti RAM a 4 jádra nebo máte 2 uzly s 16GB pamětí RAM a 4 jádry.

Podrobnosti o velikosti úložiště najdete v článku věnovaném [konfiguraci úložiště](storage-configuration.md) .

## <a name="data-controller-sizing-details"></a>Podrobnosti o velikosti řadiče dat

Řadič dat je kolekce lusků, které se nasazují do vašeho clusteru Kubernetes, aby poskytovaly rozhraní API, službu kontroleru, zaváděcí nástroj a databáze a řídicí panely pro monitorování.  Tato tabulka popisuje výchozí hodnoty pro paměť a požadavky a omezení procesoru.

|Název pod|Požadavek procesoru|Požadavek na paměť|Limit procesoru|Omezení paměti|Poznámky|
|---|---|---|---|---|---|
|**zaváděcího nástroje**|100 milionů|100Mi|200m|200Mi||
|**nad**|400m|2Gi|1800m|2Gi||
|**controldb**|200m|3Gi|800m|6Gi||
|**controlwd**|10 milionů|100Mi|100 milionů|200Mi||
|**logsdb**|200m|1600Mi|2|1600Mi||
|**logsui**|100 milionů|500Mi|2|2Gi||
|**metricsdb**|200m|800Mi|400m|2Gi||
|**metricsdc**|100 milionů|200Mi|200m|300Mi|Metricsdc je daemonset, který se vytvoří na každém uzlu Kubernetes v clusteru.  Čísla v tabulce tady jsou _na jeden uzel_. Pokud jste v souboru profilu nasazení nastavili allowNodeMetricsCollection = false před vytvořením kontroleru dat, metricsdc daemonset se nevytvoří.|
|**metricsui**|20 milionů|200Mi|500 mil|200Mi||
|**mgmtproxy**|200m|250Mi|500 mil|500Mi||

Můžete přepsat výchozí nastavení pro controldb a ovládací prvky lusků v souboru profilu nasazení nebo souboru YAML datacontroller.  Příklad:

```yaml
  resources:
    controller:
      limits:
        cpu: "1000m"
        memory: "3Gi"
      requests:
        cpu: "800m"
        memory: "2Gi"
    controllerDb:
      limits:
        cpu: "800m"
        memory: "8Gi"
      requests:
        cpu: "200m"
        memory: "4Gi"
```

Podrobnosti o velikosti úložiště najdete v článku věnovaném [konfiguraci úložiště](storage-configuration.md) .

## <a name="sql-managed-instance-sizing-details"></a>Podrobnosti o velikosti spravované instance SQL

Každá spravovaná instance SQL musí mít následující minimální požadavky na prostředky:
- Paměť: 2Gi
- Jádra: 1

Každá vytvořená instance SQL pod vytvořením má tři kontejnery:

|Název kontejneru|Požadavek procesoru|Požadavek na paměť|Limit procesoru|Omezení paměti|Poznámky|
|---|---|---|---|---|---|
|fluentbit|100 milionů|100Mi|Neurčeno|Neurčeno|Požadavky na prostředky kontejneru fluentbit jsou _kromě_ požadavků určených pro SPRAVOVANOU instanci SQL.|
|oblouk – sqlmi|Zadáno uživatelem nebo není zadán.|Zadáno uživatelem nebo není zadán.|Zadáno uživatelem nebo není zadán.|Zadáno uživatelem nebo není zadán.|
|shromážděná výkonnostní|Neurčeno|Neurčeno|Neurčeno|Neurčeno|

Výchozí velikost svazku pro všechny trvalé svazky je 5Gi.

## <a name="postgresql-hyperscale-server-group-sizing-details"></a>PostgreSQL podrobnosti o velikosti skupiny serverů s škálovatelným škálováním

Každý uzel PostgreSQL skupiny serverů s rozšířením na úrovni serveru musí obsahovat následující minimální požadavky na prostředky:
- Paměť: 256Mi
- Jádra: 1

Každý PostgreSQL koordinátor skupiny serverů nebo pracovní proces, který je vytvořen, má tři kontejnery:

|Název kontejneru|Požadavek procesoru|Požadavek na paměť|Limit procesoru|Omezení paměti|Poznámky|
|---|---|---|---|---|---|
|fluentbit|100 milionů|100Mi|Neurčeno|Neurčeno|Požadavky na prostředky kontejneru fluentbit jsou _kromě_ požadavků určených pro uzly skupin serverů PostgreSQL s rozšířením.|
|postgres|Zadáno uživatelem nebo není zadán.|Zadáno uživatelem nebo 256Mi (výchozí).|Zadáno uživatelem nebo není zadán.|Zadáno uživatelem nebo není zadán.||
|telegraf|Neurčeno|Neurčeno|Neurčeno|Neurčeno||

## <a name="cumulative-sizing"></a>Kumulativní Změna velikosti

Celková velikost prostředí vyžadovaného pro datové služby s podporou ARC Azure je primárně funkcí počtu a velikosti instancí databáze, které budou vytvořeny.  Celková velikost může být obtížné předem předpovědět, pokud se počet instancí zvětšuje a zmenšuje a množství prostředků, které jsou potřeba pro jednotlivé instance databáze, se změní.

Základní velikost pro dané prostředí datových služeb s podporou ARC Azure má velikost řadiče dat, který vyžaduje 4 jádra a 16 GB paměti RAM.  Odtud můžete na kumulativní součet jader a paměti požadované pro instance databáze přidat nahoru.  U spravované instance SQL se počet lusků rovná počtu vytvořených spravovaných instancí SQL.  U PostgreSQL serverů s škálovatelným škálováním se počet lusků rovná počtu pracovních uzlů a jeden pro uzel koordinátora.  Pokud například máte skupinu serverů PostgreSQL se 3 pracovními uzly, bude celkový počet lusků 4.

Kromě jader a paměti, které požadujete pro každou instanci databáze, byste měli přidat 250my jader a 250Mi paměti RAM pro kontejnery agentů.

Následuje příklad výpočtu velikosti.

Požadavky:

- **"SQL1"**: 1 spravovaná instance SQL s 16 GB paměti RAM, 4 jádra
- **"Sql2"**: 1 spravovaná instance SQL s 256 GB paměti RAM, 16 jader
- **"Postgres1"**: 1 PostgreSQL skupina serverů s škálovatelným škálováním a 4 pracovními procesy s 12 GB paměti RAM, 4 jádry

Výpočty velikosti:

- Velikost "SQL1" je: 1 pod * ([16 GI RAM, 4 jádra] + [250Mi RAM, 250m jádra]) pro agenty na jeden pod = 16,25 GI RAM, 4,25 jader.
- Velikost "SQL2" je: 1 pod * ([256 GI RAM, 16 jader] + + [250Mi RAM, 250m jádra]) pro agenty na pod = 256,25 GI RAM, 16,25 jader.
- Celková velikost SQL 1 a SQL 2 je: (16,25 GB + 256,25 GI) = 272,5 GB RAM, (4,25 jádra + 16,25 jader) = 20,5 jader.

- Velikost "Postgres1" je: (4 pracovní procesy + 1 koordinátor pod) * ([12 GB RAM, 4 jádra] + [250Mi RAM, 250m cores]) pro agenty na jeden pod = 61,25 GB RAM, 21,25 jader.

- Celková kapacita požadovaná pro tyto instance databáze je: 272,5 GB RAM, 20,5 jader pro SQL + 61,25 GB RAM, 21,25 jádra pro PostgreSQL, škálovatelné GB RAM, 333,75 jader.

- Celková kapacita požadovaná pro instance databáze a řadič dat je: 333,75 GB RAM, 42,5 jader pro instance databáze + 16 GB RAM, 4 jádra pro datový adaptér = 349,75 GB RAM, 46,5 jader.

Podrobnosti o velikosti úložiště najdete v článku věnovaném [konfiguraci úložiště](storage-configuration.md) .

## <a name="other-considerations"></a>Další důležité informace

Pamatujte, že daná žádost o velikost instance databáze pro jádra nebo paměť RAM nemůže překročit dostupnou kapacitu uzlů Kubernetes v clusteru.  Pokud například největší uzel Kubernetes, který máte v clusteru Kubernetes, je 256 GB paměti RAM a 24 jader, nebudete moci vytvořit instanci databáze s požadavkem 512 GB paměti RAM a 48 jader.  

Je vhodné udržovat aspoň 25% dostupné kapacity napříč Kubernetes uzly, aby bylo možné Kubernetes efektivně naplánovat vytváření lusků, a zajistit tak Elastické škálování a delší dobu růstu na vyžádání.  

V případě výpočtů velikosti nezapomeňte přidat v požadavcích na prostředky v Kubernetes systému a všech dalších úlohách, které mohou sdílet kapacitu s datovými službami s podporou ARC Azure na stejném clusteru Kubernetes.

Aby se zajistila vysoká dostupnost během plánované údržby a kontinuity havárií, měli byste naplánovat aspoň jeden z uzlů Kubernetes v clusteru, aby se v daném časovém okamžiku nedostaly k dispozici.  Kubernetes se pokusí znovu naplánovat lusky spuštěné v daném uzlu, který se vypnul za údržbu, nebo z důvodu chyby.  Pokud ve zbývajících uzlech není dostupná kapacita, nebudete moct vytvořit nové, dokud nebude dostupná kapacita.  Buďte velmi opatrní s velkými instancemi databáze.  Například pokud existuje pouze jeden uzel Kubernetes dostatečně velký pro splnění požadavků na prostředky pro velkou instanci databáze a tento uzel selže, Kubernetes nebude moci naplánovat instanci databáze pod na jiný uzel Kubernetes.

Mějte na paměti, že [maximální omezení velikosti clusteru Kubernetes](https://kubernetes.io/docs/setup/best-practices/cluster-large/) .

Váš správce Kubernetes možná nastavil v oboru názvů nebo projektu [kvóty prostředků](https://kubernetes.io/docs/concepts/policy/resource-quotas/) .  Při plánování velikosti instancí databáze mějte na paměti tyto otázky.
