---
title: Rozšíření pro systém Linux snímku virtuálního počítače pro Azure Backup
description: Proveďte zálohu virtuálního počítače konzistentní s aplikací z Azure Backup pomocí rozšíření VM Snapshot Linux.
services: backup, virtual-machines-linux
documentationcenter: ''
author: trinadhkotturu
manager: gwallace
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.date: 12/17/2018
ms.author: trinadhk
ms.openlocfilehash: 9fe50388e2fc21c28ca1f04035cc7c725e4f87ec
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91275043"
---
# <a name="vm-snapshot-linux-extension-for-azure-backup"></a>Rozšíření pro systém Linux snímku virtuálního počítače pro Azure Backup



Azure Backup poskytuje podporu pro zálohování úloh z místního prostředí do cloudu a zálohování cloudových prostředků do trezoru Recovery Services. Azure Backup používá rozšíření snímku virtuálního počítače k pořízení zálohy virtuálního počítače Azure konzistentní vzhledem k aplikacím, aniž by bylo nutné virtuální počítač vypnout. Rozšíření pro Linux snímků virtuálního počítače je publikované a podporované Microsoftem jako součást služby Azure Backup. Azure Backup nainstaluje rozšíření jako součást prvního naplánovaného zálohování aktivovaného po povolení zálohování. Tento dokument podrobně popisuje podporované platformy, konfigurace a možnosti nasazení pro rozšíření snímku virtuálního počítače.

Rozšíření VMSnapshot se zobrazí v Azure Portal pouze pro nespravované virtuální počítače.

## <a name="prerequisites"></a>Požadavky

