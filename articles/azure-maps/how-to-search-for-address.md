---
title: Najít adresu pomocí služby Azure Maps Search Service | Microsoft Docs
description: Naučte se hledat adresu pomocí služby Azure Maps Search Service.
author: walsehgal
ms.author: v-musehg
ms.date: 04/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 56194bcfb9531def87a9918ad442a2927413c964
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75432965"
---
# <a name="find-an-address-using-the-azure-maps-search-service"></a>Najít adresu pomocí služby Azure Maps Search Service

Vyhledávací služba Maps je sada rozhraní RESTful API navržená vývojářům, kteří hledají adresy, místa, body zájmu, obchodní výpisy a další geografické informace. Služba přiřadí zeměpisnou šířku ke konkrétní adrese, mezinárodní ulici, geografické funkci nebo zájmu v oblasti zájmu (POI). Hodnoty zeměpisné šířky a délky vrácené hledáním se dají použít jako parametry v jiných službách Maps, jako je směrování a přenosový tok.

V tomto článku se dozvíte, jak:

* Hledání adresy pomocí [rozhraní API pro přibližné vyhledávání](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)
* Hledání adresy spolu s vlastnostmi a souřadnicemi
* Chcete-li vyhledat ulici, vyhledejte adresu v [obráceném pořadí](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) .
* Hledání mezi ulici pomocí [hledané adresy rozhraní API pro reverzní Meziulici](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreversecrossstreet)

## <a name="prerequisites"></a>Požadavky

