---
title: Rozšíření Windows s snímek virtuálního počítače pro azure backup
description: Provedení konzistentního zálohování virtuálního počítače z Azure Backup pomocí rozšíření snímků virtuálního počítače
services: backup, virtual-machines-windows
documentationcenter: ''
author: trinadhkotturu
manager: gwallace
ms.service: virtual-machines-windows
ms.topic: article
ms.date: 12/17/2018
ms.author: trinadhk
ms.openlocfilehash: 5ee3b97eb835322a0ad6c8a69c20776a243713fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79415108"
---
# <a name="vm-snapshot-windows-extension-for-azure-backup"></a>Rozšíření Windows s snímek virtuálního počítače pro azure backup

Azure Backup poskytuje podporu pro zálohování úloh z místního do cloudu a zálohování cloudových prostředků do trezoru služby Recovery Services. Azure Backup používá rozšíření snapshot v v doručování k provedení aplikace konzistentní zálohování virtuálního počítače Azure bez nutnosti vypnutí virtuálního počítače. Rozšíření Snímek virtuálního počítače je publikováno a podporováno společností Microsoft jako součást služby Azure Backup. Azure Backup nainstaluje rozšíření jako součást prvního naplánovaného zálohovat aktivované ho povislého příspěvku umožňujícího zálohování. Tento dokument podrobně popisuje podporované platformy, konfigurace a možnosti nasazení pro rozšíření Snímek virtuálního počítače.

Rozšíření VMSnapshot se zobrazí na webu Azure Portal jenom pro nespravované virtuální počítače.

## <a name="prerequisites"></a>Požadavky

