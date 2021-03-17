---
title: Přineste si vlastní klíčovou specifikaci – Azure Key Vault | Microsoft Docs
description: Tento dokument popisuje vlastní specifikaci klíče.
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: conceptual
ms.date: 02/04/2021
ms.author: ambapat
ms.openlocfilehash: 141abea0c0946c98b6dfe627f32f01682a18be44
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2021
ms.locfileid: "99581019"
---
# <a name="bring-your-own-key-specification"></a>Specifikace používání vlastního klíče

Tento dokument popisuje specifikace pro import klíčů chráněných HSM z místních HSM zákazníků do Key Vault.

## <a name="scenario"></a>Scenario

Key Vault zákazník by chtěli bezpečně přenést klíč z místního modulu hardwarového zabezpečení (HSM) mimo Azure do Azure Key Vault HSM. Proces importu klíče vygenerovaného mimo Key Vault se obecně označuje jako Bring Your Own Key (BYOK).

Požadavky jsou následující:
* Klíč, který se má přenášet, nikdy neexistuje mimo modul HARDWAROVÉho zabezpečení ve formě prostého textu.
* Mimo modul HARDWAROVÉho zabezpečení je klíč, který se má přenést, vždycky chráněný klíčem uloženým v modulu Azure Key Vault HSM.

## <a name="terminology"></a>Terminologie

|Název klíče|Typ klíče|Zdroj|Description|
|---|---|---|---|
|Klíč pro výměnu klíčů (KEK)|RSA|Modul HSM Azure Key Vault|V Azure Key Vault byl vygenerován pár klíčů RSA zálohovaný modulem HSM.
Klíč pro zabalení|AES|HSM dodavatele|[Dočasný] klíč AES generovaný modulem HSM v-Prem
Cílový klíč|RSA, ES, AES (jenom spravovaný HSM)|HSM dodavatele|Klíč, který se má přenést do modulu HARDWAROVÉho zabezpečení Azure Key Vault

**Klíč pro výměnu klíčů**: klíč zálohovaný modulem HSM, který zákazník vygeneruje v trezoru klíčů, do kterého se naimportuje BYOK klíč. Tento KEK musí mít následující vlastnosti:

* Jedná se o klíč RSA-HSM (4096 nebo 3072-bit nebo 2048-bit).
* Bude mít opravený key_ops (jenom import), který umožní použití jenom během BYOK.
* Musí být ve stejném trezoru, kam se bude cílový klíč importovat.

## <a name="user-steps"></a>Kroky uživatele

K provedení převodu klíčů uživatel provede následující kroky:

1. Vygenerujte KEK.
2. Načtěte veřejný klíč pro KEK.
3. Pomocí nástroje BYOK poskytnutého dodavatelem HSM – importujte KEK do cílového modulu HSM a exportujte cílový klíč chráněný rozhraním KEK.
4. Importujte chráněný cílový klíč do Azure Key Vault.

Zákazníci dokončí kroky 3 pomocí nástroje BYOK a dokumentace poskytované dodavatelem HSM. Vytvoří objekt BLOB přenosu klíče (soubor. BYOK).


## <a name="hsm-constraints"></a>Omezení HSM

Stávající modul HARDWAROVÉho zabezpečení může u klíče, který spravuje, použít omezení, včetně:
* Modul HARDWAROVÉho zabezpečení může být potřeba nakonfigurovat tak, aby povoloval export na základě zalamování klíčů.
* Aby mohl modul HARDWAROVÉho zabezpečení umožňovat řízený export, může být nutné označit cílový klíč jako CKA_EXTRACTABLE.
* V některých případech může být nutné označit klíč KEK a zalamování jako CKA_TRUSTED. To umožňuje použít k zabalení klíčů v modulu hardwarového zabezpečení (HSM).

Konfigurace zdrojového modulu HSM je obecně mimo rozsah této specifikace. Společnost Microsoft očekává, že dodavatel HSM předloží dokumentaci přiloženou ke svému BYOK nástroji, aby zahrnovala takové kroky konfigurace.

> [!NOTE]
> Kroky 1, 2 a 4 popsané níže je možné provádět pomocí jiných rozhraní, například Azure PowerShell a webu Azure Portal. Můžete je také provádět programově pomocí ekvivalentních funkcí v sadě Key Vault SDK.

### <a name="step-1-generate-kek"></a>Krok 1: vygenerujte KEK

Pomocí příkazu **AZ Key trezor Key Create** vytvořte KEK s klíčovými operacemi nastavenými na import. Poznamenejte si identifikátor klíče Kid vrácený z následujícího příkazu.

```azurecli
az keyvault key create --kty RSA-HSM --size 4096 --name KEKforBYOK --ops import --vault-name ContosoKeyVaultHSM
```

### <a name="step-2-retrieve-the-public-key-of-the-kek"></a>Krok 2: načtení veřejného klíče KEK

Stáhněte si část KEK veřejného klíče a uložte ji do souboru PEM.

```azurecli
az keyvault key download --name KEKforBYOK --vault-name ContosoKeyVaultHSM --file KEKforBYOK.publickey.pem
```

### <a name="steps-3-generate-key-transfer-blob-using-hsm-vendor-provided-byok-tool"></a>Kroky 3: vygenerování objektu BLOB přenosu klíče pomocí poskytnutého nástroje BYOK dodavatele HSM

K vytvoření objektu BLOB pro přenos klíčů (uložený jako soubor. BYOK) použije zákazník nástroj HSM poskytnutý dodavatelem HSM. KEK veřejný klíč (jako soubor. pem) bude jedním ze vstupů tohoto nástroje.