Pokud chcete volat rozhraní API služby Maps, potřebujete účet a klíč mapy. Podle pokynů v části [Vytvoření účtu](quick-demo-map-app.md#create-an-account-with-azure-maps) vytvořte předplatné Azure Maps účtu a podle kroků v části [získání primárního klíče](quick-demo-map-app.md#get-the-primary-key-for-your-account) Získejte primární klíč pro svůj účet. Další podrobnosti o ověřování v Azure Maps najdete v tématu [Správa ověřování v Azure Maps](./how-to-manage-authentication.md).

V tomto článku se k sestavení volání REST používá [aplikace pro publikování](https://www.getpostman.com/apps) . Můžete použít libovolné vývojové prostředí API, které dáváte přednost.

## <a name="using-fuzzy-search"></a>Použití vyhledávání s fuzzy logikou

Výchozí rozhraní API pro vyhledávací službu je [přibližné vyhledávání](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) a je užitečné, Pokud nevíte, jaké jsou uživatelské vstupy pro vyhledávací dotaz. Rozhraní API kombinuje POI vyhledávání a geografické kódování do kanonického víceřádkového vyhledávání. Rozhraní API může například zpracovat vstupy libovolné kombinace adres nebo POI tokenu. Může být také vážená s kontextovou polohou (lat./lon. párové), plně omezená souřadnicí a poloměrem nebo bez jakýchkoli geografických posunutí bodu ukotvení.

Většina vyhledávacích dotazů ve výchozím nastavení `maxFuzzyLevel=1` pro získání výkonu a omezení neobvyklých výsledků. Tato výchozí hodnota se dá přepsat podle potřeby na žádost předáním parametru dotazu `maxFuzzyLevel=2` nebo `3`.

### <a name="search-for-an-address-using-fuzzy-search"></a>Hledání adresy pomocí hledání přibližných výsledků

1. Otevřete aplikaci po kliknutí na nový | Vytvořte nové a vyberte **získat žádost**. Zadejte název žádosti **přibližného hledání**, vyberte kolekci nebo složku, do které se má uložit, a klikněte na **Uložit**.

2. Na kartě tvůrce vyberte metodu **Get** http a zadejte adresu URL žádosti pro koncový bod rozhraní API.

    ![Hledání přibližných výsledků](./media/how-to-search-for-address/fuzzy_search_url.png)

    | Parametr | Navrhovaná hodnota |
    |---------------|------------------------------------------------|
    | Metoda HTTP | GET |
    | Adresa URL požadavku | [https://atlas.microsoft.com/search/fuzzy/json?](https://atlas.microsoft.com/search/fuzzy/json?) |
    | Autorizace | No Auth |

    Atribut **JSON** v cestě URL určuje formát odpovědi. V celém tomto článku používáte JSON, abyste usnadnili používání a čitelnost. Dostupné formáty odezvy najdete v definici **přibližné vyhledávání** v [referenci rozhraní API pro funkce Maps](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy).

3. Klikněte na **parametry**a zadejte následující páry klíč/hodnota, které se použijí jako parametry dotazu nebo cesty v adrese URL požadavku:

    ![Hledání přibližných výsledků](./media/how-to-search-for-address/fuzzy_search_params.png)

    | Klíč | Hodnota |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | předplatné – klíč | \<Azure Maps Key\> |
    | query | pizza |

4. Klikněte na **Odeslat** a zkontrolujte text odpovědi.

    Nejednoznačný řetězec dotazu "pizza" vrátil 10 [bodů výsledku úroku](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi#searchpoiresponse) (POI) výsledky s kategoriemi, které jsou v oblasti "pizza" a "restaurace". Každý výsledek vrátí adresu ulice, hodnoty zeměpisné šířky a délky, zobrazit port a vstupní body pro dané umístění.
  
    Výsledky jsou pro tento dotaz různé, nevázané na žádné konkrétní referenční místo. Pomocí parametru **countrySet** můžete zadat jenom země nebo oblasti, pro které vaše aplikace potřebuje pokrytí, protože výchozí chování je hledání celého světa, který může vracet zbytečné výsledky.

5. Do oddílu **param** přidejte následující dvojici klíč/hodnota a klikněte na **Odeslat**:

    | Klíč | Hodnota |
    |------------------|-------------------------|
    | countrySet | Spojené státy |
  
    Výsledky jsou nyní ohraničeny kódem země a dotaz vrátí Pizza restaurací ve USA.
  
    K poskytnutí výsledků pro umístění můžete zadat dotaz na bod zájmu a použít vrácenou zeměpisnou šířku a délku ve volání služby pro vyhledávání přibližných hodnot. V tomto případě jste použili službu Search, která vrátí umístění ručičky prostoru Seattle a použila lat. / lon. hodnoty pro orientaci hledání
  
6. V části params zadejte následující páry klíč/hodnota a klikněte na **Odeslat**:

    ![Hledání přibližných výsledků](./media/how-to-search-for-address/fuzzy_search_latlon.png)
  
    | Klíč | Hodnota |
    |-----|------------|
    | připojí | 47,620525 |
    | lon | -122,349274 |

## <a name="search-for-address-properties-and-coordinates"></a>Hledání vlastností adresy a souřadnic

Do rozhraní API pro hledání adresy můžete předat úplnou nebo částečnou ulici a získat odpověď, která obsahuje podrobné vlastnosti adresy, jako je například obec nebo pododdíl, a také poziční hodnoty v zeměpisné šířce a délce.

1. V příspěvku klikněte na **nový požadavek** | **získat žádost** a pojmenovat **hledání na adrese**.
2. Na kartě tvůrce vyberte metodu **Get** http, zadejte adresu URL žádosti pro koncový bod rozhraní API a vyberte autorizační protokol (pokud nějaký existuje).

    ![Hledání adres](./media/how-to-search-for-address/address_search_url.png)
  
    | Parametr | Navrhovaná hodnota |
    |---------------|------------------------------------------------|
    | Metoda HTTP | GET |
    | Adresa URL požadavku | [https://atlas.microsoft.com/search/address/json?](https://atlas.microsoft.com/search/address/json?) |
    | Autorizace | No Auth |

3. Klikněte na **parametry**a zadejte následující páry klíč/hodnota, které se použijí jako parametry dotazu nebo cesty v adrese URL požadavku:
  
    ![Hledání adres](./media/how-to-search-for-address/address_search_params.png)
  
    | Klíč | Hodnota |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | předplatné – klíč | \<Azure Maps Key\> |
    | query | 400, široká, Praha, WA 98109 |
  
4. Klikněte na **Odeslat** a zkontrolujte text odpovědi.
  
    V tomto případě jste zadali kompletní dotaz na adresu a v těle odpovědi přijali jeden výsledek.
  
5. V parametrech param řetězec dotazu upravte na následující hodnotu:
    ```plaintext
        400 Broad, Seattle
    ```

6. Do oddílu **param** přidejte následující dvojici klíč/hodnota a klikněte na **Odeslat**:

    | Klíč | Hodnota |
    |-----|------------|
    | typeahead | true |

    Příznak **typeahead** oznamuje rozhraní API pro vyhledávání adres, aby se dotaz považoval za částečný vstup a vrátil pole prediktivních hodnot.

## <a name="search-for-a-street-address-using-reverse-address-search"></a>Hledání adresy ulice pomocí zpětného vyhledávání adres

1. V příspěvku klikněte na **nový požadavek** | **získat žádost** a pojmenujte ho na **zpětné vyhledávání**.

2. Na kartě tvůrce vyberte metodu **Get** http a zadejte adresu URL žádosti pro koncový bod rozhraní API.
  
    ![Adresa URL pro hledání zpětné adresy](./media/how-to-search-for-address/reverse_address_search_url.png)
  
    | Parametr | Navrhovaná hodnota |
    |---------------|------------------------------------------------|
    | Metoda HTTP | GET |
    | Adresa URL požadavku | [https://atlas.microsoft.com/search/address/reverse/json?](https://atlas.microsoft.com/search/address/reverse/json?) |
    | Autorizace | No Auth |
  
3. Klikněte na **parametry**a zadejte následující páry klíč/hodnota, které se použijí jako parametry dotazu nebo cesty v adrese URL požadavku:
  
    ![Parametry vyhledávání zpětné adresy](./media/how-to-search-for-address/reverse_address_search_params.png)
  
    | Klíč | Hodnota |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | předplatné – klíč | \<Azure Maps Key\> |
    | query | 47.591180,-122,332700 |
  
4. Klikněte na **Odeslat** a zkontrolujte text odpovědi.

    Odpověď obsahuje klíčová informace o adrese pole Safeco Field.
  
5. Do oddílu **param** přidejte následující dvojici klíč/hodnota a klikněte na **Odeslat**:

    | Klíč | Hodnota |
    |-----|------------|
    | číslo | true |

    Pokud je parametr dotazu [Number](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) odeslán s požadavkem, může odpověď zahrnovat stranu ulice (vlevo/vpravo) a také pozici posunu pro toto číslo.
  
6. Do oddílu **param** přidejte následující dvojici klíč/hodnota a klikněte na **Odeslat**:

    | Klíč | Hodnota |
    |-----|------------|
    | returnSpeedLimit | true |
  
    Pokud je nastaven parametr dotazu [returnSpeedLimit](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) , vrátí se odpověď na odeslaný limit.

7. Do oddílu **param** přidejte následující dvojici klíč/hodnota a klikněte na **Odeslat**:

    | Klíč | Hodnota |
    |-----|------------|
    | returnRoadUse | true |

    Pokud je nastaven parametr dotazu [returnRoadUse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) , vrátí odpověď pole pro použití pro reverzní mikrokódování na úrovni ulice.

8. Do oddílu **param** přidejte následující dvojici klíč/hodnota a klikněte na **Odeslat**:

    | Klíč | Hodnota |
    |-----|------------|
    | roadUse | true |

    Můžete omezit dotaz na reverzní mikrokódování na konkrétní typ cesty pomocí parametru dotazu [roadUse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) .
  
## <a name="search-for-the-cross-street-using-reverse-address-cross-street-search"></a>Hledání příčné ulice pomocí zpětného vyhledávání přes meziulici

1. V příspěvku klikněte na **nový požadavek** | **získat žádost** a pojmenujte ho **Zpětná adresa hledání mezi ulice**.

2. Na kartě tvůrce vyberte metodu **Get** http a zadejte adresu URL žádosti pro koncový bod rozhraní API.
  
    ![Hledání zpětné adresy v meziulici](./media/how-to-search-for-address/reverse_address_search_url.png)
  
    | Parametr | Navrhovaná hodnota |
    |---------------|------------------------------------------------|
    | Metoda HTTP | GET |
    | Adresa URL požadavku | [https://atlas.microsoft.com/search/address/reverse/crossstreet/json?](https://atlas.microsoft.com/search/address/reverse/crossstreet/json?) |
    | Autorizace | No Auth |
  
3. Klikněte na **parametry**a zadejte následující páry klíč/hodnota, které se použijí jako parametry dotazu nebo cesty v adrese URL požadavku:
  
    | Klíč | Hodnota |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | předplatné – klíč | \<Azure Maps Key\> |
    | query | 47.591180,-122,332700 |
  
4. Klikněte na **Odeslat** a zkontrolujte text odpovědi.

## <a name="next-steps"></a>Další kroky

- Prozkoumejte dokumentaci k rozhraní API [služby Azure Maps Search Service](https://docs.microsoft.com/rest/api/maps/search) .
