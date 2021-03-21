---
title: Vytvoření skupiny serverů PostgreSQL Hyperscale s podporou služby Azure Arc
description: Vytvoření skupiny serverů PostgreSQL Hyperscale s podporou služby Azure Arc
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 02/11/2021
ms.topic: how-to
ms.openlocfilehash: 046f9d80c034e1ac1f2e7ffe144b4f389861b043
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101687936"
---
# <a name="create-an-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Vytvoření skupiny serverů PostgreSQL Hyperscale s podporou služby Azure Arc

Tento dokument popisuje kroky pro vytvoření PostgreSQL skupiny serverů s škálovatelným škálováním na Azure ARC.

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="getting-started"></a>Začínáme
Pokud jste již obeznámeni s tématy uvedenými níže, můžete tento odstavec vynechat.
Než budete pokračovat v vytváření, můžete si přečíst důležitá témata:
- [Přehled datových služeb s podporou ARC Azure](overview.md)
- [Režimy připojení a požadavky](connectivity.md)
- [Koncepty konfigurace úložiště a Kubernetes úložiště](storage-configuration.md)
- [Model prostředků Kubernetes](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)

Pokud dáváte přednost vyzkoušení bez samotného celého prostředí, začněte rychle začít pomocí [Azure ARC rychlé zprovoznění](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) ve službě Azure Kubernetes Service (AKS), AWS elastické Kubernetesové služby (EKS), Google Cloud Kubernetes Engine (GKE) nebo na virtuálním počítači Azure.


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
Před přechodem k dalšímu kroku implementujte tento krok. Pokud chcete nasadit skupinu serverů PostgreSQL s vlastním škálováním na Red Hat OpenShift v jiném projektu než ve výchozím nastavení, musíte pro svůj cluster spustit následující příkazy, abyste mohli aktualizovat omezení zabezpečení. Tento příkaz uděluje potřebná oprávnění účtům služeb, které budou spouštět PostgreSQL skupinu serverů s vlastním škálováním. V poli omezení kontextu zabezpečení (SCC) ARC – data-SCC je ten, který jste přidali při nasazení řadiče dat ARC Azure.

```Console
oc adm policy add-scc-to-user arc-data-scc -z <server-group-name> -n <namespace name>
```

**Server-Group-Name je název skupiny serverů, kterou vytvoříte během dalšího kroku.**

