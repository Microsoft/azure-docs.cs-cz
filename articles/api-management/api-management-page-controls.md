---
title: Ovládací prvky stránky Azure API Management | Microsoft Docs
description: Přečtěte si o ovládacích prvcích stránky, které jsou dostupné pro použití v šablonách portálu pro vývojáře v Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: ff608cac9786ebc0d3908ce4d88fb501e621be6c
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78374370"
---
# <a name="azure-api-management-page-controls"></a>Ovládací prvky stránky Azure API Management
Azure API Management poskytuje následující ovládací prvky pro použití v šablonách portálu pro vývojáře.  
  
Chcete-li použít ovládací prvek, umístěte ho do požadovaného umístění v šabloně portálu pro vývojáře. Některé ovládací prvky, například ovládací prvek [Akce aplikace](#app-actions) , mají parametry, jak je znázorněno v následujícím příkladu:  
  
```xml  
<app-actions params="{ appId: '{{app.id}}' }"></app-actions>  
```  
  
Hodnoty pro parametry jsou předány v rámci datového modelu pro šablonu. Ve většině případů můžete jednoduše vložit do uvedeného příkladu pro každý ovládací prvek, aby správně fungoval. Další informace o hodnotách parametrů naleznete v části datový model pro každou šablonu, v níž lze ovládací prvek použít.  

Další informace o práci se šablonami najdete v tématu [Postup přizpůsobení API Management portálu pro vývojáře pomocí šablon](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/).  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
## <a name="developer-portal-template-page-controls"></a>Ovládací prvky stránky šablony portálu pro vývojáře  
  
-   [aplikace – akce](#app-actions)  
-   [základní – přihlášení](#basic-signin)  
-   [ovládací prvek stránkování](#paging-control)  
-   [dodavateli](#providers)  
-   [hledání – ovládací prvek](#search-control)  
-   [registrace](#sign-up)  
-   [přihlášení k odběru – tlačítko](#subscribe-button)  
-   [předplatné – zrušit](#subscription-cancel)  
  
##  <a name="app-actions"></a>aplikace – akce  
 Ovládací prvek `app-actions` poskytuje uživatelské rozhraní pro interakci s aplikacemi na stránce profil uživatele na portálu pro vývojáře.  
  
 ![řízení&#45;akcí aplikace](./media/api-management-page-controls/APIM-app-actions-control.png "Řízení akcí aplikace APIM")  
  
### <a name="usage"></a>Využití  
  
```xml  
<app-actions params="{ appId: '{{app.id}}' }"></app-actions>  
```  
  
### <a name="parameters"></a>Parametry  
  
|Parametr|Popis|  
|---------------|-----------------|  
|appId|ID aplikace|  
  
### <a name="developer-portal-templates"></a>Šablony portálu pro vývojáře  
 `app-actions` ovládací prvek lze použít na následujících šablonách portálu pro vývojáře:  
  
-   [Aplikace](api-management-user-profile-templates.md#Applications)  
  
##  <a name="basic-signin"></a>základní – přihlášení  
 Ovládací prvek `basic-signin` poskytuje ovládací prvek pro shromažďování přihlašovacích údajů uživatelů na přihlašovací stránce na portálu pro vývojáře.  
  
 ![základní&#45;ovládací prvek přihlášení](./media/api-management-page-controls/APIM-basic-signin-control.png "APIM Basic – řízení přihlášení")  
  
### <a name="usage"></a>Využití  
  
```xml  
<basic-SignIn></basic-SignIn>  
```  
  
### <a name="parameters"></a>Parametry  
 Žádné.  
  
### <a name="developer-portal-templates"></a>Šablony portálu pro vývojáře  
 `basic-signin` ovládací prvek lze použít na následujících šablonách portálu pro vývojáře:  
  
-   [Přihlásit se](api-management-page-templates.md#SignIn)  
  
##  <a name="paging-control"></a>ovládací prvek stránkování  
 `paging-control` poskytuje funkce stránkování na stránkách portálu pro vývojáře, které zobrazují seznam položek.  
  
 ![ovládací prvek stránkování](./media/api-management-page-controls/APIM-paging-control.png "Ovládací prvek stránkování APIM")  
  
### <a name="usage"></a>Využití  
  
```xml  
<paging-control></paging-control>  
```  
  
### <a name="parameters"></a>Parametry  
 Žádné.  
  
### <a name="developer-portal-templates"></a>Šablony portálu pro vývojáře  
 `paging-control` ovládací prvek lze použít na následujících šablonách portálu pro vývojáře:  
  
-   [Seznam rozhraní API](api-management-api-templates.md#APIList)  
  
-   [Seznam problémů](api-management-issue-templates.md#IssueList)  
  
-   [Seznam produktů](api-management-product-templates.md#ProductList)  
  
##  <a name="providers"></a>dodavateli  
 Ovládací prvek `providers` poskytuje ovládací prvek pro výběr zprostředkovatelů ověřování na přihlašovací stránce na portálu pro vývojáře.  
  
 ![ovládací prvek Providers](./media/api-management-page-controls/APIM-providers-control.png "APIM – ovládací prvek poskytovatelů")  
  
### <a name="usage"></a>Využití  
  
```xml  
<providers></providers>  
```  
  
### <a name="parameters"></a>Parametry  
 Žádné.  
  
### <a name="developer-portal-templates"></a>Šablony portálu pro vývojáře  
 `providers` ovládací prvek lze použít na následujících šablonách portálu pro vývojáře:  
  
-   [Přihlásit se](api-management-page-templates.md#SignIn)  
  
##  <a name="search-control"></a>hledání – ovládací prvek  
 `search-control` poskytuje funkce hledání na stránkách portálu pro vývojáře, na kterých se zobrazuje seznam položek.  
  
 ![ovládací prvek hledání](./media/api-management-page-controls/APIM-search-control.png "APIM – ovládací prvek hledání")  
  
### <a name="usage"></a>Využití  
  
```xml  
<search-control></search-control>  
```  
  
### <a name="parameters"></a>Parametry  
 Žádné.  
  
### <a name="developer-portal-templates"></a>Šablony portálu pro vývojáře  
 `search-control` ovládací prvek lze použít na následujících šablonách portálu pro vývojáře:  
  
-   [Seznam rozhraní API](api-management-api-templates.md#APIList)  
  
-   [Seznam produktů](api-management-product-templates.md#ProductList)  
  
##  <a name="sign-up"></a>registrace  
 Ovládací prvek `sign-up` poskytuje kontrolu na shromažďování informací o profilu uživatele na stránce pro registraci na portálu pro vývojáře.  
  
 ![řízení&#45;registrace](./media/api-management-page-controls/APIM-sign-up-control.png "Řízení registrace APIM")  
  
### <a name="usage"></a>Využití  
  
```xml  
<sign-up></sign-up>  
```  
  
### <a name="parameters"></a>Parametry  
 Žádné.  
  
### <a name="developer-portal-templates"></a>Šablony portálu pro vývojáře  
 `sign-up` ovládací prvek lze použít na následujících šablonách portálu pro vývojáře:  
  
-   [Zaregistrovat se](api-management-page-templates.md#SignUp)  
  
##  <a name="subscribe-button"></a>přihlášení k odběru – tlačítko  
 `subscribe-button` poskytuje ovládací prvek pro přihlášení uživatele k produktu.  
  
 ![ovládací&#45;prvek tlačítko pro přihlášení k odběru](./media/api-management-page-controls/APIM-subscribe-button-control.png "Ovládací prvek tlačítko pro přihlášení k odběru APIM")  
  
### <a name="usage"></a>Využití  
  
```xml  
<subscribe-button></subscribe-button>  
```  
  
### <a name="parameters"></a>Parametry  
 Žádné.  
  
### <a name="developer-portal-templates"></a>Šablony portálu pro vývojáře  
 `subscribe-button` ovládací prvek lze použít na následujících šablonách portálu pro vývojáře:  
  
-   [Produktu](api-management-product-templates.md#Product)  
  
##  <a name="subscription-cancel"></a>předplatné – zrušit  
 Ovládací prvek `subscription-cancel` poskytuje ovládací prvek pro zrušení předplatného na stránce profilu uživatele na portálu pro vývojáře.  
  
 ![zrušit&#45;kontrolu předplatného](./media/api-management-page-controls/APIM-subscription-cancel-control.png "Předplatné APIM – zrušení ovládacího prvku")  
  
### <a name="usage"></a>Využití  
  
```xml  
<subscription-cancel params="{ subscriptionId: '{{subscription.id}}', cancelUrl: '{{subscription.cancelUrl}}' }">  
</subscription-cancel>  
  
```  
  
### <a name="parameters"></a>Parametry  
  
|Parametr|Popis|  
|---------------|-----------------|  
|subscriptionId|ID předplatného, které se má zrušit|  
|cancelUrl|Předplatné zruší adresu URL.|  
  
### <a name="developer-portal-templates"></a>Šablony portálu pro vývojáře  
 `subscription-cancel` ovládací prvek lze použít na následujících šablonách portálu pro vývojáře:  
  
-   [Produktu](api-management-product-templates.md#Product)

## <a name="next-steps"></a>Další kroky
Další informace o práci se šablonami najdete v tématu [Postup přizpůsobení API Management portálu pro vývojáře pomocí šablon](api-management-developer-portal-templates.md).
