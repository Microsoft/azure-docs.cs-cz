---
title: 'Synchronizace Azure AD Connect: Scheduler | Microsoft Docs'
description: Toto téma popisuje funkci integrované plánovače v synchronizaci Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 6b1a598f-89c0-4244-9b20-f4aaad5233cf
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/12/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: fa99de4aac11e7310085cc37e6ebaee441415c61
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34594155"
---
# <a name="azure-ad-connect-sync-scheduler"></a>Synchronizace Azure AD Connect: plánovače
Toto téma popisuje předdefinované plánovače v synchronizaci Azure AD Connect (také známa jako synchronizační modul).

Tato funkce byla zavedená s sestavení 1.1.105.0 (vydané. února 2016).

## <a name="overview"></a>Přehled
Synchronizace Azure AD Connect synchronizovat změny, které se vašeho místního adresáře pomocí plánovače. Existují dva procesy scheduler, jednu pro synchronizaci hesel a druhou pro objekt nebo atribut úlohy synchronizace a údržby. Toto téma popisuje ty druhé.

V dřívějších verzích se externí synchronizačního modulu scheduler pro objekty a atributy. Použije se spustit proces synchronizace plánovače úloh systému Windows nebo samostatné služby systému Windows. Plánovač je pomocí integrované verze 1.1 na synchronizace modul a umožňují některé vlastní nastavení. Nové výchozí frekvence synchronizace je 30 minut.

Plánovač zodpovídá za dvě úlohy:

* **Synchronizační cyklus**. Proces importu, synchronizaci a exportovat změny.
* **Úlohy údržby**. Obnovení klíčů a certifikátů pro obnovení hesla a služby DRS (Device Registration). Vymazat staré položky v protokolu operací.

Plánovač samotné bude vždy spuštěn, ale můžete nastavit běžela jenom jedna nebo žádná z těchto úloh. Například pokud je potřeba mít vlastní proces synchronizace cyklus, můžete zakázat tuto úlohu v Plánovači ale pořád spustit úlohu údržby.

## <a name="scheduler-configuration"></a>Konfiguraci plánovače
Chcete-li zobrazit aktuální nastavení konfigurace, přejděte na prostředí PowerShell a spusťte `Get-ADSyncScheduler`. Zobrazuje něco jako tomto obrázku:

![GetSyncScheduler](./media/active-directory-aadconnectsync-feature-scheduler/getsynccyclesettings2016.png)

Pokud se zobrazí **příkaz synchronizace nebo rutiny není k dispozici** při spuštění této rutiny, pak není načíst modul prostředí PowerShell. Tento problém se může stát, když spustíte Azure AD Connect na řadiči domény nebo na serveru s vyšší úrovně omezení prostředí PowerShell než výchozí nastavení. Pokud se zobrazí tato chyba, spusťte `Import-Module ADSync` chcete zpřístupnit rutinu.

