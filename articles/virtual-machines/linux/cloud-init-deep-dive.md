---
title: Principy Cloud-init
description: Hlubokou podrobně pro porozumění zřizování virtuálního počítače Azure pomocí Cloud-init.
author: danielsollondon
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: conceptual
ms.date: 07/06/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: 94dd57310375febb4bc9a55efa704a5fbf4e80e8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102559439"
---
# <a name="diving-deeper-into-cloud-init"></a>Začnete hlouběji do cloudu – init
Pokud se chcete dozvědět víc o [Cloud-init](https://cloudinit.readthedocs.io/en/latest/index.html) nebo je vyřešit na hlubší úrovni, potřebujete pochopit, jak to funguje. Tento dokument popisuje důležité části a vysvětluje konkrétní informace o Azure.

Když je Cloud-init součástí generalizované image a z této image se vytvoří virtuální počítač, zpracuje se konfigurace a spustí se během počátečního spuštění během 5 fází. Tyto fáze se zobrazí tak, jak vám ukáže, v jakém bodě Cloud-init budou aplikovány konfigurace. 


## <a name="understand-cloud-init-configuration"></a>Principy konfigurace Cloud-Init
Pokud konfigurujete virtuální počítač, který se má spustit na platformě, znamená to, že Cloud-init potřebuje použít více konfigurací, jako je například příjemce obrazu, hlavní konfigurace, které budete používat, jsou `User data` (CustomData), který podporuje více formátů, jsou zde popsány [zde](https://cloudinit.readthedocs.io/en/latest/topics/format.html#user-data-formats). Máte také možnost Přidat a spustit skripty (/var/lib/Cloud/Scripts) pro další konfiguraci. níže se tento postup zabývá podrobněji.

Některé konfigurace se už vloženými do Azure Marketplace imagí, které jsou součástí nástroje Cloud-init, například:

1. **Cloudový zdroj dat** – Cloud-init obsahuje kód, který může komunikovat s Cloud platformami, se nazývají zdroje dat. Když se virtuální počítač vytvoří z image Cloud-init v [Azure](https://cloudinit.readthedocs.io/en/latest/topics/datasources/azure.html#azure), Cloud-init načte zdroj dat Azure, který bude komunikovat s koncovými body Azure metadata a získá konfiguraci specifickou pro virtuální počítače.
2. **Konfigurace modulu runtime** (/Run/Cloud-init)
3. **Image config** (/etc/Cloud), jako `/etc/cloud/cloud.cfg` je například `/etc/cloud/cloud.cfg.d/*.cfg` . Příklad toho, kde se používá v Azure, je běžné, že image operačního systému Linux s Cloud-init mají mít direktivu zdroje dat Azure, která oznamuje Cloud-init, který by měl používat, tím šetří čas Cloud-Init:

   ```bash
   /etc/cloud/cloud.cfg.d# cat 90_dpkg.cfg
   # to update this file, run dpkg-reconfigure cloud-init
   datasource_list: [ Azure ]
   ```


## <a name="cloud-init-boot-stages-processing-configuration"></a>Fáze spouštění Cloud-init (konfigurace zpracování)

Při zřizování pomocí Cloud-init je k dispozici 5 fází spouštění, konfigurace procesu a zobrazení v protokolech.

1. [Fáze generátoru](https://cloudinit.readthedocs.io/en/latest/topics/boot.html#generator): spustí se generátor Cloud-init System a určí, že by se měl zahrnout Cloud-init do spouštěcích cílů, a pokud ano, povolí Cloud-init. 

2. [Místní fáze Cloud-init](https://cloudinit.readthedocs.io/en/latest/topics/boot.html#local): Cloud-init vyhledá místní zdroj dat "Azure", který umožní použití Cloud-init pro rozhraní s Azure a použije konfiguraci sítě, včetně Fallback.

3. [Fáze inicializace pro Cloud-init (síť)](https://cloudinit.readthedocs.io/en/latest/topics/boot.html#network): síť by měla být online a měla by se vygenerovat informace o síťové kartě a tabulce směrování. V této fázi se spustí moduly uvedené v `cloud_init_modules` v/etc/Cloud/Cloud.cfg. Virtuální počítač v Azure se připojí, dočasný disk se naformátuje, název hostitele se nastaví společně s dalšími úkoly.

   Tady jsou některé z těchto `cloud_init_modules` :
   
   ```bash
   - migrator
   - seed_random
   - bootcmd
   - write-files
   - growpart
   - resizefs
   - disk_setup
   - mounts
   - set_hostname
   - update_hostname
   - ssh
   ```
   
   Po této fázi se Cloud-init pošle na platformu Azure, že se virtuální počítač úspěšně zřídil. Některé moduly se možná nezdařily, a ne všechny chyby modulu způsobí selhání zřizování.

4. [Fáze konfigurace Cloud-init](https://cloudinit.readthedocs.io/en/latest/topics/boot.html#config): v této fázi `cloud_config_modules` se spustí moduly definované a uvedené v/etc/Cloud/Cloud.cfg.


5. [Finální fáze Cloud-init](https://cloudinit.readthedocs.io/en/latest/topics/boot.html#final): v této konečné fázi se spustí moduly v `cloud_final_modules` , které jsou uvedené v/etc/Cloud/Cloud.cfg. Moduly, které je potřeba spustit pozdě v procesu spouštění, jako je instalace balíčků a spouštění skriptů atd. 

   -   Během této fáze můžete spouštět skripty jejich umístěním do adresářů v části `/var/lib/cloud/scripts` :
   - `per-boot` -skripty v tomto adresáři se spouštějí při každém restartování.
   - `per-instance` -skripty v tomto adresáři se spouštějí při prvním spuštění nové instance.
   - `per-once` -skripty v tomto adresáři se spouští jenom jednou.

## <a name="next-steps"></a>Další kroky

[Řešení potíží s cloudem a inicializací](cloud-init-troubleshooting.md)
