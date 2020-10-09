---
title: Zálohujte tajný klíč, klíč nebo certifikát uložený v Azure Key Vault | Microsoft Docs
description: Tento dokument vám umožní zálohovat tajný klíč, klíč nebo certifikát uložený v Azure Key Vault.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/12/2019
ms.author: sudbalas
ms.openlocfilehash: a1c07432dcf90759662e8f4aaedc760abd18157c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88585929"
---
# <a name="azure-key-vault-backup"></a>Zálohování Azure Key Vault

V tomto dokumentu se dozvíte, jak zálohovat tajné klíče, klíče a certifikáty uložené v trezoru klíčů. Záloha je určena k tomu, aby vám poskytla offline kopii všech vašich tajných kódů v nepravděpodobném případě, že ztratíte přístup k trezoru klíčů.

## <a name="overview"></a>Přehled

Azure Key Vault automaticky poskytuje funkce, které vám pomůžou zachovat dostupnost a zabránit ztrátě dat. Zálohujte tajné klíče jenom v případě, že máte důležité obchodní odůvodnění. Zálohování tajných klíčů v trezoru klíčů může vést k provozním výzvám, jako je udržování několika sad protokolů, oprávnění a záloh po vypršení platnosti tajných klíčů.

Key Vault udržuje dostupnost ve scénářích havárií a automaticky převezme požadavky na spárované oblasti bez zásahu uživatele. Další informace najdete v tématu [Azure Key Vault dostupnost a redundance](https://docs.microsoft.com/azure/key-vault/general/disaster-recovery-guidance).

Pokud chcete chránit před náhodným nebo škodlivým odstraněním tajných kódů, nakonfigurujte funkce ochrany obnovitelného odstranění a vyprázdnění v trezoru klíčů. Další informace najdete v tématu [přehled Azure Key Vaultho obnovitelného odstranění](https://docs.microsoft.com/azure/key-vault/general/soft-delete-overview).

## <a name="limitations"></a>Omezení

Key Vault aktuálně neposkytuje způsob, jak zálohovat celý Trezor klíčů v rámci jedné operace. Jakékoli pokusy o použití příkazů uvedených v tomto dokumentu k provedení automatizovaného zálohování trezoru klíčů může způsobit chyby a společnost Microsoft ani tým Azure Key Vault nepodporují. 

Vezměte v úvahu také následující důsledky:

* Zálohování tajných klíčů, které mají více verzí, může způsobit chyby s časovým limitem.
* Při zálohování se vytvoří snímek v daném časovém okamžiku. Tajné kódy se můžou během zálohování prodloužit, což způsobilo neshodu šifrovacích klíčů.
* Pokud překročíte omezení služby trezoru klíčů pro žádosti za sekundu, váš Trezor klíčů bude omezený a zálohování se nezdaří.

## <a name="design-considerations"></a>Na co dát pozor při navrhování

Při zálohování objektu trezoru klíčů, jako je tajný klíč, klíč nebo certifikát, bude operace zálohování stahovat objekt jako zašifrovaný objekt BLOB. Tento objekt BLOB není možné dešifrovat mimo Azure. Pokud chcete získat použitelná data z tohoto objektu blob, musíte obnovit objekt blob do trezoru klíčů v rámci stejného předplatného Azure a [geografické oblasti Azure](https://azure.microsoft.com/global-infrastructure/geographies/).

## <a name="prerequisites"></a>Požadavky

Pokud chcete zálohovat objekt trezoru klíčů, musíte mít: 

* Oprávnění na úrovni přispěvatele nebo vyšší úrovně na předplatném Azure.
* Primární Trezor klíčů obsahující tajné údaje, které chcete zálohovat.
* Sekundární Trezor klíčů, ve kterém se obnoví tajné klíče

## <a name="back-up-and-restore-from-the-azure-portal"></a>Zálohování a obnovení z Azure Portal

Podle kroků v této části můžete zálohovat a obnovovat objekty pomocí Azure Portal.

### <a name="back-up"></a>Zálohování

1. Přejděte na Azure Portal.
2. Vyberte svůj Trezor klíčů.
3. Přejít na objekt (tajný klíč, klíč nebo certifikát), který chcete zálohovat.

    ![Snímek obrazovky s informacemi o tom, kde vybrat nastavení klíčů a objekt v trezoru klíčů](../media/backup-1.png)

4. Vyberte objekt.
5. Vyberte **stáhnout zálohu**.

    ![Snímek obrazovky s informacemi o tom, kde vybrat tlačítko pro stažení zálohy v trezoru klíčů](../media/backup-2.png)
    
6. Vyberte **Stáhnout**.

    ![Snímek obrazovky s informacemi o tom, kde vybrat tlačítko pro stažení v trezoru klíčů](../media/backup-3.png)
    
7. Zašifrovaný objekt BLOB uložte na bezpečném místě.

### <a name="restore"></a>Obnovení

1. Přejděte na Azure Portal.
2. Vyberte svůj Trezor klíčů.
3. Přejít na typ objektu (tajný klíč, klíč nebo certifikát), který chcete obnovit.
4. Vyberte **obnovit zálohu**.

    ![Snímek obrazovky s informacemi o tom, kde vybrat obnovit zálohu v trezoru klíčů](../media/backup-4.png)
    
5. Přejít do umístění, kam jste uložili zašifrovaný objekt BLOB.
6. Vyberte **OK**.

## <a name="back-up-and-restore-from-the-azure-cli"></a>Zálohování a obnovení z Azure CLI

```azurecli
## Log in to Azure
az login

## Set your subscription
az account set --subscription {AZURE SUBSCRIPTION ID}

## Register Key Vault as a provider
az provider register -n Microsoft.KeyVault

## Back up a certificate in Key Vault
az keyvault certificate backup --file {File Path} --name {Certificate Name} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Back up a key in Key Vault
az keyvault key backup --file {File Path} --name {Key Name} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Back up a secret in Key Vault
az keyvault secret backup --file {File Path} --name {Secret Name} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Restore a certificate in Key Vault
az keyvault certificate restore --file {File Path} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Restore a key in Key Vault
az keyvault key restore --file {File Path} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Restore a secret in Key Vault
az keyvault secret restore --file {File Path} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

```

## <a name="next-steps"></a>Další kroky

Zapněte [protokolování a monitorování](https://docs.microsoft.com/azure/key-vault/general/logging) pro Key Vault.
