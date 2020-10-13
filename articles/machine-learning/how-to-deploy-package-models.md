---
title: Modely balíčků
titleSuffix: Azure Machine Learning
description: Zabalení modelu jako souboru Dockerfile
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 07/31/2020
ms.topic: conceptual
ms.reviewer: larryfr
ms.custom: deploy
ms.openlocfilehash: edaae4f4c06f038d12267e35a2c356af430e1555
ms.sourcegitcommit: 090ea6e8811663941827d1104b4593e29774fa19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91998816"
---
# <a name="how-to-package-a-registered-model-with-docker"></a>Jak zabalit registrovaný model pomocí Docker

Tento článek ukazuje, jak zabalit registrovaný Azure Machine Learning model pomocí Docker.

## <a name="prerequisites"></a>Požadavky

Tento článek předpokládá, že jste už v pracovním prostoru machine learningu provedli školení a zaregistrovali model. Pokud se chcete dozvědět, jak naučit a registrovat model scikit-učení, [postupujte podle tohoto kurzu](how-to-train-scikit-learn.md).


## <a name="package-models"></a>Modely balíčků

V některých případech můžete chtít vytvořit bitovou kopii Docker bez nasazení modelu (Pokud například plánujete [nasazení nasadit do Azure App Service](how-to-deploy-app-service.md)). Nebo můžete chtít stáhnout image a spustit ji v místní instalaci Docker. Můžete dokonce chtít stáhnout soubory používané k sestavení obrázku, zkontrolovat je, upravit je a sestavit bitovou kopii ručně.

Balení modelu vám umožní provádět tyto akce. Zabalí všechny prostředky potřebné k hostování modelu jako webové služby a umožňuje stáhnout zcela sestavenou image Docker nebo soubory potřebné k jeho sestavení. Existují dva způsoby použití balení modelu:

**Stáhnout zabalený model:** Stáhněte si image Docker, která obsahuje model a další soubory potřebné k jeho hostování jako webové služby.

**Vygenerujte souboru Dockerfile:** Stáhněte si souboru Dockerfile, model, vstupní skript a další materiály potřebné k vytvoření image Docker. Pak můžete soubory zkontrolovat nebo provést změny před tím, než Image sestavíte místně.

Oba balíčky lze použít k získání místní image Docker.

> [!TIP]
> Vytvoření balíčku se podobá nasazení modelu. Použijete registrovaný model a odvozenou konfiguraci.

