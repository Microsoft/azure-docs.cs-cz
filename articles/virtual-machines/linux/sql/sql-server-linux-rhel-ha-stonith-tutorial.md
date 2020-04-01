---
title: Konfigurace skupin dostupnosti pro SQL Server na virtuálních počítačích RHEL v Azure – virtuální počítače S IF | Dokumenty společnosti Microsoft
description: Informace o nastavení vysoké dostupnosti v prostředí clusteru RHEL a nastavení STONITH
ms.service: virtual-machines-linux
ms.subservice: ''
ms.topic: tutorial
author: VanMSFT
ms.author: vanto
ms.reviewer: jroth
ms.date: 02/27/2020
ms.openlocfilehash: 40c91f67231fb6a9d01191ee5215eae8d4dc045b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79096702"
---
# <a name="tutorial-configure-availability-groups-for-sql-server-on-rhel-virtual-machines-in-azure"></a>Kurz: Konfigurace skupin dostupnosti pro SQL Server na virtuálních počítačích RHEL v Azure 

> [!NOTE]
> Prezentovaný kurz je ve **verzi Public Preview**. 
>
> V tomto kurzu používáme SQL Server 2017 s RHEL 7.6, ale je možné použít SQL Server 2019 v RHEL 7 nebo RHEL 8 ke konfiguraci HA. Příkazy ke konfiguraci prostředků skupiny dostupnosti se změnily v RHEL 8 a budete chtít podívat na článek, [Vytvořit zdroj skupiny dostupnosti](/sql/linux/sql-server-linux-availability-group-cluster-rhel#create-availability-group-resource) a zdroje RHEL 8 pro další informace o správné příkazy.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> - Vytvoření nové skupiny prostředků, sady dostupnosti a virtuálních počítačů Azure Linux (VM)
> - Povolit vysokou dostupnost (HA)
> - Vytvoření clusteru kardiostimulátoru
> - Konfigurace agenta oplocení vytvořením zařízení STONITH
> - Instalace SQL Serveru a nástrojů mssql na RHEL
> - Konfigurace serveru SQL Server always on Availability Group
> - Konfigurace prostředků skupiny dostupnosti (AG) v clusteru Pacemaker
> - Otestujte převzetí služeb při selhání a agenta oplocení

Tento kurz bude používat rozhraní příkazového řádku Azure (CLI) k nasazení prostředků v Azure.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Pokud dáváte přednost instalaci a použití příkazového příkazu k místnímu použití, tento kurz vyžaduje Azure CLI verze 2.0.30 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Pokud máte více než jedno předplatné, [nastavte předplatné,](/cli/azure/manage-azure-subscriptions-azure-cli) které chcete nasadit tyto prostředky.

Pomocí následujícího příkazu vytvořte skupinu `<resourceGroupName>` prostředků v oblasti. Nahraďte `<resourceGroupName>` název podle vašeho výběru. Používáme `East US 2` pro tento kurz. Další informace naleznete v následujícím programu [Rychlý start](../quick-create-cli.md).

```azurecli-interactive
az group create --name <resourceGroupName> --location eastus2
```

## <a name="create-an-availability-set"></a>Vytvoření sady dostupnosti

Dalším krokem je vytvoření skupiny dostupnosti. Spusťte následující příkaz v prostředí `<resourceGroupName>` Azure Cloud Shell a nahraďte název skupiny prostředků. Zvolte název `<availabilitySetName>`pro .

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

## <a name="create-rhel-vms-inside-the-availability-set"></a>Vytvoření virtuálních aplikací RHEL uvnitř sady dostupnosti

> [!WARNING]
> Pokud zvolíte bitovou kopii RHEL s průběžným platbou (PAYG) a nakonfigurujete vysokou dostupnost (HA), můžete být požádáni o registraci předplatného. To může způsobit, že zaplatíte dvakrát za předplatné, protože se vám bude účtovat předplatné Microsoft Azure RHEL pro virtuální počítač a předplatné Red Hatu. Další informace naleznete v tématu https://access.redhat.com/solutions/2458541.
>
> Abyste se vyhnuli "double fakturaci", použijte při vytváření virtuálního počítače Azure bitovou kopii RHEL HA. Obrázky nabízené jako obrázky RHEL-HA jsou také obrázky PAYG s přednastaveným repo ha.

1. Získejte seznam imitek virtuálního počítače, které nabízejí RHEL s HA:

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
            }
    ]
    ```

    Pro tento kurz vybíráme obrázek `RedHat:RHEL-HA:7.6:7.6.2019062019`.

    > [!IMPORTANT]
    > Chcete-li nastavit skupinu dostupnosti, musí být názvy počítačů menší než 15 znaků. Uživatelské jméno nesmí obsahovat velká písmena a hesla musí mít více než 12 znaků.

1. Chceme vytvořit 3 virtuální chody v sadě dostupnosti. V následujícím příkazu nahraďte následující:

    - `<resourceGroupName>`
    - `<VM-basename>`
    - `<availabilitySetName>`
    - `<VM-Size>`- Příkladem by bylo "Standard_D16_v3"
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

Výše uvedený příkaz vytvoří virtuální chod a vytvoří výchozí virtuální síť pro tyto virtuální aplikace. Další informace o různých konfiguracích najdete v článku [vytvoření az vm.](https://docs.microsoft.com/cli/azure/vm)

Po dokončení příkazu pro každý virtuální virtuální mě byste měli získat podobné výsledky následujícímu:

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
> Výchozí obraz, který je vytvořen pomocí výše uvedeného příkazu, vytvoří ve výchozím nastavení disk s 32 GB operačního systému. S touto výchozí instalací může dojít k nedostatku místa. Následující parametr přidaný do výše `az vm create` uvedeného příkazu můžete použít k vytvoření disku `--os-disk-size-gb 128`operačního systému s kapacitou 128 GB jako příklad: .
>
> Potom můžete [nakonfigurovat Správce logických svazků (LVM),](../../../virtual-machines/linux/configure-lvm.md) pokud potřebujete rozbalit příslušné svazky složek tak, aby vyhovovaly vaší instalaci.

### <a name="test-connection-to-the-created-vms"></a>Testování připojení k vytvořeným virtuálním sobě

Připojte se k Virtuálnímu počítači 1 nebo k jiným virtuálním počítačům pomocí následujícího příkazu v Prostředí Azure Cloud Shell. Pokud se vám nedaří najít IP adresy virtuálních připojení, postupujte podle tohoto [úvodního panelu v prostředí Azure Cloud Shell](../../../cloud-shell/quickstart.md#ssh-into-your-linux-vm).

```azurecli-interactive
ssh <username>@publicipaddress
```

Pokud je připojení úspěšné, měli byste vidět následující výstup představující terminál Linuxu:

```output
[<username>@<VM1> ~]$
```

Chcete-li opustit relaci SSH, zadejte. `exit`

## <a name="enable-high-availability"></a>Povolit vysokou dostupnost

> [!IMPORTANT]
> Chcete-li dokončit tuto část kurzu, musíte mít předplatné pro RHEL a doplněk s vysokou dostupností. Pokud používáte obrázek doporučený v předchozí části, není třeba registrovat jiné předplatné.
 
Připojte se ke každému uzlu virtuálního uživatele a postupujte podle níže uvedeného průvodce a povolte ha. Další informace naleznete v [tématu povolení předplatného s vysokou dostupností pro rhel](/sql/linux/sql-server-linux-availability-group-cluster-rhel#enable-the-high-availability-subscription-for-rhel).

> [!TIP]
> Bude to jednodušší, pokud otevřete relaci SSH pro každý z virtuálních počítačů současně jako stejné příkazy bude nutné spustit na každém virtuálním počítači v celém článku.
>
> Pokud kopírujete a `sudo` vložujete více příkazů a budete vyzváni k zadání hesla, další příkazy se nespustí. Spusťte každý příkaz samostatně.


1. Spusťte následující příkazy na každém virtuálním počítači a otevřete porty brány firewall Pacemakeru:

    ```bash
    sudo firewall-cmd --permanent --add-service=high-availability
    sudo firewall-cmd --reload
    ```

1. Aktualizujte a nainstalujte balíčky Pacemakeru do všech uzlů pomocí následujících příkazů:

    > [!NOTE]
    > **nmap** je nainstalován jako součást tohoto příkazového bloku jako nástroj pro vyhledání dostupných IP adres v síti. Nemusíte instalovat **nmap**, ale to bude užitečné později v tomto tutoriálu.

    ```bash
    sudo yum update -y
    sudo yum install -y pacemaker pcs fence-agents-all resource-agents fence-agents-azure-arm nmap
    sudo reboot
    ```

1. Nastavte heslo pro výchozího uživatele, který je vytvořen při instalaci balíčků Pacemaker. Použijte stejné heslo ve všech uzlech.

    ```bash
    sudo passwd hacluster
    ```

1. Pomocí následujícího příkazu otevřete soubor hosts a nastavte překlad názvů hostitele. Další informace naleznete v [tématu Configure AG](/sql/linux/sql-server-linux-availability-group-configure-ha#prerequisites) on configuring the hosts file.

    ```
    sudo vi /etc/hosts
    ```

    V editoru **vi** zadejte `i` pro vložení textu a na prázdný řádek přidejte **privátní IP adresu** odpovídajícího virtuálního počítači. Pak přidejte název virtuálního počítačů za mezeru vedle IP adresy. Každý řádek by měl mít samostatnou položku.

    ```output
    <IP1> <VM1>
    <IP2> <VM2>
    <IP3> <VM3>
    ```

    > [!IMPORTANT]
    > Doporučujeme použít vaši **privátní IP** adresu výše. Použití veřejné IP adresy v této konfiguraci způsobí selhání nastavení a nedoporučujeme vystavení virtuálního počítače externím sítím.

    Chcete-li ukončit editor **vi,** nejprve stiskněte klávesu **Esc** a potom zadejte příkaz `:wq` pro zápis souboru a ukončete.

## <a name="create-the-pacemaker-cluster"></a>Vytvoření clusteru Pacemaker

V této části povolíme a spustíme službu PCSD a pak nakonfigurujeme cluster. Pro SQL Server na Linuxu nejsou prostředky clusteru vytvořeny automaticky. Budeme muset povolit a vytvořit prostředky kardiostimulátoru ručně. Další informace naleznete v článku o [konfiguraci instance clusteru s podporou převzetí služeb při selhání pro RHEL](/sql/linux/sql-server-linux-shared-disk-cluster-red-hat-7-configure#install-and-configure-pacemaker-on-each-cluster-node)

### <a name="enable-and-start-pcsd-service-and-pacemaker"></a>Povolení a spuštění služby PCSD a kardiostimulátoru

1. Spusťte příkazy ve všech uzlech. Tyto příkazy umožňují uzlům znovu připojit clusteru po restartování.

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

1. Na primárním uzlu nastavte cluster následujícími příkazy.

    - Při spuštění `pcs cluster auth` příkazu k ověření uzlů clusteru budete vyzváni k zadání hesla. Zadejte heslo pro uživatele **hacluster** uvytvořeného dříve.

    ```bash
    sudo pcs cluster auth <VM1> <VM2> <VM3> -u hacluster
    sudo pcs cluster setup --name az-hacluster <VM1> <VM2> <VM3> --token 30000
    sudo pcs cluster start --all
    sudo pcs cluster enable --all
    ```

1. Spusťte následující příkaz a zkontrolujte, zda jsou všechny uzly online.

    ```bash
    sudo pcs status
    ```

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

1. Nastavte očekávané hlasy v živém clusteru na 3. Tento příkaz ovlivní pouze živý cluster a nezmění konfigurační soubory.

    Na všech uzlech nastavte očekávané hlasy pomocí následujícího příkazu:

    ```bash
    sudo pcs quorum expected-votes 3
    ```

## <a name="configure-the-fencing-agent"></a>Konfigurace agenta oplocení

Zařízení STONITH poskytuje šermířské činidlo. Níže uvedené pokyny jsou upraveny pro tento kurz. Další informace naleznete [v tématu vytvoření zařízení STONITH](../../../virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker.md#create-stonith-device).
 
[Zkontrolujte verzi agenta Azure Fence a ujistěte se, že je aktualizován](../../../virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker.md#cluster-installation). Použijte následující příkaz:

```bash
sudo yum info fence-agents-azure-arm
```

Podobný výstup byste měli vidět jako v níže uvedeném příkladu.

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

### <a name="register-a-new-application-in-azure-active-directory"></a>Registrace nové aplikace ve službě Azure Active Directory
 
 1. Přejděte na https://portal.azure.com.
 2. Otevřete [okno Služby Azure Active Directory](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties). Přejděte na vlastnosti a poznamenejte si ID adresáře. Jedná se o`tenant ID`
 3. Klikněte na [ **Registrace aplikací.**](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)
 4. Klikněte na **Nová registrace.**
 5. Zadejte **Name** název `<resourceGroupName>-app`jako , vyberte **pouze v tomto adresáři organizace položku Účty.**
 6. Vyberte **Web**typu aplikace , zadejte http://localhost) přihlašovací adresu URL (například a klepněte na Přidat. Přihlašovací adresa URL se nepoužívá a může se jedná o libovolnou platnou adresu URL. Po dokončení klikněte na **registr**
 7. Vyberte **certifikáty a tajné kódy** pro novou registraci aplikace a klikněte na **Nový tajný klíč klienta.**
 8. Zadejte popis nového klíče (tajný klíč klienta), vyberte **Nikdy nevyprší** a klikněte na **Přidat**
 9. Zapište si hodnotu tajného klíče. Používá se jako heslo pro instanční objekt
10. Vyberte **Přehled**. Poznamenejte si ID aplikace. Používá se jako uživatelské jméno (login ID v níže uvedených krocích) instančního objektu
 
### <a name="create-a-custom-role-for-the-fence-agent"></a>Vytvoření vlastní role agenta plotu

Podle kurzu [vytvořte vlastní roli pro prostředky Azure pomocí Azure CLI](../../../role-based-access-control/tutorial-custom-role-cli.md#create-a-custom-role).

Soubor json by měl vypadat podobně jako následující:

- Nahraďte `<username>` název podle vašeho výběru. To to je, aby se zabránilo jakékoli duplicitě při vytváření této definice role.
- Nahraďte `<subscriptionId>` id předplatného Azure.

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

Chcete-li roli přidat, spusťte následující příkaz:

- Nahraďte `<filename>` název souboru.
- Pokud provádíte příkaz z jiné cesty, než je složka, do které je soubor uložen, zahrňte do příkazu cestu ke složce souboru.

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

### <a name="assign-the-custom-role-to-the-service-principal"></a>Přiřazení vlastní role instančnímu objektu

Přiřaďte `Linux Fence Agent Role-<username>` vlastní roli, která byla vytvořena v posledním kroku instančního objektu. Už nepoužívejte roli vlastníka!
 
1. Přejděte na https://portal.azure.com.
2. Otevření [okna Všechny prostředky](https://ms.portal.azure.com/#blade/HubsExtension/BrowseAll)
3. Výběr virtuálního počítače prvního uzlu clusteru
4. Klikněte na **ovládací prvek přístupu (IAM)**
5. Klikněte **na Přidat přiřazení role.**
6. Výběr role `Linux Fence Agent Role-<username>` ze seznamu **rolí**
7. Do seznamu **Vybrat** zadejte název aplikace, kterou jste vytvořili výše,`<resourceGroupName>-app`
8. Klikněte na **Uložit.**
9. Opakujte výše uvedené kroky pro všechny uzla clusteru.

### <a name="create-the-stonith-devices"></a>Vytvoření zařízení STONITH

Spusťte následující příkazy na uzlu 1:

- Nahraďte hodnotu `<ApplicationID>` ID z registrace aplikace.
- Nahraďte hodnotu `<servicePrincipalPassword>` z tajného klíče klienta.
- Nahraďte skupinu `<resourceGroupName>` prostředků z vašeho předplatného použitého pro tento kurz.
- Nahraďte `<tenantID>` `<subscriptionId>` a z předplatného Azure.

```bash
sudo pcs property set stonith-timeout=900
sudo pcs stonith create rsc_st_azure fence_azure_arm login="<ApplicationID>" passwd="<servicePrincipalPassword>" resourceGroup="<resourceGroupName>" tenantId="<tenantID>" subscriptionId="<subscriptionId>" power_timeout=240 pcmk_reboot_timeout=900
```

Vzhledem k tomu, že jsme již přidali pravidlo do našeho firewallu, aby služba HA (`--add-service=high-availability`), není třeba otevírat následující porty firewallu na všech uzlech: 2224, 3121, 21064, 5405. Pokud však dochází k jakémukoli typu problémů s připojením s HA, otevřete tyto porty, které jsou přidruženy k ha, pomocí následujícího příkazu.

> [!TIP]
> Volitelně můžete přidat všechny porty v tomto kurzu najednou ušetřit nějaký čas. Porty, které je třeba otevřít, jsou vysvětleny v jejich relativních částech níže. Pokud chcete nyní přidat všechny porty, přidejte další porty: 1433 a 5022.

```bash
sudo firewall-cmd --zone=public --add-port=2224/tcp --add-port=3121/tcp --add-port=21064/tcp --add-port=5405/tcp --permanent
sudo firewall-cmd --reload
```

## <a name="install-sql-server-and-mssql-tools"></a>Instalace sql serveru a nástrojů mssql
 
Pomocí následující části nainstalujte SQL Server a mssql-tools na virtuálních počítačích. Proveďte každou z těchto akcí na všech uzlech. Další informace naleznete v [tématu instalace SQL Server a Red Hat VM](/sql/linux/quickstart-install-connect-red-hat).

### <a name="installing-sql-server-on-the-vms"></a>Instalace SQL Serveru na virtuální chod

K instalaci serveru SQL Server se používají následující příkazy:

```bash
sudo curl -o /etc/yum.repos.d/mssql-server.repo https://packages.microsoft.com/config/rhel/7/mssql-server-2017.repo
sudo yum install -y mssql-server
sudo /opt/mssql/bin/mssql-conf setup
sudo yum install mssql-server-ha
```

### <a name="open-firewall-port-1433-for-remote-connections"></a>Otevření portu brány firewall 1433 pro vzdálená připojení

Budete muset otevřít port 1433 na virtuálním počítači, aby se vzdáleně připojit. Pomocí následujících příkazů otevřete port 1433 v bráně firewall každého virtuálního počítače:

```bash
sudo firewall-cmd --zone=public --add-port=1433/tcp --permanent
sudo firewall-cmd --reload
```

### <a name="installing-sql-server-command-line-tools"></a>Instalace nástrojů příkazového řádku serveru SQL Server

Následující příkazy slouží k instalaci nástrojů příkazového řádku serveru SQL Server. Další informace naleznete [v tématu instalace nástrojů příkazového řádku serveru SQL Server](/sql/linux/quickstart-install-connect-red-hat#tools).

```bash
sudo curl -o /etc/yum.repos.d/msprod.repo https://packages.microsoft.com/config/rhel/7/prod.repo
sudo yum install -y mssql-tools unixODBC-devel
```
 
> [!NOTE] 
> Pro větší pohodlí přidejte /opt/mssql-tools/bin/ do proměnné prostředí PATH. To umožňuje spustit nástroje bez zadání úplné cesty. Spuštěním následujících příkazů upravte PATH pro relace přihlášení i interaktivní relace a relace bez přihlášení:</br></br>
`echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bash_profile`</br>
`echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bashrc`</br>
`source ~/.bashrc`


### <a name="check-the-status-of-the-sql-server"></a>Kontrola stavu serveru SQL Server

Po dokončení konfigurace můžete zkontrolovat stav serveru SQL Server a ověřit, zda je spuštěn:

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

## <a name="configure-sql-server-always-on-availability-group"></a>Konfigurace serveru SQL Server always on Availability Group

Pomocí následujících kroků nakonfigurujte SQL Server always on Availability Group pro vaše virtuální počítače. Další informace naleznete v [tématu konfigurace SQL Server always on Availability Group pro vysokou dostupnost na Linuxu](/sql/linux/sql-server-linux-availability-group-configure-ha)

### <a name="enable-alwayson-availability-groups-and-restart-mssql-server"></a>Povolit skupiny dostupnosti AlwaysOn a restartovat mssql-server

Povolit skupiny dostupnosti AlwaysOn na každém uzlu, který hostuje instanci serveru SQL Server. Potom restartujte mssql-server. Spusťte tento skript:

```
sudo /opt/mssql/bin/mssql-conf set hadr.hadrenabled 1
sudo systemctl restart mssql-server
```

### <a name="create-a-certificate"></a>Vytvoření certifikátu

V současné době nepodporujeme ověřování služby AD pro koncový bod AG. Proto musíme použít certifikát pro šifrování koncového bodu AG.

1. Připojte se ke **všem uzlům** pomocí sql server management studio (SSMS) nebo SQL CMD. Spusťte následující příkazy, které povolí AlwaysOn_health relaci a vytvoří hlavní klíč:

    > [!IMPORTANT]
    > Pokud se připojujete vzdáleně k instanci serveru SQL Server, budete muset mít port 1433 otevřený na bráně firewall. Budete také muset povolit příchozí připojení k portu 1433 ve vašem nsg pro každý virtuální počítač. Další informace naleznete [v tématu Vytvoření pravidla zabezpečení](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) pro vytvoření pravidla příchozího zabezpečení.

    - `<Master_Key_Password>` Nahraďte jej vlastním heslem.


    ```sql
    ALTER EVENT SESSION  AlwaysOn_health ON SERVER WITH (STARTUP_STATE=ON);
    GO
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<Master_Key_Password>';
    ```

 
1. Připojte se k primární replice pomocí SSMS nebo SQL CMD. Níže uvedené příkazy vytvoří `/var/opt/mssql/data/dbm_certificate.cer` certifikát na a `var/opt/mssql/data/dbm_certificate.pvk` soukromý klíč na primární repliku serveru SQL Server:

    - `<Private_Key_Password>` Nahraďte jej vlastním heslem.

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

Ukončete relaci SQL `exit` CMD spuštěním příkazu a vraťte se zpět do relace SSH.
 
### <a name="copy-the-certificate-to-the-secondary-replicas-and-create-the-certificates-on-the-server"></a>Zkopírujte certifikát do sekundárních replik a vytvořte certifikáty na serveru

1. Zkopírujte dva soubory, které byly vytvořeny do stejného umístění na všech serverech, které budou hostitelem replik dostupnosti.
 
    Na primárním serveru zkopírujte certifikát na cílových serverech následujícím `scp` příkazem:

    - Nahraďte `<username>` a `<VM2>` s uživatelským jménem a cílovým názvem virtuálního uživatele, které používáte.
    - Spusťte tento příkaz pro všechny sekundární repliky.

    > [!NOTE]
    > Není třeba spouštět `sudo -i`, což vám dává kořenové prostředí. Můžete jen spustit `sudo` příkaz před každým příkazem, jak jsme to dříve udělali v tomto kurzu.

    ```bash
    # The below command allows you to run commands in the root environment
    sudo -i
    ```

    ```bash
    scp /var/opt/mssql/data/dbm_certificate.* <username>@<VM2>:/home/<username>
    ```

1. Na cílovém serveru spusťte následující příkaz:

    - Nahraďte `<username>` svým uživatelským jménem.
    - Příkaz `mv` přesune soubory nebo adresář e- z jednoho místa na druhé.
    - Příkaz `chown` se používá ke změně vlastníka a skupiny souborů, adresářů nebo odkazů.
    - Spusťte tyto příkazy pro všechny sekundární repliky.

    ```bash
    sudo -i
    mv /home/<username>/dbm_certificate.* /var/opt/mssql/data/
    cd /var/opt/mssql/data
    chown mssql:mssql dbm_certificate.*
    ```

1. Následující skript Transact-SQL vytvoří certifikát ze zálohy, kterou jste vytvořili na primární replice serveru SQL Server. Aktualizujte skript silnými hesly. Heslo pro dešifrování je stejné heslo, které jste použili k vytvoření souboru PVK v předchozím kroku. Chcete-li vytvořit certifikát, spusťte následující skript pomocí SQL CMD nebo SSMS na všech sekundárních serverech:

    ```sql
    CREATE CERTIFICATE dbm_certificate
        FROM FILE = '/var/opt/mssql/data/dbm_certificate.cer'
        WITH PRIVATE KEY (
        FILE = '/var/opt/mssql/data/dbm_certificate.pvk',
        DECRYPTION BY PASSWORD = '<Private_Key_Password>'
                );
    GO
    ```

### <a name="create-the-database-mirroring-endpoints-on-all-replicas"></a>Vytvoření koncových bodů zrcadlení databáze na všech replikách

Spusťte následující skript na všech instancích SQL pomocí SQL CMD nebo SSMS:

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

Připojte se k instanci serveru SQL Server, která je hostitelem primární repliky pomocí SQL CMD nebo SSMS. Chcete-li vytvořit skupinu dostupnosti, spusťte následující příkaz:

- Nahraďte `ag1` požadovaným názvem skupiny dostupnosti.
- `<VM1>`Nahraďte `<VM2>`hodnoty `<VM3>` , a názvy instancí serveru SQL Server, které jsou hostiteli replik.

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

### <a name="create-a-sql-server-login-for-pacemaker"></a>Vytvoření přihlášení sql serveru pro kardiostimulátor

Na všech sql serverech vytvořte sql login pro Pacemaker. Následující Transact-SQL vytvoří přihlášení.

- Nahraďte `<password>` svým vlastním složitým heslem.

```sql
USE [master]
GO

