---
title: Rozšíření Windows snímku virtuálního počítače pro službu Azure Backup | Dokumentace Microsoftu
description: Přijmout žádosti konzistentní zálohování virtuálního počítače z Azure Backup pomocí rozšíření snímku virtuálního počítače
services: backup, virtual-machines-windows
documentationcenter: ''
author: trinadhk
manager: jeconnoc
ms.service: backup, virtual-machines-windows
ms.topic: article
ms.date: 12/17/2018
ms.author: trinadhk
ms.openlocfilehash: c41f609786620c8b90d484813d675efcd667d1e1
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2019
ms.locfileid: "55692626"
---
# <a name="vm-snapshot-windows-extension-for-azure-backup"></a>Rozšíření Windows snímku virtuálního počítače pro službu Azure Backup

Azure Backup podporuje pro zálohování úloh z místního cloudu a zálohování cloudových prostředků na trezor služby Recovery Services. Azure Backup používá rozšíření snímku virtuálního počítače se žádosti konzistentní zálohování virtuálního počítače Azure bez nutnosti vypnutí virtuálního počítače. Snímek virtuálního počítače rozšíření je publikována a podporuje Microsoft jako součást služby Azure Backup. Azure Backup nainstaluje rozšíření jako součást první naplánované zálohování aktivované příspěvek povolení zálohování. Tento dokument podrobně popisuje podporované platformy, konfigurace a možnosti nasazení pro rozšíření snímek virtuálního počítače.

## <a name="prerequisites"></a>Požadavky

