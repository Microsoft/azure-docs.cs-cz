---
title: Pomocí profilů rozhraní API verze Python v zásobníku Azure | Microsoft Docs
description: Další informace o použití profilů verze rozhraní API s Pythonem v zásobníku Azure.
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
ms.openlocfilehash: d17ba9ed4548a986d6846d934aee197609ec80ca
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/06/2018
ms.locfileid: "34806832"
---
# <a name="use-api-version-profiles-with-python-in-azure-stack"></a>Použití profilů verze rozhraní API s Pythonem v Azure zásobníku

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

## <a name="python-and-api-version-profiles"></a>Profily verze Python a rozhraní API

Python SDK podporuje profily verze rozhraní API pro platformu jiný cloud, jako je například Azure zásobníku a globální Azure. Pomocí profilů rozhraní API pro vytváření řešení pro hybridní cloud. Python SDK podporuje následující profily rozhraní API:

1. **Nejnovější**  
    Profil cílí nejnovější verze rozhraní API pro všechny poskytovatele služeb v platformě Azure.
2.  **2017-03-09profil**  
    **2017-03-09profil**  
    Profil cílí verze rozhraní API poskytovatele prostředků nepodporuje Azure zásobníku.

    Další informace o profilech API a zásobník Azure najdete v tématu [profily verze spravovat rozhraní API v Azure zásobníku](azure-stack-version-profiles.md).

## <a name="install-azure-python-sdk"></a>Instalace sady Azure Python SDK

