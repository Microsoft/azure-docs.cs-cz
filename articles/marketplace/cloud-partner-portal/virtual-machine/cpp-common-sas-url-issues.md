---
title: Běžné problémy s adresou URL SAS a opravy pro Azure Marketplace
description: Seznamte se s běžnými problémy s používáním identifikátorů URI sdíleného přístupového podpisu a možných řešení.
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 09/27/2018
ms.author: pabutler
ms.openlocfilehash: 502ba1a65f9b0740a51c7a4da219cc87af494f27
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73813311"
---
# <a name="common-sas-url-issues-and-fixes"></a>Běžné problémy s adresou URL SAS a opravy

V následující tabulce jsou uvedené některé běžné problémy, ke kterým došlo při práci se sdílenými přístupovými podpisy (které slouží k identifikaci a sdílení nahraných virtuálních pevných disků pro vaše řešení) spolu s navrhovanými řešeními.

| **Chybu** | **Zpráva o chybě** | **Opravit** | 
| --------- | ------------------- | ------- | 
| &emsp;*selhání při kopírování imagí* |  |  |
| znak "?" se v adrese URL SAS nenašel. | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Aktualizujte adresu URL SAS pomocí doporučených nástrojů. |
| parametry "St" a "se" v adrese URL SAS nejsou | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Aktualizujte adresu URL SAS pomocí správného **počátečního data** a hodnot **koncového data** . | 
| "SP = RL" není adresa URL SAS | `Failure: Copying Images. Not able to download blob using provided SAS Uri` | Aktualizujte adresu URL SAS pomocí oprávnění nastaveného jako `Read` a `List`. | 
| Adresa URL SAS obsahuje v názvu virtuálního pevného disku prázdné znaky | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Aktualizujte adresu URL SAS tak, aby se odebraly prázdné znaky. |
| Chyba autorizace adresy URL SAS | `Failure: Copying Images. Not able to download blob due to authorization error` | Zkontrolujte a opravte formát identifikátoru URI SAS. V případě potřeby znovu vygenerujte. |
| Parametry adresy URL SAS "St" a "se" neobsahují úplné specifikace data a času. | `Failure: Copying Images. Not able to download blob due to incorrect SAS URL` | `se``st` parametry **počátečního** a **koncového data** adresy URL SAS jsou vyžadovány pro úplný formát data a času, jako je například `11-02-2017T00:00:00Z`. Zkrácené verze jsou neplatné. (Některé příkazy v Azure CLI můžou ve výchozím nastavení generovat zkrácené hodnoty.) | 
|  |  |  |

Další informace najdete v tématu [použití sdílených přístupových podpisů (SAS)](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/).
