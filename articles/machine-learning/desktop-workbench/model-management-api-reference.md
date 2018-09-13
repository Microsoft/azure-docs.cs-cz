---
title: Vytvoření image Dockeru pro správu modelů ve službě Azure Machine Learning | Dokumentace Microsoftu
description: Tento článek popisuje kroky pro vytvoření image Dockeru pro webovou službu.
services: machine-learning
author: chhavib
ms.author: chhavib
manager: hjerez
editor: jasonwhowell
ms.reviewer: jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.devlang: na
ms.topic: article
ms.date: 09/20/2017
ms.openlocfilehash: c0f51e47038737d6aa743be718ad6b28c161c766
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "35643411"
---
# <a name="azure-machine-learning-model-management-account-api-reference"></a>Referenční informace k Azure Machine Learning Model správy účtu API

Informace o nastavení prostředí nasazení najdete v tématu [nastavení účtu správy modelů](deployment-setup-configuration.md).

Azure Machine Learning Model správy účtu API implementuje následující operace:

- Registrace modelu
- Vytváření manifestu
- Vytvoření image dockeru
- Při vytváření webové služby

Tato image můžete vytvořit webovou službu místně nebo na vzdálený cluster Azure Container Service nebo jiné prostředí podle vašeho výběru s podporou Dockeru.

## <a name="prerequisites"></a>Požadavky
Ujistěte se, že jste prošli kroky instalace v [instalace a vytvoření rychlý Start](../service/quickstart-installation.md) dokumentu.

Následující jsou požadovány, než budete pokračovat:
1. Zřizování účtu správy modelů
2. Vytváření prostředí pro nasazování a správu modelů
3. Model Machine Learning

### <a name="azure-ad-token"></a>Tokenu Azure AD
Pokud používáte rozhraní příkazového řádku Azure, přihlaste se pomocí `az login`. Rozhraní příkazového řádku používá ze souboru .azure tokenu Azure Active Directory (Azure AD). Pokud chcete použít rozhraní API, máte následující možnosti.

##### <a name="acquire-the-azure-ad-token-manually"></a>Získání tokenu Azure AD ručně
Můžete použít `az login` dostanete nejnovější token ze souboru .azure na domovský adresář.

##### <a name="acquire-the-azure-ad-token-programmatically"></a>Získání tokenu Azure AD prostřednictvím kódu programu
```
az ad sp create-for-rbac --scopes /subscriptions/<SubscriptionId>/resourcegroups/<ResourceGroupName> --role Contributor --years <length of time> --name <MyservicePrincipalContributor>
```
Jakmile vytvoříte instanční objekt služby, uložte si výstup. Nyní, který můžete použít k získání tokenu z Azure AD:

```cs
 private static async Task<string> AcquireTokenAsync(string clientId, string password, string authority, string resource)
{
        var creds = new ClientCredential(clientId, password);
        var context = new AuthenticationContext(authority);
        var token = await context.AcquireTokenAsync(resource, creds).ConfigureAwait(false);
        return token.AccessToken;
}
```
Token, který je umístěn v hlavičce autorizace pro volání rozhraní API.


## <a name="register-a-model"></a>Zaregistrujte model

Krok registrace modelu zaregistruje váš model strojového učení, který jste vytvořili účet služby Správa modelů Azure. Díky této registraci může sledování modelů a jejich verze, které jsou přiřazeny v době registrace. Uživatel zadá název modelu. Následující registraci modelů pod stejným názvem vygeneruje nové verze a ID.

### <a name="request"></a>Žádost

| Metoda | Identifikátor URI žádosti |
|------------|------------|
| POST |  / webové rozhraní API/subscriptions / {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} / modely 
 |
### <a name="description"></a>Popis
Zaregistruje modelu.

