---
title: 'Kurz: program pro přizpůsobování notebooků Azure'
titleSuffix: Azure Cognitive Services
description: V tomto kurzu se simuluje smyčka přizpůsobeného _system v poznámkovém bloku Azure, který navrhuje typ kávy, který si zákazník může objednat. Uživatelé a jejich předvolby jsou uloženy v uživatelské datové sadě. Informace o kávě jsou také k dispozici a uloženy v datové sadě kávy.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: tutorial
ms.date: 04/27/2020
ms.custom: devx-track-python
ms.openlocfilehash: 0cf2aa504fd7a36a55740b3a59eeb3759e67f469
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88509905"
---
# <a name="tutorial-use-personalizer-in-azure-notebook"></a>Kurz: použití přizpůsobeného úložiště v Azure poznámkovém bloku

V tomto kurzu se spouští smyčka individuálního nastavení v poznámkovém bloku Azure, který demonstruje koncové životní cykly smyčky přizpůsobené v rámci personalizace.

Smyčka navrhuje, který typ kávy by zákazník měl objednat. Uživatelé a jejich předvolby jsou uloženy v uživatelské datové sadě. Informace o kávě jsou uloženy v datové sadě kávy.

## <a name="users-and-coffee"></a>Uživatelé a káva

Poznámkový blok simulující interakci uživatele s webem vybere náhodného uživatele, denní dobu a typ počasí z datové sady. Souhrn informací o uživateli:

|Zákazníci – kontextové funkce|Denní doba|Typy počasí|
|--|--|--|
|Náležící<br>Bob<br>Cathy<br>Dave|Označení<br>Celkem<br>Celkem|Slunečné<br>RAINY<br>Bílá|

Aby bylo možné lépe přizpůsobovat informace, _systém_ ví, že v průběhu času zná také informace o výběru kávy pro každou osobu.

|Funkce kávy – akce|Typy teploty|Místa původu|Typy Roast|Organic|
|--|--|--|--|--|
|Cappacino|Horká|Keňa|Tmavý|Organic|
|Studená Brew|Bez zájmu|Brazílie|Světlý|Organic|
|Iced Mocha|Bez zájmu|Etiopie|Světlý|Neorganické|
|Latte|Horká|Brazílie|Tmavý|Neorganické|

**Účelem** smyčky pro přizpůsobování je najít nejlepší shodu mezi uživateli a kávy co nejvíce času.

Kód pro tento kurz je k dispozici v [úložišti GitHub Samples Samples](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/tree/master/samples/azurenotebook).

## <a name="how-the-simulation-works"></a>Jak simulace funguje

Na začátku běžícího systému jsou návrhy z přizpůsobeného nástroje úspěšné jenom mezi 20% a 30%. Tato úspěšnost je popsána v případě, že se vám podařilo odeslat zpět do aplikačního rozhraní API pro přizpůsobení s skóre 1. Po určitém pořadí a bez jakýchkoli volání se systém zlepšuje.

Po počátečních požadavcích spusťte offline vyhodnocení. To umožňuje přizpůsobovat, aby si data zkontrolovali a navrhli lepší výukové zásady. Použijte nové zásady učení a znovu spusťte Poznámkový blok s 20% počtu předchozích požadavků. Tato smyčka bude lepší díky novým zásadám učení.

## <a name="rank-and-reward-calls"></a>Volání pořadí a odměňování

U každého z tisíc volání služby přizpůsobeného rozhraní odešle Poznámkový blok Azure požadavek na **řazení** do REST API:

* Jedinečné ID pro událost pořadí/žádosti
* Kontextové funkce – náhodným výběrem uživatele, počasí a času, kdy se uživateli na webu nebo na mobilním zařízení simuluje uživatel.
* Akce s funkcemi – _všechna_ data kávy – od kterých se přizpůsobuje návrh

Systém obdrží požadavek a pak porovná tuto předpověď se známou volbou uživatele pro stejnou denní dobu a počasí. Pokud je známá volba stejná jako předpokládaná volba, **odměna** 1 se pošle zpátky do přizpůsobeného. V opačném případě je zpětná síla odeslána 0.

