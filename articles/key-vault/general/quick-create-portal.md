---
title: Rychlý Start – vytvoření Azure Key Vault s využitím Azure Portal
description: Rychlý Start ukazující, jak vytvořit Azure Key Vault pomocí Azure Portal
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: quickstart
ms.custom: mvc
ms.date: 12/08/2020
ms.author: mbaldwin
ms.openlocfilehash: 2d8bfdf3a4ad8b713fb6c937d61fa437fc7977f2
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749667"
---
# <a name="quickstart-create-a-key-vault-using-the-azure-portal"></a>Rychlý Start: vytvoření trezoru klíčů pomocí Azure Portal

Azure Key Vault je cloudová služba, která poskytuje zabezpečené úložiště klíčů, [tajných](../secrets/index.yml) [kódů](../keys/index.yml)a [certifikátů](../certificates/index.yml). Další informace o Key Vault najdete v tématu [o Azure Key Vault](overview.md). Další informace o tom, co je možné uložit v trezoru klíčů, najdete v tématu [informace o klíčích, tajných klíčích a certifikátech](about-keys-secrets-certificates.md).

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

V tomto rychlém startu vytvoříte Trezor klíčů s [Azure Portal](https://portal.azure.com). 

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
    - V části **Skupina prostředků** vyberte **vytvořit novou** a zadejte název skupiny prostředků.
    - V rozevírací nabídce **Umístění** zvolte umístění.
    - U ostatních možností ponechte jejich výchozí hodnoty.
6. Po zadání výše uvedených informací vyberte **Vytvořit**.

Poznamenejte si hodnoty dvou vlastností uvedených níže:

* **Název trezoru:** V tomto příkladu je to **Contoso-Vault2**. Tento název budete používat pro další kroky.
* **Identifikátor URI trezoru:** V tomto příkladu je to https://contoso-vault2.vault.azure.net/. Aplikace, které používají váš trezor prostřednictvím REST API musí používat tento identifikátor URI.

V tuto chvíli je váš účet Azure jediným účtem s oprávněním provádět operace s tímto novým trezorem.

![Výstup po dokončení vytváření služby Key Vault](../media/quick-create-portal/vault-properties.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Další rychlé starty a kurzy týkající se služby Key Vault vycházejí z tohoto rychlého startu. Pokud chcete pokračovat v práci s dalšími rychlými starty a kurzy, možná budete chtít tyto prostředky zachovat.
Až nebudete prostředky potřebovat, odstraňte jejich skupinu. Tím odstraníte Key Vault i související prostředky. Odstranění skupiny prostředků přes portál:

1. Do pole Hledat v horní části portálu zadejte název vaší skupiny prostředků. Jakmile se ve výsledcích hledání zobrazí skupina prostředků použitá v tomto rychlém startu, vyberte ji.
2. Vyberte **Odstranit skupinu prostředků**.
3. Do pole **ZADEJTE NÁZEV SKUPINY PROSTŘEDKŮ:** zadejte název vaší skupiny prostředků a vyberte **Odstranit**.


## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili Key Vault pomocí Azure Portal. Další informace o Key Vault a o tom, jak je integrovat s vašimi aplikacemi, najdete dál v článcích níže.

- Přečtěte si [přehled Azure Key Vault](overview.md)
- Přečtěte si [Přehled zabezpečení Azure Key Vault](security-overview.md)
- Další informace najdete v [příručce pro vývojáře Azure Key Vault](developers-guide.md) .
