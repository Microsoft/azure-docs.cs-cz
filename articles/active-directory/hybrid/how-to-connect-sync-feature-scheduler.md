---
title: 'Azure AD Connect synchronizace: Scheduler | Microsoft Docs'
description: Toto téma popisuje integrovanou funkci Scheduleru v Azure AD Connect synchronizaci.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 6b1a598f-89c0-4244-9b20-f4aaad5233cf
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/01/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad7b0039602add7f4cd3cdd300bd829c4f148a79
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "90084732"
---
# <a name="azure-ad-connect-sync-scheduler"></a>Synchronizace Azure AD Connect: Plánovač
Toto téma popisuje integrovaný Plánovač v Azure AD Connect Sync (synchronizační modul).

Tato funkce byla představena s 1.1.105.0EM buildu (vydáno 2016. února).

## <a name="overview"></a>Přehled
Azure AD Connect synchronizace synchronizuje změny, ke kterým došlo v místním adresáři pomocí plánovače. Existují dva procesy plánovače: jeden pro synchronizaci hesla a druhý pro úlohy synchronizace objektů a atributů a údržby. Toto téma se zabývá tímto tématem.

V dřívějších verzích byl Plánovač pro objekty a atributy externě pro modul synchronizace. Pro aktivaci procesu synchronizace používala Plánovač úloh systému Windows nebo samostatnou službu systému Windows. Plánovač se nachází ve verzi 1,1, která je integrovaná do synchronizačního modulu, a umožňuje některé vlastní nastavení. Nová výchozí frekvence synchronizace je 30 minut.

Scheduler zodpovídá za dvě úlohy:

* **Cyklus synchronizace**. Proces importu, synchronizace a exportu změn.
* **Úlohy údržby**. Obnovení klíčů a certifikátů pro resetování hesla a službu Device Registration Service (DRS). Vyprázdnit staré položky v protokolu operací.

Samotný Plánovač je vždycky spuštěný, ale dá se nakonfigurovat tak, aby se spouštěl jenom jeden nebo žádný z těchto úkolů. Například pokud potřebujete mít vlastní proces synchronizačního cyklu, můžete tuto úlohu v Plánovači zakázat, ale úlohu údržby pořád spustit.

>[!IMPORTANT]
>Ve výchozím nastavení se cyklus synchronizace spouští každých 30 minut. Pokud jste změnili cyklus synchronizace, budete se muset ujistit, že se cyklus synchronizace spouští aspoň každých 7 dní. 
>
>* Rozdílová synchronizace se musí provést do 7 dnů od poslední rozdílové synchronizace.
>* Rozdílová synchronizace (po úplné synchronizaci) se musí nacházet do 7 dnů od okamžiku dokončení poslední úplné synchronizace.
>
>V takovém případě může dojít k problémům s synchronizací, které budou vyžadovat, abyste spustili úplnou synchronizaci, která se má vyřešit. To platí i pro servery v pracovním režimu.

## <a name="scheduler-configuration"></a>Konfigurace Scheduleru
Pokud chcete zobrazit aktuální konfigurační nastavení, přejděte do PowerShellu a spusťte `Get-ADSyncScheduler` . Zobrazuje se vám obrázek podobný tomuto:

![GetSyncScheduler](./media/how-to-connect-sync-feature-scheduler/getsynccyclesettings2016.png)

Pokud se vám při spuštění této rutiny zobrazí **příkaz synchronizovat nebo rutina není dostupná** , modul PowerShellu se nenačte. K tomuto problému může dojít, když spustíte Azure AD Connect v řadiči domény nebo na serveru s vyššími úrovněmi omezení prostředí PowerShell, než je výchozí nastavení. Pokud se zobrazí tato chyba, spusťte `Import-Module ADSync` rutinu, aby byla dostupná.

