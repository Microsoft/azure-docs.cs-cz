---
title: Používání služby Azure AD Connect Health se synchronizací | Dokumentace Microsoftu
description: Toto je stránka o službě Azure AD Connect Health, která popisuje sledování synchronizace Azure AD Connect.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: daveba
ms.assetid: 1dfbeaba-bda2-4f68-ac89-1dbfaf5b4015
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/18/2017
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: abed56ee64cbca8646c1aa1d24fea292aa4d8de3
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56201818"
---
# <a name="monitor-azure-ad-connect-sync-with-azure-ad-connect-health"></a>Sledování synchronizace Azure AD Connect pomocí služby Azure AD Connect Health
Následující dokumentace se věnuje sledování služby Azure AD Connect (Sync) pomocí služby Azure AD Connect Health.  Informace o sledování služby AD FS pomocí služby Azure AD Connect Health najdete v článku [Používání služby Azure AD Connect Health se službou AD FS](how-to-connect-health-adfs.md). Informace o sledování služby Active Directory Domain Services pomocí služby Azure AD Connect Health najdete v článku [Používání služby Azure AD Connect Health se službou AD DS](how-to-connect-health-adds.md).

![Azure AD Connect Health pro synchronizaci](./media/how-to-connect-health-sync/syncsnapshot.png)

## <a name="alerts-for-azure-ad-connect-health-for-sync"></a>Upozornění služby Azure AD Connect Health pro synchronizaci
Část pojednávající o výstrahách služby Azure AD Connect Health pro synchronizaci uvádí seznam aktivních upozornění. Každé upozornění obsahuje důležité informace, postup řešení a odkazy na související dokumentaci. Výběrem aktivního nebo vyřešeného upozornění zobrazíte nové okno s doplňujícími informacemi, kroky, které můžete k vyřešení upozornění použít, a odkazy na další dokumentaci. Můžete si zobrazit i historické údaje o dříve vyřešených upozorněních.

Výběrem některého upozornění zobrazíte doplňující informace, kroky, které můžete k vyřešení upozornění použít, a odkazy na další dokumentaci.

![Chyba synchronizace služby Azure AD Connect](./media/how-to-connect-health-sync/alert.png)

### <a name="limited-evaluation-of-alerts"></a>Omezené vyhodnocení upozornění
Pokud služba Azure AD Connect nepoužívá výchozí konfiguraci (například když je filtrování atributů změněné z výchozí konfigurace na vlastní), agent služby Azure AD Connect Health nebude odesílat chybové události související se službou Azure AD Connect.

Služba tak bude při vyhodnocování upozornění omezená. Zobrazí se banner, který v rámci služby upozorňuje na tento stav na webu Azure Portal.

![Azure AD Connect Health pro synchronizaci](./media/how-to-connect-health-sync/banner.png)

Můžete to změnit kliknutím na „Nastavení“ a povolením, aby agent služby Azure AD Connect Health mohl odesílat všechny protokoly chyb.

![Azure AD Connect Health pro synchronizaci](./media/how-to-connect-health-sync/banner2.png)

## <a name="sync-insight"></a>Analýza synchronizace
Správce často zajímá, jak dlouho trvá synchronizace změn do služby Azure AD, a množství změn, které probíhají. Tato funkce poskytuje snadný způsob vizualizace těchto informací pomocí uvedených grafů:   

* sledování latence operací synchronizace
* sledování trendu změn objektů

### <a name="sync-latency"></a>Latence synchronizace
Tato funkce poskytuje grafické zobrazení trendu latence operací synchronizace (import, export atd.) pro jednotlivé konektory.  Díky tomu se nejen rychle a snadno seznámíte s latencí operací (latence je větší, pokud máte velké sady změn), ale budete moct i zjišťovat anomálie v latenci, které můžou vyžadovat další šetření.

![Latence synchronizace](./media/how-to-connect-health-sync/synclatency02.png)

Ve výchozím nastavení se zobrazuje jenom latence operace „exportu“ na konektoru Azure AD.  Pokud chcete zobrazit další operace na konektoru nebo zobrazit operace z jiných konektorů, klikněte pravým tlačítkem na graf, vyberte Upravit graf, nebo klikněte na tlačítko Upravit graf latence a zvolte konkrétní operaci a konektor.

### <a name="sync-object-changes"></a>Změny objektů synchronizace
Tato funkce nabízí grafické zobrazení trendu v počtu změn, které se vyhodnocují a exportují do služby Azure AD.  V současné době je snaha o shromáždění těchto informací z protokolů synchronizace obtížná.  Graf poskytuje nejen jednodušší způsob sledování počtu změn ve vašem prostředí, ale i vizuální zobrazení chyb, ke kterým dochází.

![Latence synchronizace](./media/how-to-connect-health-sync/syncobjectchanges02.png)

