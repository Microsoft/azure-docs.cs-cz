---
title: Kurz – Import certifikátu v Key Vault pomocí Azure Portal | Microsoft Docs
description: Kurz ukazující Import certifikátu v Azure Key Vault
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: tutorial
ms.custom: mvc, devx-track-azurecli
ms.date: 04/16/2020
ms.author: sebansal
ms.openlocfilehash: 8689b6851ae219ee6f41ebf58736692e557b2344
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/03/2020
ms.locfileid: "93289725"
---
# <a name="tutorial-import-a-certificate-in-azure-key-vault"></a>Kurz: Import certifikátu v Azure Key Vault

Azure Key Vault je cloudová služba, která funguje jako zabezpečené úložiště tajných kódů. Můžete bezpečně ukládat klíče, hesla, certifikáty a další tajné klíče. Trezory klíčů Azure můžete vytvářet a spravovat přes web Azure Portal. V tomto kurzu vytvoříte Trezor klíčů a pak ho použijete k importu certifikátu. Další informace o službě Key Vault najdete v tématu [Přehled](../general/overview.md).

V tomto kurzu získáte informace o následujících postupech:

> [!div class="checklist"]
> * Vytvořte trezor klíčů.
> * Importujte certifikát v Key Vault pomocí portálu.
> * Importujte certifikát v Key Vault pomocí rozhraní příkazového řádku.
> * Importujte certifikát v Key Vault pomocí PowerShellu.


Než začnete, přečtěte si téma [Key Vault Basic koncepty](../general/basic-concepts.md). 

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="create-a-vault"></a>Vytvoření trezoru

1. V nabídce Azure Portal nebo na **domovské** stránce vyberte **vytvořit prostředek**.
2. Do vyhledávacího pole zadejte **Key Vault**.
3. V seznamu výsledků zvolte **Key Vault**.
4. V části Key Vault zvolte **Vytvořit**.
5. V části **Vytvořit trezor klíčů** zadejte následující informace:
    - **Název:** Je potřeba zadat jedinečný název. V tomto rychlém startu používáme **příklad – trezor**. 
    - **Předplatné:** Zvolte předplatné.
    - V části **Skupina prostředků** vyberte **vytvořit novou** a zadejte název skupiny prostředků.
    - V rozevírací nabídce **Umístění** zvolte umístění.
    - U ostatních možností ponechte jejich výchozí hodnoty.
6. Po zadání výše uvedených informací vyberte **Vytvořit**.

Poznamenejte si hodnoty dvou vlastností uvedených níže:

* **Název trezoru** : v tomto příkladu je to **příklad – trezor**. Tento název budete používat pro další kroky.
* **Identifikátor URI trezoru:** V tomto příkladu je to https://example-vault.vault.azure.net/. Aplikace, které používají váš trezor prostřednictvím REST API musí používat tento identifikátor URI.

V tuto chvíli je váš účet Azure jediným účtem s oprávněním provádět operace s tímto novým trezorem.

![Výstup po dokončení vytváření služby Key Vault](../media/certificates/tutorial-import-cert/vault-properties.png)

## <a name="import-a-certificate-to-key-vault"></a>Import certifikátu do Key Vault

Pokud chcete importovat certifikát do trezoru, musíte mít soubor certifikátu PEM nebo PFX, který bude na disku. V tomto případě naimportujeme certifikát s názvem souboru s názvem **ExampleCertificate**.

> [!IMPORTANT]
> Ve službě Azure Key Vault se podporují certifikáty ve formátu PFX a PEM. 
> - Formát souboru. pem obsahuje jeden nebo více souborů certifikátu x509.
> - Formát souboru. PFX je formát archivního souboru pro ukládání několika kryptografických objektů do jednoho souboru, tj. certifikát serveru (vydaný pro vaši doménu), odpovídajícího privátního klíče a volitelně může zahrnovat zprostředkující certifikační autoritu.  

1. Na stránkách Key Vault vlastnosti vyberte **certifikáty**.
2. Klikněte na **Vygenerovat/importovat**.
3. Na obrazovce **vytvořit certifikát** vyberte následující hodnoty:
    - **Metoda vytvoření certifikátu** : import.
    - **Název certifikátu** : ExampleCertificate.
    - **Nahrát soubor certifikátu** : vyberte soubor certifikátu z disku.
    - **Heslo** : Pokud nahráváte soubor certifikátu chráněný heslem, zadejte toto heslo sem. V opačném případě ponechte prázdné. Po úspěšném importu souboru certifikátu odstraní Trezor klíčů toto heslo.
