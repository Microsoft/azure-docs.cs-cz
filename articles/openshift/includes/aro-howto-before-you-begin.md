---
author: sabbour
ms.author: asabbour
ms.date: 4/5/2020
ms.openlocfilehash: 1fded0ad08af4b1e5d915e32e09087c1a78bd318
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520690"
---
### <a name="create-the-cluster"></a>Vytvoření clusteru

Postupujte podle kurzu a [vytvořte cluster Azure Red Hat OpenShift](../tutorial-create-cluster.md). Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít spuštěnou verzi Azure CLI 2.6.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli).

### <a name="connect-to-the-cluster"></a>Připojení ke clusteru

Pokud chcete spravovat cluster Azure Red Hat OpenShift, použijte klienta příkazového řádku OpenShift ( [oC](https://docs.openshift.com/container-platform/4.7/cli_reference/openshift_cli/getting-started-cli.html)).

> [!NOTE]
> Doporučujeme, abyste si [nainstalovali příkazový řádek OpenShift](../tutorial-connect-cluster.md) na [Azure Cloud Shell](https://shell.azure.com/) a použili ho pro všechny operace příkazového řádku níže. Spusťte prostředí z shell.azure.com nebo kliknutím na odkaz:
>
> [![Vložené spuštění](https://docs.microsoft.com/azure/includes/media/cloud-shell-try-it/hdi-launch-cloud-shell.png "Spuštění služby Azure Cloud Shell")](https://shell.azure.com/bash)

Podle kurzu nainstalujte rozhraní příkazového řádku, načtěte přihlašovací údaje clusteru a [Připojte se ke clusteru](../tutorial-connect-cluster.md) pomocí webové konzole a OpenShift CLI.

Po přihlášení by se vám měla zobrazit zpráva oznamující, že `default` projekt používáte.

```output
Login successful.

You have access to 61 projects, the list has been suppressed. You can list all projects with 'oc projects'

Using project "default".
```
