---
title: Otestování definice uživatelského rozhraní pro Azure Managed Applications | Microsoft Docs
description: V této části najdete popis postupu testování uživatelského prostředí pro vytvoření spravované aplikace Azure prostřednictvím portálu.
author: tfitzmac
ms.service: managed-applications
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: tomfitz
ms.openlocfilehash: 54eb2df06df56c33e1a3cd74e7a4a93c07aab682
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2019
ms.locfileid: "69575662"
---
# <a name="test-your-portal-interface-for-azure-managed-applications"></a>Otestování rozhraní portálu pro Azure Managed Applications

Po [Vytvoření souboru createUiDefinition. JSON](create-uidefinition-overview.md) pro spravovanou aplikaci je nutné otestovat činnost koncového uživatele. Pro zjednodušení testování použijte prostředí izolovaného prostoru (sandbox), které načte váš soubor na portálu. Nemusíte ve skutečnosti nasazovat spravovanou aplikaci. Sandbox prezentuje vaše uživatelské rozhraní v aktuálním, samoobslužném portálu pro celou obrazovku. Nebo můžete použít skript pro otestování rozhraní. Oba přístupy jsou uvedené v tomto článku. Izolovaný prostor (sandbox) je doporučený způsob, jak zobrazit náhled rozhraní.

## <a name="prerequisites"></a>Požadavky

* Soubor **createUiDefinition. JSON** . Pokud tento soubor nemáte, zkopírujte [ukázkový soubor](https://github.com/Azure/azure-quickstart-templates/blob/master/100-marketplace-sample/createUiDefinition.json).

* Předplatné Azure. Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="use-sandbox"></a>Použít izolovaný prostor

1. Otevřete okno [Vytvoření izolovaného prostoru (sandbox) definice uživatelského rozhraní](https://portal.azure.com/?feature.customPortal=false&#blade/Microsoft_Azure_CreateUIDef/SandboxBlade).

   ![Zobrazit izolovaný prostor](./media/test-createuidefinition/show-sandbox.png)

1. Nahraďte prázdnou definici obsahem souboru createUiDefinition. JSON. Vyberte **Náhled**.

   ![Vybrat náhled](./media/test-createuidefinition/select-preview.png)

1. Zobrazí se formulář, který jste vytvořili. Můžete krokovat s uživatelským prostředím a vyplnit hodnoty.

   ![Zobrazit formulář](./media/test-createuidefinition/show-ui-form.png)

### <a name="troubleshooting"></a>Řešení potíží

Pokud se formulář po výběru **náhledu**nezobrazí, může dojít k chybě syntaxe. Vyhledejte červený indikátor na pravém posuvníku a přejděte k němu.

![Zobrazit chybu syntaxe](./media/test-createuidefinition/show-syntax-error.png)

Pokud se formulář nezobrazí a místo toho se zobrazí ikona cloudu s přerušením, ve formuláři dojde k chybě, jako je například chybějící vlastnost. V prohlížeči otevřete web Vývojářské nástroje. **Konzola** zobrazuje důležité zprávy o vašem rozhraní.

![Zobrazit chybu](./media/test-createuidefinition/show-error.png)

## <a name="use-test-script"></a>Použít testovací skript

Pokud chcete otestovat rozhraní na portálu, zkopírujte do svého místního počítače jeden z následujících skriptů:

* [Koncová rutina skriptu PowerShell – AZ Module](https://github.com/Azure/azure-quickstart-templates/blob/master/SideLoad-AzCreateUIDefinition.ps1)
* [PowerShellový skript na straně zatížení – modul Azure](https://github.com/Azure/azure-quickstart-templates/blob/master/SideLoad-CreateUIDefinition.ps1)
* [Skript Azure CLI na straně zatížení](https://github.com/Azure/azure-quickstart-templates/blob/master/sideload-createuidef.sh)

Pokud chcete zobrazit soubor rozhraní na portálu, spusťte stažený skript. Skript vytvoří účet úložiště ve vašem předplatném Azure a nahraje soubor createUiDefinition. JSON do účtu úložiště. Účet úložiště se vytvoří při prvním spuštění skriptu nebo při odstranění účtu úložiště. Pokud účet úložiště už ve vašem předplatném Azure existuje, skript ho znovu použije. Skript otevře portál a načte soubor z účtu úložiště.

Zadejte umístění pro účet úložiště a zadejte složku, která obsahuje soubor createUiDefinition. JSON.

Pokud používáte PowerShell, použijte:

```powershell
.\SideLoad-AzCreateUIDefinition.ps1 `
  -StorageResourceGroupLocation southcentralus `
  -ArtifactsStagingDirectory .\100-Marketplace-Sample
```

Pokud používáte Azure CLI, použijte:

```azurecli
./sideload-createuidef.sh \
  -l southcentralus \
  -a .\100-Marketplace-Sample
```

Pokud je soubor createUiDefinition. JSON ve stejné složce jako skript a už jste vytvořili účet úložiště, nemusíte tyto parametry zadávat.

Pokud používáte PowerShell, použijte:

```powershell
.\SideLoad-AzCreateUIDefinition.ps1
```

Pokud používáte Azure CLI, použijte:

```azurecli
./sideload-createuidef.sh
```

Skript otevře v prohlížeči novou kartu. Zobrazuje portál s vaším rozhraním pro vytvoření spravované aplikace.

Zadejte hodnoty pro pole. Po dokončení uvidíte hodnoty, které se předávají do šablony, které najdete v konzole vývojářských nástrojů v prohlížeči.

![Zobrazit hodnoty](./media/test-createuidefinition/show-json.png)

Tyto hodnoty můžete použít jako soubor parametrů pro testování šablony nasazení.

Pokud portál přestane reagovat na obrazovku souhrnu, může se jednat o chybu v části výstup. Například jste mohli odkazovat na ovládací prvek, který neexistuje. Pokud je parametr ve výstupu prázdný, parametr může odkazovat na vlastnost, která neexistuje. Například odkaz na ovládací prvek je platný, ale odkaz na vlastnost není platný.

## <a name="test-your-solution-files"></a>Testování souborů řešení

Teď, když jste ověřili, že rozhraní vašeho portálu pracuje podle očekávání, je čas ověřit, jestli je váš soubor createUiDefinition správně integrovaný do souboru mainTemplate. JSON. Můžete spustit test ověřovacího skriptu a otestovat obsah souborů řešení, včetně souboru createUiDefinition. Skript ověřuje syntaxi JSON, kontroluje výrazy regulárních výrazů u textových polí a zajišťuje, aby výstupní hodnoty rozhraní portálu odpovídaly parametrům vaší šablony. Informace o spuštění tohoto skriptu najdete v tématu [spuštění kontroly statického ověřování pro šablony](https://github.com/Azure/azure-quickstart-templates/tree/master/test/template-validation-tests).

## <a name="next-steps"></a>Další kroky

Po ověření rozhraní portálu se dozvíte, jak [v Marketplace zpřístupnit spravovanou aplikaci Azure](publish-marketplace-app.md).
