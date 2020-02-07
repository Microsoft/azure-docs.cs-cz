---
title: Tlačítko nasadit do Azure
description: Pomocí tlačítka nasaďte Azure Resource Manager šablony z úložiště GitHub.
ms.topic: conceptual
ms.date: 02/05/2020
ms.openlocfilehash: cf22203b843f4483f495b6eba9a522267c010a79
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2020
ms.locfileid: "77050587"
---
# <a name="use-a-deployment-button-to-deploy-templates-from-github-repository"></a>Použití tlačítka nasazení k nasazení šablon z úložiště GitHub

Tento článek popisuje, jak pomocí tlačítka **nasadit do Azure** nasazovat šablony z úložiště GitHub. Tlačítko můžete přidat přímo do souboru README.md v úložišti GitHub nebo na webovou stránku, která odkazuje na úložiště.

## <a name="use-common-image"></a>Použít běžný obrázek

Chcete-li přidat tlačítko na webovou stránku nebo úložiště, použijte následující obrázek:

```html
<img src="https://aka.ms/deploytoazurebutton"/>
```

Obrázek se zobrazí jako:

![Tlačítko nasadit do Azure](https://aka.ms/deploytoazurebutton)

## <a name="create-url-for-deploying-template"></a>Vytvořit adresu URL pro nasazení šablony

Pokud chcete vytvořit adresu URL pro vaši šablonu, začněte s nezpracovanými adresami URL šablony v úložišti:

```html
https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

Pak je adresa URL zakóduje. Můžete použít online kodér nebo spustit příkaz. Následující příklad PowerShellu ukazuje, jak adresa URL kóduje hodnotu.

```powershell
[uri]::EscapeDataString($url)
```

Adresa URL příkladu má následující hodnotu, pokud je zakódována adresa URL.

```html
https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json
```

Každý odkaz začíná stejnou základní adresou URL:

```html
https://portal.azure.com/#create/Microsoft.Template/uri/
```

Přidejte odkaz šablony s kódováním URL na konec základní adresy URL.

```html
https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json
```

Máte úplnou adresu URL odkazu.

## <a name="create-deploy-to-azure-button"></a>Tlačítko pro vytvoření nasazení do Azure

Nakonec vložte odkaz a obrázek dohromady. Tento kód HTML můžete přidat do souboru README.md v úložišti GitHub nebo na webové stránce.

```html
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json" target="_blank">
  <img src="https://aka.ms/deploytoazurebutton"/>
</a>
```

## <a name="deploy-the-template"></a>Nasazení šablony

Chcete-li otestovat úplné řešení, vyberte následující tlačítko:
<br><br>
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json" target="_blank">
  <img src="https://aka.ms/deploytoazurebutton"/>
</a>

Portál zobrazí podokno, které umožňuje snadno zadat hodnoty parametrů. Parametry jsou předem vyplněny výchozími hodnotami ze šablony.

![Nasazení pomocí portálu](./media/deploy-to-azure-button/portal.png)

## <a name="next-steps"></a>Další kroky

- Další informace o šablonách naleznete v tématu [pochopení struktury a syntaxe šablon Azure Resource Manager](template-syntax.md).