1.  Nainstalovat Git z [oficiální web](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
2.  Pokyny k instalaci sady SDK pro Python najdete v tématu [Azure pro vývojáře v Pythonu](https://docs.microsoft.com/python/azure/python-sdk-azure-install?view=azure-python).
3.  Pokud není k dispozici, vytvořit odběr a uložte ID předplatného pro pozdější použití. Pokyny pro vytvoření odběru naleznete v tématu [vytvoření odběrů nabídky v Azure zásobníku](../azure-stack-subscribe-plan-provision-vm.md). 
4.  Vytvořit objekt služby a uložte jeho ID a tajný klíč. Pokyny k vytvoření objektu služby pro Azure zásobníku najdete v tématu [poskytnout přístup aplikace k Azure zásobníku](../azure-stack-create-service-principals.md). 
5.  Ujistěte se, zda že má vaše objektu služby roli Přispěvatel nebo vlastníka vaše předplatné. Pokyny o tom, jak přiřadit role objektu služby najdete v tématu [poskytnout přístup aplikace k Azure zásobníku](../azure-stack-create-service-principals.md).

## <a name="prerequisites"></a>Požadavky

Chcete-li použít Python Azure SDK s Azure zásobníku, musíte zadat následující hodnoty a potom nastavte hodnoty proměnné prostředí. Postupujte podle pokynů pod tabulkou pro operační systém na nastavení proměnné prostředí. 

| Hodnota | Proměnné prostředí | Popis |
|---------------------------|-----------------------|-------------------------------------------------------------------------------------------------------------------------|
| ID tenanta | AZURE_TENANT_ID | Hodnota zásobník Azure [ID klienta](../azure-stack-identity-overview.md). |
| ID klienta | AZURE_CLIENT_ID | Služba ID hlavní aplikace se neuloží, když instanční objekt byl vytvořen v předchozí části tohoto dokumentu. |
| ID předplatného | AZURE_SUBSCRIPTION_ID | [ID předplatného](../azure-stack-plan-offer-quota-overview.md#subscriptions) je, jak přistupovat k nabídky v Azure zásobníku. |
| Tajný kód klienta | AZURE_CLIENT_SECRET | Aplikace služby hlavní tajný klíč uložit při vytvoření objektu služby. |
| Koncový bod služby Resource Manager | ARM_ENDPOINT | V tématu [koncový bod služby zásobník Azure resource manager](azure-stack-version-profiles-ruby.md#the-azure-stack-resource-manager-endpoint). |


## <a name="python-samples-for-azure-stack"></a>Ukázky jazyka Python pro Azure zásobníku 

Následující ukázky kódu můžete provádět běžné úlohy správy pro virtuální počítače v do Azure zásobníku.

Ukázky kódu ukazují na:

- Vytváření virtuálních počítačů:
    - Vytvoření virtuálního počítače s Linuxem
    - Vytvoření virtuálního počítače s Windows
- Aktualizujte virtuální počítač:
    - Rozbalte položku na jednotku
    - Značka virtuálního počítače
    - Připojte datových disků
    - Odpojení datových disků
- Provoz virtuálního počítače:
    - Spuštění virtuálního počítače
    - Zastavit virtuální počítač
    - Restartování virtuálního počítače
- Seznam virtuálních počítačů
- Odstranění virtuálního počítače

Projít k provádění těchto operací, podívejte se **run_example()** funkce ve skriptu Python **Hybrid/unmanaged-disks/example.py** v úložišti GitHub [ virtuální počítače python spravovat](https://github.com/viananth/virtual-machines-python-manage/tree/8643ed4bec62aae6fdb81518f68d835452872f88).

Každé operace jasně označený verzí komentář a tiskové funkce.
Příklady, nemusí nutně být v pořadí uvedeném v seznamu nahoře.


## <a name="run-the-python-sample"></a>Spustit ukázku Python

1.  Pokud ještě nemáte, [instalaci Pythonu](https://www.python.org/downloads/).

    Tato ukázka (a sady SDK) je kompatibilní s Python 2.7, 3,4, 3.5 a 3.6.

2.  Obecná doporučení pro vývoj Python je použití virtuálního prostředí. 
    Další informace najdete v tématu https://docs.python.org/3/tutorial/venv.html
    
    Instalace a inicializace virtuálního prostředí s modulem "venv" na Python 3 (je třeba nainstalovat [virtualenv](https://pypi.python.org/pypi/virtualenv) pro Python 2.7):

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

4.  Nainstalujte pomocí nástroje pip závislosti.

    ````bash
    cd virtual-machines-python-manage\Hybrid
    pip install -r requirements.txt
    ````

5.  Vytvoření [instanční objekt](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals) pro práci s Azure zásobníku. Ověřte, zda má instanční objekt [roli Přispěvatel nebo vlastníka](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals#assign-role-to-service-principal) vaše předplatné.

6.  Nastavte následující proměnné a exportovat těchto proměnných prostředí do své aktuální prostředí. 

    ````bash
    export AZURE_TENANT_ID={your tenant id}
    export AZURE_CLIENT_ID={your client id}
    export AZURE_CLIENT_SECRET={your client secret}
    export AZURE_SUBSCRIPTION_ID={your subscription id}
    export ARM_ENDPOINT={your AzureStack Resource Manager Endpoint}
    ```

7.  In order to run this sample, Ubuntu 16.04-LTS and WindowsServer 2012-R2-Datacenter images must be present in Azure Stack market place. These can be either [downloaded from Azure](https://docs.microsoft.com/azure/azure-stack/azure-stack-download-azure-marketplace-item) or [added to Platform Image Repository](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-vm-image).

8. Run the sample.

    ```
    python unmanaged-disks\example.py
    ```

## Notes

You may be tempted to try to retrieve a VM's OS disk by using
`virtual_machine.storage_profile.os_disk`.
In some cases, this may do what you want,
but be aware that it gives you an `OSDisk` object.
In order to update the OS Disk's size, as `example.py` does,
you need not an `OSDisk` object but a `Disk` object.
`example.py` gets the `Disk` object with the following:

```python
os_disk_name = virtual_machine.storage_profile.os_disk.name
os_disk = compute_client.disks.get(GROUP_NAME, os_disk_name)
```

## Next steps

- [Azure Python Development Center](https://azure.microsoft.com/develop/python/)
- [Azure Virtual Machines documentation](https://azure.microsoft.com/services/virtual-machines/)
- [Learning Path for Virtual Machines](https://azure.microsoft.com/documentation/learning-paths/virtual-machines/)
- If you don't have a Microsoft Azure subscription, you can get a FREE trial account [here](http://go.microsoft.com/fwlink/?LinkId=330212).