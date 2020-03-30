---
title: Událost vytvoření fondu dávek Azure
description: Odkaz pro událost vytvoření fondu dávek, která je vyzařována po vytvoření fondu. Obsah protokolu bude zpřístupnit obecné informace o fondu.
services: batch
author: LauraBrenner
manager: evansma
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: labrenne
ms.openlocfilehash: dea025b274278aa5fed2900c95b4a274541ffef9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022185"
---
# <a name="pool-create-event"></a>Událost vytvoření fondu

 Tato událost je vyzařována po vytvoření fondu. Obsah protokolu bude zpřístupnit obecné informace o fondu. Všimněte si, že pokud je cílová velikost fondu větší než 0 výpočetních uzlů, bude událost změny velikosti fondu následovat bezprostředně po této události.

 Následující příklad ukazuje tělo fondu vytvořit událost pro fond `CloudServiceConfiguration` vytvořený pomocí vlastnosti.

```
{
    "id": "myPool1",
    "displayName": "Production Pool",
    "vmSize": "Standard_F1s",
    "imageType": "VirtualMachineConfiguration",
    "cloudServiceConfiguration": {
        "osFamily": "3",
        "targetOsVersion": "*"
    },
    "networkConfiguration": {
        "subnetId": " "
    },
    "virtualMachineConfiguration": {
          "imageReference": {
            "publisher": " ",
            "offer": " ",
            "sku": " ",
            "version": " "
          },
          "nodeAgentId": " "
        },
    "resizeTimeout": "300000",
    "targetDedicatedNodes": 2,
    "targetLowPriorityNodes": 2,
    "maxTasksPerNode": 1,
    "vmFillType": "Spread",
    "enableAutoScale": false,
    "enableInterNodeCommunication": false,
    "isAutoPool": false
}
```

