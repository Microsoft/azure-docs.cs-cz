---
title: Postup generování a přenosu klíčů chráněných HSM pro Azure Key Vault-Azure Key Vault | Microsoft Docs
description: Tento článek vám může posloužit k plánování, generování a přenosu vlastních klíčů chráněných HSM pro použití s Azure Key Vault. Označuje se také jako Přineste si vlastní klíč (BYOK).
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: ambapat
ms.openlocfilehash: 9b8f1065660ea8331853f8804e709134fe682ba7
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/23/2020
ms.locfileid: "77566110"
---
# <a name="import-hsm-protected-keys-to-key-vault-preview"></a>Import klíčů chráněných modulem HSM do Key Vaultu (Preview)

> [!NOTE]
> Tato funkce je ve verzi Preview a je dostupná jenom v oblastech Azure *východní USA 2 EUAP* a *střed USA EUAP*. 

Pro zvýšení zabezpečení při použití Azure Key Vault můžete importovat nebo generovat klíč v modulu hardwarového zabezpečení (HSM); klíč nebude nikdy opustit hranici modulu HARDWAROVÉho zabezpečení. Tento scénář se často označuje jako *Přineste si vlastní klíč* (BYOK). Key Vault používá hardwarového nshieldou rodinu HSM (FIPS 140-2 Level 2) k ochraně vašich klíčů.

Informace v tomto článku vám pomůžou při plánování, generování a přenosu vlastních klíčů chráněných HSM pro použití s Azure Key Vault.

