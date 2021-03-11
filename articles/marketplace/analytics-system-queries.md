---
title: Seznam systémových dotazů
description: Přečtěte si o systémových dotazech, pomocí kterých můžete programově získat analytické údaje o vašich nabídkách na komerčním webu Microsoft Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: f2b5f7eb559e349947f88067a3d2ea53d99b7cbf
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102583746"
---
# <a name="list-of-system-queries"></a>Seznam systémových dotazů

V [rozhraní API pro vytvoření sestavy](analytics-programmatic-access.md#create-report-api) můžete použít následující systémové dotazy přímo s `QueryId` . Systémové dotazy jsou jako sestavy exportu v partnerském centru po dobu šesti měsíců (6 min) výpočtu.

Další informace o názvech sloupců, atributech a popisech najdete v těchto článcích:

- [Řídicí panel zákazníci na komerčním webu Marketplace Analytics](customer-dashboard.md#customer-details-table)
- [Řídicí panel Objednávky v analýzách komerčního marketplace](orders-dashboard.md#orders-details-table)
- [Řídicí panel využití v komerčních obchodech Marketplace](usage-dashboard.md#usage-details-table)
- [Řídicí panel Přehledy Marketplace v analýzách komerčního marketplace](insights-dashboard.md#marketplace-insights-details-table)

V následujících oddílech jsou uvedeny dotazy sestav pro různé sestavy.

## <a name="customers-report-query"></a>Dotaz na sestavu pro zákazníky

**Popis sestavy**: zákazníci sestavy za poslední 6 min

**QueryId**:  `b9df4929-073f-4795-b0cb-a2c81b11e28d`

**Dotaz sestavy**:

`SELECT MarketplaceSubscriptionId,DateAcquired,DateLost,ProviderName,ProviderEmail,FirstName,LastName,Email,CustomerCompanyName,CustomerCity,CustomerPostalCode,CustomerCommunicationCulture,CustomerCountryRegion,AzureLicenseType,PromotionalCustomers,CustomerState,CommerceRootCustomer,CustomerId,BillingAccountId,ID  FROM ISVCustomer TIMESPAN LAST_6_MONTHS`

## <a name="orders-report-query"></a>Dotaz na sestavu objednávky

**Popis sestavy**: objednávky pro poslední 6 min

**QueryId**:  `fd0f299c-5a1c-4929-9f48-bfc6cc44355d`

**Dotaz sestavy**:

`SELECT MarketplaceSubscriptionId,MonthStartDate,OfferType,AzureLicenseType,MarketplaceLicenseType,Sku,CustomerCountry,IsPreviewSKU,OrderId,OrderQuantity,CloudInstanceName,IsNewCustomer,OrderStatus,OrderCancelDate,CustomerCompanyName,CustomerName,OrderPurchaseDate,OfferName,TrialEndDate,CustomerId,BillingAccountId FROM ISVOrder TIMESPAN LAST_6_MONTHS`

## <a name="usage-report-queries"></a>Dotazy k sestavám využití

**Popis sestavy**: sestava normalizovaného využití virtuálního počítače pro poslední 6 min

**QueryId**:  `2c6f384b-ad52-4aed-965f-32bfa09b3778`

**Dotaz sestavy**:

`SELECT MarketplaceSubscriptionId,MonthStartDate,OfferType,AzureLicenseType,MarketplaceLicenseType,SKU,CustomerCountry,IsPreviewSKU,SKUBillingType,IsInternal,VMSize,CloudInstanceName,ServicePlanName,OfferName,DeploymentMethod,CustomerName,CustomerCompanyName,UsageDate,IsNewCustomer,CoreSize,TrialEndDate,CustomerCurrencyCC,PriceCC,PayoutCurrencyPC,EstimatedPricePC,UsageReference,UsageUnit,CustomerId,BillingAccountId,NormalizedUsage,EstimatedExtendedChargeCC,EstimatedExtendedChargePC FROM ISVUsage WHERE OfferType IN ('vm core image', 'Virtual Machine Licenses', 'multisolution') TIMESPAN LAST_6_MONTHS`

**Popis sestavy**: sestava nezpracovaného využití virtuálního počítače pro poslední 6 min

**QueryId**:  `3f19fb95-5bc4-4ee0-872e-cedd22578512`

**Dotaz sestavy**:

`SELECT MarketplaceSubscriptionId,MonthStartDate,OfferType,AzureLicenseType,MarketplaceLicenseType,SKU,CustomerCountry,IsPreviewSKU,SKUBillingType,IsInternal,VMSize,CloudInstanceName,ServicePlanName,OfferName,DeploymentMethod,CustomerName,CustomerCompanyName,UsageDate,IsNewCustomer,CoreSize,TrialEndDate,CustomerCurrencyCC,PriceCC,PayoutCurrencyPC,EstimatedPricePC,UsageReference,UsageUnit,CustomerId,BillingAccountId,RawUsage,EstimatedExtendedChargeCC,EstimatedExtendedChargePC FROM ISVUsage WHERE OfferType IN ('vm core image', 'Virtual Machine Licenses', 'multisolution') TIMESPAN LAST_6_MONTHS`

**Popis sestavy**: sestava měření využití pro poslední 6 min

**QueryId**:  `f0c4927f-1f23-4c99-be4a-1371a5a9a086`

**Dotaz sestavy**:

`SELECT MarketplaceSubscriptionId,MonthStartDate,OfferType,AzureLicenseType,MarketplaceLicenseType,SKU,CustomerCountry,IsPreviewSKU,SKUBillingType,IsInternal,VMSize,CloudInstanceName,ServicePlanName,OfferName,DeploymentMethod,CustomerName,CustomerCompanyName,UsageDate,IsNewCustomer,CoreSize,TrialEndDate,CustomerCurrencyCC,PriceCC,PayoutCurrencyPC,EstimatedPricePC,UsageReference,UsageUnit,CustomerId,BillingAccountId,MeteredUsage,EstimatedExtendedChargeCC,EstimatedExtendedChargePC FROM ISVUsage WHERE OfferType IN ('SaaS', 'Azure Applications') TIMESPAN LAST_6_MONTHS`

## <a name="marketplace-insights-report-query"></a>Dotaz na sestavu v Marketplace Insights

**Popis sestavy**: sestava Marketplace Insights pro poslední 6 min

**QueryId**:  `6fd7624b-aa9f-42df-a61d-67d42fd00e92`

**Dotaz sestavy**:

`Date,OfferName,ReferralDomain,CountryName,PageVisits,GetItNow,ContactMe,TestDrive,FreeTrial FROM ISVMarketplaceInsights TIMESPAN LAST_6_MONTHS`

## <a name="next-steps"></a>Další kroky

- [Rozhraní API pro přístup k datům analýzy komerčního webu Marketplace](analytics-available-apis.md)
- [Ukázková aplikace pro přístup k datům analýzy komerčního tržiště](analytics-sample-application.md)
