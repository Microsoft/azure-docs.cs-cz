---
title: Rychlý Start – vytvoření instance služby Azure API Management pomocí rozhraní příkazového řádku
description: Pomocí Azure CLI vytvořte novou instanci služby Azure API Management.
author: dlepow
ms.service: api-management
ms.topic: quickstart
ms.custom: ''
ms.date: 09/10/2020
ms.author: apimpm
ms.openlocfilehash: 19fc2e1629e7f67063e3cc3eec8cb3707b6dd2e4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775848"
---
# <a name="quickstart-create-a-new-azure-api-management-service-instance-by-using-the-azure-cli"></a>Rychlý Start: vytvoření nové instance služby Azure API Management pomocí rozhraní příkazového řádku Azure

Azure API Management (APIM) pomáhá organizacím při publikování rozhraní API pro externí, partnerské a interní vývojáře, aby tak uvolnila potenciál jejich dat a služeb. Služba API Management nabízí základní možnosti pro zajištění úspěšného programu s rozhraním API prostřednictvím zapojení vývojářů, informací o podniku, analýz, zabezpečení a ochrany. APIM umožňuje vytvářet a spravovat moderní brány rozhraní API pro stávající služby back-end hostované kdekoli. Další informace najdete v tématu [Přehled](api-management-key-concepts.md).

Tento rychlý Start popisuje kroky pro vytvoření nové instance API Management pomocí příkazů [AZ APIM](/cli/azure/apim) v rozhraní příkazového řádku Azure CLI.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Tento článek vyžaduje verzi rozhraní příkazového řádku Azure 2.11.1 nebo novější. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Instance Azure API Management, jako jsou všechny prostředky Azure, musí být nasazené do skupiny prostředků. Skupiny prostředků vám umožňují organizaci a správu souvisejících prostředků Azure.

Nejprve vytvořte skupinu prostředků s názvem *myResourceGroup* v umístění střed USA pomocí následujícího příkazu [AZ Group Create](/cli/azure/group#az_group_create) :

```azurecli-interactive
az group create --name myResourceGroup --location centralus
```

## <a name="create-a-new-service"></a>Vytvoření nové služby

Teď, když máte skupinu prostředků, můžete vytvořit instanci služby API Management. Vytvořte ho pomocí příkazu [AZ APIM Create](/cli/azure/apim#az_apim_create) a sdělte mu podrobnosti o názvu služby a vydavateli. Název služby musí být v rámci Azure jedinečný. 

V následujícím příkladu se pro název služby používá *myapim* . Aktualizujte název na jedinečnou hodnotu. Aktualizujte také název organizace vydavatele rozhraní API a e-mailovou adresu pro příjem oznámení. 

```azurecli-interactive
az apim create --name myapim --resource-group myResourceGroup \
  --publisher-name Contoso --publisher-email admin@contoso.com \
  --no-wait
```

Ve výchozím nastavení příkaz vytvoří instanci ve vrstvě vývojář, což je ekonomická možnost pro vyhodnocení API Management Azure. Tato vrstva není pro produkční použití. Další informace o škálování úrovní služby API Management najdete v tématu věnovaném [upgradu a škálování](upgrade-and-scale.md). 

> [!TIP]
> Vytvoření a aktivace služby API Management v této vrstvě může trvat 30 až 40 minut. Předchozí příkaz použije `--no-wait` možnost, takže se příkaz vrátí hned při vytvoření služby.

Stav nasazení ověřte spuštěním příkazu [AZ APIM show](/cli/azure/apim#az_apim_show) :

```azurecli-interactive
az apim show --name myapim --resource-group myResourceGroup --output table
```

Ve výchozím stavu je výstup podobný následujícímu, který zobrazuje `Activating` stav:

```console
NAME         RESOURCE GROUP    LOCATION    GATEWAY ADDR    PUBLIC IP    PRIVATE IP    STATUS      TIER       UNITS
-----------  ----------------  ----------  --------------  -----------  ------------  ----------  ---------  -------
myapim       myResourceGroup   Central US                                             Activating  Developer  1
```

Po aktivaci je stav `Online` a instance služby má adresu brány a veřejnou IP adresu. V současné době tyto adresy nevystavují žádný obsah. Například:

```console
NAME         RESOURCE GROUP    LOCATION    GATEWAY ADDR                       PUBLIC IP     PRIVATE IP    STATUS    TIER       UNITS
-----------  ----------------  ----------  ---------------------------------  ------------  ------------  --------  ---------  -------
myapim       myResourceGroup   Central US  https://myapim.azure-api.net       203.0.113.1                 Online    Developer  1
```

Když je vaše instance API Management služby online, jste připraveni ji použít. Začněte s kurzem [importu a publikování vašeho prvního rozhraní API](import-and-publish.md).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků a instance služby API Management použít příkaz [AZ Group Delete](/cli/azure/group#az_group_delete) .

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Import a publikování vašeho prvního rozhraní API](import-and-publish.md)
