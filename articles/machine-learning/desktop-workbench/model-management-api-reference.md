---
title: Vytvoření bitové kopie Docker Model správy v nástroji Azure Machine Learning | Microsoft Docs
description: Tento článek popisuje kroky pro vytvoření image Docker pro webovou službu.
services: machine-learning
author: chhavib
ms.author: chhavib
manager: hjerez
editor: jasonwhowell
ms.reviewer: jasonwhowell, mldocs
ms.service: machine-learning
ms.component: desktop-workbench
ms.workload: data-services
ms.devlang: na
ms.topic: article
ms.date: 09/20/2017
ms.openlocfilehash: 233ae50246619c3e503e42081c3b4de88090f411
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34835028"
---
# <a name="azure-machine-learning-model-management-account-api-reference"></a>Azure Machine Learning Model správy účtu API – referenční informace

Informace o nastavení prostředí pro nasazení najdete v tématu [nastavení modelu správce účtu](deployment-setup-configuration.md).

Azure Machine Learning Model správy účtu API implementuje následující operace:

- Registrace modelu
- Vytvoření manifestu
- Vytvoření bitové kopie docker
- Vytvoření webové služby

Tuto bitovou kopii můžete použít k vytvoření webové služby místně nebo na vzdálený cluster Azure Container Service nebo jiné podporované Docker prostředí podle svého výběru.

## <a name="prerequisites"></a>Požadavky
Zajistěte, aby jste prošli kroků instalace v [instalovat a vytvořte rychlý Start](../service/quickstart-installation.md) dokumentu.

Toto jsou požadovány, než budete pokračovat:
1. Model správy zřizování účtu
2. Vytvoření prostředí pro nasazení a správu modelů
3. Model Machine Learning

### <a name="azure-ad-token"></a>Azure AD token
Pokud používáte Azure CLI, přihlaste se pomocí `az login`. Rozhraní příkazového řádku používá váš token zabezpečení Azure Active Directory (Azure AD) ze souboru .azure. Pokud chcete použít rozhraní API, máte následující možnosti.

##### <a name="acquire-the-azure-ad-token-manually"></a>Získání tokenu Azure AD ručně
Můžete použít `az login` a získat nejnovější token ze souboru .azure na domovský adresář.

##### <a name="acquire-the-azure-ad-token-programmatically"></a>Získání tokenu Azure AD prostřednictvím kódu programu
```
az ad sp create-for-rbac --scopes /subscriptions/<SubscriptionId>/resourcegroups/<ResourceGroupName> --role Contributor --years <length of time> --name <MyservicePrincipalContributor>
```
Po vytvoření objektu služby, uložte si výstup. Teď, můžete použít k získání tokenu z Azure AD:

```cs
 private static async Task<string> AcquireTokenAsync(string clientId, string password, string authority, string resource)
{
        var creds = new ClientCredential(clientId, password);
        var context = new AuthenticationContext(authority);
        var token = await context.AcquireTokenAsync(resource, creds).ConfigureAwait(false);
        return token.AccessToken;
}
```
Token je uvést v hlavičce autorizace pro volání rozhraní API.


## <a name="register-a-model"></a>Zaregistrovat modelu

Krok registrace modelu zaregistruje váš model Machine Learning s Azure Model správy účtu, který jste vytvořili. Tato registrace umožňuje sledování modelů a jejich verze, které jsou přiřazeny v době registrace. Uživatel poskytuje název modelu. Následující registraci modelů pod stejným názvem generuje novou verzí a ID.

### <a name="request"></a>Žádost

| Metoda | Identifikátor URI žádosti |
|------------|------------|
| POST |  /API/subscriptions / {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} / modelů 
 |
### <a name="description"></a>Popis
Zaregistruje modelu.

