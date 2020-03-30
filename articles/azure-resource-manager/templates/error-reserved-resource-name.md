---
title: Chyby názvu rezervovaného prostředku
description: Popisuje, jak vyřešit chyby při poskytování názvu prostředku, který obsahuje vyhrazené slovo.
ms.topic: troubleshooting
ms.date: 11/08/2017
ms.openlocfilehash: e76f4bf9bfee7de6e7523d69acf1388d2dd80e93
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75477626"
---
# <a name="resolve-reserved-resource-name-errors"></a>Vyřešit chyby názvu rezervovaného prostředku

Tento článek popisuje chybu, na kterou narazíte při nasazování prostředku, který obsahuje vyhrazené slovo v jeho názvu.

## <a name="symptom"></a>Příznak

Při nasazování prostředku, který je k dispozici prostřednictvím veřejného koncového bodu, se může zobrazit následující chyba:

```
Code=ReservedResourceName;
Message=The resource name <resource-name> or a part of the name is a trademarked or reserved word.
```

## <a name="cause"></a>Příčina

Prostředky, které mají veřejný koncový bod, nemohou v názvu používat vyhrazená slova nebo ochranné známky.

Jsou vyhrazena tato slova:

* PŘÍSTUP
* Azure
* Bing
* Bizspark
* Biztalk
* Cortana
* Directx
* Dotnet
* Dynamics
* EXCEL
* Exchange
* Forefront
* Groove
* HOLOLENS
* HYPERV
* Kinect
* Lync
* MSDN
* O365
* Office
* OFFICE365
* ONEDRIVE
* Aplikace onenote
* Aplikace outlook
* Powerpoint
* Sharepoint
* Skype
* Aplikace visio
* VISUALSTUDIO

Následující slova nelze použít jako celé slovo nebo podřetězec v názvu:

* Přihlášení
* Microsoft
* Windows
* Xbox

## <a name="solution"></a>Řešení

Zadejte název, který nepoužívá jedno z vyhrazených slov.