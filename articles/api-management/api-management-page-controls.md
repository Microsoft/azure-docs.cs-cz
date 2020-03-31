---
title: Ovládací prvky stránky Azure API Management | Dokumenty společnosti Microsoft
description: Přečtěte si o ovládacích prvcích stránky, které jsou dostupné pro použití v šablonách portálu pro vývojáře ve správě rozhraní Azure API.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244014"
---
# <a name="azure-api-management-page-controls"></a>Ovládací prvky stránky Azure API Management
Azure API Management poskytuje následující ovládací prvky pro použití v šablonách portálu pro vývojáře.  
  
Chcete-li použít ovládací prvek, umístěte jej do požadovaného umístění v šabloně portálu pro vývojáře. Některé ovládací prvky, například ovládací prvek [akce aplikace,](#app-actions) mají parametry, jak je znázorněno v následujícím příkladu:  
  
```xml  
<app-actions params="{ appId: '{{app.id}}' }"></app-actions>  
```  
  
Hodnoty parametrů jsou předány jako součást datového modelu pro šablonu. Ve většině případů můžete jednoduše vložit do zadaný příklad pro každý ovládací prvek pro něj pracovat správně. Další informace o hodnotách parametrů naleznete v části datového modelu pro každou šablonu, ve které lze použít ovládací prvek.  

Další informace o práci se šablonami najdete [v tématu Jak přizpůsobit portál pro vývojáře pro správu rozhraní API pomocí šablon](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/).  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
## <a name="developer-portal-template-page-controls"></a>Ovládací prvky stránky šablony portálu pro vývojáře  
  
-   [akce aplikace](#app-actions)  
-   [základní signin](#basic-signin)  
-   [stránkovací řízení](#paging-control)  
-   [Poskytovatelů](#providers)  
-   [řízení vyhledávání](#search-control)  
-   [Sign-up](#sign-up)  
-   [tlačítko pro přihlášení k odběru](#subscribe-button)  
-   [zrušení předplatného](#subscription-cancel)  
  
##  <a name="app-actions"></a><a name="app-actions"></a>akce aplikace  
 Ovládací `app-actions` prvek poskytuje uživatelské rozhraní pro interakci s aplikacemi na stránce profilu uživatele na portálu pro vývojáře.  
  
 ![ovládání akce&#45;aplikace](./media/api-management-page-controls/APIM-app-actions-control.png "Ovládací prvek akce aplikace APIM")  
  
### <a name="usage"></a>Využití  
  
```xml  
<app-actions params="{ appId: '{{app.id}}' }"></app-actions>  
```  
  
### <a name="parameters"></a>Parametry  
  
|Parametr|Popis|  
|---------------|-----------------|  
|appId|ID aplikace.|  
  
### <a name="developer-portal-templates"></a>Šablony portálu pro vývojáře  
 Ovládací `app-actions` prvek lze použít v následujících šablonách portálu pro vývojáře:  
  
-   [Aplikace](api-management-user-profile-templates.md#Applications)  
  
##  <a name="basic-signin"></a><a name="basic-signin"></a>základní signin  
 Ovládací `basic-signin` prvek poskytuje ovládací prvek pro shromažďování informací o přihlášení uživatele na přihlašovací stránce na portálu pro vývojáře.  
  
 ![základní&#45;signin control](./media/api-management-page-controls/APIM-basic-signin-control.png "Ovládací prvek základního přihlášení APIM")  
  
### <a name="usage"></a>Využití  
  
```xml  
<basic-SignIn></basic-SignIn>  
```  
  
### <a name="parameters"></a>Parametry  
 Žádné.  
  
### <a name="developer-portal-templates"></a>Šablony portálu pro vývojáře  
 Ovládací `basic-signin` prvek lze použít v následujících šablonách portálu pro vývojáře:  
  
-   [Přihlásit](api-management-page-templates.md#SignIn)  
  
##  <a name="paging-control"></a><a name="paging-control"></a>stránkovací řízení  
 Poskytuje `paging-control` funkce stránkování na stránkách portálu pro vývojáře, které zobrazují seznam položek.  
  
 ![ovládací prvek stránkování](./media/api-management-page-controls/APIM-paging-control.png "Ovládací prvek stránkování APIM")  
  
### <a name="usage"></a>Využití  
  
```xml  
<paging-control></paging-control>  
```  
  
### <a name="parameters"></a>Parametry  
 Žádné.  
  
### <a name="developer-portal-templates"></a>Šablony portálu pro vývojáře  
 Ovládací `paging-control` prvek lze použít v následujících šablonách portálu pro vývojáře:  
  
-   [Seznam rozhraní API](api-management-api-templates.md#APIList)  
  
-   [Seznam problémů](api-management-issue-templates.md#IssueList)  
  
-   [Seznam produktů](api-management-product-templates.md#ProductList)  
  
##  <a name="providers"></a><a name="providers"></a>Poskytovatelů  
 Ovládací `providers` prvek poskytuje ovládací prvek pro výběr zprostředkovatelů ověřování na přihlašovací stránce na portálu pro vývojáře.  
  
 ![řízení poskytovatelů](./media/api-management-page-controls/APIM-providers-control.png "Řízení zprostředkovatelů APIM")  
  
### <a name="usage"></a>Využití  
  
```xml  
<providers></providers>  
```  
  
### <a name="parameters"></a>Parametry  
 Žádné.  
  
### <a name="developer-portal-templates"></a>Šablony portálu pro vývojáře  
 Ovládací `providers` prvek lze použít v následujících šablonách portálu pro vývojáře:  
  
-   [Přihlásit](api-management-page-templates.md#SignIn)  
  
##  <a name="search-control"></a><a name="search-control"></a>řízení vyhledávání  
 Poskytuje `search-control` funkce vyhledávání na stránkách portálu pro vývojáře, které zobrazují seznam položek.  
  
 ![ovládací prvek hledání](./media/api-management-page-controls/APIM-search-control.png "Ovládací prvek vyhledávání APIM")  
  
### <a name="usage"></a>Využití  
  
```xml  
<search-control></search-control>  
```  
  
### <a name="parameters"></a>Parametry  
 Žádné.  
  
### <a name="developer-portal-templates"></a>Šablony portálu pro vývojáře  
 Ovládací `search-control` prvek lze použít v následujících šablonách portálu pro vývojáře:  
  
-   [Seznam rozhraní API](api-management-api-templates.md#APIList)  
  
-   [Seznam produktů](api-management-product-templates.md#ProductList)  
  
##  <a name="sign-up"></a><a name="sign-up"></a>Sign-up  
 Ovládací `sign-up` prvek poskytuje ovládací prvek pro shromažďování informací o profilu uživatele na stránce registrace na portálu pro vývojáře.  
  
 ![ovládání&#45;](./media/api-management-page-controls/APIM-sign-up-control.png "Ovládací prvek přihlášení APIM")  
  
### <a name="usage"></a>Využití  
  
```xml  
<sign-up></sign-up>  
```  
  
### <a name="parameters"></a>Parametry  
 Žádné.  
  
### <a name="developer-portal-templates"></a>Šablony portálu pro vývojáře  
 Ovládací `sign-up` prvek lze použít v následujících šablonách portálu pro vývojáře:  
  
-   [Registrace](api-management-page-templates.md#SignUp)  
  
##  <a name="subscribe-button"></a><a name="subscribe-button"></a>tlačítko pro přihlášení k odběru  
 Poskytuje `subscribe-button` ovládací prvek pro přihlášení uživatele k produktu.  
  
 ![přihlášení k odběru ovládacího prvku tlačítka&#45;](./media/api-management-page-controls/APIM-subscribe-button-control.png "Ovládací prvek tlačítka odběru APIM")  
  
### <a name="usage"></a>Využití  
  
```xml  
<subscribe-button></subscribe-button>  
```  
  
### <a name="parameters"></a>Parametry  
 Žádné.  
  
### <a name="developer-portal-templates"></a>Šablony portálu pro vývojáře  
 Ovládací `subscribe-button` prvek lze použít v následujících šablonách portálu pro vývojáře:  
  
-   [Produktu](api-management-product-templates.md#Product)  
  
##  <a name="subscription-cancel"></a><a name="subscription-cancel"></a>zrušení předplatného  
 Ovládací `subscription-cancel` prvek poskytuje ovládací prvek pro zrušení předplatného produktu na stránce profilu uživatele na portálu pro vývojáře.  
  
 ![ovládací prvek&#45;zrušení předplatného](./media/api-management-page-controls/APIM-subscription-cancel-control.png "Ovládací prvek apim subscription-cancel")  
  
### <a name="usage"></a>Využití  
  
```xml  
<subscription-cancel params="{ subscriptionId: '{{subscription.id}}', cancelUrl: '{{subscription.cancelUrl}}' }">  
</subscription-cancel>  
  
```  
  
### <a name="parameters"></a>Parametry  
  
|Parametr|Popis|  
|---------------|-----------------|  
|subscriptionId|ID předplatného zrušit.|  
|cancelUrl|Předplatné zruší adresu URL.|  
  
### <a name="developer-portal-templates"></a>Šablony portálu pro vývojáře  
 Ovládací `subscription-cancel` prvek lze použít v následujících šablonách portálu pro vývojáře:  
  
-   [Produktu](api-management-product-templates.md#Product)

## <a name="next-steps"></a>Další kroky
Další informace o práci se šablonami najdete [v tématu Jak přizpůsobit portál pro vývojáře pro správu rozhraní API pomocí šablon](api-management-developer-portal-templates.md).
