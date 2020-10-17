---
title: Pravidla přístupu brány firewall
description: Nakonfigurujte pravidla pro přístup ke službě Azure Container Registry za bránou firewall tím, že povolíte přístup k REST APIům a názvům domén koncového bodu dat nebo rozsahům IP adres závislým na službě.
ms.topic: article
ms.date: 05/18/2020
ms.openlocfilehash: b9ecd5f802176cdc6881294f5dedefd3dd467244
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2020
ms.locfileid: "92148515"
---
# <a name="configure-rules-to-access-an-azure-container-registry-behind-a-firewall"></a>Konfigurace pravidel pro přístup ke službě Azure Container Registry za bránou firewall

Tento článek vysvětluje, jak nakonfigurovat pravidla pro bránu firewall, aby povolovala přístup ke službě Azure Container Registry. Například zařízení Azure IoT Edge za bránou firewall nebo proxy server může vyžadovat přístup k registru kontejneru, aby mohl načíst image kontejneru. Nebo je možné, že uzamčený Server v místní síti bude potřebovat přístup k nabízení obrázku.

Pokud místo toho chcete nakonfigurovat příchozí přístup k síti do registru kontejneru jenom v rámci služby Azure Virtual Network, přečtěte si téma [konfigurace privátního odkazu Azure pro službu Azure Container Registry](container-registry-private-link.md).

## <a name="about-registry-endpoints"></a>O koncových bodech registru

Aby mohl klient, jako je démon Docker, načítat nebo předávat obrázky nebo jiné artefakty do služby Azure Container Registry, musí komunikovat přes protokol HTTPS se dvěma odlišnými koncovými body. Pro klienty, kteří přistupují k registru z za bránou firewall, je potřeba nakonfigurovat pravidla přístupu pro oba koncové body. Oba koncové body jsou dosaženy přes port 443.

* **Registry REST API koncového bodu** – ověřování a správa registru se zpracovávají prostřednictvím koncového bodu veřejné REST API registru. Tento koncový bod je název přihlašovacího serveru registru. Příklad: `myregistry.azurecr.io`

* **Koncový bod úložiště (dat)** – Azure [přiděluje službě blob Storage](container-registry-storage.md) v Azure Storage účtů jménem každého registru za účelem správy dat pro Image kontejnerů a další artefakty. Když klient přistupuje k vrstvám imagí ve službě Azure Container Registry, provede požadavky pomocí koncového bodu účtu úložiště, který poskytuje registr.

Pokud je registr [geograficky replikovaný](container-registry-geo-replication.md), klient může potřebovat pracovat s koncovým bodem dat v určité oblasti nebo v několika replikovaných oblastech.

## <a name="allow-access-to-rest-and-data-endpoints"></a>Povolení přístupu k koncovým bodům REST a data

