---
title: Nasazení Průvodce odstraňováním potíží pro službu Azure Machine Learning
description: Další problém obejdete tak, jak řešení a řešení potíží s běžnými chybami nasazení Dockeru se službou Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: haining
author: hning86
ms.reviewer: jmartens
ms.date: 10/01/2018
ms.openlocfilehash: 5c5468619533e66ddaac352dea8bdcbc9616b10d
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/03/2018
ms.locfileid: "48242287"
---
# <a name="troubleshoot-your-azure-machine-learning-service-deployments"></a>Řešení potíží s nasazeními služby Azure Machine Learning

V tomto článku se dozvíte, jak obejít nebo řešit běžné chyby nasazení Dockeru se službou Azure Machine Learning.

Při nasazení modelu ve službě Azure Machine Learning, systém provádí řadu úloh. Toto je komplexní posloupnost událostí a někdy vzniku. Úkoly nasazení jsou:

1. Zaregistrujte model v registru pracovního prostoru modelu.

2. Sestavíte image Dockeru, včetně:
    1. Stáhněte si registrovanému modelu z registru. 
    2. Vytvoření souboru dockerfile, pomocí prostředí Pythonu na základě závislostí, které zadáte v souboru yaml prostředí.
    3. Přidáte soubory modelu a hodnoticí skript, který zadáte v souboru dockerfile.
    4. Vytvářejte nová image Dockeru pomocí souboru dockerfile.
    5. Zaregistrujte image Dockeru s Azure Container Registry přidružený k pracovnímu prostoru.

3. Nasaďte image Dockeru do služby Azure Container Instance (ACI) nebo do Azure Kubernetes Service (AKS).

4. Spuštění nového kontejneru (nebo kontejnery) v ACI a AKS. 

Další informace o tomto procesu v [Správa modelů ve službě](concept-model-management-and-deployment.md) úvod.

## <a name="before-you-begin"></a>Než začnete

Pokud narazíte na jakékoli potíže, je prvním krokem je rozdělit úlohu nasazení (viz předchozí) do jednotlivých kroků a izolovat daný problém. 

To je zvláště užitečné, pokud používáte `Webservice.deploy` rozhraní API, nebo `Webservice.deploy_from_model` rozhraní API, protože tyto funkce seskupit dohromady výše uvedené kroky v rámci jedné akce. Obvykle jsou velmi vhodné těchto rozhraních API, ale pomáhá rozdělit kroky při řešení potíží. 

Zde jsou funkce pro použití při přerušení nasazení kroky:

1. Zaregistrujte model. Tady je ukázkový kód:

    ```python
    # register a model out of a run record
    model = best_run.register_model(model_name='my_best_model', model_path='outputs/my_model.pkl')

    # or, you can register a file or a folder of files as a model
    model = Model.register(model_path='my_model.pkl', model_name='my_best_model', workspace=ws)
    ```

2. Sestavení image. Tady je ukázkový kód:

    ```python
    # configure the image
    image_config = ContainerImage.image_configuration(runtime="python",
                                                      execution_script="score.py",
                                                      conda_file="myenv.yml")

    # create the image
    image = Image.create(name='myimg', models=[model], image_config=img_cfg, workspace=ws)

    # wait for image creation to finish
    image.wait_for_creation(show_output=True)
    ```

3. Nasazení bitové kopie jako služba. Tady je ukázkový kód:

    ```python
    # configure an ACI-based deployment
    aciconfig = AciWebservice.deploy_configuration(cpu_cores=1, memory_gb=1)

    aci_service = Webservice.deploy_from_image(deployment_config=aciconfig, 
                                               image=image, 
                                               name='mysvc', 
                                               workspace=ws)
    aci_service.wait_for_deployment(show_output=True)    
    ```

Jakmile máte rozdělené procesu nasazení do jednotlivých kroků, abychom se mohli podívat na některé z nejběžnějších chyb.

## <a name="image-building-fails"></a>Bitové kopie sestavení se nezdaří
Pokud je systém nelze sestavit image Dockeru `image.wait_for_creation` volání selže s některé chybové zprávy. Můžete také zjistit další podrobnosti o chybě v protokolu sestavení image. Identifikátor uri protokolu je adresa URL SAS odkazující na soubor protokolu se ukládají ve službě Azure blob storage. Jednoduše zkopírujte a vložte identifikátor uri do okna prohlížeče a můžete stáhnout a zobrazit protokol.

```python
# if you already have the image object handy
print(image.image_build_log_uri)

# if you only know the name of the image (note there might be multiple images with the same name but different version number)
print(ws.images()['myimg'].image_build_log_uri)

# list logs for all images in the workspace
for name, img in ws.images().items()
    print (img.name, img.version, img.image_build_log_uri)
```

## <a name="service-launch-fails"></a>Selhání spuštění služby
Jakmile na obrázku je úspěšně vytvořen, se systém pokusí o spuštění kontejneru v ACI a AKS v závislosti na konfiguraci vašeho nasazení. Vždy doporučujeme nejprve vyzkoušet nasazení služby ACI, protože jde o jednoduchý jeden kontejner nasazení. Tímto způsobem lze potom vyloučit jakýkoli problém s konkrétní AKS.