> [!IMPORTANT]
> Pokud chcete stáhnout plně sestavenou Image nebo sestavit image místně, musíte mít ve svém vývojovém prostředí nainstalovaný [Docker](https://www.docker.com) .

### <a name="download-a-packaged-model"></a>Stažení zabaleného modelu

Následující příklad vytvoří image, která je zaregistrovaná ve službě Azure Container registry pro váš pracovní prostor:

```python
package = Model.package(ws, [model], inference_config)
package.wait_for_creation(show_output=True)
```

Po vytvoření balíčku můžete použít `package.pull()` k načtení image do místního prostředí Docker. Výstup tohoto příkazu zobrazí název obrázku. Příklad: 

`Status: Downloaded newer image for myworkspacef78fd10.azurecr.io/package:20190822181338`. 

Po stažení modelu použijte `docker images` příkaz k vypsání místních imagí:

```text
REPOSITORY                               TAG                 IMAGE ID            CREATED             SIZE
myworkspacef78fd10.azurecr.io/package    20190822181338      7ff48015d5bd        4 minutes ago       1.43 GB
```

Chcete-li spustit místní kontejner na základě tohoto obrázku, použijte následující příkaz pro spuštění pojmenovaného kontejneru z prostředí nebo příkazového řádku. Nahraďte `<imageid>` hodnotu identifikátorem image vráceným `docker images` příkazem.

```bash
docker run -p 6789:5001 --name mycontainer <imageid>
```

Tento příkaz spustí nejnovější verzi image s názvem `myimage` . Mapuje místní port 6789 na port v kontejneru, ve kterém webová služba naslouchá (5001). Také přiřadí název `mycontainer` kontejneru, což usnadňuje zastavení kontejneru. Po spuštění kontejneru můžete odeslat požadavky na `http://localhost:6789/score` .

### <a name="generate-a-dockerfile-and-dependencies"></a>Vygenerovat souboru Dockerfile a závislosti

Následující příklad ukazuje, jak stáhnout souboru Dockerfile, model a další prostředky potřebné k vytvoření image místně. `generate_dockerfile=True`Parametr označuje, že chcete soubory, nikoli plně sestavenou bitovou kopii.

```python
package = Model.package(ws, [model], inference_config, generate_dockerfile=True)
package.wait_for_creation(show_output=True)
# Download the package.
package.save("./imagefiles")
# Get the Azure container registry that the model/Dockerfile uses.
acr=package.get_container_registry()
print("Address:", acr.address)
print("Username:", acr.username)
print("Password:", acr.password)
```

Tento kód stáhne soubory potřebné k sestavení image do `imagefiles` adresáře. Souboru Dockerfile zahrnuté do uložených souborů odkazuje na základní image uloženou v registru služby Azure Container Registry. Při sestavování image v místní instalaci Docker musíte pro ověření v registru použít adresu, uživatelské jméno a heslo. Pomocí následujících kroků sestavíte Image pomocí místní instalace Docker:

1. Z prostředí nebo relace příkazového řádku použijte následující příkaz k ověření Docker pomocí služby Azure Container Registry. Nahradí `<address>` `<username>` hodnoty, a `<password>` s hodnotami načtenými `package.get_container_registry()` .

    ```bash
    docker login <address> -u <username> -p <password>
    ```

2. K sestavení image použijte následující příkaz. Nahraďte `<imagefiles>` cestou k adresáři, kam se `package.save()` soubory uložily.

    ```bash
    docker build --tag myimage <imagefiles>
    ```

    Tento příkaz nastaví název bitové kopie na `myimage` .

Chcete-li ověřit, zda je obrázek sestaven, použijte `docker images` příkaz. V seznamu by se měla zobrazit `myimage` obrázek:

```text
REPOSITORY      TAG                 IMAGE ID            CREATED             SIZE
<none>          <none>              2d5ee0bf3b3b        49 seconds ago      1.43 GB
myimage         latest              739f22498d64        3 minutes ago       1.43 GB
```

Chcete-li spustit nový kontejner na základě tohoto obrázku, použijte následující příkaz:

```bash
docker run -p 6789:5001 --name mycontainer myimage:latest
```

Tento příkaz spustí nejnovější verzi image s názvem `myimage` . Mapuje místní port 6789 na port v kontejneru, ve kterém webová služba naslouchá (5001). Také přiřadí název `mycontainer` kontejneru, což usnadňuje zastavení kontejneru. Po spuštění kontejneru můžete odeslat požadavky na `http://localhost:6789/score` .

### <a name="example-client-to-test-the-local-container"></a>Příklad klienta pro otestování místního kontejneru

Následující kód je příkladem klienta Pythonu, který lze použít s kontejnerem:

```python
import requests
import json

# URL for the web service.
scoring_uri = 'http://localhost:6789/score'

# Two sets of data to score, so we get two results back.
data = {"data":
        [
            [ 1,2,3,4,5,6,7,8,9,10 ],
            [ 10,9,8,7,6,5,4,3,2,1 ]
        ]
        }
# Convert to JSON string.
input_data = json.dumps(data)

# Set the content type.
headers = {'Content-Type': 'application/json'}

# Make the request and display the response.
resp = requests.post(scoring_uri, input_data, headers=headers)
print(resp.text)
```

Například klienty v jiných programovacích jazycích najdete v tématu [využívání modelů nasazených jako webové služby](how-to-consume-web-service.md).

### <a name="stop-the-docker-container"></a>Zastavení kontejneru Docker

Pokud chcete kontejner zastavit, použijte následující příkaz z jiného prostředí nebo příkazového řádku:

```bash
docker kill mycontainer
```

## <a name="next-steps"></a>Další kroky

* [Řešení potíží s neúspěšným nasazením](how-to-troubleshoot-deployment.md)
* [Nasazení do Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)
* [Vytváření klientských aplikací pro využívání webových služeb](how-to-consume-web-service.md)
* [Aktualizace webové služby](how-to-deploy-update-web-service.md)
* [Postup nasazení modelu pomocí vlastní image Docker](how-to-deploy-custom-docker-image.md)
* [Zabezpečení webové služby prostřednictvím služby Azure Machine Learning s využitím protokolu TLS](how-to-secure-web-service.md)
* [Monitorování modelů Azure Machine Learning s využitím Application Insights](how-to-enable-app-insights.md)
* [Shromažďování dat pro modely v produkčním prostředí](how-to-enable-data-collection.md)
* [Vytváření výstrah a triggerů událostí pro nasazení modelů](how-to-use-event-grid.md)
