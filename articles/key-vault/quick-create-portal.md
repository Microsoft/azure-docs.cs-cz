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
ms.openlocfilehash: a57370b7bf63ad73318ba13eff1b554aead7e186
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79241119"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-the-azure-portal"></a>Rychlý start: Nastavení a načtení tajného klíče ze služby Azure Key Vault pomocí webu Azure Portal

Azure Key Vault je cloudová služba, která funguje jako zabezpečené úložiště tajných kódů. Můžete bezpečně ukládat klíče, hesla, certifikáty a další tajné klíče. Trezory klíčů Azure můžete vytvářet a spravovat přes web Azure Portal. V tomto rychlém startu vytvoříte trezor klíčů, do kterého uložíte tajný kód. Další informace o službě Key Vault najdete v tématu [Přehled](key-vault-overview.md).

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="create-a-vault"></a>Vytvoření trezoru

1. V nabídce Portál Azure nebo na **domovské** stránce vyberte **Vytvořit prostředek**.
2. Do vyhledávacího pole zadejte **Key Vault**.
3. V seznamu výsledků zvolte **Key Vault**.
4. V části Key Vault zvolte **Vytvořit**.
5. V části **Vytvořit trezor klíčů** zadejte následující informace:
    - **Název:** Je potřeba zadat jedinečný název. Pro tento rychlý start používáme **Contoso-vault2**. 
    - **Předplatné:** Zvolte předplatné.
    - V části **Skupina prostředků**zvolte **Vytvořit nový** a zadejte název skupiny prostředků.
    - V rozevírací nabídce **Umístění** zvolte umístění.
    - U ostatních možností ponechte jejich výchozí hodnoty.
6. Po zadání výše uvedených informací vyberte **Vytvořit**.

Poznamenejte si hodnoty dvou vlastností uvedených níže:

* **Název trezoru:** V tomto příkladu je to **Contoso-Vault2**. Tento název budete používat pro další kroky.
* **Identifikátor URI trezoru:** V tomto příkladu je to https://contoso-vault2.vault.azure.net/. Aplikace, které používají váš trezor prostřednictvím REST API musí používat tento identifikátor URI.

V tuto chvíli je váš účet Azure jediným účtem s oprávněním provádět operace s tímto novým trezorem.

![Výstup po dokončení vytváření služby Key Vault](./media/quick-create-portal/vault-properties.png)

## <a name="add-a-secret-to-key-vault"></a>Přidání tajného klíče do služby Key Vault

Pokud chcete do trezoru přidat tajný klíč, stačí provést několik dalších kroků. V tomto případě přidáme heslo, které může používat aplikace. Heslo se nazývá **ExamplePassword** a ukládáme hodnotu **hVFkk965BuUv** v něm.

1. Na stránkách vlastností trezoru klíčů vyberte **položku Tajné položky**.
2. Klikněte na **Vygenerovat/importovat**.
3. Na obrazovce **Vytvoření tajného kódu** zvolte následující hodnoty:
    - **Možnosti nahrání:** Ruční
    - **Název:** ExamplePassword
    - **Hodnota**: hVFkk965BuUv
    - U ostatních hodnot ponechte jejich výchozí nastavení. Klikněte na **Vytvořit**.

Jakmile se zobrazí zpráva o úspěšném vytvoření tajného kódu, můžete na něj kliknout v seznamu. Zobrazí se několik jeho vlastností. Pokud kliknete na aktuální verzi, zobrazí se hodnota, kterou jste zadali v předchozím kroku.

![Vlastnosti tajného klíče](./media/quick-create-portal/current-version-hidden.png)

Kliknutím na tlačítko "Zobrazit tajnou hodnotu" v pravém podokně uvidíte skrytou hodnotu. 

![Objevila se tajná hodnota](./media/quick-create-portal/current-version-shown.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Další rychlé starty a kurzy týkající se služby Key Vault vycházejí z tohoto rychlého startu. Pokud chcete pokračovat v práci s dalšími rychlými starty a kurzy, možná budete chtít tyto prostředky zachovat.
Až nebudete prostředky potřebovat, odstraňte jejich skupinu. Tím odstraníte Key Vault i související prostředky. Odstranění skupiny prostředků přes portál:

1. Do pole Hledat v horní části portálu zadejte název vaší skupiny prostředků. Jakmile se ve výsledcích hledání zobrazí skupina prostředků použitá v tomto rychlém startu, vyberte ji.
2. Vyberte **Odstranit skupinu prostředků**.
3. Do pole **ZADEJTE NÁZEV SKUPINY PROSTŘEDKŮ:** zadejte název vaší skupiny prostředků a vyberte **Odstranit**.


## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili trezor klíčů a uložili do něj tajný klíč. Chcete-li se dozvědět více o trezoru klíčů a o tom, jak jej integrovat s aplikacemi, pokračujte v následujících článcích.

- Přečtěte si [přehled trezoru klíčů Azure](key-vault-overview.md)
- Podívejte se na [průvodce vývojářem azure key vaultu](key-vault-developers-guide.md)
- Informace o [klíčích, tajných kódech a certifikátech](about-keys-secrets-and-certificates.md)
- Kontrola [doporučených postupů azure key vaultu](key-vault-best-practices.md)