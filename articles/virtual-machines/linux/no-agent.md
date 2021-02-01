---
title: Vytváření imagí Linux bez agenta zřizování
description: Vytvářejte generalizované image pro Linux bez zřizovacího agenta v Azure.
author: danielsollondon
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 09/01/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: 1c9ac872587804adbd9e62a3dc3ef3daed9e0c25
ms.sourcegitcommit: 8c8c71a38b6ab2e8622698d4df60cb8a77aa9685
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2021
ms.locfileid: "99223047"
---
# <a name="creating-generalized-images-without-a-provisioning-agent"></a>Vytváření zobecněných imagí bez zřizovacího agenta

Microsoft Azure poskytuje agenty zřizování pro virtuální počítače se systémem Linux ve formě [walinuxagent](https://github.com/Azure/WALinuxAgent) nebo [Cloud-init](https://github.com/canonical/cloud-init) (doporučeno). Nicméně může nastat situace, kdy nechcete použít některou z těchto aplikací pro vašeho agenta zřizování, například:

- Distribuce/Version pro Linux nepodporuje agenta Cloud-init/Linux.
- Vyžadujete, aby byly nastavené konkrétní vlastnosti virtuálního počítače, třeba název hostitele.

> [!NOTE] 
>
> Pokud nepotřebujete, aby se nastavily žádné vlastnosti nebo žádná forma zřizování, měli byste zvážit vytvoření specializované image.

V tomto článku se dozvíte, jak můžete nastavit image virtuálního počítače tak, aby splňovala požadavky na platformu Azure a aby se název hostitele nastavil, aniž by se nainstaloval agent zřizování.

## <a name="networking-and-reporting-ready"></a>Příprava na sítě a vytváření sestav

Aby váš virtuální počítač se systémem Linux komunikoval se součástmi Azure, budete potřebovat klienta DHCP, aby načetl IP adresu hostitele z virtuální sítě a také rozlišení DNS a správu tras. Většina distribuce je dodávána s těmito nástroji předem. K nástrojům, které byly testovány v Azure distribuce výrobci ze systému Linux `dhclient` , patří, `network-manager` `systemd-networkd` a další.

> [!NOTE]
>
> Aktuálně se vytváří generalizované image bez zřizovacího agenta, který podporuje jenom virtuální počítače s podporou protokolu DHCP.

Po nastavení a konfiguraci sítě musí být sestava připravena. Tím se dozvíte, že Azure úspěšně zřídil virtuální počítač.

> [!IMPORTANT]
>
> Neúspěšná příprava sestavy do Azure způsobí, že se váš virtuální počítač restartuje.

## <a name="demosample"></a>Ukázka/ukázka

V této ukázce se dozvíte, jak můžete získat existující image na webu Marketplace (v tomto případě virtuální počítač s Debian Buster) a odebrat agenta pro Linux (walinuxagent), ale také vytvořit nejvíce základní proces pro hlášení do Azure, že je virtuální počítač připravený.

### <a name="create-the-resource-group-and-base-vm"></a>Vytvořte skupinu prostředků a základní virtuální počítač:

```bash
$ az group create --location eastus --name demo1
```

Vytvořte základní virtuální počítač:

```bash
$ az vm create \
    --resource-group demo1 \
    --name demo1 \
    --location eastus \
    --ssh-key-value <ssh_pub_key_path> \
    --public-ip-address-dns-name demo1 \
    --image "debian:debian-10:10:latest"
```

### <a name="remove-the-image-provisioning-agent"></a>Odebrání agenta pro zřizování imagí

Jakmile se virtuální počítač zřídí, můžete k němu přihlédnout a odebrat agenta pro Linux:

```bash
$ sudo apt purge -y waagent
$ sudo rm -rf /var/lib/waagent /etc/waagent.conf /var/log/waagent.log
```

### <a name="add-required-code-to-the-vm"></a>Přidat do virtuálního počítače požadovaný kód

I uvnitř virtuálního počítače, protože jsme odebrali agenta Azure Linux, musíme vytvořit mechanismus pro přípravu sestavy. 

#### <a name="python-script"></a>Skript jazyka Python

```python
import http.client
import sys
from xml.etree import ElementTree

wireserver_ip = '168.63.129.16'
wireserver_conn = http.client.HTTPConnection(wireserver_ip)

print('Retrieving goal state from the Wireserver')
wireserver_conn.request(
    'GET',
    '/machine?comp=goalstate',
    headers={'x-ms-version': '2012-11-30'}
)

resp = wireserver_conn.getresponse()

if resp.status != 200:
    print('Unable to connect with wireserver')
    sys.exit(1)

wireserver_goalstate = resp.read().decode('utf-8')

xml_el = ElementTree.fromstring(wireserver_goalstate)

container_id = xml_el.findtext('Container/ContainerId')
instance_id = xml_el.findtext('Container/RoleInstanceList/RoleInstance/InstanceId')
print(f'ContainerId: {container_id}')
print(f'InstanceId: {instance_id}')

# Construct the XML response we need to send to Wireserver to report ready.
health = ElementTree.Element('Health')
goalstate_incarnation = ElementTree.SubElement(health, 'GoalStateIncarnation')
goalstate_incarnation.text = '1'
container = ElementTree.SubElement(health, 'Container')
container_id_el = ElementTree.SubElement(container, 'ContainerId')
container_id_el.text = container_id
role_instance_list = ElementTree.SubElement(container, 'RoleInstanceList')
role = ElementTree.SubElement(role_instance_list, 'Role')
instance_id_el = ElementTree.SubElement(role, 'InstanceId')
instance_id_el.text = instance_id
health_second = ElementTree.SubElement(role, 'Health')
state = ElementTree.SubElement(health_second, 'State')
state.text = 'Ready'

out_xml = ElementTree.tostring(
    health,
    encoding='unicode',
    method='xml'
)
print('Sending the following data to Wireserver:')
print(out_xml)

wireserver_conn.request(
    'POST',
    '/machine?comp=health',
    headers={
        'x-ms-version': '2012-11-30',
        'Content-Type': 'text/xml;charset=utf-8',
        'x-ms-agent-name': 'custom-provisioning'
    },
    body=out_xml
)

resp = wireserver_conn.getresponse()
print(f'Response: {resp.status} {resp.reason}')

wireserver_conn.close()
```

#### <a name="generic-steps-without-using-python"></a>Obecné kroky (bez použití Pythonu)

Pokud na vašem VIRTUÁLNÍm počítači není nainstalovaný Python nebo je k dispozici, můžete tuto logiku skriptu prostřednictvím kódu programu znovu vyvolat pomocí následujících kroků:

1. Načte `ContainerId` a `InstanceId` analyzuje odpověď z WireServer: `curl -X GET -H 'x-ms-version: 2012-11-30' http://168.63.129.16/machine?comp=goalstate` .

2. Sestavte následující data XML vložením analyzovaných `ContainerId` a `InstanceId` z výše uvedeného kroku:
   ```xml
   <Health>
     <GoalStateIncarnation>1</GoalStateIncarnation>
     <Container>
       <ContainerId>CONTAINER_ID</ContainerId>
       <RoleInstanceList>
         <Role>
           <InstanceId>INSTANCE_ID</InstanceId>
           <Health>
             <State>Ready</State>
           </Health>
         </Role>
       </RoleInstanceList>
     </Container>
   </Health>
   ```

3. Publikujte tato data do WireServer: `curl -X POST -H 'x-ms-version: 2012-11-30' -H "x-ms-agent-name: WALinuxAgent" -H "Content-Type: text/xml;charset=utf-8" -d "$REPORT_READY_XML" http://168.63.129.16/machine?comp=health`

### <a name="automating-running-the-code-at-first-boot"></a>Automatizace spouštění kódu při prvním spuštění

Tato ukázka používá systém, který je nejběžnějším systémem init v moderních distribuce systému Linux. Proto nejjednodušší a nejtivněný způsob, jak zajistit, aby tato sestava připravená na sestavu běžela ve správný čas, je vytvoření jednotky systémové služby. Do `/etc/systemd/system` (příklad názvu souboru jednotky) můžete přidat následující soubor jednotek `azure-provisioning.service` :

```bash
[Unit]
Description=Azure Provisioning

[Service]
Type=oneshot
ExecStart=/usr/bin/python3 /usr/local/azure-provisioning.py
ExecStart=/bin/bash -c "hostnamectl set-hostname $(curl \
    -H 'metadata: true' \
    'http://169.254.169.254/metadata/instance/compute/name?api-version=2019-06-01&format=text')"
ExecStart=/usr/bin/systemctl disable azure-provisioning.service

[Install]
WantedBy=multi-user.target
```

Tato služba systému poskytuje tři věci pro základní zřizování:

1. Sestavy připravené k Azure (k indikaci úspěšného úspěšného dokončení)
1. Přejmenuje virtuální počítač na základě názvu virtuálního počítače zadaného uživatelem načtením těchto dat z [Azure instance metadata Service (IMDS)](./instance-metadata-service.md). **Poznámka:** IMDS také poskytuje další [metadata instance](./instance-metadata-service.md#access-azure-instance-metadata-service), jako jsou například veřejné klíče SSH, takže můžete nastavit více než název hostitele.
1. Zakáže sám sebe, aby se spouštěla jenom při prvním spuštění, a ne při následném restartování.

S jednotkou v systému souborů spusťte následující příkaz pro povolení:

```bash
$ sudo systemctl enable azure-provisioning.service
```

Virtuální počítač je teď připravený k zobecnění a z něho se vytvořila jeho image. 

#### <a name="completing-the-preparation-of-the-image"></a>Dokončuje se příprava image.

Zpátky na svém vývojovém počítači spusťte následující příkaz a připravte se na vytvoření image ze základního virtuálního počítače:

```bash
$ az vm deallocate --resource-group demo1 --name demo1
$ az vm generalize --resource-group demo1 --name demo1
```

A vytvořte image z tohoto virtuálního počítače:

```bash
$ az image create \
    --resource-group demo1 \
    --source demo1 \
    --location eastus \
    --name demo1img
```

Teď jsme připraveni vytvořit nový virtuální počítač (nebo několik virtuálních počítačů) z Image:

```bash
$ IMAGE_ID=$(az image show -g demo1 -n demo1img --query id -o tsv)
$ az vm create \
    --resource-group demo12 \
    --name demo12 \
    --location eastus \
    --ssh-key-value <ssh_pub_key_path> \
    --public-ip-address-dns-name demo12 \
    --image "$IMAGE_ID" 
    --enable-agent false
```

> [!NOTE]
>
> Pro `--enable-agent` `false` Tento virtuální počítač, který se má vytvořit z image, je důležité nastavit na, protože walinuxagent neexistuje.

Tento virtuální počítač by se měl úspěšně zřídit. Přihlášení k nově zřizovacímu virtuálnímu počítači byste měli být schopni zobrazit výstup služby, která je připravená pro sestavy:

```bash
$ sudo journalctl -u azure-provisioning.service
-- Logs begin at Thu 2020-06-11 20:28:45 UTC, end at Thu 2020-06-11 20:31:24 UTC. --
Jun 11 20:28:49 thstringnopa systemd[1]: Starting Azure Provisioning...
Jun 11 20:28:54 thstringnopa python3[320]: Retrieving goal state from the Wireserver
Jun 11 20:28:54 thstringnopa python3[320]: ContainerId: 7b324f53-983a-43bc-b919-1775d6077608
Jun 11 20:28:54 thstringnopa python3[320]: InstanceId: fbb84507-46cd-4f4e-bd78-a2edaa9d059b._thstringnopa2
Jun 11 20:28:54 thstringnopa python3[320]: Sending the following data to Wireserver:
Jun 11 20:28:54 thstringnopa python3[320]: <Health><GoalStateIncarnation>1</GoalStateIncarnation><Container><ContainerId>7b324f53-983a-43bc-b919-1775d6077608</ContainerId><RoleInstanceList><Role><InstanceId>fbb84507-46cd-4f4e-bd78-a2edaa9d059b._thstringnopa2</InstanceId><Health><State>Ready</State></Health></Role></RoleInstanceList></Container></Health>
Jun 11 20:28:54 thstringnopa python3[320]: Response: 200 OK
Jun 11 20:28:56 thstringnopa bash[472]:   % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
Jun 11 20:28:56 thstringnopa bash[472]:                                  Dload  Upload   Total   Spent    Left  Speed
Jun 11 20:28:56 thstringnopa bash[472]: [158B blob data]
Jun 11 20:28:56 thstringnopa2 systemctl[475]: Removed /etc/systemd/system/multi-user.target.wants/azure-provisioning.service.
Jun 11 20:28:56 thstringnopa2 systemd[1]: azure-provisioning.service: Succeeded.
Jun 11 20:28:56 thstringnopa2 systemd[1]: Started Azure Provisioning.
```

## <a name="support"></a>Podpora

Pokud implementujete vlastní zřizovací kód nebo agenta, budete si moci vlastní podporu tohoto kódu. Podpora Microsoftu bude zkoumat pouze problémy související s rozhraními zřizování, které nejsou k dispozici. V této oblasti průběžně probíhá vylepšení a změny, takže musíte monitorovat změny v modulu Cloud-init a Azure Linux agent pro zřizování změn rozhraní API.

## <a name="next-steps"></a>Další kroky

Další informace najdete v tématu [zřizování Linux](provisioning.md).
