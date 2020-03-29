---
title: Jak generovat a přenášet klíče chráněné hsm pro Azure Key Vault - Azure Key Vault | Dokumenty společnosti Microsoft
description: Tento článek vám pomůže naplánovat, generovat a přenést vlastní klíče chráněné hsm pro použití s Azure Key Vault. Také známý jako přineste si vlastní klíč (BYOK).
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: ambapat
ms.openlocfilehash: 08a4330f4a786deca8ddb2f1c6803b29152e7f50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79080140"
---
# <a name="import-hsm-protected-keys-to-key-vault-preview"></a>Import klíčů chráněných modulem HSM do Key Vaultu (Preview)

> [!NOTE]
> Tato funkce je ve verzi Preview a je dostupná jenom v oblastech Azure *– VÝCHOD USA 2 EUAP* a Ve střední části USA *EUAP*. 

Pro větší jistotu při použití azure key vault, můžete importovat nebo generovat klíč v modulu hardwarového zabezpečení (HSM); klíč nikdy neopustí hranici hsm. Tento scénář se často označuje jako *přineste vlastní klíč* (BYOK). Trezor klíčů používá k ochraně klíčů řadu nCipher nShield hsmů (FIPS 140-2 Level 2 ověřeno).

Informace v tomto článku vám pomohou naplánovat, generovat a přenést vlastní klíče chráněné hsm pro použití s Azure Key Vault.

