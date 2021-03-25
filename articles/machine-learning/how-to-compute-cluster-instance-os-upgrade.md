---
title: Upgrade hostitelského operačního systému pro výpočetní cluster a instanci
titleSuffix: Azure Machine Learning
description: Upgradujte hostitelský operační systém pro výpočetní cluster a instanci COMPUTE z Ubuntu 16,04 LTS na 18,04 LTS.
services: machine-learning
author: nishankgu
ms.author: nigup
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.date: 03/03/2021
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 710a860b1ed87f176b6f42b4963dad17acb323b1
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2021
ms.locfileid: "104954050"
---
# <a name="upgrade-compute-instance-and-compute-cluster-host-os"></a>Upgradovat výpočetní instanci a operační systém hostitele výpočetního clusteru

Výpočetní __cluster__ a __instance COMPUTE__ jsou spravované výpočetní infrastrukturou. Azure Machine Learning Jako spravovaná služba společnost Microsoft spravuje hostitelský operační systém a nainstalované balíčky a verze softwaru.

Hostitelský operační systém pro výpočetní cluster a instanci služby COMPUTE byl Ubuntu 16,04 LTS. **30. dubna 2021** Ubuntu končí podpora pro 16,04. Od __15. března 2021__ bude Microsoft hostitelský operační systém automaticky aktualizovat na Ubuntu 18,04 LTS. Aktualizace na 18,04 zajistí průběžné aktualizace zabezpečení a podporu od komunity Ubuntu. Tato aktualizace bude zahrnuta v rámci oblastí Azure a bude dostupná ve všech oblastech do __9. dubna 2021__. Další informace o Ubuntu ukončení podpory pro 16,04 najdete na blogu věnovaném [vydání Ubuntu](https://wiki.ubuntu.com/Releases).

> [!TIP]
> * Hostitelský operační systém není verze operačního systému, kterou můžete zadat pro [prostředí](how-to-use-environments.md) při výuce nebo nasazování modelu. Prostředí se spouštějí v Docker. Docker běží na hostitelském operačním systému.
> * Pokud aktuálně používáte prostředí založená na Ubuntu 16,04 pro školení nebo nasazení, Microsoft doporučuje přepnout na používání imagí založených na Ubuntu 18,04. Další informace najdete v tématu [použití prostředí](how-to-use-environments.md) a [úložiště Azure Machine Learning kontejnerů](https://github.com/Azure/AzureML-Containers/tree/master/base).
> * Při použití výpočetní instance Azure Machine Learning založené na Ubuntu 18,04 je výchozí verze Pythonu _python 3,8_.
## <a name="creating-new-resources"></a>Vytváření nových prostředků

Výpočetní cluster nebo výpočetní instance vytvořené po __09 dubna 2021__ ve výchozím nastavení používají jako hostitelský operační systém Ubuntu 18,04 LTS. Nemůžete vybrat jiný hostitelský operační systém.

## <a name="upgrade-existing-resources"></a>Upgradovat existující prostředky

Pokud jste již vytvořili výpočetní clustery nebo výpočetní instance vytvořené před __15. března 2021__, je nutné provést akci pro upgrade operačního systému hostitele na Ubuntu 18,04. V závislosti na oblasti, ke které přistupujete Azure Machine Learning, doporučujeme, abyste provedli tyto akce po __09 2021. dubna,__ abyste zajistili, že se změny provedou ve všech oblastech:

* __Azure Machine Learning výpočetní cluster__:

    * Pokud je cluster nakonfigurovaný s __minimálními uzly = 0__, automaticky se upgraduje po dokončení všech úloh a zkracuje se na nula uzlů.
    * Pokud je minimální počet __uzlů > 0__, dočasně změňte minimální uzly na hodnotu nula a umožněte clusteru zmenšení na nulové uzly.

    Další informace o tom, jak změnit minimální počet uzlů, najdete v tématu [AZ ml computetarget Update amlcompute](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/update#ext_azure_cli_ml_az_ml_computetarget_update_amlcompute) příkaz Azure CLI nebo referenční informace k sadě SDK [amlcompute. Update ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#update-min-nodes-none--max-nodes-none--idle-seconds-before-scaledown-none-) .

* __Azure Machine Learning výpočetní instance__: Vytvořte novou výpočetní instanci (která bude používat Ubuntu 18,04) a odstraňte starou instanci.

    * Libovolný Poznámkový blok uložený ve sdílené složce pracovního prostoru, úložiště dat datových sad bude přístupný z nové instance Compute.
    * Pokud jste vytvořili vlastní prostředí Conda, můžete tato prostředí exportovat z existující instance a importovat na novou instanci. Informace o exportu a importu conda najdete v [dokumentaci k conda](https://docs.conda.io/) na adrese docs.conda.IO.

    Další informace najdete v článku [co je výpočetní instance](concept-compute-instance.md) a [vytváření a Správa Azure Machine Learningch výpočetních instancí](how-to-create-manage-compute-instance.md) .

## <a name="check-host-os-version"></a>Ověřit verzi operačního systému hostitele

Informace o kontrole verze operačního systému hostitele najdete na stránce wiki komunity Ubuntu při [kontrole verze Ubuntu](https://help.ubuntu.com/community/CheckingYourUbuntuVersion).

> [!TIP]
> Pokud chcete použít `lsb_release -a` příkaz ze wikiwebu, můžete [použít relaci Terminálové služby na výpočetní instanci](how-to-access-terminal.md).
## <a name="next-steps"></a>Další kroky

Pokud máte nějaké další dotazy nebo obavy, kontaktujte nás na adrese [ubuntu18azureml@service.microsoft.com](mailto:ubuntu18azureml@service.microsoft.com) .
