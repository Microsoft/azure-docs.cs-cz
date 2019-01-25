---
title: Kurz – Automatické škálování škálovací sady pomocí Azure CLI | Microsoft Docs
description: Zjistěte, jak pomocí Azure CLI automaticky škálovat škálovací sadu virtuálních počítačů s ohledem na zvyšující a snižující se požadavky na CPU.
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/18/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 3160624f6daa28c30c0888229ba53b0e80b2bc7a
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54887984"
---
# <a name="tutorial-automatically-scale-a-virtual-machine-scale-set-with-the-azure-cli"></a>Kurz: Automatické škálování škálovací sady virtuálních počítačů pomocí Azure CLI

Při vytváření škálovací sady definujete počet instancí virtuálních počítačů, které chcete spouštět. S měnícími se požadavky na aplikaci můžete počet instancí virtuálních počítačů automaticky zvyšovat nebo snižovat. Možnost automatického škálování umožňuje držet krok s požadavky zákazníků nebo reagovat na změny výkonu aplikace v průběhu jejího životního cyklu. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Použití automatického škálování u škálovací sady
> * Vytvoření a použití pravidel automatického škálování
> * Zátěžový test instancí virtuálních počítačů a aktivace pravidel automatického škálování
> * Opětovné automatické horizontální snížení kapacity po snížení požadavků

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a místně používat rozhraní příkazového řádku, musíte mít Azure CLI verze 2.0.32 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-a-scale-set"></a>Vytvoření škálovací sady

