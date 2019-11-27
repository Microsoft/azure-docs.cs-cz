---
title: Použití nastavení diagnostiky pro trezory Recovery Services
description: Článek popisující, jak používat staré a nové diagnostické události pro Azure Backup
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: 875adb82aeeb56b378a84ca01e716c7189abc64f
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74281100"
---
# <a name="using-diagnostics-settings-for-recovery-services-vaults"></a>Použití nastavení diagnostiky pro trezory služby Recovery Services

Azure Backup odesílá diagnostické události, které je možné shromažďovat a používat pro účely analýzy, upozorňování a vytváření sestav. 

Nastavení diagnostiky pro Recovery Services trezor můžete nakonfigurovat přes Azure Portal tak, že přejdete do trezoru a kliknete na položku nabídky **nastavení diagnostiky** . Kliknutím na **+ Přidat nastavení diagnostiky** můžete odeslat jednu nebo více událostí diagnostiky do účtu úložiště, centra událostí nebo pracovního prostoru Log Analytics (La).

![Okno nastavení diagnostiky](./media/backup-azure-diagnostics-events/diagnostics-settings-blade.png)

## <a name="diagnostics-events-available-for-azure-backup-users"></a>Události diagnostiky dostupné pro Azure Backup uživatele

Azure Backup poskytuje následující diagnostické události, z nichž každá poskytuje podrobná data na konkrétní sadu artefaktů souvisejících se zálohováním:
* CoreAzureBackup
* AddonAzureBackupAlerts
* AddonAzureBackupProtectedInstance
* AddonAzureBackupJobs
* AddonAzureBackupPolicy
* AddonAzureBackupStorage 

