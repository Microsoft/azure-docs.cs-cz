---
title: Ovládací prvky stránky Azure API Management | Microsoft Docs
description: Další informace o ovládací prvky stránky, která je k dispozici pro použití v šablonách portálu vývojáře ve službě Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/20/2017
ms.author: apimpm
ms.openlocfilehash: da68c9b7ebbb1880e35bd60b12db9f920f51e13c
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="azure-api-management-page-controls"></a>Ovládací prvky stránky Azure API Management
Azure API Management poskytuje následující ovládací prvky pro použití v vývojář portálu šablony.  
  
Použití ovládacího prvku, umístěte ho na požadované místo v šabloně portálu pro vývojáře. Některé ovládací prvky, jako například [akcí aplikace](#app-actions) řízení, musí mít parametry, jak je znázorněno v následujícím příkladu:  
  
```xml  
<app-actions params="{ appId: '{{app.id}}' }"></app-actions>  
```  
  
 Hodnoty parametrů jsou předána jako součást datový model pro šablonu. Ve většině případů můžete jednoduše vložit zadaný příklad pro každý ovládací prvek pro mohl správně fungovat. Další informace o hodnoty parametrů uvidíte oddíl modelu dat ke každé šabloně, ve kterém může použít ovládacího prvku.  
  
 Další informace o práci se šablonami najdete v tématu [postup přizpůsobení portálu pro vývojáře API Management pomocí šablon](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/).  
  
## <a name="developer-portal-template-page-controls"></a>Ovládací prvky stránky šablony portálu vývojáře  
  
-   [Akce aplikace](#app-actions)  
-   [Basic přihlášení](#basic-signin)  
-   [ovládací prvek stránkování](#paging-control)  
-   [Zprostředkovatelé](#providers)  
-   [ovládací prvek vyhledávání](#search-control)  
-   [registrace](#sign-up)  
-   [přihlášení k odběru tlačítko](#subscribe-button)  
-   [zrušení předplatného](#subscription-cancel)  
  
##  <a name="app-actions"></a> Akce aplikace  
 `app-actions` Řízení poskytuje uživatelské rozhraní pro interakci s aplikacemi na stránce profilu uživatele na portál pro vývojáře.  
  
 ![aplikace&#45;akce řízení](./media/api-management-page-controls/APIM-app-actions-control.png "APIM akcí aplikace řízení")  
  
### <a name="usage"></a>Využití  
  
```xml  
<app-actions params="{ appId: '{{app.id}}' }"></app-actions>  
```  
  
### <a name="parameters"></a>Parametry  
  
|Parametr|Popis|  
|---------------|-----------------|  
|appId|Id aplikace.|  
  
### <a name="developer-portal-templates"></a>Šablony na portálu vývojáře  
 `app-actions` Řízení je možné použít následující šablony portálu developer:  
  
-   [Aplikace](api-management-user-profile-templates.md#Applications)  
  
##  <a name="basic-signin"></a> Basic přihlášení  
 `basic-signin` Řízení poskytuje ovládací prvek pro shromažďování uživatelské přihlašovací údaje na stránce přihlášení v portálu pro vývojáře.  
  
 ![základní&#45;přihlášení řízení](./media/api-management-page-controls/APIM-basic-signin-control.png "APIM basic přihlášení řízení")  
  
### <a name="usage"></a>Využití  
  
```xml  
<basic-SignIn></basic-SignIn>  
```  
  
### <a name="parameters"></a>Parametry  
 Žádné.  
  
### <a name="developer-portal-templates"></a>Šablony na portálu vývojáře  
 `basic-signin` Řízení je možné použít následující šablony portálu developer:  
  
-   [Přihlásit se](api-management-page-templates.md#SignIn)  
  
##  <a name="paging-control"></a> ovládací prvek stránkování  
 `paging-control` Poskytuje funkce stránkování vývojáře portálu stránky zobrazující seznam položek.  
  
 ![stránkování řízení](./media/api-management-page-controls/APIM-paging-control.png "APIM stránkování ovládacího prvku")  
  
### <a name="usage"></a>Využití  
  
```xml  
<paging-control></paging-control>  
```  
  
### <a name="parameters"></a>Parametry  
 Žádné.  
  
### <a name="developer-portal-templates"></a>Šablony na portálu vývojáře  
 `paging-control` Řízení je možné použít následující šablony portálu developer:  
  
-   [Rozhraní API seznamu](api-management-api-templates.md#APIList)  
  
-   [Seznam problémů](api-management-issue-templates.md#IssueList)  
  
-   [Seznam produktů](api-management-product-templates.md#ProductList)  
  
##  <a name="providers"></a> Zprostředkovatelé  
 `providers` Řízení poskytuje ovládací prvek pro výběr zprostředkovatele ověřování na stránce přihlášení v portálu pro vývojáře.  
  
 ![poskytovatelé řízení](./media/api-management-page-controls/APIM-providers-control.png "APIM poskytovatelé řízení")  
  
### <a name="usage"></a>Využití  
  
```xml  
<providers></providers>  
```  
  
### <a name="parameters"></a>Parametry  
 Žádné.  
  
### <a name="developer-portal-templates"></a>Šablony na portálu vývojáře  
 `providers` Řízení je možné použít následující šablony portálu developer:  
  
-   [Přihlásit se](api-management-page-templates.md#SignIn)  
  
##  <a name="search-control"></a> ovládací prvek vyhledávání  
 `search-control` Poskytuje funkce hledání vývojáře portálu stránky zobrazující seznam položek.  
  
 ![hledání řízení](./media/api-management-page-controls/APIM-search-control.png "APIM vyhledávání řízení")  
  
### <a name="usage"></a>Využití  
  
```xml  
<search-control></search-control>  
```  
  
### <a name="parameters"></a>Parametry  
 Žádné.  
  
### <a name="developer-portal-templates"></a>Šablony na portálu vývojáře  
 `search-control` Řízení je možné použít následující šablony portálu developer:  
  
-   [Rozhraní API seznamu](api-management-api-templates.md#APIList)  
  
-   [Seznam produktů](api-management-product-templates.md#ProductList)  
  
##  <a name="sign-up"></a> registrace  
 `sign-up` Řízení poskytuje ovládací prvek pro shromažďování informací o profilu uživatele na stránce registrace v portálu pro vývojáře.  
  
 ![přihlašovací&#45;až řízení](./media/api-management-page-controls/APIM-sign-up-control.png "APIM registrace ovládacího prvku")  
  
### <a name="usage"></a>Využití  
  
```xml  
<sign-up></sign-up>  
```  
  
### <a name="parameters"></a>Parametry  
 Žádné.  
  
### <a name="developer-portal-templates"></a>Šablony na portálu vývojáře  
 `sign-up` Řízení je možné použít následující šablony portálu developer:  
  
-   [Registrace](api-management-page-templates.md#SignUp)  
  
##  <a name="subscribe-button"></a> přihlášení k odběru tlačítko  
 `subscribe-button` Poskytuje ovládací prvek pro přihlášení k odběru uživatele k produktu.  
  
 ![přihlášení k odběru&#45;tlačítko řízení](./media/api-management-page-controls/APIM-subscribe-button-control.png "APIM přihlášení k odběru tlačítko – ovládací prvek")  
  
### <a name="usage"></a>Využití  
  
```xml  
<subscribe-button></subscribe-button>  
```  
  
### <a name="parameters"></a>Parametry  
 Žádné.  
  
### <a name="developer-portal-templates"></a>Šablony na portálu vývojáře  
 `subscribe-button` Řízení je možné použít následující šablony portálu developer:  
  
-   [Produktu](api-management-product-templates.md#Product)  
  
##  <a name="subscription-cancel"></a> zrušení předplatného  
 `subscription-cancel` Řízení poskytuje ovládací prvek pro zrušení odběru produktu na stránce profilu uživatele v portálu pro vývojáře.  
  
 ![předplatné&#45;zrušit řízení](./media/api-management-page-controls/APIM-subscription-cancel-control.png "řízení APIM zrušit předplatné")  
  
### <a name="usage"></a>Využití  
  
```xml  
<subscription-cancel params="{ subscriptionId: '{{subscription.id}}', cancelUrl: '{{subscription.cancelUrl}}' }">  
</subscription-cancel>  
  
```  
  
### <a name="parameters"></a>Parametry  
  
|Parametr|Popis|  
|---------------|-----------------|  
|subscriptionId|Id předplatného zrušit.|  
|cancelUrl|Předplatné zruší adresy URL.|  
  
### <a name="developer-portal-templates"></a>Šablony na portálu vývojáře  
 `subscription-cancel` Řízení je možné použít následující šablony portálu developer:  
  
-   [Produktu](api-management-product-templates.md#Product)

## <a name="next-steps"></a>Další postup
Další informace o práci se šablonami najdete v tématu [postup přizpůsobení portálu pro vývojáře API Management pomocí šablon](api-management-developer-portal-templates.md).