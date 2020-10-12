---
title: Řešení potíží s nasazením OpenShift Container Platform 3,11 v Azure
description: Řešení potíží s nasazením OpenShift Container Platform 3,11 v Azure
author: haroldwongms
manager: mdotson
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.custom: devx-track-ansible
ms.openlocfilehash: 9595627e9d7ca2de577aa83ebba3dd58d69e6750
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87373551"
---
# <a name="troubleshoot-openshift-container-platform-311-deployment-in-azure"></a>Řešení potíží s nasazením OpenShift Container Platform 3,11 v Azure

Pokud se OpenShift cluster neúspěšně nasadí, bude se v Azure Portal zaslat chybový výstup. Výstup může být obtížně čitelný, což ztěžuje identifikaci problému. Tento výstup můžete rychle vyhledat pro ukončovací kód 3, 4 nebo 5. Následující informace obsahují informace o těchto třech ukončovacích kódech:

- Ukončovací kód 3: uživatelské jméno/heslo předplatného Red Hat nebo ID nebo aktivační klíč organizace nejsou správné.
- Ukončovací kód 4: vaše ID fondu Red Hat je nesprávné nebo nejsou k dispozici žádná oprávnění.
- Ukončovací kód 5: nelze zřídit svazek Docker tenkých fondů

Pro všechny ostatní ukončovací kódy se připojte k hostitelům přes protokol SSH, aby se zobrazily soubory protokolu.

**OpenShift Container Platform 3.11**

SSH pro hostitele Ansible PlayBook. Pro šablonu nebo nabídku Marketplace použijte hostitele bastionu. Z bastionu můžete SSH pro všechny ostatní uzly v clusteru (hlavní, infračervená, CNS, COMPUTE). Aby bylo možné zobrazit soubory protokolu, bude nutné, abyste byli kořen. Kořen je ve výchozím nastavení zakázán pro přístup SSH, takže nelze použít root pro SSH pro jiné uzly.

**OKD**

SSH pro hostitele Ansible PlayBook. Pro šablonu OKD (verze 3,9 a starší) použijte hostitele hlavní-0. Pro šablonu OKD (verze 3,10 a novější) použijte hostitele bastionu. Z hostitele Ansible PlayBook můžete SSH pro všechny ostatní uzly v clusteru (hlavní, infračervená, CNS, COMPUTE). Chcete-li zobrazit soubory protokolu, bude nutné, abyste byli kořen (sudo SU-). Kořen je ve výchozím nastavení zakázán pro přístup SSH, takže nelze použít root pro SSH pro jiné uzly.

## <a name="log-files"></a>Soubory protokolů

Soubory protokolu (stderr a STDOUT) pro skripty přípravy hostitele se nacházejí ve složce `/var/lib/waagent/custom-script/download/0` na všech hostitelích. Pokud během přípravy hostitele došlo k chybě, Projděte si tyto soubory protokolů a určete chybu.

Pokud se přípravné skripty úspěšně spustily, `/var/lib/waagent/custom-script/download/1` bude nutné prozkoumat soubory protokolu v adresáři PlayBook hostitele Ansible. Pokud k chybě došlo při vlastní instalaci OpenShift, zobrazí se v souboru stdout chyba. Pomocí těchto informací můžete kontaktovat podporu a požádat o další pomoc.

Příklad výstupu

