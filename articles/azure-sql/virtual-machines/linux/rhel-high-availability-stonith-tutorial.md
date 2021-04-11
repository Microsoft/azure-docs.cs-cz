---
title: Konfigurace skupin dostupnosti pro SQL Server na virtuálních počítačích RHEL ve virtuálních počítačích Azure-Linux | Microsoft Docs
description: Přečtěte si o nastavení vysoké dostupnosti v prostředí clusteru RHEL a nastavení STONITH.
ms.service: virtual-machines-sql
ms.topic: tutorial
author: VanMSFT
ms.author: vanto
ms.reviewer: jroth
ms.date: 06/25/2020
ms.openlocfilehash: 889df16191104ca5b335d067abe0f3d1311c4603
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2021
ms.locfileid: "106450102"
---
# <a name="tutorial-configure-availability-groups-for-sql-server-on-rhel-virtual-machines-in-azure"></a>Kurz: Konfigurace skupin dostupnosti pro SQL Server virtuálních počítačů s RHEL v Azure 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!NOTE]
> V tomto kurzu používáme SQL Server 2017 s RHEL 7,6, ale ke konfiguraci vysoké dostupnosti je možné použít SQL Server 2019 v RHEL 7 nebo RHEL 8. Příkazy pro konfiguraci prostředků clusteru pacemake a skupin dostupnosti se změnily v RHEL 8 a pro další informace o správných příkazech se můžete podívat na článek [vytvoření prostředků skupiny dostupnosti](/sql/linux/sql-server-linux-availability-group-cluster-rhel#create-availability-group-resource) a RHEL 8 prostředků.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> - Vytvořit novou skupinu prostředků, skupinu dostupnosti a virtuální počítače se systémem Linux
> - Povolit vysokou dostupnost (HA)
> - Vytvoření clusteru Pacemaker
> - Konfigurace agenta pro oplocení vytvořením zařízení STONITH
> - Instalace SQL Server a MSSQL-Tools na RHEL
> - Konfigurace skupiny dostupnosti Always On SQL Server
> - Konfigurace prostředků skupiny dostupnosti (AG) v clusteru Pacemaker
> - Testování převzetí služeb při selhání a agenta pro oplocení

Tento kurz použije Azure CLI k nasazení prostředků v Azure.

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../includes/azure-cli-prepare-your-environment.md)]

- Tento článek vyžaduje verzi rozhraní příkazového řádku Azure 2.0.30 nebo novější. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Pokud máte více než jedno předplatné, [nastavte předplatné](/cli/azure/manage-azure-subscriptions-azure-cli) , na které chcete tyto prostředky nasadit.

Pomocí následujícího příkazu vytvořte `<resourceGroupName>` v oblasti skupinu prostředků. Nahraďte názvem, který `<resourceGroupName>` zvolíte. `East US 2`Pro tento kurz používáme. Další informace najdete v následujícím [rychlém](../../../application-gateway/quick-create-cli.md)startu.

```azurecli-interactive
az group create --name <resourceGroupName> --location eastus2
```

## <a name="create-an-availability-set"></a>Vytvoření skupiny dostupnosti

Dalším krokem je vytvoření skupiny dostupnosti. Spusťte v Azure Cloud Shell následující příkaz a nahraďte `<resourceGroupName>` ho názvem vaší skupiny prostředků. Vyberte název `<availabilitySetName>` .

```azurecli-interactive
az vm availability-set create \
    --resource-group <resourceGroupName> \
    --name <availabilitySetName> \
    --platform-fault-domain-count 2 \
    --platform-update-domain-count 2
```

Po dokončení příkazu byste měli získat následující výsledky:

```output
{
  "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/availabilitySets/<availabilitySetName>",
  "location": "eastus2",
  "name": "<availabilitySetName>",
  "platformFaultDomainCount": 2,
  "platformUpdateDomainCount": 2,
  "proximityPlacementGroup": null,
  "resourceGroup": "<resourceGroupName>",
  "sku": {
    "capacity": null,
    "name": "Aligned",
    "tier": null
  },
  "statuses": null,
  "tags": {},
  "type": "Microsoft.Compute/availabilitySets",
  "virtualMachines": []
}
```

## <a name="create-rhel-vms-inside-the-availability-set"></a>Vytvoření virtuálních počítačů s RHEL v rámci skupiny dostupnosti

> [!WARNING]
> Pokud zvolíte image RHEL s průběžnými platbami (PAYG) a nakonfigurujete vysokou dostupnost (HA), může se stát, že budete muset zaregistrovat své předplatné. To může u předplatného platit dvakrát, protože se vám bude účtovat předplatné Microsoft Azure RHEL pro virtuální počítač a předplatné Red Hat. Další informace naleznete v tématu https://access.redhat.com/solutions/2458541.
>
> Abyste se vyhnuli "dvojitému fakturaci", při vytváření virtuálního počítače Azure použijte image RHEL HA. Obrázky, které jsou nabízené jako image RHEL-HA, jsou také PAYG image s úložištěm HA předem povolenou.

