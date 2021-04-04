---
title: Konfigurace ověřování pro modely nasazené jako webové služby
titleSuffix: Azure Machine Learning
description: Naučte se konfigurovat ověřování modelů strojového učení nasazených do webových služeb v Azure Machine Learning.
services: machine-learning
author: cjgronlund
ms.author: cgronlun
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.date: 11/06/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 5bd938fce347d439c2acb4e3fcace04d5b27d770
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "94447586"
---
# <a name="configure-authentication-for-models-deployed-as-web-services"></a>Konfigurace ověřování pro modely nasazené jako webové služby

Azure Machine Learning umožňuje nasazení vašich školicích modelů strojového učení jako webových služeb. V tomto článku se dozvíte, jak nakonfigurovat ověřování pro tato nasazení.

Nasazení modelu vytvořená Azure Machine Learning lze nakonfigurovat tak, aby používalo jednu ze dvou metod ověřování:

* **založené na klíčích**: ke ověřování webové služby se používá statický klíč.
* **založené na tokenech**: dočasný token se musí získat z Azure Machine Learningho pracovního prostoru (pomocí Azure Active Directory) a použít ho k ověření webové služby. Platnost tohoto tokenu vyprší po uplynutí určité doby a je nutné ji aktualizovat, aby bylo možné pokračovat v práci s webovou službou.

    > [!NOTE]
    > Ověřování založené na tokenech je dostupné jenom při nasazení do služby Azure Kubernetes.

## <a name="key-based-authentication"></a>Ověřování na základě klíčů

Webové služby nasazené ve službě Azure Kubernetes Service (AKS) mají ve výchozím nastavení *povolené* ověřování na základě klíčů.

Ve výchozím nastavení mají nasazené služby Azure Container Instances (ACI) ověřování na základě klíčů *zakázané* , ale můžete je povolit nastavením `auth_enabled=True` při vytváření webové služby ACI. Následující kód je příkladem vytvoření konfigurace nasazení ACI s povoleným ověřováním pomocí klíče.

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(cpu_cores = 1,
                                                memory_gb = 1,
                                                auth_enabled=True)
```

Pak můžete použít vlastní konfiguraci ACI v nasazení pomocí `Model` třídy.

```python
from azureml.core.model import Model, InferenceConfig


inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
aci_service = Model.deploy(workspace=ws,
                       name="aci_service_sample",
                       models=[model],
                       inference_config=inference_config,
                       deployment_config=aci_config)
aci_service.wait_for_deployment(True)
```

Chcete-li načíst klíče ověřování, použijte `aci_service.get_keys()` . Chcete-li znovu vygenerovat klíč, použijte `regen_key()` funkci a předejte ji buď **primární** , nebo **sekundární**.

```python
aci_service.regen_key("Primary")
# or
aci_service.regen_key("Secondary")
```

## <a name="token-based-authentication"></a>Ověřování na základě tokenů

Pokud povolíte ověřování pomocí tokenu pro webovou službu, uživatelé musí pro přístup k této webové službě předložit Azure Machine Learning JSON Web Token. Platnost tokenu po zadané době vyprší a je potřeba ji obnovit, pokud chcete provádět další volání.

* Ověřování tokenu je **ve výchozím nastavení** při nasazení do služby Azure Kubernetes zakázané.
* Ověřování tokenu se při nasazení do Azure Container Instances **nepodporuje** .
* Ověřování tokenu **nelze použít současně s ověřováním na základě klíčů**.

K řízení ověřování tokenu použijte `token_auth_enabled` parametr při vytváření nebo aktualizaci nasazení:

```python
from azureml.core.webservice import AksWebservice
from azureml.core.model import Model, InferenceConfig

# Create the config
aks_config = AksWebservice.deploy_configuration()

#  Enable token auth and disable (key) auth on the webservice
aks_config = AksWebservice.deploy_configuration(token_auth_enabled=True, auth_enabled=False)

aks_service_name ='aks-service-1'

# deploy the model
aks_service = Model.deploy(workspace=ws,
                           name=aks_service_name,
                           models=[model],
                           inference_config=inference_config,
                           deployment_config=aks_config,
                           deployment_target=aks_target)

aks_service.wait_for_deployment(show_output = True)
```

Pokud je povoleno ověřování tokenu, můžete použít `get_token` metodu k načtení JSON web token (Jwt) a času vypršení platnosti tokenu:

> [!TIP]
> Pokud k získání tokenu použijete instanční objekt a chcete, aby měl minimální požadovaný přístup k načtení tokenu, přiřaďte ho k roli **čtenáře** pro daný pracovní prostor.

```python
token, refresh_by = aks_service.get_token()
print(token)
```

> [!IMPORTANT]
> Po uplynutí doby `refresh_by` tokenu je potřeba vyžádat si nový token. Pokud potřebujete aktualizovat tokeny mimo sadu Python SDK, jednu z možností je použít REST API s ověřováním instančního objektu pro pravidelné `service.get_token()` volání, jak je popsáno výše.
>
> Důrazně doporučujeme vytvořit pracovní prostor Azure Machine Learning ve stejné oblasti jako cluster služby Azure Kubernetes.
>
> K ověřování pomocí tokenu webová služba provede volání do oblasti, ve které je vytvořen Azure Machine Learning pracovní prostor. Pokud vaše oblast pracovního prostoru není k dispozici, nebudete moci načíst token pro webovou službu, i když je váš cluster v jiné oblasti než pracovní prostor. Výsledkem je, že ověřování Azure AD není k dispozici, dokud nebude vaše oblast pracovního prostoru znovu dostupná.
>
> Čím větší je vzdálenost mezi oblastí vašeho clusteru a oblastí pracovního prostoru, tím déle bude trvat Načtení tokenu.

## <a name="next-steps"></a>Další kroky

Další informace o ověřování pro nasazený model najdete v tématu [Vytvoření klienta pro model nasazený jako webovou službu](how-to-consume-web-service.md).