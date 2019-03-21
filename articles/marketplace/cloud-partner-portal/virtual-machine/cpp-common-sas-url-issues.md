---
title: Běžné problémy adresy URL SAS a opravy pro Azure Marketplace | Dokumentace Microsoftu
description: Seznam běžných problémy z hlediska pomocí sdíleného přístupového podpisu URI a možná řešení.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 09/27/2018
ms.author: pbutlerm
ms.openlocfilehash: cdee17185b7051220f66ede3b9da50a333409e6d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2019
ms.locfileid: "58119259"
---
# <a name="common-sas-url-issues-and-fixes"></a>Běžné problémy adresy URL SAS a opravy

Následující tabulka uvádí některé běžné problémy při práci s sdílených přístupových podpisů (které se používají k identifikaci a sdílené složky nahraný virtuální pevné disky pro vaše řešení), spolu s návrhy řešení.

| **Problém** | **Zpráva o selhání** | **Oprava** | 
| --------- | ------------------- | ------- | 
| &emsp;  *Chyba při kopírování imagí* |  |  |
| "?" nebyl nalezen v adrese URL SAS | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Aktualizace adres URL SAS pomocí doporučené nástroje. |
| parametry "st" a "se" není v adrese URL SAS | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Aktualizace adres URL SAS pomocí správných **datum zahájení** a **koncové datum** hodnoty v ní. | 
| "sp = rl" není v adrese URL SAS | `Failure: Copying Images. Not able to download blob using provided SAS Uri` | Aktualizace adres URL SAS s oprávněními nastavenými jako `Read` a `List`. | 
| Adresa URL SAS obsahuje prázdné znaky v názvu virtuálního pevného disku | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Aktualizace adres URL SAS odebrat mezery. |
| Chyba autorizace adres URL SAS | `Failure: Copying Images. Not able to download blob due to authorization error` | Zkontrolovat a opravit formát identifikátoru URI SAS. Znovu vygenerovat klíč v případě potřeby. |
| Adresa URL SAS "st" a "se" parametry nemají specifikace úplné datum a čas | `Failure: Copying Images. Not able to download blob due to incorrect SAS URL` | Adresa URL SAS **datum zahájení** a **koncové datum** parametry (`st` a` se` podřetězců) musí mít formát úplné datum a čas, jako například `11-02-2017T00:00:00Z`. Zkrácené verze nejsou platné. (Některé příkazy v rozhraní příkazového řádku Azure může generovat zkrácený hodnoty ve výchozím nastavení.) | 
|  |  |  |

Další informace najdete v tématu [použití sdílených přístupových podpisů (SAS)](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/).
