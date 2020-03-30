---
title: 'Synchronizace služby Azure AD Connect: Plánovač | Dokumenty společnosti Microsoft'
description: Toto téma popisuje předdefinovanou funkci plánovače v synchronizaci Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 6b1a598f-89c0-4244-9b20-f4aaad5233cf
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/01/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 309adfbebd4f4b615ac1f4061823ca01f3d3ee15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261070"
---
# <a name="azure-ad-connect-sync-scheduler"></a>Synchronizace Azure AD Connect: Plánovač
Toto téma popisuje předdefinovaný plánovač v synchronizaci Azure AD Connect (synchronizační modul).

Tato funkce byla představena s sestavením 1.1.105.0 (vydáno v únoru 2016).

## <a name="overview"></a>Přehled
Synchronizace synchronizace synchronizace Azure AD Connect synchronizuje změny, ke kterým dochází v místním adresáři pomocí plánovače. Existují dva procesy plánovače, jeden pro synchronizaci hesel a druhý pro objekt/atribut synchronizace a údržby úlohy. Toto téma se týká druhé.

V dřívějších verzích plánovač pro objekty a atributy byl externí synchronizačního modulu. K aktivaci procesu synchronizace se používal plánovač úloh systému Windows nebo samostatná služba systému Windows. Plánovač je s 1.1 verze vestavěné do synchronizačního modulu a umožňují některé přizpůsobení. Nová výchozí frekvence synchronizace je 30 minut.

Plánovač je zodpovědný za dva úkoly:

* **Cyklus synchronizace**. Proces importu, synchronizace a exportu změn.
* **Úlohy údržby**. Obnovte klíče a certifikáty pro službu resetování hesla a službu registrace zařízení (DRS). Vymazat staré položky v protokolu operací.

Samotný plánovač je vždy spuštěn, ale lze jej nakonfigurovat tak, aby spouštěl pouze jednu nebo žádnou z těchto úloh. Například pokud potřebujete mít vlastní proces cyklu synchronizace, můžete zakázat tuto úlohu v plánovači, ale stále spustit úlohu údržby.

## <a name="scheduler-configuration"></a>Konfigurace plánovače
Chcete-li zobrazit aktuální nastavení konfigurace, `Get-ADSyncScheduler`přejděte na PowerShell a spusťte . To vám ukáže něco jako tento obrázek:

![GetSyncScheduler](./media/how-to-connect-sync-feature-scheduler/getsynccyclesettings2016.png)

Pokud se zobrazí **příkaz synchronizace nebo rutina není k dispozici** při spuštění této rutiny, pak modul Prostředí PowerShell není načten. K tomuto problému může dojít, pokud spustíte Azure AD Connect na řadiči domény nebo na serveru s vyššími úrovněmi omezení prostředí PowerShell než výchozí nastavení. Pokud se zobrazí tato `Import-Module ADSync` chyba, pak spusťte, aby byla rutina k dispozici.

