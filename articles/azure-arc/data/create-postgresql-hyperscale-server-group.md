---
title: Vytvoření Azure Database for PostgreSQL skupiny serverů s škálovatelným škálováním na ARC Azure
description: Vytvoření Azure Database for PostgreSQL skupiny serverů s škálovatelným škálováním na ARC Azure
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: d432f29e91097491fc4719ec59a11cb96948f431
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/16/2020
ms.locfileid: "97609050"
---
# <a name="create-an-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Vytvoření skupiny serverů PostgreSQL Hyperscale s podporou služby Azure Arc

Tento dokument popisuje kroky pro vytvoření PostgreSQL skupiny serverů s škálovatelným škálováním na Azure ARC.

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="getting-started"></a>Začínáme
Pokud jste již obeznámeni s tématy níže, můžete tento odstavec vynechat.
Než budete pokračovat v vytváření, můžete si přečíst důležitá témata:
- [Přehled datových služeb s podporou ARC Azure](overview.md)
- [Režimy připojení a požadavky](connectivity.md)
- [Koncepty konfigurace úložiště a Kubernetes úložiště](storage-configuration.md)
- [Model prostředků Kubernetes](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)

Pokud dáváte přednost tomu, abyste si vyzkoušeli nějaké možnosti bez zřízení celého prostředí, začněte rychle začít pomocí [Azure ARC rychlé zprovoznění](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) ve službě Azure Kubernetes Service (AKS), AWS elastické KUBERNETES (EKS), Google Cloud Kubernetes Engine (GKE) nebo na virtuálním počítači Azure.


## <a name="login-to-the-azure-arc-data-controller"></a>Přihlášení k řadiči dat ARC Azure

Než budete moct vytvořit instanci, musíte se nejdřív přihlásit k řadiči dat ARC Azure. Pokud jste už přihlášení k řadiči dat, můžete tento krok přeskočit.

```console
azdata login
```

Pak se zobrazí výzva k zadání uživatelského jména, hesla a oboru názvů System.  

> Pokud jste použili skript k vytvoření kontroleru dat, váš obor názvů by měl být **ARC** .

```console
Namespace: arc
Username: arcadmin
Password:
Logged in successfully to `https://10.0.0.4:30080` in namespace `arc`. Setting active context to `arc`
```

## <a name="preliminary-and-temporary-step-for-openshift-users-only"></a>Předběžný a dočasný krok jenom pro uživatele OpenShift

Před přechodem k dalšímu kroku implementujte tento krok. Pokud chcete nasadit skupinu serverů PostgreSQL s vlastním škálováním na Red Hat OpenShift v jiném projektu než ve výchozím nastavení, musíte pro svůj cluster spustit následující příkazy, abyste mohli aktualizovat omezení zabezpečení. Tento příkaz uděluje potřebná oprávnění účtům služeb, které budou spouštět PostgreSQL skupinu serverů s vlastním škálováním. V poli omezení kontextu zabezpečení (SCC) **_ARC – data-SCC_** je ten, který jste přidali při nasazení řadiče dat ARC Azure.

```console
oc adm policy add-scc-to-user arc-data-scc -z <server-group-name> -n <namespace name>
```

_**Server-Group-Name** je název skupiny serverů, kterou vytvoříte během dalšího kroku._
   
Další informace o SCCs v OpenShift najdete v [dokumentaci k OpenShift](https://docs.openshift.com/container-platform/4.2/authentication/managing-security-context-constraints.html).
Teď můžete implementovat další krok.

## <a name="create-an-azure-database-for-postgresql-hyperscale-server-group"></a>Vytvoření Azure Database for PostgreSQL skupiny serverů s škálovatelným škálováním

Pokud chcete ve službě Azure ARC vytvořit Azure Database for PostgreSQL skupinu serverů se škálováním na více systému, použijte následující příkaz:

```console
azdata arc postgres server create -n <name> --workers <# worker nodes with #>=2> --storage-class-data <storage class name> --storage-class-logs <storage class name> --storage-class-backups <storage class name>

