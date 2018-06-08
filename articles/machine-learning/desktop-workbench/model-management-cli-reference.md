---
title: Azure Machine Learning modelu rozhraní příkazového řádku managementu | Microsoft Docs
description: Referenční informace o rozhraní příkazového řádku Azure Machine Learning modelu Management.
services: machine-learning
author: aashishb
ms.author: aashishb
manager: hjerez
ms.reviewer: jasonwhowell, mldocs
ms.service: machine-learning
ms.component: desktop-workbench
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 11/08/2017
ms.openlocfilehash: 540f22e38201ec488d8e2c1d7494bc83d7b83a7e
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34831917"
---
# <a name="model-management-command-line-interface-reference"></a>Referenční informace o modelu správy rozhraní příkazového řádku

## <a name="base-cli-concepts"></a>Základní koncepty rozhraní příkazového řádku:

    account : Manage model management accounts. 
    env     : Manage compute environments.
    image   : Manage operationalization images.
    manifest: Manage operationalization manifests.
    model   : Manage operationalization models.
    service : Manage operationalized services.

## <a name="account-commands"></a>Účet příkazy
Účet pro správu modelu je potřeba použít služby, které umožňují nasadit a spravovat modely. Použití `az ml account modelmanagement -h` zobrazíte v následujícím seznamu:

    create: Create a Model Management Account.
    delete: Delete a specified Model Management Account.
    list  : Gets the Model Management Accounts in the current subscriptiong.
    set   : Set the active Model Management Account.
    show  : Show a Model Management Account.
    update: Update an existing Model Management Account.

**Vytvoření modelu správy účtu**

Vytvořte účet model správy pro fakturaci pomocí následujícího příkazu:

`az ml account modelmanagement create --location [Azure region e.g. eastus2] --name [new account name] --resource-group [resource group name to store the account in]`

Místní argumenty:

    --location -l       [Required]: Resource location.
    --name -n           [Required]: Name of the model management account.
    --resource-group -g [Required]: Resource group to create the model management account in.
    --description -d              : Description of the model management account.
    --sku-instances               : Number of instances of the selected SKU. Must be between 1 and
                                    16 inclusive.  Default: 1.
    --sku-name                    : SKU name. Valid names are S1|S2|S3|DevTest.  Default: S1.
    --tags -t                     : Tags for the model management account.  Default: {}.
    -v                            : Verbosity flag.

## <a name="environment-commands"></a>Příkazy prostředí

    cluster        : Switch the current execution context to 'cluster'.
    delete         : Delete an MLCRP-provisioned resource.
    get-credentials: List the keys for an environment.
    list           : List all environments in the current subscription.
    local          : Switch the current execution context to 'local'.
    set            : Set the active MLC environment.
    setup          : Sets up an MLC environment.
    show           : Show an MLC resource; if resource_group or cluster_name are not provided, shows
                     the active MLC env.

**Nastavení prostředí pro nasazení**

Příkaz instalační program vyžaduje, abyste Přispěvatel přístup k předplatnému. Pokud ho nemáte, potřebujete přístup na úrovni Přispěvatel alespoň ke skupině prostředků, do které nasazujete. V druhém případě je potřeba zadat název skupiny prostředků jako součást příkazu pro nastavení pomocí příznaku `-g`. 

Existují dvě možnosti pro nasazení: *místní* a *clusteru*. Nastavení `--cluster` (nebo `-c`) příznak umožňuje nasazení clusteru, který se zřídí clusteru služby ACS. Základní nastavení syntaxe vypadá takto:

`az ml env setup [-c] --location [location of environment resources] --name[name of environment]`

Tento příkaz inicializuje vaší Azure strojového učení prostředí s účet úložiště, ACR registru a službě Statistika aplikaci vytvořit ve vašem předplatném. Ve výchozím nastavení je-li zadána žádná příznak prostředí inicializována pro pouze místní nasazení (žádné ACS). Pokud potřebujete škálování služby, zadejte `--cluster` (nebo `-c`) příznak pro vytvoření clusteru služby ACS.

Podrobnosti o příkazu:

    --location -l        [Required]: Location for environment resources; an Azure region, e.g. eastus2.
    --name -n            [Required]: Name of environment to provision.
    --acr -r                       : ARM ID of ACR to associate with this environment.
    --agent-count -z               : Number of agents to provision in the ACS cluster. Default: 3.
    --cert-cname                   : CNAME of certificate.
    --cert-pem                     : Path to .pem file with certificate bytes.
    --cluster -c                   : Flag to provision ACS cluster. Off by default; specify this to force an ACS cluster deployment.
    --key-pem                      : Path to .pem file with certificate key.
    --master-count -m              : Number of master nodes to provision in the ACS cluster. Acceptable values: 1, 3, 5. Default: 1.
    --resource-group -g            : Resource group in which to create compute resource. Is created if it does not exist.
                                     If not provided, resource group is created with 'rg' appended to 'name.'.
    --service-principal-app-id -a  : App ID of service principal to use for configuring ML compute.
    --service-principal-password -p: Password associated with service principal.
    --storage -s                   : ARM ID of storage account to associate with this environment.
    --yes -y                       : Flag to answer 'yes' to any prompts. Command fails if user is not logged in.