* **AllowedSyncCycleInterval**. Nejkratší časový interval mezi cykly synchronizace povolenými službou Azure AD. Synchronizaci nelze synchronizovat častěji než toto nastavení a stále podporovány.
* **CurrentlyEffectiveSyncCycleInterval**. Plán, který je v současné době v platnosti. Má stejnou hodnotu jako CustomizedSyncInterval (pokud je nastaven), pokud není častější než AllowedSyncInterval. Pokud použijete sestavení před 1.1.281 a změníte CustomizedSyncCycleInterval, tato změna se projeví po dalším cyklu synchronizace. Z sestavení 1.1.281 se změna projeví okamžitě.
* **VlastnísynchronsyncCycleInterval**. Pokud chcete, aby plánovač běžel na libovolné jiné frekvenci než výchozí 30 minut, nakonfigurujte toto nastavení. Na obrázku výše plánovač byla nastavena na spuštění každou hodinu místo. Pokud nastavíte toto nastavení na hodnotu nižší než AllowedSyncInterval, použije se tato hodnota.
* **NextSyncCyclePolicyType**. Buď Delta nebo Initial. Definuje, zda by mělo další spuštění zpracovat pouze rozdílové změny nebo zda by mělo další spuštění provést úplný import a synchronizaci. Ten by také znovu zpracovat všechny nové nebo změněné pravidla.
* **Další synccyclestarttimeinutc**. Příště plánovač spustí další cyklus synchronizace.
* **Interval PurgeRunHistoryInterval**. Protokoly operací času by měly být uchovávány. Tyto protokoly lze zkontrolovat ve správci služeb synchronizace. Ve výchozím nastavení je uchovávat tyto protokoly po dobu 7 dnů.
* **SyncCycleEnabled**. Označuje, zda plánovač spouštějí procesy importu, synchronizace a exportu v rámci své operace.
* **MaintenanceEnabled**. Zobrazuje, zda je povolen proces údržby. Aktualizuje certifikáty/klíče a vyčistí protokol operací.
* **Pracovní modeEnabled**. Zobrazuje, zda je povolen [pracovní režim.](how-to-connect-sync-staging-server.md) Pokud je toto nastavení povoleno, potlačí exporty ze spuštěné, ale stále spustit import a synchronizaci.
* **SchedulerPozastavena**. Nastavit připojit během upgradu dočasně blokovat plánovače spuštění.

Některá z těchto nastavení `Set-ADSyncScheduler`můžete změnit pomocí aplikace . Následující parametry lze upravit:

* Přizpůsobenýinterval synchronizátoru
* Typ nextsynccyclepolicytype
* Interval purgeRunHistoryInterval
* SyncCycleEnabled
* MaintenanceEnabled

V dřívějších sestaveních Azure AD Connect byl **isStagingModeEnabled** vystaven v Set-ADSyncScheduler. Nastavení této vlastnosti není **podporováno.** Vlastnost **SchedulerSuspended** by měla být změněna pouze connect. Není **podporováno** nastavit přímo pomocí prostředí PowerShell.

Konfigurace plánovače se uložených ve službě Azure AD. Pokud máte pracovní server, všechny změny na primárním serveru také ovlivní pracovní server (s výjimkou IsStagingModeEnabled).

### <a name="customizedsynccycleinterval"></a>Přizpůsobenýinterval synchronizátoru
Syntaxe:`Set-ADSyncScheduler -CustomizedSyncCycleInterval d.HH:mm:ss`  
d - dny, HH - hodiny, mm - minuty, ss - sekundy

Příklad: `Set-ADSyncScheduler -CustomizedSyncCycleInterval 03:00:00`  
Změní plánovač tak, aby se spouštěl každé 3 hodiny.

Příklad: `Set-ADSyncScheduler -CustomizedSyncCycleInterval 1.0:0:0`  
Změny změní plánovač, aby se spouštěl denně.

### <a name="disable-the-scheduler"></a>Zakázání plánovače  
Pokud potřebujete provést změny konfigurace, chcete zakázat plánovač. Například při [konfiguraci filtrování](how-to-connect-sync-configure-filtering.md) nebo [provést změny pravidel synchronizace](how-to-connect-sync-change-the-configuration.md).

Plánovač zakážete `Set-ADSyncScheduler -SyncCycleEnabled $false`spuštěním programu .

![Zakázání plánovače](./media/how-to-connect-sync-feature-scheduler/schedulerdisable.png)

Po provázku nezapomeňte plánovač znovu povolit pomocí `Set-ADSyncScheduler -SyncCycleEnabled $true`aplikace .

## <a name="start-the-scheduler"></a>Spuštění plánovače
Plánovač je ve výchozím nastavení spuštěn každých 30 minut. V některých případech můžete chtít spustit cyklus synchronizace mezi naplánovanými cykly nebo potřebujete spustit jiný typ.

### <a name="delta-sync-cycle"></a>Cyklus synchronizace delta
Cyklus synchronizace delta zahrnuje následující kroky:


- Rozdílový import na všech konektorech
- Synchronizace delta na všech konektorech
- Export na všechny konektory

### <a name="full-sync-cycle"></a>Cyklus úplné synchronizace
Úplný cyklus synchronizace zahrnuje následující kroky:

