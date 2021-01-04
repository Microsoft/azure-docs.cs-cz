---
title: Přehled obnovení Azure Key Vault | Microsoft Docs
description: Funkce Key Vault Recovery jsou navržené tak, aby se zabránilo nechtěnému nebo škodlivému odstranění trezoru klíčů a tajných klíčů, klíčů a certifikátů uložených v trezoru klíčů.
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.author: mbaldwin
author: msmbaldwin
manager: rkarlin
ms.date: 09/30/2020
ms.openlocfilehash: 258d100276b20ea2437ebffb1473492a247657e8
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/20/2020
ms.locfileid: "97704210"
---
# <a name="azure-key-vault-recovery-management-with-soft-delete-and-purge-protection"></a>Azure Key Vault správu obnovení s ochranou pomocí obnovitelného odstranění a mazání

Tento článek se zabývá dvěma funkcemi pro obnovení Azure Key Vault a ochranou proti odstranění a vyprázdnění. Tento dokument obsahuje přehled těchto funkcí a ukazuje, jak je spravovat prostřednictvím Azure Portal, Azure CLI a Azure PowerShell.

Další informace o Key Vault najdete v tématu.
- [Přehled služby Key Vault](overview.md)
- [Přehled Azure Key Vault klíčů, tajných kódů a certifikátů](about-keys-secrets-certificates.md)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [můžete ho vytvořit zdarma](https://azure.microsoft.com/free/dotnet) .
* [Modul PowerShellu](https://docs.microsoft.com/powershell/azure/install-az-ps).
* [Azure CLI](/cli/azure/install-azure-cli)
* Key Vault – můžete ho vytvořit pomocí [Azure Portal](../general/quick-create-portal.md) [Azure CLI](../general/quick-create-cli.md)nebo [Azure PowerShell](../general/quick-create-powershell.md)

## <a name="what-are-soft-delete-and-purge-protection"></a>Co jsou ochrana obnovitelného odstranění a mazání

Ochrana proti [odstranění](soft-delete-overview.md) a vyprázdnění je ve dvou různých funkcích pro obnovení trezoru klíčů.

> [!IMPORTANT]
> Zapnutí obnovitelného odstranění je důležité, aby bylo zajištěno, že vaše trezory klíčů a přihlašovací údaje jsou chráněny před náhodným odstraněním. Zapnutí obnovitelného odstranění je ale považováno za zásadní změnu, protože může vyžadovat změnu logiky aplikace nebo poskytnutí dalších oprávnění k objektům služby. Než zapnete obnovitelné odstranění pomocí níže uvedených pokynů, ujistěte se, že je vaše aplikace kompatibilní se změnou v tomto dokumentu [ .](soft-delete-change.md)

**Obnovitelné odstranění** je navrženo tak, aby nedocházelo k nechtěnému odstranění trezoru klíčů a klíčů, tajných kódů a certifikátů uložených v trezoru klíčů. Představte si obnovitelné odstranění podobně jako odpadkový koš. Když odstraníte Trezor klíčů nebo objekt trezoru klíčů, zůstane možné, že bude možné obnovit uživatelsky nastavitelnou dobu uchování nebo výchozí dobu 90 dnů. Trezory klíčů ve stavu tichého odstranění je také možné **Vymazat** , což znamená, že jsou trvale odstraněny. To umožňuje znovu vytvořit trezory klíčů a objekty trezoru klíčů se stejným názvem. Obnovení a odstranění trezorů klíčů a objektů vyžaduje oprávnění zásad zvýšeného přístupu. **Po povolení obnovitelného odstranění ho nejde zakázat.**

Je důležité si uvědomit, že **názvy trezorů klíčů jsou globálně jedinečné**, takže nebudete moct vytvořit Trezor klíčů se stejným názvem jako Trezor klíčů ve stavu tichého odstranění. Podobně jsou názvy klíčů, tajných kódů a certifikátů jedinečné v rámci trezoru klíčů. Nebudete moct vytvořit tajný klíč, klíč nebo certifikát se stejným názvem jako jiný ve stavu tichého odstranění.

Funkce **Vyčištění ochrany** je navržená tak, aby zabránila odstranění trezoru klíčů, klíčů, tajných kódů a certifikátů prostřednictvím škodlivého programu Insider. Tuto složku si můžete představit jako koš s zámkem na základě času. Během konfigurovatelné doby uchovávání můžete položky kdykoli obnovit. **Trezor klíčů nebudete moct trvale odstranit ani vyprázdnit, dokud dobu uchování neuplyne.** Jakmile doba uchování uplyne, Trezor klíčů nebo objekt trezoru klíčů se vyprázdní automaticky.

> [!NOTE]
> Ochrana vyprázdnění je navržená tak, že žádná role správce ani oprávnění nemůže přepsat, zakázat nebo obejít ochranu vyprázdnění. **Jakmile je ochrana vyprázdnění povolena, nelze ji zakázat ani přepsat kýmkoli včetně společnosti Microsoft.** To znamená, že musíte obnovit odstraněný Trezor klíčů nebo počkat na uplynutí doby uchovávání, než znovu použijete název trezoru klíčů.

Další informace o obnovitelném odstranění najdete v tématu [přehled Azure Key Vaultho obnovitelného odstranění](soft-delete-overview.md) .

# <a name="azure-portal"></a>[Azure Portal](#tab/azure-portal)

## <a name="verify-if-soft-delete-is-enabled-on-a-key-vault-and-enable-soft-delete"></a>Ověřte, jestli je v trezoru klíčů povolené obnovitelné odstranění a povolit obnovitelné odstranění.

1. Přihlaste se k webu Azure Portal.
1. Vyberte svůj Trezor klíčů.
1. Klikněte na okno Vlastnosti.
1. Ověřte, zda je přepínač vedle možnosti obnovitelné – odstranění nastaven na možnost povolit obnovení.
1. Pokud není v trezoru klíčů povolené obnovitelné odstranění, klikněte na přepínač a povolte obnovitelné odstranění a klikněte na Uložit.

:::image type="content" source="../media/key-vault-recovery-1.png" alt-text="V případě vlastností je měkké odstranění zvýrazněno, jak je hodnota, která má být povolena.":::

## <a name="grant-access-to-a-service-principal-to-purge-and-recover-deleted-secrets"></a>Udělení přístupu k instančnímu objektu pro vyprázdnění a obnovení odstraněných tajných klíčů

1. Přihlaste se k webu Azure Portal.
1. Vyberte svůj Trezor klíčů.
1. Klikněte na okno zásady přístupu.
1. V tabulce Najděte řádek objektu zabezpečení, ke kterému chcete udělit přístup (nebo přidejte nový objekt zabezpečení).
1. Pro klíče, certifikáty a tajné klíče klikněte na rozevírací seznam.
1. Posuňte se do dolní části rozevíracího seznamu a klikněte na tlačítko obnovit a vymazat.
1. K provedení většiny operací budou mít objekty zabezpečení taky funkce Get a list.

:::image type="content" source="../media/key-vault-recovery-2.png" alt-text="V levém navigačním podokně se zvýrazní zásady přístupu. V části zásady přístupu se zobrazuje rozevírací seznam pozice v tajnosti a jsou vybrány čtyři položky: získat, vypsat, obnovit a vyprázdnit.":::

## <a name="list-recover-or-purge-a-soft-deleted-key-vault"></a>Vypíše, obnoví nebo vyprázdní trezor klíčů s odstraněnými přístředníkem.

1. Přihlaste se k webu Azure Portal.
1. V horní části stránky klikněte na panel hledání.
1. V části Nedávné služby klikněte na Key Vault. Neklepejte na jeden Trezor klíčů.
1. V horní části obrazovky klikněte na možnost Správa odstraněných trezorů.
1. Na pravé straně obrazovky se otevře podokno kontextu.
1. Vyberte své předplatné.
1. Pokud byl váš Trezor klíčů měkký, zobrazí se v podokně kontext na pravé straně.
1. Pokud existuje příliš mnoho trezorů, můžete v dolní části podokna kontextu kliknout na načíst více nebo získat výsledky pomocí CLI nebo PowerShellu.
1. Jakmile najdete trezor, který chcete obnovit nebo vymazat, zaškrtněte políčko vedle něj.
1. Pokud chcete obnovit Trezor klíčů, vyberte možnost obnovit v dolní části podokna kontextu.
1. Pokud chcete trvale odstranit Trezor klíčů, vyberte možnost vyprázdnění.

:::image type="content" source="../media/key-vault-recovery-3.png" alt-text="V trezorech klíčů se zvýrazní možnost Správa odstraněných trezorů.":::

:::image type="content" source="../media/key-vault-recovery-4.png" alt-text="V části spravovat odstraněné trezory klíčů je zvýrazněný a vybraný jenom seznam trezorů klíčů a tlačítko obnovit je zvýrazněné.":::

## <a name="list-recover-or-purge-soft-deleted-secrets-keys-and-certificates"></a>Vypíše, obnoví nebo odstraní obnovitelné odstraněné tajné klíče, klíče a certifikáty.

1. Přihlaste se k webu Azure Portal.
1. Vyberte svůj Trezor klíčů.
1. Vyberte okno odpovídající typu tajného klíče, který chcete spravovat (klíče, tajné kódy nebo certifikáty).
1. V horní části obrazovky klikněte na Správa odstraněných (klíčů, tajných klíčů nebo certifikátů).
1. Na pravé straně obrazovky se zobrazí podokno kontextu.
1. Pokud se váš tajný klíč, klíč nebo certifikát nezobrazí v seznamu, není ve stavu tiché odstranění.
1. Vyberte tajný klíč, klíč nebo certifikát, který chcete spravovat.
1. V dolní části podokna kontextu vyberte možnost obnovení nebo vymazání.

:::image type="content" source="../media/key-vault-recovery-5.png" alt-text="V části klíče se zvýrazní možnost Správa odstraněných klíčů.":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

## <a name="key-vault-cli"></a>Key Vault (CLI)

* Ověřte, jestli je u trezoru klíčů povolené obnovitelné odstranění.

    ```azurecli
    az keyvault show --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}
    ```

* Povolit obnovitelné odstranění u trezoru klíčů

    Všechny nové trezory klíčů mají ve výchozím nastavení povolené obnovitelné odstranění. Pokud máte v současné době Trezor klíčů, který nemá povolené obnovitelné odstranění, povolte obnovitelné odstranění pomocí následujícího příkazu.

    ```azurecli
    az keyvault update --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME} --enable-soft-delete true
    ```

* Odstranění trezoru klíčů (obnovitelné, pokud je povolené obnovitelné odstranění)

    ```azurecli
    az keyvault delete --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}
    ```

* Vypsat všechny odstraněné trezory klíčů

    ```azurecli
    az keyvault list-deleted --subscription {SUBSCRIPTION ID} --resource-type vault
    ```

* Obnovení tichého a odstraněného trezoru klíčů

    ```azurecli
    az keyvault recover --subscription {SUBSCRIPTION ID} -n {VAULT NAME}
    ```

* Vyprázdnit dočasně odstraněný Trezor klíčů **(upozornění! Při této operaci dojde k TRVALÉmu odstranění TREZORu klíčů.)**

    ```azurecli
    az keyvault purge --subscription {SUBSCRIPTION ID} -n {VAULT NAME}
    ```

* Povolit vyprázdnit ochranu klíče v trezoru klíčů

    ```azurecli
    az keyvault update --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME} --enable-purge-protection true
    ```

## <a name="certificates-cli"></a>Certifikáty (CLI)

* Udělení přístupu k vyprázdnit a obnovit certifikáty

    ```azurecli
    az keyvault set-policy --upn user@contoso.com --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}  --certificate-permissions recover purge
    ```

* Odstranit certifikát

    ```azurecli
    az keyvault certificate delete --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {CERTIFICATE NAME}
    ```

* Vypsat odstraněné certifikáty

    ```azurecli
    az keyvault certificate list-deleted --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME}
    ```

* Obnovit odstraněný certifikát

    ```azurecli
    az keyvault certificate recover --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {CERTIFICATE NAME}
    ```

* Vyprázdnit certifikát s přípouštějící obnovitelné **(upozornění! Tato operace trvale odstraní váš certifikát)** .

    ```azurecli
    az keyvault certificate purge --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {CERTIFICATE NAME}
    ```

## <a name="keys-cli"></a>Klíče (CLI)

* Udělení přístupu k vyprázdnit a obnovit klíče

    ```azurecli
    az keyvault set-policy --upn user@contoso.com --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}  --key-permissions recover purge
    ```

* Klávesa Delete

    ```azurecli
    az keyvault key delete --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {KEY NAME}
    ```

* Seznam odstraněných klíčů

    ```azurecli
    az keyvault key list-deleted --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME}
    ```

* Obnovit odstraněný klíč

    ```azurecli
    az keyvault key recover --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {KEY NAME}
    ```

* Vyprázdnit měkký odstraněný klíč **(upozornění! Tato operace trvale odstraní váš klíč.)**

    ```azurecli
    az keyvault key purge --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {KEY NAME}
    ```

## <a name="secrets-cli"></a>Tajné kódy (CLI)

* Udělení přístupu k vymazání a obnovení tajných klíčů

    ```azurecli
    az keyvault set-policy --upn user@contoso.com --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}  --secret-permissions recover purge
    ```

* Odstranit tajný kód

    ```azurecli
    az keyvault secret delete --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {SECRET NAME}
    ```

* Seznam odstraněných tajných klíčů

    ```azurecli
    az keyvault secret list-deleted --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME}
    ```

* Obnovit odstraněný tajný klíč

    ```azurecli
    az keyvault secret recover --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {SECRET NAME}
    ```

* Vyprázdnit jemný odstraněný tajný klíč **(upozornění! Tato operace trvale odstraní váš tajný klíč** .

    ```azurecli
    az keyvault secret purge --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {SECRET NAME}
    ```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

## <a name="key-vault-powershell"></a>Key Vault (PowerShell)

* Ověřte, jestli je u trezoru klíčů povolené obnovitelné odstranění.

    ```powershell
    Get-AzKeyVault -VaultName "ContosoVault"
    ```

* Odstranit Trezor klíčů

    ```powershell
    Remove-AzKeyVault -VaultName 'ContosoVault'
    ```

* Vypsat všechny odstraněné trezory klíčů

    ```powershell
    Get-AzKeyVault -InRemovedState
    ```

* Obnovení tichého a odstraněného trezoru klíčů

    ```powershell
    Undo-AzKeyVaultRemoval -VaultName ContosoVault -ResourceGroupName ContosoRG -Location westus
    ```

* Vyprázdnit obnovitelné – Trezor klíčů **(upozornění) Při této operaci dojde k TRVALÉmu odstranění TREZORu klíčů.)**

    ```powershell
    Remove-AzKeyVault -VaultName ContosoVault -InRemovedState -Location westus
    ```

* Povolit vyprázdnit ochranu klíče v trezoru klíčů

    ```powershell
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "ContosoVault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true"

    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```

## <a name="certificates-powershell"></a>Certifikáty (PowerShell)

* Udělení oprávnění k obnovení a vyprázdnění certifikátů

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToCertificates recover,purge
    ```

* Odstranit certifikát

  ```powershell
  Remove-AzKeyVaultCertificate -VaultName ContosoVault -Name 'MyCert'
  ```

* Vypsat všechny odstraněné certifikáty v trezoru klíčů

  ```powershell
  Get-AzKeyVaultCertificate -VaultName ContosoVault -InRemovedState
  ```

* Obnovení certifikátu v odstraněném stavu

  ```powershell
  Undo-AzKeyVaultCertificateRemoval -VaultName ContosoVault -Name 'MyCert'
  ```

* Vyprázdnit částečný odstraněný certifikát **(upozornění! Tato operace trvale odstraní váš certifikát)** .

  ```powershell
  Remove-AzKeyVaultcertificate -VaultName ContosoVault -Name 'MyCert' -InRemovedState
  ```

## <a name="keys-powershell"></a>Klíče (PowerShell)

* Udělení oprávnění k obnovení a vyprázdnění klíčů

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToKeys recover,purge
    ```

* Odstranění klíče

  ```powershell
  Remove-AzKeyVaultKey -VaultName ContosoVault -Name 'MyKey'
  ```

* Vypsat všechny odstraněné certifikáty v trezoru klíčů

  ```powershell
  Get-AzKeyVaultKey -VaultName ContosoVault -InRemovedState
  ```

* Obnovení neodstraněného klíče

    ```powershell
    Undo-AzKeyVaultKeyRemoval -VaultName ContosoVault -Name ContosoFirstKey
    ```

* Vyprázdnit měkký odstraněný klíč **(upozornění! Tato operace trvale odstraní váš klíč.)**

    ```powershell
    Remove-AzKeyVaultKey -VaultName ContosoVault -Name ContosoFirstKey -InRemovedState
    ```

## <a name="secrets-powershell"></a>Tajné kódy (PowerShell)

* Udělení oprávnění k obnovení a mazání tajných kódů

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToSecrets recover,purge
    ```

* Odstranění tajného kódu s názvem SQLPassword

  ```powershell
  Remove-AzKeyVaultSecret -VaultName ContosoVault -name SQLPassword
  ```

* Výpis všech odstraněných tajných klíčů v trezoru klíčů

  ```powershell
  Get-AzKeyVaultSecret -VaultName ContosoVault -InRemovedState
  ```

* Obnovení tajného klíče v odstraněném stavu

  ```powershell
  Undo-AzKeyVaultSecretRemoval -VaultName ContosoVault -Name SQLPAssword
  ```

* Vyprázdnit tajný klíč v odstraněném stavu **(upozornění! Tato operace trvale odstraní váš klíč.)**

  ```powershell
  Remove-AzKeyVaultSecret -VaultName ContosoVault -InRemovedState -name SQLPassword
  ```
---

## <a name="next-steps"></a>Další kroky

- [Rutiny Azure Key Vault PowerShellu](https://docs.microsoft.com/powershell/module/az.keyvault)
- [Key Vault příkazů rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/keyvault)
- [Zálohování Azure Key Vault](backup.md)
- [Postup povolení protokolování Key Vault](howto-logging.md)
- [Zabezpečený přístup k trezoru klíčů](secure-your-key-vault.md)
- [Azure Key Vault příručka pro vývojáře](developers-guide.md)
- [Osvědčené postupy použití trezoru klíčů](best-practices.md)
