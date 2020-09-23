---
title: Správa klíčů ve spravovaném modulu HSM-Azure Key Vault | Microsoft Docs
description: Pomocí tohoto článku můžete spravovat klíče ve spravovaném modulu HSM.
services: key-vault
author: amitbapat
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: 9353750fdbbb52aff60fc41b7fd028ec4c5f0ec8
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "91000664"
---
# <a name="manage-a-managed-hsm-using-the-azure-cli"></a>Správa spravovaného modulu HSM pomocí Azure CLI

> [!NOTE]
> Key Vault podporuje dva typy prostředků: trezory a spravované HSM. Tento článek se týká **spravovaného modulu HSM**. Pokud se chcete dozvědět, jak spravovat trezor, přečtěte si téma [správa Key Vault pomocí rozhraní příkazového řádku Azure CLI](../general/manage-with-cli2.md).

Přehled spravovaného modulu HSM najdete v tématu [co je spravovaný modul HSM?](overview.md) .

Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Požadavky

K dokončení kroků v tomto článku musíte mít následující položky:

* Předplatné Microsoft Azure. Pokud žádný nemáte, můžete si zaregistrovat [bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial).
* Verze Azure CLI 2.12.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI]( /cli/azure/install-azure-cli).
* Spravovaný modul HSM v rámci vašeho předplatného. Informace najdete v tématu [rychlý Start: zřízení a aktivace spravovaného modulu HSM pomocí Azure CLI](quick-create-cli.md) , který umožňuje zřídit a aktivovat spravovaný modul HSM.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Pokud se chcete přihlásit k Azure pomocí rozhraní příkazového řádku, můžete zadat:

```azurecli
az login
```

Další informace o možnostech přihlášení prostřednictvím rozhraní příkazového řádku najdete v tématu [přihlášení pomocí Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest&preserve-view=true) .

> [!NOTE]
> Všechny níže uvedené příkazy zobrazují dvě metody použití. Jeden s parametrem **--HSM-Name** a **--Name** (pro název klíče) a další parametr **--ID** , kde můžete zadat celou adresu URL včetně názvu klíče, kde je to vhodné. Druhá metoda je užitečná v případě, že volající (uživatel nebo aplikace) nemá přístup pro čtení na rovině ovládacího prvku a pouze omezený přístup k rovině dat.

## <a name="create-an-hsm-key"></a>Vytvoření klíče HSM

Pomocí `az keyvault key create` příkazu vytvořte klíč.

### <a name="create-an-rsa-key"></a>Vytvořte klíč RSA

Následující příklad ukazuje, jak vytvořit 3070 klíč **RSA** , který se bude používat jenom pro **WrapKey, operace unwrapKey** (--OPS). 


```azurecli-interactive
az keyvault key create --hsm-name ContosoMHSM --name myrsakey --ops wrapKey unwrapKey --kty RSA-HSM --size 3072

## OR
# Note the key name (myrsakey) in the URI

az keyvault key create --id https://ContosoMHSM.managedhsm.azure.net/keys/myrsakey --ops wrapKey unwrapKey --kty RSA-HSM --size 3072
```

