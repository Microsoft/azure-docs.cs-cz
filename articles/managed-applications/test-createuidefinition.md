---
title: Otestování definice uživatelského rozhraní pro spravované aplikace Azure | Dokumentace Microsoftu
description: Popisuje, jak otestovat uživatelské prostředí pro vytvoření spravované aplikace prostřednictvím portálu Azure.
author: tfitzmac
ms.service: managed-applications
ms.topic: conceptual
ms.date: 05/26/2019
ms.author: tomfitz
ms.openlocfilehash: 99ca319910be2cb20214172826eb40361abe72f0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66257596"
---
# <a name="test-your-portal-interface-for-azure-managed-applications"></a>Testovací rozhraní portálu pro Azure Managed Applications

Po [vytváření souboru createUiDefinition.json](create-uidefinition-overview.md) pro vaši spravovanou aplikaci, je potřeba otestovat uživatelské prostředí. Pro zjednodušení testování, používejte prostředí izolovaného prostoru, který načte soubor na portálu. Není nutné skutečně nasadit spravované aplikace. Sandbox uvede uživatelského rozhraní v prostředí portálu aktuální, celou obrazovku. Nebo můžete použít skript prostředí PowerShell pro testování rozhraní, ale používá starší verzi zobrazení na portálu. V tomto článku jsou uvedeny oba přístupy. Sandbox jsou doporučeným způsobem, jak ve verzi preview rozhraní.

## <a name="prerequisites"></a>Požadavky

* A **createUiDefinition.json** souboru. Pokud nemáte k dispozici tento soubor, zkopírujte [ukázkový soubor](https://github.com/Azure/azure-quickstart-templates/blob/master/100-marketplace-sample/createUiDefinition.json).

* Předplatné Azure. Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="use-sandbox"></a>Použití izolovaného prostoru

1. Otevřít [vytvoření izolovaného prostoru definice uživatelského rozhraní](https://portal.azure.com/?feature.customPortal=false&#blade/Microsoft_Azure_CreateUIDef/SandboxBlade).

   ![Zobrazit izolovaného prostoru](./media/test-createuidefinition/show-sandbox.png)

1. Nahraďte obsah souboru createUiDefinition.json prázdnou definici. Vyberte **ve verzi Preview**.

   ![Vyberte Náhled](./media/test-createuidefinition/select-preview.png)

1. Zobrazí se formulář, který jste vytvořili. Můžete projít uživatelské prostředí a vyplňte požadované hodnoty.

   ![Zobrazit formulář](./media/test-createuidefinition/show-ui-form.png)

### <a name="troubleshooting"></a>Řešení potíží

Pokud se nezobrazí formuláře po výběru **ve verzi Preview**, bude pravděpodobně chyba syntaxe. Vyhledejte red ukazatel na pravý posuvník a přejít k němu.

![Chyba syntaxe show](./media/test-createuidefinition/show-syntax-error.png)

Pokud se nezobrazí formuláře a místo toho se zobrazí ikona cloudu s rozevírací vše, formuláře došlo k chybě, jako je například chybějící vlastnost. V prohlížeči otevřete Web Developer Tools. **Konzoly** zobrazuje důležité zprávy o rozhraní.

![Zobrazit chybu](./media/test-createuidefinition/show-error.png)

## <a name="use-test-script"></a>Použití skriptu testu

K otestování rozhraní na portálu, zkopírujte jeden z následujících skriptů do místního počítače:

* [Načtení skriptu prostředí PowerShell](https://github.com/Azure/azure-quickstart-templates/blob/master/SideLoad-CreateUIDefinition.ps1)
* [Skript Azure CLI bokem](https://github.com/Azure/azure-quickstart-templates/blob/master/sideload-createuidef.sh)

Váš soubor rozhraní na portálu najdete spuštěním staženého skriptu. Tento skript vytvoří účet úložiště ve vašem předplatném Azure a createUiDefinition.json soubor nahraje do účtu úložiště. Účet úložiště se vytvoří při prvním spuštění skriptu nebo pokud účet úložiště se odstranil. Pokud účet úložiště existuje ve vašem předplatném Azure, opětovně používá skript ho. Skript se otevře na portálu a načte soubor z účtu úložiště.

Zadejte umístění pro účet úložiště a zadat složku, která obsahuje váš soubor createUiDefinition.json.

Pokud používáte PowerShell, použijte:

```powershell
.\SideLoad-CreateUIDefinition.ps1 `
  -StorageResourceGroupLocation southcentralus `
  -ArtifactsStagingDirectory .\100-Marketplace-Sample
```

Pokud používáte Azure CLI, použijte:

```azurecli
./sideload-createuidef.sh \
  -l southcentralus \
  -a .\100-Marketplace-Sample
```

Pokud váš soubor createUiDefinition.json je ve stejné složce jako skript a už jste vytvořili účet úložiště, není nutné zadat tyto parametry.

Pokud používáte PowerShell, použijte:

```powershell
.\SideLoad-CreateUIDefinition.ps1
```

Pokud používáte Azure CLI, použijte:

```azurecli
./sideload-createuidef.sh
```

Skript se otevře na nové kartě v prohlížeči. Zobrazí se na portálu s rozhraní pro vytvoření spravované aplikace.

![Zobrazit portál](./media/test-createuidefinition/view-portal.png)

Zadejte hodnoty pro pole. Až budete hotovi, uvidíte hodnoty, které jsou předány do šablony.

![Zobrazí hodnoty](./media/test-createuidefinition/show-json.png)

Tyto hodnoty můžete použít jako soubor parametrů pro testování nasazení šablony.

Pokud na souhrnné obrazovce přestane reagovat na portálu, může jít o chybu v části výstupu. Může mít například odkazuje ovládací prvek, který neexistuje. Pokud parametr ve výstupu je prázdný, může parametr odkazuje vlastnost, která neexistuje. Například odkaz na ovládací prvek je platný, ale není platný odkaz na vlastnost.

## <a name="test-your-solution-files"></a>Testovací soubory řešení

Teď, když jste ověřili, že rozhraní portálu funguje podle očekávání, je čas k ověření, že je váš soubor createUiDefinition náležité integrace souborem mainTemplate.json. Můžete spustit ověření skriptu testu obsah soubory řešení, včetně soubor createUiDefinition. Skript ověří syntaxi souboru JSON, vyhledává výrazy regulární výraz textových polí a je zajištěno, že výstupní hodnoty rozhraní portálu odpovídat parametrům šablony. Informace o tomto skriptu najdete v tématu [spuštění kontroly statického ověření pro šablony](https://github.com/Azure/azure-quickstart-templates/tree/master/test/template-validation-tests).

## <a name="next-steps"></a>Další postup

Po ověření rozhraní portálu, přečtěte si informace o provádění vaší [Azure spravované aplikace k dispozici na webu Marketplace](publish-marketplace-app.md).
