---
title: Hledat umístění pomocí Search Service Azure Maps | Mapy Microsoft Azure
description: V tomto článku se naučíte, jak vyhledat umístění pomocí Search Service Microsoft Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 01/15/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 53856b4157afa5976947c451952fc26eefcdd0ea
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2020
ms.locfileid: "76264182"
---
# <a name="find-an-address-using-the-azure-maps-search-service"></a>Najít adresu pomocí služby Azure Maps Search Service

Vyhledávací služba Maps je sada rozhraní RESTful API navržených pro vývojáře. Služba může hledat adresy, místa, body zájmu, obchodní výpisy a další geografické informace. Každé z těchto hodnot je zeměpisná šířka a zeměpisná délka: konkrétní adresa, meziulici, zeměpisná funkce nebo POI (Point of Interest). Z dotazu můžete použít vrácené hodnoty zeměpisné šířky a délky jako parametry v jiných mapových službách. Například vrácené hodnoty se mohou stát parametry služby směrování nebo služby toku provozu. 

Pojďme se naučit, jak:

* Hledání adresy pomocí [rozhraní API pro přibližné vyhledávání](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)
* Hledání adresy spolu s vlastnostmi a souřadnicemi
* Chcete-li vyhledat ulici, vyhledejte adresu v [obráceném pořadí](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) .
* Hledání mezi ulici pomocí [hledané adresy rozhraní API pro reverzní Meziulici](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreversecrossstreet)

## <a name="prerequisites"></a>Požadavky

Abyste mohli volat rozhraní API služby Maps, potřebujete účet pro mapování a klíč. Pokud chcete vytvořit účet pro Azure Maps, postupujte podle pokynů v tématu [Vytvoření účtu](quick-demo-map-app.md#create-an-account-with-azure-maps). Pokud potřebujete pomoc s získáním primárního klíče, postupujte podle kroků v tématu [získání primárního klíče](quick-demo-map-app.md#get-the-primary-key-for-your-account). Další informace o ověřování v Azure Maps najdete v tématu [Správa ověřování v Azure Maps](./how-to-manage-authentication.md).

V tomto článku se k sestavení volání REST používá [aplikace pro publikování](https://www.getpostman.com/apps) . Můžete použít libovolné vývojové prostředí API, které dáváte přednost.

## <a name="using-fuzzy-search"></a>Použití vyhledávání s fuzzy logikou

Výchozím rozhraním API pro vyhledávací službu je [hledání přibližné](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy). Tato služba je užitečná v případě, že ve vyhledávacím dotazu nevíte o formátování vstupu uživatele. Rozhraní API kombinuje POI vyhledávání a geografické kódování do kanonického víceřádkového vyhledávání. Rozhraní API může například zpracovat vstupy libovolné kombinace adres nebo POI tokenu. Také může být vážená s kontextovou polohou (lat./lon. párové), plně omezená souřadnicí a poloměrem nebo bez jakýchkoli geografických posunutí bodu ukotvení.

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

    Atribut **JSON** v cestě URL určuje formát odpovědi. V tomto článku se používá JSON pro snadné použití a čitelnost. Dostupné formáty odezvy najdete v definici **přibližné vyhledávání** v [referenci rozhraní API pro funkce Maps](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy).

3. Klikněte na **parametry**a zadejte následující páry klíč/hodnota, které se použijí jako parametry dotazu nebo cesty v adrese URL požadavku:

    ![Hledání přibližných výsledků](./media/how-to-search-for-address/fuzzy_search_params.png)

    | Klíč | Hodnota |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | předplatné – klíč | \<Azure Maps Key\> |
    | query | pizza |

4. Klikněte na **Odeslat** a zkontrolujte text odpovědi.

    Nejednoznačný řetězec dotazu pro "pizza" vrátil 10 [bodů výsledku zájmu](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi#searchpoiresponse) (POI) v kategoriích "pizza" i "restaurace". Každý výsledek vrátí ulici, zeměpisnou šířku a délku hodnoty, zobrazit port a vstupní body pro dané umístění.
  
    Výsledky jsou pro tento dotaz různé, nevázané na žádné konkrétní referenční místo. Pomocí parametru **countrySet** můžete zadat jenom země nebo oblasti, pro které vaše aplikace potřebuje pokrytí. Výchozím chováním je hledání celého světa, který může vracet zbytečné výsledky.

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

Rozhraní API pro vyhledávání adres můžete předat úplnou nebo částečnou ulici. Pořád dostanete odpověď, která obsahuje podrobné vlastnosti adresy. Podrobné vlastnosti adresy jsou hodnoty, jako jsou poziční hodnoty v nadmořské výšce a délka, obec nebo dělení.

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

    Pokud je parametr dotazu [Number](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) odeslán s požadavkem, může odpověď zahrnovat stranu ulice (vlevo nebo vpravo) a také pozici posunu pro toto číslo.
  
6. Do oddílu **param** přidejte následující dvojici klíč/hodnota a klikněte na **Odeslat**:

    | Klíč | Hodnota |
    |-----|------------|
    | returnSpeedLimit | true |
  
    Pokud je nastaven parametr dotazu [returnSpeedLimit](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) , odpověď vrátí vyslaný limit rychlosti.

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
