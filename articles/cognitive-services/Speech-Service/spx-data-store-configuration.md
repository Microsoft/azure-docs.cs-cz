---
title: Speech CLI – možnosti konfigurace – služba pro rozpoznávání řeči
titleSuffix: Azure Cognitive Services
description: Naučte se vytvářet a spravovat konfigurační soubory pro použití s rozhraním příkazového řádku Azure Speech.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 01/13/2021
ms.author: erhopf
ms.openlocfilehash: aa8551e49c8ef16984783c4e9735c987174b180a
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2021
ms.locfileid: "98540055"
---
# <a name="speech-cli-configuration-options"></a>Možnosti konfigurace funkce Speech CLI

Chování rozhraní příkazového řádku pro rozpoznávání řeči se může spolehnout na nastavení v konfiguračních souborech, na které můžete odkazovat pomocí `@` symbolu. Rozpoznávání řeči v rozhraní příkazového řádku uloží nové nastavení v novém `./spx/data` podadresáři, který je vytvořen v aktuálním pracovním adresáři pro rozhraní příkazového řádku pro rozpoznávání řeči. Při hledání hodnoty konfigurace vyhledá rozhraní příkazového řádku aktuální pracovní adresář, potom v úložišti dat na `./spx/data` a v dalších datastorech, včetně konečného úložiště dat jen pro čtení v `spx` binárním souboru. 

V rychlém startu pro rozpoznávání řeči jste úložiště dat použili k uložení `@key` hodnot a `@region` , takže je nemusíte zadávat pomocí jednotlivých `spx` příkazů. Mějte na paměti, že k ukládání vlastních nastavení konfigurace můžete použít konfigurační soubory, nebo je dokonce použít k předávání adres URL nebo jiného dynamického obsahu vygenerovaného za běhu.

## <a name="create-and-manage-configuration-files-in-the-datastore"></a>Vytváření a správa konfiguračních souborů v úložišti dat

V této části se dozvíte, jak použít konfigurační soubor v místním úložišti úložiště k uložení a načtení nastavení příkazů pomocí `spx config` a jak uložit výstup z rozhraní příkazového řádku pro rozpoznávání řeči pomocí `--output` Možnosti.

Následující příklad vymaže `@my.defaults` konfigurační soubor, přidá páry klíč-hodnota pro **klíč** a **oblast** v souboru a použije konfiguraci při volání `spx recognize` .

```console
spx config @my.defaults --clear
spx config @my.defaults --add key 000072626F6E20697320636F6F6C0000
spx config @my.defaults --add region westus

spx config @my.defaults

spx recognize --nodefaults @my.defaults --file hello.wav
```

Můžete také zapisovat dynamický obsah do konfiguračního souboru. Například následující příkaz vytvoří vlastní model řeči a uloží adresu URL nového modelu do konfiguračního souboru. Následující příkaz počká, dokud model na této adrese URL není připravený k použití, než se vrátí.

```console
spx csr model create --name "Example 4" --datasets @my.datasets.txt --output url @my.model.txt
spx csr model status --model @my.model.txt --wait
```

Následující příklad zapíše dvě adresy URL do `@my.datasets.txt` konfiguračního souboru. V tomto scénáři `--output` může zahrnovat volitelné klíčové slovo **Add** pro vytvoření konfiguračního souboru nebo připojení k existujícímu.


```console
spx csr dataset create --name "LM" --kind Language --content https://crbn.us/data.txt --output url @my.datasets.txt
spx csr dataset create --name "AM" --kind Acoustic --content https://crbn.us/audio.zip --output add url @my.datasets.txt

spx config @my.datasets.txt
```

Další podrobnosti o souborech úložiště dat, včetně použití výchozích konfiguračních souborů ( `@spx.default` , `@default.config` a `@*.default.config` pro výchozí nastavení příkazu), získáte, když zadáte tento příkaz:

```console
spx help advanced setup
```

## <a name="next-steps"></a>Další kroky 

* [Operace dávkového zpracování pomocí funkce CLI](./spx-batch-operations.md)