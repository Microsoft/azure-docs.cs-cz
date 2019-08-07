---
title: Pravidla brány firewall pro přístup k Azure Container Registry
description: Nakonfigurujte pravidla pro přístup ke službě Azure Container Registry za bránou firewall.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 07/17/2019
ms.author: danlep
ms.openlocfilehash: 88b6da4e9bd2938adadadc1ef0e696399fc3c75e
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68828004"
---
# <a name="configure-rules-to-access-an-azure-container-registry-behind-a-firewall"></a>Konfigurace pravidel pro přístup ke službě Azure Container Registry za bránou firewall

Tento článek vysvětluje, jak nakonfigurovat pravidla pro bránu firewall, aby povolovala přístup ke službě Azure Container Registry. Například zařízení Azure IoT Edge za bránou firewall nebo proxy server může vyžadovat přístup k registru kontejneru, aby mohl načíst image kontejneru. Nebo je možné, že uzamčený Server v místní síti bude potřebovat přístup k nabízení obrázku.

Pokud místo toho chcete nakonfigurovat příchozí pravidla přístupu k síti v registru kontejneru tak, aby povolovala přístup jenom v rámci služby Azure Virtual Network, nebo z rozsahu veřejných IP adres, přečtěte si téma [omezení přístupu ke službě Azure Container Registry z virtuální sítě](container-registry-vnet.md).

## <a name="about-registry-endpoints"></a>O koncových bodech registru

Aby mohl klient, jako je démon Docker, načítat nebo předávat obrázky nebo jiné artefakty do služby Azure Container Registry, musí komunikovat přes protokol HTTPS se dvěma odlišnými koncovými body.

* **Registry REST API koncového bodu** – ověřování a správa registru se zpracovávají prostřednictvím koncového bodu veřejné REST API registru. Tento koncový bod je adresa URL přihlašovacího serveru registru nebo přidružený rozsah IP adres. 

* **Koncový bod úložiště** – Azure [přiděluje úložiště objektů BLOB](container-registry-storage.md) v účtech Azure Storage jménem každého registru za účelem správy imagí kontejneru a dalších artefaktů. Když klient přistupuje k vrstvám imagí ve službě Azure Container Registry, provede požadavky pomocí koncového bodu účtu úložiště, který poskytuje registr.

Pokud je registr [geograficky replikovaný](container-registry-geo-replication.md), může klient spolupracovat s koncovými body Rest a úložiště v konkrétní oblasti nebo v několika replikovaných oblastech.

## <a name="allow-access-to-rest-and-storage-urls"></a>Povolení přístupu k adresám REST a Storage

* **Koncový bod REST** – povolí přístup k adrese URL serveru registru, například`myregistry.azurecr.io`
* **Koncový bod úložiště** – povolí přístup ke všem účtům služby Azure Blob Storage pomocí zástupného znaku.`*.blob.core.windows.net`


## <a name="allow-access-by-ip-address-range"></a>Povolení přístupu podle rozsahu IP adres

Pokud potřebujete přístup ke konkrétním IP adresám, Stáhněte si [rozsahy IP adres Azure a značky služeb – veřejný cloud](https://www.microsoft.com/download/details.aspx?id=56519).

Pokud chcete najít rozsahy IP adres koncového bodu REST ACR, vyhledejte **AzureContainerRegistry** v souboru JSON.

> [!IMPORTANT]
> Rozsahy IP adres pro služby Azure se můžou měnit a aktualizace se zveřejňují týdně. Stáhněte si soubor JSON pravidelně a proveďte potřebné aktualizace v pravidlech přístupu. Pokud váš scénář zahrnuje konfiguraci pravidel skupiny zabezpečení sítě ve službě Azure Virtual Network pro přístup k Azure Container Registry, použijte místo toho [značku služby](#allow-access-by-service-tag) **AzureContainerRegistry** .
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

Ve službě Azure Virtual Network použijte pravidla zabezpečení sítě k filtrování provozu z prostředku, jako je například virtuální počítač, do registru kontejneru. Pro zjednodušení vytváření pravidel sítě Azure použijte [značku služby](../virtual-network/security-overview.md#service-tags) **AzureContainerRegistry** . Značka služby představuje skupinu předpon IP adres pro účely přístupu ke službě Azure globálně nebo podle oblasti Azure. Značka je automaticky aktualizována při změně adres. 

Můžete například vytvořit pravidlo skupiny zabezpečení odchozí sítě s cílovým **AzureContainerRegistry** , které umožní provoz do služby Azure Container Registry. Chcete-li udělit přístup ke značce služby pouze v konkrétní oblasti, zadejte oblast v následujícím formátu: **AzureContainerRegistry**. [*název oblasti*].

## <a name="next-steps"></a>Další postup

* Další informace o [osvědčených postupech Azure pro zabezpečení sítě](../security/fundamentals/network-best-practices.md)

* Další informace o [skupinách zabezpečení](/azure/virtual-network/security-overview) ve službě Azure Virtual Network



<!-- IMAGES -->

<!-- LINKS - External -->

<!-- LINKS - Internal -->

