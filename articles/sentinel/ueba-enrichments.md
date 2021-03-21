---
title: Referenční informace o rozšířeních Azure Sentinel UEBA | Microsoft Docs
description: Tento článek zobrazuje rozšíření entit generované analýzou chování entit služby Azure Sentinel.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 01/04/2021
ms.author: yelevin
ms.openlocfilehash: daba8fc1f645b51dc8668c806be63744b6ae0842
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97901694"
---
# <a name="azure-sentinel-ueba-enrichments-reference"></a>Reference k rozšíření UEBA pro Azure Sentinel

Tyto tabulky obsahují seznam a popisují rozšíření entit, která se dají použít k zaostření a zostření šetření incidentů zabezpečení.

První dvě tabulky, **přehledy uživatelů** a **zařízení**, obsahují informace o entitách ze služby Active Directory/Azure AD a zdrojů informací o hrozbách Microsoftu.

<a name="baseline-explained"></a>Zbývající tabulky v **tabulce Activity Insights** obsahují informace o entitách na základě behaviorálních profilů sestavených pomocí analýzy chování entit v Azure Sentinel. Aktivity jsou analyzovány proti směrnému plánu, který je dynamicky kompilován při každém použití. Každá aktivita má svou definovanou lookback dobu, ze které je odvozen tento dynamický základní plán. Toto období je uvedené ve sloupci [**směrný plán**](#activity-insights-tables) v této tabulce.

> [!NOTE] 
> Pole **název obohacení** ve všech třech tabulkách zobrazuje dva řádky informací. První, **tučně**, je popisný název rozšíření. Druhý *(v kurzívách a závorkách)* je název pole rozšíření, jak je uloženo v [**tabulce analýzy chování**](identify-threats-with-entity-behavior-analytics.md#data-schema).

## <a name="user-insights-table"></a>Tabulka přehledů uživatelů

| Název rozšíření | Description | Ukázková hodnota |
| --- | --- | --- | --- |
| **Zobrazovaný název účtu**<br>*(AccountDisplayName)* | Zobrazovaný název účtu uživatele | Správce, Hayden Cook |
| **Doména účtu**<br>*(AccountDomain)* | Název domény účtu uživatele |  |
| **ID objektu účtu**<br>*(AccountObjectID)* | ID objektu účtu uživatele | a58df659-5cab-446c-9dd0-5a3af20ce1c2 |
| **Poloměr vysokého využití**<br>*(BlastRadius)* | Poloměr je vypočítán na základě několika faktorů: pozice uživatele ve stromové struktuře org a role a oprávnění uživatele Azure Active Directory. | Nízká, střední, vysoká |
| **Je neaktivní účet**<br>*(IsDormantAccount)* | Účet se v posledních 180 dnech nepoužil. | True, false |
| **Je místní správce**<br>*(IsLocalAdmin)* | Účet má oprávnění místního správce. | True, false |
| **Je nový účet**<br>*(IsNewAccount)* | Účet byl vytvořen během posledních 30 dnů. | True, false |
| **Místní SID**<br>*(OnPremisesSID)* | Místní SID uživatele související s akcí. | S-1-5-21-1112946627-1321165628-2437342228-1103 |
|

## <a name="device-insights-table"></a>Tabulka Insights pro zařízení

| Název rozšíření | Description | Ukázková hodnota |
| --- | --- | --- | --- |
| **Prohlížeč**<br>*Prohlížeee* | Prohlížeč použitý v akci | Edge, Chrome |
| **Řada zařízení**<br>*(DeviceFamily)* | Rodina zařízení použitá v akci. | Windows |
| **Typ zařízení**<br>*DeviceType* | Typ klientského zařízení, který se používá v akci | Desktop |
| **U**<br>*U* | Poskytovatel internetových služeb, který se používá v akci. |  |
| **Operační systém**<br>*OperatingSystem* | Operační systém použitý v akci. | Windows 10 |
| **Popis indikátoru technologie Intel Threat**<br>*(ThreatIntelIndicatorDescription)* | Popis zjištěného indikátoru hrozeb vyřešený z IP adresy použité v akci | Hostitel je členem botnetu: azorult. |
| **Typ indikátoru hrozby Intel**<br>*(ThreatIntelIndicatorType)* | Typ indikátoru hrozby, který se vyřešil z IP adresy použité v akci. | Botnetu, C2, CryptoMining, adres darknetu, DDoS, MaliciousUrl, malware, phishing, proxy, PUA, seznamu ke zhlédnutí |
| **Uživatelský agent**<br>*UserAgent* | Uživatelský agent použitý v akci | Klientská knihovna Microsoft Azure Graph 1,0,<br>Swagger – CodeGen/1.4.0.0/CSharp,<br>EvoSTS |
| **Rodina uživatelských agentů**<br>*(UserAgentFamily)* | Rodina uživatelských agentů použitá v akci. | Chrome, Edge, Firefox |
|

## <a name="activity-insights-tables"></a>Tabulky pro přehled aktivit

#### <a name="action-performed"></a>Provedena akce

| Název rozšíření | [Směrný plán](#baseline-explained) (dny) | Description | Ukázková hodnota |
| --- | --- | --- | --- |
| **Uživatel poprvé provede akci.**<br>*(FirstTimeUserPerformedAction)* | 180 | Akce byla provedena poprvé uživatelem. | True, false |
| **Neobvyklá akce uživatelem**<br>*(ActionUncommonlyPerformedByUser)* | 10 | Tuto akci obvykle neprovádí uživatel. | True, false |
| **Neobvyklá akce mezi partnerskými uzly**<br>*(ActionUncommonlyPerformedAmongPeers)* | 180 | Tato akce se běžně neprovádí mezi partnery uživatele. | True, false |
| **Při prvním provedení akce v tenantovi**<br>*(FirstTimeActionPerformedInTenant)* | 180 | Akce byla provedena poprvé pro každého v organizaci. | True, false |
| **Neobvyklá akce v tenantovi**<br>*(ActionUncommonlyPerformedInTenant)* | 180 | Tato akce se v organizaci běžně neprovádí. | True, false |
|

#### <a name="app-used"></a>Použitá aplikace

| Název rozšíření | [Směrný plán](#baseline-explained) (dny) | Description | Ukázková hodnota |
| --- | --- | --- | --- |
| **První aplikace, kterou uživatel použil při prvním použití**<br>*(FirstTimeUserUsedApp)* | 180 | Uživatel tuto aplikaci použil poprvé. | True, false |
| **Aplikace neběžně používaná uživatelem**<br>*(AppUncommonlyUsedByUser)* | 10 | Uživatel tuto aplikaci běžně nepoužívá. | True, false |
| **Aplikace neběžně používaná mezi partnerskými uzly**<br>*(AppUncommonlyUsedAmongPeers)* | 180 | Aplikace se běžně nepoužívá mezi partnery uživatele. | True, false |
| **První zjištěná aplikace v tenantovi**<br>*(FirstTimeAppObservedInTenant)* | 180 | Aplikace byla v organizaci zjištěna poprvé. | True, false |
| **Aplikace neběžně používaná v tenantovi**<br>*(AppUncommonlyUsedInTenant)* | 180 | Aplikace se v organizaci běžně nepoužívá. | True, false |
| 

#### <a name="browser-used"></a>Použitý prohlížeč

| Název rozšíření | [Směrný plán](#baseline-explained) (dny) | Description | Ukázková hodnota |
| --- | --- | --- | --- |
| **Při prvním připojení uživatele přes prohlížeč**<br>*(FirstTimeUserConnectedViaBrowser)* | 30 | V prohlížeči byl uživatel pozorován poprvé. | True, false |
| **Prohlížeč neběžně používaný uživatelem**<br>*(BrowserUncommonlyUsedByUser)* | 10 | Prohlížeč je běžně nepoužívá. | True, false |
| **Prohlížeč neběžně používaný mezi partnerskými uzly**<br>*(BrowserUncommonlyUsedAmongPeers)* | 30 | Prohlížeč se běžně nepoužívá mezi partnery uživatele. | True, false |
| **První čas prohlížeče v tenantovi**<br>*(FirstTimeBrowserObservedInTenant)* | 30 | Prohlížeč se pozoroval poprvé v organizaci. | True, false |
| **Prohlížeč se neběžně používá v tenantovi.**<br>*(BrowserUncommonlyUsedInTenant)* | 30 | Prohlížeč se v organizaci běžně nepoužívá. | True, false |
| 

#### <a name="country-connected-from"></a>Země připojená

| Název rozšíření | [Směrný plán](#baseline-explained) (dny) | Description | Ukázková hodnota |
| --- | --- | --- | --- |
| **První připojení uživatele z země**<br>*(FirstTimeUserConnectedFromCountry)* | 90 | Geografické umístění, jak bylo vyřešeno z IP adresy, bylo uživatelem připojeno poprvé. | True, false |
| **Země, která není běžně připojená uživatelem**<br>*(CountryUncommonlyConnectedFromByUser)* | 10 | Geografické umístění, jak je vyřešeno z IP adresy, není většinou připojeno uživatelem. | True, false |
| **Země, která není běžně připojená mezi partnery**<br>*(CountryUncommonlyConnectedFromAmongPeers)* | 90 | Geografické umístění, jak je vyřešeno z IP adresy, není běžně připojeno mezi partnery ze strany uživatele. | True, false |
| **Při prvním připojení ze země zjištěná v tenantovi**<br>*(FirstTimeConnectionFromCountryObservedInTenant)* | 90 | Tato země byla poprvé připojena kýmkoli v organizaci. | True, false |
| **Neběžně připojená země v tenantovi**<br>*(CountryUncommonlyConnectedFromInTenant)* | 90 | Geografické umístění, jak je vyřešeno z IP adresy, není běžně připojeno v organizaci. | True, false |
| 

#### <a name="device-used-to-connect"></a>Zařízení používané pro připojení

| Název rozšíření | [Směrný plán](#baseline-explained) (dny) | Description | Ukázková hodnota |
| --- | --- | --- | --- |
| **Při prvním připojení uživatele ze zařízení**<br>*(FirstTimeUserConnectedFromDevice)* | 30 | Zdrojové zařízení bylo uživatelem připojeno poprvé. | True, false |
| **Zařízení neběžně využívané uživatelem**<br>*(DeviceUncommonlyUsedByUser)* | 10 | Zařízení obvykle nepoužívá uživatel. | True, false |
| **Zařízení neběžně používané mezi partnerskými uzly**<br>*(DeviceUncommonlyUsedAmongPeers)* | 180 | Zařízení se běžně nepoužívá mezi partnery uživatele. | True, false |
| **První čas zjištěného zařízení v tenantovi**<br>*(FirstTimeDeviceObservedInTenant)* | 30 | Zařízení se zjistilo poprvé v organizaci. | True, false |
| **Zařízení se neběžně používá v tenantovi.**<br>*(DeviceUncommonlyUsedInTenant)* | 180 | Zařízení se v organizaci běžně nepoužívá. | True, false |
| 

#### <a name="other-device-related"></a>Další související zařízení

| Název rozšíření | [Směrný plán](#baseline-explained) (dny) | Description | Ukázková hodnota |
| --- | --- | --- | --- |
| **Uživatel poprvé přihlášený k zařízení**<br>*(FirstTimeUserLoggedOnToDevice)* | 180 | Cílové zařízení bylo uživatelem připojeno poprvé. | True, false |
| **Rodina zařízení se v tenantovi neběžně používá**<br>*(DeviceFamilyUncommonlyUsedInTenant)* | 30 | Rodina zařízení se v organizaci běžně nepoužívá. | True, false |
| 

#### <a name="internet-service-provider-used-to-connect"></a>Poskytovatel internetových služeb, který se používá pro připojení

| Název rozšíření | [Směrný plán](#baseline-explained) (dny) | Description | Ukázková hodnota |
| --- | --- | --- | --- |
| **První připojení uživatele přes poskytovatele internetových služeb**<br>*(FirstTimeUserConnectedViaISP)* | 30 | Poskytovatel internetových služeb byl během prvního zjištěn uživatelem. | True, false |
| **Poskytovatel internetových služeb, který neběžně používá uživatel**<br>*(ISPUncommonlyUsedByUser)* | 10 | Uživatel ho většinou nepoužívá. | True, false |
| **Poskytovatel internetových služeb, který se neběžně používá mezi partnery**<br>*(ISPUncommonlyUsedAmongPeers)* | 30 | Poskytovatel internetových služeb se běžně nepoužívá mezi partnery uživatele. | True, false |
| **Při prvním připojení přes poskytovatele internetových služeb v tenantovi**<br>*(FirstTimeConnectionViaISPInTenant)* | 30 | Poskytovatel internetových služeb byl v organizaci zjištěn poprvé. | True, false |
| **Poskytovatel internetových služeb se neběžně používá v tenantovi**<br>*(ISPUncommonlyUsedInTenant)* | 30 | Poskytovatel internetových služeb se v organizaci běžně nepoužívá. | True, false |
| 

#### <a name="resource-accessed"></a>Byl získán přístup k prostředku.

| Název rozšíření | [Směrný plán](#baseline-explained) (dny) | Description | Ukázková hodnota |
| --- | --- | --- | --- |
| **Prostředek pro první použití uživatele**<br>*(FirstTimeUserAccessedResource)* | 180 | Tento uživatel použil prostředek poprvé. | True, false |
| **Prostředek, ke kterému neběžně přistupoval uživatel**<br>*(ResourceUncommonlyAccessedByUser)* | 10 | K prostředku se běžně nepoužívá uživatel. | True, false |
| **Prostředek, ke kterému se neběžně přistupoval v rámci partnerských uzlů**<br>*(ResourceUncommonlyAccessedAmongPeers)* | 180 | Prostředek není běžně k dispozici mezi partnery uživatele. | True, false |
| **První otevření prostředku v tenantovi**<br>*(FirstTimeResourceAccessedInTenant)* | 180 | K prostředku byl poprvé přidaný každý v organizaci. | True, false |
| **Prostředek, ke kterému se neběžně přistupoval v tenantovi**<br>*(ResourceUncommonlyAccessedInTenant)* | 180 | K prostředku se běžně nepoužívá v organizaci. | True, false |
| 

#### <a name="miscellaneous"></a>Různé

| Název rozšíření | [Směrný plán](#baseline-explained) (dny) | Description | Ukázková hodnota |
| --- | --- | --- | --- |
| **Čas posledního provedení akce uživatelem**<br>*(LastTimeUserPerformedAction)* | 180 | Čas, kdy uživatel naposledy provedl stejnou akci. | <Timestamp> |
| **Podobná akce nebyla provedena v minulosti.**<br>*(SimilarActionWasn'tPerformedInThePast)* | 30 | Uživatel neprovedl žádnou akci ve stejném zprostředkovateli prostředků. | True, false |
| **Umístění zdrojové IP adresy**<br>*(SourceIPLocation)* | *NENÍ K DISPOZICI* | Země byla vyřešena ze zdrojové IP adresy akce. | [Surrey, Anglie] |
| **Nespolečný velký objem operací**<br>*(UncommonHighVolumeOfOperations)* | 7 | Uživatel provedl nárůst podobných operací v rámci stejného zprostředkovatele. | True, false |
| **Neobvyklý počet selhání podmíněného přístupu Azure AD**<br>*(UnusualNumberOfAADConditionalAccessFailures)* | 5 | Kvůli podmíněnému přístupu se nepovedlo ověřit neobvyklý počet uživatelů. | True, false |
| **Byl přidán neobvyklý počet zařízení.**<br>*(UnusualNumberOfDevicesAdded)* | 5 | Uživatel přidal neobvyklý počet zařízení. | True, false |
| **Neobvyklý počet odstraněných zařízení**<br>*(UnusualNumberOfDevicesDeleted)* | 5 | Uživatel odstranil neobvyklý počet zařízení. | True, false |
| **Neobvyklý počet uživatelů přidaných do skupiny**<br>*(UnusualNumberOfUsersAddedToGroup)* | 5 | Uživatel přidal do skupiny neobvyklý počet uživatelů. | True, false |
|