```json
TASK [openshift_storage_glusterfs : Load heketi topology] **********************
fatal: [mycluster-master-0]: FAILED! => {"changed": true, "cmd": ["oc", "--config=/tmp/openshift-glusterfs-ansible-IbhnUM/admin.kubeconfig", "rsh", "--namespace=glusterfs", "deploy-heketi-storage-1-d9xl5", "heketi-cli", "-s", "http://localhost:8080", "--user", "admin", "--secret", "VuoJURT0/96E42Vv8+XHfsFpSS8R20rH1OiMs3OqARQ=", "topology", "load", "--json=/tmp/openshift-glusterfs-ansible-IbhnUM/topology.json", "2>&1"], "delta": "0:00:21.477831", "end": "2018-05-20 02:49:11.912899", "failed": true, "failed_when_result": true, "rc": 0, "start": "2018-05-20 02:48:50.435068", "stderr": "", "stderr_lines": [], "stdout": "Creating cluster ... ID: 794b285745b1c5d7089e1c5729ec7cd2\n\tAllowing file volumes on cluster.\n\tAllowing block volumes on cluster.\n\tCreating node mycluster-cns-0 ... ID: 45f1a3bfc20a4196e59ebb567e0e02b4\n\t\tAdding device /dev/sdd ... OK\n\t\tAdding device /dev/sde ... OK\n\t\tAdding device /dev/sdf ... OK\n\tCreating node mycluster-cns-1 ... ID: 596f80d7bbd78a1ea548930f23135131\n\t\tAdding device /dev/sdc ... Unable to add device: Unable to execute command on glusterfs-storage-4zc42:   Device /dev/sdc excluded by a filter.\n\t\tAdding device /dev/sde ... OK\n\t\tAdding device /dev/sdd ... OK\n\tCreating node mycluster-cns-2 ... ID: 42c0170aa2799559747622acceba2e3f\n\t\tAdding device /dev/sde ... OK\n\t\tAdding device /dev/sdf ... OK\n\t\tAdding device /dev/sdd ... OK", "stdout_lines": ["Creating cluster ... ID: 794b285745b1c5d7089e1c5729ec7cd2", "\tAllowing file volumes on cluster.", "\tAllowing block volumes on cluster.", "\tCreating node mycluster-cns-0 ... ID: 45f1a3bfc20a4196e59ebb567e0e02b4", "\t\tAdding device /dev/sdd ... OK", "\t\tAdding device /dev/sde ... OK", "\t\tAdding device /dev/sdf ... OK", "\tCreating node mycluster-cns-1 ... ID: 596f80d7bbd78a1ea548930f23135131", "\t\tAdding device /dev/sdc ... Unable to add device: Unable to execute command on glusterfs-storage-4zc42:   Device /dev/sdc excluded by a filter.", "\t\tAdding device /dev/sde ... OK", "\t\tAdding device /dev/sdd ... OK", "\tCreating node mycluster-cns-2 ... ID: 42c0170aa2799559747622acceba2e3f", "\t\tAdding device /dev/sde ... OK", "\t\tAdding device /dev/sdf ... OK", "\t\tAdding device /dev/sdd ... OK"]}

PLAY RECAP *********************************************************************
mycluster-cns-0       : ok=146  changed=57   unreachable=0    failed=0   
mycluster-cns-1       : ok=146  changed=57   unreachable=0    failed=0   
mycluster-cns-2       : ok=146  changed=57   unreachable=0    failed=0   
mycluster-infra-0     : ok=143  changed=55   unreachable=0    failed=0   
mycluster-infra-1     : ok=143  changed=55   unreachable=0    failed=0   
mycluster-infra-2     : ok=143  changed=55   unreachable=0    failed=0   
mycluster-master-0    : ok=502  changed=198  unreachable=0    failed=1   
mycluster-master-1    : ok=348  changed=140  unreachable=0    failed=0   
mycluster-master-2    : ok=348  changed=140  unreachable=0    failed=0   
mycluster-node-0      : ok=143  changed=55   unreachable=0    failed=0   
mycluster-node-1      : ok=143  changed=55   unreachable=0    failed=0   
localhost                  : ok=13   changed=0    unreachable=0    failed=0   

INSTALLER STATUS ***************************************************************
Initialization             : Complete (0:00:39)
Health Check               : Complete (0:00:24)
etcd Install               : Complete (0:01:24)
Master Install             : Complete (0:14:59)
Master Additional Install  : Complete (0:01:10)
Node Install               : Complete (0:10:58)
GlusterFS Install          : In Progress (0:03:33)
    This phase can be restarted by running: playbooks/openshift-glusterfs/config.yml

Failure summary:

  1. Hosts:    mycluster-master-0
     Play:     Configure GlusterFS
     Task:     Load heketi topology
     Message:  Failed without returning a message.
```

Nejběžnější chyby při instalaci:

1. Privátní klíč má přístupové heslo.
2. Tajný kód trezoru klíčů s privátním klíčem nebyl správně vytvořen.
3. Přihlašovací údaje instančního objektu byly zadány nesprávně.
4. Instanční objekt nemá přístup přispěvatele ke skupině prostředků.

### <a name="private-key-has-a-passphrase"></a>Privátní klíč má přístupové heslo.

Zobrazí se chyba, že oprávnění pro SSH bylo zamítnuto. SSH pro hostitele PlayBook Ansible pro kontrolu hesla na privátním klíči.

### <a name="key-vault-secret-with-private-key-wasnt-created-correctly"></a>Tajný kód trezoru klíčů s privátním klíčem nebyl správně vytvořen.

Privátní klíč se zkopíruje do hostitele PlayBook Ansible-~/.ssh/id_rsa. Potvrďte, že je tento soubor správný. Otestujte otevřením relace SSH na jednom z uzlů clusteru z PlayBook hostitele Ansible.

### <a name="service-principal-credentials-were-entered-incorrectly"></a>Přihlašovací údaje instančního objektu byly zadány nesprávně.

Když zadáte vstup do šablony nebo nabídky Marketplace, zadali jste nesprávné informace. Ujistěte se, že jste pro instanční objekt používali správné appId (clientId) a Password (clientSecret). Ověřte vyvoláním následujícího příkazu rozhraní příkazového řádku Azure CLI.

```azurecli
az login --service-principal -u <client id> -p <client secret> -t <tenant id>
```

### <a name="service-principal-doesnt-have-contributor-access-to-the-resource-group"></a>Instanční objekt nemá přístup přispěvatele ke skupině prostředků.

Pokud je poskytovatel cloudového Azure povolený, použitý instanční objekt musí mít přístup přispěvatele k této skupině prostředků. Ověřte vyvoláním následujícího příkazu rozhraní příkazového řádku Azure CLI.

```azurecli
az group update -g <openshift resource group> --set tags.sptest=test
```

## <a name="additional-tools"></a>Další nástroje

V případě některých chyb můžete k získání dalších informací použít také následující příkazy:

1. stav systemctl \<service>
2. journalctl – XE
