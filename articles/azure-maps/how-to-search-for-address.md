---
title: Vyhledat umístění pomocí služby Azure Maps Search Services | Mapy Microsoft Azure
description: V tomto článku se naučíte, jak vyhledat umístění pomocí Microsoft Azure map Search Service k geografickému kódování a zpětnému zakódování.
author: philmea
ms.author: philmea
ms.date: 01/15/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 2d8c7ce62f7c592c396fa1ea7a7f5e7dc7df2dc1
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2020
ms.locfileid: "86517582"
---
# <a name="search-for-a-location-using-azure-maps-search-services"></a>Vyhledat umístění pomocí služby Azure Maps Search Services

Azure Maps [Search Service](https://docs.microsoft.com/rest/api/maps/search) je sada rozhraní API pro RESTful navržená tak, aby vývojářům usnadnila hledání adres, míst, obchodních seznamů podle názvu nebo kategorie a dalších geografických informací. Kromě podpory tradičního geografického kódování můžou služby také přesměrovat adresy geografického kódu a meziulic na základě Latitudes a délky. Hodnoty zeměpisné šířky a délky vrácené hledáním se dají použít jako parametry v jiných Azure Maps službách, jako jsou třeba [trasy](https://docs.microsoft.com/rest/api/maps/route) a [povětrnostní](https://docs.microsoft.com/rest/api/maps/weather) služby.

V tomto článku se dozvíte, jak:

* Vyžádat souřadnice zeměpisné šířky a délky pro adresu (umístění adresy geografického kódu) pomocí [rozhraní API pro hledání adres]( https://docs.microsoft.com/rest/api/maps/search/getsearchaddress)
* Hledání adresy nebo bodu zájmu (POI) pomocí [rozhraní API pro přibližné vyhledávání](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)
* Hledání adresy spolu s vlastnostmi a souřadnicemi
* Chcete-li přeložit polohu souřadnic na ulici, proveďte [hledání zpětného překladu adres](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) .
* Hledání mezi ulici pomocí [hledané adresy rozhraní API pro reverzní Meziulici](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreversecrossstreet)

## <a name="prerequisites"></a>Předpoklady

Abyste mohli dokončit kroky v tomto článku, musíte nejdřív vytvořit účet Azure Maps a poznáte klíč předplatného účtu. Podle pokynů v části [Vytvoření účtu](quick-demo-map-app.md#create-an-azure-maps-account) vytvořte předplatné Azure Maps účtu a podle kroků v části [získání primárního klíče](quick-demo-map-app.md#get-the-primary-key-for-your-account) Získejte primární klíč pro svůj účet. Další informace o ověřování v Azure Maps najdete v tématu [Správa ověřování v Azure Maps](./how-to-manage-authentication.md).

V tomto článku se k sestavení volání REST používá [aplikace pro publikování](https://www.getpostman.com/apps) . Můžete použít libovolné vývojové prostředí API, které dáváte přednost.

## <a name="request-latitude-and-longitude-for-an-address-geocoding"></a>Vyžádat zeměpisnou šířku a zeměpisnou délku adresy (geografické kódování)

V tomto příkladu používáme Azure Maps [získat rozhraní API pro vyhledávání adres](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) pro převod ulice na souřadnice zeměpisné šířky a délky. Rozhraní API můžete předat úplnou nebo částečnou ulici a získat odpověď, která obsahuje podrobné vlastnosti adresy, jako je ulice, PSČ a země/oblast, a také poziční hodnoty zeměpisné šířky a délky.

Pokud máte sadu adres pro INCODE, můžete k odeslání dávky dotazů v jednom volání rozhraní API použít [rozhraní API pro dávkové vyhledávání](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressbatch) .

1. V příspěvku klikněte na **nový požadavek**  |  **získat žádost** a pojmenujte ho **Vyhledat**.

2. Na kartě tvůrce vyberte metodu **Get** http, zadejte adresu URL žádosti pro koncový bod rozhraní API a vyberte autorizační protokol (pokud nějaký existuje).

![Hledání adres](./media/how-to-search-for-address/address_search_url.png)

| Parametr | Navrhovaná hodnota |
|---------------|------------------------------------------------| 
| Metoda HTTP | GET |
| Adresa URL požadavku | [https://atlas.microsoft.com/search/address/json?](https://atlas.microsoft.com/search/address/json?) | 
| Autorizace | Bez ověřování |

3. Klikněte na **parametry**a zadejte následující páry klíč/hodnota, které se použijí jako parametry dotazu nebo cesty v adrese URL požadavku: 

![Hledání adres](./media/how-to-search-for-address/address_search_params.png) 

| Key | Hodnota | 
|------------------|-------------------------| 
| verze-api | 1.0 | 
| předplatné – klíč | \<your Azure Maps key\> | 
| query | 400, široká, Praha, WA 98109 | 

4. Klikněte na **Odeslat** a zkontrolujte text odpovědi. 

V tomto případě jste zadali kompletní dotaz na adresu a v těle odpovědi přijali jeden výsledek. 

5. V parametrech param řetězec dotazu upravte na následující hodnotu: 

    ```plaintext 
        400 Broad, Seattle 
    ``` 

6. Do oddílu **param** přidejte následující dvojici klíč/hodnota a klikněte na **Odeslat**: 

| Key | Hodnota | 
|-----|------------| 
| typeahead | true | 

Příznak **typeahead** oznamuje rozhraní API pro vyhledávání adres, aby se dotaz považoval za částečný vstup a vrátil pole prediktivních hodnot.

## <a name="using-fuzzy-search-api"></a>Použití rozhraní API pro vyhledávání s fuzzy logikou

Azure Maps[ rozhraní API pro vyhledávání s fuzzy logikou](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) se doporučuje používat v případě, že nevíte, jaké jsou uživatelské vstupy pro vyhledávací dotaz. Rozhraní API kombinuje hledání POI (Point of Interest) a geografické kódování na kanonickém "jednořádkovém hledání". Rozhraní API může například zpracovat vstupy libovolné kombinace adres nebo POI tokenu. Může být také vážená s kontextovou polohou (lat./lon. párové), plně omezená souřadnicí a poloměrem nebo bez jakýchkoli geografických posunutí bodu ukotvení.

Většina vyhledávacích dotazů je ve výchozím nastavení tak, aby `maxFuzzyLevel=1` získala výkon a snížila neobvyklé výsledky. Tato výchozí hodnota se dá přepsat podle potřeby na žádost předáním parametru dotazu `maxFuzzyLevel=2` nebo `3` .

### <a name="search-for-an-address-using-fuzzy-search"></a>Hledání adresy pomocí hledání přibližných výsledků

1. Otevřete aplikaci po kliknutí na nový | Vytvořte nové a vyberte **získat žádost**. Zadejte název žádosti **přibližného hledání**, vyberte kolekci nebo složku, do které se má uložit, a klikněte na **Uložit**.

2. Na kartě tvůrce vyberte metodu **Get** http a zadejte adresu URL žádosti pro koncový bod rozhraní API.

    ![Hledání přibližných výsledků](./media/how-to-search-for-address/fuzzy_search_url.png)

    | Parametr | Navrhovaná hodnota |
    |---------------|------------------------------------------------|
    | Metoda HTTP | GET |
    | Adresa URL požadavku | [https://atlas.microsoft.com/search/fuzzy/json?](https://atlas.microsoft.com/search/fuzzy/json?) |
    | Autorizace | Bez ověřování |

    Atribut **JSON** v cestě URL určuje formát odpovědi. V tomto článku se používá JSON pro snadné použití a čitelnost. Dostupné formáty odezvy najdete v definici **přibližné vyhledávání** v [referenci rozhraní API pro funkce Maps](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy).

3. Klikněte na **parametry**a zadejte následující páry klíč/hodnota, které se použijí jako parametry dotazu nebo cesty v adrese URL požadavku:

    ![Hledání přibližných výsledků](./media/how-to-search-for-address/fuzzy_search_params.png)

    | Key | Hodnota |
    |------------------|-------------------------|
    | verze-api | 1.0 |
    | předplatné – klíč | \<your Azure Maps key\> |
    | query | Pizza |

4. Klikněte na **Odeslat** a zkontrolujte text odpovědi.

    Nejednoznačný řetězec dotazu pro "pizza" vrátil 10 [bodů výsledku zájmu](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi#searchpoiresponse) (POI) v kategoriích "pizza" i "restaurace". Každý výsledek vrátí ulici, zeměpisnou šířku a délku hodnoty, zobrazit port a vstupní body pro dané umístění.
  
    Výsledky jsou pro tento dotaz různé, nevázané na žádné konkrétní referenční místo. Pomocí parametru **countrySet** můžete zadat jenom země nebo oblasti, pro které vaše aplikace potřebuje pokrytí. Výchozím chováním je hledání celého světa, který může vracet zbytečné výsledky.

5. Do oddílu **param** přidejte následující dvojici klíč/hodnota a klikněte na **Odeslat**:

    | Key | Hodnota |
    |------------------|-------------------------|
    | countrySet | USA |
  
    Výsledky jsou nyní ohraničeny kódem země a dotaz vrátí Pizza restaurací ve USA.
  
    K poskytnutí výsledků pro umístění můžete zadat dotaz na bod zájmu a použít vrácenou zeměpisnou šířku a délku ve volání služby pro vyhledávání přibližných hodnot. V tomto případě jste použili službu Search, která vrátí umístění ručičky prostoru Seattle a použila lat. / lon. hodnoty pro orientaci hledání
  
6. V části params zadejte následující páry klíč/hodnota a klikněte na **Odeslat**:

    ![Hledání přibližných výsledků](./media/how-to-search-for-address/fuzzy_search_latlon.png)
  
    | Key | Hodnota |
    |-----|------------|
    | připojí | 47,620525 |
    | lon | -122,349274 |


## <a name="search-for-a-street-address-using-reverse-address-search"></a>Hledání adresy ulice pomocí zpětného vyhledávání adres

Azure Maps [získat reverzní rozhraní API]( https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) pro překládání se souřadnicemi (příklad: 37,786505,-122,3862) na adresu, kterou si můžete snáze přeložit. Nejčastěji to je potřeba ve sledovacích aplikacích, kde obdržíte informační kanál GPS ze zařízení nebo assetu a chcete znát adresu, kde se nachází souřadnice.
Pokud máte k dispozici sadu souřadnicových umístění, můžete pomocí [rozhraní API pro reverzní vyhledávání](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressreversebatch) odeslat dávku dotazů v jednom volání rozhraní API.


1. V příspěvku klikněte na **nový požadavek**  |  **získat žádost** a pojmenujte ho pro **zpětné vyhledávání adres**.

2. Na kartě tvůrce vyberte metodu **Get** http a zadejte adresu URL žádosti pro koncový bod rozhraní API.
  
    ![Adresa URL pro hledání zpětné adresy](./media/how-to-search-for-address/reverse_address_search_url.png)
  
    | Parametr | Navrhovaná hodnota |
    |---------------|------------------------------------------------|
    | Metoda HTTP | GET |
    | Adresa URL požadavku | [https://atlas.microsoft.com/search/address/reverse/json?](https://atlas.microsoft.com/search/address/reverse/json?) |
    | Autorizace | Bez ověřování |
  
3. Klikněte na **parametry**a zadejte následující páry klíč/hodnota, které se použijí jako parametry dotazu nebo cesty v adrese URL požadavku:
  
    ![Parametry vyhledávání zpětné adresy](./media/how-to-search-for-address/reverse_address_search_params.png)
  
    | Key | Hodnota |
    |------------------|-------------------------|
    | verze-api | 1.0 |
    | předplatné – klíč | \<your Azure Maps key\> |
    | query | 47.591180,-122,332700 |
  
4. Klikněte na **Odeslat** a zkontrolujte text odpovědi.

    Odpověď obsahuje klíčová informace o adrese pole Safeco Field.
  
5. Do oddílu **param** přidejte následující dvojici klíč/hodnota a klikněte na **Odeslat**:

    | Key | Hodnota |
    |-----|------------|
    | číslo | true |

    Pokud je parametr dotazu [Number](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) odeslán s požadavkem, může odpověď zahrnovat stranu ulice (vlevo nebo vpravo) a také pozici posunu pro toto číslo.
  
6. Do oddílu **param** přidejte následující dvojici klíč/hodnota a klikněte na **Odeslat**:

    | Key | Hodnota |
    |-----|------------|
    | returnSpeedLimit | true |
  
    Pokud je nastaven parametr dotazu [returnSpeedLimit](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) , odpověď vrátí vyslaný limit rychlosti.

7. Do oddílu **param** přidejte následující dvojici klíč/hodnota a klikněte na **Odeslat**:

    | Key | Hodnota |
    |-----|------------|
    | returnRoadUse | true |

    Pokud je nastaven parametr dotazu [returnRoadUse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) , vrátí odpověď pole pro použití pro reverzní mikrokódování na úrovni ulice.

8. Do oddílu **param** přidejte následující dvojici klíč/hodnota a klikněte na **Odeslat**:

    | Key | Hodnota |
    |-----|------------|
    | roadUse | true |

    Můžete omezit dotaz na reverzní mikrokódování na konkrétní typ cesty pomocí parametru dotazu [roadUse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) .
  
## <a name="search-for-cross-street-using-reverse-address-cross-street-search"></a>Hledání meziulici pomocí zpětného vyhledávání přes meziulici

1. V příspěvku klikněte na **nový požadavek**  |  **získat žádost** a pojmenujte ho **obrátit na hledání mezi ulice**.

2. Na kartě tvůrce vyberte metodu **Get** http a zadejte adresu URL žádosti pro koncový bod rozhraní API.
  
    ![Hledání zpětné adresy v meziulici](./media/how-to-search-for-address/reverse_address_search_url.png)
  
    | Parametr | Navrhovaná hodnota |
    |---------------|------------------------------------------------|
    | Metoda HTTP | GET |
    | Adresa URL požadavku | [https://atlas.microsoft.com/search/address/reverse/crossstreet/json?](https://atlas.microsoft.com/search/address/reverse/crossstreet/json?) |
    | Autorizace | Bez ověřování |
  
3. Klikněte na **parametry**a zadejte následující páry klíč/hodnota, které se použijí jako parametry dotazu nebo cesty v adrese URL požadavku:
  
    | Key | Hodnota |
    |------------------|-------------------------|
    | verze-api | 1.0 |
    | předplatné – klíč | \<your Azure Maps key\> |
    | query | 47.591180,-122,332700 |
  
4. Klikněte na **Odeslat** a zkontrolujte text odpovědi.

## <a name="next-steps"></a>Další kroky

- Prozkoumejte [dokumentaci Azure Maps Search Service REST API](https://docs.microsoft.com/rest/api/maps/search).
- Seznamte se s [Azure Maps Search Service osvědčenými postupy](https://docs.microsoft.com/azure/azure-maps/how-to-use-best-practices-for-search) a s postupem optimalizace vašich dotazů.
