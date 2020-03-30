---
title: Běžné problémy s adresou URL a opravy SAS pro Azure Marketplace
description: Seznam běžných problémů týkajících se používání identifikátorů URI se sdíleným podpisem a možných řešení.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/27/2018
ms.author: dsindona
ms.openlocfilehash: 47702959474a352a8e13710ec850f789dee4d517
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80278157"
---
# <a name="common-sas-url-issues-and-fixes"></a>Běžné problémy a opravy adres URL s pomocí SAS

V následující tabulce jsou uvedeny některé běžné problémy, ke kterým došlo při práci se sdílenými přístupovými podpisy (které slouží k identifikaci a sdílení nahraných virtuálních discích pro vaše řešení) spolu s navrhovanými řešeními.

| **Problém** | **Zpráva o selhání** | **Oprava** | 
| --------- | ------------------- | ------- | 
| &emsp;  *Selhání kopírování obrázků* |  |  |
| "?" není nalezen v adrese URL SAS | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Aktualizujte adresu URL SAS pomocí doporučených nástrojů. |
| parametry "st" a "se" nejsou v adrese URL SAS | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Aktualizujte adresu URL SAS pomocí správných hodnot **Počáteční datum** a **Koncové datum.** | 
| "sp=rl" není v adrese URL SAS | `Failure: Copying Images. Not able to download blob using provided SAS Uri` | Aktualizujte adresu URL SAS `Read` `List`s oprávněními nastavenými jako a . | 
| Adresa URL SAS má mezery v názvu virtuálního pevného disku | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Aktualizujte adresu URL SAS a odeberte mezery. |
| Chyba autorizace adresy URL sas | `Failure: Copying Images. Not able to download blob due to authorization error` | Zkontrolujte a opravte formát identifikátoru URI SAS. V případě potřeby regenerovat. |
| Parametry adresy URL SAS "st" a "se" nemají úplnou specifikaci data a času | `Failure: Copying Images. Not able to download blob due to incorrect SAS URL` | Parametry **Počáteční datum** a **Koncové** `st` datum `se` adresy SAS ( a podřetězce) musí `11-02-2017T00:00:00Z`mít úplný formát datatime, například . Zkrácené verze nejsou platné. (Některé příkazy v azure cli může generovat zkrácené hodnoty ve výchozím nastavení.) | 
|  |  |  |

Další informace naleznete [v tématu Použití sdílených přístupových podpisů (SAS).](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)
