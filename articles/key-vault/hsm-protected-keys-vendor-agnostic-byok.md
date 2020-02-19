---
title: Postup generování a přenosu klíčů chráněných HSM pro Azure Key Vault-Azure Key Vault | Microsoft Docs
description: Tento článek vám může pomáhat při plánování, generování a přenosu vlastních klíčů chráněných HSM pro použití s Azure Key Vault. Označuje se také jako BYOK nebo Přineste si vlastní klíč.
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: ambapat
ms.openlocfilehash: 2f5269587d222be9a1628b72c1f3f0dc1b105f3c
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2020
ms.locfileid: "77461737"
---
# <a name="import-hsm-protected-keys-to-key-vault-preview"></a>Import klíčů chráněných HSM do Key Vault (Preview)

> [!NOTE]
> Tato funkce je ve verzi Preview a je dostupná jenom v oblastech **východní USA 2 EUAP** a **střed USA EUAP** . 

Pro zvýšení zabezpečení při použití Azure Key Vault můžete importovat nebo generovat klíče v modulech hardwarového zabezpečení (HSM), které nikdy nezanechají hranici HSM. Tento scénář se často označuje jako *Přineste si vlastní klíč*nebo BYOK. Azure Key Vault používá hardwarového nshieldou rodinu HSM (FIPS 140-2 Level 2) k ochraně vašich klíčů.

Informace v tomto tématu vám pomůžou při plánování, generování a přenosu vlastních klíčů chráněných HSM, které se používají s Azure Key Vault.

