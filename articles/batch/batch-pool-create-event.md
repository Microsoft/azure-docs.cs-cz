---
title: Událost vytvoření fondu Azure Batch | Microsoft Docs
description: Referenční informace pro událost vytvoření fondu Batch
services: batch
author: laurenhughes
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: lahugh
ms.openlocfilehash: 083702a5340ed47370a8b4c7d64846848636ba30
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70094879"
---
# <a name="pool-create-event"></a>Událost vytvoření fondu

 Tato událost je generována po vytvoření fondu. Obsah protokolu bude zveřejňovat Obecné informace o fondu. Počítejte s tím, že pokud je cílová velikost fondu větší než 0 výpočetních uzlů, pak se událost spuštění změny velikosti fondu provede okamžitě po této události.

 Následující příklad ukazuje tělo události vytvoření fondu pro fond vytvořený pomocí vlastnosti CloudServiceConfiguration.

```
{
    "id": "myPool1",
    "displayName": "Production Pool",
    "vmSize": "Small",
    "cloudServiceConfiguration": {
        "osFamily": "3",
        "targetOsVersion": "*"
    },
    "networkConfiguration": {
        "subnetId": " "
    },
    "resizeTimeout": "300000",
    "targetDedicated": 2,
    "maxTasksPerNode": 1,
    "vmFillType": "Spread",
    "enableAutoScale": false,
    "enableInterNodeCommunication": false,
    "isAutoPool": false
}
```

|Prvek|type|Poznámky|
|-------------|----------|-----------|
|id|Řetězec|ID fondu|
|displayName|Řetězec|Zobrazovaný název fondu.|
|vmSize|Řetězec|Velikost virtuálních počítačů ve fondu. Všechny virtuální počítače ve fondu mají stejnou velikost. <br/><br/> Informace o dostupných velikostech virtuálních počítačů pro fondy Cloud Services (fondy vytvořené pomocí nástroje cloudServiceConfiguration) najdete v tématu [velikosti pro Cloud Services](https://azure.microsoft.com/documentation/articles/cloud-services-sizes-specs/). Batch podporuje všechny velikosti Cloud Services virtuálních počítačů `ExtraSmall`s výjimkou.<br/><br/> Informace o dostupných velikostech virtuálních počítačů pro fondy pomocí imagí z Virtual Machines Marketplace (fondy vytvořené pomocí virtualMachineConfiguration) najdete v tématu [velikosti pro Virtual Machines](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-sizes/) (Linux) nebo [velikosti pro Virtual Machines](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/) (Windows). Služba Batch podporuje všechny velikosti VM Azure kromě `STANDARD_A0` a těch, které používají Premium Storage (série `STANDARD_GS`, `STANDARD_DS` a `STANDARD_DSV2`).|
|[cloudServiceConfiguration](#bk_csconf)|Komplexní typ|Konfigurace cloudové služby pro fond.|
|[virtualMachineConfiguration](#bk_vmconf)|Komplexní typ|Konfigurace virtuálního počítače pro fond.|
|[networkConfiguration](#bk_netconf)|Komplexní typ|Konfigurace sítě pro fond.|
|resizeTimeout|Time|Časový limit pro přidělení výpočetních uzlů do fondu zadaného pro operaci poslední změny velikosti ve fondu.  (Počáteční velikost při vytváření fondu se počítá jako změna velikosti.)|
|targetDedicated|Int32|Počet výpočetních uzlů, které jsou pro fond požadovány.|
|enableAutoScale|Bool|Určuje, jestli se velikost fondu v průběhu času automaticky upraví.|
|enableInterNodeCommunication|Bool|Určuje, jestli je fond nastavený pro přímou komunikaci mezi uzly.|
|isAutoPool|Bool|Určuje, jestli se fond vytvořil pomocí mechanismu autopoolu úlohy.|
|maxTasksPerNode|Int32|Maximální počet úloh, které mohou běžet souběžně na jednom výpočetním uzlu ve fondu.|
|vmFillType|Řetězec|Definuje, jak služba Batch distribuuje úlohy mezi výpočetními uzly ve fondu. Platné hodnoty jsou rozprostření nebo sbalení.|

###  <a name="bk_csconf"></a>cloudServiceConfiguration

|Název elementu|type|Poznámky|
|------------------|----------|-----------|
|Atribut|Řetězec|Řada hostovaných operačních systémů Azure, která se má nainstalovat na virtuální počítače ve fondu.<br /><br /> Možné hodnoty jsou:<br /><br /> **2** – řada OS 2, ekvivalent Windows serveru 2008 R2 SP1.<br /><br /> **3** – řada OS 3, ekvivalent Windows Serveru 2012.<br /><br /> **4** – řada OS 4, která odpovídá Windows Serveru 2012 R2.<br /><br /> Další informace najdete v tématu [vydání hostovaného operačního systému Azure](https://azure.microsoft.com/documentation/articles/cloud-services-guestos-update-matrix/#releases).|
|targetOSVersion|Řetězec|Verze operačního systému hosta Azure, která se má nainstalovat na virtuální počítače ve fondu.<br /><br /> Výchozí hodnota je **\*** , která určuje nejnovější verzi operačního systému pro určenou rodinu.<br /><br /> Další povolené hodnoty najdete v tématu [vydání hostovaného operačního systému Azure](https://azure.microsoft.com/documentation/articles/cloud-services-guestos-update-matrix/#releases).|

###  <a name="bk_vmconf"></a>virtualMachineConfiguration

|Název elementu|type|Poznámky|
|------------------|----------|-----------|
|[imageReference](#bk_imgref)|Komplexní typ|Určuje informace o platformě nebo imagi Marketplace, které se mají použít.|
|nodeAgentSKUId|Řetězec|SKU agenta uzlu Batch zřízeného na výpočetním uzlu.|
|[windowsConfiguration](#bk_winconf)|Komplexní typ|Určuje nastavení operačního systému Windows na virtuálním počítači. Tato vlastnost nesmí být zadána, pokud element imagereference odkazuje na bitovou kopii operačního systému Linux.|

###  <a name="bk_imgref"></a>Element imagereference

|Název elementu|type|Poznámky|
|------------------|----------|-----------|
|publisher|Řetězec|Vydavatel obrázku|
|dodání|Řetězec|Nabídka obrázku|
|SKU|Řetězec|SKU obrázku.|
|version|Řetězec|Verze bitové kopie|

###  <a name="bk_winconf"></a>windowsConfiguration

|Název elementu|type|Poznámky|
|------------------|----------|-----------|
|enableAutomaticUpdates|Logická hodnota|Určuje, jestli je virtuální počítač povolený pro automatické aktualizace. Pokud není tato vlastnost zadána, výchozí hodnota je true.|

###  <a name="bk_netconf"></a>networkConfiguration

|Název elementu|type|Poznámky|
|------------------|--------------|----------|
|subnetId|Řetězec|Určuje identifikátor prostředku podsítě, ve které jsou výpočetní uzly fondu vytvářeny.|
