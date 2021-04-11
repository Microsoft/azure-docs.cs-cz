---
title: Vytvoření profilu a koncového bodu Azure Content Delivery Network (CDN) pomocí rozhraní příkazového řádku Azure
description: Ukázkové skripty Azure CLI pro vytvoření profilu Azure CDN, koncového bodu, skupiny původu, původu a vlastní domény.
author: asudbring
ms.author: allensu
manager: danielgi
ms.date: 03/09/2021
ms.topic: sample
ms.service: azure-cdn
ms.devlang: azurecli
ms.custom: devx-track-azurecli
ms.openlocfilehash: ce01c1f851a5dbaedc85d848b12bf0b0831a16ef
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104726817"
---
# <a name="create-an-azure-cdn-profile-and-endpoint-using-the-azure-cli"></a>Vytvoření profilu Azure CDN a koncového bodu pomocí Azure CLI

Jako alternativu k Azure Portal můžete použít tyto ukázkové skripty Azure CLI ke správě následujících operací CDN:

- Vytvořte profil CDN.
- Vytvořit koncový bod CDN.
- Vytvořte skupinu zdrojů CDN a nastavte ji jako výchozí skupinu.
- Vytvořte zdroj CDN.
- Vytvořte vlastní doménu a povolte HTTPS.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="sample-scripts"></a>Ukázkové skripty

Pokud ještě nemáte skupinu prostředků pro váš profil CDN, vytvořte ji pomocí příkazu `az group create` :

```azurecli
# Create a resource group to use for the CDN.
az group create --name MyResourceGroup --location eastus

```

Následující skript Azure CLI vytvoří profil CDN a koncový bod CDN:

```azurecli
# Create a CDN profile.
az cdn profile create --resource-group MyResourceGroup --name MyCDNProfile --sku Standard_Microsoft

# Create a CDN endpoint.
az cdn endpoint create --resource-group MyResourceGroup --name MyCDNEndpoint --profile-name MyCDNProfile --origin www.contoso.com

```

Následující skript Azure CLI vytvoří skupinu zdrojů CDN, nastaví výchozí skupinu původu pro koncový bod a vytvoří nový zdroj:

```azurecli
# Create an origin group.
az cdn origin-group create --resource-group MyResourceGroup --endpoint-name MyCDNEndpoint --profile-name MyCDNProfile --name MyOriginGroup --origins origin-0

# Make the origin group the default group of an endpoint.
az cdn endpoint update --resource-group MyResourceGroup --name MyCDNEndpoint --profile-name MyCDNProfile --default-origin-group MyOriginGroup
                           
# Create another origin for an endpoint.
az cdn origin create --resource-group MyResourceGroup --endpoint-name MyCDNEndpoint --profile-name MyCDNProfile --name origin-1 --host-name example.contoso.com

```

Následující skript Azure CLI vytvoří vlastní doménu CDN a povolí protokol HTTPS. Než budete moct přidružit vlastní doménu k Azure CDN koncovému bodu, musíte nejdřív vytvořit záznam kanonického názvu (CNAME) s Azure DNS nebo vaším poskytovatelem DNS, aby odkazoval na koncový bod CDN. Další informace najdete v tématu [Vytvoření záznamu DNS CNAME](../../../cdn/cdn-map-content-to-custom-domain.md#create-a-cname-dns-record).

```azurecli
# Associate a custom domain with an endpoint.
az cdn custom-domain create --resource-group MyResourceGroup --endpoint-name MyCDNEndpoint --profile-name MyCDNProfile --name MyCustomDomain --hostname www.example.com

# Enable HTTPS on the custom domain.
az cdn custom-domain enable-https --resource-group MyResourceGroup --endpoint-name MyCDNEndpoint --profile-name MyCDNProfile --name MyCustomDomain

```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení používání ukázkových skriptů pomocí následujícího příkazu odeberte skupinu prostředků a všechny k ní přidružené prostředky.

```azurecli
# Delete the resource group.
az group delete --name MyResourceGroup

```

## <a name="azure-cli-commands-used-in-this-article"></a>Příkazy rozhraní příkazového řádku Azure, které se používají v tomto článku

- [AZ CDN Endpoint Create](/cli/azure/cdn/endpoint#az_cdn_endpoint_create)
- [AZ CDN Endpoint Update](/cli/azure/cdn/endpoint#az_cdn_endpoint_update)
- [AZ CDN Origin Create](/cli/azure/cdn/origin#az_cdn_origin_create)
- [AZ CDN Origin-Group Create](/cli/azure/cdn/origin-group#az_cdn_origin_group_create)
- [AZ CDN Profile Create](/cli/azure/cdn/profile#az_cdn_profile_create)
- [az group create](/cli/azure/group#az_group_create)
- [az group delete](/cli/azure/group#az_group_delete)
- [AZ CDN Custom-Domain Create](/cli/azure/cdn/custom-domain#az_cdn_custom_domain_create)
- [AZ CDN Custom-Domain Enable-https](/cli/azure/cdn/custom-domain#az_cdn_custom_domain_enable_https)
