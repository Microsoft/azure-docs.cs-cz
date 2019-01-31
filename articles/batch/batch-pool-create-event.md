---
title: Událost vytvoření fondu Azure Batch | Dokumentace Microsoftu
description: Referenční informace pro fond služby Batch vytvoří událost.
services: batch
author: laurenhughes
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: lahugh
ms.openlocfilehash: 176f00de77c2d353d6efeb8b5a535a607b8f3204
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55470585"
---
# <a name="pool-create-event"></a>Událost vytvoření fondu

 Tato událost je vygenerován po vytvoření fondu. Obsah protokolu bude vystavovat obecné informace o fondu. Všimněte si, že pokud cílová velikost fondu je větší než 0 výpočetních uzlů, událost zahájení změny velikosti fondu bude následovat bezprostředně po této události.

 Následující příklad ukazuje text fondu vytvořit událost pro fond vytvořen pomocí vlastnosti CloudServiceConfiguration.

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

|Element|Type|Poznámky|
|-------------|----------|-----------|
|id|String|Id fondu.|
|displayName|String|Zobrazovaný název fondu.|
|vmSize|String|Velikost virtuálních počítačů ve fondu. Všechny virtuální počítače ve fondu mají stejnou velikost. <br/><br/> Informace o dostupných velikostech virtuálních počítačů pro Cloud Services fondy (fondy vytvořené s cloudServiceConfiguration), najdete v části [velikosti pro Cloud Services](https://azure.microsoft.com/documentation/articles/cloud-services-sizes-specs/). Služba batch podporuje všechny velikosti virtuálních počítačů cloudových služeb s výjimkou `ExtraSmall`.<br/><br/> Informace o dostupných virtuálních počítačů najdete v článku velikosti fondů pomocí imagí z Tržiště virtuálních počítačů (fondy vytvořené s virtualMachineConfiguration) [velikosti virtuálních počítačů](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-sizes/) (Linux) nebo [velikostí pro virtuální Počítače](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/) (Windows). Služba Batch podporuje všechny velikosti VM Azure kromě `STANDARD_A0` a těch, které používají Premium Storage (série `STANDARD_GS`, `STANDARD_DS` a `STANDARD_DSV2`).|
|[cloudServiceConfiguration](#bk_csconf)|Komplexní typ|Konfigurace cloudové služby pro fond.|
|[virtualMachineConfiguration](#bk_vmconf)|Komplexní typ|Konfigurace virtuálního počítače pro fond.|
|[networkConfiguration](#bk_netconf)|Komplexní typ|Konfigurace sítě fondu.|
|resizeTimeout|Čas|Časový limit pro přidělování výpočetních uzlů pro fond zadat pro poslední operace změny velikosti ve fondu.  (Počáteční velikosti při vytváření fondu se počítá jako změna velikosti.)|
|targetDedicated|Int32|Počet výpočetních uzlů, které jsou požadovány pro fond.|
|enableAutoScale|Bool|Určuje, zda se velikost fondu automaticky přizpůsobí v čase.|
|enableInterNodeCommunication|Bool|Určuje, zda fond je nastaven pro přímou komunikaci mezi uzly.|
|isAutoPool|Bool|Určuje, zda že byl fond vytvořen prostřednictvím mechanismu AutoPool úlohy.|
|maxTasksPerNode|Int32|Maximální počet úloh, které můžou běžet souběžně na jedné výpočetních uzlech ve fondu.|
|vmFillType|String|Definuje způsob, jakým služba Batch distribuuje úkoly mezi výpočetní uzly ve fondu. Platné hodnoty jsou rozloženy nebo aktualizací Service Pack.|

###  <a name="bk_csconf"></a> cloudServiceConfiguration

|Název elementu|Type|Poznámky|
|------------------|----------|-----------|
|atribut osFamily|String|Hostovaný operační systém Azure řady nainstalovat na virtuální počítače ve fondu.<br /><br /> Možné hodnoty:<br /><br /> **2** – operační systém řady 2, ekvivalentní k Windows serveru 2008 R2 SP1.<br /><br /> **3** – operačního systému rodiny 3, odpovídá na Windows Server 2012.<br /><br /> **4** – operačního systému řady 4, odpovídá na Windows Server 2012 R2.<br /><br /> Další informace najdete v tématu [verzí hostovaného operačního systému Azure](https://azure.microsoft.com/documentation/articles/cloud-services-guestos-update-matrix/#releases).|
|targetOSVersion|String|Verze hostovaného operačního systému Azure nainstalovat na virtuální počítače ve fondu.<br /><br /> Výchozí hodnota je **\*** označující poslední verze operačního systému pro zadané řady.<br /><br /> Další povolené hodnoty, najdete v části [verzí hostovaného operačního systému Azure](https://azure.microsoft.com/documentation/articles/cloud-services-guestos-update-matrix/#releases).|

###  <a name="bk_vmconf"></a> virtualMachineConfiguration

|Název elementu|Type|Poznámky|
|------------------|----------|-----------|
|[imageReference](#bk_imgref)|Komplexní typ|Určuje informace o platformě nebo image Marketplace, které chcete použít.|
|nodeAgentSKUId|String|SKU agenta uzlu služby Batch zřízené na výpočetním uzlu.|
|[windowsConfiguration](#bk_winconf)|Komplexní typ|Určuje nastavení operačního systému Windows na virtuálním počítači. Tato vlastnost nesmí být zadaná, pokud element imageReference odkazuje na image operačního systému Linux.|

###  <a name="bk_imgref"></a> Element imageReference

|Název elementu|Type|Poznámky|
|------------------|----------|-----------|
|vydavatele|String|Vydavatel image.|
|nabídka|String|Nabídka image.|
|SKU|String|Skladová položka image.|
|version|String|Verze image.|

###  <a name="bk_winconf"></a> windowsConfiguration

|Název elementu|Type|Poznámky|
|------------------|----------|-----------|
|enableAutomaticUpdates|Logická hodnota|Určuje, zda virtuální počítač je povolena pro automatické aktualizace. Pokud není tato vlastnost určena, výchozí hodnota je true.|

###  <a name="bk_netconf"></a> networkConfiguration

|Název elementu|Type|Poznámky|
|------------------|--------------|----------|
|subnetId|String|Určuje identifikátor prostředku z podsítě, ve kterém se vytvářejí výpočetní uzly fondu.|