### <a name="operating-system"></a>Operační systém
Seznam podporovaných operačních systémů najdete v tématu operační systémy, které [podporuje Azure Backup](../../backup/backup-azure-arm-vms-prepare.md#before-you-start)

## <a name="extension-schema"></a>Schéma rozšíření

Následující JSON zobrazuje schéma pro rozšíření snímku virtuálního počítače. Přípona vyžaduje ID úlohy – identifikuje úlohu zálohování, která aktivovala snímek na virtuálním počítači, identifikátor ID objektu BLOB stavu, ve kterém je napsaný stav operace snímku, plánovaný počáteční čas snímku, identifikátor URI objektu blob, kde jsou zapsané protokoly, které odpovídají úloze snímku, objstr disky virtuálních počítačů a meta data.  Vzhledem k tomu, že tato nastavení by měla být považována za citlivá data, měla by být uložena v konfiguraci chráněného nastavení. Data nastavení chráněná rozšířením virtuálního počítače Azure jsou šifrovaná a v cílovém virtuálním počítači se dešifrují jenom. Upozorňujeme, že tato nastavení se doporučuje předávat z Azure Backup služby pouze v rámci úlohy zálohování.

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

| Name | Hodnota/příklad | Typ dat |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| taskId | e07354cf-041e-4370-929f-25a319ce8933_1 | řetězec |
| commandStartTimeUTCTicks | 6.36458 e + 17 | řetězec |
| locale | cs-cz | řetězec |
| objectStr | Encoding of sas uri array- "blobSASUri": ["https: \/ \/ sopattna5365.blob.core.windows.net \/ vhds \/ vmubuntu1404ltsc201652903941.vhd?sv=2014-02-14&sr=b&sig=TywkROXL1zvhXcLujtCut8g3jTpgbE6JpSWRLZxAdtA%3D&st=2017-11-09T14%3A23%3A28Z&se=2017-11-09T17%3A38%3A28Z&sp=rw", "https: \/ \/ sopattna8461.blob.core.windows.net \/ vhds \/ vmubuntu1404ltsc-20160629-122418.vhd?sv=2014-02-14&sr=b&sig=5S0A6YDWvVwqPAkzWXVy%2BS%2FqMwzFMbamT5upwx05v8Q%3D&st=2017-11-09T14%3A23%3A28Z&se=2017-11-09T17%3A38%3A28Z&sp=rw", "https: \/ \/ sopattna8461.blob.core.windows.net \/ bootdiagnostics-vmubuntu1-deb58392-ed5e-48be-9228-ff681b0cd3ee \/ vmubuntu1404ltsc-20160629-122541.vhd?sv=2014-02-14&sr=b&sig=X0Me2djByksBBMVXMGIUrcycvhQSfjYvqKLeRA7nBD4%3D&st=2017-11-09T14%3A23%3A28Z&se=2017-11-09T17%3A38%3A28Z&sp=rw", "https: \/ \/ sopattna5365.blob.core.windows.net \/ vhds \/ vmubuntu1404ltsc-20160701-163922.vhd?sv=2014-02-14&sr=b&sig=oXvtK2IXCNqWv7fpjc7TAzFDpc1GoXtT7r%2BC%2BNIAork%3D&st=2017-11-09T14%3A23%3A28Z&se=2017-11-09T17%3A38%3A28Z&sp=rw", "https: \/ \/ sopattna5365.blob.core.windows.net \/ vhds \/ vmubuntu1404ltsc-20170705-124311.vhd?sv=2014-02-14&sr=b&sig=ZUM9d28Mvvm%2FfrhJ71TFZh0Ni90m38bBs3zMl%2FQ9rs0%3D&st=2017-11-09T14%3A23%3A28Z&se=2017-11-09T17%3A38%3A28Z&sp=rw"] | řetězec |
| logsBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Logs.txt?sv=2014-02-14&sr=b&sig=DbwYhwfeAC5YJzISgxoKk%2FEWQq2AO1vS1E0rDW%2FlsBw%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | řetězec |
| statusBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Status.txt?sv=2014-02-14&sr=b&sig=96RZBpTKCjmV7QFeXm5IduB%2FILktwGbLwbWg6Ih96Ao%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | řetězec |



## <a name="template-deployment"></a>Nasazení šablon

Rozšíření virtuálních počítačů Azure je možné nasadit pomocí šablon Azure Resource Manager. Doporučený způsob přidání rozšíření snímku virtuálního počítače na virtuální počítač je ale povolením zálohování na virtuálním počítači. To je možné dosáhnout pomocí Správce prostředků šablony.  Ukázková Správce prostředků šablona umožňující zálohování na virtuálním počítači najdete v [galerii Azure Rychlé zprovoznění](https://azure.microsoft.com/resources/templates/101-recovery-services-backup-vms/).


## <a name="azure-cli-deployment"></a>Nasazení Azure CLI

Azure CLI je možné použít k povolení zálohování na virtuálním počítači. Po dokončení zálohování se naplánuje první úloha zálohování, která na virtuálním počítači nainstaluje rozšíření snímku virtuálního počítače.

```azurecli
az backup protection enable-for-vm \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --vm myVM \
    --policy-name DefaultPolicy
```

## <a name="troubleshoot-and-support"></a>Řešení potíží a podpora

### <a name="troubleshoot"></a>Řešení potíží

Data o stavu nasazení rozšíření lze načíst z Azure Portal a pomocí rozhraní příkazového řádku Azure CLI. Pokud chcete zobrazit stav nasazení rozšíření pro daný virtuální počítač, spusťte následující příkaz pomocí Azure CLI.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Výstup spuštění rozšíření se zaznamená do následujícího souboru:

```
/var/log/waagent.log
```

### <a name="error-codes-and-their-meanings"></a>Kódy chyb a jejich významy

Informace o řešení potíží najdete v [Průvodci odstraňováním potíží se zálohováním virtuálních počítačů Azure](../../backup/backup-azure-vms-troubleshoot.md).

### <a name="support"></a>Podpora

Pokud potřebujete další podrobnější informace v jakémkoli bodě tohoto článku, můžete kontaktovat odborníky na Azure na [webu MSDN Azure a Stack Overflow fóra](https://azure.microsoft.com/support/forums/). Případně můžete zasouborovat incident podpory Azure. Přejít na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte získat podporu. Informace o použití podpory Azure najdete v tématu [Nejčastější dotazy k podpoře pro Microsoft Azure](https://azure.microsoft.com/support/faq/).
