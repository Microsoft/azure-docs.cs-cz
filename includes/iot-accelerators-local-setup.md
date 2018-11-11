---
title: zahrnout soubor
description: zahrnout soubor
services: iot-accelerators
author: avneet723
ms.service: iot-accelerators
ms.topic: include
ms.date: 10/29/2018
ms.author: avneet723
ms.custom: include file
ms.openlocfilehash: 900d75f826830ea7336044a892506d3bec546e30
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2018
ms.locfileid: "51283908"
---
## <a name="download-the-source-code"></a>Stáhněte si zdrojový kód

Vzdálené monitorování úložišť zdrojového kódu zahrnout soubory konfigurace Dockeru, které potřebujete pro spuštění imagí Dockeru mikroslužeb.

Klonovat a vytvořte místní verzi úložiště, použijte prostředí příkazového řádku přejděte do složky na místním počítači vhodný. Poté spusťte následující sady příkazů naklonujte buď .NET nebo Javě úložiště:

Pokud chcete stáhnout nejnovější verzi implementace mikroslužby .NET, spusťte:

```cmd/sh
git clone --recurse-submodules https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet.git

# To retrieve the latest submodules, run the following command:

cd azure-iot-pcs-remote-monitoring-dotnet
git submodule foreach git pull origin master
```

Pokud chcete stáhnout nejnovější verzi implementace mikroslužeb Java, spusťte:

```cmd/sh
git clone --recurse-submodules https://github.com/Azure/azure-iot-pcs-remote-monitoring-java.git

# To retrieve the latest submodules, run the following command:

cd azure-iot-pcs-remote-monitoring-java
git submodule foreach git pull origin master
```

> [!NOTE]
> Tyto příkazy stáhnout zdrojový kód pro všechny mikroslužby kromě skripty, které používáte ke spouštění mikroslužby lokálně. I když není nutné zdrojový kód pro spuštění mikroslužby v Dockeru, zdrojový kód je užitečné, pokud budete později chtít upravit akcelerátor řešení a místní test provedených změn.

## <a name="deploy-the-azure-services"></a>Nasazení služby Azure

I když v tomto článku se dozvíte, jak spouštět mikroslužby lokálně, jsou závislé na spouštění v cloudu služby Azure. Pomocí následujícího skriptu pro nasazení služby Azure. Následující příklady skriptu se předpokládá, že používáte úložiště .NET na počítači s Windows. Pokud pracujete s jiným prostředím, cesty, přípony souboru a oddělovače cest odpovídajícím způsobem nastavte.

### <a name="create-new-azure-resources"></a>Vytvářet nové prostředky Azure

Pokud jste dosud vytvořili požadované prostředky Azure, postupujte podle těchto kroků:

1. Ve vašem prostředí příkazového řádku, přejděte **\services\scripts\local\launch** složky v klonovaném kopii úložiště.

1. Spusťte následující příkazy pro instalaci **počítače** rozhraní příkazového řádku nástroje a přihlaste se ke svému účtu Azure:

    ```cmd
    npm install -g iot-solutions
    pcs login
    ```

1. Spustit **start.cmd** skriptu. Skript vyzve k zadání následujících informací:
    * Název řešení.
    * Předplatné Azure, které se má použít.
    * Umístění datového centra Azure používat.

    Tento skript vytvoří skupinu prostředků v Azure s názvem řešení. Tato skupina prostředků obsahuje prostředky Azure, které používá akcelerátor řešení. Jakmile už nepotřebujete odpovídající prostředky můžete odstranit tuto skupinu prostředků.

    Skript také přidá sadu proměnných prostředí s předponou **počítače** do místního počítače. Při spuštění kontejneru Docker místně jejich čtení jejich hodnoty konfigurace z těchto proměnných prostředí.

> [!TIP]
> Po dokončení skriptu, zobrazí se seznam proměnných prostředí. Pokud uložíte tyto hodnoty **služby\\skripty\\místní\\.env** soubor, můžete využít pro nasazení akcelerátoru řešení budoucí. Všimněte si, že všechny proměnné prostředí nastavené v místním počítači přepsat hodnoty v **služby\\skripty\\místní\\.env** souboru při spuštění **docker-compose**.

### <a name="use-existing-azure-resources"></a>Použít existující prostředky Azure

Pokud jste již vytvořili požadované prostředky Azure, vytvořte odpovídající proměnné prostředí v místním počítači. Jste uložili v tyto hodnoty **služby\\skripty\\místní\\.env** souboru jako součást vašeho posledního nasazení. Všimněte si, že proměnné prostředí nastavené v místním počítači přepsat hodnoty v **služby\\skripty\\místní\\.env** souboru při spuštění **docker-compose**.