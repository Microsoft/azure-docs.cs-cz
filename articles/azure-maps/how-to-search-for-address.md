---
title: Hledání polohy pomocí služeb Azure Maps Search | Mapy Microsoft Azure
description: V tomto článku se dozvíte, jak vyhledat umístění pomocí služby Microsoft Azure Maps Search Service pro geokódování a reverzní geokódování.
author: philmea
ms.author: philmea
ms.date: 01/15/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: cf0e5267885df1ace51271c53bb2d68ee5002f00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335432"
---
# <a name="search-for-a-location-using-azure-maps-search-services"></a>Hledání polohy pomocí služeb Azure Maps Search

[Služba Vyhledávání](https://docs.microsoft.com/rest/api/maps/search) map Azure je sada rozhraní API RESTful, která vývojářům pomáhá vyhledávat adresy, místa, firemní zápisy podle názvu nebo kategorie a další geografické informace. Kromě podpory tradičního geokódování mohou služby také obrátit adresy geokódů a procházet ulicemi na základě zeměpisných šířků a délky. Hodnoty zeměpisné šířky a délky vrácené hledáním lze použít jako parametry v jiných službách Azure Maps, jako [jsou služby Route](https://docs.microsoft.com/rest/api/maps/route) a [Weather.](https://docs.microsoft.com/rest/api/maps/weather)

V tomto článku se dozvíte, jak:

* Požadavek souřadnic zeměpisné šířky a délky adresy (umístění adresy geokódu) pomocí [rozhraní Search Address API]( https://docs.microsoft.com/rest/api/maps/search/getsearchaddress)
* Hledání adresy nebo bodu zájmu (POI) pomocí [rozhraní Fuzzy Search API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)
* Hledání adresy spolu s vlastnostmi a souřadnicemi
* Vytvoření [vyhledávání reverzních adres](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) pro překlad souřadnicového umístění na adresu ulice
* Hledání cross street pomocí [vyhledávací adresy Reverse Cross Street API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreversecrossstreet)

## <a name="prerequisites"></a>Požadavky

Chcete-li provést kroky v tomto článku, musíte nejprve vytvořit účet Azure Maps a získat klíč předplatného účtu maps. Postupujte podle pokynů v [tématu Vytvoření účtu](quick-demo-map-app.md#create-an-account-with-azure-maps) k vytvoření předplatného účtu Azure Maps a postupujte podle pokynů v [tématu získání primárního klíče](quick-demo-map-app.md#get-the-primary-key-for-your-account) k získání primárního klíče pro váš účet. Další informace o ověřování v Azure Maps najdete v [tématu správa ověřování v Azure Maps](./how-to-manage-authentication.md).

Tento článek používá [aplikaci Postman](https://www.getpostman.com/apps) k vytváření volání REST. Můžete použít libovolné vývojové prostředí rozhraní API, které upřednostňujete.

## <a name="request-latitude-and-longitude-for-an-address-geocoding"></a>Požadavek na zeměpisnou šířku a délku adresy (geocoding)

V tomto příkladu používáme Azure Maps [Get Search Address API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) převést adresu ulice do zeměpisné šířky a délky souřadnice. Můžete předat úplnou nebo částečnou adresu do rozhraní API a obdržet odpověď, která obsahuje podrobné vlastnosti adresy, jako je ulice, PSČ a země nebo oblast, stejně jako poziční hodnoty v zeměpisné šířce a délky.

Pokud máte sadu adres ke ke ke kódování, můžete použít [post search address batch api](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressbatch) k odeslání dávky dotazů v jednom volání rozhraní API.

1. V pošťákovi klikněte na**Žádost GET** **nové žádosti** | a pojmenujte ji **Hledání adres**.

2. Na kartě Tvůrce vyberte metodu **GET** HTTP, zadejte adresu URL požadavku pro koncový bod rozhraní API a vyberte autorizační protokol, pokud existuje.

![Hledání adres](./media/how-to-search-for-address/address_search_url.png)

| Parametr | Navrhovaná hodnota |
|---------------|------------------------------------------------| 
| Metoda HTTP | GET |
| Adresa URL požadavku | [https://atlas.microsoft.com/search/address/json?](https://atlas.microsoft.com/search/address/json?) | 
| Autorizace | Žádné umělá |

3. Klepněte na **položku Params**a zadejte následující dvojice klíč / hodnota, které chcete použít jako parametry dotazu nebo cesty v adrese URL požadavku: 

![Hledání adres](./media/how-to-search-for-address/address_search_params.png) 

| Klíč | Hodnota | 
|------------------|-------------------------| 
| verze-api | 1.0 | 
| klíč předplatného | \<klíč Azure Maps\> | 
| query | 400 Broad St, Seattle, WA 98109 | 

4. Klikněte na **Odeslat** a zkontrolujte tělo odpovědi. 

V takovém případě jste zadali úplný dotaz na adresu a obdrželi jste jeden výsledek v těle odpovědi. 

5. V Params upravte řetězec dotazu na následující hodnotu: 

    ```plaintext 
        400 Broad, Seattle 
    ``` 

6. Přidejte následující dvojici Klíč / Hodnota do sekce **Params** a klepněte na **tlačítko Odeslat**: 

| Klíč | Hodnota | 
|-----|------------| 
| dopředného psaní | true | 

Příznak **typeahead** říká rozhraní API pro vyhledávání adres, aby dotaz považoval za částečný vstup a vrátil pole prediktivních hodnot.

## <a name="using-fuzzy-search-api"></a>Použití rozhraní FUZZY SEARCH API

Azure Maps[ Fuzzy Search API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) se doporučuje službu použít, když nevíte, jaké jsou vaše uživatelské vstupy pro vyhledávací dotaz. Rozhraní API kombinuje vyhledávání v místě zájmu (POI) a geokódování do kanonického jednořádkového vyhledávání. Rozhraní API může například zpracovávat vstupy libovolné kombinace tokenů adres nebo pozi. Může být také vážena kontextovou polohou (lat./lon. dvojice), plně omezena souřadnicí a poloměrem, nebo provedena obecněji bez geo biasing kotevníbod.

Většina vyhledávacích `maxFuzzyLevel=1` dotazů výchozí získat výkon a snížit neobvyklé výsledky. Toto výchozí nastavení lze podle potřeby přepsat na `maxFuzzyLevel=2` `3`požadavek předáním parametru dotazu nebo .

### <a name="search-for-an-address-using-fuzzy-search"></a>Hledání adresy pomocí přibližné vyhledávání

1. Otevřete aplikaci Pošťák, klikněte na Nový | Vytvořit nový a vybrat **get požadavek**. Zadejte název požadavku **přibližného hledání**, vyberte kolekci nebo složku, do které ji chcete uložit, a klepněte na tlačítko **Uložit**.

2. Na kartě Tvůrce vyberte metodu **GET** HTTP a zadejte adresu URL požadavku pro koncový bod rozhraní API.

    ![Přibližné hledání](./media/how-to-search-for-address/fuzzy_search_url.png)

    | Parametr | Navrhovaná hodnota |
    |---------------|------------------------------------------------|
    | Metoda HTTP | GET |
    | Adresa URL požadavku | [https://atlas.microsoft.com/search/fuzzy/json?](https://atlas.microsoft.com/search/fuzzy/json?) |
    | Autorizace | Žádné umělá |

    Formát odpovědi určuje atribut **json** v cestě URL. Tento článek používá json pro snadné použití a čitelnost. Dostupné formáty odpovědí naleznete v definici **přibližné** funkce při hledání [odkazu Rozhraní API pro funkce map](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy).

3. Klepněte na **položku Params**a zadejte následující dvojice klíč / hodnota, které chcete použít jako parametry dotazu nebo cesty v adrese URL požadavku:

    ![Přibližné hledání](./media/how-to-search-for-address/fuzzy_search_params.png)

    | Klíč | Hodnota |
    |------------------|-------------------------|
    | verze-api | 1.0 |
    | klíč předplatného | \<klíč Azure Maps\> |
    | query | Pizza |

4. Klikněte na **Odeslat** a zkontrolujte tělo odpovědi.

    Nejednoznačný řetězec dotazu pro "pizza" vrátil 10 [bodů zájmu výsledek](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi#searchpoiresponse) (POI) v obou "pizza" a "restaurace" kategorie. Každý výsledek vrátí hodnotu ulice, šířky a délky, port zobrazení a vstupní body pro umístění.
  
    Výsledky jsou různé pro tento dotaz, není vázána na jakékoli konkrétní umístění odkazu. Parametr **imisit** můžete použít k určení pouze zemí nebo oblastí, pro které vaše aplikace potřebuje pokrytí. Výchozí chování je prohledávat celý svět, potenciálně vracet zbytečné výsledky.

5. Přidejte následující dvojici Klíč / Hodnota do sekce **Params** a klepněte na **tlačítko Odeslat**:

    | Klíč | Hodnota |
    |------------------|-------------------------|
    | zeměSet | USA |
  
    Výsledky jsou nyní ohraničeny kódem země a dotaz vrací pizzerie ve Spojených státech.
  
    Chcete-li poskytnout výsledky pro umístění, můžete zadat dotaz na bod zájmu a použít vrácené hodnoty zeměpisné šířky a délky ve volání služby Fuzzy Search. V tomto případě jste použili vyhledávací službu k vrácení umístění Seattle Space Needle a použili jste lat. / lon. hodnoty pro orientaci vyhledávání.
  
6. V části Params zadejte následující dvojice klíč / hodnota a klepněte na tlačítko **Odeslat**:

    ![Přibližné hledání](./media/how-to-search-for-address/fuzzy_search_latlon.png)
  
    | Klíč | Hodnota |
    |-----|------------|
    | Lat | 47.620525 |
    | Lon | -122.349274 |


## <a name="search-for-a-street-address-using-reverse-address-search"></a>Hledání adresy pomocí zpětného vyhledávání adres

Azure Maps [Get Search Address Reverse API]( https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) pomáhá přeložit souřadnici (například: 37.786505, -122.3862) do srozumitelné ulice lidské. Nejčastěji je to potřeba ve sledovacích aplikacích, kde obdržíte GPS kanál ze zařízení nebo aktiva a chcete vědět, jakou adresu, kde se nachází souřadnice.
Pokud máte sadu umístění souřadnic pro obrácení geokódu, můžete použít [rozhraní API pro reverzní dávky za zřazené adresy pro vyhledávání](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressreversebatch) k odeslání dávky dotazů v jednom volání rozhraní API.


1. V pošťákovi klikněte na**žádost GET** **nové žádosti** | a pojmenujte ji **Reverzní hledání adres**.

2. Na kartě Tvůrce vyberte metodu **GET** HTTP a zadejte adresu URL požadavku pro koncový bod rozhraní API.
  
    ![Adresa URL vyhledávání s obrácenou adresou](./media/how-to-search-for-address/reverse_address_search_url.png)
  
    | Parametr | Navrhovaná hodnota |
    |---------------|------------------------------------------------|
    | Metoda HTTP | GET |
    | Adresa URL požadavku | [https://atlas.microsoft.com/search/address/reverse/json?](https://atlas.microsoft.com/search/address/reverse/json?) |
    | Autorizace | Žádné umělá |
  
3. Klepněte na **položku Params**a zadejte následující dvojice klíč / hodnota, které chcete použít jako parametry dotazu nebo cesty v adrese URL požadavku:
  
    ![Parametry vyhledávání reverzních adres](./media/how-to-search-for-address/reverse_address_search_params.png)
  
    | Klíč | Hodnota |
    |------------------|-------------------------|
    | verze-api | 1.0 |
    | klíč předplatného | \<klíč Azure Maps\> |
    | query | 47.591180,-122.332700 |
  
4. Klikněte na **Odeslat** a zkontrolujte tělo odpovědi.

    Odpověď obsahuje klíčové informace o adrese safeco field.
  
5. Přidejte následující dvojici Klíč / Hodnota do sekce **Params** a klepněte na **tlačítko Odeslat**:

    | Klíč | Hodnota |
    |-----|------------|
    | číslo | true |

    Pokud je parametr [dotazu na číslo](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) odeslán s požadavkem, odpověď může obsahovat stranu ulice (vlevo nebo vpravo) a také pozici posunu pro toto číslo.
  
6. Přidejte následující dvojici Klíč / Hodnota do sekce **Params** a klepněte na **tlačítko Odeslat**:

    | Klíč | Hodnota |
    |-----|------------|
    | returnSpeedLimit | true |
  
    Když je nastaven parametr dotazu [returnSpeedLimit,](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) odpověď vrátí zaúčtovaný rychlostní limit.

7. Přidejte následující dvojici Klíč / Hodnota do sekce **Params** a klepněte na **tlačítko Odeslat**:

    | Klíč | Hodnota |
    |-----|------------|
    | returnRoadUse | true |

    Když je nastaven parametr dotazu [returnRoadUse,](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) odpověď vrátí pole pro použití silnice pro reverzní geokódy na úrovni ulice.

8. Přidejte následující dvojici Klíč / Hodnota do sekce **Params** a klepněte na **tlačítko Odeslat**:

    | Klíč | Hodnota |
    |-----|------------|
    | roadUse | true |

    Reverzní dotaz geocode můžete omezit na určitý typ silnice pomocí parametru [roadUse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) query.
  
## <a name="search-for-cross-street-using-reverse-address-cross-street-search"></a>Hledat cross street pomocí reverzní adresy Cross Street Search

1. V pošťáku klikněte na**Žádost o získání** žádosti o nový **požadavek** | a pojmenujte ji **Reverzní adresa Cross Street Search**.

2. Na kartě Tvůrce vyberte metodu **GET** HTTP a zadejte adresu URL požadavku pro koncový bod rozhraní API.
  
    ![Vyhledávání v ulici obrácené adresy](./media/how-to-search-for-address/reverse_address_search_url.png)
  
    | Parametr | Navrhovaná hodnota |
    |---------------|------------------------------------------------|
    | Metoda HTTP | GET |
    | Adresa URL požadavku | [https://atlas.microsoft.com/search/address/reverse/crossstreet/json?](https://atlas.microsoft.com/search/address/reverse/crossstreet/json?) |
    | Autorizace | Žádné umělá |
  
3. Klepněte na **položku Params**a zadejte následující dvojice klíč / hodnota, které chcete použít jako parametry dotazu nebo cesty v adrese URL požadavku:
  
    | Klíč | Hodnota |
    |------------------|-------------------------|
    | verze-api | 1.0 |
    | klíč předplatného | \<klíč Azure Maps\> |
    | query | 47.591180,-122.332700 |
  
4. Klikněte na **Odeslat** a zkontrolujte tělo odpovědi.

## <a name="next-steps"></a>Další kroky

- Prozkoumejte [dokumentaci k rozhraní REST API služby Azure Maps Search Service](https://docs.microsoft.com/rest/api/maps/search).
- Přečtěte si o [doporučených postupech služby Azure Maps Search Service](https://docs.microsoft.com/azure/azure-maps/how-to-use-best-practices-for-search) a o tom, jak optimalizovat dotazy.
