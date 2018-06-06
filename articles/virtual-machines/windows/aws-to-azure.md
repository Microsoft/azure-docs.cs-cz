---
title: Přesunout virtuální počítače Windows AWS do Azure | Microsoft Docs
description: Přesuňte instanci Windows EC2 Amazon Web Services (AWS) pro virtuální počítače Azure pomocí Azure PowerShell.
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
ms.openlocfilehash: cb5b68e7bd0a1b247327e7147fe38eae19395f50
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34726529"
---
# <a name="move-a-windows-vm-from-amazon-web-services-aws-to-azure-using-powershell"></a>Přesunout virtuální počítač s Windows z Amazon Web Services (AWS) do Azure pomocí prostředí PowerShell

Pokud hodnotíte virtuální počítače Azure pro hostování vašich úloh, můžete exportovat existující instanci virtuálního počítače Windows EC2 Amazon Web Services (AWS) pak nahrajte virtuální pevný disk (VHD) do Azure. Po nahrání virtuálního pevného disku můžete vytvořit nový virtuální počítač v Azure z disku VHD. 

Tento článek se týká přesunu jeden virtuální počítač z AWS do Azure. Pokud chcete přesunout virtuální počítače z AWS do Azure ve velkém měřítku, přečtěte si téma [migraci virtuálních počítačů v Amazon Web Services (AWS) do Azure s Azure Site Recovery](../../site-recovery/site-recovery-migrate-aws-to-azure.md).

## <a name="prepare-the-vm"></a>Příprava virtuálního počítače 
 
Specializované i zobecněný virtuální pevné disky můžete nahrát do Azure. Každý typ vyžaduje, abyste připravili virtuální počítač před exportem z AWS. 

- **Zobecněný virtuální pevný disk** -zobecněný virtuální pevný disk má měl všechny vaše osobní účet informace odebrat pomocí nástroje Sysprep. Pokud máte v úmyslu použít virtuální pevný disk jako bitovou kopii k vytvoření nové virtuální počítače z, proveďte následující kroky: 
 
    * [Příprava systému Windows virtuálního počítače](prepare-for-upload-vhd-image.md).  
    * Generalize virtuální počítač pomocí nástroje Sysprep.  

 
- **Specializuje virtuálního pevného disku** -specializované virtuálního pevného disku uchovává uživatelské účty, aplikace a další data o stavu z vašeho původního virtuálního počítače. Pokud máte v úmyslu použít virtuální pevný disk jako-je chcete vytvořit nový virtuální počítač, zkontrolujte následující kroky.  
    * [Příprava virtuálního pevného disku Windows nahrát do Azure](prepare-for-upload-vhd-image.md). **Nechcete** generalize virtuální počítač pomocí nástroje Sysprep. 
    * Odeberte všechny hosta virtualizačních nástrojů a agentů, které jsou nainstalovány do virtuálního počítače (tj. nástroje VMware). 
    * Zajistěte, aby že virtuální počítač nakonfigurovaný tak, aby jeho IP adresu a nastavení DNS pomocí protokolu DHCP pro vyžádání obsahu. To zajistí, že server získá IP adresu v rámci virtuální sítě, při spuštění.  


## <a name="export-and-download-the-vhd"></a>Export a stáhnout virtuální pevný disk 

Exportujte EC2 instance virtuálního pevného disku v sady Amazon S3. Postupujte podle kroků v článku dokumentace Amazon [export Instance jako virtuálních počítačů pomocí virtuálních počítačů importu a exportu](http://docs.aws.amazon.com/vm-import/latest/userguide/vmexport.html) a spusťte [vytvořit instanci export úkolů](http://docs.aws.amazon.com/cli/latest/reference/ec2/create-instance-export-task.html) příkaz pro export EC2 instance do souboru virtuálního pevného disku. 

Exportovaný soubor virtuálního pevného disku se uloží do sady Amazon S3, které zadáte. Základní syntaxe pro export virtuálního pevného disku je menší než, nahraďte zástupný text v <brackets> s informacemi.

```
aws ec2 create-instance-export-task --instance-id <instanceID> --target-environment Microsoft \
  --export-to-s3-task DiskImageFormat=VHD,ContainerFormat=ova,S3Bucket=<bucket>,S3Prefix=<prefix>
```

Jakmile byla exportována virtuálního pevného disku, postupujte podle pokynů v [jak lze stáhnout objekt z sady S3?](http://docs.aws.amazon.com/AmazonS3/latest/user-guide/download-objects.html) ke stažení souboru virtuálního pevného disku z sady S3. 

> [!IMPORTANT]
> Přenos dat poplatky AWS poplatky za stahování virtuální pevný disk. V tématu [Amazon S3 ceny](https://aws.amazon.com/s3/pricing/) Další informace.


## <a name="next-steps"></a>Další postup

Nyní můžete nahrávat VHD do Azure a vytvořit nový virtuální počítač. 

- Pokud jste spustili nástroj Sysprep na svůj zdroj k **generalize** je před exportem, najdete v části [nahrát zobecněný virtuální pevný disk a použít ho k vytvoření nové virtuální počítače v Azure](upload-generalized-managed.md)
- Pokud před exportem nebyl spuštěn nástroj Sysprep, je považován za virtuální pevný disk **specializované**, najdete v části [nahrát specializované VHD do Azure a vytvoření nového virtuálního počítače](create-vm-specialized.md)

 
