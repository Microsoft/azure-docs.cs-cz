---
title: Rozšíření Windows snímků virtuálních počítačů pro Azure Backup | Microsoft Docs
description: Proveďte zálohování s konzistentními aplikací virtuálního počítače z Azure Backup pomocí rozšíření snímek virtuálního počítače
services: backup, virtual-machines-windows
documentationcenter: ''
author: trinadhk
manager: jeconnoc
editor: ''
ms.assetid: 57759670-0baa-44db-ae14-8cdc00d3a906
ms.service: backup, virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 3/26/2018
ms.author: trinadhk
ms.openlocfilehash: 8426a2472a28cf287dfe574cb80da56108394ae8
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="vm-snapshot-windows-extension-for-azure-backup"></a>Rozšíření Windows snímků virtuálních počítačů pro zálohování Azure

## <a name="overview"></a>Přehled

Zálohování Azure poskytuje podporu pro zálohování úloh z místního do cloudu a zálohování cloudové prostředky do trezoru služeb zotavení. Azure Backup používá rozšíření snímek virtuálního počítače pro aplikaci konzistentní zálohování virtuálního počítače Azure bez nutnosti vypnutí virtuálního počítače. Rozšíření snímku virtuálního počítače je publikována a společnost Microsoft podporuje jako součást služby zálohování Azure. Azure Backup nainstaluje jako součást první naplánované zálohování spouštěná post povolení zálohování rozšíření. Tento dokument podrobně popisuje podporované platformy, konfigurace a možnosti nasazení pro rozšíření snímku virtuálního počítače.

## <a name="prerequisites"></a>Požadavky

