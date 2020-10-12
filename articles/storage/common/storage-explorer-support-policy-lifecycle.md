---
title: Životní cyklus podpory Průzkumník služby Azure Storage | Microsoft Docs
description: Přehled zásad podpory a životního cyklu pro Průzkumník služby Azure Storage
services: storage
author: MRayermannMSFT
manager: jinglouMSFT
ms.service: storage
ms.topic: article
ms.date: 07/10/2020
ms.author: marayerm
ms.openlocfilehash: bf9cda37e0cc8186938a316c87bcc69a7a7370b8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90085276"
---
# <a name="azure-storage-explorer-support-lifecycle-and-policy"></a>Životní cyklus podpory Průzkumník služby Azure Storage a zásady

Tento dokument popisuje životní cyklus podpory a zásady pro Průzkumník služby Azure Storage.

## <a name="update-schedule-and-process"></a>Aktualizace plánu a procesu

Průzkumník služby Azure Storage se vydává čtyři až šestkrát po dobu roku. Microsoft může tento plán také obejít pro vydání oprav pro kritické problémy.

Průzkumník služby Storage kontroluje aktualizace každou hodinu a stáhne je, až budou k dispozici. K zahájení procesu instalace se ale vyžaduje přijetí uživatele. Pokud se uživatel rozhodne aktualizovat v jinou dobu, může ručně vyhledat aktualizace. V systému Windows a Linux mohou uživatelé vyhledat aktualizace výběrem možnosti **Vyhledat aktualizace** v nabídce **nápovědu** . V macOS můžete **Vyhledat aktualizace** v **nabídce aplikace**. Uživatelé můžou také přímo přejít na stránku pro stažení [Průzkumník služby Storage](https://azure.microsoft.com/features/storage-explorer/) ke stažení a instalaci nejnovější verze.

Důrazně doporučujeme, abyste vždy používali nejnovější verze Průzkumník služby Storage. Pokud vám problém nebrání v aktualizaci na nejnovější verzi Průzkumník služby Storage, otevřete problém na našem [GitHubu](https://github.com/microsoft/AzureStorageExplorer).

## <a name="support-lifecycle"></a>Životní cyklus podpory

Průzkumník služby Storage se řídí [moderními zásadami životního cyklu](https://support.microsoft.com/help/30881/modern-lifecycle-policy). Očekává se, že uživatelé budou mít k disPrůzkumník služby Storage aktuální instalaci. Aktuální nastavení zajišťuje, že uživatelé mají nejnovější možnosti, vylepšení výkonu, zabezpečení a spolehlivost služeb.

Od verze 1.14.1 se bude považovat za Nepodporovaná verze Průzkumník služby Storage, která je starší než 12 měsíců. Všechny verze před 1.14.1 se budou považovat za nepodporované od 14. července 2021. Verze, které nejsou podporovány, již nejsou zaručeny, aby plně fungovaly jako navržené a očekávané. Seznam všech vydání, jejich datum vydání a datum konce podpory najdete v tématu [releases](#releases).

Počínaje verzí 1.13.0 se výstraha v aplikaci zobrazí, jakmile je verze přibližně jeden měsíc od nedostupnosti podpory. Výstraha uživatelům přizývají k aktualizaci na nejnovější verzi Průzkumník služby Storage. Jakmile je verze Nepodporovaná, zobrazí se při každém spuštění výstraha v aplikaci.

## <a name="releases"></a>Verze

Tato tabulka obsahuje popis data vydání a konce platnosti data podpory pro jednotlivé verze Průzkumník služby Azure Storage.

| Verze Průzkumník služby Storage  | Datum vydání       | Datum ukončení podpory |
|:-------------------------:|:------------------:|:-------------------:|
| v 1.15.1                   | 2. září 2020  | 2. září 2021   |
| v 1.15.0                   | 27. srpna 2020    | 27. srpna 2021     |
| v 1.14.2                   | 16. července 2020      | 16. července 2021       |
| v 1.14.1                   | 14. července 2020      | 14. července 2021       |
| v 1.14.0                   | 24. června 2020      | 14. července 2021       |
| v 1.13.1                   | 18. května 2020       | 14. července 2021       |
| v 1.13.0                   | 1. května 2020        | 14. července 2021       |
| v 1.12.0                   | 16. ledna 2020   | 14. července 2021       |
| v 1.11.2                   | 17. prosince 2019  | 14. července 2021       |
| v 1.11.1                   | 20. listopadu 2019  | 14. července 2021       |
| v 1.11.0                   | 4. listopadu 2019   | 14. července 2021       |
| v 1.10.1                   | 19. září 2019 | 14. července 2021       |
| v 1.10.0                   | 12. září 2019 | 14. července 2021       |
| v 1.9.0                    | Od 1. července 2019       | 14. července 2021       |
| v 1.8.1                    | 10. května 2019       | 14. července 2021       |
| v 1.8.0                    | 2. května 2019        | 14. července 2021       |
| v 1.7.0                    | 5. března 2019      | 14. července 2021       |
| v 1.6.2                    | 8. ledna 2019    | 14. července 2021       |
| v 1.6.1                    | 18. prosince 2018  | 14. července 2021       |
| v 1.6.0                    | 4. prosince 2018   | 14. července 2021       |
| v 1.5.0                    | 29. října 2018   | 14. července 2021       |
| v 1.4.4                    | 15. října 2018   | 14. července 2021       |
| v 1.4.2                    | Září 24, 2018 | 14. července 2021       |
| v 1.4.1                    | 28. srpna 2018    | 14. července 2021       |
| v 1.3.1                    | 11. července 2018      | 14. července 2021       |
| v 1.2.0                    | 12. června 2018      | 14. července 2021       |
| v 1.1.0                    | 9. května 2018        | 14. července 2021       |
| v 1.0.0 (a starší)        | 16. dubna 2018     | 14. července 2021       |