1. Získejte seznam imagí virtuálních počítačů, které nabízejí RHEL s HA:

    ```azurecli-interactive
    az vm image list --all --offer "RHEL-HA"
    ```

    Měly by se zobrazit následující výsledky:

    ```output
    [
      {
    "offer": "RHEL-HA",
    "publisher": "RedHat",
    "sku": "7.4",
    "urn": "RedHat:RHEL-HA:7.4:7.4.2019062021",
    "version": "7.4.2019062021"
       },
       {
    "offer": "RHEL-HA",
    "publisher": "RedHat",
    "sku": "7.5",
    "urn": "RedHat:RHEL-HA:7.5:7.5.2019062021",
    "version": "7.5.2019062021"
        },
        {
    "offer": "RHEL-HA",
    "publisher": "RedHat",
    "sku": "7.6",
    "urn": "RedHat:RHEL-HA:7.6:7.6.2019062019",
    "version": "7.6.2019062019"
         },
         {
    "offer": "RHEL-HA",
    "publisher": "RedHat",
    "sku": "8.0",
    "urn": "RedHat:RHEL-HA:8.0:8.0.2020021914",
    "version": "8.0.2020021914"
         },
         {
    "offer": "RHEL-HA",
    "publisher": "RedHat",
    "sku": "8.1",
    "urn": "RedHat:RHEL-HA:8.1:8.1.2020021914",
    "version": "8.1.2020021914"
          },
          {
    "offer": "RHEL-HA",
    "publisher": "RedHat",
    "sku": "80-gen2",
    "urn": "RedHat:RHEL-HA:80-gen2:8.0.2020021915",
    "version": "8.0.2020021915"
           },
           {
    "offer": "RHEL-HA",
    "publisher": "RedHat",
    "sku": "81_gen2",
    "urn": "RedHat:RHEL-HA:81_gen2:8.1.2020021915",
    "version": "8.1.2020021915"
           }
    ]
    ```

    Pro tento kurz si vybíráme obrázek `RedHat:RHEL-HA:7.6:7.6.2019062019` pro příklad RHEL 7 a ZVOLÍTE `RedHat:RHEL-HA:8.1:8.1.2020021914` RHEL 8.
    
    Můžete také zvolit SQL Server 2019 předem nainstalované na obrázcích RHEL8-HA. Seznam těchto imagí získáte spuštěním následujícího příkazu:  
    
    ```azurecli-interactive
    az vm image list --all --offer "sql2019-rhel8"
    ```

    Měly by se zobrazit následující výsledky:

    ```output
    [
      {
    "offer": "sql2019-rhel8",
    "publisher": "MicrosoftSQLServer",
    "sku": "enterprise",
    "urn": "MicrosoftSQLServer:sql2019-rhel8:enterprise:15.0.200317",
    "version": "15.0.200317"
       },
       }
    "offer": "sql2019-rhel8",
    "publisher": "MicrosoftSQLServer",
    "sku": "enterprise",
    "urn": "MicrosoftSQLServer:sql2019-rhel8:enterprise:15.0.200512",
    "version": "15.0.200512"
       },
       {
    "offer": "sql2019-rhel8",
    "publisher": "MicrosoftSQLServer",
    "sku": "sqldev",
    "urn": "MicrosoftSQLServer:sql2019-rhel8:sqldev:15.0.200317",
    "version": "15.0.200317"
       },
       {
    "offer": "sql2019-rhel8",
    "publisher": "MicrosoftSQLServer",
    "sku": "sqldev",
    "urn": "MicrosoftSQLServer:sql2019-rhel8:sqldev:15.0.200512",
    "version": "15.0.200512"
       },
       {
    "offer": "sql2019-rhel8",
    "publisher": "MicrosoftSQLServer",
    "sku": "standard",
    "urn": "MicrosoftSQLServer:sql2019-rhel8:standard:15.0.200317",
    "version": "15.0.200317"
       },
       {
    "offer": "sql2019-rhel8",
    "publisher": "MicrosoftSQLServer",
    "sku": "standard",
    "urn": "MicrosoftSQLServer:sql2019-rhel8:standard:15.0.200512",
    "version": "15.0.200512"
       }
    ]
    ```

    Pokud k vytvoření virtuálních počítačů použijete jednu z výše uvedených imagí, má předinstalované SQL Server 2019. Přeskočte oddíl [Install SQL Server a MSSQL-Tools](#install-sql-server-and-mssql-tools) , jak je popsáno v tomto článku.
    
    
    > [!IMPORTANT]
    > Aby bylo možné nastavit skupinu dostupnosti, musí mít názvy počítačů méně než 15 znaků. Uživatelské jméno nemůže obsahovat velká písmena a hesla musí být delší než 12 znaků.

1. Chceme vytvořit 3 virtuální počítače ve skupině dostupnosti. V následujícím příkazu nahraďte následující:

    - `<resourceGroupName>`
    - `<VM-basename>`
    - `<availabilitySetName>`
    - `<VM-Size>` -Příkladem může být "Standard_D16_v3"
    - `<username>`
    - `<adminPassword>`

    ```azurecli-interactive
    for i in `seq 1 3`; do
           az vm create \
             --resource-group <resourceGroupName> \
             --name <VM-basename>$i \
             --availability-set <availabilitySetName> \
             --size "<VM-Size>"  \
             --image "RedHat:RHEL-HA:7.6:7.6.2019062019" \
             --admin-username "<username>" \
             --admin-password "<adminPassword>" \
             --authentication-type all \
             --generate-ssh-keys
    done
    ```

Pomocí výše uvedeného příkazu se vytvoří virtuální počítače a pro tyto virtuální počítače se vytvoří výchozí virtuální síť. Další informace o různých konfiguracích najdete v článku [AZ VM Create](/cli/azure/vm) .

Po dokončení příkazu u každého virtuálního počítače byste měli získat výsledky podobné následujícímu:

```output
{
  "fqdns": "",
  "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/virtualMachines/<VM1>",
  "location": "eastus2",
  "macAddress": "<Some MAC address>",
  "powerState": "VM running",
  "privateIpAddress": "<IP1>",
  "publicIpAddress": "",
  "resourceGroup": "<resourceGroupName>",
  "zones": ""
}
```

> [!IMPORTANT]
> Výchozí image, která je vytvořena pomocí příkazu výše, vytvoří ve výchozím nastavení disk s operačním systémem 32 GB. Můžete mít pravděpodobně nedostatek místa v této výchozí instalaci. K `az vm create` Vytvoření disku s operačním systémem pomocí 128 GB jako příkladu můžete použít následující parametr přidaný do výše uvedeného příkazu: `--os-disk-size-gb 128` .
>
> Pak můžete [nakonfigurovat Správce logických svazků (LVM)](/previous-versions/azure/virtual-machines/linux/configure-lvm) , pokud potřebujete rozbalit příslušné svazky složek, aby vyhovovaly vaší instalaci.

### <a name="test-connection-to-the-created-vms"></a>Test připojení k vytvořeným virtuálním počítačům

Připojte se k VM1 nebo k ostatním virtuálním počítačům pomocí následujícího příkazu v Azure Cloud Shell. Pokud nemůžete najít IP adresy virtuálních počítačů, postupujte podle tohoto [rychlého startu v Azure Cloud Shell](../../../cloud-shell/quickstart.md#ssh-into-your-linux-vm).

```azurecli-interactive
ssh <username>@publicipaddress
```

Pokud je připojení úspěšné, měl by se zobrazit následující výstup reprezentující terminál pro Linux:

```output
[<username>@<VM1> ~]$
```

Zadejte `exit` , chcete-li opustit relaci SSH.

## <a name="enable-high-availability"></a>Povolit vysokou dostupnost

> [!IMPORTANT]
> Aby bylo možné dokončit tuto část kurzu, musíte mít předplatné pro RHEL a doplněk vysoké dostupnosti. Pokud používáte image doporučenou v předchozí části, nemusíte registrovat jiné předplatné.
 
Připojte se ke každému uzlu virtuálního počítače a postupujte podle pokynů níže, abyste povolili HA. Další informace najdete v tématu [Povolení předplatného vysoké dostupnosti pro RHEL](/sql/linux/sql-server-linux-availability-group-cluster-rhel#enable-the-high-availability-subscription-for-rhel).

> [!TIP]
> Bude jednodušší, pokud otevřete relaci SSH ke každému virtuálnímu počítači současně, protože stejné příkazy bude nutné spustit na každém virtuálním počítači v celém článku.
>
> Pokud kopírujete a vkládáte více `sudo` příkazů a zobrazí se výzva k zadání hesla, další příkazy se nespustí. Spouštějte jednotlivé příkazy samostatně.


1. Spuštěním následujících příkazů na každém virtuálním počítači otevřete porty brány firewall Pacemaker:

    ```bash
    sudo firewall-cmd --permanent --add-service=high-availability
    sudo firewall-cmd --reload
    ```

1. Aktualizujte a nainstalujte balíčky Pacemaker na všech uzlech pomocí následujících příkazů:

    > [!NOTE]
    > **nmap** se instaluje jako součást tohoto bloku příkazů jako nástroj k vyhledání dostupných IP adres ve vaší síti. Nemusíte instalovat **nmap**, ale bude to užitečné později v tomto kurzu.

    ```bash
    sudo yum update -y
    sudo yum install -y pacemaker pcs fence-agents-all resource-agents fence-agents-azure-arm nmap
    sudo reboot
    ```

1. Nastavte heslo pro výchozího uživatele, který se vytvoří při instalaci balíčků Pacemaker. Používejte stejné heslo na všech uzlech.

    ```bash
    sudo passwd hacluster
    ```

1. Pomocí následujícího příkazu otevřete soubor Hosts a nastavte překlad názvu hostitele. Další informace najdete v tématu [Konfigurace AG](/sql/linux/sql-server-linux-availability-group-configure-ha#prerequisites) pro konfiguraci souboru hostitelů.

    ```
    sudo vi /etc/hosts
    ```

    V editoru **VI** zadejte `i` text pro vložení textu a na prázdný řádek přidejte **privátní IP adresu** odpovídajícího virtuálního počítače. Pak přidejte název virtuálního počítače za mezeru vedle IP adresy. Každý řádek by měl mít samostatnou položku.

    ```output
    <IP1> <VM1>
    <IP2> <VM2>
    <IP3> <VM3>
    ```

    > [!IMPORTANT]
    > Doporučujeme použít výše uvedenou **privátní IP** adresu. Použití veřejné IP adresy v této konfiguraci způsobí selhání instalace a nedoporučujeme vystavovat váš virtuální počítač pro externí sítě.

    Chcete-li ukončit Editor **VI** , nejprve stiskněte klávesu **ESC** a potom zadejte příkaz `:wq` pro zápis souboru a ukončení.

## <a name="create-the-pacemaker-cluster"></a>Vytvoření clusteru Pacemaker

V této části povolíme a spustíte službu pcsd a potom nakonfigurujete cluster. V případě SQL Server on Linux se prostředky clusteru automaticky nevytvoří. Budeme muset povolit a vytvořit prostředky Pacemaker ručně. Další informace najdete v článku o [konfiguraci instance clusteru s podporou převzetí služeb při selhání pro RHEL](/sql/linux/sql-server-linux-shared-disk-cluster-red-hat-7-configure#install-and-configure-pacemaker-on-each-cluster-node) .

### <a name="enable-and-start-pcsd-service-and-pacemaker"></a>Povolení a spuštění služby pcsd a Pacemaker

1. Spusťte příkazy na všech uzlech. Tyto příkazy umožní, aby se uzly po restartování znovu připojily ke clusteru.

    ```bash
    sudo systemctl enable pcsd
    sudo systemctl start pcsd
    sudo systemctl enable pacemaker
    ``` 

1. Odeberte všechny existující konfigurace clusteru ze všech uzlů. Spusťte následující příkaz:

    ```bash
    sudo pcs cluster destroy 
    sudo systemctl enable pacemaker 
    ```

1. Na primárním uzlu spusťte následující příkazy a nastavte cluster.

    - Při spuštění `pcs cluster auth` příkazu pro ověření uzlů clusteru se zobrazí výzva k zadání hesla. Zadejte heslo pro uživatele **hacluster** , který jste vytvořili dříve.

    **RHEL7**

    ```bash
    sudo pcs cluster auth <VM1> <VM2> <VM3> -u hacluster
    sudo pcs cluster setup --name az-hacluster <VM1> <VM2> <VM3> --token 30000
    sudo pcs cluster start --all
    sudo pcs cluster enable --all
    ```

    **RHEL8**

    Pro RHEL 8 budete muset uzly ověřovat samostatně. Po zobrazení výzvy zadejte do pole uživatelské jméno a heslo pro **hacluster** ručně.

    ```bash
    sudo pcs host auth <node1> <node2> <node3>
    sudo pcs cluster setup <clusterName> <node1> <node2> <node3>
    sudo pcs cluster start --all
    sudo pcs cluster enable --all
    ```

1. Spusťte následující příkaz a ověřte, zda jsou všechny uzly online.

    ```bash
    sudo pcs status
    ```

   **RHEL 7** 
   
    Pokud jsou všechny uzly online, zobrazí se výstup podobný následujícímu:

    ```output
    Cluster name: az-hacluster
     
    WARNINGS:
    No stonith devices and stonith-enabled is not false
     
    Stack: corosync
    Current DC: <VM2> (version 1.1.19-8.el7_6.5-c3c624ea3d) - partition with quorum
    Last updated: Fri Aug 23 18:27:57 2019
    Last change: Fri Aug 23 18:27:56 2019 by hacluster via crmd on <VM2>
     
    3 nodes configured
    0 resources configured
     
    Online: [ <VM1> <VM2> <VM3> ]
     
    No resources
     
     
    Daemon Status:
          corosync: active/enabled
          pacemaker: active/enabled
          pcsd: active/enabled
    ```
   
   **RHEL 8** 
   
    ```output
    Cluster name: az-hacluster
     
    WARNINGS:
    No stonith devices and stonith-enabled is not false
     
    Cluster Summary:
    * Stack: corosync
    * Current DC: <VM2> (version 1.1.19-8.el7_6.5-c3c624ea3d) - partition with quorum
    * Last updated: Fri Aug 23 18:27:57 2019
    * Last change: Fri Aug 23 18:27:56 2019 by hacluster via crmd on <VM2>
    * 3 nodes configured
    * 0 resource instances configured
     
   Node List:
    * Online: [ <VM1> <VM2> <VM3> ]
   
   Full List of Resources:
   * No resources
     
   Daemon Status:
          corosync: active/enabled
          pacemaker: active/enabled
          pcsd: active/enabled
    
    ```
    
1. Nastavte v živém clusteru očekávané hlasy na 3. Tento příkaz ovlivní pouze živý cluster a nemění konfigurační soubory.

    Na všech uzlech nastavte pomocí následujícího příkazu očekávané hlasy:

    ```bash
    sudo pcs quorum expected-votes 3
    ```

## <a name="configure-the-fencing-agent"></a>Konfigurace agenta pro oplocení

Zařízení STONITH poskytuje agenta pro oplocení. Pokyny pro tento kurz jsou upraveny níže. Další informace najdete v tématu [Vytvoření zařízení STONITH](../../../virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker.md#create-stonith-device).
 
[Zkontrolujte verzi agenta Azure plot, abyste měli jistotu, že je aktualizovaný](../../../virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker.md#cluster-installation). Použijte následující příkaz:

```bash
sudo yum info fence-agents-azure-arm
```

Měl by se zobrazit podobný výstup jako v následujícím příkladu.

```output
Loaded plugins: langpacks, product-id, search-disabled-repos, subscription-manager
Installed Packages
Name        : fence-agents-azure-arm
Arch        : x86_64
Version     : 4.2.1
Release     : 11.el7_6.8
Size        : 28 k
Repo        : installed
From repo   : rhel-ha-for-rhel-7-server-eus-rhui-rpms
Summary     : Fence agent for Azure Resource Manager
URL         : https://github.com/ClusterLabs/fence-agents
License     : GPLv2+ and LGPLv2+
Description : The fence-agents-azure-arm package contains a fence agent for Azure instances.
```

### <a name="register-a-new-application-in-azure-active-directory"></a>Registrace nové aplikace v Azure Active Directory
 
 1. Přejděte na https://portal.azure.com.
 2. Otevřete okno [Azure Active Directory](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties). Přejděte na vlastnosti a zapište ID adresáře. Toto je `tenant ID`
 3. Klikněte na [ **Registrace aplikací**](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)
 4. Klikněte na **Nová registrace** .
 5. Zadejte **název** , jako `<resourceGroupName>-app` je, vyberte **účty pouze v tomto adresáři organizace**
 6. Vyberte možnost **Web** typu aplikace, zadejte adresu URL pro přihlášení (například http://localhost) a klikněte na Přidat. Přihlašovací adresa URL se nepoužívá a může to být jakákoli platná adresa URL. Po dokončení klikněte na **zaregistrovat** .
 7. Vyberte **certifikáty a tajné klíče** pro novou registraci aplikace a pak klikněte na **nový tajný klíč klienta** .
 8. Zadejte popis nového klíče (tajný klíč klienta), vyberte možnost **nikdy nevyprší** a klikněte na **Přidat** .
 9. Zapište hodnotu tajného kódu. Používá se jako heslo instančního objektu.
10. Vyberte **Přehled**. Poznamenejte si ID aplikace. Používá se jako uživatelské jméno (přihlašovací ID v následujících krocích) instančního objektu.
 
### <a name="create-a-custom-role-for-the-fence-agent"></a>Vytvoření vlastní role pro agenta plotu

Postupujte podle kurzu a [vytvořte vlastní roli Azure pomocí Azure CLI](../../../role-based-access-control/tutorial-custom-role-cli.md#create-a-custom-role).

Váš soubor JSON by měl vypadat nějak takto:

- Nahraďte `<username>` názvem, který jste si zvolili. Při vytváření této definice role se tak vyhnete jakýmkoli duplicitám.
- Nahraďte `<subscriptionId>` ID předplatného Azure.

```json
{
  "Name": "Linux Fence Agent Role-<username>",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows to power-off and start virtual machines",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/powerOff/action",
    "Microsoft.Compute/virtualMachines/start/action"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<subscriptionId>"
  ]
}
```

Chcete-li přidat roli, spusťte následující příkaz:

- Nahraďte `<filename>` názvem souboru.
- Pokud příkaz spouštíte z jiné než složky, do které je soubor uložen, do příkazu zahrňte cestu ke složce souboru.

```bash
az role definition create --role-definition "<filename>.json"
```

Měl by se zobrazit následující výstup:

```output
{
  "assignableScopes": [
    "/subscriptions/<subscriptionId>"
  ],
  "description": "Allows to power-off and start virtual machines",
  "id": "/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/roleDefinitions/<roleNameId>",
  "name": "<roleNameId>",
  "permissions": [
    {
      "actions": [
        "Microsoft.Compute/*/read",
        "Microsoft.Compute/virtualMachines/powerOff/action",
        "Microsoft.Compute/virtualMachines/start/action"
      ],
      "dataActions": [],
      "notActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Linux Fence Agent Role-<username>",
  "roleType": "CustomRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="assign-the-custom-role-to-the-service-principal"></a>Přiřazení vlastní role k instančnímu objektu

Přiřaďte vlastní roli `Linux Fence Agent Role-<username>` vytvořenou v posledním kroku objektu služby. Nepoužívejte již vlastníka role.
 
1. Přejděte na https://portal.azure.com.
2. Otevřete okno [všechny prostředky](https://ms.portal.azure.com/#blade/HubsExtension/BrowseAll) .
3. Vyberte virtuální počítač prvního uzlu clusteru.
4. Klikněte na **řízení přístupu (IAM)** .
5. Klikněte na **Přidat přiřazení role** .
6. Vyberte roli `Linux Fence Agent Role-<username>` ze seznamu **rolí** .
7. V seznamu **Vybrat** zadejte název aplikace, kterou jste vytvořili výše. `<resourceGroupName>-app`
8. Klikněte na **Uložit**.
9. Opakujte výše uvedené kroky pro uzel všechny uzly clusteru.

### <a name="create-the-stonith-devices"></a>Vytvoření zařízení STONITH

V uzlu 1 Spusťte následující příkazy:

- Nahraďte `<ApplicationID>` hodnotu ID z registrace vaší aplikace.
- Nahraďte `<servicePrincipalPassword>` hodnotu hodnotou z tajného klíče klienta.
- Nahraďte `<resourceGroupName>` skupinu prostředků z předplatného, které jste použili pro tento kurz.
- Nahraďte `<tenantID>` a `<subscriptionId>` z předplatného Azure.

```bash
sudo pcs property set stonith-timeout=900
sudo pcs stonith create rsc_st_azure fence_azure_arm login="<ApplicationID>" passwd="<servicePrincipalPassword>" resourceGroup="<resourceGroupName>" tenantId="<tenantID>" subscriptionId="<subscriptionId>" power_timeout=240 pcmk_reboot_timeout=900
```

Vzhledem k tomu, že jsme už přidali pravidlo k naší bráně firewall, aby bylo možné službu HA ( `--add-service=high-availability` ), nemusíte na všech uzlech otevírat následující porty brány firewall: 2224, 3121, 21064, 5405. Pokud ale máte nějaký typ potíží s připojením s HA, otevřete pomocí následujícího příkazu porty, které jsou spojené s HA.

> [!TIP]
> Volitelně můžete přidat všechny porty v tomto kurzu najednou a ušetřit tak čas. Porty, které je třeba otevřít, jsou vysvětleny v jejich relativních částech níže. Pokud chcete nyní přidat všechny porty, přidejte další porty: 1433 a 5022.

```bash
sudo firewall-cmd --zone=public --add-port=2224/tcp --add-port=3121/tcp --add-port=21064/tcp --add-port=5405/tcp --permanent
sudo firewall-cmd --reload
```

## <a name="install-sql-server-and-mssql-tools"></a>Instalace SQL Server a MSSQL-Tools

> [!NOTE]
> Pokud jste vytvořili virtuální počítače s SQL Server 2019 předem nainstalovaným na RHEL8-HA, můžete přeskočit níže uvedené kroky a nainstalovat SQL Server a nástroje MSSQL-Tools a po nastavení hesla sa na všech virtuálních počítačích spustit oddíl **Konfigurovat skupinu dostupnosti** tak, že spustíte příkaz `sudo /opt/mssql/bin/mssql-conf set-sa-password` na všech virtuálních počítačích.

Pomocí níže uvedeného oddílu můžete na virtuální počítače nainstalovat SQL Server a nástroje MSSQL. Můžete vybrat jednu z níže uvedených ukázek a nainstalovat SQL Server 2017 na RHEL 7 nebo SQL Server 2019 na RHEL 8. Proveďte každou z těchto akcí na všech uzlech. Další informace najdete v tématu [instalace SQL Server na virtuálním počítači Red Hat](/sql/linux/quickstart-install-connect-red-hat).


### <a name="installing-sql-server-on-the-vms"></a>Instalace SQL Server na virtuální počítače

K instalaci SQL Server slouží následující příkazy:

**RHEL 7 s SQL Server 2017** 

```bash
sudo curl -o /etc/yum.repos.d/mssql-server.repo https://packages.microsoft.com/config/rhel/7/mssql-server-2017.repo
sudo yum install -y mssql-server
sudo /opt/mssql/bin/mssql-conf setup
sudo yum install mssql-server-ha
```

**RHEL 8 s SQL Server 2019** 

```bash
sudo curl -o /etc/yum.repos.d/mssql-server.repo https://packages.microsoft.com/config/rhel/8/mssql-server-2019.repo
sudo yum install -y mssql-server
sudo /opt/mssql/bin/mssql-conf setup
sudo yum install mssql-server-ha
```
### <a name="open-firewall-port-1433-for-remote-connections"></a>Otevřete port brány firewall 1433 pro vzdálená připojení.

Aby se bylo možné vzdáleně připojit, budete muset na virtuálním počítači otevřít port 1433. K otevření portu 1433 v bráně firewall každého virtuálního počítače použijte následující příkazy:

```bash
sudo firewall-cmd --zone=public --add-port=1433/tcp --permanent
sudo firewall-cmd --reload
```

### <a name="installing-sql-server-command-line-tools"></a>Instalace nástrojů příkazového řádku SQL Server

Následující příkazy se používají k instalaci SQL Server nástrojů příkazového řádku. Další informace najdete v tématu [Instalace nástrojů příkazového řádku SQL Server](/sql/linux/quickstart-install-connect-red-hat#tools).

**RHEL 7** 

```bash
sudo curl -o /etc/yum.repos.d/msprod.repo https://packages.microsoft.com/config/rhel/7/prod.repo
sudo yum install -y mssql-tools unixODBC-devel
```

**RHEL 8** 

```bash
sudo curl -o /etc/yum.repos.d/msprod.repo https://packages.microsoft.com/config/rhel/8/prod.repo
sudo yum install -y mssql-tools unixODBC-devel
```
 
> [!NOTE] 
> Pro usnadnění přidejte/opt/MSSQL-Tools/bin/do proměnné prostředí PATH. To vám umožní spustit nástroje bez zadání úplné cesty. Spuštěním následujících příkazů upravte PATH pro relace přihlášení i interaktivní relace a relace bez přihlášení:</br></br>
`echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bash_profile`</br>
`echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bashrc`</br>
`source ~/.bashrc`


### <a name="check-the-status-of-the-sql-server"></a>Ověřte stav SQL Server

Po dokončení konfigurace můžete zkontrolovat stav SQL Server a ověřit, jestli je spuštěný:

```bash
systemctl status mssql-server --no-pager
```

Měl by se zobrazit následující výstup:

```output
● mssql-server.service - Microsoft SQL Server Database Engine
   Loaded: loaded (/usr/lib/systemd/system/mssql-server.service; enabled; vendor preset: disabled)
   Active: active (running) since Thu 2019-12-05 17:30:55 UTC; 20min ago
     Docs: https://docs.microsoft.com/en-us/sql/linux
 Main PID: 11612 (sqlservr)
   CGroup: /system.slice/mssql-server.service
           ├─11612 /opt/mssql/bin/sqlservr
           └─11640 /opt/mssql/bin/sqlservr
```

## <a name="configure-an-availability-group"></a>Konfigurace skupiny dostupnosti

Následující postup použijte ke konfiguraci skupiny dostupnosti Always On SQL Server pro vaše virtuální počítače. Další informace najdete v tématu [Konfigurace skupin dostupnosti Always On SQL Server pro zajištění vysoké dostupnosti v systému Linux](/sql/linux/sql-server-linux-availability-group-configure-ha) .

### <a name="enable-always-on-availability-groups-and-restart-mssql-server"></a>Povolit skupiny dostupnosti Always On a restartovat server MSSQL

V každém uzlu, který je hostitelem instance SQL Server, povolte skupiny dostupnosti Always On. Pak restartujte server MSSQL. Spusťte tento skript:

```
sudo /opt/mssql/bin/mssql-conf set hadr.hadrenabled 1
sudo systemctl restart mssql-server
```

### <a name="create-a-certificate"></a>Vytvoření certifikátu

V současné době nepodporujeme ověřování AD na koncový bod AG. Proto je nutné použít certifikát pro šifrování koncového bodu AG.

1. Připojte se ke **všem uzlům** pomocí SQL Server Management Studio (SSMS) nebo SQL cmd. Spuštěním následujících příkazů povolte relaci AlwaysOn_health a vytvořte hlavní klíč:

    > [!IMPORTANT]
    > Pokud se ke své instanci SQL Server vzdáleně připojujete, budete muset na bráně firewall otevřít port 1433. Pro každý virtuální počítač budete taky muset povolit příchozí připojení k portu 1433 ve vaší NSG. Další informace najdete v tématu [Vytvoření pravidla zabezpečení](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) pro vytvoření příchozího pravidla zabezpečení.

    - Nahraďte `<Master_Key_Password>` vlastními hesly.


    ```sql
    ALTER EVENT SESSION  AlwaysOn_health ON SERVER WITH (STARTUP_STATE=ON);
    GO
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<Master_Key_Password>';
    ```

 
1. Připojte se k primární replice pomocí SSMS nebo SQL CMD. V následujících příkazech se vytvoří certifikát na `/var/opt/mssql/data/dbm_certificate.cer` `var/opt/mssql/data/dbm_certificate.pvk` primárním SQL Server replice a privátní klíč:

    - Nahraďte `<Private_Key_Password>` vlastními hesly.
    
    ```sql
    CREATE CERTIFICATE dbm_certificate WITH SUBJECT = 'dbm';
    GO
    
    BACKUP CERTIFICATE dbm_certificate
       TO FILE = '/var/opt/mssql/data/dbm_certificate.cer'
       WITH PRIVATE KEY (
               FILE = '/var/opt/mssql/data/dbm_certificate.pvk',
               ENCRYPTION BY PASSWORD = '<Private_Key_Password>'
           );
    GO
    ```

Ukončete relaci SQL CMD spuštěním `exit` příkazu a vraťte se zpět do relace SSH.
 
### <a name="copy-the-certificate-to-the-secondary-replicas-and-create-the-certificates-on-the-server"></a>Zkopírujte certifikát do sekundárních replik a vytvořte na serveru certifikáty.

1. Zkopírujte dva soubory, které byly vytvořeny do stejného umístění na všech serverech, které budou hostovat repliky dostupnosti.
 
    Na primárním serveru spusťte následující `scp` příkaz pro zkopírování certifikátu na cílové servery:

    - Nahraďte `<username>` a názvem `<VM2>` uživatelského jména a cílového virtuálního počítače, který používáte.
    - Spusťte tento příkaz pro všechny sekundární repliky.

    > [!NOTE]
    > Nemusíte spouštět `sudo -i` , což vám poskytne kořenové prostředí. Mohli byste jenom spustit `sudo` příkaz před každým příkazem, jako jsme to předtím v tomto kurzu.

    ```bash
    # The below command allows you to run commands in the root environment
    sudo -i
    ```

    ```bash
    scp /var/opt/mssql/data/dbm_certificate.* <username>@<VM2>:/home/<username>
    ```

1. Na cílovém serveru spusťte následující příkaz:

    - Nahraďte `<username>` uživatelským jménem.
    - `mv`Příkaz přesune soubory nebo adresář z jednoho místa do druhého.
    - `chown`Příkaz slouží ke změně vlastníka a skupiny souborů, adresářů nebo odkazů.
    - Spusťte tyto příkazy pro všechny sekundární repliky.

    ```bash
    sudo -i
    mv /home/<username>/dbm_certificate.* /var/opt/mssql/data/
    cd /var/opt/mssql/data
    chown mssql:mssql dbm_certificate.*
    ```

1. Následující skript Transact-SQL vytvoří certifikát ze zálohy, kterou jste vytvořili v primární replice SQL Server. Aktualizujte skript pomocí silných hesel. Šifrovací heslo je stejné heslo, které jste použili k vytvoření souboru. PVK v předchozím kroku. Chcete-li vytvořit certifikát, spusťte následující skript pomocí příkazu SQL CMD nebo SSMS na všech sekundárních serverech:

    ```sql
    CREATE CERTIFICATE dbm_certificate
        FROM FILE = '/var/opt/mssql/data/dbm_certificate.cer'
        WITH PRIVATE KEY (
        FILE = '/var/opt/mssql/data/dbm_certificate.pvk',
        DECRYPTION BY PASSWORD = '<Private_Key_Password>'
                );
    GO
    ```

### <a name="create-the-database-mirroring-endpoints-on-all-replicas"></a>Vytvoření koncových bodů zrcadlení databáze ve všech replikách

Spusťte následující skript na všech instancích SQL Server pomocí SQL CMD nebo SSMS:

```sql
CREATE ENDPOINT [Hadr_endpoint]
    AS TCP (LISTENER_PORT = 5022)
    FOR DATABASE_MIRRORING (
    ROLE = ALL,
    AUTHENTICATION = CERTIFICATE dbm_certificate,
ENCRYPTION = REQUIRED ALGORITHM AES
);
GO

ALTER ENDPOINT [Hadr_endpoint] STATE = STARTED;
GO
```

### <a name="create-the-availability-group"></a>Vytvoření skupiny dostupnosti

Připojte se k instanci SQL Server, která hostuje primární repliku, pomocí příkazu SQL CMD nebo SSMS. Spuštěním následujícího příkazu vytvořte skupinu dostupnosti:

- Nahraďte `ag1` požadovaným názvem skupiny dostupnosti.
- `<VM1>` `<VM2>` Hodnoty, a nahraďte `<VM3>` názvy instancí SQL Server, které hostují repliky.

```sql
CREATE AVAILABILITY GROUP [ag1]
     WITH (DB_FAILOVER = ON, CLUSTER_TYPE = EXTERNAL)
     FOR REPLICA ON
         N'<VM1>'
          WITH (
             ENDPOINT_URL = N'tcp://<VM1>:5022',
             AVAILABILITY_MODE = SYNCHRONOUS_COMMIT,
             FAILOVER_MODE = EXTERNAL,
             SEEDING_MODE = AUTOMATIC
             ),
         N'<VM2>'
          WITH (
             ENDPOINT_URL = N'tcp://<VM2>:5022',
             AVAILABILITY_MODE = SYNCHRONOUS_COMMIT,
             FAILOVER_MODE = EXTERNAL,
             SEEDING_MODE = AUTOMATIC
             ),
         N'<VM3>'
         WITH(
            ENDPOINT_URL = N'tcp://<VM3>:5022',
            AVAILABILITY_MODE = SYNCHRONOUS_COMMIT,
            FAILOVER_MODE = EXTERNAL,
            SEEDING_MODE = AUTOMATIC
            );
GO

ALTER AVAILABILITY GROUP [ag1] GRANT CREATE ANY DATABASE;
GO
```

### <a name="create-a-sql-server-login-for-pacemaker"></a>Vytvoření SQL Server přihlašovacího jména pro Pacemaker

U všech instancí SQL Server vytvořte SQL Server přihlášení pro Pacemaker. Následující příkaz Transact-SQL vytvoří přihlášení.

- Nahraďte `<password>` vlastními složitými hesly.

```sql
USE [master]
GO

CREATE LOGIN [pacemakerLogin] with PASSWORD= N'<password>';
GO

ALTER SERVER ROLE [sysadmin] ADD MEMBER [pacemakerLogin];
GO
```

V části všechny SQL Server instance uložte přihlašovací údaje používané pro přihlášení SQL Server. 

1. Vytvořte soubor:

    ```bash
    sudo vi /var/opt/mssql/secrets/passwd
    ```

1. Přidejte následující dva řádky do souboru:

    ```bash
    pacemakerLogin
    <password>
    ```

    Chcete-li ukončit Editor **VI** , nejprve stiskněte klávesu **ESC** a potom zadejte příkaz `:wq` pro zápis souboru a ukončení.

1. Nastavit soubor jen jako čitelný v kořenovém adresáři:

    ```bash
    sudo chown root:root /var/opt/mssql/secrets/passwd
    sudo chmod 400 /var/opt/mssql/secrets/passwd
    ```

### <a name="join-secondary-replicas-to-the-availability-group"></a>Připojit sekundární repliky do skupiny dostupnosti

1. Aby bylo možné připojit sekundární repliky ke službě AG, budete muset otevřít port 5022 na bráně firewall pro všechny servery. V relaci SSH spusťte následující příkaz:

    ```bash
    sudo firewall-cmd --zone=public --add-port=5022/tcp --permanent
    sudo firewall-cmd --reload
    ```

1. V případě sekundárních replik spusťte následující příkazy, které je připojte k AG:

    ```sql
    ALTER AVAILABILITY GROUP [ag1] JOIN WITH (CLUSTER_TYPE = EXTERNAL);
    GO

    ALTER AVAILABILITY GROUP [ag1] GRANT CREATE ANY DATABASE;
    GO
    ```

1. Spusťte následující skript Transact-SQL na primární replice a na každé sekundární replice:

    ```sql
    GRANT ALTER, CONTROL, VIEW DEFINITION ON AVAILABILITY GROUP::ag1 TO pacemakerLogin;
    GO
    
    GRANT VIEW SERVER STATE TO pacemakerLogin;
    GO
    ```

1. Až budou sekundární repliky připojené, můžete je zobrazit v SSMS Průzkumník objektů rozbalením uzlu **vždy na vysokou dostupnost** :

    ![Snímek obrazovky se zobrazí v primárních a sekundárních replikách dostupnosti.](./media/rhel-high-availability-stonith-tutorial/availability-group-joined.png)

### <a name="add-a-database-to-the-availability-group"></a>Přidání databáze do skupiny dostupnosti

Budeme postupovat podle [článku Konfigurace skupiny dostupnosti týkající se přidání databáze](/sql/linux/sql-server-linux-availability-group-configure-ha#add-a-database-to-the-availability-group).

V tomto kroku se používají následující příkazy jazyka Transact-SQL. Spusťte tyto příkazy na primární replice:

```sql
CREATE DATABASE [db1]; -- creates a database named db1
GO

ALTER DATABASE [db1] SET RECOVERY FULL; -- set the database in full recovery mode
GO

BACKUP DATABASE [db1] -- backs up the database to disk
   TO DISK = N'/var/opt/mssql/data/db1.bak';
GO

ALTER AVAILABILITY GROUP [ag1] ADD DATABASE [db1]; -- adds the database db1 to the AG
GO
```

### <a name="verify-that-the-database-is-created-on-the-secondary-servers"></a>Ověření, jestli je databáze vytvořená na sekundárních serverech

V každé sekundární replice SQL Server spusťte následující dotaz, který zjistí, zda byla databáze DB1 vytvořena a je v SYNCHRONIZOVANÉm stavu:

```
SELECT * FROM sys.databases WHERE name = 'db1';
GO
SELECT DB_NAME(database_id) AS 'database', synchronization_state_desc FROM sys.dm_hadr_database_replica_states;
```

Pokud jsou `synchronization_state_desc` seznamy synchronizované pro `db1` , znamená to, že repliky se synchronizují. Sekundární `db1` replika se zobrazuje v primární replice.

## <a name="create-availability-group-resources-in-the-pacemaker-cluster"></a>Vytvoření prostředků skupiny dostupnosti v clusteru Pacemaker

Po [vytvoření prostředků skupiny dostupnosti v clusteru Pacemaker](/sql/linux/sql-server-linux-create-availability-group#create-the-availability-group-resources-in-the-pacemaker-cluster-external-only)budeme postupovat podle pokynů.

> [!NOTE]
> Tento článek obsahuje odkazy na podřízený termín, termín, který už Microsoft nepoužívá. Po odebrání termínu ze softwaru ho odebereme z tohoto článku.

### <a name="create-the-ag-cluster-resource"></a>Vytvoření prostředku clusteru AG

1. Použijte jeden z následujících příkazů na základě dříve zvoleného prostředí a vytvořte prostředek `ag_cluster` ve skupině dostupnosti `ag1` .

    **RHEL 7**
  
    ```bash
    sudo pcs resource create ag_cluster ocf:mssql:ag ag_name=ag1 meta failure-timeout=30s master notify=true
    ```

    **RHEL 8**
  
    ```bash
    sudo pcs resource create ag_cluster ocf:mssql:ag ag_name=ag1 meta failure-timeout=30s promotable notify=true
    ```

2. Zkontrolujte svůj prostředek a ujistěte se, že jsou online, než budete pokračovat pomocí následujícího příkazu:

    ```bash
    sudo pcs resource
    ```

    Měl by se zobrazit následující výstup:
    
    **RHEL 7** 
    
    ```output
    [<username>@VM1 ~]$ sudo pcs resource
    Master/Slave Set: ag_cluster-master [ag_cluster]
    Masters: [ <VM1> ]
    Slaves: [ <VM2> <VM3> ]
    ```
    
    **RHEL 8** 
    
    ```output
    [<username>@VM1 ~]$ sudo pcs resource
    * Clone Set: ag_cluster-clone [ag_cluster] (promotable):
    * ag_cluster             (ocf::mssql:ag) :            Slave VMrhel3 (Monitoring) 
    * ag_cluster             (ocf::mssql:ag) :            Master VMrhel1 (Monitoring)
    * ag_cluster             (ocf::mssql:ag) :            Slave VMrhel2 (Monitoring)
    ```


### <a name="create-a-virtual-ip-resource"></a>Vytvoření prostředku virtuální IP adresy

1. K vytvoření prostředku virtuální IP adresy použijte dostupnou statickou IP adresu ze sítě. Můžete ji najít pomocí nástroje příkazového řádku `nmap` .

    ```bash
    nmap -sP <IPRange>
    # For example: nmap -sP 10.0.0.*
    # The above will scan for all IP addresses that are already occupied in the 10.0.0.x space.
    ```

2. Nastavte vlastnost **stonith-Enabled** na hodnotu false.

    ```bash
    sudo pcs property set stonith-enabled=false
    ```

3. Vytvořte prostředek virtuální IP adresy pomocí následujícího příkazu:

    - `<availableIP>`Hodnotu uvedenou níže nahraďte nepoužitou IP adresou.

    ```bash
    sudo pcs resource create virtualip ocf:heartbeat:IPaddr2 ip=<availableIP>
    ```

### <a name="add-constraints"></a>Přidat omezení

1. Aby bylo zajištěno, že IP adresa a prostředek AG běží na stejném uzlu, musí být nakonfigurováno omezení pro společné umístění. Spusťte následující příkaz:

   **RHEL 7**
  
    ```bash
    sudo pcs constraint colocation add virtualip ag_cluster-master INFINITY with-rsc-role=Master
    ```

   **RHEL 8**
   
    ```bash
     sudo pcs constraint colocation add virtualip with master ag_cluster-clone INFINITY with-rsc-role=Master
    ```
  
2. Vytvořte omezení řazení, aby bylo zajištěno, že prostředek AG bude spuštěn před IP adresou. I když omezení pro společné umístění implikuje omezení řazení, vynutilo ho.

   **RHEL 7**
   
    ```bash
    sudo pcs constraint order promote ag_cluster-master then start virtualip
    ```

   **RHEL 8**
   
    ```bash
    sudo pcs constraint order promote ag_cluster-clone then start virtualip
    ```
  
3. Chcete-li ověřit omezení, spusťte následující příkaz:

    ```bash
    sudo pcs constraint list --full
    ```

    Měl by se zobrazit následující výstup:
    
    **RHEL 7**

    ```
    Location Constraints:
    Ordering Constraints:
          promote ag_cluster-master then start virtualip (kind:Mandatory) (id:order-ag_cluster-master-virtualip-mandatory)
    Colocation Constraints:
          virtualip with ag_cluster-master (score:INFINITY) (with-rsc-role:Master) (id:colocation-virtualip-ag_cluster-master-INFINITY)
    Ticket Constraints:
    ```
    
    **RHEL 8**
    
    ```output
    Location Constraints:
    Ordering Constraints:
            promote ag_cluster-clone then start virtualip (kind:Mandatory) (id:order-ag_cluster-clone-virtualip-mandatory)
    Colocation Constraints:
            virtualip with ag_cluster-clone (score:INFINITY) (with-rsc-role:Master) (id:colocation-virtualip-ag_cluster-clone-INFINITY)
    Ticket Constraints:
    ```

### <a name="re-enable-stonith"></a>Opětovné povolení stonith

Jsme připraveni na testování. V clusteru znovu povolte stonith spuštěním následujícího příkazu v uzlu 1:

```bash
sudo pcs property set stonith-enabled=true
```

### <a name="check-cluster-status"></a>Kontrola stavu clusteru

Stav prostředků clusteru můžete zjistit pomocí následujícího příkazu:

```output
[<username>@VM1 ~]$ sudo pcs status
Cluster name: az-hacluster
Stack: corosync
Current DC: <VM3> (version 1.1.19-8.el7_6.5-c3c624ea3d) - partition with quorum
Last updated: Sat Dec  7 00:18:38 2019
Last change: Sat Dec  7 00:18:02 2019 by root via cibadmin on VM1

3 nodes configured
5 resources configured

Online: [ <VM1> <VM2> <VM3> ]

Full list of resources:

 Master/Slave Set: ag_cluster-master [ag_cluster]
     Masters: [ <VM2> ]
     Slaves: [ <VM1> <VM3> ]
 virtualip      (ocf::heartbeat:IPaddr2):       Started <VM2>
 rsc_st_azure   (stonith:fence_azure_arm):      Started <VM1>

Daemon Status:
  corosync: active/enabled
  pacemaker: active/enabled
  pcsd: active/enabled
```

## <a name="test-failover"></a>Testovací převzetí služeb při selhání

Abychom zajistili, že se konfigurace úspěšně provedla, otestujeme převzetí služeb při selhání. Další informace najdete v tématu [převzetí služeb při selhání skupiny dostupnosti Always On v systému Linux](/sql/linux/sql-server-linux-availability-group-failover-ha).

1. Spusťte následující příkaz, který ručně převezme služby primární repliky na `<VM2>` . Nahraďte `<VM2>` hodnotou názvu vašeho serveru.

   **RHEL 7**
   
    ```bash
    sudo pcs resource move ag_cluster-master <VM2> --master
    ```

   **RHEL 8**
   
    ```bash
    sudo pcs resource move ag_cluster-clone <VM2> --master
    ```

   Můžete také zadat další možnost, aby dočasné omezení vytvořené pro přesunutí prostředku na požadovaný uzel bylo automaticky zakázáno a není nutné provádět kroky 2 a 3 níže.

   **RHEL 7**

    ```bash
    sudo pcs resource move ag_cluster-master <VM2> --master lifetime=30S
    ```

   **RHEL 8**

    ```bash
    sudo pcs resource move ag_cluster-clone <VM2> --master lifetime=30S
    ```

   Další alternativou pro automatizaci kroků 2 a 3 níže, které zruší dočasné omezení v příkazu pro přesunutí prostředku, je spojením více příkazů na jednom řádku. 

   **RHEL 7**

    ```bash
    sudo pcs resource move ag_cluster-master <VM2> --master && sleep 30 && pcs resource clear ag_cluster-master
    ```

   **RHEL 8**

    ```bash
    sudo pcs resource move ag_cluster-clone <VM2> --master && sleep 30 && pcs resource clear ag_cluster-clone
    ```
    
2. Pokud znovu zkontrolujete vaše omezení, uvidíte, že se kvůli ručnímu převzetí služeb při selhání přidalo jiné omezení:
    
    **RHEL 7**
    
    ```output
    [<username>@VM1 ~]$ sudo pcs constraint list --full
    Location Constraints:
          Resource: ag_cluster-master
            Enabled on: VM2 (score:INFINITY) (role: Master) (id:cli-prefer-ag_cluster-master)
    Ordering Constraints:
            promote ag_cluster-master then start virtualip (kind:Mandatory) (id:order-ag_cluster-master-virtualip-mandatory)
    Colocation Constraints:
            virtualip with ag_cluster-master (score:INFINITY) (with-rsc-role:Master) (id:colocation-virtualip-ag_cluster-master-INFINITY)
    Ticket Constraints:
    ```

    **RHEL 8**
    
    ```output
    [<username>@VM1 ~]$ sudo pcs constraint list --full
    Location Constraints:
          Resource: ag_cluster-master
            Enabled on: VM2 (score:INFINITY) (role: Master) (id:cli-prefer-ag_cluster-clone)
    Ordering Constraints:
            promote ag_cluster-clone then start virtualip (kind:Mandatory) (id:order-ag_cluster-clone-virtualip-mandatory)
    Colocation Constraints:
            virtualip with ag_cluster-clone (score:INFINITY) (with-rsc-role:Master) (id:colocation-virtualip-ag_cluster-clone-INFINITY)
    Ticket Constraints:
    ```
    
3. Odeberte omezení s ID `cli-prefer-ag_cluster-master` pomocí následujícího příkazu:

    **RHEL 7**
    
    ```bash
    sudo pcs constraint remove cli-prefer-ag_cluster-master
    ```

    **RHEL 8**
    
    ```bash
    sudo pcs constraint remove cli-prefer-ag_cluster-clone
    ```

1. Pomocí příkazu zkontrolujte prostředky clusteru `sudo pcs resource` a měli byste vidět, že je primární instance nyní `<VM2>` .

    ```output
    [<username>@<VM1> ~]$ sudo pcs resource
    Master/Slave Set: ag_cluster-master [ag_cluster]
         ag_cluster (ocf::mssql:ag):        FAILED <VM1> (Monitoring)
         Masters: [ <VM2> ]
         Slaves: [ <VM3> ]
    virtualip      (ocf::heartbeat:IPaddr2):       Started <VM2>
    [<username>@<VM1> ~]$ sudo pcs resource
    Master/Slave Set: ag_cluster-master [ag_cluster]
         Masters: [ <VM2> ]
         Slaves: [ <VM1> <VM3> ]
    virtualip      (ocf::heartbeat:IPaddr2):       Started <VM2>
    ```

## <a name="test-fencing"></a>Oplocení testů

STONITH můžete otestovat spuštěním následujícího příkazu. Zkuste spustit níže uvedený příkaz z `<VM1>` pro `<VM3>` .

```bash
sudo pcs stonith fence <VM3> --debug
```

> [!NOTE]
> Ve výchozím nastavení akce plotu přesune uzel a pak na. Pokud chcete pouze převést uzel do režimu offline, použijte možnost `--off` v příkazu.

Měli byste získat následující výstup:

```output
[<username>@<VM1> ~]$ sudo pcs stonith fence <VM3> --debug
Running: stonith_admin -B <VM3>
Return Value: 0
--Debug Output Start--
--Debug Output End--
 
Node: <VM3> fenced
```
Další informace o testování ochranného zařízení najdete v následujícím článku o [Red Hat](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/s1-stonithtest-haar) .

## <a name="next-steps"></a>Další kroky

Aby bylo možné využít naslouchací proces skupiny dostupnosti pro instance SQL Server, budete muset vytvořit a nakonfigurovat nástroj pro vyrovnávání zatížení.

> [!div class="nextstepaction"]
> [Kurz: Konfigurace naslouchacího procesu skupiny dostupnosti pro SQL Server na virtuálních počítačích s RHEL v Azure](rhel-high-availability-listener-tutorial.md)