> [!Note]
> Toto je simulace, takže algoritmus pro odměnu je jednoduchý. Ve scénáři reálného světa by měl algoritmus používat obchodní logiku, případně i váhy pro různé aspekty prostředí zákazníka, a určit tak skóre odměňování.


## <a name="prerequisites"></a>Požadavky

* Účet [poznámkového bloku Azure](https://notebooks.azure.com/) .
* [Prostředek pro přizpůsobování Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer).
    * Pokud jste už použili prostředek pro přizpůsobeného přizpůsobeného, ujistěte se, že se data v Azure Portal pro prostředek [vymazala](how-to-settings.md#clear-data-for-your-learning-loop) .
* Nahrajte všechny soubory pro [tuto ukázku](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/tree/master/samples/azurenotebook) do projektu poznámkového bloku Azure.

Popisy souborů:

* [Personalizovat. ipynb](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/samples/azurenotebook/Personalizer.ipynb) je notebook Jupyter pro tento kurz.
* [Uživatelská datová sada](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/samples/azurenotebook/users.json) je uložena v objektu JSON.
* [Datová sada kávy](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/samples/azurenotebook/coffee.json) je uložena v objektu JSON.
* [Příkladem JSON požadavku](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/samples/azurenotebook/example-rankrequest.json) je očekávaný formát pro požadavek post na rozhraní API řazení.

## <a name="configure-personalizer-resource"></a>Konfigurace prostředku přizpůsobeného pro přizpůsobování

V Azure Portal nakonfigurujte [prostředek](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer) přizpůsobeného přizpůsobeného pomocí **Frekvence aktualizace modelu** nastavenou na 15 sekund a **dobu čekání na odměnu** 15 sekund. Tyto hodnoty jsou k dispozici na stránce **[Konfigurace](how-to-settings.md#configure-service-settings-in-the-azure-portal)** .

|Nastavení|Hodnota|
|--|--|
|aktualizace frekvence modelu|15 sekund|
|Doba čekání na odměnu|15 sekund|

Tyto hodnoty mají velmi krátkou dobu trvání, aby bylo možné zobrazit změny v tomto kurzu. Tyto hodnoty by se neměly používat v produkčním scénáři bez ověřování, aby dosáhly svého cíle v rámci vaší smyčky pro přizpůsobování.

## <a name="set-up-the-azure-notebook"></a>Nastavení poznámkového bloku Azure

1. Změňte jádro na `Python 3.6` .
1. Otevřete soubor `Personalizer.ipynb`.

## <a name="run-notebook-cells"></a>Spustit buňky poznámkového bloku

Spusťte každou spustitelnou buňku a počkejte, než se vrátí. Víte, že je to hotové, když se závorky vedle buňky zobrazují jako číslo místo `*` . Následující části vysvětlují, co jednotlivé buňky programově a co mají očekávat pro výstup.

### <a name="include-the-python-modules"></a>Zahrnutí modulů Pythonu

Zahrňte požadované moduly Pythonu. Buňka nemá žádný výstup.

```python
import json
import matplotlib.pyplot as plt
import random
import requests
import time
import uuid
```

### <a name="set-personalizer-resource-key-and-name"></a>Nastavení klíče a názvu prostředku pro přizpůsobení

V Azure Portal Najděte svůj klíč a koncový bod na stránce **rychlý Start** vašeho prostředku pro přizpůsobení. Změňte hodnotu `<your-resource-name>` na název prostředku přizpůsobeného pro přizpůsobení. Změňte hodnotu `<your-resource-key>` na klíč pro přizpůsobení.

```python
# Replace 'personalization_base_url' and 'resource_key' with your valid endpoint values.
personalization_base_url = "https://<your-resource-name>.cognitiveservices.azure.com/"
resource_key = "<your-resource-key>"
```

### <a name="print-current-date-and-time"></a>Tisk aktuálního data a času
Pomocí této funkce si můžete všimnout počátečního a koncového času iterační funkce, iterací.

Tyto buňky nemají žádný výstup. Funkce provede výstup aktuálního data a času při volání.

```python
# Print out current datetime
def currentDateTime():
    currentDT = datetime.datetime.now()
    print (str(currentDT))
```

### <a name="get-the-last-model-update-time"></a>Získat čas poslední aktualizace modelu

Když `get_last_updated` je zavolána funkce, funkce vypíše datum poslední změny a čas, kdy byl model aktualizován.

Tyto buňky nemají žádný výstup. Funkce provede výstup posledního data školení modelu při volání.

Funkce pro [získání vlastností modelu](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/GetModelProperties)používá REST API Get.

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

### <a name="get-policy-and-service-configuration"></a>Získat konfiguraci zásad a služeb

Ověřte stav služby pomocí těchto dvou volání REST.

Tyto buňky nemají žádný výstup. Funkce provede výstup hodnot služeb při volání.

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

* vytvoří adresy URL používané v voláních REST.
* Nastaví hlavičku zabezpečení pomocí klíče prostředku pro přizpůsobování.
* nastaví náhodné osazení pro ID události klasifikace.
* čtení v datových souborech JSON
* volání `get_last_updated` metody – zásady učení se odebraly v příkladu výstupu.
* Call – `get_service_settings` Metoda

Buňka obsahuje výstup z volání `get_last_updated` `get_service_settings` funkce and.

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

Ověřte, že je výstup `rewardWaitTime` a `modelExportFrequency` nastavený na 15 sekund.

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

### <a name="troubleshooting-the-first-rest-call"></a>Řešení potíží s prvním voláním REST

Tato předchozí buňka je první buňkou, která volá do přizpůsobeného. Ujistěte se, že stavový kód REST ve výstupu je `<Response [200]>` . Pokud se zobrazí chyba, třeba 404, ale jste si jisti, že je klíč prostředku a název správný, znovu načtěte Poznámkový blok.

Ujistěte se, že počet kávy a uživatelé jsou obě 4. Pokud se zobrazí chyba, ověřte, že jste nahráli všechny 3 soubory JSON.

### <a name="set-up-metric-chart-in-azure-portal"></a>Nastavení grafu metriky v Azure Portal

V pozdější části tohoto kurzu se dlouho běžící proces 10 000 požadavků zobrazuje v prohlížeči s textovým polem aktualizace. Může být snazší zobrazit v grafu nebo jako celkový součet, až dlouho běžící proces skončí. Chcete-li zobrazit tyto informace, použijte metriky, které jsou k dispozici v prostředku. Graf teď můžete vytvořit, když jste dokončili žádost o službu, a pak graf pravidelně aktualizovat, dokud neběží dlouho probíhající proces.

1. V Azure Portal vyberte prostředek pro přizpůsobování.
1. V navigaci prostředků vyberte v části monitorování **metriky** .
1. V grafu vyberte **Přidat metriku**.
1. Obor názvů prostředku a metriky jsou už nastavené. Stačí jenom vybrat metriku **úspěšných volání** a agregaci **Sum**.
1. Změňte časový filtr na poslední 4 hodiny.

    ![Nastavením grafu metriky v Azure Portal přidáte metriku pro úspěšná volání za poslední 4 hodiny.](./media/tutorial-azure-notebook/metric-chart-setting.png)

    V grafu by se měla zobrazit tři úspěšná volání.

### <a name="generate-a-unique-event-id"></a>Generování jedinečného ID události

Tato funkce generuje jedinečné ID pro každé volání pořadí. IDENTIFIKÁTOR slouží k identifikaci informací o pořadí a odměnu. Tato hodnota může pocházet z obchodního procesu, jako je ID webového zobrazení nebo ID transakce.

Buňka nemá žádný výstup. Při volání funkce výstupuje jedinečné ID.

```python
def add_event_id(rankjsonobj):
    eventid = uuid.uuid4().hex
    rankjsonobj["eventId"] = eventid
    return eventid
```

### <a name="get-random-user-weather-and-time-of-day"></a>Získat náhodného uživatele, počasí a denní dobu

Tato funkce vybere jedinečného uživatele, počasí a denní dobu a potom přidá tyto položky do objektu JSON, který se odešle do žádosti o řazení.

Buňka nemá žádný výstup. Při volání funkce vrátí jméno náhodného uživatele, náhodné počasí a náhodný čas dne.

Seznam 4 uživatelů a jejich předvolby – pro zkrácení se zobrazí jenom některé předvolby:

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


### <a name="add-all-coffee-data"></a>Přidat všechna data v kávě

Tato funkce přidá celý seznam kávy do objektu JSON, který se odešle do požadavku na řazení.

Buňka nemá žádný výstup. Funkce `rankjsonobj` při volání mění.


Příkladem jedné kávové funkce je:

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

### <a name="compare-prediction-with-known-user-preference"></a>Porovnání předpovědi se známými preferencemi uživatele

Tato funkce se volá po volání rozhraní API řazení pro každou iteraci.

Tato funkce porovnává preference uživatele pro káva na základě počasí a denního času s návrhem přizpůsobeného pro uživatele pro tyto filtry. Pokud se návrh shoduje, je vráceno skóre 1, jinak je skóre 0. Buňka nemá žádný výstup. Funkce provede výstup skóre při volání.

```python
def get_reward_from_simulated_data(name, weather, timeofday, prediction):
    if(userpref[name][weather][timeofday] == str(prediction)):
        return 1
    return 0
```

### <a name="loop-through-calls-to-rank-and-reward"></a>Smyčka prostřednictvím volání do řazení a odměňování

Další buňkou je _Hlavní_ práce poznámkového bloku, která získá náhodného uživatele, získá seznam kávy a odešle je do rozhraní API pro řazení. Porovnání předpovědi se známými preferencemi uživatele a odeslání odměnu zpět do služby pro přizpůsobení.

Cyklus se spustí za `num_requests` krátkou dobu. Přizpůsobování vyžaduje několik tisíc volání, která umožňují seřadit a odměnu vytvořit model.

Následuje příklad formátu JSON odeslaného rozhraním API pro řazení. Seznam kávy není úplný pro zkrácení. Celý formát JSON pro káva můžete zobrazit v `coffee.json` .

Do rozhraní API řazení se poslal kód JSON:

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

Odpověď JSON z rozhraní API řazení:

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

Nakonec každá smyčka ukazuje náhodný výběr uživatele, počasí, denní dobu a zjištěnou odměnu. Odměna 1 označuje prostředek přizpůsobeného uživatelem, který vybral správný typ kávy pro daného uživatele, počasí a denní dobu.

```console
1 Alice Rainy Morning Latte 1
```

Funkce používá:

* Rank: REST API příspěvku, který [získá pořadí](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank).
* Odměňování: příspěvek REST API k [vykazování odměna](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward).

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

## <a name="run-for-10000-iterations"></a>Spustit pro iterace 10 000
Spusťte smyčku personalizace pro 10 000 iterace. Jedná se o dlouhou spuštěnou událost. Nezavírejte prohlížeč, který spouští Poznámkový blok. Aktualizujte graf metrik v Azure Portal pravidelně, aby se zobrazila celková volání služby. Pokud máte kolem volání 20 000, je pro každou iteraci smyčky volání pořadí a odměna dokončena.

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

Vytvořte graf z `count` a `rewards` .

```python
def createChart(x, y):
    plt.plot(x, y)
    plt.xlabel("Batch of rank events")
    plt.ylabel("Correct recommendations per batch")
    plt.show()
```

## <a name="run-chart-for-10000-rank-requests"></a>Spuštění grafu pro 10 000 požadavky na řazení

Spusťte `createChart` funkci.

```python
createChart(count,rewards)
```

## <a name="reading-the-chart"></a>Čtení grafu

Tento graf znázorňuje úspěšnost modelu pro aktuální výchozí zásady učení.

![Tento graf znázorňuje úspěšnost aktuálních zásad učení po dobu trvání testu.](./media/tutorial-azure-notebook/azure-notebook-chart-results.png)


V ideálním cíli, který je na konci testu, smyčka je průměrnou mírou úspěšnosti, která je blízko až 100% minus průzkumu. Výchozí hodnota průzkumu je 20%.

`100-20=80`

Tato hodnota průzkumu se nachází v Azure Portal pro prostředek přizpůsobeného nástroji na stránce **Konfigurace** .

Pokud chcete najít lepší zásady učení na základě vašich dat na rozhraní API pro řazení, spusťte na portálu pro vaši smyčku pro přizpůsobování [offline testování](how-to-offline-evaluation.md) .

## <a name="run-an-offline-evaluation"></a>Spustit zkušební verzi offline

1. V Azure Portal otevřete stránku **hodnocení** prostředku přizpůsobeného pro přizpůsobení.
1. Vyberte **vytvořit vyhodnocení**.
1. Zadejte požadovaná data zkušebního názvu a rozsah dat pro vyhodnocení smyčky. Rozsah kalendářních dat by měl obsahovat jenom dny, na které se zaměřujete pro vyhodnocení.
    ![V Azure Portal otevřete stránku hodnocení prostředku přizpůsobeného pro přizpůsobení. Vyberte vytvořit vyhodnocení. Zadejte název vyhodnocení a rozsah dat.](./media/tutorial-azure-notebook/create-offline-evaluation.png)

    Účelem spuštění tohoto testování v režimu offline je určit, jestli jsou k dispozici lepší zásady učení pro funkce a akce použité v této smyčce. Aby se zajistilo, že se mají lepší zásady učení, ujistěte se, že je zapnuté **zjišťování optimalizace** .

1. Kliknutím na **OK** zahajte vyhodnocení.
1. Tato stránka **hodnocení** obsahuje nové vyhodnocení a aktuální stav. V závislosti na tom, kolik dat máte, může toto vyhodnocení trvat delší dobu. Po několika minutách se můžete vrátit k této stránce a zobrazit výsledky.
1. Po dokončení vyhodnocení vyberte hodnocení a pak vyberte **porovnání různých zásad učení**. Zobrazí se dostupné zásady učení a jak se budou chovat s daty.
1. V tabulce vyberte zásadu učení nejvyšší úrovně a vyberte **použít**. To platí pro vaše modely a přeškolování zásad _nejlepšího_ učení.

## <a name="change-update-model-frequency-to-5-minutes"></a>Změna frekvence aktualizace modelu na 5 minut

1. V Azure Portal ještě na prostředku přizpůsobeného nástroji vyberte stránku **Konfigurace** .
1. Změňte **Četnost aktualizace modelu** a nastavte **dobu čekání** na 5 minut a vyberte **Uložit**.

Přečtěte si další informace o [době čekání na odměnu](concept-rewards.md#reward-wait-time) a [četnosti aktualizací modelu](how-to-settings.md#model-update-frequency).

```python
#Verify new learning policy and times
get_service_settings()
```

Ověřte, že je výstup `rewardWaitTime` a `modelExportFrequency` nastavený na 5 minut.
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

Vraťte se do souboru Azure Notebooks a pokračujte spuštěním stejné smyčky, ale pouze pro 2 000 iterace. Aktualizujte graf metrik v Azure Portal pravidelně, aby se zobrazila celková volání služby. Pokud máte kolem volání 4 000, je pro každou iteraci smyčky volání pořadí a odměna dokončena.

```python
# max iterations
num_requests = 2000

# check last mod date N% of time - currently 10%
lastModCheck2 = int(num_requests * .10)

jsonTemplate2 = rankactionsjsonobj

# main iterations
[count2, rewards2] = iterations(num_requests, lastModCheck2, jsonTemplate)
```

## <a name="run-chart-for-2000-rank-requests"></a>Spuštění grafu pro 2 000 požadavky na řazení

Spusťte `createChart` funkci.

```python
createChart(count2,rewards2)
```

## <a name="review-the-second-chart"></a>Kontrola druhého grafu

Druhý graf by měl zobrazit viditelné zvýšení pořadí řazení předpovědi s uživatelskými preferencemi.

![Druhý graf by měl zobrazit viditelné zvýšení pořadí řazení předpovědi s uživatelskými preferencemi.](./media/tutorial-azure-notebook/azure-notebook-chart-results-happy-graph.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nechcete pokračovat v sérii kurzů, vyčistěte následující prostředky:

* Odstraňte svůj projekt Azure notebook.
* Odstraňte prostředek pro přizpůsobení.

## <a name="next-steps"></a>Další kroky

[Poznámkový blok Jupyter a datové soubory](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/tree/master/samples/azurenotebook) použité v této ukázce jsou k dispozici v úložišti GitHub pro přizpůsobení.

