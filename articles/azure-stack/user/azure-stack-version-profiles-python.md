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
ms.lastreviewed: 01/05/2019
<!-- dev: viananth -->
ms.openlocfilehash: c7c23352cea4f9e79b371f38112fb66ac31ac849
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55242293"
---
# <a name="use-api-version-profiles-with-python-in-azure-stack"></a>Použití profilů verzí API s využitím Pythonu ve službě Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

## <a name="python-and-api-version-profiles"></a>Profilů verzí Pythonu a rozhraní API

Python SDK podporuje profilů verzí API cílit na různé cloudové platformy, jako je Azure Stack a globální Azure. Pomocí profilů rozhraní API ve vytváření řešení pro hybridní cloud. Python SDK podporuje následující profily rozhraní API:

- **nejnovější**  
    Tento profil zaměřuje na nejnovější verze rozhraní API pro všechny poskytovatele služby na platformě Azure.
- **2018-03-01-hybrid**     
    Tento profil zaměřuje na nejnovější verze rozhraní API pro všechny poskytovatele prostředků na platformě Azure Stack.
- **2017-03-09-profile**  
    Tento profil, zaměřuje nejvíce kompatibilní verze rozhraní API poskytovatele prostředků, které jsou podporované ve službě Azure Stack.

   Další informace o profilech rozhraní API a služby Azure Stack najdete v tématu [profilů verzí API spravovat ve službě Azure Stack](azure-stack-version-profiles.md).

## <a name="install-the-azure-python-sdk"></a>Instalace Azure Python SDK

1. Nainstalovat Git z [oficiální web](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
2. Pokyny, jak nainstalovat sadu SDK pro Python, najdete v části [Azure pro vývojáře v Pythonu](/python/azure/python-sdk-azure-install?view=azure-python).
3. Pokud není k dispozici, vytvořte předplatné a uložte ID předplatného pro pozdější použití. Pokyny k vytvoření předplatného najdete v tématu [vytvářet předplatná na nabídky ve službě Azure Stack](../azure-stack-subscribe-plan-provision-vm.md).
4. Vytvoření instančního objektu a uložit jeho ID a tajný klíč. Pokyny o tom, jak vytvořit instanční objekt pro Azure Stack najdete v tématu [poskytují aplikacím přístup ke službě Azure Stack](../azure-stack-create-service-principals.md).
5. Ujistěte se, že má instančního objektu služby roli Přispěvatel nebo vlastník v rámci předplatného. Pokyny o tom, jak přiřadit roli instančnímu objektu služby najdete v tématu [poskytují aplikacím přístup ke službě Azure Stack](../azure-stack-create-service-principals.md).

## <a name="prerequisites"></a>Požadavky

Chcete-li použít sady Azure Python SDK pro Azure Stack, musíte zadat následující hodnoty a pak nastavte hodnoty proměnné prostředí. Postupujte podle pokynů pod tabulkou pro váš operační systém na nastavení proměnných prostředí.

| Value | Proměnné prostředí | Popis |
|---------------------------|-----------------------|-------------------------------------------------------------------------------------------------------------------------|
| ID tenanta | AZURE_TENANT_ID | Výhody služby Azure Stack [ID tenanta](../azure-stack-identity-overview.md). |
| ID klienta | AZURE_CLIENT_ID | Služba ID instančního objektu aplikace neuloží, když se vytvoří nový instanční objekt služby v předchozí části tohoto článku. |
| ID předplatného | AZURE_SUBSCRIPTION_ID | [ID předplatného](../azure-stack-plan-offer-quota-overview.md#subscriptions) je, jak získat přístup k nabídky ve službě Azure Stack. |
| Tajný kód klienta | AZURE_CLIENT_SECRET | Služba hlavní tajný klíč aplikace neuloží, když se vytvoří nový instanční objekt služby. |
| Koncový bod Resource Manageru | ARM_ENDPOINT | Zobrazit [koncový bod služby Azure Stack resource manager](azure-stack-version-profiles-ruby.md#the-azure-stack-resource-manager-endpoint). |
| Umístění prostředku | AZURE_RESOURCE_LOCATION | Umístění prostředku vaším prostředím Azure Stack.

## <a name="python-samples-for-azure-stack"></a>Ukázky Pythonu pro službu Azure Stack

Zde jsou některé z ukázek kódu, který je k dispozici pro službu Azure Stack pomocí sady Python SDK:

- [Správa prostředků a skupin prostředků](https://azure.microsoft.com/resources/samples/hybrid-resourcemanager-python-manage-resources/).
- [Správa účtu úložiště](https://azure.microsoft.com/resources/samples/hybrid-storage-python-manage-storage-account/).
- [Správa virtuálních počítačů](https://azure.microsoft.com/resources/samples/hybrid-compute-python-manage-vm/).

## <a name="python-manage-virtual-machine-sample"></a>Správa Pythonu ukázkový virtuální počítač

Následující vzorový kód můžete použít k provádění běžných úloh správy pro virtuální počítače v Azure stacku. Vzorový kód ukazuje na:

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

Kód, který provádí těchto operací najdete v tématu **run_example()** funkce ve skriptu Pythonu **example.py** v úložišti Githubu [hybridní-výpočetní-Python-spravovat-VM](https://github.com/Azure-Samples/Hybrid-Compute-Python-Manage-VM).

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
    git clone https://github.com/Azure-Samples/Hybrid-Compute-Python-Manage-VM.git
    ```

5. Instalace závislosti pomocí pip:

    ```bash
    cd Hybrid-Compute-Python-Manage-VM
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
    export AZURE_RESOURCE_LOCATION={your AzureStack Resource location}
    ```

8. Pokud chcete tuto ukázku spustit, musí být Ubuntu 16.04-LTS a WindowsServer 2012-R2-Datacenter imagí v Tržišti Azure Stack. Mohou to být buď [stáhli z Azure](../azure-stack-download-azure-marketplace-item.md), nebo přidat do [úložiště Imagí platforem](../azure-stack-add-vm-image.md).

9. Spusťte ukázku:

    ```python
    python example.py
    ```


## <a name="next-steps"></a>Další postup

- [Centrum Azure vývoje v Pythonu](https://azure.microsoft.com/develop/python/)
- [Dokumentace ke službě Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/)
- [Postup výuky pro Virtual Machines](/learn/paths/deploy-a-website-with-azure-virtual-machines/)
- Pokud nemáte předplatné Microsoft Azure, můžete získat bezplatný zkušební účet [tady](https://go.microsoft.com/fwlink/?LinkId=330212).
