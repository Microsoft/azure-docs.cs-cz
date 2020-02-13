---
title: Pravidla přístupu brány firewall
description: Nakonfigurujte pravidla pro přístup ke službě Azure Container Registry za bránou firewall tím, že povolíte přístup k ("povolenému") REST API a názvům koncových bodů úložiště nebo rozsahům IP adres pro konkrétní služby.
ms.topic: article
ms.date: 02/11/2020
ms.openlocfilehash: 06fedea2adf5e73929f5752279f2bd7e7227e570
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2020
ms.locfileid: "77168017"
---
# <a name="configure-rules-to-access-an-azure-container-registry-behind-a-firewall"></a>Konfigurace pravidel pro přístup ke službě Azure Container Registry za bránou firewall

Tento článek vysvětluje, jak nakonfigurovat pravidla pro bránu firewall, aby povolovala přístup ke službě Azure Container Registry. Například zařízení Azure IoT Edge za bránou firewall nebo proxy server může vyžadovat přístup k registru kontejneru, aby mohl načíst image kontejneru. Nebo je možné, že uzamčený Server v místní síti bude potřebovat přístup k nabízení obrázku.

Pokud místo toho chcete nakonfigurovat příchozí pravidla přístupu k síti v registru kontejneru jenom v rámci virtuální sítě Azure nebo z rozsahu veřejných IP adres, přečtěte si téma [omezení přístupu ke službě Azure Container Registry z virtuální sítě](container-registry-vnet.md).

## <a name="about-registry-endpoints"></a>O koncových bodech registru

Aby mohl klient, jako je démon Docker, načítat nebo předávat obrázky nebo jiné artefakty do služby Azure Container Registry, musí komunikovat přes protokol HTTPS se dvěma odlišnými koncovými body.

* **Registry REST API koncového bodu** – ověřování a správa registru se zpracovávají prostřednictvím koncového bodu veřejné REST API registru. Tento koncový bod je název přihlašovacího serveru registru nebo přidružený rozsah IP adres. 

* **Koncový bod úložiště** – Azure [přiděluje úložiště objektů blob](container-registry-storage.md) v rámci Azure Storage účtů jménem každého registru za účelem správy dat pro Image kontejnerů a další artefakty. Když klient přistupuje k vrstvám imagí ve službě Azure Container Registry, provede požadavky pomocí koncového bodu účtu úložiště, který poskytuje registr.

Pokud je registr [geograficky replikovaný](container-registry-geo-replication.md), může klient spolupracovat s koncovými body Rest a úložiště v konkrétní oblasti nebo v několika replikovaných oblastech.

## <a name="allow-access-to-rest-and-storage-domain-names"></a>Povolení přístupu k názvům domén REST a úložiště

* **Koncový bod REST** – povolí přístup k plně kvalifikovanému názvu přihlašovacího serveru registru, například `myregistry.azurecr.io`
* **Koncový bod úložiště (data)** – povolí přístup ke všem účtům služby Azure Blob Storage pomocí zástupného znaku `*.blob.core.windows.net`


## <a name="allow-access-by-ip-address-range"></a>Povolení přístupu podle rozsahu IP adres

Pokud má vaše organizace zásady povolující přístup jenom ke konkrétním IP adresám nebo rozsahům adres, Stáhněte si [rozsahy IP adres Azure a značky služeb – veřejný cloud](https://www.microsoft.com/download/details.aspx?id=56519).

Pokud chcete najít rozsahy IP adres koncového bodu REST ACR, pro které potřebujete přístup, vyhledejte **AzureContainerRegistry** v souboru JSON.

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

Můžete například vytvořit pravidlo skupiny zabezpečení odchozí sítě s cílovým **AzureContainerRegistry** , které umožní provoz do služby Azure Container Registry. Pokud chcete přístup ke značce služby udělit jenom v konkrétní oblasti, zadejte oblast v následujícím formátu: **AzureContainerRegistry**. [*název oblasti*].

## <a name="configure-client-firewall-rules-for-mcr"></a>Konfigurace pravidel brány firewall klienta pro MCR

Pokud potřebujete přístup k Microsoft Container Registry (MCR) z za bránou firewall, přečtěte si pokyny ke konfiguraci [pravidel brány firewall klienta MCR](https://github.com/microsoft/containerregistry/blob/master/client-firewall-rules.md). MCR je primární registr pro všechny image Docker publikované Microsoftem, jako jsou image Windows serveru.

## <a name="next-steps"></a>Další kroky

* Další informace o [osvědčených postupech Azure pro zabezpečení sítě](../security/fundamentals/network-best-practices.md)

* Další informace o [skupinách zabezpečení](/azure/virtual-network/security-overview) ve službě Azure Virtual Network



<!-- IMAGES -->

<!-- LINKS - External -->

<!-- LINKS - Internal -->