CREATE LOGIN [pacemakerLogin] with PASSWORD= N'<password>';
GO

ALTER SERVER ROLE [sysadmin] ADD MEMBER [pacemakerLogin];
GO
```

Na všech serverech SQL uložte pověření použitá pro přihlášení serveru SQL Server. 

1. Vytvořte soubor:

    ```bash
    sudo vi /var/opt/mssql/secrets/passwd
    ```

1. Do souboru přidejte následující 2 řádky:

    ```bash
    pacemakerLogin
    <password>
    ```

    Chcete-li ukončit editor **vi,** nejprve stiskněte klávesu **Esc** a potom zadejte příkaz `:wq` pro zápis souboru a ukončete.

1. Aby byl soubor čitelný pouze podle kořenového adresáře:

    ```bash
    sudo chown root:root /var/opt/mssql/secrets/passwd
    sudo chmod 400 /var/opt/mssql/secrets/passwd
    ```

### <a name="join-secondary-replicas-to-the-availability-group"></a>Připojení sekundárních replik ke skupině dostupnosti

1. Chcete-li připojit sekundární repliky k AG, budete muset otevřít port 5022 na firewallu pro všechny servery. V relaci SSH spusťte následující příkaz:

    ```bash
    sudo firewall-cmd --zone=public --add-port=5022/tcp --permanent
    sudo firewall-cmd --reload
    ```

1. Na sekundárních replikách spusťte následující příkazy, abyste je připojili k ag:

    ```sql
    ALTER AVAILABILITY GROUP [ag1] JOIN WITH (CLUSTER_TYPE = EXTERNAL);
    GO

    ALTER AVAILABILITY GROUP [ag1] GRANT CREATE ANY DATABASE;
    GO
    ```

1. Spusťte následující skript Transact-SQL na primární replice a každé sekundární repliky:

    ```sql
    GRANT ALTER, CONTROL, VIEW DEFINITION ON AVAILABILITY GROUP::ag1 TO pacemakerLogin;
    GO
    
    GRANT VIEW SERVER STATE TO pacemakerLogin;
    GO
    ```

1. Jakmile jsou sekundární repliky spojeny, můžete je zobrazit v Průzkumníku objektů SSMS rozbalením uzlu **Vždy na vysoké dostupnosti:**

    ![availability-group-joined.png](media/sql-server-linux-rhel-ha-stonith-tutorial/availability-group-joined.png)

### <a name="add-a-database-to-the-availability-group"></a>Přidání databáze do skupiny dostupnosti

Budeme postupovat [podle článku skupiny dostupnosti konfigurace o přidání databáze](/sql/linux/sql-server-linux-availability-group-configure-ha#add-a-database-to-the-availability-group).

V tomto kroku se používají následující příkazy Transact-SQL. Spusťte tyto příkazy na primární replice:

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

### <a name="verify-that-the-database-is-created-on-the-secondary-servers"></a>Ověření vytvoření databáze na sekundárních serverech

Na každé sekundární replice serveru SQL Server spusťte následující dotaz a zjistěte, zda byla databáze DB1 vytvořena a zda je ve stavu SYNCHRONIZED:

```
SELECT * FROM sys.databases WHERE name = 'db1';
GO
SELECT DB_NAME(database_id) AS 'database', synchronization_state_desc FROM sys.dm_hadr_database_replica_states;
```

Pokud `synchronization_state_desc` je seznam `db1`synchronizován pro , znamená to, že repliky jsou synchronizovány. Sekundární jsou zobrazeny `db1` v primární replice.

## <a name="create-availability-group-resources-in-the-pacemaker-cluster"></a>Vytvoření prostředků skupiny dostupnosti v clusteru Pacemaker

Budeme se řídit průvodcem pro [vytvoření prostředků skupiny dostupnosti v clusteru Pacemaker](/sql/linux/sql-server-linux-create-availability-group#create-the-availability-group-resources-in-the-pacemaker-cluster-external-only).

### <a name="create-the-ag-cluster-resource"></a>Vytvoření prostředku clusteru AG

1. Pomocí následujícího příkazu `ag_cluster` vytvořte prostředek `ag1`ve skupině dostupnosti .

    ```bash
    sudo pcs resource create ag_cluster ocf:mssql:ag ag_name=ag1 meta failure-timeout=30s master notify=true
    ```

1. Zkontrolujte zdroj a ujistěte se, že jsou online, než budete pokračovat pomocí následujícího příkazu:

    ```bash
    sudo pcs resource
    ```

    Měl by se zobrazit následující výstup:

    ```output
    [<username>@VM1 ~]$ sudo pcs resource
    Master/Slave Set: ag_cluster-master [ag_cluster]
    Masters: [ <VM1> ]
    Slaves: [ <VM2> <VM3> ]
    ```

### <a name="create-a-virtual-ip-resource"></a>Vytvoření virtuálního prostředku IP

1. K vytvoření virtuálního prostředku IP použijte dostupnou statickou adresu IP ze sítě. Můžete najít pomocí příkazového `nmap`nástroje .

    ```bash
    nmap -sP <IPRange>
    # For example: nmap -sP 10.0.0.*
    # The above will scan for all IP addresses that are already occupied in the 10.0.0.x space.
    ```

1. Nastavte vlastnost **s povolenou stoništem** na hodnotu false.

    ```bash
    sudo pcs property set stonith-enabled=false
    ```

1. Vytvořte prostředek virtuální IP pomocí následujícího příkazu:

    - Nahraďte níže uvedenou `<availableIP>` hodnotu nepoužitou adresou IP.

    ```bash
    sudo pcs resource create virtualip ocf:heartbeat:IPaddr2 ip=<availableIP>
    ```

### <a name="add-constraints"></a>Přidat omezení

1. Chcete-li zajistit, aby adresa IP a prostředek AG byly spuštěny na stejném uzlu, musí být nakonfigurováno omezení společného umístění. Spusťte následující příkaz:

    ```bash
    sudo pcs constraint colocation add virtualip ag_cluster-master INFINITY with-rsc-role=Master
    ```

1. Vytvořte omezení řazení, abyste zajistili, že prostředek AG je spuštěn před adresou IP. Zatímco omezení společného umístění znamená omezení řazení, to to vynucuje.

    ```bash
    sudo pcs constraint order promote ag_cluster-master then start virtualip
    ```

1. Chcete-li ověřit omezení, spusťte následující příkaz:

    ```bash
    sudo pcs constraint list --full
    ```

    Měl by se zobrazit následující výstup:

    ```
    Location Constraints:
    Ordering Constraints:
          promote ag_cluster-master then start virtualip (kind:Mandatory) (id:order-ag_cluster-master-virtualip-mandatory)
    Colocation Constraints:
          virtualip with ag_cluster-master (score:INFINITY) (with-rsc-role:Master) (id:colocation-virtualip-ag_cluster-master-INFINITY)
    Ticket Constraints:
    ```

### <a name="re-enable-stonith"></a>Znovu povolit stonith

Jsme připraveni na testování. Znovu povolte stonitth v clusteru spuštěním následujícího příkazu na uzlu 1:

```bash
sudo pcs property set stonith-enabled=true
```

### <a name="check-cluster-status"></a>Kontrola stavu clusteru

Stav prostředků clusteru můžete zkontrolovat pomocí následujícího příkazu:

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

Abychom zajistili, že konfigurace byla dosud úspěšná, otestujeme převzetí služeb při selhání. Další informace naleznete [v tématu Always On Availability Group převzetí služeb při selhání na Linuxu](/sql/linux/sql-server-linux-availability-group-failover-ha).

1. Spusťte následující příkaz a ručně přepojujte službu převzetí služeb při selhání primární repliky do . `<VM2>` Nahraďte `<VM2>` hodnotou názvu serveru.

    ```bash
    sudo pcs resource move ag_cluster-master <VM2> --master
    ```

1. Pokud znovu zkontrolujete omezení, uvidíte, že z důvodu ručního převzetí služeb při selhání bylo přidáno další omezení:

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

1. Odstraňte omezení s `cli-prefer-ag_cluster-master` ID pomocí následujícího příkazu:

    ```bash
    sudo pcs constraint remove cli-prefer-ag_cluster-master
    ```

1. Zkontrolujte prostředky clusteru `sudo pcs resource`pomocí příkazu a měli byste `<VM2>`vidět, že primární instance je nyní .

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

## <a name="test-fencing"></a>Testovací oplocení

Stonith můžete otestovat spuštěním následujícího příkazu. Zkuste spustit níže `<VM1>` příkaz `<VM3>`z pro .

```bash
sudo pcs stonith fence <VM3> --debug
```

> [!NOTE]
> Ve výchozím nastavení akce plotu přináší uzel vypnout a pak na. Pokud chcete uzel pouze převést do funkce, `--off` použijte možnost v příkazu.

Měli byste získat následující výstup:

```output
[<username>@<VM1> ~]$ sudo pcs stonith fence <VM3> --debug
Running: stonith_admin -B <VM3>
Return Value: 0
--Debug Output Start--
--Debug Output End--
 
Node: <VM3> fenced
```
Další informace o testování plotového zařízení naleznete v následujícím článku [red hat.](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/s1-stonithtest-haar)

## <a name="next-steps"></a>Další kroky

Chcete-li využít naslouchací proces skupiny dostupnosti pro servery SQL, budete muset vytvořit a nakonfigurovat nástroje pro vyrovnávání zatížení.

> [!div class="nextstepaction"]
> [Kurz: Konfigurace naslouchací proces skupiny dostupnosti pro SQL Server na virtuálních počítačích RHEL v Azure](sql-server-linux-rhel-ha-listener-tutorial.md)
