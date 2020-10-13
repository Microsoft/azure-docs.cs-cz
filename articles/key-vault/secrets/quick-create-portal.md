---
title: Rychlý start Azure – Nastavení a načtení tajného klíče ze služby Key Vault pomocí webu Azure Portal | Microsoft Docs
description: Rychlý start ukazující nastavení a načtení tajného klíče ze služby Azure Key Vault pomocí webu Azure Portal
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: mvc
ms.date: 09/03/2019
ms.author: mbaldwin
ms.openlocfilehash: 126df6e7f4d227c20c2173a1e2d4c0d7361b043f
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91962441"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-the-azure-portal"></a>Rychlý start: Nastavení a načtení tajného klíče ze služby Azure Key Vault pomocí webu Azure Portal

Azure Key Vault je cloudová služba, která funguje jako zabezpečené úložiště tajných kódů. Můžete bezpečně ukládat klíče, hesla, certifikáty a další tajné klíče. Trezory klíčů Azure můžete vytvářet a spravovat přes web Azure Portal. V tomto rychlém startu vytvoříte trezor klíčů, do kterého uložíte tajný kód. Další informace o službě Key Vault najdete v tématu [Přehled](../general/overview.md).

Další informace o tajných klíčích najdete v tématu (about-secrets.md).

## <a name="prerequisites"></a>Předpoklady

- Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="create-a-vault"></a>Vytvoření trezoru

1. V nabídce Azure Portal nebo na **domovské** stránce vyberte **vytvořit prostředek**.
2. Do vyhledávacího pole zadejte **Key Vault**.
3. V seznamu výsledků zvolte **Key Vault**.
4. V části Key Vault zvolte **Vytvořit**.
5. V části **Vytvořit trezor klíčů** zadejte následující informace:
    - **Název:** Je potřeba zadat jedinečný název. V tomto rychlém startu používáme **Contoso-vault2**. 
    - **Předplatné:** Zvolte předplatné.
    - V části **Skupina prostředků**vyberte **vytvořit novou** a zadejte název skupiny prostředků.
    - V rozevírací nabídce **Umístění** zvolte umístění.
    - U ostatních možností ponechte jejich výchozí hodnoty.
6. Po zadání výše uvedených informací vyberte **Vytvořit**.

Poznamenejte si hodnoty dvou vlastností uvedených níže:

* **Název trezoru:** V tomto příkladu je to **Contoso-Vault2**. Tento název budete používat pro další kroky.
* **Identifikátor URI trezoru:** V tomto příkladu je to https://contoso-vault2.vault.azure.net/. Aplikace, které používají váš trezor prostřednictvím REST API musí používat tento identifikátor URI.

Můžete také vytvořit Key Vault pomocí Azure CLI a PowerShellu: [Vytvoření Key Vault pomocí prostředí PowerShell](../general/quick-create-powershell.md) 
 [Vytvoření Key Vault pomocí Azure CLI](../general/quick-create-cli.md) .

V tuto chvíli je váš účet Azure jediným účtem s oprávněním provádět operace s tímto novým trezorem.

![Výstup po dokončení vytváření služby Key Vault](../media/quick-create-portal/vault-properties.png)

## <a name="add-a-secret-to-key-vault"></a>Přidání tajného klíče do služby Key Vault

Pokud chcete do trezoru přidat tajný klíč, stačí provést několik dalších kroků. V tomto případě přidáme heslo, které může používat aplikace. Heslo se nazývá **ExamplePassword** a ukládáme hodnotu **hVFkk965BuUv** .

1. Na stránkách Key Vault vlastnosti vyberte **tajné**kódy.
2. Klikněte na **Vygenerovat/importovat**.
3. Na obrazovce **Vytvoření tajného kódu** zvolte následující hodnoty:
    - **Možnosti nahrání:** Ruční
    - **Název:** ExamplePassword
    - **Hodnota**: hVFkk965BuUv
    - U ostatních hodnot ponechte jejich výchozí nastavení. Klikněte na **Vytvořit**.

Jakmile se zobrazí zpráva o úspěšném vytvoření tajného kódu, můžete na něj kliknout v seznamu. 

## <a name="retrieve-a-secret-from-key-vault"></a>Načtení tajného kódu z Key Vault

Pokud kliknete na aktuální verzi, zobrazí se hodnota, kterou jste zadali v předchozím kroku.

![Vlastnosti tajného klíče](../media/quick-create-portal/current-version-hidden.png)

Kliknutím na tlačítko Zobrazit hodnotu tajného klíče v pravém podokně uvidíte skrytou hodnotu. 

![Objevila se hodnota tajného klíče](../media/quick-create-portal/current-version-shown.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Další rychlé starty a kurzy týkající se služby Key Vault vycházejí z tohoto rychlého startu. Pokud chcete pokračovat v práci s dalšími rychlými starty a kurzy, možná budete chtít tyto prostředky zachovat.
Až nebudete prostředky potřebovat, odstraňte jejich skupinu. Tím odstraníte Key Vault i související prostředky. Odstranění skupiny prostředků přes portál:

1. Do pole Hledat v horní části portálu zadejte název vaší skupiny prostředků. Jakmile se ve výsledcích hledání zobrazí skupina prostředků použitá v tomto rychlém startu, vyberte ji.
2. Vyberte **Odstranit skupinu prostředků**.
3. Do pole **ZADEJTE NÁZEV SKUPINY PROSTŘEDKŮ:** zadejte název vaší skupiny prostředků a vyberte **Odstranit**.


## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili Key Vault a uložili do něj tajný klíč. Další informace o Key Vault a o tom, jak je integrovat s vašimi aplikacemi, najdete dál v článcích níže.

- Přečtěte si [přehled Azure Key Vault](../general/overview.md)
- Číst [zabezpečený přístup k Key Vault](../general/secure-your-key-vault.md)
- Viz [použití Key Vault s webovou aplikací App Service](../general/tutorial-net-create-vault-azure-web-app.md)
- Viz [použití Key Vault s aplikací nasazenou do virtuálního počítače](../general/tutorial-net-virtual-machine.md) .
- Další informace najdete v [příručce pro vývojáře Azure Key Vault](../general/developers-guide.md) .
- Kontrola [Azure Key Vault osvědčených postupů](../general/best-practices.md)