Globální argumenty
```
    --debug                        : Increase logging verbosity to show all debug logs.
    --help -h                      : Show this help message and exit.
    --output -o                    : Output format.  Allowed values: json, jsonc, table, tsv. Default: json.
    --query                        : JMESPath query string. See http://jmespath.org/ for more information and examples.
    --verbose                      : Increase logging verbosity. Use --debug for full debug logs.
```
## <a name="model-commands"></a>Příkazy modelu

    list
    register
    show

**Zaregistrovat modelu**

Příkaz pro registraci modelu.

`az ml model register --model [path to model file] --name [model name]`

Podrobnosti o příkazu:

    --model -m [Required]: Model to register.
    --name -n  [Required]: Name of model to register.
    --description -d     : Description of the model.
    --tag -t             : Tags for the model. Multiple tags can be specified with additional -t arguments.
    -v                   : Verbosity flag.

Globální argumenty

    --debug              : Increase logging verbosity to show all debug logs.
    --help -h            : Show this help message and exit.
    --output -o          : Output format.  Allowed values: json, jsonc, table, tsv.  Default: json.
    --query              : JMESPath query string. See http://jmespath.org/ for more information and
                           examples.
    --verbose            : Increase logging verbosity. Use --debug for full debug logs.

## <a name="manifest-commands"></a>Příkazy manifestu

    create: Create an Operationalization Manifest. This command has two different
            sets of required arguments, depending on if you want to use previously registered
            model/s.
    list
    show

**Vytvoření manifestu**

Následující příkaz vytvoří soubor manifestu pro model. 

`az ml manifest create --manifest-name [your new manifest name] -f [path to code file] -r [runtime for the image, e.g. spark-py]`

Podrobnosti o příkazu:

    --manifest-name -n [Required]: Name of the manifest to create.
    -f                 [Required]: The code file to be deployed.
    -r                 [Required]: Runtime of the web service. Valid runtimes are spark-py|python.
    --conda-file -c              : Path to Conda Environment file.
    --dependency -d              : Files and directories required by the service. Multiple
                                   dependencies can be specified with additional -d arguments.
    --manifest-description       : Description of the manifest.
    --schema-file -s             : Schema file to add to the manifest.
    -p                           : A pip requirements.txt file needed by the code file.
    -v                           : Verbosity flag.

Argumenty registrované modelu

    --model-id -i                : [Required] Id of previously registered model to add to manifest.
                                   Multiple models can be specified with additional -i arguments.

Zrušení registrace modelu argumenty

    --model-file -m              : [Required] Model file to register. If used, must be combined with
                                   model name.

Globální argumenty

    --debug                      : Increase logging verbosity to show all debug logs.
    --help -h                    : Show this help message and exit.
    --output -o                  : Output format.  Allowed values: json, jsonc, table, tsv.
                                   Default: json.
    --query                      : JMESPath query string. See http://jmespath.org/ for more
                                   information and examples.
    --verbose                    : Increase logging verbosity. Use --debug for full debug logs.


## <a name="image-commands"></a>Příkazy bitové kopie

    create: Creates a docker image with the model and its dependencies. This command has two different sets of
            required arguments, depending on if you want to use a previously created manifest.
    list
    show
    usage

**Vytvoření bitové kopie**

Můžete vytvořit bitovou kopii s možností obsahující vytvořené před jeho manifestu. 

`az ml image create -n [image name] --manifest-id [the manifest ID]`

Nebo můžete vytvořit manifest a bitové kopie s jeden příkaz. 

`az ml image create -n [image name] --model-file [model file or folder path] -f [code file, e.g. the score.py file] -r [the runtime eg.g. spark-py which is the Docker container image base]`

Podrobnosti o příkazu:

    --image-name -n [Required]: The name of the image being created.
    --image-description       : Description of the image.
    --image-type              : The image type to create. Defaults to "Docker".
    -v                        : Verbosity flag.

Registrovaný manifestu argumenty

    --manifest-id             : [Required] Id of previously registered manifest to use in image creation.

Zrušení registrace manifestu argumenty

    --conda-file -c           : Path to Conda Environment file.
    --dependency -d           : Files and directories required by the service. Multiple dependencies can
                                be specified with additional -d arguments.
    --model-file -m           : [Required] Model file to register.
    --schema-file -s          : Schema file to add to the manifest.
    -f                        : [Required] The code file to be deployed.
    -p                        : A pip requirements.txt file needed by the code file.
    -r                        : [Required] Runtime of the web service. Valid runtimes are python|spark-py.


## <a name="service-commands"></a>Příkazy služby
Podporovány jsou následující příkazy pro službu. Informace o parametrech u každého příkazu, použijte parametr -h. Například použít `az ml service create realtime -h` zobrazíte podrobnosti o příkazu vytvořit.

    create
    delete
    keys
    list
    logs
    run
    show
    update
    usage