* **AllowedSyncCycleInterval**. Nejkratší časový interval mezi synchronizačními cykly povolenými službou Azure AD. Nemůžete synchronizovat častěji než toto nastavení a pořád se podporuje.
* **CurrentlyEffectiveSyncCycleInterval**. Plán je aktuálně platný. Má stejnou hodnotu jako CustomizedSyncInterval (Pokud je nastaveno), pokud není častější než AllowedSyncInterval. Použijete-li sestavení před 1.1.281 a změníte CustomizedSyncCycleInterval, projeví se tato změna po příštím cyklu synchronizace. V sestavách 1.1.281 se změna projeví okamžitě.
* **CustomizedSyncCycleInterval**. Pokud chcete, aby se Plánovač spouštěl v jakékoli jiné frekvenci, než je výchozí hodnota 30 minut, pak toto nastavení nakonfigurujete. Ve výše uvedeném obrázku byl Plánovač nastaven tak, aby se spouštěl každou hodinu. Pokud nastavíte toto nastavení na hodnotu nižší než AllowedSyncInterval, použije se druhý.
* **NextSyncCyclePolicyType**. Buď rozdílová, nebo počáteční. Definuje, jestli má příští spuštění zpracovat jenom rozdílové změny, nebo jestli by se měl další spuštění provést jako úplný Import a synchronizace. Druhé by se taky znovu zpracovávat všechna nová nebo změněná pravidla.
* **NextSyncCycleStartTimeInUTC**. Při příštím spuštění plánovače další cyklus synchronizace.
* **PurgeRunHistoryInterval**. Protokoly operací času by měly být zachovány. Tyto protokoly je možné zkontrolovat v synchronizaci Service Manageru. Ve výchozím nastavení jsou tyto protokoly uchovávány po dobu 7 dní.
* **SyncCycleEnabled**. Určuje, jestli má Plánovač v rámci své operace spouštět procesy importu, synchronizace a exportu.
* **MaintenanceEnabled**. Ukazuje, zda je povolen proces údržby. Aktualizuje certifikáty nebo klíče a vyprázdní protokol operací.
* **StagingModeEnabled**. Ukazuje, zda je povolen [pracovní režim](how-to-connect-sync-staging-server.md) . Pokud je toto nastavení povoleno, potlačí se exporty na spouštění, ale stále spouštějte import a synchronizaci.
* **SchedulerSuspended**. Během upgradu nastavte připojit k dočasnému blokování spuštění plánovače.

Některá z těchto nastavení můžete změnit pomocí `Set-ADSyncScheduler` . Je možné upravit následující parametry:

* CustomizedSyncCycleInterval
* NextSyncCyclePolicyType
* PurgeRunHistoryInterval
* SyncCycleEnabled
* MaintenanceEnabled

V dřívějších sestaveních Azure AD Connect byl **isStagingModeEnabled** v set-ADSyncScheduler. Nastavení této vlastnosti se **nepodporuje** . Vlastnost **SchedulerSuspended** by měla být upravena pouze pomocí Connect. Toto nastavení není **podporováno** přímo v PowerShellu.

Konfigurace Scheduleru je uložená v Azure AD. Pokud máte pracovní server, bude mít každá změna na primárním serveru také vliv na pracovní server (kromě IsStagingModeEnabled).

### <a name="customizedsynccycleinterval"></a>CustomizedSyncCycleInterval
Syntaktick `Set-ADSyncScheduler -CustomizedSyncCycleInterval d.HH:mm:ss`  
d-dnů, HH-hours, mm-minut, SS-sekund

Příklad: `Set-ADSyncScheduler -CustomizedSyncCycleInterval 03:00:00`  
Změní Plánovač, aby se spouštěl každé 3 hodiny.

Příklad: `Set-ADSyncScheduler -CustomizedSyncCycleInterval 1.0:0:0`  
Změny změní Plánovač tak, aby běžel denně.

### <a name="disable-the-scheduler"></a>Zakázat Plánovač  
Pokud potřebujete provést změny konfigurace, budete chtít Plánovač zakázat. Například při [konfiguraci filtrování](how-to-connect-sync-configure-filtering.md) nebo [provádění změn pravidel synchronizace](how-to-connect-sync-change-the-configuration.md).

Chcete-li zakázat Plánovač, spusťte příkaz `Set-ADSyncScheduler -SyncCycleEnabled $false` .

![Zakázat Plánovač](./media/how-to-connect-sync-feature-scheduler/schedulerdisable.png)

Až provedete změny, nezapomeňte znovu povolit Plánovač s nástrojem `Set-ADSyncScheduler -SyncCycleEnabled $true` .

## <a name="start-the-scheduler"></a>Spustit Plánovač
Plánovač se ve výchozím nastavení spouští každých 30 minut. V některých případech můžete chtít spustit cyklus synchronizace mezi naplánovanými cykly nebo musíte spustit jiný typ.

