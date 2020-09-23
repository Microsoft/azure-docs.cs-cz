---
title: Řešení potíží s PostgreSQL skupinami serverů s škálovatelným škálováním
description: Řešení potíží s PostgreSQLmi skupinami serverů s škálovatelným škálováním pomocí Jupyter Notebook
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 9c0d3d9c74be8dabaec20ff5d4c7e7cfc74d8eef
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90936051"
---
# <a name="troubleshooting-postgresql-hyperscale-server-groups"></a>Řešení potíží s PostgreSQL skupinami serverů s škálovatelným škálováním

Poznámkové bloky můžou zdokumentovat postupy včetně obsahu Markdownu, který popisuje, jak to udělat. Může také poskytnout spustitelný kód pro automatizaci procedury.  Tento model je vhodný pro vše od standardních operačních postupů až po Průvodce odstraňováním potíží.

Pojďme například řešit potíže s PostgreSQL skupinou serverů s škálovatelným škálováním, které mohou mít problémy s použitím Azure Data Studio.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="install-tools"></a>Instalace nástrojů

Nainstalujte Azure Data Studio `kubectl` a `azdata` na klientském počítači, který používáte ke spuštění poznámkového bloku v Azure Data Studio. Postupujte prosím podle pokynů v tématu [instalace klientských nástrojů](install-client-tools.md)

## <a name="update-the-path-environment-variable"></a>Aktualizuje proměnnou prostředí PATH.

Ujistěte se, že tyto nástroje lze vyvolat odkudkoli na tomto klientském počítači. Například na klientském počítači s Windows aktualizujte proměnnou prostředí PATH systému a přidejte složku, do které jste nainstalovali kubectl.

## <a name="sign-in-with-azdata"></a>Přihlásit se pomocí `azdata`

Přihlaste se k řadiči dat ARC z tohoto klientského počítače a před spuštěním Azure Data Studio. Uděláte to tak, že spustíte příkaz jako:

```console
azdata login --endpoint https://<IP address>:<port>
```

Nahraďte `<IP address>` IP adresou vašeho clusteru Kubernetes a `<port>` portem, na kterém Kubernetes naslouchá. Zobrazí se výzva k zadání uživatelského jména a hesla. Chcete-li zobrazit další podrobnosti, spusťte příkaz: _

```console
azdata login --help
```

## <a name="log-into-your-kubernetes-cluster-with-kubectl"></a>Přihlášení ke clusteru Kubernetes pomocí kubectl

K tomu můžete použít ukázkové příkazy uvedené v [tomto](https://blog.christianposta.com/kubernetes/logging-into-a-kubernetes-cluster-with-kubectl/) blogovém příspěvku.
Mohli byste spustit například následující příkazy:

```console
kubectl config view
kubectl config set-credentials kubeuser/my_kubeuser --username=<your Arc Data Controller Admin user name> --password=<password>
kubectl config set-cluster my_kubeuser --server=https://<IP address>:<port>
kubectl config set-context default/my_kubeuser/ArcDataControllerAdmin --user=ArcDataControllerAdmin/my_kubeuser --namespace=arc --cluster=my_kubeuser
kubectl config use-context default/my_kubeuser/ArcDataControllerAdmin
```

### <a name="the-troubleshooting-notebook"></a>Poznámkový blok řešení potíží

Spusťte Azure Data Studio a otevřete Poznámkový blok řešení potíží. 

Implementujte kroky popsané v tématu  [033-Manage-Postgres-with-AzureDataStudio.MD](manage-postgresql-hyperscale-server-group-with-azure-data-studio.md) :

1. Připojení k řadiči dat ARC
2. Pravým tlačítkem vyberte instanci Postgres a zvolte **[spravovat]**
3. Výběr **řídicího panelu [Diagnostika a řešení problémů]**
4. Vyberte **odkaz [Poradce při potížích]** .

:::image type="content" source="media/postgres-hyperscale/ads-controller-postgres-troubleshooting-notebook.jpg" alt-text="Azure Data Studio – otevřít poznámkový blok pro řešení potíží s PostgreSQL":::

TSG100 – Poznámkový **blok pro poradce při potížích s PostgreSQLem s podporou škálování na více koncových akcích ve službě Azure ARC** : :::image type="content" source="media/postgres-hyperscale/ads-controller-postgres-troubleshooting-notebook2.jpg" alt-text="Azure Data Studio – použití PostgreSQL":::

### <a name="run-the-scripts"></a>Spuštění skriptů
Vyberte tlačítko spustit vše v horní části pro spuštění poznámkového bloku najednou, nebo můžete krokovat a provádět každou buňku kódu jednu po druhém.

Zobrazit výstup z provádění buněk kódu s případnými případnými problémy.

Do poznámkového bloku přidáváme další podrobnosti o tom, jak rozpoznat běžné problémy a jak je řešit.

## <a name="next-step"></a>Další krok
- Přečtěte si o [získání protokolů pro datové služby s podporou ARC Azure](troubleshooting-get-logs.md) .
- Přečtěte si o [hledání protokolů pomocí Kibana](monitor-grafana-kibana.md)
- Přečtěte si o [monitorování pomocí Grafana](monitor-grafana-kibana.md)
- Vytvoření vlastních poznámkových bloků
