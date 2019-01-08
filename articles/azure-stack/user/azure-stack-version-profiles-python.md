---
title: Použití profilů verzí API s využitím Pythonu ve službě Azure Stack | Dokumentace Microsoftu
description: Další informace o použití profilů verzí API s využitím Pythonu ve službě Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2019
ms.author: sethm
ms.reviewer: sijuman
<!-- dev: viananth -->
ms.openlocfilehash: cafae6d71401bc44813b2e366f8e72f7b806236b
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/07/2019
ms.locfileid: "54062771"
---
# <a name="use-api-version-profiles-with-python-in-azure-stack"></a>Použití profilů verzí API s využitím Pythonu ve službě Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

## <a name="python-and-api-version-profiles"></a>Profilů verzí Pythonu a rozhraní API

Python SDK podporuje profilů verzí API cílit na různé cloudové platformy, jako je Azure Stack a globální Azure. Pomocí profilů rozhraní API ve vytváření řešení pro hybridní cloud. Python SDK podporuje následující profily rozhraní API:

1. **nejnovější**  
    Profil, který cílí na nejnovější verze rozhraní API pro všechny poskytovatele služby na platformě Azure.
2. **2017-03-09-profile**  
   **2017-03-09-profile**  
   Profil, který cílí na verze rozhraní API poskytovatele prostředků, které jsou podporované ve službě Azure Stack.

   Další informace o profilech rozhraní API a služby Azure Stack najdete v tématu [profilů verzí API spravovat ve službě Azure Stack](azure-stack-version-profiles.md).

## <a name="install-the-azure-python-sdk"></a>Instalace Azure Python SDK