### <a name="operating-system"></a>Operační systém
Seznam podporovaných operačních systémů, najdete v tématu [operační systémy podporované službou Azure Backup](../../backup/backup-azure-arm-vms-prepare.md#before-you-start)

### <a name="internet-connectivity"></a>Připojení k internetu

Snímek virtuálního počítače rozšíření vyžaduje, aby cílový virtuální počítač je připojený k Internetu, když jsme vytvořit zálohu virtuálního počítače.

## <a name="extension-schema"></a>Schéma rozšíření

Následující kód JSON ukazuje schéma pro rozšíření snímku virtuálního počítače. Rozšíření vyžaduje ID úlohy - Určuje záložní úlohu, která aktivuje snímků na virtuálním počítači, identifikátor uri objektu blob stavu - níž je zapsána stav operace vytvoření snímku, plánovaný čas zahájení snímku, protokoly objektů blob uri – kde snímku odpovídající protokoly úloh jsou určeny, objstr reprezentace disky virtuálních počítačů a metadata.  Protože tato nastavení mají být považována za citlivá data, by měl být uložený v chráněném nastavení konfigurace. Data Azure nastavení rozšíření chráněný virtuální počítač je zašifrovaný a dešifrovat jenom na cílovém virtuálním počítači. Všimněte si, že tato nastavení se doporučuje předat ze služby Azure Backup pouze jako součást úlohy zálohování.

```json
{
  "type": "extensions",
  "name": "VMSnapshot",
  "location":"<myLocation>",
  "properties": {
    "publisher": "Microsoft.RecoveryServices",
    "type": "VMSnapshot",
    "typeHandlerVersion": "1.9",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "locale":"<location>",
      "taskId":"<taskId used by Azure Backup service to communicate with extension>",
      "commandToExecute": "snapshot",
      "commandStartTimeUTCTicks": "<scheduled start time of the snapshot task>",
      "vmType": "microsoft.compute/virtualmachines"
    },
    "protectedSettings": {
      "objectStr": "<blob SAS uri representation of VM sent by Azure Backup service to extension>",
      "logsBlobUri": "<blob uri where logs of command execution by extension are written to>",
      "statusBlobUri": "<blob uri where status of the command executed by extension is written>"
    }
  }
}
```

### <a name="property-values"></a>Hodnoty vlastností

| Název | Hodnota / příklad | Typ dat |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| taskId | e07354cf-041e-4370-929f-25a319ce8933_1 | řetězec |
| commandStartTimeUTCTicks | 6.36458E + 17 | řetězec |
| národní prostředí | En-us | řetězec |
| objectStr | Kódování sas uri pole-"blobSASUri": ["https:\/\/sopattna5365.blob.core.windows.net\/virtuální pevné disky\/vmwin1404ltsc201652903941.vhd? sv = 2014-02-14 & sr = b & sig = TywkROXL1zvhXcLujtCut8g3jTpgbE6JpSWRLZxAdtA % 3D & st = 2017-11-09T14 % 3A23 % 3A28Z & se = 2017-11-09T17 % 3A38 % 3A28Z & sp = rw "," protokol https:\/\/sopattna8461.blob.core.windows.net\/virtuálníchpevnýchdisků\/vmwin1404ltsc. 20160629 122418.vhd? sv = 2014-02-14 & sr = b & sig = 5S0A6YDWvVwqPAkzWXVy % 2BS 2FqMwzFMbamT5upwx05v8Q % 3D & st = 2017-11-09T14 % 3A23 % 3A28Z & se = 2017-11-09T17 % 3A38 % 3A28Z & sp = rw "," protokol https:\/ \/ sopattna8461.BLOB.Core.Windows.NET\/bootdiagnostics-vmwintu1-deb58392-ed5e-48be-9228-ff681b0cd3ee\/vmubuntu1404ltsc. 20160629 122541.vhd? sv = 2014-02-14 & sr = b & sig = X0Me2djByksBBMVXMGIUrcycvhQSfjYvqKLeRA7nBD4 % 3D & st = 2017-11-09T14 % 3A23 % 3A28Z & se = 2017-11-09T17 % 3A38 % 3A28Z & sp = rw "," protokol https:\/\/sopattna5365.blob.core.windows.net\/virtuálníchpevnýchdisků\/vmwin1404ltsc. 20160701 163922.vhd? sv = 2014-02-14 & sr = b & sig = oXvtK2IXCNqWv7fpjc7TAzFDpc1GoXtT7r % 2BC 2BNIAork % 3D & st = 2017-11-09T14 % 3A23 % 3A28Z & se = 2017-11-09T17 % 3A38 % 3A28Z & sp = rw "," protokol https:\/ \/ sopattna5365.BLOB.Core.Windows.NET\/virtuální pevné disky\/vmwin1404ltsc. 20170705 124311.vhd? sv = 2014-02-14 & sr = b & sig = ZUM9d28Mvvm % 2FfrhJ71TFZh0Ni90m38bBs3zMl 2FQ9rs0 % 3D & st = 2017-11-09T14 % 3A23 % 3A28Z & se = 2017-11-09T17 % 3A38 % 3A28Z & sp = rw "] | řetězec |
| logsBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Logs.txt?sv=2014-02-14&sr=b&sig=DbwYhwfeAC5YJzISgxoKk%2FEWQq2AO1vS1E0rDW%2FlsBw%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | řetězec |
| statusBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Status.txt?sv=2014-02-14&sr=b&sig=96RZBpTKCjmV7QFeXm5IduB%2FILktwGbLwbWg6Ih96Ao%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | řetězec |



## <a name="template-deployment"></a>Nasazení šablon

Rozšíření virtuálního počítače Azure je možné nasadit s využitím šablon Azure Resource Manageru. Doporučený způsob, jak přidat rozšíření snímku virtuálního počítače na virtuální počítač je však tím, že zálohování na virtuálním počítači. Toho lze dosáhnout pomocí šablony Resource Manageru.  Ukázka šablony Resource Manageru se povolí zálohování na virtuálním počítači můžete najít na [Galerie Azure rychlý Start](https://azure.microsoft.com/resources/templates/101-recovery-services-backup-vms/).


## <a name="azure-cli-deployment"></a>Nasazení v Azure CLI

Azure CLI slouží k povolení zálohování na virtuálním počítači. Příspěvek povolit zálohování, první naplánovaná úloha zálohování nainstaluje rozšíření snímku virtuálního počítače na virtuálním počítači.

```azurecli
az backup protection enable-for-vm \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --vm myVM \
    --policy-name DefaultPolicy
```

## <a name="troubleshoot-and-support"></a>Řešení potíží a podpora

### <a name="troubleshoot"></a>Řešení potíží

Data o stavu nasazení rozšíření se dají načíst z portálu Azure portal a pomocí rozhraní příkazového řádku Azure. Pokud chcete zobrazit stav nasazení rozšíření pro daný virtuální počítač, spusťte následující příkaz pomocí Azure CLI.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Rozšíření provádění výstup je zaznamenán do následujícího souboru:

```
C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot
```

### <a name="error-codes-and-their-meanings"></a>Kódy chyb a jejich význam

Informace o odstraňování potíží najdete v [Průvodce odstraňováním potíží se zálohováním virtuálního počítače Azure](../../backup/backup-azure-vms-troubleshoot.md).

### <a name="support"></a>Podpora

Pokud potřebujete další nápovědu v libovolném bodě v tomto článku, můžete se obrátit odborníků na Azure na [fóra MSDN Azure a Stack Overflow](https://azure.microsoft.com/support/forums/). Alternativně můžete soubor incidentu podpory Azure. Přejděte [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte získat podporu. Informace o používání podpory Azure najdete v článku [nejčastější dotazy k podpoře Microsoft Azure](https://azure.microsoft.com/support/faq/).
