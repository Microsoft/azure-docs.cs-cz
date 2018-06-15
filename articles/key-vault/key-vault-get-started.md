---
title: Začínáme s Azure Key Vault | Dokumentace Microsoftu
description: Tento kurz vám pomůže začít s Azure Key Vault a ukáže vám, jak v Azure vytvořit zesílený kontejner, a jak ukládat a spravovat kryptografické klíče a tajné klíče v Azure.
services: key-vault
documentationcenter: ''
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 36721e1d-38b8-4a15-ba6f-14ed5be4de79
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 05/10/2018
ms.author: barclayn
ms.openlocfilehash: 58a283f1ce5bd2fd78c4fa2038c3998aea1598c9
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
ms.locfileid: "34070347"
---
# <a name="get-started-with-azure-key-vault"></a>Začínáme s Azure Key Vault
Tento článek vám pomůže začít s Azure Key Vault pomocí PowerShellu a provede vás těmito aktivitami:
- Jak v Azure vytvořit zesílený kontejner (trezor)
- Jak v Azure ukládat a spravovat kryptografické klíče a tajné kódy s využitím KeyVault
- Jak aplikace může tento klíč nebo heslo použít

Azure Key Vault je dostupný ve většině oblastí. Další informace najdete na [stránce s cenami Key Vault](https://azure.microsoft.com/pricing/details/key-vault/).

> [!NOTE]
> Tento článek neobsahuje pokyny, jak psát aplikace Azure. K těmto krokům můžete použít [ukázkovou aplikaci Azure Key Vault](https://www.microsoft.com/download/details.aspx?id=45343).

Pokyny pro rozhraní příkazového řádku pro různé platformy najdete v [tomto ekvivalentním kurzu](key-vault-manage-with-cli2.md).

## <a name="requirements"></a>Požadavky
Než budete v tomto článku pokračovat dál, potvrďte, že máte:

- **Předplatné Azure**. Pokud žádné nemáte, můžete si zaregistrovat [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/)
- **Azure PowerShell**, **minimálně verze 1.1.0**. Chcete-li nainstalovat Azure PowerShell a přidružit ho ke svému předplatnému Azure, prohlédněte si téma [Instalace a konfigurace Azure PowerShellu](/powershell/azure/overview). Pokud jste již Azure PowerShell nainstalovali, ale neznáte jeho verzi, z konzoly Azure PowerShell zadejte `(Get-Module azure -ListAvailable).Version`. Máte-li nainstalovaný Azure PowerShell ve verzi 0.9.1 až 0.9.8, stále můžete tento kurz použít, pouze s menšími změnami. Například musíte použít příkaz `Switch-AzureMode AzureResourceManager` a zároveň se změnily některé příkazy Azure Key Vault. Seznam rutin Key Vault pro verze 0.9.1 až 0.9.8 naleznete v tématu [Rutiny Azure Key Vault](/powershell/module/azurerm.keyvault/#key_vault).
- **Aplikaci, kterou je možné nakonfigurovat tak, aby používala Key Vault**. Vzorová aplikace je k dispozici ve službě [Microsoft Download Center](http://www.microsoft.com/download/details.aspx?id=45343). Pokyny naleznete v souvisejícím souboru **Readme**.

>[!NOTE]
Tento článek předpokládá základní znalost PowerShellu a Azure. Další informace o PowerShellu najdete v tématu [Začínáme s prostředím Windows PowerShell](https://technet.microsoft.com/library/hh857337.aspx).

Podrobnou nápovědu k jakékoli rutině v tomto kurzu získáte pomocí rutiny **Get-Help**.

```powershell-interactive
Get-Help <cmdlet-name> -Detailed
```
    
Například pro získání nápovědy pro rutinu **Connect-AzureRmAccount** zadejte:

```PowerShell
Get-Help Connect-AzureRmAccount -Detailed
```

Pro seznámení se s modelem nasazení Azure Resource Manager v Azure PowerShellu si také můžete přečíst následující články:

* [Jak nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/overview)
* [Použití Azure PowerShellu s Resource Managerem](../powershell-azure-resource-manager.md)

## <a id="connect"></a>Připojení k předplatným
Spusťte relaci Azure PowerShellu a přihlaste se k účtu Azure pomocí následujícího příkazu:  

```PowerShell
Connect-AzureRmAccount
```

>[!NOTE]
 Pokud používáte konkrétní instanci Azure, použijte parametr -Environment. Příklad: 
 ```powershell
 Connect-AzureRmAccount –Environment (Get-AzureRmEnvironment –Name AzureUSGovernment)
 ```

V automaticky otevřeném okně prohlížeče zadejte svoje uživatelské jméno a heslo k účtu Azure. Azure PowerShell získá všechna předplatná přidružená k tomuto účtu a ve výchozím nastavení použije první předplatné.

Máte-li více předplatných a chcete určit jedno konkrétní, které se má použít pro Azure Key Vault, zadejte následující příkaz k zobrazení předplatných pro váš účet:

```powershell
Get-AzureRmSubscription
```

Poté pro určení předplatného, které se má použít, zadejte:

```powershell
Set-AzureRmContext -SubscriptionId <subscription ID>
```

Další informace o konfiguraci prostředí Azure PowerShell najdete v tématu [Instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/overview).

## <a id="resource"></a>Vytvoření nové skupiny prostředků
Používáte-li Azure Resource Manager, pak se všechny související prostředky vytváří v uvnitř skupiny prostředků. Pro účely tohoto kurzu vytvoříme novou skupinu prostředků s názvem **ContosoResourceGroup**:

```powershell
New-AzureRmResourceGroup –Name 'ContosoResourceGroup' –Location 'East US'
```

## <a id="vault"></a>Vytvoření trezoru klíčů
Pro vytvoření trezoru klíčů použijte rutinu [New-AzureRmKeyVault](/powershell/module/azurerm.keyvault/new-azurermkeyvault). Tato rutina má tři povinné parametry: **název skupiny prostředků**, **název trezoru klíčů** a **zeměpisné umístění**.

Pokud například použijete:
- Název trezoru **ContosoKeyVault**
- Název skupiny prostředků **ContosoResourceGroup**
- Umístění **Východní USA**

měli byste zadat

```powershell
New-AzureRmKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East US'
```
![Výstup po dokončení příkazu pro vytvoření trezoru klíčů](./media/key-vault-get-started/output-after-creating-keyvault.png)

Výstup této rutiny zobrazuje vlastnosti trezoru klíčů, který jste vytvořili. Dvě nejdůležitější vlastnosti jsou:

* **Název trezoru**: V tomto příkladu je to **ContosoKeyVault**. Tento název budete používat pro další rutiny Key Vault.
* **Identifikátor URI trezoru:** V tomto příkladu je to https://contosokeyvault.vault.azure.net/. Aplikace, které používají váš trezor prostřednictvím REST API musí používat tento identifikátor URI.

Váš účet Azure je nyní oprávněn provádět nad tímto trezorem klíčů všechny operace. Zatím k tomu není oprávněn nikdo jiný.

> [!NOTE]
> Při pokusu o vytvoření nového trezoru klíčů se může zobrazit chyba **Předplatné není zaregistrované k používání oboru názvů „Microsoft.KeyVault“**. Pokud se tato chyba zobrazí, spusťte `Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.KeyVault"`. Po úspěšném dokončení registrace můžete znovu spustit příkaz New-AzureRmKeyVault. Další informace najdete v tématu [Register-AzureRmResourceProvider](/powershell/module/azurerm.resources/register-azurermresourceprovider).
>
>

## <a id="add"></a>Přidání klíče nebo tajného klíče do trezoru
Existuje několik různých způsobů, které můžete potřebovat pro interakci se službou Key Vault a klíči nebo tajnými kódy.

### <a name="azure-key-vault-generates-a-software-protected-key"></a>Azure Key Vault generuje softwarově chráněný klíč

Pokud chcete, aby pro vás služba Azure Key Vault vytvořila softwarově chráněný klíč, použijte rutinu [Add-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/add-azurekeyvaultkey) a zadejte:

```powershell
$key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoFirstKey' -Destination 'Software'
```
pro zobrazení URI pro tento typ klíče:
```powershell
$key.id
```

Na klíč, který vytvoříte nebo nahrajete do Azure Key Vaultu, můžete odkazovat pomocí jeho identifikátoru URI. K získání aktuální verze můžete použít **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey** a tuto konkrétní verzi získáte pomocí **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87**.  

### <a name="importing-an-existing-pfx-file-into-azure-key-vault"></a>Import existujícího souboru PFX do Azure Key Vaultu

V případě existujících klíčů uložených v souboru pfx, který chcete nahrát do Azure Key Vaultu, se použijí jiné kroky. Příklad:
- Pokud máte existující softwarově chráněný klíč v souboru .PFX.
- Soubor pfx má název softkey.pfx. 
- Tento soubor je uložený v jednotce C.

Můžete zadat:

```powershell
$securepfxpwd = ConvertTo-SecureString –String '123' –AsPlainText –Force  // This stores the password 123 in the variable $securepfxpwd
```

Poté zadejte následující kód, abyste importovali klíč ze souboru .PFX, který chrání klíč softwarem, do služby Key Vault:

```powershell
$key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoImportedPFX' -KeyFilePath 'c:\softkey.pfx' -KeyFilePassword $securepfxpwd
```

Identifikátor URI tohoto klíče zobrazíte zadáním:

```powershell
$Key.id
```
Chcete-li zobrazit klíč, zadejte: 

```powershell
Get-AzureKeyVaultKey –VaultName 'ContosoKeyVault'
```
Pokud chcete zobrazit vlastnosti souboru PFX na portálu, to, co uvidíte, bude podobné jako na následujícím obrázku.

![Jak certifikát vypadá na portálu](./media/key-vault-get-started/imported-pfx.png)
### <a name="to-add-a-secret-to-azure-key-vault"></a>Přidání tajného kódu do služby Azure Key Vault

Pokud chcete do trezoru služby Azure Key Vault přidat tajný kód v podobě hesla s názvem SQLPassword a hodnotou Pa$$w0rd, nejprve převeďte hodnotu Pa$$w0rd na zabezpečený řetězec zadáním:

```powershell    
$secretvalue = ConvertTo-SecureString 'Pa$$w0rd' -AsPlainText -Force
```

Potom zadejte:

```powershell
$secret = Set-AzureKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'SQLPassword' -SecretValue $secretvalue
```


Nyní na toto heslo, které jste přidali do Azure Key Vault, můžete odkazovat pomocí jeho identifikátoru URI. Pomocí **https://ContosoVault.vault.azure.net/secrets/SQLPassword** vždy získáte aktuální verzi a pomocí **https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d** získáte tuto konkrétní verzi.

Chcete-li zobrazit identifikátor URI tajného klíče, zadejte:

```powershell
$secret.Id
```
Pokud chcete zobrazit tajný kód, zadejte `Get-AzureKeyVaultSecret –VaultName 'ContosoKeyVault'`. další možností je zobrazit tajný kód na portálu.

![Tajný kód](./media/key-vault-get-started/secret-value.png)

Pokud chcete zobrazit hodnotu v tajném kódu jako prostý text:
```powershell
(get-azurekeyvaultsecret -vaultName "Contosokeyvault" -name "SQLPassword").SecretValueText
```
Nyní jsou váš trezor klíčů a klíč nebo tajný klíč připravené pro použití aplikacemi. Aby aplikace mohly klíče používat, musíte je nejdříve autorizovat.  

## <a id="register"></a>Registrace aplikace s Azure Active Directory
Tento krok obvykle provádí vývojář na samostatném počítači. Není pro Azure Key Vault specifický. Podrobné pokyny k registraci aplikace v Azure Active Directory najdete v článku nazvaném [Integrace aplikací se službou Azure Active Directory](../active-directory/develop/active-directory-integrating-applications.md) nebo v článku věnovaném [použití portálu k vytvoření aplikace Azure Active Directory a instančního objektu, který má přístup k prostředkům](../azure-resource-manager/resource-group-create-service-principal-portal.md).

> [!IMPORTANT]
> Pro dokončení kurzu musí být váš účet, trezor i aplikace, kterou budete v tomto kroku registrovat, ve stejném adresáři Azure.


Aplikace, které používají trezor klíčů, se musí ověřit pomocí tokenu z Azure Active Directory. K tomu je potřeba, aby majitel aplikace nejprve zaregistroval aplikaci ve vlastním Azure Active Directory. Na konci registrace obdrží majitel aplikace následující hodnoty:

- **ID aplikace** 
- **Ověřovací klíč** (také označovaný jako sdílený tajný kód) 

Pro získání tokenu musí aplikace obě tyto hodnoty poskytnout do Azure Active Directory. Záleží na aplikaci, jak je k tomu nakonfigurovaná. Pro [ukázkovou aplikaci Key Vault](https://www.microsoft.com/download/details.aspx?id=45343) nastavuje majitel tyto hodnoty v souboru app.config.


Chcete-li zaregistrovat aplikaci v Azure Active Directory:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Nalevo klikněte na **registrace aplikací**. Pokud se registrace aplikací nezobrazí, klikněte na **Další služby** a vyhledejte je tam.  
>[!NOTE]
Musíte vybrat adresář obsahující předplatné Azure, které jste použili pro vytvoření trezoru klíčů. 
3. Klikněte na **Registrace nové aplikace**.
4. V okně **Vytvořit** zadejte název vaší aplikace a poté vyberte **WEBOVÁ APLIKACE NEBO WEBOVÉ ROZHRANÍ API** (výchozí možnost) a zadejte **PŘIHLAŠOVACÍ ADRESU URL** pro vaši webovou aplikaci. Pokud tyto informace v tuto chvíli nemáte, můžete si je pro tento krok vymyslet (můžete například zadat http://test1.contoso.com). Nezáleží na tom, zda tyto weby existují. 

    ![Registrace nové aplikace](./media/key-vault-get-started/new-application-registration.png)
    >[!WARNING]
    Ověřte, že pokud se v nastavení nezobrazila položka **klíče**, zvolili jste **WEBOVÁ APLIKACE NEBO WEBOVÉ ROZHRANÍ API**.

5. Klikněte na tlačítko **Vytvořit**.
6. Po dokončení registrace aplikace se zobrazí seznam registrovaných aplikací. Najděte aplikaci, který jste právě zaregistrovali, a klikněte na ni.
7. Klikněte v okně **Registrovaná aplikace** a zkopírujte **ID aplikace**.
8. Klikněte na **Veškerá nastavení**.
9. V okně **Nastavení** klikněte na **klíče**.
9. Do pole **Popis klíče** zadejte popis, vyberte dobu trvání a potom klikněte na **ULOŽIT**. Stránka se obnoví a zobrazí hodnotu klíče. 
10. Informace v polích **ID aplikace** a **Klíč** použijete v dalším kroku k nastavení oprávnění pro váš trezor.

## <a id="authorize"></a>Autorizace aplikace pro použití klíče nebo tajného klíče
Aplikaci můžete autorizovat pro přístup ke klíči nebo tajnému klíči v trezoru dvěma způsoby.

### <a name="using-powershell"></a>Pomocí prostředí PowerShell
Pokud chcete použít PowerShell, použijte rutinu [Set-AzureRmKeyVaultAccessPolicy](/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy).

Máte-li například trezor s názvem **ContosoKeyVault** a ID klienta aplikace, kterou chcete ověřit, má hodnotu 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed, můžete aplikaci autorizovat pro dešifrování a podepisování pomocí klíčů ve vašem trezoru spuštěním následujícího příkazu:

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToKeys decrypt,sign
```

Chcete-li tu samou aplikaci autorizovat pro čtení tajných klíčů v trezoru, spusťte následující:

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToSecrets Get
```
### <a name="using-the-azure-portal"></a>Použití webu Azure Portal
Změna ověřování aplikace na používání klíčů nebo tajných klíčů:
1. V okně prostředku služby Key Vault vyberte **Zásady přístupu**.
2. Klikněte na tlačítko [+ Přidat nový] v horní části okna.
3. Klikněte na **Výběr objektu zabezpečení** a vyberte aplikaci, kterou jste vytvořili dříve.
4. V rozevíracím seznamu **Oprávnění klíče** vyberte Dešifrovat a Podepisovat, abyste aplikaci autorizovali k dešifrování a podepisování pomocí klíčů ve vašem trezoru.
5. V rozevíracím seznamu **Oprávnění tajného klíče** vyberte Získat, abyste aplikaci povolili čtení tajných klíčů v trezoru.

## <a id="HSM"></a>Použití modulu hardwarového zabezpečení (HSM)
Pro lepší kontrolu můžete importovat nebo generovat klíče v modulech hardwarového zabezpečení (HSM), které nikdy neopustí hranice HSM. Moduly hardwarového zabezpečení jsou ověřené podle standardu FIPS 140-2 Level 2. Pokud se vás tento požadavek netýká, přeskočte tuto část a přejděte k části [Odstranění trezoru klíčů, přidružených klíčů a tajných klíčů](#delete).

Pokud chcete vytvořit tyto klíče s ochranou HSM, musíte použít [vrstvu služby Azure Key Vault Premium pro podporu klíčů s ochranou HSM](https://azure.microsoft.com/pricing/free-trial/). Kromě toho tato funkce není dostupná pro Azure China.

Při vytváření trezoru klíčů přidejte parametr **-SKU**:

```powershell
New-AzureRmKeyVault -VaultName 'ContosoKeyVaultHSM' -ResourceGroupName 'ContosoResourceGroup' -Location 'East US' -SKU 'Premium'
```


Do tohoto trezoru klíčů můžete přidat klíče chráněné softwarem (jak jsme ukázali výše) a klíče chráněné pomocí HSM. Chcete-li vytvořit klíč chráněný pomocí HSM, nastavte parametr **-Destination** na „HSM“:

```powershell
$key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -Destination 'HSM'
```

Pro import klíče ze souboru .PFX můžete ve vašem počítači použít následující příkaz. Tento příkaz importuje klíč do HSM ve službě Key Vault:

```powershell
$key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -KeyFilePath 'c:\softkey.pfx' -KeyFilePassword $securepfxpwd -Destination 'HSM'
```

Další příkaz importuje balíček „přineste si vlastní klíč“ (BYOK). Tento scénář umožňuje vygenerovat klíč v místním modulu HSM a přenést ho do modulů HSM ve službě Key Vault, aniž by klíč opustil hranice modulu HSM:

```powershell
$key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -KeyFilePath 'c:\ITByok.byok' -Destination 'HSM'
```

Podrobnější pokyny o tom, jak generovat tento balíček BYOK naleznete v tématu [Generování a přenos klíčů chráněných pomocí HSM pro Azure Key Vault](key-vault-hsm-protected-keys.md).

## <a id="delete"></a>Odstranění trezoru klíčů, přidružených klíčů a tajných klíčů
Pokud již nepotřebujete trezor klíčů, ani klíče a tajné klíče, které obsahuje, můžete trezor klíčů odstranit pomocí rutiny [Remove-AzureRmKeyVault](/powershell/module/azurerm.keyvault/remove-azurermkeyvault):

```powershell
Remove-AzureRmKeyVault -VaultName 'ContosoKeyVault'
```

Nebo můžete odstranit celou skupinu prostředků Azure, která zahrnuje trezor klíčů a všechny další prostředky, které jste do skupiny zahrnuli.

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName 'ContosoResourceGroup'
```

## <a id="other"></a>Další rutiny Azure PowerShellu
Další užitečné příkazy pro správu Azure Key Vault:

- `$Keys = Get-AzureKeyVaultKey -VaultName 'ContosoKeyVault'`: Tento příkaz načte tabelární zobrazení všech klíčů a vybraných vlastnosti.
- `$Keys[0]`: Tento příkaz zobrazí úplný seznam vlastností zadaného klíče.
- `Get-AzureKeyVaultSecret`: Tento příkaz vypíše tabelární zobrazení všech názvů tajných klíčů a vybraných vlastností.
- `Remove-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoFirstKey'`: Příklad odebrání určitého klíče.
- `Remove-AzureKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'SQLPassword'`: Příklad odebrání určitého tajného klíče.

## <a name="next-steps"></a>Další kroky

- Souhrnné informace o Azure Key Vault naleznete v tématu [Co je Azure Key Vault?](key-vault-whatis.md).
- Chcete-li mít přehled o využívání trezoru klíčů, podívejte se na kurz [Protokolování v Azure Key Vault](key-vault-logging.md).
- Chcete-li používat Azure Key Vault ve webové aplikaci, podívejte se na navazující kurz [Použití Azure Key Vault z webové aplikace](key-vault-use-from-web-application.md).
- Programátorské reference najdete v [příručce pro vývojáře Azure Key Vault](key-vault-developers-guide.md).
- Seznam nejnovějších rutin Azure PowerShellu pro Azure Key Vault naleznete v tématu [Rutiny Azure Key Vault](/powershell/module/azurerm.keyvault/#key_vault).
