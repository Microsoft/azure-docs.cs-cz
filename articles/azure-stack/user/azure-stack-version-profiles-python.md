---
title: Použití profilů verzí API s využitím Pythonu ve službě Azure Stack | Dokumentace Microsoftu
description: Další informace o použití profilů verzí API s využitím Pythonu ve službě Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/21/2018
ms.author: mabrigg
ms.reviewer: sijuman
<!-- dev: viananth -->
ms.openlocfilehash: 23b5b5d79f0f905d7c4a173247232ede2cad2877
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39412443"
---
# <a name="use-api-version-profiles-with-python-in-azure-stack"></a>Použití profilů verzí API s využitím Pythonu ve službě Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

## <a name="python-and-api-version-profiles"></a>Profilů verzí Pythonu a rozhraní API

Python SDK podporuje profilů verzí API cílit na různé cloudové platformy, jako je Azure Stack a globální Azure. Pomocí profilů rozhraní API ve vytváření řešení pro hybridní cloud. Python SDK podporuje následující profily rozhraní API:

1. **nejnovější**  
    Profil, který cílí na nejnovější verze rozhraní API pro všechny poskytovatele služby na platformě Azure.
2.  **2017-03-09-profile**  
    **2017-03-09-profile**  
    Profil, který cílí na verze rozhraní API poskytovatele prostředků, které jsou podporované ve službě Azure Stack.

    Další informace o profilech rozhraní API a služby Azure Stack najdete v tématu [profilů verzí API spravovat ve službě Azure Stack](azure-stack-version-profiles.md).

## <a name="install-azure-python-sdk"></a>Instalace sady Azure Python SDK