- Úplný import na všech konektorech
- Úplná synchronizace na všech konektorech
- Export na všechny konektory

Je možné, že máte naléhavou změnu, která musí být synchronizována okamžitě, což je důvod, proč je třeba ručně spustit cyklus. 

Pokud potřebujete ručně spustit cyklus synchronizace, pak `Start-ADSyncSyncCycle -PolicyType Delta`z PowerShell spustit .

Chcete-li zahájit úplný `Start-ADSyncSyncCycle -PolicyType Initial` cyklus synchronizace, spusťte z výzvy prostředí PowerShell.   

Spuštění úplného cyklu synchronizace může být velmi časově náročné, přečtěte si další část, ve které si přečtěte, jak optimalizovat tento proces.

### <a name="sync-steps-required-for-different-configuration-changes"></a>Synchronizace kroků požadovaných pro různé změny konfigurace
Různé změny konfigurace vyžadují různé kroky synchronizace, aby bylo zajištěno, že změny jsou správně použity pro všechny objekty.

- Přidáno více objektů nebo atributů, které mají být importovány ze zdrojového adresáře (přidáním/úpravou pravidel synchronizace)
    - V konektoru pro daný zdrojový adresář je vyžadován úplný import.
- Provedené změny pravidel synchronizace
    - Pro změněná pravidla synchronizace je vyžadována úplná synchronizace.
- Změněno [filtrování,](how-to-connect-sync-configure-filtering.md) takže by měl být zahrnut jiný počet objektů
    - Úplný import je vyžadován na konektoru pro každou spojnici služby AD, pokud nepoužíváte filtrování založené na atributech na základě atributů, které jsou již importovány do synchronizačního modulu

### <a name="customizing-a-sync-cycle-run-the-right-mix-of-delta-and-full-sync-steps"></a>Přizpůsobení cyklu synchronizace spustit správnou kombinaci kroků synchronizace a úplné synchronizace
Chcete-li se vyhnout spuštění úplného cyklu synchronizace, můžete označit konkrétní konektory pro spuštění úplného kroku pomocí následujících rutin.

`Set-ADSyncSchedulerConnectorOverride -Connector <ConnectorGuid> -FullImportRequired $true`

`Set-ADSyncSchedulerConnectorOverride -Connector <ConnectorGuid> -FullSyncRequired $true`

`Get-ADSyncSchedulerConnectorOverride -Connector <ConnectorGuid>` 

Příklad: Pokud jste provedli změny pravidel synchronizace pro konektor "Doménová struktura služby A", které nevyžadují žádné nové atributy, které mají být importovány, spustíte následující rutiny pro spuštění cyklu synchronizace delta, který také provedl krok úplné synchronizace pro tento konektor.

`Set-ADSyncSchedulerConnectorOverride -ConnectorName “AD Forest A” -FullSyncRequired $true`

`Start-ADSyncSyncCycle -PolicyType Delta`

Příklad: Pokud jste provedli změny pravidel synchronizace pro konektor "Doménová struktura a služby A", takže nyní vyžadují nový atribut, který má být importován, spustíte následující rutiny pro spuštění cyklu synchronizace delta, který také provedl krok úplného importu, úplné synchronizace pro tento konektor.

`Set-ADSyncSchedulerConnectorOverride -ConnectorName “AD Forest A” -FullImportRequired $true`

`Set-ADSyncSchedulerConnectorOverride -ConnectorName “AD Forest A” -FullSyncRequired $true`

`Start-ADSyncSyncCycle -PolicyType Delta`


## <a name="stop-the-scheduler"></a>Zastavení plánovače
Pokud plánovač aktuálně běží cyklus synchronizace, bude pravděpodobně nutné jej zastavit. Například pokud spustíte průvodce instalací a zobrazí se tato chyba:

![Chyba SyncCycleRunningError](./media/how-to-connect-sync-feature-scheduler/synccyclerunningerror.png)

