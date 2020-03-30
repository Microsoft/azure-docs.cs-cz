---
title: Testování definičního souboru ui
description: Popisuje, jak otestovat uživatelské prostředí pro vytváření spravované aplikace Azure prostřednictvím portálu.
author: tfitzmac
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: tomfitz
ms.openlocfilehash: e2d075a58872f9337c7d1faa642a48047e2f9ddf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78250181"
---
# <a name="test-your-portal-interface-for-azure-managed-applications"></a>Testování rozhraní portálu pro spravované aplikace Azure

Po [vytvoření souboru createUiDefinition.json](create-uidefinition-overview.md) pro spravovanou aplikaci je třeba otestovat uživatelské prostředí. Chcete-li zjednodušit testování, použijte prostředí izolovaného prostoru, které načte soubor na portálu. Spravované aplikace není potřeba nasadit. Izolovaného prostoru představuje vaše uživatelské rozhraní v aktuálním prostředí portálu na celou obrazovku. Nebo můžete použít skript pro testování rozhraní. Oba přístupy jsou uvedeny v tomto článku. Izolovaného prostoru je doporučený způsob, jak zobrazit náhled rozhraní.

## <a name="prerequisites"></a>Požadavky

* Soubor **createUiDefinition.json.** Pokud tento soubor nemáte, zkopírujte [ukázkový soubor](https://github.com/Azure/azure-quickstart-templates/blob/master/100-marketplace-sample/createUiDefinition.json).

* Předplatné Azure. Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="use-sandbox"></a>Použití izolovaného prostoru

1. Otevřete [sandbox definice](https://portal.azure.com/?feature.customPortal=false&#blade/Microsoft_Azure_CreateUIDef/SandboxBlade)vytvoření ui .

   ![Zobrazit pískoviště](./media/test-createuidefinition/show-sandbox.png)

1. Nahraďte prázdnou definici obsahem souboru createUiDefinition.json. Vyberte **Náhled**.

   ![Vybrat náhled](./media/test-createuidefinition/select-preview.png)

1. Zobrazí se formulář, který jste vytvořili. Můžete krokovat uživatelské prostředí a vyplnit hodnoty.

   ![Zobrazit formulář](./media/test-createuidefinition/show-ui-form.png)

### <a name="troubleshooting"></a>Řešení potíží

Pokud se formulář po výběru **náhledu**nezobrazí , může se zobrazit chyba syntaxe. Vyhledejte červený indikátor na pravém posuvníku a přejděte na něj.

![Zobrazit chybu syntaxe](./media/test-createuidefinition/show-syntax-error.png)

Pokud se formulář nezobrazí a místo toho se zobrazí ikona cloudu s roztržením, zobrazí se chyba formuláře, například chybějící vlastnost. Otevřete nástroje pro vývojáře webu v prohlížeči. **Konzole** zobrazuje důležité zprávy o vašem rozhraní.

![Zobrazit chybu](./media/test-createuidefinition/show-error.png)

## <a name="use-test-script"></a>Použití testovacího skriptu

Chcete-li otestovat rozhraní na portálu, zkopírujte jeden z následujících skriptů do místního počítače:

* [Skript pro boční načítání prostředí PowerShell - Modul Az](https://github.com/Azure/azure-quickstart-templates/blob/master/SideLoad-AzCreateUIDefinition.ps1)
* [Skript pro boční načítání powershellu – modul Azure](https://github.com/Azure/azure-quickstart-templates/blob/master/SideLoad-CreateUIDefinition.ps1)
* [Skript pro boční načítání Azure CLI](https://github.com/Azure/azure-quickstart-templates/blob/master/sideload-createuidef.sh)

Chcete-li zobrazit soubor rozhraní na portálu, spusťte stažený skript. Skript vytvoří účet úložiště ve vašem předplatném Azure a nahraje soubor createUiDefinition.json do účtu úložiště. Účet úložiště se vytvoří při prvním spuštění skriptu nebo v případě, že byl odstraněn účet úložiště. Pokud účet úložiště již existuje ve vašem předplatném Azure, skript jej znovu použije. Skript otevře portál a načte soubor z účtu úložiště.

Zadejte umístění pro účet úložiště a zadejte složku, která má soubor createUiDefinition.json.

Pokud používáte PowerShell, použijte:

```powershell
.\SideLoad-AzCreateUIDefinition.ps1 `
  -StorageResourceGroupLocation southcentralus `
  -ArtifactsStagingDirectory .\100-Marketplace-Sample
```

Pokud používáte Azure CLI, použijte:

```bash
./sideload-createuidef.sh \
  -l southcentralus \
  -a .\100-Marketplace-Sample
```

Pokud je soubor createUiDefinition.json ve stejné složce jako skript a již jste vytvořili účet úložiště, nemusíte tyto parametry zajišťovat.

Pokud používáte PowerShell, použijte:

```powershell
.\SideLoad-AzCreateUIDefinition.ps1
```

Pokud používáte Azure CLI, použijte:

```bash
./sideload-createuidef.sh
```

Skript otevře novou kartu ve vašem prohlížeči. Zobrazí portál s rozhraním pro vytvoření spravované aplikace.

Zadejte hodnoty pro pole. Po dokončení se zobrazí hodnoty, které jsou předány do šablony, které lze nalézt v konzole vývojářských nástrojů vašeho prohlížeče.

![Zobrazit hodnoty](./media/test-createuidefinition/show-json.png)

Tyto hodnoty můžete použít jako soubor parametrů pro testování šablony nasazení.

Pokud portál přestane reagovat na souhrnné obrazovce, může být chyba ve výstupní části. Je například možné, že jste odkazovali na ovládací prvek, který neexistuje. Pokud je parametr ve výstupu prázdný, parametr může odkazovat na vlastnost, která neexistuje. Například odkaz na ovládací prvek je platný, ale odkaz na vlastnost není platný.

## <a name="test-your-solution-files"></a>Testování souborů řešení

Nyní, když jste ověřili, že rozhraní portálu funguje podle očekávání, je čas ověřit, zda je soubor createUiDefinition správně integrován s vaším hlavním souboremTemplate.json. Můžete spustit test ověřovacího skriptu a otestovat obsah souborů řešení, včetně souboru createUiDefinition. Skript ověřuje syntaxi JSON, kontroluje výrazy regulárních výrazů v textových polích a zajišťuje, aby výstupní hodnoty rozhraní portálu odpovídaly parametrům šablony. Informace o spuštění tohoto skriptu naleznete v tématu [Spuštění statických kontrol ověření šablon](https://github.com/Azure/azure-quickstart-templates/tree/master/test).

## <a name="next-steps"></a>Další kroky

Po ověření rozhraní portálu se dozvíte o zpřístupnění [spravované aplikace Azure na Marketplace](publish-marketplace-app.md).
