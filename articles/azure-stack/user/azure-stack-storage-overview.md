---
title: Úvod do Azure zásobník úložiště
description: Další informace o Azure zásobník úložiště
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.assetid: 092aba28-04bc-44c0-90e1-e79d82f4ff42
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/21/2018
ms.author: mabrigg
ms.openlocfilehash: d97a5f8aff57f4bbfd7d5222a87d258fa5c92da8
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34604382"
---
# <a name="introduction-to-azure-stack-storage"></a>Úvod do Azure zásobník úložiště

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

## <a name="overview"></a>Přehled

Azure zásobníku úložiště je sada služby cloudového úložiště, které obsahuje objekty BLOB, tabulek a front, které jsou konzistentní s služby Azure Storage.

## <a name="azure-stack-storage-services"></a>Služba Azure zásobník úložiště

Azure zásobníku úložiště poskytuje následující tři služby:

- **Blob Storage**

    BLOB storage ukládá nestrukturované datové objekty. Objekt blob může být jakýkoli druh textu nebo binárních dat, jako je dokument, soubor médií nebo instalátor aplikace.

- **Úložiště Table**

    Table storage ukládá strukturované datové sady. Table Storage je datové úložiště na bázi NoSQL typu klíč-atribut, které umožňuje rychlý vývoj a přístup k velkým objemům dat.

- **Queue Storage**

    Queue storage poskytuje spolehlivé zasílání zpráv pro zpracování pracovního postupu a pro komunikaci mezi součástmi cloudových služeb.

Účet úložiště Azure zásobníku je zabezpečený účet, který poskytuje přístup ke službám v zásobníku úložiště Azure. Váš účet úložiště poskytuje jedinečný obor názvů pro vaše prostředky úložiště. Následující diagram znázorňuje vztahy mezi prostředky Azure zásobníku úložiště v účtu úložiště:

![Přehled služby Azure zásobník úložiště](media/azure-stack-storage-overview/AzureStackStorageOverview.png)

### <a name="blob-storage"></a>Blob Storage

Uživatelé s velkým množstvím nestrukturovaných dat ukládat v cloudu úložiště blob nabízí efektivní a škálovatelné řešení. Úložiště objektů blob můžete použít k ukládání obsahu, jako například:

- Dokumenty
- Sociální data, jako jsou fotografie, videa, hudba nebo blogy
- Zálohy souborů, počítačů, databází a zařízení
- Obrázky a text pro webové aplikace
- Konfigurační dat pro cloudové aplikace
- Velké objemy dat, jako jsou protokoly a další velké datové sady

Každý objekt blob se organizuje do kontejneru. Kontejnery také nabízejí praktický způsob přiřazení zásad zabezpečení skupinám objektů. Účet úložiště může obsahovat libovolný počet kontejnerů a kontejner může obsahovat libovolný počet objektů BLOB až do limitu účtu úložiště.

Úložiště BLOB nabízí tři typy objektů blob:

- **Objekty BLOB bloku**

    Objekty BLOB bloku jsou optimalizované pro streamování a ukládání cloudových objektů a jsou dobrou volbou pro ukládání dokumentů, souborů médií, záloh a atd.

- **Doplňovací objekty BLOB**

    Doplňovací objekty blob jsou podobné objektům blob bloku, ale jsou optimalizované pro doplňovací operace. Doplňovací objekt blob se může aktualizovat jen přidáním nového bloku na konec. Doplňovací objekty blob jsou dobrou volbou pro takové scénáře, jako je například protokolování, kde se nová data potřebují zapisovat jen na konec objektu blob.

- **Objekty BLOB stránky**

    Objekty BLOB stránky jsou optimalizované pro zastoupení disků IaaS a podporují náhodné zápisy, který je velikost až 1 TB. Virtuální počítač Azure zásobníku připojen IaaS disk je virtuální pevný disk uložený jako objekt blob stránky.

### <a name="table-storage"></a>Úložiště Table

Moderní aplikace často potřebují datová úložiště s větší škálovatelností a flexibilitou, než potřebovaly starší generace softwaru. Úložiště Table nabízí vysoce dostupné, enormně škálovatelné úložiště, se kterým se vaše aplikace může automaticky škálovat podle požadavků uživatelů. Úložiště Table je úložiště klíčů/atributů NoSQL společnosti Microsoft – má návrhem, takže je odlišný od tradičních relačních databází. S datovým úložištěm bez schématu je snadné data přizpůsobovat měnícím se potřebám vaší aplikace. Úložiště Table se snadno používá, takže vývojáři můžou aplikace vytvářet rychle.

Úložiště Table je úložiště klíč atribut, což znamená, že každých hodnota v tabulce je uložená se typovým názvem vlastnosti. název vlastnosti se může použít pro filtrování a upřesnění kritérií výběru. Kolekce vlastností a jejich hodnot tvoří entitu. Vzhledem k tomu, že úložiště table nemá schéma, dvě entity ve stejné tabulce můžou obsahovat různé kolekce vlastností a tyto vlastnosti můžou být různých typů.

Úložiště tabulek můžete použít k ukládání flexibilních datových sad, například uživatelských dat pro webové aplikace, adresáře, informace o zařízení a jiný typ metadat, které vaše služba vyžaduje. Pro dnešní aplikace založené na Internetu databáze NoSQL, jako je úložiště table nabízí oblíbenou alternativu tradičních relačních databází.

Účet úložiště může obsahovat libovolný počet tabulek a tabulka může obsahovat libovolný počet entit, až do limitu kapacity účtu úložiště.

### <a name="queue-storage"></a>Queue Storage

Při navrhování aplikací pro škálování ve větším měřítku jsou jednotlivé součásti aplikací často nepropojené, aby je bylo možné škálovat nezávisle. Queue storage poskytuje spolehlivé řešení zasílání zpráv pro asynchronní komunikaci mezi součástmi aplikací, zda používáte systém v cloudu, na ploše, na místním serveru nebo na mobilním zařízení. Queue Storage také podporuje správu asynchronních úloh a pracovní postupy procesů sestavování buildů.

Účet úložiště může obsahovat libovolný počet front a fronty může obsahovat libovolný počet zpráv až do limitu kapacity účtu úložiště. Jednotlivé zprávy můžou mít velikost až 64 KB.

## <a name="next-steps"></a>Další postup

- [Konzistentní s Azure storage: rozdíly a důležité informace](azure-stack-acs-differences.md)

- Další informace o službě Azure Storage najdete v tématu [Úvod do Microsoft Azure Storage](../../storage/common/storage-introduction.md)
