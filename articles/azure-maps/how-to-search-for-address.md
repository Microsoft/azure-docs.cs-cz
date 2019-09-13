---
title: Hledání adresy pomocí služby Azure Maps Search | Microsoft Docs
description: Naučte se hledat adresu pomocí služby Azure Maps Search Service.
author: walsehgal
ms.author: v-musehg
ms.date: 04/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: a41a811e9313f79c9c3165e02cb5eaa4353b65ab
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2019
ms.locfileid: "70914472"
---
# <a name="find-an-address-using-the-azure-maps-search-service"></a>Najít adresu pomocí služby Azure Maps Search Service

Vyhledávací služba Maps je sada rozhraní RESTful API navržená vývojářům, kteří hledají adresy, místa, body zájmu, obchodní výpisy a další geografické informace. Služba přiřadí zeměpisnou šířku ke konkrétní adrese, mezinárodní ulici, geografické funkci nebo zájmu v oblasti zájmu (POI). Hodnoty zeměpisné šířky a délky vrácené hledáním se dají použít jako parametry v jiných službách Maps, jako je směrování a přenosový tok.

V tomto článku se dozvíte, jak:

* Hledání adresy pomocí [rozhraní API pro přibližné vyhledávání](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)
* Hledání adresy spolu s vlastnostmi a souřadnicemi
* Chcete-li vyhledat ulici, vyhledejte adresu v [obráceném pořadí](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) .
* Hledání mezi ulici pomocí [hledané adresy rozhraní API pro reverzní Meziulici](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreversecrossstreet)

## <a name="prerequisites"></a>Požadavky