Pokud je spuštěn cyklus synchronizace, nelze provádět změny konfigurace. Můžete počkat, až plánovač dokončí proces, ale můžete jej také zastavit, abyste mohli okamžitě provést změny. Zastavení aktuálního cyklu není škodlivé a čekající změny jsou zpracovány při příštím spuštění.

1. Začněte tím, že plánovače zastavíte svůj aktuální `Stop-ADSyncSyncCycle`cyklus pomocí rutiny prostředí PowerShell .
2. Pokud použijete sestavení před 1.1.281, zastavení plánovače nezastaví aktuální konektor z jeho aktuální úlohy. Chcete-li vynutit zastavení konektoru, provázte následující akce: ![StopAConnector](./media/how-to-connect-sync-feature-scheduler/stopaconnector.png)
   * **Spusťte službu synchronizace** z nabídky Start. Přejděte na **Konektory**, zvýrazněte spojnici se stavem **Spuštěno**a z akce vyberte **Zastavit.**

Plánovač je stále aktivní a začíná znovu při další příležitosti.

## <a name="custom-scheduler"></a>Vlastní plánovač
Rutiny zdokumentované v této části jsou k dispozici pouze v sestavení [1.1.130.0](reference-connect-version-history.md#111300) a novější.

Pokud předdefinovaný plánovač nesplňuje vaše požadavky, můžete naplánovat konektory pomocí prostředí PowerShell.

### <a name="invoke-adsyncrunprofile"></a>Vyvolat adsyncrunprofil
Profil konektoru můžete spustit tímto způsobem:

```
Invoke-ADSyncRunProfile -ConnectorName "name of connector" -RunProfileName "name of profile"
```

Názvy, které mají být použity pro [názvy konektorů](how-to-connect-sync-service-manager-ui-connectors.md) a [spustit názvy profilů,](how-to-connect-sync-service-manager-ui-connectors.md#configure-run-profiles) naleznete v [uzlení Správce služeb synchronizace](how-to-connect-sync-service-manager-ui.md).

![Vyvolat profil spuštění](./media/how-to-connect-sync-feature-scheduler/invokerunprofile.png)  

Rutina `Invoke-ADSyncRunProfile` je synchronní, to znamená, že nevrátí řízení, dokud konektor dokončil operaci, nebo s chybou.

Při plánování konektorů doporučujeme naplánovat je v následujícím pořadí:

1. (Úplné/Delta) Import z místních adresářů, jako je služba Active Directory
2. (Úplné/Delta) Import ze služby Azure AD
3. (Úplné/Delta) Synchronizace z místních adresářů, jako je například služba Active Directory
4. (Úplné/Delta) Synchronizace ze služby Azure AD
5. Export do Azure AD
6. Export do místních adresářů, například do služby Active Directory

Toto pořadí je, jak předdefinované plánovač spustí konektory.

### <a name="get-adsyncconnectorrunstatus"></a>Stav rozhraní Get-AdSyncConnectorRunStatus
Můžete také sledovat synchronizační modul a zjistit, zda je zaneprázdněn nebo nečinný. Tato rutina vrátí prázdný výsledek, pokud je synchronizační modul nečinný a nespouštějící konektor. Pokud je spuštěn konektor, vrátí název Connector.

```
Get-ADSyncConnectorRunStatus
```

![Stav spuštění konektoru](./media/how-to-connect-sync-feature-scheduler/getconnectorrunstatus.png)  
Na obrázku výše první řádek je ze stavu, kde je synchronizační modul volnoběh. Druhý řádek z doby, kdy je spuštěna služba Azure AD Connector.

## <a name="scheduler-and-installation-wizard"></a>Průvodce plánovačem a instalací
Pokud spustíte průvodce instalací, plánovač je dočasně pozastaven. Toto chování je proto, že se předpokládá, že provedete změny konfigurace a tato nastavení nelze použít, pokud je synchronizační modul aktivně spuštěn. Z tohoto důvodu nenechávejte průvodce instalací otevřený, protože brání synchronizačnímu modulu provádět akce synchronizace.

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o konfiguraci [synchronizace Azure AD Connect.](how-to-connect-sync-whatis.md)

Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).