1. Nainstalovat Git z [oficiální web](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
2. Pokyny, jak nainstalovat sadu SDK pro Python, najdete v části [Azure pro vývojáře v Pythonu](/python/azure/python-sdk-azure-install?view=azure-python).
3. Pokud není k dispozici, vytvořte předplatné a uložte ID předplatného pro pozdější použití. Pokyny k vytvoření předplatného najdete v tématu [vytvářet předplatná na nabídky ve službě Azure Stack](../azure-stack-subscribe-plan-provision-vm.md).
4. Vytvoření instančního objektu a uložit jeho ID a tajný klíč. Pokyny o tom, jak vytvořit instanční objekt pro Azure Stack najdete v tématu [poskytují aplikacím přístup ke službě Azure Stack](../azure-stack-create-service-principals.md).
5. Ujistěte se, že má instančního objektu služby roli Přispěvatel nebo vlastník v rámci předplatného. Pokyny o tom, jak přiřadit roli instančnímu objektu služby najdete v tématu [poskytují aplikacím přístup ke službě Azure Stack](../azure-stack-create-service-principals.md).

## <a name="prerequisites"></a>Požadavky

Chcete-li použít sady Azure Python SDK pro Azure Stack, musíte zadat následující hodnoty a pak nastavte hodnoty proměnné prostředí. Postupujte podle pokynů pod tabulkou pro váš operační systém na nastavení proměnných prostředí.

| Hodnota | Proměnné prostředí | Popis |
|---------------------------|-----------------------|-------------------------------------------------------------------------------------------------------------------------|
| ID tenanta | AZURE_TENANT_ID | Výhody služby Azure Stack [ID tenanta](../azure-stack-identity-overview.md). |
| ID klienta | AZURE_CLIENT_ID | Služba ID instančního objektu aplikace neuloží, když se vytvoří nový instanční objekt služby v předchozí části tohoto článku. |
| ID předplatného | AZURE_SUBSCRIPTION_ID | [ID předplatného](../azure-stack-plan-offer-quota-overview.md#subscriptions) je, jak získat přístup k nabídky ve službě Azure Stack. |
| Tajný kód klienta | AZURE_CLIENT_SECRET | Služba hlavní tajný klíč aplikace neuloží, když se vytvoří nový instanční objekt služby. |
| Koncový bod Resource Manageru | ARM_ENDPOINT | Zobrazit [koncový bod služby Azure Stack resource manager](azure-stack-version-profiles-ruby.md#the-azure-stack-resource-manager-endpoint). |

## <a name="python-samples-for-azure-stack"></a>Ukázky Pythonu pro službu Azure Stack

Následující ukázky kódu můžete použít k provádění běžných úloh správy pro virtuální počítače v Azure stacku. Ukázky kódu ukazují, na:

- Vytvoření virtuálních počítačů:
  - Vytvoření virtuálního počítače s Linuxem
  - Vytvoření virtuálního počítače s Windows
- Aktualizujte virtuální počítač:
  - Rozšířit jednotku
  - Označit virtuální počítač
  - Připojit datové disky
  - Odpojení datových disků
- Provoz virtuálního počítače:
  - Spuštění virtuálního počítače
  - Zastavit virtuální počítač
  - Restartování virtuálního počítače
- Seznam virtuálních počítačů
- Odstranění virtuálního počítače

Kód, který provádí těchto operací najdete v tématu **run_example()** funkce ve skriptu Pythonu **Hybrid/unmanaged-disks/example.py** v úložišti Githubu [ virtuální počítače python Správa](https://github.com/Azure-Samples/virtual-machines-python-manage).

Každá operace jasně označené jako komentář a tisku funkcí. Příklady není nutně v pořadí uvedeném v tomto seznamu.

## <a name="run-the-python-sample"></a>Spuštění ukázky Pythonu

1. Pokud nemáte, [nainstalujte Python](https://www.python.org/downloads/). Tato ukázka (a sady SDK) je kompatibilní s Pythonem 2.7, 3.4, 3.5 a 3.6.

2. Obecná doporučení pro vývoj v jazyce Python se má použít virtuální prostředí. Další informace najdete v tématu [dokumentace pro Python](https://docs.python.org/3/tutorial/venv.html).

3. Instalace a inicializovat virtuální prostředí s modulem "venv" na jazyce Python 3 (je nutné nainstalovat [virtualenv](https://pypi.python.org/pypi/virtualenv) for Python 2.7):

    ```bash
    python -m venv mytestenv # Might be "python3" or "py -3.6" depending on your Python installation
    cd mytestenv
    source bin/activate      # Linux shell (Bash, ZSH, etc.) only
    ./scripts/activate       # PowerShell only
    ./scripts/activate.bat   # Windows CMD only
    ```

4. Naklonujte úložiště:

    ```bash
    git clone https://github.com/Azure-Samples/virtual-machines-python-manage.git
    ```

5. Instalace závislosti pomocí pip:

    ```bash
    cd virtual-machines-python-manage\Hybrid
    pip install -r requirements.txt
    ```

6. Vytvoření [instanční objekt služby](../azure-stack-create-service-principals.md) pro práci s Azure Stack. Ujistěte se, že má váš objekt služby [role přispěvatele nebo vlastníka](../azure-stack-create-service-principals.md#assign-a-role) v rámci předplatného.

7. Nastavte následující proměnné a exportujte tyto proměnné prostředí v aktuálním prostředí:

    ```bash
    export AZURE_TENANT_ID={your tenant id}
    export AZURE_CLIENT_ID={your client id}
    export AZURE_CLIENT_SECRET={your client secret}
    export AZURE_SUBSCRIPTION_ID={your subscription id}
    export ARM_ENDPOINT={your AzureStack Resource Manager Endpoint}
    ```

8. Pokud chcete tuto ukázku spustit, musí být Ubuntu 16.04-LTS a WindowsServer 2012-R2-Datacenter imagí v Tržišti Azure Stack. Mohou to být buď [stáhli z Azure](../azure-stack-download-azure-marketplace-item.md), nebo přidat do [úložiště Imagí platforem](../azure-stack-add-vm-image.md).

9. Spusťte ukázku:

    ```python
    python unmanaged-disks\example.py
    ```

## <a name="notes"></a>Poznámky

Jste možná v pokušení se pokouší načíst disk s operačním systémem Virtuálního počítače pomocí `virtual_machine.storage_profile.os_disk`. V některých případech to může provést požadovanou, ale mějte na paměti, že poskytuje **OSDisk** objektu. Za účelem aktualizace velikost disku operačního systému, jako `example.py` provádí, můžete **disku** objekt nelze **OSDisk** objektu. `example.py` Získá **disku** objektu s následujícími vlastnostmi:

```python
os_disk_name = virtual_machine.storage_profile.os_disk.name
os_disk = compute_client.disks.get(GROUP_NAME, os_disk_name)
```

## <a name="next-steps"></a>Další postup

- [Centrum Azure vývoje v Pythonu](https://azure.microsoft.com/develop/python/)
- [Dokumentace ke službě Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/)
- [Postup výuky pro Virtual Machines](/learn/paths/deploy-a-website-with-azure-virtual-machines/)
- Pokud nemáte předplatné Microsoft Azure, můžete získat bezplatný zkušební účet [tady](https://go.microsoft.com/fwlink/?LinkId=330212).