1.  Nainstalovat Git z [oficiální web](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
2.  Pokyny k instalaci sady Python SDK najdete v tématu [Azure pro vývojáře v Pythonu](https://docs.microsoft.com/python/azure/python-sdk-azure-install?view=azure-python).
3.  Pokud není k dispozici, vytvořte předplatné a uložte ID předplatného pro pozdější použití. Pokyny pro vytvoření odběru naleznete v tématu [vytvářet předplatná na nabídky ve službě Azure Stack](../azure-stack-subscribe-plan-provision-vm.md). 
4.  Vytvoření instančního objektu a uložit jeho ID a tajný klíč. Pokyny k vytvoření instančního objektu pro Azure Stack najdete v tématu [poskytují aplikacím přístup ke službě Azure Stack](../azure-stack-create-service-principals.md). 
5.  Zajistěte, aby že váš objekt služby má role Přispěvatel nebo vlastník v rámci předplatného. Pokyny o tom, jak přiřadit roli instančnímu objektu služby najdete v tématu [poskytují aplikacím přístup ke službě Azure Stack](../azure-stack-create-service-principals.md).

## <a name="prerequisites"></a>Požadavky

Chcete-li používat sadu Azure SDK pro Python pomocí služby Azure Stack, musíte zadat následující hodnoty a pak nastavte hodnoty proměnné prostředí. Postupujte podle pokynů pod tabulkou pro váš operační systém na nastavení proměnné prostředí. 

| Hodnota | Proměnné prostředí | Popis |
|---------------------------|-----------------------|-------------------------------------------------------------------------------------------------------------------------|
| ID tenanta | AZURE_TENANT_ID | Výhody služby Azure Stack [ID tenanta](../azure-stack-identity-overview.md). |
| ID klienta | AZURE_CLIENT_ID | Služba ID instančního objektu aplikace neuloží, když se vytvoří nový instanční objekt služby v předchozí části tohoto dokumentu. |
| ID předplatného | AZURE_SUBSCRIPTION_ID | [ID předplatného](../azure-stack-plan-offer-quota-overview.md#subscriptions) je, jak získat přístup k nabídky ve službě Azure Stack. |
| Tajný kód klienta | AZURE_CLIENT_SECRET | Aplikace instančního objektu služby tajný klíč neuloží, když se vytvoří nový instanční objekt služby. |
| Koncový bod Resource Manageru | ARM_ENDPOINT | Zobrazit [koncový bod služby Azure Stack resource manager](azure-stack-version-profiles-ruby.md#the-azure-stack-resource-manager-endpoint). |


## <a name="python-samples-for-azure-stack"></a>Ukázky Pythonu pro službu Azure Stack 

Následující ukázky kódu můžete použít k provádění běžných úloh správy pro virtuální počítače v Azure stacku.

Ukázky kódu ukazují, na:

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

Ke kontrole kódu k provedení těchto operací, podívejte se **run_example()** funkce ve skriptu Pythonu **Hybrid/unmanaged-disks/example.py** v úložišti Githubu [ virtuální počítače python Správa](https://github.com/viananth/virtual-machines-python-manage/tree/8643ed4bec62aae6fdb81518f68d835452872f88).

Každá operace jasně označené jako komentář a tisku funkcí.
Příklady není nutně v pořadí uvedeném v seznamu nahoře.


## <a name="run-the-python-sample"></a>Spuštění ukázky Pythonu

1.  Pokud ještě nemáte, [nainstalujte Python](https://www.python.org/downloads/).

    Tato ukázka (a sady SDK) je kompatibilní s Pythonem 2.7, 3.4, 3.5 a 3.6.

2.  Obecná doporučení pro vývoj v jazyce Python se má použít virtuální prostředí. 
    Další informace najdete v tématu https://docs.python.org/3/tutorial/venv.html
    
    Instalace a inicializovat virtuální prostředí s modulem "venv" na jazyce Python 3 (je nutné nainstalovat [virtualenv](https://pypi.python.org/pypi/virtualenv) for Python 2.7):

    ````bash
    python -m venv mytestenv # Might be "python3" or "py -3.6" depending on your Python installation
    cd mytestenv
    source bin/activate      # Linux shell (Bash, ZSH, etc.) only
    ./scripts/activate       # PowerShell only
    ./scripts/activate.bat   # Windows CMD only
    ````

3.  Naklonujte úložiště.

    ````bash
    git clone https://github.com/Azure-Samples/virtual-machines-python-manage.git
    ````

4.  Instalace závislostí pomocí nástroje pip.

    ````bash
    cd virtual-machines-python-manage\Hybrid
    pip install -r requirements.txt
    ````

5.  Vytvoření [instanční objekt služby](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals) pro práci s Azure Stack. Ujistěte se, že má váš objekt služby [role přispěvatele nebo vlastníka](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals#assign-role-to-service-principal) v rámci předplatného.

6.  Nastavte následující proměnné a vyexportovat tyto proměnné prostředí do aktuálního prostředí. 

    ```bash
    export AZURE_TENANT_ID={your tenant id}
    export AZURE_CLIENT_ID={your client id}
    export AZURE_CLIENT_SECRET={your client secret}
    export AZURE_SUBSCRIPTION_ID={your subscription id}
    export ARM_ENDPOINT={your AzureStack Resource Manager Endpoint}
    ```

7.  Aby bylo možné tuto ukázku spustit, musí být součástí Azure stacku trh Ubuntu 16.04-LTS a obrázky WindowsServer 2012-R2-Datacenter. Mohou to být buď [stáhli z Azure](https://docs.microsoft.com/azure/azure-stack/azure-stack-download-azure-marketplace-item) nebo [přidat do úložiště Imagí platforem](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-vm-image).

8. Spusťte ukázku.

    ```
    python unmanaged-disks\example.py
    ```

## <a name="notes"></a>Poznámky

Jste možná v pokušení se pokouší načíst disk s operačním systémem Virtuálního počítače pomocí `virtual_machine.storage_profile.os_disk`.
V některých případech to může provést požadovanou, ale mějte na paměti, že poskytuje `OSDisk` objektu.
Za účelem aktualizace velikost disku operačního systému, jako `example.py` , které potřebuje není `OSDisk` objekt ale `Disk` objektu.
`example.py` Získá `Disk` objektu následujícím kódem:

```python
os_disk_name = virtual_machine.storage_profile.os_disk.name
os_disk = compute_client.disks.get(GROUP_NAME, os_disk_name)
```

## <a name="next-steps"></a>Další postup

- [Centrum Azure vývoje v Pythonu](https://azure.microsoft.com/develop/python/)
- [Dokumentace ke službě Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/)
- [Postup výuky pro Virtual Machines](https://azure.microsoft.com/documentation/learning-paths/virtual-machines/)
- Pokud nemáte předplatné Microsoft Azure, můžete získat bezplatný zkušební účet [tady](http://go.microsoft.com/fwlink/?LinkId=330212).
