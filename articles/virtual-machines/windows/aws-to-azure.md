---
title: Přesunout virtuální počítače Windows AWS do Azure | Dokumentace Microsoftu
description: Přesuňte instance EC2 Windows Amazon Web Services (AWS) do virtuálního počítače Azure.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/01/2018
ms.author: cynthn
ms.openlocfilehash: 3fa890b02c791f26f3f25bf2418b105d1116ca75
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2018
ms.locfileid: "50094422"
---
# <a name="move-a-windows-vm-from-amazon-web-services-aws-to-an-azure-virtual-machine"></a>Přesunutí virtuálního počítače s Windows na virtuálním počítači Azure z Amazon Web Services (AWS)

Pokud hodnotíte virtuálních počítačů Azure pro hostování své pracovní zatížení, můžete exportovat existující instanci virtuálního počítače Windows EC2 Amazon Web Services (AWS) pak nahrajte virtuální pevný disk (VHD) do Azure. Po nahrání virtuálního pevného disku můžete vytvořit nový virtuální počítač v Azure z virtuálního pevného disku. 

Tento článek se týká přesunu jeden virtuální počítač z AWS do Azure. Pokud chcete přesunout virtuální počítače z AWS do Azure ve velkém měřítku, přečtěte si téma [migrace virtuálních počítačů v Amazon Web Services (AWS) do Azure pomocí Azure Site Recovery](../../site-recovery/site-recovery-migrate-aws-to-azure.md).

## <a name="prepare-the-vm"></a>Příprava virtuálního počítače 
 
Zobecněný a specializovaných virtuálních pevných disků můžete nahrát do Azure. Každý typ vyžaduje připravit virtuální počítač před exportem od AWS. 

- **Zobecněný virtuální pevný disk** – všechny informace vašeho osobního účtu odebrat, pomocí nástroje Sysprep měl generalizovaného virtuálního pevného disku. Pokud máte v úmyslu použít virtuální pevný disk jako image k vytvoření nové virtuální počítače z, měli byste: 
 
    * [Příprava virtuálního počítače Windows](prepare-for-upload-vhd-image.md).  
    * Generalizace virtuálního počítače pomocí nástroje Sysprep.  

 
- **Specializované virtuální pevný disk** -specializovaného virtuálního pevného disku uchovává uživatelské účty, aplikace a další data o stavu z původního virtuálního počítače. Pokud máte v úmyslu použít virtuální pevný disk jako – k vytvoření nového virtuálního počítače, ověřte následující kroky.  
    * [Příprava virtuálního pevného disku Windows k nahrání do Azure](prepare-for-upload-vhd-image.md). **Ne** generalizace virtuálního počítače pomocí nástroje Sysprep. 
    * Odeberte všechny hostované virtualizačních nástrojů a agenty, kteří jsou nainstalováni na virtuálním počítači (tj. nástroje VMware). 
    * Zajistěte, aby že virtuální počítač je nakonfigurovaný na vyžádání jeho IP adresu a nastavení DNS prostřednictvím protokolu DHCP. Tím se zajistí, že server při spuštění získá IP adresu v rámci virtuální sítě.  


## <a name="export-and-download-the-vhd"></a>Export a stáhnout virtuální pevný disk 

Exportujte EC2 instance virtuální pevný disk v kontejneru Amazon S3. Postupujte podle kroků v článku dokumentace Amazon [export Instance jako virtuální počítač pomocí virtuálního počítače Import/Export](http://docs.aws.amazon.com/vm-import/latest/userguide/vmexport.html) a spustit [vytvořit instance-export úlohu](http://docs.aws.amazon.com/cli/latest/reference/ec2/create-instance-export-task.html) příkaz pro export EC2 instance do souboru virtuálního pevného disku. 

Exportovaný soubor virtuálního pevného disku je uložen v intervalu Amazon S3, kterou zadáte. Základní syntaxe pro export virtuálního pevného disku je pod, stačí nahradit zástupný text v <brackets> s informacemi o.

```
aws ec2 create-instance-export-task --instance-id <instanceID> --target-environment Microsoft \
  --export-to-s3-task DiskImageFormat=VHD,ContainerFormat=ova,S3Bucket=<bucket>,S3Prefix=<prefix>
```

Jakmile byl exportován virtuálního pevného disku, postupujte podle pokynů v [jak stáhnout objekt ze sady S3?](http://docs.aws.amazon.com/AmazonS3/latest/user-guide/download-objects.html) stahování souborů virtuálního pevného disku z sady S3. 

> [!IMPORTANT]
> Poplatky za stažení virtuálního pevného disku za přenosy dat poplatky za AWS. Zobrazit [Amazon S3 ceny](https://aws.amazon.com/s3/pricing/) Další informace.


## <a name="next-steps"></a>Další postup

Teď můžete nahrát virtuální pevný disk do Azure a vytvoření nového virtuálního počítače. 

- Pokud jste spustili nástroj Sysprep na zdroji **generalize** ho před exportem, naleznete v tématu [nahrání generalizovaného virtuálního pevného disku a použít ho k vytvoření nové virtuální počítače v Azure](upload-generalized-managed.md)
- Pokud před exportem nebyl spuštěn nástroj Sysprep, virtuální pevný disk se považuje za **specializované**, naleznete v tématu [nahrání specializovaného virtuálního pevného disku do Azure a vytvoření nového virtuálního počítače](create-vm-specialized.md)

 
