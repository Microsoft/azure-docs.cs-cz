---
title: Běžné problémy s adresou URL SAS a opravy pro Azure Marketplace
description: Seznamte se s běžnými problémy s používáním identifikátorů URI sdíleného přístupového podpisu a možných řešení.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/27/2018
ms.author: dsindona
ms.openlocfilehash: 723762695d34380b7f237fa9082dc470dafcc8df
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "82147051"
---
# <a name="common-sas-url-issues-and-fixes"></a>Běžné problémy s adresou URL SAS a opravy

> [!IMPORTANT]
> Od 13. dubna 2020 začneme přesouvat správu nabídek virtuálních počítačů Azure do partnerského centra. Po dokončení migrace vytvoříte a budete spravovat své nabídky v partnerském centru. Pokud chcete spravovat migrované nabídky, postupujte podle pokynů v tématu [běžné problémy s adresou URL SAS a opravy](https://docs.microsoft.com/azure/marketplace/partner-center-portal/common-sas-uri-issues) .

V následující tabulce jsou uvedené některé běžné problémy, ke kterým došlo při práci se sdílenými přístupovými podpisy (které slouží k identifikaci a sdílení nahraných virtuálních pevných disků pro vaše řešení) spolu s navrhovanými řešeními.

| **Chybu** | **Zpráva o chybě** | **Oprava** | 
| --------- | ------------------- | ------- | 
| &emsp;  *Selhání při kopírování imagí* |  |  |
| znak "?" se v adrese URL SAS nenašel. | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Aktualizujte adresu URL SAS pomocí doporučených nástrojů. |
| parametry "St" a "se" v adrese URL SAS nejsou | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Aktualizujte adresu URL SAS pomocí správného **počátečního data** a hodnot **koncového data** . | 
| "SP = RL" není adresa URL SAS | `Failure: Copying Images. Not able to download blob using provided SAS Uri` | Aktualizujte adresu URL SAS pomocí nastavení oprávnění `Read` nastaveného jako a `List`. | 
| Adresa URL SAS obsahuje v názvu virtuálního pevného disku prázdné znaky | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Aktualizujte adresu URL SAS tak, aby se odebraly prázdné znaky. |
| Chyba autorizace adresy URL SAS | `Failure: Copying Images. Not able to download blob due to authorization error` | Zkontrolujte a opravte formát identifikátoru URI SAS. V případě potřeby znovu vygenerujte. |
| Parametry adresy URL SAS "St" a "se" neobsahují úplné specifikace data a času. | `Failure: Copying Images. Not able to download blob due to incorrect SAS URL` | `st` Parametry `se` `11-02-2017T00:00:00Z` **počátečního** a **koncového data** adresy URL SAS jsou požadovány pro úplný formát data a času, například. Zkrácené verze jsou neplatné. (Některé příkazy v Azure CLI můžou ve výchozím nastavení generovat zkrácené hodnoty.) | 
|  |  |  |

Další informace najdete v tématu [použití sdílených přístupových podpisů (SAS)](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/).