### <a name="operating-system"></a>Operační systém
Seznam podporovaných operačních systémů, najdete v části [operační systémy podporované nástrojem Azure Backup](../../backup/backup-azure-arm-vms-prepare.md#supported-operating-systems-for-backup)

### <a name="internet-connectivity"></a>Připojení k internetu

Rozšíření snímku virtuálního počítače vyžaduje, aby cílový virtuální počítač je připojený k Internetu, když jsme proveďte zálohu virtuálního počítače.

## <a name="extension-schema"></a>Schéma rozšíření

Následujícím kódu JSON znázorňuje schéma pro rozšíření snímek virtuálního počítače. Rozšíření vyžaduje ID úkolu – to identifikuje zálohování úlohu, která spustí snímku na virtuálním počítači, identifikátor uri objektu blob stavu - umístění, kam je zapisován stav operace snímku, naplánovaný čas zahájení snímku, protokoly blob uri – kde protokoly odpovídající snímku úloh jsou určeny, objstr reprezentace disky virtuálních počítačů a metadata.  Protože tato nastavení by měl být považován za citlivá data, by měly být uložené v chráněném nastavení konfigurace. Data Azure nastavení rozšíření chráněný virtuální počítač je zašifrovaná a dešifrovat jenom na cílový virtuální počítač. Všimněte si, že tato nastavení se doporučuje možné předat z služby zálohování Azure jenom jako součást úlohy zálohování.

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
      "objectStr": "<blob SAS uri represenattion of VM sent by Azure Backup service to extension>",
      "logsBlobUri": "<blob uri where logs of command execution by extension are written to>",
      "statusBlobUri": "<blob uri where status of the command executed by extension is written>"
    }
  }
}
```

### <a name="property-values"></a>Hodnoty vlastností

| Název | Hodnota nebo příklad | Typ dat |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | datum |
| taskId | e07354cf-041e-4370-929f-25a319ce8933_1 | řetězec |
| commandStartTimeUTCTicks | 6.36458E + 17 | řetězec |
| Národní prostředí | en-us | řetězec |
| objectStr | Kódování sas uri pole-"blobSASUri": ["https:\/\/sopattna5365.blob.core.windows.net\/virtuální pevné disky\/vmwin1404ltsc201652903941.vhd? sv = 2014-02-14 & sr = b & sig = TywkROXL1zvhXcLujtCut8g3jTpgbE6JpSWRLZxAdtA % 3D & st = 2017. 11 09T14 % 3A23 % 3A28Z & oužít = 2017. 11 09T17 % 3A38 % 3A28Z & sp = rw "," https:\/\/sopattna8461.blob.core.windows.net\/virtuálníchpevnýchdisků\/vmwin1404ltsc. 20160629 122418.vhd? sv = 2014-02-14 & sr = b & sig = 5S0A6YDWvVwqPAkzWXVy % 2BS % 2FqMwzFMbamT5upwx05v8Q % 3D & st = 2017. 11 09T14 % 3A23 % 3A28Z & oužít = 2017. 11 09T17 % 3A38 % 3A28Z & sp = rw "," https:\/ \/ sopattna8461.BLOB.Core.Windows.NET\/bootdiagnostics-vmwintu1-deb58392-ed5e-48be-9228-ff681b0cd3ee\/vmubuntu1404ltsc. 20160629 122541.vhd? sv = 2014-02-14 & sr = b & sig = X0Me2djByksBBMVXMGIUrcycvhQSfjYvqKLeRA7nBD4 % 3D & st = 2017. 11 09T14 % 3A23 % 3A28Z & oužít = 2017. 11 09T17 % 3A38 % 3A28Z & sp = rw "," https:\/\/sopattna5365.blob.core.windows.net\/virtuálníchpevnýchdisků\/vmwin1404ltsc. 20160701 163922.vhd? sv = 2014-02-14 & sr = b & sig = oXvtK2IXCNqWv7fpjc7TAzFDpc1GoXtT7r % 2BC % 2BNIAork % 3D & st = 2017. 11 09T14 % 3A23 % 3A28Z & oužít = 2017. 11 09T17 % 3A38 % 3A28Z & sp = rw "," https:\/ \/ sopattna5365.BLOB.Core.Windows.NET\/virtuální pevné disky\/vmwin1404ltsc. 20170705 124311.vhd? sv = 2014-02-14 & sr = b & sig = ZUM9d28Mvvm % 2FfrhJ71TFZh0Ni90m38bBs3zMl % 2FQ9rs0 % 3D & st = 2017. 11 09T14 % 3A23 % 3A28Z & se = 2017. 11 09T17 % 3A38 % 3A28Z & sp = rw "] | řetězec |
| logsBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Logs.txt?sv=2014-02-14&sr=b&sig=DbwYhwfeAC5YJzISgxoKk%2FEWQq2AO1vS1E0rDW%2FlsBw%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | řetězec |
| statusBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Status.txt?sv=2014-02-14&sr=b&sig=96RZBpTKCjmV7QFeXm5IduB%2FILktwGbLwbWg6Ih96Ao%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | řetězec |



## <a name="template-deployment"></a>Nasazení šablon

Rozšíření virtuálního počítače Azure se dá nasadit pomocí šablon Azure Resource Manager. Doporučený způsob přidání rozšíření snímek virtuálního počítače na virtuální počítač je však povolením zálohování na virtuálním počítači. Toho lze dosáhnout pomocí šablony Resource Manageru.  Vzorové šablony Resource Manageru, umožňující zálohování na virtuálním počítači najdete na [Azure rychlý Start Galerie](https://azure.microsoft.com/resources/templates/101-recovery-services-backup-vms/).


## <a name="azure-cli-deployment"></a>Nasazení Azure CLI

Rozhraní příkazového řádku Azure slouží k povolení zálohování na virtuálním počítači. Povolit zálohování POST, nainstaluje první naplánované úlohy zálohování rozšíření snímek virtuálního počítače na virtuálním počítači.

```azurecli
az backup protection enable-for-vm \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --vm myVM \
    --policy-name DefaultPolicy
```

## <a name="troubleshoot-and-support"></a>Řešení potíží a podpora

### <a name="troubleshoot"></a>Řešení potíží

Z portálu Azure a pomocí rozhraní příkazového řádku Azure je možné načíst data o stavu nasazení rozšíření. Pokud chcete zobrazit stav nasazení rozšíření pro daný virtuální počítač, spusťte následující příkaz pomocí rozhraní příkazového řádku Azure.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Výstupu spuštění rozšíření je zaznamenána do následujícího souboru:

```
C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot
```

### <a name="error-codes-and-their-meanings"></a>Kódy chyb a jejich významů

Informace o odstraňování potíží naleznete na [zálohování virtuálního počítače Azure Průvodce odstraňováním potíží s](../../backup/backup-azure-vms-troubleshoot.md).

### <a name="support"></a>Podpora

Pokud potřebujete další pomoc v libovolném bodě v tomto článku, obraťte se na Azure odborníky na [fórech MSDN Azure a Stack Overflow](https://azure.microsoft.com/support/forums/). Alternativně můžete soubor incidentu podpory Azure. Přejděte na [podporu Azure lokality](https://azure.microsoft.com/support/options/) a vyberte Get podpory. Informace o používání Azure podporovat, najdete v tématu [podporu Microsoft Azure – nejčastější dotazy](https://azure.microsoft.com/support/faq/).
