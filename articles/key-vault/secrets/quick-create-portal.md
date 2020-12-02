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
ms.openlocfilehash: 3d7e6357fd8f1091509cbf27875c028d3af310cb
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2020
ms.locfileid: "96445917"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-the-azure-portal"></a>Rychlý start: Nastavení a načtení tajného klíče ze služby Azure Key Vault pomocí webu Azure Portal

Azure Key Vault je cloudová služba, která funguje jako zabezpečené úložiště tajných kódů. Můžete bezpečně ukládat klíče, hesla, certifikáty a další tajné klíče. Trezory klíčů Azure můžete vytvářet a spravovat přes web Azure Portal. V tomto rychlém startu vytvoříte trezor klíčů, do kterého uložíte tajný kód. 

Další informace o najdete v tématu. 
- [Přehled Key Vault](../general/overview.md)
- [Přehled tajných klíčů](about-secrets.md)

## <a name="prerequisites"></a>Předpoklady

Pokud chcete získat přístup k Azure Key Vault, budete potřebovat předplatné Azure. Pokud ještě předplatné nemáte, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

Veškerý přístup k tajným klíčům probíhá prostřednictvím Azure Key Vault. V tomto rychlém startu vytvoříte Trezor klíčů pomocí [Azure Portal](../general/quick-create-portal.md), [Azure CLI](../general/quick-create-cli.md)nebo [Azure PowerShell](../general/quick-create-powershell.md).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="add-a-secret-to-key-vault"></a>Přidání tajného klíče do služby Key Vault

Pokud chcete do trezoru přidat tajný klíč, postupujte podle následujících kroků:

1. Přejděte k novému trezoru klíčů v Azure Portal
1. Na stránkách nastavení Key Vault vyberte **tajné klíče**.
1. Klikněte na **Vygenerovat/importovat**.
1. Na obrazovce **Vytvoření tajného kódu** zvolte následující hodnoty:
    - **Možnosti nahrání:** Ruční
    - **Název**: zadejte název tajného kódu. Název tajného kódu musí být v rámci Key Vault jedinečný. Název musí být řetězec znaků 1-127, který začíná písmenem a obsahuje pouze 0-9, a-z, A-Z a-. Další informace o pojmenování najdete v tématu [Key Vault objektů, identifikátorů a verzí](../general/about-keys-secrets-certificates.md#objects-identifiers-and-versioning) .
    - **Hodnota**: zadejte hodnotu tajného kódu. Key Vault rozhraní API akceptuje a vrací tajné hodnoty jako řetězce. 
    - U ostatních hodnot ponechte jejich výchozí nastavení. Klikněte na **Vytvořit**.

Jakmile se zobrazí zpráva o úspěšném vytvoření tajného kódu, můžete na něj kliknout v seznamu. 

Další informace o atributech tajných kódů najdete v tématu [o Azure Key Vault tajných klíčů](./about-secrets.md) .

## <a name="retrieve-a-secret-from-key-vault"></a>Načtení tajného kódu z Key Vault

Pokud kliknete na aktuální verzi, zobrazí se hodnota, kterou jste zadali v předchozím kroku.

![Vlastnosti tajného klíče](../media/quick-create-portal/current-version-hidden.png)

Kliknutím na tlačítko Zobrazit hodnotu tajného klíče v pravém podokně uvidíte skrytou hodnotu. 

![Objevila se hodnota tajného klíče](../media/quick-create-portal/current-version-shown.png)

K načtení dříve vytvořeného tajného kódu můžete použít také rozhraní příkazového [řádku Azure]()nebo [Azure PowerShell]() .

## <a name="clean-up-resources"></a>Vyčištění prostředků

Další rychlé starty a kurzy týkající se služby Key Vault vycházejí z tohoto rychlého startu. Pokud chcete pokračovat v práci s dalšími rychlými starty a kurzy, možná budete chtít tyto prostředky zachovat.
Až nebudete prostředky potřebovat, odstraňte jejich skupinu. Tím odstraníte Key Vault i související prostředky. Odstranění skupiny prostředků přes portál:

1. Do pole Hledat v horní části portálu zadejte název vaší skupiny prostředků. Jakmile se ve výsledcích hledání zobrazí skupina prostředků použitá v tomto rychlém startu, vyberte ji.
2. Vyberte **Odstranit skupinu prostředků**.
3. Do pole **ZADEJTE NÁZEV SKUPINY PROSTŘEDKŮ:** zadejte název vaší skupiny prostředků a vyberte **Odstranit**.

> [!NOTE]
> Je důležité si všimnout, že když se odstraní tajný klíč, klíč, certifikát nebo Trezor klíčů, zůstane možné, že se po konfigurovatelnou dobu 7 až 90 kalendářních dnů obnoví obnovitelné období. Pokud není zadána žádná konfigurace, výchozí doba obnovení bude nastavena na 90 dní. Tato operace uživatelům poskytne dostatek času na oznámení nechtěného odstranění tajného klíče a na reakci. Další informace o odstranění a obnovení trezorů klíčů a objektů trezoru klíčů najdete v tématu [přehled Azure Key Vaultho obnovitelného odstranění](../general/soft-delete-overview.md) .

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili Key Vault a uložili do něj tajný klíč. Další informace o Key Vault a o tom, jak je integrovat s vašimi aplikacemi, najdete dál v článcích níže.

- Přečtěte si [přehled Azure Key Vault](../general/overview.md)
- Číst [zabezpečený přístup k Key Vault](../general/secure-your-key-vault.md)
- Viz [použití Key Vault s webovou aplikací App Service](../general/tutorial-net-create-vault-azure-web-app.md)
- Viz [použití Key Vault s aplikací nasazenou do virtuálního počítače](../general/tutorial-net-virtual-machine.md) .
- Další informace najdete v [příručce pro vývojáře Azure Key Vault](../general/developers-guide.md) .
- Kontrola [Azure Key Vault osvědčených postupů](../general/best-practices.md)