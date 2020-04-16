---
title: Kurz – Import certifikátu v trezoru klíčů pomocí portálu Azure | Dokumenty společnosti Microsoft
description: Kurz, který ukazuje, jak importovat certifikát v úložišti klíčů Azure
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/03/2020
ms.author: sebansal
ms.openlocfilehash: 754f30f7931f9fad6a95328cbf8ab34f70cb75a0
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81423107"
---
# <a name="tutorial-import-a-certificate-in-azure-key-vault"></a>Kurz: Import certifikátu v trezoru klíčů Azure

Azure Key Vault je cloudová služba, která funguje jako zabezpečené úložiště tajných kódů. Můžete bezpečně ukládat klíče, hesla, certifikáty a další tajné klíče. Trezory klíčů Azure můžete vytvářet a spravovat přes web Azure Portal. V tomto kurzu vytvoříte trezor klíčů a pak ho použijete k importu certifikátu. Další informace o službě Key Vault najdete v tématu [Přehled](../general/overview.md).

V tomto kurzu získáte informace o následujících postupech:

> [!div class="checklist"]
> * Vytvoření trezoru klíčů
> * Importujte certifikát v trezoru klíčů pomocí portálu.
> * Importujte certifikát v trezoru klíčů pomocí funkce cli.


Než začnete, přečtěte si [základní koncepty trezoru klíčů](../general/basic-concepts.md). 

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="create-a-vault"></a>Vytvoření trezoru

1. V nabídce Portál Azure nebo na **domovské** stránce vyberte **Vytvořit prostředek**.
2. Do vyhledávacího pole zadejte **Key Vault**.
3. V seznamu výsledků zvolte **Key Vault**.
4. V části Key Vault zvolte **Vytvořit**.
5. V části **Vytvořit trezor klíčů** zadejte následující informace:
    - **Název:** Je potřeba zadat jedinečný název. Pro tento rychlý start používáme **Example-Vault**. 
    - **Předplatné:** Zvolte předplatné.
    - V části **Skupina prostředků**zvolte **Vytvořit nový** a zadejte název skupiny prostředků.
    - V rozevírací nabídce **Umístění** zvolte umístění.
    - U ostatních možností ponechte jejich výchozí hodnoty.
6. Po zadání výše uvedených informací vyberte **Vytvořit**.

Poznamenejte si hodnoty dvou vlastností uvedených níže:

* **Název trezoru**: V příkladu je to **Example-Vault**. Tento název budete používat pro další kroky.
* **Identifikátor URI trezoru:** V tomto příkladu je to https://example-vault.vault.azure.net/. Aplikace, které používají váš trezor prostřednictvím REST API musí používat tento identifikátor URI.

V tuto chvíli je váš účet Azure jediným účtem s oprávněním provádět operace s tímto novým trezorem.

![Výstup po dokončení vytváření služby Key Vault](../media/certificates/tutorial-import-cert/vault-properties.png)

## <a name="import-a-certificate-to-key-vault"></a>Import certifikátu do trezoru klíčů

Chcete-li importovat certifikát do úložiště, musíte mít soubor certifikátu PEM nebo PFX, abyste mohli být na disku. V takovém případě importujeme certifikát s názvem **ExampleCertificate**.

> [!IMPORTANT]
> V trezoru klíčů Azure jsou podporované formáty certifikátů PFX a PEM. 
> - Formát souboru .pem obsahuje jeden nebo více souborů certifikátů X509.
> - Formát souboru .pfx je formát archivního souboru pro ukládání několika kryptografických objektů do jednoho souboru, tj.  

1. Na stránkách vlastností trezoru klíčů vyberte **certifikáty**.
2. Klikněte na **Vygenerovat/importovat**.
3. Na obrazovce **Vytvořit certifikát zvolte** následující hodnoty:
    - **Způsob vytváření certifikátů**: Import.
    - **Název certifikátu**: ExampleCertificate.
    - **Nahrát soubor certifikátu**: vyberte soubor certifikátu z disku
    - U ostatních hodnot ponechte jejich výchozí nastavení. Klikněte na **Vytvořit**.

![Vlastnosti certifikátu](../media/certificates/tutorial-import-cert/cert-import.png)

Jakmile obdržíte zprávu, že certifikát byl úspěšně importován, můžete na něj kliknout v seznamu. Poté můžete zobrazit některé z jeho vlastností. 

![Vlastnosti certifikátu](../media/certificates/tutorial-import-cert/current-version-hidden.png)

## <a name="import-a-certificate-using-azure-cli"></a>Import certifikátu pomocí azure cli

Importujte certifikát do zadaného trezoru klíčů. Chcete-li importovat existující platný certifikát obsahující soukromý klíč do trezoru klíčů Azure, soubor, který má být importován, může být ve formátu PFX nebo PEM. Pokud je certifikát ve formátu PEM, musí soubor PEM obsahovat klíče i certifikáty x509. Tato operace vyžaduje certifikáty/oprávnění k importu.

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
Další informace o parametrech [naleznete zde](https://docs.microsoft.com/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-import)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Další rychlé starty a kurzy týkající se služby Key Vault vycházejí z tohoto rychlého startu. Pokud chcete pokračovat v práci s dalšími rychlými starty a kurzy, možná budete chtít tyto prostředky zachovat.
Až nebudete prostředky potřebovat, odstraňte jejich skupinu. Tím odstraníte Key Vault i související prostředky. Odstranění skupiny prostředků přes portál:

1. Do pole Hledat v horní části portálu zadejte název vaší skupiny prostředků. Jakmile se ve výsledcích hledání zobrazí skupina prostředků použitá v tomto rychlém startu, vyberte ji.
2. Vyberte **Odstranit skupinu prostředků**.
3. Do pole **ZADEJTE NÁZEV SKUPINY PROSTŘEDKŮ:** zadejte název vaší skupiny prostředků a vyberte **Odstranit**.


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili trezor klíčů a importovali v něm certifikát. Chcete-li se dozvědět více o trezoru klíčů a o tom, jak jej integrovat s aplikacemi, pokračujte v následujících článcích.

- Další informace o [správě certifikátů v úložišti klíčů Azure](/archive/blogs/kv/manage-certificates-via-azure-key-vault)
- Zobrazit příklady [importu certifikátů pomocí repoziturek REST API](/rest/api/keyvault/importcertificate/importcertificate)
- Kontrola [doporučených postupů azure key vaultu](../general/best-practices.md)