> [!NOTE]
> Tato funkce není pro Azure Čína 21Vianet k dispozici. 
> 
> Tato metoda importu je dostupná jenom pro [podporované HSM](#supported-hsms). 

Další informace o Azure Key Vault najdete v tématu [co je Azure Key Vault?](key-vault-overview.md)  Úvodní kurz, který zahrnuje vytvoření trezoru klíčů pro klíče chráněné HSM, najdete v tématu [co je Azure Key Vault?](key-vault-overview.md).

## <a name="overview"></a>Přehled

* Vygenerujte klíč (v trezoru klíčů, který se označuje jako klíč pro výměnu klíčů nebo KEK). Musí se jednat o klíč RSA-HSM s názvem import jako jediná operace klíče. Klíče RSA-HSM podporuje jenom SKU trezoru klíčů úrovně Premium.
* Stažení veřejného klíče KEK jako souboru. pem
* Přeneste veřejný klíč KEK do offline pracovní stanice připojené k místnímu modulu HARDWAROVÉho zabezpečení.
* V offline pracovní stanici použijte k vytvoření souboru BYOK nástroj BYOK, který poskytuje dodavatel HSM. 
* Cílový klíč je zašifrovaný pomocí KEK, který zůstane zašifrovaný, dokud se nepřenáší do Azure Key Vault HSM. Místní modul hardwarového zabezpečení (HSM) opouští jenom zašifrovaná verze vašeho klíče.
* KEK, který je generován v rámci Azure Key Vault HSM a nelze exportovat. HSM vynutila, že nemůžete bez jasné verze KEK mimo Key Vault HSM.
* KEK musí být ve stejném trezoru klíčů, kam se má cílový klíč importovat.
* Po nahrání souboru BYOK do Key Vault Key Vault HSM použít privátní klíč KEK k dešifrování cílového materiálu klíče a naimportuje ho jako klíč HSM. Tato operace probíhá zcela uvnitř Key Vault HSM a cílový klíč vždy zůstává v hranici ochrany HSM.

## <a name="prerequisites"></a>Požadavky

Seznam požadavků pro Přineste si vlastní klíč (BYOK) pro Azure Key Vault najdete v následující tabulce.

| Požadavek | Další informace |
| --- | --- |
| Předplatné Azure |Pokud chcete vytvořit Azure Key Vault, potřebujete předplatné Azure: [Zaregistrujte se do bezplatné zkušební verze](https://azure.microsoft.com/pricing/free-trial/) . |
| Trezor klíčů (SKU úrovně Premium) pro import klíčů chráněných pomocí HSM |Další informace o úrovních služby a možnostech pro Azure Key Vault najdete na webu [Azure Key Vault s cenami](https://azure.microsoft.com/pricing/details/key-vault/) . |
| Modul HARDWAROVÉho zabezpečení z podporovaného seznamu HSM spolu s nástrojem BYOK a pokyny, které poskytuje dodavatel HSM. | Musíte mít přístup k modulu hardwarového zabezpečení a základnímu provoznímu znalostí vaší HSM. Viz článek [podporované HSM](#supported-hsms). |
| Azure CLI verze 2.1.0 nebo novější | Další informace najdete v tématu [instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) .|

## <a name="supported-hsms"></a>Podporované HSM

|Název dodavatele HSM|Podporované modely HSM|Další podrobnosti|
|---|---|---|
|Thales|SafeNet Luna HSM 7 Family s firmwarem verze 7,3 nebo novější| [Nástroj SafeNet Luna BYOK a dokumentace](https://supportportal.thalesgroup.com/csm?id=kb_article_view&sys_kb_id=3892db6ddb8fc45005c9143b0b961987&sysparm_article=KB0021016)|


> [!NOTE]
> Import klíčů chráněných modulem HSM z řady podpůrný software nCipher hardwarového nShield HSM [použití starší verze procesu BYOK](hsm-protected-keys-legacy.md)


## <a name="generate-and-transfer-your-key-to-azure-key-vault-hsm"></a>Generování a přenos klíče pro Azure Key Vault HSM

Pomocí následujících kroků vygenerujte a přeneste klíč do Azure Key Vault HSM:

* [Krok 1: vygenerujte KEK](#step-1-generate-a-kek)
* [Krok 2: stažení veřejného klíče KEK](#step-2-download-kek-public-key)
* [Krok 3: vygenerujte a připravte klíč pro přenos.](#step-3-generate-and-prepare-your-key-for-transfer)
* [Krok 4: přenesení klíče do Azure Key Vault](#step-4-transfer-your-key-to-azure-key-vault)

### <a name="step-1-generate-a-kek"></a>Krok 1: vygenerujte KEK

KEK (klíč výměny klíčů) je klíč RSA generovaný v modulu HARDWAROVÉho zabezpečení Key Vault. Tento klíč se používá k zašifrování klíče, který se má importovat (cílový klíč).

KEK musí být:
1. klíč **RSA-HSM** (2048 nebo 3072 bit nebo 4096-bit)
2. vygenerováno ve stejném trezoru klíčů, do kterého chcete importovat cílový klíč.
3. Vytvořeno s povolenou operací Key nastavenou na **Import**

Pomocí příkazu [AZ Key trezor Key Create](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create) vytvořte KEK s klíčovými operacemi nastavenými na import. Poznamenejte si identifikátor klíče Kid vrácený z následujícího příkazu. Budete ho potřebovat v [kroku 3](#step-3-generate-and-prepare-your-key-for-transfer).


```azurecli
az keyvault key create --kty RSA-HSM --size 4096 --name KEKforBYOK --ops import --vault-name ContosoKeyVaultHSM
```

### <a name="step-2-download-kek-public-key"></a>Krok 2: stažení veřejného klíče KEK

Ke stažení veřejného klíče KEK do souboru. pem použijte [stažení klíče AZ Key trezor](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-download) . Cílový klíč, který importujete, je zašifrovaný pomocí veřejného klíče KEK.

```azurecli
az keyvault key download --name KEKforBYOK --vault-name ContosoKeyVaultHSM --file KEKforBYOK.publickey.pem
```

Přeneste soubor KEKforBYOK. PublicKey. pem do offline pracovní stanice. Tento soubor budete potřebovat v dalším kroku.

### <a name="step-3-generate-and-prepare-your-key-for-transfer"></a>Krok 3: vygenerujte a připravte klíč pro přenos.

Pokud si chcete stáhnout a nainstalovat nástroj BYOK, přečtěte si prosím dokumentaci od dodavatele HSM. Pomocí instrukce od dodavatele HSM vygenerujte cílový klíč a pak vytvořte balíček pro přenos klíčů (soubor BYOK). Nástroj BYOK použije identifikátor klíče z [kroku 1](#step-1-generate-a-kek) a KEKforBYOK. PublicKey. pem, který jste stáhli v [kroku 2](#step-2-download-kek-public-key) pro vygenerování šifrovaného CÍLOVÉHO klíče v souboru BYOK.

Přeneste soubor BYOK na připojenou pracovní stanici.

> [!NOTE] 
> Cílový klíč musí být klíčem RSA o velikosti 2048-bit nebo 3072-bit nebo 4096-bit. Import klíčů s eliptickou křivkou není v současné době podporován.
> <br/><strong>Známý problém:</strong> Import cílového klíče RSA 4K z SafeNet Luna HSM se nezdařil. Když se problém vyřeší, tento dokument se aktualizuje.

### <a name="step-4-transfer-your-key-to-azure-key-vault"></a>Krok 4: přenesení klíče do Azure Key Vault

V tomto posledním kroku přeneste balíček pro přenos klíčů (soubor BYOK) z odpojené pracovní stanice na pracovní stanici připojené k Internetu a potom pomocí příkazu [AZ Key trezor Key import](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import) nahrajte soubor BYOK Azure Key Vault HSM, aby se import klíče dokončil.

```azurecli
az keyvault key import --vault-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file KeyTransferPackage-ContosoFirstHSMkey.byok
```

Pokud je nahrávání úspěšné, zobrazí se zobrazené vlastnosti klíče, který jste právě naimportovali.

## <a name="next-steps"></a>Další kroky

Tento klíč chráněný HSM teď můžete použít ve vašem trezoru klíčů. Další informace najdete v této ceně a [porovnání](https://azure.microsoft.com/pricing/details/key-vault/)funkcí.
