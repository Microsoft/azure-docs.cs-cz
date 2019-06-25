---
title: Vyhrazené chyby názvů prostředků Azure | Dokumentace Microsoftu
description: Popisuje, jak řešit chyby při poskytnutí názvu prostředku, který obsahuje vyhrazené slovo.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/08/2017
ms.author: tomfitz
ms.openlocfilehash: 922389b7c6c1bb7ad1d9b8f6ec35ccc1c5656723
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "64683937"
---
# <a name="resolve-reserved-resource-name-errors"></a>Řešení chyb název vyhrazený prostředek

Tento článek popisuje, chyby, které zaznamenáte při nasazování prostředek, který obsahuje rezervovaná slova v názvu.

## <a name="symptom"></a>Příznak

Při nasazování prostředek, který je dostupný prostřednictvím veřejného koncového bodu, zobrazí se následující chybová zpráva:

```
Code=ReservedResourceName;
Message=The resource name <resource-name> or a part of the name is a trademarked or reserved word.
```

## <a name="cause"></a>Příčina

Prostředky, které mají veřejný koncový bod nemůže používat vyhrazená slova nebo ochranné známky v názvu.

Tato slova jsou vyhrazené:

* PŘÍSTUP
* AZURE
* BING
* BIZSPARK
* BIZTALK
* CORTANA
* DIRECTX
* DOTNET
* DYNAMICS
* EXCEL
* EXCHANGE
* FOREFRONT
* GROOVE
* HOLOLENS
* HYPERV
* SADA KINECT
* LYNC
* MSDN
* O365
* OFFICE
* OFFICE365
* ONEDRIVE
* APLIKACE ONENOTE
* APLIKACE OUTLOOK
* APLIKACE POWERPOINT
* SHAREPOINT
* SKYPE
* VISIO
* VISUAL STUDIO

Tato slova nelze použít jako celá slova nebo podřetězec v názvu:

* PŘIHLÁŠENÍ
* MICROSOFT
* SYSTÉM WINDOWS
* XBOX

## <a name="solution"></a>Řešení

Zadejte název, který nepoužívá jedno z vyhrazených slov.