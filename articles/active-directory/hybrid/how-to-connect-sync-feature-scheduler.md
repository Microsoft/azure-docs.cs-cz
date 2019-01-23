---
title: 'Synchronizace Azure AD Connect: Scheduler | Dokumentace Microsoftu'
description: Toto téma popisuje funkce integrované plánovače ve službě Azure AD Connect sync.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
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
ms.openlocfilehash: 8099194feed3761e32686ab15e8738b10ffd4e8b
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54462313"
---
# <a name="azure-ad-connect-sync-scheduler"></a>Synchronizace Azure AD Connect: Scheduler
Toto téma popisuje (označovaný také jako integrované plánovače ve službě Azure AD Connect sync synchronizační stroj).

Tato funkce byla zavedena v systému sestavení 1.1.105.0 (všeobecně dostupné. února 2016).

## <a name="overview"></a>Přehled
Synchronizace Azure AD Connect synchronizaci změn, ke kterým dochází v místním adresáři pomocí plánovače. Existují dva procesy Plánovač, jednu pro synchronizaci hesel a druhou pro atribut/objektu úlohy synchronizace a údržba. Toto téma popisuje ten.

V dřívějších verzích byla externí synchronizačního modulu scheduler objektů a atributů. Použít Plánovač úloh Windows nebo samostatnou službu Windows ke spuštění procesu synchronizace. Plánovač má s integrovanými verze 1.1 na synchronizace engine a umožňují některá vlastní nastavení. Nové četnost synchronizací výchozí je 30 minut.

Plánovač zodpovídá za dvě úlohy:

* **Synchronizační cyklus**. Proces importu, synchronizovat a exportovat změny.
* **Úlohy údržby**. Obnovení klíče a certifikáty pro resetování hesla a Device Registration Service (DRS). Odstranit staré položky v protokolu operací.

Vždy spuštěný Plánovač samotný, ale můžete ji nakonfigurovat, aby se spustily jenom jedna nebo žádná z těchto úloh. Například pokud potřebujete mít vlastní proces synchronizace cyklu, můžete zakázat tuto úlohu v Plánovači ale přesto spustit úlohu údržby.

## <a name="scheduler-configuration"></a>Konfiguraci plánovače
Pokud chcete zobrazit aktuální nastavení konfigurace, přejděte na prostředí PowerShell a spusťte `Get-ADSyncScheduler`. To se dozvíte něco podobně jako na tomto obrázku:

![GetSyncScheduler](./media/how-to-connect-sync-feature-scheduler/getsynccyclesettings2016.png)

Pokud se zobrazí **příkaz synchronizace nebo rutina není k dispozici** když tuto rutinu spustíte, pak není načten modul prostředí PowerShell. Tento problém může nastat při spuštění služby Azure AD Connect na řadiči domény nebo na serveru s vyšší úrovní omezení prostředí PowerShell než výchozí nastavení. Pokud se zobrazí tato chyba, spusťte `Import-Module ADSync` zpřístupnit rutinu.

