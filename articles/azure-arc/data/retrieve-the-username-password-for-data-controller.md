---
title: Načíst uživatelské jméno a heslo pro připojení k řadiči dat ARC
description: Načíst uživatelské jméno a heslo pro připojení k řadiči dat ARC
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 1c33cf21be0b4b1ea39a568d6df9fd90507dd454
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90935949"
---
# <a name="retrieve-the-user-name-and-password-to-connect-to-the-arc-data-controller"></a>Načíst uživatelské jméno a heslo pro připojení k řadiči dat ARC

Může se jednat o situaci, kdy potřebujete načíst uživatelské jméno a heslo pro řadič dat. Toto jsou příkazy, které potřebujete při spuštění. 

```console
azdata login
```

Pokud jste správcem Kubernetes pro daný cluster. Vzhledem k tím, že máte oprávnění ke spouštění příkazů pro načtení z tajného kódu Kubernetes, jsou uloženy informace, na kterých se nachází Azure ARC.

> [!NOTE]
>  Pokud jste použili jiný název oboru názvů, ve kterém byl vytvořen řadič dat, nezapomeňte změnit `-n arc` parametr v následujících příkazech, aby používal název oboru názvů, pro který jste vytvořili řadič dat.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="linux"></a>Linux

Spusťte následující příkaz, který načte uživatelské jméno:

```console
kubectl -n arc get secret controller-login-secret -o=jsonpath="{.data['username']}" | base64 -d
```

Spusťte následující příkaz pro načtení hesla:

```console
kubectl -n arc get secret controller-login-secret -o=jsonpath="{.data['password']}" | base64 -d
```

## <a name="powershell"></a>PowerShell

Spusťte následující příkaz, který načte uživatelské jméno:

```console
[Text.Encoding]::Utf8.GetString([Convert]::FromBase64String((kubectl -n arc get secret controller-login-secret -o=jsonpath="{.data['username']}")))
```

Spusťte následující příkaz pro načtení hesla:

```console
[Text.Encoding]::Utf8.GetString([Convert]::FromBase64String((kubectl -n arc get secret controller-login-secret -o=jsonpath="{.data['password']}")))
```

## <a name="next-steps"></a>Další kroky

Vyzkoušejte jiné [scénáře](https://github.com/microsoft/Azure-data-services-on-Azure-Arc/tree/master/scenarios)
