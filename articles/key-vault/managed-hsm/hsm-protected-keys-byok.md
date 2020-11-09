---
title: Postup generování a přenosu klíčů chráněných HSM pro Azure Key Vault spravované modul HSM-Azure Key Vault | Microsoft Docs
description: Tento článek vám může pomáhat při plánování, generování a přenosu vlastních klíčů chráněných HSM pro použití se spravovaným modulem HSM. Označuje se také jako Přineste si vlastní klíč (BYOK).
services: key-vault
author: amitbapat
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 09/17/2020
ms.author: ambapat
ms.openlocfilehash: 39486b076f9284436207f823ea48fddc98bb48a0
ms.sourcegitcommit: 65d518d1ccdbb7b7e1b1de1c387c382edf037850
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2020
ms.locfileid: "94372842"
---
# <a name="import-hsm-protected-keys-to-managed-hsm-byok"></a>Import klíčů chráněných HSM do spravovaného HSM (BYOK)

 Azure Key Vault spravovaný HSM podporuje import klíčů vygenerovaných v místním modulu hardwarového zabezpečení (HSM). klíče nebudou nikdy opustit hranici ochrany HSM. Tento scénář se často označuje jako *Přineste si vlastní klíč* (BYOK). Spravovaný modul HSM používá k ochraně klíčů adaptéry LiquidSecurity HSM (Standard FIPS 140-2 Level 3).

Informace v tomto článku vám pomůžou při plánování, generování a přenosu vlastních klíčů chráněných HSM pro použití se spravovaným modulem HSM.