|Element|Typ|Poznámky|
|-------------|----------|-----------|
|`id`|Řetězec|ID bazénu.|
|`displayName`|Řetězec|Zobrazovaný název fondu.|
|`vmSize`|Řetězec|Velikost virtuálních počítačů ve fondu. Všechny virtuální počítače ve fondu mají stejnou velikost. <br/><br/> Informace o dostupných velikostech virtuálních počítačů pro fondy cloudových služeb (fondy vytvořené pomocí cloudServiceConfiguration) najdete v [tématu Velikosti pro cloudové služby](https://azure.microsoft.com/documentation/articles/cloud-services-sizes-specs/). Batch podporuje všechny velikosti virtuálních počítače cloudových služeb s výjimkou `ExtraSmall`.<br/><br/> Informace o dostupných velikostech virtuálních počítačů pro fondy pomocí iobrazek z webu Virtual Machines Marketplace (fondy vytvořené pomocí virtualMachineConfiguration) najdete v [tématu Velikosti virtuálních počítačů](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-sizes/) (Linux) nebo [Velikosti pro virtuální počítače](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/) (Windows). Služba Batch podporuje všechny velikosti VM Azure kromě `STANDARD_A0` a těch, které používají Premium Storage (série `STANDARD_GS`, `STANDARD_DS` a `STANDARD_DSV2`).|
|`imageType`|Řetězec|Metoda nasazení pro bitovou kopii. Podporované hodnoty `virtualMachineConfiguration` jsou nebo`cloudServiceConfiguration`|
|[`cloudServiceConfiguration`](#bk_csconf)|Komplexní typ|Konfigurace cloudové služby pro fond.|
|[`virtualMachineConfiguration`](#bk_vmconf)|Komplexní typ|Konfigurace virtuálního počítače pro fond.|
|[`networkConfiguration`](#bk_netconf)|Komplexní typ|Konfigurace sítě pro fond.|
|`resizeTimeout`|Time|Časový čas pro přidělení výpočetních uzlů do fondu určeného pro poslední operaci změny velikosti ve fondu.  (Počáteční velikost při vytvoření fondu se počítá jako změna velikosti.)|
|`targetDedicatedNodes`|Int32|Počet vyhrazených výpočetních uzlů, které jsou požadovány pro fond.|
|`targetLowPriorityNodes`|Int32|Počet výpočetních uzlů s nízkou prioritou, které jsou požadovány pro fond.|
|`enableAutoScale`|Logická hodnota|Určuje, zda se velikost fondu v průběhu času automaticky upravuje.|
|`enableInterNodeCommunication`|Logická hodnota|Určuje, zda je fond nastaven pro přímou komunikaci mezi uzly.|
|`isAutoPool`|Logická hodnota|Určuje, zda byl fond vytvořen pomocí mechanismu automatického spoluvytváření úlohy.|
|`maxTasksPerNode`|Int32|Maximální počet úloh, které lze spustit souběžně na jednom výpočetním uzlu ve fondu.|
|`vmFillType`|Řetězec|Definuje, jak služba Batch distribuuje úlohy mezi výpočetními uzly ve fondu. Platné hodnoty jsou Spread nebo Pack.|

###  <a name="cloudserviceconfiguration"></a><a name="bk_csconf"></a>cloudServiceKonfigurace

|Název elementu|Typ|Poznámky|
|------------------|----------|-----------|
|`osFamily`|Řetězec|Rodina azure hostovaného operačního systému, které mají být nainstalovány na virtuálních počítačích ve fondu.<br /><br /> Možné hodnoty:<br /><br /> **2** – OS Family 2, ekvivalentní systému Windows Server 2008 R2 SP1.<br /><br /> **3** – OS Family 3, což odpovídá Systému Windows Server 2012.<br /><br /> **4** – OS Family 4, což odpovídá systému Windows Server 2012 R2.<br /><br /> Další informace najdete [v tématu Azure Guest OS Releases .](https://azure.microsoft.com/documentation/articles/cloud-services-guestos-update-matrix/#releases)|
|`targetOSVersion`|Řetězec|Verze hostovaného operačního systému Azure, která se má nainstalovat na virtuální počítače ve fondu.<br /><br /> Výchozí hodnota **\*** je, která určuje nejnovější verzi operačního systému pro zadanou rodinu.<br /><br /> Další povolené hodnoty najdete v [tématu Azure Guest OS Releases](https://azure.microsoft.com/documentation/articles/cloud-services-guestos-update-matrix/#releases).|

###  <a name="virtualmachineconfiguration"></a><a name="bk_vmconf"></a>virtualMachineConfiguration

|Název elementu|Typ|Poznámky|
|------------------|----------|-----------|
|[`imageReference`](#bk_imgref)|Komplexní typ|Určuje informace o platformě nebo image Marketplace, která má být používána.|
|`nodeAgentId`|Řetězec|Skladová položka agenta uzlu Batch zřízená na výpočetníuzel.|
|[`windowsConfiguration`](#bk_winconf)|Komplexní typ|Určuje nastavení operačního systému Windows ve virtuálním počítači. Tuto vlastnost nelze zadat, pokud imageReference odkazuje na bitovou kopii operačního systému Linux.|

###  <a name="imagereference"></a><a name="bk_imgref"></a>imageReference

|Název elementu|Typ|Poznámky|
|------------------|----------|-----------|
|`publisher`|Řetězec|Vydavatel obrázku.|
|`offer`|Řetězec|Nabídka obrazu.|
|`sku`|Řetězec|Skladová položka obrázku.|
|`version`|Řetězec|Verze obrázku.|

###  <a name="windowsconfiguration"></a><a name="bk_winconf"></a>windowsKonfigurace

|Název elementu|Typ|Poznámky|
|------------------|----------|-----------|
|`enableAutomaticUpdates`|Logická hodnota|Označuje, zda je virtuální počítač povolen pro automatické aktualizace. Pokud tato vlastnost není zadána, výchozí hodnota je true.|

###  <a name="networkconfiguration"></a><a name="bk_netconf"></a>síťKonfigurace

|Název elementu|Typ|Poznámky|
|------------------|--------------|----------|
|`subnetId`|Řetězec|Určuje identifikátor prostředku podsítě, ve které jsou vytvořeny výpočetní uzly fondu.|
