---
title: Přesunutí instance Windows AWS EC2 do Azure
description: Přesuňte Amazon Web Services (AWS) EC2 instanci Windows na virtuální počítač Azure.
author: cynthn
ms.service: virtual-machines
ms.collection: windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 06/01/2018
ms.author: cynthn
ms.openlocfilehash: 86d3e67b121bf7d66651e1e823e5f137e918a171
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102550786"
---
# <a name="move-a-windows-vm-from-amazon-web-services-aws-to-an-azure-virtual-machine"></a>Přesunutí virtuálního počítače s Windows z Amazon Web Services (AWS) na virtuální počítač Azure

Pokud vyhodnocujete virtuální počítače Azure pro hostování vašich úloh, můžete exportovat existující Amazon Web Services (AWS) EC2 instanci virtuálního počítače s Windows a potom do Azure nahrajte virtuální pevný disk (VHD). Po nahrání VHD můžete z virtuálního pevného disku vytvořit nový virtuální počítač v Azure. 

Tento článek popisuje přesunutí jednoho virtuálního počítače z AWS do Azure. Pokud chcete přesunout virtuální počítače z AWS do Azure ve velkém měřítku, přečtěte si téma [migrace virtuálních počítačů v Amazon Web Services (AWS) do Azure pomocí Azure Site Recovery](../../site-recovery/migrate-tutorial-aws-azure.md).

## <a name="prepare-the-vm"></a>Příprava virtuálního počítače 
 
Do Azure můžete nahrát jak generalizované, tak specializované virtuální pevné disky. Každý typ vyžaduje, abyste virtuální počítač připravili před exportem z AWS. 

- **Zobecněný virtuální pevný disk** : zobecněný virtuální pevný disk obsahuje všechny informace o vašem osobním účtu, které se odeberou pomocí nástroje Sysprep. Pokud máte v úmyslu použít VHD jako image k vytváření nových virtuálních počítačů z nástroje, měli byste: 
 
    * [Připravte si virtuální počítač s Windows](prepare-for-upload-vhd-image.md).  
    * Generalizujte virtuální počítač pomocí nástroje Sysprep.  

 
- **Specializovaný VHD** – specializovaný virtuální pevný disk udržuje uživatelské účty, aplikace a další stavová data z původního virtuálního počítače. Pokud máte v úmyslu použít virtuální pevný disk jako vytvoření nového virtuálního počítače, ujistěte se, že jsou splněné následující kroky.  
    * [Připravte si virtuální pevný disk Windows, který se má nahrát do Azure](prepare-for-upload-vhd-image.md). **Neprovádějte generalizaci** virtuálního počítače pomocí nástroje Sysprep. 
    * Odeberte všechny virtualizační nástroje a agenty hosta, které jsou nainstalované na virtuálním počítači (například nástroje VMware). 
    * Ujistěte se, že je virtuální počítač nakonfigurovaný tak, aby z protokolu DHCP vyčetl adresu IP a nastavení DNS. Tím se zajistí, že server při spuštění získá IP adresu v rámci virtuální sítě.  


## <a name="export-and-download-the-vhd"></a>Export a stažení virtuálního pevného disku 

Exportujte instanci EC2 do virtuálního pevného disku v kontejneru Amazon S3. Postupujte podle kroků v článku dokumentace k Amazon [Export instance jako virtuálního počítače pomocí importu/exportu virtuálního počítače](https://docs.aws.amazon.com/vm-import/latest/userguide/vmexport.html) a spuštěním příkazu [Create-instance-export-Task](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-instance-export-task.html) exportujte instanci EC2 do souboru VHD. 

Exportovaný soubor VHD je uložený v kontejneru Amazon S3, který zadáte. Základní syntaxe pro export virtuálního pevného disku je níže, stačí nahradit zástupný text \<brackets> pomocí vašich informací.

```
aws ec2 create-instance-export-task --instance-id <instanceID> --target-environment Microsoft \
  --export-to-s3-task DiskImageFormat=VHD,ContainerFormat=ova,S3Bucket=<bucket>,S3Prefix=<prefix>
```

Po exportu virtuálního pevného disku postupujte podle pokynů v tématu [Jak stáhnout objekt z kontejneru S3?](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/download-objects.html) ke stažení souboru VHD z intervalu S3. 

> [!IMPORTANT]
> Poplatky za přenos dat AWS poplatků za stažení virtuálního pevného disku. Další informace najdete v tématu [ceny služby Amazon S3](https://aws.amazon.com/s3/pricing/) .


## <a name="next-steps"></a>Další kroky

Teď můžete nahrát virtuální pevný disk do Azure a vytvořit nový virtuální počítač. 

- Pokud jste na svém zdroji spustili nástroj Sysprep, abyste ho **generalizoval** před exportem, přečtěte si téma [nahrání zobecněného virtuálního pevného disku a jeho použití k vytvoření nových virtuálních počítačů v Azure](upload-generalized-managed.md)
- Pokud jste před exportem nespustili Sysprep, virtuální pevný disk se považuje za **specializovaný**, viz [nahrání specializovaného virtuálního pevného disku do Azure a vytvoření nového virtuálního počítače](create-vm-specialized.md) .

 