Jako součást procesu spouštění kontejneru `init()` vyvolání funkce v hodnoticí skript v systému. Pokud existují nezachycených výjimek `init()` fungovat, může se zobrazit **CrashLoopBackOff** chyby v chybové zprávě. Níže uvádíme tipy, které vám pomohou vyřešit problém.

### <a name="inspect-the-docker-log"></a>Zkontrolujte protokol Dockeru.
Pokud image se úspěšně vytvořil, ale dojde k chybám při nasazování bitové kopie jako kontejner, můžete zjistit chybovou zprávu do protokolu Dockeru.

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices()['mysvc'].get_logs())
```

### <a name="debug-the-docker-image-locally"></a>Ladit místně image Dockeru
Některé časy protokol Dockeru negeneruje dostatek informací o tom, co je špatně. Můžete přejděte o krok dál a stáhněte sestavenou image Dockeru, spusťte místní kontejner a ladit přímo v kontejneru živé interaktivní. Spuštění místního kontejneru, musíte mít místně spuštěný modul Docker, a je to mnohem jednodušší, pokud máte také [rozhraní příkazového řádku azure](/cli/azure/install-azure-cli?view=azure-cli-latest) nainstalované.

Nejprve je třeba zjistit umístění obrázku:

```python
# print image location
print(image.image_location)
```

Umístění obrázku má tento formát: `<acr-name>.azurecr.io/<image-name>:<version-number>`, jako například `myworkpaceacr.azurecr.io/myimage:3`. 

Teď přejděte na okno příkazového řádku a zadejte následující příkazy pro přihlášení do služby ACR (Azure Container Registry) přidružený k pracovnímu prostoru, kde je image uložená.

```sh
# note the acr_name is just the domain name WITHOUT the ".azurecr.io" postfix
# e.g.: az acr login -n myworkpaceacr
$ az acr login -n <acr-name>
```
Pokud nemáte nainstalované azure cli, můžete také použít `docker login` příkaz pro přihlášení služby ACR. Stačí načíst uživatelské jméno a heslo ACR z webu Azure portal.

Nyní můžete stáhnout image Dockeru a spuštění kontejneru místně a pak spusťte relaci prostředí bash pro ladění.

```sh
# note the image_id is <acr-name>.azurecr.io/<image-name>:<version-number>
# for example: myworkpaceacr.azurecr.io/myimage:3
$ docker run -it <image_id> /bin/bash
```

Po spuštění relace bashe spuštěný kontejner můžete najít v hodnoticí skripty `/var/azureml-app` složky. Potom můžete spustit relaci Pythonu se ladit hodnoticí skripty. 

```sh
# enter the directory where scoring scripts live
cd /var/azureml-app

# find what Python packages are installed in the python environment
pip freeze

# sanity-check on score.py
# you might want to edit the score.py to trigger init().
# as most of the errors happen in init() when you are trying to load the model.
python score.py
```
V případě, že budete potřebovat textový editor k úpravě skripty, můžete nainstalovat vim, nano, (emacs) nebo svém oblíbeném editoru.

```sh
# update package index
apt-get update

# install text editor of your choice
apt-get install vim
apt-get install nano
apt-get install emacs

# launch emacs (for example) to edit score.py
emacs score.py

# exit the container bash shell
exit
```

Můžete také spustit místně webové služby a odesílat provoz protokolu HTTP. Server Flask v kontejneru Dockeru běží na portu 5001. Můžete namapovat na žádné jiné porty, které jsou k dispozici na hostitelském počítači.
```sh
# you can find the scoring API at: http://localhost:8000/score
$ docker run -p 8000:5001 <image_id>
```

## <a name="function-fails-getmodelpath"></a>Selže funkce: get_model_path()
Často v `init()` funkce v hodnoticí skript `Model.get_model_path()` funkce je volána k vyhledání souboru modelu nebo složku souborů v kontejneru. To je často příčiny selhání, pokud model soubor nebo složku nelze nalézt. Nejjednodušší způsob, jak ladění této chyby je spustit níže uvedeného kódu Pythonu v prostředí kontejneru:

```python
from azureml.core.model import Model
print(Model.get_model_path(model_name='my-best-model'))
```

To by vytisknout místní cestu, hodnoticí skript očekává k vyhledání souboru modelu nebo složky. Potom můžete ověřit, pokud soubor nebo složka jsou skutečně kde se očekává se.


## <a name="function-fails-runinputdata"></a>Selže funkce: run(input_data)
Pokud úspěšně nasazení služby, a to dojde k chybě při odesílání dat na bodovací koncový bod, můžete přidat chyby zachytávání příkaz v vaše `run(input_data)` funkce rozdělit si chybovou zprávu. Příklad:

```python
def run(input_data):
    try:
        data = json.loads(input_data)['data']
        data = np.array(data)
        result = model.predict(data)
        return json.dumps({"result": result.tolist()})
    except Exception as e:
        result = str(e)
        # return error message back to the client
        return json.dumps({"error": result})
```

## <a name="next-steps"></a>Další postup

Další informace o nasazení: 
* [Postup nasazení do služby ACI](how-to-deploy-to-aci.md)

* [Postup nasazení do AKS](how-to-deploy-to-aks.md)

* [Kurz, část 1: trénování modelu](tutorial-train-models-with-aml.md)

* [Kurz, část 2: nasazení modelu](tutorial-deploy-models-with-aml.md)