[Datový model pro události diagnostiky Azure Backup](https://aka.ms/diagnosticsdatamodel)

Data pro tyto události se dají odeslat do účtu úložiště, do pracovního prostoru LA nebo do centra událostí. Pokud tato data posíláte do pracovního prostoru LA, musíte na obrazovce **nastavení diagnostiky** vybrat přepínač **specifický pro prostředky** (Další informace najdete v následujících částech).

## <a name="using-diagnostics-settings-with-log-analytics-la"></a>Použití nastavení diagnostiky s Log Analytics (LA)

V souladu s plánem Azure Log Analytics se teď Azure Backup umožňuje odeslat diagnostická data trezoru k vyhrazeným tabulkám LA pro zálohování. Tyto jsou označovány jako [tabulky specifické pro prostředky](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-workspace#resource-specific).

Odeslání dat diagnostiky trezoru do nástroje LA:
1.  Přejděte do trezoru a klikněte na **nastavení diagnostiky**. Klikněte na **+ Přidat nastavení diagnostiky**.
2.  Zadejte název nastavení diagnostiky.
3.  Zaškrtněte políčko **Odeslat do Log Analytics** a vyberte pracovní prostor Log Analytics.
4.  V přepínači vyberte **prostředky specifické** a zaškrtněte následující šest událostí – **CoreAzureBackup**, **AddonAzureBackupAlerts**, **AddonAzureBackupProtectedInstance**, **AddonAzureBackupJobs**, **AddonAzureBackupPolicy**a **AddonAzureBackupStorage**.
5.  Klikněte na **Uložit**.

![Režim specifický pro prostředky](./media/backup-azure-diagnostics-events/resource-specific-blade.png)

Jakmile se data natoků do pracovního prostoru LA, v pracovním prostoru se vytvoří vyhrazené tabulky pro každou z těchto událostí. V případě potřeby můžete zadávat dotazy na kterékoli z těchto tabulek přímo a v případě potřeby také provádět spojení nebo sjednocení mezi těmito tabulkami.

> [!IMPORTANT]
> Výše uvedené šest událostí, konkrétně CoreAzureBackup, AddonAzureBackupAlerts, AddonAzureBackupProtectedInstance, AddonAzureBackupJobs, AddonAzureBackupPolicy a AddonAzureBackupStorage, jsou podporovány **pouze** v režimu specifických pro prostředky. **Upozorňujeme, že pokud se pokusíte odeslat data pro tyto šest událostí v režimu Azure Diagnostics, nebudou se do pracovního prostoru LA nacházet žádná data.**

## <a name="legacy-event"></a>Starší verze události

V rámci jedné události s názvem "AzureBackupReport" se tradičně používala všechna diagnostická data týkající se zálohování pro trezor. Šest událostí popsaných výše jsou v podstatě dekompozice všech dat obsažených v AzureBackupReport. 

V současné době i nadále podporujeme událost AzureBackupReport pro zpětnou kompatibilitu, v případech, kdy uživatelé mají v této události existující vlastní dotazy, například vlastní výstrahy protokolu, vlastní vizualizace atd. V trezoru ale doporučujeme zvolit nové události pro všechna nová nastavení diagnostiky, protože díky tomu bude mnohem snazší pracovat s nimi v protokolových dotazech. poskytuje lepší zjistitelnost schémat a jejich strukturu, zlepšuje výkon při příjmu dat. latence a časy dotazování. Podpora pro použití režimu Azure Diagnostics bude nakonec vycházet z provozu, takže výběr nových událostí vám může pomoci vyhnout se složitým migracím později.

Můžete se rozhodnout vytvořit samostatná nastavení diagnostiky pro AzureBackupReport a šest nových událostí, dokud nemigrujete všechny vlastní dotazy, aby používaly data z nových tabulek. Následující obrázek ukazuje příklad trezoru se dvěma nastaveními diagnostiky. První nastavení s názvem **Setting1** odesílá data události AzureBackupReport do pracovního prostoru La v režimu AzureDiagnostics. Druhé nastavení s názvem **Setting2** odesílá data o šesti nových událostech Azure Backup do pracovního prostoru La v režimu specifickém pro prostředky.

![Dvě nastavení](./media/backup-azure-diagnostics-events/two-settings-example.png)

> [!IMPORTANT]
> Událost AzureBackupReport je podporována **pouze** v režimu Azure Diagnostics. **Počítejte s tím, že pokud se pokusíte odeslat data pro tuto událost v režimu specifickém pro prostředky, nebudou do pracovního prostoru LA předávána žádná data.**

> [!NOTE]
> Přepínač pro Azure Diagnostics/prostředek se zobrazí jenom v případě, že uživatel vyhledá **Log Analytics odeslat**. Pokud chcete odesílat data do účtu úložiště nebo centra událostí, uživatel může jednoduše vybrat požadovaný cíl a ověřit jakoukoli z požadovaných událostí bez dalších vstupů. Znovu se doporučuje, abyste nezvolili starší verzi Event AzureBackupReport, a to až dál.

## <a name="users-sending-azure-site-recovery-events-to-log-analytics-la"></a>Uživatelé odesílající události Azure Site Recovery do Log Analytics (LA)

Události Azure Backup a Azure Site Recovery jsou odesílány ze stejného trezoru Recovery Services. Protože Azure Site Recovery není v současné době připojená k tabulkám specifických pro prostředky, uživatelé, kteří chtějí posílat Azure Site Recovery události do LA, se přesměrují **jenom** na použití režimu Azure Diagnostics (viz obrázek níže). **Volba režimu specifického pro prostředky pro události Azure Site Recovery zabrání odeslání požadovaných dat do pracovního prostoru La**.

![Události Site Recovery](./media/backup-azure-diagnostics-events/site-recovery-settings.png)

Sumarizace výše uvedených drobné odlišnosti:

* Pokud už máte diagnostiku LA nastavenou na Azure Diagnostics a máte na ní vytvořené vlastní dotazy, nechte toto nastavení **nedotčeno**, dokud nemigrujete dotazy na použití dat z nových událostí.
* Pokud chcete také zařadit do nových tabulek (podle doporučení), vytvořte **nové** nastavení diagnostiky, zvolte možnost **specifické pro prostředky** a vyberte šest nových událostí, jak je uvedeno výše.
* Pokud nyní odesíláte události Azure Site Recovery do nástroje LA, **nevybírejte pro** tyto události režim pro konkrétní prostředky, jinak nebudou data pro tyto události předávat do pracovního prostoru La. Místo toho vytvořte **Další nastavení diagnostiky**, vyberte **Azure Diagnostics**a zvolte příslušné události Azure Site Recovery.

Následující obrázek ukazuje příklad uživatele se třemi nastaveními diagnostiky pro trezor. První nastavení s názvem **Setting1** odesílá data z události AzureBackupReport do pracovního prostoru La v režimu AzureDiagnostics. Druhé nastavení s názvem **Setting2** odesílá data z šesti nových událostí Azure Backup do pracovního prostoru La v režimu specifického pro prostředky. Třetí nastavení s názvem **Setting3**odesílá data z událostí Azure Site Recovery do pracovního prostoru La v režimu Azure Diagnostics.

![Tři nastavení](./media/backup-azure-diagnostics-events/three-settings-example.png)

## <a name="next-steps"></a>Další kroky

[Naučte se Log Analytics datový model pro diagnostické události](https://aka.ms/diagnosticsdatamodel)
