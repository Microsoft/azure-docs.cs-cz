---
title: Co dělat v případě výpadku služby Azure, který má vliv na spravovaný modul HSM-Azure Key Vault | Microsoft Docs
description: Přečtěte si, co dělat f, dochází k přerušení služeb Azure, které má vliv na spravovaný modul HSM.
services: key-vault
author: amitbapat
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: 08c1b415ac075429a9bc89098233fffb8c25b710
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2020
ms.locfileid: "94369252"
---
# <a name="managed-hsm-disaster-recovery"></a>Spravované zotavení po havárii modulu HSM

Můžete chtít vytvořit přesnou repliku svého modulu hardwarového zabezpečení (HSM), pokud dojde ke ztrátě původního nebo nedostupnosti z následujících důvodů:

- Byla odstraněna a pak vyprázdněna.
- Závažné selhání v oblasti vedlo ke zničení všech členských oddílů.

Instanci HSM můžete znovu vytvořit ve stejné nebo jiné oblasti, pokud máte následující:
- [Doména zabezpečení](security-domain.md) zdrojového modulu HSM.
- Privátní klíče (alespoň číslo kvora), které zašifrují doménu zabezpečení.
- Poslední úplné [zálohování](backup-restore.md) HSM ze zdrojového modulu HSM.

Postup zotavení po havárii najdete tady:

1. Vytvořte novou instanci HSM.
1. Aktivujte možnost obnovení domény zabezpečení. Nový pár klíčů RSA (klíč domény zabezpečení) bude vygenerován pro přenos domény zabezpečení a odeslán v odpovědi, který bude stažen jako SecurityDomainExchangeKey (veřejný klíč).
1. Vytvořte a pak nahrajte soubor "Security Domain Transfer File". Budete potřebovat privátní klíče, které zašifrují doménu zabezpečení. Privátní klíče se používají místně a nikdy se přenáší kdekoli v tomto procesu.
1. Proveďte zálohu nového modulu HSM. Před obnovením se vyžaduje záloha, a to i v případě, že modul HARDWAROVÉho zabezpečení je prázdný. Zálohování umožňuje snadnou vrácení se změnami.
1. Obnovit poslední zálohu HSM ze zdrojového modulu HSM

Obsah trezoru klíčů se replikuje v rámci oblasti a do sekundární oblasti minimálně 150 kilometrů, ale ve stejné geografické oblasti. Tato funkce udržuje vysokou odolnost klíčů a tajných kódů. Podrobnosti o dvojicích konkrétních oblastí najdete v dokumentu s [spárovanými](../../best-practices-availability-paired-regions.md) oblastmi Azure.

## <a name="create-a-new-managed-hsm"></a>Vytvoření nového spravovaného modulu HSM

Pomocí `az keyvault create` příkazu vytvořte spravovaný modul HSM. Tento skript má tři povinné parametry: název skupiny prostředků, název HSM a geografické umístění.

Pro vytvoření spravovaného prostředku HSM musíte zadat následující vstupy:

- Název modulu HSM.
- Skupina prostředků, do které se umístí do vašeho předplatného.
- Umístění Azure.
- Seznam počátečních správců.

Následující příklad vytvoří modul HARDWAROVÉho zabezpečení s názvem **ContosoMHSM** ve skupině prostředků  **ContosoResourceGroup** umístěný v umístění **východní USA 2** s **aktuálně přihlášeným uživatelem** jako jediný správce.

```azurecli-interactive
oid=$(az ad signed-in-user show --query objectId -o tsv)
az keyvault create --hsm-name "ContosoMHSM" --resource-group "ContosoResourceGroup" --location "East US 2" --administrators $oid
```

> [!NOTE]
> Příkaz Create může trvat několik minut. Po úspěšném vrácení budete připraveni aktivovat modul hardwarového zabezpečení (HSM).

Výstup tohoto příkazu zobrazuje vlastnosti spravovaného modulu HSM, který jste vytvořili. Dvě nejdůležitější vlastnosti jsou:

* **název** : v tomto příkladu je název ContosoMHSM. Tento název použijete pro jiné Key Vault příkazy.
* **hsmUri** : v tomto příkladu je identifikátor URI ' https://contosohsm.managedhsm.azure.net . ' Aplikace, které používají váš modul HARDWAROVÉho zabezpečení prostřednictvím REST API, musí používat tento identifikátor URI.

Váš účet Azure je teď autorizovaný k provádění operací s tímto spravovaným modulem HSM. Od tohoto konce je nikdo jiný autorizovaný.

## <a name="activate-the-security-domain-recovery-mode"></a>Aktivace režimu obnovení domény zabezpečení

V normálním procesu vytváření inicializujeme a stáhneme novou doménu zabezpečení v tomto okamžiku. Vzhledem k tomu, že provádíme postup zotavení po havárii, požádáme modul HARDWAROVÉho zabezpečení, aby zadal režim obnovení domény zabezpečení a stáhl bezpečnostní klíč domény zabezpečení. Klíč pro výměnu domény zabezpečení je veřejný klíč RSA, který se použije k zašifrování domény zabezpečení před odesláním do modulu HSM. Odpovídající soukromý klíč je chráněn v modulu HARDWAROVÉho zabezpečení, aby bylo možné bezpečně chránit obsah domény v průběhu přenosu.

