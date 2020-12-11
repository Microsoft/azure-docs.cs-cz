---
title: Azure API Management Soft – odstranění (Preview) | Microsoft Docs
description: Obnovitelné odstranění umožňuje obnovit odstraněné instance API Management.
ms.service: api-management
ms.topic: conceptual
author: vladvino
ms.author: apimpm
ms.date: 11/27/2020
ms.openlocfilehash: 72e91715398b4920c62afae5f36aa09954a577f9
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2020
ms.locfileid: "97092138"
---
# <a name="api-management-soft-delete-preview"></a>API Management Soft-Delete (Preview)

Pomocí API Managementho obnovitelného odstranění (Preview) můžete obnovit a obnovit nedávno odstraněné instance API Management (APIM).

> [!IMPORTANT]
> Jenom API Management instance odstraněné pomocí `2020-01-01-preview` a novější verze rozhraní API se odeberou a obnoví pomocí kroků popsaných v tomto článku. Instance APIM odstraněné pomocí předchozích verzí rozhraní API budou pořád pevně odstraněné. Azure PowerShell a rozhraní příkazového řádku Azure aktuálně nepoužívají `2020-06-01-preview` verzi a výsledkem bude také chování pevného odstranění.

## <a name="supporting-interfaces"></a>Podpůrná rozhraní

Funkce obnovitelného odstranění je k dispozici prostřednictvím [REST API](/rest/api/apimanagement/2020-06-01-preview/apimanagementservice/restore).

> [!TIP]
> Tipy a nástroje pro volání rozhraní Azure REST API najdete v [referenčních informacích k azure REST API](/rest/api/azure/) .

| Operace | Popis | Obor názvů API Management | Minimální verze rozhraní API |
|--|--|--|--|
| [Vytvořit nebo aktualizovat](/rest/api/apimanagement/2020-06-01-preview/apimanagementservice/createorupdate) | Vytvoří nebo aktualizuje službu API Management.  | Služba API Management | Libovolný |
| [Vytvořit nebo aktualizovat](/rest/api/apimanagement/2020-06-01-preview/apimanagementservice/createorupdate) s `restore` vlastností nastavenou na **hodnotu true** | Odstraní API Management službu, pokud byla dříve odstraněna. Pokud `restore` je zadána a nastavena na `true` všechny ostatní vlastnosti, budou ignorovány.  | Služba API Management |  2020-06-01 – Preview |
| [Odstranit](/rest/api/apimanagement/2020-06-01-preview/apimanagementservice/delete) | Odstraní existující službu API Management. | Služba API Management | 2020-01-01 – Preview|
| [Získat podle názvu](/rest/api/apimanagement/2020-06-01-preview/deletedservices/getbyname) | Získejte dočasná odstraněnou službu API Management podle názvu. | Odstraněné služby | 2020-06-01 – Preview |
| [Seznam podle předplatného](/rest/api/apimanagement/2020-06-01-preview/deletedservices/listbysubscription) | Vypíše všechny dočasně odstraněné služby, které jsou k dispozici pro odstranění daného předplatného. | Odstraněné služby | 2020-06-01 – Preview
| [Odstraněna](/rest/api/apimanagement/2020-06-01-preview/deletedservices/purge) | Vyprázdní API Management službu (odstraní ji bez možnosti zrušení odstranění). | Odstraněné služby | 2020-06-01 – Preview

## <a name="soft-delete-behavior"></a>Chování obnovitelného odstranění

Můžete použít libovolnou verzi rozhraní API k vytvoření instance API Management, ale budete muset použít `2020-01-01-preview` nebo novější verze, abyste mohli opravit instanci APIM (a máte možnost ji obnovit).

Po odstranění instance API Management bude služba existovat v odstraněném stavu, takže nebude přístupná pro žádné operace APIM. V tomto stavu může být instance APIM jenom uvedená, obnovená nebo vyčištěná (trvale Odstraněná).

V současné době Azure naplánuje odstranění podkladových dat, která odpovídají instanci APIM, pro spuštění po předplánovaném intervalu uchování (48 hodin). Záznam DNS odpovídající instanci se zachovává i po dobu trvání intervalu uchování. Nemůžete znovu použít název instance API Management, která byla přístupným smazána, dokud neuplyne doba uchování.