Pokud chcete volat rozhraní API služby Maps, potřebujete účet a klíč mapy. Informace o vytvoření účtu najdete v pokynech v tématu [Správa účtu](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys#create-a-new-account) a postup pro získání primárního klíče předplatného pro váš účet pomocí kroků v části [získání primárního klíče](./tutorial-search-location.md#getkey) .

V tomto článku se k sestavení volání REST používá [aplikace pro publikování](https://www.getpostman.com/apps) . Můžete použít libovolné vývojové prostředí API, které dáváte přednost.

## <a name="using-fuzzy-search"></a>Použití vyhledávání s fuzzy logikou

Výchozí rozhraní API pro vyhledávací službu je [přibližné vyhledávání](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) a je užitečné, Pokud nevíte, jaké jsou uživatelské vstupy pro vyhledávací dotaz. Rozhraní API kombinuje POI vyhledávání a geografické kódování do kanonického víceřádkového vyhledávání. Rozhraní API může například zpracovat vstupy libovolné kombinace adres nebo POI tokenu. Může být také vážená s kontextovou polohou (lat./lon. párové), plně omezená souřadnicí a poloměrem nebo bez jakýchkoli geografických posunutí bodu ukotvení.

Většina vyhledávacích dotazů je `maxFuzzyLevel=1` ve výchozím nastavení tak, aby získala výkon a snížila neobvyklé výsledky. Tato výchozí hodnota se dá přepsat podle potřeby na žádost předáním parametru `maxFuzzyLevel=2` dotazu nebo. `3`

### <a name="search-for-an-address-using-fuzzy-search"></a>Hledání adresy pomocí hledání přibližných výsledků

1. Otevřete aplikaci po kliknutí na nový | Vytvořte nové a vyberte **získat žádost**. Zadejte název žádosti přibližného **hledání**, vyberte kolekci nebo složku, do které se má uložit, a klikněte na **Uložit**.

2. Na kartě tvůrce vyberte metodu **Get** http a zadejte adresu URL žádosti pro koncový bod rozhraní API.

    ![Hledání přibližných výsledků](./media/how-to-search-for-address/fuzzy_search_url.png)

    | Parametr | Navrhovaná hodnota |
    |---------------|------------------------------------------------|
    | Metoda HTTP | GET |
    | Adresa URL požadavku | [https://atlas.microsoft.com/search/fuzzy/json?](https://atlas.microsoft.com/search/fuzzy/json?) |
    | Authorization | Bez ověřování |

    Atribut **JSON** v cestě URL určuje formát odpovědi. V celém tomto článku používáte JSON, abyste usnadnili používání a čitelnost. Dostupné formáty odezvy najdete v definici přibližné **vyhledávání** v [referenci rozhraní API pro funkce Maps](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy).

3. Klikněte na **parametry**a zadejte následující páry klíč/hodnota, které se použijí jako parametry dotazu nebo cesty v adrese URL požadavku:

    ![Hledání přibližných výsledků](./media/how-to-search-for-address/fuzzy_search_params.png)

    | Klíč | Value |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | předplatné – klíč | \<váš Azure Maps klíč\> |
    | query | pizza |

4. Klikněte na **Odeslat** a zkontrolujte text odpovědi.

    Nejednoznačný řetězec dotazu "pizza" vrátil 10 [bodů výsledku úroku](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi#searchpoiresponse) (POI) výsledky s kategoriemi, které jsou v oblasti "pizza" a "restaurace". Každý výsledek vrátí adresu ulice, hodnoty zeměpisné šířky a délky, zobrazit port a vstupní body pro dané umístění.
  
    Výsledky jsou pro tento dotaz různé, nevázané na žádné konkrétní referenční místo. Pomocí parametru **countrySet** můžete zadat jenom země nebo oblasti, pro které vaše aplikace potřebuje pokrytí, protože výchozí chování je hledání celého světa, který může vracet zbytečné výsledky.

5. Do oddílu param přidejte následující dvojici klíč /hodnota a klikněte na **Odeslat**:

    | Klíč | Value |
    |------------------|-------------------------|
    | countrySet | USA |
  
    Výsledky jsou nyní ohraničeny kódem země a dotaz vrátí Pizza restaurací ve USA.
  
    K poskytnutí výsledků pro umístění můžete zadat dotaz na bod zájmu a použít vrácenou zeměpisnou šířku a délku ve volání služby pro vyhledávání přibližných hodnot. V tomto případě jste použili službu Search, která vrátí umístění ručičky prostoru Seattle a použila lat. / lon. hodnoty pro orientaci hledání
  
6. V části params zadejte následující páry klíč/hodnota a klikněte na **Odeslat**:

    ![Hledání přibližných výsledků](./media/how-to-search-for-address/fuzzy_search_latlon.png)
  
    | Klíč | Value |
    |-----|------------|
    | připojí | 47,620525 |
    | lon | -122,349274 |

## <a name="search-for-address-properties-and-coordinates"></a>Hledání vlastností adresy a souřadnic

Do rozhraní API pro hledání adresy můžete předat úplnou nebo částečnou ulici a získat odpověď, která obsahuje podrobné vlastnosti adresy, jako je například obec nebo pododdíl, a také poziční hodnoty v zeměpisné šířce a délce.

1. V příspěvku klikněte na **nový požadavek** | **získat žádost** a pojmenujte ho **Vyhledat**.
2. Na kartě tvůrce vyberte metodu **Get** http, zadejte adresu URL žádosti pro koncový bod rozhraní API a vyberte autorizační protokol (pokud nějaký existuje).

    ![Hledání adres](./media/how-to-search-for-address/address_search_url.png)
  
    | Parametr | Navrhovaná hodnota |
    |---------------|------------------------------------------------|
    | Metoda HTTP | GET |
    | Adresa URL požadavku | [https://atlas.microsoft.com/search/address/json?](https://atlas.microsoft.com/search/address/json?) |
    | Authorization | Bez ověřování |

3. Klikněte na **parametry**a zadejte následující páry klíč/hodnota, které se použijí jako parametry dotazu nebo cesty v adrese URL požadavku:
  
    ![Hledání adres](./media/how-to-search-for-address/address_search_params.png)
  
    | Klíč | Value |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | předplatné – klíč | \<váš Azure Maps klíč\> |
    | query | 400, široká, Praha, WA 98109 |
  
4. Klikněte na **Odeslat** a zkontrolujte text odpovědi.
  
    V tomto případě jste zadali kompletní dotaz na adresu a v těle odpovědi přijali jeden výsledek.
  
5. V parametrech param řetězec dotazu upravte na následující hodnotu:
    ```plaintext
        400 Broad, Seattle
    ```

6. Do oddílu param přidejte následující dvojici klíč /hodnota a klikněte na **Odeslat**:

    | Klíč | Value |
    |-----|------------|
    | typeahead | true |

    Příznak **typeahead** oznamuje rozhraní API pro vyhledávání adres, aby se dotaz považoval za částečný vstup a vrátil pole prediktivních hodnot.

## <a name="search-for-a-street-address-using-reverse-address-search"></a>Hledání adresy ulice pomocí zpětného vyhledávání adres

1. V příspěvku klikněte na **nový požadavek** | **získat žádost** a pojmenujte ho pro **zpětné vyhledávání adres**.

2. Na kartě tvůrce vyberte metodu **Get** http a zadejte adresu URL žádosti pro koncový bod rozhraní API.
  
    ![Adresa URL pro hledání zpětné adresy](./media/how-to-search-for-address/reverse_address_search_url.png)
  
    | Parametr | Navrhovaná hodnota |
    |---------------|------------------------------------------------|
    | Metoda HTTP | GET |
    | Adresa URL požadavku | [https://atlas.microsoft.com/search/address/reverse/json?](https://atlas.microsoft.com/search/address/reverse/json?) |
    | Authorization | Bez ověřování |
  
3. Klikněte na **parametry**a zadejte následující páry klíč/hodnota, které se použijí jako parametry dotazu nebo cesty v adrese URL požadavku:
  
    ![Parametry vyhledávání zpětné adresy](./media/how-to-search-for-address/reverse_address_search_params.png)
  
    | Klíč | Value |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | předplatné – klíč | \<váš Azure Maps klíč\> |
    | query | 47.591180,-122,332700 |
  
4. Klikněte na **Odeslat** a zkontrolujte text odpovědi.

    Odpověď obsahuje klíčová informace o adrese pole Safeco Field.
  
5. Do oddílu param přidejte následující dvojici klíč /hodnota a klikněte na **Odeslat**:

    | Klíč | Value |
    |-----|------------|
    | number | true |

    Pokud je parametr dotazu [Number](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) odeslán s požadavkem, může odpověď zahrnovat stranu ulice (vlevo/vpravo) a také pozici posunu pro toto číslo.
  
6. Do oddílu param přidejte následující dvojici klíč /hodnota a klikněte na **Odeslat**:

    | Klíč | Value |
    |-----|------------|
    | returnSpeedLimit | true |
  
    Pokud je nastaven parametr dotazu [returnSpeedLimit](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) , vrátí se odpověď na odeslaný limit.

7. Do oddílu param přidejte následující dvojici klíč /hodnota a klikněte na **Odeslat**:

    | Klíč | Value |
    |-----|------------|
    | returnRoadUse | true |

    Pokud je nastaven parametr dotazu [returnRoadUse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) , vrátí odpověď pole pro použití pro reverzní mikrokódování na úrovni ulice.

8. Do oddílu param přidejte následující dvojici klíč /hodnota a klikněte na **Odeslat**:

    | Klíč | Value |
    |-----|------------|
    | roadUse | true |

    Můžete omezit dotaz na reverzní mikrokódování na konkrétní typ cesty pomocí parametru dotazu [roadUse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) .
  
## <a name="search-for-the-cross-street-using-reverse-address-cross-street-search"></a>Hledání příčné ulice pomocí zpětného vyhledávání přes meziulici

1. V příspěvku klikněte na **nový požadavek** | **získat žádost** a pojmenujte ho **obrátit na hledání mezi ulice**.

2. Na kartě tvůrce vyberte metodu **Get** http a zadejte adresu URL žádosti pro koncový bod rozhraní API.
  
    ![Hledání zpětné adresy v meziulici](./media/how-to-search-for-address/reverse_address_search_url.png)
  
    | Parametr | Navrhovaná hodnota |
    |---------------|------------------------------------------------|
    | Metoda HTTP | GET |
    | Adresa URL požadavku | [https://atlas.microsoft.com/search/address/reverse/crossstreet/json?](https://atlas.microsoft.com/search/address/reverse/crossstreet/json?) |
    | Authorization | Bez ověřování |
  
3. Klikněte na **parametry**a zadejte následující páry klíč/hodnota, které se použijí jako parametry dotazu nebo cesty v adrese URL požadavku:
  
    | Klíč | Value |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | předplatné – klíč | \<váš Azure Maps klíč\> |
    | query | 47.591180,-122,332700 |
  
4. Klikněte na **Odeslat** a zkontrolujte text odpovědi.

## <a name="next-steps"></a>Další kroky

- Prozkoumejte dokumentaci k rozhraní API [služby Azure Maps Search Service](https://docs.microsoft.com/rest/api/maps/search) .