4. Klikněte na **Vytvořit**.

![Vlastnosti certifikátu](../media/certificates/tutorial-import-cert/cert-import.png)

Když přidáte certifikát pomocí metody **importování** , služba Azure Key trezor automaticky naplní parametry certifikátu (tj. období platnosti, název vystavitele, datum aktivace atd.).

Jakmile se zobrazí zpráva o úspěšném importu certifikátu, můžete na něj kliknout v seznamu a zobrazit jeho vlastnosti. 

![Snímek obrazovky, který ukazuje, kde zobrazit vlastnosti certifikátu.](../media/certificates/tutorial-import-cert/current-version-hidden.png)

## <a name="import-a-certificate-using-azure-cli"></a>Import certifikátu pomocí Azure CLI

Importuje certifikát do zadaného trezoru klíčů. Pokud chcete importovat existující platný certifikát, který obsahuje privátní klíč, do Azure Key Vault, soubor, který se má importovat, může být ve formátu PFX nebo PEM. Pokud je certifikát ve formátu PEM, musí soubor PEM obsahovat klíč i certifikáty x509. Tato operace vyžaduje oprávnění k importu certifikátů.

```azurecli
az keyvault certificate import --file
                               --name
                               --vault-name
                               [--disabled {false, true}]
                               [--only-show-errors]
                               [--password]
                               [--policy]
                               [--subscription]
                               [--tags]
```

Přečtěte si další informace o [parametrech](/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-import).

Po importu certifikátu si můžete certifikát zobrazit pomocí [zobrazení certifikátu](/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-show) .


```azurecli
az keyvault certificate show [--id]
                             [--name]
                             [--only-show-errors]
                             [--subscription]
                             [--vault-name]
                             [--version]
```

Nyní jste vytvořili Trezor klíčů, importovali certifikát a zobrazili jste vlastnosti certifikátu.

## <a name="import-a-certificate-using-azure-powershell"></a>Import certifikátu pomocí Azure PowerShell

```
Import-AzureKeyVaultCertificate
      [-VaultName] <String>
      [-Name] <String>
      -FilePath <String>
      [-Password <SecureString>]
      [-Tag <Hashtable>]
      [-DefaultProfile <IAzureContextContainer>]
      [-WhatIf]
      [-Confirm]
      [<CommonParameters>]
```

Přečtěte si další informace o [parametrech](/powershell/module/azurerm.keyvault/import-azurekeyvaultcertificate?view=azurermps-6.13.0).


## <a name="clean-up-resources"></a>Vyčištění prostředků

Další rychlé starty a kurzy týkající se služby Key Vault vycházejí z tohoto rychlého startu. Pokud chcete pokračovat v práci s dalšími rychlými starty a kurzy, možná budete chtít tyto prostředky zachovat.
Až nebudete prostředky potřebovat, odstraňte jejich skupinu. Tím odstraníte Key Vault i související prostředky. Odstranění skupiny prostředků přes portál:

1. Do pole Hledat v horní části portálu zadejte název vaší skupiny prostředků. Jakmile se ve výsledcích hledání zobrazí skupina prostředků použitá v tomto rychlém startu, vyberte ji.
2. Vyberte **Odstranit skupinu prostředků**.
3. Do pole **ZADEJTE NÁZEV SKUPINY PROSTŘEDKŮ:** zadejte název vaší skupiny prostředků a vyberte **Odstranit**.


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili Key Vault a importovali do něj certifikát. Další informace o Key Vault a o tom, jak je integrovat s vašimi aplikacemi, najdete dál v článcích níže.

- Přečtěte si další informace o [správě vytváření certifikátů v Azure Key Vault](./create-certificate-scenarios.md)
- Podívejte se na příklady [importu certifikátů pomocí rozhraní REST API](/rest/api/keyvault/importcertificate/importcertificate) .
- Kontrola [Azure Key Vault osvědčených postupů](../general/best-practices.md)