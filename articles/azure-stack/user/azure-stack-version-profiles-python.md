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
ms.date: 04/30/2018
ms.author: mabrigg
ms.reviewer: sijuman
<!-- dev: viananth -->
ms.openlocfilehash: a4fe62ba8c0732745326831b977e8975e1210436
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/01/2018
---
# <a name="use-api-version-profiles-with-python-in-azure-stack"></a>Použití profilů verze rozhraní API s Pythonem v Azure zásobníku

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

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

5.  Vytvoření [instanční objekt](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-create-service-principals) pro práci s Azure zásobníku. Ověřte, zda má instanční objekt [roli Přispěvatel nebo vlastníka](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-create-service-principals#assign-role-to-service-principal) vaše předplatné.

6.  Nastavte následující proměnné a exportovat těchto proměnných prostředí do své aktuální prostředí. 

`Note: provide an explanation of where these variables come from?`

    ````bash
    export AZURE_TENANT_ID={your tenant id}
    export AZURE_CLIENT_ID={your client id}
    export AZURE_CLIENT_SECRET={your client secret}
    export AZURE_SUBSCRIPTION_ID={your subscription id}
    export ARM_ENDPOINT={your AzureStack Resource Manager Endpoint}
    ```

7.  Poznámka: aby bylo možné tuto ukázku spustit, Ubuntu 16.04-LTS a Windows Server 2012 R2 – Datacenter obrázků musí být přítomen v zásobníku Azure Marketplace. To může být buď [stahovány ze služby Azure](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-download-azure-marketplace-item) nebo [přidat do úložiště bitové kopie platformy](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-add-vm-image).


8. Spusťte ukázku.

    ```
    python unmanaged-disks\example.py
    ```

## <a name="notes"></a>Poznámky

Pravděpodobně budete mít tendenci k pokusu o načtení disk operačního systému Virtuálního počítače pomocí `virtual_machine.storage_profile.os_disk`.
V některých případech to může provést požadovanou, ale mějte na paměti, že nabízí `OSDisk` objektu.
Aby bylo možné aktualizovat velikost disku operačního systému, jako `example.py` nemá, musíte není `OSDisk` objekt ale `Disk` objektu.
`example.py` Získá `Disk` objekt s následující:

```python
os_disk_name = virtual_machine.storage_profile.os_disk.name
os_disk = compute_client.disks.get(GROUP_NAME, os_disk_name)
```

## <a name="next-steps"></a>Další postup

- [Azure Python vývoj Center](https://azure.microsoft.com/develop/python/)
- [Dokumentace k Azure virtuální počítače](https://azure.microsoft.com/services/virtual-machines/)
- [Studijní postup pro virtuální počítače](https://azure.microsoft.com/documentation/learning-paths/virtual-machines/)
- Pokud nemáte předplatné Microsoft Azure získáte Bezplatný zkušební účet [zde](http://go.microsoft.com/fwlink/?LinkId=330212).