> [!NOTE]
> Tato funkce není pro Azure Čína 21Vianet k dispozici. 
> 
> Tato metoda importu je dostupná jenom pro [podporované HSM](#supported-hsms). 

Další informace a návod, jak začít používat Key Vault (včetně postupu vytvoření trezoru klíčů pro klíče chráněné HSM), najdete v tématu [co je Azure Key Vault?](key-vault-overview.md).

## <a name="overview"></a>Přehled

Tady je přehled tohoto procesu. Konkrétní kroky k dokončení jsou popsány dále v článku.

* V Key Vault vygenerujte klíč (označovaný jako klíč pro *výměnu klíčů* (KEK)). KEK musí být klíč RSA-HSM, který má pouze operaci `import` Key. Klíče RSA-HSM podporuje jenom Key Vault SKU úrovně Premium.
* Stáhněte si veřejný klíč KEK jako soubor. pem.
* Přeneste veřejný klíč KEK do offline počítače, který je připojený k místnímu modulu HARDWAROVÉho zabezpečení.
* V počítači v režimu offline použijte k vytvoření souboru BYOK nástroj BYOK, který poskytuje dodavatel HSM. 
* Cílový klíč je zašifrovaný pomocí KEK, který zůstane zašifrovaný, dokud se nepřenáší do modulu HSM Key Vault. Místní modul hardwarového zabezpečení (HSM) opouští jenom zašifrovanou verzi vašeho klíče.
* KEK vygenerované v modulu HSM Key Vault nelze exportovat. HSM vynutilo pravidlo, že žádné jasné verze KEK neexistují mimo Key Vault HSM.
* KEK musí být ve stejném trezoru klíčů, kam se bude cílový klíč importovat.
* Po nahrání souboru BYOK do Key Vault Key Vault HSM používá privátní klíč KEK k dešifrování cílového klíčového materiálu a naimportuje ho jako klíč HSM. Tato operace probíhá zcela v rámci Key Vault HSM. Cílový klíč vždy zůstává na hranici ochrany HSM.

## <a name="prerequisites"></a>Požadavky

V následující tabulce jsou uvedeny předpoklady pro používání BYOK v Azure Key Vault:

| Požadavek | Další informace |
| --- | --- |
| Předplatné Azure |Pokud chcete vytvořit Trezor klíčů v Azure Key Vault, potřebujete předplatné Azure. [Zaregistrujte si bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial/). |
| SKU Key Vault úrovně Premium pro import klíčů chráněných HSM |Další informace o úrovních a funkcích služby v Azure Key Vault najdete v článku o [cenách Key Vault](https://azure.microsoft.com/pricing/details/key-vault/). |
| Modul HARDWAROVÉho zabezpečení z podporovaného seznamu HSM a nástroj BYOK a pokyny, které poskytuje dodavatel HSM. | Musíte mít oprávnění pro modul HARDWAROVÉho zabezpečení a základní informace o tom, jak používat modul hardwarového zabezpečení (HSM). Viz článek [podporované HSM](#supported-hsms). |
| Azure CLI verze 2.1.0 nebo novější | Viz [instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).|

## <a name="supported-hsms"></a>Podporované HSM

|Název dodavatele HSM|Podporované modely HSM|Další informace|
|---|---|---|
|Thales|SafeNet Luna HSM 7 Family s firmwarem verze 7,3 nebo novější| [Nástroj SafeNet Luna BYOK a dokumentace](https://supportportal.thalesgroup.com/csm?id=kb_article_view&sys_kb_id=3892db6ddb8fc45005c9143b0b961987&sysparm_article=KB0021016)|

> [!NOTE]
> Pro import klíčů chráněných pomocí HSM z podpůrný software nCipher hardwarového nShield řady HSM použijte [starší postup v BYOK](hsm-protected-keys-legacy.md).

## <a name="supported-key-types"></a>Podporované typy klíčů

|Název klíče|Typ klíče|Velikost klíče|Zdroj|Popis|
|---|---|---|---|---|
|Klíč pro výměnu klíčů (KEK)|RSA| 2 048 – bit<br />3 072 – bit<br />4 096 – bit|Modul HSM Azure Key Vault|Pár klíčů RSA zálohovaný modulem HSM vygenerovaný v Azure Key Vault|
|Cílový klíč|RSA|2 048 – bit<br />3 072 – bit<br />4 096 – bit|HSM dodavatele|Klíč, který se má přenést do modulu HARDWAROVÉho zabezpečení Azure Key Vault|

## <a name="generate-and-transfer-your-key-to-the-key-vault-hsm"></a>Generování a přenos klíče do modulu Key Vault HSM

Postup generování a přenos klíče do modulu HARDWAROVÉho zabezpečení Key Vault:

* [Krok 1: vygenerujte KEK](#step-1-generate-a-kek)
* [Krok 2: stažení veřejného klíče KEK](#step-2-download-the-kek-public-key)
* [Krok 3: vygenerujte a připravte klíč pro přenos.](#step-3-generate-and-prepare-your-key-for-transfer)
* [Krok 4: přenesení klíče do Azure Key Vault](#step-4-transfer-your-key-to-azure-key-vault)

### <a name="step-1-generate-a-kek"></a>Krok 1: vygenerujte KEK

KEK je klíč RSA generovaný v modulu Key Vault HSM. KEK se používá k zašifrování klíče, který chcete importovat ( *cílový* klíč).

KEK musí být:
- Klíč RSA-HSM (2 048-bit; 3 072-bit; nebo 4 096-bit)
- vygenerováno ve stejném trezoru klíčů, do kterého chcete importovat cílový klíč.
- Vytvořeno s povolenou operací Key nastavenou na `import`

Pomocí příkazu [AZ Key trezor Key Create](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create) vytvořte KEK, který má klíčové operace nastavené na `import`. Poznamenejte si identifikátor klíče (`kid`), který se vrátil z následujícího příkazu. (V [kroku 3](#step-3-generate-and-prepare-your-key-for-transfer)použijete hodnotu `kid`.)

```azurecli
az keyvault key create --kty RSA-HSM --size 4096 --name KEKforBYOK --ops import --vault-name ContosoKeyVaultHSM
```

### <a name="step-2-download-the-kek-public-key"></a>Krok 2: stažení veřejného klíče KEK

K stažení veřejného klíče KEK do souboru. pem použijte [AZ Key trezor Key Download](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-download) . Cílový klíč, který importujete, je zašifrovaný pomocí veřejného klíče KEK.

```azurecli
az keyvault key download --name KEKforBYOK --vault-name ContosoKeyVaultHSM --file KEKforBYOK.publickey.pem
```

Přeneste soubor KEKforBYOK. PublicKey. pem do počítače offline. Tento soubor budete potřebovat v dalším kroku.

### <a name="step-3-generate-and-prepare-your-key-for-transfer"></a>Krok 3: vygenerujte a připravte klíč pro přenos.

Pokud si chcete stáhnout a nainstalovat nástroj BYOK, přečtěte si dokumentaci od dodavatele HSM. Podle pokynů dodavatele HSM vygenerujte cílový klíč a pak vytvořte balíček pro přenos klíčů (soubor BYOK). Nástroj BYOK použije `kid` z [kroku 1](#step-1-generate-a-kek) a souboru KEKforBYOK. PublicKey. pem, který jste stáhli v [kroku 2](#step-2-download-the-kek-public-key) pro vygenerování šifrovaného CÍLOVÉHO klíče v souboru BYOK.

Přeneste soubor BYOK do připojeného počítače.

> [!NOTE] 
> Import klíčů RSA 1 024 není podporován. Import klíče eliptické křivky (ES) v současné době není podporován.
> 
> **Známý problém**: import CÍLOVÉHO klíče RSA 4k z Safenet Luna HSM se nezdařil. Po vyřešení problému bude tento článek aktualizován.

### <a name="step-4-transfer-your-key-to-azure-key-vault"></a>Krok 4: přenesení klíče do Azure Key Vault

Pokud chcete dokončit import klíče, přeneste balíček přenosu klíčů (soubor BYOK) z odpojeného počítače na počítač připojený k Internetu. Pomocí příkazu [AZ Key trezor Key import](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import) nahrajte soubor BYOK do modulu HSM Key Vault.

```azurecli
az keyvault key import --vault-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file KeyTransferPackage-ContosoFirstHSMkey.byok
```

Pokud je nahrávání úspěšné, Azure CLI zobrazí vlastnosti importovaného klíče.

## <a name="next-steps"></a>Další kroky

Tento klíč chráněný HSM teď můžete použít ve vašem trezoru klíčů. Další informace najdete v [této ceně a porovnání funkcí](https://azure.microsoft.com/pricing/details/key-vault/).



