---
title: Ovládací prvky stránky Azure API Management | Dokumentace Microsoftu
description: Další informace o ovládacích prvků stránky, která je k dispozici pro použití v šablon portálu pro vývojáře ve službě Azure API Management.
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
ms.openlocfilehash: d87293d89e4009512494bf47f9742ea5901f909a
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/28/2018
ms.locfileid: "52445358"
---
# <a name="azure-api-management-page-controls"></a>Ovládací prvky stránky Azure API Management
Azure API Management poskytuje následující ovládací prvky pro použití v vývojář šablon portálu.  
  
Použití ovládacího prvku, umístěte ho do požadovaného umístění v šabloně portálu pro vývojáře. Některé ovládací prvky, jako [akcí aplikace](#app-actions) řídit, které mají parametry, jak je znázorněno v následujícím příkladu:  
  
```xml  
<app-actions params="{ appId: '{{app.id}}' }"></app-actions>  
```  
  
 Hodnoty pro parametry jsou předány v rámci datový model pro šablonu. Ve většině případů můžete jednoduše vložit do zadané příklad pro každý ovládací prvek, aby to fungovalo správně. Další informace o hodnoty parametrů uvidíte datové části model ke každé šabloně, ve kterém ovládací prvek slouží.  
  
 Další informace o práci se šablonami najdete v tématu [přizpůsobení portálu pro správu rozhraní API pro vývojáře pomocí šablon](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/).  

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
## <a name="developer-portal-template-page-controls"></a>Ovládací prvky stránky šablony portálu pro vývojáře  
  
-   [aplikace akcí](#app-actions)  
-   [Basic – přihlášení](#basic-signin)  
-   [ovládací prvek stránkování](#paging-control)  
-   [Zprostředkovatelé](#providers)  
-   [ovládací prvek hledání](#search-control)  
-   [Zaregistrujte se](#sign-up)  
-   [tlačítko přihlášení odběru](#subscribe-button)  
-   [zrušení předplatného](#subscription-cancel)  
  
##  <a name="app-actions"></a> aplikace akcí  
 `app-actions` Ovládacího prvku poskytuje uživatelské rozhraní pro interakci s aplikací na stránku profilu uživatele na portálu pro vývojáře.  
  
 ![aplikace&#45;akce řízení](./media/api-management-page-controls/APIM-app-actions-control.png "APIM akcí aplikace ovládací prvek")  
  
### <a name="usage"></a>Využití  
  
```xml  
<app-actions params="{ appId: '{{app.id}}' }"></app-actions>  
```  
  
### <a name="parameters"></a>Parametry  
  
|Parametr|Popis|  
|---------------|-----------------|  
|appId|Id aplikace.|  
  
### <a name="developer-portal-templates"></a>Šablon portálu pro vývojáře  
 `app-actions` Ovládací prvek lze použít v následujících šablon portálu pro vývojáře:  
  
-   [Aplikace](api-management-user-profile-templates.md#Applications)  
  
##  <a name="basic-signin"></a> Basic – přihlášení  
 `basic-signin` Ovládací prvek obsahuje ovládací prvek k shromažďování údajů o uživatelské přihlašovací údaje na stránce přihlášení v portálu pro vývojáře.  
  
 ![základní&#45;signin řízení](./media/api-management-page-controls/APIM-basic-signin-control.png "APIM basic signin ovládacího prvku")  
  
### <a name="usage"></a>Využití  
  
```xml  
<basic-SignIn></basic-SignIn>  
```  
  
### <a name="parameters"></a>Parametry  
 Žádné.  
  
### <a name="developer-portal-templates"></a>Šablon portálu pro vývojáře  
 `basic-signin` Ovládací prvek lze použít v následujících šablon portálu pro vývojáře:  
  
-   [Přihlásit se](api-management-page-templates.md#SignIn)  
  
##  <a name="paging-control"></a> ovládací prvek stránkování  
 `paging-control` Poskytuje funkce stránkování na vývojáře portálu stránek, které zobrazují seznam položek.  
  
 ![ovládací prvek stránkování](./media/api-management-page-controls/APIM-paging-control.png "ovládací prvek stránkování APIM")  
  
### <a name="usage"></a>Využití  
  
```xml  
<paging-control></paging-control>  
```  
  
### <a name="parameters"></a>Parametry  
 Žádné.  
  
### <a name="developer-portal-templates"></a>Šablon portálu pro vývojáře  
 `paging-control` Ovládací prvek lze použít v následujících šablon portálu pro vývojáře:  
  
-   [Seznam rozhraní API](api-management-api-templates.md#APIList)  
  
-   [Seznam problémů](api-management-issue-templates.md#IssueList)  
  
-   [Seznam produktů](api-management-product-templates.md#ProductList)  
  
##  <a name="providers"></a> Zprostředkovatelé  
 `providers` Ovládací prvek obsahuje ovládací prvek pro výběr zprostředkovatele ověřování na stránce přihlášení v portálu pro vývojáře.  
  
 ![ovládací prvek Providers](./media/api-management-page-controls/APIM-providers-control.png "APIM ovládací prvek providers")  
  
### <a name="usage"></a>Využití  
  
```xml  
<providers></providers>  
```  
  
### <a name="parameters"></a>Parametry  
 Žádné.  
  
### <a name="developer-portal-templates"></a>Šablon portálu pro vývojáře  
 `providers` Ovládací prvek lze použít v následujících šablon portálu pro vývojáře:  
  
-   [Přihlásit se](api-management-page-templates.md#SignIn)  
  
##  <a name="search-control"></a> ovládací prvek hledání  
 `search-control` Obsahuje vyhledávací funkce pro vývojáře portálu stránek, které zobrazují seznam položek.  
  
 ![Hledat ovládací prvek](./media/api-management-page-controls/APIM-search-control.png "ovládací prvek hledání služby APIM")  
  
### <a name="usage"></a>Využití  
  
```xml  
<search-control></search-control>  
```  
  
### <a name="parameters"></a>Parametry  
 Žádné.  
  
### <a name="developer-portal-templates"></a>Šablon portálu pro vývojáře  
 `search-control` Ovládací prvek lze použít v následujících šablon portálu pro vývojáře:  
  
-   [Seznam rozhraní API](api-management-api-templates.md#APIList)  
  
-   [Seznam produktů](api-management-product-templates.md#ProductList)  
  
##  <a name="sign-up"></a> Zaregistrujte se  
 `sign-up` Ovládací prvek obsahuje ovládací prvek pro shromažďování informací o profilu uživatele na stránce registrace v portálu pro vývojáře.  
  
 ![znaménko&#45;nahoru ovládacího prvku](./media/api-management-page-controls/APIM-sign-up-control.png "Zaregistrujte ovládací prvek služby APIM")  
  
### <a name="usage"></a>Využití  
  
```xml  
<sign-up></sign-up>  
```  
  
### <a name="parameters"></a>Parametry  
 Žádné.  
  
### <a name="developer-portal-templates"></a>Šablon portálu pro vývojáře  
 `sign-up` Ovládací prvek lze použít v následujících šablon portálu pro vývojáře:  
  
-   [Zaregistrovat se](api-management-page-templates.md#SignUp)  
  
##  <a name="subscribe-button"></a> tlačítko přihlášení odběru  
 `subscribe-button` Poskytuje ovládací prvek pro přihlášení k odběru uživatelů do produktu.  
  
 ![přihlášení odběru&#45;ovládacímu prvku tlačítko](./media/api-management-page-controls/APIM-subscribe-button-control.png "APIM odběru – ovládací prvek")  
  
### <a name="usage"></a>Využití  
  
```xml  
<subscribe-button></subscribe-button>  
```  
  
### <a name="parameters"></a>Parametry  
 Žádné.  
  
### <a name="developer-portal-templates"></a>Šablon portálu pro vývojáře  
 `subscribe-button` Ovládací prvek lze použít v následujících šablon portálu pro vývojáře:  
  
-   [Produkt](api-management-product-templates.md#Product)  
  
##  <a name="subscription-cancel"></a> zrušení předplatného  
 `subscription-cancel` Ovládací prvek obsahuje ovládací prvek pro zrušení odběru produktů na stránce profilu uživatele na portálu pro vývojáře.  
  
 ![předplatné&#45;zrušit řízení](./media/api-management-page-controls/APIM-subscription-cancel-control.png "řízení zrušit předplatné služby APIM")  
  
### <a name="usage"></a>Využití  
  
```xml  
<subscription-cancel params="{ subscriptionId: '{{subscription.id}}', cancelUrl: '{{subscription.cancelUrl}}' }">  
</subscription-cancel>  
  
```  
  
### <a name="parameters"></a>Parametry  
  
|Parametr|Popis|  
|---------------|-----------------|  
|subscriptionId|Id odběru, který chcete zrušit.|  
|cancelUrl|Předplatné se zruší adresa URL.|  
  
### <a name="developer-portal-templates"></a>Šablon portálu pro vývojáře  
 `subscription-cancel` Ovládací prvek lze použít v následujících šablon portálu pro vývojáře:  
  
-   [Produkt](api-management-product-templates.md#Product)

## <a name="next-steps"></a>Další postup
Další informace o práci se šablonami najdete v tématu [přizpůsobení portálu pro správu rozhraní API pro vývojáře pomocí šablon](api-management-developer-portal-templates.md).