---
title: Běžné otázky týkající se sítě Azure Service Fabric | Dokumentace Microsoftu
description: Další informace o nejčastější dotazy a odpovědi pro Azure Service Fabric mřížky.
services: service-fabric-mesh
keywords: ''
author: chackdan
ms.author: chackdan
ms.date: 06/25/2018
ms.topic: troubleshooting
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: d0ae7fbb22f6d98662f83968158182d447a75394
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/03/2018
ms.locfileid: "39501963"
---
# <a name="commonly-asked-service-fabric-mesh-questions"></a>Nejčastější dotazy služby prostředků infrastruktury sítě
Azure Service Fabric Mesh je plně spravovaná služba, která vývojářům umožňuje nasazovat aplikace zajišťující mikroslužby, aniž by museli spravovat virtuální počítače, úložiště nebo sítě. Tento článek obsahuje odpovědi na nejčastější dotazy.

## <a name="how-do-i-report-an-issue-or-ask-a-question"></a>Jak ohlásit problém nebo dotaz?

Ptejte se, získejte odpovědi od odborníků Microsoftu a hlášení problémů v [úložiště GitHub service-fabric sítě preview](https://aka.ms/sfmeshissues).

## <a name="quota-and-cost"></a>Kvóty a nákladů

**Kolik stojí účast ve verzi preview?**

Neúčtují žádné poplatky pro nasazování aplikací nebo kontejnerů na síť ve verzi preview. Ale doporučujeme odstranit prostředky, nasadit a nechávat je spuštěné, pokud aktivně testování.

**Existuje nějaké omezení kvóty počtu jader a paměti RAM?**

Ano, jsou kvóty pro každé předplatné:

- Počet aplikací – 5 
- Počet jader na aplikaci – 12 
- Celkové paměti RAM na aplikaci – 48 GB 
- Počet sítí a příchozího přenosu dat koncové body – 5  
- Počet Azure svazků, které můžete připojit - 10 
- Počet replik služby – 3 
- Největší kontejner, který můžete nasadit je omezena na 4 jádra, 16GB paměti RAM.
- Kontejnery v přírůstcích po 0,5 jádra, až do maximálního počtu jader 6, kterou můžete přidělit částečné jader.

**Můžete nechat aplikaci přes noc spuštění?**

Ano, je to možné, ale budete ukončena. doporučujeme odstranit prostředky, nasadit a nechávat je spuštěné, dokud ho aktivně testování. Tyto zásady mohou v budoucnu změnit a prostředky se odstraní jejich zneužití.

## <a name="supported-container-os-images"></a>Image kontejnerů podporovaný operační systém
Následující Image kontejneru operačních systémů lze použít při nasazování služby.

- Windows – windowsservercore a nanoserver
    - Windows Server 2016
    - Windows Server verze 1709
- Linux
    - Žádné známé omezení

## <a name="features-gaps-and-known-issues"></a>Funkce mezer a známé problémy

**Po nasazení aplikace, síťový prostředek s ním spojená zřejmě není IP adresa**

Existuje známý problém ještě dnes s IP adresou, chystá se po prodlevě. Zkontrolujte stav síťový prostředek za pár minut zobrazíte přidruženou IP adresu.

**Moje aplikace selhává pro přístup k prostředku správné síti nebo svazku**

Ve vašem aplikačním modelu budete muset použít úplné ID prostředku pro sítě a svazků bude mít přístup k přidružený prostředek. Zde je, jak to vypadá v ukázce rychlý start:

```json
"networkRefs": [
    {
    "name":  "[resourceId('Microsoft.ServiceFabric/networks', 'SbzVotingNetwork')]" 
    }
]
```

**Nevidím aktuální model aplikace podporující způsob, jak šifrování Moje tajné kódy**

Ano, šifrování tajných kódů není podporován v aktuální verzi Private Preview. 

**DNS nebude fungovat stejným způsobem jako v mé vývojový cluster Service Fabric a sítě**

Existuje známý problém, kde bude pravděpodobně nutné odkazovat odlišně v místního vývojového clusteru a sítě Azure. Ve vašem místním vývojovém clusteru pomocí {služby serviceName}. aplikace {applicationName}. V Azure Service Fabric sítě pomocí {služby servicename}. Síť Azure aktuálně nepodporuje překlad názvů dns v aplikacích.

Pro další známých problémů s DNS se spouštěním vývojový cluster Service Fabric ve Windows 10, najdete tady: [kontejnery Windows ladění](/azure/service-fabric/service-fabric-how-to-debug-windows-containers).

**Při použití rozhraní příkazového řádku modulu, ImportError zobrazí tato chyba: nelze importovat název sdk_no_wait**

Pokud používáte starší verzi rozhraní příkazového řádku, než 2.0.30, zobrazí tato chyba-

```
cannot import name 'sdk_no_wait'
Traceback (most recent call last):
File "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\knack\cli.py", line 193, in invoke cmd_result = self.invocation.execute(args)
File "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\azure\cli\core\commands_init_.py", line 241, in execute self.commands_loader.load_arguments(command)
File "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\azure\cli\core_init_.py", line 201, in load_arguments self.command_table[command].load_arguments() # this loads the arguments via reflection
File "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\azure\cli\core\commands_init_.py", line 142, in load_arguments super(AzCliCommand, self).load_arguments()
File "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\knack\commands.py", line 76, in load_arguments cmd_args = self.arguments_loader()
File "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\azure\cli\core_init_.py", line 332, in default_arguments_loader op = handler or self.get_op_handler(operation)
File "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\azure\cli\core_init_.py", line 375, in get_op_handler op = import_module(mod_to_import)
File "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\importlib_init_.py", line 126, in import_module return _bootstrap._gcd_import(name[level:], package, level)
File "", line 978, in _gcd_import
File "", line 961, in _find_and_load
File "", line 950, in _find_and_load_unlocked
File "", line 655, in _load_unlocked
File "", line 678, in exec_module
File "", line 205, in _call_with_frames_removed
File "C:\Users\annayak.azure\cliextensions\azure-cli-sbz\azext_sbz\custom.py", line 18, in 
from azure.cli.core.util import get_file_json, shell_safe_json_parse, sdk_no_wait
ImportError: cannot import name 'sdk_no_wait'.
```

**Při instalaci balíčku rozšíření rozhraní příkazového řádku se zobrazí název neshoda distribuce**

To neznamená, že nebyl nainstalován rozšíření. Byste měli stále moct používat příkazy rozhraní příkazového řádku bez problémů.

**Když mám horizontální navýšení kapacity, vidím, že jsou ovlivněny Moje kontejnerů, včetně Moje spuštěné ty**

Jedná se chybu, by měl vyřešen v příští aktualizace modulu runtime.

## <a name="next-steps"></a>Další postup

Další informace o Service Fabric mřížky, [přehled](service-fabric-mesh-overview.md).