```azurecli-interactive
az keyvault security-domain init-recovery --hsm-name ContosoMHSM2 --sd-exchange-key ContosoMHSM2-SDE.cer
```

## <a name="upload-security-domain-to-destination-hsm"></a>Odeslat doménu zabezpečení do cílového modulu HSM

Pro tento krok budete potřebovat následující:
- Klíč Exchange domény zabezpečení, který jste stáhli v předchozím kroku.
- Doména zabezpečení zdrojového modulu HSM.
- Minimální počet privátních klíčů, které se použily k šifrování domény zabezpečení, je minimálně kvorum.

`az keyvault security-domain upload`Příkaz provede následující operace:

- Dešifrujte doménu zabezpečení HSM zdrojového kódu pomocí privátních klíčů, které zadáte. 
- Vytvořte objekt BLOB pro odeslání domény zabezpečení zašifrovaný pomocí klíče pro výměnu domény zabezpečení, který jste stáhli v předchozím kroku, a potom
- Aby bylo možné dokončit obnovení domény zabezpečení, nahrajte do modulu HSM objekt BLOB pro nahrání domény zabezpečení.

V následujícím příkladu používáme doménu zabezpečení z **ContosoMHSM** , 2 odpovídajících privátních klíčů a nahrajeme ji do **ContosoMHSM2** , která čeká na přijetí domény zabezpečení. 

```azurecli-interactive
az keyvault security-domain upload --hsm-name ContosoMHSM2 --sd-exchange-key ContosoMHSM-SDE.cer --sd-file ContosoMHSM-SD.json --sd-wrapping-keys cert_0.key cert_1.key
```

Nyní mají zdrojový modul HSM (ContosoMHSM) i cílový modul HSM (ContosoMHSM2) stejnou doménu zabezpečení. Nyní můžeme obnovit úplnou zálohu ze zdrojového modulu HSM do cílového modulu HSM.

## <a name="create-a-backup-as-a-restore-point-of-your-new-hsm"></a>Vytvořte zálohu (jako bod obnovení) nového modulu HSM.

Před provedením úplného obnovení modulu HSM je vždycky vhodné provést úplnou zálohu, abyste měli bod obnovení v případě, že se při obnovení vyskytne něco špatného.

Pokud chcete vytvořit zálohu HSM, budete potřebovat následující:
- Účet úložiště, do kterého se bude ukládat záloha
- Kontejner úložiště objektů BLOB v tomto účtu úložiště, kde proces zálohování vytvoří novou složku pro uložení šifrované zálohy

Příkaz se používá `az keyvault backup` pro zálohování HSM v kontejneru úložiště **mhsmbackupcontainer** , který je v **ContosoBackup** účtu úložiště pro níže uvedený příklad. Vytvoříme token SAS, který vyprší za 30 minut a poskytne spravovanému modulu HSM, který zapíše zálohu.

```azurecli-interactive
end=$(date -u -d "30 minutes" '+%Y-%m-%dT%H:%MZ')
skey=$(az storage account keys list --query '[0].value' -o tsv --account-name ContosoBackup)
az storage container create --account-name  mhsmdemobackup --name mhsmbackupcontainer  --account-key $skey
sas=$(az storage container generate-sas -n mhsmbackupcontainer --account-name ContosoBackup --permissions crdw --expiry $end --account-key $skey -o tsv)
az keyvault backup start --hsm-name ContosoMHSM2 --storage-account-name ContosoBackup --blob-container-name mhsmdemobackupcontainer --storage-container-SAS-token $sas

```

## <a name="restore-backup-from-source-hsm"></a>Obnovit zálohu ze zdrojového modulu HSM

Pro tento krok potřebujete následující:

- Účet úložiště a kontejner objektů blob, ve kterých jsou uložené zálohy zdrojového modulu HSM.
- Název složky, ze které chcete zálohu obnovit. Pokud vytvoříte pravidelné zálohování, bude v tomto kontejneru mnoho složek.


```azurecli-interactive
end=$(date -u -d "30 minutes" '+%Y-%m-%dT%H:%MZ')
skey=$(az storage account keys list --query '[0].value' -o tsv --account-name ContosoBackup)
sas=$(az storage container generate-sas -n mhsmdemobackupcontainer --account-name ContosoBackup --permissions rl --expiry $end --account-key $skey -o tsv)
az keyvault restore start --hsm-name ContosoMHSM2 --storage-account-name ContosoBackup --blob-container-name mhsmdemobackupcontainer --storage-container-SAS-token $sas --backup-folder mhsm-ContosoMHSM-2020083120161860
```

Nyní jste dokončili úplný proces zotavení po havárii. Obsah zdrojového modulu HARDWAROVÉho zabezpečení při pořízení zálohy se zkopíruje do cílového modulu HSM, včetně všech klíčů, verzí, atributů, značek a přiřazení rolí.

## <a name="next-steps"></a>Další kroky

- Další informace o doméně zabezpečení najdete v tématu [o spravované doméně zabezpečení HSM](security-domain.md) .
- Sledovat [osvědčené postupy spravované HSM](best-practices.md)