* **Koncový bod REST** – povolí přístup k plně kvalifikovanému názvu přihlašovacího serveru registru, `<registry-name>.azurecr.io` nebo k přidruženému rozsahu IP adres.
* **Koncový bod úložiště (data)** – povolí přístup ke všem účtům služby Azure Blob Storage pomocí zástupného znaku `*.blob.core.windows.net` nebo přidruženého rozsahu IP adres.
> [!NOTE]
> Azure Container Registry zavádí [vyhrazené koncové body dat](#enable-dedicated-data-endpoints), což vám umožní přesně určit pravidla brány firewall klienta pro úložiště registru. Volitelně můžete povolit koncové body dat ve všech oblastech, kde je registr umístěný nebo replikovaný, pomocí formuláře `<registry-name>.<region>.data.azurecr.io` .

## <a name="allow-access-by-ip-address-range"></a>Povolení přístupu podle rozsahu IP adres

Pokud má vaše organizace zásady povolující přístup jenom ke konkrétním IP adresám nebo rozsahům adres, Stáhněte si [rozsahy IP adres Azure a značky služeb – veřejný cloud](https://www.microsoft.com/download/details.aspx?id=56519).

Pokud chcete najít rozsahy IP adres koncového bodu REST ACR, pro které potřebujete přístup, vyhledejte **AzureContainerRegistry** v souboru JSON.

> [!IMPORTANT]
> Rozsahy IP adres pro služby Azure se můžou měnit a aktualizace se zveřejňují týdně. Stáhněte si soubor JSON pravidelně a proveďte potřebné aktualizace v pravidlech přístupu. Pokud váš scénář zahrnuje konfiguraci pravidel skupiny zabezpečení sítě ve službě Azure Virtual Network nebo používáte Azure Firewall, použijte místo toho **AzureContainerRegistry** [značku služby](#allow-access-by-service-tag) AzureContainerRegistry.
>

### <a name="rest-ip-addresses-for-all-regions"></a>IP adresy REST pro všechny oblasti

```json
{
  "name": "AzureContainerRegistry",
  "id": "AzureContainerRegistry",
  "properties": {
    "changeNumber": 10,
    "region": "",
    "platform": "Azure",
    "systemService": "AzureContainerRegistry",
    "addressPrefixes": [
      "13.66.140.72/29",
    [...]
```

### <a name="rest-ip-addresses-for-a-specific-region"></a>IP adresy REST pro konkrétní oblast

Vyhledejte konkrétní oblast, například **AzureContainerRegistry. AustraliaEast**.

```json
{
  "name": "AzureContainerRegistry.AustraliaEast",
  "id": "AzureContainerRegistry.AustraliaEast",
  "properties": {
    "changeNumber": 1,
    "region": "australiaeast",
    "platform": "Azure",
    "systemService": "AzureContainerRegistry",
    "addressPrefixes": [
      "13.70.72.136/29",
    [...]
```

### <a name="storage-ip-addresses-for-all-regions"></a>IP adresy úložiště pro všechny oblasti

```json
{
  "name": "Storage",
  "id": "Storage",
  "properties": {
    "changeNumber": 19,
    "region": "",
    "platform": "Azure",
    "systemService": "AzureStorage",
    "addressPrefixes": [
      "13.65.107.32/28",
    [...]
```

### <a name="storage-ip-addresses-for-specific-regions"></a>IP adresy úložiště pro konkrétní oblasti

Vyhledejte konkrétní oblast, například **Storage. AustraliaCentral**.

```json
{
  "name": "Storage.AustraliaCentral",
  "id": "Storage.AustraliaCentral",
  "properties": {
    "changeNumber": 1,
    "region": "australiacentral",
    "platform": "Azure",
    "systemService": "AzureStorage",
    "addressPrefixes": [
      "52.239.216.0/23"
    [...]
```

## <a name="allow-access-by-service-tag"></a>Povolení přístupu podle značky služby

Ve službě Azure Virtual Network použijte pravidla zabezpečení sítě k filtrování provozu z prostředku, jako je například virtuální počítač, do registru kontejneru. Pro zjednodušení vytváření pravidel sítě Azure použijte [značku služby](../virtual-network/network-security-groups-overview.md#service-tags) **AzureContainerRegistry** . Značka služby představuje skupinu předpon IP adres pro účely přístupu ke službě Azure globálně nebo podle oblasti Azure. Značka je automaticky aktualizována při změně adres. 

Můžete například vytvořit pravidlo skupiny zabezpečení odchozí sítě s cílovým **AzureContainerRegistry** , které umožní provoz do služby Azure Container Registry. Pokud chcete přístup ke značce služby udělit jenom v konkrétní oblasti, zadejte oblast v následujícím formátu: **AzureContainerRegistry**. [*název oblasti*].

## <a name="enable-dedicated-data-endpoints"></a>Povolit vyhrazené koncové body dat 

> [!WARNING]
> Pokud jste dříve nakonfigurovali přístup klienta brány firewall ke stávajícím `*.blob.core.windows.net` koncovým bodům, přechody na vyhrazené koncové body budou mít vliv na připojení klienta, což způsobí selhání operace Pull. Chcete-li zajistit, aby klienti měli konzistentní přístup, přidejte nová pravidla datových koncových bodů do pravidel brány firewall klienta. Po dokončení povolte pro vaše registry vyhrazené koncové body dat pomocí rozhraní příkazového řádku Azure CLI nebo jiných nástrojů.

Vyhrazená koncová data jsou volitelnou funkcí úrovně služby kontejneru **Premium** Registry. Informace o úrovních a omezeních služby registru najdete v tématu [Azure Container Registry úrovně služeb](container-registry-skus.md). 

Můžete povolit vyhrazené koncové body dat pomocí Azure Portal nebo Azure CLI. Koncové body dat následují podle regionálního vzoru, `<registry-name>.<region>.data.azurecr.io` . V geograficky replikovaném registru povoluje koncovým bodům dat koncové body ve všech oblastech repliky.

### <a name="portal"></a>Portál

Postup povolení koncových bodů dat pomocí portálu:

1. Přejděte do registru kontejneru.
1. Vyberte **síť**  >  **veřejný přístup**.
1. Zaškrtněte políčko **zapnout koncový bod vyhrazených dat** .
1. Vyberte **Uložit**.

Koncový bod dat nebo koncové body se zobrazí na portálu.

:::image type="content" source="media/container-registry-firewall-access-rules/dedicated-data-endpoints-portal.png" alt-text="Vyhrazené koncové body dat na portálu":::

### <a name="azure-cli"></a>Azure CLI

Pokud chcete povolit koncové body dat pomocí rozhraní příkazového řádku Azure CLI, použijte Azure CLI verze 2.4.0 nebo novější. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli).

Následující příkaz [AZ ACR Update][az-acr-update] povolí v registru *myregistry*vyhrazené koncové body dat. 

```azurecli
az acr update --name myregistry --data-endpoint-enabled
```

Chcete-li zobrazit koncové body dat, použijte příkaz [AZ ACR show-Endpoints][az-acr-show-endpoints] :

```azurecli
az acr show-endpoints --name myregistry
```

Výstup pro demonstrační účely ukazuje dva regionální koncové body.

```
{
    "loginServer": "myregistry.azurecr.io",
    "dataEndpoints": [
        {
            "region": "eastus",
            "endpoint": "myregistry.eastus.data.azurecr.io",
        },
        {
            "region": "westus",
            "endpoint": "myregistry.westus.data.azurecr.io",
        }
    ]
}
```

Po nastavení vyhrazených koncových bodů dat pro váš registr můžete povolit pravidla přístupu klienta brány firewall pro koncové body dat. Povolte pravidla přístupu koncového bodu dat pro všechny požadované oblasti registru.

## <a name="configure-client-firewall-rules-for-mcr"></a>Konfigurace pravidel brány firewall klienta pro MCR

Pokud potřebujete přístup k Microsoft Container Registry (MCR) z za bránou firewall, přečtěte si pokyny ke konfiguraci [pravidel brány firewall klienta MCR](https://github.com/microsoft/containerregistry/blob/master/client-firewall-rules.md). MCR je primární registr pro všechny image Docker publikované Microsoftem, jako jsou image Windows serveru.

## <a name="next-steps"></a>Další kroky

* Další informace o [osvědčených postupech Azure pro zabezpečení sítě](../security/fundamentals/network-best-practices.md)

* Další informace o [skupinách zabezpečení](../virtual-network/network-security-groups-overview.md) ve službě Azure Virtual Network

* Další informace o nastavení [privátního odkazu](container-registry-private-link.md) pro registr kontejneru

* Další informace o [vyhrazených datových koncových bodech](https://azure.microsoft.com/blog/azure-container-registry-mitigating-data-exfiltration-with-dedicated-data-endpoints/) pro Azure Container Registry



<!-- IMAGES -->

<!-- LINKS - External -->

<!-- LINKS - Internal -->

[az-acr-update]: /cli/azure/acr#az-acr-update
[az-acr-show-endpoints]: /cli/azure/acr#az-acr-show-endpoints