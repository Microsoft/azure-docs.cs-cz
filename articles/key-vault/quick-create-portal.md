---
title: Rychlý start Azure – Nastavení a načtení tajného klíče ze služby Key Vault pomocí webu Azure Portal | Microsoft Docs
description: Rychlý start ukazující nastavení a načtení tajného klíče ze služby Azure Key Vault pomocí webu Azure Portal
services: key-vault
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 98cf8387-34de-468e-ac8f-5c02c9e83e68
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.custom: mvc
ms.date: 01/07/2019
ms.author: barclayn
ms.openlocfilehash: c3b605507c3878770db2c7dcb53a2ff9bb512b6b
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54078384"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-the-azure-portal"></a>Rychlý start: Nastavení a načtení tajného klíče ze služby Azure Key Vault pomocí webu Azure portal

Azure Key Vault je cloudová služba, která funguje jako zabezpečené úložiště tajných kódů. Můžete bezpečně ukládat klíče, hesla, certifikáty a další tajné klíče. Trezory klíčů Azure můžete vytvářet a spravovat přes web Azure Portal. V tomto rychlém startu vytvoříte trezor klíčů, do kterého uložíte tajný kód. Další informace o službě Key Vault najdete v tématu [Přehled](key-vault-overview.md).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="sign-in-to-azure"></a>Přihlásit se k Azure

Přihlaste se k webu Azure Portal na adrese http://portal.azure.com.

## <a name="create-a-vault"></a>Vytvoření trezoru

1. Vyberte možnost **Vytvořit prostředek** v levém horním rohu webu Azure Portal.

    ![Výstup po dokončení vytváření služby Key Vault](./media/quick-create-portal/search-services.png)
2. Do vyhledávacího pole zadejte **Key Vault**.
3. V seznamu výsledků zvolte **Key Vault**.
4. V části Key Vault zvolte **Vytvořit**.
5. V části **Vytvořit trezor klíčů** zadejte následující informace:
    - **Název**: Jedinečný název je povinný. V tomto rychlém startu používáme **Contoso-vault2**. 
    - **Předplatné**: Zvolte předplatné.
    - V části **Skupina prostředků** zvolte **Vytvořit novou** a zadejte název skupiny prostředků.
    - V rozevírací nabídce **Umístění** zvolte umístění.
    - Zaškrtněte políčko **Připnout na řídicí panel**.
    - U ostatních možností ponechte jejich výchozí hodnoty.
6. Po zadání výše uvedených informací vyberte **Vytvořit**.

Poznamenejte si hodnoty dvou vlastností uvedených níže:

* **Název trezoru**: V tomto příkladu je to **Contoso-Vault2**. Tento název budete používat pro další kroky.
* **Identifikátor URI trezoru**: V tomto příkladu je to https://contoso-vault2.vault.azure.net/. Aplikace, které používají váš trezor prostřednictvím REST API musí používat tento identifikátor URI.

V tuto chvíli je váš účet Azure jediným účtem s oprávněním provádět operace s tímto novým trezorem.

![Výstup po dokončení vytváření služby Key Vault](./media/quick-create-portal/vault-properties.png)

## <a name="add-a-secret-to-key-vault"></a>Přidání tajného klíče do služby Key Vault

Pokud chcete do trezoru přidat tajný klíč, stačí provést několik dalších kroků. V tomto případě přidáme heslo, které může používat aplikace. Heslo se nazývá **ExamplePassword** a uchovává hodnotu **Pa$$w0rd**.

1. Na stránce vlastností služby Key Vault vyberte **Tajné kódy**.
2. Klikněte na **Vygenerovat/importovat**.
3. Na obrazovce **Vytvoření tajného kódu** zvolte následující hodnoty:
    - **Možnosti nahrání**: Ruční.
    - **Název**: ExamplePassword.
    - **Hodnota**: Pa$ $w0rd.
    - U ostatních hodnot ponechte jejich výchozí nastavení. Klikněte na možnost **Vytvořit**.

Jakmile se zobrazí zpráva o úspěšném vytvoření tajného kódu, můžete na něj kliknout v seznamu. Zobrazí se několik jeho vlastností. Pokud kliknete na aktuální verzi, zobrazí se hodnota, kterou jste zadali v předchozím kroku.

![Vlastnosti tajného klíče](./media/quick-create-portal/version.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Další rychlé starty a kurzy týkající se služby Key Vault vycházejí z tohoto rychlého startu. Pokud chcete pokračovat v práci s dalšími rychlými starty a kurzy, možná budete chtít tyto prostředky zachovat.
Až nebudete prostředky potřebovat, odstraňte jejich skupinu. Tím odstraníte Key Vault i související prostředky. Odstranění skupiny prostředků přes portál:

1. Do pole Hledat v horní části portálu zadejte název vaší skupiny prostředků. Jakmile se ve výsledcích hledání zobrazí skupina prostředků použitá v tomto rychlém startu, vyberte ji.
2. Vyberte **Odstranit skupinu prostředků**.
3. Do pole **ZADEJTE NÁZEV SKUPINY PROSTŘEDKŮ:** zadejte název vaší skupiny prostředků a vyberte **Odstranit**.


## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste vytvořili službu Key Vault a uložili tajný klíč. Pokud potřebujete další informace o službě Key Vault a jejím použití v aplikacích, pokračujte kurzem o fungování webových aplikací se službou Key Vault.

> [!div class="nextstepaction"]
> Informace o načtení tajného kódu ze služby Key Vault webovou aplikací pomocí spravovaných identit prostředků Azure najdete v následujícím kurzu [Konfigurace webové aplikace Azure pro čtení tajného kódu ze služby Key Vault](quick-create-net.md).