Všimněte si, že `get` operace vrací pouze veřejný klíč a klíčové atributy. Nevrací privátní klíč (v případě asymetrického klíče nebo klíč materiálu (v případě symetrického klíče).

### <a name="create-an-ec-key"></a>Vytvoření klíče EC

Následující příklad ukazuje, jak vytvořit klíč **EC** s křivkou P-256, která se bude používat jenom pro operace **podepisování a ověření** (--OPS) a má dvě značky, **použití** a **AppName**. Značky vám pomůžou přidat další metadata ke klíči pro sledování a správu.

```azurecli-interactive
az keyvault key create --hsm-name ContosoMHSM --name myec256key --ops sign verify  --tags ‘usage=signing] appname=myapp’ --kty EC-HSM --curve P-256

## OR
# Note the key name (myec256key) in the URI

az keyvault key create --id https://ContosoMHSM.managedhsm.azure.net/keys/myec256key --ops sign verify  --tags ‘usage=signing] appname=myapp’ --kty EC-HSM --curve P-256
```

### <a name="create-a-256-bit-symmetric-key"></a>Vytvořit 256 symetrický klíč

Následující příklad ukazuje, jak vytvořit 3070 **symetrický** klíč, který bude použit pouze pro operace **šifrování a dešifrování** (--OPS).

```azurecli-interactive
az keyvault key create --hsm-name ContosoMHSM --name myaeskey --ops encrypt decrypt  --tags --kty oct-HSM --size 256

## OR
# Note the key name (myaeskey) in the URI

az keyvault key create --id https://ContosoMHSM.managedhsm.azure.net/keys/myaeskey --ops sign verify  --tags ‘usage=signing] appname=myapp’ --kty EC-HSM --curve P-256
```

## <a name="view-key-attributes-and-tags"></a>Zobrazit klíčové atributy a značky

Pomocí `az keyvault key show` příkazu můžete zobrazit atributy, verze a značky pro klíč.

```azurecli-interactive
az keyvault key show --hsm-name ContosoHSM --name myrsakey

## OR
# Note the key name (myaeskey) in the URI

az keyvault key show --id https://ContosoMHSM.managedhsm.azure.net/keys/myrsakey

```

## <a name="list-keys"></a>Zobrazit seznam klíčů

Pomocí `az keyvault key list` příkazu můžete zobrazit seznam všech klíčů v rámci spravovaného modulu HSM.

```azurecli-interactive
az keyvault key list --hsm-name ContosoHSM

## OR
# use full URI

az keyvault key list --id https://ContosoMHSM.managedhsm.azure.net/

```

## <a name="delete-a-key"></a>Odstranění klíče

Pomocí `az keyvault key delete` příkazu odstraňte klíč ze spravovaného modulu HSM. Upozorňujeme, že obnovitelné odstranění je vždycky zapnuté. Odstraněné klíče proto zůstanou v odstraněném stavu a dají se obnovit až do doby, kdy počet dnů uchování předáte, když se klíč vyprázdní (trvale odstraní) bez možnosti obnovení.

```azurecli-interactive
az keyvault key delete --hsm-name ContosoHSM --name myrsakey

## OR
# Note the key name (myaeskey) in the URI

az keyvault key delete --id https://ContosoMHSM.managedhsm.azure.net/keys/myrsakey
```

## <a name="list-deleted-keys"></a>Seznam odstraněných klíčů

Pomocí `az keyvault key list-deleted` příkazu můžete zobrazit seznam všech klíčů v odstraněném stavu ve spravovaném modulu HSM.

```azurecli-interactive
az keyvault key list-deleted --hsm-name ContosoHSM

## OR
# use full URI

az keyvault key list-deleted --id https://ContosoMHSM.managedhsm.azure.net/
```

## <a name="recover-undelete-a-deleted-key"></a>Obnovit (zrušit odstranění) odstraněný klíč

Pomocí `az keyvault key list-deleted` příkazu můžete zobrazit seznam všech klíčů v odstraněném stavu ve spravovaném modulu HSM. Pokud při obnovování odstraněných klíčů potřebujete obnovit (obnovit) klíč pomocí parametru--ID, je nutné si poznamenat `recoveryId` hodnotu odstraněného klíče získaného z `az keyvault key list-deleted` příkazu.

```azurecli-interactive
az keyvault key recover --hsm-name ContosoHSM --name myrsakey

## OR
# Note the key name (myaeskey) in the URI

az keyvault key recover --id https://ContosoMHSM.managedhsm.azure.net/deletedKeys/myrsakey
```

## <a name="purge-permanently-delete-a-key"></a>Vyprázdnit (trvale odstranit) klíč

Pomocí `az keyvault key purge` příkazu vymažete (trvale odstraní) klíč.

> [!NOTE]
> Pokud má spravovaný modul HSM zapnutou ochranu vyprázdnění, operace vyprázdnění nebude povolena. Při uplynutí doby uchování se klíč automaticky odstraní.

```azurecli-interactive
az keyvault key purge --hsm-name ContosoHSM --name myrsakey

## OR
# Note the key name (myaeskey) in the URI

az keyvault key recover --id https://ContosoMHSM.managedhsm.azure.net/deletedKeys/myrsakey

```

## <a name="create-a-single-key-backup"></a>Vytvoření jediného zálohování klíčů

Použijte `az keyvault key backup` k vytvoření zálohy klíče. Záložní soubor je šifrovaný objekt BLOB zašifrovaný s doménou zabezpečení zdrojového modulu HSM. Dá se obnovit jenom v HSM, které sdílejí stejnou doménu zabezpečení. Přečtěte si další informace o [doméně zabezpečení](security-domain.md).

```azurecli-interactive
az keyvault key backup --hsm-name ContosoHSM --name myrsakey --file myrsakey.backup

## OR
# Note the key name (myaeskey) in the URI

az keyvault key backup --id https://ContosoMHSM.managedhsm.azure.net/deletedKeys/myrsakey  --file myrsakey.backup

```

## <a name="restore-a-single-key-from-backup"></a>Obnovení jednoho klíče ze zálohy

Slouží `az keyvault key restore` k obnovení jednoho klíče. Zdrojový modul HSM, ve kterém se vytvořilo zálohování, musí sdílet stejnou doménu zabezpečení jako cílový modul HSM, ve kterém se klíč obnovuje.

> [!NOTE]
> Obnovení nebude úspěšné, pokud klíč se stejným názvem existuje v aktivním nebo odstraněném stavu.

```azurecli-interactive
az keyvault key restore --hsm-name ContosoHSM --name myrsakey --file myrsakey.bakup

## OR
# Note the key name (myaeskey) in the URI

az keyvault key recover --id https://ContosoMHSM.managedhsm.azure.net/deletedKeys/myrsakey --file myrsakey.bakup

```

## <a name="import-a-key-from-a-file"></a>Import klíče ze souboru

Pomocí `az keyvault key import` příkazu můžete importovat klíč (jenom RSA a EC) ze souboru. Soubor certifikátu musí mít privátní klíč a musí používat kódování PEM (definované v části RFC [1421](https://tools.ietf.org/html/rfc1421), [1422](https://tools.ietf.org/html/rfc1422), [1423](https://tools.ietf.org/html/rfc1423), [1424](https://tools.ietf.org/html/rfc1424)).

```azurecli-interactive
az keyvault key import --hsm-name ContosoHSM --name myrsakey --pem-file mycert.key --password 'mypassword'

## OR
# Note the key name (myaeskey) in the URI

az keyvault key recover --id https://ContosoMHSM.managedhsm.azure.net/deletedKeys/myrsakey --pem-file mycert.key --password 'mypassword'
```

Pokud chcete importovat klíč z místního modulu HARDWAROVÉho zabezpečení do spravovaného HSM, přečtěte si téma [Import klíčů chráněných HSM do spravovaného HSM (BYOK)](hsm-protected-keys-byok.md) .

## <a name="next-steps"></a>Další kroky

- Kompletní Reference k rozhraní příkazového řádku Azure CLI pro příkazy trezoru klíčů najdete v tématu [Key Vault reference CLI](/cli/azure/keyvault).
- Odkazy na programování najdete [v tématu Azure Key Vault příručka pro vývojáře](../general/developers-guide.md) .
- Další informace o [správě rolí spravovaného HSM](role-management.md)
- Další informace o [osvědčených postupech spravovaného modulu HSM](best-practices.md)