* **AllowedSyncCycleInterval**. Nejkratší časový interval mezi cykly synchronizace povolené ve službě Azure AD. Nelze synchronizovat častěji, než toto nastavení a i nadále podporovaná.
* **CurrentlyEffectiveSyncCycleInterval**. Plán aktuálně používána. Má stejnou hodnotu jako CustomizedSyncInterval (Pokud nastavit) Pokud není častější než AllowedSyncInterval. Pokud používáte sestavení před 1.1.281 a změníte CustomizedSyncCycleInterval, tato změna se projeví po příštím synchronizačním cyklu. Z buildu 1.1.281 změna se projeví okamžitě.
* **CustomizedSyncCycleInterval**. Pokud chcete, aby spuštění frekvencí jakékoli jiné než výchozí 30 minut, nakonfigurujete toto nastavení. Na obrázku výše je nastavená plánovač provádět každou hodinu. Pokud nastavíte toto nastavení na hodnotu nižší než AllowedSyncInterval, pak ten se používá.
* **NextSyncCyclePolicyType**. Rozdílová nebo počáteční. Definuje, jestli se další spuštění by měl jenom procesem rozdílové změny, nebo pokud dalšího spuštění by měl provést úplný import a synchronizaci. Ten by také znovu zpracovat všechna pravidla nové nebo změněné.
* **NextSyncCycleStartTimeInUTC**. Příště, když Plánovač spustí další cyklus synchronizace.
* **PurgeRunHistoryInterval**. Čas se uchovávají protokoly operací. Tyto protokoly můžete zkontrolovat v synchronization service manager. Výchozí hodnota je pro tyto protokoly po dobu 7 dní.
* **SyncCycleEnabled**. Určuje, zda Plánovač běží import, synchronizace a exportu procesů jako součást operace.
* **MaintenanceEnabled**. Zobrazí, pokud je povoleno procesu údržby. Aktualizace certifikátů nebo klíčů a vymaže provoznímu protokolu.
* **StagingModeEnabled**. Zobrazí-li [pracovním režimu](how-to-connect-sync-operations.md#staging-mode) je povolená. Pokud je toto nastavení povolené, pak potlačí exporty spouštění, ale stále spusťte import a synchronizaci.
* **SchedulerSuspended**. Nastavte připojení během upgradu dočasně bloku Plánovač spouštění.

Je možné změnit některá z těchto nastavení s `Set-ADSyncScheduler`. Je možné upravit následující parametry:

* CustomizedSyncCycleInterval
* NextSyncCyclePolicyType
* PurgeRunHistoryInterval
* SyncCycleEnabled
* MaintenanceEnabled

V dřívějších sestavení služby Azure AD Connect **isStagingModeEnabled** byla vystavena v sadě ADSyncScheduler. Je **nepodporované** pro nastavení této vlastnosti. Vlastnost **SchedulerSuspended** by měl být upraven pouze Connect. Je **nepodporované** přímo nastavit pomocí prostředí PowerShell.

Plánovač konfigurace je uložená ve službě Azure AD. Pokud máte testovací server, všechny změny na primárním serveru také ovlivní pracovní server (s výjimkou IsStagingModeEnabled).

### <a name="customizedsynccycleinterval"></a>CustomizedSyncCycleInterval
Syntaxe: `Set-ADSyncScheduler -CustomizedSyncCycleInterval d.HH:mm:ss`  
d – dny, HH – hodiny, mm - minut, ss sekundy

Příklad: `Set-ADSyncScheduler -CustomizedSyncCycleInterval 03:00:00`  
Změní, aby každé 3 hodiny.

Příklad: `Set-ADSyncScheduler -CustomizedSyncCycleInterval 1.0:0:0`  
Změny služby Plánovač na každodenní spouštění.

### <a name="disable-the-scheduler"></a>Zakázat plánovače  
Pokud potřebujete provést změny konfigurace, které chcete zakázat plánovač. Například, když jste [konfigurace filtrování](how-to-connect-sync-configure-filtering.md) nebo [měnit synchronizační pravidla](how-to-connect-sync-change-the-configuration.md).

Pokud chcete zakázat plánovač, spusťte `Set-ADSyncScheduler -SyncCycleEnabled $false`.

![Zakázat plánovače](./media/how-to-connect-sync-feature-scheduler/schedulerdisable.png)

Pokud jste provedli změny, nezapomeňte povolit Plánovač znovu s `Set-ADSyncScheduler -SyncCycleEnabled $true`.

## <a name="start-the-scheduler"></a>Spustit Plánovač
Plánovač se ve výchozím nastavení spouští každých 30 minut. V některých případech můžete chtít spustit cyklus synchronizace mezi cykly plánované nebo budete muset spustit jiného typu.

**Cyklus synchronizace delta**  
Cyklus synchronizace delta zahrnuje následující kroky:

* Rozdílový import na všechny konektory
* Rozdílová synchronizace na všechny konektory
* Exportovat na všechny konektory

Je možné, že máte naléhavé změny, která musí být synchronizovány okamžitě, proto budete muset ručně spustit cyklus. Pokud je potřeba ručně spustit cyklus, pak z prostředí PowerShell spustit `Start-ADSyncSyncCycle -PolicyType Delta`.

**Cyklus úplné synchronizace**  
Pokud jste provedli jednu z následující změny konfigurace, budete muset spustit cyklus úplné synchronizace (označovaný také jako Počáteční):

* Přidat více objektů nebo atributů, které mají být importována ze zdrojového adresáře
* Změny provedené v synchronizační pravidla
* Změnit [filtrování](how-to-connect-sync-configure-filtering.md) jiný počet objektů, které by tak měly být zahrnuté

Pokud jste provedli jednu z těchto změn, budete muset spustit cyklus úplné synchronizace, synchronizační modul je moci znovu sloučit prostor konektoru. Úplná synchronizace cyklus zahrnuje následující kroky:

* Úplný Import na všechny konektory
* Plná synchronizace v všechny konektory
* Exportovat na všechny konektory

K zahájení cyklu úplné synchronizace, spusťte `Start-ADSyncSyncCycle -PolicyType Initial` z příkazového řádku Powershellu. Tento příkaz spustí cyklus úplné synchronizace.

## <a name="stop-the-scheduler"></a>Zastavení plánovače
Plánovač synchronizační cyklus běží, potřebujete ji zastavit. Například pokud spustíte Průvodce instalací a tato chyba:

![SyncCycleRunningError](./media/how-to-connect-sync-feature-scheduler/synccyclerunningerror.png)

Když cyklus synchronizace je spuštěná, nemůžete provádět změny konfigurace. Počkat až do dokončení procesu plánovače, ale taky můžete zastavit jeho abyste mohli dělat změny okamžitě. Zastavování aktuální cyklu není škodlivých a čekajících změn se zpracovávají další spuštění.

1. Začněte tím, že o tom, aby ukončit jeho aktuální cyklu pomocí rutiny Powershellu `Stop-ADSyncSyncCycle`.
2. Pokud používáte sestavení před 1.1.281, nezastaví pak zastavení plánovače aktuální konektor z jeho aktuálního úkolu. Vynutit konektoru zastavit pomocí následujících kroků: ![StopAConnector](./media/how-to-connect-sync-feature-scheduler/stopaconnector.png)
   * Spustit **synchronizační služba** z nabídky start. Přejděte na **konektory**, zvýrazněte konektoru se stavem **systémem**a vyberte **Zastavit** z akcí.

Plánovač je stále aktivní a znovu spustí na nejbližší příležitosti.

## <a name="custom-scheduler"></a>Vlastní plánovače
Rutiny uvedené v této části jsou dostupné pouze v sestavení [1.1.130.0](reference-connect-version-history.md#111300) a novější.

Pokud integrované plánovače nesplňuje vaše požadavky, můžete naplánovat konektorů pomocí Powershellu.

### <a name="invoke-adsyncrunprofile"></a>Invoke-ADSyncRunProfile
Profil, který můžete spustit pro konektor tímto způsobem:

```
Invoke-ADSyncRunProfile -ConnectorName "name of connector" -RunProfileName "name of profile"
```

Názvy pro [názvy konektorů](how-to-connect-sync-service-manager-ui-connectors.md) a [názvy profilů spuštění](how-to-connect-sync-service-manager-ui-connectors.md#configure-run-profiles) najdete v [Synchronization Service Manager UI](how-to-connect-sync-service-manager-ui.md).

![Vyvolání profil spuštění](./media/how-to-connect-sync-feature-scheduler/invokerunprofile.png)  

`Invoke-ADSyncRunProfile` Rutina je synchronní, to znamená, ho nevrátí ovládací prvek dokud konektoru byla dokončena operace, ať už úspěšně nebo s chybou.

Při plánování své konektory, doporučujeme naplánovat v následujícím pořadí:

1. (Úplné nebo rozdílové) Importovat z místních adresářů, jako je Active Directory
2. (Úplné nebo rozdílové) Import ze služby Azure AD
3. (Úplné nebo rozdílové) Synchronizace místních adresářů, jako je Active Directory
4. (Úplné nebo rozdílové) Synchronizace Azure AD
5. Export do služby Azure AD
6. Exportovat do místních adresářů, jako je Active Directory

Pořadí je vykonávání konektory integrované plánovače.

### <a name="get-adsyncconnectorrunstatus"></a>Get-ADSyncConnectorRunStatus
Můžete také sledovat synchronizačního modulu a zjistěte, jestli je zaneprázdněná nebo nečinnosti. Tato rutina vrací prázdný výsledek, pokud synchronizační modul je v nečinnosti a není spuštěn konektor. Pokud konektor je spuštěný, vrátí název konektoru.

```
Get-ADSyncConnectorRunStatus
```

![Stav spuštění konektoru](./media/how-to-connect-sync-feature-scheduler/getconnectorrunstatus.png)  
Na obrázku výše je první řádek ze stavu, ve kterém je synchronizační modul nečinnosti. Na druhém řádku od kdy je spuštěný konektor služby Azure AD.

## <a name="scheduler-and-installation-wizard"></a>Plánovač a Průvodce instalací
Pokud spustíte Průvodce instalací, Plánovač dočasně pozastaveno. Toto chování je, protože se předpokládá, že provedete změny konfigurace a tato nastavení nelze použít, pokud aktivně běží synchronizačního modulu. Z tohoto důvodu nesmí zůstat Průvodce instalací otevřít od zastaví synchronizační modul provádět všechny akce synchronizace.

## <a name="next-steps"></a>Další postup
Další informace o [synchronizace Azure AD Connect](how-to-connect-sync-whatis.md) konfigurace.

Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).