### <a name="parameters"></a>Parametry
| Název | Nachází se | Popis | Požaduje se | Schéma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID předplatného Azure. | Ano | řetězec |
| resourceGroupName | path | Název skupiny prostředků, ve kterém je umístěn účet modelu správy. | Ano | řetězec |
| název účtu | path | Název účtu pro správu modelu. | Ano | řetězec |
| verze rozhraní API. | query | Verze zprostředkovatele prostředků Microsoft.Machine.Learning rozhraní API používat. | Ano | řetězec |
| Autorizace | záhlaví | Autorizační token. Měla by vypadat jako "Nosiče XXXXXX." | Ano | řetězec |
| model | hlavní část | Datové části, která se používá k registraci modelu. | Ano | [Model](#model) |


### <a name="responses"></a>Odezvy
| Kód | Popis | Schéma |
|--------------------|--------------------|--------------------|
| 200 | OK. Model registrace byla úspěšná. | [Model](#model) |
| default | Odpovědi na chybu, která popisuje, proč operace se nezdařila. | [ErrorResponse](#errorresponse) |

## <a name="query-the-list-of-models-in-an-account"></a>Dotaz na seznam modelů v účtu
### <a name="request"></a>Žádost
| Metoda | Identifikátor URI žádosti |
|------------|------------|
| GET |  /API/subscriptions / {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} / modelů 
 |
### <a name="description"></a>Popis
Dotazuje seznam modelů v účtu. Můžete filtrovat seznam výsledků podle značky a názvu. Pokud není předán žádný filtr dotaz uvádí všechny modely v účtu. Vráceném seznamu jsou čísla stránek vložena a počet položek v každé stránce je volitelný parametr.

### <a name="parameters"></a>Parametry
| Název | Nachází se | Popis | Požaduje se | Schéma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID předplatného Azure. | Ano | řetězec |
| resourceGroupName | path | Název skupiny prostředků, ve kterém je umístěn účet modelu správy. | Ano | řetězec |
| název účtu | path | Název účtu pro správu modelu. | Ano | řetězec |
| verze rozhraní API. | query | Verze zprostředkovatele prostředků Microsoft.Machine.Learning rozhraní API používat. | Ano | řetězec |
| Autorizace | záhlaví | Autorizační token. Měla by vypadat jako "Nosiče XXXXXX." | Ano | řetězec |
| jméno | query | Název objektu. | Ne | řetězec |
| značka | query | Značka modelu. | Ne | řetězec |
| počet | query | Počet položek načíst na stránce. | Ne | řetězec |
| $skipToken | query | Token pro pokračování pro načtení další stránky. | Ne | řetězec |

### <a name="responses"></a>Odezvy
| Kód | Popis | Schéma |
|--------------------|--------------------|--------------------|
| 200 | Úspěch | [PaginatedModelList](#paginatedmodellist) |
| default | Odpovědi na chybu, která popisuje, proč operace se nezdařila. | [ErrorResponse](#errorresponse) |

## <a name="get-model-details"></a>Získat podrobné informace o modelu
### <a name="request"></a>Žádost
| Metoda | Identifikátor URI žádosti |
|------------|------------|
| GET |  /API/subscriptions / {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} /models/ {id}  
 |

### <a name="description"></a>Popis
Získá model podle ID.

### <a name="parameters"></a>Parametry
| Název | Nachází se | Popis | Požaduje se | Schéma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID předplatného Azure. | Ano | řetězec |
| resourceGroupName | path | Název skupiny prostředků, ve kterém je umístěn účet modelu správy. | Ano | řetězec |
| název účtu | path | Název účtu pro správu modelu. | Ano | řetězec |
| id | path | ID objektu. | Ano | řetězec |
| verze rozhraní API. | query | Verze zprostředkovatele prostředků Microsoft.Machine.Learning rozhraní API používat. | Ano | řetězec |
| Autorizace | záhlaví | Autorizační token. Měla by vypadat jako "Nosiče XXXXXX." | Ano | řetězec |

### <a name="responses"></a>Odezvy
| Kód | Popis | Schéma |
|--------------------|--------------------|--------------------|
| 200 | Úspěch | [Model](#model) |
| default | Odpovědi na chybu, která popisuje, proč operace se nezdařila. | [ErrorResponse](#errorresponse) |

## <a name="register-a-manifest-with-the-registered-model-and-all-dependencies"></a>Zaregistrovat manifestu registrované modelu a všechny závislosti

### <a name="request"></a>Žádost
| Metoda | Identifikátor URI žádosti |
|------------|------------|
| POST |  /API/subscriptions / {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} / manifesty | 

### <a name="description"></a>Popis
Zaregistruje manifestu se registrované modelu a všechny jeho závislé součásti.

### <a name="parameters"></a>Parametry
| Název | Nachází se | Popis | Požaduje se | Schéma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID předplatného Azure. | Ano | řetězec |
| resourceGroupName | path | Název skupiny prostředků, ve kterém je umístěn účet modelu správy. | Ano | řetězec |
| název účtu | path | Název účtu pro správu modelu. | Ano | řetězec |
| verze rozhraní API. | query | Verze zprostředkovatele prostředků Microsoft.Machine.Learning rozhraní API používat. | Ano | řetězec |
| Autorizace | záhlaví | Autorizační token. Měla by vypadat jako "Nosiče XXXXXX." | Ano | řetězec |
| manifestRequest | hlavní část | Datová část, která slouží k registraci manifestu. | Ano | [Manifest](#manifest) |

### <a name="responses"></a>Odezvy
| Kód | Popis | Schéma |
|--------------------|--------------------|--------------------|
| 200 | Manifestu registrace byla úspěšná. | [Manifest](#manifest) |
| default | Odpovědi na chybu, která popisuje, proč operace se nezdařila. | [ErrorResponse](#errorresponse) |

## <a name="query-the-list-of-manifests-in-an-account"></a>Dotaz na seznam manifesty na účtu

### <a name="request"></a>Žádost
| Metoda | Identifikátor URI žádosti |
|------------|------------|
| GET |  /API/subscriptions / {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} / manifesty | 

### <a name="description"></a>Popis
Dotazuje seznam manifesty v účtu. Můžete filtrovat seznam výsledků podle modelu ID a název manifestu. Pokud není předán žádný filtr dotaz uvádí všechny manifesty v účtu. Vráceném seznamu jsou čísla stránek vložena a počet položek v každé stránce je volitelný parametr.

### <a name="parameters"></a>Parametry
| Název | Nachází se | Popis | Požaduje se | Schéma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID předplatného Azure. | Ano | řetězec |
| resourceGroupName | path | Název skupiny prostředků, ve kterém je umístěn účet modelu správy. | Ano | řetězec |
| název účtu | path | Název účtu pro správu modelu. | Ano | řetězec |
| verze rozhraní API. | query | Verze zprostředkovatele prostředků Microsoft.Machine.Learning rozhraní API používat. | Ano | řetězec |
| Autorizace | záhlaví | Autorizační token. Měla by vypadat jako "Nosiče XXXXXX." | Ano | řetězec |
| modelId | query | ID modelu. | Ne | řetězec |
| ManifestName | query | Název souboru manifestu. | Ne | řetězec |
| počet | query | Počet položek načíst na stránce. | Ne | řetězec |
| $skipToken | query | Token pro pokračování pro načtení další stránky. | Ne | řetězec |

### <a name="responses"></a>Odezvy
| Kód | Popis | Schéma |
|--------------------|--------------------|--------------------|
| 200 | Úspěch | [PaginatedManifestList](#paginatedmanifestlist) |
| default | Odpovědi na chybu, která popisuje, proč operace se nezdařila. | [ErrorResponse](#errorresponse) |

## <a name="get-manifest-details"></a>Získat podrobnosti o manifestu

### <a name="request"></a>Žádost
| Metoda | Identifikátor URI žádosti |
|------------|------------|
| GET |  /API/subscriptions / {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} /manifests/ {id} | 

### <a name="description"></a>Popis
Získá manifest podle ID.

### <a name="parameters"></a>Parametry
| Název | Nachází se | Popis | Požaduje se | Schéma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID předplatného Azure. | Ano | řetězec |
| resourceGroupName | path | Název skupiny prostředků, ve kterém je umístěn účet modelu správy. | Ano | řetězec |
| název účtu | path | Název účtu pro správu modelu. | Ano | řetězec |
| id | path | ID objektu. | Ano | řetězec |
| verze rozhraní API. | query | Verze zprostředkovatele prostředků Microsoft.Machine.Learning rozhraní API používat. | Ano | řetězec |
| Autorizace | záhlaví | Autorizační token. Měla by vypadat jako "Nosiče XXXXXX." | Ano | řetězec |

### <a name="responses"></a>Odezvy
| Kód | Popis | Schéma |
|--------------------|--------------------|--------------------|
| 200 | Úspěch | [Manifest](#manifest) |
| default | Odpovědi na chybu, která popisuje, proč operace se nezdařila. | [ErrorResponse](#errorresponse) |

## <a name="create-an-image"></a>Vytvoření image

### <a name="request"></a>Žádost
| Metoda | Identifikátor URI žádosti |
|------------|------------|
| POST |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images | 

### <a name="description"></a>Popis
Vytvoří bitovou kopii jako obrázek na Docker v registru kontejner Azure.

### <a name="parameters"></a>Parametry
| Název | Nachází se | Popis | Požaduje se | Schéma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID předplatného Azure. | Ano | řetězec |
| resourceGroupName | path | Název skupiny prostředků, ve kterém je umístěn účet modelu správy. | Ano | řetězec |
| název účtu | path | Název účtu pro správu modelu. | Ano | řetězec |
| verze rozhraní API. | query | Verze zprostředkovatele prostředků Microsoft.Machine.Learning rozhraní API používat. | Ano | řetězec |
| Autorizace | záhlaví | Autorizační token. Měla by vypadat jako "Nosiče XXXXXX." | Ano | řetězec |
| imageRequest | hlavní část | Datová část, která se používá k vytvoření image. | Ano | [imageRequest](#imagerequest) |

### <a name="responses"></a>Odezvy
| Kód | Popis | Záhlaví | Schéma |
|--------------------|--------------------|--------------------|--------------------|
| 202 | Adresa URL umístění asynchronní operaci. Volání GET zobrazí stav úlohy vytváření bitové kopie. | Operace umístění |
| default | Odpovědi na chybu, která popisuje, proč operace se nezdařila. | [ErrorResponse](#errorresponse) |

## <a name="query-the-list-of-images-in-an-account"></a>Dotaz na seznam obrázků v účtu

### <a name="request"></a>Žádost
| Metoda | Identifikátor URI žádosti |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images | 

### <a name="description"></a>Popis
Dotazy v seznamu bitových kopií v účtu. Můžete filtrovat seznam výsledků manifestu ID a název. Pokud není předán žádný filtr dotaz uvádí všechny bitové kopie v účtu. Vráceném seznamu jsou čísla stránek vložena a počet položek v každé stránce je volitelný parametr.

### <a name="parameters"></a>Parametry
| Název | Nachází se | Popis | Požaduje se | Schéma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID předplatného Azure. | Ano | řetězec |
| resourceGroupName | path | Název skupiny prostředků, ve kterém je umístěn účet modelu správy. | Ano | řetězec |
| název účtu | path | Název účtu pro správu modelu. | Ano | řetězec |
| verze rozhraní API. | query | Verze zprostředkovatele prostředků Microsoft.Machine.Learning rozhraní API používat. | Ano | řetězec |
| Autorizace | záhlaví | Autorizační token. Měla by vypadat jako "Nosiče XXXXXX." | Ano | řetězec |
| ID manifestu | query | ID manifestu. | Ne | řetězec |
| ManifestName | query | Název souboru manifestu. | Ne | řetězec |
| počet | query | Počet položek načíst na stránce. | Ne | řetězec |
| $skipToken | query | Token pro pokračování pro načtení další stránky. | Ne | řetězec |

### <a name="responses"></a>Odezvy
| Kód | Popis | Schéma |
|--------------------|--------------------|--------------------|
| 200 | Úspěch | [PaginatedImageList](#paginatedimagelist) |
| default | Odpovědi na chybu, která popisuje, proč operace se nezdařila. | [ErrorResponse](#errorresponse) |

## <a name="get-image-details"></a>Načíst podrobnosti image.

### <a name="request"></a>Žádost
| Metoda | Identifikátor URI žádosti |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images/{id} | 

### <a name="description"></a>Popis
Získá bitovou kopii podle ID.

### <a name="parameters"></a>Parametry
| Název | Nachází se | Popis | Požaduje se | Schéma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID předplatného Azure. | Ano | řetězec |
| resourceGroupName | path | Název skupiny prostředků, ve kterém je umístěn účet modelu správy. | Ano | řetězec |
| název účtu | path | Název účtu pro správu modelu. | Ano | řetězec |
| id | path | ID obrázku. | Ano | řetězec |
| verze rozhraní API. | query | Verze zprostředkovatele prostředků Microsoft.Machine.Learning rozhraní API používat. | Ano | řetězec |
| Autorizace | záhlaví | Autorizační token. Měla by vypadat jako "Nosiče XXXXXX." | Ano | řetězec |

### <a name="responses"></a>Odezvy
| Kód | Popis | Schéma |
|--------------------|--------------------|--------------------|
| 200 | Úspěch | [Image](#image) |
| default | Odpovědi na chybu, která popisuje, proč operace se nezdařila. | [ErrorResponse](#errorresponse) |


## <a name="create-a-service"></a>Vytvoření služby

### <a name="request"></a>Žádost
| Metoda | Identifikátor URI žádosti |
|------------|------------|
| POST |  /API/subscriptions / {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} / služby | 

### <a name="description"></a>Popis
Vytvoří službu z bitové kopie.

### <a name="parameters"></a>Parametry
| Název | Nachází se | Popis | Požaduje se | Schéma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID předplatného Azure. | Ano | řetězec |
| resourceGroupName | path | Název skupiny prostředků, ve kterém je umístěn účet modelu správy. | Ano | řetězec |
| název účtu | path | Název účtu pro správu modelu. | Ano | řetězec |
| verze rozhraní API. | query | Verze zprostředkovatele prostředků Microsoft.Machine.Learning rozhraní API používat. | Ano | řetězec |
| Autorizace | záhlaví | Autorizační token. Měla by vypadat jako "Nosiče XXXXXX." | Ano | řetězec |
| elementu serviceRequest | hlavní část | Datová část, která se používá k vytvoření služby. | Ano | [ServiceCreateRequest](#servicecreaterequest) |

### <a name="responses"></a>Odezvy
| Kód | Popis | Záhlaví | Schéma |
|--------------------|--------------------|--------------------|--------------------|
| 202 | Adresa URL umístění asynchronní operaci. Volání GET zobrazí stav úlohy vytváření služby. | Operace umístění |
| 409 | Služba se zadaným názvem již existuje. |
| default | Odpovědi na chybu, která popisuje, proč operace se nezdařila. | [ErrorResponse](#errorresponse) |

## <a name="query-the-list-of-services-in-an-account"></a>Dotaz na seznam služeb v účtu

### <a name="request"></a>Žádost
| Metoda | Identifikátor URI žádosti |
|------------|------------|
| GET |  /API/subscriptions / {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} / služby | 

### <a name="description"></a>Popis
Dotazuje seznam služeb v účtu. Můžete filtrovat seznam výsledků podle názvu nebo ID modelu, manifestu název nebo ID, ID bitové kopie, název služby nebo ID Machine Learning výpočetních prostředků. Pokud není předán žádný filtr dotaz uvádí všechny služby v účtu. Vráceném seznamu jsou čísla stránek vložena a počet položek v každé stránce je volitelný parametr.

### <a name="parameters"></a>Parametry
| Název | Nachází se | Popis | Požaduje se | Schéma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID předplatného Azure. | Ano | řetězec |
| resourceGroupName | path | Název skupiny prostředků, ve kterém je umístěn účet modelu správy. | Ano | řetězec |
| název účtu | path | Název účtu pro správu modelu. | Ano | řetězec |
| verze rozhraní API. | query | Verze zprostředkovatele prostředků Microsoft.Machine.Learning rozhraní API používat. | Ano | řetězec |
| Autorizace | záhlaví | Autorizační token. Měla by vypadat jako "Nosiče XXXXXX." | Ano | řetězec |
| serviceName | query | Název služby. | Ne | řetězec |
| modelId | query | Název modelu. | Ne | řetězec |
| modelName | query | ID modelu. | Ne | řetězec |
| ID manifestu | query | ID manifestu. | Ne | řetězec |
| ManifestName | query | Název souboru manifestu. | Ne | řetězec |
| ID obrázku | query | ID obrázku. | Ne | řetězec |
| computeResourceId | query | ID počítače Learning výpočetních prostředků. | Ne | řetězec |
| počet | query | Počet položek načíst na stránce. | Ne | řetězec |
| $skipToken | query | Token pro pokračování pro načtení další stránky. | Ne | řetězec |

### <a name="responses"></a>Odezvy
| Kód | Popis | Schéma |
|--------------------|--------------------|--------------------|
| 200 | Úspěch | [PaginatedServiceList](#paginatedservicelist) |
| default | Odpovědi na chybu, která popisuje, proč operace se nezdařila. | [ErrorResponse](#errorresponse) |

## <a name="get-service-details"></a>Získat podrobnosti služby

### <a name="request"></a>Žádost
| Metoda | Identifikátor URI žádosti |
|------------|------------|
| GET |  /API/subscriptions / {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} /services/ {id} | 

### <a name="description"></a>Popis
Získá službu podle ID.

### <a name="parameters"></a>Parametry
| Název | Nachází se | Popis | Požaduje se | Schéma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID předplatného Azure. | Ano | řetězec |
| resourceGroupName | path | Název skupiny prostředků, ve kterém je umístěn účet modelu správy. | Ano | řetězec |
| název účtu | path | Název účtu pro správu modelu. | Ano | řetězec |
| id | path | ID objektu. | Ano | řetězec |
| verze rozhraní API. | query | Verze zprostředkovatele prostředků Microsoft.Machine.Learning rozhraní API používat. | Ano | řetězec |
| Autorizace | záhlaví | Autorizační token. Měla by vypadat jako "Nosiče XXXXXX." | Ano | řetězec |

### <a name="responses"></a>Odezvy
| Kód | Popis | Schéma |
|--------------------|--------------------|--------------------|
| 200 | Úspěch | [ServiceResponse](#serviceresponse) |
| default | Odpovědi na chybu, která popisuje, proč operace se nezdařila. | [ErrorResponse](#errorresponse)

## <a name="update-a-service"></a>Aktualizace služby

### <a name="request"></a>Žádost
| Metoda | Identifikátor URI žádosti |
|------------|------------|
| PUT |  /API/subscriptions / {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} /services/ {id} | 

### <a name="description"></a>Popis
Aktualizuje existující službu.

### <a name="parameters"></a>Parametry
| Název | Nachází se | Popis | Požaduje se | Schéma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID předplatného Azure. | Ano | řetězec |
| resourceGroupName | path | Název skupiny prostředků, ve kterém je umístěn účet modelu správy. | Ano | řetězec |
| název účtu | path | Název účtu pro správu modelu. | Ano | řetězec |
| id | path | ID objektu. | Ano | řetězec |
| verze rozhraní API. | query | Verze zprostředkovatele prostředků Microsoft.Machine.Learning rozhraní API používat. | Ano | řetězec |
| Autorizace | záhlaví | Autorizační token. Měla by vypadat jako "Nosiče XXXXXX." | Ano | řetězec |
| serviceUpdateRequest | hlavní část | Datová část, která se používá k aktualizaci existující službu. | Ano |  [ServiceUpdateRequest](#serviceupdaterequest) |

### <a name="responses"></a>Odezvy
| Kód | Popis | Záhlaví | Schéma |
|--------------------|--------------------|--------------------|--------------------|
| 202 | Adresa URL umístění asynchronní operaci. Volání GET zobrazí stav úlohy aktualizace služby. | Operace umístění |
| 404 | Služba se zadaným ID neexistuje. |
| default | Odpovědi na chybu, která popisuje, proč operace se nezdařila. | [ErrorResponse](#errorresponse)

## <a name="delete-a-service"></a>Odstranění služby

### <a name="request"></a>Žádost
| Metoda | Identifikátor URI žádosti |
|------------|------------|
| DELETE |  /API/subscriptions / {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} /services/ {id} | 

### <a name="description"></a>Popis
Odstraní služby.

### <a name="parameters"></a>Parametry
| Název | Nachází se | Popis | Požaduje se | Schéma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID předplatného Azure. | Ano | řetězec |
| resourceGroupName | path | Název skupiny prostředků, ve kterém je umístěn účet modelu správy. | Ano | řetězec |
| název účtu | path | Název účtu pro správu modelu. | Ano | řetězec |
| id | path | ID objektu. | Ano | řetězec |
| verze rozhraní API. | query | Verze zprostředkovatele prostředků Microsoft.Machine.Learning rozhraní API používat. | Ano | řetězec |
| Autorizace | záhlaví | Autorizační token. Měla by vypadat jako "Nosiče XXXXXX." | Ano | řetězec |

### <a name="responses"></a>Odezvy
| Kód | Popis | Schéma |
|--------------------|--------------------|--------------------|
| 200 | Úspěch |  |
| 204 | Služba se zadaným ID neexistuje. |
| default | Odpovědi na chybu, která popisuje, proč operace se nezdařila. | [ErrorResponse](#errorresponse)

## <a name="get-service-keys"></a>Získat klíče služby

### <a name="request"></a>Žádost
| Metoda | Identifikátor URI žádosti |
|------------|------------|
| GET |  /API/subscriptions / {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} /services/ {id} / klíče | 

### <a name="description"></a>Popis
Získá klíče služby.

### <a name="parameters"></a>Parametry
| Název | Nachází se | Popis | Požaduje se | Schéma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID předplatného Azure. | Ano | řetězec |
| resourceGroupName | path | Název skupiny prostředků, ve kterém je umístěn účet modelu správy. | Ano | řetězec |
| název účtu | path | Název účtu pro správu modelu. | Ano | řetězec |
| id | path | ID služby. | Ano | řetězec |
| verze rozhraní API. | query | Verze zprostředkovatele prostředků Microsoft.Machine.Learning rozhraní API používat. | Ano | řetězec |
| Autorizace | záhlaví | Autorizační token. Měla by vypadat jako "Nosiče XXXXXX." | Ano | řetězec |

### <a name="responses"></a>Odezvy
| Kód | Popis | Schéma |
|--------------------|--------------------|--------------------|
| 200 | Úspěch | [AuthKeys](#authkeys)
| default | Odpovědi na chybu, která popisuje, proč operace se nezdařila. | [ErrorResponse](#errorresponse)

## <a name="regenerate-service-keys"></a>Obnovit klíče služby

### <a name="request"></a>Žádost
| Metoda | Identifikátor URI žádosti |
|------------|------------|
| POST |  /API/subscriptions / {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} /services/ {id} / regenerateKeys | 

### <a name="description"></a>Popis
Regeneruje klíče služby a vrátí tyto adresy.

### <a name="parameters"></a>Parametry
| Název | Nachází se | Popis | Požaduje se | Schéma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID předplatného Azure. | Ano | řetězec |
| resourceGroupName | path | Název skupiny prostředků, ve kterém je umístěn účet modelu správy. | Ano | řetězec |
| název účtu | path | Název účtu pro správu modelu. | Ano | řetězec |
| id | path | ID služby. | Ano | řetězec |
| verze rozhraní API. | query | Verze zprostředkovatele prostředků Microsoft.Machine.Learning rozhraní API používat. | Ano | řetězec |
| Autorizace | záhlaví | Autorizační token. Měla by vypadat jako "Nosiče XXXXXX." | Ano | řetězec |
| regenerateKeyRequest | hlavní část | Datová část, která se používá k aktualizaci existující službu. | Ano | [ServiceRegenerateKeyRequest](#serviceregeneratekeyrequest) |

### <a name="responses"></a>Odezvy
| Kód | Popis | Schéma |
|--------------------|--------------------|--------------------|
| 200 | Úspěch | [AuthKeys](#authkeys)
| default | Odpovědi na chybu, která popisuje, proč operace se nezdařila. | [ErrorResponse](#errorresponse)

## <a name="query-the-list-of-deployments-in-an-account"></a>Dotaz na seznam nasazení v účtu

### <a name="request"></a>Žádost
| Metoda | Identifikátor URI žádosti |
|------------|------------|
| GET |  /API/subscriptions / {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} / nasazení | 

### <a name="description"></a>Popis
Dotazuje seznamu nasazení v účtu. Můžete filtrovat seznam výsledků podle ID služby, které se vrátí jenom nasazení, které jsou vytvořené pro konkrétní službu. Pokud není předán žádný filtr dotaz uvádí všechna nasazení v účtu.

### <a name="parameters"></a>Parametry
| Název | Nachází se | Popis | Požaduje se | Schéma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID předplatného Azure. | Ano | řetězec |
| resourceGroupName | path | Název skupiny prostředků, ve kterém je umístěn účet modelu správy. | Ano | řetězec |
| název účtu | path | Název účtu pro správu modelu. | Ano | řetězec |
| verze rozhraní API. | query | Verze zprostředkovatele prostředků Microsoft.Machine.Learning rozhraní API používat. | Ano | řetězec |
| Autorizace | záhlaví | Autorizační token. Měla by vypadat jako "Nosiče XXXXXX." | Ano | řetězec |
| serviceId | query | ID služby. | Ne | řetězec |

### <a name="responses"></a>Odezvy
| Kód | Popis | Schéma |
|--------------------|--------------------|--------------------|
| 200 | Úspěch | [DeploymentList](#deploymentlist) |
| default | Odpovědi na chybu, která popisuje, proč operace se nezdařila. | [ErrorResponse](#errorresponse)

## <a name="get-deployment-details"></a>Získat podrobnosti o nasazení

### <a name="request"></a>Žádost
| Metoda | Identifikátor URI žádosti |
|------------|------------|
| GET |  /API/subscriptions / {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} /deployments/ {id} | 

### <a name="description"></a>Popis
Získá nasazení podle ID.

### <a name="parameters"></a>Parametry
| Název | Nachází se | Popis | Požaduje se | Schéma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID předplatného Azure. | Ano | řetězec |
| resourceGroupName | path | Název skupiny prostředků, ve kterém je umístěn účet modelu správy. | Ano | řetězec |
| název účtu | path | Název účtu pro správu modelu. | Ano | řetězec |
| id | path | ID nasazení. | Ano | řetězec |
| verze rozhraní API. | query | Verze zprostředkovatele prostředků Microsoft.Machine.Learning rozhraní API používat. | Ano | řetězec |
| Autorizace | záhlaví | Autorizační token. Měla by vypadat jako "Nosiče XXXXXX." | Ano | řetězec |

### <a name="responses"></a>Odezvy
| Kód | Popis | Schéma |
|--------------------|--------------------|--------------------|
| 200 | Úspěch | [Nasazení](#deployment) |
| default | Odpovědi na chybu, která popisuje, proč operace se nezdařila. | [ErrorResponse](#errorresponse)

## <a name="get-operation-details"></a>Získat podrobnosti o operaci

### <a name="request"></a>Žádost
| Metoda | Identifikátor URI žádosti |
|------------|------------|
| GET |  /API/subscriptions / {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} /operations/ {id} | 

### <a name="description"></a>Popis
Získá stav asynchronní operace podle ID operace.

### <a name="parameters"></a>Parametry
| Název | Nachází se | Popis | Požaduje se | Schéma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID předplatného Azure. | Ano | řetězec |
| resourceGroupName | path | Název skupiny prostředků, ve kterém je umístěn účet modelu správy. | Ano | řetězec |
| název účtu | path | Název účtu pro správu modelu. | Ano | řetězec |
| id | path | ID operace. | Ano | řetězec |
| verze rozhraní API. | query | Verze zprostředkovatele prostředků Microsoft.Machine.Learning rozhraní API používat. | Ano | řetězec |
| Autorizace | záhlaví | Autorizační token. Měla by vypadat jako "Nosiče XXXXXX." | Ano | řetězec |

### <a name="responses"></a>Odezvy
| Kód | Popis | Schéma |
|--------------------|--------------------|--------------------|
| 200 | Úspěch | [OperationStatus](#asyncoperationstatus) |
| default | Odpovědi na chybu, která popisuje, proč operace se nezdařila. | [ErrorResponse](#errorresponse)



<a name="definitions"></a>
## <a name="definitions"></a>Definice

<a name="asset"></a>
### <a name="asset"></a>Prostředek
Objekt asset, který bude potřeba při vytváření Docker obrázku.


|Název|Popis|Schéma|
|---|---|---|
|**ID**  <br>*Volitelné*|ID prostředku.|řetězec|
|**mimeType**  <br>*Volitelné*|Typ MIME modelu obsahu. Další informace o typu MIME najdete v tématu [seznam typů médií IANA](https://www.iana.org/assignments/media-types/media-types.xhtml).|řetězec|
|**Rozbalte**  <br>*Volitelné*|Určuje, které je třeba rozbalit obsah během vytváření bitové kopie Docker.|Boolean|
|**url**  <br>*Volitelné*|Adresa URL umístění Asset.|řetězec|


<a name="asyncoperationstate"></a>
### <a name="asyncoperationstate"></a>AsyncOperationState
Stav asynchronní operace.

*Typ*: výčtu (NotStarted, spuštění, zrušeno, bylo úspěšné, neúspěšné)


<a name="asyncoperationstatus"></a>
### <a name="asyncoperationstatus"></a>AsyncOperationStatus
Stav operace.


|Název|Popis|Schéma|
|---|---|---|
|**createdTime**  <br>*Volitelné*  <br>*jen pro čtení*|Asynchronní operace vytvoření čas (UTC).|řetězec (datum a čas)|
|**endTime**  <br>*Volitelné*  <br>*jen pro čtení*|Asynchronní operace koncový čas (UTC).|řetězec (datum a čas)|
|**Chyba**  <br>*Volitelné*||[ErrorResponse](#errorresponse)|
|**ID**  <br>*Volitelné*|ID asynchronní operace.|řetězec|
|**Typ operace**  <br>*Volitelné*|Typ asynchronní operace.|výčet (bitové kopie, služba)|
|**resourceLocation**  <br>*Volitelné*|Prostředek vytvořit nebo aktualizovat asynchronní operace.|řetězec|
|**Stav**  <br>*Volitelné*||[AsyncOperationState](#asyncoperationstate)|


<a name="authkeys"></a>
### <a name="authkeys"></a>AuthKeys
Ověřovací klíče pro službu.


|Název|Popis|Schéma|
|---|---|---|
|**primaryKey**  <br>*Volitelné*|Primární klíč.|řetězec|
|**secondaryKey**  <br>*Volitelné*|Sekundární klíč.|řetězec|


<a name="autoscaler"></a>
### <a name="autoscaler"></a>AutoScaler
Nastavení autoscaler.


|Název|Popis|Schéma|
|---|---|---|
|**autoscaleEnabled**  <br>*Volitelné*|Povolit nebo zakázat autoscaler.|Boolean|
|**maxReplicas**  <br>*Volitelné*|Maximální počet replik pod až škálování.  <br>**Minimální hodnota**: `1`|integer|
|**minReplicas**  <br>*Volitelné*|Minimální počet replik pod škálování dolů na.  <br>**Minimální hodnota**: `0`|integer|
|**refreshPeriodInSeconds**  <br>*Volitelné*|Aktualizace času automatického škálování aktivační událost.  <br>**Minimální hodnota**: `1`|integer|
|**targetUtilization**  <br>*Volitelné*|Procento využití, která aktivuje automatické škálování.  <br>**Minimální hodnota**: `0`  <br>**Maximální hodnota**: `100`|integer|


<a name="computeresource"></a>
### <a name="computeresource"></a>ComputeResource
Machine Learning výpočetní prostředky.


|Název|Popis|Schéma|
|---|---|---|
|**ID**  <br>*Volitelné*|ID prostředku.|řetězec|
|**type**  <br>*Volitelné*|Typ prostředku.|výčet (Cluster)|


<a name="containerresourcereservation"></a>
### <a name="containerresourcereservation"></a>ContainerResourceReservation
Konfigurace tak, aby vyhradil prostředky pro kontejner v clusteru.


|Název|Popis|Schéma|
|---|---|---|
|**cpu**  <br>*Volitelné*|Určuje rezervace procesoru. Formát pro Kubernetes: najdete v části [význam procesoru](https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/#meaning-of-cpu).|řetězec|
|**Paměť**  <br>*Volitelné*|Určuje paměť rezervace. Formát pro Kubernetes: najdete v části [význam paměti](https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/#meaning-of-memory).|řetězec|


<a name="deployment"></a>
### <a name="deployment"></a>Nasazení
Instance nasazení služby Azure Machine Learning.


|Název|Popis|Schéma|
|---|---|---|
|**CreatedAt**  <br>*Volitelné*  <br>*jen pro čtení*|Čas vytvoření nasazení (UTC).|řetězec (datum a čas)|
|**expiredAt**  <br>*Volitelné*  <br>*jen pro čtení*|Platnost nasazení čas (UTC).|řetězec (datum a čas)|
|**ID**  <br>*Volitelné*|ID nasazení.|řetězec|
|**imageId**  <br>*Volitelné*|ID obrázku přidružený toto nasazení.|řetězec|
|**serviceName**  <br>*Volitelné*|Název služby.|řetězec|
|**Stav**  <br>*Volitelné*|Aktuální stav nasazení.|řetězec|


<a name="deploymentlist"></a>
### <a name="deploymentlist"></a>DeploymentList
Pole objektů nasazení.

*Typ*: <[nasazení](#deployment)> pole


<a name="errordetail"></a>
### <a name="errordetail"></a>ErrorDetail
Model správy podrobnosti o chybě služby.


|Název|Popis|Schéma|
|---|---|---|
|**Kód**  <br>*Požadované*|Kód chyby.|řetězec|
|**Zpráva**  <br>*Požadované*|Chybová zpráva.|řetězec|


<a name="errorresponse"></a>
### <a name="errorresponse"></a>ErrorResponse
Objekt modelu správy služby chyby.


|Název|Popis|Schéma|
|---|---|---|
|**Kód**  <br>*Požadované*|Kód chyby.|řetězec|
|**Podrobnosti**  <br>*Volitelné*|Pole objektů podrobnosti o chybě.|<[ErrorDetail](#errordetail)> pole|
|**Zpráva**  <br>*Požadované*|Chybová zpráva.|řetězec|
|**statusCode**  <br>*Volitelné*|Stavový kód HTTP.|integer|


<a name="image"></a>
### <a name="image"></a>Image
Obrázek Azure Machine Learning.


|Název|Popis|Schéma|
|---|---|---|
|**computeResourceId**  <br>*Volitelné*|ID prostředí vytvořené v Machine Learning výpočetní prostředek.|řetězec|
|**createdTime**  <br>*Volitelné*|Čas vytvoření bitové kopie (UTC).|řetězec (datum a čas)|
|**creationState**  <br>*Volitelné*||[AsyncOperationState](#asyncoperationstate)|
|**Popis**  <br>*Volitelné*|Text popisu Image.|řetězec|
|**Chyba**  <br>*Volitelné*||[ErrorResponse](#errorresponse)|
|**ID**  <br>*Volitelné*|ID obrázku.|řetězec|
|**imageBuildLogUri**  <br>*Volitelné*|Identifikátor URI nahrané protokoly ze sestavení bitové kopie.|řetězec|
|**Vlastnost imageLocation**  <br>*Volitelné*|Azure řetězec umístění kontejneru registru pro vytvoření bitové kopie.|řetězec|
|**imageType**  <br>*Volitelné*||[ImageType](#imagetype)|
|**Manifest**  <br>*Volitelné*||[Manifest](#manifest)|
|**Jméno**  <br>*Volitelné*|Název bitové kopie.|řetězec|
|**Verze**  <br>*Volitelné*|Verze bitové kopie nastavit službou Management modelu.|integer|


<a name="imagerequest"></a>
### <a name="imagerequest"></a>imageRequest
Požadavek na vytvoření image Azure Machine Learning.


|Název|Popis|Schéma|
|---|---|---|
|**computeResourceId**  <br>*Požadované*|ID prostředí vytvořené v Machine Learning výpočetní prostředek.|řetězec|
|**Popis**  <br>*Volitelné*|Text popisu Image.|řetězec|
|**imageType**  <br>*Požadované*||[ImageType](#imagetype)|
|**ID manifestu**  <br>*Požadované*|ID manifestu, ze kterého se vytvoří bitovou kopii.|řetězec|
|**Jméno**  <br>*Požadované*|Název bitové kopie.|řetězec|


<a name="imagetype"></a>
### <a name="imagetype"></a>Typ obrázku
Určuje typ obrázku.

*Typ*: výčtu (Docker)


<a name="manifest"></a>
### <a name="manifest"></a>Manifest
Azure Machine Learning manifest.


|Název|Popis|Schéma|
|---|---|---|
|**Prostředky**  <br>*Požadované*|Seznam prostředků.|<[Asset](#asset)> pole|
|**createdTime**  <br>*Volitelné*  <br>*jen pro čtení*|Vytvoření manifestu čas (UTC).|řetězec (datum a čas)|
|**Popis**  <br>*Volitelné*|Manifest text popisu.|řetězec|
|**driverProgram**  <br>*Požadované*|Ovladač program manifestu.|řetězec|
|**ID**  <br>*Volitelné*|ID manifestu.|řetězec|
|**modelIds**  <br>*Volitelné*|Seznam ID modelu registrované modelů. Žádost se nezdaří, pokud nejsou registrované žádné zahrnuté modelů.|<string> Pole|
|**modelType**  <br>*Volitelné*|Určuje, že modely jsou již registrovaný ve službě správy modelu.|výčet (registrovaná)|
|**Jméno**  <br>*Požadované*|Název souboru manifestu.|řetězec|
|**TargetRuntime**  <br>*Požadované*||[TargetRuntime](#targetruntime)|
|**Verze**  <br>*Volitelné*  <br>*jen pro čtení*|Verze manifestu přiřazené službou modelu správy.|integer|
|**webserviceType**  <br>*Volitelné*|Určuje požadovaný typ webové služby, která bude vytvořena z manifestu.|výčet (v reálném čase)|


<a name="model"></a>
### <a name="model"></a>Model
Instance model Azure Machine Learning.


|Název|Popis|Schéma|
|---|---|---|
|**CreatedAt**  <br>*Volitelné*  <br>*jen pro čtení*|Model vytváření čas (UTC).|řetězec (datum a čas)|
|**Popis**  <br>*Volitelné*|Textový popis modelu.|řetězec|
|**ID**  <br>*Volitelné*  <br>*jen pro čtení*|ID modelu.|řetězec|
|**mimeType**  <br>*Požadované*|Typ MIME obsah modelu. Další informace o typu MIME najdete v tématu [seznam typů médií IANA](https://www.iana.org/assignments/media-types/media-types.xhtml).|řetězec|
|**Jméno**  <br>*Požadované*|Název modelu.|řetězec|
|**značek**  <br>*Volitelné*|Seznam značek modelu.|<string> Pole|
|**Rozbalte**  <br>*Volitelné*|Určuje, jestli je potřeba rozbalte modelu během vytváření bitové kopie Docker.|Boolean|
|**url**  <br>*Požadované*|Adresa URL modelu. Obvykle jsme uvést adresu URL sdílený přístupový podpis sem.|řetězec|
|**Verze**  <br>*Volitelné*  <br>*jen pro čtení*|Verze modelu přiřazené službou modelu správy.|integer|


<a name="modeldatacollection"></a>
### <a name="modeldatacollection"></a>ModelDataCollection
Informace o modelu dat kolekce.


|Název|Popis|Schéma|
|---|---|---|
|**eventHubEnabled**  <br>*Volitelné*|Povolte Centru událostí pro službu.|Boolean|
|**storageEnabled**  <br>*Volitelné*|Povolte úložiště pro službu.|Boolean|


<a name="paginatedimagelist"></a>
### <a name="paginatedimagelist"></a>PaginatedImageList
Seznam stránkované bitové kopie.


|Název|Popis|Schéma|
|---|---|---|
|**nextLink**  <br>*Volitelné*|Pokračování odkaz na další stránku výsledků v seznamu (absolutní identifikátor URI).|řetězec|
|**value**  <br>*Volitelné*|Pole objektů modelu.|<[Obrázek](#image)> pole|


<a name="paginatedmanifestlist"></a>
### <a name="paginatedmanifestlist"></a>PaginatedManifestList
Seznam stránkované manifesty.


|Název|Popis|Schéma|
|---|---|---|
|**nextLink**  <br>*Volitelné*|Pokračování odkaz na další stránku výsledků v seznamu (absolutní identifikátor URI).|řetězec|
|**value**  <br>*Volitelné*|Pole objektů manifestu.|<[Manifest](#manifest)> pole|


<a name="paginatedmodellist"></a>
### <a name="paginatedmodellist"></a>PaginatedModelList
Stránkované seznam modelů.


|Název|Popis|Schéma|
|---|---|---|
|**nextLink**  <br>*Volitelné*|Pokračování odkaz na další stránku výsledků v seznamu (absolutní identifikátor URI).|řetězec|
|**value**  <br>*Volitelné*|Pole objektů modelu.|<[Model](#model)> pole|


<a name="paginatedservicelist"></a>
### <a name="paginatedservicelist"></a>PaginatedServiceList
Stránkované seznam služeb.


|Název|Popis|Schéma|
|---|---|---|
|**nextLink**  <br>*Volitelné*|Pokračování odkaz na další stránku výsledků v seznamu (absolutní identifikátor URI).|řetězec|
|**value**  <br>*Volitelné*|Pole objektů služby.|<[ServiceResponse](#serviceresponse)> pole|


<a name="servicecreaterequest"></a>
### <a name="servicecreaterequest"></a>ServiceCreateRequest
Požadavek na vytvoření služby.


|Název|Popis|Schéma|
|---|---|---|
|**appInsightsEnabled**  <br>*Volitelné*|Povolte application insights pro služby.|Boolean|
|**autoScaler**  <br>*Volitelné*||[AutoScaler](#autoscaler)|
|**ComputeResource**  <br>*Požadované*||[ComputeResource](#computeresource)|
|**containerResourceReservation**  <br>*Volitelné*||[ContainerResourceReservation](#containerresourcereservation)|
|**Element DataCollection**  <br>*Volitelné*||[ModelDataCollection](#modeldatacollection)|
|**imageId**  <br>*Požadované*|Obrázek se vytvořit službu.|řetězec|
|**maxConcurrentRequestsPerContainer**  <br>*Volitelné*|Maximální počet souběžných požadavků.  <br>**Minimální hodnota**: `1`|integer|
|**Jméno**  <br>*Požadované*|Název služby.|řetězec|
|**numReplicas**  <br>*Volitelné*|Počet replik pod systémem kdykoli. Nelze zadat, zda je povoleno Autoscaler.  <br>**Minimální hodnota**: `0`|integer|


<a name="serviceregeneratekeyrequest"></a>
### <a name="serviceregeneratekeyrequest"></a>ServiceRegenerateKeyRequest
Požadavek na znovu vygenerovat klíč pro službu.


|Název|Popis|Schéma|
|---|---|---|
|**Typ_klíče.**  <br>*Volitelné*|Určuje, který klíč znovu vygenerovat.|výčet (primární, sekundární)|


<a name="serviceresponse"></a>
### <a name="serviceresponse"></a>ServiceResponse
Podrobný stav služby.


|Název|Popis|Schéma|
|---|---|---|
|**CreatedAt**  <br>*Volitelné*|Vytvoření služby čas (UTC).|řetězec (datum a čas)|
|**ID**  <br>*Volitelné*|ID služby.|řetězec|
|**Bitové kopie**  <br>*Volitelné*||[Image](#image)|
|**Manifest**  <br>*Volitelné*||[Manifest](#manifest)|
|**Modely**  <br>*Volitelné*|Seznam modelů.|<[Model](#model)> pole|
|**Jméno**  <br>*Volitelné*|Název služby.|řetězec|
|**scoringUri**  <br>*Volitelné*|Identifikátor URI pro vyhodnocování službu.|řetězec|
|**Stav**  <br>*Volitelné*||[AsyncOperationState](#asyncoperationstate)|
|**updatedAt**  <br>*Volitelné*|Poslední aktualizace čas (UTC).|řetězec (datum a čas)|
|**appInsightsEnabled**  <br>*Volitelné*|Povolte application insights pro služby.|Boolean|
|**autoScaler**  <br>*Volitelné*||[AutoScaler](#autoscaler)|
|**ComputeResource**  <br>*Požadované*||[ComputeResource](#computeresource)|
|**containerResourceReservation**  <br>*Volitelné*||[ContainerResourceReservation](#containerresourcereservation)|
|**Element DataCollection**  <br>*Volitelné*||[ModelDataCollection](#modeldatacollection)|
|**maxConcurrentRequestsPerContainer**  <br>*Volitelné*|Maximální počet souběžných požadavků.  <br>**Minimální hodnota**: `1`|integer|
|**numReplicas**  <br>*Volitelné*|Počet replik pod systémem kdykoli. Nelze zadat, zda je povoleno Autoscaler.  <br>**Minimální hodnota**: `0`|integer|
|**Chyba**  <br>*Volitelné*||[ErrorResponse](#errorresponse)|


<a name="serviceupdaterequest"></a>
### <a name="serviceupdaterequest"></a>ServiceUpdateRequest
Žádost o aktualizaci služby.


|Název|Popis|Schéma|
|---|---|---|
|**appInsightsEnabled**  <br>*Volitelné*|Povolte application insights pro služby.|Boolean|
|**autoScaler**  <br>*Volitelné*||[AutoScaler](#autoscaler)|
|**containerResourceReservation**  <br>*Volitelné*||[ContainerResourceReservation](#containerresourcereservation)|
|**Element DataCollection**  <br>*Volitelné*||[ModelDataCollection](#modeldatacollection)|
|**imageId**  <br>*Volitelné*|Obrázek se vytvořit službu.|řetězec|
|**maxConcurrentRequestsPerContainer**  <br>*Volitelné*|Maximální počet souběžných požadavků.  <br>**Minimální hodnota**: `1`|integer|
|**numReplicas**  <br>*Volitelné*|Počet replik pod systémem kdykoli. Nelze zadat, zda je povoleno Autoscaler.  <br>**Minimální hodnota**: `0`|integer|


<a name="targetruntime"></a>
### <a name="targetruntime"></a>TargetRuntime
Typ cílový modul runtime.


|Název|Popis|Schéma|
|---|---|---|
|**Vlastnosti**  <br>*Požadované*||< řetězec, řetězec > mapy|
|**runtimeType**  <br>*Požadované*|Určuje modulu runtime.|výčet (SparkPython, Python)|

