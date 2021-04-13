---
title: Omezení odchozího provozu v clusteru Azure Red Hat OpenShift (ARO)
description: Informace o tom, jaké porty a adresy se vyžadují k řízení odchozího provozu v Azure Red Hat OpenShift (ARO)
author: sakthi-vetrivel
ms.author: jzim
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 04/09/2021
ms.openlocfilehash: 24c4686306aff9d84fe7bf74ddfdccff987244d9
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107368629"
---
# <a name="control-egress-traffic-for-your-azure-red-hat-openshift-aro-cluster-preview"></a>Řízení provozního provozu pro cluster Azure Red Hat OpenShift (ARO) (Preview)

Tento článek poskytuje potřebné informace, které vám umožní zabezpečit odchozí přenosy z vašeho clusteru Azure Red Hat OpenShift (ARO). Obsahuje požadavky na cluster pro nasazení Basic ARO a další požadavky na volitelné Red Hat a komponenty třetích stran. [Příklad](#private-aro-cluster-setup) vám poskytneme na konci postupu konfigurace těchto požadavků pomocí Azure firewall. Pamatujte na to, že tyto informace můžete použít pro Azure Firewall nebo na jakoukoli metodu nebo zařízení pro odchozí omezení.

## <a name="before-you-begin"></a>Než začnete

V tomto článku se předpokládá, že vytváříte nový cluster. Pokud potřebujete cluster se základními ARO, přečtěte si [rychlý Start pro ARO](https://docs.microsoft.com/azure/openshift/tutorial-create-cluster).

> [!IMPORTANT]
> Funkce ve verzi Preview v ARO jsou k dispozici na samoobslužné službě, na základě souhlasu. Verze Preview jsou k dispozici "tak jak jsou" a "jako dostupné" a jsou vyloučeny ze smluv o úrovni služeb a omezené záruky. Verze Preview pro ARO jsou částečně pokryté zákaznickou podporou na základě nejlepších úsilí.

## <a name="minimum-required-fqdn--application-rules"></a>Minimální požadovaný plně kvalifikovaný název domény nebo pravidla použití aplikace

Tento seznam je založený na seznamu plně kvalifikovaných názvů domény, které najdete v OpenShift Docs: https://docs.openshift.com/container-platform/4.6/installing/install_config/configuring-firewall.html

Vyžaduje se následující plně kvalifikovaný název domény nebo pravidla použití:

| Cílový plně kvalifikovaný název domény | Port | Použití |
| ----------- | ----------- | ------------- |
| **`quay.io`** | **HTTPS: 443** | Povinné pro instalaci, kterou cluster používá. Používá ho cluster ke stažení imagí kontejneru platformy. |
| **`registry.redhat.io`** | **HTTPS: 443** | Povinné pro základní Doplňky Tento cluster používá ke stažení základních komponent, jako jsou vývojové nástroje, doplňky založené na operátorech a image kontejnerů dodaných na Red Hat.
| **`mirror.openshift.com`** | **HTTPS: 443** | To je nutné v prostředí VDI nebo přenosném počítači pro přístup k zrcadlenému obsahu a bitovým kopiím instalace. V clusteru je potřeba stáhnout Signatury vydaných verzí, abyste věděli, jaké image se mají z quay.io načíst. |
| **`api.openshift.com`** | **HTTPS: 443** | Vyžadovaná clusterem ke kontrole, jestli existují aktualizace k dispozici před stažením signatur imagí. |
| **`arosvc.azurecr.io`** | **HTTPS: 443** | Interní soukromý registr pro operátory ARO  Vyžaduje se, pokud nepovolíte na svých podsítích Microsoft. ContainerRegistry koncové body služby. |
| **`management.azure.com`** | **HTTPS: 443** | Cluster tuto službu používá pro přístup k rozhraním API Azure. |
| **`login.microsoftonline.com`** | **HTTPS: 443** | Tuto službu používá cluster k ověřování do Azure. |
| **`gcs.prod.monitoring.core.windows.net`** | **HTTPS: 443** | Používá se pro sledování v programu Microsoft Ženeva, aby tým ARO mohl monitorovat clustery zákazníků. |
| **`*.blob.core.windows.net`** | **HTTPS: 443** | Používá se pro sledování v programu Microsoft Ženeva, aby tým ARO mohl monitorovat clustery zákazníků. |
| **`*.servicebus.windows.net`** | **HTTPS: 443** | Používá se pro sledování v programu Microsoft Ženeva, aby tým ARO mohl monitorovat clustery zákazníků. |
| **`*.table.core.windows.net`** | **HTTPS: 443** | Používá se pro sledování v programu Microsoft Ženeva, aby tým ARO mohl monitorovat clustery zákazníků. |

> [!NOTE] 
> Pro mnoho zákazníků, kteří zveřejňují *. blob, *. Table a další velké adresní prostory, vytvoří potenciální exfiltraceí dat. Možná budete chtít zvážit použití [OpenShift odchozího firewallu](https://docs.openshift.com/container-platform/4.6/networking/openshift_sdn/configuring-egress-firewall.html) k ochraně aplikací nasazených v clusteru od dosažení těchto cílů a používání privátního propojení Azure pro konkrétní potřeby aplikací.

---

## <a name="complete-list-of-required-and-optional-fqdns"></a>Úplný seznam povinných a volitelných plně kvalifikovaných názvů domény

### <a name="first-group-installing-and-downloading-packages-and-tools"></a>PRVNÍ SKUPINA: INSTALACE A STAŽENÍ BALÍČKŮ A NÁSTROJŮ

- **`quay.io`**: Povinné pro instalaci, kterou cluster používá. Používá ho cluster ke stažení imagí kontejneru platformy.
- **`registry.redhat.io`**: Povinné pro základní doplňky. Cluster tuto službu používá ke stažení základních komponent, jako jsou vývojové nástroje, doplňky založené na operátorech nebo Red Hat dodávané image kontejnerů, jako je náš middleware, univerzální základní image...
- **`sso.redhat.com`**: Tento postup je vyžadován v prostředí VDI nebo v počítači, aby se mohl připojit k cloud.redhat.com. Jedná se o web, ve kterém můžeme stáhnout tajný kód pro vyžádání obsahu a použít některá z řešení SaaS, která nabízíme ve Red Hat, a usnadnit tak monitorování vašich předplatných, inventáře clusterů, vrácení peněz vytváření sestav, mimo jiné věci.
- **`openshift.org`**: Tento postup je vyžadován v prostředí VDI nebo na přenosném počítači, aby se mohl připojit ke stažení imagí CoreOS, ale v Azure jsou převzaty z webu Marketplace, takže není nutné stahovat image operačního systému.

---

### <a name="second-group-telemetry"></a>DRUHÁ SKUPINA: TELEMETRIE

Tato část se dá zarezervovat, ale před tím, než budeme znát, prosím zkontrolujte, co je: https://docs.openshift.com/container-platform/4.6/support/remote_health_monitoring/about-remote-health-monitoring.html
- **`cert-api.access.redhat.com`**: Použijte v prostředí VDI nebo přenosném počítači.
- **`api.access.redhat.com`**: Použijte v prostředí VDI nebo přenosném počítači.
- **`infogw.api.openshift.com`**: Použijte v prostředí VDI nebo přenosném počítači.
- **`https://cloud.redhat.com/api/ingress`**: Použijte v clusteru pro operátor Insights, který se integruje s nástrojem aaS Red Hat Insights.
V OpenShift Container Platform se zákazníci můžou odhlásit informace o stavu a používání sestav. Připojené clustery ale umožňují Red Hat rychleji reagovat na problémy a lépe podporovat naše zákazníky a lépe porozumět tomu, jak clustery upgradují. Podrobnosti najdete tady: https://docs.openshift.com/container-platform/4.6/support/remote_health_monitoring/opting-out-of-remote-health-reporting.html .

---

### <a name="third-group-cloud-apis"></a>TŘETÍ skupina: CLOUDová rozhraní API

- **`management.azure.com`**: Tento cluster používá pro přístup k rozhraním API Azure.

---

### <a name="fourth-group-other-openshift-requirements"></a>ČTVRTÁ SKUPINA: JINÉ POŽADAVKY NA OPENSHIFT

- **`mirror.openshift.com`**: Tento postup je vyžadován v prostředí VDI nebo v přenosném počítači pro přístup k zrcadlenému instalačnímu obsahu a bitovým kopiím, které jsou požadovány v clusteru ke stažení podpisů vydaných verzí platforem, které cluster používá k tomu, aby věděl, které image budou
- **`storage.googleapis.com/openshift-release`**: Alternativní lokalita pro stažení signatur vydaných verzí platformy, kterou cluster používá k určení, které image se mají z quay.io načíst.
- **`*.apps.<cluster_name>.<base_domain>`** (Nebo ekvivalentní adresa URL služby ARO): Pokud allowlisting domény, používá se ve vaší podnikové síti k přístupu k aplikacím nasazeným v OpenShift nebo k přístupu ke konzole OpenShift.
- **`api.openshift.com`**: Vyžadovaná clusterem ke kontrole, jestli existují aktualizace k dispozici před stažením signatur imagí.
- **`registry.access.redhat.com`**: V prostředí VDI nebo přenosném počítači se vyžaduje přístup k registru, aby se při použití nástroje CLI ODO stáhly vývojové image. (Tento nástroj rozhraní příkazového řádku je alternativním nástrojem CLI pro vývojáře, kteří nejsou obeznámeni s Kubernetes). https://docs.openshift.com/container-platform/4.6/cli_reference/developer_cli_odo/understanding-odo.html

---

### <a name="fifth-group-microsoft--red-hat-aro-monitoring-service"></a>PÁTÁ SKUPINA: MICROSOFT & RED HAT ARO MONITORING SERVICE

- **`login.microsoftonline.com`**: Používá ho cluster k ověřování do Azure.
- **`gcs.prod.monitoring.core.windows.net`**: Používá se pro sledování v programu Microsoft Ženeva, aby tým ARO mohl monitorovat clustery zákazníků.
- **`*.blob.core.windows.net`**: Používá se pro sledování v programu Microsoft Ženeva, aby tým ARO mohl monitorovat clustery zákazníků.
- **`*.servicebus.windows.net`**: Používá se pro sledování v programu Microsoft Ženeva, aby tým ARO mohl monitorovat clustery zákazníků.
- **`*.table.core.windows.net`**: Používá se pro sledování v programu Microsoft Ženeva, aby tým ARO mohl monitorovat clustery zákazníků.

## <a name="aro-integrations"></a>Integrace ARO

### <a name="azure-monitor-for-containers"></a>Azure Monitor pro kontejnery

Existují dvě možnosti, jak zajistit přístup k Azure Monitor kontejnerů, můžete umožnit, aby Azure Monitor [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) **nebo** poskytovali přístup k požadovaným plně kvalifikovanému názvu domény nebo pravidlům aplikace.  Tady najdete [pokyny](https://docs.microsoft.com/azure/azure-monitor/containers/container-insights-azure-redhat4-setup) , jak přidat Azure monitor do stávajícího clusteru ARO.

#### <a name="required-network-rules"></a>Požadovaná síťová pravidla

Vyžaduje se následující plně kvalifikovaný název domény nebo pravidla použití:

| Cílový koncový bod                                                             | Protokol | Port    | Použití  |
|----------------------------------------------------------------------------------|----------|---------|------|
| [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureMonitor:443`**  | TCP           | 443      | Tento koncový bod slouží k posílání dat metrik a protokolů pro Azure Monitor a Log Analytics. |

#### <a name="required-fqdn--application-rules"></a>Požadovaný plně kvalifikovaný název domény nebo pravidla použití aplikace

Pro clustery ARO s povoleným Azure Monitor pro kontejnery jsou vyžadovány následující plně kvalifikovaný název domény nebo pravidla použití:

| FQDN                                    | Port      | Použití      |
|-----------------------------------------|-----------|----------|
| **`dc.services.visualstudio.com`** | **`HTTPS:443`**    | Tento koncový bod se používá pro metriky a monitorování telemetrie pomocí Azure Monitor. |
| **`*.ods.opinsights.azure.com`**    | **`HTTPS:443`**    | Tento koncový bod používá Azure Monitor k ingestování dat Log Analytics. |
| **`*.oms.opinsights.azure.com`** | **`HTTPS:443`** | Tento koncový bod používá omsagent, který se používá k ověření služby Log Analytics. |
| **`*.monitoring.azure.com`** | **`HTTPS:443`** | Tento koncový bod slouží k odesílání dat metrik do Azure Monitor. |


## <a name="private-aro-cluster-setup"></a>Instalace clusteru privátních ARO
Cílem je zabezpečit cluster v ARO díky směrování odchozího provozu prostřednictvím Azure Firewall
### <a name="before"></a>Před:
![Před](media/concepts-networking/aro-private.jpg)
### <a name="after"></a>Po:
![Po](media/concepts-networking/aro-fw.jpg)

## <a name="create-a-private-aro-cluster"></a>Vytvoření privátního clusteru ARO

### <a name="set-up-vars-for-your-environment"></a>Nastavení proměnných pro vaše prostředí
```bash

CLUSTER=aro-cluster # Name of your created cluster
RESOURCEGROUP=aro-rg # The name of your resource group where you created the ARO cluster
AROVNET=aro-vnet # The name of your vnet from your created ARO cluster
JUMPSUBNET=jump-subnet
LOCATION=eastus # The location where ARO cluster is deployed

```

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků
```bash
az group create -g "$RESOURCEGROUP" -l $LOCATION
```

### <a name="create-the-virtual-network"></a>Vytvoření virtuální sítě
```bash
az network vnet create \
  -g $RESOURCEGROUP \
  -n $AROVNET \
  --address-prefixes 10.0.0.0/8
```

### <a name="add-two-empty-subnets-to-your-virtual-network"></a>Přidání dvou prázdných podsítí do virtuální sítě
```bash
  az network vnet subnet create \
    -g "$RESOURCEGROUP" \
    --vnet-name $AROVNET \
    -n "$CLUSTER-master" \
    --address-prefixes 10.10.1.0/24 \
    --service-endpoints Microsoft.ContainerRegistry

  az network vnet subnet create \
    -g $RESOURCEGROUP \
    --vnet-name $AROVNET \
    -n "$CLUSTER-worker" \
    --address-prefixes 10.20.1.0/24 \
    --service-endpoints Microsoft.ContainerRegistry
```

### <a name="disable-network-policies-for-private-link-service-on-your-virtual-network-and-subnets-this-is-a-requirement-for-the-aro-service-to-access-and-manage-the-cluster"></a>Zakažte zásady sítě pro službu privátního propojení ve vaší virtuální síti a podsítích. To je požadavek, aby služba ARO měla přístup ke clusteru a jeho správě.
```bash
az network vnet subnet update \
  -g "$RESOURCEGROUP" \
  --vnet-name $AROVNET \
  -n "$CLUSTER-master" \
  --disable-private-link-service-network-policies true
```
### <a name="create-a-firewall-subnet"></a>Vytvoření podsítě brány firewall
```bash
az network vnet subnet create \
    -g "$RESOURCEGROUP" \
    --vnet-name $AROVNET \
    -n "AzureFirewallSubnet" \
    --address-prefixes 10.100.1.0/26
```

## <a name="create-a-jump-host-vm"></a>Vytvoření virtuálního počítače pro skok a hostitele
### <a name="create-a-jump-subnet"></a>Vytvoření skoku – podsíť
```bash
  az network vnet subnet create \
    -g "$RESOURCEGROUP" \
    --vnet-name $AROVNET \
    -n $JUMPSUBNET \
    --address-prefixes 10.30.1.0/24 \
    --service-endpoints Microsoft.ContainerRegistry
```
### <a name="create-a-jump-host-vm"></a>Vytvoření virtuálního počítače pro skok a hostitele
```bash
VMUSERNAME=aroadmin

az vm create --name ubuntu-jump \
             --resource-group $RESOURCEGROUP \
             --ssh-key-values ~/.ssh/id_rsa.pub \
             --admin-username $VMUSERNAME \
             --image UbuntuLTS \
             --subnet $JUMPSUBNET \
             --public-ip-address jumphost-ip \
             --vnet-name $AROVNET 
```

## <a name="create-an-azure-red-hat-openshift-cluster"></a>Vytvoření clusteru Azure Red Hat OpenShift
### <a name="get-a-red-hat-pull-secret-optional"></a>Získání tajného kódu pro vyžádání Red Hat (volitelné)

Tajný kód pro vyžádání obsahu Red Hat umožňuje vašemu clusteru přístup k registrům kontejnerů Red Hat spolu s jiným obsahem. Tento krok je nepovinný, ale doporučuje se.

1. **[Navštivte portál Red Hat OpenShift Cluster Manager](https://cloud.redhat.com/openshift/install/azure/aro-provisioned) a přihlaste se.**

   Budete se muset přihlásit k účtu Red Hat nebo vytvořit nový účet Red Hat pomocí podnikového e-mailu a přijmout podmínky a ujednání.

2. **Klikněte na Stáhnout tajný klíč pro vyžádání.**

Uložte si uložený `pull-secret.txt` soubor na bezpečném místě, bude se používat při každém vytváření clusteru.

Při spuštění `az aro create` příkazu můžete na svůj tajný kód pro vyžádání obsahu odkazovat pomocí `--pull-secret @pull-secret.txt` parametru. Spusťte `az aro create` z adresáře, kam jste uložili `pull-secret.txt` soubor. V opačném případě nahraďte parametrem `@pull-secret.txt` `@<path-to-my-pull-secret-file` .

Pokud kopírujete tajný kód pro vyžádání obsahu nebo na něj odkazujete v jiných skriptech, měl by váš tajný klíč pro vyžádání formátu obsahovat platný řetězec JSON.

```bash
az aro create \
  -g "$RESOURCEGROUP" \
  -n "$CLUSTER" \
  --vnet $AROVNET \
  --master-subnet "$CLUSTER-master" \
  --worker-subnet "$CLUSTER-worker" \
  --apiserver-visibility Private \
  --ingress-visibility Private \
  --pull-secret @pull-secret.txt
```

## <a name="create-an-azure-firewall"></a>Vytvoření Azure Firewall

### <a name="create-a-public-ip-address"></a>Vytvoření veřejné IP adresy
```bash
az network public-ip create -g $RESOURCEGROUP -n fw-ip --sku "Standard" --location $LOCATION
```
### <a name="update-install-azure-firewall-extension"></a>Aktualizovat rozšíření Azure Firewall instalace
```bash
az extension add -n azure-firewall
az extension update -n azure-firewall
```

### <a name="create-azure-firewall-and-configure-ip-config"></a>Vytvoření Azure Firewall a konfigurace konfigurace protokolu IP
```bash
az network firewall create -g $RESOURCEGROUP -n aro-private -l $LOCATION
az network firewall ip-config create -g $RESOURCEGROUP -f aro-private -n fw-config --public-ip-address fw-ip --vnet-name $AROVNET

```

### <a name="capture-azure-firewall-ips-for-a-later-use"></a>Zachytit Azure Firewall IP adresy pro pozdější použití
```bash
FWPUBLIC_IP=$(az network public-ip show -g $RESOURCEGROUP -n fw-ip --query "ipAddress" -o tsv)
FWPRIVATE_IP=$(az network firewall show -g $RESOURCEGROUP -n aro-private --query "ipConfigurations[0].privateIpAddress" -o tsv)

echo $FWPUBLIC_IP
echo $FWPRIVATE_IP
```

### <a name="create-a-udr-and-routing-table-for-azure-firewall"></a>Vytvoření UDR a směrovací tabulky pro Azure Firewall
```bash
az network route-table create -g $RESOURCEGROUP --name aro-udr

az network route-table route create -g $RESOURCEGROUP --name aro-udr --route-table-name aro-udr --address-prefix 0.0.0.0/0 --next-hop-type VirtualAppliance --next-hop-ip-address $FWPRIVATE_IP
```

### <a name="add-application-rules-for-azure-firewall"></a>Přidat pravidla aplikací pro Azure Firewall
Pravidlo, které OpenShift funguje na základě tohoto [seznamu](https://docs.openshift.com/container-platform/4.3/installing/install_config/configuring-firewall.html#configuring-firewall_configuring-firewall):
```bash
az network firewall application-rule create -g $RESOURCEGROUP -f aro-private \
 --collection-name 'ARO' \
 --action allow \
 --priority 100 \
 -n 'required' \
 --source-addresses '*' \
 --protocols 'http=80' 'https=443' \
 --target-fqdns 'registry.redhat.io' '*.quay.io' 'sso.redhat.com' 'management.azure.com' 'mirror.openshift.com' 'api.openshift.com' 'quay.io' '*.blob.core.windows.net' 'gcs.prod.monitoring.core.windows.net' 'registry.access.redhat.com' 'login.microsoftonline.com' '*.servicebus.windows.net' '*.table.core.windows.net' 'grafana.com'
```
Volitelná pravidla pro Image Docker:
```bash
az network firewall application-rule create -g $RESOURCEGROUP -f aro-private \
 --collection-name 'Docker' \
 --action allow \
 --priority 200 \
 -n 'docker' \
 --source-addresses '*' \
 --protocols 'http=80' 'https=443' \
 --target-fqdns '*cloudflare.docker.com' '*registry-1.docker.io' 'apt.dockerproject.org' 'auth.docker.io'
```

### <a name="associate-aro-subnets-to-fw"></a>Přidružte podsítě ARO do FW.
```bash
az network vnet subnet update -g $RESOURCEGROUP --vnet-name $AROVNET --name "$CLUSTER-master" --route-table aro-udr
az network vnet subnet update -g $RESOURCEGROUP --vnet-name $AROVNET --name "$CLUSTER-worker" --route-table aro-udr
```

## <a name="test-the-configuration-from-the-jumpbox"></a>Otestování konfigurace z JumpBox
Tyto kroky fungují jenom v případě, že jste přidali pravidla pro Image Docker. 
### <a name="configure-the-jumpbox"></a>Konfigurace JumpBox
Přihlaste se k virtuálnímu počítači s JumpBox a nainstalujte `azure-cli` , `oc-cli` a `jq` . Pro instalaci OpenShift-CLI si Projděte portál Red Hat Customer.
```bash
#Install Azure-cli
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
#Install jq
sudo apt install jq -y
```
### <a name="log-into-the-aro-cluster"></a>Přihlášení k clusteru ARO
Zobrazit seznam přihlašovacích údajů clusteru:
```bash

# Login to Azure
az login
# Set Vars in Jumpbox
CLUSTER=aro-cluster # Name of your created cluster
RESOURCEGROUP=aro-rg # The name of your resource group where you created the ARO cluster

#Get the cluster credentials
ARO_PASSWORD=$(az aro list-credentials -n $CLUSTER -g $RESOURCEGROUP -o json | jq -r '.kubeadminPassword')
ARO_USERNAME=$(az aro list-credentials -n $CLUSTER -g $RESOURCEGROUP -o json | jq -r '.kubeadminUsername')
```
Získání koncového bodu serveru API:
```bash
ARO_URL=$(az aro show -n $CLUSTER -g $RESOURCEGROUP -o json | jq -r '.apiserverProfile.url')
```

### <a name="download-the-oc-cli-to-the-jumpbox"></a>Stažení rozhraní příkazového řádku (OC) do JumpBox
```bash
cd ~
wget https://mirror.openshift.com/pub/openshift-v4/clients/ocp/latest/openshift-client-linux.tar.gz

mkdir openshift
tar -zxvf openshift-client-linux.tar.gz -C openshift
echo 'export PATH=$PATH:~/openshift' >> ~/.bashrc && source ~/.bashrc
```

Přihlaste se pomocí `oc login` :
```bash
oc login $ARO_URL -u $ARO_USERNAME -p $ARO_PASSWORD
```

### <a name="run-centos-to-test-outside-connectivity"></a>Spuštění CentOS k otestování vnějšího připojení
Vytvořit pod
```bash
cat <<EOF | oc apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: centos
spec:
  containers:
  - name: centos
    image: centos
    ports:
    - containerPort: 80
    command:
    - sleep
    - "3600"
EOF
```
Po spuštění pod ní se spustí a otestuje se z něho mimo připojení.

```bash
oc exec -it centos -- /bin/bash
curl microsoft.com
```

## <a name="access-the-web-console-of-the-private-cluster"></a>Přístup k webové konzole privátního clusteru

### <a name="set-up-ssh-forwards-commands"></a>Nastavení příkazů pro přeposílání SSH

```bash
sudo ssh -i $SSH_PATH -L 443:$CONSOLE_URL:443 aroadmin@$JUMPHOST

example:
sudo ssh -i /Users/jimzim/.ssh/id_rsa -L 443:console-openshift-console.apps.d5xm5iut.eastus.aroapp.io:443 aroadmin@104.211.18.56
```

### <a name="modify-the-etc-hosts-file-on-your-local-machine"></a>Úprava souboru atd. hosts na místním počítači
```bash
##
# Host Database
#
127.0.0.1 console-openshift-console.apps.d5xm5iut.eastus.aroapp.io
127.0.0.1 oauth-openshift.apps.d5xm5iut.eastus.aroapp.io
```

### <a name="use-sshuttle-as-another-option"></a>Použití SShuttle jako jiné možnosti

[SSHuttle](https://github.com/sshuttle/sshuttle)


## <a name="clean-up-resources"></a>Vyčištění prostředků

```bash

# Clean up the ARO cluster, vnet, firewall and jumpbox

# Remove udr from master and worker subnets first or will get error when deleting ARO cluster
az network vnet subnet update --vnet-name $AROVNET -n aro-cluster-master -g $RESOURCEGROUP --route-table aro-udr --remove routeTable
az network vnet subnet update --vnet-name $AROVNET -n aro-cluster-worker -g $RESOURCEGROUP --route-table aro-udr --remove routeTable

# Remove ARO Cluster
az aro delete -n $CLUSTER -g $RESOURCEGROUP

# Remove the resource group that contains the firewall, jumpbox and vnet
az group delete -n $RESOURCEGROUP
```