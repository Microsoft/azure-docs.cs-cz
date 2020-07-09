---
title: Azure Key Vault zálohování | Microsoft Docs
description: Tento dokument vám umožní zálohovat tajný klíč, klíč nebo certifikát uložený v Azure Key Vault.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: sudbalas
ms.openlocfilehash: 8a152e2771f0b207e81f42c6ecae2e4d14605051
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/08/2020
ms.locfileid: "86147795"
---
# <a name="azure-key-vault-backup"></a>Zálohování Azure Key Vault

V tomto dokumentu se dozvíte, jak provést zálohování jednotlivých tajných klíčů, klíčů a certifikátů uložených v trezoru klíčů. Účelem této zálohy je poskytnout offline kopii všech vašich tajných kódů v nepravděpodobném případě, že ztratíte přístup k trezoru klíčů.

## <a name="overview"></a>Přehled

Key Vault automaticky poskytuje několik funkcí pro udržení dostupnosti a ochranu před únikem informací. Tato záloha by měla být prováděna pouze v případě, že existuje důležitý obchodní odůvodnění, aby bylo možné zachovat zálohu vašich tajných kódů. Zálohování tajných klíčů v trezoru klíčů může vést k dalším provozním výzvám, jako je udržování více sad protokolů, oprávnění a zálohování, když vyprší nebo natočí tajnosti klíčů.

Key Vault udržuje dostupnost ve scénářích havárií a automaticky převezme požadavky na spárované oblasti bez nutnosti zásahu uživatele. Další informace najdete na následujícím odkazu. [Azure Key Vault zotavení po havárii](https://docs.microsoft.com/azure/key-vault/general/disaster-recovery-guidance)

Key Vault chránit před náhodným a škodlivým odstraněním tajných kódů prostřednictvím ochrany pomocí obnovitelného odstranění a vyprázdnění. Pokud chcete chránit před náhodným nebo škodlivým odstraněním tajných kódů, nakonfigurujte prosím funkce ochrany obnovitelného odstranění a vyprázdnění pro váš Trezor klíčů. Další informace najdete v následujícím dokumentu. [Azure Key Vault obnovení](https://docs.microsoft.com/azure/key-vault/general/overview-soft-delete)

## <a name="limitations"></a>Omezení

Azure Key Vault v současné době nepodporuje způsob zálohování celého trezoru klíčů v rámci jedné operace. Žádný pokus o použití příkazů uvedených v tomto dokumentu k provedení automatizovaného zálohování trezoru klíčů nebude podporován společností Microsoft ani týmem Azure Key Vault.

Při pokusu o použití příkazů uvedených v následujícím dokumentu k vytvoření vlastní automatizace může dojít k chybám.

* Zálohování tajných kódů s více verzemi může způsobit chyby s časovým limitem.
* Při zálohování se vytvoří snímek v daném časovém okamžiku. Tajné kódy se můžou prodloužit při zálohování, což způsobuje neshodu šifrovacích klíčů.
* Překročení limitů služby trezoru klíčů pro žádosti za sekundu způsobí omezení trezoru klíčů a způsobí selhání zálohování.

## <a name="design-considerations"></a>Na co dát pozor při navrhování

Když zálohujete objekt uložený v trezoru klíčů (tajný klíč, klíč nebo certifikát), operace zálohování stáhne objekt jako zašifrovaný objekt BLOB. Tento objekt BLOB se nedá dešifrovat mimo Azure. Pokud chcete získat použitelná data z tohoto objektu blob, musíte obnovit objekt blob do trezoru klíčů v rámci stejného předplatného Azure a geografické oblasti Azure.
[Geografické grafy Azure](https://azure.microsoft.com/global-infrastructure/geographies/)

## <a name="prerequisites"></a>Požadavky

* Oprávnění na úrovni přispěvatele nebo vyšší úrovně oprávnění pro předplatné Azure
* Primární Trezor klíčů obsahující tajné kódy, které chcete zálohovat
* Sekundární Trezor klíčů, ve kterém se obnoví tajné klíče

## <a name="back-up-and-restore-with-azure-portal"></a>Zálohování a obnovení pomocí Azure Portal

### <a name="back-up"></a>Zálohování

1. Přejděte na Azure Portal.
2. Vyberte svůj Trezor klíčů.
3. Přejděte k objektu (tajný klíč, klíč nebo certifikát), který chcete zálohovat.

    ![Image](../media/backup-1.png)

4. Vyberte objekt.
5. Vyberte stáhnout zálohu.

    ![Image](../media/backup-2.png)
    
6. Klikněte na tlačítko Download (Stáhnout).

    ![Image](../media/backup-3.png)
    
7. Zašifrovaný objekt BLOB uložte na bezpečném místě.

### <a name="restore"></a>Obnovení

1. Přejděte na Azure Portal.
2. Vyberte svůj Trezor klíčů.
3. Přejděte na typ objektu (tajný klíč, klíč nebo certifikát), který chcete obnovit.
4. Vyberte obnovit zálohu.

    ![Image](../media/backup-4.png)
    
5. Přejděte do umístění, kam jste uložili zašifrovaný objekt BLOB.
6. Vyberte OK.

## <a name="back-up-and-restore-with-the-azure-cli"></a>Zálohování a obnovení pomocí Azure CLI

```azurecli
## Login To Azure
az login

## Set your Subscription
az account set --subscription {AZURE SUBSCRIPTION ID}

## Register Key Vault as a Provider
az provider register -n Microsoft.KeyVault

## Backup a Certificate in Key Vault
az keyvault certificate backup --file {File Path} --name {Certificate Name} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Backup a Key in Key Vault
az keyvault key backup --file {File Path} --name {Key Name} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Backup a Secret in Key Vault
az keyvault secret backup --file {File Path} --name {Secret Name} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Restore a Certificate in Key Vault
az keyvault certificate restore --file {File Path} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Restore a Key in Key Vault
az keyvault key restore --file {File Path} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Restore a Secret in Key Vault
az keyvault secret restore --file {File Path} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

```

## <a name="next-steps"></a>Další kroky

Zapněte protokolování a monitorování pro Azure Key Vault. [Protokolování Azure Key Vault](https://docs.microsoft.com/azure/key-vault/general/logging)
