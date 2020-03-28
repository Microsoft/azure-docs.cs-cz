---
title: 'Kurz: Poznámkový blok Azure – personalizátor'
titleSuffix: Azure Cognitive Services
description: Tento kurz simuluje smyčky personalizace _system v poznámkovém bloku Azure, což naznačuje, jaký typ kávy by měl zákazník objednat. Uživatelé a jejich předvolby jsou uloženy v datové sadě uživatele. Informace o kávě jsou také k dispozici a uloženy v datové sadě kávy.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: tutorial
ms.date: 02/03/2020
ms.author: diberry
ms.openlocfilehash: 03e8b658f7edf4640d738e5ea3af84953185d0f5
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76986831"
---
# <a name="tutorial-use-personalizer-in-azure-notebook"></a>Kurz: Použití personalisty v poznámkovém bloku Azure

Tento kurz spustí smyčky personalikátní v poznámkovém bloku Azure, demonstrovat konec do konce životního cyklu personalizované smyčky.

Smyčka naznačuje, jaký typ kávy by si měl zákazník objednat. Uživatelé a jejich předvolby jsou uloženy v datové sadě uživatele. Informace o kávě jsou uloženy v datové sadě kávy.

## <a name="users-and-coffee"></a>Uživatelé a káva

Poznámkový blok, který simuluje interakci uživatele s webem, vybere náhodného uživatele, denní dobu a typ počasí z datové sady. Souhrn informací o uživateli je:

|Zákazníci - kontextové funkce|Denní doba|Druhy počasí|
|--|--|--|
|Alice<br>Bob<br>Cathy<br>Dave|Ráno<br>Odpoledne<br>Večer|Slunné<br>Deštivé<br>Zasněžené|

Chcete-li pomoci Personalista učit, v průběhu času, _systém_ také zná podrobnosti o výběru kávy pro každou osobu.

|Káva - akční prvky|Typy teplot|Místa původu|Druhy pečeně|Organic|
|--|--|--|--|--|
|Cappacino|Hot|Keňa|Tmavý|Organic|
|Studené pivo|Bez zájmu|Brazílie|Světlý|Organic|
|Ledová moka|Bez zájmu|Etiopie|Světlý|Není ekologické|
|Latte|Hot|Brazílie|Tmavý|Není ekologické|

**Účelem** smyčky Personalizace je najít nejlepší shodu mezi uživateli a kávou co nejvíce času.

Kód pro tento kurz je k dispozici v [personalizované ukázky GitHub úložiště](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/tree/master/samples/azurenotebook).

## <a name="how-the-simulation-works"></a>Jak simulace funguje

Na začátku běžícího systému jsou návrhy od Personalisty úspěšné pouze mezi 20% až 30%. Tento úspěch je indikován odměnou zaslanou zpět do rozhraní Personalizer's Reward API se skóre 1. Po některých rankových a bonusových hovorech se systém zlepšuje.

Po počátečních požadavcích spusťte offline vyhodnocení. To umožňuje personalista zkontrolovat data a navrhnout lepší zásady učení. Použijte nové zásady učení a znovu spusťte poznámkový blok s 20 % předchozího počtu požadavků. Smyčka bude fungovat lépe s novou politikou učení.

## <a name="rank-and-reward-calls"></a>Volání o hodnocení a odměnu

Pro každý z několika tisíc volání služby Personalikátor Azure poznámkový blok odešle požadavek **pořadí** na rozhraní REST API:

* Jedinečné ID události Rank/Request
* Kontextové funkce - Náhodná volba uživatele, počasí a denní doby - simulace uživatele na webových stránkách nebo mobilním zařízení
* Akce s funkcemi - _Všechna_ data kávy - ze kterého personalizace dělá návrh

Systém obdrží požadavek, pak porovná tuto předpověď se známou volbou uživatele pro stejnou denní dobu a počasí. Pokud je známá volba stejná jako předpovídaná volba, **odměna 1** je odeslána zpět personalistovi. V opačném případě je odměna odeslaná zpět 0.