### <a name="parameters"></a>Parametry
| Název | Nachází se | Popis | Požaduje se | Schéma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID předplatného Azure. | Ano | řetězec |
| resourceGroupName | path | Název skupiny prostředků, ve kterém je umístěný účet správy modelů. | Ano | řetězec |
| název účtu | path | Název účtu správy modelů. | Ano | řetězec |
| verze API-version | query | Verze poskytovatele prostředků Microsoft.Machine.Learning rozhraní API pro použití. | Ano | řetězec |
| Autorizace | záhlaví | Autorizační token. Ji by měl vypadat "Nosiče XXXXXX." | Ano | řetězec |
| model | hlavní část | Datová část, která se použije k registraci modelu. | Ano | [Model](#model) |


### <a name="responses"></a>Odezvy
| Kód | Popis | Schéma |
|--------------------|--------------------|--------------------|
| 200 | OK. Model registrace byla úspěšná. | [Model](#model) |
| default | Chybová odpověď, která popisuje, proč operace se nezdařila. | [ErrorResponse](#errorresponse) |

## <a name="query-the-list-of-models-in-an-account"></a>Dotaz seznam modely v rámci účtu
### <a name="request"></a>Žádost
| Metoda | Identifikátor URI žádosti |
|------------|------------|
| GET |  / webové rozhraní API/subscriptions / {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} / modely 
 |
### <a name="description"></a>Popis
Dotazuje seznamu modelů v rámci účtu. Můžete filtrovat seznam výsledků podle značky a názvu. Pokud není předán žádný filtr, výsledný seznam obsahuje všechny modely v rámci účtu. Paginated vráceném seznamu a počet položek na každé stránce je volitelný parametr.

### <a name="parameters"></a>Parametry
| Název | Nachází se | Popis | Požaduje se | Schéma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID předplatného Azure. | Ano | řetězec |
| resourceGroupName | path | Název skupiny prostředků, ve kterém je umístěný účet správy modelů. | Ano | řetězec |
| název účtu | path | Název účtu správy modelů. | Ano | řetězec |
| verze API-version | query | Verze poskytovatele prostředků Microsoft.Machine.Learning rozhraní API pro použití. | Ano | řetězec |
| Autorizace | záhlaví | Autorizační token. Ji by měl vypadat "Nosiče XXXXXX." | Ano | řetězec |
| jméno | query | Název objektu. | Ne | řetězec |
| značka | query | Model značky. | Ne | řetězec |
| count | query | Počet položek k načtení stránky. | Ne | řetězec |
| $skipToken | query | Token pro pokračování k načtení další stránky. | Ne | řetězec |

### <a name="responses"></a>Odezvy
| Kód | Popis | Schéma |
|--------------------|--------------------|--------------------|
| 200 | Úspěch | [PaginatedModelList](#paginatedmodellist) |
| default | Chybová odpověď, která popisuje, proč operace se nezdařila. | [ErrorResponse](#errorresponse) |

## <a name="get-model-details"></a>Získejte podrobné informace o modelu
### <a name="request"></a>Žádost
| Metoda | Identifikátor URI žádosti |
|------------|------------|
| GET |  / webové rozhraní API/subscriptions / {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} /models/ {id}  
 |

### <a name="description"></a>Popis
Získá model podle ID.

### <a name="parameters"></a>Parametry
| Název | Nachází se | Popis | Požaduje se | Schéma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID předplatného Azure. | Ano | řetězec |
| resourceGroupName | path | Název skupiny prostředků, ve kterém je umístěný účet správy modelů. | Ano | řetězec |
| název účtu | path | Název účtu správy modelů. | Ano | řetězec |
| id | path | ID objektu. | Ano | řetězec |
| verze API-version | query | Verze poskytovatele prostředků Microsoft.Machine.Learning rozhraní API pro použití. | Ano | řetězec |
| Autorizace | záhlaví | Autorizační token. Ji by měl vypadat "Nosiče XXXXXX." | Ano | řetězec |

### <a name="responses"></a>Odezvy
| Kód | Popis | Schéma |
|--------------------|--------------------|--------------------|
| 200 | Úspěch | [Model](#model) |
| default | Chybová odpověď, která popisuje, proč operace se nezdařila. | [ErrorResponse](#errorresponse) |

## <a name="register-a-manifest-with-the-registered-model-and-all-dependencies"></a>Zaregistrovat registrovanému modelu a všechny závislosti manifestu

### <a name="request"></a>Žádost
| Metoda | Identifikátor URI žádosti |
|------------|------------|
| POST |  / webové rozhraní API/subscriptions / {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} / manifestů | 

### <a name="description"></a>Popis
Registruje manifestu registrovanému modelu a jeho závislosti.

### <a name="parameters"></a>Parametry
| Název | Nachází se | Popis | Požaduje se | Schéma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID předplatného Azure. | Ano | řetězec |
| resourceGroupName | path | Název skupiny prostředků, ve kterém je umístěný účet správy modelů. | Ano | řetězec |
| název účtu | path | Název účtu správy modelů. | Ano | řetězec |
| verze API-version | query | Verze poskytovatele prostředků Microsoft.Machine.Learning rozhraní API pro použití. | Ano | řetězec |
| Autorizace | záhlaví | Autorizační token. Ji by měl vypadat "Nosiče XXXXXX." | Ano | řetězec |
| manifestRequest | hlavní část | Datová část, která se použije k registraci manifestu. | Ano | [Manifest](#manifest) |

### <a name="responses"></a>Odezvy
| Kód | Popis | Schéma |
|--------------------|--------------------|--------------------|
| 200 | Manifestu registrace byla úspěšná. | [Manifest](#manifest) |
| default | Chybová odpověď, která popisuje, proč operace se nezdařila. | [ErrorResponse](#errorresponse) |

## <a name="query-the-list-of-manifests-in-an-account"></a>Dotaz seznamu manifestů v účtu služby

### <a name="request"></a>Žádost
| Metoda | Identifikátor URI žádosti |
|------------|------------|
| GET |  / webové rozhraní API/subscriptions / {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} / manifestů | 

### <a name="description"></a>Popis
Dotazuje seznamu manifestů v rámci účtu. Můžete filtrovat seznam výsledků podle ID modelu a název manifestu. Pokud není předán žádný filtr, výsledný seznam obsahuje všechny manifesty v účtu. Paginated vráceném seznamu a počet položek na každé stránce je volitelný parametr.

### <a name="parameters"></a>Parametry
| Název | Nachází se | Popis | Požaduje se | Schéma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID předplatného Azure. | Ano | řetězec |
| resourceGroupName | path | Název skupiny prostředků, ve kterém je umístěný účet správy modelů. | Ano | řetězec |
| název účtu | path | Název účtu správy modelů. | Ano | řetězec |
| verze API-version | query | Verze poskytovatele prostředků Microsoft.Machine.Learning rozhraní API pro použití. | Ano | řetězec |
| Autorizace | záhlaví | Autorizační token. Ji by měl vypadat "Nosiče XXXXXX." | Ano | řetězec |
| modelId | query | ID modelu. | Ne | řetězec |
| manifestName | query | Název souboru manifestu. | Ne | řetězec |
| count | query | Počet položek k načtení stránky. | Ne | řetězec |
| $skipToken | query | Token pro pokračování k načtení další stránky. | Ne | řetězec |

### <a name="responses"></a>Odezvy
| Kód | Popis | Schéma |
|--------------------|--------------------|--------------------|
| 200 | Úspěch | [PaginatedManifestList](#paginatedmanifestlist) |
| default | Chybová odpověď, která popisuje, proč operace se nezdařila. | [ErrorResponse](#errorresponse) |

## <a name="get-manifest-details"></a>Získat podrobnosti o manifestu

### <a name="request"></a>Žádost
| Metoda | Identifikátor URI žádosti |
|------------|------------|
| GET |  / webové rozhraní API/subscriptions / {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} /manifests/ {id} | 

### <a name="description"></a>Popis
Získá manifest podle ID.

### <a name="parameters"></a>Parametry
| Název | Nachází se | Popis | Požaduje se | Schéma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID předplatného Azure. | Ano | řetězec |
| resourceGroupName | path | Název skupiny prostředků, ve kterém je umístěný účet správy modelů. | Ano | řetězec |
| název účtu | path | Název účtu správy modelů. | Ano | řetězec |
| id | path | ID objektu. | Ano | řetězec |
| verze API-version | query | Verze poskytovatele prostředků Microsoft.Machine.Learning rozhraní API pro použití. | Ano | řetězec |
| Autorizace | záhlaví | Autorizační token. Ji by měl vypadat "Nosiče XXXXXX." | Ano | řetězec |

### <a name="responses"></a>Odezvy
| Kód | Popis | Schéma |
|--------------------|--------------------|--------------------|
| 200 | Úspěch | [Manifest](#manifest) |
| default | Chybová odpověď, která popisuje, proč operace se nezdařila. | [ErrorResponse](#errorresponse) |

## <a name="create-an-image"></a>Vytvoření image

### <a name="request"></a>Žádost
| Metoda | Identifikátor URI žádosti |
|------------|------------|
| POST |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images | 

### <a name="description"></a>Popis
Vytvoří bitovou kopii jako image Dockeru ve službě Azure Container Registry.

### <a name="parameters"></a>Parametry
| Název | Nachází se | Popis | Požaduje se | Schéma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID předplatného Azure. | Ano | řetězec |
| resourceGroupName | path | Název skupiny prostředků, ve kterém je umístěný účet správy modelů. | Ano | řetězec |
| název účtu | path | Název účtu správy modelů. | Ano | řetězec |
| verze API-version | query | Verze poskytovatele prostředků Microsoft.Machine.Learning rozhraní API pro použití. | Ano | řetězec |
| Autorizace | záhlaví | Autorizační token. Ji by měl vypadat "Nosiče XXXXXX." | Ano | řetězec |
| imageRequest | hlavní část | Datová část, která se používá k vytvoření image. | Ano | [ImageRequest](#imagerequest) |

### <a name="responses"></a>Odezvy
| Kód | Popis | Záhlaví | Schéma |
|--------------------|--------------------|--------------------|--------------------|
| 202 | Adresa URL umístění asynchronní operace. Volání GET se zobrazí stav úlohy vytvoření bitové kopie. | Operace umístění |
| default | Chybová odpověď, která popisuje, proč operace se nezdařila. | [ErrorResponse](#errorresponse) |

## <a name="query-the-list-of-images-in-an-account"></a>Dotaz seznam imagí v účtu služby

### <a name="request"></a>Žádost
| Metoda | Identifikátor URI žádosti |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images | 

### <a name="description"></a>Popis
Vyhledá seznam imagí v rámci účtu. Můžete filtrovat seznam výsledků podle ID manifestu a názvu. Pokud není předán žádný filtr, výsledný seznam obsahuje všechny Image v rámci účtu. Paginated vráceném seznamu a počet položek na každé stránce je volitelný parametr.

### <a name="parameters"></a>Parametry
| Název | Nachází se | Popis | Požaduje se | Schéma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID předplatného Azure. | Ano | řetězec |
| resourceGroupName | path | Název skupiny prostředků, ve kterém je umístěný účet správy modelů. | Ano | řetězec |
| název účtu | path | Název účtu správy modelů. | Ano | řetězec |
| verze API-version | query | Verze poskytovatele prostředků Microsoft.Machine.Learning rozhraní API pro použití. | Ano | řetězec |
| Autorizace | záhlaví | Autorizační token. Ji by měl vypadat "Nosiče XXXXXX." | Ano | řetězec |
| ID manifestu | query | ID manifestu. | Ne | řetězec |
| manifestName | query | Název souboru manifestu. | Ne | řetězec |
| count | query | Počet položek k načtení stránky. | Ne | řetězec |
| $skipToken | query | Token pro pokračování k načtení další stránky. | Ne | řetězec |

### <a name="responses"></a>Odezvy
| Kód | Popis | Schéma |
|--------------------|--------------------|--------------------|
| 200 | Úspěch | [PaginatedImageList](#paginatedimagelist) |
| default | Chybová odpověď, která popisuje, proč operace se nezdařila. | [ErrorResponse](#errorresponse) |

## <a name="get-image-details"></a>Získat podrobnosti image

### <a name="request"></a>Žádost
| Metoda | Identifikátor URI žádosti |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images/{id} | 

### <a name="description"></a>Popis
Získá obrázek podle ID.

### <a name="parameters"></a>Parametry
| Název | Nachází se | Popis | Požaduje se | Schéma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID předplatného Azure. | Ano | řetězec |
| resourceGroupName | path | Název skupiny prostředků, ve kterém je umístěný účet správy modelů. | Ano | řetězec |
| název účtu | path | Název účtu správy modelů. | Ano | řetězec |
| id | path | ID bitové kopie. | Ano | řetězec |
| verze API-version | query | Verze poskytovatele prostředků Microsoft.Machine.Learning rozhraní API pro použití. | Ano | řetězec |
| Autorizace | záhlaví | Autorizační token. Ji by měl vypadat "Nosiče XXXXXX." | Ano | řetězec |

### <a name="responses"></a>Odezvy
| Kód | Popis | Schéma |
|--------------------|--------------------|--------------------|
| 200 | Úspěch | [Image](#image) |
| default | Chybová odpověď, která popisuje, proč operace se nezdařila. | [ErrorResponse](#errorresponse) |


## <a name="create-a-service"></a>Vytvoření služby

### <a name="request"></a>Žádost
| Metoda | Identifikátor URI žádosti |
|------------|------------|
| POST |  / webové rozhraní API/subscriptions / {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} / služby | 

### <a name="description"></a>Popis
Vytvoří službu z image.

### <a name="parameters"></a>Parametry
| Název | Nachází se | Popis | Požaduje se | Schéma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID předplatného Azure. | Ano | řetězec |
| resourceGroupName | path | Název skupiny prostředků, ve kterém je umístěný účet správy modelů. | Ano | řetězec |
| název účtu | path | Název účtu správy modelů. | Ano | řetězec |
| verze API-version | query | Verze poskytovatele prostředků Microsoft.Machine.Learning rozhraní API pro použití. | Ano | řetězec |
| Autorizace | záhlaví | Autorizační token. Ji by měl vypadat "Nosiče XXXXXX." | Ano | řetězec |
| serviceRequest | hlavní část | Datová část, která se používá k vytvoření služby. | Ano | [ServiceCreateRequest](#servicecreaterequest) |

### <a name="responses"></a>Odezvy
| Kód | Popis | Záhlaví | Schéma |
|--------------------|--------------------|--------------------|--------------------|
| 202 | Adresa URL umístění asynchronní operace. Volání GET se zobrazí stav úlohy vytváření služby. | Operace umístění |
| 409 | Služba se zadaným názvem už existuje. |
| default | Chybová odpověď, která popisuje, proč operace se nezdařila. | [ErrorResponse](#errorresponse) |

## <a name="query-the-list-of-services-in-an-account"></a>Dotaz na seznam služeb v rámci účtu

### <a name="request"></a>Žádost
| Metoda | Identifikátor URI žádosti |
|------------|------------|
| GET |  / webové rozhraní API/subscriptions / {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} / služby | 

### <a name="description"></a>Popis
Vyhledá v seznamu služeb v rámci účtu. Můžete filtrovat seznam výsledků název modelu/ID, název nebo ID manifestu, ID bitové kopie, název služby nebo ID Machine Learning výpočetních prostředků. Pokud není předán žádný filtr, výsledný seznam obsahuje všechny služby v rámci účtu. Paginated vráceném seznamu a počet položek na každé stránce je volitelný parametr.

### <a name="parameters"></a>Parametry
| Název | Nachází se | Popis | Požaduje se | Schéma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID předplatného Azure. | Ano | řetězec |
| resourceGroupName | path | Název skupiny prostředků, ve kterém je umístěný účet správy modelů. | Ano | řetězec |
| název účtu | path | Název účtu správy modelů. | Ano | řetězec |
| verze API-version | query | Verze poskytovatele prostředků Microsoft.Machine.Learning rozhraní API pro použití. | Ano | řetězec |
| Autorizace | záhlaví | Autorizační token. Ji by měl vypadat "Nosiče XXXXXX." | Ano | řetězec |
| serviceName | query | Název služby. | Ne | řetězec |
| modelId | query | Název modelu. | Ne | řetězec |
| modelName | query | ID modelu. | Ne | řetězec |
| ID manifestu | query | ID manifestu. | Ne | řetězec |
| manifestName | query | Název souboru manifestu. | Ne | řetězec |
| ID obrázku | query | ID bitové kopie. | Ne | řetězec |
| computeResourceId | query | ID Machine Learning výpočetního prostředku. | Ne | řetězec |
| count | query | Počet položek k načtení stránky. | Ne | řetězec |
| $skipToken | query | Token pro pokračování k načtení další stránky. | Ne | řetězec |

### <a name="responses"></a>Odezvy
| Kód | Popis | Schéma |
|--------------------|--------------------|--------------------|
| 200 | Úspěch | [PaginatedServiceList](#paginatedservicelist) |
| default | Chybová odpověď, která popisuje, proč operace se nezdařila. | [ErrorResponse](#errorresponse) |

## <a name="get-service-details"></a>Získat podrobnosti služby

### <a name="request"></a>Žádost
| Metoda | Identifikátor URI žádosti |
|------------|------------|
| GET |  / webové rozhraní API/subscriptions / {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} /services/ {id} | 

### <a name="description"></a>Popis
Získá službu podle ID.

### <a name="parameters"></a>Parametry
| Název | Nachází se | Popis | Požaduje se | Schéma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID předplatného Azure. | Ano | řetězec |
| resourceGroupName | path | Název skupiny prostředků, ve kterém je umístěný účet správy modelů. | Ano | řetězec |
| název účtu | path | Název účtu správy modelů. | Ano | řetězec |
| id | path | ID objektu. | Ano | řetězec |
| verze API-version | query | Verze poskytovatele prostředků Microsoft.Machine.Learning rozhraní API pro použití. | Ano | řetězec |
| Autorizace | záhlaví | Autorizační token. Ji by měl vypadat "Nosiče XXXXXX." | Ano | řetězec |

### <a name="responses"></a>Odezvy
| Kód | Popis | Schéma |
|--------------------|--------------------|--------------------|
| 200 | Úspěch | [ServiceResponse](#serviceresponse) |
| default | Chybová odpověď, která popisuje, proč operace se nezdařila. | [ErrorResponse](#errorresponse)

## <a name="update-a-service"></a>Aktualizace služby

### <a name="request"></a>Žádost
| Metoda | Identifikátor URI žádosti |
|------------|------------|
| PUT |  / webové rozhraní API/subscriptions / {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} /services/ {id} | 

### <a name="description"></a>Popis
Aktualizuje existující služby.

### <a name="parameters"></a>Parametry
| Název | Nachází se | Popis | Požaduje se | Schéma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID předplatného Azure. | Ano | řetězec |
| resourceGroupName | path | Název skupiny prostředků, ve kterém je umístěný účet správy modelů. | Ano | řetězec |
| název účtu | path | Název účtu správy modelů. | Ano | řetězec |
| id | path | ID objektu. | Ano | řetězec |
| verze API-version | query | Verze poskytovatele prostředků Microsoft.Machine.Learning rozhraní API pro použití. | Ano | řetězec |
| Autorizace | záhlaví | Autorizační token. Ji by měl vypadat "Nosiče XXXXXX." | Ano | řetězec |
| serviceUpdateRequest | hlavní část | Datová část, která se používá k aktualizaci existující služby. | Ano |  [ServiceUpdateRequest](#serviceupdaterequest) |

### <a name="responses"></a>Odezvy
| Kód | Popis | Záhlaví | Schéma |
|--------------------|--------------------|--------------------|--------------------|
| 202 | Adresa URL umístění asynchronní operace. Volání GET se zobrazí stav úlohy aktualizace služby. | Operace umístění |
| 404 | Služba se zadaným ID neexistuje. |
| default | Chybová odpověď, která popisuje, proč operace se nezdařila. | [ErrorResponse](#errorresponse)

## <a name="delete-a-service"></a>Odstranit službu

### <a name="request"></a>Žádost
| Metoda | Identifikátor URI žádosti |
|------------|------------|
| DELETE |  / webové rozhraní API/subscriptions / {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} /services/ {id} | 

### <a name="description"></a>Popis
Služba odstraní.

### <a name="parameters"></a>Parametry
| Název | Nachází se | Popis | Požaduje se | Schéma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID předplatného Azure. | Ano | řetězec |
| resourceGroupName | path | Název skupiny prostředků, ve kterém je umístěný účet správy modelů. | Ano | řetězec |
| název účtu | path | Název účtu správy modelů. | Ano | řetězec |
| id | path | ID objektu. | Ano | řetězec |
| verze API-version | query | Verze poskytovatele prostředků Microsoft.Machine.Learning rozhraní API pro použití. | Ano | řetězec |
| Autorizace | záhlaví | Autorizační token. Ji by měl vypadat "Nosiče XXXXXX." | Ano | řetězec |

### <a name="responses"></a>Odezvy
| Kód | Popis | Schéma |
|--------------------|--------------------|--------------------|
| 200 | Úspěch |  |
| 204 | Služba se zadaným ID neexistuje. |
| default | Chybová odpověď, která popisuje, proč operace se nezdařila. | [ErrorResponse](#errorresponse)

## <a name="get-service-keys"></a>Získat klíče služby

### <a name="request"></a>Žádost
| Metoda | Identifikátor URI žádosti |
|------------|------------|
| GET |  / webové rozhraní API/subscriptions / {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} /services/ {id} / klíče | 

### <a name="description"></a>Popis
Získá klíče služby.

### <a name="parameters"></a>Parametry
| Název | Nachází se | Popis | Požaduje se | Schéma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID předplatného Azure. | Ano | řetězec |
| resourceGroupName | path | Název skupiny prostředků, ve kterém je umístěný účet správy modelů. | Ano | řetězec |
| název účtu | path | Název účtu správy modelů. | Ano | řetězec |
| id | path | ID služby. | Ano | řetězec |
| verze API-version | query | Verze poskytovatele prostředků Microsoft.Machine.Learning rozhraní API pro použití. | Ano | řetězec |
| Autorizace | záhlaví | Autorizační token. Ji by měl vypadat "Nosiče XXXXXX." | Ano | řetězec |

### <a name="responses"></a>Odezvy
| Kód | Popis | Schéma |
|--------------------|--------------------|--------------------|
| 200 | Úspěch | [AuthKeys](#authkeys)
| default | Chybová odpověď, která popisuje, proč operace se nezdařila. | [ErrorResponse](#errorresponse)

## <a name="regenerate-service-keys"></a>Znovu vygenerovat klíče služby

### <a name="request"></a>Žádost
| Metoda | Identifikátor URI žádosti |
|------------|------------|
| POST |  / webové rozhraní API/subscriptions / {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} /services/ {id} / znovu vygenerovat klíče | 

### <a name="description"></a>Popis
Obnoví klíče služby a vrátí je.

### <a name="parameters"></a>Parametry
| Název | Nachází se | Popis | Požaduje se | Schéma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID předplatného Azure. | Ano | řetězec |
| resourceGroupName | path | Název skupiny prostředků, ve kterém je umístěný účet správy modelů. | Ano | řetězec |
| název účtu | path | Název účtu správy modelů. | Ano | řetězec |
| id | path | ID služby. | Ano | řetězec |
| verze API-version | query | Verze poskytovatele prostředků Microsoft.Machine.Learning rozhraní API pro použití. | Ano | řetězec |
| Autorizace | záhlaví | Autorizační token. Ji by měl vypadat "Nosiče XXXXXX." | Ano | řetězec |
| regenerateKeyRequest | hlavní část | Datová část, která se používá k aktualizaci existující služby. | Ano | [ServiceRegenerateKeyRequest](#serviceregeneratekeyrequest) |

### <a name="responses"></a>Odezvy
| Kód | Popis | Schéma |
|--------------------|--------------------|--------------------|
| 200 | Úspěch | [AuthKeys](#authkeys)
| default | Chybová odpověď, která popisuje, proč operace se nezdařila. | [ErrorResponse](#errorresponse)

## <a name="query-the-list-of-deployments-in-an-account"></a>Dotaz seznam nasazení v rámci účtu

### <a name="request"></a>Žádost
| Metoda | Identifikátor URI žádosti |
|------------|------------|
| GET |  / webové rozhraní API/subscriptions / {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} / nasazení | 

### <a name="description"></a>Popis
Vyhledá seznam nasazení v rámci účtu. Můžete filtrovat seznam výsledků podle ID služby, který vrátí pouze nasazení, které jsou vytvořené pro konkrétní službu. Pokud není předán žádný filtr, výsledný seznam obsahuje všechna nasazení v rámci účtu.

### <a name="parameters"></a>Parametry
| Název | Nachází se | Popis | Požaduje se | Schéma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID předplatného Azure. | Ano | řetězec |
| resourceGroupName | path | Název skupiny prostředků, ve kterém je umístěný účet správy modelů. | Ano | řetězec |
| název účtu | path | Název účtu správy modelů. | Ano | řetězec |
| verze API-version | query | Verze poskytovatele prostředků Microsoft.Machine.Learning rozhraní API pro použití. | Ano | řetězec |
| Autorizace | záhlaví | Autorizační token. Ji by měl vypadat "Nosiče XXXXXX." | Ano | řetězec |
| ID služby | query | ID služby. | Ne | řetězec |

### <a name="responses"></a>Odezvy
| Kód | Popis | Schéma |
|--------------------|--------------------|--------------------|
| 200 | Úspěch | [DeploymentList](#deploymentlist) |
| default | Chybová odpověď, která popisuje, proč operace se nezdařila. | [ErrorResponse](#errorresponse)

## <a name="get-deployment-details"></a>Získat podrobnosti o nasazení

### <a name="request"></a>Žádost
| Metoda | Identifikátor URI žádosti |
|------------|------------|
| GET |  / webové rozhraní API/subscriptions / {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} /deployments/ {id} | 

### <a name="description"></a>Popis
Získá nasazení podle ID.

### <a name="parameters"></a>Parametry
| Název | Nachází se | Popis | Požaduje se | Schéma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID předplatného Azure. | Ano | řetězec |
| resourceGroupName | path | Název skupiny prostředků, ve kterém je umístěný účet správy modelů. | Ano | řetězec |
| název účtu | path | Název účtu správy modelů. | Ano | řetězec |
| id | path | ID nasazení. | Ano | řetězec |
| verze API-version | query | Verze poskytovatele prostředků Microsoft.Machine.Learning rozhraní API pro použití. | Ano | řetězec |
| Autorizace | záhlaví | Autorizační token. Ji by měl vypadat "Nosiče XXXXXX." | Ano | řetězec |

### <a name="responses"></a>Odezvy
| Kód | Popis | Schéma |
|--------------------|--------------------|--------------------|
| 200 | Úspěch | [Nasazení](#deployment) |
| default | Chybová odpověď, která popisuje, proč operace se nezdařila. | [ErrorResponse](#errorresponse)

## <a name="get-operation-details"></a>Získat podrobnosti o operaci

### <a name="request"></a>Žádost
| Metoda | Identifikátor URI žádosti |
|------------|------------|
| GET |  / webové rozhraní API/subscriptions / {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} /operations/ {id} | 

### <a name="description"></a>Popis
Umožňuje získat stav asynchronní operace podle ID operace.

### <a name="parameters"></a>Parametry
| Název | Nachází se | Popis | Požaduje se | Schéma
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID předplatného Azure. | Ano | řetězec |
| resourceGroupName | path | Název skupiny prostředků, ve kterém je umístěný účet správy modelů. | Ano | řetězec |
| název účtu | path | Název účtu správy modelů. | Ano | řetězec |
| id | path | ID operace. | Ano | řetězec |
| verze API-version | query | Verze poskytovatele prostředků Microsoft.Machine.Learning rozhraní API pro použití. | Ano | řetězec |
| Autorizace | záhlaví | Autorizační token. Ji by měl vypadat "Nosiče XXXXXX." | Ano | řetězec |

### <a name="responses"></a>Odezvy
| Kód | Popis | Schéma |
|--------------------|--------------------|--------------------|
| 200 | Úspěch | [Stav OperationStatus](#asyncoperationstatus) |
| default | Chybová odpověď, která popisuje, proč operace se nezdařila. | [ErrorResponse](#errorresponse)



<a name="definitions"></a>
## <a name="definitions"></a>Definice

<a name="asset"></a>
### <a name="asset"></a>Prostředek
Objekt asset, který bude potřeba při vytvoření image Dockeru.


|Název|Popis|Schéma|
|---|---|---|
|**ID**  <br>*Volitelné*|ID prostředku.|řetězec|
|**mimeType**  <br>*Volitelné*|Typ MIME obsahu modelu. Další informace o typu MIME, najdete v článku [seznam typů médií IANA](https://www.iana.org/assignments/media-types/media-types.xhtml).|řetězec|
|**Rozbalit.**  <br>*Volitelné*|Určuje, které je třeba rozbalit obsah při vytvoření image Dockeru.|Boolean|
|**url**  <br>*Volitelné*|Adresa URL umístění prostředku.|řetězec|


<a name="asyncoperationstate"></a>
### <a name="asyncoperationstate"></a>AsyncOperationState
Stav asynchronní operace.

*Typ*: výčtu (NotStarted, spuštěno, byla zrušena, úspěšné, neúspěšné)


<a name="asyncoperationstatus"></a>
### <a name="asyncoperationstatus"></a>AsyncOperationStatus
Stav operace.


|Název|Popis|Schéma|
|---|---|---|
|**createdTime**  <br>*Volitelné*  <br>*jen pro čtení*|Asynchronní operace čas vytvoření (UTC).|řetězec (datum a čas)|
|**endTime**  <br>*Volitelné*  <br>*jen pro čtení*|Asynchronní operace koncový čas (UTC).|řetězec (datum a čas)|
|**Chyba**  <br>*Volitelné*||[ErrorResponse](#errorresponse)|
|**ID**  <br>*Volitelné*|ID asynchronní operace.|řetězec|
|**Typ operace**  <br>*Volitelné*|Typ asynchronní operace.|výčet (Image, Service)|
|**resourceLocation**  <br>*Volitelné*|Prostředek vytvořen nebo aktualizován asynchronní operace.|řetězec|
|**Stav**  <br>*Volitelné*||[AsyncOperationState](#asyncoperationstate)|


<a name="authkeys"></a>
### <a name="authkeys"></a>AuthKeys
Ověřovací klíče pro službu.


|Název|Popis|Schéma|
|---|---|---|
|**primaryKey**  <br>*Volitelné*|Primární klíč.|řetězec|
|**secondaryKey**  <br>*Volitelné*|Sekundární klíč.|řetězec|


<a name="autoscaler"></a>
### <a name="autoscaler"></a>Automatického škálování
Nastavení automatického škálování.


|Název|Popis|Schéma|
|---|---|---|
|**autoscaleEnabled**  <br>*Volitelné*|Povolení nebo zakázání automatického škálování.|Boolean|
|**maxReplicas**  <br>*Volitelné*|Maximální počet replik podů až škálování.  <br>**Minimální hodnota**: `1`|integer|
|**minReplicas**  <br>*Volitelné*|Minimální počet replik podů škálovat dolů na.  <br>**Minimální hodnota**: `0`|integer|
|**refreshPeriodInSeconds**  <br>*Volitelné*|Čas aktivační události automatického škálování aktualizace.  <br>**Minimální hodnota**: `1`|integer|
|**targetUtilization**  <br>*Volitelné*|Procento využití, která spustí automatické škálování.  <br>**Minimální hodnota**: `0`  <br>**Maximální hodnota**: `100`|integer|


<a name="computeresource"></a>
### <a name="computeresource"></a>computeResource
Výpočetních prostředků Machine Learning.


|Název|Popis|Schéma|
|---|---|---|
|**ID**  <br>*Volitelné*|ID prostředku.|řetězec|
|**type**  <br>*Volitelné*|Typ prostředku.|výčet (Cluster)|


<a name="containerresourcereservation"></a>
### <a name="containerresourcereservation"></a>ContainerResourceReservation
Konfigurace rezervovat prostředky pro kontejner v clusteru.


|Název|Popis|Schéma|
|---|---|---|
|**cpu**  <br>*Volitelné*|Určuje procesor rezervaci. Formát pro Kubernetes: viz [význam procesoru](https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/#meaning-of-cpu).|řetězec|
|**Paměť**  <br>*Volitelné*|Určuje rezervace paměti. Formát pro Kubernetes: viz [význam paměti](https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/#meaning-of-memory).|řetězec|


<a name="deployment"></a>
### <a name="deployment"></a>Nasazení
Instance nasazení služby Azure Machine Learning.


|Název|Popis|Schéma|
|---|---|---|
|**createdAt**  <br>*Volitelné*  <br>*jen pro čtení*|Nasazení čas vytvoření (UTC).|řetězec (datum a čas)|
|**expiredAt**  <br>*Volitelné*  <br>*jen pro čtení*|Vypršela platnost nasazování čas (UTC).|řetězec (datum a čas)|
|**ID**  <br>*Volitelné*|ID nasazení.|řetězec|
|**imageId**  <br>*Volitelné*|ID bitové kopie spojené s tímto nasazením.|řetězec|
|**serviceName**  <br>*Volitelné*|Název služby.|řetězec|
|**Stav**  <br>*Volitelné*|Aktuální stav nasazení.|řetězec|


<a name="deploymentlist"></a>
### <a name="deploymentlist"></a>DeploymentList
Pole objektů nasazení.

*Typ*: <[nasazení](#deployment)> pole


<a name="errordetail"></a>
### <a name="errordetail"></a>ErrorDetail
Podrobnosti o chybě služby Správa modelů.


|Název|Popis|Schéma|
|---|---|---|
|**kód**  <br>*Vyžaduje*|Kód chyby.|řetězec|
|**Zpráva**  <br>*Vyžaduje*|Chybová zpráva.|řetězec|


<a name="errorresponse"></a>
### <a name="errorresponse"></a>ErrorResponse
Objekt Chyba služby Správa modelů.


|Název|Popis|Schéma|
|---|---|---|
|**kód**  <br>*Vyžaduje*|Kód chyby.|řetězec|
|**Podrobnosti**  <br>*Volitelné*|Pole objektů podrobnosti o chybě.|<[ErrorDetail](#errordetail)> pole|
|**Zpráva**  <br>*Vyžaduje*|Chybová zpráva.|řetězec|
|**statusCode**  <br>*Volitelné*|Stavový kód HTTP.|integer|


<a name="image"></a>
### <a name="image"></a>Image
Obrázek, Azure Machine Learning.


|Název|Popis|Schéma|
|---|---|---|
|**computeResourceId**  <br>*Volitelné*|ID prostředí vytvořené v Machine Learning výpočetních prostředků.|řetězec|
|**createdTime**  <br>*Volitelné*|Obrázek čas vytvoření (UTC).|řetězec (datum a čas)|
|**creationState**  <br>*Volitelné*||[AsyncOperationState](#asyncoperationstate)|
|**Popis**  <br>*Volitelné*|Text popisu Image.|řetězec|
|**Chyba**  <br>*Volitelné*||[ErrorResponse](#errorresponse)|
|**ID**  <br>*Volitelné*|ID bitové kopie.|řetězec|
|**imageBuildLogUri**  <br>*Volitelné*|Identifikátor URI nahrané protokoly z bitové kopie sestavení.|řetězec|
|**Vlastnost imageLocation**  <br>*Volitelné*|Azure Container Registry umístění řetězce pro vytvořenou image.|řetězec|
|**imageType**  <br>*Volitelné*||[ImageType](#imagetype)|
|**Manifest**  <br>*Volitelné*||[Manifest](#manifest)|
|**Jméno**  <br>*Volitelné*|Název Image.|řetězec|
|**Verze**  <br>*Volitelné*|Verze Image nastavil služba Správa modelů.|integer|


<a name="imagerequest"></a>
### <a name="imagerequest"></a>imageRequest
Požadavek na vytvoření image Azure Machine Learning.


|Název|Popis|Schéma|
|---|---|---|
|**computeResourceId**  <br>*Vyžaduje*|ID prostředí vytvořené v Machine Learning výpočetních prostředků.|řetězec|
|**Popis**  <br>*Volitelné*|Text popisu Image.|řetězec|
|**imageType**  <br>*Vyžaduje*||[ImageType](#imagetype)|
|**ID manifestu**  <br>*Vyžaduje*|ID manifestu, ze kterého se vytvoří bitovou kopii.|řetězec|
|**Jméno**  <br>*Vyžaduje*|Název Image.|řetězec|


<a name="imagetype"></a>
### <a name="imagetype"></a>Typ obrázku
Určuje typ obrázku.

*Typ*: výčtu (Dockeru)


<a name="manifest"></a>
### <a name="manifest"></a>Manifest
Azure Machine Learning manifestu.


|Název|Popis|Schéma|
|---|---|---|
|**Prostředky**  <br>*Vyžaduje*|Seznam prostředků.|<[Asset](#asset)> pole|
|**createdTime**  <br>*Volitelné*  <br>*jen pro čtení*|Manifest čas vytvoření (UTC).|řetězec (datum a čas)|
|**Popis**  <br>*Volitelné*|Popis manifestu.|řetězec|
|**driverProgram**  <br>*Vyžaduje*|Program ovladače v manifestu.|řetězec|
|**ID**  <br>*Volitelné*|ID manifestu.|řetězec|
|**modelIds**  <br>*Volitelné*|Seznam ID modelu registrované modely. Požadavek selže, pokud některý z zahrnuté modely nejsou registrovány.|<string> Pole|
|**modelType**  <br>*Volitelné*|Určuje, že jsou ve službě Správa modelů ve službě už zaregistrovaný modely.|výčet (registrovaná)|
|**Jméno**  <br>*Vyžaduje*|Název souboru manifestu.|řetězec|
|**targetRuntime**  <br>*Vyžaduje*||[TargetRuntime](#targetruntime)|
|**Verze**  <br>*Volitelné*  <br>*jen pro čtení*|Verze manifestu přiřadil služba Správa modelů.|integer|
|**webserviceType**  <br>*Volitelné*|Určuje požadovaný typ webová služba, která bude vytvořena z manifestu.|výčet (v reálném čase)|


<a name="model"></a>
### <a name="model"></a>Model
Instance model ve službě Azure Machine Learning.


|Název|Popis|Schéma|
|---|---|---|
|**createdAt**  <br>*Volitelné*  <br>*jen pro čtení*|Model čas vytvoření (UTC).|řetězec (datum a čas)|
|**Popis**  <br>*Volitelné*|Textový popis modelu.|řetězec|
|**ID**  <br>*Volitelné*  <br>*jen pro čtení*|ID modelu.|řetězec|
|**mimeType**  <br>*Vyžaduje*|Typ MIME obsahu modelu. Další informace o typu MIME, najdete v článku [seznam typů médií IANA](https://www.iana.org/assignments/media-types/media-types.xhtml).|řetězec|
|**Jméno**  <br>*Vyžaduje*|Název modelu.|řetězec|
|**značek**  <br>*Volitelné*|Seznam značek modelu.|<string> Pole|
|**Rozbalit.**  <br>*Volitelné*|Určuje, jestli musíme rozbalení modelu během vytváření image Dockeru.|Boolean|
|**url**  <br>*Vyžaduje*|Adresa URL modelu. Obvykle jsme adresy URL sdíleného přístupového podpisu sem zadejte.|řetězec|
|**Verze**  <br>*Volitelné*  <br>*jen pro čtení*|Verze modelu přiřadil služba Správa modelů.|integer|


<a name="modeldatacollection"></a>
### <a name="modeldatacollection"></a>ModelDataCollection
Informace o modelu dat kolekce.


|Název|Popis|Schéma|
|---|---|---|
|**eventHubEnabled**  <br>*Volitelné*|Povolte centra událostí pro službu.|Boolean|
|**storageEnabled**  <br>*Volitelné*|Povolte úložiště pro službu.|Boolean|


<a name="paginatedimagelist"></a>
### <a name="paginatedimagelist"></a>PaginatedImageList
Stránkovaná seznam imagí.


|Název|Popis|Schéma|
|---|---|---|
|**nextLink**  <br>*Volitelné*|Pokračování odkaz na další stránku výsledků v seznamu (absolutní identifikátor URI).|řetězec|
|**value**  <br>*Volitelné*|Pole objektů modelu.|<[Obrázek](#image)> pole|


<a name="paginatedmanifestlist"></a>
### <a name="paginatedmanifestlist"></a>PaginatedManifestList
Stránkovaná seznamu manifestů.


|Název|Popis|Schéma|
|---|---|---|
|**nextLink**  <br>*Volitelné*|Pokračování odkaz na další stránku výsledků v seznamu (absolutní identifikátor URI).|řetězec|
|**value**  <br>*Volitelné*|Pole objektů manifestu.|<[Manifest](#manifest)> pole|


<a name="paginatedmodellist"></a>
### <a name="paginatedmodellist"></a>PaginatedModelList
Stránkovaná seznam modelů.


|Název|Popis|Schéma|
|---|---|---|
|**nextLink**  <br>*Volitelné*|Pokračování odkaz na další stránku výsledků v seznamu (absolutní identifikátor URI).|řetězec|
|**value**  <br>*Volitelné*|Pole objektů modelu.|<[Model](#model)> pole|


<a name="paginatedservicelist"></a>
### <a name="paginatedservicelist"></a>PaginatedServiceList
Stránkovaná seznamu služeb.


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
|**autoScaler**  <br>*Volitelné*||[Automatického škálování](#autoscaler)|
|**computeResource**  <br>*Vyžaduje*||[ComputeResource](#computeresource)|
|**containerResourceReservation**  <br>*Volitelné*||[ContainerResourceReservation](#containerresourcereservation)|
|**dataCollection**  <br>*Volitelné*||[ModelDataCollection](#modeldatacollection)|
|**imageId**  <br>*Vyžaduje*|Image vytvořte službu.|řetězec|
|**maxConcurrentRequestsPerContainer**  <br>*Volitelné*|Maximální počet souběžných požadavků.  <br>**Minimální hodnota**: `1`|integer|
|**Jméno**  <br>*Vyžaduje*|Název služby.|řetězec|
|**numReplicas**  <br>*Volitelné*|Počet replik podů spuštěná v každém okamžiku. Nelze zadat, jestli je povolené automatického škálování.  <br>**Minimální hodnota**: `0`|integer|


<a name="serviceregeneratekeyrequest"></a>
### <a name="serviceregeneratekeyrequest"></a>ServiceRegenerateKeyRequest
Požadavek na znovu vygenerovat klíč pro službu.


|Název|Popis|Schéma|
|---|---|---|
|**Typ klíče**  <br>*Volitelné*|Určuje, který klíč se znovu vygenerovat.|výčet (primární, sekundární)|


<a name="serviceresponse"></a>
### <a name="serviceresponse"></a>ServiceResponse
Podrobný stav služby.


|Název|Popis|Schéma|
|---|---|---|
|**createdAt**  <br>*Volitelné*|Čas vytvoření služby (UTC).|řetězec (datum a čas)|
|**ID**  <br>*Volitelné*|ID služby.|řetězec|
|**Bitové kopie**  <br>*Volitelné*||[Image](#image)|
|**Manifest**  <br>*Volitelné*||[Manifest](#manifest)|
|**Modely**  <br>*Volitelné*|Seznam modelů.|<[Model](#model)> pole|
|**Jméno**  <br>*Volitelné*|Název služby.|řetězec|
|**scoringUri**  <br>*Volitelné*|Identifikátor URI pro vyhodnocení služby.|řetězec|
|**Stav**  <br>*Volitelné*||[AsyncOperationState](#asyncoperationstate)|
|**updatedAt**  <br>*Volitelné*|Čas poslední aktualizace (UTC).|řetězec (datum a čas)|
|**appInsightsEnabled**  <br>*Volitelné*|Povolte application insights pro služby.|Boolean|
|**autoScaler**  <br>*Volitelné*||[Automatického škálování](#autoscaler)|
|**computeResource**  <br>*Vyžaduje*||[ComputeResource](#computeresource)|
|**containerResourceReservation**  <br>*Volitelné*||[ContainerResourceReservation](#containerresourcereservation)|
|**dataCollection**  <br>*Volitelné*||[ModelDataCollection](#modeldatacollection)|
|**maxConcurrentRequestsPerContainer**  <br>*Volitelné*|Maximální počet souběžných požadavků.  <br>**Minimální hodnota**: `1`|integer|
|**numReplicas**  <br>*Volitelné*|Počet replik podů spuštěná v každém okamžiku. Nelze zadat, jestli je povolené automatického škálování.  <br>**Minimální hodnota**: `0`|integer|
|**Chyba**  <br>*Volitelné*||[ErrorResponse](#errorresponse)|


<a name="serviceupdaterequest"></a>
### <a name="serviceupdaterequest"></a>ServiceUpdateRequest
Požadavek na aktualizaci služby.


|Název|Popis|Schéma|
|---|---|---|
|**appInsightsEnabled**  <br>*Volitelné*|Povolte application insights pro služby.|Boolean|
|**autoScaler**  <br>*Volitelné*||[Automatického škálování](#autoscaler)|
|**containerResourceReservation**  <br>*Volitelné*||[ContainerResourceReservation](#containerresourcereservation)|
|**dataCollection**  <br>*Volitelné*||[ModelDataCollection](#modeldatacollection)|
|**imageId**  <br>*Volitelné*|Image vytvořte službu.|řetězec|
|**maxConcurrentRequestsPerContainer**  <br>*Volitelné*|Maximální počet souběžných požadavků.  <br>**Minimální hodnota**: `1`|integer|
|**numReplicas**  <br>*Volitelné*|Počet replik podů spuštěná v každém okamžiku. Nelze zadat, jestli je povolené automatického škálování.  <br>**Minimální hodnota**: `0`|integer|


<a name="targetruntime"></a>
### <a name="targetruntime"></a>TargetRuntime
Zadejte cílový modul runtime.


|Název|Popis|Schéma|
|---|---|---|
|**Vlastnosti**  <br>*Vyžaduje*||< string, string > mapy|
|**runtimeType**  <br>*Vyžaduje*|Určuje modul runtime.|výčet (SparkPython, Python)|