Pokud se vaše instance APIM v rámci 48 hodin neobnoví, bude odstraněna (neobnovitelné). Můžete se také rozhodnout, že chcete [Vymazat](#purge-a-soft-deleted-apim-instance) (trvale odstranit) instanci APIM a neodstranit dobu uchovávání obnovitelného odstranění.

## <a name="list-deleted-apim-instances"></a>Vypsat odstranění instancí APIM

Můžete ověřit, jestli je k dispozici APIM instance s přístupným odstraněnou pro obnovení (obnovení), a to pomocí odstraněných služeb [Get podle názvu](/rest/api/apimanagement/2020-06-01-preview/deletedservices/getbyname) nebo [seznamu podle jejich předplatného](/rest/api/apimanagement/2020-06-01-preview/deletedservices/listbysubscription) .

### <a name="get-a-soft-deleted-instance-by-name"></a>Získat instanci s odstraněnou přístupným názvem

Použijte operaci API Management [získat podle názvu](/rest/api/apimanagement/2020-06-01-preview/deletedservices/getbyname) , nahrazování `{subscriptionId}` , `{location}` a `{serviceName}` s vaším předplatným Azure, umístěním prostředku a názvem instance API Management:

```rest
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.ApiManagement/locations/{location}/deletedservices/{serviceName}?api-version=2020-06-01-preview
```

Pokud je k dispozici pro obnovení, Azure vrátí záznam instance APIM, na které se `deletionDate` zobrazuje `scheduledPurgeDate` , a například:

```json
{
    "id": "subscriptions/########-####-####-####-############/providers/Microsoft.ApiManagement/locations/southcentralus/deletedservices/apimtest",
    "name": "apimtest",
    "type": "Microsoft.ApiManagement/deletedservices",
    "location": "South Central US",
    "properties": {
        "serviceId": "/subscriptions/########-####-####-####-############/resourceGroups/apimtestgroup/providers/Microsoft.ApiManagement/service/apimtest",
        "scheduledPurgeDate": "2020-11-26T19:40:26.3596893Z",
        "deletionDate": "2020-11-24T19:40:50.1013572Z"
    }
}
```

### <a name="list-all-soft-deleted-instances-for-a-given-subscription"></a>Vypsat všechny obnovitelné odstraněné instance pro dané předplatné

Použijte seznam API Management [podle předplatného](/rest/api/apimanagement/2020-06-01-preview/deletedservices/listbysubscription) a nahraďte `{subscriptionId}` ID vašeho předplatného:

```rest
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.ApiManagement/deletedservices?api-version=2020-06-01-preview
```

Tato akce vrátí seznam všech místně odstraněných služeb, které jsou k dispozici pro obnovení v rámci daného předplatného, které zobrazí `deletionDate` a `scheduledPurgeDate` pro každý z nich.

## <a name="recover-a-deleted-apim-instance"></a>Obnovení odstraněné instance APIM

Použijte API Management operaci [vytvořit nebo aktualizovat](/rest/api/apimanagement/2020-06-01-preview/apimanagementservice/createorupdate) , nahradit `{subscriptionId}` , `{resourceGroup}` a `{apimServiceName}` pomocí předplatného Azure, názvu skupiny prostředků a názvu API Management:

```rest
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.ApiManagement/service/{apimServiceName}?api-version=2020-06-01-preview
```

. . . a nastavte `restore` vlastnost na `true` text v požadavku. (Pokud je tento příznak zadaný a nastaven na *hodnotu true*, všechny ostatní vlastnosti se budou ignorovat.) Například:

```json
{
  "properties": {
    "publisherEmail": "help@contoso.com",
    "publisherName": "Contoso",
    "restore": true
  },
  "sku": {
    "name": "Developer",
    "capacity": 1
  },
  "location": "South Central US"
}
```

## <a name="purge-a-soft-deleted-apim-instance"></a>Vyprázdnit instanci APIM s přístupným odstraněním

Použijte operaci [Vyprázdnit](/rest/api/apimanagement/2020-06-01-preview/deletedservices/purge) API Management, nahraďte `{subscriptionId}` , `{location}` a `{serviceName}` s vaším předplatným Azure, umístěním prostředku a názvem API Management:

```rest
DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.ApiManagement/locations/{location}/deletedservices/{serviceName}?api-version=2020-06-01-preview
```

Tím se trvale odstraní vaše API Management instance z Azure.

## <a name="next-steps"></a>Další kroky

Další informace o dlouhodobém API Management možností zálohování a obnovení:

- [Implementace zotavení po havárii pomocí zálohování a obnovení služby ve službě Azure API Management](api-management-howto-disaster-recovery-backup-restore.md)