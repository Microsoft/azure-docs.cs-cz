---
title: Povolit privátní přístup pomocí privátního odkazu (Preview) – CLI
titleSuffix: Azure Digital Twins
description: Podívejte se, jak povolit privátní přístup pro řešení digitálních vláken Azure pomocí privátního propojení pomocí Azure CLI.
author: baanders
ms.author: baanders
ms.date: 02/09/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 5bd7ffda508980a9a56d86037887fc53a0fed640
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102202939"
---
# <a name="enable-private-access-with-private-link-preview-azure-cli"></a>Povolit privátní přístup pomocí privátního odkazu (Preview): Azure CLI

[!INCLUDE [digital-twins-private-link-selector.md](../../includes/digital-twins-private-link-selector.md)]

Tento článek popisuje různé způsoby, jak [Povolit privátní propojení s privátním koncovým bodem pro instanci digitálních vláken Azure](concepts-security.md#private-network-access-with-azure-private-link-preview) (v současnosti ve verzi Preview). Konfigurace privátního koncového bodu pro instanci digitálních vláken Azure vám umožní zabezpečit vaši instanci digitálních vláken Azure a eliminovat veřejnou expozici a zabránit tomu, aby exfiltrace data z [Azure Virtual Network (VNET)](../virtual-network/virtual-networks-overview.md).

Tento článek vás provede procesem pomocí [**Azure CLI**](/cli/azure/what-is-azure-cli).

Tady je postup, který je popsaný v tomto článku: 
1. Zapnout privátní odkaz a nakonfigurovat privátní koncový bod pro instanci digitálních vláken Azure
1. Zakáže nebo povolí příznaky přístupu k veřejné síti, aby se omezil přístup k rozhraním API jenom na připojení přes privátní propojení.

## <a name="prerequisites"></a>Požadavky

Než budete moct nastavit privátní koncový bod, budete potřebovat službu [**Azure Virtual Network (VNET)**](../virtual-network/virtual-networks-overview.md) , ve které se dá koncový bod nasadit. Pokud ještě nemáte virtuální síť, můžete ji nastavit pomocí některého z [rychlých startů](../virtual-network/quick-create-portal.md) pro Azure Virtual Network.

## <a name="manage-private-endpoints-for-an-azure-digital-twins-instance"></a>Správa privátních koncových bodů instance digitálních vláken Azure 

Při použití rozhraní příkazového [řádku Azure](/cli/azure/what-is-azure-cli)můžete nastavit privátní koncové body s privátním odkazem na instanci digitálních vláken Azure, která už existuje (nedá se přidat jako součást vytváření instancí). Pak je můžete dál zobrazovat a spravovat prostřednictvím dalších příkazů rozhraní příkazového řádku. 

>[!TIP]
> Můžete také nastavit koncový bod privátního propojení prostřednictvím služby privátního propojení namísto instance digitálního vlákna Azure. To také nabízí stejné možnosti konfigurace a stejný konečný výsledek.
>
> Další podrobnosti o nastavení prostředků privátního propojení najdete v tématu dokumentace k privátním odkazům pro [Azure Portal](../private-link/create-private-endpoint-portal.md), [Azure CLI](../private-link/create-private-endpoint-cli.md), [šablony ARM](../private-link/create-private-endpoint-template.md)nebo [PowerShell](../private-link/create-private-endpoint-powershell.md).

### <a name="add-a-private-endpoint-to-an-existing-instance"></a>Přidání privátního koncového bodu do existující instance

Pokud chcete vytvořit privátní koncový bod a propojit ho s instancí digitálních vláken Azure, použijte příkaz [**AZ Network Private-Endpoint Create**](/cli/azure/network/private-endpoint#az_network_private_endpoint_create) . Identifikujte instanci digitálních vláken Azure pomocí jejího plně kvalifikovaného ID v `--private-connection-resource-id` parametru.

Tady je příklad, který používá příkaz k vytvoření privátního koncového bodu s pouze požadovanými parametry.

```azurecli-interactive
az network private-endpoint create --connection-name {private_link_service_connection} -n {name_for_private_endpoint} -g {resource_group} --subnet {subnet_ID} --private-connection-resource-id "/subscriptions/{subscription_ID}/resourceGroups/{resource_group}/providers/Microsoft.DigitalTwins/digitalTwinsInstances/{Azure_Digital_Twins_instance_name}" 
```

Úplný seznam povinných a volitelných parametrů a další příklady vytváření privátních koncových bodů najdete v tématu [ **AZ Network Private-Endpoint Create** reference Document](/cli/azure/network/private-endpoint#az_network_private_endpoint_create).

### <a name="manage-private-endpoint-connections-on-the-instance"></a>Správa připojení privátního koncového bodu na instanci

Po vytvoření privátního koncového bodu pro instanci digitálních vláken Azure můžete použít příkazy [**AZ DT Network Private-Endpoint Connection**](/cli/azure/ext/azure-iot/dt/network/private-endpoint/connection) , abyste mohli dál spravovat **připojení** privátních koncových bodů s ohledem na instanci. Operace zahrnují:
* Zobrazit připojení privátního koncového bodu
* Nastavení stavu připojení privátního koncového bodu
* Odstranit připojení privátního koncového bodu
* Zobrazit seznam všech připojení privátního koncového bodu pro instanci

Další informace a příklady najdete v [dokumentaci **AZ DT Network Private-Endpoint** reference](/cli/azure/ext/azure-iot/dt/network/private-endpoint).

### <a name="manage-other-private-link-information-on-an-azure-digital-twins-instance"></a>Správa dalších informací o privátních odkazech v instanci digitálních vláken Azure

Další informace o stavu privátního propojení vaší instance můžete získat pomocí příkazů [**AZ DT Network Private-Link**](/cli/azure/ext/azure-iot/dt/network/private-link) . Operace zahrnují:
* Výpis privátních odkazů přidružených k instanci digitálních vláken Azure
* Zobrazit privátní odkaz přidružený k instanci

Další informace a příklady najdete v [referenční dokumentaci **AZ DT Network Private-Link**](/cli/azure/ext/azure-iot/dt/network/private-link).

## <a name="disable--enable-public-network-access-flags"></a>Zakázat/povolit příznaky přístupu k veřejné síti

Instanci digitálního vlákna Azure můžete nakonfigurovat tak, aby odepřela všechna veřejná připojení a povolovala jenom připojení prostřednictvím privátních koncových bodů, aby se zvýšilo zabezpečení sítě. Tato akce se provádí s **příznakem přístupu k veřejné síti**. 

Tato zásada umožňuje omezit přístup k rozhraní API jenom na připojení pomocí privátního propojení. Pokud je příznak přístupu k veřejné síti nastavený na *zakázáno*, vrátí se všechna REST API volání do roviny dat instance digitálních vláken Azure z veřejného cloudu `403, Unauthorized` . Případně, pokud je zásada nastavená na *zakázáno* a požadavek se provede prostřednictvím privátního koncového bodu, volání rozhraní API se nezdaří.

V tomto článku se dozvíte, jak aktualizovat hodnotu příznaku sítě pomocí rozhraní příkazového [řádku Azure CLI](/cli/azure/) nebo [nástroje ARMClient](https://github.com/projectkudu/ARMClient). Pokyny k tomu, jak to provést s Azure Portal, najdete v části [verze](how-to-enable-private-link-portal.md) tohoto článku na portálu.

### <a name="use-the-azure-cli"></a>Použití Azure CLI

V rozhraní příkazového řádku Azure můžete zakázat nebo povolit přístup k veřejné síti přidáním `--public-network-access` parametru do `az dt create` příkazu. I když tento příkaz lze také použít k vytvoření nové instance, můžete ji použít k úpravě vlastností existující instance zadáním názvu instance, která již existuje. (Další informace o tomto příkazu najdete v [referenční dokumentaci](/cli/azure/ext/azure-iot/dt#ext_azure_iot_az_dt_create) nebo v [obecných pokynech k nastavení instance digitálního vlákna Azure](how-to-set-up-instance-cli.md#create-the-azure-digital-twins-instance).)

Pokud chcete **Zakázat** přístup k veřejné síti pro instanci digitálních vláken Azure, použijte `--public-network-access` jako příklad tento parametr:

```azurecli-interactive
az dt create -n {name_of_existing_instance} -g {resource_group} --public-network-access Disabled
```

Pokud chcete **Povolit** přístup k veřejné síti u instance, kde je aktuálně zakázaná, použijte následující podobný příkaz:

```azurecli-interactive
az dt create -n {name_of_existing_instance} -g {resource_group} --public-network-access Enabled
```

### <a name="usethe-armclientcommand-tool"></a>Použití nástroje příkazového řádku ARMClient 

Pomocí [příkazového nástroje ARMClient](https://github.com/projectkudu/ARMClient)je přístup k veřejné síti povolený nebo zakázaný pomocí níže uvedených příkazů. 

**Zakázání** přístupu k veřejné síti:
  
```cmd/sh
armclient login 

armclient PATCH /subscriptions/<your-Azure-subscription-ID>/resourceGroups/<your-resource-group>/providers/Microsoft.DigitalTwins/digitalTwinsInstances/<your-Azure-Digital-Twins-instance>?api-version=2020-12-01 "{ 'properties': { 'publicNetworkAccess': 'disabled' } }"  
```

**Povolení** přístupu k veřejné síti:  
  
```cmd/sh
armclient login 

armclient PATCH /subscriptions/<your-Azure-subscription-ID>/resourceGroups/<your-resource-group>/providers/Microsoft.DigitalTwins/digitalTwinsInstances/<your-Azure-Digital-Twins-instance>?api-version=2020-12-01 "{ 'properties': { 'publicNetworkAccess': 'enabled' } }"  
``` 

## <a name="next-steps"></a>Další kroky

Další informace o privátním propojení pro Azure: 
* [*Co je služba privátního propojení Azure?*](../private-link/private-link-service-overview.md)