* **AllowedSyncCycleInterval**. Nejkratší časový interval mezi cykly synchronizace povolený Azure AD. Nelze synchronizovat častěji, než toto nastavení a přesto být podporována.
* **CurrentlyEffectiveSyncCycleInterval**. Plán právě vliv. Má stejnou hodnotu jako CustomizedSyncInterval (Pokud nastavit) Pokud není častější, než AllowedSyncInterval. Pokud používáte sestavení před 1.1.281 a změníte CustomizedSyncCycleInterval, tato změna se projeví po příštím synchronizačním cyklu. Ze sestavení 1.1.281 změna se projeví okamžitě.
* **CustomizedSyncCycleInterval**. Pokud chcete plánovač spustit frekvencí jakékoli jiné než výchozí 30 minut, nakonfigurujete toto nastavení. Na obrázku výše je nastavená Plánovač běžela každou hodinu. Nastavíte-li toto nastavení na hodnotu nižší než AllowedSyncInterval, k tomu je použita.
* **NextSyncCyclePolicyType**. Rozdílová nebo počáteční. Určuje, zda příštím spuštění by měl jenom proces rozdílové změny, nebo pokud příštím spuštění musí používat úplnou import a synchronizaci. K tomu by také znovu zpracovat všechna pravidla, nové nebo změněné.
* **NextSyncCycleStartTimeInUTC**. Příště Plánovač spustí příštím synchronizačním cyklu.
* **PurgeRunHistoryInterval**. Čas protokoly operací by měly být udržovány. Tyto protokoly mohou být zjišťovány synchronization service manager. Výchozí hodnota je pro tyto protokoly pro 7 dní.
* **SyncCycleEnabled**. Označuje, zda Plánovač běží import, synchronizaci a export procesy v rámci jeho operace.
* **MaintenanceEnabled**. Zobrazí, pokud je povoleno procesu údržby. Ho aktualizuje certifikáty nebo klíče a vymaže v protokolu operací.
* **StagingModeEnabled**. Zobrazí-li [pracovním režimu](active-directory-aadconnectsync-operations.md#staging-mode) je povoleno. Pokud je toto nastavení povoleno, pak jej potlačí exporty spuštění, ale pořád spustit import a synchronizaci.
* **SchedulerSuspended**. Nastavte Connect během upgradu dočasně blok Plánovač spuštění.

Můžete změnit některé z těchto nastavení s `Set-ADSyncScheduler`. Tyto parametry se dají změnit:

* CustomizedSyncCycleInterval
* NextSyncCyclePolicyType
* PurgeRunHistoryInterval
* SyncCycleEnabled
* MaintenanceEnabled

V dřívějších sestavení Azure AD Connect **isStagingModeEnabled** byl vystavený v ADSyncScheduler sady. Je **nepodporované** pro tuto vlastnost nastavit. Vlastnost **SchedulerSuspended** by měl být upraven pouze Connect. Je **nepodporované** přímo nastavit pomocí prostředí PowerShell.

Konfiguraci plánovače, kterou je uložená ve službě Azure AD. Pokud máte na testovacím serveru, všechny změny na primárním serveru také ovlivní pracovní server (s výjimkou IsStagingModeEnabled).

### <a name="customizedsynccycleinterval"></a>CustomizedSyncCycleInterval
Syntaxe: `Set-ADSyncScheduler -CustomizedSyncCycleInterval d.HH:mm:ss`  
d – počet dnů, HH - hodiny, mm - minuty, ss - sekundy

Příklad: `Set-ADSyncScheduler -CustomizedSyncCycleInterval 03:00:00`  
Změní Plánovač každé 3 hodiny.

Příklad: `Set-ADSyncScheduler -CustomizedSyncCycleInterval 1.0:0:0`  
Změny změnit plánovače na každodenní spouštění.

### <a name="disable-the-scheduler"></a>Zakázat plánovače  
Pokud potřebujete provést změny konfigurace, budete chtít zakázat plánovače. Například, když jste [konfigurace filtrování](active-directory-aadconnectsync-configure-filtering.md) nebo [změnit pravidla synchronizace](active-directory-aadconnectsync-change-the-configuration.md).

Pokud chcete zakázat plánovač, spusťte `Set-ADSyncScheduler -SyncCycleEnabled $false`.

![Zakázat plánovače](./media/active-directory-aadconnectsync-change-the-configuration/schedulerdisable.png)

Pokud jste udělali změny, nezapomeňte povolit Plánovač znovu s `Set-ADSyncScheduler -SyncCycleEnabled $true`.

## <a name="start-the-scheduler"></a>Spuštění plánovače
Plánovač je ve výchozím nastavení spouští každých 30 minut. V některých případech můžete chtít spustit synchronizační cyklus mezi naplánované cyklů nebo budete muset spustit jiného typu.

**Rozdílová synchronizace cyklu**  
Cyklus synchronizace delta zahrnuje následující kroky:

* Rozdílový import na všechny konektory
* Rozdílová synchronizace na všechny konektory
* Exportovat na všechny konektory

Může to být, že máte naléhavé změny, která musí být synchronizovány okamžitě, proto musíte ručně spustit cyklus. Pokud potřebujete ručně spustit cyklus, pak z prostředí PowerShell spustit `Start-ADSyncSyncCycle -PolicyType Delta`.

**Cyklus úplné synchronizace**  
Pokud jste provedli jednu z následujících změn konfigurace, budete muset spustit cyklus úplné synchronizace (také známa jako Počáteční):

* Přidat další objekty nebo atributy, které mají být importována ze zdrojového adresáře
* Změny synchronizační pravidla
* Změnit [filtrování](active-directory-aadconnectsync-configure-filtering.md) tak odlišný počet objektů, které by měly být zahrnuty

Pokud jste provedli jednu z těchto změn, budete muset spustit úplnou synchronizaci cyklus, takže synchronizační modul má možnost znovu sloučit prostor konektoru. Cyklus úplné synchronizace zahrnuje následující kroky:

* Úplný Import na všechny konektory
* Plná synchronizace v všechny konektory
* Exportovat na všechny konektory

Chcete-li cyklus úplné synchronizace, spusťte `Start-ADSyncSyncCycle -PolicyType Initial` z řádku prostředí PowerShell. Tento příkaz spustí cyklus úplné synchronizace.

## <a name="stop-the-scheduler"></a>Zastavení plánovače
Pokud plánovač aktuálně běží synchronizační cyklus, možná budete muset zastavte ji. Například pokud spustíte Průvodce instalací a se tato chyba:

![SyncCycleRunningError](./media/active-directory-aadconnectsync-feature-scheduler/synccyclerunningerror.png)

Když běží synchronizační cyklus, nemůžete změnit konfiguraci. Může Počkejte, dokud Plánovač byl dokončen proces, ale můžete ho umožní vám provádět změny okamžitě také zastavit. Zastavení aktuální cyklus není škodlivé a změny čekající na zpracování se zpracují bez další spuštění.

1. Začněte tím, že se na zastavení jeho aktuální cyklus pomocí rutiny prostředí PowerShell plánovače `Stop-ADSyncSyncCycle`.
2. Pokud používáte sestavení před 1.1.281, pak zastavování plánovače nezastaví aktuální konektor z jeho aktuální úlohy. Chcete-li vynutit konektor zastavit, proveďte následující akce: ![StopAConnector](./media/active-directory-aadconnectsync-feature-scheduler/stopaconnector.png)
   * Spustit **synchronizační služba** z nabídky start. Přejděte na **konektory**, zvýrazněte konektor s daným stavem **systémem**a vyberte **Zastavit** z akce.

Plánovač je stále aktivní, začne znovu na nejbližší příležitosti.

## <a name="custom-scheduler"></a>Vlastní plánovače
Rutiny popsané v této části jsou dostupné jenom v sestavení [1.1.130.0](active-directory-aadconnect-version-history.md#111300) a novější.

Pokud integrované Plánovač nevyhovuje vašim požadavkům, můžete naplánovat konektory pomocí prostředí PowerShell.

### <a name="invoke-adsyncrunprofile"></a>Vyvolání ADSyncRunProfile
Profil můžete spustit pro konektor tímto způsobem:

```
Invoke-ADSyncRunProfile -ConnectorName "name of connector" -RunProfileName "name of profile"
```

Názvů pro [názvy konektorů.](active-directory-aadconnectsync-service-manager-ui-connectors.md) a [spustit profil názvy](active-directory-aadconnectsync-service-manager-ui-connectors.md#configure-run-profiles) lze nalézt v [uživatelského rozhraní Synchronization Service Manager](active-directory-aadconnectsync-service-manager-ui.md).

![Vyvolání profil spuštění](./media/active-directory-aadconnectsync-feature-scheduler/invokerunprofile.png)  

`Invoke-ADSyncRunProfile` Rutina je synchronní, to znamená, nevrátí řízení až konektor po dokončení operace úspěšně nebo se stala chyba.

Při plánování vaší konektory, doporučuje se při plánování je v následujícím pořadí:

1. (Úplná nebo rozdílová) Importovat z místních adresářů, jako je Active Directory
2. (Úplná nebo rozdílová) Import ze služby Azure AD
3. (Úplná nebo rozdílová) Synchronizaci z místních adresářů, jako je Active Directory
4. (Úplná nebo rozdílová) Synchronizace z Azure AD
5. Export do služby Azure AD
6. Exportovat do místních adresářů, jako je Active Directory

Pořadí je, jak předdefinované Plánovač běží konektory.

### <a name="get-adsyncconnectorrunstatus"></a>Get-ADSyncConnectorRunStatus
Také můžete monitorovat synchronizační modul a zjistěte, zda je zaneprázdněn nebo nečinné. Tato rutina vrací prázdný výsledek, pokud synchronizační modul je nečinnosti a není spuštěn konektor. Pokud je spuštěn konektor, vrátí název konektoru.

```
Get-ADSyncConnectorRunStatus
```

![Stav spuštění konektoru](./media/active-directory-aadconnectsync-feature-scheduler/getconnectorrunstatus.png)  
Na obrázku výše je první řádek z stavu, kdy je synchronizační modul nečinnosti. Druhý řádek z při spuštění konektor služby Azure AD.

## <a name="scheduler-and-installation-wizard"></a>Průvodce Plánovač a instalace
Pokud spustíte Průvodce instalací, Plánovač dočasně pozastaveno. Toto chování je vzhledem k tomu, že se předpokládá, provedete změny v konfiguraci a nelze ji použít tato nastavení, pokud aktivně synchronizační modul běží. Z tohoto důvodu nenechávejte Průvodce instalací otevřete vzhledem k tomu, že zastaví synchronizační modul provádět všechny akce synchronizace.

## <a name="next-steps"></a>Další postup
Další informace o [synchronizace Azure AD Connect](active-directory-aadconnectsync-whatis.md) konfigurace.

Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](active-directory-aadconnect.md).