**Vytvoření služby**

Vytvoření služby s dříve vytvořenou bitovou kopii, použijte následující příkaz:

`az ml service create realtime --image-id [image to deploy] -n [service name]`

Vytvoření služby, manifest a bitovou kopii pomocí jednoho příkazu, použijte následující příkaz:

`az ml service create realtime --model-file [path to model file(s)] -f [path to model scoring file, e.g. score.py] -n [service name] -r [run time included in the image, e.g. spark-py]`

Příkazy podrobnosti:

    -n                                : [Required] Webservice name.
    --autoscale-enabled               : Enable automatic scaling of service replicas based on request demand.
                                        Allowed values: true, false. False if omitted.  Default: false.
    --autoscale-max-replicas          : If autoscale is enabled - sets the maximum number of replicas.
    --autoscale-min-replicas          : If autoscale is enabled - sets the minimum number of replicas.
    --autoscale-refresh-period-seconds: If autoscale is enabled - the interval of evaluating scaling demand.
    --autoscale-target-utilization    : If autoscale is enabled - target utilization of replicas time.
    --collect-model-data              : Enable model data collection. Allowed values: true, false. False if omitted.  Default: false.
    --cpu                             : Reserved number of CPU cores per service replica (can be fraction).
    --enable-app-insights -l          : Enable app insights. Allowed values: true, false. False if omitted.  Default: false.
    --memory                          : Reserved amount of memory per service replica, in M or G. (ex. 1G, 300M).
    --replica-max-concurrent-requests : Maximum number of concurrent requests that can be routed to a service replica.
    -v                                : Verbosity flag.
    -z                                : Number of replicas for a Kubernetes service.  Default: 1.

Argumenty registrované bitové kopie

    --image-id                        : [Required] Image to deploy to the service.

Zrušení registrace Image argumenty

    --conda-file -c                   : Path to Conda Environment file.
    --image-type                      : The image type to create. Defaults to "Docker".
    --model-file -m                   : [Required] The model to be deployed.
    -d                                : Files and directories required by the service. Multiple dependencies can be specified
                                        with additional -d arguments.
    -f                                : [Required] The code file to be deployed.
    -p                                : A pip requirements.txt file of package needed by the code file.
    -r                                : [Required] Runtime of the web service. Valid runtimes are python|spark-py.
    -s                                : Input and output schema of the web service.

Globální argumenty

    --debug                           : Increase logging verbosity to show all debug logs.
    --help -h                         : Show this help message and exit.
    --output -o                       : Output format.  Allowed values: json, jsonc, table, tsv. Default: json.
    --query                           : JMESPath query string. See http://jmespath.org/ for more information and examples.
    --verbose                         : Increase logging verbosity. Use --debug for full debug logs.


Poznámka: na `-d` příznak pro připojení závislosti: Pokud předáte název adresáře, který ještě není dodávat (zip, vkládání atd.), automaticky získá tar'ed adresáře a je předán hotová, pak automaticky jednoduchý na druhém konci. 

Pokud předáte v adresáři, který je již instalován, adresář je považován za soubor a předají je. Je zpřístupnění automaticky. předpokládá se, že pro zpracování, který do vašeho kódu.

**Získat podrobnosti služby**

Načíst podrobnosti ze služby včetně adresu URL, využití (včetně ukázková data, pokud byla vytvořena schématu).

`az ml service show realtime --name [service name]`

Podrobnosti o příkazu:

    --id -i    : The service id to show.
    --name -n  : Webservice name.
    -v         : Verbosity flag.

Globální argumenty

    --debug    : Increase logging verbosity to show all debug logs.
    --help -h  : Show this help message and exit.
    --output -o: Output format.  Allowed values: json, jsonc, table, tsv.  Default: json.
    --query    : JMESPath query string. See http://jmespath.org/ for more information and examples.
    --verbose  : Increase logging verbosity. Use --debug for full debug logs.

**Spouštění služby**

`az ml service run realtime -n [service name] -d [input_data]`

Podrobnosti o příkazu:

    --id -i    : The service id to show.
    --name -n  : Webservice name.
    -v         : Verbosity flag.

Globální argumenty

    --debug    : Increase logging verbosity to show all debug logs.
    --help -h  : Show this help message and exit.
    --output -o: Output format.  Allowed values: json, jsonc, table, tsv.  Default: json.
    --query    : JMESPath query string. See http://jmespath.org/ for more information and examples.
    --verbose  : Increase logging verbosity. Use --debug for full debug logs.

Příkaz

    az ml service run realtime

Argumenty – id -i: [vyžaduje] id služby skóre proti.
-d: data, která mají použít pro volání webové služby.
-v: Příznak podrobností.

Globální argumenty

    --debug    : Increase logging verbosity to show all debug logs.
    --help -h  : Show this help message and exit.
    --output -o: Output format.  Allowed values: json, jsonc, table, tsv. Default: json.
    --query    : JMESPath query string. See http://jmespath.org/ for more information and examples.
    --verbose  : Increase logging verbosity. Use --debug for full debug logs.