> [!NOTE]
> Tato funkce není pro Azure Čína 21Vianet k dispozici. Tato metoda importu je dostupná jenom pro [podporované HSM](#supported-hsms). 

Další informace a návod, jak začít používat spravovaný modul HSM, najdete v tématu [co je spravovaný modul HSM?](overview.md).

## <a name="overview"></a>Přehled

Tady je přehled tohoto procesu. Konkrétní kroky k dokončení jsou popsány dále v článku.

* Ve spravovaném modulu HSM vygenerujte klíč (označovaný jako *klíč pro výměnu klíčů* (KEK)). KEK musí být klíč RSA-HSM, který má pouze `import` operaci Key. 
* Stáhněte si veřejný klíč KEK jako soubor. pem.
* Přeneste veřejný klíč KEK do offline počítače, který je připojený k místnímu modulu HARDWAROVÉho zabezpečení.
* V počítači v režimu offline použijte k vytvoření souboru BYOK nástroj BYOK, který poskytuje dodavatel HSM. 
* Cílový klíč je zašifrovaný pomocí KEK, který zůstane zašifrovaný, dokud se nepřenáší do spravovaného HSM. Místní modul hardwarového zabezpečení (HSM) opouští jenom zašifrovanou verzi vašeho klíče.
* KEK, který se vygeneroval ve spravovaném modulu HSM, nejde exportovat. HSM vynutilo pravidlo, že žádné jasné verze KEK neexistují mimo spravovaný modul HSM.
* KEK musí být ve stejném spravovaném modulu HSM, kde se bude cílový klíč importovat.
* Po nahrání souboru BYOK do spravovaného modulu HARDWAROVÉho zabezpečení spravuje modul HARDWAROVÉho zabezpečení, který používá privátní klíč KEK, k dešifrování cílového klíčového materiálu a naimportuje ho jako klíč HSM. Tato operace probíhá zcela uvnitř modulu hardwarového zabezpečení (HSM). Cílový klíč vždy zůstává na hranici ochrany HSM.


## <a name="prerequisites"></a>Požadavky

Pokud chcete používat příkazy rozhraní příkazového řádku Azure CLI v tomto článku, musíte mít následující položky:

* Předplatné Microsoft Azure. Pokud žádný nemáte, můžete si zaregistrovat [bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial).
* Verze Azure CLI 2.12.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI]( /cli/azure/install-azure-cli).
* Spravovaný modul HSM [podporuje seznam HSM](#supported-hsms) v rámci vašeho předplatného. Informace najdete v tématu [rychlý Start: zřízení a aktivace spravovaného modulu HSM pomocí Azure CLI](quick-create-cli.md) , který umožňuje zřídit a aktivovat spravovaný modul HSM.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se chcete přihlásit k Azure pomocí rozhraní příkazového řádku, můžete zadat:

```azurecli
az login
```

Další informace o možnostech přihlášení prostřednictvím rozhraní příkazového řádku si můžete prohlédnout v části [přihlášení pomocí Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest&preserve-view=true) .

## <a name="supported-hsms"></a>Podporované HSM

|Název dodavatele|Typ dodavatele|Podporované modely HSM|Další informace|
|---|---|---|---|
|Podpůrný software nCipher|Výrobců<br/>HSM jako služba|<ul><li>Hardwarového nShield rodina HSM</li><li>Hardwarového nShield jako služba</ul>|[Podpůrný software nCipher nový nástroj a dokumentace pro BYOK](https://www.ncipher.com/products/key-management/cloud-microsoft-azure)|
|Thales|Manufacturer|<ul><li>Luna HSM 7 Family s firmwarem verze 7,3 nebo novější</li></ul>| [Nástroj a dokumentace pro Luna BYOK](https://supportportal.thalesgroup.com/csm?id=kb_article_view&sys_kb_id=3892db6ddb8fc45005c9143b0b961987&sysparm_article=KB0021016)|
|Fortanix|Výrobců<br/>HSM jako služba|<ul><li>Služba správy klíčů Self-Defending (SDKMS)</li><li>Equinix SmartKey</li></ul>|[Export klíčů SDKMS do poskytovatelů cloudu pro BYOK-Azure Key Vault](https://support.fortanix.com/hc/en-us/articles/360040071192-Exporting-SDKMS-keys-to-Cloud-Providers-for-BYOK-Azure-Key-Vault)|
|PERC|Manufacturer|Všechny LiquidSecurity HSM s<ul><li>Firmware verze 2.0.4 nebo novější</li><li>Firmware verze 3,2 nebo novější</li></ul>|[Nástroj a dokumentace k PERC BYOK](https://www.marvell.com/products/security-solutions/nitrox-hs-adapters/exporting-marvell-hsm-keys-to-cloud-azure-key-vault.html)|
|Cryptomathic|ISV (Enterprise Key Management System)|Několik značek HSM a modelů, včetně<ul><li>Podpůrný software nCipher</li><li>Thales</li><li>Utimaco</li></ul>Podrobnosti najdete v tématu [Cryptomathic web](https://www.cryptomathic.com/azurebyok) .|[Nástroj a dokumentace pro Cryptomathic BYOK](https://www.cryptomathic.com/azurebyok)|
|Securosys SA|Výrobce, HSM jako služba|Primus, rodina HSM, Securosys cloudy HSM|[Nástroj a dokumentace pro Primus BYOK](https://www.securosys.com/primus-azure-byok)|
||||


## <a name="supported-key-types"></a>Podporované typy klíčů

|Název klíče|Typ klíče|Velikost klíče|Zdroj|Popis|
|---|---|---|---|---|
|Klíč pro výměnu klíčů (KEK)|RSA| 2 048 – bit<br />3 072 – bit<br />4 096 – bit|Managed HSM|Pár klíčů RSA zálohovaný modulem HSM generovaný ve spravovaném modulu HSM|
|Cílový klíč|RSA|2 048 – bit<br />3 072 – bit<br />4 096 – bit|HSM dodavatele|Klíč, který se má přenést do spravovaného modulu HSM|

## <a name="generate-and-transfer-your-key-to-the-managed-hsm"></a>Generování a přenos klíče do spravovaného modulu HSM

Postup generování a přenos klíče do spravovaného modulu HSM:

* [Krok 1: vygenerujte KEK](#step-1-generate-a-kek)
* [Krok 2: stažení veřejného klíče KEK](#step-2-download-the-kek-public-key)
* [Krok 3: vygenerujte a připravte klíč pro přenos.](#step-3-generate-and-prepare-your-key-for-transfer)
* [Krok 4: přenos klíče do spravovaného modulu HSM](#step-4-transfer-your-key-to-managed-hsm)

### <a name="step-1-generate-a-kek"></a>Krok 1: vygenerujte KEK

KEK je klíč RSA, který se generuje ve spravovaném modulu HSM. KEK se používá k zašifrování klíče, který chcete importovat ( *cílový* klíč).

KEK musí být:
- Klíč RSA-HSM (2 048-bit; 3 072-bit; nebo 4 096-bit)
- Vygenerováno ve stejném spravovaném modulu HSM, kde máte v úmyslu importovat cílový klíč.
- Vytvořeno s povolenou operací Key nastavenou na `import`

> [!NOTE]
> KEK musí mít ' import ' jako jedinou povolenou operaci klíče. Import se vzájemně vylučuje se všemi ostatními klíčovými operacemi.

Pomocí příkazu [AZ Key trezor Key Create](/cli/azure/keyvault/key?view=azure-cli-latest&preserve-view=true#az-keyvault-key-create) vytvořte KEK, který má klíčové operace nastavené na `import` . Poznamenejte si identifikátor klíče ( `kid` ) vrácený z následujícího příkazu. ( `kid` V [kroku 3](#step-3-generate-and-prepare-your-key-for-transfer)použijete hodnotu.)

```azurecli-interactive
az keyvault key create --kty RSA-HSM --size 4096 --name KEKforBYOK --ops import --hsm-name ContosoKeyVaultHSM
```
---


### <a name="step-2-download-the-kek-public-key"></a>Krok 2: stažení veřejného klíče KEK

K stažení veřejného klíče KEK do souboru. pem použijte [AZ Key trezor Key Download](/cli/azure/keyvault/key?view=azure-cli-latest&preserve-view=true#az-keyvault-key-download) . Cílový klíč, který importujete, je zašifrovaný pomocí veřejného klíče KEK.

```azurecli-interactive
az keyvault key download --name KEKforBYOK --hsm-name ContosoKeyVaultHSM --file KEKforBYOK.publickey.pem
```
---

Přeneste soubor KEKforBYOK. PublicKey. pem do počítače offline. Tento soubor budete potřebovat v dalším kroku.

### <a name="step-3-generate-and-prepare-your-key-for-transfer"></a>Krok 3: vygenerujte a připravte klíč pro přenos.

Pokud si chcete stáhnout a nainstalovat nástroj BYOK, přečtěte si dokumentaci od dodavatele HSM. Podle pokynů dodavatele HSM vygenerujte cílový klíč a pak vytvořte balíček pro přenos klíčů (soubor BYOK). Nástroj BYOK použije `kid` z [kroku 1](#step-1-generate-a-kek) a souboru KEKforBYOK. PublicKey. pem, který jste stáhli v [kroku 2](#step-2-download-the-kek-public-key) pro vygenerování šifrovaného cílového klíče v souboru BYOK.

Přeneste soubor BYOK do připojeného počítače.

> [!NOTE] 
> Import klíčů RSA 1 024 není podporován. Import klíče eliptické křivky (ES) v současné době není podporován.
>
> **Známý problém** : import CÍLOVÉHO klíče RSA 4k z Luna HSM se podporuje jenom se firmwarem 7.4.0 nebo novějším.

### <a name="step-4-transfer-your-key-to-managed-hsm"></a>Krok 4: přenos klíče do spravovaného modulu HSM

Pokud chcete dokončit import klíče, přeneste balíček přenosu klíčů (soubor BYOK) z odpojeného počítače na počítač připojený k Internetu. Pomocí příkazu [AZ Key trezor Key import](/cli/azure/keyvault/key?view=azure-cli-latest&preserve-view=true#az-keyvault-key-import) nahrajte soubor BYOK do spravovaného HSM.

```azurecli-interactive
az keyvault key import --hsm-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file KeyTransferPackage-ContosoFirstHSMkey.byok
```

Pokud je nahrávání úspěšné, Azure CLI zobrazí vlastnosti importovaného klíče.

## <a name="next-steps"></a>Další kroky

Tento klíč chráněný HSM teď můžete použít ve spravovaném modulu HSM. Další informace najdete v [této ceně a porovnání funkcí](https://azure.microsoft.com/pricing/details/key-vault/).



