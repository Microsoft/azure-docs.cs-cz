---
title: Vytvoření šablony Azure Resource Manageru pro nasazení šifrovaného účtu úložiště | Microsoft Docs
description: Vytvořte šablonu pro nasazení šifrovaného účtu úložiště pomocí nástroje Visual Studio Code.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 10/30/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 79572a364c2346ffd567cab7d3633ae398715210
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50239946"
---
# <a name="tutorial-deploy-an-encrypted-azure-storage-account-with-resource-manager-template"></a>Kurz: Nasazení šifrovaného účtu služby Azure Storage pomocí šablony Resource Manageru

Zjistěte, jak vyhledat informace o schématu šablony a jak pomocí těchto informací vytvářet šablony Azure Resource Manageru.

V tomto kurzu použijete základní šablonu Azure pro rychlý start. Pomocí referenční dokumentace k šablonám tuto šablonu přizpůsobíte tak, abyste s její pomocí vytvořili šifrovaný účet služby Storage.

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Otevření šablony pro rychlý start
> * Vysvětlení šablony
> * Vyhledání referenčních informací k šablonám
> * Úprava šablony
> * Nasazení šablony

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto článku potřebujete:

* [Visual Studio Code](https://code.visualstudio.com/) s [rozšířením Nástroje Resource Manageru](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites)

## <a name="open-a-quickstart-template"></a>Otevření šablony pro rychlý start

[Šablony Azure pro rychlý start](https://azure.microsoft.com/resources/templates/) slouží jako úložiště šablon Resource Manageru. Místo vytvoření šablony úplně od začátku si můžete najít ukázkovou šablonu a přizpůsobit ji. Šablona, kterou jsme použili v tomto rychlém startu, se nazývá [Create a standard storage account](https://azure.microsoft.com/resources/templates/101-storage-account-create/) (Vytvoření standardního účtu úložiště). Šablona definuje prostředek účtu služby Azure Storage.

1. V nástroji Visual Studio Code vyberte **File** (Soubor) >**Open File** (Otevřít soubor).
2. Do pole **File name** (Název souboru) vložte následující adresu URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```
3. Výběrem **Open** (Otevřít) soubor otevřete.
4. Vyberte **File** (Soubor) >**Save As** (Uložit jako) a soubor uložte jako **azuredeploy.json** na místní počítač.

## <a name="understand-the-schema"></a>Vysvětlené schématu

1. V nástroji VS Code sbalte šablony na kořenovou úroveň. Vidíte nejjednodušší strukturu s následující elementy:

    ![Nejjednodušší struktura šablony Resource Manageru](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-simplest-structure.png)

    * **$schema**: zadejte umístění souboru schématu JSON, který popisuje verzi jazyka šablony.
    * **contentVersion**: pro tento element můžete zvolit libovolnou hodnotu, která dokumentuje významné změny v šabloně.
    * **parameters**: přizpůsobte nasazení prostředků zadáním hodnot, které se použijí při provádění nasazení.
    * **variables**: určete hodnoty sloužící v šabloně jako fragmenty formátu JSON, aby se zjednodušily výrazy jazyka šablony.
    * **resources**: určete typy prostředků nasazovaných nebo aktualizovaných ve skupině prostředků.
    * **outputs**: uvádí hodnoty vrácené po nasazení.

2. Rozbalte element **resources**. Obsahuje definici prostředku `Microsoft.Storage/storageAccounts`. Šablona vytvoří nešifrovaný účet služby Storage.

    ![Definice účtu úložiště v šabloně Resource Manageru](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-encrypted-storage-resource.png)

## <a name="find-the-template-reference"></a>Vyhledání referenčních informací k šablonám

1. Přejděte na článek o [šablonách Azure](https://docs.microsoft.com/azure/templates/).
2. V nabídce **Filtrovat podle názvu**, zadejte **Účty úložiště**.
3. Vyberte **odkaz / šablonu odkazu / úložiště / úložiště účtů**, jak je znázorněno na následujícím snímku obrazovky:

    ![Odkaz šablony Azure Resource Manageru na účet úložiště](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-resources-reference-storage-accounts.png)

4. Vyhledejte informace o definici týkající se šifrování.  

    ```json
    "encryption": {
      "services": {
        "blob": {
          "enabled": boolean
        },
        "file": {
          "enabled": boolean
        }
      },
      "keySource": "string",
      "keyvaultproperties": {
        "keyname": "string",
        "keyversion": "string",
        "keyvaulturi": "string"
      }
    },
    ```

    Na stejné webové stránce následující popis potvrzuje, že objekt `encryption` slouží k vytvoření šifrovaného účtu úložiště.

    ![Referenční šablona Resource Manageru – šifrování účtu úložiště](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-resources-reference-storage-accounts-encryption.png)

    Šifrovací klíč navíc můžete spravovat dvěma způsoby. Můžete použít šifrovací klíče s Šifrováním služby Storage spravované Microsoftem nebo vlastní šifrovací klíče. Pro zjednodušení tohoto kurzu použijete možnost `Microsoft.Storage`, takže nemusíte vytvářet službu Azure Key Vault.

    ![Referenční šablona Resource Manageru – objekt šifrování účtu úložiště](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-resources-reference-storage-accounts-encryption-object.png)

    Váš objekt šifrování by měl vypadat takto:

    ```json
    "encryption": {
        "services": {
            "blob": {
                "enabled": true
            },
            "file": {
              "enabled": true
            }
        },
        "keySource": "Microsoft.Storage"
    }
    ```

## <a name="edit-the-template"></a>Úprava šablony

Ve Visual Studio Code upravte šablonu tak, aby element resources vypadal následovně:

![Šablona Resource Manageru – element resources šifrovaného účtu úložiště](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-encrypted-storage-resources.png)

## <a name="deploy-the-template"></a>Nasazení šablony

Informace o procesu nasazení najdete v části [Nasazení šablony](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#deploy-the-template) v rychlém startu pro Visual Studio Code.

Následující snímek obrazovky ukazuje příkaz rozhraní příkazového řádku pro zobrazení nově vytvořeného účtu úložiště, který značí povolení šifrování pro úložiště objektů blob.

![Azure Resource Manager – šifrovaný účet úložiště](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-encrypted-storage-account.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nasazené prostředky Azure nepotřebujete, vyčistěte je odstraněním skupiny prostředků.

1. Na portálu Azure Portal vyberte v nabídce nalevo **Skupina prostředků**.
2. Do pole **Filtrovat podle názvu** zadejte název skupiny prostředků.
3. Vyberte název skupiny prostředků.  Ve skupině prostředků uvidíte celkem šest prostředků.
4. V nabídce nahoře vyberte **Odstranit skupinu prostředků**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli, jak přizpůsobit existující šablonu pomocí odkazu na šablonu. Naučte se, jak vytvořit několik instancí účtu úložiště, viz:

> [!div class="nextstepaction"]
> [Vytvoření víc instancí](./resource-manager-tutorial-create-multiple-instances.md)
