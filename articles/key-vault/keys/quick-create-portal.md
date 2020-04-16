---
title: Azure Quickstart – nastavení a načtení klíče z trezoru klíčů pomocí webu Azure Portal | Dokumenty společnosti Microsoft
description: Úvodní příručka ukazuje, jak nastavit a načíst klíč z Azure Key Vault pomocí webu Azure Portal
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/24/2020
ms.author: mbaldwin
ms.openlocfilehash: 41f3d60d91b7418d6e9733b8351d4830b31dbace
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424192"
---
# <a name="quickstart-set-and-retrieve-a-key-from-azure-key-vault-using-the-azure-portal"></a>Úvodní příručka: Nastavení a načtení klíče z Azure Key Vault pomocí portálu Azure

Azure Key Vault je cloudová služba, která funguje jako zabezpečené úložiště tajných kódů. Můžete bezpečně ukládat klíče, hesla, certifikáty a další tajné klíče. Trezory klíčů Azure můžete vytvářet a spravovat přes web Azure Portal. V tomto rychlém startu vytvoříte trezor klíčů a pak ho použijete k uložení klíče. Další informace o službě Key Vault najdete v tématu [Přehled](../general/overview.md).

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

![Výstup po dokončení vytváření služby Key Vault](../media/keys/quick-create-portal/vault-properties.png)

## <a name="add-a-key-to-key-vault"></a>Přidání klíče do trezoru klíčů

Chcete-li přidat klíč do trezoru, stačí provést několik dalších kroků. V tomto případě přidáme klíč, který by mohl být použit aplikací. Klíč se nazývá **ExampleKey**.

1. Na stránkách vlastností trezoru klíčů vyberte **klávesy**.
2. Klikněte na **Vygenerovat/importovat**.
3. Na obrazovce **Vytvořit klávesu** zvolte následující hodnoty:
    - **Možnosti**: Generovat.
    - **Název**: ExampleKey.
    - U ostatních hodnot ponechte jejich výchozí nastavení. Klikněte na **Vytvořit**.

Jakmile obdržíte zprávu, že klíč byl úspěšně vytvořen, můžete na něj kliknout v seznamu. Zobrazí se několik jeho vlastností. Pokud kliknete na aktuální verzi, zobrazí se hodnota, kterou jste zadali v předchozím kroku.

![Základní vlastnosti](../media/keys/quick-create-portal/current-version-hidden.png)


## <a name="clean-up-resources"></a>Vyčištění prostředků

Další rychlé starty a kurzy týkající se služby Key Vault vycházejí z tohoto rychlého startu. Pokud chcete pokračovat v práci s dalšími rychlými starty a kurzy, možná budete chtít tyto prostředky zachovat.
Až nebudete prostředky potřebovat, odstraňte jejich skupinu. Tím odstraníte Key Vault i související prostředky. Odstranění skupiny prostředků přes portál:

1. Do pole Hledat v horní části portálu zadejte název vaší skupiny prostředků. Jakmile se ve výsledcích hledání zobrazí skupina prostředků použitá v tomto rychlém startu, vyberte ji.
2. Vyberte **Odstranit skupinu prostředků**.
3. Do pole **ZADEJTE NÁZEV SKUPINY PROSTŘEDKŮ:** zadejte název vaší skupiny prostředků a vyberte **Odstranit**.


## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili trezor klíčů a uložili do něj klíč. Chcete-li se dozvědět více o trezoru klíčů a o tom, jak jej integrovat s aplikacemi, pokračujte v následujících článcích.

- Přečtěte si [přehled trezoru klíčů Azure](../general/overview.md)
- Podívejte se na [průvodce vývojářem azure key vaultu](../general/developers-guide.md)
- Kontrola [doporučených postupů azure key vaultu](../general/best-practices.md)