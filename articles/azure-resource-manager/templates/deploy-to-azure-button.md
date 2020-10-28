---
title: Tlačítko pro nasazení do Azure
description: Pomocí tlačítka nasaďte Azure Resource Manager šablony z úložiště GitHub.
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: 62a0a8b0336d9a7fcf00efb172775b9606bcef98
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92675402"
---
# <a name="use-a-deployment-button-to-deploy-templates-from-github-repository"></a>Použití tlačítka nasazení k nasazení šablon z úložiště GitHub

Tento článek popisuje, jak pomocí tlačítka **nasadit do Azure** nasazovat šablony z úložiště GitHub. Tlačítko můžete přidat přímo do souboru README.md v úložišti GitHub. Nebo můžete přidat tlačítko na webovou stránku, která odkazuje na úložiště.

Rozsah nasazení je určen schématem šablony. Další informace naleznete v tématech:

* [skupiny prostředků](deploy-to-resource-group.md)
* [odběru](deploy-to-subscription.md)
* [skupiny pro správu](deploy-to-management-group.md)
* [klienti](deploy-to-tenant.md).

## <a name="use-common-image"></a>Použít běžný obrázek

Chcete-li přidat tlačítko na webovou stránku nebo úložiště, použijte následující obrázek:

```html
<img src="https://aka.ms/deploytoazurebutton"/>
```

Obrázek se zobrazí jako:

![Tlačítko pro nasazení do Azure](https://aka.ms/deploytoazurebutton)

## <a name="create-url-for-deploying-template"></a>Vytvořit adresu URL pro nasazení šablony

Pokud chcete vytvořit adresu URL pro vaši šablonu, začněte s nezpracovanými adresami URL šablony v úložišti. Pokud chcete zobrazit neupravenou adresu URL, vyberte **Nezpracovaná** .

:::image type="content" source="./media/deploy-to-azure-button/select-raw.png" alt-text="vybrat nezpracované":::

Formát adresy URL je:

```html
https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

Pak převeďte adresu URL na hodnotu kódovanou pomocí adresy URL. Můžete použít online kodér nebo spustit příkaz. Následující příklad PowerShellu ukazuje, jak adresa URL kóduje hodnotu.

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

Nakonec vložte odkaz a obrázek dohromady.

Pokud chcete přidat tlačítko s Markdownu do souboru README.md v úložišti GitHubu nebo na webové stránce, použijte:

```markdown
[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json)
```

V případě HTML použijte:

```html
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json" target="_blank">
  <img src="https://aka.ms/deploytoazurebutton"/>
</a>
```

## <a name="deploy-the-template"></a>Nasazení šablony

Chcete-li otestovat úplné řešení, vyberte následující tlačítko:

[![Nasazení do Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json)

Portál zobrazí podokno, které umožňuje snadno zadat hodnoty parametrů. Parametry jsou předem vyplněny výchozími hodnotami ze šablony.

![Nasazení pomocí portálu](./media/deploy-to-azure-button/portal.png)

## <a name="next-steps"></a>Další kroky

- Další informace o šablonách naleznete v tématu [pochopení struktury a syntaxe šablon Azure Resource Manager](template-syntax.md).
