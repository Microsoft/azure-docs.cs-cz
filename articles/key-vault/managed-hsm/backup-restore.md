---
title: Úplné zálohování/obnovení a selektivní obnovení pro spravovaný modul HSM Azure
description: Tento dokument vysvětluje úplné zálohování, obnovení a selektivní obnovení.
services: key-vault
author: amitbapat
tags: azure-key-vault
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: e926dcd4b05d137c7927bdfe5221923d25d4670c
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/10/2021
ms.locfileid: "100093484"
---
# <a name="full-backup-and-restore"></a>Úplné zálohování a obnovení

> [!NOTE]
> Tato funkce je dostupná jenom pro typ prostředku spravovaný modul HSM.

Spravovaný HSM podporuje vytváření úplné zálohy celého obsahu modulu HSM včetně všech klíčů, verzí, atributů, značek a přiřazení rolí. Záloha je zašifrovaná pomocí kryptografických klíčů přidružených k doméně zabezpečení HSM.

Zálohování je operace roviny dat. Volající iniciující operaci zálohování musí mít oprávnění k provedení operace dataaction **společnosti Microsoft. managedHsm/Backup/Start/Action**.

K provedení úplného zálohování mají oprávnění pouze následující předdefinované role:
- Spravovaný správce HSM
- Spravované zálohování HSM

K provedení úplného zálohování je nutné zadat následující informace:
- Název HSM nebo adresa URL
- Název účtu úložiště
- Kontejner úložiště objektů BLOB v účtu úložiště
- Token SAS kontejneru úložiště s oprávněními `crdw`

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="full-backup"></a>Úplné zálohování

Zálohování je dlouhodobá operace, ale okamžitě vrátí ID úlohy. Můžete kontrolovat stav procesu zálohování pomocí tohoto ID úlohy. Proces zálohování vytvoří v určeném kontejneru složku s následujícím vzorem pojmenování **`mhsm-{HSM_NAME}-{YYYY}{MM}{DD}{HH}{mm}{SS}`** , kde HSM_NAME je název spravovaného modulu HSM, který se zálohuje, a rrrr, mm, DD, hh, mm, mm, SS je rok, měsíc, datum, hodina, minuta a sekunda v čase UTC při přijetí příkazu Backup.

I když probíhá zálohování, nemusí modul HARDWAROVÉho zabezpečení fungovat při plné propustnosti, protože některé oddíly HSM budou zaneprázdněny prováděním operace zálohování.

```azurecli-interactive
# time for 500 minutes later for SAS token expiry

end=$(date -u -d "500 minutes" '+%Y-%m-%dT%H:%MZ')

# Get storage account key

skey=$(az storage account keys list --query '[0].value' -o tsv --account-name mhsmdemobackup --subscription a1ba9aaa-b7f6-4a33-b038-6e64553a6c7b)

# Create a container

az storage container create --account-name  mhsmdemobackup --name mhsmdemobackupcontainer  --account-key $skey

# Generate a container sas token

sas=$(az storage container generate-sas -n mhsmdemobackupcontainer --account-name mhsmdemobackup --permissions crdw --expiry $end --account-key $skey -o tsv --subscription a1ba9aaa-b7f6-4a33-b038-6e64553a6c7b)

# Backup HSM

az keyvault backup start --hsm-name mhsmdemo2 --storage-account-name mhsmdemobackup --blob-container-name mhsmdemobackupcontainer --storage-container-SAS-token $sas --subscription 361da5d4-a47a-4c79-afdd-d66f684f4070
```

## <a name="full-restore"></a>Úplné obnovení

Úplné obnovení umožňuje zcela obnovit obsah modulu HSM pomocí předchozí zálohy, včetně všech klíčů, verzí, atributů, značek a přiřazení rolí. Vše, co je aktuálně Uloženo v modulu HSM, bude vymazáno a vrátí se do stejného stavu, ve kterém byla vytvořena zdrojová záloha.

> [!IMPORTANT]
> Úplné obnovení je velmi destruktivní a rušivý provoz. Proto je nutné provést úplnou zálohu během posledních 30 minut, než `restore` bude možné provést operaci.

Obnovení je operace roviny dat. Volající spouštějící operaci obnovení musí mít oprávnění k provedení operace dataaction **Microsoft. WebmanagedHsm/Restore/Start/Action**. Zdrojový modul HARDWAROVÉho zabezpečení, ve kterém se vytvořila záloha, a cílový modul HSM, ve kterém se provede obnovení, **musí** mít stejnou doménu zabezpečení. Přečtěte si další [informace o spravované doméně zabezpečení HSM](security-domain.md).

K provedení úplného obnovení musíte zadat následující informace:
- Název HSM nebo adresa URL
- Název účtu úložiště
- Kontejner objektů BLOB účtu úložiště
- Token SAS kontejneru úložiště s oprávněními `rl`
- Název složky kontejneru úložiště, do které se ukládá zdrojové zálohování

Obnovení je dlouhodobá operace, ale okamžitě vrátí ID úlohy. Stav procesu obnovení můžete zjistit pomocí ID této úlohy. Když proces obnovení probíhá, modul HARDWAROVÉho zabezpečení vstoupí do režimu obnovení a všechny příkazy roviny dat (kromě příkazu check Restore status) jsou zakázané.

```azurecli-interactive
#### time for 500 minutes later for SAS token expiry

end=$(date -u -d "500 minutes" '+%Y-%m-%dT%H:%MZ')

# Get storage account key

skey=$(az storage account keys list --query '[0].value' -o tsv --account-name mhsmdemobackup --subscription a1ba9aaa-b7f6-4a33-b038-6e64553a6c7b)

# Generate a container sas token

sas=$(az storage container generate-sas -n mhsmdemobackupcontainer --account-name mhsmdemobackup --permissions rl --expiry $end --account-key $skey -o tsv --subscription a1ba9aaa-b7f6-4a33-b038-6e64553a6c7b)
```

## <a name="restore-hsm"></a>Obnovit HSM

```
az keyvault restore start --hsm-name mhsmdemo2 --storage-account-name mhsmdemobackup --blob-container-name mhsmdemobackupcontainer --storage-container-SAS-token $sas --backup-folder mhsm-mhsmdemo-2020083120161860
```

## <a name="next-steps"></a>Další kroky
- Viz [Správa spravovaného HSM pomocí Azure CLI](key-management.md).
- Další informace o [spravované doméně zabezpečení HSM](security-domain.md)