> [!Note]
> Jedná se o simulaci, takže algoritmus pro odměnu je jednoduchý. V reálném scénáři algoritmus by měl používat obchodní logiku, případně s váhami pro různé aspekty zkušeností zákazníka, k určení skóre odměny.


## <a name="prerequisites"></a>Požadavky

* Účet [poznámkového bloku Azure.](https://notebooks.azure.com/)
* Prostředek [Personalisty Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer).
    * Pokud jste už použili prostředek personalizace, ujistěte se, že [vymazat data](how-to-settings.md#clear-data-for-your-learning-loop) na portálu Azure pro prostředek.
* Nahrajte všechny soubory pro [tuto ukázku](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/tree/master/samples/azurenotebook) do projektu Azure Notebook.

Popisy souborů:

* [Personalizer.ipynb](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/samples/azurenotebook/Personalizer.ipynb) je Jupyter notebook pro tento výukový program.
* [Uživatelská datová sada](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/samples/azurenotebook/users.json) je uložena v objektu JSON.
* [Datová sada kávy](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/samples/azurenotebook/coffee.json) je uložena v objektu JSON.
* [Příklad Požadavku JSON](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/samples/azurenotebook/example-rankrequest.json) je očekávaný formát pro požadavek POST na rank API.

## <a name="configure-personalizer-resource"></a>Konfigurovat prostředek personalikátora

Na webu Azure Portal nakonfigurujte [prostředek personalisty](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer) s **frekvencí modelu aktualizace** nastavenou na 15 sekund a dobou čekání **odměny** 15 sekund. Tyto hodnoty se nacházejí na stránce **[Konfigurace.](how-to-settings.md#configure-service-settings-in-the-azure-portal)**

|Nastavení|Hodnota|
|--|--|
|aktualizovat frekvenci modelu|15 sekund|
|doba čekání na odměnu|15 sekund|

Tyto hodnoty mají velmi krátkou dobu trvání, aby bylo možné zobrazit změny v tomto kurzu. Tyto hodnoty by neměly být použity v produkčním scénáři bez ověření, že dosáhnou vašeho cíle pomocí smyčky personalizátoru.

## <a name="set-up-the-azure-notebook"></a>Nastavení poznámkového bloku Azure

1. Změňte jádro `Python 3.6`na .
1. Otevřete soubor `Personalizer.ipynb`.

## <a name="run-notebook-cells"></a>Spuštění buněk poznámkového bloku

Spusťte každou spustitelnou buňku a počkejte, až se vrátí. Víte, že se provádí, když závorky vedle `*`buňky zobrazí číslo namísto . V následujících částech je vysvětleno, co každá buňka dělá programově a co lze očekávat pro výstup.

### <a name="include-the-python-modules"></a>Zahrnout moduly pythonu

Zahrňte požadované moduly pythonu. Buňka nemá žádný výstup.

```python
import json
import matplotlib.pyplot as plt
import random
import requests
import time
import uuid
```

### <a name="set-personalizer-resource-key-and-name"></a>Nastavení klíče a názvu prostředku personalizátoru

Na webu Azure Portal najdete klíč a koncový bod na stránce **Rychlého startu** prostředku personalisty. Změňte hodnotu `<your-resource-name>` na název prostředku personalisty. Změňte hodnotu klávesy `<your-resource-key>` Personalizátor.

```python
# Replace 'personalization_base_url' and 'resource_key' with your valid endpoint values.
personalization_base_url = "https://<your-resource-name>.cognitiveservices.azure.com/"
resource_key = "<your-resource-key>"
```

### <a name="print-current-date-and-time"></a>Tisk aktuálního data a času
Pomocí této funkce můžete zaznamenat počáteční a koncový čas iterativní funkce, iterací.

Tyto buňky nemají žádný výstup. Funkce nemá výstup aktuální datum a čas při volání.

```python
# Print out current datetime
def currentDateTime():
    currentDT = datetime.datetime.now()
    print (str(currentDT))
```

### <a name="get-the-last-model-update-time"></a>Získat čas poslední aktualizace modelu

Při volání `get_last_updated`funkce , funkce vytiskne datum a čas poslední změny, kdy byl model aktualizován.

Tyto buňky nemají žádný výstup. Funkce nemá výstup poslední model trénování datum při volání.

Funkce používá rozhraní GET REST API k [získání vlastností modelu](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/GetModelProperties).

```python
# ititialize variable for model's last modified date
modelLastModified = ""
```

```python
def get_last_updated(currentModifiedDate):

    print('-----checking model')

    # get model properties
    response = requests.get(personalization_model_properties_url, headers = headers, params = None)

    print(response)
    print(response.json())

    # get lastModifiedTime
    lastModifiedTime = json.dumps(response.json()["lastModifiedTime"])

    if (currentModifiedDate != lastModifiedTime):
        currentModifiedDate = lastModifiedTime
        print(f'-----model updated: {lastModifiedTime}')
```

### <a name="get-policy-and-service-configuration"></a>Získání konfigurace zásad a služeb

Ověřte stav služby pomocí těchto dvou volání REST.

Tyto buňky nemají žádný výstup. Funkce nemá výstup hodnoty služby při volání.

```python
def get_service_settings():

    print('-----checking service settings')

    # get learning policy
    response = requests.get(personalization_model_policy_url, headers = headers, params = None)

    print(response)
    print(response.json())

    # get service settings
    response = requests.get(personalization_service_configuration_url, headers = headers, params = None)

    print(response)
    print(response.json())
```

### <a name="construct-urls-and-read-json-data-files"></a>Vytváření adres URL a čtení datových souborů JSON

Tato buňka

* vytvoří adresy URL používané ve volání REST
* Nastaví záhlaví zabezpečení pomocí klíče prostředku personalizátoru
* nastaví náhodné osivo pro ID události pořadí
* čte v datových souborech JSON
* metoda `get_last_updated` volání - zásady učení byly odstraněny v ukázkovém výstupu
* metoda `get_service_settings` volání

Buňka má výstup z `get_last_updated` `get_service_settings` volání a funkce.

```python
# build URLs
personalization_rank_url = personalization_base_url + "personalizer/v1.0/rank"
personalization_reward_url = personalization_base_url + "personalizer/v1.0/events/" #add "{eventId}/reward"
personalization_model_properties_url = personalization_base_url + "personalizer/v1.0/model/properties"
personalization_model_policy_url = personalization_base_url + "personalizer/v1.0/configurations/policy"
personalization_service_configuration_url = personalization_base_url + "personalizer/v1.0/configurations/service"

headers = {'Ocp-Apim-Subscription-Key' : resource_key, 'Content-Type': 'application/json'}

# context
users = "users.json"

# action features
coffee = "coffee.json"

# empty JSON for Rank request
requestpath = "example-rankrequest.json"

# initialize random
random.seed(time.time())

userpref = None
rankactionsjsonobj = None
actionfeaturesobj = None

with open(users) as handle:
    userpref = json.loads(handle.read())

with open(coffee) as handle:
    actionfeaturesobj = json.loads(handle.read())

with open(requestpath) as handle:
    rankactionsjsonobj = json.loads(handle.read())

get_last_updated(modelLastModified)
get_service_settings()

print(f'User count {len(userpref)}')
print(f'Coffee count {len(actionfeaturesobj)}')
```

Ověřte, zda `rewardWaitTime` `modelExportFrequency` výstup a jsou nastaveny na 15 sekund.

```console
-----checking model
<Response [200]>
{'creationTime': '0001-01-01T00:00:00+00:00', 'lastModifiedTime': '0001-01-01T00:00:00+00:00'}
-----model updated: "0001-01-01T00:00:00+00:00"
-----checking service settings
<Response [200]>
{...learning policy...}
<Response [200]>
{'rewardWaitTime': '00:00:15', 'defaultReward': 0.0, 'rewardAggregation': 'earliest', 'explorationPercentage': 0.2, 'modelExportFrequency': '00:00:15', 'logRetentionDays': -1}
User count 4
Coffee count 4
```

### <a name="troubleshooting-the-first-rest-call"></a>Poradce při potížích s prvním voláním REST

Tato předchozí buňka je první buňka, která volá personalistu. Ujistěte se, že stavový `<Response [200]>`kód REST ve výstupu je . Pokud se zobrazí chyba, například 404, ale jste si jisti, že klíč prostředku a název jsou správné, znovu načtěte poznámkový blok.

Ujistěte se, že počet kávy a uživatelů je oba 4. Pokud se zobrazí chyba, zkontrolujte, zda jste nahráli všechny 3 soubory JSON.

### <a name="set-up-metric-chart-in-azure-portal"></a>Nastavení grafu metrik na webu Azure Portal

Později v tomto kurzu je z prohlížeče s aktualizovaným textovým polem viditelný dlouho běžící proces 10 000 požadavků. Může být snazší vidět v grafu nebo jako celkový součet, když dlouho běžící proces skončí. Chcete-li tyto informace zobrazit, použijte metriky poskytnuté s prostředkem. Graf můžete vytvořit nyní, když jste dokončili požadavek na službu, a pak graf pravidelně aktualizovat, zatímco dlouhodobý proces probíhá.

1. Na webu Azure Portal vyberte prostředek personalisty.
1. V navigaci prostředků vyberte **metriky** pod sledováním.
1. V grafu vyberte **Přidat metriku**.
1. Obor názvů prostředků a metriky jsou již nastaveny. Stačí vybrat metriku **úspěšných hovorů** a agregaci **součtu**.
1. Změňte časový filtr na poslední 4 hodiny.

    ![Nastavte na webu Azure Portal graf a přidejte metriku pro úspěšná volání za poslední 4 hodiny.](./media/tutorial-azure-notebook/metric-chart-setting.png)

    V grafu by se měly zobrazit tři úspěšné hovory.

### <a name="generate-a-unique-event-id"></a>Generovat jedinečné ID události

Tato funkce generuje jedinečné ID pro každé volání pořadí. ID se používá k identifikaci informací o pořadí a odměně. Tato hodnota může pocházet z obchodního procesu, jako je například ID webového zobrazení nebo ID transakce.

Buňka nemá žádný výstup. Funkce má výstup jedinečné ID při volání.

```python
def add_event_id(rankjsonobj):
    eventid = uuid.uuid4().hex
    rankjsonobj["eventId"] = eventid
    return eventid
```

### <a name="get-random-user-weather-and-time-of-day"></a>Získejte náhodného uživatele, počasí a denní dobu

Tato funkce vybere jedinečného uživatele, počasí a denní dobu a potom tyto položky přidá do objektu JSON, který se odešle do požadavku pořadí.

Buňka nemá žádný výstup. Když je funkce volána, vrátí jméno náhodného uživatele, náhodné počasí a náhodný čas dne.

Seznam 4 uživatelů a jejich preference - pouze některé preference jsou zobrazeny pro stručnost:

```json
{
  "Alice": {
    "Sunny": {
      "Morning": "Cold brew",
      "Afternoon": "Iced mocha",
      "Evening": "Cold brew"
    }...
  },
  "Bob": {
    "Sunny": {
      "Morning": "Cappucino",
      "Afternoon": "Iced mocha",
      "Evening": "Cold brew"
    }...
  },
  "Cathy": {
    "Sunny": {
      "Morning": "Latte",
      "Afternoon": "Cold brew",
      "Evening": "Cappucino"
    }...
  },
  "Dave": {
    "Sunny": {
      "Morning": "Iced mocha",
      "Afternoon": "Iced mocha",
      "Evening": "Iced mocha"
    }...
  }
}
```

```python
def add_random_user_and_contextfeatures(namesoption, weatheropt, timeofdayopt, rankjsonobj):
    name = namesoption[random.randint(0,3)]
    weather = weatheropt[random.randint(0,2)]
    timeofday = timeofdayopt[random.randint(0,2)]
    rankjsonobj['contextFeatures'] = [{'timeofday': timeofday, 'weather': weather, 'name': name}]
    return [name, weather, timeofday]
```


### <a name="add-all-coffee-data"></a>Přidat všechna data kávy

Tato funkce přidá celý seznam kávy do objektu JSON odeslat rank požadavku.

Buňka nemá žádný výstup. Funkce změní při `rankjsonobj` volání.


Příkladem funkcí jedné kávy je:

```json
{
    "id": "Cappucino",
    "features": [
    {
        "type": "hot",
        "origin": "kenya",
        "organic": "yes",
        "roast": "dark"

    }
}
```

```python
def add_action_features(rankjsonobj):
    rankjsonobj["actions"] = actionfeaturesobj
```

### <a name="compare-prediction-with-known-user-preference"></a>Porovnat předpověď se známými uživatelskými předvolbami

Tato funkce je volána po rank API je volána, pro každou iteraci.

Tato funkce porovnává uživatele preference pro kávu, na základě počasí a denní doby, s personalizátní k návrhu pro uživatele pro tyto filtry. Pokud se návrh shoduje, je vráceno skóre 1, jinak je skóre 0. Buňka nemá žádný výstup. Funkce má výstup skóre při volání.

```python
def get_reward_from_simulated_data(name, weather, timeofday, prediction):
    if(userpref[name][weather][timeofday] == str(prediction)):
        return 1
    return 0
```

### <a name="loop-through-calls-to-rank-and-reward"></a>Smyčka prostřednictvím volání na hodnost a odměnu

Další buňka je _hlavní_ práce notebooku, získání náhodného uživatele, získání seznamu kávy, odeslání obou do Rank API. Porovnání matné předpovědi se známými předvolbami uživatele a odeslání matné odměny zpět do služby Personalista.

Smyčka běží `num_requests` na časy. Personalizár potřebuje několik tisíc hovorů na Rank a Odměna k vytvoření modelu.

Následuje příklad JSON odeslaného do rozhraní API hodnosti. Seznam kávy není úplný, pro stručnost. Můžete vidět celý JSON na `coffee.json`kávu v .

JSON odeslána do Rank API:

```json
{
   'contextFeatures':[
      {
         'timeofday':'Evening',
         'weather':'Snowy',
         'name':'Alice'
      }
   ],
   'actions':[
      {
         'id':'Cappucino',
         'features':[
            {
               'type':'hot',
               'origin':'kenya',
               'organic':'yes',
               'roast':'dark'
            }
         ]
      }
        ...rest of coffee list
   ],
   'excludedActions':[

   ],
   'eventId':'b5c4ef3e8c434f358382b04be8963f62',
   'deferActivation':False
}
```

Odpověď JSON z rozhraní RANK API:

```json
{
    'ranking': [
        {'id': 'Latte', 'probability': 0.85 },
        {'id': 'Iced mocha', 'probability': 0.05 },
        {'id': 'Cappucino', 'probability': 0.05 },
        {'id': 'Cold brew', 'probability': 0.05 }
    ],
    'eventId': '5001bcfe3bb542a1a238e6d18d57f2d2',
    'rewardActionId': 'Latte'
}
```

Nakonec každá smyčka zobrazuje náhodný výběr uživatele, počasí, denní dobu a určenou odměnu. Odměna 1 označuje, že zdroj Personalista vybral správný typ kávy pro daného uživatele, počasí a denní dobu.

```console
1 Alice Rainy Morning Latte 1
```

Funkce používá:

* Pořadí: POST REST API [získat hodnost](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank).
* Odměna: POST REST API [pro nahlášení odměny](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward).

```python
def iterations(n, modelCheck, jsonFormat):

    i = 1

    # default reward value - assumes failed prediction
    reward = 0

    # Print out dateTime
    currentDateTime()

    # collect results to aggregate in graph
    total = 0
    rewards = []
    count = []

    # default list of user, weather, time of day
    namesopt = ['Alice', 'Bob', 'Cathy', 'Dave']
    weatheropt = ['Sunny', 'Rainy', 'Snowy']
    timeofdayopt = ['Morning', 'Afternoon', 'Evening']


    while(i <= n):

        # create unique id to associate with an event
        eventid = add_event_id(jsonFormat)

        # generate a random sample
        [name, weather, timeofday] = add_random_user_and_contextfeatures(namesopt, weatheropt, timeofdayopt, jsonFormat)

        # add action features to rank
        add_action_features(jsonFormat)

        # show JSON to send to Rank
        print('To: ', jsonFormat)

        # choose an action - get prediction from Personalizer
        response = requests.post(personalization_rank_url, headers = headers, params = None, json = jsonFormat)

        # show Rank prediction
        print ('From: ',response.json())

        # compare personalization service recommendation with the simulated data to generate a reward value
        prediction = json.dumps(response.json()["rewardActionId"]).replace('"','')
        reward = get_reward_from_simulated_data(name, weather, timeofday, prediction)

        # show result for iteration
        print(f'   {i} {currentDateTime()} {name} {weather} {timeofday} {prediction} {reward}')

        # send the reward to the service
        response = requests.post(personalization_reward_url + eventid + "/reward", headers = headers, params= None, json = { "value" : reward })

        # for every N rank requests, compute total correct  total
         total =  total + reward

        # every N iteration, get last updated model date and time
        if(i % modelCheck == 0):

            print("**** 10% of loop found")

            get_last_updated(modelLastModified)

        # aggregate so chart is easier to read
        if(i % 10 == 0):
            rewards.append( total)
            count.append(i)
             total = 0

        i = i + 1

    # Print out dateTime
    currentDateTime()

    return [count, rewards]
```

## <a name="run-for-10000-iterations"></a>Spustit pro 10 000 iterací
Spusťte smyčku Personalizátor pro 10 000 iterací. Jedná se o dlouho běžící událost. Nezavírejte prohlížeč se spuštěnou poznámkovým blokem. Aktualizujte graf metrik na webu Azure portal pravidelně zobrazíte celkový počet volání ke službě. Pokud máte kolem 20 000 volání, volání hodnosti a odměny pro každou iteraci smyčky, iterace jsou provedeny.

```python
# max iterations
num_requests = 200

# check last mod date N% of time - currently 10%
lastModCheck = int(num_requests * .10)

jsonTemplate = rankactionsjsonobj

# main iterations
[count, rewards] = iterations(num_requests, lastModCheck, jsonTemplate)
```



## <a name="chart-results-to-see-improvement"></a>Výsledky grafu pro zobrazení zlepšení

Vytvořte graf `count` z `rewards`a .

```python
def createChart(x, y):
    plt.plot(x, y)
    plt.xlabel("Batch of rank events")
    plt.ylabel("Correct recommendations per batch")
    plt.show()
```

## <a name="run-chart-for-10000-rank-requests"></a>Spustit graf pro 10 000 požadavků na pořadí

Spusťte `createChart` funkci.

```python
createChart(count,rewards)
```

## <a name="reading-the-chart"></a>Čtení grafu

Tento graf ukazuje úspěch modelu pro aktuální výchozí zásady učení.

![Tento graf ukazuje úspěch aktuální zásady učení po dobu trvání testu.](./media/tutorial-azure-notebook/azure-notebook-chart-results.png)


Ideální cíl, že na konci testu smyčky je v průměru úspěšnost, která se blíží 100 procent minus průzkumu. Výchozí hodnota průzkumu je 20 %.

`100-20=80`

Tato hodnota průzkumu se nachází na portálu Azure, pro prostředek personalizace, na stránce **Konfigurace.**

Chcete-li najít lepší zásady učení, na základě vašich dat do rozhraní API pořadí, spusťte [offline vyhodnocení](how-to-offline-evaluation.md) na portálu pro vaše smyčky personalizátoru.

## <a name="run-an-offline-evaluation"></a>Spuštění offline hodnocení

1. Na webu Azure Portal otevřete stránku **Hodnocení** prostředků personalisty.
1. Vyberte **možnost Vytvořit vyhodnocení**.
1. Zadejte požadovaná data názvu vyhodnocení a rozsah dat pro vyhodnocení smyčky. Časové období by mělo zahrnovat pouze dny, na které se při hodnocení zaměřujete.
    ![Na webu Azure Portal otevřete stránku Hodnocení prostředků personalisty. Vyberte Vytvořit vyhodnocení. Zadejte název hodnocení a rozsah dat.](./media/tutorial-azure-notebook/create-offline-evaluation.png)

    Účelem spuštění tohoto offline hodnocení je zjistit, zda existuje lepší zásady učení pro funkce a akce používané v této smyčce. Chcete-li najít lepší zásady učení, ujistěte se, že je **zapnuto zjišťování optimalizace.**

1. Chcete-li zahájit hodnocení, vyberte **možnost OK.**
1. Tato stránka **Hodnocení** uvádí nové hodnocení a jeho aktuální stav. V závislosti na tom, kolik dat máte, toto vyhodnocení může nějakou dobu trvat. Můžete se vrátit na tuto stránku po několika minutách vidět výsledky.
1. Po dokončení hodnocení vyberte hodnocení a pak vyberte **porovnání různých zásad učení**. To ukazuje dostupné zásady učení a jak by se chovali s daty.
1. V tabulce vyberte zásady učení, které je nejvíce nahoře, a vyberte **použít**. To platí _pro_ váš model nejlepší zásady učení a přepne.

## <a name="change-update-model-frequency-to-5-minutes"></a>Změna četnosti aktualizace modelu na 5 minut

1. Na webu Azure Portal, který je stále na prostředku personalisty, vyberte stránku **Konfigurace.**
1. Změňte **frekvenci aktualizace modelu** a **odměňte čekací dobu** na 5 minut a vyberte **Uložit**.

Přečtěte si další informace o [čekací době odměn](concept-rewards.md#reward-wait-time) y a [četnosti aktualizací modelu](how-to-settings.md#model-update-frequency).

```python
#Verify new learning policy and times
get_service_settings()
```

Ověřte, zda `rewardWaitTime` `modelExportFrequency` výstup a jsou nastaveny na 5 minut.
```console
-----checking model
<Response [200]>
{'creationTime': '0001-01-01T00:00:00+00:00', 'lastModifiedTime': '0001-01-01T00:00:00+00:00'}
-----model updated: "0001-01-01T00:00:00+00:00"
-----checking service settings
<Response [200]>
{...learning policy...}
<Response [200]>
{'rewardWaitTime': '00:05:00', 'defaultReward': 0.0, 'rewardAggregation': 'earliest', 'explorationPercentage': 0.2, 'modelExportFrequency': '00:05:00', 'logRetentionDays': -1}
User count 4
Coffee count 4
```

## <a name="validate-new-learning-policy"></a>Ověřit nové zásady učení

Vraťte se do poznámkového bloku Azure a pokračujte spuštěním stejné smyčky, ale pouze pro 2 000 iterací. Aktualizujte graf metrik na webu Azure portal pravidelně zobrazíte celkový počet volání ke službě. Pokud máte kolem 4 000 volání, volání hodnosti a odměny pro každou iteraci smyčky, iterace jsou hotové.

```python
# max iterations
num_requests = 2000

# check last mod date N% of time - currently 10%
lastModCheck2 = int(num_requests * .10)

jsonTemplate2 = rankactionsjsonobj

# main iterations
[count2, rewards2] = iterations(num_requests, lastModCheck2, jsonTemplate)
```

## <a name="run-chart-for-2000-rank-requests"></a>Spustit graf pro 2 000 požadavků na pořadí

Spusťte `createChart` funkci.

```python
createChart(count2,rewards2)
```

## <a name="review-the-second-chart"></a>Kontrola druhého grafu

Druhý graf by měl zobrazovat viditelné zvýšení předpovědi pořadí zarovnány s uživatelskými předvolbami.

![Druhý graf by měl zobrazovat viditelné zvýšení předpovědi pořadí zarovnány s uživatelskými předvolbami.](./media/tutorial-azure-notebook/azure-notebook-chart-results-happy-graph.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nemáte v úmyslu pokračovat v sérii kurzů, vyčistěte následující zdroje:

* Odstraňte projekt poznámkového bloku Azure.
* Odstraňte prostředek personalizátoru.

## <a name="next-steps"></a>Další kroky

[Jupyter notebook a datové soubory](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/tree/master/samples/azurenotebook) použité v této ukázce jsou k dispozici na úložišti GitHub pro personalizaci.