## <a name="object-level-synchronization-error-report"></a>Sestava chyb synchronizace na úrovni objektu
Tato funkce poskytuje sestavu chyb synchronizace, ke kterým může dojít při synchronizaci dat identity mezi službou Windows Server AD a Azure AD pomocí služby Azure AD Connect.

* Sestava obsahuje chyby zaznamenané klientem synchronizace (Azure AD Connect verze 1.1.281.0 nebo vyšší).
* Zahrnuje chyby, ke kterým došlo při poslední operaci synchronizace u synchronizačního modulu. (Export v konektoru Azure AD)
* Agent služby Azure AD Connect Health pro synchronizaci musí mít odchozí připojení k požadovaným koncovým bodům, aby se v sestavě mohla promítnout nejnovější data.
* Sestava se **aktualizuje každých 30 minut** pomocí dat nahraných agentem služby Azure AD Connect Health pro synchronizaci. Poskytuje následující klíčové funkce:

  * Kategorizace chyb
  * Seznam chybných objektů podle kategorie
  * Všechna data o chybách na jednom místě
  * Souběžné porovnání objektů, u kterých došlo k chybě z důvodu konfliktu
  * Stažení sestavy chyb ve formátu CSV

### <a name="categorization-of-errors"></a>Kategorizace chyb
Sestava zařazuje stávající chyby synchronizace do následujících kategorií:

| Kategorie | Popis |
| --- | --- |
| Duplicitní atribut |Chyby vzniklé při pokusu služby Azure AD Connect o vytvoření nebo aktualizaci objektů s duplicitními hodnotami atributů ve službě Azure AD, které musí být v tenantovi jedinečné, například proxyAddresses, UserPrincipalName |
| Neshoda dat |Chyby synchronizace vzniklé v důsledku neúspěšného měkkého párování objektů |
| Chyba ověřování dat |Chyby vzniklé v důsledku neplatných dat, jako jsou nepodporované znaky v klíčových atributech (např. UserPrincipalName), chyby formátování, které se před zápisem do Azure AD nepodaří ověřit |
| Změna federované domény | Chyby, když účty používají jinou federovanou doménu. |
| Rozsáhlý atribut |Chyby vzniklé v důsledku toho, že některé atributy překračují povolenou velikost, délku nebo počet |
| Ostatní |Všechny ostatní chyby, které nevyhovují uvedeným kategoriím Na základě zpětné vazby rozdělíme tuto kategorii do podkategorií. |

![Souhrnná sestava chyb synchronizace](./media/how-to-connect-health-sync/errorreport01.png)
![Kategorie sestavy chyb synchronizace](./media/how-to-connect-health-sync/SyncErrorByTypes.PNG)

### <a name="list-of-objects-with-error-per-category"></a>Seznam chybných objektů podle kategorie
Rozbalením jednotlivých kategorií zobrazíte seznam objektů, které mají chybu v dané kategorii.
![Seznam sestav chyb synchronizace](./media/how-to-connect-health-sync/errorreport03.png)

### <a name="error-details"></a>Podrobnosti o chybě
Následující data jsou k dispozici v podrobném zobrazení jednotlivých chyb.

* Zvýrazněný konfliktní atribut
* Identifikátory příslušného *objektu AD*
* Identifikátory příslušného *objektu Azure AD* (podle vhodnosti)
* Popis chyby a její řešení

![Podrobnosti sestavy chyb synchronizace](./media/how-to-connect-health-sync/duplicateAttributeSyncError.png)

### <a name="download-the-error-report-as-csv"></a>Stažení sestavy chyb ve formátu CSV
Pomocí tlačítka Exportovat můžete stáhnout soubor CSV s podrobnými informacemi o všech chybách.

### <a name="diagnose-and-remediate-sync-errors"></a>Diagnostika a oprava chyb synchronizace 
Určité scénáře chyb synchronizace kvůli duplicitním atributům, které zahrnují uživatelskou aktualizaci zdrojového ukotvení, je možné opravit přímo na portálu. Další informace o [diagnostice a opravě chyb synchronizace kvůli duplicitním atributům](how-to-connect-health-diagnose-sync-errors.md)

## <a name="related-links"></a>Související odkazy
* [Řešení chyb při synchronizaci](tshoot-connect-sync-errors.md)
* [Odolnost duplicitních atributů](how-to-connect-syncservice-duplicate-attribute-resiliency.md)
* [Azure AD Connect Health](whatis-hybrid-identity-health.md)
* [Instalace agenta služby Azure AD Connect Health](how-to-connect-health-agent-install.md)
* [Operace služby Azure AD Connect Health](how-to-connect-health-operations.md)
* [Používání služby Azure AD Connect Health se službou AD FS](how-to-connect-health-adfs.md)
* [Používání služby Azure AD Connect Health se službou AD DS](how-to-connect-health-adds.md)
* [Azure AD Connect Health – nejčastější dotazy](reference-connect-health-faq.md)
* [Historie verzí služby Azure AD Connect Health](reference-connect-health-version-history.md)
