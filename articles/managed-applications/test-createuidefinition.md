---
title: Otestování definice uživatelského rozhraní pro spravované aplikace Azure | Dokumentace Microsoftu
description: Popisuje, jak otestovat uživatelské prostředí pro vytvoření spravované aplikace prostřednictvím portálu Azure.
services: managed-applications
documentationcenter: na
author: tfitzmac
ms.service: managed-applications
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/10/2018
ms.author: tomfitz
ms.openlocfilehash: 2c313538e297c5781b48fcfe9d0d5390f94c97f5
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/11/2018
ms.locfileid: "40043690"
---
# <a name="test-azure-portal-interface-for-your-managed-application"></a>Testování rozhraní Azure portal pro vaši spravovanou aplikaci
Po [vytváření souboru createUiDefinition.json](create-uidefinition-overview.md) pro spravované aplikace Azure, je potřeba otestovat uživatelské prostředí. Pro zjednodušení testování pomocí skriptu, který načte soubor na portálu. Není nutné skutečně nasadit spravované aplikace.

## <a name="prerequisites"></a>Požadavky

* A **createUiDefinition.json** souboru. Pokud nemáte k dispozici tento soubor, zkopírujte [ukázkový soubor](https://github.com/Azure/azure-quickstart-templates/blob/master/test/template-validation-tests/sample-template/createUIDefinition.json) a uloží do místního prostředí.

* Předplatné Azure. Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="download-test-script"></a>Stáhněte si skript testu

K otestování rozhraní na portálu, zkopírujte jeden z následujících skriptů do místního počítače:

* [Načtení skriptu prostředí PowerShell](https://github.com/Azure/azure-quickstart-templates/blob/master/SideLoad-CreateUIDefinition.ps1)
* [Skript Azure CLI bokem](https://github.com/Azure/azure-quickstart-templates/blob/master/sideload-createuidef.sh)

## <a name="run-script"></a>Spuštění skriptu

Váš soubor rozhraní na portálu najdete spuštěním staženého skriptu. Tento skript vytvoří účet úložiště ve vašem předplatném Azure a createUiDefinition.json soubor nahraje do účtu úložiště. Potom se otevře na portálu a načte soubor z účtu úložiště.

Zadejte umístění pro účet úložiště a zadat složku, která obsahuje váš soubor createUiDefinition.json. Stačí zadat úložiště účtu umístění první čas spuštění skriptu nebo pokud účet úložiště se odstranil.

Pokud používáte PowerShell, použijte:

```powershell
.\SideLoad-CreateUIDefinition.ps1 `
  -StorageResourceGroupLocation southcentralus `
  -ArtifactsStagingDirectory <path-to-folder-with-createuidefinition>
```

Pokud používáte Azure CLI, použijte:

```azurecli
./sideload-createuidef.sh \
  -l southcentralus \
  -a <path-to-folder-with-createuidefinition>
```

## <a name="test-your-interface"></a>Testovací rozhraní

Skript se otevře na nové kartě v prohlížeči. Zobrazí se na portálu s rozhraní pro vytvoření spravované aplikace.

![Zobrazit portál](./media/test-createuidefinition/view-portal.png)

Před vyplňování polí, otevřete Web Developer Tools v prohlížeči. **Konzoly** zobrazuje důležité zprávy o rozhraní.

![Vyberte konzoly](./media/test-createuidefinition/select-console.png)

Pokud vaše definice rozhraní obsahuje chybu, viz popis v konzole.

![Zobrazit chybu](./media/test-createuidefinition/show-error.png)

Zadejte hodnoty pro pole. Až budete hotovi, uvidíte hodnoty, které jsou předány do šablony.

![Zobrazí hodnoty](./media/test-createuidefinition/show-json.png)

## <a name="test-your-solution-files"></a>Testovací soubory řešení

Teď, když jste ověřili, že rozhraní portálu funguje podle očekávání, je čas k ověření, že je váš soubor createUiDefinition náležité integrace souborem mainTemplate.json. Můžete spustit ověření skriptu testu obsah soubory řešení, včetně soubor createUiDefinition. Skript ověří syntaxi souboru JSON, vyhledává výrazy regulární výraz textových polí a je zajištěno, že výstupní hodnoty rozhraní portálu odpovídat parametrům šablony. Informace o tomto skriptu najdete v tématu [spuštění kontroly statického ověření pro šablony](https://github.com/Azure/azure-quickstart-templates/tree/master/test/template-validation-tests).

## <a name="next-steps"></a>Další postup

Po ověření rozhraní portálu, přečtěte si informace o provádění vaší [Azure spravované aplikace k dispozici na webu Marketplace](publish-marketplace-app.md).
