---
title: Pravidla přístupu k bráně firewall
description: Nakonfigurujte pravidla pro přístup k registru kontejnerů Azure zpoza brány firewall tím, že povolíte přístup k rozhraní REST API ("whitelisting") rozhraní REST API a názvům domén koncového bodu úložiště nebo rozsahům IP adres specifických pro službu.
ms.topic: article
ms.date: 02/11/2020
ms.openlocfilehash: 06fedea2adf5e73929f5752279f2bd7e7227e570
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77168017"
---
# <a name="configure-rules-to-access-an-azure-container-registry-behind-a-firewall"></a>Konfigurace pravidel pro přístup k registru kontejnerů Azure za bránou firewall

Tento článek vysvětluje, jak nakonfigurovat pravidla na bráně firewall povolit přístup k registru kontejnerů Azure. Například zařízení Azure IoT Edge za bránou firewall nebo proxy serverem může potřebovat přístup k registru kontejneru k vyhledání image kontejneru. Nebo uzamčený server v místní síti může potřebovat přístup k nabízení bitové kopie.

Pokud místo toho chcete nakonfigurovat pravidla přístupu k příchozí síti v registru kontejneru jenom v rámci virtuální sítě Azure nebo z rozsahu veřejných IP adres, přečtěte si témat [u tématu Omezení přístupu k registru kontejnerů Azure z virtuální sítě](container-registry-vnet.md).

## <a name="about-registry-endpoints"></a>O koncových bodech registru

Chcete-li vyžádat nebo push image nebo jiné artefakty do registru kontejneru Azure, klient, jako je například demon Docker u potřeb komunikovat přes HTTPS se dvěma odlišnými koncovými body.

* **Koncový bod rozhraní REST API registru** – operace ověřování a správy registru jsou zpracovávány prostřednictvím veřejného koncového bodu rozhraní REST API registru. Tento koncový bod je název přihlašovacího serveru registru nebo přidruženého rozsahu adres IP. 

* **Koncový bod úložiště** – Azure [přiděluje úložiště objektů blob](container-registry-storage.md) v účtech Azure Storage jménem každého registru ke správě dat pro ibi kontejnerů a další artefakty. Když klient přistupuje k vrstvám image v registru kontejneru Azure, provede požadavky pomocí koncového bodu účtu úložiště poskytovaného registrem.

Pokud je váš registr [geograficky replikován](container-registry-geo-replication.md), může být nutné, aby klient spolupracoval s koncovými body REST a úložiště v určité oblasti nebo ve více replikovaných oblastech.

## <a name="allow-access-to-rest-and-storage-domain-names"></a>Povolit přístup k názvům domén REST a úložiště

* **Koncový bod REST** – povolit přístup k plně kvalifikovanému názvu přihlašovacího serveru registru, například`myregistry.azurecr.io`
* **Koncový bod úložiště (dat)** – povolit přístup ke všem účtům úložiště objektů blob Azure pomocí zástupné ho svižné stránky`*.blob.core.windows.net`


## <a name="allow-access-by-ip-address-range"></a>Povolit přístup podle rozsahu IP adres

Pokud má vaše organizace zásady umožňující přístup pouze k určitým IP adresám nebo rozsahům adres, stáhněte si [rozsahy IP adres Azure a značky služeb – Veřejný cloud](https://www.microsoft.com/download/details.aspx?id=56519).

Chcete-li najít rozsahy IP adres koncových bodů ACR REST, pro které potřebujete povolit přístup, vyhledejte **AzureContainerRegistry** v souboru JSON.

> [!IMPORTANT]
> Rozsahy IP adres pro služby Azure se můžou měnit a aktualizace se publikují každý týden. Pravidelně stahujte soubor JSON a provázte potřebné aktualizace v pravidlech přístupu. Pokud váš scénář zahrnuje konfiguraci pravidel skupiny zabezpečení sítě ve virtuální síti Azure pro přístup k registru kontejnerů Azure, použijte místo toho [značku služby](#allow-access-by-service-tag) **AzureContainerRegistry.**
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

### <a name="rest-ip-addresses-for-a-specific-region"></a>IP adresy REST pro určitou oblast

Vyhledejte konkrétní oblast, například **AzureContainerRegistry.AustraliaEast**.

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

### <a name="storage-ip-addresses-for-specific-regions"></a>IP adresy úložiště pro určité oblasti

Vyhledejte konkrétní oblast, například **Storage.AustraliaCentral**.

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

## <a name="allow-access-by-service-tag"></a>Povolit přístup podle výrobního označení

Ve virtuální síti Azure použijte pravidla zabezpečení sítě k filtrování provozu z prostředku, jako je virtuální počítač, do registru kontejnerů. Chcete-li zjednodušit vytváření síťových pravidel Azure, použijte [značku služby](../virtual-network/security-overview.md#service-tags) **AzureContainerRegistry** . Značka služby představuje skupinu předpon IP adres pro přístup ke službě Azure globálně nebo v oblasti Azure. Značka se automaticky aktualizuje při změně adres. 

Můžete například vytvořit pravidlo skupiny zabezpečení odchozí sítě s cílovým **AzureContainerRegistry,** které umožní přenos do registru kontejnerů Azure. Chcete-li povolit přístup k výrobním určovatelům pouze v určité oblasti, zadejte oblast v následujícím formátu: **AzureContainerRegistry**. [*název regionu*].

## <a name="configure-client-firewall-rules-for-mcr"></a>Konfigurace pravidel brány firewall klienta pro mcr

Pokud potřebujete přístup k registru Microsoft Container Registry (MCR) zpoza brány firewall, přečtěte si pokyny ke konfiguraci [pravidel brány firewall klienta MCR](https://github.com/microsoft/containerregistry/blob/master/client-firewall-rules.md). MCR je primární registr pro všechny bitové kopie dockeru publikované společností Microsoft, například s bitovými kopiemi systému Windows Server.

## <a name="next-steps"></a>Další kroky

* Informace o [doporučených postupech Azure pro zabezpečení sítě](../security/fundamentals/network-best-practices.md)

* Další informace o [skupinách zabezpečení](/azure/virtual-network/security-overview) ve virtuální síti Azure



<!-- IMAGES -->

<!-- LINKS - External -->

<!-- LINKS - Internal -->