Následujícím způsobem vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#create):

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Teď vytvořte škálovací sadu virtuálních počítačů pomocí příkazu [az vmss create](/cli/azure/vmss#create). Následující příklad vytvoří škálovací sadu se *2* instancemi a vygeneruje klíče SSH, pokud ještě neexistují:

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --instance-count 2 \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="define-an-autoscale-profile"></a>Definice profilu automatického škálování

Pokud chcete povolit automatické škálování na škálovací sadě, je nejdříve potřeba definovat profil automatického škálování. Tento profil definuje výchozí, minimální a maximální kapacitu škálovací sady. Tato omezení umožňují řídit náklady tím, že nedochází k nepřetržitému vytváření instancí virtuálních počítačů, a vyvažují přijatelný výkon s minimálním počtem instancí, které zůstanou v události horizontálního snížení kapacity. Vytvořte profil automatického škálování pomocí příkazu [az monitor autoscale create](/cli/azure/monitor/autoscale#az-monitor-autoscale-create). Následující příklad nastaví výchozí a minimální kapacitu na *2* instance virtuálních počítačů a maximální kapacitu na *10*:

```azurecli-interactive
az monitor autoscale create \
  --resource-group myResourceGroup \
  --resource myScaleSet \
  --resource-type Microsoft.Compute/virtualMachineScaleSets \
  --name autoscale \
  --min-count 2 \
  --max-count 10 \
  --count 2
```

## <a name="create-a-rule-to-autoscale-out"></a>Vytvoření pravidla automatického horizontálního navýšení kapacity

Pokud se požadavky na vaši aplikaci zvýší, zvýší se i zatížení instancí virtuálních počítačů ve škálovací sadě. Pokud je toto zvýšené zatížení konzistentní, a nejedná se pouze o krátkou poptávku, můžete nakonfigurovat pravidla automatického škálování pro zvýšení počtu instancí virtuálních počítačů ve škálovací sadě. Po vytvoření těchto instancí virtuálních počítačů a nasazení aplikací do nich začne škálovací sada distribuovat provoz prostřednictvím nástroje pro vyrovnávání zatížení. Můžete řídit, které metriky se mají monitorovat, například CPU nebo disk, jak dlouho musí zatížení aplikace dosahovat dané prahové hodnoty a kolik instancí virtuálních počítačů se má do škálovací sady přidat.

Teď pomocí příkazu [az monitor autoscale rule create](/cli/azure/monitor/autoscale/rule#az-monitor-autoscale-rule-create) vytvořte pravidlo, které zvýší počet instancí virtuálních počítačů ve škálovací sadě, pokud je průměrné zatížení CPU vyšší než 70 % po dobu 5 minut. Když se pravidlo aktivuje, počet instancí virtuálních počítačů se zvýší o tři.

```azurecli-interactive
az monitor autoscale rule create \
  --resource-group myResourceGroup \
  --autoscale-name autoscale \
  --condition "Percentage CPU > 70 avg 5m" \
  --scale out 3
```

## <a name="create-a-rule-to-autoscale-in"></a>Vytvoření pravidla automatického horizontálního snížení kapacity

Večer nebo o víkendu se požadavky na vaši aplikaci můžou snížit. Pokud je toto snížené zatížení po určitou dobu konzistentní, můžete nakonfigurovat pravidla automatického škálování pro snížení počtu instancí virtuálních počítačů ve škálovací sadě. Tato akce horizontálního snížení kapacity sníží náklady na provoz škálovací sady, protože budete spouštět pouze takový počet instancí, který je potřeba ke zpracování aktuálních požadavků.

Pomocí příkazu [az monitor autoscale rule create](/cli/azure/monitor/autoscale/rule#az-monitor-autoscale-rule-create) vytvořte další pravidlo, které sníží počet instancí virtuálních počítačů ve škálovací sadě, pokud se průměrné zatížení CPU sníží pod 30 % po dobu 5 minut. Následující příklad definuje pravidlo pro horizontální snížení kapacity počtu instancí virtuálních počítačů o jednu:

```azurecli-interactive
az monitor autoscale rule create \
  --resource-group myResourceGroup \
  --autoscale-name autoscale \
  --condition "Percentage CPU < 30 avg 5m" \
  --scale in 1
```

## <a name="generate-cpu-load-on-scale-set"></a>Generování zatížení CPU ve škálovací sadě

Pokud chcete pravidla automatického škálování otestovat, vygenerujte nějaké zatížení CPU instancí virtuálních počítačů ve škálovací sadě. Toto simulované zatížení CPU způsobí, že pravidla automatického škálování provedou horizontální navýšení kapacity a zvýší počet instancí virtuálních počítačů. S následným snížením zatížení CPU pravidla automatického škálování provedou horizontální snížení kapacity a sníží počet instancí virtuálních počítačů.

Nejprve vypište adresu a porty pro připojení k instancím virtuálních počítačů ve škálovací sadě pomocí příkazu [az vmss list-instance-connection-info](/cli/azure/vmss#az_vmss_list_instance_connection_info):

```azurecli-interactive
az vmss list-instance-connection-info \
  --resource-group myResourceGroup \
  --name myScaleSet
```

Následující příklad výstupu ukazuje název instance, veřejnou IP adresu nástroje pro vyrovnávání zatížení a číslo portu, na které pravidla překladu adres směrují provoz:

```json
{
  "instance 1": "13.92.224.66:50001",
  "instance 3": "13.92.224.66:50003"
}
```

Připojte se přes SSH k první instanci virtuálního počítače. Pomocí parametru `-p` zadejte vlastní veřejnou IP adresu a číslo portu uvedené ve výstupu předchozího příkazu:

```azurecli-interactive
ssh azureuser@13.92.224.66 -p 50001
```

Po přihlášení nainstalujte nástroj **stress**. Spusťte *10* pracovních procesů **stress**, které vygenerují zatížení CPU. Tyto pracovní procesy budou spuštěné *420* sekund, což je dostatečná doba na to, aby pravidla automatického škálování implementovala požadovanou akci.

```azurecli-interactive
sudo apt-get -y install stress
sudo stress --cpu 10 --timeout 420 &
```

Při **zátěže** zobrazí podobný výstup *stress: info: [2688] dispatching hogs: 10 cpu, 0 io, 0 vm, 0 hdd*, stiskněte *Enter* klíč se vraťte do příkazového řádku.

Pokud chcete potvrdit, že nástroj **stress** generuje zatížení CPU, prozkoumejte zatížení aktivního systému pomocí nástroje **top**:

```azuecli-interactive
top
```

Ukončete nástroj **top** a pak ukončete připojení k instanci virtuálního počítače. Nástroj **stress** zůstane na instanci virtuálního počítače spuštěný.

```azurecli-interactive
Ctrl-c
exit
```

Připojte se k druhé instanci virtuálního počítače s použitím čísla portu uvedeného ve výstupu předchozího příkazu [az vmss list-instance-connection-info](/cli/azure/vmss#az_vmss_list_instance_connection_info):

```azurecli-interactive
ssh azureuser@13.92.224.66 -p 50003
```

Nainstalujte a spusťte nástroj **stress** a pak na této druhé instanci spusťte deset pracovních procesů.

```azurecli-interactive
sudo apt-get -y install stress
sudo stress --cpu 10 --timeout 420 &
```

Znovu, když **zátěže** zobrazí podobný výstup *stress: info: [2713] dispatching hogs: 10 cpu, 0 io, 0 vm, 0 hdd*, stiskněte *Enter* klíč se vraťte do příkazového řádku.

Ukončete připojení k druhé instanci virtuálního počítače. Nástroj **stress** zůstane na instanci virtuálního počítače spuštěný.

```azurecli-interactive
exit
```

## <a name="monitor-the-active-autoscale-rules"></a>Monitorování aktivních pravidel automatického škálování

K monitorování počtu instancí virtuálních počítačů ve škálovací sadě použijte nástroj **watch**. Zahájení procesu škálování na více instancí na základě zatížení CPU generovaného nástrojem **stress** na jednotlivých instancích virtuálních počítačů pravidly automatického škálování trvá přibližně 5 minut:

```azurecli-interactive
watch az vmss list-instances \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --output table
```

Po dosažení prahové hodnoty CPU pravidla automatického škálování navýší počet instancí virtuálních počítačů ve škálovací sadě. Následující výstup ukazuje tři virtuální počítače vytvořené při automatickém horizontálním navýšení kapacity škálovací sady:

```bash
Every 2.0s: az vmss list-instances --resource-group myResourceGroup --name myScaleSet --output table

  InstanceId  LatestModelApplied    Location    Name          ProvisioningState    ResourceGroup    VmId
------------  --------------------  ----------  ------------  -------------------  ---------------  ------------------------------------
           1  True                  eastus      myScaleSet_1  Succeeded            myResourceGroup  4f92f350-2b68-464f-8a01-e5e590557955
           2  True                  eastus      myScaleSet_2  Succeeded            myResourceGroup  d734cd3d-fb38-4302-817c-cfe35655d48e
           4  True                  eastus      myScaleSet_4  Creating             myResourceGroup  061b4c90-0d73-49fc-a066-19eab0b3d95c
           5  True                  eastus      myScaleSet_5  Creating             myResourceGroup  4beff8b9-4e65-40cb-9652-43899309da27
           6  True                  eastus      myScaleSet_6  Creating             myResourceGroup  9e4133dd-2c57-490e-ae45-90513ce3b336
```

Jakmile se nástroj **stress** na původních instancích virtuálních počítačů zastaví, průměrné zatížení CPU se vrátí do normálu. Po dalších 5 minutách pak pravidla automatického škálování horizontálně sníží kapacitu počtu instancí virtuálních počítačů. Akce horizontálního snížení kapacity odeberou nejprve instance virtuálních počítačů s nejvyšším ID. Když škálovací sada používá skupiny nebo zóny dostupnosti, akce pro horizontální snížení kapacity se mezi tyto instance virtuálních počítačů rovnoměrně rozloží. Následující příklad výstupu ukazuje jednu instanci virtuálního počítače odstraněnou při automatickém horizontálním snížení kapacity škálovací sady:

```bash
           6  True                  eastus      myScaleSet_6  Deleting             myResourceGroup  9e4133dd-2c57-490e-ae45-90513ce3b336
```

Stisknutím `Ctrl-c` ukončete nástroj *watch*. Kapacita škálovací sady se nadále bude každých 5 minut horizontálně snižovat odebráním jedné instance virtuálního počítače, dokud se nedosáhne minimálního počtu 2 instancí.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete odebrat škálovací sadu a další prostředky, odstraňte skupinu prostředků a všechny její prostředky pomocí příkazu [az group delete](/cli/azure/group#az_group_delete). Parametr `--no-wait` vrátí řízení na příkazový řádek bez čekání na dokončení operace. Parametr `--yes` potvrdí, že chcete prostředky odstranit, aniž by se na to zobrazoval další dotaz.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Další postup

V tomto kurzu jste zjistili, jak automaticky horizontálně snižovat nebo zvyšovat kapacitu škálovací sady pomocí Azure CLI:

> [!div class="checklist"]
> * Použití automatického škálování u škálovací sady
> * Vytvoření a použití pravidel automatického škálování
> * Zátěžový test instancí virtuálních počítačů a aktivace pravidel automatického škálování
> * Opětovné automatické horizontální snížení kapacity po snížení požadavků

Další příklady škálovacích sad virtuálních počítačů v akci najdete v následujících ukázkových skriptech Azure CLI:

> [!div class="nextstepaction"]
> [Ukázkové skripty Azure CLI pro škálovací sady](cli-samples.md)
