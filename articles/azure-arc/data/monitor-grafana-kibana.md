---
title: Zobrazení protokolů a metrik pomocí Kibana a Grafana
description: Zobrazení protokolů a metrik pomocí Kibana a Grafana
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 3693c30a34601512770f5d9071f5d786410fb00e
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2020
ms.locfileid: "92360373"
---
# <a name="view-logs-and-metrics-using-kibana-and-grafana"></a>Zobrazení protokolů a metrik pomocí Kibana a Grafana

Webové řídicí panely Kibana a Grafana umožňují získat jasný přehled o oborech názvů Kubernetes používaných datovými službami s podporou služby Azure Arc.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="retrieve-the-ip-address-of-your-cluster"></a>Načtení IP adresy vašeho clusteru

Pro přístup k řídicím panelům budete potřebovat načíst IP adresu vašeho clusteru. Metoda načtení správné IP adresy se liší v závislosti na tom, jak jste zvolili nasazení Kubernetes. Projděte si následující možnosti, abyste našli ten správný.

### <a name="azure-virtual-machine"></a>Virtuální počítač Azure

Pokud chcete načíst veřejnou IP adresu, použijte následující příkaz:

```azurecli
az network public-ip list -g azurearcvm-rg --query "[].{PublicIP:ipAddress}" -o table
```

### <a name="kubeadm-cluster"></a>Cluster Kubeadm

Pokud chcete načíst IP adresu clusteru, použijte následující příkaz:

```console
kubectl cluster-info
```


### <a name="aks-or-other-load-balanced-cluster"></a>AKS nebo jiný cluster s vyrovnáváním zatížení

Pokud chcete monitorovat prostředí v AKS nebo jiném clusteru s vyrovnáváním zatížení, musíte získat IP adresu služby proxy pro správu. Pomocí tohoto příkazu načtěte **externí IP** adresu:

```console
kubectl get svc mgmtproxy-svc-external -n <namespace>

#Example:
#kubectl get svc mgmtproxy-svc-external -n arc
NAME                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)           AGE
mgmtproxy-svc-external   LoadBalancer   10.0.186.28   52.152.148.25   30777:30849/TCP   19h
```

## <a name="additional-firewall-configuration"></a>Další konfigurace brány firewall

Možná zjistíte, že pro přístup k koncovým bodům Kibana a Grafana budete muset otevřít porty na bráně firewall.

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

## <a name="monitor-azure-sql-managed-instances-on-azure-arc"></a>Monitorování spravovaných instancí Azure SQL v Arc Azure

Teď, když máte IP adresu a vystavené porty byste měli mít přístup k Grafana a Kibana.

> [!NOTE]
>  Až se zobrazí výzva k zadání uživatelského jména a hesla, zadejte uživatelské jméno a heslo, které jste zadali v době, kdy jste vytvořili řadič dat ARC Azure.

> [!NOTE]
>  Zobrazí se výzva s upozorněním certifikátu, protože certifikáty používané ve verzi Preview jsou certifikáty podepsané svým držitelem.

Pro přístup k řídicím panelům protokolování a monitorování pro spravovanou instanci Azure SQL použijte následující vzor adresy URL:

```html
https://<external-ip-from-above>:30777/grafana
https://<external-ip-from-above>:30777/kibana
```

Důležité jsou tyto řídicí panely:

* "Metriky spravované instance Azure SQL"
* "Metriky hostitelských uzlů"
* "Metriky" hostitele pro lusky "

## <a name="monitor-azure-database-for-postgresql-hyperscale---azure-arc"></a>Monitorování Azure Database for PostgreSQL škálování – Azure ARC

Použijte následující vzor adresy URL pro přístup k řídicím panelům protokolování a monitorování pro PostgreSQL s měřítkem:

```html
https://<external-ip-from-above>:30777/grafana
https://<external-ip-from-above>:30777/kibana
```

Důležité jsou tyto řídicí panely:

* "Postgres metriky"
* "Postgres metriky tabulky"
* "Metriky hostitelských uzlů"
* "Metriky" hostitele pro lusky "

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

