---
title: "Azure ML Workbench poznámky k sprintu 0 říjen 2017"
description: "Tento dokument podrobně popisuje aktualizace pro verzi sprintu 0 Azure ML"
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 10/12/2017
ms.openlocfilehash: 37e0a4461e8a0de631a6194a1eb8cc16b610954f
ms.sourcegitcommit: 1131386137462a8a959abb0f8822d1b329a4e474
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2017
---
# <a name="sprint-0---october-2017"></a>Sprint 0 – říjen 2017 

**Číslo verze: 0.1.1710.04013**

Vítá vás Azure Machine Learning Workbench následující naše počáteční verze public preview konference Microsoft Ignite 2017 první aktualizace. Hlavní aktualizace v této verzi jsou spolehlivost a ustálení opravy.  Mezi důležité problémy, které jsme řešili patří:

## <a name="new-features"></a>Nové funkce
- systému macOS vysoké Sierra se teď podporuje.

## <a name="bug-fixes"></a>Opravy chyb
### <a name="workbench-experience"></a>Workbench prostředí
- Přetažení souboru do Workbench způsobí, že Workbench došlo k chybě.
- Okno terminálu v produktu VS Code nakonfigurované jako rozhraní IDE pro Workbench nerozpoznává _az ml_ příkazy.

### <a name="workbench-authentication"></a>Workbench ověřování
Jsme provedli několik aktualizací pro zlepšení různé problémy přihlášení a ověření nahlásí.
- Okno ověřování zachová odebrání up, zejména při připojení k Internetu není stabilní.
- Problémy s lepší spolehlivostí kolem ověřování vypršení platnosti tokenu.
- V některých případech ověřování zobrazí se okno dvakrát.
- Hlavní okno Workbench stále zobrazí zpráva "ověřování", když ve skutečnosti dokončí proces ověřování a pole automaticky otevíraný dialog už se zavře.
- Pokud není k dispozici žádné připojení k Internetu, ověřovací dialog se zobrazí prázdnou obrazovku.

### <a name="data-preparation"></a>Příprava dat 
- Když je filtrován určitou hodnotu, chyb a chybějící hodnoty jsou také vyfiltrovány.
- Změna strategie vzorkování odebere následné existující operace spojení.
- Nahrazující hodnota chybějící transformace není zohledňují NaN.
- Odvození typu datum vyvolá výjimku, když je zjištěna hodnota null.

### <a name="job-execution"></a>Provádění úlohy
- Při provádění úlohy se nepodaří odeslat složky projektu, protože překročila limit velikosti neexistuje žádná zrušte chybová zpráva.
- Pokud se skript v jazyce Python uživatele změní pracovní adresář, nejsou sledovány soubory zapisovat do složky výstupy. 
- Pokud aktivní předplatné Azure je jiný než ten, který patří aktuální projekt, výsledků úlohy chyby 403.
- Když Docker není k dispozici, žádná zrušte chybová zpráva se vrátí, pokud se uživatel pokusí použít Docker jako cíl provádění.
- .runconfig soubor se neuloží automaticky, když uživatel klikne na _spustit_ tlačítko.

### <a name="jupyter-notebook"></a>Poznámkový blok Jupyter
- Poznámkový blok serveru nelze spustit, pokud uživatel používá s určitými typy přihlášení.
- Poznámkový blok serveru chybové zprávy není surface protokolů, které jsou viditelné pro uživatele.

### <a name="azure-portal"></a>portál Azure
- Když vyberete tmavým motivem portálu Azure, budou okna Správa modelu zobrazuje jako černé políčko.

### <a name="operationalization"></a>Operationalization
- Opětovné použití manifestu aktualizovat webovou službu způsobí, že novou bitovou kopii Docker vytvořené s náhodným názvem.
- Protokoly webové služby nelze načíst z Kubernetes clusteru.
- Zavádějící chybová zpráva je vytištěna, když uživatel se pokusí vytvořit Model správy účet nebo účet ML výpočetní a zaznamená problémy s oprávněními.