### <a name="delta-sync-cycle"></a>Cyklus rozdílové synchronizace
Cyklus rozdílového synchronizace zahrnuje následující kroky:


- Rozdílový import u všech konektorů
- Rozdílová synchronizace u všech konektorů
- Exportovat na všechny konektory

### <a name="full-sync-cycle"></a>Úplný cyklus synchronizace
Úplný cyklus synchronizace zahrnuje následující kroky:

- Úplný import na všechny konektory
- Úplná synchronizace na všech konektorech
- Exportovat na všechny konektory

Je možné, že máte naléhavou změnu, kterou je třeba synchronizovat okamžitě, což je důvod, proč je nutné ručně spustit cyklus. 

Pokud potřebujete ručně spustit cyklus synchronizace, pak z PowerShellu spusťte `Start-ADSyncSyncCycle -PolicyType Delta` .

Pokud chcete spustit úplný cyklus synchronizace, spusťte `Start-ADSyncSyncCycle -PolicyType Initial` z příkazového řádku PowerShellu.   

Spuštění úplného cyklu synchronizace může být velmi časově náročné. Přečtěte si další část a přečtěte si, jak tento proces optimalizovat.

### <a name="sync-steps-required-for-different-configuration-changes"></a>Kroky synchronizace vyžadované pro různé změny konfigurace
Různé změny konfigurace vyžadují jiný postup synchronizace, aby bylo zajištěno, že se změny správně projeví u všech objektů.

- Přidání dalších objektů nebo atributů, které se mají importovat ze zdrojového adresáře (přidáním nebo úpravou pravidel synchronizace)
    - V konektoru pro daný zdrojový adresář je vyžadován úplný import.
- Provedeny změny synchronizačních pravidel
    - V konektoru pro změněná synchronizační pravidla se vyžaduje Úplná synchronizace.
- Změněné [filtrování](how-to-connect-sync-configure-filtering.md) , aby bylo možné zahrnout jiný počet objektů
    - Pokud nepoužíváte filtrování založené na atributech na základě atributů, které už jsou importované do synchronizačního modulu, vyžaduje se u konektoru pro každý konektor AD úplný import.

### <a name="customizing-a-sync-cycle-run-the-right-mix-of-delta-and-full-sync-steps"></a>Přizpůsobení cyklu synchronizace spustí pravou kombinaci rozdílových a úplných kroků synchronizace.
Abyste se vyhnuli spuštění úplného cyklu synchronizace, můžete označit konkrétní konektory a spustit úplný krok pomocí následujících rutin.

`Set-ADSyncSchedulerConnectorOverride -Connector <ConnectorGuid> -FullImportRequired $true`

`Set-ADSyncSchedulerConnectorOverride -Connector <ConnectorGuid> -FullSyncRequired $true`

`Get-ADSyncSchedulerConnectorOverride -Connector <ConnectorGuid>` 

Příklad: Pokud jste provedli změny synchronizačních pravidel pro Connector "doménová struktura služby AD" A nevyžadují import žádné nové atributy, měli byste spustit následující rutiny pro spuštění rozdílového synchronizačního cyklu, který pro tento konektor má taky úplný krok synchronizace.

`Set-ADSyncSchedulerConnectorOverride -ConnectorName “AD Forest A” -FullSyncRequired $true`

`Start-ADSyncSyncCycle -PolicyType Delta`

Příklad: Pokud jste provedli změny synchronizačních pravidel pro Connector "doménová struktura služby AD" A "tak, aby nyní vyžadovala Import nového atributu, měli byste spuštěním následujících rutin spustit rozdílový cyklus synchronizace, který má také úplný krok importu a úplné synchronizace pro tento konektor.

`Set-ADSyncSchedulerConnectorOverride -ConnectorName “AD Forest A” -FullImportRequired $true`

`Set-ADSyncSchedulerConnectorOverride -ConnectorName “AD Forest A” -FullSyncRequired $true`

`Start-ADSyncSyncCycle -PolicyType Delta`


## <a name="stop-the-scheduler"></a>Zastavení plánovače
Pokud Plánovač aktuálně spouští cyklus synchronizace, může být nutné ho zastavit. Například pokud spustíte Průvodce instalací a zobrazí se tato chyba:

![Snímek obrazovky s informacemi o chybě konfigurace nelze změnit.](./media/how-to-connect-sync-feature-scheduler/synccyclerunningerror.png)

