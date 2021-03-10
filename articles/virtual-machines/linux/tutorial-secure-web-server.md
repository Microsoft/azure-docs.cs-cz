---
title: 'Kurz: zabezpečení webového serveru Linux s certifikáty TLS/SSL v Azure'
description: V tomto kurzu zjistíte, jak pomocí Azure CLI zabezpečit virtuální počítač s Linuxem, na kterém běží webový server NGINX, pomocí certifikátů SSL uložených ve službě Azure Key Vault.
services: virtual-machines
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines
ms.collection: linux
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/30/2018
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 3f762597ad81dfaba907115cbcf6074d81ec2fa4
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102549579"
---
# <a name="tutorial-secure-a-web-server-on-a-linux-virtual-machine-in-azure-with-tlsssl-certificates-stored-in-key-vault"></a>Kurz: zabezpečení webového serveru na virtuálním počítači se systémem Linux v Azure s certifikáty TLS/SSL uloženými v Key Vault
Aby bylo možné zabezpečit webové servery, lze pomocí protokolu TLS (Transport Layer Security), dříve označovaného jako SSL (Secure Sockets Layer) (SSL), použít certifikát k šifrování webového provozu. Tyto certifikáty TLS/SSL můžou být uložené v Azure Key Vault a umožňují zabezpečená nasazení certifikátů do virtuálních počítačů se systémem Linux v Azure. Co se v tomto kurzu naučíte:

> [!div class="checklist"]
> * Vytvoření služby Azure Key Vault
> * Generování nebo nahrání certifikátu do služby Key Vault
> * Vytvoření virtuálního počítače a instalace webového serveru NGINX
> * Vložení certifikátu do virtuálního počítače a konfigurace NGINX s použitím vazby TLS

V tomto kurzu se používá CLI v rámci [Azure Cloud Shell](../../cloud-shell/overview.md), který se průběžně aktualizuje na nejnovější verzi. Chcete-li otevřít Cloud Shell, vyberte možnost **vyzkoušet** v horní části libovolného bloku kódu.

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít Azure CLI verze 2.0.30 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli).


## <a name="overview"></a>Přehled
Azure Key Vault chrání kryptografické klíče a tajné kódy, jako jsou certifikáty a hesla. Key Vault pomáhá zjednodušit proces správy certifikátů a zajišťuje kontrolu nad klíči, které se používají k přístupu k těmto certifikátům. V rámci služby Key Vault můžete vytvořit certifikát podepsaný svým držitelem nebo nahrát stávající důvěryhodný certifikát, který již vlastníte.

Místo použití vlastní image virtuálního počítače, která zahrnuje integrované certifikáty, vložíte certifikáty do spuštěného virtuálního počítače. Tento proces zajistí, že se při nasazování na webový server nainstalují nejnovější certifikáty. Zároveň pokud obnovíte nebo nahradíte certifikát, nebudete muset vytvářet novou vlastní image virtuálního počítače. Při vytváření dalších virtuálních počítačů se automaticky vloží nejnovější certifikáty. Během celého procesu certifikáty neopustí platformu Azure ani nejsou zveřejněné v žádném skriptu, historii příkazového řádku nebo šabloně.


## <a name="create-an-azure-key-vault"></a>Vytvoření služby Azure Key Vault
Než vytvoříte službu Key Vault a certifikáty, vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group). Následující příklad vytvoří skupinu prostředků *myResourceGroupSecureWeb* v umístění *eastus*:

```azurecli-interactive 
az group create --name myResourceGroupSecureWeb --location eastus
```

Dále vytvořte službu Key Vault pomocí příkazu [az keyvault create](/cli/azure/keyvault) a povolte její použití při nasazování virtuálního počítače. Každá služba Key Vault vyžaduje jedinečný název, který by měl být malými písmeny. Nahraďte *\<mykeyvault>* v následujícím příkladu vlastním jedinečným názvem Key Vault:

```azurecli-interactive 
keyvault_name=<mykeyvault>
az keyvault create \
    --resource-group myResourceGroupSecureWeb \
    --name $keyvault_name \
    --enabled-for-deployment
```

## <a name="generate-a-certificate-and-store-in-key-vault"></a>Vygenerování certifikátu a jeho uložení do služby Key Vault
V případě použití v produkčním prostředí byste měli importovat platný certifikát podepsaný důvěryhodným poskytovatelem pomocí příkazu [az keyvault certificate import](/cli/azure/keyvault/certificate). Pro účely tohoto kurzu následující příklad ukazuje, jak můžete pomocí příkazu [az keyvault certificate create](/cli/azure/keyvault/certificate) vygenerovat certifikát podepsaný svým držitelem, který využívá výchozí zásady certifikátu:

```azurecli-interactive 
az keyvault certificate create \
    --vault-name $keyvault_name \
    --name mycert \
    --policy "$(az keyvault certificate get-default-policy)"
```

