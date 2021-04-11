---
title: Zobrazení protokolů a metrik pomocí Kibana a Grafana
description: Zobrazení protokolů a metrik pomocí Kibana a Grafana
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 12/08/2020
ms.topic: how-to
ms.openlocfilehash: cb53aba300b933c78d9ac2f5fc5cf8054f3413e3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "104669997"
---
# <a name="view-logs-and-metrics-using-kibana-and-grafana"></a>Zobrazení protokolů a metrik pomocí Kibana a Grafana

Webové řídicí panely Kibana a Grafana umožňují získat jasný přehled o oborech názvů Kubernetes používaných datovými službami s podporou služby Azure Arc.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]


## <a name="monitor-azure-sql-managed-instances-on-azure-arc"></a>Monitorování spravovaných instancí Azure SQL v Arc Azure

Pokud chcete získat přístup k řídicím panelům protokoly a monitorování pro spravovanou instanci SQL s povoleným obloukem, spusťte následující `azdata` příkaz CLI.

```bash

azdata arc sql endpoint list -n <name of SQL instance>

```
Příslušné řídicí panely Grafana:

* "Metriky spravované instance Azure SQL"
* "Metriky hostitelských uzlů"
* "Metriky" hostitele pro lusky "


> [!NOTE]
>  Až se zobrazí výzva k zadání uživatelského jména a hesla, zadejte uživatelské jméno a heslo, které jste zadali v době, kdy jste vytvořili řadič dat ARC Azure.

> [!NOTE]
>  Zobrazí se výzva s upozorněním certifikátu, protože certifikáty používané ve verzi Preview jsou certifikáty podepsané svým držitelem.


## <a name="monitor-azure-database-for-postgresql-hyperscale-on-azure-arc"></a>Monitorování Azure Database for PostgreSQLho škálování na ARC Azure

Pokud chcete získat přístup k řídicím panelům protokolů a monitorování pro PostgreSQL, spusťte následující `azdata` příkaz CLI.

```bash

azdata arc postgres endpoint list -n <name of postgreSQL instance>

```

Příslušné řídicí panely postgreSQL:

* "Postgres metriky"
* "Postgres metriky tabulky"
* "Metriky hostitelských uzlů"
* "Metriky" hostitele pro lusky "


## <a name="additional-firewall-configuration"></a>Další konfigurace brány firewall

V závislosti na tom, kde je řadič dat nasazený, možná zjistíte, že v bráně firewall musíte otevřít porty pro přístup k koncovým bodům Kibana a Grafana.

Tady je příklad toho, jak to udělat pro virtuální počítač Azure. Pokud jste Kubernetes nasadili pomocí skriptu, budete ho muset provést.

Následující postup ukazuje, jak vytvořit pravidlo NSG pro koncové body Kibana a Grafana:

### <a name="find-the-name-of-the-nsg"></a>Zjištění názvu skupiny zabezpečení sítě

```azurecli
az network nsg list -g azurearcvm-rg --query "[].{NSGName:name}" -o table
```

### <a name="add-the-nsg-rule"></a>Přidat pravidlo NSG

Jakmile budete mít název NSG, můžete pravidlo přidat pomocí následujícího příkazu:

```azurecli
az network nsg rule create -n ports_30777 --nsg-name azurearcvmNSG --priority 600 -g azurearcvm-rg --access Allow --description 'Allow Kibana and Grafana ports' --destination-address-prefixes '*' --destination-port-ranges 30777 --direction Inbound --protocol Tcp --source-address-prefixes '*' --source-port-ranges '*'
```


## <a name="next-steps"></a>Další kroky
- Zkuste [nahrávat metriky a protokoly a Azure monitor](upload-metrics-and-logs-to-azure-monitor.md)
- Přečtěte si o Grafana:
   - [Začínáme](https://grafana.com/docs/grafana/latest/getting-started/getting-started)
   - [Základy Grafana](https://grafana.com/tutorials/grafana-fundamentals/#1)
   - [Kurzy Grafana](https://grafana.com/tutorials/grafana-fundamentals/#1)
- Přečtěte si o Kibana
   - [Úvod](https://www.elastic.co/webinars/getting-started-kibana?baymax=default&elektra=docs&storm=top-video)
   - [Průvodce Kibana](https://www.elastic.co/guide/en/kibana/current/index.html)
   - [Seznámení s podrobnostmi o řídicím panelu s vizualizacemi dat v Kibana](https://www.elastic.co/webinars/dashboard-drilldowns-with-data-visualizations-in-kibana/)
   - [Postup sestavení řídicích panelů Kibana](https://www.elastic.co/webinars/how-to-build-kibana-dashboards/)