> [!NOTE]
> Tato funkce není k dispozici pro Azure China 21Vianet. 
> 
> Tato metoda importu je k dispozici pouze pro [podporované moduly hesm .](#supported-hsms) 

Další informace a kurz, jak začít používat trezor klíčů (včetně jak vytvořit trezor klíčů pro klíče chráněné pomocí hsm), najdete v [tématu Co je Azure Key Vault?](key-vault-overview.md).

## <a name="overview"></a>Přehled

Zde je přehled procesu. Konkrétní kroky k dokončení jsou popsány dále v článku.

* V trezoru klíčů vygenerujte klíč (označovaný jako *klíč KEK).* KEK musí být klíč RSA-HSM, `import` který má pouze operaci klíče. Pouze Key Vault Premium SKU podporuje RSA-HSM klíče.
* Stáhněte veřejný klíč KEK jako soubor PEM.
* Přeneste veřejný klíč KEK do offline počítače, který je připojen k místnímu serveru zabezpečení hesm.
* V počítači offline použijte nástroj BYOK poskytnutý dodavatelem hsm k vytvoření souboru BYOK. 
* Cílový klíč je zašifrován pomocí kek, který zůstane zašifrován, dokud nebude přenesen do hsm trezoru klíčů. Pouze šifrovaná verze vašeho klíče opustí místní hsm.
* KEK, který je generován uvnitř klíče trezoru hsm mj. nelze exportovat. Soubory hesm vynucují pravidlo, že neexistuje žádná jasná verze kek mimo klíč trezoru hesma.
* KEK musí být ve stejném trezoru klíčů, kde bude importován cílový klíč.
* Při nahrání souboru BYOK do trezoru klíčů, key vault HSM používá k dešifrování materiálu cílového klíče a importu jako klíč hsm. Tato operace probíhá zcela uvnitř klíče trezoru hsm. Cílový klíč vždy zůstane v hranici ochrany bezpečnostního zařízení.

## <a name="prerequisites"></a>Požadavky

V následující tabulce jsou uvedeny požadavky pro použití funkce BYOK v úložišti klíčů Azure:

| Požadavek | Další informace |
| --- | --- |
| Předplatné Azure |K vytvoření trezoru klíčů v azure key vaultu potřebujete předplatné Azure. [Zaregistrujte si bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial/). |
| Prémiová skladová položka trezoru klíčů pro import klíčů chráněných softwarem HeSM |Další informace o úrovních služeb a možnostech v trezoru klíčů Azure Key Vault najdete v [tématu Ceny trezoru klíčů](https://azure.microsoft.com/pricing/details/key-vault/). |
| Modul y HSM z podporovaného seznamu modulů hesm a nástroj BYOK a pokyny poskytnuté dodavatelem modulu hesm | Musíte mít oprávnění pro hsmm a základní znalosti o tom, jak používat jej. Viz [Podporované moduly hesm .](#supported-hsms) |
| Azure CLI verze 2.1.0 nebo novější | Viz [Instalace příkazového příkazového příkazu k Azure](/cli/azure/install-azure-cli?view=azure-cli-latest).|

## <a name="supported-hsms"></a>Podporované moduly hesmazívky nebo je v pouz

|Název dodavatele|Typ dodavatele|Podporované modely modulu hsm|Další informace|
|---|---|---|---|
|Thales|Výrobce|SafeNet Luna HSM 7 rodina s firmwarem verze 7.3 nebo novější| [Nástroj a dokumentace SafeNet Luna BYOK](https://supportportal.thalesgroup.com/csm?id=kb_article_view&sys_kb_id=3892db6ddb8fc45005c9143b0b961987&sysparm_article=KB0021016)|
|Fortanix|HSM jako služba|Samoobslužná služba správy klíčů (SDKMS)|[Export klíčů SDKMS poskytovatelům cloudu pro BYOK – Azure Key Vault](https://support.fortanix.com/hc/en-us/articles/360040071192-Exporting-SDKMS-keys-to-Cloud-Providers-for-BYOK-Azure-Key-Vault)|


> [!NOTE]
> Chcete-li importovat klíče chráněné hsm z řady nCipher nShield hsm, použijte [starší postup BYOK](hsm-protected-keys-legacy.md).

## <a name="supported-key-types"></a>Podporované typy klíčů

|Název klíče|Typ klíče|Velikost klíče|Zdroj|Popis|
|---|---|---|---|---|
|Klíč výměny klíčů (KEK)|RSA| 2 048 bitů<br />3 072bitové<br />4 096 bitů|Azure Key Vault HSM|Dvojice klíčů RSA podporovaná hsm generovaným v trezoru klíčů Azure|
|Cílový klíč|RSA|2 048 bitů<br />3 072bitové<br />4 096 bitů|Dodavatel HSM|Klíč, který má být přenesen do hsm úložiště klíčů Azure|

## <a name="generate-and-transfer-your-key-to-the-key-vault-hsm"></a>Vygenerujte a přeneste klíč do hsm trezoru klíčů

Při generování a přenosu klíče do hsm ového klíče trezoru klíčů:

* [Krok 1: Generování KEK](#step-1-generate-a-kek)
* [Krok 2: Stažení veřejného klíče KEK](#step-2-download-the-kek-public-key)
* [Krok 3: Vygenerujte a připravte klíč k přenosu](#step-3-generate-and-prepare-your-key-for-transfer)
* [Krok 4: Přenos klíče do azure key vaultu](#step-4-transfer-your-key-to-azure-key-vault)

### <a name="step-1-generate-a-kek"></a>Krok 1: Generování KEK

KEK je klíč RSA, který je generován v objektu hsm. KEK se používá k šifrování klíče, který chcete importovat *(cílový* klíč).

KEK musí být:
- Klíč RSA-HSM (2 048 bitů; 3 072 bitů nebo 4 096 bitů)
- Generováno ve stejném trezoru klíčů, ve kterém chcete importovat cílový klíč
- Vytvořeno s povolenými klíčovými operacemi nastavenými na`import`

> [!NOTE]
> KEK musí mít "import" jako jedinou povolenou operaci klíče. "import" se vzájemně vylučují se všemi ostatními klíčovými operacemi.

Pomocí příkazu [az keyvault vytvořte](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create) k vytvoření keku, který má klíčové operace nastavené na `import`. Zaznamenejte`kid`identifikátor klíče ( ), který je vrácen z následujícího příkazu. (Hodnotu `kid` použijete v [kroku 3](#step-3-generate-and-prepare-your-key-for-transfer).)

```azurecli
az keyvault key create --kty RSA-HSM --size 4096 --name KEKforBYOK --ops import --vault-name ContosoKeyVaultHSM
```

### <a name="step-2-download-the-kek-public-key"></a>Krok 2: Stažení veřejného klíče KEK

Ke stažení veřejného klíče KEK do souboru .pem použijte [ke stažení klíče az keyvault.](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-download) Cílový klíč, který importujete, je zašifrován pomocí veřejného klíče KEK.

```azurecli
az keyvault key download --name KEKforBYOK --vault-name ContosoKeyVaultHSM --file KEKforBYOK.publickey.pem
```

Přeneste soubor KEKforBYOK.publickey.pem do offline počítače. Tento soubor budete potřebovat v dalším kroku.

### <a name="step-3-generate-and-prepare-your-key-for-transfer"></a>Krok 3: Vygenerujte a připravte klíč k přenosu

Stažení a instalace nástroje BYOK naleznete v dokumentaci dodavatele hsm. Postupujte podle pokynů od dodavatele hsm generovat cílový klíč a potom vytvořit balíček přenosu klíče (soubor BYOK). Nástroj BYOK použije `kid` soubor od [kroku 1](#step-1-generate-a-kek) a soubor KEKforBYOK.publickey.pem, který jste stáhli v [kroku 2,](#step-2-download-the-kek-public-key) ke generování šifrovaného cílového klíče v souboru BYOK.

Přeneste soubor BYOK do připojeného počítače.

> [!NOTE] 
> Import 1 024bitových klíčů RSA není podporován. V současné době není import klíče eliptické křivky (EC) podporován.
> 
> **Známý problém**: Import cílového klíče RSA 4K z modulů hesm a hsm safenet luna je podporován pouze firmwarem 7.4.0 nebo novějším.

### <a name="step-4-transfer-your-key-to-azure-key-vault"></a>Krok 4: Přenos klíče do azure key vaultu

Chcete-li dokončit import klíče, přeneste balíček přenosu klíčů (soubor BYOK) z odpojeného počítače do počítače připojeného k internetu. Pomocí příkazu [importu kláves az keyvault](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import) nahrajte soubor BYOK do souboru hsm.

```azurecli
az keyvault key import --vault-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file KeyTransferPackage-ContosoFirstHSMkey.byok
```

Pokud je nahrávání úspěšné, Azure CLI zobrazí vlastnosti importovaného klíče.

## <a name="next-steps"></a>Další kroky

Nyní můžete použít tento klíč chráněný s hsm v trezoru klíčů. Další informace naleznete v [tomto porovnání cen a funkcí](https://azure.microsoft.com/pricing/details/key-vault/).



