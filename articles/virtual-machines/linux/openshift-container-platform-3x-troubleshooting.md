---
title: Poradce při potížích s nasazením platformy OpenShift Container Platform 3.11 v Azure
description: Poradce při potížích s nasazením platformy OpenShift Container Platform 3.11 v Azure.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: 90fd3680cfdc4ecd1dcb0ce33b63f8d76dd8bfae
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759468"
---
# <a name="troubleshoot-openshift-container-platform-311-deployment-in-azure"></a>Poradce při potížích s nasazením platformy OpenShift Container Platform 3.11 v Azure

Pokud se cluster OpenShift nenasadí úspěšně, portál Azure bude poskytovat výstup chyby. Výstup může být obtížné číst, což ztěžuje identifikaci problému. Rychle naskenujte tento výstup pro výstupní kód 3, 4 nebo 5. Následující informace o těchto třech výstupních kódech:

- Ukončovací kód 3: Uživatelské jméno / heslo nebo ID organizace / aktivační klíč předplatného Red Hat je nesprávné
- Kód ukončení 4: ID fondu Red Hat je nesprávné nebo nejsou k dispozici žádné nároky
- Ukončovací kód 5: Nelze zřídit svazek tenkého fondu Dockeru

Pro všechny ostatní ukončovací kódy se připojte k hostitelům přes ssh a zobrazte soubory protokolu.

**OpenShift Container Platform 3.11**

SSH na ansible playbook hostitele. Pro šablonu nebo nabídku Marketplace použijte hostitele bašty. Z bašty můžete SSH do všech ostatních uzlů v clusteru (master, infra, CNS, compute). Chcete-li zobrazit soubory protokolu, musíte být kořenové. Root je ve výchozím nastavení zakázán pro přístup SSH, takže nepoužívejte root na SSH do jiných uzlů.

**OKD**

SSH na ansible playbook hostitele. Pro šablonu OKD (verze 3.9 a starší) použijte hostitele master-0. Pro šablonu OKD (verze 3.10 a novější) použijte hostitele bastionu. Z ansible playbook hostitele, můžete SSH do všech ostatních uzlů v clusteru (master, infra, CNS, compute). Budete muset být root (sudo su -) pro zobrazení souborů protokolu. Root je ve výchozím nastavení zakázán pro přístup SSH, takže nepoužívejte root na SSH do jiných uzlů.

## <a name="log-files"></a>Soubory protokolu

Soubory protokolu (stderr a stdout) pro skripty `/var/lib/waagent/custom-script/download/0` přípravy hostitele jsou umístěny na všech hostitelích. Pokud došlo k chybě během přípravy hostitele, zobrazte tyto soubory protokolu k určení chyby.

Pokud byly přípravné skripty úspěšně spuštěny, `/var/lib/waagent/custom-script/download/1` bude nutné prozkoumat soubory protokolu v adresáři hostitele rezervovatelného adresáře. Pokud došlo k chybě během skutečné instalace OpenShift, soubor stdout zobrazí chybu. Pomocí těchto informací můžete kontaktovat podporu a požádat o další pomoc.

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

Nejčastější chyby během instalace jsou:

1. Soukromý klíč má přístupové heslo
2. Tajný klíč trezoru klíčů se soukromým klíčem nebyl vytvořen správně.
3. Pověření instančního objektu byla zadána nesprávně.
4. Instanční objekt nemá přístup přispěvatele ke skupině prostředků.

### <a name="private-key-has-a-passphrase"></a>Soukromý klíč má přístupové heslo

Zobrazí se chyba, že oprávnění bylo odepřeno pro ssh. ssh na ansible playbook hostitele zkontrolovat přístupové heslo na soukromém klíči.

### <a name="key-vault-secret-with-private-key-wasnt-created-correctly"></a>Tajný klíč trezoru klíčů se soukromým klíčem nebyl vytvořen správně.

Soukromý klíč je zkopírován do hostitele ansible playbook - ~/.ssh/id_rsa. Zkontrolujte, zda je tento soubor správný. Vyzkoušejte otevřením relace SSH do jednoho z uzlů clusteru z hostitele ansible playbook.

### <a name="service-principal-credentials-were-entered-incorrectly"></a>Pověření instančního objektu byla zadána nesprávně.

Při poskytování vstup do šablony nebo marketplace nabídky, byly poskytnuty nesprávné informace. Ujistěte se, že používáte správné appId (clientId) a heslo (clientSecret) pro instanční objekt. Ověřte vydáním následujícího příkazu azure cli.

```azurecli
az login --service-principal -u <client id> -p <client secret> -t <tenant id>
```

### <a name="service-principal-doesnt-have-contributor-access-to-the-resource-group"></a>Instanční objekt nemá přístup přispěvatele ke skupině prostředků.

Pokud je poskytovatel cloudu Azure povolen, musí mít použitý instanční objekt přístup k skupině prostředků. Ověřte vydáním následujícího příkazu azure cli.

```azurecli
az group update -g <openshift resource group> --set tags.sptest=test
```

## <a name="additional-tools"></a>Další nástroje

U některých chyb můžete také získat další informace pomocí následujících příkazů:

1. > stavové \<služby systemCTL
2. deníkctl -xe
