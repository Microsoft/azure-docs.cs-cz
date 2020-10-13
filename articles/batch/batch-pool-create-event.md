---
title: Událost vytvoření fondu Azure Batch
description: Odkaz na událost vytvoření fondu Batch, která je generována po vytvoření fondu. Obsah protokolu bude zveřejňovat Obecné informace o fondu.
ms.topic: reference
ms.date: 10/08/2020
ms.openlocfilehash: dea0e04fa506274e645ba7a578039a0d6f021043
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91850944"
---
# <a name="pool-create-event"></a>Událost vytvoření fondu

 Tato událost je generována po vytvoření fondu. Obsah protokolu bude zveřejňovat Obecné informace o fondu. Počítejte s tím, že pokud je cílová velikost fondu větší než 0 výpočetních uzlů, pak se událost spuštění změny velikosti fondu provede okamžitě po této události.

 Následující příklad ukazuje tělo události vytvoření fondu pro fond vytvořený pomocí `CloudServiceConfiguration` Vlastnosti.

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
    "taskSlotsPerNode": 1,
    "vmFillType": "Spread",
    "enableAutoScale": false,
    "enableInterNodeCommunication": false,
    "isAutoPool": false
}
```

|Prvek|Typ|Poznámky|
|-------------|----------|-----------|
|`id`|Řetězec|ID fondu|
|`displayName`|Řetězec|Zobrazovaný název fondu.|
|`vmSize`|Řetězec|Velikost virtuálních počítačů ve fondu. Všechny virtuální počítače ve fondu mají stejnou velikost. <br/><br/> Informace o dostupných velikostech virtuálních počítačů pro fondy Cloud Services (fondy vytvořené pomocí nástroje cloudServiceConfiguration) najdete v tématu [velikosti pro Cloud Services](../cloud-services/cloud-services-sizes-specs.md). Batch podporuje všechny velikosti Cloud Services virtuálních počítačů s výjimkou `ExtraSmall` .<br/><br/> Informace o dostupných velikostech virtuálních počítačů pro fondy pomocí imagí z Virtual Machines Marketplace (fondy vytvořené pomocí virtualMachineConfiguration) najdete v tématu [velikosti pro Virtual Machines](../virtual-machines/linux/sizes.md?toc=%2Fazure%2Fvirtual-machines%2Flinux%2Ftoc.json) (Linux) nebo [velikosti pro Virtual Machines](../virtual-machines/windows/sizes.md?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json) (Windows). Služba Batch podporuje všechny velikosti VM Azure kromě `STANDARD_A0` a těch, které používají Premium Storage (série `STANDARD_GS`, `STANDARD_DS` a `STANDARD_DSV2`).|
|`imageType`|Řetězec|Metoda nasazení pro obrázek. Podporovány jsou hodnoty `virtualMachineConfiguration` nebo. `cloudServiceConfiguration`|
|[`cloudServiceConfiguration`](#bk_csconf)|Komplexní typ|Konfigurace cloudové služby pro fond.|
|[`virtualMachineConfiguration`](#bk_vmconf)|Komplexní typ|Konfigurace virtuálního počítače pro fond.|
|[`networkConfiguration`](#bk_netconf)|Komplexní typ|Konfigurace sítě pro fond.|
|`resizeTimeout`|Čas|Časový limit pro přidělení výpočetních uzlů do fondu zadaného pro operaci poslední změny velikosti ve fondu.  (Počáteční velikost při vytváření fondu se počítá jako změna velikosti.)|
|`targetDedicatedNodes`|Int32|Počet vyhrazených výpočetních uzlů, které jsou požadovány pro fond.|
|`targetLowPriorityNodes`|Int32|Počet výpočetních uzlů s nízkou prioritou, které jsou pro fond požadovány.|
|`enableAutoScale`|Logická hodnota|Určuje, jestli se velikost fondu v průběhu času automaticky upraví.|
|`enableInterNodeCommunication`|Logická hodnota|Určuje, jestli je fond nastavený pro přímou komunikaci mezi uzly.|
|`isAutoPool`|Logická hodnota|Určuje, jestli se fond vytvořil pomocí mechanismu autopoolu úlohy.|
|`taskSlotsPerNode`|Int32|Maximální počet úloh, které mohou běžet souběžně na jednom výpočetním uzlu ve fondu.|
|`vmFillType`|Řetězec|Definuje, jak služba Batch distribuuje úlohy mezi výpočetními uzly ve fondu. Platné hodnoty jsou rozprostření nebo sbalení.|

###  <a name="cloudserviceconfiguration"></a><a name="bk_csconf"></a> cloudServiceConfiguration

|Název elementu|Typ|Poznámky|
|------------------|----------|-----------|
|`osFamily`|Řetězec|Řada hostovaných operačních systémů Azure, která se má nainstalovat na virtuální počítače ve fondu.<br /><br /> Možné hodnoty:<br /><br /> **2** – řada OS 2, ekvivalent Windows serveru 2008 R2 SP1.<br /><br /> **3** – řada OS 3, ekvivalent Windows Serveru 2012.<br /><br /> **4** – řada OS 4, která odpovídá Windows Serveru 2012 R2.<br /><br /> Další informace najdete v tématu [vydání hostovaného operačního systému Azure](../cloud-services/cloud-services-guestos-update-matrix.md#releases).|
|`targetOSVersion`|Řetězec|Verze operačního systému hosta Azure, která se má nainstalovat na virtuální počítače ve fondu.<br /><br /> Výchozí hodnota je, **\*** která určuje nejnovější verzi operačního systému pro určenou rodinu.<br /><br /> Další povolené hodnoty najdete v tématu [vydání hostovaného operačního systému Azure](../cloud-services/cloud-services-guestos-update-matrix.md#releases).|

###  <a name="virtualmachineconfiguration"></a><a name="bk_vmconf"></a> virtualMachineConfiguration

|Název elementu|Typ|Poznámky|
|------------------|----------|-----------|
|[`imageReference`](#bk_imgref)|Komplexní typ|Určuje informace o platformě nebo imagi Marketplace, které se mají použít.|
|`nodeAgentId`|Řetězec|SKU agenta uzlu Batch zřízeného na výpočetním uzlu.|
|[`windowsConfiguration`](#bk_winconf)|Komplexní typ|Určuje nastavení operačního systému Windows na virtuálním počítači. Tato vlastnost nesmí být zadána, pokud element imagereference odkazuje na bitovou kopii operačního systému Linux.|

###  <a name="imagereference"></a><a name="bk_imgref"></a> Element imagereference

|Název elementu|Typ|Poznámky|
|------------------|----------|-----------|
|`publisher`|Řetězec|Vydavatel obrázku|
|`offer`|Řetězec|Nabídka obrázku|
|`sku`|Řetězec|SKU obrázku.|
|`version`|Řetězec|Verze bitové kopie|

###  <a name="windowsconfiguration"></a><a name="bk_winconf"></a> windowsConfiguration

|Název elementu|Typ|Poznámky|
|------------------|----------|-----------|
|`enableAutomaticUpdates`|Logická hodnota|Určuje, jestli je virtuální počítač povolený pro automatické aktualizace. Pokud není tato vlastnost zadána, výchozí hodnota je true.|

###  <a name="networkconfiguration"></a><a name="bk_netconf"></a> networkConfiguration

|Název elementu|Typ|Poznámky|
|------------------|--------------|----------|
|`subnetId`|Řetězec|Určuje identifikátor prostředku podsítě, ve které jsou výpočetní uzly fondu vytvářeny.|
