---
title: Vyhledat umístění pomocí služby Azure Maps Search Services
description: Přečtěte si o službě Azure Maps Search. Podívejte se, jak používat tuto sadu rozhraní API pro účely geografického kódování, reverzního geografického kódování, přibližného vyhledávání a obráceného hledání mezi ulice.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/21/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 2a322de383194f131395629d33456d7561397eb9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91310981"
---
# <a name="search-for-a-location-using-azure-maps-search-services"></a>Vyhledat umístění pomocí služby Azure Maps Search Services

[Azure Maps Search Service](https://docs.microsoft.com/rest/api/maps/search) je sada rozhraní API pro RESTful navržená tak, aby vývojářům usnadnila hledání adres, míst a obchodních seznamů podle názvu, kategorie a dalších geografických informací. Kromě podpory tradičního geografického kódování můžou služby také přesměrovat adresy geografického kódu a meziulic na základě Latitudes a délky. Hodnoty zeměpisné šířky a délky vrácené hledáním se dají použít jako parametry v jiných službách Azure Maps, jako jsou třeba [trasy](https://docs.microsoft.com/rest/api/maps/route) a [povětrnostní](https://docs.microsoft.com/rest/api/maps/weather) služby.

V tomto článku se dozvíte, jak:

* Vyžádá souřadnice zeměpisné šířky a délky pro adresu (umístění adresy geografického kódu) pomocí [rozhraní API pro hledání adresy]( https://docs.microsoft.com/rest/api/maps/search/getsearchaddress).
* Vyhledejte adresu nebo bod zájmu (POI) pomocí [rozhraní API pro hledání přibližné](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy).
* Chcete-li přeložit polohu souřadnic na ulici, proveďte [hledání zpětného překladu adres](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) .
* Přeloží umístění souřadnic do uživatelsky srozumitelnější meziulice pomocí [hledané adresy rozhraní API pro reverzní meziulici](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreversecrossstreet).  Nejčastěji to je potřeba při sledování aplikací, které přijímají informační kanál GPS ze zařízení nebo z assetu, a chcete zjistit, kde se nachází souřadnice.

## <a name="prerequisites"></a>Požadavky

1. [Vytvořit účet Azure Maps](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Získejte primární klíč předplatného](quick-demo-map-app.md#get-the-primary-key-for-your-account), označovaný také jako primární klíč nebo klíč předplatného.

V tomto kurzu se používá aplikace [po](https://www.postman.com/) aplikaci, ale můžete zvolit jiné vývojové prostředí API.

## <a name="request-latitude-and-longitude-for-an-address-geocoding"></a>Vyžádat zeměpisnou šířku a zeměpisnou délku adresy (geografické kódování)

V tomto příkladu použijeme rozhraní API pro Azure Maps [získat adresu pro hledání](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) k převedení adresy na souřadnice zeměpisné šířky a délky. Tento proces se označuje také jako *geografické kódování*. Kromě vrácení souřadnic vrátí odpověď také podrobné vlastnosti adresy, jako je ulice, PSČ, okres a informace o zemi nebo oblasti.

>[!TIP]
>Pokud máte sadu adres pro účely INCODE, můžete k odeslání dávky dotazů v jednom volání rozhraní API použít [rozhraní API pro dávku po vyhledání](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressbatch) .

1. Otevřete aplikaci pro vyúčtování. V horní části okna po aplikaci vyberte **Nový**. V okně **vytvořit nové** vyberte **kolekce**.  Pojmenujte kolekci a vyberte tlačítko **vytvořit** . Tuto kolekci použijete pro zbytek příkladů v tomto dokumentu.

2. Pokud chcete vytvořit žádost, vyberte **Nový** znovu. V okně **vytvořit nové** vyberte **požadavek**. Zadejte **název žádosti** . Vyberte kolekci, kterou jste vytvořili v předchozím kroku, a pak vyberte **Uložit**.

3. Na kartě tvůrce vyberte metodu **Get** http a zadejte následující adresu URL. V této žádosti hledáme konkrétní adresu: `400 Braod St, Seattle, WA 98109` .

    U této žádosti a dalších žádostí uvedených v tomto článku nahraďte `{Azure-Maps-Primary-Subscription-key}` primárním klíčem předplatného. Požadavek by měl vypadat jako na následující adrese URL:

    ```http
    https://atlas.microsoft.com/search/address/json?&subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0&language=en-US&query=400 Broad St, Seattle, WA 98109
    ```

4. Klikněte na modré tlačítko **Odeslat** . Tělo odpovědi bude obsahovat data pro jedno umístění.

5. Teď prohledáme adresu, která má více než jedno možná umístění. V oddílu **param** změňte `query` klíč na `400 Broad, Seattle` . Klikněte na modré tlačítko **Odeslat** .

    :::image type="content" source="./media/how-to-search-for-address/search-address.png" alt-text="Hledat adresu":::

6. Dále zkuste nastavit `query` klíč na `400 Broa` .

7. Klikněte na tlačítko **Odeslat** .  Nyní vidíte, že odpověď zahrnuje odpovědi z více zemí. Chcete-li výsledky geografického posunu do příslušné oblasti pro uživatele, vždy do žádosti přidat tolik podrobností o umístění.

## <a name="using-fuzzy-search-api"></a>Použití rozhraní API pro vyhledávání s fuzzy logikou

[Rozhraní API pro přibližné vyhledávání](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) Azure Maps podporuje standardní hledání na jednom řádku a volném formuláři. Pokud neznáte typ uživatelského vstupu pro požadavek hledání, doporučujeme, abyste používali Azure Maps prohledat přibližné rozhraní API.  Vstup dotazu může být úplná nebo částečná adresa. Může to být také token POI (Point of Interest), jako je název POI, kategorie POI nebo název značky. Kromě toho pro zlepšení relevance výsledků hledání může být výsledky dotazu omezené pomocí umístění souřadnic a poloměru, případně definováním ohraničovacího rámečku.

>[!TIP]
>Většina vyhledávacích dotazů je ve výchozím nastavení nastavena na maxFuzzyLevel = 1, aby získala výkon a snížila neobvyklé výsledky. Úrovně tomu můžete upravit pomocí `maxFuzzyLevel` `minFuzzyLevel` parametrů nebo. Další informace `maxFuzzyLevel` a úplný seznam všech volitelných parametrů najdete v tématu [parametry identifikátoru URI pro hledání přibližné vyhledávání](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy#uri-parameters) .

### <a name="search-for-an-address-using-fuzzy-search"></a>Hledání adresy pomocí hledání přibližných výsledků

V tomto příkladu použijeme přibližné hledání k prohledání celého světa pro `pizza` .  Pak vám ukážeme, jak hledat v oboru konkrétní země. Nakonec vám ukážeme, jak používat umístění souřadnic a poloměr k určení oboru hledání v konkrétní oblasti a omezení počtu vrácených výsledků.

>[!IMPORTANT]
>Pokud chcete výsledky geografického posunu do příslušné oblasti pro vaše uživatele, vždy přidejte tolik podrobností umístění, kolik je možné. Další informace najdete v tématu [osvědčené postupy pro hledání](how-to-use-best-practices-for-search.md#geobiased-search-results).

1. Otevřete aplikaci pro odesílání, klikněte na **Nový**a vyberte **žádost**. Zadejte **název žádosti** . Vyberte kolekci, kterou jste vytvořili v předchozí části, nebo vytvořte novou, a pak vyberte **Uložit**.

2. Na kartě tvůrce vyberte metodu **Get** http a zadejte následující adresu URL. U této žádosti a dalších žádostí uvedených v tomto článku nahraďte `{Azure-Maps-Primary-Subscription-key}` primárním klíčem předplatného. Požadavek by měl vypadat jako na následující adrese URL:

    ```http
   https://atlas.microsoft.com/search/fuzzy/json?&api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}&language=en-US&query=pizza
    ```

    >[!NOTE]
    >Atribut _JSON_ v cestě URL určuje formát odpovědi. V tomto článku se používá JSON pro snadné použití a čitelnost. Další podporované formáty odpovědí najdete v části `format` definice parametru v [referenční dokumentaci k parametru identifikátoru URI](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy#uri-parameters).

3. Klikněte na **Odeslat** a zkontrolujte text odpovědi.

    Nejednoznačný řetězec dotazu pro "pizza" vrátil 10 [bodů výsledku zájmu](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi#searchpoiresponse) (POI) v kategoriích "pizza" i "restaurace". Každý výsledek obsahuje podrobnosti, jako je adresa ulice, hodnota zeměpisné šířky a délky, zobrazení portu a vstupní body pro dané umístění. Výsledky jsou nyní pro tento dotaz proměnlivé a nejsou vázané na žádné referenční místo.
  
    V dalším kroku použijeme `countrySet` parametr k určení pouze zemí nebo oblastí, pro které vaše aplikace potřebuje pokrytí. Úplný seznam podporovaných zemí nebo oblastí najdete v tématu věnovaném [vyhledávání pokrytí](https://docs.microsoft.com/azure/azure-maps/geocoding-coverage).

4. Výchozím chováním je hledání celého světa, který může vracet zbytečné výsledky. V dalším kroku vyhledáme jenom Pizza USA. Přidejte `countrySet` klíč do oddílu **param** a nastavte jeho hodnotu na `US` . Nastavením `countrySet` klíče se `US` naváže výsledky k USA.

    :::image type="content" source="./media/how-to-search-for-address/search-fuzzy-country.png" alt-text="Hledat adresu":::

    Výsledky jsou nyní ohraničeny kódem země a dotaz vrátí Pizza restaurací ve USA.

5. Chcete-li získat ještě více cílené vyhledávání, můžete hledat v oboru lat./lon. sjednotit dvojici V tomto příkladu použijeme lat./lon.. Ručička místa v Seattlu. Vzhledem k tomu, že chceme vracet pouze výsledky v rámci poloměru 400 měřičů, přidáme `radius` parametr. Také přidáme `limit` parametr pro omezení výsledků na pět nejbližších Pizza míst.

    V části **params** přidejte následující páry klíč/hodnota:

     | Klíč | Hodnota |
    |-----|------------|
    | připojí | 47,620525 |
    | lon | -122,349274 |
    | zaoblen | 400 |
    | počtu | 5|

6. Klikněte na **Odeslat**. Odpověď zahrnuje výsledky pro Pizza restaurací poblíž ručičky místa v Seattlu.

## <a name="search-for-a-street-address-using-reverse-address-search"></a>Hledání adresy ulice pomocí zpětného vyhledávání adres

[Rozhraní API pro reverzní adresování Azure Maps získat zpětné vyhledávání]( https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) . Toto rozhraní API se často používá pro aplikace, které využívají kanály GPS a chtějí zjišťovat adresy v konkrétních souřadnicových bodech.

>[!IMPORTANT]
>Pokud chcete výsledky geografického posunu do příslušné oblasti pro vaše uživatele, vždy přidejte tolik podrobností umístění, kolik je možné. Další informace najdete v tématu [osvědčené postupy pro hledání](how-to-use-best-practices-for-search.md#geobiased-search-results).

>[!TIP]
>Pokud máte k dispozici sadu souřadnicových umístění, můžete pomocí [rozhraní API pro reverzní vyhledávání](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressreversebatch) odeslat dávku dotazů v jednom volání rozhraní API.

V tomto příkladu provedeme zpětné vyhledávání pomocí několika volitelných parametrů, které jsou k dispozici. Úplný seznam volitelných parametrů najdete v tématu [parametry zpětného vyhledávání](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse#uri-parameters).

1. V aplikaci pro odesílání klikněte na **Nový**a vyberte **požadavek**. Zadejte **název žádosti** . Vyberte kolekci, kterou jste vytvořili v první části, nebo vytvořte novou, a pak vyberte **Uložit**.

2. Na kartě tvůrce vyberte metodu **Get** http a zadejte následující adresu URL. U této žádosti a dalších žádostí uvedených v tomto článku nahraďte `{Azure-Maps-Primary-Subscription-key}` primárním klíčem předplatného. Požadavek by měl vypadat jako na následující adrese URL:

    ```http
    https://atlas.microsoft.com/search/address/reverse/json?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}&language=en-US&query=47.591180,-122.332700&number=1
    ```

3. Klikněte na **Odeslat**a zkontrolujte text odpovědi. Měl by se zobrazit jeden výsledek dotazu. Odpověď obsahuje klíčová informace o adrese pole Safeco Field.
  
4. Nyní přidáme následující páry klíč/hodnota do oddílu **param** :

    | Klíč | Hodnota | Návraty
    |-----|------------|------|
    | číslo | 1 |Odpověď může zahrnovat stranu ulice (vlevo/vpravo) a také pozici posunu pro dané číslo.|
    | returnSpeedLimit | true | Vrátí omezení rychlosti na adrese.|
    | returnRoadUse | true | Vrátí na adrese cesty pro použití. Všechny možné typy provozu najdete v tématu [typy cest pro použití](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse#uri-parameters).|
    | returnMatchType | true| Vrátí typ shody. Všechny možné hodnoty najdete v tématu s [výsledky hledání zpětné adresy](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse#searchaddressreverseresult) .

   :::image type="content" source="./media/how-to-search-for-address/search-reverse.png" alt-text="Hledat adresu":::

5. Klikněte na **Odeslat**a zkontrolujte text odpovědi.

6. V dalším kroku přidáme `entityType` klíč a nastavíme jeho hodnotu na `Municipality` . `entityType`Klíč přepíše `returnMatchType` klíč v předchozím kroku. Bude také nutné odebrat `returnSpeedLimit` a, `returnRoadUse` protože požadujeme informace o úřadu.  Všechny možné typy entit naleznete v tématu [typy entit](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse#entitytype).

    :::image type="content" source="./media/how-to-search-for-address/search-reverse-entity-type.png" alt-text="Hledat adresu":::

7. Klikněte na **Odeslat**. Porovnejte výsledky s výsledky vrácenými v kroku 5.  Vzhledem k tomu, že požadovaný typ entity je nyní `municipality` , odpověď neobsahuje informace o ulici. Vrácené `geometryId` hodnoty lze také použít k vyžádání mnohoúhelníku hranice prostřednictvím Azure Maps získat [rozhraní API pro mnohoúhelník vyhledávání](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon).

>[!TIP]
>Chcete-li získat další informace o těchto parametrech a získat informace o dalších informacích, přečtěte si [část parametry zpětného vyhledávání](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse#uri-parameters).

## <a name="search-for-cross-street-using-reverse-address-cross-street-search"></a>Hledání meziulici pomocí zpětného vyhledávání přes meziulici

V tomto příkladu vyhledáme meziulici na základě souřadnic adresy.

1. V aplikaci pro odesílání klikněte na **Nový**a vyberte **požadavek**. Zadejte **název žádosti** . Vyberte kolekci, kterou jste vytvořili v první části, nebo vytvořte novou, a pak vyberte **Uložit**.

2. Na kartě tvůrce vyberte metodu **Get** http a zadejte následující adresu URL. U této žádosti a dalších žádostí uvedených v tomto článku nahraďte `{Azure-Maps-Primary-Subscription-key}` primárním klíčem předplatného. Požadavek by měl vypadat jako na následující adrese URL:
  
    ```http
   https://atlas.microsoft.com/search/address/reverse/crossstreet/json?&api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}&language=en-US&query=47.591180,-122.332700
    ```

    :::image type="content" source="./media/how-to-search-for-address/search-address-cross.png" alt-text="Hledat adresu":::
  
3. Klikněte na **Odeslat**a zkontrolujte text odpovědi. Všimněte si, že odpověď obsahuje `crossStreet` hodnotu `Occidental Avenue South` .

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Azure Maps Search Service REST API](https://docs.microsoft.com/rest/api/maps/search)

> [!div class="nextstepaction"]
> [Osvědčené postupy Azure Maps Search Service](how-to-use-best-practices-for-search.md)
