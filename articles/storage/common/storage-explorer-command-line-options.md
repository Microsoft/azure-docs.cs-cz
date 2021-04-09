---
title: Možnosti příkazového řádku Průzkumník služby Azure Storage | Microsoft Docs
description: Dokumentace k parametrům příkazového řádku pro spuštění Průzkumník služby Azure Storage
services: storage
author: chuye
ms.service: storage
ms.topic: article
ms.date: 02/24/2021
ms.author: chuye
ms.openlocfilehash: 0d588d85852f798542c5d52658e8dae3b9e57949
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101744748"
---
# <a name="azure-storage-explorer-command-line-options"></a>Možnosti příkazového řádku Průzkumník služby Azure Storage

Průzkumník služby Microsoft Azure Storage má sadu možností příkazového řádku, které lze přidat při spuštění aplikace. Většina možností příkazového řádku je pro účely ladění nebo řešení potíží.

## <a name="command-line-options"></a>Možnosti příkazového řádku
Možnost  | Popis
:------- | :-----------
`--debug`/`--prod`  | Spusťte aplikaci v režimu ladění nebo produkčního prostředí. V režimu ladění budou data místních příloh uložená v místním úložišti aplikace a nebudou se šifrovat. Skryté vlastnosti se zobrazí na panelu Vlastnosti pro vybrané uzly prostředků. Úroveň podrobností protokolu bude nastavena pro tisk zpráv ladění, které odhalí interní logiku nastavení Průzkumník služby Storage. Výchozí hodnota je `--prod`.
`--lang`  | Spustí aplikaci s daným jazykem. Například, `--lang="zh-Hans"`.
`--disable-gpu` | Spusťte aplikaci bez akcelerace GPU.
`--auto-open-dev-tools` | Nechte aplikaci otevřít okno vývojářské nástroje hned po zobrazení okna prohlížeče. Tato možnost je užitečná, když chcete přejít na bod přerušení na řádku v úvodním kódu okna prohlížeče.
`--verbosity` | Nastavte úroveň podrobností protokolování Průzkumník služby Storage. Podporované úrovně podrobností zahrnují `debug` , `verbose` ,, `info` , `warn` a `error` `silent` . Například, `--verbosity=verbose`. Při spuštění v produkčním režimu je výchozí úroveň podrobností `info` . Při spuštění v režimu ladění bude úroveň podrobností protokolu vždy `debug` .
`--log-dir` | Nastavte adresář pro ukládání souborů protokolu. Například, `--log-dir=path_to_a_directory`.

Příklad spuštění Průzkumník služby Storage s vlastními možnostmi příkazového řádku

```shell
./MicrosoftAzureStorageExplorer --lang=en --auto-open-dev-tools
```

> [!NOTE]
> Tyto možnosti příkazového řádku se můžou v nových verzích Průzkumník služby Storage změnit.

## <a name="when-to-use-command-line-options"></a>Kdy použít možnosti příkazového řádku

Některé možnosti příkazového řádku je možné použít k přizpůsobení Průzkumník služby Storage. Pro tyto možnosti, které mají odpovídající nastavení uživatele, například `--lang` . Místo použití možnosti příkazového řádku doporučujeme použít uživatelská nastavení. 

Další možnosti příkazového řádku mohou být užitečné při ladění a odstraňování potíží. Pokud narazíte na problém v Průzkumník služby Storage, může vám řešení problému v režimu ladění nám pomáhat získat podrobnější informace, které je třeba prozkoumat.