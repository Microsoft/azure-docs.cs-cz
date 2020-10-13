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
ms.openlocfilehash: 8e91a611084d201e6609f7e203eaa08c81e19a00
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91569998"
---
# <a name="troubleshooting-postgresql-hyperscale-server-groups"></a>Řešení potíží s PostgreSQL skupinami serverů s škálovatelným škálováním
Tento článek popisuje některé postupy, které můžete použít k řešení potíží se skupinou serverů. Kromě tohoto článku si můžete přečíst, jak používat [Kibana](monitor-grafana-kibana.md) k hledání protokolů, nebo k vizualizaci metriky o skupině serverů pomocí [Grafana](monitor-grafana-kibana.md) . 

## <a name="getting-more-details-about-the-execution-of-an-azdata-command"></a>Získání dalších podrobností o spuštění příkazu azdata
Můžete přidat parametr **--Debug** k jakémukoli azdata příkazu, který spustíte. Provedete to tak, že v konzole zobrazíte další informace o spuštění tohoto příkazu. Měli byste najít informace, které vám pomůžou pochopit, jak se chování tohoto příkazu hodí.
Můžete například spustit
```console
azdata arc postgres server create -n postgres01 -w 2 --debug
```

nebo
```console
azdata arc postgres server edit -n postgres01 --extension SomeExtensionName --debug
```

Kromě toho můžete pomocí parametru--Help na jakémkoli příkazu azdata zobrazit nápovědu, seznam parametrů pro konkrétní příkaz. Například:
```console
azdata arc postgres server create --help
```


## <a name="collecting-logs-of-the-data-controller-and-your-server-groups"></a>Shromažďování protokolů řadiče dat a skupin serverů
Přečtěte si článek o [získání protokolů pro datové služby s podporou ARC Azure](troubleshooting-get-logs.md) .



## <a name="interactive-troubleshooting-with-jupyter-notebooks-in-azure-data-studio"></a>Interaktivní řešení potíží s Jupyter poznámkovým blokem v Azure Data Studio
Poznámkové bloky můžou dokumentovat postupy zahrnutím obsahu Markdown s popisem, co a jak se má udělat. Také můžou poskytovat spustitelný kód pro automatizaci určitého postupu.  Tento model je užitečný pro cokoli od standardních provozních postupů až po průvodce odstraňováním potíží.

Pojďme například řešit potíže s PostgreSQL skupinou serverů s škálovatelným škálováním, které mohou mít problémy s použitím Azure Data Studio.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

### <a name="install-tools"></a>Instalace nástrojů

Nainstalujte Azure Data Studio `kubectl` a `azdata` na klientském počítači, který používáte ke spuštění poznámkového bloku v Azure Data Studio. Postupujte prosím podle pokynů v tématu [instalace klientských nástrojů](install-client-tools.md)

### <a name="update-the-path-environment-variable"></a>Aktualizuje proměnnou prostředí PATH.

Ujistěte se, že tyto nástroje lze vyvolat odkudkoli na tomto klientském počítači. Například na klientském počítači s Windows aktualizujte proměnnou prostředí PATH systému a přidejte složku, do které jste nainstalovali kubectl.

### <a name="sign-in-with-azdata"></a>Přihlásit se pomocí `azdata`

Přihlaste se k řadiči dat ARC z tohoto klientského počítače a před spuštěním Azure Data Studio. Uděláte to tak, že spustíte příkaz jako:

```console
azdata login --endpoint https://<IP address>:<port>
```

Nahraďte `<IP address>` IP adresou vašeho clusteru Kubernetes a `<port>` portem, na kterém Kubernetes naslouchá. Zobrazí se výzva k zadání uživatelského jména a hesla. Chcete-li zobrazit další podrobnosti, spusťte příkaz: _

```console
azdata login --help
```

### <a name="log-into-your-kubernetes-cluster-with-kubectl"></a>Přihlášení ke clusteru Kubernetes pomocí kubectl

K tomu můžete použít ukázkové příkazy uvedené v [tomto](https://blog.christianposta.com/kubernetes/logging-into-a-kubernetes-cluster-with-kubectl/) blogovém příspěvku.
Mohli byste spustit například následující příkazy:

```console
kubectl config view
kubectl config set-credentials kubeuser/my_kubeuser --username=<your Arc Data Controller Admin user name> --password=<password>
kubectl config set-cluster my_kubeuser --server=https://<IP address>:<port>
kubectl config set-context default/my_kubeuser/ArcDataControllerAdmin --user=ArcDataControllerAdmin/my_kubeuser --namespace=arc --cluster=my_kubeuser
kubectl config use-context default/my_kubeuser/ArcDataControllerAdmin
```

#### <a name="the-troubleshooting-notebook"></a>Poznámkový blok řešení potíží

Spusťte Azure Data Studio a otevřete Poznámkový blok řešení potíží. 

Implementujte kroky popsané v tématu  [033-Manage-Postgres-with-AzureDataStudio.MD](manage-postgresql-hyperscale-server-group-with-azure-data-studio.md) :

1. Připojení k řadiči dat ARC
2. Pravým tlačítkem vyberte instanci Postgres a zvolte **[spravovat]**
3. Výběr **řídicího panelu [Diagnostika a řešení problémů]**
4. Vyberte **odkaz [Poradce při potížích]** .

:::image type="content" source="media/postgres-hyperscale/ads-controller-postgres-troubleshooting-notebook.jpg" alt-text="Azure Data Studio – otevřít poznámkový blok pro řešení potíží s PostgreSQL":::

TSG100 – Poznámkový **blok pro poradce při potížích s PostgreSQLem s podporou škálování na více koncových akcích ve službě Azure ARC** : :::image type="content" source="media/postgres-hyperscale/ads-controller-postgres-troubleshooting-notebook2.jpg" alt-text="Azure Data Studio – otevřít poznámkový blok pro řešení potíží s PostgreSQL":::

#### <a name="run-the-scripts"></a>Spuštění skriptů
Vyberte tlačítko spustit vše v horní části pro spuštění poznámkového bloku najednou, nebo můžete krokovat a provádět každou buňku kódu jednu po druhém.

Zobrazit výstup z provádění buněk kódu s případnými případnými problémy.

Do poznámkového bloku přidáváme další podrobnosti o tom, jak rozpoznat běžné problémy a jak je řešit.

## <a name="next-step"></a>Další krok
- Přečtěte si o [získání protokolů pro datové služby s podporou ARC Azure](troubleshooting-get-logs.md) .
- Přečtěte si o [hledání protokolů pomocí Kibana](monitor-grafana-kibana.md)
- Přečtěte si o [monitorování pomocí Grafana](monitor-grafana-kibana.md)
- Vytvoření vlastních poznámkových bloků