Další informace o SCCs v OpenShift najdete v [dokumentaci k OpenShift](https://docs.openshift.com/container-platform/4.2/authentication/managing-security-context-constraints.html). Teď můžete implementovat další krok.


## <a name="create-an-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Vytvoření skupiny serverů PostgreSQL Hyperscale s podporou služby Azure Arc

Pokud chcete na řadiči dat ARC vytvořit PostgreSQL skupinu serverů s podporou rozšíření Azure ARC, použijete příkaz `azdata arc postgres server create` , ke kterému budete předávat několik parametrů.

Podrobnosti o všech parametrech, které můžete nastavit v čase vytvoření, najdete v výstupu příkazu:
```console
azdata arc postgres server create --help
```

Hlavní parametry by měly být zváženy:
- **název skupiny serverů** , kterou chcete nasadit. Uveďte buď `--name` nebo `-n` následované názvem, jehož délka nesmí být delší než 11 znaků.

- **verze modulu PostgreSQL** , který chcete nasadit: ve výchozím nastavení je to verze 12. K nasazení verze 12 můžete buď vynechat tento parametr, nebo předat jeden z následujících parametrů: `--engine-version 12` nebo `-ev 12` . K nasazení verze 11 označte `--engine-version 11` nebo `-ev 11` .

- **počet pracovních uzlů** , které chcete nasadit pro horizontální navýšení kapacity, a případně dosažení lepších výkonů. Než budete pokračovat, přečtěte si [koncepty o Postgres škálování](concepts-distributed-postgres-hyperscale.md). Chcete-li určit počet uzlů pracovních procesů, které mají být nasazeny, použijte parametr `--workers` nebo `-w` za něj zadejte celé číslo větší nebo rovno 2. Například pokud chcete nasadit skupinu serverů se dvěma pracovními uzly, uveďte `--workers 2` nebo `-w 2` . Vytvoří se tři lusky, jedna pro uzel koordinátora/instance a dvě pro pracovní uzly/instance (jeden pro každý z pracovních procesů).

- **třídy úložiště** , které má skupina serverů používat. Je důležité nastavit třídu úložiště přímo v době, kdy nasazujete skupinu serverů, protože po nasazení nejde změnit. Pokud jste po nasazení změnili třídu úložiště, budete muset extrahovat data, odstranit skupinu serverů, vytvořit novou skupinu serverů a importovat data. Můžete určit třídy úložiště, které se mají používat pro data, protokoly a zálohy. Ve výchozím nastavení platí, že pokud neuvedete třídy úložiště, budou použity třídy úložiště v řadiči dat.
    - Chcete-li pro data nastavit třídu úložiště, označte parametr `--storage-class-data` nebo `-scd` za něj uveďte název třídy úložiště.
    - Chcete-li nastavit třídu úložiště pro protokoly, označte parametr `--storage-class-logs` nebo `-scl` za něj uveďte název třídy úložiště.
    - nastavení třídy úložiště pro zálohy: v této verzi Preview PostgreSQL s povoleným rozšířením Azure ARC existují dva způsoby, jak nastavit třídy úložiště v závislosti na tom, jaké typy operací zálohování a obnovení chcete provést. Pracujeme na zjednodušení tohoto prostředí. Označíte buď třídu úložiště, nebo připojení ke svazku s deklarací identity. Deklarace identity připojení svazku je dvojice existující deklarace identity trvalého svazku (ve stejném oboru názvů) a typu svazku (a volitelná metadata v závislosti na typu svazku) oddělená dvojtečkou. Trvalý svazek bude pro skupinu serverů PostgreSQL připojen ke každé pod.
        - Chcete-li naplánovat pouze úplné obnovení databáze, nastavte parametr `--storage-class-backups` nebo `-scb` následovaný názvem třídy úložiště.
        - Pokud máte v úmyslu obnovovat úplné obnovení databáze a obnovení bodu v čase, nastavte parametr `--volume-claim-mounts` nebo `-vcm` následovaný názvem deklarace svazku a typem svazku.

Všimněte si, že při spuštění příkazu CREATE budete vyzváni k zadání hesla výchozího `postgres` správce. V této verzi Preview nejde změnit jméno tohoto uživatele. Můžete přeskočit interaktivní výzvu nastavením `AZDATA_PASSWORD` proměnné prostředí relace před spuštěním příkazu CREATE.

### <a name="examples"></a>Příklady

**Pokud chcete nasadit skupinu serverů Postgres verze 12 s názvem postgres01 se dvěma pracovními uzly, které používají stejné třídy úložiště jako řadič dat, spusťte následující příkaz:**
```console
azdata arc postgres server create -n postgres01 --workers 2
```

**Pokud chcete nasadit skupinu serverů Postgres verze 12 s názvem postgres01 se dvěma pracovními uzly, které používají stejné třídy úložiště jako řadič dat pro data a protokoly, ale konkrétní třída úložiště pro obnovení úplného obnovení i v čase, použijte následující postup:**

 V tomto příkladu se předpokládá, že je skupina serverů hostovaná v clusteru Azure Kubernetes Service (AKS). V tomto příkladu se jako název třídy úložiště používá azurefile-Premium. Níže uvedený příklad můžete nastavit tak, aby odpovídal vašemu prostředí. Všimněte si, že pro tuto konfiguraci **se vyžaduje AccessModes ReadWriteMany** .  

Nejprve vytvořte soubor YAML, který obsahuje níže uvedený popis záložního virtuálního okruhu, a pojmenujte ho CreateBackupPVC. yml. Příklad:
```console
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: backup-pvc
  namespace: arc
spec:
  accessModes:
    - ReadWriteMany
  volumeMode: Filesystem
  resources:
    requests:
      storage: 100Gi
  storageClassName: azurefile-premium
```

Dále vytvořte okruh PVC pomocí definice uložené v souboru YAML:

```console
kubectl create -f e:\CreateBackupPVC.yml -n arc
``` 

Potom vytvořte skupinu serverů:

```console
azdata arc postgres server create -n postgres01 --workers 2 -vcm backup-pvc:backup
```

> [!IMPORTANT]
> - Přečtěte si [aktuální omezení související se zálohováním a obnovením](limitations-postgresql-hyperscale.md#backup-and-restore).


> [!NOTE]  
> - Pokud jste nasadili řadič dat `AZDATA_USERNAME` pomocí `AZDATA_PASSWORD` proměnné prostředí a prostředí relace ve stejné relaci Terminálové služby, pak hodnoty pro `AZDATA_PASSWORD` budou použity také k nasazení skupiny serverů PostgreSQL s velkým měřítkem. Pokud dáváte přednost použití jiného hesla, buď (1) aktualizujte hodnotu pro `AZDATA_PASSWORD` nebo (2) odstraňte `AZDATA_PASSWORD` proměnnou prostředí nebo (3) Odstraňte její hodnotu, aby se při vytváření skupiny serverů zobrazila výzva k interaktivnímu zadání hesla.
> - Když vytvoříte skupinu serverů PostgreSQL s škálovatelným škálováním, nebudou se prostředky hned registrovat přímo v Azure. V rámci procesu nahrávání [inventáře prostředků](upload-metrics-and-logs-to-azure-monitor.md)  nebo dat o [využití](view-billing-data-in-azure.md) do Azure se prostředky vytvoří v Azure a vaše prostředky budete moct zobrazit v Azure Portal.



## <a name="list-the-postgresql-hyperscale-server-groups-deployed-in-your-arc-data-controller"></a>Seznam skupin serverů PostgreSQL s vlastním škálováním nasazených v řadiči dat ARC

Pokud chcete zobrazit seznam PostgreSQL skupin serverů s vlastním škálováním nasazených v řadiči dat ARC, spusťte následující příkaz:

```console
azdata arc postgres server list
```


```output
Name        State     Workers
----------  --------  ---------
postgres01  Ready     2
```

## <a name="get-the-endpoints-to-connect-to-your-azure-arc-enabled-postgresql-hyperscale-server-groups"></a>Získání koncových bodů pro připojení ke skupinám serverů s povoleným PostgreSQLým rozšířením Azure ARC

Chcete-li zobrazit koncové body pro skupinu serverů PostgreSQL, spusťte následující příkaz:

```console
azdata arc postgres endpoint list -n <server group name>
```
Například:
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

Pomocí koncového bodu instance PostgreSQL se můžete připojit ke skupině serverů PostgreSQL s vlastním škálováním z oblíbeného nástroje:  [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio), [Pgcli](https://www.pgcli.com/) psql, pgAdmin atd. Když to uděláte, připojíte se k uzlu nebo instanci koordinátora, která bude mít za následek směrování dotazu do příslušných pracovních uzlů nebo instancí, pokud jste vytvořili distribuované tabulky. Pokud chcete získat další informace, přečtěte si [Koncepty s povoleným PostgreSQLm škálováním Azure ARC](concepts-distributed-postgres-hyperscale.md).

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

- Přečtěte si téma koncepty a návody Azure Database for PostgreSQLho škálování pro distribuci dat napříč několika uzly v PostgreSQL a s výhodou lepších výkonů:
    * [Uzly a tabulky](../../postgresql/concepts-hyperscale-nodes.md)
    * [Určení typu aplikace](../../postgresql/concepts-hyperscale-app-type.md)
    * [Volba distribučního sloupce](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [Společné umístění tabulek](../../postgresql/concepts-hyperscale-colocation.md)
    * [Distribuce a úprava tabulek](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [Návrh databáze s více klienty](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [Návrh řídicího panelu pro analýzu v reálném čase](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

    > \* V dokumentech výše přeskočte oddíly, **které se přihlásí k Azure Portal**, & **Vytvoření Azure Database for PostgreSQL-Citus (škálování)**. Implementujte zbývající kroky v nasazení ARC Azure. Tyto části jsou specifické pro Azure Database for PostgreSQL Citus (PaaS), které nabízíme jako službu v cloudu Azure, ale ostatní části dokumentů jsou přímo použitelné pro PostgreSQL škálování na úrovni Azure ARC.

- [Horizontální navýšení kapacity Azure ARC pro PostgreSQL skupinu serverů s vlastním škálováním](scale-out-postgresql-hyperscale-server-group.md)
- [Koncepty konfigurace úložiště a Kubernetes úložiště](storage-configuration.md)
- [Rozšiřování deklarací trvalých svazků](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#expanding-persistent-volumes-claims)
- [Model prostředků Kubernetes](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)