### <a name="operating-system"></a>Operační systém
Seznam podporovaných operačních systémů najdete v [části Operační systémy podporované službou Azure Backup.](../../backup/backup-azure-arm-vms-prepare.md#before-you-start)

## <a name="extension-schema"></a>Schéma rozšíření

Následující JSON ukazuje schéma pro rozšíření snímku virtuálního počítače. Rozšíření vyžaduje ID úlohy – to identifikuje úlohu zálohování, která spustila snímek na virtuálním počítači, stav objektu BLOB uri - kde je zapsán stav operace snímku, naplánované spuštění čas snímku, protokoly objektu blob uri - kde protokoly odpovídající snímek úlohy jsou zapsány, objstr- reprezentace disků virtuálních počítačů a meta data.  Vzhledem k tomu, že tato nastavení by měla být považována za citlivá data, měla by být uložena v konfiguraci chráněného nastavení. Data nastavení chráněné rozšířením virtuálního počítače Azure jsou šifrovaná a dešifrovaná jenom na cílovém virtuálním počítači. Všimněte si, že tato nastavení se doporučuje předat ze služby Azure Backup pouze jako součást úlohy zálohování.

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

| Name (Název) | Hodnota / Příklad | Typ dat |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| Taskid | e07354cf-041e-4370-929f-25a319ce8933_1 | řetězec |
| příkazStartTimeUTCTicks | 6.36458E+17 | řetězec |
| locale | cs-cz | řetězec |
| objectStr | Kódování sas uri array- "blobSASUri":\/\/["https: sopattna5365.blob.core.windows.net\/vhds\/vmwin1404ltsc201652903941.vhd?sv=2014-02-14&sr=b&sig=TywkROXL1zvhXcLuj tCut8g3jTpgbE6JpSWRLZxAdtA%3D&st=2017-11-09T14%3A23%3A28Z&se=2017-11-09T17%3A38%3A28Z&sp=rw", "https:\/\/sopattna8461.blob.core.windows.net\/vhds\/vmwin1404ltsc-20160629-122418.vhd?sv=2014-02-14&sr=b&sig= 5S0A6YDWvVwqPAkzWXVy%2BS%2FqMWzFMbamT5upwx05v8Q%3D&st=2017-11-09T14%3A23%3A28Z&se=2017-11-09T17%3A38%3A28Z&"https:\/\/sopattna8461.blob.core.windows.net\/bootdiagnostics-vmwintu1-deb58392-ed5e-48be-9228-ff681b0cd3ee\/v mubuntu1404ltsc-20160629-122541.vhd?sv=2014-02-14&sr=b&sig=X0Me2djByksBBMVXMGIUrcycvhQSfj YvqKLeRA7nBD4%3D&st=2017-11-09T14%3A23%3A28Z&se=2017-11-09T17%3A38%3A28Z&sp=rw", " https:\/\/\/\/sopattna5365.blob.core.windows.net vhds vmwin1404ltsc-20160701-163922.vhd?sv=2014-02-14&sr=b&sig=oXvtK2IXCNqWv7fpjc7TAzFDpc 1GoXtT7r%2BC%2BNIAork%3D&st=2017-11-09T14%3A23%3A28Z&se=2017-11-09T17%3A38%3A28Z&sp=rw", "https:\/\/sopattna5365.blob.core.windows.net\/vhds\/vmwin1404ltsc-20170705-124311.vhd?sv=2014-02-14&sr=b&sig=ZUM9d28Mvvm% 2FfrhJ71TFZh0Ni90m38bBs3zMl%2FQ9rs0%3D&st=2017-11-09T14%3A23%3A28Z&se=2017-11-09T17%3A38%3A28Z&sp=rw] | řetězec |
| logsBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Logs.txt?sv=2014-02-14&sr=b&sig=DbwYhwfeAC5YJzISgxoKk%2FEWQq2AO1vS1E0rDW%2FlsBw%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | řetězec |
| stavBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Status.txt?sv=2014-02-14&sr=b&sig=96RZBpTKCjmV7QFeXm5IduB%2FILktwGbLwbWg6Ih96Ao%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | řetězec |



## <a name="template-deployment"></a>Nasazení šablon

Rozšíření virtuálních počítačů Azure se můžou nasadit pomocí šablon Azure Resource Manageru. Doporučený způsob přidání rozšíření snímku virtuálního počítače do virtuálního počítače je však povolení zálohování ve virtuálním počítači. Toho lze dosáhnout prostřednictvím šablony Správce prostředků.  Ukázkovou šablonu Správce prostředků, která umožňuje zálohování na virtuálním počítači, najdete v [galerii Rychlých startů Azure](https://azure.microsoft.com/resources/templates/101-recovery-services-backup-vms/).


## <a name="azure-cli-deployment"></a>Nasazení azure cli

Rozhraní příkazového řádku Azure lze použít k povolení zálohování na virtuálním počítači. Povolit zálohování, první naplánované zálohování úlohy nainstaluje rozšíření snímek virtuálního počítače na virtuální počítač.

```azurecli
az backup protection enable-for-vm \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --vm myVM \
    --policy-name DefaultPolicy
```

## <a name="troubleshoot-and-support"></a>Poradce při potížích a podpora

### <a name="troubleshoot"></a>Řešení potíží

Data o stavu nasazení rozšíření lze načíst z portálu Azure a pomocí azure cli. Chcete-li zobrazit stav nasazení rozšíření pro daný virtuální počítač, spusťte následující příkaz pomocí příkazu Příkaz příkazu Azure.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Výstup spuštění rozšíření je zaznamenán do následujícího souboru:

```
C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot
```

### <a name="error-codes-and-their-meanings"></a>Chybové kódy a jejich význam

Informace o řešení potíží najdete v [průvodci odstraňováním potíží se zálohováním virtuálních počítačů Azure](../../backup/backup-azure-vms-troubleshoot.md).

### <a name="support"></a>Podpora

Pokud potřebujete další pomoc v libovolném bodě v tomto článku, můžete kontaktovat odborníky Azure na [Fóra MSDN Azure a přetečení zásobníku](https://azure.microsoft.com/support/forums/). Případně můžete soubor incidentu podpory Azure. Přejděte na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte Získat podporu. Informace o používání podpory Azure načtete v [nejčastějších dotazech k podpoře Microsoft Azure](https://azure.microsoft.com/support/faq/).