### <a name="prepare-a-certificate-for-use-with-a-vm"></a>Příprava certifikátu pro použití na virtuálním počítači
Pokud chcete certifikát použít během procesu vytváření virtuálního počítače, získejte ID vašeho certifikátu pomocí příkazu [az keyvault secret list-versions](/cli/azure/keyvault/secret). Převeďte certifikát pomocí příkazu [az vm secret format](/cli/azure/vm/secret#az-vm-secret-format). Z důvodu snadnějšího použití v dalších krocích přiřadí následující příklad výstup těchto příkazů do proměnných:

```azurecli-interactive 
secret=$(az keyvault secret list-versions \
          --vault-name $keyvault_name \
          --name mycert \
          --query "[?attributes.enabled].id" --output tsv)
vm_secret=$(az vm secret format --secrets "$secret" -g myResourceGroupSecureWeb --keyvault $keyvault_name)
```

### <a name="create-a-cloud-init-config-to-secure-nginx"></a>Vytvoření konfigurace cloud-init pro zabezpečení serveru NGINX
[Cloud-init](https://cloudinit.readthedocs.io) je široce využívaným přístupem k přizpůsobení virtuálního počítače s Linuxem při jeho prvním spuštění. Pomocí cloud-init můžete instalovat balíčky a zapisovat soubory nebo konfigurovat uživatele a zabezpečení. Vzhledem k tomu, že se cloud-init spustí během procesu prvotního spuštění, nevyžaduje použití vaší konfigurace žádné další kroky ani agenty.

Při vytváření virtuálního počítače se certifikáty a klíče uloží do chráněného adresáře */var/lib/waagent/*. Pokud chcete automatizovat přidávání certifikátů do virtuálního počítače a konfiguraci webového serveru, použijte cloud-init. V tomto příkladu nainstalujete a nakonfigurujete webový server NGINX. Pomocí stejného postupu můžete nainstalovat a nakonfigurovat i Apache. 

Vytvořte soubor *cloud-init-web-server.txt* a vložte do něj následující konfiguraci:

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 443 ssl;
        ssl_certificate /etc/nginx/ssl/mycert.cert;
        ssl_certificate_key /etc/nginx/ssl/mycert.prv;
      }
runcmd:
  - secretsname=$(find /var/lib/waagent/ -name "*.prv" | cut -c -57)
  - mkdir /etc/nginx/ssl
  - cp $secretsname.crt /etc/nginx/ssl/mycert.cert
  - cp $secretsname.prv /etc/nginx/ssl/mycert.prv
  - service nginx restart
```

### <a name="create-a-secure-vm"></a>Vytvoření zabezpečeného virtuálního počítače
Teď pomocí příkazu [az vm create](/cli/azure/vm) vytvořte virtuální počítač. Data certifikátu ze služby Key Vault se vloží pomocí parametru `--secrets`. Konfiguraci cloud-init předáte pomocí parametru `--custom-data`:

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroupSecureWeb \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init-web-server.txt \
    --secrets "$vm_secret"
```

Vytvoření virtuálního počítače, instalace balíčků a spuštění aplikace trvá několik minut. Po vytvoření virtuálního počítače si poznamenejte hodnotu `publicIpAddress` zobrazenou v Azure CLI. Tato adresa slouží k přístupu k vašemu webu ve webovém prohlížeči.

Pokud chcete pro přístup k virtuálnímu počítači povolit zabezpečený webový provoz, otevřete port 443 z internetu pomocí příkazu [az vm open-port](/cli/azure/vm):

```azurecli-interactive 
az vm open-port \
    --resource-group myResourceGroupSecureWeb \
    --name myVM \
    --port 443
```


### <a name="test-the-secure-web-app"></a>Testování zabezpečené webové aplikace
Nyní můžete otevřít webový prohlížeč a zadat *https: \/ \/ \<publicIpAddress>* na adresním řádku. Zadejte vlastní veřejnou IP adresu získanou při vytváření virtuálního počítače. Pokud jste použili certifikát podepsaný svým držitelem, přijměte upozornění zabezpečení:

![Přijetí upozornění zabezpečení ve webovém prohlížeči](./media/tutorial-secure-web-server/browser-warning.png)

Následně se zobrazí váš zabezpečený web NGINX, jak je znázorněno v následujícím příkladu:

![Zobrazení spuštěného zabezpečeného webu NGINX](./media/tutorial-secure-web-server/secured-nginx.png)


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zabezpečený webový server NGINX s certifikátem TLS/SSL uloženým v Azure Key Vault. Naučili jste se:

> [!div class="checklist"]
> * Vytvoření služby Azure Key Vault
> * Generování nebo nahrání certifikátu do služby Key Vault
> * Vytvoření virtuálního počítače a instalace webového serveru NGINX
> * Vložení certifikátu do virtuálního počítače a konfigurace NGINX s použitím vazby TLS

Na tomto odkazu najdete předem připravené ukázky skriptů pro virtuální počítače.

> [!div class="nextstepaction"]
> [Ukázky skriptů pro virtuální počítače s Linuxem](https://github.com/Azure-Samples/azure-cli-samples/tree/master/virtual-machine)