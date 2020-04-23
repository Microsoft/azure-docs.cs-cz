---
title: Přesunutí instance Windows AWS EC2 do Azure
description: Přesuňte instanci EC2 Windows (Amazon Web Services( AWS) do virtuálního počítače Azure.
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: article
ms.date: 06/01/2018
ms.author: cynthn
ms.openlocfilehash: 59d1bf08c0680d222710b55c6d6bdb4d5745da56
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2020
ms.locfileid: "82084511"
---
# <a name="move-a-windows-vm-from-amazon-web-services-aws-to-an-azure-virtual-machine"></a>Přesunutí virtuálního počítače s Windows z Amazon Web Services (AWS) do virtuálního počítače Azure

Pokud vyhodnocujete virtuální počítače Azure pro hostování úloh, můžete exportovat existující instanci virtuálního počítače EC2 ec2 služby Amazon Web Services (AWS) a pak nahrát virtuální pevný disk (VHD) do Azure. Po nahrání virtuálního pevného disku můžete vytvořit nový virtuální počítač v Azure z virtuálního pevného disku. 

Tento článek popisuje přesunutí jednoho virtuálního počítače z AWS do Azure. Pokud chcete přesunout virtuální počítače z AWS do Azure ve velkém měřítku, přečtěte si témat [u hlášet virtuální počítače ve službě Amazon Web Services (AWS) do Azure pomocí Azure Site Recovery](../../site-recovery/site-recovery-migrate-aws-to-azure.md).

## <a name="prepare-the-vm"></a>Příprava virtuálního počítače 
 
Do Azure můžete nahrát generalizované i specializované virtuální počítače. Každý typ vyžaduje připravit virtuální počítač před exportem z AWS. 

- **Generalizované Virtuální pevné rozlišení** – zobecněného virtuálního pevného disku byly odstraněny všechny informace o vašem osobním účtu pomocí sysprepu. Pokud máte v úmyslu použít virtuální pevný disk jako image k vytvoření nových virtuálních ms z, měli byste: 
 
    * [Příprava virtuálního počítače s Windows](prepare-for-upload-vhd-image.md).  
    * Zobecnit virtuální počítač pomocí sysprep.  

 
- **Specialized VHD** - specializovaný virtuální pevný disk udržuje uživatelské účty, aplikace a další stavová data z původního virtuálního počítače. Pokud máte v úmyslu použít virtuální pevný disk jako-je k vytvoření nového virtuálního virtuálního soudu, ujistěte se, že jsou dokončeny následující kroky.  
    * [Příprava virtuálního pevného disku windows pro nahrání do Azure](prepare-for-upload-vhd-image.md). **Nezobecnit** virtuálního mísu pomocí sysprep. 
    * Odeberte všechny nástroje pro virtualizaci hosta a agenty, které jsou nainstalované ve virtuálním počítači (tj. nástroje VMware). 
    * Ujistěte se, že virtuální počítače je nakonfigurovaný pro vytahování jeho IP adresu a nastavení DNS přes DHCP. Tím zajistíte, že server získá ip adresu v rámci virtuální sítě při spuštění.  


## <a name="export-and-download-the-vhd"></a>Export a stažení virtuálního pevného disku 

Exportujte instanci EC2 do virtuálního pevného disku v kontejneru Amazon S3. Postupujte podle kroků v článku dokumentace Amazon [exportinstance jako virtuálního virtuálního mě pomocí importu a exportu virtuálního měn](https://docs.aws.amazon.com/vm-import/latest/userguide/vmexport.html) a spusťte příkaz [create-instance-export-task-task](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-instance-export-task.html) pro export instance EC2 do souboru Virtuálního pevného disku. 

Exportovaný soubor VHD se uloží do zadaný hodu Amazon S3. Základní syntaxe pro export virtuálního pevného disku je \<níže, stačí nahradit zástupný text v závorkách> s vašimi informacemi.

```
aws ec2 create-instance-export-task --instance-id <instanceID> --target-environment Microsoft \
  --export-to-s3-task DiskImageFormat=VHD,ContainerFormat=ova,S3Bucket=<bucket>,S3Prefix=<prefix>
```

Po exportu virtuálního pevného disku postupujte podle pokynů v části [Jak stáhnout objekt z kontejneru S3?](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/download-objects.html) 

> [!IMPORTANT]
> AWS účtuje poplatky za přenos dat za stažení virtuálního pevného disku. Další informace naleznete v [tématu Amazon S3 Pricing.](https://aws.amazon.com/s3/pricing/)


## <a name="next-steps"></a>Další kroky

Teď můžete nahrát virtuální pevný disk do Azure a vytvořit nový virtuální počítač. 

- Pokud jste spustili program Sysprep ve zdroji, abyste ho před exportem **zobecnili,** přečtěte si informace [o nahrání generalizovaného virtuálního disku VHD a jeho použití k vytvoření nových virtuálních počítačů v Azure.](upload-generalized-managed.md)
- Pokud jste před exportem nespouštěli program Sysprep, virtuální pevný disk se považuje za **specializovaný**, přečtěte si informace [o nahrání specializovaného virtuálního pevného disku do Azure a vytvoření nového virtuálního počítače.](create-vm-specialized.md)

 