#Example
#azdata arc postgres server create -n postgres01 --workers 2
```

> [!NOTE]
> - **K dispozici jsou další parametry příkazového řádku.  Úplný seznam možností zobrazíte spuštěním `azdata arc postgres server create --help` .**
> - Třída úložiště používaná pro zálohy (_--Storage-Class-Backup-SCB_) je ve výchozím nastavení pro třídu úložiště dat řadiče dat, pokud není k dispozici.
> - Jednotka přijatá parametrem--Volume-Size-* je množství prostředků Kubernetes (celé číslo následované jedním z těchto pokusů (T, G, M, K, m) nebo jejich mocniny dvou ekvivalentů (ti, GI, mi, Ki)).
> - Názvy musí být delší než 12 znaků a musí odpovídat konvencím názvů DNS.
> - Zobrazí se výzva k zadání hesla pro standardního administrativního uživatele _Postgres_ .  Interaktivní výzvu můžete přeskočit nastavením `AZDATA_PASSWORD` proměnné prostředí relace před spuštěním příkazu CREATE.
> - Pokud jste nasadili řadič dat pomocí AZDATA_USERNAME a AZDATA_PASSWORD proměnných prostředí relace ve stejné relaci terminálu, pak se hodnoty pro AZDATA_PASSWORD použijí i k nasazení skupiny serverů PostgreSQL s velkým měřítkem. Pokud dáváte přednost použití jiného hesla, buď (1) aktualizujte hodnotu pro AZDATA_PASSWORD nebo (2) odstraňte proměnnou prostředí AZDATA_PASSWORD nebo při vytváření skupiny serverů v případě, že odstraníte její hodnotu, se zobrazí výzva k interaktivnímu zadání hesla.
> - Název výchozího správce pro databázový stroj PostgreSQL ve velkém měřítku je _Postgres_ a v tomto okamžiku jej nelze změnit.
> - Když vytvoříte skupinu serverů PostgreSQL s škálovatelným škálováním, nebudou se prostředky hned registrovat přímo v Azure. V rámci procesu nahrávání [inventáře prostředků](upload-metrics-and-logs-to-azure-monitor.md)  nebo dat o [využití](view-billing-data-in-azure.md) do Azure se prostředky vytvoří v Azure a vaše prostředky budete moct zobrazit v Azure Portal.



## <a name="list-your-azure-database-for-postgresql-server-groups-created-in-your-arc-setup"></a>Výpis skupin Azure Database for PostgreSQL serverů vytvořených v nastavení ARC

Pokud chcete zobrazit PostgreSQL skupiny serverů s škálovatelným škálováním na Azure ARC, použijte následující příkaz:

```console
azdata arc postgres server list
```


```output
Name        State     Workers
----------  --------  ---------
postgres01  Ready     2
```

## <a name="get-the-endpoints-to-connect-to-your-azure-database-for-postgresql-server-groups"></a>Získání koncových bodů pro připojení ke skupinám Azure Database for PostgreSQL serverů

Chcete-li zobrazit koncové body pro instanci PostgreSQL, spusťte následující příkaz:

```console
azdata arc postgres endpoint list -n <server group name>
```
Příklad:
```console
[
  {
    "Description": "PostgreSQL Instance",
    "Endpoint": "postgresql://postgres:<replace with password>@12.345.123.456:1234"
  },
  {
    "Description": "Log Search Dashboard",
    "Endpoint": "https://12.345.123.456:12345/kibana/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:\"postgres01\"'))"
  },
  {
    "Description": "Metrics Dashboard",
    "Endpoint": "https://12.345.123.456:12345/grafana/d/postgres-metrics?var-Namespace=arc3&var-Name=postgres01"
  }
]
```

Pomocí koncového bodu instance PostgreSQL se můžete připojit ke skupině serverů PostgreSQL s vlastním škálováním z oblíbeného nástroje:  [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio), [Pgcli](https://www.pgcli.com/) psql, pgAdmin atd.

Pokud k testování používáte virtuální počítač Azure, postupujte podle následujících pokynů:

## <a name="special-note-about-azure-virtual-machine-deployments"></a>Zvláštní Poznámka o nasazeních virtuálních počítačů Azure

Pokud používáte virtuální počítač Azure, IP adresa koncového bodu nezobrazuje _veřejnou_ IP adresu. Veřejnou IP adresu najdete pomocí následujícího příkazu:

```azurecli
az network public-ip list -g azurearcvm-rg --query "[].{PublicIP:ipAddress}" -o table
```

Pak můžete zkombinovat veřejnou IP adresu s portem a vytvořit tak připojení.

Také je možné, že bude nutné vystavit port skupiny serverů PostgreSQL s škálovatelným škálováním prostřednictvím brány zabezpečení sítě (NSG). Pokud chcete povolit provoz přes (NSG), budete muset přidat pravidlo, které můžete provést pomocí následujícího příkazu:

Pokud chcete nastavit pravidlo, budete muset znát název vašeho NSGu. NSG určíte pomocí příkazu níže:

```azurecli
az network nsg list -g azurearcvm-rg --query "[].{NSGName:name}" -o table
```

Jakmile budete mít název NSG, můžete přidat pravidlo brány firewall pomocí následujícího příkazu. Ukázkové hodnoty zde vytvoří pravidlo NSG pro port 30655 a umožní připojení z **jakékoli** zdrojové IP adresy.  Nejedná se o osvědčený postup zabezpečení.  To můžete lépe uzamknout tím, že zadáte hodnotu-Source-Address-prefixs, která je specifická pro vaši IP adresu klienta, nebo rozsah IP adres, který se vztahuje na IP adresy vašeho týmu nebo organizace.

Hodnotu parametru--destination-port-Ranges nahraďte číslem portu, který jste získali v příkazu azdata ARC Postgres server list.

```azurecli
az network nsg rule create -n db_port --destination-port-ranges 30655 --source-address-prefixes '*' --nsg-name azurearcvmNSG --priority 500 -g azurearcvm-rg --access Allow --description 'Allow port through for db access' --destination-address-prefixes '*' --direction Inbound --protocol Tcp --source-port-ranges '*'
```

## <a name="connect-with-azure-data-studio"></a>Připojení pomocí nástroje Azure Data Studio

Otevřete Azure Data Studio a připojte se k instanci s IP adresou externího koncového bodu a číslem portu výše a heslem, které jste zadali v době, kdy jste instanci vytvořili.  Pokud v rozevíracím seznamu *Typ připojení* není dostupná služba PostgreSQL, můžete rozšíření PostgreSQL nainstalovat tak, že na kartě rozšíření vyhledáte PostgreSQL.

> [!NOTE]
> Pokud chcete zadat číslo portu, budete muset kliknout na tlačítko [Upřesnit] na panelu připojení.

Pamatujte, že pokud používáte virtuální počítač Azure, budete potřebovat _veřejnou_ IP adresu, která je přístupná prostřednictvím následujícího příkazu:

```azurecli
az network public-ip list -g azurearcvm-rg --query "[].{PublicIP:ipAddress}" -o table
```

## <a name="connect-with-psql"></a>Připojení pomocí psql

Pokud chcete získat přístup k PostgreSQL skupině serverů s vlastním škálováním, předejte externí koncový bod skupiny serverů PostgreSQL s vlastním škálováním, kterou jste získali z výše uvedeného:

Nyní se můžete připojit buď k psql:

```console
psql postgresql://postgres:<EnterYourPassword>@10.0.0.4:30655
```

## <a name="next-steps"></a>Další kroky

- Přečtěte si téma koncepty a návody Azure Database for PostgreSQLho škálování pro distribuci vašich dat napříč několika PostgreSQL uzly s vlastním škálováním a využijte výhod Azure Database for PostgreSQL vysokého měřítka. :
    * [Uzly a tabulky](../../postgresql/concepts-hyperscale-nodes.md)
    * [Určení typu aplikace](../../postgresql/concepts-hyperscale-app-type.md)
    * [Volba distribučního sloupce](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [Společné umístění tabulek](../../postgresql/concepts-hyperscale-colocation.md)
    * [Distribuce a úprava tabulek](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [Návrh databáze s více klienty](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [Návrh řídicího panelu pro analýzu v reálném čase](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

    > \* V dokumentech výše přeskočte oddíly, **které se přihlásí k Azure Portal**, & **Vytvoření Azure Database for PostgreSQL-Citus (škálování)**. Implementujte zbývající kroky v nasazení ARC Azure. Tyto části jsou specifické pro Azure Database for PostgreSQL Citus (PaaS), které nabízíme jako službu v cloudu Azure, ale ostatní části dokumentů jsou přímo použitelné pro PostgreSQL škálování na úrovni Azure ARC.

- [Škálování skupiny serverů Azure Database for PostgreSQL Hyperscale na více instancí](scale-out-postgresql-hyperscale-server-group.md)
- [Koncepty konfigurace úložiště a Kubernetes úložiště](storage-configuration.md)
- [Rozšiřování deklarací trvalých svazků](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#expanding-persistent-volumes-claims)
- [Model prostředků Kubernetes](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)