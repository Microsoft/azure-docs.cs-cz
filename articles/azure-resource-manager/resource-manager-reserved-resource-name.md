---
title: Chyby rezervovaného názvu prostředku
description: Popisuje, jak vyřešit chyby při poskytování názvu prostředku, který obsahuje rezervované slovo.
ms.topic: troubleshooting
ms.date: 11/08/2017
ms.openlocfilehash: e76f4bf9bfee7de6e7523d69acf1388d2dd80e93
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150460"
---
# <a name="resolve-reserved-resource-name-errors"></a>Vyřešit chyby názvů rezervovaných prostředků

Tento článek popisuje chybu, se kterou se setkáte při nasazování prostředku, který obsahuje rezervované slovo v názvu.

## <a name="symptom"></a>Příznak

Při nasazování prostředku, který je k dispozici prostřednictvím veřejného koncového bodu, se může zobrazit následující chyba:

```
Code=ReservedResourceName;
Message=The resource name <resource-name> or a part of the name is a trademarked or reserved word.
```

## <a name="cause"></a>Příčina

Prostředky, které mají veřejný koncový bod, nemůžou v názvu používat vyhrazená slova nebo ochranné známky.

Jsou vyhrazena následující slova:

* STOUPIT
* AZURE
* ZJIŠŤUJE
* BIZSPARK
* BIZTALK
* CORTANA
* DIRECTX
* DOTNET
* DYNAMICS
* EXCEL
* VÝMĚN
* Díky
* GROOVE
* HOLOLENS
* HYPERV
* KINECT
* LYNC
* MSDN
* O365
* PODNIKOVÉ
* OFFICE365
* ONEDRIVEM
* APLIKACÍ
* APL
* APLIKACÍCH
* SERVEREM
* SI
* VISIU
* VISUALSTUDIO

Následující slova nelze použít buď jako celé slovo, nebo jako podřetězec v názvu:

* HLAS
* MICROSOFT
* SYSTÉMU
* OVLÁDÁNÍ

## <a name="solution"></a>Řešení

Zadejte název, který nepoužívá jedno z vyhrazených slov.