Když je spuštěný cyklus synchronizace, nemůžete provádět změny konfigurace. Můžete počkat, až proces plánovače proces dokončí, ale můžete ho také zastavit, aby bylo možné provést změny hned. Zastavení aktuálního cyklu není škodlivé a probíhající změny se zpracovávají při příštím spuštění.

1. Začněte tím, že zaznamenáte, že Plánovač zastaví aktuální cyklus pomocí rutiny prostředí PowerShell `Stop-ADSyncSyncCycle` .
2. Pokud použijete sestavení před 1.1.281, pak zastavení plánovače nezastaví aktuální konektor z aktuální úlohy. Chcete-li vynutit zastavení konektoru, proveďte následující akce:

   ![Snímek obrazovky ukazuje Synchronization Service Manager s vybranými konektory a běžící konektor zvýrazněný vybranou akcí zastavit.](./media/how-to-connect-sync-feature-scheduler/stopaconnector.png)

   * Spusťte **synchronizační službu** z nabídky Start. Přejděte na **konektory**, zvýrazněte konektor se stavem **spuštěno** a vyberte možnost **zastavit** z akcí.

Plánovač je stále aktivní a při další příležitosti se znovu spustí.

## <a name="custom-scheduler"></a>Vlastní Plánovač
Rutiny popsané v této části jsou dostupné jenom v sestavách [1.1.130.0](reference-connect-version-history.md) a novějších.

Pokud předdefinovaný Plánovač nesplňuje vaše požadavky, můžete konektory naplánovat pomocí PowerShellu.

### <a name="invoke-adsyncrunprofile"></a>Invoke-ADSyncRunProfile
Profil konektoru můžete spustit tímto způsobem:

```
Invoke-ADSyncRunProfile -ConnectorName "name of connector" -RunProfileName "name of profile"
```

Názvy, které se mají použít pro [názvy konektorů](how-to-connect-sync-service-manager-ui-connectors.md) a [názvy profilů spuštění](how-to-connect-sync-service-manager-ui-connectors.md#configure-run-profiles) , najdete v [uživatelském rozhraní Synchronization Service Manager](how-to-connect-sync-service-manager-ui.md).

![Vyvolat profil spuštění](./media/how-to-connect-sync-feature-scheduler/invokerunprofile.png)  

`Invoke-ADSyncRunProfile`Rutina je synchronní, to znamená, že nevrací řízení, dokud konektor nedokončí operaci, buď úspěšně, nebo s chybou.

Při plánování konektorů je potřeba, abyste je naplánovali v následujícím pořadí:

1. (Úplné/rozdílové) Import z místních adresářů, jako je například služba Active Directory
2. (Úplné/rozdílové) Import z Azure AD
3. (Úplné/rozdílové) Synchronizace z místních adresářů, jako je například služba Active Directory
4. (Úplné/rozdílové) Synchronizace z Azure AD
5. Exportovat do Azure AD
6. Exportovat do místních adresářů, jako je například Active Directory

V tomto pořadí je způsob, jakým integrovaný plánovač spouští konektory.

### <a name="get-adsyncconnectorrunstatus"></a>Get-ADSyncConnectorRunStatus
Můžete také monitorovat synchronizační modul a zjistit, jestli je zaneprázdněný nebo nečinný. Tato rutina vrátí prázdný výsledek, pokud je synchronizační modul nečinný a nespouští konektor. Pokud je konektor spuštěný, vrátí název konektoru.

```
Get-ADSyncConnectorRunStatus
```

![Stav spuštění konektoru](./media/how-to-connect-sync-feature-scheduler/getconnectorrunstatus.png)  
Na obrázku výše je první řádek ze stavu, ve kterém je synchronizační modul nečinný. Druhý řádek od okamžiku, kdy je spuštěn konektor Azure AD.

## <a name="scheduler-and-installation-wizard"></a>Průvodce schedulerem a instalací
Pokud spustíte Průvodce instalací nástroje, Plánovač je dočasně pozastaven. K tomuto chování dochází, protože se předpokládá, že provádíte změny konfigurace a tato nastavení nejde použít, pokud je modul synchronizace aktivně spuštěný. Z tohoto důvodu nenechávejte Průvodce instalací otevřený, protože zastaví synchronizační modul před provedením synchronizačních akcí.

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o konfiguraci [Azure AD Connect synchronizace](how-to-connect-sync-whatis.md) .

Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).