#### <a name="key-transfer-blob"></a>Objekt BLOB přenosu klíčů
V dlouhodobém horizontu by Microsoft chtěl použít mechanismus CKM_RSA_AES_KEY_WRAP PKCS # 11 k přenosu cílového klíče do Azure Key Vault, protože tento mechanismus vytváří jeden objekt BLOB a důležitější je, zprostředkující klíč AES se zpracovává dvěma HSM a je zaručený, aby byl dočasný. Tento mechanismus není v některých HSM aktuálně k dispozici, ale kombinací ochrany cílového klíče s CKM_AES_KEY_WRAP_PAD pomocí klíče AES a ochranou klíče AES pomocí CKM_RSA_PKCS_OAEP vytváří ekvivalentní objekt BLOB.

Cílový klíč ve formátu prostého textu závisí na typu klíče: 
* Pro klíč RSA se v souboru PKCS # 8 (RFC5208) zabalí privátní klíč ASN. 1 DER Encoding [RFC3447]. 
* Pro klíč ES se jedná o číslo ASN. 1 kódování DER [RFC5915] zabalené ve formátu PKCS # 8 [RFC5208].
* Pro oktetový klíč, nezpracované bajty klíče

Bajty pro nešifrovaný klíč se pak transformují pomocí mechanismu CKM_RSA_AES_KEY_WRAP:
* Dočasný klíč AES se vygeneruje a zašifruje pomocí klíče RSA s vybalením pomocí RSA-výplně OAEP se SHA1.
* Kódovaný nešifrovaný klíč je zašifrovaný pomocí klíče AES pomocí zalamování kláves AES s odsazením.
* Zašifrovaný klíč AES a zašifrovaný prostý text jsou zřetězené a vytvoří konečný šifrovaný objekt BLOB.

Formát objektu BLOB pro přenos používá RFC7516 (JSON web Encryption Compact Serialization) primárně jako vozidlo pro doručování požadovaných metadat do služby pro správné dešifrování.

Pokud se používá CKM_RSA_AES_KEY_WRAP_PAD, serializace objektu BLOB přenosu by byla:

```json
{
  "schema_version": "1.0.0",
  "header":
  {
    "kid": "<key identifier of the KEK>",
    "alg": "dir",
    "enc": "CKM_RSA_AES_KEY_WRAP"
  },
  "ciphertext":"BASE64URL(<ciphertext contents>)",
  "generator": "BYOK tool name and version; source HSM name and firmware version"
}

```

* Kid = identifikátor klíče KEK. U Key Vault klíčů vypadá nějak takto: https://ContosoKeyVaultHSM.vault.azure.net/keys/mykek/eba63d27e4e34e028839b53fac905621
* ALG = algoritmus. 
* DIR = přímý režim, tj. odkazovaný dětský odkaz se používá k přímé ochraně šifrovaného textu, který je přesná reprezentace CKM_RSA_AES_KEY_WRAP
* Generator = informativní pole, které označuje název a verzi nástroje BYOK a zdrojový výrobce a model HSM. Tyto informace jsou určené pro použití při řešení potíží a podpoře.

Objekt BLOB JSON je uložený v souboru s příponou. BYOK, aby klienti Azure PowerShell/CLI správně považovali, když se používají příkazy add-AzKeyVaultKey (PSH) nebo AZ Key trezor Key import (CLI).

### <a name="step-4-upload-key-transfer-blob-to-import-hsm-key"></a>Krok 4: nahrání objektu BLOB pro přenos klíčů pro import klíče HSM

Zákazník převede objekt BLOB přenosu klíče (soubor. BYOK) do online pracovní stanice a potom spustí příkaz **AZ Key trezor Key import** pro import tohoto objektu BLOB jako nového klíče zálohovaného modulem HSM do Key Vault. 

Pokud chcete importovat klíč RSA, použijte tento příkaz:
```azurecli
az keyvault key import --vault-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file KeyTransferPackage-ContosoFirstHSMkey.byok --ops encrypt decrypt
```
Chcete-li importovat klíč EC, je nutné zadat typ klíče a název křivky.

```azurecli
az keyvault key import --vault-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file --kty EC-HSM --curve-name "P-256" KeyTransferPackage-ContosoFirstHSMkey.byok --ops sign verify
```


Při spuštění výše uvedeného příkazu má za následek odeslání REST API žádosti následujícím způsobem:

```
PUT https://contosokeyvaulthsm.vault.azure.net/keys/ContosoFirstHSMKey?api-version=7.0
```

Text žádosti při importu klíče RSA:
```json
{
  "key": {
    "kty": "RSA-HSM",
    "key_ops": [
      "decrypt",
      "encrypt"
    ],
    "key_hsm": "<Base64 encoded BYOK_BLOB>"
  },
  "attributes": {
    "enabled": true
  }
}
```

Text žádosti při importu klíče EC:
```json
{
  "key": {
    "kty": "EC-HSM",
    "crv": "P-256",
    "key_ops": [
      "sign",
      "verify"
    ],
    "key_hsm": "<Base64 encoded BYOK_BLOB>"
  },
  "attributes": {
    "enabled": true
  }
}
```

hodnota "key_hsm" je celý obsah KeyTransferPackage-ContosoFirstHSMkey. BYOK kódovaný ve formátu base64.

## <a name="references"></a>Reference
- [Průvodce vývojáře pro službu Key Vault](../general/developers-guide.md)

## <a name="next-steps"></a>Další kroky
* Podrobné pokyny BYOK: [Import klíčů chráněných HSM do Key Vault (BYOK)](hsm-protected-keys-byok.md)

