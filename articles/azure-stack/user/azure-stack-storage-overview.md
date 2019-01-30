---
title: Úvod do úložiště Azure Stack
description: Další informace o ukládání Azure Stack
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
ms.date: 01/14/2019
ms.author: mabrigg
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: d1baeb5ff32fcadaeca25244ce3167fe3fe4477a
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55249710"
---
# <a name="introduction-to-azure-stack-storage"></a>Úvod do úložiště Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

## <a name="overview"></a>Přehled

Úložiště Azure Stack je sada služeb cloudového úložiště, který obsahuje objekty BLOB, tabulek a front, které jsou konzistentní vzhledem k aplikacím pomocí služby Azure Storage.

## <a name="azure-stack-storage-services"></a>Služby Azure Storage zásobníku

Azure Stack úložiště poskytuje následující tři služby:

- **Blob Storage**

    BLOB storage ukládá nestrukturované datové objekty. Objekt blob může být jakýkoli druh textu nebo binárních dat, jako je dokument, soubor médií nebo instalátor aplikace.

- **Table Storage**

    Table storage ukládá strukturované datové sady. Table Storage je datové úložiště na bázi NoSQL typu klíč-atribut, které umožňuje rychlý vývoj a přístup k velkým objemům dat.

- **Queue Storage**

    Queue storage poskytuje spolehlivé zasílání zpráv pro zpracování úloh a komunikaci mezi komponentami cloudových služeb.

Účet úložiště Azure Stack je zabezpečený účet, který poskytuje přístup ke službám ve službě Azure Stack Storage. Váš účet úložiště poskytuje jedinečný obor názvů pro vaše prostředky úložiště. Následující diagram znázorňuje vztahy mezi prostředky úložiště služby Azure Stack v účtu úložiště:

![Přehled služby Azure Stack úložiště](media/azure-stack-storage-overview/AzureStackStorageOverview.png)

### <a name="blob-storage"></a>Blob Storage

Pro uživatele s velkým množstvím nestrukturovaných dat objektů do úložiště v cloudu blob storage nabízí efektivní a škálovatelné řešení. Úložiště objektů blob můžete použít k uložení obsahu, jako například:

- Dokumenty
- Sociální data, jako jsou fotografie, videa, hudba nebo blogy
- Zálohy souborů, počítačů, databází a zařízení
- Obrázky a text pro webové aplikace
- Konfigurační dat pro cloudové aplikace
- Velké objemy dat, jako jsou protokoly a další velké datové sady

Každý objekt blob se organizuje do kontejneru. Kontejnery také nabízejí praktický způsob přiřazení zásad zabezpečení skupinám objektů. Účet úložiště může obsahovat libovolný počet kontejnerů a kontejner může obsahovat libovolný počet objektů BLOB až do limitu účtu úložiště.

BLOB storage nabízí tři typy objektů blob:

- **Objekty BLOB bloku**

    Objekty BLOB bloku jsou optimalizované pro streamování a ukládání cloudových objektů a jsou dobrou volbou pro ukládání dokumentů, souborů médií, záloh a atd.

- **Doplňovací objekty BLOB**

    Doplňovací objekty blob jsou podobné objektům blob bloku, ale jsou optimalizované pro doplňovací operace. Doplňovací objekt blob se může aktualizovat jen přidáním nového bloku na konec. Doplňovací objekty blob jsou dobrou volbou pro takové scénáře, jako je například protokolování, kde se nová data potřebují zapisovat jen na konec objektu blob.

- **Objekty BLOB stránky**

    Objekty BLOB stránky jsou optimalizované pro zastoupení disků IaaS a podporují náhodné zápisy, který je velikost až 1 TB. Virtuální pevný disk uložený jako objekt blob stránky je disk IaaS připojení přes virtuální stroj služby Azure Stack.

### <a name="table-storage"></a>Úložiště Table

Moderní aplikace často potřebují datová úložiště s větší škálovatelností a flexibilitou, než potřebovaly starší generace softwaru. Úložiště Table nabízí vysoce dostupné, enormně škálovatelné úložiště, se kterým se vaše aplikace může automaticky škálovat podle požadavků uživatelů. Table storage je od Microsoftu NoSQL s dvojicí klíč/atribut – nemá návrhem, rozdíl od tradičních relačních databází. S datovým úložištěm bez schématu je snadné data přizpůsobovat měnícím se potřebám vaší aplikace. Úložiště Table se snadno používá, takže vývojáři můžou aplikace vytvářet rychle.

Úložiště Table je úložiště klíč atribut, což znamená, že každá hodnota v tabulce je uložená se typovým názvem vlastnosti. název vlastnosti se může použít pro filtrování a upřesnění kritérií výběru. Kolekce vlastností a jejich hodnot tvoří entitu. Protože úložiště table je bez schématu, dvě entity ve stejné tabulce můžou obsahovat různé kolekce vlastností a tyto vlastnosti můžou být různých typů.

Úložiště tabulek můžete použít k ukládání flexibilních datových sad, například uživatelských dat pro webové aplikace, adresářů, informací o zařízení a jiný typ metadat, které vaše služba vyžaduje. Pro dnešní aplikace založené na Internetu databáze NoSQL, jako je úložiště table nabízí oblíbenou alternativu tradičních relačních databází.

Účet úložiště může obsahovat libovolný počet tabulek a tabulku může obsahovat libovolný počet entit, až do limitu kapacity účtu úložiště.

### <a name="queue-storage"></a>Queue Storage

Při navrhování aplikací pro škálování ve větším měřítku jsou jednotlivé součásti aplikací často nepropojené, aby je bylo možné škálovat nezávisle. Queue storage poskytuje spolehlivé řešení zasílání zpráv pro asynchronní komunikaci mezi komponentami aplikace, ať už běží v cloudu, v klientských počítačích, na místním serveru nebo na mobilním zařízení. Queue Storage také podporuje správu asynchronních úloh a pracovní postupy procesů sestavování buildů.

Účet úložiště může obsahovat libovolný počet front a jedna fronta může obsahovat libovolný počet zpráv až do limitu kapacity účtu úložiště. Jednotlivé zprávy můžou mít velikost až 64 KB.

## <a name="next-steps"></a>Další postup

- [Konzistentní s Azure storage: rozdíly a aspekty](azure-stack-acs-differences.md)

- Další informace o službě Azure Storage najdete v tématu [Úvod do Microsoft Azure Storage](../../storage/common/storage-introduction.md)
